// left menu
/*jQuery(document).ready(function(){
	$('#custom-nav-left').mouseenter(function(){
		$(this).animate({
			left: 0
		}, 100 );
	});
	$('#custom-nav-left').mouseleave(function(){
		$(this).animate({
			left: -166
		}, 100 );
		$('.custom-nav-left>nav>ul>li>ul').hide();
		$('#custom-nav-left p').parent().removeClass('active');
	});
	$('#custom-nav-left p').click(function(){
		$('#custom-nav-left p').parent().removeClass('active');
		$(this).parent().addClass('active');
		$('.custom-nav-left>nav>ul>li>ul').hide();
		$(this).next('ul').show();
	});
});*/

function checkWordLenth(obj, maxLength, target){
	var str = obj.value; // 이벤트가 일어난 컨트롤의 value 값
	var strLength = str.length; // 전체길이

	if (isMaxLength(strLength, maxLength)) {
		$('#'+target).text(maxLength + "/" + maxLength);
		$(obj).val(str.substring(0, 5000));
		return;
	}
	$('#'+target).text(strLength + "/" + maxLength);
}

function checkWordLenth2(obj, maxLength, target){
	var str = obj.value; // 이벤트가 일어난 컨트롤의 value 값
	var strLength = str.length; // 전체길이

	if (isMaxLength(strLength, maxLength)) {
		$('#'+target).text(maxLength + "/" + maxLength);
		$(obj).val(str.substring(0, maxLength));
		return;
	}
	$('#'+target).text(strLength + "/" + maxLength);
}

function isMaxLength(length, maxLength) {
	if (length > maxLength) {
		return true;
	}
	return false;
}

function numberWithCommas(number) {
    return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
}

/*
 * 쿠키생성
 * cDay : 일(날짜)
 * */
function setCookie(cName, cValue, cDay) {
	var expire = new Date();
	//expire.setDate(expire.getDate() + (cDay * 24 * 60 * 60 * 1000));
	expire.setDate(expire.getDate() + cDay);
	document.cookie = cName + '=' + escape(cValue) + '; path=/; expires=' + expire.toGMTString() + ';';
}

/*
 * 쿠키생성
 * cMsec : 밀리세컨드
 * */
function setCookieMsec(cName, cValue, cMsec) {
	var expire = new Date();
	expire.setTime(expire.getTime() + cMsec);
	document.cookie = cName + '=' + escape(cValue) + '; path=/; expires=' + expire.toGMTString() + ';';
	return cName + '=' + escape(cValue) + '; path=/; expires=' + expire.toGMTString() + ';';
}

// 쿠기가져오기
function getCookies(cName) {
	var name = cName + '=';
	var ca = document.cookie.split(';');
	for (var i=0; i < ca.length; i++) {
		var c = $.trim(ca[i]);
		if (c.indexOf(name) == 0) {
			return c.substring(name.length, c.length);
		}
	}
	return '';
}


// 배열 스위치
function arrayMove(arr, srcIndex, dstIndex) {
	if (dstIndex >= arr.length) {
        var k = dstIndex - arr.length;
        while ((k--) + 1) {
        	arr.push(undefined);
        }
    }
	arr.splice(dstIndex, 0, arr.splice(srcIndex, 1)[0]);
}

// 타이머
(function($) {
	"use strict";

	var defaults = {
		second: 10,
		callback: function() {}
	};

	$.fn.countdown = function(method, options) {

		var $this = this;

		var stop = function() {
			var timer = $this.data('timer');
			clearTimeout(timer);
		};

		var start = function(second, callback) {
			var expireTime = second;
			if (expireTime < 0) {
				callback();
				return;
			}

			var min = Math.floor(expireTime / 60);
			var sec = expireTime % 60;

			$this.text(min + '분 ' + sec + '초');

			expireTime--;

			var t = setTimeout(function() {
				start(expireTime, callback);
			}, 1000);

			$this.data('timer', t);

			if (!$this.is(':visible')) {
				stop();
			}
		};

		if (typeof method === 'string' && method === 'stop') {
			stop();
		} else if (typeof method === 'object') {
			options = method;

			var settings = $.extend({}, defaults, options);
			start(settings.second, settings.callback);
		}
	};

})(jQuery);

function formatCardNo(cardNo) {
	return cardNo.substring(0, 4) + '-' + cardNo.substring(4, 8) + '-' + cardNo.substring(8, 12) + '-' + cardNo.substring(12, 16);
}

function formatDate(date, format) {
	var yyyy = date.getFullYear();
	var mm = date.getMonth() + 1;
	var dd = date.getDate();

	mm = mm < 10 ? "0"+mm.toString() : mm.toString();
	dd = dd < 10 ? "0"+dd.toString() : dd.toString();

	return yyyy + format + mm + format + dd;
}

function mobileCheck() {
	if( navigator.userAgent.match(/Android/i) || navigator.userAgent.match(/ANDROID/i) || navigator.userAgent.match(/MegaBox\/ANDROID/i)) {
		return 'android';
	} else if (navigator.userAgent.match(/iPhone/i)
			|| navigator.userAgent.match(/iPad/i)
			|| navigator.userAgent.match(/iPod/i) || navigator.userAgent.match(/MegaBox\/IOS/i)) {
		return 'ios';
	} else {
		return 'other';
	}
}

function isNativeApp(){
	if(navigator.userAgent.match(/MegaBox/i)) return true;
	return false;
}

// keyBlock으로 동작안될 때 대체용
function nextFocus(obj, nextObj) {
	alert('a');
	if(obj.value.length==4)
		$(nextObj).focus();
}

function nextFocus2(obj, nextObj) {
	alert('aas');
	if(obj.value.length==4)
		document.getElementById(nextObj).focus();
}

function measureScrollbar() {
	var scrollDiv = document.createElement('div');
    scrollDiv.className = 'modal-scrollbar-measure';
    this.$body.append(scrollDiv);
    var scrollbarWidth = scrollDiv.offsetWidth - scrollDiv.clientWidth;
    this.$body[0].removeChild(scrollDiv);
    return scrollbarWidth;
}

function goMovieDetail(code)
{
    var type = mobileCheck();
    var urlString = 'mobile://detail?code='+code;

    if ( type == 'ios' )
    {
            window.location = urlString;
    }
    else if( type == 'android' )
    {
        window.callbackandroid.callAndroid(urlString);
    }
    else
    {
    	MovieDetail.showPage(code);
    	$("#movie_detail").css({"z-index":"1041"});
    	$("div.movie_detail").css({"z-index":"1040"});
    }
}

function goBooking(code)
{
   var type = mobileCheck();
   var urlString = 'mobile://Booking.showPageSelectedMovie?code='+code;

   if ( type == 'ios' )
   {
      window.location = urlString;
   }
   else if( type == 'android' )
   {
         window.callbackandroid.callAndroid(urlString);
   }
   else {
      Booking.showPageSelectedMovie(code);
   }
}

/** <PRE>
 * 소수점 num자리이하 버림
 *
 * 메소드 : floor
 * 작성자 : 천창환
 *
 * @param retVal
 * @param num
 * @return
 * </PRE> */
function floor(retVal, num) {
	try{
		var i = 1.0;

		for(var a=0; a< num; a++)	i = i*10.0;

		retVal = parseFloat(Math.floor(retVal * i) / i);
	} catch(e) {

    }

	return retVal;
}

String.prototype.replaceAll = function(str1,str2) {
	var tmp = eval("/\\"+str1+"/g");
	return this.replace(tmp,str2);
}

String.prototype.trim = function() {
    return this.replace(/(^\s*)|(\s*$)/gi, "");
}

function getServerUrl(secureYn){

	var result = "";

	if(location.hostname.indexOf('localhost')>-1){
		if(secureYn){
			result = "https://" + location.hostname +":8443";
		}else{
			result = "http://" + location.hostname + ":8080";
		}
	}else{
		if(secureYn){
			result = "https://" + location.hostname;
		}else{
			result = "http://" + location.hostname;
		}
	}

	return result;

}

/*
 * 배너팝업 URL 구하기
 * System.getFrontBanner 서비스 참고
 * linkType : 링크 종류
 * link : 타입이 EVENT일 때는 이벤트 코드
 * */
function getBannerPopupUrl(linkType, link){

	var result = "";

	if(linkType == 'EVENT'){

		result = '/?menuId=event&p=detail&p2=' + link;

	// 나머지는 추후에
	} else if(linkType == 'URL'){
		result = link;
	}

	return result;
}

// dataSource 클릭 시 클릭 수 인자 전달
function adHasClick(link, adClick, linkType) {
	// alert(adClick);
	if(adClick > 0) {
		$.ajax({
			url	  : '/DataProvider',
			type  : 'POST',
			async : true,
			data  : {
				_command : 'System.adHasClicked',
				adClick	 : adClick,
				siteCode : '36'
			}
		}).success(function( data ) {
		});
	}

	//URL 값이 없을때는 종료
	if (link == null || link.length == 0) {
		return;
	}

	// 알반 링크 or 팝업
	if(linkType == 'link') location.href = link;
	else if(linkType == '_blank') window.open(link, '_blank');
	else window.open(link);
}
var _isPasswordPop = false;

function passwordLengthCheck(obj) {
	var value  = $(obj).val();
	var length = $(obj).attr('maxlength');

	if (value.length == Number(length) && !_isPasswordPop) {
		var alert = messageBox('비밀번호는 최대 ' + length + '자리까지 입력하실 수 있습니다');
		$(alert).on('click', 'button.ok', function(){
			$('#mask:eq(0)').remove();
			_isPasswordPop = true;
			$(obj).focus();
		}).on('click', 'button.btn_ca_close', function(){
			$('#mask:eq(0)').remove();
			_isPasswordPop = true;
			$(obj).focus();
		});
//		messageBox('비밀번호는 최대 ' + length + '자리까지 입력하실 수 있습니다', function(){
//			_isPasswordPop = true;
//			$(obj).focus();
//		});
	} else {
		_isPasswordPop = false;
	}
}

function getCinemaPopup(region, cinema){

	if (region == null || region == 'null' || region == 'undefined' || region.length == 0)	return;
	if (cinema == null || cinema == 'null' || cinema == 'undefined' || cinema.length == 0)	return;

	$.ajax({
		url: '/DataProvider',
		type: 'POST',
		async : false,
		data: {
			_command: 'Booking.getCinemasByMovie2',
			siteCode : '36',
			areaGroupCode: region,
			movieCode1 : '',
			movieCode2 : '',
			movieCode3 : '',
			movieCode4 : '',
			korEngGubun : '1'
		}
	}).success(function( data ) {
		var cinemaList = data.list;

		if (cinemaList != null && data.resultCode == "0000") {
			$.each(cinemaList, function(idx, item){
				if (item.cinemaCode == cinema) {
					var popupYn = item.popupYn;
					var popupMsg = item.popupMsg;
					if (popupYn == "Y" && (popupMsg != null && popupMsg.length > 0)) {
						messageBox(popupMsg);
						return;
					}
				}
			})
		}
	});

}

function checkDormantAccount() {
	$.ajax({
		url: getServerUrl(true) + '/DataProvider',
		type: 'POST',
		crossDomain: true,
	      xhrFields: {
	        withCredentials: true
	      },
		data: { _command: "User.userDormantProcess" }
	}).success(function( data ) {
		if(data.resultCode == "0000") {
			location.href = "/?menuId=user-dormant-input";
		} else {
			location.href = "/?menuId=user-dormant";
		}
	});
}

/**
 * 앱 인스톨 여부 확인
 **/
function launchApp(command, code) {
	// 앱 기동 시도 시간
	var startTime = new Date();

	// OS 판별
	var isAnd = (navigator.userAgent.match(/android/i) || navigator.userAgent.match(/ANDROID/i)) ? true : false;
	var isIos = (navigator.userAgent.match(/iPhone/i) || navigator.userAgent.match(/iPad/i) || navigator.userAgent.match(/iPod/i) || navigator.userAgent.match(/IOS/i)) ? true : false

	//앱으로 이동 처리 실패시 처리
	setTimeout(function() {
		//app 실행후 다시 브라우저로 왔을때 setTimeout이 실행되는 경우가 있으므로
		//2초 이내에 setTimeout 이 실행된 경우에만 앱 설치페이지로 이동함
		var re_start = new Date();
		var datediff = re_start.getTime() - startTime.getTime();
		if (datediff > 2000) {
			return;
		}

		if (isAnd) {
			//안드로이드는 앱 이동 처리 내에서 마켓 이동
		} else if (isIos) {
			//app store 이동
			window.location.href = "https://itunes.apple.com/kr/app/megabox/id894443858?l=ko&ls=1&mt=8";
		} else {
			//PC인 경우에는 앱 이동 처리 내에서 페이지 이동
		}
	}, 1000);

	//이동 URL 작성
	var schUrl = "megaboxapp://" + command + "?";	// scheme URL(앱 기동용)
	var webUrl = "http://www.megabox.co.kr/?";		// Web URL(홈페이지 용)
	switch(command) {
		case 'movieDetail':
			schUrl = schUrl + "movieCode=" + code + "&cinemaCode=&eventCode=";
			webUrl = webUrl + "show=detail&rtnShowMovieCode=" + code;
			break;
		case 'booking':
			schUrl = schUrl + "movieCode=" + code + "&cinemaCode=&eventCode=";
			webUrl = webUrl + "show=booking&rtnShowMovieCode=" + code;
			break;
		case 'cinemaDetail':
			schUrl = schUrl + "movieCode=&cinemaCode=" + code + "&eventCode=";
			webUrl = webUrl + "menuId=theater-detail&cinema=" + code;
			break;
		case 'eventDetail':
			schUrl = schUrl + "movieCode=&cinemaCode=&eventCode=" + code;
			webUrl = webUrl + "menuId=event&p=detail&p2=" + code;
			break;
		default:
			schUrl = "megaboxapp://";
			webUrl = "http://www.megabox.co.kr/";
			break;
	}

	//앱으로 이동 처리
	if (isAnd) {
		window.location.href = "market://details?id=com.megabox.mop&url=" + schUrl;
	} else if (isIos) {
		window.location.href = schUrl;
	} else {
		window.location.href = webUrl;
	}
}


var LUser = {
	getRId : function(){
		var RId = "";
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async : false,
			data: {
				_command: 'User.getRId',
			}
		}).success(function( data ) {
			if (data.RId != null && data.resultCode == "0000") {
				RId = data.RId;
			}
		});
		return RId;
	},

	URLForward : function(webURL){
		if(!webURL){
			messageBox("이동할 URL정보가 없습니다.");
			return false;
		}
		var RId = LUser.getRId();
		if(RId){
			if(webURL.indexOf("?")==-1) webURL += "?";
			else webURL += "&";
			webURL += "key="+RId;
		}
		return webURL;
	},

	/**
	 * webURL
	 * @param webUrl : 이동할 URL 정보
	 * @param popupYN : 팝업 여부
	 * @param popup title : 팝업 타이틀
	 * @param popup value : 팝업 속성
	 */
	URLForwardMove : function(webURL, popupYN, title, value){
		var forwardURL = LUser.URLForward(webURL);
		if(popupYN){
			if(value){
				if(!title) title= "메가박스 이벤트";
				window.open(forwardURL,title,value);
			}else{
				window.open(forwardURL);
			}
		}else{
			window.location.href = forwardURL;
		}
	}
}

//연락처
window.isMobile = function(value, title){

	// 정규식 -휴대전화번호 유효성 검사
	var regExp = /^(01[016789]{1}|02|0[3-9]{1}[0-9]{1})-?[0-9]{3,4}-?[0-9]{4}$/;
  if( !value ){
  		messageBox('휴대폰번호를 입력해주세요.');
      return false;
  } else {
      if(!regExp.test(value)) {
      	messageBox('휴대폰번호를 확인 해 주세요.');
          return false;
      }
  }
		return true;
};


function beforeDate (){

	var aDate = new Date();
	var month = new DateUtil().format(aDate.getMonth()+1);

	var beforeMonth = new DateUtil().format(aDate.getMonth());
	var beforeDay = new DateUtil().format(aDate.getDate()-1);

	//alert("month" + month);
	//alert("beforeMonth: " + Number(beforeMonth));
	//alert("beforeDay" + beforeDay);

	 if(beforeDay == '00') {
		 month = new DateUtil().format(aDate.getMonth());

		switch ( Number(beforeMonth)) {
		case 4:case 6:case 9:case 11:
			beforeDay = '30';
			break;
		case 2:
			if ( ( year % 4 == 0 && year % 100 != 0 ) || year % 400 == 0 )
				beforeDay = '29';
			else
				beforeDay = '28';
			break;
		default:
			beforeDay = '31';
		}
	 }

	 return aDate.getFullYear() + "." + month + "." + beforeDay;
}

/*
 * 구글 애널리스틱 호출 관련 스크립트
 */
var megaboxLog = {
	// 운영
	GA_ID : "UA-30006739-1",
	// 테스트
	// GA_ID : "UA-120050647-1",
	moveArry : [],
	init : function(gubun, category, name){
		// GA 호출
		(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
			(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
			m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
			})(window,document,'script','//www.google-analytics.com/analytics.js','ga');
			ga('create', this.GA_ID, 'auto');
			ga('send', 'pageview');
			
			if (gubun == "event"){
				ga('send', 'event', category, name);
			}
			
	},

	openPopup: function(moveURL, moveTitle){
		if(moveTitle) document.title = moveTitle;
		megaboxLog.browserEditURL(moveURL);
		megaboxLog.moveArry.push({ moveTitle: moveTitle, moveURL: moveURL});
		ga('send', 'pageview', (location.origin+moveURL));
	},

	closePopup: function(){
		var title = URLTitle;
		var search = URLSearch;
		if(megaboxLog.moveArry.length > 1){
			title = megaboxLog.moveArry[megaboxLog.moveArry.length-2].moveTitle;
			search = megaboxLog.moveArry[megaboxLog.moveArry.length-2].moveURL;
		}
		if(megaboxLog.moveArry.length > 0){
			megaboxLog.moveArry.pop();
		}
		document.title = title;
		megaboxLog.browserEditURL(search);
		ga('send', 'pageview', (location.origin+search));
	},

	browserEditURL : function(moveURL){
		var trident = navigator.userAgent.match(/Trident\/(\d.\d)/i);
		if(trident != null){
			var tridentNum = parseInt(trident[1]);
			if(tridentNum >= 6){
				history.replaceState({}, "", moveURL);
			}
		}else{
			history.replaceState({}, "", moveURL);
		}
	}
}