---
title: "Managing Inbound Provisioning for Employees with Multiple Employments in Microsoft Entra"
date: 2025-11-28
categories: 
- Microsoft Entra
- Identity Management
- HR Provisioning
tags: 
- inbound provisioning
- multiple employments
- contracts
- automation
- Entra Id
summary: "Learn how to efficiently manage inbound provisioning for employees who have multiple employments or contracts using Microsoft Entra. This article covers best practices, technical setup, and common challenges."
author: Guido
published: false
---

<!-- # Managing Inbound Provisioning for Employees with Multiple Employments in Microsoft Entra -->

**I've done my fair share of Inbound Provisioning implementations in Microsoft Entra. Employees with multiple contracts at the same company are common and tricky for inbound provisioning. The real headache appears when you try to automate birthright access with Entitlement Management: attributes like `jobTitle` and `department` only accept a single value. The good news? With a few smart patterns in Microsoft Entra, you can handle these multi-employment scenarios cleanly, keep HR data aligned, and provision access accurately at scale. This post shows you how.**

Multiple contracts? Yes, it is more common than you think. Each HR(M)-system works differently and each company as well. I've seen organizations that create a custom position in their hr-system for each employee that joins an extra department. But in The Netherlands, it is common to have contracts for 2 days a week for role X at department Y, and another contract for 3 days a week for role Z at a different department. I've seen HR(M)-systems output `person`-cards with just primary employment information, but also API's that output every single `employment` but with the same user-information.

In most cases organizations are happy with provisioning of the primary employment information or the information for the role in which the employee works the most hours. With grouping on user-information in your system or code of choice, and selecting the objects that fit your policies, you're probably fine. However, I recently had the requirement to assign Birthright access automatically based on attributes. Also for those users with double-employments. For all their roles.


> **Birthright Access** is the automatic provision of the minimum necessary access rights to employees based on their job title, department or other attributes. You get access because you exist and belong to a certain department or you have a certain job.
{: .prompt-info }

## Types of custom attributes in Entra

There are five ways of storing data in fields that don't natively exist in Microsoft Entra:

1. **Custom Security Attributes**: Meant for PII data that is needed in target applications. Has no multi-value type. Not available in Dynamic Queries.
1. **Extension attributes (1-15)**: Originally meant to sync on-premises extension attributes to Entra. Only single valued strings. Can't be managed from Entra directly for hybrid accounts.
1. **Directory extensions**: You add extensions to an `application` that can be used to store custom data. Also multi-valued-strings :)
1. **Schema extensions**: Configured on the tenant and can be assigned to almost all resource types. Even to Administrative Units. Not available in Dynamic Queries.
1. **Open extensions** Not discoverable or filterable data. Meant for SharePoint or Exchange related content. Not available in Dynamic Queries.
More differences can be found [here](https://learn.microsoft.com/en-us/graph/extensibility-overview?tabs=http#comparison-of-extension-types).

To have a solution for entitlement management with multi-employments, we need the attribute to be multi-valued and to be available in Dynamic Queries. This means we are bound to Directory extensions to achieve our goal.

## How to create directory extensions

Some text about app registration and a post action to the `/extensionProperties` endpoint with the right body. Give an example for `departmentJobtitle` field.
Note that the app registration needs user.readwrite.all permissions! :S:S:S and Check if it is also possible with less. Maybe check the permissions of the b2c extensions app in LABS.

```json
POST https://graph.microsoft.com/v1.0/applications
{
    "displayName":"userextensionsapp"
    "extensionProperties":[
        { "extensionObject"}
    ]
}
```

Another one:
```json
POST to assign the user.readwrite.all permissions
```

Then go to this url to consent to the permissions.

```json
PATCH to assign value to the user object
```

## How to do this in provisioning
Some information about csv2scim and how to get multi-valued attributes.
How to map in the application

## And how to use in Entitlement Management or Dynamic Groups
using the `in` operator.