---
title: Wprowadzenie do usługi Azure Cosmos DB
description: Zapoznaj się z informacjami na temat usługi Azure Cosmos DB. Ta dostępna w skali światowej, wielomodelowa baza danych zapewnia małe opóźnienia, elastyczną skalowalność i wysoką dostępność oraz oferuje natywną obsługę danych NoSQL.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: overview
ms.date: 12/18/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 4a4bcf80eeff8700da37f5ea46353b1be6f2327b
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2018
ms.locfileid: "53809016"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB — Zapraszamy!

Dzisiejsze aplikacje muszą być bardzo dynamiczne i zawsze w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Aplikacje muszą reagować w czasie rzeczywistym na duże zmiany użycia w godzinach szczytu, przechowywać coraz większe ilości danych i udostępniać je użytkownikom w ciągu kilku milisekund.

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Usługa Azure Cosmos DB umożliwia elastyczne i niezależne skalowanie przepływności i przestrzeni dyskowej dla dowolnej liczby regionów geograficznych platformy Azure za pomocą jednego kliknięcia przycisku. Możesz elastycznie skalować przepływność i magazyn oraz korzystać z szybkiego dostępu do danych rzędu kilku milisekund przy użyciu ulubionego interfejsu API, m.in. SQL, MongoDB, Cassandra, Tables czy Gremlin. W odróżnieniu od innych usług baz danych usługa Cosmos DB zapewnia kompleksowe [umowy dotyczące poziomu usług](https://aka.ms/acdbsla) (SLA, Service Level Agreement) obejmujące gwarancje w zakresie przepływności, opóźnienia, dostępności i spójności.

Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB to dostępna w skali światowej usługa bazy danych firmy Microsoft z elastycznym skalowaniem, gwarantowanymi małymi opóźnieniami, pięcioma modelami spójności oraz kompleksowymi, gwarantowanymi umowami SLA](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="turnkey-global-distribution"></a>Gotowa do użycia dystrybucja globalna

Usługa Cosmos DB umożliwia tworzenie bardzo dynamicznych i wysoce dostępnych aplikacji na całym świecie. Usługa Cosmos DB jawnie replikuje dane wszędzie tam, gdzie są użytkownicy, dzięki czemu mogą oni wchodzić w interakcje z repliką danych znajdującą się najbliżej.

Usługa Cosmos DB umożliwia dodawanie i usuwanie regionów platformy Azure do konta usługi Cosmos w dowolnym momencie, wystarczy kliknąć przycisk. Usługa Cosmos DB będzie bezproblemowo replikować dane do wszystkich regionów skojarzonych z danym kontem usługi Cosmos, podczas gdy aplikacja będzie nadal wysoko dostępna dzięki międzyregionalnym możliwościom usługi.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [globalnej dystrybucji](distribute-data-globally.md).

### <a name="always-on"></a>Zawsze „Włączone”

Ze względu na ścisłą integrację z infrastrukturą platformy Azure i [jawną wielowzorcową replikację](global-dist-under-the-hood.md) usługa Cosmos DB zapewnia 99,999% [wysokiej dostępności](high-availability.md) zarówno dla operacji odczytu jak i zapisu. Usługa Cosmos DB zapewnia również możliwość programowego (lub za pomocą portalu) wywoływania regionalnego trybu failover konta usługi Cosmos. Ta funkcja pomaga zagwarantować, że jeśli baza danych usługi Cosmos automatycznie przejdzie po awarii w regionie w tryb failover, również reszta aplikacji przejdzie w tryb failover.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastyczne skalowanie przepływności i magazynu na całym świecie

Zaprojektowana pod kątem przezroczystego partycjonowania w poziomie i replikacji wielowzorcowej usługa Cosmos DB oferuje niespotykaną dotąd elastyczną skalowalność operacji odczytu i zapisu na całym świecie. Można elastycznie skalować w górę od tysięcy do setek milionów żądań na sekundę na całym świecie przy użyciu jednego wywołania interfejsu API i płacić tylko za przepływność i magazyn, które są potrzebne. Ta funkcja pomaga radzić sobie z niespodziewanymi wzrostami w obciążeniach bez konieczności nadmiernej aprowizacji dla szczytu. Zobacz informacje dotyczące [partycjonowania w usłudze Cosmos DB](partitioning-overview.md), [aprowizowanej przepływności na kontenerach i w bazach danych](set-throughput.md) oraz [globalnego skalowania aprowizowanej przepływności](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Gwarantowane małe opóźnienia na poziomie 99. percentyla, na całym świecie

Przy użyciu usługi Cosmos DB można tworzyć szybko reagujące aplikacje skalowane na całym świecie. Dzięki nowatorskiemu protokołowi replikacji wielowzorcowej oraz [zoptymalizowanemu pod kątem zapisu i niekorzystającemu z zatrzaśnięć aparatowi bazy danych](index-policy.md) usługa Cosmos DB gwarantuje opóźnienia mniejsze niż 10 ms dla operacji odczytu i zapisu (indeksowanych) na poziomie 99. percentyla. Ta funkcja umożliwia stałe pozyskiwanie danych i wyjątkowo szybkie żądania do natychmiast reagujących aplikacji.

### <a name="precisely-defined-multiple-consistency-choices"></a>Wiele dokładnie zdefiniowanych opcji spójności

Nie musisz już więcej zawierać ekstremalnych [kompromisów między spójnością, dostępnością, opóźnieniem i możliwościami programowania](consistency-levels-tradeoffs.md). Protokół replikacji wielowzorcowej usługi Cosmos DB został starannie zaprojektowany, aby oferować [pięć dokładnie zdefiniowanych opcji spójności](consistency-levels.md) — siła, powiązana nieaktualność, spójny prefiks, sesja i ostateczność — umożliwiających pracę z aplikacjami rozproszonymi z użyciem intuicyjnego modelu programowania o małym opóźnieniu i wysokiej dostępności.

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

Zachowanie synchronizacji schematów i indeksów bazy danych ze schematami aplikacji jest szczególnie trudne w przypadku aplikacji globalnie rozproszonych. Jednak w przypadku usługi Cosmos DB nie trzeba mieć do czynienia ze schematami lub indeksami. Aparat bazy danych jest całkowicie niezależny od schematów.  Ponieważ zarządzanie schematami i indeksami nie jest wymagane, nie trzeba się martwić o przerwy w działaniu aplikacji podczas migrowania schematów. Usługa Cosmos DB [automatycznie indeksuje wszystkie dane](index-policy.md) — schematy ani indeksy nie są wymagane — i szybko obsługuje zapytania.

### <a name="battle-tested-database-service"></a>Gruntownie przetestowana usługa bazy danych

Usługa Cosmos DB to podstawowa usługa na platformie Azure. Od prawie dekady usługa Cosmos DB jest używana przez wiele produktów firmy Microsoft do krytycznych aplikacji w skali globalnej, m.in. Skype, Xbox, Office 365, Azure i wielu innych. Już dziś Cosmos DB jest jedną z najszybciej rozwijających się usług na platformie Azure. Używa jej wielu klientów zewnętrznych i wiele aplikacji wymagających elastycznego skalowania i/lub gotowej replikacji wielu centrów danych/wielu regionów i wielu wzorców w celu uzyskania małych opóźnień i wysokiej dostępności zarówno odczytu jak i zapisu.

### <a name="ubiquitous-regional-presence"></a>Szeroka dostępność w regionach

Usługa Cosmos DB jest dostępna we wszystkich regionach platformy Azure na całym świecie, w tym w ponad 54 regionach w chmurze publicznej, Azure China 21Vianet, Azure Germany, Azure Government i Azure Government for Department of Defense (DoD). Zobacz temat [Cosmos DB’s regional presence](regional-presence.md) (Obecność usługi Cosmos DB w regionach).

### <a name="secure-by-default-and-enterprise-ready"></a>Gwarancja bezpieczeństwa i przeznaczenie dla przedsiębiorstw

Usługa Cosmos DB jest certyfikowana dla [szerokiej gamy standardów zgodności](compliance.md). Ponadto wszystkie dane w usłudze Cosmos DB są szyfrowane podczas magazynowania i w ruchu. Usługa Cosmos DB zapewnia autoryzację na poziomie wiersza i jest zgodna z rygorystycznymi standardami zabezpieczeń.

### <a name="significant-tco-savings"></a>Znaczne oszczędności całkowitego kosztu posiadania

Cosmos DB jest usługą w pełni zarządzaną, dlatego nie musisz już zarządzać złożonymi wdrożeniami wielu centrów danych oraz uaktualnieniami oprogramowania baz danych ani ich obsługiwać, płacić za obsługę, licencjonowanie lub operacje. Zobacz temat [Optimize cost with Cosmos DB](total-cost-ownership.md) (Optymalizacja kosztów za pomocą usługi Cosmos DB).

### <a name="industry-leading-comprehensive-slas"></a>Wiodące w branży kompleksowe umowy SLA

Usługa Cosmos DB jest pierwszą i jedyną usługą, która oferuje [wiodące w branży kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zapewniające wysoką dostępność przez 99,999% czasu, opóźnienie odczytu i zapisu w 99. percentylu, gwarantowaną przepływność i spójność.

### <a name="apache-spark--cosmos-db--operational-analytics-at-global-scale"></a>Apache Spark + Cosmos DB = analiza operacyjna w skali globalnej

Możesz uruchomić projekt [Spark](spark-connector.md) na danych przechowywanych w usłudze Cosmos DB. Ta funkcja umożliwia przeprowadzenie analizy operacyjnej o małych opóźnieniach w skali globalnej bez wywierania wpływu na obciążenia transakcyjne działające bezpośrednio w odniesieniu do usługi Cosmos DB.

### <a name="develop-applications-for-cosmos-db-using-popular-nosql-apis"></a>Tworzenie aplikacji dla usługi Cosmos DB przy użyciu popularnych interfejsów API NoSQL

Usługa Cosmos DB oferuje wachlarz interfejsów API do aktualizacji danych przechowywanych w bazie danych usługi Cosmos oraz wykonywania zapytań dla nich. Domyślnie [można używać języka SQL](how-to-sql-query.md) do aktualizacji danych w bazie danych Cosmos oraz do wykonywania zapytań.

Usługa Cosmos DB wdraża również bezpośrednio w usłudze protokoły przewodowe [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) oraz [Azure Table Storage](table-introduction.md). Umożliwia to skierowanie sterowników klienta (i narzędzi) dla często używanych interfejsów API NoSQL bezpośrednio do bazy danych usługi Cosmos. Dzięki obsłudze protokołów przewodowych często używanych interfejsów API usługi NoSQL usługa Cosmos DB umożliwia:

* Łatwe migrowanie aplikacji do usługi Cosmos DB przy jednoczesnym zachowaniu znacznej części logiki aplikacji.
* Zapewnienie, że aplikacja będzie nadal przenośna i niezależna od dostawcy i chmury.
* Uzyskanie wiodących w branży zabezpieczonych finansowo umów SLA dla wspólnych interfejsów API NoSQL. 
* Elastyczne skalowanie aprowizowanej przepływności i magazynu dla baz danych zgodnie z potrzebami i płatność tylko za przepływność i magazyn, które są potrzebne. Prowadzi to do znacznych oszczędności kosztów.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Rozwiązania, które korzystają z zalet usługi Azure Cosmos DB

Wszystkie [aplikacje internetowe, IoT i mobilne oraz gry](use-cases.md) wymagające obsługi ogromnych ilości danych, operacji odczytu i zapisu w skali [globalnej](distribute-data-globally.md) z czasem odpowiedzi bliskim rzeczywistemu dla różnych danych będą korzystać z zalet związanych z [gwarantowaną](https://azure.microsoft.com/support/legal/sla/cosmos-db/) wysoką dostępnością, wysoką przepływnością, małymi opóźnieniami i możliwościami dostosowania spójności, które są zapewniane przez usługę Azure Cosmos DB. Dowiedz się, jak można zastosować usługę Azure Cosmos DB do rozwiązań z zakresu [IoT i telematyki](use-cases.md#iot-and-telematics), [sprzedaży i marketingu](use-cases.md#retail-and-marketing), [gier](use-cases.md#gaming) oraz [aplikacji mobilnych i internetowych](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat możliwości [globalnej dystrybucji](distribute-data-globally.md) i [partycjonowania](partitioning-overview.md) usługi Cosmos DB.

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
