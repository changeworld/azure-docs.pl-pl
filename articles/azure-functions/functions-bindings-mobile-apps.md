---
title: Powiązania funkcji Mobile Apps dla usługi Azure Functions
description: Dowiedz się, jak używać powiązań usługi Azure Mobile Apps w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 965ec88b39336e71d2779da4b7cd31b9c26e28be
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480366"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Powiązania funkcji Mobile Apps dla usługi Azure Functions 

> [!NOTE]
> Powiązania funkcji Mobile Apps usługi Azure są dostępne tylko dla usługi Azure Functions 1.x. Nie są obsługiwane w usłudze Azure Functions 2.x.

W tym artykule wyjaśniono, jak pracować z [usługi Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) powiązań w usłudze Azure Functions. Usługi Azure Functions obsługuje danych wejściowych i wyjściowych powiązania funkcji Mobile Apps.

Powiązania funkcji Mobile Apps pozwalają na odczytywanie i aktualizowanie tabel danych w aplikacjach mobilnych.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania funkcji Mobile Apps znajdują się w [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) pakietu NuGet w wersji 1.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Dane wejściowe

Powiązania danych wejściowych funkcji Mobile Apps ładuje rekord z punktu końcowego tabel mobilnych i przekazuje ją do funkcji. W C# i F# funkcje, wszelkie zmiany wprowadzone do rekordu są automatycznie wysyłane do tabeli, jeśli funkcja kończy się pomyślnie.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Dane wejściowe — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano powiązania danych wejściowych funkcji Mobile Apps, w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja jest wyzwalana przez komunikatu w kolejce, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i modyfikuje jego `Text` właściwości.

Oto powiązanie danych w *function.json* pliku:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Dane wejściowe — JavaScript

W poniższym przykładzie pokazano powiązania danych wejściowych funkcji Mobile Apps, w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja jest wyzwalana przez komunikatu w kolejce, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i modyfikuje jego `Text` właściwości.

Oto powiązanie danych w *function.json* pliku:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atrybutu.

Aby uzyskać informacji na temat właściwości atrybutów, które można skonfigurować, zobacz [w poniższej sekcji konfiguracji](#input---configuration).

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `MobileTable` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type**|| Musi być równa "mobileTable"|
| **direction**||Musi być równa "in"|
| **name**|| Nazwa parametru wejściowego w sygnaturze funkcji.|
|**tableName** |**TableName**|Nazwa tabeli danych z aplikacji mobilnej|
| **id**| **Identyfikator** | Identyfikator rekordu do pobrania. Może być statyczne lub w oparciu o wyzwalacz, który wywołuje funkcję. Na przykład, jeśli używasz wyzwalacza kolejki funkcji, następnie `"id": "{queueTrigger}"` używa wartość ciągu komunikatu w kolejce jako identyfikator rekordu do pobrania.|
|**połączenia**|**połączenia**|Nazwa ustawienia aplikacji, która ma adres URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymagane operacje REST względem Twojej aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, a następnie podaj nazwę ustawienia aplikacji w `connection` właściwości w swojej powiązania danych wejściowych. Adres URL wygląda `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Nazwa ustawienia aplikacji, który ma klucz interfejsu API aplikacji mobilnej. Podaj if klucza interfejsu API możesz [zaimplementować klucza interfejsu API w aplikacji mobilnej Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), lub [zaimplementować klucza interfejsu API w aplikacji mobilnej platformy .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, utworzyć ustawienie aplikacji w swojej aplikacji funkcji, który zawiera klucz interfejsu API, a następnie dodaj `apiKey` właściwości w swojej powiązania danych wejściowych o nazwie ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza interfejsu API z klientów aplikacji mobilnej. On tylko należy rozdzielić bezpiecznie klientom po stronie usługi, takie jak usługi Azure Functions. Usługa Azure Functions przechowuje Twoje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, dzięki czemu nie są sprawdzane w Twoim repozytorium kontroli źródła. To chroni poufne informacje.

## <a name="input---usage"></a>Dane wejściowe — użycie

W funkcji języka C#, gdy zostanie znaleziony rekord o określonym identyfikatorze, jest przekazywana do nazwanego [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parametru. Gdy rekord nie zostanie znaleziony, wartość parametru jest `null`. 

W przypadku funkcji JavaScript rekordu jest przekazywana do `context.bindings.<name>` obiektu. Gdy rekord nie zostanie znaleziony, wartość parametru jest `null`. 

W C# i F# funkcje, wszelkie zmiany wprowadzone w danych wejściowych rekordu (parametr wejściowy) są automatycznie wysyłane do tabeli, jeśli funkcja kończy się pomyślnie. Nie można zmodyfikować rekord w funkcji języka JavaScript.

## <a name="output"></a>Output

Użyj funkcji Mobile Apps powiązania danych wyjściowych usługi zapisać nowy rekord tabeli funkcji Mobile Apps.  

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , jest wyzwalana przez komunikatu w kolejce i tworzy rekord w tabeli aplikacji mobilnej.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano powiązania w danych wyjściowych funkcji Mobile Apps *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja jest wyzwalana przez komunikatu w kolejce i tworzy nowy rekord z wartością ustaloną `Text` właściwości.

Oto powiązanie danych w *function.json* pliku:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie pokazano powiązania w danych wyjściowych funkcji Mobile Apps *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja jest wyzwalana przez komunikatu w kolejce i tworzy nowy rekord z wartością ustaloną `Text` właściwości.

Oto powiązanie danych w *function.json* pliku:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atrybutu.

Aby uzyskać informacji na temat właściwości atrybutów, które można skonfigurować, zobacz [dane wyjściowe — Konfiguracja](#output---configuration). Oto `MobileTable` przykład atrybutu w podpisie metody:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `MobileTable` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type**|| Musi być równa "mobileTable"|
| **direction**||Musi być równa "out"|
| **name**|| Nazwa parametru wyjściowego w sygnaturze funkcji.|
|**tableName** |**TableName**|Nazwa tabeli danych z aplikacji mobilnej|
|**połączenia**|**MobileAppUriSetting**|Nazwa ustawienia aplikacji, która ma adres URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymagane operacje REST względem Twojej aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, a następnie podaj nazwę ustawienia aplikacji w `connection` właściwości w swojej powiązania danych wejściowych. Adres URL wygląda `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Nazwa ustawienia aplikacji, który ma klucz interfejsu API aplikacji mobilnej. Podaj if klucza interfejsu API możesz [zaimplementować klucza interfejsu API na platformie Node.js zaplecza aplikacji mobilnej](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), lub [zaimplementować klucza interfejsu API na platformie .NET zaplecza aplikacji mobilnej](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, utworzyć ustawienie aplikacji w swojej aplikacji funkcji, który zawiera klucz interfejsu API, a następnie dodaj `apiKey` właściwości w swojej powiązania danych wejściowych o nazwie ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza interfejsu API z klientów aplikacji mobilnej. On tylko należy rozdzielić bezpiecznie klientom po stronie usługi, takie jak usługi Azure Functions. Usługa Azure Functions przechowuje Twoje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, dzięki czemu nie są sprawdzane w Twoim repozytorium kontroli źródła. To chroni poufne informacje.

## <a name="output---usage"></a>Dane wyjściowe — użycie

W funkcji języka C# script, należy użyć parametr wyjściowy o nazwie typu `out object` uzyskać dostęp do danych wyjściowych rekordu. W języku C# w bibliotekach klas `MobileTable` atrybut może być używany z dowolnymi z następujących typów:

* `ICollector<T>` lub `IAsyncCollector<T>`, gdzie `T` jest `JObject` lub dowolny typ z `public string Id` właściwości.
* `out JObject`
* `out T` lub `out T[]`, gdzie `T` jest dowolnego typu z `public string Id` właściwości.

W przypadku funkcji środowiska Node.js użyj `context.bindings.<name>` uzyskać dostęp do danych wyjściowych rekordu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
