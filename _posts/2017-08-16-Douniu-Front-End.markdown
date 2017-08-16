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
斗牛前端代码经过三次迭代更新，分别是：
### 第一遍迭代
第一遍迭代纯Html页面，不考虑跳转和数据逻辑，文件命名无前缀。第一版本迭代文件已不再使用。以下为第一版本迭代页面极其作用：
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
斗牛前端第二次迭代，该次迭代是为了提升页面的性能，并同时解决socket.io经过页面跳转无法保存的问题，而采用单页面跳转的实现代码，当前版本已经弃用：
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




	



[Materialize]: http://materializecss.com/
[Swig-Template]: http://node-swig.github.io/swig-templates/
