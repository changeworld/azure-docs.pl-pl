---
title: Zarządzanie użytkownikami za pomocą interfejsu API programu Microsoft Graph
titleSuffix: Azure AD B2C
description: Jak zarządzać użytkownikami w dzierżawie usługi Azure AD B2C, wywołując interfejs API programu Microsoft Graph i używając tożsamości aplikacji do automatyzacji procesu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3bd166572cea23fbb710cd053c28f51e76ba534a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476675"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Zarządzanie kontami użytkowników usługi Azure AD B2C za pomocą programu Microsoft Graph

Program Microsoft Graph umożliwia zarządzanie kontami użytkowników w katalogu usługi Azure AD B2C, udostępniając metody tworzenia, odczytu, aktualizacji i usuwania w interfejsie API programu Microsoft Graph. Istniejący magazyn użytkowników można migrować do dzierżawy usługi Azure AD B2C i wykonywać inne operacje zarządzania kontem użytkowników, wywołując interfejs API programu Microsoft Graph.

W kolejnych sekcjach przedstawiono kluczowe aspekty zarządzania użytkownikami usługi Azure AD B2C za pomocą interfejsu API programu Microsoft Graph. Przedstawione tutaj operacje, typy i właściwości interfejsu API programu Microsoft Graph są podzbiorem tego, który pojawia się w dokumentacji referencyjnej interfejsu API programu Microsoft Graph.

## <a name="register-a-management-application"></a>Rejestrowanie aplikacji do zarządzania

Zanim dowolna aplikacja do zarządzania użytkownikami lub skrypt, który piszesz, będzie mogła wchodzić w interakcje z zasobami w dzierżawie usługi Azure AD B2C, potrzebna jest rejestracja aplikacji, która przyznaje uprawnienia do tego celu.

Wykonaj kroki opisane w tym artykule, aby utworzyć rejestrację aplikacji, której może używać aplikacja do zarządzania:

[Zarządzanie usługą Azure AD B2C za pomocą programu Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Zarządzanie użytkownikami w programie Microsoft Graph

W [interfejsie API programu Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user)dostępne są następujące operacje zarządzania użytkownikami:

- [Pobierz listę użytkowników](https://docs.microsoft.com/graph/api/user-list)
- [Tworzenie użytkownika](https://docs.microsoft.com/graph/api/user-post-users)
- [Uzyskaj użytkownika](https://docs.microsoft.com/graph/api/user-get)
- [Aktualizowanie użytkownika](https://docs.microsoft.com/graph/api/user-update)
- [Usuń użytkownika](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Właściwości użytkownika

### <a name="display-name-property"></a>Właściwość nazwa wyświetlana

Jest `displayName` to nazwa do wyświetlenia w usłudze Azure portal zarządzania użytkownikami dla użytkownika i w tokenie dostępu usługi Azure AD B2C zwraca do aplikacji. Ta właściwość jest wymagana.

### <a name="identities-property"></a>Właściwość tożsamości

Konto odbiorcy, które może być konsumentem, partnerem lub obywatelem, może być skojarzone z tymi typami tożsamości:

- Tożsamość **lokalna** — nazwa użytkownika i hasło są przechowywane lokalnie w katalogu usługi Azure AD B2C. Często nazywamy te tożsamości "kontami lokalnymi".
- **Tożsamość federacyjnej** — znana również jako konta *społecznościowe* lub *korporacyjne,* tożsamość użytkownika jest zarządzana przez dostawcę tożsamości federacyjnej, takiego jak Facebook, Microsoft, ADFS lub Salesforce.

Użytkownik z kontem klienta może zalogować się przy użyciu wielu tożsamości. Na przykład nazwa użytkownika, adres e-mail, identyfikator pracownika, identyfikator rządowy i inne. Jedno konto może mieć wiele tożsamości, zarówno lokalnych, jak i społecznościowych, z tym samym hasłem.

W interfejsie API programu Microsoft Graph zarówno lokalne, jak `identities` i federacyjne tożsamości są przechowywane w atrybucie użytkownika, który jest [typem objectIdentity][graph-objectIdentity]. Kolekcja `identities` reprezentuje zestaw tożsamości używanych do logowania się do konta użytkownika. Ta kolekcja umożliwia użytkownikowi zalogowanie się do konta użytkownika przy każdej z jego tożsamości skojarzonych.

| Właściwość   | Typ |Opis|
|:---------------|:--------|:----------|
|signInType (Typ)|ciąg| Określa typy logowania użytkownika w katalogu. Dla konta `emailAddress`lokalnego: `emailAddress3` `userName`, `emailAddress1`, `emailAddress2`, , lub innego typu, który ci się podoba. Konto społecznościowe musi `federated`być ustawione na .|
|issuer|ciąg|Określa wystawcę tożsamości. W przypadku kont lokalnych (gdzie **signInType** nie `federated`jest) ta właściwość jest `contoso.onmicrosoft.com`lokalną domyślną nazwą domeny dzierżawy B2C, na przykład . Dla tożsamości społecznej (gdzie `federated` **signInType** jest) wartość jest nazwą wystawcy, na przykład`facebook.com`|
|identyfikator przypisany wystawcy|ciąg|Określa unikatowy identyfikator przypisany użytkownikowi przez wystawcę. Kombinacja **wystawcy** i **identyfikatora wystawcy musi** być unikatowa w obrębie dzierżawy. Dla konta lokalnego, gdy **signInType** jest ustawiona na `emailAddress` lub `userName`, reprezentuje nazwę logowania dla użytkownika.<br>Gdy **signInType** jest ustawiony na: <ul><li>`emailAddress`(lub zaczyna `emailAddress` się `emailAddress1`od polu jak ) **identyfikator emity** musi być prawidłowym adresem e-mail</li><li>`userName`(lub dowolną inną wartość), **identyfikator wystawcy musi** być prawidłową [lokalną częścią adresu e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **identyfikator wystawcy** reprezentuje unikatowy identyfikator konta federacyjnego</li></ul>|

Następująca właściwość **Tożsamości** z lokalną tożsamością konta z nazwą logowania, adresem e-mail jako logowanie i tożsamością społecznościową. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Dla tożsamości federacyjnych, w zależności od dostawcy tożsamości, **identyfikator wystawcy jest** unikatową wartością dla danego użytkownika na aplikację lub konto dewelopera. Skonfiguruj zasady usługi Azure AD B2C o tym samym identyfikatorze aplikacji, który został wcześniej przypisany przez dostawcę społecznościowego lub inną aplikację w ramach tego samego konta dewelopera.

### <a name="password-profile-property"></a>Właściwość profilu hasła

Dla tożsamości **lokalnej, passwordProfile** właściwość jest wymagana i zawiera hasło użytkownika. Właściwość `forceChangePasswordNextSignIn` musi `false`być ustawiona na .

Dla tożsamości federacyjnej (społecznej) **hasłoProfile** właściwość nie jest wymagana.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Właściwość zasad haseł

Zasady haseł usługi Azure AD B2C (dla kont lokalnych) są oparte na zasadach [silnej siły haseł](../active-directory/authentication/concept-sspr-policy.md) usługi Azure Active Directory. Zasady rejestracji usługi Azure AD B2C lub logowania i resetowania hasła wymagają tej silnej siły hasła i nie wygasają hasła.

W scenariuszach migracji użytkowników, jeśli konta, które chcesz przeprowadzić migrację, mają słabszą siłę hasła niż [silna siła hasła](../active-directory/authentication/concept-sspr-policy.md) wymuszana przez usługę Azure AD B2C, można wyłączyć wymaganie dotyczące silnego hasła. Aby zmienić domyślną zasadę `DisableStrongPassword`haseł, ustaw właściwość na `passwordPolicies` . Na przykład można zmodyfikować żądanie tworzenia użytkownika w następujący sposób:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Właściwości rozszerzenia

Każda aplikacja skierowana do klienta ma unikalne wymagania dotyczące informacji, które mają być zbierane. Dzierżawa usługi Azure AD B2C jest wyposażona we wbudowany zestaw informacji przechowywanych we właściwościach, takich jak imię, nazwisko, miasto i kod pocztowy. Za pomocą usługi Azure AD B2C można rozszerzyć zestaw właściwości przechowywanych na każdym koncie klienta. Aby uzyskać więcej informacji na temat definiowania atrybutów niestandardowych, zobacz [atrybuty niestandardowe (przepływy użytkownika)](user-flow-custom-attributes.md) i [atrybuty niestandardowe (zasady niestandardowe).](custom-policy-custom-attributes.md)

Interfejs API programu Microsoft Graph obsługuje tworzenie i aktualizowanie użytkownika z atrybutami rozszerzenia. Atrybuty rozszerzenia w interfejsie API wykresu są nazywane przy użyciu konwencji `extension_ApplicationObjectID_attributename`. Przykład:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Przykład kodu

Ten przykładowy kod to aplikacja konsoli .NET Core, która używa [zestawu Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) do interakcji z interfejsem API programu Microsoft Graph. Jego kod pokazuje, jak wywołać interfejs API do programowego zarządzania użytkownikami w dzierżawie usługi Azure AD B2C.
Możesz [pobrać przykładowe archiwum](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), [przeglądać repozytorium](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) na GitHubie lub sklonować repozytorium:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po uzyskaniu przykładu kodu skonfiguruj go dla środowiska, a następnie skompiluj projekt:

1. Otwórz projekt w [programie Visual Studio](https://visualstudio.microsoft.com) lub Visual Studio [Code](https://code.visualstudio.com).
1. Otwórz plik `src/appsettings.json`.
1. W `appSettings` `your-b2c-tenant` sekcji zastąp nazwą dzierżawy `Application (client) ID` `Client secret` i wartościami rejestracji aplikacji zarządzania (zobacz [sekcję Rejestrowanie aplikacji do zarządzania](#register-a-management-application) w tym artykule).
1. Otwórz okno konsoli w lokalnym klonze repozytorium, przełącz się do `src` katalogu, a następnie skompiluj projekt:
    ```console
    cd src
    dotnet build
    ```
1. Uruchom aplikację za `dotnet` pomocą polecenia:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

Aplikacja wyświetla listę poleceń, które można wykonać. Na przykład pobierz wszystkich użytkowników, uzyskaj jednego użytkownika, usuń użytkownika, zaktualizuj hasło użytkownika i importu zbiorczego.

### <a name="code-discussion"></a>Dyskusja o kodzie

Przykładowy kod używa zestawu [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview), który został zaprojektowany w celu uproszczenia tworzenia wysokiej jakości, wydajnych i odpornych aplikacji, które uzyskują dostęp do programu Microsoft Graph. Dlatego nie trzeba bezpośrednio wszystkie interfejsu API programu Microsoft Graph.

Każde żądanie do interfejsu API programu Microsoft Graph wymaga tokenu dostępu do uwierzytelniania. Rozwiązanie korzysta z pakietu [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet, który udostępnia otokę opartą na scenariuszu uwierzytelniania biblioteki uwierzytelniania firmy Microsoft (MSAL) do użytku z pakietem Microsoft Graph SDK.

Metoda `RunAsync` w pliku _Program.cs:_

1. Odczytuje ustawienia aplikacji z pliku _appsettings.json_
1. Inicjuje dostawcy uwierzytelni przy użyciu [OAuth 2.0 przepływu dotacji poświadczeń klienta.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Dzięki przepływowi dotacji poświadczeń klienta aplikacja może uzyskać token dostępu do wywołania interfejsu API programu Microsoft Graph.
1. Konfiguruje klienta usługi Microsoft Graph z dostawcą eru:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Zainicjowany *GraphServiceClient* jest następnie używany w _UserService.cs_ do wykonywania operacji zarządzania użytkownikami. Na przykład uzyskanie listy kont użytkowników w dzierżawie:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Wykonywanie wywołań interfejsu API przy użyciu zestawu Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/create-requests) zawiera informacje `$select` na temat sposobu odczytu i zapisu `$filter` informacji `$orderBy` z programu Microsoft Graph, służy do kontrolowania właściwości zwracanych, podać niestandardowe parametry kwerendy i używać parametrów i kwerendy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełny indeks operacji interfejsu API programu Microsoft Graph obsługiwanych dla zasobów usługi Azure AD B2C, zobacz [Operacje programu Microsoft Graph dostępne dla usługi Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
