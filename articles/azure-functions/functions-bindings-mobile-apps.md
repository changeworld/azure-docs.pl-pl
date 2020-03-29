---
title: Powiązania aplikacji mobilnych dla funkcji platformy Azure
description: Dowiedz się, jak używać powiązań usługi Azure Mobile Apps w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 952a94797e01a3931fdd151461250af0c2590c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76120545"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Powiązania aplikacji mobilnych dla funkcji platformy Azure 

> [!NOTE]
> Powiązania usługi Azure Mobile Apps są dostępne tylko dla usługi Azure Functions 1.x. Nie są one obsługiwane w usłudze Azure Functions 2.x lub nowszej.

W tym artykule wyjaśniono, jak pracować z powiązaniami [usługi Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązania danych wejściowych i wyjściowych dla aplikacji mobilnych.

Powiązania aplikacji mobilnych umożliwiają odczytywanie i aktualizowanie tabel danych w aplikacjach mobilnych.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety - Funkcje 1.x

Powiązania aplikacji mobilnych są dostępne w pakiecie [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet w wersji 1.x. Kod źródłowy pakietu znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Dane wejściowe

Powiązanie wejściowe aplikacji mobilnych ładuje rekord z punktu końcowego tabeli mobilnej i przekazuje go do funkcji. W językach C# i F# funkcje wszelkie zmiany wprowadzone do rekordu są automatycznie wysyłane z powrotem do tabeli, gdy funkcja kończy się pomyślnie.

## <a name="input---example"></a>Wejście - przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Dane wejściowe — przykład skryptu C#

W poniższym przykładzie pokazano powiązanie wejściowe aplikacji mobilnych w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i `Text` modyfikuje jego właściwość.

Oto dane powiązania w pliku *function.json:*

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
W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

Oto kod skryptu języka C#:

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

### <a name="input---javascript"></a>Wejście — JavaScript

W poniższym przykładzie pokazano powiązanie wejściowe aplikacji mobilnych w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i `Text` modyfikuje jego właściwość.

Oto dane powiązania w pliku *function.json:*

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
W sekcji [konfiguracji](#input---configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Dane wejściowe - atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Aby uzyskać informacje o właściwościach atrybutów, które można skonfigurować, zobacz [następującą sekcję konfiguracji](#input---configuration).

## <a name="input---configuration"></a>Wejście - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `MobileTable` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Typu**| Nie dotyczy | Musi być ustawiona na "mobileTable"|
| **Kierunku**| Nie dotyczy |Musi być ustawiona na "in"|
| **Nazwa**| Nie dotyczy | Nazwa parametru wejściowego w podpisie funkcji.|
|**tableName** |**Tablename**|Nazwa tabeli danych aplikacji mobilnej|
| **Identyfikator**| **Identyfikator** | Identyfikator rekordu do pobrania. Może być statyczne lub na podstawie wyzwalacza, który wywołuje funkcję. Na przykład jeśli używasz wyzwalacza kolejki `"id": "{queueTrigger}"` dla funkcji, a następnie używa wartości ciągu wiadomości kolejki jako identyfikator rekordu do pobrania.|
|**Połączenia**|**Połączenia**|Nazwa ustawienia aplikacji zawierającego adres URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymaganych operacji REST dla aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, `connection` a następnie określ nazwę ustawienia aplikacji w właściwości w powiązaniu wejściowym. Adres URL `http://<appname>.azurewebsites.net`wygląda jak .
|**apiKey (klawisz apiKey)**|**Klawisz ApiKey**|Nazwa ustawienia aplikacji z kluczem INTERFEJSU API aplikacji mobilnej. Podaj klucz interfejsu API, jeśli [zaimplementujesz klucz interfejsu API w aplikacji mobilnej Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)lub [zaimplementuj klucz interfejsu API w aplikacji mobilnej .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, należy utworzyć ustawienie aplikacji w aplikacji funkcji, `apiKey` która zawiera klucz interfejsu API, a następnie dodać właściwość w powiązaniu wejściowym o nazwie ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza INTERFEJSU API klientom aplikacji mobilnej. Powinien być bezpiecznie dystrybuowany tylko do klientów po stronie usługi, takich jak usługi Azure Functions. Usługa Azure Functions przechowuje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, dzięki czemu nie są zaewidencjonowane w repozytorium kontroli źródła. Chroni to twoje poufne informacje.

## <a name="input---usage"></a>Wejście - użycie

W językach C# po znalezieniu rekordu o określonym identyfikatorze jest przekazywany do nazwanego parametru [JObject.](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) Gdy rekord nie zostanie znaleziony, `null`wartość parametru jest . 

W funkcjach JavaScript rekord jest `context.bindings.<name>` przekazywany do obiektu. Gdy rekord nie zostanie znaleziony, `null`wartość parametru jest . 

W językach C# i F# funkcje wszelkie zmiany wprowadzone do rekordu wejściowego (parametr wejściowy) są automatycznie wysyłane z powrotem do tabeli, gdy funkcja kończy się pomyślnie. Nie można zmodyfikować rekordu w funkcjach JavaScript.

## <a name="output"></a>Dane wyjściowe

Użyj powiązania danych wyjściowych aplikacji mobilnych, aby zapisać nowy rekord w tabeli Aplikacje mobilne.  

## <a name="output---example"></a>Wyjście - przykład

Zobacz przykład specyficzny dla języka:

* [C #](#output---c-example)
* [Skrypt języka C# (csx)](#output---c-script-example)
* [Javascript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład języka C#

W poniższym przykładzie przedstawiono [funkcję Języka C#,](functions-dotnet-class-library.md) która jest wyzwalana przez komunikat kolejki i tworzy rekord w tabeli aplikacji mobilnej.

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

### <a name="output---c-script-example"></a>Dane wyjściowe — przykład skryptu C#

W poniższym przykładzie pokazano powiązanie danych wyjściowych aplikacji mobilnych w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki i tworzy nowy rekord z `Text` zakodowane wartości właściwości.

Oto dane powiązania w pliku *function.json:*

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

W sekcji [konfiguracji](#output---configuration) opisano te właściwości.

Oto kod skryptu języka C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład języka JavaScript

W poniższym przykładzie pokazano powiązanie wyjściowe aplikacji mobilnych w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki i tworzy nowy rekord z `Text` zakodowane wartości właściwości.

Oto dane powiązania w pliku *function.json:*

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

W sekcji [konfiguracji](#output---configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Dane wyjściowe - atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Aby uzyskać informacje o właściwościach atrybutów, które można skonfigurować, zobacz [Dane wyjściowe — konfiguracja](#output---configuration). Oto przykład `MobileTable` atrybutu w podpisie metody:

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

Aby uzyskać pełny przykład, zobacz [Output — Przykład C#.](#output---c-example)

## <a name="output---configuration"></a>Wyjście - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `MobileTable` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Typu**| Nie dotyczy | Musi być ustawiona na "mobileTable"|
| **Kierunku**| Nie dotyczy |Musi być ustawiona na "out"|
| **Nazwa**| Nie dotyczy | Nazwa parametru wyjściowego w podpisie funkcji.|
|**tableName** |**Tablename**|Nazwa tabeli danych aplikacji mobilnej|
|**Połączenia**|**MobileAppUriSetting**|Nazwa ustawienia aplikacji zawierającego adres URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymaganych operacji REST dla aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, `connection` a następnie określ nazwę ustawienia aplikacji w właściwości w powiązaniu wejściowym. Adres URL `http://<appname>.azurewebsites.net`wygląda jak .
|**apiKey (klawisz apiKey)**|**Interfejs ApiKeySetting**|Nazwa ustawienia aplikacji z kluczem INTERFEJSU API aplikacji mobilnej. Podaj klucz interfejsu API, jeśli [zaimplementujesz klucz interfejsu API w wewnętrznej cemie aplikacji mobilnej Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)lub [zaimplementuj klucz interfejsu API w wewnętrznej cemie aplikacji mobilnej .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, należy utworzyć ustawienie aplikacji w aplikacji funkcji, `apiKey` która zawiera klucz interfejsu API, a następnie dodać właściwość w powiązaniu wejściowym o nazwie ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza INTERFEJSU API klientom aplikacji mobilnej. Powinien być bezpiecznie dystrybuowany tylko do klientów po stronie usługi, takich jak usługi Azure Functions. Usługa Azure Functions przechowuje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, dzięki czemu nie są zaewidencjonowane w repozytorium kontroli źródła. Chroni to twoje poufne informacje.

## <a name="output---usage"></a>Wyjście - użycie

W c# funkcje skryptu, użyj `out object` nazwanego parametru wyjściowego typu, aby uzyskać dostęp do rekordu wyjściowego. W bibliotekach klas języka `MobileTable` C# atrybut może być używany z dowolnym z następujących typów:

* `ICollector<T>`lub `IAsyncCollector<T>`, `T` gdzie `JObject` jest jeden lub `public string Id` dowolny typ z właściwością.
* `out JObject`
* `out T`lub `out T[]`, `T` gdzie jest `public string Id` dowolny Typ z właściwością.

W funkcji Node.js, służy `context.bindings.<name>` do uzyskiwania dostępu do rekordu wyjściowego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)
