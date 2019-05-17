---
title: Tworzenie aplikacji internetowej Flask za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB i zestawu SDK języka Python
description: Przykładowy kod Python Flask, którego można używać do nawiązywania połączeń i wykonywania zapytań przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 3a95ee065b25f936f08e1c775dc9c42e75a0f67e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65793852"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Szybki start: Tworzenie aplikacji w języku Python za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

W tym przewodniku Szybki start wykorzystano następujący [przykład Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) i przedstawiono sposób tworzenia prostej aplikacji Flask zadań do wykonania (To-Do) za pomocą [emulatora usługi Azure Cosmos DB](local-emulator.md) i interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz [emulatora usługi Cosmos Azure DB](local-emulator.md). Emulator jest obecnie obsługiwany tylko w systemie Windows. Przedstawiono sposób używania przykładu z kluczem produkcyjnym platformy Azure, co można zrobić na dowolnej platformie.

- Jeśli nie masz jeszcze zainstalowanego programu Visual Studio Code, możesz szybko zainstalować program [VS Code](https://code.visualstudio.com/Download) odpowiedni dla swojej platformy (Windows, Mac, Linux).

- Pamiętaj o dodaniu obsługi języka Python przez zainstalowanie jednego z popularnych rozszerzeń języka Python.
  1. Wybierz rozszerzenie.
  2. Zainstaluj rozszerzenie, wpisując polecenie `ext install` w palecie poleceń skrótu `Ctrl+Shift+P`.

     W przykładach omówionych w tym dokumencie używane jest popularne i pełnofunkcyjne [rozszerzenie języka Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) Dona Jayamanne’a.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację Flask bazy danych MongoDB z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi.

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminalu usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Uruchom następujące polecenie, aby zainstalować moduły języka Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Otwórz folder w programie Visual Studio Code.

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji internetowej](#run-the-web-app). 

Wszystkie poniższe fragmenty kodu pochodzą z pliku app.py i używają parametrów połączenia lokalnego emulatora usługi Azure Cosmos DB. Hasło musi zostać podzielone, jak pokazano poniżej, aby uwzględnić ukośniki, których w przeciwnym razie nie można by było przeanalizować.

* Inicjujemy klienta bazy danych MongoDB, pobieramy bazę danych i uwierzytelniamy.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Pobieramy kolekcję lub tworzymy ją, jeśli jeszcze nie istnieje.

    ```python
    todos = db.todo #Select the collection
    ```

* Tworzymy aplikację.

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Uruchamianie aplikacji internetowej

1. Upewnij się, że emulator usługi Azure Cosmos DB działa.

2. Otwórz okno terminalu i za pomocą polecenia `cd` przejdź do katalogu, w którym jest zapisana aplikacja.

3. Następnie ustaw zmienną środowiskową dla aplikacji Flask za pomocą `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` dla edytorów programu PowerShell lub `export FLASK_APP=app.py` korzystania z komputerów Mac. 

4. Uruchom aplikację za pomocą polecenia `flask run` i przejdź do adresu [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Dodawaj oraz usuwaj zadania i sprawdzaj, jak są dodawane i zmieniane w kolekcji.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Jeśli chcesz przetestować kod na aktywnym koncie usługi Cosmos, przejdź do witryny Azure Portal, aby utworzyć konto i uzyskać informacje o parametrach połączenia. Następnie skopiuj je do aplikacji.

1. W witrynie [Azure Portal](https://portal.azure.com/), korzystając ze swojego konta usługi Cosmos, kliknij w lewym panelu nawigacyjnym pozycję **Parametry połączenia**, a następnie pozycję **Klucze odczytu i zapisu**. W następnym kroku, korzystając z przycisków kopiowania dostępnych po prawej stronie ekranu, skopiujesz nazwę użytkownika, hasło i hosta do pliku Dal.cs.

2. Otwórz plik **app.py** w katalogu głównym.

3. Skopiuj wartość **username** z portalu (przy użyciu przycisku kopiowania) i przypisz ją do klucza **name** w pliku **app.py**.

4. Następnie skopiuj wartość **connection string** (parametry połączenia) z portalu i przypisz ją do klucza MongoClient w pliku **app.py**.

5. Na końcu skopiuj wartość **password** z portalu i przypisz ją do klucza **password** w pliku **app.py**.

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do komunikowania się z usługą Cosmos DB. Możesz ją uruchomić tak jak wcześniej.

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

Aby wdrożyć tę aplikację, możesz utworzyć nową aplikację internetową na platformie Azure i włączyć ciągłe wdrażanie z rozwidleniem tego repozytorium GitHub. Skorzystaj z tego [samouczka](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), aby skonfigurować ciągłe wdrażanie za pomocą usługi GitHub na platformie Azure.

W przypadku wdrażania na platformie Azure należy usunąć klucze aplikacji i upewnić się, że poniższa sekcja nie jest wyłączona przez oznaczenie jej jako komentarza:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Następnie należy dodać do ustawień aplikacji odpowiednie wartości MONGOURL, MONGO_PASSWORD i MONGO_USERNAME. Aby dowiedzieć się więcej o ustawieniach aplikacji w usłudze Azure Web Apps, skorzystaj z tego [samouczka](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings).

Jeśli nie chcesz tworzyć rozwidlenia tego repozytorium, możesz też kliknąć przycisk „Wdrażanie na platformie Azure” poniżej. Przejdziesz wtedy na platformę Azure i skonfigurujesz ustawienia aplikacji przy użyciu informacji dotyczących używanego konta usługi Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Jeśli planujesz przechowywanie kodu w usłudze GitHub lub w innych rozwiązaniach kontroli źródła, pamiętaj o usunięciu z kodu parametrów połączenia. Można je zamiast tego konfigurować za pomocą ustawień aplikacji internetowej.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Cosmos i uruchamiania aplikacji Flask. Teraz możesz zaimportować dodatkowe dane do swojej bazy danych usługi Cosmos. 

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)
