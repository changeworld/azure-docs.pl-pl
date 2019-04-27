---
title: Konfigurowanie bazy danych MySQL na Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować stosu MySQL na maszynie wirtualnej systemu Linux (Ubuntu i Red Hat rodzina systemu operacyjnego) na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: f9e0582a1338bcae7b330c7ece7c3d8cc8593cfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543943"
---
# <a name="how-to-install-mysql-on-azure"></a>Jak zainstalować oprogramowanie MySQL na platformie Azure
W tym artykule dowiesz się, jak zainstalować i skonfigurować serwer MySQL na maszynie wirtualnej platformy Azure z systemem Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Instalowanie bazy danych MySQL na maszynie wirtualnej.
> [!NOTE]
> Musi już mieć Microsoft Azure maszyny wirtualnej z systemem Linux w celu ukończenia tego samouczka. Zobacz [maszyny Wirtualnej systemu Linux platformy Azure w ramach samouczka](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) utworzyć i skonfigurować Maszynę wirtualną systemu Linux przy użyciu `mysqlnode` jako nazwę maszyny Wirtualnej i `azureuser` jako użytkownika przed kontynuowaniem.
> 
> 

W tym przypadku użyj portu 3306 jako MySQL port.  

Łączenie z systemem Linux maszyny Wirtualnej utworzonej przy użyciu programu putty. Jeśli po raz pierwszy używasz maszyny Wirtualnej systemu Linux platformy Azure, zobacz, jak przy użyciu programu putty łączenie z maszyną Wirtualną systemu Linux [tutaj](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Firma Microsoft użyje repozytorium pakietu do zainstalowania MySQL5.6 jako przykład w tym artykule. W rzeczywistości MySQL5.6 ma więcej poprawy wydajności niż MySQL5.5.  Więcej informacji na [tutaj](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Jak zainstalować MySQL5.6 w systemie Ubuntu
W tym miejscu użyjemy maszyny Wirtualnej systemu Linux z systemem Ubuntu na platformie Azure.

* Krok 1: Zainstalować program MySQL Server 5.6 Przełącz do `root` użytkownika:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Instalowanie serwera mysql 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Podczas instalacji zostanie wyświetlone okno dialogowe wydają się monit o ustawienie hasła głównego MySQL poniżej, a w tym miejscu należy ustawić hasło.
  
    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Wprowadź ponownie hasło, aby potwierdzić.

    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Krok 2: Serwer MySQL logowania
  
    Po zakończeniu instalacji serwera MySQL, MySQL, usługa zostanie uruchomiona automatycznie. Możesz zalogować się serwerem MySQL za pomocą `root` użytkownika.
    Użyj poniższego polecenia do logowania i wprowadzania hasła.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Krok 3: Zarządzanie uruchomionej usługi MySQL
  
    () Pobierz stan usługi MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) uruchomienie usługi MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) zatrzymanie usługi MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) uruchom ponownie usługę MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Jak zainstalować oprogramowanie MySQL w systemie Red Hat rodziny takie jak CentOS, Oracle Linux
W tym miejscu użyjemy maszyny Wirtualnej systemu Linux CentOS lub Oracle Linux.

* Krok 1: Dodaj repozytorium narzędzia MySQL Yum przełącznika do `root` użytkownika:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Pobierz i zainstaluj pakiet wydania MySQL:
  
            #[root@mysqlnode ~]# wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Krok 2: Edytuj poniżej plik, aby umożliwić pobieranie pakietu MySQL5.6 repozytorium MySQL.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Zaktualizuj każdą wartość w tym pliku poniżej:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Krok 3: Instalowanie programu MySQL z repozytorium MySQL zainstalować MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Pakiet MySQL RPM i wszystkie powiązane pakiety zostaną zainstalowane.
* Krok 4: Zarządzanie uruchomionej usługi MySQL
  
    () sprawdź stan usługi serwera MySQL:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) sprawdź, czy jest uruchomiony serwer domyślny port MySQL:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) uruchom serwer MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) Zatrzymaj serwer MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) zestaw MySQL do uruchamiania po rozruchu systemu:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Jak zainstalować oprogramowanie MySQL w systemie SUSE Linux
W tym miejscu użyjemy maszyny Wirtualnej systemu Linux z dystrybucją systemu OpenSUSE.

* Krok 1: Pobierz i zainstaluj serwer MySQL
  
    Przełącz się do `root` użytkownika za pomocą poniższego polecenia:  
  
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

    (d) Zatrzymaj serwer MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) zestaw MySQL do uruchamiania po rozruchu systemu:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Następny krok
Znajdź więcej informacji dotyczących użycia i dotyczące MySQL [tutaj](https://www.mysql.com/).

