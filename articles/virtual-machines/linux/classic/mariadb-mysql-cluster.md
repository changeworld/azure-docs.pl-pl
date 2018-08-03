---
title: Uruchamianie klastra MariaDB (MySQL) na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz MariaDB + Galera MySQL klastra na maszynach wirtualnych platformy Azure
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
ms.openlocfilehash: 2cdc58a827f696d62e6240b90202ee04ce371d07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426857"
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Klastra MariaDB (MySQL): samouczek dotyczący usługi Azure
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager](../../../resource-manager-deployment-model.md) i model klasyczny. W tym artykule opisano klasyczny model wdrażania. Firma Microsoft zaleca się, że większości nowych wdrożeń korzystać z modelu usługi Azure Resource Manager.

> [!NOTE]
> MariaDB Enterprise cluster jest teraz dostępna w witrynie Azure Marketplace. Nowa oferta automatycznie wdroży klaster MariaDB Galera na usłudze Resource Manager. Skorzystaj z nowej oferty z [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/).
>
>

W tym artykule dowiesz się, jak utworzyć Multi-Master [Galera](http://galeracluster.com/products/) klastra [MariaDBs](https://mariadb.org/en/about/) (niezawodnych, skalowalnych i niezawodnych zamiennikiem usługi MySQL) do pracy w środowisku o wysokiej dostępności na platformie Azure maszyny wirtualne.

## <a name="architecture-overview"></a>Omówienie architektury
W tym artykule opisano sposób wykonania następujących czynności:

- Utwórz klaster z trzema węzłami.
- Oddzielne dyski z danymi z dysku systemu operacyjnego.
- Tworzenie dysków z danymi w macierzy RAID-0/rozkładane ustawienie, aby zwiększyć operacje We/Wy.
- Usługi Azure Load Balancer umożliwia równoważenie obciążenia dla trzech węzłów.
- Aby zminimalizować powtarzających się zadań, utworzyć obraz maszyny Wirtualnej, która zawiera MariaDB + Galera i użyć go do utworzenia innych maszyn wirtualnych klastra.

![Architektura systemu](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> W tym temacie używany [wiersza polecenia platformy Azure](../../../cli-install-nodejs.md) narzędzia, więc upewnij się, że ich pobierania i łącz je z subskrypcją platformy Azure zgodnie z instrukcjami. Aby uzyskać odwołanie do poleceń dostępnych w interfejsie wiersza polecenia platformy Azure, zobacz [odniesienie do polecenia wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Będzie trzeba będzie również [Tworzenie klucza SSH do uwierzytelniania] i zanotuj lokalizację pliku PEM.
>
>

## <a name="create-the-template"></a>Tworzenie szablonu
### <a name="infrastructure"></a>Infrastruktura
1. Tworzenie grupy koligacji do przechowywania zasobów ze sobą.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
1. Utwórz sieć wirtualną.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
1. Utwórz konto magazynu do obsługi wszystkich naszych dysków. Więcej niż 40 intensywnie używanych dysków nie należy umieścić na tym samym koncie magazynu, aby uniknąć osiągnięcia 20 000 operacji We/Wy limit konta magazynu. W tym przypadku możesz teraz znacznie poniżej tego limitu, dzięki czemu będą przechowywane wszystkie elementy na tym samym koncie dla uproszczenia.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
1. Znajdź nazwę obrazu maszyny wirtualnej CentOS 7.

        azure vm image list | findstr CentOS
   Dane wyjściowe będą mieć podobny `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   W następnym kroku, należy użyć tej nazwy.
1. Utwórz szablon maszyny Wirtualnej i Zastąp /path/to/key.pem ścieżki, w którym przechowujesz PEM wygenerowanego klucza SSH.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
1. Dołącz cztery dyski z danymi 500 GB do maszyny Wirtualnej do użytku w konfiguracji RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
1. Zaloguj się do szablonu maszyny Wirtualnej, który został utworzony w mariadbhatemplate.cloudapp.net:22 za pomocą protokołu SSH, a potem połączyć przy użyciu klucza prywatnego.

### <a name="software"></a>Oprogramowanie
1. Uzyskaj katalogu głównego.

        sudo su

1. Nainstalovat podporu RAID:

    a. Zainstaluj mdadm.

              yum install mdadm

    b. Utwórz konfigurację RAID0/stripe przy użyciu systemu plików EXT4.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Utwórz katalog punktu instalacji.

              mkdir /mnt/data
    d. Pobierz identyfikator UUID nowo utworzony urządzeniu RAID.

              blkid | grep /dev/md0
    e. Edytuj/etc/fstab.

              vi /etc/fstab
    f. Dodaj urządzenie, aby włączyć automatyczne instalowanie przy rozruchu, zastępując identyfikator UUID uzyskaną z poprzedniego **blkid** polecenia.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Instalowanie nowej partycji.

              mount /mnt/data

1. Zainstaluj MariaDB.

    a. Utwórz plik MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Wprowadź plik repozytorium o następującej zawartości:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Aby uniknąć konfliktów, Usuń istniejące przyrostkowe i mariadb libs.

           yum remove postfix mariadb-libs-*
    d. Zainstaluj MariaDB z Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

1. Przenieś katalog danych MySQL na urządzeniu bloku RAID.

    a. Skopiować bieżący katalog MySQL do nowej lokalizacji, a następnie usuń stare katalogu.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Odpowiednio ustawić uprawnienia dla nowego katalogu.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Utwórz Link symboliczny wskazujący katalogu starej do nowej lokalizacji na partycji RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

1. Ponieważ [SELinux zakłóca działanie klastra](http://galeracluster.com/documentation-webpages/configuration.html#selinux), należy ją wyłączyć dla bieżącej sesji. Edytuj `/etc/selinux/config` wyłączania jego dla kolejnych ponownych uruchomień.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
1. Sprawdź poprawność MySQL działa.

   a. Uruchom oprogramowanie MySQL.

           service mysql start
   b. Zabezpieczenia instalacji programu MySQL, ustaw hasło główne, Usuń użytkowników anonimowych, aby wyłączyć logowania zdalnego katalogu głównego i usunąć bazę danych testowych.

           mysql_secure_installation
   c. Utwórz użytkownika bazy danych dla operacji klastra i opcjonalnie dla aplikacji.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Zatrzymaj MySQL.

            service mysql stop
1. Utwórz symbol zastępczy konfiguracji.

   a. Zmień konfigurację MySQL, aby utworzyć symbol zastępczy dla ustawienia klastra. Nie zastępuj **`<Variables>`** lub usuń znaczniki komentarza teraz. To nastąpi, po utworzeniu maszyny Wirtualnej za pomocą tego szablonu.

            vi /etc/my.cnf.d/server.cnf
   b. Edytuj **[galera]** sekcji, a następnie je Wyczyść.

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
1. Za pomocą FirewallD na CentOS 7, należy otworzyć wymagane porty w zaporze.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * ISTA GALERA: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Załaduj ponownie zapory: `firewall-cmd --reload`

1. Optymalizuj systemu pod kątem wydajności. Aby uzyskać więcej informacji, zobacz [dostrajanie strategii wydajności](optimize-mysql.md).

   a. Edytuj ponownie plik konfiguracyjny MySQL.

            vi /etc/my.cnf.d/server.cnf
   b. Edytuj **[mariadb]** sekcji, a następnie dołącz następującą zawartością:

   > [!NOTE]
   > Firma Microsoft zaleca tego aparatu innodb\_buforu\_pool_size jest 70 procent pamięci maszyny Wirtualnej. W tym przykładzie jej został ustawiony 2.45 GB dla średnich maszyny Wirtualnej platformy Azure, 3,5 GB pamięci RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
1. Zatrzymaj MySQL, wyłącz usługi MySQL na uruchamianie podczas uruchamiania, aby uniknąć zakłóceń w podczas dodawania węzła do klastra i anulować aprowizację maszyny.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
1. Przechwytywanie maszyny Wirtualnej za pośrednictwem portalu. (Obecnie [wystawiać 1268 # narzędzi wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli/issues/1268) w tym artykule wyjaśniono obrazy przechwycone za pomocą narzędzi wiersza polecenia platformy Azure nie należy przechwytywać dołączonych dysków z danymi.)

    a. Zamknij maszynę za pośrednictwem portalu.

    b. Kliknij przycisk **przechwytywania** i określ nazwę obrazu jako **mariadb galera obrazu**. Podaj opis i sprawdź "I mieć uruchomiono agenta waagent".
      
      ![Przechwytywanie maszyny wirtualnej](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Tworzenie klastra
Tworzenie trzech maszyn wirtualnych za pomocą szablonu utworzony, a następnie skonfigurować i uruchomić klaster.

1. Tworzenie pierwszej maszyny Wirtualnej 7 CentOS na podstawie obrazu mariadb galera obrazu, który został utworzony, podając następujące informacje:

 - Nazwa sieci wirtualnej: mariadbvnet
 - Podsieć: mariadb
 - Rozmiar maszyny: średni
 - Nazwa usługi w chmurze: mariadbha (lub dowolną nazwę, który chcesz być dostępne za pośrednictwem mariadbha.cloudapp.net)
 - Nazwa maszyny: mariadb1
 - Nazwa użytkownika: azureuser
 - Dostęp protokołu SSH: włączone
 - Przekazywanie pliku PEM certyfikatu SSH i zastępując /path/to/key.pem ścieżki, w którym przechowujesz PEM wygenerowanego klucza SSH.

   > [!NOTE]
   > Następujące polecenia są dzielone na wiele wierszy w celu uściślenia, ale należy wprowadzić jako jeden wiersz.
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
1. Utwórz dwie maszyny wirtualne z więcej, łącząc je z usługą w chmurze mariadbha. Zmień nazwę maszyny Wirtualnej i SSH port na unikatowy port nie powoduje konflikt z innymi maszynami wirtualnymi w tej samej usłudze w chmurze.

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
  Aby uzyskać MariaDB3:

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
1. Należy uzyskać wewnętrzny adres IP w każdej z trzech maszyn wirtualnych do kolejnego kroku:

    ![Pobieranie adresu IP](./media/mariadb-mysql-cluster/IP.png)
1. Zaloguj się do trzech maszyn wirtualnych i edytować plik konfiguracji na każdym z nich za pomocą protokołu SSH.

        sudo vi /etc/my.cnf.d/server.cnf

    Usuń znaczniki komentarza **`wsrep_cluster_name`** i **`wsrep_cluster_address`** , usuwając **#** na początku wiersza.
    Ponadto Zastąp **`<ServerIP>`** w **`wsrep_node_address`** i **`<NodeName>`** w **`wsrep_node_name`** z Adres IP maszyny Wirtualnej na adres i nazwę, odpowiednio, i usuń znaczniki komentarza te wiersze również.
1. Uruchomienie klastra w MariaDB1 i pozwolić mu działać przy uruchamianiu.

        sudo service mysql bootstrap
        chkconfig mysql on
1. Uruchom oprogramowanie MySQL na MariaDB2 i MariaDB3 i pozwolić mu działać przy uruchamianiu.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Równoważenie obciążenia klastra
Podczas tworzenia klastra maszyny wirtualne zostały dodane do zestawu dostępności o nazwie clusteravset do upewnij się, że zostały one wprowadzane w różnych domenach błędów i aktualizacji tej usługi Azure nigdy nie jest konserwacji na wszystkich komputerach jednocześnie. Ta konfiguracja spełnia wymagania, które są obsługiwane przez Azure Umowa dotycząca poziomu usług (SLA).

Teraz za pomocą usługi Azure Load Balancer zrównoważenia żądań między trzy węzły.

Uruchom następujące polecenia na komputerze przy użyciu wiersza polecenia platformy Azure.

Struktura parametrów polecenia to: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Interfejs wiersza polecenia ustawia interwał sondy modułu równoważenia obciążenia do 15 sekund, które mogą być nieco jest za długa. Zmień go w portalu w obszarze **punktów końcowych** jakichkolwiek maszyn wirtualnych.

![Edytuj punkt końcowy](./media/mariadb-mysql-cluster/Endpoint.PNG)

Wybierz **ponownie skonfigurować zrównoważonym obciążeniu**.

![Skonfiguruj ponownie zrównoważonym obciążeniu](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Zmiana **interwał sondowania** na 5 sekund, a następnie zapisz zmiany.

![Interwał sondowania zmian](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Sprawdź poprawność klastra
Odbywa się trudną pracę. Klaster powinny być teraz dostępne w `mariadbha.cloudapp.net:3306`, która osiąga obciążenia równoważenia i kierować żądania między trzy maszyny wirtualne, sprawnie i efektywnie.

Użyj swojego ulubionego klienta MySQL, aby połączyć lub połączyć się z jednej z maszyn wirtualnych, aby sprawdzić, czy ten klaster działa.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Utwórz bazę danych i wypełnić ją z danymi.

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
W tym artykule utworzono MariaDB trzema węzłami + Galera klastra o wysokiej dostępności na platformie Azure wirtualnych maszyn uruchomionych CentOS 7. Maszyny wirtualne są równoważone za pomocą usługi Azure Load Balancer.

Możesz chcieć Przyjrzyj się [klastra MySQL w systemie Linux w inny sposób](mysql-cluster.md) i sposobów [optymalizacji i testowanie wydajności programu MySQL na platformie Azure maszyn wirtualnych z systemem Linux](optimize-mysql.md).

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
