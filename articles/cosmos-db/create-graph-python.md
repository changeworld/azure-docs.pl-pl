---
title: 'Szybki start: interfejs API języka Gremlin za pomocą języka Python — Azure Cosmos DB'
description: W tym przewodniku Szybki start przedstawiono użycie interfejsu API języka Gremlin usługi Azure Cosmos DB do utworzenia aplikacji konsolowej przy użyciu witryny Azure Portal i języka Python
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: lbosq
ms.openlocfilehash: 18f54e64498c7809e662ceb71d1e497a0f275ad6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480451"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>Szybki start: Tworzenie grafowej bazy danych w usłudze Azure Cosmos DB przy użyciu języka Python i witryny Azure Portal

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

W tym przewodniku Szybki start przedstawiono użycie języka Python i [interfejsu API języka Gremlin](graph-introduction.md) usługi Azure Cosmos DB do utworzenia aplikacji konsolowej przez sklonowanie przykładu z usługi GitHub. Ten przewodnik Szybki start przeprowadzi Cię również przez tworzenie konta usługi Azure Cosmos DB przy użyciu portalu internetowego platformy Azure.   

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu globalnego rozproszenia i możliwości skalowania w poziomie w usłudze Azure Cosmos DB można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, tabel, par klucz/wartość i grafowe.  

> [!NOTE]
> Ten przewodnik Szybki start wymaga konta grafowej bazy danych utworzonego po 20 grudnia 2017 r. Istniejące konta będą obsługiwać język Python po migrowaniu do wersji powszechnie dostępnej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Można też [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

Ponadto:
* [Python](https://www.python.org/downloads/) — wersja 3.5 lub nowsza
* [Menedżer pakietów pip](https://pip.pypa.io/en/stable/installing/)
* [Usługa Git](https://git-scm.com/)
* [Sterownik języka Python dla języka Gremlin](https://github.com/apache/tinkerpop/tree/master/gremlin-python)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych grafów musisz utworzyć konto bazy danych Gremlin (Graph) z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Dodawanie grafu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujemy aplikację interfejsu API języka Gremlin z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi.  

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do folderu instalacji aplikacji przykładowej.  

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. Wszystkie fragmenty kodu pochodzą z pliku connect.py w folderze C:\git-samples\azure-cosmos-db-graph-python-getting-started\. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-information). 

* Element `client` języka Gremlin jest inicjowany w wierszu 104 w pliku `connect.py`:

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* Na początku pliku `connect.py` jest zadeklarowana seria kroków języka Gremlin. Następnie są one wykonywane przy użyciu metody `client.submitAsync()`:

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>Aktualizowanie danych połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o połączeniu i skopiować je do aplikacji. Te ustawienia umożliwiają aplikacji komunikację z hostowaną bazą danych.

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij pozycję **Klucze**. 

    Skopiuj pierwszą część wartości identyfikatora URI.

    ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, strona Klucze](./media/create-graph-python/keys.png)

2. Otwórz plik connect.py i w wierszu 104 wklej wartość URI w lokalizacji `<YOUR_ENDPOINT>` tutaj:

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    Część obiektu klienta dotycząca identyfikatora URI powinna teraz wyglądać podobnie jak w tym kodzie:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. Zmień drugi parametr obiektu `client`, zamieniając ciągi `<YOUR_DATABASE>` i `<YOUR_COLLECTION_OR_GRAPH>`. Jeśli użyjesz wartości sugerowanych, parametr powinien wyglądać podobnie do tego kodu:

    `username="/dbs/sample-database/colls/sample-graph"`

    Cały obiekt `client` powinien teraz wyglądać podobnie do tego kodu:

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. W witrynie Azure Portal za pomocą przycisku kopiowania skopiuj wartość KLUCZ PODSTAWOWY i wklej ją w lokalizacji `<YOUR_PASSWORD>` w parametrze `password=<YOUR_PASSWORD>`.

    Cała definicja obiektu `client` powinna teraz wyglądać podobnie do tego kodu:
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. Zapisz plik `connect.py`.

## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

1. W oknie terminalu usługi Git za pomocą polecenia `cd` przejdź do folderu azure-cosmos-db-graph-python-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. W oknie terminalu usługi Git użyj następującego polecenia, aby zainstalować wymagane pakiety języka Python.

   ```
   pip install -r requirements.txt
   ```

3. W oknie terminalu usługi Git użyj następującego polecenia, aby uruchomić aplikację języka Python.
    
    ```
    python connect.py
    ```

    W oknie terminalu zostaną wyświetlone wierzchołki i krawędzie dodawane do grafu. 
    
    Jeśli występują błędy przekroczenia limitu czasu, sprawdź, czy zaktualizowano poprawnie informacje o połączeniu w sekcji [Aktualizowanie danych połączenia](#update-your-connection-information), a także spróbuj ponownie uruchomić ostatnie polecenie. 
    
    Po zatrzymaniu działania programu naciśnij klawisz Enter i przejdź z powrotem do witryny Azure Portal w przeglądarce internetowej.

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Przeglądanie i dodawanie przykładowych danych

Teraz możesz wrócić do Eksploratora danych i zobaczyć wierzchołki dodane do grafu, a także dodać kolejne punkty danych.

1. Kliknij przycisk **Eksplorator danych**, rozwiń opcję **sample-graph**, kliknij pozycję **Graf**, a następnie **Zastosuj filtr**. 

   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png)

2. Na liście **Wyniki** zwróć uwagę na nowych użytkowników dodanych do grafu. Wybierz **ben** i zwróć uwagę, że są one połączone z użytkownikiem robin. Możesz przenosić wierzchołki, przeciągając je i upuszczając, zmieniać powiększenie przy użyciu kółka myszy oraz powiększać rozmiar grafu przy użyciu podwójnej strzałki. 

   ![Nowe wierzchołki grafu w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png)

3. Dodajmy kilku nowych użytkowników. Kliknij przycisk **Nowy wierzchołek**, aby dodać dane do grafu.

   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png)

4. Wprowadź etykietę *osoba*.

5. Kliknij pozycję **Dodaj właściwość**, aby dodać poszczególne poniższe właściwości. Zauważ, że możesz utworzyć unikatowe właściwości dla każdej osoby w grafie. Tylko klucz id jest wymagany.

    key|wartość|Uwagi
    ----|----|----
    id|ashley|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|kobieta| 
    techniczne | java | 

    > [!NOTE]
    > W tym przewodniku Szybki start zostaje utworzona kolekcja niepartycjonowana. Niemniej jednak, jeśli utworzysz kolekcję partycjonowaną poprzez określenie klucza partycji podczas tworzenia kolekcji, musisz uwzględnić klucz partycji jako klucz w każdym nowym wierzchołku. 

6. Kliknij przycisk **OK**. Może być konieczne rozszerzenie ekranu w celu wyświetlenia przycisku **OK** u dołu ekranu.

7. Kliknij przycisk **Nowy wierzchołek** ponownie i dodaj kolejnego nowego użytkownika. 

8. Wprowadź etykietę *osoba*.

9. Kliknij pozycję **Dodaj właściwość**, aby dodać poszczególne poniższe właściwości:

    key|wartość|Uwagi
    ----|----|----
    id|rakesh|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|mężczyzna| 
    szkoła|MIT| 

10. Kliknij przycisk **OK**. 

11. Kliknij przycisk **Zastosuj filtr** przy użyciu domyślnego filtru `g.V()`, aby wyświetlić wszystkie wartości na grafie. Wszyscy użytkownicy będą teraz wyświetlani na liście **Wyniki**. 

    W miarę dodawania większej ilości danych można używać filtrów do ograniczania wyników. Domyślnie Eksplorator danych korzysta z zapytania `g.V()` w celu pobrania wszystkich wierzchołków grafu. Można je zmienić na inne [zapytanie o graf](tutorial-query-graph.md), takie jak`g.V().count()`, aby zwrócić liczbę wszystkich wierzchołków grafu w formacie JSON. W przypadku zmiany filtru zmień filtr ponownie na `g.V()` i kliknij pozycję **Zastosuj filtr**, aby ponownie wyświetlić wszystkie wyniki.

12. Teraz możemy połączyć użytkowników rakesh i ashley. Upewnij się, że użytkownik **ashley** został wybrany na liście **Wyniki**, a następnie kliknij przycisk edycji obok pozycji **Cele** u dołu po prawej. Może być konieczne rozszerzenie okna w celu wyświetlenia obszaru **Właściwości**.

    ![Zmiana celu wierzchołka w grafie](./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png)

13. W polu **Cel** wpisz *rakesh*, a w polu **Etykieta krawędzi** wpisz *zna*, a następnie kliknij pole wyboru.

    ![Dodawanie połączenia między użytkownikami ashley i rakesh w Eksploratorze danych](./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png)

14. Teraz wybierz użytkownika **rakesh** z listy wyników, aby zobaczyć, że użytkownicy ashley i rakesh są połączeni. 

    ![Dwa wierzchołki połączone w Eksploratorze danych](./media/create-graph-python/azure-cosmosdb-graph-explorer.png)

    Na tym kończy się część tego samouczka poświęcona tworzeniu zasobów. Możesz dodać do grafu kolejne wierzchołki, zmodyfikować istniejące wierzchołki lub zmienić zapytania. Teraz przejrzyjmy metryki udostępniane przez usługę Azure Cosmos DB, a następnie wyczyśćmy zasoby. 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia grafu za pomocą Eksploratora danych i uruchamiania aplikacji. Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)

