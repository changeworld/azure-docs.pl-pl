---
title: Typy rozwiązywania konfliktów i zasad rozpoznawania w usłudze Azure Cosmos DB
description: W tym artykule opisano kategorie konfliktu i zasady rozwiązywania konfliktów w usłudze Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: c682b61a39224f2c80db8fe5fa153ea5e5d82922
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958573"
---
# <a name="conflict-types-and-resolution-policies"></a>Zasad rozpoznawania i typy konfliktów

Konflikty i rozwiązywania konfliktów, że zasady są stosowane, jeśli Twoje konta usługi Cosmos jest skonfigurowany z wieloma zapisu regionów.

Dla kont usługi Cosmos DB, skonfigurowanych przy użyciu wielu regionów zapisu może wystąpić konflikt aktualizacji, gdy wiele modułów zapisujących aktualizacji jednocześnie tego samego elementu w wielu regionach. Konflikty aktualizacji są przydzielane do trzech następujących typów:

1. **Wstaw konfliktów:** te konflikty może wystąpić, gdy aplikacja jednocześnie wstawia dwóch lub więcej elementów z tego samego unikatowego indeksu (na przykład właściwość ID) z co najmniej dwóch regionach. W takim przypadku wszystkie zapisy może się powieść, początkowo w ich regionach lokalnych, ale na podstawie zasady rozwiązywania konfliktów, które wybierzesz, tylko jeden element o identyfikatorze oryginalnego jest ostatecznie zatwierdzić.

1. **Zastąp konfliktów:** te konflikty może wystąpić, gdy aplikacja aktualizuje pojedynczy element równocześnie z co najmniej dwóch regionach.

1. **Usuń konfliktów:** te konflikty może wystąpić, gdy aplikacja jednocześnie usuwa element z jednego regionu i aktualizuje go w innym regionie.

## <a name="conflict-resolution-policies"></a>Zasady rozwiązywania konfliktów

Usługa cosmos DB oferuje elastyczny mechanizm opartych na zasadach rozwiązywania konfliktów aktualizacji. Możesz wybrać z następujących zasad rozpoznawania konfliktu dwóch na kontenerze Cosmos:

- **Ostatni zapis usługi Wins (LWW):** tych zasad rozpoznawania domyślnie używa właściwości zdefiniowane przez system sygnatura czasowa (oparta na protokole zegara synchronizacji czasu). Alternatywnie korzystając z interfejsu API SQL, Cosmos DB umożliwia określenie wszystkich innych właściwości do wartości liczbowych niestandardowych, (nazywane również "path rozwiązywania konfliktów") służący do rozwiązywania konfliktów.  

  Jeśli dwa lub więcej elementów konflikt wstawić lub Zastąp operacje, element, który zawiera najwyższą wartość "path rozwiązywania konfliktów" staje się "głównej". Jeśli wiele elementów ma tę samą wartość liczbową dla ścieżki rozwiązywania konfliktów, wersja wybranego "głównej" jest określana przez system. Wszystkie regiony są gwarantowane zbiegają się do pojedynczego zwycięzca i na końcu się przy użyciu identycznych wersji elementu zatwierdzone. Konflikt usuwania są zaangażowani, usuniętych wersji zawsze wins przez albo Wstaw lub Zastąp konflikty, niezależnie od wartości Ścieżka rozpoznawania konfliktu.

  > [!NOTE]
  > Ostatni zapis usługi Wins jest domyślne zasady rozwiązywania konfliktów i jest dostępna dla kont usługi SQL, tabela, bazy danych MongoDB, Cassandra i interfejs API Gremlin.

  Aby dowiedzieć się więcej, zobacz [przykłady użycia LWW konflikt zasad rozpoznawania](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Niestandardowy:** tych zasad rozpoznawania jest przeznaczona dla semantyki zdefiniowanych przez aplikację do uzgodnienia konflikty. Podczas ustawiania tych zasad w kontenerze usługi Cosmos, należy również zarejestrować procedury przechowywane scalania jest wywoływana automatycznie, gdy wykryto konflikty aktualizacji w ramach transakcji bazy danych na serwerze. System zawiera dokładnie raz gwarantuje do wykonania procedury scalania w ramach protokołu zobowiązania.  

  Konfigurowanie kontenera przy użyciu opcji niestandardowych rozdzielczości, ale albo nie można zarejestrować procedury scalania w kontenerze lub procedury scalania zgłasza wyjątek w czasie wykonywania, konflikty są zapisywane do konfliktów, źródła danych. Aplikację należy ręcznie rozwiązać konflikty w kanale informacyjnym konflikty. Aby dowiedzieć się więcej, zobacz [przykłady za pomocą zasad niestandardowych rozdzielczości i sposób korzystania z kanału informacyjnego konflikty](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Zasady rozwiązywania konfliktów niestandardowego jest dostępna tylko dla kont usługi interfejsu API SQL.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się, jak skonfigurować zasady rozwiązywania konfliktów, korzystając z następującymi artykułami:

* [Jak używać LWW zasady rozwiązywania konfliktów](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Jak używać zasady rozwiązywania konfliktów niestandardowe](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Sposób użycia kanału informacyjnego konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed)
