/**
 *
 */

// 메뉴 화면으로 이동하기

function print(comment, data) {
//	console.log("> " + comment);
//	console.log(data);
}


function navigate(urlString, menuId, reset)
{
	$.ajax({
		url: urlString + "?_menuId=" + menuId + "&_reset=" + reset,
		success:function(data)
		{
			$("div#main.main-content").html(data).append(
				$("<script>")
					.attr("type", "text/javascript")
					.text("initControls($('div#main.main-content'));")
			);
		},
		error:function()
		{
			alert("해당 페이지를 호출 할 수 없습니다: " + this.url);
		}
	});
}

function getData(data, name) {

	if(data instanceof Array) {
		var value = null;
		for(var i in data)
		{
			if(data[i].name == name) {
				value = data[i].name;
				break;
			}
		}
		/*
		data.forEach(function(item){
			if(item.name == name) {
				value = item.value;
				return false;
			}
		});
		*/
		return value;
	}
	else
		return data[name];

}

function putData(data, name, value)
{
	if(data instanceof Array)
	{
		var found = false;

		for(var i in data)
		{
			if(data[i].name == name) {
				found = true;
				data[i].value = value;
				break;
			}
		}
		/*
		data.forEach(function(item) {
			if(item.name == name)
			{
				found = true;
				item.value = value;
				return false;
			}
		});
		*/

		if(!found)
			data.push({name:name, value:value});
	}
	else
		data[name] = value;
}


// JSP 요청
function JSPRequest(url, target, container)
{
	this.url = url;
	this.type = "POST";
	this.dataType = "html";
	this.processing = 'replace'; // 'append'
	this.target = target;
	this.container = container;
	this.async = false;
	this.data = null;
	this.onSuccess = null;
	this.onError = null;
	this.scrollTop = 0;
	this.success = function(result)
	{
		if(this.processing == 'replace') {

			var scriptTag = document.createElement('script');
			scriptTag.text= "initControls($('" + this.target + "'));";

			$(this.target).html(result).append(scriptTag);
		}
		else {
			var scriptTag = document.createElement('script');
			scriptTag.text= "initControls($('" + this.target + "'));";

			$(this.target).append(result).append(scriptTag);
		}

// IE8에서 문제가 발생하여 위와 같이 변경
//		if(this.processing == 'replace') {
//			$(this.target).html(result).append(
//					$("<script>")
//						.attr("type", "text/javascript")
//						.text("initControls($('" + this.target + "'));")
//				);
//		}
//		else {
//			$(this.target).append(result).append(
//					$("<script>")
//						.attr("type", "text/javascript")
//						.text("initControls($('" + this.target + "'));")
//				);
//		}

		if(this.onSuccess) {
			this.onSuccess(result);
		}
	};

	this.error = function(jqXHR, textStatus, errorThrown)
    {
		if(this.onError)
			this.onError(jqXHR, textStatus, errorThrown);
		else
			//alert("해당 페이지를 호출 할 수 없습니다: " + this.url);
			throw "요청하신 페이지에 사용자가 많거나 오류가 발생하여 페이지를 찾을 수 없습니다.<br />잠시후 다시 접속하여 주시기 바랍니다.<br />이용에 불편을 드려서 죄송합니다.";
    };

    this.request = function(data)
	{
		if(this.url && this.url.indexOf("https://") > -1 && !lowBrowserVer)
		{
			this.crossDomain=true;
			this.xhrFields={withCredentials : true};
		}
		this.data = data;
		$.ajax(this);
	};

	this.put = function(name, value)
	{
		putData(this.data, name, value);
	};

	this.refresh = function()
	{
		$.ajax(this);
	};

	this.hide = function()
	{
		return $(this.container).hide();
	};

	this.show = function()
	{
		return $(this.container).show();
	};

	this.remove = function()
	{
		return $(this.container).remove();
	};
}

// 네비게이션
function NavigationController(rootRequest)
{
	var requests = new Array();

	this.root = rootRequest;
	this.top = this.root;

	this.push = function(url, data, id, css)
	{
		this.top
			.hide()
			.after($("<div>")
				.attr("id", id)
				.addClass(css)
			);
		this.top.scrollTop = $(document).scrollTop();
		var request = new JSPRequest(url, "div#" + id, "div#" + id);
		request.request(data);
		requests.push(request);
		this.top = request;
	};

	this.pop = function(refresh)
	{
		if(requests.length > 0)
		{
			var element = requests.pop();
			element.remove();
			if(requests.length > 0)
				this.top = requests[requests.length-1];
			else
				this.top = this.root;
			this.top.show();
			$(document).scrollTop(this.top.scrollTop);
			if(refresh)
				this.top.refresh();

			return element;
		}
		else
		{
			this.top = this.root;
			return null;
		}
	};

	this.popToRoot = function(refresh)
	{
		while(requests.length > 0)
		{
			requests.pop().remove();
		}
		this.top = root;
		this.top.show();
		$(document).scrollTop(this.top.scrollTop);
		if(refresh)
			this.top.refresh();

		return root;
	};

//	this.showModal = function(url, data, id, css)
//	{
//		var request = new JSPRequest(url, "div#" + id, "div#" + id);
//		request.request(data, true);
//		requests.push(request);
//		this.top = request;
//	};
//
//	this.closeModal = function(refresh)
//	{
//		if(requests.length > 0)
//		{
//			var request = requests.pop();
//			$(request.target).hide();
//
//			if(requests.length > 0)
//				this.top = requests[requests.length-1];
//			else
//				this.top = this.root;
//
//			if(refresh)
//				this.top.refresh();
//
//			return element;
//		}
//		else
//		{
//			this.top = root;
//			return null;
//		}
//	};
}

function showModal(sender, target, url, data)
{
    var $this   = $(sender);
    var $target = $(target); //strip for ie7
    var option  = 'toggle';

    $target
      .modal(option, this)
      .one('hide', function () {
        $this.is(':visible') && $this.focus();
      });

	$.ajax({
		url: url,
		data: data,
		success: function(result) {
			$target.html(result).append(
					$("<script>")
					.attr("type", "text/javascript")
					.text("initControls($('" + target + "'));")
			);
		},
		error:function()
		{
			alert("해당 페이지를 호출 할 수 없습니다: " + url);
		}
	});
}

function extractFormData(container)
{
//	$('#detail-info input, #detail-info select, #detail-info textarea').each(function() {
//		console.log($(this).prop("tagName").toLowerCase() + ":" + $(this).attr("name") + ":" + $(this).val() + ":" + $(this).data("original"));
//	});

	var data = new Object();
	var count = 0;
	$(container + ' input, ' + container + ' select, ' + container + ' textarea, ' + container + ' img').each(function() {

		if(! $(this).attr('name'))
			return;

		if($(this).prop('tagName').toLowerCase() == 'input' && $(this).attr('type').toLowerCase() == 'radio' ) {
			if($(this).is(':checked')) {
				var oldValue = $(this).data("original") + '';
				var newValue = $(this).val();
				if(oldValue != newValue) {
//					var exp = /[^[\]]+(?=])/;
//					var fieldName = $(this).attr('name').match(exp);
					var fieldName = $(this).attr('name');
					data[fieldName] = newValue;
				}
			}
		}
		else if($(this).prop('tagName').toLowerCase() == 'input' && $(this).attr('type').toLowerCase() == 'checkbox' ) {
			var checked;
			if ($(this).is(':checked'))
				checked = 'Y';
			else
				checked = 'null';

			if( checked != ($(this).data("original") + '')) {
				data[$(this).attr('name')] = checked;
			}
		}
		else if($(this).prop("tagName").toLowerCase() == 'select' && ($(this).attr('multiple') == 'multiple'))
		{ // select중 다중 선택 옵션인 경우
			var changed = false;
			$(this).find('option').each(function(){
				var original = $(this).data("original");
				if((original == 'selected' && !$(this).is(':selected')) || (original != 'selected' && $(this).is(':selected'))) {
					changed = true;
					return false;
				}
				return true;
			});
			if(changed) {
				data[$(this).attr('name')] = $(this).val();
				count++;
			}
		}
		else if($(this).prop('tagName').toLowerCase() == 'img') {
			var name= $(this).attr('name');
			data[name] = $(this).data('image');
			data[name + 'Original'] = $(this).data('original');

//console.log('data image:: ', $(this).data('image'));
//console.log('data original:: ', $(this).data('original'));

		}
		else {
			var oldValue = $(this).data('original') + '';
			var newValue = $(this).val();
			//console.log($(this).attr('name') + '=' +  oldValue + ':' + newValue + ':' + (oldValue == null) + ":" + (newValue == null));
			if(oldValue != newValue)
			{

				data[$(this).attr("name")] = newValue;
				count++;
			}
		}
	});
	data.count = count;
	print("FORM DATA:", data);
	return data;
}

function validate(container) {

	var isTrue = true;

	$(container + ' input, ' + container + ' select, ' + container + ' textarea').each(function() {
		var required = $(this).attr('required');
		if ( typeof required != 'undefined' && required != false) {
			if($.trim($(this).val()) == '') {

				var fieldName = $(this).attr('fieldname');
				isTrue = false;

				var that = this;
				messageBox(fieldName + '은(는) 필수 입력 사항입니다.', function(){
					$(that).focus();
				});

				return false;
			}
		}

		if ($(this).attr('validate') === 'email') {
			var regex = /^([a-zA-Z0-9_.+-])+\@(([a-zA-Z0-9-])+\.)+([a-zA-Z0-9]{2,4})+$/;
			if ($(this).val() !== '' && !regex.test($(this).val())) {

				isTrue = false;

				var that = this;
				messageBox('이메일 형식이 아닙니다.', function(){
					$(that).focus();
				});

				return false;
			}
		}

		if ($(this).attr('validate') === 'number') {
			var regex = /^[0-9]+$/;
			if ($(this).val() !== '' && !regex.test($(this).val())) {

				isTrue = false;

				var that = this;
				messageBox('숫자만 입력해주세요.', function(){
					$(that).focus();
				});

				return false;
			}
		}
	});

	return isTrue;
}


/*
 * header, content_wrap안에 element(modal 제외 : class에 modal 포함된)를 hide() 를 시킴
 * 'none_div' << display:none 클래스
 *  hide시에는 class 추가
 * show시에는 class 삭제
 * showYn : Y - 보이기, N - 숨기기
 * */
function nonModalShow(showYn){

	$('.content_wrap div').each(function(){

		if(showYn == "N"){

			// 모달이 아닌 div
			if(!($(this).hasClass('modal'))){
				// 숨기기
//				$(this).addClass('none_div');
			}

		}else if(showYn == "Y"){

			// 모달이 아닌 div
			if(!($(this).hasClass('modal'))){
				// 보이기
//				$(this).removeClass('none_div');
			}

		}else{
			// 없음
			;
		}

	});

}

/*
 * 메시지 박스 닫기 버튼 클릭시 onclick
 */
function messageBoxClose(){

	// 모달창이 아닌 창 모두 보이기
	nonModalShow('Y');

//	// 예매결제 상세창 팝업에게 focus()
//	$('#bookingPayment .lp_box').each(function(){
//
//		// 열려있는(보이는) 창이 있는 경우 focus
//		if($(this).is(':visible')){
//			$(this).focus();
//		}
//
//	});

}

/**
 * 메세지박스를 띄운다
 * @param title
 * @param message
 * @param fn
 * @param focusOkButton (true|false)
 */
function messageBox(title, message, fn, focusOkButton) {
//	if (typeof(scope) !== 'object') {
//		title = scope;
//		message = title;
//		fn = message;
//		focusOkButton = fn;
//	}

	var msgBoxEl = 'messageBox';
	var msgBoxElCnt = $('div[id^=messageBox]').length;

	if (msgBoxElCnt > 0)
		msgBoxEl = 'messageBox' + msgBoxElCnt;

	// 메세지박스 중복노출 방지
//	if ($('#'+msgBoxEl).length > 0) {
//		return;
//	}

	if (typeof message === 'undefined') {
		message = title;
		title = '알림';
	}

	if(typeof message === 'function') {
		fn = message;
		message = title;
		title = '알림';
	}

	message=String(message).split("\n").join("<br />");

	if (typeof fn !== 'function') {
    	focusOkButton = fn;
    }

	if (typeof focusOkButton === 'undefined' || typeof focusOkButton !== 'boolean') {
		focusOkButton = true;
	}

	var $mask = $('<div>').attr('id', 'mask').css({
		position: 'fixed',
		top: 0,
		left: 0,
		zIndex: 10100,
		backgroundColor: '#000',
		display: 'block'
	});

    var scrollAddWidth=0;
    if(navigator.userAgent.indexOf("MSIE 9") > 0 || navigator.userAgent.indexOf("MSIE 8") > 0)
    {
    	scrollAddWidth=16;
    }
    else if(navigator.userAgent.indexOf("Chrome") > 0)
    {
    	scrollAddWidth=18;
    }

	var scrollWidth = $(window.outerWidth-document.body).width()+scrollAddWidth;

	$('body').addClass('body_ovh').css('margin-right',scrollWidth).find('>header,div.sticky_header').css('margin-right',scrollWidth);
//	$('#select_seat').prepend($mask);


	// 모달창이 아닌 창 모두 숨기기
	nonModalShow("N");

	var htmlTxt = "";
	var gubun = message.indexOf("<img src=", 0);//-1은 Text
	var imgOrText  = " <div class=\"custom_alert\">";//기본값 세팅은 Text
	if(gubun != -1){
		imgOrText  = " <div class=\"custom_alert alert_img\">";//image용
	}
	htmlTxt += "<div id=\"" + msgBoxEl + "\" class=\"custom_alert_wrap\">";
	htmlTxt += imgOrText;
	htmlTxt += "		<div class=\"ca_header\">";
	htmlTxt += "			<h6>" + title + "</h6>";

	htmlTxt += "		</div>";
	htmlTxt += "		<div class=\"ca_body\">";
	htmlTxt += "			<p>" + message + "</p>";
	htmlTxt += "		</div>";
	htmlTxt += "		<div class=\"ca_footer\">";
	// 확인 버튼 클릭시 hide했던 element를 다시 보이게 처리
	htmlTxt += "			<button type=\"button\" class=\"img_btn booking ok\"\ onmousedown=\"\" autofocus onclick=\"messageBoxClose()\">확인</button>";
//	htmlTxt += "			<button type=\"button\" class=\"img_btn booking ok\"\ onmousedown=\"\" autofocus=\"autofocus\">확인</button>";
	htmlTxt += "		</div>";
	htmlTxt += "		<button type=\"button\" class=\"img_btn booking btn_ca_close\" onmousedown=\"\">알림닫기</button>";
	htmlTxt += "	</div>";
	htmlTxt += "</div>";

	var messageBoxZindex = '10110';
	if (!$('div#mask').is(':hidden')) {
		messageBoxZindex = '10120';
    }

	// 마스킹이 이미 적용되어있다면
	if ($('div#mask').length == 2) {
		$('div#mask').first().remove();
	}

//	$('body').append(htmlTxt);
	var step3 = $("#bookingPayment").children();
	var step2 = $("#bookingSeat").children();
	var step1 = $("#booking").children();

	var store = $("#store_snack_detail");
	var storeP = $("#messageBox_reAuthPopup");
	var storeP2 = $("#messageBox_authNoCheck");
	var storeP3 = $("#messageBox_mailAuthNoCheck");
	var theater = $("#select_theater");
	var selectMovie = $("#select_movie");
	var loginmodal = $("#modal_login");
	var Sloginmodal = $("#store_modal_login");
	var mycard = $("#mypage_card_add");
	var okcashbag = $("#okcashbagSavePointPopup");
	var moviedetail = $("#movie_detail");
	var bookingok= $("#booking_done");
	var storePayment= $("#store_payment");
	var joongangVipPopup = $('#joongangVipPopup');
	var passwordInitPopup = $('#passwordInitPopup');
	var messageBox_changePassword = $('#messageBox_changePassword');
	var messageBox_changePassword2 = $('#messageBox_changePassword2');
	var messageBox_IDregisterPopup = $('#messageBox_IDregisterPopup');

	var moviepost = $("#pop_moviepost_detail");
	var moviepostP = $("#messageBox_reAuthPopup");
	var moviepostP2 = $("#messageBox_authNoCheck");
	var moviepostP3 = $("#messageBox_mailAuthNoCheck");
	var Mloginmodal = $("#moviepost_modal_login");
	var nonmemberBookingPopup= $("#nonmemberBookingPopup");

	if(joongangVipPopup.text() != "" && joongangVipPopup.css("display") == "block"){
		$("#footer").prepend($mask);
		$("#footer").prepend(htmlTxt);
	} else if(passwordInitPopup.css("display") == "block"){
		passwordInitPopup.prepend(htmlTxt);
	} else if(messageBox_changePassword2.css("display") == "block"){
		messageBox_changePassword2.prepend(htmlTxt);
	} else if(messageBox_changePassword.css("display") == "block"){
		messageBox_changePassword.prepend(htmlTxt);
	} else if(messageBox_IDregisterPopup.css("display") == "block"){
		messageBox_IDregisterPopup.prepend(htmlTxt);
	} else if(step3.text() != "" && step3.css("display") == "block"){
	   if(okcashbag.css("display") == "block"){
	      $("#mask").remove();
	      $("#okcashbagSavePointPopup").prepend(htmlTxt);
	   }else{
	      $('#select_pay').prepend($mask);
	      $("#select_pay").prepend(htmlTxt);
	   }
	}else if(step2.text() != "" && step2.css("display") == "block"){
	   if(loginmodal.css("display") == "block"){
	      $('#modal_login').prepend($mask);
	      $("#modal_login").prepend(htmlTxt);
	   }else if(nonmemberBookingPopup.css("display")=="block"){
		  $('#nonmemberBookingPopup').prepend($mask);
		  $("#nonmemberBookingPopup").prepend(htmlTxt);
	   }else if(bookingok.text() != "" && bookingok.css("display") == "block"){
	      $("#booking_done").prepend($mask);
	      $("#booking_done").prepend(htmlTxt);
	   }else if(storeP.css("display") == "block"){
			storeP.prepend(htmlTxt);
	   }else if(storeP2.css("display") == "block"){
			storeP2.prepend(htmlTxt);
	   }else if(storeP3.css("display") == "block"){
			storeP3.prepend(htmlTxt);
	   }else{
	      $('#select_seat').prepend($mask);
	      $("#select_seat").prepend(htmlTxt);
	   }
	}else if(selectMovie.css("display") == "block"){
		$('#select_movie').prepend($mask);
		$("#select_movie").prepend(htmlTxt);
	}else if(theater.css("display") == "block"){
		$('#select_theater').prepend($mask);
		$("#select_theater").prepend(htmlTxt);
	}else if(step1.text() != "" && step1.css("display") == "block"){
		$('#reservation').prepend($mask);
		$("#reservation").prepend(htmlTxt);
	}else if(store.css("display") == "block"){
		if(Sloginmodal.css("display") == "block"){
			$("#store_modal_login").prepend($mask);
			$("#store_modal_login").prepend(htmlTxt);
		}else if(storeP.css("display") == "block"){
			storeP.prepend(htmlTxt);
		}else if(storeP2.css("display") == "block"){
			storeP2.prepend(htmlTxt);
		}else if(storeP3.css("display") == "block"){
			storeP3.prepend(htmlTxt);
		}else if(storePayment.css("display") == "block"){
			storePayment.prepend(htmlTxt);
		}else{
			$('#store_snack_detail').prepend($mask);
			$("#store_snack_detail").prepend(htmlTxt);
		}
	}else if(moviepost.css("display") == "block"){
		if(Mloginmodal.css("display") == "block"){
			$("#moviepost_modal_login").prepend($mask);
			$("#moviepost_modal_login").prepend(htmlTxt);
		}else if(moviepostP.css("display") == "block"){
			moviepostP.prepend(htmlTxt);
		}else if(moviepostP2.css("display") == "block"){
			moviepostP2.prepend(htmlTxt);
		}else if(moviepostP3.css("display") == "block"){
			moviepostP3.prepend(htmlTxt);
		}else{
			$('#pop_moviepost_detail').prepend($mask);
			$("#pop_moviepost_detail").prepend(htmlTxt);
		}
	}else if(mycard.css("display") == "block"){
		$("#mypage_card_add").prepend($mask);
		$("#mypage_card_add").prepend(htmlTxt);
	}else if(storeP.css("display") == "block"){
		storeP.prepend(htmlTxt);
	}else if(storeP2.css("display") == "block"){
			storeP2.prepend(htmlTxt);
	}else if(storeP3.css("display") == "block"){
			storeP3.prepend(htmlTxt);
	}else{
		if ($('div#mask').length == 0) {
			$('body').prepend($mask);
		}
		$('body').prepend(htmlTxt);
	}

//	else if(step1.text() != "" && step1.css("display") == "block")
//		step1.find("#").prepend(htmlTxt);
//	if($("#bookingSeat"))
//		$("#bookingPayment")
//	$('body').prepend(htmlTxt);

	$('#'+msgBoxEl+' .layerPopupImgLoad').load(function(){
		layerPopupImgLoad(msgBoxEl);
	});

	$('#'+msgBoxEl+' button.img_btn').on('click', function() {
		closeMessageBox(msgBoxEl, '', fn);

		if(!$('body').hasClass('body_ovh')){
			nonModalShow('Y');
		}
		/*
		 * TODO:메시지 박스가 아직 존재할 경우
		 * 해당 메시지 박스 버튼에 포커스
		 * */


	});

	// 화면의 너비와 높이를 구함.
    var maskHeight = $(document).height();
    //var maskWidth = $(window).width();

    // 전체 스크린에 마스크를 적용한다.
    $('#mask').css({'width':'100%','height':maskHeight});

    // 마스크 효과적용
    //$('#mask').fadeIn(300); // 걸리는시간 0.3초
    $('#mask').fadeTo("fast", 0.5); // 천천히 투명도 50%

    // 효과적용
    $('#'+msgBoxEl).fadeIn(700);

    // 윈도우 너비와 높이를 구함.
    var winH = $(window).height();
    var winW = $(window).width();

    // 팝업을 윈도우 중앙에 배치
    $('#'+msgBoxEl+' .custom_alert').css('position','relative');//높이를 같기전 초기화
    var msgBoxHeight = $('#'+msgBoxEl+' .custom_alert').height();
    var msgBoxWidth  = $('#'+msgBoxEl+' .custom_alert').width();

    $('#'+msgBoxEl+' .custom_alert').css('position','absolute');
    $('#'+msgBoxEl+' .custom_alert').css('top',  (winH - msgBoxHeight) / 2);
    $('#'+msgBoxEl+' .custom_alert').css('left', (winW - msgBoxWidth) / 2);

    $('#'+msgBoxEl).height($(document).innerHeight());
    $('#'+msgBoxEl).css('z-index', messageBoxZindex);
    $('#'+msgBoxEl+' button.ok').focus();

//    $("body *").focus(function(){
//    	alert($("this").attr("class"));
//    	$(this).attr("test","test");
//    });

	if (focusOkButton) {

		if($('#'+msgBoxEl+' button.ok')[0] != undefined){
			$('#'+msgBoxEl+' button.ok')[0].focus();

		}else{
			$('#'+msgBoxEl+' button.ok').focus();
		}
    }

	return $('#' + msgBoxEl);
}

/**
 * 이미지용 메세지박스를 띄운다
 * @param title
 * @param message
 * @param fn
 * @param focusOkButton (true|false)
 */
function imageMessageBox(title, message, fn, focusOkButton) {
	
	var msgBoxEl = 'messageBox';
	var msgBoxElCnt = $('div[id^=messageBox]').length;
	
	if (msgBoxElCnt > 0)
		msgBoxEl = 'messageBox' + msgBoxElCnt;
	
	if (typeof message === 'undefined') {
		message = title;
		title = '알림';
	}
	
	if(typeof message === 'function') {
		fn = message;
		message = title;
		title = '알림';
	}
	
	message=String(message).split("\n").join("<br />");
	
	if (typeof fn !== 'function') {
		focusOkButton = fn;
	}
	
	if (typeof focusOkButton === 'undefined' || typeof focusOkButton !== 'boolean') {
		focusOkButton = true;
	}
	
	var $mask = $('<div>').attr('id', 'mask').css({
		position: 'fixed',
		top: 0,
		left: 0,
		zIndex: 10100,
		backgroundColor: '#000',
		display: 'block'
	});
	
	var scrollAddWidth=0;
	if(navigator.userAgent.indexOf("MSIE 9") > 0 || navigator.userAgent.indexOf("MSIE 8") > 0)
	{
		scrollAddWidth=16;
	}
	else if(navigator.userAgent.indexOf("Chrome") > 0)
	{
		scrollAddWidth=18;
	}
	
	var scrollWidth = $(window.outerWidth-document.body).width()+scrollAddWidth;
	
	$('body').addClass('body_ovh').css('margin-right',scrollWidth).find('>header,div.sticky_header').css('margin-right',scrollWidth);
	
	// 모달창이 아닌 창 모두 숨기기
	nonModalShow("N");
	
	var htmlTxt = "";
	var imgOrText  = " <div class=\"custom_alert alert_img\">";//image용

	htmlTxt += "<div id=\"" + msgBoxEl + "\" class=\"custom_alert_wrap\" >";
	htmlTxt += imgOrText;
	htmlTxt += "		<div class=\"ca_header\">";
	htmlTxt += "			<h6>" + title + "</h6>";
	
	htmlTxt += "		</div>";
	htmlTxt += "		<div class=\"ca_body\">";
	htmlTxt += "			<p><img src=" + message + " id=\"imageMessageBox\"></p>";
	htmlTxt += "		</div>";
	htmlTxt += "	</div>";
	htmlTxt += "</div>";
	
	var messageBoxZindex = '10110';
	if (!$('div#mask').is(':hidden')) {
		messageBoxZindex = '10120';
	}
	
	// 마스킹이 이미 적용되어있다면
	if ($('div#mask').length == 2) {
		$('div#mask').first().remove();
	}
	

	$('body').prepend($mask);
	$('body').prepend(htmlTxt);
	
	$('#'+msgBoxEl+' .layerPopupImgLoad').load(function(){
		layerPopupImgLoad(msgBoxEl);
	});
	
	$('#'+msgBoxEl+' button.img_btn').on('click', function() {
		closeMessageBox(msgBoxEl, '', fn);
		
		if(!$('body').hasClass('body_ovh')){
			nonModalShow('Y');
		}
		
	});
	
	// 화면의 너비와 높이를 구함.
	var maskHeight = $(document).height();
	
	// 전체 스크린에 마스크를 적용한다.
	$('#mask').css({'width':'100%','height':maskHeight});
	
	// 마스크 효과적용
	//$('#mask').fadeIn(300); // 걸리는시간 0.3초
	$('#mask').fadeTo("fast", 0.5); // 천천히 투명도 50%
	
	// 효과적용
	$('#'+msgBoxEl).fadeIn(700);
	
	
	if (focusOkButton) {
		
		if($('#'+msgBoxEl+' button.ok')[0] != undefined){
			$('#'+msgBoxEl+' button.ok')[0].focus();
			
		}else{
			$('#'+msgBoxEl+' button.ok').focus();
		}
	}
	
	// 이미지 팝업 위치 조절
	$('#imageMessageBox').load(function(){
		// 윈도우 너비와 높이를 구함.
		var winH = $(window).height();
		var winW = $(window).width();
		// 팝업을 윈도우 중앙에 배치
		$('#'+msgBoxEl+' .custom_alert').css('position','relative');//높이를 같기전 초기화
		var msgBoxHeight = $('#'+msgBoxEl+' .custom_alert').height();
		var msgBoxWidth  = $('#'+msgBoxEl+' .custom_alert').width();
		
		$('#'+msgBoxEl+' .custom_alert').css('position','absolute');
		$('#'+msgBoxEl+' .custom_alert').css('top', (winH - msgBoxHeight) / 2);
		$('#'+msgBoxEl+' .custom_alert').css('left',(winW - msgBoxWidth) / 2 );
		
		$('#'+msgBoxEl).height($(document).innerHeight());
		$('#'+msgBoxEl).css('z-index', messageBoxZindex);
	});
}

function layerPopupImgLoad(msgBoxEl)
{
    var winH = $(window).height();
    var winW = $(window).width();

    $('#'+msgBoxEl+' .custom_alert').css('position','relative');//높이를 같기전 초기화
    var msgBoxHeight = $('#'+msgBoxEl+' .custom_alert').height();
    var msgBoxWidth  = $('#'+msgBoxEl+' .custom_alert').width();

    $('#'+msgBoxEl+' .custom_alert').css('position','absolute');
    $('#'+msgBoxEl+' .custom_alert').css('top',  (winH - msgBoxHeight) / 2);
    $('#'+msgBoxEl+' .custom_alert').css('left', (winW - msgBoxWidth) / 2);
}

function confirmBox(title, message, yesfn, nofn) {
	message=String(message).split("\n").join("<br />");

	var msgBoxEl = 'messageBox';
	var msgBoxElCnt = $('div[id^=messageBox]').length;

	if (msgBoxElCnt > 0)
		msgBoxEl = 'messageBox' + msgBoxElCnt;

	var $mask = $('<div>').attr('id', 'mask').css({
		position: 'fixed',
		top: 0,
		left: 0,
		zIndex: 10100,
		backgroundColor: '#000',
		display: 'block'
	});

	var scrollWidth = window.outerWidth-document.body.clientWidth;
	$('body').addClass('body_ovh').css('margin-right',scrollWidth).find('>header,div.sticky_header').css('margin-right',scrollWidth);


	var htmlTxt = "";
	var gubun = message.indexOf("<img src=", 0);//-1은 Text
	var imgOrText  = " <div class=\"custom_alert\">";//기본값 세팅은 Text
	if(gubun != -1){
		imgOrText  = " <div class=\"custom_alert alert_img\">";//image용
	}
	htmlTxt += "<div id=\"" + msgBoxEl + "\" class=\"custom_alert_wrap\" tabindex='0'>";
	htmlTxt += imgOrText;
	htmlTxt += "		<div class=\"ca_header\">";
	htmlTxt += "			<h6>" + title + "</h6>";
	htmlTxt += "		</div>";
	htmlTxt += "		<div class=\"ca_body\">";
	htmlTxt += "			<p>" + message + "</p>";
	htmlTxt += "		</div>";
	htmlTxt += "		<div class=\"ca_footer\">";
	if ( title == "지점확인"){
		htmlTxt += "			<button type=\"button\" class=\"img_btn booking cancel\"\ onmousedown=\";\">취소</button>";			
		htmlTxt += "			<button type=\"button\" class=\"img_btn booking ok\"\ onmousedown=\"\">계속구매</button>";
	}else{
		htmlTxt += "			<button type=\"button\" class=\"img_btn booking ok\"\ onmousedown=\"\">확인</button>";
		htmlTxt += "			<button type=\"button\" class=\"img_btn booking cancel\"\ onmousedown=\";\">취소</button>";		
	}
	htmlTxt += "		</div>";
	htmlTxt += "		<button type=\"button\" class=\"img_btn booking btn_ca_close\" onmousedown=\"\" onclick=\"closeMessageBox('"+msgBoxEl+"', '', " + nofn + ");return false;\">닫기</button>";
	htmlTxt += "	</div>";
	//htmlTxt += "	<span class=\"bg_custom_alert\"></span>";
	htmlTxt += "</div>";

	var messageBoxZindex = '10110';
	if (!$('div#mask').is(':hidden')) {
		messageBoxZindex = '10120';
    }
	var step3 = $("#bookingPayment").children();
	var step2 = $("#bookingSeat").children();
	var step1 = $("#booking").children();

	var store = $("#store_snack_detail");
	var storeP = $("#messageBox_reAuthPopup");
	var storeP2 = $("#messageBox_authNoCheck");
	var storeP3 = $("#messageBox_mailAuthNoCheck");
	var theater = $("#select_theater");
	var selectMovie = $("#select_movie");
	var loginmodal = $("#modal_login");
	var Sloginmodal = $("#store_modal_login");
	var mycard = $("#mypage_card_add");
	var okcashbag = $("#okcashbagSavePointPopup");
	var moviedetail = $("#movie_detail");
	var bookingok= $("#booking_done");

	if(step3.text() != "" && step3.css("display") == "block"){
	   if(okcashbag.css("display") == "block"){
	      $("#mask").remove();
	      $("#okcashbagSavePointPopup").prepend(htmlTxt);
	   }else{
	      $('#select_pay').prepend($mask);
	      $("#select_pay").prepend(htmlTxt);
	   }
	}else if(step2.text() != "" && step2.css("display") == "block"){
	   if(loginmodal.css("display") == "block"){
	      $('#modal_login').prepend($mask);
	      $("#modal_login").prepend(htmlTxt);
	   }else if(bookingok.text() != "" && bookingok.css("display") == "block"){
	      $("#booking_done").prepend($mask);
	      $("#booking_done").prepend(htmlTxt);
	   }else if(storeP.css("display") == "block"){
			storeP.prepend(htmlTxt);
	   }else if(storeP2.css("display") == "block"){
			storeP2.prepend(htmlTxt);
	   }else if(storeP3.css("display") == "block"){
			storeP3.prepend(htmlTxt);
	   }else{
	      $('#select_seat').prepend($mask);
	      $("#select_seat").prepend(htmlTxt);
	   }
	}else if(selectMovie.css("display") == "block"){
		$('#select_movie').prepend($mask);
		$("#select_movie").prepend(htmlTxt);
	}else if(theater.css("display") == "block"){
		$('#select_theater').prepend($mask);
		$("#select_theater").prepend(htmlTxt);
	}else if(step1.text() != "" && step1.css("display") == "block"){
		$('#reservation').prepend($mask);
		$("#reservation").prepend(htmlTxt);
	}else if(store.css("display") == "block"){
		if(Sloginmodal.css("display") == "block"){
			$("#store_modal_login").prepend($mask);
			$("#store_modal_login").prepend(htmlTxt);
		}else if(storeP.css("display") == "block"){
			storeP.prepend(htmlTxt);
		}else if(storeP2.css("display") == "block"){
			storeP2.prepend(htmlTxt);
		}else if(storeP3.css("display") == "block"){
			storeP3.prepend(htmlTxt);
		}else{
			$('#store_snack_detail').prepend($mask);
			$("#store_snack_detail").prepend(htmlTxt);
		}
	}else if(mycard.css("display") == "block"){
		$("#mypage_card_add").prepend($mask);
		$("#mypage_card_add").prepend(htmlTxt);
	}else if(moviedetail.css("display") == "block"){
		if(step1.text() != ""){
			$('#reservation').prepend($mask);
			$("#reservation").prepend(htmlTxt);
		}else{
			$("#movie_detail").prepend($mask);
			$("#movie_detail").prepend(htmlTxt);
		}
	}else if(storeP.css("display") == "block"){
		storeP.prepend(htmlTxt);
	}else if(storeP2.css("display") == "block"){
			storeP2.prepend(htmlTxt);
	}else if(storeP3.css("display") == "block"){
			storeP3.prepend(htmlTxt);
	}else{
		$('body').prepend($mask);
		$('body').prepend(htmlTxt);
	}
	$('#'+msgBoxEl+' button.img_btn.ok').on('click', function() {
		closeMessageBox(msgBoxEl, '', yesfn);
	});

	$('#'+msgBoxEl+' button.img_btn.cancel').on('click', function() {
		closeMessageBox(msgBoxEl, '', nofn);
	});

	// 화면의 너비와 높이를 구함.
    var maskHeight = $(document).height();
    //var maskWidth = $(window).width();

    // 전체 스크린에 마스크를 적용한다.
    $('#mask').css({'width':'100%','height':maskHeight});

    // 마스크 효과적용
    //$('#mask').fadeIn(300); // 걸리는시간 0.3초
    $('#mask').fadeTo("fast", 0.8); // 천천히 투명도 70%

    $('#'+msgBoxEl).fadeIn("slow", function(){
    	$('#'+msgBoxEl+' button.ok').focus();
    });

    // 윈도우 너비와 높이를 구함.
    var winH = $(window).height();
    var winW = $(window).width();

    // 팝업을 윈도우 중앙에 배치
    var msgBoxHeight = $('#'+msgBoxEl+' .custom_alert').height();
    var msgBoxWidth  = $('#'+msgBoxEl+' .custom_alert').width();
    if(msgBoxWidth == 0){
    	msgBoxHeight = 183;
    	msgBoxWidth = 510;
    }
    $('#'+msgBoxEl+' .custom_alert').css('top',  (winH - msgBoxHeight) / 2);
	$('#'+msgBoxEl+' .custom_alert').css('left', (winW - msgBoxWidth) / 2);


    $('#'+msgBoxEl).css('z-index', messageBoxZindex);

    $('#'+msgBoxEl).height($(document).innerHeight());

    if (yesfn) {
		if($('#'+msgBoxEl+' button.ok')[0] != undefined){
			$('#'+msgBoxEl+' button.ok')[0].focus();
		}else{
			$('#'+msgBoxEl+' button.ok').focus();
		}
    }



    return $('#' + msgBoxEl);
}

/**
 * 	id		element id
 *	topAdj	top위치 조정
 *	left	left위치 조정
 */
function customMessageBox(id, topAdj, leftAdj) {

	if (typeof topAdj === 'undefined') {
		topAdj = 0;
	}
	if (typeof leftAdj === 'undefined') {
		leftAdj = 0;
	}

	var $mask = $('<div>').attr('id', 'mask').css({
		position: 'fixed',
		top: 0,
		left: 0,
		zIndex: 10000,
		backgroundColor: '#000',
		opacity: 0.8,
		display: 'block'
	});

	var scrollAddWidth=0;
    if(navigator.userAgent.indexOf("MSIE 9") > 0 || navigator.userAgent.indexOf("MSIE 8") > 0)
    {
    	scrollAddWidth=16;
    }
    else if(navigator.userAgent.indexOf("Chrome") > 0)
    {
    	scrollAddWidth=18;
    }

	var scrollWidth = $(window.outerWidth-document.body).width()+scrollAddWidth;
	$('body').addClass('body_ovh').css('margin-right',scrollWidth).find('>header,div.sticky_header').css('margin-right',scrollWidth);
	$('body').prepend($mask);

	// 화면의 너비와 높이를 구함.
    var maskHeight = $(document).height();
    //var maskWidth = $(window).width();

    // 전체 스크린에 마스크를 적용한다.
    $('#mask').css({'width':'100%','height':maskHeight});

    // 마스크 효과적용
    //$('#mask').fadeIn(300); // 걸리는시간 0.3초
    $('#mask').fadeTo("fast", 0.8); // 천천히 투명도 80%

	$('div#'+id).show();

    // 윈도우 너비와 높이를 구함.
    var winH = $(window).height();
    var winW = $(window).width();

    var msgBoxHeight = $('div#' + id + ' .custom_alert').height();
    var msgBoxWidth = $('div#' + id + ' .custom_alert').width();

    // 팝업을 윈도우 중앙에 배치
    //$('div#' + id + ' .custom_alert').css('top',  (winH - msgBoxHeight)/2 + $(document).scrollTop() + (topAdj));
    //$('div#' + id + ' .custom_alert').css('left', (winW - msgBoxWidth) / 2 + (leftAdj));
	var top = (winH / 2 - msgBoxHeight / 2);

	// 윈도우 top위치가 0보다 작다면
	if (top < 0) {
		top = 0;
		$('div#' + id + ' .custom_alert').css('margin', '30px 0');
	}

    $('div#' + id + ' .custom_alert').css('top',  top + (topAdj));
    $('div#' + id + ' .custom_alert').css('left', winW / 2 - msgBoxWidth / 2 + (leftAdj));

    var messageBoxZindex = '10010';
	if (!$('div#mask').is(':hidden')) {
		messageBoxZindex = '10020';
    }

//	if ($('div#mask').length == 2) {
//		$('div#mask').first().remove();
//	}

    $('div#'+id).css('z-index', messageBoxZindex);

    //$('div#'+id).height($(document).innerHeight());

    //$('div#'+id).keyBlock();

    // 효과적용
    //$('div#'+id).fadeIn(700);
    if(id == "okcashbagSavePointPopup"){
    	$("#mask").hide();
    	$("#"+id).css({position:"fixed"}).find("#ok_cashbag_card input:first").focus();
    }
}

function closeMessageBox(id, type, fn) {
//	if (typeof scope !== 'object') {
//		id = scope;
//		type = id;
//		fn = type;
//	}
	if (type === 'custom') {
		$('div#' + id).fadeOut();

		resetInput(id);

		// 커스텀박스가 하나만 있을 경우만 마스크 제거
		if ($('.custom_alert_wrap:visible').length == 1) {
			// .pop_con 유형에 팝업이 없을 경우만 body에 스크롤을 제거한다.
			if ($('.pop_con:visible').length == 0) {
				// 부트스트랩 모달창이 떠있지 않다면
				if (!$('body').hasClass('modal-open')) {
					$('body').removeClass('body_ovh').css('margin-right', 0).find('>header,div.sticky_header').css('margin-right', 0);
				} else {
					$('body').removeClass('body_ovh');
				}
			}
			$('div#mask').remove();
		}
		$('div#' + id).hide();
	} else {

		$('div#' + id).fadeOut(function() {
			$('div#' + id).remove();
		});

		// 메세지박스가 하나만 있을 경우만 마스크 제거
		if ($('div[id^=messageBox].custom_alert_wrap:visible').length == 1) {
			// 부트스트랩 모달창이 떠있지 않다면
			if (!$('body').hasClass('modal-open')) {
				$('body').removeClass('body_ovh').css('margin-right', 0).find('>header,div.sticky_header').css('margin-right', 0);
			} else {
				$('body').removeClass('body_ovh');
			}
			$('div#mask').remove();
//			if( $('div#mask').length == 1){
//				$('div#mask').remove();
//
//			} else {
//				$('div#mask').first().remove();
//			}

//			$('div[id*=modal_login]').each(function(){
//				if($(this).css('display') == 'block'){
//					var popupZindex = parseInt($(this).css('z-index'));
//
//					$('div#mask').css('z-index', popupZindex-1);
//					return false;
//				}
//			});

		}
	}


	if (typeof fn === 'function') {
		fn();
	}
}

function resetInput(id) {
	// 입력값들을 초기화
	$('div#'+id+' input:text').val('');
	$('div#'+id+' input:password').val('');
	$('div#'+id+' input:radio:first').iCheck('check');
	$('div#'+id+' input:checkbox').iCheck('uncheck');
}

function showMenu(menuId) {
	if(menuId=='movie-festival'){
		// 영화 > 나의 무비스토리 인 경우 처리
		movieFestivalMoveYN(menuId, arguments);
	}else{
		showMenuMove(menuId, arguments);
	}
}
function showMenuMove(menuId, arg) {

	$("#mainFrm input[name=menuId]").val(menuId);
	if (arg.length > 1) {
		for (var i=1; i < arg.length; i++) {
			if (i == 1) {
				$('form#mainFrm').append("<input type='hidden' name='p' value='" + arg[i] + "' />");
			} else {
				$('form#mainFrm').append("<input type='hidden' name='p"+i+"' value='" + arg[i] + "' />");
			}
		}
	}
	$('#mainFrm').submit();

}

function movieFestivalMoveYN (menuId, arg){
	$.ajax({
		url: '/DataProvider',
		type: 'POST',
		data: {_command: 'Movie.getFestivalCount', siteCode: '36', count :0 }
	}).success(function( data ) {
		if(data.count > 0) showMenuMove(menuId, arg);
		else {
			messageBox("등록된 영화제가 없습니다.");
		}
	});
	return false;
}
function showMenuLoginCheck(menuId){
	loginCheck(function( data ) {
		if (data.resultCode === 'success') {
			showMenu(menuId);
		} else {
			messageBox("로그인 후 이용 가능합니다");
		}
	});
	return false;
}

/**
 * 텍스트박스 입력값 제한
 * [Usage]
 * 필요속성: alloctype(허용문자형),  blocktype: 불허문자형 두가지 타입(alloc, block) 중 택일
 * 허용속성값: number|alphabet|hangul
 * 사용예:
 * 			<div id="container">
 * 				<input type="text" name="name" 	alloctype="hangul" />
 * 				<input type="text" name="id" 	blocktype="hangul" />
 * 				<input type="text" name="phone" alloctype="number" />
 * 			</div>
 *
 * 			$('#container').keyBlock();
 *
 * [추가기능!]
 * 설정한 maxlength값에 도달하면 자동으로 다음 text필드로 포커싱.
 * 필요요소: name, maxlength
 * @param $
 */
(function($) {
	"use strict";
	$.fn.keyBlock = function() {

		//if($.attr('id')=='ok_cashbag_card')


		var $this = this;

		//console.log($this.attr('id'));

		var NUMBER = 'number';
		var HANGUL = 'hangul';
		var ALPHABET = 'alphabet';
		var SPECIAL = 'special';
		var COMMAND = 'command';
		var OTHER = 'other';

		var getInputType = function(e, val) {
			if (/[`~!@+_#$%^&*|\\\'\";:\/?]/gi.test(val)) {
				return SPECIAL;
			}

			// number check
//			if(/[0-9]/.test(val)) {
			if (e.which >= 48 && e.which <= 57) {
				return NUMBER;
			}
			// 키패드
			if (e.which >= 96 && e.which <= 105) {
				return NUMBER;
			}

			// hangul check
			if(/[ㄱ-ㅎ|ㅏ-ㅣ|가-힣]/.test(val)) {
				return HANGUL;
			}

			// alphabet check
//			if(/[a-z|A-Z]/.test(val)) {
			if ((e.which >= 65 && e.which <= 90) || (e.which >= 97 && e.which <= 122)) {
				return ALPHABET;
			}

			if ( e.which == 8 // back space
					|| e.which == 9 // Tab
					|| e.which == 13 // Enter
					|| e.which == 16 // Shift
					) {
				return COMMAND;
			}
			return OTHER;
		};

		var isValidate = function(type, charType, inputType) {

//console.log('inputType: ', inputType);

			if (inputType === OTHER) {
				return false;
			}

			if (inputType === COMMAND) {
				return true;
			}

			if (type === 'BLOCK') {
				if (inputType === charType)
					return false;
			} else if (type === 'ALLOW') {
				if (inputType !== charType)
					return false;
			}
			return true;
		};

		var getCheckType = function(blocktypes, allowtypes) {
			if (typeof blocktypes !== 'undefined') {
				return 'BLOCK';
			}
			if (typeof allowtypes !== 'undefined') {
				return 'ALLOW';
			}
		};

		$(this).find('input:text, input:password').each(function() {

			$(this).off('keyup');
			$(this).on('keyup', function(e) {
				var $selected = $(this);
				var autoFocusTarget = 'input:text:not([readonly]), input:password:not([readonly])'; // 자동으로 포커싱 될 대상

				// 입력길이가 maxlength값일 경우 다음 입력박스로 자동focus해준다.
				if ($(this).val().length === parseInt($(this).attr('maxlength'))) {
					if (getInputType(e) === COMMAND) {
						return;
					}

					var total = $this.find(autoFocusTarget).length;

					$this.find(autoFocusTarget).each(function(idx) {
						if($selected.attr('name') === $(this).attr('name')) {
							if (idx !== (total - 1)) {
								$this.find(autoFocusTarget).get(idx+1).focus();
							}
						}
					});
				}

				// keyenter속성 처리
				var keyenter = $selected.attr('keyenter');
				if (typeof keyenter !== 'undefined' && keyenter != false) {

					if (window.event) {
						if (e.keyCode == 13) {
							$(keyenter).click();
						}
					}
					else
					{
						if (e.which == 13) {
							$(keyenter).click();
						}
					}
				}

				var val = $(this).val();
				var inputType = getInputType(e, val); // 입력값 유형을 가져온다.

				if (typeof inputType === 'undefined') {
					return false;
				}

				var blocktypes = $(this).attr('blocktype');
				var allowtypes = $(this).attr('allowtype');

				var chartypes = blocktypes||allowtypes;

				if (typeof chartypes == 'undefined') {
					return true;
				}

				if(typeof allowtypes=='undefined') allowtypes='';

				//var chartypeArr = chartypes.split(' ');

				//var checkCount = 0;
				//var checktype = getCheckType(blocktypes, allowtypes);
				//for (var i=0; i < chartypeArr.length; i++) {
				//	if(isValidate(checktype, chartypeArr[i], inputType)) {
				//		checkCount++;
				//	};
				//}

				//if (checkCount == 0) {
					//var that = this;

					if(allowtypes.indexOf(HANGUL)==-1)
					{
						var pattern=/[ㄱ-ㅎ|ㅏ-ㅣ|가-힣]/;
						if(pattern.test($(this).val()))
							$(this).val($(this).val().replace(/[ㄱ-ㅎ|ㅏ-ㅣ|가-힣]/g,''));
					}

					if(allowtypes.indexOf(NUMBER)==-1)
					{
						var pattern=/[0-9]/;
						if(pattern.test($(this).val()))
							$(this).val($(this).val().replace(/[0-9]/g,''));
					}

					if(allowtypes.indexOf(ALPHABET)==-1)
					{
						var pattern=/[a-zA-Z]/;
						if(pattern.test($(this).val()))
							$(this).val($(this).val().replace(/[a-zA-Z]/g,''));
					}

					if(allowtypes.indexOf(SPECIAL)==-1)
					{
						var pattern=/[`~!@+_#$%^&*|\\\'\";:\/?]/;
						if(pattern.test($(this).val()))
							$(this).val($(this).val().replace(/[`~!@+_#$%^&*|\\\'\";:\/?]/g,''));

						if($(this).val().indexOf('-')>-1)
						{
							$(this).val($(this).val().replace(/-/g,''));
						}
					}

					///$(this).val($(this).val().substring(0, $(this).val().length - 1));
///					messageBox('허용하지 않은 문자유형입니다.', function() {
//						isDup = false;
//						$(that).focus();
//					}, true);
//					$(that).focus();
					//return false;
				//}
			});
		});
	};
})(jQuery);

/* sticky */
$.fn.sticky = function(options) {
	var headerHeight = $('header').height();
	//var stickyHeaderHeight = this.height();
	var stickyHeaderTop = this.offset().top;

	var array = [];
	var windowsHeight = $(document).outerHeight();

	var loadData = function() {
		$('div[id^=menu]').each(function(index){
			if ($(this).last().index() == index) {
				return false;
			}

			//var paddingTop = parseInt($(this).css('padding-top').replace('px', ''));
			var mt = {};
			mt.id = $(this).attr('id');
			//mt.top = parseInt($(this).offset().top + (stickyHeaderHeight + headerHeight)) - paddingTop;
			//mt.top = parseInt($(this).offset().top);
			mt.top = parseInt($(this).offset().top - headerHeight) - 4 + (options.adjTop[index]);
			array.push(mt);
		});
	};

	var reloadData = function() {
		array = [];
		loadData();
	};

	loadData();

	$(window).scroll(function(){
		var scrollT = $(window).scrollTop();

//console.log('scrollT: ', scrollT);

		var index = 0;
		var nextTop = 0;
		var targetId;
		for (var i=0; i < array.length; i++) {

			//if (i == (array.length-1)) {
			//	index = (array.length-1);
			//	break;
			//}
			if(i<array.length-1){
				nextTop = array[i+1].top;
			}else{
				nextTop = windowsHeight;
			}

			if (array[i].top <= scrollT && scrollT < nextTop) {
				index = i + 1;
				targetId = array[i].id;
				break;
			}
		}

		$('.sticky_header a').removeClass('active');
		if(targetId){
			$('.sticky_header a.'+targetId).addClass('active');
		}else{
			$('.sticky_header a.menu'+index).addClass('active');
		}

//		$('div[id^=menu]').each(function(index){
//			if(array[index].top <= scrollT && scrollT < array[index+1].top){
//				$('.sticky_header a').removeClass('active');
//				$('.sticky_header a.menu'+(index+1)).addClass('active');
//			} else if (array[array.length-1].top<=scrollT) {
//				$('.sticky_header a').removeClass('active');
//				$('.sticky_header a.menu'+(index+1)).addClass('active');
//			}
//		});

		if ((headerHeight + scrollT) >= stickyHeaderTop) {
			$('.sticky_header').addClass('fxd');
		} else {
			$('.sticky_header').removeClass('fxd');
		}
	});

	$('.sticky_header a').each(function(){
		$(this).on('click',function(){

			reloadData();
			var elTop=0;
			var id = $(this).attr('href').replace('#', '');
			for (var i=0 ; i < array.length; i++) {
				if (id == array[i].id) {
					elTop = array[i].top;
					break;
				}
			}
			//var elTop = $(id).offset().top - parseInt($(id).css('padding-top'));
			$('html, body').animate({scrollTop:(elTop)});
		});
		//$('#no1').on('click', function(){
		//	$('html, body').animate({scrollTop:0});
		//});
	});

	// top 데이터 갱신을 위한 이벤트
	$('.sticky_delegate_dataload').on('click', function() {
		reloadData();
	});
};

function showLoginPopup() {
	customShowModal('div#modal_login');
}

function rateToText(rate) {
	if (rate == '1') {
		return "괜히봤어요";
	} else if (rate == '2') {
		return "기대하진 말아요";
	} else if (rate == '3') {
		return "무난했어요"
	} else if (rate == '4') {
		return "기대해도 좋아요!";
	} else if (rate == '5') {
		return "너무 멋진 영화였어요!";
	} else {
		return "평점을 입력해주세요"
	}
}

function closeAllModal() {
	// 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
	megaboxLog.moveArry=[];
	megaboxLog.closePopup();
	$('.modal').modal('hide');
}

/**
 * backdrop 디자인이 messageBox와 같은 모달창을 띄운다.
 * @param selector
 */
function customShowModal(selector) {
	var $mask = $('<div>').attr('id', 'mask').css({
		position: 'fixed',
		top: 0,
		left: 0,
		zIndex: parseInt($(selector).css('z-index')) - 1,
		backgroundColor: '#000',
		display: 'block'
	});

	var scrollWidth = window.outerWidth-document.body.clientWidth;
	$('body').addClass('body_ovh').css('margin-right',scrollWidth).find('>header,div.sticky_header').css('margin-right',scrollWidth);
	$('body').prepend($mask);

	// 화면의 너비와 높이를 구함.
    var maskHeight = $(document).height();
    //var maskWidth = $(window).width();

    // 전체 스크린에 마스크를 적용한다.
    $('#mask').css({'width':'100%','height':maskHeight});

    // 마스크 효과적용
    //$('#mask').fadeIn(300); // 걸리는시간 0.3초
    $('#mask').fadeTo("fast", 0.8); // 천천히 투명도 80%

	$(selector).modal('show');
	if($(selector).css('display') == 'none'){

		var $divNomember = $('div#modal_login div.nomember');
		var $divmember = $('div#modal_login div.member');

		var $aNomember = $('div#modal_login div.selectType.nomem').find('a');
		var $amember = $('div#modal_login div.selectType.mem').find('a');

		$divmember.show();
		$divNomember.hide();

		$amember.addClass('on');
		$aNomember.removeClass();

		$(selector).show();
	}
}

function customHideModal(selector) {
	$(selector).modal('hide');

	// 부트스트랩 모달창이 떠있지 않다면
	if (!$('body').hasClass('modal-open')) {
		$('body').removeClass('body_ovh').css('margin-right', 0).find('>header,div.sticky_header').css('margin-right', 0);
	} else {
		$('body').removeClass('body_ovh');
	}

	$('div#mask').remove();
	$('div' + selector).hide();
}

//우편번호팝업 관련 Start
//var ZipCode = {
//	el: {
//		zipCode1: null,
//		zipCode2: null,
//		address: null
//	},
//
//	// 선택한 주소정보가 리턴될 엘리먼트를 jquery selector로 설정한다.
//	initSetting: function(zipCode1, zipCode2, address) {
//		this.el.zipCode1 = zipCode1;
//		this.el.zipCode2 = zipCode2;
//		this.el.address = address;
//	},
//
//	show: function() {
//		$('.find_zipcode').fadeIn(200).find("input[name='search']").focus();
//
//		// 윈도우 너비와 높이를 구함.
//	    var winH = $(window).height();
//	    var winW = $(window).width();
//
//		var oHeight = $('.find_zipcode_lp').height();
//	    var oWidth = $('.find_zipcode_lp').width();
//
//		// 팝업을 윈도우 중앙에 배치
//	    $('.find_zipcode_lp').css('top',  (winH - oHeight)/2);
//	    $('.find_zipcode_lp').css('left', winW / 2 - oWidth / 2);
//
//	    $('.find_zipcode_bg').height($(document).innerHeight());
//	    var scrollWidth = window.outerWidth-document.body.clientWidth;
//		$(document.body).css('margin-right',scrollWidth).addClass('body_ovh').find('>header,div.sticky_header').css('margin-right', scrollWidth);
//	},
//
//	close: function() {
//		$('.find_zipcode').fadeOut(200);
//		$(document.body).css('margin-right',0).removeClass('body_ovh').find('>header,div.sticky_header').css('margin-right',0);
//	},
//
//	search: function() {
//		//var search = $('.find_zipcode .active input:text[name=search]').val();
//		var search = $('.find_zipcode input:text[name=search]').val();
//		$.ajax({
//			url: '/DataProvider',
//			type: 'POST',
//			data: {
//				_command: 'User.searchZipCode',
//				search: $.trim(search),
//				startNo: 0,
//				count: 999
//			}
//		}).success(function( data ) {
//
//			$('ul#zipcodeList').empty();
//
//			if (data.resultCode === '0000') {
//				var list = data.list;
//
//				var htmlTxt = '';
//
//				for (var i=0; i < list.length; i++) {
//					htmlTxt += '<li>';
//					htmlTxt += '<a href="javascript:;" onclick="ZipCode.setZipCode(\'' + list[i].zipCode + '\', \'' + list[i].address + '\')">';
//					htmlTxt += '	<span class="col1">' + list[i].zipCode + '</span>';
//					htmlTxt += '	<span class="col2">' + list[i].address + '</span>';
//					htmlTxt += '</a>';
//				}
//
//				$('ul#zipcodeList').append(htmlTxt);
//			}
//		});
//	},
//
//	setZipCode: function(zipCode, address) {
//		$(this.el.zipCode1).val(zipCode.split('-')[0]);
//		$(this.el.zipCode2).val(zipCode.split('-')[1]);
//		$(this.el.address).val(address);
//
//		this.close();
//	}
//};

//우편번호팝업 관련 Start
var ZipCode = {
	el: {
		zipCode1: null,
		zipCode2: null,
		address: null
	},

	// 선택한 주소정보가 리턴될 엘리먼트를 jquery selector로 설정한다.
	initSetting: function(zipCode1, zipCode2, address) {
		this.el.zipCode1 = zipCode1;
		this.el.zipCode2 = zipCode2;
		this.el.address = address;
	},

	show: function() {
		$('.nfind_zipcode').fadeIn(200).find("input[name='search']").focus();

		// 윈도우 너비와 높이를 구함.
	    var winH = $(window).height();
	    var winW = $(window).width();

		var oHeight = $('.find_zipcode_lp').height();
	    var oWidth = $('.find_zipcode_lp').width();

		// 팝업을 윈도우 중앙에 배치
	    $('.find_zipcode_lp').css('top',  (winH - oHeight)/2);
	    $('.find_zipcode_lp').css('left', winW / 2 - oWidth / 2);

	    $('.find_zipcode_bg').height($(document).innerHeight());
	    var scrollWidth = window.outerWidth-document.body.clientWidth;
		$(document.body).css('margin-right',scrollWidth).addClass('body_ovh').find('>header,div.sticky_header').css('margin-right', scrollWidth);
	},

	showSelected: function(zipCode, roadAddr, numberAddr) {
		ZipCode.close('.nfind_zipcode');
		$('.nfind_zipcode_selected').fadeIn(200)

		// 윈도우 너비와 높이를 구함.
	    var winH = $(window).height();
	    var winW = $(window).width();

		var oHeight = $('.find_zipcode_lp_selected').height();
	    var oWidth = $('.find_zipcode_lp_selected').width();

		// 팝업을 윈도우 중앙에 배치
	    $('.find_zipcode_lp_selected').css('top',  (winH - oHeight)/2);
	    $('.find_zipcode_lp_selected').css('left', winW / 2 - oWidth / 2);

	    $('.find_zipcode_bg_selected').height($(document).innerHeight());

	    $('.z_road .zcode').text(zipCode);
	    $('.z_road .address').text(roadAddr);
	    $('.z_num .zcode').text(zipCode);
	    $('.z_num .address').text(numberAddr);
	    $('.nfind_zipcode_selected button:button[name=useAddrBtn]').attr('onclick', 'ZipCode.setZipCode(\''+zipCode+'\',\''+roadAddr+'\',\''+numberAddr+'\');');
	},

	close: function(target) {
		$(target).fadeOut(200);
		$(document.body).css('margin-right',0).removeClass('body_ovh').find('>header,div.sticky_header').css('margin-right',0);
	},

	search: function() {
		//var search = $('.find_zipcode .active input:text[name=search]').val();
		var search = $('.nfind_zipcode input:text[name=search]').val();

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {
				_command: 'User.searchZipCode',
				searchName: $.trim(search),
				siteCode: '36',
				startNo: 0,
				count: 300
			}
		}).success(function( data ) {

			if(data.resultCode === '0000'){

				var cnt = data.list.length;

				$('div.scroll_txt').empty();

				if(cnt >= 1){
					var list = data.list;

					var htmlTxt = '';

					for (var i=0; i < cnt; i++) {
						htmlTxt += '<dl>';
						htmlTxt += '<dt class="blind" >우편번호</dt>';
						htmlTxt += '<dd class="zip">' + list[i].zipCode + '</dd>';
						htmlTxt += '<dt class="blind" >주소</dt>';
						htmlTxt += '<dd class="address" ><a href="javascript:;" onclick="ZipCode.showSelected(\''+list[i].zipCode+'\', \''+list[i].roadAddr+'\', \''+list[i].numberAddr+'\');" title="'+list[i].roadAddr+' 선택">'+list[i].roadAddr+'</a></dd>';
						htmlTxt += '<dd class="address" ><a href="javascript:;" onclick="ZipCode.showSelected(\''+list[i].zipCode+'\', \''+list[i].roadAddr+'\', \''+list[i].numberAddr+'\')" title="'+list[i].numberAddr+' 선택">'+list[i].numberAddr+'</a></dd>';
//						htmlTxt += '<dd class="" ><a href="javascript:messageBox(왕십리);" >팝업테스트</a></dd>';
//						htmlTxt += '<dd class="" ><a href="javascript:messageBox(왕십리, \'' + list[i].zipCode + '\', \'' + list[i].roadAddr + '\', \'' + list[i].numberAddr + '\');" >팝업테스트</a></dd>';
//						htmlTxt += '<dd class="" ><a href="javascript:;" onclick="messageBox(\''선택'\', \'' + list[i].zipCode + '\', \'' + list[i].roadAddr + '\', \'' + list[i].numberAddr + '\')" >''</a></dd>';
						htmlTxt += '</dl>';
					}

					$('div.scroll_txt').append(htmlTxt);
					$('div.zip_result_none').hide();
					$('div.scroll_txt').show();
				}else{
					$('div.scroll_txt').empty();
					$('div.scroll_txt').hide();
					$('div.zip_result_none').show();
				}

			}else{
				messageBox("검색 조건이 적합하지 않습니다.");
			}
		});

	},

	setZipCode: function(zipCode, roadAddr, numberAddr) {

/*		$(this.el.zipCode1).val(zipCode.split('-')[0]);
		$(this.el.zipCode2).val(zipCode.split('-')[1]);
		$(this.el.address).val(address);*/
		$("#zipCodeNew").text("("+zipCode+")");
		$("#roadAddr").css('display', 'block');
		$('#roadAddr').text("도로명주소 : "+roadAddr);
		$('#numberAddr').text("지번주소 : "+ numberAddr);

		this.close('.nfind_zipcode_selected');
	}
};



function loginCheck(callback) {
	// 로그인 체크
	$.ajax({
		url: '/pages/common/loginCheck.jsp',
		type: 'POST'
	}).success(function( data ) {
		callback(data);
	});
}

//왼쪽에서부터 채운다는 의미
function lpad(s, c, n) {

	// 숫자의 경우 스트링화
	s += '';
	c += '';

    if (! s || ! c || s.length >= n) {
        return s;
    }

    var max = (n - s.length)/c.length;
    for (var i = 0; i < max; i++) {
        s = c + s;
    }

    return s;
}

// 오른쪽에서부터 채운다는 의미
function rpad(s, c, n) {

	// 숫자의 경우 스트링화
	s += '';
	c += '';

    if (! s || ! c || s.length >= n) {
        return s;
    }

    var max = (n - s.length)/c.length;
    for (var i = 0; i < max; i++) {
        s += c;
    }

    return s;
}

// 날짜 체크(윤달포함)
function isValidDate(param) {
    try
    {
        param = param.replace(/-/g,'');

        // 자리수가 맞지않을때
        if( isNaN(param) || param.length!=8 ) {
            return false;
        }

        var year = Number(param.substring(0, 4));
        var month = Number(param.substring(4, 6));
        var day = Number(param.substring(6, 8));

        var dd = day / 0;


        if( month<1 || month>12 ) {
            return false;
        }

        var maxDaysInMonth = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
        var maxDay = maxDaysInMonth[month-1];

        // 윤년 체크
        if( month==2 && ( year%4==0 && year%100!=0 || year%400==0 ) ) {
            maxDay = 29;
        }

        if( day<=0 || day>maxDay ) {
            return false;
        }
        return true;

    } catch (err) {
        return false;
    }
}

function istLowBrowser(){

	var lowBrowserVer = false;

	if( navigator.appName.indexOf("Microsoft") > -1 )
	{
		if(navigator.appVersion.indexOf("MSIE 8") > -1)
			lowBrowserVer=true;
		else if(navigator.appVersion.indexOf("MSIE 9") > -1)
			lowBrowserVer=true;
		else if(navigator.appVersion.indexOf("MSIE 7") > -1)
			lowBrowserVer=true;
		else
			lowBrowserVer=false;
	}
    var browser = navigator.userAgent.toLowerCase();
	if(browser.indexOf('firefox') > -1)
	{
		lowBrowserVer=false;
	}

	if(browser.indexOf('opera') > -1)
	{
		lowBrowserVer=false;
	}

	return lowBrowserVer;

}

var megaMovie = {
	/**
	 * 관람등급의 맞는 텍스트를 리턴한다.
	 * @param filmRating
	 */
	getFilmRatingText : function (filmRating) {
		filmRating = filmRating ? filmRating : "0";

		if ("0" == filmRating) {
			return "전체관람가";
		} else if ("12" == filmRating) {
			return "12세관람가";
		} else if ("15" == filmRating) {
			return "15세관람가";
		} else if ("99" == filmRating) {
			return "미정";
		} else {
			return "청소년관람불가";
		}
	},

	/**
	 * 관람등급의 맞는 css 클래스명을 리턴한다.
	 * @param filmRating
	 */
	getFilmRatingCss : function (filmRating) {
		if ("0" == filmRating || "00" == filmRating) {
			filmRating = "all";
		} else if ("18" == filmRating || "19" == filmRating) {
			filmRating = "19";
		}

		return filmRating ? "age_"+filmRating : "";
	}
}

function sendGAMegabox(category, action, label) {
	var eventLable = label;
	if(typeof label === 'undefined'){
		eventLable = '';
	}

	//alert("sendGAMegabox('MoviePost', 'GNB_PostWirting')");
	ga('send', {
		hitType: 'event',
		eventCategory: category,
		eventAction: action,
		eventLabel: eventLable
	});
}