---
title: 'Szybki start: Tworzenie serwera usługi Azure Database for MariaDB za pomocą witryny Azure Portal'
description: W tym artykule podano instrukcje pozwalające utworzyć przykładowy serwer usługi Azure Database for MariaDB za pomocą witryny Azure Portal w ciągu około pięciu minut.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: d4de6bdb6ab880a3f7da3cf27e34baa1c0284ec7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409409"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-portal"></a>Tworzenie serwera usługi Azure Database for MariaDB za pomocą witryny Azure Portal

Azure Database for MariaDB to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MariaDB o wysokiej dostępności oraz zarządzanie nimi. W tym przewodniku Szybki start przedstawiono, jak utworzyć serwer usługi Azure Database for MariaDB za pomocą witryny Azure Portal w ciągu okołu pięciu minut.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Otwórz przeglądarkę internetową, a następnie przejdź do witryny [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB
Serwer usługi Azure Database for MariaDB jest tworzony za pomocą zdefiniowanego zestawu zasobów obliczeniowych i przestrzeni dyskowej <!-- [compute and storage resources](./concepts-compute-unit-and-storage.md)-->. Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/resource-group-overview.md).

Wykonaj następujące kroki, aby utworzyć serwer usługi Azure Database for MariaDB:

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wpisz frazę **Azure Database for MariaDB** w polu wyszukiwania, aby znaleźć tę usługę.

   ![Opcja usługi Azure Database for MariaDB](./media/quickstart-create-mariadb-server-database-using-azure-portal/2_navigate-to-mariadb.png)

3. Wypełnij formularz szczegółów nowego serwera, używając następujących informacji:
   
   ![Tworzenie formularza serwera](./media/quickstart-create-mariadb-server-database-using-azure-portal/4-create-form.png)

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
    Warstwa cenowa | **Ogólnego przeznaczenia**, **generacja 5**, **2 rdzenie wirtualne**, **5 GB**, **7 dni**, **Geograficznie nadmiarowa** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz **warstwę cenową**. Następnie wybierz kartę **Ogólnego przeznaczenia**. *Generacja 5*, *2 rdzenie wirtualne*, *5 GB* oraz *7 dni* to wartości domyślne opcji **Generowanie obliczeń**, **Rdzeń wirtualny**, **Magazyn** oraz **Okres przechowywania kopii zapasowej**. Te suwaki możesz zostawić bez zmian. Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** w pozycji **Opcje nadmiarowości kopii zapasowej**. Aby zapisać tę wybraną warstwę cenową, wybierz przycisk **OK**. Następny zrzut ekranu przedstawia te wybory.
  
    > [!IMPORTANT]
    > Nazwa logowania i hasło administratora serwera określone w tym miejscu będą wymagane do logowania do serwera i jego baz danych w późniejszej części tego przewodnika Szybki start. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.
    > 

   ![Tworzenie serwera — okno warstwy cenowej](./media/quickstart-create-mariadb-server-database-using-azure-portal/3-pricing-tier.png)

4.  Wybierz pozycję **Utwórz**, aby aprowizować serwer. Aprowizowanie może zająć do 20 minut.
   
5.  Wybierz pozycję **Powiadomienia** (ikonę dzwonka) na pasku narzędzi, aby monitorować proces wdrażania.
   
  Domyślnie na serwerze są tworzone następujące bazy danych: **information_schema**, **mysql**, **performance_schema** i **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Usługa Azure Database for MariaDB tworzy zaporę na poziomie serwera. Uniemożliwia ona zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze, chyba że zostanie utworzona reguła zapory otwierająca zaporę dla konkretnych adresów IP. 

1.   Po zakończeniu wdrażania odszukaj serwer. Jeśli to konieczne, możesz go wyszukać. Na przykład wybierz pozycję **Wszystkie zasoby** z menu po lewej stronie. Wpisz nazwę serwera, takiego jak przykładowy serwer **mydemoserver**, aby wyszukać nowo utworzony serwer. Wybierz nazwę serwera z listy wyników wyszukiwania. Zostanie otwarta strona **Przegląd**, która zawiera dalsze opcje konfiguracji.

2. Na stronie serwera wybierz pozycję **Zabezpieczenia połączeń**.

3.  W obszarze z nagłówkiem **Reguły zapory** wybierz puste pole tekstowe w kolumnie **Nazwa reguły**, aby rozpocząć tworzenie reguły zapory. Podaj dokładny zakres adresów IP klientów, którzy będą łączyć się z tym serwerem.
   
   ![Zabezpieczenia połączeń — reguły zapory](./media/quickstart-create-mariadb-server-database-using-azure-portal/5-firewall-2.png)

4. Na górnym pasku narzędzi strony **Zabezpieczenia połączeń** wybierz pozycję **Zapisz**. Przed kontynuowaniem zaczekaj, aż pojawi się powiadomienie, że aktualizacja została zakończona pomyślnie. 

   > [!NOTE]
   > Połączenia z usługą Azure Database for MariaDB korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.
   > 

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu
Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Być może te wartości zostały zanotowane wcześniej podczas pracy z artykułem Szybki start. W przeciwnym razie możesz łatwo odnaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** lub **Właściwości** serwera w witrynie Azure Portal.

Aby znaleźć te wartości, wykonaj następujące czynności: 

1. Otwórz stronę **Przegląd** serwera. Zanotuj wartości **Nazwa serwera** i **Identyfikator logowania administratora serwera**. 

2. Umieszczaj kursor nad poszczególnymi polami, a po prawej stronie tekstu pojawi się ikona kopiowania. Wybierz ikonę kopiowania, aby w razie potrzeby skopiować wartości.

W tym przykładzie nazwa serwera to **mydemoserver.mariadb.database.azure.com**, a identyfikator logowania administratora serwera to **myadmin@mydemoserver**.

## <a name="connect-to-mariadb-using-mysql-command-line"></a>Nawiązywanie połączenia z serwerem MariaDB za pomocą wiersza polecenia mysql
Istnieje wiele aplikacji, za pomocą których można nawiązać połączenie z serwerem usługi Azure Database for MariaDB.

Użyjmy najpierw narzędzia wiersza polecenia [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html), aby zilustrować procedurę nawiązywania połączenia z serwerem. Możesz użyć przeglądarki internetowej i usługi Azure Cloud Shell w opisany sposób bez instalowania żadnego dodatkowego oprogramowania. Jeśli masz zainstalowane lokalnie narzędzie mysql, możesz nawiązać połączenie także lokalnie.

1. Uruchom usługę Azure Cloud Shell za pomocą ikony terminalu (**>_**) w prawej górnej części witryny Azure Portal.
![Symbol terminalu usługi Azure Cloud Shell](./media/quickstart-create-mariadb-server-database-using-azure-portal/7-cloud-console.png)

2.  Usługa Azure Cloud Shell zostanie otwarta w przeglądarce, umożliwiając wpisywanie poleceń powłoki Bash.

   ![Wiersz polecenia — przykładowy wiersz polecenia mysql](./media/quickstart-create-mariadb-server-database-using-azure-portal/8-bash.png)

3. W wierszu polecenia usługi Cloud Shell nawiąż połączenie z serwerem usługi Azure Database for MariaDB, wpisując wiersz polecenia mysql.

    Następujący format służy do łączenia z serwerem usługi Azure Database for MariaDB przy użyciu narzędzia mysql:

    ```bash
    mysql --host <fully qualified server name> --user <server admin login name>@<server name> -p
    ```

    Na przykład następujące polecenie umożliwia połączenie z przykładowym serwerem:

    ```azurecli-interactive
    mysql --host mydemoserver.mariadb.database.azure.com --user myadmin@mydemoserver -p
    ```

    Parametr narzędzia mysql |Sugerowana wartość|Opis
    ---|---|---
    --host | *Nazwa serwera* | Wartość nazwy serwera, która została użyta wcześniej podczas tworzenia serwera usługi Azure Database for MariaDB. Przykładowy serwer to **mydemoserver.mariadb.database.azure.com**. Użyj w pełni kwalifikowanej nazwy domeny (**\*.mariadb.database.azure.com**), tak jak pokazano w przykładzie. Jeśli nie pamiętasz nazwy serwera, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia.
    --user | *Nazwa logowania administratora serwera* |Nazwa logowania administratora serwera, którą podano wcześniej podczas tworzenia serwera usługi Azure Database for MariaDB. Jeśli nie pamiętasz nazwy użytkownika, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia. Format to *username@servername*.
    -p | *Zaczekaj, aż zostanie wyświetlony monit* |Wtedy wpisz hasło podane podczas tworzenia serwera. Weź pod uwagę, że wpisywane znaki nie są wyświetlane w wierszu polecenia powłoki Bash. Po wprowadzeniu hasła wybierz klawisz **Enter**.

   Po nawiązaniu połączenia narzędzie mysql wyświetli monit `mysql>` umożliwiający wpisywanie poleceń. 

   Poniżej przedstawiono przykładowe dane wyjściowe polecenia mysql:

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.39.0 MariaDB Server
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Jeśli konfiguracja zapory nie umożliwia dostępu do adresu IP usługi Azure Cloud Shell, wystąpi następujący błąd:
    >
    > BŁĄD 2003 (28000): Klient o adresie IP 123.456.789.0 nie może uzyskać dostępu do serwera.
    >
    > Aby naprawić błąd, upewnij się, że konfiguracja serwera jest zgodna z krokami opisanymi w sekcji „Konfigurowanie reguły zapory na poziomie serwera” tego artykułu.

4. Aby upewnić się, że połączenie działa, wyświetl stan serwera, wpisując polecenie `status` w wierszu polecenia mysql>.

    ```sql
    status
    ```

   > [!TIP]
   > Aby zapoznać się z dodatkowymi poleceniami, zobacz [MySQL 5.7 Reference Manual - Chapter 4.5.1 (Podręcznik programu MySQL 5.7 — rozdział 4.5.1)](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Utwórz pustą bazę danych, wpisując następujące polecenie w wierszu polecenia **mysql>**:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    Wykonanie polecenia może potrwać kilka chwil. 

    Na serwerze usługi Azure Database for MariaDB można utworzyć jedną lub wiele baz danych. Można wybrać opcję utworzenia jednej bazy danych na serwer w celu wykorzystania wszystkich zasobów. Można też utworzyć wiele baz danych, współdzielących zasoby. Nie istnieje limit dotyczący maksymalnej liczby baz danych, które można utworzyć, ale w przypadku wielu baz danych są współdzielone te same zasoby serwera. 

6. Wyświetl bazy danych, wpisując następujące polecenie w wierszu polecenia **mysql>**:

    ```sql
    SHOW DATABASES;
    ```

7.  Wpisz polecenie `\q`, a następnie wybierz klawisz **Enter**, aby zamknąć narzędzie mysql. Po wykonaniu tych czynności możesz zamknąć usługę Azure Cloud Shell.

Nawiązano połączenie z serwerem usługi Azure Database for MariaDB i utworzono pustą bazę danych użytkownika. Przejdź do następnej sekcji, w której przedstawiono podobne ćwiczenie. Następne ćwiczenie polega na nawiązaniu połączenia z tym samym serwerem przy użyciu innego popularnego narzędzia — MySQL Workbench.

## <a name="connect-to-server-using-mysql-workbench"></a>Nawiązywanie połączenia z serwerem za pomocą aplikacji MySQL Workbench
Aby nawiązać połączenie z serwerem za pomocą aplikacji MySQL Workbench, wykonaj następujące czynności:

1. Otwórz aplikację MySQL Workbench na komputerze klienckim. Aplikację MySQL Workbench możesz pobrać i zainstalować [ze strony pobierania aplikacji MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

2. Utwórz nowe połączenie. Kliknij ikonę znaku plus (+) obok nagłówka **Połączenia MySQL**.

3. W oknie dialogowym **Konfigurowanie nowego połączenia** wprowadź informacje dotyczące połączenia serwera na karcie **Parametry**. Wartości zastępcze są wyświetlane jako przykład. Zamień nazwę hosta, nazwę użytkownika i hasło na własne wartości.

   ![Konfigurowanie nowego połączenia](./media/quickstart-create-mariadb-server-database-using-azure-portal/setup-new-connection.png)

    |Ustawienie |Sugerowana wartość|Opis pola|
    |---|---|---|
     Nazwa połączenia | Połączenie demonstracyjne | Etykieta dla tego połączenia. |
    Metoda połączenia | Standardowa (TCP/IP) | Metoda Standardowa (TCP/IP) jest wystarczająca. |
    Nazwa hosta | *Nazwa serwera* | Wartość nazwy serwera, która została użyta wcześniej podczas tworzenia serwera usługi Azure Database for MariaDB. Przykładowy serwer to **mydemoserver.mariadb.database.azure.com**. Użyj w pełni kwalifikowanej nazwy domeny (**\*.mariadb.database.azure.com**), tak jak pokazano w przykładzie. Jeśli nie pamiętasz nazwy serwera, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia.|
     Port | 3306 | Port używany podczas łączenia z serwerem usługi Azure Database for MariaDB. |
    Nazwa użytkownika |  *Nazwa logowania administratora serwera* | Informacje dotyczące logowania administratora serwera, które podano wcześniej podczas tworzenia serwera usługi Azure Database for MariaDB. Przykładowa nazwa użytkownika to **myadmin@mydemoserver**. Jeśli nie pamiętasz nazwy użytkownika, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia. Format to *username@servername*.
    Hasło | *Twoje hasło* | Aby zapisać hasło, wybierz przycisk **Zapisz w magazynie**. |

4. Wybierz pozycję **Testuj połączenie**, aby sprawdzić, czy wszystkie parametry zostały skonfigurowane poprawnie. Następnie wybierz przycisk **OK**, aby zapisać połączenie. 

    > [!NOTE]
    > Na serwerze domyślnie jest wymuszane używanie protokołu SSL. W związku z tym do pomyślnego nawiązania połączenia jest wymagana dodatkowa konfiguracja. <!--For more information, see [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md).--> Aby wyłączyć protokół SSL na czas wykonywania instrukcji w tym przewodniku Szybki start, przejdź do witryny Azure Portal. Wybierz stronę Zabezpieczenia połączeń i kliknij przycisk przełączania **Wymuszaj połączenie SSL** w celu wyłączenia go.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Zasoby, które zostały utworzone w ramach tego przewodnika Szybki start, możesz wyczyścić na dwa sposoby. Możesz usunąć [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) zawierającą wszystkie zasoby w tej grupie. Jeśli chcesz zachować inne zasoby bez zmian, usuń tylko ten jeden zasób serwera.

> [!TIP]
> Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli chcesz kontynuować pracę z przewodnikami Szybki start, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuowania pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.
>

Aby usunąć całą grupę zasobów łącznie z nowo utworzonym serwerem, wykonaj następujące czynności:

1.  Znajdź grupę zasobów w witrynie Azure Portal. W menu po lewej stronie wybierz pozycję **Grupy zasobów**, a następnie wybierz nazwę grupy zasobów, takiej jak przykładowa grupa **myresourcegroup**.

2.  Na stronie grupy zasobów wybierz pozycję **Usuń**. Następnie wpisz nazwę grupy zasobów, takiej jak przykładowa grupa **myresourcegroup**, w polu tekstowym, aby potwierdzić usunięcie, i wybierz pozycję **Usuń**.

Aby usunąć tylko nowo utworzony serwer, wykonaj następujące czynności:

1.  Znajdź serwer w witrynie Azure Portal, jeśli nie jest jeszcze otwarty. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Wszystkie zasoby**. Następnie wyszukaj utworzony serwer.

2.  Na stronie **Przegląd** wybierz pozycję **Usuń**. 

   ![Azure Database for MariaDB — usuwanie serwera](./media/quickstart-create-mariadb-server-database-using-azure-portal/delete-server.png)

3.  Potwierdź nazwę serwera do usunięcia i wyświetl jego bazy danych, których dotyczy ta operacja. W polu tekstowym wpisz nazwę serwera, np. nazwę przykładowego serwera **mydemoserver**. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
- [Projektowanie pierwszej bazy danych usługi Azure Database for MariaDB](./tutorial-design-database-using-portal.md)
