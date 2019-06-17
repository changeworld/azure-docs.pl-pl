---
title: Typy rozwiązywania konfliktów i zasad rozpoznawania z wieloma zapisu regionów w usłudze Azure Cosmos DB
description: W tym artykule opisano kategorie konfliktu i zasady rozwiązywania konfliktów w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 98e9f5fff1b74d417ee07ed0056c8046b49baa17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236547"
---
# <a name="conflict-types-and-resolution-policies"></a>Typy konfliktów i zasady ich rozwiązywania

Konflikty i rozwiązywania konfliktów, że zasady są stosowane, jeśli skonfigurowano konta usługi Azure Cosmos DB z wieloma zapisu regionów.

Dla konta usługi Azure Cosmos skonfigurowany z wieloma regionami zapisu może wystąpić konflikt aktualizacji, gdy moduły zapisujące aktualizacji jednocześnie tego samego elementu w wielu regionach. Konflikty aktualizacji mogą być następujące trzy typy:

* **Wstaw konflikty**: Te konflikty może wystąpić, gdy aplikacja jednocześnie wstawia dwie lub więcej elementów z tego samego unikatowego indeksu w co najmniej dwóch regionach. Na przykład ten konflikt może wystąpić z właściwością ID.

* **Zastąp konflikty**: Te konflikty mogą wystąpić, gdy aplikacja aktualizuje ten sam element jednocześnie w co najmniej dwóch regionach.

* **Usuń konflikty**: Te konflikty mogą wystąpić, gdy aplikacja jednocześnie usuwa element w jednym regionie i aktualizuje go w innym regionie.

## <a name="conflict-resolution-policies"></a>Zasady rozwiązywania konfliktów

Usługa Azure Cosmos DB oferuje elastyczny mechanizm rozwiązywania konfliktów przy zapisywaniu opartych na zasadach. Możesz wybrać dwie zasady rozwiązywania konfliktów na kontenerze usługi Azure Cosmos:

- **Ostatni zapis Wins (LWW)** : Te zasady rozpoznawania domyślnie używa właściwości zdefiniowane przez system sygnatury czasowej. Jest ona oparta na protokole zegara synchronizacji czasu. Jeśli korzystasz z interfejsu API SQL, można określić wszelkie inne wartości liczbowych właściwości niestandardowej (np. własny pojęcie sygnaturę czasową) służący do rozwiązywania konfliktów. Właściwości niestandardowej wartości liczbowych jest również nazywany *Ścieżka rozpoznawania konfliktu*. 

  Jeśli dwa lub więcej elementów konflikt wstawić lub Zastąp operacje, element o najwyższej wartości dla ścieżki rozwiązywania konfliktów staje się zwycięzca. System Określa zwycięzcą, jeśli wiele elementów ma taką samą wartość liczbową dla ścieżki rozwiązywania konfliktów. Wszystkie regiony są gwarantowane zbiegają się do pojedynczego zwycięzca i na końcu się przy użyciu tej samej wersji elementu zatwierdzone. Po usunięciu biorących udział konflikty, zawsze usuniętych wersji wins przez wstawić lub Zastąp konflikty. Ten wynik odbywa się niezależnie od tego, co to jest wartość Ścieżka rozpoznawania konfliktu.

  > [!NOTE]
  > Ostatni zapis Wins jest domyślne zasady rozwiązywania konfliktów. Jest dostępny dla następujących interfejsów API: SQL, MongoDB, Cassandra, Gremlin and Table.

  Aby dowiedzieć się więcej, zobacz [zasad rozpoznawania w konflikcie przykłady z zastosowaniem LWW](how-to-manage-conflicts.md).

- **Niestandardowy**: Te zasady rozwiązania jest przeznaczona dla semantyki zdefiniowanych przez aplikację do uzgodnienia konflikty. Po ustawieniu tych zasad w kontenerze usługi Azure Cosmos należy również zarejestrować *scalania procedury składowanej*. Ta procedura jest wywoływana automatycznie, gdy wykryto konflikty w ramach transakcji bazy danych na serwerze. System zawiera dokładnie raz gwarantuje do wykonania procedury scalania w ramach protokołu zobowiązania.  

  Jeśli kontenera można skonfigurować przy użyciu opcji niestandardowych rozdzielczości i zakończyć się niepowodzeniem do rejestrowania procedury scalania w kontenerze lub procedury scalania zgłasza wyjątek w czasie wykonywania, są zapisywane konflikty *konflikty kanału informacyjnego*. Następnie aplikacja musi ręcznie rozwiązać konflikty w konflikcie, źródła danych. Aby dowiedzieć się więcej, zobacz [przykłady za pomocą zasad niestandardowych rozwiązania i sposobu używania konflikty kanału informacyjnego](how-to-manage-conflicts.md).

  > [!NOTE]
  > Zasady rozwiązywania konfliktów niestandardowych jest dostępne tylko dla kont interfejsu API SQL.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak skonfigurować zasady rozwiązywania konfliktów:

* [Jak skonfigurować Multi-Master w swoich aplikacjach](how-to-multi-master.md)
* [Jak zarządzać zasadami rozwiązywania konfliktów](how-to-manage-conflicts.md)
* [Jak odczytać z konfliktów kanału informacyjnego](how-to-manage-conflicts.md#read-from-conflict-feed)
