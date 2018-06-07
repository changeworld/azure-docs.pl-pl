---
title: Tworzenie bazy danych programu Oracle w maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Szybko uzyskać bazy danych bazy danych programu Oracle 12c w górę i uruchomione w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: rclaus
ms.openlocfilehash: 8ff463b89b395947a66db6d067b6ba32281087ba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657902"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Tworzenie bazy danych programu Oracle w maszynie Wirtualnej platformy Azure

Szczegóły tego przewodnika, przy użyciu wiersza polecenia platformy Azure, aby wdrożyć maszynę wirtualną platformy Azure z [obrazu galerii witryny marketplace Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) w celu utworzenia bazy danych Oracle 12 c. Po wdrożeniu serwera będzie łączyć za pośrednictwem protokołu SSH, aby skonfigurować bazę danych programu Oracle. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną (VM), należy użyć [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. 

W poniższym przykładzie utworzono maszynę wirtualną o nazwie `myVM`. Tworzy również kluczy SSH, jeśli nie już istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po utworzeniu maszyny Wirtualnej Azure CLI Wyświetla informacje podobne do poniższego przykładu. Zwróć uwagę na wartość dla `publicIpAddress`. Ten adres umożliwia dostęp do maszyny Wirtualnej.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Aby utworzyć sesję SSH z maszyną Wirtualną, użyj następującego polecenia. Zastąp adres IP z `publicIpAddress` wartość dla maszyny Wirtualnej.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Utwórz bazę danych

Oprogramowanie Oracle jest już zainstalowana na obrazu z witryny Marketplace. Tworzenie przykładowej bazy danych w następujący sposób. 

1.  Przełącz się do *oracle* administratora, a następnie zainicjować odbiornika dla rejestrowania:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    Dane wyjściowe będą podobne do następujących:

    ```bash
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Tworzenie bazy danych:

    ```bash
    dbca -silent \
           -createDatabase \
           -templateName General_Purpose.dbc \
           -gdbname cdb1 \
           -sid cdb1 \
           -responseFile NO_VALUE \
           -characterSet AL32UTF8 \
           -sysPassword OraPasswd1 \
           -systemPassword OraPasswd1 \
           -createAsContainerDatabase true \
           -numberOfPDBs 1 \
           -pdbName pdb1 \
           -pdbAdminPassword OraPasswd1 \
           -databaseType MULTIPURPOSE \
           -automaticMemoryManagement false \
           -storageType FS \
           -ignorePreReqs
    ```

    Trwa kilka minut, aby utworzyć bazę danych.

3. Ustaw zmienne Oracle

Przed nawiązaniem połączenia należy ustawić dwie zmienne środowiskowe: *ORACLE_HOME* i *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Możesz również dodać zmienne ORACLE_HOME i ORACLE_SID do pliku .bashrc. Spowoduje to zapisanie zmiennych środowiskowych dla przyszłych logowania. Potwierdzić poniższe instrukcje zostały dodane do `~/.bashrc` pliku za pomocą dowolnego edytora.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express łączności

Narzędzia zarządzania graficznego interfejsu użytkownika, który można użyć, aby zapoznać się z bazy danych skonfiguruj Oracle EM Express. Aby połączyć się Oracle EM Express, należy najpierw skonfigurować port w oprogramowaniu Oracle. 

1. Połączenia z bazą danych przy użyciu sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Po nawiązaniu połączenia należy ustawić portu 5502 EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Otwórz kontener PDB1 Jeśli jeszcze nie otwarty, ale pierwszym sprawdzanie stanu:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Dane wyjściowe będą podobne do następujących:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Jeśli OPEN_MODE dla `PDB1` nie jest do odczytu zapisu, następnie uruchom polecenia temacie, aby otworzyć PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Należy wpisać `quit` zakończenia sesji sqlplus i typ `exit` się wylogować użytkownika oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatyzowanie uruchamiania bazy danych i zamykania

Baza danych Oracle domyślnie nie automatyczne uruchamianie po ponownym uruchomieniu maszyny Wirtualnej. Aby skonfigurować bazę danych programu Oracle do automatycznego uruchamiania, najpierw zaloguj się jako katalogu głównego. Następnie tworzenie i aktualizowanie niektórych plików systemowych.

1. Zalogować się jako katalogu głównego
    ```bash
    sudo su -
    ```

2.  Za pomocą ulubionego edytora, przeprowadź edycję pliku `/etc/oratab` i zmienić domyślną `N` do `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Utwórz plik o nazwie `/etc/init.d/dbora` i Wklej poniższą zawartość:

    ```
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Zmiana uprawnień do plików z *chmod* w następujący sposób:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Utworzenie łącza symbolicznego uruchamiania i wyłączania w następujący sposób:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Aby przetestować zmiany, uruchom ponownie maszynę Wirtualną:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Otwórz porty dla łączności

Ostatnim zadaniem jest skonfigurowanie niektóre zewnętrzne punkty końcowe. Aby skonfigurować grupy zabezpieczeń sieci Azure, która chroni maszyny Wirtualnej, zakończyć sesję SSH w maszynie Wirtualnej (powinien mieć zostały kopać poza SSH po ponownym uruchomieniu komputera w poprzednim kroku). 

1.  Aby otworzyć punktu końcowego, który umożliwia dostęp do bazy danych Oracle zdalnie, należy utworzyć regułę sieciowej grupy zabezpieczeń z [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) w następujący sposób: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Aby otworzyć punktu końcowego, który umożliwia zdalny dostęp Oracle EM Express, należy utworzyć regułę sieciowej grupy zabezpieczeń z [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) w następujący sposób:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. W razie potrzeby uzyskania publicznego adresu IP maszyny Wirtualnej ponownie, podając [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show) w następujący sposób:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Połącz EM Express z przeglądarki. Upewnij się, że przeglądarka jest zgodny z Express EM (wymagana jest instalacja Flash): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Możesz zalogować się przy użyciu **SYS** konta i sprawdź **jako grupy sysdba** wyboru. Użyj hasła **OraPasswd1** skonfigurowane podczas instalacji. 

![Zrzut ekranu przedstawiający stronę logowania Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu eksploracji pierwszą bazę danych programu Oracle na platformie Azure i maszyny Wirtualnej nie jest już potrzebny, możesz użyć [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenie Usuń grupę zasobów maszyny Wirtualnej, i wszystkich powiązanych zasobów.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o innych [Oracle rozwiązania na platformie Azure](oracle-considerations.md). 

Spróbuj [Instalowanie i konfigurowanie programu Oracle automatycznego zarządzania magazynem](configure-oracle-asm.md) samouczka.