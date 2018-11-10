---
title: Samouczek bazy danych MongoDB, usługi Angular i języka Node dla platformy Azure — część 5 | Microsoft Doc
description: Część 5 z serii samouczków o tworzeniu aplikacji bazy danych MongoDB przy użyciu usługi Angular i języka Node dla usługi Azure Cosmos DB przy użyciu dokładnie tych samych interfejsów API, które były używane dla bazy danych MongoDB
services: cosmos-db
author: johnpapa
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: jopapa
ms.custom: mvc
ms.openlocfilehash: e9a1b7951d111606d84e235864e3649a742e874e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741508"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Tworzenie aplikacji bazy danych MongoDB przy użyciu usług Angular i Azure Cosmos DB — część 5: łączenie z usługą Azure Cosmos DB przy użyciu wtyczki Mongoose

Ten wieloczęściowy samouczek pokazuje, jak utworzyć aplikację Node.js za pomocą programu Express oraz usługi Angular, a następnie podłączyć ją do konta [interfejsu API bazy danych MongoDB w usłudze Cosmos DB](mongodb-introduction.md).

Część 5 samouczka jest oparta na [Części 4](tutorial-develop-mongodb-nodejs-part4.md) i obejmuje następujące zadania:

> [!div class="checklist"]
> * Łączenie z usługą Azure Cosmos DB przy użyciu wtyczki Mongoose
> * Pobieranie informacji o parametrach połączenia usługi Cosmos DB
> * Tworzenie modelu elementów hero
> * Tworzenie usługi hero w celu pobrania danych elementów hero
> * Lokalne uruchamianie aplikacji

## <a name="video-walkthrough"></a>Przewodnik wideo

Możesz obejrzeć poniższy film, aby szybko zapoznać się z czynnościami opisanymi w tym artykule: 

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tej części samouczka upewnij się, że zostały wykonane czynności opisane w [Części 4](tutorial-develop-mongodb-nodejs-part4.md) samouczka.

> [!TIP]
> Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Łączenie z usługą Azure Cosmos DB przy użyciu wtyczki Mongoose

1. Zainstaluj moduł npm mongoose, czyli interfejs API używany do komunikowania się z bazą danych MongoDB.

    ```bash
    npm i mongoose --save
    ```

2. Teraz w folderze **server** utwórz nowy plik o nazwie **mongo.js**. Dodasz do tego pliku szczegóły połączenia konta usługi Cosmos DB.

3. Skopiuj poniższy kod do pliku **mongo.js**. Ten kod:

    * Wymaga wtyczki Mongoose.

    * Zastępuje obietnicę Mongo w celu użycia podstawowej obietnicy wbudowanej w wersji ES6/ES2015 i wyższych.

    * Wywołuje plik env, który umożliwia skonfigurowanie pewnych elementów w oparciu o to, czy pracujesz na etapie przygotowywania, produkcji, czy opracowywania. Utworzysz ten plik w następnej sekcji.

    * Obejmuje parametry połączenia bazy danych MongoDB, które są ustawiane w pliku env.

    * Tworzy funkcję connect, która wywołuje wtyczkę Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. W okienku eksploratora w obszarze **server** o utwórz folder o nazwie **environment**, a w folderze **environment** utwórz nowy plik o nazwie **environment.js**.

5. Na podstawie pliku mongo.js wiemy, że musimy uwzględnić elementy `dbName`, `key` i `cosmosPort`, dlatego skopiuj poniższy kod do pliku **environment.js**.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string-information"></a>Pobieranie informacji o parametrach połączenia

1. W pliku **environment.js** zmień wartość `port` na 10255. (Port usługi Cosmos DB można znaleźć w witrynie Azure Portal)

    ```javascript
    const port = 10255;
    ```

2. W pliku **environment.js** zmień wartość `accountName` na nazwę konta usługi Azure Cosmos DB utworzonego w ramach [kroku 4](tutorial-develop-mongodb-nodejs-part4.md). 

3. Pobierz klucz podstawowy konta usługi Azure Cosmos DB, używając następującego polecenia interfejsu wiersza polecenia w oknie terminala: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>` to nazwa konta usługi Azure Cosmos DB utworzonego w ramach [kroku 4](tutorial-develop-mongodb-nodejs-part4.md).

4. Skopiuj klucz podstawowy do pliku environment.js jako wartość `key`.

    Aplikacja ma teraz wszystkie informacje wymagane do nawiązania połączenia z usługą Azure Cosmos DB. Te informacje można również pobrać w portalu. Aby uzyskać więcej informacji, zobacz [Get the MongoDB connection string to customize (Pobieranie parametrów połączenia bazy danych MongoDB do dostosowania)](connect-mongodb-account.md#GetCustomConnection). Nazwa użytkownika w portalu jest taka sama jak element dbName w pliku environments.js. 

## <a name="create-a-hero-model"></a>Tworzenie modelu elementów hero

1. W okienku eksploratora utwórz plik **hero.model.js** w folderze **server**.

2. Skopiuj poniższy kod do pliku **hero.model.js**. Ten kod zawiera następujące funkcje:

   * Wymaga wtyczki Mongoose.
   * Tworzy nowy schemat z identyfikatorem, nazwą i powiedzeniem.
   * Tworzy model przy użyciu schematu.
   * Eksportuje model. 
   * Nazwij kolekcję Heroes (zamiast Heros — byłaby to nazwa domyślna kolekcji zgodna z regułami wtyczki Mongoose dotyczącymi nazw w liczbie mnogiej).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Tworzenie usługi hero

1. W okienku eksploratora utwórz plik **hero.service.js** w folderze **server**.

2. Skopiuj poniższy kod do pliku **hero.service.js**. Ten kod:

   * Pobiera właśnie utworzony model
   * Nawiązuje połączenie z bazą danych
   * Tworzy zmienną docquery, która używa metody hero.find do definiowania zapytania zwracającego wszystkie elementy hero.
   * Uruchamia zapytanie przy użyciu pliku docquery.exec z obietnicą w celu uzyskania listy wszystkich elementów hero, gdzie stan odpowiedzi to 200. 
   * Jeśli stan ma wartość 500, wysyła z powrotem komunikat o błędzie
   * Ponieważ używamy modułów, nastąpi pobranie elementów hero. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>Dodawanie usługi hero do pliku routes.js

1. W programie Visual Studio Code w pliku **routes.js** skomentuj funkcję `res.send`, która wysyła przykładowe dane elementu hero, i dodaj wiersz w celu wywołania w zamian funkcji `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. W pliku **routes.js** wymagaj usługi hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. W pliku **hero.service.js** zaktualizuj funkcję getHeroes, aby pobrać parametry `req` i `res`, w następujący sposób:

    ```javascript
    function getHeroes(req, res) {
    ```

    Poświęćmy teraz chwilę na zapoznanie się z łańcuchem wywołań. Najpierw przechodzimy do pliku `index.js`, który konfiguruje serwer węzłów, i zauważamy, że konfiguruje on i definiuje trasy. Plik routes.js komunikuje się następnie z usługą hero i informuje ją o konieczności pobrania funkcji, takich jak getHeroes, oraz przekazania żądania i odpowiedzi. Tutaj plik hero.service.js pobierze model i nawiąże połączenie z usługą Mongo, a następnie wykona wywołaną funkcję getHeroes i zwróci odpowiedź 200. Następnie będzie przechodzić z powrotem przez łańcuch. 

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Teraz uruchommy aplikację ponownie. W programie Visual Studio Code zapisz wszystkie zmiany, kliknij przycisk **Debug** (Debuguj) ![Ikona Debug (Debuguj) w programie Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) po lewej stronie, a następnie kliknij przycisk **Start Debugging** (Rozpocznij debugowanie) ![Ikona Debug (Debuguj) w programie Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Przejdź teraz do przeglądarki, otwórz pozycję Narzędzia programistyczne i kartę Sieć, a następnie przejdź do lokalizacji http://localhost:3000, w której znajduje się aplikacja.

    ![Nowe konto usługi Azure Cosmos DB w witrynie Azure Portal](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   W aplikacji nie są jeszcze przechowywane żadne elementy hero, ale w ramach następnego kroku w samouczku dodamy funkcjonalność Put, Push and Delete, dzięki której zyskamy możliwość dodawania, aktualizowania i usuwania elementów hero z interfejsu użytkownika przy użyciu połączeń wtyczki Mongoose z naszą bazą danych usługi Azure Cosmos DB. 

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Użyto interfejsów API wtyczki Mongoose do połączenia aplikacji Heroes z usługą Azure Cosmos DB 
> * Dodano funkcjonalność pobierania elementów hero do aplikacji

Możesz przejść do następnej części samouczka, aby dodać do aplikacji funkcje Post, Put i Delete.

> [!div class="nextstepaction"]
> [Dodawanie funkcji Post, Put i Delete do aplikacji](tutorial-develop-mongodb-nodejs-part6.md)
