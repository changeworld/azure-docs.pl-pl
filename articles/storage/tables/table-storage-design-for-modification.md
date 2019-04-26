---
title: Projektowanie tabel usługi Azure storage do modyfikacji danych | Dokumentacja firmy Microsoft
description: Projektowanie tabel do modyfikacji danych w usłudze Azure table storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: e993d169025f9b76c5e813bae31ca6cb2a39ba71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325888"
---
# <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
Ten artykuł koncentruje się na informacjach dotyczących projektowania optymalizacji operacji wstawiania, aktualizacji i usuwa. W niektórych przypadkach konieczne będzie oceniać kompromisu między projekty, które optymalizują do wykonywania zapytań względem projektów, które Optymalizuj pod kątem modyfikacji danych, podobnie jak w projektach dla relacyjnych baz danych (mimo że techniki projektowania wad i zalet zarządzania są inne w przypadku relacyjnej bazy danych). Wzorce projektowe tabeli w sekcji opisano niektóre wzorce szczegółowego projektowania usługi Table i opisano niektóre te wad. W praktyce znajdziesz, że wiele projektów, zoptymalizowane pod kątem zapytań jednostki również działać dobrze w przypadku modyfikowania jednostek.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizuj wydajność insert, update i operacje usuwania
Aby zaktualizować lub usunąć jednostkę, musi mieć możliwość identyfikację za pomocą **PartitionKey** i **RowKey** wartości. W związku z tym wybór **PartitionKey** i **RowKey** modyfikowanie jednostek powinno postępuj zgodnie z podobne kryteria z wybranymi do obsługi zapytań o punkt, ponieważ chcesz zidentyfikować jednostki jako najbardziej wydajny sposób. Czy chcesz użyć nieefektywne skanowanie za pomocą partycji lub tabeli, aby zlokalizować jednostki w celu odnajdywania **PartitionKey** i **RowKey** wartości, musisz zaktualizować lub usunąć go.  

Następujących wzorów w określonej sekcji adres wzorców projektowania tabeli optymalizacji wydajności lub wstawki, aktualizowanie i usuwanie działań:  

* [Duża Usuń wzorzec](table-storage-design-patterns.md#high-volume-delete-pattern) -Włącz usuwanie dużej liczby jednostek, przechowując wszystkie jednostki do usunięcia jednoczesnych w ich własnych osobnej tabeli; jednostki możesz usunąć przez usunięcie tabeli.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) -Store serii kompletne dane w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzeniu.  
* [Wzorzec szerokiego jednostek](table-storage-design-patterns.md#wide-entities-pattern) — korzystanie z wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwości.  
* [Wzorzec dużych jednostek](table-storage-design-patterns.md#large-entities-pattern) — usługa blob storage do przechowywania wartości właściwości dużych.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnienie spójności w jednostkach składowanych
Kluczowym czynnikiem, który ma wpływ na wybór klucze na potrzeby optymalizacji modyfikacji danych jest sposób zapewnienia spójności za pomocą transakcji niepodzielnej. EGT można używać tylko do wykonywania operacji jednostek przechowywanych w tej samej partycji.  

Następujące wzorce w artykule [wzorce projektowe oparte na tabeli](table-storage-design-patterns.md) adresów, zarządzanie spójności:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec pomocniczy indeks partycji między](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek za pomocą różnych wartości RowKey w oddzielnych partycjach lub w oddzielnych tabelach umożliwiają szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych **RowKey** wartości.  
* [Wzorzec transakcji ostatecznie spójną](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -Włącz ostatecznie spójne zachowanie całej granice partycji lub granice system magazynu za pomocą kolejek systemu Azure.
* [Wzorzec jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  
* [Wzorzec denormalizacja](table-storage-design-patterns.md#denormalization-pattern) — łączenie pokrewnych danych ze sobą w pojedynczej jednostki, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania pojedynczy punkt.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) -Store serii kompletne dane w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzeniu.  

Aby uzyskać informacje o transakcji grup jednostek, zobacz sekcję [transakcji grup jednostek](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Upewnij się, że projekt pod kątem efektywnego modyfikacje ułatwia wydajne zapytania
Projektowanie pod kątem wydajne tworzenie zapytań powoduje efektywne modyfikacje, ale w wielu przypadkach zawsze powinni sprawdzić, czy to w przypadku określonego scenariusza. Niektóre wzorce w artykule [wzorce projektowe oparte na tabeli](table-storage-design-patterns.md) jawnie oceny kompromis między wykonywania zapytań i modyfikowania obiektów, a użytkownik powinien zawsze wziąć pod uwagę liczbę każdego typu operacji.  

Następujące wzorce w artykule [wzorce projektowe oparte na tabeli](table-storage-design-patterns.md) kompromis między projektowanie pod kątem wydajne zapytania i projektowanie pod kątem modyfikacji danych wydajne rozwiązania:  

* [Wzorca klucza złożonego](table-storage-design-patterns.md#compound-key-pattern) — Użyj złożone **RowKey** wartości, aby włączyć klienta do wyszukiwania danych powiązane z zapytaniem pojedynczy punkt.  
* [Wzorzec ogona dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  
