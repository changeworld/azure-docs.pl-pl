---
title: Wskazówki dotyczące projektowania tabel magazynu platformy Azure | Dokumenty firmy Microsoft
description: Zaprojektuj usługę tabel platformy Azure, aby wydajnie obsługiwać operacje odczytu.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61269851"
---
# <a name="guidelines-for-table-design"></a>Wytyczne dotyczące projektu tabel

Projektowanie tabel do użytku z usługą tabel magazynu azure bardzo różni się od zagadnień projektowych dla relacyjnej bazy danych. W tym artykule opisano wskazówki dotyczące projektowania rozwiązania usługi tabel, aby były wydajne odczytywane i wydajne.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Zaprojektuj rozwiązanie serwisowe tabel, aby było wydajne odczytywanie

* ***Projektowanie do wykonywania zapytań w aplikacjach intensywnie odczytu.*** Podczas projektowania tabel, należy pomyśleć o zapytaniach (zwłaszcza te wrażliwe na opóźnienia), które zostaną wykonane, zanim zastanowisz się, jak będzie aktualizować jednostki. Zazwyczaj skutkuje to wydajnym i wydajnym rozwiązaniem.  
* ***Określ zarówno PartitionKey i RowKey w zapytaniach.*** *Zapytania punktowe,* takie jak te, są najbardziej wydajnymi zapytaniami usługi tabel.  
* ***Należy rozważyć przechowywanie zduplikowanych kopii jednostek.*** Magazyn tabel jest tani, więc należy rozważyć przechowywanie tej samej jednostki wiele razy (z różnymi kluczami), aby włączyć bardziej wydajne zapytania.  
* ***Należy wziąć pod uwagę denormalizing danych.*** Przechowywanie tabel jest tanie, więc należy rozważyć denormalizing danych. Na przykład należy przechowywać jednostki podsumowania, tak aby kwerendy dotyczące danych agregujących muszą mieć dostęp tylko do jednej jednostki.  
* ***Użyj złożonych wartości klucza.*** Jedynymi kluczami, które masz są **PartitionKey** i **RowKey**. Na przykład użyj złożonych wartości klucza, aby włączyć alternatywne ścieżki dostępu z kluczem do jednostek.  
* ***Użyj projekcji kwerendy.*** Można zmniejszyć ilość danych przesyłanych przez sieć za pomocą kwerend, które wybierają tylko pola, których potrzebujesz.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Zaprojektuj rozwiązanie usługi tabeli, aby było wydajne pod względem zapisu  

* ***Nie należy tworzyć gorących partycji.*** Wybierz klawisze, które umożliwiają rozłożenie żądań na wiele partycji w dowolnym momencie.  
* ***Unikaj skoków w ruchu.*** Wygładź ruch w rozsądnym czasie i unikaj skoków w ruchu.
* ***Nie koniecznie należy utworzyć oddzielną tabelę dla każdego typu encji.*** Jeśli potrzebujesz transakcji niepodzielnych między typami jednostek, można przechowywać te typy wielu jednostek w tej samej partycji w tej samej tabeli.
* ***Należy wziąć pod uwagę maksymalną przepływność, którą należy osiągnąć.*** Należy pamiętać o cele skalowalności dla usługi tabel i upewnij się, że projekt nie spowoduje, aby je przekroczyć.  

Czytając ten przewodnik, zobaczysz przykłady, które wcieli wszystkie te zasady w życie. 

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabel](table-storage-design-patterns.md)
- [Projektowanie pod kątem wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfruj dane tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
