var Overlay = Class.extend({
	init: function(name,start,end,startFn,endFn){
		this.name = name;
		this.start = start;
		this.end = end;
		this.startFn=startFn ? startFn : rtc.overlay.show;
		this.endFn=endFn ? endFn : rtc.overlay.hide;
		this.isShowing=false;
		this.patchTimings();
	},
	patchTimings:function() {
		if ( 'screenOverlay1_7' === this.name ) {
			this.start = parseFloat( this.start ) - 1.5;
		}

		if ( $( "#jquery_jplayer_videoplayer video" ).length > 0 ) { // only apply if playing MP4 video
			if ('referral_name_mc' === this.name) {
				this.end = parseFloat(this.end) - 2;
			};
			if ('wsj_weather_mc' === this.name) {
				this.start = parseFloat(this.start) + 0.4;	//Without parseFloat, this.start is treated as a string and, for example, becomes 20.361.3 .
				this.end = parseFloat(this.end) - 0.2;
			};
			if ('fsi_mc' === this.name) {
				this.end = parseFloat(this.end) + 0.2;
			};
			if ('stats_mc' === this.name) {
				this.start = parseFloat(this.start) - 0.5;
			};
			if ('name_lights_mc' === this.name) {
//				this.start = parseFloat(this.start) - 0.7;
				this.end = parseFloat(this.end) - 1.5;
			};
		}
		else {
			if ('referral_name_mc' === this.name) {
				this.end = parseFloat(this.end) - 2;
			};
			if ('name_lights_mc' === this.name) {
				this.start = parseFloat(this.start) - 0.5;
				this.end = parseFloat(this.end) - 1.5;
			};
		}
	},
	update : function(time)
	{
		if((time > this.start && time < this.end) && !this.isShowing)
		{
			this.isShowing=true;
			this.startFn(this.name);
		}
		if((time < this.start || time > this.end) && this.isShowing)
		{
			this.isShowing=false;
			this.endFn(this.name);
		}
	}
});
$.extend(true, rtc.overlay, {
	dataForVisit: function(name, visit, overlayData) {
		visit = visit ? visit : {};
		overlayData = overlayData ? overlayData : {};

		var firstname = function() {
			var firstname = visit.fullname ? visit.fullname.split(/\s+/)[0] : '';
			firstname = (!(firstname && firstname.length) && visit.firstname) ? visit.firstname.split(/\s+/)[0] : firstname;
			return firstname.length ? firstname : 'Firstname';
		};

		var dayMonthDate = function() {
			var d = new Date();
			return rtc.overlay.weekday(d) + " " + rtc.overlay.month(d) + " " + rtc.overlay.dateAndSuffix(d); // Thursday February 16th
		};

		var r = {
				"profile_mc": function() {
					var ret = {};

					ret.firstname = firstname(visit);
					ret.browser = $.browserrtc.browserrtc() + ' ' + $.browserrtc.version.number();
					ret.timezone = jstz.determine_timezone().name();
					ret.loc = rtc.utils.geoIp.getCity("Unknown");
					/*if (visit.zip) {
						ret.loc = ret.loc + ' / ' + visit.zip;
					}*/
					ret.localtime = new Date().toTimeString().substring(0, 5);
					ret.weather = visit.weather ? visit.weather : 'Unknown';
					ret.zip = visit.zip ? visit.zip : '';
					ret.visitCount = RTCVisit.visitCount ? RTCVisit.visitCount : 1;//'1'; // FIXME actually find the visitCount.

					return ret;
				},
				"wsj_date_mc": function() {

					return {
						date: dayMonthDate()
					};
				},
				"name_lights_mc": {
					firstname: firstname(visit)
				},
				"fsi_mc": function() {

//					fsi_mc.journey_txt.text = "Juneau, WI 53202 to Lexington, KY 48502";
//					fsi_mc.fuel_date_txt.text = "03/05/2012"; //Format "00/00/0000"
//					fsi_mc.fuel_type_txt.text = "ProMiles Retail"; //Format "ProMiles Retail"
//					fsi_mc.route_method_txt.text = "Truck Practical"; //Format "Truck Practical"
//					fsi_mc.mpg_txt.text = "6.0"; //Format "0.00"
//					fsi_mc.cpg_adjust_txt.text = "0.0"; //Format "-0.0000"
//					fsi_mc.trigger_txt.text = "$1.21"; //Format "$0.000"
//					fsi_mc.stop_count_txt.text = "32"; //Format "000"
//					fsi_mc.fuel_surcharge_txt.text = "$220.33"; //Format "$000.00"
//					fsi_mc.cpg_txt.text = "$3.978"; //Format "$0.000"
//					fsi_mc.trip_dist_txt.text = "477.5mi."; //Format "0000.0 mi."
//					fsi_mc.cpm_txt.text = "$0.663"; //Format "$0.000"
//					//Calculate the DOE
//					fsi_mc.doe_cpg_txt.text = "$3.914";
//					fsi_mc.doe_fuel_surcharge_txt.text = "$215.19";
//					//DOE Fuel surcharge
//					fsi_mc.doe_miscalc_txt.text = "$5.14";
	//
					var defaultFSIData = {

								journey: "Juneau, WI 53202 to Lexington, KY 48502",
								fuel_date: "03/05/2012",
								fuel_type: "ProMiles Retail",
								route_method: "Truck Practical",
								mpg: "6.0",
								cpg_adjust: "0.0",
								trigger: "1.21",
								stop_count: 32,
								fuel_surcharge: "1220.33",
								cpg: "3.978",
								trip_dist: "477.5",
								cpm: "0.663",
								origin_place: "Beverly Hills, CA",
								origin_state: "CA",
								area: "West Coast",
								doe_price: "3.914",
								date: "07/30/2012"

					};
					return overlayData.fsiData ? overlayData.fsiData : defaultFSIData;
				},
				"stats_mc": function() {
					var stats =  overlayData.lateststats ? overlayData.lateststats : {};
					stats.date = dayMonthDate() + ' ' + new Date().getFullYear();
					return stats;
				},
				"wsj_weather_mc": function() {
					var weatherData = overlayData.weatherData ? overlayData.weatherData : rtc.overlay.weather.weatherDefaults;
					weatherData.weather_date = weatherData.date;
					return weatherData;
				},
				"referral_name_mc": {
					referral_text: visit.referral + '<br/>said you might stop by!'
				}
		};

		var ret = r[name];
		if ('function' == typeof ret) {
			ret = ret();
		}


//		var defaults = {
//				firstname: 'Default name',
//				company: 'Default co',
//				loc: 'default loc',
//				interest: 'default interest'
//			};
//		ret = $.extend(defaults, ret);
//		consoleLog(ret.firstname);
		return ret;
	},
	fns: {
		name_lights_mc: function() {
			$('#name_lights_mc').find('#name').text(rtc.overlay.data("name_lights_mc").firstname);
		},
		profile_mc :function(){
			var overlayDatavals=rtc.overlay.data("profile_mc");
			var overlayProfile=$('#profile_mc');
			overlayProfile.find("#name").text(overlayDatavals.firstname);
			overlayProfile.find('#browser').text(overlayDatavals.browser);
			overlayProfile.find('#visitCount').text(overlayDatavals.visitCount);
			overlayProfile.find('#location').text(overlayDatavals.loc);
			overlayProfile.find('#zip').text(overlayDatavals.zip);
			overlayProfile.find('#weather').text(rtc.overlay.titleCase(overlayDatavals.weather));
			overlayProfile.find('#localtime').text(overlayDatavals.localtime);
			overlayProfile.find('#timezone').text(overlayDatavals.timezone);
		},
		fsi_mc: function() {
			var o=rtc.overlay.data("fsi_mc");
			var fsi=$('#fsi_mc');

			fsi.find("#journey").text(o.journey);

			fsi.find("#date").text(o.fuel_date);
			fsi.find("#fuel_type").text(o.fuel_type);
			fsi.find("#route_method").text(o.route_method);

			fsi.find("#mpg").text(o.mpg);
			fsi.find("#cpg_adjust").text(o.cpg_adjust);
			fsi.find("#trigger").text('$' + o.trigger);
			fsi.find("#stop_count").text(o.stop_count);

			fsi.find("#fuel_surcharge").text('$' + o.fuel_surcharge);
			fsi.find("#cpg	").text('$' + o.cpg);
			fsi.find("#trip_dist").text(o.trip_dist + " mi.");
			fsi.find("#cpm").text('$' + o.cpm);

			var doe_surcharge = (( o.doe_price - o.trigger ) * o.trip_dist / o.mpg).toFixed(2);
			fsi.find("#doe_surcharge").text("$" + doe_surcharge); // FIXME round 2 d.p.
			fsi.find("#doe_price").text("$" + o.doe_price);
			var doe_error = Math.abs(o.fuel_surcharge - doe_surcharge).toFixed(2);
			if (doe_error.length > 5){
				doe_error = Math.round(Number(doe_error));
			}
			fsi.find("#miscalculation").text("$" + doe_error);
		},
		stats_mc: function() {
			var o=rtc.overlay.data("stats_mc");
			var stats=$('#stats_mc');

			stats.find("#date").text(o.date);

			stats.find('#vid_played').html(rtc.overlay.formatNumbers(o.videos_played) + '<span style="font-size:16px;"> VIDEOS</span>');
			stats.find('#meet_held').html(rtc.overlay.formatNumbers(o.meetings_held) + '<span style="font-size:16px;"> MEETINGS</span>');
			stats.find('#meet_dur').html(rtc.overlay.formatNumbers(o.average_meeting_duration_mins) + '<span style="font-size:16px;"> MINS</span> ' + o.average_meeting_duration_secs + '<span style="font-size:16px;"> SECS</span>');
			stats.find('#meet_over').html(rtc.overlay.formatNumbers(o.meetings_over_thirty_mins) + '<span style="font-size:16px;"> MEETINGS</span>');
			stats.find('#meet_suc').html(rtc.overlay.formatNumbers(o.successful_meetings) + '<span style="font-size:16px;"> MEETINGS</span>');

		},
		wsj_date_mc: function(){
			$('#wsj_date_mc').text(rtc.overlay.data("wsj_date_mc").date);
		},
		wsj_weather_mc: function(){
			var weatherdiv=$('#overlays').find('#wsj_weather_mc');
			var o=rtc.overlay.data("wsj_weather_mc");
			weatherdiv.find('#date').text(o.weather_date);
			weatherdiv.find('#maxtemp').text(o.max_temp);
			weatherdiv.find('#mintemp').text(o.min_temp);
			weatherdiv.find('#weather_img').attr("src",RTCConfig.baseDir + "/css/images/weather/" + rtc.overlay.weather.weatherMap(o.weather) + ".png");
			weatherdiv.find('#location').text(o.location);
		},
		referral_name_mc: function(){
			$('#referral_name_mc').html(rtc.overlay.data("referral_name_mc").referral_text);
		}
	},
	fnMapShow: {
		screenOverlay1_7: function(name) {
			if ( !rtc.utils.isVideoStatementOpening( RTCVisit ) ) {
				rtc.card.showCardByName('1-7', function() {} /* don't call rtc.player.controls.playBtnPause()*/);
			}
		},
		buttonOverlay3_1: function(name) {
			$('#sideButtonsBackground').rtcFadeIn('slow');
		},
		name_lights_mc: function(name) {
			rtc.overlay.fns[name]();
			$('#overlays').find('#'+ name).rtcFadeIn(1000);
		},
		profile_mc: function(name) {
			rtc.overlay.fns[name]();
			$('#overlays').find('#'+ name).rtcFadeIn(1000);
		},
		fsi_mc: function(name) {
			rtc.overlay.fns[name]();
			$('#overlays').find('#'+ name).rtcFadeIn(1000);
		},
		stats_mc: function(name) {
			rtc.overlay.fns[name]();
			$('#overlays').find('#'+ name).rtcFadeIn(1000);
		},
		wsj_date_mc: function(name) {
			rtc.overlay.fns[name]();
			$('#overlays').find('#'+ name).rtcFadeIn(1000);
		},
		wsj_weather_mc: function(name) {
			rtc.overlay.fns[name]();
			$('#overlays').find('#'+ name).show();
			var fadetime=300;
			weatherdiv=$('#overlays').find('#wsj_weather_mc');
			weatherdiv.find('#datediv').animate({
				width: 166
			},fadetime);
			weatherdiv.find('#locationdiv').animate({
				width: 166
			},fadetime);
			weatherdiv.find('#temperature').animate({
				width: 166,
				left: 418
			},fadetime);
			weatherdiv.find('#weather').animate({
				width: 166,
				left: 418
			},fadetime);
		},
		referral_name_mc: function(name) {
			rtc.overlay.fns[name]();
			$('#overlays').find('#'+ name).rtcFadeIn(1000);
		}
	},
	fnMapHide: {
		screenOverlay1_7: function() {
			if ( !rtc.utils.isVideoStatementOpening( RTCVisit ) ) {
				consoleLog('hide screenOverlay1_7');
				$('#card17').dialog('close');
			}
		},
		buttonOverlay3_1: function() {
			$('#sideButtonsBackground').rtcFadeOut('slow');
		},
		name_lights_mc: rtc.overlay.fade,
		profile_mc: rtc.overlay.fade,
		fsi_mc: rtc.overlay.fade,
		stats_mc: rtc.overlay.fade,
		wsj_date_mc: rtc.overlay.fade,
		wsj_weather_mc: function(name) {
			var fadetime=400;
			weatherdiv=$('#overlays').find('#wsj_weather_mc');
			weatherdiv.find('#datediv').animate({
				width: 0
			},fadetime);
			weatherdiv.find('#locationdiv').animate({
				width: 0
			},fadetime);
			weatherdiv.find('#temperature').animate({
				width: 0,
				left: 584
			},fadetime);
			weatherdiv.find('#weather').animate({
				width: 0,
				left: 584
			},fadetime,function() {
				rtc.overlay.fade(name);
			});
		},
		referral_name_mc: rtc.overlay.fade
	}
});
$.extend(true, rtc.overlay, {

	parseState: function(location) { // TODO: namespace
		return location ? $.trim(location.replace(/([^,]*,)/g, '')) : null;
	},

	weekday: function(d) {
		var d_names = new Array("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday");
		var curr_day = d.getDay();
		return d_names[curr_day];
	},

	month: function(d) {
		var m_names = new Array("January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December");
		var curr_month = d.getMonth();
		return m_names[curr_month];
	},

	dateAndSuffix: function(d) {
		var curr_date = d.getDate();
		var sup = "";
		if (curr_date == 1 || curr_date == 21 || curr_date ==31)
		{
			sup = "st";
		}
		else if (curr_date == 2 || curr_date == 22)
		{
			sup = "nd";
		}
		else if (curr_date == 3 || curr_date == 23)
		{
			sup = "rd";
		}
		else
		{
			sup = "th";
		}
		return curr_date + sup;
	},

	weekdayDateSuffix: function(d) {
		return rtc.overlay.weekday(d) + " " + rtc.overlay.dateAndSuffix(d); // Thursday 16th
	},

	monthDate: function(d) {
		return rtc.overlay.month(d) + " " + rtc.overlay.dateAndSuffix(d); // February 16th
	},

	formatNumbers: function(n) {
		return n ? n.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") : n;
	},

	titleCase: function(str) {
		str = str || '';
		str = str.replace(/_/g, ' ');
	    return str.replace(/\w\S*/g, function(txt){return txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase();});
	}
});
$.extend(true, rtc.overlay, {
	weather: {
		weatherDefaults : {
			max_temp: 82,
			min_temp:59,
			weather: '',
			date: rtc.overlay.monthDate(new Date()),
			location: 'Las Vegas',
			region: 'NV',
			state: 'Nevada'
		},
		getUrl: function(zip, visitCntrycode, geoip_city, geoip_region_name, geoip_country_code) {

			var units = 'US' === visitCntrycode ? 'f' : 'c';

			zip = $.trim(zip);
			geoip_city = $.trim(geoip_city);
			geoip_region_name = $.trim(geoip_region_name);
			geoip_country_code = $.trim(geoip_country_code);

			var parts = [];

			if (visitCntrycode && zip) {
				parts.push(zip, visitCntrycode);
			}
			else {
				if (geoip_city) {
					parts.push(geoip_city);
				}
				else if ('GB' === geoip_country_code) {
					parts.push('London');
				}
				if (geoip_country_code) {
					parts.push(geoip_country_code);
				}
			}


		//	if (!parts.length && zip) {
		//		parts.push(zip);
		//	}

			if (!parts.length) { // Default to New York
				parts.push('New York', 'NY', 'US');
				units = 'f';
			}

		//	parts = ['Charlotte', 'NC', 'US']; // for testing.

			// Only allow cached results if they were in the last hour
			var cachebuster = '&tcb=' + Math.round(new Date().getTime() / (3600 * 1000));
			return RTCConfig.baseDir + '/api/weather?weather=' + encodeURIComponent(parts.join(',')) + '&u=' + units + cachebuster;
		},
		conditionCodes: {
				0: 'tornado',
				1: 'tropical storm',
				2: 'hurricane',
				3: 'severe thunderstorms',
				4: 'thunderstorms',
				5: 'mixed rain and snow',
				6: 'mixed rain and sleet',
				7: 'mixed snow and sleet',
				8: 'freezing drizzle',
				9: 'drizzle',
				10: 'freezing rain',
				11: 'showers',
				12: 'showers',
				13: 'snow flurries',
				14: 'light snow showers',
				15: 'blowing snow',
				16: 'snow',
				17: 'hail',
				18: 'sleet',
				19: 'dust',
				20: 'foggy',
				21: 'haze',
				22: 'smoky',
				23: 'blustery',
				24: 'windy',
				25: 'cold',
				26: 'cloudy',
				27: 'mostly cloudy (night)',
				28: 'mostly cloudy (day)',
				29: 'partly cloudy (night)',
				30: 'partly cloudy (day)',
				31: 'clear (night)',
				32: 'sunny',
				33: 'fair (night)',
				34: 'fair (day)',
				35: 'mixed rain and hail',
				36: 'hot',
				37: 'isolated thunderstorms',
				38: 'scattered thunderstorms',
				39: 'scattered thunderstorms',
				40: 'scattered showers',
				41: 'heavy snow',
				42: 'scattered snow showers',
				43: 'heavy snow',
				44: 'partly cloudy',
				45: 'thundershowers',
				46: 'snow showers',
				47: 'isolated thundershowers',
				3200: 'not available'
		},

		conditionImages: { // TODO: namespace
				0: 'chance_of_storm',
				1: 'chance_of_storm',
				2: 'chance_of_storm',
				3: 'chance_of_storm',
				4: 'chance_of_storm',
				5: 'chance_of_snow_showers',
				6: 'chance_of_snow_showers',
				7: 'chance_of_snow',
				8: 'chance_of_snow_showers',
				9: 'chance_of_rain',
				10: 'rain',
				11: 'showers',
				12: 'showers',
				13: 'snow',
				14: 'snow',
				15: 'snow',
				16: 'snow',
				17: 'rain',
				18: 'chance_of_snow_showers',
				19: 'clear',
				20: 'fog',
				21: 'partly_sunny',
				22: 'smoky',
				23: 'clear',
				24: 'clear',
				25: 'cold',
				26: 'cloudy',
				27: 'mostly cloudy (night)',
				28: 'mostly cloudy (day)',
				29: 'mostly_cloudy (night)',
				30: 'mostly_cloudy (day)',
				31: 'clear (night)',
				32: 'mostly_sunny',
				33: 'mostly_sunny (night)',
				34: 'mostly_sunny (day)',
				35: 'chance_of_snow_showers',
				36: 'mostly_sunny',
				37: 'chance_of_storm',
				38: 'chance_of_storm',
				39: 'chance_of_storm',
				40: 'chance_of_showers',
				41: 'snow',
				42: 'chance_of_snow',
				43: 'snow',
				44: 'mostly_cloudy',
				45: 'chance_of_storm',
				46: 'chance_of_snow_showers',
				47: 'chance_of_storm',
				3200: 'not available'
		},

		parseWeatherResponse: function(data, visitCntryCode) { // TODO: namespace
//			if ($(data).find('problem_cause').length) {
//				return null;
//			}

			try {
				if ( typeof data.query.results.channel.item.forecast == "undefined" ) {
					return null;
				}
			}
			catch ( e ) {
				return null;
			}

			var forecastConditions = data.query.results.channel.item.forecast[0];
			var condition = rtc.overlay.weather.conditionImages[forecastConditions.code];
			condition = condition.replace(/ \(.*\)/, '');
			condition = 'Fog' === condition ? 'Overcast' : condition;
			consoleLog('Forecast:[' + condition + '].');
			RTCVisit.weather = condition;

			// Only lowercase for overlay data because RTCVisit.weather (used by rtc.video.params.js) needs to be capitalized.
			condition = condition ? condition.toLowerCase().replace(/ /g, '_') : null;
			var weatherData = {
				max_temp: forecastConditions.high,
				min_temp: forecastConditions.low,
				weather: condition,
				date: forecastConditions.date,
				location: data.query.results.channel.location.city,
				region: data.query.results.channel.location.region
			};
			if ('US' === visitCntryCode) {
				weatherData.state = data.location.state_abbreviation;
			}

			return weatherData;
		},
		lookupWeather: function(zip, visitCntrycode, attempts) { // TODO: namespace
			if(attempts === undefined) attempts = 0;
			if (attempts > 4) {
				RTCOverlayData.weatherData = null;
				return;
			}
			$.get( rtc.overlay.weather.getUrl(zip, visitCntrycode, rtc.utils.geoIp.getCity(), rtc.utils.geoIp.getRegionName(), rtc.utils.geoIp.getCountryCode()), function(data) {
				RTCOverlayData.weatherData = rtc.overlay.weather.parseWeatherResponse(data);
				if (!RTCOverlayData.weatherData) {
					rtc.overlay.weather.lookupWeather(null, null, ++attempts);
				}
			}).error(function() {
				rtc.overlay.weather.lookupWeather(zip, visitCntrycode, ++attempts);
			});
		},
		weatherMap: function (condition) // TODO: namespace
		{
			var weathertypes=Array ("chance_of_snow_showers","chance_of_snow","snow","chance_of_rain","chance_of_showers","chance_of_storm","clear","cloudy","fog","mostly_cloudy","mostly_sunny","overcast","partly_sunny","rain","showers","thunderstorm");
			if ($.inArray(condition, weathertypes)>-1)
			{
				return condition;
			}
			switch(condition)
			{
			case 'partly_sunny': return 'mostly_sunny';
			case 'scattered_thunderstorms': return 'thunderstorm';
			case 'scattered_showers': return 'showers';
			case 'rain_and_snow': return 'snow';
			case 'light_snow': return 'snow';
			case 'freezing_drizzle': return 'showers';
			case 'sunny': return 'clear';
			case 'partly_cloudy': return 'mostly_sunny';
			case 'mist': return 'fog';
			case 'storm': return 'thunderstorm';
			case 'chance_of_tstorm': return 'chance_of_storm';
			case 'sleet': return 'rain';
			case 'icy': return 'snow';
			case 'dust': return 'mostly_sunny';
			case 'smoke': return 'fog';
			case 'haze': return 'fog';
			case 'flurries': return 'chance_of_snow_showers';
			case 'light_rain': return 'showers';
			case 'snow_showers': return 'chance_of_snow_showers';
			case 'hail': return 'rain';
			default: return 'mostly_sunny';
			}
		}
	}
});

var overlay = function(name, hide, stop, start, offset) {
	consoleLog('overlay name:[' + name + '], hide:[' + hide + '], stop:[' + stop + '], start:[' + start + '], offset:[' + offset + '].');
	if (hide) {
		rtc.overlay.hide(name);
	}
};
