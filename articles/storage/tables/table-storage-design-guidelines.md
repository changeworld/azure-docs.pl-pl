---
title: Wskazówki dotyczące usługi Azure storage projektu tabeli | Dokumentacja firmy Microsoft
description: Projektowanie usługi tabeli platformy Azure, aby efektywnie obsługiwać operacje odczytu.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 5329d33aee1bb1a55e9982b1ba9e3e8329246980
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660897"
---
# <a name="guidelines-for-table-design"></a>Wskazówki dotyczące projektowania tabeli

Projektowanie tabelach do użycia z usługą magazynu platformy Azure tabeli bardzo różni się od projektowania relacyjnej bazy danych. W tym artykule opisano wskazówki dotyczące projektowania rozwiązania usługi tabeli można odczytać wydajne i zapisywanie wydajne.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Zaprojektowanie rozwiązania usługi tabeli za skuteczny odczytu

* ***Projekt do wykonywania zapytań w aplikacjach ciężki odczytu.*** Podczas projektowania tabel pomyśleć o zapytań (szczególnie opóźnienia poufnych z nich), które będą wykonywane przed zaplanowaniem będzie aktualizowania jednostki. Powoduje to zwykle rozwiązania wydajne i wydajności.  
* ***Określ zarówno PartitionKey i RowKey zapytania.*** *Polecenie zapytania* takich jak te są najbardziej efektywny zapytania usługi tabeli.  
* ***Warto przechowywać kopie jednostek.*** Magazyn tabel jest tanie dlatego należy rozważyć przechowywania tej samej jednostki zbyt wiele razy (z różnymi kluczami) umożliwiające efektywniejsze kwerendy.  
* ***Należy rozważyć denormalizing danych.*** Magazyn tabel jest tanie dlatego należy rozważyć denormalizing danych. Na przykład przechowywanie podsumowania jednostek, tak, aby zapytania dotyczące agregowanie danych wymagane jest tylko dostęp do pojedynczej jednostki.  
* ***Użyj wartości klucza złożonego.*** Są tylko klucze masz **PartitionKey** i **RowKey**. Na przykład użyć wartości klucza złożonego włączyć ścieżki alternatywnej kluczem dostępu do jednostek.  
* ***Użyj projekcji zapytań.*** Można zmniejszyć ilość danych za pośrednictwem sieci przy użyciu kwerend wybierających tylko pola, które są potrzebne.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Zaprojektowanie rozwiązania usługi tabeli za skuteczny zapisu  

* ***Nie należy tworzyć gorących partycji.*** Wybierz klucze, które umożliwiają rozłożyć żądań na wiele partycji w dowolnym czasie.  
* ***Unikaj największego ruchu.*** Wygładzanie ruch w odpowiednim przedziale czasu i uniknąć największego ruchu.
* ***Nie należy tworzyć niekoniecznie osobnej tabeli dla każdego typu jednostki.*** Jeśli wymagane jest atomic transakcji między typami jednostek, można przechowywać te wiele typów jednostek w tej samej partycji w tej samej tabeli.
* ***Należy wziąć pod uwagę maksymalną przepływność, którą należy osiągnąć.*** Należy znać docelowe skalowalności usługi tabel i upewnij się, że projektu nie spowoduje przekroczenie je.  

Omówione w tym przewodniku, zobaczysz przykłady, których praktycznego wdrożenia wszystkich tych zasad. 

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Projekt do wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projekt do modyfikacji danych](table-storage-design-for-modification.md)