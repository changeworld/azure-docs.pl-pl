---
title: Resetowanie hasła maszyny Wirtualnej systemu Linux i klucz SSH z poziomu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: Resetowanie hasła maszyny Wirtualnej systemu Linux lub klucza SSH, popraw konfigurację SSH i sprawdzanie spójności dysku przy użyciu rozszerzenia VMAccess z Azure interfejsu wiersza polecenia (CLI)
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: cynthn
ms.openlocfilehash: c36498d2f4fef506dc7047fe91666aceec73c13d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Jak można zresetować hasła maszyny Wirtualnej systemu Linux lub klucza SSH, popraw konfigurację SSH i sprawdzanie spójności dysku przy użyciu rozszerzenia VMAccess
Nie można połączyć z maszyną wirtualną systemu Linux na platformie Azure z powodu zapomniane hasło, nieprawidłowy klucz Secure Shell (SSH), lub na problem z konfiguracją SSH za pomocą rozszerzenia VMAccessForLinux z wiersza polecenia platformy Azure można zresetować hasła lub klucza SSH, napraw SSH Konfiguracja i sprawdzania spójności dysku. 

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonać te kroki przy użyciu modelu usługi Resource Manager](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Z wiersza polecenia platformy Azure, użyj **zestaw rozszerzenia maszyny wirtualnej azure** polecenie do dostępu do poleceń interfejsu wiersza polecenia (Bash, terminali, wiersza polecenia). Uruchom **zestaw rozszerzenia maszyny wirtualnej azure pomocy** użycia szczegółowe rozszerzenia.

Z wiersza polecenia platformy Azure możesz wykonać następujące zadania:

* [Resetowanie hasła](#pwresetcli)
* [Resetuj klucz SSH](#sshkeyresetcli)
* [Resetowanie hasła i klucza SSH](#resetbothcli)
* [Utwórz nowe konto użytkownika sudo](#createnewsudocli)
* [Zresetuj konfigurację protokołu SSH](#sshconfigresetcli)
* [Usuń użytkownika](#deletecli)
* [Wyświetla stan rozszerzenia VMAccess](#statuscli)
* [Sprawdzanie spójności dodanych dysków](#checkdisk)
* [Napraw dodane dyski na maszynie Wirtualnej systemu Linux](#repairdisk)

## <a name="prerequisites"></a>Wymagania wstępne
Należy wykonać następujące czynności:

* Konieczne będzie [instalowanie interfejsu wiersza polecenia Azure](../../../cli-install-nodejs.md) i [nawiązać połączenia z subskrypcją](/cli/azure/authenticate-azure-cli) korzystać z zasobów platformy Azure skojarzonych z Twoim kontem.
* Ustaw tryb dla klasycznym modelu wdrażania, wpisując następujące polecenie w wierszu polecenia:
    ``` 
        azure config mode asm
    ```
* Jeśli chcesz przywrócić jedną mają nowe hasło lub zestawu kluczy SSH. Nie trzeba je, aby zresetować konfiguracji SSH.

## <a name="pwresetcli"></a>Resetowanie hasła
1. Utwórz plik na komputerze o nazwie PrivateConf.json z tych wierszy. Zastąp **myUserName** i **myP@ssW0rd** z własną nazwę użytkownika i hasło i ustawić własne datę wygaśnięcia.

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. Uruchom to polecenie, zastępując nazwę maszyny wirtualnej dla **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>Resetuj klucz SSH
1. Utwórz plik o nazwie PrivateConf.json z tych zawartości. Zastąp **myUserName** i **mySSHKey** wartościami odpowiednimi informacjami.

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. Uruchom to polecenie, zastępując nazwę maszyny wirtualnej dla **myVM**.
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Zresetuj hasło i klucz SSH
1. Utwórz plik o nazwie PrivateConf.json z tych zawartości. Zastąp **myUserName**, **mySSHKey** i **myP@ssW0rd** wartościami odpowiednimi informacjami.

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. Uruchom to polecenie, zastępując nazwę maszyny wirtualnej dla **myVM**.

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>Utwórz nowe konto użytkownika sudo

Jeśli zapomnisz swoją nazwę użytkownika, można użyć rozszerzenia VMAccess do Utwórz nową z urzędem sudo. W takim przypadku istniejące nazwę użytkownika i hasło nie zostaną zmodyfikowane.

Aby utworzyć nowy użytkownik sudo z dostępem do hasła, należy użyć skryptu w [resetowania hasła](#pwresetcli) i określić nową nazwę użytkownika.

Aby utworzyć nowego użytkownika sudo z dostępu do klucza SSH, należy użyć skryptu w [Resetuj klucz SSH](#sshkeyresetcli) i określić nową nazwę użytkownika.

Można również użyć [resetowania hasła i klucza SSH](#resetbothcli) do utworzenia nowego użytkownika z hasła i dostęp do klucza SSH.

## <a name="sshconfigresetcli"></a>Zresetuj konfigurację protokołu SSH
Jeśli konfiguracji SSH jest w stanie niepożądane, użytkownik może również utracić dostęp do maszyny Wirtualnej. Można użyć rozszerzenia VMAccess do zresetowania konfiguracji do stanu domyślnego. W tym celu wystarczy ustawić klucza "reset_ssh" na "True". Rozszerzenia Uruchom ponownie serwer SSH, otwórz port SSH na maszynie Wirtualnej i zresetowanie konfiguracji SSH do wartości domyślnych. Konto użytkownika (nazwy, hasła lub kluczy SSH) nie zostaną zmienione.

> [!NOTE]
> Plik konfiguracji SSH, który pobiera zresetować znajduje się w /etc/ssh/sshd_config.
> 
> 

1. Utwórz plik o nazwie PrivateConf.json z tą zawartością.

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. Uruchom to polecenie, zastępując nazwę maszyny wirtualnej dla **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>Usuń użytkownika
Jeśli chcesz usunąć konto użytkownika, bez logowania do do maszyny Wirtualnej bezpośrednio, możesz użyć tego skryptu.

1. Utwórz plik o nazwie PrivateConf.json z tej zawartości, zastępując nazwę użytkownika, aby usunąć **removeUserName**. 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. Uruchom to polecenie, zastępując nazwę maszyny wirtualnej dla **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>Wyświetla stan rozszerzenia VMAccess
Aby wyświetlić stan rozszerzenia VMAccess, uruchom następujące polecenie.

```
        azure vm extension get
```

## <a name='checkdisk'></a>Sprawdzanie spójności dodanych dysków
Aby uruchomić fsck na wszystkich dyskach na maszynie wirtualnej systemu Linux, należy wykonać następujące czynności:

1. Utwórz plik o nazwie PublicConf.json z tą zawartością. Sprawdź, czy dysk przyjmuje wartość logiczną, czy należy sprawdzić dysków dołączonych do maszyny wirtualnej, czy nie. 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. Uruchom to polecenie do wykonania, zastępując nazwę maszyny wirtualnej dla **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>Napraw dyski
Aby naprawić dysków, które nie są Instalowanie lub błędy konfiguracji instalacji, należy użyć rozszerzenia VMAccess do zresetowania konfiguracji instalacji na maszynie wirtualnej systemu Linux. Zastępując nazwę dysku dla **myDisk**.

1. Utwórz plik o nazwie PublicConf.json z tą zawartością. 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. Uruchom to polecenie do wykonania, zastępując nazwę maszyny wirtualnej dla **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>Kolejne kroki
* Jeśli chcesz użyć poleceń cmdlet programu Azure PowerShell lub szablonów usługi Azure Resource Manager można zresetować hasła lub klucza SSH, zobacz SSH konfiguracji i sprawdzania spójności dysku, napraw [dokumentacją dotyczącą rozszerzenia VMAccess w serwisie GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Można również użyć [portalu Azure](https://portal.azure.com) do resetowania hasła lub klucza SSH maszyny wirtualnej systemu Linux wdrożonych w klasycznym modelu wdrażania. Nie można obecnie używać portalu tej maszyny wirtualnej systemu Linux wdrożonych w modelu wdrażania usługi Resource Manager.
* Zobacz [o rozszerzenia maszyny wirtualnej i funkcjach](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) więcej informacji o przy użyciu rozszerzeń maszyny Wirtualnej dla maszyn wirtualnych platformy Azure.

