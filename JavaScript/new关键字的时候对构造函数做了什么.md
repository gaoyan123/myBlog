## 一、对话记录渲染
####1. 客服端对话框中，部分动作需要与框架进行互动，以下描述的均为客服端监控页面click事件，通过click事件配合触发元素的特殊属性来判断需要触发的逻辑，具有交互动作的行为描述如下：
1. 文本搜索
    * 触发元素：button[class='textBox-search"]   
    * 搜索内容：此button上的“data-search”属性值
      >客服端判断条件：node.nodeName == "BUTTON" && node.dataset.search

2. 图片预览
    * 触发元素：a[data-role="external"] 或 img[data-role="external"]
    * 图片Url： 此a标签的href属性值 或 img标签的src属性 
      >客服端判断条件：node.nodeName == "A" && node.dataset.role != undefined && node.dataset.role=="external"  
      客服端判断条件：node.nodeName == "IMG" && node.dataset.role != undefined && node.dataset.role=="external"  

3. 视频预览
    * 触发元素：div[data-role="external"] 或 a[data-role="external"]
    * 视频Url： 此a标签的href属性值
      >客服端判断条件：node.nodeName == "DIV" && node.dataset.type != undefined && node.dataset.type=="video && node.dataset.role=="external"  
        客服端判断条件：node.nodeName == "A" && node.parentNode.className.indexOf("fileIcon_6")>0 && node.dataset.role=="external"  

####2. 页面对客服端框架开放的接口挂接在window对象下，具体方法如下：
1. 渲染对话数据到历史框中
   <pre>
   window.messageHandler.render({
    data:[{xxxx}], 
    scene:"realTime", 
    scrollLock: false,
    unreadable: true
   });  
   </pre>
    >data: 数组对象，可以一次多条数据，格式参考mockData.json  
    scrollLock: 添加记录后，是否锁定滚动条，默认为false  
    reload: 切换访客或刷新对话窗口的时候，默认false：true-插入数据并且定位滚动条到最底部；false-按照正常的流程  
    scene: 使用场景，history-历史渲染；realTime-实时渲染  
    unreadable: 判断是否能够计入未读消息, true-可以计入未读消息；false-不计入未读消息默认为true  
    
    >注意：   
     1.除微信渠道外，其他不可获取poster的视频均以文件的方式展示，点击文件图标进行预览
    

2. 重新加载页面
   <pre>window.messageHandler.reload();</pre>

3. 撤销消息
   <pre>window.messageHandler.withdrawMsg(msgId, recallName, recallTime);</pre>  
   >msgId：消息id  
    recallName：撤销此消息的客服名称  
    recallTime：撤销此消息的时间  

4. 清空对话记录：
   <pre>window.messageHandler.emptyRecord();</pre>
   
5. 创建“查看历史记录”按钮, **chatId** 必填
   <pre>window.messageHandler.renderViewHistory(chatId, scrollLock)</pre>
   >chatId：消息id  
    scrollLock：是否锁定滚动条，0：否  1：是
   
6. 历史记录显示完成后，隐藏“查看历史记录”按钮
   <pre>window.messageHandler.historyOver();</pre>
   
7. 改变消息状态
    <pre>
    window.messageHandler.changeMsgStatus({
        "msgId":"000001", 
        "status":2
    });
    </pre>
    >1、文件发送成功、发送中、失败或被限制后提示文字修改  
    2、消息发送失败状态，整合sendFail方法  
    3、重复成功状态，整合resendSuccess方法  
    status参数如下：  
      0-消息失败  
      1-重发成功  
      2-文件消息被限制类型或大小后失败

8. 发送消息失败：通过ID修改当前消息状态，**msgId** 必填
   <pre>window.messageHandler.sendFail(msgId);</pre>
   >此条消息会生成一个失败图标，点击此图标需要进行重发操作  
   失败图标判断：node.nodeName == "SPAN" && node.dataset.role != undefined && node.dataset.role=="resend"

9. 重新发送消息成功，通过ID修改当前消息状态，**msgId** 必填 
   <pre>window.messageHandler.resendSuccess(msgId);</pre>
   >发送成功后，删除失败图标
   
10. ~~渲染通过url地址解析的商品简介信息，发送的商品url地址，以text方式发送，服务器端把url地址放在带有ID的p标签中，解析商品详情后通过此接口展示：~~
   > 此接口方法融合到render方法中，暂无使用
   <pre>
   window.messageHandler.renderGoodsIntro({
       "id": "链接ID",
       "goodsIntro": {
           "pic": "商品图片url",
           "intro": "商品简介",
           "id": "商品ID",
           "category": "商品类型",
           "price": "商品价格"
       } 
   });
   </pre> 
   
11. 字体大小修改 **fontSize** 必填：
   <pre>window.messageHandler.changeFontSize(fontSize)</pre>
 
12. 切换消息外观模式 **facade** 必填
    <pre>window.messageHandler.changeFacade(facade)</pre>
    > 系统默认为气泡模式，参数如下：  
    0 - 文本模式  
    1 - 气泡模式  
    
13. 字体修改:
    <pre>window.messageHandler.changeFont("宋体")</pre>  

14. 图片、语音上传后替换线上地址
    <pre>
    window.messageHandler.replaceMaterial({
        msgId: "消息ID",
        materialId: "素材ID"
        url: "图片地址",
        tm:  "语音含有时长",
        text: "语音含有文本",
        width: "图片实际宽度",
        height："图片实际高度"
    })
    </pre>

15. ~~更新页面场景：6.8中整合到 funControl 方法中~~
    <pre>
    window.messageHandler.searchable(boolean);
    </pre>
    > 传入boolean值
    
16. 功能权限控制，后续都在这里扩展功能：
    <pre>
    window.messageHandler.funControl({
        "searchable": false
    });
    </pre>
    > searchable: 传入boolean值,控制在文本字段上是否显示知识推荐的“搜索”按钮
    
17. 超大图片禁止预览提示
    <pre>
    window.messageHandler.forbidTips();
    </pre>

18. 水印渲染
    <pre>
    window.messageHandler.renderWatermasks({companyId:'multi', userName:'wanggq'});
    </pre>
    
    
####3. 页面url参数
   <pre>http://xxxx/live800/chatClient/refactor/serviceRecords/src/chatRecords.jsp?lang=zh-CN&scene=0&operatorId=0001&ai=1</pre>   
   * scene  
     >页面使用场景：  
     0-访客和客服对话：访客消息在左侧，客服消息在右侧(辅助客服颜色区分)；显示文本搜索；  
     1-对话评估：访客消息在左侧，客服消息在右侧(辅助客服颜色区分)；不显示文本搜索  
     2-客服对话：其他客服消息在左侧，本客服消息在右侧 &&　对话界面宽度大于850px时，所有消息都展示在左侧；当对话界面宽度小于850px时，其他客服消息展示在左侧，客服本人消息展示在右侧；不显示文本搜索    
   * lang    
     >语言：zh-CN、en、zh-TW、jp、kr..  
   * operatorId  
     >当前客服ID，主要用于判断当前客服和其他客服  
   * ai
     >是否开启智能助理功能，开启为1；未开启为0；
  
***

## 二、图片、视频预览

1.  图片预览，支持多图片预览，接口使用如下：
    <pre>
    previewHandler.previewGallery({
        "imgArr": [
             {
                 "src": "url",  //图片地址
                 "nw": 8192,    //图片真实宽度
                 "nh": 3072     //图片真实高度
             }
        ],
        "activeIndex": 0 //多图片预览时，默认首图索引
    });
    </pre> 

2.  视频预览，单文件预览，接口如下：
    <pre>
    previewHandler.previewVideo("视频url地址");
    </pre>

3.  关闭预览窗口：
    * 触发元素：button
    * 元素属性：data-role="closeWindow"；
      >客服端判断条件：node.nodeName == "BUTTON" && node.dataset.role != undefined && node.dataset.role=="closeWindow"

4.  重置窗口状态：
    <pre>
    previewHandler.resetLoading();
    </pre>
    > 关闭视频播放 && 隐藏图片预览 && 隐藏视频播放器 && 显示loading

***

## 三、开发和生产资源加载路径:
1. 开发环境加载路径：  
    * 客服对话url：
      > http://对话服务器ip或域名:对话服务器端口/对话服务器应用名/chatClient/refactor/serviceRecords/src/chatRecords.jsp?lang=zh-CN&scene=0&operatorId=0001&ai=1
    * 图片和视频预览url：
      > http://对话服务器ip或域名:对话服务器端口/对话服务器应用名/chatClient/refactor/serviceRecords/src/preview.jsp?lang=zh-CN&ai=1

2. 生产环境加载路径：  
    * 客服对话url：
      > http://对话服务器ip或域名:对话服务器端口/对话服务器应用名/chatClient/refactor/serviceRecords/dist/chatRecords.html?lang=zh-CN&scene=0&operatorId=0001&ai=1
    * 图片和视频预览url：
      > http://对话服务器ip或域名:对话服务器端口/对话服务器应用名/chatClient/refactor/serviceRecords/dist/preview.html?lang=zh-CN&ai=1



