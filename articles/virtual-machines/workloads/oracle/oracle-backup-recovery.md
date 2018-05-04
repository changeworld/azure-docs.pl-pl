---
title: Tworzenie kopii zapasowej i odzyskiwanie bazy danych 12c baz danych programu Oracle na maszynie wirtualnej platformy Azure w systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać kopię zapasową i przywrócić bazę danych bazy danych programu Oracle 12c w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 5/17/2017
ms.author: rclaus
ms.openlocfilehash: e804fd17c3dbe9df9e9dc258e67b8f9192c1f8ad
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Tworzenie kopii zapasowej i odzyskiwanie bazy danych 12c baz danych programu Oracle na maszynie wirtualnej platformy Azure w systemie Linux

Interfejsu wiersza polecenia Azure służy do tworzenia i zarządzania zasobami Azure, w wierszu polecenia lub za pomocą skryptów. W tym artykule używamy skryptów wiersza polecenia platformy Azure do wdrażania bazy danych programu Oracle Database 12c z obrazu w galerii Azure Marketplace.

Przed rozpoczęciem upewnij się, czy zainstalowano wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="step-1-prerequisites"></a>Krok 1: wymagania wstępne

*   Aby wykonać proces tworzenia kopii zapasowych i odzyskiwania, należy najpierw utworzyć maszyny Wirtualnej systemu Linux, który ma zainstalowane wystąpienie bazy danych Oracle 12c. Obrazu witryny Marketplace, używany do utworzenia maszyny Wirtualnej o nazwie *Oracle: Oracle — bazy danych — Ee:12.1.0.2:latest*.

    Aby dowiedzieć się, jak utworzyć bazę danych programu Oracle, zobacz [Szybki Start bazy danych utwórz Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2: Połączenie z maszyną Wirtualną

*   Do utworzenia sesji protokołu Secure Shell (SSH) z maszyny Wirtualnej, użyj następującego polecenia. Zastąp adres IP i nazwy hosta z `publicIpAddress` wartość dla maszyny Wirtualnej.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3: Przygotowanie bazy danych

1.  Tego kroku przyjęto założenie, że masz wystąpienie Oracle (cdb1), która działa na maszynie Wirtualnej o nazwie *myVM*.

    Uruchom *oracle* głównego administratora, a następnie zainicjować odbiornika:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
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

2.  (Opcjonalnie) Upewnij się, że baza danych jest w trybie dziennika archiwum:

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  (Opcjonalnie) Utwórz tabelę, aby przetestować zatwierdzenia:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Sprawdź lub zmień lokalizację pliku kopii zapasowej i rozmiar:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Aby utworzyć kopię zapasową bazy danych, należy użyć Menedżera odzyskiwania Oracle (RMAN):

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4: Spójnych z aplikacją kopii zapasowej dla maszyn wirtualnych systemu Linux

Kopie zapasowe spójnych z aplikacją jest nową funkcją w programie Kopia zapasowa Azure. Można tworzyć i wybierz skryptów do uruchomienia przed i po wykonaniu migawki maszyny Wirtualnej (migawka przed i po utworzeniu migawki).

1. Pobierz plik JSON.

    Pobierz VMSnapshotScriptPluginConfig.json z https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Zawartość pliku wyglądać podobnie do następującego:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Utwórz /etc/azure folder na maszynie Wirtualnej:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Skopiuj plik JSON.

    Skopiuj VMSnapshotScriptPluginConfig.json do folderu /etc/azure.

4. Edytuj plik JSON.

    Edytuj plik VMSnapshotScriptPluginConfig.json, aby uwzględnić `PreScriptLocation` i `PostScriptlocation` parametrów. Na przykład:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Tworzenie plików skryptów migawki przed i po utworzeniu migawki.

    Oto przykład migawki przed i po utworzeniu migawki skryptów dla "kopii zapasowej zimnych" (kopię zapasową offline, zamknięcie i ponowne uruchomienie):

    Dla /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Dla /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Oto przykład migawki przed i po utworzeniu migawki skryptów dla "kopii zapasowej gorących" (kopii zapasowej online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Dla /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    W przypadku /etc/azure/pre_script.sql zmodyfikuj zawartość pliku, zgodnie z wymaganiami:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    W przypadku /etc/azure/post_script.sql zmodyfikuj zawartość pliku, zgodnie z wymaganiami:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Zmień uprawnienia do pliku:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Testowanie skryptów.

    Aby przetestować skrypty, najpierw zaloguj się jako katalogu głównego. Następnie upewnij się, że nie ma żadnych błędów:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Aby uzyskać więcej informacji, zobacz [spójnych z aplikacją kopii zapasowej dla maszyn wirtualnych systemu Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5: Magazyny usług odzyskiwania Azure używany do tworzenia kopii zapasowych maszyny Wirtualnej

1.  W portalu Azure, wyszukaj **Magazyny usług odzyskiwania**.

    ![Strona Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Na **Magazyny usług odzyskiwania** bloku, aby dodać nowy magazyn, kliknij przycisk **Dodaj**.

    ![Strona Dodawanie Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Aby kontynuować, kliknij przycisk **myVault**.

    ![Strony szczegółów Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na **myVault** bloku, kliknij przycisk **kopii zapasowej**.

    ![Magazyny usług odzyskiwania kopii zapasowej strony](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Na **cel kopii zapasowej** bloku, użyj wartości domyślnej **Azure** i **maszyny wirtualnej**. Kliknij przycisk **OK**.

    ![Strony szczegółów Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Dla **kopii zapasowej zasad**, użyj **DefaultPolicy**, lub wybierz **utworzyć nowe zasady**. Kliknij przycisk **OK**.

    ![Strona szczegółów zasady kopii zapasowej Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Na **wybierz maszyny wirtualne** bloku, wybierz **myVM1** pole wyboru, a następnie kliknij przycisk **OK**. Kliknij przycisk **kopii zapasowej Włącz** przycisku.

    ![Elementy Magazyny usług odzyskiwania do strony szczegółów tworzenia kopii zapasowej](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po kliknięciu **kopii zapasowej Włącz**, nie możesz uruchomić proces tworzenia kopii zapasowej do momentu wygaśnięcia zaplanowanego czasu. Aby skonfigurować natychmiastowej kopii zapasowej, wykonania następnego kroku.

8.  Na **myVault - kopii zapasowej elementów** bloku, w obszarze **kopii zapasowej liczba elementów**, wybierz liczba elementów kopii zapasowych.

    ![Strona szczegółów myVault Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Na **elementy kopii zapasowej (maszynie wirtualnej platformy Azure)** bloku w prawej części strony, kliknij przycisk wielokropka (**...** ) przycisk, a następnie kliknij przycisk **wykonaj kopię zapasową teraz**.

    ![Magazyny usług odzyskiwania kopii zapasowej teraz poleceń](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kliknij przycisk **kopii zapasowej** przycisku. Poczekaj na zakończenie procesu tworzenia kopii zapasowej. Następnie przejdź do [krok 6: Usuń pliki bazy danych](#step-6-remove-the-database-files).

    Aby wyświetlić stan zadania tworzenia kopii zapasowej, kliknij przycisk **zadania**.

    ![Magazyny usług odzyskiwania zadania strony](./media/oracle-backup-recovery/recovery_service_10.png)

    Stan zadania tworzenia kopii zapasowej zostanie wyświetlony na poniższej ilustracji:

    ![Strona o stanie zadania Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_11.png)

11. W celu tworzenia kopii zapasowych spójnych z aplikacją adresów błędy w pliku dziennika. Plik dziennika znajduje się w /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6: Usuń pliki bazy danych 
W dalszej części tego artykułu dowiesz się, jak przetestować proces odzyskiwania. Zanim można przetestować proces odzyskiwania, należy usunąć pliki bazy danych.

1.  Usuwanie plików obszaru tabel i kopii zapasowej:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Opcjonalnie) Zamknij wystąpienie programu Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Przywrócenie plików usuniętych z Magazyny usług odzyskiwania
Aby przywrócić usunięte pliki, wykonaj następujące kroki:

1. W portalu Azure, wyszukaj *myVault* elementu Magazyny usług odzyskiwania. Na **omówienie** bloku, w obszarze **kopii zapasowej elementów**, wybierz liczbę elementów.

    ![Elementy kopii zapasowej myVault Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_12.png)

2. W obszarze **liczba elementów kopii zapasowej**, wybierz liczbę elementów.

    ![Liczba elementów kopii zapasowych maszyny wirtualnej Azure Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na **myvm1** bloku, kliknij przycisk **odzyskiwanie plików (wersja zapoznawcza)**.

    ![Zrzut ekranu przedstawiający usług odzyskiwania magazyny strony odzyskiwania plików](./media/oracle-backup-recovery/recovery_service_14.png)

4. Na **odzyskiwanie plików (wersja zapoznawcza)** okienku, kliknij przycisk **Pobierz skrypt**. Następnie zapisz plik pobierania (SH) do folderu na komputerze klienckim.

    ![Pobierz opcje zapisuje plik skryptu](./media/oracle-backup-recovery/recovery_service_15.png)

5. Skopiuj plik SH do maszyny Wirtualnej.

    W poniższym przykładzie pokazano, jak polecenia należy używać kopii bezpiecznego (scp) można przenieść pliku z maszyną wirtualną. Możesz również skopiować zawartość do Schowka, a następnie wklej zawartość w nowym pliku, który został skonfigurowany na maszynie Wirtualnej.

    > [!IMPORTANT]
    > W poniższym przykładzie upewnij się, zaktualizowanie wartości IP adres i folderu. Wartości musi być zamapowany na folder, w którym zapisany plik.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Zmienić ten plik, dzięki czemu jest własnością głównego.

    W poniższym przykładzie należy zmienić plik, aby jego właścicielem jest katalogu głównego. Następnie należy zmienić uprawnienia.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    W poniższym przykładzie pokazano, co widzą po uruchomieniu skryptu poprzedzającego. Po wyświetleniu monitu, aby kontynuować, wprowadź **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. Dostęp do woluminów zainstalowanych został potwierdzony.

    Aby zakończyć pracę, wprowadź **q**, a następnie wyszukaj zainstalowanych woluminów. Aby utworzyć listę woluminów dodany, w wierszu polecenia, wpisz **df -k**.

    ![Polecenie -k df](./media/oracle-backup-recovery/recovery_service_16.png)

8. Aby skopiować brakujące pliki do folderów, użyj następującego skryptu:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. W poniższym skrypcie Użyj RMAN, aby odzyskać bazę danych:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Usuń dysk.

    W portalu Azure na **odzyskiwanie plików (wersja zapoznawcza)** bloku, kliknij przycisk **odinstalować dyski**.

    ![Odinstaluj polecenia dyski](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Przywróć całą maszynę Wirtualną

Zamiast przywracania usuniętych plików z Magazyny usług odzyskiwania, można przywrócić całą maszynę Wirtualną.

### <a name="step-1-delete-myvm"></a>Krok 1: Usuń myVM

*   W portalu Azure, przejdź do **myVM1** magazynu, a następnie wybierz **usunąć**.

    ![Polecenie Usuń magazyn](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2: Odzyskiwanie maszyny Wirtualnej

1.  Przejdź do **Magazyny usług odzyskiwania**, a następnie wybierz **myVault**.

    ![wpis myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na **omówienie** bloku, w obszarze **kopii zapasowej elementów**, wybierz liczbę elementów.

    ![myVault kopii zapasowej elementów](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Na **elementy kopii zapasowej (maszynie wirtualnej platformy Azure)** bloku, wybierz opcję **myvm1**.

    ![Strony maszyny Wirtualnej odzyskiwania](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na **myvm1** bloku, kliknij przycisk wielokropka (**...** ) przycisk, a następnie kliknij przycisk **przywracanie maszyny Wirtualnej**.

    ![Maszyna wirtualna polecenia Restore](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na **wybierz punkt przywracania** bloku i wybierz element, który chcesz przywrócić, a następnie kliknij przycisk **OK**.

    ![Wybierz punkt przywracania](./media/oracle-backup-recovery/recover_vm_06.png)

    Jeśli włączono spójnych z aplikacją kopii zapasowej, zostanie wyświetlony pasek pionowy niebieski.

6.  Na **przywracania** bloku, wybierz nazwę maszyny wirtualnej, wybierz grupę zasobów, a następnie kliknij przycisk **OK**.

    ![Przywróć wartości konfiguracji](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Aby przywrócić maszynę Wirtualną, kliknij przycisk **przywrócić** przycisku.

8.  Aby wyświetlić stan procesu przywracania, kliknij przycisk **zadania**, a następnie kliknij przycisk **zadania tworzenia kopii zapasowej**.

    ![Polecenie stan zadania tworzenia kopii zapasowej](./media/oracle-backup-recovery/recover_vm_08.png)

    Poniższa ilustracja przedstawia stan procesu przywracania:

    ![Stan procesu przywracania](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3: Ustawianie publicznego adresu IP
Po przywróceniu maszyny Wirtualnej, należy skonfigurować publicznego adresu IP.

1.  W polu wyszukiwania wprowadź **publicznego adresu IP**.

    ![Lista publicznych adresów IP](./media/oracle-backup-recovery/create_ip_00.png)

2.  Na **publicznego adresu IP, adresy** bloku, kliknij przycisk **Dodaj**. Na **tworzenie publicznego adresu IP** bloku dla **nazwa**, wybierz nazwa publicznego adresu IP. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**. Następnie kliknij pozycję **Utwórz**.

    ![Utwórz adres IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Aby skojarzyć publiczny adres IP z interfejsu sieciowego dla maszyny Wirtualnej, wyszukaj i wybierz **myVMip**. Następnie kliknij przycisk **skojarzyć**.

    ![Powiąż adres IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Aby uzyskać **typu zasobu**, wybierz pozycję **interfejsu sieciowego**. Wybierz interfejs sieciowy, który jest używany przez wystąpienie myVM, a następnie kliknij przycisk **OK**.

    ![Wybierz typ zasobu i wartości karty Sieciowej](./media/oracle-backup-recovery/create_ip_03.png)

5.  Wyszukaj i otwórz wystąpienie myVM, który jest przenoszone z portalu. Adres IP, który jest skojarzony z maszyną Wirtualną jest wyświetlany na myVM **omówienie** bloku.

    ![Wartość adresu IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4: Połączenie z maszyną Wirtualną

*   Aby nawiązać połączenie z maszyną Wirtualną, użyj następującego skryptu:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5: Testowanie, czy baza danych jest dostępna
*   Aby przetestować ułatwień dostępu, użyj następującego skryptu:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Jeśli bazy danych **uruchamiania** polecenie generuje błąd, aby odzyskać bazę danych, zobacz [krok 6: Użyj RMAN przywrócić bazę danych](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6. (Opcjonalnie) Użyj RMAN przywrócić bazę danych
*   Aby odzyskać bazę danych, użyj następującego skryptu:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Kopii zapasowych i odzyskiwania bazy danych 12c baz danych programu Oracle na maszynie Wirtualnej platformy Azure Linux jest zakończone.

## <a name="delete-the-vm"></a>Usuń maszynę Wirtualną

Gdy maszyna wirtualna nie jest już potrzebny, służy polecenie Usuń grupę zasobów, maszyny Wirtualnej i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

[Samouczek: Tworzenie maszyn wirtualnych wysokiej dostępności](../../linux/create-cli-complete.md)

[Eksploruj przykłady Azure CLI wdrożenia maszyny Wirtualnej](../../linux/cli-samples.md)



