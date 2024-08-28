---
title: 'Password delivery in a Microsoft hybrid world'
author: Guido
categories: [Microsoft Entra ID]
tags: [entra, identity governance, passwordless]
image: ../assets/img/password-delivery-in-a-microsoft-hybrid-world/password-delivery.jpg
---

*Password delivery is a pain in the \***. We all know we must do better. Unfortunately, we cannot change the world in one go, so we have to live with processes within the organization and support them as best as possible, while still striving for a passwordless environment. This blog focuses on the methods available in Hybrid Active Directory/Microsoft Entra ID environments when using Inbound User Provisioning via the native API-driven inbound user provisioning.*

If you use the native Inbound User Provisioning, you might know that it generates a password for new users that cannot be retrieved. Microsoft advises delivering a Temporary Access Pass (TAP) via lifecycle workflows. They even added a template to create and send the TAP to the user’s manager on the employee’s hire date so they can provide it to the new employee. The employee should then use the TAP to register passwordless methods like the Authenticator App with Phone-sign-in.

In a modern environment, this might work. But I have some objections:

- I don’t want anyone else to possess a user’s credentials besides the user themselves.
- I don’t want usernames and credentials to be sent in the same *plain* email.
- The UX, especially with multi-use TAPs, is not great. When a TAP exists, Microsoft Login will prompt for the TAP, even after setting a password or phone sign-in.
- Good luck signing in to your on-prem thin client or any device that does not support web sign-in.

I’ve implemented a similar method for clients where passwordless is the norm. For them, we crafted a custom extension Logic App for Lifecycle workflows that leverages an API to send the TAP by text message to the end-user. They can obtain their username from their managers and are directed to register passwordless methods like a passkey or phone sign-in at https://aka.ms/mysecurityinfo and remove the TAP afterwards.

## Methods available

What methods do we natively have to retrieve a password in a Hybrid Environment when leveraging API-driven inbound user provisioning?

1. Provision the end users mobile phone number and direct users to https://aka.ms/sspr for a 'forgotten password'.[^footnote]
2. Send a TAP to the manager via Microsoft Entra Lifecycle Workflows and ask them to register MFA-methods in https://aka.ms/mysecurityinfo and then experience the 'I forgot my password'-flow at https://aka.ms/sspr.
3. Instruct users to contact a Servicedesk or IT Admin for a password reset.

I have issues with all of them. The user has never received a password, so they cannot have forgotten it yet. I don’t think Self-Service Password Reset is a welcoming way to be introduced to your new work environment. It just doesn’t feel right and gives me an amateur vibe.

## What else?

Microsoft Graph is, as far as I know, not an option[^fn-nth-2][^fn-nth-3]. Because we are talking about hybrid environments, the password needs to be changed on-premises. SSPR can change passwords on-premises, but I just wrote that I don't want to use that route. If you want to extend cloud automation to on-premises, Azure Automation offers Extension-based Hybrid Workers.

Using the Hybrid Runbook Worker VM extension, you can configure scripts in Azure Automation that will run on-premises. We can trigger runbooks from within Logic Apps and therefore can still use Lifecycle Workflows with custom extensions. This way we can set a temporary password on an account, require password change on sign in and sent the password to either the user via SMS or the manager by mail.

However, this has a huge potential for failure as we leverage multiple tools and identities. They all need to be hardened and if one fails, the whole chain fails and the user still has no password.

I once built an application that hands out Verified Employee ID credentials when you scanned your passport, driver's license or ID card. That Verified Credential could then be used to retrieve a Temporary Access Pass. This is secure, but doesn't overcome the quest to retrieve a password.

## To conclude

Passwordless is still a future thought for most companies with a large on-premises footprint. In the meantime we have to wait for a native method that is secure and failure-proof. It would be great if we could use the Change Password flow within the MyAccounts/MySecurityInfo portal when you sign in with a secure passwordless method without providing your current password. Until then, what are your thoughts on delivering passwords without using third party tooling?

[^footnote]: Microsoft mentions this method on one of the provisioning learn pages. [Plan for password delivery of new user accounts - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/app-provisioning/plan-cloud-hr-provision#plan-for-password-delivery-of-new-user-accounts)

[^fn-nth-2]: Authentication Methods Change Password via Microsoft Graph cannot be used with Application or Managed Identities. Only Delegated permissions are allowed. [authenticationMethod: resetPassword - Microsoft Graph v1.0 | Microsoft Learn](https://learn.microsoft.com/en-us/graph/api/authenticationmethod-resetpassword?view=graph-rest-1.0&tabs=http#permissions)

[^fn-nth-3]: You can update the users password profile with the https://graph.microsoft.com/user/{userId} endpoint. However, this will be overwritten with the first sync from on-premises. Entra Connect Sync has an default interval of 30 minutes while Cloud Sync runs intervals of around 3 minutes.