---
title: Tworzenie bazy danych grafu przy użyciu języka Java w Azure Cosmos DB
description: Przykładowy kod Java, którego można używać do nawiązywania połączeń z danymi grafu i wykonywania zapytań względem nich w usłudze Azure Cosmos DB za pomocą języka Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: lbosq
ms.custom: seo-java-july2019
ms.openlocfilehash: cea53aefae2e559b7874b1235e4f952fe46ea642
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509612"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-the-java-sdk"></a>Szybki start: Tworzenie grafowej bazy danych w usłudze Azure Cosmos DB przy użyciu zestawu SDK języka Java 

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Usługa Azure Cosmos DB umożliwia szybkie tworzenie i wysyłanie zapytań do zarządzanych baz danych dokumentów, tabel i grafowych. 

Ten przewodnik Szybki start tworzy prostą grafową bazę danych przy użyciu narzędzi witryny Azure Portal dla usługi Azure Cosmos DB. Ponadto ten przewodnik Szybki start pokazuje, jak szybko utworzyć aplikację konsolową Java przy użyciu bazy danych [interfejsu API języka Gremlin](graph-introduction.md), korzystając ze sterownika OSS [Apache TinkerPop](https://tinkerpop.apache.org/). Instrukcje podane w tym przewodniku Szybki start można wykonać w dowolnym systemie operacyjnym, w którym można uruchomić oprogramowanie Java. Ten przewodnik Szybki start pozwala zaznajomić się z tworzeniem i modyfikowaniem grafów przy użyciu interfejsu użytkownika lub programowo, zgodnie z preferencjami. 

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ponadto:

* [Zestaw Java Development Kit (JDK) w wersji 8](https://aka.ms/azure-jdks)
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne [Maven](https://maven.apache.org/)
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Usługa Git](https://www.git-scm.com/)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizowanie parametrów połączenia](#update-your-connection-information).

Poniższe fragmenty kodu pochodzą z pliku C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java.

* Element `Client` języka Gremlin jest inicjowany z konfiguracji w pliku C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* Wykonywanie serii kroków języka Gremlin przy użyciu metody `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Aktualizowanie danych połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o połączeniu i skopiować je do aplikacji. Te ustawienia umożliwiają aplikacji komunikację z hostowaną bazą danych.

1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **klucze**. 

    Skopiuj pierwszą część wartości identyfikatora URI.

    ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, strona Klucze](./media/create-graph-java/keys.png)
2. Otwórz plik src/remote.yaml i wklej wartość unikatowego identyfikatora za pośrednictwem elementu `$name$` do adresu `hosts: [$name$.graphs.azure.com]`.

    Wiersz 1 pliku remote.yaml powinien wyglądać podobnie do 

    `hosts: [test-graph.graphs.azure.com]`

3. Zmień element `graphs` na `gremlin.cosmosdb` w wartości `endpoint`. (Jeśli konto bazy danych programu Graph zostało utworzone przed 20 grudnia 2017 r., nie wprowadzaj żadnych zmian wartości punktu końcowego i przejdź do następnego kroku).

    Wartość punktu końcowego powinna wyglądać następująco:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. W witrynie Azure Portal użyj przycisku kopiowania, aby skopiować KLUCZ PODSTAWOWY, i wklej go za pośrednictwem elementu `$masterKey$` w `password: $masterKey$`.

    Wiersz 4 pliku remote.yaml powinien wyglądać podobnie do 

    `password: 2Ggkr662ifxz2Mg==`

5. Zmień wiersz 3 pliku remote.yaml z

    `username: /dbs/$database$/colls/$collection$`

    na 

    `username: /dbs/sample-database/colls/sample-graph`

    W przypadku użycia unikatowej nazwy dla przykładowej bazy danych lub grafu odpowiednio zaktualizuj wartości.

6. Zapisz plik remote.yaml.

## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

1. W oknie terminalu usługi Git za pomocą polecenia `cd` przejdź do folderu azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. W oknie terminala usługi Git użyj następującego polecenia, aby zainstalować wymagane pakiety języka Java.

   ```git
   mvn package
   ```

3. W oknie terminala usługi Git użyj następującego polecenia, aby uruchomić aplikację języka Java.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    W oknie terminalu zostaną wyświetlone wierzchołki dodawane do grafu. 
    
    Jeśli występują błędy przekroczenia limitu czasu, sprawdź, czy zaktualizowano poprawnie informacje o połączeniu w sekcji [Aktualizowanie danych połączenia](#update-your-connection-information), a także spróbuj ponownie uruchomić ostatnie polecenie. 
    
    Po zatrzymaniu programu wybierz pozycję ENTER, a następnie przejdź z powrotem do Azure Portal w przeglądarce internetowej. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Przeglądanie i dodawanie przykładowych danych

Teraz możesz wrócić do Eksploratora danych i zobaczyć wierzchołki dodane do grafu, a także dodać kolejne punkty danych.

1. Wybierz pozycję **Eksplorator danych**, rozwiń pozycję **przykład-Graph**, wybierz pozycję **Graph**, a następnie wybierz pozycję **Zastosuj filtr**. 

   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. Na liście **Wyniki** zwróć uwagę na nowych użytkowników dodanych do grafu. Wybierz użytkownika **ben** i zwróć uwagę, że jest on połączony z użytkownikiem robin. Możesz przenosić wierzchołki, przeciągając je i upuszczając, zmieniać powiększenie przy użyciu kółka myszy oraz powiększać rozmiar grafu przy użyciu podwójnej strzałki. 

   ![Nowe wierzchołki grafu w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Dodajmy kilku nowych użytkowników. Wybierz pozycję **nowy wierzchołek** , aby dodać dane do grafu.

   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. W polu etykiety wprowadź *osobę*.

5. Wybierz pozycję **Dodaj właściwość** , aby dodać każdą z następujących właściwości. Zauważ, że możesz utworzyć unikatowe właściwości dla każdej osoby w grafie. Tylko klucz id jest wymagany.

    key|wartość|Uwagi
    ----|----|----
    id|ashley|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|kobieta| 
    techniczne | java | 

    > [!NOTE]
    > W tym przewodniku Szybki start tworzona jest kolekcja niepartycjonowana. Niemniej jednak, jeśli utworzysz kolekcję partycjonowaną poprzez określenie klucza partycji podczas tworzenia kolekcji, musisz uwzględnić klucz partycji jako klucz w każdym nowym wierzchołku. 

6. Kliknij przycisk **OK**. Może być konieczne rozszerzenie ekranu w celu wyświetlenia przycisku **OK** u dołu ekranu.

7. Ponownie wybierz **nowy wierzchołek** i Dodaj dodatkowego nowego użytkownika. 

8. Wprowadź etykietę *osoba*.

9. Wybierz pozycję **Dodaj właściwość** , aby dodać każdą z następujących właściwości:

    key|wartość|Uwagi
    ----|----|----
    id|rakesh|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|mężczyzna| 
    szkoła|MIT| 

10. Kliknij przycisk **OK**. 

11. ClSelectck przycisk **Zastosuj filtr** z domyślnym `g.V()` filtrem, aby wyświetlić wszystkie wartości na grafie. Wszyscy użytkownicy będą teraz wyświetlani na liście **Wyniki**. 

    W miarę dodawania większej ilości danych można używać filtrów do ograniczania wyników. Domyślnie Eksplorator danych korzysta z zapytania `g.V()` w celu pobrania wszystkich wierzchołków grafu. Można je zmienić na inne [zapytanie o graf](tutorial-query-graph.md), takie jak`g.V().count()`, aby zwrócić liczbę wszystkich wierzchołków grafu w formacie JSON. W przypadku zmiany filtru Zmień filtr z powrotem na `g.V()` i wybierz pozycję **Zastosuj filtr** , aby ponownie wyświetlić wszystkie wyniki.

12. Teraz możesz połączyć użytkowników rakesh i ashley. Upewnij się, że na liście **wyników** została wybrana ![opcja **Ashley** , a następnie wybierz pozycję Zmień obiekt docelowy wierzchołka w grafie](./media/create-graph-java/edit-pencil-button.png) obok **elementów docelowych** znajdujących się w prawej dolnej części. Może być konieczne rozszerzenie okna w celu wyświetlenia przycisku.

    ![Zmiana celu wierzchołka w grafie](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. W polu **Target** wpisz *Rakesh*, a w polu **etykieta krawędzi** wpisz *wie*, a następnie zaznacz pole wyboru.

    ![Dodawanie połączenia między użytkownikami ashley i rakesh w Eksploratorze danych](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Teraz wybierz użytkownika **rakesh** z listy wyników, aby zobaczyć, że użytkownicy ashley i rakesh są połączeni. 

    ![Dwa wierzchołki połączone w Eksploratorze danych](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    Na tym kończy się część tego samouczka poświęcona tworzeniu zasobów. Możesz dodać do grafu kolejne wierzchołki, zmodyfikować istniejące wierzchołki lub zmienić zapytania. Teraz przejrzyjmy metryki udostępniane przez usługę Azure Cosmos DB, a następnie wyczyśćmy zasoby. 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia grafu za pomocą Eksploratora danych i uruchamiania aplikacji. Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)

