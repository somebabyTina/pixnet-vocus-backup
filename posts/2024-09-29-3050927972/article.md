---
title: "Receipts: Setup and Usage of Automatic Cash Application Functionality (Doc ID 3022392.1)"
date: "2024-09-29 19:31"
pixnet_id: "3050927972"
public_url: "https://somebabytina.pixnet.net/blog/posts/3050927972"
status: published
views: 130
category_personal: "Oracle Support Doc"
category_primary: "職場甘苦"
category_secondary: "不設分類"
vocus_room: "Oracle ebs"
vocus_map_category: "觀念與文件"
ebs_version: "12.2.6 and later"
suggested_tags:
  - Oracle
  - EBS
  - Doc ID 3022392.1
  - Receivables
vocus_draft: "https://vocus.cc/new-editor/6aa96970fd897800017e38ad"
image_count: 18
language: zh-Hant
source: pixnet
---

# Receipts: Setup and Usage of Automatic Cash Application Functionality (Doc ID 3022392.1)

> **原文資訊（自 Pixnet 遷移）**
> - 原文連結：https://somebabytina.pixnet.net/blog/posts/3050927972
> - 發布日期：2024-09-29 19:31
> - 瀏覽人數：130
> - 分類：觀念與文件（原：Oracle Support Doc／職場甘苦）
> - Oracle EBS 版本：12.2.6 and later

---

In this Document

			Abstract

			History

			Details

			1. Introduction

			2. Automatic Matching Rules and Automatic Matching Rule Sets

			2.1 Automatic Matching Rules

			2.2 Automatic Matching Rule Sets

			3. Assign Automatic Matching Rule Sets

			3.1 Receipt Level

			3.2 System Options Level

			4. Remittance Lines

			5. Automatic Cash Application Master Program

			6. Files involved

			Summary

			References

Applies to:

Oracle Receivables - Version 12.2.6 and later
Information in this document applies to any platform.

Abstract

This functionality was originally controlled by Development, and although it has been enabled widely from 12.2.6+ versions of the application, its use has some limitations, especially with performance.

History

Created date: MAY-2024

Details

NOTE: In the images below and/or the attached document, user details / company name / address / email / telephone number represent a fictitious sample (based upon made up data used in the Oracle Demo Vision instance).  Any similarity to actual persons, living or dead, is purely coincidental and not intended in any manner.

1. Introduction

This functionality is supported from 12.2.7 and above.

The functionality described in this document provide an automated method to apply any unapplied receipt, regardless of the source of origin, to outstanding receivables transactions at the header level, partially or in full. The automated process works based on the user-defined matching rules.

This automatic cash application process affects any fully or partially unapplied receipts (manual, automatic, lockbox, quick cash, and prepayment) and all outstanding AR transactions (invoices, debit memos, chargebacks and deposits).

The application process occurs at the transaction header level, partially or in full and the receipt that is applied can be either manually entered in the system or uploaded automatically by any supported method.

The receipt can be applied on any transaction created for the customer entered on receipt header (across customer sites within customer account) or any 3rd party payer (across a group of associated customers).

It is performed by the Automatic Cash Application Master program which can be invoked explicitly or internally in the code.

The screenshots appearing on this note correspond to 12.2.10 application version.

2. Automatic Matching Rules and Automatic Matching Rule Sets

Automatic Cash Application Master program applies receipts using the Automatic Matching Rules Sets defined by the user.

(Note:  Please do not confuse with Autocash Rules Sets, Application Rules Sets or Cash Application Owner Assignment rules, these belong to other setup)

To create and maintain Automatic Matching Rules and Automatic Matching Rules Sets, please use the following navigation path:

Using: Receivables Manager responsibility

Nav: Setup > Receipts > Automatic Matching Setup.

The navigation leads to a page that has 2 tabs:

Automatic Matching Rules tab – this allow users to create, update and view automatic matching rules

Automatic Matching Rule Sets tab  – this allow users to create, update and view automatic matching rule sets

2.1 Automatic Matching Rules

Use this tab to search, create, or update automatic matching rules.

To create an automatic matching rule:

 When clicking on Create button in the above screen, the landing page contains two main sections:

    1. The Rule Information section which contains the matching criteria

    2. The Transformations section which allows users to define string functions to be applied to the remittance reference and document number before system attempts to find a match for the receipt.

  Rule Information section

 This section contains following fields:

     - Rule Name

     - Description

     - Effective From

     - Effective To

     - Match Type:  This field determines the type of match the rule will use. This field cannot be updated once the rule is already assigned to a rule set.

           There are 2 types of matching:

            >> Exact match  (this was not included in earlier versions of this feature).

                 Use this when want the receivable application to happen if the document number in the remittance line (reference number) matches exactly to the document number in the system.

                 If there are rule transformations defined, the exact match evaluation is done after the transformations has been applied..

            >> Scoring Match, this will use the match score that is calculated using the Levenshtein distance algorithm.  When selecting this option, 2 additional fields are required:

                   >>>  Threshold for Automatic Match (%) :

                           Threshold for Automatic Match defines the minimum match score a match must have for system to automatically apply the remittance line to the transaction corresponding to the match.

                           Enter a threshold equal to or greater than 80%.

                   >>>  Threshold for Suggested Match (%):

                           Threshold for Suggested Match defines the minimum match score a match must have for system to consider it as a match suggestion.

                           Enter a threshold which is lesser than the threshold for automatic match and equal to or greater than 60%.

                           If the match score is less than the automatic match tolerance but greater than suggested match tolerance then those matches will be made available to user as suggested matches.

                           User can manually review the suggested matches and apply them, if appropriate.

                           The suggestions will appear in the Suggested Transaction Matches section of the Remittance Lines menu option after querying the receipt, or through the Cash Application Work Queue form.

     - Match Document Number By:  User can select any of the below attributes to which the rule will be applied for matching.

       The attribute chosen here is the attribute from the transaction that will get matched to the remittance line reference to do the cash application.

       These list was enhanced to include the line level attributes in case of line level application is to be performed. Please be aware that the list of values in the form does not show the items in alphabetical order.

        •    Transaction Number

        •    Header Sales Order Number

        •    Line Sales Order Number

        •    Purchase Order Number

        •    Way Bill Number

        •    Balance Forward Bill Number

        •    Trailer Number

        •    Service Contract Number

        •    Reference Number

        •    Interface Header Attribute1 to Interface Header Attribute15

        •    Transaction Header DFF Attribute1 to Attribute15

        •    Line Transaction Flexfield Attribute1 to Attribute15

        •    Transaction Line DFF Attribute1 to Attribute15

        The use of line level attributes might add a performance issue when searching for the match, because is trying to match with data in ra_customer_trx_lines_all table.

    - Match Customer Reference Number By:  This field is available only when the Match Type is Exact Match.

      All the attributes provided for the document number matching will be available for selection for the customer reference matching also, but the same attribute cannot be selected for both document number and

      customer reference matching.

      The customer reference provided on the remittance line can be used as a secondary matching criterion when the matching is based on the exact match.

      System will try to find an exact match for both, the document number and customer reference number. The receipt will be automatically applied only if a unique match found for both the attributes.

Transformations section

The Transformations region allows the user to define how a remittance reference and transaction attributes values should be transformed for the purpose of matching. The string transformation can be performed on the Reference value (or Customer Reference value) of the remittance line, as well as on the value of the attribute selected in Match Document Number by and Match Customer Reference by form fields.

The string functions defined here will be applied before system attempts to find a match for the receipt.

User can add multiple transformation rows for each of the numbers.

Defining transformation records is optional. User can create an automatic matching rule without creating any rules for transforming remittance reference and document numbers. You can do none, both, or any.

Multiple rows will be interpreted as having an AND Boolean operator. Each transformation row will be executed in the order of sequence number. When multiple transformation rows are defined the result from the prior transformation row is passed to the next transformation row and so on for performing string transformations.

String transformation is performed as follows:

- If the String Value is ‘Space’ then all spaces will be removed from either front or end of the string based on the value given for String Location field.

- If the String Value is ‘Zero’ then all zeros will be removed from either front or end of the string based on the value given for String Location field.

- If the String Value is ‘Any’ then system will remove specific number characters from either front or end of the string based on the values given for String Location field and No. of Characters fields.

- If the String Location is ‘Floating’ the system will remove all occurrences of the character(s) specified in the String Value field.

When using rule Match Type as Exact Match, you can setup the Transformations section for both document number and customer reference. There are 2 tabs to accomplish this:

>> Document Number Matching tab, this is applicable when using the Match Document Number by field.

      Inside this tab there are 2 sections:

a. Remittance Reference Number, this applies to the Reference Number value in the remittance line.

b. Remittance Invoice Number, this applies to the transaction attribute chosen in the Match Document Number by field, not necessarily refers to Invoice Number as the name seems to imply.

        >> Customer Reference Matching tab, this is applicable when selecting Match Customer Reference by field.

             Inside this tab there are 2 sections:

a. Remittance Reference Number, this applies to the Customer Reference value in the remittance line.

b. Remittance Invoice Number, this applies to the transaction attribute chosen in the Match Document Number by field, not necessarily refers to Invoice Number as the name seems to imply.

When using rule Match Type as Scoring Match, you can setup the Transformations section for only the document number.

2.2 Automatic Matching Rule Sets

Use this tab to search, create, or update the automatic matching rule sets.

An automatic matching rule set consist of one or several automatic matching rules.

 To create an automatic matching rule set, click on Create button above, which lands on this page below:

Rule Set Information section

Enter the Rule Set Name, Description, Effective From and To dates.If it is Active or not.

Duplicate Matches Handling: This field determines how duplicate matches will be handled in the case they occur.

The list has two values:

    - Apply in the order of aging, meaning the cash application will take place in the descending order of invoice aging.

    - Mark as suggested matches. If duplicates found they will display as suggested matches.

Enable Knap Sack Matching: This checkbox actives the knap sack matching. When this box is enabled, in the scenario when the receipt does not have remittance lines or no document reference is provided on the remittance lines, the system will try to find a unique match by knap sacking the remittance amount with the open transactions of the customer.

Knapsack matching is the method of applying receipts by trying to find a set of transactions whose open balance matches with the remittance amount.  System will first pool all the eligible transactions of the customer (and the related customers) and tries to find a combination of transactions whose open balance matches the remittance amount. The receipt will be automatically applied if a unique match is found.

Knap sack matching will be used for:

•    Unapplied remittance lines without reference –remittance line amount will be used for knapsack matching;

•    Receipts without remittance lines –receipt amount will be used for knapsack matching.

If a receipt has unapplied remittance lines and if the knapsack matching of unapplied remittance lines does not yield a match then the receipt amount will be used for knapsack matching.

When Enable Knap Sack Matching is checked, if duplicate matches are found, the invoices will be displayed under suggested matches. If no match is found, the receipt will remain unapplied.

Outstanding Amount Calculation section

Exclude Disputed Amount: If checked, disputed amount will be excluded from outstanding amount.

Exclude Credit Memo: If checked, credit memos will be excluded from outstanding amount.

Exclude Finance Charges: If checked, finance charges will be excluded from outstanding amount.

Tolerance Amount: The user can specify the tolerance amount for finding a match based using the remittance or receipt amount. The tolerance amount will provide an amount range for matching the remittance amount with the open balance of a transaction or a set of transactions. For example, if the tolerance range is defined as 50 then the system will calculate the amount range as remittance amount minus 50 and remittance amount plus 50 and will try to find a match using the range instead of the remittance amount.

Matching Rules section

Here you enter the rules previously defined. You can assign a priority to each of Exact Match rules in the group. Priority determines the order in which system will evaluate each rule for matching the attributes.

A total of ten matching rules can be included in this section. Exact match rules will be executed in the rank of priority. All the scoring match rules will be executed in parallel after the exact match rules

3. Assign Automatic Matching Rule Sets

This can be done at

    - Receipt level

    - System Options level

The field is also at Customer Site/Account, and customer profile level, but it was not implemented.

Rules at receipt level have precedence over rules at System Options level.

3.1 Receipt Level

    3.1.1. On Receipt form

        Nav: Receipts > Receipts

        Two attributes were added in tab More on Receipts form:

            - Use Automatic Matching Rules checkbox to indicate whether receipt is eligible to automatic application using auto match rules.

            - Automatic Matching Rule Set field to store the automatic matching rule set to be used for automatic application.

   3.1.2. On Receipt Batch form for a manual Quickcash batch

       Nav: Receipts > Batches

       Click Receipts button

   3.1.3. On Lockbox Setup form

       Nav: Setup > Receipts > Lockboxes > Lockboxes

       The Lockboxes setup form shows  2 fields for automatic receipt matching process. These are located in the Receipts tab.

       Use checkbox:  ‘Use Automatic Matching Rules’ to specify the receipts processed through lockbox will be applied using automatic matching rules.

       If the checkbox above is checked, the field ‘Automatic Matching Rule Set’ becomes available to store the automatic matching rule set to be used on the lockbox setup.

3.2 System Options Level

Nav: Setup > System > System Options

Search for Operating Unit, select Miscellaneous tab and enter a Automatic Matching Rule Sets and save:

The automatic matching rule set defined in the System Options form acts as default rule set at the operating unit level.

4. Remittance Lines

 Remittance Lines can be created by:

    - Lockbox

    - Receipts Workbench

4.1. Lockbox

If Lockbox is set to use the automatic matching rules, lockbox will generate the remittance information and populates the remittance lines.

4.2. Receipts Workbench

Remittance Lines form was introduced to display remittance lines for a receipt. User can view, modify, delete and enter new remittance lines in this form.

The form can be accessed:

From QuickCash batch form, click receipts button,  click on the Remittance Lines button.

            For the Quickcash batch created through lockbox process, the Remittance Lines region will show the remittance lines imported through lockbox file. In the case of manual Quickcash batch, the form will not contain any lines. In both cases the form allows users to view and update existing remittance lines, as well as create new remittance lines. The form validates that total amount of all remittance lines does not exceed the receipt amount.

From Receipt form Tools menu. There is the menu option Remittance Lines.

         The form “Remittance Lines and Suggested Matches” above displays the remittance lines imported through lockbox process as well as manually entered by the user.

         This form will also display the suggested matches generated by the Automatic Application program.

         Each remittance line has a status of either:

                 Auto applied – when the Auto Applied checkbox is selected

                 Manually applied – when the Manually Applied checkbox is selected

                 Not applied – when neither Auto Applied or Manually Applied checkboxes are selected

        This form allows user to update the remittance lines when they are not yet applied. Once either auto applied or manually applied, users cannot update the remittance lines.

If the remittance line is not applied then user can select one of the suggested matches for the line and apply the same by clicking the Apply button. The Apply button will navigate the user to the Applications form and populate the application information.

        For current record in Remittance Lines region, the Suggestion Matches region will show one or more suggested matches for the remittance line.

For an unapplied remittance line, the user can select one or more suggested matches to apply, by clicking on the “Select” checkbox in the Suggested Matches region.

If the user selects more than one suggested match the system will prorate the remittance line amount in the ratio of remaining balance for the selected suggested matches.

For applied remittance lines, user cannot select any suggestion match for application.

        The conditions for selecting the suggested matches for application are as follows:

If the customer is already identified for the receipt then user can select suggested matches belonging to the customer on the receipt as well as the customers with whom that customer has paying relationship. If the “Allow Payment of Unrelated Transactions” option is selected in the System Options then transactions belonging to any customer can be selected for application.

If the customer is not already identified for the receipt then all suggested matches selected should belong to same Bill To Customer. Note: In this case, system will associate the receipt with the customer whose transactions are selected from suggested matches.

5. Automatic Cash Application Master Program

Short name: ARATAPPM

This is the actual concurrent program that does the receipt applications based on the rules defined.

It is run internally by Lockbox, also can be manually submitted through request window or through the Actions menu option named Auto Apply in the receipt form.

Displayed parameters:

The ‘Automatic Cash Application Master Program’ will select a batch of receipts based on the criteria provided by the user in the program parameters. The program will then apply the automatic matching rule sets assigned to the receipt to either find a match to apply the receipt or generate a list of suggested matches. In addition to the selection criteria specified in the program parameters, the program will select a receipt for auto application only if the receipt:

Has the ‘Use Automatic Matching Rules’ check box selected;

Has unidentified or unapplied amount available for application;

Has unapplied remittance lines available for application.

Once the program selects a receipt for automatic application the program will loop through all unapplied remittance lines and attempt to automatically apply the remittance line using automatic matching rules.

The program will use the automatic matching rule set assigned to the receipt to perform matching.

If a rule is successful in providing automatic match then rest of the rules will not be evaluated. However, if the rule is successful in only providing a suggested match or does not provide any match then the program will use the rule with next highest priority.

Once the program finds matches and calculates match scores, it will use the match score thresholds defined for the automatic matching rules to determine whether the match is eligible for automatic match or suggested match.

If the program finds multiple matches which exceed the automatic matching threshold then program will use the match with highest score to apply the remittance line.

Following conditions must be satisfied for the system to automatically apply the remittance line:

If the customer is already identified for the receipt then the select matches must belong to the customer on the receipt or the customers with whom that customer has paying relationship. If the “Allow Payment of Unrelated Transactions” option is selected in the System Options then transactions can belong to any customer.

If the customer is not already identified for the receipt then all selected matches should belong to same Bill To Customer.

Note: In this case, system will associate the receipt with the customer and Bill To location, whose transactions are selected for automatic application.

Apart from the threshold for automatic match, the selected match should also satisfy the Match on Corresponding Date and Match on Corresponding Amount conditions of the Automatic Matching rule.

If the program is successful in finding a match then the program will apply the receipt. This will ensure that all the existing validations will be applied for receipt application.

After a match is successfully applied, system will:

Update the Auto Applied flag of the remittance line to selected state.

If the receipt has customer location assigned then updated the customer site profile to store the auto match rule, the one that was successful in the finding the match. If the customer location is not assigned to the receipt then update the customer account profile to store the auto match rule.

Application to Balance Forward Bills:

In the case of applying a remittance line to a Balance Forward Bill, system will:

First select all open transactions of the bill;

Sort them by transaction date;

And then apply the remittance line amount in the sort order till the application amount is exhausted.

Suggested Matches:

If the program fails to find automatic match but finds match with a score, which exceeds the suggested match threshold then the program will populate the new table introduced to store the suggested matches. For generating suggested match, the program will not apply the Match on Corresponding Date and Match on Corresponding Amount conditions of the Automatic Matching rule.

The program also assign the exception reason from the automatic matching rule set to the receipt, if the receipt has at least one unapplied remittance line which the program could not automatically apply.

The program provides an execution report as output, which will list the matching outcome for the each remittance line processed by the report.

The program is capable for parallel processing to improve the performance. Based on the value specified for the “No. of Instances” parameter, the program will launch the child requests which will process the data in parallel.
