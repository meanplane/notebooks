```javascript
<input type="hidden"  id="JSHOP_CHANNEL_FLAG" value="jd"/>
<input type="hidden" value="82494" id="pageInstance_appId"/>
<input type="hidden" value="2551811" id="pageInstance_id"/>
<input type="hidden" id="vender_id" value="29849" />
<input type="hidden" id="shop_id" value="28985" />
<input type="hidden" id="use3DShop" value="" />
<input type="hidden" id="url3d" value="" />
<input type="hidden" id="hkFlag" value="false" />
<input type="hidden" id="mallType" value=" 1 " />
<input type="hidden" id="mainCategoryId" value="11729">
<input type="hidden" id="isFuseShop" value="false">
<input type="hidden" name="" id="J_ApplicationType" value="2"/>
<input type="hidden" id="pinpai_brandId" value="0"/>
<input type="hidden" id="tb_id" value="0"/>
  eeeee
  eeee
  // 
  (function(){
        $('.button01').click(function(){
            var key = jQuery.trim($('#key01').val());
            var url = "//mall.jd.com/view_search-" + 82494 + '-' + 29849 + '-' + 28985 + '-0-0-0-0-1-1-60.html';
            var key = encodeURIComponent(encodeURIComponent(key));

            if(key!='') {
                url += '?keyword=' + key;
            }
            location.href = url;
        });
    })();
```

