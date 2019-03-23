---
title: Uwierzytelnianie oparte na certyfikatach w systemie Android — usługi Azure Active Directory
description: Dowiedz się więcej o obsługiwanych scenariuszach i wymagania dotyczące konfigurowania uwierzytelniania opartego na certyfikatach w rozwiązaniach z urządzeń z systemem Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: b55b439f61c76d6d0524c1f01ba5fef745187d04
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368293"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory na podstawie certyfikatu uwierzytelniania w systemie Android

Urządzenia z systemem android można użyć uwierzytelniania opartego na certyfikatach (CBA) do uwierzytelniania w usłudze Azure Active Directory przy użyciu certyfikatu klienta na swoim urządzeniu podczas nawiązywania połączenia:

* Aplikacje mobilne pakietu Office, takich jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Konfiguruje tę funkcję, eliminuje konieczność wprowadzania kombinację nazwy użytkownika i hasła w niektórych poczty i aplikacje Microsoft Office na urządzeniu przenośnym.

W tym temacie przedstawiono wymagania i obsługiwane scenariusze dotyczące konfigurowania uwierzytelniania CBA na urządzeniu z systemem iOS(Android) dla użytkowników dzierżaw w Office 365 Enterprise, Business, edukacja, instytucji rządowych USA, Chiny i Niemcy planów.

Ta funkcja jest dostępna w wersji zapoznawczej w planach usług Office 365 dla instytucji rządowych Obrony USA i agencje federalne.

## <a name="microsoft-mobile-applications-support"></a>Obsługa aplikacji mobilnych firmy Microsoft

| Aplikacje | Pomoc techniczna |
| --- | --- |
| Usługa Azure Information Protection app |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| Portal firmy usługi Intune |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| Microsoft Teams |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| OneNote |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| OneDrive |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| Outlook |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| Power BI |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| Skype dla firm |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| Word / Excel / PowerPoint |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |
| Yammer |![Znacznik wyboru oznaczającą pomocy technicznej dla tej aplikacji][1] |

### <a name="implementation-requirements"></a>Wymagania dotyczące implementacji

Wersja systemu operacyjnego urządzenia musi być Android 5.0 (Lollipop) i nowszych.

Serwer federacyjny musi być skonfigurowany.

Usługi Azure Active Directory odwołać certyfikat klienta tokenu usług AD FS musi mieć następujące oświadczeń:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Numer seryjny certyfikatu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (String dla wystawcy certyfikatu klienta)

Usługa Azure Active Directory dodaje te oświadczenia na token odświeżania, jeśli są one dostępne w tokenu usług AD FS (lub inne tokenu SAML). Gdy token odświeżania musi być weryfikowane, te informacje służy do sprawdzania odwołania.

Najlepszym rozwiązaniem należy zaktualizować strony błędów usług AD FS w organizacji z następującymi informacjami:

* Wymagania dotyczące instalowania Microsoft Authenticator w systemie Android.
* Instrukcje dotyczące sposobu uzyskania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [dostosowywania stron AD FS Zaloguj](https://technet.microsoft.com/library/dn280950.aspx).

Wyślij niektóre aplikacje pakietu Office (z włączoną nowoczesnego uwierzytelniania) "*= monit logowania*" do usługi Azure AD w żądaniu. Domyślnie program Azure AD tłumaczy "*= monit logowania*"w żądaniu usług AD FS jako"*wauth = usernamepassworduri*" (prosi usług AD FS w celu uwierzytelnienia użytkownik/hasło) i "*wfresh = 0*" (prosi usług AD FS do Ignoruj stanu logowania jednokrotnego i wykonać świeże uwierzytelnianie). Aby włączyć uwierzytelnianie oparte na certyfikatach dla tych aplikacji, należy zmodyfikować domyślne zachowanie usługi Azure AD. Ustaw "*PromptLoginBehavior*"w ustawieniach domeny federacyjnej do"*wyłączone*".
Możesz użyć [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) polecenia cmdlet do wykonania tego zadania:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Obsługa klientów programu Exchange ActiveSync

Niektóre aplikacje programu Exchange ActiveSync w systemie Android 5.0 (Lollipop) lub nowszej są obsługiwane. Aby określić, jeśli aplikacja poczty e-mail obsługuje tę funkcję, skontaktuj się z deweloperem aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz skonfigurować uwierzytelnianie oparte na certyfikatach w danym środowisku, zobacz [wprowadzenie do uwierzytelniania opartego na certyfikatach w systemie Android](active-directory-certificate-based-authentication-get-started.md) instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
