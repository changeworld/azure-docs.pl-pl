---
title: Typy konfliktów i zasady rozwiązywania problemów w usłudze Azure Cosmos DB
description: W tym artykule opisano kategorie konfliktów i zasady rozwiązywania konfliktów w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441986"
---
# <a name="conflict-types-and-resolution-policies"></a>Typy konfliktów i zasady ich rozwiązywania

Zasady rozwiązywania konfliktów i konfliktów mają zastosowanie, jeśli konto usługi Azure Cosmos DB jest skonfigurowane z wieloma regionami zapisu.

W przypadku kont usługi Azure Cosmos skonfigurowanych z wieloma regionami zapisu mogą wystąpić konflikty aktualizacji, gdy autorzy równocześnie aktualizują ten sam element w wielu regionach. Konflikty aktualizacji mogą być następujące trzy typy:

* **Wstaw konflikty:** Te konflikty mogą wystąpić, gdy aplikacja jednocześnie wstawia dwa lub więcej elementów z tym samym unikatowym indeksem w dwóch lub więcej regionach. Na przykład ten konflikt może wystąpić z właściwością ID.

* **Zamień konflikty:** Te konflikty mogą wystąpić, gdy aplikacja aktualizuje ten sam element jednocześnie w dwóch lub więcej regionach.

* **Usuń konflikty:** Te konflikty mogą wystąpić, gdy aplikacja jednocześnie usuwa element w jednym regionie i aktualizuje go w innym regionie.

## <a name="conflict-resolution-policies"></a>Zasady rozwiązywania konfliktów

Usługa Azure Cosmos DB oferuje elastyczny mechanizm oparty na zasadach w celu rozwiązywania konfliktów zapisu. Można wybrać jedną z dwóch zasad rozwiązywania konfliktów w kontenerze usługi Azure Cosmos:

* **Ostatnie wygrane zapisu (LWW)**: Ta zasada rozpoznawania domyślnie używa właściwości sygnatury czasowej zdefiniowanej przez system. Jest on oparty na protokole zegara synchronizacji czasu. Jeśli używasz interfejsu API SQL, można określić dowolną inną niestandardową właściwość numeryczną (np. własne pojęcie sygnatury czasowej), która ma być używana do rozwiązywania konfliktów. Niestandardowa właściwość numeryczna jest również określana jako *ścieżka rozwiązywania konfliktów*. 

  Jeśli dwa lub więcej elementów jest w konflikcie podczas wstawiania lub zamieniania operacji, element o najwyższej wartości dla ścieżki rozwiązywania konfliktów staje się zwycięzcą. System określa zwycięzcę, jeśli wiele elementów ma taką samą wartość liczbową dla ścieżki rozwiązywania konfliktów. Wszystkie regiony są gwarantowane zbiegają się do jednego zwycięzcy i kończy się z tej samej wersji zatwierdzonego elementu. Gdy konflikty usuwania są zaangażowane, usunięta wersja zawsze wygrywa nad albo wstawić lub zastąpić konflikty. Ten wynik występuje bez względu na wartość ścieżki rozwiązywania konfliktów.

  > [!NOTE]
  > Ostatnie wygrane zapisu jest domyślną zasadą `_ts` rozwiązywania konfliktów i używa sygnatury czasowej dla następujących interfejsów API: SQL, MongoDB, Cassandra, Gremlin i Table. Niestandardowa właściwość numeryczna jest dostępna tylko dla interfejsu API SQL.

  Aby dowiedzieć się więcej, zobacz [przykłady, które używają zasad rozwiązywania konfliktów LWW](how-to-manage-conflicts.md).

* **Niestandardowe:** Ta zasada rozpoznawania jest przeznaczona dla semantyki zdefiniowanej przez aplikację do uzgadniania konfliktów. Po ustawieniu tej zasady w kontenerze usługi Azure Cosmos należy również zarejestrować *procedurę składowaną korespondencji seryjnej.* Ta procedura jest wywoływana automatycznie, gdy konflikty są wykrywane w ramach transakcji bazy danych na serwerze. System zapewnia dokładnie raz gwarancję wykonania procedury scalania w ramach protokołu zobowiązania.  

  Jeśli kontener zostanie skonfigurowany z opcją niestandardowej rozdzielczości i nie zarejestrujesz procedury scalania w kontenerze lub procedura scalania zgłoś wyjątek w czasie wykonywania, konflikty są zapisywane w *pliku danych o konfliktach*. Aplikacja musi następnie ręcznie rozwiązać konflikty w pliku danych o konfliktach. Aby dowiedzieć się więcej, zobacz [przykłady korzystania z zasad niestandardowego rozpoznawania i korzystania z pliku danych o konfliktach](how-to-manage-conflicts.md).

  > [!NOTE]
  > Niestandardowe zasady rozwiązywania konfliktów są dostępne tylko dla kont interfejsu API SQL.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować zasady rozwiązywania konfliktów:

* [Jak skonfigurować multi-master w aplikacjach](how-to-multi-master.md)
* [Jak zarządzać zasadami rozwiązywania konfliktów](how-to-manage-conflicts.md)
* [Jak czytać z pliku danych o konfliktach](how-to-manage-conflicts.md#read-from-conflict-feed)
