---
title: 'Szybki start: tworzenie aplikacji języka Python przy użyciu konta interfejsu API usługi Azure Cosmos DB SQL'
description: Przykładowy kod w języku Python, którego można użyć do nawiązywania połączenia z interfejsem API SQL usługi Azure Cosmos DB i do wykonywania w niej zapytań
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 10247e22b3fbe1250a15b06a0cce974905ca6b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942624"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Szybki start: tworzenie aplikacji języka Python przy użyciu konta interfejsu API SQL usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku Szybki start można utworzyć konto interfejsu API SQL usługi Azure Cosmos DB i zarządzać nim z witryny Azure portal oraz z programu Visual Studio Code za pomocą aplikacji języka Python sklonowanej z usługi GitHub. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Możesz [też bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora bazy danych usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Python 3.6+](https://www.python.org/downloads/), `python` z plikiem wykonywalnym w twoim `PATH`.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Rozszerzenie Języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

Teraz można użyć narzędzia Eksploratora danych w witrynie Azure portal do tworzenia bazy danych i kontenera. 

1. Wybierz nowy kontener **Eksploratora** > **New Container**danych . 
    
    Obszar **Dodaj kontener** jest wyświetlany po prawej stronie, może być konieczne przewinięcie w prawo, aby go zobaczyć.

    ![Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodaj kontener** wprowadź ustawienia nowego kontenera.

    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|Zadania|Wprowadź *Zadania* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **przepływność bazy danych aprowizuj,** umożliwia udostępnianie przepływności aprowizowanej do bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w oszczędzaniu kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.| 
    |**Identyfikator kontenera**|Items|Wprowadź *elementy* jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.|
    |**Klucz partycji**| /category| Przykład opisany w tym artykule używa */category* jako klucz partycji.|
    
    Oprócz poprzednich ustawień można opcjonalnie dodać **unikatowe klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc unikatowe zasady klucza podczas tworzenia kontenera, należy zapewnić unikatowość jednej lub więcej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](unique-keys.md).
    
    Kliknij przycisk **OK**. W Eksploratorze danych zostanie wyświetlona nowa baza danych i nowy kontener.

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API SQL z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Ten szybki start używa wersji 4 [SDK Języka Python](https://pypi.org/project/azure-cosmos/#history).

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```cmd
    md "git-samples"
    ```
   Jeśli używasz monitu bash, należy zamiast tego użyć następującego polecenia:

   ```bash
   mkdir "git-samples"
   ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. Na koncie usługi Azure Cosmos DB w [witrynie Azure portal](https://portal.azure.com/)wybierz **klawisze** z lewej strony nawigacji. Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować **identyfikator URI** i **klucz podstawowy** do pliku *cosmos_get_started.py* w następnym kroku.

    ![Uzyskiwanie klucza dostępu i identyfikatora URI w ustawieniach kluczy w witrynie Azure portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. W programie Visual Studio Code otwórz plik *cosmos_get_started.py* w *folderze \git-samples\azure-cosmos-db-python-getting-started*.

3. Skopiuj wartość **identyfikatora URI** z portalu (za pomocą przycisku kopiowania) i spraw, aby była to wartość zmiennej **punktu końcowego** w *cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Następnie skopiuj wartość **klucza podstawowego** z portalu i spraw, aby była to wartość **klucza** w *cosmos_get_started.py*. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

    `key = 'FILLME'`

5. Zapisz *plik cosmos_get_started.py.*

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Dowiedz się więcej o zasobach bazy danych utworzonych w kodzie lub przejdź do przodu, [aby zaktualizować parametry połączenia](#update-your-connection-string).

Poniższe fragmenty kodu są pobierane z pliku *cosmos_get_started.py.*

* Inicjowanie klienta CosmosClient. Upewnij się, aby zaktualizować wartości "punkt końcowy" i "klucz", jak opisano w [sekcji Aktualizuj ciąg połączenia.](#update-your-connection-string) 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Tworzenie nowej bazy danych.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Tworzony jest nowy kontener z 400 000 ru/s [aprowizowanej przepływności](request-units.md). Wybieramy `lastName` jako [klucz partycji](partitioning-overview.md#choose-partitionkey), co pozwala nam na efektywne zapytania, które filtrują na tej właściwości. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Dodawanie niektórych elementów do kontenera. Kontenery są kolekcją elementów (dokumenty JSON), które mogą mieć zróżnicowany schemat. Metody ```get_[name]_family_item``` pomocnicze zwracają reprezentacje rodziny, które są przechowywane w usłudze Azure Cosmos DB jako dokumenty JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Odczyty punktowe (wyszukiwania wartości klucza) są wykonywane przy użyciu `read_item` metody. Drukujemy [opłatę RU każdej](request-units.md) operacji.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Kwerenda jest wykonywana przy użyciu składni kwerendy SQL. Ponieważ używamy wartości klucza ```lastName``` partycji w klauzuli WHERE, usługa Azure Cosmos DB będzie skutecznie kierować tę kwerendę do odpowiednich partycji, zwiększając wydajność.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Uruchomienie aplikacji

1. W programie Visual Studio Code wybierz pozycję **Wyświetl** > **paletę poleceń**. 

2. W wierszu polecenia wprowadź **Python: Select Interpreter** (Python: wybierz interpreter), a następnie wybierz wersję języka Python do użycia.

    Stopka w programie Visual Studio Code jest aktualizowana w celu wskazania wybranego interpretera. 

3. Wybierz **opcję Wyświetl** > **zintegrowany terminal,** aby otworzyć zintegrowany terminal programu Visual Studio Code.

4. W zintegrowanym oknie terminala upewnij się, że znajdujesz się w folderze *azure-cosmos-db-python-getting-started.* Jeśli nie, uruchom poniższe polecenie, aby przejść do folderu przykładu. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Uruchom następujące polecenie, aby zainstalować pakiet azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Jeśli podczas próby zainstalowania pakietu azure-cosmos zostanie wyświetlony błąd odmowy dostępu, będzie trzeba [uruchomić program VS Code jako administrator](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Uruchom następujące polecenie, aby uruchomić przykład i utworzyć i przechowywać nowe dokumenty w usłudze Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Aby potwierdzić, że nowe elementy zostały utworzone i zapisane, w witrynie Azure Portal wybierz pozycję **Data Explorer** > **AzureSampleFamilyDatabase** > **Items**. Wyświetl elementy, które zostały utworzone. Na przykład oto przykładowy dokument JSON dla rodziny Andersen:
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos DB, utworzyć kontener przy użyciu Eksploratora danych i uruchomić aplikację Języka Python w programie Visual Studio Code. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importuj dane do usługi Azure Cosmos DB na potrzeby interfejsu API SQL](import-data.md)


