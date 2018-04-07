---
title: Jak używać usługi tabel magazynu Azure lub interfejsu API tabeli bazy danych rozwiązania Cosmos Azure za pomocą języka PHP | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i usuwania tabeli, za pomocą interfejsu API usługi tabeli za pomocą języka PHP i wstawianie, usuwanie i zapytanie tabeli.
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/22/2018
ms.author: mimig
ms.openlocfilehash: 69fa1f8f9717d1ea4ca5081a45317cff8d752809
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-azure-storage-table-service-or-cosmos-db-table-api-from-php"></a>Jak używać usługi tabel magazynu Azure lub rozwiązania Cosmos interfejsu API tabeli bazy danych z PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Przegląd
W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi tabel magazynu Azure i interfejsu API Azure rozwiązania Cosmos bazy danych tabeli. Przykłady są napisane w PHP i użyj [biblioteki klienta usługi Azure Storage tabeli PHP][download]. Omówione scenariusze obejmują **tworzenia i usuwania tabeli**, i **Wstawianie, usuwanie i badania jednostek w tabeli**. Aby uzyskać więcej informacji w usłudze tabel Azure, zobacz [następne kroki](#next-steps) sekcji.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure

Możesz pracować z tabel za pomocą magazynu tabel Azure lub interfejsu API Azure rozwiązania Cosmos bazy danych tabeli. Dowiedz się więcej na temat różnic między usługami, odczytując [tabeli ofert](table-introduction.md#table-offerings). Musisz utworzyć konto usługi ma być używana. 

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najprostszym sposobem tworzenia pierwsze konto magazynu jest przy użyciu [portalu Azure](https://portal.azure.com). Więcej informacji można znaleźć w temacie [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Można również utworzyć konto magazynu przy użyciu [programu Azure PowerShell](../storage/common/storage-powershell-guide-full.md) lub [interfejsu wiersza polecenia Azure](../storage/common/storage-azure-cli.md).

Jeśli nie chcesz utworzyć konto magazynu w tym momencie, umożliwia także emulatora magazynu Azure do uruchomienia i testowania kodu w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Aby uzyskać instrukcje dotyczące tworzenia konta bazy danych rozwiązania Cosmos platformy Azure, zobacz [Utwórz konto interfejsu API tabeli](create-table-dotnet.md#create-a-database-account).

## <a name="create-a-php-application"></a>Tworzenie aplikacji PHP

Jedynym wymaganiem, aby utworzyć aplikację PHP do uzyskania dostępu do usługi Magazyn tabel lub interfejsu API Azure rozwiązania Cosmos DB tabeli jest odwołanie do klasy w zestawie SDK azure magazynu table dla PHP z w kodzie. Narzędzia do programowania służy do tworzenia aplikacji, łącznie z Notatnika.

W tym przewodniku za pomocą usługi Magazyn tabel lub bazy danych Azure rozwiązania Cosmos funkcje, które mogą być wywoływane w ramach aplikacji PHP lokalnie lub w kodzie działających w roli sieci web platformy Azure, roli procesu roboczego lub witryny sieci Web.

## <a name="get-the-client-library"></a>Pobierz biblioteki klienta

1. Utwórz plik o nazwie composer.json w folderze głównym projektu i Dodaj do niej następujący kod:
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. Pobierz [composer.phar](http://getcomposer.org/composer.phar) w katalogu głównym. 
3. Otwórz wiersz polecenia i uruchom następujące polecenie w katalogu głównym projektu:
```
php composer.phar install
```
Możesz też przejść do [biblioteki klienta usługi Azure Storage tabeli PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) w witrynie GitHub klonowanie kodu źródłowego.


## <a name="add-required-references"></a>Dodaj odwołania wymagane
Aby korzystać z usługi tabeli magazynu lub interfejsów API usługi Azure rozwiązania Cosmos bazy danych, należy:

* Odwołanie przy użyciu pliku automatycznej ładowarki [require_once] [ require_once] instrukcji, i
* Odwoływać się do wszystkich klas, którego używasz.

Poniższy przykład pokazuje, jak dołączyć plik automatycznej ładowarki i odwołanie **TableRestProxy** klasy.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

W poniższych przykładach `require_once` instrukcji jest zawsze widoczne, ale odwołuje się tylko do klas, które są konieczne na przykład do wykonania.

## <a name="add-a-storage-table-service-connection"></a>Dodawanie połączenia z usługą Magazyn tabeli
Można utworzyć klienta usługi magazynu tabeli, najpierw musi mieć prawidłowe parametry połączenia. Format dla parametrów połączenia usługi Magazyn tabel jest:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Dodaj połączenie bazy danych Azure rozwiązania Cosmos
Można utworzyć wystąpienia klienta tabeli bazy danych rozwiązania Cosmos Azure, najpierw musi mieć prawidłowe parametry połączenia. Format ciągu połączenia bazy danych Azure rozwiązania Cosmos jest:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Dodaj połączenie emulatora magazynu
Dostęp do emulatora magazynu:

```php
UseDevelopmentStorage = true
```

Aby utworzyć klienta usługi tabel Azure lub bazy danych Azure rozwiązania Cosmos klienta, należy użyć **TableRestProxy** klasy. Możesz:

* Przekaż parametry połączenia do niego bezpośrednio lub
* Użyj **CloudConfigurationManager (CCM)** do sprawdzenia wiele źródeł zewnętrznych ciągu połączenia:
  * Domyślnie pochodzi z obsługą jednego źródła zewnętrznego — zmiennych środowiskowych.
  * Można dodać nowego źródła rozszerzając `ConnectionStringSource` klasy.

Przykłady przedstawione w tym miejscu ciąg połączenia jest przekazywany bezpośrednio.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Tworzenie tabeli
A **TableRestProxy** obiektu umożliwia tworzenie tabeli z **createTable** metody. Podczas tworzenia tabeli, można ustawić limitu czasu usługi tabeli. (Aby uzyskać więcej informacji na temat limitu czasu usługi tabeli, zobacz [ustawienie przekroczeń limitu czasu dla operacji usługi tabeli][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Aby uzyskać informacji o ograniczeniach dotyczących nazw tabeli, zobacz [opis modelu danych usługi tabel][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę do tabeli, Utwórz nową **jednostki** obiektu i przekaż go do **TableRestProxy -> insertEntity**. Należy pamiętać, że podczas tworzenia obiektu, należy określić `PartitionKey` i `RowKey`. Są to unikatowych identyfikatorów dla jednostek które mają wartości, które można wykonać zapytania znacznie szybciej niż inne właściwości jednostki. System używa `PartitionKey` to automatyczną dystrybucję jednostek tabeli przez wiele węzłów magazynu. Jednostek o takim samym `PartitionKey` są przechowywane w tym samym węźle. (Wykonywania operacji na wiele jednostek przechowywanych w tym samym węźle lepszym rozwiązaniem niż na jednostek przechowywanych w różnych węzłach.) `RowKey` Jest unikatowy identyfikator jednostek w partycji.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Informacje dotyczące właściwości i typów, zobacz [opis modelu danych usługi tabel][table-data-model].

**TableRestProxy** klasy oferuje dwie alternatywne metody Wstawianie jednostek: **insertOrMergeEntity** i **insertOrReplaceEntity**. Aby korzystać z tych metod, Utwórz nową **jednostki** i przekaż go jako parametr do każdej z metod. Każda metoda powoduje wstawienie jednostki, jeśli nie istnieje. Jeśli ta jednostka już istnieje, **insertOrMergeEntity** aktualizacji wartości właściwości, jeśli właściwości już istnieje i dodaje nowe właściwości nie są dostępne, podczas gdy **insertOrReplaceEntity** całkowicie zastępuje istniejącej jednostki. Poniższy przykład przedstawia użycie **insertOrMergeEntity**. Jeśli jednostka z `PartitionKey` "tasksSeattle" i `RowKey` "1" nie istnieje, zostanie on włożony. Jednak jeśli wcześniej wstawione (jak pokazano w powyższym przykładzie), `DueDate` zaktualizowaniu właściwości oraz `Status` właściwość została dodana. `Description` i `Location` są również zaktualizować właściwości, ale z wartościami które skutecznie pozostaw je bez zmian. Jeśli te dwie właściwości to drugie nie zostały dodane, jak pokazano w przykładzie, ale znajdował się na obiekcie docelowym, ich istniejące wartości pozostanie bez zmian.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki
**TableRestProxy -> getEntity** metoda pozwala na pobranie jednej jednostki przez wykonanie zapytania dotyczącego jego `PartitionKey` i `RowKey`. W przykładzie poniżej klucz partycji `tasksSeattle` i klucz wiersza `1` są przekazywane do **getEntity** metody.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji
Zapytania jednostki są konstruowane przy użyciu filtrów (Aby uzyskać więcej informacji, zobacz [badania tabel i jednostek][filters]). Aby uzyskać dostęp do wszystkich jednostek w partycji, użyj filtru "PartitionKey eq *nazwa_partycji*". Poniższy przykład przedstawia sposób pobieranie wszystkich jednostek w `tasksSeattle` partycji, przekazując filtr, aby **queryEntities** metody.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Pobrać podzbiór jednostek w partycji
Tego samego wzorca użyty w poprzednim przykładzie może służyć do pobierania dowolny podzbiór jednostek w partycji. Podzbiór pobierania jednostek są określane przez filtr używasz (Aby uzyskać więcej informacji, zobacz [badania tabel i jednostek][filters]). Poniższy przykład przedstawia sposób Użyj filtru w celu pobrania wszystkich jednostek z określonym `Location` i `DueDate` mniej niż określona data.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Pobrać podzbioru właściwości jednostki
Zapytania można pobrać podzbioru właściwości jednostki. Ta technika, zwana *projekcji*, redukuje przepustowość i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Aby określić właściwości do pobrania, należy przekazać nazwę właściwości do **zapytania -> addSelectField** metody. Tę metodę można wywołać wielokrotnie można dodać więcej właściwości. Po wykonaniu **TableRestProxy -> queryEntities**, zwrócone jednostki będzie miał tylko wybranych właściwości. (Jeśli chcesz przywrócić podzbiór jednostek tabeli, jak pokazano powyżej zapytań Użyj filtru.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Aktualizuj jednostkę
Można zaktualizować istniejącej jednostki przy użyciu **jednostki -> setProperty** i **jednostki -> addProperty** w jednostki, a następnie wywołania metod **TableRestProxy -> updateEntity**. Poniższy przykład pobiera jednostki, modyfikuje jedną właściwość usuwa innej właściwości i dodaje nową właściwość. Usunięcie właściwości ustawiając wartość **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Usuwanie jednostki
Aby usunąć jednostkę, przekaż nazwę tabeli i jednostki `PartitionKey` i `RowKey` do **TableRestProxy -> deleteEntity** metody.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Kontrolach współbieżności, można ustawić Etag obiektu do usunięcia za pomocą **DeleteEntityOptions -> setEtag** — metoda i przekazywanie **DeleteEntityOptions** do obiektu  **deleteEntity** jako czwartego parametru.

## <a name="batch-table-operations"></a>Operacje tabeli partii
**TableRestProxy -> partii** metoda umożliwia wykonywanie wielu operacji w ramach pojedynczego żądania. Wzorzec tutaj obejmuje dodawanie działań do **BatchRequest** obiektu, a następnie przekazywanie **BatchRequest** do obiektu **TableRestProxy -> partii** metody. Aby dodać operację do **BatchRequest** obiektu, można wywołać żadnego z następujących metod wiele razy:

* **addInsertEntity** (dodaje operację insertEntity)
* **addUpdateEntity** (dodaje operację updateEntity)
* **addMergeEntity** (dodaje operacji mergeEntity)
* **addInsertOrReplaceEntity** (dodaje operację insertOrReplaceEntity)
* **addInsertOrMergeEntity** (dodaje operację insertOrMergeEntity)
* **addDeleteEntity** (dodaje operacji deleteEntity)

Poniższy przykład przedstawia sposób wykonania **insertEntity** i **deleteEntity** operacji w ramach pojedynczego żądania. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Aby uzyskać więcej informacji na temat przetwarzania wsadowego operacje tabeli, zobacz [wykonywanie transakcji grupy jednostek][entity-group-transactions].

## <a name="delete-a-table"></a>Usuwanie tabeli
Na koniec można usunąć tabeli, Przekaż nazwy tabeli, aby **TableRestProxy -> deleteTable** metody.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi Azure tabeli i bazy danych Azure rozwiązania Cosmos, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.

* [Centrum deweloperów języka PHP](/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
