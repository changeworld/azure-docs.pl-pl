---
title: Działania elementu Webhook w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Działania elementu Webhook nie kontynuacja wykonywania potoku sprawdza poprawność dołączonych zestaw danych o określone kryteria, określonego przez użytkownika.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 0b8b892f02e54c3b0ddb155af97ce63ff115bb1f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523001"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Działania elementu Webhook w usłudze Azure Data Factory
Działanie punktu zaczepienia sieci web umożliwia kontrolować wykonywanie potoków poprzez kod niestandardowy. Przy użyciu działania elementu webhook, klienci mogą wywołać punkt końcowy i przekazać adres URL wywołania zwrotnego. Uruchomienie potoku czeka, aż wywołanie zwrotne do wywołania przed przejściem do następnego działania.

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
name | Nazwa działania punktu zaczepienia sieci web | String | Yes |
type | Musi być równa **elementu WebHook**. | String | Yes |
method | Metoda interfejsu API REST dla docelowego punktu końcowego. | ciąg. Obsługiwane typy: 'POST' | Yes |
url | Docelowy punkt końcowy i ścieżki | Ciąg lub wyrażenie obiektu resultType ciągu. | Yes |
Nagłówki | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i typ żądania: "nagłówki": {"Accept-Language": "en-us", "Content-Type": "application/json"}. | Ciąg (lub wyrażenie obiektu resultType ciągu) | Tak, wymagany jest nagłówek Content-type. "headers":{ "Content-Type":"application/json"} |
treść | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | Treść przekazywany z powrotem do wywołania zwrotne URI powinien być prawidłowym kodem JSON. Wyświetlić schemat ładunek żądania w [schematu ładunku żądania](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) sekcji. | Yes |
uwierzytelnianie | Metoda uwierzytelniania do wywoływania punktu końcowego. Obsługiwane typy to "Basic" lub "ClientCertificate." Aby uzyskać więcej informacji, zobacz [uwierzytelniania](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) sekcji. Jeśli uwierzytelnianie nie jest wymagane, należy wykluczyć tej właściwości. | Ciąg (lub wyrażenie obiektu resultType ciągu) | Nie |
timeout | Jak długo będzie czekać działania &#39;element callbackuri&#39; do wywołania. Jak długo działanie będzie czekać "element callbackuri" do wywołania. Wartość domyślna to 10mins ("00: 10:00"). Format jest przedział czasu, czyli d.hh:mm:ss | String | Nie |

## <a name="additional-notes"></a>Uwagi dodatkowe

Usługa Azure Data Factory zostanie przekazany dodatkowa właściwość "element callbackuri" w treści do punktu końcowego adresu url i będzie oczekiwać ten identyfikator uri do wywołania przed określona wartość limitu czasu. Identyfikator uri nie zostanie wywołany, działanie zakończy się niepowodzeniem ze stanem "Upłynął limit czasu".

Działania w sieci web hook kończy się niepowodzeniem, tylko jeśli wywołanie do niestandardowego punktu końcowego nie powiedzie się. Wszelkie komunikaty o błędach mogą być dodawane do treści wywołania zwrotnego lub używane w kolejnych działań.

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
