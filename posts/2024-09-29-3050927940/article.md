---
title: "R12: AP: How to Generate the Payment Register in Excel Format? (Doc ID 3050384.1)"
date: "2024-09-29 19:19"
pixnet_id: "3050927940"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050927940"
status: published
views: 63
category_personal: "Oracle Support Doc"
category_primary: "職場甘苦"
category_secondary: "不設分類"
vocus_room: "Oracle ebs"
vocus_map_category: "觀念與文件"
ebs_version: "12.2.5 and later"
suggested_tags:
  - Oracle
  - EBS
  - Doc ID 3050384.1
  - R12
  - AP
  - Payables
vocus_draft: "https://vocus.cc/new-editor/6aa969e5fd897800017e52f7"
image_count: 0
language: zh-Hant
source: pixnet
---

# R12: AP: How to Generate the Payment Register in Excel Format? (Doc ID 3050384.1)

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050927940
> - 發布日期：2024-09-29 19:19
> - 瀏覽人數：63
> - 分類：觀念與文件（原：Oracle Support Doc／職場甘苦）
> - Oracle EBS 版本：12.2.5 and later

---

Applies to:

Oracle Payables - Version 12.2.5 and later
Information in this document applies to any platform.

Goal

How to generate the Payment Register report output as an Excel file with proper headings?

Solution

To do this will require the use of BI Publisher and the creation of a custom template.

The task of creating the template is considered a customization and is up to the user to complete.  I would suggest using and installing desktop publisher for this step.

If using desktop publisher be sure to the Backward Compatibility in the options before creating the template.

From the "System Administrator" responsibility navigate to Concurrent -> Program -> Define

Pull up the report with the short name of APXMTDCR

Change the output type to XML

Save

From a Payables Responsibility navigate to Others -> Request Run

Submit the "Payment Register" report

Once complete View the output, it will be XML.  Save this for the template creation in a later step.

From the XML Publisher Administrator responsibility navigate to Data Definitions

Create a data definition with a code of APXMTDCR (it must use this code, the name can be anything)

From the XML Publisher Administrator responsibility navigate to Templates

Create a custom template with the code of APXMTDCR (it must use this code, the name can be anything)

	Use the output from step 7 for the proper tags to use in the template.

	Set the default output type to Excel

From a Payables Responsibility navigate to Others -> Request Run

Submit the "Payment Register" report

View the output, it should create the output specified in the custom template created in step 11
