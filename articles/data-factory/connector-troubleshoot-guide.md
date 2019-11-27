---
title: Rozwiązywanie problemów z łącznikami Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z łącznikiem w Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533146"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Rozwiązywanie problemów z łącznikami Azure Data Factory

W tym artykule przedstawiono typowe metody rozwiązywania problemów z łącznikami w Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Komunikat o błędzie: serwer zdalny zwrócił błąd: (403) zabroniony

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Przyczyna**: jedną z możliwych przyczyn jest to, że główna lub zarządzana tożsamość usługi nie ma uprawnień dostępu do określonego folderu/pliku.

- **Rozwiązanie**: Przyznaj odpowiednie uprawnienia do wszystkich folderów i podfolderów, które chcesz skopiować. Zapoznaj się z [tym dokumentem](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Komunikat o błędzie: nie można uzyskać tokenu dostępu przy użyciu nazwy głównej usługi. Błąd ADAL: service_unavailable

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Przyczyna**: gdy serwer tokenów usług (STS) należący do Azure Active Directory nie jest niedostępny, czyli zbyt zajęty, aby obsługiwać żądania, zwraca błąd HTTP 503. 

- **Rozwiązanie**: ponownie uruchom działanie kopiowania po kilku minutach.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Komunikat o błędzie: rozmiar żądania jest zbyt duży

- **Objawy**: dane są kopiowane do Azure Cosmos dB przy użyciu domyślnego rozmiaru wsadu zapisu i wystąpił błąd *"**rozmiar żądania jest zbyt duży**"* .

- **Przyczyna**: Cosmos DB ogranicza jeden rozmiar pojedynczego żądania do 2 MB. Formuła jest, rozmiar żądania = rozmiar pojedynczego dokumentu * Zapisz rozmiar partii. Jeśli rozmiar dokumentu jest duży, zachowanie domyślne spowoduje zbyt duży rozmiar żądania. Można dostosować rozmiar partii zapisu.

- **Rozwiązanie**: w ujścia działania kopiowania Zmniejsz wartość w polu "Zapisz rozmiar partii" (wartość domyślna to 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Komunikat o błędzie: naruszenie ograniczenia indeksu unikatowego

- **Objawy**: podczas kopiowania danych do Cosmos DB można napotkać następujący błąd:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Przyczyna**: Istnieją dwie możliwe przyczyny:

    - Jeśli używasz funkcji **Wstaw** jako zachowanie zapisu, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty o takim samym identyfikatorze.

    - Jeśli używasz **upsert** jako zachowania zapisu i ustawisz kolejny unikatowy klucz do kontenera, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty z różnymi identyfikatorami, ale te same wartości zdefiniowanego unikatowego klucza.

- **Rozwiązanie**: 

    - W przypadku cause1 Ustaw **upsert** jako zachowanie zapisu.
    - W przypadku przyczyny 2 Upewnij się, że każdy dokument ma inną wartość dla zdefiniowanego unikatowego klucza.

### <a name="error-message-request-rate-is-large"></a>Komunikat o błędzie: częstotliwość żądań jest duża

- **Objawy**: podczas kopiowania danych do Cosmos DB można napotkać następujący błąd:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Przyczyna**: użyte jednostki żądania są większe niż dostępne ru skonfigurowane w Cosmos DB. Dowiedz się, jak Cosmos DB oblicza RU z tego [miejsca](../cosmos-db/request-units.md#request-unit-considerations).

- **Rozwiązanie**: Oto dwa rozwiązania:

    1. **Zwiększ wartość kontenera ru** do większej w Cosmos DB, co poprawi wydajność działania kopiowania, ale powiąże się z dodatkowym kosztem Cosmos DB. 

    2. Zmniejsz **writeBatchSize** do mniejszej wartości (na przykład 1000), a następnie ustaw **parallelCopies** na mniejszą wartość, taką jak 1, co spowoduje, że wydajność działania kopii będzie gorsza niż bieżąca, ale nie spowoduje to ponoszenia dodatkowych kosztów w Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Brak kolumny w mapowaniu kolumny

- **Objawy**: podczas importowania schematu dla Cosmos DB na potrzeby mapowania kolumn brakuje niektórych kolumn. 

- **Przyczyna**: ADF zawnioskuje schemat z pierwszych 10 Cosmos DB dokumentów. Jeśli niektóre kolumny/właściwości nie mają wartości w tych dokumentach, nie będą one wykrywane przez ADF, dlatego nie będą wyświetlane.

- **Rozwiązanie**: można dostosować zapytanie jak poniżej, aby wymusić wyświetlenie kolumny w zestawie wyników z wartością pustą: (Załóżmy, że w pierwszych 10 dokumentach brakuje kolumny "niemożliwe"). Alternatywnie można ręcznie dodać kolumnę do mapowania.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Komunikat o błędzie: GuidRepresentation dla czytnika to CSharpLegacy

- **Objawy**: podczas kopiowania danych z Cosmos DB MongoAPI/MongoDB z użyciem pola UUID wystąpił następujący błąd:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Przyczyna**: Istnieją dwa sposoby reprezentowania identyfikatora UUID w BSON-UuidStardard i UuidLegacy. Domyślnie UuidLegacy jest używany do odczytywania danych. Zostanie trafiony błąd, jeśli dane UUID w MongoDB jest UuidStandard.

- **Rozwiązanie**: w parametrach połączenia MongoDB Dodaj opcję "**uuidRepresentation = Standard**". Aby uzyskać więcej informacji, zobacz [MongoDB parametry połączenia](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Komunikat o błędzie: podano nieprawidłowe poświadczenie SFTP dla typu uwierzytelniania "SshPublicKey"

- **Objawy**: używasz uwierzytelniania `SshPublicKey` i wystąpił następujący błąd:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Przyczyna**: Istnieją trzy możliwe przyczyny:

    1. Jeśli do utworzenia połączonej usługi SFTP jest używany interfejs użytkownika tworzenia APD, ten błąd oznacza, że klucz prywatny, którego chcesz użyć, ma zły format. Możesz użyć formatu PKCS # 8 prywatnego klucza SSH, podczas gdy funkcja ADF obsługuje tylko tradycyjny format klucza SSH. Bardziej szczegółowe różnice w formacie PKCS # 8 i tradycyjnym formacie klucza to zawartość klucza PKCS # 8 rozpoczyna się od " *-----rozpoczęcia szyfrowania klucza prywatnego-----* ", natomiast tradycyjny format klucza rozpoczyna się od " *-----Rozpocznij-----klucza prywatnego RSA*".
    2. Jeśli używasz Azure Key Vault do przechowywania zawartości klucza prywatnego lub korzystania z programistycznego sposobu tworzenia połączonej usługi SFTP, ten błąd oznacza, że zawartość klucza prywatnego jest niepoprawna, prawdopodobnie nie jest zakodowana w formacie base64.
    3. Nieprawidłowa zawartość poświadczeń lub klucza prywatnego.

- **Rozwiązanie**: 

    - W celu uzyskania przyczyny #1 Uruchom następujące polecenia, aby skonwertować klucz na tradycyjny format, a następnie użyj go w interfejsie użytkownika tworzenia ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - W celu wygenerowania takiego ciągu przez #2 Klient może użyć poniżej 2 sposobów:
    - Używanie narzędzia konwersji Base64 innej firmy: wklej całą zawartość klucza prywatnego do narzędzi, takich jak [kodowanie base64 i dekodowanie](https://www.base64encode.org/), zakoduj ją w formacie Base64, a następnie wklej ten ciąg do magazynu kluczy lub Użyj tej wartości w celu programistycznego tworzenia połączonej usługi SFTP.
    - Korzystanie C# z kodu:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - W celu uzyskania przyczyny #3 należy dokładnie sprawdzić, czy plik klucza lub hasło są poprawne przy użyciu innych narzędzi do sprawdzenia, czy można go użyć w celu poprawnego dostępu do serwera SFTP.
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Azure SQL Data Warehouse \ Azure SQL Database \ SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kod błędu: SqlFailedToConnect

- **Komunikat**: `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "SqlException", baza danych SQL zgłasza błąd wskazujący, że niektóre konkretne operacje nie powiodły się.

- **Zalecenie**: Aby uzyskać więcej informacji, Wyszukaj według kodu błędu SQL w tym dokumencie odwołania: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną platformy Azure.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "klient z adresem IP"... " nie ma dostępu do serwera "i próbujesz nawiązać połączenie z usługą Azure SQL Database, zwykle jest to spowodowane problemem z zaporą usługi Azure SQL Database.

- **Zalecenie**: w konfiguracji zapory SQL Server platformy Azure Włącz opcję "Zezwalaj na dostęp do tego serwera dla usług i zasobów platformy Azure". Doc Reference: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Kod błędu: SqlOperationFailed

- **Komunikat**: `A database operation failed. Please search error to get more details.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "SqlException", baza danych SQL zgłasza błąd wskazujący, że niektóre konkretne operacje nie powiodły się.

- **Zalecenie**: Aby uzyskać więcej informacji, Wyszukaj według kodu błędu SQL w tym dokumencie odwołania: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną platformy Azure.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera "PdwManagedToNativeInteropException", zazwyczaj jest spowodowany niezgodnością rozmiarów kolumn źródłowych i ujścia.

- **Zalecenie**: Sprawdź rozmiar kolumn źródłowych i ujścia. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną platformy Azure.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "InvalidOperationException", zazwyczaj jest to spowodowane przez nieprawidłowe dane wejściowe.

- **Zalecenie**: aby ustalić, który wiersz napotyka problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu przeprowadzenia dalszej analizy. Doc Reference: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Kod błędu: SqlUnauthorizedAccess

- **Komunikat**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Przyczyna**: poświadczenie jest niepoprawne lub konto logowania nie może uzyskać dostępu do bazy danych SQL.

- **Zalecenie**: Sprawdź, czy konto logowania ma wystarczające uprawnienia dostępu do bazy danych SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kod błędu: SqlOpenConnectionTimeout

- **Komunikat**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Przyczyna**: może to być błąd przejściowy bazy danych SQL.

- **Zalecenie**: spróbuj ponownie zaktualizować parametry połączenia połączonej usługi z większą wartością limitu czasu połączenia.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kod błędu: SqlAutoCreateTableTypeMapFailed

- **Komunikat**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **Przyczyna**: tabela autotworzenia nie może spełnić wymagań źródłowych.

- **Zalecenie**: zaktualizuj typ kolumny w polu "mapowania" lub ręcznie Utwórz tabelę ujścia na serwerze docelowym.


### <a name="error-code--sqldatatypenotsupported"></a>Kod błędu: SqlDataTypeNotSupported

- **Komunikat**: `A database operation failed. Please check the SQL errors.`

- **Przyczyna**: Jeśli problem występuje w źródle SQL, a błąd jest związany z przepełnieniem SqlDateTime, wartość danych jest poza zakresem typów logiki (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Zalecenie**: Przeprowadź rzutowanie typu na ciąg w źródłowym zapytaniu SQL lub w mapowaniu kolumny działania kopiowania Zmień typ kolumny na "String".

- **Przyczyna**: Jeśli problem występuje w usłudze SQL sink i błąd jest związany z przepełnieniem elementu SqlDateTime, wartość danych jest poza dozwolonym zakresem w tabeli ujścia.

- **Zalecenie**: zaktualizuj odpowiedni typ kolumny do typu "datetime2" w tabeli sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kod błędu: SqlInvalidDbStoredProcedure

- **Komunikat**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Przyczyna**: określona procedura składowana jest nieprawidłowa. Może to być spowodowane tym, że procedura składowana nie zwraca żadnych danych.

- **Zalecenie**: Sprawdź poprawność procedury składowanej przez narzędzia SQL. Upewnij się, że procedura składowana może zwracać dane.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kod błędu: SqlInvalidDbQueryString

- **Komunikat**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Przyczyna**: określone zapytanie SQL jest nieprawidłowe. Przyczyną może być to, że zapytanie nie zwraca żadnych danych

- **Zalecenie**: Sprawdź poprawność zapytania SQL przez narzędzia SQL. Upewnij się, że zapytanie może zwracać dane.


### <a name="error-code--sqlinvalidcolumnname"></a>Kod błędu: SqlInvalidColumnName

- **Komunikat**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Przyczyna**: nie można odnaleźć kolumny. Nieprawidłowa konfiguracja.

- **Zalecenie**: Upewnij się, że kolumna w zapytaniach "Structure" w elemencie DataSet i "Mappings" w działaniu.


### <a name="error-code--sqlbatchwritetimeout"></a>Kod błędu: SqlBatchWriteTimeout

- **Komunikat**: `Timeout in SQL write opertaion.`

- **Przyczyna**: może to być błąd przejściowy bazy danych SQL.

- **Zalecenie**: Jeśli problem Odtwórz, skontaktuj się z pomocą techniczną Azure SQL.


### <a name="error-code--sqlbatchwriterollbackfailed"></a>Kod błędu: SqlBatchWriteRollbackFailed

- **Komunikat**: `Timeout in SQL write operation and rollback also fail.`

- **Przyczyna**: może to być błąd przejściowy bazy danych SQL.

- **Zalecenie**: spróbuj ponownie zaktualizować parametry połączenia połączonej usługi z większą wartością limitu czasu połączenia.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kod błędu: SqlBulkCopyInvalidColumnLength

- **Komunikat**: `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **Przyczyna**: Kopiowanie zbiorcze SQL nie powiodło się, ponieważ odebrano nieprawidłową długość kolumny od klienta bcp.

- **Zalecenie**: aby ustalić, który wiersz napotyka problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu przeprowadzenia dalszej analizy. Doc Reference: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Kod błędu: SqlConnectionIsClosed

- **Komunikat**: `The connection is closed by SQL database.`

- **Przyczyna**: połączenie SQL jest zamknięte przez bazę danych SQL, gdy duże współbieżne uruchomienie i przerwanie połączenia serwera.

- **Zalecenie**: serwer zdalny zamknie połączenie SQL. Spróbuj ponownie. Jeśli problem Odtwórz, skontaktuj się z pomocą techniczną Azure SQL.

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Komunikat o błędzie: konwersja nie powiodła się podczas konwertowania ciągu znaków na unikatowy identyfikator

- **Objawy**: w przypadku kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do Azure SQL Data Warehouse przy użyciu kopiowania etapowego i bazy, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Przyczyna**: Azure SQL Data Warehouse baza nie może przekonwertować pustego ciągu na identyfikator GUID.

- **Rozwiązanie**: w ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji**Użyj typu domyślnego**.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Komunikat o błędzie: Oczekiwano typu danych: DECIMAL (x, x), wartość powodująca problemy

- **Objawy**: podczas kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do usługi SQL DW przy użyciu kopiowania etapowego i wielopodstawa, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Przyczyna**: Azure SQL Data Warehouse Base nie może wstawić pustego ciągu (wartość null) do kolumny dziesiętnej.

- **Rozwiązanie**: w ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji**Użyj typu domyślnego**.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Komunikat o błędzie: komunikat o wyjątku Java: HdfsBridge:: CreateRecordReader

- **Objawy**: skopiujesz dane do Azure SQL Data Warehouse przy użyciu bazy danych Base i wystąpił następujący błąd:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Przyczyna**: możliwe przyczyny jest to, że schemat (całkowita szerokość kolumny) jest zbyt duży (większy niż 1 MB). Sprawdź schemat docelowej tabeli programu SQL DW przez dodanie rozmiaru wszystkich kolumn:

    - Int-> 4 bajty
    - Bigint-> 8 bajtów
    - Varchar (n), char (n), Binary (n), varbinary (n)-> n bajtów
    - Nvarchar (n), nchar (n)-> n * 2 bajty
    - Data — > 6 bajtów
    - DateTime/(2), smalldatetime-> 16 bajtów
    - DateTimeOffset — > 20 bajtów
    - Liczba dziesiętna — > 19 bajtów
    - Float-> 8 bajtów
    - Money-> 8 bajtów
    - Smallmoney — > 4 bajty
    - Real-> 4 bajty
    - Smallint — > 2 bajty
    - Czas — > 12 bajtów
    - Tinyint — > 1 bajt

- **Rozdzielczość**: Zmniejsz szerokość kolumny, aby była mniejsza niż 1 MB

- Lub użyj metody wstawiania zbiorczego, wyłączając bazę

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Komunikat o błędzie: warunek określony przy użyciu nagłówków warunkowych HTTP nie jest spełniony

- **Objawy**: używasz zapytania SQL do ściągania danych z Azure SQL Data Warehouse i wystąpił następujący błąd:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Przyczyna**Azure SQL Data Warehouse: Wystąpił błąd podczas wysyłania zapytania do tabeli zewnętrznej w usłudze Azure Storage.

- **Rozwiązanie**: Uruchom to samo zapytanie w programie SSMS i sprawdź, czy widzisz ten sam wynik. Jeśli tak, otwórz bilet pomocy technicznej dotyczący usługi Azure SQL Data Warehouse i podaj nazwę serwera i bazy danych SQL DW, aby kontynuować rozwiązywanie problemów.
            

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Kod błędu: AzureBlobOperationFailed

- **Komunikat**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Przyczyna**: problem z trafieniem magazynu obiektów BLOB.

- **Zalecenie**: Sprawdź błąd, aby uzyskać szczegółowe informacje. Zapoznaj się z dokumentem pomocy obiektu BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Skontaktuj się z zespołem ds. magazynu, jeśli potrzebujesz pomocy.



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Kod błędu: AdlsGen2OperationFailed

- **Komunikat**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Przyczyna**: ADLS Gen2 zgłasza błąd wskazujący, że operacja nie powiodła się.

- **Zalecenie**: Sprawdź szczegółowy komunikat o błędzie wygenerowany przez ADLS Gen2. Jeśli jest to spowodowane błędem przejściowym, spróbuj ponownie. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "zabronione", nazwa główna usługi lub tożsamość zarządzana może nie mieć wystarczających uprawnień, aby uzyskać dostęp do ADLS Gen2.

- **Zalecenie**: Zapoznaj się z dokumentem pomocy: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "InternalServerError", błąd jest zwracany przez ADLS Gen2.

- **Zalecenie**: może to być spowodowane błędem przejściowym, spróbuj ponownie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
            
