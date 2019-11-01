/**
 * @Class Name :
 * @Description :
 * @Modification Information
 *
 * 수정일         수정자      수정내용
 * ----------     --------    ---------------------------
 * 2015.06.01     kch         하나SK, 현대M, 신용카드 유효기간 자릿수 체크
 * 2015.07.07     kch         KB이퀀즈 삭제
 *
 * Copyright (C) 2009 by MOPAS  All right reserved.
 */
var _requestBookingPayment = new JSPRequest('/pages/booking/step3/Booking_Payment.jsp', 'div#bookingPayment', 'div#bookingPayment');
var _requestBookingPaymentCheck = new JSPRequest('/pages/booking/step3/Booking_Payment_Check.jsp', 'div#bookingPaymentCheck', 'div#bookingPaymentCheck');
var _requestBookingPaymentDone = new JSPRequest('/pages/booking/step3/Booking_Payment_Done.jsp', 'div#bookingPaymentDone', 'div#bookingPaymentDone');

// 개발에서만 Start
//if(!lowBrowserVer)
//	hostname = hostname.replace('8080', '8443');
// 개발에서만 End

var BookingPayment = {
	payMethods:null,
	applyMethods:null, // 적용된 할인/관람/결제만 내려옴.
	balance: null,
	initPaySet:null,
	executeCheck:false,
	executeCardCheck:false,
	okCashbagSaveCheck:false,
	selectPayGbn:[],
	isDup: false,	// 중복처리 방지
	memberYn:'', // 회원여부
	dbClickBlock:false,
	hydmCheck:null, // 현대 M point
	culturenuriCheck:null, // 문화누리 Check
	paymentPreCardCreditCheck:false, // BC 선할인 카드 조회 Check

	showPage: function(transNo, screen, transType, playDate, memberYn) {
		
		var that = this;
		this.memberYn = memberYn;

		// 예매창 영역을 숨긴다.
		$("#bookingPayment").show();
		_requestBookingPayment.onSuccess = function() {
			var totalPrice = BookingSeatDatas.getTotalPrice();
			$('#payment_phone input[name=amount]').val(numberWithCommas(totalPrice));
			$('p#balance').html(numberWithCommas(totalPrice) + ' <span>원</span>'); // 남은 결제금액 출력
			$('p#totalAmount').html(numberWithCommas(totalPrice) + ' <span>원</span>'); // 총 결제금액 출력
			//$('b#okcashbagSaveAmount').html(numberWithCommas(totalPrice));

			that.balance = totalPrice;
		};

		try {
			_requestBookingPayment.request({
				screen: screen,
				transType: transType,
				playDate: playDate,
				transNo: transNo
			});

			// 1.상세 레이어 표시할때 타이틀 교체를 위한 이벤트 바인딩
			// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var moveTitle = '빠른예매(결제)' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?show=booking&p=step3";
			megaboxLog.openPopup(moveURL, moveTitle);
		} catch(e) {
			messageBox(e);
		}

		// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
		$('#bookingPayment').off('hide.bs.modal').on('hide.bs.modal',function() {
			megaboxLog.closePopup();
		});

		this.prepareBooking(transNo, '1', memberYn);
	},
	
	executePaymentBtn: function() {
		//2015.04.30 중복클릭 막음 - kch
		//alert("결제 시작점 Dennis");
		if (this.dbClickBlock)	return;
		else					this.dbClickBlock = true;

		if(Number(this.balance)==0)
		{
			this.executePayment();
		}
		else
			messageBox('결제되지 않은 금액이 있습니다.<br/>결제수단을 선택해 주세요.');

		this.dbClickBlock = false;	//2015.04.30 중복클릭잠금 해제 - kch
	},

	// 결제실행
	executePayment: function() {

		//alert("결제 시작점 2 Dennis");
		if(!this.executeCheck)
		{
			this.executeCheck=true;
			this.bookingFinalCheck(BookingSeatDatas.movieData.transNo);

		}
	},

	// 결제취소
	cancelPayment: function() {
		if(this.executeCardCheck || this.executeCheck)
		{
			return false;
		}

		this.selectPayGbn=[];
		var obj = BookingSeatDatas.movieData;
		this.disposeBookingPay(obj.transNo);

		BookingSeatDatas.releaseSeat(obj.cinemaCode, obj.screenCode, obj.playDate, obj.showSeq, obj.transNo);

		//이전버튼 클리시 인원/좌석선택팝업창으로 포커싱
		$(".skipnavi").focus(function(){
			$("#select_seat").focus();
		});

		// 예매창 영역을 숨긴다.
		$("#bookingPayment").hide();
	},

	// 신용/체크카드 적용
	applyCreditCard: function(transNo) {

		if (validate('#payment_card_credit')) {

			if(this.executeCardCheck) return;
			this.executeCardCheck=true;

			var data = extractFormData('#payment_card_credit');

			//하나SK, 현대M, 신용카드 유효기간 자릿수 체크 2015.06.01 kch
			if (data.expireYear.length != 2) {
				messageBox("유효기간 '년'을 확인해 주세요");
				this.executeCardCheck=false;
				return;
			}

			if (data.expireMonth.length != 2) {
				messageBox("유효기간 '월'을 확인해 주세요<br/>(1월 : 01)");
				this.executeCardCheck=false;
				return;
			}

			if (this.balance < 50000 && data.installment > 0) {
				messageBox("5만원 이상부터 할부선택이 가능합니다.");
				this.executeCardCheck=false;
				return;
			}

			var that = this;

			var ajaxOptions={
					url: getServerUrl(true) + '/DataProvider',
					type: 'POST',
					async: false,
					data: {
						_command: 'Pay.applyCreditCard',
						transNo: transNo,
						cardNo1: data.cardNo1,
						cardNo2: data.cardNo2,
						cardNo3: data.cardNo3,
						cardNo4: data.cardNo4,
						expireYear: data.expireYear,
						expireMonth: data.expireMonth,
						password: data.password,
						socialNo: data.socialNo,
						installment: data.installment
					}
				};

			if(!lowBrowserVer)
			{
				ajaxOptions.crossDomain=true;
				ajaxOptions.xhrFields={withCredentials : true};
			}

			$.ajax(ajaxOptions).success(function( data ) {
				if (data.resultCode === '0000') {
					//that.afterApply(data);
					$('div#payment_card button.lp_close').click();
					that.executePayment(transNo);
				} else if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
				} else {
					BookingPayment.executeCardCheck=false;
					messageBox(data.resultMessage);
				}
			});
		}
	},

	/**
	 * BC 선할인 카드 신용/체크카드 적용
	 * @param transNo
	 */

	applyPreCreditCard: function(transNo) {

		if (validate('#payment_card_precredit')) {

			if(this.executeCardCheck) return;
			this.executeCardCheck=true;

			var data = extractFormData('#payment_card_precredit');

			//하나SK, 현대M, 신용카드 유효기간 자릿수 체크 2015.06.01 kch
			if (data.expireYear.length != 2) {
				messageBox("유효기간 '년'을 확인해 주세요");
				this.executeCardCheck=false;
				return;
			}

			if (data.expireMonth.length != 2) {
				messageBox("유효기간 '월'을 확인해 주세요<br/>(1월 : 01)");
				this.executeCardCheck=false;
				return;
			}
			/* 화면 재진입시 조회하지 않았으면 조회/적용 버튼을 클릭하여 먼저 조회하는 것으로. */
			if (!this.paymentPreCardCreditCheck) {
				messageBox("조회/적용 버튼을 클릭하여 먼저 조회하여 주세요.");
				this.executeCardCheck=false;
				return;
			}

			var that = this;

			var ajaxOptions={
					url: getServerUrl(true) + '/DataProvider',
					type: 'POST',
					async: false,
					data: {
						_command: 'Pay.applyCardPreDiscount',
						transNo: transNo,
						cardNo1: data.cardNo1,
						cardNo2: data.cardNo2,
						cardNo3: data.cardNo3,
						cardNo4: data.cardNo4,
						expireYear: data.expireYear,
						expireMonth: data.expireMonth,
						password: data.password,
						socialNo: data.socialNo
					}
				};

			if(!lowBrowserVer)
			{
				ajaxOptions.crossDomain=true;
				ajaxOptions.xhrFields={withCredentials : true};
			}

			$.ajax(ajaxOptions).success(function( data ) {
				if (data.resultCode === '0000') {
					//that.afterApply(data);
					$('div#payment_card button.lp_close').click();
					that.executePayment(transNo);
				} else if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
				} else {
					BookingPayment.executeCardCheck=false;
					messageBox(data.resultMessage);
				}
			});
		}
	},


	// 삼성포인트카드
	applySamsungPoint: function(transNo) {

		if (validate('#payment_card_samp')) {

			if(this.executeCardCheck) return;
			this.executeCardCheck=true;

			var data = extractFormData('#payment_card_samp');

			//하나SK, 현대M, 신용카드 유효기간 자릿수 체크 2015.06.01 kch
			if (data.expireYear.length != 2) {
				messageBox("유효기간 '년'을 확인해 주세요");
				this.executeCardCheck=false;
				return;
			}

			if (data.expireMonth.length != 2) {
				messageBox("유효기간 '월'을 확인해 주세요<br/>(1월 : 01)");
				this.executeCardCheck=false;
				return;
			}

			var that = this;

			var ajaxOptions={
					url: getServerUrl(true) + '/DataProvider',
					type: 'POST',
					async: false,
					data: {
						_command: 'Pay.applySamsungPoint',
						transNo: transNo,
						cardNo1: data.cardNo1,
						cardNo2: data.cardNo2,
						cardNo3: data.cardNo3,
						cardNo4: data.cardNo4,
						expireYear: data.expireYear,
						expireMonth: data.expireMonth,
						password: data.password,
						socialNo: data.socialNo,
						installment: '0'
					}
				};

			if(!lowBrowserVer)
			{
				ajaxOptions.crossDomain=true;
				ajaxOptions.xhrFields={withCredentials : true};
			}

			$.ajax(ajaxOptions).success(function( data ) {
				if (data.resultCode === '0000') {
					//that.afterApply(data);
					$('div#payment_point button.lp_close').click();
					that.executePayment(transNo);
				} else if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
				} else {
					BookingPayment.executeCardCheck=false;
					messageBox(data.resultMessage);
				}
			});
		}
	},

	// 하나SK포인트
	applyHanaSKPoint: function(transNo) {

		if (validate('#payment_card_hskp')) {

			if(this.executeCardCheck) return;
			this.executeCardCheck=true;

			var data = extractFormData('#payment_card_hskp');

			//하나SK, 현대M, 신용카드 유효기간 자릿수 체크 2015.06.01 kch
			if (data.expireYear.length != 2) {
				messageBox("유효기간 '년'을 확인해 주세요");
				this.executeCardCheck=false;
				return;
			}

			if (data.expireMonth.length != 2) {
				messageBox("유효기간 '월'을 확인해 주세요<br/>(1월 : 01)");
				this.executeCardCheck=false;
				return;
			}

			var that = this;

			var ajaxOptions={
					url: getServerUrl(true) + '/DataProvider',
					type: 'POST',
					async: false,
					data: {
						_command: 'Pay.applyHanaSKPoint',
						transNo: transNo,
						cardNo1: data.cardNo1,
						cardNo2: data.cardNo2,
						cardNo3: data.cardNo3,
						cardNo4: data.cardNo4,
						expireYear: data.expireYear,
						expireMonth: data.expireMonth,
						password: data.password,
						socialNo: data.socialNo,
						installment: '0'
					}
				};

			if(!lowBrowserVer)
			{
				ajaxOptions.crossDomain=true;
				ajaxOptions.xhrFields={withCredentials : true};
			}

			$.ajax(ajaxOptions).success(function( data ) {
				if (data.resultCode === '0000') {
					//that.afterApply(data);
					$('div#payment_point button.lp_close').click();
					that.executePayment(transNo);
				} else if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
				} else {
					BookingPayment.executeCardCheck=false;
					messageBox(data.resultMessage);
				}
			});
		}
	},

	checkCard : function(obj){

		//alert("신용/체크카드 결제 확인 Dennis");
		if($(obj).attr('data-target')!="")
		{
			for (var i=0; i < this.payMethods.length; i++) {

				var payCode = this.payMethods[i].code;
				var message = this.payMethods[i].message;
				var $input = $('#payment_card_' + payCode + '_input');
				var $label = $('#payment_card_' + payCode + '_label');

				if(payCode == 'mobile' || payCode == 'okcb' || payCode == 'joongang'){
					continue;
				}

				$input.attr('payCode', payCode);

				// 사용가능한 결제도구인지 체크
				if (this.payMethods[i].enabled === 'N') {

					/*
					 *  2015.04.07
					 *  웹표준 관련 수정
					 *  1. 비활성화 disabled
					 *  2. onclick시 메시지 값이 있으면 출력. 아니면 패스
					 * */
					$input.attr('message', message);
					$label.attr('message', message);

					$label.addClass('fwn');
					$label.addClass('c_999');
					$input.attr('disabled', 'disabled');

					$label.on('click', function(){
						var message = $(this).attr('message');
						if(message != undefined && message !== ''){
							messageBox(message);
						}
					});

				} else {

					$label.removeClass('fwn');
					$label.removeClass('c_999');
					$input.removeAttr('disabled');

					$input.on('click', function(){

						if($(this).is(':checked')){
							$(".tab-content > div").hide();
							$("#payment_card_" + $(this).attr('payCode')).show();
						}

					});

				}
			}

			//5만원 이상 할부결제추가
			var $selectInstallment = $('#payment_card_credit select#credit_installment');
			if (this.balance >= 50000) {
				$selectInstallment.val(0);
				$selectInstallment.prop('disabled', false);
				$selectInstallment.selectpicker('refresh');
			} else {
				$selectInstallment.val(0);
				$selectInstallment.attr('disabled', 'disabled');
				$selectInstallment.selectpicker('refresh');
			}

			// 신용 체크카드 -> 즉시 할인선택 시 잔여결제금액 갱신과 초기화.
			$("#payment_card .tab-content").find("input[type='text'],input[type='password']").val(""); // 초기화.
			BookingPayment.paymentPreCardCreditCheck = false;	// BC 선할인 카드 조회하지 않음으로 세팅.
			var remainBalancePrecredit = "";
			var htmlSettleAmountTxtInit = "<strong>" + '0' + "원</strong>";
			remainBalancePrecredit = numberWithCommas(BookingPayment.balance) + "원";
			$("div#payment_card_precredit #precredit_remainAmountTxt").text(remainBalancePrecredit);
			$("div#payment_card_precredit #precredit_additionalDiscountTxt").text("");
			$("div#payment_card_precredit #precredit_settleAmountTxt").html(htmlSettleAmountTxtInit);
			$(".smbtn").show();	// 조회/적용 버튼 나타나게

			$('div.pay_final_wrp').hide();

		}
	},

	// 모바일 조회
	checkMobile: function(transNo) {

		if($('#methods_mobile').attr('data-target')!="")
		{
			var amount = BookingPayment.balance;

			var frm = document.payAuthMobFrm;

			frm.amount.value = amount;
			frm.target = 'payAuthMobFrame';
			frm.action = 'https://' + hostname + '/pages/common/payAuthMob.jsp';
			frm.submit();
		}
	},

	// 카카오페이 조회
	checkKakaoPay: function(transNo) {

		if($('#methods_kakaopay').attr('data-target')!="")
		{
			//alert('checkKakaoPay' + $('#methods_kakaopay').attr('data-target'));

			var message = $('#methods_kakaopay').attr('message');
			var enabledYn = $('#methods_kakaopay').attr('enabledyn');

			//alert('checkKakaoPay' + $('#methods_kakaopay').attr('data-target'));
			//alert("message"+ message);
			//alert("enabledYn" + enabledYn);

			if (message != null && message.length > 0) {
				//alert("message.length" + message.length);
				messageBox(message,function(){
					if(enabledYn === 'Y')
						//alert("checkKakaoPay enabledYn");
					{
						callKakaoPay(transNo);
					}
				});
			} else {
				callKakaoPay(transNo);
			}

			function callKakaoPay(transNo){
				var mobileNo   = null;
				var memberName = null;
				var cinemaCode = BookingSeatDatas.movieData.cinemaCode;
				var screenCode = BookingSeatDatas.movieData.screenCode;

				// 비회원일 경우
				if (BookingPayment.memberYn !== 'Y') {
					var nonmemberInfo = BookingSeatDatas.nonmemberInfo;
					memberName = nonmemberInfo.name;
					mobileNo = nonmemberInfo.mobileNo1 + '-' + nonmemberInfo.mobileNo2 + '-' + nonmemberInfo.mobileNo3;
				}

				var param = {
					type : 'booking',
					transNo : transNo,
					amount : BookingPayment.balance,
					count : 1,
					memberName : memberName,
					mobileNo : mobileNo,
					cinemaCode : cinemaCode,
					screenCode : screenCode
				}

				confirmKakaoPay(param);
			}
		}
	},
	// 카카오페이 적용
	applyKakaoPay : function(data) {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				/* legacy
				data: {
					_command : 'Pay.applyKakaoPay'
					,transNo : data.transNo
					,amt : data.amt
					,txnId : data.txnId
					,mId : data.mId
					,buyerName : data.buyerName
					,ediDate : data.ediDate
					,encryptData : data.encryptData
					,merchant_Txt_Num : data.merchant_Txt_Num
					,spu : data.spu
					,spuSignToken : data.spuSignToken
					,mpayPub : data.mpayPub
					,authFlg : data.authFlg
					,buyerEmail : data.buyerEmail
					,goodsName : data.goodsName
				}
				*/
				// 기존 kakaopay 를 남겨두고 N 을 하나 붙여서 만듬. Pay.applyKakaoPayN
	            data: {
	                _command : 'Pay.applyKakaoPayN'
	                ,transNo : data.transNo
	                ,amt : data.amt
	                ,userId : data.userId
	                ,pgToken : data.pgToken
	                ,mId : data.mId
	                ,tid : data.tid
	            }
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				that.executePayment(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCardCheck=false;
				messageBox(data.resultMessage);
			}
		});
	},

	// 페이나우 조회
	checkPayNow: function(transNo) {

		if($('#methods_paynow').attr('data-target')!="")
		{
			var message = $('#methods_paynow').attr('message');
			var enabledYn = $('#methods_paynow').attr('enabledyn');

			//alert('checkPayNow' + $('#methods_paynow').attr('data-target'));
			//alert("message"+ message);
			//alert("enabledYn" + enabledYn);


			if (message != null && message.length > 0) {
				//alert("message.length" + message.length);
				messageBox(message,function(){
					if(enabledYn === 'Y')
						//alert("checkPayNow = enabledYn");
					{
						callPayNow(transNo);
					}
				});
			} else {
				callPayNow(transNo);
			}

			function callPayNow(transNo){
				var mobileNo   = null;
				var memberName = null;
				var cinemaCode = BookingSeatDatas.movieData.cinemaCode;
				var screenCode = BookingSeatDatas.movieData.screenCode;

				// 비회원일 경우
				if (BookingPayment.memberYn !== 'Y') {
					var nonmemberInfo = BookingSeatDatas.nonmemberInfo;
					memberName = nonmemberInfo.name;
					mobileNo = nonmemberInfo.mobileNo1 + '-' + nonmemberInfo.mobileNo2 + '-' + nonmemberInfo.mobileNo3;
				}

				var param = {
					type : 'booking',
					transNo : transNo,
					amount : BookingPayment.balance,
					count : 1,
					memberName : memberName,
					mobileNo : mobileNo,
					cinemaCode : cinemaCode,
					screenCode : screenCode
				}

				confirmPayNow(param);
			}
		}
	},
	// 페이나우 적용
	applyPayNow : function(data) {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command : 'Pay.applyPayNow'
					,transNo : data.transNo
					,lgdMid : data.lgdMid
					,lgdPaykey : data.lgdPaykey
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				that.executePayment(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCardCheck=false;
				messageBox(data.resultMessage);
			}
		});
	},

	// 페이코 조회
	checkPayco: function(transNo) {

		if($('#methods_payco').attr('data-target')!="")
		{
			var message = $('#methods_payco').attr('message');
			var enabledYn = $('#methods_payco').attr('enabledyn');

			/* 결제수단이 내려오기 전에 임시로 세팅. */

			//alert('checkPayco' + $('#methods_payco').attr('data-target'));
			//alert("message :"+ message);
			//alert("enabledYn :" + enabledYn);
			//alert("movieCode" + BookingSeatDatas.movieData.movieCode);
			//alert("showMovieCode" + BookingSeatDatas.movieData.showMovieCode);

			if (message != null && message.length > 0) {
				//alert("message.length" + message.length);
				messageBox(message,function(){
					if(enabledYn === 'Y')
						//alert("checkPayNow = enabledYn");
					{
						callPayco(transNo);
					}
				});
			} else {
				callPayco(transNo);
			}

			function callPayco(transNo){
				var mobileNo   = null;
				var memberName = null;
				var cinemaCode = BookingSeatDatas.movieData.cinemaCode;
				var screenCode = BookingSeatDatas.movieData.screenCode;
				var movieCode = BookingSeatDatas.movieData.movieCode;



				// 비회원일 경우
				if (BookingPayment.memberYn !== 'Y') {
					var nonmemberInfo = BookingSeatDatas.nonmemberInfo;
					memberName = nonmemberInfo.name;
					mobileNo = nonmemberInfo.mobileNo1 + '-' + nonmemberInfo.mobileNo2 + '-' + nonmemberInfo.mobileNo3;
				}

				var param = {
					type : 'booking',
					transNo : transNo,
					amount : BookingPayment.balance,
					count : 1,
					memberName : memberName,
					mobileNo : mobileNo,
					cinemaCode : cinemaCode,
					screenCode : screenCode,
					movieCode : movieCode
				}

				confirmPayco(param);
			}
		}
	},

	// 페이코 적용
	applyPayco : function(data) {

		//alert("applyPayco");
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command : 'Pay.applyPayco'
					,transNo : data.sellerOrderReferenceKey
					,sellerKey : data.sellerKey
					,reserveOrderNo : data.reserveOrderNo
					,sellerOrderReferenceKey : data.sellerOrderReferenceKey
					,paymentCertifyToken : data.paymentCertifyToken
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				that.executePayment(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCardCheck=false;
				messageBox(data.resultMessage);
			}
		});
	},

	// 모바일적용
	applyMobile: function(transNo) {

		if ($('input:checkbox[name=checkApplyMobile]').is(':not(:checked)')) {
			messageBox('결제 내용을 확인해주세요.');
			return;
		}

		$('#payment_phone2 strong.timer').countdown('stop');

		var telecom 	= $('div#payment_phone input[name=telecom]:checked').val();
		var mobileNo1 	= $('div#payment_phone input[name=mobileNo1]').val();
		var mobileNo2 	= $('div#payment_phone input[name=mobileNo2]').val();
		var mobileNo3 	= $('div#payment_phone input[name=mobileNo3]').val();
		var socialNo1 	= $('div#payment_phone input[name=socialNo1]').val();
		var socialNo2 	= $('div#payment_phone input[name=socialNo2]').val();
		var userId 		= $('div#payment_phone input[name=userId]').val();
		var userName 	= $('div#payment_phone input[name=userName]').val();
		var email 		= $('div#payment_phone input[name=email]').val();
		var denyEmail 	= $('div#payment_phone input[name=denyEmail]').is(':checked');
		if (denyEmail) {
			denyEmail = 'Y';
		} else {
			denyEmail = 'N';
		}
		var phoneId 	= $('div#payment_phone2 input[name=phoneId]').val();

		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.applyMobile',
					transNo: transNo,
					telecom: telecom,
					mobileNo1: mobileNo1,
					mobileNo2: mobileNo2,
					mobileNo3: mobileNo3,
					socialNo1: socialNo1,
					socialNo2: socialNo2,
					userId: userId,
					userName: userName,
					email: email,
					denyEmail: denyEmail,
					amount: BookingSeatDatas.getTotalPrice(),
					phoneId: phoneId,
					certificate: $('div#payment_phone2 input[name=certificate]').val()
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {

				that.afterApply(data);

				$('#payment_phone').fadeOut('fast');
				$('#payment_phone2').fadeOut('fast');
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
			}
		});
	},

	// Honorscard 번호 자동조회.
	checkHonorscardNumber: function(transNo, webId) {

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					//_command: 'Pay.prepareTicket',
					_command: 'Pay.checkHonorscardNumber',
					//siteCode: '36',
					siteCode: '36',
					transNo: transNo,
					webId: webId
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			//alert(data.resultCode);
			//alert(data.honorsCardNo);
			//alert(data.cardNo);
			//alert(data.cardNo1);
			//alert(data.cardNo2);
			//alert(data.cardNo3);
			//alert(data.cardNo.length);

			if (data.cardNo !== undefined) {	// 카드 넘버가 넘어오지 않을경우 그냥 넘어가도록.
				if (data.cardNo.length >= 12) {

					$('div#honorscard #honorscard-cardNo1').val(data.cardNo1);
					$('div#honorscard #honorscard-cardNo2').val(data.cardNo2);
					$('div#honorscard #honorscard-cardNo3').val(data.cardNo3);

				} else {
					messageBox(data.resultMessage);
				}
			}

		});

	},

	// 예매정보등록 - 등록이 안될 경우 장시간 입력 실패로 나옴.
	prepareBooking: function(transNo, language, memberYn) {

		var memberName = '';
		var mobileNo = '';
		var birthday = '';
		var password = '';

		// 비회원일 경우
		if (memberYn !== 'Y') {

			$('#select_pay').addClass('guest_pay');

			var nonmemberInfo = BookingSeatDatas.nonmemberInfo;

			memberName = nonmemberInfo.name;
			mobileNo = nonmemberInfo.mobileNo1 + nonmemberInfo.mobileNo2 + nonmemberInfo.mobileNo3;
			birthday = nonmemberInfo.birthday;
			password = nonmemberInfo.password;

			/*$('input:text[name="sendSmsMoblieNo1"]').val(nonmemberInfo.mobileNo1);
			$('input:text[name="sendSmsMoblieNo2"]').val(nonmemberInfo.mobileNo2);
			$('input:text[name="sendSmsMoblieNo3"]').val(nonmemberInfo.mobileNo3);*/

			// 비회원의 경우 OK캐시백 적립불가. 해당란 숨기기
			$('.payment4').hide();

		} else {

			$('#select_pay').removeClass('guest_pay');

			// 회원의 경우 OK캐시백 적립가능. 해당란 보이기
			$('.payment4').show();

		}

		// 포인트결제 구현 전까지 임시로 막음. Start
//		$('#select_pay').addClass('guest_pay');
//		$('dl.step1 dd a, dl.step2 dd a').off('click');
//		$('dl.step1 dd a, dl.step2 dd a').on('click', function() {
//
//			alert('준비중');
//		});
		// 포인트결제 구현 전까지 임시로 막음. End

		var that = this;

		var obj = BookingSeatDatas.movieData;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.prepareBooking',
					siteCode: '36',
					transNo: transNo,
					memberYn: memberYn,
					memberName: memberName,
					mobileNo: mobileNo,
					birthday: birthday,
					password: password,
					cinemaCode: obj.cinemaCode,
					screenCode: obj.screenCode,
					movieCode: obj.movieCode,
					showMovieCode: obj.showMovieCode,
					playDate: obj.playDate,
					startTime: obj.startTime,
					showSeq: obj.showSeq,
					language: language
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {

				var selectedSeats = null;
				if (BookingNonSeat.nonSeatYn === 'Y') {
					selectedSeats = BookingNonSeat.selectedSeats;
				} else {
					selectedSeats = BookingSeatDatas.selectedSeats;
				}

				for (var i=0; i < selectedSeats.length; i++) {
					that.prepareTicket(transNo, selectedSeats[i].ticketCode, selectedSeats[i].seatGroup, selectedSeats[i].seatNo);
				}
				that.getMethods(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
				$('#select_pay').modal('hide');
			}
		});
	},

	// 티켓정보등록
	prepareTicket: function(transNo, ticketCode, seatRow, seatNo) {

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.prepareTicket',
					siteCode: '36',
					transNo: transNo,
					ticketCode: ticketCode,
					seatRow: seatRow,
					seatNo: seatNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {

			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
				BookingPayment.cancelPayment();
			}
		}).error(function(data) {
			messageBox("선택한 좌석수와 선점된 좌석수가 일치하지 않습니다. 좌석을 다시 선택해주세요.");
			BookingPayment.cancelPayment();
		});
	},

	// 결제 수단 가져오기
	getMethods: function(transNo) {

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.getMethods',
					siteCode: '36',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				BookingPayment.initPaySet=data;
				BookingPayment.paySetting();
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
				BookingPayment.cancelPayment();
			}
		});
	},

	paySettingCheck:function(disableDiscount, disableCoupon, disableSettle, okCashbagBalance, joongangBalance){

		this.paySetting();

		var discount = BookingPayment.initPaySet.discount;
		var coupon = BookingPayment.initPaySet.coupon;
		var pay = BookingPayment.initPaySet.pay;

		/*
		 * paynow DB 에 결재수단으로 내려오기 전에 테스트로 스크립트에 넣어즌 로직 시잣.
		 * */
		//alert("pay.length:" + pay.length);
		//var data = [];
		////data[pay.length+1] = {"message":"", "enabled":"Y", "showPopup":"N", "code":"dcoupon_coupon"};
		//data[pay.length+1] = {"useYn":"Y", "popupMsg":"paynow 테스트", "groupSeq":"9", "popupYn":"Y", "groupCode":"pay", "gubunCode":"paynow", "gubunSeq":"3" }
		//discount.push(data);

		/* paynow DB 에 결재수단으로 내려오기 전에 테스트로 스크립트에 넣어즌 로직 끝. */

		if(disableDiscount=='Y')
		{
			for (var i=0; i < discount.length; i++) {

				if(discount[i].code=='okcb')
				{
					var target = $('#methods_'+discount[i].code).data('target');
					$('#methods_'+discount[i].code).attr('data-target', target);
					$('#methods_'+discount[i].code).css({color:"#333",fontWeight:"bold"});
				}
				else
				{
					$('#methods_'+discount[i].code).attr('data-target', '');
					$('#methods_'+discount[i].code).css({color:"#999",fontWeight:"normal"});
				}

				for(var k=0;k<this.selectPayGbn.length;k++)
				{
					if(this.selectPayGbn[k]==discount[i].code)
					{
						var target = $('#methods_'+discount[i].code).data('target');
						$('#methods_'+discount[i].code).attr('data-target', target);
						$('#methods_'+discount[i].code).css({color:"#333",fontWeight:"bold"});
					}
				}
			}
		}

		if(disableCoupon=='Y')
		{
			for (var i=0; i < coupon.length; i++) {

				$('#methods_'+coupon[i].code).attr('data-target', '');
				$('#methods_'+coupon[i].code).css({color:"#999",fontWeight:"normal"});

				for(var k=0;k<this.selectPayGbn.length;k++)
				{
					if(this.selectPayGbn[k]==coupon[i].code)

					{

						var target = $('#methods_'+coupon[i].code).data('target');
						$('#methods_'+coupon[i].code).attr('data-target', target);
						$('#methods_'+coupon[i].code).css({color:"#333",fontWeight:"bold"});
					}
				}
			}
		}

		if(disableSettle=='Y')
		{
			for (var i=0; i < pay.length; i++) {
				if(pay[i].code=='mobile' || pay[i].code=='credit' || pay[i].code=='kakaopay' || pay[i].code=='paynow' || pay[i].code=='payco')
				{
					$('#methods_'+pay[i].code).attr('data-target', '');
					$('#methods_'+pay[i].code).css({color:"#999",fontWeight:"normal"});

					for(var k=0;k<this.selectPayGbn.length;k++)
					{
						if(this.selectPayGbn[k]==pay[i].code)
						{
							var target = $('#methods_'+pay[i].code).data('target');
							$('#methods_'+pay[i].code).attr('data-target', target);
							$('#methods_'+pay[i].code).css({color:"#333",fontWeight:"bold"});
						}
					}
				}
			}
		}
		else
		{
			var isEnableCredit=false;
			var hydm_check=false;
			var culturenuri_check=false;

			for(var k=0;k<BookingPayment.selectPayGbn.length;k++){
				if(BookingPayment.selectPayGbn[k]=='hydm'){
					hydm_check=true;
				}
			}

			// 문화누리 추가.
			for(var k=0;k<BookingPayment.selectPayGbn.length;k++){
				if(BookingPayment.selectPayGbn[k]=='culturenuri'){
					culturenuri_check=true;
				}
			}

			for (var i=0; i < pay.length; i++) {
				/*
				 * 사용가능한 결제도구인지 체크
				 * 현대M카드 적용의 경우 무조건 비활성화
				 * 추후에 수정 필요
				 */
				if (pay[i].enabled === 'N' || hydm_check || culturenuri_check) {
					if(pay[i].code=='mobile' || pay[i].code=='kakaopay' || pay[i].code=='paynow' || pay[i].code=='payco')
					{
						$('#methods_'+pay[i].code).attr('data-target', '');
						$('#methods_'+pay[i].code).css({color:"#999",fontWeight:"normal"});
					}
				} else {

					if(pay[i].code=='mobile' || pay[i].code=='kakaopay' || pay[i].code=='paynow' || pay[i].code=='payco')
					{
						var target = $('#methods_'+pay[i].code).data('target');
						$('#methods_'+pay[i].code).attr('data-target', target);
						$('#methods_'+pay[i].code).css({color:"#333",fontWeight:"bold"});
					}
					else
						isEnableCredit=true;
				}
			}

			// 신용/체크카드 사용가능 체크
			if (isEnableCredit) {
				var target = $('#methods_credit').data('target');
				$('#methods_credit').attr('data-target', target);
				$('#methods_credit').css({color:"#333",fontWeight:"bold"});
			}
		}

		/*
		 * 2015.04.01
		 * 현대 M포인트 적용시
		 * 결제도구(신용카드, 모바일은 비활성화)
		 * */
		if(typeof this.applyMethods != 'undefined' && this.applyMethods != null)
		{
			for (var i=0; i < this.applyMethods.length; i++) {
				if (this.applyMethods[i].status === 'disabled') {
					$('#methods_'+this.applyMethods[i].code).attr('data-target', '');
					$('#methods_'+this.applyMethods[i].code).css({color:"#999",fontWeight:"normal"});
				} else {
					var target = $('#methods_'+this.applyMethods[i].code).data('target');
					$('#methods_'+this.applyMethods[i].code).attr('data-target', target);
					$('#methods_'+this.applyMethods[i].code).css({color:"#333",fontWeight:"bold"});
				}
			}
		}

		var okcb_check=false;
		var joongang_check=false;
		for(var k=0;k<this.selectPayGbn.length;k++)
		{
			if(this.selectPayGbn[k]=='okcb')
			{
				okcb_check=true;
				break;
			} else if(this.selectPayGbn[k]=='joongang') {
				joongang_check=true;
				break;
			}
		}

		//ok cashbag
		if(okcb_check || Number(okCashbagBalance)>0)
		{
			var target = $('#methods_okcb').data('target');
			$('#methods_okcb').attr('data-target', target);
			$('#methods_okcb').css({color:"#333",fontWeight:"bold"});
		}
		else
		{
			$('#methods_okcb').unbind();
			$('#methods_okcb').attr('data-target', '');
			$('#methods_okcb').css({color:"#999",fontWeight:"normal"});
		}

		//중앙멤버십
		if (joongang_check || Number(joongangBalance) > 0) {
			var target = $('#methods_joongang').data('target');
			$('#methods_joongang').attr('data-target', target);
			$('#methods_joongang').css({color:"#333",fontWeight:"bold"});
		} else {
			$('#methods_joongang').attr('data-target', '');
			$('#methods_joongang').css({color:"#999",fontWeight:"normal"});
		}

		if(this.selectPayGbn.length==0)
		{
			this.paySetting();
		}
	},

	paySetting:function(){
		var discount = BookingPayment.initPaySet.discount;
		var coupon = BookingPayment.initPaySet.coupon;
		var pay = BookingPayment.initPaySet.pay;

		// 사용가능한 할인수단인지 체크
		for (var i=0; i < discount.length; i++) {
			if (discount[i].enabled === 'Y') {
				var target = $('#methods_'+discount[i].code).data('target');
				$('#methods_'+discount[i].code).attr('data-target', target);
				$('#methods_'+discount[i].code).css({color:"#333",fontWeight:"bold"});

			} else {
				$('#methods_'+discount[i].code).attr('data-target', '');
				$('#methods_'+discount[i].code).css({color:"#999",fontWeight:"normal"});
			}

			if (discount[i].showPopup == 'Y') {
				var show_popup_message=discount[i].message;
				var discount_enabled=discount[i].enabled;
				var discount_code=discount[i].code;
				$('#methods_'+discount[i].code).attr('message',show_popup_message);
				$('#methods_'+discount[i].code).attr('enabledYn',discount_enabled);
				$('#methods_'+discount[i].code).attr('code',discount_code);
				$('#methods_'+discount[i].code).unbind('click');
				$('#methods_'+discount[i].code).bind('click',function() {
					var enabledYn=$(this).attr('enabledYn');
					var code=$(this).attr('code');
					messageBox($(this).attr('message'),function(){
						if(enabledYn === 'Y')
						{
							var target = $('#methods_'+code).data('target');
							$(target).fadeIn('fast');
						}
					});
				});
			}
			//알림 디자인 적용 노출
			if (discount[i].Display_EventYN == 'Y') {
				$('#methods_'+discount[i].code+' span.ing').remove();
				$('#methods_'+discount[i].code).append($('<span/>', { class:'ing'}));
			}
		}

		for (var i=0; i < coupon.length; i++) {
			// 사용가능한 결제도구인지 체크
			if (coupon[i].enabled === 'N') {
				$('#methods_'+coupon[i].code).attr('data-target', '');
				$('#methods_'+coupon[i].code).css({color:"#999",fontWeight:"normal"});
			} else {
				var target = $('#methods_'+coupon[i].code).data('target');
				$('#methods_'+coupon[i].code).attr('data-target', target);
				$('#methods_'+coupon[i].code).css({color:"#333",fontWeight:"bold"});
			}

			if (coupon[i].showPopup == 'Y') {
				var show_popup_message=coupon[i].message;
				var coupon_enabled=coupon[i].enabled;
				var coupon_code=coupon[i].code;
				$('#methods_'+coupon[i].code).attr('message',show_popup_message);
				$('#methods_'+coupon[i].code).attr('enabledYn',coupon_enabled);
				$('#methods_'+coupon[i].code).attr('code',coupon_code);
				$('#methods_'+coupon[i].code).unbind('click');
				$('#methods_'+coupon[i].code).bind('click',function() {
					var enabledYn=$(this).attr('enabledYn');
					var code=$(this).attr('code');
					messageBox($(this).attr('message'),function(){
						if(enabledYn === 'Y')
						{
							var target = $('#methods_'+code).data('target');
							$(target).fadeIn('fast');
						}
					});
				});
			}
			//알림 디자인 적용 노출
			if (coupon[i].Display_EventYN == 'Y') {
				$('#methods_'+coupon[i].code+' span.ing').remove();
				$('#methods_'+coupon[i].code).append($('<span/>', { class:'ing'}));
			}
		}

		var isEnablePoint = false;
		var isEnableCredit = false;
		BookingPayment.payMethods=pay;

		for (var i=0; i < pay.length; i++) {

			// 사용가능한 결제도구인지 체크
			if (pay[i].enabled === 'N') {
				if(pay[i].code=='mobile' || pay[i].code=='okcb' || pay[i].code=='joongang' || pay[i].code=='kakaopay' || pay[i].code=='paynow' || pay[i].code=='payco')
				{
					$('#methods_'+pay[i].code).attr('data-target', '');
					$('#methods_'+pay[i].code).css({color:"#999",fontWeight:"normal"});
				}
			} else {

				if(pay[i].code=='mobile' || pay[i].code=='okcb' || pay[i].code=='joongang' || pay[i].code=='kakaopay' || pay[i].code=='paynow' || pay[i].code=='payco')
				{
					var target = $('#methods_'+pay[i].code).data('target');
					$('#methods_'+pay[i].code).attr('data-target', target);
					$('#methods_'+pay[i].code).css({color:"#333",fontWeight:"bold"});
				}
				else
					isEnableCredit=true;
			}

			if (pay[i].showPopup == 'Y') {

				if(pay[i].code=='mobile' || pay[i].code=='okcb' || pay[i].code=='credit' || pay[i].code=='joongang' || pay[i].code=='kakaopay' || pay[i].code=='paynow' || pay[i].code=='payco')
				{
					var show_popup_message=pay[i].message;
					var pay_enabled=pay[i].enabled;
					var pay_code=pay[i].code;
					$('#methods_'+pay[i].code).attr('message',show_popup_message);
					$('#methods_'+pay[i].code).attr('enabledYn',pay_enabled);
					$('#methods_'+pay[i].code).attr('code',pay_code);
					$('#methods_'+pay[i].code).unbind('click');
					$('#methods_'+pay[i].code).bind('click',function() {
						var enabledYn=$(this).attr('enabledYn');
						var code=$(this).attr('code');

						// 비회원이고 OK케쉬백 비활성화일 경우엔 bypass
						if(BookingPayment.memberYn == 'N' && enabledYn == 'N') {}
						// 그외엔 모드 메시지 창 띄워줌
						else {
							if (code != 'kakaopay' && code != 'paynow' && code != 'payco') {
								//alert("kakaopay. paynow");
								messageBox($(this).attr('message'),function(){
									if(enabledYn === 'Y')
									{
										var target = $('#methods_'+code).data('target');
										$(target).fadeIn('fast');
										$(target).focus();
									}
								});
							}
						}
					});
				}
				else
				{
					var show_popup_message=pay[i].message;
					$('#payment_card_'+pay[i].code+'_li').attr('message',show_popup_message);
					$('#payment_card_'+pay[i].code+'_li').unbind('click');
					$('#payment_card_'+pay[i].code+'_li').bind('click',function() {
						messageBox($(this).attr('message'));
					});
				}
			} else {
				var pay_enabled=pay[i].enabled;
				var pay_code=pay[i].code;

				if(pay_code == 'okcb' && pay_enabled == 'N') {
					$('#okcashSaveCardNo').unbind('click');
					$('#okcashSaveCardNo').bind('click',function() {
						messageBox("OK캐쉬백 적립 대상이 아닙니다.");
					});
				}
			}
			//알림 디자인 적용 노출
			if (pay[i].Display_EventYN == 'Y') {
				$('#methods_'+pay[i].code+' span.ing').remove();
				$('#methods_'+pay[i].code).append($('<span/>', { class:'ing'}));
			}
		}

		// 포인트결제 사용가능 체크
		/*
		if (isEnablePoint) {
			var target = $('#methods_'+pay[i].code).data('target');
			$('#methods_point').attr('data-target', target);
			$('#methods_point').css('color', '#333');
		}
		*/

		// 신용/체크카드 사용가능 체크
		if (isEnableCredit) {
			var target = $('#methods_credit').data('target');
			$('#methods_credit').attr('data-target', target);
			$('#methods_credit').css({color:"#333",fontWeight:"bold"});
		}
		else
		{
			$('#methods_credit').attr('data-target', '');
			$('#methods_credit').css({color:"#999",fontWeight:"normal"});
		}
	},

	// 상영시간등을 최종확인
	bookingFinalCheck: function(transNo) {
		var that = this;
		//alert("결재 2 ~ Dennis ");
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.bookingFinalCheck',
					transNo: transNo,
					bypassYn: 'Y',
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				that.seatFinalCheck(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCheck=false;
				BookingPayment.executeCardCheck=false;
				messageBox( data.resultMessage);
			}
		});
	},

	// 좌석상태 최종확인
	seatFinalCheck: function(transNo) {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.seatFinalCheck',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				that.transNoFinalCheck(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCheck=false;
				BookingPayment.executeCardCheck=false;
				messageBox( data.resultMessage);
			}
		});
	},

	// 예매번호 최종확인
	transNoFinalCheck: function(transNo) {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.transNoFinalCheck',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				that.prepareBookingPay(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCheck=false;
				BookingPayment.executeCardCheck=false;
				messageBox( data.resultMessage);
			}
		});
	},

	// 결제준비
	prepareBookingPay: function(transNo) {

		if (BookingPayment.isDup) {
			messageBox('결제가 진행중입니다. 잠시만 기다려주세요.');
			return;
		}

		BookingPayment.isDup = true;

		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.prepareBookingPay',
					transNo: transNo,
					mobileNo1:$('input[name=sendSmsMoblieNo1]').val(),
					mobileNo2:$('input[name=sendSmsMoblieNo2]').val(),
					mobileNo3:$('input[name=sendSmsMoblieNo3]').val()
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {

			if (data.resultCode === '0000') {
				that.executeBookingPay(transNo);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCheck=false;
				BookingPayment.executeCardCheck=false;
				messageBox( data.resultMessage);
				that.rollbackAll(transNo);
			}
		});
	},

	// 결제실행
	executeBookingPay: function(transNo) {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.executeBookingPay',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {

			if (data.resultCode === '0000') {
				if (data.hasNext === 'Y') {
					that.executeBookingPay(transNo);
				} else {
					that.completeBookingPay(transNo);
				}
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCheck=false;
				BookingPayment.executeCardCheck=false;
				messageBox( data.resultMessage);
				that.rollbackAll(transNo);
			}
		});
	},

	// 결제완료
	completeBookingPay: function(transNo) {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.completeBookingPay',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				//BookingPaymentCheck.showPage(transNo);
				BookingPaymentDone.showPage(transNo);
				BookingPayment.isDup = false;

				BookingPayment.executeCheck=false;
				BookingPayment.executeCardCheck=false;

				/*
				 * 2015.03.04
				 * 개인화 영역 멤버십 포인트 새로고침
				 * 2015.04.07
				 * 새로고침은 회원일 경우만 처리
				 * 비회원의 경우는 패스
				 * */
				if(that.memberYn === 'Y'){

					$.ajaxSend({
						param: {
							_command: 'Membership.getRemainPoint',
						}
					,secure:'N'
						,callback:function( data ) {
							if (data.resultCode === '0000') {

								// 개인화 영역 잔여포인트 변경
								$('.login_info .point').text(numberWithCommas(data.info.remainPoint) + " P");

								// 세션에 잔여포인트 변경
								BookingPayment.saveSessionRemainPoint(data.info.remainPoint);

							} else {

								messageBox(data.resultMessage);

							}
						}
					});

				}

			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				BookingPayment.executeCheck=false;
				BookingPayment.executeCardCheck=false;
				messageBox( data.resultMessage);
				that.rollbackAll(transNo);
			}
		});
	},

	rollbackAll: function(transNo) {
		this.prepareBookingRollback(transNo);
		/* 순차적으로 RollBack 을 실행시키기 위해서. 변경. */
		//this.executeBookingRollback(transNo);
		//this.completeBookingRollback(transNo);

		BookingPayment.isDup = false;
	},

	// 예매준비 롤백
	prepareBookingRollback: function(transNo) {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.prepareBookingRollback',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {

			if (data.resultCode == '0000') {
				/* 순차적으로 RollBack 을 실행시키기 위해서. 변경. */
				that.executeBookingRollback(transNo);
				/* 순차적으로 RollBack 을 실행시키기 위해서. 변경. */

			} else if (data.resultCode != '0000') {

				if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
					return;
				}

				messageBox( data.resultMessage);
				BookingPayment.isDup = false;
			}
		});
	},

	// 실행 롤백
	executeBookingRollback: function(transNo) {

		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.executeBookingRollback',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			/*
			 * 예외로 에러가 발생해도(resultCode !== '0000') 무조건 실행
			 */

			// 결제 취소 실행
			if (data.hasNext === 'Y') {
				that.executeBookingRollback(transNo);
			} else {
			// 결제 완료 실행
			/* 순차적으로 RollBack 을 실행시키기 위해서. 변경. */
				that.completeBookingRollback(transNo);
			}
		});
	},

	// 결제완료 롤백
	completeBookingRollback: function(transNo) {

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.completeBookingRollback',
					transNo: transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode != '0000') {
				if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
					return;
				}

				messageBox( data.resultMessage);
			}
			else{
				BookingPayment.isDup = false;
				BookingPayment.afterApply(data, transNo);
			}
		});
	},

	// 결제수단 적용후 처리
	/*
	afterApply: function(data, transNo) {

		if(typeof transNo=='undefined')
		{
			var obj = BookingSeatDatas.movieData;
			transNo=obj.transNo;
		}

		var methods = data.methods;
		this.applyMethods=methods;
		var hydm_check=false;

		if(typeof data.additionalAmount == 'undefined') data.additionalAmount='0';
		if(data.additionalAmount == null) data.additionalAmount='0';
		if(data.additionalAmount == '') data.additionalAmount='0';

		if (typeof methods !== 'undefined') {

			for (var i=0; i < methods.length; i++) {
				var amountText = '';
				var code = methods[i].code;
				var amount = numberWithCommas(methods[i].amount-Number(data.additionalAmount));
				if(code == 'mcoupon' && data.additionalAmount > 0 || code == 'dcoupon_coupon' && data.additionalAmount > 0) amount = numberWithCommas(methods[i].amount);
				var count = methods[i].count;

				if(Number(methods[i].amount)>0)
				{
					if (count > 0)
						amountText = '-(' + count + '매 / ' + numberWithCommas(amount) + '원)';
					else
						amountText = '-' + numberWithCommas(amount) + '원';

					if(code=='hydm')
						hydm_check=true;

					this.selectPayGbn.push(code);

					$('#methods_'+ code).parent("dd").removeClass("point_ok");
					$('#amountText_'+code).remove();
					$('#payCancelBtn_'+code).remove();

					if(hydm_check && code=='credit')
					{
						$('#methods_'+ code).parent("dd").addClass("point_ok").append('<strong id="amountText_'+code+'"><i></i><span>'+amountText+'</span></strong>');
						$('#methods_'+ code).parent("dd").append('<button name="payCancelBtn" id="payCancelBtn_'+code+'" code="'+code+'" onclick="hydmCancel(\''+transNo+'\')">'+$('#methods_'+ code).text()+' 할인취소</button>');
					}
					else
					{
						$('#methods_'+ code).parent("dd").addClass("point_ok").append('<strong id="amountText_'+code+'"><i></i><span>'+amountText+'</span></strong>');
						$('#methods_'+ code).parent("dd").append('<button name="payCancelBtn" id="payCancelBtn_'+code+'" code="'+code+'" onclick="'+code+'Cancel(\''+transNo+'\')">'+$('#methods_'+ code).text()+' 할인취소</button>');
					}
				}
				else
				{
					$('#methods_'+ code).parent("dd").removeClass("point_ok");
					$('#amountText_'+code).remove();
					$('#payCancelBtn_'+code).remove();
					var selectPayGbnClone = this.selectPayGbn.slice(0);
					for(var k=0;k<this.selectPayGbn.length;k++)
					{
						if(selectPayGbnClone[k]==code)
						{
							selectPayGbnClone.splice(k,1);
							break;
						}
					}

					this.selectPayGbn=selectPayGbnClone;
				}

				//$('#methods_'+methods[i].code).css('color', '#999');	// disable
			}

			if(hydm_check)
			{
				var selectPayGbnClone = this.selectPayGbn.slice(0);
				for(var k=0;k<this.selectPayGbn.length;k++)
				{
					if(selectPayGbnClone[k]=='credit')
					{
						selectPayGbnClone.splice(k,1);
						break;
					}
				}

				this.selectPayGbn=selectPayGbnClone;
			}
		}

		if(Number(data.okCashbagSaveAmount)>0 && BookingPayment.okCashbagSaveCheck)
		{
			$("#okCashbagSaveAmount").html("적립대상금 : "+numberWithCommas(data.okCashbagSaveAmount)+" 원");
		}
		else if(Number(data.okCashbagSaveAmount)==0 && BookingPayment.okCashbagSaveCheck)
		{

			$("#okCashbagSaveAmount").html("적립대상금 : 0 원");
		}

		if(Number(data.okCashbagSaveAmount)>0)
		{
			$("#okcashSaveCardNo").off('click').on('click',function(){
				customMessageBox('okcashbagSavePointPopup');
			});
		}
		else
		{
			$("#okcashSaveCardNo").off('click').on('click',function(){
				messageBox('OK캐쉬백 적립할 금액이 없습니다.');
			});
		}


		this.balance = data.balance;

		$('p#totalAmount').html(numberWithCommas(data.totalAmount) + ' <span>원</span>');
		$('p#discountAmount').html('<i class="fa fa-minus-circle"></i> ' + numberWithCommas(data.discountAmount+data.couponAmount-Number(data.additionalAmount)) + ' <span>원</span>');
		$('p#settleAmount').html('<i class="fa fa-minus-circle"></i> ' + numberWithCommas(data.settleAmount) + ' <span>원</span>');
		$('p#balance').html(numberWithCommas(data.balance + ' <span>원</span>'));
		//$('b#okcashbagSaveAmount').html(numberWithCommas(data.balance));

		if(typeof data.disableDiscount !='undefined' && typeof data.disableCoupon !='undefined' && typeof data.disableSettle !='undefined')
		{
			this.paySettingCheck(data.disableDiscount, data.disableCoupon, data.disableSettle, data.okCashbagBalance, data.joongangBalance);
		}

		if(data.showPopup=='Y')
		{
			messageBox(data.popupMessage);
		}
	},
	*/

	// 결제수단 적용후 처리 (이전 버전을 원복해 봄.)
	afterApply: function(data, transNo) {

		if(typeof transNo=='undefined')
		{
			var obj = BookingSeatDatas.movieData;
			transNo=obj.transNo;
		}

		var methods = data.methods;
		this.applyMethods=methods;
		var hydm_check=false;
		/* 신용카드와 동기화해서 결제하는 부분이 현대 M 포인트와 마찬가지로 문화누리 카드도 그러하므로 추가로 로직을 구성한다.  Dennis Na. */
		var culturenuri_check=false;

		if(typeof data.additionalAmount == 'undefined') data.additionalAmount='0';
		if(data.additionalAmount == null) data.additionalAmount='0';
		if(data.additionalAmount == '') data.additionalAmount='0';

		if (typeof methods !== 'undefined') {

			//alert("methods :" + methods);
			for (var i=0; i < methods.length; i++) {
				var amountText = '';
				var code = methods[i].code;
				var amount = numberWithCommas(methods[i].amount-Number(data.additionalAmount));


				//alert("methods[i].count :" + methods[i].count);
				//alert("methods[i].code :" + methods[i].code);
				//alert("methods[i].amount :" + methods[i].amount);
				//alert("data.additionalAmount :" + data.additionalAmount);
				//alert("mcoupon 이나 dcoupon 이 아닌경우에 amount :" + amount);


				if(code == 'mcoupon' && data.additionalAmount > 0) amount = numberWithCommas(methods[i].amount);
				//alert("amount :" + amount);
				/* dcoupon_coupon 추가. */
				//if(code == 'dcoupon_coupon' && data.additionalAmount > 0) amount = numberWithCommas(methods[i].amount);
				//alert("amount :" + amount);

				var count = methods[i].count;

				if(Number(methods[i].amount)>0)
				{
					//alert("methods[i].amount :" + methods[i].amount); // :: [ 0: {amount:4000; point:0; count:1; status:normal; code:mcoupon; }]  <-- 6000 원 관람권이므로 4000 원이 할인된 셈.
																	  // :: 결국 amount 가 할임금액 임.
					if (count > 0)
						amountText = '-(' + count + '매 / ' + numberWithCommas(amount) + '원)';
					else
						amountText = '-' + numberWithCommas(amount) + '원';

					if(code=='hydm')
						hydm_check=true;
					/* 문화누리 추가하면서 credit 과 동기화 해주기 위해 추가한다. Dennis Na. */
					if(code=='culturenuri')
						culturenuri_check=true;


					this.selectPayGbn.push(code);
					//alert("code: " + code);

					$('#methods_'+ code).parent("dd").removeClass("point_ok");
					$('#amountText_'+code).remove();
					$('#payCancelBtn_'+code).remove();

					if(hydm_check && code=='credit')
					{
						$('#methods_'+ code).parent("dd").addClass("point_ok").append('<strong id="amountText_'+code+'"><i></i><span>'+amountText+'</span></strong>');
						$('#methods_'+ code).parent("dd").append('<button name="payCancelBtn" id="payCancelBtn_'+code+'" code="'+code+'" onclick="hydmCancel(\''+transNo+'\')">'+$('#methods_'+ code).text()+' 할인취소</button>');
					}
					/* credit 을 취소할때 만약에 culturenuri 와 함께 동기화해서 결제가 추가되면 credit 을 취소할때의 취소 버튼도 원래 creditCancel()이 아닌 culturenuriCancel()을 호출해줘야 하므로 현대 M포인트카드처럼 로직을 추가해 준다. - Dennis Na. */
					else if(culturenuri_check && code=='credit')
					{
						$('#methods_'+ code).parent("dd").addClass("point_ok").append('<strong id="amountText_'+code+'"><i></i><span>'+amountText+'</span></strong>');
						$('#methods_'+ code).parent("dd").append('<button name="payCancelBtn" id="payCancelBtn_'+code+'" code="'+code+'" onclick="culturenuriCancel(\''+transNo+'\')">'+$('#methods_'+ code).text()+' 할인취소</button>');
					}
					else
					{
						$('#methods_'+ code).parent("dd").addClass("point_ok").append('<strong id="amountText_'+code+'"><i></i><span>'+amountText+'</span></strong>');
						$('#methods_'+ code).parent("dd").append('<button name="payCancelBtn" id="payCancelBtn_'+code+'" code="'+code+'" onclick="'+code+'Cancel(\''+transNo+'\')">'+$('#methods_'+ code).text()+' 할인취소</button>');
					}
				}
				else
				{
					$('#methods_'+ code).parent("dd").removeClass("point_ok");
					$('#amountText_'+code).remove();
					$('#payCancelBtn_'+code).remove();
					var selectPayGbnClone = this.selectPayGbn.slice(0);
					for(var k=0;k<this.selectPayGbn.length;k++)
					{
						if(selectPayGbnClone[k]==code)
						{
							selectPayGbnClone.splice(k,1);
							break;
						}
					}

					this.selectPayGbn=selectPayGbnClone;
				}

				//$('#methods_'+methods[i].code).css('color', '#999');	// disable
			}

			if(hydm_check)
			{
				var selectPayGbnClone = this.selectPayGbn.slice(0);
				for(var k=0;k<this.selectPayGbn.length;k++)
				{
					if(selectPayGbnClone[k]=='credit')
					{
						selectPayGbnClone.splice(k,1);
						break;
					}
				}

				this.selectPayGbn=selectPayGbnClone;
				BookingPayment.hydmCheck = true;
			}
			/* 문화누리 추가. selectPayGbn AfterApply 이후에 값을 변경시켜주는 로직. */

			if(culturenuri_check)
			//alert("2029 culturenuri_check : "+ culturenuri_check);
			{
				var selectPayGbnClone = this.selectPayGbn.slice(0);
				for(var k=0;k<this.selectPayGbn.length;k++)
				{
					if(selectPayGbnClone[k]=='credit')
					{
						selectPayGbnClone.splice(k,1);
						break;
					}
				}

				this.selectPayGbn=selectPayGbnClone;
				BookingPayment.culturenuriCheck = true;
			}

		}

		if(Number(data.okCashbagSaveAmount)>0 && BookingPayment.okCashbagSaveCheck)
		{
			$("#okCashbagSaveAmount").html("적립대상금 : "+numberWithCommas(data.okCashbagSaveAmount)+" 원");
		}
		else if(Number(data.okCashbagSaveAmount)==0 && BookingPayment.okCashbagSaveCheck)
		{

			$("#okCashbagSaveAmount").html("적립대상금 : 0 원");
		}

		if(Number(data.okCashbagSaveAmount)>0)
		{
			$("#okcashSaveCardNo").off('click').on('click',function(){
				customMessageBox('okcashbagSavePointPopup');
			});
		}
		else
		{
			$("#okcashSaveCardNo").off('click').on('click',function(){
				messageBox('OK캐쉬백 적립할 금액이 없습니다.');
			});
		}


		this.balance = data.balance;

		$('p#totalAmount').html(numberWithCommas(data.totalAmount) + ' <span>원</span>');
		$('p#discountAmount').html('<i class="fa fa-minus-circle"></i> ' + numberWithCommas(data.discountAmount+data.couponAmount-Number(data.additionalAmount)) + ' <span>원</span>');
		$('p#settleAmount').html('<i class="fa fa-minus-circle"></i> ' + numberWithCommas(data.settleAmount) + ' <span>원</span>');
		$('p#balance').html(numberWithCommas(data.balance + ' <span>원</span>'));
		//$('b#okcashbagSaveAmount').html(numberWithCommas(data.balance));

		if(typeof data.disableDiscount !='undefined' && typeof data.disableCoupon !='undefined' && typeof data.disableSettle !='undefined')
		{
			this.paySettingCheck(data.disableDiscount, data.disableCoupon, data.disableSettle, data.okCashbagBalance, data.joongangBalance);
		}

		if(data.showPopup=='Y')
		{
			messageBox(data.popupMessage);
		}
	},

	// 결제초기화
	disposeBookingPay: function(transNo) {

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				data: {
					_command: 'Pay.disposeBookingPay',
					transNo: transNo
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode != '0000') {
				if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
					return;
				}
				messageBox(data.resultMessage);
			}
		});
	},

	// OK캐쉬백 적립
	saveOKCashbag: function(transNo) {

		var that = this;

		if (validate('#okcashbagSavePointPopup')) {
			var input_data = extractFormData('#okcashbagSavePointPopup');

			var ajaxOptions={
					url: getServerUrl(true) + '/DataProvider',
					type: 'POST',
					data: {
						_command: 'Pay.saveOKCashbag',
						transNo: transNo,
						cardNo1: input_data.cardNo1,
						cardNo2: input_data.cardNo2,
						cardNo3: input_data.cardNo3,
						cardNo4: input_data.cardNo4
					}
				};

			if(!lowBrowserVer)
			{
				ajaxOptions.crossDomain=true;
				ajaxOptions.xhrFields={withCredentials : true};
			}

			$.ajax(ajaxOptions).success(function( data ) {
				if (data.resultCode === '0000') {
					BookingPayment.okCashbagSaveCheck=true;
					$('#okcashSaveCardNo').html(input_data.cardNo1 + ' - **** - **** - ' + input_data.cardNo4);
					closeMessageBox('okcashbagSavePointPopup', 'custom');

					if(Number(data.okCashbagSaveAmount)>0)
					{
						$("#okCashbagSaveAmount").html("적립대상금 : "+numberWithCommas(data.okCashbagSaveAmount)+" 원");
					}
					$(".okcash_input").focus();
				} else if (data.resultCode == '0900') {
					messageBox(data.resultMessage, function() {
						location.reload(true);
					});
				} else {
					messageBox(data.resultMessage);

				}
			});
		}
	},

	cancelCreditCard: function() {
		$('div.pay_final_wrp').show();
	},

	cancelPreCreditCard: function() {
		$('div.pay_final_wrp').show();
	},

	cancelSamsungPoint: function() {
		$('div.pay_final_wrp').show();
	},

	cancelHanaSKPoint: function() {
		$('div.pay_final_wrp').show();
	},
	// 세션에 잔여포인트 저장
	saveSessionRemainPoint:function(remainPoint){
		$.ajax({
			url	  : '/pages/common/setSession.jsp',
			type  : 'POST',
			async : true,
			data  : 	{
				remainPoint:remainPoint
			}
		}).success(function( data ) {});

	}
};

var BookingPaymentCheck = {
	showPage: function(transNo) {

		try {
			_requestBookingPaymentCheck.request({
				transNo: transNo
			});
		} catch(e) {
			messageBox(e);
		}

		$('#payment').modal('show');
	}
};

var BookingPaymentDone = {
	showPage: function(transNo) {

		try {
			_requestBookingPaymentDone.request({
				transNo: transNo
			});
			// 1.상세 레이어 표시할때 타이틀 교체를 위한 이벤트 바인딩
			// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var moveTitle = '빠른예매(예매완료)' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?show=booking&p=done";
			megaboxLog.openPopup(moveURL, moveTitle);
		} catch(e) {
			messageBox(e);
		}

		if(BookingPayment.memberYn !== 'Y'){
			$('#booking_done_print').remove();
		}
		$('#booking_done').modal('show');
		$('#select_pay').modal('hide');
		// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
		$('#booking_done').off('hide.bs.modal').on('hide.bs.modal',function() {
			megaboxLog.closePopup();
		});
	},

	bookingConfirm: function() {
		var frm = document.bookingDoneFrm;

		var nonmemberInfo = BookingSeatDatas.nonmemberInfo;

		frm.memberName.value = nonmemberInfo.name;
		frm.mobileNo.value = nonmemberInfo.mobileNo1 + nonmemberInfo.mobileNo2 + nonmemberInfo.mobileNo3;
		frm.birthday.value = nonmemberInfo.birthday;
		frm.password.value = nonmemberInfo.password;

		frm.action = '/?menuId=mypage-booking';
		frm.submit();
	},

	complete: function() {
		location.href = '/';
	},
	smsReSend : function(reservationCode){
		//예매 상세내역 SMS보내기
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {
				_command: 'Booking.resendSms',
				reservationCode: reservationCode,
				siteCode: '36',
				flag : 'Y'
			}
		}).success(function( data ) {

			if (data.resultCode == '0000') {
				setTimeout(function(){messageBox( data.resultMessage);},500);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				//박스 메세지 동작 안해서 메세지박스를 닫아주고
				//closeMessageBox('messageBox', '', undefined);
	            setTimeout(function(){messageBox( data.resultMessage);},500);
			}
		});
	}

};