---
title: Wprowadzenie do usługi Azure Cosmos DB
description: Zapoznaj się z informacjami na temat usługi Azure Cosmos DB. Ta dostępna w skali światowej, wielomodelowa baza danych zapewnia małe opóźnienia, elastyczną skalowalność i wysoką dostępność oraz oferuje natywną obsługę danych NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 2a09d5af013e804f33327855fb7b9f2104bc225c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240394"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB — Zapraszamy!

Dzisiejsze aplikacje muszą być bardzo dynamiczne i zawsze w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Aplikacje muszą reagować w czasie rzeczywistym na duże zmiany użycia w godzinach szczytu, przechowywać coraz większe ilości danych i udostępniać je użytkownikom w ciągu kilku milisekund.

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Kliknięciem przycisku usługa Cosmos DB umożliwia elastyczne i niezależne skalowanie przepływności i magazynu w dowolnej liczbie regionów platformy Azure na całym świecie. Można elastycznie skalować przepływność i magazyn oraz korzystać z szybkiego, jednocyfrowego dostępu do danych w milisekundach przy użyciu ulubionego interfejsu API, w tym: SQL, MongoDB, Cassandra, Tables lub Gremlin. Usługa Cosmos DB zapewnia [kompleksowe umowy dotyczące poziomu usług](https://aka.ms/acdbsla) (SLA) dla zapewnienia przepływności, opóźnienia, dostępności i spójności, czego nie oferuje żadna inna usługa bazy danych.

Możesz [wypróbować usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) bez bezpłatnej subskrypcji platformy Azure i zobowiązań lub skorzystać z [bezpłatnej warstwy usługi Azure Cosmos DB,](optimize-dev-test.md#azure-cosmos-db-free-tier) aby uzyskać konto z pierwszymi 400 usługami RU/s i 5 GB wolnego miejsca.

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB za darmo](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB to dostępna w skali światowej usługa bazy danych firmy Microsoft z elastycznym skalowaniem, gwarantowanymi małymi opóźnieniami, pięcioma modelami spójności oraz kompleksowymi, gwarantowanymi umowami SLA](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="turnkey-global-distribution"></a>Gotowa do użycia dystrybucja globalna

Usługa Cosmos DB umożliwia tworzenie bardzo dynamicznych i wysoce dostępnych aplikacji na całym świecie. Usługa Cosmos DB w sposób przejrzysty replikuje dane wszędzie tam, gdzie znajdują się użytkownicy, dzięki czemu użytkownicy mogą wchodzić w interakcje z repliką danych, która jest im najbliższa.

Usługa Cosmos DB umożliwia dodawanie i usuwanie regionów platformy Azure do konta usługi Cosmos w dowolnym momencie, wystarczy kliknąć przycisk. Usługa Cosmos DB bezproblemowo replikuje dane do wszystkich regionów skojarzonych z kontem usługi Cosmos, podczas gdy aplikacja nadal jest wysoce dostępna dzięki możliwościom *multi-homing* usługi. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [globalnej dystrybucji](distribute-data-globally.md).

### <a name="always-on"></a>Zawsze włączone

Dzięki głębokiej integracji z infrastrukturą platformy Azure i [przezroczystej replikacji wielonarodowej](global-dist-under-the-hood.md)usługa Cosmos DB zapewnia [99,999% wysokiej dostępności](high-availability.md) zarówno odczytów, jak i zapisów. Usługa Cosmos DB zapewnia również możliwość programowego (lub za pomocą portalu) wywoływania regionalnego trybu failover konta usługi Cosmos. Ta funkcja pomaga upewnić się, że aplikacja jest przeznaczony do pracy awaryjnej w przypadku awarii regionalnej.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastyczne skalowanie przepływności i magazynu na całym świecie

Zaprojektowana pod kątem przezroczystego partycjonowania w poziomie i replikacji wielowzorcowej usługa Cosmos DB oferuje niespotykaną dotąd elastyczną skalowalność operacji odczytu i zapisu na całym świecie. Można elastycznie skalować w górę od tysięcy do setek milionów żądań na sekundę na całym świecie przy użyciu jednego wywołania interfejsu API i płacić tylko za przepływność i magazyn, które są potrzebne. Ta funkcja pomaga radzić sobie z niespodziewanymi wzrostami w obciążeniach bez konieczności nadmiernej aprowizacji dla szczytu. Aby uzyskać więcej informacji, zobacz [partycjonowanie w usłudze Cosmos DB](partitioning-overview.md), [aprowizowana przepływność w kontenerach i bazach danych](set-throughput.md)oraz [skalowanie aprowizowanej przepływności globalnie](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Gwarantowane małe opóźnienia na poziomie 99. percentyla, na całym świecie

Przy użyciu usługi Cosmos DB można tworzyć szybko reagujące aplikacje skalowane na całym świecie. Dzięki nowatorskiemu protokołowi replikacji wielopanom oraz silnikowi bazy danych bez zatrzasku i [zoptymalizowanemu pod kątem zapisu,](index-policy.md)usługa Cosmos DB gwarantuje mniej niż 10 ms opóźnień dla obu, odczytów (indeksowanych) i zapisuje na 99 percentylu, na całym świecie. Ta funkcja umożliwia stałe pozyskiwanie danych i wyjątkowo szybkie żądania do natychmiast reagujących aplikacji.

### <a name="precisely-defined-multiple-consistency-choices"></a>Wiele dokładnie zdefiniowanych opcji spójności

Podczas tworzenia aplikacji rozproszonych globalnie w usłudze Cosmos DB nie trzeba już wprowadzać ekstremalnych [kompromisów między spójnością, dostępnością, opóźnieniem i przepływnością.](consistency-levels-tradeoffs.md) Protokół replikacji wielu wzorcowych usługi Cosmos DB został starannie zaprojektowany, aby oferować [pięć dobrze zdefiniowanych opcji](consistency-levels.md) - *spójności, ograniczoną* *nieaktualność,* *sesję,* *spójny prefiks*i *ewentualny* — dla intuicyjnego modelu programowania o niskim opóźnieniu i wysokiej dostępności dla aplikacji rozproszonej globalnie.

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

Zachowanie synchronizacji schematów i indeksów bazy danych ze schematami aplikacji jest szczególnie trudne w przypadku aplikacji globalnie rozproszonych. Z usługi Cosmos DB, nie trzeba zajmować się schematu lub zarządzania indeksem. Aparat bazy danych jest całkowicie niezależny od schematów.  Ponieważ zarządzanie schematami i indeksami nie jest wymagane, nie trzeba się martwić o przerwy w działaniu aplikacji podczas migrowania schematów. Usługa Cosmos DB [automatycznie indeksuje wszystkie dane](index-policy.md) i szybko obsługuje zapytania.

### <a name="battle-tested-database-service"></a>Gruntownie przetestowana usługa bazy danych

Usługa Cosmos DB to podstawowa usługa na platformie Azure. Przez prawie dekadę usługa Cosmos DB była używana przez wiele produktów firmy Microsoft do aplikacji o znaczeniu krytycznym na skalę globalną, w tym Skype, Xbox, Office 365, Azure i wiele innych. Obecnie usługa Cosmos DB jest jedną z najszybciej rozwijających się usług na platformie Azure, używanych przez wielu klientów zewnętrznych i aplikacje o znaczeniu krytycznym, które wymagają elastycznej skali, dystrybucji globalnej pod klucz, replikacji wielopanomowej dla małych opóźnień i wysokiej dostępności obu odczytuje i pisze.

### <a name="ubiquitous-regional-presence"></a>Szeroka dostępność w regionach

Usługa Cosmos DB jest dostępna we wszystkich regionach platformy Azure na całym świecie, w tym w ponad 54 regionach w chmurze publicznej, [usłudze Azure China 21Vianet](https://www.azure.cn/en-us/), platformie Azure Germany, usłudze Azure Government i azure government for Department of Defense (DoD). Zobacz temat [Cosmos DB’s regional presence](regional-presence.md) (Obecność usługi Cosmos DB w regionach).

### <a name="secure-by-default-and-enterprise-ready"></a>Gwarancja bezpieczeństwa i przeznaczenie dla przedsiębiorstw

Usługa Cosmos DB jest certyfikowana dla [szerokiej gamy standardów zgodności](compliance.md). Ponadto wszystkie dane w usłudze Cosmos DB są szyfrowane podczas magazynowania i w ruchu. Usługa Cosmos DB zapewnia autoryzację na poziomie wiersza i jest zgodna z rygorystycznymi standardami zabezpieczeń.

### <a name="significant-tco-savings"></a>Znaczne oszczędności całkowitego kosztu posiadania

Ponieważ usługa Cosmos DB jest w pełni zarządzaną usługą, nie trzeba już zarządzać złożonymi wdrożeniami i uaktualnieniami wielu centrów danych i uaktualnień oprogramowania bazy danych, płacić za pomoc techniczną, licencjonowanie lub operacje ani aprowizować bazy danych dla szczytowego obciążenia. Aby uzyskać więcej informacji, zobacz [Optymalizowanie kosztów za pomocą usługi Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Wiodące w branży kompleksowe umowy SLA

Usługa Cosmos DB jest pierwszą i jedyną usługą, która oferuje [wiodące w branży kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zapewniające wysoką dostępność przez 99,999% czasu, opóźnienie odczytu i zapisu w 99. percentylu, gwarantowaną przepływność i spójność.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globalnie rozproszona analiza operacyjna i sztuczna inteligencja z natywnie wbudowaną platformą Apache Spark

Możesz uruchomić projekt [Spark](spark-connector.md) bezpośrednio na danych przechowywanych w usłudze Cosmos DB. Ta funkcja umożliwia przeprowadzenie analizy operacyjnej o małych opóźnieniach w skali globalnej bez wywierania wpływu na obciążenia transakcyjne działające bezpośrednio w odniesieniu do usługi Cosmos DB. Aby uzyskać więcej informacji, zobacz [Globalnie rozproszona analiza operacyjna](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Tworzenie aplikacji w usłudze Cosmos DB przy użyciu popularnych interfejsów API oprogramowania open source (OSS)

Usługa Cosmos DB oferuje wybór interfejsów API do pracy z danymi przechowywanymi w bazie danych usługi Cosmos. Domyślnie [można użyć sql](how-to-sql-query.md) (podstawowy interfejs API) do wykonywania zapytań bazy danych usługi Cosmos. Usługa Cosmos DB implementuje również interfejsy API dla [cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) i [Azure Table Storage](table-introduction.md). Sterowniki (i narzędzia) można skierować dla powszechnie używanego nosql (np. Obsługując protokoły przewodowe powszechnie używanych interfejsów API NoSQL, usługa Cosmos DB umożliwia:

* Łatwe migrowanie aplikacji do usługi Cosmos DB przy jednoczesnym zachowaniu znacznej części logiki aplikacji.
* Zapewnienie, że aplikacja będzie nadal przenośna i niezależna od dostawcy i chmury.
* Uzyskaj w pełni zarządzaną usługę w chmurze z wiodącymi w branży, wspieranymi finansowo sla dla typowych interfejsów API NoSQL. 
* Elastyczne skalowanie aprowizowanej przepływności i magazynu dla baz danych zgodnie z potrzebami i płatność tylko za przepływność i magazyn, które są potrzebne. Prowadzi to do znacznych oszczędności kosztów.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Rozwiązania, które korzystają z zalet usługi Azure Cosmos DB

Każda [aplikacja internetowa, mobilna, gamingowa i IoT,](use-cases.md) która musi obsługiwać ogromne ilości danych, odczytuje i zapisuje w [skali globalnej](distribute-data-globally.md) z niemal rzeczywistym czasem reakcji dla różnych danych, skorzysta z [gwarantowanej wysokiej dostępności](https://azure.microsoft.com/support/legal/sla/cosmos-db/)usługi Cosmos DB, wysokiej przepustowości, niskiego opóźnienia i przestrajalnej spójności. Dowiedz się, jak można używać usługi Azure Cosmos DB do kompilowania rozwiązań z zakresu [IoT i telematyki](use-cases.md#iot-and-telematics), [sprzedaży i marketingu](use-cases.md#retail-and-marketing), [gier](use-cases.md#gaming) oraz [aplikacji mobilnych i internetowych](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o podstawowych pojęciach usługi Cosmos DB [pod klucz globalnej dystrybucji](distribute-data-globally.md) i [partycjonowania](partitioning-overview.md) oraz [aprowizowanej przepływności.](request-units.md)

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
