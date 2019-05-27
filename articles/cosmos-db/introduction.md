---
title: Wprowadzenie do usługi Azure Cosmos DB
description: Zapoznaj się z informacjami na temat usługi Azure Cosmos DB. Ta dostępna w skali światowej, wielomodelowa baza danych zapewnia małe opóźnienia, elastyczną skalowalność i wysoką dostępność oraz oferuje natywną obsługę danych NoSQL.
author: rimman
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/20/2019
ms.author: rimman
ms.openlocfilehash: a338cec213b82d54b08f2a11a969ddecf72ea7e5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953903"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB — Zapraszamy!

Dzisiejsze aplikacje muszą być bardzo dynamiczne i zawsze w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Aplikacje muszą reagować w czasie rzeczywistym na duże zmiany użycia w godzinach szczytu, przechowywać coraz większe ilości danych i udostępniać je użytkownikom w ciągu kilku milisekund.

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Jednym kliknięciem przycisku usługi Cosmos DB umożliwia elastyczne i niezależne skalowanie przepływności i magazynu w dowolnej liczbie regionów platformy Azure na całym świecie. Można elastycznie skalować przepływność oraz Magazyn i korzystać z dostępu do danych szybkie, pojedynczych milisekund, za pomocą ulubionego interfejsu API w tym SQL, MongoDB, Cassandra, tabel lub języka Gremlin. Usługa cosmos DB zapewnia kompleksowe [umowy dotyczące poziomu usług](https://aka.ms/acdbsla) (SLA) dla przepływności, opóźnienia, dostępności i gwarancje spójności coś żadna inna usługa bazy danych oferuje.

Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB to dostępna w skali światowej usługa bazy danych firmy Microsoft z elastycznym skalowaniem, gwarantowanymi małymi opóźnieniami, pięcioma modelami spójności oraz kompleksowymi, gwarantowanymi umowami SLA](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="turnkey-global-distribution"></a>Gotowa do użycia dystrybucja globalna

Usługa Cosmos DB umożliwia tworzenie bardzo dynamicznych i wysoce dostępnych aplikacji na całym świecie. Usługa cosmos DB w sposób przezroczysty replikuje dane wszędzie tam, gdzie są Twoi użytkownicy, dzięki czemu użytkownicy mogą wchodzić w interakcje z repliką danych znajdujących się najbliżej nich.

Usługa Cosmos DB umożliwia dodawanie i usuwanie regionów platformy Azure do konta usługi Cosmos w dowolnym momencie, wystarczy kliknąć przycisk. Usługa cosmos DB bezproblemowo będą replikowane dane do wszystkich regionów skojarzonych z Twoim kontem Cosmos, nadal aplikacji o wysokiej dostępności dzięki do *multihosting* możliwości usługi. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [globalnej dystrybucji](distribute-data-globally.md).

### <a name="always-on"></a>Zawsze włączone

Ze względu na ścisłą integrację z infrastrukturą platformy Azure i [przezroczyste replikacji wielu wzorców](global-dist-under-the-hood.md), Cosmos DB zapewnia [wysoką dostępność przez 99,999%](high-availability.md) zarówno dla operacji odczytu i zapisu. Usługa Cosmos DB zapewnia również możliwość programowego (lub za pomocą portalu) wywoływania regionalnego trybu failover konta usługi Cosmos. Ta funkcja pomaga upewnić się, że aplikacja jest przeznaczona do pracy awaryjnej w przypadku regionalnej awarii.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastyczne skalowanie przepływności i magazynu na całym świecie

Zaprojektowana pod kątem przezroczystego partycjonowania w poziomie i replikacji wielowzorcowej usługa Cosmos DB oferuje niespotykaną dotąd elastyczną skalowalność operacji odczytu i zapisu na całym świecie. Można elastycznie skalować w górę od tysięcy do setek milionów żądań na sekundę na całym świecie przy użyciu jednego wywołania interfejsu API i płacić tylko za przepływność i magazyn, które są potrzebne. Ta funkcja pomaga radzić sobie z niespodziewanymi wzrostami w obciążeniach bez konieczności nadmiernej aprowizacji dla szczytu. Aby uzyskać więcej informacji, zobacz [partycjonowanie w usłudze Cosmos DB](partitioning-overview.md), [aprowizowaną przepływność na kontenerach i baz danych](set-throughput.md), i [globalnie skalowanie aprowizowanej przepływności](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Gwarantowane małe opóźnienia na poziomie 99. percentyla, na całym świecie

Przy użyciu usługi Cosmos DB można tworzyć szybko reagujące aplikacje skalowane na całym świecie. Z protokołu nowej replikacji wielu wzorców i niekorzystającemu z zatrzaśnięć i [zoptymalizowanej pod kątem zapisu bazy danych aparatu](index-policy.md), Cosmos DB gwarantuje opóźnienia mniejsze niż 10 ms przy odczycie w obu przypadkach odczytuje (indeksowanych) i zapisuje je w 99. percentylu, na całym świecie . Ta funkcja umożliwia stałe pozyskiwanie danych i wyjątkowo szybkie żądania do natychmiast reagujących aplikacji.

### <a name="precisely-defined-multiple-consistency-choices"></a>Wiele dokładnie zdefiniowanych opcji spójności

Jeśli tworzenie globalnie dystrybuowanych aplikacji w usłudze Cosmos DB, masz już się najdalej [kompromis między spójnością, dostępnością, opóźnieniem i przepływnością](consistency-levels-tradeoffs.md). Protokołu replikacji wielu wzorców usługi cosmos DB starannie zaprojektować oferują [pięć dobrze zdefiniowanych opcji spójności](consistency-levels.md) - *silne*, *powiązana nieaktualność*, *sesji*, *spójny prefiks*, i *ostatecznej* — intuicyjnego modelu programowania o małym opóźnieniu i wysokiej dostępności dla Twojego globalnie aplikacji rozproszonej.

### <a name="no-schema-or-index-management"></a>Bez zarządzania schematami lub indeksami

Zachowanie synchronizacji schematów i indeksów bazy danych ze schematami aplikacji jest szczególnie trudne w przypadku aplikacji globalnie rozproszonych. Za pomocą usługi Cosmos DB ma konieczności zarządzania schematami lub indeksami. Aparat bazy danych jest całkowicie niezależny od schematów.  Ponieważ zarządzanie schematami i indeksami nie jest wymagane, nie trzeba się martwić o przerwy w działaniu aplikacji podczas migrowania schematów. Usługa Cosmos DB [automatycznie indeksuje wszystkie dane](index-policy.md) i szybko obsługuje zapytania.

### <a name="battle-tested-database-service"></a>Gruntownie przetestowana usługa bazy danych

Usługa Cosmos DB to podstawowa usługa na platformie Azure. Przez prawie dekadę Cosmos DB został użyty przez wiele produktów firmy Microsoft dla krytycznych aplikacji w skali globalnej, w tym Skype, Xbox, usługi Office 365, Azure i wiele innych. Już dziś Cosmos DB jest jednym z najszybciej rozwijająca się usług na platformie Azure, które są używane przez wielu klientów zewnętrznych i aplikacji o krytycznym znaczeniu, które wymagają elastycznej skali, gotowa do użycia funkcja dystrybucji globalnej, replikacji wielu wzorców małe opóźnienia i wysoką dostępność w obu tych elementów Odczyty i zapisy.

### <a name="ubiquitous-regional-presence"></a>Szeroka dostępność w regionach

Usługa cosmos DB jest dostępna we wszystkich regionach platformy Azure na całym świecie, w tym 54 + regiony w chmurze publicznej, [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure (Niemcy), platforma Azure Government i platformy Azure Government dla Departamentu Obrony (DoD). Zobacz temat [Cosmos DB’s regional presence](regional-presence.md) (Obecność usługi Cosmos DB w regionach).

### <a name="secure-by-default-and-enterprise-ready"></a>Gwarancja bezpieczeństwa i przeznaczenie dla przedsiębiorstw

Usługa Cosmos DB jest certyfikowana dla [szerokiej gamy standardów zgodności](compliance.md). Ponadto wszystkie dane w usłudze Cosmos DB są szyfrowane podczas magazynowania i w ruchu. Usługa Cosmos DB zapewnia autoryzację na poziomie wiersza i jest zgodna z rygorystycznymi standardami zabezpieczeń.

### <a name="significant-tco-savings"></a>Znaczne oszczędności całkowitego kosztu posiadania

Ponieważ usługi Cosmos DB to w pełni zarządzana usługa, nie potrzebujesz już do zarządzania i obsługi złożonych wielu centrów danych wdrażania i uaktualniania, oprogramowania, bazy danych, płacisz z pomocy technicznej, licencjonowania lub operacje lub musisz aprowizować bazę danych pod kątem obciążenia szczytowego. Aby uzyskać więcej informacji, zobacz [optymalizacji kosztów za pomocą usługi Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Wiodące w branży kompleksowe umowy SLA

Usługa Cosmos DB jest pierwszą i jedyną usługą, która oferuje [wiodące w branży kompleksowe umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) zapewniające wysoką dostępność przez 99,999% czasu, opóźnienie odczytu i zapisu w 99. percentylu, gwarantowaną przepływność i spójność.

### <a name="globally-distributed-operational-analytics-with-spark"></a>Globalnie dystrybuowana analiza operacyjna przy użyciu platformy Spark

Możesz uruchomić projekt [Spark](spark-connector.md) bezpośrednio na danych przechowywanych w usłudze Cosmos DB. Ta funkcja umożliwia przeprowadzenie analizy operacyjnej o małych opóźnieniach w skali globalnej bez wywierania wpływu na obciążenia transakcyjne działające bezpośrednio w odniesieniu do usługi Cosmos DB. Aby uzyskać więcej informacji, zobacz [globalnie rozproszone analiza operacyjna](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-nosql-apis"></a>Tworzenie aplikacji na Cosmos DB przy użyciu popularnych interfejsów API NoSQL

Usługa cosmos DB umożliwia wybór interfejsów API do pracy z danych przechowywanych w bazie danych Cosmos. Domyślnie [SQL można używać](how-to-sql-query.md) (podstawowa interfejsu API) do wykonywania zapytań w bazie danych Cosmos. Usługa cosmos DB implementuje również interfejsy API dla [Cassandra](cassandra-introduction.md), [bazy danych MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) i [Azure Table Storage](table-introduction.md). Można wskazać sterowników klienta (i narzędzi) dla często używanych NoSQL (np. bazy danych MongoDB, Cassandra, Gremlin) bezpośrednio z bazą danych Cosmos. Dzięki obsłudze protokołów przewodowy najczęściej używanych interfejsów API usługi NoSQL, usługi Cosmos DB pozwala na:

* Łatwe migrowanie aplikacji do usługi Cosmos DB przy jednoczesnym zachowaniu znacznej części logiki aplikacji.
* Zapewnienie, że aplikacja będzie nadal przenośna i niezależna od dostawcy chmury.
* Pobierz w pełni zarządzana usługa w chmurze w branży wiodących, zabezpieczone finansowo umowy SLA dla wspólnych interfejsów API NoSQL. 
* Elastyczne skalowanie aprowizowanej przepływności i magazynu dla baz danych zgodnie z potrzebami i płatność tylko za przepływność i magazyn, które są potrzebne. Prowadzi to do znacznych oszczędności kosztów.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Rozwiązania, które korzystają z zalet usługi Azure Cosmos DB

Wszelkie [sieci web, mobilnych, gier i aplikacji IoT](use-cases.md) wymagające obsługi ogromnych ilości danych, operacje odczytu i zapisuje w [globalną skalę](distribute-data-globally.md) przy użyciu odpowiedzi bliskim rzeczywistemu czasy dla różnych danych będą mogli korzystać z rozwiązania Cosmos DB [gwarantowana wysoka dostępność](https://azure.microsoft.com/support/legal/sla/cosmos-db/), wysoką przepływnością, małymi opóźnieniami i możliwościami dostosowania spójności. Dowiedz się, jak można używać usługi Azure Cosmos DB do kompilowania rozwiązań z zakresu [IoT i telematyki](use-cases.md#iot-and-telematics), [sprzedaży i marketingu](use-cases.md#retail-and-marketing), [gier](use-cases.md#gaming) oraz [aplikacji mobilnych i internetowych](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi Cosmos DB podstawowe pojęcia dotyczące [kompleksowa dystrybucja globalna](distribute-data-globally.md) i [partycjonowanie](partitioning-overview.md) i [aprowizowanej przepływności](request-units.md).

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
