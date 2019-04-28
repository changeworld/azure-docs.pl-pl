---
title: Model cen usługi Azure Cosmos DB
description: W tym artykule opisano model cen usługi Azure Cosmos DB i jak upraszcza zarządzanie kosztami i planowanie kosztów.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: rimman
ms.openlocfilehash: 997a80ed1a8089c5255292f23bc5dacf8a6cb0e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059677"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Model cen w usłudze Azure Cosmos DB 

Model cen usługi Azure Cosmos DB upraszcza koszty zarządzania i planowania. Za pomocą usługi Azure Cosmos DB płacisz za aprowizowaną przepływność i Magazyn, które zostaną zużyte.

* **Aprowizowana przepływność**: Aprowizowana przepływność (nazywane również zarezerwowaną przepływność) gwarantuje wysoką wydajność w dowolnej skali. Należy określić przepływność (RU/s), czy potrzebujesz, i usługi Azure Cosmos DB przeznacza zasoby wymagane w celu zagwarantowania skonfigurowanej przepływności. Opłaty są naliczane godzinowo dla maksymalna aprowizowana przepływność dla danej godziny.

   > [!NOTE]
   > Ponieważ model aprowizowanej przepływności przeznacza zasoby z bazą danych lub kontenera, opłata wyniesie aprowizowanej przepływności nawet wtedy, gdy nie uruchamiania żadnych obciążeń.

* **Wykorzystany magazyn**: Opłaty są naliczane zgodnie ze stałą opłatą całkowitą ilość zajmowanego w magazynie (GB) dla danych i indeksów dla danej godziny.

Aprowizowana przepływność, określony jako [jednostek żądań](request-units.md) na sekundę (RU/s) służy do odczytu lub zapisu danych do baz danych lub kontenerów. Możesz [aprowizowanie przepływności bazy danych lub kontener](set-throughput.md). Oparte na potrzeby związane z obciążeniem, możesz skalować przepływność w górę/w dół w dowolnym momencie. Cennik usługi Azure Cosmos DB jest elastyczny i jest proporcjonalna do przepływności w bazie danych lub w kontenerze. Wartości minimalne, przepływności i przestrzeni dyskowej i zwiększa skalowania zapewniają pełną gamę ceny i elastyczność o szerokim zakresie funkcji do wszystkich segmentów odbiorców, od małej skali do kontenerów na dużą skalę. Każda baza danych lub kontener jest rozliczany godzinowo za aprowizowaną przepływność w jednostkach 100 RU/s przy co najmniej 400 RU/s, przepływność i Magazyn używane w GB. W odróżnieniu od aprowizowana przepływność magazynu jest rozliczana na podstawie użycia. Oznacza to, że nie trzeba każdy magazyn Rezerwuj z wyprzedzeniem. Opłaty są naliczane tylko za magazyn, które zostaną zużyte.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) i [informacje o rachunku Azure Cosmos DB](understand-your-bill.md).

Model cen w usłudze Azure Cosmos DB jest spójny w ramach wszystkich interfejsów API. Aby dowiedzieć się więcej, zobacz [jak usługi Azure Cosmos DB modelu cen jest opłacalna dla klientów](total-cost-ownership.md). Brak minimalnej przepustowości wymagane w bazie danych lub kontener, w celu zapewnienia umów SLA i można zwiększyć lub zmniejszyć aprowizowanej przepływności za 6 USD, dla każdego 100 jednostek RU/s.

Obecnie minimalna cena dla bazy danych i przepływności opartych na kontenerach wynosi 24 USD/miesiąc (zobacz [stronę z cennikiem usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) najnowszych informacji. Jeśli obciążenie korzysta z wielu kontenerów, można zoptymalizować dla kosztów przy użyciu poziomu przepływności bazy danych, ponieważ poziom przepływności bazy danych pozwala na korzystanie z dowolną liczbę kontenerów w bazie danych, udostępnianie informacji o przepływności między kontenerów. W poniższej tabeli podsumowano aprowizowaną przepływność i koszty dla różnych obiektów:

|**Jednostki**  | **Przepustowość minimalna i kosztów** |**Zwiększa skalowania i kosztów** |**Inicjowanie obsługi administracyjnej zakresu** |
|---------|---------|---------|-------|
|Database (Baza danych)    | 400 jednostek RU/s (24 USD/miesiąc)    | 100 jednostek RU/s (6 USD/miesiąc)   |Przepływność jest zarezerwowany dla bazy danych i jest współużytkowany przez kontenery w bazie danych |
|Kontener     | 400 jednostek RU/s (24 USD/miesiąc)    | 100 jednostek RU/s (6 USD/miesiąc)  |Przepływność jest zarezerwowany dla określonego kontenera |

Jak pokazano w poprzedniej tabeli, minimalna przepływność w usłudze Azure Cosmos DB zostanie uruchomiony w cenie 24 USD/miesiąc. W przypadku uruchomienia z minimalną przepustowość i skali wraz z upływem czasu w celu obsługi obciążeń produkcyjnych, koszty wzrosną sprawnie, z przyrostem równym 6 USD/miesiąc. Model cen w usłudze Azure Cosmos DB jest elastyczny i istnieje smooth wzrostu lub spadku ceny podczas skalowania w górę lub w dół.

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie 

Usługa Azure Cosmos DB oferuje kilka opcji programistów do niego bezpłatnie. Dostępne są następujące opcje:

* **Bezpłatne konto platformy Azure**: Platforma Azure oferuje [w warstwie bezpłatna](https://azure.microsoft.com/free/) daje 200 USD środków platformy Azure przez pierwsze 30 dni i ograniczoną ilość bezpłatnych usług przez 12 miesięcy. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](../billing/billing-avoid-charges-free-account.md). Usługa Azure Cosmos DB jest częścią bezpłatnego konta platformy Azure. Specjalnie dla usługi Azure Cosmos DB to bezpłatne konto oferuje 5 GB pamięci masowej i 400 jednostek żądania przepływności aprowizowane za cały rok. 

* **Wypróbuj bezpłatnie usługę Azure Cosmos DB**: Usługa Azure Cosmos DB oferuje ograniczonej czasowo środowisko przy użyciu Wypróbuj usługę Azure Cosmos DB dla bezpłatnych kont. Możesz utworzyć konto usługi Azure Cosmos DB, tworzenia bazy danych i kolekcji i uruchomić przykładową aplikację, korzystając z przewodników Szybki Start i samouczków. Przykładową aplikację można uruchomić bez subskrypcji na koncie platformy Azure lub przy użyciu karty kredytowej. [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) oferuje możliwość odnowić swojego konta usługi Azure Cosmos DB na jeden miesiąc, dowolną liczbę razy.

* **Emulator usługi Azure Cosmos DB**: Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programistycznych. Emulator jest oferowana bezpłatnie i zapewniające wysoką wierność do usługi w chmurze. Przy użyciu emulatora usługi Azure Cosmos DB, możesz opracowywać i testować aplikacje lokalnie, bez tworzenia subskrypcji platformy Azure ani generowania kosztów. Można tworzyć aplikacje przy użyciu emulatora usługi lokalnie przed przejściem do środowiska produkcyjnego. Po zakończeniu funkcjonalność aplikacji względem emulator możesz przełączyć się do korzystania z usługi Azure Cosmos DB w chmurze i znacząco zaoszczędzić na kosztach. Aby uzyskać więcej informacji na temat emulatora zobacz [przy użyciu usługi Azure Cosmos DB do tworzenia i testowania](local-emulator.md) artykuł, aby uzyskać więcej informacji.

## <a name="pricing-with-reserved-capacity"></a>Cennik za rezerwowanie pojemności

Usługa Azure Cosmos DB [wydajności rezerwowej](cosmos-db-reserved-capacity.md) pomaga zaoszczędzić płacąc wstępnie dla zasobów usługi Azure Cosmos DB za rok lub trzy lata. Można znacznie zmniejszyć koszty przy użyciu jednego roku lub trzech lat ponoszonych z góry zobowiązań i zapisać między 20 65% zniżki w porównaniu do normalnej ceny. Usługa Azure Cosmos DB zarezerwowane wydajność pomaga zmniejszyć koszty przez wstępnie płacenia za aprowizowaną przepływność (RU/s) na okres jednego roku lub trzech lat i Uzyskaj rabat na aprowizowaną przepływność. 

Rezerwowanie pojemności zniżki rozliczeń i nie ma wpływu na stan środowiska uruchomieniowego zasobów usługi Azure Cosmos DB. Rezerwowanie pojemności dostępnej spójnie wszystkie interfejsy API, która obejmuje bazy danych MongoDB, Cassandra, SQL, Gremlin, a tabele platformy Azure i we wszystkich regionach na całym świecie. Dowiedz się więcej o rezerwowanie pojemności w [przedpłaty dla zasobów usługi Azure Cosmos DB przy użyciu rezerwowanie pojemności](cosmos-db-reserved-capacity.md) artykuł i Kup zarezerwowane pojemność z [witryny Azure portal](https://portal.azure.com/).

## <a name="next-steps"></a>Kolejne kroki

Możesz dowiedzieć się więcej na temat optymalizowania kosztów dla zasobów usługi Azure Cosmos DB w następujących artykułach:

* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [informacje o rachunku Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów multiregionalne konta usługi Cosmos](optimize-cost-regions.md)
* Dowiedz się więcej o [wydajności rezerwowej usługi Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Dowiedz się więcej o [emulatora usługi Azure Cosmos DB](local-emulator.md)
