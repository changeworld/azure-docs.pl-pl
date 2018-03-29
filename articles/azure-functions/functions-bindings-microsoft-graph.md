---
title: Program Microsoft Graph powiązania dla usługi Azure Functions
description: Zrozumienie, jak używać programu Microsoft Graph wyzwalaczy i powiązań w usługi Azure Functions.
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 2de80760484ae1869b340898ea1e5f740fbc2883
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Program Microsoft Graph powiązania dla usługi Azure Functions

W tym artykule opisano sposób konfigurowania i pracować z programu Microsoft Graph wyzwalaczy i powiązań w funkcji platformy Azure. Z tych opcji, możesz użyć funkcji Azure do pracy z danymi, szczegółowych informacji i zdarzeń z [Microsoft Graph](https://graph.microsoft.io).

Rozszerzenia Microsoft Graph zapewnia następujące powiązania:
- [Powiązania danych wejściowych token uwierzytelniania](#token-input) pozwala korzystać z interfejsu API programu Graph żadnych firmy Microsoft.
- [Powiązania danych wejściowych tabelę programu Excel](#excel-input) umożliwia odczytywanie danych z programu Excel.
- [Powiązania wyjściowego tabelę programu Excel](#excel-output) umożliwia modyfikowanie danych programu Excel.
- A [powiązania danych wejściowych plików usługi OneDrive](#onedrive-input) umożliwia odczytanie plików z poziomu usługi OneDrive.
- A [powiązania wyjściowego pliku OneDrive](#onedrive-output) umożliwia zapisywanie do plików w usłudze OneDrive.
- [Powiązania wyjściowego wiadomości programu Outlook](#outlook-output) pozwala na wysyłanie wiadomości e-mail za pomocą programu Outlook.
- Kolekcja [Microsoft Graph elementu webhook wyzwalaczy i powiązań](#webhooks) służy do reagowania na zdarzenia z programu Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Program Microsoft Graph powiązania są obecnie w wersji zapoznawczej usługi Azure Functions wersji 2.x. Nie są obsługiwane w wersji funkcji 1.x.

## <a name="packages"></a>Pakiety

Token powiązania wejściowego uwierzytelniania jest udostępniany w [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) pakietu NuGet. Inne powiązania Microsoft Graph znajdują się w [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) pakietu. Kod źródłowy pakietów jest [microsoftgraph rozszerzenie, azure funkcji w-](https://github.com/Azure/azure-functions-microsoftgraph-extension/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="setting-up-the-extensions"></a>Konfigurowanie rozszerzeń

Program Microsoft Graph powiązania są dostępne za pośrednictwem _powiązanie rozszerzenia_. Rozszerzenia powiązania są opcjonalne składniki środowiska uruchomieniowego usługi Azure Functions. W tej sekcji pokazano, jak skonfigurować program Microsoft Graph i rozszerzenia tokenu uwierzytelniania.

### <a name="enabling-functions-20-preview"></a>Włączanie funkcji 2.0 w wersji zapoznawczej

Rozszerzenia powiązania są dostępne tylko dla 2.0 funkcji Azure w wersji zapoznawczej. 

Aby uzyskać informacje dotyczące sposobu konfigurowania aplikacji funkcji w wersji 2.0 wersja zapoznawcza funkcji aparatu plików wykonywalnych, zobacz [jak korzystać z wersji środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie z portalu Azure, przejdź do szablonu lub powiązanie, które odwołuje się on. Utwórz nową funkcję, a w ekranu wyboru szablonu, wybierz scenariusz "Microsoft Graph". Wybierz jeden z szablonów z tego scenariusza. Można też, przejdź do karty "Integracji" istniejącej funkcji i wybierz jedno z powiązań omówione w tym artykule.

W obu przypadkach zostanie wyświetlone ostrzeżenie, który określa rozszerzenia do zainstalowania. Kliknij przycisk **zainstalować** uzyskać rozszerzenia. Każde rozszerzenie tylko musi być zainstalowany po każdej funkcji aplikacji. 

> [!Note] 
> Plan przez proces instalacji w portalu może zająć do 10 minut.

Jeśli używasz programu Visual Studio, rozszerzeń można uzyskać, instalując [pakietów NuGet, które są wymienione w tym artykule](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurowanie uwierzytelniania / autoryzacji

Powiązania opisane w tym artykule wymagać potwierdzenia tożsamości do użycia. Dzięki temu program Microsoft Graph do wymuszenia uprawnień i inspekcji interakcji. Tożsamość może być dostęp do aplikacji lub aplikacji użytkownika. Aby skonfigurować tożsamość, skonfigurować [aplikacji usługi uwierzytelniania / autoryzacji](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) usłudze Azure Active Directory. Należy również żądania żadnych uprawnień zasobów, które wymagają funkcji.

> [!Note] 
> Rozszerzenia Microsoft Graph obsługuje tylko uwierzytelniania usługi Azure AD. Użytkownicy muszą przeprowadzić logowania za pomocą konta służbowego.

Jeśli używasz portalu Azure, zobaczysz ostrzeżenie poniżej monitu zainstaluj rozszerzenie. Ostrzeżenie monituje o skonfigurowanie aplikacji usługi uwierzytelniania / autoryzacji i żądanie wymaga uprawnień powiązania lub szablonu. Kliknij przycisk **Konfigurowanie usługi Azure AD teraz** lub **teraz dodać uprawnienia** odpowiednio.



<a name="token-input"></a>
## <a name="auth-token"></a>Token uwierzytelniania

Token powiązania wejściowego uwierzytelniania pobiera token do usługi Azure AD dla danego zasobu i udostępnia go kodu jako ciąg. Zasób może to być dowolna, dla którego aplikacji ma uprawnienia. 

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#auth-token---example)
* [Atrybuty](#auth-token---attributes)
* [Konfiguracja](#auth-token---configuration)
* [Użycie](#auth-token---usage)

### <a name="auth-token---example"></a>Token uwierzytelniania — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token uwierzytelniania: Przykładowy skrypt w języku C#

Poniższy przykład pobiera informacje o profilu użytkownika.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z tokenu powiązania wejściowego:

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

Kod skryptu C# używa tokenu do wywoływania HTTP Microsoft Graph i zwraca wynik:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Token uwierzytelniania — przykład JavaScript

Poniższy przykład pobiera informacje o profilu użytkownika.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z tokenu powiązania wejściowego:

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

Kod JavaScript używa tokenu do wywoływania HTTP Microsoft Graph i zwraca wynik.

```js
const rp = require('request-promise');

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

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [tokenu](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) atrybutu.

### <a name="auth-token---configuration"></a>Token uwierzytelniania — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Token` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używane w kodzie funkcja tokenu uwierzytelniania. Zobacz [przy użyciu tokenu uwierzytelniania wejściowych powiązania z kodu](#token-input-code).|
|**Typ**||Wymagana — musi być ustawiona `token`.|
|**Kierunek**||Wymagana — musi być ustawiona `in`.|
|**Tożsamości**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może to być jedna z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe z [wyzwalacza HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> -Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Używa tożsamości reprezentowany przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Ma zostać użyta tożsamość aplikacji funkcji.</li></ul>|
|**Nazwa użytkownika**|**Nazwa użytkownika**  |Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromId`. Użytkownik identyfikator podmiotu zabezpieczeń skojarzone z wcześniej zalogowanego użytkownika.|
|**UserToken**|**UserToken**|Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromToken`. Token nieprawidłowy dla funkcji aplikacji. |
|**Zasób**|**resource**|Wymagana — adres URL zasobu usługi Azure AD, dla którego token jest wymagany.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token uwierzytelniania — użycie

Powiązanie sam nie wymaga żadnych uprawnień usługi Azure AD, ale w zależności od tego, jak jest używany, konieczne może być zażądania dodatkowych uprawnień. Sprawdź wymagania zasób, do którego chcesz uzyskać dostęp z tokenem.

Token jest zawsze widoczne dla kodu jako ciąg.




<a name="excel-input"></a>
## <a name="excel-input"></a>Dane wejściowe programu Excel

Powiązania wejściowego tabeli programu Excel odczytuje zawartość tabeli programu Excel zapisanych w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-input---example)
* [Atrybuty](#excel-input---attributes)
* [Konfiguracja](#excel-input---configuration)
* [Użycie](#excel-input---usage)

### <a name="excel-input---example"></a>Excel danych wejściowych — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel wejściowych — przykładowy skrypt w języku C#

Następujące *function.json* plik definiuje wyzwalacz protokołu HTTP z powiązaniem wejściowy programu Excel:

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

Poniższy kod skryptu C# odczytuje zawartość z określonej tabeli i zwraca je do użytkownika:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel wejściowych — przykład JavaScript

Następujące *function.json* plik definiuje wyzwalacz protokołu HTTP z powiązaniem wejściowy programu Excel:

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

### <a name="excel-input---attributes"></a>Excel wejściowych — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atrybutu.

### <a name="excel-input---configuration"></a>Dane wejściowe — w programie Excel konfiguracji

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Excel` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używane w kodzie funkcji tabeli programu Excel. Zobacz [przy użyciu tabeli programu Excel wejściowych powiązania z kodu](#excel-input-code).|
|**Typ**||Wymagana — musi być ustawiona `excel`.|
|**Kierunek**||Wymagana — musi być ustawiona `in`.|
|**Tożsamości**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może to być jedna z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe z [wyzwalacza HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> -Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Używa tożsamości reprezentowany przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Ma zostać użyta tożsamość aplikacji funkcji.</li></ul>|
|**Nazwa użytkownika**|**Nazwa użytkownika**  |Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromId`. Użytkownik identyfikator podmiotu zabezpieczeń skojarzone z wcześniej zalogowanego użytkownika.|
|**UserToken**|**UserToken**|Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromToken`. Token nieprawidłowy dla funkcji aplikacji. |
|**Ścieżka**|**Ścieżka**|Wymagana — ścieżka w usłudze OneDrive do skoroszytu programu Excel.|
|**WorksheetName**|**WorksheetName**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**TableName**|Nazwa tabeli. Jeśli nie zostanie określony, będzie używany zawartość arkusza.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel wejściowych — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:
|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytuj pliki użytkownika|

Powiązanie udostępnia następujące funkcje platformy .NET:
- string[][]
- Microsoft.Graph.WorkbookTable
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)










<a name="excel-output"></a>
## <a name="excel-output"></a>Dane wyjściowe programu Excel

Excel powiązania wyjściowego modyfikuje zawartość tabeli programu Excel zapisanych w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-output---example)
* [Atrybuty](#excel-output---attributes)
* [Konfiguracja](#excel-output---configuration)
* [Użycie](#excel-output---usage)

### <a name="excel-output---example"></a>Excel dane wyjściowe — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Wyjście - Excel przykładowy skrypt w języku C#

Poniższy przykład umożliwia dodanie wierszy do tabeli programu Excel.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu programu Excel powiązania wyjściowego:

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

Kod skryptu C# dodaje nowy wiersz do tabeli (przyjęto, że jedna kolumna) na podstawie danych wprowadzonych z ciągu zapytania:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
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

#### <a name="excel-output---javascript-example"></a>Excel output — przykład JavaScript

Poniższy przykład umożliwia dodanie wierszy do tabeli programu Excel.

*Function.json* plik definiuje wyzwalacza HTTP przy użyciu programu Excel powiązania wyjściowego:

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

Następujący kod JavaScript dodaje nowy wiersz do tabeli (przyjęto, że jedna kolumna) na podstawie danych wprowadzonych z ciągu zapytania.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel output — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) atrybutu.

### <a name="excel-output---configuration"></a>Dane wyjściowe — w programie Excel konfiguracji

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Excel` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używane w kodzie funkcja tokenu uwierzytelniania. Zobacz [przy użyciu tabeli programu Excel powiązania z kodu wyjściowego](#excel-output-code).|
|**Typ**||Wymagana — musi być ustawiona `excel`.|
|**Kierunek**||Wymagana — musi być ustawiona `out`.|
|**Tożsamości**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może to być jedna z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe z [wyzwalacza HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> -Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Używa tożsamości reprezentowany przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Ma zostać użyta tożsamość aplikacji funkcji.</li></ul>|
|**Nazwa użytkownika** |**Nazwa użytkownika** |Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromId`. Użytkownik identyfikator podmiotu zabezpieczeń skojarzone z wcześniej zalogowanego użytkownika.|
|**UserToken**|**UserToken**|Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromToken`. Token nieprawidłowy dla funkcji aplikacji. |
|**Ścieżka**|**Ścieżka**|Wymagana — ścieżka w usłudze OneDrive do skoroszytu programu Excel.|
|**WorksheetName**|**WorksheetName**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**TableName**|Nazwa tabeli. Jeśli nie zostanie określony, będzie używany zawartość arkusza.|
|**updateType**|**UpdateType**|Wymagana — typ zmiany należy do tabeli. Może to być jedna z następujących wartości:<ul><li><code>update</code> -Zastępuje zawartość tabeli w usłudze OneDrive.</li><li><code>append</code> -Dodaje ładunku na koniec tabeli w usłudze OneDrive, tworząc nowe wiersze.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Wyjście - Excel użycia

To powiązanie wymaga następujących uprawnień usługi Azure AD:
|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Miej pełny dostęp do plików użytkownika|

Powiązanie udostępnia następujące funkcje platformy .NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Plik danych wejściowych

Powiązania wejściowego pliku OneDrive odczytuje zawartość pliku zapisanych w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-input---example)
* [Atrybuty](#file-input---attributes)
* [Konfiguracja](#file-input---configuration)
* [Użycie](#file-input---usage)

### <a name="file-input---example"></a>Plik wejściowy — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Plik wejściowy — przykładowy skrypt w języku C#

Poniższy przykład odczytuje plik, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z powiązaniem wejściowych plików usługi OneDrive:

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

Kod skryptu C# odczytuje plik określony w ciągu kwerendy i rejestruje jego długość:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Plik wejściowy — przykład JavaScript

Poniższy przykład odczytuje plik, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z powiązaniem wejściowych plików usługi OneDrive:

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

Następujący kod JavaScript odczytuje plik określony w ciągu kwerendy i zwraca jego długość.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Plik wejściowy — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atrybutu.

### <a name="file-input---configuration"></a>Plik wejściowy — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `OneDrive` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używana w funkcji kodu dla pliku. Zobacz [przy użyciu pliku OneDrive wejściowych powiązania z kodu](#onedrive-input-code).|
|**Typ**||Wymagana — musi być ustawiona `onedrive`.|
|**Kierunek**||Wymagana — musi być ustawiona `in`.|
|**Tożsamości**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może to być jedna z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe z [wyzwalacza HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> -Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Używa tożsamości reprezentowany przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Ma zostać użyta tożsamość aplikacji funkcji.</li></ul>|
|**Nazwa użytkownika**|**Nazwa użytkownika**  |Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromId`. Użytkownik identyfikator podmiotu zabezpieczeń skojarzone z wcześniej zalogowanego użytkownika.|
|**UserToken**|**UserToken**|Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromToken`. Token nieprawidłowy dla funkcji aplikacji. |
|**Ścieżka**|**Ścieżka**|Wymagana — ścieżka w usłudze OneDrive do pliku.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Plik wejściowy — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:
|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytuj pliki użytkownika|

Powiązanie udostępnia następujące funkcje platformy .NET:
- byte[]
- Strumień
- ciąg
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Dane wyjściowe pliku

Plik OneDrive powiązania wyjściowego modyfikuje zawartość pliku zapisanych w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-output---example)
* [Atrybuty](#file-output---attributes)
* [Konfiguracja](#file-output---configuration)
* [Użycie](#file-output---usage)

### <a name="file-output---example"></a>Plik wyjściowy — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Plik wyjściowy — przykładowy skrypt w języku C#

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacza HTTP z usługi OneDrive powiązania wyjściowego:

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

Kod skryptu C# pobiera tekst z ciągu zapytania i zapisuje go w pliku tekstowym (FunctionsTest.txt zgodnie z definicją w poprzednim przykładzie) w katalogu głównym wywołującego OneDrive:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```

#### <a name="file-output---javascript-example"></a>Plik wyjściowy — przykład JavaScript

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

*Function.json* plik definiuje wyzwalacza HTTP z usługi OneDrive powiązania wyjściowego:

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

Kod JavaScript pobiera tekst z ciągu zapytania i zapisuje go w pliku tekstowym (FunctionsTest.txt określone w powyższej konfiguracji) w katalogu głównym usługi OneDrive obiektu wywołującego.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Plik wyjściowy — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) atrybutu.

### <a name="file-output---configuration"></a>Dane wyjściowe — plik konfiguracji

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `OneDrive` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używana w funkcji kodu dla pliku. Zobacz [przy użyciu pliku OneDrive powiązania z kodu wyjściowego](#onedrive-output-code).|
|**Typ**||Wymagana — musi być ustawiona `onedrive`.|
|**Kierunek**||Wymagana — musi być ustawiona `out`.|
|**Tożsamości**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może to być jedna z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe z [wyzwalacza HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> -Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Używa tożsamości reprezentowany przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Ma zostać użyta tożsamość aplikacji funkcji.</li></ul>|
|**Nazwa użytkownika** |**Nazwa użytkownika** |Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromId`. Użytkownik identyfikator podmiotu zabezpieczeń skojarzone z wcześniej zalogowanego użytkownika.|
|**UserToken**|**UserToken**|Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromToken`. Token nieprawidłowy dla funkcji aplikacji. |
|**Ścieżka**|**Ścieżka**|Wymagana — ścieżka w usłudze OneDrive do pliku.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Plik wyjściowy — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:
|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Miej pełny dostęp do plików użytkownika|

Powiązanie udostępnia następujące funkcje platformy .NET:
- byte[]
- Strumień
- ciąg
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Dane wyjściowe programu Outlook

Wiadomości programu Outlook output powiązanie wysyła wiadomości e-mail za pomocą programu Outlook.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#outlook-output---example)
* [Atrybuty](#outlook-output---attributes)
* [Konfiguracja](#outlook-output---configuration)
* [Użycie](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Dane wyjściowe programu Outlook — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Wyjście - Outlook przykładowy skrypt w języku C#

W poniższym przykładzie wysyłana wiadomość e-mail za pomocą programu Outlook.

*Function.json* plik definiuje wyzwalacza HTTP z programu Outlook powiązania wyjściowego komunikat:

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

Kod skryptu C# wysyła wiadomości e-mail z wywołującego do adresatów określonych w ciągu zapytania:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
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

#### <a name="outlook-output---javascript-example"></a>Wyjście - Outlook przykład JavaScript

W poniższym przykładzie wysyłana wiadomość e-mail za pomocą programu Outlook.

*Function.json* plik definiuje wyzwalacza HTTP z programu Outlook powiązania wyjściowego komunikat:

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

Kod JavaScript wysyła wiadomości e-mail z wywołującego do adresatów określonych w ciągu zapytania:

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

### <a name="outlook-output---attributes"></a>Outlook output — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) atrybutu.

### <a name="outlook-output---configuration"></a>Dane wyjściowe programu Outlook — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Outlook` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używane w kodzie funkcji wiadomości e-mail. Zobacz [wiadomości programu Outlook powiązania z kodu wyjściowego](#outlook-output-code).|
|**Typ**||Wymagana — musi być ustawiona `outlook`.|
|**Kierunek**||Wymagana — musi być ustawiona `out`.|
|**Tożsamości**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może to być jedna z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe z [wyzwalacza HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> -Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Używa tożsamości reprezentowany przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Ma zostać użyta tożsamość aplikacji funkcji.</li></ul>|
|**Nazwa użytkownika**|**Nazwa użytkownika**  |Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromId`. Użytkownik identyfikator podmiotu zabezpieczeń skojarzone z wcześniej zalogowanego użytkownika.|
|**UserToken**|**UserToken**|Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromToken`. Token nieprawidłowy dla funkcji aplikacji. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Wyjście - Outlook użycia

To powiązanie wymaga następujących uprawnień usługi Azure AD:
|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Wysyłania wiadomości e-mail jako użytkownik|

Powiązanie udostępnia następujące funkcje platformy .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- ciąg
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)






## <a name="webhooks"></a>Elementy webhook

Elementów Webhook umożliwiają reagowania na zdarzenia w programie Microsoft Graph. Aby zapewnić obsługę elementów webhook, funkcje są potrzebne do tworzenia, Odśwież i reagowania na _subskrypcje elementu webhook_. Rozwiązanie elementu webhook pełną wymaga kombinację następujących powiązania:
- A [wyzwalacza elementu webhook Microsoft Graph](#webhook-trigger) służy do reagowania na przychodzące elementu webhook.
- A [powiązania danych wejściowych subskrypcji elementu webhook Microsoft Graph](#webhook-input) pozwala na liście istniejące subskrypcje i opcjonalnie odświeżać je.
- A [powiązania wyjściowego subskrypcji elementu webhook Microsoft Graph](#webhook-output) służy do tworzenia lub usuwania elementu webhook subskrypcji.

Powiązania się nie wymagają żadnych uprawnień usługi Azure AD, ale musisz zażądać uprawnień odpowiedni typ zasobu, który ma zostać reagowania na. Dla listy, które są potrzebne uprawnienia dla każdego typu zasobu, zobacz [uprawnienia subskrypcji](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Praca z elementów webhook w programie Microsoft Graph].





## <a name="webhook-trigger"></a>Wyzwalacz elementu Webhook

Wyzwalacz webhook Microsoft Graph umożliwia funkcja reagowanie na element webhook przychodzące z programu Microsoft Graph. Każde wystąpienie tego wyzwalacza mogą reagować na jeden typ zasobu Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-trigger---example)
* [Atrybuty](#webhook-trigger---attributes)
* [Konfiguracja](#webhook-trigger---configuration)
* [Użycie](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Wyzwalacz Webhook — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Element Webhook wyzwalacza — przykładowy skrypt w języku C#

Poniższy przykład obsługuje elementów webhook przychodzących wiadomości programu Outlook. Aby użyć elementu webhook wyzwolenia można [Utwórz subskrypcję](#webhook-output---example), i będzie możliwe [Odśwież subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacz elementu webhook:

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

Kod skryptu C# reaguje na przychodzące wiadomości e-mail i rejestruje treść tych wysyłane przez odbiorcę i zawierający "Usługi Azure Functions" w temacie:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Element Webhook wyzwalacza — przykład JavaScript

Poniższy przykład obsługuje elementów webhook przychodzących wiadomości programu Outlook. Aby użyć elementu webhook wyzwolenia można [Utwórz subskrypcję](#webhook-output---example), i będzie możliwe [Odśwież subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacz elementu webhook:

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

Kod JavaScript reaguje na przychodzące wiadomości e-mail i rejestruje treść tych wysyłane przez odbiorcę i zawierający "Usługi Azure Functions" w temacie:

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

### <a name="webhook-trigger---attributes"></a>Element Webhook wyzwalacza — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) atrybutu.

### <a name="webhook-trigger---configuration"></a>Wyzwalacz Webhook — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `GraphWebHookTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używane w kodzie funkcji wiadomości e-mail. Zobacz [wiadomości programu Outlook powiązania z kodu wyjściowego](#outlook-output-code).|
|**Typ**||Wymagana — musi być ustawiona `graphWebhook`.|
|**Kierunek**||Wymagana — musi być ustawiona `trigger`.|
|**resourceType**|**ResourceType**|Wymagana — zasobów wykresu, dla których ta funkcja powinno odpowiedzieć na elementów webhook. Może to być jedna z następujących wartości:<ul><li><code>#Microsoft.Graph.Message</code> -zmiany wprowadzone w wiadomości programu Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> -zmiany wprowadzone do usługi OneDrive elementów głównych.</li><li><code>#Microsoft.Graph.Contact</code> -zmiany wprowadzone do osobistego kontaktów programu Outlook.</li><li><code>#Microsoft.Graph.Event</code> -zmiany wprowadzone do elementów kalendarza programu Outlook.</li></ul>|

> [!Note]
> Aplikacja funkcji mogą mieć tylko jedną funkcję rejestrowane dla danego `resourceType` wartość.

### <a name="webhook-trigger---usage"></a>Element Webhook wyzwalacza — użycie

Powiązanie udostępnia następujące funkcje platformy .NET:
- Zestaw SDK programu Microsoft Graph typy odpowiedni typ zasobu, takich jak `Microsoft.Graph.Message` lub `Microsoft.Graph.DriveItem`.
- Typów obiektów niestandardowych (przy użyciu wiązania modelu strukturalnych)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Dane wejściowe elementu Webhook

Powiązania wejściowego elementu webhook Microsoft Graph umożliwia pobieranie listy zarządzane przez tę aplikację funkcji subskrypcji. Powiązanie odczytuje z magazynu aplikacji funkcji, dzięki czemu nie odzwierciedla inne subskrypcje utworzone na podstawie poza aplikacją.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-input---example)
* [Atrybuty](#webhook-input---attributes)
* [Konfiguracja](#webhook-input---configuration)
* [Użycie](#webhook-input---usage)

### <a name="webhook-input---example"></a>Dane wejściowe elementu Webhook — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Element Webhook wejściowych — przykładowy skrypt w języku C#

W poniższym przykładzie pobiera wszystkich subskrypcji użytkownika wywołującego i usuwa je.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z powiązaniem wejściowych subskrypcji i wyjścia subskrypcji, powiązanie używa Akcja usuwania:

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

Kod skryptu C# pobiera subskrypcje i usunie je:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Element Webhook wejściowych — przykład JavaScript

W poniższym przykładzie pobiera wszystkich subskrypcji użytkownika wywołującego i usuwa je.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z powiązaniem wejściowych subskrypcji i wyjścia subskrypcji, powiązanie używa Akcja usuwania:

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

### <a name="webhook-input---attributes"></a>Element Webhook wejściowych — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atrybutu.

### <a name="webhook-input---configuration"></a>Dane wejściowe elementu Webhook — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `GraphWebHookSubscription` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używane w kodzie funkcji wiadomości e-mail. Zobacz [wiadomości programu Outlook powiązania z kodu wyjściowego](#outlook-output-code).|
|**Typ**||Wymagana — musi być ustawiona `graphWebhookSubscription`.|
|**Kierunek**||Wymagana — musi być ustawiona `in`.|
|**Filtr**|**Filtr**| Jeśli ustawioną `userFromRequest`, a następnie powiązanie pobierze tylko subskrypcje należących do użytkownika wywołującego (prawidłowa tylko w przypadku [wyzwalacza HTTP]).| 

### <a name="webhook-input---usage"></a>Element Webhook wejściowych — użycie

Powiązanie udostępnia następujące funkcje platformy .NET:
- ciąg]
- Tablice typu niestandardowego obiektu
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Dane wyjściowe elementu Webhook

Subskrypcja elementu webhook powiązania danych wyjściowych pozwala na tworzenie, usuwanie i odświeżanie subskrypcji elementu webhook w programie Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-output---example)
* [Atrybuty](#webhook-output---attributes)
* [Konfiguracja](#webhook-output---configuration)
* [Użycie](#webhook-output---usage)

### <a name="webhook-output---example"></a>Dane wyjściowe elementu Webhook — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Element Webhook wyjście - przykładowy skrypt w języku C#

Poniższy przykład tworzy subskrypcję. Możesz [Odśwież subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z danymi wyjściowymi powiązanie, korzystając z akcji tworzenia subskrypcji:

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

Kod skryptu C# rejestruje elementu webhook, który powiadomi to funkcja aplikacji podczas wywoływania użytkownik odbiera wiadomości programu Outlook:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Element Webhook output — przykład JavaScript

Poniższy przykład tworzy subskrypcję. Możesz [Odśwież subskrypcji](#webhook-subscription-refresh) aby zapobiec wygaśnięciu.

*Function.json* plik definiuje wyzwalacz protokołu HTTP z danymi wyjściowymi powiązanie, korzystając z akcji tworzenia subskrypcji:

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

Kod JavaScript rejestruje elementu webhook, który powiadomi to funkcja aplikacji podczas wywoływania użytkownik odbiera wiadomości programu Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Element Webhook output — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) atrybutu.

### <a name="webhook-output---configuration"></a>Dane wyjściowe elementu Webhook — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `GraphWebHookSubscription` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwa**||Wymagana — nazwa zmiennej używane w kodzie funkcji wiadomości e-mail. Zobacz [wiadomości programu Outlook powiązania z kodu wyjściowego](#outlook-output-code).|
|**Typ**||Wymagana — musi być ustawiona `graphWebhookSubscription`.|
|**Kierunek**||Wymagana — musi być ustawiona `out`.|
|**Tożsamości**|**Tożsamość**|Wymagana — tożsamości, która będzie służyć do wykonania akcji. Może to być jedna z następujących wartości:<ul><li><code>userFromRequest</code> — Jedyne prawidłowe z [wyzwalacza HTTP]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code> -Używa tożsamości wcześniej zalogowanego użytkownika o określonym identyfikatorze. Zobacz <code>userId</code> właściwości.</li><li><code>userFromToken</code> -Używa tożsamości reprezentowany przez określony token. Zobacz <code>userToken</code> właściwości.</li><li><code>clientCredentials</code> -Ma zostać użyta tożsamość aplikacji funkcji.</li></ul>|
|**Nazwa użytkownika**|**Nazwa użytkownika**  |Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromId`. Użytkownik identyfikator podmiotu zabezpieczeń skojarzone z wcześniej zalogowanego użytkownika.|
|**UserToken**|**UserToken**|Wymagane w przypadku i tylko wtedy, gdy _tożsamości_ ma ustawioną wartość `userFromToken`. Token nieprawidłowy dla funkcji aplikacji. |
|**Akcja**|**Akcja**|Niewymagana — określa akcji powiązanie powinny być obsługiwane. Może to być jedna z następujących wartości:<ul><li><code>create</code> -Rejestruje nową subskrypcję.</li><li><code>delete</code> — Usuwa z określonej subskrypcji.</li><li><code>refresh</code> -Odświeża określonej subskrypcji, aby zapobiec wygaśnięciu.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Wymagane w przypadku i tylko wtedy, gdy _akcji_ ma ustawioną wartość `create`. Określa zasób Microsoft Graph, które będą monitorowane zmian. Zobacz [Praca z elementów webhook w programie Microsoft Graph]. |
|**changeType**|**ChangeType**|Wymagane w przypadku i tylko wtedy, gdy _akcji_ ma ustawioną wartość `create`. Wskazuje typ zmiany w subskrybowanego zasób, który zostanie podniesiony powiadomienie. Obsługiwane wartości to: `created`, `updated`, `deleted`. Można łączyć wiele wartości, używając listy rozdzielanej przecinkami.|

### <a name="webhook-output---usage"></a>Element Webhook wyjście - użycia

Powiązanie udostępnia następujące funkcje platformy .NET:
- ciąg
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Odświeżanie subskrypcji elementu Webhook

Istnieją dwa podejścia do odświeżania subskrypcji:

- Użyj tożsamości aplikacji na wypadek wszystkie subskrypcje. Będzie to wymagało zgody administratora usługi Azure Active Directory. Może być używany przez wszystkie języki obsługiwane przez usługi Azure Functions.
- Użyj tożsamość skojarzoną z każdej subskrypcji przez ręcznie powiązanie każdy identyfikator użytkownika. Będzie to wymagało kodu niestandardowego w celu wykonania powiązania. Może być używany tylko przez funkcje środowiska .NET.

Ta sekcja zawiera przykład dla każdego z tych metod:

* [Przykład tożsamości aplikacji](#webhook-subscription-refresh---app-identity-example)
* [Przykład tożsamości użytkownika](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Odświeżanie subskrypcji elementu Webhook — przykład tożsamości aplikacji

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykładowy skrypt w języku C#

W poniższym przykładzie użyto tożsamości aplikacji aby odświeżyć subskrypcji.

*Function.json* definiuje wyzwalacza bazującego na czasomierzu z subskrypcją powiązania wejściowego i subskrypcji powiązania wyjściowego:

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

Kod skryptu C# odświeża subskrypcji:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykładowy skrypt w języku C#

W poniższym przykładzie użyto tożsamości aplikacji aby odświeżyć subskrypcji.

*Function.json* definiuje wyzwalacza bazującego na czasomierzu z subskrypcją powiązania wejściowego i subskrypcji powiązania wyjściowego:

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
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Odświeżanie subskrypcji elementu Webhook — przykład tożsamości użytkownika

W poniższym przykładzie użyto tożsamości użytkownika, aby odświeżyć subskrypcji.

*Function.json* plików definiuje wyzwalacza bazującego na czasomierzu i różni się subskrypcji powiązania wejściowego kodu funkcji:

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

Kod skryptu C# odświeża subskrypcje i tworzy powiązania danych wyjściowych w kodzie za pomocą tożsamości każdego użytkownika:

```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
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
            log.Info($"Refreshing subscription {subscription}");
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
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)

[wyzwalacza HTTP]: functions-bindings-http-webhook.md
[Praca z elementów webhook w programie Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
