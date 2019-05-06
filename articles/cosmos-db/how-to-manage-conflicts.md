---
title: Dowiedz się, jak zarządzać konfliktami między regionami w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać konfliktami w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: a6e57dc5b4bcfa3f02e323253e24d68381c3535d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068734"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Zarządzanie zasadami rozwiązywania konfliktów w usłudze Azure Cosmos DB

Za pomocą zapisu w wielu regionach podczas zapisu wielu klientów do tego samego elementu konflikty mogą wystąpić. Gdy wystąpi konflikt, można rozwiązać konfliktu, za pomocą zasad rozpoznawania konfliktu z inną. W tym artykule opisano sposób zarządzania zasady rozwiązywania konfliktów.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Tworzenie zasad rozwiązywania konfliktów polegających na traktowaniu ostatniego zapisu jako prawidłowego

Poniższe przykłady pokazują, jak skonfigurować kontener za pomocą zasad rozwiązywania konfliktów polegających na traktowaniu ostatniego zapisu jako prawidłowego. Ścieżka domyślna ostatni składnik zapisywania usługi wins jest pole znacznika czasu lub `_ts` właściwości. Może to również ustawić do ścieżki zdefiniowanej dla typu liczbowego. W przypadku konfliktu najwyższą wartość wins. Jeśli nie ustawiono ścieżki lub jest nieprawidłowy, jego wartość domyślna to `_ts`. Konflikt został rozwiązany za pomocą tych zasad nie są wyświetlane w kanale informacyjnym konflikt. Ta zasada może służyć przez wszystkie interfejsy API.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java Sync SDK

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'LastWriterWins',
                    'conflictResolutionPath': '/myCustomId'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Tworzenie zasad rozwiązania konfliktu niestandardowych za pomocą procedury składowanej

Poniższe przykłady pokazują, jak skonfigurować kontener za pomocą niestandardowych zasad rozwiązywania konfliktów z procedurą składowaną, aby rozwiązać konflikt. Te konflikty nie są widoczne w kanale informacyjnym konfliktów, chyba że w procedurze składowanej wystąpi błąd. Po utworzeniu zasad z kontenerem, należy utworzyć procedurę składowaną. Przykładowy zestaw SDK platformy .NET, poniżej przedstawiono przykład. Ta zasada jest obsługiwany tylko na podstawowych (SQL) interfejsu Api.

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Przykładowe niestandardowe konfliktów procedury składowanej

Niestandardowe konflikt rozpoznawania przechowywane procedury musi można zaimplementować przy użyciu sygnatury funkcji, pokazano poniżej. Nazwa funkcji nie jest konieczne jest zgodna z nazwą używany podczas rejestrowania procedury składowanej z kontenerem, ale uprościć, nazewnictwa. Poniżej przedstawiono opis parametrów, które muszą zostać zaimplementowane dla tej procedury składowanej.

- **incomingItem**: Element wstawiany lub aktualizowany w zatwierdzeniu, które generuje konflikty. Ma wartość null dla operacji usuwania.
- **existingItem**: Element, który aktualnie zatwierdzone. Ta wartość jest inna niż null w aktualizacji i wartość null w przypadku wstawiania lub usuwa.
- **isTombstone**: Wartość logiczna wskazująca, jeśli incomingItem powoduje konflikt z wcześniej usuniętego elementu. W przypadku wartości true existingItem również ma wartość null.
- **conflictingItems**: Tablica zatwierdzonej wersji wszystkich elementów w kontenerze, które są w konflikcie z incomingItem w identyfikatorze lub innych właściwości unikatowego indeksu.

> [!IMPORTANT]
> Podobnie jak przy użyciu dowolnej procedury składowanej procedury rozwiązywania konfliktów niestandardowe mogą dostęp do danych za pomocą tego samego klucza partycji i mogą wykonywać żadnych insert, aktualizować lub usuwać operację, aby rozwiązać konflikty.


Tej próbki, przechowywane procedury rozwiązywania konfliktów przez wybranie najniższa wartość z `/myCustomId` ścieżki.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Po utworzeniu kontenera należy utworzyć procedurę składowaną `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java Sync SDK

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Po utworzeniu kontenera należy utworzyć procedurę składowaną `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Po utworzeniu kontenera należy utworzyć procedurę składowaną `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
  'id': self.udp_collection_name,
  'conflictResolutionPolicy': {
      'mode': 'Custom',
      'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
      }
  }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

Po utworzeniu kontenera należy utworzyć procedurę składowaną `resolver`.


## <a name="create-a-custom-conflict-resolution-policy"></a>Tworzenie niestandardowych zasad rozwiązywania konfliktów

Poniższe przykłady pokazują, jak skonfigurować kontener za pomocą niestandardowych zasad rozwiązywania konfliktów. Te konflikty są widoczne w kanale informacyjnym konfliktów.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Java Sync SDK

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
                    'id': self.manual_collection_name,
                    'conflictResolutionPolicy': {
                          'mode': 'Custom'
                        }
                    }
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Odczytywanie z kanału informacyjnego konfliktów

Te przykłady pokazują, jak odczytywać z kanału informacyjnego konfliktów kontenera. Konflikty wyświetlane w konflikcie, źródła danych tylko wtedy, gdy nie zostały rozwiązane automatycznie, lub jeśli za pomocą zasad niestandardowych konflikt.

### <a id="read-from-conflict-feed-dotnet"></a>.NET SDK

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-java-async"></a>Java Async SDK

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Java Sync SDK

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Następne kroki

Poznaj następujące pojęcia dotyczące usługi Azure Cosmos DB:

* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Jak skonfigurować Multi-Master w swoich aplikacjach](how-to-multi-master.md)
* [Konfigurowanie klientów w ramach wieloadresowości](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Dodać lub usunąć regiony z Twojego konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [How to configure multi-master in your applications (Jak skonfigurować wielowzorcowość w aplikacji)](how-to-multi-master.md).
* [Partycjonowanie i dystrybucja danych](partition-data.md)
* [Indeksowanie w usłudze Azure Cosmos DB](indexing-policies.md)