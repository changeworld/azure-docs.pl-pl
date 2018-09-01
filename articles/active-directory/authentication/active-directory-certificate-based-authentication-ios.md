---
title: Azure Active Directory na podstawie certyfikatu uwierzytelniania w systemie iOS
description: Dowiedz się więcej o obsługiwanych scenariuszach i wymagania dotyczące konfigurowania uwierzytelniania opartego na certyfikatach w rozwiązaniach z urządzeń z systemem iOS
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: 655fa6b4bf0f04f2d88e9a3f11cb9d3917ea3dd3
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345195"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory na podstawie certyfikatu uwierzytelniania w systemie iOS

urządzenia z systemem iOS można użyć uwierzytelniania opartego na certyfikatach (CBA) do uwierzytelniania w usłudze Azure Active Directory przy użyciu certyfikatu klienta na swoim urządzeniu podczas nawiązywania połączenia:

* Aplikacje mobilne pakietu Office, takich jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Konfiguruje tę funkcję, eliminuje konieczność wprowadzania kombinację nazwy użytkownika i hasła w niektórych poczty i aplikacje Microsoft Office na urządzeniu przenośnym.

W tym temacie przedstawiono wymagania i obsługiwane scenariusze dotyczące konfigurowania uwierzytelniania CBA na urządzeniu z systemem iOS(Android) dla użytkowników dzierżaw w Office 365 Enterprise, Business, edukacja, instytucji rządowych USA, Chiny i Niemcy planów.

Ta funkcja jest dostępna w wersji zapoznawczej w planach usług Office 365 dla instytucji rządowych Obrony USA i agencje federalne.

## <a name="microsoft-mobile-applications-support"></a>Obsługa aplikacji mobilnych firmy Microsoft

| Aplikacje | Pomoc techniczna |
| --- | --- |
| Usługa Azure Information Protection app |![Zaznaczenie][1] |
| Portal firmy usługi Intune |![Zaznaczenie][1] |
| Microsoft Teams |![Zaznaczenie][1] |
| OneNote |![Zaznaczenie][1] |
| OneDrive |![Zaznaczenie][1] |
| Outlook |![Zaznaczenie][1] |
| Power BI |![Zaznaczenie][1] |
| Skype dla firm |![Zaznaczenie][1] |
| Word / Excel / PowerPoint |![Zaznaczenie][1] |
| Yammer |![Zaznaczenie][1] |

## <a name="requirements"></a>Wymagania

Wersja systemu operacyjnego urządzenia musi mieć system iOS 9 i nowsze wersje

Serwer federacyjny musi być skonfigurowany.

Microsoft Authenticator jest wymagana dla aplikacji pakietu Office w systemie iOS.

Usługi Azure Active Directory odwołać certyfikat klienta tokenu usług AD FS musi mieć następujące oświadczeń:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Numer seryjny certyfikatu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (String dla wystawcy certyfikatu klienta)

Usługa Azure Active Directory dodaje te oświadczenia na token odświeżania, jeśli są one dostępne w tokenu usług AD FS (lub inne tokenu SAML). Gdy token odświeżania musi być weryfikowane, te informacje służy do sprawdzania odwołania.

Najlepszym rozwiązaniem należy zaktualizować strony błędów usług AD FS w organizacji z następującymi informacjami:

* Wymagania dotyczące instalowania Microsoft Authenticator w systemie iOS
* Instrukcje dotyczące sposobu uzyskania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [dostosowywania stron AD FS Zaloguj](https://technet.microsoft.com/library/dn280950.aspx).

Wyślij niektóre aplikacje pakietu Office (z włączoną nowoczesnego uwierzytelniania) "*= monit logowania*" do usługi Azure AD w żądaniu. Domyślnie program Azure AD tłumaczy "*= monit logowania*"w żądaniu usług AD FS jako"*wauth = usernamepassworduri*" (prosi usług AD FS w celu uwierzytelnienia użytkownik/hasło) i "*wfresh = 0*" (prosi usług AD FS do Ignoruj stanu logowania jednokrotnego i wykonać świeże uwierzytelnianie). Aby włączyć uwierzytelnianie oparte na certyfikatach dla tych aplikacji, należy zmodyfikować domyślne zachowanie usługi Azure AD. Ustaw wartość "*PromptLoginBehavior*"w ustawieniach domeny federacyjnej do"*wyłączone*".
Możesz użyć [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) polecenia cmdlet do wykonania tego zadania:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Obsługa klientów programu Exchange ActiveSync

W systemie iOS 9 lub nowszy klient poczty natywnych dla systemów iOS jest obsługiwany. Dla wszystkich innych aplikacji Exchange ActiveSync Aby ustalić, czy ta funkcja jest obsługiwana, skontaktuj się z usługi dla deweloperów aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz skonfigurować uwierzytelnianie oparte na certyfikatach w danym środowisku, zobacz [wprowadzenie do uwierzytelniania opartego na certyfikatach w systemie Android](../authentication/active-directory-certificate-based-authentication-get-started.md) instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
