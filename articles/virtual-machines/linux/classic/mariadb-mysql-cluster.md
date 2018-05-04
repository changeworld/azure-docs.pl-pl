---
title: Uruchom klaster MariaDB (MySQL) na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz MariaDB + Galera MySQL klastra na maszynach wirtualnych Azure
services: virtual-machines-linux
documentationcenter: ''
author: sabbour
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 4a3eede532345f8628af1722a06531571f01afbf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Klaster MariaDB (MySQL): samouczek platformy Azure
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager](../../../resource-manager-deployment-model.md) i klasycznym. W tym artykule opisano klasyczny model wdrażania. Firma Microsoft zaleca, aby większości nowych wdrożeń korzystać modelu usługi Azure Resource Manager.

> [!NOTE]
> Klaster MariaDB przedsiębiorstwa jest obecnie dostępna w portalu Azure Marketplace. Klaster MariaDB Galera na usługi Azure Resource Manager automatycznie wdraża aplikację nową ofertę. Należy używać nową ofertę z [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/).
>
>

W tym artykule przedstawiono sposób tworzenia wielu wzorca [Galera](http://galeracluster.com/products/) klaster [MariaDBs](https://mariadb.org/en/about/) (zastępuje niezawodne, skalowalne i niezawodne dzięki wsuwanej konstrukcji MySQL) do pracy w środowisku wysokiej dostępności dla maszyn wirtualnych platformy Azure.

## <a name="architecture-overview"></a>Omówienie architektury
W tym artykule opisano sposób wykonania następujących czynności:

- Tworzenie klastra z trzema węzłami.
- Oddzielne dyski danych z dysku systemu operacyjnego.
- Tworzenie dysków danych w macierzy RAID-0/rozkładane ustawienie, aby zwiększyć liczbę IOPS.
- Moduł równoważenia obciążenia Azure umożliwia równoważenie obciążenia dla trzech węzłów.
- Aby zminimalizować powtarzających się zadań, należy utworzyć obraz maszyny Wirtualnej, który zawiera MariaDB + Galera i użyć go do utworzenia klastra innych maszyn wirtualnych.

![Architektura systemu](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> W tym temacie używa [interfejsu wiersza polecenia Azure](../../../cli-install-nodejs.md) narzędzia, dlatego upewnij się, że je pobrać i połączenie ich z subskrypcją platformy Azure zgodnie z instrukcjami. Jeśli potrzebujesz odwołanie z poleceniami, które są dostępne w wiersza polecenia platformy Azure, zobacz [Dokumentacja poleceń interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Należy również [Tworzenie klucza SSH do uwierzytelniania] i zanotuj lokalizację pliku PEM.
>
>

## <a name="create-the-template"></a>Tworzenie szablonu
### <a name="infrastructure"></a>Infrastruktura
1. Utwórz grupę koligacji do przechowywania zasobów ze sobą.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Utwórz sieć wirtualną.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Utwórz konto magazynu do obsługi wszystkich naszych dysków. Więcej niż 40 intensywnie używane dyski nie należy umieścić na tego samego konta magazynu, aby uniknąć naciśnięcie 20 000 limitu konta magazynu IOPS. Możesz w tym przypadku znacznie poniżej ten limit, więc będą przechowywane wszystkie elementy na tego samego konta dla uproszczenia.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Znajdź nazwę obrazu maszyny wirtualnej CentOS 7.

        azure vm image list | findstr CentOS
   Dane wyjściowe będą wyglądać mniej więcej tak `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Użyj tej nazwy w następnym kroku.
5. Utwórz szablon maszyny Wirtualnej i Zastąp /path/to/key.pem ścieżkę przechowywania klucza SSH wygenerowanego PEM.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Dołącz cztery dyski 500 GB danych do maszyny Wirtualnej do użytku w konfiguracji RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Używanie protokołu SSH do logowania się w szablonie maszyny Wirtualnej, który został utworzony na mariadbhatemplate.cloudapp.net:22 i połączyć przy użyciu klucza prywatnego.

### <a name="software"></a>Oprogramowanie
1. Pobierz katalogu głównego.

        sudo su

2. Zainstaluj obsługę RAID:

    a. Zainstaluj mdadm.

              yum install mdadm

    b. Utwórz konfigurację RAID0/stripe EXT4 systemie plików.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Utworzyć katalogu punktu instalacji.

              mkdir /mnt/data
    d. Pobierz identyfikator UUID nowo utworzony urządzenie RAID.

              blkid | grep /dev/md0
    e. Edytuj /etc/fstab.

              vi /etc/fstab
    f. Dodaj urządzenie, aby włączyć automatyczne instalowanie przy rozruchu, zamieniając identyfikator UUID wartość uzyskane z poprzedniej **blkid** polecenia.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Instalowanie nowej partycji.

              mount /mnt/data

3. Zainstaluj MariaDB.

    a. Utwórz plik MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Należy wypełnić repozytorium pliku o następującej treści:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Aby uniknąć konfliktów, Usuń istniejące przyrostek i mariadb biblioteki.

           yum remove postfix mariadb-libs-*
    d. Zainstaluj MariaDB z Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Przenieś katalog danych MySQL na urządzeniu bloku RAID.

    a. Skopiuj bieżący katalog MySQL do nowej lokalizacji i Usuń stare katalog.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. W związku z tym ustawić uprawnień dla nowego katalogu.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Tworzenie łącza symbolicznego, wskazujący stary katalogu do nowej lokalizacji na partycji RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Ponieważ [SELinux zakłóca działanie klastra](http://galeracluster.com/documentation-webpages/configuration.html#selinux), należy ją wyłączyć w bieżącej sesji. Edytuj `/etc/selinux/config` wyłączyć tę funkcję dla kolejnych ponownego uruchomienia.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Sprawdź poprawność uruchamia MySQL.

   a. Uruchom MySQL.

           service mysql start
   b. Secure instalacji MySQL, ustawianie hasła głównego usunąć użytkowników anonimowych, można wyłączyć głównego zdalnego logowania i Usuń testowej bazy danych.

           mysql_secure_installation
   c. Utwórz użytkownika w bazie danych dla operacji klastra i opcjonalnie dla aplikacji.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Zatrzymaj MySQL.

            service mysql stop
7. Utwórz symbol zastępczy konfiguracji.

   a. Zmień konfigurację MySQL, aby utworzyć symbol zastępczy ustawienia klastra. Nie zastępuj **`<Variables>`** lub Usuń komentarz teraz. Które nastąpi po utworzeniu maszyny Wirtualnej za pomocą tego szablonu.

            vi /etc/my.cnf.d/server.cnf
   b. Edytuj **[galera]** sekcji i wyczyszczenie go.

   c. Edytuj **[mariadb]** sekcji.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. Otwórz wymagane porty zapory przy użyciu FirewallD na CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Załaduj ponownie zapory: `firewall-cmd --reload`

9. Optymalizacja wydajności systemu. Aby uzyskać więcej informacji, zobacz [strategii dostrajania wydajności](optimize-mysql.md).

   a. Przeprowadź edycję pliku konfiguracji MySQL ponownie.

            vi /etc/my.cnf.d/server.cnf
   b. Edytuj **[mariadb]** sekcji i Dołącz następującą zawartość:

   > [!NOTE]
   > Firma Microsoft zaleca tego innodb\_buforu\_pool_size wynosi 70 procent pamięci maszyny Wirtualnej. W tym przykładzie ustawieniu co 2.45 GB dla maszyny Wirtualnej platformy Azure w wersji 3.5 GB pamięci RAM nośnika.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Zatrzymaj MySQL, wyłącz usługi MySQL uruchamiania podczas uruchamiania, aby uniknąć zakłócania działania klastra podczas dodawania węzła i anulowanie zastrzeżenia komputera.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Przechwytywanie maszyny Wirtualnej za pośrednictwem portalu. (Aktualnie [wystawiać 1268 # w narzędziach wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli/issues/1268) w tym artykule wyjaśniono obrazy przechwycone przez narzędzia wiersza polecenia platformy Azure nie należy przechwytywać dysków dołączonych danych.)

    a. Zamknij maszynę za pośrednictwem portalu.

    b. Kliknij przycisk **przechwytywania** i określ nazwę obrazu jako **mariadb galera obrazu**. Podaj opis i sprawdź "Uruchomiono agenta waagent."
      
      ![Przechwytywanie maszyny wirtualnej](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Tworzenie klastra
Utwórz trzy maszyny wirtualne z szablonem utworzone, a następnie skonfigurować i uruchomić klaster.

1. Tworzenie pierwszej maszyny Wirtualnej 7 CentOS z obrazu mariadb galera obrazu, który został utworzony, podając następujące informacje:

 - Nazwa sieci wirtualnej: mariadbvnet
 - Podsieci: mariadb
 - Rozmiar maszyny: średni
 - Nazwa usługi w chmurze: mariadbha (lub nazwa ma zostać będą dostępne za pośrednictwem mariadbha.cloudapp.net)
 - Nazwa maszyny: mariadb1
 - Nazwa użytkownika: azureuser
 - Dostęp SSH: włączone
 - Przekazywanie pliku PEM certyfikatu SSH i zastępowanie /path/to/key.pem ze ścieżką przechowywania klucza SSH wygenerowanego PEM.

   > [!NOTE]
   > Poniższe polecenia są podzielić na wiele wierszy z myślą o przejrzystości, ale należy wprowadzić jako jeden wiersz.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. Utwórz dwie maszyny wirtualne, łącząc je do usługi w chmurze mariadbha. Zmień nazwę maszyny Wirtualnej i portu SSH do portu unikatowy nie powoduje konflikt z innych maszyn wirtualnych w tej samej usłudze w chmurze.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  Dla MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. Należy uzyskać wewnętrzny adres IP każdego z trzech maszyn wirtualnych do następnego kroku:

    ![Pobieranie adresu IP](./media/mariadb-mysql-cluster/IP.png)
4. Używanie protokołu SSH, aby zalogować się do trzech maszyn wirtualnych i edytować pliku konfiguracji na każdym z nich.

        sudo vi /etc/my.cnf.d/server.cnf

    Usuń znaczniki komentarza **`wsrep_cluster_name`** i **`wsrep_cluster_address`** przez usunięcie **#** na początku wiersza.
    Ponadto Zastąp **`<ServerIP>`** w **`wsrep_node_address`** i **`<NodeName>`** w **`wsrep_node_name`** z adresem IP maszyny Wirtualnej adresu i nazwa, i Usuń komentarz także te wiersze.
5. Uruchomienie klastra w MariaDB1 i pozwól mu uruchamiane automatycznie.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Uruchom MySQL na MariaDB2 i MariaDB3 i pozwól mu uruchamiane automatycznie.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Równoważenie obciążenia klastra
Po utworzeniu klastra maszyny wirtualne zostały dodane do zestawu dostępności o nazwie clusteravset, aby zapewnić umieszczają w różnych domenach awarii i aktualizacji i że Azure nigdy nie jest konserwacji na wszystkich komputerach jednocześnie. Ta konfiguracja spełnia wymagania obsługiwany przez Azure umowę dotyczącą poziomu usług (SLA).

Teraz użyć modułu równoważenia obciążenia Azure równoważenie żądań między trzy węzły.

Uruchom następujące polecenia na komputerze przy użyciu wiersza polecenia platformy Azure.

Struktura parametrów polecenia jest: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Interfejsu wiersza polecenia ustawia interwał sondy modułu równoważenia obciążenia na 15 sekund, które mogą być nieco zbyt długa. Zmień go w portalu w obszarze **punkty końcowe** dla poszczególnych maszyn wirtualnych.

![Edytuj punktu końcowego](./media/mariadb-mysql-cluster/Endpoint.PNG)

Wybierz **ponownie skonfigurować zestaw o zrównoważonym obciążeniu**.

![Skonfiguruj ponownie zestaw z równoważeniem obciążenia](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Zmień **interwał sondowania** na 5 sekund, a następnie zapisz zmiany.

![Interwał sondowania zmian](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Sprawdzanie poprawności klastra
Praca odbywa się. Klastra powinna być teraz dostępne w `mariadbha.cloudapp.net:3306`, które trafienia żądań równoważenia i tras obciążenia między maszynami wirtualnymi trzy sprawnie i wydajnie.

Użyj swojego ulubionego klienta MySQL nawiązania połączenia lub połączyć się z jednej z maszyn wirtualnych, aby sprawdzić, czy ten klaster działa.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Następnie utwórz bazę danych i wypełnić ją niektórych danych.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Bazy danych utworzonej zwraca poniższej tabeli:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
W tym artykule tworzone MariaDB trzy + Galera klastra wysokiej dostępności na platformie Azure wirtualnych maszyn uruchomionych CentOS 7. Maszyn wirtualnych jest równoważone z modułem równoważenia obciążenia w Azure.

Można przyjrzeć się [klastra MySQL w systemie Linux w inny sposób](mysql-cluster.md) i sposoby [optymalizacji i testowania wydajności MySQL na maszynach wirtualnych systemu Linux Azure](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[Tworzenie klucza SSH do uwierzytelniania]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
