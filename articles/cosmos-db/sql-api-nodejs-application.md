---
title: Tworzenie aplikacji internetowej środowiska Node.js dla usługi Azure Cosmos DB | Microsoft Docs
description: W tym samouczku środowiska Node.js przedstawiono, jak przy użyciu usługi Microsoft Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji internetowej Node.js Express hostowanej w usłudze Azure Websites.
keywords: Programowanie aplikacji, samouczek bazy danych, nauka node.js, samouczek node.js
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: d18e6dd9464ef103157a8532215fa797ab282437
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797485"
---
# <a name="_Toc395783175"></a>Tworzenie aplikacji internetowej Node.js za pomocą usługi Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

W tym samouczku środowiska Node.js pokazano, jak przy użyciu usługi Azure Cosmos DB oraz interfejsu API SQL przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji Node.js Express hostowanej w usłudze Azure Websites. Utworzysz prostą, opartą na sieci Web aplikację do zarządzania zadaniami (aplikację ToDo), która umożliwia tworzenie, pobieranie i kończenie zadań. Zadania są przechowywane jako dokumenty JSON w usłudze Azure Cosmos DB. Ten samouczek zawiera szczegółowe omówienie tworzenia i rozwoju aplikacji oraz objaśnienie poszczególnych fragmentów kodu.

![Zrzut ekranu aplikacji My Todo List utworzonej w tym samouczku środowiska Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Nie masz czasu na ukończenie tego samouczka i po prostu chcesz uzyskać kompletne rozwiązanie? Nie ma problemu, możesz pobrać kompletne przykładowe rozwiązanie z witryny [GitHub][GitHub]. Aby uzyskać instrukcje dotyczące uruchomienia aplikacji, wystarczy przeczytać plik [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Wymagania wstępne
> [!TIP]
> Ten samouczek środowiska Node.js zakłada, że masz już pewne doświadczenie w korzystaniu ze środowiska Node.js i usługi Azure Websites.
> 
> 

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] w wersji 0.10.29 lub nowszej. Zalecamy środowisko Node.js w wersji 6.10 lub nowszej.
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
   
        express todo
4. Otwórz nowy katalog **todo** i zainstaluj zależności.
   
        cd todo
        npm install
5. Uruchom nową aplikację.
   
        npm start
6. Swoją nową aplikację możesz wyświetlić, przechodząc w przeglądarce na adres [http://localhost:3000](http://localhost:3000).
   
    ![Poznaj środowisko Node.js — zrzut ekranu aplikacji Hello World w oknie przeglądarki](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

    Następnie, aby zatrzymać aplikację, w oknie terminalu naciśnij klawisze CTRL+C, po czym (tylko na komputerach z systemem Windows) naciśnij klawisz **Y** w celu zakończenia zadania wsadowego.

## <a name="_Toc395783179"></a>Krok 3. Instalowanie dodatkowych modułów
Plik **package.json** jest jednym z plików utworzonych w folderze głównym projektu. Ten plik zawiera listę dodatkowych modułów, które są wymagane dla aplikacji Node.js. Później, podczas wdrażania tej aplikacji w usłudze Azure Websites, ten plik jest używany do określenia, które moduły muszą być zainstalowane na platformie Azure w celu obsługi tej aplikacji. Nadal trzeba zainstalować jeszcze dwa pakiety dla tego samouczka.

1. Z poziomu terminala zainstaluj moduł **async** za pośrednictwem menedżera npm.
   
        npm install async --save
2. Zainstaluj moduł **documentdb** za pomocą menedżera npm. To jest moduł, w którym dzieje się cała „magia” usługi Azure Cosmos DB.
   
        npm install documentdb --save

## <a name="_Toc395783180"></a>Krok 4. Korzystanie z usługi Azure Cosmos DB w aplikacji Node
To kończy całą wstępną instalację i konfigurację. Teraz przejdźmy do najważniejszej części tego samouczka, czyli napisania kodu za pomocą usługi Azure Cosmos DB.

### <a name="create-the-model"></a>Tworzenie modelu
1. W katalogu projektu utwórz nowy katalog o nazwie **models** w tym samym katalogu, w którym znajduje się plik package.json.
2. W katalogu **models** utwórz nowy plik o nazwie **task-model.js**. Ten plik zawiera model dla zadań tworzonych przez naszą aplikację.
3. W tym samym katalogu **models** utwórz nowy plik o nazwie **cosmosdb-manager.js**. Ten plik będzie zawierał pewną ilość przydatnego kodu do ponownego wykorzystania, który będzie używany w naszej aplikacji. 
4. Skopiuj poniższy kod do pliku **cosmosdb-manager.js**.
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;

    module.exports = {
    getOrCreateDatabase: (client, databaseId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: databaseId }]
        };

        client.queryDatabases(querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let databaseSpec = { id: databaseId };
            client.createDatabase(databaseSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    },

    getOrCreateCollection: (client, databaseLink, collectionId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id=@id',
        parameters: [{ name: '@id', value: collectionId }]
        };

        client.queryCollections(databaseLink, querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let collectionSpec = { id: collectionId };
            client.createCollection(databaseLink, collectionSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    }
    };
    ```
5. Zapisz i zamknij plik **cosmosdb-manager.js**.
6. Na początku pliku **task-model.js** dodaj następujący kod, aby odwołać się do elementu **DocumentDBClient** i pliku **cosmosdb-manager.js**, które zostały utworzone powyżej: 

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager.js');
    ```
7. Następnie dodasz kod w celu zdefiniowania i wyeksportowania obiektu Task. Jest on odpowiedzialny za inicjowanie obiektu Task oraz konfigurowanie bazy danych i kolekcji dokumentów, które będą używane.  

    ```nodejs
    function TaskModel(documentDBClient, databaseId, collectionId) {
      this.client = documentDBClient;
      this.databaseId = databaseId;
      this.collectionId = collectionId;
   
      this.database = null;
      this.collection = null;
    }
   
    module.exports = TaskModel;
    ```
8. Następnie dodaj poniższy kod, aby zdefiniować dodatkowe metody dla obiektu Task umożliwiające interakcje z danymi przechowywanymi w usłudze Azure Cosmos DB.

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager');

    function TaskModel(documentDBClient, databaseId, collectionId) {
    this.client = documentDBClient;
    this.databaseId = databaseId;
    this.collectionId = collectionId;

    this.database = null;
    this.collection = null;
    }

    TaskModel.prototype = {
    init: function(callback) {
        let self = this;

        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
        if (err) {
            callback(err);
        } else {
            self.database = db;
            docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
            if (err) {
                callback(err);
            } else {
                self.collection = coll;
            }
            });
        }
        });
    },

    find: function(querySpec, callback) {
        let self = this;

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results);
        }
        });
    },

    addItem: function(item, callback) {
        let self = this;

        item.date = Date.now();
        item.completed = false;

        self.client.createDocument(self.collection._self, item, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            callback(null, doc);
        }
        });
    },

    updateItem: function(itemId, callback) {
        let self = this;

        self.getItem(itemId, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            doc.completed = true;

            self.client.replaceDocument(doc._self, doc, function(err, replaced) {
            if (err) {
                callback(err);
            } else {
                callback(null, replaced);
            }
            });
        }
        });
    },

    getItem: function(itemId, callback) {
        let self = this;
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: itemId }]
        };

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results[0]);
        }
        });
    }
    };

    module.exports = TaskModel;
    ```
9. Zapisz i zamknij plik **task-model.js**. 

### <a name="create-the-controller"></a>Tworzenie kontrolera
1. W katalogu **routes** projektu utwórz nowy plik o nazwie **tasklist.js**. 
2. Dodaj następujący kod do pliku **tasklist.js**. Służy on do ładowania modułów DocumentDBClient i async, które są używane przez plik **tasklist.js**. Definiuje również funkcję **TaskList**, która przekazuje wystąpienie obiektu **Task** zdefiniowanego wcześniej:
   
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let async = require('async');

    function TaskList(taskModel) {
    this.taskModel = taskModel;
    }

    module.exports = TaskList;
    ```
3. Kontynuuj dodawanie do pliku **tasklist.js** przez dodanie metod **showTasks, addTask** i **completeTasks**:
   
   ```nodejs
    TaskList.prototype = {
    showTasks: function(req, res) {
        let self = this;

        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.completed=@completed',
        parameters: [
            {
            name: '@completed',
            value: false
            }
        ]
        };

        self.taskModel.find(querySpec, function(err, items) {
        if (err) {
            throw err;
        }

        res.render('index', {
            title: 'My ToDo List ',
            tasks: items
        });
        });
    },

    addTask: function(req, res) {
        let self = this;
        let item = req.body;

        self.taskModel.addItem(item, function(err) {
        if (err) {
            throw err;
        }

        res.redirect('/');
        });
    },

    completeTask: function(req, res) {
        let self = this;
        let completedTasks = Object.keys(req.body);

        async.forEach(
        completedTasks,
        function taskIterator(completedTask, callback) {
            self.taskModel.updateItem(completedTask, function(err) {
            if (err) {
                callback(err);
            } else {
                callback(null);
            }
            });
        },
        function goHome(err) {
            if (err) {
            throw err;
            } else {
            res.redirect('/');
            }
        }
        );
    }
    };
    ```        
4. Zapisz i zamknij plik **tasklist.js**.

### <a name="add-configjs"></a>Dodawanie pliku config.js
1. W katalogu projektu utwórz nowy plik o nazwie **config.js**.
2. Dodaj następujący kod do pliku **config.js**. Służy on do definiowania ustawień konfiguracji i wartości potrzebnych dla aplikacji.
   
    ```nodejs
    let config = {}
   
    config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.databaseId = "ToDoList";
    config.collectionId = "Items";
   
    module.exports = config;
    ```
3. W pliku **config.js** zaktualizuj wartości kluczy HOST i AUTH_KEY przy użyciu wartości znajdujących się na stronie Klucze Twojego konta usługi Azure Cosmos DB w witrynie [Microsoft Azure Portal](https://portal.azure.com).
4. Zapisz i zamknij plik **config.js**.

### <a name="modify-appjs"></a>Modyfikowanie pliku app.js
1. W katalogu projektu otwórz plik **app.js**. Ten plik został utworzony wcześniej podczas tworzenia aplikacji internetowej platformy Express.
2. Dodaj następujący kod na górze pliku **app.js**:
   
    ```nodejs
    var DocumentDBClient = require('documentdb').DocumentClient;
    var config = require('./config');
    var TaskList = require('./routes/tasklist');
    var TaskModel = require('./models/taskModel');
    ```
3. Ten kod definiuje plik konfiguracji, który ma być używany, oraz odczytuje wartości z tego pliku i umieszcza je w pewnych zmiennych, których będziemy wkrótce używać.
4. Zastąp następujące dwa wiersze w pliku **app.js**:
   
    ```nodejs
    app.use('/', index);
    app.use('/users', users); 
    ```
   
    następującym fragmentem kodu:
   
    ```nodejs
    let docDbClient = new DocumentDBClient(config.host, {
        masterKey: config.authKey
    });
    let taskModel = new TaskModel(docDbClient, config.databaseId, config.collectionId);
    let taskList = new TaskList(taskModel);
    taskModel.init();
   
    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    app.set('view engine', 'jade');
    ```
5. Te wiersze definiują nowe wystąpienie obiektu **TaskModel** z nowym połączeniem z usługą Azure Cosmos DB (przy użyciu wartości odczytanych z pliku **config.js**), inicjują obiekt zadania, a następnie wiążą akcje formularza z metodami w kontrolerze **TaskList**. 
6. Na koniec zapisz i zamknij plik **app.js**. To już prawie koniec.

## <a name="_Toc395783181"></a>Krok 5. Tworzenie interfejsu użytkownika
Teraz skupimy się na tworzeniu interfejsu użytkownika, aby użytkownik mógł faktycznie wchodzić w interakcję z naszą aplikacją. Utworzona aplikacja Express używa aparatu widoku **Jade**. Więcej informacji na temat aparatu Jade można znaleźć w witrynie [http://jade-lang.com/](http://jade-lang.com/).

1. Plik **layout.jade** w katalogu **views** jest używany jako szablon globalny dla innych plików **jade**. W tym kroku zmodyfikujesz go w celu używania struktury [Twitter Bootstrap](https://github.com/twbs/bootstrap), która jest zestawem narzędzi ułatwiającym projektowanie dobrze wyglądającej witryny sieci Web. 
2. Otwórz plik **layout.jade** znajdujący się w folderze **views** i zastąp jego zawartość następującym kodem:

    ```
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
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Rozszerza on układ i udostępnia zawartość dla symbolu zastępczego **content**, który wcześniej widzieliśmy w pliku **layout.jade**.
   
W tym układzie utworzyliśmy dwa formularze HTML.

Pierwszy formularz zawiera tabelę danych i przycisk umożliwiający aktualizowanie elementów przez publikowanie do metody **/completetask** kontrolera.
    
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
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app

