---
title: "Lockbox via API: Sample Code to Call Lockbox Process (ARLPLB) using FND_REQUEST API (Doc ID 1342139.1)"
date: "2024-09-29 19:13"
pixnet_id: "3050927928"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050927928"
status: published
views: 80
category_personal: "Oracle Support Doc"
category_primary: "進修深造"
category_secondary: "不設分類"
vocus_room: "Oracle ebs"
vocus_map_category: "觀念與文件"
ebs_version: "12 and later"
suggested_tags:
  - Oracle
  - EBS
  - Doc ID 1342139.1
  - Receivables
vocus_draft: "https://vocus.cc/new-editor/6aa969e6fd897800017e5323"
image_count: 0
language: zh-Hant
source: pixnet
---

# Lockbox via API: Sample Code to Call Lockbox Process (ARLPLB) using FND_REQUEST API (Doc ID 1342139.1)

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050927928
> - 發布日期：2024-09-29 19:13
> - 瀏覽人數：80
> - 分類：觀念與文件（原：Oracle Support Doc／進修深造）
> - Oracle EBS 版本：12 and later

---

Applies to:

Oracle Receivables - Version 12 and later
Information in this document applies to any platform.
FND_REQUEST.SUBMIT_REQUEST
ARLPLB

Purpose

This sample code will demonstrate the API: FND_REQUEST.SUBMIT_REQUEST to call Process Lockboxes program (ARLPLB) in Release 12.

Requirements

SQL*Plus or Oracle SQL Developer to execute the script

Configuring

The Lockbox process takes the below parameters, text in parenthese are the field names within arlplb.opc:

new transmission (Y/N) (NEW_TRANSMISSION)

transmission_id (LB_TRANSMISSION_ID)

original request id (ORIG_REQUEST_ID)

transmission name (TRANSMISSION_NAME)

submit import (Y/N) (SUBMIT_IMPORT)

datafile (DATA_FILE)

control file (CNTRL_FILE)

transmission format id (TRANSMISSION_FORMAT_ID)

submit validation (Y/N) (SUBMIT_VALIDATION)

pay unrelated invoices (Y/N) (PAY_UNRELATED_INVOICES)

lockbox ID (LOCKBOX_ID)

gl date (GL_DATE)

report format (REPORT_FORMAT)

complete batches only (Y/N) (COMPLETE_BATCHES_ONLY)

submit postbatch (Y/N) (SUBMIT_POSTBATCH)

alternate name search option (ALTERNATE_NAME_SEARCH)

post partial amount or reject entire receipt (Y/N) (IGNORE_INVALID_TXN_NUM)

USSGL transaction code (USSGL_TRN_CODE)

Organization Id (ORG_ID)

Apply Unearned Discount (Y/N) (APPLY_UNEARN_DISC)

Number of Instances (1-99) (NO_OF_INSTANCES)

Source type flag (S - SmartCash or L - Lockbox) (SOURCE_TYPE_FLAG)

scoring model Id (SCORING_MODEL_ID)

Considering the definition of the concurrent program is volatile and may change over time, you can always check for the parameters as follows:

Responsibility: System Administrator

Navigation: Concurrent > Program > Define

Run a search on Short Name = ARLPLB and scan through the list of parameters

Please ensure to modify the input parameters accordingly and send either "Y" or "N".

Note:

1) Apply Unearn discounts parameter has been introduced as part of Bug 9288902 SETUP OPTION NOT TO CALCULATE UNEARNED DISC FOR APPLICATIONS VIA LOCKBOX.

2) The parameter for "Source type flag" should be passed in as "L", otherwise, you might find following error:

ARLPLB module: Process Lockboxes

Creating new row in ar_batches

Inserted batch and updated last_batch_num

Submission is Smartcash, bypassing insert to Interim tables.

No more receipts for this batch

No more batches for this lockbox

Instructions

1. You must run this code in the APPS schema.

2. Your options to run the code are:

You can save the code in a PL/SQL package and register the procedure as a concurrent program to run from within the application

You can run it stand alone directly from PL/SQL.

Caution

This sample code is provided for educational purposes only, and is not supported by Oracle Support. It has been tested internally, however, we do not guarantee that it will work for you. Ensure that you run it in your test environment before using.

Sample Code

To set the proper context when running the process through a script, you need to determine the value for USER_ID and RESPONSIBILITY_ID. This provides information to the process so that it can simulate running as if you were logged into the Oracle Receivables application.

SELECT USER_ID,RESPONSIBILITY_ID,

RESPONSIBILITY_APPLICATION_ID, SECURITY_GROUP_ID

FROM FND_USER_RESP_GROUPS

WHERE USER_ID = (SELECT USER_ID

FROM FND_USER

WHERE USER_NAME = '&user_name')

AND RESPONSIBILITY_ID = (SELECT RESPONSIBILITY_ID

FROM FND_RESPONSIBILITY_VL

WHERE RESPONSIBILITY_NAME = '&resp_name');

You can also retrieve the above values using Help > Diagnostics > Examine menu from the application.

Block = $PROFILE$

Field = RESP_ID and USER_ID (APPL_ID is always 222)

SET SERVEROUTPUT ON;

DECLARE

v_return_val Number;

BEGIN

--Values shown here need to be replace with actual value

--fnd_global.apps_initialize(&user_id, &resp_id, &appl_id);

fnd_global.apps_initialize(1318, 50559, 222);

--mo_global.set_policy_context('S',&org_id);

mo_global.set_policy_context('S',204);

--fnd_request.set_org_id(&org_id);

fnd_request.set_org_id(204);

v_return_val := fnd_request.submit_request

('AR',    -- Application short name

'ARLPLB', -- program short name

NULL,     -- program name

NULL,     -- start date

FALSE,    -- sub-request

-- the following are ARLPLB parameters:

'Y',      --1. new transmission

NULL ,    --2. transmission_id

NULL ,    --3. original request id

'ptotatest10',  --4. transmission name

'Y',            --5. submit import

'/test.dat', --6. datafile

'ardeft', --7. control file

100,      --8. transmission format id

'N',      --9. submit validation

NULL ,    --10. pay unrelated invoices

NULL ,    --11. lockbox ID

NULL ,    --12. gl date

NULL ,    --13. report format

'Y',      --14. complete batches only

'N',      --15. submit postbatch

'N',      --16. alternate name search option

'N',      --17. post partial amount or reject entire receipt

NULL ,    --18. USSGL transaction code

204,      --19. Organization Id

'Y'       --20. apply unearn discounts

1,        --21. Number of instances

'L',      --22. Source Type Flag

1        --23. scoring model)

);

dbms_output.put_line('Concurrent Request ID :' || v_return_val);

COMMIT;

END;
