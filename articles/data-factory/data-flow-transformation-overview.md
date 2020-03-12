---
title: Mapowanie transformacji przepływu danych — omówienie
description: Przegląd różnych przekształceń dostępnych w mapowaniu przepływu danych
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086921"
---
# <a name="mapping-data-flow-transformation-overview"></a>Mapowanie transformacji przepływu danych — omówienie

Poniżej znajduje się lista transformacji, które są obecnie obsługiwane w mapowaniu przepływu danych. Kliknij poszczególne przekształcenia, aby poznać jego szczegóły konfiguracji.

| Name (Nazwa) | Kategoria | Opis |
| ---- | -------- | ----------- |
| [Agreguj](data-flow-aggregate.md) | Modyfikator schematu | Zdefiniuj różne typy agregacji, takie jak SUM, MIN, MAX i COUNT pogrupowane według istniejących lub obliczanych kolumn. | 
| [Zmień wiersz](data-flow-alter-row.md) | Modyfikator wiersza | Ustawianie zasad INSERT, DELETE, Update i upsert w wierszach. |
| [Podział warunkowy](data-flow-conditional-split.md) | Wiele danych wejściowych/wyjściowych | Kierowanie wierszy danych do różnych strumieni w oparciu o pasujące warunki. |
| [Kolumna pochodna](data-flow-derived-column.md) | Modyfikator schematu | Generuj nowe kolumny lub Modyfikuj istniejące pola przy użyciu języka wyrażeń przepływu danych. | 
| [Istniejący](data-flow-exists.md) | Wiele danych wejściowych/wyjściowych | Sprawdź, czy Twoje dane istnieją w innym źródle lub w strumieniu. | 
| [Filtru](data-flow-filter.md) | Modyfikator wiersza | Filtruj wiersz na podstawie warunku. |
| [Flatten](data-flow-flatten.md) | Modyfikator schematu |  Należy zastosować wartości tablicowe w strukturach hierarchicznych, takich jak JSON, i odrzucić je w poszczególnych wierszach. |
| [Dołącz](data-flow-join.md) | Wiele danych wejściowych/wyjściowych |  Połącz dane z dwóch źródeł lub strumieni. |
| [Wyszukiwania](data-flow-lookup.md) | Wiele danych wejściowych/wyjściowych | Dane referencyjne z innego źródła. |
| [Nowa gałąź](data-flow-new-branch.md) | Wiele danych wejściowych/wyjściowych | Zastosuj wiele zestawów operacji i transformacji dla tego samego strumienia danych. |
| [Odbywa](data-flow-pivot.md) | Modyfikator schematu | Agregacja, w której co najmniej jedna kolumna grupowania ma swoje unikatowe wartości wierszy przekształcone w pojedyncze kolumny. |
| [Wybierz](data-flow-select.md) | Modyfikator schematu | Kolumny aliasów i nazwy strumieni oraz upuszczanie lub zmiana kolejności kolumn |
| [Fotografii](data-flow-sink.md) | - | Ostateczne miejsce docelowe dla danych |
| [Porządku](data-flow-sort.md) | Modyfikator wiersza | Sortuj wiersze przychodzące w bieżącym strumieniu danych |
| [Element źródłowy](data-flow-source.md) | - | Źródło danych dla przepływu danych |
| [Klucz zastępczy](data-flow-surrogate-key.md) | Modyfikator schematu | Dodaj przyrostową wartość klucza niezwiązanej z firmą |
| [Unii](data-flow-union.md) | Wiele danych wejściowych/wyjściowych | Łączenie wielu strumieni danych w pionie |
| [UNPIVOT](data-flow-unpivot.md) | Modyfikator schematu | Przestawianie kolumn na wartości wierszy |
| [Dział](data-flow-window.md) | Modyfikator schematu |  Zdefiniuj agregacje kolumn w strumieniach danych. |
