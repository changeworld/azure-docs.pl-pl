---
title: Rozwijaj lokalnie za pomocą emulatora usługi Azure Cosmos
description: Za pomocą emulatora usługi Azure Cosmos można bezpłatnie tworzyć i testować aplikację lokalnie bez tworzenia subskrypcji platformy Azure.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: 9650bb3214c22926427717569f718ca0426ed729
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618747"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Użyj emulatora usługi Azure Cosmos do lokalnego rozwoju i testowania

Emulator usługi Azure Cosmos udostępnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programisty. Za pomocą emulatora usługi Azure Cosmos można tworzyć i testować aplikację lokalnie, bez tworzenia subskrypcji platformy Azure lub ponoszenia żadnych kosztów. Jeśli użytkownik jest zadowolony z działania aplikacji w emulatorze usługi Azure Cosmos, można przełączyć się do korzystania z konta usługi Azure Cosmos w chmurze.

Można tworzyć przy użyciu azure cosmos emulator z [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)i [tabeli](local-emulator.md#table-api) kont interfejsu API. Jednak w tej chwili widok Eksploratora danych w emulatorze w pełni obsługuje klientów tylko dla interfejsu API SQL. 

## <a name="how-the-emulator-works"></a>Jak działa emulator

Emulator usługi Azure Cosmos zapewnia emulację wysokiej wierności usługi Usługi Azure Cosmos DB. Obsługuje identyczne funkcje jak usługa Azure Cosmos DB, w tym obsługa tworzenia i wykonywania zapytań o dane, inicjowanie obsługi administracyjnej i skalowanie kontenerów oraz wykonywanie procedur i wyzwalaczy przechowywanych. Można tworzyć i testować aplikacje przy użyciu emulatora usługi Azure Cosmos i wdrażać je na platformie Azure w skali globalnej, po prostu wykonując jedną zmianę konfiguracji w punkcie końcowym połączenia dla usługi Azure Cosmos DB.

Chociaż emulator dokładnie odzwierciedla działanie usługi Azure Cosmos DB, różni się od niej pod względem wdrożenia. Na przykład emulator używa standardowych składników systemu operacyjnego — między innymi lokalnego systemu plików jako magazynu trwałego i stosu protokołu HTTPS na potrzeby łączności. Funkcje, które opierają się na infrastrukturze platformy Azure, takie jak replikacja globalna, jednocyfrowe opóźnienie milisekundy dla odczytów/zapisów i przestrajalne poziomy spójności nie mają zastosowania.

Dane można migrować między emulatorem usługi Azure Cosmos i usługą Azure Cosmos DB przy użyciu [narzędzia do migracji danych usługi Azure Cosmos DB.](https://github.com/azure/azure-documentdb-datamigrationtool)

Emulator usługi Azure Cosmos można uruchomić w kontenerze platformy Windows Docker, zobacz Centrum platformy `Dockerfile` [Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) dla polecenia ściągania platformy docker i [GitHub,](https://github.com/Azure/azure-cosmos-db-emulator-docker) aby uzyskać więcej informacji.

## <a name="differences-between-the-emulator-and-the-service"></a>Różnice między emulatorem a usługą

Ponieważ emulator usługi Azure Cosmos udostępnia emulowane środowisko uruchomione na lokalnej stacji roboczej dla deweloperów, istnieją pewne różnice w funkcjonalności między emulatorem a kontem usługi Azure Cosmos w chmurze:

* Obecnie Eksplorator danych w emulatorze obsługuje klientów interfejsu API SQL. Widok i operacje Eksploratora danych dla interfejsów API bazy danych usługi Azure Cosmos, takich jak MongoDB, Table, Graph i Cassandra API, nie są w pełni obsługiwane.
* Emulator usługi Azure Cosmos obsługuje tylko jedno konto stałe i dobrze znany klucz główny. Regeneracja kluczy nie jest możliwe w emulatorze usługi Azure Cosmos, jednak klucz domyślny można zmienić za pomocą opcji wiersza polecenia.
* Emulator usługi Azure Cosmos nie jest skalowalną usługą i nie będzie obsługiwać dużej liczby kontenerów.
* Emulator usługi Azure Cosmos nie oferuje różnych [poziomów spójności usługi Azure Cosmos DB.](consistency-levels.md)
* Emulator usługi Azure Cosmos nie oferuje [replikacji wieloregiona.](distribute-data-globally.md)
* Ponieważ kopia emulatora usługi Azure Cosmos może nie zawsze być aktualna z najnowszymi zmianami w usłudze Azure Cosmos DB, należy odwołać się do [planowania pojemności usługi Azure Cosmos DB,](https://www.documentdb.com/capacityplanner) aby dokładnie oszacować potrzeby dotyczące przepływności produkcji aplikacji.
* Podczas korzystania z azure cosmos emulator, domyślnie można utworzyć maksymalnie 25 kontenerów o stałym rozmiarze (obsługiwane tylko przy użyciu usługi Azure Cosmos DB SDKs) lub 5 nieograniczony kontenerów przy użyciu emulatora usługi Azure Cosmos. Aby uzyskać więcej informacji na temat zmiany tej wartości, zobacz [Ustawianie wartości PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Wymagania systemowe

Emulator usługi Azure Cosmos ma następujące wymagania sprzętowe i programowe:

* Wymagania dotyczące oprogramowania
  * Windows Server 2012 R2, Windows Server 2016 lub Windows 10
  * 64-bitowy system operacyjny
* Minimalne wymagania sprzętowe
  * 2 GB pamięci RAM
  * 10 GB dostępnego miejsca na dysku twardym

## <a name="installation"></a>Instalacja

Emulator usługi Azure Cosmos można pobrać i zainstalować z [Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator) lub można uruchomić emulator w programie Docker dla systemu Windows. Aby uzyskać instrukcje dotyczące korzystania z emulatora w aplikacji Docker for Windows, zobacz sekcję [Uruchamianie na platformie Docker](#running-on-docker).

> [!NOTE]
> Aby zainstalować, skonfigurować i uruchomić emulator usługi Azure Cosmos, musisz mieć uprawnienia administracyjne na komputerze. Emulator utworzy/doda certyfikat, a także ustawi reguły zapory w celu uruchomienia jego usług; w związku z tym jest konieczne dla emulatora, aby móc wykonać takie operacje.

## <a name="running-on-windows"></a>Uruchamianie w systemie Windows

Aby uruchomić emulator usługi Azure Cosmos, wybierz przycisk Start lub naciśnij klawisz Windows. Rozpocznij wpisywanie **emulatora usługi Azure Cosmos**i wybierz emulator z listy aplikacji.

![Wybierz przycisk Start lub naciśnij klawisz Windows, zacznij wpisywać **Azure Cosmos Emulator** i wybierz emulator z listy aplikacji](./media/local-emulator/database-local-emulator-start.png)

Po uruchomieniu emulatora pojawi się ikona w obszarze powiadomień paska zadań systemu Windows. ![Powiadomienie o pasku zadań lokalnego emulatora emulatora usługi Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

Emulator usługi Azure Cosmos domyślnie działa na komputerze lokalnym ("localhost") nasłuchiwanie na porcie 8081.

Emulator usługi Azure Cosmos `C:\Program Files\Azure Cosmos DB Emulator` jest instalowany domyślnie. Można również uruchomić i zatrzymać emulator z poziomu wiersza polecenia. Więcej informacji zawiera [dokumentacja narzędzia wiersza polecenia](#command-line).

## <a name="start-data-explorer"></a>Uruchamianie Eksploratora danych

Po uruchomieniu emulatora usługi Azure Cosmos automatycznie otwiera Eksplorator danych usługi Azure Cosmos w przeglądarce. Adres jest wyświetlany jako `https://localhost:8081/_explorer/index.html`. Jeśli zamkniesz eksploratora i chcesz go ponownie otworzyć później, możesz otworzyć adres URL w przeglądarce lub uruchomić go z emulatora usługi Azure Cosmos w ikonie zasobnika systemu Windows, jak pokazano poniżej.

![Uruchamianie eksploratora danych lokalnego emulatora emulatora usługi Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Sprawdzanie dostępności aktualizacji

Eksplorator danych informuje o możliwości pobrania nowych aktualizacji.

> [!NOTE]
> Dane utworzone w jednej wersji emulatora usługi Azure Cosmos (zobacz %LOCALAPPDATA%\CosmosDBEmulator lub ustawienia opcjonalne ścieżki danych) nie są dostępne w przypadku korzystania z innej wersji. Jeśli trzeba utrwalić dane na dłuższą metę, zaleca się przechowywanie tych danych na koncie usługi Azure Cosmos, a nie w emulatorze usługi Azure Cosmos.

## <a name="authenticating-requests"></a>Uwierzytelnianie żądań

Podobnie jak w usłudze Azure Cosmos DB w chmurze, każde żądanie, które zostanie złożony przeciwko azure cosmos emulator musi być uwierzytelniony. Emulator usługi Azure Cosmos obsługuje jedno konto stałe i dobrze znany klucz uwierzytelniania do uwierzytelniania klucza głównego. To konto i klucz są tylko poświadczenia dozwolone do użycia z emulatorem usługi Azure Cosmos. Oto one:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Klucz główny obsługiwany przez emulator usługi Azure Cosmos jest przeznaczony do użytku tylko z emulatorem. Nie można użyć produkcyjnego konta usługi Azure Cosmos DB i klucza z emulatorem usługi Azure Cosmos.

> [!NOTE]
> Jeśli emulator został uruchomiony z opcją /Key, użyj wygenerowanego `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`klucza zamiast . Aby uzyskać więcej informacji na temat opcji /Key, zobacz [Odwołanie do wiersza polecenia.](#command-line)

Podobnie jak w witrynie Azure Cosmos DB, emulator usługi Azure Cosmos obsługuje tylko bezpieczną komunikację za pośrednictwem protokołu TLS.

## <a name="running-on-a-local-network"></a>Uruchamianie w sieci lokalnej

Emulator możesz uruchomić w sieci lokalnej. Aby włączyć dostęp do `/AllowNetworkAccess` sieci, należy określić opcję w `/Key=key_string` [wierszu polecenia,](#command-line-syntax)która wymaga również określenia lub `/KeyFile=file_name`. Można użyć `/GenKeyFile=file_name` do wygenerowania pliku z losowym kluczem z góry. Następnie można przekazać, `/KeyFile=file_name` `/Key=contents_of_file`że do lub .

Aby włączyć dostęp do sieci po raz pierwszy użytkownik powinien zamknąć emulator i usunąć katalog danych emulatora (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Programowanie za pomocą emulatora

### <a name="sql-api"></a>Interfejs API SQL

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie można użyć dowolnego obsługiwanego [pakietu Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) lub [interfejsu API REST usługi Azure Cosmos DB](/rest/api/cosmos-db/) do interakcji z emulatorem. Emulator usługi Azure Cosmos zawiera również wbudowany Eksplorator danych, który umożliwia tworzenie kontenerów dla interfejsu API SQL lub usługi Cosmos DB dla interfejsu API bazy danych Mongo DB oraz wyświetlanie i edytowanie elementów bez pisania kodu.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie można użyć [interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](mongodb-introduction.md) do interakcji z emulatorem. Uruchom emulator z wiersza polecenia jako administrator z "/EnableMongoDbEndpoint". Następnie użyj następującego ciągu połączenia, aby połączyć się z kontem interfejsu API usługi MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Interfejs API tabel

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie można użyć [pakietu Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) do interakcji z emulatorem. Uruchom emulator z wiersza polecenia jako administrator z "/EnableTableEndpoint". Następnie uruchom następujący kod, aby połączyć się z kontem interfejsu API tabeli:

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

Uruchom emulator z wiersza polecenia administratora z "/EnableCassandraEndpoint". Alternatywnie można również ustawić `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`zmienną środowiskową .

* [Instalowanie języka Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Instalowanie Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Uruchom następujące polecenia w zwykłym oknie wiersza polecenia:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* W powłoce CQLSH uruchom następujące polecenia, aby połączyć się z punktem końcowym Cassandra:

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

Uruchom emulator z wiersza polecenia administratora z "/EnableGremlinEndpoint". Alternatywnie można również ustawić zmienną środowiskową`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Zainstaluj apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* W Eksploratorze danych emulatora utwórz bazę danych "db1" i kolekcję "coll1"; dla klucza partycji wybierz "/name"

* Uruchom następujące polecenia w zwykłym oknie wiersza polecenia:

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

* W powłoce Gremlin uruchom następujące polecenia, aby połączyć się z punktem końcowym Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>Eksportowanie certyfikatu TLS/SSL

Do nawiązywania bezpiecznego połączenia z lokalnym emulatorem usługi Azure Cosmos DB języki i środowisko uruchomieniowe .NET używają magazynu certyfikatów systemu Windows. Inne języki korzystają z własnych metod używania certyfikatów i zarządzania nimi. Język Java używa własnego [magazynu certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), a język Python — [otok gniazd](https://docs.python.org/2/library/ssl.html).

Aby uzyskać certyfikat do użycia z językami i środowiskami uruchomieniowymi, które nie zapewniają integracji z magazynem certyfikatów systemu Windows, trzeba wyeksportować ten certyfikat przy użyciu menedżera certyfikatów systemu Windows. Można go uruchomić, uruchamiając plik certlm.msc lub postępuj zgodnie z instrukcjami krok po kroku w [obszarze Eksportowanie certyfikatów emulatora usługi Azure Cosmos.](./local-emulator-export-ssl-certificates.md) Po uruchomieniu menedżera certyfikatów otwórz sekcję certyfikatów osobistych, jak pokazano poniżej, i wyeksportuj certyfikat o przyjaznej nazwie „DocumentDBEmulatorCertificate” jako plik Certyfikat X.509 szyfrowany algorytmem Base-64 (CER).

![Certyfikat emulatora lokalnego usługi Azure Cosmos DB TLS/SSL](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Aby zaimportować certyfikat X.509 do magazynu certyfikatów Java, postępuj zgodnie z instrukcjami zawartymi w artykule [Adding a Certificate to the Java CA Certificates Store (Dodawanie certyfikatu do magazynu certyfikatów Java CA)](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Po zaimportowaniu certyfikatu do magazynu certyfikatów klienci interfejsu API usługi SQL i usługi Azure Cosmos DB dla usługi MongoDB będą mogli połączyć się z emulatorem usługi Azure Cosmos.

Podczas łączenia się z emulatorem z modułów SDK języka Python i Node.js weryfikacja TLS jest wyłączona.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Dokumentacja narzędzia wiersza polecenia
Z lokalizacji instalacji można użyć wiersza polecenia, aby uruchomić i zatrzymać emulator, skonfigurować opcje i wykonać inne operacje.

### <a name="command-line-syntax"></a>Składnia wiersza polecenia

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Aby wyświetlić listę opcji, wpisz ciąg `Microsoft.Azure.Cosmos.Emulator.exe /?` w wierszu polecenia.

|**Opcja** | **Opis** | **Polecenie**| **Argumenty**|
|---|---|---|---|
|[Bez argumentów] | Uruchamia emulator usługi Azure Cosmos z ustawieniami domyślnymi. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |Wyświetla listę obsługiwanych argumentów wiersza polecenia.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Pobiera stan emulatora usługi Azure Cosmos. Stan jest wskazywany przez kod zakończenia: 1 = uruchamiany, 2 = uruchomiony, 3 = zatrzymany. Kod zakończenia o wartości ujemnej informuje o wystąpieniu błędu. Inne dane wyjściowe nie są generowane. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Zamyka emulator usługi Azure Cosmos.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Określa ścieżkę przechowywania plików danych. Wartość domyślna to %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<ścieżka danych\> | \<ścieżka_do_danych\>: dostępna ścieżka |
|Port | Określa numer portu używanego przez emulatora. Wartość domyślna to 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>: numer pojedynczego portu |
| ComputePort (ComputePort) | Określono numer portu, który ma być używany dla usługi Compute Interop Gateway. Port sondy punktu końcowego HTTP bramy jest obliczany jako ComputePort + 79. W związku z tym ComputePort i ComputePort + 79 musi być otwarty i dostępny. Wartość domyślna to 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort= computeport =\<computeport\> | \<computeport\>: Numer pojedynczego portu |
| EnableMongoDbEndpoint=3.2 | Włącza interfejs API MongoDB 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Włącza interfejs API MongoDB 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Określa numer portu używanego w celu zapewnienia zgodności z interfejsem API usługi MongoDB. Wartość domyślna to 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<port_mongo\>: numer pojedynczego portu|
| EnableCassandraEndpoint (Włącz punkt obsługi | Włącza API Cassandra | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| Port Cassandra | Określa numer portu, który ma być używany dla punktu końcowego Cassandra. Wartość domyślna to 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: Numer portu jednoportowego |
| Włącz punkt | Włącza API Gremlin | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| Port Gremlin | Numer portu do użycia dla punktu końcowego Gremlin. Wartość domyślna to 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: numer pojedynczego portu |
|Włącz tabelę.ń | Włącza interfejs API tabel platformy Azure | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|Port tabeli | Numer portu do użycia dla punktu końcowego tabeli platformy Azure. Wartość domyślna to 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: numer pojedynczego portu|
| Keyfile | Odczyt klucza autoryzacji z określonego pliku. Użyj opcji /GenKeyFile, aby wygenerować plik kluczy | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Ścieżka do pliku |
| Resetowanie ścieżki danych | Rekursywnie usuwa wszystkie pliki w określonej ścieżce. Jeśli ścieżka nie zostanie określona, wartość domyślna to %LOCALAPPDATA%\CosmosDbEmulator | Ścieżka microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<ścieżka> | \<ścieżka\>: Ścieżka pliku  |
| StartTraces (Ślady początkowe)  |  Rozpocznij zbieranie dzienników śledzenia debugowania przy użyciu funkcji LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces (Ślady stopu)     | Zatrzymaj zbieranie dzienników śledzenia debugowania przy użyciu funkcji LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces (Traktowanie początkowe)  |  Rozpocznij zbieranie dzienników śledzenia debugowania za pomocą narzędzia Dogranie wydajności systemu Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces (StopWprTraces)     | Zatrzymaj zbieranie dzienników śledzenia debugowania za pomocą narzędzia Dogranie wydajności systemu Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Domyślnie emulator ponownie generuje swój certyfikat TLS/SSL z podpisem własnym, jeśli sieć SAN certyfikatu nie zawiera nazwy domeny hosta emulatora, lokalnego adresu IPv4, "localhost" i "127.0.0.1". Przy tej opcji emulator zakończy się niepowodzeniem podczas uruchamiania. Następnie należy użyć opcji /GenCert, aby utworzyć i zainstalować nowy certyfikat TLS/SSL z podpisem własnym. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert ( GenCert ) | Generowanie i instalowanie nowego certyfikatu TLS/SSL z podpisem własnym. opcjonalnie zawiera listę dodatkowych nazw DNS oddzielonych przecinkami w celu uzyskania dostępu do emulatora za pośrednictwem sieci. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: Opcjonalna lista dodatkowych nazw dns oddzielona przecinkami  |
| DirectPorts |Określa porty używane przez połączenia bezpośrednie. Wartość domyślna to 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<porty bezpośrednie\> | \<porty_bezpośrednie\>: lista rozdzielana przecinkami zawierająca cztery porty |
| Klucz |Klucz autoryzacji dla emulatora. Klucz musi być 64-bajtowym wektorem szyfrowanym algorytmem Base-64. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<klucz\> | \<klucz\>: klucz musi być 64-bajtowym wektorem szyfrowanym algorytmem Base-64|
| EnableRateLimiting | Wskazuje, że ograniczanie szybkości żądania jest włączone. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Wskazuje, że ograniczanie szybkości żądania jest wyłączone. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Nie wyświetla interfejsu użytkownika emulatora. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Nie wyświetla Eksploratora danych podczas uruchamiania. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Określa maksymalną liczbę kontenerów podzielonych na partycje. Zobacz [Zmienianie liczby kontenerów,](#set-partitioncount) aby uzyskać więcej informacji. | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<liczba partycji\> | \<partitioncount\>: Maksymalna liczba dozwolonych kontenerów pojedynczych partycji. Wartość domyślna to 25. Maksymalna dozwolona wartość to 250.|
| DefaultPartitionCount| Określa domyślną liczbę partycji dla kontenera podzielonego na partycje. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<domyślny licznik partycji\> | \<defaultpartitioncount\> Wartość domyślna to 25.|
| AllowNetworkAccess | Włącza dostęp do emulatora za pośrednictwem sieci. Aby umożliwić dostęp w sieci, musisz także podać opcję /Key=\<ciąg_klucza\> lub /KeyFile=\<nazwa_pliku\>. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> lub Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Nie dopasowuj reguł zapory, gdy używana jest opcja /AllowNetworkAccess. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Generuje nowy klucz autoryzacji i zapisuje go w określonym pliku. Wygenerowanego klucza można używać z opcją /Key lub /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<ścieżka do pliku klucza\> | |
| Spójność | Ustawia domyślny poziom spójności dla konta. | Microsoft.Azure.Cosmos.Emulator.exe /Spójność =\<spójność\> | \<spójność\>: wartość musi być jednym z następujących [poziomów spójności](consistency-levels.md): Sesja, Silne, Ostateczne lub Powiązana nieaktualność. Wartość domyślna to Sesja. |
| ? | Wyświetla komunikat pomocy.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Zmienianie liczby kontenerów

Domyślnie można utworzyć maksymalnie 25 kontenerów o stałym rozmiarze (obsługiwanych tylko przy użyciu zestawów SDK usługi Azure Cosmos DB) lub 5 nieograniczonych kontenerów przy użyciu emulatora usługi Azure Cosmos. Modyfikując **PartitionCount** wartość, można utworzyć do 250 kontenerów o stałym rozmiarze lub 50 nieograniczony kontenerów lub dowolną kombinację dwóch, które nie przekracza 250 kontenerów o stałym rozmiarze (gdzie jeden nieograniczony kontener = 5 kontenerów o stałym rozmiarze). Jednak nie jest zalecane, aby skonfigurować emulator do uruchamiania z ponad 200 kontenerów o stałym rozmiarze. Ze względu na obciążenie, które dodaje do operacji we/wy dysku, co powoduje nieprzewidywalne limity czasu podczas korzystania z interfejsów API punktu końcowego.

Jeśli spróbujesz utworzyć kontener po przekroczeniu bieżącej liczby partycji, emulator zgłasza wyjątek ServiceUnavailable, z następującym komunikatem.

"Niestety, obecnie mamy duże zapotrzebowanie w tym regionie i nie możemy spełnić Twojego żądania w tej chwili. Nieustannie pracujemy nad zwiększeniem pojemności online i zachęcamy do ponownej próby.
ActivityId: 12345678-1234-1234-1234-123456789abc"

Aby zmienić liczbę kontenerów dostępnych w emulatorze usługi Azure Cosmos, uruchom następujące kroki:

1. Usuń wszystkie lokalne dane emulatora usługi Azure Cosmos, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos DB** na zasobniku systemowym, a następnie klikając polecenie **Resetuj dane...**.
2. Usuń wszystkie dane emulatora w tym folderze `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Zamknij wszystkie otwarte wystąpienia, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos DB** w obszarze powiadomień, a następnie klikając polecenie **Exit** (Zakończ). Zamykanie wszystkich wystąpień może potrwać około minuty.
4. Zainstaluj najnowszą wersję [emulatora usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Uruchom emulator z flagą PartitionCount, ustawiając wartość <= 250. Na przykład: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Sterowanie emulatorem

Emulator jest wyposażony w moduł programu PowerShell, aby uruchomić, zatrzymać, odinstalować i pobrać stan usługi. Uruchom następujące polecenie cmdlet, aby użyć modułu programu PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

lub umieść `PSModules` katalog na `PSModulesPath` swoim i zaimportuj go w następującym poleceniu:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Poniżej przedstawiono podsumowanie poleceń umożliwiających sterowanie emulatorem za pomocą programu PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Składni**

`Get-CosmosDbEmulatorStatus`

**Uwagi**

Zwraca jedną z następujących wartości ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running lub ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Składni**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Uwagi**

Uruchamia emulator. Domyślnie to polecenie czeka, aż emulator zgłosi gotowość do odbierania żądań. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po uruchomieniu emulatora, użyj opcji -NoWait.

### `Stop-CosmosDbEmulator`

**Składni**

 `Stop-CosmosDbEmulator [-NoWait]`

**Uwagi**

Zatrzymuje emulator. Domyślnie to polecenie czeka, aż emulator zostanie w pełni zamknięty. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po rozpoczęciu zamykania emulatora, użyj opcji -NoWait.

### `Uninstall-CosmosDbEmulator`

**Składni**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Uwagi**

Odinstalowuje emulator i opcjonalnie usuwa całą zawartość katalogu $env:LOCALAPPDATA\CosmosDbEmulator.
To polecenie cmdlet gwarantuje zatrzymanie emulatora przed jego odinstalowaniem.

## <a name="running-on-docker"></a>Uruchamianie na platformie Docker

Emulator usługi Azure Cosmos można uruchomić w programie Docker dla systemu Windows. Emulator nie współdziała z aplikacją Docker for Oracle Linux.

Po zainstalowaniu aplikacji [Docker for Windows](https://www.docker.com/docker-windows) przełącz się na kontenery systemu Windows, klikając prawym przyciskiem myszy ikonę programu Docker na pasku narzędzi i wybierając polecenie **Switch to Windows containers** (Przełącz na kontenery systemu Windows).

Następnie ściągnij obraz emulatora z usługi Docker Hub, uruchamiając następujące polecenie w wybranej powłoce.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Aby uruchomić obraz, uruchom następujące polecenia.

W wierszu polecenia:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Jeśli po uruchomieniu polecenia uruchom docker zostanie wyświetlony błąd konfliktu portu (określony port jest już używany), można przekazać port niestandardowy, zmieniając numery portów. Na przykład można zmienić "-p 8081:8081" na "-p 443:8081"

W programie PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

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

Teraz użyj punktu końcowego i klucza głównego z odpowiedzi w kliencie i zaimportuj certyfikat TLS/SSL do hosta. Aby zaimportować certyfikat TLS/SSL, wykonaj następujące czynności z wiersza polecenia administratora:

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

Zamknięcie powłoki interaktywnej po uruchomieniu emulatora spowoduje zamknięcie kontenera emulatora.

Aby otworzyć Eksploratora danych, otwórz następujący adres URL w przeglądarce. Punkt końcowy emulatora znajduje się w powyższym komunikacie odpowiedzi.

    https://<emulator endpoint provided in response>/_explorer/index.html

Jeśli masz aplikację kliencką platformy .NET uruchomiona w kontenerze platformy docker systemu Linux i jeśli używasz emulatora usługi Azure Cosmos na komputerze-hoście, postępuj zgodnie z poniższą sekcją dla systemu Linux, aby zaimportować certyfikat do kontenera platformy dokowane systemu Linux.

## <a name="running-on-mac-or-linux"></a>Uruchamianie na komputerach Mac lub Linux<a id="mac"></a>

Obecnie emulator usługi Cosmos można uruchomić tylko w systemie Windows. Użytkownicy korzystający z komputerów Mac lub Linux mogą uruchomić emulator na maszynie wirtualnej systemu Windows, na maszynie wirtualnej hostowanego przez funkcję hypervisor, takiej jak Parallels lub VirtualBox. Poniżej znajdują się kroki, aby włączyć to.

W ramach maszyny Wirtualnej systemu Windows uruchom poniższe polecenie i zanotuj adres IPv4.

```cmd
ipconfig.exe
```

W aplikacji należy zmienić identyfikator URI używany jako punkt końcowy, aby `ipconfig.exe` użyć `localhost`adresu IPv4 zwracanego przez zamiast .

W następnym kroku, z poziomu maszyny Wirtualnej systemu Windows, uruchom emulator usługi Cosmos z wiersza polecenia przy użyciu następujących opcji.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Na koniec musimy zaimportować certyfikat Emulator URZĘDU certyfikacji do środowiska Linux lub Mac.

### <a name="linux"></a>Linux

Jeśli pracujesz na Linuksie, .NET przekaźniki na OpenSSL do sprawdzania poprawności:

1. [Wyeksportuj certyfikat w formacie PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX jest dostępny podczas eksportowania klucza prywatnego). 

1. Skopiuj ten plik PFX do środowiska Linuksa.

1. Konwertowanie pliku PFX na plik CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Skopiuj plik CRT do folderu zawierającego certyfikaty niestandardowe w dystrybucji systemu Linux. Powszechnie w dystrybucjach Debiana znajduje `/usr/local/share/ca-certificates/`się na .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Zaktualizuj certyfikaty `/etc/ssl/certs/` urzędu certyfikacji, które zaktualizują folder.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

W przypadku pracy na komputerze Mac należy wykonać następujące czynności:

1. [Wyeksportuj certyfikat w formacie PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX jest dostępny podczas eksportowania klucza prywatnego).

1. Skopiuj ten plik PFX do środowiska Mac.

1. Otwórz aplikację *Dostęp do pęku kluczy* i zaimportuj plik PFX.

1. Otwórz listę certyfikatów i zidentyfikuj listę z nazwą `localhost`.

1. Otwórz menu kontekstowe dla danego elementu, wybierz pozycję *Pobierz element* i w obszarze *Ufaj* > podczas korzystania z tej opcji*certyfikatu* wybierz pozycję *Zawsze ufaj*. 

   ![Otwórz menu kontekstowe dla danego elementu, wybierz pozycję Pobierz element i w obszarze Zaufanie — korzystając z tej opcji certyfikatu, wybierz pozycję Zawsze ufaj](./media/local-emulator/mac-trust-certificate.png)

Po wykonaniu tych kroków środowisko będzie ufać certyfikat używany przez emulator `/AllowNetworkAccess`podczas łączenia się z adresem IP udostępnia przez .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Skorzystaj z następujących wskazówek, aby ułatwić rozwiązywanie problemów napotkanych za pomocą emulatora usługi Azure Cosmos:

- Jeśli po zainstalowaniu nowej wersji emulatora występują błędy, upewnij się, że dane zostały zresetowane. Możesz zresetować dane, klikając prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos na zasobniku systemowym, a następnie klikając polecenie Resetuj dane.... Jeśli to nie naprawi błędów, można odinstalować emulator i wszystkie starsze wersje emulatora, jeśli zostanie znaleziony, usuń katalog "C:\Program files\Azure Cosmos DB Emulator" i ponownie zainstalować emulator. Odpowiednie instrukcje znajdziesz w sekcji [Odinstalowywanie lokalnego emulatora](#uninstall).

- Jeśli emulator usługi Azure Cosmos ulegnie awarii, zbierz pliki zrzutu z folderu '%LOCALAPPDATA%\CrashDumps', skompresuj je i otwórz bilet pomocy technicznej z [witryny Azure portal](https://portal.azure.com).

- Jeśli wystąpią `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`awarie w , może to być objaw, w którym liczniki wydajności są w stanie uszkodzonym. Zwykle uruchomione następujące polecenie z wiersza polecenia administratora rozwiązuje ten problem:

  ```cmd
  lodctr /R
   ```

- Jeśli napotkasz problem z łącznością, [zbierz pliki śledzenia,](#trace-files)skompresuj je i otwórz bilet pomocy technicznej w [witrynie Azure portal](https://portal.azure.com).

- Jeśli zostanie wyświetlony komunikat **Service Unavailable** (Usługa niedostępna), może to wskazywać na błąd inicjacji stosu sieciowego. Sprawdź, czy masz zainstalowanego klienta Pulse Secure lub Juniper Networks — ich sterowniki filtrów sieci mogą powodować problemy. Odinstalowanie sterowników filtrów sieci innych firm zwykle pozwala rozwiązać problem. Alternatywnie należy uruchomić emulator z /DisableRIO, który przełączy komunikację sieci emulatora do regularnego Winsock. 

- Kiedy emulator jest uruchomiony, jeśli komputer przejdzie w tryb uśpienia lub uruchomi dowolną aktualizację systemu operacyjnego, możesz zobaczyć komunikat **Service is currently unavailable** (Usługa jest obecnie niedostępna). Zresetuj dane emulatora, klikając prawym przyciskiem myszy ikonę wyświetlaną na tacy powiadomień systemu Windows i wybierz pozycję **Resetuj dane**.

### <a name="collect-trace-files"></a><a id="trace-files"></a>Zbieranie plików śledzenia

Aby zebrać dane śledzenia debugowania, uruchom następujące polecenia w wierszu polecenia z uprawnieniami administratora:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. W obszarze powiadomień sprawdź, czy program został zamknięty — może to potrwać około minuty. Można również po prostu kliknij przycisk **Zakończ** w interfejsie użytkownika emulatora usługi Azure Cosmos.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Odtwórz problem. Jeśli Eksplorator danych nie działa, po prostu poczekaj na otwarcie przeglądarki, która po kilku sekundach przechwyci błąd.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Znajdź plik docdbemulator_000001.etl w folderze `%ProgramFiles%\Azure Cosmos DB Emulator`.
8. Otwórz bilet pomocy technicznej w [witrynie Azure portal](https://portal.azure.com) i dołącz plik .etl wraz z krokami repropro.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Odinstalowywanie lokalnego emulatora

1. Zamknij wszystkie otwarte wystąpienia emulatora lokalnego, klikając prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos w zasobniku systemowym, a następnie klikając pozycję Zakończ. Zamykanie wszystkich wystąpień może potrwać około minuty.
2. W polu wyszukiwania systemu Windows wpisz ciąg **Aplikacje i funkcje** i kliknij wynik **Aplikacje i funkcje (Ustawienia systemowe)**.
3. Przewiń listę aplikacji, wybierz pozycję **Emulator usługi Azure Cosmos DB**, kliknij przycisk **Odinstaluj**, potwierdź i ponownie kliknij przycisk **Odinstaluj**.
4. Gdy aplikacja zostanie odinstalowana, przejdź do katalogu `%LOCALAPPDATA%\CosmosDBEmulator` i usuń folder.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisaliśmy sposób użycia lokalnego emulatora do bezpłatnego programowania w środowisku lokalnym. Teraz możesz przejść do następnego samouczka i dowiedzieć się, jak eksportować certyfikaty emulatora TLS/SSL.

> [!div class="nextstepaction"]
> [Eksportowanie certyfikatów emulatora usługi Azure Cosmos](local-emulator-export-ssl-certificates.md)
