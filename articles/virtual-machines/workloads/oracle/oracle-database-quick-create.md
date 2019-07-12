---
title: Tworzenie bazy danych Oracle na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Szybkie rozpoczęcie bazy danych programu Oracle Database 12c w górę i w swoim środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 26c6abc75e653b489a7385c423a9b2d00a4ed063
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705306"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Tworzenie bazy danych Oracle na Maszynie wirtualnej platformy Azure

Ten przewodnik zawiera szczegółowe informacje przy użyciu wiersza polecenia platformy Azure, aby wdrożyć maszynę wirtualną platformy Azure z [obrazu z galerii marketplace Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) aby można było utworzyć bazy danych Oracle 12 c. Po wdrożeniu serwera łączą za pośrednictwem protokołu SSH w celu skonfigurowania bazy danych programu Oracle. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną (VM), użyj [tworzenie az vm](/cli/azure/vm) polecenia. 

W poniższym przykładzie utworzono maszynę wirtualną o nazwie `myVM`. Tworzy również klucze SSH, jeśli ich jeszcze nie istnieją w domyślnej lokalizacji kluczy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po utworzeniu maszyny Wirtualnej, wiersza polecenia platformy Azure wyświetli informacje podobne do poniższego przykładu. Zwróć uwagę na wartość dla `publicIpAddress`. Ten adres umożliwia dostęp do maszyny Wirtualnej.

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

Aby utworzyć sesję SSH z maszyną Wirtualną, należy użyć następującego polecenia. Zastąp adres IP za pomocą `publicIpAddress` wartości dla swojej maszyny Wirtualnej.

```bash 
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Tworzenie bazy danych

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

Przed nawiązaniem połączenia, należy ustawić dwie zmienne środowiskowe: *ORACLE_HOME* i *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Możesz również dodać zmienne ORACLE_HOME i ORACLE_SID pliku .bashrc. Spowoduje to zapisanie zmienne środowiskowe do przyszłych operacji logowania. Upewnij się, zostały dodane następujące instrukcje do `~/.bashrc` pliku za pomocą dowolnego edytora.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Łączność EM Express Oracle

Graficzny interfejs użytkownika narzędzia do zarządzania, który można użyć, aby zapoznać się z bazą danych, skonfiguruj Oracle EM Express. Nawiązać połączenia Oracle EM Express, należy najpierw skonfigurować port Oracle. 

1. Połącz z bazą danych przy użyciu sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Po nawiązaniu połączenia Ustaw port 5502 Express EM

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Otwórz kontener PDB1 Jeśli jeszcze nie jest otwarty, ale pierwsze sprawdzenie stanu:

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

4. Jeśli OPEN_MODE dla `PDB1` nie jest odczyt PISAĆ, następnie uruchom polecenia następujących tematach, aby otworzyć PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Należy wpisać `quit` sqlplus sesji i typ `exit` wylogować użytkownika bazy danych oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatyzowanie bazy danych, uruchamiania i zamykania

Baza danych programu Oracle domyślnie automatycznie nie zaczyna się po ponownym uruchomieniu maszyny Wirtualnej. Aby skonfigurować bazy danych programu Oracle do automatycznego uruchamiania, najpierw zaloguj się jako użytkownik główny. Następnie utwórz i zaktualizować niektórych plików systemowych.

1. Zaloguj się jako użytkownik główny
    ```bash
    sudo su -
    ```

2.  Za pomocą ulubionego edytora, Edytuj plik `/etc/oratab` i zmienić domyślną `N` do `Y`:

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

4.  Zmienianie uprawnień do plików za pomocą *chmod* w następujący sposób:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Tworzenie łączy symbolicznych do uruchamiania i zamykania w następujący sposób:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Aby przetestować zmiany, uruchom ponownie maszynę Wirtualną:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Otwieranie portów dla łączności

Ostatnim zadaniem jest skonfigurować niektóre zewnętrzne punkty końcowe. Aby skonfigurować sieciowej grupy zabezpieczeń platformy Azure chroni maszynę Wirtualną, najpierw zamknij sesję SSH na maszynie Wirtualnej (powinien mieć zostało uruchomione z protokołu SSH po ponownym uruchomieniu komputera w poprzednim kroku). 

1.  Aby otworzyć punkt końcowy, który umożliwia zdalny dostęp do bazy danych Oracle, Utwórz regułę sieciowej grupy zabezpieczeń za pomocą [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule) w następujący sposób: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Aby otworzyć punkt końcowy, który umożliwia zdalny dostęp Oracle EM Express, Utwórz regułę sieciowej grupy zabezpieczeń za pomocą [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule) w następujący sposób:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Jeśli to konieczne, Uzyskaj publiczny adres IP swojej maszyny wirtualnej ponownie, używając [az sieci public-ip show](/cli/azure/network/public-ip) w następujący sposób:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Połącz EM Express z poziomu przeglądarki. Upewnij się, że Twoja przeglądarka jest zgodna z platformą Express EM (wymagana jest instalacja Flash): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Możesz zalogować się przy użyciu **SYS** konta, a następnie sprawdź **jako grupy sysdba** pole wyboru. Użyj hasła **OraPasswd1** ustawioną podczas instalacji. 

![Zrzut ekranu przedstawiający stronę logowania Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu eksplorowania pierwszej bazy danych Oracle na platformie Azure i maszyna wirtualna nie jest już potrzebny, można użyć [usunięcie grupy az](/cli/azure/group) polecenia, aby usunąć grupę zasobów maszyny Wirtualnej i wszystkie pokrewne zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych [rozwiązania Oracle na platformie Azure](oracle-considerations.md). 

Spróbuj [Instalowanie i konfigurowanie programu Oracle automatyczne zarządzanie magazynem](configure-oracle-asm.md) samouczka.
