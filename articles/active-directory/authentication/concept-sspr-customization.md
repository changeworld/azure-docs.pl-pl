---
title: Customize self-service password reset - Azure Active Directory
description: Customization options for Azure AD self-service password reset
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dfd035f73ea529ddb55bac6ce601185fda51a4d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381936"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Customize the Azure AD functionality for self-service password reset

IT professionals who want to deploy self-service password reset (SSPR) in Azure Active directory (Azure AD) can customize the experience to match their users' needs.

## <a name="customize-the-contact-your-administrator-link"></a>Customize the "Contact your administrator" link

Self-service password reset users have a "Contact your administrator" link available to them in the password reset portal. If a user selects this link, it will do one of two things:

* If left in the default state:
   * Email is sent to your administrators and asks them to provide assistance in changing the user's password. See the [sample email](#sample-email) below.
* If customized:
   * Sends your user to a webpage or email address specified by the administrator for assistance.

> [!TIP]
> If you customize this, we recommend setting this to something users are already familiar with for support

> [!WARNING]
> If you customize this setting with an email address and account that needs a password reset the user may be unable to ask for assistance.

### <a name="sample-email"></a>Sample email

![Sample request to reset email sent to Administrator][Contact]

The contact email is sent to the following recipients in the following order:

1. If the **password administrator** role is assigned, administrators with this role are notified.
2. If no password administrators are assigned, then administrators with the **user administrator** role are notified.
3. If neither of the previous roles are assigned, then the **global administrators** are notified.

In all cases, a maximum of 100 recipients are notified.

To find out more about the different administrator roles and how to assign them, see [Assigning administrator roles in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Disable "Contact your administrator" emails

If your organization does not want to notify administrators about password reset requests, you can enable the following configuration:

* Enable self-service password reset for all end users. This option is under **Password Reset** > **Properties**. If you don't want users to reset their own passwords, you can scope access to an empty group. *We don't recommend this option.*
* Customize the helpdesk link to provide a web URL or mailto: address that users can use to get assistance. This option is under **Password Reset** > **Customization** > **Custom helpdesk email or URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Customize the AD FS sign-in page for SSPR

Active Directory Federation Services (AD FS) administrators can add a link to their sign-in page by using the guidance found in the [Add sign-in page description](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) article.

To add a link to the AD FS sign-in page, use the following command on your AD FS server. Users can use this page to enter the SSPR workflow.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Customize the sign-in page and access panel look and feel

You can customize the sign-in page. You can add a logo that appears along with the image that fits your company branding.

The graphics you choose are shown in the following circumstances:

* After a user enters their username
* If the user accesses the customized URL:
   * By passing the `whr` parameter to the password reset page, like `https://login.microsoftonline.com/?whr=contoso.com`
   * By passing the `username` parameter to the password reset page, like `https://login.microsoftonline.com/?username=admin@contoso.com`

Find details on how to configure company branding in the article [Add company branding to your sign-in page in Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Directory name

You can change the directory name attribute under **Azure Active Directory** > **Properties**. You can show a friendly organization name that is seen in the portal and in the automated communications. This option is the most visible in automated emails in the forms that follow:

* The friendly name in the email, for example “Microsoft on behalf of CONTOSO demo”
* The subject line in the email, for example “CONTOSO demo account email verification code”

## <a name="next-steps"></a>Następne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [I think something is broken. How do I troubleshoot SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Contact your administrator for help with resetting your password email example"
