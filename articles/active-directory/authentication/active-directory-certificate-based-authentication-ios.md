---
title: Uwierzytelnianie oparte na certyfikatach w systemie iOS — Azure Active Directory
description: Poznaj obsługiwane scenariusze i wymagania dotyczące konfigurowania uwierzytelniania opartego na certyfikatach w rozwiązaniach z urządzeniami z systemem iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f9e7d71ab660c4df6f65d6bebe1d3854086bdd
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848803"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory uwierzytelniania opartego na certyfikatach w systemie iOS

urządzenia z systemem iOS mogą uwierzytelniać się za pomocą uwierzytelniania opartego na certyfikatach (OBSŁUGIWANIE) w celu Azure Active Directory przy użyciu certyfikatu klienta na urządzeniu podczas nawiązywania połączenia z:

* Aplikacje mobilne pakietu Office, takie jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Skonfigurowanie tej funkcji eliminuje konieczność wprowadzenia kombinacji nazwy użytkownika i hasła do określonych aplikacji poczty i Microsoft Office na urządzeniu przenośnym.

Ten temat zawiera wymagania i obsługiwane scenariusze dotyczące konfigurowania OBSŁUGIWANIE na urządzeniu z systemem iOS (Android) dla użytkowników dzierżaw w pakiecie Office 365 Enterprise, Business, Education, USA, Chinach i Niemczech.

Ta funkcja jest dostępna w wersji zapoznawczej w pakiecie Office 365 Stany obrony i plany federalne dla instytucji rządowych USA.

## <a name="microsoft-mobile-applications-support"></a>Obsługa aplikacji mobilnych firmy Microsoft

| Aplikacje | Pomoc techniczna |
| --- | --- |
| Aplikacja Azure Information Protection |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Portal firmy usługi Intune |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Microsoft Teams |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| OneNote |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| OneDrive |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Outlook |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Power BI |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Skype dla firm |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Word/Excel/PowerPoint |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Yammer |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |

## <a name="requirements"></a>Wymagania

Wersja systemu operacyjnego urządzenia musi działać pod kontrolą systemu iOS 9 lub nowszego

Należy skonfigurować serwer federacyjny.

W przypadku aplikacji pakietu Office w systemie iOS jest wymagane Microsoft Authenticator.

Aby Azure Active Directory odwołać certyfikat klienta, token ADFS musi mieć następujące oświadczenia:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (numer seryjny certyfikatu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (ciąg dla wystawcy certyfikatu klienta)

Azure Active Directory dodaje te oświadczenia do tokenu odświeżania, jeśli są dostępne w tokenie ADFS (lub dowolnym innym tokenie SAML). Gdy token odświeżania musi być zweryfikowany, te informacje są używane do sprawdzania odwołania.

Najlepszym rozwiązaniem jest zaktualizowanie stron błędów organizacji ADFS z następującymi informacjami:

* Wymagania dotyczące instalowania Microsoft Authenticator w systemie iOS
* Instrukcje dotyczące pobierania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie stron logowania AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Niektóre aplikacje pakietu Office (z włączonym współczesnym uwierzytelnianiem) wysyłają jako żądanie "*Prompt = login*" do usługi Azure AD. Domyślnie usługa Azure AD tłumaczy wartość "*Prompt = login*" w żądaniu na usługi AD FS jako "*wauth = usernamepassworduri*" (żąda od usług AD FS uwierzytelniania U/P) i "*wfresh = 0*" (poprosi ADFS o zignorowanie stanu logowania jednokrotnego i przeprowadzenie nowego uwierzytelniania). Jeśli chcesz włączyć uwierzytelnianie oparte na certyfikacie dla tych aplikacji, musisz zmodyfikować domyślne zachowanie usługi Azure AD. Po prostu ustaw wartość "*PromptLoginBehavior*" w ustawieniach domeny federacyjnej na wartość "*Disabled*".
Aby wykonać to zadanie, można użyć polecenia cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) :

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Obsługa klientów programu Exchange ActiveSync

W systemie iOS 9 lub nowszym jest obsługiwany natywny klient poczty systemu iOS. Aby określić, czy ta funkcja jest obsługiwana, należy skontaktować się z deweloperem aplikacji w przypadku wszystkich innych aplikacji programu Exchange ActiveSync.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz skonfigurować uwierzytelnianie oparte na certyfikacie w środowisku, zobacz Wprowadzenie do [uwierzytelniania opartego na certyfikatach w systemie Android](../authentication/active-directory-certificate-based-authentication-get-started.md) , aby uzyskać instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
