---
title: Controls in segregation of duties
Description: Documentation related to the configuration of SoD matrix in the studio
---

# Segregation of Duties Controls

## Concept

Segregation of Duties (SoD) is a risk management principle that ensures no single individual has control over all steps of a critical process, thereby preventing errors and reducing the risk of fraud.

### Different types of SoD

SoD rules can be defined for any critical business process, such as for instance: 

- Accounting and finances
    - Procure-to-Pay 
    - Order-to-Cash 
    - Record-to-Report 
    - Inventory Mgt 
    - Treasury & Cash Mgt 
    - Payroll & HR 
    - Fixed Assets 
- Trading and Asset Management
    - Separation of Front-Office, Middle-Office and Back-Office duties 
    - Separation of IT / maintenance and business duties 

- IT
    - Separation of Development and Production duties
    - Separation of admin rights and other high privilege access (backup, DBA, User access mgt …) 
    - Change management / CI/CD

## SoD in IDA

There are two major ways to configure SoD in IDA. Either by creating custom controls for each SoD rule you want to implement, or by using the SoD matrix data collection process.

### Pros and cons

Those two ways of implementing SoD each have pros and cons.

Implementing SoD via the use of custom controls is more time consuming and requires the creation of a singular control for each rule. But this allows for more flexibility and complexity for each rule configuration.

Implementing SoD with embedded matrix gives a standardize way of loading rules, hence less flexibility and complexity. But it makes the process faster and can quickly be used out of the box.

Whatever the complexity of the SoD rules that should be implemented in the project, an effort will be required to define those rules.

### Custom control

There are three ways to build custom 'SoD' controls.

> [!Warning] Controls configured this way are not considered SoD controls by the product. Therefore they won't be available in the SoD pages.

1) **Deprecated:** SoD control between two singular permissions. This kind of control returns the identities or accounts in discrepancy due to them having access to both permissions at the same time. The use of SoD matrix is recommended instead, as those controls are not displayed OOTB in the latest version of the IAP portal.
    ![Segregation of duties](./images/sod-control-permissionxpermission.png "SoD permission x permission")


2) **Deprecated:** SoD control between two sets of permissions. This kind of control returns the identities or accounts in discrepancy due to them having access to at least one permission in the first set and one in the second set at the same time. The use of SoD matrix along with business activities is recommended instead, as those controls are not displayed OOTB in the latest version of the IAP portal.
    ![Segregation of duties](./images/sod-control-permission_list.png "SoD sets of permission")

3) The third way of doing it would be through a custom rule used for the control and this would provide way more flexibility and complexity in the control.

    In the example below, we want to identify the identities having access to the permissions "Utilisateur" and "Valideur3", not having access to the permission "Valideur2" and having access to either the "Administrateur Remedy" or the "Administrateur SAGE" permission.

    As a logical operation : `Utilisateur AND Valideur3 AND NOT Valideur2 AND (Administrateur Remedy OR Administrateur SAGE)`

    ![Segregation of duties](./images/sod-rule.png "SoD rule")

    That rule can then be used in a control and the identities returned would have SoD defects.

### SoD matrix

SoD matrices are objects embedded in the product, their purpose is to be able to generate multiple SoD controls automatically during an execution plan. The controls covered by a matrix are the same as the one defined in the part 1 and 2 in [Custom control](#custom-control).

A matrix is identified by a code and a displayname. As well as optional tags including a type and a list of 9 custom fields.

![SoD collector matrix](./images/sod-collector-matrix.png "SoD collector matrix")

Once created, a matrix is an empty shell and several pairs of toxic permissions should be added to that matrix. Each of those toxic pair will be computed as a SoD control during the execution plan. These pairs can either be between two permissions of between two sets of permissions. In the latter those sets of permissions must be loaded in business activities beforehand.

#### SoD matrix permission pair

Permission pairs are built based on two pairs of application/permission, a unique control identifier and the code of a matrix created in a previous step.

The `Result type` combo makes the control process on the identity or the account level.
- On the account level, a discepancy will show up if the account has both permissions.
- On the identity level, a discrepancy will show up if the identity has both permissions through one or more account.

For permission pairs, the `Model validation` should be set to `Permission type validation`

![SoD pair permission 1](./images/sod-pair-permission_1.png "SoD pair permission 1")

Some optional attributes can be configured on the pair, like the displayname, risklevel (integer between 0 and 5), suggested mitigation...

![SoD pair permission 2](./images/sod-pair-permission_2.png "SoD pair permission 2")

Some tags can be added as well. For the compatibility with SoD pages as of IAP 3.5, the type field should be filled with the value "SoD between permissions" and the custom1 should contain the same matrix code as the one defined in the SOD control tab.

![SoD pair permission 3](./images/sod-pair-permission_3.png "SoD pair permission 3")

#### SoD matrix business activity pair

Creating a toxic pair based on BAs (Business Activities), can be done in the same way as for standard permissions but requires mandatory process beforehand as well as some slight modification of several fields.

It is mandatory to load the BAs along with its permission with a permissiontype set to 'Activity' before using them in the matrix permission pair target. The toxic pair can then be created in the same way as explained above, using the identifier of the BAs permission.

For BAs pairs, the Model validation should be set to  "Activity type validation"

![SoD pair BA 1](./images/sod-pair-ba_1.png "SoD pair BA 1")

For the compatibility with SoD pages as of IAP 3.5, the type field should be filled with the value "SoD between activities" and the custom1 should contain the same matrix code as the one defined in the SOD control tab.

## SoD Designer

The SoD Designer is a webpage available in the IAP portal meant to help users build their SoD matrices. It provides an interface to select pairs of toxic permissions, that can be added to a defined matrix along with a risk level defined for each pair.

This 'SoD - Designer page' is available for functionaladmins and technicaladmins through the Controls menu. The sod license feature is required to access this interface.

### Create a matrix

From that page, the user have to select two lists of applications. The permissions contained in those applications will then be displayed and can be used to fill a SoD matrix. 

![SoD designer 1](./images/sod-designer_1.png "SoD designer 1")

The user can select one or more permission in each list along with a risk level below. Then press the "Add pairs" button to create the list of toxic pairs that will be added to the list on the right, with the risk level.

If two permissions are selected in the first list and three in the second, six toxic pairs will be created (2×3). A permission cannot be toxic with itself, and the pairs can be cross-applications.

![SoD designer 2](./images/sod-designer_2.png "SoD designer 2")

If toxic pairs need to be removed, they can be selected and the "Remove pairs" button pressed to remove them from the list.
​
In case of an incorrect risk level, the pair should be selected again in both lists on the left along with the new risk level and added again. The previous pair will be overwritten by the new one, correcting the risk level.

![SoD designer 3](./images/sod-designer_3.png "SoD designer 3")

The matrix can be displayed as a cross table with the "Crosstable" button to get a better view of it.

![SoD designer crosstable](./images/sod-designer_crosstable.png "SoD designer crosstable")

Once created, the toxic pairs can be saved in a matrix. A name must be entered in the text field in the top right.

![SoD designer 4](./images/sod-designer_4.png "SoD designer 4")

The "Save matrix" button can then be pressed to save everything that has been created.

### Load a matrix

There are two ways to load a matrix in the SoD Designer page, both available via the "Load matrix" button.
​
![SoD designer load](./images/sod-designer_load.png "SoD designer load")

A matrix can be loaded either by selecting one that has already been created in the environment, which will then populate the page with the correct information, or by uploading an xlsx file of an exported matrix that follows a specific format. These exported matrix files can be retrieved with the "Export matrix" button, which is useful when a matrix has been configured in one environment and must be available in another.

### Load a matrix

> [!Warning] The `bw_sod_designer` facet must be deployed in the project to load the matrix configured with the SoD Designer.

During the configuration of the matrix, the `Load the matrix in the future execution plan` option can be checked before saving to load it in future execution plans.

It can also be exported to an xlsx file that can be placed in a defined folder to load it on another environment.

### Delete a matrix

To delete a matrix, click "Delete matrix". A matrix must then be selected in the dialog that appears from among the existing matrices in the environment, and the deletion must be confirmed.

