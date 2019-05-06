---
title: Za pomocą środowiska Node.js, połączyć z usługą Azure Database for PostgreSQL — pojedynczy serwer
description: Ten przewodnik Szybki Start zawiera przykładowy kod Node.js, którego można użyć do nawiązywania połączeń i wykonywanie zapytań o dane z usługi Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 5ebcb3151bf9b6eaca4a726553a3c8f1d93c08aa
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067133"
---
# <a name="azure-database-for-postgresql---single-server-use-nodejs-to-connect-and-query-data"></a>Azure Database for PostgreSQL — pojedynczy serwer: Nawiązywanie połączeń i wykonywanie zapytań na danych przy użyciu platformy Node.js
Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for PostgreSQL przy użyciu aplikacji języka [Node.js](https://nodejs.org/). Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych. W krokach w tym artykule założono, że wiesz już, jak programować za pomocą języka Node.js, i dopiero zaczynasz pracę z usługą Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z tych przewodników:
- [Tworzenie bazy danych — portal](quickstart-create-server-database-portal.md)
- [Tworzenie bazy danych — interfejs wiersza polecenia](quickstart-create-server-database-azure-cli.md)

Należy również:
- Zainstalować środowisko [Node.js](https://nodejs.org).

## <a name="install-pg-client"></a>Instalowanie klienta pg
Zainstaluj program [pg](https://www.npmjs.com/package/pg), który jest klientem PostgreSQL dla środowiska Node.js.

W tym celu uruchom narzędzie Node Package Manager (npm) dla języka JavaScript z poziomu wiersza polecenia, aby zainstalować klienta pg.
```bash
npm install pg
```

Zweryfikuj instalację, wyświetlając listę zainstalowanych pakietów.
```bash
npm list
```

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z usługą Azure Database for PostgreSQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Wszystkie zasoby** i wyszukaj utworzony serwer, taki jak **mydemoserver**.
3. Kliknij nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 ![Nazwa serwera usługi Azure Database for PostgreSQL](./media/connect-nodejs/1-connection-string.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Uruchamianie kodu JavaScript w środowisku Node.js
Środowisko Node.js można uruchomić w wierszu polecenia powłoki Bash, programu Terminal lub systemu Windows, wpisując polecenie `node`, a następnie uruchomić przykładowy kod JavaScript przez skopiowanie i wklejenie go w wierszu polecenia. Alternatywnie można zapisać kod JavaScript w pliku tekstowym, a następnie uruchomić polecenie `node filename.js` z nazwą pliku jako parametrem uruchamiania.

## <a name="connect-create-table-and-insert-data"></a>Nawiązywanie połączenia, tworzenie tabeli i wstawianie danych
Użyj poniższego kodu, aby nawiązać połączenie i załadować dane przy użyciu instrukcji **CREATE TABLE** i **INSERT INTO** języka SQL.
Obiekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) służy do połączenia interfejsem z serwerem programu PostgreSQL. Funkcja [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) służy do nawiązywania połączenia z serwerem. Funkcja [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) służy do wykonywania zapytania SQL względem bazy danych PostgreSQL. 

Zastąp parametry hosta, nazwy bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia serwera i bazy danych.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Odczyt danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. Obiekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) służy do połączenia interfejsem z serwerem programu PostgreSQL. Funkcja [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) służy do nawiązywania połączenia z serwerem. Funkcja [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) służy do wykonywania zapytania SQL względem bazy danych PostgreSQL. 

Zastąp parametry hosta, nazwy bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia serwera i bazy danych. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Aktualizowanie danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **UPDATE** języka SQL. Obiekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) służy do połączenia interfejsem z serwerem programu PostgreSQL. Funkcja [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) służy do nawiązywania połączenia z serwerem. Funkcja [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) służy do wykonywania zapytania SQL względem bazy danych PostgreSQL. 

Zastąp parametry hosta, nazwy bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia serwera i bazy danych. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Usuwanie danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **DELETE** języka SQL. Obiekt [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) służy do połączenia interfejsem z serwerem programu PostgreSQL. Funkcja [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) służy do nawiązywania połączenia z serwerem. Funkcja [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) służy do wykonywania zapytania SQL względem bazy danych PostgreSQL. 

Zastąp parametry hosta, nazwy bazy danych, użytkownika i hasła wartościami, które zostały określone podczas tworzenia serwera i bazy danych. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
