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
var _requestMovieList = new JSPRequest("/pages/movie/Movie_List.jsp", "ul#movieList", "ul#movieList");
var _requestMovieFestivalList = new JSPRequest("/pages/movie/Movie_Festival_List.jsp", "ul#movieList", "ul#movieList");
var _requestMovieDetail = new JSPRequest("/pages/movie/Movie_Detail.jsp", "div#movieDetail", "div#movieDetail");
//var _requestMovieDetailMyScore = new JSPRequest("/pages/movie/Movie_Detail_MyScore.jsp", "div#movieDetailMyScore", "div#movieDetailMyScore");
var _requestMovieCommentList = new JSPRequest("/pages/movie/Movie_Detail_Comment_List.jsp", "div#movieCommentList", "div#movieCommentList");

/**
 * 영화목록
 */
var MovieList = {

	init: function(menuId) {
		if(menuId=='movie-festival') this.showPage2(menuId, 'releaseDate');
		else this.showPage(menuId, 'releaseDate');
	},

	showPage: function(menuId, sort) {
		var len = $('[name=adType]').length;
		var count = (this.getItemNumberPerRow() * 3) - len;

		try {
			_requestMovieList.request({
				menuId: menuId,
				startNo: 0,
				count: count,
				sort: sort
			});
		} catch(e) {
			messageBox(e);
		}

		if(len>0) this.addAds();
	},

	showPage2: function(menuId, sort) {
		var len = $('[name=adType]').length;
		var count = (this.getItemNumberPerRow() * 3) - len;

		try {
			_requestMovieFestivalList.request({
				menuId: menuId,
				startNo: 0,
				count: count,
				sort: sort
			});
		} catch(e) {
			messageBox(e);
		}

		if(len>0) this.addAds();
	},

	getMore: function() {
		var count = (this.getItemNumberPerRow() * 3) - (this.getCurrentItemTotalCount() % this.getItemNumberPerRow());

		var startNo = (_requestMovieList.data.startNo == 0) ? -1 : _requestMovieList.data.startNo;

		try {
			_requestMovieList.processing = 'append';
			_requestMovieList.put('startNo', startNo + count);
			_requestMovieList.put('count', count);
			_requestMovieList.refresh();
			_requestMovieList.processing = 'replace';
			var $focusItem = $("li.item", "#movieList").eq(count).find(".star-wrap").find("input");
			if($focusItem.length>0){
				$focusItem.eq(count).focus();
			}
		} catch(e) {
			messageBox(e);
		}
	},

	getMore2: function() {
		var count = (this.getItemNumberPerRow() * 3) - (this.getCurrentItemTotalCount() % this.getItemNumberPerRow());

		var startNo = (_requestMovieFestivalList.data.startNo == 0) ? -1 : _requestMovieFestivalList.data.startNo;

		try {
			_requestMovieFestivalList.processing = 'append';
			_requestMovieFestivalList.put('startNo', startNo + count);
			_requestMovieFestivalList.put('count', count);
			_requestMovieFestivalList.refresh();
			_requestMovieFestivalList.processing = 'replace';
			var $focusItem = $("li.item", "#movieList").eq(count).find(".star-wrap").find("input");
			if($focusItem.length>0){
				$focusItem.eq(count).focus();
			}
		} catch(e) {
			messageBox(e);
		}
	},

	getItemNumberPerRow: function() {
		var ITEM_WIDTH = 246;
		var number = Math.floor($(window).width() / ITEM_WIDTH);
		if (number < 4) {
			number = 4;
		}
		return number;
	},

	getCurrentItemTotalCount: function() {
		//return $('ul#movieList li:visible:not(.movie_list_ad)').length;
		return $('ul#movieList li:visible').length;
	},

	getCountInterestingMovie: function(memberCode) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {_command: 'Movie.getMyInterestingCount', member: memberCode }
		}).success(function( data ) {
			$('span#movie-interesting').text(data.count);
		});
	},
	//내가 못본영화 카운트  2014.09.12  김기열
	getCountMissedMovie: function(memberCode) {
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {_command: 'Movie.getMissedMovieCount', member: memberCode }
		}).success(function( data ) {
			$('span#movie-missed').text(data.count);
		});
	},

	// 광고추가
	addAds: function() {
		var adType = $('[name=adType]').val();
		if(adType=='slideAds'){
			this.slideAds();
		}else if(adType=='html'){
			var adsDescription = $('[name=adsDescription]').val();
			$('ul#movieList').prepend("<li class=\"item sm_ad fr\" tabindex='0' style='padding-top:1px; padding-left:1px; border:0;'>"+adsDescription+"</li>");
		}else{
			var adsImagePath = $('input[name=adsImagePath]').val();
			var adsType = $('input[name=adsType]').val();
			var adsCode = $('input[name=adsCode]').val();
			var adsLink = $('input[name=adsLink]').val();
			var adsLinkAlt = $('input[name=adsLinkAlt]').val();
			var adsAlt = $('input[name=adsAlt]').val();
			var adsClick = $('input[name=adsClick]').val();
			if( adsLink && adsImagePath ){
				var htmlTxt = "<li class=\"item movie_list_ad\"><div>";
				htmlTxt += "<a href=\"javascript:adHasClick('"+adsLink+"', "+adsClick+", 'link');\" title=\""+adsLinkAlt+"\">";
				htmlTxt +="<img src=\"" + adsImagePath + "\" alt=\""+adsAlt+"\" />";
				htmlTxt += "</a>";
				htmlTxt += "</div></li>";
				$('ul#movieList').prepend(htmlTxt);
			}
		}
	},

	slideAds: function(){

		var adsImagePath = $('input[name=adsImagePath]').val();
		var adsType = $('input[name=adsType]').val();
		var adsCode = $('input[name=adsCode]').val();
		var adsLink = $('input[name=adsLink]').val();
		var adsLinkAlt = $('input[name=adsLinkAlt]').val();
		var adsAlt = $('input[name=adsAlt]').val();
		var adsClick = $('input[name=adsClick]').val();
		//alert($('input[name=adsImagePath]').length);
		var idx = 0;
		var htmlTxt = "";
		var total_htmlTxt = "";
		$('input[name=adsImagePath]').each(function(i){
			htmlTxt ="";
			//idx = $(this).index();
			idx = i;

			adsImagePath = $("input[name=adsImagePath]:eq("+idx+")").val();
			adsType = $("input[name=adsType]:eq("+idx+")").val();
			adsCode = $("input[name=adsCode]:eq("+idx+")").val();
			adsLink = $("input[name=adsLink]:eq("+idx+")").val();
			adsLinkAlt = $("input[name=adsLinkAlt]:eq("+idx+")").val();
			adsAlt = $("input[name=adsAlt]:eq("+idx+")").val();
			adsClick = Number($("input[name=adsClick]:eq("+idx+")").val());

			if(adsLink){
				idx = idx+1;
				htmlTxt += "<dt class='adt"+idx+"'><a href='javascript:void(0);' title='배너"+idx+"'>배너"+idx+"</a></dt>";
				htmlTxt += "<dd class='adt"+idx+"_dd' style='background:url("+adsImagePath+") 0 0 no-repeat'>";
				htmlTxt += "<a href=\"javascript:adHasClick('"+adsLink+"', "+adsClick+", 'link');\" title=\""+adsLinkAlt+"\">";
				htmlTxt += "<span class='blind3'>"+adsAlt+"</span>";
				htmlTxt += "</a>";
				htmlTxt += "<span class='btn_bg'></span>";
				htmlTxt += "</dd>";
			}
			if(adsImagePath) total_htmlTxt += htmlTxt;
		});

		$('ul#movieList').prepend("<li class=\"item sm_ad fr\" tabindex='0' style='padding-top:1px; padding-left:1px; border:0;'><div class=\"ad_slide size_l\"><div class=\"ad_autostop\"><a class=\"ad_stop\" href=\"javascript:void(0);\" title=\"배너 롤링 정지\">배너 롤링 정지</a></div><dl>"+total_htmlTxt+"</dl>	</div></div></li>");

		//광고슬라이트 스크립트
		//초기설정
		$(".adt1 a").addClass("on");

		//키이벤트 - 버튼보임
		$(".sm_ad").focus(function(){
			$(".ad_autostop, .ad_slide dl dd .btn_bg").show();
			$(".ad_slide dl dt").css("display","inline-block");
		});



		//마우스이벤트
		$(".ad_slide").hover(function(){
			$(".ad_autostop, .btn_bg").show();
			$(".ad_slide dl dt").css("display","inline-block");  // 마우스 오버시 버튼보임
	   	},function(){
		   	$(".ad_autostop, .btn_bg").hide();
			$(".ad_slide dl dt").css("display","none");  // 마우스 아웃시 버튼가림
   		});

		//초기설정
		$(".ad_slide dl dd").addClass("pass");
		$(".ad_slide dl dd:eq(0)").addClass("now").removeClass("pass");

		$(".ad_slide dl dt a").click(function(){
			$(".ad_slide dl dt a").removeClass("on");	//on클래스 리셋
			$(this).addClass("on");						//on추가
			var currentDD = $(this).parent().find("+dd");//보이고자하는 dd

			if(currentDD.attr("class").indexOf("now") != -1){	//현재 dd와 보이고자하는 dd 같을경우 반응없음
			}else{
				//작동시 모든dd 보임
				$(".ad_slide dl dd").show();

				//현재dd 지나감
				$(".ad_slide dl dd.now").animate({
					left:-100+"%"
				},"fast","swing",function(){
					$(this).css("left","100%").removeClass("now").addClass("pass");
				});

				//보이고자하는 dd 나옴
				currentDD.animate({
					left:0+"%"
				},"fast","swing",function(){
					$(this).addClass("now").removeClass("pass");
					$(".ad_slide dl dd.pass").hide();
				});

			}


			$(".ad_autostop a").removeClass("ad_stop").addClass("ad_play").text("배너 롤링 재생");
			clearInterval(adTimer);
		});

		//자동롤링
		var adTimer = '';
		if(adTimer) clearInterval(adTimer);
		adTimer = setInterval(function(){
			addAutoroll();
			}, 4000);

		function addAutoroll(){
			var len = $(".ad_slide dl dt a").length;
			var onClass = $(".ad_slide dl dt a.on").parent().attr("class");//지나가는 이미지 클래스
			var indexClass = onClass.indexOf("adt");
			var nextClass = onClass.slice(indexClass+3);
			if(nextClass < len){
				nextClass = parseInt(nextClass)+1;
			}else if(nextClass == len){
				nextClass = 1;
			}

			$(".ad_slide dl dt."+onClass+" a").removeClass("on");
			$(".ad_slide dl dt.adt"+nextClass+" a").addClass("on");
			//지나가는 이미지
			$("."+onClass+"_dd:not(:animated)").animate({
				left:-100+"%",
			},"normal","swing",function(){
				$(this).css("left","100%").removeClass("now").addClass("pass");
			});

			//보여지는 이미지
			$(".adt"+nextClass+"_dd:not(:animated)").show().animate({
				left:0+"%",
			},"normal","swing",function(){
				$(this).addClass("now").removeClass("pass");
				$(".ad_slide dl dd.pass").hide();
			});
		}


		$(".ad_stop").click(function(){
			if($(this).attr("class").indexOf("ad_stop") != -1){
				$(this).removeClass("ad_stop").addClass("ad_play").text("배너 롤링 재생");
				clearInterval(adTimer);
			}else{
				$(this).removeClass("ad_play").addClass("ad_stop").text("배너 롤링 정지");
				adTimer = setInterval(function(){
					addAutoroll();
					}, 4000);
			}
		});


	},

	// 보고싶어, 못본영화 카운트 갱신 xframe 세션기능 수정후 적용
//	refreshMovieCount: function() {
//		$.ajax({
//			url: '/pages/movie/Movie_List_Process.jsp',
//			type: 'POST'
//		}).success(function( data ) {
//			$('span#movie-seen').text(numberWithCommas('<%= mySeenCount %>')); 				// 본영화개수 설정
//			$('span#movie-missed').text(numberWithCommas('<%= missedMovieCount %>')); 		// 못본영화개수 설정
//			$('span#movie-interesting').text(numberWithCommas('<%= myInterestingCount %>'));	// 볼영화개수 설정
//		});
//	}
};

/**
 * 영화상세
 */
var MovieDetail =  {
	movieCode: null,
//	init: function() {
//
//		var me = this;
//		$('#movie_detail').on('show.bs.modal', function(e) {
//			var code = $(e.relatedTarget).data('code');
//
//			me.movieCode = code;
//
//console.log('movieCode is ', code);
//
//			me.showPage(code);
//
//		});
//	},

	showPage: function(movieCode, adClick) {
		this.movieCode = movieCode;

		try {
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

			_requestMovieDetail.request({
				code: movieCode
			});

			// 1.상세 레이어 표시할때 타이틀 교체를 위한 이벤트 바인딩
			// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var $title = $('#movieDetail .title h2');
			var filmRating = $title.find("i").text();
			var koreanTitle = $title.find("span").text();
			var moveTitle = '영화상세('+koreanTitle+')' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?show=detail&rtnShowMovieCode=" + this.movieCode;
			megaboxLog.openPopup(moveURL, moveTitle);

		} catch(e) {
			messageBox(e);
		}

		// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩
		$('#movie_detail').off('hide.bs.modal').on('hide.bs.modal',function() {
			megaboxLog.closePopup();
			$('#movieDetail').html(''); //레이어 닫을때 클리어 2015.05.26 김창현
		});

		//if (type === 'snslink') {
			$('#movie_detail').modal('show');
		//}
	},

	getData: function(movieCode){

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {_command: 'Movie.getDetail', code: movieCode}

		}).success(function( data ) {
			$('#starMemberCount_'+movieCode).html(data.movie.starCount);
		});

	}
};

/**
 * 	한줄평
 */
var MovieComment = {

	releaseDate: null,
	scoreData:null,

	init: function(releaseDate) {
		this.releaseDate = releaseDate;
		this.list('createDate');
	},

	list: function(sort) {
		try {
			_requestMovieCommentList.request({
				movieCode: MovieDetail.movieCode,
				sort: sort,
				releaseDate: this.releaseDate
			});
		} catch(e) {
			messageBox(e);
		}
	},

	regist: function(dday) {

		var starScore 	= $('#movieCommentFrm input[name=starScore]').val();
		var comment 	= $("#movieCommentFrm textarea[name=comment]").val();


		if ($.trim(starScore) == "") {
			if (dday <= 0 || typeof dday == 'undefined' ) {
				messageBox('평점을 적용해주세요.');
				return;
			}
			else{
				if (typeof starScore == 'undefined' || starScore == "") {
					starScore = 0;
				}
			}
		}

		if ($.trim(comment) == "") {

			var alert = messageBox('내용을 입력해주세요.');
			$(alert).find('button.ok').on('click', function(){
				$('.textarea textarea').focus();
			});
			$(alert).find('button.btn_ca_close').on('click', function(){
				$('.textarea textarea').focus();
			});
			return;
		}

		var that = this;

		$.ajax({
			url: '/pages/movie/Movie_Detail_Process.jsp',
			type: 'POST',
			data: {command: 'putMovieComment', movie: MovieDetail.movieCode, starScore: parseInt(starScore)*2, comment: comment}
		}).success(function( data ) {
			if (data.result === 'success') {
				$("#movieCommentFrm textarea[name=comment]").val("");

				try {
					_requestMovieCommentList.refresh();
				} catch(e) {
					messageBox(e);
				}

				var totalCount = that.getTotalCount(MovieDetail.movieCode);
				$('#movieCommentTotalCount').text('('+totalCount+')');
				var movie = MovieDetail.movieCode;
				var score = starScore;

				MovieComment.scoreData= starScore;
				$.ajax({
					url: '/pages/movie/Movie_List_Process.jsp',
				  	type: 'POST',
				  	data: {command: 'setStarScore', movie: MovieDetail.movieCode, starScore: parseInt(starScore)*2}
			  	}).success(function(data) {
			  		MovieDetail.getData(movie);
					$('span[name^=averageScore_'+movie+']').html((parseFloat(data.averageScore)).toFixed(1));
					$('#averageScoreDetail_'+movie).html((parseFloat(data.averageScore)).toFixed(1));
					var star_width=parseFloat(data.averageScore) * 10;
					$('span[name^=averageStar_'+movie+']').css({"width":star_width+"%"});
					$('#averageStarDetail_'+movie).css({"width":star_width+"%"});

					$('#starScore_'+movie).raty('set', {score: score});
					$('p[name^=starScoreTxt_'+movie+']').text(rateToText(score));
					$('span#movieDetailStarScoreTxt').text(rateToText(score));
					$("#movieDetailStarScore").raty('set', {score: score});
				});

			} else {
				if(typeof data.resultMessage == 'undefined')
					messageBox( data);
				else
					messageBox( data.resultMessage);
			}
		});
	},

	modify: function(obj, score) {

		if(MovieComment.scoreData==null)
			MovieComment.scoreData=score;

		$(obj).parents('.cell').hide();
		$(obj).parents('.cell').next().show();
		$('#upate_comment_star').raty('set', {score: MovieComment.scoreData});
		$('#upate_comment_star_text').text(rateToText(MovieComment.scoreData));

	},

	cancelModify: function(obj, comment) {
		$(obj).parents('.cell').hide();
		$(obj).parents('.cell').prev().show();
		$('.edit_wrap textarea[name=comment]').val(comment);
	},

	saveModify: function(obj) {
		var starScore = $('.edit_wrap input[name=starScore]').val();

		if (typeof starScore === 'undefined' || starScore == "") {
			starScore = 0;
		}

		starScore = parseInt(starScore);

		var comment = $('.edit_wrap textarea[name=comment]').val();

		if ($.trim(comment) == "") {
			messageBox('내용을 입력해주세요.');
			return;
		}

		var that = this;

		$.ajax({
			url: '/pages/movie/Movie_Detail_Process.jsp',
			type: 'POST',
			data: {command: 'updateMovieComment', movie: MovieDetail.movieCode, starScore: starScore*2, comment: comment}
		}).success(function( data ) {
			if (data.result === 'success') {

				$(obj).parents('.cell').hide();

				$(obj).parents('.cell').prev().find('span.small_star2 span.fill').css('width', ((starScore*2) * 10)+'%');
				$(obj).parents('.cell').prev().find('span.comment').text(comment);
				$(obj).parents('.cell').prev().fadeIn();

				var totalCount = that.getTotalCount(MovieDetail.movieCode);
				$('#movieCommentTotalCount').text('('+totalCount+')');

				var movie = MovieDetail.movieCode;
				var score = starScore;
				MovieComment.scoreData = starScore;
				$.ajax({
					url: '/pages/movie/Movie_List_Process.jsp',
				  	type: 'POST',
				  	data: {command: 'setStarScore', movie: MovieDetail.movieCode, starScore: parseInt(starScore)*2}
			  	}).success(function(data) {
			  		MovieDetail.getData(movie);
					$('span[name^=averageScore_'+movie+']').html((parseFloat(data.averageScore)).toFixed(1));
					$('#averageScoreDetail_'+movie).html((parseFloat(data.averageScore)).toFixed(1));
					var star_width=parseFloat(data.averageScore) * 10;
					$('span[name^=averageStar_'+movie+']').css({"width":star_width+"%"});
					$('#averageStarDetail_'+movie).css({"width":star_width+"%"});

					$('div#myStarScore').raty('set', {score: score});
					$('#movieCommentFrm div.rate p').text(rateToText(score));
					$('#starScore_'+movie).raty('set', {score: score});
					$('p[name^=starScoreTxt_'+movie+']').text(rateToText(score));
					$('span#movieDetailStarScoreTxt').text(rateToText(score));
					$("#movieDetailStarScore").raty('set', {score: score});
				});
			} else {
				if(typeof data.resultMessage == 'undefined')
					messageBox( data);
				else
					messageBox( data.resultMessage);
			}
		});
	},

	remove: function() {
		var that = this;
//		confirmBox('한줄평 삭제', '삭제하시겠습니까?', function() {
//			$.ajax({
//				url: '/pages/movie/Movie_Detail_Process.jsp',
//				type: 'POST',
//				data: {command: 'deleteMovieComment', movie: MovieDetail.movieCode}
//			}).success(function( data ) {
//				if (data.result === 'success') {
//					try {
//						_requestMovieCommentList.refresh();
//					} catch(e) {
//						messageBox(e);
//					}
//					var totalCount = that.getTotalCount(MovieDetail.movieCode);
//					$('#movieCommentTotalCount').text('('+totalCount+')');
//				} else {
//					messageBox(data.resultMessage);
//				}
//			});
//		});

		if(confirm('삭제하시겠습니까?')) {
			$.ajax({
				url: '/pages/movie/Movie_Detail_Process.jsp',
				type: 'POST',
				data: {command: 'deleteMovieComment', movie: MovieDetail.movieCode}
			}).success(function( data ) {
				if (data.result === 'success') {
					try {
						_requestMovieCommentList.refresh();
					} catch(e) {
						messageBox(e);
					}
					var totalCount = that.getTotalCount(MovieDetail.movieCode);
					$('#movieCommentTotalCount').text('('+totalCount+')');
				} else {
					messageBox(data.resultMessage);
				}
			});
		}
	},

	like: function(owner, likeYn) {
		var command = "setMovieCommentLike";
		if (likeYn === 'Y') {
			command = "resetMovieCommentLike";
		}

		$.ajax({
			url: '/pages/movie/Movie_Detail_Process.jsp',
			type: 'POST',
			data: {command: command, member: owner, movie: MovieDetail.movieCode}
		}).success(function( data ) {
			if (data.result === 'success') {
				try {
					_requestMovieCommentList.refresh();
				} catch(e) {
					messageBox(e);
				}
			} else {
				messageBox( data.resultMessage);
			}
		});
	},

	getTotalCount: function(movieCode) {
		var totalCount = 0;
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {_command: 'Movie.getMovieCommentCount', movie: movieCode}

		}).success(function( data ) {
			totalCount = data.count;
		});
		return totalCount;
	},

	getMoreData: function(pageNo) {
		var pageSize = 10;

		var startNo = ( (parseInt(pageNo)-1) * pageSize+1);
		startNo=Number(startNo)-1;
		$(".custom-pagination > li > a").removeClass("active");
		$("#"+pageNo).addClass("active");
		try {
			_requestMovieCommentList.processing = 'replace';
			_requestMovieCommentList.put('startNo', startNo);
			_requestMovieCommentList.put('count', pageSize);
			_requestMovieCommentList.put('_pageNo', pageNo);

			_requestMovieCommentList.refresh();
			_requestMovieCommentList.processing = 'replace';
		} catch(e) {
			messageBox(e);
		}
	},

	//한줄평 신고하기 팝업 표시
	showReportSpoiler: function(obj) {
		var $editWrap = $(obj).parent().find('.btn_edit_wrap');
		$editWrap.toggle();
	},

	// 로그인 체크
	checkReportSpoiler: function(obj, type, movieCode, memberCode) {
		$.ajax({
			url: '/pages/common/loginCheck.jsp',
			type: 'POST'
		}).success(function( data ) {
			$(obj).closest('.btn_edit_wrap').toggle();
			if (data.resultCode === 'success') {
				MovieComment.reportSpoiler(type, movieCode, memberCode);
			} else {
				messageBox('로그인 후 이용가능한 서비스입니다.');
			}
		});
	},

	// 한줄평 신고하기
	reportSpoiler: function(type, movieCode, memberCode) {
		var title = '';
		var msg = '';

		if (type == '10') {
			title = '스포일러 신고하기';
			msg = '스포일러가 포함되어 있나요?';
		} else if (type == '20') {
			title = '부적절한 표현 신고하기';
			msg = '욕설/비방/광고 등 부적절한 표현이 포함되어 있나요?';
		}

		confirmBox(
			title
			, msg
			, function() {
				var data = {
						_command : 'Movie.reportMovieComment',
						movie: movieCode,
						member: memberCode,
						declaration: type,
					};

				$.ajax({
					url: '/DataProvider',
					type: 'POST',
					async: true,
					data: data
				}).success(function (data) {
					messageBox(data.resultMessage);
				});
			}
			, function () {

			}
		);
	}

};

function setSeenMovie(movieCode, score) {
	$.ajax({
		  url: '/pages/movie/Movie_List_Process.jsp',
		  type: 'POST',
		  async: false,
		  data: {command: 'setSeenMovie', movie: movieCode}
	}).success(function(data) {
		if (data.result === 'success') {
			$('#btnSeenMovie').addClass('active');
			$('button[name^=btnSeenMovie_'+movieCode+']').addClass('active');
		} else {
			messageBox( data.resultMessage);
		}
	});
}

function setStarScore(movieCode, score) {
	$.ajax({
		  url: '/pages/movie/Movie_List_Process.jsp',
		  type: 'POST',
		  async: false,
		  data: {command: 'setStarScore', movie: movieCode, starScore: (score * 2)}
	}).success(function(data) {
		if (data.result === 'success') {
			$('span[name^=averageScore_'+movieCode+']').html((parseFloat(data.averageScore)).toFixed(1));
			$('#averageScoreDetail_'+movieCode).html((parseFloat(data.averageScore)).toFixed(1));
			var star_width=parseFloat(data.averageScore) * 10;
			$('span[name^=averageStar_'+movieCode+']').css({"width":star_width+"%"});
			$('#averageStarDetail_'+movieCode).css({"width":star_width+"%"});
			var text = rateToText(score);
			$('p[name^=starScoreTxt_'+movieCode+']').text(text);
		} else {
			messageBox(data.resultMessage);
		}
	});
}

function resetSeenMovie(movieCode) {
	$.ajax({
		  url: '/pages/movie/Movie_List_Process.jsp',
		  type: 'POST',
		  async: false,
		  data: {command: 'resetSeenMovie', movie: movieCode}
	}).success(function(data) {
		if (data.result === 'success') {
			$('#btnSeenMovie').removeClass('active'); // 영화상세에 있는 버튼
			$('button[name^=btnSeenMovie_'+movieCode+']').removeClass('active');
		} else {
			messageBox( data.resultMessage);
		}
	});
}

function setInterestingMovie(movieCode) {
	$.ajax({
		url: '/pages/movie/Movie_List_Process.jsp',
		type: 'POST',
		async: false,
		data: {command: 'setInterestingMovie', movie: movieCode}
	}).success(function( data ) {
		if (data.result === 'success') {
			$('#btnInterestingMovie').addClass('active'); // 영화상세에 있는 버튼
			$('button[name^=btnInterestingMovie_'+movieCode+']').addClass('active');
		} else {
			messageBox( data.resultMessage);
		}
	});
}

function resetInterestingMovie(movieCode) {
	$.ajax({
		url: '/pages/movie/Movie_List_Process.jsp',
		type: 'POST',
		async: false,
		data: {command: 'resetInterestingMovie', movie: movieCode}
	}).success(function( data ) {
		if (data.result === 'success') {
			$('#btnInterestingMovie').removeClass('active'); // 영화상세에 있는 버튼
			$('button[name^=btnInterestingMovie_'+movieCode+']').removeClass('active');
		} else {
			messageBox( data.resultMessage);
		}
	});
}

function setUninterestingMovie(movieCode) {
	$.ajax({
		url: '/pages/movie/Movie_List_Process.jsp',
		type: 'POST',
		async: false,
		data: {command: 'setUninterestingMovie', movie: movieCode}
	}).success(function( data ) {
		if (data.result === 'success') {
			$('#btnUninterestingMovie').addClass('active'); // 영화상세에 있는 버튼
			$('button[name^=btnUninterestingMovie_'+movieCode+']').addClass('active');
		} else {
			messageBox( data.resultMessage);
		}
	});
}

function resetUninterestingMovie(movieCode) {
	$.ajax({
		url: '/pages/movie/Movie_List_Process.jsp',
		type: 'POST',
		async: false,
		data: {command: 'resetUninterestingMovie', movie: movieCode}
	}).success(function( data ) {
		if (data.result === 'success') {
			$('#btnUninterestingMovie').removeClass('active'); // 영화상세에 있는 버튼
			$('button[name^=btnUninterestingMovie_'+movieCode+']').removeClass('active');
		} else {
			messageBox( data.resultMessage);
		}
	});
}
