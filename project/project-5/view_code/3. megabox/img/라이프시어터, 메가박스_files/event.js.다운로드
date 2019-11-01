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
var _requestEventList = new JSPRequest("/pages/event/Event_List.jsp", "div#eventList", "div#eventList");
var _requestEventDetail = new JSPRequest("/pages/event/Event_Detail.jsp", "div#eventDetail", "div#eventDetail");
var _requestEventWinnersDetail = new JSPRequest("/pages/event/Event_Winners_Detail.jsp", "div#eventWinnersDetail", "div#eventWinnersDetail");
var _requestEventCommentList = new JSPRequest("/pages/event/Event_Detail_Comment_List.jsp", "div#eventCommentList", "div#eventCommentList");
var _requestEventAppliedList = new JSPRequest('/pages/event/Event_Applied_List.jsp', 'div#eventContent', 'div#eventContent');

var EventList = {
	showPage: function() {
		var type = $("input:hidden[name=eventType]").val();
		var gubunStatus = $("input:hidden[name=gubunStatus]").val();
		var region = $("#selectGruop_Event").val();
		var searchName = $("input:text[name=eventSearchName]").val();
//		if(searchName=="")
//			searchName=null;
		try {
			_requestEventList.request({
				type: type,
				region: region,
				searchName : searchName,
				gubunStatus : gubunStatus,
				startNo: 0,
				count: 8
			});
		} catch(e) {
			messageBox(e);
		}
	},

	getMore: function() {
		_requestEventList.put('startNo', _requestEventList.data.startNo + _requestEventList.data.count);
		_requestEventList.processing = 'append';
		_requestEventList.refresh();
		_requestEventList.processing = 'replace';
	},

	addCinemaList: function(id, groupCode) {

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {_command: 'Cinema.getCinemasInRegion', siteCode: 36, areaGroupCode: groupCode, reservationYn: 'N'}

		}).success(function( result ) {

			//$('#'+id).html("<option value=''>영화관선택</option>");

			var list = result.cinemaList;
			for (var i=0; i < list.length; i++) {
				$('#'+id).append("<option value='" + list[i].cinemaCode + "'>" + list[i].cinemaName + "</option>");
			}
			$('#'+id).selectpicker('refresh');
		});
	}
};

// 이벤트상세
var EventDetail =  {
	eventCode: null,

//	init: function() {
//		var me = this;
//		$('#event_detail').on('show.bs.modal', function(e) {
//			var code = $(e.relatedTarget).data('code');
//
//			me.eventCode = code;
//			me.showPage(code);
//		});
//	},

	showPage: function(eventCode, adClick) {
		//[netfunnel] : 이벤트 상세화면 START
		NetFunnel_Action({action_id:"WEB_EVENT_DTL"},function(){
			_requestEventDetail.onSuccess = function() {
				//$('style').remove();
			};
	
			this.eventCode = eventCode;
	
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
	
				_requestEventDetail.request({
					code: eventCode
				});
	
				var menu = "event";
				if(menuId) menu = menuId;
				// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
				// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
				// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
				var moveTitle ='이벤트상세('+eventCode+')' ;
				// URL 변경 및 GA 호출
				var moveURL = "/?menuId="+menu+"&p=detail&p2=" + eventCode;
				megaboxLog.openPopup(moveURL, moveTitle);
			} catch(e) {
				messageBox(e);
			}
	
			// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩 예제
			$('#event_detail').off('hide.bs.modal').on('hide.bs.modal',function() {
				megaboxLog.closePopup();
				$(this).scrollTop(0);
				$('#eventDetail').html(''); //레이어 닫을때 클리어 2015.05.26 김창현
			});
	
		$('#event_detail').modal('show');
		});
	}
}

//이벤트상세
var EventWinnersDetail =  {
	eventCode: null,

	init: function() {
		var me = this;
		$('#winner_detail').on('show.bs.modal', function(e) {
			var code = $(e.relatedTarget).data('code');

			me.eventCode = code;
			me.showPage(code);

			var menu = "event";
			if(menuId) menu = menuId;
			// TODO. 상세 레이어 비표시하면서 이전 타이틀을 표시해주기 위해서는
			// (메인 -> 상세로 이동한 경우 상세 레이어를 닫으면 타이틀에 "메인" 재표시 등)
			// 이전 타이틀의 저장처리등이 필요할 것 같습니다.
			var moveTitle ='당첨자상세('+code+')' ;
			// URL 변경 및 GA 호출
			var moveURL = "/?menuId="+menu+"&p=winners&p2=" + code;
			megaboxLog.openPopup(moveURL, moveTitle);

			// 2. 상세 레이어 비표시할때 타이틀 교체를 위한 이벤트 바인딩 예제
			$('#winner_detail').off('hide.bs.modal').on('hide.bs.modal',function() {
				megaboxLog.closePopup();
				$(this).scrollTop(0);
			});
		});
	},

	showPage: function(eventCode) {

		try {
			_requestEventWinnersDetail.request({
				event: eventCode
			});
		} catch(e) {
			messageBox(e);
		}
	}
};


function winnersshowPage(eventCode)
{
	try {
		_requestEventWinnersDetail.request({
			event: eventCode
		});
	} catch(e) {
		messageBox(e);
	}
};

// 이벤트 댓글
var EventComment = {
	init: function() {
		this.list('createDate');
	},

	list: function(sort) {
		try {
			_requestEventCommentList.request({
				eventCode: EventDetail.eventCode,
				sort: sort,
				startNo: 0,
				count: 3
			});
		} catch(e) {
			messageBox(e);
		}
	},

	regist: function() {
		var comment 	= $("#eventCommentFrm textarea[name=comment]").val();

		if ($.trim(comment) == "") {
			messageBox('내용을 입력해주세요.');
			return;
		}

		var that = this;

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {_command: 'Event.putComment', event: EventDetail.eventCode, comment: comment}
		}).success(function( data ) {
			if (data.resultCode === '0000') {
				$("#eventCommentFrm textarea[name=comment]").val("");

				try {
					_requestEventCommentList.refresh();
				} catch(e) {
					messageBox(e);
				}

				var totalCount = that.getTotalCount(EventDetail.eventCode);
				$('#eventCommentTotalCount').text('('+totalCount+')');

			} else {
				if(typeof data.resultMessage == 'undefined')
					messageBox( data);
				else
					messageBox( data.resultMessage);
			}
		});
	},

	modify: function(obj) {
		$(obj).parents('li').hide();
		$(obj).parents('li').next().show();
	},

	cancelModify: function(obj, comment) {
		$(obj).parents('li').hide();
		$(obj).parents('li').prev().show();
		$('.edit_wrap textarea[name=comment]').val(comment);
	},

	saveModify: function(obj, commentNo) {
		var comment = $('.edit_wrap textarea[name=comment]').val();

		if ($.trim(comment) == "") {
			messageBox('내용을 입력해주세요.');
			return;
		}

		var that = this;

		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			data: {_command: 'Event.editComment', commentNo: commentNo, comment: comment}
		}).success(function( data ) {
			if (data.resultCode === '0000') {

				$(obj).parents('li').hide();

				$(obj).parents('li').prev().find('span.comment').text(comment);
				$(obj).parents('li').prev().fadeIn();

				var totalCount = that.getTotalCount(EventDetail.eventCode);
				$('#eventCommentTotalCount').text('('+totalCount+')');

			} else {
				if(typeof data.resultMessage == 'undefined')
					messageBox( data);
				else
					messageBox( data.resultMessage);
			}
		});
	},

	remove: function(commentNo) {

		var that = this;

		confirmBox('댓글 삭제', '삭제하시겠습니까?', function() {
			$.ajax({
				url: '/DataProvider',
				type: 'POST',
				data: {_command: 'Event.deleteComment', commentNo: commentNo}
			}).success(function( data ) {
				if (data.resultCode === '0000') {
					try {
						_requestEventCommentList.refresh();
					} catch(e) {
						messageBox(e);
					}
					var totalCount = that.getTotalCount(EventDetail.eventCode);
					$('#eventCommentTotalCount').text('('+totalCount+')');
				} else {
					messageBox( data.resultMessage);
				}
			});
		});
	},

	getTotalCount: function(eventCode) {
		var totalCount = 0;
		$.ajax({
			url: '/DataProvider',
			type: 'POST',
			async: false,
			data: {_command: 'Event.getCommentList', event: eventCode, startNo: 0, count: 9999}

		}).success(function( data ) {
			totalCount = data.count;
		});
		return totalCount;
	},

	getMoreData: function() {
		var startNo = _requestEventCommentList.data.startNo + _requestEventCommentList.data.count;

		try {
			_requestEventCommentList.processing = 'append';
			_requestEventCommentList.put('startNo', startNo);
			_requestEventCommentList.refresh();
			_requestEventCommentList.processing = 'replace';
		} catch(e) {
			messageBox(e);
		}
	}
};

//나의 응모 내역
var EventApplied = {

	// 리스트 페이지
	showListPage: function(pageNo) {
		var search = $("input:text[name=eventSearchName]").val()||'';
		try {
			_requestEventAppliedList.request({
				search: search
				, _pageNo: pageNo
				, _rowsPerPage: 10
			});
		} catch(e) {
			messageBox(e);
		}
	}
};

function eventDetailViewClose(){
	if(loginUtil.getLoginResultCode()=='0000'){
		location.reload();
	}
}