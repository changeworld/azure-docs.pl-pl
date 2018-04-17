---
title: Jak używać magazynu tabel platformy Azure i interfejsu API Azure rozwiązania Cosmos DB tabeli z Ruby | Dokumentacja firmy Microsoft
description: Przechowywanie danych strukturalnych w chmurze za pomocą Magazynu tabel Azure, magazyn danych NoSQL.
services: cosmos-db
documentationcenter: ruby
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 19ffdab40b3032421612ef4ba1b840eeb0d2e62b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-ruby"></a>Jak używać magazynu tabel platformy Azure i interfejsu API Azure rozwiązania Cosmos DB tabeli z Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Przegląd
W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi tabel Azure i interfejsu API Azure rozwiązania Cosmos bazy danych tabeli. Przykłady są napisane w Ruby i użyj [biblioteki klienta usługi Azure Storage tabeli dla środowiska Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Omówione scenariusze obejmują **tworzenia i usuwania tabeli, wstawianie i badania jednostek w tabeli**.

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta usługi interfejsu API Azure rozwiązania Cosmos DB tabeli
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Dodaj dostęp do magazynu lub bazy danych Azure rozwiązania Cosmos
Aby korzystać z usługi Azure Storage lub bazy danych rozwiązania Cosmos platformy Azure, musisz pobrać i za pomocą pakietu Ruby Azure, zawierającym zestaw wygody bibliotek, które komunikują się z usługami REST tabeli.

### <a name="use-rubygems-to-obtain-the-package"></a>Umożliwia uzyskanie pakietu RubyGems
1. Użyj interfejsu wiersza polecenia, takich jak **PowerShell** (system Windows), **terminali** (Mac), lub **Bash** (Unix).
2. Typ **gem zainstalować azure magazynu table** w oknie wiersza polecenia, aby zainstalować gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Użyj edytora tekstu, Dodaj następujący element do góry pliku dopisków fonetycznych, których zamierzasz używać magazynu:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Dodawanie połączenia z magazynem Azure
Moduł usługi Azure Storage odczytuje zmiennych środowiskowych **AZURE_STORAGE_ACCOUNT** i **AZURE_STORAGE_ACCESS_KEY** informacje wymagane do łączenia się z kontem magazynu platformy Azure. Jeśli te zmienne środowiskowe nie są skonfigurowane, należy określić informacje o koncie przed użyciem **Azure::Storage::Table::TableService** następującym kodem:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Aby uzyskać te wartości z klasyczny lub Menedżera zasobów konta magazynu w portalu Azure:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu, którego chcesz użyć.
3. W bloku ustawienia po prawej stronie, kliknij przycisk **klucze dostępu**.
4. W bloku klucze dostępu pojawia się zostanie wyświetlony klucz dostępu 1 i klucz dostępu 2. Można użyć jednego z tych.
5. Kliknij ikonę kopiowania, aby skopiować klucz do Schowka.

## <a name="add-an-azure-cosmos-db-connection"></a>Dodaj połączenie bazy danych Azure rozwiązania Cosmos
Aby nawiązać połączenie bazy danych Azure rozwiązania Cosmos, skopiuj parametry połączenia podstawowej z portalu Azure i utworzyć **klienta** przy użyciu parametrów połączenia skopiowane. Można przekazać **klienta** obiekt po utworzeniu **TableService** obiektu:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Tworzenie tabeli
**Azure::Storage::Table::TableService** obiektu umożliwia pracę z tabel i jednostek. Aby utworzyć tabelę, użyj **create_table()** metody. Poniższy przykład tworzy tabelę lub drukuje ten błąd, jeśli istnieje.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę, należy najpierw utworzyć obiektu skrótu, który definiuje właściwości jednostki. Należy zauważyć, że dla każdej jednostki można określić **PartitionKey** i **RowKey**. Są to unikatowe identyfikatory jednostek które są wartości, które można wykonać zapytania znacznie szybciej niż inne właściwości. Usługa Azure Storage korzysta **PartitionKey** to automatyczną dystrybucję jednostek tabeli przez wiele węzłów magazynu. Jednostek o takim samym **PartitionKey** są przechowywane w tym samym węźle. **RowKey** jest unikatowy identyfikator w partycji, należy do jednostki.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Aktualizuj jednostkę
Dostępnych jest kilka metod do zaktualizowania istniejącej jednostki:

* **update_entity():** zaktualizowania istniejącej jednostki poprzez zastąpienie jej.
* **merge_entity():** aktualizuje istniejącą jednostkę przez scalenie nowej wartości właściwości istniejącej jednostki.
* **insert_or_merge_entity():** aktualizacji przez zastąpienie istniejącej jednostki. Jeśli jednostka nie istnieje, zostanie wstawiony nowy:
* **insert_or_replace_entity():** aktualizuje istniejącą jednostkę przez scalenie nowej wartości właściwości istniejącej jednostki. Jeśli jednostka nie istnieje, zostanie wstawiony nowy.

W poniższym przykładzie pokazano, aktualizowanie jednostki przy użyciu **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Z **update_entity()** i **merge_entity()**, jeśli jednostka, która aktualizujesz nie istnieje, operacja aktualizacji zakończy się niepowodzeniem. W związku z tym, jeśli chcesz przechowywać jednostki niezależnie od tego, czy już istnieje, należy zamiast tego użyć **insert_or_replace_entity()** lub **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Praca z grupami jednostek
Czasami warto przesłać wiele operacji ze sobą w partii zapewnienie atomic przetwarzania przez serwer. Celu, który należy najpierw utworzyć **partii** obiekt, a następnie użyj **execute_batch()** metoda **TableService**. W poniższym przykładzie pokazano, przesyłanie dwie jednostki z RowKey 2 i 3 w partii. Należy zauważyć, że działa ona tylko dla jednostek o tej samej PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Zapytanie dla jednostki
Aby sprawdzić jednostkę w tabeli, użyj **get_entity()** metody, przekazując nazwy tabeli **PartitionKey** i **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Zapytanie zestawu jednostek
Aby odpytać zestawu jednostek w tabeli, utworzyć obiektu skrótu zapytania i użyj **query_entities()** metody. W poniższym przykładzie pokazano pobieranie wszystkich jednostek o takim samym **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Jeśli zestaw wyników jest zbyt duży dla pojedynczego zapytania do zwrócenia, zwracany jest token kontynuacji, której można pobrać kolejnych stronach.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki
Zapytanie do tabeli może pobrać kilka właściwości jednostki. Ta technika, zwana "projekcji", redukuje przepustowość i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Użyj klauzuli select i przekazywać nazwy właściwości, które chcesz przełączyć za pośrednictwem do klienta.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Usuwanie jednostki
Aby usunąć jednostkę, użyj **delete_entity()** metody. Podaj nazwę tabeli, która zawiera obiekt, PartitionKey i RowKey jednostki.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Usuwanie tabeli
Aby usunąć tabelę, użyj **delete_table()** — metoda i przekazać nazwy tabeli, które chcesz usunąć.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Kolejne kroki

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Centrum deweloperów języka Ruby](https://azure.microsoft.com/develop/ruby/)
* [Biblioteka klienta usługi Microsoft Azure Storage tabeli dla środowiska Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table) 

