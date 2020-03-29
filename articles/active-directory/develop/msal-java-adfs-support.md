---
title: Obsługa usług AD FS (MSAL dla języka Java)
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o obsłudze usług federacyjnych Active Directory (AD FS) w bibliotece uwierzytelniania firmy Microsoft dla języka Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696218"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Obsługa usług federacyjnych Active Directory w języku MSAL dla języka Java

Usługi federacyjne Active Directory (AD FS) w systemie Windows Server umożliwiają dodawanie uwierzytelniania i autoryzacji opartej na usłudze OpenID Connect i OAuth 2.0 do biblioteki uwierzytelniania firmy Microsoft dla aplikacji Java (MSAL for Java). Po zintegrowaniu aplikacja może uwierzytelniać użytkowników w usługach AD FS, sfederowanych za pośrednictwem usługi Azure AD. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [Scenariusze usług AD FS dla deweloperów](/windows-server/identity/ad-fs/ad-fs-development).

Aplikacja korzystająca z usługi MSAL dla języka Java będzie rozmawiać z usługą Azure Active Directory (Azure AD), która następnie jest przekazywać do usług AD FS.

Msal dla języka Java łączy się z usługą Azure AD, która loguje się do użytkowników zarządzanych w usłudze Azure AD (zarządzanych użytkowników) lub użytkowników zarządzanych przez innego dostawcę tożsamości, takiego jak AD FS (użytkownicy federowani). MSAL dla języka Java nie wie, że użytkownik jest sfederowany. Po prostu rozmawia z usługą Azure AD.

[Urząd](msal-client-application-configuration.md#authority) używany w tym przypadku jest zwykłym organem (nazwa hosta urzędu + dzierżawca, wspólne lub organizacje).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Uzyskaj token interaktywnie dla użytkownika federacyjnego

Podczas rozmowy `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` lub `AuthorizationCodeParameters` `DeviceCodeParameters`z lub , doświadczenie użytkownika jest zazwyczaj:

1. Użytkownik wprowadzi swój identyfikator konta.
2. Usługa Azure AD krótko wyświetla "Zawładniesz do strony organizacji", a użytkownik zostanie przekierowany do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowywana za pomocą logo organizacji.

Obsługiwane wersje usług AD FS w tym scenariuszu federacyjne są następujące:
- Usługi federacyjne Active Directory FS w wersji 2
- Usługi federacyjne Active Directory w wersji 3 (Windows Server 2012 R2)
- Usługi federacyjne Active Directory w wersji 4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Zdobądź token za pomocą nazwy użytkownika i hasła

Po nabyciu `ConfidentialClientApplication.AcquireToken()` tokenu przy użyciu lub `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` lub `UsernamePasswordParameters`, MSAL dla języka Java pobiera dostawcy tożsamości do kontaktu na podstawie nazwy użytkownika. Msal dla języka Java pobiera token [saml 1.1 token](reference-saml-tokens.md) od dostawcy tożsamości, który następnie zapewnia do usługi Azure AD, która zwraca token JSON Web Token (JWT).

## <a name="next-steps"></a>Następne kroki

W przypadku federacyjnego zobacz [Konfigurowanie zachowania logowania usługi Azure Active Directory dla aplikacji przy użyciu zasad odnajdowania obszaru macierzystego](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)