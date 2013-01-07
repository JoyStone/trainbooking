/*
 *  12306 Auto Query => A javascript snippet to help you book tickets online.
 *  12306 Booking Assistant
 *  Copyright (C) 2011 Hidden
 * 
 *  12306 Auto Query => A javascript snippet to help you book tickets online.
 *  Copyright (C) 2011 Jingqin Lynn
 * 
 *  12306 Auto Login => A javascript snippet to help you auto login 12306.com.
 *  Copyright (C) 2011 Kevintop
 * 
 *  Includes jQuery
 *  Copyright 2011, John Resig
 *  Dual licensed under the MIT or GPL Version 2 licenses.
 *  http://jquery.org/license
 * 
 *  This program is free software: you can redistribute it and/or modify
 *  it under the terms of the GNU General Public License as published by
 *  the Free Software Foundation, either version 3 of the License, or
 *  (at your option) any later version.
 * 
 *  This program is distributed in the hope that it will be useful,
 *  but WITHOUT ANY WARRANTY; without even the implied warranty of
 *  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 *  GNU General Public License for more details.
 * 
 * 
 */

// ==UserScript==  
// @name         12306 Booking Assistant
// @version  	 1.4.1
// @author       
// @namespace    https://github.com/JoyStone
// @description  订票助手
// @include      *://dynamic.12306.cn/otsweb/*
// @require	 https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js
// ==/UserScript== 

function withjQuery(callback, safe){
	if(typeof(jQuery) == "undefined") {
		var script = document.createElement("script");
		script.type = "text/javascript";
		script.src = "https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js";

		if(safe) {
			var cb = document.createElement("script");
			cb.type = "text/javascript";
			cb.textContent = "jQuery.noConflict();(" + callback.toString() + ")(jQuery, window);";
			script.addEventListener('load', function() {
				document.head.appendChild(cb);
			});
		}
		else {
			var dollar = undefined;
			if(typeof($) != "undefined") dollar = $;
			script.addEventListener('load', function() {
				jQuery.noConflict();
				$ = dollar;
				callback(jQuery, window);
			});
		}
		document.head.appendChild(script);
	} else {
		setTimeout(function() {
			//Firefox supports
			callback(jQuery, typeof unsafeWindow === "undefined" ? window : unsafeWindow);
		}, 30);
	}
}

withjQuery(function($, window){
	$(document).click(function() {
		if( window.webkitNotifications && window.webkitNotifications.checkPermission() != 0 ) {
			window.webkitNotifications.requestPermission();
		}
	});
	function notify(str, timeout, skipAlert) {
		if( window.webkitNotifications && window.webkitNotifications.checkPermission() == 0 ) {
			var notification = webkitNotifications.createNotification(
				"http://www.12306.cn/mormhweb/images/favicon.ico",  // icon url - can be relative
				'订票',  // notification title
				str
			);
			notification.show();
			if ( timeout ) {
				setTimeout(function() {
					notification.cancel();
				}, timeout);
			}
			return true;
		} else {
			if( !skipAlert ) {
				alert( str );
			}
			return false;
		}
	}
	function route(match, fn) {
		if( window.location.href.indexOf(match) != -1 ) {
			fn();
		};
	}


	function OrderQueueWaitTime(tourFlag, waitMethod, finishMethod) {
	    this.tourFlag = tourFlag;
	    this.waitMethod = waitMethod;
	    this.finishMethod = finishMethod;

	    this.dispTime = 1;
	    this.nextRequestTime = 1;
	    this.isFinished = false;
	    this.waitObj;
	}

	
	function query() {

		//query
        var maxIncreaseDay  = 0 ;
        var start_autoIncreaseDay = null ;
        var index_autoIncreaseDay = 1 ;
        var pools_autoIncreaseDay = []  ;
        function  __reset_autoIncreaseDays(){
            maxIncreaseDay   = parseInt( document.getElementById('autoIncreaseDays').value ) || 1 ;
            if( maxIncreaseDay > 10 ) {
                maxIncreaseDay  = 10 ;
            }
            document.getElementById('autoIncreaseDays').value   = maxIncreaseDay ;
            start_autoIncreaseDay   = null ;
            $('#app_next_day,#app_pre_day').addClass('disabled').css('color', '#aaa' );
        }
        function  __unset_autoIncreaseDays(){
            if( start_autoIncreaseDay ) {
                document.getElementById('startdatepicker').value    = start_autoIncreaseDay ;
                start_autoIncreaseDay   = null ;
            }
            $('#app_next_day,#app_pre_day').removeClass('disabled').css('color', '#000' );
        }
        function __date_format( date ) {
                var y   = date.getFullYear() ;
                var m   = date.getMonth() + 1 ;
                var d   =  date.getDate() ;
                if( m <= 9 ) {
                    m = '0' + String( m ) ;
                } else {
                    m = String(  m ) ;
                }
                if( d <= 9 ) {
                    d = '0' + String(  d ) ;
                } else {
                    d = String( d );
                }
                return  String(y) + '-' + m + '-' + d ;
        }
        function __date_parse(txt){
                var a  =  $.map(txt.replace(/^\D+/, '').replace(/\D$/, '' ).split(/\D+0?/) , function(i){
                    return parseInt(i) ;
                }) ;
                a[1]    -= 1 ;
                var   date  = new Date;
                date.setFullYear(  a[0]    ) ;
                date.setMonth( a[1]  , a[2]  ) ;
                date.setDate( a[2] ) ;
                return date ;
        }
        function  __set_autoIncreaseDays() {
            if( !start_autoIncreaseDay ) {
                start_autoIncreaseDay   =  document.getElementById('startdatepicker').value ;
                var date = __date_parse(start_autoIncreaseDay);
                pools_autoIncreaseDay  = new Array() ;
                for(var i = 0 ; i < maxIncreaseDay  ; i++) {
                    pools_autoIncreaseDay.push(  __date_format(date) ) ;
                    date.setTime(  date.getTime() + 3600 * 24 * 1000 ) ;
                }
                index_autoIncreaseDay = 1 ; 
                return ;
            }
            if( index_autoIncreaseDay >= pools_autoIncreaseDay.length ) {
                index_autoIncreaseDay   = 0 ;
            }
            var value   = pools_autoIncreaseDay[index_autoIncreaseDay++];
             document.getElementById('startdatepicker').value   = value ;
        }
        function getTimeLimitValues(){
            return $.map(  [ $('#startTimeHFrom').val()  , $('#startTimeMFrom').val(), $('#startTimeHTo').val(), $('#startTimeMTo').val() ] , function(val){
                return parseInt(val) || 0 ;
            }) ;
        }
        
		var isTicketAvailable = false;
		var firstRemove = false;

		window.$ && window.$(".obj:first").ajaxComplete(function() {
            var  _timeLimit = getTimeLimitValues();
			$(this).find("tr").each(function(n, e) {
				if(checkTickets(e, _timeLimit, n )){
					isTicketAvailable = true;
					highLightRow(e);
				}	
			});
			if(firstRemove) {
				firstRemove = false;
				if (isTicketAvailable) {
					if (isAutoQueryEnabled)
						document.getElementById("refreshButton").click();
					onticketAvailable(); //report
				}
				else {
					//wait for the button to become valid
				}
			}
		}).ajaxError(function() {
			if(isAutoQueryEnabled) doQuery();
		});

		//hack into the validQueryButton function to detect query
		var _delayButton = window.delayButton;

		window.delayButton = function() {
			_delayButton();
			if(isAutoQueryEnabled) doQuery();
		}

		//Trigger the button
		var doQuery = function() {
			displayQueryTimes(queryTimes++);
			firstRemove = true;
            __set_autoIncreaseDays();
			document.getElementById(isStudentTicket ? "stu_submitQuery" : "submitQuery").click();
		}

		var $special = $("<input type='text' />")	
		//add by 冯岩 begin 2012-01-18
		var $specialOnly = $("<label style='margin-left:10px;color: blue;'><input type='checkbox'  id='__chkspecialOnly'/>仅显示限定车次<label>");
		var $includeCanOder = $("<label style='margin-right:10px;color: blue;'><input type='checkbox' id='__chkIncludeCanOder'/>显示可预定车次<label>");
		//add by 冯岩 end 2012-01-18
		var checkTickets = function(row, time_limit , row_index ) {

			var hasTicket = false;
			var v1 = $special.val();			
			var removeOther = $("#__chkspecialOnly").attr("checked");
			var includeCanOder = $("#__chkIncludeCanOder").attr("checked");
			if( v1 ) {
				var v2 = $.trim( $(row).find(".base_txtdiv").text() );
				if( v1.indexOf( v2 ) == -1 ) {
					//add by 冯岩 begin 2012-01-18
					if(removeOther)
					{
						if(v2 != "")
						{
							if(includeCanOder)
							{
								//包括其他可以预定的行
								if($(row).find(".yuding_u").size() == 0)
								{
									$(row).remove();
								}
							}
							else
							{
								$(row).remove();
							}
						}
					}
					//add by 冯岩 end 2012-01-18
					return false;
				}
			}

			if( $(row).find("td input.yuding_x[type=button]").length ) {
				return false;
			}
           
            var cells  = $(row).find("td") ;
            if( cells.length < 5 ) {
                return false ;
            }
            var _start_time = $.map(  $(cells[1]).text().replace(/^\D+|\D+$/, '').split(/\D+0?/) , function(val){
               return parseInt(val) || 0 ; 
            }) ;
            
            while( _start_time.length > 2 ) {
                _start_time.shift() ; // remove station name include number 
            }
            if( _start_time[0] < time_limit[0] ||  _start_time[0]  > time_limit[2] ) {
                return false ;
            }
            if( _start_time[0] == time_limit[0] && _start_time[1]  <  time_limit[1] ){
                return false ;
            }
            if( _start_time[0] == time_limit[2] && _start_time[1]  >  time_limit[3] ){
                return false ;
            }
            
			cells.each(function(i, e) {
				if(ticketType[i-1]) {
					var info = $.trim($(e).text());
					if(info != "--" && info != "无") {
						hasTicket = true;
						highLightCell(e);
					}
				}
			});

			return hasTicket;
		}


		var queryTimes = 0; //counter
		var isAutoQueryEnabled = false; //enable flag

		//please DIY:
		var audio = null;

		var onticketAvailable = function() {
			if(window.Audio) {
				if(!audio) {
					audio = new Audio("http://www.w3school.com.cn/i/song.ogg");
					audio.loop = true;
				}
				audio.play();
				notify("可以订票了！", null, true);
			} else {
				notify("可以订票了！");
			}
		}
		var highLightRow = function(row) {
			$(row).css("background-color", "#D1E1F1");
		}
		var highLightCell = function(cell) {
			$(cell).css("background-color", "#2CC03E");
		}
		var displayQueryTimes = function(n) {
			document.getElementById("refreshTimes").innerHTML = n;
		};

		var isStudentTicket = false;

		//Control panel UI
		var ui = $("<div>请先选择好出发地，目的地，和出发时间。&nbsp;&nbsp;&nbsp;</div>")
			.append(
				$("<input id='isStudentTicket' type='checkbox' />").change(function(){
					isStudentTicket = this.checked ;
				})
			)
			.append(
				$("<label for='isStudentTicket'></label>").html("学生票&nbsp;&nbsp;")
			)
            .append(
				$("<input id='autoIncreaseDays' type='text' value='1'  maxLength=2 style='width:18px;' />") 
			)
			.append(
				$("<label for='autoIncreaseDays'></label>").html("天循环&nbsp;&nbsp;")
			)
			.append(
				$("<button style='padding: 5px 10px; background: #2CC03E;border-color: #259A33;border-right-color: #2CC03E;border-bottom-color:#2CC03E;color: white;border-radius: 5px;text-shadow: -1px -1px 0 rgba(0, 0, 0, 0.2);'/>").attr("id", "refreshButton").html("开始刷票").click(function() {
					if(!isAutoQueryEnabled) {
                        __reset_autoIncreaseDays() ;
						isTicketAvailable = false;
						if(audio && !audio.paused) audio.pause();
						isAutoQueryEnabled = true;
						doQuery();
						this.innerHTML="停止刷票";
					}
					else {
                        __unset_autoIncreaseDays();
						isAutoQueryEnabled = false;
						this.innerHTML="开始刷票";
					}
				})
			)
			.append(
				$("<span>").html("&nbsp;&nbsp;尝试次数：").append(
					$("<span/>").attr("id", "refreshTimes").text("0")
				)
			)
			.append( 
				//Custom ticket type
				$("<div>如果只需要刷特定的票种，请在余票信息下面勾选。</div>")
					.append($("<a href='#' style='color: blue;'>只勾选坐票&nbsp;&nbsp;</a>").click(function() {
						$(".hdr tr:eq(2) td").each(function(i,e) {
							var val = this.innerHTML.indexOf("座") != -1;
							var el = $(this).find("input").attr("checked", val);
							el && el[0] && ( ticketType[el[0].ticketTypeId] = val );
						});
						return false;
					}))
					.append($("<a href='#' style='color: blue;'>只勾选卧铺&nbsp;&nbsp;</a>").click(function() {
						$(".hdr tr:eq(2) td").each(function(i,e) {
							var val = this.innerHTML.indexOf("卧") != -1;
							var el = $(this).find("input").attr("checked", val);
							el && el[0] && ( ticketType[el[0].ticketTypeId] = val );
						});
						return false;
					}))
			)
			.append( 
				$("<div>限定出发车次：</div>")
					.append( $special )
					.append( $specialOnly)
					.append( $includeCanOder )
					.append( "不限制不填写，限定多次用逗号分割,例如: G32,G34" )
			);
		var container = $(".cx_title_w:first");
		container.length ?
			ui.insertBefore(container) : ui.appendTo(document.body);
        
        $('<div style="position:relative;top:0px; left:0px; height:0px; width:1px; overflow:visiable; background-color:#ff0;"></div>')
                .append(
                        $('<a id="app_pre_day" style="position:absolute;top:26px; left:2px; width:40px; color:#000;">前一天</a>').click(function() {
                            if( $(this).hasClass("disabled") ) {
                                return false ;
                            }
                            var date = __date_parse( document.getElementById('startdatepicker').value );
                            date.setTime(  date.getTime() - 3600 * 24 * 1000 ) ;
                            document.getElementById('startdatepicker').value    =  __date_format(date)  ;
                            return false;
                        })
                    )
                .append(
                        $('<a id="app_next_day"  style="position:absolute;top:26px; left:114px; width:40px; color:#000;">下一天</a>').click(function() {
                            if( $(this).hasClass("disabled") ) {
                                return false ;
                            }
                            var date = __date_parse( document.getElementById('startdatepicker').value );
                            date.setTime(  date.getTime() + 3600 * 24 * 1000 ) ;
                            document.getElementById('startdatepicker').value    =  __date_format(date)  ;
                            return false;
                        })
                    )
                .insertBefore( $('#startdatepicker') ) ;
  
        setTimeout(function(){
            var box = $('<div style="position:relative;top:2px; left:0px; width:100px; height:18px; line-height:18px;  font-size:12px; padding:0px; overflow:hidden;"></div>') ;
            function makeSelect(id, max_value, default_value){
                var element  = $('<select id="' + id + '" style="margin:-2px 0px 0px -5px;padding:0px;font-size:12px; line-height:100%; "></select>') ;
                for(var i = 0; i <= max_value ; i++) {
                    element.append(
                       $('<option value="' + i + '" style="padding:0px;margin:0px;font-size:12px; line-height:100%;" ' + ( default_value == i ? ' selected="selected" ' : '' ) + '>' + ( i <= 9 ? '0' + i : i ) + '</option>' )
                    )
                }
                box.append(
                    $('<div style="width:18px; padding:0px; overflow:hidden; float:left;"></div>') .append(element)
                );
                return element ;
            }
            function check(evt){
                var tl  = getTimeLimitValues() ;
                if( tl[0] > tl[2] || (tl[0] == tl[2]  && tl[1] > tl[3]) ) {
                    alert('最早发车时间必须早于最晚发车时间，请重新选择！') ;
                    return false ;
                }
            }
            makeSelect('startTimeHFrom' , 23 ).change(check) ;
            box.append( $('<div style="float:left;">:</div>')) ;
            makeSelect('startTimeMFrom' , 59 ).change(check) ;
            box.append( $('<div style="float:left;padding:0px 1px;">--</div>')) ;
            makeSelect('startTimeHTo' , 23, 23 ).change(check) ;
            box.append( $('<div style="float:left;">:</div>')) ;
            makeSelect('startTimeMTo' , 59, 59 ).change(check) ;
            
            box.insertAfter(  $('#startTime') )
   
        }, 10 ) ;
        
		//Ticket type selector & UI
		var ticketType = new Array();
        var checkbox_list   = new Array();
		$(".hdr tr:eq(2) td").each(function(i,e) {
			ticketType.push(false);
			if(i<3) return;
			ticketType[i] = true;

			var c = $("<input/>").attr("type", "checkBox").attr("checked", true);
			c[0].ticketTypeId = i;
			c.change(function() {
				ticketType[this.ticketTypeId] = this.checked;
			}).appendTo(e);
            checkbox_list.push(c);
		});
        $.each([1, 2 ], function(){
            var c   = checkbox_list.pop() ;
            c[0].checked    = false ;
            ticketType[ c[0].ticketTypeId ] = this.checked ;
        });
        delete checkbox_list ;
	}

	route("querySingleAction.do", query);
	route("myOrderAction.do?method=resign", query);
	route("confirmPassengerResignAction.do?method=cancelOrderToQuery", query);

	route("loginAction.do?method=init", function() {
		
		if( !window.location.href.match( /init$/i ) ) {
			return;
		}
		//login
		var url = "https://dynamic.12306.cn/otsweb/loginAction.do?method=login";
		var surl = "https://dynamic.12306.cn/otsweb/loginAction.do?method=loginAysnSuggest";
		var queryurl = "https://dynamic.12306.cn/otsweb/order/querySingleAction.do?method=init";
		//Check had login, redirect to query url
		if( window.parent && window.parent.$ ) {
			var str = window.parent.$("#username_ a").attr("href");
			if( str && str.indexOf("sysuser/user_info") != -1 ){
				window.location.href = queryurl;
				return;
			}
		}

		function submitForm(){
		    var submitUrl = url;
		    //更改登录方式，增加post参数 2013-1-5 by stone 

		    $.ajax(
			  {
			      url: surl,
			      type: "POST",
			      dataType: "json",
			      success: function (data) {
			          if (data.randError != 'Y') {
			              //refreshImg();
			              alert(data.randError);
			             // $("#password").val("");
			              //$("#password").focus();
			             // $("#randCode").val("");
			              $("#loginRand").val(data.loginRand);
			              return false;
			          } else {
			              $("#loginRand").val(data.loginRand);

			              $.ajax({
			                  url: submitUrl,
			                  type: "POST",
			                  data: {
			                       "loginRand": $("#loginRand").val()
                                   , "refundLogin": 'N'
                                   , "refundFlag": 'Y'
			                       , "loginUser.user_name": $("#UserName").val()
                                   , "nameErrorFocus": $("#nameErrorFocus").val()
                                   , "user.password": $("#password").val()
                                   , "passwordErrorFocus": $("#passwordErrorFocus").val()
                                   , "randCode": $("#randCode").val()
                                   , "randErrorFocus": $("#randErrorFocus").val()
			                  },
			                  beforeSend: function (xhr) {
			                      try {
			                          //xhr.setRequestHeader('X-Requested-With', {toString: function(){ return ''; }});
			                          //xhr.setRequestHeader('Cache-Control', 'max-age=0');
			                          //xhr.setRequestHeader('Accept', 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8');
			                      } catch (e) { };
			                  },
			                  timeout: 30000,
			                  //cache: false,
			                  //async: false,
			                  success: function (msg) {
			                      //密码输入错误
			                      //您的用户已经被锁定
			                     // $('#errorhmtl').val(msg);

			                      if (msg.indexOf('请输入正确的验证码') > -1) {
			                          alert('请输入正确的验证码！');
			                      } else if (msg.indexOf('当前访问用户过多') > -1) {
			                          reLogin();
			                      } else if (msg.match(/var\s+isLogin\s*=\s*true/i)) {
			                          notify('登录成功，开始查询车票吧！');
			                          window.location.replace(queryurl);
			                      } else {
			                          msg = msg.match(/var\s+message\s*=\s*"([^"]*)/);
			                          if (msg && msg[1]) {
			                              alert(msg && msg[1]);
			                          } else {
			                              reLogin();
			                          }
			                      }
			                  },
			                  error: function (msg) {

			                      reLogin();
			                  }
			              });
			              
			          }
			      },
			      error: function (XMLHttpRequest, textStatus, errorThrown) {
			          reLogin();
			          //return false;
			      }
			  });

			
		}

		var count = 1;
		function reLogin(){
			count ++;
			$('#refreshButton').html("("+count+")次登录中...");
			setTimeout(submitForm, 2000);
		}
	    //初始化
		//$("#subLink").after("<textarea id='errorhmtl'></textarea>");
		$("#subLink").after($("<a href='#' style='padding: 5px 10px; background: #2CC03E;border-color: #259A33;border-right-color: #2CC03E;border-bottom-color:#2CC03E;color: white;border-radius: 5px;text-shadow: -1px -1px 0 rgba(0, 0, 0, 0.2);'/>").attr("id", "refreshButton").html("自动登录").click(function() {
			count = 1;
			$(this).html("(1)次登录中...");
			//notify('开始尝试登录，请耐心等待！', 4000);
			submitForm();
			return false;
		}));


		alert('如果使用自动登录功能，请输入用户名、密码及验证码后，点击自动登录，系统会尝试登录，直至成功！');
	});
	route("confirmPassengerAction.do", submit);
	route("confirmPassengerResignAction.do", submit);
	function submit() {
		/**
		 * Auto Submit Order
		 * From: https://gist.github.com/1577671
		 * Author: kevintop@gmail.com  
		 */
		//Auto select the first user when not selected
		if( !$("input._checkbox_class:checked").length ) {
			try{
				//Will failed in IE
				$("input._checkbox_class:first").click();
			}catch(e){};
		}
		//passengerTickets

		var userInfoUrl = 'https://dynamic.12306.cn/otsweb/order/myOrderAction.do?method=queryMyOrderNotComplete&leftmenu=Y';

		var count = 1, freq = 1000, doing = false, timer, $msg = $("<div style='padding-left:470px;'></div>");

		OrderQueueWaitTime.prototype.start = function () {
		    var t = this;
		    t.timerJob();
		    window.setInterval(function () { t.timerJob(); }, 1000);
		};

		OrderQueueWaitTime.prototype.timerJob = function () {
		    if (this.isFinished) {
		        return;
		    }
		    //alert(this.dispTime);
		    if (this.dispTime <= 0) {
		        this.isFinished = true;
		        //alert("ok");
		        this.finishMethod(this.tourFlag, this.dispTime, this.waitObj);
		        return;
		    }

		    if (this.dispTime == this.nextRequestTime) {
		        this.getWaitTime();
		    }

		    //格式化时间，把秒转换为时分秒
		    var second = this.dispTime;
		    var show_time = "";
		    //	var hour = parseInt(second / 3600);  //时
		    //	if(hour > 0){
		    //		show_time = hour + "小时";
		    //		second = second % 3600;
		    //	}
		    //	var minute = parseInt(second / 60);  //分
		    //	if(minute >= 1){
		    //		show_time = show_time + minute + "分";
		    //		second = second % 60;
		    //	} else if(hour >= 1 && second > 0){
		    //		show_time = show_time + "0分";
		    //	}
		    //	if(second > 0){
		    //		show_time = show_time + second + "秒";
		    //	}

		    //时间大于等于1分钟的，按分钟倒计时，小于1分钟的按1分钟显示
		    var minute = parseInt(second / 60);
		    if (minute >= 1) {
		        show_time = minute + "分";
		        second = second % 60;
		    } else {
		        //		show_time = second + "秒";
		        show_time = "1分";
		    }

		    this.waitMethod(this.tourFlag, this.dispTime > 1 ? --this.dispTime : 1, show_time);
		};

		OrderQueueWaitTime.prototype.getWaitTime = function () {
		    var t = this;
		    $.ajax(
              {
                  url: 'https://dynamic.12306.cn/otsweb/order/myOrderAction.do?method=getOrderWaitTime',
                  type: "GET",
                  data: { tourFlag: t.tourFlag },
                  dataType: "json",
                  success: function (data) {
                      if (data != null) {
                          t.waitObj = data;
                          t.dispTime = data.waitTime;
                          var flashWaitTime = parseInt(data.waitTime / 1.5);
                          flashWaitTime = flashWaitTime > 60 ? 60 : flashWaitTime;
                          var nextTime = data.waitTime - flashWaitTime;

                          t.nextRequestTime = nextTime <= 0 ? 1 : nextTime;
                          //alert(t.dispTime);
                          //alert(t.nextRequestTime);
                      }
                  },
                  error: function (XMLHttpRequest, textStatus, errorThrown) {
                      //alert("计算排队时间失败！");
                      return false;
                  }
              });
		};

		function waitFunc(tourFlag, return_time, show_time) {
		    if (return_time <= 5) {
		        //dispMessage("您的订单已经提交，系统正在处理中，请稍等。");
		    } else if (return_time > 30 * 60) {
		        //dispMessage("您的订单已经提交，预计等待时间超过30分钟，请耐心等待。");
		    } else {
		        //dispMessage("您的订单已经提交，最新预估等待时间" + show_time + "，请耐心等待。");
		    }

		}

		function procFail(flag, returnObj) {
		    var renewURL = "<a id='link' target='main' style='color:#2C72BA' onclick='closePopWin()' href='"
                    + ctx + "/order/querySingleAction.do?method=init'>[重新购票]</a>";
		    var my12306URL = "<a id='link' target='main' style='color:#2C72BA' onclick='closePopWin()' href='"
                    + ctx + "/loginAction.do?method=initForMy12306'>[我的12306]</a>";
		    if (flag == -1) {
		        reSubmitForm("未知错误");
		    } else if (flag == -2) {
		        if (returnObj.errorcode == 0) {
		            //dispMessage("占座失败，原因:" + returnObj.msg + " 请点击" + my12306URL
		            //    + ",办理其他业务.");
		            reSubmitForm("占座失败:" + returnObj.msg);
		        } else {
		            //  dispMessage("占座失败，原因:" + returnObj.msg + " 请点击" + renewURL
		            //    + ",重新选择其它车次.");
		            reSubmitForm("占座失败:" + returnObj.msg);
		        }
		    } else if (flag == -3) {
		        //dispMessage("订单已撤销 " + " 请点击" + renewURL + ",重新选择其它车次.");
		        reSubmitForm("订单已撤销");
		    } else {
		        // 进入未完成订单页面
		        //parent.closePopWin();
		       // var form = document.getElementById("confirmPassenger");
		        //form.action = "https://dynamic.12306.cn/otsweb/order/myOrderAction.do?method=queryMyOrderNotComplete&leftmenu=Y&fakeParent=true";
		        //form.submit();
		        var audio;
		        if (window.Audio) {
		            audio = new Audio("http://www.w3school.com.cn/i/song.ogg");
		            audio.loop = true;
		            audio.play();
		        }
		        notify("恭喜，车票预订成！", null, true);
		        setTimeout(function () {
		            if (confirm("车票预订成，去付款？")) {
		                //window.location.replace(userInfoUrl);
		                var form = document.getElementById("confirmPassenger");
		                form.action = "https://dynamic.12306.cn/otsweb/order/myOrderAction.do?method=queryMyOrderNotComplete&leftmenu=Y&fakeParent=true";
		                form.submit();
		            } else {
		                if (audio && !audio.paused) audio.pause();
		            }
		        }, 100);
		    }
		}

	    // 跳转-单程
		function finishFun(tourFlag, time, returnObj) {
		    if (time == -1) {
		        var action_url = "";
		        action_url = "https://dynamic.12306.cn/otsweb/order/confirmPassengerAction.do?method=payOrder&orderSequence_no="
                           + returnObj.orderId;
		        var audio;
		        if (window.Audio) {
		            audio = new Audio("http://www.w3school.com.cn/i/song.ogg");
		            audio.loop = true;
		            audio.play();
		        }
		        notify("恭喜，车票预订成！", null, true);
		        setTimeout(function () {
		            if (confirm("车票预订成，去付款？")) {
		                var form = document.getElementById("confirmPassenger");
		                form.action = action_url;
		                form.submit();
		            } else {
		                if (audio && !audio.paused) audio.pause();
		            }
		        }, 100);
		      
		       

		        //parent.closePopWin();
		    } else {
		        procFail(time, returnObj);
		    }
		}

		function erromessageFunc(erromsg) {
		    if (erromsg.indexOf("输入的验证码不正确") > -1) {
		        $("#img_rrand_code").click();
		        $("#rand").focus().select();
		        stop();
		        return;
		    }
		    if (erromsg.indexOf('未处理的订单') > -1) {
		        notify("有未处理的订单!");
		        window.location.replace(userInfoUrl);
		        return;
		    }
		    setTimeout(reSubmitForm, freq || 50, erromsg);
		}

		function submitForm(){
			var timer = null;
			//更改提交列车日期参数
			//var wantDate = $("#startdatepicker").val();
			//$("#start_date").val(wantDate);
		    //$("#_train_date_str").val(wantDate);
			var url = 'https://dynamic.12306.cn/otsweb/order/confirmPassengerAction.do?method=confirmSingleForQueueOrder';

			var checkurl = "https://dynamic.12306.cn/otsweb/order/confirmPassengerAction.do?method=checkOrderInfo&rand=" + $("#rand").val();
			
			var t = jQuery("#confirmPassenger");
			var tmp = $('#confirmPassenger').serialize();
			tmp += "&tFlag=dc";
			jQuery.ajax({
			    url: checkurl,
			    type: "POST",
			    data: tmp,
			    contentType: "application/x-www-form-urlencoded; charset=utf-8",
			    dataType: "json",
			    success: function (data) {
			        if (data.errMsg != 'Y') {
			            erromessageFunc(data.errMsg);
			            //setTimeout(reSubmitForm, freq || 50,data.errMsg);
			        }
			        else {
			         jQuery.ajax(
					  {
					    url: url,
					    type: "POST",
					    data: tmp,
					    contentType: "application/x-www-form-urlencoded; charset=utf-8",
					    dataType: "json",
					    success: function (data) {

					        if (data.errMsg != 'Y') {
					            erromessageFunc(data.errMsg);
					            //reSubmitForm();
					            //setTimeout(reSubmitForm, freq || 50,data.errMsg);
					        } else {
					            timer = new OrderQueueWaitTime('dc',
										waitFunc, finishFun);
					            timer.start();
					        }
					    },
					    error: function (XMLHttpRequest, textStatus,
								errorThrown) {
					        //dispMessage("下单失败！网络忙，请稍后再试。");
					        setTimeout(reSubmitForm, freq || 50, "网络忙");
					        //reSubmitForm("网络忙");
					        //return false;
					      }
					  });
			        }
			    },
			    error: function (XMLHttpRequest, textStatus, errorThrown) {
			        if (errorThrown.concat('登陆')) {
			            alert("您离开页面的时间过长，请重新登录系统。");
			        }
			        else {
			            setTimeout(reSubmitForm, freq || 50,"网络忙");
			        }
			    }
			});

			//jQuery("#confirmPassenger").ajaxSubmit(
			

			/*jQuery.ajax({
				url: $("#confirmPassenger").attr('action'),
				data: $('#confirmPassenger').serialize(),
				beforeSend: function( xhr ) {
					try{
						xhr.setRequestHeader('X-Requested-With', {toString: function(){ return ''; }});
						xhr.setRequestHeader('Cache-Control', 'max-age=0');
						
					}catch(e){};
				},
				type: "POST",
				timeout: 30000,
				success: function( msg )
				{
					//Refresh token
					var match = msg && msg.match(/org\.apache\.struts\.taglib\.html\.TOKEN['"]?\s*value=['"]?([^'">]+)/i);
					var newToken = match && match[1];
					if(newToken) {
						$("input[name='org.apache.struts.taglib.html.TOKEN']").val(newToken);
					}

					if( msg.indexOf('payButton') > -1 ) {
						//Success!
						var audio;
						if( window.Audio ) {
							audio = new Audio("http://www.w3school.com.cn/i/song.ogg");
							audio.loop = true;
							audio.play();
						}
						notify("恭喜，车票预订成！", null, true);
						setTimeout(function() {
							if( confirm("车票预订成，去付款？") ){
								window.location.replace(userInfoUrl);
							} else {
								if(audio && !audio.paused) audio.pause();
							}
						}, 100);
						return;
					}else if(msg.indexOf('未处理的订单') > -1){
						notify("有未处理的订单!");
						window.location.replace(userInfoUrl);
						return;
					}
					var reTryMessage = [
						'用户过多'
					  , '确认客票的状态后再尝试后续操作'
					  ,	'请不要重复提交'
					  , '没有足够的票!'
					  , '车次不开行'
					];
					for (var i = reTryMessage.length - 1; i >= 0; i--) {
						if( msg.indexOf( reTryMessage[i] ) > -1 ) {
							reSubmitForm( reTryMessage[i] );
							return;
						}
					};
					// 铁道部修改验证码规则后  update by 冯岩
					if( msg.indexOf( "输入的验证码不正确" ) > -1 ) {
						$("#img_rrand_code").click();
						$("#rand").focus().select();
						stop();
						return;
					}
					//Parse error message
					msg = msg.match(/var\s+message\s*=\s*"([^"]*)/);
					stop(msg && msg[1] || '出错了。。。。 啥错？ 我也不知道。。。。。');
				},
				error: function(msg){
					reSubmitForm("网络错误");
				}
			});*/
		};
		function reSubmitForm(msg){
			if( !doing )return;
			count ++;
			$msg.html("("+count+")次自动提交中... " + (msg || ""));
		    //timer = setTimeout( submitForm, freq || 50 );
			submitForm();
		}

		function showmsg(msg)
		{
		    $msg.html("(" + count + ")次自动提交中... " + (msg || ""));
		}
		function stop ( msg ) {
			doing = false;
			$msg.html("("+count+")次 已停止");
			$('#refreshButton').html("自动提交订单");
			//timer && clearTimeout( timer );
			msg && alert( msg );
		}
		function reloadSeat(){
			$("select[name$='_seat']").html('<option value="M" selected="">一等座</option><option value="O" selected="">二等座</option><option value="1">硬座</option><option value="3">硬卧</option><option value="4">软卧</option>');
		}
		//初始化

		if($("#refreshButton").size()<1){

			//	//重置后加载所有席别
			//	$("select[name$='_seat']").each(function(){this.blur(function(){
			//		alert(this.attr("id") + "blur");
			//	})});
			////初始化所有席别
			//$(".qr_box :checkbox[name^='checkbox']").each(function(){$(this).click(reloadSeat)});
			//reloadSeat();

			//日期可选
			$("td.bluetext:first").html('<input type="text" name="orderRequest.train_date" value="' +$("#start_date").val()+'" id="startdatepicker" style="width: 150px;" class="input_20txt"  onfocus="WdatePicker({firstDayOfWeek:1})" />');
			$("#start_date").remove();
			 
			$(".tj_btn").append($("<a style='padding: 5px 10px; background: #2CC03E;border-color: #259A33;border-right-color: #2CC03E;border-bottom-color:#2CC03E;color: white;border-radius: 5px;text-shadow: -1px -1px 0 rgba(0, 0, 0, 0.2);'></a>").attr("id", "refreshButton").html("自动提交订单").click(function() {
				//alert('开始自动提交订单，请点确定后耐心等待！');
				if( this.innerHTML.indexOf("自动提交订单") == -1 ){
					//doing
					stop();
				} else {
					if( window.submit_form_check && !window.submit_form_check("confirmPassenger") ) {
						return;
					}
					count = 0;
					doing = true;
					this.innerHTML = "停止自动提交";
					reSubmitForm();
				}
				return false;
			}));
			$(".tj_btn").append("自动提交频率：")
				.append($("<select id='freq'><option value='50' >频繁</option><option value='500' selected='' >正常</option><option value='2000' >缓慢</option></select>").change(function() {
					freq = parseInt( $(this).val() );
				}))
				.append($msg);
			
			
			
			$("#rand").bind('keydown', function (e) {
				var key = e.which;
				if (key == 13) {
					$("#refreshButton").click();
				}
			});
		}
	};
}, true);
