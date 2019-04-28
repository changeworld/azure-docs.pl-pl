---
title: Zdarzenie filtrowania dla usługi Azure Event Grid
description: W tym artykule opisano jak filtrować zdarzenia w przypadku tworzenia subskrypcji usługi Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 87599b05a3569bf6f28880352185a131f48a7f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436175"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Omówienie zdarzeń filtrowania dla subskrypcji usługi Event Grid

W tym artykule opisano różne sposoby, aby filtrować zdarzenia, które są wysyłane do punktu końcowego usługi. Podczas tworzenia subskrypcji zdarzeń, masz trzy opcje filtrowania:

* Typy zdarzeń
* Temat zaczyna się lub kończy się ciągiem
* Pola zaawansowane i operatory

## <a name="event-type-filtering"></a>Filtrowanie typów zdarzeń

Domyślnie wszystkie [typy zdarzeń](event-schema.md) dla źródła zdarzenia są wysyłane do punktu końcowego. Możesz zdecydować się na wysyłanie tylko określone typy zdarzeń do punktu końcowego usługi. Na przykład możesz można uzyskać powiadomienia o aktualizacjach do zasobów, ale nie powiadomienie o inne operacje, takie jak usuwanie. W takim przypadku filtrowania według `Microsoft.Resources.ResourceWriteSuccess` typu zdarzenia. Udostępnij tablicę typów zdarzeń lub określ `All` można pobrać wszystkie typy zdarzeń dla źródła zdarzenia.

Jest ze składnią pliku JSON w celu filtrowania według typu zdarzenia:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Temat filtrowania

Proste filtrowanie według tematu, należy określić wartość początkową lub końcową dla podmiotu. Na przykład można określić, temat kończy się `.txt` można pobierać tylko zdarzenia związane z przekazywania pliku tekstowego do konta magazynu. Lub możesz filtrować temat zaczyna się od `/blobServices/default/containers/testcontainer` można pobrać wszystkie zdarzenia dla tego kontenera, ale nie innych kontenerów na koncie magazynu.

Podczas publikowania zdarzeń w tematy niestandardowe, należy utworzyć tematy dla zdarzeń, które ułatwiają subskrybentów dowiedzieć się, czy są one zainteresowani zdarzenia. Subskrybenci należy użyć właściwości podmiotu do filtrowania i trasy zdarzeń. Rozważ dodanie ścieżki, dla której zaszło zdarzenie, dzięki czemu subskrybenci można filtrować według segmentami tej ścieżki. Ścieżka pozwala subskrybentom wąskiego lub szeroko filtrowanie zdarzeń. Jeśli podano ścieżkę trzy segmentu, taką jak `/A/B/C` w tym temacie, subskrybenci można filtrować według pierwszy segment `/A` uzyskać szeroką gamę zdarzenia. Te zdarzenia jest subskrybenci z tematów, takich jak `/A/B/C` lub `/A/D/E`. Innymi subskrybentami, można filtrować według `/A/B` Aby uzyskać bardziej szczegółowy zestaw zdarzeń.

Jest ze składnią pliku JSON w celu filtrowania według typu zdarzenia:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtrowanie zaawansowane

Aby filtrować według wartości w polach danych oraz określić operator porównania, użyj zaawansowanych opcji filtrowania. W zaawansowanego filtrowania, należy określić:

* Operator typ — typ porównania.
* klucz: pole danych zdarzenia, które są używane do filtrowania. Można go, liczba, wartość logiczna lub ciąg.
* wartość lub wartości — wartość lub wartości do porównania z klucza.

Jest ze składnią pliku JSON dla korzystanie z zaawansowanych filtrów:

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>Operator

Są dostępne operatory dla numerów:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

Operator dostępne wartości logiczne to: BoolEquals

Dostępne operatory ciągów są:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Wszystkie porównania ciągów są insensitve przypadek.

### <a name="key"></a>Klucz

W przypadku zdarzeń w schemacie usługi Event Grid Użyj następujących wartości dla klucza:

* Identyfikator
* Temat
* Subject
* EventType
* dataVersion
* Dane zdarzenia (na przykład Data.key1)

W przypadku zdarzeń w schemacie zdarzeń w chmurze Użyj następujących wartości dla klucza:

* Identyfikator zdarzenia
* Element źródłowy
* EventType
* eventTypeVersion
* Dane zdarzenia (na przykład Data.key1)

Niestandardowy schemat danych wejściowych można użyć w polach danych zdarzeń (na przykład Data.key1).

### <a name="values"></a>Wartości

Możliwe wartości:

* numer
* string
* wartość logiczna
* tablica

### <a name="limitations"></a>Ograniczenia

Zaawansowane filtrowanie ma następujące ograniczenia:

* Pięć zaawansowane filtry dla subskrypcji usługi event grid
* 512 znaków na wartość ciągu
* Pięć wartości **w** i **nie** operatorów
* Klucz może mieć tylko jeden poziom zagnieżdżenia (na przykład data.key1)
* Schematy zdarzeń niestandardowych można filtrować tylko według pól najwyższego poziomu

Ten sam klucz może służyć w więcej niż jeden filtr.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej informacji na temat filtrowania zdarzeń za pomocą programu PowerShell i wiersza polecenia platformy Azure, zobacz [filtrowanie zdarzeń usługi Event Grid](how-to-filter-events.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
