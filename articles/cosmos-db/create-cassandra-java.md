---
title: Tworzenie aplikacji Java za pomocą interfejsu API Cassandra usługi Azure Cosmos DB Cassandra
description: W tym przewodniku Szybki start przedstawiono używanie interfejsu API bazy danych Cassandra w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przy użyciu witryny Azure Portal i języka Java
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4c93ffa7828710c26200408666c03a28fd7f3d3c
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549290"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data"></a>Szybki start: tworzenie aplikacji Java do zarządzania danymi interfejsu API usługi Azure Cosmos DB Cassandra

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

W tym przewodniku Szybki start utworzysz konto interfejsu API usługi Azure Cosmos DB Cassandra i użyj aplikacji Java Cassandra sklonowanej z usługi GitHub, aby utworzyć bazę danych i kontener Cassandra. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Możesz [też bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure.
- [Zestaw java development (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Skieruj `JAVA_HOME` zmienną środowiskową do folderu, w którym jest zainstalowany JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). Na Ubuntu `apt-get install maven` uruchom, aby zainstalować Maven.
- [Git](https://www.git-scm.com/downloads). Na Ubuntu `sudo apt-get install git` uruchom, aby zainstalować Git.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów musisz utworzyć konto bazy danych Cassandra z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujemy aplikację bazy danych Cassandra z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia. Utwórz nowy folder o nazwie `git-samples`. Następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak kod tworzy zasoby bazy danych, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). Wszystkie te fragmenty są pobierane z pliku *src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.*  

* Są ustawione opcje hosta Cassandra, portu, nazwy użytkownika, hasła i protokołu TLS/SSL. Informacje o parametrach połączenia pochodzą ze strony parametrów połączenia w witrynie Azure Portal.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Obiekt `cluster` łączy się z interfejsem API bazy danych Cassandra w usłudze Azure Cosmos DB i zwraca sesję umożliwiającą uzyskanie dostępu.

    ```java
    return cluster.connect();
    ```

Poniższe fragmenty kodu pochodzą z pliku *src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.*

* Utwórz nową przestrzeń kluczy.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Utwórz nową tabelę.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Wstaw jednostki użytkowników przy użyciu obiektu przygotowanej instrukcji.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Wyślij zapytanie w celu pobrania informacji o wszystkich użytkownikach.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Wyślij zapytanie w celu pobrania informacji o jednym użytkowniku.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Szczegóły parametrów połączenia umożliwiają aplikacji komunikowanie się z hostowaną bazą danych.

1. Na swoim koncie usługi Azure Cosmos DB w [portalu Azure](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

    ![Wyświetlanie i kopiowanie nazwy użytkownika z witryny Portal Azure, strona Parametry połączenia](./media/create-cassandra-java/copy-username-connection-string-azure-portal.png)

2. Użyj przycisku ![Kopiuj](./media/create-cassandra-java/copy-button-azure-portal.png) po prawej stronie ekranu, aby skopiować wartość PUNKT KONTAKTOWY.

3. Otwórz plik *config.properties* z folderu *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources* folder. 

3. Wklej wartość PUNKT KONTAKTOWY z portalu do lokalizacji `<Cassandra endpoint host>` w wierszu 2.

    Linia 2 *config.properties* powinna teraz wyglądać podobnie do 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Wróć do portalu i skopiuj wartość NAZWA_ Wklej wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<cassandra endpoint username>` w wierszu 4.

    Linia 4 *config.properties* powinna teraz wyglądać podobnie do 

    `cassandra_username=cosmos-db-quickstart`

4. Wróć do portalu i skopiuj wartość PASSWORD. Wklej wartość HASŁO z portalu do lokalizacji `<cassandra endpoint password>` w wierszu 5.

    Linia 5 *config.properties* powinna teraz wyglądać podobnie do 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. W wierszu 6, jeśli chcesz użyć określonego certyfikatu TLS/SSL, zamień `<SSL key store file location>` lokalizację certyfikatu TLS/SSL. Jeśli wartość nie zostanie podana, będzie używany certyfikat JDK zainstalowany w lokalizacji <JAVA_HOME>/jre/lib/security/cacerts. 

6. Jeśli wiersz 6 został zmieniony, aby użyć określonego certyfikatu TLS/SSL, zaktualizuj wiersz 7, aby użyć hasła do tego certyfikatu. 

7. Zapisz plik *config.properties.*

## <a name="run-the-java-app"></a>Uruchamianie aplikację języka Java

1. W oknie terminalu usługi git wpisz polecenie `cd` i przejdź do folderu `azure-cosmosdb-cassandra-java-getting-started\java-examples`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. W oknie terminalu usługi Git użyj poniższego polecenia, aby wygenerować plik `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. W oknie terminalu usługi Git uruchom następujące polecenie, aby uruchomić aplikację języka Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    W oknie terminalu zostaną wyświetlone powiadomienia o utworzeniu przestrzeni kluczy i tabeli. Następnie zostaną wybrani i zwróceni wszyscy użytkownicy w tabeli oraz pojawią się dane wyjściowe, po czym zostanie wybrany wiersz według identyfikatora i pojawi się wartość.  

    Naciśnij klawisze Ctrl+C, aby zatrzymać wykonywanie programu i zamknąć okno konsoli.

4. W witrynie Azure Portal otwórz **Eksploratora danych**, aby wykonywać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    ![Wyświetlanie danych w Eksploratorze danych — usługa Azure Cosmos DB](./media/create-cassandra-java/view-data-explorer-java-app.png)

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos DB za pomocą interfejsu API Cassandra i uruchomić aplikację Cassandra Java, która tworzy bazę danych i kontener Cassandra. Teraz można importować dodatkowe dane do konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)
