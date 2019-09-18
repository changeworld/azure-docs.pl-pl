---
title: Użyj interfejs API programu Graph w Azure Active Directory B2C | Microsoft Docs
description: Jak wywoływać interfejs API programu Graph dzierżawy B2C przy użyciu tożsamości aplikacji w celu zautomatyzowania procesu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7fcbbbfcc2192160ca852538c015a365518e448
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065943"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Korzystanie z interfejsu API Graph usługi Azure AD

>[!NOTE]
> Aby zarządzać użytkownikami w katalogu Azure AD B2C, należy użyć [interfejs API programu Graph usługi Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) . Różni się to od interfejsu API Microsoft Graph. Więcej informacji można znaleźć [tutaj](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Dzierżawy Azure Active Directory B2C (Azure AD B2C) mogą być bardzo duże. Oznacza to, że wiele typowych zadań zarządzania dzierżawcami należy wykonać programowo. Podstawowym tego przykładem jest zarządzanie użytkownikami. Może być konieczne przeprowadzenie migracji istniejącego magazynu użytkownika do dzierżawy B2C. Możesz zdecydować się na hostowanie rejestracji użytkowników na własnej stronie i tworzenie kont użytkowników w katalogu usługi Azure AD B2C w tle. Zadania tego typu wymagają możliwości tworzenia, odczytywania, aktualizowania i usuwania kont użytkowników. Te zadania można wykonać przy użyciu interfejs API programu Graph usługi Azure AD.

W przypadku dzierżawców B2C istnieją dwa podstawowe tryby komunikacji z interfejs API programu Graph.

* W przypadku interakcyjnych zadań uruchamianych jednokrotnie należy działać jako konto administratora w dzierżawie B2C podczas wykonywania zadań podrzędnych. Ten tryb wymaga od administratora zalogowania się przy użyciu poświadczeń, aby administrator mógł wykonać jakiekolwiek wywołania do interfejs API programu Graph.
* W przypadku zautomatyzowanych, ciągłych zadań należy używać określonego typu konta usługi z uprawnieniami do wykonywania zadań zarządzania. W usłudze Azure AD można to zrobić przez zarejestrowanie aplikacji i uwierzytelnienie w usłudze Azure AD. Jest to realizowane przy użyciu **identyfikatora aplikacji** , który używa [przyznania poświadczeń klienta OAuth 2,0](../active-directory/develop/service-to-service.md). W takim przypadku aplikacja działa jako sama, a nie jako użytkownik, aby wywołać interfejs API programu Graph.

W tym artykule dowiesz się, jak wykonać zautomatyzowany przypadek użycia. Utworzysz platformę .NET 4,5 `B2CGraphClient` , która wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) użytkownika. Klient będzie miał interfejs wiersza polecenia (CLI) systemu Windows, który umożliwia wywoływanie różnych metod. Jednak kod jest zapisywana w taki sposób, aby zachowywać się w nieinteraktywny, zautomatyzowany sposób.

## <a name="get-an-azure-ad-b2c-tenant"></a>Uzyskiwanie dzierżawy Azure AD B2C
Aby można było tworzyć aplikacje lub użytkowników, potrzebna jest dzierżawa Azure AD B2C. Jeśli nie masz już dzierżawy, [Rozpocznij pracę z Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Rejestrowanie aplikacji w dzierżawie
Po utworzeniu dzierżawy usługi B2C należy zarejestrować aplikację przy użyciu [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Aby użyć interfejs API programu Graph z dzierżawcą usługi B2C, musisz zarejestrować aplikację przy użyciu usługi *rejestracji aplikacji* w menu Azure Portal, a **nie** w *aplikacjach* Azure AD B2C's. Poniższe instrukcje prowadzą do odpowiedniego menu. Nie można ponownie użyć istniejących aplikacji B2C zarejestrowanych w menu *aplikacji* usługi Azure AD B2C's.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz dzierżawę Azure AD B2C, wybierając swoje konto w prawym górnym rogu strony.
3. W okienku nawigacji po lewej stronie wybierz pozycję **wszystkie usługi**, kliknij pozycję **rejestracje aplikacji**, a następnie kliknij pozycję **Nowa rejestracja**.
4. Postępuj zgodnie z monitami i utwórz nową aplikację.
    1. Dodaj odpowiednią nazwę
    2. Wybierz **konta tylko w tym katalogu organizacji**
    3. Wybierz pozycję **Sieć Web** jako typ aplikacji, a następnie podaj **adres URL logowania** (np `https://B2CGraphAPI`.), ponieważ nie dotyczy tego przykładu.
    4. Kliknij pozycję Zarejestruj.
5. Aplikacja zostanie teraz wyświetlona na liście aplikacji, kliknij ją, aby uzyskać **Identyfikator aplikacji** (znany również jako identyfikator klienta). Skopiuj ją tak, jak będzie potrzebna w dalszej części.
6. W menu Ustawienia kliknij pozycję **certyfikaty & wpisy tajne**.
7. W sekcji wpisy tajne **klienta** kliknij **Nowy wpis tajny klienta**, podaj opis wpisu tajnego i wybierz czas trwania, a następnie kliknij przycisk **Dodaj**. Skopiuj wartość klucza tajnego (znanego również jako klucz tajny klienta) do użycia w dalszej części.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Skonfiguruj uprawnienia do tworzenia, odczytu i aktualizacji dla aplikacji
Teraz musisz skonfigurować aplikację, aby uzyskać wszystkie wymagane uprawnienia do tworzenia, odczytywania, aktualizowania i usuwania użytkowników.

1. Kontynuuj w menu Azure Portal rejestracje aplikacji wybierz swoją aplikację.
2. W menu Ustawienia kliknij pozycję **wymagane uprawnienia**.
3. W menu wymagane uprawnienia kliknij pozycję **Windows Azure Active Directory**.
4. W menu Włącz dostęp wybierz uprawnienie **Odczyt i zapis danych katalogu** z **uprawnień aplikacji** , a następnie kliknij przycisk **Zapisz**.
5. Na koniec przejdź do menu wymagane uprawnienia, a następnie kliknij przycisk **Udziel uprawnień** .

Masz teraz aplikację, która ma uprawnienia do tworzenia, odczytywania i aktualizowania użytkowników z dzierżawy usługi B2C.

> [!NOTE]
> Przyznanie uprawnień może potrwać kilka minut.
>
>

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Konfigurowanie uprawnień do usuwania lub aktualizowania hasła dla aplikacji
Obecnie uprawnienia do *odczytu i zapisu danych katalogu* **nie** obejmują możliwości usuwania użytkowników ani aktualizowania haseł użytkowników. Jeśli chcesz, aby aplikacja mogła usuwać użytkowników lub aktualizować hasła, musisz wykonać te dodatkowe kroki, które obejmują program PowerShell. w przeciwnym razie możesz przejść do następnej sekcji.

Po pierwsze, jeśli nie jest jeszcze zainstalowany, zainstaluj [moduł Azure AD PowerShell V1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Po zainstalowaniu modułu programu PowerShell Połącz się z dzierżawą Azure AD B2C.

> [!IMPORTANT]
> Musisz użyć konta administratora dzierżawy B2C, które jest **lokalne** dla dzierżawy B2C. Te konta wyglądają następująco: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Teraz użyjemy **identyfikatora aplikacji** w skrypcie poniżej, aby przypisać aplikacji rolę administratora konta użytkownika. Role te mają dobrze znane identyfikatory, więc wystarczy wprowadzić **Identyfikator aplikacji** w poniższym skrypcie.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Aplikacja ma teraz również uprawnienia do usuwania użytkowników lub aktualizowania haseł z dzierżawy usługi B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Pobieranie, Konfigurowanie i kompilowanie przykładowego kodu
Najpierw Pobierz przykładowy kod i poproś go o uruchomienie. Następnie zajmiemy się tym lepiej.  [Przykładowy kod można pobrać jako plik. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Można również sklonować go do wybranego katalogu:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Otwórz rozwiązanie `B2CGraphClient\B2CGraphClient.sln` Visual Studio w programie Visual Studio. W projekcie Otwórz plik `App.config`. `B2CGraphClient` Zastąp trzy ustawienia aplikacji własnymi wartościami:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Następnie kliknij prawym przyciskiem `B2CGraphClient` myszy rozwiązanie i Skompiluj ponownie przykład. Jeśli się powiedzie, powinien teraz `B2C.exe` znajdować się plik wykonywalny w programie. `B2CGraphClient\bin\Debug`

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Kompiluj operacje CRUD użytkownika przy użyciu interfejs API programu Graph
Aby użyć B2CGraphClient, Otwórz `cmd` wiersz polecenia systemu Windows i zmień katalog `Debug` na katalog. Następnie uruchom `B2C Help` polecenie.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Spowoduje to wyświetlenie krótkiego opisu każdego polecenia. Przy każdym wywołaniu jednego z tych poleceń program `B2CGraphClient` wysyła żądanie do interfejs API programu Graph usługi Azure AD.

### <a name="get-an-access-token"></a>Pobranie tokenu dostępu
Każde żądanie do interfejs API programu Graph wymaga tokenu dostępu do uwierzytelniania. `B2CGraphClient`używa Active Directory Authentication Library Open Source (ADAL) w celu uzyskania tokenów dostępu. Biblioteka ADAL ułatwia pozyskiwanie tokenów przez zapewnienie prostego interfejsu API i podjęcie pewnych ważnych informacji, takich jak buforowanie tokenów dostępu. Nie trzeba używać biblioteki ADAL do uzyskiwania tokenów, chociaż. Możesz również uzyskać tokeny, wysyłając żądania HTTP.

> [!NOTE]
> Ten przykładowy kod używa biblioteki ADAL v2 w celu komunikowania się z interfejs API programu Graph.  Aby uzyskać tokeny dostępu, które mogą być używane z interfejs API programu Graph usługi Azure AD, należy użyć biblioteki ADAL v2 lub v3.
>
>

Po `B2CGraphClient` uruchomieniu tworzy wystąpienie `B2CGraphClient` klasy. Konstruktor dla tej klasy konfiguruje szkielet uwierzytelniania ADAL:

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

Użyjemy przykładowego `B2C Get-User` polecenia. Gdy `B2C Get-User` jest wywoływana bez żadnych dodatkowych danych wejściowych, interfejs wiersza polecenia `B2CGraphClient.GetAllUsers(...)` wywołuje metodę. Ta metoda wywołuje `B2CGraphClient.SendGraphGetRequest(...)`, która przesyła żądanie HTTP GET do interfejs API programu Graph. Przed `B2CGraphClient.SendGraphGetRequest(...)` wysłaniem żądania GET najpierw pobiera token dostępu przy użyciu biblioteki ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Możesz uzyskać token dostępu dla interfejs API programu Graph, wywołując metodę ADAL `AuthenticationContext.AcquireToken(...)` . Biblioteka ADAL zwraca `access_token` następnie, która reprezentuje tożsamość aplikacji.

### <a name="read-users"></a>Odczytuj użytkowników
Aby uzyskać listę użytkowników lub uzyskać określonego użytkownika z interfejs API programu Graph, można wysłać żądanie HTTP `GET` `/users` do punktu końcowego. Żądanie dla wszystkich użytkowników w dzierżawie wygląda następująco:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Aby wyświetlić to żądanie, uruchom polecenie:

 ```cmd
 B2C Get-User
 ```

Istnieją dwie ważne kwestie, na które należy zwrócić uwagę:

* Token dostępu uzyskany za pośrednictwem biblioteki ADAL zostanie `Authorization` dodany do nagłówka przy `Bearer` użyciu schematu.
* W przypadku dzierżawców B2C należy użyć parametru `api-version=1.6`zapytania.

Obie te szczegóły są obsługiwane w `B2CGraphClient.SendGraphGetRequest(...)` metodzie:

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
Podczas tworzenia kont użytkowników w dzierżawie B2C można wysłać żądanie HTTP `POST` `/users` do punktu końcowego:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Większość z tych właściwości w tym żądaniu jest wymagana do tworzenia użytkowników-klientów. Aby dowiedzieć się więcej, kliknij [tutaj](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Zwróć uwagę, `//` że komentarze zostały dołączone do ilustracji. Nie należy uwzględniać ich w rzeczywistym żądaniu.

Aby zobaczyć żądanie, Uruchom jedno z następujących poleceń:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Polecenie przyjmuje plik. JSON jako parametr wejściowy. Zawiera reprezentację obiektu użytkownika w formacie JSON. W przykładowym kodzie znajdują się dwa przykładowe pliki JSON: `usertemplate-email.json` i `usertemplate-username.json`. Pliki te można modyfikować zgodnie z potrzebami. Oprócz wymaganych pól powyżej kilka opcjonalnych pól, których można użyć, jest dołączonych do tych plików. Szczegóły dotyczące pól opcjonalnych można znaleźć w [dokumentacji jednostki usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

Można zobaczyć, jak jest konstruowany `B2CGraphClient.SendGraphPostRequest(...)`żądanie post.

* Dołącza token dostępu do `Authorization` nagłówka żądania.
* Zestawy `api-version=1.6`IT.
* Zawiera obiekt użytkownika JSON w treści żądania.

> [!NOTE]
> Jeśli konta, które mają zostać zmigrowane z istniejącego magazynu użytkowników, mają niższą siłę hasła niż [siła silnego hasła wymuszonego przez Azure AD B2C](/previous-versions/azure/jj943764(v=azure.100)), można wyłączyć wymaganie silnego hasła przy `DisableStrongPassword` użyciu wartości z `passwordPolicies`właściwość. Na przykład można zmodyfikować poniższe żądanie utworzenia użytkownika: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
>
>

### <a name="update-consumer-user-accounts"></a>Aktualizowanie kont użytkowników indywidualnych
Podczas aktualizacji obiektów użytkownika proces jest podobny do tego, który jest używany do tworzenia obiektów użytkownika. Ale ten proces używa metody http `PATCH` :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Spróbuj zaktualizować użytkownika, aktualizując pliki JSON przy użyciu nowych danych. Można następnie użyć `B2CGraphClient` do uruchomienia jednego z następujących poleceń:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Sprawdź metodę `B2CGraphClient.SendGraphPatchRequest(...)` , aby uzyskać szczegółowe informacje na temat wysyłania tego żądania.

### <a name="search-users"></a>Wyszukaj użytkowników
Użytkowników w dzierżawie usługi B2C można wyszukiwać na kilka sposobów. Jeden, przy użyciu identyfikatora obiektu użytkownika lub dwóch, przy użyciu Identyfikator logowania użytkownika (tj. `signInNames` właściwości).

Uruchom jedno z następujących poleceń, aby wyszukać określonego użytkownika:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Oto kilka przykładów:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Usuwanie użytkowników
Proces usuwania użytkownika jest prosty. Użyj metody http `DELETE` i Skonstruuj adres URL przy użyciu poprawnego identyfikatora obiektu:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Aby zobaczyć przykład, wprowadź to polecenie i Wyświetl żądanie usuwania, które jest wydrukowane w konsoli programu:

```cmd
B2C Delete-User <object-id-of-user>
```

Sprawdź metodę `B2CGraphClient.SendGraphDeleteRequest(...)` , aby uzyskać szczegółowe informacje na temat wysyłania tego żądania.

Oprócz zarządzania użytkownikami można wykonać wiele innych akcji z interfejs API programu Graph usługi Azure AD. [Dokumentacja usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/api/api-catalog) zawiera szczegółowe informacje o każdej akcji, a także przykładowe żądania.

## <a name="use-custom-attributes"></a>Używanie atrybutów niestandardowych
Większość aplikacji konsumenckich musi przechowywać niektóre typy niestandardowych informacji o profilu użytkownika. Jednym ze sposobów jest zdefiniowanie atrybutu niestandardowego w dzierżawie B2C. Następnie można traktować ten atrybut w taki sam sposób, jak w przypadku każdej innej właściwości obiektu użytkownika. Można zaktualizować atrybut, usunąć atrybut, zapytanie według atrybutu, wysłać atrybut jako rolę w tokenach logowania i nie tylko.

Aby zdefiniować atrybut niestandardowy w dzierżawie B2C, zobacz odwołanie do [atrybutu niestandardowego B2C](active-directory-b2c-reference-custom-attr.md).

Atrybuty niestandardowe zdefiniowane w dzierżawie B2C można wyświetlić za pomocą polecenia `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Dane wyjściowe tych funkcji ujawniają szczegóły każdego z atrybutów niestandardowych, takie jak:

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

Możesz użyć pełnej nazwy, takiej jak `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, jako właściwości obiektu użytkownika.  Zaktualizuj plik JSON przy użyciu nowej właściwości i wartości właściwości, a następnie uruchom polecenie:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Za pomocą `B2CGraphClient`programu masz aplikację usługi, która może programowo zarządzać użytkownikami dzierżawy B2C. `B2CGraphClient`używa własnej tożsamości aplikacji do uwierzytelniania w usłudze Azure AD interfejs API programu Graph. Uzyskuje także tokeny przy użyciu klucza tajnego klienta. Po dodaniu tej funkcji do aplikacji należy pamiętać kilka najważniejszych punktów dla aplikacji B2C:

* Należy przyznać aplikacji odpowiednie uprawnienia w dzierżawie.
* Na razie musisz użyć biblioteki ADAL (nie MSAL), aby uzyskać tokeny dostępu. (Można również wysyłać komunikaty protokołu bezpośrednio, bez korzystania z biblioteki).
* Gdy wywołasz interfejs API programu Graph, użyj `api-version=1.6`.
* Podczas tworzenia i aktualizowania użytkowników konsumentów wymagane są pewne właściwości, zgodnie z powyższym opisem.

Jeśli masz jakieś pytania lub prośby o akcje, które chcesz wykonać przy użyciu interfejs API programu Graph w dzierżawie usługi B2C, pozostaw komentarz dotyczący tego artykułu lub zakwestionuj problem w repozytorium przykładowym kodu usługi GitHub.

