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
var _requestBookingSeatSpecial = new JSPRequest('pages/booking/step2_old/Booking_SelectSeat_Special.jsp', 'div#bookingSeat', 'div#bookingSeat');
var _requestBookingSeatTicket = new JSPRequest('pages/booking/step2_old/Booking_SelectSeat_Ticket.jsp', 'div#bookingSelectSeatTicket', 'div#bookingSelectSeatTicket');
var _requestBookingSeatSeatType = new JSPRequest('pages/booking/step2_old/Booking_SelectSeat_SeatType.jsp', 'div#bookingSelectSeatSeatType', 'div#bookingSelectSeatSeatType');

/****************************
 * 좌석선택화면
 ****************************/
var browser = navigator.userAgent.toLowerCase();
var BookingSeat = {

	movieCode: null,
	showMovieCode: null,
	cinemaCode: null,
	screenCode: null,
	screenName: null,
	playDate: null,
	startTime: null,
	showSeq: null,
	vipGradeCode: null,
	transNo: null,
	nonmemberInfo: {},		// 비회원정보
	tmpSelectedSeats: [],	// 좌석(마우스오버된) 임시저장 배열
	seatListData: [],		// 좌석목록정보
	//selectedSeatCount: 0,	// 현재 선택된 좌석수
	selectedSeats: [], 		// 현재 선택된 좌석정보
	seatPaxCount: 1,		// 좌석붙임설정 갯수
	exitListData:[],
	tempSeatLineCntCnt:0,
	dbClickBlock:false,		// 더블클릭 방지
	checkSeatFlag: false,

	// 초기화
	init: function(code, showMovieCode, cinemaCode, screenCode, screenName, playDate, startTime, showSeq, vipGradeCode) {
		this.movieCode  = code;
		this.showMovieCode  = showMovieCode;
		this.cinemaCode = cinemaCode;
		this.screenCode = screenCode;
		this.screenName = screenName;
		this.playDate   = playDate;
		this.startTime	= startTime;
		this.showSeq    = showSeq;
		this.vipGradeCode = vipGradeCode;
		this.seatListData = [];
		this.selectedSeats = [];
		this.tmpSelectedSeats = [];
		this.seatPaxCount = 1;
		this.exitListData = [];
		this.checkSeatFlag = false;

		// 좌석정보를 가져온다.
		this.seatListData = this.getSeatListData(this.cinemaCode, this.screenCode, this.playDate, this.showSeq);

		// 출구정보정보를 가져온다.
		this.getExitList(this.cinemaCode, this.screenCode);

		// 좌석선택-좌석종류화면 호출
		BookingSeatType.showPage(this.cinemaCode, this.screenCode);

		// 좌석선택-티켓종류화면 호출
		BookingSelectSeatTicket.showPage(this.cinemaCode, this.screenCode, this.playDate, this.showSeq, this.vipGradeCode, this.showMovieCode);

		// 좌석선택-예약현황화면 호출
		BookingSeatStatusBoard.showPage(this.movieCode, this.showMovieCode, this.cinemaCode, this.screenName, this.playDate, this.startTime);

		// 좌석그리기
		this.draw();

		// 예매관련 팝업
		//this.checkCinemaPopup(this.cinemaCode);
		this.checkMoviePopup(this.showMovieCode, this.cinemaCode);
		this.checkRefundPopup(this.showMovieCode);
		this.checkScreenPopup(this.cinemaCode, this.screenCode);
		this.checkSequencePopup(this.cinemaCode, this.screenCode, this.playDate, this.showSeq);

		BookingTicket.init(cinemaCode, screenCode, playDate, showSeq, vipGradeCode);
	},
	checkRefundPopup: function(showMovieCode) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkRefundPopup',
				showMovieCode: showMovieCode,
				siteCode: '36'
			}
		}).success(function( data ) {

			if (data.resultCode === '0000') {
				var result = data.result;
				// 환불 수수료(Y), 환불불가(W) 팝업
				if (result != null && $.trim(result.popupMsg) !== '' && ($.trim(result.refundFee_popYn) =='Y' || $.trim(result.refundFee_popYn) =='W')) {
					if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"/>');
					else
						messageBox( result.popupMsg);
				}
			} else {
				messageBox(data.resultMessage);
			}
		});
	},

	// 남은 좌석여부 확인
	checkSeatRemains: function(cinemaCode, screenCode, playDate, showSeq, showMovieCode, startTime) {

		var isRemains = false;

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {
				_command: 'Booking.checkSeatRemains',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				showMovieCode: showMovieCode,
				startTime: startTime,
				siteCode: '36'
			}
		}).success(function(data) {
			if (data.resultCode === '0000') {
				isRemains = true;

			/*
			 * 2014.12.18
			 * 응답코드(0000: 정상진행 가능, 3333:팝업, 이외 오류)
			 * '3333'일 경우만 popupKind를 사용한다고 함. 박후선 이사님 확인완료
			 * */
			}else if (data.resultCode === '3333') {

				if (data.popupKind !== null) {
					if (data.popupKind === 'info') {
						messageBox(data.resultMessage);
					} else if (data.popupKind === 'yesno') {
						//confirmBox('알림', data.resultMessage, yesfn, nofn);
						if(confirm(data.resultMessage)) {
							isRemains = true;
						}
					}
				}
			} else {
				messageBox(data.resultMessage);
			}
		});

		return isRemains;
	},

	checkMoviePopup: function(movieCode, cinemaCode) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkMoviePopup',
				showMovieCode: movieCode,
				cinemaCode: cinemaCode,
				korEngGubun: '1',
				siteCode: '36'
			}
		}).success(function( data ) {
			var result = data.result;
			if (result != null && $.trim(result.popupMsg) !== '') {
				if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
					messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad" alt="청소년 관람 불가 영화입니다.*만 18세 미만의 고객님(영,유아 포함)은 보호자를 동반하여도 영화 관람이 불가합니다.*입장 시, 신분증을 반드시 지참하시기 바랍니다.*만 18세 이상이지만 초/중/고등학교 재학중인 고객님은 영화 관람이 불가합니다."/>');
				else
					messageBox( result.popupMsg);
			}
		});
	},

	checkScreenPopup: function(cinemaCode, screenCode) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkScreenPopup',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				korEngGubun: '1',
				siteCode: '36'
			}
		}).success(function( data ) {
			var result = data.result;
			if (result != null && $.trim(result.popupMsg) !== '') {
				if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
					if(result.popupMsg.indexOf("WEB_1351_12") > 0){
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="더 부티크 스위트룸 이용안내 *더 부티크스위트룸은 만 13세 이상 고객님의 이용을 권장하고 있습니다.	* 청소년 요금할인과 미취학 아동요금제는 적용되지 않습니다.(그 외 우대요금은 적용)"/>');
					}else if(result.popupMsg.indexOf("WEB_1351_13") > 0){
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="더 부티크 스위트룸 이용안내 *더 부티크스위트룸은 만 13세 이상 고객님의 이용을 권장하고 있습니다.	* 청소년 요금할인과 미취학 아동요금제는 적용되지 않습니다.(그 외 우대요금은 적용)"/>');
					}else if(result.popupMsg.indexOf("WEB_1351_16") > 0){
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="부트크M 스위트룸 이용안내 *더 부티크 스위트룸은 만 13세 이상 고객님의 이용을 권장하고 있습니다.*청소년 요금할인과 미취학 아동요금제는 적용되지 않습니다.(그 외 우대요금은 적용)"/>');
					}else if(result.popupMsg.indexOf("WEB_4431_kids") > 0){
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="지금 예매하신 상영관은 키즈관입니다.	*키즈관은 어린이에게 최적화 되어 있는 어린이/가족 전용관입니다. * 24개월 이상 티켓 구매 24개월 이상 티켓 가격은 동일합니다.(24개월 미만 영아는 의료 보험증, 등본 등 확인 후 무료 입장이 가능하나, 좌석은 제공되지 않습니다.)	*선착순 자율 좌석제 입장 순서에 따라 원하는 좌석을 선택하여 관람합니다."/>');
					}else if(result.popupMsg.indexOf("WEB_4631_08") > 0){
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt=" 더 부티크 스위트룸 분당점 이용안내 *더 부티크 스위트룸 분당점 관람시 상영관 내 미니바 서비스를 이용하실 수 있습니다. *더 부티크 스위트룸은 만 13세 이상 고객님의 이용을 권장하고 있습니다. * 청소년 요금할인과 미취학 아동요금제는 적용되지 않습니다. (그 외 우대요금은 적용)"/>');
					}else{
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"/>');
					}

				else
					messageBox( result.popupMsg);
			}
		});
	},

	checkSequencePopup: function(cinemaCode, screenCode, playDate, showSeq) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkSequencePopup',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				korEngGubun: '1',
				siteCode: '36'
			}
		}).success(function( data ) {
			var result = data.result;
			if (result != null && $.trim(result.popupMsg) !== '') {
				if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
					messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="대체텍스트입력3"/>');
				else
					messageBox( result.popupMsg);
			}
		});
	},

	// 좌석정보를 가져온다.
	getSeatListData: function(cinemaCode, screenCode, playDate, showSeq) {

		var seatList = [];

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {
				_command: 'Booking.getSeatList',
				siteCode: '36',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq
			}
		}).success(function( data ) {

			if (data.resultCode === '0000') {
				var resultList = data.list;

				var tmpSeatGroup = "";
				var tmpSeatLine = 0;
				var physicalNum = 0;	// 각 좌석라인별 넘버링

				for (var i=0; i < resultList.length; i++) {
					var seatData = resultList[i];

					if (tmpSeatGroup != seatData.seatGroup || tmpSeatLine != seatData.seatLine) {
						//if(tmpSeatGroup==seatData.seatGroup || tmpSeatLine==seatData.seatLine)
							physicalNum = 1;
						//else
						//	physicalNum = seatData.seatNo;
					}

					tmpSeatGroup = seatData.seatGroup;
					tmpSeatLine  = seatData.seatLine;
					seatData.physicalNum = physicalNum;
					seatData.isSelected  = false;
					seatList.push(seatData);
					physicalNum++;
				}
			}
		});

		return seatList;
	},

	// 출구정보를 가져온다.
	getExitList: function(cinemaCode, screenCode) {

		var exitList = [];

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {
				_command: 'Booking.getExitList',
				siteCode: '36',
				cinemaCode: cinemaCode,
				screenCode: screenCode
			}
		}).success(function( result ) {
		/*
			2015.03.31
			출구데이터가 정상적으로 내려오지 않으므로
			일단 주석처리로 출구 표시 안함.
			오윤호D, 최성호T 협의 완료
		*/
			BookingSeat.exitList = result.list;
			BookingSeat.exitListData = result.list;
		});
	},

	// 좌석선택화면
	showPage: function() {
		try {
			_requestBookingSeat.request();
			// 1.상세 레이어 표시할때 타이틀 교체를 위한 이벤트 바인딩
			// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var moveTitle = '빠른예매(좌석)' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?show=booking&p=step2";
			megaboxLog.openPopup(moveURL, moveTitle);
		} catch(e) {
			messageBox(e);
		}
		$('#select_seat').modal('show');
		// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
		$('#select_seat').off('hide.bs.modal').on('hide.bs.modal',function() {
			megaboxLog.closePopup();
		});
	},

	// 좌석선택
	selectSeat: function(obj, seatGroupValue, seatNoValue) {

		// 선택되어있을시 해제
//		if ($(obj).hasClass('seat_selected')) {
//			// 타입별 컬러처리
//			$(obj).removeClass();
//			$(obj).addClass('seat_normal');
//			BookingSeatStatusBoard.nondisplaySelectedSeat(seatGroup+seatNum);
//			this.selectedSeatCount--;
//		} else {

		if (BookingSelectSeatTicket.getTotalCountSelectedTicket() == 0) {
			messageBox('인원을 먼저 선택해주세요.');
			return;
		}

		// 현재 선택가능한 좌석수에 따라 좌석붙임설정 활성화 처리 Start
		var selectedSeatCount = this.getSelectedSeatCount(); // 현재 선택한 좌석수
		var totalCountSelectedTicket = BookingSelectSeatTicket.getTotalCountSelectedTicket();	// 선택한 전체티켓수
		var selectableSeatCount = totalCountSelectedTicket - (selectedSeatCount + this.seatPaxCount);	// 선택 가능한 좌석수
		if (selectableSeatCount >= 4) {
			activeSeatPaxCount(4);
		} else {
			if(obj.getAttribute('seattype') == '40'){
				activeSeatPaxCount(totalCountSelectedTicket - (selectedSeatCount + $("button.seat_selected[seatType='40'][title*='장애인석/휠체어석']").length));
			}else{
				activeSeatPaxCount(selectableSeatCount);
			}

		}
		// 현재 선택가능한 좌석수에 따라 좌석붙임설정 활성화 처리 End

		if ((selectedSeatCount+this.seatPaxCount) >= totalCountSelectedTicket) {
			if (totalCountSelectedTicket == selectedSeatCount) {
				messageBox('좌석 선택이 완료되었습니다.');
				return;
			} else {
				var remainingSeat = totalCountSelectedTicket - selectedSeatCount;
				messageBox('선택가능한 좌석수가 초과되었습니다.\n현재 선택 가능한 좌석수는 ' + remainingSeat + '석 입니다.');
				return;
			}
		}

		// 장애인석/휠체어석이 선택되어 있는지 확인
		var seatType = obj.getAttribute("seatType");
		if(seatType!='40' && $("button.seat_selected[seatType='40']").length == 0 ){
			BookingSeat.checkSeatFlag=false;
		}else if(seatType=='40'){
			// 장애인석/휠체어석인 경우
			var result = BookingSeat.seatPopupMsg;
			BookingSeat.seatPopupMsg = null;
			if (result != null && $.trim(result.popupMsg) !== '') {
				if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
				{
					messageBox( '<img src="'+result.popupMsg+'" />');
//					layerPopup.alertMsg({message:'<img src="'+result.popupMsg+'" class="layerPopupImgLoad" />'});
				}
				else
				{
					messageBox( result.popupMsg);
//					layerPopup.alertMsg({message:result.popupMsg});
				}
			}
		}

		var group = new Date().getTime();
		var selectedSeatCount = this.getSelectedSeatCount();// 현재 좌석선택한 티켓수
		var selectSeatInfo=[];
		for (var i=0; i < this.tmpSelectedSeats.length; i++) {

			var tmpSeatId = this.tmpSelectedSeats[i].id;
			var seatArr = tmpSeatId.split('_');	// seatArr (1:seatGroup, 2:seatNo, 3:seatLineSort, 4:index)
			var index = seatArr[4];
			var seatGroup = this.seatListData[index].seatGroup;
			var seatNo = this.seatListData[index].seatNo;
			var seatAllocType = this.seatListData[index].seatAllocType;
			var popupYn = this.seatListData[index].popupYn;

			this.seatListData[index].isSelected = true;

			var seatInfo = {};
			seatInfo.group = group;
			seatInfo.seatIndex = index;
			seatInfo.seatGroup = seatGroup;
			seatInfo.seatNo = seatNo;
			seatInfo.popupYn = popupYn;
			seatInfo.seatType = seatAllocType;
//			seatInfo.ticketCode = BookingSelectSeatTicket.selectedTickets[i].code;
//			seatInfo.amt = BookingSelectSeatTicket.selectedTickets[i].amt;
			selectSeatInfo.push(seatInfo);
			this.selectedSeats.push(seatInfo); // 선택좌석정보에 추가
		}

		// 예매현황에 선택된 좌석 표시
		BookingSeatStatusBoard.resetDisplaySelectedSeat();	// 좌석표시 초기화

		// 예매현황판에 보여줄 좌석번호 정렬
		// 좌석그룹으로 1차정렬후 좌석번호로 2차정렬
		this.selectedSeats.sort(function(a, b) {
			var o1 = a.seatGroup;
			var o2 = b.seatGroup;
			var p1 = parseInt(a.seatNo);
			var p2 = parseInt(b.seatNo);

			if (o1 != o2) {
				if (o1 > o2) return 1;
				if (o1 < o2) return -1;
			    return 0;
			}

			if (p1 > p2) return 1;
			if (p1 < p2) return -1;
			return 0;
		});

		if(seatType=='40'){
			var $selectedWheel = $("button.seat_selected[seatType='40'][title*='선택됨']");
			var seatGroup, seatNo;
			var selected=[];
			$.each(this.selectedSeats, function(i, data){
				if(i == 0){
					selected.push(data);
				}else if(seatGroup!=data.seatGroup || seatNo!=data.seatNo){
					selected.push(data);
				}
				seatGroup = data.seatGroup;
				seatNo = data.seatNo;
			});
			this.selectedSeats = selected;
			for (var i=0; i < selected.length; i++) {
				BookingSeatStatusBoard.displaySelectedSeat(selected[i].seatGroup + selected[i].seatNo);
			}
		}else{
			for (var i=0; i < this.selectedSeats.length; i++) {
				BookingSeatStatusBoard.displaySelectedSeat(this.selectedSeats[i].seatGroup + this.selectedSeats[i].seatNo);
			}
		}


		BookingSeatStatusBoard.displayTotalTicketPrice(BookingSelectSeatTicket.getTotalPrice());	// 예매현황 가격표시
		$('span#currentCountSelectedTicket').text(this.getSelectedSeatCount()); // 현재 좌석선택한 티켓수 표시

		this.tmpSelectedSeats = []; // 저장된 임시 seatId 초기화

		for(var i=0;i<selectSeatInfo.length;i++) {
			if(selectSeatInfo[i].popupYn=="Y") {
				this.checkSeatPopup(this.cinemaCode, this.screenCode, this.playDate, this.showSeq, selectSeatInfo);
				break;
			}
		}

		this.draw();
	},

	// 좌석선택
	selectSeatKey: function(obj, seatGroupValue, seatNoValue) {
		// 선택되어있을시 해제
//		if ($(obj).hasClass('seat_selected')) {
//			// 타입별 컬러처리
//			$(obj).removeClass();
//			$(obj).addClass('seat_normal');
//
//			BookingSeatStatusBoard.nondisplaySelectedSeat(seatGroup+seatNum);
//
//			this.selectedSeatCount--;
//
//		} else {

		if (BookingSelectSeatTicket.getTotalCountSelectedTicket() == 0) {
			messageBox('인원을 먼저 선택해주세요.');
			return;
		}

		// 현재 선택가능한 좌석수에 따라 좌석붙임설정 활성화 처리 Start
		var selectedSeatCount = this.getSelectedSeatCount(); // 현재 선택한 좌석수
		var totalCountSelectedTicket = BookingSelectSeatTicket.getTotalCountSelectedTicket();	// 선택한 전체티켓수
		var selectableSeatCount = totalCountSelectedTicket - (selectedSeatCount + this.seatPaxCount);	// 선택 가능한 좌석수
		if (selectableSeatCount >= 4) {
			activeSeatPaxCount(4);
		} else {
			activeSeatPaxCount(selectableSeatCount);
		}
		// 현재 선택가능한 좌석수에 따라 좌석붙임설정 활성화 처리 End

		if ((selectedSeatCount+this.seatPaxCount) >= totalCountSelectedTicket) {
			if (totalCountSelectedTicket == selectedSeatCount) {
				messageBox('좌석 선택이 완료되었습니다.');
				return;
			} else {
				var remainingSeat = totalCountSelectedTicket - selectedSeatCount;
				messageBox('선택가능한 좌석수가 초과되었습니다.\n현재 선택 가능한 좌석수는 ' + remainingSeat + '석 입니다.');
				return;
			}
		}

		var group = new Date().getTime();
		var selectedSeatCount = this.getSelectedSeatCount();// 현재 좌석선택한 티켓수
		var selectSeatInfo=[];

		for (var i=0; i < this.tmpSelectedSeats.length; i++) {

			var tmpSeatId = this.tmpSelectedSeats[i].id;
			var seatArr = tmpSeatId.split('_');	// seatArr (1:seatGroup, 2:seatNo, 3:seatLineSort, 4:index)
			var index = seatArr[4];
			var seatGroup = this.seatListData[index].seatGroup;
			var seatNo = this.seatListData[index].seatNo;
			var seatAllocType = this.seatListData[index].seatAllocType;
			var popupYn = this.seatListData[index].popupYn;

			this.seatListData[index].isSelected = true;

			var seatInfo = {};
			seatInfo.group = group;
			seatInfo.seatIndex = index;
			seatInfo.seatGroup = seatGroup;
			seatInfo.seatNo = seatNo;
			seatInfo.popupYn = popupYn;
			seatInfo.seatType = seatAllocType;
//			seatInfo.ticketCode = BookingSelectSeatTicket.selectedTickets[i].code;
//			seatInfo.amt = BookingSelectSeatTicket.selectedTickets[i].amt;
			selectSeatInfo.push(seatInfo);

			this.selectedSeats.push(seatInfo); // 선택좌석정보에 추가
		}

		// 예매현황에 선택된 좌석 표시
		BookingSeatStatusBoard.resetDisplaySelectedSeat();	// 좌석표시 초기화

		// 예매현황판에 보여줄 좌석번호 정렬
		// 좌석그룹으로 1차정렬후 좌석번호로 2차정렬
		this.selectedSeats.sort(function(a, b) {
			var o1 = a.seatGroup;
			var o2 = b.seatGroup;
			var p1 = parseInt(a.seatNo);
			var p2 = parseInt(b.seatNo);

			if (o1 != o2) {
				if (o1 > o2) return 1;
				if (o1 < o2) return -1;
			    return 0;
			}

			if (p1 > p2) return 1;
			if (p1 < p2) return -1;
			return 0;
		});

		for (var i=0; i < this.selectedSeats.length; i++) {
			BookingSeatStatusBoard.displaySelectedSeat(this.selectedSeats[i].seatGroup + this.selectedSeats[i].seatNo);
		}

		BookingSeatStatusBoard.displayTotalTicketPrice(BookingSelectSeatTicket.getTotalPrice());	// 예매현황 가격표시
		$('span#currentCountSelectedTicket').text(this.getSelectedSeatCount()); // 현재 좌석선택한 티켓수 표시

		this.tmpSelectedSeats = []; // 저장된 임시 seatId 초기화

		for(var i=0;i<selectSeatInfo.length;i++) {
			if(selectSeatInfo[i].popupYn=="Y") {
				this.checkSeatPopup(this.cinemaCode, this.screenCode, this.playDate, this.showSeq, selectSeatInfo);
				break;
			}
		}

		this.draw();
	},

	checkSeatPopup:function(cinemaCode, screenCode, playDate, showSeq, selectSeatInfo){

		var seatNo1='',seatNo2='',seatNo3='',seatNo4='',seatNo5='',seatNo6='',seatNo7='',seatNo8='';
		for(var i=0;i<selectSeatInfo.length;i++) {
			if(i==0) seatNo1=selectSeatInfo[i].seatNo;
			if(i==1) seatNo2=selectSeatInfo[i].seatNo;
			if(i==2) seatNo3=selectSeatInfo[i].seatNo;
			if(i==3) seatNo4=selectSeatInfo[i].seatNo;
			if(i==4) seatNo5=selectSeatInfo[i].seatNo;
			if(i==5) seatNo6=selectSeatInfo[i].seatNo;
			if(i==6) seatNo7=selectSeatInfo[i].seatNo;
			if(i==7) seatNo8=selectSeatInfo[i].seatNo;
		}

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkSeatPopup',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				seatType: selectSeatInfo[0].seatType,
				seatGroup: selectSeatInfo[0].seatGroup,
				seatNo1: seatNo1,
				seatNo2: seatNo2,
				seatNo3: seatNo3,
				seatNo4: seatNo4,
				seatNo5: seatNo5,
				seatNo6: seatNo6,
				seatNo7: seatNo7,
				seatNo8: seatNo8,
				korEngGubun: '1',
				siteCode: '36'
			}
		}).success(function( data ) {
			var result = data.result;
			if (result != null && $.trim(result.popupMsg) !== '') {
				if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
					messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="대체텍스트입력4"/>');
				else
					messageBox( result.popupMsg);
			}
		});
	},

	checkSeatPopup2:function(cinemaCode, screenCode, playDate, showSeq, seatType,seatGroup, seatNo1){
		BookingSeat.checkSeatPopup3(cinemaCode, screenCode, playDate, showSeq, seatType,seatGroup, seatNo1, null);
	},

	checkSeatPopup3:function(cinemaCode, screenCode, playDate, showSeq, seatType,seatGroup, seatNo1, obj){

		var selectedSeatCount = this.getSelectedSeatCount();
		var totalCountSelectedTicket = BookingSelectSeatTicket.getTotalCountSelectedTicket();

		// 현재 클릭된 좌석
		var seatObj = $('[id^=seat_' + seatGroup +'_' + seatNo1 + ']');

		if (totalCountSelectedTicket == 0) {
			messageBox('인원을 먼저 선택해주세요.');
			return;
		}

		// 선택불가 좌석일 경우 2015.03.02 > 2015.11.10
		if(obj == null || obj.getAttribute('class') == 'seat_cant'){
			messageBox('옆좌석을 선택해주세요.');
			return;
		}

		// 장애인석/휠체어석은 Booking.checkSeatPopup 리턴값 표시
		if(seatType != '40'){
			if ((selectedSeatCount+this.seatPaxCount) >= totalCountSelectedTicket) {

				if (totalCountSelectedTicket == selectedSeatCount) {
					messageBox('좌석 선택이 완료되었습니다.');
					return;
				}

				if (totalCountSelectedTicket > selectedSeatCount) {
					var remainingSeat = totalCountSelectedTicket - selectedSeatCount;
					messageBox('선택가능한 좌석수가 초과되었습니다.\n현재 선택 가능한 좌석수는 ' + remainingSeat + '석 입니다.');
					return;
				}

			}
		}

		// 장애인석/휠체어석이 선택되어 있는지 확인
		var seatType = seatObj.attr("seatType");
		if(seatType=='40' && BookingSeat.checkSeatFlag){
				BookingSeat.selectSeat(obj, seatGroup, seatNo1);
				return;
		}
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkSeatPopup',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				seatType: seatType,
				seatGroup: seatGroup,
				seatNo1: seatNo1,
				seatNo2: '',
				seatNo3: '',
				seatNo4: '',
				seatNo5: '',
				seatNo6: '',
				seatNo7: '',
				seatNo8: '',
				korEngGubun: '1',
				siteCode: '36'
			}
		}).success(function( data ) {
			var result = data.result;
			if(seatType == '40'){
				BookingSeat.checkSeatFlag=true;
				BookingSeat.seatPopupMsg=result;
				BookingSeat.selectSeat(obj, seatGroup, seatNo1);
			}else{
				if (result != null && $.trim(result.popupMsg) !== '') {
					if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="대체텍스트입력5"/>');
					else
						messageBox( result.popupMsg);
				}
			}
		});
	},

	// 좌석선택해제
	deselectSeat: function(seatGroup, seatNo) {
		var group = null;

		for (var i=0; i < this.selectedSeats.length; i++) {
			if (this.selectedSeats[i].seatGroup === seatGroup && this.selectedSeats[i].seatNo === seatNo) {
				group = this.selectedSeats[i].group;
				break;
			}
		}

		for (var i=(this.selectedSeats.length-1); i >= 0; i--) {
			if (this.selectedSeats[i].group === group) {
				var selectedSeatGroup = this.selectedSeats[i].seatGroup;
				var selectedSeatNo = this.selectedSeats[i].seatNo;
				this.seatListData[this.selectedSeats[i].seatIndex].isSelected = false;
				BookingSeatStatusBoard.nondisplaySelectedSeat(selectedSeatGroup + selectedSeatNo); // 예매현황에 선택된 좌석 표시
				this.selectedSeats.splice(i, 1); // 선택된좌석 배열에서 제거
			}
		}

		//var totalPrice = this.getTotalPrice();

		//BookingSeatStatusBoard.displayTotalTicketPrice(totalPrice);	// 예매현황 가격표시
		$('span#currentCountSelectedTicket').text(this.getSelectedSeatCount());	// 현재 좌석선택한 티켓수 표시

		// 현재 선택가능한 좌석수에 따라 좌석붙임설정 활성화 처리 Start
		var selectedSeatCount = this.getSelectedSeatCount(); // 현재 선택한 좌석수
		var totalCountSelectedTicket = BookingSelectSeatTicket.getTotalCountSelectedTicket();	// 선택한 전체티켓수
//		var selectableSeatCount = totalCountSelectedTicket - (selectedSeatCount + this.seatPaxCount);	// 선택 가능한 좌석수
		var selectableSeatCount = totalCountSelectedTicket - selectedSeatCount;	// 선택 가능한 좌석수

		if (selectableSeatCount >= 4) {
			activeSeatPaxCount(4);
		} else {
			activeSeatPaxCount(selectableSeatCount);
		}
		// 현재 선택가능한 좌석수에 따라 좌석붙임설정 활성화 처리 End

		this.draw();
	},

	// 좌석그리기
	draw: function() {

		var SEAT_WIDTH = 16;	// 좌석 width
		var SEAT_HEIGHT = 16;	// 좌석 height
		var SEAT_GROUP_WIDTH = 20;	// 좌석그룹명 width
		var OFFSET_X_PLUS = 23;

		// 좌석화면 가운데정렬 처리를 위한 변수
		var largestSeatPosX = 0;
		var largestSeatPosY = 0;
		var seatWrapWidth  = 0;
		var seatWrapHeight = 0;
		var seatListData = this.seatListData;	// 좌석데이터
		var exitListData = this.exitListData;	// 출입구데이터

		this.tempSeatLineCntCnt=0;

		var htmlTxt = "";

		// 출구처리
		for (var i=0; i < exitListData.length; i++) {
			var exitPosX = (parseInt(exitListData[i].posX) / 10);
			if (exitPosX <= 0) {
				exitPosX = exitPosX - 15;
			} else {
				exitPosX = exitPosX + 25;
			}
			var exitPosY = (parseInt(exitListData[i].posY) / 10);

			// direction: L(레프트), T(탑), R(라이트), B(바텀) ,LR(입출구겸용-좌우), TB(입출구겸용-상하)
			if (exitListData[i].direction === 'L')
				htmlTxt += "<span class=\"exit left\" style=\"top:" + exitPosY + "px; left: " + exitPosX + "px;\">출입구</span>";
			if (exitListData[i].direction === 'R')
				htmlTxt += "<span class=\"exit right\" style=\"top:" + exitPosY + "px; left: " + exitPosX + "px;\">출입구</span>";
			if (exitListData[i].direction === 'T')
				htmlTxt += "<span class=\"exit top\" style=\"top:" + exitPosY + "px; left: " + exitPosX + "px;\">출입구</span>";
			if (exitListData[i].direction === 'B')
				htmlTxt += "<span class=\"exit bottom\" style=\"top:" + exitPosY + "px; left: " + exitPosX + "px;\">출입구</span>";
			if (exitListData[i].direction === 'LR')
				htmlTxt += "<span class=\"exit left_right\" style=\"top:" + exitPosY + "px; left: " + exitPosX + "px;\">출입구</span>";
			if (exitListData[i].direction === 'TB')
				htmlTxt += "<span class=\"exit top_bottom\" style=\"top:" + exitPosY + "px; left: " + exitPosX + "px;\">출입구</span>";
		}

		var tmpSeatGroup="";
		var seatPosX=0,seatPosY=0;
		for (var i=0; i < seatListData.length;) {
			//var row = seatListData[i];
			var seatCount=(Number(seatListData[i].seatLineCnt)+i);

			var seatIdx=1;
			var maxCnt=0;
			for (var j=i; j < seatCount; j++) {

				seatPosX   = parseInt(seatListData[j].seatPosX) / 10;
				seatPosY   = parseInt(seatListData[j].seatPosY) / 10;

				if (largestSeatPosX < seatPosX) {
					largestSeatPosX = seatPosX;
				}

				if (largestSeatPosY < seatPosY) {
					largestSeatPosY = seatPosY;
				}

				var seatNo = Number(seatListData[j].seatNo);
				var seatAlloc = Number(seatListData[j].seatAlloc);
				var seatStatus = seatListData[j].seatStatus;
				var seatLineSort = Number(seatListData[j].seatLineSort);
				var seatGroup = seatListData[j].seatGroup;
				var seatLineCnt = Number(seatListData[j].seatLineCnt);
				var seatGroupCnt = Number(seatListData[j].seatGroupCnt);
				var seatLine = Number(seatListData[j].seatLine);

				// 좌석그룹이 변경됐을 경우
				if ('' == tmpSeatGroup || tmpSeatGroup != seatGroup) {
					tmpSeatGroup = seatGroup;
					htmlTxt += "<span class=\"line line_" + seatGroup.toLowerCase() + "\" style=\"left:0px; top: " + seatPosY + "px;\">" + seatGroup + "</span>";
				}

				var isSelectable=false;

				if (seatLineSort == 0)
				{
					if((seatIdx % 2)!=0)
					{
						isSelectable=true;
					}
				}
				else
				{
					if((seatLineCnt%2)==0)
					{
						if((seatIdx % 2)==0 || (seatIdx % seatLineCnt) == 0)
						{
							isSelectable=true;
						}
					}
					else
					{
						if((seatIdx % 2)!=0 || (seatIdx % seatLineCnt) == 0)
						{
							isSelectable=true;
						}
					}
				}

				if(this.seatPaxCount==1)
				{
					if(i==j || (j+1)==seatCount && seatStatus=='00' && !seatListData[j].isSelected)
						isSelectable=true;

					if((j-i)==1 || (seatCount-(j+1))==1 && seatLineCnt>2)
					{
						if(seatLineSort == 0 && (j-1)>=0 && seatListData[j-1].seatStatus=='00'  && !seatListData[j-1].isSelected)
						{
							isSelectable=false;
						}
						else if(seatLineSort == 1 && (j+1)<=(seatListData.length-1) && seatListData[j+1].seatStatus=='00'  && !seatListData[j+1].isSelected)
						{
							isSelectable=false;
						}
					}

					if(seatLineCnt<=2 && seatStatus=='00')
						isSelectable=true;

					if((j+1)<=(seatListData.length-1) && ( seatListData[j+1].seatStatus!='00' || seatListData[j+1].isSelected ) && seatListData[j+1].seatAlloc==seatAlloc  && seatListData[j+1].seatLine==seatLine && seatListData[j+1].seatGroup==seatGroup)
					{
						isSelectable=true;
					}

					if((j-1)>=0 && ( seatListData[j-1].seatStatus!='00' || seatListData[j-1].isSelected ) && seatListData[j-1].seatAlloc==seatAlloc  && seatListData[j-1].seatLine==seatLine && seatListData[j-1].seatGroup==seatGroup)
					{
						isSelectable=true;
					}
				}

				if ((seatLineCnt < this.seatPaxCount) || (seatAlloc != 10 && seatAlloc != 40 && seatAlloc <= 70)) {
					isSelectable = false;
				}

				if(this.seatPaxCount>1)
				{
					if(seatListData.length>=(j+this.seatPaxCount) && seatLineSort == 0)
					{
						for(var k=(j+1);k<(j+this.seatPaxCount);k++)
						{
							if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
									|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected)
								isSelectable = false;
						}
					}
					else if(seatListData.length<(j+this.seatPaxCount) && seatLineSort == 0)
					{
						isSelectable = false;
					}

					if(0<=(j-(this.seatPaxCount-1)) && seatLineSort == 1)
					{
						for(var k=(j-1);k>(j-this.seatPaxCount);k--)
						{
							if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
									|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected)
								isSelectable = false;
						}
					}
					else if(0>(j-(this.seatPaxCount-1)) && seatLineSort == 1)
					{
						isSelectable = false;
					}

					if(seatLineSort==0)
					{
						if(seatCount==(j+1) && seatStatus=='00' && !seatListData[j].isSelected)
						{
							var check=false;
							var totCnt=j-this.seatPaxCount;
							if(totCnt>=0)
							{
								for(var k=(j-1);k>totCnt;k--)
								{
									if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
											|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected)
										check = true;
								}

								if(!check)
								{
									isSelectable = true;
									seatLineSort=1;
								}
							}
						}
					}
					else
					{
						if(j==i && seatStatus=='00' && !seatListData[j].isSelected)
						{
							var check=false;
							var totCnt=j+this.seatPaxCount;
							if(totCnt<=seatListData.length)
							{
								for(var k=(j+1);k<totCnt;k++)
								{
									if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
											|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected)
										check = true;
								}

								if(!check)
								{
									isSelectable = true;
									seatLineSort=0;
								}
							}
						}
					}
				}

				if (seatListData[j].seatAllocType != BookingSeatType.seatType) {
					isSelectable = false;
					if(BookingSeatType.seatType == '10' && seatListData[j].seatAllocType == '40') isSelectable = true;
				}

				htmlTxt+=seatHtml(seatListData[j], isSelectable, seatLineSort, j, this);

				if(maxCnt<seatIdx) maxCnt=seatIdx;

				if(seatStatus!='00' || seatListData[j].isSelected)
					seatIdx=1;
				else
					seatIdx++;
			}
			i=seatCount;

			if(this.tempSeatLineCntCnt<maxCnt)
				this.tempSeatLineCntCnt=maxCnt;
		}

		function seatHtml(data, isSelectable, seatLineSort, idx, self)
		{
			var seatHtml="";
			var seatClass = "seat_normal";	// 좌석상태이미지
			var seatId = seatIdGen(data.seatGroup, data.seatNo, seatLineSort, idx);

			var seatLengthX = (data.seatLenX >= 160) ? data.seatLenX / 10 : 16;
			var seatLengtyY = (data.seatLenY >= 160) ? data.seatLenY / 10 : 16;

			if(browser.indexOf('chrome') > -1)	{
				/*
				 * chrome 용
				 * onfocus, onblur, onkeyup(enter키만) << 키보드
				 * onmouseover, onmouseout, onclick << 마우스
				 */
				// 예매된 좌석이거나 고장난 좌석인 경우
				if (data.seatStatus != '00' || data.seatTrouble == 'Y') {
					// 선택불가(예매완료/고장)
					seatHtml +=
						"<button title=\"" + data.seatGroup + data.seatNo + "(선택불가)\" "
						+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"seat_done\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
						+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\" "
						+ " onclick=\"BookingSeat.checkSeatPopup2('"+self.cinemaCode+"','"+self.screenCode+"','"+self.playDate+"','"+self.showSeq+"','"+data.seatAllocType+"','"+data.seatGroup+"','"+data.seatNo+"')\">"
						+ data.seatNo
						+ "</button>";
				} else {

					// 선택된 좌석
					if (data.isSelected) {
						seatHtml +=
							"<button title=\"" + data.seatGroup + data.seatNo + "(선택됨)\" "
							+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"seat_selected\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
							+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\" onclick=\"BookingSeat.deselectSeat('" + data.seatGroup + "', '" + data.seatNo + "')\">" + data.seatNo
							+ "</button>";
					} else {

						// 선택되지 않은 좌석
						if (!isSelectable) {

								seatHtml += "<button title=\"" + data.seatGroup + data.seatNo + "(예매가능/선택불가)\" "
								+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"seat_cant\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
								+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\" "
								+ " onclick=\"BookingSeat.checkSeatPopup2('"+self.cinemaCode+"','"+self.screenCode+"','"+self.playDate+"','"+self.showSeq+"','"+data.seatAllocType+"','"+data.seatGroup+"','"+data.seatNo+"')\">"
								+ data.seatNo
								+ "</button>";

						} else {
							// 장애인석/휠체어석
							if(data.seatAlloc=='40'){
								seatHtml += "<button title=\"" + data.seatGroup + data.seatNo + "(장애인석/휠체어석)\" "
								+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"ico_wheel\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
								+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
								+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
								+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
								+ " onclick=\"BookingSeat.checkSeatPopup3('"+self.cinemaCode+"','"+self.screenCode+"','"+self.playDate+"','"+self.showSeq+"','"+data.seatAllocType+"','"+data.seatGroup+"','"+data.seatNo+"', this) \""
								+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//								+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
								+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
								+ ">" + data.seatNo	+ "</button>";

							//4인소파 , 2인소파 일 경우 좌석 색 알아보기 쉽게 변경
							//4인소파
							}else if(BookingSeatType.seatType == 83) {
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(소파4인석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_4\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//2인소파
							}else if( BookingSeatType.seatType == 84 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(소파2인석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_2\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//스위트 ex) 코엑스 스위트룸.
							}else if( BookingSeatType.seatType == 82 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(스위트석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_sweet\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//테이블석
							}else if( BookingSeatType.seatType == 76 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(테이블석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_table\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							// 발코니M
							}else if( BookingSeatType.seatType == 85 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(발코니M)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_sofa_bcnM\" "
									+ " style=\"width: " +seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							// 키즈존
							}else if( BookingSeatType.seatType == 86 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(키즈존)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_kids\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							// 발코니디럭스M
							}else if( BookingSeatType.seatType == 87 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(발코니2)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_sofa_bcnDM\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							// 스페셜존
							}else if( BookingSeatType.seatType == 88 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(스페셜존)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_special\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//일반
							}else{
								seatHtml +=

									"<button title=\"" + data.seatGroup + data.seatNo + "(일반석)\""
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + "\""
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \""
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
//									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
//									+ " onkeydown=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							}
						}
					}
				}
			}else{
				/*
				 * Explorer 용
				 * onfocus, onblur, onkeyup(enter키만) << 키보드
				 * onmouseover, onmouseout, onclick << 마우스
				 */
				// 예매된 좌석이거나 고장난 좌석인 경우
				if (data.seatStatus != '00' || data.seatTrouble == 'Y') {
					// 선택불가(예매완료/고장)
					seatHtml +=
						"<button title=\"" + data.seatGroup + data.seatNo + "(선택불가)\" "
						+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"seat_done\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
						+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\" "
						+ " onclick=\"BookingSeat.checkSeatPopup2('"+self.cinemaCode+"','"+self.screenCode+"','"+self.playDate+"','"+self.showSeq+"','"+data.seatAllocType+"','"+data.seatGroup+"','"+data.seatNo+"')\">"
						+ data.seatNo
						+ "</button>";
				} else {

					// 선택된 좌석
					if (data.isSelected) {
						seatHtml +=
							"<button title=\"" + data.seatGroup + data.seatNo + "(선택됨)\" "
							+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"seat_selected\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
							+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\" onclick=\"BookingSeat.deselectSeat('" + data.seatGroup + "', '" + data.seatNo + "')\">" + data.seatNo
							+ "</button>";
					} else {

						// 선택되지 않은 좌석
						if (!isSelectable) {
							// 장애인석/휠체어석

								seatHtml += "<button title=\"" + data.seatGroup + data.seatNo + "(예매가능/선택불가)\" "
								+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"seat_cant\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
								+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\" "
								+ " onclick=\"BookingSeat.checkSeatPopup2('"+self.cinemaCode+"','"+self.screenCode+"','"+self.playDate+"','"+self.showSeq+"','"+data.seatAllocType+"','"+data.seatGroup+"','"+data.seatNo+"')\">"
								+ data.seatNo
								+ "</button>";

						} else {
							// 장애인석/휠체어석
							if(data.seatAlloc=='40'){
								seatHtml += "<button title=\"" + data.seatGroup + data.seatNo + "(장애인석/휠체어석)\" "
								+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"ico_wheel\" style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; "
								+ " left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
								+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
								+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
								+ " onclick=\"BookingSeat.checkSeatPopup3('"+self.cinemaCode+"','"+self.screenCode+"','"+self.playDate+"','"+self.showSeq+"','"+data.seatAllocType+"','"+data.seatGroup+"','"+data.seatNo+"', this) \""
								+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
								+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
								+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
								+ ">" + data.seatNo	+ "</button>";
							//4인소파 , 2인소파 일 경우 좌석 색 알아보기 쉽게 변경
							//4인소파
							} else if(BookingSeatType.seatType == 83) {
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(소파4인석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_4\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//2인소파
							}else if( BookingSeatType.seatType == 84 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(소파2인석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_2\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//스위트 ex) 코엑스 스위트룸.
							}else if( BookingSeatType.seatType == 82 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(스위트석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_sweet\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//테이블석
							}else if( BookingSeatType.seatType == 76 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(테이블석)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_table\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							// 발코니M
							}else if( BookingSeatType.seatType == 85 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(발코니M)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_sofa_bcnM\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//키즈존
							}else if( BookingSeatType.seatType == 86 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(키즈존)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_kids\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							// 발코니디럭스M
							}else if( BookingSeatType.seatType == 87 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(발코니2)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_sofa_bcnDM\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							// 스페셜존
							}else if( BookingSeatType.seatType == 88 ){
								seatHtml +=
									"<button title=\"" + data.seatGroup + data.seatNo + "(스페셜존)\" "
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " seat_special\" "
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" "
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							//일반
							}else{
								seatHtml +=

									"<button title=\"" + data.seatGroup + data.seatNo + "(일반석)\""
									+ " type=\"button\" seatType=\""+data.seatAlloc+"\" popupYn=\""+data.popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + "\""
									+ " style=\"width: " + seatLengthX + "px; height: " + seatLengtyY + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \""
									+ " onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" "
									+ " onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\""
									+ " onclick=\"BookingSeat.selectSeat(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ " onkeyup=\"BookingSeat.seatFocus('" + seatId + "')\" "
									+ " onblur=\"BookingSeat.seatKeyOut('" + seatId + "')\""
									+ " onkeypress=\"BookingSeat.selectSeatKey(this, '" + data.seatGroup + "', '" + data.seatNo + "')\" "
									+ ">" + data.seatNo	+ "</button>";
							}
						}
					}
				}
			}
			return seatHtml;
		}

		/*
		for (var i=0; i < seatListData.length; i++) {
			//var row = seatListData[i];

			var seatPosX   = parseInt(seatListData[i].seatPosX) / 10;
			var seatPosY   = parseInt(seatListData[i].seatPosY) / 10;

			if (largestSeatPosX < seatPosX) {
				largestSeatPosX = seatPosX;
			}

			if (largestSeatPosY < seatPosY) {
				largestSeatPosY = seatPosY;
			}

			var seatNo = seatListData[i].seatNo;
			var seatAlloc = parseInt(seatListData[i].seatAlloc);
			var seatAllocType = seatListData[i].seatAllocType;
			var seatLineSort = seatListData[i].seatLineSort;

			var tmpSeatGroup = seatListData[i].seatGroup;

			// 좌석그룹이 변경됐을 경우
			if ('' == seatGroup || tmpSeatGroup != seatGroup) {
				seatGroup = tmpSeatGroup;
				htmlTxt += "<span class=\"line line_" + seatGroup.toLowerCase() + "\" style=\"left:0px; top: " + seatPosY + "px;\">" + seatGroup + "</span>";
			}

			var seatId = seatIdGen(seatGroup, seatNo, seatLineSort, i);

			var seatClass = "seat_normal";	// 좌석상태이미지

			var isSelectable = true;	// 좌석선택가능여부

			// 선택한좌석유형이 아닌경우
			if (seatAllocType != BookingSeatType.seatType) {
				isSelectable = false;
			} else {
				// 좌석라인갯수가 붙임설정갯수보다 작으면 선택불가
				// 좌석기할당조건에 따라 선택불가
				if ((seatListData[i].seatLineCnt < this.seatPaxCount) || (seatAlloc != 10 && seatAlloc <= 70)) {
					isSelectable = false;
				} else {

					var emptySeatCount = 0;	// 빈좌석갯수

					// 좌->우
					if ( seatLineSort == 0) {

						// 현재좌석기준으로 붙임설정좌석갯수만큼 loop
						for (var index=i; index < (i+this.seatPaxCount); index++) {
							if (index == seatListData.length) {
								break;
							}

							// 빈좌석 = 선택안된 좌석 & 좌석선택가능(00) & 장애가없는좌석
							if ((!seatListData[index].isSelected) && seatListData[index].seatStatus == '00' && seatListData[index].seatTrouble != 'Y' && seatListData[index].seatAlloc!='40') {
								emptySeatCount++;
							}
						}

						// 좌석라인별 좌석번호가 짝수는 선택불가
						//if (seatListData[i].physicalNum % 2 == 0 && (((seatListData[i].seatLineCnt - seatListData[i].physicalNum) + 1) > this.seatPaxCount)) {
						if (seatListData[i].physicalNum % 2 == 0 && (((seatListData[i].seatLineCnt - seatListData[i].physicalNum) + 1) > this.seatPaxCount)) {

							isSelectable = false;

							// 한사람만 보는경우,
							// 1) 양옆자리가 예매되었거나 선택된 경우만 열어준다.
							// 2) 선택된 자리 양옆자리를 열어준다.
							if (this.seatPaxCount == 1) {
								var prevSeat = null;
								var nextSeat = null;

								if (i == 0) {
									prevSeat = seatListData[0];
								} else {
									prevSeat = seatListData[i-1];
								}

								if (i == (seatListData.length - 1)) {
									nextSeat = seatListData[seatListData.length-1];
								} else {
									nextSeat = seatListData[i+1];
								}

								// 양옆자리가 예매되었거나 선택된 경우라면
								if ((prevSeat.isSelected && nextSeat.isSelected)
										|| (prevSeat.seatStatus != '00' && nextSeat.seatStatus != '00' && nextSeat.seatAlloc!='40')) {
									isSelectable = true;
								}

								// 선택된자리(예매된 자리가 아님)인 경우 양옆자리를 열어준다
								if (prevSeat.isSelected || nextSeat.isSelected) {
									isSelectable = true;
								}
							}
						}

						if (emptySeatCount != this.seatPaxCount
								|| (((seatListData[i].seatLineCnt - seatListData[i].physicalNum) + 1) < this.seatPaxCount)) {
							isSelectable = false;
						}

						if(Number(this.seatPaxCount)>1 && (i+Number(this.seatPaxCount)+1)<=seatListData.length)
						{
							if(seatListData[i].seatAlloc!=seatListData[i+(Number(this.seatPaxCount)-1)].seatAlloc)
								isSelectable = false;
						}
					}

					// 우->좌
					else {
						// 현재좌석기준으로 붙임설정좌석갯수만큼 loop
						for (var index=i; index > (i-this.seatPaxCount); index--) {
							if (index < 0) {
								break;
							}

							// 빈좌석 = 선택안된 좌석 & 좌석선택가능(00) & 장애가없는좌석
							if ((!seatListData[index].isSelected) && seatListData[index].seatStatus =='00' && seatListData[index].seatTrouble != 'Y' && seatListData[index].seatAlloc!='40') {
								emptySeatCount++;
							}
						}

						// 좌석라인별 좌석번호가 홀수이면 선택불가
						//if (seatListData[i].physicalNum % 2 == 1 && (seatListData[i].physicalNum > this.seatPaxCount)) {
						if (seatListData[i].seatNo % 2 == 1 && (seatListData[i].seatNo > this.seatPaxCount)) {
							isSelectable = false;


							var prevSeat = null;
							var nextSeat = null;

							if (i == 0) {
								prevSeat = seatListData[0];
							} else {
								prevSeat = seatListData[i-1];
							}

							if (i == (seatListData.length - 1)) {
								nextSeat = seatListData[seatListData.length-1];
							} else {
								nextSeat = seatListData[i+1];
							}

							// 한사람만 보는경우,
							// 1) 양옆자리가 예매되었거나 선택된 경우만 열어준다.
							// 2) 선택된 자리 양옆자리를 열어준다.
							if (this.seatPaxCount == 1) {

								// 양옆자리가 예매되었거나 선택된 경우라면
								if ((prevSeat.isSelected && nextSeat.isSelected)
										|| (prevSeat.seatStatus != '00' && nextSeat.seatStatus != '00' && nextSeat.seatAlloc!='40' && prevSeat.seatAlloc!='40')) {
									isSelectable = true;
								}

								// 선택된자리(예매된 자리가 아님)인 경우 양옆자리를 열어준다
								if (prevSeat.isSelected || nextSeat.isSelected) {
									isSelectable = true;
								}
							}
							else
							{

							}
						}

						if (emptySeatCount != this.seatPaxCount
								|| (this.seatListData[i].physicalNum < this.seatPaxCount)) {
							isSelectable = false;
						}

						if(Number(this.seatPaxCount)>1 && (i-(Number(this.seatPaxCount)-1))>=0)
						{
							if(seatListData[i].seatAlloc!=seatListData[i-(Number(this.seatPaxCount)-1)].seatAlloc)
								isSelectable = false;
						}
					}
				}
			}

			// @TODO 예매좌석
			// 예매된 좌석이거나 고장난 좌석인 경우
			if (seatListData[i].seatStatus != '00' || seatListData[i].seatTrouble == 'Y' || seatListData[i].seatAlloc=='40') {
				if(seatListData[i].seatAlloc=='40')
					htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"ico_wheel\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \">" + seatNo + "</button>";
				else
					htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"seat_done\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\">" + seatNo + "</button>";

			} else {
				if (seatListData[i].isSelected) {
					htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"seat_selected\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\" onclick=\"BookingSeat.deselectSeat('" + seatGroup + "', '" + seatNo + "')\">" + seatNo + "</button>";
				} else {
					if (!isSelectable) {
						if(seatListData[i].seatAlloc=='40')
							htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"ico_wheel\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \">" + seatNo + "</button>";
						else
							htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"seat_cant\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px;\">" + seatNo + "</button>";
					} else {
						//4인소파 , 2인소파 일 경우 좌석 색 알아보기 쉽게 변경
						if(BookingSeatType.seatType == 83)
							htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_4\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" onclick=\"BookingSeat.selectSeat(this, '" + seatGroup + "', '" + seatNo + "')\" onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\">" + seatNo + "</button>";
						else if( BookingSeatType.seatType == 84 )
							htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_2\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" onclick=\"BookingSeat.selectSeat(this, '" + seatGroup + "', '" + seatNo + "')\" onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\">" + seatNo + "</button>";
						else if( BookingSeatType.seatType == 82 )
							htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + " sofa_sweet\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" onclick=\"BookingSeat.selectSeat(this, '" + seatGroup + "', '" + seatNo + "')\" onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\">" + seatNo + "</button>";
						else
						{
							htmlTxt += "<button type=\"button\" popupYn=\""+seatListData[i].popupYn+"\" id=\"" + seatId + "\" class=\"" + seatClass + "\" style=\"width: " + SEAT_WIDTH + "px; height: " + SEAT_HEIGHT + "px; left: " + (seatPosX + OFFSET_X_PLUS) + "px; top: " + seatPosY + "px; \" onclick=\"BookingSeat.selectSeat(this, '" + seatGroup + "', '" + seatNo + "')\" onmouseover=\"BookingSeat.seatMouseOver('" + seatId + "')\" onmouseout=\"BookingSeat.seatMouseOut('" + seatId + "')\">" + seatNo + "</button>";
						}
					}
				}
			}
		}
		*/
		seatWrapWidth  = largestSeatPosX + SEAT_WIDTH + SEAT_GROUP_WIDTH + 3;
		seatWrapHeight = largestSeatPosY + SEAT_HEIGHT;

		$('#seatPositionList').css({width: seatWrapWidth, height: seatWrapHeight});
		$('#seatPositionList').html(htmlTxt);

	},

	// 다시선택
	resetAll: function() {
		this.selectedSeats = [];
		this.tmpSelectedSeats = [];
		this.seatPaxCount = 1;

		// 좌석정보를 가져온다.
		this.seatListData = this.getSeatListData(this.cinemaCode, this.screenCode, this.playDate, this.showSeq);

		// 좌석선택-티켓종류화면 호출
		BookingSelectSeatTicket.showPage(this.cinemaCode, this.screenCode, this.playDate, this.showSeq, this.vipGradeCode, this.showMovieCode);

		// 좌석선택-예약현황화면 호출
		BookingSeatStatusBoard.showPage(this.movieCode, this.showMovieCode, this.cinemaCode, this.screenName, this.playDate, this.startTime);

		// 좌석그리기
		this.draw();
	},

	// 좌석선점
	holdSeat: function(cinemaCode, screenCode, playDate, showSeq, transNo, memberYn) {
		var that = this;

		var cnt = BookingSelectSeatTicket.getTotalCountSelectedTicket(); // 선택한 전체티켓수
		var seatText = '';
		for (var i=0; i < that.selectedSeats.length; i++) {
			seatText += that.selectedSeats[i].seatGroup + ';' + that.selectedSeats[i].seatNo + ';$';
		}

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.holdSeat',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				terminalCode: '77',
				transNo: transNo,
				user: '36',
				cnt: cnt,
				seatText: seatText
			}
		}).success(function( data ) {
			if (data.resultCode === '000') {
				$('.seat_text').popover('hide');// 붙임설정설명 가리기
				BookingPayment.showPage(that.transNo, that.screenCode, 'BOK', that.playDate, memberYn);
				$('#select_pay').modal('show');
			} else {
				messageBox('이미 예매진행된 좌석입니다.');
			}
		});
	},

	// 좌석선점 해제
	releaseSeat: function(cinemaCode, screenCode, playDate, showSeq, transNo) {

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.releaseSeat',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				terminalCode: '77',
				transNo: transNo,
				user: '36'
			}
		}).success(function( data ) {
			if (data.resultCode === '000') {
				if (BookingNonSeat.nonSeatYn !== 'Y') {
					BookingSeat.resetAll();
				}
				else
				{
					BookingNonSeat.resetAll();
				}
			} else {
			}
		});
	},

	// 좌석 포커스(키보드)
	seatFocus: function(selectedSeatId) {
		var seatArr = selectedSeatId.split('_');	// seatArr (1:seatGroup, 2:seatNo, 3:seatLineSort, 4:index)

		var seatGroup 	 = seatArr[1];
		var seatNo 		 = parseInt(seatArr[2]);
		var seatLineSort = seatArr[3];
		var index 		 = parseInt(seatArr[4]);

		// 좌->우
		if (seatLineSort == 0) {

			for (var i=0; i < this.seatPaxCount; i++) {
				var tmpSeatInfo = {};

				var seatId     = seatIdGen(seatGroup, seatNo + i, seatLineSort, index + i);

				if ($('#'+seatId).length == 0) {
					seatId = seatIdGen(seatGroup, seatNo + i, 1, index + i);
				}

				var seatStatus = $('#'+seatId).attr('class');

				if ($('#'+seatId).length == 0) {
					continue;
				}

				tmpSeatInfo.id = seatId;
				tmpSeatInfo.seatStatus = seatStatus;
				tmpSeatInfo.popupYn = $('#'+seatId).attr("popupYn");

				$('#'+seatId).removeClass();
				$('#'+seatId).addClass('seat_selected');

				this.tmpSelectedSeats.push(tmpSeatInfo);
			}

		// 우->좌
		} else {
			for (var i=0; i < this.seatPaxCount; i++) {
				var tmpSeatInfo = {};

				var seatId = seatIdGen(seatGroup, seatNo - i, seatLineSort, index - i);

				if ($('#'+seatId).length == 0) {
					seatId = seatIdGen(seatGroup, seatNo - i, 0, index - i);
				}

				var seatStatus = $('#'+seatId).attr('class');

				if ($('#'+seatId).length == 0) {
					continue;
				}

				tmpSeatInfo.id = seatId;
				tmpSeatInfo.seatStatus = seatStatus;
				tmpSeatInfo.popupYn = $('#'+seatId).attr("popupYn");

				$('#'+seatId).removeClass();
				$('#'+seatId).addClass('seat_selected');

				this.tmpSelectedSeats.push(tmpSeatInfo);
			}
		}

	},
	// 좌석 마우스오버시 처리
	seatMouseOver: function(selectedSeatId) {
		var seatArr = selectedSeatId.split('_');	// seatArr (1:seatGroup, 2:seatNo, 3:seatLineSort, 4:index) ex. seat_A_1_1_0

		var seatGroup 	 = seatArr[1];
		var seatNo 		 = parseInt(seatArr[2]);
		var seatLineSort = seatArr[3];
		var index 		 = parseInt(seatArr[4]);

		// 좌->우
		if (seatLineSort == 0) {

			for (var i=0; i < this.seatPaxCount; i++) {
				var tmpSeatInfo = {};

				var seatId     = seatIdGen(seatGroup, seatNo + i, seatLineSort, index + i);

				if ($('#'+seatId).length == 0) {
					seatId = seatIdGen(seatGroup, seatNo + i, 1, index + i);
				}

				var seatStatus = $('#'+seatId).attr('class');

				if ($('#'+seatId).length == 0) {
					continue;
				}

				tmpSeatInfo.id = seatId;
				tmpSeatInfo.seatStatus = seatStatus;
				tmpSeatInfo.popupYn = $('#'+seatId).attr("popupYn");

				$('#'+seatId).removeClass();
				$('#'+seatId).addClass('seat_selected');
				this.tmpSelectedSeats.push(tmpSeatInfo);
			}

		// 우->좌
		} else {
			for (var i=0; i < this.seatPaxCount; i++) {
				var tmpSeatInfo = {};

				var seatId = seatIdGen(seatGroup, seatNo - i, seatLineSort, index - i);

				if ($('#'+seatId).length == 0) {
					seatId = seatIdGen(seatGroup, seatNo - i, 0, index - i);
				}

				var seatStatus = $('#'+seatId).attr('class');

				if ($('#'+seatId).length == 0) {
					continue;
				}

				tmpSeatInfo.id = seatId;
				tmpSeatInfo.seatStatus = seatStatus;
				tmpSeatInfo.popupYn = $('#'+seatId).attr("popupYn");

				$('#'+seatId).removeClass();
				$('#'+seatId).addClass('seat_selected');

				this.tmpSelectedSeats.push(tmpSeatInfo);
			}
		}
	},

	seatKeyOut: function() {
		for (var i=0; i < this.tmpSelectedSeats.length; i++) {
			var seatArr = this.tmpSelectedSeats[i].id.split('_');
			//var seatNo = parseInt(seatArr[2]);
			var index = parseInt(seatArr[4]);

			if (!this.seatListData[index].isSelected) {
				$('#' + this.tmpSelectedSeats[i].id).removeClass();
				$('#' + this.tmpSelectedSeats[i].id).addClass(this.tmpSelectedSeats[i].seatStatus);
			}
		}

		this.tmpSelectedSeats = [];
	},

	// 좌석 마우스아웃시 처리
	seatMouseOut: function() {
		for (var i=0; i < this.tmpSelectedSeats.length; i++) {
			var seatArr = this.tmpSelectedSeats[i].id.split('_');
			//var seatNo = parseInt(seatArr[2]);
			var index = parseInt(seatArr[4]);

			if (!this.seatListData[index].isSelected) {
				$('#' + this.tmpSelectedSeats[i].id).removeClass();
				$('#' + this.tmpSelectedSeats[i].id).addClass(this.tmpSelectedSeats[i].seatStatus);
			}
		}

		this.tmpSelectedSeats = [];
	},

	// 붙임설정갯수 설정
	setSeatPaxCount: function(count) {
		this.seatPaxCount = parseInt(count);
		this.draw();
	},

	// 현재 선택한 좌석수 가져오기
	getSelectedSeatCount: function() {
		var count = 0;
		for (var i=0; i < this.selectedSeats.length; i++) {
			if (this.selectedSeats[i].seatNo !== '') {
				count++;
			}
		}
		return count;
	},

	// 좌석선택완료
	completeSelectSeat: function() {

		//2015.04.30 중복클릭 막음 - kch
		if (this.dbClickBlock)	return;
		else					this.dbClickBlock = true;

		if(!BookingNonSeat.kidTicketCheck())
		{
			messageBox('어린이요금과 성인요금을 같이 선택하셔야 진행이 가능합니다.');
			this.dbClickBlock = false;	//2015.04.30 중복클릭잠금 해제 - kch
			return;
		}

		var totalCountSelectedTicket = BookingSelectSeatTicket.getTotalCountSelectedTicket(); // 예매하고자 하는 티켓수
		var selectedSeatCount = this.getSelectedSeatCount(); // 현재 선택한 좌석수

		if (totalCountSelectedTicket == 0) {
			messageBox('인원을 먼저 선택해주세요.');
			this.dbClickBlock = false;	//2015.04.30 중복클릭잠금 해제 - kch
			return;
		}
		if (selectedSeatCount == 0) {
			messageBox('예매하고자 하는 좌석을 선택해주세요.');
			this.dbClickBlock = false;	//2015.04.30 중복클릭잠금 해제 - kch
			return;
		}
		if (selectedSeatCount != totalCountSelectedTicket) {
			messageBox('예매하고자 하는 좌석수와 선택한 좌석수가 일치하지 않습니다.');
			this.dbClickBlock = false;	//2015.04.30 중복클릭잠금 해제 - kch
			return;
		}

		var that = this;

		// 로그인 체크
		$.ajax({
			url: '/pages/common/loginCheck.jsp',
			type: 'POST'
		}).success(function( data ) {
			if (data.resultCode === 'success') {

				if(!BookingSeat.checkBlackList('')) {
					that.dbClickBlock = false;	//2015.04.30 중복클릭잠금 해제 - kch
					return;
				}

				// 선택한 좌석에 따른 티켓 코드 최종 세팅
				for(var i=0; i< BookingSeat.selectedSeats.length; i++){
					BookingSeat.selectedSeats[i].ticketCode = BookingSelectSeatTicket.selectedTickets[i].code;
					BookingSeat.selectedSeats[i].amt = BookingSelectSeatTicket.selectedTickets[i].amt;
				}

				that.transNo = that.createTradeNo(that.cinemaCode);
				// 좌석선점
				that.holdSeat(that.cinemaCode, that.screenCode, that.playDate, that.showSeq, that.transNo, 'Y');
			} else {

				// 선택한 좌석에 따른 티켓 코드 최종 세팅
				for(var i=0; i< BookingSeat.selectedSeats.length; i++){
					BookingSeat.selectedSeats[i].ticketCode = BookingSelectSeatTicket.selectedTickets[i].code;
					BookingSeat.selectedSeats[i].amt = BookingSelectSeatTicket.selectedTickets[i].amt;
				}

				if (data.resultMessage != null && (data.resultMessage).trim().length > 0) {
					messageBox(data.resultMessage, function(){
						showLoginPopup();
					});
				} else {
					$.ajax({
						url: '/pages/common/loginPopupAd.jsp',
						type: 'POST'
					}).success(function( data ) {
						if (data.resultCode === 'success') {
							$("#modal_login .ad_box").attr("href", "javascript:adHasClick(\'"+data.link+"\', "+data.adClick+")").attr("title", data.linkAlt).html("<img src=\""+data.image+"\" alt=\""+data.alt+"\"/>");
							//$(".ad_box").html("<img src=\""+data.image+"\" alt=\""+data.alt+"\"/>");
							showLoginPopup();
						}
					});
				}
			}

			that.dbClickBlock = false;	//2015.04.30 중복클릭잠금 해제 - kch
		});
	},

	createTradeNo: function(cinemaCode) {

		var transNo = null;

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {_command: 'Booking.createTradeNo', cinemaCode: cinemaCode, empNo: '36'}

		}).success(function( data ) {
			transNo = data.transNo;
			BookingSeatDatas.movieData.transNo = data.transNo;
		});

		return transNo;
	},

	nonmemberBooking: function() {

		var data = extractFormData('#nonmemberBookingPopupSub');

		if (validate('#nonmemberBookingPopupSub')) {

			if(data.name.length < 2){
				messageBox("이름은 최소 2글자 이상 입력해주세요.");
				$('#nonmemberBookingPopupSub input[name=name]').focus();
				return;
			}

			var pattern = /([^가-힣\x20])/i;
			if (pattern.test(data.name)) {
				messageBox("이름을 정확히 입력해주세요.");
				$('#nonmemberBookingPopupSub input[name=name]').focus();
				return;
			}

			if(data.birthday.trim().length != 6){
				messageBox( "생년월일을 정확히 입력해주세요.");
				$('#nonmemberBookingPopupSub input[name=birthday]').focus();
				return;

			} else if(Number(data.birthday.trim().substring(2,4))<1 || Number(data.birthday.trim().substring(2,4))>12 || Number(data.birthday.trim().substring(4,6)) > 31){
				messageBox( "생년월일을 정확히 입력해주세요.");
				this.isSaveCheck=false;
				return;
			}

			if(data.mobileNo1 && data.mobileNo1.length != 3){
				messageBox("휴대폰번호를 확인해주세요.");
				$('#nonmemberBookingPopupSub input[name=mobileNo1]').focus();
				return;
			}

			if(data.mobileNo2 && (data.mobileNo2.length != 3 && data.mobileNo2.length != 4)){
				messageBox("휴대폰번호를 확인해주세요.");
				$('#nonmemberBookingPopupSub input[name=mobileNo2]').focus();
				return;
			}

			if(data.mobileNo3 && data.mobileNo3.length != 4){
				messageBox("휴대폰번호를 확인해주세요.");
				$('#nonmemberBookingPopupSub input[name=mobileNo3]').focus();
				return;
			}

			if(!isMobile(data.mobileNo1 + data.mobileNo2 + data.mobileNo3)){
				return;
			}

			if(data.password.length != 4){
				messageBox("비밀번호는 4자리로 입력해주세요.");
				$('#nonmemberBookingPopupSub input[name=password]').focus();
				return;
			}

			if(data.password != data.confirmPassword){
				messageBox( "비밀번호 재입력을 다시 확인해주세요.");
				$('#nonmemberBookingPopupSub input[name=confirmPassword]').focus();
				return;
			}

			//비회원 이름에서 공백 제거
			this.nonmemberInfo.name = data.name ? data.name.replace(/ /g, '') : '';
			this.nonmemberInfo.birthday = data.birthday;
			this.nonmemberInfo.mobileNo1 = data.mobileNo1;
			this.nonmemberInfo.mobileNo2 = data.mobileNo2;
			this.nonmemberInfo.mobileNo3 = data.mobileNo3;
			this.nonmemberInfo.password = sha256_digest(data.password);

			if(!this.checkBlackList('nonmember')) return;

			// 좌석선점
			if (BookingNonSeat.nonSeatYn !== 'Y') {
				this.transNo = this.createTradeNo(this.cinemaCode);
				this.holdSeat(this.cinemaCode, this.screenCode, this.playDate, this.showSeq, this.transNo, 'N');
			} else {
				BookingNonSeat.transNo = this.createTradeNo(BookingNonSeat.cinemaCode);
				BookingNonSeat.holdNonselectionSeat(BookingNonSeat.cinemaCode, BookingNonSeat.screenCode, BookingNonSeat.playDate, BookingNonSeat.showSeq, BookingSeatType.seatType, BookingNonSeat.transNo, 'N');
			}
			closeMessageBox('nonmemberBookingPopup', 'custom');
		}
	},

	checkBlackList: function(gbn){

		var mobileNo="";
		var returnCheck=false;

		if(typeof gbn!='undefined' && gbn=='nonmember')
			mobileNo=String(this.nonmemberInfo.mobileNo1)+String(this.nonmemberInfo.mobileNo2)+String(this.nonmemberInfo.mobileNo3);

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async:false,
			data: {
				_command: 'Pay.checkBlackList',
				mobileNo: mobileNo,
				siteCode: '36'
			}
		}).success(function( data ) {
			if(data.resultCode=='0000')
			{
				if(data.continueYn=="Y")
					returnCheck=true;
				else
					messageBox(data.resultMessage);
			}
			else
			{
				if(data.continueYn=="Y")
					returnCheck=true;

				messageBox(data.resultMessage);
			}

		});

		return returnCheck;
	}

};

/****************************
 * 티켓선택화면
 ****************************/
var BookingSelectSeatTicket = {

	MAX_PERSON: 8,	// 최대인원수

	//personTypeInfos: [],
	selectedTickets: [],

	showPage: function(cinemaCode, screenCode, playDate, showSeq, vipGradeCode, showMovieCode) {
		try {
			_requestBookingSeatTicket.request({
				seatType: BookingSeatType.seatType,
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				vipGradeCode: vipGradeCode,
				showMovieCode: showMovieCode
			});
		} catch (e) {
			messageBox(e);
		}
	},

	// 인원 선택시 팝업여부 체크 후 팝업 출력
	checkTicketPopup: function(cinemaCode, screenCode, playDate, showSeq, ticketGroupCode, ticketCode, showMovieCode) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkTicketPopup',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				ticketGroupCode: ticketGroupCode,
				ticketCode: ticketCode,
				showMovieCode: showMovieCode,
				korEngGubun: '1',
				siteCode: '36'
			}
		}).success(function( data ) {
			var result = data.result;
			if (result != null && $.trim(result.popupMsg) !== '') {
				if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image'){
					if(result.popupMsg.indexOf("WEB_tickettype_09") > 0){
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="해당 영화는  12/15세 관람가 영화이므로 어린이는 보호자 없이 관람이 불가능하여, 어린이요금과 성인요금을 같이 선택하셔야 진행이 가능합니다."/>');
					}else if(result.popupMsg.indexOf("WEB_tickettype_14") > 0){
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt="우대요금은 장애인 고객에 한하여 적용됩니다.상영관 입장 시 복지카드 확인이 필요하므로, 반드시 복지카드를 지참하시기 바랍니다.복지카드 확인에 따라 입장이 제한될 수 있습니다.(장애 1~3등급은 동반 1인 포함,장애 4~6등급은 본인에 한하여 할인 적용)*우대 요금 선택 시, 신용카드, 휴대폰 결제만 가능합니다.*국가유공자, 만 65세 이상 등 기타 우대 발권은 현장에서 신분증/국가유공자증 확인 후 할인 적용됩니다."/>');
					}else{
						messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"/>');
					}

				}else
					messageBox( result.popupMsg);
			}
		});
	},

	refreshPage: function(options) {
		for (var option in options) {
			_requestBookingSeatTicket.put(option, options[option]);
		}
		try {
			_requestBookingSeatTicket.refresh();
		} catch(e) {
			messageBox(e);
		}
	},

	// selectbox값 변경시 처리
	changedSelect: function(obj, cinemaCode) {

		// 일반 or 드라이브 존 및 스페셜존 선택시 둘 중 하나는 선택 불가능 체크 로직 추가
		if(cinemaCode == '4461') {
			var ticketTypeCode = $(obj).attr('ticketTypeCode');
			var ticketCode = $('select[name=ticketCodeZR70]').val();

			if(ticketTypeCode == '01' && typeof ticketCode == 'undefined') {
				var ticketCodeDrive = $('select[name=ticketCodeZR76]').val();
				var ticketCodeVIP = $('select[name=ticketCodeZXFJ]').val();
				if(ticketTypeCode == '01' && (ticketCodeDrive == '0' || ticketCodeVIP == '0')) {
					this._optionValue = $(obj).val();
				} else {
					if(this._optionValue > 0) {
						messageBox('일반요금과 VIP요금은 같이 선택할 수 없습니다.');
						$(obj).val($(obj).data('pre-value')); // 이전값으로 셋팅
						return;
					}
				}
			}

			if(ticketTypeCode == '12') {
				var ticketCodeDrive = $('select[name=ticketCodeZR71]').val();
				var ticketCodeVIP = $('select[name=ticketCodeZXFH]').val();

				if(ticketTypeCode == '12' && (ticketCodeDrive == '0' || ticketCodeVIP == '0')) {
					this._optionValue = $(obj).val();
				} else {
					if(this._optionValue > 0) {
						messageBox('일반요금과 VIP요금은 같이 선택할 수 없습니다.');
						$(obj).val($(obj).data('pre-value')); // 이전값으로 셋팅
						return;
					}
				}
			}
		}

		//this.setSelectedPersonType();

		var totalCountSelectedTicket = this.getTotalCountSelectedTicket();	// 선택한 전체티켓수


		// // 일반 or 드라이브 존 및 스페셜존 선택시 둘 중 하나는 선택 불가능 체크 로직 추가됨으로서 주석 처리함.
		if (totalCountSelectedTicket > 8) {
			messageBox('인원선택은 총 ' + this.MAX_PERSON + '명까지 가능합니다.');
			$(obj).val($(obj).data('pre-value')); // 이전값으로 셋팅
			return;
		}

		this.setSelectedTickets();

		$('span#totalCountSelectedTicket').text(totalCountSelectedTicket);	// 선택한 티켓수 표시
		BookingSeatStatusBoard.displayCountSelectedByTicket(this.selectedTickets);	// 티켓별 선택한 티켓수 표시
		BookingSeatStatusBoard.displayTotalTicketPrice(this.getTotalPrice());		// 전체티켓금액표시

		// 이전값이 0이 아니라면
		if ($(obj).data('pre-value') != 0) {
			// 좌석정보를 초기화하여 다시 그린다
			BookingSeat.selectedSeats = [];
			BookingSeat.tmpSelectedSeats = [];
			BookingSeat.seatListData = BookingSeat.getSeatListData(BookingSeat.cinemaCode, BookingSeat.screenCode, BookingSeat.playDate, BookingSeat.showSeq);
			BookingSeatStatusBoard.resetDisplaySelectedSeat();	// 예매현황 좌석번호 초기화
			BookingSeat.draw();
		}

		// 비지정좌석이 아니라면
		if (BookingNonSeat.nonSeatYn !== 'Y') {
			// 선택한 붙임설정에 따라 활성화처리
			var selectableSeatCount = totalCountSelectedTicket - BookingSeat.getSelectedSeatCount();
			if (selectableSeatCount >= 4 ) {
				activeSeatPaxCount(4);
			} else {
				activeSeatPaxCount(selectableSeatCount);
			}
		}

		$(obj).data('pre-value', $(obj).val()); // 이전값 갱신
	},

	// 선택한 전체 티켓수 가져오기
	getTotalCountSelectedTicket: function() {
		var totalCount = 0;
		$('select[name^=ticketCode]').each(function() {
			totalCount += parseInt($(this).val());
		});
		return totalCount;
	},

	getTotalCountSelectedTicketCnt: function() {
		var totalCount = 0;
		$('select[name^=ticketCode]').each(function() {
			totalCount += parseInt($(this).find('option:selected').attr("cnt"));
		});
		return totalCount;
	},

	// 지정한 타입의 인원수 가져오기
	getCountSelectedPerson: function(ticketCode) {
		var count = $('select[name=ticketCode'+ticketCode+']').val();
		return count;
	},

	// 티켓정보 설정
	setSelectedTickets: function() {

		this.selectedTickets  = [];

		var that = this;

		$('select[name^=ticketCode]').each(function() {

			var ticketCode  = $(this).data('ticketCode');
			var ticketName = $(this).data('ticketName');
			var ticketAmt = $(this).data('ticketAmt');
			var count = $(this).val();
			var ticket = {};
			ticket.code = ticketCode;
			ticket.name = ticketName;
			ticket.amt = parseInt(ticketAmt);
			ticket.count = parseInt(count);

			if (ticket.count != 0) {
				for (var i=0; i < ticket.count; i++) {
					that.selectedTickets.push(ticket);
				}
			}
		});
	},

	// 선택한 전체티켓 가격합계
	getTotalPrice: function() {
		var totalPrice = 0;
		var selectedTickets = this.selectedTickets;

		for (var i=0; i < selectedTickets.length; i++) {
			totalPrice += selectedTickets[i].amt;
		}
		return totalPrice;
	}
};

/****************************
 * 좌석종류선택 화면
 ****************************/
var BookingSeatType = {

	seatType: null,

	showPage: function(cinemaCode, screenCode) {

		try {
			_requestBookingSeatSeatType.request({
				seatType: this.seatType,
				cinemaCode: cinemaCode,
				screenCode: screenCode
			});
		} catch(e) {
			messageBox(e);
		}
	},

	setSeatType: function(seatType) {
		this.seatType = seatType;
	}

};

var BookingTicket = {
	ticketPrices: null,		// 티켓가격정보

	init: function(cinemaCode, screenCode, playDate, showSeq, vipGradeCode) {
		// 티켓가격정보를 가져온다.
		this.getTicketPrices(cinemaCode, screenCode, playDate, showSeq, vipGradeCode);
	},

	//티켓정보를 가져온다.
	getTicketPrices: function(cinemaCode, screenCode, playDate, showSeq, vipGradeCode) {
		var that = this;

		if(typeof vipGradeCode=='undefined' || vipGradeCode==null || vipGradeCode=='')
			vipGradeCode='00';

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.getTicketPrices',
				siteCode: '36',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				vipGradeCode: vipGradeCode
			}
		}).success(function(result) {
			that.ticketPrices = result.list;
		});
	},

	getTicketPrice: function(ticketCode) {
		var ticketPrices = this.ticketPrices;
		var price = 0;
		for (var i=0; i < ticketPrices.length; i++) {
			if (ticketPrices[i].ticketCode == personType && ticketPrices[i].seatType == seatType) {
				price = ticketPrices[i].amt;
				break;
			}
		}
		return price;
	},
};

/***********************************
 * 비지정좌석선택
 ***********************************/
var BookingNonSeat = {

	nonSeatYn: null,
	movieCode: null,
	showMovieCode: null,
	cinemaCode: null,
	screenCode: null,
	playDate: null,
	startTime: null,
	showSeq: null,
	transNo: null,
	vipGradeCode:null,
	screenName:null,
	nonSeatYn:'N',
	selectedSeats: null,

	init: function(movieCode, showMovieCode, cinemaCode, screenCode, screenName, playDate, startTime, showSeq, vipGradeCode, englishYn, filePath) {

		this.movieCode = movieCode;
		this.showMovieCode = showMovieCode;
		this.cinemaCode = cinemaCode;
		this.screenCode = screenCode;
		this.playDate = playDate;
		this.startTime = startTime;
		this.showSeq = showSeq;
		this.vipGradeCode=vipGradeCode;
		this.screenName=screenName;

		// 좌석선택-좌석종류화면 호출
		BookingSeatType.showPage(cinemaCode, screenCode);

		// 좌석선택-티켓종류화면 호출
		BookingSelectSeatTicket.showPage(cinemaCode, screenCode, playDate, showSeq, vipGradeCode, showMovieCode);

		// 좌석선택-예약현황화면 호출
		this.nonSeatYn = 'Y';
		BookingSeatStatusBoard.showPage(movieCode, showMovieCode, cinemaCode, screenName, playDate, startTime, this.nonSeatYn);

		BookingTicket.init(cinemaCode, screenCode, playDate, showSeq, vipGradeCode);

		// 예매관련 팝업
		//BookingSeat.checkCinemaPopup(this.cinemaCode);
		setTimeout(function() {
			BookingSeat.checkMoviePopup(showMovieCode, cinemaCode);
			BookingSeat.checkRefundPopup(showMovieCode);
			BookingSeat.checkScreenPopup(cinemaCode, screenCode);
			BookingSeat.checkSequencePopup(cinemaCode, screenCode, playDate, showSeq);
		}, 300);
	},

	// 다시선택
	resetAll: function() {
		// 좌석선택-좌석종류화면 호출
		//BookingSeatType.showPage(this.cinemaCode, this.screenCode);

		// 좌석선택-티켓종류화면 호출
		BookingSelectSeatTicket.showPage(this.cinemaCode, this.screenCode, this.playDate, this.showSeq, this.vipGradeCode, this.showMovieCode);

		// 좌석선택-예약현황화면 호출
		BookingSeatStatusBoard.showPage(this.movieCode, this.showMovieCode, this.cinemaCode, this.screenName, this.playDate, this.startTime, this.nonSeatYn);

		BookingTicket.init(this.cinemaCode, this.screenCode, this.playDate, this.showSeq, this.vipGradeCode);
	},

	showPage: function(cinemaCode, screenCode, playDate, showSeq, englishYn, filePath) {
		try {
			_requestBookingSeatSpecial.request({
				siteCode: '36',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				englishYn: englishYn,
				testYn: 'N',
				filePath: filePath
			});
			// 1.상세 레이어 표시할때 타이틀 교체를 위한 이벤트 바인딩
			// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var moveTitle = '빠른예매(비회원좌석)' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?show=booking&p=step2";
			megaboxLog.openPopup(moveURL, moveTitle);
		} catch(e) {
			messageBox(e);
		}
		$('#select_seat').modal('show');
		// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
		$('#select_seat').off('hide.bs.modal').on('hide.bs.modal',function() {
			megaboxLog.closePopup();
		});
	},

	// 비지정좌석여부
	getSeatSelectionType: function(cinemaCode, screenCode, englishYn, callback) {

		var that = this;

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.getSeatSelectionType',
				siteCode: '36',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				englishYn: englishYn,
				testYn: 'N'
			}
		}).success(function( data ) {
			that.nonSeatYn = data.nonSeatyn;
			callback(data);
		});
	},

	kidTicketCheck :function(){

		var kidCheck=false;
		var etcCheck=false;
		for(var i=0;i<$('select[name^=ticketCode]').length;i++)
		{
			if(Number($($('select[name^=ticketCode]').get(i)).val())>0 )
			{
				if($($('select[name^=ticketCode]').get(i)).attr('tickettypecode')=='09' || $($('select[name^=ticketCode]').get(i)).attr('tickettypecode')=='20' || $($('select[name^=ticketCode]').get(i)).attr('tickettypecode')=='17')
					kidCheck=true;
				else if($($('select[name^=ticketCode]').get(i)).attr('tickettypecode')=='01' || $($('select[name^=ticketCode]').get(i)).attr('tickettypecode')=='15' || $($('select[name^=ticketCode]').get(i)).attr('tickettypecode')=='18')
					etcCheck=true;
			}
		}

		var filmRating=BookingSeatStatusBoard.filmRating;

		//alert("kidCheck :" + kidCheck);
		//alert("etcCheck :" + etcCheck)
		if(kidCheck && filmRating!='0' && filmRating!='00')
		{
			if(etcCheck)
				return true;
			else
				return false;
		}
		else
			return true;
	},

	// 좌석선택완료
	completeSelectSeat: function() {

		if(!this.kidTicketCheck())
		{
			messageBox('어린이요금과 성인요금을 같이 선택하셔야 진행이 가능합니다.');
			return;
		}

		var ticketCnt = BookingSelectSeatTicket.getTotalCountSelectedTicket(); // 예매하고자 하는 티켓수
		if (ticketCnt == 0) {
			messageBox('인원을 먼저 선택해주세요.');
			return;
		}

		var tempCnt=$("strong[name="+BookingSeatType.seatType+"]").html();
		var seatTicketCnt=0;
		if(typeof tempCnt!="undefined" && tempCnt!="")
			seatTicketCnt=Number(tempCnt);

		var tempTicketCnt = BookingSelectSeatTicket.getTotalCountSelectedTicketCnt();
		if(seatTicketCnt<tempTicketCnt)
		{
			messageBox('남은 좌석수를 초과하였습니다. 좌석수를 확인해 주세요.');
			return;
		}

		var that = this;

		// 로그인 체크
		$.ajax({
			url: '/pages/common/loginCheck.jsp',
			type: 'POST'
		}).success(function( data ) {
			if (data.resultCode === 'success') {

				if(!BookingSeat.checkBlackList('')) return;

				// 선택한 좌석에 따른 티켓 코드 최종 세팅
				for(var i=0; i< BookingSeat.selectedSeats.length; i++){
					BookingSeat.selectedSeats[i].ticketCode = BookingSelectSeatTicket.selectedTickets[i].code;
					BookingSeat.selectedSeats[i].amt = BookingSelectSeatTicket.selectedTickets[i].amt;
				}
				that.transNo = BookingSeat.createTradeNo(that.cinemaCode);
				// 좌석선점
				that.holdNonselectionSeat(that.cinemaCode, that.screenCode, that.playDate, that.showSeq, BookingSeatType.seatType, that.transNo, 'Y');
			} else {

				// 선택한 좌석에 따른 티켓 코드 최종 세팅
				for(var i=0; i< BookingSeat.selectedSeats.length; i++){
					BookingSeat.selectedSeats[i].ticketCode = BookingSelectSeatTicket.selectedTickets[i].code;
					BookingSeat.selectedSeats[i].amt = BookingSelectSeatTicket.selectedTickets[i].amt;
				}

				if (data.resultMessage != null && data.resultMessage.length > 0) {
					messageBox(data.resultMessage, function(){
						showLoginPopup();
					});
				} else {
					$.ajax({
						url: '/pages/common/loginPopupAd.jsp',
						type: 'POST'
					}).success(function( data ) {
						if (data.resultCode === 'success') {
							$(".ad_box").attr("href", "javascript:adHasClick(\'"+data.link+"\', "+data.adClick+", 'link')").attr("title", data.linkAlt).html("<img src=\""+data.image+"\" alt=\""+data.alt+"\"/>");
							//$(".ad_box").html("<img src=\""+data.image+"\" alt=\""+data.alt+"\"/>");
							showLoginPopup();
						}
					});
				}
			}
		});
	},

	// 좌석선점
	holdNonselectionSeat: function(cinemaCode, screenCode, playDate, showSeq, seatType, transNo, memberYn) {

		var ticketCnt = BookingSelectSeatTicket.getTotalCountSelectedTicket();

		var that = this;

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.holdNonselectionSeat',
				cinemaCode: cinemaCode,
				screenCode: screenCode,
				playDate: playDate,
				showSeq: showSeq,
				seatType: seatType,
				transNo: transNo,
				ticketCnt: ticketCnt,
				siteCode: '36'
			}
		}).success(function( data ) {
			if (data.resultCode === '0000') {

				if(typeof data.list =='undefined' || data.list.length==0)
				{
					messageBox('남은 좌석수를 초과하였습니다. 좌석수를 확인해 주세요.');
					return;
				}

				// 비지정좌석정보를 저장
				that.selectedSeats = data.list;
				var tickets = BookingSelectSeatTicket.selectedTickets;
				for (var i=0; i < tickets.length; i++) {
					that.selectedSeats[i].ticketCode = tickets[i].code;
				}

				BookingPayment.showPage(that.transNo, that.screenCode, 'BOK', that.playDate, memberYn);

				$('#select_pay').modal('show');

				// 결제창팝업 백드롭 클릭시 처리
				$('#select_pay').on('call.backdrop.function', function() {
					BookingPayment.cancelPayment();
				});

			} else {
				messageBox('이미 예매진행된 좌석입니다.');
			}
		});
	}
};

function seatIdGen(seatGroup, seatNo, seatLineSort, index) {
	return "seat_" + seatGroup + "_" + seatNo + "_" + seatLineSort + "_" + index;
}

// 선택한 좌석선택붙임설정 활성화
function activeSeatPaxCount(count) {

	if(BookingSeat.tempSeatLineCntCnt<count) {
		count=BookingSeat.tempSeatLineCntCnt;
	}

	// 붙임설정갯수 설정
	BookingSeat.setSeatPaxCount(count);

	if (count == 0) {
		$('input[name=seats]').iCheck('disable');
		$('ul.seat_continue li').removeClass('selected');
		return;
	}

	$('input[name=seats]').iCheck('disable');
	$('ul.seat_continue li').removeClass('selected');

	$('ul.seat_continue li').each(function(index) {
		$('#seatPaxCount'+(index+1)).iCheck('enable');
		$('#seatPaxCount'+count).iCheck('check');

		if ((index+1) == count) {
			$(this).addClass('selected');
			return false;
		}
	});
}