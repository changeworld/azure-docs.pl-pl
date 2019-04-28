---
title: Wskazówki dotyczące usługi Azure storage table projekt | Dokumentacja firmy Microsoft
description: Projektowanie usługi Azure table, aby efektywnie obsługiwać operacje odczytu.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 04/23/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.component: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269851"
---
# <a name="guidelines-for-table-design"></a>Wytyczne dotyczące projektu tabel

Projektowanie tabel do użycia z usługą Azure storage table bardzo różni się od zagadnień projektowych dotyczących relacyjnej bazy danych. W tym artykule opisano wskazówki dotyczące projektowania rozwiązania usługi tabeli można odczytać wydajne i zapisywanie wydajne.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Projektowanie własnego rozwiązania usługi tabeli jako wydajność odczytu

* ***Projektowanie pod kątem wykonywania zapytania w odczycie aplikacji.*** Podczas projektowania tabel wziąć pod uwagę zapytań (szczególnie opóźnienia poufnych z nich), które będą wykonywane przed zaplanowaniem jak spowoduje zaktualizowanie jednostki. Skutkuje to zazwyczaj wydajne i wydajne rozwiązanie.  
* ***Należy określić zarówno właściwości PartitionKey i RowKey w zapytaniach.*** *Polecenie zapytania* np. są to najbardziej wydajne zapytania usługi tabeli.  
* ***Rozważ przechowywanie duplikatów kopii jednostki.*** Magazyn tabel jest magazynem tania dlatego należy rozważyć przechowywanie tego samego obiektu wiele razy (przy użyciu różnych kluczy) umożliwiające wydajniejszych zapytań.  
* ***Należy wziąć pod uwagę denormalizing danych.*** Magazyn tabel jest magazynem tania zatem należy wziąć pod uwagę denormalizing danych. Na przykład przechowywać podsumowania jednostki tak, aby zapytania dla danych zagregowanych wystarczy dostęp do pojedynczej jednostki.  
* ***Użyj wartości klucza złożonego.*** To tylko klucze masz **PartitionKey** i **RowKey**. Aby włączyć alternatywnego kluczem dostępu do ścieżki do jednostek, na przykład użyć wartości klucza złożonego.  
* ***Użyj projekcji zapytań.*** Można zmniejszyć ilość danych, który transferu za pośrednictwem sieci przy użyciu kwerend wybierających tylko pola, które są potrzebne.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Projektowanie własnego rozwiązania usługi tabeli to wydajne zapisu  

* ***Nie należy tworzyć partycje gorąca.*** Wybierz klucze, które umożliwiają żądań rozkłada się na wielu partycjach w dowolnym momencie.  
* ***Należy unikać skoków ruchu.*** Smooth ruch za pośrednictwem uzasadnionym czasie i uniknąć skoków ruchu.
* ***Nie musi utworzyć oddzielną tabelę dla każdego typu jednostki.*** Transakcje niepodzielne jest stosowanie różnych typów jednostek, tych typów jednostek można przechowywać w jednej partycji w tej samej tabeli.
* ***Należy wziąć pod uwagę maksymalną przepływność, którą należy osiągnąć.*** Należy wiedzieć o wartości docelowe skalowalności usługi tabel i upewnij się, że projekt nie spowoduje przekroczenie je.  

Ponieważ w tym przewodniku zobaczysz przykładów, które praktycznego wdrożenia wszystkich tych zasad. 

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe oparte na tabelę](table-storage-design-patterns.md)
- [Projektowanie pod kątem zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych w tabelach](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)