---
title: 'Azure Cosmos DB: Tworzenie aplikacji za pomocą języka Python i interfejsu API SQL | Microsoft Docs'
description: Przykładowy kod w języku Python, którego można użyć do nawiązywania połączenia z interfejsem API SQL usługi Azure Cosmos DB i do wykonywania w niej zapytań
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 51c11be2-af6d-425f-a86a-39cbfe61da29
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: sngun
ms.openlocfilehash: 2e439b260ae2964aeab33c100db3f62e0bd06f33
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-cosmos-db-build-a-sql-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB: Tworzenie aplikacji interfejsu API SQL za pomocą języka Python i witryny Azure Portal

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów. 

Ten przewodnik Szybki start przedstawia sposób tworzenia konta [interfejsu SQL API](sql-api-introduction.md) usługi Azure Cosmos DB, bazy danych dokumentów i kolekcji przy użyciu witryny Azure Portal. Następnie za pomocą [interfejsu API SQL języka Python](sql-api-sdk-python.md) utworzysz i uruchomisz aplikację konsoli.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Język [Python 3.6](https://www.python.org/downloads/) z folderami \<lokalizacja_instalacji\>\Python36 i \<lokalizacja_instalacji>\Python36\Scripts dodanymi do ścieżki PATH. 
* [Visual Studio Code](https://code.visualstudio.com/)
* [Rozszerzenie języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Dodawanie kolekcji

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API SQL z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-python-getting-started.git
    ```  
    
## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-string). 

Wszystkie poniższe fragmenty kodu są pobierane z pliku DocumentDBGetStarted.py.

* Inicjowanie klienta DocumentClient.

    ```python
    # Initialize the Python client
    client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})
    ```

* Tworzenie nowej bazy danych.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['SQL_DATABASE'] })
    ```

* Tworzenie nowej kolekcji.

    ```python
    # Create collection options
    options = {
        'offerEnableRUPerMinuteThroughput': True,
        'offerVersion': "V2",
        'offerThroughput': 400
    }

    # Create a collection
    collection = client.CreateCollection(db['_self'], { 'id': config['SQL_COLLECTION'] }, options)
    ```

* Tworzenie kilku dokumentów.

    ```python
    # Create some documents
    document1 = client.CreateDocument(collection['_self'],
        { 
            'id': 'server1',
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': 'some' 
        })
    ```

* Wykonywanie zapytania przy użyciu programu SQL.

    ```python
    # Query them in SQL
    query = { 'query': 'SELECT * FROM server s' }    
            
    options = {} 
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryDocuments(collection['_self'], query, options)
    results = list(result_iterable);

    print(results)
    ```

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. W witrynie [Azure Portal](http://portal.azure.com/), korzystając ze swojego konta usługi Azure Cosmos DB, kliknij pozycję **Klucze** w obszarze nawigacji po lewej stronie. W następnym kroku, korzystając z przycisków kopiowania dostępnych po prawej stronie ekranu, skopiujesz **identyfikator URI** i **klucz podstawowy** do pliku DocumentDBGetStarted.py.

    ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, blok Klucze](./media/create-sql-api-dotnet/keys.png)

2. Otwórz plik C:\git-samples\azure-cosmos-db-documentdb-python-getting-startedDocumentDBGetStarted.py w programie Visual Studio Code. 

3. Skopiuj wartość identyfikatora **URI** z portalu (przy użyciu przycisku kopiowania) i przypisz ją do klucza **endpoint** w pliku DocumentDBGetStarted.py. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Następnie skopiuj wartość **PRIMARY KEY** z portalu i przypisz ją do klucza **config.MASTERKEY** w pliku DocumentDBGetStarted.py. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

    `'MASTERKEY': 'FILLME',`

5. Zapisz plik DocumentDBGetStarted.py.
    
## <a name="run-the-app"></a>Uruchamianie aplikacji

1. W programie Visual Studio Code wybierz pozycję **Widok**>**Paleta poleceń**. 

2. W wierszu polecenia wprowadź **Python: Select Interpreter** (Python: wybierz interpreter), a następnie wybierz wersję języka Python do użycia.

    Stopka w programie Visual Studio Code jest aktualizowana w celu wskazania wybranego interpretera. 

3. Wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu Visual Studio SCode.

4. W oknie terminalu zintegrowanego upewnij się, znajdujesz się w folderze azure-cosmos-db-documentdb-python-getting-started. Jeśli nie, uruchom poniższe polecenie, aby przejść do folderu przykładu. 

    ```
    cd "C:\git-samples\azure-cosmos-db-documentdb-python-getting-started"`
    ```

5. Uruchom poniższe polecenie, aby zainstalować pakiet pydocumentdb. 

    ```
    pip3 install pydocumentdb
    ```

    Jeśli podczas próby zainstalowania pakietu pydocumentdb zostanie wyświetlony błąd odmowy dostępu, musisz [uruchomić program VS Code jako administrator](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Uruchom poniższe polecenie, aby uruchomić przykład i utworzyć oraz przechowywać nowe dokumenty w usłudze Azure Cosmos DB.

    ```
    python DocumentDBGetStarted.py
    ```

7. Aby potwierdzić utworzenie i zapisanie nowych dokumentów, w witrynie Azure Portal wybierz pozycję **Eksplorator danych**, rozwiń węzeł **coll**, rozwiń węzeł **Dokumenty**, a następnie wybierz dokument **server1**. Zawartość dokumentu server1 odpowiada zawartości zwróconej w oknie terminalu zintegrowanego. 

    ![Wyświetlanie nowych dokumentów w witrynie Azure Portal](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia kolekcji za pomocą Eksploratora danych i uruchamiania aplikacji. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB. 

> [!div class="nextstepaction"]
> [Importuj dane do usługi Azure Cosmos DB na potrzeby interfejsu API SQL](import-data.md)


