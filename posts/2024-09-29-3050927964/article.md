---
title: "R12: AP/IBY: How to Add an End Date/or Remove an End Date for a Supplier Bank Account Via an API (Doc ID 2787870.1)"
date: "2024-09-29 19:27"
pixnet_id: "3050927964"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050927964"
status: published
views: 95
category_personal: "Oracle Support Doc"
category_primary: "職場甘苦"
category_secondary: "不設分類"
vocus_room: "Oracle ebs"
vocus_map_category: "觀念與文件"
ebs_version: "12.0.0 and later / 12.0.0 and later"
suggested_tags:
  - Oracle
  - EBS
  - Doc ID 2787870.1
  - R12
  - AP
  - IBY
  - Payables
vocus_draft: "https://vocus.cc/new-editor/6aa969e4fd897800017e5278"
image_count: 0
language: zh-Hant
source: pixnet
---

# R12: AP/IBY: How to Add an End Date/or Remove an End Date for a Supplier Bank Account Via an API (Doc ID 2787870.1)

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050927964
> - 發布日期：2024-09-29 19:27
> - 瀏覽人數：95
> - 分類：觀念與文件（原：Oracle Support Doc／職場甘苦）
> - Oracle EBS 版本：12.0.0 and later / 12.0.0 and later

---

Applies to:

Oracle Payables - Version 12.0.0 and later
Oracle Payments - Version 12.0.0 and later
Information in this document applies to any platform.

Goal

Set the end date, or remove the end date, of a supplier bank account via an API.

Solution

The following API call will end date all the bank accounts for a single supplier.

If you only want to end date some of the accounts, then the query will need to be modified to limit the script to the desired accounts.

set serveroutput on size 1000000;

declare

   l_msg_data VARCHAR2 (1000);

   l_msg_count NUMBER;

   l_return_status VARCHAR2 (100);

   l_response IBY_FNDCPT_COMMON_PUB.Result_rec_type;

   cursor a is

       SELECT EXT_BANK_ACCOUNT_ID,

       START_DATE,

       end_date,

       object_version_number

       FROM IBY_EXT_BANK_ACCOUNTS

       WHERE end_date is null

       and ext_bank_account_id IN (

           SELECT DISTINCT a.instrument_id

           FROM iby_pmt_instr_uses_all a

           WHERE a.payment_flow    = 'DISBURSEMENTS'

           AND a.instrument_type   = 'BANKACCOUNT'

           AND a.EXT_PMT_PARTY_ID IN (

               SELECT EXT_PAYEE_ID

               FROM IBY_EXTERNAL_PAYEES_ALL

               WHERE (payee_party_id = (

                   SELECT a.party_id

                   FROM ap_suppliers a

                   WHERE a.vendor_id = &vendor_id)

               AND (party_site_id IS NULL

               OR party_site_id IN (

                   SELECT b.party_site_id

                   FROM ap_supplier_sites_all b

                   WHERE b.vendor_id         = &vendor_id

                   AND (b. vendor_site_id    = NVL( 0, vendor_site_id) OR 0 = 0 ))))))

       ORDER BY ext_bank_account_id ;

BEGIN

   FND_MSG_PUB.initialize;

   for x in a loop

       dbms_output.put('processing account id: ' || x.EXT_BANK_ACCOUNT_ID);

       iby_ext_bankacct_pub.set_ext_bank_acct_dates (

             p_api_version => 1.0

           , p_init_msg_list => FND_API.G_TRUE

           , p_acct_id        => x.EXT_BANK_ACCOUNT_ID

           , p_start_date => nvl(x.start_date,sysdate)

           , p_end_date     => sysdate

           , p_object_version_number  => x.object_version_number

           , x_return_status => l_return_status

           , x_msg_count => l_msg_count

           , x_msg_data => l_msg_data

           , x_response => l_response );

      dbms_output.put_line(' --- ' || l_return_status || ' --- ' || l_msg_count || ' --- ' || l_msg_data);

       IF l_msg_count >1 THEN

           FOR I IN 1..l_msg_count LOOP

               dbms_output.put_line(' ' || I || '. ' || SubStr(FND_MSG_PUB.Get(p_encoded => FND_API.G_FALSE ), 1, 255));

           END LOOP;

       END IF;

       if (l_return_status = 'S') then

           dbms_output.put_line('Success');

           commit;

       else

           rollback;

       end if;

   end loop;

end;

/

To remove an end date set it to a date in the future.  in the script above change

, p_end_date => sysdate

to

, p_end_date => TO_DATE('31.12.9999 23:59:59', 'dd.mm.yyyy hh24:mi:ss');

To end date the supplier link to the bank account instead of the bank account itself see note "R12: AP/IBY: How to set Bank Account Uses End Date for a Specific Supplier Site" (Doc ID 726763.1)
