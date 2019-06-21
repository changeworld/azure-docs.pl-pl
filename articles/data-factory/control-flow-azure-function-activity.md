---
title: Działanie funkcji platformy Azure w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działanie funkcji platformy Azure służy do uruchamiania funkcji platformy Azure w potoku usługi fabryka danych
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: dfdfb9e38f16d0077175587933b0800b87cc1931
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144127"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktywności funkcji platformy Azure w usłudze Azure Data Factory

Działanie funkcji platformy Azure umożliwia uruchamianie [usługi Azure Functions](../azure-functions/functions-overview.md) w potoku usługi fabryka danych. Aby uruchomić funkcję platformy Azure, musisz utworzyć połączenie połączonej usługi i działanie, które określa funkcji platformy Azure, który chcesz wykonać.

Wprowadzenie minutę 8 i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Usługa połączona — funkcja

Zwracany typ funkcji platformy Azure musi być prawidłowym `JObject`. (Należy pamiętać, że [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) jest *nie* `JObject`.) Dowolny zwracany typ inny niż `JObject` kończy się niepowodzeniem i zgłasza błąd użytkownika *zawartość odpowiedzi nie jest prawidłową jobject zostanie opróżniony*.

| **Property** | **Opis** | **Wymagane** |
| --- | --- | --- |
| — typ   | Właściwość type musi być równa: **AzureFunction** | tak |
| adres url aplikacji funkcji | Adres URL dla aplikacji funkcji platformy Azure. Format jest `https://<accountname>.azurewebsites.net`. Ten adres URL jest wartością w **adresu URL** sekcji podczas przeglądania aplikacji funkcji w witrynie Azure portal  | tak |
| klucz funkcji | Klucz dostępu dla funkcji platformy Azure. Kliknij pozycję **Zarządzaj** sekcji dla odpowiedniej funkcji, a następnie skopiuj albo **funkcyjne** lub **klucz hosta**. Dowiedz się więcej tutaj: [Usługa Azure Functions HTTP wyzwalaczy i powiązań](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | tak |
|   |   |   |

## <a name="azure-function-activity"></a>Działanie funkcji platformy Azure

| **Property**  | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| name  | Nazwa działania w potoku  | String | tak |
| type  | Typ działania jest "AzureFunctionActivity" | String | tak |
| Połączona usługa | Funkcji platformy Azure, połączone usługi dla odpowiedniej aplikacji funkcji platformy Azure  | Odwołanie do połączonej usługi | tak |
| Nazwa funkcji  | Nazwa funkcji w aplikacji funkcji platformy Azure, który wywołuje działanie | String | tak |
| method  | Metoda interfejsu API REST na wywołanie funkcji | Ciąg obsługiwane typy: POBIERZ "PUBLIKUJ", "PUT"   | tak |
| nagłówek  | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i typ żądania: "nagłówki": {"Accept-Language": "en-us", "Content-Type": "application/json"} | Ciąg (lub wyrażenie obiektu resultType ciągu) | Nie |
| Treść  | treść, która jest wysyłany wraz z żądania do metody interfejsu api — funkcja  | Ciąg (lub wyrażenie obiektu resultType ciągu) lub obiekt.   | Wymagane dla metod PUT/POST |
|   |   |   | |

Wyświetlić schemat ładunek żądania w [schematu ładunku żądania](control-flow-web-activity.md#request-payload-schema) sekcji.

## <a name="routing-and-queries"></a>Routing i zapytań

Działanie funkcji platformy Azure obsługuje **routingu**. Na przykład, jeśli funkcji platformy Azure ma punkt końcowy `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, a następnie `functionName` do użycia w ramach działania funkcji platformy Azure jest `<functionName>/<value>`. Możecie tę funkcję, aby zapewnić żądany `functionName` w czasie wykonywania.

Działanie funkcji platformy Azure obsługuje również **zapytania**. Zapytanie musi być dołączane jako część `functionName`. Na przykład, jeśli nazwa funkcji jest `HttpTriggerCSharp` i jest zapytanie, które mają zostać uwzględnione `name=hello`, a następnie można skonstruować `functionName` w ramach działania funkcji platformy Azure jako `HttpTriggerCSharp?name=hello`. Ta funkcja może być sparametryzowany, dzięki czemu można określić wartości w czasie wykonywania.

## <a name="timeout-and-long-running-functions"></a>Limit czasu i długo działających funkcji

Usługa Azure Functions limit czasu po 230 sekundy, niezależnie od tego `functionTimeout` ustawienia zostały skonfigurowane w ustawieniach. Więcej informacji znajduje się w [tym artykule](../azure-functions/functions-versions.md#timeout). Aby obejść ten problem, postępuj zgodnie z wzorca asynchronicznego lub funkcje trwałe. Zaletą funkcje trwałe jest, że oferują one ich własny mechanizm śledzenia stanu, dzięki czemu nie trzeba implementować własne.

Dowiedz się więcej o funkcje trwałe w [w tym artykule](../azure-functions/durable/durable-functions-overview.md). Możesz skonfigurować działanie funkcji platformy Azure do wywołania funkcji trwałe, która zwróci odpowiedź z innego identyfikatora URI, takich jak [w tym przykładzie](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Ponieważ `statusQueryGetUri` zwraca HTTP 202 stan podczas funkcja działa, można sondować stan funkcji za pomocą działania w sieci Web. Po prostu skonfigurować działanie internetowe za pomocą `url` pola `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Po zakończeniu funkcji trwałych danych wyjściowych funkcji będą dane wyjściowe działania w sieci Web.


## <a name="sample"></a>Sample

Można znaleźć przykładowe usługi Data Factory, która używa funkcji platformy Azure można wyodrębnić zawartości pliku tar [tutaj](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat działania w usłudze Data Factory w [potokami i działaniami w usłudze Azure Data Factory](concepts-pipelines-activities.md).
