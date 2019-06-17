---
title: Publikuj zdarzenia do tematu niestandardowego usługi Azure Event Grid
description: Opisuje, jak można opublikować zdarzenia do tematu niestandardowego usługi Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 14ae5f2a0b6a950889d8587cd4d03ff4fc9a171b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304210"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Wpis do tematu niestandardowego usługi Azure Event Grid

W tym artykule opisano, jak można opublikować zdarzenia do tematu niestandardowego. Przedstawia on format danych post i zdarzeń. [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) dotyczy tylko wpisy, które pasuje do oczekiwanego formatu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Endpoint

Podczas wysyłania HTTP POST do tematu niestandardowego, użyj formatu identyfikatora URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Na przykład jest prawidłowym identyfikatorem URI: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Aby uzyskać punkt końcowy dla tematu niestandardowego przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Aby uzyskać punkt końcowy dla tematu niestandardowego przy użyciu programu Azure PowerShell, należy użyć:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>nagłówek

W żądaniu, zawierają wartość nagłówka o nazwie `aeg-sas-key` zawierający klucz na potrzeby uwierzytelniania.

Na przykład, jest wartością prawidłowego nagłówka `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Aby uzyskać klucz dla tematu niestandardowego przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Aby uzyskać klucz dla tematu niestandardowego przy użyciu programu PowerShell, należy użyć:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dane zdarzenia

Niestandardowe tematy najwyższego poziomu dane zawierają te same pola jako standardowych zdarzeń zdefiniowanych przez zasób. Jedną z tych właściwości jest właściwością danych, która zawiera właściwości, które są unikatowe dla tematu niestandardowego. Jako wydawca zdarzeń należy określić właściwości dla tego obiektu danych. Użyj następującego schematu:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Aby uzyskać opis tych właściwości, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md). Ogłaszając zdarzenia do tematu usługi event grid tablicy może mieć całkowity rozmiar do 1 MB. Każde zdarzenie w tablicy jest ograniczony do 64 KB (ogólnie) lub 1 MB (wersja zapoznawcza).

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęty przez umowy poziomu usług (SLA) ogólna dostępność (GA). Pomoc techniczna dotycząca zdarzenie o rozmiarze do 1 MB jest obecnie w wersji zapoznawczej. Zdarzenia przekracza 64 KB są naliczane w przyrostach 64 KB. 

Na przykład jest schematu danych ważnego zdarzenia:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Odpowiedź

Po publikowanie do endpoint tematu, otrzymasz odpowiedź. Odpowiedź jest standardowy kod odpowiedzi HTTP. Niektóre typowe odpowiedzi są:

|Wynik  |Odpowiedź  |
|---------|---------|
|Powodzenie  | 200 OK  |
|Dane zdarzenia mają niepoprawny format | 400 Niewłaściwe żądanie |
|Nieprawidłowy klucz dostępu. | 401 Brak autoryzacji |
|Nieprawidłowy punkt końcowy | 404 — Nie odnaleziono |
|Tablica lub zdarzeń przekracza limit rozmiaru | Ładunek 413 zbyt duży |

Błędy treść wiadomości ma następujący format:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

* Informacje o monitorowaniu dostarczenie zdarzeń, zobacz [dostarczanie komunikatów Monitor Event Grid](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
