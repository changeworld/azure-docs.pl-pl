---
title: Obsługa AD FS w bibliotece uwierzytelniania firmy Microsoft dla języka Java
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat obsługi Active Directory Federation Services (AD FS) w bibliotece uwierzytelniania firmy Microsoft dla języka Java (MSAL4j).
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c286ed7467560c90f9cf9594e75af06d6cdcc5d1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482090"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Obsługa Active Directory Federation Services w programie MSAL for Java

Active Directory Federation Services (AD FS) w systemie Windows Server umożliwia dodawanie uwierzytelniania i autoryzacji na podstawie OpenID Connect Connect i OAuth 2,0 do biblioteki uwierzytelniania firmy Microsoft dla języka Java (MSAL for Java). Po zintegrowaniu aplikacja może uwierzytelniać użytkowników w AD FS federacyjnych za pomocą usługi Azure AD. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [AD FS scenariusze dla deweloperów](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Aplikacja, która używa MSAL for Java, będzie komunikować się z Azure Active Directory (Azure AD), a następnie tworzy federację do AD FS.

MSAL for Java nawiązuje połączenie z usługą Azure AD, co oznacza, że użytkownicy, którzy są zarządzani za pomocą usługi Azure AD (zarządzani użytkownicy) lub Użytkownicy zarządzani przez innego dostawcę tożsamości, na przykład AD FS (użytkowników federacyjnych). MSAL for Java nie wie, że użytkownik jest federacyjny. Po prostu rozmowy z usługą Azure AD.

[Urząd](msal-client-application-configuration.md#authority) , którego używasz w tym przypadku, jest zazwyczaj urzędem certyfikacji (nazwa hosta urzędu + dzierżawca, typowe lub organizacje).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Interaktywny pozyskiwanie tokenu dla użytkownika federacyjnego

Podczas wywoływania `ConfidentialClientApplication.AcquireToken()` lub `PublicClientApplication.AcquireToken()` z `AuthorizationCodeParameters` lub `DeviceCodeParameters`środowisko użytkownika jest zwykle:

1. Użytkownik wprowadza swój identyfikator konta.
2. Usługa Azure AD krótko wyświetli "przejście do strony organizacji" i użytkownik zostanie przekierowany do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowana do logo organizacji.

Obsługiwane wersje AD FS w tym scenariuszu federacyjnym są następujące:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Uzyskiwanie tokenu za pomocą nazwy użytkownika i hasła

Po pozyskaniu tokenu przy użyciu `ConfidentialClientApplication.AcquireToken()` lub `PublicClientApplication.AcquireToken()` z `IntegratedWindowsAuthenticationParameters` lub `UsernamePasswordParameters`, MSAL for Java pobiera dostawcę tożsamości do kontaktu na podstawie nazwy użytkownika. MSAL for Java pobiera token [tokenu SAML 1,1](reference-saml-tokens.md) od dostawcy tożsamości, który następnie udostępnia usługę Azure AD, która zwraca token sieci Web JSON (JWT).

## <a name="next-steps"></a>Następne kroki

W przypadku federacyjnego przypadku należy zapoznać się z tematem [Konfigurowanie zachowania logowania Azure Active Directory aplikacji przy użyciu zasad odnajdywania obszaru głównego](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) .