---
title: "R12: AP: ECC: How to Grant Privileges to Access Payables Command Center Dashboards? (Doc ID 2959411.1)"
date: "2024-09-29 19:46"
pixnet_id: "3050928008"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050928008"
status: published
views: 83
category_personal: "Oracle Support Doc"
category_primary: "進修深造"
category_secondary: ""
vocus_room: "Oracle ebs"
vocus_map_category: "觀念與文件"
ebs_version: "12.2.12 and later / Release 12.2"
suggested_tags:
  - Oracle EBS
  - R12.2
  - Payables
  - AP
  - ECC
  - Enterprise Command Center
  - SYSADMIN
  - Doc ID 2959411.1
language: zh-Hant
source: pixnet
---

# R12: AP: ECC: How to Grant Privileges to Access Payables Command Center Dashboards? (Doc ID 2959411.1)

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050928008
> - 發布日期：2024-09-29 19:46
> - 瀏覽人數：83
> - 分類：觀念與文件（原：Oracle Support Doc／進修深造）
> - Oracle EBS 版本：12.2.12 and later／Release 12.2

---

APPLIES TO:

Oracle Payables - Version 12.2.12 and later
Oracle Enterprise Command Center Framework - Version 12.2.12 to 12.2.12 [Release 12.2]
Information in this document applies to any platform.

GOAL

How to Grant Privileges to access Enterprise Command Center (ECC)?

SOLUTION

User will need to log into Instance using SYSADMIN User.

Navigation:

1. User Management Responsibility -- > Roles & Role Inheritance Page

2. Query Code UMX%AP%ECC%

3. Go

4. Choose "Quick Select"

5. Expand and confirm "Add Node" is active

6. Payables Command Center Access Role should have Node added.

7. Once done, clear the cache and re test.
