---
title: Użyj interfejs API programu Graph w Azure Active Directory B2C
description: Jak zarządzać użytkownikami w dzierżawie Azure AD B2C, wywołując usługę Azure AD interfejs API programu Graph i używając tożsamości aplikacji do automatyzowania procesu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 71b437f57f9d9e6e18af88d6413269cac6f66c47
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161668"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: korzystanie z usługi Azure AD interfejs API programu Graph

Dzierżawy Azure Active Directory B2C (Azure AD B2C) mogą mieć tysiące lub miliony użytkowników. Oznacza to, że wiele typowych zadań zarządzania dzierżawcami należy wykonać programowo. Podstawowym tego przykładem jest zarządzanie użytkownikami.

Może być konieczne przeprowadzenie migracji istniejącego magazynu użytkownika do dzierżawy B2C. Możesz zdecydować się na hostowanie rejestracji użytkowników na własnej stronie i tworzenie kont użytkowników w katalogu usługi Azure AD B2C w tle. Te rodzaje zadań wymagają, aby można było tworzyć, odczytywać, aktualizować i usuwać konta użytkowników. Te zadania można wykonać przy użyciu interfejs API programu Graph usługi Azure AD.

W przypadku dzierżawców B2C istnieją dwa podstawowe tryby komunikacji z interfejs API programu Graph:

* W przypadku **interakcyjnych**zadań uruchamianych jednokrotnie należy działać jako konto administratora w dzierżawie B2C podczas wykonywania zadań podrzędnych. Ten tryb wymaga od administratora zalogowania się przy użyciu poświadczeń, aby administrator mógł wykonać jakiekolwiek wywołania do interfejs API programu Graph.
* W przypadku **zautomatyzowanych**, ciągłych zadań należy używać określonego typu konta usługi z uprawnieniami do wykonywania zadań zarządzania. W usłudze Azure AD można to zrobić przez zarejestrowanie aplikacji i uwierzytelnienie w usłudze Azure AD. Jest to realizowane przy użyciu *identyfikatora aplikacji* , który używa [przyznania poświadczeń klienta OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). W takim przypadku aplikacja działa jako sama, a nie jako użytkownik, aby wywołać interfejs API programu Graph.

W tym artykule dowiesz się, jak wykonać zautomatyzowany przypadek użycia. Utworzysz `B2CGraphClient` .NET 4,5, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) użytkownika. Klient będzie miał interfejs wiersza polecenia (CLI) systemu Windows, który umożliwia wywoływanie różnych metod. Jednak kod jest zapisywana, aby zachować zachowanie w nieinteraktywny, zautomatyzowany sposób.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było tworzyć aplikacje lub użytkowników, potrzebna jest dzierżawa Azure AD B2C. [Utwórz dzierżawę Azure Active Directory B2C](tutorial-create-tenant.md), jeśli jeszcze jej nie masz.

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Gdy masz dzierżawę Azure AD B2C, musisz zarejestrować swoją aplikację zarządzania przy użyciu [Azure Portal](https://portal.azure.com). Należy również przyznać mu uprawnienia wymagane do wykonywania zadań zarządzania w imieniu zautomatyzowanego skryptu lub aplikacji do zarządzania.

### <a name="register-application-in-azure-active-directory"></a>Zarejestruj aplikację w Azure Active Directory

Aby korzystać z interfejs API programu Graph usługi Azure AD z dzierżawą B2C, należy zarejestrować aplikację za pomocą przepływu pracy rejestracji aplikacji Azure Active Directory.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Przypisywanie uprawnień dostępu do interfejsu API

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Utwórz klucz tajny klienta

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Masz teraz aplikację, która ma uprawnienia do *tworzenia*, *odczytywania*i *aktualizowania* użytkowników w dzierżawie Azure AD B2C. Przejdź do następnej sekcji, aby dodać uprawnienia do *usuwania* i *aktualizowania hasła* użytkownika.

## <a name="add-user-delete-and-password-update-permissions"></a>Dodawanie uprawnień do usuwania i aktualizowania hasła użytkownika

Przyznane wcześniej uprawnienie *Odczyt i zapis danych katalogu* **nie obejmuje możliwości** usuwania użytkowników ani aktualizowania ich haseł.

Jeśli chcesz, aby aplikacja mogła usuwać użytkowników lub aktualizować hasła, musisz przyznać mu rolę *administratora* .

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **Zarządzanie**wybierz pozycję **role i Administratorzy**.
1. Wybierz rolę **administratora użytkowników** .
1. Wybierz pozycję **Dodaj przypisanie**.
1. W polu tekstowym **Wybierz** wpisz nazwę wcześniej zarejestrowanej aplikacji, na przykład *managementapp1*. Wybierz aplikację, gdy zostanie ona wyświetlona w wynikach wyszukiwania.
1. Wybierz pozycję **Dodaj**. Pełne propagowanie uprawnień może potrwać kilka minut.

Aplikacja Azure AD B2C ma teraz dodatkowe uprawnienia wymagane do usuwania użytkowników lub aktualizowania ich haseł w dzierżawie B2C.

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

Przykładowy kod jest aplikacją konsolową .NET, która używa [Active Directory Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) do współdziałania z usługą Azure AD interfejs API programu Graph. W jego kodzie pokazano, jak wywołać interfejs API, aby programowo zarządzać użytkownikami w dzierżawie Azure AD B2C.

Możesz [pobrać przykładowe archiwum](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) lub sklonować repozytorium GitHub:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Po uzyskaniu przykładowego kodu skonfiguruj go dla środowiska, a następnie Skompiluj projekt:

1. Otwórz rozwiązanie `B2CGraphClient\B2CGraphClient.sln` w programie Visual Studio.
1. W projekcie **B2CGraphClient** Otwórz plik *App. config* .
1. Zastąp sekcję `<appSettings>` następującym kodem XML. Następnie zastąp `{your-b2c-tenant}` nazwą dzierżawy, a `{Application ID}` i `{Client secret}` wartościami zarejestrowanymi wcześniej.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Skompiluj rozwiązanie. Kliknij prawym przyciskiem myszy rozwiązanie **B2CGraphClient** w Eksplorator rozwiązań, a następnie wybierz pozycję **Skompiluj ponownie rozwiązanie**.

Jeśli kompilacja zakończy się pomyślnie, aplikacja konsoli `B2C.exe` można znaleźć w `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod

Aby użyć B2CGraphClient, Otwórz wiersz polecenia (`cmd.exe`) i przejdź do katalogu `Debug` projektu. Następnie uruchom polecenie `B2C Help`.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Polecenie `B2C Help` wyświetla Krótki opis dostępnych podpoleceń. Przy każdym wywołaniu jednego z jego podpoleceń `B2CGraphClient` wysyła żądanie do interfejs API programu Graph usługi Azure AD.

W poniższych sekcjach omówiono, jak kod aplikacji wykonuje wywołania do interfejs API programu Graph usługi Azure AD.

### <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Każde żądanie do interfejs API programu Graph usługi Azure AD wymaga tokenu dostępu do uwierzytelniania. `B2CGraphClient` używa Active Directory Authentication Library Open Source (ADAL), aby pomóc w uzyskaniu tokenów dostępu. Biblioteka ADAL ułatwia pozyskiwanie tokenów, dostarczając interfejs API pomocnika i poświęcając kilka ważnych informacji, takich jak buforowanie tokenów dostępu. Nie trzeba jednak używać biblioteki ADAL do uzyskiwania tokenów. Zamiast tego można uzyskać tokeny, ręcznie podając żądania HTTP.

> [!NOTE]
> Aby uzyskać tokeny dostępu, które mogą być używane z interfejs API programu Graph usługi Azure AD, należy użyć biblioteki ADAL v2 lub nowszej. Nie można użyć biblioteki ADAL v1.

Gdy `B2CGraphClient` wykonuje, tworzy wystąpienie klasy `B2CGraphClient`. Konstruktor dla tej klasy ustawia szkielet uwierzytelniania ADAL:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Użyjmy przykładowego polecenia `B2C Get-User`.

Gdy `B2C Get-User` jest wywoływana bez dodatkowych argumentów, aplikacja wywołuje metodę `B2CGraphClient.GetAllUsers()`. `GetAllUsers()` następnie wywoła `B2CGraphClient.SendGraphGetRequest()`, które przesyła żądanie HTTP GET do interfejs API programu Graph usługi Azure AD. Zanim `B2CGraphClient.SendGraphGetRequest()` wyśle żądanie GET, najpierw uzyskuje token dostępu przy użyciu biblioteki ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

Możesz uzyskać token dostępu dla interfejs API programu Graph, wywołując metodę ADAL `AuthenticationContext.AcquireToken()`. Biblioteka ADAL zwraca następnie `access_token`, która reprezentuje tożsamość aplikacji.

### <a name="read-users"></a>Odczytuj użytkowników

Jeśli chcesz uzyskać listę użytkowników lub uzyskać określonego użytkownika z interfejs API programu Graph usługi Azure AD, możesz wysłać żądanie HTTP `GET` do punktu końcowego `/users`. Żądanie dla wszystkich użytkowników w dzierżawie wygląda następująco:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Aby wyświetlić to żądanie, uruchom polecenie:

 ```cmd
 B2C Get-User
 ```

Istnieją dwie ważne kwestie, na które należy zwrócić uwagę:

* Token dostępu uzyskany przy użyciu biblioteki ADAL został dodany do nagłówka `Authorization` przy użyciu schematu `Bearer`.
* W przypadku dzierżawców B2C należy użyć parametru zapytania `api-version=1.6`.

Obie te szczegóły są obsługiwane w metodzie `B2CGraphClient.SendGraphGetRequest()`:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Tworzenie kont użytkowników konsumentów

Podczas tworzenia kont użytkowników w dzierżawie B2C można wysłać żądanie HTTP `POST` do punktu końcowego `/users`. Poniższe żądanie HTTP `POST` pokazuje przykład użytkownika, który ma zostać utworzony w dzierżawie.

Większość właściwości w podanym żądaniu jest wymaganych do tworzenia użytkowników-klientów. `//` Komentarze zostały dołączone do ilustracji — nie Uwzględniaj ich w rzeczywistym żądaniu.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Aby zobaczyć żądanie, Uruchom jedno z następujących poleceń:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

Polecenie `Create-User` przyjmuje jako parametr wejściowy plik JSON zawierający reprezentację obiektu użytkownika w formacie JSON. Przykład kodu zawiera dwa przykładowe pliki JSON: `usertemplate-email.json` i `usertemplate-username.json`. Pliki te można modyfikować zgodnie z potrzebami. Oprócz wymaganych pól powyżej w plikach są zawarte kilka opcjonalnych pól.

Aby uzyskać więcej informacji na temat wymaganych i opcjonalnych pól, zobacz [Informacje o jednostkach i typach złożonych | Odwołanie interfejs API programu Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Możesz zobaczyć, jak to żądanie POST zostało skonstruowane w `B2CGraphClient.SendGraphPostRequest()`:

* Dołącza token dostępu do nagłówka `Authorization` żądania.
* Ustawia `api-version=1.6`.
* Zawiera obiekt użytkownika JSON w treści żądania.

> [!NOTE]
> Jeśli konta, które mają zostać zmigrowane z istniejącego magazynu użytkowników, mają niższą siłę hasła niż [siła silnego hasła wymuszone przez Azure AD B2C](user-flow-password-complexity.md), można wyłączyć wymaganie silnego hasła, używając `DisableStrongPassword` wartości we właściwości `passwordPolicies`. Na przykład można zmodyfikować poprzednie żądanie utworzenia użytkownika w następujący sposób: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Aktualizowanie kont użytkowników indywidualnych

Podczas aktualizacji obiektów użytkownika proces jest podobny do tego, który jest używany do tworzenia obiektów użytkownika, ale używa metody `PATCH` HTTP:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Spróbuj zaktualizować użytkownika, modyfikując pewne wartości w plikach JSON, a następnie użyj `B2CGraphClient`, aby uruchomić jedno z następujących poleceń:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Sprawdź metodę `B2CGraphClient.SendGraphPatchRequest()`, aby uzyskać szczegółowe informacje na temat wysyłania tego żądania.

### <a name="search-users"></a>Wyszukiwania użytkowników

Możesz wyszukać użytkowników w dzierżawie usługi B2C w następujący sposób:

* Odwołuje się do **identyfikatora obiektu**użytkownika.
* Odwołuje się do identyfikator logowania, właściwości `signInNames`.
* Odwołuje się do żadnego z prawidłowych parametrów OData. Na przykład "imięname", "nazwisko", "displayName" itd.

Uruchom jedno z następujących poleceń, aby wyszukać użytkownika:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Na przykład:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, użyj metody HTTP `DELETE` i Skonstruuj adres URL przy użyciu identyfikatora obiektu użytkownika:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Aby zobaczyć przykład, wprowadź to polecenie i Wyświetl żądanie usuwania, które jest wydrukowane w konsoli programu:

```cmd
B2C Delete-User <object-id-of-user>
```

Sprawdź metodę `B2CGraphClient.SendGraphDeleteRequest()`, aby uzyskać szczegółowe informacje na temat wysyłania tego żądania.

Oprócz zarządzania użytkownikami można wykonać wiele innych akcji z interfejs API programu Graph usługi Azure AD. [Dokumentacja usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/api/api-catalog) zawiera szczegółowe informacje o każdej akcji, a także przykładowe żądania.

## <a name="use-custom-attributes"></a>Używanie atrybutów niestandardowych

Większość aplikacji konsumenckich musi przechowywać niektóre typy niestandardowych informacji o profilu użytkownika. Jednym ze sposobów jest zdefiniowanie atrybutu niestandardowego w dzierżawie B2C. Następnie można traktować ten atrybut w taki sam sposób, w jaki traktujesz każdą inną właściwość obiektu użytkownika. Można zaktualizować atrybut, usunąć atrybut, zapytanie według atrybutu, wysłać atrybut jako rolę w tokenach logowania i nie tylko.

Aby zdefiniować atrybut niestandardowy w dzierżawie B2C, zobacz odwołanie do [atrybutu niestandardowego B2C](user-flow-custom-attributes.md).

Atrybuty niestandardowe zdefiniowane w dzierżawie B2C można wyświetlić za pomocą następujących poleceń `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Dane wyjściowe ujawniają szczegóły każdego z atrybutów niestandardowych. Na przykład:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Możesz użyć pełnej nazwy, takiej jak `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, jako właściwości obiektów użytkownika. Zaktualizuj plik JSON przy użyciu nowej właściwości i wartości właściwości, a następnie uruchom polecenie:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Następne kroki

Za pomocą `B2CGraphClient`masz aplikację usługi, która może programowo zarządzać użytkownikami dzierżawy B2C. `B2CGraphClient` używa własnej tożsamości aplikacji do uwierzytelniania w usłudze Azure AD interfejs API programu Graph. Uzyskuje także tokeny przy użyciu klucza tajnego klienta.

Po dodaniu tej funkcji do własnej aplikacji Pamiętaj kilka najważniejszych punktów dla aplikacji B2C:

* Przyznaj aplikacji wymagane uprawnienia w dzierżawie.
* Gdy wywołasz interfejs API programu Graph, użyj `api-version=1.6`.
* Podczas tworzenia i aktualizowania użytkowników konsumentów wymagane są pewne właściwości, zgodnie z powyższym opisem.
