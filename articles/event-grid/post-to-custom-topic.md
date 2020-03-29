---
title: Publikowanie zdarzenia w niestandardowym temacie siatki zdarzeń platformy Azure
description: W tym artykule opisano sposób publikowania zdarzenia w temacie niestandardowym. Pokazuje format postu i danych zdarzeń.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721561"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Opublikuj temat niestandardowy dla usługi Azure Event Grid

W tym artykule opisano sposób publikowania zdarzenia w temacie niestandardowym. Pokazuje format postu i danych zdarzeń. [Umowa na poziomie usług (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) ma zastosowanie tylko do wpisów, które pasują do oczekiwanego formatu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Endpoint

Podczas wysyłania protokołu HTTP POST do tematu `https://<topic-endpoint>?api-version=2018-01-01`niestandardowego należy użyć formatu URI: .

Na przykład prawidłowym identyfikatorem `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`URI jest: .

Aby uzyskać punkt końcowy dla tematu niestandardowego za pomocą interfejsu wiersza polecenia platformy Azure, użyj:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Aby uzyskać punkt końcowy dla tematu niestandardowego za pomocą programu Azure PowerShell, należy użyć:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Nagłówek

W żądaniu dołącz wartość `aeg-sas-key` nagłówka o nazwie, która zawiera klucz do uwierzytelniania.

Na przykład prawidłową wartością nagłówka jest `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Aby uzyskać klucz do tematu niestandardowego za pomocą interfejsu wiersza polecenia platformy Azure, użyj:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Aby uzyskać klucz do tematu niestandardowego za pomocą programu PowerShell, należy użyć:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dane zdarzenia

W przypadku tematów niestandardowych dane najwyższego poziomu zawierają te same pola co standardowe zdarzenia zdefiniowane za zasoby. Jedną z tych właściwości jest właściwość danych, która zawiera właściwości unikatowe dla tematu niestandardowego. Jako wydawca zdarzeń należy określić właściwości dla tego obiektu danych. Użyj następującego schematu:

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

Aby uzyskać opis tych właściwości, zobacz [schemat zdarzeń usługi Azure Event Grid](event-schema.md). Podczas księgowania zdarzeń w temacie siatki zdarzeń tablica może mieć całkowity rozmiar do 1 MB. Każde zdarzenie w tablicy jest ograniczone do 64 KB (ogólna dostępność) lub 1 MB (wersja zapoznawcza).

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęte umową dotyczącą poziomu usług ogólnej dostępności (GA). Obsługa zdarzenia o rozmiarze do 1 MB jest obecnie w wersji zapoznawczej. Zdarzenia o masie powyżej 64 KB są naliczane w przyrostach o 64 KB. 

Na przykład prawidłowy schemat danych zdarzeń jest:

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

Po zaksięgowaniu w punkcie końcowym tematu otrzymasz odpowiedź. Odpowiedź jest standardowym kodem odpowiedzi HTTP. Niektóre typowe odpowiedzi są:

|Wynik  |Odpowiedź  |
|---------|---------|
|Powodzenie  | 200 ok.  |
|Dane zdarzenia mają nieprawidłowy format | 400 Zła prośba |
|Nieprawidłowy klucz dostępu | 401 Nieautoryzowane |
|Niepoprawny punkt końcowy | 404 — Nie znaleziono |
|Tablica lub zdarzenie przekracza limity rozmiaru | 413 Ładowność za duża |

W przypadku błędów treść wiadomości ma następujący format:

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

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące monitorowania dostaw zdarzeń, zobacz [Monitorowanie dostarczania komunikatów w sieci zdarzeń](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
