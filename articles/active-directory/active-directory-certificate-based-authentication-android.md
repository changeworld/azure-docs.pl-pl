---
title: Azure Active Directory na podstawie certyfikatu uwierzytelniania w systemie Android
description: Więcej informacji na temat obsługiwanych scenariuszy i wymagania dotyczące konfigurowania uwierzytelniania opartego na certyfikatach w rozwiązaniach urządzeń z systemem Android
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: ef58fd6c4701f367c6b8664c9cf9ee76e15fbd70
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33880752"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory na podstawie certyfikatu uwierzytelniania w systemie Android

Urządzenia z systemem android umożliwiają uwierzytelnianie w usłudze Azure Active Directory za pomocą certyfikatu klienta na swoim urządzeniu podczas łączenia się z uwierzytelniania opartego na certyfikatach (CBA):

* Aplikacje mobilne pakietu Office, takich jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Konfigurowanie tej funkcji eliminuje potrzebę wprowadzić kombinacja nazwy użytkownika i hasła do niektórych poczty i aplikacje Microsoft Office na urządzeniu przenośnym.

W tym temacie przedstawiono wymagania i obsługiwane scenariusze związane z konfigurowaniem CBA na urządzeniu z systemem iOS(Android) dla użytkowników dzierżaw Office 365 Enterprise, Business, edukacji, instytucji rządowych Stanów Zjednoczonych, Chin i planów Niemczech.

Ta funkcja jest dostępna w wersji zapoznawczej w planach Office 365 instytucji rządowych Stanów Zjednoczonych obrony i federalne.

## <a name="microsoft-mobile-applications-support"></a>Pomoc techniczna firmy Microsoft aplikacji dla urządzeń przenośnych

| Aplikacje | Pomoc techniczna |
| --- | --- |
| Azure Information Protection aplikacji |![Zaznacz][1] |
| Portal firmy usługi Intune |![Zaznacz][1] |
| Microsoft Teams |![Zaznacz][1] |
| OneNote |![Zaznacz][1] |
| OneDrive |![Zaznacz][1] |
| Outlook |![Zaznacz][1] |
| Power BI |![Zaznacz][1] |
| Skype dla firm |![Zaznacz][1] |
| Word / Excel / PowerPoint |![Zaznacz][1] |
| Yammer |![Zaznacz][1] |

### <a name="implementation-requirements"></a>Wymagania dotyczące implementacji

Wersja systemu operacyjnego urządzenia musi być Android 5.0 (interfejs typu lizak) lub nowszym.

Serwer federacyjny musi być skonfigurowany.

Dla usługi Azure Active Directory odwołać certyfikat klienta tokenu usług AD FS musi mieć następujące oświadczeń:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Numer seryjny certyfikatu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (String wystawcy certyfikatu klienta)

Usługa Azure Active Directory dodaje te oświadczenia do tokenu odświeżania, jeśli są dostępne w tokenu usług AD FS (lub inne tokenu SAML). Gdy token odświeżania musi być weryfikowane, te informacje służy do sprawdzania odwołania.

Najlepszym rozwiązaniem należy zaktualizować strony błędów usług AD FS w organizacji z następującymi informacjami:

* Wymagania dotyczące instalowania Authenticator firmy Microsoft w systemie Android.
* Instrukcje dotyczące sposobu uzyskania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [dostosowywanie stron AD FS logowania](https://technet.microsoft.com/library/dn280950.aspx).

Wyślij niektóre aplikacje pakietu Office (z włączoną nowoczesnego uwierzytelniania) "*= monit logowania*" do usługi Azure AD w żądaniu. Domyślnie program Azure AD tłumaczy "*= monit logowania*"w żądaniu dla usług AD FS jako"*wauth = usernamepassworduri*" (zapyta usług AD FS do uwierzytelniania U/P) i "*wfresh = 0*" (zapyta usług AD FS do Ignoruj stan logowania jednokrotnego i nie świeże uwierzytelnianie). Aby włączyć uwierzytelnianie oparte na certyfikatach dla tych aplikacji, należy zmodyfikować domyślne zachowanie usługi Azure AD. Ustaw "*PromptLoginBehavior*"w ustawieniach domeny federacyjnej do"*wyłączone*".
Można użyć [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) polecenia cmdlet do wykonania tego zadania:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Obsługa klientów programu Exchange ActiveSync

Niektóre aplikacje programu Exchange ActiveSync w systemie Android 5.0 (interfejs typu lizak) lub nowszym są obsługiwane. Aby ustalić, czy aplikację poczty e-mail obsługuje tę funkcję, skontaktuj się z deweloperem aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz skonfigurować uwierzytelnianie oparte na certyfikatach w danym środowisku, zobacz [wprowadzenie do uwierzytelniania opartego na certyfikatach w systemie Android](active-directory-certificate-based-authentication-get-started.md) instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
