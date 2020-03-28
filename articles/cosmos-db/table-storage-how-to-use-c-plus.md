---
title: Korzystanie z usługi Azure Table Storage i interfejsu API tabel usługi Azure Cosmos DB w języku C++
description: Przechowywanie danych strukturalnych w chmurze za pomocą usługi Azure Table Storage lub interfejsu Table API usługi Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: e6d61e329ba91f53b11ace4d258b35950e188dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76771218"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak korzystać z usługi Azure Table Storage i interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

W tym przewodniku przedstawiono typowe scenariusze przy użyciu usługi Azure Table storage service lub interfejsu API tabeli usługi Azure Cosmos DB. Przykłady są napisane w języku C++ i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). W tym artykule omówiono następujące scenariusze:

* Tworzenie i usuwanie tabeli
* Praca z encjami tabeli

> [!NOTE]
> Ten przewodnik jest przeznaczony do użycia z biblioteką klienta usługi Azure Storage dla języka C++ w wersji 1.0.0 lub wyższej. Zalecana wersja to Storage Client Library 2.2.0, która jest dostępna za pomocą [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Tworzenie kont

### <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++

W tym przewodniku należy użyć funkcji magazynu z aplikacji C++. W tym celu należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++.

Aby zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, należy użyć następujących metod:

* **Linux:** Postępuj zgodnie z instrukcjami podanymi w [bibliotece klienta usługi Azure Storage dla języka C++ README: Wprowadzenie na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stronie Linux.
* **Windows:** W systemie Windows użyj [vcpkg](https://github.com/microsoft/vcpkg) jako menedżera zależności. Postępuj zgodnie z [szybkim startem,](https://github.com/microsoft/vcpkg#quick-start) aby zainicjować vcpkg. Następnie użyj następującego polecenia, aby zainstalować bibliotekę:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Można znaleźć przewodnik, jak zbudować kod źródłowy i eksportować do Nuget w pliku [README.](https://github.com/Azure/azure-storage-cpp#download--install)

### <a name="configure-access-to-the-table-client-library"></a>Konfigurowanie dostępu do biblioteki klienta usługi Table Storage

Aby uzyskać dostęp do tabel za pomocą interfejsów API magazynu platformy Azure, dodaj następujące `include` instrukcje do górnej części pliku Języka C++:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

W kliencie usługi Azure Storage lub Cosmos DB punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia. Po uruchomieniu aplikacji klienckiej, należy podać parametry połączenia magazynu lub parametry połączenia usługi Azure Cosmos DB w odpowiednim formacie.

### <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage

W tym przykładzie pokazano, jak zadeklarować statyczne pole do przechowywania ciągu połączenia usługi Azure Storage:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Użyj nazwy konta Magazynu `<your_storage_account>`dla . Aby <your_storage_account_key>, użyj klucza dostępu dla konta magazynu wymienionego w [witrynie Azure portal](https://portal.azure.com). Aby uzyskać informacje o kontach magazynu i kluczach dostępu, zobacz [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Cosmos DB

W tym przykładzie pokazano, jak zadeklarować statyczne pole do przechowywania ciągu połączenia usługi Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Użyj nazwy konta usługi Azure Cosmos `<your_cosmos_db_account>`DB dla . Wprowadź klucz podstawowy `<your_cosmos_db_account_key>`dla pliku . Wprowadź punkt końcowy wymieniony w `<your_cosmos_db_endpoint>` [witrynie Azure portal](https://portal.azure.com) dla .

Aby przetestować aplikację na komputerze lokalnym z systemem Windows, możesz użyć emulatora magazynu platformy Azure, instalowanego w ramach [zestawu Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu to narzędzie umożliwiające symulowanie usług Azure Blob, Queue i Table Storage na lokalnej maszynie deweloperskiej. W poniższym przykładzie pokazano, jak zadeklarować pole statyczne do przechowywania ciągu połączenia do emulatora magazynu lokalnego:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulator magazynu platformy Azure, na pulpicie systemu Windows wybierz przycisk **Start** lub klawisz Windows. Wprowadź i uruchom *emulator magazynu microsoft azure*. Aby uzyskać więcej informacji, zobacz [Używanie emulatora magazynu platformy Azure do tworzenia i testowania.](../storage/common/storage-use-emulator.md)

### <a name="retrieve-your-connection-string"></a>Pobieranie parametrów połączenia

Klasy można `cloud_storage_account` użyć do reprezentowania informacji o koncie magazynu. Aby pobrać informacje o koncie magazynu z `parse` ciągu połączenia magazynu, należy użyć tej metody.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie uzyskaj odwołanie do `cloud_table_client` klasy. Ta klasa umożliwia uzyskanie obiektów referencyjnych dla tabel i jednostek przechowywanych w usłudze magazynu tabel. Poniższy kod `cloud_table_client` tworzy obiekt przy użyciu obiektu konta magazynu, który został pobrany wcześniej:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Tworzenie i dodawanie encji do tabeli

### <a name="create-a-table"></a>Tworzenie tabeli

Obiekt `cloud_table_client` umożliwia uzyskanie obiektów odniesienia dla tabel i elementów. Poniższy kod `cloud_table_client` tworzy obiekt i używa go do utworzenia nowej tabeli.

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

### <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać encję do tabeli, utwórz `table_entity` `table_operation::insert_entity`nowy obiekt i przekaż go do pliku . W poniższym kodzie imię klienta jest używane jako klucz wiersza, a nazwisko jako klucz partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji można zbadać szybciej niż jednostki z różnymi kluczami partycji. Korzystanie z różnych kluczy partycji pozwala na większą skalowalność operacji równoległych. Aby uzyskać więcej informacji, zobacz temat [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../storage/common/storage-performance-checklist.md).

Poniższy kod tworzy nowe `table_entity` wystąpienie z niektórych danych klienta do przechowywania. Kod następnie `table_operation::insert_entity` wywołuje utworzenie `table_operation` obiektu, aby wstawić jednostkę do tabeli i kojarzy z nią nową encję tabeli. Na koniec kod wywołuje `execute` metodę `cloud_table` na obiekcie. Nowy `table_operation` wysyła żądanie do usługi Tabela, aby wstawić nową jednostkę `people` klienta do tabeli.  

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

### <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek

Możesz wstawić partię jednostek do usługi tabel w ramach jednej operacji zapisu. Poniższy kod `table_batch_operation` tworzy obiekt, a następnie dodaje trzy operacje wstawania do niego. Każda operacja wstawiania jest dodawana przez utworzenie nowego obiektu `insert` encji, ustawienie jego wartości, a następnie wywołanie metody na `table_batch_operation` obiekcie, aby skojarzyć encję z nową operacją wstawiania. Następnie wywołano `cloud_table.execute` wywołanie kodu, aby uruchomić operację.  

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

Kilka uwag dotyczących operacji zbiorczych:

* `insert`Można wykonać maksymalnie 100 `delete` `merge`, `replace` `insert-or-merge`, `insert-or-replace` , i operacji w dowolnej kombinacji w jednej partii.  
* Operacja wsadowa może mieć operację pobierania, jeśli jest to jedyna operacja w partii.  
* Wszystkie jednostki w jednej operacji zbiorczej muszą mieć ten sam klucz partycji.  
* Maksymalny rozmiar ładunku danych operacji zbiorczej to 4 MB.  

## <a name="query-and-modify-entities"></a>Kwerenda i modyfikowanie encji

### <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji

Aby zbadać tabelę dla wszystkich jednostek `table_query` na partycji, użyj obiektu. Poniższy przykład kodu określa filtr dla `Smith` jednostek, gdzie jest klucz partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.  

> [!NOTE]
> Te metody języka C++ nie są obecnie obsługiwane w przypadku usługi Azure Cosmos DB.

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

Kwerenda w tym przykładzie zwraca wszystkie jednostki, które odpowiadają kryteriom filtru. Jeśli masz duże tabele i często pobierasz jednostki z tabel, zalecamy zamiast tego przechowywanie danych w obiektach blob w usłudze Azure Storage.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Pobieranie zakresu jednostek w partycji

Jeśli nie chcesz wysyłać zapytań o wszystkie jednostki na partycji, możesz określić zakres. Połącz filtr klucza partycji z filtrem klucza wiersza. Poniższy przykład kodu używa dwóch filtrów, aby `Smith` uzyskać wszystkie jednostki w partycji, gdzie `E` klucz wiersza (imię) rozpoczyna się literą wcześniej niż w alfabecie, a następnie drukuje wyniki kwerendy.  

> [!NOTE]
> Te metody języka C++ nie są obecnie obsługiwane w przypadku usługi Azure Cosmos DB.

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

### <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki

Można napisać zapytanie do pobrania jednej, określonej jednostki. Poniższy kod `table_operation::retrieve_entity` służy do `Jeff Smith`określenia odbiorcy . Ta metoda zwraca tylko jedną jednostkę, a nie `table_result`kolekcję, a zwrócona wartość znajduje się w . Określenie kluczy partycji i wiersza w pojedynczym zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z usługi tabel.  

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

### <a name="replace-an-entity"></a>Zastępowanie jednostki

Aby zastąpić jednostkę, pobierz ją z usługi Table Storage, zmodyfikuj obiekt jednostki, a następnie zapisz zmiany w usłudze Table Storage. Poniższy kod zmienia numer telefonu i adres e-mail istniejącego klienta. Zamiast wywoływania, `table_operation::insert_entity`ten kod `table_operation::replace_entity`używa . Takie podejście powoduje, że jednostka ma być w pełni zastąpiona na serwerze, chyba że jednostka na serwerze uległa zmianie od czasu jej pobrania. Jeśli została zmieniona, operacja zakończy się niepowodzeniem. Ten błąd uniemożliwia aplikacji zastąpienie zmiany wprowadzonej między pobieraniem i aktualizacją przez inny składnik. Prawidłową obsługą tego błędu jest ponowne pobranie jednostki, wprowadzenie zmian, jeśli `table_operation::replace_entity` nadal jest prawidłowe, a następnie wykonaj inną operację.  

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

### <a name="insert-or-replace-an-entity"></a>Wstawianie lub zastępowanie elementu

`table_operation::replace_entity`operacje nie powiodą się, jeśli jednostka została zmieniona, ponieważ została pobrana z serwera. Ponadto należy najpierw pobrać jednostkę z serwera, `table_operation::replace_entity` aby odnieść sukces. Czasami nie wiadomo, czy jednostka istnieje na serwerze. Bieżące wartości przechowywane w nim są nieistotne, ponieważ aktualizacja powinna zastąpić je wszystkie. Aby osiągnąć ten wynik, należy użyć `table_operation::insert_or_replace_entity` operacji. Ta operacja wstawia jednostkę, jeśli nie istnieje. Operacja zastępuje jednostkę, jeśli istnieje. W poniższym przykładzie kodu `Jeff Smith` encja klienta jest nadal pobierana, ale jest `table_operation::insert_or_replace_entity`następnie zapisywana z powrotem na serwerze przy użyciu programu . Wszelkie aktualizacje wprowadzone w jednostce między operacjami pobrania i aktualizacji zostaną zastąpione.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki

Za pomocą zapytania wykonywanego względem tabeli można pobrać tylko kilka właściwości z jednostki. Kwerenda w poniższym kodzie używa `table_query::set_select_columns` metody do zwracania tylko adresów e-mail jednostek w tabeli.  

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
> Pobieranie wybranych właściwości z jednostki za pomocą zapytania będzie bardziej wydajne niż pobieranie wszystkich właściwości.
>

## <a name="delete-content"></a>Usuwanie zawartości

### <a name="delete-an-entity"></a>Usuwanie jednostki

Encję można usunąć po jej pobraniu. Po pobraniu encji, wywołać `table_operation::delete_entity` z jednostką, aby usunąć. Następnie wywołaj `cloud_table.execute` metodę. Poniższy kod pobiera i usuwa jednostkę z `Smith` kluczem partycji i kluczem wiersza `Jeff`.

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

### <a name="delete-a-table"></a>Usuwanie tabeli

Poniższy przykład kodu usuwa tabelę z konta magazynu. Tabela, która została usunięta, jest niedostępna do ponownego utworzenia przez pewien czas po usunięciu.  

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

W programie Visual Studio Community Edition, jeśli projekt pobiera błędy kompilacji z powodu plików dołączanych *storage_account.h* i *table.h*, usuń przełącznik kompilatora **/permissive:**

1. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Właściwości**.
1. W oknie dialogowym **Strony właściwości** rozwiń węzeł **Właściwości konfiguracji**, rozwiń węzeł **C/C++**, a następnie wybierz pozycję **Język**.
1. Zmień ustawienie **Tryb zgodności** na **Nie**.

## <a name="next-steps"></a>Następne kroki

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure Storage i interfejsu API tabel usługi Azure Cosmos DB:

* [Wprowadzenie do interfejsu API tabel](table-introduction.md)
* [Generowanie listy zasobów usługi Azure Storage w języku C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Dokumentacja biblioteki klienta usługi Storage dla języka C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
