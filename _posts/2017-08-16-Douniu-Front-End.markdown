---
layout: single
title:  "Bullup Front End Code"
date:   2017-08-16 09:08:55 +0800
#categories: iOS

excerpt: "斗牛电竞前端代码说明(Using Materializecss framework and Swig Template)"
header:
  teaser: /assets/images/codeback.jpg
  overlay_image: /assets/images/codeback.jpg
  overlay_filter: 0.4 # same as adding an opacity of 0.5 to a black background
  
---
{% include toc title="Contents" %}

## 概要说明
**<span style = "display:block;
	float:left;
	font-family:Georgia;
	font-size: 310%;
	font-weight: bold;
	line-height: 90%;
	margin-right: 6px;
	margin-bottom:-2px;
	margin-top: 7px;">斗</span>**牛电竞前端的实现，样式采用的是[Materializecss的框架][Materialize]，页面布局通过该框架的Grid布局预定义类来完成，页面元素主要使用到其中的以下一些内容，其中从第7个开始，是需要使用js进行初始化的一些页面元素。
- Buttons
- Cards
- Chips
- Collections
- Navbar
- Pagination
- Modal
- Carousel
- Collapsible
- Dropdown
- Sidenav
- Tabs

在数据注入方面，采用的是[Swig Template][Swig-Template]模板，其基本原理是在页面嵌入控制语句，根据后端所获取到的数据，来进行页面内容的渲染。

## 项目代码的运行
### 安装Node.js
参考[Node.js Official Website][NODEJS]
### 下载Node Webkit
参考[Node Webkit Official Website][NWJS]
### 项目下载和运行
#### 安装依赖
因为页面使用到了node的一些包，所以需要在ForK和Clone下[Bullup斗牛仓库][Bullup]后，使用
```
node install
```
先安装``package.json``中的依赖包。之后参考[Node Webkit Official Website][NWJS]文档中的运行方法进行运行。
#### 运行
在命令行中：
```
cd /path/to/your/app
/path/to/nw .
```
其中``/path/to/nw``是下载的nwjs的二进制运行文件，在Windows系统下，对应的是``nw.exe``；在Linux系统下，为``nw``；在MacOS下，为``nwjs.app/Contents/MacOS/nwjs``.

在windows系统下，可以通过直接拖拽``SPAFront``文件夹，到``nw.exe``程序上，来运行前端界面。


## 文件结构
```
SPAFront/
|   AJAX_SPA.org
|   battlepage.html
|   chatbox.html
|   chatroom.html
|   dataquery.html
|   DouNiu.html
|   dropdown_menu.html
|   filetree.txt
|   home.html
|   index.html
|   mhome.html
|   package-lock.json
|   package.json
|   personalInfo.html
|   rank.html
|   readme.md
|   searchusr.html
|   spa_battle.html
|   spa_battleres.html
|   spa_chat.html
|   spa_dataquery.html
|   spa_fight.html
|   spa_home.html
|   spa_myroom.html
|   spa_personal.html
|   spa_queryres.html
|   spa_rank.html
|   swig_battle.html
|   swig_battleres.html
|   swig_dataquery.html
|   swig_fight.html
|   swig_friend_list.html
|   swig_friend_list_head.html
|   swig_home.html
|   swig_matches.html
|   swig_match_detail.html
|   swig_menu.html
|   swig_myroom_frame.html
|   swig_myroom_info.html
|   swig_myroon_friends.html
|   swig_pagination.html
|   swig_personal_basic.html
|   swig_queryres.html
|   swig_rank.html
|   swig_starter.html
|   swig_team_detail.html
|   swig_test.html
|   swig_tournament.html
|   userinfo.html
|           
+---css
|       battle.css
|       battleres.css
|       boxstyle.css
|       form.css
|       friendbox.css
|       history.html
|       main.css
|       materialize.min.css
|       mhome.css
|       modal.css
|       myroom.css
|       normalize.css
|       personalInfo.css
|       queryres.css
|       rank.css
|       searchbox.css
|       searchusr.css
|       set1.css
|       spa_fight.css
|       starter.css
|       tournament.css
|       validation_pop.css
|       
+---d3
|       
+---flipclock
|       flipclock.css
|       flipclock.js
|       
+---font-awesome-4.7.0
|           
+---fonts
|           
+---js
|       carousel.js
|       change_page.js
|       Chart.bundle.js
|       classie.js
|       close_modal.js
|       create_room.js
|       draw_chats.js
|       draw_radar.js
|       emoticon.js
|       find_team.js
|       game_history_query.js
|       genRadar.js
|       global.js
|       home.js
|       HudsonJoe@HudsonJoe-PC.local.1280
|       initial_pagination.js
|       jkanban.min.css
|       jkanban.min.js
|       jquery-2.2.4.js
|       jquery.validate.min.js
|       login.js
|       lolcfg.js
|       lolutil.js
|       main.js
|       materialize-pagination.js
|       materialize-pagination.min.js
|       materialize.min.js
|       modal.js
|       query_res.js
|       request_rank_list.js
|       router.js
|       socket_util.js
|       spa.js
|       starter_router.js
|       swig.js
|       testData.js
|       validate_log.js
|       validate_roominfo.js
|       validate_sign.js
|
+---media
|   +---imgs
|   |       
|   +---item_imgs
|   |       
|   +---lol_champions
|   |       
|   \---user_icon
|           1.png
|           2.png
|           3.png
|           
\---node_modules
```

## Html界面详细说明
斗牛前端代码经过三次迭代更新，分别是：
### 第一遍迭代
第一遍迭代纯Html页面，不考虑跳转和数据逻辑，文件命名无前缀。第一版本迭代文件已不再使用。以下为第一版本迭代页面及其作用：
- ``battlepage.html`` 战队列表
- ``chatbox.html`` 旧版竖排聊天和好友列表
- ``chatroom.html`` 横版聊天和好友列表
- ``dataquery.html`` 旧版个人主页
- ``DouNiu.html`` 第一次迭代最终主页
- ``dropdown_menu.html`` 导航栏右侧下拉菜单
- ``home.html`` 旧版主页
- ``index.html`` 旧版战队列表
- ``mhome.html`` 旧版主页
- ``personalInfo.html`` 个人主页（带雷达图）
- ``rank.html`` 排行榜
- ``searchusr.html`` 搜索用户页面

### 第二遍迭代
斗牛前端第二次迭代，该次迭代是为了提升页面的性能，并同时解决socket-io经过页面跳转无法保存的问题，而采用单页面跳转的实现代码，当前版本已经弃用：
- ``spa_battle.html`` 单页面战队列表 
- ``spa_battleres.html`` 对战结果页面
- ``spa_chat.html`` 聊天室
- ``spa_dataquery.html`` 数据查询
- ``spa_fight.html`` 战斗准备页面
- ``spa_home.html`` 单页面主页
- ``spa_myroom.html`` 约战个人战队页面
- ``spa_personal.html`` 个人中心
- ``spa_queryres.html``战绩查询结果
- ``spa_rank.html`` 排行榜

### 第三遍迭代
第三遍迭代是目前前端正在使用的页面版本，其结构为：``主页面框架``<--``swig模板``<--``swig子模板（如果有）``
- ``swig_battle.html`` 战队列表（需要修改，从导航栏去除，加入主页约战和匹配逻辑）
- ``swig_battleres.html`` 战斗结果页面
- ``swig_dataquery.html`` 数据查询入口页面
- ``swig_fight.html`` 战斗准备页面
- ``swig_friend_list.html`` 战队全部好友列表
- ``swig_friend_list_head.html`` 战队界面好友列表头部信息
- ``swig_home.html`` swig模板主界面框架
- ``swig_matches.html`` 战绩查询结果界面左边栏比赛记录
- ``swig_match_detail.html`` 战绩查询结果界面右边栏比赛详情
- ``swig_menu.html`` 导航栏下拉菜单
- ``swig_myroom_frame.html`` 我的战队房间框架
- ``swig_myroom_info.html`` 我的战队房间
- ``swig_myroon_friends.html`` 我的战队已经邀请好友列表
- ``swig_pagination.html`` swig模板通用分页控件
- ``swig_personal_basic.html`` 个人中心基本信息
- ``swig_queryres.html`` 战绩查询结果页面
- ``swig_rank.html`` 排行榜
- ``swig_starter.html`` 主页初始页面
- ``swig_team_detail.html`` 战队详细信息Modal基本信息模板
- ``swig_test.html`` 测试用（删除）
- ``swig_tournament.html`` 赛事界面

## CSS样式文件详细说明
- ``battle.css`` 战队详情页面样式
- ``battleres.css`` 战斗结果页面样式
- ``boxstyle.css`` 聊天框样式
- ``form.css`` 自建表单样式（已弃用）
- ``friendbox.css`` 聊天室好友列表样式
- ``main.css`` 旧版页面使用的主框架
- ``materialize.min.css`` materializecss样式文件
- ``mhome.css`` 当前使用的主框架样式
- ``modal.css`` 旧版已经弃用的弹出框样式
- ``myroom.css`` 我的战队主页
- ``normalize.css`` (I don't know what is this either...)
- ``personalInfo.css`` 个人主页样式
- ``queryres.css`` 数据查询结果样式
- ``rank.css`` 排行榜样式
- ``searchbox.css`` 聊天室好友列表搜索框样式
- ``searchusr.css`` 搜索用户搜索框样式
- ``set1.css`` 网上找来的搜索框样式（未使用）
- ``spa_fight.css`` 战斗计时界面样式
- ``starter.css`` 主页内容样式
- ``tournament.css`` 赛事主页样式
- ``validation_pop.css`` 表单验证弹出框样式（新版本中尚未添加使用）


## JS文件详细说明
- ``change_page.js`` 
- ``Chart.bundle.js`` 绘图工具类
- ``classie.js``
- ``close_modal.js`` 解决materializecss框架modal弹出以后overlay无法自行关闭的文件，当有新的modal出现类似问题时，参考此文件中的解决办法
- ``create_room.js`` 创建房时，获取当前时间的逻辑
- ``draw_chats.js`` 画图实验类
- ``draw_radar.js`` 绘制雷达图封装类
- ``emoticon.js`` 预留文件，填入聊天框表情按钮载入和选择表情逻辑
- ``find_team.js`` 预留文件，填入查找战队并发送约战请求逻辑
- ``game_history_query.js`` 战绩查询页面搜索框点击搜索逻辑
- ``genRadar.js`` 根据传入数据，向目标画板中绘制雷达图
- ``global.js`` 全局文件，用于页面元素的初始化
- ``home.js`` 看板(弃用)
- ``initial_pagination.js`` 预留方法：向页面中载入页码模板内容（参数需要修改）
- ``jkanban.min.css`` 看板样式（删除）
- ``jkanban.min.js`` 看板动态效果（删除）
- ``jquery-2.2.4.js`` 
- ``jquery.validate.min.js`` 表单验证包
- ``login.js`` 登录逻辑
- ``lolcfg.js`` lol英雄代码和代码对应名称
- ``lolutil.js`` lolapi通用类
- ``main.js`` （删除）
- ``materialize-pagination.js`` 第三方页码类（先前使用时有样式BUG，原因不明，遂暂时弃用)
- ``materialize-pagination.min.js``
- ``materialize.min.js`` 
- ``modal.js`` 各类弹出框打开关闭逻辑（打开新的弹出框时关闭其它弹出框）
- ``query_res.js`` 预留文件：可删除，查询结果逻辑已经在``game_history_query.js``中实现
- ``request_rank_list.js`` 读取排行榜逻辑
- ``router.js`` 主页面框架路由
- ``socket_util.js`` socket通用类
- ``spa.js`` （弃用）页面第二版迭代使用的单页面跳转类
- ``starter_router.js`` 主页面内容路由预留文件
- ``swig.js`` 模板通用类
- ``testData.js`` 渲染模板页面测试数据存放处
- ``validate_log.js`` （需要更新）第二版本网页 登录表单验证类
- ``validate_roominfo.js`` （需要更新）第二版本网页 队伍信息表单验证类
- ``validate_sign.js`` （需要更新）第二版本网页 注册表单验证类

### 其他补充说明
在项目开始，原有<span style = "color:#f0f0f0; background-color:#ff5555; padding:0.06em 0.5em 0.06em; border-radius:6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5); margin-left: 2px; margin-right: 2px;">
房间</span>的概念，再后来的逻辑梳理中，将该概念更改成为了<span style = "color:#f0f0f0; background-color:#ff5555; padding:0.06em 0.5em 0.06em; border-radius:6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5); margin-left: 2px; margin-right: 2px;">战队</span>，故在文件名中，``room``实际指代与战队有关的内容。

因为目前代码未经过系统整理，所以控件对应的js逻辑可能散落在各处，建议安装[ag(silver-search)][AG]（不支持windows，但可以使用[msys2][MSYS2]在window模拟Linux命令终端）或者其他拥有类似搜索功能的编辑器([Atom][ATOM], [VSCode][VSCODE], [Brackets][BRACKETS]等)，配合git来进行项目内的全局搜索，来定位页面上某个id或者class的元素的所有被使用的位置。

## 前端Swig模板结构说明
<span style = "color:#f0f0f0; background-color:#f50057; padding:0.06em 0.5em 0.06em; border-radius:6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5); margin-left: 2px; margin-right: 2px;">数据</span> ------>
<span style = "color:#f0f0f0; background-color:#9575cd; padding:0.06em 0.5em 0.06em; border-radius:6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5); margin-left: 2px; margin-right: 2px;">Swig模板文件</span> ------>
<span style = "color:#f0f0f0; background-color:#ff9800; padding:0.06em 0.5em 0.06em; border-radius:6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5); margin-left: 2px; margin-right: 2px;">最终页面</span>




	


[NODEJS]: https://nodejs.org/en/
[Materialize]: http://materializecss.com/
[Swig-Template]: http://node-swig.github.io/swig-templates/
[AG]: https://github.com/ggreer/the_silver_searcher
[ATOM]: https://atom.io
[VSCODE]: https://code.visualstudio.com
[BRACKETS]: http://brackets.io
[MSYS2]: http://www.msys2.org
[Bullup]: https://github.com/guojingming/bullup5v5
[NWJS]: https://nwjs.io/
