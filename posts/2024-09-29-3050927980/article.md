---
title: "R12: How to Disable The Consolidation Defination? (Doc ID 3048400.1)"
date: "2024-09-29 19:33"
pixnet_id: "3050927980"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050927980"
status: published
views: 52
category_personal: "Oracle Support Doc"
category_primary: "進修深造"
category_secondary: "不設分類"
vocus_room: "Oracle ebs"
vocus_map_category: "觀念與文件"
ebs_version: "12.2 and later"
suggested_tags:
  - Oracle
  - EBS
  - Doc ID 3048400.1
  - R12
vocus_draft: "https://vocus.cc/new-editor/6aa96970fd897800017e38a7"
image_count: 0
language: zh-Hant
source: pixnet
---

# R12: How to Disable The Consolidation Defination? (Doc ID 3048400.1)

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050927980
> - 發布日期：2024-09-29 19:33
> - 瀏覽人數：52
> - 分類：觀念與文件（原：Oracle Support Doc／進修深造）
> - Oracle EBS 版本：12.2 and later

---

Applies to:

Oracle General Ledger - Version 12.2 and later
Information in this document applies to any platform.

Goal

How to disable the consolidation definition?

Solution

There is no option to disable the Consolidation definition.

As an workaround we can perform the following steps:

Login to General Ledger responsibility.

Navigate to Consolidation > Define > Consolidation

Consolidation definition form have 'Enable Security'. Users can use this feature to give View Only access to one of the Consolidation definition at the responsibility level. By enabling this feature we can restrict the user from running consolidation program.

In addition to this users can also add a prefix to consolidation definition which you do not want to use.

References

NOTE:2054823.1 - How to Hide the AutoPost Option in the GLXCORUN Transfer Consolidation Data Form?
NOTE:1089532.1 - Assign Access Button is Invisible in the Consolidation Definition Form
NOTE:415901.1 - How to Properly Assign/Remove Definition Access Sets in Oracle General Ledger R12
NOTE:2187030.1 - How to Set Multiple Privileges for Definition Access Sets
NOTE:735944.1 - R12: Definition Access Set Restricts All Responsibilities
NOTE:1951967.1 - How to Make Function Read Only for Chart of Accounts Mappings - Segment Rules (GLXCOMAP)
