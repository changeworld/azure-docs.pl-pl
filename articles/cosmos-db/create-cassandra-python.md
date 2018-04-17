---
title: 'Szybki start: interfejs API bazy danych Cassandra z językiem Python — Azure Cosmos DB | Microsoft Docs'
description: W tym przewodniku Szybki start przedstawiono używanie interfejsu API bazy danych Apache Cassandra w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przy użyciu języka Python do tworzenia aplikacji profilów przy użyciu języka Python
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 4ebc883e-c512-4e34-bd10-19f048661159
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: c725610dde10c275c3e2811cab197cf4912fe808
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-build-a-cassandra-app-with-python-and-azure-cosmos-db"></a>Szybki start: tworzenie aplikacji interfejsu API bazy danych Cassandra przy użyciu języka Python i usługi Azure Cosmos DB

W tym przewodniku Szybki start przedstawiono używanie języka Python i [interfejsu API bazy danych Cassandra](cassandra-introduction.md) w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przez sklonowanie przykładu z usługi GitHub. Ten przewodnik Szybki start przeprowadzi Cię również przez tworzenie konta usługi Azure Cosmos DB przy użyciu internetowej witryny Azure Portal.

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu globalnego rozproszenia i możliwości skalowania w poziomie w usłudze Azure Cosmos DB można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, tabel, par klucz/wartość i grafowe.   

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Można też [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

Uzyskaj dostęp do programu wersji zapoznawczej interfejsu API bazy danych Cassandra w usłudze Azure Cosmos DB. Jeśli jeszcze nie został wysłany wniosek o dostęp, [zarejestruj się teraz](cassandra-introduction.md#sign-up-now).

Ponadto:
* Język [Python](https://www.python.org/downloads/) w wersji 2.7.14
* [Git](http://git-scm.com/)
* [Sterownik języka Python dla bazy danych Apache Cassandra](https://github.com/datastax/python-driver)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów musisz utworzyć konto bazy danych Cassandra z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API bazy danych Cassandra z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz okno terminala usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do folderu instalacji aplikacji przykładowej. 

    ```bash
    cd "C:\git-samples"
    ```

2. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. Fragmenty kodu są pobierane z pliku `pyquickstart.py`. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-string). 

* Nazwę użytkownika i hasło można ustawić przy użyciu strony parametrów połączenia w witrynie Azure Portal. Zastąp fragment path\to\cert ścieżką do swojego certyfikatu X509.

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* Element `cluster` jest inicjowany przy użyciu informacji contactPoint. Dane contactPoint są pobierane z witryny Azure Portal.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* Element `cluster` łączy się z interfejsem API bazy danych Cassandra w usłudze Azure Cosmos DB.

    ```python
    session = cluster.connect()
    ```

* Tworzona jest nowa przestrzeń kluczy.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Tworzona jest nowa tabela.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Wstawiane są jednostki klucz-wartość.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    batch = BatchStatement()
    batch.add(insert_data, (1, 'LyubovK', 'Dubai'))
    batch.add(insert_data, (2, 'JiriK', 'Toronto'))
    batch.add(insert_data, (3, 'IvanH', 'Mumbai'))
    batch.add(insert_data, (4, 'YuliaT', 'Seattle'))
    ....
    session.execute(batch)
    ```

* Zapytanie o pobranie wszystkich wartości kluczy.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Zapytanie o pobranie pary klucz-wartość.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych.

1. W witrynie [Azure Portal](http://portal.azure.com/) kliknij pozycję **Parametry połączenia**. 

    Użyj przycisku ![Kopiuj](./media/create-cassandra-python/copy.png) po prawej stronie ekranu, aby skopiować górną wartość, PUNKT KONTAKTOWY.

    ![Wyświetlanie i kopiowanie nazwy użytkownika, hasła i punktu kontaktowego w witrynie Azure Portal, blok parametrów połączenia](./media/create-cassandra-python/keys.png)

2. Otwórz plik `config.py`. 

3. Wklej wartość PUNKT KONTAKTOWY z portalu do lokalizacji `<FILLME>` w wierszu 10.

    Wiersz 10 powinien teraz wyglądać podobnie do: 

    `'contactPoint': 'cosmos-db-quickstarts.documents.azure.com:10350'`

4. Skopiuj wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<FILLME>` w wierszu 6.

    Wiersz 6 powinien teraz wyglądać podobnie do: 

    `'username': 'cosmos-db-quickstart',`
    
5. Skopiuj wartość HASŁO z portalu do lokalizacji `<FILLME>` w wierszu 8.

    Wiersz 8 powinien teraz wyglądać podobnie do:

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Zapisz plik config.py.
    
## <a name="use-the-x509-certificate"></a>Używanie certyfikatu X509

1. Jeśli musisz dodać certyfikat Baltimore CyberTrust Root, jego numer to 02:00:00:b9, a odcisk palca SH1 to d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. Można go pobrać ze strony https://cacert.omniroot.com/bc2025.crt i zapisać w pliku lokalnym z rozszerzeniem cer.

2. Otwórz plik pyquickstart.py i zmień element „path\to\cert” tak, aby wskazywał nowy certyfikat.

3. Zapisz plik pyquickstart.py.

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Użyj polecenia cd w terminalu git, aby zmienić folder na azure-cosmos-db-cassandra-python-getting-started. 

2. Uruchom następujące polecenia, aby zainstalować wymagane moduły:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Uruchom poniższe polecenie, aby uruchomić aplikację węzła:

    ```
    python pyquickstart.py
    ```

3. W wierszu polecenia sprawdź, czy wyniki są zgodne z oczekiwaniami.

    Naciśnij klawisze CTRL+C, aby zatrzymać działanie programu i zamknąć okno konsoli. 

    ![Wyświetlanie i weryfikowanie danych wyjściowych](./media/create-cassandra-python/output.png)
    
    Teraz możesz otworzyć Eksploratora danych w witrynie Azure Portal, aby wyświetlać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    ![Wyświetlanie danych w Eksploratorze danych](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia kolekcji za pomocą Eksploratora danych i uruchamiania aplikacji. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)

