---
title: Przewodnik migracji biblioteki ADAL do MSAL dla języka Java | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji Java biblioteki Azure Active Directory Authentication Library (ADAL) do biblioteki uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: sangonzal
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/4/2019
ms.author: sagonzal
ms.reviewer: navyari.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 158cbe4f2598722abe3b2ed3a0dd179a21dc1e1c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145274"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Przewodnik migracji biblioteki ADAL do MSAL dla języka Java

W tym artykule przedstawiono zmiany, które należy wykonać w celu przeprowadzenia migracji aplikacji, która Azure Active Directory używa biblioteki MSAL Authentication Library (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft.

Zarówno Biblioteka uwierzytelniania firmy Microsoft dla języka Java (MSAL4J), jak i Biblioteka Azure AD Authentication Library for Java (ADAL4J) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD for Developers platform (v 1.0) do uwierzytelniania tożsamości usługi Azure AD (konta służbowe) przez żądanie tokenów przy użyciu biblioteki Azure AD Authentication Library (ADAL).

MSAL oferuje następujące korzyści:

- Ponieważ używa ona nowszego punktu końcowego platformy tożsamości firmy Microsoft, można uwierzytelnić szerszy zestaw tożsamości firmy Microsoft, takich jak tożsamości usługi Azure AD, konta Microsoft i konta społecznościowe i lokalne za pośrednictwem usługi Azure AD Business to Consumer (B2C).
- Użytkownicy będą korzystać z najlepszego środowiska logowania jednokrotnego.
- Aplikacja może włączyć przyrostową zgodę i ułatwić dostęp warunkowy.

MSAL for Java (MSAL4J) jest biblioteką uwierzytelniania zalecaną do użycia z platformą tożsamości firmy Microsoft. Żadne nowe funkcje nie zostaną zaimplementowane w systemie ADAL4J. Wszystkie wysiłki w przód są skoncentrowane na ulepszaniu MSAL.

## <a name="differences"></a>Wynikających

Jeśli pracujesz z punktem końcowym usługi Azure AD dla deweloperów (i ADAL4J), warto zapoznać się z [informacjami o tym, co się dzieje w punkcie końcowym platformy Identity platform (v 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Zakresy nie są zasobami

ADAL4J uzyskuje tokeny dla zasobów, a MSAL4J uzyskuje tokeny dla zakresów. Liczba klas MSAL4J wymaga parametru Scopes. Ten parametr jest listą ciągów, które deklarują żądane uprawnienia i żądane zasoby. Zobacz [zakresy Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) , aby zobaczyć przykładowe zakresy.

## <a name="core-classes"></a>Klasy podstawowe

W ADAL4J Klasa `AuthenticationContext` reprezentuje połączenie z usługą tokenu zabezpieczającego (STS) lub serwerem autoryzacji za pomocą urzędu. Jednak MSAL4J jest przeznaczony dla aplikacji klienckich. Oferuje dwie osobne klasy: `PublicClientApplication` i `ConfidentialClientApplication` do reprezentowania aplikacji klienckich.  Ten ostatni `ConfidentialClientApplication`reprezentuje aplikację, która została zaprojektowana w celu bezpiecznego utrzymania hasła, takiego jak identyfikator aplikacji dla aplikacji demona.

W poniższej tabeli przedstawiono, jak funkcje ADAL4J są mapowane na nowe funkcje MSAL4J:

| ADAL4J, Metoda| MSAL4J, Metoda|
|------|-------|
|acquireToken (zasób ciągu, obiekt ClientCredential, AuthenticationCallback wywołanie zwrotne) | acquireToken(ClientCredentialParameters)|
|acquireToken (zasób ciągu, potwierdzenie ClientAssertion, AuthenticationCallback wywołania zwrotne)|acquireToken(ClientCredentialParameters)|
|acquireToken (zasób ciągu, poświadczenia AsymmetricKeyCredential, wywołanie zwrotne AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken (ciąg zasobu, ciąg clientId, nazwa użytkownika ciągu, hasło ciągu, wywołanie zwrotne AuthenticationCallback)| acquireToken(UsernamePasswordParameters)|
|acquireToken (ciąg Resource, String clientId, String username, String Password = null, AuthenticationCallback callback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (ciąg zasobu, UserAssertion userAssertion, obiekt ClientCredential Credential, AuthenticationCallback wywołania zwrotnego)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () i acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount zamiast IUser

ADAL4J manipulować użytkownikami. Chociaż użytkownik reprezentuje jednego agenta człowieka lub oprogramowania, może mieć co najmniej jedno konto w systemie tożsamości firmy Microsoft. Na przykład użytkownik może mieć kilka kont usługi Azure AD, Azure AD B2C lub Microsoft Personal.

MSAL4J definiuje koncepcję konta za pośrednictwem interfejsu `IAccount`. Jest to istotna zmiana z ADAL4J, ale jest to dobre, ponieważ przechwytuje fakt, że ten sam użytkownik może mieć kilka kont, a nawet w różnych katalogach usługi Azure AD. Usługa MSAL4J zapewnia lepsze informacje w scenariuszach gościa, ponieważ są udostępniane informacje o kontach domowych.

## <a name="cache-persistence"></a>Trwałość pamięci podręcznej

ADAL4J nie obsługiwał pamięci podręcznej tokenów.
MSAL4J dodaje [pamięć podręczną tokenów](msal-acquire-cache-tokens.md) , aby uprościć zarządzanie okresami istnienia tokenu przez automatyczne odświeżenie wygasłych tokenów, gdy jest to możliwe, i uniemożliwienie niepotrzebnych wyświetleń przez użytkownika poświadczeń

## <a name="common-authority"></a>Wspólny urząd

W wersji 1.0, jeśli używasz urzędu `https://login.microsoftonline.com/common`, użytkownicy mogą logować się przy użyciu dowolnego konta usługi Azure Active Directory (AAD) (dla dowolnej organizacji).

Jeśli używasz urzędu `https://login.microsoftonline.com/common` w wersji 2.0, użytkownicy mogą logować się przy użyciu dowolnej organizacji usługi AAD, a nawet konta osobistego firmy Microsoft (MSA). Jeśli chcesz ograniczyć logowanie do dowolnego konta usługi AAD w MSAL4J, musisz użyć urzędu `https://login.microsoftonline.com/organizations` (to samo zachowanie jest takie samo jak w przypadku programu ADAL4J). Aby określić urząd, należy ustawić parametr `authority` w metodzie [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) podczas tworzenia klasy `PublicClientApplication`.

## <a name="v10-and-v20-tokens"></a>tokeny w wersji 1.0 i 2.0

Punkt końcowy v 1.0 (używany przez ADAL) emituje tylko tokeny w wersji 1.0.

Punkt końcowy v 2.0 (używany przez MSAL) może emitować tokeny v 1.0 i v 2.0. Właściwość manifestu aplikacji internetowego interfejsu API pozwala deweloperom wybrać, która wersja tokenu jest akceptowana. Zobacz `accessTokenAcceptedVersion` w dokumentacji referencyjnej [manifestu aplikacji](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) .

Aby uzyskać więcej informacji na temat tokenów v 1.0 i v 2.0, zobacz [Azure Active Directory tokeny dostępu](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>Migracja biblioteki ADAL do MSAL

W programie ADAL4J tokeny odświeżania były ujawniane — które umożliwiają deweloperom ich buforowanie. Następnie używają `AcquireTokenByRefreshToken()` do włączania rozwiązań, takich jak implementacja długotrwałych usług, które odświeżają pulpity nawigacyjne w imieniu użytkownika, gdy użytkownik nie jest już połączony.

MSAL4J nie ujawnia tokenów odświeżania ze względów bezpieczeństwa. Zamiast tego MSAL obsługuje odświeżanie tokenów.

MSAL4J ma interfejs API, który umożliwia migrowanie tokenów odświeżania uzyskanych z ADAL4j do ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Za pomocą tej metody można podać poprzednio używany token odświeżania wraz z dowolnymi żądanymi zakresami (zasobami). Token odświeżania zostanie nadany nowemu serwerowi w pamięci podręcznej do użycia przez aplikację.

Poniższy fragment kodu przedstawia kod migracji w poufnej aplikacji klienckiej:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult` zwraca token dostępu i token identyfikatora, podczas gdy nowy token odświeżania jest przechowywany w pamięci podręcznej. Aplikacja będzie również zawierać IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Aby użyć tokenów, które znajdują się teraz w pamięci podręcznej, wywołaj:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
