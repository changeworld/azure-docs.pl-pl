---
title: Azure schematów siatki zdarzeń dla zdarzenia usługi Media Services
description: Opisuje właściwości, które są dostępne dla zdarzeń usługi Media Services Azure zdarzeń siatki
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788130"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure schematów siatki zdarzeń dla zdarzenia usługi Media Services

Ten artykuł zawiera właściwości i schematów zdarzeń usługi Media Services.

## <a name="media-services-job-state-change-event"></a>Zdarzenia zmiany stanu zadania usługi multimediów

Ta sekcja zawiera zdarzenie zmiany właściwości i schematu dla stan zadania usługi multimediów.  

### <a name="available-event-types"></a>Typy dostępnych zdarzeń

Usługa Media Services **zadania** emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Wywoływane, gdy stan zmiany zadania. |

### <a name="example-event"></a>Przykład zdarzeń

W poniższym przykładzie przedstawiono schematu gotowego zdarzenia stan zadania: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Temat | ciąg | Zdarzenie siatki udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zasobu w obszarze zasobów konta usługi Media Services. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzenia. Na przykład "Microsoft.Media.JobStateChange". |
| eventTime | ciąg | Czas jest generowane zdarzenie oparte na czas UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia usługi Media Services. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersji schematu. |
| Element metadataVersion | ciąg | Wersja schematu metadanych zdarzeń. Zdarzenie siatki definiuje schemat właściwości najwyższego poziomu. Zdarzenie siatki udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| previousState | ciąg | Stan zadania przed zdarzeniem. |
| state | ciąg | Nowy stan zadania powiadomienia w tym zdarzeniu. Na przykład "w kolejce: zadanie oczekuje na zasoby" lub "zaplanowane: zadanie jest gotowy do uruchomienia".|

Gdy stan zadania może być jedna z wartości: *w kolejce*, *zaplanowane*, *przetwarzania*, *Zakończono*, *błąd*, *Anulowane*, *anulowanie*

## <a name="next-steps"></a>Kolejne kroki

[Rejestrowanie zdarzeń zmiany stanu zadania](job-state-events-cli-how-to.md)
