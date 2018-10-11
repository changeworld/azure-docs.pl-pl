---
title: 'Samouczek: projektowanie usługi Azure Database for MariaDB za pomocą witryny Azure Portal'
description: W tym samouczku wyjaśniono, jak utworzyć serwer oraz bazę danych usługi Azure Database for MariaDB i zarządzać nimi przy użyciu witryny Azure Portal.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: tutorial
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 2e1cd0d28c544b2e4c5dc86c7a6db39a5d20c1ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991015"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-using-the-azure-portal"></a>Samouczek: projektowanie bazy danych usługi Azure Database for MariaDB za pomocą witryny Azure Portal
Azure Database for MariaDB to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. Za pomocą witryny Azure Portal możesz łatwo zarządzać serwerem i zaprojektować bazę danych.

W tym samouczku nauczysz się wykonywać następujące czynności, używając witryny Azure Portal:

> [!div class="checklist"]
> * Tworzenie usługi Azure Database for MariaDB
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą narzędzia wiersza polecenia mysql
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Otwórz ulubioną przeglądarkę internetową i przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB
Serwer usługi Azure Database for MariaDB jest tworzony za pomocą zdefiniowanego zestawu zasobów obliczeniowych i przestrzeni dyskowej <!--[compute and storage resources](./concepts-compute-unit-and-storage.md)-->. Serwer jest tworzony w ramach [grupy zasobów Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wpisz frazę **Azure Database for MariaDB** w polu wyszukiwania, aby znaleźć tę usługę.
   
   ![Przechodzenie do bazy danych MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Kliknij kafelek **Azure Database for MariaDB**, a następnie kliknij pozycję **Utwórz**. Wypełnij formularz usługi Azure Database for MariaDB.
   
   ![Tworzenie formularza](./media/tutorial-design-database-using-portal/2-create-form.png)

    **Ustawienie** | **Sugerowana wartość** | **Opis pola** 
    ---|---|---
    Nazwa serwera | Unikatowa nazwa serwera | Wybierz unikatową nazwę, która identyfikuje serwer usługi Azure Database for MariaDB. Na przykład mydemoserver. Nazwa domeny *.mariadb.database.azure.com* jest dołączana do podawanej nazwy serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
    Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | *myresourcegroup* | Podaj nazwę nowej lub istniejącej grupy zasobów.    Grupa zasobów|*myresourcegroup*| Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.
    Wybierz źródło | *Puste* | Wybierz opcję *Puste*, aby utworzyć nowy serwer od początku. (Opcję *Kopia zapasowa* należy wybrać w przypadku tworzenia serwera z geograficznej kopii zapasowej istniejącego serwera usługi Azure Database for MariaDB).
    Identyfikator logowania administratora serwera | myadmin | Konto logowania do użycia podczas łączenia z serwerem. Nazwą logowania administratora nie może być **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
    Hasło | *Wartość wybrana przez użytkownika* | Podaj nowe hasło dla konta administratora serwera. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (0–9) i znaki inne niż alfanumeryczne (!, $, #, % itp.).
    Potwierdź hasło | *Wartość wybrana przez użytkownika*| Potwierdź hasło do konta administratora.
    Lokalizacja | *Region najbliżej Twoich użytkowników*| Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure.
    Wersja | *Najnowsza wersja*| Najnowsza wersja, chyba że z konkretnych powodów wymagana jest inna wersja.
    Warstwa cenowa | **Ogólnego przeznaczenia**, **Generacja 5**, **2 rdzenie wirtualne**, **5 GB**, **7 dni**, **Geograficznie nadmiarowy** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz **warstwę cenową**. Następnie wybierz kartę **Ogólnego przeznaczenia**. *Generacja 5*, *2 rdzenie wirtualne*, *5 GB* oraz *7 dni* to wartości domyślne opcji **Generacja obliczeń**, **Rdzeń wirtualny**, **Magazyn** oraz **Okres przechowywania kopii zapasowej**. Te suwaki możesz zostawić bez zmian. Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** w pozycji **Opcje nadmiarowości kopii zapasowej**. Aby zapisać tę wybraną warstwę cenową, wybierz przycisk **OK**. Następny zrzut ekranu przedstawia te wybory.
    
   ![Warstwa cenowa](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Kliknij pozycję **Utwórz**. Po około dwóch minutach nowy serwer usługi Azure Database for MariaDB będzie działać w chmurze. Aby monitorować proces wdrażania, możesz kliknąć przycisk **Powiadomienia** na pasku narzędzi.

## <a name="configure-firewall"></a>Konfigurowanie zapory
Bazy danych usługi Azure Database for MariaDB są chronione przez zaporę. Domyślnie wszystkie połączenia z serwerem i znajdującymi się na nim bazami danych są odrzucane. Przed nawiązaniem pierwszego połączenia z usługą Azure Database for MariaDB skonfiguruj zaporę, aby dodać publiczny adres IP (lub zakres adresów IP) sieci maszyny klienta.

1. Kliknij nowo utworzony serwer, a następnie kliknij pozycję **Zabezpieczenia połączeń**.
   
   ![Zabezpieczenia połączeń](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. W tym miejscu możesz skorzystać z funkcji **Dodaj mój adres IP** lub skonfigurować reguły zapory. Pamiętaj, aby po utworzeniu reguł kliknąć przycisk **Zapisz**.
Teraz możesz nawiązać połączenie z serwerem za pomocą narzędzia wiersza polecenia mysql lub narzędzia z graficznym interfejsem użytkownika MySQL Workbench.

> [!TIP]
> Serwer usługi Azure Database for MariaDB komunikuje się przez port 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany przez zaporę sieciową. Jeśli wystąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure Database for MariaDB, chyba że dział IT otworzy port 3306.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Uzyskaj z witryny Azure Portal w pełni kwalifikowaną **Nazwę serwera** i **Nazwę logowania administratora serwera** dla serwera usługi Azure Database for MariaDB. W pełni kwalifikowana nazwa serwera służy do nawiązywania połączenia z serwerem przy użyciu narzędzia wiersza polecenia mysql. 

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij pozycję **Wszystkie zasoby** w menu po lewej stronie, wpisz nazwę, a następnie wyszukaj serwer usługi Azure Database for MariaDB. Wybierz nazwę serwera, aby wyświetlić szczegóły.

2. Ze strony **Przegląd** zanotuj **Nazwę serwera** i **Nazwę logowania administratora serwera**. Aby skopiować zawartość do schowka, możesz nacisnąć przycisk kopiowania obok każdego pola.
   ![4-2 Właściwości serwera](./media/tutorial-design-database-using-portal/2-server-properties.png)

W tym przykładzie nazwa serwera to *mydemoserver.mariadb.database.azure.com*, a identyfikator logowania administratora serwera to *myadmin@mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia mysql
Nawiąż połączenie z serwerem usługi Azure Database for MariaDB za pomocą [narzędzia wiersza polecenia mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html). Narzędzie wiersza polecenia mysql możesz uruchomić z poziomu usługi Azure Cloud Shell w przeglądarce lub z poziomu własnej maszyny, korzystając z zainstalowanych lokalnie narzędzi mysql. Aby uruchomić usługę Azure Cloud Shell, kliknij przycisk `Try It` w bloku kodu w tym artykule lub przejdź do witryny Azure Portal i kliknij ikonę `>_` na pasku narzędzi w prawym górnym rogu. 

Aby nawiązać połączenie, wpisz poniższe polecenie:
```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Tworzenie pustej bazy danych
Po nawiązaniu połączenia z serwerem utwórz pustą bazę danych, z którą będziesz pracować.
```sql
CREATE DATABASE mysampledb;
```

W wierszu polecenia uruchom poniższe polecenie, aby przełączyć połączenie na tę nowo utworzoną bazę danych:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych
Teraz, gdy wiesz, jak nawiązać połączenie z bazą danych usługi Azure Database for MariaDB, możesz wykonać niektóre podstawowe zadania:

Najpierw utwórz tabelę i załaduj do niej dane. Utwórz tabelę z informacjami o spisie.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Ładowanie danych do tabel
Teraz, gdy masz tabelę, wstaw do niej dane. W otwartym oknie wiersza polecenia uruchom następujące zapytanie, aby wstawić wiersze danych.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

W utworzonej wcześniej tabeli masz teraz dwa wiersze przykładowych danych.

## <a name="query-and-update-the-data-in-the-tables"></a>Wykonywanie zapytania względem danych w tabelach i aktualizowanie tych danych
Wykonaj następujące zapytanie, aby pobrać informacje z tabeli bazy danych.
```sql
SELECT * FROM inventory;
```

Możesz także zaktualizować dane w tabelach.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Wiersz jest aktualizowany podczas pobierania danych.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Przywracanie bazy danych do określonego punktu w czasie
Załóżmy, że przypadkowo usunięto ważną tabelę bazy danych i danych nie można w prosty sposób odzyskać. Usługa Azure Database for MariaDB umożliwia przywrócenie serwera do punktu w czasie i utworzenie kopii baz danych na nowym serwerze. Przy użyciu tego nowego serwera można odzyskać usunięte dane. Poniższa procedura opisuje przywrócenie przykładowego serwera do punktu przed dodaniem tabeli.

1. W witrynie Azure Portal znajdź usługę Azure Database for MariaDB. Na stronie **Przegląd** kliknij pozycję **Przywróć** na pasku narzędzi. Zostanie otwarta strona Przywracanie.

   ![10-1 Przywracanie bazy danych](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Wypełnij formularz **Przywracanie** wymaganymi informacjami.
   
   ![10-2 Formularz Przywracanie](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Punkt przywracania**: wybierz punkt w czasie, do którego chcesz wykonać przywrócenie, w podanym przedziale czasowym. Pamiętaj o przekonwertowaniu lokalnej strefy czasowej na czas UTC.
   - **Przywróć na nowy serwer**: podaj nazwę nowego serwera, do którego chcesz wykonać przywrócenie.
   - **Lokalizacja**: region jest taki sam, jak w przypadku serwera źródłowego, i nie można go zmienić.
   - **Warstwa cenowa**: warstwa cenowa jest taka sama, jak w przypadku serwera źródłowego, i nie można jej zmienić.
   
3. Kliknij przycisk **OK**, aby przywrócić serwer do punktu w czasie <!--[restore to a point in time](./howto-restore-server-portal.md)--> przed usunięciem tabeli. Przywrócenie serwera powoduje utworzenie nowej kopii serwera od określonego punktu w czasie. 

<!--## Next steps
In this tutorial, you use the Azure portal to learned how to:

> [!div class="checklist"]
> * Create an Azure Database for MariaDB
> * Configure the server firewall
> * Use mysql command-line tool to create a database
> * Load sample data
> * Query data
> * Update data
> * Restore data

><> [!div class="nextstepaction"]
> [How to connect applications to Azure Database for MariaDB](./howto-connection-string.md)-->
