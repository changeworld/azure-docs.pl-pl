---
title: Zdarzenie ogłaszania do tematu Azure siatki zdarzeń niestandardowych
description: Opisuje sposób przesłać zdarzenia do niestandardowego tematu Azure zdarzeń siatki
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: tomfitz
ms.openlocfilehash: e4256de1d9112d785b6d1cd52067fc99144a0a04
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303338"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>POST do tematu niestandardowych dla usługi Azure Event siatki

W tym artykule opisano, jak można wysłać zdarzenia do niestandardowego tematu. Przedstawia on format danych post i zdarzeń. [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) ma zastosowanie tylko do wpisów, które pasuje do oczekiwanego formatu.

## <a name="endpoint"></a>Endpoint

Podczas wysyłania HTTP POST do niestandardowego tematu, użyj formatu identyfikatora URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Na przykład jest prawidłowy identyfikator URI: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Aby uzyskać punkt końcowy dla niestandardowego tematu z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Aby uzyskać punkt końcowy dla niestandardowego tematu przy użyciu programu Azure PowerShell, należy użyć:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Nagłówek

W żądaniu, zawierają wartość nagłówka o nazwie `aeg-sas-key` zawierający klucz uwierzytelniania.

Na przykład jest wartością prawidłowego nagłówka `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Aby uzyskać klucz dla niestandardowego tematu z wiersza polecenia platformy Azure, należy użyć:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Aby uzyskać klucz dla niestandardowego tematu przy użyciu programu PowerShell, należy użyć:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dane zdarzenia

Niestandardowe tematy najwyższego poziomu dane zawierają tych samych polach jako standardowych zdarzeń zdefiniowanych zasobów. Jedna z tych właściwości jest właściwością danych zawierającego właściwości, które są unikatowe dla niestandardowego tematu. Jako wydawca zdarzeń można określić właściwości dla obiektu danych. Użyj następującego schematu:

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

Aby uzyskać opis tych właściwości, zobacz [schematu zdarzeń siatki zdarzeń Azure](event-schema.md). Zaksięgowania zdarzenia do zdarzenia tematu siatki, Tablica może mieć całkowity rozmiar maksymalnie 1 MB. Każde zdarzenie w tablicy jest ograniczony do 64 KB.

Na przykład schemat danych ważne jest:

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

Po publikowanie do punktu końcowego tematu, otrzymasz odpowiedź. Odpowiedź jest standardowy kod odpowiedzi HTTP. Niektóre typowe odpowiedzi są:

|Wynik  |Odpowiedź  |
|---------|---------|
|Powodzenie  | 200 OK  |
|Dane zdarzenia mają niepoprawny format | 400 Niewłaściwe żądanie |
|Nieprawidłowy klucz dostępu. | 401 nieautoryzowane |
|Nieprawidłowy punkt końcowy | 404 — Nie odnaleziono |
|Tablica lub zdarzeń przekracza limity rozmiaru | Ładunek 413 za duży |

Błędy treść komunikatu ma następujący format:

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

* Informacje o monitorowaniu dostaw zdarzeń, zobacz [dostarczanie komunikatów Monitora zdarzeń siatki](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [siatki zdarzeń zabezpieczeń i uwierzytelniania](security-authentication.md).
* Aby uzyskać więcej informacji o tworzeniu subskrypcji platformy Azure zdarzeń siatki, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).
