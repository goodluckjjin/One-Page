function initControls($sender) {
	$(function () {
		if(navigator.userAgent.indexOf("Chrome") > 0){

			$("body").addClass("Chrome");
		}
		//j.s.m - start
		/*//본영화,보고싶어,안볼래 버튼 클릭시
		$(".img_you_ck").click(function(){
			$(".img_you_ck").removeClass("active");
			$(this).addClass("active");
		});*/

		//상세정보,예매하기 버튼 키업&블러
		$(".btn_wrap a").keyup(function(){
			$(".btn_wrap a").removeClass("hover");
			$(this).addClass("hover");
		}).blur(function(){
			$(".btn_wrap a").removeClass("hover");
		});
		//j.s.m - end

		if ($('html').hasClass('csstransforms3d')) {

			//flip
			$('.thumb').removeClass('scroll').addClass('flip');
			$('.thumb.flip').hover(
				function () {
					if(!$(this).find('span.special_end_event').hasClass('end_event'))
						$(this).addClass('flipIt');
				},
				function () {
					if(!$(this).find('span.special_end_event').hasClass('end_event'))
						$(this).removeClass('flipIt');
				}
			);
			$('.thumb.flip').keyup(function () {
				$(".flip").removeClass("flipIt");
				if(!$(this).find('span.special_end_event').hasClass('end_event'))
					$(this).addClass('flipIt');
			});
		} else {
			//slide ie8~9
			$('.thumb').hover(
				function () {
					if(!$(this).find('span.special_end_event').hasClass('end_event'))
						$(this).find('.lower').stop().animate({top:0}, 500, 'easeOutCubic');
				},
				function () {
					if(!$(this).find('span.special_end_event').hasClass('end_event'))
						$(this).find('.lower').stop().animate({top: ($(this).height() * 1) }, 500, 'easeOutCubic');
				}
			);

			//영화목록 탭이동 추가:j.s.m - start
			$('.thumb').keyup(function () {
				if(!$(this).find('span.special_end_event').hasClass('end_event'))
					$(this).find('.lower').stop().animate({top:0}, 500, 'easeOutCubic');
			});
			$('.item').blur(function () {
				$('.lower').stop().animate({top: ($(this).height() * 1) }, 500, 'easeOutCubic');
			});
			//영화목록 탭이동 추가:j.s.m - end
		}
	});

	$sender.find('ul.old_main_slide').bxSlider({
		ticker: false,
		auto:true,
	    pause: 4000,
		onSlideNext:function(){
			$sender.find('ul.main_slide').find('button.video_pause').click();
		},
		onSlidePrev:function(){
			$sender.find('ul.main_slide').find('button.video_pause').click();
		}
	});

	$sender.find('div.bx-controls-direction a').hover(function(){
		$sender.find('ul.main_slide').find('button.video').addClass('off');
	},function(){
		$sender.find('ul.main_slide').find('button.video').removeClass('off');
	});

	$sender.find('div.tab_arrow a').click(function(){
		var o = $(this).parents('.tab_arrow').prev('ul');
		if($(this).hasClass('bx-prev')){
			$(this).hide();
			$('div.tab_arrow .bx-next').show();
			o.find('li').toggle();
		} else {
			$(this).hide();
			$('div.tab_arrow .bx-prev').show();
			o.find('li').toggle();
		};
		return false;

//		var o = $(this).parents('.tab_arrow').prev().find('.active');
//		if($(this).hasClass('bx-prev')){
//			o.prev().find('a').click();
//		} else {
//			o.next().find('a').click();
//		};
//		return false;
	});

	$sender.find('ul.main_slide2').bxSlider({
		auto:true,
		autoControls:true
	});

	$sender.find('input.style_input').iCheck({
		checkboxClass: 'icheckbox_minimal',
		radioClass: 'iradio_minimal',
		increaseArea: '20%'
	});

	//$sender.find('select').customSelect();

	$sender.find('input.icheck').iCheck({
		checkboxClass: 'icheckbox_minimal',
		radioClass: 'iradio_minimal',
		increaseArea: '20%'
	});

	$sender.find('input.icheck[name=seats]').on('ifChecked', function(event){
		$('.seat_continue li').removeClass('selected');
		if ($(this).is(":checked") == true){
			$(this).parents('li').addClass('selected');
		}
	});

	$sender.find('.seat_text').popover({
        html : true,
        placement:'bottom',
//        content: function() {
//          	return $('#seat_text_content').html();
//        }
    });

	$sender.find('.seat_text').popover('show');

	$sender.find('select').selectpicker();

	$sender.find('#open_myinfo, .member_info .hide, .member_info .photo, .member_info .name_btn').click(function() {
		if ($sender.find('.login_info').hasClass('open_myinfo_open')) {
			$sender.find('.login_info').removeClass('open_myinfo_open');
			$sender.find('.content_wrap').removeClass('open_myinfo_open');
		} else {
			$sender.find('.login_info').addClass('open_myinfo_open');
			$sender.find('.content_wrap').click(function() {
				//$sender.find('.login_info').removeClass('open_myinfo_open');
				//$sender.find('.content_wrap').removeClass('open_myinfo_open');
			});

			//추가합니다. 선호영화관 긴지짧은지확인
			var my_favor = $(".my_favor_ck");
			var my_favor_w = my_favor.width();

			if(my_favor_w > 160) my_favor.css({float:"none",clear:"both",width:"100%"});
		}
	});

	//myinfo 닫기버튼 추가jsm
	$sender.find(".myinfo_close").click(function(){

		$sender.find(".login_info").removeClass("open_myinfo_open");
		$sender.find(".name_btn").focus();
	});

	//팝업창으로 탭이동시 클래스
	$(".focus_pop").click(function(){
		$(this).addClass("nowfocus");
		$(".custom_alert").focus();
	});
	//팝업창 열어준 버튼으로 돌아간 후 리셋
	$sender.find(".focus_close").click(function(){
		$(".nowfocus").focus().removeClass("nowfocus");
	});





	$sender.find('input.icheck').on('ifChecked', function(event){
		//var get_id = '#'+$(this).attr('id')+'_view';
		//$sender.find('.login_input_wrap').hide();
		//if ($(this).is(":checked") == true){
		//	$(get_id).show();
		//}
	});

	//menu_all 검색후 닫기버튼으로 포커스이동
	$('.memu_all .custom_close2').blur(function(){
		$('.menus .cols:first dl:first a:first').focus();
	});

	//사이트맵 클릭 이벤트
	$sender.find('.btn_menu_all').click(function(){
		//menu_all 열린상태
		if(!($('.memu_all').hasClass('menu_open'))){
		//menu_all 닫힌상태
			$('.memu_all').addClass('menu_open');
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
			$('body').addClass('body_ovh').css('margin-right', scrollWidth).find('>header,div.sticky_header').css('margin-right', scrollWidth);
			$('.header_bg_black').show();
			$('.menus .cols:first dl:first a:first').focus();
			var moveTitle = '사이트맵' ;
			// URL 변경 및 GA 호출
			var moveURL = "?show=sitemap";
			megaboxLog.openPopup(moveURL, moveTitle);
		}
	});
	//사이트맵 닫기 이벤트
	$sender.find('.memu_all .custom_close2 , .header_bg_black').click(function(){
		$('.memu_all').removeClass('menu_open');
		$('body').removeClass('body_ovh').css('margin-right', 0).find('>header,div.sticky_header').css('margin-right', 0);
		$('.header_bg_black').hide();
		$('.btn_menu_all').focus();
		megaboxLog.closePopup();
	});

	$sender.find('.body_theater2').hide();
	$sender.find('#h3_wrap h3').each(function(index){
		$(this).click(function(){
			var idx = index+1;
			$sender.find('#h3_wrap h3').removeClass('active');
			$(this).addClass('active');
			$sender.find('#select_theater .movie_body').hide();
			$sender.find('.body_theater'+idx).show();
		});
	});

	//$sender.find('select').customSelect();
	$sender.on('mouseover', '#reservation .movie_list>ul>li', (function(){
		$(this).addClass('selected');
	}));

	$sender.on('mouseout', '#reservation .movie_list>ul>li', (function(){
		$(this).removeClass('selected');
	}));

	$sender.find('.movie_body .util .selected p i').click(function(){
		$(this).parent().remove();
	});


	if ($(window).width()>=1295) {
		$('.section .content_wrap').removeClass('narrow');
		$('.section .content_wrap').addClass('wide');
	} else {
		$('.section .content_wrap').removeClass('wide');
		$('.section .content_wrap').addClass('narrow');
	}

	$(window).resize(function(){
		if ($(window).width()>=1295) {
			$('.section .content_wrap').removeClass('narrow');
			$('.section .content_wrap').addClass('wide');
		} else {
			$('.section .content_wrap').removeClass('wide');
			$('.section .content_wrap').addClass('narrow');
		}
	});

	//$sender.find('.scroll_wrap').perfectScrollbar();
	//$sender.find('.customer_term_text').perfectScrollbar();

	$sender.find('.btn_telecom').click(function() {
		var btn_id = $(this).attr('id');
		$sender.find('#card_move').removeClass().addClass(btn_id);
	});

	$sender.find('.lp').click(function(){
		var lp_target = $(this).attr('data-target')||$(this).attr('href');
		$(lp_target).fadeIn('fast');
	});

	$sender.find('.lp_close,.lp_cancel').click(function(){
		$(this).parents('.lp_box').fadeOut('fast');
	});

	$sender.find('.cinema_time').on('mouseenter', function(){
		$(this).find('.hover_time').fadeIn('fast');
	});
	$sender.find('.cinema_time').on('mouseleave', function(){
		$(this).find('.hover_time').fadeOut('fast');
	});


	/* 달력*/
	$(".datepicker-days table tr td.active a").attr("title","선택됨");



	//jsm : start
}