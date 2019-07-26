---
title: Obecność regionalnych za pomocą usługi Azure Cosmos DB
description: W tym artykule opisano regionalnych obecności usługi Azure Cosmos DB i środowisk w innej chmurze.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 775ca8c57a9a444df2f7d0a4df4224b7eba9d677
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384045"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Obecność regionalnych za pomocą usługi Azure Cosmos DB

Azure Cosmos DB to fundamentowa usługa na platformie Azure, a domyślnie zawsze jest dostępna we wszystkich regionach, w których platforma Azure jest dostępna. Obecnie platforma Azure jest dostępna w [54 regionów](https://azure.microsoft.com/global-infrastructure/regions/) na całym świecie. 

[![Regiony, w których Azure Cosmos DB jest dostępna](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Usługa cosmos DB jest dostępna we wszystkich pięciu środowiskach różne chmury platformy Azure dostępne dla klientów:

* **Publicznej platformy Azure** chmury, co jest dostępne na całym świecie.

* **Usługa Azure Chiny 21Vianet** jest dostępna za pomocą unikatowego powiązania między firmą Microsoft i 21Vianet — jednym z największych dostawców Internetu w danym kraju w Chinach.

* **Azure (Niemcy)** świadczy usługi w modelu powiernika danych, który zapewnia tego klienta dane pozostają w Niemczech pod kontrolą T-Systems International GmbH, podmiot zależny firmy Deutsche Telecom, działają jako niemiecki powiernik danych.

* **Platforma Azure Government** jest dostępna w czterech regionach na terenie Stanów Zjednoczonych do agencji rządowych Stanów Zjednoczonych oraz partnerzy. 

* **Azure Government dla Departamentu Obrony (DoD)** jest dostępny w dwóch regionach w Stany Zjednoczone do Departamentu Obrony Stanów Zjednoczonych.

## <a name="regional-presence-with-global-distribution"></a>Regionalne obecności o globalnej dystrybucji

Wszystkie interfejsy API udostępniane przez Azure Cosmos DB (w tym SQL, MongoDB, Cassandra, Gremlin i Table) są domyślnie dostępne we wszystkich regionach platformy Azure. Można na przykład mieć MongoDB i Cassandra interfejsy API udostępniane przez Azure Cosmos DB nie tylko we wszystkich globalnych regionach świadczenia usługi Azure, ale również w przypadku suwerennych chmur, takich jak Chiny, Niemcy, instytucje rządowe i Departament Obrony (DoD).

Azure Cosmos DB to [globalnie dystrybuowana](distribute-data-globally.md) usługa bazy danych. Można skojarzyć dowolną liczbę regionów platformy Azure przy użyciu konta usługi Azure Cosmos i automatycznie i w przezroczysty sposób replikacji danych. Można dodać lub w dowolnej chwili można usunąć region do konta usługi Azure Cosmos. Gotowa do użycia funkcja dystrybucji globalnej, możliwości i protokołu replikacji zarządzanych multi Azure Cosmos DB oferuje mniej niż 10 ms, Odczyt i zapis opóźnienia w 99. percentylu, w przypadku, 99,999 dostępność odczytu i zapisu oraz możliwość elastycznego skalowania zainicjowano obsługę administracyjną. Przepływność dla operacji odczytu i zapisu we wszystkich regionach skojarzonych z Twoim kontem usługi Azure Cosmos. Usługa Azure Cosmos DB oferuje pięć dokładnie zdefiniowanych modeli spójności i możliwość zastosowania modelu spójności określonych danych. Na koniec Azure Cosmos DB jest jedyną usługą bazy danych w branży, która zapewnia kompleksową [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) obejmującą przepływność, opóźnienie 99 percentylu, wysoką dostępność i spójność. Powyższe możliwości są dostępne we wszystkich chmurach platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz możesz zapoznać się z podstawowymi koncepcjami Azure Cosmos DB z następującymi artykułami:

* [Rozkład danych globalnych](distribute-data-globally.md)
* [Jak zarządzać kontem usługi Azure Cosmos DB](manage-account.md)
* [Aprowizowanie przepływności baz danych i kontenerów usługi Azure Cosmos](set-throughput.md)
* [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
