window.rtc.card = window.rtc.card || {};
$.extend(true, rtc.card, {
	// Use experimental mailto card instead of share.
	sideCardNameMap: function(name) {
		return 'share' === name && RTCConfig.shareMailToEnabled ? 'mailto' : name;
	},
	sideCardIsVisibleMap: function(name) {
		return 'mailto' === name ? 'share' : name;
	}
});

$.extend( true, rtc, {
	main: {
		playerAndBandwidthReady: function() {
			if ( rtc.bandwidth !== null && rtc.playerReady) {
				rtc.player.playVideo({state:rtc.state.startState(), firstPlay:true});
			}
		},
		initPlayer: function() {
			rtc.player.init( function() {
				rtc.playerReady = true;
				rtc.main.playerAndBandwidthReady();
			}, RTCConfig.playerOptions );
		},
		renderTimeline: function() {
			var left = $( "#timeline .ui-slider-handle" ).css( "left" );
			var markerPos = 0;

			if ( left ) {
				markerPos = StateEngine[RTCVisit.currentState].percentFromTimeline( parseInt( left.replace( /[a-z]+/, "" ) ) );
			}

			if ( rtc.utils.isVideoStatement( RTCVisit ) ) {
				StateEngine = rtc.state.removeState( StateEngine, "S_3_3", Object.keys( StateEngine ) );
				StateEngine = rtc.state.removeState( StateEngine, "S_4_12", Object.keys( StateEngine ) );
				StateEngine = rtc.state.removeState( StateEngine, "S_4_12a", Object.keys( StateEngine ) );
				if ( rtc.utils.isVideoStatementOpening( RTCVisit ) ) {
					StateEngine = rtc.state.removeState( StateEngine, "S_1_7", Object.keys( StateEngine ) );
				}
			}
			else {
				StateEngine = rtc.state.createStateEngine();
			}

			var left = $( "#timeline .ui-slider-handle" ).css( "left" );

			if ( left ) {
				rtc.timeline.setPosForce( RTCVisit.currentState, markerPos );
				rtc.player.buffering.updateProgressToMarkerPosition();
			}

			rtc.timeline.render( rtc.strings.get( "main", "timeline" ), { intermediateSections: [
				"S_4_12",
				"S_4_13",
				"S_4_14",
				"S_4_15",
				"S_4_16",
				"S_5_2"
			] } );
		}
	}
} );

$(document).ready(function() {
	rtc.utils.checkCookiesEnabled();
	rtc.main.renderTimeline();
	rtc.hd.css( "tts_hq.css" );
	rtc.utils.bandwidth.check( rtc.main.playerAndBandwidthReady );
	$('#timeline').slider({
		max:813,
		slide:function(event, ui) {
			var targetState = rtc.state.timelineTargetState(ui.value);
			var isCurrentState = rtc.state.currentState() === targetState;
			if ( !isCurrentState || !rtc.player.isMediaSet() ) {
				if ( !isCurrentState ) {
					rtc.card.hideCards();
					rtc.overlay.hideAll();
				}
				rtc.timeline.setPosForce( targetState, 0 );
				rtc.timeline.sliding = true;
				return false;
			}
			else if ( isCurrentState ) {
				rtc.timeline.sliding = false;
				return rtc.timeline.slideIgnoreRepeated(event, ui, rtc.timeline.slide);
			}
		},
		stop: function(event, ui) {
			if ( rtc.state.currentState() !== rtc.state.timelineTargetState( ui.value ) ) {
				rtc.timeline.slide( event, ui );
			}
			setTimeout( function() { rtc.timeline.sliding = false; }, 250 );
		}
	});
	// lower the z-index of the tooltips when the user hovers over the timeline slider
	$( "#timeline .ui-slider-handle" ).bind( {
		mouseover: function() {
			$( "#tooltipLayer" ).css( "z-index", 2 );
		},
		mouseout: function() {
			$( "#tooltipLayer" ).css( "z-index", "" );
		} } );

	$('.sideButtons').show().delay(500).animate({opacity:1},500);
	$('.sideButtons li').click(function() {
		rtc.card.toggleSideCard($(this).attr('class'));
	});
	rtc.session.save();
	rtc.session.setViewerProfile(RTCVisit);

	if ( RTCVisit.startState ) {
		RTCVisit.currentState = RTCVisit.startState;
	}

	$('#termsLink').click(function() {
		window.open(RTCConfig.baseDir + "/termswww.php?v=" + RTCVisit.name, '_blank');
		return false;
	});
	$('#privacyPolicyLink').click(function() {
		window.open(RTCConfig.baseDir + "/privacyPolicywww.php?v=" + RTCVisit.name, '_blank');
		return false;
	});
	$('#HelpLink').click(function() {
		rtc.utils.track("view.help");
		rtc.utils.popitup(RTCConfig.baseDir + '/help.html');
		//window.open(RTCConfig.baseDir + "/privacyPolicywww.php?v=" + RTCVisit.name, '_blank');
		return false;
	});

	rtc.utils.loadPromoImages();
	rtc.utils.lookupStats();
	rtc.utils.incrementVisits();

	rtc.utils.vars.imagesToPreload = [
        "/tts/css/images/1-6_final_frame.jpg",
        "/tts/css/themes/tts/images/ui-bg_highlight-hard_40_00498f_1x100.png"
    ];

	rtc.main.initPlayer();

	// load geoip without blocking.
	$.ajax({url:'//js.maxmind.com/js/geoip.js', dataType:'script', cache:true});

	rtc.logging.domLoadComplete();

	rtc.utils.showCorporateFirewallDetected();

	rtc.player.documentReady();
	rtc.app.documentReady();
	rtc.timeline.documentReady();
	rtc.utils.documentReady();
	rtc.overlay.documentReady();
});
