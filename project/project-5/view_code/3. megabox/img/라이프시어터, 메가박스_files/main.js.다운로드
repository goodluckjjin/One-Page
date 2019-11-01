var _requestMainMovieList = new JSPRequest('/pages/main/Main_Movie.jsp', '', '');
var _requestMainEventNew = new JSPRequest('/pages/main/Main_Event_New.jsp', 'div#mainEventNew', 'div#mainEventNew');
var _requestMainEventPartner = new JSPRequest('/pages/main/Main_Event_Partner.jsp', 'div#mainEventPartner', 'div#mainEventPartner');
var _requestMainParallax = new JSPRequest('/pages/main/Main_BannerParallax.jsp', 'div#mainBannerParallax', 'div#mainBannerParallax');
var _requestMainNotice = new JSPRequest('/pages/main/Main_Notice.jsp', '', '');
var _requestMainCardSearch = new JSPRequest('/pages/main/Main_CardSearch.jsp', 'div#cardSearch', 'div#cardSearch');
var _requestMainCardList = new JSPRequest('/pages/main/Main_CardList.jsp', '#cardList', '#cardList');

var MainMovieList = {
		
	showPage: function(type) {
		var target = '';
		var container = '';
		
		if (type == 'boxoffice') {
			target = '.flip_box dl dd#tab1';
			container = '.flip_box dl dd#tab1';	
			
		} else if (type == 'showing') {
			target = '.flip_box dl dd#tab2';
			container = '.flip_box dl dd#tab2';			
		} else if (type == 'scheduled') {
			target = '.flip_box dl dd#tab3';
			container = '.flip_box dl dd#tab3';			
		} else {
			target = '.flip_box dl dd#tab4';
			container = '.flip_box dl dd#tab4';			
		}
		
		try {
			_requestMainMovieList.target = target;
			_requestMainMovieList.container = container;
			_requestMainMovieList.request({
				type: type
			});
		} catch(e) {
			messageBox(e);
		}		
	}
};

// 메인 - 새로운 이벤트
var MainEventNew = {
	showPage: function() {
		try {
			_requestMainEventNew.request();
		} catch(e) {
			messageBox(e);
		}
	}	
}

// 메인 - 제휴이벤트
var MainEventPartner = {
	showPage: function() {
		try {
			_requestMainEventPartner.request();
		} catch(e) {
			messageBox(e);
		}
	}
};

// 메인 - 배너(패럴럭스)
var MainBannerParallax = {
	showPage: function() {
		try {
			_requestMainParallax.request();
		} catch(e) {
			messageBox(e);
		}
	}	
};

// 메인 - 공지사항
var MainNotice = {
	showPage: function(type) {
		
		var target = '';
		var container = '';
		
		if (type == 'MEG') {
			target = 'div#notice_tab1';
			container = 'div#notice_tab1';
		} else {
			target = 'div#notice_tab2';
			container = 'div#notice_tab2';
		}
		
		try {
			_requestMainNotice.target = target;
			_requestMainNotice.container = container;
			_requestMainNotice.request({
				type: type
			});
		} catch(e) {
			messageBox(e);
		}
	}
};

// 메인 - 할인카드
var MainCardSearch = {
	showPage: function(adClick) {
		var search = $('input:hidden[name=searchCard]').val();
		
		try {
			// dataSource 클릭 시 클릭 수 인자 전달 
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
			
			
			_requestMainCardSearch.request({
				search: search
			});
		} catch(e) {
			messageBox(e);
		}
		
		$('#card_search').modal('show');
	},
	showList: function(){
		//$("#cardList").html("bbb");
		_requestMainCardList.request();
	}

};