---
title: 'Fixing my own environment - Part 2 - Microsoft Secure Score for Identity'
author: Guido
categories: [Microsoft Entra ID, Tutorial]
tags: [entra, identity governance, conditional access, diy, fixingMyOwnEnvironment]
image: ../assets/img/fixing-my-own-environment-pt2-secure-score/SecureScore overview preview.png
published: true
---

In the previous blog, I introduced you to my efforts to elevate the **identity and security** of my developer tenant to the highest standard. This blog will zoom in on **Microsoft Secure Score for Identity**. We will find out how this tool can help us _fix my own environment._

> This blog series is about my path to a safer environment. All shown vulnerabilities have already been patched.
{: .prompt-warning}

![Modern Secure Score for Identity start result](<../assets/img/fixing-my-own-environment-pt1/identity secure score start.png>){: w="350" }
_Modern Secure Score for Identity result at the start_

We start with a score of 65.18% which is not that bad. I hope to reach a score above 90% while keeping the environment usable for development purposes. Let's see what recommendations we have.

## Classic Overview Recommendations

We will first work with the classic overview of Identity Secure score. Based on this overview, there are only 4 points *To address*.

| Name    | Score Impact | Current Score | Max Score | User Impact | Implementation Cost | Status |
| -------- | ------- | -------- | ------- | -------- | ------- | -------- |
| Ensure multi-factor authentication is enabled for all users in administrative roles|17.86%|10|10|Low|Low|Completed|
| **Ensure multi-factor authentication is enabled for all users** | **16.07%** | **4.5** | **9** | **High** | **High** | **To address** |
|Ensure the 'Password expiration policy' is set to 'Set passwords to never expire (recommended)'|14.29%|8|8|Moderate|Low|Completed|
|Enable Conditional Access policies to block legacy authentication|14.29%|8|8|Moderate|Moderate|Completed |
|**Enable Microsoft Entra ID Identity Protection sign-in risk policies**|**12.50%** |**0** |**7** |**Moderate** |**Moderate** |**To address** |
|**Enable Microsoft Entra ID Identity Protection user risk policies** |**12.50%** |**0** |**7** |**Moderate** |**Moderate** |**To address**|
|Ensure user consent to apps accessing company data on their behalf is not allowed |7.14% |4 |4 |Moderate |Low |Completed|
|Ensure 'Self service password reset enabled' is set to 'All' |1.79% |1 |1 |Moderate |Moderate |Completed|
|**Designate more than one global admin** |**1.79%** |**0** |**1** |**Low** |**Low** |**To address**|
|Use least privileged administrative roles |1.79% |1 |1 |Low |Low |Completed|

The four remaining points are:

1. Ensure multi-factor authentication is enabled for all users
2. Enable Microsoft Entra ID Identity Protection sign-in risk policies
3. Enable Microsoft Entra ID Identity Protection user risk policies
4. Designate more than one global admin

Let's start with the last one. Because, why not?

### Multiple Global Administrators

Adding multiple Global Administrators could have been the easiest recommendation to implement. However, it isn't as straightforward as it seems. Currently, I have assigned this role to my main user. Wow. Bad practice. But hey, it is a development tenant, right? I will change this to a Break Glass setup where I create a new user, protect the user with a Passkey, and assign the account permanent Global Administrator permissions. I can then make my main administrator account Eligible for Global Administrator via Privileged Identity Management. To limit the risk of phishing, I've removed the license and mailbox for this Break Glass user.

Microsoft states in the description of this item that the *CIS O365 Benchmark 2.0.0* suggests to have between two and four global admins. On the other hand, Microsoft advises limiting the number of privileged roles in your environment as much as possible. In the Privileged Identity Management overview, Microsoft even warns you when you have more than five Global Administrators.

#### Rationale

Microsoft gives the following rationale:
> If there is only one global tenant administrator, he or she can perform malicious activity without the possibility of being discovered by another admin. If there are numerous global tenant administrators, the more likely it is that one of their accounts will be successfully breached by an external attacker.

Adding multiple Global Administrators in my environment only helps with the risk of locking me out since I am the only user and administrator. Therefore, I do not need to mitigate the risk of another user performing malicious activity.

One down, three to go.

### Enable Microsoft Entra ID Identity Protection user risk policies

This recommendation has a significant impact on the Secure Score for Identity (12.50%). It requires an Entra ID P2 license. An Entra ID Identity Protection user risk policy can be created via Conditional Access. I once imported the Conditional Access framework that we developed at InSpark for our customers, but I have not enabled the Identity Protection policies yet. I don't know why. Now is the time to do so. If you want to work on Conditional Access but do not have experience with it, check out the [available built-in templates](https://portal.azure.com/#view/Microsoft_AAD_ConditionalAccess/CaTemplates.ReactView).

![Available Conditional Access Templates](<../assets/img/fixing-my-own-environment-pt2-secure-score/conditional access templates.png>)
_Available Conditional Access Policy Templates_

Another way to get started with Conditional Access is by implementing a pre-defined framework. For example, the [Zero Trust framework](https://github.com/microsoft/ConditionalAccessforZeroTrustResources/tree/main) that was made available by Claus Jespersen of Microsoft.

### Enable Microsoft Entra ID Identity Protection sign-in risk policies

Nothing different from the recommendation before. I already had the policy, it was just not enabled.

### Ensure multi-factor authentication is enabled for all users

The recommendation is to ensure multi-factor authentication (MFA) for all users. Microsoft Entra ID provides multiple methods to register MFA. A policy can be set to require MFA registration during the user's next login, and it has been set. It is enabled for all users, but we do not get the full score yet. The current score is 4.5 out of a maximum of 9 points, which is because half of my (fake) users have not registered an additional authentication method. This means that I have never signed in with those users; otherwise, I would have had to register an MFA method.

In a production environment, this is a huge risk, and you might encounter this situation when locations like the office are not in scope for MFA enforcement. Users who only work in the office never have to register for MFA, and the first person to use the account from somewhere else (with stolen credentials) can register MFA without anyone noticing any strange behavior.

In my case, these users don't have any data and no access. I could add my phone number to all of them, but at the same time, I preach that phone is not a safe MFA method. Therefore, I will accept the 'risk' and proceed with the new Secure Score experience.

One way I can still secure those users is by requiring *secure registration of MFA methods* via Conditional Access. This ensures that users need a Temporary Access Pass or an already registered MFA method before they can register a new MFA method. For this purpose, I created the following conditional access policy:

|Scope|Target resources|Conditions|Access controls - Grant|
|---|---|---|---|
|All Users, except the Break Glass account|User actions: Register security information|Any device, location or client app|Require multi-factor authentication|

This ensures that even the users who have not yet registered an MFA method cannot be used to gain access to my tenant.

## New Secure Score Recommendations

![New secure score overview](<../assets/img/fixing-my-own-environment-pt2-secure-score/SecureScore overview preview.png>)
_Recommendations in the New Secure Score overview_

The new secure score overview ([Entra Admin panel > Recommendations](https://entra.microsoft.com/#view/Microsoft_AAD_IAM/TenantOverview.ReactView/tabId/recommendationsTab)) is a huge step forward in helping organizations reach their security goals. While it does not look that different besides some colors and spacing, it has much better guidance to overcome the (new) recommendations.

Let's have a look at the recommendations. In the new overview, there are 13 recommendations. We have seen 10 of them before, and we've just fixed them.

| Priority | Recommendation | Secure Score points | Impacted resource type | Status |
|---|---|---|---|---|
| **Medium** | **Remove unused credentials from applications** | **N/A** | **Applications** | **Active** |
| **Medium** | **Remove unused applications** |  **N/A** | **Applications** | **Active** |
| Low | Use least privileged administrative roles | 1/1 | Users | Completed |
| *High* | *Protect all users with a user risk policy* |  *0/7* | *Users* | *Active* |
| *High* | *Protect all users with a sign-in risk policy* |  *0/7* | *Users* | *Active* |
| High | Require multi-factor authentication for administrative roles |  10/10 | Users | Completed |
| *High* | *Ensure all users can complete multi-factor authentication* |  *4.50/9* | *Users* | *Active* |
| High | Enable policy to block legacy authentication |  8/8 | Users | Completed |
| *Low* | *Designate more than one global admin* |  *0/1* | *Users* | *Active* |
| **High** | **Renew expiring application credentials** |  **N/A**| **Applications** | **Active** |
| High | Do not expire passwords |  8/8 | Tenant level | Completed |
| Low | Enable self-service password reset |  1/1 | Users | Completed |
| Medium | Do not allow users to grant consent to unreliable applications |  4/4 | Tenant level | Completed |

There are currently three new recommendations that we haven't seen in the classic overview. Since the new overview is still in preview, there are no points given for these recommendations. This means that our score will not go up when we overcome these issues. However, they are still good recommendations and offer a clear action plan. Let's start with the first one.

### Remove unused credentials from applications

As you can see in the screenshot, this recommendation provides an overview of applications with credentials that have not been used in the last 30 days. Unfortunately, the resources are not clickable, which means that I have to manually search for the resource and address the issue with the resource or credential.

![New Secure Score recommendation](<../assets/img/fixing-my-own-environment-pt2-secure-score/new secure score recommendation.png>)
_New Secure Score recommendation to remove unused credentials from applications_

Microsoft provides the following rationale for this recommendation:
> An application credential is used to get a token that grants access to a resource or another service. If an application credential is compromised, it could be used to access sensitive resources or allow a bad actor to move latterly depending on the access granted to the application. Removing credentials not actively used by applications improves security posture and promotes app hygiene. It reduces the risk of application compromise and improves the security posture of the application by reducing the attack surface for credential misuse by discovery.
{: .prompt-info }

It would have been great if we could immediately take action by selecting items in this list and clicking a button. Via the Actions column, it is possible to get more details and later click through to the application, where I had to manually remove the app or the credentials.

![More details pane for remove unused credentials recommendation](<../assets/img/fixing-my-own-environment-pt2-secure-score/remove credentials more details.png>)
_More details pane for the remove unused credentials recommendation_

Fixed. two to go.

### Remove unused applications

Like I mentioned before, the tenant we are working with is my development tenant. What happens with these kinds of tenants? You create a lot of app registrations and never remove them. This recommendation provides a list of **40 applications** that have not been used *in the past 90 days*.

So, now I have to do some cleaning up. It's not that difficult since they are not in use and there is no user impact. However, if I want to spin up an old Visual Studio project and it doesn't work, I must remember that the app registration no longer exists and I need to create a new one. Or not? The recommendation also shows app registrations required for the functioning of Entra ID, so be careful.

![App that should not be deleted](<../assets/img/fixing-my-own-environment-pt2-secure-score/Should I delete this one.png>)
_I think I should not delete this one_

### Renew expiring application credentials

I question why this recommendation fits within Secure Score for Identity. How does this contribute to a safer environment? Microsoft's rationale is:
> Your tenant has applications with credentials that will expire soon. Renewing the app credential(s) before its expiration ensures the application continues to function and reduces the possibility of downtime due to an expired credential.
{: .prompt-info }

My opinion is that no access is safer than having access. Fixing this recommendation will not make my environment safer. I understand that it can impact your users when applications are unable to function due to an expiring secret. In my case, there is only **1 resource** impacted, and my solution is simply to remove the app registration.

## Conclusion

If we look at the steps we have taken, can we conclude that the environment is much safer now? Maybe a little bit. Inactive service principals and credentials are a security risk, especially in an enterprise environment. On the other hand, Identity Protection doesn't make a difference as the users in my tenant are all demo users who do not have accounts with other services. Their credentials are not prone to leaking as their credentials essentially do not exist.

The same goes for *Multiple Global admins*. It does not add additional safety as I am the only administrator. But hey, all for the KPIs, right?

The Secure Score for Identity after implementing as much as possible is **91.34%** which exceeds my 90% goal.

![Secure Score after fixes](<../assets/img/fixing-my-own-environment-pt2-secure-score/Secure Score after.png>)
_Secure Score for Identity after applying as much of the recommendations as possible_

### Remaining points

Achieving 100% is not possible in my setup because I use a Break Glass account that is excluded from most of my Conditional Access policies. This account is critical for emergency access and thus remains out of scope for some recommendations. It might not be an issue to include the break glass account as the remediation of a sign-in or user risk is a new password or MFA. The registered MFA is a Passkey which uses the same authentication API as the password and does not rely on the Azure MFA service.

|Name|Score Impact|Current Score|Max Score|User Impact|Implementation Cost|Status|
|----|----|----|----|----|---|---|
|Ensure multi-factor authentication is enabled for all users|16.07%|4.67|9|High|High|Risk accepted|
|Enable Microsoft Entra ID Identity Protection sign-in risk policies|12.50%|6.74|7|Moderate|Moderate|Risk accepted|
|Enable Microsoft Entra ID Identity Protection user risk policies|12.50%|6.74|7|Moderate|Moderate|Risk accepted|

_Available points left in Microsoft Secure Score for Identity_

Most points missing are for users who have not registered an Authentication Method. These users are for demo purposes and won't be actively used. And I don't bother the lack of a registered MFA for them.

### What's next

In the next episode of this blog series, we'll look into [Maester.dev](https://Maester.dev). We'll see how much the score has improved with the implementation of all Secure Score for Identity recommendations, and how it will help enhance our security posture even further.
