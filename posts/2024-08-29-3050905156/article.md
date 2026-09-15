---
title: "HR Full Name的呈現"
date: "2024-08-29 01:39"
pixnet_id: "3050905156"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050905156"
status: published
views: 75
category_personal: "HR:Human Resource"
category_primary: "職場甘苦"
category_secondary: "不設分類"
vocus_room: "Oracle ebs"
vocus_map_category: "人資"
ebs_version: "未註明"
suggested_tags:
  - Oracle
  - EBS
  - HR
vocus_draft: "https://vocus.cc/new-editor/6aa969e7fd897800017e534d"
image_count: 5
language: zh-Hant
source: pixnet
---

# HR Full Name的呈現

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050905156
> - 發布日期：2024-08-29 01:39
> - 瀏覽人數：75
> - 分類：人資（原：HR:Human Resource／職場甘苦）
> - Oracle EBS 版本：未註明

---

今天遇到一個問題，進行了一些研究把它紀錄在這跟大家分享...

我有兩個ERP系統

一個環境是12.2.8 , 一個是12.1.3 然我目前想要將兩個ERP的Buyer資訊統一

但是兩個系統的Full_name 呈現是不一樣的format，對我造成資料分析的困擾

於是我想知道設定在哪?

提供未來有需要的人一個方向

1) 首先有兩個Profiles 先看一下設什麼

(N) System Administrator > Profiles

HR: Default Full Name Format

HR: Local or Global Name Format

2) 查看Local format的設定

(R) China HRMS Manager

(N) Other Definitions >Person Name Formats

原來這裡有設定，限定了他了
