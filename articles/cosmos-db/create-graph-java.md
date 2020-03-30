---
title: Tworzenie bazy danych wykresów za pomocą oprogramowania Java w usłudze Azure Cosmos DB
description: Przykładowy kod Java, którego można używać do nawiązywania połączeń z danymi grafu i wykonywania zapytań względem nich w usłudze Azure Cosmos DB za pomocą języka Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: lbosq
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9f9b6614c586d9c7c721dfc59da9c4a9c342b57c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062067"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Szybki start: tworzenie bazy danych wykresów za pomocą zestawu Java SDK i interfejsu API Gremlin usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

W tym przewodniku Szybki start można utworzyć konto interfejsu API usługi Azure Cosmos DB Gremlin (wykres) i zarządzać nim z witryny Azure Portal i dodawać dane przy użyciu aplikacji Java sklonowanej z usługi GitHub. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Zestaw java development (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Skieruj `JAVA_HOME` zmienną środowiskową do folderu, w którym jest zainstalowany JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

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

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-information).

Poniższe fragmenty kodu są pobierane z pliku *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java.*

Ta aplikacja konsoli Java korzysta z bazy danych [API Gremlin](graph-introduction.md) ze sterownikiem OSS [Apache TinkerPop.](https://tinkerpop.apache.org/) 

- Gremlin `Client` jest inicjowany z konfiguracji w pliku *C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml.*

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Wykonywanie serii kroków języka Gremlin przy użyciu metody `client.submit`.

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

1. Na swoim koncie usługi Azure Cosmos DB w [portalu Azure](https://portal.azure.com/)wybierz pozycję **Klucze**. 

    Skopiuj pierwszą część wartości identyfikatora URI.

    ![Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, strona Klucze](./media/create-graph-java/copy-access-key-azure-portal.png)
2. Otwórz plik *src/remote.yaml* i wklej unikatową `$name$` `hosts: [$name$.graphs.azure.com]`wartość identyfikatora w pliku .

    Linia 1 *remote.yaml* powinna teraz wyglądać podobnie do 

    `hosts: [test-graph.graphs.azure.com]`

3. Zmień element `graphs` na `gremlin.cosmosdb` w wartości `endpoint`. (Jeśli konto bazy danych programu Graph zostało utworzone przed 20 grudnia 2017 r., nie wprowadzaj żadnych zmian wartości punktu końcowego i przejdź do następnego kroku).

    Wartość punktu końcowego powinna wyglądać następująco:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. W witrynie Azure Portal użyj przycisku kopiowania, aby skopiować KLUCZ PODSTAWOWY, i wklej go za pośrednictwem elementu `$masterKey$` w `password: $masterKey$`.

    Linia 4 *remote.yaml* powinna teraz wyglądać podobnie do 

    `password: 2Ggkr662ifxz2Mg==`

5. Zmień wiersz 3 *pliku remote.yaml* z

    `username: /dbs/$database$/colls/$collection$`

    na 

    `username: /dbs/sample-database/colls/sample-graph`

    W przypadku użycia unikatowej nazwy dla przykładowej bazy danych lub grafu odpowiednio zaktualizuj wartości.

6. Zapisz plik *remote.yaml.*

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
    
    Po zatrzymaniu programu wybierz pozycję Enter, a następnie wróć do witryny Azure portal w przeglądarce internetowej. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Przeglądanie i dodawanie przykładowych danych

Teraz możesz wrócić do Eksploratora danych i zobaczyć wierzchołki dodane do grafu, a także dodać kolejne punkty danych.

1. W swoim koncie usługi Azure Cosmos DB w portalu Azure wybierz **pozycję Eksplorator danych**, rozwiń **przykładowy wykres**, wybierz pozycję **Wykres**, a następnie wybierz pozycję **Zastosuj filtr**. 

   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. Na liście **Wyniki** zwróć uwagę na nowych użytkowników dodanych do grafu. Wybierz użytkownika **ben** i zwróć uwagę, że jest on połączony z użytkownikiem robin. Możesz przenosić wierzchołki, przeciągając je i upuszczając, zmieniać powiększenie przy użyciu kółka myszy oraz powiększać rozmiar grafu przy użyciu podwójnej strzałki. 

   ![Nowe wierzchołki grafu w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Dodajmy kilku nowych użytkowników. Wybierz **pozycję Nowy wierzchołek,** aby dodać dane do wykresu.

   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. W polu etykiety wprowadź *osobę*.

5. Wybierz **dodaj właściwość,** aby dodać każdą z następujących właściwości. Zauważ, że możesz utworzyć unikatowe właściwości dla każdej osoby w grafie. Tylko klucz id jest wymagany.

    key|value|Uwagi
    ----|----|----
    id|ashley|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|kobieta| 
    techniczne | java | 

    > [!NOTE]
    > W tym przewodniku Szybki start tworzona jest kolekcja niepartycjonowana. Niemniej jednak, jeśli utworzysz kolekcję partycjonowaną poprzez określenie klucza partycji podczas tworzenia kolekcji, musisz uwzględnić klucz partycji jako klucz w każdym nowym wierzchołku. 

6. Kliknij przycisk **OK**. Może być konieczne rozszerzenie ekranu w celu wyświetlenia przycisku **OK** u dołu ekranu.

7. Ponownie wybierz **pozycję Nowy wierzchołek** i dodaj kolejnego nowego użytkownika. 

8. Wprowadź etykietę *osoba*.

9. Wybierz **dodaj właściwość,** aby dodać każdą z następujących właściwości:

    key|value|Uwagi
    ----|----|----
    id|rakesh|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|mężczyzna| 
    szkoła|MIT| 

10. Kliknij przycisk **OK**. 

11. Wybierz przycisk **Zastosuj filtr** `g.V()` z filtrem domyślnym, aby wyświetlić wszystkie wartości na wykresie. Wszyscy użytkownicy będą teraz wyświetlani na liście **Wyniki**. 

    W miarę dodawania większej ilości danych można używać filtrów do ograniczania wyników. Domyślnie Eksplorator danych korzysta z zapytania `g.V()` w celu pobrania wszystkich wierzchołków grafu. Można je zmienić na inne [zapytanie o graf](tutorial-query-graph.md), takie jak`g.V().count()`, aby zwrócić liczbę wszystkich wierzchołków grafu w formacie JSON. Jeśli filtr został zmieniony, zmień `g.V()` filtr z powrotem na i wybierz opcję **Zastosuj filtr,** aby ponownie wyświetlić wszystkie wyniki.

12. Teraz możesz połączyć użytkowników rakesh i ashley. Upewnij **się, że ashley** jest zaznaczone na liście **Wyniki,** a następnie wybierz zmień ![cel wierzchołka na wykresie](./media/create-graph-java/edit-pencil-button.png) obok pozycji Obiekty **docelowe** w prawym dolnym czasie. Może być konieczne rozszerzenie okna w celu wyświetlenia przycisku.

    ![Zmienianie obiektu docelowego wierzchołka na wykresie — usługa Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. W polu **Cel** wprowadź *rakesh*, a w polu **Etykieta Krawędź** wprowadź *polecenie zna*, a następnie zaznacz pole wyboru.

    ![Dodawanie połączenia w Eksploratorze danych — Usługa Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Teraz wybierz użytkownika **rakesh** z listy wyników, aby zobaczyć, że użytkownicy ashley i rakesh są połączeni. 

    ![Dwa wierzchołki połączone w Eksploratorze danych — Usługa Azure CosmosDB](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

Na tym kończy się część tego samouczka poświęcona tworzeniu zasobów. Możesz dodać do grafu kolejne wierzchołki, zmodyfikować istniejące wierzchołki lub zmienić zapytania. Teraz przejrzyjmy metryki udostępniane przez usługę Azure Cosmos DB, a następnie wyczyśćmy zasoby. 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos DB, utworzyć wykres przy użyciu Eksploratora danych i uruchomić aplikację Java, która dodaje dane do wykresu. Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)

