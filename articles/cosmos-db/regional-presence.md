---
title: Obecność regionalna dzięki usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono o regionalnej obecności usługi Azure Cosmos DB i różnych środowiskach chmury.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753227"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Obecność regionalna dzięki usłudze Azure Cosmos DB

Usługa Azure Cosmos DB jest usługą podstawową na platformie Azure i domyślnie jest zawsze dostępna we wszystkich regionach, w których dostępna jest platforma Azure. Obecnie platforma Azure jest dostępna w [54 regionach](https://azure.microsoft.com/global-infrastructure/regions/) na całym świecie. 

[![Regiony, w których dostępna jest usługa Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Usługa Cosmos DB jest dostępna we wszystkich pięciu różnych środowiskach chmury platformy Azure dostępnych dla klientów:

* **Chmura publiczna platformy Azure,** która jest dostępna na całym świecie.

* **Usługa Azure China 21Vianet** jest dostępna za pośrednictwem unikatowego partnerstwa między firmą Microsoft i 21Vianet, jednym z największych dostawców internetu w Chinach.

* **Platforma Azure Germany** świadczy usługi w ramach modelu powiernika danych, który zapewnia, że dane klientów pozostają w Niemczech pod kontrolą T-Systems International GmbH, spółki zależnej Deutsche Telecom, działającej jako niemiecki powiernik danych.

* **Usługa Azure Government** jest dostępna w czterech regionach Stanów Zjednoczonych dla amerykańskich agencji rządowych i ich partnerów. 

* **Usługa Azure Government for Department of Defense (DoD)** jest dostępna w dwóch regionach Stanów Zjednoczonych dla Departamentu Obrony STANÓW Zjednoczonych.

## <a name="regional-presence-with-global-distribution"></a>Obecność regionalna z globalną dystrybucją

Wszystkie interfejsy API udostępniane przez usługę Azure Cosmos DB (w tym SQL, MongoDB, Cassandra, Gremlin i Table) są domyślnie dostępne we wszystkich regionach platformy Azure. Na przykład interfejsy API mongodb i cassandra mogą być udostępniane przez usługę Azure Cosmos DB nie tylko we wszystkich globalnych regionach platformy Azure, ale także w suwerennych chmurach, takich jak Chiny, Niemcy, rząd i Departament Obrony (DoD).

Usługa Azure Cosmos DB to [usługa globalnie rozproszonej](distribute-data-globally.md) bazy danych. Możesz skojarzyć dowolną liczbę regionów platformy Azure z kontem usługi Azure Cosmos, a dane są replikowane automatycznie i w sposób przezroczysty. Region usługi Azure Cosmos można w dowolnym momencie dodać lub usunąć do konta usługi Azure Cosmos. Dzięki możliwości globalnej dystrybucji "pod klucz" i wielowpanicowanej replikacji usługa Azure Cosmos DB oferuje mniej niż 10 ms opóźnień odczytu i zapisu przy 99 percentylu, dostępności odczytu i zapisu 99,999 oraz możliwości elastycznego skalowania aprowizacji przepływność odczytów i zapisów we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. Usługa Azure Cosmos DB, oferuje również pięć dobrze zdefiniowanych modeli spójności i można zastosować określony model spójności do danych. Na koniec usługa Azure Cosmos DB jest jedyną usługą bazy danych w branży, która zapewnia kompleksową [umowę dotyczącą poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) obejmującą aprowizowaną przepływność, opóźnienie na 99 percentylu, wysoką dostępność i spójność. Powyższe możliwości są dostępne we wszystkich chmurach platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o podstawowych pojęciach usługi Azure Cosmos DB, wykonując następujące artykuły:

* [Globalna dystrybucja danych](distribute-data-globally.md)
* [Jak zarządzać kontem usługi Azure Cosmos DB](manage-account.md)
* [Przepływność obsługi kontenerów i baz danych usługi Azure Cosmos](set-throughput.md)
* [Usługa Azure Cosmos DB NDLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
