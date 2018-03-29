---
title: Konfigurowanie bazy danych MySQL na Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować stosu MySQL na maszynie wirtualnej systemu Linux (Ubuntu RedHat rodziny lub systemu operacyjnego) na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: iainfou
ms.openlocfilehash: d91f8cf8455a60d3e0afb2f209ba07933bcdee1c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-install-mysql-on-azure"></a>Jak zainstalować oprogramowanie MySQL na platformie Azure
W tym artykule dowiesz się, jak zainstalować i skonfigurować MySQL na maszynie wirtualnej platformy Azure systemem Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Zainstaluj MySQL na maszynie wirtualnej
> [!NOTE]
> Musi już mieć maszyny wirtualnej Microsoft Azure z systemem Linux w celu ukończenia tego samouczka. Zobacz [samouczek maszyny Wirtualnej systemu Linux Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) tworzenie i konfigurowanie maszyny Wirtualnej systemu Linux z `mysqlnode` jako nazwę maszyny Wirtualnej i `azureuser` jako użytkownika przed kontynuowaniem.
> 
> 

W takim przypadku należy użyć portu 3306 jako MySQL port.  

Nawiązać połączenia z systemem Linux maszyny Wirtualnej utworzonej przy użyciu programu putty. Jeśli używasz maszyny Wirtualnej systemu Linux platformy Azure po raz pierwszy, zobacz temat jak przy użyciu programu putty połączenie z maszyną wirtualną systemu Linux [tutaj](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Używamy repozytorium pakietu do zainstalowania MySQL5.6, na przykład w tym artykule. W rzeczywistości MySQL5.6 ma więcej poprawy wydajności niż MySQL5.5.  Więcej informacji [tutaj](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Jak zainstalować MySQL5.6 na Ubuntu
W tym miejscu użyjemy maszyny Wirtualnej systemu Linux z Ubuntu z platformy Azure.

* Krok 1: Zainstaluj MySQL serwera 5.6 przełącznik `root` użytkownika:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Instalowanie serwerów mysql 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Podczas instalacji zostanie wyświetlone okno dialogowe okna poping do z pytaniem, czy należy ustawić hasła użytkownika root MySQL poniżej, na które należy ustawić hasło tutaj.
  
    ![Obraz](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Wprowadź hasło ponownie, aby potwierdzić.

    ![Obraz](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Krok 2: Serwer MySQL logowania
  
    Po zakończeniu instalacji serwer MySQL, MySQL zostanie uruchomiona automatycznie. Możesz zalogować się serwer MySQL z `root` użytkownika.
    Użyj poniżej polecenia do logowania i wprowadzania hasła.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Krok 3: Zarządzanie uruchomionej usługi MySQL
  
    () Pobierz stan usługi MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) uruchomić usługi MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) zatrzymać usługi MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) uruchom ponownie usługę MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Jak zainstalować MySQL rodziny Red Hat OS, takie jak CentOS, Oracle Linux
W tym miejscu użyjemy maszyny Wirtualnej systemu Linux CentOS lub Oracle Linux.

* Krok 1: Dodaj repozytorium MySQL Yum przełącznika do `root` użytkownika:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Pobierz i zainstaluj pakiet wersji MySQL:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Krok 2: Edycja poniżej plik, aby włączyć Pobieranie pakietu MySQL5.6 repozytorium MySQL.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Zaktualizuj wartości tego pliku poniżej:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Krok 3: Zainstaluj MySQL z repozytorium MySQL zainstalować MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Zostanie zainstalowany pakiet MySQL RPM i wszystkich powiązanych pakietów.
* Krok 4: Zarządzanie uruchomionej usługi MySQL
  
    () sprawdź stan usługi serwera MySQL:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) sprawdź, czy działa serwer domyślny port MySQL:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) uruchom serwer MySQL:

           #[root@mysqlnode ~]#service mysqld start

    d Zatrzymaj serwer MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL zestawu do uruchamiania podczas rozruchu systemu zapasowe:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Jak zainstalować MySQL w systemie SUSE Linux
W tym miejscu użyjemy maszyny Wirtualnej systemu Linux z OpenSUSE.

* Krok 1: Pobierz i zainstaluj serwer MySQL
  
    Przełącz się do `root` użytkownika za pomocą poniższych poleceń:  
  
           #sudo su -
  
    Pobierz i zainstaluj pakiet MySQL:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Krok 2: Zarządzanie uruchomionej usługi MySQL
  
    () sprawdź stan serwera MySQL:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) sprawdź, czy domyślny numer portu serwera MySQL:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) uruchom serwer MySQL:

           #[root@mysqlnode ~]# rcmysql start

    d Zatrzymaj serwer MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL zestawu do uruchamiania podczas rozruchu systemu zapasowe:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Następny krok
Znajdź więcej użycia i informacje dotyczące MySQL [tutaj](https://www.mysql.com/).

