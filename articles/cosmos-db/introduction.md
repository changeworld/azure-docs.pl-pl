---
title: Wprowadzenie do usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Zapoznaj się z informacjami na temat usługi Azure Cosmos DB. Ta dostępna w skali światowej, wielomodelowa baza danych zapewnia małe opóźnienia, elastyczną skalowalność i wysoką dostępność.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: dcc384fad0479c66211fe31e547ad7076150b58e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611232"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB — Zapraszamy!

Azure Cosmos DB to dostępna w skali światowej, wielomodelowa baza danych firmy Microsoft. Usługa Azure Cosmos DB umożliwia elastyczne i niezależne skalowanie przepływności i przestrzeni dyskowej dla dowolnej liczby regionów geograficznych platformy Azure za pomocą jednego kliknięcia przycisku. W odróżnieniu od wszelkich innych usług baz danych, oferuje ona gwarancje dotyczące przepływności, opóźnienia, dostępności i spójności dzięki kompleksowym [umowom dotyczącym poziomu usług](https://aka.ms/acdbsla) (SLA, Service Level Agreement). Możesz [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB to dostępna w skali światowej usługa bazy danych firmy Microsoft z elastycznym skalowaniem, gwarantowanymi małymi opóźnieniami, pięcioma modelami spójności oraz kompleksowymi, gwarantowanymi umowami SLA](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>Najważniejsze możliwości
Jako globalnie dystrybuowana, wielomodelowa usługa bazy danych usługa Azure Cosmos DB ułatwia tworzenie skalowalnych i szybko reagujących aplikacji w skali globalnej:

* **Gotowa do użytku dystrybucja globalna**
    * Możesz [dystrybuować dane](distribute-data-globally.md) w dowolnej liczbie [regionów platformy Azure](https://azure.microsoft.com/regions/) [jednym kliknięciem przycisku](tutorial-global-distribution-sql-api.md). Umożliwia to używanie Twoich danych tam, gdzie znajdują się użytkownicy, co zapewnia najniższe możliwe opóźnienie Twoim klientom. 
    * Za pomocą międzyregionalnych interfejsów API usługi Azure Cosmos DB aplikacja zawsze posiada informacje o tym, gdzie znajduje się najbliższy region, i wysyła żądania do najbliższego centrum danych. Wszystko to jest możliwe bez zmian konfiguracji. Wystarczy ustawić region zapisu i dowolną liczbę regionów odczytu, a pozostałe operacje będą obsługiwane za Ciebie.
    * W przypadku dodawania regionów do bazy danych Azure Cosmos DB i usuwania ich z niej aplikacja nie musi być ponownie wdrażana i jest stale wysoce dostępna dzięki możliwościom międzyregionalnych interfejsów API.

* **Wiele modeli danych i popularnych interfejsów API na potrzeby uzyskiwania dostępu do danych i wykonywania względem nich zapytań**
    * Model danych oparty na technologii atom-rekord-sekwencja (ARS, atom-record-sequence), na podstawie którego zbudowana jest usługi Azure Cosmos DB, natywnie obsługuje wiele modeli danych, między innymi rodziny modeli opartych na dokumentach, grafach, parach klucz-wartość i kolumnach.
    * Interfejsy API dla następujących modeli danych są obsługiwane przez zestawy SDK dostępne w wielu językach:
        * [Interfejs API usługi SQL](sql-api-introduction.md): aparat bazy danych JSON bez schematu z zaawansowanymi możliwościami wysyłania zapytań SQL.
        * [Interfejs API usługi MongoDB](mongodb-introduction.md): wysoce skalowalna *baza danych MongoDB jako usługa* obsługiwana przez platformę Azure Cosmos DB. Zgodny z istniejącymi bibliotekami, sterownikami, narzędziami i aplikacjami MongoDB.
        * [Interfejs API rozwiązania Cassandra](cassandra-introduction.md): globalnie dystrybuowane rozwiązanie Cassandra jako usługa obsługiwane przez platformę Azure Cosmos DB. Zgodny z istniejącymi bibliotekami, sterownikami, narzędziami i aplikacjami [Apache Cassandra](https://cassandra.apache.org/).
        * [Interfejs API programu Gremlin](graph-introduction.md): w pełni zarządzana, skalowalna w poziomie usługa bazy danych programu Graph, która ułatwia tworzenie i uruchamianie aplikacji działających z zestawami danych o wysokim stopniu połączenia, obsługującymi otwarte interfejsy API programu Graph (na podstawie [specyfikacji Apache TinkerPop](http://tinkerpop.apache.org/), Apache Gremlin).
        * [Interfejs API tabel](table-introduction.md): usługa bazy danych wartości klucza utworzona w celu zapewnienia funkcji premium (na przykład automatycznego indeksowania, gwarantowanego małego opóźnienia, globalnej dystrybucji) istniejącym aplikacjom usługi Azure Table Storage bez wprowadzania żadnych zmian w aplikacji.
        * Dodatkowe modele danych i interfejsy API będą dostępne wkrótce!

* **Elastycznie i niezależnie skalowana przepływność i przestrzeń dyskowa na żądanie, na całym świecie**
    * Łatwe skalowanie przepływności bazy danych z [sekundową](request-units.md) dokładnością i możliwość jej zmiany w dowolnym momencie. 
    * Skalowanie rozmiaru magazynu w sposób [przejrzysty i automatyczny](partition-data.md) w celu obsługi wymagań dotyczących rozmiaru teraz i w każdym innym momencie.

* **Tworzenie szybko reagujących aplikacji o kluczowym znaczeniu**
    * Usługa Azure Cosmos DB gwarantuje klientom całościowe małe opóźnienia na poziomie 99 percentyla. 
    * Dla typowego elementu o wielkości 1 KB usługa Cosmos DB gwarantuje całościowe opóźnienie operacji odczytu poniżej 10 ms oraz indeksowanego zapisu poniżej 15 ms na poziomie 99 percentyla w obrębie tego samego regionu świadczenia usługi Azure. Średnie opóźnienia są znacznie niższe (poniżej 5 ms).

* **Zapewnienie dostępności na poziomie „zawsze włączona”**
    * Umowa SLA gwarantująca dostępność na poziomie co najmniej 99,99% dla wszystkich kont bazy danych w obrębie jednego regionu, a także dostępność do odczytu na poziomie co najmniej 99,999% dla wszystkich kont bazy danych w wielu regionach.
    * Możliwość wdrażania w dowolnej liczbie [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions), aby uzyskać większą dostępność i lepszą wydajność.
    * Dynamicznie ustawiane priorytety regionów i [symulacja awarii](regional-failover.md) jednego lub kilku regionów z gwarancją utraty danych do testowania kompleksowej dostępności dla całej aplikacji (również poza bazą danych). 

* **Tworzenie aplikacji dystrybuowanych globalnie w odpowiedni sposób**
    * Pięć dobrze zdefiniowanych, praktycznych i intuicyjnych [modeli spójności](consistency-levels.md) zapewnia silną spójność, podobną do spójności SQL, oraz spójność ostateczną, podobną do luźnej spójności NoSQL (i wszystko pomiędzy nimi). 
  
* **Gwarancja zwrotu pieniędzy**
    * Wiodące w branży, zabezpieczone finansowo, kompleksowe [umowy dotyczące poziomu usług (SLA)](https://aka.ms/acdbsla) gwarantujące dostępność, czas oczekiwania, przepływność i spójność kluczowych danych. 

* **Brak potrzeby zarządzania schematami i indeksami bazy danych**
    * Błyskawiczne tworzenie iteracji schematu aplikacji bez martwienia się o zarządzanie schematem i/lub indeksem bazy danych.
    * Aparat bazy danych usługi Azure Cosmos DB jest w pełni niezależny od schematów — automatycznie indeksuje wszystkie dane, które pozyska, bez wymagania żadnych schematów ani indeksów, a także umożliwia wykonywanie błyskawicznych zapytań. 

* **Niski koszt posiadania**
    * Od pięciu do dziesięciu razy [bardziej ekonomicznie](https://aka.ms/cosmos-db-tco-paper) niż w przypadku rozwiązania niezarządzanego lub rozwiązania lokalnego NoSQL.
    * Trzy razy tańsze niż usługi DynamoDB AWS lub Google Spanner.

## <a name="capability-comparison"></a>Porównanie możliwości

Usługa Azure Cosmos DB zapewnia najlepsze możliwości tradycyjnych relacyjnych i nierelacyjnych baz danych.

| Możliwości | Relacyjne bazy danych   | Nierelacyjne bazy danych (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Dystrybucja globalna | Nie | Nie | Tak, gotowa do użycia dystrybucja w ponad 30 regionach z międzyregionalnymi interfejsami API|
| Skalowanie w poziomie | Nie | Yes | Tak, możliwe jest niezależne skalowanie magazynu i przepływności | 
| Gwarancje dot. opóźnienia | Nie | Yes | Tak, 99% odczytów trwa poniżej 10 ms, a 99% zapisów poniżej 15 ms | 
| Wysoka dostępność | Nie | Yes | Tak, usługa Azure Cosmos DB jest zawsze włączona, umożliwia dobrze zdefiniowane wymiany zgodne z modelem PACELC oraz oferuje opcje automatycznego i ręcznego przechodzenia w tryb failover|
| Model danych + interfejs API | Relacyjny + SQL | Wiele modeli + OSS API | Wiele modeli + SQL + OSS API (więcej wkrótce) |
| Umowy SLA | Yes | Nie | Tak, kompleksowe umowy SLA dotyczące opóźnienia, przepływności, spójności i dostępności |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Rozwiązania, które korzystają z zalet usługi Azure Cosmos DB

Wszystkie [aplikacje internetowe, IoT i mobilne oraz gry](use-cases.md) wymagające obsługi ogromnych ilości danych, operacji odczytu i zapisu w skali [globalnej](distribute-data-globally.md) z czasem odpowiedzi bliskim rzeczywistemu dla różnych danych będą korzystać z zalet związanych z [gwarantowaną](https://azure.microsoft.com/support/legal/sla/cosmos-db/) wysoką dostępnością, wysoką przepływnością, małymi opóźnieniami i możliwościami dostosowania spójności, które są zapewniane przez usługę Azure Cosmos DB. Dowiedz się, jak można zastosować usługę Azure Cosmos DB do rozwiązań z zakresu [IoT i telematyki](use-cases.md#iot-and-telematics), [sprzedaży i marketingu](use-cases.md#retail-and-marketing), [gier](use-cases.md#gaming) oraz [aplikacji mobilnych i internetowych](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Następne kroki
Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

* [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
* [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
* [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)
