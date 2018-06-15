---
title: Konfigurowanie PostgreSQL na maszynie Wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować PostgreSQL na maszynie wirtualnej systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 7741f861c5697da1e453c0d613b4b762511cf555
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30241015"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalowanie i konfigurowanie rozwiązania PostgreSQL na platformie Azure
PostgreSQL jest zaawansowane open source bazy danych podobny do Oracle i bazy danych DB2. Zawiera on funkcje gotowe enterprise, takie jak ACID pełnej zgodności, niezawodne przetwarzania transakcyjnego i sterowania współbieżnością wielu wersji. Obsługuje ona również standardy, takie jak ANSI SQL i SQL/MED (w tym otoki obcego danych Oracle, MySQL, MongoDB i wielu innych). Jest to rozszerzalna o obsługę ponad 12 procedurach języków, indeksy GIN i GiST obsługę danych przestrzennych i wiele funkcji NoSQL dla formatu JSON lub aplikacji opartych na protokole klucz wartość.

W tym artykule dowiesz się, jak zainstalować i skonfigurować PostgreSQL na maszynie wirtualnej platformy Azure systemem Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Zainstaluj PostgreSQL
> [!NOTE]
> Musi już mieć Azure maszyny wirtualnej z systemem Linux w celu ukończenia tego samouczka. Aby utworzyć i skonfigurować maszyny Wirtualnej systemu Linux, aby kontynuować, zobacz [samouczek maszyny Wirtualnej systemu Linux Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

W takim przypadku należy użyć portu 1999 jako portu PostgreSQL.  

Nawiązać połączenia z systemem Linux maszyny Wirtualnej utworzonej przy użyciu programu PuTTY. Jeśli po raz pierwszy używasz maszyny Wirtualnej systemu Linux na platformie Azure, zobacz [sposobu użycia protokołu SSH z systemem Linux na platformie Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) informacje na temat nawiązywania połączenia z maszyną wirtualną systemu Linux przy użyciu programu PuTTY.

1. Uruchom następujące polecenie, aby przełączyć się do katalogu głównego (Administrator):
   
        # sudo su -
2. Niektórych dystrybucji zależnościami, które należy zainstalować przed zainstalowaniem PostgreSQL. Sprawdź, czy Twoje distro na tej liście, a następnie uruchom odpowiednie polecenie:
   
   * Red Hat podstawowego systemu Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian podstawowego systemu Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Pobierz PostgreSQL w katalogu głównym, a następnie Rozpakuj pakiet:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Powyższy kod jest przykładem. Można znaleźć bardziej szczegółowe adres pobierania w [indeks/pub/źródło/](https://ftp.postgresql.org/pub/source/).
4. Można uruchomić kompilacji, uruchom następujące polecenia:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Jeśli chcesz skompilować wszystkie czynności, które mogą być tworzone, w tym dokumentację (strony HTML i man) oraz dodatkowych modułów (contrib), uruchom następujące polecenie:
   
        # gmake install-world
   
    Powinien zostać wyświetlony następujący komunikat potwierdzenia:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Skonfiguruj PostgreSQL
1. (Opcjonalnie) Utwórz link symboliczny do skrócenia PostgreSQL odwołanie do zawiera numer wersji:
   
        # ln -s /opt/pgsql9.3.5 /opt/pgsql
2. Utwórz katalog dla bazy danych:
   
        # mkdir -p /opt/pgsql_data
3. Tworzenie użytkowników nie głównego i modyfikowanie profilu użytkownika. Następnie należy przełączyć się do tego nowego użytkownika (o nazwie *postgres* w naszym przykładzie):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Ze względów bezpieczeństwa PostgreSQL używa administratora — aby zainicjować, uruchomić lub zamknąć bazy danych.
   > 
   > 
4. Edytuj *bash_profile* pliku, wprowadzając poniższe polecenia. Te wiersze zostaną dodane na końcu *bash_profile* pliku:
   
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
5. Wykonanie *bash_profile* pliku:
   
        $ source .bash_profile
6. Sprawdzanie poprawności instalacji za pomocą następującego polecenia:
   
        $ which psql
   
    Jeśli instalacja zakończy się pomyślnie, zostanie wyświetlony następujący odpowiedzi:
   
        /opt/pgsql/bin/psql
7. Możesz również sprawdzić wersji PostgreSQL:
   
        $ psql -V
8. Inicjowanie bazy danych:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Powinien zostać wyświetlony następujący komunikat:

![Obraz](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Konfigurowanie PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Uruchom następujące polecenia:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Zmodyfikuj dwie zmienne w pliku /etc/init.d/postgresql. Prefiks jest ustawiona na ścieżkę instalacji programu PostgreSQL: **/opt/pgsql**. PGDATA ma ustawioną wartość ścieżki do magazynu danych programu PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![Obraz](./media/postgresql-install/no2.png)

Zmienianie pliku dokonanie pliku wykonywalnego:

    # chmod +x /etc/init.d/postgresql

Uruchom PostgreSQL:

    # /etc/init.d/postgresql start

Sprawdź, czy punkt końcowy PostgreSQL na:

    # netstat -tunlp|grep 1999

Powinny być widoczne następujące dane wyjściowe:

![Obraz](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Połączenie z bazą danych Postgres
Przełącz do użytkownika postgres ponownie:

    # su - postgres

Utwórz bazę danych Postgres:

    $ createdb events

Połączenie z bazą danych zdarzenia, który został właśnie utworzony:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Tworzenie i usuwanie tabeli Postgres
Teraz, gdy nawiązano połączenie bazy danych, można utworzyć tabele w nim.

Na przykład utwórz nową tabelę Postgres przykład za pomocą następującego polecenia:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Teraz skonfigurowane cztery kolumny tabeli z następujących nazw kolumn i ograniczenia:

1. Kolumna "name" ma ograniczone przez polecenie VARCHAR mieć mniej niż 20 znaków.
2. Kolumna "żywności" wskazuje element żywności pozwalających każda osoba. VARCHAR ogranicza ten tekst ma być poniżej 30 znaków.
3. Kolumna "potwierdzone" rejestruje, czy osoba potwierdziła przyjęcie składkowe. Dopuszczalne wartości to "Y" i "N".
4. Pokazuje kolumny "date", po ich zarejestrowaniu zdarzenia. Postgres wymaga zapisać daty w postaci rrrr mm-dd.

Powinny być widoczne następujące po pomyślnym utworzeniu tabeli:

![Obraz](./media/postgresql-install/no4.png)

Możesz również sprawdzić strukturę tabeli za pomocą następującego polecenia:

![Obraz](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Dodawanie danych do tabeli
Najpierw należy wstawić informacji do wiersza:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Te dane wyjściowe powinny być widoczne:

![Obraz](./media/postgresql-install/no6.png)

Dodawanie z kilka osób, do tabeli. Poniżej przedstawiono niektóre opcje, lub utworzyć własny:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Pokaż tabele
Aby wyświetlić tabelę, użyj następującego polecenia:

    select * from potluck;

Wynik jest:

![Obraz](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Usuń dane w tabeli
Aby usunąć dane w tabeli, użyj następującego polecenia:

    delete from potluck where name=’John’;

Spowoduje to usunięcie wszystkich informacji w wierszu "Jan". Wynik jest:

![Obraz](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizuj dane w tabeli
Użyj następującego polecenia, aby zaktualizować dane w tabeli. Dla, Sandy potwierdzi, że jest ona uczestnictwa, więc zostanie zmieniona jej RSVP z "N" do "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Uzyskaj więcej informacji o PostgreSQL
Teraz, gdy została ukończona instalacja PostgreSQL w maszynie Wirtualnej systemu Linux platformy Azure, można oglądać używania go w usłudze Azure. Aby dowiedzieć się więcej na temat PostgreSQL, odwiedź stronę [PostgreSQL witryny sieci Web](http://www.postgresql.org/).

