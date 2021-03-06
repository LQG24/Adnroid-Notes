#APP优化之分析

###背景
用户反馈最近发布的版本非常卡,之前的几个版本不会出现类似问题.具体表现为:
 * 进入比分详情首页卡顿
 * 比分详情首页面加载时间过长
 * 比分详情的10个页面首次加载卡顿:①页面切换动画不流畅 ②小球加载卡顿
 * 比分详情的10个页面之间相互切换卡顿:①一级菜单分析切换到赔率卡顿 ②二级菜单赔率界面切换卡顿
 * 比分详情首页面"基本面"滑动卡顿:①上滑到顶部不流畅 ②下滑道低端不流畅
 * 部分机型偶尔处发现黑屏问题
 * 部分机型出现无响应,卡界面问题
 * APP使用时间久了卡顿


###问题分析
卡顿主要原因是UI绘制卡顿,内存过大卡顿,本次优化主要是针对UI绘制优化

###解决方法
问题|之前|改版
---|---|---
进入比分详情首页卡顿|直接初始化框架,并十几个界面和数据|只初始化框架与第一个界面
比分详情首页面加载时间过长|直接请求比分详情头上,视频以及基本面全部数据,并绘制整个基本面UI|优先请求头部与基本面数据,并分步绘制基本面UI
比分详情的10个页面首次加载卡顿|采用懒加载方式,只有用户点击了每个页面才会加载数据,导致动画不流畅,卡顿|优先请求基本面数据,基本面数据,UI绘制完毕,再加载后面界面
比分详情的10个页面之间相互切换卡顿|界面布局过于复杂,层级过深|重点对每个界面进行重新搭建,用RelativeLayout替换耗性能的LinearLayout,优化赔率界面的布局
比分详情首页面"基本面"滑动卡顿|框架不兼容部分机型导致卡顿|重新使用谷歌官方新框架搭建比分详情,兼容大部分机型
部分机型偶尔处发现黑屏问题|框架不兼容部分机型导致卡顿|重新使用谷歌官方新框架搭建比分详情,兼容大部分机型
部分机型出现无响应,卡界面问题|框架不兼容部分机型导致卡顿|重新使用谷歌官方新框架搭建比分详情,兼容大部分机型
APP使用时间久了卡顿|内存泄露引发卡顿|后续版本针对内存进行优化

###量化
![Image](/Android/MemoryAnalyzer eg/001.png)
![Image](/Android/MemoryAnalyzer eg/002.png)

其中线程Thread 1为主线程,UI绘制必须在主线程中绘制,主线程占用卡则用户体验卡

两张图片对比可知,延迟加载后面的界面,数据,进入比分详情页面占用内存从360W字节降低到了177W字节,降低了50%

其中非常消耗资源的赔率页面UI绘制不存在了,具体看下图
![Image](/Android/MemoryAnalyzer eg/004.png)
![Image](/Android/MemoryAnalyzer eg/005.png)



###总结
本次优化主要是针对界面UI的优化

下一步将针对内存的使用,分配进行优化
