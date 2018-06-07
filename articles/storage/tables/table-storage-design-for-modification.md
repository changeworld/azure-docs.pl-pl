---
title: Projektowanie tabelach magazynu Azure do modyfikacji danych | Dokumentacja firmy Microsoft
description: Projektowanie tabelach modyfikacji danych w magazynie tabel platformy Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 6c008175f01521ce4f96d13e58244dc72d9f6990
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660909"
---
# <a name="design-for-data-modification"></a>Projekt do modyfikacji danych
Ten artykuł skupia się na informacjach dotyczących projektowania optymalizacji operacji wstawienia, aktualizacje i usuwa. W niektórych przypadkach należy ocenić równowagę między projekty, które Optymalizuj dla zapytaniach dotyczących projektów, które Optymalizuj dla modyfikacji danych tak samo, jak w projektach relacyjnych baz danych (chociaż techniki zarządzania kompromisy projektu są różne relacyjnej bazy danych). Sekcja [wzorce projektowe tabeli](#table-design-patterns) opisano niektóre wzorce projektowe szczegółowe dla usługi tabel i opisano niektóre te kompromisy. W praktyce znajdziesz, że wiele projektów, zoptymalizowana pod kątem zapytań jednostek również działać efektywne w przypadku modyfikowania jednostek.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizowanie insert, update i operacji usuwania
Aby zaktualizować lub usunąć jednostki, musi być mógł zidentyfikować, używając **PartitionKey** i **RowKey** wartości. W tym zakresie wybór **PartitionKey** i **RowKey** do modyfikowania obiektów należy stosować podobnych kryteriów wybrany do obsługi punktu zapytania, ponieważ chcesz zidentyfikować jednostek jak najbardziej wydajny. Czy chcesz użyć nieefektywne partycji lub tabeli skanowanie można zlokalizować jednostki, aby odnaleźć **PartitionKey** i **RowKey** wartości, należy zaktualizować lub usunąć.  

Następujące wzorce w sekcji [wzorce projektowe tabeli](#table-design-patterns) adresów optymalizacji wydajności lub Twoje insert, update i operacji usunięcia:  

* [Wzorzec usunąć dużą](table-storage-design-patterns.md#high-volume-delete-pattern) -Włącz usuwanie dużej liczby jednostek przez zapisanie wszystkich jednostek do usunięcia jednoczesnych w ich własnych osobnej tabeli; usuwanie jednostek przez usunięcie tabeli.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) -magazynu pełnych danych serii w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzania.  
* [Wzorzec szeroki jednostek](table-storage-design-patterns.md#wide-entities-pattern) — umożliwia przechowywanie jednostek logicznych z więcej niż 252 właściwości wiele jednostek fizycznych.  
* [Wzorzec dużych jednostek](table-storage-design-patterns.md#large-entities-pattern) — magazyn obiektów blob służy do przechowywania dużej wartości.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnienia spójności przechowywanych jednostek
Kluczowym czynnikiem, który ma wpływ na wybór klucze optymalizacji modyfikacji danych jest sposób zapewnienia spójności za pomocą atomic transakcji. EGT można używać tylko do działania na jednostek przechowywanych w tej samej partycji.  

Następujące wzorce w artykule [wzorce projektowe tabeli](table-storage-design-patterns.md) adresu zarządzania spójności:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), Włącz szybkie i wydajne wyszukiwań i alternatywne sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec między partycji pomocniczy indeks](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowywać wiele kopii każdej jednostki przy użyciu różnych wartości RowKey w osobnych partycji lub w oddzielnych tabelach umożliwia szybkie i wydajne wyszukiwanie i sortowanie alternatywne porządkuje przy użyciu różnych **RowKey** wartości.  
* [Wzorzec transakcji po pewnym czasie spójne](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -włączyć spójności po pewnym czasie działania przez granice partycji lub granice systemu magazynu za pomocą kolejek platformy Azure.
* [Wzorzec jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — Obsługa indeksu jednostek umożliwiające wydajne wyszukiwanie, które zwracają list jednostek.  
* [Wzorzec denormalization](table-storage-design-patterns.md#denormalization-pattern) — łączenie dane dotyczące razem w pojedynczej jednostki umożliwiają można pobrać wszystkich danych, należy za pomocą kwerendy pojedynczy punkt.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) -magazynu pełnych danych serii w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzania.  

Informacje o transakcjach grupy jednostek, w sekcji [transakcji grupy jednostek](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Upewnij się, że projekt wydajne modyfikacji ułatwia efektywne zapytań
W wielu przypadkach zaprojektować wydajne podczas badania powoduje efektywne modyfikacje, ale zawsze należy ocenić, czy jest to dla konkretnego scenariusza. Niektóre wzorców w artykule [wzorce projektowe tabeli](table-storage-design-patterns.md) jawnie ocenić kompromis między sprawdzanie i modyfikowanie jednostek, a użytkownik powinien zawsze wziąć pod uwagę liczbę każdego typu operacji.  

Następujące wzorce w artykule [wzorce projektowe tabeli](table-storage-design-patterns.md) kompromis między projektowania zapytań wydajne i projektowania modyfikacji danych wydajne rozwiązania:  

* [Wzorca klucza złożonego](table-storage-design-patterns.md#compound-key-pattern) -Użyj złożone **RowKey** wartości, aby włączyć klienta do wyszukiwania powiązanych danych z zapytaniem pojedynczy punkt.  
* [Wzorzec końcowego fragmentu dziennika](table-storage-design-patterns.md#log-tail-pattern) -pobrać *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość sortujące odwrotnej daty i czasu kolejności.  
