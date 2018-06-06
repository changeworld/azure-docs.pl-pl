---
title: Użyj interfejs API Graph w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Sposób wywołania interfejsu API programu Graph dla dzierżawy B2C przy użyciu tożsamości aplikacji aby zautomatyzować proces.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9bd50d454bf298e7965c5d2f9bcc82061685df46
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710461"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Usługa Azure AD B2C: Użyj Azure interfejs API Graph usługi AD

>[!NOTE]
> Należy użyć [interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) do zarządzania użytkownikami w katalogu usługi Azure AD B2C. Różni się to od interfejsu API programu Microsoft Graph. Więcej informacji można znaleźć [tutaj](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Dzierżaw usługi Azure Active Directory (Azure AD) B2C zazwyczaj bardzo duże. Oznacza to, że wiele typowych zadań zarządzania dzierżawy należy wykonać programowo. Podstawowy przykład to zarządzanie użytkownikami. Konieczne może być migrację istniejącego magazynu użytkownika do dzierżawy B2C. Możesz udostępniać rejestracja użytkownika na stronie i tworzenia kont użytkowników w katalogu usługi Azure AD B2C w tle. Te typy zadań muszą mieć możliwość tworzenia, odczytu, aktualizacji i usuwania kont użytkowników. Można wykonywać te zadania przy użyciu interfejsu API Azure AD Graph.

Dla dzierżawcy usługi B2C istnieją dwa tryby głównej komunikowania się z interfejsu API programu Graph.

* Interaktywny, jednokrotnego uruchamiania zadań powinny działać jako konto administratora w dzierżawie usługi B2C należy wykonać zadania. Ten tryb wymaga administratorem, aby zalogować się przy użyciu poświadczeń, zanim które administrator może wykonać wszelkie wywołania interfejsu API programu Graph.
* Zautomatyzowane, ciągłe zadania należy używać pewien typ konta usługi, które dostarczają niezbędne uprawnienia do wykonywania zadań zarządzania. W usłudze Azure AD możesz to zrobić, rejestrowanie aplikacji i uwierzytelniania w usłudze Azure AD. Jest to zrobić za pomocą **identyfikator aplikacji** używającą [przyznania poświadczeń klienta OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). W takim przypadku aplikacja działa jako sam nie jako użytkownik, do wywołania interfejsu API programu Graph.

W tym artykule Dowiedz się jak wykonać w przypadku użycia automatycznego. Będzie kompilacji platformy .NET 4.5 `B2CGraphClient` wykonująca użytkownika tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacji. Klient ma interfejs wiersza polecenia systemu Windows (CLI) umożliwiający różne metody invoke. Jednak kod jest zapisywany zachowują się w sposób nieinteraktywne, automatycznej.

## <a name="get-an-azure-ad-b2c-tenant"></a>Uzyskiwanie dzierżawy usługi Azure AD B2C
Przed utworzeniem aplikacji lub użytkowników, należy dzierżawy usługi Azure AD B2C. Jeśli nie masz już, dzierżawcy [wprowadzenie do usługi Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Zarejestrować aplikację w dzierżawie
Po umieszczeniu dzierżawy B2C, należy zarejestrować swojej aplikacji za pomocą [portalu Azure](https://portal.azure.com).

> [!IMPORTANT]
> Aby używać interfejsu API programu Graph z dzierżawy usługi B2C, należy zarejestrować aplikację przy użyciu *rejestracji aplikacji* w portalu Azure **nie** usługi Azure AD B2C *aplikacji*menu. Poniższe instrukcje prowadzić do menu masz. Nie można ponownie użyć istniejących aplikacji B2C, które są zarejestrowane w usłudze Azure AD B2C w *aplikacji* menu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz dzierżawy usługi Azure AD B2C, wybierając konto w prawym górnym rogu strony.
3. W okienku nawigacji po lewej stronie wybierz **wszystkie usługi**, kliknij przycisk **rejestracji aplikacji**i kliknij przycisk **Dodaj**.
4. Postępuj zgodnie z monitami i utwórz nową aplikację. 
    1. Wybierz **aplikacji w sieci Web / interfejs API** jako typ aplikacji.    
    2. Podaj **żadnych adres URL logowania** (np. https://B2CGraphAPI) jako nie jest to potrzebne w tym przykładzie.  
5. Aplikacja zostanie teraz zostanie wyświetlona lista aplikacji, kliknij przycisk w celu uzyskania **identyfikator aplikacji** (znanej także jako identyfikator klienta). Skopiuj go, ponieważ będzie on potrzebny w dalszej części artykułu.
6. W menu Ustawienia kliknij **klucze**.
7. W **hasła** sekcji, wprowadź opis klucza i wybierz czas trwania, a następnie kliknij **zapisać**. Skopiuj wartość klucza (znanej także jako klucz tajny klienta) do użycia w dalszej części artykułu.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Skonfiguruj tworzenie, odczytywanie i aktualizowanie uprawnień dla aplikacji
Teraz musisz skonfigurować aplikację, aby pobrać wszystkie wymagane uprawnienia do tworzenia, odczytu, aktualizacji i usuwania użytkowników.

1. Kontynuowanie w menu rejestracji aplikacji w portalu Azure, wybierz aplikację.
2. W menu Ustawienia kliknij **wymagane uprawnienia**.
3. W menu wymaganych uprawnień, kliknij polecenie **Windows Azure Active Directory**.
4. Wybierz z menu Włącz dostęp **Odczyt i zapis danych katalogu** zgody **uprawnienia aplikacji** i kliknij przycisk **zapisać**.
5. Ponadto w menu wymaganych uprawnień, kliknij na **udzielanie uprawnień** przycisku.

Teraz masz aplikację, która ma uprawnienia do tworzenia, odczytu i aktualizacji użytkowników z dzierżawy usługi B2C.

> [!NOTE]
> Udzielanie uprawnień należy zająć kilka minut na pełne przetworzenie.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Skonfiguruj uprawnienia do usuwania aplikacji
Obecnie *Odczyt i zapis danych katalogu* uprawnienie jest **nie** obejmują możliwość czy żadnych usunięć, takich jak usuwanie użytkowników. Jeśli chcesz nadać aplikacji możliwość usunięcia użytkowników, należy wykonać te dodatkowe kroki, które wymagają programu PowerShell, w przeciwnym razie możesz przejść do następnej sekcji.

Po pierwsze, jeśli nie masz już zainstalowany, zainstaluj [modułu Azure AD PowerShell v1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Po zainstalowaniu modułu programu PowerShell połączyć się z dzierżawy usługi Azure AD B2C.

> [!IMPORTANT]
> Należy użyć konta administratora dzierżawy B2C, która jest **lokalnego** do dzierżawy B2C. Te konta wyglądać następująco: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Teraz użyjemy **identyfikator aplikacji** w skrypcie poniżej, aby przypisać tę aplikację z rolą administratora konta użytkownika, która pozwoli na usuwanie użytkowników. Te role mają dobrze znane identyfikatory, więc wszystko co należy zrobić danych wejściowych użytkownika **identyfikator aplikacji** w poniższym skrypcie.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Teraz aplikacja ma również uprawnienia do usuwania użytkowników z dzierżawy usługi B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Pobrać, skonfigurować i utworzyć przykładowy kod
Najpierw należy pobrać przykładowy kod i będzie działać. Następnie firma Microsoft podejmie bliższe spojrzenie na go.  Możesz [pobrać przykładowy kod w pliku .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Można również sklonować go w wybranym katalogu:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Otwórz `B2CGraphClient\B2CGraphClient.sln` rozwiązania Visual Studio w programie Visual Studio. W `B2CGraphClient` projekt, otwórz plik `App.config`. Zastąp ustawienia aplikacji trzy własne wartości:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Następnie kliknij prawym przyciskiem myszy `B2CGraphClient` rozwiązania i skompiluj ponownie próbki. W przypadku pomyślnego, po wykonaniu `B2C.exe` plik wykonywalny znajduje się w `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Tworzenie operacji CRUD użytkownika przy użyciu interfejsu API programu Graph
Aby użyć B2CGraphClient, otwórz `cmd` poleceń systemu Windows, w wierszu polecenia i zmień katalog na `Debug` katalogu. Następnie uruchom `B2C Help` polecenia.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Spowoduje to wyświetlenie krótki opis każdego polecenia. Zawsze jednego z tych poleceń, wywołaj `B2CGraphClient` zgłasza żądanie do interfejsu API programu Azure AD Graph.

### <a name="get-an-access-token"></a>Pobranie tokenu dostępu
Wszystkie żądania interfejsu API programu Graph wymaga tokenu dostępu do uwierzytelniania. `B2CGraphClient` korzysta open source Active Directory Authentication Library (ADAL), aby uzyskać tokeny dostępu. Biblioteka ADAL ułatwia tokenu nabycia, zapewniając prostych interfejsu API i Dbamy o pewne ważne informacje, takie jak buforowanie tokenów dostępu. Nie masz uzyskać tokenów, jednak przy użyciu biblioteki ADAL. Można również uzyskać tokeny przez obsługuje tworzenie żądań HTTP.

> [!NOTE]
> Ten przykład kodu wykorzystuje ADAL w wersji 2, aby komunikować się z interfejsu API programu Graph.  Aby uzyskać tokenów dostępu, które mogą być używane z interfejsem API usługi Azure AD Graph, należy użyć ADAL w wersji 2 lub 3.
> 
> 

Gdy `B2CGraphClient` uruchomieniu tworzy wystąpienie `B2CGraphClient` klasy. Ustawia konstruktora dla tej klasy szkieletów uwierzytelniania ADAL:

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

Użyjemy `B2C Get-User` polecenie jako przykład. Gdy `B2C Get-User` jest wywoływana bez żadnych dodatkowych danych wejściowych, wywołania interfejsu wiersza polecenia `B2CGraphClient.GetAllUsers(...)` metody. Ta metoda wywołuje `B2CGraphClient.SendGraphGetRequest(...)`, który przesyła żądanie HTTP GET do interfejsu API programu Graph. Przed `B2CGraphClient.SendGraphGetRequest(...)` wysyła żądanie GET, należy go najpierw pobiera token dostępu za pomocą biblioteki ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Możesz też uzyskać dostęp do tokenu interfejsu API programu Graph przez wywołanie metody z biblioteki ADAL `AuthenticationContext.AcquireToken(...)` metody. Zwraca ADAL `access_token` reprezentujący tożsamości aplikacji.

### <a name="read-users"></a>Przeczytaj użytkowników
Jeśli chcesz uzyskać listę użytkowników lub pobrać danego użytkownika z interfejsu API programu Graph, możesz wysłać HTTP `GET` żądanie `/users` punktu końcowego. Żądanie dla wszystkich użytkowników w dzierżawie wygląda następująco:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Aby wyświetlić to żądanie, uruchom polecenie:

 ```cmd
 B2C Get-User
 ```

Istnieją dwa ważnych rzeczy do uwzględnienia:

* Token dostępu nabyte za pomocą biblioteki ADAL zostanie dodany do `Authorization` nagłówka przy użyciu `Bearer` schematu.
* Dla dzierżawcy usługi B2C, należy użyć parametru zapytania `api-version=1.6`.

Oba te informacje są obsługiwane w `B2CGraphClient.SendGraphGetRequest(...)` metody:

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

### <a name="create-consumer-user-accounts"></a>Tworzenie konta użytkownika
Podczas tworzenia kont użytkowników w dzierżawie usługi B2C, możesz wysłać HTTP `POST` żądanie `/users` punktu końcowego:

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

Większość tych właściwości w tym żądaniu są wymagane do utworzenia konsumentów. Aby dowiedzieć się więcej, kliknij przycisk [tutaj](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Należy pamiętać, że `//` komentarze zostały włączone na ilustracji. Nie należy wprowadzać ich w rzeczywistego żądania.

Aby wyświetlić żądanie, uruchom jedno z następujących poleceń:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Polecenie przyjmuje jako parametr wejściowy plik JSON. Zawiera reprezentacja JSON obiektu user. Istnieją dwa przykładowe pliki JSON w przykładowym kodzie: `usertemplate-email.json` i `usertemplate-username.json`. Można zmodyfikować te pliki, w zależności od potrzeb. Oprócz wymaganych pól powyżej kilka opcjonalne pola, które można użyć znajdują się w tych plikach. Szczegółowe informacje o opcjonalne pola znajdują się w [odwołania do jednostki interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Widać, jak żądania POST jest tworzony w `B2CGraphClient.SendGraphPostRequest(...)`.

* Dołącza token dostępu do `Authorization` nagłówka żądania.
* Ustawia `api-version=1.6`.
* Obejmuje on obiektu użytkownika w formacie JSON w treści żądania.

> [!NOTE]
> Jeśli konta, które chcesz przeprowadzić migrację z istniejącym magazynem użytkownika ma niższe siły hasła niż [siły silne hasło, wymuszane przez usługę Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), można wyłączyć przy użyciu wymaganie silne hasło `DisableStrongPassword` wartość w `passwordPolicies` właściwości. Na przykład można zmodyfikować żądanie użytkownika utworzenia podanego powyżej w następujący sposób: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Aktualizowanie kont użytkownika klienta
Podczas aktualizowania obiektów użytkowników, proces jest podobny do tego, który służy do tworzenia obiektów użytkowników. Ten proces korzysta z protokołu HTTP, ale `PATCH` metody:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Spróbuj zaktualizować użytkownika aktualizując nowych danych plików JSON. Następnie można użyć `B2CGraphClient` na jeden z tych poleceń:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Sprawdź `B2CGraphClient.SendGraphPatchRequest(...)` metody, aby uzyskać szczegółowe informacje dotyczące sposobu wysyłania tego żądania.

### <a name="search-users"></a>Wyszukiwania użytkowników
Możesz wyszukać użytkowników w dzierżawie usługi B2C na kilka sposobów. Obiekt go przy użyciu użytkownika Identyfikatora lub dwóch przy użyciu identyfikatora logowania użytkownika (tj. `signInNames` właściwości).

Uruchom jedno z poniższych poleceń, aby wyszukać określonego użytkownika:

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
Proces związanych z usuwaniem użytkowników jest prosta. Korzystanie z protokołu HTTP `DELETE` — metoda i konstrukcja do adresu URL poprawny identyfikator obiektu:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Na przykład, wprowadź następujące polecenie i wyświetlić żądanie usunięcia, używanego do konsoli:

```cmd
B2C Delete-User <object-id-of-user>
```

Sprawdź `B2CGraphClient.SendGraphDeleteRequest(...)` metody, aby uzyskać szczegółowe informacje dotyczące sposobu wysyłania tego żądania.

Można wykonywać wiele akcji przy użyciu interfejsu API Azure AD Graph oprócz Zarządzanie użytkownikami. [Dokumentacja interfejsu API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) podano szczegółowe informacje dotyczące każdej akcji, wraz z próbki żądań.

## <a name="use-custom-attributes"></a>Używanie atrybutów niestandardowych
Większość aplikacji klienta należy przechowywać pewien typ informacji o profilu użytkownika niestandardowego. Można to zrobić jednym ze sposobów jest zdefiniuj atrybut niestandardowy w dzierżawie usługi B2C. Ten atrybut można następnie traktować ten sam sposób traktować innych właściwości dla obiektu user. Można zaktualizować atrybutu, usuń atrybut, zapytania za pomocą atrybutu, wysyłanie atrybut jako oświadczenia w tokeny logowania i inne.

Aby zdefiniować atrybutu niestandardowego w dzierżawie usługi B2C, zobacz [odwołanie do atrybutu niestandardowego B2C](active-directory-b2c-reference-custom-attr.md).

Możesz wyświetlić atrybuty niestandardowe zdefiniowane w dzierżawie usługi B2C przy użyciu `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Dane wyjściowe z tych funkcji ujawnia szczegóły każdego z atrybutów niestandardowych, takich jak:

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

Można użyć pełnej nazwy, takie jak `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, jako właściwość obiektów użytkownika.  Zaktualizuj plik JSON o nowej właściwości i wartości dla właściwości, a następnie uruchom:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Za pomocą `B2CGraphClient`, masz aplikacji usługi, który można programowo zarządzać użytkownicy dzierżawy B2C. `B2CGraphClient` używa jego tożsamość aplikacji do uwierzytelniania interfejsu API programu Azure AD Graph. Uzyskuje on również tokenów przy użyciu klucza tajnego klienta. Ponieważ ta funkcja jest dołączyć do aplikacji, należy pamiętać o kilka najważniejszych dla aplikacji B2C:

* Należy przyznać odpowiednie uprawnienia w dzierżawie aplikacji.
* Na razie należy użyć biblioteki ADAL (nie MSAL) można uzyskać tokeny dostępu. (Możesz również wysłać wiadomości protokołu bezpośrednio, bez korzystania z biblioteki.)
* Po wywołaniu interfejsu API programu Graph, użyj `api-version=1.6`.
* Podczas tworzenia i aktualizowania użytkowników konsumenta, kilka właściwości są wymagane, zgodnie z powyższym opisem.

Jeśli masz pytania lub żądań dla akcji się, że chcesz przeprowadzić przy użyciu interfejsu API programu Graph na dzierżawę B2C, zostaw komentarz w tym artykule, lub plików problemu w repozytorium GitHub przykładowy kod.

