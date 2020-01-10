---
title: Projektowanie magazynu tabel platformy Azure pod kątem modyfikacji danych | Microsoft Docs
description: Projektowanie tabel do modyfikacji danych w usłudze Azure Table Storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771550"
---
# <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
Ten artykuł koncentruje się na zagadnieniach projektowych dotyczących optymalizacji operacji wstawiania, aktualizacji i usuwania. W niektórych przypadkach konieczne będzie oceniać kompromisu między projekty, które optymalizują do wykonywania zapytań względem projektów, które Optymalizuj pod kątem modyfikacji danych, podobnie jak w projektach dla relacyjnych baz danych (mimo że techniki projektowania wad i zalet zarządzania są inne w przypadku relacyjnej bazy danych). W sekcjach wzorów projektowania tabeli opisano niektóre szczegółowe wzorce projektowe dla Table service i wyróżniają niektóre z nich. W praktyce znajdziesz, że wiele projektów, zoptymalizowane pod kątem zapytań jednostki również działać dobrze w przypadku modyfikowania jednostek.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizowanie wydajności operacji wstawiania, aktualizowania i usuwania
Aby zaktualizować lub usunąć jednostkę, musi mieć możliwość identyfikację za pomocą **PartitionKey** i **RowKey** wartości. W związku z tym wybór **PartitionKey** i **RowKey** modyfikowanie jednostek powinno postępuj zgodnie z podobne kryteria z wybranymi do obsługi zapytań o punkt, ponieważ chcesz zidentyfikować jednostki jako najbardziej wydajny sposób. Czy chcesz użyć nieefektywne skanowanie za pomocą partycji lub tabeli, aby zlokalizować jednostki w celu odnajdywania **PartitionKey** i **RowKey** wartości, musisz zaktualizować lub usunąć go.  

Następujące wzorce w wzorach projektu tabeli sekcji dotyczą optymalizacji wydajności lub operacji wstawiania, aktualizowania i usuwania:  

* [Duża Usuń wzorzec](table-storage-design-patterns.md#high-volume-delete-pattern) -Włącz usuwanie dużej liczby jednostek, przechowując wszystkie jednostki do usunięcia jednoczesnych w ich własnych osobnej tabeli; jednostki możesz usunąć przez usunięcie tabeli.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) -Store serii kompletne dane w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzeniu.  
* [Wzorzec szerokiego jednostek](table-storage-design-patterns.md#wide-entities-pattern) — korzystanie z wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwości.  
* [Wzorzec dużych jednostek](table-storage-design-patterns.md#large-entities-pattern) — usługa blob storage do przechowywania wartości właściwości dużych.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnianie spójności przechowywanych jednostek
Kluczowym czynnikiem, który ma wpływ na wybór klucze na potrzeby optymalizacji modyfikacji danych jest sposób zapewnienia spójności za pomocą transakcji niepodzielnej. EGT można używać tylko do wykonywania operacji jednostek przechowywanych w tej samej partycji.  

W poniższych wzorcach [wzorców projektu tabeli](table-storage-design-patterns.md) artykułów adres zarządzania spójnością:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec pomocniczy indeks partycji między](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek za pomocą różnych wartości RowKey w oddzielnych partycjach lub w oddzielnych tabelach umożliwiają szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych **RowKey** wartości.  
* [Wzorzec transakcji ostatecznie spójną](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -Włącz ostatecznie spójne zachowanie całej granice partycji lub granice system magazynu za pomocą kolejek systemu Azure.
* [Indeks jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  
* [Wzorzec denormalizacja](table-storage-design-patterns.md#denormalization-pattern) — łączenie pokrewnych danych ze sobą w pojedynczej jednostki, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania pojedynczy punkt.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) -Store serii kompletne dane w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzeniu.  

Informacje o transakcjach grupy jednostek można znaleźć w sekcji dotyczącej [transakcji grupy jednostek](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Upewnij się, że projekt do wydajnej modyfikacji ułatwia wykonywanie wydajnych zapytań
Projektowanie pod kątem wydajne tworzenie zapytań powoduje efektywne modyfikacje, ale w wielu przypadkach zawsze powinni sprawdzić, czy to w przypadku określonego scenariusza. Niektóre wzorce w [wzorcach projektowych tabeli](table-storage-design-patterns.md) artykułów wyraźnie ocenią różnice między kwerendami i modyfikowaniem jednostek, a zawsze należy wziąć pod uwagę liczbę poszczególnych typów operacji.  

Poniższe wzorce w [projekcie tabeli](table-storage-design-patterns.md) artykułów stanowią kompromis między projektowaniem dla wydajnych zapytań i projektowaniem do wydajnej modyfikacji danych:  

* [Wzorzec klucza złożonego](table-storage-design-patterns.md#compound-key-pattern) — Użyj złożonych wartości **RowKey** , aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  
* [Wzorzec ogona dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  
