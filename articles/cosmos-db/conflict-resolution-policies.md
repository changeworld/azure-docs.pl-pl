---
title: Rozwiązywanie konfliktów w usłudze Azure Cosmos DB
description: W tym artykule opisano kategorie konfliktu i zasady rozwiązywania konfliktów w usłudze Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 2f219ed6c3155e8b7d3d978116e1748f6c115fea
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244115"
---
# <a name="conflict-types-and-resolution-policies"></a>Zasad rozpoznawania i typy konfliktów

Konflikty i rozwiązywania konfliktów, że zasady są stosowane, jeśli Twoje konta usługi Cosmos jest skonfigurowany z wieloma zapisu regionów.

Dla kont usługi Cosmos DB, skonfigurowanych przy użyciu wielu regionów zapisu może wystąpić konflikt aktualizacji, gdy wiele modułów zapisujących aktualizacji jednocześnie tego samego elementu w wielu regionach. Konflikty aktualizacji są przydzielane do trzech następujących typów:

1. **Wstaw konflikty** może wystąpić, gdy aplikacja jednocześnie wstawia dwóch lub więcej elementów z tego samego unikatowego indeksu (na przykład właściwość ID) z co najmniej dwóch regionach. W takim przypadku wszystkie zapisy może się powieść, początkowo w ich regionach lokalnych, ale na podstawie zasady rozwiązywania konfliktów, które wybierzesz, tylko jeden element o identyfikatorze oryginalnego jest ostatecznie zatwierdzić.
2. **Zastąp konflikty** może wystąpić, gdy aplikacja aktualizuje pojedynczy element równocześnie z co najmniej dwóch regionach.
3. **Usuń konflikty** może wystąpić, gdy aplikacja jednocześnie usuwa element z jednego regionu i aktualizuje go z innego regionu.

## <a name="conflict-resolution-policies"></a>Zasad rozpoznawania konfliktu

Usługa cosmos DB oferuje elastyczny mechanizm rozwiązywania konfliktów aktualizacji opartych na zasadach. Możesz wybrać z następujących zasad rozpoznawania konfliktu dwóch na kontenerze Cosmos:

- **Ostatni zapis usługi Wins (LWW)** , która domyślnie używa właściwości zdefiniowane przez system sygnatura czasowa (oparta na protokole zegara synchronizacji czasu). Alternatywnie podczas korzystania z interfejsu API SQL usługi Cosmos DB umożliwia określenie wszystkich innych właściwości do wartości liczbowych niestandardowych, (nazywane również "path rozwiązywania konfliktów") służący do rozwiązywania konfliktów.  

Jeśli dwa lub więcej elementów konflikt wstawić lub Zastąp operacje, element, który zawiera najwyższą wartość "path rozwiązywania konfliktów" staje się "głównej". Jeśli wiele elementów ma tę samą wartość liczbową dla ścieżki rozwiązywania konfliktów, wersja wybranego "głównej" jest określana przez system. Wszystkie regiony są gwarantowane zbiegają się do pojedynczego zwycięzca i na końcu się przy użyciu identycznych wersji elementu zatwierdzone. W przypadku usunięcia konflikty związane, usunięto wersję zawsze wins za pośrednictwem operacji wstawienia lub Zastąp konflikty, niezależnie od wartości Ścieżka rozpoznawania konfliktu.

> [!NOTE]
> LWW jest domyślne zasady rozwiązywania konfliktów i jest dostępny dla programu SQL, tabela, MongoDB, Cassandra i interfejsów API rozwiązania Gremlin.

Aby dowiedzieć się więcej, zobacz [przykłady użycia LWW konflikt zasad rozpoznawania](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Niestandardowe** zasada ta jest przeznaczona dla semantyki zdefiniowanych przez aplikację do uzgodnienia konflikty. Podczas ustawiania tych zasad w kontenerze usługi Cosmos, należy również zarejestrować procedury przechowywane scalania jest wywoływana automatycznie, gdy wykryto konflikty aktualizacji w ramach transakcji bazy danych po stronie serwera. System zawiera dokładnie raz gwarantuje do wykonania procedury scalania w ramach protokołu zobowiązania.  

Konfigurowanie kontenera przy użyciu opcji niestandardowych rozdzielczości, ale albo nie można zarejestrować procedury scalania w kontenerze lub procedury scalania zgłasza wyjątek w czasie wykonywania, konflikty są zapisywane do konfliktów, źródła danych. Aplikację należy ręcznie rozwiązać konflikty w kanale informacyjnym konflikty. Aby dowiedzieć się więcej, zobacz [przykłady za pomocą zasad niestandardowych rozdzielczości i sposób korzystania z kanału informacyjnego konflikty](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

> [!NOTE]
> Zasady rozwiązywania konfliktów niestandardowego jest dostępna tylko dla kont usługi interfejsu API SQL.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się, jak skonfigurować zasady rozwiązywania konfliktów, korzystając z następującymi artykułami:

* [Jak używać LWW zasady rozwiązywania konfliktów](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Jak używać zasady rozwiązywania konfliktów niestandardowe](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Sposób użycia kanału informacyjnego konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed)
