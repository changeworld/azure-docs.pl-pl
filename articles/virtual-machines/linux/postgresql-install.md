---
title: Konfigurowanie bazy danych PostgreSQL na Maszynę wirtualną systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak instalowanie i konfigurowanie rozwiązania PostgreSQL na maszynie wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 5fe5e819c4f1079b6eb1fa8bb19d337ecfed600d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65955154"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalowanie i konfigurowanie rozwiązania PostgreSQL na platformie Azure
PostgreSQL jest zaawansowane typu open-source bazy danych podobny do bazy danych Oracle i DB2. Obejmuje funkcje gotowa do użycia w przedsiębiorstwie, takie jak pełne ACID zgodność, niezawodne przetwarzanie transakcyjne i kontroli współbieżności wielu wersji. Obsługuje ona standardów, takich jak ANSI SQL i programu SQL/MED (w tym otoki obcego danych Oracle, MySQL, MongoDB i wielu innych). Jest wysoce rozszerzalny obsługę ponad 12 języków proceduralne, GIN i GiST indeksów, obsługę danych przestrzennych i wiele funkcji NoSQL dla formatu JSON czy aplikacji na podstawie wartości klucza.

W tym artykule dowiesz się, jak instalowanie i konfigurowanie rozwiązania PostgreSQL na maszynie wirtualnej platformy Azure z systemem Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Instalowanie bazy danych PostgreSQL
> [!NOTE]
> Musi już mieć maszyn wirtualnych z systemem Linux w celu ukończenia tego samouczka. Aby utworzyć i skonfigurować Maszynę wirtualną systemu Linux przed kontynuowaniem, zobacz [maszyny Wirtualnej systemu Linux platformy Azure w ramach samouczka](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

W takim przypadku należy użyć portu 1999 jako PostgreSQL port.  

Łączenie z systemem Linux maszyny Wirtualnej utworzonej przy użyciu programu PuTTY. Jeśli po raz pierwszy używasz maszyny Wirtualnej systemu Linux na platformie Azure, zobacz [jak używanie protokołu SSH z systemem Linux na platformie Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby dowiedzieć się, jak nawiązać połączenie z maszyny Wirtualnej z systemem Linux przy użyciu programu PuTTY.

1. Uruchom następujące polecenie, aby przełączyć się do katalogu głównego (administratora):
   
        # sudo su -
2. Niektórych dystrybucji mają zależności, które należy zainstalować przed zainstalowaniem programu PostgreSQL. Sprawdź, czy Twoja dystrybucji na liście i uruchom odpowiednie polecenie:
   
   * Red Hat podstawowy system Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian podstawowy system Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Pobierz PostgreSQL do katalogu głównego, a następnie Rozpakuj pakiet:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Powyżej znajduje się przykład. Można znaleźć bardziej szczegółowe adres pobierania w [indeksu/pub/źródła /](https://ftp.postgresql.org/pub/source/).
4. Aby uruchomić kompilację, uruchom następujące polecenia:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Jeśli chcesz zbudować wszystko, co może być kompilowana, w tym dokumentację (strony HTML i ataków typu man) oraz dodatkowych modułów (contrib), uruchom następujące polecenie:
   
        # gmake install-world
   
    Otrzymasz następujący komunikat potwierdzający:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurowanie rozwiązania PostgreSQL
1. (Opcjonalnie) Utwórz link symboliczny skrócenie czasu odwołania PostgreSQL wykluczającą numer wersji:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Utwórz katalog dla bazy danych:
   
        # mkdir -p /opt/pgsql_data
3. Tworzenie użytkownika innego niż główny i modyfikowanie profilu tego użytkownika. Następnie przejdź do nowego użytkownika (o nazwie *postgres* w naszym przykładzie):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Ze względów bezpieczeństwa PostgreSQL używa użytkownik inny niż główny do zainicjowania, uruchomić lub zamknąć bazy danych.
   > 
   > 
4. Edytuj *bash_profile* pliku, wprowadzając poniższe polecenia. Zostaną dodane następujące wiersze na końcu *bash_profile* pliku:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Wykonaj *bash_profile* pliku:
   
        $ source .bash_profile
6. Sprawdzanie poprawności instalacji za pomocą następującego polecenia:
   
        $ which psql
   
    Jeśli instalacja zakończy się pomyślnie, zostanie wyświetlony następującą odpowiedź:
   
        /opt/pgsql/bin/psql
7. Możesz również sprawdzić wersja PostgreSQL:
   
        $ psql -V

8. Inicjowanie bazy danych:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Powinien zostać wyświetlony następujący komunikat:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Konfigurowanie PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Uruchom następujące polecenia:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Należy zmodyfikować dwie zmienne w pliku /etc/init.d/postgresql. Prefiks jest ustawiony do ścieżki instalacji postgresql: **/opt/pgsql**. Ustawiono PGDATA ścieżkę magazynu danych programu PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Zmień plik się pliku wykonywalnego:

    # chmod +x /etc/init.d/postgresql

Start PostgreSQL:

    # /etc/init.d/postgresql start

Sprawdź, czy punkt końcowy postgresql na:

    # netstat -tunlp|grep 1999

Powinny zostać wyświetlone następujące dane wyjściowe:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Połączenia z bazą danych Postgres
Ponownie przełączyć użytkownika postgres:

    # su - postgres

Tworzenie bazy danych Postgres:

    $ createdb events

Połączenia z bazą danych zdarzenia, który został utworzony:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Tworzenie i usuwanie tabeli Postgres
Teraz, gdy nawiązano połączenie z bazą danych, można utworzyć tabele w nim.

Na przykład utworzyć nową tabelę Postgres przykład za pomocą następującego polecenia:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Po skonfigurowaniu teraz cztery kolumny tabeli przy użyciu następujących nazw kolumn i ograniczeń:

1. Kolumna "name" ma została ograniczona przez polecenie VARCHAR, aby mieć mniej niż 20 znaków.
2. Kolumna "jedzenie" wskazuje element żywności, pozwalających zapewnić każda osoba. VARCHAR ogranicza ten tekst ma być poniżej 30 znaków.
3. Kolumna "potwierdzone" rejestruje, czy osoba potwierdziła przyjęcie składkowe. Dopuszczalne wartości to "Y" i "N".
4. "Data" kolumna pokazuje zarejestrowali się dla zdarzenia. Postgres wymaga zapisać daty w postaci rrrr mm-dd.

Powinny zostać wyświetlone następujące po pomyślnym utworzeniu tabeli:

![image](./media/postgresql-install/no4.png)

Struktura tabeli można również wykonać przy użyciu następującego polecenia:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Dodawanie danych do tabeli
Do wiersza, najpierw wstawić informacji:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Powinny zostać wyświetlone następujące dane wyjściowe:

![image](./media/postgresql-install/no6.png)

Możesz dodać kilka więcej osób, również w tabeli. Poniżej przedstawiono niektóre opcje, lub możesz utworzyć swój własny:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Pokaż tabele
Aby wyświetlić tabelę, użyj następującego polecenia:

    select * from potluck;

Dane wyjściowe to:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Usuń dane w tabeli
Aby usunąć dane w tabeli, użyj następującego polecenia:

    delete from potluck where name=’John’;

Spowoduje to usunięcie wszystkich informacji w wierszu "John". Dane wyjściowe to:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizowanie danych w tabeli
Użyj następującego polecenia, aby zaktualizować dane w tabeli. Dla tego jednego Sandy potwierdziła, że są one uczestnictwa, więc zmienimy RSVP z "N", "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Uzyskaj więcej informacji na temat PostgreSQL
Teraz, że Ukończono instalację programu PostgreSQL w maszynie Wirtualnej systemu Linux platformy Azure, możesz cieszyć się korzystania z niego na platformie Azure. Aby dowiedzieć się, jak PostgreSQL, odwiedź stronę [PostgreSQL witryny sieci Web](https://www.postgresql.org/).

