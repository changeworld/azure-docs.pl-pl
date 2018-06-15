---
title: 'Szybki start: interfejs API bazy danych Cassandra z językiem Java — Azure Cosmos DB | Microsoft Docs'
description: W tym przewodniku Szybki start przedstawiono używanie interfejsu API bazy danych Cassandra w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przy użyciu witryny Azure Portal i języka Java
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.custom: quick start connect, mvc
ms.devlang: java
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 886756718a4e55638d641adc12cd628f7d13521d
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798410"
---
# <a name="quickstart-build-a-cassandra-app-with-java-and-azure-cosmos-db"></a>Szybki start: tworzenie aplikacji interfejsu API bazy danych Cassandra przy użyciu języka Java i usługi Azure Cosmos DB

W tym przewodniku Szybki start przedstawiono używanie języka Java i [interfejsu API bazy danych Cassandra](cassandra-introduction.md) w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przez sklonowanie przykładu z serwisu GitHub. Ten przewodnik Szybki start przeprowadzi Cię również przez tworzenie konta usługi Azure Cosmos DB przy użyciu portalu internetowego platformy Azure.

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu globalnego rozproszenia i możliwości skalowania w poziomie w usłudze Azure Cosmos DB można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, tabel, par klucz/wartość i grafowe. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Można też [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

Uzyskaj dostęp do programu wersji zapoznawczej interfejsu API bazy danych Cassandra w usłudze Azure Cosmos DB. Jeśli jeszcze nie został wysłany wniosek o dostęp, [zarejestruj się teraz](cassandra-introduction.md#sign-up-now).

Ponadto: 

* [Zestaw Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstaluj](http://maven.apache.org/install.html) archiwum binarne [Maven](http://maven.apache.org/)
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.



## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów musisz utworzyć konto bazy danych Cassandra z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujemy aplikację bazy danych Cassandra z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-string). Wszystkie te fragmenty kodu pochodzą z pliku src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.  

* Ustawiany jest host bazy danych Cassandra oraz port, nazwa użytkownika, hasło i opcje protokołu SSL. Informacje o parametrach połączenia pochodzą ze strony parametrów połączenia w witrynie Azure Portal.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Obiekt `cluster` łączy się z interfejsem API bazy danych Cassandra w usłudze Azure Cosmos DB i zwraca sesję umożliwiającą uzyskanie dostępu.

    ```java
    return cluster.connect();
    ```

Poniższe fragmenty kodu pochodzą z pliku src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.

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

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych.

1. W witrynie [Azure Portal](http://portal.azure.com/) kliknij pozycję **Parametry połączenia**. 

    ![Wyświetlanie i kopiowanie nazwy użytkownika z witryny Portal Azure, strona Parametry połączenia](./media/create-cassandra-java/keys.png)

2. Użyj przycisku ![Kopiuj](./media/create-cassandra-java/copy.png) po prawej stronie ekranu, aby skopiować wartość PUNKT KONTAKTOWY.

3. Otwórz plik `config.properties` z folderu C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources. 

3. Wklej wartość PUNKT KONTAKTOWY z portalu do lokalizacji `<Cassandra endpoint host>` w wierszu 2.

    Wiersz 2 pliku config.properties powinien teraz wyglądać podobnie do: 

    `cassandra_host=cosmos-db-quickstarts.cassandra.cosmosdb.azure.com`

3. Wróć do portalu i skopiuj wartość NAZWA UŻYTKOWNIKA. Wklej wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<cassandra endpoint username>` w wierszu 4.

    Wiersz 4 pliku config.properties powinien teraz wyglądać podobnie do: 

    `cassandra_username=cosmos-db-quickstart`

4. Wróć do portalu i skopiuj wartość HASŁO. Wklej wartość HASŁO z portalu do lokalizacji `<cassandra endpoint password>` w wierszu 5.

    Wiersz 5 pliku config.properties powinien teraz wyglądać podobnie do: 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Jeśli chcesz użyć określonego certyfikatu SSL, w wierszu 6 zamień wartość `<SSL key store file location>` na lokalizację certyfikatu SSL. Jeśli wartość nie zostanie podana, będzie używany certyfikat JDK zainstalowany w lokalizacji <JAVA_HOME>/jre/lib/security/cacerts. 

6. Jeśli został zmieniony wiersz 6 w celu użycia określonego certyfikatu SSL, zaktualizuj wiersz 7, aby użyć hasła dla tego certyfikatu. 

7. Zapisz plik config.properties.

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. W oknie terminalu usługi Git za pomocą polecenia `cd` przejdź do folderu azure-cosmosdb-cassandra-java-getting-started\java-examples.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. W oknie terminalu usługi Git użyj następującego polecenia, aby wygenerować plik cosmosdb-cassandra-examples.jar.

    ```git
    mvn clean install
    ```

3. W oknie terminalu usługi Git uruchom następujące polecenie, aby uruchomić aplikację języka Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    W oknie terminalu zostaną wyświetlone powiadomienia o utworzeniu przestrzeni kluczy i tabeli. Następnie zostaną wybrani i zwróceni wszyscy użytkownicy w tabeli oraz pojawią się dane wyjściowe, po czym zostanie wybrany wiersz według identyfikatora i pojawi się wartość.  

    Naciśnij klawisze CTRL + C, aby zatrzymać działanie programu i zamknąć okno konsoli. 
    
    Teraz możesz otworzyć Eksploratora danych w witrynie Azure Portal, aby wyświetlać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    ![Wyświetlanie danych w Eksploratorze danych](./media/create-cassandra-java/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono tworzenie konta usługi Azure Cosmos DB, bazy danych Cassandra i kolekcji za pomocą Eksploratora danych oraz uruchamianie aplikacji w celu wykonania tych samych czynności w sposób programowy. Teraz możesz zaimportować dodatkowe dane do kolekcji usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)
