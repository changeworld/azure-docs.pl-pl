---
title: Działanie funkcji platformy Azure w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działanie funkcji platformy Azure służy do uruchamiania funkcji platformy Azure w potoku usługi fabryka danych
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: ee99733440d74424f98a2ed16de83c88bae53ff1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321793"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktywności funkcji platformy Azure w usłudze Azure Data Factory

Działanie funkcji platformy Azure umożliwia uruchamianie [usługi Azure Functions](../azure-functions/functions-overview.md) w potoku usługi fabryka danych. Aby uruchomić funkcję platformy Azure, musisz utworzyć połączenie połączonej usługi i działanie, które określa funkcji platformy Azure, który chcesz wykonać.

Wprowadzenie minutę 8 i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Usługa połączona — funkcja

Zwracany typ funkcji platformy Azure musi być prawidłowym `JObject`. (Należy pamiętać, że [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) jest *nie* `JObject`.) Dowolny zwracany typ inny niż `JObject` kończy się niepowodzeniem i zgłasza błąd ogólny użytkownik *błąd podczas wywoływania punktu końcowego*.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| type   | Właściwość type musi być równa: **AzureFunction** | tak |
| adres url aplikacji funkcji | Adres URL dla aplikacji funkcji platformy Azure. Format jest `https://<accountname>.azurewebsites.net`. Ten adres URL jest wartością w **adresu URL** sekcji podczas przeglądania aplikacji funkcji w witrynie Azure portal  | tak |
| klucz funkcji | Klucz dostępu dla funkcji platformy Azure. Kliknij pozycję **Zarządzaj** sekcji dla odpowiedniej funkcji, a następnie skopiuj albo **funkcyjne** lub **klucz hosta**. Dowiedz się więcej tutaj: [Usługa Azure Functions HTTP wyzwalaczy i powiązań](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | tak |
|   |   |   |

## <a name="azure-function-activity"></a>Działanie funkcji platformy Azure

| **Właściwość**  | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| name  | Nazwa działania w potoku  | Ciąg | tak |
| type  | Typ działania jest "AzureFunctionActivity" | Ciąg | tak |
| Połączona usługa | Funkcji platformy Azure, połączone usługi dla odpowiedniej aplikacji funkcji platformy Azure  | Odwołanie do połączonej usługi | tak |
| Nazwa funkcji  | Nazwa funkcji w aplikacji funkcji platformy Azure, który wywołuje działanie | Ciąg | tak |
| method  | Metoda interfejsu API REST na wywołanie funkcji | Ciąg obsługiwane typy: POBIERZ "PUBLIKUJ", "PUT"   | tak |
| nagłówek  | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i typ żądania: "nagłówki": {"Accept-Language": "en-us", "Content-Type": "application/json"} | Ciąg (lub wyrażenie obiektu resultType ciągu) | Nie |
| treść  | treść, która jest wysyłany wraz z żądania do metody interfejsu api — funkcja  | Ciąg (lub wyrażenie obiektu resultType ciągu) lub obiekt.   | Wymagane dla metod PUT/POST |
|   |   |   | |

Wyświetlić schemat ładunek żądania w [schematu ładunku żądania](control-flow-web-activity.md#request-payload-schema) sekcji.

## <a name="more-info"></a>Więcej informacji

Działanie funkcji platformy Azure obsługuje **routingu**. Na przykład, jeśli aplikacja używa następujących routing - `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` — a następnie `functionName` jest `functionName/{value}`, który można zdefiniować parametry zapewnienie żądaną `functionName` w czasie wykonywania.

Działanie funkcji platformy Azure obsługuje również **zapytania**. Zapytanie ma być częścią `functionName` — na przykład `HttpTriggerCSharp2?name=hello` — gdzie `function name` jest `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat działania w usłudze Data Factory w [potokami i działaniami w usłudze Azure Data Factory](concepts-pipelines-activities.md).
