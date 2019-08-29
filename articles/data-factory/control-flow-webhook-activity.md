---
title: Działanie elementu webhook w Azure Data Factory | Microsoft Docs
description: Działanie elementu webhook nie kontynuuje wykonywania potoku do momentu zweryfikowania dołączonego zestawu danych z określonymi kryteriami, które użytkownik określi.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: c11fb800dba06ab5566647489f020f727860a7ff
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142405"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Działanie elementu webhook w Azure Data Factory
Możesz użyć działania elementu webhook, aby kontrolować wykonywanie potoków za pomocą kodu niestandardowego. Korzystając z działania elementu webhook, klienci mogą wywołać punkt końcowy i przekazać adres URL wywołania zwrotnego. Uruchomienie potoku oczekuje na wywołanie wywołania zwrotnego przed przejściem do następnego działania.

## <a name="syntax"></a>Składnia

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Właściwości typu



Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania elementu webhook | String | Tak |
type | Musi być ustawiona na **element webhook**. | String | Tak |
— metoda | Metoda interfejsu API REST dla docelowego punktu końcowego. | Parametry. Obsługiwane typy: POUBOJOWEGO | Tak |
url | Docelowy punkt końcowy i ścieżka | Ciąg (lub wyrażenie z typem ResultType ciągu). | Tak |
nagłówka | Nagłówki wysyłane do żądania. Na przykład, aby ustawić język i typ dla żądania: "heads": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"}. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Tak, nagłówek Content-Type jest wymagany. "headers":{ "Content-Type":"application/json"} |
treść | Reprezentuje ładunek, który jest wysyłany do punktu końcowego. | Treść przeniesiona z powrotem do identyfikatora URI wywołania zwrotnego powinna być prawidłowym kodem JSON. Zobacz schemat ładunku żądania w sekcji [schematu ładunku żądania](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Tak |
uwierzytelnianie | Metoda uwierzytelniania używana do wywoływania punktu końcowego. Obsługiwane typy to "podstawowa" lub "ClientCertificate". Aby uzyskać więcej informacji, zobacz sekcję [uwierzytelnianie](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) . Jeśli uwierzytelnianie nie jest wymagane, Wyklucz tę właściwość. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie |
limit czasu | Jak długo działanie będzie oczekiwać na wywołanie &#39;callBackUri.&#39; Jak długo działanie będzie oczekiwać na wywołanie elementu "callBackUri". Wartość domyślna to 10mins ("00:10:00"). Format to TimeSpan, np. hh: mm: SS | String | Nie |

## <a name="additional-notes"></a>Dodatkowe uwagi

Azure Data Factory przekaże dodatkową właściwość "callBackUri" w treści do punktu końcowego adresu URL i będzie oczekiwać, że ten identyfikator URI będzie wywoływany przed określoną wartością limitu czasu. Jeśli identyfikator URI nie zostanie wywołany, działanie zakończy się niepowodzeniem ze stanem "TimedOut".

Działanie elementu webhook nie powiedzie się tylko wtedy, gdy wywołanie niestandardowego punktu końcowego zakończy się niepowodzeniem. Dowolny komunikat o błędzie może zostać dodany do treści wywołania zwrotnego i użyty w kolejnym działaniu.

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
