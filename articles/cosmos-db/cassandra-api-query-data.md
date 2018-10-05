---
title: Wykonywanie zapytań dotyczących danych z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB
description: W tym artykule przedstawiono sposób wykonywania zapytań o dane użytkownika z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB przy użyciu aplikacji języka Java.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: c1fb4c27f897e3c0952ed6419e167613ac8204f7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223495"
---
# <a name="query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Wykonywanie zapytań dotyczących danych z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB

W tym samouczku przedstawiono sposób wykonywania zapytań o dane użytkownika z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB przy użyciu aplikacji języka Java. Aplikacja języka Java używa [sterownika Java](https://github.com/datastax/java-driver) i wykonuje zapytania o dane użytkownika, takie jak identyfikator użytkownika, nazwa użytkownika i miasto użytkownika. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Wykonywanie zapytań dotyczących danych z tabeli bazy danych Cassandra
> * Uruchamianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* Ten artykuł należy do wieloczęściowego samouczka. Przed rozpoczęciem pamiętaj o ukończeniu poprzednich kroków [tworzenia konta, przestrzeni kluczy i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) oraz [ładowania przykładowych danych do tabeli](cassandra-api-load-data.md). 

## <a name="query-data"></a>Zapytania o dane

Otwórz plik `UserRepository.java` w folderze `src\main\java\com\azure\cosmosdb\cassandra`. Dodaj poniższy blok kodu. Ten kod zawiera trzy funkcje: wykonywania zapytań dotyczących wszystkich użytkowników w bazie danych, wykonywania zapytań dotyczących określonego użytkownika według jego identyfikatora oraz usuwania tabeli. 

```java
/**
* Select all rows from user table
*/
public void selectAllUsers() {

    final String query = "SELECT * FROM uprofile.user";
    List<Row> rows = session.execute(query).all();

    for (Row row : rows) {
       LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
}

/**
* Select a row from user table
*
* @param id user_id
*/
public void selectUser(int id) {
    final String query = "SELECT * FROM uprofile.user where user_id = 3";
    Row row = session.execute(query).one();

    LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
}

/**
* Delete user table.
*/
public void deleteTable() {
   final String query = "DROP TABLE IF EXISTS uprofile.user";
   session.execute(query);
}
```

Otwórz plik `UserProfile.java` w folderze `src\main\java\com\azure\cosmosdb\cassandra`. Ta klasa zawiera metodę główną, która wywołuje zdefiniowane wcześniej metody wstawiania danych createKeyspace i createTable. Teraz dodaj poniższy kod, który wykonuje zapytanie dotyczące wszystkich użytkowników lub określonego użytkownika:

```java
LOGGER.info("Select all users");
repository.selectAllUsers();

LOGGER.info("Select a user by id (3)");
repository.selectUser(3);

LOGGER.info("Delete the users profile table");
repository.deleteTable();
```

## <a name="run-the-java-app"></a>Uruchamianie aplikację języka Java
1. Otwórz wiersz polecenia lub okno terminala. Wklej poniższy blok kodu. 

   Ten kod zmienia katalog (polecenie cd) na ścieżkę folderu, w której utworzono projekt. Następnie jest uruchamiane polecenie `mvn clean install` w celu wygenerowania pliku `cosmosdb-cassandra-examples.jar` w folderze docelowym. Na koniec kod uruchamia aplikację języka Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Teraz w witrynie Azure Portal otwórz **Eksplorator danych** i upewnij się, że tabela użytkowników została usunięta.

## <a name="next-steps"></a>Następne kroki

* W tym samouczku przedstawiono sposób wykonywania zapytań dotyczących danych z poziomu konta interfejsu API Cassandra usługi Azure Cosmos DB. Teraz możesz przejść do następnego artykułu:

> [!div class="nextstepaction"]
> [Migrowanie danych do konta interfejsu API Cassandra](cassandra-import-data.md)


