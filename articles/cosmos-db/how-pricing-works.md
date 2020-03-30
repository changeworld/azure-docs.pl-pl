---
title: Model cenowy usługi Azure Cosmos DB
description: W tym artykule opisano model cenowy usługi Azure Cosmos DB oraz sposób, w jaki upraszcza zarządzanie kosztami i planowanie kosztów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977536"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Model cen w usłudze Azure Cosmos DB 

Model cen usługi Azure Cosmos DB upraszcza zarządzanie kosztami i planowanie. W usłudze Azure Cosmos DB płacisz za aprowizowaną przepływność i ilość zużytego miejsca do magazynowania.

* **Aprowizowana przepływność:** Aprowizowana przepływność (nazywana również przepływnością zarezerwowaną) gwarantuje wysoką wydajność w dowolnej skali. Należy określić przepływność (RU/s), które są potrzebne, a usługa Azure Cosmos DB poświęca zasoby wymagane do zagwarantowania skonfigurowaną przepływność. Opłaty są naliczane co godzinę za maksymalną aprowizowaną przepływność dla danej godziny.

   > [!NOTE]
   > Ponieważ model aprowizowanej przepływności poświęca zasoby do kontenera lub bazy danych, zostanie naliczona opłata za aprowizowaną przepływność, nawet jeśli nie uruchomisz żadnych obciążeń.

* **Zużyty magazyn:** Opłaty są rozliczane zryczałtowaną stawką za całkowitą ilość magazynu (GB) zużywanej dla danych i indeksów dla danej godziny.

Aprowizowana przepływność, określona jako [jednostki żądań](request-units.md) na sekundę (RU/s), umożliwia odczytywanie lub zapisywanie danych w kontenerach lub bazach danych. Można [aprowizować przepływność w bazie danych lub kontenerze](set-throughput.md). W zależności od potrzeb obciążenia można skalować przepływność w górę/w dół w dowolnym momencie. Cennik usługi Azure Cosmos DB jest elastyczny i jest proporcjonalny do przepływności, którą można skonfigurować w bazie danych lub kontenerze. Minimalne wartości przepływności i magazynowania oraz przyrosty skali zapewniają pełny zakres widma cen i elastyczności dla wszystkich segmentów klientów, od małych kontenerów na dużą skalę po duże rozmiary. Każda baza danych lub kontener jest rozliczana co godzinę za przepływność aprowizowaną w jednostkach 100 jednostek RU/s, z co najmniej 400 jednostek RU/s i magazynem zużywanym w gb. W przeciwieństwie do aprowizowanej przepływności magazyn jest rozliczany na podstawie zużycia. Oznacza to, że nie musisz rezerwować żadnego miejsca z wyprzedzeniem. Naliczane są tylko za magazyn, który zużywasz.

Aby uzyskać więcej informacji, zobacz [stronę cennika usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) i [opis rachunku usługi Azure Cosmos DB](understand-your-bill.md).

Model cenowy w usłudze Azure Cosmos DB jest spójny we wszystkich interfejsach API. Aby dowiedzieć się więcej, zobacz [Jak model cenowy usługi Azure Cosmos DB jest opłacalny dla klientów.](total-cost-ownership.md) Istnieje minimalna przepływność wymagana w bazie danych lub kontenerze, aby zapewnić ławy SLA i można zwiększyć lub zmniejszyć aprowizowaną przepływność o 6 USD za każde 100 RU/s.

Obecnie minimalna cena zarówno dla bazy danych, jak i przepływności opartej na kontenerze wynosi 24 USD/miesiąc (najnowsze informacje można znaleźć na [stronie cennik usługi Azure Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) Jeśli obciążenie używa wielu kontenerów, można zoptymalizować pod kątem kosztów przy użyciu przepływności na poziomie bazy danych, ponieważ przepływność na poziomie bazy danych umożliwia dowolną liczbę kontenerów w bazie danych współużytkuje przepływność między kontenerami. W poniższej tabeli podsumowano aprowizowaną przepływność i koszty dla różnych jednostek:

|**Jednostka**  | **Minimalna przepustowość & koszt** |**Zwiększanie skali & koszt** |**Zakres inicjowania obsługi administracyjnej** |
|---------|---------|---------|-------|
|baza danych    | 400 RU/s ($24/miesiąc)    | 100 RU/s ($6/miesiąc)   |Przepływność jest zarezerwowana dla bazy danych i jest współużytkowana przez kontenery w bazie danych |
|Kontener     | 400 RU/s ($24/miesiąc)    | 100 RU/s ($6/miesiąc)  |Przepływność jest zarezerwowana dla określonego kontenera |

Jak pokazano w poprzedniej tabeli, minimalna przepływność w usłudze Azure Cosmos DB rozpoczyna się od ceny $24/month. Jeśli zaczniesz od minimalnej przepływności i skalujesz w górę w czasie, aby obsługiwać obciążenia produkcyjne, koszty wzrosną płynnie, w przyrostach $6/month. Model cenowy w usłudze Azure Cosmos DB jest elastyczny i występuje płynny wzrost lub spadek ceny podczas skalowania w górę lub w dół.

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie 

Usługa Azure Cosmos DB oferuje kilka opcji dla deweloperów do niego za darmo. Dostępne są następujące opcje:

* **Bezpłatne konto platformy Azure:** platforma Azure oferuje [bezpłatną warstwę,](https://azure.microsoft.com/free/) która zapewnia 200 USD w kredytach platformy Azure przez pierwsze 30 dni i ograniczoną ilość bezpłatnych usług przez 12 miesięcy. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Usługa Azure Cosmos DB jest częścią bezpłatnego konta platformy Azure. W szczególności dla usługi Azure Cosmos DB to bezpłatne konto oferuje 5 GB pamięci masowej i 400 procesorów administracyjnych aprowizowanej przepływności przez cały rok. 

* **Wypróbuj usługę Azure Cosmos DB za darmo:** usługa Azure Cosmos DB oferuje ograniczone czasowo środowisko przy użyciu usługi Azure Cosmos DB dla bezpłatnych kont. Możesz utworzyć konto usługi Azure Cosmos DB, utworzyć bazę danych i kolekcje oraz uruchomić przykładową aplikację przy użyciu przewodników Szybki start i samouczków. Możesz uruchomić przykładową aplikację bez subskrybowania konta platformy Azure lub przy użyciu karty kredytowej. [Wypróbuj usługę Azure Cosmos DB za darmo](https://azure.microsoft.com/try/cosmosdb/) oferuje usługę Azure Cosmos DB przez jeden miesiąc, z możliwością odnawiania konta dowolną liczbę razy.

* **Emulator usługi Azure Cosmos DB:** Emulator usługi Azure Cosmos DB udostępnia lokalne środowisko, które emuluje usługę Usługi Azure Cosmos DB do celów programisty. Emulator jest oferowany bezpłatnie i z wysoką wiernością usługi w chmurze. Za pomocą emulatora usługi Azure Cosmos DB można tworzyć i testować aplikacje lokalnie, bez tworzenia subskrypcji platformy Azure lub ponoszenia żadnych kosztów. Aplikacje można tworzyć przy użyciu emulatora lokalnie przed przejściem do produkcji. Po upewnieniu się z funkcjonalności aplikacji względem emulatora, można przełączyć się do korzystania z konta usługi Azure Cosmos DB w chmurze i znacznie zaoszczędzić na kosztach. Aby uzyskać więcej informacji na temat emulatora, zobacz [korzystanie z usługi Azure Cosmos DB do tworzenia i testowania](local-emulator.md) artykułu, aby uzyskać więcej informacji.

## <a name="pricing-with-reserved-capacity"></a>Ceny z pojemnością zarezerwowaną

[Pojemność zarezerwowana](cosmos-db-reserved-capacity.md) usługi Azure Cosmos DB pomaga zaoszczędzić pieniądze, płacąc z góry za zasoby usługi Azure Cosmos DB przez rok lub trzy lata. Możesz znacznie obniżyć koszty dzięki rocznym lub trzyletnim zobowiązaniom z góry i zaoszczędzić od 20 do 65% rabatów w porównaniu do regularnych cen. Pojemność zarezerwowana usługi Azure Cosmos DB pomaga obniżyć koszty, płacąc z góry za aprowizowaną przepływność (RU/s) przez okres jednego roku lub trzech lat i uzyskać rabat na aprowizowaną przepływność. 

Pojemność zarezerwowana zapewnia rabat na rachunkach i nie wpływa na stan czasu wykonywania zasobów usługi Azure Cosmos DB. Pojemność zarezerwowana jest stale dostępna dla wszystkich interfejsów API, w tym MongoDB, Cassandra, SQL, Gremlin i Tabel platformy Azure oraz wszystkich regionów na całym świecie. Więcej informacji na temat pojemności zarezerwowanej można uzyskać w [obszarze Przedpłata za zasoby usługi Azure Cosmos DB z artykułem o pojemności zarezerwowanej](cosmos-db-reserved-capacity.md) i kupić pojemność zarezerwowaną w [witrynie Azure.](https://portal.azure.com/)

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat optymalizacji kosztów zasobów usługi Azure Cosmos DB można dowiedzieć się więcej w następujących artykułach:

* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [opisie rachunku za usługę Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów kont cosmosu wieloregionowego](optimize-cost-regions.md)
* Dowiedz się więcej o [pojemności zarezerwowanej usługi Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Dowiedz się więcej o [emulatorze usługi Azure Cosmos DB](local-emulator.md)
