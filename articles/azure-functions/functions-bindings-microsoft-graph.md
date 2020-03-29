---
title: Powiązania programu Microsoft Graph dla funkcji platformy Azure
description: Dowiedz się, jak używać wyzwalaczy i powiązań programu Microsoft Graph w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 770187693e5bac6e059dfd20455099fcc695b74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715037"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Powiązania programu Microsoft Graph dla funkcji platformy Azure

W tym artykule wyjaśniono, jak skonfigurować i pracować z microsoft graph wyzwalaczy i powiązań w usłudze Azure Functions. Dzięki tym usługom Azure Functions można pracować z danymi, spostrzeżeniami i zdarzeniami z [programu Microsoft Graph.](https://developer.microsoft.com/graph)

Rozszerzenie Microsoft Graph zawiera następujące powiązania:
- [Powiązanie wejściowe tokenu akcesowego](#token-input) umożliwia interakcję z dowolnym interfejsem API programu Microsoft Graph.
- [Powiązanie wprowadzania tabeli programu Excel](#excel-input) umożliwia odczytywanie danych z programu Excel.
- [Powiązanie danych wyjściowych tabeli programu Excel](#excel-output) umożliwia modyfikowanie danych programu Excel.
- [Powiązanie wprowadzania pliku usługi OneDrive](#onedrive-input) umożliwia odczytywanie plików z usługi OneDrive.
- [Powiązanie danych wyjściowych pliku usługi OneDrive](#onedrive-output) umożliwia zapisywanie do plików w usłudze OneDrive.
- [Powiązanie danych wyjściowych wiadomości programu Outlook](#outlook-output) umożliwia wysyłanie wiadomości e-mail za pośrednictwem programu Outlook.
- Kolekcja [wyzwalaczy i powiązań elementu webhook programu Microsoft Graph](#webhooks) umożliwia reagowanie na zdarzenia z programu Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Powiązania programu Microsoft Graph są obecnie w wersji zapoznawczej dla usługi Azure Functions w wersji 2.x lub nowszej. Nie są one obsługiwane w funkcji w wersji 1.x.

## <a name="packages"></a>Pakiety

Powiązanie wejściowe tokenu eru znajduje się w pakiecie [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet. Inne powiązania programu Microsoft Graph znajdują się w pakiecie [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) Kod źródłowy pakietów znajduje się w repozytorium GitHub z rozszerzeniem azure-functions-microsoftgraph.Source code for the packages is in the [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub repozytorium.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Konfigurowanie rozszerzeń

Powiązania programu Microsoft Graph są dostępne za pośrednictwem _rozszerzeń powiązań._ Rozszerzenia powiązania są opcjonalnymi składnikami środowiska wykonawczego usługi Azure Functions. W tej sekcji pokazano, jak skonfigurować rozszerzenia microsoft graph i auth token.

### <a name="enabling-functions-20-preview"></a>Włączanie funkcji 2.0 podglądu

Rozszerzenia powiązania są dostępne tylko dla usługi Azure Functions 2.0 w wersji zapoznawczej. 

Aby uzyskać informacje dotyczące ustawiania aplikacji funkcyjnej do korzystania z wersji preview 2.0 środowiska wykonawczego Functions, zobacz [Jak kierować wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie z witryny Azure Portal, przejdź do szablonu lub powiązania, które odwołuje się do niego. Utwórz nową funkcję, a na ekranie wyboru szablonu wybierz scenariusz "Microsoft Graph". Wybierz jeden z szablonów z tego scenariusza. Alternatywnie można przejść do zakładki "Integruj" istniejącej funkcji i wybrać jedno z powiązań omówionych w tym artykule.

W obu przypadkach pojawi się ostrzeżenie, które określa rozszerzenie, które ma zostać zainstalowane. Kliknij **przycisk Zainstaluj,** aby uzyskać rozszerzenie. Każde rozszerzenie musi być zainstalowane tylko raz na aplikację funkcji. 

> [!Note] 
> Proces instalacji w portalu może potrwać do 10 minut w planie zużycia.

Jeśli używasz programu Visual Studio, można uzyskać rozszerzenia, instalując [pakiety NuGet, które są wymienione wcześniej w tym artykule.](#packages)

### <a name="configuring-authentication--authorization"></a>Konfigurowanie uwierzytelniania / autoryzacji

Powiązania opisane w tym artykule wymagają tożsamości do użycia. Dzięki temu program Microsoft Graph może wymuszać uprawnienia i interakcje inspekcji. Tożsamość może być użytkownik uzyskujący dostęp do aplikacji lub samej aplikacji. Aby skonfigurować tę tożsamość, należy skonfigurować [uwierzytelnianie/ autoryzację usługi app service](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) w usłudze Azure Active Directory. Należy również zażądać wszelkich uprawnień zasobów, których wymagają funkcje.

> [!Note] 
> Rozszerzenie Microsoft Graph obsługuje tylko uwierzytelnianie usługi Azure AD. Użytkownicy muszą zalogować się za pomocą konta służbowego.

Jeśli używasz witryny Azure portal, zobaczysz ostrzeżenie poniżej monitu o zainstalowanie rozszerzenia. Ostrzeżenie monituje o skonfigurowanie uwierzytelniania/autoryzacji usługi app service i żądanie wszelkich uprawnień wymaganych przez szablon lub powiązanie. Kliknij **pozycję Konfiguruj usługę Azure AD teraz** lub Dodaj uprawnienia **teraz,** w razie potrzeby.



<a name="token-input"></a>
## <a name="auth-token"></a>Token emocie

Powiązanie wejściowe tokenu eru pobiera token usługi Azure AD dla danego zasobu i udostępnia go do kodu jako ciąg. Zasób może być dowolny, dla którego aplikacja ma uprawnienia. 

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#auth-token---example)
* [Atrybuty](#auth-token---attributes)
* [Konfiguracja](#auth-token---configuration)
* [Użycia](#auth-token---usage)

### <a name="auth-token---example"></a>Urojnia - przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#auth-token---c-script-example)
* [Javascript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token urywek — przykład skryptu Języka C#

Poniższy przykład pobiera informacje o profilu użytkownika.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem danych wejściowych tokenu:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu języka C# używa tokenu do wywołania HTTP do programu Microsoft Graph i zwraca wynik:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Token Auth — przykład języka JavaScript

Poniższy przykład pobiera informacje o profilu użytkownika.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem danych wejściowych tokenu:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript używa tokenu do wywołania HTTP do programu Microsoft Graph i zwraca wynik.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Token urywek - atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [Token.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs)

### <a name="auth-token---configuration"></a>Token eru - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Token` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla tokenu eru. Zobacz [Korzystanie z powiązania wprowadzania tokenu eru z kodu](#token-input-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `token`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `in`ustawiona na .|
|**Tożsamości**|**Tożsamość**|Wymagane — tożsamość, która będzie używana do wykonywania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>- Tylko ważne z [wyzwalaczem HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>- Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwość.</li><li><code>userFromToken</code>- Używa tożsamości reprezentowane przez określony token. Zobacz <code>userToken</code> właściwość.</li><li><code>clientCredentials</code>- Używa tożsamości aplikacji funkcji.</li></ul>|
|**Userid**|**Userid**  |Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId`. Identyfikator główny użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**Usertoken**|**Usertoken**|Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken`. Token ważny dla aplikacji funkcji. |
|**Zasobów**|**Zasobów**|Wymagane — adres URL zasobu usługi Azure AD, dla którego token jest wymagany.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token emocie - użycie

Samo powiązanie nie wymaga żadnych uprawnień usługi Azure AD, ale w zależności od sposobu użycia tokenu może być konieczne żądanie dodatkowych uprawnień. Sprawdź wymagania zasobu, do którego zamierzasz uzyskać dostęp za pomocą tokenu.

Token jest zawsze przedstawiany do kodu jako ciąg.

> [!Note]
> Podczas tworzenia lokalnie za `userFromId` `userFromToken` pomocą `userFromRequest` jednego z , lub opcji, wymagany `X-MS-TOKEN-AAD-ID-TOKEN` token można uzyskać [ręcznie](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) i określone w nagłówku żądania z wywołującej aplikacji klienckiej.


<a name="excel-input"></a>
## <a name="excel-input"></a>Dane wejściowe programu Excel

Powiązanie wprowadzania tabeli programu Excel odczytuje zawartość tabeli programu Excel przechowywanej w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-input---example)
* [Atrybuty](#excel-input---attributes)
* [Konfiguracja](#excel-input---configuration)
* [Użycia](#excel-input---usage)

### <a name="excel-input---example"></a>Dane wejściowe programu Excel — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#excel-input---c-script-example)
* [Javascript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Dane wejściowe programu Excel — przykład skryptu C#

Następujący plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wejściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Następujący kod skryptu Języka C# odczytuje zawartość określonej tabeli i zwraca je użytkownikowi:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Dane wejściowe programu Excel — przykład języka JavaScript

Następujący plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wejściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniższy kod JavaScript odczytuje zawartość określonej tabeli i zwraca ją użytkownikowi.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Dane wejściowe programu Excel — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [programu Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-input---configuration"></a>Dane wejściowe programu Excel — konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Excel` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla tabeli programu Excel. Zobacz [Używanie powiązania wprowadzania tabeli programu Excel z kodu](#excel-input-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `excel`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `in`ustawiona na .|
|**Tożsamości**|**Tożsamość**|Wymagane — tożsamość, która będzie używana do wykonywania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>- Tylko ważne z [wyzwalaczem HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>- Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwość.</li><li><code>userFromToken</code>- Używa tożsamości reprezentowane przez określony token. Zobacz <code>userToken</code> właściwość.</li><li><code>clientCredentials</code>- Używa tożsamości aplikacji funkcji.</li></ul>|
|**Userid**|**Userid**  |Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId`. Identyfikator główny użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**Usertoken**|**Usertoken**|Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken`. Token ważny dla aplikacji funkcji. |
|**Ścieżka**|**Ścieżka**|Wymagane — ścieżka w usłudze OneDrive do skoroszytu programu Excel.|
|**nazwa arkusza**|**Nazwa arkusza**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**Tablename**|Nazwa tabeli. Jeśli nie zostanie określona, zostanie użyta zawartość arkusza.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Dane wejściowe programu Excel — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytywanie plików użytkownika|

Powiązanie udostępnia następujące typy do funkcji .NET:
- string[][]
- Tabela microsoft.Graph.WorkbookTable
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)










<a name="excel-output"></a>
## <a name="excel-output"></a>Dane wyjściowe programu Excel

Powiązanie danych wyjściowych programu Excel modyfikuje zawartość tabeli programu Excel przechowywanej w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-output---example)
* [Atrybuty](#excel-output---attributes)
* [Konfiguracja](#excel-output---configuration)
* [Użycia](#excel-output---usage)

### <a name="excel-output---example"></a>Dane wyjściowe programu Excel — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#excel-output---c-script-example)
* [Javascript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Dane wyjściowe programu Excel — przykład skryptu C#

Poniższy przykład dodaje wiersze do tabeli programu Excel.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wyjściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu Języka C# dodaje nowy wiersz do tabeli (zakłada się, że jest jednokolumnowa) na podstawie danych wejściowych z ciągu zapytania:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Dane wyjściowe programu Excel — przykład języka JavaScript

Poniższy przykład dodaje wiersze do tabeli programu Excel.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wyjściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniższy kod JavaScript dodaje nowy wiersz do tabeli (zakłada się, że jest jednokolumnowa) na podstawie danych wejściowych z ciągu zapytania.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Dane wyjściowe programu Excel — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [programu Excel.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs)

### <a name="excel-output---configuration"></a>Dane wyjściowe programu Excel — konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Excel` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla tokenu eru. Zobacz [Używanie powiązania danych wyjściowych tabeli programu Excel z kodu](#excel-output-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `excel`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `out`ustawiona na .|
|**Tożsamości**|**Tożsamość**|Wymagane — tożsamość, która będzie używana do wykonywania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>- Tylko ważne z [wyzwalaczem HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>- Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwość.</li><li><code>userFromToken</code>- Używa tożsamości reprezentowane przez określony token. Zobacz <code>userToken</code> właściwość.</li><li><code>clientCredentials</code>- Używa tożsamości aplikacji funkcji.</li></ul>|
|**Userid** |**Userid** |Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId`. Identyfikator główny użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**Usertoken**|**Usertoken**|Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken`. Token ważny dla aplikacji funkcji. |
|**Ścieżka**|**Ścieżka**|Wymagane — ścieżka w usłudze OneDrive do skoroszytu programu Excel.|
|**nazwa arkusza**|**Nazwa arkusza**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**Tablename**|Nazwa tabeli. Jeśli nie zostanie określona, zostanie użyta zawartość arkusza.|
|**typ aktualizacji**|**Typ aktualizacji**|Wymagane — typ zmiany do wykonania w tabeli. Może być jedną z następujących wartości:<ul><li><code>update</code>- Zastępuje zawartość tabeli w usłudze OneDrive.</li><li><code>append</code>- Dodaje ładunek na końcu tabeli w usłudze OneDrive, tworząc nowe wiersze.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Dane wyjściowe programu Excel — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Mieć pełny dostęp do plików użytkownika|

Powiązanie udostępnia następujące typy do funkcji .NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Tabela microsoft.Graph.WorkbookTable
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Wprowadzanie pliku

Powiązanie wejściowe Plik usługi OneDrive odczytuje zawartość pliku przechowywanego w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-input---example)
* [Atrybuty](#file-input---attributes)
* [Konfiguracja](#file-input---configuration)
* [Użycia](#file-input---usage)

### <a name="file-input---example"></a>Wprowadzanie pliku — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#file-input---c-script-example)
* [Javascript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Wprowadzanie pliku — przykład skryptu C#

W poniższym przykładzie odczytuje plik, który jest przechowywany w usłudze OneDrive.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wejściowym pliku usługi OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu języka C# odczytuje plik określony w ciągu zapytania i rejestruje jego długość:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Wprowadzanie pliku — przykład języka JavaScript

W poniższym przykładzie odczytuje plik, który jest przechowywany w usłudze OneDrive.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wejściowym pliku usługi OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniższy kod JavaScript odczytuje plik określony w ciągu zapytania i zwraca jego długość.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Wprowadzanie pliku - atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-input---configuration"></a>Wprowadzanie pliku - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `OneDrive` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla pliku. Zobacz [Używanie powiązania wejściowego pliku usługi OneDrive z kodu](#onedrive-input-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `onedrive`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `in`ustawiona na .|
|**Tożsamości**|**Tożsamość**|Wymagane — tożsamość, która będzie używana do wykonywania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>- Tylko ważne z [wyzwalaczem HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>- Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwość.</li><li><code>userFromToken</code>- Używa tożsamości reprezentowane przez określony token. Zobacz <code>userToken</code> właściwość.</li><li><code>clientCredentials</code>- Używa tożsamości aplikacji funkcji.</li></ul>|
|**Userid**|**Userid**  |Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId`. Identyfikator główny użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**Usertoken**|**Usertoken**|Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken`. Token ważny dla aplikacji funkcji. |
|**Ścieżka**|**Ścieżka**|Wymagane — ścieżka w usłudze OneDrive do pliku.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Wprowadzanie pliku - użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytywanie plików użytkownika|

Powiązanie udostępnia następujące typy do funkcji .NET:
- bajt[]
- Strumień
- ciąg
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Dane wyjściowe pliku

Powiązanie danych wyjściowych pliku usługi OneDrive modyfikuje zawartość pliku przechowywanego w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-output---example)
* [Atrybuty](#file-output---attributes)
* [Konfiguracja](#file-output---configuration)
* [Użycia](#file-output---usage)

### <a name="file-output---example"></a>Dane wyjściowe pliku — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#file-output---c-script-example)
* [Javascript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Dane wyjściowe pliku — przykład skryptu C#

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wyjściowym usługi OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu Języka C# pobiera tekst z ciągu zapytania i zapisuje go do pliku tekstowego (FunctionsTest.txt zgodnie z definicją w poprzednim przykładzie) w katalogu głównym usługi OneDrive wywołującego:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Dane wyjściowe pliku — przykład języka JavaScript

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wyjściowym usługi OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript pobiera tekst z ciągu zapytania i zapisuje go do pliku tekstowego (FunctionsTest.txt zgodnie z definicją w konfiguracji powyżej) w katalogu głównym usługi OneDrive wywołującego.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Dane wyjściowe pliku - atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [OneDrive.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs)

### <a name="file-output---configuration"></a>Wyjście pliku - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `OneDrive` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla pliku. Zobacz [Używanie powiązania danych wyjściowych usługi OneDrive z kodu](#onedrive-output-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `onedrive`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `out`ustawiona na .|
|**Tożsamości**|**Tożsamość**|Wymagane — tożsamość, która będzie używana do wykonywania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>- Tylko ważne z [wyzwalaczem HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>- Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwość.</li><li><code>userFromToken</code>- Używa tożsamości reprezentowane przez określony token. Zobacz <code>userToken</code> właściwość.</li><li><code>clientCredentials</code>- Używa tożsamości aplikacji funkcji.</li></ul>|
|**Userid** |**Userid** |Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId`. Identyfikator główny użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**Usertoken**|**Usertoken**|Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken`. Token ważny dla aplikacji funkcji. |
|**Ścieżka**|**Ścieżka**|Wymagane — ścieżka w usłudze OneDrive do pliku.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Dane wyjściowe pliku — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Mieć pełny dostęp do plików użytkownika|

Powiązanie udostępnia następujące typy do funkcji .NET:
- bajt[]
- Strumień
- ciąg
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Dane wyjściowe programu Outlook

Powiązanie danych wyjściowych wiadomości programu Outlook wysyła wiadomość e-mail za pośrednictwem programu Outlook.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#outlook-output---example)
* [Atrybuty](#outlook-output---attributes)
* [Konfiguracja](#outlook-output---configuration)
* [Użycia](#outlook-output---usage)

### <a name="outlook-output---example"></a>Dane wyjściowe programu Outlook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#outlook-output---c-script-example)
* [Javascript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Dane wyjściowe programu Outlook — przykład skryptu C#

Poniższy przykład wysyła wiadomość e-mail za pośrednictwem programu Outlook.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wyjściowym wiadomości programu Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Kod skryptu języka C# wysyła wiadomość e-mail od wywołującego do adresata określonego w ciągu zapytania:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Dane wyjściowe programu Outlook — przykład języka JavaScript

Poniższy przykład wysyła wiadomość e-mail za pośrednictwem programu Outlook.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wyjściowym wiadomości programu Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Kod JavaScript wysyła wiadomość e-mail od osoby dzwoniącej do adresata określonego w ciągu zapytania:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Dane wyjściowe programu Outlook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [programu Outlook.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs)

### <a name="outlook-output---configuration"></a>Dane wyjściowe programu Outlook — konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Outlook` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Korzystanie z powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `outlook`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `out`ustawiona na .|
|**Tożsamości**|**Tożsamość**|Wymagane — tożsamość, która będzie używana do wykonywania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>- Tylko ważne z [wyzwalaczem HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>- Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwość.</li><li><code>userFromToken</code>- Używa tożsamości reprezentowane przez określony token. Zobacz <code>userToken</code> właściwość.</li><li><code>clientCredentials</code>- Używa tożsamości aplikacji funkcji.</li></ul>|
|**Userid**|**Userid**  |Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId`. Identyfikator główny użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**Usertoken**|**Usertoken**|Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken`. Token ważny dla aplikacji funkcji. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Dane wyjściowe programu Outlook — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Wysyłanie poczty jako użytkownik|

Powiązanie udostępnia następujące typy do funkcji .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- ciąg
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)






## <a name="webhooks"></a>Elementy webhook

Element webhooks umożliwiają reagowanie na zdarzenia w programie Microsoft Graph. Do obsługi elementów webhook potrzebne są funkcje do tworzenia, odświeżania i reagowania na _subskrypcje elementu webhook._ Kompletne rozwiązanie elementu webhook wymaga kombinacji następujących powiązań:
- [Wyzwalacz elementu webhook programu Microsoft Graph](#webhook-trigger) umożliwia reagowanie na przychodzący element webhook.
- [Powiązanie wejściowe subskrypcji elementu webhook programu Microsoft Graph](#webhook-input) umożliwia wyświetlanie listy istniejących subskrypcji i opcjonalnie ich odświeżanie.
- [Powiązanie danych wyjściowych subskrypcji elementu webhook programu Microsoft Graph](#webhook-output) umożliwia tworzenie lub usuwanie subskrypcji elementu webhook.

Same powiązania nie wymagają żadnych uprawnień usługi Azure AD, ale należy zażądać uprawnień odpowiednich do typu zasobu, na który chcesz zareagować. Aby uzyskać listę uprawnień, które są potrzebne dla każdego typu zasobu, zobacz [uprawnienia subskrypcji](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Praca z elementami webhook w programie Microsoft Graph].





## <a name="webhook-trigger"></a>Wyzwalacz elementu webhook

Wyzwalacz elementu webhook programu Microsoft Graph umożliwia funkcji reagowanie na przychodzący element webhook z programu Microsoft Graph. Każde wystąpienie tego wyzwalacza może reagować na jeden typ zasobu programu Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-trigger---example)
* [Atrybuty](#webhook-trigger---attributes)
* [Konfiguracja](#webhook-trigger---configuration)
* [Użycia](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Wyzwalacz elementu webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#webhook-trigger---c-script-example)
* [Javascript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Wyzwalacz elementu webhook — przykład skryptu języka C#

W poniższym przykładzie obsługuje elementy webhook dla przychodzących wiadomości programu Outlook. Aby użyć wyzwalacza elementu webhook, [należy utworzyć subskrypcję](#webhook-output---example)i [odświeżyć subskrypcję,](#webhook-subscription-refresh) aby zapobiec jej wygaśnięciu.

Plik *function.json* definiuje wyzwalacz elementu webhook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Kod skryptu Języka C# reaguje na przychodzące wiadomości e-mail i rejestruje treść tych wysłanych przez adresata i zawierające "Usługi Azure Functions" w temacie:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Wyzwalacz elementu webhook — przykład języka JavaScript

W poniższym przykładzie obsługuje elementy webhook dla przychodzących wiadomości programu Outlook. Aby użyć wyzwalacza elementu webhook, [należy utworzyć subskrypcję](#webhook-output---example)i [odświeżyć subskrypcję,](#webhook-subscription-refresh) aby zapobiec jej wygaśnięciu.

Plik *function.json* definiuje wyzwalacz elementu webhook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Kod JavaScript reaguje na przychodzące wiadomości e-mail i rejestruje treść tych wysłanych przez odbiorcę i zawierających "Usługi Azure Functions" w temacie:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Wyzwalacz elementu webhook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [GraphWebhookTrigger.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs)

### <a name="webhook-trigger---configuration"></a>Wyzwalacz elementu webhook — konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `GraphWebhookTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Korzystanie z powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `graphWebhook`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `trigger`ustawiona na .|
|**resourceType**|**Resourcetype**|Wymagane — zasób wykresu, dla którego ta funkcja powinna odpowiadać na elementów webhook. Może być jedną z następujących wartości:<ul><li><code>#Microsoft.Graph.Message</code>- zmiany wprowadzone w wiadomościach programu Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>- zmiany wprowadzone w elementach głównych usługi OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>- zmiany wprowadzone do kontaktów osobistych w Outlooku.</li><li><code>#Microsoft.Graph.Event</code>- zmiany wprowadzone w elementach kalendarza programu Outlook.</li></ul>|

> [!Note]
> Aplikacja funkcji może mieć tylko jedną funkcję, `resourceType` która jest zarejestrowana względem danej wartości.

### <a name="webhook-trigger---usage"></a>Wyzwalacz elementu webhook — użycie

Powiązanie udostępnia następujące typy do funkcji .NET:
- Microsoft Graph SDK typy istotne dla `Microsoft.Graph.Message` `Microsoft.Graph.DriveItem`typu zasobu, takich jak lub .
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Wprowadzanie elementu Webhook

Powiązanie wejściowe elementu webhook programu Microsoft Graph umożliwia pobranie listy subskrypcji zarządzanych przez tę aplikację funkcji. Powiązanie odczytuje z magazynu aplikacji funkcji, więc nie odzwierciedla innych subskrypcji utworzonych spoza aplikacji.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-input---example)
* [Atrybuty](#webhook-input---attributes)
* [Konfiguracja](#webhook-input---configuration)
* [Użycia](#webhook-input---usage)

### <a name="webhook-input---example"></a>Wprowadzanie webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#webhook-input---c-script-example)
* [Javascript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Wprowadzanie elementu webhook — przykład skryptu języka C#

Poniższy przykład pobiera wszystkie subskrypcje dla użytkownika wywołującego i usuwa je.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wejściowym subskrypcji i powiązaniem danych wyjściowych subskrypcji, które używa akcji usuwania:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu języka C# pobiera subskrypcje i usuwa je:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Wprowadzanie webhook — przykład języka JavaScript

Poniższy przykład pobiera wszystkie subskrypcje dla użytkownika wywołującego i usuwa je.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem wejściowym subskrypcji i powiązaniem danych wyjściowych subskrypcji, które używa akcji usuwania:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript pobiera subskrypcje i usuwa je:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Wprowadzanie webhook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-input---configuration"></a>Wejście webhook — konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `GraphWebhookSubscription` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Korzystanie z powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `graphWebhookSubscription`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `in`ustawiona na .|
|**Filtr**|**Filtr**| Jeśli ustawiona na `userFromRequest`, to powiązanie będzie pobierać tylko subskrypcje należące do użytkownika wywołującego (ważne tylko za pomocą [wyzwalacza HTTP]).| 

### <a name="webhook-input---usage"></a>Wprowadzanie webhook — użycie

Powiązanie udostępnia następujące typy do funkcji .NET:
- ciąg[]
- Niestandardowe tablice typów obiektów
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Dane wyjściowe elementu webhook

Powiązanie danych wyjściowych subskrypcji elementu webhook umożliwia tworzenie, usuwanie i odświeżanie subskrypcji elementu webhook w programie Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-output---example)
* [Atrybuty](#webhook-output---attributes)
* [Konfiguracja](#webhook-output---configuration)
* [Użycia](#webhook-output---usage)

### <a name="webhook-output---example"></a>Dane wyjściowe elementu webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#webhook-output---c-script-example)
* [Javascript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Dane wyjściowe elementu webhook — przykład skryptu języka C#

Poniższy przykład tworzy subskrypcję. Można [odświeżyć subskrypcję,](#webhook-subscription-refresh) aby zapobiec jej wygaśnięciu.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem danych wyjściowych subskrypcji przy użyciu akcji create:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu języka C# rejestruje element webhook, który powiadomi tę aplikację funkcji, gdy użytkownik wywołujący odbiera komunikat programu Outlook:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Dane wyjściowe elementu webhook — przykład języka JavaScript

Poniższy przykład tworzy subskrypcję. Można [odświeżyć subskrypcję,](#webhook-subscription-refresh) aby zapobiec jej wygaśnięciu.

Plik *function.json* definiuje wyzwalacz HTTP z powiązaniem danych wyjściowych subskrypcji przy użyciu akcji create:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript rejestruje element webhook, który powiadomi tę aplikację funkcji, gdy użytkownik wywołujący odbiera komunikat programu Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Dane wyjściowe elementu webhook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [GraphWebhookSubscription.](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs)

### <a name="webhook-output---configuration"></a>Wyjście elementu webhook — konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `GraphWebhookSubscription` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**| Nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Korzystanie z powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Typu**| Nie dotyczy |Wymagane - musi być `graphWebhookSubscription`ustawiona na .|
|**Kierunku**| Nie dotyczy |Wymagane - musi być `out`ustawiona na .|
|**Tożsamości**|**Tożsamość**|Wymagane — tożsamość, która będzie używana do wykonywania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>- Tylko ważne z [wyzwalaczem HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>- Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwość.</li><li><code>userFromToken</code>- Używa tożsamości reprezentowane przez określony token. Zobacz <code>userToken</code> właściwość.</li><li><code>clientCredentials</code>- Używa tożsamości aplikacji funkcji.</li></ul>|
|**Userid**|**Userid**  |Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId`. Identyfikator główny użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**Usertoken**|**Usertoken**|Potrzebne wtedy i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken`. Token ważny dla aplikacji funkcji. |
|**Działania**|**Akcja**|Wymagane — określa akcję, która powinna wykonać powiązanie. Może być jedną z następujących wartości:<ul><li><code>create</code>- Rejestruje nową subskrypcję.</li><li><code>delete</code>- Usuwa określoną subskrypcję.</li><li><code>refresh</code>- Odświeża określoną subskrypcję, aby nie wygasała.</li></ul>|
|**subskrypcjaŹródło**|**SubskrypcjaŹródło**|Potrzebne wtedy i _action_ tylko wtedy, `create`gdy akcja jest ustawiona na . Określa zasób programu Microsoft Graph, który będzie monitorowany pod kątem zmian. Zobacz [Praca z elementami webhook w programie Microsoft Graph]. |
|**Changetype**|**Changetype**|Potrzebne wtedy i _action_ tylko wtedy, `create`gdy akcja jest ustawiona na . Wskazuje typ zmiany w subskrybowanym zasobie, który spowoduje zgłoszenie powiadomienia. Obsługiwane wartości to: `created` `updated`, `deleted`, . Wiele wartości można łączyć za pomocą listy oddzielonej przecinkami.|

### <a name="webhook-output---usage"></a>Dane wyjściowe elementu webhook — użycie

Powiązanie udostępnia następujące typy do funkcji .NET:
- ciąg
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Odświeżanie subskrypcji elementu Webhook

Istnieją dwa podejścia do odświeżania subskrypcji:

- Użyj tożsamości aplikacji, aby poradzić sobie ze wszystkimi subskrypcjami. Będzie to wymagało zgody administratora usługi Azure Active Directory. Może to być używane przez wszystkie języki obsługiwane przez usługi Azure Functions.
- Użyj tożsamości skojarzonej z każdą subskrypcją, ręcznie wiążąc każdy identyfikator użytkownika. Będzie to wymagało pewnego kodu niestandardowego do wykonania powiązania. Może to być używane tylko przez funkcje .NET.

Ta sekcja zawiera przykład dla każdego z tych podejść:

* [Przykład tożsamości aplikacji](#webhook-subscription-refresh---app-identity-example)
* [Przykład tożsamości użytkownika](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Odświeżanie subskrypcji elementu Webhook — przykład tożsamości aplikacji

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykład skryptu języka C#

W poniższym przykładzie użyto tożsamości aplikacji, aby odświeżyć subskrypcję.

*Function.json* definiuje wyzwalacz czasomierza z powiązaniem wejściowym subskrypcji i powiązaniem wyjścia subskrypcji:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Kod skryptu języka C# odświeża subskrypcje:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykład skryptu języka C#

W poniższym przykładzie użyto tożsamości aplikacji, aby odświeżyć subskrypcję.

*Function.json* definiuje wyzwalacz czasomierza z powiązaniem wejściowym subskrypcji i powiązaniem wyjścia subskrypcji:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Kod JavaScript odświeża subskrypcje:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Odświeżanie subskrypcji elementu Webhook — przykład tożsamości użytkownika

W poniższym przykładzie użyto tożsamości użytkownika, aby odświeżyć subskrypcję.

Plik *function.json* definiuje wyzwalacz czasomierza i odracza powiązanie wejściowe subskrypcji z kodem funkcji:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Kod skryptu Języka C# odświeża subskrypcje i tworzy powiązanie danych wyjściowych w kodzie, przy użyciu tożsamości każdego użytkownika:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)

[Wyzwalacz HTTP]: functions-bindings-http-webhook.md
[Praca z elementami webhook w programie Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
