---
title: 'Samouczek NoSQL: interfejsu API SQL dla usługi Azure rozwiązania Cosmos DB Java SDK | Dokumentacja firmy Microsoft'
description: Samouczek NoSQL, który powoduje utworzenie bazy danych w trybie online i aplikacji konsoli w języku Java za pomocą interfejsu API SQL dla bazy danych Azure rozwiązania Cosmos. Azure SQL jest bazą danych NoSQL dla formatu JSON.
keywords: nosql tutorial, online database, java console application
services: cosmos-db
documentationcenter: Java
author: SnehaGunda
manager: kfile
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/22/2017
ms.author: sngun
ms.openlocfilehash: 8405894451c60f0661922584d9927960e3d0f611
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Samouczek NoSQL: tworzenie aplikacji konsoli Java interfejsu API SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js dla MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Samouczek NoSQL dla interfejsu API SQL dla Zapraszamy Azure rozwiązania Cosmos DB Java SDK! W ramach tego samouczka zostanie utworzona aplikacja konsolowa, która tworzy zasoby usługi Azure Cosmos DB i wykonuje dla nich zapytania.

Zostaną opisane:

* Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
* Konfigurowanie rozwiązania Visual Studio
* Tworzenie bazy danych w trybie online
* Tworzenie kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Zastępowanie dokumentu
* Usuwanie dokumentu
* Usuwanie bazy danych

Teraz do dzieła!

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Zestaw Java Development Kit (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB
Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Klonowanie projektu GitHub](#GitClone). Jeśli używasz emulatora usługi Azure Cosmos DB, wykonaj czynności opisane w temacie [Emulator usługi Azure Cosmos DB](local-emulator.md), aby skonfigurować emulator, a następnie przejdź do kroku [Klonowanie projektu GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Krok 2. Klonowanie projektu GitHub
Możesz rozpocząć od sklonowania repozytorium GitHub na potrzeby pracy z tematem [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/documentdb-java-getting-started) (Rozpoczynanie pracy z usługą Azure Cosmos DB i językiem Java). Na przykład z katalogu lokalnego uruchom następujące polecenie, aby lokalnie pobrać przykładowy projekt.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Katalog zawiera `pom.xml` dla projektu i `src` folder zawierający kod źródłowy Java w tym `Program.java` które pokazuje sposób wykonywaniem prostych operacji dotyczących bazy danych rozwiązania Cosmos platformy Azure, takich jak tworzenie dokumentów i wykonywanie zapytania na danych w ramach kolekcji . `pom.xml` Zawiera zależności na [Azure rozwiązania Cosmos DB Java SDK na Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Krok 3. Łączenie się z kontem usługi Azure Cosmos DB
Następnie wróć do witryny [Azure Portal](https://portal.azure.com), aby pobrać punkt końcowy i główny klucz podstawowy. Klucz podstawowy i punkt końcowy usługi Azure Cosmos DB są niezbędne, aby aplikacja wiedziała, z jakim elementem ma się połączyć, oraz aby usługa Azure Cosmos DB ufała połączeniu aplikacji.

W witrynie Azure Portal przejdź do swojego konta usługi Azure Cosmos DB i kliknij pozycję **Klucze**. Skopiuj identyfikator URI z portalu i wklej go w miejsce `https://FILLME.documents.azure.com` w pliku Program.java. Następnie skopiuj KLUCZ PODSTAWOWY z portalu i wklej go w miejsce `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Zrzut ekranu przedstawiający witrynę Azure Portal używaną przez samouczek NoSQL do tworzenia aplikacji konsolowej Java. Przedstawia konto usługi Azure Cosmos DB z wyróżnionym AKTYWNYM centrum, przyciskiem KLUCZE wyróżnionym w bloku konta usługi Azure Cosmos DB oraz wartościami IDENTYFIKATOR URI, KLUCZ PODSTAWOWY i KLUCZ POMOCNICZY wyróżnionymi w bloku Klucze][keys]

## <a name="step-4-create-a-database"></a>Krok 4. Tworzenie bazy danych
Własną [bazę danych](sql-api-resources.md#databases) usługi Azure Cosmos DB można utworzyć za pomocą metody [createDatabase](/java/api/com.microsoft.azure.documentdb._document_client.createdatabase) klasy **DocumentClient**. Baza danych jest kontenerem logicznym magazynu dokumentów JSON podzielonym na partycje w kolekcjach.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Krok 5. Tworzenie kolekcji
> [!WARNING]
> Metoda **createCollection** tworzy nową kolekcję z zarezerwowaną przepływnością, co ma wpływ na cenę. Aby uzyskać więcej informacji, odwiedź naszą [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

[Kolekcję](sql-api-resources.md#collections) można utworzyć za pomocą metody [createCollection](/java/api/com.microsoft.azure.documentdb._document_client.createcollection) klasy **DocumentClient**. Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Krok 6. Tworzenie dokumentów JSON
[Dokument](sql-api-resources.md#documents) można utworzyć za pomocą metody [createDocument](/java/api/com.microsoft.azure.documentdb._document_client.createdocument) klasy **DocumentClient**. Dokumenty są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Można teraz wstawić jeden lub więcej dokumentów. Jeśli masz już dane, które chcesz przechowywać w bazie danych, możesz użyć Azure rozwiązania Cosmos DB [narzędzia migracji danych](import-data.md) do importowania danych do bazy danych.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagram pokazujący hierarchiczną relację między kontem, bazą danych w trybie online, kolekcją i dokumentami używanymi przez samouczek NoSQL do tworzenia aplikacji konsolowej Java](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Krok 7. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje zaawansowane [zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji.  Następujący przykładowy kod przedstawia sposób wykonania zapytania względem dokumentów w usłudze Azure Cosmos DB przy użyciu składni SQL za pomocą metody [queryDocuments](/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Krok 8. Zastępowanie dokumentu JSON
Usługa Azure Cosmos DB obsługuje aktualizowanie dokumentów JSON za pomocą metody [replaceDocument](/java/api/com.microsoft.azure.documentdb._document_client.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Krok 9. Usuwanie dokumentu JSON
Podobnie usługa Azure Cosmos DB obsługuje usuwanie dokumentów JSON za pomocą metody [deleteDocument](/java/api/com.microsoft.azure.documentdb._document_client.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Krok 10. Usuwanie bazy danych
Usunięcie utworzonej bazy danych usunie bazę danych i wszystkie zasoby podrzędne (kolekcje, dokumenty itd.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Krok 11. Uruchamianie całej aplikacji konsolowej Java!
Aby uruchomić aplikację z poziomu konsoli, przejdź do folderu projektu i skompilować za pomocą programu Maven:
    
    mvn package

Uruchomienie polecenia `mvn package` spowoduje pobranie najnowszej biblioteki Azure Cosmos DB z narzędzia Maven i utworzenie pliku `GetStarted-0.0.1-SNAPSHOT.jar`. Następnie można uruchomić aplikację za pomocą następującego polecenia:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Gratulacje! Pomyślnie ukończono ten samouczek NoSQL i utworzono działającą aplikację konsolową Java!

## <a name="next-steps"></a>Kolejne kroki
* Czy chcesz samouczek aplikacji sieci Web w języku Java? Zobacz [Build a web application with Java using Azure Cosmos DB](sql-api-java-application.md) (Tworzenie aplikacji internetowej w języku Java przy użyciu usługi Azure Cosmos DB).
* Dowiedz się, jak [monitorować konto usługi Azure Cosmos DB](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
