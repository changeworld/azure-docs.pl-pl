---
title: Rozwiązywanie problemów z łącznikami Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z łącznikiem w Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143507"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Rozwiązywanie problemów z łącznikami Azure Data Factory

W tym artykule przedstawiono typowe metody rozwiązywania problemów z łącznikami w Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Komunikat o błędzie: Serwer zdalny zwrócił błąd: (403) zabronione

- **Objawy**: Działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Przyczyna**: Jedną z możliwych przyczyn jest to, że główna lub zarządzana tożsamość usługi nie ma uprawnień dostępu do określonego folderu/pliku.

- **Rozpoznawanie**: Udziel odpowiednich uprawnień do wszystkich folderów i podfolderów, które chcesz skopiować. Zapoznaj się z [tym dokumentem](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Komunikat o błędzie: Nie można uzyskać tokenu dostępu przy użyciu nazwy głównej usługi. Błąd ADAL: service_unavailable

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Przyczyna**: Gdy serwer tokenów usług (STS) należący do Azure Active Directory nie jest niedostępny, czyli zbyt zajęty, aby obsługiwać żądania, zwraca błąd HTTP 503. 

- **Rozpoznawanie**: Uruchom ponownie działanie kopiowania po kilku minutach.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Komunikat o błędzie: Konwersja ciągu znaków na unikatowy identyfikator nie powiodła się

- **Objawy**: W przypadku kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do Azure SQL Data Warehouse przy użyciu kopiowania etapowego i bazy, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Przyczyna**: Azure SQL Data Warehouse baza Base nie może przekonwertować pustego ciągu na identyfikator GUID.

- **Rozpoznawanie**: W obszarze ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji**Użyj typu domyślnego**.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Komunikat o błędzie: Oczekiwany typ danych: Liczba dziesiętna (x, x), wartość powodująca problemy

- **Objawy**: W przypadku kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do usługi SQL DW przy użyciu kopiowania etapowego i bazy, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Przyczyna**: Azure SQL Data Warehouse Base nie może wstawić pustego ciągu (wartość null) do kolumny dziesiętnej.

- **Rozpoznawanie**: W obszarze ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji**Użyj typu domyślnego**.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Komunikat o błędzie: Komunikat o wyjątku Java: HdfsBridge:: CreateRecordReader

- **Objawy**: Dane są kopiowane do Azure SQL Data Warehouse przy użyciu bazy danych Base i wystąpił następujący błąd:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Przyczyna**: Możliwą przyczyną jest to, że schemat (całkowita szerokość kolumny) jest zbyt duży (większy niż 1 MB). Sprawdź schemat docelowej tabeli programu SQL DW przez dodanie rozmiaru wszystkich kolumn:

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

- **Rozpoznawanie**: Zmniejsz szerokość kolumny, tak aby była mniejsza niż 1 MB

- Lub użyj metody wstawiania zbiorczego, wyłączając bazę

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Komunikat o błędzie: Warunek określony przy użyciu nagłówków warunkowych HTTP nie jest spełniony

- **Objawy**: Kwerenda SQL służy do ściągania danych z Azure SQL Data Warehouse i wystąpił następujący błąd:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Przyczyna**: Wystąpił problem z Azure SQL Data Warehouseą zapytania o tabelę zewnętrzną w usłudze Azure Storage.

- **Rozpoznawanie**: Uruchom to samo zapytanie w programie SSMS i sprawdź, czy widzisz ten sam wynik. Jeśli tak, Otwórz bilet pomocy technicznej, aby Azure SQL Data Warehouse i podać nazwę serwera i bazy danych SQL DW, aby kontynuować rozwiązywanie problemów.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Komunikat o błędzie: Rozmiar żądania jest zbyt duży

- **Objawy**: Kopiujesz dane do Azure Cosmos DB przy użyciu domyślnego rozmiaru wsadu zapisu i wystąpił błąd *"**rozmiar żądania jest zbyt duży**"* .

- **Przyczyna**: Cosmos DB ograniczenie rozmiaru jednego żądania do 2 MB. Formuła jest, rozmiar żądania = rozmiar pojedynczego dokumentu * Zapisz rozmiar partii. Jeśli rozmiar dokumentu jest duży, zachowanie domyślne spowoduje zbyt duży rozmiar żądania. Można dostosować rozmiar partii zapisu.

- **Rozpoznawanie**: W obszarze ujścia działania kopiowania Zmniejsz wartość w polu "Zapisz rozmiar partii" (wartość domyślna to 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Komunikat o błędzie: Naruszenie ograniczenia indeksu unikatowego

- **Objawy**: Podczas kopiowania danych do Cosmos DB wystąpił następujący błąd:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Przyczyna**: Istnieją dwie możliwe przyczyny:

    - Jeśli używasz funkcji **Wstaw** jako zachowanie zapisu, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty o takim samym identyfikatorze.

    - Jeśli używasz **upsert** jako zachowania zapisu i ustawisz kolejny unikatowy klucz do kontenera, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty z różnymi identyfikatorami, ale te same wartości zdefiniowanego unikatowego klucza.

- **Rozpoznawanie**: 

    - W przypadku cause1 Ustaw **upsert** jako zachowanie zapisu.
    - W przypadku przyczyny 2 Upewnij się, że każdy dokument ma inną wartość dla zdefiniowanego unikatowego klucza.

### <a name="error-message-request-rate-is-large"></a>Komunikat o błędzie: Duża liczba żądań

- **Objawy**: Podczas kopiowania danych do Cosmos DB wystąpił następujący błąd:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Przyczyna**: Używane jednostki żądania są większe niż dostępne RU skonfigurowane w Cosmos DB. Dowiedz się, jak Cosmos DB oblicza RU z tego [miejsca](../cosmos-db/request-units.md#request-unit-considerations).

- **Rozpoznawanie**: Poniżej przedstawiono dwa rozwiązania:

    1. **Zwiększ wartość kontenera ru** do większej w Cosmos DB, co poprawi wydajność działania kopiowania, ale powiąże się z dodatkowym kosztem Cosmos DB. 

    2. Zmniejsz **writeBatchSize** do mniejszej wartości (na przykład 1000), a następnie ustaw **parallelCopies** na mniejszą wartość, taką jak 1, co spowoduje, że wydajność działania kopii będzie gorsza niż bieżąca, ale nie spowoduje to ponoszenia dodatkowych kosztów w Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Brak kolumny w mapowaniu kolumny

- **Objawy**: Podczas importowania schematu dla Cosmos DB na potrzeby mapowania kolumn brakuje niektórych kolumn. 

- **Przyczyna**: ADF zawnioskuje schemat z pierwszych 10 Cosmos DB dokumentów. Jeśli niektóre kolumny/właściwości nie mają wartości w tych dokumentach, nie będą one wykrywane przez ADF, dlatego nie będą wyświetlane.

- **Rozpoznawanie**: Możesz dostosować zapytanie jak poniżej, aby wymusić wyświetlanie kolumny w zestawie wyników z wartością pustą: (Załóżmy, że w pierwszych 10 dokumentach brakuje kolumny "niemożliwe"). Alternatywnie można ręcznie dodać kolumnę do mapowania.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Komunikat o błędzie: GuidRepresentation dla czytnika to CSharpLegacy

- **Objawy**: Podczas kopiowania danych z Cosmos DB MongoAPI/MongoDB z użyciem pola UUID wystąpił następujący błąd:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Przyczyna**: Istnieją dwa sposoby reprezentowania identyfikatora UUID w BSON-UuidStardard i UuidLegacy. Domyślnie UuidLegacy jest używany do odczytywania danych. Zostanie trafiony błąd, jeśli dane UUID w MongoDB jest UuidStandard.

- **Rozpoznawanie**: W parametrach połączenia MongoDB Dodaj opcję "**uuidRepresentation = Standard**". Aby uzyskać więcej informacji, zobacz [MongoDB parametry połączenia](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Komunikat o błędzie: Podano nieprawidłowe poświadczenie SFTP dla typu uwierzytelniania "SshPublicKey"

- **Objawy**: `SshPublicKey` Używasz uwierzytelniania i wystąpił następujący błąd:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Przyczyna**: Istnieją 3 możliwe przyczyny:

    1. Jeśli do utworzenia połączonej usługi SFTP jest używany interfejs użytkownika tworzenia APD, ten błąd oznacza, że klucz prywatny, którego chcesz użyć, ma zły format. Możesz użyć formatu PKCS # 8 prywatnego klucza SSH, podczas gdy funkcja ADF obsługuje tylko tradycyjny format klucza SSH. Bardziej szczegółowe różnice w formacie PKCS # 8 i tradycyjnym formacie klucza to zawartość klucza PKCS # 8 rozpoczyna się od " *-----rozpoczęcia szyfrowania klucza prywatnego-----* ", natomiast tradycyjny format klucza rozpoczyna się od " *-----Rozpocznij-----klucza prywatnego RSA*".
    2. Jeśli używasz Azure Key Vault do przechowywania zawartości klucza prywatnego lub korzystania z programistycznego sposobu tworzenia połączonej usługi SFTP, ten błąd oznacza, że zawartość klucza prywatnego jest niepoprawna, prawdopodobnie nie jest zakodowana w formacie base64.
    3. Nieprawidłowa zawartość poświadczeń lub klucza prywatnego.

- **Rozpoznawanie**: 

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)



