---
title: Migrowanie do platformy MSAL.NET | Azure
description: Dowiedz się więcej na temat różnic między Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET) i Azure AD Authentication Library for .NET (ADAL.NET) oraz jak przeprowadzić migrację do platformy MSAL.NET.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9be13ac22e6eda32668d635032ebcccf417b6c7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785219"
---
# <a name="migrating-applications-to-msalnet"></a>Migrowanie aplikacji do platformy MSAL.NET

Zarówno Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET) i Azure AD Authentication Library for .NET (ADAL.NET) są używane do uwierzytelniania jednostki usługi Azure AD i żądać tokenów z usługi Azure AD. Aż do chwili Większość programistów mający doświadczenie z usługą Azure AD dla deweloperów platformy (w wersji 1.0) do uwierzytelniania tożsamości usługi Azure AD (kont służbowych), żądając tokenów przy użyciu usługi Azure AD Authentication Library (ADAL). Teraz przy użyciu platformy MSAL.NET, można uwierzytelniać szerszego zakresu tożsamości firmy Microsoft (tożsamości usługi Azure AD i konta Microsoft i kont społecznościowych i lokalnych przy użyciu usługi Azure AD B2C) za pośrednictwem punktu końcowego platformy tożsamości firmy Microsoft. 

W tym artykule opisano, jak dokonać wyboru między Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET) i Azure AD Authentication Library for .NET (ADAL.NET) i porównuje dwie biblioteki.  

## <a name="differences-between-adal-and-msal-apps"></a>Różnice między aplikacjami biblioteki ADAL i biblioteki MSAL
W większości przypadków chcesz użyć platformy MSAL.NET i punktu końcowego platformy tożsamości firmy Microsoft, w którym to najnowsza generacja bibliotek uwierzytelniania firmy Microsoft. Przy użyciu platformy MSAL.NET, możesz uzyskać tokeny dla użytkowników, rejestracja w usłudze aplikacji za pomocą usługi Azure AD (kont służbowych) konta Microsoft (osobistych) (MSA) lub usługi Azure AD B2C. 

Jeśli już znasz z usługą Azure AD dla deweloperów (1.0) punktu końcowego (i ADAL.NET), warto przeczytać [czym się różni od punktu końcowego platformy (w wersji 2.0) tożsamości firmy Microsoft?](active-directory-v2-compare.md).

Nadal jednak należy używać ADAL.NET, jeśli Twoja aplikacja potrzebuje do logowania użytkowników z wcześniejszymi wersjami programu [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services). Aby uzyskać więcej informacji, zobacz [usług AD FS obsługuje](https://aka.ms/msal-net-adfs-support).

Poniższy obraz zawiera podsumowanie różnic między ADAL.NET i platformy MSAL.NET ![Side-by-side kodu](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pakiety NuGet i przestrzenie nazw

ADAL.NET jest używane z [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pakietu NuGet. przestrzeń nazw do użycia jest `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Aby użyć platformy MSAL.NET, należy dodać [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet pakietu, a następnie użyj `Microsoft.Identity.Client` przestrzeni nazw

### <a name="scopes-not-resources"></a>Zakresy nie zasobów

ADAL.NET uzyskuje tokenów dla *zasobów*, ale platformy MSAL.NET uzyskuje tokenów dla *zakresy*. Liczba zastąpień AcquireToken platformy MSAL.NET wymagają parametr o nazwie zakresy (`IEnumerable<string> scopes`). Ten parametr jest prostą listę ciągów, które deklarują żądane uprawnienia i zasoby, które są wymagane. Dobrze znane zakresy są [zakresy programu Microsoft Graph](/graph/permissions-reference).

Jest również możliwe platformy MSAL.NET dostęp do zasobów w wersji 1.0. Szczegółowe informacje w [zakresów dla aplikacji w wersji 1.0](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Klasy podstawowe

- Używa ADAL.NET [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) jako reprezentacja Twoje połączenie z serwerem usługa tokenu zabezpieczającego (STS) lub autoryzacji za pomocą urzędu. Przeciwnie, platformy MSAL.NET zaprojektowany pod kątem [aplikacje klienckie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Zapewnia dwóch osobnych klas: `PublicClientApplication` i `ConfidentialClientApplication`

- Uzyskiwanie tokenów: ADAL.NET platformy MSAL.NET się tego samego wywołania uwierzytelniania (`AcquireTokenAsync` i `AcquireTokenSilentAsync` dla ADAL.NET, i `AqquireTokenInteractive` i `AcquireTokenSilent` w platformy MSAL.NET), ale z różnymi parametrami, wymagane. Jedną różnicaą jest to fakt, że w platformy MSAL.NET, masz już do przekazywania `ClientID` dla aplikacji w każdym wywołaniu AcquireTokenXX. W rzeczywistości `ClientID` ustawić tylko raz podczas kompilowania (`IPublicClientApplication` lub `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>IAccount nie IUser

ADAL.NET manipulować użytkowników. Jednak użytkownik znajduje się agent oprogramowania lub ludzi, ale może posiadać/właścicielem/odpowiada za co najmniej jednego konta w systemie tożsamości firmy Microsoft (kilka usługi Azure AD konta usługi Azure AD B2C osobistych kont Microsoft). 

Platformy MSAL.NET 2.x teraz wprowadzono pojęcie konta (za pośrednictwem interfejsu IAccount). Tej istotnej zmiany zapewnia semantykę, prawy: fakt, że ten sam użytkownik może mieć kilka kont na innej platformie Azure AD katalogów. Platformy MSAL.NET także lepsze informacje w scenariuszach gościa, ponieważ informacje o koncie głównego.

Aby uzyskać więcej informacji na temat różnic między IUser i IAccount zobacz [platformy MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Wyjątki

#### <a name="interaction-required-exceptions"></a>Wyjątki interakcji ze strony

Platformy MSAL.NET ma dokładniejsze wyjątki. Na przykład gdy dyskretne uwierzytelnianie nie powiedzie się w bibliotece ADAL procedura ma wyłapania wyjątku i poszukaj `user_interaction_required` kod błędu:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Szczegółowe informacje w [zalecany wzorzec, aby uzyskać token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) z ADAL.NET

Przy użyciu platformy MSAL.NET, catch `MsalUiRequiredException` zgodnie z opisem w [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Obsługa wyjątków żądania oświadczeń

ADAL.NET oświadczeń wyzwaniem, któremu wyjątki są obsługiwane w następujący sposób:

- `AdalClaimChallengeException` Wyjątek stanowi (wynikających ze `AdalServiceException`) generowany przez usługę, w przypadku, gdy zasób wymaga więcej oświadczeń użytkownika (na przykład dwa czynniki uwierzytelniania). `Claims` Elementu członkowskiego zawiera niektóre fragment kodu JSON z oświadczeń, które oczekują.
- Nadal w ADAL.NET, aplikacja kliencka publicznej odbierania ten wyjątek musi wywołać `AcquireTokenInteractive` zastąpienia mających parametr oświadczeń. To zastąpienie elementu `AcquireTokenInteractive` nawet nie próbuje trafień pamięci podręcznej, ponieważ nie jest konieczne. Przyczyną jest to, czy token w pamięci podręcznej nie ma odpowiednie oświadczenia (w przeciwnym razie `AdalClaimChallengeException` nie będzie były zgłaszane). W związku z tym nie ma potrzeby spojrzeć na pamięci podręcznej. Należy pamiętać, że `ClaimChallengeException` może zostać odebrany w WebAPI OBO, wykonując, natomiast `AcquireTokenInteractive` musi być wywoływany w aplikacji klienckiej publicznych wywołanie tego interfejsu API sieci Web.
- Aby uzyskać szczegółowe informacje, w tym przykłady Zobacz obsługi [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

Platformy MSAL.NET oświadczeń wyzwaniem, któremu wyjątki są obsługiwane w następujący sposób:

- `Claims` Są udostępniane w `MsalServiceException`.
- Brak `.WithClaim(claims)` metody, które można zastosować do `AcquireTokenInteractive` konstruktora. 

### <a name="supported-grants"></a>Przyznaje obsługiwane

Nie wszystkie granty są jeszcze obsługiwane w platformy MSAL.NET i punktu końcowego v2.0. Poniżej przedstawiono podsumowanie porównania ADAL.NET i Biblioteka MSAL. NET użytkownika obsługiwane przyznaje.

#### <a name="public-client-applications"></a>Aplikacje klienckie publiczne

Poniżej przedstawiono przyznaje obsługiwane ADAL.NET i platformy MSAL.NET dla aplikacji Desktop i Mobile

Udziel | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktywne | [Uwierzytelnianie interakcyjne](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Uzyskiwanie tokenów interaktywnie w platformy MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Zintegrowane uwierzytelnianie systemu Windows | [Zintegrowane uwierzytelnianie na Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Uwierzytelnianie zintegrowane Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nazwa użytkownika / hasło | [Uzyskiwanie tokenów przy użyciu nazwy użytkownika i hasła](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Uwierzytelnianie za pomocą nazwy użytkownika, hasła](msal-authentication-flows.md#usernamepassword)
Przepływ kodu urządzenia | [Profil urządzenia dla urządzeń bez przeglądarki sieci web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Aplikacje poufne klienta

Poniżej przedstawiono przyznaje obsługiwana w ADAL.NET i platformy MSAL.NET dla aplikacji sieci Web, interfejsów API sieci Web i aplikacji demona:

Typ aplikacji | Udziel | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web App, interfejs API sieci Web, demona | Poświadczenia klienta | [Przepływy poświadczeń klienta w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Poświadczenia klienta przepływu w ramach platformy MSAL.NET](msal-authentication-flows.md#client-credentials))
Interfejs API sieci Web | W imieniu użytkownika | [Wywołania Usługa do usługi w imieniu użytkownika z ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [W imieniu w platformy MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplikacja sieci Web | Kod uwierzytelniania | [Uzyskiwanie tokenów z informacjami o kodach autoryzacji na aplikacje internetowe za pomocą ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Uzyskiwanie tokenów z informacjami o kodach autoryzacji w usłudze web apps przy użyciu platformy MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Stan trwały pamięci podręcznej

ADAL.NET umożliwia rozszerzenie `TokenCache` klasy do implementacji funkcji odpowiednim stanem trwałym na platformach bez bezpiecznego magazynu (.NET Framework i .NET core) przy użyciu `BeforeAccess`, i `BeforeWrite` metody. Aby uzyskać więcej informacji, zobacz [tokenu serializacji pamięci podręcznej w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

Platformy MSAL.NET sprawia, że pamięć podręczną tokenu zamkniętej klasy należy rozszerzyć jej możliwości. W związku z tym implementacja trwałość pamięci podręcznej tokenu musi być w formie klasa pomocnika, która wchodzi w interakcję z pamięci podręcznej tokenu zapieczętowany. Ta interakcja jest opisana w [tokenu serializacji pamięci podręcznej w platformy MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Znaczne wspólnej własności

W wersji 1.0, jeśli używasz https://login.microsoftonline.com/common urząd, użytkownicy zalogować się przy użyciu dowolnego konta usługi AAD (dla każdej organizacji). Zobacz [Weryfikacja urzędu w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Jeśli używasz https://login.microsoftonline.com/common urzędu w wersji 2.0, użytkownicy zalogować się przy użyciu dowolnej organizacji usługi AAD lub osobiste konto Microsoft (MSA). W przypadku platformy MSAL.NET, jeśli chcesz ograniczyć Zaloguj się do dowolnego konta usługi AAD (takie samo zachowanie jako za pomocą ADAL.NET), należy użyć https://login.microsoftonline.com/organizations. Aby uzyskać więcej informacji, zobacz `authority` parametru w [aplikacja kliencka publicznych](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>tokeny w wersji 1.0 i 2.0

Istnieją dwie wersje tokenów:
- Tokenów w wersji 1.0
- Tokenów w wersji 2.0 

Punkt końcowy w wersji 1.0 (używany przez bibliotekę ADAL) emituje tylko tokenów w wersji 1.0.

Jednak punktu końcowego v2.0 (używane przez biblioteki MSAL) emituje wersję token, który akceptuje interfejsu API sieci Web. Właściwości manifestu aplikacji interfejsu API sieci Web umożliwia deweloperom wybrać, którą wersję token jest akceptowany. Zobacz `accessTokenAcceptedVersion` w [manifest aplikacji](reference-app-manifest.md) dokumentację referencyjną.

Aby uzyskać więcej informacji na temat tokenów w wersji 1.0 i 2.0, zobacz [tokenów dostępu usługi Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy interfejsu API sieci Web akceptuje tokenów w wersji 1.0

Uprawnienia OAuth2 są zakresy uprawnień, które v1.0 aplikacji sieci web interfejsu API (zasobu) uwidacznia aplikacjom klienckim. Te zakresy uprawnień, może przyznawać aplikacjom klienckim podczas wyrażania zgody. Zobacz sekcję dotyczącą oauth2Permissions w [manifest aplikacji w usłudze Azure Active Directory](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy, aby zawnioskować o dostęp do określonych uprawnień OAuth2 aplikacji w wersji 1.0

Jeśli chcesz uzyskać tokeny dla określonych zakresów aplikacji w wersji 1.0 (na przykład grafu usługi AAD, czyli https://graph.windows.net), będziesz potrzebować do utworzenia `scopes` przez złączenie identyfikatorem żądanego zasobu z uprawnieniem OAuth2 odpowiednią dla tego zasobu.

Na przykład na dostęp do nazwy użytkownika interfejsu API sieci Web, czyli identyfikator URI Identyfikatora aplikacji w wersji 1.0 `ResourceId`, może okazać się użycie:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Jeśli chcesz odczytywać i zapisywać przy użyciu platformy MSAL.NET usługi Azure Active Directory przy użyciu interfejsu API programu graph usługi AAD (https://graph.windows.net/), należy utworzyć listę zakresów, podobnie jak w poniższym fragmencie kodu:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Ostrzeżenie: Powinien mieć co najmniej dwóch ukośników w zakresie odpowiadający 1.0 interfejsu API sieci Web

Jeśli chcesz zapisać zakres odpowiadający interfejsu API usługi Azure Resource Manager (https://management.core.windows.net/), trzeba zażądać następującym zakresem (Uwaga dwa ukośniki) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Jest to spowodowane interfejsu API usługi Resource Manager oczekuje ukośnika w jego oświadczenia odbiorców (`aud`), a następnie jest ukośnikiem, aby oddzielić nazwę interfejsu API z zakresu.

Przez logikę używaną przez usługę Azure AD jest następująca:
- Dla punktu końcowego biblioteki ADAL (1.0), przy użyciu tokenu dostępu w wersji 1.0 (tylko to możliwe), aud = zasobów
- Dla biblioteki MSAL (punktu końcowego v2.0) pytaniem tokenu dostępu dla zasobu, akceptując tokenów w wersji 2.0, aud = zasobów. Identyfikator aplikacji
- Biblioteka MSAL (punktu końcowego v2.0) pytaniem tokenu dostępu dla zasobu akceptowania tokenu dostępu w wersji 1.0 (czyli tak powyżej) usługi Azure AD zostanie przetworzony przez pobranie wszystkiego wcześniej niż ostatni ukośnika i używać go jako identyfikator zasobu odbiorców w żądanym zakresie. W związku z tym jeśli https:\//database.windows.net oczekuje odbiorcami "https://database.windows.net/", należy zażądać zakres https:\//database.windows.net//.default. Zobacz również wydać #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Ukośnika url zasobu jest pominięty, który spowodował niepowodzenie uwierzytelniania sql #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Zakresy, aby zawnioskować o dostęp do uprawnień aplikacji w wersji 1.0

Na przykład jeśli chcesz uzyskać token dla wszystkich zakresów statycznej aplikacji w wersji 1.0, jeden musiałby użyć

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Zakresy, aby zażądać w przypadku klienta poświadczeń przepływu / aplikacji demona

W przypadku przepływ poświadczeń klienta, również będą zakres, aby przekazać `/.default`. Oznacza to, do usługi Azure AD: "wszystkie poziomie aplikacji uprawnienia, które administrator wyraził zgody na rejestracji aplikacji.

## <a name="adal-to-msal-migration"></a>Biblioteki ADAL w celu migracji biblioteki MSAL

W wersji 2 ADAL.NET. X, odświeżania, tokeny były uwidocznione, dzięki czemu możesz opracowywać rozwiązania wokół korzystanie z tych tokenów w pamięci podręcznej je oraz przy użyciu `AcquireTokenByRefreshToken` metod dostarczonych przez 2.x biblioteki ADAL. Niektóre z tych rozwiązań zostały użyte w scenariuszach takich jak:
* Długotrwałe usługi, które obsługują działań w tym odświeżanie pulpitów nawigacyjnych w imieniu użytkowników, dlatego użytkownicy nie jest już połączony. 
* Scenariusze WebFarm włączenie na kliencie wyświetlić RT w usłudze sieci web (buforowanie odbywa się po stronie klienta, zaszyfrowanego pliku cookie, a nie po stronie serwera)

Nie jest wymagane przy użyciu platformy MSAL.NET, jednak jako firma Microsoft nie zaleca się dłużej, wykorzystujących odświeżanie tokenów w ten sposób ze względów bezpieczeństwa. To może utrudnić migrację do biblioteki MSAL 3.x jako interfejs API nie umożliwiają przekazywanie tokenów odświeżania wcześniej uzyskana. 

Na szczęście platformy MSAL.NET ma teraz interfejsu API, który umożliwia migrację do poprzedniego tokenów odświeżania `IConfidentialClientApplication` 

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Przy użyciu tej metody można podać token odświeżania poprzednio używanych wraz z wszystkie zakresy (zasoby) wygodną pracę. Token odświeżania zostaną wymienione na nową i buforowane w aplikacji.  

Ta metoda jest przeznaczona dla scenariuszy, które nie są typowe, dlatego nie jest łatwo dostępne z `IConfidentialClientApplication` bez uprzedniego rzutowania go na `IByRefreshToken`.

Następujący fragment kodu przedstawia migrację kodu w aplikacji klienckiej poufne. `GetCachedRefreshTokenForSignedInUser` Pobierz token odświeżania, przechowywane w niektórych magazynu przy użyciu poprzedniej wersji aplikacji, która umożliwia korzystanie z biblioteki ADAL 2.x. `GetTokenCacheForSignedInUser` deserializuje pamięci podręcznej dla zalogowanego użytkownika (jako poufne klienta aplikacji powinien mieć jeden pamięci podręcznej na użytkownika).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Zostanie wyświetlony token dostępu i token Identyfikatora zwrócone w Twojej AuthenticationResult, gdy nowy token odświeżania jest przechowywany w pamięci podręcznej.

Ta metoda służy również dla różnych scenariuszy integracji, w którym masz dostępnego tokenu odświeżania.

## <a name="next-steps"></a>Kolejne kroki

Można znaleźć więcej informacji na temat zakresów w [zakresy, uprawnienia i zgody w punkcie końcowym platforma tożsamości firmy Microsoft](v2-permissions-and-consent.md)