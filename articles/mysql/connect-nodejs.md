---
title: Połącz się przy użyciu pliku Node.js — usługa Azure Database for MySQL
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu Node.js, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 3/18/2020
ms.openlocfilehash: a8c4f84fe958c1b2762509432596fea772e39d7e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067933"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki start: łączenie danych i wykonywanie zapytań w bazie danych Usługi Azure dla aplikacji MySQL za pomocą aplikacji Node.js

W tym przewodniku Szybki start można połączyć się z usługą Azure Database for MySQL przy użyciu pliku Node.js. Następnie używasz instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

W tym temacie przyjęto założenie, że jesteś zaznajomiony z tworzenia przy użyciu Node.js, ale jesteś nowy do pracy z usługą Azure Database dla MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Usługa Azure Database dla serwera MySQL. [Utwórz usługę Azure Database dla serwera MySQL przy użyciu witryny Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) lub [utwórz usługę Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.](quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalowanie środowiska Node.js i łącznika programu MySQL

W zależności od platformy postępuj zgodnie z instrukcjami zawartymi w odpowiedniej sekcji, aby zainstalować [plik Node.js](https://nodejs.org). Użyj npm, aby zainstalować pakiet [mysql](https://www.npmjs.com/package/mysql) i jego zależności w folderze projektu.

### <a name="windows"></a>**Windows**

1. Odwiedź [stronę pobierania programu Node.js](https://nodejs.org/en/download/) i wybierz odpowiednią opcję Instalatora Windows.
2. Utwórz lokalny folder projektu, taki jak `nodejsmysql`. 
3. Otwórz wiersz polecenia, a następnie zmień katalog w folderze projektu, na przykład`cd c:\nodejsmysql\`
4. Uruchom narzędzie NPM, aby zainstalować bibliotekę mysql w folderze projektu.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Sprawdź instalację, sprawdzając tekst danych wyjściowych polecenia `npm list`. Numer wersji może się różnić po wydaniu nowych poprawek.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

1. Uruchom następujące polecenia, aby zainstalować środowisko **Node.js** i program **npm**, czyli menedżera pakietów dla środowiska Node.js.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Uruchom następujące polecenia, aby utworzyć folder projektu `mysqlnodejs` i zainstalować pakiet mysql w tym folderze.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Sprawdź instalację, sprawdzając tekst danych wyjściowych listy npm. Numer wersji może się różnić po wydaniu nowych poprawek.

### <a name="mac-os"></a>**Mac OS**

1. Wprowadź następujące polecenia, aby zainstalować rozwiązanie **brew**, czyli łatwy w użyciu menedżer pakietów dla systemu Mac OS X i środowiska **Node.js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Uruchom następujące polecenia, aby utworzyć folder projektu `mysqlnodejs` i zainstalować pakiet mysql w tym folderze.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Sprawdź instalację, sprawdzając tekst danych wyjściowych polecenia `npm list`. Numer wersji może się różnić po wydaniu nowych poprawek.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z usługą Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Z menu po lewej stronie w witrynie Azure portal wybierz pozycję **Wszystkie zasoby**, a następnie wyszukaj utworzony serwer (np. **mydemoserver**).
3. Wybierz nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 ![Nazwa serwera usługi Azure Database for MySQL](./media/connect-nodejs/server-name-azure-database-mysql.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Uruchamianie kodu JavaScript w środowisku Node.js

1. Wklej kod JavaScript do plików tekstowych i zapisz w folderze projektu z rozszerzeniem js (na przykład C:\nodejsmysql\createtable.js lub /home/username/nodejsmysql/createtable.js).
2. Otwórz wiersz polecenia lub powłokę bash, a następnie `cd nodejsmysql`zmień katalog w folderze projektu .
3. Aby uruchomić aplikację, wprowadź polecenie węzła, po `node createtable.js`którym następuje nazwa pliku, na przykład .
4. W systemie Windows, jeśli aplikacja Node nie znajduje się w ścieżce zmiennej środowiskowej, może być konieczne użycie pełnej ścieżki do uruchomienia aplikacji Node, takiej jak `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Nawiązywanie połączenia, tworzenie tabeli i wstawianie danych

Użyj następującego kodu, aby połączyć i załadować dane przy użyciu **create tabeli** i **wstaw do** instrukcji SQL.

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Funkcja [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Funkcja [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. 

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. Tablica wyników jest używana do przechowywania wyników zapytania.

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Aktualizowanie danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **UPDATE** języka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. 

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Usuwanie danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **DELETE** języka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. 

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./concepts-migrate-import-export.md)
