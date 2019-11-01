/**
 * @Class Name :
 * @Description :
 * @Modification Information
 *
 * 수정일         수정자      수정내용
 * ----------     --------    ---------------------------
 *
 * Copyright (C) 2009 by MOPAS  All right reserved.
 */
var _requestBooking = new JSPRequest('/pages/booking/Booking_Main.jsp', 'div#booking', 'div#booking');
var _navigation = new NavigationController(_requestBooking);

var Booking = {

	loginFlag: 'N',	// 예매 도중 로그인을 했는지 여부

	showPage: function() {
		closeMessageBox('storeSummaryView', 'custom');
		try {

			if(lowBrowserVer){
				open_pop('.pop_update');//ie updat
			}
			_requestBooking.request();

			// 화면높이가 768이하일 경우 예매창 위치 조정
			if ($(window).height() < 768) {
				$('#reservation .wrapper').css('padding-top', '50px');
			}
			// 상세 레이어 표시할때 타이틀 교체를 위한 이벤트 바인딩
			// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var moveTitle = '빠른예매(시간표)' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?show=booking&p=step1";
			megaboxLog.openPopup(moveURL, moveTitle);
		} catch(e) {
			messageBox(e);
		}
	},

	showPage2: function() {
		closeMessageBox('storeSummaryView', 'custom');
		try {

			_requestBooking.request();
			$('div#reservation').modal('show');

		} catch(e) {
			messageBox(e);
		}
	},

	showPageSelectedMovie: function(movieCode) {

		//this.getMovieShowingCinemas(movieCode);

		closeMessageBox('storeSummaryView', 'custom');

		try {
			_requestBooking.request({
				movieCode: movieCode,
			});
			$('div#reservation').modal('show');

		} catch(e) {
			messageBox(e);
		}
	},

	// 선호 영화관 표시 제어를 위한 파라미터 추가
	showPage3: function(isShowFavCinema) {
		closeMessageBox('storeSummaryView', 'custom');
		try {
			_requestBooking.request({
				isShowFavCinema: (isShowFavCinema=='') ? "Y" : isShowFavCinema,
			});
			$('div#reservation').modal('show');

		} catch(e) {
			messageBox(e);
		}
	},

	// 선호 영화관 표시 제어를 위한 파라미터 추가
	showPageSelectedMovie2: function(movieCode,isShowFavCinema) {
		closeMessageBox('storeSummaryView', 'custom');
		try {
			_requestBooking.request({
				movieCode: movieCode,
				isShowFavCinema: (isShowFavCinema=='') ? "Y" : isShowFavCinema,
			});
			$('div#reservation').modal('show');

		} catch(e) {
			messageBox(e);
		}
	},


	showPageSelectedMovieCinema: function(movieCode, areaCode, cinemaCode, rtnDate) {
//	showPageSelectedMovieCinema: function(movieCode, areaCode, cinemaCode, rtnDate, cinemaAutoSettingYn) {

		//this.getMovieShowingCinemas(movieCode);

		closeMessageBox('storeSummaryView', 'custom');

		try {
			_requestBooking.request({
				movieCode: movieCode,
				areaCode: areaCode,
				cinemaCode: cinemaCode,
				rtnDate: rtnDate,
				siteCode: '36',
//				cinemaAutoSettingYn : cinemaAutoSettingYn
			});
			$('div#reservation').modal('show');

		} catch(e) {
			messageBox(e);
		}
	},

	showPageSelectedCinema: function(cinemaCode) {

		//this.getMovieShowingCinemas(movieCode);
		closeMessageBox('storeSummaryView', 'custom');

		try {
			_requestBooking.request({
				cinemaCode: cinemaCode,
				siteCode: '36'
			});
			$('div#reservation').modal('show');

		} catch(e) {
//			messageBox(e);
			layerPopup.alertMsg({message:e});
		}
	},

	//영화관 다중 선택 2015.06.25 kch
	showPageSelectedMovieCinemas: function(selectedInfo) {

		try {
			selectedInfo.siteCode = '36';
			_requestBooking.request(selectedInfo);
			$('div#reservation').modal('show');

		} catch(e) {
			messageBox(e);
		}
	},

	// 예매가능일자
	getScreeningDates: function() {
		var screeningDates = [];
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {_command: 'Booking.getScreeningDates', siteCode: '36'}
		}).success(function( data ) {
			var result = data.result;
			for (var i=0; i < result.length; i++) {
				screeningDates.push(result[i].playDate);
			}
		});
		return screeningDates;
	},

	close: function() {
		if (this.loginFlag === 'Y') {
			location.href = '/';
		}

		// 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
		megaboxLog.closePopup();

		// 모빌리언스 팝업창을 닫는다
		if($('#payAuthMobFrame').length>0)
			$('#payAuthMobFrame')[0].contentWindow.postMessage("Close Mobil Popup.", 'https://' + hostname);
	},

	// 영화가 상영되는 영화관 날짜
	getMovieShowingCinemas: function(movieCode) {
		var jspRequest = new JSPRequest('/pages/booking/Booking_ShowingCinema_Popup.jsp', 'div#movieShowingCinemasPopup', 'div#movieShowingCinemasPopup');

		try {
			jspRequest.request({
				movieCode: movieCode
			});
		} catch (e) {
			messageBox(e);
		}
	}
};

// 빠른예매 버튼 클릭시에 브라우저 업데이트 팝업 관련 함수
function getCookie( name )
{
	var nameOfCookie = name + "=";
	var x = 0;
	while ( x <= document.cookie.length )
	{
		var y = (x+nameOfCookie.length);
		if ( document.cookie.substring( x, y ) == nameOfCookie ) {
			if ( (endOfCookie=document.cookie.indexOf( ";", y )) == -1 )
				endOfCookie = document.cookie.length;
			return unescape( document.cookie.substring( y, endOfCookie ) );
		}
		x = document.cookie.indexOf( " ", x ) + 1;
		if ( x == 0 )
			break;
	}
	return "";
}

function setCook( name, value, expiredays ){
	var today = new Date();
	var todayDate = new Date();
	todayDate.setDate( todayDate.getDate() + expiredays );
	document.cookie = name + "=" + escape( value ) + "; path=/; expires=" + todayDate.toGMTString() + ";"
}

//ie updata 추가합니다.
function open_pop(idx){
	var win_h = $(window).height();//win_height

	if( $(idx).hasClass("open_pop") ){
		//한달동안 안보기
		if($("#updata_ck").prop("checked")){
			setCook("pop_update", "done" , 30);
		}

		$(idx).hide().removeClass("open_pop");
		$("body").removeClass("body_ovh");
	}else{
		//setCook("pop_update", "done" , 30);
		$(".pop_update .icheckbox_minimal").removeClass("checked");
		$("#updata_ck").prop("checked", false);
		//브라우저업데이트 팝업 한달동안 안보기 체크
		if(getCookie("pop_update") != "done"){

			$(idx).show().addClass("open_pop");
			var pop_h = $(idx).find(".pop_con").height();//pop_con height
			var css_mr_top = (win_h-pop_h)/2;
			if( css_mr_top < 0 ) css_mr_top = 10;

			$(idx).css("visibility","visible").find(".pop_con").css("marginTop",css_mr_top+"px");
			$("body").addClass("body_ovh");
		}
	}
}