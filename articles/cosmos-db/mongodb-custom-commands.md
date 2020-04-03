---
title: Polecenia rozszerzenia MongoDB do zarządzania danymi w interfejsie API usługi Azure Cosmos DB dla mongodb
description: W tym artykule opisano sposób używania poleceń rozszerzenia MongoDB do zarządzania danymi przechowywanymi w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583578"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Polecenia rozszerzenia MongoDB do zarządzania danymi przechowywanymi w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB 

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB można komunikować się przy użyciu dowolnego [sterownika klienta mongodb](https://docs.mongodb.org/ecosystem/drivers)typu open source. Interfejs API usługi Azure Cosmos DB dla mongodb umożliwia korzystanie z istniejących sterowników klienta, stosując się do [protokołu przewodowego MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Korzystając z interfejsu API usługi Azure Cosmos DB dla mongodb, można korzystać z korzyści usługi Cosmos DB, takich jak dystrybucja globalna, automatyczne dzielenie na fragmenty, wysoka dostępność, gwarancje opóźnienia, automatyczne, szyfrowanie w spoczynku, kopie zapasowe i wiele innych, przy jednoczesnym zachowaniu inwestycji w aplikacji MongoDB.

## <a name="mongodb-protocol-support"></a>Obsługa protokołu MongoDB

Domyślnie interfejs API usługi Azure Cosmos DB dla mongodb jest zgodny z serwerem MongoDB w wersji 3.2, aby uzyskać więcej informacji, zobacz [obsługiwane funkcje i składnię](mongodb-feature-support.md). Funkcje lub operatory zapytań dodane w mongodb w wersji 3.4 są obecnie dostępne jako wersja zapoznawcza w interfejsie API usługi Azure Cosmos DB dla mongodb. Następujące polecenia rozszerzenia obsługują określone funkcje usługi Azure Cosmos DB podczas wykonywania operacji CRUD na danych przechowywanych w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB:

* [Tworzenie bazy danych](#create-database)
* [Aktualizuj bazę danych](#update-database)
* [Pobierz bazę danych](#get-database)
* [Tworzenie kolekcji](#create-collection)
* [Aktualizuj kolekcję](#update-collection)
* [Pobierz kolekcję](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Tworzenie bazy danych

Polecenie utwórz rozszerzenie bazy danych tworzy nową bazę danych MongoDB. Nazwa bazy danych jest używana z kontekstu baz danych, dla którego polecenie jest wykonywane. Format polecenia CreateDatabase jest następujący:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| customAction (akcją)   |  ciąg  |   Nazwa polecenia niestandardowego musi być "CreateDatabase".      |
| ofertaWyjęcie | int  | Aprowizowana przepływność ustawiona w bazie danych. Ten parametr jest opcjonalny. |

### <a name="output"></a>Dane wyjściowe

Zwraca domyślną niestandardową odpowiedź polecenia. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Tworzenie bazy danych**

Aby utworzyć bazę danych o nazwie "test", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Tworzenie bazy danych z przepływnością**

Aby utworzyć bazę danych o nazwie "test" i aprowizowaną przepływność 1000 procesorów RU, należy użyć następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Aktualizuj bazę danych

Polecenie rozszerzenia bazy danych aktualizacji aktualizuje właściwości skojarzone z określoną bazą danych. Obecnie można zaktualizować tylko "offerThroughput" właściwość.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| customAction (akcją)    |    ciąg     |   Nazwa polecenia niestandardowego. Musi być "UpdateDatabase".      |
|  ofertaWyjęcie   |  int       |     Nowa aprowizowana przepływność, którą chcesz ustawić w bazie danych.    |

### <a name="output"></a>Dane wyjściowe

Zwraca domyślną niestandardową odpowiedź polecenia. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Aktualizowanie aprowizowanej przepływności skojarzonej z bazą danych**

Aby zaktualizować aprowizowaną przepływność bazy danych o nazwie "test" do 1200 rUs, należy użyć następującego polecenia:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Pobierz bazę danych

Polecenie Get extension zwraca obiekt bazy danych. Nazwa bazy danych jest używana z kontekstu bazy danych, względem którego wykonywane jest polecenie.

```
{
  customAction: "GetDatabase"
}
```

W poniższej tabeli opisano parametry w poleceniu:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  customAction (akcją)   |   ciąg      |   Nazwa polecenia niestandardowego. Musi być "GetDatabase"|
        
### <a name="output"></a>Dane wyjściowe

Jeśli polecenie powiedzie się, odpowiedź zawiera dokument z następującymi polami:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stan odpowiedzi. 1 == sukces. 0 == awaria.      |
| `database`    |    `string`        |   Nazwa bazy danych.      |
|   `provisionedThroughput`  |    `int`      |    Aprowizowana przepływność ustawiona w bazie danych. Jest to opcjonalny parametr odpowiedzi.     |

Jeśli polecenie nie powiedzie się, zwracana jest domyślna odpowiedź polecenia niestandardowego. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Pobierz bazę danych**

Aby uzyskać obiekt bazy danych dla bazy danych o nazwie "test", użyj następującego polecenia:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Tworzenie kolekcji

Polecenie utwórz rozszerzenie kolekcji tworzy nową kolekcję MongoDB. Nazwa bazy danych jest używana z kontekstu baz danych, dla którego polecenie jest wykonywane. Format polecenia CreateCollection jest następujący:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

W poniższej tabeli opisano parametry w poleceniu:

| **Pole** | **Typ** | **Wymagane** | **Opis** |
|---------|---------|---------|---------|
| customAction (akcją) | ciąg | Wymagany | Nazwa polecenia niestandardowego. Musi być "CreateCollection".|
|  — kolekcja | ciąg | Wymagany | Nazwa kolekcji. Znaki specjalne nie są dozwolone.|
| ofertaWyjęcie | int | Opcjonalnie* | Aprowizowana przepływność do skonfigurowania w bazie danych. Jeśli ten parametr nie jest podany, domyślnie będzie minimalny, 400 RU/s. * Aby określić przepływność powyżej 10 000 RU/s, `shardKey` wymagany jest parametr.|
| shardKey | ciąg | Opcjonalnie* | Ścieżka do klucza niezależnego fragmentu dla kolekcji podzielonej na fragmenty. Ten parametr jest wymagany, jeśli ustawisz więcej niż 10 `offerThroughput`000 RU/s w .  Jeśli zostanie określony, wszystkie wstawione dokumenty będą wymagać tej wartości. |

### <a name="output"></a>Dane wyjściowe

Zwraca domyślną niestandardową odpowiedź polecenia. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Tworzenie nierozgnieńczonej kolekcji**

Aby utworzyć niezgiełkioną kolekcję o nazwie "testCollection" i aprowizowaną przepływność 1000 procesorów RU, należy użyć następującego polecenia: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Tworzenie kolekcji podzielonej na fragmenty**

Aby utworzyć kolekcję podzieloną na fragmenty o nazwie "testCollection" i aprowizowaną przepływność 1000 procesorów RU i właściwość shardkey "a.b", należy użyć następującego polecenia:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Aktualizuj kolekcję

Polecenie rozszerzenia kolekcji aktualizacji aktualizuje właściwości skojarzone z określoną kolekcją.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

W poniższej tabeli opisano parametry w poleceniu:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  customAction (akcją)   |   ciąg      |   Nazwa polecenia niestandardowego. Musi być "UpdateCollection".      |
|   — kolekcja   |   ciąg      |   Nazwa kolekcji.       |
| ofertaWyjęcie   |int|   Aprowizowana przepływność do ustawionego w kolekcji.|

## <a name="output"></a>Dane wyjściowe

Zwraca domyślną niestandardową odpowiedź polecenia. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Aktualizowanie aprowizowanej przepływności skojarzonej z kolekcją**

Aby zaktualizować aprowizowaną przepływność kolekcji o nazwie "testCollection" do 1200 rUs, należy użyć następującego polecenia:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Pobierz kolekcję

Polecenie get collection custom zwraca obiekt kolekcji.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

W poniższej tabeli opisano parametry w poleceniu:


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
| customAction (akcją)    |   ciąg      |   Nazwa polecenia niestandardowego. Musi być "GetCollection".      |
|  — kolekcja    |    ciąg     |    Nazwa kolekcji.     |

### <a name="output"></a>Dane wyjściowe

Jeśli polecenie zakończy się pomyślnie, odpowiedź zawiera dokument z następującymi polami


|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 == sukces. 0 == awaria.      |
| `database`    |    `string`     |   Nazwa bazy danych.      |
| `collection`    |    `string`     |    Nazwa kolekcji.     |
|  `shardKeyDefinition`   |   `document`      |  Dokument specyfikacji indeksu używany jako klucz niezależnego fragmentu. Jest to opcjonalny parametr odpowiedzi.       |
|  `provisionedThroughput`   |   `int`      |    Aprowizowana przepływność do ustawionego w kolekcji. Jest to opcjonalny parametr odpowiedzi.     |

Jeśli polecenie nie powiedzie się, zwracana jest domyślna odpowiedź polecenia niestandardowego. Zobacz [domyślne dane wyjściowe](#default-output) polecenia niestandardowego dla parametrów w danych wyjściowych.

### <a name="examples"></a>Przykłady

**Pobierz kolekcję**

Aby uzyskać obiekt kolekcji dla kolekcji o nazwie "testCollection", należy użyć następującego polecenia:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Domyślne dane wyjściowe polecenia niestandardowego

Jeśli nie zostanie określona, odpowiedź niestandardowa zawiera dokument z następującymi polami:

|**Pole**|**Typ** |**Opis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stan odpowiedzi. 1 == sukces. 0 == awaria.      |
| `code`    |   `int`      |   Zwracane tylko wtedy, gdy polecenie nie powiodło się (tj. ok == 0). Zawiera kod błędu MongoDB. Jest to opcjonalny parametr odpowiedzi.      |
|  `errMsg`   |  `string`      |    Zwracane tylko wtedy, gdy polecenie nie powiodło się (tj. ok == 0). Zawiera przyjazny dla użytkownika komunikat o błędzie. Jest to opcjonalny parametr odpowiedzi.      |

## <a name="next-steps"></a>Następne kroki

Następnie możesz przejść do zapoznania się z następującymi pojęciami usługi Azure Cosmos DB: 

* [Indeksowanie w usłudze Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md) (Automatyczne wygasanie danych w usłudze Azure Cosmos DB przy użyciu czasu wygaśnięcia)
