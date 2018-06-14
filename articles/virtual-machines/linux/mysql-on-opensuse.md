---
title: Zainstaluj MySQL na maszynie Wirtualnej OpenSUSE na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się zainstalować MySQL na maszynie OpenSUSE VMirtual systemu Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: cynthn
ms.openlocfilehash: 88bd895cb3a384f1ada0394fe2da206aca86b981
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2018
ms.locfileid: "28001178"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalowanie bazy danych MySQL na maszynie wirtualnej z dystrybucją systemu OpenSUSE Linux na platformie Azure

[MySQL](http://www.mysql.com) popularnych, open source baza danych SQL. W tym samouczku przedstawiono sposób tworzenia maszyny wirtualnej z systemem OpenSUSE Linux, a następnie zainstalować MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Istnieje możliwość instalacji i używania interfejsu wiersza polecenia lokalnie, należy najpierw wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Utwórz maszynę wirtualną z systemem OpenSUSE Linux

Najpierw utwórz grupę zasobów. W tym przykładzie mamy nazwy grupy zasobów *mySQSUSEResourceGroup* i tworzenie w *wschodnie stany USA* regionu.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Tworzenie maszyny Wirtualnej. W tym przykładzie mamy nazewnictwa maszyny Wirtualnej *myVM*. Zamierzamy również użyć rozmiaru maszyny Wirtualnej *Standard_D2s_v3*, ale należy wybrać [rozmiar maszyny Wirtualnej](sizes.md) uważasz, że jest najbardziej odpowiednia dla obciążenia.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Należy również dodać regułę do grupy zabezpieczeń sieci, aby zezwolić na ruch przez port 3306 MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Użyjesz SSH do nawiązania połączenia z maszyną Wirtualną. W tym przykładzie jest publiczny adres IP maszyny wirtualnej *10.111.112.113*. Adres IP w danych wyjściowych jest widoczny po utworzeniu maszyny Wirtualnej.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Zaktualizuj maszynę Wirtualną
 
Po nawiązaniu połączenia z maszyną wirtualną, zainstalować system aktualizacje i poprawki. 
   
```bash
sudo zypper update
```

Postępuj zgodnie z monitami, aby zaktualizować maszyny Wirtualnej.

## <a name="install-mysql"></a>Instalowanie programu MySQL 


Zainstaluj MySQL w maszynie Wirtualnej za pomocą protokołu SSH. Odpowiedz na monity zależnie od potrzeb.

```bash
sudo zypper install mysql
```
 
Ustaw MySQL można uruchomić podczas rozruchu systemu. 

```bash
sudo systemctl enable mysql
```
Sprawdź, czy jest włączona funkcja MySQL.

```bash
systemctl is-enabled mysql
```

Powinny zostać zwrócone: włączone.


## <a name="mysql-password"></a>Hasło MySQL

Po zakończeniu instalacji MySQL hasła głównego jest domyślnie pusta. Uruchom **mysql\_bezpiecznego\_instalacji** skryptu, aby zabezpieczyć MySQL. Skrypt wyświetli monit o zmianę hasła głównego MySQL, usunąć konta użytkownika anonimowego, wyłącz logowania zdalnego głównego, usunąć bazy danych testu i ponownie załadować tabelę uprawnień. 


```bash
mysql_secure_installation
```

## <a name="log-in-to-mysql"></a>Zaloguj się do MySQL

Można teraz zalogować się i wprowadź w wierszu polecenia programu MySQL.

```bash  
mysql -u root -p
```
To przełączników wiersza MySQL których możesz wykonywać instrukcje SQL do interakcji z bazą danych.

Teraz można utworzyć nowego użytkownika MySQL.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Średnikami (;) na końcu wiersza jest kluczowa dla zakończenia polecenia.


## <a name="create-a-database"></a>Tworzenie bazy danych


Utwórz bazę danych i udzielić `mysqluser` uprawnienia użytkownika.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Bazy danych, nazwy użytkownika i hasła są używane tylko przez skrypty łączenia z bazą danych.  Nazwy kont użytkowników bazy danych nie przedstawiają niekoniecznie konta użytkowników w systemie.

Włącz logowanie z innego komputera. W tym przykładzie jest adres IP komputera, na którym chcemy, aby zalogować się z *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Aby zamknąć narzędzie administracyjne bazy danych MySQL, wpisz:

```    
quit
```


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje o MySQL, zobacz [dokumentacji MySQL](http://dev.mysql.com/doc/index-topic.html).




