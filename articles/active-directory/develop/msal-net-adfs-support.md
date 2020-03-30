---
title: Obsługa usług AD FS w MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o obsłudze usług federacyjnych Active Directory (AD FS) w bibliotece uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160764"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Obsługa usług federacyjnych Active Directory w MSAL.NET
Usługi federacyjne Active Directory (AD FS) w systemie Windows Server umożliwiają dodawanie uwierzytelniania i autoryzacji opartej na usłudze OpenID Connect i OAuth 2.0 do opracowywanych aplikacji. Aplikacje te mogą zatem uwierzytelniać użytkowników bezpośrednio w przypadku usług AD FS. Aby uzyskać więcej informacji, przeczytaj [scenariusze usług AD FS dla deweloperów](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Biblioteka uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) obsługuje dwa scenariusze uwierzytelniania względem usług AD FS:

- MSAL.NET rozmowy z usługą Azure Active Directory, która sama jest *sfederowana* z usługami AD FS.
- MSAL.NET rozmawia **bezpośrednio** z urzędem ADFS. Jest to obsługiwane tylko z usług AD FS 2019 i powyżej. Jednym ze scenariuszy, które podkreślają, jest obsługa [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>Msal łączy się z usługą Azure AD, która jest sfederowana z usługami AD FS
MSAL.NET obsługuje łączenie się z usługą Azure AD, która loguje się do użytkowników zarządzanych (użytkowników zarządzanych w usłudze Azure AD) lub użytkowników federacyjnych (użytkowników zarządzanych przez innego dostawcę tożsamości, takiego jak usługi AD FS). MSAL.NET nie wie o tym, że użytkownicy są sfederowani. Jeśli chodzi o to, że rozmowy z usługą Azure AD.

[Urząd](msal-client-application-configuration.md#authority) używany w tym przypadku jest zwykłym organem (nazwa hosta urzędu + dzierżawca, wspólne lub organizacje).

### <a name="acquiring-a-token-interactively"></a>Interaktywne pozyskiwanie tokenu
Po wywołaniu `AcquireTokenInteractive` metody, środowisko użytkownika jest zazwyczaj:

1. Użytkownik wprowadzi swój identyfikator konta.
2. Usługa Azure AD wyświetla krótko komunikat "Zawładniesz do strony organizacji".
3. Użytkownik jest przekierowywał do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowywana za pomocą logo organizacji.

Obsługiwane wersje usług AD FS w tym sfederowanym scenariuszu to AD FS w wersji 2, AD FS w 3 (Windows Server 2012 R2) i AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Pobieranie tokenu przy użyciu acquireTokenByIntegratedAuthentication lub AcquireTokenByUsernamePassword
Podczas uzyskiwania `AcquireTokenByIntegratedAuthentication` tokenu `AcquireTokenByUsernamePassword` przy użyciu lub metody, MSAL.NET pobiera dostawcy tożsamości do kontaktu na podstawie nazwy użytkownika.  MSAL.NET odbiera [token SAML 1.1](reference-saml-tokens.md) po skontaktowaniu się z dostawcą tożsamości.  MSAL.NET następnie udostępnia token SAML do usługi Azure AD jako potwierdzenia użytkownika (podobne do [przepływu w imieniu),](msal-authentication-flows.md#on-behalf-of)aby odzyskać JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL łączy się bezpośrednio z usługami AD FS
MSAL.NET obsługuje łączenie się z usługą AD FS 2019, która jest zgodna z open id connect i rozumie PKCE i zakresy. Ta obsługa wymaga zastosowania dodatku Service Pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) do systemu Windows Server. Podczas łączenia się bezpośrednio z usługami AD FS urząd, którego `https://mysite.contoso.com/adfs/`chcesz użyć do tworzenia aplikacji, jest podobny do .

Obecnie nie ma planów obsługi bezpośredniego połączenia z:

- Usługi AD FS 16, ponieważ nie obsługuje pkce i nadal korzysta z zasobów, a nie zakresu
- Usługi AD FS w wersji 2, która nie jest zgodna z oidc.

 Jeśli potrzebujesz obsługi scenariuszy wymagających bezpośredniego połączenia z usługami AD FS 2016, użyj najnowszej wersji [biblioteki uwierzytelniania usługi Azure Active Directory](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Po uaktualnieniu systemu lokalnego do usług AD FS 2019 będzie można używać MSAL.NET.

## <a name="next-steps"></a>Następne kroki

W przypadku federacyjnego zobacz [Konfigurowanie zachowania logowania usługi Azure Active Directory dla aplikacji przy użyciu zasad odnajdowania obszaru macierzystego](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
