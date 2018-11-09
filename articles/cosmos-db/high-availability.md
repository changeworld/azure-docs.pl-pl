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
ms.openlocfilehash: 5dc43aa5b98f097bd8bdf927f40b2d3efc878d48
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283499"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Wysoka dostępność w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane we wszystkich regionach platformy Azure skojarzony z Twoim kontem Cosmos. Usługa cosmos DB wykorzystuje wiele warstw nadmiarowości danych, jak pokazano na poniższej ilustracji:

![Partycjonowanie zasobów](./media/high-availability/figure1.png) 

- Dane w kontenerach Cosmos w poziomie jest podzielona na partycje.

- W każdym regionie każdej partycji jest chroniony za pomocą wszystkie zapisy replikowane i trwałym zatwierdzeniu większością replik zestawu replik. Repliki są dystrybuowane na maksymalnie 10-20 domen błędów.

- Każda partycja we wszystkich regionach są replikowane. Każdy region zawiera wszystkie partycje dane kontenera Cosmos i mogą akceptować zapisów i obsługiwać operacje odczytu.  

Jeśli Twoje konta usługi Cosmos jest rozłożona na regiony platformy Azure N, będzie co najmniej N x 4 kopii wszystkich danych. Oprócz zapewnienia dostępu do danych z małymi opóźnieniami i skalowanie przepływności odczytu/zapisu między regionami skojarzonych z Twoim kontem Cosmos, również o większej liczbie regionów (N wyższej) zwiększa dostępność.  

## <a name="slas-for-availability"></a>Umowy SLA dla dostępności   

Jako globalnie rozproszonej bazy danych Cosmos DB zapewnia kompleksowe umowy SLA, które obejmują przepustowość, opóźnienie w 99. percentylu, spójności i wysokiej dostępności. W poniższej tabeli opisano gwarancje dotyczące wysokiej dostępności oferowanej przez Cosmos DB dla kont pojedynczych i wielu regionów. Wysoką dostępność należy skonfigurować konta Cosmos mieć wiele regionów zapisu.

|Typ operacji  | Pojedynczy region |Multiregionalne (zapisuje pojedynczy region)|Multiregionalne (zapisuje wielu regionów) |
|---------|---------|---------|-------|
|Zapisuje    | dostępność przez 99,99    |dostępność przez 99,99   |99,999|
|Czyta     | dostępność przez 99,99    |99,999  |99,999|

> [!NOTE]
> W praktyce dostępność rzeczywiste zapisu powiązana nieaktualność, sesja, spójny prefiks i spójność ostateczna modeli jest znacznie wyższa niż opublikowanych umowy SLA. Rzeczywiste dostępność do odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowanych umowy SLA.

## <a name="regional-outages"></a>Regionalnych przestojów

Regionalnej awarii nie są niczym niezwykłym, a usługi Azure Cosmos DB zapewnia, że baza danych jest zawsze dostępna. Poniższe szczegóły Przechwyć zachowanie usługi Cosmos DB podczas awarii, w zależności od konfiguracji konta usługi Cosmos: 

- Skonfigurowano regiony odczytu dla wielu kont w wielu regionach będą pozostawać stale dostępnymi dla operacji odczytu i zapisu. Wywoływania regionalnego trybu failover są natychmiastowe i nie wymaga żadnych zmian z aplikacji.

- Multiregionalne konta z regionem zapisu pojedynczego: podczas awarii region zapisu tych kont będzie pozostawać stale dostępnymi dla operacji odczytu. Jednak opłata za zapisywanie należy "włączyć automatyczny tryb failover" na Twoim koncie Cosmos, do których to dotyczy regionu do innego regionu skojarzonego z trybu failover. Przełączenie w tryb failover miało miejsce w kolejności priorytet regionu, który został określony. Po pewnym czasie po objęte wpływem region jest wróci do trybu online, niezreplikowane danych w regionie zapisu dotyczy problem, w czasie awarii jest udostępniany za pośrednictwem konflikty źródła danych. Aplikacje mogą odczytywać konflikty źródła danych, rozwiąż konflikty, w oparciu o logikę specyficzną dla aplikacji i zapisywać zaktualizowane dane z powrotem do kontenera Cosmos zgodnie z potrzebami. Po odzyskaniu region zapisu wcześniej objęte wpływem staje się automatycznie dostępne jako region odczytu. Można wywołać ręcznej pracy awaryjnej i przywrócić objęte wpływem regionie, co region zapisu. Ręczna praca awaryjna można zrobić za pomocą [wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#enable-manual-failover-for-your-cosmos-account).  

- Multiregionalne konta z regionem zapisu pojedynczego: podczas awarii regionem odczytu, te konta będzie pozostawać stale dostępnymi dla operacji odczytu i zapisu. Objęte wpływem region jest automatycznie rozłączany z regionu zapisu i zostanie oznaczona w trybie offline. Zestawy SDK Cosmos DB będzie przekierowywać odczytu wywołania do następnego dostępnego regionu na liście preferowany region. Jeśli żaden z regionów na liście preferowany region jest dostępny, wywołania automatycznie wrócić do bieżącego regionu zapisu. Żadne zmiany nie są wymagane w kodzie aplikacji do obsługi awarii w regionie odczytu. Po pewnym czasie gdy objęte wpływem region jest wróci do trybu online, wcześniej objęte wpływem odczytu z regionu zostanie automatycznie zsynchronizowana z bieżącego regionu zapisu i będzie można ponownie obsługiwać żądań odczytu. Dalsze operacje odczytu są przekierowywane do regionu odzyskane bez wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy awaryjnej i ponowne przyłączanie regionu zakończyły się niepowodzeniem gwarancji spójności odczytu nadal uznawane przez usługi Cosmos DB.

- Konta w jednym regionie mogą utracić dostępność w przypadku regionalnej awarii. Zaleca się skonfigurować co najmniej dwóch regionach (najlepiej, co najmniej dwa regiony zapisu) przy użyciu konta Cosmos w celu zapewnienia wysokiej dostępności przez cały czas.

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

- Aby upewnić się wysoką zapisu, a także dostępność do odczytu, skonfiguruj konto usługi Cosmos, na co najmniej dwóch regionach za pomocą zapisu wielu regionów. Ta konfiguracja zapewnia dostępność, najmniejszego opóźnienia i skalowalności, zarówno dla odczytuje i zapisuje je to jest poparte umowy SLA. Aby dowiedzieć się więcej, zobacz temat jak [Skonfiguruj konto usługi Cosmos z wieloma regionami zapisu](tutorial-global-distribution-sql-api.md).

- Dla konta usługi Cosmos wielu regionów, które są skonfigurowane z regionem zapisu pojedynczego [włączyć automatyczny tryb failover przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account). Po włączeniu automatycznej pracy awaryjnej, zawsze, gdy występuje regionalnej awarii, Cosmos DB zostanie automatycznie trybu failover Twoje konto.  

- Nawet w przypadku Twojego konta usługi Cosmos o wysokiej dostępności, aplikacja może nie być poprawnie zaprojektowana pozostaje o wysokiej dostępności. Aby zapewnić wysoką dostępność aplikacji end-to-end, okresowo wywoływać [ręcznej pracy awaryjnej przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal](how-to-manage-database-account.md#enable-manual-failover-for-your-cosmos-account), jako część testowania aplikacji lub odzyskiwania po awarii (DR) awarii. 

## <a name="next-steps"></a>Kolejne kroki

Następnie informacje na temat skalowania przepływności w następującym artykule:

* [Skalowanie przepływności](scaling-throughput.md)

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)

* [Skalowanie aprowizowanej przepływności globalnie](scaling-throughput.md)

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)

* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)


