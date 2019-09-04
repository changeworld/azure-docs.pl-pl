---
title: Często zadawane pytania dotyczące różnych interfejsów API w usłudze Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Cosmos DB, to usługa globalnie dystrybuowanej, wielomodelowej bazy danych. Więcej informacji na temat pojemności, poziomów wydajności i skalowania.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 30530f445941747c659f584d279261148b08825e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240797"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące różnych interfejsów API w usłudze Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jakie są typowe przypadki użycia usługi Azure Cosmos DB?

Usługa Azure Cosmos DB jest dobrym wyborem dla nowych sieci web, mobilnych, gier, i aplikacji IoT, gdy automatyczne skalowanie, przewidywalną wydajność, szybkie kolejność czasy odpowiedzi w milisekundach oraz możliwość obsługi zapytań względem danych bez schematu jest ważne. Usługa Azure Cosmos DB pozwala na szybkie opracowywanie i obsługę ciągłej iteracji modeli danych aplikacji. Aplikacje zarządzające o zawartość wygenerowaną przez użytkowników i dane są [typowe przypadki użycia usługi Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak usługa Azure Cosmos DB zapewnia przewidywalną wydajność?

A [jednostki żądań](request-units.md) służy do mierzenia przepływności w usłudze Azure Cosmos DB. Przepływność 1RU odpowiada przepływności pobrania dokumentu o wartości 1 KB. Każda operacja w usłudze Azure Cosmos DB, w tym odczyty, zapisy, zapytań SQL i wykonania procedury składowanej ma deterministyczną wartość RU, który jest oparty na przepływności wymaganej do ukończenia tej operacji. Zamiast myśleć o procesora CPU, we/wy i pamięci oraz ich wpływ na przepływność aplikacji, można traktować pod względem pojedynczej mierze jednostek RU.

Każdy kontener usługi Azure Cosmos można skonfigurować przy użyciu alokowanej przepływności w zakresie jednostek ru przepływności na sekundę. W przypadku aplikacji o dowolnej skali test porównawczy poszczególnych żądań w celu pomiaru ich wartości jednostki ru na i aprowizować kontener służący do obsługi suma liczby jednostek żądania udostępniane wszystkim żądaniom. Możesz również skalować w górę lub Skaluj w dół przepływność z kontenerem aplikacji rozwijających się potrzeb. Aby uzyskać więcej informacji na temat jednostek żądania i pomocy przy określaniu potrzeb kontenera, wypróbuj [Kalkulator przepływności](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak usługa Azure Cosmos DB obsługuje różne modele danych, takie jak klucz/wartość, kolumnowy, dokument i wykres?

Klucz/wartość (tabela), kolumnowy, dokumentów i danych wykresu, który modeli są natywnie obsługiwane ze względu na ARS (atomów, rekordów i sekwencji) projektowania, że usługi Azure Cosmos DB zaprojektowano. Atomów, rekordów i sekwencji umożliwia łatwe mapowane i zaprojektowana różne modele danych. Interfejsy API dla podzestawu modeli są dostępne teraz (SQL, MongoDB, Table i Gremlin), a inne specyficzne dla dodatkowych modeli danych będą dostępne w przyszłości.

Usługa Azure Cosmos DB ma schemat niezależny od aparatu indeksowania która może automatycznie indeksować wszystkie dane, które które pozyska, bez wymagania żadnych schematów ani indeksów pomocniczych od dewelopera. Aparat opiera się na zestaw układów logicznych indeksu (odwrócona, kolumnowy, drzewo), które rozdzielenie układ magazynu z indeksem i podsystemy przetwarzania zapytań. Usługa cosmos DB ma również możliwość obsługi zestawu o komunikacji sieciowej protokołów i interfejsów API w sposób rozszerzalny i wydajnie tłumaczyć je do modelu danych podstawowych (1) i układów logicznych indeksu, (2) co jednoznacznie może obsługiwać więcej niż jeden model danych natywnie.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Czy można używać wielu interfejsów API do uzyskiwania dostępu do danych?

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. W przypadku, gdy wiele modeli oznacza, Azure Cosmos DB obsługuje wiele interfejsów API i wielu modeli danych, różne interfejsy API używają różnych formatów danych dla magazynu i protokołu przewodowego. Na przykład SQL używa JSON, MongoDB używa BSON, tabela używa modelu EDM, Cassandra używa CQL, Gremlin używa GraphSON. W związku z tym zalecamy używanie tego samego interfejsu API do uzyskiwania dostępu do danych w danym koncie.

Każdy interfejs API działa niezależnie, z wyjątkiem interfejsu API Gremlin i SQL, które są współobsługiwane.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB HIPAA jest zgodne?

Tak, usługa Azure Cosmos DB jest zgodnych ze standardami HIPAA. Ustawa HIPAA określa wymogi dotyczące wykorzystywania, ujawniania i ochrony informacji o zdrowiu umożliwiających indywidualną identyfikację osoby. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jakie są limity magazynu usługi Azure Cosmos DB?

Nie ma żadnego limitu, całkowitej ilości danych, które mogą być przechowywane przez kontener w usłudze Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jakie są ograniczenia przepływności usługi Azure Cosmos DB?

Nie ma żadnego limitu, na łączną przepływność, które obsługują kontenera w usłudze Azure Cosmos DB. Kluczowe chodzi o to, aby rozłożyć obciążenie około równomiernie między wystarczająco dużą liczbę kluczy partycji.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Tryby łączności usługi bezpośrednie i bramy są szyfrowane?

Tak, oba tryby są zawsze w pełni zaszyfrowane.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Ile kosztuje usługa Azure Cosmos DB

Aby uzyskać szczegółowe informacje, zapoznaj się [szczegóły cennika usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) strony. Opłaty za użycie usługi Azure Cosmos DB są określane przez liczbę kontenerów elastycznie, liczby godzin kontenery zostały online, i aprowizowanej przepływności dla każdego kontenera.

### <a name="is-a-free-account-available"></a>Bezpłatne konto jest dostępny?

Tak, możesz zasubskrybować konta ograniczonej czasowo, bez dodatkowych opłat, bez zobowiązań. Aby się zarejestrować, odwiedź stronę [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) lub Przeczytaj więcej w [Wypróbuj usługi Azure Cosmos DB — często zadawane pytania](#try-cosmos-db).

Jeśli jesteś nowym użytkownikiem platformy Azure, możesz zarejestrować się w celu [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), które zapewnia 30 dni i środki na wypróbowanie usług platformy Azure. Jeśli masz subskrypcję programu Visual Studio, możesz również możliwość skorzystania z [bezpłatnych środków platformy Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) na korzystanie z dowolnej usługi platformy Azure.

Można również użyć [emulatora usługi Azure Cosmos DB](local-emulator.md) na opracowywanie i testowanie aplikacji lokalnie, bezpłatnie, bez tworzenia subskrypcji platformy Azure. Jeśli sposób działania aplikacji w emulatorze usługi Azure Cosmos DB jest zadowalający, możesz zacząć korzystać z konta usługi Azure Cosmos DB w chmurze.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak uzyskać dodatkową pomoc dzięki usłudze Azure Cosmos DB?

Aby zadawać pytania techniczne, możesz wpis do jednej z tych dwóch pytań i odpowiedzi na forach:

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Przepełnienie stosu jest najważniejsze pytania na temat programowania. Upewnij się, Twoje pytanie jest [na temat](https://stackoverflow.com/help/on-topic) i [zawierają tyle szczegółowe informacje, jak to możliwe, dzięki czemu zapytania teraz bardziej czytelne i którą można odpowiedzieć](https://stackoverflow.com/help/how-to-ask).

Aby poprosić o nowe funkcje, należy utworzyć nowe żądanie na [User voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.

Inne pytania można przesyłać do zespół [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); jednak nie jest to alias pomocy technicznej.

## <a id="try-cosmos-db"></a>Spróbuj subskrypcji usługi Azure Cosmos DB

Użytkownik może teraz korzystaj z ograniczonej czasowo usługi Azure Cosmos DB bez subskrypcji, bez opłat ani zobowiązań. Aby utworzyć konto subskrypcji Wypróbuj usługę Azure Cosmos DB, przejdź do [Wypróbuj bezpłatnie usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Ta subskrypcja jest oddzielony od [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/free/)i mogą być używane razem z bezpłatnej wersji próbnej platformy Azure lub platformy Azure płatnej subskrypcji.

Try Azure Cosmos DB subskrypcje będą widoczne w witrynie Azure portal obok innych subskrypcji skojarzonych z identyfikatora użytkownika.

Do subskrypcji Wypróbuj usługę Azure Cosmos DB mają zastosowanie następujące warunki:

* Jeden [kontener aprowizacji o przepływności](./set-throughput.md#set-throughput-on-a-container) na subskrypcję usług SQL, Gremlin API i Table accounts.
* Maksymalnie trzy [kolekcje inicjowane przez przepływność](./set-throughput.md#set-throughput-on-a-container) na subskrypcję dla kont usługi MongoDB.
* Jedna [baza danych z obsługą przepływności](./set-throughput.md#set-throughput-on-a-database) na subskrypcję. Bazy danych z obsługą przepływności mogą zawierać dowolną liczbę kontenerów wewnątrz.
* Pojemność magazynu o rozmiarze 10 GB.
* Replikacja globalna jest dostępna w następujących [regionach platformy Azure](https://azure.microsoft.com/regions/): Środkowe stany USA, Europa Północna i Azja Południowo-Wschodnia
* Maksymalna przepływność 5 K/s w przypadku aprowizacji na poziomie kontenera.
* Maksymalna przepływność 20 K/s w przypadku aprowizacji na poziomie bazy danych.
* Subskrypcje wygasną po upływie 30 dni i można je przedłużyć do maksymalnie 31 dni.
* Nie można utworzyć biletów pomocy technicznej platformy Azure dla konta Wypróbuj usługę Azure Cosmos DB jednak obsługiwane dla subskrybentów przy użyciu istniejących planów pomocy technicznej.

## <a name="set-up-azure-cosmos-db"></a>Konfigurowanie usługi Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak utworzyć konto usługi Azure Cosmos DB?

Usługa Azure Cosmos DB jest dostępna w witrynie Azure portal. Najpierw należy utworzyć konto subskrypcji platformy Azure. Po zarejestrowaniu możesz dodać konto Azure Cosmos DB do subskrypcji platformy Azure.

### <a name="what-is-a-master-key"></a>Co to jest klucz główny?

Klucz główny jest tokenem zabezpieczającym dostęp do wszystkich zasobów dla konta. Osoby, które mają klucz ma uprawnienia odczytu i zapisu do wszystkich zasobów w ramach konta bazy danych. Podczas dystrybucji kluczy głównych należy zachować ostrożność. Podstawowy klucz główny i pomocniczy klucz główny są dostępne w bloku **klucze** [Azure Portal][azure-portal]. Aby uzyskać więcej informacji na temat kluczy, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Co to są regiony, które można ustawić PreferredLocations?

Wartość PreferredLocations można ustawić do dowolnego z regionów świadczenia usługi Azure, w których usługi Cosmos DB jest dostępna. Aby uzyskać listę dostępnych regionów, zobacz [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Czy jest coś, w których warto pamiętać podczas dystrybucji danych na całym świecie za pośrednictwem centrów danych platformy Azure?

Usługa Azure Cosmos DB znajduje się we wszystkich regionach platformy Azure, zgodnie z instrukcjami na [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) strony. Ponieważ usługa core, co w nowym centrum danych ma obecności usługi Azure Cosmos DB.

Po ustawieniu regionu, należy pamiętać, że usługi Azure Cosmos DB szanuje chmur suwerennych i dla instytucji rządowych. Oznacza to jeśli tworzysz konto w [suwerennych region](https://azure.microsoft.com/global-infrastructure/), nie można replikować poza tym, że [suwerennych region](https://azure.microsoft.com/global-infrastructure/). Podobnie nie można włączyć replikacji do innych lokalizacji suwerennych z zewnętrznego konta.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Jest możliwe przełączyć się z poziomu przepływności kontenerów aprowizacji aprowizacji poziomu przepływności bazy danych? I odwrotnie

Kontener i aprowizacji poziomu przepływności bazy danych to osobne oferty i przełączania się między jedną z tych wersji wymagają migracji danych ze źródła do miejsca docelowego. Co oznacza, że trzeba utworzyć nową bazę danych lub nowy kontener, a następnie przeprowadzić migrację danych przy użyciu [biblioteki wykonawczy zbiorczej](bulk-executor-overview.md) lub [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Usługa Azure cosmos DB obsługuje analiza serii czasu?

Tak, Azure cosmos DB obsługuje analizy serii czasowych, poniżej przedstawiono przykład, który [wzorzec serii czasu](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). W tym przykładzie pokazano, jak używać zmiany źródła danych, aby tworzyć zagregowane dane szeregów czasowych. Można rozszerzyć to podejście, przy użyciu przesyłania strumieniowego platformy spark lub innego przetwarzający dane strumienia.

## <a name="sql-api"></a>Interfejs API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak rozpocząć tworzenie przy użyciu interfejsu API SQL

Najpierw musisz zarejestrować do subskrypcji platformy Azure. Po zalogowaniu do subskrypcji platformy Azure z subskrypcją platformy Azure można dodać kontener interfejsu API SQL. Aby uzyskać instrukcje dotyczące dodawania konta Azure Cosmos DB, zobacz [Tworzenie konta bazy danych usługi Azure Cosmos](create-sql-api-dotnet.md#create-account).

[Zestawy SDK](sql-api-sdk-dotnet.md) są dostępne dla języków .NET, Python, Node.js, JavaScript i Java. Deweloperzy mogą również używać [interfejsów API RESTful protokołu HTTP](/rest/api/cosmos-db/) do interakcji z zasobami usługi Azure Cosmos DB z różnych platform i języków.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Można uzyskać dostęp do gotowych do użycia przykłady, aby łatwiej rozpocząć pracę?

Przykłady dla interfejsu API SQL [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), i [Python](sql-api-python-samples.md) zestawy SDK są dostępne w witrynie GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Baza danych interfejsu API SQL obsługuje dane bez schematu?

Tak, interfejs API SQL umożliwia aplikacjom przechowywanie dowolnych dokumentów JSON bez definicji schematu lub wskazówek. Dane są natychmiast dostępne dla zapytania za pomocą interfejsu zapytania SQL usługi Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>Interfejs API SQL obsługuje transakcje ACID?

Tak, interfejs API SQL obsługuje transakcje dla wielu dokumentów wyrażone jako procedury składowane JavaScript i wyzwalacze. Transakcje są ograniczone do jednej partycji w ramach każdego kontenera i wykonywane przy użyciu semantyki ACID jako "wszystko lub nic" odizolowanych od innego współbieżnie wykonywanego kodu i żądań użytkownika. Jeśli istnieją wyjątki zgłaszane przez wykonanie kodu aplikacji JavaScript po stronie serwera, cała transakcja zostanie wycofana. 

### <a name="what-is-a-container"></a>Co to jest kontenerem?

Kontener jest grupą dokumentów i ich skojarzonej logiki aplikacji JavaScript. Kontener jest płatna jednostka, której [koszt](performance-levels.md) zależy od informacji o przepływności i magazynu używane. Kontenery mogą znajdować się na partycji lub serwerów i skalowalność niezbędną do obsługi praktycznie nieograniczonej ilości magazynu lub przepływności.

* W przypadku interfejsu API SQL kontener jest mapowany do kontenera.
* Dla interfejsu API Cosmos DB dla kont MongoDB, kontener jest mapowany do kolekcji.
* W przypadku kont bazy danych Cassandra i interfejsu API tabel kontener mapuje do tabeli.
* Interfejs API Gremlin kont kontener mapuje do wykresu.

Kontenery są również jednostkami rozliczeniowymi dla usługi Azure Cosmos DB. Każdy kontener jest naliczana co godzinę, aprowizowaną przepływność w oparciu i użyte miejsce do magazynowania. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Jak utworzyć bazę danych?

Bazy danych można tworzyć przy użyciu [Azure Portal](https://portal.azure.com), zgodnie z opisem w [sekcji Dodawanie kontenera](create-sql-api-java.md#add-a-container), jednego z [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak skonfigurować użytkowników i uprawnienia?

Można utworzyć przy użyciu jednego z użytkownikami i uprawnieniami [zestawami SDK interfejsu API bazy danych Cosmos](sql-api-sdk-dotnet.md) lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Interfejs API SQL obsługuje SQL?

Język zapytań SQL, które są obsługiwane przez konta interfejsu API SQL jest rozszerzonym podzbiorem funkcji zapytań, który jest obsługiwany przez program SQL Server. Język zapytań SQL usługi Azure Cosmos DB zapewnia rozbudowane operatorów relacyjnych i hierarchicznych oraz rozszerzalność funkcje oparte na języku JavaScript, zdefiniowane przez użytkownika (UDF). Gramatyka JSON umożliwia modelowanie dokumentów JSON jako drzewa z etykietami węzły, które są używane zarówno przez techniki automatycznego indeksowania usługi Azure Cosmos DB i dialekt zapytań SQL usługi Azure Cosmos DB. Aby uzyskać informacje o używaniu gramatyki SQL, zobacz artykuł dotyczący [zapytań SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Interfejs API SQL obsługuje funkcje agregacji SQL?

Interfejs API SQL obsługuje agregację o małych opóźnieniach w dowolnej skali za pomocą funkcji agregujących `COUNT`, `MIN`, `MAX`, `AVG`, i `SUM` przy użyciu Gramatyka języka SQL. Aby uzyskać więcej informacji, zobacz [funkcje](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak interfejs API SQL zapewnia współbieżność?

Interfejs API SQL obsługuje optymistycznej współbieżności sterowanie współbieżnością za pomocą tagów jednostki HTTP lub elementów ETag. Każdy zasób interfejsu API SQL ma element ETag, a element ETag jest ustawiony na serwerze za każdym razem, gdy dokument zostanie zaktualizowany. Nagłówka ETag i bieżącą wartość są objęte wszystkie wiadomości odpowiedzi. Elementów etag może służyć nagłówek If-Match, aby serwer mógł zdecydować, czy można zaktualizować zasobu. Wartość If-Match jest wartość elementu ETag ma być sprawdzany względem. Jeśli wartość elementu ETag odpowiada wartość elementu ETag serwera, zasób jest aktualizowany. Jeśli element ETag nie jest już aktualna, serwer odrzuca operację, podając "HTTP 412 niepowodzenie warunku wstępnego" Kod odpowiedzi. Klient następnie refetches zasobu, aby uzyskać bieżącą wartość elementu ETag dla zasobu. Ponadto elementów etag może służyć za pomocą nagłówka If-None-Match do określenia, czy ponownie Pobierz zasobu jest wymagana.

Aby użyć optymistycznej współbieżności na platformie .NET, należy użyć [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) klasy. Aby uzyskać przykład .NET, zobacz [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) w przykładzie DocumentManagement w witrynie GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak wykonywać transakcje w interfejsie API SQL?

Interfejs API SQL obsługuje transakcje zintegrowane z językiem za pomocą procedur składowanych JavaScript i wyzwalacze. Wszystkie operacje bazy danych wewnątrz skryptów są wykonywane w ramach izolacji migawki. Jeśli jest to kontener o pojedynczej partycji, wykonanie jest ograniczone do kontenera. W przypadku partycjonowania kontenera wykonywanie jest ograniczone do dokumentów o tej samej wartości klucza partycji w kontenerze. Migawka wersji dokumentów (elementy ETag) jest wykonywana na początku transakcji i zatwierdzana tylko wtedy, gdy działanie skryptu zakończy się pomyślnie. Jeśli kod JavaScript zwraca błąd, transakcja zostaje wycofana. Aby uzyskać więcej informacji, zobacz [programowania języka JavaScript po stronie serwera dla usługi Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak można I wstawiania zbiorczego dokumenty do usługi Cosmos DB?

Użytkownik może wstawiania zbiorczego dokumenty do usługi Azure Cosmos DB w jeden z następujących sposobów:

* Narzędzia funkcji wykonawczej zbiorcze, zgodnie z opisem w [biblioteki .NET wykonawca zbiorcze Using](bulk-executor-dot-net.md) i [biblioteka języka Java wykonawca zbiorcze Using](bulk-executor-java.md)
* Narzędzie migracji danych, zgodnie z opisem w [narzędzia migracji bazy danych usługi Azure Cosmos DB](import-data.md).
* Procedury składowane, zgodnie z opisem w [programowania języka JavaScript po stronie serwera dla usługi Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Czy buforowanie linków zasobów pomocy technicznej interfejsu API SQL?

Tak, ponieważ usługa Azure Cosmos DB jest usługą RESTful, linki zasobów są niezmienne i mogą być buforowane. Klienci interfejsu API SQL mogą określić nagłówek "If-None-Match", aby odczytywać wszystkie dokumenty lub kontenery podobne do zasobów, a następnie zaktualizować ich kopie lokalne po zmianie wersji serwera.

### <a name="is-a-local-instance-of-sql-api-available"></a>Lokalne wystąpienie programu interfejsu API SQL jest dostępna?

Tak. [Emulatora usługi Azure Cosmos DB](local-emulator.md) zapewnia emulacji o dużej wierności usługi Cosmos DB. Obsługuje funkcje, które są identyczne z usługi Azure Cosmos DB, w tym obsługę tworzenia i wykonywanie zapytań względem dokumentów JSON, aprowizacja i skalowanie kolekcje i wykonywanie procedur składowanych i wyzwalaczy. Mogą tworzyć i testować aplikacje przy użyciu emulatora usługi Azure Cosmos DB i wdrażać je na platformie Azure na skalę globalną, wprowadzając jedną konfigurację Zmień na punkt końcowy połączenia usługi Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Dlaczego są różne wartości zmiennoprzecinkowe długo w dokumencie zaokrąglana przy przeglądaniu z poziomu Eksploratora danych w portalu.

To ograniczenie języka JavaScript. Kod JavaScript używa liczb zmiennoprzecinkowych o podwójnej precyzji, jak określono w standardzie IEEE 754 i może bezpiecznie przechowywać cyfry od-(2<sup>53</sup> -1) i 2<sup>53</sup>-1 (tj. 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Gdzie są dozwolone uprawnienia w hierarchii obiektów?

Tworzenie uprawnień za pomocą ResourceTokens jest dozwolone na poziomie kontenera i jego obiektów podrzędnych (takich jak dokumenty, załączniki). Oznacza to, które można utworzyć uprawnienia w bazie danych lub na poziomie konta aktualnie nie jest dozwolone.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Co to jest interfejs API Azure Cosmos DB dla MongoDB?

Interfejs API Azure Cosmos DB dla MongoDB to warstwa zgodności protokołu przewodowego, która umożliwia aplikacjom łatwe i nieprzezroczyste komunikowanie się z natywnym aparatem bazy danych Cosmos platformy Azure przy użyciu istniejących, obsługiwanych przez społeczność zestawów SDK i sterowników dla MongoDB. Deweloperzy mogą teraz używać istniejących MongoDB łańcuchy narzędzi i umiejętności do kompilowania aplikacji, które korzystają z Azure Cosmos DB. Deweloperzy mogą korzystać z unikatowych możliwości Azure Cosmos DB, które obejmują dystrybucję globalną z replikacją obejmującą wiele wzorców, funkcję autoindeksowania, konserwację kopii zapasowej, finansowo objęte umową dotyczącą poziomu usług (umowy SLA) itp.

### <a name="how-do-i-connect-to-my-database"></a>Jak mogę połączyć się z moją bazą danych?

Najszybszym sposobem łączenia się z bazą danych Cosmos Azure Cosmos DB z interfejsem API programu MongoDB jest przełączenie do [Azure Portal](https://portal.azure.com). Przejdź do swojego konta, a następnie w menu nawigacji po lewej stronie kliknij **— Szybki Start**. Szybki Start to najlepszy sposób na uzyskanie fragmentów kodu w celu nawiązania połączenia z bazą danych.

Usługa Azure Cosmos DB wymusza ścisłymi zasadami kontrolnymi zabezpieczeń wymagania i standardy. Konta usługi Azure Cosmos DB wymaga uwierzytelniania i bezpiecznej komunikacji za pośrednictwem protokołu SSL, dlatego należy użyć zabezpieczeń TLSv1.2.

Aby uzyskać więcej informacji, zobacz [łączenie się z bazą danych Cosmos przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Czy istnieją dodatkowe kody błędów, z którymi należy się zająć, przy użyciu interfejsu API Azure Cosmos DB dla MongoDB?

Wraz ze wspólnymi kodami błędów MongoDB interfejs API Azure Cosmos DB dla MongoDB ma własne konkretne kody błędów:

| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba zużytych jednostek żądania jest większa niż stawka żądania aprowizacji dla kontenera i została ograniczona. | Należy rozważyć skalowanie przepływności przypisanych do kontenera lub zestaw kontenerów na platformie Azure portal lub Trwa ponawianie próby ponownie. |
| ExceededMemoryLimit | 16501 | Jako usługa dla wielu dzierżawców operacji stała się za pośrednictwem przydziału pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteria zapytania lub skontaktuj się z działem pomocy technicznej firmy [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Przykład: <em> &nbsp; DB.&nbsp;getcollection ("Users"). Aggregate ([ &nbsp; &nbsp;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {Name: "Adam"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {Age:-1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Czy sterownik Simba dla MongoDB jest obsługiwany z interfejsem API Azure Cosmos DB dla MongoDB?

Tak, możesz użyć sterownika Simba Mongo ODBC z interfejsem API Azure Cosmos DB dla MongoDB

## <a id="table"></a>Interfejs API tabel

### <a name="how-can-i-use-the-table-api-offering"></a>Jak można korzystać z oferty interfejsu Table API?

Interfejs API tabel Azure Cosmos DB jest dostępny w [Azure Portal][azure-portal]. Najpierw musisz zarejestrować do subskrypcji platformy Azure. Po utworzeniu konta Dodaj konto interfejsu API tabeli usługi Azure Cosmos DB do subskrypcji platformy Azure, a następnie dodać tabele do swojego konta.

Obsługiwane języki i skojarzone przewodniki szybkiego startu w można znaleźć [wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Czy muszę mieć nowy zestaw SDK do korzystania z interfejsu API tabeli?

Brak istniejącego magazynu, zestawy SDK powinny nadal działać. Jednak zaleca się, że jeden zawsze pobiera najnowsze zestawy SDK najlepsze pomocy technicznej i w wielu przypadkach doskonałą wydajność. Zobacz listę dostępnych języków w [wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Gdy interfejs API tabel nie jest taka sama, jak usługa Azure Table storage zachowanie?

Istnieją pewne różnice zachowanie, które pochodzące z usługi Azure Table storage użytkownicy, którzy chcą tworzyć tabele za pomocą interfejsu API tabeli usługi Azure Cosmos DB, trzeba wiedzieć:

* Interfejs API usługi Azure Cosmos DB Table jest używany model rezerwowanie pojemności w celu zapewnienia wydajności gwarantowane, ale oznacza to, że jeden płaci za pojemność zaraz po utworzeniu tabeli, nawet jeśli pojemność nie jest używana. Za pomocą usługi Azure Table storage, jeden płaci tylko za pojemność, która jest używana. Pomaga to wyjaśnić, dlaczego interfejs API tabel może oferować odczytu 10 ms i 15 ms zapisu umowy SLA w 99. percentylu, natomiast usługa Azure Table storage oferuje umowy SLA 10 sekund. Jednak w konsekwencji za pomocą interfejsu Table API tabel, nawet pustych tabel bez żadnych żądań pieniądze kosztów w celu zapewnienia, że pojemność jest dostępna do obsługi żądań do nich w umowie SLA oferowane przez usługę Azure Cosmos DB.
* Wyniki zapytania zwrócone przez interfejs API tabel nie są posortowane w kolejności kluczy/wierszy partycji, ponieważ znajdują się w usłudze Azure Table Storage.
* Kluczy wierszy może składać się maksymalnie 255 bajtów
* Partie może mieć tylko do 2 MB
* Mechanizm CORS nie jest obecnie obsługiwane.
* W nazwach tabel w usłudze Azure Table Storage nie jest rozróżniana wielkość liter, ale znajdują się one w Azure Cosmos DB interfejs API tabel
* Niektóre usługi Azure Cosmos DB wewnętrznego formaty kodowania informacje, takie jak pola binarne, nie są obecnie wydajne jeden mogą zainteresować. W związku z tym może to spowodować nieoczekiwane ograniczenia rozmiaru danych. Na przykład aktualnie jeden nie można użyć Meg pełnej jedną jednostkę tabeli do przechowywania danych binarnych, ponieważ kodowanie zwiększa rozmiar danych.
* Nazwa właściwości jednostki "ID" jest obecnie nieobsługiwana
* TableQuery TakeCount nie jest ograniczone do 1000

Pod względem interfejsu API REST istnieje kilka opcji punkty końcowe/zapytania, które nie są obsługiwane przez interfejsu API tabeli usługi Azure Cosmos DB:

| Metody REST | Stosowanie opcji zapytania/na punkt końcowy REST | Adresy URL dokumentacji | Wyjaśnienie |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /? restype =service@comp= właściwości| [Ustaw właściwości usługi tabeli](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) i [pobrać właściwości usługi tabeli](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ten punkt końcowy jest używana do ustawiania reguły CORS, konfiguracja usługi storage analytics i ustawień rejestrowania. Mechanizm CORS nie jest obecnie obsługiwane i analizy i rejestrowania są obsługiwane inaczej w usłudze Azure Cosmos DB niż tabele magazynu platformy Azure |
| OPCJE | /\<tabela-zasób-Nazwa > | [Żądanie tabeli CORS krótkiej](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Jest to część CORS, który nie obsługuje obecnie usługi Azure Cosmos DB. |
| GET | /? restype =service@comp= statystyki | [Pobieranie statystyk usługi tabeli](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Zawiera informacje, jak szybko danych jest replikowana między podstawowe i pomocnicze bazy danych. To nie jest wymagany w usłudze Cosmos DB, ponieważ replikacja jest częścią zapisów. |
| GET, PUT | /mytable?comp=acl | [Pobierz tabelę listy ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) i [ustawić tabeli listy ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | To pobiera i ustawia zasady dostępu przechowywane, używany do zarządzania sygnaturami dostępu współdzielonego (SAS). Chociaż sygnatura dostępu współdzielonego jest obsługiwana, są one ustawiane i zarządzane w różny sposób. |

Oprócz interfejsu API tabeli usługi Azure Cosmos DB obsługuje tylko format JSON nie ATOM.

Azure Cosmos DB obsługuje dostęp do sygnatur Współdzielonego istnieją pewne zasady, które nie jest obsługiwana, szczególnie te, które związane z operacji zarządzania, takich jak prawo, aby utworzyć nowe tabele.

Dla zestawu .NET SDK w szczególności, istnieją pewne klas i metod, które aktualnie nie obsługuje usługi Azure Cosmos DB.

| Klasa | Nieobsługiwanej metody |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats* |
| CloudTable | Ustawiania * |
|            | GetPermissions * |
| TableServiceContext | * (Ta klasa jest przestarzałe) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Jeśli którekolwiek z tych różnic są problemem dla projektu, skontaktuj się z [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) i Daj nam znać.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak Wyraź swoją opinię dotyczącą zestawu SDK lub usterki?

Podziel się swoją opinią, w dowolnym z następujących sposobów:

* [Głos użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Przepełnienie stosu jest najważniejsze pytania na temat programowania. Upewnij się, Twoje pytanie jest [na temat](https://stackoverflow.com/help/on-topic) i [zawierają tyle szczegółowe informacje, jak to możliwe, dzięki czemu zapytania teraz bardziej czytelne i którą można odpowiedzieć](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Co to jest ciąg połączenia, wymagających do nawiązywania połączenia z interfejsem API tabel?

Parametry połączenia są:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Parametry połączenia można uzyskać ze strony parametrów połączenia w witrynie Azure portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak zastąpić ustawienia konfiguracji dla opcji żądania w zestawie SDK .NET dla interfejsu API tabel?

Niektóre ustawienia są obsługiwane na temat metody CreateCloudTableClient metody i innych za pomocą pliku app.config, w sekcji appSettings w aplikacji klienckiej. Aby uzyskać informacje dotyczące ustawień konfiguracji, zobacz [możliwości usługi Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Czy wprowadzono jakiekolwiek zmiany dla klientów, którzy korzystają z istniejącej usługi Azure Table storage SDK?

Brak. Brak zmian do istniejących i nowych klientów, którzy korzystają z istniejącej usługi Azure Table storage SDK.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Jak wyświetlić dane tabeli, który jest przechowywany w usłudze Azure Cosmos DB do użytku z interfejsem API tabeli?

Aby przeglądać dane, można użyć witryny Azure portal. Można również użyć kodu interfejsu API tabel lub narzędzia opisane w następnej odpowiedzi.

### <a name="which-tools-work-with-the-table-api"></a>Które narzędzia pracy z interfejsem API tabeli?

Możesz użyć [Eksploratora usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Narzędzia elastyczność wykonaj parametry połączenia w formacie określonym poprzednio może obsługiwać nowego interfejsu API tabeli. Lista narzędzi tabeli znajduje się na [usługi Azure Storage Client Tools](../storage/common/storage-explorers.md) strony.

### <a name="is-the-concurrency-on-operations-controlled"></a>Jest współbieżność na operacje kontrolowane?

Tak, optymistycznej współbieżności jest oferowana w ramach stosowania mechanizmu element ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Model zapytań OData jest obsługiwane dla jednostek?

Tak, interfejs API tabel obsługuje zapytania OData i zapytań LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Można połączyć z usługą Azure Table Storage i Azure Cosmos DB tabeli API obok siebie w tej samej aplikacji?

Tak, można połączyć się przez utworzenie dwóch osobnych wystąpień obiekt CloudTableClient każdego wskazaniu własny identyfikator URI przy użyciu parametrów połączenia.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak migrować istniejącą aplikację magazynu tabel Azure do tej oferty?

[Narzędzie AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) i [narzędzie migracji usługi Azure Cosmos DB dane](import-data.md) są obsługiwane.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak jest rozszerzania rozmiaru magazynu, wykonywane dla tej usługi, jeśli na przykład uruchomić przy użyciu *n* GB danych i danych wzrośnie do 1 TB, wraz z upływem czasu?

Usługa Azure Cosmos DB pozwala uzyskać nieograniczony magazyn wykorzystaniem skalowanie w poziomie. Usługę można monitorować i skutecznie zwiększyć magazynu.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak monitorować oferty interfejsu Table API?

Można użyć interfejsu API tabel **metryki** okienko do monitorowania żądań i użycie magazynu.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Sposób obliczania przepływności, które mogę pozyskać?

Narzędzie do szacowania wydajności służy do obliczania TableThroughput, które są wymagane dla operacji. Aby uzyskać więcej informacji, zobacz [oszacowanie jednostek żądania i przechowywanie danych](https://www.documentdb.com/capacityplanner). Ogólnie rzecz biorąc można wyświetlić jednostkę jako dane JSON i podanie liczby operacji.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Czy można używać zestawu SDK API tabeli lokalnie w emulatorze?

Nie w tej chwili.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Czy mojej istniejącej aplikacji można pracować z interfejsem API tabel?

Tak, tego samego interfejsu API jest obsługiwany.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Należy przeprowadzić migrację mojej istniejącej aplikacjom Azure Table storage z zestawem SDK, jeśli nie chcesz korzystać z funkcji interfejsu API tabeli?

Nie można tworzyć i korzystać z istniejących zasobów magazynu tabel Azure bez przeszkód wszelkiego rodzaju. Jednak jeśli nie korzystasz z interfejsu API tabel, nie mogą korzystać z automatycznego indeksu, spójności dodatkowych opcji lub globalnej dystrybucji.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Jak dodać replikacji danych w interfejsie API tabeli w więcej niż jeden region platformy Azure?

Możesz użyć portalu usługi Azure Cosmos DB [ustawienia replikacji globalnej](tutorial-global-distribution-sql-api.md#portal) Dodawanie regionów, które są odpowiednie dla twojej aplikacji. Tworzenie aplikacji dystrybuowanych globalnie, należy również dodać aplikację przy użyciu informacji o PreferredLocation w lokalnym regionie zapewniające małe opóźnienia odczytu.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Jak zmienić podstawowego regionu zapisu dla konta w interfejsie API tabeli?

W okienku portalu globalnej replikacji usługi Azure Cosmos DB umożliwia dodawanie regionu, a następnie przełączyć w tryb failover do regionu wymagane. Aby uzyskać instrukcje, zobacz [opracowywanie zawartości przy użyciu kont usługi Azure Cosmos DB w wielu regionach](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak skonfigurować Mój preferowanych regionów odczytu w celu uzyskania niskich opóźnień, gdy dystrybuować Moje dane?

Aby ułatwić odczytywanie lokalizacji lokalnej, należy użyć klucza PreferredLocation w pliku app.config. W przypadku istniejących aplikacji interfejsu API tabel zgłasza błąd, jeśli ustawiono LocationMode. Ten kod, należy usunąć, ponieważ interfejs API tabel zbiera te informacje z pliku app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>W jaki sposób należy rozważać poziomów spójności w interfejsie API tabeli?

Usługa Azure Cosmos DB zapewnia dobrze uzasadnione kompromis między spójnością, dostępnością i opóźnieniem. Usługa Azure Cosmos DB oferuje pięć poziomów spójności dla deweloperów interfejsu API tabel, aby można było wybrać model prawo spójności na poziomie tabeli i wprowadzania poszczególnych żądań zapytań o dane. Gdy klient nawiąże połączenie, można określić poziom spójności. Można zmienić poziomu za pomocą argumentu consistencyLevel metody CreateCloudTableClient.

Interfejs API tabel udostępnia o małych opóźnieniach odczytuje z "Odczyt własne zapisów" spójności powiązana nieaktualność jako domyślny. Aby uzyskać więcej informacji, zobacz [poziomów spójności](consistency-levels.md).

Domyślnie usługa Azure Table storage zapewnia wysoki poziom spójności w obrębie regionu i spójność ostateczna w dodatkowej lokalizacji.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Interfejsu API tabeli usługi Azure Cosmos DB oferuje więcej poziomów spójności, niż Usługa Azure Table storage?

Tak, aby uzyskać informacje o tym, jak korzystać z Rozproszony charakter usługi Azure Cosmos DB, zobacz [poziomów spójności](consistency-levels.md). Ponieważ gwarancje znajdują się w celu zapewnienia spójności, można ich użyć z pewnością.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Po włączeniu dystrybucji globalnej, jak długo trwa do replikowania danych?

Usługa Azure Cosmos DB zatwierdzeń danych trwale w regionie lokalnym i wypychanie danych do innych regionów natychmiast w ciągu kilku milisekund. Ta replikacja jest zależna tylko czas Rundy (RTT) w centrum danych. Aby dowiedzieć się więcej na temat możliwości globalnej dystrybucji Azure Cosmos DB, zobacz [Azure Cosmos DB: Globalnie dystrybuowana usługa bazy danych na](distribute-data-globally.md)platformie Azure.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Można zmienić poziomu spójności żądanie odczytu?

Za pomocą usługi Azure Cosmos DB można ustawić poziomu spójności na poziomie kontenera (w tabeli). Za pomocą zestawu .NET SDK, możesz zmienić poziom, podając wartość dla klucza TableConsistencyLevel w pliku app.config. Możliwe wartości to: Mocna, powiązana nieaktualność, sesja, spójny prefiks i ostateczna. Aby uzyskać więcej informacji, zobacz [dostosowania danych poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md). Kluczowe chodzi o to, że nie można ustawić spójności żądania poziomu więcej niż ustawienie dla tabeli. Na przykład nie można ustawić poziomu spójności dla tabeli ostateczna i poziomu spójności żądania na silne.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak interfejs API tabel obsługuje tryb failover Jeśli region ulegnie awarii?

Interfejs API tabel korzysta z globalnie rozproszoną platformę usługi Azure Cosmos DB. Aby upewnić się, że Twoja aplikacja może tolerować przestój centrum danych, należy włączyć co najmniej jeden region więcej dla konta w portalu usługi Azure Cosmos DB [opracowywanie zawartości przy użyciu kont usługi Azure Cosmos DB w wielu regionach](high-availability.md). Można ustawić priorytet regionu za pomocą portalu [opracowywanie zawartości przy użyciu kont usługi Azure Cosmos DB w wielu regionach](high-availability.md).

Możesz dodać dowolną liczbę regionów, jak konta i kontrolować, gdzie go może zostać przeniesiony do, zapewniając priorytetem trybu failover. Korzystanie z bazy danych, musisz podać aplikacji istnieje zbyt. Jeśli tak zrobisz, nie będzie przestoju swoich klientów. [Najnowszy zestaw SDK klienta .NET](table-sdk-dotnet.md) jest automatycznie obsługujące usługę, ale innych nie zestawów SDK. Oznacza to, że może wykryć regionu, który nie działa i automatycznie przełączania w tryb failover do nowego regionu.

### <a name="is-the-table-api-enabled-for-backups"></a>Interfejs API tabel włączono obsługę kopii zapasowych?

Tak, interfejs API tabel korzysta z platformy usługi Azure Cosmos DB, tworzenia kopii zapasowych. Kopie zapasowe są wykonywane automatycznie. Aby uzyskać więcej informacji, zobacz [Online kopii zapasowej i przywracania w usłudze Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Powoduje interfejsu API tabel indeksowania domyślnie wszystkie atrybuty jednostki

Tak, wszystkie atrybuty jednostki są indeksowane, domyślnie. Aby uzyskać więcej informacji, [Zobacz Azure Cosmos DB: Zasady](index-policy.md)indeksowania.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Czy oznacza to, że nie trzeba utworzyć więcej niż jednego indeksu do zaspokojenia zapytania?

Tak, interfejsu API tabeli usługi Azure Cosmos DB obsługuje automatyczne indeksowanie wszystkich atrybutów bez żadnych definicji schematu. Ta Automatyzacja ułatwia deweloperom skoncentrować się na aplikacji, a nie na tworzenie indeksu i zarządzanie nimi. Aby uzyskać więcej informacji, [Zobacz Azure Cosmos DB: Zasady](index-policy.md)indeksowania.

### <a name="can-i-change-the-indexing-policy"></a>Czy mogę zmienić zasady indeksowania

Tak, można zmienić zasady indeksowania, podając definicję indeksu. Musisz prawidłowo kodowania i wprowadzić ustawienia.

Dla innych niż - zestawów .NET SDK, zasady indeksowania można ustawić tylko w portalu na stronie **Eksplorator danych**, przejdź do określonej tabeli, które chcesz zmienić, a następnie przejdź do **skalowanie i ustawienia**-> zasady indeksowania Wprowadź żądane zmiany i następnie **Zapisz**.

Z zestawu SDK platformy .NET mogą być dostarczane w pliku app.config:

```JSON
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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Usługa Azure Cosmos DB jako platforma wydaje się mieć wiele możliwości, takich jak sortowanie, agregaty, hierarchii i inne funkcje. Użytkownik doda te funkcje interfejsu API tabeli?

Interfejs API tabel udostępnia taką samą funkcjonalność zapytań, jak usługa Azure Table storage. Usługa Azure Cosmos DB obsługuje także sortowanie, agregaty, zapytania geoprzestrzenne, hierarchię i szeroki zakres funkcji wbudowanych. Aby uzyskać więcej informacji, zobacz [zapytania SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Podczas zmiany TableThroughput dla interfejsu API tabel?

Należy zmienić TableThroughput, gdy stosuje się jedną z następujących warunków:

* Wykonujesz wyodrębniania, przekształcania i ładowania (ETL) danych lub chcesz przekazać dużej ilości danych w krótkim czasie.
* Potrzebujesz więcej przepływności z kontenera lub zestaw kontenerów na zapleczu. Na przykład zobaczysz że przepustowość używana jest większa niż aprowizowaną przepływność i jesteś ograniczany. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności dla kontenerów usługi Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Można skalować w górę lub dół przepływność Moja tabela interfejsu API tabeli?

Tak, można użyć okienka skalowania w portalu usługi Azure Cosmos DB do skalowania przepływności. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Jest domyślnie TableThroughput ustawiony dla tabel użytkownicy nowo aprowizowanych?

Tak, jeśli nie zastąpisz TableThroughput przy użyciu pliku app.config, a nie należy używać wstępnie utworzony kontener w usłudze Azure Cosmos DB, usługa tworzy tabelę przy przepływności równej 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Czy jest jakaś zmiana, cen dla istniejących klientów usługi Azure Table storage?

Brak. Nie ma zmian cen dla istniejących klientów usługi Azure Table storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak obliczana jest cena dla interfejsu API tabel?

Cena zależy od TableThroughput przydzielone.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Jak obsługiwać wszystkie szybkości w tabelach w ofercie interfejsu API tabel

Jeśli liczba żądań jest więcej niż pojemność aprowizowaną przepływność dla kontenera podstawowej lub zestaw kontenerów, wystąpi błąd, a zestaw SDK ponawia próbę wywołania, stosując zasady ponawiania.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Dlaczego trzeba wybrać przepływność niezależnie od właściwości PartitionKey i RowKey, aby móc korzystać z oferty interfejsu API tabel usługi Azure Cosmos DB?

Usługa Azure Cosmos DB ustawia kontenera przepływność domyślną, jeśli nie zostaną podane w pliku app.config lub za pośrednictwem portalu.

Usługa Azure Cosmos DB zapewnia gwarancji wydajności i opóźnień z górną granicę dla operacji. Gwarancja jest możliwe w przypadku, gdy aparat można wymusić nadzoru na operacje dzierżawcy. Ustawienie TableThroughput zapewnia uzyskać gwarantowaną przepływność i opóźnienie, ponieważ rezerwuje tej pojemności i gwarantuje Powodzenie operacyjne platformy.

Przy użyciu specyfikacji przepływności, można elastycznie zmienić go do korzyści z sezonowości aplikacji, potrzeb przepływność i zmniejsza koszty.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Usługa Azure Table storage zostało niedrogie dla mnie, ponieważ zapłacić tylko do przechowywania danych i rzadko zapytania. Oferta interfejsu API tabeli usługi Azure Cosmos DB wydaje się być ładowania mnie, mimo że I nie zostały wykonane pojedynczą transakcję lub przechowywane niczego. Jakie?

Usługa Azure Cosmos DB została zaprojektowana jako globalnie dystrybuowana, oparte na umowie SLA systemie gwarantuje dostępność, opóźnienia i przepływności. Podczas rezerwowania przepustowości w usłudze Azure Cosmos DB ma żadnej gwarancji, w odróżnieniu od przepływności innych systemów. Usługa Azure Cosmos DB zapewnia dodatkowe funkcje, wymagające klientów, takich jak indeksy pomocnicze i globalnej dystrybucji.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nigdy nie otrzymam osiągnięto limit przydziału"powiadomienia (co oznacza, że partycja jest zapełniony) podczas pozyskiwania danych w usłudze Azure Table storage. Za pomocą interfejsu API tabel pojawia się w tej wiadomości. Jest to oferta ograniczanie mnie i wymuszanie mnie do mojej istniejącej aplikacji zmiany?

Usługa Azure Cosmos DB jest oparte na umowie SLA systemu, który umożliwia nieograniczone Skalowanie z gwarancji w odniesieniu do opóźnienia, przepływności, dostępności i spójności. Aby zapewnić wydajność gwarantowana — wersja premium, upewnij się, że rozmiar danych i indeksu są zarządzane i skalowalne. Limit 10 GB, liczby jednostek lub elementów na klucz partycji jest upewnij się, że firma Microsoft zapewnia doskonałą wydajność wyszukiwania i zapytań. Aby upewnić się, że aplikacja efektywne, nawet w przypadku usługi Azure Storage, zaleca się, że możesz *nie* tworzenie gorącą partycją dzięki przechowywaniu wszystkich informacji w jednej partycji i badając ją.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Dlatego właściwości PartitionKey i RowKey są nadal wymagane przy użyciu interfejsu API tabeli?

Tak. Ponieważ powierzchni interfejsu API tabel jest podobny do usługi Azure Table storage SDK, klucz partycji zapewnia skuteczny sposób dystrybucji danych. Klucz wiersza jest unikatowa w obrębie tej partycji. Klucz wiersza musi być obecny i nie może być pusta, jak standardowy zestaw SDK. Długość RowKey wynosi 255 bajtów i długość PartitionKey to 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Co to są komunikaty o błędach dla interfejsu API tabeli?

Usługa Azure Table storage i interfejsu API tabeli usługi Azure Cosmos DB używać tych samych zestawów SDK, więc większość błędów będą takie same.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Dlaczego mogę ograniczeni podczas próby utworzenia jeden po drugim wiele tabel w interfejsie API tabeli?

Usługa Azure Cosmos DB jest oparte na umowie SLA udostępniającego opóźnienia, przepływności, dostępności i gwarancje spójności. Ponieważ jest to systemowy obiekt elastycznie, rezerwuje zasobów w celu zagwarantowania tych wymagań. Szybkim tempie tworzenia tabel jest wykrywany i ograniczenia. Firma Microsoft zaleca, spójrz na kurs tworzenia tabel i obniżyć go do mniej niż 5 na minutę. Pamiętaj, że interfejs API tabel ma elastycznie systemu. Obecnie możesz aprowizować, zaczną się płacić w.

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Dla języka C# / programowanie na platformie .NET, należy używać pakietu Microsoft.Azure.Graphs lub Gremlin.NET?

Interfejs API usługi Azure Cosmos DB Gremlin wykorzystuje sterowników open source jako główny łączniki dla usługi. Dlatego zalecaną opcją jest użycie [sterowniki, które są obsługiwane przez przez witrynę Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak są rozliczane jednostek RU/s podczas uruchamiania zapytań w bazie danych programu graph?

Wszystkie obiekty programu graph, wierzchołki i krawędzie, są wyświetlane jako dokumenty JSON w wewnętrznej bazie danych. Ponieważ jednego zapytania języka Gremlin można zmodyfikować jedną lub wiele grafu obiektów w danym momencie, koszt związany z nim jest bezpośrednio związana obiektów, krawędzie, które są przetwarzane przez zapytanie. Jest to ten sam proces, który korzysta z usługi Azure Cosmos DB dla innych interfejsów API. Aby uzyskać więcej informacji, zobacz [jednostek żądań w usłudze Azure Cosmos DB](request-units.md).

Opłata za RU opiera się na zestawie roboczym danych podczas przechodzenia i ustaw nie wynik. Na przykład jeśli zapytanie ma na celu uzyskanie poszczególnych wierzchołków w wyniku, ale musi przechodzić przez więcej niż jeden inny obiekt w taki sposób, następnie koszt będzie opierać się na wszystkie obiekty programu graph, które spowoduje przejście do obliczenia wierzchołka jeden wynik.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaka jest maksymalna skala, który grafowej bazy danych może mieć w usłudze Azure Cosmos DB — interfejs API Gremlin?

Usługa Azure Cosmos DB wykorzystuje [partycjonowanie poziome](partition-data.md) automatycznie adres wzrost wymagania dotyczące magazynu i przepływności. Maksymalna przepływność i pojemność magazynu obciążenia są określane przez liczbę partycji skojarzonych z danym kontenerem. Jednak kontener interfejsu API Gremlin ma określony zestaw wytycznych, aby zapewnić prawidłowe działanie wydajności na dużą skalę. Aby uzyskać więcej informacji na temat partycjonowania i najlepsze rozwiązania, zobacz [partycjonowanie w usłudze Azure Cosmos DB](partition-data.md) artykułu.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak chronić przed atakami polegającymi na iniekcji przy użyciu języka Gremlin sterowniki?

Większość natywnych sterowników Gremlin Apache Tinkerpop umożliwia określenie słownika parametrów do wykonania zapytania. Jest to przykład jak to zrobić [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) i [Gremlin Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Dlaczego otrzymuję komunikat o błędzie "Gremlin kompilacja zapytania: Nie można znaleźć żadnej metody "błąd"?

Interfejs API usługi Azure Cosmos DB Gremlin implementuje podzbiór funkcji zdefiniowane w obszarze powierzchni języka Gremlin. Obsługiwane kroki i dodatkowe informacje, zobacz [Obsługa języka Gremlin](gremlin-support.md) artykułu.

Najlepszym obejściem tego problemu jest do przepisania wymaganych kroków języka Gremlin z obsługiwanych funkcji, ponieważ wszystkie niezbędne kroki Gremlin są obsługiwane przez usługę Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Dlaczego otrzymuję komunikat "WebSocketException: Serwer zwrócił kod stanu "200", gdy oczekiwano kodu stanu "101"?

Ten błąd prawdopodobnie jest zgłaszany, gdy jest używany nieprawidłowy punkt końcowy. Punkt końcowy, który generuje ten błąd ma następującego wzorca:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

To jest punkt końcowy dokumentów, bazy danych programu graph.  Prawidłowy punkt końcowy do użycia jest punkt końcowy języka Gremlin, który ma następujący format:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Dlaczego otrzymuję błąd "RequestRateIsTooLarge"?

Ten błąd oznacza, że przydzielone jednostek żądań na sekundę nie są wystarczająco dużo, aby obsługiwać zapytanie. Ten błąd występuje zazwyczaj, po uruchomieniu zapytania, który uzyskuje wszystkie wierzchołki:

```
// Query example:
g.V()
```

To zapytanie będzie podejmować próby pobrania wszystkich wierzchołków z wykresu. Tak koszt tego zapytania będzie równy co najmniej liczby wierzchołków pod względem jednostek RU. Aby rozwiązać tego zapytania należy dostosować ustawienie jednostek RU/s.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Dlaczego Moje połączenia sterownika Gremlin porzucane po pewnym czasie?

Za pośrednictwem połączenia protokołu WebSocket nawiązywane jest połączenie z języka Gremlin. Mimo że połączeń protokołu WebSocket nie jest określony czas wygaśnięcia, Azure Cosmos DB — interfejs API Gremlin utracą ważność bezczynnych połączeń po 30 minutach braku aktywności.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Dlaczego nie można użyć wywołania interfejsu API fluent w sterowników natywnych języka Gremlin?

Wywołania interfejsu API Fluent jeszcze nie są obsługiwane przez interfejs API języka Gremlin usługi Azure Cosmos DB. Wywołania interfejsu API Fluent wymagają wewnętrznych funkcji formatowania, znany jako kod bajtowy pomocy technicznej, która obecnie nie jest obsługiwany przez usługi Azure Cosmos DB — interfejs API Gremlin. Z powodu tego samego powodu najnowszy sterownik języka Gremlin JavaScript również nie jest obecnie obsługiwane.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Jak można ocenić wydajność zapytań w języku Gremlin?

**ExecutionProfile()** kroku (wersja zapoznawcza) może służyć do zapewnienia analizy planu wykonania zapytania. Ten krok musi być umieszczona na końcu dowolnego zapytania języka Gremlin, jak pokazano w poniższym przykładzie:

**Przykład zapytania**

```
g.V('mary').out('knows').executionProfile()
```

**Przykładowe dane wyjściowe**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Dane wyjściowe powyższego profilu pokazują, ile czasu poświęcają na uzyskanie obiektów wierzchołków, obiektów brzegowych i rozmiaru zestawu danych roboczych. Odnosi się do pomiarów ewidencyjnych dla zapytań usługi Azure Cosmos DB.

## <a id="cassandra"></a> Interfejs API rozwiązania Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Jaka jest wersja protokołu obsługiwana przez usługę Azure Cosmso DB interfejs API Cassandra? Czy istnieje plan do obsługi innych protokołów?

Interfejs Apache Cassandra API usługi Azure Cosmos DB obsługuje obecnie języka CQL w wersji 4. Jeśli masz opinię na temat obsługi innych protokołów, Daj nam znać za pośrednictwem [opinia głosowych użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db) lub Wyślij wiadomość e-mail do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Dlaczego jest wybranie przepływności dla tabeli zapotrzebowania?

Usługa Azure Cosmos DB ustawia domyślne przepływność dla kontenera, w której utworzono tabelę z - portalu lub języka CQL.
Usługa Azure Cosmos DB zapewnia gwarancji wydajności i opóźnień z górną granicę dla operacji. Gwarancja jest możliwe w przypadku, gdy aparat można wymusić nadzoru na operacje dzierżawcy. Ustawienie przepustowości zapewnia uzyskać gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tej pojemności i gwarantuje powodzenie operacji.
Elastycznie można zmienić przepustowość, aby korzystać z sezonowości aplikacji i obniżenia kosztów.

Pojęcie przepustowości została wyjaśniona w [jednostek żądań w usłudze Azure Cosmos DB](request-units.md) artykułu. Przepływność dla tabeli są dystrybuowane równomiernie między podstawowym partycje fizyczne.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Co to jest domyślne jednostek RU/s, tabeli, gdy utworzone za pomocą języka CQL? Co zrobić, jeśli należy je zmienić?

Usługa Azure Cosmos DB używa jednostki żądania na sekundę (RU/s) jako walutę zapewniające przepływności. Tabele utworzone za pomocą języka CQL mieć 400 jednostek RU. Możesz zmienić tę jednostkę, z poziomu portalu.

JĘZYKA CQL

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

### <a name="what-happens-when-throughput-is-used-up"></a>Co się stanie po wyczerpaniu przepływności?

Usługa Azure Cosmos DB zapewnia gwarancji wydajności i opóźnień z górną granicę dla operacji. Gwarancja jest możliwe w przypadku, gdy aparat można wymusić nadzoru na operacje dzierżawcy. Jest to możliwe, oparte na temat ustawiania przepływnością, co zapewnia uzyskać gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tej pojemności i gwarantuje powodzenie operacji.
Jeśli przekroczysz tej pojemności uzyskasz dostęp przeciążona komunikat o błędzie wskazujący używane pojemności zapasowej.
przeciążone 0x1001: nie można przetworzyć żądania, ponieważ "Wskaźnik żądań jest duży". W tym miejscu jest, aby zobaczyć, jakie operacje i ich wielkości powoduje, że ten problem. Możesz uzyskać pomysł o pojemności wykorzystanych przechodzi przez zaprowizowaną pojemnością z metrykami w portalu. Należy upewnić się, pojemność jest zajęta niemal równie we wszystkich partycji podstawowej. Jeśli zostanie wyświetlony, większość przepływność jest używane przez jedną partycję, masz niesymetryczność obciążenia.

Metryki są dostępne, dowiesz się, jak przepływność jest używany w ciągu godzin, dni, a na siedem dni, między partycjami lub w agregacji. Aby uzyskać więcej informacji, zobacz [monitorowania i debugowania przy użyciu metryk w usłudze Azure Cosmos DB](use-metrics.md).

Dzienniki diagnostyczne zostały wyjaśnione w [rejestrowania diagnostycznego usługi Azure Cosmos DB](logging.md) artykułu.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Czy głównej mapy kluczy do partycji kluczową koncepcją w usłudze Azure Cosmos DB?

Tak, klucz partycji służy do umieszczania jednostki w odpowiedniej lokalizacji. W usłudze Azure Cosmos DB służy można znaleźć po prawej logicznej partycji, który jest przechowywany na fizyczną partycję. Koncepcja partycjonowania również zostało wyjaśnione w [partycji i skali w usłudze Azure Cosmos DB](partition-data.md) artykułu. Podejmij podstawowe natychmiast Oto czy partycji logicznej nie powinien już dziś przechodzi przez limit 10 GB.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co się stanie, gdy pojawia się osiągnięto limit przydziału"powiadomienie wskazujące, że partycja jest zapełniony?

Usługa Azure Cosmos DB jest oparte na umowie SLA systemu, który umożliwia nieograniczone Skalowanie z gwarancji w odniesieniu do opóźnienia, przepływności, dostępności i spójności. Ta nieograniczony magazyn opiera się na skalowaniu wykorzystać dane za pomocą partycjonowania jako kluczową koncepcją. Koncepcja partycjonowania również zostało wyjaśnione w [partycji i skali w usłudze Azure Cosmos DB](partition-data.md) artykułu.

Limit 10 GB, liczby jednostek lub elementów na partycji logicznej należy przestrzegać. Aby upewnić się, że skalowanie aplikacji, zaleca się, że możesz *nie* tworzenie gorącą partycją dzięki przechowywaniu wszystkich informacji w jednej partycji i badając ją. Ten błąd może pochodzić tylko, jeśli danych jest niesymetryczna: oznacza to, że masz dużą ilość danych w jednej partycji klucza (ponad 10&nbsp;GB). Możesz znaleźć dystrybucja danych za pomocą portalu magazynu. Aby naprawić ten błąd jest ponowne utworzenie tabeli, a następnie wybierz podstawowy szczegółowe (klucz partycji), co pozwala lepiej rozkład danych.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Czy jest możliwe za pomocą usług bazy danych Cassandra API jako magazyn wartości klucza milionów lub miliardów klucze poszczególnych partycji

Usługa Azure Cosmos DB przechowywać dane nieograniczone skalowanie magazynu. Ta funkcja jest niezależna od informacji o przepływności. Tak zawsze można użyć interfejsu API rozwiązania Cassandra do przechowywania i pobierania kluczy/wartości, określając klucz podstawowy/partycji prawo. Te klucze poszczególnych Uzyskaj ich własnych partycji logicznej i znajdują się na jego podstawie fizyczną partycję bez problemów.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Czy jest możliwe utworzyć więcej niż jedną tabelę z Apache Cassandra API usługi Azure Cosmos DB?

Jest tak, można utworzyć więcej niż jednej tabeli za pomocą interfejsu API Apache Cassandra. Każda z tych tabel jest traktowany jako jednostki przepływności i magazynu.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Jest możliwe utworzyć więcej niż jednej tabeli w odstępie czasu?

Usługa Azure Cosmos DB to system zasobów podlegają działań płaszczyzny danych i kontrolki. Kontenery, takich jak kolekcje, tabele są jednostkach środowiska uruchomieniowego, które są udostępniane dla danego przepływność. Tworzenie te kontenery szybko nie jest oczekiwane działanie i ograniczenia. Jeśli testy, które listy/Tworzenie tabel natychmiast, spróbuj rozmieścić je.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Co to jest maksymalna liczba tabel, które mogą być tworzone?

Nie ma żadnego limitu fizycznych na liczbę tabel, Wyślij wiadomość e-mail na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) w przypadku dużej liczby tabel (których łączny rozmiar stały przekroczą 10 TB danych), które mają zostać utworzone na podstawie zwykle 10s lub 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Jaka jest maksymalna liczba przestrzeń kluczy, który można utworzyć?

Nie ma limitu fizycznych na liczbie keyspaces przejdą w kontenerach metadanych, Wyślij wiadomość e-mail na [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) w przypadku dużej liczby keyspaces przyczyny.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Czy jest możliwe po od normalnych tabel, Przełącz w dużej ilości danych

Pojemność magazynu odbywa się automatycznie i zwiększa się podczas wypychania w większej ilości danych. Dlatego bez obaw można zaimportować tak dużej ilości danych bez inicjowania obsługi administracyjnej węzłów i więcej i zarządzanie nimi.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Czy jest możliwe, aby podać ustawienia pliku yaml, aby skonfigurować zachowanie Apache Casssandra interfejsu API usługi Azure Cosmos DB?

Apache Cassandra API usługi Azure Cosmos DB to usługa platformy. Zapewnia zgodność z protokołem poziomu wykonywania operacji. Natychmiast ukrywa złożoność zarządzania, monitorowania i konfiguracji. Jako deweloper/użytkownika nie musisz się martwić o dostępności, reliktów, kluczy pamięci podręcznej, wiersza pamięci podręcznej, filtr rozwiniętej i wieloma innymi ustawieniami. Danych usługi Azure Cosmos DB interfejsu API Apache Cassandra skoncentrowano się na wydajność odczytu i zapisu wymaganych aplikacji bez konieczności konfigurowania i zarządzania.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Będzie interfejsu API Apache Cassandra usługi Azure Cosmos DB obsługuje węzła dodawania/klaster stanie/node stanu polecenia?

Interfejsu API Apache Cassandra to usługa platformy, która ułatwia planowanie pojemności, odpowiada na żądania elastyczności, przepływności i magazynu, szybka i bezproblemowa. Usługa Azure Cosmos DB można aprowizować przepływność mierzoną, potrzebujesz. Następnie możesz skalować ją w górę i w dół dowolną liczbę razy, za pośrednictwem dnia bez martwienia się o Dodawanie/usuwanie węzłów lub zarządzania nimi. Oznacza to, że nie musisz za pomocą węzła, narzędzie do zarządzania klastrem.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co się dzieje w odniesieniu do różnych ustawień konfiguracji do tworzenia przestrzeń kluczy, takie jak proste i sieci?

Usługa Azure Cosmos DB zapewnia globalną dystrybucję użytkowanie dostępności i małym opóźnieniu przyczyny. Nie trzeba repliki instalacji lub inne czynności. Wszystkie zapisy są zawsze trwale kworum zatwierdzone w dowolnym regionie, w którym piszesz, zapewniając gwarancje wydajności.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Co się stanie w odniesieniu do różnych ustawień dla tabeli metadanych, takich jak filtr rozwiniętej buforowania, odczytu, zmień naprawy, gc_grace, memtable_flush_period kompresji i?

Usługa Azure Cosmos DB zapewnia wydajność dla operacji odczytu/zapisu i przepustowość, bez konieczności zmiany ustawień konfiguracji i przypadkowo manipulowania nimi.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Czas wygaśnięcia (TTL) jest obsługiwane dla tabel bazy danych Cassandra?

Tak, jest obsługiwany czas wygaśnięcia.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Czy jest możliwe monitorowanie stanu węzła, stan repliki, wykaz globalny i parametrów systemu operacyjnego wcześniej przy użyciu różnych narzędzi Czego potrzebuje do monitorowanego teraz?

Usługa Azure Cosmos DB to usługa platformy, która pomaga zwiększyć produktywność i nie martw się o zarządzanie i monitorowanie infrastruktury. Potrzebujesz zadbać o przepływności, które są dostępne w portalu metryki, aby dowiedzieć się, jeśli jesteś ograniczany i zwiększyć lub zmniejszyć, że przepustowość.
Monitor [umowy SLA](monitor-accounts.md).
Użyj [metryki](use-metrics.md) użyj [dzienniki diagnostyczne](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Które zestawy SDK klientów można pracować z Apache Cassandra API usługi Azure Cosmos DB?

Na potrzeby programów klienckich użyto sterowników klienta zestawu Apache Cassandra SDK, które korzystają z CQLv3. Jeśli masz inne sterowniki, które używane, lub Jeśli napotykasz problemy, Wyślij wiadomość e-mail do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Klucz partycji złożonego jest obsługiwana?

Tak, można użyć regularnych składni można utworzyć klucza złożonego partycji.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Czy mogę używać sstableloader do ładowania danych?

Nie, sstableloader nie jest obsługiwany.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Czy lokalny Klaster Apache Cassandra jest sparowany z interfejs API Cassandraem Azure Cosmos DBu?

Na obecne usługi Azure Cosmos DB zawiera zoptymalizowany środowisko, w środowisku chmury bez koszty operacji. Jeśli potrzebujesz parowania, Wyślij wiadomość e-mail do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) z opisem Twojego scenariusza. Pracujemy nad ofertą, aby pomóc w sparowaniu lokalnego/różnego klastra Cassandra w chmurze z usługą Cosomos interfejs API Cassandra DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Interfejs API rozwiązania Cassandra zapewnia pełne kopie zapasowe?

Usługa Azure Cosmos DB oferuje dwa bezpłatne pełnej kopii zapasowych wykonanych interwałem cztery godziny już dziś wszystkich interfejsów API. Dzięki temu nie trzeba skonfigurować harmonogram tworzenia kopii zapasowych i innymi.
Jeśli chcesz zmodyfikować i częstotliwość przechowywania, Wyślij wiadomość e-mail do [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) wywoływać zgłoszenia do pomocy technicznej. Informacje o możliwości tworzenia kopii zapasowej znajduje się w [automatyczne tworzenie kopii zapasowej online i przywracanie za pomocą usługi Azure Cosmos DB](online-backup-and-restore.md) artykułu.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak konto interfejsu API rozwiązania Cassandra obsługuje tryb failover Jeśli region ulegnie awarii?

Interfejs API usługi Azure Cosmos DB Cassandra obiektowy globalnie rozproszoną platformę usługi Azure Cosmos DB. Aby upewnić się, że Twoja aplikacja może tolerować przestój centrum danych, należy włączyć co najmniej jeden region więcej dla konta w portalu usługi Azure Cosmos DB [opracowywanie zawartości przy użyciu kont usługi Azure Cosmos DB w wielu regionach](high-availability.md). Można ustawić priorytet regionu za pomocą portalu [opracowywanie zawartości przy użyciu kont usługi Azure Cosmos DB w wielu regionach](high-availability.md).

Możesz dodać dowolną liczbę regionów, jak konta i kontrolować, gdzie go może zostać przeniesiony do, zapewniając priorytetem trybu failover. Korzystanie z bazy danych, musisz podać aplikacji istnieje zbyt. Jeśli tak zrobisz, nie będzie przestoju swoich klientów.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Powoduje interfejsu API Apache Cassandra indeksowania domyślnie wszystkie atrybuty jednostki

Interfejs API Cassandra planowania obsługi indeksowania pomocniczego, aby ułatwić tworzenie selektywnego indeksu dla niektórych atrybutów. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Czy można użyć nowego zestawu SDK interfejsu API rozwiązania Cassandra lokalnie w emulatorze?

Tak, jest to obsługiwane.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB jako platforma prawdopodobnie ma wiele możliwości, takich jak źródło zmian i inne funkcje. Te funkcje zostaną dodane do interfejsu API rozwiązania Cassandra?

Interfejs Apache Cassandra API zapewnia taką samą funkcjonalność języka CQL jak Apache Cassandra. Planujemy zbadać możliwości obsługi różnych funkcji w przyszłości.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funkcja x regularne interfejsu API rozwiązania Cassandra nie działa jak dzisiaj, w którym opinii można podać?

Przekazać opinię za pośrednictwem [opinia głosowych użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
