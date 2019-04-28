---
title: Skalowanie przepływności w usłudze Azure Cosmos DB
description: W tym artykule opisano, jak usługi Azure Cosmos DB jest skalowana przepływność elastycznie
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926429"
---
# <a name="globally-scale-provisioned-throughput"></a>Globalne skalowanie aprowizowanej przepływności 

W usłudze Azure Cosmos DB, aprowizowana przepływność jest reprezentowany jako żądania jednostek na sekundę (RU/s lub mnogiej jednostek zarezerwowanych). Mierzenie koszt odczytu i zapisu operacji dotyczących kontenera usługi Cosmos, jak pokazano na poniższej ilustracji:

![Jednostki żądania](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Możesz aprowizować jednostek żądań na kontener Cosmos lub bazy danych Cosmos. Jednostek ru zaprowizowanych w kontenerze jest dostępna wyłącznie dla operacji wykonywanych w tym kontenerze. Jednostek ru zaprowizowanych w bazie danych są współużytkowane przez wszystkie kontenery w ramach tej bazy danych (z wyjątkiem wszystkie kontenery z wyłącznie przypisanych RUs).

Elastyczne skalowanie aprowizowanej przepływności, można zwiększyć lub zmniejszyć aprowizowanych jednostek RU/s w dowolnym momencie. Aby uzyskać więcej informacji, zobacz [porad aprowizowanie przepływności](set-throughput.md) i elastycznie Skaluj kontenery Cosmos i baz danych. Globalnie skalowania przepływności, można dodać lub usunąć regiony z konta usługi Cosmos w dowolnym momencie. Aby uzyskać więcej informacji, zobacz [Dodaj/Usuń regiony z Twojego konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Kojarzenie wielu regionów za pomocą konta usługi Cosmos jest ważne w wielu scenariuszach - uzyskanie małych opóźnień i [wysokiej dostępności](high-availability.md) na świecie.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>w jaki sposób aprowizowana przepływność jest dystrybuowane w wielu regionach

Zainicjowanie obsługi *"R"* jednostek ru na Cosmos kontenera (lub bazy danych), usługi Cosmos DB gwarantuje, że *"R"* (RUS) są dostępne w *każdego* regionu skojarzonego z Twoim kontem Cosmos. Po dodaniu każdego nowego regionu do swojego konta usługi Cosmos DB automatycznie aprowizuje *"R"* RUs w nowo dodanym regionie. Można pobrać gwarancję operacji wykonywanych względem kontenera usługi Cosmos *"R"* RUs w każdym regionie. Nie można selektywnie przypisywać jednostki zarezerwowane w określonym regionie. (RUS), którego obsługę zainicjowano na kontenerze Cosmos (lub bazy danych) są aprowizowane we wszystkich regionach skojarzonych z Twoim kontem Cosmos.

Przy założeniu, że skonfigurowano kontener Cosmos *"R"* (RUS) wiąże się z *"n"* regionów skojarzonych z kontem Cosmos następnie:

- Jeśli konto usługi Cosmos jest skonfigurowany z jednym regionie zapisu łączna liczba jednostek ru dostępna globalnie w kontenerze = *R* x *N*.

- Jeśli konto usługi Cosmos jest skonfigurowany z wieloma regionami zapisu, łączna liczba jednostek ru dostępna globalnie w kontenerze = *R* x (*N*+ 1). Dodatkowe *R* (RUS) są automatycznie konfigurowani do konfliktów aktualizacji procesu i ruch przeciw entropii między regionami.

Wybór [modelu spójności](consistency-levels.md) wpływa również na przepływność. Możesz też uzyskać około 2 x przepływność odczytu mniej restrykcyjne poziomów spójności (np. *sesji*, *spójny prefiks* i *ostatecznej* spójności) w porównaniu do silniejsze poziomów spójności (np. *powiązana nieaktualność* lub *silne* spójności).

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się, jak skonfigurować przepływność na kontenerze lub bazy danych:

* [Pobieranie i ustawianie przepływności baz danych i kontenerów](set-throughput.md) 

