---
title: 'Azure Cosmos DB: tworzenie aplikacji internetowej Flask za pomocą języka Python i interfejsu API bazy danych MongoDB w usłudze Azure Cosmos DB | Microsoft Docs'
description: Przykładowy kod Python Flask, za pomocą którego można nawiązywać połączenia z interfejsem API bazy danych MongoDB w usłudze Azure Cosmos DB i wykonywać względem niego zapytania
services: cosmos-db
documentationcenter: ''
author: heatherbshapiro
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/2/2017
ms.author: hshapiro
ms.openlocfilehash: 7fd25a1181e1a4d69bf8233b34a4c416378e0181
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB: tworzenie aplikacji Flask za pomocą interfejsu API bazy danych MongoDB

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

W tym przewodniku Szybki start wykorzystano następujący [przykład Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) i przedstawiono sposób tworzenia prostej aplikacji Flask zadań do wykonania (To-Do) za pomocą [emulatora usługi Azure Cosmos DB](/local-emulator.md) i [interfejsu API MongoDB](mongodb-introduction.md) usługi Azure Cosmos DB zamiast bazy danych MongoDB.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz [emulatora usługi Cosmos Azure DB](/local-emulator.md). Emulator jest obecnie obsługiwany tylko w systemie Windows. Przedstawiono sposób używania przykładu z kluczem produkcyjnym platformy Azure, co można zrobić na dowolnej platformie.

- Jeśli nie masz jeszcze zainstalowanego programu Visual Studio Code, możesz szybko zainstalować program [VS Code](https://code.visualstudio.com/Download) odpowiedni dla swojej platformy (Windows, Mac, Linux).

- Pamiętaj o dodaniu obsługi języka Python przez zainstalowanie jednego z popularnych rozszerzeń języka Python.
    1. Wybierz rozszerzenie.
    2. Zainstaluj rozszerzenie, wpisując polecenie `ext install` w palecie poleceń skrótu `Ctrl+Shift+P`.

    W przykładach omówionych w tym dokumencie używane jest popularne i pełnofunkcyjne [rozszerzenie języka Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) Dona Jayamanne’a.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację Flask interfejsu API bazy danych MongoDB z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi.

1. Otwórz okno terminalu usługi Git, na przykład git bash, i za pomocą polecenia `cd` przejdź do katalogu roboczego.
2. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Uruchom następujące polecenie, aby zainstalować moduły języka Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Otwórz folder w programie Visual Studio Code.

## <a name="review-the-code"></a>Przeglądanie kodu

Dokonajmy szybkiego przeglądu działań wykonywanych w aplikacji. Otwieramy plik **app.py** w katalogu głównym i możemy się przekonać, że następujące wiersze kodu tworzą połączenie usługi Azure Cosmos DB. W poniższym kodzie użyto parametrów połączenia lokalnego emulatora usługi Azure Cosmos DB. Hasło musi zostać podzielone, jak pokazano poniżej, aby uwzględnić ukośniki, których w przeciwnym razie nie można by było przeanalizować.

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
    
## <a name="run-the-web-app"></a>Uruchamianie aplikacji sieci Web

1. Upewnij się, że emulator usługi Azure Cosmos DB działa.

2. Otwórz okno terminalu i za pomocą polecenia `cd` przejdź do katalogu, w którym jest zapisana aplikacja.

3. Następnie ustaw zmienną środowiskową dla aplikacji Flask za pomocą polecenia `set FLASK_APP=app.py` lub `export FLASK_APP=app.py`, jeśli używasz komputera Mac.

4. Uruchom aplikację za pomocą polecenia `flask run` i przejdź do adresu [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Dodawaj oraz usuwaj zadania i sprawdzaj, jak są dodawane i zmieniane w kolekcji.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Jeśli chcesz przetestować kod na aktywnym koncie usługi Azure Cosmos DB, przejdź do witryny Azure Portal, aby utworzyć konto i uzyskać informacje o parametrach połączenia. Następnie skopiuj je do aplikacji.

1. W witrynie [Azure Portal](http://portal.azure.com/), korzystając ze swojego konta usługi Azure Cosmos DB, kliknij na lewym panelu nawigacyjnym pozycję **Parametry połączenia**, a następnie pozycję **Klucze odczytu i zapisu**. W następnym kroku, korzystając z przycisków kopiowania dostępnych po prawej stronie ekranu, skopiujesz nazwę użytkownika, hasło i hosta do pliku Dal.cs.

2. Otwórz plik **app.py** w katalogu głównym.

3. Skopiuj wartość **username** z portalu (przy użyciu przycisku kopiowania) i przypisz ją do klucza **name** w pliku **app.py**.

4. Następnie skopiuj wartość **connection string** (parametry połączenia) z portalu i przypisz ją do klucza MongoClient w pliku **app.py**.

5. Na końcu skopiuj wartość **password** z portalu i przypisz ją do klucza **password** w pliku **app.py**.

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. Możesz ją uruchomić tak jak wcześniej.

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

Aby wdrożyć tę aplikację, możesz utworzyć nową aplikację internetową na platformie Azure i włączyć ciągłe wdrażanie z rozwidleniem tego repozytorium GitHub. Skorzystaj z tego [samouczka](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), aby skonfigurować ciągłe wdrażanie za pomocą usługi GitHub na platformie Azure.

W przypadku wdrażania na platformie Azure należy usunąć klucze aplikacji i upewnić się, że poniższa sekcja nie jest wyłączona przez oznaczenie jej jako komentarza:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Następnie należy dodać do ustawień aplikacji odpowiednie wartości MONGOURL, MONGO_PASSWORD i MONGO_USERNAME. Aby dowiedzieć się więcej o ustawieniach aplikacji w usłudze Azure Web Apps, skorzystaj z tego [samouczka](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings).

Jeśli nie chcesz tworzyć rozwidlenia tego repozytorium, możesz też kliknąć przycisk „Wdrażanie na platformie Azure” poniżej. Przejdziesz wtedy na platformę Azure i skonfigurujesz ustawienia aplikacji przy użyciu informacji dotyczących używanego konta usługi Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> Jeśli planujesz przechowywanie kodu w usłudze GitHub lub w innych rozwiązaniach kontroli źródła, pamiętaj o usunięciu z kodu parametrów połączenia. Można je zamiast tego konfigurować za pomocą ustawień aplikacji internetowej.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz w przyszłości korzystać z tej aplikacji, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono sposób tworzenia konta usługi Azure Cosmos DB i uruchamiania aplikacji Flask za pomocą interfejsu API dla bazy danych MongoDB. Teraz można zaimportować dodatkowe dane na konto usługi Cosmos DB.

> [!div class="nextstepaction"]
> [Importuj dane do usługi Azure Cosmos DB na potrzeby interfejsu API usługi MongoDB](mongodb-migrate.md)
