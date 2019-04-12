---
title: Programowanie lokalnie za pomocą emulatora usługi Azure Cosmos
description: Przy użyciu emulatora usługi Azure Cosmos, można tworzyć i testować aplikację lokalnie bezpłatnie, bez tworzenia subskrypcji platformy Azure.
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 04/20/2018
author: deborahc
ms.author: dech
ms.openlocfilehash: 3d535c71480693d0424c6697776a1ddbf37b47c5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493216"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Na użytek emulatora usługi Azure Cosmos lokalne programowanie i testowanie

Emulator usługi Azure Cosmos zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programistycznych. Przy użyciu emulatora usługi Azure Cosmos, możesz rozwijać i przetestować aplikację lokalnie, bez tworzenia subskrypcji platformy Azure ani generowania kosztów. Po zakończeniu pracy aplikacji w emulatorze Cosmos Azure, możesz przełączyć się do korzystania z konta usługi Azure Cosmos w chmurze.

Można tworzyć przy użyciu emulatora usługi Azure Cosmos z [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [bazy danych MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api), i [tabeli](local-emulator.md#table-api) Konta interfejsów API. Jednak w tej chwili widoku Eksploratora danych w emulatorze w pełni obsługuje klientów dla interfejsu API SQL tylko. 

## <a name="how-the-emulator-works"></a>Jak działa emulator

Emulator usługi Azure Cosmos zapewnia emulacji o wysokiej wierności, usługi Azure Cosmos DB. Obsługuje ona identyczną funkcjonalność jako usługi Azure Cosmos DB, w tym obsługę tworzenia i wykonywanie zapytań o dane, aprowizacja i skalowanie kontenerów i wykonywania procedur składowanych i wyzwalaczy. Można tworzyć i testować aplikacje przy użyciu emulatora usługi Azure Cosmos i wdrażać je na platformie Azure na skalę globalną, wprowadzając tylko jedną konfigurację Zmień na punkt końcowy połączenia usługi Azure Cosmos DB.

Chociaż emulator dokładnie odzwierciedla działanie usługi Azure Cosmos DB, różni się od niej pod względem wdrożenia. Na przykład emulator używa standardowych składników systemu operacyjnego — między innymi lokalnego systemu plików jako magazynu trwałego i stosu protokołu HTTPS na potrzeby łączności. Funkcjonalność, która zależy od infrastruktury platformy Azure, takich jak replikacji globalnej, milisekundowe opóźnienie dla operacji odczytu/zapisu i dostosowywalnych poziomów spójności nie mają zastosowania.

Przeprowadzić migrację danych między emulatora usługi Cosmos Azure i usługi Azure Cosmos DB przy użyciu [narzędzie migracji usługi Azure Cosmos DB dane](https://github.com/azure/azure-documentdb-datamigrationtool).

Można uruchomić emulatora usługi Azure Cosmos w kontenerze Windows Docker, zobacz [usługi Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) dla polecenia ściągania platformy docker i [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) emulatora kodu źródłowego.

## <a name="differences-between-the-emulator-and-the-service"></a>Różnice między emulatorem a usługą

Ponieważ Emulator usługi Azure Cosmos zapewnia środowisko emulowanej uruchomione na stacji roboczej dewelopera lokalnego, istnieją pewne różnice w funkcjonalności między emulator, a konto usługi Azure Cosmos w chmurze:

* Aktualnie Eksplorator danych w emulatorze obsługuje klientów dla interfejsu API SQL. Widok Eksploratora danych i operacje dotyczące usługi Azure Cosmos DB API takich jak bazy danych MongoDB, Table, wykresów i interfejsów API rozwiązania Cassandra nie są w pełni obsługiwane.
* Emulator usługi Azure Cosmos obsługuje tylko jedno konto stałych i znanych klucz główny. Ponowne generowanie klucza nie jest możliwe w emulatorze Cosmos Azure, jednak można zmienić domyślnego klucza, korzystając z opcji wiersza polecenia.
* Emulator usługi Azure Cosmos nie jest skalowalna usługa i nie będzie obsługiwać dużą liczbę kontenerów.
* Emulator usługi Azure Cosmos nie oferuje różne [poziomów spójności w usłudze Azure Cosmos DB](consistency-levels.md).
* Emulator usługi Azure Cosmos nie oferuje [replikacja w wielu regionach](distribute-data-globally.md).
* Jak Twoja kopia emulatora usługi Azure Cosmos nie zawsze jest aktualne przy użyciu najnowszych zmian w usłudze Azure Cosmos DB, należy zapoznać się [planisty wydajności usługi Azure Cosmos DB](https://www.documentdb.com/capacityplanner) Aby dokładnie oszacować produkcji wymagania aplikacji dotyczące przepływność (ru).
* Korzystając z emulatora usługi Azure Cosmos, domyślnie, można utworzyć maksymalnie 25 kontenery o stałym rozmiarze (obsługiwana tylko przy użyciu zestawów SDK usługi Azure Cosmos DB) lub 5 nieograniczone kontenery przy użyciu emulatora usługi Azure Cosmos. Aby uzyskać więcej informacji na temat zmiany tej wartości, zobacz [Ustawianie wartości PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Wymagania systemowe

Emulator usługi Azure Cosmos ma następujące wymagania dotyczące sprzętu i oprogramowania:

* Wymagania dotyczące oprogramowania
  * Windows Server 2012 R2, Windows Server 2016 lub Windows 10
  * 64-bitowym systemie operacyjnym
* Minimalne wymagania sprzętowe
  * 2 GB pamięci RAM
  * 10 GB dostępnego miejsca na dysku twardym

## <a name="installation"></a>Instalacja

Można pobrać i zainstalować Emulator usługi Azure Cosmos z [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) lub emulator można uruchamiać na platformy Docker for Windows. Aby uzyskać instrukcje dotyczące korzystania z emulatora w aplikacji Docker for Windows, zobacz sekcję [Uruchamianie na platformie Docker](#running-on-docker).

> [!NOTE]
> Aby zainstalować, skonfigurować i uruchomić emulatora usługi Azure Cosmos, musi mieć uprawnienia administratora na komputerze. Emulator będzie tworzyć/Dodawanie certyfikatu i również ustawić reguły zapory, aby można było uruchomić jej usług; w związku z tym jest niezbędne do emulator, aby mieć możliwość wykonania takich operacji.

## <a name="running-on-windows"></a>Uruchamianie w systemie Windows

Można uruchomić emulatora usługi Azure Cosmos, wybierz przycisk Start, lub naciśnij klawisz Windows. Rozpocznij wpisywanie ciągu **emulatora usługi Azure Cosmos**, a następnie wybierz emulator z listy aplikacji.

![Wybierz przycisk Start i naciśnij klawisz Windows, rozpocznij wpisywanie ciągu ** Emulator usługi Cosmos Azure **, a następnie wybierz emulator z listy aplikacji](./media/local-emulator/database-local-emulator-start.png)

Po uruchomieniu emulatora pojawi się ikona w obszarze powiadomień paska zadań systemu Windows. ![Powiadomienia pasek zadania lokalnego emulatora usługi Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

Emulator usługi Azure Cosmos domyślnie działa na komputerze lokalnym ("localhost") nasłuchuje na porcie 8081.

Emulator usługi Azure Cosmos jest instalowana na `C:\Program Files\Azure Cosmos DB Emulator` domyślnie. Można również uruchomić i zatrzymać emulator z poziomu wiersza polecenia. Więcej informacji zawiera [dokumentacja narzędzia wiersza polecenia](#command-line).

## <a name="start-data-explorer"></a>Uruchamianie Eksploratora danych

Po uruchomieniu emulatora usługi Azure Cosmos następuje automatyczne otwarcie Eksploratora danych usługi Azure Cosmos w przeglądarce. Adres będzie widoczny jako `https://localhost:8081/_explorer/index.html`. Po zamknięciu programu explorer i chcesz go ponownie, możesz otworzyć adresu URL w przeglądarce lub uruchomienia z emulatora usługi Azure Cosmos w Windows ikona na pasku zadań, jak pokazano poniżej.

![Azure uruchamiania Eksploratora danych lokalnego emulatora usługi Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Sprawdzanie dostępności aktualizacji

Eksplorator danych informuje o możliwości pobrania nowych aktualizacji.

> [!NOTE]
> Dane utworzone w jednej wersji emulatora usługi Azure Cosmos (zobacz opcjonalne ustawienia ścieżki danych lub %LOCALAPPDATA%\CosmosDBEmulator) nie jest gwarantowana była dostępna w przypadku korzystania z innej wersji. Jeśli potrzebujesz w celu utrwalenia Twoich danych w perspektywie długoterminowej, zalecane jest przechowywanie danych na koncie usługi Azure Cosmos, a nie w emulatora usługi Azure Cosmos.

## <a name="authenticating-requests"></a>Uwierzytelnianie żądań

Jak usługa Azure Cosmos DB w chmurze, każde żądanie, wprowadzone dla emulatora usługi Azure Cosmos musi zostać uwierzytelnione. Emulator usługi Azure Cosmos obsługuje jednego stałego konta i klucz uwierzytelniania dobrze znanych na potrzeby uwierzytelniania klucza głównego. Tego konta i klucz są tylko poświadczenia dozwolone do użytku z emulatora usługi Azure Cosmos. Oto one:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Klucz główny, obsługiwane przez Emulator usługi Azure Cosmos jest przeznaczony do użytku tylko przy użyciu emulatora. Nie można użyć swojego konta usługi Azure Cosmos DB produkcji i klucz przy użyciu emulatora usługi Azure Cosmos.

> [!NOTE]
> Jeśli emulator zostało uruchomione z opcją następujący/key, Użyj klucza wygenerowanego, zamiast `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Aby uzyskać więcej informacji na temat opcji następujący/key, zobacz [odwołanie do narzędzia wiersza polecenia.](#command-line-syntax)

Za pomocą usługi Azure Cosmos DB Emulator usługi Azure Cosmos obsługuje tylko bezpiecznej komunikacji SSL.

## <a name="running-on-a-local-network"></a>Uruchamianie w sieci lokalnej

Emulator możesz uruchomić w sieci lokalnej. Aby włączyć dostęp do sieci, należy określić `/AllowNetworkAccess` opcji na [wiersza polecenia](#command-line-syntax), która wymaga również określenia `/Key=key_string` lub `/KeyFile=file_name`. Możesz użyć `/GenKeyFile=file_name` aby wygenerować plik z kluczem losowego ponoszonych z góry kosztów. Następnie można przekazać go do `/KeyFile=file_name` lub `/Key=contents_of_file`.

Umożliwia dostęp do sieci po raz pierwszy użytkownika należy zamknąć emulator, a następnie usunąć katalog danych emulatora usługi (% LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Programowanie za pomocą emulatora

### <a name="sql-api"></a>Interfejs API SQL

Po utworzeniu emulatora usługi Azure Cosmos uruchomiony na pulpicie, możesz użyć dowolnej obsługiwane [zestawu SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md) lub [API REST usługi Azure Cosmos DB](/rest/api/cosmos-db/) wchodzić w interakcje z emulatorem. Emulator usługi Azure Cosmos zawiera także wbudowaną Eksploratora danych, która umożliwia tworzenie kontenerów dla interfejsu API SQL i Cosmos DB dla interfejsu API usługi Mongo DB i wyświetlanie i edytowanie elementów bez pisania żadnego kodu.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Jeśli używasz [interfejsu API usługi Azure Cosmos DB, bazy danych mongodb](mongodb-introduction.md), użyj następujących parametrów połączenia:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Interfejs API tabel

Gdy masz uruchomiony na pulpicie z emulatora usługi Cosmos Azure, możesz użyć [zestawu SDK API tabeli usługi Azure Cosmos DB](table-storage-how-to-use-dotnet.md) wchodzić w interakcje z emulatorem. Uruchom emulator w wierszu polecenia jako administrator z "/ EnableTableEndpoint". Uruchom następujący kod, aby nawiązać połączenie z kontem interfejsu table API:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

Uruchom emulator w wierszu polecenia administratora z "/ EnableCassandraEndpoint". Alternatywnie można również ustawić zmienną środowiskową `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Zainstaluj język Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Instalowanie interfejsu wiersza polecenia/CQLSH bazy danych Cassandra](http://cassandra.apache.org/download/)

* Uruchom następujące polecenia w oknie wiersza polecenia regularne:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* W powłoce CQLSH uruchom następujące polecenia, aby nawiązać połączenie z punktem końcowym Cassandra:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Interfejs API języka Gremlin

Uruchom emulator w wierszu polecenia administratora z "/ EnableGremlinEndpoint". Alternatywnie można również ustawić zmienną środowiskową `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Zainstaluj apache-tinkerpop-gremlin konsoli-3.3.4](http://tinkerpop.apache.org/downloads.html)

* W Eksploratorze danych to emulator tworzenie bazy danych "db1" i "coll1"; kolekcji dla klucza partycji wybierz opcję "/ name"

* Uruchom następujące polecenia w oknie wiersza polecenia regularne:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* W powłoce Gremlin uruchom następujące polecenia, aby nawiązać połączenie z punktem końcowym Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Eksportowanie certyfikatu SSL

Do nawiązywania bezpiecznego połączenia z lokalnym emulatorem usługi Azure Cosmos DB języki i środowisko uruchomieniowe .NET używają magazynu certyfikatów systemu Windows. Inne języki korzystają z własnych metod używania certyfikatów i zarządzania nimi. Język Java używa własnego [magazynu certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), a język Python — [otok gniazd](https://docs.python.org/2/library/ssl.html).

Aby uzyskać certyfikat do użycia z językami i środowiskami uruchomieniowymi, które nie zapewniają integracji z magazynem certyfikatów systemu Windows, trzeba wyeksportować ten certyfikat przy użyciu menedżera certyfikatów systemu Windows. Można go uruchomić, uruchamiając certlm.msc lub postępuj zgodnie z instrukcjami krok po kroku [eksportowanie certyfikatów emulatora usługi Cosmos Azure](./local-emulator-export-ssl-certificates.md). Po uruchomieniu menedżera certyfikatów otwórz sekcję certyfikatów osobistych, jak pokazano poniżej, i wyeksportuj certyfikat o przyjaznej nazwie „DocumentDBEmulatorCertificate” jako plik Certyfikat X.509 szyfrowany algorytmem Base-64 (CER).

![Certyfikat SSL lokalnego emulatora usługi Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Aby zaimportować certyfikat X.509 do magazynu certyfikatów Java, postępuj zgodnie z instrukcjami zawartymi w artykule [Adding a Certificate to the Java CA Certificates Store (Dodawanie certyfikatu do magazynu certyfikatów Java CA)](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Po zaimportowaniu certyfikatu do magazynu certyfikatów klientów dla interfejsu API języków SQL i Azure Cosmos DB dla bazy danych MongoDB będzie można nawiązać połączenia z emulatora usługi Azure Cosmos.

W przypadku łączenia się z emulatorem za pomocą zestawów SDK środowisk Python i Node.js weryfikacja SSL jest wyłączona.

## <a id="command-line"></a>Dokumentacja narzędzia wiersza polecenia
Z lokalizacji instalacji służy wiersza polecenia do uruchamiania i zatrzymywania emulatora, skonfiguruj opcje i wykonywać inne operacje.

### <a name="command-line-syntax"></a>Składnia wiersza polecenia

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Aby wyświetlić listę opcji, wpisz ciąg `CosmosDB.Emulator.exe /?` w wierszu polecenia.

|**Opcja** | **Opis** | **Polecenie**| **Argumenty**|
|---|---|---|---|
|[Bez argumentów] | Uruchamiania emulatora usługi Azure Cosmos przy użyciu ustawień domyślnych. |CosmosDB.Emulator.exe| |
|[Help] |Wyświetla listę obsługiwanych argumentów wiersza polecenia.|CosmosDB.Emulator.exe /? | |
| GetStatus |Pobiera stan emulatora usługi Azure Cosmos. Stan jest wskazywany przez kod zakończenia: 1 = uruchamiany, 2 = uruchomiony, 3 = zatrzymany. Kod zakończenia o wartości ujemnej informuje o wystąpieniu błędu. Inne dane wyjściowe nie są generowane. | CosmosDB.Emulator.exe /GetStatus| |
| Shutdown| Zamyka emulatora usługi Azure Cosmos.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Określa ścieżkę przechowywania plików danych. Wartość domyślna to % LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<ścieżka_do_danych\> | \<ścieżka_do_danych\>: dostępna ścieżka |
|Port | Określa numer portu używanego przez emulatora. Wartość domyślna to 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<port\>: numer pojedynczego portu |
| MongoPort | Określa numer portu używanego w celu zapewnienia zgodności z interfejsem API usługi MongoDB. Wartość domyślna to 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<port_mongo\>: numer pojedynczego portu|
| CassandraPort | Określa numer portu do użycia dla punktu końcowego bazy danych Cassandra. Wartość domyślna to 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: numer pojedynczego portu |
| ComputePort | Określony numer portu do użycia dla usługa Compute bramy międzyoperacyjności. Port sondy punktu końcowego HTTP bramy jest obliczana jako ComputePort + 79. Dzięki temu ComputePort i ComputePort + 79 musi być otwarty i dostępny. Wartości domyślne są 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: numer pojedynczego portu |
| EnableCassandraEndpoint | Włącza interfejsu API rozwiązania Cassandra | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| EnableGremlinEndpoint | Zapewnia interfejs API języka Gremlin | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Numer portu używany dla punktu końcowego języka Gremlin. Wartość domyślna to 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<port\>: numer pojedynczego portu |
|TablePort | Numer portu używany dla punktu końcowego z tabeli platformy Azure. Wartość domyślna to 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<port\>: numer pojedynczego portu|
| KeyFile | Odczyt klucza autoryzacji z określonego pliku. Użyj opcji /GenKeyFile do wygenerowania pliku klucza | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<nazwa_pliku\>: Ścieżka do pliku |
| ResetDataPath | Rekursywnie usuwa wszystkie pliki w określonej ścieżce. Jeśli nie określisz ścieżki, jego wartość domyślna to %LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath [=<path>] | \<path\>: Ścieżka pliku  |
| StartTraces  |  Rozpoczęcie zbierania dzienników śledzenia debugowania. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Zatrzymaj zbieranie dzienników śledzenia debugowania. | CosmosDB.Emulator.exe /StopTraces  | |
|EnableTableEndpoint | Umożliwia usłudze Azure Table API | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|FailOnSslCertificateNameMismatch | Domyślnie Emulator generuje ponownie podpisany certyfikat SSL, jeśli certyfikat SAN nie obejmuje hostów emulatora nazwy domeny, lokalne IPv4 adresu, "localhost" i "127.0.0.1". Po wybraniu tej opcji emulator zakończy się niepowodzeniem przy uruchamianiu zamiast tego. Należy następnie użyć opcji /GenCert, aby utworzyć i zainstalować nowy certyfikat SSL z podpisem własnym. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Wygeneruj i zainstaluj nowy certyfikat SSL z podpisem własnym. Opcjonalnie łącznie z rozdzielaną przecinkami listą dodatkowe nazwy DNS do uzyskiwania dostępu do emulatora za pośrednictwem sieci. | CosmosDB.Emulator.exe /GenCert [ \<rozdzielaną przecinkami listę dodatkowe nazwy dns\>] | |
| DirectPorts |Określa porty używane przez połączenia bezpośrednie. Wartość domyślna to 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<porty_bezpośrednie\> | \<porty_bezpośrednie\>: lista rozdzielana przecinkami zawierająca cztery porty |
| Klucz |Klucz autoryzacji dla emulatora. Klucz musi być 64-bajtowym wektorem szyfrowanym algorytmem Base-64. | CosmosDB.Emulator.exe /Key:\<klucz\> | \<klucz\>: klucz musi być 64-bajtowym wektorem szyfrowanym algorytmem Base-64|
| EnableRateLimiting | Wskazuje, że ograniczanie szybkości żądania jest włączone. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Wskazuje, że ograniczanie szybkości żądania jest wyłączone. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Nie wyświetla interfejsu użytkownika emulatora. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Nie wyświetla Eksploratora danych podczas uruchamiania. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Określa maksymalną liczbę kontenerów podzielonym na partycje. Zobacz [Zmień liczbę kontenerów](#set-partitioncount) Aby uzyskać więcej informacji. | CosmosDB.Emulator.exe /PartitionCount=\<liczba_partycji\> | \<liczba_partycji\>: Maksymalna liczba dozwolonych jednej partycji kontenerów. Wartość domyślna to 25. Maksymalna dozwolona wartość to 250.|
| DefaultPartitionCount| Określa domyślny numer partycji dla kontener podzielony na partycje. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<domyślna_liczba_partycji\> | \<defaultpartitioncount\> wartość domyślna to 25.|
| AllowNetworkAccess | Włącza dostęp do emulatora za pośrednictwem sieci. Aby umożliwić dostęp w sieci, musisz także podać opcję /Key=\<ciąg_klucza\> lub /KeyFile=\<nazwa_pliku\>. | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<ciąg_klucza\> lub CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<nazwa_pliku\>| |
| NoFirewall | Nie zmieniaj reguły zapory, gdy jest używana opcja /AllowNetworkAccess. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Generuje nowy klucz autoryzacji i zapisuje go w określonym pliku. Wygenerowanego klucza można używać z opcją /Key lub /KeyFile. | CosmosDB.Emulator.exe /GenKeyFile=\<ścieżka_do_pliku_klucza\> | |
| Spójność | Ustawia domyślny poziom spójności dla konta. | CosmosDB.Emulator.exe /Consistency=\<spójność\> | \<spójność\>: wartość musi być jednym z następujących [poziomów spójności](consistency-levels.md): Sesja, Silna, Ostateczna lub Powiązana nieaktualność. Wartość domyślna to Sesja. |
| ? | Wyświetla komunikat pomocy.| | |

## <a id="set-partitioncount"></a>Zmień liczbę kontenerów

Domyślnie można utworzyć maksymalnie 25 kontenery o stałym rozmiarze (obsługiwana tylko przy użyciu zestawów SDK usługi Azure Cosmos DB) lub 5 nieograniczone kontenery przy użyciu emulatora usługi Azure Cosmos. Modyfikując **liczba partycji** wartości, można utworzyć maksymalnie 250 kontenery o stałym rozmiarze lub 50 nieograniczone kontenery lub dowolną kombinację dwóch, które nie może przekraczać 250 kontenery o stałym rozmiarze (gdzie jednego nieograniczonego kontenera = 5 o stałym rozmiarze kontenery). Jednak nie zaleca się skonfigurować emulator, aby uruchomić program z ponad 200 kontenery o stałym rozmiarze. Ze względu na obciążenie, które dodaje do operacje We/Wy dysku, który powodować nieprzewidywalne przekroczeń limitu czasu podczas przy użyciu punktu końcowego interfejsów API.


Jeśli spróbujesz utworzyć kontener po bieżąca liczba partycji została przekroczona, emulator zgłasza wyjątek ServiceUnavailable, następujący komunikat o błędzie.

"Niestety, mamy obecnie występuje duże zapotrzebowanie w tym regionie i nie można wykonać żądania w tej chwili. Firma Microsoft stale do dostosowania coraz więcej pojemności online oraz zalecamy, aby spróbować ponownie.
Prosimy do poczty e-mail askcosmosdb@microsoft.com w dowolnym momencie i z dowolnego powodu. ActivityId: 12345678-1234-1234-1234-123456789abc"

Aby zmienić liczbę kontenerów, które są dostępne w emulatora usługi Azure Cosmos, wykonaj następujące kroki:

1. Usuń wszystkie dane lokalne z emulatora usługi Azure Cosmos przez kliknięcie prawym przyciskiem myszy **emulatora usługi Azure Cosmos DB** ikonę na pasku zadań, a następnie klikając pozycję **resetowania danych...** .
2. Usunięcie wszystkich danych emulatora w tym folderze `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Zamknij wszystkie otwarte wystąpienia, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos DB** w obszarze powiadomień, a następnie klikając polecenie **Exit** (Zakończ). Zamykanie wszystkich wystąpień może potrwać około minuty.
4. Zainstaluj najnowszą wersję [emulatora usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Uruchom emulator z flagą PartitionCount, ustawiając wartość <= 250. Na przykład: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Sterowanie emulatorem

Emulator jest dostarczany za pomocą modułu programu PowerShell, aby uruchomić, zatrzymać, odinstaluj i pobierania stanu usługi. Uruchom następujące polecenie cmdlet, aby użyć modułu programu PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

albo umieść `PSModules` katalogu Twojej `PSModulesPath` i zaimportować go, jak pokazano w następującym poleceniu:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Poniżej przedstawiono podsumowanie poleceń umożliwiających sterowanie emulatorem za pomocą programu PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Składnia**

`Get-CosmosDbEmulatorStatus`

**Uwagi**

Zwraca jedną z następujących wartości ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running lub ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Składnia**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Uwagi**

Uruchamia emulator. Domyślnie to polecenie czeka, aż emulator zgłosi gotowość do odbierania żądań. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po uruchomieniu emulatora, użyj opcji -NoWait.

### `Stop-CosmosDbEmulator`

**Składnia**

 `Stop-CosmosDbEmulator [-NoWait]`

**Uwagi**

Zatrzymuje emulator. Domyślnie to polecenie czeka, aż emulator zostanie w pełni zamknięty. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po rozpoczęciu zamykania emulatora, użyj opcji -NoWait.

### `Uninstall-CosmosDbEmulator`

**Składnia**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Uwagi**

Odinstalowuje emulator i opcjonalnie usuwa całą zawartość katalogu $env:LOCALAPPDATA\CosmosDbEmulator.
To polecenie cmdlet gwarantuje zatrzymanie emulatora przed jego odinstalowaniem.

## <a name="running-on-docker"></a>Uruchamianie na platformie Docker

Emulator usługi Azure Cosmos może działać w Docker for Windows. Emulator nie współdziała z aplikacją Docker for Oracle Linux.

Po zainstalowaniu aplikacji [Docker for Windows](https://www.docker.com/docker-windows) przełącz się na kontenery systemu Windows, klikając prawym przyciskiem myszy ikonę programu Docker na pasku narzędzi i wybierając polecenie **Switch to Windows containers** (Przełącz na kontenery systemu Windows).

Następnie ściągnij obraz emulatora z usługi Docker Hub, uruchamiając następujące polecenie w wybranej powłoce.

```bash
docker pull microsoft/azure-cosmosdb-emulator
```
Aby uruchomić obraz, uruchom następujące polecenia.

W wierszu polecenia:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator
```

W programie PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator

```

Odpowiedź wygląda mniej więcej tak:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

W kliencie użyj punktu końcowego i klucza głównego z odpowiedzi i zaimportuj certyfikat SSL do hosta. Aby zaimportować certyfikat SSL, uruchom następujące polecenie w wierszu polecenia z uprawnieniami administratora:

W wierszu polecenia:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

W programie PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Zamknięcie interakcyjnej powłoki po uruchomieniu emulatora spowoduje zamknięcie kontenera emulatora.

Aby otworzyć Eksploratora danych, otwórz następujący adres URL w przeglądarce. Punkt końcowy emulatora znajduje się w powyższym komunikacie odpowiedzi.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Skorzystaj z następujących porad, aby ułatwić rozwiązywanie problemów związanych z emulatora usługi Azure Cosmos:

- Jeśli po zainstalowaniu nowej wersji emulatora występują błędy, upewnij się, że dane zostały zresetowane. Kliknij prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos na pasku zadań, a następnie klikając pozycję Resetuj dane mogą resetować swoje dane... Jeśli to nie rozwiąże błędy, możesz odinstalować emulatora i wszystkie starsze wersje emulatora Jeśli znaleziono, usunąć katalogu "C:\Program files\Azure Emulator usługi Cosmos DB" i ponownie zainstaluj emulator. Odpowiednie instrukcje znajdziesz w sekcji [Odinstalowywanie lokalnego emulatora](#uninstall).

- Jeśli Emulator usługi Azure Cosmos ulegnie awarii, Zbierz pliki zrzutu z folderu "% LOCALAPPDATA%\CrashDumps" kompresować i dołącz je do wiadomości e-mail do [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Jeśli występują awarie w `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, może to być objawem gdzie liczniki wydajności są w stanie uszkodzenia. Zazwyczaj uruchamiając następujące polecenie w wierszu polecenia administratora z rozwiązuje problem:

  ```cmd
  lodctr /R
   ```

- Jeśli wystąpi problem z łącznością, [zbierz pliki śledzenia](#trace-files), skompresuj je i wyślij w wiadomości e-mail na adres [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Jeśli zostanie wyświetlony komunikat **Service Unavailable** (Usługa niedostępna), może to wskazywać na błąd inicjacji stosu sieciowego. Sprawdź, czy masz zainstalowanego klienta Pulse Secure lub Juniper Networks — ich sterowniki filtrów sieci mogą powodować problemy. Odinstalowanie sterowników filtrów sieci innych firm zwykle pozwala rozwiązać problem. Alternatywnie rozpoczynać emulator /DisableRIO, który spowoduje przełączenie komunikacji sieciowej emulatora do regularnego Winsock. 

- Kiedy emulator jest uruchomiony, jeśli komputer przejdzie w tryb uśpienia lub uruchomi dowolną aktualizację systemu operacyjnego, możesz zobaczyć komunikat **Service is currently unavailable** (Usługa jest obecnie niedostępna). Resetuj dane to emulator, klikając prawym przyciskiem myszy ikonę, która jest wyświetlana na pasku powiadomień systemu windows, a następnie wybierz pozycję **Resetuj dane**.

### <a id="trace-files"></a>Zbieranie plików śledzenia

Aby zebrać dane śledzenia debugowania, uruchom następujące polecenia w wierszu polecenia z uprawnieniami administratora:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. W obszarze powiadomień sprawdź, czy program został zamknięty — może to potrwać około minuty. Możesz też po prostu kliknąć **zakończenia** w interfejsie użytkownika emulatora usługi Azure Cosmos.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Odtwórz problem. Jeśli Eksplorator danych nie działa, po prostu poczekaj na otwarcie przeglądarki, która po kilku sekundach przechwyci błąd.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Znajdź plik docdbemulator_000001.etl w folderze `%ProgramFiles%\Azure Cosmos DB Emulator`.
7. Wyślij plik etl razem z krokami umożliwiającymi odtworzenie problemu na adres [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) w celu debugowania.

### <a id="uninstall"></a>Odinstalowywanie lokalnego emulatora

1. Zamknij wszystkie otwarte wystąpienia lokalnego emulatora prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos na pasku zadań, a następnie klikając pozycję Zakończ. Zamykanie wszystkich wystąpień może potrwać około minuty.
2. W polu wyszukiwania systemu Windows wpisz ciąg **Aplikacje i funkcje** i kliknij wynik **Aplikacje i funkcje (Ustawienia systemowe)**.
3. Przewiń listę aplikacji, wybierz pozycję **Emulator usługi Azure Cosmos DB**, kliknij przycisk **Odinstaluj**, potwierdź i ponownie kliknij przycisk **Odinstaluj**.
4. Gdy aplikacja zostanie odinstalowana, przejdź do katalogu `%LOCALAPPDATA%\CosmosDBEmulator` i usuń folder.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku opisaliśmy sposób użycia lokalnego emulatora do bezpłatnego programowania w środowisku lokalnym. Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak wyeksportować certyfikaty SSL emulatora.

> [!div class="nextstepaction"]
> [Eksportowanie certyfikatów emulatora usługi Azure Cosmos](local-emulator-export-ssl-certificates.md)
