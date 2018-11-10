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
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739213"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Samouczek: wykonywanie zapytań względem danych z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB

W tym samouczku przedstawiono sposób wykonywania zapytań o dane użytkownika z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB przy użyciu aplikacji języka Java. Aplikacja języka Java używa [sterownika Java](https://github.com/datastax/java-driver) i wykonuje zapytania o dane użytkownika, takie jak identyfikator użytkownika, nazwa użytkownika i miasto użytkownika. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Wykonywanie zapytań dotyczących danych z tabeli bazy danych Cassandra
> * Uruchamianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* Ten artykuł należy do wieloczęściowego samouczka. Przed rozpoczęciem pamiętaj o ukończeniu poprzednich kroków tworzenia konta, przestrzeni kluczy i tabeli interfejsu API Cassandra oraz [ładowania przykładowych danych do tabeli](cassandra-api-load-data.md). 

## <a name="query-data"></a>Zapytania o dane

Przeprowadź poniższe czynności, aby wykonać zapytania względem danych z poziomu konta interfejsu API Cassandra:

1. Otwórz plik `UserRepository.java` w folderze `src\main\java\com\azure\cosmosdb\cassandra`. Dodaj poniższy blok kodu. Ten kod umożliwia zastosowanie trzech metod: 

   * Wykonywanie zapytań względem wszystkich użytkowników w bazie danych
   * Wykonywanie zapytań względem określonego użytkownika przefiltrowanego według identyfikatora
   * Usuwanie tabeli.

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

2. Otwórz plik `UserProfile.java` w folderze `src\main\java\com\azure\cosmosdb\cassandra`. Ta klasa zawiera metodę główną, która wywołuje zdefiniowane wcześniej metody wstawiania danych createKeyspace i createTable. Teraz dodaj poniższy kod, który wykonuje zapytanie dotyczące wszystkich użytkowników lub określonego użytkownika:

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


