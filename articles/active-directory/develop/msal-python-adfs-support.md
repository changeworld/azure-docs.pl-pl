---
title: Obsługa usługi Azure AD FS (MSAL Python)
titleSuffix: Microsoft identity platform
description: Informacje o obsłudze usług federacyjnych Active Directory (AD FS) w bibliotece uwierzytelniania firmy Microsoft dla języka Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699550"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Obsługa usług federacyjnych Active Directory w języku MSAL dla języka Python

Usługi federacyjne Active Directory (AD FS) w systemie Windows Server umożliwiają dodawanie uwierzytelniania i autoryzacji opartej na usłudze OpenID Connect i OAuth 2.0 do aplikacji przy użyciu biblioteki uwierzytelniania Firmy Microsoft (MSAL) dla języka Python. Za pomocą biblioteki MSAL dla języka Python aplikacja może uwierzytelniać użytkowników bezpośrednio względem usług AD FS. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [Scenariusze usług AD FS dla deweloperów](/windows-server/identity/ad-fs/ad-fs-development).

Zazwyczaj istnieją dwa sposoby uwierzytelniania względem usług AD FS:

- Program MSAL Python prowadzi rozmowy z usługą Azure Active Directory, która sama jest sfederowana z innymi dostawcami tożsamości. Federacja odbywa się za pośrednictwem usług AD FS. Msal Python łączy się z usługą Azure AD, która loguje się do użytkowników zarządzanych w usłudze Azure AD (zarządzanych użytkowników) lub użytkowników zarządzanych przez innego dostawcę tożsamości, takiego jak AD FS (użytkownicy federowani). MSAL Python nie wie, że użytkownik jest sfederowany. Po prostu rozmawia z usługą Azure AD. [Urząd](msal-client-application-configuration.md#authority) używany w tym przypadku jest zwykłym organem (nazwa hosta urzędu + dzierżawca, wspólne lub organizacje).
- Program MSAL Python prowadzi rozmowy bezpośrednio z urzędem usług AD FS. Jest to obsługiwane tylko przez usługi AD FS 2019 i nowsze.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Łączenie się z usługą Active Directory z usługą AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Uzyskaj token interaktywnie dla użytkownika federacyjnego

Poniżej przedstawiono, czy łączysz się bezpośrednio z usługami federacyjnymi Active Directory (AD FS), czy za pośrednictwem usługi Active Directory.

Podczas połączenia `acquire_token_by_authorization_code` `acquire_token_by_device_flow`lub , środowisko użytkownika jest zazwyczaj w następujący sposób:

1. Użytkownik wprowadzi swój identyfikator konta.
2. Usługa Azure AD wyświetla krótko komunikat "Zawładniesz do strony organizacji", a użytkownik zostanie przekierowany do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowywana za pomocą logo organizacji.

Obsługiwane wersje usług AD FS w tym scenariuszu federacyjne są następujące:
- Usługi federacyjne Active Directory FS w wersji 2
- Usługi federacyjne Active Directory w wersji 3 (Windows Server 2012 R2)
- Usługi federacyjne Active Directory w wersji 4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Zdobądź token za pomocą nazwy użytkownika i hasła

Poniżej przedstawiono, czy łączysz się bezpośrednio z usługami federacyjnymi Active Directory (AD FS), czy za pośrednictwem usługi Active Directory.

Po nabyciu `acquire_token_by_username_password`tokenu przy użyciu programu MSAL Python pobiera dostawcę tożsamości do kontaktu na podstawie nazwy użytkownika. MSAL Python pobiera [token SAML 1.1](reference-saml-tokens.md) od dostawcy tożsamości, który następnie udostępnia do usługi Azure AD, która zwraca token json web token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Łączenie się bezpośrednio z usługą AD FS

Po podłączeniu katalogu do usług AD FS urząd, którego chcesz użyć do utworzenia aplikacji, będzie czymś w rodzaju`https://somesite.contoso.com/adfs/`

Protokół MSAL Python obsługuje usługę ADFS 2019.

Nie obsługuje bezpośredniego połączenia z usługą ADFS 2016 lub ADFS v2. Jeśli potrzebujesz obsługi scenariuszy wymagających bezpośredniego połączenia z usługą ADFS 2016, użyj najnowszej wersji programu ADAL Python. Po uaktualnieniu systemu lokalnego do systemu ADFS 2019 można użyć programu MSAL Python.

## <a name="next-steps"></a>Następne kroki

- W przypadku federacyjnego zobacz [Konfigurowanie zachowania logowania usługi Azure Active Directory dla aplikacji przy użyciu zasad odnajdowania obszaru macierzystego](../manage-apps/configure-authentication-for-federated-users-portal.md)