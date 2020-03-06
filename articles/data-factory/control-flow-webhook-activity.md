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
ms.openlocfilehash: 8c52bb21276071581a83fb3ee6a3a4a31ba0bb4a
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78400006"
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



Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania elementu webhook | Ciąg | Yes |
type | Musi być ustawiona na **element webhook**. | Ciąg | Yes |
method | Metoda interfejsu API REST dla docelowego punktu końcowego. | Ciąg. Obsługiwane typy: "POST" | Yes |
url | Docelowy punkt końcowy i ścieżka | Ciąg (lub wyrażenie z typem ResultType ciągu). | Yes |
nagłówki | Nagłówki wysyłane do żądania. Na przykład, aby ustawić język i typ dla żądania: "heads": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"}. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Tak, nagłówek Content-Type jest wymagany. "headers":{ "Content-Type":"application/json"} |
jednostce | Reprezentuje ładunek, który jest wysyłany do punktu końcowego. | Prawidłowy kod JSON (lub wyrażenie z typem ResultType JSON). Zobacz schemat ładunku żądania w sekcji [schematu ładunku żądania](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Yes |
uwierzytelnianie | Metoda uwierzytelniania używana do wywoływania punktu końcowego. Obsługiwane typy to "podstawowa" lub "ClientCertificate". Aby uzyskać więcej informacji, zobacz sekcję [uwierzytelnianie](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) . Jeśli uwierzytelnianie nie jest wymagane, Wyklucz tę właściwość. | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie |
timeout | Jak długo działanie będzie oczekiwać na wywołanie &#39;callBackUri.&#39; Jak długo działanie będzie oczekiwać na wywołanie elementu "callBackUri". Wartość domyślna to 10mins ("00:10:00"). Format to TimeSpan, np. hh: mm: SS | Ciąg | Nie |
Stan raportu dla wywołania zwrotnego | Zezwala użytkownikowi na raportowanie stanu niepowodzenia aktywności elementu webhook, co oznacza działanie jako zakończone niepowodzeniem | Wartość logiczna | Nie |

## <a name="authentication"></a>Uwierzytelnianie

Poniżej przedstawiono obsługiwane typy uwierzytelniania w działaniu elementu webhook.

### <a name="none"></a>None

Jeśli uwierzytelnianie nie jest wymagane, nie należy uwzględniać właściwości "Authentication".

### <a name="basic"></a>Podstawowa

Określ nazwę użytkownika i hasło, które mają być używane z uwierzytelnianiem podstawowym.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certyfikat klienta

Określ zawartość pliku PFX i hasło w formacie base64.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Tożsamość zarządzana

Określ identyfikator URI zasobu, dla którego będzie wymagany token dostępu przy użyciu tożsamości zarządzanej fabryki danych. Aby wywołać interfejs API zarządzania zasobami platformy Azure, użyj `https://management.azure.com/`. Aby uzyskać więcej informacji na temat sposobu działania tożsamości zarządzanych, zobacz [stronę Omówienie zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Jeśli Fabryka danych została skonfigurowana przy użyciu repozytorium git, musisz przechowywać poświadczenia w Azure Key Vault, aby używać uwierzytelniania podstawowego lub certyfikatu klienta. Azure Data Factory nie zapisuje haseł w usłudze git.

## <a name="additional-notes"></a>Uwagi dodatkowe

Azure Data Factory przekaże dodatkową właściwość "callBackUri" w treści do punktu końcowego adresu URL i będzie oczekiwać, że ten identyfikator URI będzie wywoływany przed określoną wartością limitu czasu. Jeśli identyfikator URI nie zostanie wywołany, działanie zakończy się niepowodzeniem ze stanem "TimedOut".

Działanie elementu webhook kończy się niepowodzeniem, gdy wywołanie do niestandardowego punktu końcowego zakończy się niepowodzeniem. Dowolny komunikat o błędzie może zostać dodany do treści wywołania zwrotnego i użyty w kolejnym działaniu.

Dla każdego wywołania interfejsu API REST klient zostanie przekroczy limit czasu, jeśli punkt końcowy nie odpowie w ciągu 1 minuty. Jest to standardowe najlepsze rozwiązanie http. Aby rozwiązać ten problem, należy zaimplementować wzorzec 202 w tym przypadku, gdy punkt końcowy zwróci 202 (zaakceptowano), a klient będzie sondował.

1 minimalny limit czasu dla żądania nie ma niczego do wykonania z limitem czasu działania. Ta wartość zostanie użyta w celu poczekania na callbackUri.

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
