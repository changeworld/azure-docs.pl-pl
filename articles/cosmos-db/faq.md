---
title: Często zadawane pytania dotyczące różnych interfejsów API w usłudze Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące Azure Cosmos DB, globalnie dystrybuowanej, wielomodelowej usługi bazy danych. Dowiedz się więcej o pojemności, poziomach wydajności i skalowaniu.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 15e4fc3ce412807908c75fe25acecac0fe86d261
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262798"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące różnych interfejsów API w usłudze Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jakie są typowe przypadki użycia dla Azure Cosmos DB?

Azure Cosmos DB to dobry wybór w przypadku nowych aplikacji sieci Web, mobilnych, gier i IoT, w których automatyczne skalowanie, przewidywalna wydajność, szybka kolejność milisekund czas odpowiedzi i możliwość wykonywania zapytań dotyczących danych bez schematu jest ważna. Azure Cosmos DB nadają sobie możliwość szybkiego tworzenia i obsługi ciągłej iteracji modeli danych aplikacji. Aplikacje zarządzające zawartością i danymi generowanymi przez użytkownika są [typowymi przypadkami użycia dla Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak Azure Cosmos DB oferować przewidywalną wydajność?

[Jednostka żądania](request-units.md) (ru) to miara przepływności w Azure Cosmos DB. Przepływność 1RU odpowiada przepływności pobrania dokumentu o wartości 1 KB. Każda operacja w Azure Cosmos DB, w tym operacje odczytu, zapisu, zapytania SQL i wykonania procedury składowanej, ma wartość deterministyczną RU, która jest oparta na przepływności wymaganej do ukończenia tej operacji. Zamiast zastanawiać się nad procesorami CPU, we/wy i pamięcią oraz jak każdy ma wpływ na przepływność aplikacji, można myśleć o pojedynczej mierze RU.

Każdy kontener usługi Azure Cosmos można skonfigurować przy użyciu alokowanej przepływności w zakresie jednostek ru przepływności na sekundę. W przypadku aplikacji dowolnej skali można testować pojedyncze żądania, aby zmierzyć ich wartości RU i udostępnić kontener do obsługi łącznej liczby jednostek żądań we wszystkich żądaniach. Możesz również skalować w górę lub w dół przepływność kontenera w miarę rozwoju aplikacji. Aby uzyskać więcej informacji na temat jednostek żądania i pomocy przy określaniu potrzeb kontenera, wypróbuj [Kalkulator przepływności](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak Azure Cosmos DB obsługuje różne modele danych, takie jak key/value, kolumnowy, Document i Graph?

Modele danych typu klucz/wartość (tabela), kolumnowy, dokument i Graph są obsługiwane natywnie z powodu projektu ARS (Atoms, Records i sequences), który jest zbudowany Azure Cosmos DB. Atomy, rekordy i sekwencje mogą być łatwo mapowane i rzutowane na różne modele danych. Interfejsy API dla podzestawu modeli są dostępne teraz (SQL, MongoDB, Table i Gremlin), a inne specyficzne dla dodatkowych modeli danych będą dostępne w przyszłości.

Azure Cosmos DB ma aparat indeksowania niezależny od schematu, który umożliwia automatyczne indeksowanie wszystkich danych, które pozyskają, bez konieczności stosowania żadnych indeksów schematu lub pomocniczych od dewelopera. Aparat opiera się na zestawie logicznych układów indeksów (odwróconych, kolumnowych, drzewa), które oddzielają układ magazynu od podsystemów indeksu i przetwarzania zapytań. Cosmos DB ma także możliwość obsługi zestawu protokołów sieci i interfejsów API w sposób rozszerzalny i przetłumaczy je wydajnie na podstawowy model danych (1) i logiczne układy indeksów (2), dzięki czemu można jednoznacznie obsługiwać więcej niż jeden model danych w sposób natywny.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Czy można używać wielu interfejsów API do uzyskiwania dostępu do danych?

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. W przypadku, gdy wiele modeli oznacza, Azure Cosmos DB obsługuje wiele interfejsów API i wielu modeli danych, różne interfejsy API używają różnych formatów danych dla magazynu i protokołu przewodowego. Na przykład SQL używa JSON, MongoDB używa BSON, tabela używa modelu EDM, Cassandra używa CQL, Gremlin używa GraphSON. W związku z tym zalecamy używanie tego samego interfejsu API do uzyskiwania dostępu do danych w danym koncie.

Każdy interfejs API działa niezależnie, z wyjątkiem interfejsu API Gremlin i SQL, które są współobsługiwane.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Czy Azure Cosmos DB jest zgodny z HIPAA?

Tak, Azure Cosmos DB jest zgodny z HIPAA. Ustawa HIPAA określa wymogi dotyczące wykorzystywania, ujawniania i ochrony informacji o zdrowiu umożliwiających indywidualną identyfikację osoby. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jakie są limity magazynu Azure Cosmos DB?

Nie ma limitu całkowitej ilości danych, które kontener może przechowywać w Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jakie są limity przepływności Azure Cosmos DB?

Nie ma limitu całkowitej ilości przepływności obsługiwanej przez kontener w Azure Cosmos DB. Najważniejszym pomysłem jest równomierne rozłożenie obciążenia na wystarczająco dużą liczbę kluczy partycji.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Czy tryby łączności bezpośredniej i bramy są szyfrowane?

Tak Oba tryby są zawsze w pełni szyfrowane.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Ile kosztuje Azure Cosmos DB?

Szczegółowe informacje można znaleźć na stronie [szczegóły cennika Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Opłaty za użycie Azure Cosmos DB są określane przez liczbę kontenerów z zainicjowaną obsługą, liczbę godzin, w których kontenery były w trybie online, oraz zainicjowaną przepływność dla każdego kontenera.

### <a name="is-a-free-account-available"></a>Czy jest dostępne bezpłatne konto?

Tak, możesz utworzyć konto w ograniczonym czasie bez opłat bez żadnych zobowiązań. Aby się zarejestrować, odwiedź stronę [Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) lub Przeczytaj więcej na stronie [Wypróbuj Azure Cosmos DB często zadawane pytania](#try-cosmos-db).

Jeśli dopiero zaczynasz korzystać z platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), które zapewnia 30 dni i środki do wypróbowania wszystkich usług platformy Azure. Jeśli masz subskrypcję programu Visual Studio, możesz również uzyskać bezpłatne środki na korzystanie z [platformy Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) do użycia w dowolnej usłudze platformy Azure.

Możesz również użyć [emulatora Azure Cosmos DB](local-emulator.md) , aby bezpłatnie opracowywać i testować swoją aplikację lokalnie, bez tworzenia subskrypcji platformy Azure. Jeśli sposób działania aplikacji w emulatorze usługi Azure Cosmos DB jest zadowalający, możesz zacząć korzystać z konta usługi Azure Cosmos DB w chmurze.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak uzyskać dodatkową pomoc dotyczącą Azure Cosmos DB?

Aby zadać pytanie techniczne, możesz ogłosić na jednym z tych pytań i odpowiedzi na forach:

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow jest Najlepsza w przypadku pytań związanych z programowaniem. Upewnij się, że pytanie jest [w temacie](https://stackoverflow.com/help/on-topic) i [Podaj jak najwięcej szczegółów, dzięki czemu pytanie jest jasne i możliwe do odpowiedzi](https://stackoverflow.com/help/how-to-ask).

Aby zażądać nowych funkcji, Utwórz nowe żądanie na [głos użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.

Inne pytania można przesłać do zespołu w [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). nie jest to jednak alias pomocy technicznej.

## <a id="try-cosmos-db"></a>Wypróbuj Azure Cosmos DB subskrypcje

Możesz teraz korzystać z ograniczonych do czasu Azure Cosmos DB środowiska bez subskrypcji i zobowiązań. Aby zarejestrować się w celu uzyskania subskrypcji usługi try Azure Cosmos DB, przejdź do [bezpłatnej usługi Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Ta subskrypcja jest oddzielona od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/)i może być używana razem z bezpłatną wersją próbną platformy Azure lub subskrypcją płatną platformy Azure.

Wypróbuj Azure Cosmos DB subskrypcje pojawiają się w obszarze Azure Portal dalej inne subskrypcje skojarzone z IDENTYFIKATORem użytkownika.

Aby wypróbować subskrypcje Azure Cosmos DB, mają zastosowanie następujące warunki:

* Jeden [kontener aprowizacji o przepływności](./set-throughput.md#set-throughput-on-a-container) na subskrypcję usług SQL, Gremlin API i Table accounts.
* Maksymalnie trzy [kolekcje inicjowane przez przepływność](./set-throughput.md#set-throughput-on-a-container) na subskrypcję dla kont usługi MongoDB.
* Jedna [baza danych z obsługą przepływności](./set-throughput.md#set-throughput-on-a-database) na subskrypcję. Bazy danych z obsługą przepływności mogą zawierać dowolną liczbę kontenerów wewnątrz.
* Pojemność 10 GB pamięci masowej.
* Replikacja globalna jest dostępna w następujących [regionach świadczenia usługi Azure](https://azure.microsoft.com/regions/): środkowe stany USA, Europa Północna i Azja Południowo-Wschodnia
* Maksymalna przepływność 5 K/s w przypadku aprowizacji na poziomie kontenera.
* Maksymalna przepływność 20 K/s w przypadku aprowizacji na poziomie bazy danych.
* Subskrypcje wygasną po upływie 30 dni i można je przedłużyć do maksymalnie 31 dni.
* Nie można utworzyć biletów pomocy technicznej platformy Azure dla usług try Azure Cosmos DB. Jednak pomoc techniczna jest świadczona dla subskrybentów z istniejącymi planami pomocy technicznej.

## <a name="set-up-azure-cosmos-db"></a>Skonfiguruj Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak mogę zarejestrować się w usłudze Azure Cosmos DB?

Azure Cosmos DB jest dostępna w Azure Portal. Najpierw Zarejestruj się, aby uzyskać subskrypcję platformy Azure. Po zarejestrowaniu możesz dodać konto Azure Cosmos DB do subskrypcji platformy Azure.

### <a name="what-is-a-master-key"></a>Co to jest klucz główny?

Klucz główny jest tokenem zabezpieczającym dostęp do wszystkich zasobów dla konta. Osoby z kluczem mają dostęp do odczytu i zapisu do wszystkich zasobów na koncie bazy danych. Podczas dystrybucji kluczy głównych należy zachować ostrożność. Podstawowy klucz główny i pomocniczy klucz główny są dostępne w bloku **klucze** [Azure Portal][azure-portal]. Aby uzyskać więcej informacji na temat kluczy, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Do jakich regionów można ustawić PreferredLocations?

Wartość PreferredLocations można ustawić na dowolne regiony platformy Azure, w których Cosmos DB jest dostępna. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Czy jest coś, co należy wiedzieć, gdy rozpowszechniasz dane na całym świecie za pośrednictwem centrów danych platformy Azure?

Azure Cosmos DB jest dostępna we wszystkich regionach świadczenia usługi Azure, jak określono na stronie [regionów platformy Azure](https://azure.microsoft.com/regions/) . Ponieważ jest to podstawowa usługa, każde nowe centrum danych ma Azure Cosmos DB obecność.

Po ustawieniu regionu należy pamiętać, że Azure Cosmos DB respektuje ona chmury suwerenne i rządowe. Oznacza to, że w przypadku utworzenia konta w [suwerennym regionie](https://azure.microsoft.com/global-infrastructure/)nie można przeprowadzić replikacji poza ten [suwerenny region](https://azure.microsoft.com/global-infrastructure/). Podobnie nie można włączyć replikacji do innych suwerennych lokalizacji na koncie zewnętrznym.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Czy jest możliwe przełączenie z aprowizacji na poziomie kontenera do aprowizacji na poziomie bazy danych? Lub odwrotnie

Alokacja przepływności na poziomie kontenera i bazy danych to osobne oferty i przełączanie między tymi, które wymagają migrowania danych ze źródła do miejsca docelowego. Co oznacza, że trzeba utworzyć nową bazę danych lub nowy kontener, a następnie przeprowadzić migrację danych przy użyciu [biblioteki wykonawczy zbiorczej](bulk-executor-overview.md) lub [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Czy usługa Azure CosmosDB obsługuje analizę szeregów czasowych?

Tak, aby usługa Azure CosmosDB obsługiwała analizę szeregów czasowych, Oto przykład dla [wzorca szeregów czasowych](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Ten przykład pokazuje, jak używać kanału informacyjnego zmiany do kompilowania zagregowanych widoków w danych szeregów czasowych. Tę metodę można rozłożyć przy użyciu strumienia Spark lub innego procesora danych strumienia.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Jakie są limity przydziału usługi Azure Cosmos DB i przepływności

Aby uzyskać więcej informacji, zobacz [przydziały usługi](concepts-limits.md) Azure Cosmos DB i [limity dotyczące poszczególnych kontenerów i baz danych](set-throughput.md#comparison-of-models) .

## <a name="sql-api"></a>Interfejs SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak mogę rozpocząć programowanie względem interfejsu API SQL?

Najpierw musisz zarejestrować się, aby uzyskać subskrypcję platformy Azure. Po zarejestrowaniu się w celu uzyskania subskrypcji platformy Azure Możesz dodać do niej kontener interfejsu API SQL. Aby uzyskać instrukcje dotyczące dodawania konta Azure Cosmos DB, zobacz [Tworzenie konta bazy danych usługi Azure Cosmos](create-sql-api-dotnet.md#create-account).

[Zestawy SDK](sql-api-sdk-dotnet.md) są dostępne dla języków .NET, Python, Node.js, JavaScript i Java. Deweloperzy mogą również używać [interfejsów API RESTful http](/rest/api/cosmos-db/) do współpracy z zasobami Azure Cosmos DB z różnych platform i języków.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Czy mogę uzyskać dostęp do gotowych przykładów, aby zacząć od początku?

Przykłady [dla interfejsów API](sql-api-dotnet-samples.md)SQL, [Java](https://github.com/Azure/azure-documentdb-java), [Node. js](sql-api-nodejs-samples.md)i [Python](sql-api-python-samples.md) SDK są dostępne w serwisie GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Czy baza danych SQL API obsługuje dane bez schematu?

Tak. interfejs API SQL umożliwia aplikacjom przechowywanie dowolnych dokumentów JSON bez definicji schematu ani wskazówek. Dane są natychmiast dostępne dla zapytań za pomocą interfejsu zapytania SQL Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>Czy interfejs API SQL obsługuje transakcje dotyczące kwasu?

Tak, interfejs API SQL obsługuje transakcje między dokumentami wyrażone jako procedury i wyzwalacze przechowywane w języku JavaScript. Transakcje są objęte zakresem pojedynczej partycji w ramach każdego kontenera i wykonywane z użyciem semantyki KWASowej jako "All lub Nothing", odizolowane od innych współbieżnie wykonywanych żądań kodu i użytkowników. Jeśli wyjątki są zgłaszane przez wykonanie kodu aplikacji JavaScript po stronie serwera, cała transakcja zostanie wycofana. 

### <a name="what-is-a-container"></a>Co to jest kontener?

Kontener jest grupą dokumentów i skojarzonych z nimi logiki aplikacji JavaScript. Kontener jest jednostką rozliczaną, w której [koszt](performance-levels.md) jest określany przez przepływność i użycie magazynu. Kontenery mogą obejmować co najmniej jedną partycję lub serwery i mogą być skalowane w celu obsługi praktycznie nieograniczonego wolumenu magazynu lub przepływności.

* W przypadku interfejsu API SQL kontener jest mapowany do kontenera.
* Dla interfejsu API Cosmos DB dla kont MongoDB, kontener jest mapowany do kolekcji.
* W przypadku kont Cassandra i interfejs API tabel kontener jest mapowany na tabelę.
* W przypadku kont interfejsu API Gremlin kontener jest mapowany na wykres.

Kontenery są również jednostkami rozliczeń dla Azure Cosmos DB. Każdy kontener jest rozliczany godzinowo, w oparciu o zainicjowaną przepływność i ilość miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Jak utworzyć bazę danych?

Bazy danych można tworzyć przy użyciu [Azure Portal](https://portal.azure.com), zgodnie z opisem w [sekcji Dodawanie kontenera](create-sql-api-java.md#add-a-container), jednego z [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak skonfigurować użytkowników i uprawnienia?

Możesz tworzyć użytkowników i uprawnienia za pomocą jednego z [Cosmos DB zestawów SDK interfejsu API](sql-api-sdk-dotnet.md) lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Czy interfejs API SQL obsługuje kod SQL?

Język zapytań SQL obsługiwany przez konta interfejsu API SQL to ulepszony podzbiór funkcji zapytania, które są obsługiwane przez SQL Server. Język zapytań SQL Azure Cosmos DB zapewnia zaawansowane operatory hierarchiczne i relacyjne oraz rozszerzalność za pośrednictwem opartych na języku JavaScript funkcji zdefiniowanych przez użytkownika (UDF). Gramatyka JSON umożliwia modelowanie dokumentów JSON jako drzew z węzłami oznaczonymi etykietami, które są używane przez zarówno Azure Cosmos DB technik automatycznego indeksowania i dialekt zapytań SQL Azure Cosmos DB. Aby uzyskać informacje o używaniu gramatyki SQL, zobacz artykuł dotyczący [zapytań SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Czy interfejs API SQL obsługuje funkcje agregacji SQL?

Interfejs API SQL obsługuje agregację o małym opóźnieniu w dowolnej skali za pośrednictwem funkcji agregujących `COUNT`, `MIN`, `MAX`, `AVG` i `SUM` za pośrednictwem gramatyki SQL. Aby uzyskać więcej informacji, zobacz [funkcje agregujące](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak interfejs API SQL udostępnia współbieżność?

Interfejs API SQL obsługuje optymistyczną kontrolę współbieżności (OCC) za pośrednictwem tagów jednostek HTTP lub elementów ETag. Każdy zasób interfejsu API SQL ma element ETag, a element ETag jest ustawiany na serwerze za każdym razem, gdy dokument zostanie zaktualizowany. Nagłówek ETag i bieżąca wartość są uwzględniane we wszystkich komunikatach odpowiedzi. Elementy ETag mogą być używane z nagłówkiem if-Match, aby umożliwić serwerowi podjęcie decyzji o tym, czy zasób powinien zostać zaktualizowany. Wartość if-Match jest wartością ETag, która ma zostać sprawdzona. Jeśli wartość ETag jest zgodna z wartością ETag serwera, zasób zostanie zaktualizowany. Jeśli element ETag nie jest już aktualny, serwer odrzuca operację z kodem odpowiedzi "HTTP 412 niepowodzenie warunku wstępnego". Klient ponownie pobierze zasób, aby uzyskać bieżącą wartość ETag dla zasobu. Ponadto elementy ETag mogą być używane z nagłówkiem If-None-Match, aby określić, czy konieczne jest ponowne pobranie zasobu.

Aby użyć optymistycznej współbieżności w programie .NET, należy użyć klasy [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Aby zapoznać się z przykładem platformy .NET, zobacz [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) w przykładzie DocumentManagement w witrynie GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak mogę wykonywać transakcji w interfejsie API SQL?

Interfejs API SQL obsługuje transakcje zintegrowane z językiem za pomocą procedur i wyzwalaczy przechowywanych w języku JavaScript. Wszystkie operacje bazy danych wewnątrz skryptów są wykonywane w ramach izolacji migawki. Jeśli jest to kontener o pojedynczej partycji, wykonanie jest ograniczone do kontenera. W przypadku partycjonowania kontenera wykonywanie jest ograniczone do dokumentów o tej samej wartości klucza partycji w kontenerze. Migawka wersji dokumentów (elementy ETag) jest wykonywana na początku transakcji i zatwierdzana tylko wtedy, gdy działanie skryptu zakończy się pomyślnie. Jeśli kod JavaScript zwraca błąd, transakcja zostaje wycofana. Aby uzyskać więcej informacji, zobacz [programowanie JavaScript po stronie serwera dla Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak można zbiorczo wstawiać dokumenty do Cosmos DB?

Można zbiorczo wstawiać dokumenty do Azure Cosmos DB w jeden z następujących sposobów:

* Narzędzie do wykonywania zbiorczego, zgodnie z opisem w temacie Korzystanie z biblioteki wykonawczej [programu .NET](bulk-executor-dot-net.md) i [Używanie zbiorczej procedury wykonawcze środowiska Java](bulk-executor-java.md)
* Narzędzie do migracji danych, zgodnie z opisem w [narzędziu do migracji bazy danych dla Azure Cosmos DB](import-data.md).
* Procedury składowane, zgodnie z opisem w [programowaniu JavaScript po stronie serwera dla Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Czy interfejs API SQL obsługuje buforowanie linków zasobów?

Tak, ponieważ Azure Cosmos DB jest usługą RESTful, linki zasobów są niezmienne i mogą być buforowane. Klienci interfejsu API SQL mogą określić nagłówek "If-None-Match", aby odczytywać wszystkie dokumenty lub kontenery podobne do zasobów, a następnie zaktualizować ich kopie lokalne po zmianie wersji serwera.

### <a name="is-a-local-instance-of-sql-api-available"></a>Czy jest dostępne lokalne wystąpienie interfejsu API SQL?

Tak. [Emulator Azure Cosmos DB](local-emulator.md) zapewnia emulację o wysokiej dokładności usługi Cosmos DB. Obsługuje ona funkcje identyczne z Azure Cosmos DB, w tym obsługę tworzenia i wykonywania zapytań dotyczących dokumentów JSON, aprowizacji i skalowania kolekcji oraz wykonywania procedur składowanych i wyzwalaczy. Możesz tworzyć i testować aplikacje za pomocą emulatora Azure Cosmos DB i wdrażać je na platformie Azure na skalę globalną, wprowadzając jedną zmianę konfiguracji do punktu końcowego połączenia dla Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Dlaczego są długotrwałe wartości zmiennoprzecinkowe w dokumencie zaokrąglonym podczas wyświetlania z Eksploratora danych w portalu.

Jest to ograniczenie języka JavaScript. Kod JavaScript używa liczb zmiennoprzecinkowych o podwójnej precyzji, jak określono w standardzie IEEE 754 i może bezpiecznie przechowywać cyfry od-(2<sup>53</sup> -1) i 2<sup>53</sup>-1 (tj. 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Gdzie są dozwolone uprawnienia w hierarchii obiektów?

Tworzenie uprawnień przy użyciu ResourceTokens jest dozwolone na poziomie kontenera i jego elementach podrzędnych (takich jak dokumenty, załączniki). Oznacza to, że próba utworzenia uprawnienia do bazy danych lub poziomu konta nie jest obecnie dozwolona.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Co to jest interfejs API Azure Cosmos DB dla MongoDB?

Interfejs API Azure Cosmos DB dla MongoDB to warstwa zgodności protokołu przewodowego, która umożliwia aplikacjom łatwe i nieprzezroczyste komunikowanie się z natywnym aparatem bazy danych Cosmos platformy Azure przy użyciu istniejących, obsługiwanych przez społeczność zestawów SDK i sterowników dla MongoDB. Deweloperzy mogą teraz używać istniejących MongoDB łańcuchy narzędzi i umiejętności do kompilowania aplikacji, które korzystają z Azure Cosmos DB. Deweloperzy mogą korzystać z unikatowych możliwości Azure Cosmos DB, które obejmują dystrybucję globalną z replikacją obejmującą wiele wzorców, funkcję autoindeksowania, konserwację kopii zapasowej, finansowo objęte umową dotyczącą poziomu usług (umowy SLA) itp.

### <a name="how-do-i-connect-to-my-database"></a>Jak mogę połączyć się z moją bazą danych?

Najszybszym sposobem łączenia się z bazą danych Cosmos Azure Cosmos DB z interfejsem API programu MongoDB jest przełączenie do [Azure Portal](https://portal.azure.com). Przejdź do swojego konta, a następnie w menu nawigacji po lewej stronie kliknij pozycję **Szybki Start**. Szybki Start to najlepszy sposób na uzyskanie fragmentów kodu w celu nawiązania połączenia z bazą danych.

Azure Cosmos DB wymusza ścisłe wymagania i standardy dotyczące zabezpieczeń. Konta Azure Cosmos DB wymagają uwierzytelniania i bezpiecznej komunikacji za pośrednictwem protokołu SSL, dlatego należy używać TLSv 1.2.

Aby uzyskać więcej informacji, zobacz [łączenie się z bazą danych Cosmos przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Czy istnieją dodatkowe kody błędów, z którymi należy się zająć, przy użyciu interfejsu API Azure Cosmos DB dla MongoDB?

Wraz ze wspólnymi kodami błędów MongoDB interfejs API Azure Cosmos DB dla MongoDB ma własne konkretne kody błędów:

| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba zużytych jednostek żądania jest większa niż stawka żądania aprowizacji dla kontenera i została ograniczona. | Rozważ przeskalowanie przepływności przypisanej do kontenera lub zestawu kontenerów z Azure Portal lub ponowienie próby. |
| ExceededMemoryLimit | 16501 | W ramach usługi wielodostępnej Operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteriów zapytania lub skontaktuj się z pomocą techniczną z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Przykład: <em> @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4DB. getcollection ("Users"). Aggregate ([<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$match: {Name: "Adam"}}, <br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$sort: {Age:-1}}<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3]) </em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Czy sterownik Simba dla MongoDB jest obsługiwany z interfejsem API Azure Cosmos DB dla MongoDB?

Tak, możesz użyć sterownika Simba Mongo ODBC z interfejsem API Azure Cosmos DB dla MongoDB

## <a id="table"></a>Interfejs API tabel

### <a name="how-can-i-use-the-table-api-offering"></a>Jak mogę korzystać z oferty interfejs API tabel?

Interfejs API tabel Azure Cosmos DB jest dostępny w [Azure Portal][azure-portal]. Najpierw musisz zarejestrować się, aby uzyskać subskrypcję platformy Azure. Po zarejestrowaniu możesz dodać konto Azure Cosmos DB interfejs API tabel do subskrypcji platformy Azure, a następnie dodać tabele do swojego konta.

Obsługiwane języki i skojarzone szybkie uruchomienia można znaleźć w temacie [wprowadzenie do Azure Cosmos DB interfejs API tabel](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Czy potrzebuję nowego zestawu SDK, aby użyć interfejs API tabel?

Nie, istniejące zestawy SDK magazynu powinny nadal funkcjonować. Zaleca się jednak, aby zawsze pobierać najnowsze zestawy SDK dla najlepszej obsługi i w wielu przypadkach o najwyższej wydajności. Zapoznaj się z listą dostępnych języków w artykule [wprowadzenie do Azure Cosmos DB interfejs API tabel](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Gdzie interfejs API tabel nie jest identyczna z zachowaniem usługi Azure Table Storage?

Istnieją pewne różnice w zachowaniu, które użytkownicy pochodzą z usługi Azure Table Storage, którzy chcą tworzyć tabele z Azure Cosmos DB interfejs API tabel powinny mieć świadomość:

* Azure Cosmos DB interfejs API tabel wykorzystuje zastrzeżony model pojemności, aby zapewnić zagwarantowaną wydajność, ale oznacza to, że jeden płaci dla pojemności zaraz po utworzeniu tabeli, nawet jeśli pojemność nie jest używana. Za pomocą usługi Azure Table Storage jest stosowana tylko do używanej pojemności. Pomaga to w wyjaśnieniu, dlaczego interfejs API tabel może oferować 10 ms umów SLA do odczytu i 15 MS w 99 percentylu, podczas gdy usługa Azure Table Storage oferuje 10-sekundową umowę SLA. Ale w związku z tym interfejs API tabel tabele, nawet puste tabele bez żadnych żądań, koszt finansowy w celu zapewnienia, że wydajność jest dostępna do obsługi dowolnych żądań do nich w ramach umowy SLA oferowanej przez Azure Cosmos DB.
* Wyniki zapytania zwrócone przez interfejs API tabel nie są posortowane w kolejności kluczy/wierszy partycji, ponieważ znajdują się w usłudze Azure Table Storage.
* Klucze wierszy mogą zawierać maksymalnie 255 bajtów
* Wsady mogą mieć maksymalnie 2 MB
* Mechanizm CORS nie jest obecnie obsługiwany
* W nazwach tabel w usłudze Azure Table Storage nie jest rozróżniana wielkość liter, ale znajdują się one w Azure Cosmos DB interfejs API tabel
* Niektóre formaty wewnętrzne Azure Cosmos DB dla informacji o kodowaniu, takie jak pola binarne, nie są obecnie takie same jak takie, jak takie. W związku z tym może to spowodować nieoczekiwane ograniczenia rozmiaru danych. Na przykład obecnie nie można użyć pełnej MB jednostki tabeli do przechowywania danych binarnych, ponieważ kodowanie zwiększa rozmiar danych.
* Nazwa właściwości jednostki "ID" jest obecnie nieobsługiwana
* TableQuery TakeCount nie jest ograniczona do 1000

W przypadku interfejsu API REST istnieje kilka punktów końcowych/opcji zapytania, które nie są obsługiwane przez Azure Cosmos DB interfejs API tabel:

| Metody REST | Punkt końcowy REST/opcja zapytania | Adresy URL doc | Wyjaśnienie |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /? restype = service@comp = właściwości| [Ustawianie właściwości usługi Table Service](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) i [pobieranie właściwości usługi Table Service](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ten punkt końcowy służy do ustawiania reguł CORS, konfiguracji analizy magazynu i ustawień rejestrowania. Mechanizm CORS nie jest obecnie obsługiwany, a analiza i rejestrowanie są obsługiwane inaczej w Azure Cosmos DB niż tabele usługi Azure Storage |
| Opcje | / @ no__t-1Table-Resource-Name > | [Żądanie tabeli CORS przed lotem](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Jest to część mechanizmu CORS, który Azure Cosmos DB obecnie nie jest obsługiwany. |
| GET | /? restype = service@comp = Statystyka | [Pobierz statystyki usługi Table Service](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Zawiera informacje o tym, jak szybko dane są replikowane między podstawowymi i pomocniczymi elementami. Nie jest to wymaganie w Cosmos DB, ponieważ replikacja jest częścią operacji zapisu. |
| GET, PUT | /MyTable? COMP = lista ACL | [Pobieranie listy ACL tabel](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) i [Ustawianie listy ACL tabel](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Spowoduje to pobranie i ustawienie przechowywanych zasad dostępu używanych do zarządzania sygnaturami dostępu współdzielonego (SAS). Chociaż sygnatura dostępu współdzielonego jest obsługiwana, są one ustawiane i zarządzane w różny sposób. |

Ponadto Azure Cosmos DB interfejs API tabel obsługuje tylko format JSON, a nie ATOM.

Chociaż Azure Cosmos DB obsługuje sygnatury dostępu współdzielonego (SAS), istnieją pewne zasady, które nie są obsługiwane, a w odniesieniu do operacji zarządzania, takich jak prawo do tworzenia nowych tabel.

W przypadku zestawu .NET SDK w szczególności istnieją klasy i metody, które Azure Cosmos DB nie są obecnie obsługiwane.

| Klasa | Nieobsługiwana Metoda |
|-------|-------- |
| CloudTableClient | @no__t – 0ServiceProperties * |
|                  | @no__t – 0ServiceStats * |
| CloudTable | SetPermissions * |
|            | Getpermissionss * |
| TableServiceContext | * (Ta klasa jest przestarzała) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Jeśli dowolna z tych różnic jest problemem dla projektu, skontaktuj się z firmą [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) i daj nam znać.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak mogę przekazać opinię na temat zestawu SDK lub usterek?

Swoją opinię można podzielić na następujące sposoby:

* [Głos użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow jest Najlepsza w przypadku pytań związanych z programowaniem. Upewnij się, że pytanie jest [w temacie](https://stackoverflow.com/help/on-topic) i [Podaj jak najwięcej szczegółów, dzięki czemu pytanie jest jasne i możliwe do odpowiedzi](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Jakie są parametry połączenia potrzebne do nawiązania połączenia z interfejs API tabel?

Parametry połączenia:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Parametry połączenia można uzyskać ze strony parametrów połączenia w Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak mogę zastąpić ustawienia konfiguracji dla opcji żądania w zestawie .NET SDK dla interfejs API tabel?

Niektóre ustawienia są obsługiwane przez metodę CreateCloudTableClient i inne za pośrednictwem pliku App. config w sekcji appSettings w aplikacji klienckiej. Informacje o ustawieniach konfiguracji można znaleźć w temacie [Azure Cosmos DB Capabilities](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Czy istnieją jakieś zmiany klientów korzystających z istniejących zestawów SDK usługi Azure Table Storage?

Brak. Nie wprowadzono żadnych zmian dla istniejących lub nowych klientów korzystających z istniejących zestawów SDK usługi Azure Table Storage.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Jak mogę przeglądać dane tabeli przechowywane w Azure Cosmos DB do użycia z interfejs API tabelem?

Możesz użyć Azure Portal, aby przeglądać dane. Możesz również użyć kodu interfejs API tabel lub narzędzi wymienionych w następnej odpowiedzi.

### <a name="which-tools-work-with-the-table-api"></a>Które narzędzia współpracują z interfejs API tabel?

Możesz użyć [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Narzędzia z elastycznością do łączenia parametrów połączenia w określonym wcześniej formacie mogą obsługiwać nowe interfejs API tabel. Lista narzędzi tabel jest dostępna na stronie [narzędzia klienta usługi Azure Storage](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>Czy współbieżność w działaniu kontrolowanym przez operacje?

Tak, współbieżność optymistyczna jest zapewniana przy użyciu mechanizmu ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Czy model zapytania OData jest obsługiwany dla jednostek?

Tak, interfejs API tabel obsługuje zapytania OData i zapytanie LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Czy można połączyć się z usługą Azure Table Storage i Azure Cosmos DB interfejs API tabel obok siebie w tej samej aplikacji?

Tak, możesz nawiązać połączenie, tworząc dwa oddzielne wystąpienia CloudTableClient, z których każdy wskazuje własny identyfikator URI za pośrednictwem parametrów połączenia.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak mogę przeprowadzić migrację istniejącej aplikacji usługi Azure Table Storage do tej oferty?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) i [Narzędzie do migracji danych Azure Cosmos DB](import-data.md) są obsługiwane.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak można zwiększyć rozmiar magazynu dla tej usługi, jeśli na przykład zaczynam się od *n* GB danych, a moje dane zostaną rozbudowane na 1 TB w czasie?

Azure Cosmos DB zaprojektowano tak, aby zapewnić nieograniczony magazyn przy użyciu skalowania w poziomie. Usługa umożliwia monitorowanie i efektywne zwiększenie ilości miejsca do magazynowania.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak mogę monitorować interfejs API tabel oferty?

Za pomocą okienka **metryk** interfejs API tabel można monitorować żądania i użycie magazynu.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Jak mogę obliczyć wymaganą przepływność?

Możesz użyć szacowania pojemności, aby obliczyć TableThroughput, który jest wymagany dla operacji. Aby uzyskać więcej informacji, zobacz [szacunkowo jednostki żądań i magazyn danych](https://www.documentdb.com/capacityplanner). Ogólnie rzecz biorąc, można wyświetlić jednostkę jako kod JSON i wprowadzić numery dla operacji.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Czy można używać zestawu SDK interfejs API tabel lokalnie z emulatorem?

Obecnie nie.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Czy moja istniejąca aplikacja może współdziałać z interfejs API tabel?

Tak, obsługiwany jest ten sam interfejs API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Czy muszę migrować istniejące aplikacje usługi Azure Table Storage do zestawu SDK, jeśli nie chcę używać funkcji interfejs API tabel?

Nie. możesz tworzyć i korzystać z istniejących zasobów usługi Azure Table Storage bez zakłócania żadnego rodzaju. Niemniej jednak, jeśli nie używasz interfejs API tabel, nie możesz korzystać z indeksu automatycznego, opcji dodatkowej spójności ani dystrybucji globalnej.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Jak mogę dodać replikację danych w interfejs API tabel w więcej niż jednym regionie platformy Azure?

Aby dodać regiony odpowiednie dla aplikacji, można użyć [ustawień replikacji globalnej](tutorial-global-distribution-sql-api.md#portal) portalu Azure Cosmos DB. Aby opracować globalnie rozproszoną aplikację, należy również dodać aplikację z zestawem informacji PreferredLocation do lokalnego regionu w celu zapewnienia niskiego opóźnienia odczytu.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Jak mogę zmienić podstawowego regionu zapisu dla konta w interfejs API tabel?

Aby dodać region, a następnie przełączyć się do trybu failover w wymaganym regionie, można użyć okienka globalnego Azure Cosmos DB Portal replikacji. Aby uzyskać instrukcje, zobacz [programowanie z wieloregionowymi kontami Azure Cosmos DB](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak mogę skonfigurować moje preferowane regiony odczytu na potrzeby małych opóźnień podczas dystrybucji danych?

Aby ułatwić odczytywanie z lokalizacji lokalnej, użyj klucza PreferredLocation w pliku App. config. W przypadku istniejących aplikacji interfejs API tabel zgłasza błąd, jeśli ustawiono element Locationmode. Usuń ten kod, ponieważ interfejs API tabel pobiera te informacje z pliku App. config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak należy myśleć o poziomach spójności w interfejs API tabel?

Azure Cosmos DB zapewnia dobre kompromisy między spójnością, dostępnością i opóźnieniem. Azure Cosmos DB oferuje pięć poziomów spójności dla interfejs API tabel deweloperów, dzięki czemu można wybrać odpowiedni model spójności na poziomie tabeli i wprowadzić indywidualne żądania podczas wykonywania zapytania dotyczącego danych. Gdy klient łączy się z programem, może określić poziom spójności. Możesz zmienić poziom za pośrednictwem argumentu consistencyLevel CreateCloudTableClient.

Interfejs API tabel zapewnia odczyty o małym opóźnieniu przy użyciu "odczytywanie własnych zapisów", ze spójnością nieodświeżoną jako domyślną. Aby uzyskać więcej informacji, zobacz [poziomy spójności](consistency-levels.md).

Domyślnie usługa Azure Table Storage zapewnia silną spójność w obrębie regionu i ostatecznej spójności w lokalizacjach pomocniczych.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Czy Azure Cosmos DB interfejs API tabel oferować więcej poziomów spójności niż usługa Azure Table Storage?

Tak, aby uzyskać informacje o sposobach korzystania z rozproszonego charakteru Azure Cosmos DB, zobacz [poziomy spójności](consistency-levels.md). Ponieważ gwarancje są udostępniane dla poziomów spójności, można z nich korzystać z pewnością.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Kiedy jest włączona dystrybucja globalna, jak długo trwa replikowanie danych?

Azure Cosmos DB zatwierdza trwale danych w regionie lokalnym i wypychanie danych do innych regionów bezpośrednio w zakresie milisekund. Ta replikacja jest zależna tylko od czasu błądzenia centrum danych. Aby dowiedzieć się więcej o możliwościach dystrybucji globalnej Azure Cosmos DB, zobacz [Azure Cosmos DB: globalnie dystrybuowana usługa bazy danych na platformie Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Czy można zmienić poziom spójności żądania odczytu?

Za pomocą Azure Cosmos DB można ustawić poziom spójności na poziomie kontenera (w tabeli). Przy użyciu zestawu SDK platformy .NET można zmienić poziom, podając wartość klucza TableConsistencyLevel w pliku App. config. Możliwe wartości to: silna, powiązana nieaktualność, sesja, spójny prefiks i ostateczna. Aby uzyskać więcej informacji, zobacz [możliwość dostosowania poziomów spójności danych w Azure Cosmos DB](consistency-levels.md). Najważniejszym pomysłem jest to, że nie można ustawić poziomu spójności żądania na więcej niż ustawienie tabeli. Na przykład nie można ustawić poziomu spójności dla tabeli na początku i na poziomie spójności żądania.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak interfejs API tabel obsługiwać tryb failover, jeśli region ulegnie awarii?

Interfejs API tabel wykorzystuje globalnie dystrybuowaną platformę Azure Cosmos DB. Aby mieć pewność, że aplikacja może tolerować przestoje centrum danych, należy włączyć co najmniej jeden region dla konta w portalu Azure Cosmos DB [przy tworzeniu kont Azure Cosmos DB z wieloma regionami](high-availability.md). Priorytet regionu można ustawić przy użyciu portalu [opracowywania z wieloregionowymi kontami Azure Cosmos DB](high-availability.md).

Możesz dodać dowolną liczbę regionów dla konta i kontrolować miejsce, w którym może być przełączane do trybu failover, podając priorytet pracy awaryjnej. Aby można było korzystać z bazy danych, należy podać również aplikację. W takim przypadku klienci nie będą napotykać przestojów. [Najnowsza wersja zestawu SDK klienta platformy .NET](table-sdk-dotnet.md) to multihostingu, ale inne zestawy SDK nie są. Oznacza to, że może wykryć region, który jest wyłączony i automatycznie przechodzić do trybu failover w nowym regionie.

### <a name="is-the-table-api-enabled-for-backups"></a>Czy interfejs API tabel jest włączona dla kopii zapasowych?

Tak, interfejs API tabel wykorzystuje platformę Azure Cosmos DB do tworzenia kopii zapasowych. Kopie zapasowe są wykonywane automatycznie. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej online i przywracanie za pomocą Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Czy interfejs API tabel indeksować wszystkie atrybuty jednostki domyślnie?

Tak, wszystkie atrybuty jednostki są indeksowane domyślnie. Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB: zasady indeksowania](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Czy oznacza to, że nie trzeba tworzyć więcej niż jednego indeksu w celu spełnienia zapytań?

Tak, Azure Cosmos DB interfejs API tabel zapewnia automatyczne indeksowanie wszystkich atrybutów bez żadnej definicji schematu. Ta Automatyzacja pozwala deweloperom skupić się na aplikacji, a nie na tworzeniu indeksu i zarządzaniu nimi. Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB: zasady indeksowania](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Czy mogę zmienić zasady indeksowania?

Tak, zasady indeksowania można zmienić, podając definicję indeksu. Należy prawidłowo zakodować i wyjść z ustawień.

W przypadku zestawów SDK non-.NET zasady indeksowania można ustawić tylko w portalu w **Eksplorator danych**, przejdź do konkretnej tabeli, którą chcesz zmienić, a następnie przejdź do pozycji **skalowanie & Ustawienia**-> zasad indeksowania, wprowadź żądaną zmianę, a następnie **Zapisz** .

Z zestawu SDK platformy .NET można przesłać plik App. config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB jako platforma prawdopodobnie ma wiele możliwości, takich jak sortowanie, agregacje, hierarchia i inne funkcje. Czy te funkcje zostaną dodane do interfejs API tabel?

Interfejs API tabel zapewnia te same funkcje zapytań co usługa Azure Table Storage. Usługa Azure Cosmos DB obsługuje także sortowanie, agregaty, zapytania geoprzestrzenne, hierarchię i szeroki zakres funkcji wbudowanych. Aby uzyskać więcej informacji, zobacz [zapytania SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Kiedy należy zmienić TableThroughput dla interfejs API tabel?

Należy zmienić TableThroughput w przypadku zastosowania jednego z następujących warunków:

* Wykonujesz wyodrębnianie, przekształcanie i ładowanie danych (ETL) lub chcesz przekazać znaczną ilość danych w krótkim czasie.
* Potrzebna jest większa przepływność z kontenera lub z zestawu kontenerów na zapleczu. Na przykład widzisz, że używana przepływność jest większa niż przepustowość i jest ograniczona. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności dla kontenerów usługi Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Czy można skalować w górę i w dół przepływność tabeli interfejs API tabel?

Tak, możesz użyć okienka skalowanie w portalu Azure Cosmos DB, aby skalować przepływność. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Czy jest to domyślny zestaw TableThroughput dla nowo zainicjowanych tabel?

Tak, jeśli nie zastąpisz TableThroughput za pomocą pliku App. config i nie użyjesz wstępnie utworzonego kontenera w Azure Cosmos DB, usługa utworzy tabelę o przepływności wynoszącej 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Czy istnieje jakakolwiek zmiana cen dla istniejących klientów usługi Azure Table Storage?

Brak. Nie wprowadzono zmian w cenie dla istniejących klientów usługi Azure Table Storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak obliczana jest cena dla interfejs API tabel?

Cena zależy od przydzieloną TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Jak mogę obsługiwać dowolnych limitów szybkości dla tabel w ofercie interfejs API tabel?

Jeśli częstotliwość żądań jest większa niż pojemność zainicjowanej przepływności dla kontenera bazowego lub zestawu kontenerów, występuje błąd, a zestaw SDK ponawia próbę wywołania, stosując zasady ponawiania.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Dlaczego należy wybrać przepływność z PartitionKey i RowKey, aby korzystać z oferty interfejs API tabel Azure Cosmos DB?

Azure Cosmos DB ustawia domyślną przepływność dla kontenera, jeśli nie podano go w pliku App. config lub za pośrednictwem portalu.

Azure Cosmos DB zapewnia gwarancje wydajności i opóźnień z górnymi granicami operacji. Ta gwarancja jest możliwa, gdy silnik może wymusić Zarządzanie operacjami dzierżawy. Ustawienie TableThroughput zapewnia gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tę pojemność i gwarantuje pomyślne sukcesy działania.

Korzystając ze specyfikacji przepływności, można elastycznie zmienić ją na korzyść z sezonowości aplikacji, spełnić wymagania dotyczące przepływności i zaoszczędzić koszty.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Usługa Azure Table Storage została niedrogie dla mnie, ponieważ płacisz tylko za przechowywanie danych i rzadko wykonuję zapytania. Oferta interfejs API tabel Azure Cosmos DB wydaje się otrzymywać, mimo że nie wykonano żadnej pojedynczej transakcji ani nie zostały one zapisane. Czy można wyjaśnić?

Azure Cosmos DB jest zaprojektowana jako globalnie dystrybuowany system oparty na umowie SLA z gwarancjami dostępności, opóźnień i przepływności. Zarezerwowanie przepływności w Azure Cosmos DB jest gwarantowane, w przeciwieństwie do przepływności innych systemów. Azure Cosmos DB zapewnia dodatkowe możliwości, które klienci zażądały, takie jak indeksy pomocnicze i globalna dystrybucja.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nigdy nie otrzymuję pełnego limitu przydziału (oznacza to, że partycja jest pełna) podczas pozyskiwania danych do usługi Azure Table Storage. Korzystając z interfejs API tabel, uzyskuję tę wiadomość. Czy ta oferta ogranicza mnie i wymusza zmianę istniejącej aplikacji?

Azure Cosmos DB to system oparty na umowie SLA, który zapewnia nieograniczoną skalę, z gwarancją opóźnienia, przepływności, dostępności i spójności. Aby zapewnić gwarantowaną wydajność warstwy Premium, upewnij się, że rozmiar i indeks danych są zarządzane i skalowalne. Limit 10 GB dla liczby jednostek lub elementów na klucz partycji polega na tym, że zapewniamy doskonałe wyszukiwanie i wydajność zapytań. Aby zapewnić, że aplikacja będzie skalowana prawidłowo, nawet w przypadku usługi Azure Storage, zalecamy *utworzenie partycji* gorącej przez zapisanie wszystkich informacji w jednej partycji i wykonanie zapytania.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>PartitionKey i RowKey nadal są wymagane w przypadku interfejs API tabel?

Tak. Ponieważ obszar powierzchniowy interfejs API tabel jest podobny do tego w zestawie SDK usługi Azure Table Storage, klucz partycji stanowi wydajny sposób dystrybucji danych. Klucz wiersza jest unikatowy w obrębie tej partycji. Klucz wiersza musi być obecny i nie może mieć wartości null, podobnie jak w przypadku standardowego zestawu SDK. Długość RowKey to 255 bajtów, a długość PartitionKey wynosi 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jakie są komunikaty o błędach dla interfejs API tabel?

Usługa Azure Table Storage i Azure Cosmos DB interfejs API tabel używają tych samych zestawów SDK, dzięki czemu większość błędów będzie taka sama.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Dlaczego otrzymuję ograniczenie przy próbie utworzenia dużej ilości tabel po drugiej stronie w interfejs API tabel?

Azure Cosmos DB to system oparty na umowie SLA, który zapewnia opóźnienia, przepływność, dostępność i gwarancje spójności. Ponieważ jest to system aprowizacji, rezerwuje zasoby w celu zagwarantowania tych wymagań. Zostanie wykryta i ograniczona szybka stawka tworzenia tabel. Zalecamy przyjrzeć się szybkości tworzenia tabel i obniżyć ją do mniej niż 5 na minutę. Należy pamiętać, że interfejs API tabel jest systemem aprowizacji. Chwilę ją zainicjujesz.

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>W C#przypadku tworzenia/.NET należy używać pakietu Microsoft. Azure. graphs lub Gremlin.NET?

Azure Cosmos DB interfejs API Gremlin korzysta ze sterowników typu "open source" jako głównych łączników dla usługi. Zalecanym rozwiązaniem jest użycie [sterowników, które są obsługiwane przez Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak są naliczone opłaty za RU/s podczas wykonywania zapytań w bazie danych grafu?

Wszystkie obiekty wykresu, wierzchołki i krawędzie są wyświetlane jako dokumenty JSON w zapleczu. Ponieważ jedno zapytanie Gremlin może modyfikować jeden lub wiele obiektów grafu jednocześnie, koszt skojarzony z nim jest bezpośrednio związany z obiektami, krawędziami przetworzonymi przez zapytanie. Jest to ten sam proces, którego Azure Cosmos DB używa dla wszystkich innych interfejsów API. Aby uzyskać więcej informacji, zobacz [jednostki żądań w Azure Cosmos DB](request-units.md).

Opłata za RU zależy od zestawu danych roboczych przechodzenia, a nie zestawu wyników. Na przykład, jeśli zapytanie ma na celu uzyskanie pojedynczego wierzchołka w wyniku, ale wymaga przechodzenia więcej niż jednego innego obiektu w sposób, koszt będzie oparty na wszystkich obiektach grafu, które pozostaną w celu obliczenia jednego wierzchołka wynik.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaka jest maksymalna skala, która może znajdować się w bazie danych programu Graph Azure Cosmos DB Gremlin API?

Azure Cosmos DB korzysta z [partycjonowania poziomego](partition-data.md) , aby automatycznie zwiększać wymagania dotyczące magazynu i przepływności. Maksymalna przepływność i pojemność magazynu obciążenia są określane przez liczbę partycji skojarzonych z danym kontenerem. Jednak kontener interfejsu API Gremlin ma określony zestaw wytycznych, aby zapewnić prawidłowe działanie wydajności na dużą skalę. Aby uzyskać więcej informacji o partycjonowaniu i najlepszych rozwiązaniach, zobacz [partycjonowanie w Azure Cosmos DB](partition-data.md) artykule.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak chronić przed atakami polegającymi na iniekcji przy użyciu sterowników Gremlin?

Większość natywnych sterowników Gremlin Apache Tinkerpop umożliwia określenie słownika parametrów do wykonania zapytania. Jest to przykład, jak to zrobić w [Gremlin.NET](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) i w [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Dlaczego otrzymuję komunikat o błędzie "Gremlin kompilacja zapytania: nie można znaleźć żadnej metody"?

Azure Cosmos DB API Gremlin implementuje podzestaw funkcji zdefiniowanych w obszarze powierzchni Gremlin. Aby zapoznać się z obsługiwanymi krokami i więcej informacji, zobacz artykuł dotyczący [pomocy technicznej Gremlin](gremlin-support.md) .

Najlepszym obejściem jest ponowne napisanie wymaganych kroków Gremlin z obsługiwanymi funkcjami, ponieważ wszystkie podstawowe kroki Gremlin są obsługiwane przez Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Dlaczego otrzymuję komunikat "WebSocketException: serwer zwrócił kod stanu" 200 ", gdy oczekiwano kodu stanu" 101 "?

Ten błąd jest prawdopodobnie zgłaszany, gdy jest używany niewłaściwy punkt końcowy. Punkt końcowy, który generuje ten błąd, ma następujący wzorzec:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

To jest punkt końcowy dokumentów dla bazy danych grafu.  Prawidłowym punktem końcowym, który ma być używany, jest punkt końcowy Gremlin o następującym formacie:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Dlaczego otrzymuję komunikat o błędzie "RequestRateIsTooLarge"?

Ten błąd oznacza, że przydzieloną liczbę jednostek żądań na sekundę nie są wystarczające do obsłużynia zapytania. Ten błąd jest zwykle widoczny podczas uruchamiania zapytania, które uzyskuje wszystkie wierzchołki:

```
// Query example:
g.V()
```

To zapytanie spróbuje pobrać wszystkie wierzchołki z grafu. W związku z tym koszt tego zapytania będzie równy co najmniej liczbie wierzchołków w odniesieniu do jednostek ru. Ustawienie RU/s powinno być dostosowane do tego zapytania.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Dlaczego moje połączenia sterowników Gremlin są ostatecznie porzucane?

Nawiązywane jest połączenie Gremlin za pomocą połączenia WebSocket. Chociaż połączenia protokołu WebSocket nie mają określonego czasu na żywo, Azure Cosmos DB interfejs API Gremlin zakończy połączenia bezczynne po 30 minutach braku aktywności.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Dlaczego nie mogę używać wywołań interfejsu API Fluent w natywnych sterownikach Gremlin?

Wywołania interfejsu API Fluent nie są jeszcze obsługiwane przez interfejs API usługi Azure Cosmos DB Gremlin. Wywołania interfejsu API Fluent wymagają funkcji formatowania wewnętrznego znanej jako obsługa kodu bajtowego, która obecnie nie jest obsługiwana przez Azure Cosmos DB interfejs API Gremlin. Z tego samego powodu Najnowsza wersja sterownika Gremlin-JavaScript również nie jest obecnie obsługiwana.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Jak można oszacować wydajność zapytań Gremlin?

Krok zapoznawczy **executionProfile ()** może służyć do przeprowadzenia analizy planu wykonywania zapytania. Ten krok należy dodać do końca dowolnego zapytania Gremlin, jak pokazano na poniższym przykładzie:

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

Dane wyjściowe powyższego profilu pokazują, ile czasu poświęcają na uzyskanie obiektów wierzchołków, obiektów brzegowych i rozmiaru zestawu danych roboczych. Jest to związane ze standardowymi pomiarami kosztów dla Azure Cosmos DB zapytań.

## <a id="cassandra"></a>interfejs API Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Jaka jest wersja protokołu obsługiwana przez usługę Azure Cosmso DB interfejs API Cassandra? Czy istnieje plan obsługi innych protokołów?

Interfejs API Apache Cassandra dla Azure Cosmos DB obsługuje dzisiaj CQL w wersji 4. Jeśli masz opinię na temat obsługi innych protokołów, poinformuj nas o tym za pośrednictwem [opinii o głosowaniu użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db) lub Wyślij wiadomość e-mail do [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Dlaczego należy wybrać przepływność dla tabeli a wymagania?

Azure Cosmos DB ustawia domyślną przepływność dla kontenera na podstawie tego, gdzie tworzysz tabelę z poziomu portalu lub CQL.
Azure Cosmos DB zapewnia gwarancje wydajności i opóźnień z górnymi granicami operacji. Ta gwarancja jest możliwa, gdy silnik może wymusić Zarządzanie operacjami dzierżawy. Ustawienie przepływności zapewnia gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tę pojemność i gwarantuje pomyślne wykonanie operacji.
Możesz elastycznie zmieniać przepływność, aby korzystać z sezonowości aplikacji i oszczędzać koszty.

Koncepcji przepływności wyjaśniono w [jednostkach żądań w Azure Cosmos DB](request-units.md) artykule. Przepływność tabeli jest równomiernie dystrybuowana między podstawowymi partycjami fizycznymi.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Co to jest domyślny RU/s tabeli po utworzeniu za pomocą CQL? What If muszę zmienić?

Azure Cosmos DB używa jednostek żądań na sekundę (RU/s) jako waluty do zapewnienia przepływności. Tabele utworzone za poorednictwem CQL mają 400 RU. Można zmienić RU z poziomu portalu.

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

### <a name="what-happens-when-throughput-is-used-up"></a>Co się stanie, gdy przepływność jest używana?

Azure Cosmos DB zapewnia gwarancje wydajności i opóźnień z górnymi granicami operacji. Ta gwarancja jest możliwa, gdy silnik może wymusić Zarządzanie operacjami dzierżawy. Jest to możliwe w zależności od ustawienia przepływności, co zapewnia gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tę pojemność i gwarantuje pomyślne wykonanie operacji.
Gdy przejdziesz do tej pojemności, otrzymujesz przeciążony komunikat o błędzie wskazujący, że pojemność została użyta.
0x1001 przeciążone: nie można przetworzyć żądania, ponieważ "liczba żądań jest duża". W tym miejscu niezbędne jest sprawdzenie, jakie operacje i ich woluminy powodują ten problem. Możesz uzyskać pomysł dotyczący zużytej pojemności przechodzącej przez zainicjowaną pojemność za pomocą metryk w portalu. Należy upewnić się, że pojemność jest używana niemal równomiernie we wszystkich partycji podstawowych. W przypadku, gdy większość przepływności jest używana przez jedną partycję, nastąpiło pochylenie obciążenia.

Dostępne są metryki pokazujące, jak przepływność jest używana w godzinach, dniach i na siedem dni, między partycjami lub agregacją. Aby uzyskać więcej informacji, zobacz [monitorowanie i debugowanie za pomocą metryk w Azure Cosmos DB](use-metrics.md).

Dzienniki diagnostyczne zostały omówione w artykule [rejestrowanie diagnostyczne Azure Cosmos DB](logging.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Czy klucz podstawowy jest mapowany na klucz partycji koncepcji Azure Cosmos DB?

Tak, klucz partycji jest używany do umieszczania jednostki w prawej lokalizacji. W Azure Cosmos DB służy do znajdowania odpowiednich partycji logicznych, które są przechowywane na partycji fizycznej. Koncepcja partycjonowania jest dobrze omówiona w [partycji i skalowaniu w Azure Cosmos DB](partition-data.md) artykule. Niezwykle ważne jest, że partycja logiczna nie powinna już dzisiaj korzystać z limitu 10 GB.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co się stanie, gdy uzyskam pełny limit przydziału "powiadomienie wskazujące, że partycja jest zapełniona?

Azure Cosmos DB to system oparty na umowie SLA, który zapewnia nieograniczoną skalę, z gwarancją opóźnienia, przepływności, dostępności i spójności. Ten nieograniczony magazyn jest oparty na skalowaniu w poziomie danych przy użyciu partycjonowania w postaci kluczowych koncepcji. Koncepcja partycjonowania jest dobrze omówiona w [partycji i skalowaniu w Azure Cosmos DB](partition-data.md) artykule.

Limit 10 GB dla liczby jednostek lub elementów na partycję logiczną, która powinna być zgodna. Aby upewnić się, że aplikacja jest odpowiednio skalowana, zalecamy, aby *nie* tworzyć partycji gorąca, przechowując wszystkie informacje w jednej partycji i wykonując zapytania. Ten błąd może występować tylko wtedy, gdy dane są skośne: oznacza to, że masz dużo danych dla jednego klucza partycji (więcej niż 10 no__t-0GB). Dystrybucję danych można znaleźć za pomocą portalu magazynu. Aby naprawić ten błąd, należy ponownie utworzyć tabelę i wybrać szczegółowy podstawowy (klucz partycji), co umożliwia lepszą dystrybucję danych.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Czy jest możliwe używanie interfejs API Cassandra jako magazynu wartości klucza z milionami lub miliardami poszczególnych kluczy partycji?

Azure Cosmos DB mogą przechowywać nieograniczone dane przez skalowanie magazynu. Jest to niezależne od przepływności. Tak, możesz zawsze używać interfejs API Cassandra do przechowywania i pobierania kluczy/wartości, określając prawidłowy klucz podstawowy/partycja. Te pojedyncze klucze uzyskują własną partycję logiczną i korzystającegoą partycję fizyczną bez problemów.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Czy można utworzyć więcej niż jedną tabelę z interfejs API Apache Cassandra Azure Cosmos DB?

Tak, można utworzyć więcej niż jedną tabelę z interfejs API Apache Cassandra. Każda z tych tabel jest traktowana jako jednostka do przepływności i magazynu.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Czy można utworzyć więcej niż jedną tabelę w pomyślnym zakończeniu?

Azure Cosmos DB jest systemem zarządzanym przez zasoby dla działania płaszczyzny danych i kontroli. Kontenery, takie jak kolekcje, są obiektami środowiska uruchomieniowego, które są obsługiwane przez daną przepustowość. Tworzenie tych kontenerów w szybkim pomyślnym czasie nie jest oczekiwane i ograniczane. Jeśli masz testy, które natychmiast porzucają tabele, spróbuj je przetworzyć.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Jaka jest maksymalna liczba tabel, które można utworzyć?

Nie ma fizycznego limitu liczby tabel, Wyślij wiadomość e-mail o [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) , jeśli masz dużą liczbę tabel (w przypadku których łączny rozmiar przekracza 10 TB danych), które muszą zostać utworzone na podstawie zwykłych dziesiątkach lub setek.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Jaka jest maksymalna liczba miejsc, które możemy utworzyć?

Nie ma fizycznego limitu liczby miejsc, które są kontenerami metadanych, Wyślij wiadomość e-mail na adres [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) , jeśli z jakiegoś powodu masz dużą liczbę miejsc.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Czy istnieje możliwość wprowadzenia dużej ilości danych po uruchomieniu z normalnej tabeli?

Pojemność magazynu jest automatycznie zarządzana i zwiększa się w miarę wypychania większej ilości danych. Pozwala to na zaimportowanie tyle danych, ile potrzebujesz, bez konieczności zarządzania węzłami i ich aprowizacji.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Czy jest możliwe dostarczenie ustawień pliku YAML, aby skonfigurować interfejs API Apache Casssandra dla zachowania Azure Cosmos DB?

Interfejs API Apache Cassandra Azure Cosmos DB to usługa platformy. Zapewnia zgodność na poziomie protokołu na potrzeby wykonywania operacji. Upraszcza to zarządzanie, monitorowanie i konfigurację. Jako deweloper/użytkownik nie musisz martwić się o dostępność, relikty, pamięć podręczną kluczy, pamięć podręczną wierszy, filtr z zalotem i wiele innych ustawień. Interfejs API Apache Cassandra Azure Cosmos DB koncentruje się na zapewnianiu wymaganej wydajności odczytu i zapisu bez konieczności konfigurowania i zarządzania.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Interfejs API Apache Cassandra dla Azure Cosmos DB obsługi dodawania węzłów/stanu klastra/stanu węzła?

Interfejs API Apache Cassandra to usługa platformy, która umożliwia planowanie pojemności, reagowanie na wymagania elastyczności & magazynu Breeze. Dzięki Azure Cosmos DB jest wymagana przepustowość. Następnie można skalować w górę i w dół dowolną liczbę razy dziennie bez obaw o Dodawanie/usuwanie węzłów lub zarządzanie nimi. Oznacza to, że nie trzeba używać węzła, narzędzia do zarządzania klastrem.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co się dzieje w odniesieniu do różnych ustawień konfiguracji dla tworzenia miejsc kluczy, takich jak Simple/Network?

Azure Cosmos DB zapewnia globalną dystrybucję z pola do dostępności i małych opóźnień. Nie ma potrzeby konfigurowania replik ani innych elementów. Wszystkie zapisy są zawsze trwale kworum zatwierdzone w dowolnym regionie, w którym piszesz, zapewniając gwarancje wydajności.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Co się dzieje w odniesieniu do różnych ustawień metadanych tabeli, takich jak filtr rozwinięty, buforowanie, zmiana naprawy, gc_grace, kompresja memtable_flush_period i inne?

Azure Cosmos DB zapewnia wydajność operacji odczytu/zapisu i przepływności bez konieczności dotykania jakichkolwiek ustawień konfiguracji i przypadkowego manipulowania nimi.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Czy wartość czasu wygaśnięcia (TTL) jest obsługiwana dla tabel Cassandra?

Tak, wartość czasu wygaśnięcia jest obsługiwana.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Czy możliwe jest monitorowanie stanu węzła, stanu repliki, GC i parametrów systemu operacyjnego wcześniej przy użyciu różnych narzędzi? Co należy monitorować teraz?

Azure Cosmos DB to usługa platformy, która pozwala zwiększyć produktywność i nie martwić się o zarządzanie i monitorowanie infrastruktury. Wystarczy zadbać o przepływność, która jest dostępna w metrykach portalu, aby sprawdzić, czy są one ograniczane i zwiększają lub zmniejszają przepustowość.
Monitoruj [umowy SLA](monitor-accounts.md).
Użyj [metryk](use-metrics.md) przy użyciu [dzienników diagnostycznych](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Które zestawy SDK klienta mogą współpracowały z interfejs API Apache Cassandraem Azure Cosmos DB?

Na potrzeby programów klienckich użyto sterowników klienta zestawu Apache Cassandra SDK, które korzystają z CQLv3. Jeśli masz inne sterowniki, które są używane, lub jeśli występują problemy, Wyślij wiadomość e-mail do [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Czy jest obsługiwany klucz partycji złożonej?

Tak, możesz użyć zwykłej składni, aby utworzyć złożony klucz partycji.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Czy mogę używać sstableloader do ładowania danych?

Nie, sstableloader nie jest obsługiwany.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Czy lokalny Klaster Apache Cassandra jest sparowany z interfejs API Cassandraem Azure Cosmos DBu?

W obecnym Azure Cosmos DB ma zoptymalizowane środowisko dla środowiska chmury bez konieczności wykonywania dodatkowych czynności. Jeśli wymagasz parowania, Wyślij wiadomość e-mail do [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) z opisem scenariusza. Pracujemy nad ofertą, aby pomóc w sparowaniu lokalnego/różnego klastra Cassandra w chmurze z usługą Cosomos interfejs API Cassandra DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Czy interfejs API Cassandra zapewnić pełne kopie zapasowe?

Azure Cosmos DB oferuje dwa bezpłatne pełne kopie zapasowe wykonane w ciągu czterech godzin w całym interfejsie API. Dzięki temu nie trzeba konfigurować harmonogramu tworzenia kopii zapasowych i innych elementów.
Jeśli chcesz zmodyfikować przechowywanie i częstotliwość, Wyślij wiadomość e-mail do [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) lub zgłoś zgłoszenie do pomocy technicznej. Informacje o możliwościach tworzenia kopii zapasowych znajdują się w artykule [Automatyczna kopia zapasowa online i przywracanie z Azure Cosmos DB](online-backup-and-restore.md) .

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak konto interfejs API Cassandra obsługuje tryb failover, jeśli region ulegnie awarii?

Azure Cosmos DB interfejs API Cassandra pożyczek z platformy rozproszonej globalnie Azure Cosmos DB. Aby mieć pewność, że aplikacja może tolerować przestoje centrum danych, należy włączyć co najmniej jeden region dla konta w portalu Azure Cosmos DB [przy tworzeniu kont Azure Cosmos DB z wieloma regionami](high-availability.md). Priorytet regionu można ustawić przy użyciu portalu [opracowywania z wieloregionowymi kontami Azure Cosmos DB](high-availability.md).

Możesz dodać dowolną liczbę regionów dla konta i kontrolować miejsce, w którym może być przełączane do trybu failover, podając priorytet pracy awaryjnej. Aby można było korzystać z bazy danych, należy podać również aplikację. W takim przypadku klienci nie będą napotykać przestojów.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Czy interfejs API Apache Cassandra indeksować wszystkie atrybuty jednostki domyślnie?

Interfejs API Cassandra planowania obsługi indeksowania pomocniczego, aby ułatwić tworzenie selektywnego indeksu dla niektórych atrybutów. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Czy mogę użyć nowego zestawu interfejs API Cassandra SDK lokalnie z emulatorem?

Tak, jest to obsługiwane.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB jako platforma prawdopodobnie ma wiele możliwości, takich jak źródło zmian i inne funkcje. Czy te możliwości zostaną dodane do interfejs API Cassandra?

Interfejs API Apache Cassandra zapewnia taką samą funkcjonalność CQL, jak Apache Cassandra. Firma Microsoft planuje zapoznaj się z możliwością obsługi różnych funkcji w przyszłości.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funkcja x regularne interfejs API Cassandra nie działa dzisiaj, gdzie można podać opinię?

Prześlij opinię za pośrednictwem [opinii użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
