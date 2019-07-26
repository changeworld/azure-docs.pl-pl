---
title: Skalowanie przepływności w Azure Cosmos DB
description: W tym artykule opisano, jak Azure Cosmos DB skalować przepływność elastycznie
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 29a92f04a1d36004fa082bfafe2310f9e0e3e5c6
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467599"
---
# <a name="globally-scale-provisioned-throughput"></a>Globalne skalowanie aprowizowanej przepływności 

W Azure Cosmos DB, obsługiwana przepływność jest reprezentowana jako jednostki żądań na sekundę (RU/s lub w formacie plural jednostek ru). Jednostek ru Zmierz koszt operacji odczytu i zapisu w kontenerze Cosmos, jak pokazano na poniższej ilustracji:

![Jednostki żądań](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Można udostępnić jednostek RU w kontenerze Cosmos lub bazie danych Cosmos. Jednostek ru udostępniane w kontenerze są dostępne wyłącznie dla operacji wykonywanych w tym kontenerze. Jednostek ru udostępniane w bazie danych są współdzielone przez wszystkie kontenery w tej bazie danych (z wyjątkiem kontenerów z wyłącznie przypisanymi jednostek ru).

W celu elastycznego skalowania przepływności aprowizacji można w dowolnym momencie zwiększyć lub zmniejszyć liczbę zainicjowanych jednostek RU/s. Aby uzyskać więcej informacji, zobacz artykuł [jak zapewnić przepływność](set-throughput.md) i elastyczne skalowanie kontenerów Cosmos oraz baz danych. Aby globalnie skalować przepływność, możesz w dowolnym momencie dodać lub usunąć regiony z konta usługi Cosmos. Aby uzyskać więcej informacji, zobacz [Dodawanie/usuwanie regionów z konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Kojarzenie wielu regionów z kontem Cosmos jest ważne w wielu scenariuszach — w celu osiągnięcia małych opóźnień i [wysokiej dostępności](high-availability.md) na całym świecie.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Sposób dystrybuowania przepływności w różnych regionach

Jeśli zainicjujesz jednostek ru *"r"* w kontenerze Cosmos (lub bazie danych), Cosmos DB zapewnia dostępność *"r"* jednostek RU w *każdym* regionie skojarzonym z Twoim kontem Cosmos. Za każdym razem, gdy dodasz nowy region do konta, Cosmos DB automatycznie inicjuje jednostek ru *"R"* w nowo dodanym regionie. Operacje wykonywane względem kontenera Cosmos są gwarantowane w celu uzyskania jednostek ru *"R"* w każdym regionie. Nie można wybiórczo przypisywać jednostek ru do określonego regionu. Obsługa jednostek RU w kontenerze Cosmos (lub bazie danych) jest obsługiwana we wszystkich regionach skojarzonych z Twoim kontem Cosmos.

Przy założeniu, że kontener Cosmos jest skonfigurowany przy użyciu *elementu "R"* jednostek ru, a istnieją regiony *"N"* skojarzone z kontem Cosmos, wówczas:

- W przypadku skonfigurowania konta Cosmos z jednym regionem zapisu całkowita jednostek ru dostępna globalnie w kontenerze = *R* x *N*.

- Jeśli konto Cosmos jest skonfigurowane z wieloma regionami zapisu, łączna jednostek ru dostępna globalnie w kontenerze = *R* x (*N*+ 1). Dodatkowe jednostek ru *języka R* są automatycznie inicjowane w celu przetworzenia konfliktów aktualizacji i ruchu antyentropii w regionach.

Wybór [modelu spójności](consistency-levels.md) wpływa również na przepływność. Możesz uzyskać około 2x przepływność odczytu dla bardziej swobodnych poziomów spójności (np. *sesji*, *spójnego prefiksu* i spójności *ostatecznej* ) w porównaniu z silniejszymi poziomami spójności (np. *z ograniczeniami nieodświeżonymi* lub  *silna* spójność).

## <a name="next-steps"></a>Kolejne kroki

Następnie można dowiedzieć się, jak skonfigurować przepływność na kontenerze lub w bazie danych:

* [Pobieranie i Ustawianie przepływności dla kontenerów i baz danych](set-throughput.md) 

