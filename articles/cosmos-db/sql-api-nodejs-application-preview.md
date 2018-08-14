---
title: Tworzenie aplikacji internetowej środowiska Node.js dla usługi Azure Cosmos DB | Microsoft Docs
description: W tym samouczku środowiska Node.js przedstawiono, jak przy użyciu usługi Microsoft Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji internetowej Node.js Express hostowanej w usłudze Azure Websites.
keywords: Programowanie aplikacji, samouczek bazy danych, nauka Node.js, samouczek Node.js
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 9f7744c7743107b3587bd63a6e7681a6f1e42c00
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39608974"
---
# <a name="_Toc395783175"></a>Tworzenie aplikacji internetowej Node.js za pomocą usługi Azure Cosmos DB i zestawu SDK dla platformy Node.js (wersja zapoznawcza)
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js 2.0 (wersja zapoznawcza)](sql-api-nodejs-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

W tym samouczku środowiska Node.js pokazano, jak przy użyciu konta interfejsu API SQL usługi Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji Node.js Express hostowanej w usłudze Azure Websites. W tym samouczku utworzysz prostą aplikację internetową (z listą zadań do wykonania), która umożliwia tworzenie, pobieranie i wykonywanie zadań. Zadania są przechowywane jako dokumenty JSON w usłudze Azure Cosmos DB. Na poniższej ilustracji znajduje się zrzut ekranu przedstawiający aplikację z listą zadań do wykonania:

![Zrzut ekranu aplikacji My Todo List utworzonej w tym samouczku środowiska Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

W tym samouczku przedstawiono sposób tworzenia konta interfejsu API SQL usługi Azure Cosmos DB za pomocą witryny Azure Portal. Następnie skompilujesz i uruchomisz aplikację internetową bazującą na zestawie SDK środowiska Node.js, utworzysz bazę danych, kontener i dodasz elementy do tego kontenera. Ten samouczek używa wersji 2.0 zestawu SDK środowiska Node.js, która jest obecnie dostępna w wersji zapoznawczej.

Gotowy przykład możesz też pobrać z usługi [GitHub][GitHub]. Aby uzyskać instrukcje dotyczące uruchomienia aplikacji, wystarczy przeczytać plik [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Wymagania wstępne

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] wersja 6.10 lub nowsza.
* [Generator Express](http://www.expressjs.com/starter/generator.html) (można go zainstalować za pomocą polecenia `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Krok 1. Tworzenie konta bazy danych usługi Azure Cosmos DB
Zacznijmy od utworzenia konta usługi Azure Cosmos DB. Jeśli masz już konto lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Krok 2. Tworzenie nowej aplikacji Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Krok 2. Tworzenie nowej aplikacji Node.js
Teraz nauczysz się, jak utworzyć podstawowy projekt aplikacji Hello World w środowisku Node.js przy użyciu platformy [Express](http://expressjs.com/).

1. Otwórz swój ulubiony terminal, na przykład wiersz polecenia środowiska Node.js.
2. Przejdź do katalogu, w którym chcesz przechowywać nową aplikację.
3. Użyj generatora platformy Express, aby wygenerować nową aplikację o nazwie **todo**.

   ```bash
   express todo
   ```
4. Otwórz nowy katalog **todo** i zainstaluj zależności.

   ```bash
   cd todo
   npm install
   ```
5. Uruchom nową aplikację.

   ```bash
   npm start
   ```

6. Swoją nową aplikację możesz wyświetlić, przechodząc w przeglądarce na adres [http://localhost:3000](http://localhost:3000).
   
    ![Poznaj środowisko Node.js — zrzut ekranu aplikacji Hello World w oknie przeglądarki](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Zatrzymaj aplikację, naciskając klawisze CTRL+C w oknie terminalu, i naciśnij klawisz **y** w celu zakończenia zadania wsadowego.

## <a name="_Toc395783179"></a>Krok 3. Instalowanie wymaganych modułów

Plik **package.json** jest jednym z plików utworzonych w folderze głównym projektu. Ten plik zawiera listę dodatkowych modułów, które są wymagane dla aplikacji Node.js. Później, podczas wdrażania tej aplikacji w usłudze Azure Websites, ten plik jest używany do określenia, które moduły muszą być zainstalowane na platformie Azure w celu obsługi tej aplikacji. Na potrzeby tego samouczka trzeba zainstalować jeszcze dwa pakiety.

1. Otwórz terminal i zainstaluj moduł **async** za pośrednictwem menedżera npm.

   ```bash
   npm install async --save
   ```

2. Zainstaluj moduł **@azure/cosmos** za pomocą menedżera npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Krok 4. Korzystanie z usługi Azure Cosmos DB w aplikacji Node
Teraz, po wykonaniu wstępnej instalacji i konfiguracji, napiszesz kod, który jest wymagany przez aplikację z listą zadań do wykonania do komunikowania się z usługą Azure Cosmos DB.

### <a name="create-the-model"></a>Tworzenie modelu
1. W katalogu głównym projektu utwórz nowy katalog o nazwie **models**.  

2. W katalogu **models** utwórz nowy plik o nazwie **taskDao.js**. Ten plik zawiera kod wymagany do utworzenia bazy danych i kontenera oraz definiuje metody służące do odczytu, aktualizacji, tworzenia i znajdywania zadań w usłudze Azure Cosmos DB. 

3. Skopiuj poniższy kod do pliku **taskDao.js**.

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Zapisz i zamknij plik **taskDao.js**.  

### <a name="create-the-controller"></a>Tworzenie kontrolera

1. W katalogu **routes** projektu utwórz nowy plik o nazwie **tasklist.js**.  

2. Dodaj następujący kod do pliku **tasklist.js**. Służy on do ładowania modułów CosmosClient i async, które są używane przez plik **tasklist.js**. Definiuje on również klasę **TaskList**, która jest przekazywana jako wystąpienie obiektu **TaskDao** zdefiniowanego wcześniej:
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Zapisz i zamknij plik **tasklist.js**.

### <a name="add-configjs"></a>Dodawanie pliku config.js

1. W katalogu głównym projektu utwórz nowy plik o nazwie **config.js**. 

2. Dodaj następujący kod do pliku **config.js**. Ten kod służy do definiowania ustawień konfiguracji i wartości potrzebnych dla aplikacji.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. W pliku **config.js** zaktualizuj wartości kluczy HOST i AUTH_KEY przy użyciu wartości znajdujących się na stronie Klucze Twojego konta usługi Azure Cosmos DB w witrynie [Microsoft Azure Portal](https://portal.azure.com). 

4. Zapisz i zamknij plik **config.js**.

### <a name="modify-appjs"></a>Modyfikowanie pliku app.js
1. W katalogu projektu otwórz plik **app.js**. Ten plik został utworzony wcześniej podczas tworzenia aplikacji internetowej platformy Express.  

2. Dodaj następujący kod do pliku **app.js**. Ten kod definiuje plik konfiguracji, który ma być używany, oraz odczytuje wartości z tego pliku i umieszcza je w pewnych zmiennych, których będziemy wkrótce używać. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Na koniec zapisz i zamknij plik **app.js**. To już prawie koniec.

## <a name="_Toc395783181"></a>Krok 5. Tworzenie interfejsu użytkownika
Teraz skupimy się na tworzeniu interfejsu użytkownika, aby użytkownik mógł faktycznie wchodzić w interakcję z naszą aplikacją. Utworzona aplikacja Express używa aparatu widoku **Jade**. Więcej informacji na temat aparatu Jade można znaleźć w witrynie [http://jade-lang.com/](http://jade-lang.com/).

1. Plik **layout.jade** w katalogu **views** jest używany jako szablon globalny dla innych plików **jade**. W tym kroku zmodyfikujesz go w celu używania struktury [Twitter Bootstrap](https://github.com/twbs/bootstrap), która jest zestawem narzędzi ułatwiającym projektowanie dobrze wyglądającej witryny sieci Web.  

2. Otwórz plik **layout.jade** znajdujący się w folderze **views** i zastąp jego zawartość następującym kodem:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Ten kod w praktyce określa, że aparat **Jade** ma renderować kod HTML dla aplikacji, i tworzy **blok** o nazwie **content**, w którym można udostępnić układ dla stron zawartości.

    Zapisz i zamknij ten plik **layout.jade**.

3. Teraz otwórz plik **index.jade**, definiujący widok, który będzie używany przez naszą aplikację, i zastąp zawartość pliku następującym kodem:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
       form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
   ```

Rozszerza on układ i udostępnia zawartość dla symbolu zastępczego **content**, który wcześniej widzieliśmy w pliku **layout.jade**.
   
W tym układzie utworzyliśmy dwa formularze HTML.

Pierwszy formularz zawiera tabelę danych i przycisk umożliwiający aktualizowanie elementów przez publikowanie do metody **/completeTask** kontrolera.
    
Drugi formularz zawiera dwa pola wejściowe i przycisk umożliwiający utworzenie nowego elementu przez publikowanie do metody **/addtask** kontrolera.

To powinno być wszystko, czego potrzebujemy, aby nasza aplikacja działała.

## <a name="_Toc395783181"></a>Krok 6. Uruchamianie aplikacji lokalnie
1. Aby przetestować aplikację na komputerze lokalnym, w terminalu wykonaj polecenie `npm start` w celu uruchomienia aplikacji, a następnie odśwież stronę przeglądarki [http://localhost:3000](http://localhost:3000). Strona powinna teraz wyglądać podobnie jak na poniższym obrazie:
   
    ![Zrzut ekranu aplikacji MyTodo List w oknie przeglądarki](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > W przypadku wystąpienia błędu dotyczącego wcięcia w pliku layout.jade bądź index.jade upewnij się, że dwa pierwsze wiersze w obu plikach są wyrównane do lewej, bez spacji. Jeśli przed dwoma pierwszymi wierszami występują spacje, usuń je, zapisz oba pliki, a następnie odśwież okno przeglądarki. 

2. Wprowadź nowe zadanie przy użyciu pól Item (Element), Item Name (Nazwa elementu) i Category (Kategoria), a następnie kliknij przycisk **Add Item** (Dodaj element). Spowoduje to utworzenie w usłudze Azure Cosmos DB dokumentu z tymi właściwościami. 
3. Ta strona powinna zostać zaktualizowana w celu wyświetlenia nowo utworzonego elementu na liście ToDo.
   
    ![Zrzut ekranu aplikacji z nowym elementem na liście ToDo](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Aby zakończyć zadanie, po prostu zaznacz pole wyboru w kolumnie Complete (Zakończ), a następnie kliknij przycisk **Update tasks** (Aktualizuj zadania). Spowoduje to zaktualizowanie utworzonego już dokumentu i usunięcie go z widoku.

5. Aby zatrzymać aplikację, naciśnij klawisze CTRL+C w oknie terminalu, po czym naciśnij klawisz **Y** w celu zakończenia zadania wsadowego.

## <a name="_Toc395783182"></a>Krok 7. Wdrażanie projektu tworzenia aplikacji w usłudze Azure Websites
1. Jeśli jeszcze tego nie zrobiono, włącz repozytorium Git dla usługi Azure Websites. Instrukcje, jak to zrobić, można znaleźć w temacie [Local Git Deployment to Azure App Service](../app-service/app-service-deploy-local-git.md) (Lokalne wdrażanie przy użyciu systemu Git w usłudze Azure App Service).
2. Dodaj witrynę sieci Web platformy Azure jako element zdalny narzędzia Git.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Wdróż przez wypchnięcie do elementu zdalnego.
   
        git push azure master
4. W ciągu kilku sekund narzędzie Git zakończy publikowanie aplikacji internetowej i uruchomi przeglądarkę, w której możesz zobaczyć swoje dzieło uruchomione na platformie Azure!

    Gratulacje! Udało Ci się utworzyć swoją pierwszą aplikację internetową Node.js Express za pomocą usługi Azure Cosmos DB i opublikować ją w usłudze Azure Websites.

    Jeśli chcesz pobrać kompletną aplikację referencyjną dla tego samouczka lub się do niej odwołać, to jest ona dostępna do pobrania w repozytorium [GitHub][GitHub].

## <a name="_Toc395637775"></a>Następne kroki

* Czy chcesz wykonać testowanie wydajnościowe i skalowania przy użyciu usługi Azure Cosmos DB? Zobacz [Performance and Scale Testing with Azure Cosmos DB](performance-testing.md) (Testowanie wydajności i skali w usłudze Azure Cosmos DB)
* Dowiedz się, jak [monitorować konto usługi Azure Cosmos DB](monitor-accounts.md).
* Uruchom zapytania względem naszego przykładowego zestawu danych na [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo).
* Zapoznaj się z [dokumentacją usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

