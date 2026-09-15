---
title: "R12: FND: How To Purge Attachments With No Expiration Date In FND_LOBS Segment. (Doc ID 3013809.1)"
date: "2024-09-29 19:44"
pixnet_id: "3050928000"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050928000"
status: published
views: 486
category_personal: "Oracle Support Doc"
category_primary: "進修深造"
category_secondary: "不設分類"
vocus_room: "Oracle ebs"
vocus_map_category: "觀念與文件"
ebs_version: "12.2.10 and later"
suggested_tags:
  - Oracle
  - EBS
  - Doc ID 3013809.1
  - R12
  - FND
  - Payables
vocus_draft: "https://vocus.cc/new-editor/6aa9696ffd897800017e388c"
image_count: 0
language: zh-Hant
source: pixnet
---

# R12: FND: How To Purge Attachments With No Expiration Date In FND_LOBS Segment. (Doc ID 3013809.1)

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050928000
> - 發布日期：2024-09-29 19:44
> - 瀏覽人數：486
> - 分類：觀念與文件（原：Oracle Support Doc／進修深造）
> - Oracle EBS 版本：12.2.10 and later

---

In this Document

						Goal

						Solution

						Table FND_LOBS

						Table FND_DOCUMENTS

						Table FND_ATTACHED_DOCUMENTS

						Concurrent program "Purge Obsolete Generic File Manager Data" - FNDGFMPR

						More documentation

						References

Applies to:

			Oracle Payables - Version 12.2.10 and later

			Information in this document applies to any platform.

Goal

How to purge attachments in with no expiration date to reduce the size of the FND_LOBS LOBSEGMENT?

Per MOS document How To Purge FND_LOBS "export" Records With No EXPIRATION_DATE, ran the program "Purge Obsolete Generic File Manager Data" in the following scenarios:

With parameters Expired => No & Program Name => export.  It completed file and purged records from FND_LOBS for Program "export" and program "FND_HELP"

But, with parameters Expired => No & Program Name => FNDATTCH , is NOT deleting records from FND_LOBS for program "FNDATTCH".

Even after the executions described, the FND_LOBS table still storing hundreds of gigabytes of space and the LOBSEGMENT linked to table FND_LOB occupies hundreads of gigabytes.  The following SQL from MOS document FAQ - Performance Considerations For FND_LOBS shows this:

SELECT program_name

			     , COUNT(0)

			     , ROUND( SUM (( DBMS_LOB.getlength(file_data)-1)/1024/1024/1024),1 ) size_gb

			  FROM FND_LOBS

			 GROUP BY program_name

			 ORDER BY size_gb DESC ;

			PROGRAM_NAME    COUNT(0)   SIZE_GB
--------------- -------- ---------
FNDATTCH          865331     416.7
                  175993      18.8

SELECT SUM(bytes)/1024/1024/1024

			     , s.segment_name, s.segment_type

			  FROM DBA_LOBS l

			     , DBA_SEGMENTS s

			 WHERE s.segment_type = 'LOBSEGMENT'

			   AND l.table_name = 'FND_LOBS'

			   AND s.segment_name = l.segment_name

			 GROUP BY s.segment_name,s.segment_type ;

			OWNER    OBJECT_NAME                OBJECT_TYPE CREATED    TABLESPACE_NAME  SUM(B.BYTES/1024/1024/1024)
-------- -------------------------- ----------- ---------- --------------- ----------------------------
APPLSYS  SYS_LOB0000034032C00004$$  LOB         14-MAY-00  APPS_TS_MEDIA                     674.401855

Solution

Table FND_LOBS

FND_LOBS stores information about all LOBs managed by the Generic File Manager (GFM). Each row includes the file identifier, name, content-type, and actual data.

			Each row also includes the dates the file was uploaded and will expire, the associated program name and tag, and the language and Oracle characterset.

			The file data, which is a binary LOB, is stored exactly as it is uploaded from a client browser, which means that no translation work is required during a download to

			make it HTTP compliant. Therefore uploads from non-browser sources will have to prepare the contents appropriately (for instance, separating lines with CRLF).

			The program_name and program_tag may be used by clients of the GFM for any purpose, such as striping, partitioning, or purging the table if the program is de-installed.

			They are otherwise strictly informative. These columns and the expiration date are properly set when the procedure FND_GFM.CONFIRM_UPLOAD is called.

			If not called, the column expiration_date remains set, and will eventually be purged by the procedure FND_GFM.PURGE_EXPIRED.

			Table Structure:

  Name                   Null?    Type

			  ---------------------- -------- ----------------

			  FILE_ID                NOT NULL NUMBER

			  FILE_NAME                       VARCHAR2(256)

			  FILE_CONTENT_TYPE      NOT NULL VARCHAR2(256)

			  FILE_DATA                       BLOB

			  UPLOAD_DATE                     DATE

			  EXPIRATION_DATE                 DATE

			  PROGRAM_NAME                    VARCHAR2(32)

			  PROGRAM_TAG                     VARCHAR2(32)

			  LANGUAGE                        VARCHAR2(4)

			  ORACLE_CHARSET                  VARCHAR2(30)

			  FILE_FORMAT            NOT NULL VARCHAR2(10)

Many DISTINCT values in column PROGRAM_NAME, FNDATTCH is among them:

  PROGRAM_NAME

			  --------------------------------

			  BIAF_FR_XML

			  HRMS_ADI

			  FNDATTCH

			  FOLDER_VERIFY

			  export

			  IBCCITEM - LOAD SEED ROW

			  GCS

			  PERWSIMG

			  IBE

			  FND_HELP

			  Oracle E Records

			  PAY_166_12_ar_KW.pdf

			  PER_WS1_gb_UK.pdf

			  PER_WS4_gb_UK.pdf

			  ...

			  ...

			  PAY_R167_ar_KW.rtf

			  PER_VIS_ar_AE.pdf

			  PER_PASS_ar_AE.pdf

			  ...

			  ...

			  PAY_MCP_ar_AE.rtf

			  PERDTUPR.rtf

			  PAY_NL_IZA_NLTMP.rtf

			  68 rows selected.

Table FND_DOCUMENTS

This table stores language-independent information about a document. For example, each row contains a document identifier, a category identifier, the method of security used for the document (SECURITY_TYPE, where 1=Organization, 2=Set of Books, 3=Business unit, 4=None), the period in which the document is active, and a flag to indicate whether or not the document can be shared outside of the security type (PUBLISH_FLAG).

Other specifications in this table include: datatype (DATATYPE_ID, where 1=short text, 2=long text, 3=image, 4=OLE object, 5=Web Page, 6=File, etc)

The document can be referenced by many application entities and changed only in the define document form (USAGE_TYPE=S); it can be used as a fill-in-the-blanks document, where each time you use a template, you make a copy of it (USAGE_TYPE=T); or it can be used only one time (USAGE_TYPE=O). Images and OLE Objects cannot be used as templates.

These documents are the ones linked to an EBS entity/transaction, like for example, a Payables Invoice or Payment, or a Purchasing Purchase Order, etc.

FND_LOBS links to FND_DOCUMENTS via the MEDIA_ID column (to match FND_LOBS FILE_ID).

Table FND_ATTACHED_DOCUMENTS

This table stores information relating a document to an application entity. For example, a record may link a document to a sales order or an item. Each row contains foreign keys to FND_DOCUMENTS and FND_DOCUMENT_ENTITIES.  FND_DOCUMENT_ENTITIES lists each entity to which attachments can be linked. For example, attachments can be linked to Items, Sales Orders, Invoices, etc.

Per above, the following SQL should show the list of entities related to Payables (AP):

SELECT entity_name, table_name

			FROM FND_Document_Entities

			WHERE application_Id = 200 /* Oracle Payables */ ;

			ENTITY_NAME                TABLE_NAME
-------------------------- ------------------------------
AP_CHECKS                  AP_CHECKS
AP_INVOICES                AP_INVOICES
                           PO_VENDOR_SITES
                           AP_EXPENSE_REPORT_HEADERS
                           AP_INVOICES_INTERFACE
                           AP_RECURRING_PAYMENTS
                           AP_INVOICES
PO_VENDORS                 PO_VENDORS
8 rows selected.

This shows how all documents of entity_name AP_INVOICES, refer to attachments to Invoices, Expense Reports, Recurring Payments, etc.

Concurrent program "Purge Obsolete Generic File Manager Data" - FNDGFMPR

Concurrent parameters are:

Expired: Yes/No.

Program Name:  The distinct program names stored in table FND_LOBS.  POssible values are: FNDATTCH, FND_HELP, PERWSIMGm, etc.

Program Tag:  Also, the distinct progam tags sotred in table FND_LOBS for a given "Program Name".

Purge Orphaned:  Yes/No.

Concurrent Executable is the PLSQL call to Server-side API FND_GFM.Purge.  Package FND_GFM source files are:

Package Body - $FND_TOP/patch/115/sql/AFGFMB.pls

Package Specification - $FND_TOP/patch/115/sql//AFGFMS.pls

When the concurrent program FNDGFMPR executes, API FND.GFM.Purge calls procedure fnd_gfm.purge_expired or procedure fnd_gfm.purge_set as follow:

IF concurrent program is called with program name FNDATTCH and Expired parameter set to anything other than "No" (N)

				THEN it calls fnd_gfm.purge_expired ( 'FNDATTCH', [Purge Orphanded] );

ELSE it calls fnd_gfm.purge_set ( 'FNDATTCH', [Program Tag] );

So to the following to reduce the FND_LOBS LOBSEGMENT size:

Run concurrent "Purge Obsolete Generic File Manager Data" executed with parameters:

				Program Name = FNDATTCH,  Expired = Yes, and Purge Orphaned = Yes

To confirm the effect of this, run the following SQL:

SELECT count(*)

				  FROM FND_LOBS FL

				 WHERE NOT EXISTS (SELECT '1'  FROM FND_DOCUMENTS FD  WHERE FD.MEDIA_ID = FL.FILE_ID  AND FD.DATATYPE_ID = 6)

				   AND PROGRAM_NAME = 'FNDATTCH'

				   AND EXPIRATION_DATE IS NULL ;

For the particular case that originated this MOS document, this step removed 3175 records.

IMPORTANT:  The records removed by the program are records that have NO link or reference to a particular Application Document (Table FND_DOCUMENTS). Hence, the "NOT EXIST" condition in the SQL.

Now, run the following SQL to check for records in FND_LOBS linked to a FND_DOCUMENT.  Notice we removed the "NOT" from the "NOT EXISTS" statement:

SELECT count(*)

				  FROM FND_LOBS FL

				 WHERE EXISTS (SELECT '1'  FROM FND_DOCUMENTS FD  WHERE FD.MEDIA_ID = FL.FILE_ID  AND FD.DATATYPE_ID = 6)

				   AND PROGRAM_NAME = 'FNDATTCH'  AND EXPIRATION_DATE IS NULL ;

For the particular case that originated this MOS document, this SQL returned almost 900K records.  This is 900K records in FND_LOBS linked to real EBS Applications Documents Files (DataType= 6) that belong to an EBS application module - transaction/entity, like a Payables Invoice or a Purchasing PO.

Check on these transactions "Upload Dates" (since the expiration date is NULL), using:

SELECT COUNT(*), TO_CHAR(upload_date, 'YYYY') upload_Year

				  FROM FND_LOBS

				 WHERE PROGRAM_NAME = 'FNDATTCH'

				   AND expiration_date is NULL

				 GROUP BY TO_CHAR(upload_date, 'YYYY')

				 ORDER BY 2 ;

				For the particular case that originated this MOS document, this SQL returned the following:

     COUNT(*) UPLOAD_YEAR
---------- -----------
     58699 2015
     80043 2016
     93663 2017
     94081 2018
    118106 2019
    118335 2020
    120961 2021
    102911 2022
    110001 2023
      9846 2024
10 rows selected.
				 Notice there is 10 years of transactions attachments.

Perhaps they need to review their Transaction Retention Policies.

				For transaction related attachments, there is are Purge programs. For example for AP, there is the "Submit Purge" form.  This is a form you can find under the responsibility "Payables Purge".  This form executable file is APXPGSUB.  In this form, the user creates a "Purge" definition that includes a Name, a category which my varies of what needds to be purged; like: Single Invoices, Invoices and POs, Suppliers, etc.  The user also must give the "Last Activity Date" which is the date used to fetch the transactions to purge.  The basic idea is that the Purge will fetch all transactions for which Last Update Date on any of its linked records (Accounting, payment, POs , etc) is before the parameter date entered.

The records purged by the module -specific purge program, include attachments.

				How to run the Payables Purge program:

Login Oracle Applications using a Payables Purge responsibility.

Open the "Submit Purge" form

					(N) Purge

Create a new "Purge Definition" in the "Submit Purge" form.

Once you save the new Purge definition record, you get the button to [Initiate].

					This button will submit the concurrent program "AP/PO Purge Initiation (Selection) Routine" - APXPGSEL

					This program will fetch the records to be purge and create a "preliminary" report for the user to review and decide if they want to proceed with the purge.

					The "Purge definition" will refresh on the screen and now you see the buttons to [Abort], [Restart], or [Confirm]

					Each button has its own concurrent program.  In particular, [Confirm] will run the "AP/PO Purge Deletion Routine" to perform the actual DELETEs from the tables, like AP_INVOICES, AP_INVOICE_LINES, AP_CHECKS, PO_HEADERS, etc depending on the conditions and category set.

					The Purge Deletion routine spawns child process that run in parallel (for better performance).

For the particular case that originated this MOS document, a Payable Purge was submitted with a "Last Activity Date" of 2015.  This purged 377 Invoices (with Lines and distributions, etc), 105 checks, and 203 POs, among other records.  Some attachments were purged as well.

Recheck the table FND_LOBS and the LOBSEGMENT using the SQLs given above.

For the particular case that originated this MOS document, the table and segement size started to decrease after many additional executions of the Payable Purge.

The following SQL should show the count "Totals" of records in FND_LOBS linked to any Payables transaction:

Totals for Payables:

SELECT COUNT(*), TO_CHAR( FLS.upload_date, 'YYYY' ) Upload_Yr, FDE.entity_name, FDE.table_name, FDS.datatype_ID

					FROM FND_ATTACHED_DOCUMENTS FAD

					, FND_DOCUMENTS FDS

					, FND_LOBS FLS

					, FND_DOCUMENT_ENTITIES FDE

					WHERE 2=2

					AND FLS.file_ID = FDS.media_Id

					AND FAD.document_ID = FDS.document_ID

					-- AND FDS.datatype_ID = 6

					AND FLS.program_name = 'FNDATTCH'

					AND FAD.entity_name = FDE.entity_name

					AND FDE.application_ID = 200 -- >> ONLY PAYABLES

					GROUP BY TO_CHAR( FLS.upload_date, 'YYYY' ), FDE.entity_name, FDE.table_name, FDS.datatype_ID

					ORDER BY 2, 3, 4, 5 ;

For details on the transactions:

SELECT -- COUNT(*)

					TO_CHAR( FLS.upload_date, 'YYYY' ) Upload_Yr, FDE.entity_name, FDE.table_name, FDS.datatype_ID, PK1_VALUE, PK2_VALUE

					FROM FND_ATTACHED_DOCUMENTS FAD

					, FND_DOCUMENTS FDS

					, FND_LOBS FLS

					, FND_DOCUMENT_ENTITIES FDE

					WHERE 2=2

					AND FLS.file_ID = FDS.media_Id

					AND FAD.document_ID = FDS.document_ID

					-- AND FDS.datatype_ID = 6

					AND FLS.program_name = 'FNDATTCH'

					AND FAD.entity_name = FDE.entity_name

					AND FDE.application_ID = 200

					-- GROUP BY TO_CHAR( FLS.upload_date, 'YYYY' ), FDE.entity_name, FDE.table_name, FDS.datatype_ID

					ORDER BY 1, 2, 3, 4 ;

More documentation

To get more information about Payabels Purging, please refer to Chapter 9 - Resource Management of the "Oracle® Payables User's Guide Release 12.2" - Part No. E48760-17; in PDF or Web formats.

Here you will find all about:

Criteria for Purging Records

Submitting Purges

Viewing the Status of a Purge

Aborting a Purge / Restarting a Purge

Purge Reports: Preliminary Purged Listings, Final Purged Listings, etc.

			.

References

			NOTE:298698.1 - Avoiding Abnormal Growth of FND_LOBS Table Due To Attachments Data In E-Business Suite Applications

			NOTE:1998077.1 - Automated Troubleshooting Process

			NOTE:1165208.1 - Concurrent Request "Purge Obsolete Generic File Manager Data" Methods

			NOTE:829235.1 - FAQ - Performance Considerations For FND_LOBS

			NOTE:242090.1 - SEGMENT SHRINK and Details.

			NOTE:2768387.1 - How To Purge FND_LOBS "export" Records With No EXPIRATION_DATE

			NOTE:1288149.1 - How To Manage, Reduce, and/or Purged The FND_LOBS Table?

			NOTE:752322.1 - Reducing the Oracle E-Business Suite Data Footprint

			NOTE:1163933.1 - Table FND_LOBS Shows Columns PROGRAM_NAME And UPLOAD_DATE As Null For Attachments

			NOTE:1320736.1 - AP: How To Create and Capture an FND Debug Logfile

			NOTE:303709.1 - E-Business Suite Database For Applications Technology Stack Reclaiming Unused Space In A EBS Tablespace By Reducing The Size Of The Datafiles
