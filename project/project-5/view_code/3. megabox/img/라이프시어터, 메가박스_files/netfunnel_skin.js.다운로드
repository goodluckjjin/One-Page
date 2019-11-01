if(typeof NetFunnel == "object"){
	NetFunnel.SkinUtil.add('megabox',{
		prepareCallback:function(){
			//var progress_print = document.getElementById("Progress_Print");
			//progress_print.innerHTML="0 % (0/0) - 0 sec";
		},
		updateCallback:function(percent,nwait,totwait,timeleft){
			//var progress_print = document.getElementById("Progress_Print");
			//var prog=totwait - nwait;
			//progress_print.innerHTML=percent+" % ("+prog+"/"+totwait+") - "+timeleft+" sec";
			var tempWidth = document.getElementById("NetFunnel_Loading_Popup_Progressbar_CUSTOM").style.width.replace("px","");
			var tempTarget = document.getElementById("NetFunnelCustomProgressbar");
            var i = tempWidth - Math.round((NetFunnel.gLastData.nwait / NetFunnel.gTotWait) * tempWidth);
            tempTarget.style.width = i + "px";
            //console.log(NetFunnel.gLastData.nwait +"/"+ NetFunnel.gTotWait);

		},
		
		htmlStr:'<div id="NetFunnel_Skin_Top" style="width: 430px; background-color: #fff; padding: 29px 17px; border:1px solid #eee; box-sizing:border-box;"> \
					<h2 style="font-size:18px; overflow:hidden; height:25px; margin:0; color: #333;font-weight: 700; position: relative; text-align: center;"> 접속대기안내 <span onclick="NetFunnel.countdown_stop();" style="position: absolute; right:10px; top:3px; width:18px; height:18px;background:url(http://image2.megabox.co.kr/mop/home/btns/btn_close.png) no-repeat 0 0"></span></h2> \
					<div style="padding: 20px 0 10px 0; text-align: center; border-top:2px solid #dadada; margin:5px 0;box-sizing:border-box;"> \
						<div style="font:17px "NanumGothic","나눔고딕","맑은 고딕","Malgun Gothic","돋움",dotum,"Apple SD Gothic Neo",sans-serif; color: #333333">현재 대기 인원<span style="display: block; color: #333; font-size:40px; font-weight: 500; margin:10px 0;"><span id="NetFunnel_Loading_Popup_Count">11111</span>명</span></div> \
						<p style="font:15px "NanumGothic","나눔고딕","맑은 고딕","Malgun Gothic","돋움",dotum,"Apple SD Gothic Neo",sans-serif; line-height: 1.6; padding: 0; margin: 0;">페이지 사용량이 많아 접속이 지연되고 있습니다.</p> \
						<p style="font:15px "NanumGothic","나눔고딕","맑은 고딕","Malgun Gothic","돋움",dotum,"Apple SD Gothic Neo",sans-serif; line-height: 1.6; padding: 0; margin: 0;">잠시만 기다려 주세요.</p> \
						<div id="NetFunnel_Loading_Popup_Progressbar_CUSTOM" style="width:395px; height: 30px; margin:15px 0; background-color: #d5d5d5; text-align: left;box-sizing:border-box;border-radius: 30px;overflow:hidden;"> \
							<span id="NetFunnelCustomProgressbar" style="display: inline-block; height: 100%; background-color: #4e3497; width: 0px;box-sizing:border-box;border-radius: 30px;color:#fff; font-size: 15px; padding:4px 0 0 20px;overflow: hidden;">LOADING...</span> \
						</div> \
						<div style="color:#666; font:12px "NanumGothic","나눔고딕","맑은 고딕","Malgun Gothic","돋움",dotum,"Apple SD Gothic Neo",sans-serif;">[주의] 새로고침 또는 뒤로가기를 하시면 대기 시간이 다시 부여됩니다.</div> \
					</div> \
				</div>'
			
	},'normal');
}