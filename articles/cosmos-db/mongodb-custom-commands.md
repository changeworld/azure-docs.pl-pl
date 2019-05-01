---
title: Polecenia rozszerzenia bazy danych MongoDB na zarządzanie danymi zapisanymi w interfejsie API usługi Azure Cosmos DB dla bazy danych MongoDB
description: W tym artykule opisano, jak zarządzanie danymi zapisanymi w interfejsie API usługi Azure Cosmos DB dla bazy danych MongoDB za pomocą polecenia rozszerzenia bazy danych MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 94b1048befc8716caf5f7f51adb1f95d047d4077
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925652"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Polecenia rozszerzenia bazy danych MongoDB umożliwia zarządzanie danymi zapisanymi w interfejsie API usługi Azure Cosmos DB dla bazy danych MongoDB 

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Można komunikować się z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu dowolnej z "open source" [sterowników klienta bazy danych MongoDB](https://docs.mongodb.org/ecosystem/drivers). Interfejs API usługi Azure Cosmos DB, bazy danych mongodb umożliwia korzystanie z istniejących sterowników klienta dzięki przestrzeganiu [protokół przewodowy MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, można korzystać z zalet usługi Cosmos DB w takich jak dystrybucji globalnej, automatyczne dzielenie na fragmenty, wysokiej dostępności, gwarancje dot. opóźnienia, automatyczne, szyfrowanie w spoczynku, kopii zapasowych i wiele więcej, podczas gdy zachowywanie inwestycji w aplikacji bazy danych MongoDB.

## <a name="mongodb-protocol-support"></a>Obsługa protokołu bazy danych MongoDB

Domyślnie interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB jest zgodna z bazą danych MongoDB server w wersji 3.2, aby uzyskać więcej informacji, zobacz [obsługiwanych funkcji i składni](mongodb-feature-support.md). Funkcje lub operatorów zapytań, które dodano w bazie danych MongoDB w wersji 3.4 są obecnie dostępne w wersji zapoznawczej w interfejsie API usługi Azure Cosmos DB dla bazy danych MongoDB. Następujące polecenia rozszerzenia obsługi określonych funkcji usługi Azure Cosmos DB, podczas wykonywania operacji CRUD na danych przechowywanych w usłudze Azure Cosmos DB w interfejsie API dla bazy danych MongoDB:

* [Tworzenie bazy danych](#create-database)
* [Aktualizuj bazę danych](#update-database)
* [Pobierz bazę danych](#get-database)
* [Tworzenie kolekcji](#create-collection)
* [Aktualizowanie kolekcji](#update-collection)
* [Pobierz kolekcję](#get-collection)

## <a id="create-database"></a> Tworzenie bazy danych

Polecenia create database rozszerzenie tworzy nową bazę danych MongoDB. Nazwa bazy danych jest używana w kontekście bazy danych, względem którego polecenie jest wykonywane. Format polecenia CreateDatabase jest następująca:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

W poniższej tabeli opisano parametry w ramach polecenia:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| Akcja niestandardowa   |  string  |   Nazwa niestandardowego polecenia, musi być "CreateDatabase".      |
| offerThroughput | int  | Aprowizowana przepływność, który został ustawiony w bazie danych. Ten parametr jest opcjonalny. |

### <a name="output"></a>Dane wyjściowe

Zwraca odpowiedź polecenia niestandardowego domyślną. Zobacz [domyślne dane wyjściowe](#default-output) niestandardowe polecenia dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Tworzenie bazy danych**

Aby utworzyć bazę danych o nazwie "test", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Tworzenie bazy danych o przepływności**

Aby utworzyć bazę danych o nazwie "test" i aprowizowanej przepływności na poziomie 1000 jednostek RU, użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Aktualizuj bazę danych

Polecenia rozszerzenia aktualizacji bazy danych do aktualizacji właściwości skojarzone z określoną bazą danych. Obecnie można zaktualizować tylko właściwość "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

W poniższej tabeli opisano parametry w ramach polecenia:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| Akcja niestandardowa    |    string     |   Nazwa polecenia niestandardowego. Musi być "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nowe aprowizowana przepływność, który chcesz ustawić w bazie danych.    |

### <a name="output"></a>Dane wyjściowe

Zwraca odpowiedź polecenia niestandardowego domyślną. Zobacz [domyślne dane wyjściowe](#default-output) niestandardowe polecenia dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Aktualizacja aprowizowanej przepływności skojarzonego z bazą danych**

Aby zaktualizować aprowizowanej przepływności bazy danych przy użyciu nazwy "test", aby 1200 jednostek RU, użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Pobierz bazę danych

Polecenia rozszerzenia get bazy danych zwraca obiekt bazy danych. Nazwa bazy danych jest używana w kontekście bazy danych, względem którego polecenie jest wykonywane.

```
{
  customAction: "GetDatabase"
}
```

W poniższej tabeli opisano parametry w ramach polecenia:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  Akcja niestandardowa   |   string      |   Nazwa polecenia niestandardowego. Musi być "GetDatabase"|
        
### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, odpowiedzi zawiera dokument za pomocą następujących pól:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stan odpowiedzi. 1 == sukces. 0 == awarii.      |
| `database`    |    `string`        |   Nazwa bazy danych.      |
|   `provisionedThroughput`  |    `int`      |    Aprowizowana przepływność jest ustawiona na bazie danych. Jest to parametr opcjonalny odpowiedzi.     |

Jeśli polecenie zakończy się niepowodzeniem, zwracana jest odpowiedź polecenia niestandardowego domyślną. Zobacz [domyślne dane wyjściowe](#default-output) niestandardowe polecenia dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Pobierz bazy danych**

Aby uzyskać obiekt bazy danych dla bazy danych o nazwie "test", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Tworzenie kolekcji

Utwórz kolekcję rozszerzenia polecenie tworzy nową kolekcję usługi MongoDB. Nazwa bazy danych jest używana w kontekście bazy danych, względem którego polecenie jest wykonywane. Format polecenia CreateCollection jest następująca:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

W poniższej tabeli opisano parametry w ramach polecenia:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| Akcja niestandardowa    | string | Nazwa polecenia niestandardowego. Musi być "CreateCollection"     |
| kolekcja      | string | Nazwa kolekcji                                   |
| offerThroughput | int    | Aprowizowana przepływność można ustawić w bazie danych. Jest to parametr opcjonalny |
| shardKey        | string | Ścieżka klucza fragmentu do utworzenia kolekcji podzielonych na fragmenty. Jest to parametr opcjonalny |

### <a name="output"></a>Dane wyjściowe

Zwraca odpowiedź polecenia niestandardowego domyślną. Zobacz [domyślne dane wyjściowe](#default-output) niestandardowe polecenia dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Utwórz kolekcję unsharded**

Aby utworzyć kolekcję unsharded za pomocą nazwy "testCollection" i aprowizowanej przepływności na poziomie 1000 jednostek RU, użyj następującego polecenia: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Tworzenie kolekcji podzielonych na fragmenty**

Aby utworzyć podzielonej na fragmenty kolekcji przy użyciu nazwy "testCollection" i aprowizowanej przepływności na poziomie 1000 jednostek RU, użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Aktualizowanie kolekcji

Polecenia rozszerzenia kolekcji aktualizacji aktualizuje właściwości skojarzone z określonej kolekcji.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

W poniższej tabeli opisano parametry w ramach polecenia:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  Akcja niestandardowa   |   string      |   Nazwa polecenia niestandardowego. Musi być "UpdateCollection".      |
|  kolekcja   |   string      |   Nazwa kolekcji.       |
| offerThroughput   |int|   Aprowizowana przepływność można ustawić w kolekcji.|

## <a name="output"></a>Dane wyjściowe

Zwraca odpowiedź polecenia niestandardowego domyślną. Zobacz [domyślne dane wyjściowe](#default-output) niestandardowe polecenia dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Aktualizacja aprowizowanej przepływności skojarzonych z kolekcją**

Aby zaktualizować aprowizowana przepływność kolekcji o nazwie "testCollection" 1200 jednostek RU, użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Pobierz kolekcję

Polecenie niestandardowe kolekcji get zwraca obiekt kolekcji.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

W poniższej tabeli opisano parametry w ramach polecenia:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| Akcja niestandardowa    |   string      |   Nazwa polecenia niestandardowego. Musi być "GetCollection".      |
| kolekcja    |    string     |    Nazwa kolekcji.     |

### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, odpowiedzi zawiera dokument z następującymi polami


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 == sukces. 0 == awarii.      |
| `database`    |    `string`     |   Nazwa bazy danych.      |
| `collection`    |    `string`     |    Nazwa kolekcji.     |
|  `shardKeyDefinition`   |   `document`      |  Dokument specyfikacji indeksu używany jako klucza fragmentu. Jest to parametr opcjonalny odpowiedzi.       |
|  `provisionedThroughput`   |   `int`      |    Aprowizowana przepływność można ustawić w kolekcji. Jest to parametr opcjonalny odpowiedzi.     |

Jeśli polecenie zakończy się niepowodzeniem, zwracana jest odpowiedź polecenia niestandardowego domyślną. Zobacz [domyślne dane wyjściowe](#default-output) niestandardowe polecenia dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Pobierz kolekcję**

Aby uzyskać obiekt kolekcji dla kolekcji o nazwie "testCollection", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Domyślne dane wyjściowe polecenia niestandardowe

Jeśli nie zostanie określony, niestandardową odpowiedź zawiera dokument za pomocą następujących pól:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 == sukces. 0 == awarii.      |
| `code`    |   `int`      |   Zwracany tylko wtedy, gdy polecenie nie powiodło się (czyli ok == 0). Zawiera kod błędu bazy danych MongoDB. Jest to parametr opcjonalny odpowiedzi.      |
|  `errMsg`   |  `string`      |    Zwracany tylko wtedy, gdy polecenie nie powiodło się (czyli ok == 0). Zawiera komunikat o błędzie przyjazny dla użytkownika. Jest to parametr opcjonalny odpowiedzi.      |

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz przejść do następujących pojęcia usługi Azure Cosmos DB: 

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
