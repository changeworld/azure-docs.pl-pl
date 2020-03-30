---
title: Migrowanie z usługi CouchBase do interfejsu API SQL usługi Azure Cosmos DB
description: Wskazówki krok po kroku dotyczące migracji z usługi CouchBase do interfejsu API SQL usługi Azure Cosmos DB
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210946"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrowanie z usługi CouchBase do interfejsu API SQL usługi Azure Cosmos DB

Usługa Azure Cosmos DB to skalowalna, globalnie rozproszona, w pełni zarządzana baza danych. Zapewnia gwarantowany dostęp do danych o małym opóźnieniu. Aby dowiedzieć się więcej o usłudze Azure Cosmos DB, zobacz [artykuł omówienie.](introduction.md) Ten artykuł zawiera instrukcje migracji aplikacji Java, które są połączone z Couchbase do konta interfejsu API SQL w usłudze Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Różnice w nomenklaturze

Poniżej przedstawiono kluczowe funkcje, które działają inaczej w usłudze Azure Cosmos DB w porównaniu z Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Serwer Couchbase| Konto       |
|Wiadro           | baza danych      |
|Wiadro           | Kontener/Kolekcja |
|Dokument JSON    | Pozycja / Dokument |

## <a name="key-differences"></a>Podstawowe różnice

* Usługa Azure Cosmos DB ma pole "ID" w dokumencie, podczas gdy Couchbase ma identyfikator jako część zasobnika. Pole "Identyfikator" jest unikatowe na całej partycji.

* Usługa Azure Cosmos DB skaluje się przy użyciu techniki partycjonowania lub dzielenia na fragmenty. Oznacza to, że dzieli dane na wiele fragmentów/partycji. Te partycje/fragmenty są tworzone na podstawie właściwości klucza partycji, które można podać. Można wybrać klucz partycji, aby zoptymalizować odczyt, jak również operacje zapisu lub odczytu / zapisu zoptymalizowane zbyt. Aby dowiedzieć się więcej, zobacz artykuł [partycjonowania.](./partition-data.md)

* W usłudze Azure Cosmos DB nie jest wymagane dla hierarchii najwyższego poziomu, aby oznaczyć kolekcji, ponieważ nazwa kolekcji już istnieje. Ta funkcja znacznie upraszcza strukturę JSON. Oto przykład, który pokazuje różnice w modelu danych między Couchbase i Usługi Azure Cosmos DB:

   **Baza kanapowa**: Identyfikator dokumentu = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Usługa Azure Cosmos DB**: Refer "ID" w dokumencie, jak pokazano poniżej

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Obsługa sdk Java SDK

Usługa Azure Cosmos DB ma następujące zestawy SDK do obsługi różnych struktur java:

* Async SDK
* SDK z rozruchu sprężynowego

W poniższych sekcjach opisano, kiedy należy używać każdego z tych SDK. Rozważmy przykład, w którym mamy trzy typy obciążeń:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase jako repozytorium dokumentów & wiosennych kwerend niestandardowych opartych na danych

Jeśli migrowane obciążenie jest oparte na sdk opartym na wiosennym rozruchu, można wykonać następujące kroki:

1. Dodaj element nadrzędny do pliku POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Dodawanie właściwości do pliku POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Dodawanie zależności do pliku POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Dodaj właściwości aplikacji w obszarze zasoby i określ następujące elementy. Pamiętaj, aby zastąpić parametry adresu URL, klucza i nazwy bazy danych:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Zdefiniuj nazwę kolekcji w modelu. Można również określić dalsze adnotacje. Na przykład identyfikator, klucz partycji, aby oznaczyć je jawnie:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Poniżej przedstawiono fragmenty kodu dla operacji CRUD:

### <a name="insert-and-update-operations"></a>Wstawianie i aktualizowanie operacji

Gdzie *_repo* jest obiektem repozytorium i *doc* jest obiektem klasy POJO. Można użyć `.save` do wstawienia lub upsert (jeśli znaleziono dokument o określonym identyfikatorze). Poniższy fragment kodu pokazuje, jak wstawić lub zaktualizować obiekt doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operacja usuwania

Należy wziąć pod uwagę następujący fragment kodu, gdzie obiekt doc będzie miał identyfikator i klucz partycji obowiązkowe do zlokalizowania i usunięcia obiektu:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operacja odczytu

Można odczytać dokument z lub bez określania klucza partycji. Jeśli nie określisz klucz partycji, a następnie jest traktowany jako kwerendy między partycjami. Należy wziąć pod uwagę następujące przykłady kodu, pierwszy z nich wykona operację przy użyciu pola klucza identyfikatora i partycji. Drugi przykład używa zwykłego pola & bez określania pola klucza partycji.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

To wszystko, można teraz używać aplikacji z usługi Azure Cosmos DB. Kompletny przykład kodu dla przykładu opisanego w tym doc jest dostępny w [couchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub repozytorium.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase jako repozytorium dokumentów & przy użyciu zapytań N1QL

Kwerendy N1QL to sposób definiowania zapytań w bazie couchbase.

|Zapytanie N1QL | Zapytanie usługi Azure CosmosDB|
|-------------------|-------------------|
|WYBIERZ META(`TravelDocument`.id AS `TravelDocument`id, .* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xxx.xxx.xxxx " i kraj = 'India' i ANY m in Visas SATISFIES m.type == 'Multi-Entry' i m.Country IN ['India', Bhutan'] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0   | WYBIERZ c.id,c FROM c JOIN m in c.country='India' WHERE c._type = " com.xx.xx.xxx.xxx.xxxx" i c.country = 'India' and m.type = 'Multi-Entry' i m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

Można zauważyć następujące zmiany w zapytaniach N1QL:

* Nie musisz używać słowa kluczowego META ani odwoływać się do dokumentu pierwszego poziomu. Zamiast tego można utworzyć własne odwołanie do kontenera. W tym przykładzie uznaliśmy go za "c" (może to być wszystko). To odwołanie jest używane jako prefiks dla wszystkich pól pierwszego poziomu. Przykład, c.id, c.country itp.

* Zamiast "ANY" teraz można zrobić sprzężenie w poddokumentu i odnoszą się do niego z dedykowanym aliasem, takich jak "m". Po utworzeniu aliasu dla dokumentu podrzędnego należy użyć aliasu. Na przykład m.Country.

* Sekwencja OFFSET jest inna w zapytaniu usługi Azure Cosmos DB, najpierw należy określić OFFSET, a następnie LIMIT. Zaleca się, aby nie używać spring data SDK, jeśli używasz maksymalnej kwerendy zdefiniowane niestandardowe, ponieważ może mieć niepotrzebne obciążenie po stronie klienta podczas przekazywania kwerendy do usługi Azure Cosmos DB. Zamiast tego mamy bezpośredni Async Java SDK, który może być wykorzystany znacznie skutecznie w tym przypadku.

### <a name="read-operation"></a>Operacja odczytu

Użyj asynchronii Java SDK z następującymi krokami:

1. Skonfiguruj następującą zależność w pliku POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Utwórz obiekt połączenia dla usługi Azure `ConnectionBuilder` Cosmos DB przy użyciu metody, jak pokazano w poniższym przykładzie. Upewnij się, że ta deklaracja jest umieszczana w fasoli w taki sposób, aby następujący kod został wykonany tylko raz:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Aby wykonać kwerendę, należy uruchomić następujący fragment kodu:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Teraz, za pomocą powyższej metody można przekazać wiele zapytań i wykonać bez żadnych kłopotów. W przypadku, gdy masz wymóg wykonania jednej dużej kwerendy, które można podzielić na wiele zapytań, spróbuj wykonać następujący fragment kodu zamiast poprzedniego:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Za pomocą poprzedniego kodu można uruchamiać kwerendy równolegle i zwiększyć rozproszone wykonania w celu optymalizacji. Ponadto można uruchomić operacje wstawiania i aktualizacji:

### <a name="insert-operation"></a>Operacja wstawiania

Aby wstawić dokument, uruchom następujący kod:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Następnie zapisz się do Mono jako:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Operacja upsert

Operacja upsert wymaga określenia dokumentu, który wymaga aktualizacji. Aby pobrać pełny dokument, można użyć fragmentu kodu wymienionego w operacji odczytu nagłówka, a następnie zmodyfikować wymagane pola. Następujący fragment kodu upserts dokumentu:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Następnie zapisz się do mono. Zapoznaj się z fragmentem subskrypcji mono w operacji wstawiania.

### <a name="delete-operation"></a>Operacja usuwania

Następujący fragment kodu wykona operację usuwania:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Następnie subskrybuj mono, odnoszą się fragment subskrypcji mono w operacji wstawiania. Kompletny przykład kodu jest dostępny w repozytorium GitHub [CouchbaseToCosmosDB-AsyncInSpring.](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring)

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase jako para klucz/wartość

Jest to prosty typ obciążenia, w którym można wykonywać wyszukiwania zamiast zapytań. Użyj następujących kroków dla par kluczy/wartości:

1. Należy rozważyć "/ID" jako klucz podstawowy, który zapewni, że można wykonać operację odnośnika bezpośrednio w określonej partycji. Utwórz kolekcję i określ "/ID" jako klucz partycji.

1. Całkowicie wyłączyć indeksowanie. Ponieważ operacje odnośniania będą wykonywane, nie ma sensu przenoszenia narzutów indeksowania. Aby wyłączyć indeksowanie, zaloguj się do witryny Azure portal, goto Usługi Azure Cosmos DB Account. Otwórz **Eksploratora danych**, wybierz **bazę danych** i **kontener**. Otwórz kartę **Ustawienia & skali** i wybierz pozycję Zasady **indeksowania**. Obecnie zasady indeksowania wygląda następująco:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Zastąp powyższe zasady indeksowania następującymi zasadami:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Użyj następującego fragmentu kodu, aby utworzyć obiekt połączenia. Obiekt połączenia (do @Bean umieszczenia lub uczynienia go statycznym):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Teraz można wykonać operacje CRUD w następujący sposób:

### <a name="read-operation"></a>Operacja odczytu

Aby odczytać element, użyj następującego fragmentu kodu:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Operacja wstawiania

Aby wstawić element, można wykonać następujący kod:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Następnie zapisz się do mono jako:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Operacja upsert

Aby zaktualizować wartość elementu, zapoznaj się z poniższym fragmentem kodu:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Następnie subskrybuj mono, odnoszą się fragment subskrypcji mono w operacji wstawiania.

### <a name="delete-operation"></a>Operacja usuwania

Aby wykonać operację usuwania, użyj następującego fragmentu kodu:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Następnie subskrybuj mono, odnoszą się fragment subskrypcji mono w operacji wstawiania. Kompletny przykład kodu jest dostępny w repozytorium [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub.

## <a name="data-migration"></a>Migrowanie danych

Istnieją dwa sposoby migracji danych.

* **Użyj usługi Azure Data Factory:** Jest to najbardziej zalecana metoda migracji danych. Skonfiguruj źródło jako Couchbase i sink jako interfejs API SQL usługi Azure Cosmos DB, zobacz artykuł łącznika usługi Azure [Cosmos DB Data Factory, aby](../data-factory/connector-azure-cosmos-db.md) uzyskać szczegółowe kroki.

* **Użyj narzędzia do importowania danych usługi Azure Cosmos DB:** Ta opcja jest zalecana do migracji przy użyciu maszyn wirtualnych z mniejszą ilością danych. Aby uzyskać szczegółowe kroki, zobacz artykuł [Importer danych.](./import-data.md)

## <a name="next-steps"></a>Następne kroki

* Aby wykonać testowanie wydajności, zobacz [testowanie wydajności i skalowania za pomocą usługi Azure Cosmos DB](./performance-testing.md) w artykule.
* Aby zoptymalizować kod, zobacz [porady dotyczące wydajności usługi Azure Cosmos DB](./performance-tips-async-java.md) artykułu.
* Zapoznaj się z interfejsem SDK Java Async V3, [odwołanieM do pliku SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub.
