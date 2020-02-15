---
title: Działanie funkcji platformy Azure w Azure Data Factory
description: Dowiedz się, jak używać działania funkcji platformy Azure do uruchamiania funkcji platformy Azure w potoku Data Factory
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ffb610634399594788afcb9b600ba00c6803dfdd
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207029"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Działanie funkcji platformy Azure w Azure Data Factory

Działanie funkcji platformy Azure umożliwia uruchamianie [Azure Functions](../azure-functions/functions-overview.md) w potoku Data Factory. Aby uruchomić funkcję platformy Azure, należy utworzyć połączenie połączonej usługi i działanie określające funkcję platformy Azure, która ma zostać wykonana.

Aby uzyskać 8-minutowy wprowadzenie i demonstrację tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Połączona usługa funkcji platformy Azure

Zwracany typ funkcji platformy Azure musi być prawidłowym `JObject`. (Należy pamiętać, że [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *nie* jest `JObject`.) Każdy typ zwracany inny niż `JObject` kończy się niepowodzeniem i podnosi *zawartość odpowiedzi na błąd użytkownika nie jest prawidłowym JObject*.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| typ   | Właściwość Type musi mieć wartość: **AzureFunction** | tak |
| adres URL aplikacji funkcji | Adres URL aplikacja funkcji platformy Azure. Format jest `https://<accountname>.azurewebsites.net`. Ten adres URL jest wartością w sekcji **adresu URL** podczas wyświetlania aplikacja funkcji w Azure Portal  | tak |
| klucz funkcji | Klucz dostępu dla funkcji platformy Azure. Kliknij sekcję **Zarządzanie** odpowiednią funkcją i skopiuj **klucz funkcji** lub **klucz hosta**. Dowiedz się więcej tutaj: [Azure Functions wyzwalacze i powiązania HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | tak |
|   |   |   |

## <a name="azure-function-activity"></a>Działanie funkcji platformy Azure

| **Właściwość**  | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| {1&gt;nazwa&lt;1}  | Nazwa działania w potoku  | Ciąg | tak |
| typ  | Typ działania to "AzureFunctionActivity" | Ciąg | tak |
| połączona usługa | Połączona usługa funkcji platformy Azure dla odpowiednich aplikacja funkcji platformy Azure  | Odwołanie do połączonej usługi | tak |
| Nazwa funkcji  | Nazwa funkcji w aplikacja funkcji platformy Azure, którą wywołuje to działanie | Ciąg | tak |
| metoda  | Metoda interfejsu API REST dla wywołania funkcji | Obsługiwane typy ciągów: "GET", "POST", "PUT"   | tak |
| nagłówek  | Nagłówki wysyłane do żądania. Na przykład, aby ustawić język i typ dla żądania: "heads": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"} | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie |
| jednostce  | treść, która jest wysyłana wraz z żądaniem do metody interfejsu API funkcji  | Ciąg (lub wyrażenie z typem ResultType String) lub Object.   | Wymagane dla metod PUT/POST |
|   |   |   | |

Zobacz schemat ładunku żądania w  [schematu ładunku żądania](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Routing i zapytania

Działanie funkcji platformy Azure obsługuje **Routing**. Jeśli na przykład funkcja platformy Azure ma punkt końcowy `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, `functionName` do użycia w działaniu funkcji platformy Azure to `<functionName>/<value>`. Tę funkcję można Sparametryzuj, aby zapewnić żądany `functionName` w czasie wykonywania.

Działanie funkcji platformy Azure obsługuje również **zapytania**. Zapytanie musi zostać dołączone jako część `functionName`. Na przykład, gdy nazwa funkcji jest `HttpTriggerCSharp` i zapytanie, które chcesz dołączyć, jest `name=hello`, można skonstruować `functionName` w działaniu funkcji platformy Azure jako `HttpTriggerCSharp?name=hello`. Ta funkcja może być sparametryzowana, aby można było określić wartość w czasie wykonywania.

## <a name="timeout-and-long-running-functions"></a>Funkcje limitu czasu i długotrwałego działania

Azure Functions limit czasu po 230 sekund niezależnie od ustawień `functionTimeout` skonfigurowanych w ustawieniach. Więcej informacji znajduje się w [tym artykule](../azure-functions/functions-versions.md#timeout). Aby obejść ten problem, postępuj zgodnie ze wzorcem asynchronicznym lub użyj Durable Functions. Korzyść Durable Functions polega na tym, że oferują własne mechanizmy śledzenia stanu, więc nie trzeba implementować własnych.

Dowiedz się więcej na temat Durable Functions w [tym artykule](../azure-functions/durable/durable-functions-overview.md). Można skonfigurować działanie funkcji platformy Azure w celu wywołania funkcji trwałej, która zwróci odpowiedź z innym identyfikatorem URI, na przykład w [tym przykładzie](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Ponieważ `statusQueryGetUri` zwraca stan HTTP 202, podczas gdy funkcja jest uruchomiona, można sondować stan funkcji za pomocą działania sieci Web. Po prostu Skonfiguruj działanie sieci Web za pomocą pola `url` ustawione na `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Po zakończeniu trwałej funkcji dane wyjściowe funkcji będą dane wyjściowe działania sieci Web.


## <a name="sample"></a>Sample

Możesz znaleźć przykład Data Factory, który korzysta z funkcji platformy Azure w celu wyodrębnienia zawartości pliku TAR w [tym miejscu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o działaniach w Data Factory w [potokach i działaniach w programie Azure Data Factory](concepts-pipelines-activities.md).
