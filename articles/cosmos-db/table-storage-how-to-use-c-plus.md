---
title: Jak używać magazynu tabel platformy Azure i bazy danych Azure rozwiązania Cosmos z C++ | Dokumentacja firmy Microsoft
description: Przechowywanie danych strukturalnych w chmurze za pomocą Magazynu tabel Azure, magazyn danych NoSQL.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: f191f308-e4b2-4de9-85cb-551b82b1ea7c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 60c419a45e795d5968b58fbb6b469d06750a0679
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak używać magazynu tabel Azure i interfejsu API Azure rozwiązania Cosmos DB tabeli z C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Przegląd
W tym przewodniku opisano sposób wykonywania typowych scenariuszy przy użyciu usługi Magazyn tabel Azure lub interfejsu API Azure rozwiązania Cosmos bazy danych tabeli. Przykłady są napisane w C++ i użyj [biblioteki klienta usługi Azure Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Omówione scenariusze obejmują **tworzenia i usuwania tabeli** i **Praca z jednostek tabeli**.

> [!NOTE]
> Ten przewodnik jest przeznaczony dla biblioteki klienta magazynu Azure dla języka C++ w wersji 1.0.0 i powyżej. Zalecana wersja jest biblioteka klienta usługi Storage 2.2.0, który jest dostępny za pośrednictwem [NuGet](http://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta usługi interfejsu API Azure rozwiązania Cosmos DB tabeli
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji C++
W tym przewodniku użyje funkcji magazynu, które mogą być uruchamiane w ramach aplikacji C++. Aby to zrobić, należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i Utwórz konto magazynu Azure w ramach subskrypcji platformy Azure.  

Aby zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, można użyć następujących metod:

* **Linux:** postępuj zgodnie z instrukcjami na [biblioteki klienta usługi Azure Storage dla języka C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) strony.  
* **System Windows:** w programie Visual Studio, kliknij przycisk **Narzędzia > Menedżera pakietów NuGet > konsoli Menedżera pakietów**. Wpisz następujące polecenie w [Konsola Menedżera pakietów NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) i naciśnij klawisz Enter.  
  
     Pakiet instalacyjny wastorage

## <a name="configure-access-to-the-table-client-library"></a>Konfigurowanie dostępu do tabeli biblioteki klienta
Dodaj następujące instrukcje na początku pliku C++, których chcesz użyć interfejsów API magazynu Azure dostęp do tabel obejmują:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Klienta usługi Azure Storage lub DB rozwiązania Cosmos klienta używa ciągu połączenia do przechowywania punktów końcowych i poświadczeń dostępu do danych zarządzania usług. Podczas uruchamiania aplikacji klienckiej, musisz podać parametry połączenia magazynu lub parametry połączenia bazy danych Azure rozwiązania Cosmos w odpowiednim formacie.

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu Azure
 Użyj nazwy konta magazynu i klucza dostępu dla konta magazynu na liście [Azure Portal](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. Aby uzyskać informacje dotyczące kont magazynu i klucze dostępu, zobacz [konta usługi Azure Storage](../storage/common/storage-create-storage-account.md). Ten przykład przedstawia, jak można zadeklarować pola statycznego do przechowywania parametrów połączenia magazynu Azure:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Skonfiguruj parametry połączenia bazy danych Azure rozwiązania Cosmos
Użyj nazwy konta bazy danych Azure rozwiązania Cosmos, klucz podstawowy i punkt końcowy na liście [Azure Portal](https://portal.azure.com) dla *nazwa konta*, *klucza podstawowego*, i  *Punkt końcowy* wartości. Ten przykład przedstawia, jak można zadeklarować pola statycznego do przechowywania bazy danych Azure rozwiązania Cosmos parametry połączenia:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


Aby przetestować aplikację w lokalnym komputerze z systemem Windows, można użyć platformy Azure [emulatora magazynu](../storage/common/storage-use-emulator.md) zainstalowane z [zestawu Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu jest narzędziem, która symuluje dostępne na komputerze deweloperskim lokalnej usługi obiektów Blob platformy Azure, kolejki i tabeli. W poniższym przykładzie pokazano, jak można zadeklarować pole statyczne, aby mógł pomieścić parametry połączenia z lokalnym emulatorze magazynu:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulatora magazynu Azure, kliknij przycisk **Start** przycisk lub naciśnij klawisz systemu Windows. Rozpocznij wpisywanie **emulatora magazynu Azure**, a następnie wybierz **emulatora magazynu Azure Microsoft** z listy aplikacji.  

Poniższe przykłady założono użycie jednej z tych dwóch metod można pobrać parametry połączenia magazynu.  

## <a name="retrieve-your-connection-string"></a>Pobranie parametrów połączenia
Można użyć **cloud_storage_account** klasy do reprezentowania informacje o koncie magazynu. Aby uzyskać informacje o koncie magazynu z parametrów połączenia magazynu, można użyć **przeanalizować** metody.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie należy pobrać odwołanie do **cloud_table_client** klasy, ponieważ pozwala uzyskać obiekty odwołań do tabel i jednostek przechowywanych w ramach usługi Magazyn tabel. Poniższy kod tworzy **cloud_table_client** obiektu za pomocą obiektu konta magazynu, możemy pobrać powyżej:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Tworzenie tabeli
A **cloud_table_client** obiektu pozwala pobierać obiekty odwołania do tabel i jednostek. Poniższy kod tworzy **cloud_table_client** obiektu i używa jej do utworzenia nowej tabeli.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę do tabeli, Utwórz nową **table_entity** obiektu i przekaż go do **table_operation::insert_entity**. W poniższym kodzie użyto imienia klienta jako klucza wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, ale przy użyciu różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Aby uzyskać więcej informacji, zobacz [kontrolną wydajności i skalowalności magazynu Microsoft Azure](../storage/common/storage-performance-checklist.md).

Poniższy kod tworzy nowe wystąpienie klasy **table_entity** z niektóre dane klienta mają być przechowywane. Kod wywołuje dalej **table_operation::insert_entity** utworzyć **table_operation** obiekt do wstawiania jednostek do tabeli i kojarzy nową jednostkę tabeli z nim. Na koniec kod wywołuje metody execute w **cloud_table** obiektu. I nowych **table_operation** wysyła żądanie do usługi tabel, aby wstawić nową jednostkę klienta do tabeli "osoby".  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek
Możesz wstawić partię jednostek do usługi tabel w jednej operacji zapisu. Poniższy kod tworzy **table_batch_operation** obiekt, a następnie dodanie Wstaw trzy operacje do niego. Każda operacja wstawiania jest dodawany przez utworzenie nowego obiektu jednostki, ustawienie wartości i następnie podczas wywoływania metody insert na **table_batch_operation** obiektu jednostki z nowej operacji insert. Następnie **cloud_table.execute** jest wywoływana w celu wykonania operacji.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Należy pamiętać, dotyczące operacji zbiorczych w kilku kwestiach:  

* Do 100 insert delete scalania Zamień, operacje wstawiania lub scalania i wstawianie lub zastępowanie można wykonywać w dowolnej kombinacji w pojedynczej partii.  
* Operacji zbiorczej może mieć operacji pobierania, jeśli jest jedyną operacją w partii.  
* Wszystkie jednostki w jednej operacji zbiorczej muszą mieć ten sam klucz partycji.  
* Operacji zbiorczej jest ograniczony do 4 MB danych ładunku.  

## <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji
Aby sprawdzić tabelę dla wszystkich jednostek w partycji, użyj **table_query** obiektu. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.  

> [!NOTE]
> Te metody są obecnie nieobsługiwane dla języka C++ w usłudze Azure DB rozwiązania Cosmos.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

Zapytania w tym przykładzie powoduje przeniesienie wszystkich jednostek spełniających kryteria filtrowania. Jeśli masz dużych tabel i należy pobrać jednostek tabeli często, zalecamy, aby dane przechowywane w obiektach blob magazynu Azure zamiast tego.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Pobieranie zakresu jednostek w partycji
Jeśli nie chcesz wykonywać zapytania dla wszystkich jednostek w partycji, możesz określić zakres, łącząc filtr klucza partycji z filtrem klucza wiersza. Poniższy przykład kodu wykorzystuje dwa filtry do pobrania wszystkich jednostek w partycji „Smith”, w których klucz wiersza (imię) rozpoczyna się od litery alfabetu wcześniejszej niż „E”, a następnie drukuje wyniki zapytania.  

> [!NOTE]
> Te metody są obecnie nieobsługiwane dla języka C++ w usłudze Azure DB rozwiązania Cosmos.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki
Można napisać zapytanie do pobrania jednej, określonej jednostki. Poniższy kod używa **table_operation::retrieve_entity** do określenia klienta "Jan Kowalski". Ta metoda zwraca tylko jedną jednostkę zamiast kolekcji i jest zwracana wartość **table_result**. Określenie kluczy partycji i wiersza w pojedynczym zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z usługi tabel.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

## <a name="replace-an-entity"></a>Zastępowanie jednostki
Jednostki, pobierz ją z usługi tabel, zmodyfikuj obiekt jednostki, a następnie zapisz zmiany usługi tabel. Poniższy kod zmienia istniejący klient telefonu i adresu e-mail. Zamiast wywoływać metodę **table_operation::insert_entity**, ten kod używa **table_operation::replace_entity**. Dzięki temu jednostka będzie całkowicie zastąpiona na serwerze, chyba że jednostka na serwerze zmieniła się od czasu jej pobrania. W takim przypadku operacja nie powiedzie się. Ten błąd uniemożliwia aplikacji nieodwracalne nadpisanie zmiany dokonanej pomiędzy pobraniem i zaktualizowaniem przez inny składnik aplikacji. Odpowiednie obsługi tego błędu jest ponowne pobranie jednostki, wprowadź żądane zmiany (jeśli jest nadal ważny) i następnie ponowne przeprowadzenie **table_operation::replace_entity** operacji. W następnej sekcji opisano sposób zastąpienia tego zachowania.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

## <a name="insert-or-replace-an-entity"></a>Wstawianie lub zastępowanie jednostki
**table_operation::replace_entity** operacji zakończy się niepowodzeniem, jeśli jednostka została zmieniona, ponieważ został on pobrany z serwera. Ponadto należy pobrać jednostki z serwera najpierw aby **table_operation::replace_entity** powiódł się. Czasami jednak nie wiesz, czy jednostka istnieje na serwerze i czy obecne wartości przechowywane w nim nie mają znaczenia — aktualizacja powinna nadpisać je wszystkie. W tym celu należy użyć **table_operation::insert_or_replace_entity** operacji. Ta operacja wstawi jednostkę, jeśli jednostka nie istnieje, lub zastąpi ją, jeśli już istnieje — niezależnie od czasu ostatniej aktualizacji. W poniższym przykładzie kodu jednostka klienta dla Jan Kowalski nadal jest pobierana, ale następnie jest zapisywana na serwerze za pomocą **table_operation::insert_or_replace_entity**. Wszelkie aktualizacje wprowadzone w jednostce między pobierania i operacji aktualizacji zostaną zastąpione.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert-or-replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert-or-replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki
Zapytanie do tabeli może pobrać kilka właściwości jednostki. Zapytanie w poniższym kodzie używa **table_query::set_select_columns** metodę, aby zwrócić tylko adresy e-mail jednostek w tabeli.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Badania kilka właściwości jednostki jest operacją bardziej efektywne niż pobierania wszystkich właściwości.
> 
> 

## <a name="delete-an-entity"></a>Usuwanie jednostki
Po jej pobraniu można z łatwością usunąć jednostkę. Po pobraniu jednostki wywołać **table_operation::delete_entity** z jednostką do usunięcia. Następnie wywołaj **cloud_table.execute** metody. Poniższy kod pobiera i usunięcie jednostki z kluczem partycji "Smith" i kluczem wiersza "Jeff".  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

## <a name="delete-a-table"></a>Usuwanie tabeli
Poniższy przykład kodu usuwa tabelę z konta magazynu. Tabeli, która została usunięta, nie będzie można ponownie utworzyć przez dany okres czasu po jej usunięciu.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
    {
        std::cout << "Table deleted!";
    }
    else
    {
        std::cout << "Table didn't exist";
    }
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
* Błędy w Visual Studio 2017 Community Edition kompilacji

  Jeśli projekt pobiera błędy kompilacji z powodu Dołącz pliki storage_account.h i table.h, Usuń **/ ograniczająca-** przełącznika kompilatora. 
  - W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **właściwości**.
  - W **strony właściwości** okna dialogowego rozwiń **właściwości konfiguracji**, rozwiń węzeł **C/C++**i wybierz **języka**.
  - Ustaw **tryb zgodności** do **nr**.
   
## <a name="next-steps"></a>Kolejne kroki
Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure Storage i interfejsu API tabeli w usłudze Azure DB rozwiązania Cosmos: 

* [Wprowadzenie do tabeli interfejsu API](table-introduction.md)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Lista zasobów magazynu Azure w języku C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Biblioteka klienta usługi Storage for C++ — dokumentacja](http://azure.github.io/azure-storage-cpp)
* [Dokumentację magazynu platformy Azure](https://azure.microsoft.com/documentation/services/storage/)
