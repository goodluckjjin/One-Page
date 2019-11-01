  (function($) {

     $.ajaxSend = function(settings) {
          var wts_ajax=new wtsAjax();
          wts_ajax.ajaxStart(settings);
     };
     
     function wtsAjax(settings, returnType) {

         this.isEmpty=function(value){
     	    var rslt = false;
     	    if(typeof value == 'undefined' || value == null || value.trim() == ''){
     	        rslt = true;
     	    }else if(value instanceof Array && value.lenth == 0){
     	        rslt = true;
     	    }else if(value instanceof Object && $.isEmptyObject(value)){
     	        rslt = true;
     	    }
     	    return rslt;
         };

          this.ajaxStart=function(setting, returnType)
          {
              this.settings = jQuery.extend({
                  method: "POST",
                  async: true,		// 비동기(true), 동기(false)
                  url: "",
                  param: undefined,
                  dataType: "json",
                  layerId: "",
                  secure:"N",
                  callback: function() {},
                  beforeSend: function() {},
                  complete: function() {},
                  lang : "k"
              }, setting);

              if(typeof this.settings.param=='undefined')
            	  this.settings.param={};

              var setValue=this.settings;
              var self=this;
              
              if(setValue.url=="")
              {
            	  if(setValue.secure=="Y")
            	  {
            		  if(location.hostname.indexOf('localhost')>-1)
            		  {
            			  setValue.url="https://"+location.hostname+":8443/DataProvider";
            		  }
            		  else
            		  {
            			  setValue.url="https://"+location.hostname+"/DataProvider";  
            		  }
            	  }
            	  else
            	  {
            		  if(location.hostname.indexOf('localhost')>-1)
            		  {
            			  setValue.url="http://"+location.host+"/DataProvider";
            		  }
            		  else
            		  {
            			  setValue.url="http://"+location.hostname+"/DataProvider";  
            		  }            		  
            	  }
              }
              
              $.ajax({type: setValue.method, url: setValue.url, data: setValue.param, dataType: setValue.dataType, async: setValue.async,
                  success: function(result, textStauts, xhr) {
                       try {
               			
           				setValue.callback(result);

                       } catch(err) {
                            if(setValue.layerId != "") {
                            	self.removeLoadingBar();
                            }
                        	alert(err);
                       }
                  }, error: function(xhr, textStauts, thrownError) {
                       alert(thrownError);
                  }, beforeSend : function() {
                      if(setValue.layerId != "") {
                    	  self.addLodingBar();
                      }
                  }, complete: function(xhr, textStauts) {
                	  if(setValue.layerId != "") {
                		  self.removeLoadingBar();
                      }
                  }
             });

          };

          this.addLodingBar=function()
          {
              var str = "";
              var top = 0;
              var left = 0;
              var layerTop = $("#" + this.settings.layerId).scrollTop();
              var layerWidth = $("#" + this.settings.layerId).width();
              var layerHeight = $("#" + this.settings.layerId).height();

              var pad_top='0';
              if($("#" + this.settings.layerId).css("padding-top"))
            	  pad_top = $("#" + this.settings.layerId).css("padding-top").replace("px", "").replace("auto", "0");
              
              var pad_bottom='0';
              if($("#" + this.settings.layerId).css("padding-bottom"))
            	  pad_bottom = $("#" + this.settings.layerId).css("padding-bottom").replace("px", "").replace("auto", "0");
              
              var pad_left='0';
              if($("#" + this.settings.layerId).css("padding-left"))
            	  pad_left = $("#" + this.settings.layerId).css("padding-left").replace("px", "").replace("auto", "0");

              var pad_right='0';
              if($("#" + this.settings.layerId).css("padding-right"))
            	  pad_right = $("#" + this.settings.layerId).css("padding-right").replace("px", "").replace("auto", "0");
              
              var mar_top='0';
              if($("#" + this.settings.layerId).css("margin-top"))
            	  mar_top = $("#" + this.settings.layerId).css("margin-top").replace("px", "").replace("auto", "0");
              
              var mar_bottom='0';
              if($("#" + this.settings.layerId).css("margin-bottom"))
            	  mar_bottom = $("#" + this.settings.layerId).css("margin-bottom").replace("px", "").replace("auto", "0");
              
              var mar_left='0';
              if($("#" + this.settings.layerId).css("margin-left"))
            	  mar_left = $("#" + this.settings.layerId).css("margin-left").replace("px", "").replace("auto", "0"); 
              
              var mar_right='0';
              if($("#" + this.settings.layerId).css("margin-right"))
            	  mar_right = $("#" + this.settings.layerId).css("margin-right").replace("px", "").replace("auto", "0");   


              layerWidth += parseInt(pad_left) + parseInt(pad_right) + parseInt(mar_left) + parseInt(mar_right);
              layerHeight += parseInt(pad_top) + parseInt(pad_bottom) + parseInt(mar_top) + parseInt(mar_bottom);

              str  = "<div id=\"" + this.settings.layerId + "_loading\" style=\"width:16px;height16px;\">";
             // str += "     <img src=\"/redbeanCore/images/loading.gif\"/>";
              str += "</div>";


              $("#" + this.settings.layerId).append(str);                  
              $("#" + this.settings.layerId).append("<div id=\"" + this.settings.layerId + "_loadingBg\"></div>");


              top = (layerHeight / 2) - ($("#" + this.settings.layerId + "_loading").height() / 2) + layerTop;
              left = (layerWidth / 2) - ($("#" + this.settings.layerId + "_loading").width() / 2);


              if($("#" + this.settings.layerId).css("position") != "absolute") {
                   $("#" + this.settings.layerId).css({"position":"relative"});
              }

              $("#" + this.settings.layerId + "_loading").css({
                   "top": top,
                   "left": left,
                   "position":"absolute",
                   "z-index":"9999"
              });

              var loadingBgHgt = "100%";

              if($("#" + this.settings.layerId).height() != null && $("#" + this.settings.layerId).height() != "undefined") {
                   loadingBgHgt = $("#" + this.settings.layerId).height() + "px";
              }

              $("#" + this.settings.layerId + "_loadingBg").css({
                   "top": layerTop,
                   "left": 0,
                   "width": "100%",
                   "height": loadingBgHgt,
                   "background": "black",
                   "opacity": 0.08,
                   "position": "absolute",
                   "z-index":"9998"
              });
          };

          this.removeLoadingBar=function()
          {
              $("#" + this.settings.layerId + "_loading").remove();
              $("#" + this.settings.layerId + "_loadingBg").remove();
          };

     }
})(jQuery);