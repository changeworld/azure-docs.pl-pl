---
title: Jak używać magazynu tabel Azure lub interfejsu API tabeli bazy danych rozwiązania Cosmos Azure za pomocą języka Java | Dokumentacja firmy Microsoft
description: Przechowywanie danych strukturalnych w chmurze za pomocą Magazynu tabel Azure, magazyn danych NoSQL.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: 45145189-e67f-4ca6-b15d-43af7bfd3f97
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 4ac25fd9e1d7233546b34da89eb1bcaf37f6f38b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Jak używać magazynu tabel Azure lub interfejsu API tabeli bazy danych rozwiązania Cosmos Azure za pomocą języka Java
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Przegląd
W tym artykule przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługa Azure Table storage i Azure DB rozwiązania Cosmos. Przykłady są napisane w języku Java i użyj [Azure Storage SDK for Java][Azure Storage SDK for Java]. Omówione scenariusze obejmują **tworzenie**, **wyświetlania**, i **usuwanie** tabel, a także **wstawianie**, **zapytań**, **modyfikowanie**, i **usuwanie** jednostek w tabeli. Aby uzyskać więcej informacji o tabelach zobacz [następne kroki](#next-steps) sekcji.

> [!NOTE]
> Zestaw SDK jest dostępny dla deweloperów, którzy korzystają z usługi Azure Storage na urządzeniach z systemem Android. Aby uzyskać więcej informacji, zobacz [zestawu SDK usługi Magazyn Azure dla systemu Android][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta usługi interfejsu API Azure rozwiązania Cosmos DB tabeli
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
W tym przewodniku użyje funkcji magazynu, które aplikacji Java można uruchomić lokalnie lub w kodu działającego w roli sieci web lub roli proces roboczy na platformie Azure.

Aby użyć przykłady w tym artykule, zainstalować Java Development Kit (JDK), a następnie utworzyć konto magazynu Azure w ramach subskrypcji platformy Azure. Po zostało to zrobione, sprawdź, czy platformie programistycznej spełnia minimalne wymagania i zależności, które są wymienione w [Azure Storage SDK for Java] [ Azure Storage SDK for Java] repozytorium w witrynie GitHub. Jeżeli system spełnia te wymagania, należy wykonać instrukcje, aby pobrać i zainstalować biblioteki magazynu Azure dla języka Java w systemie z tego repozytorium. Po wykonaniu tych zadań, można utworzyć aplikacji Java, która używa przykłady w tym artykule.

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurowanie aplikacji na dostęp do magazynu tabel
Dodaj następujące instrukcje import u góry pliku języka Java, której chcesz używać interfejsów API magazynu Azure lub rozwiązania Cosmos DB tabeli interfejsu API Azure do dostępu do tabel:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-an-azure-storage-connection-string"></a>Dodaj parametry połączenia magazynu Azure
Klienta usługi Azure storage używa parametrów połączenia magazynu do przechowywania punktów końcowych i poświadczeń do uzyskiwania dostępu do danych usługi zarządzania. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia magazynu w następującym formacie, przy użyciu nazwy konta magazynu i podstawowy klucz dostępu dla konta magazynu na liście [portalu Azure](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. 

Ten przykład przedstawia, jak można zadeklarować pola statycznego do przechowywania parametrów połączenia:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

## <a name="add-an-azure-cosmos-db-connection-string"></a>Dodaj ciąg połączenia bazy danych Azure rozwiązania Cosmos
Konto bazy danych Azure rozwiązania Cosmos używa parametrów połączenia do przechowywania punkt końcowy tabel i poświadczeń. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia bazy danych Azure rozwiązania Cosmos w następującym formacie, przy użyciu nazwy konta bazy danych Azure rozwiązania Cosmos i podstawowy klucz dostępu dla konta na liście [portalu Azure](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. 

Ten przykład przedstawia, jak można zadeklarować pola statycznego do przechowywania bazy danych Azure rozwiązania Cosmos parametry połączenia:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

W aplikacji działającej w ramach roli na platformie Azure, można przechowywać ten ciąg w pliku konfiguracji usługi *pliku ServiceConfiguration.cscfg*, i można do niego dostęp z wywołaniem do  **RoleEnvironment.getConfigurationSettings** metody. Oto przykład pobierania parametrów połączenia z **ustawienie** elementu o nazwie *StorageConnectionString* w pliku konfiguracji usługi:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Można również przechowywanie parametrów połączenia w pliku config.properties projektu:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

Poniższe przykłady założono użycie jednej z tych metod można pobrać parametry połączenia magazynu.

## <a name="create-a-table"></a>Tworzenie tabeli
A **CloudTableClient** obiektu pozwala pobierać obiekty odwołania do tabel i jednostek. Poniższy kod tworzy **CloudTableClient** obiektu i używa go do tworzenia nowego **CloudTable** obiekt, który reprezentuje tabeli o nazwie "osoby". 

> [!NOTE]
> Istnieją inne sposoby tworzenia **CloudStorageAccount** obiektów; Aby uzyskać więcej informacji, zobacz **CloudStorageAccount** w [odwołania do zestawu SDK klienta magazynu Azure]).
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Wyświetl tabele
Aby uzyskać listę tabel, należy wywołać **CloudTableClient.listTables()** metoda pobierania iterable listę nazw tabel.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Jednostki są mapowane do obiektów języka Java przy użyciu niestandardowej klasy implementacja **TableEntity**. Dla wygody **TableServiceEntity** klasa implementuje **TableEntity** i używa odbicia do mapowania właściwości do metody pobierającej i ustawiającej o nazwie właściwości. Aby dodać jednostkę do tabeli, należy najpierw utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki, która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Operacje tabeli obejmujące jednostki wymagają **TableOperation** obiektu. Ten obiekt definiuje operacji wykonywanych w ramach jednostki, które mogą być wykonywane przez **CloudTable** obiektu. Poniższy kod tworzy nowe wystąpienie klasy **CustomerEntity** klasy z niektóre dane klienta mają być przechowywane. Kod wywołuje dalej **TableOperation.insertOrReplace** utworzyć **TableOperation** obiekt do wstawiania jednostek do tabeli i kojarzy nowe **CustomerEntity** z nim. Na koniec kod wywołuje **wykonania** metoda **CloudTable** obiektu, określając w tabeli "osoby" i nowych **TableOperation**, który następnie wysyła żądanie do usługi magazynu, aby wstawić nową jednostkę klienta do tabeli "osoby", lub Zastąp jednostki, jeśli już istnieje.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek
Możesz wstawić partię jednostek do usługi tabel w jednej operacji zapisu. Poniższy kod tworzy **TableBatchOperation** obiekt, a następnie dodaje Wstaw trzy operacje do niego. Każda operacja insert jest dodawany przez utworzenie nowego obiektu jednostki, jego wartości ustawienia, a następnie wywołując **Wstaw** metoda **TableBatchOperation** obiektu jednostki z nowej operacji insert. Następnie kod wywołuje **wykonania** na **CloudTable** obiektu, określając w tabeli "osoby" i **TableBatchOperation** obiektu, który wysyła do usługi magazynu w ramach pojedynczego żądania partii operacje tabeli.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Należy pamiętać, dotyczące operacji zbiorczych w kilku kwestiach:

* Można wykonać wstawiania do 100, Usuń, scalić, Zastąp, Wstaw lub scalania i Wstaw lub zamienianie operacji w dowolnej kombinacji w pojedynczej partii.
* Operacji zbiorczej może mieć operacji pobierania, jeśli jest jedyną operacją w partii.
* Wszystkie jednostki w jednej operacji zbiorczej muszą mieć ten sam klucz partycji.
* Operacji zbiorczej jest ograniczony do 4MB danych ładunku.

## <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji
Aby odpytać tabeli dla jednostek w partycji, można użyć **TableQuery**. Wywołanie **TableQuery.from** utworzyć zapytanie dla konkretnej tabeli, która zwraca typ określony wynik. Poniższy kod określa filtr jednostek, gdzie "Smith" jest kluczem partycji. **TableQuery.generateFilterCondition** metodę pomocnika, aby utworzyć filtry dla zapytań. Wywołanie **gdzie** na odwołanie zwrócone przez **TableQuery.from** metody, aby zastosować filtr do zapytania. Podczas wykonywania zapytania w wyniku wywołania **wykonania** na **CloudTable** obiektu i zwraca **Iterator** z **CustomerEntity** określony typ wyniku. Następnie można użyć **iteratora** zwracane w dla każdej pętli do pracy z wyników. Ten kod drukowane są pola każdej jednostki w wynikach zapytania do konsoli.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Pobieranie zakresu jednostek w partycji
Jeśli nie chcesz zbadać wszystkich jednostek w partycji, możesz określić zakres przy użyciu operatory porównania w filtrze. Poniższy kod łączy dwa filtry do pobrania wszystkich jednostek w partycji "Smith", gdzie klucz wiersza (imię) rozpoczyna się od litery do "E" alfabetu. Następnie drukuje wyniki zapytania. Jeśli używasz dodane do tabeli w partii jednostek wstawić części tego przewodnika, tylko dwie jednostki są zwracane w tej chwili (Ben i Adam Nowak); Jan Kowalski nie jest włączony.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki
Można napisać zapytanie do pobrania jednej, określonej jednostki. Poniższy kod wywołania **TableOperation.retrieve** z partycją klucza i wiersz klucza parametrów do określenia klienta "Jan Kowalski", zamiast tworzyć **TableQuery** i za pomocą filtrów, aby zrobić to samo. Po wykonaniu operacji pobierania zwraca tylko jedną jednostkę zamiast kolekcji. **GetResultAsType** metody rzutuje wynik, który ma typ elementu docelowego przypisania **CustomerEntity** obiektu. Jeśli ten typ nie jest zgodny z typem określonym w zapytaniu, jest zwracany wyjątek. Jeśli jednostka nie ma dokładnie partycji i klucz wiersza zgodne, jest zwracana wartość null. Określenie kluczy partycji i wiersza w pojedynczym zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z usługi tabel.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Modyfikowanie jednostek
Aby zmodyfikować jednostkę, pobierz ją z usługi tabel zmiany obiektu jednostki i zapisać zmian z powrotem do usługi tabel za pomocą operacji zamiany lub scalania. Poniższy kod zmienia istniejący numer telefonu klienta. Zamiast wywoływać metodę **TableOperation.insert** jak robiliśmy Wstaw ten kod wywołuje **TableOperation.replace**. **CloudTable.execute** metoda wywołuje usługi tabel, a jednostka zostanie zastąpiony, chyba że inna aplikacja uległ zmianie w czasie od pobrany tej aplikacji. W takim przypadku jest zwracany wyjątek, a jednostka musi być pobierane, zmodyfikowane i zapisane ponownie. Ten wzorzec ponawiania optymistycznej współbieżności jest typowe w przypadku rozproszony systemem przechowywania.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki
Zapytanie do tabeli może pobrać kilka właściwości jednostki. Ta technika, zwana projekcją, redukuje przepustowość i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Zapytanie w poniższym kodzie używa **wybierz** metodę, aby zwrócić tylko adresy e-mail jednostek w tabeli. Wyniki są przedstawione jako kolekcja **ciąg** za pomocą **EntityResolver**, która działa konwersji typu na jednostkach zwrócone z serwera. Dowiedz się więcej o projekcji w [tabel Azure: Introducing Upsert i projekcji zapytań][Azure Tables: Introducing Upsert and Query Projection]. Należy pamiętać, że projekcji nie jest obsługiwana w lokalnym emulatorze magazynu, dlatego ten kod zadziała tylko wtedy, gdy przy użyciu konta w usłudze tabel.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Wstawianie lub zastępowanie jednostki
Często chcesz dodać jednostkę do tabeli bez wiedzy o Jeśli już istnieje w tabeli. Operacja wstawiania lub Zastąp pozwala na zapewnienie pojedynczego żądania, które powoduje wstawienie jednostki, jeśli nie istnieje lub Zastąp istniejącą, jeśli tak. Korzystając z poprzednich przykładach, poniższy kod wstawia lub zamienia jednostki dla "Walter Harp". Po utworzeniu nowego obiektu, ten kod wywołuje **TableOperation.insertOrReplace** metody. Ten kod wywołuje **wykonania** na **CloudTable** obiekt z tabeli i Wstaw lub Zastąp operacja tabeli jako parametry. Aby zaktualizować tylko część jednostki **TableOperation.insertOrMerge** metody można użyć zamiast niego. Należy pamiętać, że wstawianie lub zastępowanie nie jest obsługiwane w emulatorze magazynu lokalnego, dlatego ten kod zadziała tylko wtedy, gdy przy użyciu konta w usłudze tabel. Możesz dowiedzieć się więcej o Wstawianie lub zastępowanie i Wstaw lub scalania w tym [tabel Azure: Introducing Upsert i projekcji zapytań][Azure Tables: Introducing Upsert and Query Projection].

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Usuwanie jednostki
Po jej pobraniu można z łatwością usunąć jednostkę. Po pobraniu jednostki wywołać **TableOperation.delete** z jednostką do usunięcia. Następnie wywołaj **wykonania** na **CloudTable** obiektu. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Usuwanie tabeli
Na koniec następującego kodu usuwa tabelę z konta magazynu. Około 40 sekund po usunięciu tabeli, nie można odtworzyć go. 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```
[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do korzystania z usługi Azure tabel w języku Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Magazyn Azure SDK dla języka Java][Azure Storage SDK for Java]
* [Odwołanie do zestawu SDK klienta usługi Azure Storage][odwołania do zestawu SDK klienta magazynu Azure]
* [Interfejs API REST usługi Azure Storage][Azure Storage REST API]
* [Blog zespołu usługi Magazyn Azure][Azure Storage Team Blog]

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[odwołania do zestawu SDK klienta magazynu Azure]: http://azure.github.io/azure-storage-java/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
