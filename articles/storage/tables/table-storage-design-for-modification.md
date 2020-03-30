---
title: Projektowanie magazynu tabel platformy Azure do modyfikacji danych | Dokumenty firmy Microsoft
description: Projektowanie tabel do modyfikacji danych w magazynie tabel platformy Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771550"
---
# <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
W tym artykule koncentruje się na zagadnienia projektowe do optymalizacji wstawia, aktualizacje i usuwa. W niektórych przypadkach należy ocenić kompromis między projektami, które optymalizują zapytania względem projektów, które optymalizują modyfikację danych, tak jak w projektach dla relacyjnych baz danych (chociaż techniki zarządzania kompromisami projektowymi są w relacyjnej bazie danych). W sekcji Wzorce projektowania tabel opisano niektóre szczegółowe wzorce projektowe dla usługi tabeli i wyróżnia niektóre te kompromisy. W praktyce okaże się, że wiele projektów zoptymalizowanych pod kątem wykonywania zapytań jednostek również działa dobrze do modyfikowania jednostek.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizacja wydajności operacji wstawiania, aktualizowania i usuwania
Aby zaktualizować lub usunąć jednostkę, musisz mieć możliwość jej zidentyfikowania przy użyciu **partitionKey** i **RowKey** wartości. W związku z tym wybór **PartitionKey** i **RowKey** do modyfikowania jednostek powinny spełniać podobne kryteria do wyboru do obsługi zapytań punktowych, ponieważ chcesz zidentyfikować jednostki tak efektywnie, jak to możliwe. Nie chcesz używać nieefektywne partycji lub tabeli skanowania, aby zlokalizować jednostkę w celu odnajdywać **PartitionKey** i **RowKey** wartości trzeba zaktualizować lub usunąć go.  

Następujące wzorce w sekcji Wzorce projektowania tabeli są adres optymalizacji wydajności lub wstawiania, aktualizowania i usuwania operacji:  

* [Wzorzec usuwania dużych ilości](table-storage-design-patterns.md#high-volume-delete-pattern) — umożliwia usunięcie dużej liczby jednostek przez przechowywanie wszystkich jednostek do jednoczesnego usuwania w ich osobnej tabeli; usuwasz encje, usuwając tabelę.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) — przechowuj pełną serię danych w jednej jednostce, aby zminimalizować liczbę żądań.  
* [Szeroki wzorzec jednostek](table-storage-design-patterns.md#wide-entities-pattern) — użyj wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  
* [Wzorzec dużych elementów](table-storage-design-patterns.md#large-entities-pattern) — służy do przechowywania dużych wartości właściwości za pomocą magazynu obiektów blob.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnij spójność przechowywanych jednostek
Innym kluczowym czynnikiem, który wpływa na wybór kluczy do optymalizacji modyfikacji danych jest zapewnienie spójności przy użyciu transakcji niepodzielnych. EGT można używać tylko do działania na jednostkach przechowywanych w tej samej partycji.  

Następujące wzorce w [artykule Wzorce projektowania tabeli](table-storage-design-patterns.md) dotyczą zarządzania spójnością:  

* [Wewnątrzpoziomowy wzorzec indeksu pomocniczego](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) — przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości **Klawisza Wiersza** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie i alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey.**  
* [Wzorzec indeksu pomocniczego między partycjami](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości RowKey w oddzielnych partycjach lub w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwanie i alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey.**  
* [Ostatecznie spójny wzorzec transakcji](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) — włącz ostatecznie spójne zachowanie w granicach partycji lub granice systemu magazynu przy użyciu kolejek platformy Azure.
* [Wzorzec jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — obsługa jednostek indeksu w celu umożliwienia efektywnego wyszukiwania, które zwracają listy jednostek.  
* [Wzorzec denormalizacji](table-storage-design-patterns.md#denormalization-pattern) — łączenie powiązanych danych w jednej jednostce, aby umożliwić pobranie wszystkich potrzebnych danych za pomocą kwerendy punktowej.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) — przechowuj pełną serię danych w jednej jednostce, aby zminimalizować liczbę żądań.  

Aby uzyskać informacje o transakcjach grupy jednostek, zobacz sekcję [Transakcje grupy jednostek](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zadbaj o to, by twój projekt zapewniał wydajne modyfikacje, ułatwiające efektywne zapytania
W wielu przypadkach projekt efektywnego wykonywania zapytań powoduje wydajne modyfikacje, ale zawsze należy ocenić, czy jest to przypadek dla określonego scenariusza. Niektóre wzorce w artykule [Wzorce projektowania tabel](table-storage-design-patterns.md) jawnie oceniają kompromisy między jednostkami kwerendy i modyfikujące i zawsze należy wziąć pod uwagę liczbę każdego typu operacji.  

Następujące wzorce w [artykule Wzorce projektowania tabeli](table-storage-design-patterns.md) dotyczą kompromisów między projektowaniem w celu efektywnego poszukiwania i projektowaniem w celu wydajnej modyfikacji danych:  

* [Wzorzec klucza złożonego](table-storage-design-patterns.md#compound-key-pattern) — użyj złożonych wartości **RowKey,** aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą kwerendy z pojedynczym punktem.  
* [Wzorzec wiersza dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobieranie *n* jednostek ostatnio dodane do partycji przy użyciu **RowKey** wartość, która sortuje w odwrotnej kolejności daty i godziny.  
