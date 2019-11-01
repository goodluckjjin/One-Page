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
var _requestStoreDetailView = new JSPRequest('/pages/store/Store_DetailView.jsp', 'div#store_Detailview', 'div#store_Detailview');
var _requestStoreSnackbarMenu = new JSPRequest('/pages/store/Store_SnackbarMenu.jsp', 'div#store_SnackbarMenu', 'div#store_SnackbarMenu');
var _requestStoreMenuList = new JSPRequest('/pages/store/Store_MenuList.jsp', '', '');
var _requestStorePaySummary = new JSPRequest('/pages/store/Store_PaySummary_Popup.jsp', 'div#storePaySummaryPopup', 'div#storePaySummaryPopup');
var _requestStorePayment = new JSPRequest('/pages/store/Store_Payment.jsp', 'div#storePaymentPopup', 'div#storePaymentPopup');

//hostname = hostname.replace('8080', '8443');

var StoreMain = {
	//상세보기
	showDetailView: function() {
		try {
			_requestStoreDetailView.request();
		} catch(e) {
			messageBox(e);
		}
	},

	showSnackbarMenu: function() {
		try {
			_requestStoreSnackbarMenu.request();
		} catch(e) {
			messageBox(e);
		}
	},

	showMenuList: function() {
		try {
			_requestStoreMenuList.request();
		} catch(e) {
			messageBox(e);
		}
	}
};

//스토어상세
var StoreDetail =  {
	itemCode: null,

	init: function() {
		var me = this;
		// 스토어 상세 페이지 출력  YGJOO
		$('#store_snack_detail').on('show.bs.modal', function(e) {
			var code = $(e.relatedTarget).data('code');
			var minorName = $(e.relatedTarget).data('name');
			var majorCode = $(e.relatedTarget).data('code2');
			me.itemCode = code;
			me.showPage(code, minorName);
		});
	},
	showPage: function(itemCode, minorName, moveTitle) {
		try {
			_requestStoreDetailView.request({
				itemCode: itemCode,
				minorName: minorName
			});
			var menu = "store";
			if(menuId) menu = menuId;
			var moveTitle = '스토어상세('+$("li a[data-code='"+itemCode+"']",".store_lst").closest("li").find("h5").text()+')';
			// URL 변경 및 GA 호출
			var moveURL = "/?menuId="+menu+"&p=detail&p2=" + itemCode;
			megaboxLog.openPopup(moveURL, moveTitle);

		} catch(e) {
			messageBox(e);
		}
		// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩 예제
		$('#store_snack_detail').off('hide.bs.modal').on('hide.bs.modal',function() {
			megaboxLog.closePopup();
		});
	}
};

var StoreMenuList = {
	//showPage: function(type, pageNo) {
	showPage: function() {
	var target = null;
	var container = null;
	var viewPage = 4;

		/*if (majorCode=='01') {
			if(minorCode=='01'){
				target = 'div#store_MenuList01';
				container = 'div#store_MenuList01';
			}else if(minorCode=='02'){
				target = 'div#store_MenuList02';
				container = 'div#store_MenuList02';
			}else if(minorCode=='03'){
				target = 'div#store_MenuList03';
				container = 'div#store_MenuList03';
			}else if(minorCode=='04'){
				target = 'div#store_MenuList04';
				container = 'div#store_MenuList04';
			}
		}else if(majorCode=='02') {
			target = 'div#store_MenuList0200';
			container = 'div#store_MenuList0200';
			//minorCode = '00';
			viewPage = 8;
		}else if(majorCode=='03') {
			target = 'div#store_MenuList0300';
			container = 'div#store_MenuList0300';
			//minorCode = '00';
			viewPage = 8;
		}*/

		try {
			_requestStoreMenuList.target = "div#store_MenuList";
			_requestStoreMenuList.container = "div#store_MenuList";
			_requestStoreMenuList.request({
				_rowsPerPage: viewPage
			});
		} catch(e) {
			messageBox(e);
		}
	},

	tabControl : function($this) {
		var $this = $(this);
		$this.closest("ul").find("a").removeClass("active").end().end().addClass("active");
	}
};

var StorePay = {

	transNo : null,
	amount : 0,
	executeCheck : false,
	isApplyMembershipPoint : false,
	searchMembershipPoint : false,
	isApplyOKCashbag : false,
	searchOKCashbag : false,
	isApplySKT : false,
	searchSKT : false,
	methods : {},

	showPage: function(param) {
		try {
			_requestStorePayment.request(param);

			var menu = "store";
			if(menuId) menu = menuId;
			var moveTitle = '스토어결제('+$("li a[data-code='"+param.itemCode+"']",".store_lst").closest("li").find("h5").text()+')';
			// URL 변경 및 GA 호출
			var moveURL = "/?menuId="+menu+"&p=payment&p2=" + param.itemCode;
			megaboxLog.openPopup(moveURL, moveTitle);

			$('div#store_payment').modal('show');
		} catch(e) {
			//messageBox(e);
		}
		// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩 예제
		$('#store_payment').off('hide.bs.modal').on('hide.bs.modal',function() {
			megaboxLog.closePopup();
		});

		//[netfunnel] : 상세화면 內 응모하기/예매하기  버튼 END(정상)
		NetFunnel_Complete();
	},

	init : function(transNo, amount, itemName){
		StorePay.transNo = transNo;
		StorePay.amount  = amount;

		var input = document.createElement('input');
		input.type = "hidden";
		input.name = "transNo";
		input.value = transNo;
		document.storePayForm.appendChild(input);

		var input = document.createElement('input');
		input.type = "hidden";
		input.name = "amount";
		input.value = amount;
		document.storePayForm.appendChild(input);

		var input = document.createElement('input');
		input.type = "hidden";
		input.name = "itemName";
		input.value = itemName;
		document.storePayForm.appendChild(input);

//		$('#fromPhoneSelect').trigger('change');
	},

	// 결제준비
	prepareStorePay: function() {

		var sender = $('#sender-mobileNo1').val() + $('#sender-mobileNo2').val();
		if($('#sender-mobileNo3').val())  sender+=$('#sender-mobileNo3').val();

		var receiver = $('#receiver-mobileNo1').val() + $('#receiver-mobileNo2').val();
		if($('#receiver-mobileNo3').val()) receiver+= $('#receiver-mobileNo3').val();

		var that = this;

		if (!that.paymentCheck) {
			var ajaxOptions={
					url: getServerUrl(true) + '/DataProvider',
					type: 'POST',
					async: false,
					data: {
						_command: 'Pay.prepareStorePay',
						transNo: StorePay.transNo,
						sender: sender,
						message: '',
						receiver: receiver
					}
				};

			if(!lowBrowserVer)
			{
				ajaxOptions.crossDomain=true;
				ajaxOptions.xhrFields={withCredentials : true};
			}

			$.ajax(ajaxOptions).success(function( data ) {
				if (data.resultCode === '0000') {
					that.paymentCheck=true;
					that.executeStorePay();
				} else {
					messageBox( data.resultMessage);
					that.rollbackAll();
			    	//[netfunnel] : 스토어 스토어 결제하기  버튼  END
			    	NetFunnel_Complete();
				}
			});
		}
	},


	// 결제실행
	executeStorePay: function() {
		var that = this;
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.executeStorePay',
					transNo: StorePay.transNo,
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
					that.executeStorePay();
				} else {
					that.completeStorePay();
				}
			} else {
				messageBox( data.resultMessage);
				that.rollbackAll();
		    	//[netfunnel] : 스토어 스토어 결제하기  버튼  END
		    	NetFunnel_Complete();
			}
		});
	},

	// 결제완료
	completeStorePay: function() {
		var that = this;
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.completeStorePay',
					transNo: StorePay.transNo
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode === '0000') {
				getStoreSummary(StorePay.transNo);
				that.paymentCheck=false;
			} else {
				messageBox(data.resultMessage);
				that.rollbackAll();
			}
	    	//[netfunnel] : 스토어 스토어 결제하기  버튼  END
	    	NetFunnel_Complete();
		});
	},

	// 전체 롤백
	rollbackAll: function() {
		this.prepareStoreRollback();
//		this.executeStoreRollback();
//		this.completeStoreRollback();
	},

	// 스토어준비 롤백
	prepareStoreRollback: function() {
		var that = this;
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.prepareStoreRollback',
					transNo: StorePay.transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {
				that.executeStoreRollback();
			} else if (data.resultCode !== '0000') {
				messageBox( data.resultMessage);
			}
		});
	},

	// 실행 롤백
	executeStoreRollback: function() {
		var that = this;
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.executeStoreRollback',
					transNo: StorePay.transNo,
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
					that.executeStoreRollback();
				}else {
					// 결제 완료 실행
					that.completeStoreRollback();
				}
			} else {
				messageBox( data.resultMessage);
			}
		});
	},

	// 결제완료 롤백
	completeStoreRollback: function() {
		var that = this;
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.completeStoreRollback',
					transNo: StorePay.transNo,
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {
				// 결제 완료 상태로 변경
				that.paymentCheck=false;
//				messageBox( data.resultMessage);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
				return;
			} else {
				// 결제 완료 상태로 변경
				that.paymentCheck=false;
				messageBox( data.resultMessage);
			}
		});
	},

	// 결제초기화
	disposeStorePay: function() {
		var that = this;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				data: {
					_command: 'Pay.disposeStorePay',
					transNo: StorePay.transNo
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode !== '0000') {
				messageBox(data.resultMessage);
			}

			StorePay.isApplyMembershipPoint = false;
			StorePay.searchMembershipPoint = false;
			StorePay.isApplyOKCashbag = false;
			StorePay.searchOKCashbag = false;
			StorePay.isApplySKT = false;
			StorePay.searchSKT = false;
		});
	},

	// 카카오페이 적용
	applyStoreKakaoPay: function(data) {
		var that = this;
		var transNo = data.transNo;

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				/* legacy
				data: {
					_command : 'Pay.applyStoreKakaoPay'
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
				data: {
					_command : 'Pay.applyStoreKakaoPay'
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
				that.prepareStorePay();
			} else {
				messageBox('결제오류', data.resultMessage);
		    	//[netfunnel] : 스토어 스토어 결제하기  버튼  END
		    	NetFunnel_Complete();
			}
		});
	},

	// 멤버십포인트 조회
	checkStoreMembershipPoint : function () {
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				data: {
					_command:'Pay.checkStoreMembershipPoint',
					transNo:StorePay.transNo
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if(data.resultCode=='0000')
			{
				StorePay.searchMembershipPoint = true;

				var tickets = data.tickets;
				var htmlTxt = '';
				for (var i=0; i<tickets.length; i++) {
					htmlTxt += '<option value="' + tickets[i].count + '">' + tickets[i].text + '</option>'
				}

				$("#afterPoint").text(numberWithCommas(data.after_RemainPoint));
				$("#remainpoint").text(numberWithCommas(data.remainPoint));
				$('#store_pay_memp select.select_memp').html(htmlTxt).selectpicker('refresh');
			}
			else
				messageBox(data.resultMessage);
		});
	},

	// 멤버십포인트 적용
	applyStoreMembershipPoint : function () {

		if (!StorePay.searchMembershipPoint) {
			messageBox('포인트조회를 해주세요.');
			return;
		}

		var count = $('#store_pay_memp select.select_memp').val();
		StorePay.saveStoreMembershipPoint(count);
	},

	saveStoreMembershipPoint : function (count) {
		if(!count) count = 0;
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				data: {
					_command:'Pay.applyStoreMembershipPoint',
					transNo:StorePay.transNo,
					count:count
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode=='0000') {
				if (count > 0) {
					StorePay.isApplyMembershipPoint = true;
				} else {
					StorePay.isApplyMembershipPoint = false;
				}

				StorePay.afterApply(data);
			} else {
				messageBox(data.resultMessage);
			}
		});
	},

	/**
	 * 할인쿠폰 취소
	 * @param transNo
	 */
	cancelStoreMembershipPoint : function(transNo) {
		if (!StorePay.isApplyMembershipPoint) {
			messageBox('적용된 할인 내역이 없습니다.');
			return;
		}

		StorePay.saveStoreMembershipPoint(0);
	},

	// OK 캐쉬백 조회
	checkStoreOKCashbag : function () {

		if(!validate('#store_pay_okcb')){
			return;
		}

		var data = extractFormData('#store_pay_okcb');

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				data: {
					_command:'Pay.checkStoreOKCashbag',
					transNo:StorePay.transNo,
					cardNo1:data.cardNo1,
					cardNo2:data.cardNo2,
					cardNo3:data.cardNo3,
					cardNo4:data.cardNo4
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {
				StorePay.searchOKCashbag = true;

				var tickets = data.tickets;
				var htmlTxt = '';
				for (var i=0; i<tickets.length; i++) {
					htmlTxt += '<option value="' + tickets[i].count + '">' + tickets[i].text + '</option>'
				}

				$('#store_pay_okcb select.select_okcb').html(htmlTxt).selectpicker('refresh');
				$('#store_pay_okcb strong.point_okcb').text(numberWithCommas(Number(data.remainingPoint)));

			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
			}
		});
	},

	// OK 캐쉬백 적용
	applyStoreOKCashbag : function () {

		if (!StorePay.searchOKCashbag) {
			messageBox('포인트조회를 해주세요.');
			return;
		}

		var password = $('div#store_pay_okcb input[name=password]').val().trim();
		var count = $('#store_pay_okcb select.select_okcb').val();

		if (password == '' && count != '0') {
			messageBox('패스워드를 입력하세요.');
			$('div#store_pay_okcb input[name=password]').focus();
			return;
		}

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.applyStoreOKCashbag',
					transNo: StorePay.transNo,
					password: password,
					count:count
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {
				if (count > 0) {
					StorePay.isApplyOKCashbag = true;
				} else {
					StorePay.isApplyOKCashbag = false;
				}

				StorePay.afterApply(data);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
			}
		});
	},

	// SKT 포인트 조회
	checkStoreSKT : function () {
	
		$('#storePaySkt').prop("disabled", true);
		$('#storePaySkt').removeClass();
		$('#storePaySkt').addClass('btn-m btn-st5 pull-right able')

		
		if(!validate('#store_pay_skt')){
			$('#storePaySkt').removeClass();
			$('#storePaySkt').addClass('btn-m btn-st2 pull-right able');
			$('#storePaySkt').prop("disabled", false);
			return;
		}

		var data = extractFormData('#store_pay_skt');

		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				data: {
					_command:'Pay.checkStoreTMembership',
					transNo:StorePay.transNo,
					cardNo1:data.cardNo1,
					cardNo2:data.cardNo2,
					cardNo3:data.cardNo3,
					cardNo4:data.cardNo4
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {
				StorePay.searchSKT = true;

				var tickets = data.tickets;
				var htmlTxt = '';
				for (var i=0; i<tickets.length; i++) {
					htmlTxt += '<option value="' + tickets[i].count + '">' + tickets[i].text + '</option>'
				}

				$('#store_pay_skt select.select_skt').html(htmlTxt).selectpicker('refresh');
				$('#store_pay_skt strong.point_skt').text(numberWithCommas(Number(data.remainingPoint)));

			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
			}
		}).complete(function (){
			setTimeout(function(){				
				$('#storePaySkt').removeClass();
				$('#storePaySkt').addClass('btn-m btn-st2 pull-right able');
				$('#storePaySkt').prop("disabled", false);
			},10000);
		});
	},

	// SKT 포인트 적용
	applyStoreSKT : function () {

		if (!StorePay.searchSKT) {
			messageBox('포인트조회를 해주세요.');
			return;
		}

		var sktRemainingPoint = $('#store_pay_skt strong.point_skt').text();
		var mustYn = $('#store_pay_skt [name=mustYn]').val();
		sktRemainingPoint = sktRemainingPoint ? Number(sktRemainingPoint.replaceAll(",", "")) :0;
		var $select =$('#store_pay_skt select.select_skt');
		var len =$select.find("option").length;
		var defultVal =$select.find("option:eq(0)").val();
		var val =$select.find("option:selected").val();
		if(sktRemainingPoint < 500){
			messageBox('이용가능한 포인트가 부족합니다.\n최소 이용가능 포인트는 500P입니다.');
			return;
		}else if(mustYn == 'Y' && defultVal == 0){
			messageBox('T멤버십 1일 사용 횟수를 초과하였습니다.\n예매 및 스토어 이용 가능 횟수는 1일 5회 입니다.');
			return;
		}else if(val==0){
			messageBox('포인트를 선택 해주세요.');
			return;
		}

		var count = $('#store_pay_skt select.select_skt').val();
		StorePay.saveStoreSKT(count);
	},

	saveStoreSKT : function(count){
		var ajaxOptions={
				url: getServerUrl(true) + '/DataProvider',
				type: 'POST',
				async: false,
				data: {
					_command: 'Pay.applyStoreTMembership',
					transNo: StorePay.transNo,
					count:count
				}
			};

		if(!lowBrowserVer)
		{
			ajaxOptions.crossDomain=true;
			ajaxOptions.xhrFields={withCredentials : true};
		}

		$.ajax(ajaxOptions).success(function( data ) {
			if (data.resultCode == '0000') {
				if (count > 0) {
					StorePay.isApplySKT = true;
				} else {
					StorePay.isApplySKT = false;
					StorePay.searchSKT = false;
				}

				StorePay.afterApply(data);
			} else if (data.resultCode == '0900') {
				messageBox(data.resultMessage, function() {
					location.reload(true);
				});
			} else {
				messageBox(data.resultMessage);
			}
		});
	},

	cancelStoreSKT: function(){
		if (!StorePay.isApplySKT) {
			messageBox('적용된 할인 내역이 없습니다.', function(){
				StorePay.searchSKT = false;
				var $store_pay_skt = $('#store_pay_skt');
				var collapsed = $store_pay_skt.find('#heading_store_skt').hasClass('collapsed');
				if (!collapsed) {
					$store_pay_skt.find('#heading_store_skt').click();
				}
				$store_pay_skt
					.find('strong.point_skt').text(0).end()
					.find("[name^=cardNo]").val("").end()
					.find('select.select_skt')
						.find("option:eq(0)").val("0").text("0p").prop("selected", true).end()
						.find("option:not(:eq(0))").remove().end().selectpicker('refresh');
			});
			return;
		}

		StorePay.saveStoreSKT(0);
	},

	afterApply : function(data){
		StorePay.amount = data.balance;
		document.storePayForm.amount.value = data.balance;

		$('#store_payment span.ser_point').empty();
		$('#store_balance').text(numberWithCommas(Number(StorePay.amount)));

		if (data.balance == 0) {
			$('#store_payment div.pay_type_select').parent().hide();
		} else {
			$('#store_payment div.pay_type_select').parent().show();
		}

		var methods = data.methods;
		if(methods){
			for (var i=0; i<methods.length; i++) {
				var pointTxt  = '';
				var collapsed = $('#store_pay_'+ methods[i].code).find('#heading_store_'+ methods[i].code).hasClass('collapsed');

				if (!collapsed) {
					$('#store_pay_'+ methods[i].code).find('#heading_store_'+ methods[i].code).click();
				}

				var $select = $('#store_pay_'+ methods[i].code).find('select.select_'+ methods[i].code);
				if($select.length > 0 ){
					$select.find("option[value='"+methods[i].count+"']").prop("selected", true).trigger("change");
				}

				if (methods[i].count > 0) {
					pointTxt = '( -' + numberWithCommas(Number(methods[i].point)) + 'P / -' + numberWithCommas(Number(methods[i].amount)) + '원 )';
				}else{
					if(methods[i].code=='skt'){
						$select
							.find("option:eq(0)").val("0").text("0p").prop("selected", true).end()
							.find("option:not(:eq(0))").remove().end().selectpicker('refresh');
						$('#store_pay_skt strong.point_skt').text(0);
						$('#store_pay_skt').find("[name^=cardNo]").val("");
					}
				}

				$('#store_pay_'+ methods[i].code).find('span.ser_point').text(pointTxt);

			}
		}


	}

};
