---
title: Tworzenie aplikacji Java przy użyciu zestawu Async Java SDK na potrzeby zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB | Microsoft Docs
description: W tym samouczku pokazano, jak używać konta interfejsu API SQL usługi Azure Cosmos DB do przechowywania danych oraz uzyskiwania do nich dostępu za pomocą aplikacji asynchronicznego języka Java.
keywords: nosql tutorial, online database, java console application
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: 66e937e92528e2f0a1fca9d9aac78f7265eef4f7
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741236"
---
# <a name="tutorial-build-a-java-app-with-async-java-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Samouczek: tworzenie aplikacji Java przy użyciu zestawu Async Java SDK na potrzeby zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java (asynchroniczny)](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

W tym samouczku pokazano, jak utworzyć aplikację Java przy użyciu zestawu Async Java SDK na potrzeby zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
> * Konfigurowanie rozwiązania
> * Tworzenie kolekcji
> * Tworzenie dokumentów JSON
> * Wykonywanie zapytań względem kolekcji

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że masz następujące zasoby:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Zestaw Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB
Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Klonowanie projektu GitHub](#GitClone). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do kroku [Klonowanie projektu GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Krok 2. Klonowanie repozytorium GitHub

Możesz rozpocząć od sklonowania repozytorium GitHub na potrzeby pracy z tematem [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) (Rozpoczynanie pracy z usługą Azure Cosmos DB i językiem Java). Na przykład z katalogu lokalnego uruchom następujące polecenie, aby lokalnie pobrać przykładowy projekt.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Katalog zawiera plik `pom.xml` i folder `src/main/java/com/microsoft/azure/cosmosdb/sample`, w którym znajduje się kod źródłowy Java, w tym plik `Main.java`. Projekt zawiera kod wymagany do wykonania operacji za pomocą usługi Azure Cosmos DB takich jak tworzenie dokumentów oraz wykonywanie zapytań względem danych w obrębie kolekcji. Plik `pom.xml` zawiera zależność dla [zestawu Java SDK usługi Azure Cosmos DB w Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB

Następnie wróć do witryny [Azure Portal](https://portal.azure.com), aby pobrać punkt końcowy i główny klucz podstawowy. Klucz podstawowy i punkt końcowy usługi Azure Cosmos DB są niezbędne, aby aplikacja wiedziała, z jakim elementem ma się połączyć, oraz aby usługa Azure Cosmos DB ufała połączeniu aplikacji. Plik `AccountSettings.java` zawiera klucz podstawowy i wartości identyfikatora URI. 

W witrynie Azure Portal przejdź do swojego konta usługi Azure Cosmos DB i kliknij pozycję **Klucze**. Skopiuj identyfikator URI i KLUCZ PODSTAWOWY z portalu i wklej go do pliku `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Zrzut ekranu przedstawiający pobieranie kluczy][keys]

## <a name="step-4-initialize-the-client-object"></a>Krok 4. Inicjowanie obiektu klienta

Zainicjuj obiekt klienta za pomocą wartości klucza podstawowego i identyfikatora URI hosta zdefiniowanych w pliku „AccountSettings.java”

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Krok 5. Tworzenie bazy danych

[Bazę danych](sql-api-resources.md#databases) usługi Azure Cosmos DB można utworzyć za pomocą metody createDatabaseIfNotExists() klasy DocumentClient. Baza danych jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Krok 6. Tworzenie kolekcji

> [!WARNING]
> Metoda **createCollection** tworzy nową kolekcję z zarezerwowaną przepływnością, co ma wpływ na cenę. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 
Kolekcję można utworzyć za pomocą metody createDocumentCollectionIfNotExists() klasy DocumentClient. Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript.

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Krok 7. Tworzenie dokumentów JSON

[Dokument](sql-api-resources.md#documents) można utworzyć za pomocą metody createDocument klasy DocumentClient. Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Można teraz wstawić jeden lub więcej dokumentów. Plik „src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java” definiuje dokumenty JSON rodziny 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Krok 8. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB

Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji. Następujący przykładowy kod przedstawia sposób wykonania zapytania względem dokumentów w usłudze Azure Cosmos DB przy użyciu składni SQL za pomocą metody [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments).

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Krok 9. Uruchamianie aplikacji konsolowej Java

Aby uruchomić aplikację z poziomu konsoli, przejdź do folderu projektu i przeprowadź kompilację za pomocą narzędzia Maven:

```bash
mvn package
```

Uruchomienie polecenia `mvn package` spowoduje pobranie najnowszej biblioteki Azure Cosmos DB z narzędzia Maven i utworzenie pliku `GetStarted-0.0.1-SNAPSHOT.jar`. Następnie można uruchomić aplikację za pomocą następującego polecenia:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Gratulacje! Pomyślnie ukończono ten samouczek NoSQL i utworzono działającą aplikację konsolową Java!

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje na temat tworzenia aplikacji Java przy użyciu zestawu Async Java SDK na potrzeby zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB. Teraz możesz przejść do następnego artykułu:

> [!div class="nextstepaction"]
> [Tworzenie aplikacji konsolowej Node.js przy użyciu zestawu SDK języka JavaScript i usługi Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
