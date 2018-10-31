---
title: Wysoka dostępność w usłudze Azure Cosmos DB
description: W tym artykule opisano, jak usługa Azure Cosmos DB zapewnia wysoką dostępność
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fdf1bf227ed21111c1aa0754a8423c1ee6aa151b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244143"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Wysoka dostępność w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane regionami platformy Azure, które są skojarzone z kontem usługi Cosmos.  Usługa cosmos DB wykorzystuje wiele warstw nadmiarowości danych. Jak pokazano w poniższej ilustracji:

- Dane w kontenerach Cosmos w poziomie jest podzielona na partycje.
- W każdym regionie każda partycja jest chroniony przez zestawu replik wszystkie zapisy replikowane i trwałym zatwierdzeniu większością replik. Repliki są dystrybuowane na maksymalnie 10-20 domen błędów.
- We wszystkich regionach replikacja wszystkich partycji. Każdy region zawiera wszystkie partycje dane kontenera Cosmos i mogą akceptować zapisów i obsługiwać operacje odczytu.  

![Partycjonowanie zasobów](./media/high-availability/figure1.png) 

Jeśli Twoje konta usługi Cosmos jest rozłożona na regiony platformy Azure N, będzie co najmniej N x 4 kopii wszystkich danych. Oprócz małych opóźnień dostępu do danych i przepływność odczytu i zapisu skalowania w regionach skojarzonych z Twoim kontem Cosmos, również o większej liczbie regionów (N wyższej) zwiększa dostępność.  

## <a name="availability-slas"></a>Umowy SLA dotyczące dostępności  

Jako globalnie rozproszonej bazy danych Cosmos DB zapewnia kompleksowe umowy SLA, obejmujący przepustowość, opóźnienie w 99. percentylu, spójności i wysokiej dostępności. W poniższej tabeli opisano gwarancje dotyczące wysokiej dostępności oferowanej przez Cosmos DB dla kont pojedynczych i wielu regionów. Najwyższą dostępność zaleca się skonfigurowanie Twojego konta usługi Cosmos mieć wiele regionów zapisu.

|Typ operacji  | Pojedynczy Region |Multiregionalne (zapisuje pojedynczy region)|Multiregionalne (zapisuje wielu regionów|
|---------|---------|---------|-------|
|Zapisuje    | dostępność przez 99,99    |dostępność przez 99,99   |99,999|
|Czyta     | dostępność przez 99,99    |99,999  |99,999|

> [!NOTE]
> W praktyce dostępność rzeczywiste zapisu powiązana nieaktualność, sesja, spójny prefiks i spójność ostateczna modeli jest znacznie wyższa niż opublikowanych umowy SLA. I w praktyce faktyczną dostępność odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowanych umowy SLA.

## <a name="regional-outages"></a>Regionalnych przestojów

Regionalnej awarii nie są niczym niezwykłym, a usługi Azure Cosmos DB zapewnia, że baza danych jest zawsze dostępna. Poniżej znajdują się zachowanie usługi Cosmos DB podczas awarii, w zależności od konfiguracji konta usługi Cosmos: 

- Skonfigurowano regiony odczytu dla wielu kont w wielu regionach pozostanie o wysokiej dostępności dla obu zapisuje i odczytuje podczas regionalnej awarii. Wywoływania regionalnego trybu failover są natychmiastowe i nie wymaga żadnych zmian z aplikacji.

- Kont w wielu regionach z regionem zapisu pojedynczego podczas awarii region zapisu pozostanie o wysokiej dostępności dla odczytów. Jednak opłata za zapisywanie należy "włączyć automatycznej pracy awaryjnej" na koncie Cosmos usługi Cosmos DB do trybu failover, których to dotyczy regionu do innego regionu skojarzonego z kontem usługi Cosmos. Przełączenie w tryb failover miało miejsce w kolejności priorytet regionu, który został określony. Po pewnym czasie po objęte wpływem region jest wróci do trybu online, nie zostały zreplikowane dane w regionie zapisu dotyczy problem, w czasie awarii jest udostępniany za pośrednictwem konflikty źródła danych. Aplikacje mogą odczytywać konflikty źródła danych, rozwiąż konflikty, w oparciu o logikę specyficzną dla aplikacji i zapisywać zaktualizowane dane z powrotem do kontenera Cosmos zgodnie z potrzebami. Po odzyskaniu region zapisu wcześniej objęte wpływem staje się automatycznie dostępne jako region odczytu. Można wywołać ręcznej pracy awaryjnej i przywrócić objęte wpływem regionie, co region zapisu. Możesz tworzyć ręcznej pracy awaryjnej przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal.  

- Kont w wielu regionach z regionem zapisu pojedynczego podczas awarii regionem odczytu, co będzie pozostawać stale dostępnymi dla operacji odczytu i zapisu. Objęte wpływem region jest automatycznie rozłączany z regionu zapisu i zostanie oznaczona w trybie offline. Zestawy SDK Cosmos DB będzie przekierowywać odczytu wywołania do następnego dostępnego regionu na liście preferowany region. Jeśli żaden z regionów na liście preferowany region jest dostępny, wywołania automatycznie wrócić do bieżącego regionu zapisu. Żadne zmiany nie są wymagane w kodzie aplikacji do obsługi awarii w regionie odczytu. Po pewnym czasie gdy objęte wpływem region jest wróci do trybu online, wcześniej objęte wpływem odczytu z regionu zostanie automatycznie zsynchronizowana z bieżącego regionu zapisu i będzie można ponownie obsługująca operacje odczytu. Dalsze operacje odczytu są przekierowywane do regionu odzyskane bez wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy awaryjnej i ponowne przyłączanie regionu zakończyły się niepowodzeniem gwarancji spójności odczytu nadal uznawane przez usługi Cosmos DB.

- Konta w jednym regionie mogą utracić dostępność w przypadku regionalnej awarii. Zdecydowanie zachęcamy można skonfigurować co najmniej dwóch regionach (najlepiej, co najmniej dwa regiony zapisu) przy użyciu konta Cosmos w celu zapewnienia wysokiej dostępności przez cały czas.

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

- Aby upewnić się wysoką zapisu, a także dostępność do odczytu, skonfiguruj konto usługi Cosmos, na co najmniej dwóch regionach za pomocą zapisu wielu regionów. Ta konfiguracja zapewnia najlepsze dostępność, najniższe opóźnienia i skalowalność dla odczytów i zapisów wspieranej przez umowy SLA. Zobacz jak [Skonfiguruj konto usługi Cosmos z wieloma regionami zapisu](tutorial-global-distribution-sql-api.md).

- W przypadku kont Cosmos w wielu regionach, które są skonfigurowane z regionem zapisu jednego należy włączyć "automatyczne — praca awaryjna" przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal.  Po włączeniu automatycznej pracy awaryjnej, zawsze, gdy występuje regionalnej awarii, Cosmos DB zostanie automatycznie trybu failover Twoje konto.  

- Nawet w przypadku Twojego konta usługi Cosmos o wysokiej dostępności, aplikacja może nie być poprawnie zaprojektowana pozostaje o wysokiej dostępności. Aby zapewnić wysoką dostępność aplikacji end-to-end, okresowo wywołania "Ręczna praca awaryjna" przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal w ramach testowania aplikacji lub odzyskiwania po awarii (DR) zawarto bardziej szczegółowe. Aby dowiedzieć się więcej, zobacz zmiany regionalnych priorytetów dla konta usługi Cosmos).  

## <a name="next-steps"></a>Kolejne kroki

Następnie informacje na temat skalowania przepływności w następującym artykule:

* [Skalowanie przepływności](scaling-throughput.md)

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)

* [Skalowanie przepływności globalnie](scaling-throughput.md)

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)

* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)


