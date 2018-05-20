---
title: Aplikacje mobilne powiązania dla usługi Azure Functions
description: Zrozumienie, jak używać usługi Azure Mobile Apps powiązania w usługi Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 1129bd36b1f1f413e5fd40da16a48f5aff1078bc
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Aplikacje mobilne powiązania dla usługi Azure Functions 

W tym artykule opisano sposób pracy z [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) powiązania usługi Azure Functions. Azure Functions obsługuje wejściowa i wyjściowa powiązania dla aplikacji mobilnych.

Powiązania Mobile Apps pozwalają na odczytywanie i aktualizowanie tabel danych w aplikacjach mobilnych.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Pakiety

Aplikacje mobilne powiązania znajdują się w [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) pakietu NuGet. Kod źródłowy dla pakietu jest w [azure-zadań webjob sdk rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/) repozytorium GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="input"></a>Dane wejściowe

Powiązania wejściowego Mobile Apps ładuje rekord z punktu końcowego przenośnych tabeli i przekazuje ją do funkcji. W języku C# i F # funkcjach wszelkie zmiany wprowadzone do rekordu są automatycznie wysyłane powrót do tabeli, gdy funkcja kończy się pomyślnie.

## <a name="input---example"></a>Dane wejściowe — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C# (csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Dane wejściowe — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono powiązania wejściowego Mobile Apps w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja jest wyzwalany przez komunikat z kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i zmienia jego `Text` właściwości.

W tym miejscu jest powiązanie danych *function.json* pliku:

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
],
"disabled": false
}
```
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto kod skryptu C#:

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

### <a name="input---javascript"></a>Dane wejściowe - JavaScript

W poniższym przykładzie przedstawiono powiązania wejściowego Mobile Apps w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja jest wyzwalany przez komunikat z kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i zmienia jego `Text` właściwości.

W tym miejscu jest powiązanie danych *function.json* pliku:

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
],
"disabled": false
}
```
[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atrybutu.

Informacje o właściwości atrybutów, które można skonfigurować, zobacz [następującą sekcję konfiguracji](#input---configuration).

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `MobileTable` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type**|| Musi być ustawiona na "mobileTable"|
| **direction**||Musi być ustawiona na "w"|
| **Nazwa**|| Nazwa parametru wejściowego w sygnaturze funkcji.|
|**TableName** |**TableName**|Nazwa tabeli danych aplikacji mobilnej|
| **id**| **Identyfikator** | Identyfikator rekordu do pobrania. Może być statyczne lub w oparciu o wyzwalacz, który wywołuje funkcję. Na przykład, jeśli używasz wyzwalacz kolejki dla funkcji, następnie `"id": "{queueTrigger}"` używa wartości ciągu komunikatu w kolejce jako identyfikator rekordu do pobrania.|
|**Połączenia**|**Połączenia**|Nazwa ustawienia aplikacji, które ma adres URL aplikacji mobilnej. Funkcja używa tego adresu URL do skonstruowania wymagane operacje REST względem aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji zawierający adres URL aplikacji mobilnej, a następnie określ nazwę ustawienia aplikacji w `connection` właściwości w Twojej powiązania wejściowego. Adres URL wygląda `http://<appname>.azurewebsites.net`.
|**apiKey**|**apiKey**|Nazwa ustawienia aplikacji, który ma klucz interfejsu API aplikacji mobilnej. Jeśli klucza interfejsu API zapewniają możesz [zaimplementować klucz interfejsu API w aplikacji mobilnej Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), lub [zaimplementować klucz interfejsu API w aplikacji mobilnej .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, utworzyć ustawienie aplikacji w Twojej aplikacji funkcji, który zawiera klucz interfejsu API, a następnie dodaj `apiKey` właściwości w Twojej powiązania wejściowego o nazwie ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie można udostępniać klucz interfejsu API klientów aplikacji mobilnej. Go tylko należy rozdzielić bezpiecznie klientom po stronie serwera, takie jak usługi Azure Functions. Azure Functions przechowuje informacje dotyczące połączenia, a klucze interfejsu API zgodnie z ustawieniami aplikacji, dzięki czemu nie są sprawdzane w Twoim repozytorium kontroli źródła. To chroni poufne informacje.

## <a name="input---usage"></a>Dane wejściowe — użycie

W języku C# funkcje, po znalezieniu rekordu o określonym identyfikatorze przekazaniem do nazwanego [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parametru. Jeśli rekord nie zostanie znaleziony, wartość parametru jest `null`. 

W funkcji JavaScript, rekord zostanie przekazany do `context.bindings.<name>` obiektu. Jeśli rekord nie zostanie znaleziony, wartość parametru jest `null`. 

W funkcji języka C# i F #, zmiany wprowadzone w danych wejściowych rekordu (parametr wejściowy) są automatycznie wysyłane powrót do tabeli, gdy funkcja kończy się pomyślnie. Nie można zmodyfikować rekordu w funkcji JavaScript.

## <a name="output"></a>Dane wyjściowe

Za pomocą raportu Mobile Apps powiązanie można zapisać nowego rekordu do tabeli Mobile Apps.  

## <a name="output---example"></a>OUTPUT — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) jest wyzwalany przez komunikatu w kolejce i tworzy rekord w tabeli aplikacji mobilnej.

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

W poniższym przykładzie przedstawiono dane wyjściowe Mobile Apps powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja jest wyzwalany przez komunikatu w kolejce i tworzy nowy rekord z wartością ustalony `Text` właściwości.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod skryptu C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie przedstawiono dane wyjściowe Mobile Apps powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja jest wyzwalany przez komunikatu w kolejce i tworzy nowy rekord z wartością ustalony `Text` właściwości.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atrybutu.

Informacje o właściwości atrybutów, które można skonfigurować, zobacz [wyjście - konfiguracji](#output---configuration). Oto `MobileTable` przykład atrybutu w podpisie metody:

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

Pełny przykład, zobacz [dane wyjściowe — przykład C#](#output---c-example).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `MobileTable` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **type**|| Musi być ustawiona na "mobileTable"|
| **direction**||Musi być ustawiona na "out"|
| **Nazwa**|| Nazwa parametru wyjściowego w funkcji podpisu.|
|**TableName** |**TableName**|Nazwa tabeli danych aplikacji mobilnej|
|**Połączenia**|**MobileAppUriSetting**|Nazwa ustawienia aplikacji, które ma adres URL aplikacji mobilnej. Funkcja używa tego adresu URL do skonstruowania wymagane operacje REST względem aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji zawierający adres URL aplikacji mobilnej, a następnie określ nazwę ustawienia aplikacji w `connection` właściwości w Twojej powiązania wejściowego. Adres URL wygląda `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Nazwa ustawienia aplikacji, który ma klucz interfejsu API aplikacji mobilnej. Podaj if klucza interfejsu API można [implementować klucz interfejsu API zaplecza aplikacji mobilnej Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), lub [implementować klucz interfejsu API zaplecza aplikacji mobilnej .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, utworzyć ustawienie aplikacji w Twojej aplikacji funkcji, który zawiera klucz interfejsu API, a następnie dodaj `apiKey` właściwości w Twojej powiązania wejściowego o nazwie ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie można udostępniać klucz interfejsu API klientów aplikacji mobilnej. Go tylko należy rozdzielić bezpiecznie klientom po stronie serwera, takie jak usługi Azure Functions. Azure Functions przechowuje informacje dotyczące połączenia, a klucze interfejsu API zgodnie z ustawieniami aplikacji, dzięki czemu nie są sprawdzane w Twoim repozytorium kontroli źródła. To chroni poufne informacje.

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# skrypt funkcji, należy użyć parametru wyjściowego o nazwie typu `out object` uzyskać dostęp do rekordu danych wyjściowych. W języku C# biblioteki klas `MobileTable` atrybut może być używany z dowolnymi z następujących typów:

* `ICollector<T>` lub `IAsyncCollector<T>`, gdzie `T` jest `JObject` lub dowolny typ z `public string Id` właściwości.
* `out JObject`
* `out T` lub `out T[]`, gdzie `T` jest dowolnego typu `public string Id` właściwości.

W przypadku funkcji Node.js użyj `context.bindings.<name>` uzyskać dostęp do rekordu danych wyjściowych.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
