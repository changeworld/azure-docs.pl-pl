---
title: Instalowanie bazy danych MySQL na maszynie Wirtualnej z dystrybucją systemu OpenSUSE na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się zainstalować MySQL na maszynie wirtualnej systemu Linux OpenSUSE na platformie Azure.
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
ms.date: 07/11/2018
ms.author: za-rhoads
ms.openlocfilehash: 19f53449e21b39eb212f94b100eaf5d26ca4cf13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161621"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalowanie bazy danych MySQL na maszynie wirtualnej z dystrybucją systemu OpenSUSE Linux na platformie Azure

[MySQL](https://www.mysql.com) popularne, typu open-source baza danych SQL. Ten samouczek pokazuje, jak utworzyć maszyny wirtualnej z dystrybucją systemu OpenSUSE Linux, a następnie instalowanie programu MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Tworzenie maszyny wirtualnej z dystrybucją systemu OpenSUSE Linux

Najpierw utwórz grupę zasobów. W tym przykładzie grupa zasobów ma nazwę *mySQSUSEResourceGroup* zostaje utworzony w *wschodnie stany USA* regionu.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Tworzenie maszyny Wirtualnej. W tym przykładzie maszyna wirtualna nazywa *myVM* i rozmiar maszyny Wirtualnej jest *Standard_D2s_v3*, ale należy wybrać [rozmiar maszyny Wirtualnej](sizes.md) uważasz, że jest najbardziej odpowiednie dla danego obciążenia.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Należy również dodać reguły do sieciowej grupy zabezpieczeń, aby zezwolić na ruch przez port 3306 for MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Dowiesz się z maszyną Wirtualną za pomocą protokołu SSH. W tym przykładzie jest publiczny adres IP maszyny wirtualnej *10.111.112.113*. Podczas tworzenia maszyny Wirtualnej, możesz zobaczyć adres IP w danych wyjściowych.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Zaktualizuj maszynę Wirtualną
 
Po połączeniu z maszyną wirtualną, zainstalować system aktualizacje i poprawki. 
   
```bash
sudo zypper update
```

Postępuj zgodnie z monitami, aby zaktualizować maszyny Wirtualnej.

## <a name="install-mysql"></a>Instalowanie programu MySQL 


Instalowanie programu MySQL na maszynie wirtualnej za pomocą protokołu SSH. Odpowiedz na monity, zgodnie z potrzebami.

```bash
sudo zypper install mysql
```
 
Ustaw MySQL uruchamianą podczas rozruchu systemu. 

```bash
sudo systemctl enable mysql
```
Sprawdź, czy włączono MySQL.

```bash
systemctl is-enabled mysql
```

Powinny zostać zwrócone: włączone.

Uruchom ponownie serwer.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Hasło programu MySQL

Po zakończeniu instalacji hasła głównego MySQL jest domyślnie puste. Uruchom **mysql\_bezpiecznego\_instalacji** skrypt, aby zabezpieczyć MySQL. Skrypt wyświetli monit o zmianę hasła głównego MySQL, usunąć konta użytkownika anonimowego, wyłączyć logowania zdalnego katalogu głównego, Usuń test bazy danych i ponowne załadowanie tabeli uprawnień. 

Gdy serwer jest ponownie uruchamiany, ssh z maszyną wirtualną ponownie.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Zaloguj się do bazy danych MySQL

Możesz teraz zalogować i wprowadź w wierszu polecenia MySQL.

```bash  
mysql -u root -p
```
To przełączników do wiersza polecenia MySQL gdzie można wydać instrukcji SQL do interakcji z bazą danych.

Teraz Utwórz nowego użytkownika programu MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Rozdzielana średnikami (;) na końcu wiersza jest kluczowa dla zakończenie polecenia.


## <a name="create-a-database"></a>Tworzenie bazy danych


Tworzenie bazy danych i udzielanie `mysqluser` uprawnień użytkownika.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Bazy danych, nazwy użytkownika i hasła są używane tylko przez skrypty połączenie z bazą danych.  Nazwy kont użytkowników bazy danych nie musi to oznaczać konta użytkowników w systemie.

Włącz logowanie z innego komputera. W tym przykładzie jest adres IP komputera, aby umożliwić logowanie z *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Aby zamknąć narzędzie administracyjne bazy danych MySQL, wpisz:

```    
quit
```


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje o MySQL, zobacz [dokumentacji MySQL](https://dev.mysql.com/doc).




