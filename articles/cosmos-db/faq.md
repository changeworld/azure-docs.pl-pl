---
title: Azure DB rozwiązania Cosmos — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące bazy danych z rozwiązania Cosmos platformy Azure, usługa globalnie rozproszone i wiele modeli bazy danych. Więcej informacji na temat pojemności, poziomów wydajności i skalowania.
keywords: Pytania dotyczące bazy danych, często zadawane pytania, usługi documentdb, azure, platformy Microsoft azure
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: mimig
ms.openlocfilehash: 7039b87c80aecb837059d80087c97e8691da2988
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-cosmos-db-faq"></a>Często zadawane pytania dotyczące usługi Azure rozwiązania Cosmos bazy danych
## <a name="azure-cosmos-db-fundamentals"></a>Podstawowe informacje na temat usługi Azure DB rozwiązania Cosmos
### <a name="what-is-azure-cosmos-db"></a>Co to jest Azure DB rozwiązania Cosmos?
Azure DB rozwiązania Cosmos jest usługą replikowany globalnie, wielu modelu bazy danych, która zaawansowaną obsługę zapytań dane bez schematu, pomaga zapewnić oraz konfigurowalną i niezawodną wydajność i umożliwia szybkie opracowywanie. Jego wszystkich odbywa się za pośrednictwem zarządzana platforma, która nie jest obsługiwana przez możliwości i zasięg Microsoft Azure. 

Azure DB rozwiązania Cosmos jest odpowiednie rozwiązanie dla aplikacji sieci web, mobilnych, gier, i aplikacji IoT przewidywalnej przepływności, wysoką dostępność, małych opóźnień i model danych bez schematu są wymagania dotyczące klucza. Zapewnia elastyczność schematu i rozbudowane indeksowanie i zawiera obsługę transakcyjną wielu dokumentów dzięki zintegrowanemu środowisku JavaScript. 

Aby uzyskać więcej pytania bazy danych, odpowiedzi i instrukcje dotyczące instalowania i korzystania z tej usługi, zobacz [stronę dokumentacji bazy danych Azure rozwiązania Cosmos](https://docs.microsoft.com/azure/cosmos-db/).

### <a name="what-happened-to-the-documentdb-api"></a>Co się stało z interfejsem API usługi DocumentDB?

Interfejs API Azure rozwiązania Cosmos bazy danych usługi DocumentDB lub języka SQL (DocumentDB) interfejsu API ma teraz nazwę interfejsu API Azure rozwiązania Cosmos bazy danych SQL. Nie trzeba zmienić żadnych czynności, aby kontynuować uruchamianie aplikacji skompilowanej za pomocą interfejsu API usługi DocumentDB. Funkcjonalność jest taka sama.

Jeśli masz konto usługi DocumentDB API przed masz teraz konto interfejsu API SQL bez żadnych zmian rozliczeniowego. 

### <a name="what-happened-to-azure-documentdb-as-a-service"></a>Co się stało z usługi Azure DocumentDB jako usługę?

Usługa Azure DocumentDB jest obecnie częścią usługi Azure DB rozwiązania Cosmos i w sytuacji, w formie interfejsu API SQL. Aplikacje, które utworzono przy użyciu usługi Azure DocumentDB zostanie uruchomiony bez wprowadzania żadnych zmian interfejsu API usługi Azure rozwiązania Cosmos bazy danych SQL. Ponadto bazy danych rozwiązania Cosmos Azure obsługuje interfejs API programu Graph, tabeli interfejsu API, bazy danych MongoDB interfejsu API i Cassandra interfejsu API (wersja zapoznawcza).

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jakie są typowe zastosowania dla bazy danych rozwiązania Cosmos Azure?
Azure DB rozwiązania Cosmos jest dobrym rozwiązaniem w przypadku nowych sieci web, mobilnych, gier, i ważne jest, gdzie skalowania automatycznego, przewidywalną wydajność, szybkie kolejność milisekund czasy odpowiedzi oraz możliwość wykonywania kwerend danych bez schematu aplikacji IoT. Azure DB rozwiązania Cosmos pozwala na szybkie opracowywanie i obsługę ciągłej iteracji modeli danych aplikacji. Zawartość wygenerowaną przez użytkowników i dane aplikacji są [typowe przypadki użycia bazy danych Azure rozwiązania Cosmos](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak bazy danych rozwiązania Cosmos Azure oferuje przewidywalną wydajność?
A [jednostki żądania](request-units.md) (RU) to miara przepływności w usłudze Azure DB rozwiązania Cosmos. Przepływność 1 RU odpowiada przepływności pobierania dokumentu 1 KB. Każdej operacji w usłudze Azure DB rozwiązania Cosmos, w tym odczytów, zapisy zapytania SQL i wykonywaniem procedur składowanych, ma wartość RU deterministyczna, która jest oparta na przepływności wymaganej do ukończenia tej operacji. Zamiast planowania procesora CPU, we/wy i pamięci oraz ich wpływ na przepływność aplikacji, można traktować pod względem jednej miary RU.

Istnieje możliwość rezerwowania każdego kontenera Azure DB rozwiązania Cosmos z aprowizowaną przepływnością wyrażoną jako RUs przepływności na sekundę. W przypadku aplikacji o dowolnej skali testu wydajności poszczególnych żądań w celu pomiaru ich wartości RU i udostępnić kontenera do obsługi całkowitej liczby jednostek żądania dla wszystkich żądań. Można także skalowanie w górę lub skali kontenera na przepływność rozwijających się potrzeb aplikacji. Aby uzyskać więcej informacji na temat jednostek żądania i określania z kontenera musi zobacz [szacowanie potrzeb w zakresie przepustowości](request-units.md#estimating-throughput-needs) , a następnie spróbuj [Kalkulator przepływności](https://www.documentdb.com/capacityplanner). Termin *kontenera* odnosi się tutaj do odwołuje się do kolekcji interfejsu API SQL, wykres interfejsu API programu Graph, kolekcji bazy danych MongoDB interfejsu API i interfejsu API tabeli tabeli. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak usługa Azure DB rozwiązania Cosmos obsługuje różne modele danych, takie jak klucz/wartość, kolumnowy, dokumentów i wykres

Klucz/wartość, tabela kolumnowy, dokumentów i danych wykresu modele są wszystkie obsługiwane z powodu ARS (atomami, rekordów i sekwencji) projektowania tej bazy danych rozwiązania Cosmos Azure jest wbudowany w program. Atomami, rekordów i sekwencji można łatwo zamapowane i zaprojektowana różne modele danych. Interfejsy API do podzbioru modeli są dostępne prawa teraz (SQL, bazy danych MongoDB, tabeli i interfejsów API Graph) i inni użytkownicy specyficzne dla modeli dodatkowe dane będą dostępne w przyszłości.

Azure DB rozwiązania Cosmos ma schematu o niesprecyzowanym aparatu indeksowania może automatycznie indeksowania wszystkie dane, które go wysyła strumień bez żadnego schematu lub indeksów pomocniczych od dewelopera. Aparat zależy od zestawu układów logicznych indeksu (odwrócony, kolumnowy, drzewo), które oddziel układu magazynu z indeksu i podsystemy przetwarzania zapytań. Rozwiązania cosmos bazy danych ma również możliwość obsługi zestawu danych przesyłanych w sieci protokołów i interfejsów API w sposób extensible i efektywnie tłumaczyć je do modelu danych core (1) i układów logicznych indeksu (2) co jednoznacznie może obsługiwać wiele modeli danych natywnie.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure rozwiązania Cosmos DB HIPAA jest zgodne?
Tak, bazy danych Azure rozwiązania Cosmos jest zgodny z ustawą HIPAA. Ustawa HIPAA określa wymogi dotyczące wykorzystywania, ujawniania i ochrony informacji o zdrowiu umożliwiających indywidualną identyfikację osoby. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jakie są limity magazynu bazy danych rozwiązania Cosmos Azure?
Nie ma żadnego limitu całkowitej ilości danych, które kontener może przechowywać w usłudze Azure DB rozwiązania Cosmos.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jakie są ograniczenia przepływności bazy danych rozwiązania Cosmos Azure?
Nie ma żadnego limitu całkowitej przepływności obsługujące przez kontener w usłudze Azure DB rozwiązania Cosmos. Klucza będzie rozłożyć obciążenie przybliżeniu równomiernie między wystarczająco dużą liczbę kluczy partycji.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Ile kosztuje bazy danych Azure rozwiązania Cosmos
Aby uzyskać więcej informacji, zapoznaj się [Azure DB rozwiązania Cosmos szczegóły cennika](https://azure.microsoft.com/pricing/details/cosmos-db/) strony. Azure opłaty za użycie rozwiązania Cosmos bazy danych są określane przez liczbę kontenerów elastycznie, liczbę godzin kontenery były w trybie online, i udostępnionej przepływności dla każdego kontenera. Termin *kontenery* odnosi się tutaj do kolekcji interfejsu API SQL, wykres interfejsu API programu Graph, kolekcji bazy danych MongoDB interfejsu API i tabele tabeli interfejsu API. 

### <a name="is-a-free-account-available"></a>Jest dostępne bezpłatne konto?
Tak, można założyć konto ograniczone czasowo za darmo, bez zobowiązań. Aby utworzyć konto, odwiedź stronę [bezpłatnie spróbuj Azure DB rozwiązania Cosmos](https://azure.microsoft.com/try/cosmosdb/) lub Dowiedz się więcej w [spróbuj Azure rozwiązania Cosmos DB — często zadawane pytania](#try-cosmos-db).

Jeśli jesteś nowym użytkownikiem usługi Azure, możesz zarejestrować się w celu [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/), które zapewnia 30 dni i i środki na wypróbowanie usług platformy Azure. Jeśli masz subskrypcję programu Visual Studio, przysługuje Ci również [kredytów systemu Azure w warstwie bezpłatna](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) na korzystanie z dowolnej usługi Azure. 

Można również użyć [Azure rozwiązania Cosmos DB emulatora](local-emulator.md) umożliwiające opracowanie i przetestowanie aplikacji lokalnie przez bezpłatne, bez tworzenia subskrypcji platformy Azure. Po zakończeniu jak aplikacja działa w emulatorze DB rozwiązania Cosmos Azure, możesz przełączyć się do korzystania z konta bazy danych Azure rozwiązania Cosmos w chmurze.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak można uzyskać dodatkową pomoc dotyczącą bazy danych rozwiązania Cosmos Azure?

Aby zadać pytania techniczne, możesz post do jednej z tych dwóch pytania i odpowiedzi na forach:
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Przepełnienie stosu](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Przepełnienie stosu jest najlepsze dla programowania pytania. Upewnij się, że jest to pytanie [na temat](https://stackoverflow.com/help/on-topic) i [zawierają tyle szczegółowe informacje, jak to możliwe, co na pytanie, wyczyść i którą można odpowiedzieć](https://stackoverflow.com/help/how-to-ask). 

Aby poprosić o nowe funkcje, Utwórz nowe żądanie na [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Aby rozwiązać problem z Twoim kontem, pliku [żądania obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure.

Inne pytania można przesłać do zespołu w [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); jednak nie jest to alias pomocy technicznej. 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Spróbuj subskrypcji bazy danych Azure rozwiązania Cosmos

Można teraz korzystać z obsługi bazy danych Azure rozwiązania Cosmos ograniczone czasowo, bez subskrypcji, bezpłatnie i zobowiązań. Aby zarejestrować subskrypcję spróbuj rozwiązania Cosmos bazy danych Azure, przejdź do [bezpłatnie spróbuj Azure DB rozwiązania Cosmos](https://azure.microsoft.com/try/cosmosdb/). Ta subskrypcja jest oddzielony od [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/free/)i mogą być używane razem z bezpłatnej wersji próbnej platformy Azure lub Azure płatnej subskrypcji. 

Spróbuj bazy danych Azure rozwiązania Cosmos subskrypcje są wyświetlane w portalu Azure obok innych subskrypcji skojarzonych z identyfikatorem użytkownika 

Do subskrypcji spróbuj rozwiązania Cosmos bazy danych Azure mają zastosowanie następujące warunki:

* Jeden kontener na subskrypcję dla konta programu SQL, Gremlin (interfejs API programu Graph) i tabeli.
* Maksymalnie 3 kolekcje na subskrypcję dla konta bazy danych MongoDB.
* 10 GB pojemności.
* Globalne replikacja jest dostępna w następujących [regiony platformy Azure](https://azure.microsoft.com/regions/): środkowe stany USA, Europa Północna, Europa i Azja południowo-wschodnia
* Maksymalna przepustowość 5 K RU/s.
* Subskrypcji wygaśnie po 24 godzinach i może zostać rozszerzony do maksymalnie 48 godzin.
* Nie można utworzyć biletów pomocy technicznej platformy Azure dla kont spróbuj rozwiązania Cosmos bazy danych platformy Azure; jednak obsługiwane dla subskrybentów z istniejących plany pomocy technicznej. 

## <a name="set-up-azure-cosmos-db"></a>Konfigurowanie bazy danych Azure rozwiązania Cosmos
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak tworzyć konta dla bazy danych rozwiązania Cosmos Azure?
Azure DB rozwiązania Cosmos jest dostępne w portalu Azure. Najpierw Utwórz subskrypcję platformy Azure. Po zarejestrowaniu się interfejsu API SQL, interfejsu API programu Graph, tabela interfejsu API, bazy danych MongoDB interfejsu API lub konto Cassandra interfejsu API można dodać do subskrypcji platformy Azure.

### <a name="what-is-a-master-key"></a>Co to jest klucz główny?
Klucz główny jest tokenem zabezpieczającym dostęp do wszystkich zasobów dla konta. Osoby, które mają klucz odczytu i zapisu dostęp do wszystkich zasobów w ramach konta bazy danych. Podczas dystrybucji kluczy głównych należy zachować ostrożność. Podstawowy klucz główny i pomocniczy klucz główny są dostępne na **klucze** bloku [portalu Azure][azure-portal]. Aby uzyskać więcej informacji o kluczach, zobacz [wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Co to są regionów, które można ustawić PreferredLocations? 
Wartość PreferredLocations można ustawić dla każdego regiony platformy Azure, w których są dostępne rozwiązania Cosmos bazy danych. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Czy jest coś I należy zwrócić uwagę podczas dystrybucję danych na świecie za pośrednictwem centrach danych platformy Azure? 
Azure DB rozwiązania Cosmos jest obecny we wszystkich regionach platformy Azure, jak określono w [regiony platformy Azure](https://azure.microsoft.com/regions/) strony. Co nowego centrum danych, ponieważ jest usługi podstawowej, ma obecności bazy danych Azure rozwiązania Cosmos. 

Po ustawieniu regionu, należy pamiętać, że bazy danych Azure rozwiązania Cosmos szanuje chmur suwerenne i dla instytucji rządowych. Oznacza to jeśli tworzysz konto w regionie suwerennych nie może replikować poza tym suwerennych regionie. Podobnie nie można włączyć replikacji w innych lokalizacjach suwerennych poza konta. 

## <a name="develop-against-the-sql-api"></a>Tworzenie interfejsu API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak rozpocząć tworzenie oprogramowania dla interfejsu API SQL?
Najpierw musisz zarejestrować subskrypcji platformy Azure. Po utworzeniu konta dla subskrypcji platformy Azure, możesz dodać kontener interfejsu API SQL do subskrypcji platformy Azure. Aby uzyskać instrukcje dotyczące dodawania konta bazy danych rozwiązania Cosmos platformy Azure, zobacz [Tworzenie konta bazy danych Azure DB rozwiązania Cosmos](create-sql-api-dotnet.md#create-account). 

[Zestawy SDK](sql-api-sdk-dotnet.md) są dostępne dla platformy .NET, Python, Node.js, JavaScript i Java. Deweloperzy mogą również używać [interfejsy API RESTful protokołu HTTP](/rest/api/cosmos-db/) do interakcji z zasobami Azure DB rozwiązania Cosmos z różnych platform i języków.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Można uzyskać dostęp do niektórych gotowe próbek, aby utworzyć?
Przykłady dotyczące interfejsu API SQL [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), i [Python](sql-api-python-samples.md) zestawów SDK są dostępne w serwisie GitHub.


### <a name="does-the-sql-api-database-support-schema-free-data"></a>Baza danych SQL API obsługuje dane bez schematu?
Tak, interfejsu API SQL umożliwia aplikacjom przechowywanie dowolnych dokumentów JSON bez definicji schematu lub wskazówek. Dane są natychmiast dostępne dla zapytania dzięki interfejsowi zapytań Azure rozwiązania Cosmos bazy danych SQL.  

### <a name="does-the-sql-api-support-acid-transactions"></a>Interfejsu API SQL obsługuje transakcje ACID?
Tak, interfejsu API SQL obsługuje transakcje dla wielu dokumentów wyrażone jako procedury składowane JavaScript i wyzwalaczy. Transakcje są ograniczone do jednej partycji w ramach każdej kolekcji i wykonywane przy użyciu semantyki ACID jako "wszystkie lub żadne," odizolowana od innego współbieżnie wykonywanego kodu i żądań użytkownika. Jeśli istnieją wyjątki zgłaszane przez wykonanie kodu aplikacji JavaScript po stronie serwera, cała transakcja zostanie wycofana. Aby uzyskać więcej informacji na temat transakcji, zobacz [bazy danych programu transakcji](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Co to jest kolekcja?
Kolekcja jest grupą dokumentów i ich skojarzonej logiki aplikacji JavaScript. Kolekcja to płatna jednostka, której [koszt](performance-levels.md) jest określana przez przepływność i użyto magazynu. Kolekcje mogą znajdować się na partycji lub serwerów i mogą być skalowane do obsługi praktycznie nieograniczonej ilości magazynu lub przepływności.

Kolekcje są również jednostkami rozliczeniowymi dla bazy danych Azure rozwiązania Cosmos. Każda kolekcja jest rozliczane godzinowo, oparta na udostępnionej przepływności i używać miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure rozwiązania Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Jak utworzyć bazę danych?
Bazy danych można tworzyć przy użyciu [portalu Azure](https://portal.azure.com), zgodnie z opisem w [dodania kolekcji](create-sql-api-dotnet.md#create-collection), jednego z [zestawów SDK DB rozwiązania Cosmos Azure](sql-api-sdk-dotnet.md), lub [interfejsów API REST](/rest/api/cosmos-db/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak skonfigurować użytkowników i uprawnienia?
Można utworzyć użytkowników i uprawnień przy użyciu jednej z [zestawów SDK interfejsu API DB rozwiązania Cosmos](sql-api-sdk-dotnet.md) lub [interfejsów API REST](/rest/api/cosmos-db/).  

### <a name="does-the-sql-api-support-sql"></a>Interfejsu API SQL obsługuje SQL?
Język zapytań SQL, które są obsługiwane przez interfejs API SQL konta jest rozszerzonym podzbiorem funkcji zapytań, który jest obsługiwany przez program SQL Server. Języka zapytań usługi Azure rozwiązania Cosmos bazy danych SQL zawiera sformatowanego operatorów hierarchicznej i rozszerzalność dzięki funkcji oparte na języku JavaScript, zdefiniowane przez użytkownika (UDF). Gramatyka JSON umożliwia modelowanie dokumentów JSON jako drzewa z etykietami węzły, które są używane zarówno przez techniki automatycznego indeksowania bazy danych Azure rozwiązania Cosmos i dialekt zapytań SQL Azure DB rozwiązania Cosmos. Aby uzyskać informacji dotyczących używania gramatyki SQL, zobacz [zapytania SQL] [ query] artykułu.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Interfejsu API SQL obsługuje funkcje agregacji SQL?
Interfejs API SQL obsługuje agregacji o małych opóźnieniach na dowolnym poziomie za pomocą funkcji agregujących `COUNT`, `MIN`, `MAX`, `AVG`, i `SUM` za pośrednictwem gramatyki SQL. Aby uzyskać więcej informacji, zobacz [funkcje agregujące](sql-api-sql-query.md#Aggregates).

### <a name="how-does-the-sql-api-provide-concurrency"></a>W jaki sposób interfejsu API SQL zapewnia współbieżność?
Interfejs API SQL obsługuje optymistycznej współbieżności sterowanie Współbieżnością za za pomocą tagów jednostki HTTP lub elementów ETag. Każdy zasób interfejsu API SQL ma element ETag i element ETag jest ustawiony na serwerze każdej aktualizacji dokumentu. Nagłówek ETag i bieżącą wartość są uwzględnione w wszystkie wiadomości odpowiedzi. Elementy etag może służyć nagłówka If-Match, aby serwer mógł zdecydować, czy można zaktualizować zasobu. Wartość If-Match jest wartość ETag do porównania. Jeśli wartość ETag odpowiada serwera wartość ETag, zasób jest aktualizowana. Jeśli element ETag nie jest już aktualny, serwer odrzuca operację, podając "HTTP 412 niepowodzenie warunku wstępnego" Kod odpowiedzi. Następnie klient pobiera ponownie zasobów w celu uzyskania bieżącej wartości ETag dla zasobu. Ponadto elementy etag można nagłówka If-None-Match do określenia, czy ponownie pobrać zasobu jest wymagana.

Aby użyć optymistycznej współbieżności w programie .NET, użyj [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) klasy. Przykładowy .NET [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) w przykładowym DocumentManagement w witrynie GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak wykonywać transakcje w interfejsie API SQL?
Interfejs API SQL obsługuje transakcje zintegrowane z językiem za pomocą procedur składowanych JavaScript i wyzwalaczy. Wszystkie operacje bazy danych wewnątrz skryptów są wykonywane w ramach izolacji migawki. Jeśli jest to kolekcja jednej partycji, obejmuje wykonanie do kolekcji. Jeśli kolekcja jest podzielona na partycje, wykonanie obejmuje dokumentów z taką samą wartość klucza partycji w kolekcji. Migawka wersji dokumentów (elementy ETag) jest wykonywana na początku transakcji i zatwierdzana tylko wtedy, gdy działanie skryptu zakończy się pomyślnie. Jeśli kod JavaScript zwraca błąd, transakcja zostaje wycofana. Aby uzyskać więcej informacji, zobacz [programowania w języku JavaScript po stronie serwera dla bazy danych Azure rozwiązania Cosmos](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak można I wstawiania zbiorczego dokumenty w bazie danych rozwiązania Cosmos?
Użytkownik może wstawiania zbiorczego dokumentów do bazy danych Azure rozwiązania Cosmos w jeden z dwóch sposobów:

* Narzędzie migracji danych, zgodnie z opisem w [narzędzie migracji bazy danych dla bazy danych Azure rozwiązania Cosmos](import-data.md).
* Procedury składowane, zgodnie z opisem w [programowania w języku JavaScript po stronie serwera dla bazy danych Azure rozwiązania Cosmos](programming.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Czy buforowanie linków zasobów pomocy technicznej interfejsu API SQL?
Tak, ponieważ bazy danych rozwiązania Cosmos Azure jest usługą RESTful, linki zasobów są niezmienne i mogą być buforowane. Klienci interfejsu API SQL można określić nagłówek "If-None-Match" dla odczytów względem dowolnego zasobu typu dokumentu lub kolekcji, a następnie zaktualizuj ich lokalne kopie, po zmianie wersji serwera.

### <a name="is-a-local-instance-of-sql-api-available"></a>Lokalne wystąpienie programu SQL interfejsu API jest dostępny?
Tak. [Azure rozwiązania Cosmos DB emulatora](local-emulator.md) zapewnia emulacji o wysokiej wierności usługi DB rozwiązania Cosmos. Obsługuje funkcje, które są takie same jak rozwiązania Cosmos bazy danych Azure, w tym obsługa tworzenia i badania dokumentów JSON, inicjowania obsługi administracyjnej i skalowanie kolekcje i wykonywania procedury składowane i wyzwalaczy. Mogą tworzyć i testować aplikacje przy użyciu emulatora usługi Azure DB rozwiązania Cosmos i wdrożyć je na platformie Azure w skali globalnej, wprowadzając zmiany do punktu końcowego połączenia dla bazy danych Azure rozwiązania Cosmos konfiguracji pojedynczego.

## <a name="develop-against-the-api-for-mongodb"></a>Tworzenie do interfejsu API dla bazy danych MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Co to jest API Azure rozwiązania Cosmos bazy danych dla bazy danych MongoDB?
API Azure rozwiązania Cosmos bazy danych dla bazy danych MongoDB jest warstwy zgodności, który umożliwia aplikacjom łatwo i w przezroczysty sposób komunikowania się z natywnego aparatu bazy danych DB rozwiązania Cosmos Azure za pomocą istniejących, obsługiwane społeczności Apache bazy danych MongoDB z interfejsów API i sterowników. Deweloperzy mogą teraz używać do tworzenia aplikacji, które korzystają z bazy danych Azure rozwiązania Cosmos istniejących łańcuchów Narzędzia bazy danych MongoDB i umiejętności. Deweloperom korzystać z unikatowych możliwości bazy danych rozwiązania Cosmos platformy Azure, w tym obsługi automatycznego indeksowania, tworzenia kopii zapasowej, umów dotyczących poziomu finansowo kopii zapasowej usług (SLA) i tak dalej.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Jak nawiązać Moje interfejsu API dla bazy danych MongoDB
Najszybszy sposób nawiązania połączenia interfejsu API Azure rozwiązania Cosmos bazy danych dla bazy danych MongoDB ma head za pośrednictwem do [portalu Azure](https://portal.azure.com). Przejdź do swojego konta, a następnie w menu nawigacji po lewej stronie kliknij **Szybki Start**. Szybki Start to najlepszy sposób pozyskania wstawki kodu do połączenia z bazą danych. 

Azure DB rozwiązania Cosmos wymusza wymogi ograniczeniami zabezpieczeń. Azure DB rozwiązania Cosmos kont wymagają uwierzytelniania i zapewnienia bezpiecznej komunikacji za pośrednictwem protokołu SSL, dlatego należy użyć TLSv1.2.

Aby uzyskać więcej informacji, zobacz [Connect do interfejsu API dla bazy danych MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Czy istnieją dodatkowe kody błędów dla interfejsu API dla bazy danych MongoDB?
Oprócz często występujące kody błędów bazy danych MongoDB API bazy danych MongoDB ma własne kody błędu:


| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba zużywane jednostki żądania przekroczył wskaźnik elastycznie jednostka żądania dla kolekcji i został ograniczony. | Należy wziąć pod uwagę skalowanie przepływność kolekcji z portalu Azure lub podjęciem ponownej próby. |
| ExceededMemoryLimit | 16501 | Jako usługę wielodostępną operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnego zapytania kryteria lub skontaktuj się z pomocy technicznej z [portalu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Przykład:  *&nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {Nazwa: "Adama"}}, <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$sort: {wieku: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>Tworzenie tabeli interfejsu API

### <a name="how-can-i-use-the-table-api-offering"></a>Jak używać oferty tabeli interfejsu API? 
Interfejs API Azure rozwiązania Cosmos DB tabeli są dostępne w [portalu Azure][azure-portal]. Najpierw musisz zarejestrować subskrypcji platformy Azure. Po zarejestrowaniu się możesz dodać konto interfejsu API Azure rozwiązania Cosmos DB tabeli do subskrypcji platformy Azure, a następnie dodaj tabel do Twojego konta. 

Obsługiwane języki i skojarzone uruchamia szybkie w można znaleźć [wprowadzenie do interfejsu API Azure rozwiązania Cosmos DB tabeli](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Należy nowy zestaw SDK, aby za pomocą interfejsu API tabeli? 
Brak istniejącego magazynu zestawów SDK nadal powinny działać. Jednak zaleca się, że jeden zawsze pobiera najnowsze zestawów SDK najlepsze pomocy technicznej i w wielu przypadkach wyższego poziomu wydajności. Lista dostępnych języków w [wprowadzenie do interfejsu API Azure rozwiązania Cosmos DB tabeli](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Gdy tabela interfejsu API nie jest taka sama, jak zachowanie magazynu tabel Azure?
Istnieją pewne różnice zachowanie, które użytkownicy pochodzące z magazynem tabel Azure, którzy chcą do tworzenia tabel z interfejsu API Azure rozwiązania Cosmos bazy danych tabeli należy zwrócić uwagę:

* Interfejsu API Azure rozwiązania Cosmos DB tabeli używa modelu zastrzeżone pojemności w celu zapewnienia wydajności gwarantowane, ale oznacza to, że jeden pokrywa pojemność natychmiast po utworzeniu tabeli, nawet jeśli nie jest używana pojemność. Z magazynem tabel Azure jedną tylko płaci pojemności, który faktycznie jest używany. To pozwala wyjaśnić, dlaczego API tabeli mogą oferować Read 10 ms i 15 ms zapisać umowy SLA w 99-ty percentyl, gdy magazyn tabel Azure oferuje 10 drugi umowy SLA. Jednak w konsekwencji tabel tabeli interfejsu API, nawet puste tabele bez żadnych żądań pieniędzy kosztów w celu zapewnienia wydajności jest dostępny do obsługi żądań do nich na umowie SLA oferowane przez bazy danych Azure rozwiązania Cosmos.
* Zwracane przez interfejs API tabeli wyników zapytania nie są sortowane w kolejności klucza wiersza/klucz partycji, są one w magazynie tabel platformy Azure.
* Klucze wiersza mogą być tylko do 255 bajtów
* Partie może zawierać maksymalnie 2 MB
* CORS nie jest obecnie obsługiwany.
* Nazwy tabeli w magazynie tabel platformy Azure nie jest rozróżniana, ale są one interfejsu API Azure rozwiązania Cosmos DB tabeli
* Azure rozwiązania Cosmos DB wewnętrzny formaty kodowania informacje, np. pola binarnego nie są obecnie tak efektywne jak jedną może. W związku z tym może to spowodować nieoczekiwane ograniczenia na rozmiar danych. Na przykład aktualnie jedną nie można użyć pełnej 1 mg jednostki tabeli do przechowywania danych binarnych, ponieważ szyfrowanie zwiększa rozmiar danych.
* Nazwa właściwości jednostki "Id" obecnie nieobsługiwane

Pod względem interfejsu API REST istnieje wiele opcji punkty końcowe/zapytania, które nie są obsługiwane przez interfejs API Azure rozwiązania Cosmos DB tabeli:
| Metody REST | Opcja punktu końcowego/zapytania REST | Adresy URL dokumentu | Wyjaśnienie |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=properties| [Ustaw właściwości usługi tabeli](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) i [pobrać właściwości usługi tabel](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ten punkt końcowy jest używana do ustawiania zasad CORS, konfiguracji magazynu analizy i ustawień rejestrowania. CORS nie jest obecnie obsługiwany i rejestrowanie i analiza będą obsługiwane inaczej w usłudze Azure DB rozwiązania Cosmos niż tabele magazynu Azure |
| OPCJE | /<table-resource-name> | [Transmitowane wstępnego CORS tabeli żądania](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Jest to część CORS, które bazy danych Azure rozwiązania Cosmos nie obsługuje obecnie. |
| GET | /?restype=service@comp=stats | [Pobierz Statystyka usługi tabel](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Zawiera informacje, jak szybko replikuje dane między podstawowe i pomocnicze bazy danych. To nie jest potrzebne do rozwiązania Cosmos bazy danych replikacji jest częścią operacji zapisu. |
| GET, PUT | /mytable?comp=acl | [Pobierz tabelę ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) i [ustawić tabeli listy kontroli dostępu](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | To pobiera i ustawia zasady dostępu przechowywane, używany do zarządzania dostępu sygnatur dostępu Współdzielonego. Sygnatury dostępu Współdzielonego jest obsługiwana, ale są one ustawić i zarządzane w inny sposób. |

Ponadto interfejsu API Azure rozwiązania Cosmos DB tabeli obsługuje tylko format JSON nie ATOM.

Obsługuje bazy danych Azure rozwiązania Cosmos dostępu sygnatur dostępu Współdzielonego są określone zasady, który nie obsługuje on w szczególności tych związanych z operacji zarządzania, takich jak prawo, aby utworzyć nowe tabele.

Dla zestawu .NET SDK w szczególności, istnieją pewne klasy i metody, które bazy danych Azure rozwiązania Cosmos nie obsługuje obecnie.

| Klasa | Nieobsługiwany — metoda |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats* |
| CloudTable | Ustawiania * |
|            | GetPermissions * |
| TableServiceContext | * (Ta klasa faktycznie jest przestarzała) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Jeśli którekolwiek z tych różnic są problem projektu skontaktuj się z [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) i Daj nam znać.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak wyrazić opinię na temat zestawu SDK lub usterki?
Twoja opinia można udostępniać w jednym z następujących sposobów:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Przepełnienie stosu](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Przepełnienie stosu jest najlepsze dla programowania pytania. Upewnij się, że jest to pytanie [na temat](https://stackoverflow.com/help/on-topic) i [zawierają tyle szczegółowe informacje, jak to możliwe, co na pytanie, wyczyść i którą można odpowiedzieć](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Co to jest parametry połączenia, które należy użyć do nawiązania połączenia interfejsu API tabeli?
Parametry połączenia są:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```
Parametry połączenia można uzyskać ze strony parametry połączenia w portalu Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak zastąpić ustawienia konfiguracji dla opcji żądania w zestawie SDK .NET dla interfejsu API tabeli?
Aby uzyskać informacje o ustawieniach konfiguracji, zobacz [możliwości bazy danych Azure rozwiązania Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Niektóre ustawienia są obsługiwane na metodę CreateCloudTableClient i inne przy użyciu pliku app.config w sekcji appSettings w aplikacji klienta.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Dla klientów, którzy korzystają z istniejącego magazynu tabel Azure SDK są wszystkie zmiany?
Brak. Nie wprowadzono żadnych zmian dotyczących istniejących lub nowych klientów korzystających z istniejącego magazynu tabel Azure SDK. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Jak wyświetlić tabeli danych przechowywanych w usłudze Azure DB rozwiązania Cosmos do użycia przy użyciu interfejsu API tabeli? 
Aby przeglądać dane, można użyć portalu Azure. Umożliwia także kodu interfejsu API tabeli lub narzędzia wymienione w następnej odpowiedzi. 

### <a name="which-tools-work-with-the-table-api"></a>Narzędzia pracować z interfejsem API tabeli? 
Można użyć [Eksploratora usługi Storage Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Narzędzia elastyczność podjęcie parametry połączenia w formacie określonym wcześniej może obsłużyć nowy interfejs API tabeli. Lista narzędzi tabeli są przekazywane w [narzędzi klienta magazynu Azure](../storage/common/storage-explorers.md) strony. 

### <a name="do-powershell-or-azure-cli-work-with-the-table-api"></a>Programu PowerShell lub interfejsu wiersza polecenia Azure działają przy użyciu interfejsu API tabeli?
Brak obsługi dla [PowerShell](table-powershell.md). Obsługa interfejsu wiersza polecenia platformy Azure nie jest obecnie dostępna.

### <a name="is-the-concurrency-on-operations-controlled"></a>W operacji kontrolowane jest współbieżność?
Tak, optymistycznej współbieżności jest zapewniana przez użycie mechanizmu ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Model zapytań OData jest obsługiwana dla jednostek? 
Tak, interfejsu API tabeli obsługuje zapytania OData i zapytań LINQ. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Można podłączyć do magazynu tabel platformy Azure i interfejsu API tabeli bazy danych rozwiązania Cosmos Azure obok siebie w tej samej aplikacji? 
Tak, można połączyć się przez utworzenie dwóch osobnych wystąpień CloudTableClient, każdy wskazujący własny identyfikator URI przy użyciu parametrów połączenia.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak przeprowadzić migrację istniejącej aplikacji magazynu tabel Azure z tą ofertą?
[Narzędzie AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) i [narzędzia migracji danych DB rozwiązania Cosmos Azure](import-data.md) są obsługiwane.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>W jaki sposób rozszerzania rozmiaru magazynu zrobić dla tej usługi, jeśli na przykład uruchomić z *n* GB danych i danych wzrośnie do 1 TB w czasie? 
Azure DB rozwiązania Cosmos zaprojektowano w celu zapewnienia nieograniczony magazyn wykorzystaniem skalowanie w poziomie. Usługę można monitorować i skutecznie zwiększyć pamięć masową. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak monitorować oferty tabeli interfejsu API?
Można użyć interfejsu API tabeli **metryki** okienko do monitorowania żądań i wykorzystania magazynu. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Sposób obliczania przepływność, którą potrzebuję?
Narzędzia do szacowania pojemności służy do obliczania TableThroughput, które są wymagane dla operacji. Aby uzyskać więcej informacji, zobacz [szacowania jednostek żądania i przechowywanie danych](https://www.documentdb.com/capacityplanner). Ogólnie rzecz biorąc można reprezentować jednostki jako JSON i podanie liczby operacji. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Czy można użyć interfejsu API zestawu SDK tabeli lokalnie w emulatorze?
Nie w tej chwili.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Moje istniejącej aplikacji można pracować z interfejsem API tabeli? 
Tak, tego samego interfejsu API jest obsługiwane.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>Należy przeprowadzić migrację istniejące aplikacje magazynu tabel Azure do zestawu SDK, jeśli nie chce korzystać z funkcji API tabeli?
Nie można tworzyć i korzystać z istniejących zasobów magazynu tabel Azure bez przeszkód dowolnego rodzaju. Jednak jeśli nie używasz interfejsu API tabeli, nie mogą korzystać z automatycznego indeksu, spójności dodatkowych opcji lub globalnego dystrybucji. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>Jak dodać replikacji danych w interfejsie API tabeli w różnych regionach platformy Azure?
Przy użyciu portalu Azure DB rozwiązania Cosmos [ustawienia globalne replikacji](tutorial-global-distribution-sql-api.md#portal) Aby dodać obszary, które są odpowiednie dla twojej aplikacji. Do opracowywania aplikacji globalnie rozproszone, należy również dodać aplikacji z informacjami o PreferredLocation ustawioną lokalny region zapewniające małych opóźnieniach odczytu. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Jak zmienić regionu podstawowego zapisu dla konta w interfejsie API tabeli?
W okienku portalu globalnej replikacji bazy danych Azure rozwiązania Cosmos umożliwia dodawanie regionu, a następnie przełączyć się do regionu wymagane. Aby uzyskać instrukcje, zobacz [programowania z użyciem konta bazy danych Azure rozwiązania Cosmos w przypadku](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak skonfigurować Moje preferowanych regionów odczytu dla małych opóźnień I dystrybucji danych? 
Aby odczytać z lokalnej lokalizacji, należy użyć klucza PreferredLocation w pliku app.config. Istniejących aplikacji interfejsu API tabeli zgłasza błąd, jeśli ustawiono LocationMode. Ten kod, należy usunąć, ponieważ interfejs API tabeli przejmuje te informacje z pliku app.config. Aby uzyskać więcej informacji, zobacz [możliwości bazy danych Azure rozwiązania Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak należy traktować poziomów spójności w interfejsie API tabeli? 
Azure DB rozwiązania Cosmos zapewnia dobrze uzasadnione kompromis między spójności, dostępnością i opóźnieniem. Azure DB rozwiązania Cosmos oferuje pięć poziomów spójności deweloperom API tabeli, aby można było wybrać model prawo spójności na poziomie tabeli i tworzenie pojedynczych żądania podczas wykonywania zapytań. Gdy klient nawiąże połączenie, może określać poziomu spójności. Poziom za pomocą argumentu consistencyLevel CreateCloudTableClient można zmienić. 

Interfejs API tabeli zawiera małe opóźnienia odczytuje z "odczytać własne zapisy" z spójności nieaktualności Bounded jako domyślny. Aby uzyskać więcej informacji, zobacz [poziomy spójności](consistency-levels.md). 

Domyślnie Magazyn tabel Azure zapewnia wysoki poziom spójności w ramach regionu i spójność Eventual w lokalizacji dodatkowej. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Interfejs API tabeli bazy danych rozwiązania Cosmos Azure oferuje więcej poziomów spójności niż w przypadku magazynu tabel Azure?
Tak, aby uzyskać informacje na temat korzystania z bazy danych Azure rozwiązania Cosmos Rozproszony charakter, zobacz [poziomy spójności](consistency-levels.md). Ponieważ dla poziomów spójności podano gwarancji, można używać ich bez obaw. Aby uzyskać więcej informacji, zobacz [możliwości bazy danych Azure rozwiązania Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Podczas dystrybucji globalnego jest włączone, jak długo trwa replikowanie danych?
Azure DB rozwiązania Cosmos zatwierdza danych trwałym w regionie lokalnych i wypychanie danych do innych regionów natychmiast w sprawie milisekundy. Replikacja jest zależne tylko od czasu Rundy (RTT) w centrum danych. Aby dowiedzieć się więcej na temat możliwości dystrybucji globalnej bazy danych rozwiązania Cosmos Azure, zobacz [bazy danych Azure rozwiązania Cosmos: globalnie rozproszoną bazę danych usługi Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Można zmienić poziomu spójności żądanie odczytu?
Z bazy danych rozwiązania Cosmos platformy Azure można ustawić poziomu spójności na poziomie kontenera (dla tabeli). Przy użyciu zestawu .NET SDK, można zmienić poziom, dostarczając wartości dla klucza TableConsistencyLevel w pliku app.config. Możliwe wartości to: silne, ograniczone nieaktualności sesji, prefiks spójne i Eventual. Aby uzyskać więcej informacji, zobacz [danych dostosowywalne poziomy spójności w usłudze Azure DB rozwiązania Cosmos](consistency-levels.md). Pomysł klucza jest, że nie można ustawić spójności żądania poziomu więcej niż ustawienie dla tabeli. Na przykład nie można ustawić poziomu spójności dla tabeli Eventual i na poziomie spójności żądania na silne. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak API tabeli obsługuje tryb failover Jeśli region przestanie działać? 
Interfejs API tabeli wykorzystuje globalnie rozproszone platformy Azure DB rozwiązania Cosmos. Aby upewnić się, że aplikacja może tolerować przestoju centrum danych, należy włączyć co najmniej jeden region więcej konta w portalu Azure DB rozwiązania Cosmos [programowania z użyciem konta bazy danych Azure rozwiązania Cosmos w przypadku](regional-failover.md). Przy użyciu portalu można ustawić priorytet regionu [programowania z użyciem konta bazy danych Azure rozwiązania Cosmos w przypadku](regional-failover.md). 

Możesz dodać dowolną liczbę regionów, jak dla konta i kontrolować, gdzie ona może przełączyć zapewniając priorytet trybu failover. Oczywiście korzystanie z bazy danych, musisz podać aplikacji istnieje zbyt. Jeśli tak zrobisz, klienci nie wystąpi Przestój. [Najnowszego klienta .NET SDK](table-sdk-dotnet.md) jest homing automatycznie, ale innych zestawów SDK nie są. Oznacza to, że może wykryć regionu, który nie działa i automatycznie przełączyć nowy region.

### <a name="is-the-table-api-enabled-for-backups"></a>Interfejs API tabeli jest włączone dla kopii zapasowych?
Tak, interfejsu API tabeli korzysta z platformy Azure DB rozwiązania Cosmos kopii zapasowych. Kopie zapasowe są wykonywane automatycznie. Aby uzyskać więcej informacji, zobacz [Online kopii zapasowej i przywracania bazy danych Azure rozwiązania Cosmos](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Powoduje API tabeli indeksowania wszystkie atrybuty obiektu domyślnie
Tak, wszystkie atrybuty obiektu jest indeksowana domyślnie. Aby uzyskać więcej informacji, zobacz [bazy danych Azure rozwiązania Cosmos: zasady indeksowania](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Jest to średnia I nie trzeba tworzyć wiele indeksów do zaspokojenia zapytania? 
Tak, interfejsu API Azure rozwiązania Cosmos DB tabeli zapewnia automatyczne indeksowanie wszystkie atrybuty bez żadnych definicji schematu. Ta Automatyzacja zwalnia deweloperom fokusu w aplikacji, a nie na tworzenie indeksu i zarządzanie nimi. Aby uzyskać więcej informacji, zobacz [bazy danych Azure rozwiązania Cosmos: zasady indeksowania](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Czy można zmienić zasady indeksowania?
Tak, można zmienić zasady indeksowania, podając definicję indeksu. Aby uzyskać więcej informacji, zobacz [możliwości bazy danych Azure rozwiązania Cosmos](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Należy poprawnie kodowania i wprowadzić ustawienia. 

dla SDK firmy innej niż .NET zasady indeksowania można ustawić tylko Portal pod adresem **Eksploratora danych**, przejdź do określonej tabeli, które chcesz zmienić, a następnie przejdź do **& Ustawienia skalowania**-> zasady indeksowania, wprowadzić odpowiednie zmiany, a następnie **zapisać**.

Z zestawu SDK .NET mogą być dostarczane w pliku app.config:
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure DB rozwiązania Cosmos jako platforma wydaje się, że ma wiele funkcji, takich jak sortowanie, agregacje, hierarchii oraz innych funkcji. Będzie można dodawanie tych funkcji do interfejsu API tabeli? 
Interfejs API tabeli zawiera te same funkcje zapytania jako magazynu tabel Azure. Usługa Azure Cosmos DB obsługuje także sortowanie, agregaty, zapytania geoprzestrzenne, hierarchię i szeroki zakres funkcji wbudowanych. Firma Microsoft udostępni dodatkowe funkcje w interfejsie API tabeli w przyszłej aktualizacji. Aby uzyskać więcej informacji, zobacz [zapytania SQL](sql-api-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Podczas zmiany TableThroughput dla interfejsu API tabeli?
Należy zmienić TableThroughput, gdy stosuje się jeden z następujących warunków:
* Przeprowadzasz wyodrębniania, przekształcania i ładowania (ETL) danych lub chcesz przekazać dużą ilość danych w krótkim czasie. 
* Potrzebujesz więcej przepustowości z kontenera w wewnętrznej. Na przykład zobacz przepływności używane jest większa niż udostępnionej przepływności, czy możesz są pobierania ograniczane. Aby uzyskać więcej informacji, zobacz [przepływności zestawu dla kontenerów bazy danych Azure rozwiązania Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Można skalować lub dół przepływność tabeli tabeli interfejsu API? 
Tak, można użyć portalu Azure DB rozwiązania Cosmos skali okienku można skalować przepływność. Aby uzyskać więcej informacji, zobacz [przepływności zestawu](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Jest domyślnie ustawione dla nowo aprowizowanej tabel TableThroughput?
Tak, jeśli nie zastępują TableThroughput przy użyciu pliku app.config i nie należy używać kontenera utworzone wcześniej w usłudze Azure DB rozwiązania Cosmos, usługa tworzy tabelę z przepływności 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Istnieje już zmiany ceny dla istniejących klientów usługi magazynu tabel Azure?
Brak. Nie została zmieniona w cenie dla istniejących klientów usługi Magazyn tabel Azure. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Sposób obliczania ceny dla interfejsu API tabeli? 
Cena zależy od TableThroughput przydzielone. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-offering"></a>Jak obsługiwać żadnych ograniczania przepustowości w tabelach w ofercie tabeli interfejsu API 
Jeśli szybkość żądania przekracza pojemność udostępnionej przepływności dla podstawowej kontenera, wystąpi błąd i zestawu SDK ponowi próbę połączenia, stosując zasady ponawiania.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Dlaczego należy wybrać przepływności oprócz PartitionKey i RowKey, aby móc korzystać z oferty tabeli interfejsu API Azure DB rozwiązania Cosmos
Azure DB rozwiązania Cosmos ustawia przepływności domyślnego kontenera sieci, jeśli nie zostanie określona w pliku app.config, lub za pośrednictwem portalu. 

Azure DB rozwiązania Cosmos zapewnia gwarancje wydajność i opóźnienia z górne granice operacji. Gwarancja jest możliwe, gdy aparat można wymusić ładu na operacje dzierżawcy. Ustawienie TableThroughput zapewnia uzyskać gwarantowanej przepustowości i opóźnień, ponieważ platforma rezerwuje tej pojemności i gwarantującej Powodzenie operacyjnej. 

Przy użyciu specyfikacji przepływności, elastycznie zmianą jego korzystać z sezonowości aplikacji, spełniają potrzeb w zakresie przepustowości i ograniczenia kosztów.

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Magazyn tabel Azure zostało niedrogie dla mnie, ponieważ I Zapłać tylko do przechowywania danych i I rzadko zapytania. Oferty interfejsu API Azure rozwiązania Cosmos DB tabeli wydaje się być ładowania mnie, nawet jeśli nie ma I wykonać jednej transakcji lub niczego przechowywane. Sprawdź jakie?

Azure DB rozwiązania Cosmos została zaprojektowana jako globalnie rozproszone, na podstawie umowy SLA systemu z gwarancją dostępności, opóźnienia i przepływności. Podczas rezerwowania przepustowości w usłudze Azure DB rozwiązania Cosmos gwarantowane, w odróżnieniu od przepustowości innych systemów. Azure DB rozwiązania Cosmos zapewnia dodatkowe funkcje, które żądane klientów, takie jak indeksów pomocniczych i dystrybucji globalnego.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nigdy nie pojawia się powiadomienie "przydział pełna" (co oznacza, że partycji jest pełny) po pozyskiwania danych do magazynu tabel Azure. Przy użyciu interfejsu API tabeli pojawia się w tej wiadomości. Jest to oferta ograniczenie mnie i wymuszania, żeby można było zmienić mojej istniejącej aplikacji?

Azure DB rozwiązania Cosmos jest system na podstawie umowy SLA zawiera nieograniczone skali gwarancje opóźnień, przepustowości, dostępności i spójności. W celu zapewnienia wydajności premium gwarantowane, upewnij się, że rozmiar danych i indeks są łatwe w zarządzaniu i skalowalność. 10 GB limit liczby jednostek lub elementów na klucz partycji jest upewnij się, że udostępniamy doskonałą wydajność wyszukiwania i zapytania. Aby upewnić się, skalowanie aplikacji nawet w przypadku usługi Azure Storage, zaleca się możesz *nie* utworzyć partycję gorących wszystkie informacje są przechowywane w jednej partycji i badając ją. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Dlatego PartitionKey i RowKey są nadal wymagane przy użyciu interfejsu API tabeli? 
Tak. Ponieważ powierzchni interfejsu API tabeli jest podobny do magazynu tabel Azure SDK, klucz partycji zapewnia efektywną dystrybucję danych. Klucz wiersza jest unikatowy w ramach tej partycji. Klucz wiersza musi być obecny i nie może mieć wartości null jak standardowy zestaw SDK. Długość RowKey wynosi 255 bajtów i długość PartitionKey jest 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>Co to są komunikaty o błędach dla interfejsu API tabeli?
Azure Table storage i interfejsu API Azure rozwiązania Cosmos bazy danych tabeli należy używać tej samej zestawów SDK, dlatego większość błędów jest taki sam.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Dlaczego I pobrać ograniczany podczas próby utworzenia jeden po drugim wiele tabel w interfejsie API tabeli?
Azure DB rozwiązania Cosmos jest system na podstawie umowy SLA zawiera czas oczekiwania, przepływności, dostępności i gwarancje spójności. Ponieważ jest inicjowana systemu rezerwuje zasobów, aby zagwarantować te wymagania. Wykryto szybkość szybkie tworzenie tabel i ograniczany. Firma Microsoft zaleca przyjrzeć się częstotliwość tworzenia tabel i obniżyć go do mniej niż 5 na minutę. Należy pamiętać, że interfejs API tabeli jest inicjowana systemu. Obecnie obsługi administracyjnej, rozpocznie się zwrócić dla niego. 

## <a name="develop-against-the-graph-api"></a>Opracowywanie przed interfejs API Graph
### <a name="how-can-i-apply-the-functionality-of-graph-api-to-azure-cosmos-db"></a>Jak można stosować funkcje interfejsu API programu Graph do bazy danych rozwiązania Cosmos Azure?
Biblioteka rozszerzenia można użyć do zastosowania funkcji interfejsu API programu Graph. Ta biblioteka jest nazywana Microsoft Azure wykresy, i jest dostępny na NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Wygląda na to, obsługują język przechodzenie Gremlin wykresu. Czy planowane jest dodanie więcej formularzy zapytania?
Tak, firma Microsoft planuje dodanie innych mechanizmów dla zapytania w przyszłości. 

### <a name="how-can-i-use-the-new-graph-api-offering"></a>Jak używać nową ofertę interfejsu API programu Graph? 
Aby rozpocząć pracę, należy wykonać [interfejsu API programu Graph](../cosmos-db/create-graph-dotnet.md) artykułu szybki start.

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>Tworzenie przy użyciu Apache Cassandra interfejsu API (wersja zapoznawcza)

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>Co to jest wersja protokołu obsługiwana w prywatnej wersji zapoznawczej? Jest plan obsługi innych protokołów?
Interfejs API Cassandra Apache dla bazy danych rozwiązania Cosmos Azure obsługuje obecnie CQL w wersji 4. Daj nam znać, jeśli masz opinię na temat obsługi innych protokołów za pośrednictwem [opinii z witryny uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) lub Wyślij wiadomość e-mail do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Dlaczego jest wybranie przepustowości dla tabeli wymagania?
Azure DB rozwiązania Cosmos ustawia domyślny przepływności z kontenera, w zależności, w której utworzono tabelę z - portalu lub CQL. Azure DB rozwiązania Cosmos zapewnia gwarancje wydajność i opóźnienia z górne granice operacji. Gwarancja jest możliwe, gdy aparat można wymusić ładu na operacje dzierżawcy. Ustawienie przepływności zapewnia uzyskać gwarantowanej przepustowości i opóźnień, ponieważ platforma rezerwuje tej pojemności i gwarantującej powodzenie operacji. Można zmienić elastycznie przepływności, aby korzystać z sezonowości aplikacji i ograniczenia kosztów.

Pojęcie przepływności zostało wyjaśnione w dokumencie [jednostek żądania w usłudze Azure DB rozwiązania Cosmos](request-units.md) artykułu. Przepływność dla tabeli jest dystrybuowana do podstawowej partycji fizycznej jednakowo.  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Co to jest domyślna RU/s, tabeli, gdy został utworzony za pomocą CQL? Co zrobić, jeśli należy je zmienić?
Azure DB rozwiązania Cosmos używa jednostki żądania na sekundę (RU/s) jako walutę zapewniające przepływności. Tabele utworzone przy użyciu CQL ma 400 RU. Możesz zmienić RU w portalu. 

CQL
```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET
```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload); 
``` 

### <a name="what-happens-when-throughput-is-exceeded"></a>Co się stanie po przekroczeniu przepływności? 
Azure DB rozwiązania Cosmos zapewnia gwarancje wydajność i opóźnienia z górne granice operacji. Gwarancja jest możliwe, gdy aparat można wymusić ładu na operacje dzierżawcy. Jest to możliwe, oparte na ustawienie przepustowości, która pozwala uzyskać gwarantowanej przepustowości i opóźnień, ponieważ platforma rezerwuje tej pojemności i gwarantuje powodzenie operacji. Po przekroczeniu tej pojemności otrzymasz przeciążone komunikat o błędzie wskazujący, że przekroczono pojemność sieci. przeciążone 0x1001: nie można przetworzyć żądania, ponieważ "Liczby żądań jest duża". W tym momencie jest, aby zobaczyć, jakie operacje, i ich objętości powoduje, że ten problem. Możesz uzyskać wyobrażenie o wykorzystanych pojemność przekracza pojemność elastycznie o metryki w portalu. Następnie należy się upewnić, niemal wykorzystanie pojemności równomiernie we wszystkich partycji podstawowej. Jeśli widzisz większość przepływność jest używane przez jedną partycję, masz pochylenia obciążenia. 

Metryki są dostępne który dowiesz się, jak przepływności jest używany w godziny, dni i na siedem dni na partycje lub agregacji. Aby uzyskać więcej informacji, zobacz [monitorowanie i debugowanie za pomocą metryki w usłudze Azure DB rozwiązania Cosmos](use-metrics.md).

Dzienniki diagnostyczne zostały omówione w [rejestrowania diagnostycznego bazy danych Azure rozwiązania Cosmos](logging.md) artykułu.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Czy głównej mapy klucza do koncepcji klucza partycji bazy danych rozwiązania Cosmos Azure?
Tak, klucz partycji służy do umieszczania jednostki w odpowiedniej lokalizacji. W usłudze Azure DB rozwiązania Cosmos służy można znaleźć partycji logicznej po prawej, który jest przechowywany na partycji fizycznej. Pojęcie partycjonowania znajduje się również w [partycji i skali w usłudze Azure DB rozwiązania Cosmos](partition-data.md) artykułu. Podjęcie podstawowych optymalizacji Oto czy partycji logicznej nie może przekraczać limitu 10 GB dzisiaj. 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co się dzieje, gdy pojawia się powiadomienie "przydział pełna" wskazujący, że partycji jest pełny?
Azure DB rozwiązania Cosmos jest umowa SLA systemu udostępniająca nieograniczone skali gwarancje opóźnień, przepustowości, dostępności i spójności. Jego Cassandra interfejsu API umożliwia zbyt nieograniczony magazyn danych. Ten nieograniczony magazyn opiera się na poziomie skalowania danych przy użyciu partycjonowania jako kluczowe pojęcia. Pojęcie partycjonowania znajduje się również w [partycji i skali w usłudze Azure DB rozwiązania Cosmos](partition-data.md) artykułu.

10 GB limit liczby jednostek lub elementów na partycji logicznej należy przestrzegać. W celu zapewnienia także skalowanie aplikacji, zaleca się, że możesz *nie* utworzyć partycję gorących wszystkie informacje są przechowywane w jednej partycji i badając ją. Ten błąd można tylko dostarczanych Jeśli danych jest niesymetryczna — która jest np. mieć partii danych do jednej partycji klucz — więcej niż 10 GB. Można znaleźć dystrybucji przy użyciu portalu magazynu danych. Sposób, aby naprawić ten błąd jest recrete tabeli i wybierz podstawowy szczegółowego (klucz partycji), który umożliwia lepszą rozkład danych.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Czy jest możliwe Cassandra interfejsu API jako magazyn wartości klucza za pomocą miliony lub miliardów kluczy poszczególnych partycji
Dane nieograniczone przez skalowanie w poziomie magazynu można przechowywać bazę danych systemu Azure rozwiązania Cosmos. To jest niezależna od przepływności. Tak, zawsze można użyć Cassandra interfejsu API do przechowywania i pobierania klucza/wartości za pośrednictwem klucza podstawowego prawej/partycji. Te klucze poszczególnych Pobierz ich własnych partycji logicznej i sit nad partycji fizycznej bez problemów. 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>Czy jest możliwe utworzenie wielu tabel z Apache Cassandra interfejsu API z rozwiązania Cosmos bazy danych Azure
Tak, istnieje możliwość Kreta wielu tabel z interfejsem API Cassandra Apache. Każdy z tych tabel jest traktowany jako jednostki przepływności i magazynu. 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>Czy jest możliwe do utworzenia wielu tabel w przedziale czasu?
Azure DB rozwiązania Cosmos jest systemem zasobów postanowieniom działań płaszczyzny zarówno danych, jak i kontroli. Kontenery, takich jak kolekcje, tabele są jednostki środowiska uruchomieniowego, które są udostępniane dla danego przepływności. Tworzenie tych kontenerach szybkie naciśnięcie nie jest działaniem oczekiwanym i ograniczany. Jeśli masz testy, które upuszczania/tabel od razu utworzyć — spróbuj rozmieść je.

### <a name="what-is-maximum-number-of-tables-which-can-be-created"></a>Co to jest maksymalna liczba tabel, których można utworzyć?
Nie ma żadnego limitu fizycznych na liczbę tabel, Wyślij wiadomość e-mail na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Jeśli masz dużą liczbę tabel (w przypadku gdy łączny rozmiar stałej przekracza 10 TB danych), które muszą zostać utworzone z zwykle 10s lub 100s. 

### <a name="what-is-the-maximum--of-keyspace-which-we-can-create"></a>Jaka jest maksymalna liczba przestrzeni kluczy, którego można utworzyć? 
Nie ma żadnego fizycznych limitu liczby keyspaces, są one kontenery metadanych, Wyślij wiadomość e-mail na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Jeśli masz dużą liczbę keyspaces jakiegoś powodu. 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Czy jest możliwe przenieść dużej ilości danych po uruchomieniu z normalnym tabeli? 
Pojemność magazynu odbywa się automatycznie i zwiększa push większej ilości danych. Tak bez obaw można importować jak najwięcej danych, zgodnie z potrzebami bez zarządzania i inicjowania obsługi administracyjnej węzłów itp. 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Czy jest możliwe Określanie ustawień pliku yaml programu, można skonfigurować zachowanie Apache Casssandra interfejsu API z rozwiązania Cosmos bazy danych Azure
Apache Cassandra interfejsu API z rozwiązania Cosmos bazy danych platformy Azure jest usługą platformy. Zapewnia protokół poziomu programu Access do wykonywania operacji. Optymalizacji ukrywa złożoność zarządzania, monitorowania i konfiguracji. Jako deweloper/użytkownik nie trzeba martwić się o dostępności, reliktów, klucza pamięci podręcznej, wiersza pamięci podręcznej, filtr rozwiniętej i wieloma innymi ustawieniami. Zespoły Apache Cassandra API Azure DB rozwiązania Cosmos na zapewnienie wydajność odczytów i zapisów wymagane bez nakładów związanych z zarządzania i konfiguracji.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Interfejs API Cassandra Apache dla bazy danych Azure rozwiązania Cosmos będzie obsługiwać węzła dodawania/klastra/w węźle stan polecenia?
Interfejs API Cassandra Apache jest usługą platformy, co sprawia, że planowania pojemności, odpowiada na żądania elastyczność przepływność i Magazyn błyskawicznie. Z bazy danych Azure rozwiązania Cosmos udostępnieniem przepływności, które są potrzebne. Następnie można go dowolną liczbę razy za pośrednictwem dnia w górę i w dół skalować bez obaw o Dodawanie/usuwanie węzłów lub zarządzania nimi. Oznacza to, że nie należy użyć węzła, narzędzie do zarządzania klastrem za. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co się stanie w odniesieniu do różnych ustawień konfiguracji do tworzenia przestrzeni kluczy, takich jak prosty i sieci?
Azure DB rozwiązania Cosmos zawiera globalny dystrybucji fabrycznej dostępności i małe opóźnienia powodów. Nie trzeba replik instalacji itp. Zapisuje wszystkie są zawsze trwale kworum zatwierdzone w dowolnym regionie, w którym można zapisywać zapewniając gwarancje wydajności.  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>Co się stanie, względem różne ustawienia dla tabeli metadane, takie jak filtr rozwiniętej buforowania, przeczytaj zmiana naprawy, gc_grace, memtable_flush_period kompresji itp?
Azure DB rozwiązania Cosmos zapewnia wydajność dla odczytuje i zapisuje i przepływność bez potrzeby dotknięcie wszelkie ustawienia konfiguracji i manipulowania nimi przypadkowo.  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Czas wygaśnięcia (TTL) jest obsługiwana w przypadku tabel Cassandra? 
Tak, czas wygaśnięcia jest obsługiwane. 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Czy jest możliwe monitorowanie stanu węzła, stan repliki gc i parametrów systemu operacyjnego wcześniej z różnych narzędzi Co trzeba być monitorowane teraz?
Azure DB rozwiązania Cosmos jest usługą platformy, która pomaga zwiększyć wydajność i nie martw się o zarządzanie i monitorowanie infrastruktury. Wystarczy zajmie się przepływności, który jest dostępny na portalu metryki można znaleźć, jeśli są pobierania ograniczane i zwiększyć lub zmniejszyć tego przepływności. Monitor [SLA](monitor-accounts.md).
Użyj [metryki](use-metrics.md) użyj [dzienniki diagnostyczne](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Które zestawy SDK klientów może współpracować z Apache Cassandra interfejsu API z rozwiązania Cosmos bazy danych platformy Azure?
Sterowniki, które używają CQLv3 programy klienckie miały zastosowania w prywatnej wersji zapoznawczej Apache Cassandra zestawu SDK klienta. Jeśli masz inne sterowniki, które używane lub jeśli są ukierunkowane problemy, wysyłania wiadomości e-mail do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-composite-partition-key-supported"></a>Klucz partycji złożone są obsługiwane?
Tak, służy składni regularne do tworzenia klucza złożonego partycji. 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>W celu załadowania danych można użyć modułu ładującego sstable?
Nie, podczas udostępniania wersji zapoznawczej sstable modułu ładującego nie jest obsługiwana. 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Można lokalnego klastra cassandra łączyć się z interfejsu API Cassandra Apache DB rozwiązania Cosmos Azure?
Na obecne DB rozwiązania Cosmos Azure ma zoptymalizowane środowisko dla środowiska chmury bez zwiększania operacji. Jeśli potrzebujesz parowania, Wyślij wiadomość do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) opis danego scenariusza.

### <a name="does-cassandra-api-provide-full-backups"></a>Interfejs API Cassandra oferuje pełne kopie zapasowe? 
Azure DB rozwiązania Cosmos zawiera dwa wolnego pełne kopie zapasowe wykonane w odstępach czasu w czterech godzin obecnie we wszystkich interfejsów API. Dzięki temu nie trzeba skonfigurować harmonogram tworzenia kopii zapasowych itd. Jeśli chcesz zmodyfikować, przechowywania i częstotliwości, Wyślij wiadomość e-mail do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) lub Zgłoś sprawę pomocy technicznej. Informacje o możliwości tworzenia kopii zapasowej znajduje się w [automatyczne kopie zapasowe online i przywracania bazy danych Azure rozwiązania Cosmos](online-backup-and-restore.md) artykułu. 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak konto interfejsu API Cassandra obsługuje tryb failover Jeśli region przestanie działać? 
Interfejs API Azure rozwiązania Cosmos DB Cassandra obiektowy globalnie rozproszone platformy Azure DB rozwiązania Cosmos. Aby upewnić się, że aplikacja może tolerować przestoju centrum danych, należy włączyć co najmniej jeden region więcej konta w portalu Azure DB rozwiązania Cosmos [programowania z użyciem konta bazy danych Azure rozwiązania Cosmos w przypadku](regional-failover.md). Przy użyciu portalu można ustawić priorytet regionu [programowania z użyciem konta bazy danych Azure rozwiązania Cosmos w przypadku](regional-failover.md). 

Możesz dodać dowolną liczbę regionów, jak dla konta i kontrolować, gdzie ona może przełączyć zapewniając priorytet trybu failover. Korzystanie z bazy danych, musisz podać zbyt aplikacji. Jeśli tak zrobisz, klienci nie wystąpi Przestój. 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Powoduje Apache Cassandra API indeksowania wszystkie atrybuty obiektu domyślnie
Tak, wszystkie atrybuty obiektu jest indeksowana domyślnie w programie Azure DB rozwiązania Cosmos. Aby uzyskać więcej informacji, zobacz [bazy danych Azure rozwiązania Cosmos: zasady indeksowania](indexing-policies.md). Możesz uzyskać korzyści gwarantowane wydajności przy użyciu indeksowania spójne i trwałe kworum zatwierdzone zapisuje zawsze. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Jest to średnia I nie trzeba tworzyć wiele indeksów do zaspokojenia zapytania? 
Tak, bazy danych rozwiązania Cosmos Azure zapewnia automatyczne indeksowanie wszystkie atrybuty bez żadnych definicji schematu. Ta Automatyzacja zwalnia deweloperom fokusu w aplikacji, a nie na tworzenie indeksu i zarządzanie nimi. Aby uzyskać więcej informacji, zobacz [bazy danych Azure rozwiązania Cosmos: zasady indeksowania](indexing-policies.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Można używać nowego zestawu SDK interfejsu API Cassandra lokalnie w emulatorze?
Firma Microsoft planuje Obsługa tej możliwości w przyszłości. 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure DB rozwiązania Cosmos jako platforma wydaje się, że ma wiele funkcji, takich jak changefeed i inne funkcje. Te funkcje zostaną dodane do interfejsu API Cassandra? 
Interfejs API Cassandra Apache zapewnia te same funkcje CQL jako Apache Cassandra. Firma Microsoft planuje przeanalizowania możliwości obsługi różnych funkcji w przyszłości.

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>Funkcja x regularne Cassandra interfejsu API nie działa jako dzisiaj, gdzie opinii można podać?
Wyrazić swoją opinię przy użyciu [opinii z witryny uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
