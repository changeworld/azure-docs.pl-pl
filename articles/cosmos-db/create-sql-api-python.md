---
title: 'Szybki Start: Tworzenie aplikacji w języku Python przy użyciu Azure Cosmos DB konta interfejsu API SQL'
description: Przykładowy kod w języku Python, którego można użyć do nawiązywania połączenia z interfejsem API SQL usługi Azure Cosmos DB i do wykonywania w niej zapytań
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 1e0016e8ce6bc9178befd1a5ca96aa2554929ac5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719538"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Szybki Start: Tworzenie aplikacji w języku Python przy użyciu Azure Cosmos DB konta interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ten przewodnik Szybki start przedstawia, jak utworzyć konto [interfejsu API SQL](sql-api-introduction.md) usługi Azure Cosmos DB, bazę danych dokumentów i kontener przy użyciu witryny Azure Portal. Następnie za pomocą [interfejsu API SQL](sql-api-sdk-python.md) i zestawu Python SDK skompilujesz i uruchomisz aplikację konsolową.

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Można szybko tworzyć i wysyłać zapytania dotyczące dokumentów, kluczy/wartości, szerokich baz danych i grafów. Wszystkie te operacje korzystają z dystrybucji i skalowania Azure Cosmos DB.

Ten przewodnik Szybki Start używa wersji 4 [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/#history).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python 3.6 +](https://www.python.org/downloads/)z `python` pliku wykonywalnego dostępnego w `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Rozszerzenie języka Python dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

Teraz można użyć narzędzia Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener. 

1. Wybierz pozycję **Eksplorator danych** > **nowy kontener**. 
    
    Obszar **Dodaj kontener** jest wyświetlany po prawej stronie, może być konieczne przewinięcie w prawo w celu wyświetlenia go.

    ![Azure Portal Eksplorator danych, Dodaj okienko kontenera](./media/create-sql-api-python/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodawanie kontenera** wprowadź ustawienia dla nowego kontenera.

    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|Zadania|Wprowadź *todolist* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **zainicjuj przepływność bazy danych** , która umożliwia udostępnianie przepływności dla bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w obniżyć kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.| 
    |**Identyfikator kontenera**|Items|Wprowadź *elementy* jako nazwę nowego kontenera. Identyfikatory kontenerów mają takie same wymagania dotyczące znaków jak nazwy baz danych.|
    |**Klucz partycji**| /category| W przykładzie opisanym w tym artykule jest stosowany */Category* jako klucz partycji.|
    
    Oprócz powyższych ustawień można opcjonalnie dodać **unikatowe klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc unikatowe Zasady kluczy podczas tworzenia kontenera, należy zapewnić unikatowość jednej lub więcej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](unique-keys.md).
    
    Kliknij przycisk **OK**. W Eksplorator danych zostanie wyświetlona nowa baza danych i kontener.

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API SQL z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją.

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```cmd
    md "git-samples"
    ```
   Jeśli używasz monitu bash, zamiast tego użyj następującego polecenia:

   ```bash
   mkdir "git-samples"
   ```

2. Otwórz okno terminalu usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. W [Azure Portal](https://portal.azure.com/)na koncie usługi Azure Cosmos w lewym okienku nawigacji wybierz pozycję **klucze**. W następnym kroku, korzystając z przycisków kopiowania dostępnych po prawej stronie ekranu, skopiujesz wartości **Identyfikator URI** i **Klucz podstawowy** do pliku `cosmos_get_started.py`.

    ![Uzyskaj klucz dostępu i identyfikator URI w ustawieniach kluczy w Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Otwórz plik `cosmos_get_started.py` w programie \git-samples\azure-Cosmos-DB-Python-Getting-Started w programie Visual Studio Code.

3. Skopiuj wartość **identyfikatora URI** z portalu (przy użyciu przycisku kopiowania) i ustaw ją jako wartość zmiennej **punktu końcowego** w ``cosmos_get_started.py``. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Następnie skopiuj wartość **klucza podstawowego** z portalu i ustaw ją jako wartość **klucza** w ``cosmos_get_started.py``. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

    `key = 'FILLME'`

5. Zapisz plik ``cosmos_get_started.py``.

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Dowiedz się więcej o zasobach bazy danych utworzonych w kodzie lub przejdź z wyprzedzeniem, aby [zaktualizować parametry połączenia](#update-your-connection-string).

Wszystkie poniższe fragmenty kodu pochodzą z pliku `cosmos_get_started.py`.

* Inicjowanie klienta CosmosClient. Upewnij się, że Zaktualizowano wartości "Endpoint" i "Key" zgodnie z opisem w sekcji [aktualizowanie parametrów połączenia](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Tworzenie nowej bazy danych.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Tworzony jest nowy kontener z 400 RU/s [zainicjowaną przepływność](request-units.md). Wybieramy `lastName` jako [klucz partycji](partitioning-overview.md#choose-partitionkey), co umożliwia nam wykonywanie wydajnych zapytań filtrujących tę właściwość. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Dodawanie niektórych elementów do kontenera. Kontenery to kolekcja elementów (Dokumenty JSON), które mogą mieć zróżnicowany schemat. Metody pomocnika ```get_[name]_family_item``` zwracać reprezentacje rodziny, które są przechowywane w Azure Cosmos DB jako dokumenty JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Odczyty punktów (wyszukiwania wartości klucza) są wykonywane przy użyciu metody `read_item`. Przypisujemy [opłaty za usługę ru](request-units.md) dla każdej operacji.
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Zapytanie jest wykonywane przy użyciu składni zapytania SQL. Ponieważ korzystamy z wartości klucza partycji ```lastName``` w klauzuli WHERE, Azure Cosmos DB będzie efektywnie kierować to zapytanie do odpowiednich partycji, zwiększając wydajność.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Uruchomienie aplikacji

1. W programie Visual Studio Code wybierz pozycję **Widok** > **Paleta poleceń**. 

2. W wierszu polecenia wprowadź **Python: Select Interpreter** (Python: wybierz interpreter), a następnie wybierz wersję języka Python do użycia.

    Stopka w programie Visual Studio Code jest aktualizowana w celu wskazania wybranego interpretera. 

3. Wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu Visual Studio Code.

4. W oknie terminalu zintegrowanego upewnij się, znajdujesz się w folderze azure-cosmos-db-python-getting-started. Jeśli nie, uruchom poniższe polecenie, aby przejść do folderu przykładu. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Uruchom następujące polecenie, aby zainstalować pakiet azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Jeśli podczas próby zainstalowania pakietu azure-cosmos zostanie wyświetlony błąd odmowy dostępu, będzie trzeba [uruchomić program VS Code jako administrator](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Uruchom następujące polecenie, aby uruchomić przykład i utworzyć i zapisać nowe dokumenty w Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Aby potwierdzić, że nowe elementy zostały utworzone i zapisane, w Azure Portal wybierz pozycję **Eksplorator danych** > **AzureSampleFamilyDatabase** > **Items**. Wyświetl elementy, które zostały utworzone. Oto przykład przykładowego dokumentu JSON dla rodziny Andersen:

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

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos, tworzenia kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Cosmos DB. 

> [!div class="nextstepaction"]
> [Importuj dane do usługi Azure Cosmos DB na potrzeby interfejsu API SQL](import-data.md)


