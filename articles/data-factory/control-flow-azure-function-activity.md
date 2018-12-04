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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: douglasl
ms.openlocfilehash: ef93c62a2e2084a43eeda578c889a568d04db4f1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856416"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktywności funkcji platformy Azure w usłudze Azure Data Factory

Działanie funkcji platformy Azure umożliwia uruchamianie [usługi Azure Functions](../azure-functions/functions-overview.md) w potoku usługi fabryka danych. Aby uruchomić funkcję platformy Azure, musisz utworzyć połączenie połączonej usługi i działanie, które określa funkcji platformy Azure, który chcesz wykonać.

## <a name="azure-function-linked-service"></a>Usługa połączona — funkcja

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| type   | Właściwość type musi być równa: **AzureFunction** | tak |
| adres url aplikacji funkcji | Adres URL dla aplikacji funkcji platformy Azure. Format jest `https://<accountname>.azurewebsites.net`. Ten adres URL jest wartością w **adresu URL** sekcji podczas przeglądania aplikacji funkcji w witrynie Azure portal  | tak |
| klucz funkcji | Klucz dostępu dla funkcji platformy Azure. Kliknij pozycję **Zarządzaj** sekcji dla odpowiedniej funkcji, a następnie skopiuj albo **funkcyjne** lub **klucz hosta**. Dowiedz się więcej tutaj: [Azure funkcje HTTP, wyzwalaczami i powiązaniami](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | tak |
|   |   |   |

## <a name="azure-function-activity"></a>Działanie funkcji platformy Azure

| **Właściwość**  | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| name  | Nazwa działania w potoku  | Ciąg | tak |
| type  | Typ działania jest "AzureFunctionActivity" | Ciąg | tak |
| Połączona usługa | Funkcji platformy Azure, połączone usługi dla odpowiedniej aplikacji funkcji platformy Azure  | Odwołanie do połączonej usługi | tak |
| Nazwa funkcji  | Nazwa funkcji w aplikacji funkcji platformy Azure, który wywołuje działanie | Ciąg | tak |
| method  | Metoda interfejsu API REST na wywołanie funkcji | Ciąg obsługiwane typy: "GET", "POST", "PUT"   | tak |
| nagłówek  | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i typ żądania: "nagłówki": {"Accept-Language": "en-us", "Content-Type": "application/json"} | Ciąg (lub wyrażenie obiektu resultType ciągu) | Nie |
| Treść  | treść, która jest wysyłany wraz z żądania do metody interfejsu api — funkcja  | Ciąg lub wyrażenie obiektu resultType ciągu.   | Wymagane dla metod PUT/POST |
|   |   |   | |

Wyświetlić schemat ładunek żądania w [schematu ładunku żądania](control-flow-web-activity.md#request-payload-schema) sekcji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat działania w usłudze Data Factory w [potokami i działaniami w usłudze Azure Data Factory](concepts-pipelines-activities.md).