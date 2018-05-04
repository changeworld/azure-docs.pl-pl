---
title: 'Szybki start: interfejs API bazy danych Cassandra z platformą Node.js — Azure Cosmos DB | Microsoft Docs'
description: W tym przewodniku Szybki start przedstawiono używanie interfejsu API bazy danych Apache Cassandra w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przy użyciu platformy Node.js
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 4732e57d-32ed-40e2-b148-a8df4ff2630d
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: cc8fd5ef77407274187411da593be7b68ae114d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-and-azure-cosmos-db"></a>Szybki start: tworzenie aplikacji bazy danych Cassandra przy użyciu platformy Node.js i usługi Azure Cosmos DB

W tym przewodniku Szybki start przedstawiono używanie platformy Node.js i [interfejsu API bazy danych Cassandra](cassandra-introduction.md) w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przez sklonowanie przykładu z serwisu GitHub. Ten przewodnik Szybki start przeprowadzi Cię również przez tworzenie konta usługi Azure Cosmos DB przy użyciu portalu internetowego platformy Azure.

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu globalnego rozproszenia i możliwości skalowania w poziomie w usłudze Azure Cosmos DB można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, tabel, par klucz/wartość i grafowe. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Można też [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

Uzyskaj dostęp do programu wersji zapoznawczej interfejsu API bazy danych Cassandra w usłudze Azure Cosmos DB. Jeśli jeszcze nie został wysłany wniosek o dostęp, [zarejestruj się teraz](cassandra-introduction.md#sign-up-now).

Ponadto:
* [Node.js](https://nodejs.org/en/) w wersji 0.10.29 lub nowszej
* [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów musisz utworzyć konto bazy danych Cassandra z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API bazy danych Cassandra z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. Wszystkie fragmenty kodu pochodzą z pliku uprofile.js znajdującego się w folderze C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-string). 

* Nazwę użytkownika i hasło można ustawić przy użyciu strony parametrów połączenia w witrynie Azure Portal. Element „path\to\cert” zawiera ścieżkę do certyfikatu X509. 

   ```nodejs
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* Element `client` jest inicjowany przy użyciu informacji contactPoint. Dane contactPoint są pobierane z witryny Azure Portal.

    ```nodejs
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* Element `client` łączy się z interfejsem API bazy danych Cassandra w usłudze Azure Cosmos DB.

    ```nodejs
    client.connect(next);
    ```

* Tworzona jest nowa przestrzeń kluczy.

    ```nodejs
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Tworzona jest nowa tabela.

   ```nodejs
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Wstawiane są jednostki klucz-wartość.

    ```nodejs
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum', '2017-10-3136']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Zapytanie o pobranie wszystkich wartości kluczy.

    ```nodejs
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Zapytanie o pobranie pary klucz-wartość.

    ```nodejs
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych.

1. W witrynie [Azure Portal](http://portal.azure.com/) kliknij pozycję **Parametry połączenia**. 

    Użyj przycisku ![Kopiuj](./media/create-cassandra-nodejs/copy.png) po prawej stronie ekranu, aby skopiować górną wartość, PUNKT KONTAKTOWY.

    ![Wyświetlanie i kopiowanie wartości PUNKT KONTAKTOWY, NAZWA UŻYTKOWNIKA i HASŁO ze strony parametrów połączenia w witrynie Azure Portal](./media/create-cassandra-nodejs/keys.png)

2. Otwórz plik `config.js`. 

3. Wklej wartość PUNKT KONTAKTOWY z portalu do lokalizacji `<FillMEIN>` w wierszu 4.

    Wiersz 4 powinien teraz wyglądać podobnie do: 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Skopiuj wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<FillMEIN>` w wierszu 2.

    Wiersz 2 powinien teraz wyglądać podobnie do: 

    `config.username = 'cosmos-db-quickstart';`
    
5. Skopiuj wartość HASŁO z portalu do lokalizacji `<FillMEIN>` w wierszu 3.

    Wiersz 3 powinien teraz wyglądać podobnie do:

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Zapisz plik config.js.
    
## <a name="use-the-x509-certificate"></a>Używanie certyfikatu X509 

1. Jeśli musisz dodać certyfikat Baltimore CyberTrust Root, jego numer to 02:00:00:b9, a odcisk palca SH1 to d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. Można go pobrać ze strony https://cacert.omniroot.com/bc2025.crt i zapisać w pliku lokalnym z rozszerzeniem cer. 

2. Otwórz plik uprofile.js i zmień element „path\to\cert” tak, aby wskazywał nowy certyfikat. 

3. Zapisz plik uprofile.js. 

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. W oknie terminalu usługi Git uruchom polecenie `npm install`, aby zainstalować wymagane moduły npm.

2. Uruchom polecenie `node uprofile.js`, aby uruchomić aplikację Node.

3. W wierszu polecenia sprawdź, czy wyniki są zgodne z oczekiwaniami.

    ![Wyświetlanie i weryfikowanie danych wyjściowych](./media/create-cassandra-nodejs/output.png)

    Naciśnij klawisze CTRL+C, aby zatrzymać działanie programu i zamknąć okno konsoli. 

    Teraz możesz otworzyć Eksploratora danych w witrynie Azure Portal, aby wyświetlać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    ![Wyświetlanie danych w Eksploratorze danych](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia kolekcji za pomocą Eksploratora danych i uruchamiania aplikacji. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)


