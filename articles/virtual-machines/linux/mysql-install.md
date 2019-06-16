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
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158435"
---
# <a name="how-to-install-mysql-on-azure"></a>Jak zainstalować oprogramowanie MySQL na platformie Azure
W tym artykule dowiesz się, jak zainstalować i skonfigurować serwer MySQL na maszynie wirtualnej platformy Azure z systemem Linux.


> [!NOTE]
> Musi już mieć Microsoft Azure maszyny wirtualnej z systemem Linux w celu ukończenia tego samouczka. Zobacz [maszyny Wirtualnej systemu Linux platformy Azure w ramach samouczka](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) utworzyć i skonfigurować Maszynę wirtualną systemu Linux przy użyciu `mysqlnode` jako nazwę maszyny Wirtualnej i `azureuser` jako użytkownika przed kontynuowaniem.
> 
> 

W tym przypadku użyj portu 3306 jako MySQL port.  

Firma Microsoft użyje repozytorium pakietu do zainstalowania MySQL5.6 jako przykład w tym artykule. W rzeczywistości MySQL5.6 ma więcej poprawy wydajności niż MySQL5.5.  Więcej informacji na [tutaj](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Zainstaluj MySQL5.6 w systemie Ubuntu
Będziemy używać systemu Linux maszyny Wirtualnej z systemem Ubuntu.


### <a name="install-mysql"></a>Instalowanie programu MySQL

Zainstalować program MySQL Server 5.6, przełączając się `root` użytkownika:

```bash  
sudo su -
```

Instalowanie serwera mysql 5.6:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
Podczas instalacji zostanie wyświetlone okno dialogowe wydają się monit o ustawienie hasła głównego MySQL poniżej, a w tym miejscu należy ustawić hasło.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Wprowadź ponownie hasło, aby potwierdzić.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Logowanie
  
Po zakończeniu instalacji serwera MySQL, MySQL, usługa zostanie uruchomiona automatycznie. Możesz zalogować się do serwera MySQL za pomocą `root` użytkownika, a następnie wprowadź hasło.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>Zarządzanie usługą MySQL

Pobierz stan usługi MySQL

```bash   
service mysql status
```
  
Uruchomienie usługi MySQL

```bash  
service mysql start
```
  
Zatrzymanie usługi MySQL

```bash  
service mysql stop
```
  
Ponowne uruchomienie usługi MySQL

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Instalowanie bazy danych MySQL w systemie Red Hat systemu operacyjnego, CentOS, Oracle Linux
W tym miejscu użyjemy maszyny Wirtualnej systemu Linux CentOS lub Oracle Linux.

### <a name="add-the-mysql-yum-repository"></a>Dodaj repozytorium narzędzia yum MySQL
    
Przełącz się do `root` użytkownika:

```bash  
sudo su -
```

Pobierz i zainstaluj pakiet wydania MySQL:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>Włącz repozytorium MySQL
Edytuj poniżej plik, aby umożliwić pobieranie pakietu MySQL5.6 repozytorium MySQL.

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Zaktualizuj każdą wartość w tym pliku poniżej:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>Instalowanie programu MySQL 

Instalowanie programu MySQL z repozytorium.

```bash  
yum install mysql-community-server
```
  
Pakiet MySQL RPM i wszystkie powiązane pakiety zostaną zainstalowane.


## <a name="manage-the-mysql-service"></a>Zarządzanie usługą MySQL
  
Sprawdź stan usługi serwera MySQL:

```bash  
service mysqld status\
```
  
Sprawdź, czy jest uruchomiony serwer domyślny port MySQL:

```bash  
netstat  –tunlp|grep 3306
```

Uruchom serwer MySQL:

```bash
service mysqld start
```

Zatrzymaj serwer MySQL:

```bash
service mysqld stop
```

Ustaw MySQL do uruchamiania po rozruchu systemu:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>Instalowanie bazy danych MySQL w systemie SUSE Linux

W tym miejscu użyjemy maszyny Wirtualnej systemu Linux z dystrybucją systemu OpenSUSE.

### <a name="download-and-install-mysql-server"></a>Pobierz i zainstaluj serwer MySQL
  
Przełącz się do `root` użytkownika za pomocą poniższego polecenia:  

```bash  
sudo su -
```
  
Pobierz i zainstaluj pakiet MySQL:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>Zarządzanie usługą MySQL
  
Sprawdź stan serwera MySQL:

```bash  
rcmysql status
```
  
Sprawdź, czy domyślny numer portu serwera MySQL:

```bash  
netstat  –tunlp|grep 3306
```

Uruchom serwer MySQL:

```bash
rcmysql start
```

Zatrzymaj serwer MySQL:

```bash
rcmysql stop
```

Ustaw MySQL do uruchamiania po rozruchu systemu:

```bash
insserv mysql
```

## <a name="next-step"></a>Następny krok
Aby uzyskać więcej informacji, zobacz [MySQL](https://www.mysql.com/) witryny sieci Web.

