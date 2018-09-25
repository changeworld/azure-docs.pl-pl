---
title: Najważniejszych korzyści zapewnianych przez dystrybucję globalną usługi Azure Cosmos DB
description: Więcej informacji na temat usługi Azure Cosmos DB wielu wzorców, key korzyści oferowane przez replikację geograficzną, Multi-Master i użycie przypadki, gdzie jest pomocne.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968353"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB

W tym artykule opisano kluczowe korzyści dystrybuowanie danych globalnie i niektórych scenariuszy w czasie rzeczywistym, gdy potrzebne są dane globalne dystrybucji.

## <a name="key-benefits"></a>Najważniejsze korzyści

Dostępne są następujące kluczowe korzyści dostępnych dla konta, które korzystają z możliwości dystrybucji danych globalnych usługi Azure Cosmos DB:

* **Jednym jednocyfrową wartością opóźnień** — Azure Cosmos DB zapewnia < 10 ms, Odczyt i zapis opóźnienie w 99. percentylu, gwarantowana przez [finansowo umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **5-9 dostępności** — Azure Cosmos DB oferuje 99,999% Odczyt i zapis, gwarancję dostępności przez [finansowo umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Rozwiązywanie konfliktów elastyczne** — dla klientów, korzystając z możliwości wielu wzorców, usługi Azure Cosmos DB udostępnia trzy tryby konfliktów, aby zapewnić integralność i spójność danych globalnych.

* **Spójność możliwą do dostosowania** — Azure Cosmos DB obsługuje 5 różnych [poziomów spójności](consistency-levels.md) z dystrybucji globalnej, pamiętając o obsługę wszystkie elementy oprócz wysoki poziom spójności dla kont z możliwością doskonalenie znajomości multi.

* **Niejawne odporności na uszkodzenia** — dane replikowane w wielu regionach, aplikacje mogą zapewnia wysoką odporność na uszkodzenia względem regionalnych przestojów.

## <a name="use-cases"></a>Przypadki zastosowań

Poniżej przedstawiono małych przykładowe scenariusze, które mogą korzystać z globalnie dystrybucji możliwości usługi Azure Cosmos DB.

* **Aplikacje mediów społecznościowych** — aplikacje mediów społecznościowych wymagają małego opóźnienia, wysokiej dostępności i skalowalności zapewniając doskonałe środowisko dla użytkowników znajdujących się na całym świecie.

* **IoT** -edge rozproszone geograficznie wdrożeń często zachodzi potrzeba śledzenia danych szeregów czasowych z wielu lokalizacji. Każde urządzenie może skonfigurowany do jego najbliższego regionu. Urządzenia przejdzie do innej lokalizacji, te urządzenia mogą rehomed do zapisu w najbliższym dostępnym regionie.

* **Handlu elektronicznego** -E-handlu wymaga bardzo małego opóźnienia, a także o wysokiej dostępności. Geodystrybucja danych za pomocą operacji odczytu i zapisu polega na spakowaniu danych najbliżej użytkowników i zwiększyć szybkość reakcji aplikacji. Dostępność dla odczytów i zapisów w wielu regionach zapewnia większą dostępność.

* **Wykrywanie oszustw/anomalii** — często w przypadku aplikacji, które monitorowania aktywności użytkownika lub konto działania są globalnie rozproszone i musi śledzić kilka zdarzeń jednocześnie, aby zaktualizować wyniki zapewnienie wbudowanych metryk ryzyka.

* **Pomiaru** — zliczanie i regulacji użycia (np. wywołań interfejsu API używane minut transakcji na sekundę) można zaimplementować globalnie na ogromną przy użyciu usługi Azure Cosmos DB Multi-Master. Rozwiązywanie konfliktów wbudowanych gwarantuje zarówno dokładność liczb i rozporządzenie w czasie rzeczywistym.

## <a name="next-steps"></a>Kolejne kroki  

W tym artykule opisano kluczowe korzyści i przypadki użycia, możliwości dystrybucji danych globalnych w usłudze Azure Cosmos DB. Następnie Przyjrzyj się następujące zasoby:

* [Jak usługa Azure Cosmos DB umożliwia kompleksowa dystrybucja globalna](distribute-data-globally-turnkey.md)

* [Jak skonfigurować usługę Azure Cosmos DB globalna Replikacja bazy danych](tutorial-global-distribution-sql-api.md)

* [Jak włączyć Multi-Master dla kont usługi Azure Cosmos DB](enable-multi-master.md)

* [Sposób działania automatycznego i ręcznego trybu failover w usłudze Azure Cosmos DB](regional-failover.md)

* [Opis rozwiązywania konfliktów w usłudze Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Używanie Multi-Master z bazy danych NoSQL typu open source](multi-master-oss-nosql.md)
