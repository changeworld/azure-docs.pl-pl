---
title: Rozpoczynanie pracy z magazynem tabel Azure przy użyciu języka Python | Dokumentacja firmy Microsoft
description: Przechowywanie danych strukturalnych w chmurze za pomocą Magazynu tabel Azure, magazyn danych NoSQL.
services: cosmos-db
documentationcenter: python
author: SnehaGunda
manager: kfile
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: c8f35656e9db07b596cd24ecb570fa0960f540b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-azure-table-storage-using-python"></a>Rozpoczynanie pracy z magazynem tabel Azure przy użyciu języka Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage to usługa, która przechowuje dane NoSQL ze strukturą w chmurze, udostępniając magazyn par klucz-atrybut z projektem bez schematu. Ponieważ Magazyn tabel nie ma schematu, łatwo zaadaptować dane do rozwijających się potrzeb aplikacji. Dla większości aplikacji dostęp do danych w usłudze Table Storage jest szybki i ekonomiczny, jest też zazwyczaj tańszy od tradycyjnego rozwiązania SQL dla podobnych ilości danych.

Usługa Table Storage umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkowników dla aplikacji sieci Web, książki adresowe, informacje o urządzeniach i inne typy metadanych, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę jednostek, a konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

### <a name="about-this-tutorial"></a>Informacje o tym samouczku
Ten samouczek przedstawia sposób użycia [Azure rozwiązania Cosmos DB tabeli SDK dla języka Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) w typowych scenariuszy magazynu tabel Azure. Nazwa zestawu SDK wskazuje jest do użycia z bazy danych rozwiązania Cosmos platformy Azure, ale działa zarówno Azure DB rozwiązania Cosmos i magazynu tabel Azure, każda usługa zawiera tylko unikatowe punktu końcowego. Te scenariusze są przedstawione za pomocą języka Python przykłady, które ilustrują sposób:
* Tworzenie i usuwanie tabel
* Wstaw i zapytań jednostek
* Modyfikowanie jednostek

Podczas pracy nad scenariusze w tym samouczku, można odwoływać się do [Azure rozwiązania Cosmos DB SDK dla odwołania do interfejsu API języka Python](https://azure.github.io/azure-cosmosdb-python/).

## <a name="prerequisites"></a>Wymagania wstępne

Do pomyślnego ukończenia samouczka niezbędne są:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 lub 3,6
- [Rozwiązania Cosmos Azure DB tabeli zestawu SDK dla języka Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Zestaw SDK połączy się z magazynu tabel Azure i interfejsu API Azure rozwiązania Cosmos bazy danych tabeli.
- [Konto usługi Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account#create-a-storage-account) lub [konta bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/en-us/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta usługi interfejsu API Azure rozwiązania Cosmos DB tabeli
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Zainstaluj tabeli bazy danych rozwiązania Cosmos Azure SDK dla języka Python

Po utworzeniu konta magazynu, następnym krokiem jest zainstalowanie [Microsoft Azure rozwiązania Cosmos DB tabeli SDK dla języka Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Aby uzyskać więcej informacji na temat instalowania zestawu SDK, zapoznaj się [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) pliku rozwiązania Cosmos tabeli bazy danych z zestawu SDK dla języka Python repozytorium w witrynie GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importuj klasy TableService i jednostek

Aby pracować z jednostkami w usłudze Azure tabel w języku Python, należy użyć [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) i [jednostki] [ py_Entity] klasy. Dodaj ten kod u góry pliku Python do zaimportowania zarówno:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Połączyć się z usługą Azure tabeli

Aby nawiązać połączenie usługi tabel magazynu Azure, Utwórz [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) obiektu i przekazać klucz konta magazynu nazwy i konta. Zastąp `myaccount` i `mykey` z nazwą konta i klucz.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Łączenie z usługą Azure Cosmos DB

Aby nawiązać połączenie bazy danych Azure rozwiązania Cosmos, skopiuj parametry połączenia podstawowej z portalu Azure i utworzyć [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) przy użyciu parametrów połączenia skopiowane:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Tworzenie tabeli

Wywołanie [create_table] [ py_create_table] do utworzenia tabeli.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać jednostkę, najpierw utwórz obiekt, który reprezentuje jednostki, a następnie przekaż obiekt do [metody TableService.insert_entity][py_TableService]. Obiekt jednostki można słownika lub typu obiektu [jednostki][py_Entity]i definiuje użytkownika jednostki nazwy i wartości właściwości. Każdy podmiot musi zawierać wymaganych [PartitionKey i RowKey](#partitionkey-and-rowkey) właściwości, oprócz innych właściwości, należy zdefiniować dla jednostki.

Ten przykład tworzy obiekt słownik reprezentujący jednostkę, następnie przekazuje ją do [insert_entity] [ py_insert_entity] metody, aby dodać go do tabeli:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Ten przykład tworzy [jednostki] [ py_Entity] obiekt, a następnie przekazuje go do [insert_entity] [ py_insert_entity] metody, aby dodać go do tabeli:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey i RowKey

Należy określić zarówno **PartitionKey** i **RowKey** właściwości dla każdej jednostki. Jak razem są unikatowe identyfikatory jednostki, tworzą one klucza podstawowego jednostki. Można badać przy użyciu następujących wartości szybciej, niż można zapytania innych właściwości jednostki, ponieważ tylko te właściwości są indeksowane.

Używane przez usługę tabeli **PartitionKey** do inteligentnie rozpowszechniają jednostek tabeli węzłów magazynu. Jednostki, które mają taki sam **PartitionKey** są przechowywane w tym samym węźle. **RowKey** jest unikatowy identyfikator w partycji, należy do jednostki.

## <a name="update-an-entity"></a>Aktualizuj jednostkę

Aby zaktualizować wszystkie wartości właściwości jednostki, należy wywołać [update_entity] [ py_update_entity] metody. W tym przykładzie pokazano, jak zastąpić istniejącą jednostkę zaktualizowanej wersji:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Jeśli jednostka, która jest aktualizowana już nie istnieje, następnie operacji aktualizacji zakończy się niepowodzeniem. Jeśli chcesz przechowywać jednostki tego, czy istnieje lub nie, użyj [insert_or_replace_entity][py_insert_or_replace_entity]. W poniższym przykładzie pierwsze wywołanie spowoduje zastąpienie istniejącej jednostki. Drugie wywołanie będzie wstawić nową jednostkę, ponieważ brak jednostki z określonym PartitionKey i RowKey nie istnieje w tabeli.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> [Update_entity] [ py_update_entity] metoda zastępuje wszystkie właściwości i wartości istniejącej jednostki, która umożliwia także usunąć właściwości z istniejącej jednostki. Można użyć [merge_entity] [ py_merge_entity] metody do zaktualizowania istniejącej jednostki z wartościami właściwości nowe lub zmodyfikowane bez całkowicie zastępowania jednostki.

## <a name="modify-multiple-entities"></a>Modyfikowanie wiele jednostek

W celu zapewnienia atomic przetwarzania żądania przez usługę tabeli, możesz przesłać wiele operacji ze sobą w partii. Najpierw użyj [TableBatch] [ py_TableBatch] klasy, aby dodać wiele operacji do pojedynczej partii. Następnie wywołaj [TableService][py_TableService].[ commit_batch] [ py_commit_batch] przesłać operacje w niepodzielną operację. Wszystkie jednostki można zmodyfikować w partii musi należeć do tej samej partycji.

W tym przykładzie dodaje dwie jednostki w partii:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Partie można również przy użyciu składni Menedżera kontekstu:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Zapytanie dla jednostki

Aby wyszukać jednostkę w tabeli, należy przekazać jego PartitionKey i RowKey do [TableService][py_TableService].[ get_entity] [ py_get_entity] metody.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Zapytanie zestawu jednostek

Można wyszukać zestawu jednostek, podając ciąg filtru z **filtru** parametru. W tym przykładzie znajduje wszystkie zadania w Seattle, stosując filtr na PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki

Można również ograniczyć, właściwości, które są zwracane dla każdej jednostki w zapytaniu. Ta technika, zwana *projekcji*, redukuje przepustowość i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek lub zestawów wyników. Użyj **wybierz** parametru i przekazać nazwy właściwości, które jest zwracana do klienta.

Zapytanie w poniższym kodzie zwraca tylko opisy jednostek w tabeli.

> [!NOTE]
> Poniższy fragment działa tylko w usłudze Azure Storage. Nie jest obsługiwana przez emulator magazynu.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Usuwanie jednostki

Usuwanie jednostki przez przekazanie jej **PartitionKey** i **RowKey** do [delete_entity] [ py_delete_entity] metody.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Usuwanie tabeli

Jeśli nie są już potrzebne tabeli lub dowolnej jednostki w niej wywołanie [delete_table] [ py_delete_table] metodę, aby trwale usunąć tabelę z usługi Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Kolejne kroki

* [Często zadawane pytania — opracowywania tabelę interfejsu API](https://docs.microsoft.com/en-us/azure/cosmos-db/faq#develop-with-the-table-api)
* [Azure SDK DB rozwiązania Cosmos dla dokumentacji interfejsu API języka Python](https://azure.github.io/azure-cosmosdb-python/)
* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Eksplorator magazynu Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md): wolne, i platform aplikacji do pracy wizualnie z danymi usługi Azure Storage w systemie Windows, macOS i Linux.
* [Praca z języka Python w programie Visual Studio (z systemem Windows)](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
