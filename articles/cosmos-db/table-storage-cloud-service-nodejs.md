---
title: 'Magazyn tabel Azure: tworzenie aplikacji sieci web Node.js | Dokumentacja firmy Microsoft'
description: Samouczek, która tworzy w aplikacji sieci Web z samouczka Express przez dodanie usługi Azure Storage i moduł Azure.
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2018
ms.author: mimig
ms.openlocfilehash: b63f6b3be2e4576b304c1a73ff326a937815b27e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-table-storage-nodejs-web-application"></a>Magazyn tabel Azure: aplikacja sieci Web Node.js
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Przegląd
W tym samouczku aplikacji utworzony w [aplikacji sieci Web Node.js za pomocą ekspresowego] samouczka został rozszerzony za pomocą biblioteki klienta Microsoft Azure dla środowiska Node.js do pracy z usługami zarządzania danych. Aplikację można rozszerzyć przez tworzenie aplikacji opartych na sieci web listy zadań, które można wdrożyć na platformie Azure. Lista zadań pozwala użytkownikowi na pobieranie zadań, Dodaj nowe zadania i oznaczanie zadań jako ukończone.

Elementy zadania są przechowywane w usłudze Azure Storage lub bazy danych Azure rozwiązania Cosmos. Azure magazynu i bazy danych rozwiązania Cosmos Azure zapewnia magazynu danych bez struktury, która jest odporna na uszkodzenia i wysokiej dostępności. Azure magazynu i bazy danych rozwiązania Cosmos Azure obejmują kilka struktury danych, gdzie można przechowywać i uzyskać dostęp do danych. Można użyć magazynu i usług Azure DB rozwiązania Cosmos z interfejsów API, dołączony do zestawu Azure SDK dla środowiska Node.js lub za pośrednictwem interfejsów API REST. Aby uzyskać więcej informacji, zobacz [przechowywanie i uzyskiwanie dostępu do danych na platformie Azure].

Ten samouczek zakłada, że zostały wykonane [aplikacji sieci Web Node.js] i [Node.js z Express][aplikacji sieci Web Node.js za pomocą ekspresowego] samouczki.

Zawiera ona następujące informacje:

* Jak pracować z aparatu Jade szablonu
* Jak pracować z usług Azure zarządzanie danymi

Poniższy zrzut ekranu przedstawia gotową aplikację:

![Ukończono strony sieci web w programie internet explorer](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Ustawianie poświadczeń magazynu w pliku Web.Config
Należy podać poświadczenia magazynu uzyskać dostęp do usługi Azure Storage lub bazy danych Azure rozwiązania Cosmos. Jest to realizowane przy użyciu ustawienia pliku web.config aplikacji.
Ustawienia pliku web.config są przekazywane jako zmienne środowiskowe do węzła, które następnie są odczytywane przez zestaw SDK usługi Azure.

> [!NOTE]
> Poświadczenia magazynu są używane tylko w przypadku, gdy aplikacja jest wdrażana na platformie Azure. Podczas uruchamiania w emulatorze, aplikacja używa emulatora magazynu.
>
>

Wykonaj poniższe kroki, aby pobrać poświadczenia konta magazynu i dodaj je do ustawienia pliku web.config:

1. Jeśli go nie jest jeszcze otwarty, uruchom program PowerShell Azure z **Start** menu rozwijając **wszystkie programy, Azure**, kliknij prawym przyciskiem myszy **programu Azure PowerShell**, a następnie wybierz **Uruchom jako Administrator**.
2. Przejdź do folderu zawierającego aplikację. Na przykład C:\\węzła\\tasklist\\WebRole1.
3. W oknie programu Powershell systemu Azure wprowadź następujące polecenie cmdlet można pobrać informacji o koncie magazynu:

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts
    ```

   Poprzednie polecenie cmdlet pobiera listę kont magazynu i konto klucze skojarzone z usługi hostowanej.

   > [!NOTE]
   > Zestaw SDK usługi Azure tworzy konto magazynu, podczas wdrażania usługi, konto magazynu powinna już istnieć z wdrożenia aplikacji w poprzednim przewodnikach.
   >
   >
4. Otwórz **ServiceDefinition.csdef** pliku zawierającego ustawienia środowiska, które są używane, gdy aplikacja jest wdrażana na platformie Azure:

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. Wstaw następujący blok w obszarze **środowiska** elementu, zastępując {konta MAGAZYNU} i {klucz dostępu do MAGAZYNU} z nazwą konta i klucz podstawowy dla konta magazynu, którego chcesz użyć do wdrożenia:

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![Zawartość pliku web.cloud.config](./media/table-storage-cloud-service-nodejs/node37.png)

6. Zapisz plik i zamknij Notatnik.

### <a name="install-additional-modules"></a>Instalowanie dodatkowych modułów
1. Użyj następującego polecenia, aby zainstalować [azure], [uuid węzła], [nconf] i moduły [async] lokalnie również jako, aby zapisać wpis dla nich **package.json** pliku:

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  Dane wyjściowe tego polecenia powinien być podobny do następującego:

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>Korzystanie z usługi tabeli w aplikacji node
W tej sekcji aplikacji w warstwie podstawowa utworzony przez **express** polecenia zostanie rozszerzony przez dodanie **task.js** plik zawierający model dla zadań. Zmodyfikuj istniejącą **app.js** plików i utworzyć nową **tasklist.js** pliku, który korzysta z modelu.

### <a name="create-the-model"></a>Tworzenie modelu
1. W **WebRole1** katalogu, Utwórz nowy katalog o nazwie **modele**.
2. W **modele** katalogu, Utwórz nowy plik o nazwie **task.js**. Ten plik zawiera model dla zadań tworzonych przez aplikację.
3. Na początku **task.js** pliku, Dodaj następujący kod, aby odwołać wymagane biblioteki:

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. Następnie dodaj kod w celu zdefiniowania i wyeksportowania obiektu Task. Obiekt zadania jest odpowiedzialny za nawiązywania połączenia z tabeli.

    ```nodejs
    module.exports = Task;

    function Task(storageClient, tableName, partitionKey) {
      this.storageClient = storageClient;
      this.tableName = tableName;
      this.partitionKey = partitionKey;
      this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
        if(error) {
          throw error;
        }
      });
    };
    ```

5. Następnie dodaj następujący kod, aby zdefiniować dodatkowe metody dla obiektu Task umożliwiające interakcje z danymi przechowywanymi w tabeli:

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(this.tablename, query, null, null, function entitiesQueried(error, result) {
          if(error) {
            callback(error);
          } else {
            callback(null, result.entries);
          }
        });
      },

      addItem: function(item, callback) {
        self = this;
        // use entityGenerator to set types
        // NOTE: RowKey must be a string type, even though
        // it contains a GUID in this example.
        var itemDescriptor = {
          PartitionKey: entityGen.String(self.partitionKey),
          RowKey: entityGen.String(uuid()),
          name: entityGen.String(item.name),
          category: entityGen.String(item.category),
          completed: entityGen.Boolean(false)
        };

        self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
          if(error){
            callback(error);
          }
          callback(null);
        });
      },

      updateItem: function(rKey, callback) {
        self = this;
        self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
          if(error) {
            callback(error);
          }
          entity.completed._ = true;
          self.storageClient.replaceEntity(self.tableName, entity, function entityUpdated(error) {
            if(error) {
              callback(error);
            }
            callback(null);
          });
        });
      }
    }
    ```

6. Zapisz i Zamknij **task.js** pliku.

### <a name="create-the-controller"></a>Tworzenie kontrolera
1. W **WebRole1/trasy** katalogu, Utwórz nowy plik o nazwie **tasklist.js** i otwórz go w edytorze tekstów.
2. Dodaj następujący kod do pliku **tasklist.js**. Ten kod ładowania modułów azure i async, które są używane przez **tasklist.js** i definiuje **TaskList** funkcji, która została przekazana wystąpienia **zadań** zdefiniowanego wcześniej:

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. Kontynuuj dodawanie do **tasklist.js** przez dodanie metody używane do **showTasks**, **addTask**, i **completeTasks**:

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = new azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
        var item = {
            name: req.body.name, 
            category: req.body.category
        };
        self.task.addItem(item, function itemAdded(error) {
          if(error) {
            throw error;
          }
          res.redirect('/');
        });
      },

      completeTask: function(req,res) {
        var self = this;
        var completedTasks = Object.keys(req.body);
        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
          self.task.updateItem(completedTask, function itemsUpdated(error) {
            if(error){
              callback(error);
            } else {
              callback(null);
            }
          });
        }, function goHome(error){
          if(error) {
            throw error;
          } else {
            res.redirect('/');
          }
        });
      }
    }
    ```

4. Zapisz **tasklist.js** pliku.

### <a name="modify-appjs"></a>Modyfikowanie pliku app.js
1. W **WebRole1** katalogu, otwórz **app.js** plik w edytorze tekstów.
2. Na początku pliku Dodaj następujące polecenie, aby załadować moduł azure i ustaw klucz nazwy i partycji tabeli:

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. W pliku app.js przewiń w dół, do której występuje następujący wiersz:

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    Zastąp następujący kod poprzednich wierszy. Ten kod inicjuje wystąpienie klasy <strong>zadań</strong> z połączeniem z konta magazynu. <strong>Zadań</strong> jest przekazywana do <strong>TaskList</strong>, który używa do komunikacji z usługą tabeli:

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. Zapisz **app.js** pliku.

### <a name="modify-the-index-view"></a>Zmodyfikuj widok indeksu
1. Przejdź do **widoków** katalogu i Otwórz **index.jade** plik w edytorze tekstów.
2. Zastąp zawartość **index.jade** pliku następującym kodem. Ten kod definiuje widok, aby wyświetlić istniejące zadania i definiuje formularz służący do dodawania nowych zadań i oznaczenie istniejących jako ukończone.

    ```
    extends layout

    block content
      h1= title
      br

      form(action="/completetask", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Date
            td Complete
          if tasks == []
            tr
              td
          else
            each task in tasks
              tr
                td #{task.name._}
                td #{task.category._}
                - var day   = task.Timestamp._.getDate();
                - var month = task.Timestamp._.getMonth() + 1;
                - var year  = task.Timestamp._.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
        button.btn(type="submit") Update tasks
      hr
      form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
    ```

3. Zapisz i Zamknij **index.jade** pliku.

### <a name="modify-the-global-layout"></a>Modyfikowanie globalnych układu
Plik **layout.jade** w katalogu **views** jest używany jako szablon globalny dla innych plików **jade**. W tym kroku, należy zmodyfikować **layout.jade** plik [Twitter Bootstrap](https://github.com/twbs/bootstrap), która jest zestawem narzędzi ułatwiającym projektowanie nieuprzywilejowany wyglądającej witryny sieci Web.

1. Pobierz i Wyodrębnij pliki do [Twitter Bootstrap](http://getbootstrap.com/). Kopiuj **bootstrap.min.css** plik z **ładowania początkowego\\dist\\css** folder do **publicznego\\arkusze stylów** katalogu aplikacji tasklist.
2. Z **widoków** folder, otwórz **layout.jade** plik w edytorze tekstu i Zastąp zawartość następującym kodem:

    Tytuł head doctype html html = łącze do tytułu (rel = "stylesheet', href='/stylesheets/bootstrap.min.css) łącza (rel ="stylesheet', href='/stylesheets/style.css) body.app nav.navbar.navbar domyślną nagłówka div.navbar a.navbar-brand(href='/') zawartości bloku Moje zadania

3. Zapisz **layout.jade** pliku.

### <a name="running-the-application-in-the-emulator"></a>Działania aplikacji w emulatorze
Użyj następującego polecenia, aby uruchomić aplikację w emulatorze.

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

Przeglądarka otwiera i wyświetla następujące strony:

![Sieć web stronicowanej zatytułowany Moje listy zadań z tabeli zawierającej zadania i pola, aby dodać nowe zadanie.](./media/table-storage-cloud-service-nodejs/node44.png)

Użyj formularza, aby dodać elementy, lub usuń istniejące elementy oznaczając je jako ukończone.

## <a name="publishing-the-application-to-azure"></a>Publikowanie aplikacji Azure
W oknie programu Windows PowerShell Wywołaj następujące polecenie cmdlet, aby ponownie wdrożyć usługi hostowanej na platformie Azure.

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

Zastąp **myuniquename** z unikatową nazwę dla tej aplikacji. Zastąp **datacentername** nazwą centrum danych platformy Azure, takich jak **zachodnie stany USA**.

Po zakończeniu wdrażania powinny pojawić się odpowiedź podobna do następującej:

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

Określając **— uruchamianie** opcji w poprzednim poleceniu cmdlet przeglądarki otwiera i wyświetla aplikacji działających na platformie Azure po zakończeniu publikowania.

![Okno przeglądarki, strona mojej listy zadań. Adres URL wskazuje, że strona jest teraz obsługiwana na platformie Azure.](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Zatrzymywanie i usuwanie aplikacji
Po wdrożeniu aplikacji, można ją wyłączyć, możesz uniknąć kosztów lub skompilować i wdrożyć inne aplikacje w okresie bezpłatnego okresu próbnego.

Opłaty za wystąpienia ról sieci Web na platformie Azure są naliczane za godzinę korzystania z serwera.
Czas serwera jest używany po wdrożeniu aplikacji, nawet jeśli wystąpienia nie zostały uruchomione i są w stanie zatrzymania.

Poniższe kroki pokazują, jak zatrzymywać i usuwania aplikacji.

1. W oknie programu Windows PowerShell zatrzymaj wdrożenie usługi utworzone w poprzedniej sekcji za pomocą następującego polecenia cmdlet:

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   Zatrzymywanie usługi może potrwać kilka minut. Po zatrzymaniu usługi pojawi się komunikat wskazujący, że usługa została zatrzymana.

2. Aby usunąć usługę, wywołaj następujące polecenie cmdlet:

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   Po wyświetleniu monitu wpisz **Y**, aby usunąć usługę.

   Usuwanie usługi może potrwać kilka minut. Po usunięciu usługi, zostanie wyświetlony komunikat informujący, że usługa została usunięta.

[aplikacji sieci Web Node.js za pomocą ekspresowego]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[przechowywanie i uzyskiwanie dostępu do danych na platformie Azure]: https://docs.microsoft.com/azure/storage/
[aplikacji sieci Web Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/


