---
title: Połącz się przy użyciu języka Python — usługa Azure Database for MySQL
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu w języku Python, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 01/09/2020
ms.openlocfilehash: c9ea155f3cc71dd961a3780e3b188a6d062606bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067900"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Szybki start: łączenie danych usługi Azure Database dla aplikacji MySQL za pomocą języka Python i wykonywanie zapytań o dane

W tym przewodniku Szybki start można połączyć się z usługą Azure Database for MySQL przy użyciu języka Python. Następnie używasz instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

W tym temacie przyjęto założenie, że jesteś zaznajomiony z tworzenia przy użyciu języka Python, ale jesteś nowy do pracy z usługą Azure Database dla MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Usługa Azure Database dla serwera MySQL. [Utwórz usługę Azure Database dla serwera MySQL przy użyciu witryny Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) lub [utwórz usługę Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.](quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalowanie języka Python i łącznika programu MySQL

Zainstaluj pythona i łącznika MySQL dla Języka Python na komputerze, wykonując następujące kroki: 

> [!NOTE]
> Ten szybki start używa nieprzetworzonego podejścia do zapytań SQL, aby połączyć się z MySQL. Jeśli używasz struktury sieci web, użyj zalecanego łącznika dla struktury, na przykład [mysqlclient](https://pypi.org/project/mysqlclient/) dla Django.

1. Pobierz i zainstaluj [Pythona 3.7 lub więcej](https://www.python.org/downloads/) dla swojego systemu operacyjnego. Pamiętaj, aby dodać `PATH`Pythona do swojego , ponieważ wymaga tego łącznik MySQL.
   
1. Otwórz wiersz polecenia `bash` lub powłokę i sprawdź `python -V` wersję języka Python, uruchamiając go za pomocą przełącznika V wielkimi literami.
   
1. Instalator `pip` pakietu znajduje się w najnowszych wersjach pythona. Zaktualizuj `pip` do `pip install -U pip`najnowszej wersji, uruchamiając program . 
   
   Jeśli `pip` nie jest zainstalowany, można go `get-pip.py`pobrać i zainstalować za pomocą programu . Aby uzyskać więcej informacji, zobacz [Instalacja](https://pip.pypa.io/en/stable/installing/). 
   
1. Służy `pip` do instalowania łącznika MySQL dla języka Python i jego zależności:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Łącznik Pythona dla MySQL można również zainstalować z [mysql.com](https://dev.mysql.com/downloads/connector/python/). Aby uzyskać więcej informacji na temat łącznika MySQL dla języka Python, zobacz [Przewodnik po łączniku MySQL/pythonie](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu potrzebne do połączenia z usługą Azure Database for MySQL z witryny Azure portal. Potrzebna jest nazwa serwera, nazwa bazy danych i dane logowania.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
   
1. Na pasku wyszukiwania portalu wyszukaj i wybierz utworzony serwer Usługi Azure Database dla mySQL, na przykład **mydemoserver**.
   
   ![Nazwa serwera usługi Azure Database for MySQL](./media/connect-python/1_server-overview-name-login.png)
   
1. Na stronie **Przegląd** serwera zanotuj **nazwę serwera** i nazwę **logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również zresetować hasło z tej strony.
   
   ![Nazwa serwera usługi Azure Database for MySQL](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>Uruchamianie przykładów języka Python

Dla każdego przykładu kodu w tym artykule:

1. Tworzenie nowego pliku w edytorze tekstu.
1. Dodaj przykład kodu do pliku. `<mydemoserver>`W kodzie zastąp `<myadmin>` `<mypassword>` `<mydatabase>` symbole zastępcze , i symbole zastępcze wartościami serwera i bazy danych MySQL.
1. Zapisz plik w folderze projektu z rozszerzeniem *.py,* takim jak *C:\pythonmysql\createtable.py* lub */home/username/pythonmysql/createtable.py*.
1. Aby uruchomić kod, otwórz wiersz `bash` polecenia lub powłokę i zmień `cd pythonmysql`katalog w folderze projektu, na przykład . Wpisz `python` polecenie, na którym następuje `python createtable.py`na przykład nazwa pliku, a następnie naciśnij klawisz Enter. 
   
   > [!NOTE]
   > W systemie Windows, jeśli *python.exe* nie zostanie znaleziony, może być konieczne dodanie ścieżki Języka Python do zmiennej `C:\python27\python.exe createtable.py`środowiskowej PATH lub zapewnienie pełnej ścieżki do *pliku python.exe*, na przykład .

## <a name="create-a-table-and-insert-data"></a>Tworzenie tabeli i wstawianie danych

Użyj następującego kodu, aby połączyć się z serwerem i bazą danych, utworzyć tabelę i załadować dane przy użyciu instrukcji **SQL INSERT.** 

Kod importuje bibliotekę mysql.connector i używa funkcji [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) do łączenia się z usługą Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji konfiguracji. Kod używa kursora w połączeniu, a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje kwerendę SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. 

Kod importuje bibliotekę mysql.connector i używa funkcji [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) do łączenia się z usługą Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji konfiguracji. Kod używa kursora w połączeniu, a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje kwerendę SQL względem bazy danych MySQL. 

Kod odczytuje wiersze danych przy użyciu metody [fetchall(),](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) zachowuje zestaw wyników w `for` wierszu kolekcji i używa iteratora do pętli nad wierszami.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Aktualizowanie danych

Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL. 

Kod importuje bibliotekę mysql.connector i używa funkcji [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) do łączenia się z usługą Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji konfiguracji. Kod używa kursora w połączeniu, a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje kwerendę SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Usuwanie danych

Użyj poniższego kodu, aby nawiązać połączenie i usunąć dane za pomocą instrukcji **DELETE** języka SQL. 

Kod importuje bibliotekę mysql.connector i używa funkcji [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) do łączenia się z usługą Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji konfiguracji. Kod używa kursora w połączeniu, a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje kwerendę SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./concepts-migrate-import-export.md)
