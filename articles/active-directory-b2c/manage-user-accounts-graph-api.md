---
title: Zarządzanie użytkownikami za pomocą interfejsu API Microsoft Graph
titleSuffix: Azure AD B2C
description: Jak zarządzać użytkownikami w dzierżawie Azure AD B2C przez wywołanie interfejsu API Microsoft Graph i użycie tożsamości aplikacji w celu zautomatyzowania procesu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b53405d199072211304e21b681de646c4e41243c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585632"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Zarządzanie kontami użytkowników Azure AD B2C przy użyciu Microsoft Graph

Microsoft Graph umożliwia zarządzanie kontami użytkowników w katalogu Azure AD B2C przez dostarczanie metod tworzenia, odczytywania, aktualizowania i usuwania w interfejsie API Microsoft Graph. Istnieje możliwość migrowania istniejącego magazynu użytkownika do dzierżawy Azure AD B2C i wykonywania innych operacji zarządzania kontami użytkowników przez wywołanie interfejsu API Microsoft Graph.

W poniższych sekcjach przedstawiono kluczowe aspekty Azure AD B2C zarządzania użytkownikami za pomocą interfejsu API Microsoft Graph. Microsoft Graph operacje interfejsu API, typy i właściwości przedstawione w tym miejscu są podzbiorem, który pojawia się w dokumentacji dotyczącej interfejsu API Microsoft Graph.

## <a name="register-a-management-application"></a>Rejestrowanie aplikacji zarządzania

Zanim dowolna aplikacja do zarządzania użytkownikami lub skrypt, który pisze, będzie mogła korzystać z zasobów w dzierżawie Azure AD B2C, potrzebna jest rejestracja aplikacji, która przyznaje uprawnienia.

Wykonaj kroki opisane w tym artykule jak, aby utworzyć rejestrację aplikacji, która może być używana przez aplikację zarządzania:

[Zarządzanie Azure AD B2C przy użyciu Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Operacje Microsoft Graph zarządzania użytkownikami

Następujące operacje zarządzania użytkownikami są dostępne w [interfejsie API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user):

- [Pobierz listę użytkowników](https://docs.microsoft.com/graph/api/user-list)
- [Tworzenie użytkownika](https://docs.microsoft.com/graph/api/user-post-users)
- [Pobierz użytkownika](https://docs.microsoft.com/graph/api/user-get)
- [Aktualizowanie użytkownika](https://docs.microsoft.com/graph/api/user-update)
- [Usuwanie użytkownika](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Właściwości użytkownika

### <a name="display-name-property"></a>Właściwość wyświetlanej nazwy

`displayName` jest nazwą wyświetlaną w Azure Portal zarządzania użytkownikami dla użytkownika, a w tokenie dostępu Azure AD B2C powraca do aplikacji. Ta właściwość jest wymagana.

### <a name="identities-property"></a>Właściwość tożsamości

Konto klienta, które może być odbiorcą, partnerem lub obywatelem, może być skojarzone z tymi typami tożsamości:

- **Lokalna** tożsamość — nazwa użytkownika i hasło są przechowywane lokalnie w katalogu Azure AD B2C. Często odwołują się do tych tożsamości jako "konta lokalne".
- Tożsamość **federacyjna** — znana także jako konta *społecznościowe* *lub firmowe* , tożsamość użytkownika jest zarządzana przez federacyjnego dostawcę tożsamości, takiego jak Facebook, Microsoft, ADFS lub Salesforce.

Użytkownik z kontem klienta może zalogować się przy użyciu wielu tożsamości. Na przykład nazwa użytkownika, adres e-mail, identyfikator pracownika, identyfikator instytucji rządowych i inne. Pojedyncze konto może mieć wiele tożsamości, zarówno lokalnych, jak i społecznościowych, przy użyciu tego samego hasła.

W interfejsie API Microsoft Graph zarówno tożsamość lokalna, jak i federacyjna są przechowywane w atrybucie `identities` użytkownika, który jest typu [objectIdentity][graph-objectIdentity]. Kolekcja `identities` reprezentuje zestaw tożsamości używanych do logowania się do konta użytkownika. Ta kolekcja umożliwia użytkownikowi zalogowanie się do konta użytkownika przy użyciu dowolnej skojarzonej tożsamości.

| Właściwość   | Typ |Opis|
|:---------------|:--------|:----------|
|signInType|ciąg| Określa typy logowania użytkownika w katalogu. Dla konta lokalnego: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName`lub dowolnego innego typu. Konto społecznościowe musi mieć wartość `federated`.|
|Issuer|ciąg|Określa wystawcę tożsamości. W przypadku kont lokalnych (gdzie **signInType** nie jest `federated`) ta właściwość jest domyślną nazwą domeny dzierżawy lokalnej usługi B2C, na przykład `contoso.onmicrosoft.com`. W przypadku tożsamości społecznościowej (gdzie **signInType** jest `federated`) wartością jest nazwa wystawcy, na przykład `facebook.com`|
|issuerAssignedId|ciąg|Określa unikatowy identyfikator przypisany do użytkownika przez wystawcę. Kombinacja **wystawców** i **issuerAssignedId** musi być unikatowa w ramach dzierżawy. W przypadku konta lokalnego, gdy **signInType** jest ustawiony na `emailAddress` lub `userName`, reprezentuje nazwę logowania użytkownika.<br>Gdy **signInType** jest ustawiona na: <ul><li>`emailAddress` (lub rozpoczyna się od `emailAddress` jak `emailAddress1`) **issuerAssignedId** musi być prawidłowym adresem e-mail</li><li>`userName` (lub jakakolwiek inna wartość) **issuerAssignedId** musi być prawidłową [lokalną częścią adresu e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** reprezentuje unikatowy identyfikator konta federacyjnego</li></ul>|

W przypadku tożsamości federacyjnych, w zależności od dostawcy tożsamości, **issuerAssignedId** jest unikatową wartością dla danego użytkownika dla aplikacji lub konta deweloperskiego. Skonfiguruj zasady Azure AD B2C przy użyciu tego samego identyfikatora aplikacji, który został wcześniej przypisany przez dostawcę usług społecznościowych lub inną aplikację w ramach tego samego konta deweloperskiego.

### <a name="password-profile-property"></a>Właściwość profilu hasła

W przypadku tożsamości lokalnej Właściwość **passwordProfile** jest wymagana i zawiera hasło użytkownika. Właściwość `forceChangePasswordNextSignIn` musi mieć wartość `false`.

W przypadku tożsamości federacyjnej (społecznej) Właściwość **passwordProfile** nie jest wymagana.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Właściwość zasad haseł

Zasady haseł Azure AD B2C (dla kont lokalnych) są oparte na zasadach Azure Active Directory [silnych haseł](../active-directory/authentication/concept-sspr-policy.md) . Zasady rejestrowania i logowania Azure AD B2C i resetowania hasła wymagają silnego siły hasła i nie wygasają hasła.

W scenariuszach migracji użytkowników, jeśli konta, które mają zostać poddane migracji, mają słabsze siły hasła niż [silne siły hasła](../active-directory/authentication/concept-sspr-policy.md) wymuszone przez Azure AD B2C można wyłączyć wymaganie silnego hasła. Aby zmienić domyślne zasady haseł, ustaw właściwość `passwordPolicies` na `DisableStrongPassword`. Na przykład można zmodyfikować polecenie Utwórz żądanie użytkownika w następujący sposób:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Właściwości rozszerzenia

Każda aplikacja dołączona do klienta ma unikatowe wymagania dotyczące zbieranych informacji. Dzierżawa Azure AD B2C zawiera wbudowany zestaw informacji przechowywanych we właściwościach, takich jak imię, nazwisko, miejscowość i kod pocztowy. Za pomocą Azure AD B2C można rozłożyć zestaw właściwości przechowywanych na poszczególnych kontach klientów. Aby uzyskać więcej informacji na temat definiowania atrybutów niestandardowych, zobacz [atrybuty niestandardowe (przepływy użytkownika)](user-flow-custom-attributes.md) i [atrybuty niestandardowe (zasady niestandardowe)](custom-policy-custom-attributes.md).

Interfejs API Microsoft Graph obsługuje tworzenie i aktualizowanie użytkownika z atrybutami rozszerzenia. Atrybuty rozszerzenia w interfejs API programu Graph są nazwane przy użyciu konwencji `extension_ApplicationObjectID_attributename`. Na przykład:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Przykład kodu

Ten przykładowy kod jest aplikacją konsolową platformy .NET Core, która korzysta z [zestawu SDK Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview) , aby współdziałać z interfejsem API Microsoft Graph. W jego kodzie pokazano, jak wywołać interfejs API, aby programowo zarządzać użytkownikami w dzierżawie Azure AD B2C.
Możesz [pobrać przykładowe archiwum](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [przejrzeć repozytorium](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) w witrynie GitHub lub sklonować repozytorium:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po uzyskaniu przykładowego kodu skonfiguruj go dla środowiska, a następnie Skompiluj projekt:

1. Otwórz projekt w programie [Visual Studio](https://visualstudio.microsoft.com) lub [Visual Studio Code](https://code.visualstudio.com).
1. Otwórz program `src/appsettings.json`.
1. W sekcji `appSettings` Zastąp `your-b2c-tenant` nazwą dzierżawy, a `Application (client) ID` i `Client secret` z wartościami rejestracji aplikacji zarządzania (zobacz sekcję [Rejestrowanie aplikacji zarządzania](#register-a-management-application) w tym artykule).
1. Otwórz okno konsoli w ramach lokalnego klonu repozytorium, przejdź do katalogu `src`, a następnie Skompiluj projekt:
    ```console
    cd src
    dotnet build
    ```
1. Uruchom aplikację za pomocą polecenia `dotnet`:

```console
dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
```

Aplikacja wyświetla listę poleceń, które można wykonać. Na przykład Pobierz wszystkich użytkowników, uzyskaj jednego użytkownika, Usuń użytkownika, zaktualizuj hasło użytkownika i Importuj zbiorczo.

### <a name="code-discussion"></a>Dyskusja o kodzie

Przykładowy kod używa [zestawu SDK Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview), który jest przeznaczony do uproszczenia tworzenia wysokiej jakości, wydajnych i odpornych aplikacji, które mają dostęp Microsoft Graph. W związku z tym nie trzeba wykonywać żadnego bezpośredniego interfejsu API Microsoft Graph.

Każde żądanie do interfejsu API Microsoft Graph wymaga tokenu dostępu do uwierzytelniania. Rozwiązanie korzysta z pakietu NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) , który udostępnia otokę opartą na scenariuszu uwierzytelniania biblioteki uwierzytelniania firmy Microsoft (MSAL) do użycia z zestawem SDK Microsoft Graph.

Metoda `RunAsync` w pliku _program.cs_ :

1. Odczytuje ustawienia aplikacji z pliku _appSettings. JSON_
1. Inicjuje dostawcę uwierzytelniania przy użyciu funkcji przepływu [przydzielenia poświadczeń klienta OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Za pomocą przepływu Przydziel poświadczenia klienta aplikacja może uzyskać token dostępu do wywołania interfejsu API Microsoft Graph.
1. Konfiguruje klienta usługi Microsoft Graph przy użyciu dostawcy uwierzytelniania:

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

Zainicjowany *GraphServiceClient* jest następnie używany w _UserService.cs_ do wykonywania operacji zarządzania użytkownikami. Na przykład, aby uzyskać listę kont użytkowników w dzierżawie:

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

[Wykonywanie wywołań interfejsu API przy użyciu zestawów sdk Microsoft Graph](https://docs.microsoft.com/graph/sdks/create-requests) zawiera informacje na temat odczytywania i zapisywania informacji z Microsoft Graph, używania `$select` do kontrolowania zwracanych właściwości, dostarczania niestandardowych parametrów zapytania i używania parametrów zapytania `$filter` i `$orderBy`.

## <a name="next-steps"></a>Następne kroki

Pełny indeks Microsoft Graph operacji interfejsu API obsługiwanych przez zasoby Azure AD B2C można znaleźć w temacie [Microsoft Graph operacje dostępne dla Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
