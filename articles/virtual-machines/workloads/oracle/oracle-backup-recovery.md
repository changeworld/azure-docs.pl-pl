---
title: Tworzenie kopii zapasowych i odzyskiwanie bazy danych Oracle database 12c na maszynie wirtualnej z systemem Linux platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć kopie zapasowe i odzyskiwanie bazy danych Oracle database 12c w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
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
ms.openlocfilehash: c41f13a6437f69121d3bbb387c96d8e13f2be0b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567083"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Tworzenie kopii zapasowych i odzyskiwanie bazy danych Oracle database 12c na maszynie wirtualnej z systemem Linux platformy Azure

Można użyć wiersza polecenia platformy Azure, aby utworzyć i zarządzać zasobami platformy Azure, w wierszu polecenia lub za pomocą skryptów. W tym artykule używamy skrypty wiersza polecenia platformy Azure do wdrożenia oprogramowania Oracle database 12c w obrazie galerii w portalu Azure Marketplace.

Przed przystąpieniem do wykonywania upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="step-1-prerequisites"></a>Krok 1: Wymagania wstępne

*   Aby przeprowadzić proces tworzenia kopii zapasowych i odzyskiwania, należy najpierw utworzyć maszyny Wirtualnej systemu Linux, który ma zainstalowane wystąpienie bazy danych Oracle Database 12c. Obraz witryny Marketplace umożliwia utworzenie maszyny Wirtualnej o nazwie *Oracle: Oracle — bazy danych — Ee:12.1.0.2:latest*.

    Aby dowiedzieć się, jak utworzyć bazę danych Oracle, zobacz [Oracle tworzenie database — Szybki Start](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2: Łączenie z maszyną wirtualną

*   Do utworzenia sesji protokołu Secure Shell (SSH) z maszyną Wirtualną, użyj następującego polecenia. Zastąp adres IP i kombinacja nazwy hosta, za pomocą `publicIpAddress` wartości dla swojej maszyny Wirtualnej.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3: Przygotowywanie bazy danych programu

1.  W tym kroku przyjęto założenie, iż z wystąpieniem bazy danych Oracle (cdb1), który jest uruchomiony na maszynie Wirtualnej o nazwie *myVM*.

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
3.  (Opcjonalnie) Utwórz tabelę do przetestowania zatwierdzenia:

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
5. Aby utworzyć kopię zapasową bazy danych, należy użyć Menedżera odzyskiwania bazy danych Oracle (RMAN):

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4: Spójna na poziomie aplikacji Kopia zapasowa maszyn wirtualnych systemu Linux

Kopie zapasowe spójnych z aplikacją to nowa funkcja w usłudze Azure Backup. Można tworzyć i zaznaczyć skrypt do wykonania przed i po wykonaniu migawki maszyny Wirtualnej (przed i po utworzeniu migawki).

1. Pobierz plik JSON.

    Download VMSnapshotScriptPluginConfig.json from https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Zawartość pliku wyglądać podobnie do następującego:

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

2. Utwórz folder etc na maszynie Wirtualnej:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Skopiuj plik JSON.

    Skopiuj VMSnapshotScriptPluginConfig.json do folderu, etc.

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

5. Utwórz pliki skryptów przed i po utworzeniu migawki.

    Oto przykład skrypty przed i po utworzeniu migawki dla "zimnych kopia zapasowa" (kopie zapasowe offline, zamknięcie i ponowne uruchomienie):

    Aby uzyskać /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Aby uzyskać /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Oto przykład skrypty przed i po utworzeniu migawki dla "hot kopia zapasowa" (kopii zapasowej online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Aby uzyskać /etc/azure/post_script.sh:

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

7. Przetestuj skrypty.

    Aby przetestować skrypty, najpierw zaloguj się jako użytkownik główny. Następnie upewnij się, że nie ma żadnych błędów:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Aby uzyskać więcej informacji, zobacz [spójnych z aplikacją kopii zapasowych maszyn wirtualnych systemu Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5. Magazyny usług odzyskiwania Azure Użyj do tworzenia kopii zapasowej maszyny Wirtualnej

1.  W witrynie Azure portal Wyszukaj **Magazyny usługi Recovery Services**.

    ![Stronie Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Na **Magazyny usługi Recovery Services** bloku, aby dodać nowego magazynu, kliknij przycisk **Dodaj**.

    ![Strona dodawania Magazyny usługi Recovery Services](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Aby kontynuować, kliknij przycisk **myVault**.

    ![Strony szczegółów Magazyny usługi Recovery Services](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na **myVault** bloku kliknij **kopii zapasowej**.

    ![Magazyny usługi Recovery Services stronie kopii zapasowej](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Na **cel kopii zapasowej** bloku, użyj wartości domyślnej **Azure** i **maszyny wirtualnej**. Kliknij przycisk **OK**.

    ![Strony szczegółów Magazyny usługi Recovery Services](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Dla **zasady tworzenia kopii zapasowej**, użyj **DefaultPolicy**, lub wybierz **utworzyć nowe zasady**. Kliknij przycisk **OK**.

    ![Strona szczegółów zasad tworzenia kopii zapasowych magazynów usługi Recovery Services](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Na **wybierz maszyny wirtualne** bloku wybierz **myVM1** pole wyboru, a następnie kliknij przycisk **OK**. Kliknij przycisk **Włącz kopię zapasową** przycisku.

    ![Elementy Magazyny usług odzyskiwania do strony szczegółów kopii zapasowej](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po kliknięciu **Włącz kopię zapasową**, proces tworzenia kopii zapasowej nie zaczyna się do momentu wygaśnięcia ważności w zaplanowanym czasie. Aby skonfigurować Natychmiastowa kopia zapasowa, należy wykonać następny krok.

8.  Na **myVault — elementy kopii zapasowej** bloku, w obszarze **liczba elementów kopii zapasowej**, wybierz liczba elementów kopii zapasowej.

    ![Strona szczegółów myVault Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Na **elementy kopii zapasowej (maszynie wirtualnej platformy Azure)** bloku w prawej części strony, kliknij przycisk wielokropka (**...** ) przycisk, a następnie kliknij przycisk **Utwórz teraz kopię zapasową**.

    ![Kopia zapasowa teraz polecenia Magazyny usługi Recovery Services](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kliknij przycisk **kopii zapasowej** przycisku. Poczekaj, aż do zakończenia procesu tworzenia kopii zapasowej. Następnie przejdź do [krok 6: Usuń pliki bazy danych](#step-6-remove-the-database-files).

    Aby wyświetlić stan zadania tworzenia kopii zapasowej, kliknij przycisk **zadań**.

    ![Magazyny usługi Recovery Services zadania strony](./media/oracle-backup-recovery/recovery_service_10.png)

    Stan zadania tworzenia kopii zapasowej pojawia się na poniższej ilustracji:

    ![Magazyny usługi Recovery Services zadania strona ze stanem](./media/oracle-backup-recovery/recovery_service_11.png)

11. Do tworzenia kopii zapasowych spójnych z aplikacją rozwiązać wszelkie błędy w pliku dziennika. Plik dziennika znajduje się w /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6: Usuń pliki bazy danych 
W dalszej części tego artykułu dowiesz się, jak przetestować proces odzyskiwania. Przed przetestowaniem proces odzyskiwania, należy usunąć pliki bazy danych.

1.  Usuń pliki tabel i wykonywania kopii zapasowych:

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

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Przywracanie usuniętych plików z magazynów usługi Recovery Services
Aby przywrócić usunięte pliki, wykonaj następujące czynności:

1. W witrynie Azure portal Wyszukaj *myVault* elementu Magazyny usługi Recovery Services. Na **Przegląd** bloku, w obszarze **kopii zapasowych elementów**, wybierz liczbę elementów.

    ![Elementy kopii zapasowej myVault Magazyny usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_12.png)

2. W obszarze **liczba elementów kopii zapasowej**, wybierz liczbę elementów.

    ![Magazyny usługi Recovery Services liczba elementów kopii zapasowej maszyny wirtualnej platformy Azure](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na **myvm1** bloku kliknij **odzyskiwanie plików (wersja zapoznawcza)**.

    ![Zrzut ekranu przedstawiający usługi Recovery Services magazyny strona odzyskiwania plików](./media/oracle-backup-recovery/recovery_service_14.png)

4. Na **odzyskiwanie plików (wersja zapoznawcza)** okienku kliknij **Pobierz skrypt**. Następnie zapisz plik pobierania (SH) do folderu na komputerze klienckim.

    ![Pobierz opcje zapisywania pliku skryptu](./media/oracle-backup-recovery/recovery_service_15.png)

5. Skopiuj plik SH do maszyny Wirtualnej.

    Poniższy przykład pokazuje, jak przy użyciu bezpiecznego kopiowania (scp) polecenia, aby przenieść plik na maszynie wirtualnej. Możesz również skopiować zawartość do Schowka, a następnie wklej zawartość do nowego pliku, który jest ustawiony na maszynie Wirtualnej.

    > [!IMPORTANT]
    > W poniższym przykładzie upewnij się zaktualizowanie wartości IP adres i folderu. Wartość musi być mapowane do folderu, w której zapisany plik.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Tak, że jest własnością katalogu głównego, należy zmienić ten plik.

    W poniższym przykładzie należy zmienić plik tak, że jest własnością katalogu głównego. Następnie należy zmienić uprawnienia.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    Poniższy przykład pokazuje, co powinien zostać wyświetlony po uruchomieniu skryptu poprzedzającego. Po wyświetleniu monitu, aby kontynuować, wprowadź **Y**.

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

    Aby zakończyć pracę, wprowadź **q**, a następnie wyszukaj zainstalowanych woluminów. Aby utworzyć listę woluminów dodane, w wierszu polecenia, wpisz **df -k**.

    ![Polecenie -k df](./media/oracle-backup-recovery/recovery_service_16.png)

8. Użyj następującego skryptu, aby skopiować brakujące pliki ponownie do folderów:

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
    
10. Odinstaluj dysk.

    W witrynie Azure portal na **odzyskiwanie plików (wersja zapoznawcza)** bloku kliknij **odinstaluj dyski**.

    ![Odinstaluj dyski polecenia](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Przywróć całą maszynę Wirtualną

Zamiast przywracanie usuniętych plików z magazynów usługi Recovery Services, można przywrócić całą maszynę Wirtualną.

### <a name="step-1-delete-myvm"></a>Krok 1: Usuń myVM

*   W witrynie Azure portal przejdź do **myVM1** magazynu, a następnie wybierz **Usuń**.

    ![Polecenie Usuń magazyn](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2: Odzyskiwanie maszyny Wirtualnej

1.  Przejdź do **Magazyny usługi Recovery Services**, a następnie wybierz pozycję **myVault**.

    ![wpis myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na **Przegląd** bloku, w obszarze **kopii zapasowych elementów**, wybierz liczbę elementów.

    ![myVault wykonywanie kopii zapasowych elementów](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Na **elementy kopii zapasowej (maszynie wirtualnej platformy Azure)** bloku wybierz **myvm1**.

    ![Strona maszyny Wirtualnej odzyskiwania](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na **myvm1** bloku, kliknij przycisk wielokropka (**...** ) przycisk, a następnie kliknij przycisk **przywrócić maszynę Wirtualną**.

    ![Przywracanie maszyny Wirtualnej, polecenie](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na **wybierz punkt przywracania** bloku wybierz element, który chcesz przywrócić, a następnie kliknij przycisk **OK**.

    ![Wybierz punkt przywracania](./media/oracle-backup-recovery/recover_vm_06.png)

    Po włączeniu kopii zapasowej spójnej na poziomie aplikacji, pojawi się pionowy pasek niebieski.

6.  Na **przywracania konfiguracji** bloku, wybierz nazwę maszyny wirtualnej, wybierz grupę zasobów, a następnie kliknij przycisk **OK**.

    ![Przywróć wartości konfiguracji](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Aby przywrócić maszynę Wirtualną, kliknij przycisk **przywrócić** przycisku.

8.  Aby wyświetlić stan procesu przywracania, kliknij przycisk **zadania**, a następnie kliknij przycisk **zadania tworzenia kopii zapasowej**.

    ![Polecenie status zadania tworzenia kopii zapasowej](./media/oracle-backup-recovery/recover_vm_08.png)

    Na poniższej ilustracji przedstawiono stan procesu przywracania:

    ![Stan procesu przywracania](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3: Ustaw publiczny adres IP
Po przywróceniu maszyny Wirtualnej należy skonfigurować publiczny adres IP.

1.  W polu wyszukiwania wprowadź **publiczny adres IP**.

    ![Listę publicznych adresów IP](./media/oracle-backup-recovery/create_ip_00.png)

2.  Na **publiczne adresy IP** bloku kliknij **Dodaj**. Na **tworzenie publicznego adresu IP** bloku dla **nazwa**, wybierz nazwę publicznego adresu IP. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**. Następnie kliknij pozycję **Utwórz**.

    ![Utwórz adres IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Aby skojarzyć publiczny adres IP z interfejsem sieciowym maszyny wirtualnej, wyszukiwanie i wybieranie **myVMip**. Następnie kliknij przycisk **skojarzyć**.

    ![Skojarzenie adresu IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Aby uzyskać **typ zasobu**, wybierz opcję **interfejs sieciowy**. Wybierz interfejs sieciowy, który jest używany przez wystąpienie myVM, a następnie kliknij przycisk **OK**.

    ![Wybierz typ zasobu i wartości karty Sieciowej](./media/oracle-backup-recovery/create_ip_03.png)

5.  Wyszukaj, a następnie otwórz wystąpienie myVM, które są przenoszone z poziomu portalu. Adres IP, który jest skojarzony z maszyną Wirtualną jest wyświetlana na myVM **Przegląd** bloku.

    ![Wartość adresu IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4: Łączenie z maszyną wirtualną

*   Aby połączyć się z maszyną wirtualną, użyj następującego skryptu:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5. Sprawdź, czy baza danych jest niedostępna
*   Aby przetestować ułatwień dostępu, użyj następującego skryptu:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Jeśli baza danych **uruchamiania** polecenie generuje błąd, aby odzyskać bazę danych, zobacz [krok 6: Użyj RMAN, aby odzyskać bazę danych](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6: (Opcjonalnie) Użyj RMAN, aby odzyskać bazę danych
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

Kopia zapasowa i odzyskiwanie bazy danych programu Oracle Database 12c na maszynie Wirtualnej systemu Linux platformy Azure jest teraz ukończona.

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Gdy maszyna wirtualna nie są już potrzebne, można użyć następującego polecenia można usunąć grupy zasobów, maszyna wirtualna i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

[Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)

[Poznaj przykłady interfejsu wiersza polecenia Azure wdrażania maszyn wirtualnych](../../linux/cli-samples.md)



