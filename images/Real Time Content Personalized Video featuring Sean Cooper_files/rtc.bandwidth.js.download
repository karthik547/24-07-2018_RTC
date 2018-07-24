window.rtc = window.rtc || {};
$.extend( rtc, { utils: { bandwidth: { getXHR: function(){
	var xhr = null;
	if ( window.XMLHttpRequest ) {
		xhr = new XMLHttpRequest();
	}
    else if ( window.ActiveXObject ) {
    	try { xhr = new ActiveXObject( "Msxml2.XMLHTTP" ); } catch (e){ try{ xhr = new ActiveXObject( "Microsoft.XMLHTTP" ); } catch (e){} }
    }
	return xhr;
},
	bandwidthTestStarted: false,
	latencyTimes: [],
	now: function() { return (new Date()).getTime(); },
	getLatency: function( callback ) {
		var xhr = this.getXHR(), start = rtc.utils.bandwidth.now(), t = null;

		if ( xhr === null ) {
			return false;
		}

		xhr.onreadystatechange = function() {
	    	if ( xhr.readyState == 4 ) {
	    		clearTimeout( t );

	    		rtc.utils.bandwidth.latencyTimes.push( rtc.utils.bandwidth.now() - start );

	    		if ( rtc.utils.bandwidth.latencyTimes.length < 5 && xhr.status == 200 ) {
	    			rtc.utils.bandwidth.getLatency( callback );
	    		}
	    		else {
	    			consoleLog( rtc.utils.bandwidth.latencyTimes );
	    			callback( Math.min.apply( null, rtc.utils.bandwidth.latencyTimes ) );
	    		}
	    	}
		};

		xhr.open('GET', RTCConfig.baseDir + '/css/images/1x1.gif?cb=' + rtc.utils.bandwidth.now(), true);
		xhr.send(null);

		t = setTimeout( function() {
			xhr.onreadystatechange = function() {};
			xhr.abort();
			xhr = null;
			callback( rtc.utils.bandwidth.latencyTimes.length > 0 ? Math.min.apply( null, rtc.utils.bandwidth.latencyTimes ) : 5000 );
		}, 5000 );

		return true;
	},
	get: function( latency, url, callback, timeout, advanced, progress ) {
		var xhr = rtc.utils.bandwidth.getXHR(), start = rtc.utils.bandwidth.now(), t = null, calcBdw = function( size, time, latency ) {
			var b = 0;

			try {
				if ( latency < 5000 ) {
					time -= latency;
				}

				b = Math.round( ( ( size / ( time / 1000 ) ) / 1024 ) * 8 );

				if ( isNaN( b ) ) {
					b = 0;
				}
			}
			catch ( ex ) {
				b = 0;
			}

			return b;
		};

		if ( !!advanced ) {
			progress = typeof progress == "undefined" ? function( p ) {} : progress;
			var prevProgress = 0;
			var fnc = function( size ) {
				var start = rtc.utils.bandwidth.now(), url = "/rtc/lib/diagnostics/bandwidth/" + size + "k?cb=" + start;
				xhr.onreadystatechange = function() {
					if ( xhr.readyState == 4 ) {
						var time = rtc.utils.bandwidth.now() - start;

						if ( time > 4000 || size > 16384 ) {
							progress( 1 );
							callback( calcBdw( xhr.responseText.length, time, latency ), time, xhr.responseText.length, url );
						}
						else {
							progress( Math.max( prevProgress, time / 4000 ) );
							prevProgress = time / 4000;
							setTimeout( function() {
								fnc( size * 2 );
							}, 1000 );
						}
					}
				};
				xhr.open('GET', url, true);
				xhr.send(null);
			};
			fnc( 128 );
		}
		else {
			url += '?cb='+start;
			xhr.onreadystatechange = function() {
				var b, size, time = rtc.utils.bandwidth.now() - start;
				if ( xhr.readyState == 4 && rtc.bandwidth === null ) {
					clearTimeout( t );

					try {
						size = parseInt( xhr.getResponseHeader( "Content-Length" ) ) || 204898;
						b = calcBdw( size, time, latency );
						consoleLog( "Bandwidth: " + b + " Latency: " + latency + " Time: " + time );
						consoleLog( "Size: " + size );

						if ( isNaN( b ) ) {
							b = 0;
						}

						rtc.bandwidth = b;
					}
					catch ( e ) {
						rtc.bandwidth = 0;
					}

					callback( b, time, size, url );
				}
			};
			xhr.open('GET', url, true);
			xhr.send(null);

			if ( typeof timeout != "undefined" && timeout !== null ) {
				t = setTimeout( function() {
					var size = 0;
					try {
						// get the size of the partial download - unfortunately this doesn't work in IE
						size = xhr.responseText.length;
						rtc.bandwidth = calcBdw( size, timeout, latency );
					} catch (ex) {
						rtc.bandwidth = 0;
					}
					xhr.onreadystatechange = function() {};
					xhr.abort();
					xhr = null;
					callback( rtc.bandwidth, timeout, size, url );
				}, timeout );
			}
		}
	},
	check: function( readyFn ) {
		rtc.utils.bandwidth.bandwidthTestStarted = false;
		rtc.loadSequence.progress( rtc.loadSequence.steps.bandwidthCheck );
		rtc.bandwidth = null;
		if ( !this.getLatency( function( latency ) {
			if ( !rtc.utils.bandwidth.bandwidthTestStarted ) {
				rtc.utils.bandwidth.bandwidthTestStarted = true;
				rtc.loadSequence.progress( rtc.loadSequence.steps.bandwidthStart );
				// use large file to determine bandwidth
				rtc.utils.bandwidth.get( latency, RTCConfig.baseDir + "/css/images/200k.jpg", function( b, time, size, url ) {
					rtc.utils.track("bandwidth", { bandwidth: b, time: time, size: size, latency: latency });
					readyFn();
					rtc.loadSequence.progress( rtc.loadSequence.steps.bandwidthEnd );
				}, 3000 );
			}
		} ) ) {
	    	rtc.bandwidth = 0;
	    	readyFn();
	    	rtc.loadSequence.progress( rtc.loadSequence.steps.bandwidthEnd );
	    	rtc.utils.track("bandwidth", { bandwidth: 0 });
	    	return;
		}
	},
    /**
     * Check if low bandwidth occurs
     *
     * @returns {boolean}
     */
    isDetected: function() {
        return RTCConfig.lowBandwidthThreshold && rtc.bandwidth < RTCConfig.lowBandwidthThreshold && !RTCVisit.loadtest;
    }
} } } );

