---
title: Omówienie przekształcania przepływu danych mapowania
description: Omówienie różnych przekształceń dostępnych w przepływie danych mapowania
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086921"
---
# <a name="mapping-data-flow-transformation-overview"></a>Omówienie przekształcania przepływu danych mapowania

Poniżej znajduje się lista przekształceń obecnie obsługiwanych w przepływie danych mapowania. Kliknij na każdej transformacji, aby dowiedzieć się o jego szczegóły konfiguracji.

| Nazwa | Kategoria | Opis |
| ---- | -------- | ----------- |
| [Agregacji](data-flow-aggregate.md) | Modyfikator schematu | Zdefiniuj różne typy agregacji, takie jak SUMA, MIN, MAX i COUNT pogrupowane według istniejących lub obliczonych kolumn. | 
| [Modyfikuj wiersz](data-flow-alter-row.md) | Modyfikator wierszy | Ustawianie zasad wstawiania, usuwania, aktualizowania i wprowadzania aktualizacji w wierszach. |
| [Podział warunkowy](data-flow-conditional-split.md) | Wiele wejść/wyjść | Rozsyłanie wierszy danych do różnych strumieni na podstawie warunków dopasowania. |
| [Kolumna pochodna](data-flow-derived-column.md) | Modyfikator schematu | generowanie nowych kolumn lub modyfikowanie istniejących pól przy użyciu języka wyrażenia przepływu danych. | 
| [Exists](data-flow-exists.md) | Wiele wejść/wyjść | Sprawdź, czy dane istnieją w innym źródle lub strumieniu. | 
| [Filtr](data-flow-filter.md) | Modyfikator wierszy | Filtrowanie wiersza na podstawie warunku. |
| [Spłaszczyć](data-flow-flatten.md) | Modyfikator schematu |  Weź wartości tablicy wewnątrz struktur hierarchicznych, takich jak JSON i rozwiń je w poszczególnych wierszach. |
| [Dołącz](data-flow-join.md) | Wiele wejść/wyjść |  Łączenie danych z dwóch źródeł lub strumieni. |
| [Wyszukiwanie](data-flow-lookup.md) | Wiele wejść/wyjść | Dane referencyjne z innego źródła. |
| [Nowa gałąź](data-flow-new-branch.md) | Wiele wejść/wyjść | Zastosuj wiele zestawów operacji i przekształceń względem tego samego strumienia danych. |
| [Przestawianie](data-flow-pivot.md) | Modyfikator schematu | Agregacja, w której jedna lub więcej kolumn grupowania ma swoje odrębne wartości wierszy przekształcone w poszczególne kolumny. |
| [Wybierz](data-flow-select.md) | Modyfikator schematu | Kolumny aliasów i nazwy strumienia oraz upuszczanie lub ponowne kolejność kolumn |
| [Ujście](data-flow-sink.md) | - | Ostateczne miejsce docelowe twoich danych |
| [Sortowanie](data-flow-sort.md) | Modyfikator wierszy | Sortowanie przychodzących wierszy w bieżącym strumieniu danych |
| [Źródła](data-flow-source.md) | - | Źródło danych dla przepływu danych |
| [Klucz zastępczy](data-flow-surrogate-key.md) | Modyfikator schematu | Dodawanie przyrostowej wartości dowolnego klucza niebiznesowego |
| [Unia](data-flow-union.md) | Wiele wejść/wyjść | Łączenie wielu strumieni danych w pionie |
| [Anulowanie przestawiania](data-flow-unpivot.md) | Modyfikator schematu | Kolumny przestawne na wartości wierszy |
| [Okno](data-flow-window.md) | Modyfikator schematu |  Definiowanie agregacji kolumn opartych na oknach w strumieniach danych. |
