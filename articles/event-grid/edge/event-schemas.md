---
title: Schematy zdarzeń — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Schematy zdarzeń w siatce zdarzeń na krawędzi IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242471"
---
# <a name="event-schemas"></a>Schematy zdarzeń

Moduł Event Grid akceptuje i dostarcza zdarzenia w formacie JSON. Obecnie istnieją trzy schematy, które są obsługiwane przez siatkę zdarzeń: -

* **EventGridSchema**
* **CustomSchema (CustomSchema)**
* **ChmuraEventSchema**

Można skonfigurować schemat, który wydawca musi być zgodny podczas tworzenia tematu. Jeśli nie określono, domyślnie **eventgridschema**. Zdarzenia, które nie są zgodne z oczekiwanym schematem zostaną odrzucone.

Subskrybenci mogą również skonfigurować schemat, w którym chcą zdarzenia dostarczane. Jeśli nie określono, domyślnie jest schemat tematu.
Obecnie schemat dostarczania subskrybenta musi być zgodny ze schematem wejściowym tematu. 

## <a name="eventgrid-schema"></a>Schemat EventGrid

Schemat EventGrid składa się z zestawu wymaganych właściwości, które jednostka publikowania musi być zgodna. Każdy wydawca musi wypełnić pola najwyższego poziomu.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Właściwości schematu EventGrid

Wszystkie zdarzenia mają następujące dane najwyższego poziomu:

| Właściwość | Typ | Wymagany | Opis |
| -------- | ---- | ----------- |-----------
| temat | ciąg | Nie | Powinien pasować do tematu, na którym jest publikowany. Usługa Event Grid wypełnia go nazwą tematu, na którym jest publikowana, jeśli nie jest określona. |
| Temat | ciąg | Tak | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Tak | Typ zdarzenia dla tego źródła zdarzenia, na przykład BlobCreated. |
| eventTime | ciąg | Tak | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| ID | ciąg | Nie | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Nie | Służy do przechwytywania danych zdarzeń, które są specyficzne dla jednostki publikowania. |
| dataVersion | ciąg | Tak | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Nie | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

### <a name="example--eventgrid-schema-event"></a>Przykład — zdarzenie schematu EventGrid

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Schemat CustomEvent

W schemacie niestandardowym nie ma żadnych właściwości obowiązkowych, które są wymuszane, takie jak schemat EventGrid. Jednostka publikowania może całkowicie kontrolować schemat zdarzeń. Zapewnia maksymalną elastyczność i umożliwia scenariusze, w których masz już system oparty na zdarzeniach i chcesz ponownie użyć istniejących zdarzeń i/lub nie chcesz być powiązany z określonym schematem.

### <a name="custom-schema-properties"></a>Niestandardowe właściwości schematu

Brak właściwości obowiązkowych. Określenie ładunku zależy od jednostki publikowania.

### <a name="example--custom-schema-event"></a>Przykład — Zdarzenie schematu niestandardowego

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Schemat CloudEvent

Oprócz powyższych schematów usługa Event Grid natywnie obsługuje zdarzenia w [schemacie JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents to otwarta specyfikacja opisująca dane zdarzeń. Upraszcza współdziałanie, zapewniając wspólny schemat zdarzeń do publikowania i zużywanie zdarzeń. Jest częścią [CNCF](https://www.cncf.io/) i obecnie dostępna wersja jest 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Właściwości schematu CloudEvent

Zapoznaj się [ze specyfikacją CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) na temat właściwości koperty obowiązkowej.

### <a name="example--cloud-event"></a>Przykład — zdarzenie w chmurze
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
