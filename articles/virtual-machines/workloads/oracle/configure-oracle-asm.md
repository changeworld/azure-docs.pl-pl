---
title: Skonfiguruj rozwiązanie Oracle ASM na maszynie wirtualnej z systemem Linux platformy Azure | Dokumentacja firmy Microsoft
description: Szybko uzyskać rozwiązanie Oracle ASM w górę i w swoim środowisku platformy Azure.
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
ms.openlocfilehash: 0af6e87d3e0b4b3b40b63db07384d4a33a9d43e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66154351"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Skonfiguruj rozwiązanie Oracle ASM na maszynie wirtualnej z systemem Linux na platformie Azure  

Maszyny wirtualne platformy Azure oferują w pełni konfigurowalne i elastyczne środowiska obliczeniowe. W tym samouczku opisano wdrażanie podstawowa maszyna wirtualna platformy Azure w połączeniu z instalacji i konfiguracji programu Oracle zautomatyzowane Storage Management (ASM).  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie i łączenie z maszyną Wirtualną z bazy danych Oracle
> * Instalowanie i Konfigurowanie zarządzania magazynem zautomatyzowane Oracle
> * Instalowanie i konfigurowanie infrastruktury Oracle siatki
> * Zainicjuj instalację programu Oracle ASM
> * Tworzenie bazy danych Oracle, zarządzane przez usługi ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć grupę zasobów, użyj polecenia [az group create](/cli/azure/group). Grupę zasobów platformy Azure to logiczny kontener, w których Azure zasoby są wdrażane i zarządzane. W tym przykładzie grupa zasobów o nazwie *myResourceGroup* w *eastus* regionu.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną na podstawie obrazu bazy danych Oracle Database i skonfigurować go do użycia rozwiązanie Oracle ASM, należy użyć [tworzenie az vm](/cli/azure/vm) polecenia. 

Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM, który rozmiar Standard_DS2_v2 cztery dołączone dyski danych 50 GB. Jeśli jeszcze nie istnieją w domyślnej lokalizacji kluczy, również tworzy klucze SSH.  Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Po utworzeniu maszyny Wirtualnej, wiersza polecenia platformy Azure wyświetli informacje podobne do poniższego przykładu. Zwróć uwagę na wartość dla `publicIpAddress`. Ten adres umożliwia dostęp do maszyny Wirtualnej.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Aby utworzyć sesję SSH z maszyną Wirtualną i skonfigurować dodatkowe ustawienia, należy użyć następującego polecenia. Zastąp adres IP za pomocą `publicIpAddress` wartości dla swojej maszyny Wirtualnej.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalowanie programu Oracle ASM

Aby zainstalować rozwiązanie Oracle ASM, wykonaj następujące czynności. 

Aby uzyskać więcej informacji na temat instalowania programu Oracle ASM, zobacz [Oracle ASMLib pliki do pobrania dla Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Musisz zalogować się jako użytkownik główny, aby kontynuować instalację ASM:

   ```bash
   sudo su -
   ```
   
2. Uruchom następujące dodatkowe polecenia, aby zainstalować składniki programu Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Sprawdź, czy zainstalowana jest rozwiązanie Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Dane wyjściowe tego polecenia powinien zawierać następujące składniki:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM wymaga określonych użytkowników i ról w celu poprawnego działania. Następujące polecenia tworzą wstępnych kont i grup użytkowników: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Sprawdź, użytkownicy i grupy zostały utworzone prawidłowo:

   ```bash
   id grid
   ```

    Dane wyjściowe tego polecenia należy wyświetlić następującym użytkownikom i grupom:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Utwórz folder dla użytkownika *siatki* i zmieniać właściciela:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Skonfiguruj rozwiązanie Oracle ASM

W tym samouczku jest domyślny użytkownik *siatki* i jest domyślną grupą *asmadmin*. Upewnij się, że *oracle* użytkownika jest częścią grupy asmadmin. Aby skonfigurować instalację programu Oracle ASM, wykonaj następujące czynności:

1. Konfigurowanie sterownika biblioteki programu Oracle ASM obejmuje zdefiniowanie domyślny użytkownik (siatka) oraz domyślną grupę (asmadmin), a także konfigurowanie stacji do uruchomienia podczas rozruchu (Wybierz y) i do skanowania pod kątem dysków podczas rozruchu (Wybierz y). Musisz wprowadź dane w monitach z następującego polecenia:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Dane wyjściowe tego polecenia powinny wyglądać podobnie do następujące polecenie, zatrzymywania, za pomocą ustawień wyświetla monit na udzielenie odpowiedzi.

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Widok konfiguracji dysku:
   ```bash
   cat /proc/partitions
   ```

   Dane wyjściowe tego polecenia powinny wyglądać podobnie do poniższej listy dostępnych dysków

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Format dysku */dev/sdc* , uruchamiając następujące polecenie i w odpowiedzi na monity o:
   - *n* dla nowej partycji
   - *p* dla partycji podstawowej
   - *1* wybrać pierwszej partycji
   - Naciśnij klawisz `enter` dla domyślnego pierwszy cylinder
   - Naciśnij klawisz `enter` dla domyślnego ostatni cylinder
   - Naciśnij klawisz *w* można zapisać zmian w tabeli partycji  

   ```bash
   fdisk /dev/sdc
   ```
   
   Przy użyciu odpowiedzi podanych powyżej, dane wyjściowe polecenia fdisk powinien wyglądać następująco:

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Powtórz poprzednie polecenie fdisk dla `/dev/sdd`, `/dev/sde`, i `/dev/sdf`.

5. Sprawdź konfigurację dysku:

   ```bash
   cat /proc/partitions
   ```

   Dane wyjściowe polecenia powinny wyglądać następująco:

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Sprawdź stan usługi programu Oracle ASM, a następnie uruchom usługę programu Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Dane wyjściowe polecenia powinny wyglądać następująco:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Create Oracle ASM disks:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   Dane wyjściowe polecenia powinny wyglądać następująco:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. List Oracle ASM disks:

   ```bash
   service oracleasm listdisks
   ```   

   Dane wyjściowe polecenia powinny listy następujących dysków programu Oracle ASM:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Zmiana haseł dla użytkowników katalogu głównego, oracle i siatki. **Zanotuj te nowe hasła** w przypadku korzystania z nich później, podczas instalacji.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Zmień uprawnienia do folderu:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Pobierz i przygotowanie infrastruktury siatki Oracle

Aby pobrać i przygotować oprogramowanie Oracle siatki infrastruktury, wykonaj następujące czynności:

1. Pobierz Oracle siatki infrastruktury z [stronę pobierania programu Oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   W obszarze pobierania pod tytułem **bazy danych Oracle Database 12c wersji 1 siatki infrastruktury (12.1.0.2.0) dla systemu Linux x86-64**, Pobierz te dwa pliki zip.

2. Po pobraniu plików ZIP na komputer kliencki, można użyć protokołu Secure Copy (SCP) do kopiowania plików do maszyny Wirtualnej:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Protokół SSH na maszynę Wirtualną Oracle na platformie Azure, aby przenieść pliki zip do / opt folderu. Następnie należy zmienić właściciela plików:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Rozpakuj pliki. (Instalacja systemu Linux Rozpakuj narzędzie, jeśli jeszcze nie zainstalowano.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Zmiana uprawnień:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Aktualizacja skonfigurowany obszar wymiany. Składniki siatki Oracle muszą co najmniej 6.8 GB obszaru wymiany zainstalował siatki. Domyślny rozmiar pliku wymiany obrazów oprogramowania Oracle Linux na platformie Azure jest tylko 2048MB. Będzie konieczne zwiększenie `ResourceDisk.SwapSizeMB` w `/etc/waagent.conf` pliku i uruchom ponownie usługę WALinuxAgent aby zaktualizowane ustawienia zaczęły obowiązywać. Ponieważ jest to plik tylko do odczytu, musisz zmienić uprawnienia pliku, aby umożliwić dostęp do zapisu.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Wyszukaj `ResourceDisk.SwapSizeMB` i zmień wartość na **8192**. Należy nacisnąć klawisz `insert` aby przejść do trybu wstawiania, wpisz wartość **8192** , a następnie naciśnij klawisz `esc` aby powrócić do trybu poleceń. Aby zapisać zmiany i zamknij plik, wpisz `:wq` i naciśnij klawisz `enter`.
   
   > [!NOTE]
   > Zdecydowanie zaleca się używanie `WALinuxAgent` skonfigurować obszar wymiany, tak aby zawsze jest tworzony na dysku lokalnym, efemeryczne (dysk tymczasowy) Aby uzyskać najlepszą wydajność. Aby uzyskać więcej informacji na temat, zobacz [sposób dodawania pliku wymiany w systemie Linux w usłudze Azure virtual machines](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Przygotowywanie lokalnego klienta i maszynę Wirtualną, aby uruchomić x11
Konfigurowanie programu Oracle ASM wymaga interfejsu graficznego, aby zakończyć instalację i konfigurację. Używamy x11 protokół w celu ułatwienia tej instalacji. Jeśli używasz systemu klienta (Mac lub Linux), który ma już X11 możliwości włączone i skonfigurowane — można pominąć to konfiguracja i instalacja wyłączne maszyn Windows. 

1. [Pobierz program PuTTY](https://www.putty.org/) i [Pobierz Xming](https://xming.en.softonic.com/) na komputer Windows. Należy zainstalować obie aplikacje z wartościami domyślnymi, przed kontynuowaniem.

2. Po zainstalowaniu programu PuTTY, otwórz wiersz polecenia, przejdź do folderu programu PuTTY (na przykład C:\Program Files\PuTTY), a następnie uruchom `puttygen.exe` w celu wygenerowania klucza.

3. W programu PuTTY generatora klucza:
   
   1. Wygeneruj klucz, wybierając `Generate` przycisku.
   2. Skopiuj zawartość klucza (Ctrl + C).
   3. Wybierz przycisk `Save private key`.
   4. Ignoruj ostrzeżenia o zabezpieczaniu klucza za pomocą hasła, a następnie wybierz `OK`.

   ![Zrzut ekranu przedstawiający Generator kluczy PuTTY](./media/oracle-asm/puttykeygen.png)

4. Na maszynie wirtualnej uruchom następujące polecenia:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Utwórz plik o nazwie `authorized_keys`. Wklej zawartość klucza w tym pliku, a następnie zapisz plik.

   > [!NOTE]
   > Klucz musi zawierać ciąg `ssh-rsa`. Ponadto zawartość klucza musi być pojedynczy wiersz tekstu.
   >  

6. W systemie klienta Uruchom program PuTTY. W **kategorii** okienko, przejdź do **połączenia** > **SSH** > **uwierzytelniania**. W **pliku klucza prywatnego na potrzeby uwierzytelniania** przejdź do klucza który został wcześniej wygenerowany.

   ![Zrzut ekranu przedstawiający opcje uwierzytelniania SSH](./media/oracle-asm/setprivatekey.png)

7. W **kategorii** okienko, przejdź do **połączenia** > **SSH** > **X11**. Wybierz **przekazywania Włącz X11** pole wyboru.

   ![Zrzut ekranu przedstawiający SSH X11 przekazywania opcji](./media/oracle-asm/enablex11.png)

8. W **kategorii** okienko, przejdź do **sesji**. Wprowadź maszynę Wirtualną Oracle ASM `<publicIPaddress>` w oknie dialogowym nazwy hosta wypełnić nową `Saved Session` nazwę, a następnie kliknij polecenie `Save`.  Po zapisaniu kliknij `open` nawiązać połączenia z maszyną wirtualną Oracle ASM.  Przy pierwszym nawiązaniu połączenia zostanie wyświetlone ostrzeżenie, że w systemie zdalnym nie jest buforowana w rejestrze. Kliknij pozycję `yes` ją dodać i kontynuować.

   ![Zrzut ekranu przedstawiający opcje sesji programu PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalowanie infrastruktury siatki Oracle

Aby zainstalować program Oracle siatki infrastruktury, wykonaj następujące czynności:

1. Zaloguj się jako **siatki**. (Należy mogli logować się bez monitowania o podanie hasła.) 

   > [!NOTE]
   > Jeśli korzystasz z Windows, upewnij się, że uruchomieniu Xming przed rozpoczęciem instalacji.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Zostanie otwarty Oracle siatki infrastruktury 12c w wersji 1 Instalatora. (Może upłynąć kilka minut, aż Instalator rozpocząć.)

2. Na **wybierz opcję instalacji** wybierz **Instalowanie i konfigurowanie programu Oracle siatki infrastruktury na autonomicznym serwerze**.

   ![Zrzut ekranu przedstawiający stronę Wybieranie opcji instalacji Instalatora](./media/oracle-asm/install01.png)

3. Na **wybierz języki produktu** strony, upewnij się **angielski** lub wybrano język, który ma.  Kliknij pozycję `next` (Dalej).

4. Na **Utwórz grupę dysku ASM** strony:
   - Wprowadź nazwę grupy dysków.
   - W obszarze **nadmiarowości**, wybierz opcję **zewnętrznych**.
   - W obszarze **rozmiar jednostki alokacji**, wybierz opcję **4**.
   - W obszarze **dodawanie dysków**, wybierz opcję **ORCLASMSP**.
   - Kliknij pozycję `next` (Dalej).

5. Na **Określ hasło ASM** wybierz opcję **używają tych samych haseł dla tych kont** opcji, a następnie wprowadź hasło.

   ![Zrzut ekranu przedstawiający stronę Określ hasło ASM Instalatora](./media/oracle-asm/install04.png)

6. Na **Określ opcje zarządzania** strony, masz możliwość skonfigurowania EM chmury kontroli. Ta opcja jest pomijane — kliknij przycisk `next` aby kontynuować. 

7. Na **uprzywilejowanych grup w systemie operacyjnym** strony, należy użyć ustawień domyślnych. Kliknij przycisk `next` aby kontynuować.

8. Na **Określ lokalizację instalacji** strony, należy użyć ustawień domyślnych. Kliknij przycisk `next` aby kontynuować.

9. Na **Tworzenie spisu** strony, zmień katalog magazynu na `/u01/app/grid/oraInventory`. Kliknij przycisk `next` aby kontynuować.

   ![Zrzut ekranu strony tworzenia spisu Instalatora](./media/oracle-asm/install08.png)

10. Na **konfiguracji wykonywania skryptu głównego** wybierz opcję **automatycznie uruchamiać skrypty konfiguracji** pole wyboru. Następnie wybierz **Użyj poświadczeń użytkownika "root"** opcji, a następnie wprowadź hasło użytkownika głównego.

    ![Zrzut ekranu przedstawiający stronę wykonywania skryptu Instalatora głównego w konfiguracji](./media/oracle-asm/install09.png)

11. Na **wykonywania funkcji sprawdzania wymagań wstępnych** stronie bieżącej konfiguracji zakończy się niepowodzeniem z błędami. Jest to oczekiwane zachowanie. Wybierz pozycję `Fix & Check Again`.

12. W **skryptu naprawy** okno dialogowe, kliknij przycisk `OK`.

13. Na **Podsumowanie** strony, przejrzyj wybrane ustawienia, a następnie kliknij przycisk `Install`.

    ![Zrzut ekranu przedstawiający stronę podsumowania Instalatora](./media/oracle-asm/install12.png)

14. Okno dialogowe ostrzeżenia pojawia się informowania możesz konfiguracji potrzebne skrypty uruchamiane jako użytkownika uprzywilejowanego. Kliknij przycisk `Yes` aby kontynuować.

15. Na **Zakończ** kliknij `Close` aby zakończyć instalację.

## <a name="set-up-your-oracle-asm-installation"></a>Skonfiguruj instalację programu Oracle ASM

Aby skonfigurować instalację programu Oracle ASM, wykonaj następujące czynności:

1. Upewnij się, jest nadal zalogowany jako **siatki**, z Twojej X11 sesji. Konieczne może być osiągnięty `enter` zostać przywrócony terminalu. Następnie uruchom Oracle zautomatyzowane magazynu zarządzania konfiguracji Asystencie:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Otwiera Asystenta konfiguracji programu Oracle ASM.

2. W **skonfigurować ASM: Dysk grup** okno dialogowe, kliknij przycisk `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options`.

3. W **Utwórz grupę dysku** okno dialogowe:

   - Wprowadź nazwę grupy dysków **danych**.
   - W obszarze **Wybierz dyski elementu członkowskiego**, wybierz opcję **ORCL_DATA** i **ORCL_DATA1**.
   - W obszarze **rozmiar jednostki alokacji**, wybierz opcję **4**.
   - Kliknij przycisk `ok` do utworzenia grupy dysków.
   - Kliknij przycisk `ok` aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysków](./media/oracle-asm/asm02.png)

4. W **skonfigurować ASM: Dysk grup** okno dialogowe, kliknij przycisk `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options`.

5. W **Utwórz grupę dysku** okno dialogowe:

   - Wprowadź nazwę grupy dysków **(FRA)** .
   - W obszarze **nadmiarowości**, wybierz opcję **zewnętrzne (Brak)** .
   - W obszarze **Wybierz dyski elementu członkowskiego**, wybierz opcję **ORCL_FRA**.
   - W obszarze **rozmiar jednostki alokacji**, wybierz opcję **4**.
   - Kliknij przycisk `ok` do utworzenia grupy dysków.
   - Kliknij przycisk `ok` aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysków](./media/oracle-asm/asm04.png)

6. Wybierz **zakończenia** zamknąć Asystenta ustawień konfiguracji usługi ASM.

   ![Zrzut ekranu przedstawiający Konfigurowanie ASM: Okno dialogowe grupy dysków za pomocą przycisku Zakończ](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Tworzenie bazy danych

Oprogramowanie bazy danych Oracle jest już zainstalowana na obrazu z witryny Azure Marketplace. Aby utworzyć bazę danych, wykonaj następujące czynności:

1. Przełącz użytkowników administratora bazy danych Oracle, a następnie zainicjować odbiornika dla rejestracji:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Otwiera Asystenta ustawień konfiguracji bazy danych.

2. Na **operacji bazy danych** kliknij `Create Database`.

3. Na **tryb tworzenia** strony:

   - Wprowadź nazwę dla bazy danych.
   - Dla **typ magazynu**, upewnij się, **automatyczne Storage Management (ASM)** jest zaznaczone.
   - Dla **lokalizacja plików bazy danych**, użyj domyślnej usługi ASM sugerowane lokalizacji.
   - Dla **szybkiego odzyskiwania obszaru**, użyj domyślnej usługi ASM sugerowane lokalizacji.
   - Wpisz **hasło administracyjne** i **Potwierdź hasło**.
   - Upewnij się, `create as container database` jest zaznaczone.
   - Wpisz `pluggable database name` wartość.

4. Na **Podsumowanie** strony, przejrzyj wybrane ustawienia, a następnie kliknij przycisk `Finish` utworzyć bazę danych.

   ![Zrzut ekranu przedstawiający stronę podsumowania](./media/oracle-asm/createdb03.png)

5. Baza danych została utworzona. Na **Zakończ** strony, masz możliwość odblokowania dodatkowych kont do tej bazy danych i zmiany hasła. Jeśli chcesz to zrobić, wybierz **zarządzania hasłami** — w przeciwnym razie kliknij `close`.

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Pomyślnie skonfigurowano automatyczne zarządzanie magazynu bazy danych Oracle na obrazie Oracle DB w witrynie Azure Marketplace.  Podczas tej maszyny Wirtualnej nie są już potrzebne, można użyć następującego polecenia można usunąć grupy zasobów, maszyna wirtualna i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

[Samouczek: Konfigurowanie programu Oracle DataGuard](configure-oracle-dataguard.md)

[Samouczek: Konfigurowanie środowiska Oracle GoldenGate](Configure-oracle-golden-gate.md)

Przegląd [architektury baza danych Oracle](oracle-design.md)
