---
title: Jak korzystać z usługi Azure Table Storage i interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka C++
description: Przechowywanie danych strukturalnych w chmurze za pomocą usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 40b84a56f93ad670a26eb876a18820e0d4037f63
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130555"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak korzystać z usługi Azure Table Storage i interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Omówienie
Ten przewodnik przedstawia sposób wykonywania typowych zdań przy użyciu usługi Azure Table Storage oraz interfejsu API tabel usługi Azure Cosmos DB. Przykłady są napisane w języku C++ i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Przedstawione scenariusze obejmują **tworzenie i usuwanie tabel** oraz **pracę z jednostkami w tabelach**.

> [!NOTE]
> Ten przewodnik jest przeznaczony do użycia z biblioteką klienta usługi Azure Storage dla języka C++ w wersji 1.0.0 lub wyższej. Zalecana wersja biblioteki klienta usługi Storage to wersja 2.2.0, dostępna za pośrednictwem narzędzia [NuGet](https://www.nuget.org/packages/wastorage) lub witryny [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++
Podczas pracy z tym przewodnikiem będziesz używać funkcji magazynu, które można uruchamiać w aplikacji języka C++. W tym celu należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i utworzyć konto usługi Azure Storage w ramach subskrypcji platformy Azure.  

Możesz zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, korzystając z następujących metod:

* **Linux:** postępuj zgodnie z instrukcjami dostępnymi na stronie [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) (Plik README biblioteki klienta usługi Azure Storage dla języka C++).  
* **Windows:** W programie Visual Studio kliknij pozycję **Narzędzia > Menedżer pakietów NuGet > Konsola menedżera pakietów**. Wpisz następujące polecenie w [konsoli menedżera pakietów NuGet](/nuget/tools/package-manager-console) i naciśnij klawisz Enter.  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>Konfigurowanie dostępu do biblioteki klienta usługi Table Storage
Na początku pliku C++, w którym chcesz używać interfejsów API usługi Azure Storage w celu uzyskania dostępu do tabel, dodaj następujące instrukcje include:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

W kliencie usługi Azure Storage lub Cosmos DB punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia. W aplikacji klienckiej należy podać parametry połączenia usługi Storage lub Azure Cosmos DB w odpowiednim formacie.

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage
 Użyj nazwy konta usługi Storage i klucza dostępu do konta usługi Storage z witryny [Azure Portal](https://portal.azure.com) jako wartości *AccountName* i *AccountKey*. Aby uzyskać więcej informacji dotyczących kont i kluczy dostępu usługi Storage, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md). W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia usługi Azure Storage:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Cosmos DB
Użyj nazwy konta usługi Azure Cosmos DB, klucza podstawowego oraz punktu końcowego z witryny [Azure Portal](https://portal.azure.com) jako wartości *AccountName*, *AccountKey* i *Endpoint*. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia usługi Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


Aby przetestować aplikację na komputerze lokalnym z systemem Windows, możesz użyć [emulatora magazynu](../storage/common/storage-use-emulator.md) platformy Azure, instalowanego w ramach [zestawu Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu to narzędzie umożliwiające symulowanie usług Azure Blob, Queue i Table Storage na lokalnej maszynie deweloperskiej. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia z lokalnym emulatorem magazynu:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulator magazynu platformy Azure, kliknij przycisk **Start** lub naciśnij klawisz systemu Windows. Zacznij wpisywać **emulator magazynu Microsoft Azure**, a następnie wybierz pozycję **Emulator magazynu Microsoft Azure** z listy aplikacji.  

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.  

## <a name="retrieve-your-connection-string"></a>Pobieranie parametrów połączenia
Możesz użyć klasy **cloud_storage_account** do reprezentowania informacji o koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia usługi Storage, użyj metody **parse**.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie pobierz odwołanie do klasy **cloud_table_client**, co umożliwia pobieranie obiektów referencyjnych dla tabel i jednostek przechowywanych w usłudze Table Storage. Poniższy kod tworzy obiekt **cloud_table_client** przy użyciu pobranego powyżej obiektu konta magazynu:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Tworzenie tabeli
Obiekt **cloud_table_client** umożliwia pobieranie obiektów referencyjnych dla tabel i jednostek. Poniższy kod tworzy obiekt **cloud_table_client**, a następnie tworzy przy jego użyciu nową tabelę.

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
Aby dodać jednostkę do tabeli, utwórz nowy obiekt **table_entity** i przekaż go do metody **table_operation::insert_entity**. W poniższym kodzie imię klienta jest używane jako klucz wiersza, a nazwisko jako klucz partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, niemniej użycie różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Aby uzyskać więcej informacji, zobacz temat [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../storage/common/storage-performance-checklist.md).

Poniższy kod tworzy nowe wystąpienie obiektu **table_entity**, zawierające dane klienta, które mają być przechowywane. Następnie wywołuje metodę**table_operation::insert_entity**, aby utworzyć obiekt **table_operation** w celu wstawienia jednostki do tabeli, i kojarzy z nim nową jednostkę tabeli. Na koniec wywołuje metodę execute względem obiektu **cloud_table**. W rezultacie nowy obiekt **table_operation** wysyła do usługi Table Storage żądanie wstawienia nowej jednostki klienta do tabeli „people” (osoby).  

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
Możesz wstawić partię jednostek do usługi tabel w ramach jednej operacji zapisu. Poniższy kod tworzy obiekt **table_batch_operation**, a następnie dodaje do niego trzy operacje wstawiania. Każda operacja wstawiania zostaje dodana przez utworzenie nowego obiektu jednostki, ustawienie jego wartości i wywołanie metody insert względem obiektu **table_batch_operation** w celu skojarzenia jednostki z nową operacją wstawiania. Następnie wywoływana jest metoda **cloud_table.execute** w celu wykonania operacji.  

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

* W ramach jednej partii można wykonać maksymalnie 100 operacji wstawiania, usuwania, łączenia, zastępowania, wstawiania lub łączenia i wstawiania lub zastępowania — w dowolnej kombinacji.  
* Operacja zbiorcza może zawierać operację pobierania, o ile jest to jedyna operacja w partii.  
* Wszystkie jednostki w jednej operacji zbiorczej muszą mieć ten sam klucz partycji.  
* Maksymalny rozmiar ładunku danych operacji zbiorczej to 4 MB.  

## <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji
Aby wysłać zapytanie do tabeli dla wszystkich obiektów w partycji, użyj obiektu **table_query**. Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.  

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

Zapytanie w tym przykładzie zwraca wszystkie jednostki spełniające kryteria filtrowania. Jeśli masz duże tabele i często pobierasz jednostki z tabel, zalecamy zamiast tego przechowywanie danych w obiektach blob w usłudze Azure Storage.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Pobieranie zakresu jednostek w partycji
Jeśli nie chcesz wykonywać zapytania dla wszystkich jednostek w partycji, możesz określić zakres, łącząc filtr klucza partycji z filtrem klucza wiersza. Poniższy przykład kodu wykorzystuje dwa filtry do pobrania wszystkich jednostek w partycji „Smith”, w których klucz wiersza (imię) rozpoczyna się od litery alfabetu wcześniejszej niż „E”, a następnie drukuje wyniki zapytania.  

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

## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki
Można napisać zapytanie do pobrania jednej, określonej jednostki. W poniższym kodzie użyto metody **table_operation::retrieve_entity** do wskazania klienta o nazwisku „Jeff Smith”. Metoda ta zwraca tylko jedną jednostkę, a nie zbiór jednostek, a zwrócona wartość znajduje się w obiekcie **table_result**. Określenie kluczy partycji i wiersza w pojedynczym zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z usługi tabel.  

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
Aby zastąpić jednostkę, pobierz ją z usługi Table Storage, zmodyfikuj obiekt jednostki, a następnie zapisz zmiany w usłudze Table Storage. Poniższy kod zmienia numer telefonu i adres e-mail istniejącego klienta. Zamiast wywołania metody **table_operation::insert_entity** w tym kodzie jest używana metoda **table_operation::replace_entity**. Dzięki temu jednostka będzie całkowicie zastąpiona na serwerze, chyba że jednostka na serwerze zmieniła się od czasu jej pobrania. W takim przypadku operacja nie powiedzie się. Ten błąd uniemożliwia aplikacji nieodwracalne nadpisanie zmiany dokonanej pomiędzy pobraniem i zaktualizowaniem przez inny składnik aplikacji. W przypadku tego błędu prawidłowa procedura obejmuje ponowne pobranie jednostki, wprowadzenie zmian (jeśli nadal mają zastosowanie), a następnie ponowne przeprowadzenie operacji **table_operation::replace_entity**. W następnej sekcji opisano sposób zastąpienia tego zachowania.  

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
Operacje **table_operation::replace_entity** zakończą się niepowodzeniem, jeśli jednostka została zmieniona od czasu jej pobrania z serwera. Ponadto aby operacja **table_operation::replace_entity** się powiodła, należy najpierw pobrać jednostkę z serwera. Czasami jednak nie wiadomo, czy jednostka istnieje na serwerze, a jej obecne wartości są nieistotne — wszystkie powinny zostać zastąpione zaktualizowanymi wartościami. W takiej sytuacji należy użyć operacji **table_operation::insert_or_replace_entity**. Ta operacja wstawi jednostkę, jeśli jednostka nie istnieje, lub zastąpi ją, jeśli już istnieje — niezależnie od czasu ostatniej aktualizacji. W poniższym przykładzie kodu jednostka klienta „Jeff Smith” również zostanie pobrana, ale następnie zostanie zapisana na serwerze za pomocą operacji **table_operation::insert_or_replace_entity**. Wszelkie aktualizacje wprowadzone w jednostce między operacjami pobrania i aktualizacji zostaną zastąpione.  

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
Za pomocą zapytania wykonywanego względem tabeli można pobrać tylko kilka właściwości z jednostki. Zapytanie w poniższym kodzie zwraca wyłącznie adresy e-mail jednostek w tabeli dzięki użyciu metody **table_query::set_select_columns**.  

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
> 

## <a name="delete-an-entity"></a>Usuwanie jednostki
Można łatwo usunąć pobraną jednostkę. Po pobraniu jednostki wywołaj metodę **table_operation::delete_entity**, wskazując jednostkę do usunięcia. Następnie wywołaj metodę **cloud_table.execute**. Poniższy kod umożliwia pobranie i usunięcie jednostki, której klucz partycji to „Smith”, a klucz wiersza to „Jeff”.  

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
* Błędy kompilacji w programie Visual Studio 2017 Community Edition

  Jeśli w projekcie występują błędy kompilacji powodowane przez pliki dołączane storage_account.h i table.h, usuń przełącznik kompilatora **/permissive-**. 
  - Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Właściwości**.
  - W oknie dialogowym **Strony właściwości** rozwiń węzeł **Właściwości konfiguracji**, rozwiń węzeł **C/C++**, a następnie wybierz pozycję **Język**.
  - Zmień ustawienie **Tryb zgodności** na **Nie**.
   
## <a name="next-steps"></a>Następne kroki
Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure Storage i interfejsu API tabel usługi Azure Cosmos DB: 

* [Wprowadzenie do interfejsu API tabel](table-introduction.md)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Generowanie listy zasobów usługi Azure Storage w języku C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Dokumentacja biblioteki klienta usługi Storage dla języka C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
