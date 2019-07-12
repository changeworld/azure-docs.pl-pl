---
title: Implementowanie bramy Golden Oracle na maszynie Wirtualnej systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Szybkie rozpoczęcie bramy Golden Oracle w górę i w swoim środowisku platformy Azure.
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
ms.openlocfilehash: 39d1b8b860fd19261bd39c345d464dd37b48d871
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707553"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementowanie bramy Golden Oracle na maszynie Wirtualnej systemu Linux platformy Azure 

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik zawiera informacje dotyczące używania wiersza polecenia platformy Azure do wdrożenia bazy danych Oracle 12c w portalu Azure Marketplace obrazu z galerii. 

W tym dokumencie opisano krok po kroku do tworzenia, instalowanie i konfigurowanie bramy Golden Oracle na Maszynie wirtualnej platformy Azure.

Przed rozpoczęciem upewnij się, że interfejs wiersza polecenia platformy Azure został zainstalowany. Aby uzyskać więcej informacji, zobacz [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Przewodnik instalacji interfejsu wiersza polecenia platformy Azure).

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

Aby przeprowadzić instalację bramy Golden Oracle, musisz utworzyć dwie maszyny wirtualne platformy Azure na tym samym zestawie dostępności. Obraz witryny Marketplace umożliwia tworzenie maszyn wirtualnych jest **Oracle: Oracle — bazy danych — Ee:12.1.0.2:latest**.

Musisz także znać vi edytora Unix oraz mieć podstawową wiedzę na temat x11 (Windows X).

Poniżej przedstawiono podsumowanie konfiguracji środowiska:
> 
> |  | **Lokacja główna** | **Replikacja lokacji** |
> | --- | --- | --- |
> | **Wersja bazy danych Oracle** |Oracle 12c w wersji 2 – (12.1.0.2) |Oracle 12c w wersji 2 – (12.1.0.2)|
> | **Nazwa maszyny** |myVM1 |myVM2 |
> | **System operacyjny** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Schemat replikacji** |TEST|TEST |
> | **Brama Golden właściciela/replikacja** |C##GGADMIN |REPUSER |
> | **Proces Golden bramy** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do subskrypcji platformy Azure za pomocą [az login](/cli/azure/reference-index) polecenia. Następnie postępuj zgodnie z wyświetlanymi instrukcjami.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupę zasobów platformy Azure to logiczny kontener przeznaczony do określonych zasobów platformy Azure są wdrażane i z którego mogą być zarządzane. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Następny krok jest opcjonalny, ale zalecany. Aby uzyskać więcej informacji, zobacz [przewodnik zestawy dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Poniższy przykład tworzy dwie maszyny wirtualne o nazwach `myVM1` i `myVM2`. Tworzenie kluczy SSH, jeśli ich jeszcze nie istnieją w domyślnej lokalizacji kluczy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Utwórz myVM1 (podstawowy):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Po utworzeniu maszyny Wirtualnej platformy AZURE wyświetli informacje podobne do poniższego przykładu. (Zwróć uwagę na `publicIpAddress`. Ten adres umożliwia dostęp do maszyny Wirtualnej.)

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>Utwórz myVM2 (replikacja):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Zwróć uwagę na `publicIpAddress` także, po jego utworzeniu.

### <a name="open-the-tcp-port-for-connectivity"></a>Otwórz port TCP dla łączności

Następnym krokiem jest skonfigurowanie zewnętrzne punkty końcowe, które umożliwiają zdalny dostęp do bazy danych programu Oracle. Aby skonfigurować zewnętrzne punkty końcowe, uruchom następujące polecenia.

#### <a name="open-the-port-for-myvm1"></a>Otwórz port myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Wyniki powinny wyglądać podobnie do następującej:

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Otwórz port myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Zastąp adres IP adresem `publicIpAddress` swojej maszyny wirtualnej.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Tworzenie bazy danych na myVM1 (podstawowy)

Oprogramowanie Oracle jest już zainstalowana na obrazu portalu Marketplace, następnym krokiem jest zainstalowanie bazy danych. 

Uruchamianie oprogramowania jako administratora "oracle":

```bash
sudo su - oracle
```

Tworzenie bazy danych:

```bash
$ dbca -silent \
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
Dane wyjściowe powinien wyglądać podobnie do następującej:

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Ustaw zmienne ORACLE_SID i ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcjonalnie można dodać ORACLE_HOME i ORACLE_SID do pliku .bashrc tak, aby te ustawienia są zapisywane do przyszłego logowania:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Uruchomić odbiornik programu Oracle
```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Tworzenie bazy danych na myVM2 (replikacja)

```bash
sudo su - oracle
```
Tworzenie bazy danych:

```bash
$ dbca -silent \
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
Ustaw zmienne ORACLE_SID i ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcjonalnie możesz wybrać dodanych ORACLE_HOME i ORACLE_SID do pliku .bashrc tak, aby te ustawienia są zapisywane do przyszłego logowania.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Uruchomić odbiornik programu Oracle
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Konfigurowanie bramy Golden 
Aby skonfigurować bramę Golden, wykonaj kroki w tej sekcji.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Włącz tryb dziennika archiwum na myVM1 (podstawowy)

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
```
Włączanie rejestrowania życie i upewnij się, że co najmniej jeden plik dziennika znajduje się.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Pobierz oprogramowanie Golden bramy
Aby pobrać i przygotować oprogramowanie Oracle Golden bramy, wykonaj następujące czynności:

1. Pobierz **fbo_ggs_Linux_x64_shiphome.zip** plik wchodzącej w skład [stronę pobierania bramy Golden Oracle](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Pod tytułem pobierania **12.x.x.x środowiska Oracle GoldenGate dla Oracle Linux x86-64**, powinien istnieć zbiór plików zip do pobrania.

2. Po pobraniu plików ZIP na komputer kliencki do kopiowania plików do maszyny Wirtualnej należy użyć protokołu Secure Copy (SCP):

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Przenieś pliki zip do **/ opt** folderu. Następnie Zmień właściciela pliki w następujący sposób:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Rozpakuj pliki (instalowanie Linux Rozpakuj narzędzie, jeśli jeszcze nie zainstalowano):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Zmiana uprawnień:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Przygotuj klienta i maszyn wirtualnych do uruchamiania x11 (dotyczy tylko klientów Windows)
Jest to krok opcjonalny. Możesz pominąć ten krok, jeśli jest używany klient systemu Linux lub jeszcze x11 Instalatora.

1. Pobierz program PuTTY i Xming na komputer Windows:

   * [Pobierz program PuTTY](https://www.putty.org/)
   * [Pobierz Xming](https://xming.en.softonic.com/)

2. Po zainstalowaniu programu PuTTY, w folderze programu PuTTY (na przykład C:\Program Files\PuTTY), należy uruchomić puttygen.exe (Generator kluczy PuTTY).

3. W programu PuTTY generatora klucza:

   - Aby wygenerować klucz, zaznacz **Generuj** przycisku.
   - Skopiuj zawartość klucza (**klawisze Ctrl + C**).
   - Wybierz **Zapisz klucz prywatny** przycisku.
   - Zignorować to ostrzeżenie zostanie wyświetlone, a następnie wybierz pozycję **OK**.

   ![Zrzut ekranu przedstawiający stronę generator kluczy PuTTY](./media/oracle-golden-gate/puttykeygen.png)

4. Na maszynie wirtualnej uruchom następujące polecenia:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Utwórz plik o nazwie **authorized_keys**. Wklej zawartość klucza w tym pliku, a następnie zapisz plik.

   > [!NOTE]
   > Klucz musi zawierać ciąg `ssh-rsa`. Ponadto zawartość klucza musi być pojedynczy wiersz tekstu.
   >  

6. Uruchom program PuTTY. W **kategorii** okienku wybierz **połączenia** > **SSH** > **uwierzytelniania**. W **pliku klucza prywatnego na potrzeby uwierzytelniania** przejdź do klucza który został wcześniej wygenerowany.

   ![Zrzut ekranu przedstawiający stronę ustawiony klucz prywatny](./media/oracle-golden-gate/setprivatekey.png)

7. W **kategorii** okienku wybierz **połączenia** > **SSH** > **X11**. Następnie wybierz pozycję **przekazywania Włącz X11** pole.

   ![Zrzut ekranu przedstawiający stronę Włącz X11](./media/oracle-golden-gate/enablex11.png)

8. W **kategorii** okienko, przejdź do **sesji**. Wprowadź informacje dotyczące hosta, a następnie wybierz **Otwórz**.

   ![Zrzut ekranu przedstawiający stronę sesji](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Zainstaluj oprogramowanie Golden bramy

Aby zainstalować bramę Golden Oracle, wykonaj następujące czynności:

1. Zaloguj się jako bazy danych oracle. (Należy mogli logować się bez monitowania o podanie hasła.) Upewnij się, że Xming jest uruchomiony, przed rozpoczęciem instalacji.
 
   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```
2. Wybierz pozycję "Środowiska Oracle GoldenGate dla bazy danych Oracle Database 12c". Następnie wybierz pozycję **dalej** aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Wybieranie instalacji Instalatora](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Zmień lokalizację na oprogramowanie. Następnie wybierz pozycję **Uruchom Menedżera** polu, a następnie podaj lokalizację bazy danych. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Wybieranie instalacji](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Zmień katalog magazynu, a następnie wybierz **dalej** aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Wybieranie instalacji](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Na **Podsumowanie** ekranu, wybierz opcję **zainstalować** aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Wybieranie instalacji Instalatora](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Może być wyświetlony monit o uruchomienie skryptu jako "root". Jeśli tak, otwórz oddzielną sesję ssh z maszyną wirtualną, "sudo" do katalogu głównego, a następnie uruchom skrypt. Wybierz **OK** kontynuować.

   ![Zrzut ekranu przedstawiający stronę Wybieranie instalacji](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Po zakończeniu instalacji wybierz **Zamknij** aby ukończyć proces.

   ![Zrzut ekranu przedstawiający stronę Wybieranie instalacji](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Konfigurowanie usługi na myVM1 (podstawowy)

1. Utwórz lub zaktualizuj pliku tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Utwórz bramę Golden konta właściciela i użytkownika.

   > [!NOTE]
   > Właściciel konta musi mieć prefiks języka C#.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Tworzenie konta użytkownika test Golden bramy:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Skonfiguruj plik parametrów wyodrębniania.

   Uruchom interfejs wiersza polecenia złoty bramy (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```
5. Dodaj następujący element do pliku parametrów WYODRĘBNIANIA (przy użyciu polecenia vi). Naciśnij klawisz Esc, ': wq! " Aby zapisać plik. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```
6. Zarejestruj Wyodrębnij — zintegrowane wyodrębniania:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```
7. Konfigurowanie punktów kontrolnych wyodrębniania i Rozpocznij wyodrębniania w czasie rzeczywistym:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```
   W tym kroku możesz znaleźć SCN początkową, która będzie używana później w innej sekcji:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Konfigurowanie usługi na myVM2 (replikacja)


1. Utwórz lub zaktualizuj pliku tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Utwórz konto replikacji:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Utwórz bramę Golden testowe konto użytkownika:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. Plik parametrów REPLICAT do replikowania zmian: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```
   Zawartość pliku parametrów REPORA:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Skonfiguruj punkt kontrolny replikacji:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Konfigurowanie replikacji (myVM1 i myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Konfigurowanie replikacji na myVM2 (replikacja)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Aktualizacja pliku następującym kodem:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Następnie uruchom ponownie usługę Menedżer:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Konfigurowanie replikacji na myVM1 (podstawowy)

Rozpocznij ładowania początkowego i sprawdź błędy:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Konfigurowanie replikacji na myVM2 (replikacja)

Zmiana SCN liczba numer zostanie uzyskany wcześniej:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
Replikacja została rozpoczęta i mogli ją przetestować, wstawianie nowych rekordów do tabel testu.


### <a name="view-job-status-and-troubleshooting"></a>Wyświetlanie stanu zadania i rozwiązywania problemów

#### <a name="view-reports"></a>Wyświetlanie raportów
Aby wyświetlić raporty dotyczące myVM1, uruchom następujące polecenia:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Aby wyświetlić raporty dotyczące myVM2, uruchom następujące polecenia:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Wyświetl stan i historię
Aby wyświetlić stan i historię w myVM1, uruchom następujące polecenia:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Aby wyświetlić stan i historię w myVM2, uruchom następujące polecenia:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Na tym kończy instalację i konfigurację Golden bramy w systemie Oracle linux.


## <a name="delete-the-virtual-machine"></a>Usuń maszynę wirtualną

Gdy nie będzie już potrzebny, następujące polecenie może służyć do usunięcia grupy zasobów, maszyna wirtualna i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

[Samouczek dotyczący tworzenia maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)

[Przegląd przykładowych poleceń interfejsu wiersza polecenia umożliwiających wdrożenie maszyny wirtualnej](../../linux/cli-samples.md)
