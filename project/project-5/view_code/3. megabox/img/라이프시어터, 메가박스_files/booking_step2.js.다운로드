var _requestBookingSeat = new JSPRequest('/pages/booking/step2/Booking_SelectSeat.jsp', 'div#bookingSeat', 'div#bookingSeat');
var _requestBookingSeatStatusBoard = new JSPRequest('pages/booking/step2/Booking_SelectSeat_StatusBoard.jsp', 'div#bookingSelectSeatStatusBoard', 'div#bookingSelectSeatStatusBoard');

/***********************************
 * 지정좌석선택
 ***********************************/
var BookingSeatDatas = {
	kidTicketCodes : ['09','17','20'],	// 키즈석 코드
	adultTicketCodes : ['01','15','18'], // 어른, 심야어른, 조조어른
//	saleTicketCodes : ['13','25'],	// 문화의날, 마티네데이
	normalTicketCode : '',
	preferentialTicketType : '14',	// 우대티켓코드
	normalSeat : '10',	// 일반석 코드
	disabledSeat : '40',	// 장애인석 코드
	kidSeat : '86',	// 키즈석 코드
	coupleSeat : '72', // 커플석 코드
	movieData : {}, // 예매 데이터
	exitList : {},	// 출입구 리스트
	seatList : {},	// 좌석 리스트
	seatTypes : {}, // 좌석종류 리스트
	ticketPricesSummary : {},	// 권종리스트
	ticketPricesSort : {},	// 선택된 권종에 따른 요금 리스트
	selectedSeats : [],	// 선택된 좌석
	ticketPriceEtcArry : [],	// 특이좌석리스트
	checkSeatArry : {}, // 좌석타입 별 권종 리스트
	kidSeatCheck: false,	// 키즈석 존재 여부
	seatPaxCount :0, // 붙임설정
	ticketTypeArry : {},	// 좌석타입 별 요금 리스트
	ticketDifference : [],	// 상영관 전체 요금 리스트
	selectedSeatInfoItems: [],	// 예매 현황판에 표기될 티켓 정보
	exclusiveType : null,	// 좌석존
	exclusiveSeatType : null,	// 선택된 좌석이 단독인 경우 좌석 코드 담아둠
	exclusiveSeatGroup : null,	// 선택된 좌석이 C타입인 경우 그룹 코드 담아둠
	preferentialSeatPopup: false, // 장애인석 안내 팝업 호출 여부
	dbClickBlock : false, // 중복클릭 막음
	tmpSelectedSeats : [],	// 임시로 셀렉트된 좌석정보
	nonmemberInfo: {},		// 비회원정보
	execlusiveSelPopupInfo: {}, //문화의날, 마티네데이 팝업메시지 사용여부
	init: function(){
		BookingSeatDatas.exitList = {};	// 출입구 리스트
		BookingSeatDatas.seatList = {};	// 좌석 리스트
		BookingSeatDatas.ticketPricesSort = {};	// 선택된 권종에 따른 요금 리스트
		BookingSeatDatas.ticketPriceEtcArry = [];	// 특이좌석리스트
		BookingSeatDatas.kidSeatCheck = false;	// 키즈석 존재 여부
		BookingSeatDatas.ticketTypeArry = {};	// 좌석타입 별 요금 리스트
		BookingSeatDatas.ticketDifference = [];	// 상영관 전체 요금 리스트
		BookingSeatDatas.nonmemberInfo = {};		// 비회원정보
		BookingSeatDatas.checkSeatArry = {};	// 좌석타입 별 권종 리스트

		// 변수 초기화
		BookingSeatDatas.resetVariable();

		// 상영관 정보 가져오기
		BookingSeatDatas.infoInit();

		// 인원 변경
		$('select[name^=ticketTypeCode_]', '#bookingSeatTicket').change(BookingSeatTicketDatas.changeTicket);
	},

	// 변수 초기화
	resetVariable : function(){
		BookingSeatDatas.selectedSeats = [];	// 선택된 좌석
		BookingSeatDatas.seatPaxCount = 0; // 붙임설정
		BookingSeatDatas.selectedSeatInfoItems = [];	// 예매 현황판에 표기될 티켓 정보
		BookingSeatDatas.preferentialSeatPopup = false; // 장애인석 안내 팝업 호출 여부
		BookingSeatDatas.dbClickBlock = false; // 중복클릭 막음
		BookingSeatDatas.tmpSelectedSeats = [];	// 임시로 셀렉트된 좌석정보
		BookingSeatDatas.exclusiveInit(); // 단독선택 초기화
		BookingSeatTicketDatas.ticketTypeCodes = {};
	},

	exclusiveInit: function(){
		BookingSeatDatas.exclusiveType = null; // 좌석존
		BookingSeatDatas.exclusiveSeatType = null;	// 선택된 좌석이 단독인 경우 좌석 코드 담아둠
		BookingSeatDatas.exclusiveSeatGroup = null; // 선택된 좌석이 C타입인 경우 그룹 코드 담아둠
	},

	// 상영관 정보 가져오기
	infoInit: function(){
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.getBookingSeatInfo',
				siteCode: '36',
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenCode: BookingSeatDatas.movieData.screenCode,
				playDate: BookingSeatDatas.movieData.playDate,
				showSeq: BookingSeatDatas.movieData.showSeq,
				showMovieCode: BookingSeatDatas.movieData.showMovieCode,
				korEngGubun: '1',
			}
		}).success(function( data ) {
			if(data.resultCode === '0000'){
				// 문화의날, 마티네데이 체크 값
				BookingSeatDatas.execlusiveSelPopupInfo = data.execlusiveSelPopupInfo;
				// 인원종류, 좌석종류별 요금 리스트
				BookingSeatDatas.setTicketPrices(data.ticketPrices);
				// 티켓 차액 리스트
				BookingSeatDatas.ticketDifference = data.ticketDifference;
				// 좌석 그리기
				BookingSeatDatas.seatList = data.seatList;
				// 출구 그리기
				BookingSeatDatas.exitList = data.exitList;
				// 권종리스트
				BookingSeatDatas.setTicketPricesSummary(data.ticketPricesSummary);
				// 좌석종류 리스트
				BookingSeatDatas.setSeatTypes(data.seatTypes);
	   			// 좌석의 선택정보 리스트
				BookingSeatDatas.setTicketPricesEtc(data.ticketPricesEtc);
				BookingSeatDatas.draw();

				// 좌석선택-예약현황화면 호출
				BookingSeatStatusBoard.showPage();
				$('#select_seat').modal('show');
				setTimeout(function() {

					// 영화 안내 팝업
					openGuidePopup(data.moviePopup);

					// 상영관 안내 팝업
					openGuidePopup(data.screenPopup);

					// 회차 안내 팝업
					openGuidePopup(data.sequencePopup);

					// 환불 수수료(Y) 팝업
					if (data.refundPopup != null && data.refundPopup.PopUpType != null) {
						if(typeof data.refundPopup.PopUpType!='undefined' && data.refundPopup.PopUpType!=null && data.refundPopup.PopUpType=='image'){
							messageBox( '<img src="'+data.refundPopup.ImagePathKor+'" class="layerPopupImgLoad"  alt=""/>');							
						}else{
							messageBox( data.refundPopup.MessageKor);
						}
					}
				}, 300);
			}else{
				messageBox(data.resultMessage);
			}
		});
	},

	// 권종 리스트
	setTicketPricesSummary : function(ticketPricesSummary){
		var types = {};
		$.each(ticketPricesSummary, function(i, item){
			types[item.ticketTypeCode] = item.displayName;
		});
		BookingSeatDatas.ticketPricesSummary = types;
	},

	// 좌석종류 리스트
	setSeatTypes : function(seatTypes){
		var types = {};
		$.each(seatTypes, function(i, item){
			types[item.seatType] = item.seatTypeName;
		});
		BookingSeatDatas.seatTypes = types;
	},


	// 특이 좌석확인
	setTicketPricesEtc : function(ticketPricesEtc){
		var arry = {};
		BookingSeatDatas.ticketPriceEtcArry = [];
		$.each(ticketPricesEtc, function(i, item){
			if(item.selectControlCnt != 1 || item.selectType != 'multiple' || item.maxCnt != 8 || item.multiply != 1){
				if(!arry[item.seatType]) arry[item.seatType] = {};
				// 선택 가능한 인원 수 찾기
				item.selectSeat = BookingSeatDatas.getSelectSeatItem(item);
				arry[item.seatType] = item;
			}else if(item.exclusiveYn!='N'){
				arry[item.seatType] = item;
			}
			if(item.seatType==BookingSeatDatas.kidSeat) BookingSeatDatas.kidSeatCheck = true;
		});
		BookingSeatDatas.ticketPriceEtcArry = arry;
	},

	getSelectSeatItem : function(item){
		// 선택 가능한 인원 수 찾기
		var selectControlCnt = item.selectControlCnt;
		var multiply = item.multiply;
		var maxCnt = item.maxCnt;
		var maxCount = maxCnt / multiply;
		var selectSeat = {};

		for (var cnt=0; cnt <= maxCount; cnt+=selectControlCnt) {
			selectSeat[cnt] = cnt;
		}
		return selectSeat;
	},

	// 인원종류, 좌석종류별 요금 리스트
	setTicketPrices: function(ticketPrices){
		var arry = {}; // 좌석별 티켓 리스트
		var checkSeatArry = {}; // 좌석별 선택 가능 권종 리스트
		$.each(ticketPrices, function(i, item){
			if(!arry[item.seatType]) {
				arry[item.seatType] = [];
				checkSeatArry[item.seatType] = {};
			}
			arry[item.seatType].push(item);
			checkSeatArry[item.seatType][item.ticketTypeCode] = 0;
			checkSeatArry[item.seatType].selected = 0;
		});

		BookingSeatDatas.checkSeatArry = checkSeatArry;
		BookingSeatDatas.ticketTypeArry = arry;
	},

	// 남은 좌석여부 확인
	checkSeatRemains: function(movieData) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {
				_command: 'Booking.checkSeatRemains',
				cinemaCode: movieData.cinemaCode,
				screenCode: movieData.screenCode,
				playDate: movieData.playDate,
				showSeq: movieData.showSeq,
				showMovieCode: movieData.showMovieCode,
				startTime: movieData.startTime,
				siteCode: '36'
			}
		}).success(function(data) {
			/*
			 * 2014.12.18
			 * 응답코드(0000: 정상진행 가능, 3333:팝업, 이외 오류)
			 * '3333'일 경우만 popupKind를 사용한다고 함. 박후선 이사님 확인완료
			 * */
			if (data.resultCode === '0000' || data.resultCode === '3333') {
				BookingSeatDatas.getSeatSelectionType(movieData);
			}else if(data.resultCode == '3333'){
				if(data.popupKind == 'info'){
					//[netfunnel] : 영화/시간선택 후 회차선택 / 좌석선택 직전 END(비정상)
					NetFunnel_Complete();
					messageBox(data.resultMessage);
				}else if(data.popupKind == 'yesno'){
					confirmBox('확인', data.resultMessage, function(){
						BookingSeatDatas.getSeatSelectionType(movieData);
					});
				}
			} else {
				//[netfunnel] : 영화/시간선택 후 회차선택 / 좌석선택 직전 END(비정상)
				NetFunnel_Complete();
				messageBox(data.resultMessage);
			}
		}).error(function(){
			//[netfunnel] : 영화/시간선택 후 회차선택 / 좌석선택 직전 END(비정상)
			NetFunnel_Complete();
		});
	},

	// 비지정좌석여부
	getSeatSelectionType : function(movieData){
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.getSeatSelectionType',
				siteCode: '36',
				cinemaCode: movieData.cinemaCode,
				screenCode: movieData.screenCode,
				englishYn: 'N',
				testYn: 'N'
			}
		}).success(function( data ) {
			BookingSeatDatas.movieData = movieData;
			BookingSeatDatas.movieData.nonSeatYn = data.nonSeatyn;
			BookingNonSeat.nonSeatYn = data.nonSeatyn;
			if (data.nonSeatyn === 'Y') {
				BookingNonSeat.showPage(movieData.cinemaCode, movieData.screenCode, movieData.playDate, movieData.showSeq, 'N', data.filePath);
				BookingNonSeat.init(movieData.movieCode, movieData.showMovieCode, movieData.cinemaCode, movieData.screenCode, movieData.screenName, movieData.playDate, movieData.startTime, movieData.showSeq, movieData.vipGradeCode, movieData.englishYn, data.filePath);
			} else {
				BookingSeatDatas.showPage();
			}
			//[netfunnel] : 영화/시간선택 후 회차선택 / 좌석선택 직전 END(정상)
			NetFunnel_Complete();
		});
	},

	// 상영관정보 가져오기
	getBookingSeatInfo : function(movieData){
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.getBookingSeatInfo',
				siteCode: '36',
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenCode: BookingSeatDatas.movieData.screenCode,
				playDate: BookingSeatDatas.movieData.playDate,
				showSeq: BookingSeatDatas.movieData.showSeq
			}
		}).success(function( data ) {
			var a = data;
			$('#select_seat').modal('show');
		});
	},

	// 좌석선택화면
	showPage: function() {
		try {
			_requestBookingSeat.request({
				siteCode: '36',
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenCode: BookingSeatDatas.movieData.screenCode,
				playDate: BookingSeatDatas.movieData.playDate,
				showSeq: BookingSeatDatas.movieData.showSeq
			});
			// 1.상세 레이어 표시할때 타이틀 교체를 위한 이벤트 바인딩
			// 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var moveTitle = '빠른예매(좌석)' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?show=booking&p=step2";
			megaboxLog.openPopup(moveURL, moveTitle);
			// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
			$('#select_seat').off('hide.bs.modal').on('hide.bs.modal',function() {
				megaboxLog.closePopup();
			});
			BookingSeatDatas.init();
		} catch(e) {
			messageBox(e);
		}
	},

	// 좌석도 그리기
	draw :function(){
		var htmlTxt = BookingSeatDatas.getExitDrawHtml();

		var normalTicketCode = BookingSeatDatas.normalTicketCode;

		var SEAT_WIDTH = 16;	// 좌석 width
		var SEAT_HEIGHT = 16;	// 좌석 height
		var SEAT_GROUP_WIDTH = 20;	// 좌석그룹명 width
		var OFFSET_X_PLUS = 23;

		// 좌석화면 가운데정렬 처리를 위한 변수
		var largestSeatPosX = 0;
		var largestSeatPosY = 0;
		var seatWrapWidth  = 0;
		var seatWrapHeight = 0;
		var seatPosX=0,seatPosY=0;
		var tmpSeatGroup='';
		var seatListData = BookingSeatDatas.seatList;
		var exclusiveType = BookingSeatDatas.exclusiveType;
		var exclusiveSeatType = BookingSeatDatas.exclusiveSeatType;
		var exclusiveSeatGroup = BookingSeatDatas.exclusiveSeatGroup;
		var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();

		// TODO 좌석 타입별 선택 가능 좌석 수 확인
		// 선택 가능 권종이 없을 경우! 비활성화를 위한 작업
		var selectableSeatGroups = [];
		var seatActive = false;

		for (var i=0; i < seatListData.length;) {
			var seatCount=(Number(seatListData[i].seatLineCnt)+i);

			var seatIdx=1;
			var maxCnt=0;
			for (var j=i; j < seatCount; j++) {

				seatPosX   = parseInt(seatListData[j].seatPosX) / 10;		// 좌석 X위치
				seatPosY   = parseInt(seatListData[j].seatPosY) / 10;		// 좌석 Y위치

				if (largestSeatPosX < seatPosX) {
					largestSeatPosX = seatPosX;
				}

				if (largestSeatPosY < seatPosY) {
					largestSeatPosY = seatPosY;
				}

				var seatNo = Number(seatListData[j].seatNo);				// 좌석번호(1-nn)
				var seatAlloc = Number(seatListData[j].seatAlloc);			// 좌석기할당(10, 70이상, 아니면 그레이로 선택불가표시)
				var seatStatus = seatListData[j].seatStatus;				// 좌석상태(00:선택가능)
				var seatLineSort = Number(seatListData[j].seatLineSort);	// 0:왼쪽->오른쪽 1:오른쪽->왼쪽 -- 같은블럭내에서
				var seatGroup = seatListData[j].seatGroup;					// 좌석그룹(A-Z)
				var seatLineCnt = Number(seatListData[j].seatLineCnt);		// 같이 블럭할 수 있는 최대수(영역)
				var seatGroupCnt = Number(seatListData[j].seatGroupCnt);	// 좌석열의합
				var seatLine = Number(seatListData[j].seatLine);			// 좌석묶음영역, 좌석열이랑 비슷한 개념 (확인필요)

				// 좌석그룹이 변경됐을 경우
				if ('' == tmpSeatGroup || tmpSeatGroup != seatGroup) {
					tmpSeatGroup = seatGroup;
					htmlTxt += "<span class=\"line line_" + seatGroup.toLowerCase() + "\" style=\"left:0px; top: " + seatPosY + "px;\">" + seatGroup + "</span>";
				}

				// 좌석 선택 가능 여부
				var isSelectable=false;
				// 특이좌석 정보
				var ticket = BookingSeatDatas.ticketPriceEtcArry[seatAlloc];
				if(!exclusiveType || (exclusiveType=='Y' && (exclusiveSeatType == seatAlloc || (exclusiveSeatType == BookingSeatDatas.normalSeat && BookingSeatDatas.disabledSeat == seatAlloc))) || (exclusiveType=='E' && ticket && exclusiveSeatGroup == ticket.groupCode)){
					// 좌석타입별 선택 가능 수량 확인
					var seatPaxCnt = BookingSeatDatas.seatPaxCountCheck(seatAlloc);
					if(seatLineCnt < seatPaxCnt) seatPaxCnt = seatLineCnt;
					// 단독 선택 좌석이고, 선택된 좌석이 단독 선택 좌석이 아닌 경우 또는 요금 정보가 없어 선택이 불가능한 경우
					if((ticket && ticket.exclusiveYn!='N' && !exclusiveType && selectedSeatCount > 0) || seatPaxCnt==-1){
						isSelectable=false;
					}else{
						/* *
						 * 할당(10, 40, 70이상)된 좌석이며, 선택된 좌석타입과 좌석타입이 같고,
						 * 최대 붙임 가능한 좌석수가 붙임좌석 설정 값 보다 크고,
						 * 예매 가능한 좌석이며, 선택되어 있지 않은 경우
						 * - 좌석구분(:seatAlloc) => 10:WEB용, 40:장애인/휠체어석, 70이상:특별석
						 * */
						if(seatListData[j].seatAllocType == BookingSeatDatas.disabledSeat && seatListData[j].seatStatus=='00' && !seatListData[j].isSelected){
							// 장애인석은 붙임설정에 관계없이, 좌석상태가 예매 가능하고, 선택되어 있지 않은 경우 선택 가능
							isSelectable = true;
						}else if ((seatAlloc == 10 || seatAlloc == 40 || seatAlloc >= 70) && ((seatAlloc =='85' || seatAlloc == '87') || seatLineCnt > 0)
							 && seatStatus=='00' && !seatListData[j].isSelected) {

							// 기본 설정
							if (seatLineSort == 0){	// 같은블럭내에서 왼쪽->오른쪽 정렬
								if((seatIdx % 2)!=0){ // 좌석붙임좌석 idx 가 홀수인 경우 선택 가능
									isSelectable=true;
								}
							} else { // 같은블럭내에서 오른쪽->왼쪽 정렬
								if((seatIdx % seatLineCnt) == 0){ // 좌석붙임좌석idx가 최대 붙임 가능한 좌석수의 배수인 경우 선택 가능
									isSelectable=true;
								}else{
									if((seatLineCnt%2)==0){	// 같이 블럭할 수 있는 최대수(영역)가 짝수인 경우
										if((seatIdx % 2)==0){ // 좌석붙임좌석idx 가 짝수일때 선택 가능
											isSelectable=true;
										}
									} else { // 같이 블럭할 수 있는 최대수(영역)가 홀수인 경우
										if((seatIdx % 2)!=0){ // 좌석붙임좌석idx 가 홀수일때 선택 가능
											isSelectable=true;
										}
									}
								}
							}

							var backSeat = j-1;
							var nextSeat = j+1;

							// 붙임좌석설정이 0인 경우
							if(seatPaxCnt==0){
								isSelectable=true;
							}else if(seatPaxCnt==1){ // 붙임좌석설정이 1이거나, 좌석의 선택 가능 좌석 수가 1인 경우
								// 같이 블럭할 수 있는 최대수(영역)가 2석보다 많음
								if(seatLineCnt > 2){
									// 블럭의 첫번째 좌석 이거나, 블럭의 끝좌석인 경우
									if(i==j || (nextSeat)==seatCount) isSelectable=true;
									else if((j-i)==1 || (seatCount-(nextSeat))==1){ // 블럭의 두번째 좌석 이거나, 블럭의 두번째 끝좌석인 경우
										// 좌->우 정렬이며, 이전 좌석이 예매 가능한 상태이고, 좌석을 선택하지 않은 경우
										if(seatLineSort == 0 && (backSeat)>=0 && seatListData[backSeat].seatStatus=='00' && !seatListData[backSeat].isSelected){
											isSelectable=false;
										} else if(seatLineSort == 1 && (nextSeat)<=(seatListData.length-1) && seatListData[nextSeat].seatStatus=='00' && !seatListData[nextSeat].isSelected) {
										// 우->좌 정렬이며, 다음 좌석이 예매 가능한 상태이고, 좌석을 선택하지 않은 경우
											isSelectable=false;
										}
									}
								} else { // 같이 블럭할 수 있는 최대수(영역)가 1~2석인 경우
									isSelectable=true;
								}

								// 앞에 그려진 좌석의 (그룹*블럭*좌석)타입이 동일하며, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
								if((backSeat)>=0 && seatListData[backSeat].seatGroup==seatGroup && seatListData[backSeat].seatLine==seatLine && seatListData[backSeat].seatAlloc==seatAlloc && ( seatListData[backSeat].seatStatus!='00' || seatListData[backSeat].isSelected ))
								{
									isSelectable=true;
								}
								// 뒤에 그려질 좌석의 (그룹*블럭*좌석)타입이 동일하며, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
								if((nextSeat)<=(seatListData.length-1) && seatListData[nextSeat].seatGroup==seatGroup && seatListData[nextSeat].seatLine==seatLine && seatListData[nextSeat].seatAlloc==seatAlloc && ( seatListData[nextSeat].seatStatus!='00' || seatListData[nextSeat].isSelected ))
								{
									isSelectable=true;
								}
							} else if(seatPaxCnt > 0 && seatPaxCnt <= seatLineCnt){ // 붙임좌석설정이 1이상이고, 블럭할수 있는 최대수가 붙임좌석설정값보다 클 경우
								if(seatLineSort == 0){ // 좌->우 정렬
									var check=false;
									// 블럭의 마지막 좌석이고. 상태가 예매 가능이며, 선택되어있지 않은 경우
									if(seatCount==(nextSeat))
									{
										var totCnt=j-seatPaxCnt;
										// (현재 좌석 idx - 좌석붙임설정 갯수)가 0크거나 같을 경우
										if(totCnt>=0){
											// 우->좌 방향으로 붙임으로 선택 가능 한 좌석 수만큼 좌석 상태 확인하기
											for(var k=(backSeat);k>totCnt;k--)
											{
												// (그룹*블럭*좌석)타입이 각각 다르거나, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
												if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
														|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected){
													check = true;
													break;
												}
											}

											if(!check) {// 블럭할 수 있는 좌석이 존재할 경우.
												isSelectable = true;
												// 같은블럭내 정렬값 변경. 우->좌
												seatLineSort=1;
											}
										}
									}else{
										if(seatListData.length>=(j+seatPaxCnt)){
											// 좌->우 방향으로 붙임으로 선택 가능 한 좌석 수만큼 좌석 상태 확인하기
											for(var k=(nextSeat);k<(j+seatPaxCnt);k++){
												// (그룹*블럭*좌석)타입이 동일하며, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
												if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
														|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected){
													isSelectable = false;
													break;
												}
											}
										}else if(seatListData.length<(j+seatPaxCnt)){	// 좌->우 방향으로 선택할 수 있는 좌석이 모자를 경우
											isSelectable = false;
										}
									}
								} else { // 우->좌 정렬
									// 블럭의 첫번째 좌석인경우
									if(j==i){
										var totCnt=j+seatPaxCnt;
										// 전체좌석 보다 (현재 좌석 idx + 좌석붙임설정 갯수) 값이 작을경우
										if(totCnt<=seatListData.length){
											for(var k=(nextSeat);k<totCnt;k++){
												// (그룹*블럭*좌석)타입이 각각 다르거나, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
												if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
														|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected){
													check = true;
													break;
												}
											}

											if(!check){ // 블럭할 수 있는 좌석이 존재할 경우.
												isSelectable = true;
												// 같은블럭내 정렬값 변경. 좌->우
												seatLineSort=0;
											}
										}
									}else{
										if((j-(seatPaxCnt-1)) < 0) { // 우->좌 방향으로 선택할 수 있는 좌석이 모자를 경우
											isSelectable = false;
										} else {
											// 우->좌 방향으로 붙임으로 선택 가능 한 좌석 수만큼 좌석 상태 확인하기
											for(var k=(backSeat);k>(j-seatPaxCnt);k--){
												// (그룹*블럭*좌석)타입이 동일하며, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
												if(seatListData[k].seatAlloc!=seatAlloc || seatListData[k].seatLine!=seatLine
														|| seatListData[k].seatGroup!=seatGroup || seatListData[k].seatStatus!='00' || seatListData[k].isSelected){
													isSelectable = false;
													break;
												}
											}
										}
									}
								}
							} else {
								isSelectable = false;
							}

							// 붙힘 설정이 0보다 클경우
							if(seatPaxCnt > 0){
								// 비선택 좌석 선택 가능여부 채크하기.
								if(!isSelectable && seatPaxCnt > 1){
									isSelectable = true;
									var tempLineSort = -1;
									if(seatLineSort == 0){ // 좌->우 정렬
										// (그룹*블럭*좌석)타입이 각각 다르거나, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
										if(seatListData[backSeat].seatAlloc==seatAlloc && seatListData[backSeat].seatLine==seatLine
												&& seatListData[backSeat].seatGroup==seatGroup && seatListData[backSeat].seatStatus=='00' && !seatListData[backSeat].isSelected){
											// 같은블럭내 정렬값 변경. 우->좌
											tempLineSort=1;
										}else if(seatListData.length > nextSeat && seatListData[nextSeat].seatAlloc==seatAlloc && seatListData[nextSeat].seatLine==seatLine
												&& seatListData[nextSeat].seatGroup==seatGroup && seatListData[nextSeat].seatStatus=='00' && !seatListData[nextSeat].isSelected){
											// 같은블럭내 정렬값 변경. 좌->우
											tempLineSort=0;
										}
									}else{
										// (그룹*블럭*좌석)타입이 각각 다르거나, 상태가 예매 불가 이거나, 이미 선택된 좌석인 경우
										if(seatListData.length > nextSeat && seatListData[nextSeat].seatAlloc==seatAlloc && seatListData[nextSeat].seatLine==seatLine
												&& seatListData[nextSeat].seatGroup==seatGroup && seatListData[nextSeat].seatStatus=='00' && !seatListData[nextSeat].isSelected){
											// 같은블럭내 정렬값 변경. 좌->우
											tempLineSort=0;
										}else if(seatListData[backSeat].seatAlloc==seatAlloc && seatListData[backSeat].seatLine==seatLine
												&& seatListData[backSeat].seatGroup==seatGroup && seatListData[backSeat].seatStatus=='00' && !seatListData[backSeat].isSelected){
											// 같은블럭내 정렬값 변경. 우->좌
											tempLineSort=1;
										}
									}
									seatLineSort = tempLineSort;
								}

								// 4인 소파, 2인소파, 발코니 등 예외 좌석 처리
								if(ticket){
									isSelectable = selectSeatCheck(seatListData[j]);
								}

								// 키즈존(86)이 존재하는 경우
								if(BookingSeatDatas.kidSeatCheck && isSelectable){
									isSelectable = selectKidSeatCheck(seatListData[j]);
								}
							}
						}
					}
				}

				if(isSelectable) seatActive = true;
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

		function selectKidSeatCheck(seatItem){
			var isSelectable = false;

			// 선택한 전체티켓수
			var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
			var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();

			// 선택한 어린이 전체티켓수
			var kidTicket = null;
			var totalCnt = 0;
			var useCnt = 0;

			$.each(BookingSeatDatas.kidTicketCodes, function(i, key){
				kidTicket = BookingSeatTicketDatas.ticketTypeCodes[key];
				if(kidTicket){
					totalCnt = kidTicket.totalCnt;
					useCnt = kidTicket.useCnt;
					return false;
				}
			});

			// 어린이 좌석 외 선택 가능한 좌석수
			var isSelectedSeatCount = (totalSelectedTicket-totalCnt)-(selectedSeatCount-useCnt);

			if(seatItem.seatAlloc==BookingSeatDatas.kidSeat && (totalCnt-useCnt) > 0){
				isSelectable = true;
			}else if(seatItem.seatAlloc!=BookingSeatDatas.kidSeat && isSelectedSeatCount > 0){
				isSelectable = true;
			}

			return isSelectable;
		}

		// 4인 소파, 2인소파, 발코니 등 예외 좌석 처리
		function selectSeatCheck(seatItem){
			var priceEtc = BookingSeatDatas.ticketPriceEtcArry[seatAlloc];

			// 선택한 전체티켓수
			var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
			var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();
			var isSelectedSeatCount = totalSelectedTicket - selectedSeatCount;	// 선택 가능한 좌석수

			// 선택한 전체티켓수
			var normalTicket = BookingSeatTicketDatas.ticketTypeCodes[normalTicketCode];
			var totalCnt = 0;
			var useCnt = 0;
			if(normalTicket){
				totalCnt = BookingSeatTicketDatas.ticketTypeCodes[normalTicketCode].totalCnt;
				useCnt = BookingSeatTicketDatas.ticketTypeCodes[normalTicketCode].useCnt;
			}
			var isSeatCount = totalSelectedTicket - selectedSeatCount;	// 선택 가능한 좌석수

			var isSelectable = false;
			if(priceEtc.selectSeat){
				// 좌석타입별 선택 가능 수량 확인
				var seatPaxCnt = BookingSeatDatas.seatPaxCountCheck(seatAlloc);

				// 선택가능 매수인 경우
				if(priceEtc.selectType == 'multiple' && priceEtc.selectSeat[seatPaxCnt]){
					// 셀렉트 타입이 multiple 이면 붙임 설정으로 선택 가능 한 좌석~ OPEN
					isSelectable = true;
				}else if(priceEtc.selectType == 'fixed' && !fixedTicketCheck() && priceEtc.selectSeat[totalCnt] && priceEtc.selectSeat[isSeatCount]){
					// 셀렉트 타입이 fixed 이면, 권종이 일반인 총 티켓수가 선택가능 수이며, 선택 가능 좌석이 있을 경우 OPEN
					// 다른 권종이 선택되어 있을 경우 false
					isSelectable = true;
				}
			}else{
				isSelectable = true;
			}
			return isSelectable;
		}

		function fixedTicketCheck(){
			var flag = false;
			$.each(BookingSeatTicketDatas.ticketTypeCodes, function(k, item){
				if(k!=normalTicketCode) flag = true;
			});
			return flag;
		}

		// 좌석 HTML
		function seatHtml(data, isSelectable, seatLineSort, idx, self)
		{
			var seatHtml="";
			var seatId = BookingSeatDatas.seatIdGen(data.seatGroup, data.seatNo, seatLineSort, idx, data.seatAllocType);

			var seatLengthX = (data.seatLenX >= 160) ? data.seatLenX / 10 : 16;
			var seatLengtyY = (data.seatLenY >= 160) ? data.seatLenY / 10 : 16;

			var seatTitle = '';
			var seatClass = '';
			var onclickEvent = '';
			var onmouseoverEvent = '';
			var onmouseoutEvent = '';
			var onkeyupEvent = '';
			var onkeypressEvent = '';
			var onblurEvent = '';
			var seatClass = "seat_normal";	// 좌석상태이미지
			if (data.seatStatus != '00' || data.seatTrouble == 'Y') { // 예매된 좌석이거나 고장난 좌석인 경우
				seatTitle = '선택불가';
				seatClass = 'seat_done';
				onclickEvent = 'BookingSeatDatas.checkSeatPopup("'+data.seatAllocType+'","'+data.seatGroup+'",["'+data.seatNo+'"])';
			}else{
				if (data.isSelected) { // 선택된 좌석
					seatTitle = '선택됨';
					seatClass = 'seat_selected';
					onclickEvent = 'BookingSeatDatas.deselectSeat("'+data.seatGroup+'","'+data.seatNo+'")';
				}else{
					if (!isSelectable){ // 선택되지 않은 좌석
						seatTitle = '예매가능/선택불가';
						seatClass = 'seat_cant';
						onclickEvent = 'BookingSeatDatas.cantMeassge()';
					}else{
						// 장애인석/휠체어석
						if(data.seatAlloc==BookingSeatDatas.disabledSeat){
							seatClass = 'ico_wheel';
						}else if(data.seatAlloc == '71') { //로얄석
							seatClass = seatClass+' seat_royal';
						}else if(data.seatAlloc == BookingSeatDatas.coupleSeat) { //커플석
							seatClass = seatClass+' seat_couple';
						}else if(data.seatAlloc == '73') { //골드석
							seatClass = seatClass+' seat_gold';
						}else if(data.seatAlloc == '74') { //VIP석
							seatClass = seatClass+' seat_vip';
						}else if(data.seatAlloc == '83') { //4인소파
							seatClass = seatClass+' sofa_4';
						}else if(data.seatAlloc == '84') { //2인소파
							seatClass = seatClass+' sofa_2';
						}else if(data.seatAlloc == '82') { //스위트룸
							seatClass = seatClass+' sofa_sweet';
						}else if(data.seatAlloc == '76') { //테이블석
							seatClass = seatClass+' seat_table';
						}else if(data.seatAlloc == '85') { //발코니
							seatClass = seatClass+' seat_sofa_bcnM';
						}else if(data.seatAlloc == BookingSeatDatas.kidSeat) { //키즈존
							seatClass = seatClass+' seat_kids';
						}else if(data.seatAlloc == '87') { //발코니디럭스M
							seatClass = seatClass+' seat_sofa_bcnDM';
						}else if(data.seatAlloc == '88') { //스페셜존
							seatClass = seatClass+' seat_special';
						}else{ //일반
							seatClass = seatClass;
						}

						// 좌석이름
						seatTitle = BookingSeatDatas.seatTypes[data.seatAlloc];

						// 이벤트 정리
						onmouseoverEvent = 'BookingSeatDatas.seatMouseOver("'+seatId+'")';
						onmouseoutEvent = 'BookingSeatDatas.seatMouseOut("'+seatId+'")';

						if(browser.indexOf('chrome') > -1)	{
							onkeypressEvent = 'BookingSeatDatas.seatMouseOver("'+seatId+'")';
							// 장애인석/휠체어석의 경우
							if(data.seatAlloc==BookingSeatDatas.disabledSeat){
								onkeyupEvent = 'BookingSeatDatas.seatMouseOver("'+seatId+'")';
							}
						}else{
							onkeyupEvent = 'BookingSeatDatas.seatMouseOver("'+seatId+'")';
							onkeypressEvent = 'BookingSeatDatas.checkSeat(this)';
							onblurEvent = 'BookingSeatDatas.seatMouseOut("'+seatId+'")';
						}
						onclickEvent = 'BookingSeatDatas.checkSeat(this)';
					}
				}
			}

			seatHtml += '<button type="button" title="' + data.seatGroup + data.seatNo + '(' + seatTitle + ')" id="' + seatId + '" class="' + seatClass + '"'
						+ ' type="button" seatGroup="' + data.seatGroup + '" seatNo="' + data.seatNo + '" seatType="' + data.seatAlloc + '" popupYn="' + data.popupYn + '"'
						+ ' seatLineCnt = "' + data.seatLineCnt + '"'
						+ ' style="width: ' + seatLengthX + 'px; height: ' + seatLengtyY + 'px; left: ' + (seatPosX + OFFSET_X_PLUS) + 'px; top: ' + seatPosY + 'px;"'
						+ ' onmouseover=\'' + onmouseoverEvent + '\''
						+ ' onmouseout=\'' + onmouseoutEvent + '\''
						+ ' onclick=\'' + onclickEvent + '\''
						+ ' onkeyup=\'' + onkeyupEvent + '\''
						+ ' onblur=\'' + onblurEvent + '\''
						+ ' onkeypress=\'' + onkeypressEvent + '\''
						+ '>' + data.seatNo + '</button>';

			return seatHtml;
		}

		seatWrapWidth  = largestSeatPosX + SEAT_WIDTH + SEAT_GROUP_WIDTH + 3;
		seatWrapHeight = largestSeatPosY + SEAT_HEIGHT;

		$('#seatPositionList').css({width: seatWrapWidth, height: seatWrapHeight});
		$('#seatPositionList').html(htmlTxt);

		// 선택할 좌석이 없을 경우
		if(!seatActive && BookingSeatDatas.seatPaxCount != 0){
			var seatMessage = '남은 티켓매수로 선택가능한 좌석이 없습니다.';
			// 단독 선택 좌석인 경우
			if(BookingSeatDatas.exclusiveSeatType=='Y' && BookingSeatDatas.seatTypes[BookingSeatDatas.exclusiveSeatType]){
				var ticketTypeNames = '';
				$.each(BookingSeatTicketDatas.ticketTypeCodes, function(i, item){
					if(item.totalCnt > item.useCnt){
						if(ticketTypeNames) ticketTypeNames += ', ';
						ticketTypeNames += BookingSeatDatas.ticketPricesSummary[i];
					}
				});
				// ex) 청소년은 커플석을 선택하실 수 없습니다.
				if(ticketTypeNames) seatMessage = ticketTypeNames+'은 '+BookingSeatDatas.seatTypes[BookingSeatDatas.exclusiveSeatType] + '을 선택하실 수 없습니다.';
			}
			messageBox(seatMessage);
		}
	},

	// 좌석 아이디 만들기
	seatIdGen : function (seatGroup, seatNo, seatLineSort, index, seatAllocType) {
		return "seat_" + seatGroup + "_" + seatNo + "_" + seatLineSort + "_" + index + "_" + seatAllocType;
	},

	// 좌석타입별 선택 가능 수량 확인
	seatPaxCountCheck : function (seatType){
		var seatPaxCnt = 0;
		if(BookingSeatDatas.seatPaxCount==0) return 0;
		// 전체티켓수
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
		// 장애인석의 경우 좌석 타입을 일반으로 변경하여 티켓을 할당함
		if(seatType==BookingSeatDatas.disabledSeat) seatType = BookingSeatDatas.normalSeat;
		// 좌석별 선태된 권종 정보
		var checkSeat = BookingSeatDatas.checkSeatArry[seatType];
		if(!checkSeat) return -1;
		if(BookingSeatDatas.coupleSeat == seatType){
			seatPaxCnt = (totalSelectedTicket%2)!=0? -1 : ((checkSeat.maxCnt-checkSeat.selected) >= 2 ? 2 : -1);
		}else{
			seatPaxCnt = checkSeat.maxCnt > checkSeat.selected ? (checkSeat.maxCnt-checkSeat.selected) >= 2 ? 2 : 1 : -1;
		}
		return BookingSeatDatas.seatPaxCount > seatPaxCnt ? seatPaxCnt : BookingSeatDatas.seatPaxCount;
	},

	// 좌석 확인
	checkSeat : function(obj){
		var $this = $(obj);
		// 선택한 전체티켓수
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
		var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();
		var seatType = $this.attr('seatType');
		// 좌석타입별 선택 가능 수량 확인
		var seatPaxCnt = BookingSeatDatas.seatPaxCountCheck(seatType);
		var isSelectedSeatCount = totalSelectedTicket - (selectedSeatCount + seatPaxCnt);	// 선택 가능한 좌석수

		if (totalSelectedTicket == 0) {
			messageBox('인원을 먼저 선택해주세요.');
			return;
		}

		if (totalSelectedTicket == selectedSeatCount) {
			messageBox('좌석 선택이 완료되었습니다.');
			return;
		}

		// 선택불가 좌석일 경우
		if($this.hasClass('seat_cant')){
			messageBox('옆좌석을 선택해주세요.');
			return;
		}

		if (totalSelectedTicket < isSelectedSeatCount) {
			var remainingSeat = totalSelectedTicket - selectedSeatCount;
			messageBox('선택가능한 좌석수가 초과되었습니다.\n현재 선택 가능한 좌석수는 ' + remainingSeat + '석 입니다.');
			return;
		}

		// 좌석 선택 및 티켓 할당
		var group = new Date().getTime();
		var seatArry = [];
		$.each(BookingSeatDatas.tmpSelectedSeats, function(i, item){
			if(item.seatStatus=='seat_selected') return true;
			var tmpSeatId = item.id;
			var seatArr = tmpSeatId.split('_');	// seatArr (1:seatGroup, 2:seatNo, 3:seatLineSort, 4:index)
			var index = seatArr[4];
			var seatGroup = BookingSeatDatas.seatList[index].seatGroup;
			var seatNo = BookingSeatDatas.seatList[index].seatNo;
			var seatAllocType = BookingSeatDatas.seatList[index].seatAllocType;
			var popupYn = BookingSeatDatas.seatList[index].popupYn;
			var exclusiveYn = BookingSeatDatas.seatList[index].exclusiveYn;

			BookingSeatDatas.seatList[index].isSelected = true;

			var seatInfo = {};
			seatInfo.seatId = tmpSeatId;
			seatInfo.group = group;
			seatInfo.seatIndex = index;
			seatInfo.seatGroup = seatGroup;
			seatInfo.seatNo = seatNo;
			seatInfo.popupYn = popupYn;
			seatInfo.seatType = seatAllocType;
			seatInfo.cnt = 1;

			// 선택좌석정보에 추가
			BookingSeatDatas.selectedSeats.push(seatInfo);

			// 좌석 안내 팝업 체크
			if(popupYn=='Y' || (!BookingSeatDatas.preferentialSeatPopup && seatType==BookingSeatDatas.disabledSeat)) seatArry.push(seatNo);

			if(BookingSeatDatas.ticketPriceEtcArry[seatAllocType] && BookingSeatDatas.ticketPriceEtcArry[seatAllocType].exclusiveYn!='N'){
				BookingSeatDatas.exclusiveType = BookingSeatDatas.ticketPriceEtcArry[seatAllocType].exclusiveYn;
				BookingSeatDatas.exclusiveSeatType = seatAllocType;
				BookingSeatDatas.exclusiveSeatGroup = BookingSeatDatas.ticketPriceEtcArry[seatAllocType].groupCode;
			}
		});
		// 티켓 셋팅
		BookingSeatDatas.selectedSeatInfoItems =[];
		// 티켓 설정이 안된 경우 안내팝업 호출 안하도록 seatArry에서 값 삭제
		seatArry = BookingSeatDatas.assigningSeatsTotickets(seatArry);
		// 임시 좌석 정보 초기화
		BookingSeatDatas.tmpSelectedSeats = [];
		// 현황판에 좌석 표기
		this.drowDisplaySeat();
		// 장애인석의 경우 안내팝업 호출!
		if((!BookingSeatDatas.preferentialSeatPopup && seatType==BookingSeatDatas.disabledSeat) || seatArry.length > 0){
			this.checkSeatPopup(seatType, $this.attr('seatGroup'), seatArry);
		}
		// 좌석별 권종 초기화
		BookingSeatDatas.checkSeatCntCheck();
		// 선택한 붙임설정에 따라 활성화처리
		isSelectedSeatCount = totalSelectedTicket-BookingSeatDatas.getSelectedSeatCount();
		BookingSeatDatas.seatPaxCount = isSelectedSeatCount > 1 ? 2 : (isSelectedSeatCount < 0) ? 0: isSelectedSeatCount;
		BookingSeatDatas.draw();
	},

	// 좌석에 티켓 할당
	assigningSeatsTotickets: function(seatArry){
		// 할당된 티켓 정보 초기화
		$.each(BookingSeatTicketDatas.ticketTypeCodes, function(i, item){
			item.useCnt = 0;
		});

		// 장애인석 정보 가져오기
		var preferentialTicket = BookingSeatTicketDatas.ticketTypeCodes[BookingSeatDatas.preferentialTicketType];

		// 좌석 정보 초기화
		$.each(BookingSeatDatas.selectedSeats, function(i, seatInfo){
			seatInfo.ticketCode = null;
			seatInfo.ticketTypeCode = null;
			seatInfo.amt = null;
			seatInfo.cnt = null;
			seatInfo.seatCnt = null;
			var seatType = seatInfo.seatType;
			// 장애인석의 경우 우대 선택되어 있으면 우대로~ 선택 되도록 함
			if(seatType==BookingSeatDatas.disabledSeat && preferentialTicket && preferentialTicket.totalCnt > preferentialTicket.useCnt){
				seatInfo = BookingSeatDatas.assignSeatInTickets2(seatInfo, BookingSeatDatas.normalSeat, BookingSeatDatas.preferentialTicketType, false, false);
			}
		});

		// 티켓 할당
		$.each(BookingSeatDatas.ticketPricesSort, function(i, ticketInfo){
			var ticketType = ticketInfo.ticketTypeCode;
			var ticketTypeInfo = BookingSeatTicketDatas.ticketTypeCodes[ticketType];
			if(!ticketTypeInfo || ticketTypeInfo.totalCnt <= ticketTypeInfo.useCnt){
				return true;
			}
			BookingSeatDatas.assignSeatInTickets(ticketTypeInfo, ticketInfo);
		});

		// 할당 안된 좌석 확인
		var removeSeats = [];
		var coupleRemoveSeats = [];
		$.each(BookingSeatDatas.selectedSeats, function(i, seatInfo){
			if(!seatInfo.ticketCode){
				// 할당된 티켓이 없을 경우, 할당한 좌석의 권종에 요금이 있을 경우 요금을 바꿔치기한다.
				seatInfo = BookingSeatDatas.changeAssignSeatInTickets(seatInfo, i);
				if(!seatInfo.ticketCode){
					// 커플석인경우 좌석이 속한 그룹을 해제하기 위해 다른 좌석과 분리한다.
					if(BookingSeatDatas.coupleSeat==seatInfo.seatType){
						coupleRemoveSeats.push(seatInfo.group);
					}else{
						// 삭제할 좌석정보 담기
						seatInfo.index = i;
						removeSeats.push(seatInfo);
					}
				}
			}
		});

		// 티켓을 할당 못한 좌석 삭제
		if((coupleRemoveSeats && coupleRemoveSeats.length > 0) || (removeSeats && removeSeats.length > 0)){
			// 할당된 티켓이 없을 경우 좌석 해제한다.
			$.each(removeSeats, function(i, seatInfo){
				seatArry.splice(seatArry.indexOf(seatInfo.seatNo), 1);
				BookingSeatDatas.selectedSeats.splice((seatInfo.index-i), 1);
				BookingSeatDatas.seatList[seatInfo.seatIndex].isSelected = false;
			});
			// 커플석이 할당 안됬을 경우, 짝궁석도 할당 해제한다.
			$.each(coupleRemoveSeats, function(i, group){
				// 삭제할 좌석이 속한 그룹 해제
				seatArry = BookingSeatDatas.deselectGroupSeat(group, seatArry);
			});
			messageBox('선택하신 좌석 중 요금을 할당할 수 없는 좌석이 있습니다.');
		}

		// 할당된 티켓이 없는 경우
		if(BookingSeatDatas.selectedSeats==0){
			BookingSeatDatas.exclusiveInit(); // 단독선택 초기화
		}
		return seatArry;
	},

	assignSeatInTickets: function(ticketTypeInfo, ticketInfo){
		$.each(BookingSeatDatas.selectedSeats, function(j, seatInfo){
			var seatType=seatInfo.seatType;
			// 장애인석의 경우 좌석 타입을 일반으로 변경하여 티켓을 할당함
			if(seatType==BookingSeatDatas.disabledSeat) seatType = BookingSeatDatas.normalSeat;
			if(seatInfo.ticketCode){
				return true;
			}
			if(ticketInfo.seatType == seatType){
				// 선택 가능 수 확인
				seatInfo.ticketCode = ticketInfo.ticketCode;
				seatInfo.ticketTypeCode = ticketInfo.ticketTypeCode;
				seatInfo.amt = ticketInfo.amt;

				var seatCnt = BookingSeatDatas.getSeatCnt(ticketInfo);
				seatInfo.cnt = 1;
				seatInfo.seatCnt = seatCnt;

				// 현황판에 보여줄 좌석 정보
				var selectedSeatInfo = BookingSeatDatas.getSelectedSeatInfoItem(ticketInfo.ticketCode);
				if(selectedSeatInfo){
					selectedSeatInfo.count += 1;
					selectedSeatInfo.seatCnt += seatCnt;
				}else{
					BookingSeatDatas.selectedSeatInfoItems.push({'code': ticketInfo.ticketCode, 'name': ticketInfo.displayName, 'amt': ticketInfo.amt, 'count': 1, 'seatCnt': seatCnt, 'cntTypeName' : ticketInfo.cntTypeName});
				}
				ticketTypeInfo.useCnt += 1;
				if(ticketTypeInfo.totalCnt==ticketTypeInfo.useCnt){
					return false;
				}
				return true;
			}
		});
	},

	assigningSeatsTotickets2: function(seatArry){
		// 할당된 티켓 정보 초기화
		$.each(BookingSeatTicketDatas.ticketTypeCodes, function(i, item){
			item.useCnt = 0;
		});
		// 장애인석 정보 가져오기
		var preferentialTicket = BookingSeatTicketDatas.ticketTypeCodes[BookingSeatDatas.preferentialTicketType];

		// 티켓 할당을 위하여 좌석타입이 높은순으로 정렬
		BookingSeatDatas.selectedSeats.sort(function(a,b){
			return a.seatType > b.seatType ? -1: a.seatType < b.seatType ? 1 : 0;
		});

		// 좌석 정보 초기화
		$.each(BookingSeatDatas.selectedSeats, function(i, seatInfo){
			seatInfo.ticketCode = null;
			seatInfo.ticketTypeCode = null;
			seatInfo.amt = null;
			seatInfo.cnt = null;
			seatInfo.seatCnt = null;
			// 장애인석의 경우 우대 선택되어 있으면 우대로~ 선택 되도록 함
			if(seatInfo.seatType==BookingSeatDatas.disabledSeat && preferentialTicket && preferentialTicket.totalCnt > preferentialTicket.useCnt){
				seatInfo = BookingSeatDatas.assignSeatInTickets2(seatInfo, BookingSeatDatas.normalSeat, BookingSeatDatas.preferentialTicketType, false, false);
			}
		});

		// 티켓 할당
		var removeSeats = [];
		var coupleRemoveSeats = [];
		$.each(BookingSeatDatas.selectedSeats, function(i, seatInfo){
			if(!seatInfo.ticketCode){
				seatInfo = BookingSeatDatas.assignSeatInTickets2(seatInfo, seatInfo.seatType, null, false, false);
				if(!seatInfo.ticketCode){
					// 할당된 티켓이 없을 경우, 할당한 좌석의 권종에 요금이 있을 경우 요금을 바꿔치기한다.
					seatInfo = BookingSeatDatas.changeAssignSeatInTickets(seatInfo, i);
					if(!seatInfo.ticketCode){
						// 커플석인경우 좌석이 속한 그룹을 해제하기 위해 다른 좌석과 분리한다.
						if(BookingSeatDatas.coupleSeat==seatInfo.seatType){
							coupleRemoveSeats.push(seatInfo.group);
						}else{
							// 삭제할 좌석정보 담기
							seatInfo.index = i;
							removeSeats.push(seatInfo);
						}
					}
				}
			}
		});

		// 티켓을 할당 못한 좌석 삭제
		if((coupleRemoveSeats && coupleRemoveSeats.length > 0) || (removeSeats && removeSeats.length > 0)){
			// 할당된 티켓이 없을 경우 좌석 해제한다.
			$.each(removeSeats, function(i, seatInfo){
				seatArry.splice(seatArry.indexOf(seatInfo.seatNo), 1);
				BookingSeatDatas.selectedSeats.splice((seatInfo.index-i), 1);
				BookingSeatDatas.seatList[seatInfo.seatIndex].isSelected = false;
			});
			// 커플석이 할당 안됬을 경우, 짝궁석도 할당 해제한다.
			$.each(coupleRemoveSeats, function(i, group){
				// 삭제할 좌석이 속한 그룹 해제
				seatArry = BookingSeatDatas.deselectGroupSeat(group, seatArry);
			});
			messageBox('선택하신 좌석 중 요금을 할당할 수 없는 좌석이 있습니다.');
		}
		return seatArry;
	},

	// 할당된 티켓 변경
	changeAssignSeatInTickets: function(seatInfo, i){
		$.each(BookingSeatDatas.selectedSeats, function(j, changeSeatInfo){
			if(i!=j && changeSeatInfo.seatType != seatInfo.seatType && changeSeatInfo.ticketTypeCode){
				// 변경 가능한 티켓인지 확인
				var ticketPrices = BookingSeatDatas.checkChangeTicket(seatInfo.seatType, changeSeatInfo.ticketTypeCode);
				if(!ticketPrices) return true;
				// 변경할 티켓이 있는지 확인
				changeSeatInfo = BookingSeatDatas.assignSeatInTickets2(changeSeatInfo, changeSeatInfo.seatType, null, changeSeatInfo.ticketCode, changeSeatInfo.ticketTypeCode);
				if(changeSeatInfo.change){
					// 선택 가능 수 확인
					seatInfo.ticketCode = ticketPrices.ticketCode;
					seatInfo.ticketTypeCode = ticketPrices.ticketTypeCode;
					seatInfo.amt = ticketPrices.amt;

					var seatCnt = BookingSeatDatas.getSeatCnt(ticketPrices);
					seatInfo.cnt = 1;
					seatInfo.seatCnt = seatCnt;

					// 현황판에 보여줄 좌석 정보
					var selectedSeatInfo = BookingSeatDatas.getSelectedSeatInfoItem(ticketPrices.ticketCode);
					if(selectedSeatInfo){
						selectedSeatInfo.count += 1;
						selectedSeatInfo.seatCnt += seatCnt;
					}else{
						BookingSeatDatas.selectedSeatInfoItems.push({'code': ticketPrices.ticketCode, 'name': ticketPrices.displayName, 'amt': ticketPrices.amt, 'count': 1, 'seatCnt': seatCnt, 'cntTypeName' : ticketPrices.cntTypeName});
					}
					return false;
				}
			}
		});
		return seatInfo;
	},

	// 변경 가능한 티켓정보 확인 및 값 리턴함
	checkChangeTicket: function(seatType, ticketTypeCode){
		var ticketPrices = null;
		// 티켓 수량 확인
		var ticket = BookingSeatTicketDatas.ticketTypeCodes[ticketTypeCode];
		if(ticket.totalCnt < ticket.useCnt){
			return ticketPrices;
		}
		// 요금 있는지 확인
		var ticketList = BookingSeatDatas.ticketTypeArry[seatType];
		$.each(ticketList, function(i, item){
			if(item.ticketTypeCode==ticketTypeCode){
				ticketPrices = item;
				return false;
			}
		});
		return ticketPrices;
	},

	// 티켓 할당
	assignSeatInTickets2: function(seatInfo, seatType, ticketType, oldTicketCode, oldTicketType){
		// 장애인석의 경우 좌석 타입을 일반으로 변경하여 티켓을 할당함
		if(seatType==BookingSeatDatas.disabledSeat) seatType = BookingSeatDatas.normalSeat;

		var changeTicket = true;
		var ticket = null;
		if(ticketType) {
			changeTicket = false;
			ticket = BookingSeatTicketDatas.ticketTypeCodes[ticketType];
		}

		$.each(BookingSeatDatas.ticketPricesSort, function(i, item){
			// 할당할 수 있는 권종이 있는 지 확인
			if(changeTicket){
				ticketType = item.ticketTypeCode;
				ticket = BookingSeatTicketDatas.ticketTypeCodes[ticketType];
				if(!ticket || ticket.totalCnt <= ticket.useCnt){
					return true;
				}
			}
			if(ticket && item.ticketTypeCode == ticketType && item.seatType == seatType){
				// 선택 가능 수 확인
				seatInfo.ticketCode = item.ticketCode;
				seatInfo.ticketTypeCode = item.ticketTypeCode;
				seatInfo.amt = item.amt;

				var seatCnt = BookingSeatDatas.getSeatCnt(item);
				seatInfo.cnt = 1;
				seatInfo.seatCnt = seatCnt;

				// 현황판에 보여줄 좌석 정보
				var selectedSeatInfo = BookingSeatDatas.getSelectedSeatInfoItem(item.ticketCode);
				if(selectedSeatInfo){
					selectedSeatInfo.count += 1;
					selectedSeatInfo.seatCnt += seatCnt;
				}else{
					BookingSeatDatas.selectedSeatInfoItems.push({'code': item.ticketCode, 'name': item.displayName, 'amt': item.amt, 'count': 1, 'seatCnt': seatCnt, 'cntTypeName' : item.cntTypeName});
				}
				ticket.useCnt += 1;
				if(oldTicketCode && oldTicketType) {
					// 변경한 티켓 정보 현황판 업데이트
					var selectedSeatInfo = BookingSeatDatas.getSelectedSeatInfoItem(oldTicketCode);;
					if(selectedSeatInfo && selectedSeatInfo.count > 0){
						selectedSeatInfo.count -= 1;
					}
					seatInfo.change = true;
				}
				return false;
			}
		});
		return seatInfo;
	},

	// 좌석별 선택 가능 수량 초기화
	checkSeatClear: function(){
		// 좌석 정보 초기화
		$.each(BookingSeatDatas.checkSeatArry, function(i, item){
			item.maxCnt = 0;
			item.selected = 0;
		});
	},

	// 좌석별 선택 가능 수량 확인
	checkSeatCntCheck: function(){
		// 좌석 정보 초기화
		$.each(BookingSeatDatas.checkSeatArry, function(i, item){
			item.selected = 0;
		});
		// 선택좌석 갱신
		$.each(BookingSeatDatas.selectedSeats, function(i, seatInfo){
			var seatType = seatInfo.seatType;
			// 장애인석의 경우 일반석으로 변경
			if(seatType==BookingSeatDatas.disabledSeat) seatType = BookingSeatDatas.normalSeat;
			BookingSeatDatas.checkSeatArry[seatType].selected += 1;
		});
	},

	// 현황판에 보여줄 좌석 정보
	getSelectedSeatInfoItem : function(ticketCode){
		var seatData = null;
		$.each(BookingSeatDatas.selectedSeatInfoItems, function(i, item){
			if(item.code == ticketCode){
				seatData = item;
				return false;
			}
		});
		return seatData;
	},

	// 선택한 좌석이 '석' 개념인 경우 ~ 1석-x명 표기를 위해 x명 값 구하는 로직
	getSeatCnt: function(item){
		// 선택한 전체티켓수
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
		var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();
		var isSelectedSeatCount = totalSelectedTicket - selectedSeatCount;	// 선택 가능한 좌석수

		var seatCnt = 1;
		if(item.cntTypeName == '석') {
			if(isSelectedSeatCount < item.maxSeatCnt) seatCnt = isSelectedSeatCount;
			else seatCnt = item.maxSeatCnt;
		}
		return seatCnt;
	},

	// 좌석선택해제
	deselectSeat: function(seatGroup, seatNo) {
		// 붙임설정 그룹 확인하기
		var group = null;
		for (var i=0; i < BookingSeatDatas.selectedSeats.length; i++) {
			if (BookingSeatDatas.selectedSeats[i].seatGroup === seatGroup && BookingSeatDatas.selectedSeats[i].seatNo === seatNo) {
				group = BookingSeatDatas.selectedSeats[i].group;
				break;
			}
		}

		// 삭제할 좌석이 속한 그룹 해제
		BookingSeatDatas.deselectGroupSeat(group, null);
		// 좌석별 권종 초기화
		BookingSeatDatas.checkSeatCntCheck();
		// 단독선택여부 해제
		if(BookingSeatDatas.selectedSeats && BookingSeatDatas.selectedSeats.length == 0){
			BookingSeatDatas.exclusiveInit();
		}
		// 현황판에 좌석 표기
		this.drowDisplaySeat();
		// 선택한 붙임설정에 따라 활성화처리
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
		var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();
		var isSelectedSeatCount = totalSelectedTicket-selectedSeatCount;
		BookingSeatDatas.seatPaxCount = isSelectedSeatCount > 1 ? 2 : (isSelectedSeatCount < 0) ? 0: isSelectedSeatCount;
		this.draw();
	},

	// 삭제할 좌석이 속한 그룹 해제
	deselectGroupSeat: function(group, seatArry){
		for (var i=(BookingSeatDatas.selectedSeats.length-1); i >= 0; i--) {
			if (BookingSeatDatas.selectedSeats[i].group === group) {
				var selectedSeatGroup = BookingSeatDatas.selectedSeats[i].seatGroup;
				var selectedSeatNo = BookingSeatDatas.selectedSeats[i].seatNo;
				BookingSeatDatas.seatList[BookingSeatDatas.selectedSeats[i].seatIndex].isSelected = false;
				var ticket = BookingSeatTicketDatas.ticketTypeCodes[BookingSeatDatas.selectedSeats[i].ticketTypeCode];
				if(ticket) ticket.useCnt -= 1;
				var ticketCode = BookingSeatDatas.selectedSeats[i].ticketCode;

				// 현황판에 보여줄 좌석 정보
				var selectedSeatInfo = BookingSeatDatas.getSelectedSeatInfoItem(ticketCode);;
				if(selectedSeatInfo && selectedSeatInfo.count > 0){
					selectedSeatInfo.count -= 1;
				}
				if(seatArry) seatArry.splice(seatArry.indexOf(selectedSeatNo), 1);
				BookingSeatDatas.selectedSeats.splice(i, 1); // 선택된좌석 배열에서 제거
			}
		}
		return seatArry;
	},

	// 현황판에 좌석 표기
	drowDisplaySeat : function(){
		BookingSeatDatas.selectedSeatsSort();
		// 예매현황 가격표시
		$(' #ticketTotalPrice').text(numberWithCommas(BookingSeatDatas.getTotalPrice()));
		// 현재 좌석선택한 티켓수 표시
		$('span#currentCountSelectedTicket').text(BookingSeatDatas.getSelectedSeatCount());
		// 티켓 정보 표기
		BookingSeatDatas.viewSeatData();
		//  좌표 표기
		var $selectedSeatNumbers1 = $('#selectedSeatNumbers1').empty();
		$.each(BookingSeatDatas.selectedSeats, function(i, item){
			var seatNum = item.seatGroup + item.seatNo;
			var htmlTxt = "<li data-seat-num=\"" + seatNum + "\">" + seatNum + "</li>";
			$selectedSeatNumbers1.append(htmlTxt);
		});
	},

	// 티켓 정보 표기
	viewSeatData : function(){
		// 금액이 높은 순으로 표기되도록~정렬
		BookingSeatDatas.selectedSeatInfoItems.sort(function(a, b){
		    return a.amt > b.amt ? -1 : a.amt < b.amt ? 1 : 0;
		});
		var $li = $('li#countSelectedByTicket').empty();
		$.each(BookingSeatDatas.selectedSeatInfoItems, function(i, item){
			var seatCntInfo = '';
			if(item.cntTypeName != '명' && item.seatCnt > 0) seatCntInfo = '-' + item.seatCnt + '명';
			if(item.count > 0) $li.append('<span>' + item.name + ' ' + item.count + item.cntTypeName + seatCntInfo +'&nbsp;</span>');
		});
	},

	// 선택한 티켓 가격 합계
	getTotalPrice: function() {
		var totalPrice = 0;
		$.each(BookingSeatDatas.selectedSeats, function(i, item){
			totalPrice += parseInt(item.amt, 10);
		});
		return totalPrice;
	},

	// 예매현황판에 보여줄 좌석번호 정렬
	selectedSeatsSort : function(){
		this.selectedSeats.sort(function(a, b) {
			// 좌석그룹으로 1차정렬
			if(a.seatGroup != b.seatGroup) return a.seatGroup > b.seatGroup ? 1 : a.seatGroup < b.seatGroup ? -1 : 0;
			else { //좌석번호로 2차정렬
				var p1 = parseInt(a.seatNo);
				var p2 = parseInt(b.seatNo);
				return p1 > p2 ? 1 : p1 < p2 ? -1 : 0;
			}
		});
	},

	checkSeatPopup: function(seatType, seatGroup, seatArry){
		if(!seatArry || typeof seatArry != 'object') return;
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkSeatPopup',
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenCode: BookingSeatDatas.movieData.screenCode,
				playDate: BookingSeatDatas.movieData.playDate,
				showSeq: BookingSeatDatas.movieData.showSeq,
				seatType: seatType,
				seatGroup: seatGroup,
				seatNo1: (seatArry[0] ? seatArry[0] : ''),
				seatNo2: (seatArry[1] ? seatArry[1] : ''),
				seatNo3: (seatArry[2] ? seatArry[2] : ''),
				seatNo4: (seatArry[3] ? seatArry[3] : ''),
				seatNo5: (seatArry[4] ? seatArry[4] : ''),
				seatNo6: (seatArry[5] ? seatArry[5] : ''),
				seatNo7: (seatArry[6] ? seatArry[6] : ''),
				seatNo8: (seatArry[7] ? seatArry[7] : ''),
				korEngGubun: '1',
				siteCode: '36'
			}
		}).success(function( data ) {
			if(data.resultCode === '0000'){
				openGuidePopup(data.result);
				if(seatType==BookingSeatDatas.disabledSeat) BookingSeatDatas.preferentialSeatPopup = true;
			}
		});
	},

	cantMeassge: function(){
		messageBox('옆좌석을 선택해주세요.');
		return;
	},

	// 좌석 마우스오버시 처리
	seatMouseOver: function(selectedSeatId) {
		var seatArr = selectedSeatId.split('_');	// seatArr (1:seatGroup, 2:seatNo, 3:seatLineSort, 4:index) ex. seat_A_1_1_0

		var seatGroup 	 = seatArr[1];
		var seatNo 		 = parseInt(seatArr[2]);
		var seatLineSort = seatArr[3];
		var index 		 = parseInt(seatArr[4]);
		var seatType 	 = parseInt(seatArr[5]);

		var isSelectable = false;

		// 선택한 전체티켓수
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
		var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();

		// 선택한 어린이 전체티켓수
		var kidTicket = null;
		var totalCnt = 0;
		var useCnt = 0;

		$.each(BookingSeatDatas.kidTicketCodes, function(i, key){
			kidTicket = BookingSeatTicketDatas.ticketTypeCodes[key];
			if(kidTicket){
				totalCnt = kidTicket.totalCnt;
				useCnt = kidTicket.useCnt;
				return false;
			}
		});

		// 어린이 좌석 외 선택 가능한 좌석수
		var isSelectedSeatCount = (totalSelectedTicket-totalCnt)-(selectedSeatCount-useCnt);

		// 좌석타입별 선택 가능 수량 확인
		var seatPaxCnt = BookingSeatDatas.seatPaxCountCheck(seatType);
		if(seatLineSort==-1) seatPaxCnt = 1;
		var seatLineCnt = $('#'+selectedSeatId).attr('seatLineCnt') ? $('#'+selectedSeatId).attr('seatLineCnt') : -1;
		if(seatLineCnt < seatPaxCnt) seatPaxCnt = seatLineCnt;

		for (var i=0; i < seatPaxCnt; i++) {
			var tmpSeatInfo = {};
			var nextIndex = index + i;
			var nextSeatNo = seatNo + i;
			var nextSort = 1;

			// 우->좌
			if (seatLineSort != 0) {
				nextIndex = index - i;
				nextSeatNo = seatNo - i;
				nextSort = 0;
			}

			var seatId     = BookingSeatDatas.seatIdGen(seatGroup, nextSeatNo, seatLineSort, nextIndex, seatType);

			if ($('#'+seatId).length == 0) {
				seatId = BookingSeatDatas.seatIdGen(seatGroup, nextSeatNo, nextSort, nextIndex, seatType);
			}

			var seatStatus = $('#'+seatId).attr('class');

			// 예매된 좌석이거나 고장난 좌석이거나, 선택 불가인 경우!!
			if(seatStatus=='seat_done' || seatStatus=='seat_selected' || seatStatus=='seat_cant'){
				break;
			}

			if ($('#'+seatId).length == 0) {
				continue;
			}

			tmpSeatInfo.id = seatId;
			tmpSeatInfo.seatStatus = seatStatus;
			tmpSeatInfo.popupYn = $('#'+seatId).attr("popupYn");

			$('#'+seatId).removeClass();
			$('#'+seatId).addClass('seat_selected');
			this.tmpSelectedSeats.push(tmpSeatInfo);

			// 장애인석 1석씩 선택가능
			if (seatType == BookingSeatDatas.disabledSeat) break;
			// 키즈존인 경우! 붙임 설정 확인
			if(BookingSeatDatas.kidSeatCheck){
				if(seatType==BookingSeatDatas.kidSeat && (totalCnt-useCnt) < seatPaxCnt){
					break;
				}else if(seatType!=BookingSeatDatas.kidSeat && isSelectedSeatCount < seatPaxCnt){
					break;
				}
			}
		}
	},

	// 좌석 마우스아웃시 처리
	seatMouseOut: function() {
		for (var i=0; i < BookingSeatDatas.tmpSelectedSeats.length; i++) {
			var seatArr = BookingSeatDatas.tmpSelectedSeats[i].id.split('_');
			//var seatNo = parseInt(seatArr[2]);
			var index = parseInt(seatArr[4]);

			if (BookingSeatDatas.seatList[index] && !BookingSeatDatas.seatList[index].isSelected) {
				$('#' + this.tmpSelectedSeats[i].id).removeClass();
				$('#' + this.tmpSelectedSeats[i].id).addClass(BookingSeatDatas.tmpSelectedSeats[i].seatStatus);
			}
		}

		BookingSeatDatas.tmpSelectedSeats = [];
	},

	// 현재 선택한 좌석수 가져오기
	getSelectedSeatCount: function() {
		var count = 0;
		$.each(BookingSeatDatas.selectedSeats, function(i, item){
			if (item.seatCnt) count += item.seatCnt;
		});
		return count;
	},

	getExitDrawHtml : function(){
		var htmlTxt = '';
		// 출구처리
		$.each(BookingSeatDatas.exitList, function(i, item){
			var exitPosX = (parseInt(item.posX) / 10);
			if (exitPosX <= 0) exitPosX = exitPosX - 15;
			else exitPosX = exitPosX + 25;
			var exitPosY = (parseInt(item.posY) / 10);
			var addClass = '';
			switch (item.direction) {
			case 'L':
				addClass = 'left';
				break;
			case 'R':
				addClass = 'right';
				break;
			case 'T':
				addClass = 'top';
				break;
			case 'B':
				addClass = 'bottom';
				break;
			case 'LR':
				addClass = 'left_right';
				break;
			case 'TB':
				addClass = 'top_bottom';
				break;
			}
			htmlTxt += '<span class="exit '+addClass+'" style="top:'+exitPosY+'px; left:'+exitPosX+'px;"/>';
		});
		return htmlTxt;
	},

	// 좌석선택완료 - 유효성 검사 및 로그인 확인
	nextStep: function(){
		// 중복클릭 막음
		if (BookingSeatDatas.dbClickBlock)	return;
		else BookingSeatDatas.dbClickBlock = true;

		// 선택한 전체티켓수
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
		if (totalSelectedTicket == 0) {
			return failPopup('인원을 먼저 선택해주세요.');
		}
		var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();
		if (selectedSeatCount == 0) {
			return failPopup('예매하고자 하는 좌석을 선택해주세요.');
		}
		if (totalSelectedTicket != selectedSeatCount) {
			return failPopup('예매하고자 하는 좌석수와 선택한 좌석수가 일치하지 않습니다.');
		}
		if(!BookingSeatDatas.kidTicketCheck()){
			return failPopup('어린이요금과 성인요금을 같이 선택하셔야 진행이 가능합니다.');
		}

		// 문화의날, 마티네데이 체크
		if(BookingSeatDatas.execlusiveSelPopupInfo != undefined && BookingSeatDatas.execlusiveSelPopupInfo.popupYn == 'Y' && $('#ticketTypeCode_'+BookingSeatDatas.execlusiveSelPopupInfo.ticketTypeCode).val() == 0){
			return confirmBox('알림', BookingSeatDatas.execlusiveSelPopupInfo.popupMsg
					, function(){
						// 중복클릭 막음
						BookingSeatDatas.dbClickBlock = false;
						BookingSeatTicketDatas.resetAll();
					},
					BookingSeatDatas.nextLoginCheck
					);
		}

		BookingSeatDatas.nextLoginCheck();

		function failPopup(msg){
			messageBox(msg);
			BookingSeatDatas.dbClickBlock = false;	// 중복클릭잠금 해제
			return false;
		}

	},

	// 좌석선택완료 - 블랙리스트 확인 및 거래번호 및 좌석 선점
	nextStep2 : function(gbn){
		var seatText = '';
		$.each(BookingSeatDatas.selectedSeats, function(i, item){
			seatText += item.seatGroup + ';' + item.seatNo + ';$';
		});
		var mobileNo = '';
		if(typeof gbn!='undefined' && gbn=='nonmember')
			mobileNo=String(BookingSeatDatas.nonmemberInfo.mobileNo1)+String(BookingSeatDatas.nonmemberInfo.mobileNo2)+String(BookingSeatDatas.nonmemberInfo.mobileNo3);


		var ajaxData = {
			'_command': 'Booking.completeSelectSeat',
			'siteCode': '36',
			'cinemaCode': BookingSeatDatas.movieData.cinemaCode,
			'screenCode': BookingSeatDatas.movieData.screenCode,
			'playDate': BookingSeatDatas.movieData.playDate,
			'showSeq': BookingSeatDatas.movieData.showSeq,
			'terminalCode': '77',
			'cnt' : BookingSeatDatas.getTotalTicketCnt(),
			'seatText': seatText,
			'mobileNo': mobileNo,
		}
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: ajaxData,
			dataType: 'json'
		}).success(function( data ) {
			if (data.resultCode === '0000') {
				if(data.continueCode!='0000'){
					messageBox(data.continueMessage);
				}
				BookingSeatDatas.movieData.transNo = data.transNo;
				$('.seat_text').popover('hide');// 붙임설정설명 가리기
				BookingPayment.showPage(data.transNo, BookingSeatDatas.movieData.screenCode, 'BOK', BookingSeatDatas.movieData.playDate, data.memberYn);
				$('#select_pay').modal('show');
			}else{
				messageBox(data.resultMessage);
			}
			BookingSeatDatas.dbClickBlock = false;	// 중복클릭잠금 해제
		});
	},

	// 선택된 티켓 수
	getTotalTicketCnt: function(){
		var count = 0;
		$.each(BookingSeatDatas.selectedSeats, function(i, item){
			if (item.cnt) count += item.cnt;
		});
		return count;
	},

	kidTicketCheck :function(){
		var flag = true;
		// 전체 관람가 영화의 경우 true
		var filmRating=BookingSeatStatusBoard.filmRating;
		if(filmRating=='0' || filmRating=='00') return flag;

		var kidCheck=false;
		var etcCheck=false;
//		var saleCheck=false;

		$('select[name^=ticketTypeCode_]').each(function(){
			var $this = $(this);
			var ticketTypeCode = $this.attr('ticketTypeCode');
			if($this.val() > 0){
				$.each(BookingSeatDatas.kidTicketCodes, function(i, val){
					if(val==ticketTypeCode) kidCheck = true;
				});
				$.each(BookingSeatDatas.adultTicketCodes, function(i, val){
					if(val==ticketTypeCode) etcCheck = true;
				});
//				$.each(BookingSeatDatas.saleTicketCodes, function(i, val){
//					if(val==ticketTypeCode) saleCheck = true;
//				});
			}
		});

		// 어린이 티켓이 존재하고, 어른을 선택 안했을 경우
		if(kidCheck)
//			if(!etcCheck && !saleCheck) flag = false;
			if(!etcCheck) flag = false;
		return flag;
	},

	// 좌석선점 해제
	releaseSeat: function() {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.releaseSeat',
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenCode: BookingSeatDatas.movieData.screenCode,
				playDate: BookingSeatDatas.movieData.playDate,
				showSeq: BookingSeatDatas.movieData.showSeq,
				terminalCode: '77',
				transNo: BookingSeatDatas.movieData.transNo,
				user: '36'
			}
		}).success(function( data ) {
			if (data.resultCode === '000') {
				// 좌석선택-예약현황화면 호출
				BookingSeatStatusBoard.showPage();
				BookingSeatTicketDatas.resetAll();
			} else {
			}
		});
	},

	nonmemberBooking: function() {

		var data = extractFormData('#nonmemberBookingPopup');

		if (validate('#nonmemberBookingPopup')) {

			if(data.name.length < 2){
				messageBox("이름은 최소 2글자 이상 입력해주세요.");
				$('#nonmemberBookingPopup input[name=name]').focus();
				return;
			}

			var pattern = /([^가-힣\x20])/i;
			if (pattern.test(data.name)) {
				messageBox("이름을 정확히 입력해주세요.");
				$('#nonmemberBookingPopup input[name=name]').focus();
				return;
			}

			if(data.birthday.trim().length != 6){
				messageBox( "생년월일을 정확히 입력해주세요.");
				$('#nonmemberBookingPopup input[name=birthday]').focus();
				return;

			} else if(Number(data.birthday.trim().substring(2,4))<1 || Number(data.birthday.trim().substring(2,4))>12 || Number(data.birthday.trim().substring(4,6)) > 31){
				messageBox( "생년월일을 정확히 입력해주세요.");
				this.isSaveCheck=false;
				return;
			}

			if(data.mobileNo && data.mobileNo.length < 10){
				messageBox("휴대폰번호를 확인해주세요.");
				$('#nonmemberBookingPopup input[name=mobileNo]').focus();
				return;
			}

//			if(data.mobileNo2 && (data.mobileNo2.length != 3 && data.mobileNo2.length != 4)){
//				messageBox("휴대폰번호를 확인해주세요.");
//				$('#nonmemberBookingPopupSub input[name=mobileNo2]').focus();
//				return;
//			}
//
//			if(data.mobileNo3 && data.mobileNo3.length != 4){
//				messageBox("휴대폰번호를 확인해주세요.");
//				$('#nonmemberBookingPopupSub input[name=mobileNo3]').focus();
//				return;
//			}

			if(!isMobile(data.mobileNo)){
				messageBox("휴대폰번호를 확인해주세요.");
				return;
			}

			if(data.password.length != 4){
				messageBox("비밀번호는 4자리로 입력해주세요.");
				$('#nonmemberBookingPopup input[name=password]').focus();
				return;
			}

			if(data.password != data.confirmPassword){
				messageBox( "비밀번호 재입력을 다시 확인해주세요.");
				$('#nonmemberBookingPopup input[name=confirmPassword]').focus();
				return;
			}

			if($("#nonmemberBookingPopup :input:radio[name=radio0]:checked").val() != 'Y') {
				messageBox("비회원 서비스 약관에 동의하시지 않을 경우\n 서비스를 이용하실 수 없습니다.");
				$('#nonmemberBookingPopupSub input:radio[name=radio0]').focus();
				return;
			} else {
				confirmBox("알림"
						, "티켓발권을 위해 입력하신 정보를 확인해주세요!\n" + data.name + "/ " + data.birthday + "\n" + data.mobileNo
						, function(){
							BookingSeatDatas.nonmemberBookingStep1();
						}
						, function(){
							return ;
						});

			}
		}
	},
	// 동의 후 다음으로 넘어가도록 하기 위한 메소드 분리
	nonmemberBookingStep1 : function(){

		var data = extractFormData('#nonmemberBookingPopup');

		// 디자인 변경으로 인한 전화번호 분리처리
		var mobileNo1 = '';
		var mobileNo2 = '';
		var mobileNo3 = '';
		if(data.mobileNo.length == 10){
			mobileNo1 = data.mobileNo.substring(0, 3);
			mobileNo2 = data.mobileNo.substring(3, 6);
			mobileNo3 = data.mobileNo.substring(6);
		} else {
			mobileNo1 = data.mobileNo.substring(0, 3);
			mobileNo2 = data.mobileNo.substring(3, 7);
			mobileNo3 = data.mobileNo.substring(7);
		}

		//비회원 이름에서 공백 제거
		BookingSeatDatas.nonmemberInfo.name = data.name ? data.name.replace(/ /g, '') : '';
		BookingSeatDatas.nonmemberInfo.birthday = data.birthday;
		BookingSeatDatas.nonmemberInfo.mobileNo1 = mobileNo1;
		BookingSeatDatas.nonmemberInfo.mobileNo2 = mobileNo2;
		BookingSeatDatas.nonmemberInfo.mobileNo3 = mobileNo3;
		BookingSeatDatas.nonmemberInfo.password = sha256_digest(data.password);

		// 좌석선점
		if (BookingNonSeat.nonSeatYn === 'Y') {
			if(!BookingSeat.checkBlackList('nonmember')) return;
			BookingNonSeat.transNo = BookingSeat.createTradeNo(BookingNonSeat.cinemaCode);
			BookingNonSeat.holdNonselectionSeat(BookingNonSeat.cinemaCode, BookingNonSeat.screenCode, BookingNonSeat.playDate, BookingNonSeat.showSeq, BookingSeatType.seatType, BookingNonSeat.transNo, 'N');
		} else {
			BookingSeatDatas.nextStep2('');
		}
		closeMessageBox('modal_login', 'custom');
	},
	// 좌석 선택전 로그인 체크
	nextLoginCheck: function(){
		// 로그인 체크
		$.ajax({
			url: '/pages/common/loginCheck.jsp',
			type: 'POST'
		}).success(function( data ) {
			if (data.resultCode === 'success') {
				BookingSeatDatas.nextStep2('');
			} else {
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
//							$("#modal_login .ad_box").attr("href", "javascript:adHasClick(\'"+data.link+"\', "+data.adClick+")").attr("title", data.linkAlt).html("<img src=\""+data.image+"\" alt=\""+data.alt+"\"/>");

							// 광고 영역 변경으로 인한 수정
							$("#modal_login div.member div.bottomArea a").attr("href", "javascript:adHasClick(\'"+data.link+"\', "+data.adClick+")").attr("title", data.linkAlt).html("<img src=\""+data.image+"\" alt=\""+data.alt+"\"/>");

							// 초기화 처리
							$('div#nonmemberBookingPopup input[name=name]').val('');
							$('div#nonmemberBookingPopup input[name=birthday]').val('');
							$('div#nonmemberBookingPopup input[name=mobileNo]').val('');
							$('div#nonmemberBookingPopup input[name=password]').val('');
							$('div#nonmemberBookingPopup input[name=confirmPassword]').val('');
							$('div#nonmemberBookingPopup input:radio[name=radio0]:input[value=N]').attr("checked", true);

							$('div#nonmemberBookingPopup input:radio[name=radio0]:input[value=N]').closest('span').addClass('checked');
							$('div#nonmemberBookingPopup input:radio[name=radio0]:input[value=N]').closest('span').attr('aria-checked', true);
							$('div#nonmemberBookingPopup input:radio[name=radio0]:input[value=Y]').closest('span').removeClass('checked');
							$('div#nonmemberBookingPopup input:radio[name=radio0]:input[value=Y]').closest('span').attr('aria-checked', false);

							showLoginPopup();
						}
					});
				}
				BookingSeatDatas.dbClickBlock = false;	// 중복클릭잠금 해제
			}
		});
	}
}

/****************************
 * 티켓선택화면
 ****************************/
var BookingSeatTicketDatas = {
	MAX_PERSON: 8,	// 최대인원수
	changeTicket: function(){
		var $this = $(this);
		var preValue = $this.data('pre-value');
		var ticketNum = $this.val();

		if(preValue==ticketNum) return;

		// 선택한 티켓정보 담고 단독 선택 여부 확인
		var exclusiveYn = BookingSeatTicketDatas.checkSelectedTicket();

		// 선택한 전체티켓수
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();
		var selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();

		// 선택된 좌석이 있는 경우 인원 변경 안됨
		if ((totalSelectedTicket-selectedSeatCount) < 0 || BookingSeatDatas.exclusiveType == 'Y') {
			confirmBox('알림', '선택하신 좌석을 모두 취소하고 다시 선택하시겠습니까?', function(){
				BookingSeatTicketDatas.resetAll();
			}, function(){
				// 이전값으로 셋팅
				$this.val(preValue).selectpicker('refresh');
				$('[data-id="'+$this.attr('id')+'"]').focus();
			});
			return false;
		}

		// 전체 티켓이 0인 경우~ 리셋!
		if(totalSelectedTicket==0){
			BookingSeatTicketDatas.resetAll();
			return false;
		}

		if(ticketNum > 0){
			// 선택 가능 인원 수 체크
			if (totalSelectedTicket > BookingSeatTicketDatas.MAX_PERSON) {
				messageBox('인원선택은 총 ' + BookingSeatTicketDatas.MAX_PERSON + '명까지 가능합니다.', function(){
					// 이전값으로 셋팅
					$this.val(preValue).selectpicker('refresh');
					$('[data-id="'+$this.attr('id')+'"]').focus();
				});
				return false;
			}
		}
		// 값 셋팅
		BookingSeatTicketDatas.setSelectedTicket();
		// 이전값 갱신
		$this.data('pre-value', ticketNum);
		// 선택한 티켓수 표시
		$('span#totalCountSelectedTicket').text(totalSelectedTicket);
		// 좌석선택안내를 제거한다.
		$('button.alert_seat').fadeOut();
		// 좌석 안내 팝업을 확인한다.
		if($(this).attr('popupYn')=='Y' && ticketNum > 0) {
			BookingSeatTicketDatas.checkTicketGroupPopup($(this).attr('ticketTypeCode'));
		}

		// 티켓 0 인 경우 티켓 활성화
		if(totalSelectedTicket == 0){
			$('select[name^=ticketTypeCode_]', '#bookingSeatTicket').prop('disabled', false).selectpicker('refresh');
		}else{
			if(exclusiveYn){
				$('select[name^=ticketTypeCode_]:not([id='+$this.attr('id')+'])', '#bookingSeatTicket').each(function(){
					$(this).find(':eq(0)').prop('selected', true).end().prop('disabled', true).selectpicker('refresh');
				});
			}else{
				// 단독 선택 티켓선택 못하게 막기
				$('select[name^=ticketTypeCode_][exclusiveYn=Y]', '#bookingSeatTicket').each(function(){
					$(this).find(':eq(0)').prop('selected', true).end().prop('disabled', true).selectpicker('refresh');
				});
			}
		}

		// 선택한 티켓정보 담기
		BookingSeatTicketDatas.setSelectedTicket();
		// 좌석별 권종 초기화
		BookingSeatDatas.checkSeatCntCheck();

		// 커플석 단독 선택이고, 선택된 좌석 수가 있는 경우
		if(BookingSeatDatas.exclusiveSeatType==BookingSeatDatas.coupleSeat && selectedSeatCount > 0 && (totalSelectedTicket%2)!=0){
			messageBox('커플석은 2,4,6,8명으로 변경 가능합니다.', function(){
				BookingSeatTicketDatas.resetSeat();
			});
		}else{
			// 선택된 좌석이 있는 경우 티켓 재할당
			if(selectedSeatCount > 0){
				// 티켓 셋팅
				BookingSeatDatas.selectedSeatInfoItems =[];
				// 티켓 할당
				BookingSeatDatas.assigningSeatsTotickets([]);
				// 현황판에 좌석 표기
				BookingSeatDatas.drowDisplaySeat();

				selectedSeatCount = BookingSeatDatas.getSelectedSeatCount();
			}
			var isSelectedSeatCount = totalSelectedTicket - selectedSeatCount;	// 선택 가능한 좌석수
			// 선택한 붙임설정에 따라 활성화처리
			BookingSeatDatas.seatPaxCount = isSelectedSeatCount > 1 ? 2 : (isSelectedSeatCount < 0) ? 0: isSelectedSeatCount;
			BookingSeatDatas.draw();
		}
	},

	// 선택 가능 티켓 정보 담기
	ticketTypeCodes : {},
	checkSelectedTicket : function(){
		var exclusiveYn = false;
		$('select[name^=ticketTypeCode_]', '#bookingSeatTicket').each(function() {
			var cnt = parseInt($(this).val());
			var ticketTypeCode = $(this).attr('ticketTypeCode');
			if(cnt > 0){
				if($(this).attr('exclusiveYn') == 'Y') exclusiveYn = true;
			}
		});
		return exclusiveYn;
	},
	setSelectedTicket : function(){
		var arry = {};
		var normalTicketCode = '';
		$('select[name^=ticketTypeCode_]', '#bookingSeatTicket').each(function() {
			var cnt = parseInt($(this).val());
			var ticketTypeCode = $(this).attr('ticketTypeCode');
			if(cnt > 0){
				arry[ticketTypeCode] = {'totalCnt': cnt, 'useCnt': 0};
				$.each(BookingSeatDatas.adultTicketCodes, function(i, val){
					if(val==ticketTypeCode) {
						normalTicketCode = val;
					}
				});
			}
		});

		if(normalTicketCode) BookingSeatDatas.normalTicketCode = normalTicketCode;

		// 좌석별 선태된 권종 정보
		$.each(BookingSeatDatas.checkSeatArry, function(i, item){
			var maxCnt = 0;
			$.each(item, function(j, item2){
				if(arry[j]){
					maxCnt += arry[j].totalCnt;
				}
			});
			item.maxCnt = maxCnt;
			item.selected = 0;
		});

		var ticketPricesSort = [];
		$.each(BookingSeatDatas.ticketDifference, function(i, item){
			if(arry[item.ticketTypeCode]) ticketPricesSort.push(item);
		});

		// 차액이 높은 티켓순으로 정렬, 동일 금액이면, 티켓 가격이 낮은 순으로 정렬. 티켓 가격도 동일하면 권종이 낮은 순으로 정렬
		ticketPricesSort.sort(function(a, b){

			if(a.difference ==  b.difference) {
				if(a.amt ==  b.amt) return a.seatType < b.seatType ? -1 : a.seatType > b.seatType ? 1 : 0;
				else return a.amt < b.amt ? -1 : a.amt > b.amt ? 1 : 0;
			}
			return a.difference > b.difference ? -1 : a.difference < b.difference ? 1 : 0;
		});

		BookingSeatTicketDatas.ticketTypeCodes = arry;
		BookingSeatDatas.ticketPricesSort = ticketPricesSort;
	},

	// 선택한 전체 티켓수 가져오기
	getTotalSelectedTicket: function() {
		var totalCount = 0;
		$('select[name^=ticketTypeCode_]', '#bookingSeatTicket').each(function() {
			var cnt = parseInt($(this).val()) ? parseInt($(this).val()) : 0;
			if(cnt > 0) totalCount += cnt;
		});
		return totalCount;
	},

	// 티켓 안내 팝업
	checkTicketGroupPopup: function(ticketGroupCode){
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.checkTicketGroupPopup',
				siteCode: '36',
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenCode: BookingSeatDatas.movieData.screenCode,
				playDate: BookingSeatDatas.movieData.playDate,
				showSeq: BookingSeatDatas.movieData.showSeq,
				showMovieCode: BookingSeatDatas.movieData.showMovieCode,
				ticketGroupCode: ticketGroupCode,
				korEngGubun: '1',
			}
		}).success(function( data ) {
			if(data.resultCode === '0000'){
				openGuidePopup(data.result);
			}
		});
	},

	// 좌석 선택 초기화
	resetAll: function(){
		// 티켓수 초기화
		BookingSeatTicketDatas.resetTicket();
		// 선택좌석 해제
		for (var i=(BookingSeatDatas.selectedSeats.length-1); i >= 0; i--) {
			BookingSeatDatas.seatList[BookingSeatDatas.selectedSeats[i].seatIndex].isSelected = false;
		}
		// 변수 초기화
		BookingSeatDatas.resetVariable();
		// 좌석별 권종 초기화
		BookingSeatDatas.checkSeatClear();
		// 현황판에 좌석 표기
		BookingSeatDatas.drowDisplaySeat();
		// 선택한 티켓수 표시
		$('span#totalCountSelectedTicket').text(0);

		BookingSeatTicketDatas.getSeatList(function(){
			// 좌석 다시 그리기
			BookingSeatDatas.draw();
		});
	},

	// 좌석 데이터 가져오기
	getSeatList: function(fn){
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'Booking.getSeatList',
				siteCode: '36',
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenCode: BookingSeatDatas.movieData.screenCode,
				playDate: BookingSeatDatas.movieData.playDate,
				showSeq: BookingSeatDatas.movieData.showSeq
			}
		}).success(function( data ) {
			// 좌석 데이타
			BookingSeatDatas.seatList = data.list;

			if (typeof fn === 'function') {
				fn();
			}
		});
	},

	// 권종 초기화 x. 좌석정보 초기화 o
	resetSeatAll: function(){
		BookingSeatTicketDatas.getSeatList(function(){
			BookingSeatDatas.selectedSeats = [];	// 선택된 좌석

			// 좌석 다시 그리기
			BookingSeatTicketDatas.resetSeat();
		});
	},

	// 권종 초기화 x. 좌석정보 초기화 o
	resetSeat: function(){

		// 선택좌석 해제
		$.each(BookingSeatDatas.selectedSeats, function(i, seatInfo){
			BookingSeatDatas.seatList[seatInfo.seatIndex].isSelected = false;
		});

		$.each(BookingSeatTicketDatas.ticketTypeCodes, function(i, item){
			item.useCnt = 0;
		});
		BookingSeatDatas.selectedSeats = [];

		// 티켓 셋팅
		BookingSeatDatas.selectedSeatInfoItems =[];

		// 단독선택여부 해제
		BookingSeatDatas.exclusiveInit();

		// 좌석 정보 초기화
		$.each(BookingSeatDatas.checkSeatArry, function(i, item){
			item.selected = 0;
		});

		// 현황판에 좌석 표기
		BookingSeatDatas.drowDisplaySeat();

		// 선택한 전체티켓수
		var totalSelectedTicket = BookingSeatTicketDatas.getTotalSelectedTicket();

		BookingSeatDatas.seatPaxCount = totalSelectedTicket > 1 ? 2 : (totalSelectedTicket < 0) ? 0: totalSelectedTicket;


		BookingSeatDatas.draw();

	},

	// 티켓수 초기화
	resetTicket: function() {
		if(BookingSeatDatas.movieData.nonSeatYn === 'Y'){
			$('select[name^=ticketCode]', '#bookingSelectSeatTicket').val(0).data('pre-value', 0).prop('disabled', false).selectpicker('refresh');
		}else{
			$('select[name^=ticketTypeCode_]', '#bookingSeatTicket').val(0).data('pre-value', 0).prop('disabled', false).selectpicker('refresh');
		}
	}
}

function openGuidePopup (result){
	if (result != null && $.trim(result.popupMsg) !== '') {
		if(typeof result.popupType!='undefined' && result.popupType!=null && result.popupType=='image')
			messageBox( '<img src="'+result.popupMsg+'" class="layerPopupImgLoad"  alt=""/>');
		else
			messageBox( result.popupMsg);
	}
}

/****************************
 * 예매현황판 화면
 ****************************/
var BookingSeatStatusBoard = {
	filmRating:'',
	showPage: function(movieCode, showMovieCode, cinemaCode, screenName, playDate, startTime, nonSeatYn) {
		this.resetDisplaySelectedSeat(); 	// 선택좌석표시 초기화
		this.displayCountSelectedByTicket(null); // 티켓별 선택한 티켓수 표시 초기화
		try {
			_requestBookingSeatStatusBoard.request({
				movie: BookingSeatDatas.movieData.movieCode,
				showMovieCode: BookingSeatDatas.movieData.showMovieCode,
				cinemaCode: BookingSeatDatas.movieData.cinemaCode,
				screenName: BookingSeatDatas.movieData.screenName,
				playDate: BookingSeatDatas.movieData.playDate,
				startTime: BookingSeatDatas.movieData.startTime,
				nonSeatYn: BookingSeatDatas.movieData.nonSeatYn||'N'
			});
		} catch(e) {
			messageBox(e);
		}
	},

	// 티켓별 선택한 티켓수 표시
	displayCountSelectedByTicket: function(selectedTickets) {
		$('li#countSelectedByTicket').empty();
		if (selectedTickets != null) {
			var tmpTicketCode = '';
			for (var i=0; i < selectedTickets.length; i++) {
				var ticket = selectedTickets[i];
				if (ticket.code != tmpTicketCode) {
					tmpTicketCode = ticket.code;
					var kind = $("#bookingSelectSeatTicket").find("span.filter-option").text().substring($("#bookingSelectSeatTicket").find("span.filter-option").text().length-1);
					$('li#countSelectedByTicket').append('<span>' + ticket.name + ' ' + ticket.count + kind +'&nbsp;</span>');
				}
			}
		}
	},

	// 선택한 전체인원수의 가격표시
	displayTotalTicketPrice: function(price) {
		$('div#bookingSelectSeatStatusBoard  #ticketTotalPrice').text(numberWithCommas(price));
	},

	// 선택한 좌석번호 표시
	displaySelectedSeat: function(seatNum) {
		var htmlTxt = "<li data-seat-num=\"" + seatNum + "\">" + seatNum + "</li>";
		$('#selectedSeatNumbers1').append(htmlTxt);
	},

	// 선택한 좌석번호 제거
	nondisplaySelectedSeat: function(seatNum) {
		$('li', '#selectedSeatNumbers1').each(function() {
			if(seatNum == $(this).data('seatNum')) {
				$(this).remove();
			}
		});
	},

	resetDisplaySelectedSeat: function() {
		$('#selectedSeatNumbers1').empty();
	}
};
