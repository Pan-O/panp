+++
title = 'Bubble起始页拓展'
date = 2024-05-02T10:00:00+08:00
toc = true
tags = ["拓展", "项目"]
+++

## Bubble 起始页
⭐ Bubble起始页是一个简约且美观的新选项卡扩展，可与多功能框无缝交互

🔑支持自定义多个搜索引擎，并通过key快捷搜索

📌添加omnibox关键词，可以实现与起始页相同的搜索体验

🎈优雅的深色模式，以及页面动画

🧩可选的天气卡片，一言卡片

## 如何使用
在omnibox和主页的搜索框里，可以进行搜索。
### 使用主页搜索框
默认情况下直接输入内容即可进行搜索

如果要切换搜索引擎，需要先输入`:key`进行切换，这里的key是设置里每个搜索引擎自定义的值，例如使用google搜索则输入`:gg`然后空格，系统将自动识别并切换到google搜索

若输入的key有误或不存在，搜索引擎将不会发生改变，仍使用默认的搜索引擎
### 使用omnibox
和主页搜索框的使用基本相同，但需要提前输入一个`:`，再按`Tab`键让拓展接管omnibox，然后即可进行搜索
## 天气卡片
使用天气卡片需要用户填写自己的API，天气服务使用和风天气，因此用户需要自己注册账号，申请API

具体流程如下：
1. 进入[控制台 | 和风天气 (qweather.com)](https://console.qweather.com/)，登录账号（若没有账号需注册）
2. 进入控制台后，进入项目管理页面，点击创建项目3. 填写表单，项目名称随意填写，订阅选择*免费订阅* ，Key选择Web API，这里Key的名称也随意填写
4. 项目创建完成后回到项目管理页面，查看key并填写到设置里即可

## 自定义搜索引擎
在设置里可以添加自己定义的搜索引擎

添加搜索引擎需要4个值

| 选项     | 备注                         |
| -------- | --------------------------------------- |
| Name     | 搜索引擎的名称，将显示在页面里                         |
| Icon（可选） | 搜索引擎的图标，将显示在页面里（若没有填写则会自动获取）            |
| Url      | 自定义搜索引擎的Url，以`https://`开头，关键词参数名及其连接符结尾 |
| Key      | 设置该搜索引擎的key，用于切换搜索引擎                    |
