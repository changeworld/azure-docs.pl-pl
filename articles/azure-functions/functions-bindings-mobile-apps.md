---
title: Mobile Apps powiązania Azure Functions
description: Dowiedz się, jak używać powiązań usługi Azure Mobile Apps w programie Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 051b8780a102a8a1ec4d3979cc53b686e2823dc8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928591"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps powiązania Azure Functions 

> [!NOTE]
> Powiązania Mobile Apps platformy Azure są dostępne tylko dla Azure Functions 1. x. Nie są one obsługiwane w Azure Functions 2. x i wyższych.

W tym artykule opisano sposób pracy z powiązaniami [Mobile Apps platformy Azure](../app-service-mobile/app-service-mobile-value-prop.md) w programie Azure Functions. Azure Functions obsługuje powiązania wejściowe i wyjściowe dla Mobile Apps.

Powiązania Mobile Apps umożliwiają odczytywanie i aktualizowanie tabel danych w aplikacjach mobilnych.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania Mobile Apps są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) w wersji 1. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Dane wejściowe

Powiązanie danych wejściowych Mobile Apps ładuje rekord z punktu końcowego tabeli mobilnej i przekazuje go do funkcji. W C# programie F# i, wszelkie zmiany wprowadzone do rekordu są automatycznie wysyłane z powrotem do tabeli, gdy funkcja zostanie zakończona pomyślnie.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [Skryptu C# (csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Przykładowy C# skrypt wejściowy

Poniższy przykład przedstawia Mobile Apps dane wejściowe w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i modyfikuje jego właściwość `Text`.

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

Poniższy przykład przedstawia Mobile Apps dane wejściowe w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i modyfikuje jego właściwość `Text`.

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

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Aby uzyskać informacje na temat właściwości atrybutów, które można skonfigurować, zobacz [następującą sekcję konfiguracyjną](#input---configuration).

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `MobileTable` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type**|| Musi być ustawiony na "mobilny"|
| **direction**||Musi być ustawiona na wartość "in"|
| **Nazwa**|| Nazwa parametru wejściowego w sygnaturze funkcji.|
|**tableName** |**TableName**|Nazwa tabeli danych aplikacji mobilnej|
| **id**| **Identyfikator** | Identyfikator rekordu do pobrania. Może być statyczny lub oparty na wyzwalaczu, który wywołuje funkcję. Na przykład jeśli używasz wyzwalacza kolejki dla funkcji, `"id": "{queueTrigger}"` używa wartości ciągu komunikatu w kolejce jako identyfikatora rekordu do pobrania.|
|**połączenia**|**Połączenie**|Nazwa ustawienia aplikacji z adresem URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymaganych operacji REST dla aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, a następnie określ nazwę ustawienia aplikacji we właściwości `connection` w powiązaniu wejściowym. Adres URL wygląda jak `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Nazwa ustawienia aplikacji z kluczem interfejsu API aplikacji mobilnej. Podaj klucz interfejsu API w przypadku [zaimplementowania klucza interfejsu API w aplikacji mobilnej Node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)lub [ZAIMPLEMENTUJ klucz interfejsu API w aplikacji mobilnej platformy .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera klucz interfejsu API, a następnie Dodaj właściwość `apiKey` w powiązaniu wejściowym przy użyciu nazwy ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza interfejsu API klientom aplikacji mobilnej. Powinien być bezpiecznie dystrybuowany tylko do klientów po stronie usług, takich jak Azure Functions. Azure Functions przechowuje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, tak aby nie były one sprawdzane w repozytorium kontroli źródła. Zapewnia to ochronę poufnych informacji.

## <a name="input---usage"></a>Dane wejściowe — użycie

W C# funkcjach, gdy zostanie znaleziony rekord o określonym identyfikatorze, jest on przekazywać do nazwanego parametru [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) . Gdy rekord nie zostanie znaleziony, wartość parametru jest `null`. 

W funkcjach JavaScript rekord jest przesyłany do obiektu `context.bindings.<name>`. Gdy rekord nie zostanie znaleziony, wartość parametru jest `null`. 

W C# programie F# i, wszelkie zmiany wprowadzone w rekordzie wejściowym (parametr wejściowy) są automatycznie wysyłane z powrotem do tabeli, gdy funkcja zostanie zakończona pomyślnie. Nie można zmodyfikować rekordu w funkcjach JavaScript.

## <a name="output"></a>Dane wyjściowe

Użyj powiązania danych wyjściowych Mobile Apps, aby zapisać nowy rekord w tabeli Mobile Apps.  

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) wyzwalaną przez komunikat w kolejce i tworzy rekord w tabeli aplikacji mobilnej.

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

Poniższy przykład przedstawia Mobile Apps powiązanie danych wyjściowych w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat w kolejce i tworzy nowy rekord z ustaloną wartością właściwości `Text`.

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

Poniższy przykład przedstawia Mobile Apps powiązanie danych wyjściowych w pliku *Function. JSON* i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat w kolejce i tworzy nowy rekord z ustaloną wartością właściwości `Text`.

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

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj atrybutu [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Aby uzyskać informacje na temat właściwości atrybutów, które można skonfigurować, zobacz [wyjście-konfiguracja](#output---configuration). Oto `MobileTable` przykład atrybutu w podpisie metody:

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
| **type**|| Musi być ustawiony na "mobilny"|
| **direction**||Musi być ustawiona na wartość "out"|
| **Nazwa**|| Nazwa parametru wyjściowego w sygnaturze funkcji.|
|**tableName** |**TableName**|Nazwa tabeli danych aplikacji mobilnej|
|**połączenia**|**MobileAppUriSetting**|Nazwa ustawienia aplikacji z adresem URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymaganych operacji REST dla aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, a następnie określ nazwę ustawienia aplikacji we właściwości `connection` w powiązaniu wejściowym. Adres URL wygląda jak `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Nazwa ustawienia aplikacji z kluczem interfejsu API aplikacji mobilnej. Podaj klucz interfejsu API w przypadku [zaimplementowania klucza interfejsu API w zapleczu aplikacji mobilnej Node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)lub [ZAIMPLEMENTUJ klucz interfejsu API w zapleczu aplikacji mobilnej platformy .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera klucz interfejsu API, a następnie Dodaj właściwość `apiKey` w powiązaniu wejściowym przy użyciu nazwy ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza interfejsu API klientom aplikacji mobilnej. Powinien być bezpiecznie dystrybuowany tylko do klientów po stronie usług, takich jak Azure Functions. Azure Functions przechowuje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, tak aby nie były one sprawdzane w repozytorium kontroli źródła. Zapewnia to ochronę poufnych informacji.

## <a name="output---usage"></a>Dane wyjściowe — użycie

W C# funkcjach skryptów należy użyć nazwanego parametru wyjściowego typu `out object`, aby uzyskać dostęp do rekordu wyjściowego. W C# bibliotekach klas atrybut `MobileTable` może być używany z dowolnym z następujących typów:

* `ICollector<T>` lub `IAsyncCollector<T>`, gdzie `T` jest `JObject` lub dowolnego typu z właściwością `public string Id`.
* `out JObject`
* `out T` lub `out T[]`, gdzie `T` jest dowolnym typem z właściwością `public string Id`.

W funkcjach Node. js Użyj `context.bindings.<name>`, aby uzyskać dostęp do rekordu wyjściowego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
