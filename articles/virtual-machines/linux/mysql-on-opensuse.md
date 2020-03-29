---
title: Instalowanie aplikacji MySQL na maszynie Wirtualnej OpenSUSE na platformie Azure
description: Dowiedz się, jak zainstalować mysql na maszynie wirtualnej OpenSUSE Linux na platformie Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 0d3f0a61da3654c31c99cfac43c86b081876f700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944576"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalowanie mysql na maszynie wirtualnej z systemem OpenSUSE Linux na platformie Azure

[MySQL](https://www.mysql.com) to popularna, open source baza danych SQL. W tym samouczku pokazano, jak utworzyć maszynę wirtualną z systemem OpenSUSE Linux, a następnie zainstalować MySQL.


Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Tworzenie maszyny wirtualnej z systemem OpenSUSE Linux

Najpierw utwórz grupę zasobów. W tym przykładzie grupa zasobów nosi nazwę *mySQSUSEResourceGroup* i jest tworzona w regionie *Wschodnie stany USA.*

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Utwórz maszynę wirtualną. W tym przykładzie maszyna wirtualna nosi nazwę *myVM* i rozmiar maszyny Wirtualnej jest *Standard_D2s_v3*, ale należy wybrać [rozmiar maszyny Wirtualnej,](sizes.md) który uważasz za najbardziej odpowiedni dla obciążenia.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Należy również dodać regułę do sieciowej grupy zabezpieczeń, aby zezwolić na ruch przez port 3306 dla MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Użyjesz SSH, aby połączyć się z maszyną wirtualną. W tym przykładzie publiczny adres IP maszyny Wirtualnej to *10.111.112.113*. Adres IP w danych wyjściowych podczas tworzenia maszyny Wirtualnej.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aktualizowanie maszyny Wirtualnej
 
Po nawiązaniu połączenia z maszyną wirtualną zainstaluj aktualizacje systemu i poprawki. 
   
```bash
sudo zypper update
```

Postępuj zgodnie z instrukcjami, aby zaktualizować maszynę wirtualną.

## <a name="install-mysql"></a>Instalowanie programu MySQL 


Zainstaluj MySQL w maszynie wirtualnej przez SSH. Odpowiadanie na monity, stosownie do przypadku.

```bash
sudo zypper install mysql
```
 
Ustaw mysql, aby uruchamiał się po uruchomieniu systemu. 

```bash
sudo systemctl enable mysql
```
Sprawdź, czy mySQL jest włączony.

```bash
systemctl is-enabled mysql
```

To powinno powrócić: włączone.

Uruchom ponownie serwer.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Hasło MySQL

Po instalacji hasło główne MySQL jest domyślnie puste. Uruchom skrypt **bezpiecznej\_\_instalacji mysql** w celu zabezpieczenia MySQL. Skrypt monituje o zmianę hasła głównego MySQL, usunięcie anonimowych kont użytkowników, wyłączenie zdalnego logowania się do roota, usunięcie testowych baz danych i ponowne załadowanie tabeli uprawnień. 

Po ponownym uruchomieniu serwera, ssh do maszyny Wirtualnej ponownie.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Zaloguj się do MySQL

Teraz możesz się zalogować i wprowadzić monit MySQL.

```bash  
mysql -u root -p
```
Spowoduje to przełączenie monitu MySQL, w którym można wystawiać instrukcje SQL w celu interakcji z bazą danych.

Teraz utwórz nowego użytkownika MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Średnik (;) na końcu wiersza ma kluczowe znaczenie dla zakończenia polecenia.


## <a name="create-a-database"></a>Tworzenie bazy danych


Utwórz bazę `mysqluser` danych i udziel uprawnień użytkownika.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Nazwy użytkowników i hasła bazy danych są używane tylko przez skrypty łączące się z bazą danych.  Nazwy kont użytkowników bazy danych niekoniecznie reprezentują rzeczywiste konta użytkowników w systemie.

Włącz logowanie z innego komputera. W tym przykładzie adres IP komputera, który zezwala na logowanie z to *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Aby zakończyć działanie administracyjne bazy danych MySQL, wpisz:

```    
quit
```


## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje na temat mysql można znaleźć w [dokumentacji MySQL](https://dev.mysql.com/doc).




