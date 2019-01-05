---
title: Typy rozwiązywania konfliktów i zasad rozpoznawania w usłudze Azure Cosmos DB
description: W tym artykule opisano kategorie konfliktu i zasady rozwiązywania konfliktów w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4af3f4c60f186c34d0f5522461ba70c68b59d1ab
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033897"
---
# <a name="conflict-types-and-resolution-policies"></a>Typy konfliktów i zasady ich rozwiązywania

Konflikty i rozwiązywania konfliktów, że zasady są stosowane, jeśli skonfigurowano konta usługi Azure Cosmos DB z wieloma zapisu regionów.

Dla kont usługi Azure Cosmos DB, skonfigurowanych przy użyciu wielu regionów zapisu może wystąpić konflikt aktualizacji, autorzy aktualizacji jednocześnie tego samego elementu w wielu regionach. Konflikty aktualizacji są przydzielane do trzech następujących typów:

* **Wstaw konflikty**: Te konflikty może wystąpić, gdy aplikacja jednocześnie wstawia dwóch lub więcej elementów z tego samego unikatowego indeksu z co najmniej dwóch regionach. Na przykład ten konflikt może wystąpić w przypadku właściwości Identyfikatora. Wszystkie operacje zapisu może się powieść początkowo w ich regionach lokalnych. Ale oparte na zasady rozwiązywania konfliktów, które wybierzesz, tylko jeden element o identyfikatorze oryginalnego ostatecznie zatwierdzić.

* **Zastąp konflikty**: Te konflikty mogą wystąpić, gdy aplikacja aktualizuje pojedynczy element równocześnie z co najmniej dwóch regionach.

* **Usuń konflikty**: Te konflikty mogą wystąpić, gdy aplikacja jednocześnie usuwa element z jednego regionu i aktualizuje go z innego regionu.

## <a name="conflict-resolution-policies"></a>Zasady rozwiązywania konfliktów

Usługa Azure Cosmos DB oferuje elastyczny mechanizm opartych na zasadach rozwiązywania konfliktów aktualizacji. Możesz wybrać dwie zasady rozwiązywania konfliktów na kontenerze usługi Azure Cosmos DB:

- **Ostatni zapis Wins (LWW)**: Te zasady rozpoznawania domyślnie używa właściwości zdefiniowane przez system sygnatury czasowej. Jest ona oparta na protokole zegara synchronizacji czasu. Jeśli używasz interfejsu API SQL usługi Azure Cosmos DB, można określić innych niestandardowych właściwości wartości liczbowych służący do rozwiązywania konfliktów. Właściwości niestandardowej wartości liczbowych jest również określany jako ścieżka rozpoznawania konfliktu. 

  Jeśli dwa lub więcej elementów konflikt wstawić lub Zastąp operacje, element o najwyższej wartości dla ścieżki rozwiązywania konfliktów staje się zwycięzca. System Określa zwycięzcą, jeśli wiele elementów ma taką samą wartość liczbową dla ścieżki rozwiązywania konfliktów. Wszystkie regiony są gwarantowane zbiegają się do pojedynczego zwycięzca i na końcu się przy użyciu tej samej wersji elementu zatwierdzone. Po usunięciu biorących udział konflikty, zawsze usuniętych wersji wins przez wstawić lub Zastąp konflikty. Ten wynik odbywa się niezależnie od tego, wartości Ścieżka rozpoznawania konfliktu.

  > [!NOTE]
  > Ostatni zapis Wins jest domyślne zasady rozwiązywania konfliktów. Jest ona dostępna dla kont usługi SQL, Azure Cosmos DB Table, MongoDB, Cassandra i interfejs API Gremlin.

  Aby dowiedzieć się więcej, zobacz [zasad rozpoznawania w konflikcie przykłady z zastosowaniem LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Niestandardowe**: Te zasady rozwiązania jest przeznaczona dla semantyki zdefiniowanych przez aplikację do uzgodnienia konflikty. Gdy ta zasada jest ustawiona na kontenera usługi Azure Cosmos DB, należy zarejestrować procedury przechowywane scalania. Ta procedura jest wywoływana automatycznie, gdy wykryto konflikty w ramach transakcji bazy danych na serwerze. System zawiera dokładnie raz gwarantuje do wykonania procedury scalania w ramach protokołu zobowiązania.  

  Istnieją dwa punkty do zapamiętania, jeśli skonfigurować kontenera przy użyciu opcji niestandardowych rozwiązywania konfliktów. Jeśli Licencjobiorca nie można zarejestrować procedury scalania w kontenerze lub procedury scalania zgłasza wyjątek w czasie wykonywania, konflikty są zapisywane do konfliktów, źródła danych. Następnie aplikacja musi ręcznie rozwiązać konflikty w konflikcie, źródła danych. Aby dowiedzieć się więcej, zobacz [przykłady za pomocą zasad niestandardowych rozwiązania i sposobu używania konflikty kanału informacyjnego](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Zasady rozwiązywania konfliktów niestandardowych jest dostępne tylko dla kont interfejsu API SQL.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak skonfigurować zasady rozwiązywania konfliktów. Zobacz następujące artykuły:

* [Użyj zasady rozwiązywania konfliktów LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Użyj zasady rozwiązywania konfliktów niestandardowe](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Użyj konflikty kanału informacyjnego](how-to-manage-conflicts.md#read-from-conflict-feed)
