---
title: Powiązania programu Microsoft Graph dla usługi Azure Functions
description: Dowiedz się, jak używać programu Microsoft Graph wyzwalaczy i powiązań w usłudze Azure Functions.
services: functions
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: f112bdf9eacf51852659ab49a5673b0c8bfb0e46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511817"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Powiązania programu Microsoft Graph dla usługi Azure Functions

W tym artykule opisano sposób konfigurowania i Praca z wyzwalaczami programu Microsoft Graph i powiązań w usłudze Azure Functions. Za ich pomocą można użyć usługi Azure Functions do pracy z danymi, szczegółowych informacji i zdarzeń z [programu Microsoft Graph](https://developer.microsoft.com/graph).

Rozszerzenie programu Microsoft Graph udostępnia następujące powiązania:
- [Powiązania danych wejściowych tokenu uwierzytelniania](#token-input) pozwala na interakcję z dowolnym interfejsem API programu Graph firmy Microsoft.
- [Powiązania danych wejściowych w tabeli programu Excel](#excel-input) umożliwia odczytywanie danych z programu Excel.
- [Powiązania danych wyjściowych tabeli programu Excel](#excel-output) umożliwia modyfikowanie danych programu Excel.
- A [powiązania danych wejściowych plików usługi OneDrive](#onedrive-input) umożliwia odczytywanie plików z poziomu usługi OneDrive.
- A [powiązania danych wyjściowych plików usługi OneDrive](#onedrive-output) pozwala na zapis w plikach w usłudze OneDrive.
- [Powiązania danych wyjściowych wiadomości programu Outlook](#outlook-output) zezwoli Ci na wysyłanie wiadomości e-mail za pomocą programu Outlook.
- Kolekcja [wyzwalaczy elementów webhook programu Microsoft Graph i powiązań](#webhooks) pozwala reagować na zdarzenia z programu Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Program Microsoft Graph powiązania są obecnie dostępne w wersji zapoznawczej dla usługi Azure Functions w wersji 2.x. Nie są obsługiwane w funkcji wersji 1.x.

## <a name="packages"></a>Pakiety

Powiązania danych wejściowych token uwierzytelniania jest udostępniany w [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) pakietu NuGet. Innych powiązań programu Microsoft Graph znajdują się w [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) pakietu. Kod źródłowy dla pakietów znajduje się w [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Konfigurowanie rozszerzeń

Program Microsoft Graph powiązania są dostępne za pośrednictwem _powiązań rozszerzenia_. Powiązanie rozszerzenia są opcjonalne składniki środowiska uruchomieniowego usługi Azure Functions. W tej sekcji przedstawiono sposób konfigurowania programu Microsoft Graph i rozszerzenia tokenu uwierzytelniania.

### <a name="enabling-functions-20-preview"></a>Włączanie funkcji w wersji 2.0 (wersja zapoznawcza)

Powiązanie rozszerzenia są dostępne tylko w przypadku usługi Azure Functions w wersji 2.0 (wersja zapoznawcza). 

Aby dowiedzieć się, jak skonfigurować aplikację funkcji do użycia środowisko uruchomieniowe usługi Functions w wersji 2.0 w wersji zapoznawczej, zobacz [sposobu kierowania wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie z witryny Azure portal, przejdź do szablonu lub powiązania, który odwołuje się do niej. Utwórz nową funkcję i znajduje się w ekran wyboru szablonu, wybierz scenariusz "Programu Microsoft Graph". Z tego scenariusza, wybierz jeden z szablonów. Alternatywnie można przejść na kartę "Integracja" istniejących funkcji i wybierz jedno z powiązań omówione w tym artykule.

W obu przypadkach zostanie wyświetlone ostrzeżenie, która określa rozszerzenie do zainstalowania. Kliknij przycisk **zainstalować** uzyskać rozszerzenia. Każde rozszerzenie musi tylko można zainstalować raz na aplikację funkcji. 

> [!Note] 
> Proces instalacji w portalu może potrwać do 10 minut w ramach planu zużycie.

Jeśli używasz programu Visual Studio, można uzyskać rozszerzenia, instalując [pakiety NuGet, które są wymienione we wcześniejszej części tego artykułu](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurowanie uwierzytelniania / autoryzacji

Powiązania opisane w tym artykule wymagać potwierdzenia tożsamości do użycia. Dzięki temu program Microsoft Graph można wymusić uprawnień i przeprowadzanie inspekcji interakcje. Tożsamość może być dostęp do aplikacji lub aplikacji użytkownika. Aby skonfigurować tę tożsamość, skonfiguruj [uwierzytelnianie usługi App Service / autoryzacji](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) za pomocą usługi Azure Active Directory. Należy również żądania żadnych uprawnień zasobów, które wymagają funkcji.

> [!Note] 
> Rozszerzenie programu Microsoft Graph obsługuje tylko uwierzytelnianie usługi Azure AD. Użytkownicy muszą zalogować się przy użyciu konta służbowego lub szkolnego.

Jeśli używasz witryny Azure portal, zostanie wyświetlone ostrzeżenie poniżej wierszu polecenia można zainstalować rozszerzenia. Ostrzeżenie monituje o Skonfiguruj uwierzytelnianie usługi App Service / autoryzacji i żądania wymaga wszelkich uprawnień, szablonu lub powiązania. Kliknij przycisk **teraz Konfigurowanie usługi Azure AD** lub **Dodaj uprawnienia teraz** odpowiednio.



<a name="token-input"></a>
## <a name="auth-token"></a>Token uwierzytelniania

Token powiązania danych wejściowych uwierzytelniania pobiera token do usługi Azure AD dla danego zasobu i przekazuje go do kodu jako ciąg. Zasób może to być dowolna, dla którego aplikacja ma uprawnienia. 

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#auth-token---example)
* [Atrybuty](#auth-token---attributes)
* [Konfiguracja](#auth-token---configuration)
* [Użycie](#auth-token---usage)

### <a name="auth-token---example"></a>Token uwierzytelniania — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token uwierzytelniania — przykładowy skrypt w języku C#

Poniższy przykład pobiera informacje o profilu użytkownika.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu tokenu powiązania danych wejściowych:

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

Kod skryptu języka C# używa tokenu do wywołanie HTTP w programie Microsoft Graph i zwraca wynik:

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

#### <a name="auth-token---javascript-example"></a>Token uwierzytelniania — przykład JavaScript

Poniższy przykład pobiera informacje o profilu użytkownika.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu tokenu powiązania danych wejściowych:

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

Kod JavaScript używa tokenu do wywołanie HTTP w programie Microsoft Graph i zwraca wynik.

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

### <a name="auth-token---attributes"></a>Token uwierzytelniania — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [tokenu](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) atrybutu.

### <a name="auth-token---configuration"></a>Token uwierzytelniania — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Token` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji tokenu uwierzytelniania. Zobacz [przy użyciu tokenu uwierzytelniania powiązania w kodzie danych wejściowych](#token-input-code).|
|**type**||Wymagana — musi być równa `token`.|
|**direction**||Wymagana — musi być równa `in`.|
|**Tożsamość**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może być jednym z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe, za pomocą [Wyzwalacz HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> — Wykorzystanie tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Korzysta z tożsamości, reprezentowane przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Korzysta z tożsamości aplikacji funkcji.</li></ul>|
|**userId**|**UserId**  |Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromId`. Identyfikator jednostki użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromToken`. Token jest prawidłowy dla aplikacji funkcji. |
|**Zasób**|**resource**|Wymagana — adres URL zasobu usługi Azure AD, dla którego żądany jest token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token uwierzytelniania — użycie

Powiązanie sam nie wymaga żadnych uprawnień usługi Azure AD, ale w zależności od tego, jak token jest używany, konieczne może być zażądania dodatkowych uprawnień. Sprawdź wymagania dotyczące zasobów, które chcesz uzyskać dostęp przy użyciu tokenu.

Token jest zawsze prezentowane kodu jako ciąg.

> [!Note]
> Podczas tworzenia lokalnie przy użyciu jednej z `userFromId`, `userFromToken` lub `userFromRequest` , wymagany token może być [ręcznie](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) i określonych w `X-MS-TOKEN-AAD-ID-TOKEN` nagłówek żądania od wywołującego aplikacji klienckiej.


<a name="excel-input"></a>
## <a name="excel-input"></a>Dane wejściowe programu Excel

Powiązania danych wejściowych w tabeli programu Excel odczytuje zawartość tabeli programu Excel przechowywane w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-input---example)
* [Atrybuty](#excel-input---attributes)
* [Konfiguracja](#excel-input---configuration)
* [Użycie](#excel-input---usage)

### <a name="excel-input---example"></a>Excel dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Dane wejściowe — Excel przykładowy skrypt w języku C#

Następujące *function.json* plik definiuje wyzwalacza HTTP przy użyciu powiązania danych wejściowych usługi programu Excel:

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

Następujący kod skryptu języka C# odczytuje zawartość z określonej tabeli i zwraca je do użytkownika:

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

#### <a name="excel-input---javascript-example"></a>Program Excel wprowadź — przykład JavaScript

Następujące *function.json* plik definiuje wyzwalacza HTTP przy użyciu powiązania danych wejściowych usługi programu Excel:

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

Następujący kod JavaScript odczytuje zawartość z określonej tabeli i zwraca je do użytkownika.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Program Excel wprowadź — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atrybutu.

### <a name="excel-input---configuration"></a>Dane wejściowe — w programie Excel konfiguracji

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Excel` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji tabeli programu Excel. Zobacz [przy użyciu tabeli programu Excel powiązania w kodzie danych wejściowych](#excel-input-code).|
|**type**||Wymagana — musi być równa `excel`.|
|**direction**||Wymagana — musi być równa `in`.|
|**Tożsamość**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może być jednym z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe, za pomocą [Wyzwalacz HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> — Wykorzystanie tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Korzysta z tożsamości, reprezentowane przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Korzysta z tożsamości aplikacji funkcji.</li></ul>|
|**userId**|**UserId**  |Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromId`. Identyfikator jednostki użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromToken`. Token jest prawidłowy dla aplikacji funkcji. |
|**Ścieżka**|**Path**|Wymagana — ścieżka do skoroszytu programu Excel w usłudze OneDrive.|
|**worksheetName**|**worksheetName**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**TableName**|Nazwa tabeli. Jeśli nie zostanie określony, będzie używana zawartość arkusza.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Program Excel wprowadź — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Resource|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytuj pliki użytkownika|

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- [String []]
- Microsoft.Graph.WorkbookTable
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)










<a name="excel-output"></a>
## <a name="excel-output"></a>Dane wyjściowe programu Excel

Excel powiązania danych wyjściowych modyfikuje zawartość tabeli programu Excel przechowywane w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-output---example)
* [Atrybuty](#excel-output---attributes)
* [Konfiguracja](#excel-output---configuration)
* [Użycie](#excel-output---usage)

### <a name="excel-output---example"></a>W programie Excel dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Dane wyjściowe — Excel przykładowy skrypt w języku C#

Poniższy przykład dodaje wierszy do tabeli programu Excel.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu programu Excel powiązania danych wyjściowych:

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

Kod skryptu języka C# dodaje nowy wiersz do tabeli (zakłada się, że jedna kolumna) na podstawie danych wejściowych z ciągu zapytania:

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

#### <a name="excel-output---javascript-example"></a>Dane wyjściowe — Excel przykład JavaScript

Poniższy przykład dodaje wierszy do tabeli programu Excel.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu programu Excel powiązania danych wyjściowych:

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

Następujący kod JavaScript dodaje nowy wiersz do tabeli (zakłada się, że jedna kolumna) na podstawie danych wejściowych z ciągu zapytania.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Program Excel dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atrybutu.

### <a name="excel-output---configuration"></a>Dane wyjściowe — w programie Excel konfiguracji

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Excel` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji tokenu uwierzytelniania. Zobacz [wyjściowego przy użyciu tabeli programu Excel powiązania w kodzie](#excel-output-code).|
|**type**||Wymagana — musi być równa `excel`.|
|**direction**||Wymagana — musi być równa `out`.|
|**Tożsamość**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może być jednym z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe, za pomocą [Wyzwalacz HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> — Wykorzystanie tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Korzysta z tożsamości, reprezentowane przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Korzysta z tożsamości aplikacji funkcji.</li></ul>|
|**UserId** |**userId** |Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromId`. Identyfikator jednostki użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromToken`. Token jest prawidłowy dla aplikacji funkcji. |
|**Ścieżka**|**Path**|Wymagana — ścieżka do skoroszytu programu Excel w usłudze OneDrive.|
|**worksheetName**|**worksheetName**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**TableName**|Nazwa tabeli. Jeśli nie zostanie określony, będzie używana zawartość arkusza.|
|**updateType**|**UpdateType**|Wymagana — typ zmiany do wprowadzenia w tabeli. Może być jednym z następujących wartości:<ul><li><code>update</code> -Zastępuje zawartość tabeli w usłudze OneDrive.</li><li><code>append</code> -Dodaje ładunku na koniec tabeli w usłudze OneDrive przez utworzenie nowych wierszy.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Dane wyjściowe — Excel użycia

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Resource|Uprawnienie|
|--------|--------|
|Microsoft Graph|Masz pełny dostęp do plików użytkownika|

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- [String []]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Pliku wejściowego

Powiązania danych wejściowych plików usługi OneDrive odczytuje zawartość pliku przechowywanego w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-input---example)
* [Atrybuty](#file-input---attributes)
* [Konfiguracja](#file-input---configuration)
* [Użycie](#file-input---usage)

### <a name="file-input---example"></a>Plik danych wejściowych — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Plik wejściowy — przykładowy skrypt w języku C#

Poniższy przykład odczytuje plik, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu powiązania danych wejściowych plików usługi OneDrive:

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

#### <a name="file-input---javascript-example"></a>Plik wejściowy — przykład JavaScript

Poniższy przykład odczytuje plik, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu powiązania danych wejściowych plików usługi OneDrive:

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

Następujący kod JavaScript odczytuje plik określony w ciągu zapytania i zwraca jego długość.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Plik wejściowy — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atrybutu.

### <a name="file-input---configuration"></a>Plik danych wejściowych — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `OneDrive` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji dla pliku. Zobacz [przy użyciu pliku usługi OneDrive powiązania w kodzie danych wejściowych](#onedrive-input-code).|
|**type**||Wymagana — musi być równa `onedrive`.|
|**direction**||Wymagana — musi być równa `in`.|
|**Tożsamość**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może być jednym z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe, za pomocą [Wyzwalacz HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> — Wykorzystanie tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Korzysta z tożsamości, reprezentowane przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Korzysta z tożsamości aplikacji funkcji.</li></ul>|
|**userId**|**UserId**  |Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromId`. Identyfikator jednostki użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromToken`. Token jest prawidłowy dla aplikacji funkcji. |
|**Ścieżka**|**Path**|Wymagana — ścieżka do pliku w usłudze OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Plik wejściowy — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Resource|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytuj pliki użytkownika|

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Produkt wyjściowy pliku

Plik w usłudze OneDrive powiązania danych wyjściowych modyfikuje zawartość pliku przechowywanego w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-output---example)
* [Atrybuty](#file-output---attributes)
* [Konfiguracja](#file-output---configuration)
* [Użycie](#file-output---usage)

### <a name="file-output---example"></a>Plik wyjściowy — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Plik danych wyjściowych — przykładowy skrypt w języku C#

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacza HTTP usługi OneDrive powiązania danych wyjściowych:

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

Kod skryptu języka C# pobiera tekst z ciągu zapytania i zapisuje go do pliku tekstowego (FunctionsTest.txt zgodnie z definicją w poprzednim przykładzie) w katalogu głównym usługi OneDrive wywołującego:

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

#### <a name="file-output---javascript-example"></a>Plik danych wyjściowych — przykład JavaScript

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacza HTTP usługi OneDrive powiązania danych wyjściowych:

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

Kod JavaScript pobiera tekst z ciągu zapytania i zapisuje je do pliku tekstowego (FunctionsTest.txt zgodnie z definicją w powyższej konfiguracji) w katalogu głównym usługi OneDrive obiektu wywołującego.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Plik danych wyjściowych — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atrybutu.

### <a name="file-output---configuration"></a>Dane wyjściowe — plik konfiguracji

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `OneDrive` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji dla pliku. Zobacz [wyjściowego przy użyciu plików usługi OneDrive powiązania w kodzie](#onedrive-output-code).|
|**type**||Wymagana — musi być równa `onedrive`.|
|**direction**||Wymagana — musi być równa `out`.|
|**Tożsamość**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może być jednym z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe, za pomocą [Wyzwalacz HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> — Wykorzystanie tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Korzysta z tożsamości, reprezentowane przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Korzysta z tożsamości aplikacji funkcji.</li></ul>|
|**UserId** |**userId** |Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromId`. Identyfikator jednostki użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromToken`. Token jest prawidłowy dla aplikacji funkcji. |
|**Ścieżka**|**Path**|Wymagana — ścieżka do pliku w usłudze OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Plik danych wyjściowych — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Resource|Uprawnienie|
|--------|--------|
|Microsoft Graph|Masz pełny dostęp do plików użytkownika|

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Dane wyjściowe programu Outlook

Wiadomości programu Outlook danych wyjściowych powiązania wysyła wiadomość e-mail za pomocą programu Outlook.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#outlook-output---example)
* [Atrybuty](#outlook-output---attributes)
* [Konfiguracja](#outlook-output---configuration)
* [Użycie](#outlook-output---usage)

### <a name="outlook-output---example"></a>Dane wyjściowe programu Outlook — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Program Outlook dane wyjściowe — przykładowy skrypt w języku C#

Poniższy przykład wysyła wiadomość e-mail za pomocą programu Outlook.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu programu Outlook komunikat powiązania danych wyjściowych:

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

Kod skryptu języka C# wysyła wiadomość e-mail z obiektu wywołującego do adresatów określonych w ciągu zapytania:

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

#### <a name="outlook-output---javascript-example"></a>Program Outlook dane wyjściowe — przykład JavaScript

Poniższy przykład wysyła wiadomość e-mail za pomocą programu Outlook.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu programu Outlook komunikat powiązania danych wyjściowych:

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

Kod JavaScript wysyła wiadomość e-mail z obiektu wywołującego do adresatów określonych w ciągu zapytania:

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

### <a name="outlook-output---attributes"></a>Program Outlook dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) atrybutu.

### <a name="outlook-output---configuration"></a>Dane wyjściowe programu Outlook — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Outlook` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji wiadomości e-mail. Zobacz [wyjściowego przy użyciu wiadomości programu Outlook powiązania w kodzie](#outlook-output-code).|
|**type**||Wymagana — musi być równa `outlook`.|
|**direction**||Wymagana — musi być równa `out`.|
|**Tożsamość**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może być jednym z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe, za pomocą [Wyzwalacz HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> — Wykorzystanie tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Korzysta z tożsamości, reprezentowane przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Korzysta z tożsamości aplikacji funkcji.</li></ul>|
|**userId**|**UserId**  |Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromId`. Identyfikator jednostki użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromToken`. Token jest prawidłowy dla aplikacji funkcji. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Program Outlook dane wyjściowe — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Resource|Uprawnienie|
|--------|--------|
|Microsoft Graph|Wysyłaj pocztę jako użytkownik|

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)






## <a name="webhooks"></a>Elementy webhook

Elementy Webhook umożliwiają reagowanie na zdarzenia w programie Microsoft Graph. Aby zapewnić obsługę elementów webhook, funkcje są potrzebne do tworzenia, odświeżanie i reagowanie na _subskrypcje elementów webhook_. Rozwiązanie kompletny element webhook wymaga kombinacji następujących powiązania:
- A [wyzwalacza elementu webhook programu Microsoft Graph](#webhook-trigger) pozwala reagować na przychodzące elementu webhook.
- A [powiązania danych wejściowych subskrypcji elementu webhook programu Microsoft Graph](#webhook-input) można wyświetlić listę istniejących subskrypcji i możesz odświeżyć je.
- A [powiązania danych wyjściowych subskrypcja elementu webhook programu Microsoft Graph](#webhook-output) pozwala tworzyć i usuwać subskrypcje elementów webhook.

Powiązania, sami nie wymagają żadnych uprawnień usługi Azure AD, ale muszą zażądać uprawnień typ zasobu, który chcesz, aby reagować na istotne. Aby uzyskać listę, które uprawnienia są wymagane dla każdego typu zasobu, zobacz [uprawnienia subskrypcji](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Praca z elementami webhook w usłudze Microsoft Graph].





## <a name="webhook-trigger"></a>Wyzwalacza elementu Webhook

Wyzwalacza elementu webhook programu Microsoft Graph umożliwia korzystanie z funkcji reagować na przychodzące elementów webhook programu Microsoft Graph. Każde wystąpienie tego wyzwalacza pozwala reagować na jeden typ zasobu programu Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-trigger---example)
* [Atrybuty](#webhook-trigger---attributes)
* [Konfiguracja](#webhook-trigger---configuration)
* [Użycie](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Wyzwalacza elementu Webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Wyzwalacza elementu Webhook — przykładowy skrypt w języku C#

Poniższy przykład obsługuje elementy webhook dla przychodzących wiadomości programu Outlook. Aby użyć elementu webhook wyzwalacza możesz [Utwórz subskrypcję](#webhook-output---example), i możesz [odświeżania subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacza elementu webhook:

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

Kod skryptu języka C# reaguje na przychodzące wiadomości e-mail i rejestruje treści wysyłane przez adresata, a także zawierających "Usługi Azure Functions" w temacie:

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

#### <a name="webhook-trigger---javascript-example"></a>Wyzwalacza elementu Webhook — przykład JavaScript

Poniższy przykład obsługuje elementy webhook dla przychodzących wiadomości programu Outlook. Aby użyć elementu webhook wyzwalacza możesz [Utwórz subskrypcję](#webhook-output---example), i możesz [odświeżania subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacza elementu webhook:

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

Kod JavaScript reaguje na przychodzące wiadomości e-mail i rejestruje treści wysyłane przez adresata, a także zawierających "Usługi Azure Functions" w temacie:

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

### <a name="webhook-trigger---attributes"></a>Wyzwalacza elementu Webhook — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) atrybutu.

### <a name="webhook-trigger---configuration"></a>Wyzwalacza elementu Webhook — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `GraphWebHookTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji wiadomości e-mail. Zobacz [wyjściowego przy użyciu wiadomości programu Outlook powiązania w kodzie](#outlook-output-code).|
|**type**||Wymagana — musi być równa `graphWebhook`.|
|**direction**||Wymagana — musi być równa `trigger`.|
|**resourceType**|**ResourceType**|Wymagana — zasób grafu, dla których ta funkcja powinna odpowiadać do elementów webhook. Może być jednym z następujących wartości:<ul><li><code>#Microsoft.Graph.Message</code> -zmiany wprowadzone do wiadomości programu Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> -zmiany wprowadzone do elementów katalogu głównego usługi OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> -zmiany wprowadzone do kontaktów osobistych w programie Outlook.</li><li><code>#Microsoft.Graph.Event</code> -zmiany wprowadzone do elementów kalendarza programu Outlook.</li></ul>|

> [!Note]
> Aplikacja funkcji może mieć tylko jedną funkcję, która jest zarejestrowana dla podanego `resourceType` wartość.

### <a name="webhook-trigger---usage"></a>Wyzwalacza elementu Webhook — użycie

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- Zestaw SDK usługi Microsoft Graph typów istotne dla typu zasobów, takich jak `Microsoft.Graph.Message` lub `Microsoft.Graph.DriveItem`.
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Dane wejściowe elementu Webhook

Powiązania danych wejściowych elementu webhook programu Microsoft Graph umożliwia pobranie listy subskrypcji zarządzanych przez tę aplikację funkcji. Powiązanie odczytuje z magazynu aplikacji funkcji, dzięki czemu nie będzie odzwierciedlał inne subskrypcje utworzone na podstawie poza aplikacją.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-input---example)
* [Atrybuty](#webhook-input---attributes)
* [Konfiguracja](#webhook-input---configuration)
* [Użycie](#webhook-input---usage)

### <a name="webhook-input---example"></a>Dane wejściowe elementu Webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Element Webhook wprowadź — przykładowy skrypt w języku C#

Poniższy przykład pobiera wszystkie subskrypcje dla użytkownika wywołującego i usuwa je.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu powiązania danych wejściowych subskrypcji i który powiązania danych wyjściowych subskrypcja używa akcji usuwania:

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

Kod skryptu języka C# pobiera subskrypcje i usunie je:

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

#### <a name="webhook-input---javascript-example"></a>Element Webhook wprowadź — przykład JavaScript

Poniższy przykład pobiera wszystkie subskrypcje dla użytkownika wywołującego i usuwa je.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu powiązania danych wejściowych subskrypcji i który powiązania danych wyjściowych subskrypcja używa akcji usuwania:

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

Kod JavaScript pobiera subskrypcje i usunie je:

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

### <a name="webhook-input---attributes"></a>Element Webhook wprowadź — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atrybutu.

### <a name="webhook-input---configuration"></a>Dane wejściowe elementu Webhook — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `GraphWebHookSubscription` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji wiadomości e-mail. Zobacz [wyjściowego przy użyciu wiadomości programu Outlook powiązania w kodzie](#outlook-output-code).|
|**type**||Wymagana — musi być równa `graphWebhookSubscription`.|
|**direction**||Wymagana — musi być równa `in`.|
|**Filtr**|**Filtr**| Jeśli ustawiono `userFromRequest`, a następnie powiązanie pobierze tylko subskrypcje należące do użytkownika wywołującego (prawidłowa tylko w przypadku [Wyzwalacz HTTP]).| 

### <a name="webhook-input---usage"></a>Element Webhook wprowadź — użycie

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- ciąg]
- Tablice typu obiektu niestandardowego
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Dane wyjściowe elementu Webhook

Subskrypcja elementu webhook powiązania danych wyjściowych pozwala na tworzenie, usuwanie i Odśwież subskrypcje elementów webhook programu Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-output---example)
* [Atrybuty](#webhook-output---attributes)
* [Konfiguracja](#webhook-output---configuration)
* [Użycie](#webhook-output---usage)

### <a name="webhook-output---example"></a>Dane wyjściowe elementu Webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Element Webhook dane wyjściowe — przykładowy skrypt w języku C#

Poniższy przykład tworzy subskrypcję. Możesz [odświeżania subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu subskrypcji powiązania danych wyjściowych usługi za pomocą akcji tworzenia:

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

Kod skryptu języka C# rejestruje element webhook, który powiadomi tej aplikacji funkcji, kiedy użytkownika wywołującego odbiera wiadomości programu Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Element Webhook dane wyjściowe — przykład JavaScript

Poniższy przykład tworzy subskrypcję. Możesz [odświeżania subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu subskrypcji powiązania danych wyjściowych usługi za pomocą akcji tworzenia:

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

Kod JavaScript rejestruje element webhook, który powiadomi tej aplikacji funkcji, kiedy użytkownika wywołującego odbiera wiadomości programu Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Element Webhook dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atrybutu.

### <a name="webhook-output---configuration"></a>Dane wyjściowe elementu Webhook — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `GraphWebHookSubscription` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**name**||Wymagana — nazwa zmiennej, używane w kodzie funkcji wiadomości e-mail. Zobacz [wyjściowego przy użyciu wiadomości programu Outlook powiązania w kodzie](#outlook-output-code).|
|**type**||Wymagana — musi być równa `graphWebhookSubscription`.|
|**direction**||Wymagana — musi być równa `out`.|
|**Tożsamość**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może być jednym z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe, za pomocą [Wyzwalacz HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> — Wykorzystanie tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Korzysta z tożsamości, reprezentowane przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Korzysta z tożsamości aplikacji funkcji.</li></ul>|
|**userId**|**UserId**  |Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromId`. Identyfikator jednostki użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagane wtedy i tylko wtedy, gdy _tożsamości_ ustawiono `userFromToken`. Token jest prawidłowy dla aplikacji funkcji. |
|**Akcja**|**Akcja**|Niewymagana — Określa, należy wykonać akcję powiązania. Może być jednym z następujących wartości:<ul><li><code>create</code> -Rejestruje nowej subskrypcji.</li><li><code>delete</code> -Usuwa określoną subskrypcję.</li><li><code>refresh</code> — Odświeża określonej subskrypcji, aby zapobiec wygaśnięciu.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Wymagane wtedy i tylko wtedy, gdy _akcji_ ustawiono `create`. Określa zasób programu Microsoft Graph, który ma być monitorowany zmian. Zobacz [Praca z elementami webhook w usłudze Microsoft Graph]. |
|**changeType**|**ChangeType**|Wymagane wtedy i tylko wtedy, gdy _akcji_ ustawiono `create`. Wskazuje typ zmiany w subskrybowanym zasobie, która będzie powodować wygenerowanie powiadomienia. Obsługiwane wartości to: `created`, `updated`, `deleted`. Wiele wartości można łączyć, używając listę rozdzielonych przecinkami.|

### <a name="webhook-output---usage"></a>Element Webhook dane wyjściowe — użycie

Powiązanie uwidacznia następujące typy funkcji platformy .NET:
- string
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Odświeżania subskrypcji elementu Webhook

Istnieją dwa podejścia do odświeżania subskrypcji:

- Tożsamość aplikacji umożliwia postępowania w przypadku wszystkich subskrypcji. Będzie to wymagać zgody administratora usługi Azure Active Directory. Może to służyć przez wszystkie języki obsługiwane przez usługi Azure Functions.
- Użyj na tożsamość skojarzoną z każdej subskrypcji przez ręczne powiązanie każdego identyfikatora użytkownika. Będzie to wymagało kodu niestandardowego w celu wykonania powiązania. To należy używać tylko przez funkcje platformy .NET.

Ta sekcja zawiera przykład dla każdego z tych metod:

* [Przykład tożsamości aplikacji](#webhook-subscription-refresh---app-identity-example)
* [Przykład tożsamości użytkownika](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Odświeżania subskrypcji elementu Webhook — przykład tożsamości aplikacji

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykładowy skrypt w języku C#

W poniższym przykładzie użyto tożsamość aplikacji, aby odświeżyć subskrypcji.

*Function.json* definiuje wyzwalacza czasomierza z subskrypcją subskrypcję i powiązania danych wejściowych powiązania danych wyjściowych:

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

Kod skryptu języka C# odświeża subskrypcji:

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

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykładowy skrypt w języku C#

W poniższym przykładzie użyto tożsamość aplikacji, aby odświeżyć subskrypcji.

*Function.json* definiuje wyzwalacza czasomierza z subskrypcją subskrypcję i powiązania danych wejściowych powiązania danych wyjściowych:

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

Kod JavaScript odświeża subskrypcji:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Odświeżania subskrypcji elementu Webhook — przykład tożsamości użytkownika

W poniższym przykładzie użyto tożsamości użytkownika, aby odświeżyć subskrypcji.

*Function.json* plik definiuje wyzwalacza czasomierza i odracza powiązania danych wejściowych subskrypcji, aby kod funkcji:

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

Kod skryptu języka C# odświeża subskrypcje i tworzy powiązania danych wyjściowych w kodzie, przy użyciu tożsamości każdego użytkownika:

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

[Wyzwalacz HTTP]: functions-bindings-http-webhook.md
[Praca z elementami webhook w usłudze Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
