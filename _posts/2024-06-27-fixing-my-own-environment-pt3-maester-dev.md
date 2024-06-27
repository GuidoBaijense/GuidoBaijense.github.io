---
title: 'Fixing my own environment - Part 3 - Maester.dev'
author: Guido
categories: [Microsoft Entra ID, Tutorial]
tags: [entra, identity governance, conditional access, diy, maester]
image: ../assets/img/fixing-my-own-environment-pt3-maester-dev/maester-dev-header.png
published: false
---

[Maester.dev](https://maester.dev) is an open source framework to help administrators to stay on top of their Microsoft tenant's security configuration. And it's hot🔥. Maester focusses on monitoring critical aspects of your Microsoft Environment with your Security as Code baseline. It launched April 10th, 2024 (v0.0.116) with 60 built in tests and is constantly developing (currently v0.0.129). `Update: 0.1.0` It now features 115 tests from the [Entra ID Security Config Analyzer (EIDSCA)](https://github.com/Cloud-Architekt/AzureAD-Attack-Defense/blob/main/AADSecurityConfigAnalyzer.md) by [Thomas Naunheim](https://www.cloud-architekt.net/) & [Sami Lamppu](https://samilamppu.com/) (and others), the [CISA SCuBA Project](https://www.cisa.gov/resources-tools/services/secure-cloud-business-applications-scuba-project) and other best practices.

Maester comes as a PowerShell module with Microsoft Graph authentication integrated. Since it is built upon [Pester](https://pester.dev/), it is also possible to add your own tests to the framework. Most of the built in tests offer explanations why a result is good or bad and include direct links to the Microsoft admin portals where you can immediately fix the issues.

>Maester is developed by Fabian Bader, Thomas Naunheim and Merill Fernando. Since it is open source, you can contribute directly on [<i class="fa-brands fa-github"></i> GitHub](https://github.com/maester365/maester).
{: .prompt-tip}
> This blog series is about my path to a safer environment. All shown vulnerabilities have already been patched.
{: .prompt-danger}

## The status

In the [first blog]({% post_url 2024-05-29-fixing-my-own-environment-pt1 %}) I showed the score where we started with: 52 tests passed out of 115 in total which resulted in a score of 48%. I must admit that not all tests are working as they should. Maester.dev is not yet released as a 1.0, and we have to give them some slack with that. After the second blog, about [Microsoft Secure Score for Identity]({% post_url 2024-05-30-fixing-my-own-environment-pt2-secure-score %}), I ran the tests again. This time with a score of 53%. 58 tests passed, 51 failed and 6 were not tested as they were out of scope for my environment.

![Maester.dev after implementing Secure Score recommendations](<../assets/img/fixing-my-own-environment-pt3-maester-dev/maester after secure score.png>)
_Maester.dev after implementing Secure Score for Identity recommendations_

### What's good - what's bad

If we analyze the scores, I do relatively well on the configuration of Authentication Methods and Conditional Access. Also my password rules seems to be following best practices. But we are here to improve the security posture, so I'm more interested in what's not configured correctly.

### EIDSCA: Authentication Method

I've done most of the authentication method configurations as advised. However, four of the tests have failed. The first is test EIDSCA.AF04 which tests for FIDO2 Key restrictions enforcement. It is advised to restrict them so you can decide which keys are allowed in your environment. As I test new FIDO2 keys regularly, I haven't restricted them. And I don't think I will for now. Like mentioned before, I am the only user of this tenant. I think I can decide beforehand if I would like to add a specific vendors FIDO2 key to my account.

![EIDSCA - Authentication Methods results](<../assets/img/fixing-my-own-environment-pt3-maester-dev/authentication methods eidsca.png>)
_EIDSCA - Authentication Methods result_

#### Legacy (per user) MFA portal > Converged Authentication Methods pane

I haven't migrated from the legacy MFA portal to the new converged authentication methods pane yet. Microsoft wanted to enforce this migration in september this year, but have pushed it further down the road to give organizations some slack. The new pane offers multiple advantages over the old situation where you had to manage authentication methods separately for Self Service Password Reset and Azure MFA. We already had the combined registration policy for SSPR and MFA, but which methods could be used was still managed separately.

With the new pane, it is possible to target policies based on security groups. This way, you can enable unsecure MFA-methods for specific users, or start testing new safer methods with a test group.

To migrate, take the following steps:

1. Analyze what methods are registered by your end-users. Use the [User registration details](https://entra.microsoft.com/#view/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/~/UserRegistrationDetails/fromNav/) report.
2. Analyze what methods are [allowed in SSPR](https://entra.microsoft.com/#view/Microsoft_AAD_IAM/PasswordResetMenuBlade/~/AuthenticationMethods/fromNav/) and the [Legacy MFA portal.](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)
3. Make sure that the required methods are enabled in the new [Authentication Methods pane.](https://portal.azure.com/#view/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/~/AdminAuthMethods)
4. Trigger MFA via Conditional Access instead of the legacy MFA portal.
5. Disable MFA for all users where it is configured as 'enforced' or 'enabled' in the legacy MFA portal.
6. Disable all allowed authentication methods in the legacy MFA portal and the SSPR authentication methods pane.
7. Switch the 'Manage Migration setting 'Authentication Methods' to Migration Complete.

If you fix this, test MS.AAD.3.4 will also get the status `Passed`.

More information can be found on [Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/authentication/how-to-authentication-methods-manage).

![Authentication Methods migration completed](<../assets/img/fixing-my-own-environment-pt3-maester-dev/migration completed.png>)
_Authentication Methods migration completed_

#### Report suspicious activity & geographic location in notifications

Quick question: Who am I reporting suspicious activity to? To myself. But since it is not that difficult to configure, I'll configure it as Enabled. Also because it was configured as 'Microsoft Managed' which means 'Uncontrolled changes ahead.'
For geographic location I'm not willing to enable the setting. My experience in The Netherlands is that all requests seem to come from Utrecht or Amstelveen while I'm never around. This has to do with the small size of our country and most ISP's having a backbone somewhere around those cities.

### EIDSCA: Default Authorization Settings

| Name | Expected configuration | Actual configuration |
|------|------------------------|----------------------|
| EIDSCA.AP01: Default Authorization Settings - Enabled Self service password reset for administrators. | False | True |
| EIDSCA.AP04: Default Authorization Settings - Guest invite restrictions. | adminsAndGuestInviters | adminsGuestInvitersAndAllMembers |
| EIDSCA.AP05: Default Authorization Settings - Sign-up for email based subscription. | False | True |
| EIDSCA.AP07: Default Authorization Settings - Guest user access. | 2af84b1e-32c8-42b7-82bc-daa82404023b (restrictive) | 10dae51f-b6af-4016-8d66-8c2a99b929b3 (non-restrictive) |
| EIDSCA.AP10: Default Authorization Settings - Default User Role Permissions - Allowed to create Apps. | False | True |

#### SSPR for administrators

In an ideal world, administrators should work with phishing-resistant authentication methods only. Therefore, they do not need the ability to reset their password using SSPR. As I am already working with phishing-resistant authentication methods on my administrator accounts I will disable this setting. I don't know if this is possible via the admin panel, but via Microsoft Graph it is pretty easy.
Just open Graph Explorer and do a `PATCH` request to `https://graph.microsoft.com/v1.0/policies/authorizationPolicy` with the following body:

```json
{
    "allowedToUseSSPR": false
}
```

#### Guest invite restrictions

This settings defines who can invite guest users to your tenant. I always advise to restrict this to accounts with administrative roles (eg.  Guest Inviter role ). Let's configure this. You can do this in the [admin center](https://portal.azure.com/#view/Microsoft_AAD_IAM/CompanyRelationshipsMenuBlade/~/Settings) or via the same Graph call as before, but with a different body:

```json
{
    "allowInvitesFrom": "adminsAndGuestInviters"
}
```

#### Email based subscription (self-service sign up), Guest user access, Users allowed to create applications

No, we don't want people to be able to register themselves without interference of an employee. Disable this one. Again, use the graph call. And since I also don't want my guest users to have access to all company wide data, let's restrict their access with the same action. And users that can register apps without being assigned an application administrator role only introduce trouble in your environment. Even in my development tenant.

```json
{
    "allowedtosignupemailbasedsubscriptions": false,
    "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b",
    "defaultUserRolePermissions": {
        "allowedToCreateApps": false
    }
}
```

It was probably better to combine all the above actions at once.

### EIDSCA: Miscellaneous advises

With the last 7 changes, my tenant almost complies to all Entra ID Security Config Analyzer (EIDSCA) Tests. There are 7 EIDSCA findings left and I will not comply to a few of those tests. Like mentioned before, EIDSCA.AF04 checks if you enforce FIDO2 security key restrictions. And I don't. The same goes for EIDSCA.AM09 that checks for the geographic location in push and password-less notifications. Another one is the Temporary Access Pass check EIDSCA.AT02 that requires the TAP to be configured as One-time use. In certain situations I will advise single time use. However, during password-less onboardings it can be easier for organizations to manage the process when the TAP-credential is time-limited. In my environment, I am the only one that can create TAPs. Single use won't add anything to the security posture in this case.

#### EIDSCA Default Settings - Consent Policy settings

You've heard it before. Not everything has an impact on my environment as I am the only user. The two findings regarding consent policy settings however are of that kind that I like to show as they can help you with your environment and are often forgotten.

- EIDSCA.CP01: Default Settings - Consent Policy Settings - Group owner consent for apps accessing data.
- EIDSCA.CP04: Default Settings - Consent Policy Settings - Users can request admin consent to apps they are unable to consent to.

Both are also mentioned in the CISA SCuBA project. The first one is a setting in which you can restrict group and team owners to authorize (third-party) applications to access organizational data that is associated with a group. This means that a team owner can allow apps to read all messages of that team, or a SharePoint site owner to share all files with a certain application with just a few clicks. I believe that applications should be authorized by users that know the risks of it and actually conduct due diligence before adding a new application to the environment.

##### Issue with the test

There is an issue with this test as the Group Owner Consent settings [were replaced with Team Owner consent settings](https://aka.ms/TeamRscSettings) last month (May 14th, 2024). Those Team Owner Consent Settings can only be configured via PowerShell and Graph API and can be found under `https://graph.microsoft.com/beta/teamwork/teamsAppSettings`. The documentation for Group Owner Consent settings [is still up on Microsoft Learn.](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-user-consent-groups?pivots=portal)

To fix the config tenant wide, you can do the following `PATCH` request to the before mentioned Graph endpoint:

```json
{
    "isUserPersonalScopeResourceSpecificConsentEnabled": false
}
```

The second one is regarding the setting that allows users to request admin consent for adding permissions to apps that the user does not have the permission for itself. In some organizations I advise to keep this setting disabled as they will probably never look into this requests and the requester will also forget about it in a few days. No permission is safer that too many permissions, right? If users really want access or need it, they will find a way to contact the admins and it will get fixed.

The easiest way to change this setting is in the portal, as with graph it is kinda hard to do. [Portal link to Admin Consent Settings.](https://portal.azure.com/#view/Microsoft_AAD_IAM/ConsentPoliciesMenuBlade/~/AdminConsentSettings)

#### Password Protection

Password protection is a nice feature that a lot of organizations not think about. I also didn't change a setting in this tenant yet and only added two custom passwords to the custom banned password list to show a customer how simple it is to use. I have seen multiple organizations that even installed the on-premises password protection agents, but never switched the _Mode_ to Enforced, which means that it doesn't do a thing for on-premises password resets. With default settings, most of the tests succeed. Only one doesn't: [EIDSCA.PR01: Default Settings - Password Rule Settings - Password Protection - Mode](https://maester.dev/docs/tests/EIDSCA.PR01)

![EIDSCA Password Protection Test results](<../assets/img/fixing-my-own-environment-pt3-maester-dev/eidsca password protection.png>)
_Entra ID SCA Password Protection Test results_

The test that fails is for the setting that enforces the password protection policies on on-premises Password Protection agents. However, I don't have any on-premises presence and I am cloud native/only.

![Current password protection settings](<../assets/img/fixing-my-own-environment-pt3-maester-dev/Password Protection settings.png>)
_Default Password Protection settings with two custom banned passwords_

My advise is to lower the lockout threshold, make the lockout duration longer and add multiple company-, branch, and location related words in the custom banned password list. Since I try to get as much green ticks as possible, I will enforce the on-premises Password Protection.

When the _Maester_ part for this blog series is finished, I will change the lockout threshold to a much lower value and change the lockout duration to something like 10 minutes. When I review sign in logs for customers, I often see hack attempts with around 9 bad sign in attempts for a user before the requests switch to another IP-address. When you lower the threshold, those bots will be less effective as the user account is locked for a longer period and less attempts can be made.

## Enough for now

In this blog, I discussed 9 (?, I lost count) changes that result in 12 additional green thicks ✅. Enough for now. See you next time, where I will discuss the tests from Maester itself about: Conditional Access policies, Privileged Identity Management and application settings.
![Test results](<../assets/img/fixing-my-own-environment-pt3-maester-dev/Maester tests afterwards.png>)
_Maester Test Results afterwards_

O, and I don't know why it now lists 114 tests, while it first had 115. Without updating the module or test files. Strange.

## Something else: Microsoft Global Partner of the Year Award 2024

> My team at [InSpark](https://www.inspark.nl) has won the Microsoft Global Partner of the Year Award 2024 in the category `Identity`. I would like to thank Microsoft and my awesome team members [Pim Jacobs (Identity-man.eu)](https://identity-man.eu/), [Guus van Berge](https://www.linkedin.com/in/guusvanberge/), [Matthijs Tuenter](https://www.linkedin.com/in/matthijs-tuenter/), [Barry Oudejans](https://www.linkedin.com/in/barry-oudejans/), [Jelmer Vorstenburg](https://www.linkedin.com/in/jelmervorstenburg) and [Robbert Kelder](https://www.linkedin.com/in/rskelder/) for the awesome year we've had and I'm sure we can continue this the following FY.
{: .prompt-info}
