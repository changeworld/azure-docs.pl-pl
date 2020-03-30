---
title: Połącz się z Python - Usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Ten przewodnik Szybki start zawiera przykłady kodu języka Python, których można używać do łączenia się i wykonywania zapytań o dane z usługi Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 3694c0b74393068538a0c8f496444a1541d88fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76769058"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Szybki start: łączenie danych usługi Azure Database dla postgreSQL — pojedynczy serwer za pomocą języka Python

W tym przewodniku Szybki start pracujesz z usługą Azure Database for PostgreSQL przy użyciu języka Python w systemach macOS, Ubuntu Linux lub Windows. Przewodnik Szybki start pokazuje, jak połączyć się z bazą danych i używać instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych. W tym artykule założono, że jesteś zaznajomiony z Python, ale nowe do pracy z usługą Azure Database dla PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Ukończenie [przewodnika Szybki start: tworzenie bazy danych platformy Azure dla serwera PostgreSQL w witrynie Azure portal](quickstart-create-server-database-portal.md) lub szybki [start: tworzenie bazy danych platformy Azure dla postgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9+ lub 3.4+.
  
- Najnowszy instalator [pakietów pip.](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-python-libraries-for-postgresql"></a>Instalowanie bibliotek języka Python dla postgreSql
Moduł [psycopg2](https://pypi.python.org/pypi/psycopg2/) umożliwia łączenie i wykonywanie zapytań z bazą danych PostgreSQL i jest dostępny jako pakiet [kół](https://pythonwheels.com/) linux, macOS lub Windows. Zainstaluj wersję binarną modułu, w tym wszystkie zależności. Aby uzyskać `psycopg2` więcej informacji na temat instalacji i wymagań, zobacz [Instalacja](http://initd.org/psycopg/docs/install.html). 

Aby `psycopg2`zainstalować , otwórz terminal lub wiersz `pip install psycopg2`polecenia i uruchom polecenie .

## <a name="get-database-connection-information"></a>Uzyskaj informacje o połączeniu z bazą danych
Łączenie się z bazą danych usługi Azure Database for PostgreSQL wymaga w pełni kwalifikowanej nazwy serwera i poświadczeń logowania. Te informacje można uzyskać z witryny Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)wyszukaj i wybierz swoją usługę Azure Database dla nazwy serwera PostgreSQL. 
1. Na stronie **Przegląd** serwera skopiuj w pełni kwalifikowaną **nazwę serwera** i nazwę **użytkownika administratora**. W pełni kwalifikowana **nazwa serwera** jest zawsze w postaci * \<nazwy mojego serwera>.postgres.database.azure.com*, a **nazwa użytkownika administratora** jest zawsze w postaci * \<my-admin-username>@\<my-server-name>*. 
   
   Potrzebujesz również hasła administratora. Jeśli go zapomnisz, możesz go zresetować z tej strony. 
   
   ![Nazwa serwera usługi Azure Database for PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Jak uruchomić przykłady Pythona

Dla każdego przykładu kodu w tym artykule:

1. Tworzenie nowego pliku w edytorze tekstu. 
   
1. Dodaj przykład kodu do pliku. W kodzie zastąp:
   - `<server-name>`i `<admin-username>` z wartościami skopiowanymi z witryny Azure portal.
   - `<admin-password>`z hasłem serwera.
   - `<database-name>`z nazwą bazy danych usługi Azure Database for PostgreSQL. Podczas tworzenia serwera automatycznie tworzona została domyślna baza danych o nazwie *postgres.* Można zmienić nazwę tej bazy danych lub utworzyć nową bazę danych przy użyciu poleceń SQL. 
   
1. Zapisz plik w folderze projektu z rozszerzeniem *.py,* takim jak *postgres-insert.py*. W systemie Windows upewnij się, że kodowanie UTF-8 jest zaznaczone podczas zapisywania pliku. 
   
1. Aby uruchomić plik, zmień folder projektu w interfejsie wiersza polecenia i wpisz następuje `python` nazwa pliku, na przykład `python postgres-insert.py`.

## <a name="create-a-table-and-insert-data"></a>Tworzenie tabeli i wstawianie danych
Poniższy przykład kodu łączy się z bazy danych usługi Azure Database dla PostgreSQL przy użyciu [funkcji psycopg2.connect](http://initd.org/psycopg/docs/connection.html) i ładuje dane z instrukcją SQL **INSERT.** Funkcja [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) wykonuje kwerendę SQL względem bazy danych. 

```Python
import psycopg2

# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Po pomyślnym uruchomieniu kodu, generuje następujące dane wyjściowe:

![Dane wyjściowe wiersza polecenia](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Odczyt danych
Poniższy przykład kodu łączy się z bazą danych usługi Azure Database for PostgreSQL i używa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją SQL **SELECT** do odczytu danych. Ta funkcja akceptuje kwerendę i zwraca zestaw wyników do iteracji za pomocą [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Aktualizowanie danych
Poniższy przykład kodu łączy się z bazą danych usługi Azure Database for PostgreSQL i używa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją SQL **UPDATE** do aktualizowania danych. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Usuwanie danych
Poniższy przykład kodu łączy się z bazą danych usługi Azure Database for PostgreSQL i używa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją SQL **DELETE,** aby usunąć element spisu, który został wcześniej wstawiony. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
