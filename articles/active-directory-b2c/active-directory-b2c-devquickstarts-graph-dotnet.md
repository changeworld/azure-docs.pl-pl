---
title: Użyj interfejsu API programu Graph w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak wywołać interfejs API programu Graph dla dzierżawy B2C przy użyciu tożsamości aplikacji do automatyzacji procesu.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0f380aa9f2efc1ae9636b7704f7eb75004bb71f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895058"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Korzystanie z interfejsu API Graph usługi Azure AD

>[!NOTE]
> Należy użyć [interfejsu API usługi Azure AD Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) Zarządzanie użytkownikami w katalogu usługi Azure AD B2C. To różni się od interfejsu API programu Microsoft Graph. Więcej informacji można znaleźć [tutaj](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Zwykle dzierżaw usługi Azure Active Directory (Azure AD) B2C bardzo duże. Oznacza to, że wiele typowych zadań zarządzania dzierżawy muszą być wykonywane programowo. Podstawowym tego przykładem jest zarządzanie użytkownikami. Konieczne może być migracja istniejącym magazynem użytkownika do dzierżawy usługi B2C. Możesz zdecydować się na hostowanie rejestracji użytkowników na własnej stronie i tworzenie kont użytkowników w katalogu usługi Azure AD B2C w tle. Zadania tego typu wymagają możliwości tworzenia, odczytywania, aktualizowania i usuwania kont użytkowników. Można wykonywać te zadania przy użyciu interfejsu API programu Graph usługi Azure AD.

W przypadku dzierżaw B2C są dostępne dwa tryby głównej komunikowania się z interfejsu API programu Graph.

* Interaktywny, jednokrotnego uruchamiania zadań powinna działać jako konto administratora dzierżawy usługi B2C należy wykonać zadania. Ten tryb wymaga administrator, aby zalogować się przy użyciu poświadczeń, zanim administratora mogą wykonywać wszelkie wywołania interfejsu API programu Graph.
* Ciągłego, zautomatyzowanych zadań należy użyć jakiegoś rodzaju konta usługi udostępnić uprawnienia niezbędne do wykonywania zadań administracyjnych. W usłudze Azure AD możesz to zrobić, rejestrowanie aplikacji i uwierzytelniania w usłudze Azure AD. Jest to wykonywane przy użyciu **identyfikator aplikacji** , który używa [przyznawanie poświadczeń klienta OAuth 2.0](../active-directory/develop/service-to-service.md). W takim przypadku aplikacja działa w swoim imieniu, nie jako użytkownik, do wywołania interfejsu API programu Graph.

W tym artykule dowiesz się, jak przeprowadzić przypadek użycia automatycznego. Będziesz tworzyć .NET 4.5 `B2CGraphClient` wykonująca użytkownika tworzenie, Odczyt, aktualizowanie i usuwanie operacji (CRUD). Klient będzie mieć interfejs wiersza polecenia Windows (interfejs wiersza polecenia), który umożliwia wykonywanie różnych metod. Jednak kod jest zapisywany zachowują się w sposób nie interaktywnego, zautomatyzowane.

## <a name="get-an-azure-ad-b2c-tenant"></a>Uzyskiwanie dzierżawy usługi Azure AD B2C
Przed utworzeniem aplikacji lub użytkowników należy dzierżawy usługi Azure AD B2C. Jeśli nie masz już dzierżawę [Rozpoczynanie pracy z usługą Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Zarejestruj swoją aplikację w dzierżawie
Po umieszczeniu dzierżawy usługi B2C należy zarejestrować swojej aplikacji za pomocą [witryny Azure portal](https://portal.azure.com).

> [!IMPORTANT]
> Aby używać interfejsu API programu Graph z dzierżawą B2C, należy zarejestrować aplikacji przy użyciu *rejestracje aplikacji* usługi w witrynie Azure portal **nie** usługi Azure AD B2C *aplikacje*menu. Zgodnie z poniższymi instrukcjami prowadzą do odpowiednich menu. Nie można ponownie użyć istniejących aplikacji B2C, które zarejestrowane w usłudze Azure AD B2C *aplikacje* menu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz dzierżawę usługi Azure AD B2C, wybierając swoje konto w prawym górnym rogu strony.
3. W okienku nawigacji po lewej stronie wybierz **wszystkich usług**, kliknij przycisk **rejestracje aplikacji**i kliknij przycisk **Dodaj**.
4. Postępuj zgodnie z monitami i utwórz nową aplikację. 
    1. Wybierz **aplikacja internetowa / interfejs API** jako typ aplikacji.    
    2. Podaj **wszelkie adres URL logowania** (np. `https://B2CGraphAPI`), ponieważ nie jest to potrzebne w tym przykładzie.  
5. Aplikacji będą teraz wyświetlane na liście aplikacji, kliknij go w celu uzyskania **identyfikator aplikacji** (znany także jako identyfikator klienta). Skopiuj go, ponieważ będzie on potrzebny w dalszej części tego tematu.
6. W menu Ustawienia, kliknij przycisk **klucze**.
7. W **haseł** sekcji, wprowadź opis klucza i wybierz czas trwania, a następnie kliknij **Zapisz**. Skopiuj wartość klucza (znany także jako klucz tajny klienta) do użycia w dalszej części tego tematu.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Skonfiguruj tworzenie, odczytywanie i aktualizowanie uprawnień aplikacji
Teraz musisz skonfigurować aplikację, aby uzyskać wymagane uprawnienia do tworzenia, odczytu, aktualizacji i usuwania użytkowników.

1. Trwając w menu rejestracje aplikacji w witrynie Azure portal, wybierz swoją aplikację.
2. W menu Ustawienia, kliknij polecenie **wymagane uprawnienia**.
3. W menu wymaganych uprawnień, kliknij pozycję **Windows Azure Active Directory**.
4. Wybierz z menu Włącz dostęp za pomocą **odczytu i zapisu danych katalogu** uprawnienie z **uprawnienia aplikacji** i kliknij przycisk **Zapisz**.
5. Na koniec w menu wymagane uprawnienia kliknij **Udziel uprawnień** przycisku.

Masz teraz aplikację, która ma uprawnienia do tworzenia, odczytu i aktualizacji użytkowników z dzierżawy usługi B2C.

> [!NOTE]
> Udzielanie uprawnień może potrwać kilka minut na pełne przetworzenie.
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Skonfiguruj Usuń lub zaktualizuj hasło uprawnienia dla aplikacji
Obecnie *odczytu i zapisu danych katalogu* uprawnienie jest **nie** obejmuje możliwość usunięcia użytkowników lub aktualizowanie hasła użytkownika. Jeśli chcesz dać aplikacji możliwość usuwania użytkowników lub aktualizacji hasła, musisz wykonać następujące kroki, które obejmują programu PowerShell, w przeciwnym razie możesz przejść do następnej sekcji.

Po pierwsze, jeśli nie masz jeszcze zainstalowany, zainstaluj [modułu programu Azure AD PowerShell w wersji 1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Po zainstalowaniu modułu programu PowerShell Połącz się z dzierżawą usługi Azure AD B2C.

> [!IMPORTANT]
> Należy użyć konta administratora dzierżawy B2C, który jest **lokalnego** do dzierżawy usługi B2C. Te konta wyglądać następująco: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Teraz użyjemy **identyfikator aplikacji** w skrypcie poniżej, aby przypisać tę aplikację z roli użytkownika konta administratora. Te role mają dobrze znane identyfikatory, aby wszystkie potrzebne są dane wejściowe użytkownika **identyfikator aplikacji** w poniższym skrypcie.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Teraz Twoja aplikacja ma również uprawnienia do usuwania użytkowników lub aktualizacji hasła z dzierżawą B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Pobrać, skonfigurować i skompiluj przykładowy kod
Najpierw należy pobrać przykładowy kod i będzie działać. Następnie firma Microsoft podejmie bliżej przyjrzeć.  Możesz [Pobierz przykładowy kod w pliku .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Można również sklonować do wybranego katalogu:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Otwórz `B2CGraphClient\B2CGraphClient.sln` rozwiązania Visual Studio w programie Visual Studio. W `B2CGraphClient` projektu, otwórz plik `App.config`. Zastąp ustawienia trzech aplikacji przy użyciu własnych wartości:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Następnie kliknij prawym przyciskiem myszy `B2CGraphClient` rozwiązania i skompiluj ponownie przykład. Po pomyślnym wykonaniu `B2C.exe` plik wykonywalny znajduje się w `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Tworzenie operacji CRUD użytkownika za pomocą interfejsu API programu Graph
Aby użyć B2CGraphClient, otwórz `cmd` Windows polecenie w wierszu polecenia i zmień katalog na `Debug` katalogu. Następnie uruchom `B2C Help` polecenia.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Spowoduje to wyświetlenie krótki opis każdego polecenia. Każdorazowo, można wywołać jedną z tych poleceń `B2CGraphClient` kieruje żądanie do interfejsu API programu Graph usługi Azure AD.

### <a name="get-an-access-token"></a>Pobranie tokenu dostępu
Każde żądanie do interfejsu API programu Graph wymaga tokenu dostępu do uwierzytelniania. `B2CGraphClient` używa typu open-source Active Directory Authentication Library (ADAL), aby ułatwić uzyskiwanie tokenów dostępu. Biblioteka ADAL ułatwia uzyskanie tokenu przesyłając prosty interfejs API i Dbamy o pewnych ważnych informacji, takich jak buforowanie tokenów dostępu. Nie masz jednak uzyskiwać tokeny, za pomocą biblioteki ADAL. Mogą również uzyskiwać tokeny przy tworzeniu żądania HTTP.

> [!NOTE]
> Ten przykładowy kod używa biblioteki ADAL v2 do komunikowania się z interfejsu API programu Graph.  Aby uzyskać tokenów dostępu, które mogą być używane z interfejsem API programu Graph usługi Azure AD, należy użyć biblioteki ADAL v2 lub v3.
> 
> 

Gdy `B2CGraphClient` jest uruchamiany, tworzy wystąpienie `B2CGraphClient` klasy. Konstruktor dla tej klasy konfiguruje szkieletu uwierzytelniania ADAL:

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

Użyjemy `B2C Get-User` polecenia jako przykład. Gdy `B2C Get-User` jest wywoływane bez żadnych dodatkowych danych wejściowych wywołania interfejsu wiersza polecenia `B2CGraphClient.GetAllUsers(...)` metody. Ta metoda wywołuje `B2CGraphClient.SendGraphGetRequest(...)`, który przesyła żądanie HTTP GET do interfejsu API programu Graph. Przed `B2CGraphClient.SendGraphGetRequest(...)` wysyła żądanie GET, należy go najpierw pobiera token dostępu za pomocą biblioteki ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Możesz uzyskać dostęp tokenu dla interfejsu API programu Graph, wywołując biblioteki ADAL `AuthenticationContext.AcquireToken(...)` metody. Następnie zwraca ADAL `access_token` reprezentujący tożsamości aplikacji.

### <a name="read-users"></a>Odczytać użytkowników
Jeśli chcesz uzyskać listę użytkowników lub pobrać określonego użytkownika z interfejsu API programu Graph, możesz wysłać HTTP `GET` limit czasu żądania `/users` punktu końcowego. Żądanie dla wszystkich użytkowników w dzierżawie wygląda następująco:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Aby wyświetlić to żądanie, uruchom polecenie:

 ```cmd
 B2C Get-User
 ```

Istnieją dwie ważne zagadnienia, które należy zwrócić uwagę:

* Token dostępu nabyte za pośrednictwem biblioteki ADAL jest dodawany do `Authorization` nagłówka przy użyciu `Bearer` schematu.
* W przypadku dzierżaw B2C, należy użyć parametru zapytania `api-version=1.6`.

Obie te informacje są obsługiwane w `B2CGraphClient.SendGraphGetRequest(...)` metody:

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

### <a name="create-consumer-user-accounts"></a>Tworzenie kont użytkowników konsumenta
Podczas tworzenia kont użytkowników w dzierżawie B2C, możesz wysłać HTTP `POST` limit czasu żądania `/users` punktu końcowego:

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

Większość tych właściwości, w tym żądaniu są wymagane do utworzenia konsumentów. Aby dowiedzieć się więcej, kliknij przycisk [tutaj](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Należy pamiętać, że `//` komentarze zostały włączone do celów informacyjnych. Nie należy wprowadzać ich w rzeczywistego żądania.

Aby zobaczyć żądanie, uruchom jedno z następujących poleceń:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` Polecenie przyjmuje jako parametr wejściowy plik JSON. Zawiera JSON reprezentacja obiektu użytkownika. Istnieją dwa przykładowe pliki JSON w przykładowym kodzie: `usertemplate-email.json` i `usertemplate-username.json`. Można zmodyfikować te pliki do własnych potrzeb. Oprócz powyższych pól wymaganych kilka pól opcjonalnych, których można użyć znajdują się w tych plikach. Szczegółowe informacje na temat pól opcjonalnych można znaleźć w [odwołania do jednostki interfejsu API usługi Azure AD Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

Możesz zobaczyć, jak żądania POST jest tworzony w `B2CGraphClient.SendGraphPostRequest(...)`.

* Dołącza token dostępu do `Authorization` nagłówku żądania.
* Ustawia `api-version=1.6`.
* Zawiera ono obiekt użytkownika w formacie JSON w treści żądania.

> [!NOTE]
> Jeśli konta, które chcesz przeprowadzić migrację z istniejącym magazynem użytkownika ma niższy siły hasła niż [siły silne hasło, wymuszane przez usługę Azure AD B2C](/previous-versions/azure/jj943764(v=azure.100)), możesz wyłączyć wymaganie silne hasło przy użyciu `DisableStrongPassword` wartość w `passwordPolicies` właściwości. Na przykład można zmodyfikować żądanie użytkownika Utwórz podanych powyżej w następujący sposób: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Aktualizowanie kont użytkowników konsumenta
Po zaktualizowaniu obiektów użytkowników, proces jest podobny do tego, którego używasz do tworzenia obiektów użytkowników. Ten proces korzysta z protokołu HTTP, ale `PATCH` metody:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Spróbuj zaktualizować użytkownika, aktualizując plików JSON za pomocą nowych danych. Następnie można użyć `B2CGraphClient` do uruchamiania jednego z następujących poleceń:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Sprawdzanie `B2CGraphClient.SendGraphPatchRequest(...)` metody, aby uzyskać szczegółowe informacje na temat sposobu wysyła to żądanie.

### <a name="search-users"></a>Wyszukiwania użytkowników
Możesz wyszukać użytkowników w dzierżawie B2C kilka sposobów. Obiekt ją przy użyciu jego identyfikator lub dwóch, za pomocą identyfikator logowania użytkownika (czyli `signInNames` właściwości).

Uruchom jedno z poniższych poleceń, aby wyszukać konkretnego użytkownika:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Poniżej przedstawiono kilka przykładów:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Usuwanie użytkowników
Proces związanych z usuwaniem użytkowników jest bardzo proste. Korzystanie z protokołu HTTP `DELETE` metody i konstrukcji do adresu URL poprawny identyfikator obiektu:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Aby zobaczyć przykład, wprowadź następujące polecenie, a następnie Wyświetl żądanie delete, które są drukowane w konsoli:

```cmd
B2C Delete-User <object-id-of-user>
```

Sprawdzanie `B2CGraphClient.SendGraphDeleteRequest(...)` metody, aby uzyskać szczegółowe informacje na temat sposobu wysyła to żądanie.

Można wykonywać wiele akcji przy użyciu interfejsu API programu Graph usługi Azure AD, oprócz zarządzania użytkownikami. [Dokumentacja interfejsu API programu Graph usługi Azure AD](/previous-versions/azure/ad/graph/api/api-catalog) zawiera szczegółowe informacje o każdej akcji, wraz z prośby o przykłady.

## <a name="use-custom-attributes"></a>Używanie atrybutów niestandardowych
Większość aplikacji konsumentów muszą przechowywać pewien rodzaj informacji o profilu użytkownika niestandardowego. Jest jednym ze sposobów, w tym do definiowania atrybutu niestandardowego w swojej dzierżawy usługi B2C. Ten atrybut można następnie traktować taki sam sposób traktowania innych właściwości w obiekcie użytkownika. Można zaktualizować atrybutu, usuń atrybut, zapytania za pomocą atrybutu, wysyłanie atrybutu jako oświadczenia w tokeny logowania i nie tylko.

Aby zdefiniować niestandardowy atrybut w dzierżawie B2C, zobacz [odwołanie do atrybutu niestandardowego B2C](active-directory-b2c-reference-custom-attr.md).

Możesz wyświetlić atrybuty niestandardowe zdefiniowane w dzierżawie B2C przy użyciu `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Dane wyjściowe z tych funkcji, co spowoduje wyświetlenie szczegółów każdego z atrybutów niestandardowych, takich jak:

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

Można użyć pełnej nazwy, takie jak `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, jako właściwość w obiektów użytkownika.  Zaktualizuj plik JSON o nowej właściwości i wartości dla właściwości, a następnie uruchom:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Za pomocą `B2CGraphClient`, będziesz mieć aplikację usługi, które można programowo zarządzać użytkownikami dzierżawy B2C. `B2CGraphClient` używa jego tożsamość aplikacji do uwierzytelniania interfejsu API programu Graph usługi Azure AD. Uzyskuje również tokenów, korzystając z klucz tajny klienta. Ponieważ ta funkcja jest uwzględnienie w aplikacji, należy pamiętać o kilka najważniejszych aplikacji B2C:

* Musisz przyznać jej odpowiednie uprawnienia w dzierżawie.
* Na razie należy użyć biblioteki ADAL (nie MSAL) uzyskiwanie tokenów dostępu. (Można również wysyłać wiadomości protokołu bezpośrednio, bez korzystania z biblioteki.)
* Po wywołaniu interfejsu API programu Graph, należy użyć `api-version=1.6`.
* Podczas tworzenia i aktualizowania konsumentów, kilka właściwości są wymagane, zgodnie z powyższym opisem.

Jeśli masz pytania lub żądań dla akcji, czy chcesz wykonać przy użyciu interfejsu API programu Graph w dzierżawie B2C, zostaw komentarz w tym artykule lub plik problemu w repozytorium przykładów kodu usługi GitHub.

