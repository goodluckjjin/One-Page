//$.fn.scrollingDown = function(JSPRequest, callback) {
//
//	var $this = $(this);
//	var $targetContent = $this.children('ul');
//
//	$this.scroll(function() {
//		if(($this.height() + $this.scrollTop()) ==  $targetContent.height()) {
//			loadData(JSPRequest.url, JSPRequest.data);
//		}
//	});
//
//	function loadData(url, data, appendedEl) {
//		data.startNo = parseInt(data.startNo) + parseInt(data.count);
//
//		$.ajax({
//			url: url,
//			type: 'GET',
////			async: false,
//			data: data,
//			beforeSend: function() {
//
//			}
//		}).success(function(result) {
//console.log('result after scroll down : ', result);
//			callback(result);
//		});
//	}
//}

/**
 * [Usage]
 * $(selector).slideIndicator(target, function(result) {}, [options]);
 *
 * [parameter]
 * selector: 사용할 슬라이드 선택
 * target: 슬라이드에 반응할 scroll영역 선택
 * callback: scroll컨텐츠영역에서 가장 상단에 있는 item값(result)을 가진 콜백
 * options: {
 * 	numberForSlide: 한 slide에 표현되는 item 갯수지정 (deafult: 10)
 *  callMoreData: 다음 데이터를 호출할 사용자지정 function. boolean 형으로 리턴해야한다
 * }
 *
 * [Example]
 * <div id="slide"><ul><li><a data-selector="10"></a></li><ul></div>
 * <div id="scroll"><ul><li class="item" data-slide-indicator="10"><li></div>
 *
 * $('slide').slideIndicator('scroll', function(result) {
 *  // if you scroll,
 * 	console.log(result); // output: 10
 * });
 */
;(function($) {

	"use strict";

	//var MAX_CALL_COUNT = 20;
	var CONFIG_NUMBER_FOR_SLIDE = 10;

	var _slideData={}, _minGroup={}, _maxGroup={}, _groups=[], _contentDatas=[];
	//var _callMoreData;
	var _tmpGroupName;

	$.fn.moveSlideChange = function(groupName) {
		var $this = this;
		moveSlide($this, groupName);
	};

	$.fn.slideIndicator = function(target, callback, options) {

		if (typeof(options) != 'undefined' && options instanceof Object) {
			if (typeof(options.numberForSlide) != 'undefined') {
				CONFIG_NUMBER_FOR_SLIDE = options.numberForSlide;
			}
			if (typeof(options.maxCallCount) != 'undefined') {
				MAX_CALL_COUNT = options.maxCallCount;
			}

			if (typeof(options.callMoreData) == 'function') {
				//console.log('This is Function!');
				_callMoreData = options.callMoreData;
			} else {
				//console.log('This is not Function!');
			}

		} else {
			//console.log('Not support format.');
		}

		var $this = this;
		var $target = $(target);
		var $targetContent = $target.children('ul');

		var contentTop = $targetContent.offset().top;

		loadSlideData($this);
		reloadData($targetContent);

		addClickHandler($this, $target, $targetContent);
		addScrollHandler($this, $target, $targetContent, contentTop, callback);
	};

	$.fn.slideUpdateData = function(target){
		var $target = $(target);
		reloadData($target);
	};

	var loadContentData = function($targetContent) {
//		setTimeout(function() {
			$targetContent.children('li').each(function(index) {
				pushData($(this).data('slide-indicator'), ($(this).height()+1));
			});
			contentDataParser();
//		}, 400);
	};

//	$.fn.loadData = function() {
//		console.log('loadData!!');
//		loadData();
//	}

	var reloadData = function($targetContent) {
		dataReset();
		loadContentData($targetContent);
	};

	var contentDataParser = function() {
		var index = (_groups.length > 0) ?[_groups.length - 1] : 0;
		var tvalue = 0; // 임시값

		for (var i=0; i < _contentDatas.length; i++) {
			if (i == (_contentDatas.length - 1)) {
				break;
			}

			if (i == 0) {
				var group = {};
				group.name = _contentDatas[0].name;
				group.posY = 0;
				_groups.push(group);

				tvalue = _contentDatas[0].value;

				_minGroup.name = _contentDatas[0].name;
				_minGroup.posY = 0;

				_maxGroup.name = _contentDatas[0].name;
				_maxGroup.posY = 0;

				continue;
			}

			if (_contentDatas[i].name != _contentDatas[i-1].name) {
				var group = {};
				group.name = _contentDatas[i].name;
				group.posY = tvalue;
				_groups.push(group);

				index++;
			}

			// group 최소값, 최대값 저장
			if((_minGroup.name) > _contentDatas[i].name) {
				_minGroup.name = _contentDatas[i].name;
				_minGroup.posY = tvalue;
			}
			if ((_maxGroup.name) < _contentDatas[i].name) {
				_maxGroup.name = _contentDatas[i].name;
				_maxGroup.posY = tvalue;
			}

			tvalue += _contentDatas[i].value;
		}
		//console.log('============= GROUP =============');
		//console.log(_groups);
		//console.log('=================================');
	};

	var getGroupName = function(scrollTop) {
		if (_groups.length == 1) {
			return _groups[0].name;
		}

		for (var i=0; i < _groups.length; i++) {
			if (i == (_groups.length - 1)) {
				return _groups[_groups.length-1].name;
			}

			if (scrollTop >= _groups[i].posY && scrollTop < _groups[i+1].posY) {
				return _groups[i].name;
			}
		}
	};

	var pushData = function(name, value) {
		var data = {};
		data.name = name;
		data.value = value;
		_contentDatas.push(data);
	};

	var dataReset = function() {
		_contentDatas = [];
		_groups = [];
	};

	var addScrollHandler = function($this, $target, $targetContent, contentTop, callback) {
		$target.scroll(function() {

			var scrollTop = $target.scrollTop();// 스크롤 내용부분의 offset
			var groupName = getGroupName(scrollTop);

			// 빠르게 스크롤할 경우,
			// data를 새로 parsing하는 동안 groupName을 가져오려고 하기때문에 undefined가 발생하여 임시값 추가.
			if (typeof groupName === 'undefined') {
				groupName = _tmpGroupName;
			} else {
				_tmpGroupName = groupName;
			}

			// modal팝업시 처리
			if (contentTop == 0) {
				contentTop = $targetContent.offset().top;
//				reloadData($targetContent);
//				loadSlideData($this);
			}

			// 스크롤 마지막일때 데이터 갱신
//			if(($target.height() + $target.scrollTop()) ==  $targetContent.height()) {
//console.log('refresh data because current scroll is bottom.');
//				reloadData($targetContent);
//			}
			moveSlide($this, groupName);
			slideItemActive($this, groupName);

			callback(groupName);
		});
	};

	var addClickHandler = function($this, $target, $targetContent) {
		$this.children('li').each(function(index) {
			$(this).off('click');
			$(this).on('click', function() {
				var groupName = $(this).data('select');
				if (typeof _tmpGroupName == 'undefined') {
					_tmpGroupName = groupName;
				}
				moveScroll($target, $targetContent, groupName);
			});
		});
	};

	var loadSlideData = function($this) {

		setTimeout(function() {
			var activeData;
			var firstLeft = $this.children('li').first().offset().left;
			$this.children('li').each(function() {
				var name = $(this).data('select');
				var left = ($(this).offset().left - firstLeft);
				_slideData[name] = left;

				if ($(this).children('a').hasClass('active')) {
					activeData = $(this).data('select');
				}
			});
			moveSlide($this, activeData);
		}, 1000);
	};

	var moveSlide = function($this, groupName) {
		var posX = _slideData[groupName];
		var slideItemWidth = $this.children('li').first().width();
		var totalItemWidth = (slideItemWidth * $this.children('li').length);
		var viewWidth = (slideItemWidth * CONFIG_NUMBER_FOR_SLIDE);
		var maxPosX = totalItemWidth - viewWidth;

		var moveX = (posX - (slideItemWidth * Math.floor(CONFIG_NUMBER_FOR_SLIDE / 2)));
		if (maxPosX < moveX) {
			moveX = maxPosX;
		}

		$this.css({
			left: -moveX
		});
	};

	//var callCount = 0;
	var moveScroll = function($target, $targetContent, groupName) {
		//var isFind = false; // groupName 찾았는지 여부

		var posY = 0;		// 스크롤할 groupName Y좌표
		for (var i=0; i < _groups.length; i++) {
			if (_groups[i].name == groupName) {
				posY  = _groups[i].posY;
				//isFind = true;
				break;
			}
		}

		// 해당페이지에 없고, 더 가져올 데이터가 존재하는지 확인
//		if (typeof _callMoreData !== 'undefined' && ((!isFind) && _callMoreData())) {
//			if (callCount > MAX_CALL_COUNT) {
//				return;
//			}
//
//			callCount++;
//			reloadData($targetContent);
//
//			moveScroll($target, $targetContent, groupName); // 재귀호출
//
//		} else {// 찾거나, 더 가져올 데이터가 없다면 해당 위치로 스크롤

			if (groupName < _minGroup.name) {
				posY = _minGroup.posY;
			}
			if (groupName > _maxGroup.name) {
				posY = _maxGroup.posY;
			}

//			$target.scrollTop(posY);
			$target.animate({scrollTop: posY});

			//callCount = 0;
		//}
	};

	var slideItemActive = function($this, groupName) {
		$this.children('li').each(function() {
			if ($(this).data('select') == groupName) {
				$(this).children('a').addClass('active');
			} else {
				$(this).children('a').removeClass('active');
			}
		});
	};

})(jQuery);

// class: imageslide-next, imageslide-prev, imageslide-viewport
;(function($) {

	"use strict";

	var $this;
	var $preview;
	var _selectedImageIndex,
		_totalImageWidth,
		_currMovedLeft,
		_viewportWidth;
	var _slideDatas;
	var _indexData;

	$.fn.imageSlide = function(preview) {
		$this = this;
		$preview = $(preview);

		init();

		loadData();
		addClickHandler();

		showPreview();
	};

	var init = function() {
		_selectedImageIndex = 0;
		_totalImageWidth = 0;
		_currMovedLeft = 0;
		_viewportWidth = $this.parent('.imageslide-viewport').width();
		_slideDatas=[];
		_indexData={};
	};

	var loadData = function() {
		// dom이 로딩되기 전에 parsing되는 것을 방지. (임시처리)
		setTimeout(function(){ setData(); }, 400);
	};

	var setData = function() {
		 // reset
		_slideDatas = [];
		_totalImageWidth = 0;

		var group = 0;
		var tvalue = 0;

		_indexData[group] = [];

		$this.children('li').each(function(index) {
			var item = {};
			item.width = $(this).width();

			_totalImageWidth += item.width;

			tvalue += item.width;

			if (index == 0) {
				item.group = 0;
				item.posxByGroup = 0;
				item.posx = 0;
				item.isFirst = true;

			} else {
				item.group = group;
				item.posx = _slideDatas[index-1].posx + _slideDatas[index-1].width;

				if (tvalue > _viewportWidth) {
					tvalue = item.width;
					group++;
					item.group = group;

					_indexData[group] = [];
				}

				if (_slideDatas[index-1].group != item.group) {
					item.posxByGroup = item.posx;
					item.isFirst = true;
				} else {
					item.posxByGroup = _slideDatas[index-1].posxByGroup;
					item.isFirst = false;
				}
			}

			_indexData[group].push(index);

			_slideDatas.push(item);
		});

		$this.css({
			width: _totalImageWidth+10
		});
	};

	var addClickHandler = function() {
		$('.imageslide-prev').on('click', function() {
			slide('prev');
			activeImage();
			showPreview();

			$this.trigger('get.index.slide', _selectedImageIndex);
		});

		$('.imageslide-next').on('click', function() {
			slide('next');
			activeImage();
			showPreview();

			$this.trigger('get.index.slide', _selectedImageIndex);
		});

		$this.children('li').on('click', function() {
			_selectedImageIndex = $(this).index();
			activeImage();
			showPreview();

			_currMovedLeft = -(_slideDatas[_selectedImageIndex].posxByGroup);

			if (getLastGroup() == _slideDatas[_selectedImageIndex].group) {
				var firstItemRemainWidthOfLastGroup = (_viewportWidth - getGroupWidth(getLastGroup() - 1));
				var resultWidth = getGroupWidth(getLastGroup()) - firstItemRemainWidthOfLastGroup;
				_currMovedLeft = -(getPosxByGroup(getLastGroup() - 1) + resultWidth);

			}

			moveSlide(_currMovedLeft);

			$this.trigger('get.index.slide', _selectedImageIndex);
		});
	};

	var slide = function(direct) {
		if (direct == 'prev') {
			// 선택된 아이템이 첫번째인덱스인 경우
			if (_selectedImageIndex == 0) {
				return;
			} else {
				_selectedImageIndex--;
			}

			if (_slideDatas[_selectedImageIndex].group == 0) {
				_currMovedLeft = 0;
			} else {
				_currMovedLeft = -(_slideDatas[_selectedImageIndex].posxByGroup);
			}

			if (getLastGroup() != _slideDatas[_selectedImageIndex].group) {
				moveSlide(_currMovedLeft);
			}
		}

		if (direct == 'next') {
			// 선택된 아이템이 마지막인덱스인 경우
			if (_selectedImageIndex == (_slideDatas.length-1)) {
				return;
			} else {
				_selectedImageIndex++;
			}

			if (getLastGroup() == _slideDatas[_selectedImageIndex].group) {
				var firstItemRemainWidthOfLastGroup = (_viewportWidth - getGroupWidth(getLastGroup() - 1));
				var resultWidth = getGroupWidth(getLastGroup()) - firstItemRemainWidthOfLastGroup;
				_currMovedLeft = -(getPosxByGroup(getLastGroup() - 1) + resultWidth);

			} else {
				_currMovedLeft = -(_slideDatas[_selectedImageIndex].posxByGroup);
			}

			moveSlide(_currMovedLeft);
		}
	};

	var moveSlide = function(value) {
		if (_totalImageWidth > _viewportWidth) {
			$this.animate({left: value+'px'}, 'fast');
		}
	};

	var showPreview = function() {
		$this.children('li').each(function(index) {
			if (index == _selectedImageIndex) {
				if ($(this).hasClass('mov')) {

					$preview.children('div.btn_wrap').hide();

					var $previewImage = $preview.find('img');
					var $previewVideo = $preview.find('video');

					$previewImage.hide();

					//$previewVideo.get(0).play();
					$previewVideo.show();
					//$previewVideo.children('source').attr('src', 'http://m.mvod.megabox.co.kr/encodeFile/3550/2013/12/05/aeaf1d3e59f6306ab9f65a1de650428c_I.mp4');
					if (Modernizr.canvas) {
						$('.imageslide-video').css('display', 'block');
					} else {
						$('#smarthd_player').css('display', 'block');
					}

				} else {

					$preview.children('div.btn_wrap').show();

					var $image = $(this).children('img');

					var imgWidth = $image.width();
					var imgHeight = $image.height();
//					var previewWidth = $preview.width();
//					var previewHeight = $preview.height();

	//				if (imgWidth > previewWidth) {
	//					$preview.children('img').css('width', '100%');
	//				}
	//
	//				if (imgHeight > previewHeight) {
	//					$preview.children('img').css('height', '100%');
	//				}

//					if (imgWidth > imgHeight) {
//						$preview.children('img').css({
//							width: 'auto',
//							height: '100%'
//						});
//					}

//					else {
//						$preview.children('img').css({
//							width: '100%',
//							height: 'auto'
//						});
//					}

					var imageSrc = $image.attr('src');

//console.log(' ========= [image info] ========');
//console.log('img src: ', imageSrc);
//console.log('img height: ', $preview.children('img').css('height'));
//console.log('img width: ', $preview.children('img').css('width'));
//console.log(' ===============================');

					var $previewImage = $preview.find('img');
					var $previewVideo = $preview.find('video');

					$previewImage.show();
					if (Modernizr.canvas) {
						$previewVideo.get(0).pause();
						$previewVideo.hide();
					}
					$previewImage.attr('src', imageSrc);
				}
			}
		});
	};

	var activeImage = function() {
		$this.children('li').each(function(index) {
			if (index == _selectedImageIndex) {
				$(this).addClass('active');
			} else {
				$(this).removeClass('active');
			}
		});
	};

	var getSubWidthTotal = function(start, end) {
		var width = 0;

		if (start > _slideDatas.length) {
			return;
		}

		if (end >= _slideDatas.length) {
			return;
		}

		if (start > end) {
			for (var i=start; i >= end; i--) {
				width += _slideDatas[i].width;
			}
		} else {
			for (var i=start; i <= end; i++) {
				width += _slideDatas[i].width;
			}
		}

		return width;
	};

	var getLastGroup = function() {
		return _slideDatas[_slideDatas.length - 1].group;
	};

	var getGroupWidth = function(group) {
		if (group < 0) {
			group = 0;
		}
		var indexes = _indexData[group];
		return getSubWidthTotal(indexes[0], indexes[indexes.length-1]);
	};

	var getPosxByGroup = function(group) {
		if (group < 0) {
			group = 0;
		}
		var indexes = _indexData[group];
		return _slideDatas[indexes[0]].posxByGroup;
	};

})(jQuery);