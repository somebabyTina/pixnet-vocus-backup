---
title: "{Web ADI} How to create java component LOV ?"
date: "2022-04-28 19:29"
pixnet_id: "3050023116"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050023116"
status: published
views: 3
category_personal: "Web ADI"
category_primary: "職場甘苦"
category_secondary: "進修深造"
vocus_room: "Oracle ebs"
vocus_map_category: "Forms／報表／開發"
ebs_version: "未註明"
suggested_tags:
  - Oracle
  - EBS
  - AP
  - Web ADI
vocus_draft: "https://vocus.cc/new-editor/6aa969e8fd897800017e5370"
image_count: 2
language: zh-Hant
source: pixnet
---

# {Web ADI} How to create java component LOV ?

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050023116
> - 發布日期：2022-04-28 19:29
> - 瀏覽人數：3
> - 分類：Forms／報表／開發（原：Web ADI／職場甘苦）
> - Oracle EBS 版本：未註明

---

Oracle Version : 12.1.3

首先，我們會需要設定Java component，一般來說是想使用相依性的下拉式選單，版主這邊使用的情境需求是

User 在Excel template上選擇了AP vendor 供應商後，我們要帶出該供應商在該OU下的Active vendor site

[Interface Column] 定義如下

所以使用者會在Excel input 欄位是P_VENDOR_ID, 我期望帶出的選單呈現在 P_VENDOR_SITE_ID 欄位上

1) 找出你的Java Web ADI component要放置的路徑

1.1 Connect to your Oracle AP OS.

1.2 Run below commands

cd $ JAVA_TOP : 找出你的Java root path

pwd : show 出完整路徑

/ora/test6/apps/apps_st/comn/java/classes 這個就是Java root path

如果你是第一次要build java component，請先確認你未來要將java class 放在哪個路徑。

因為我的web ADI是放在客製的module "xx",所以我的java class路徑會放在 $JAVA_TOP/oracle/apps/xx/webadi

(完整路徑如: /ora/test6/apps/apps_st/comn/java/classes/oracle/apps/xx/webadi)

3. Java LOV component需要有三個classes (Component,sql,validators)

http://pastie.org/p/3p0eOlFbZHqXhH8eDgjK0R
