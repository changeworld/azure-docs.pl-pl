---
title: Skalowanie przepływności w usłudze Azure Cosmos DB
description: W tym artykule opisano, jak usługi Azure Cosmos DB jest skalowana przepływność elastycznie
services: cosmos-db
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 4da893f71f3bb91ba021dcf259d20ddd72bfbf90
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821994"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>Skalowanie przepływności w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB, aprowizowana przepływność jest reprezentowany jako żądania jednostek na sekundę (RU/s, liczba mnoga: jednostek zarezerwowanych). Mierzenie koszt odczytu i zapisu operacji dotyczących kontenera usługi Cosmos, jak pokazano na poniższej ilustracji:

![Jednostki żądania](./media/scale-throughput/figure1.png)

Możesz aprowizować jednostek żądań na kontener Cosmos lub bazy danych Cosmos. Jednostek ru zaprowizowanych w kontenerze jest dostępna wyłącznie dla operacji wykonywanych w tym kontenerze. Jednostek ru zaprowizowanych w bazie danych są współużytkowane przez wszystkie kontenery w ramach tej bazy danych (z wyjątkiem wszystkie kontenery z wyłącznie przypisanych RUs).

Elastyczne skalowanie przepływności, można zwiększyć lub zmniejszyć aprowizowanych jednostek RU/s w dowolnym momencie. Aby uzyskać więcej informacji, zobacz [porad aprowizowanie przepływności](set-throughput.md) i elastycznie Skaluj kontenery Cosmos i baz danych. Globalnie skalowania przepływności, można dodać lub usunąć regiony na Twoim koncie Cosmos w dowolnym momencie. Aby uzyskać więcej informacji, zobacz [Dodaj/Usuń regiony z Twojego konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Kojarzenie wielu regionów za pomocą konta usługi Cosmos jest ważne w wielu scenariuszach uzyskanie małych opóźnień i [wysokiej dostępności](high-availability.md) na świecie.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>w jaki sposób aprowizowana przepływność jest dystrybuowane w wielu regionach

Zainicjowanie obsługi RUs "R" w kontenerze Cosmos (lub bazy danych), usługi Cosmos DB gwarantuje, że "R" (RUS) są dostępne w *każdego* regionu skojarzonego z Twoim kontem Cosmos. Po dodaniu każdego nowego regionu do swojego konta usługi Cosmos DB automatycznie aprowizuje RUs "R" w nowo dodanym regionie. Operacje wykonywane wbrew kontenera usługi Cosmos jest gwarantowana można pobrać jednostek ru "R" w każdym regionie. Nie można selektywnie przypisywać jednostki zarezerwowane w określonym regionie. Jednostki zarezerwowane aprowizowaną dla kontenera Cosmos (lub bazy danych) są aprowizowane dla wszystkich regionów skojarzonych z Twoim kontem Cosmos.

Przy założeniu, że kontener Cosmos jest skonfigurowany przy użyciu "R" (RUS) wiąże się z "n" regiony skojarzony następnie przy użyciu konta Cosmos:

- Jeśli konto usługi Cosmos jest skonfigurowany z jednym regionie zapisu łączna liczba jednostek ru dostępna globalnie w kontenerze = R x N.

- Jeśli konto usługi Cosmos jest skonfigurowany z wieloma regionami zapisu, łączna liczba jednostek ru dostępna globalnie w kontenerze = R x (N + 1). Dodatkowe jednostki zarezerwowane języka R są automatycznie konfigurowani konfliktów aktualizacji procesu i ruch przeciw entropii między regionami.

Wybór [modelu spójności](consistency-levels.md) wpływa również na przepływności. Możesz uzyskać około 2 x przepływność odczytu, sesja, spójny prefiks i spójność na powiązana nieaktualność lub wysoki poziom spójności w porównaniu.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się, jak skonfigurować przepływność za pomocą następującego artykułu:

* [Pobieranie i ustawianie przepływności baz danych i kontenerów](set-throughput.md) 

