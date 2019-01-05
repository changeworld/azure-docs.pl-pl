---
title: Obecność regionalnych za pomocą usługi Azure Cosmos DB
description: W tym artykule opisano regionalnych obecności usługi Azure Cosmos DB i środowisk w innej chmurze.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 5a4523405f7a9182bb5123ebacaebc9a9e5ae9a9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038667"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Regionalne obecności usługi Azure Cosmos DB

Obecnie platforma Azure jest dostępna w [54 regionów](https://azure.microsoft.com/global-infrastructure/regions/) na całym świecie. Usługa Azure Cosmos DB jest podstawowe usługi na platformie Azure i jest dostępna we wszystkich regionach, w których platforma Azure jest dostępna.

[![Regiony, w których usługa Azure Cosmos DB jest dostępna](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Usługa cosmos DB jest dostępna we wszystkich pięciu środowiskach różne chmury platformy Azure dostępne dla klientów:

* **Publicznej platformy Azure** chmury, co jest dostępne na całym świecie.

* **Chińska wersja platformy Azure** jest dostępna za pośrednictwem wyjątkowemu partnerstwu między firmą Microsoft i 21Vianet — jednym z największych dostawców Internetu w tym kraju.

* **Azure (Niemcy)** świadczy usługi w modelu powiernika danych, który zapewnia tego klienta dane pozostają w Niemczech pod kontrolą T-Systems International GmbH, podmiot zależny firmy Deutsche Telecom, działają jako niemiecki powiernik danych.

* **Platforma Azure Government** jest dostępna w czterech regionach na terenie Stanów Zjednoczonych do agencji rządowych Stanów Zjednoczonych oraz partnerzy. 

* **Platforma Azure Government dla departamentu tajności** jest dostępna w dwóch regionach w Stanach Zjednoczonych, aby Departamentu Obrony USA.

## <a name="regional-presence-with-global-distribution"></a>Regionalne obecności o globalnej dystrybucji

Różnych interfejsów API udostępnianych przez usługę Azure Cosmos DB (w tym SQL, MongoDB, Cassandra, Gremlin i Azure Table storage) są dostępne we wszystkich regionach platformy Azure. Na przykład możesz mieć bazy danych MongoDB i interfejsów API rozwiązania Cassandra udostępnianych przez usługę Azure Cosmos DB nie tylko we wszystkich regionach świata usługa Azure, ale także w suwerennych regionach platformy Azure, takich jak (Chiny), (Niemcy) i dla instytucji rządowych oraz Departamentu Obrony (DoD) regionów.

Usługa Azure Cosmos DB [globalnie dystrybuowane](distribute-data-globally.md) bazy danych. Można skojarzyć dowolną liczbę regionów platformy Azure przy użyciu konta usługi Azure Cosmos i automatycznie i w przezroczysty sposób replikacji danych. Można dodać lub w dowolnej chwili można usunąć region do konta usługi Azure Cosmos. Gotowa do użycia funkcja dystrybucji globalnej, możliwości i protokołu replikacji zarządzanych multi Azure Cosmos DB oferuje mniej niż 10 ms, Odczyt i zapis opóźnienia w 99. percentylu, w przypadku, 99,999 dostępność odczytu i zapisu oraz możliwość elastycznego skalowania zainicjowano obsługę administracyjną. Przepływność dla operacji odczytu i zapisu we wszystkich regionach skojarzonych z Twoim kontem usługi Azure Cosmos. Usługa Azure Cosmos DB oferuje pięć dokładnie zdefiniowanych modeli spójności i możliwość zastosowania modelu spójności określonych danych. Ponadto usługa Azure Cosmos DB to jedyna usługa bazy danych w branży zapewniająca kompleksową Umowa dotycząca poziomu usług (SLA) obsługę aprowizowanej przepływności, opóźnienie w 99. percentylu, wysoką dostępność i spójność.

## <a name="next-steps"></a>Kolejne kroki

Teraz informacje na temat różnych pojęcia dotyczące usługi Azure Cosmos DB z następujących artykułów:

* [Rozkład danych globalnych](distribute-data-globally.md)
* [Jak zarządzać kontem usługi Azure Cosmos DB](manage-account.md)
* [Aprowizowanie przepływności baz danych i kontenerów usługi Azure Cosmos](set-throughput.md)
* [Cosmos DB w umowie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
