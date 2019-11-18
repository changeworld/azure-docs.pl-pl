---
title: Działanie elementu webhook w Azure Data Factory
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
ms.openlocfilehash: b2f7c35e6ddb3c6ed0a3032d7ea6d4c53043c17b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122910"
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
name | Nazwa działania elementu webhook | Ciąg | Yes |
type | Musi być ustawiona na **element webhook**. | Ciąg | Yes |
method | Metoda interfejsu API REST dla docelowego punktu końcowego. | parametry. Obsługiwane typy: "POST" | Yes |
url | Docelowy punkt końcowy i ścieżka | Ciąg (lub wyrażenie z typem ResultType ciągu). | Yes |
nagłówka | Nagłówki wysyłane do żądania. Na przykład, aby ustawić język i typ dla żądania: "heads": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"}. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Tak, nagłówek Content-Type jest wymagany. "headers":{ "Content-Type":"application/json"} |
jednostce | Reprezentuje ładunek, który jest wysyłany do punktu końcowego. | Prawidłowy kod JSON (lub wyrażenie z typem ResultType JSON). Zobacz schemat ładunku żądania w sekcji [schematu ładunku żądania](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Yes |
uwierzytelnianie | Metoda uwierzytelniania używana do wywoływania punktu końcowego. Obsługiwane typy to "podstawowa" lub "ClientCertificate". Aby uzyskać więcej informacji, zobacz sekcję [uwierzytelnianie](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) . Jeśli uwierzytelnianie nie jest wymagane, Wyklucz tę właściwość. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie |
timeout | Jak długo działanie będzie oczekiwać na wywołanie &#39;callBackUri.&#39; Jak długo działanie będzie oczekiwać na wywołanie elementu "callBackUri". Wartość domyślna to 10mins ("00:10:00"). Format to TimeSpan, np. hh: mm: SS | Ciąg | Nie |
Stan raportu dla wywołania zwrotnego | Zezwala użytkownikowi na raportowanie stanu niepowodzenia aktywności elementu webhook, co oznacza działanie jako zakończone niepowodzeniem | Wartość logiczna | Nie |

## <a name="additional-notes"></a>Uwagi dodatkowe

Azure Data Factory przekaże dodatkową właściwość "callBackUri" w treści do punktu końcowego adresu URL i będzie oczekiwać, że ten identyfikator URI będzie wywoływany przed określoną wartością limitu czasu. Jeśli identyfikator URI nie zostanie wywołany, działanie zakończy się niepowodzeniem ze stanem "TimedOut".

Działanie elementu webhook kończy się niepowodzeniem, gdy wywołanie do niestandardowego punktu końcowego zakończy się niepowodzeniem. Dowolny komunikat o błędzie może zostać dodany do treści wywołania zwrotnego i użyty w kolejnym działaniu.

Treść przeniesiona z powrotem do identyfikatora URI wywołania zwrotnego powinna być prawidłowym kodem JSON. Należy ustawić nagłówek Content-Type na `application/json`.

W przypadku korzystania z opcji "Zgłoś stan na wywołanie zwrotne" należy dodać następujący fragment kodu do treści podczas wykonywania wywołania zwrotnego:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Następne kroki

Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
