---
title: Usługi AD FS obsługuje w Biblioteka Microsoft Authentication Library dla platformy .NET | Azure
description: Dowiedz się więcej o obsłudze usługi Active Directory Federation Services (AD FS) w Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676731"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Usługi federacyjne Active Directory obsługuje w platformy MSAL.NET
Active Directory Federation Services (AD FS) w systemie Windows Server umożliwia dodawanie, OpenID Connect i OAuth 2.0 na podstawie uwierzytelniania i autoryzacji dla aplikacji, które tworzysz, a te aplikacje uwierzytelniania użytkowników bezpośrednio przed usług AD FS. Aby uzyskać więcej informacji, przeczytaj [usługi AD FS scenariusze dla deweloperów](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET) obsługuje dwa scenariusze uwierzytelniania w odniesieniu do usług AD FS:

- Platformy MSAL.NET rozmowy w usłudze Azure Active Directory, która sama jest *federacyjnych* z usługami AD FS.
- Rozmowy platformy MSAL.NET *bezpośrednio* dla urzędu usług AD FS, gdzie jest wersją usług AD FS OpenID Connect zgodne (począwszy od usługi AD FS 2019). Połączenie bezpośrednio z usług AD FS umożliwia platformy MSAL.NET do uwierzytelniania za pomocą aplikacji działających w [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>Biblioteka MSAL nawiązuje połączenie z usługą Azure AD, która jest Sfederowane przy użyciu usług AD FS
Platformy MSAL.NET obsługuje łączenie z usługą Azure AD, który loguje się zarządzanych użytkowników (użytkownicy zarządzanych w usłudze Azure AD) lub federacyjnych użytkowników (użytkownicy zarządzana przez innego dostawcy tożsamości, takich jak usługi AD FS). Platformy MSAL.NET nie wiedzieć o tym, że użytkownicy są federacyjne. O ile dotyczy, jego rozmawia z usługi Azure AD.

[Urząd](msal-client-application-configuration.md#authority) możesz korzystanie w tym przypadku jest zwykle urzędu (nazwa hosta urzędu + dzierżawy, typowe lub organizacji).

### <a name="acquiring-a-token-interactively"></a>Uzyskiwanie tokenu interaktywnie
Gdy wywołujesz `AcquireTokenInteractive` metody środowisko użytkownika jest zazwyczaj:

1. Użytkownik wprowadza swojego identyfikatora konta.
2. Usługa Azure AD krótko wyświetla komunikat "Przenosimy Cię na stronę Twojej organizacji".
3. Użytkownik jest przekierowany do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowane logo organizacji.

Obsługiwane wersje usług AD FS, w tym scenariuszu federacyjnych są usług AD FS v2, v3 usług AD FS (system Windows Server 2012 R2) i usług AD FS w wersji 4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Pobieranie tokenu przy użyciu AcquireTokenByIntegratedAuthentication lub AcquireTokenByUsernamePassword
Podczas uzyskiwania tokenu przy użyciu `AcquireTokenByIntegratedAuthentication` lub `AcquireTokenByUsernamePassword` metod platformy MSAL.NET pobiera dostawcę tożsamości do kontaktowania się z oparciu o nazwę użytkownika.  Odbiera platformy MSAL.NET [tokenu języka SAML 1.1](reference-saml-tokens.md) po skontaktowaniu się z dostawcy tożsamości.  Następnie platformy MSAL.NET zapewnia SAML token do usługi Azure AD jako potwierdzenie użytkownika (podobnie jak [przepływu w imieniu z](msal-authentication-flows.md#on-behalf-of)) odzyskać token JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>Biblioteka MSAL łączy się bezpośrednio z usług AD FS
Platformy MSAL.NET obsługuje łączenie z usługą AD FS 2019 r, czyli Open ID Connect zgodne. Podczas nawiązywania połączenia z bezpośrednio z usługami AD FS, urząd, będziesz chciał użyć do skompilowania aplikacji jest podobny do `https://mysite.contoso.com/adfs/`.

Obecnie nie ma żadnych planów, umożliwiających bezpośrednie połączenie usług AD FS 2016 lub usług AD FS w wersji 2, (które nie OpenID Connect zgodne). Jeśli potrzebujesz do obsługi scenariuszy konieczności bezpośredniego połączenia z usługą AD FS 2016, użyj najnowszej wersji [usługi Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Po uaktualnieniu systemu lokalnego do usługi AD FS 2019 będzie można użyć platformy MSAL.NET.
