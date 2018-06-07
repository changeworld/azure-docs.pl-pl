---
title: Konfigurowanie funkcji ASM Oracle na maszynie wirtualnej platformy Azure w systemie Linux | Dokumentacja firmy Microsoft
description: Szybko uzyskać ASM Oracle w górę i w swoim środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2017
ms.author: rclaus
ms.openlocfilehash: cc75235680eeace5107ef6ac0380e8b7a42974fc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656113"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Skonfiguruj rozwiązanie Oracle ASM na maszynie wirtualnej z systemem Linux na platformie Azure  

Maszyny wirtualne platformy Azure oferują w pełni konfigurowalne i elastyczne środowiska obliczeniowe. Ten samouczek obejmuje wdrożenia podstawowej maszyny wirtualnej platformy Azure, połączeniu z instalacji i konfiguracji programu Oracle automatycznego magazyn zarządzania (ASM).  Omawiane kwestie:

> [!div class="checklist"]
> * Utworzyć i nawiązywanie z maszyny Wirtualnej bazy danych programu Oracle
> * Instalowanie i konfigurowanie programu Oracle automatycznego zarządzania magazynem
> * Instalowanie i konfigurowanie infrastruktury Oracle siatki
> * Inicjowanie instalacji Oracle ASM
> * Utwórz bazę danych programu Oracle, zarządza ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć grupę zasobów, użyj polecenia [az group create](/cli/azure/group#az_group_create). Grupy zasobów platformy Azure jest kontenerem logicznym, w której maszyny wirtualne Azure są wdrożone i zarządzane zasoby. W tym przykładzie grupy zasobów o nazwie *myResourceGroup* w *eastus* regionu.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną na podstawie obrazu bazą danych Oracle i skonfigurować go do używania funkcji ASM Oracle, użyj [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. 

Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM, który ma rozmiar Standard_DS2_v2 z czterech dysków dołączonych danych 50 GB. Jeśli są one jeszcze nie istnieją w domyślnej lokalizacji klucza, tworzy również kluczy SSH.  Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Po utworzeniu maszyny Wirtualnej Azure CLI Wyświetla informacje podobne do poniższego przykładu. Zwróć uwagę na wartość dla `publicIpAddress`. Ten adres umożliwia dostęp do maszyny Wirtualnej.

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

Aby utworzyć sesję SSH z maszyną Wirtualną i skonfigurować dodatkowe ustawienia, użyj następującego polecenia. Zastąp adres IP z `publicIpAddress` wartość dla maszyny Wirtualnej.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Zainstaluj Oracle ASM

Aby zainstalować Oracle ASM, wykonaj następujące kroki. 

Aby uzyskać więcej informacji na temat instalowania funkcji ASM Oracle, zobacz [Oracle ASMLib pliki do pobrania dla Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Należy się zalogować jako główny, aby kontynuować instalację funkcji ASM:

   ```bash
   sudo su -
   ```
   
2. Uruchom te dodatkowe polecenia, aby zainstalować składniki Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Sprawdź, czy zainstalowano Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Dane wyjściowe tego polecenia powinny zawierać następujące składniki:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM musi konkretnych użytkowników i ról w celu poprawnego działania. Następujące polecenia Tworzenie grup i kont użytkowników wstępnych: 

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

    Dane wyjściowe tego polecenia powinien zawierać następujący użytkownicy i grupy:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Utwórz folder dla użytkownika *siatki* i zmienić właściciela:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Konfigurowanie funkcji ASM Oracle

W tym samouczku jest domyślny użytkownik *siatki* i jest domyślna grupa *asmadmin*. Upewnij się, że *oracle* użytkownika jest częścią grupy asmadmin. Aby skonfigurować instalację Oracle ASM, wykonaj następujące czynności:

1. Konfigurowanie sterownika biblioteki Oracle ASM obejmuje zdefiniowanie domyślny użytkownik (siatki) oraz domyślnej grupy (asmadmin), jak również konfigurowanie stacji, aby uruchomić podczas rozruchu (Wybierz y) i skanowanie dysków w rozruchu (Wybierz y). Należy odpowiedzieć monity z następującego polecenia:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Dane wyjściowe tego polecenia powinien wyglądać podobnie do poniższych monituje zatrzymywania z należy odpowiedzieć.

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

2. Widok konfiguracji dysków:
   ```bash
   cat /proc/partitions
   ```

   Dane wyjściowe tego polecenia powinien wyglądać podobnie do poniższej listy dostępnych dysków

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

3. Formatuj dysk */dev/sdc* , uruchamiając następujące polecenie i udzielenie odpowiedzi na monity o:
   - *n* dla nowej partycji
   - *p* dla partycji podstawowej
   - *1* wybierz pierwszej partycji
   - Naciśnij klawisz `enter` dla domyślnego pierwszy cylinder
   - Naciśnij klawisz `enter` dla domyślnego ostatni cylinder
   - Naciśnij klawisz *w* do zapisywania zmian w tabeli partycji  

   ```bash
   fdisk /dev/sdc
   ```
   
   Za pomocą odpowiedzi podanego powyżej, dane wyjściowe polecenia fdisk powinna wyglądać następująco:

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

6. Sprawdź stan usługi Oracle ASM i uruchomić usługę Oracle ASM:

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

7. Utwórz dyski Oracle ASM:

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

8. Listy Oracle ASM dysków:

   ```bash
   service oracleasm listdisks
   ```   

   Dane wyjściowe polecenia powinny zawierać poza następujących dysków Oracle ASM:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Zmienianie haseł dla użytkowników głównego, oracle i siatki. **Zanotuj te nowe hasła** jak używasz je później, podczas instalacji.

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

1. Pobierz Oracle siatki infrastruktury z [strony pobierania programu Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   W obszarze pobierania zatytułowany **bazą danych Oracle 12c wersji 1 siatki infrastruktury (12.1.0.2.0) dla systemu Linux x86-64**, Pobierz dwa pliki zip.

2. Po pobraniu plików ZIP na komputerze klienckim służy protokół Secure kopiowania (SCP) do skopiowania plików do maszyny Wirtualnej:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH wrócić do maszyny Wirtualnej na platformie Azure, aby przenieść pliki zip do Oracle / opt folderu. Następnie należy zmienić właściciela plików:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Rozpakowywanie plików. (Instalacja systemu Linux Rozpakuj narzędzia, jeśli to nie jest jeszcze zainstalowana.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Zmień uprawnienia:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Obszar wymiany aktualizacji skonfigurowane. Składniki siatki Oracle potrzeba co najmniej 6.8 GB obszaru wymiany do zainstalowania siatki. Domyślny rozmiar pliku wymiany obrazów Oracle Linux na platformie Azure jest tylko 2048MB. Należy zwiększyć `ResourceDisk.SwapSizeMB` w `/etc/waagent.conf` pliku i uruchom ponownie usługę WALinuxAgent, aby zaktualizowane ustawienia zaczęły obowiązywać. Ponieważ jest to plik tylko do odczytu, musisz zmienić uprawnienia umożliwiające dostęp do zapisu do pliku.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Wyszukaj `ResourceDisk.SwapSizeMB` i zmień wartość na **8192**. Konieczne będzie naciśnij `insert` do trybu wstawiania, wpisz wartość **8192** , a następnie naciśnij klawisz `esc` aby powrócić do trybu polecenia. Aby zapisać zmiany i zamknij plik, wpisz `:wq` i naciśnij klawisz `enter`.
   
   > [!NOTE]
   > Zdecydowanie zaleca się używanie `WALinuxAgent` skonfigurować obszar wymiany, więc, że jest ona zawsze tworzona na lokalnym dysku tymczasowych (dysku tymczasowym), aby uzyskać najlepszą wydajność. Aby uzyskać więcej informacji o zobacz [sposób dodawania pliku wymiany na maszynach wirtualnych Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Przygotowanie lokalnego klienta i maszyny Wirtualnej, aby uruchomić x11
Konfigurowanie funkcji ASM Oracle wymaga interfejs graficzny służący do ukończenia instalacji i konfiguracji. Używamy x11 protokół w celu ułatwienia tej instalacji. Jeśli używasz systemu klienta (Mac lub Linux), który ma już X11 możliwości włączone i skonfigurowane — można pominąć tej konfiguracji i instalacji wyłączności na komputerach z systemem Windows. 

1. [Pobierz program PuTTY](http://www.putty.org/) i [Pobierz Xming](https://xming.en.softonic.com/) do komputera z systemem Windows. Należy zainstalować obie aplikacje z wartościami domyślnymi, przed kontynuowaniem.

2. Po zainstalowaniu programu PuTTY, otwórz wiersz polecenia, Zmień w folderze programu PuTTY (na przykład C:\Program Files\PuTTY), a następnie uruchom `puttygen.exe` aby wygenerować klucz.

3. W PuTTY generatora klucza:
   
   1. Generowanie klucza wybierając `Generate` przycisku.
   2. Skopiuj zawartość klucza (Ctrl + C).
   3. Wybierz `Save private key` przycisku.
   4. Ignoruj ostrzeżenia dotyczące zabezpieczania klucza za pomocą hasła, a następnie wybierz `OK`.

   ![Zrzut ekranu przedstawiający PuTTY generatora klucza](./media/oracle-asm/puttykeygen.png)

4. W przypadku maszyny Wirtualnej uruchom następujące polecenia:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Utwórz plik o nazwie `authorized_keys`. Wklej zawartość klucza w tym pliku, a następnie zapisz plik.

   > [!NOTE]
   > Klucz musi zawierać ciąg `ssh-rsa`. Ponadto zawartość klucza musi być pojedynczy wiersz tekstu.
   >  

6. W systemie klienta Uruchom program PuTTY. W **kategorii** okienka, przejdź do **połączenia** > **SSH** > **uwierzytelniania**. W **pliku klucza prywatnego dla uwierzytelniania** polu, przejdź do klucza, wcześniej wygenerowany.

   ![Zrzut ekranu przedstawiający opcje uwierzytelniania SSH](./media/oracle-asm/setprivatekey.png)

7. W **kategorii** okienka, przejdź do **połączenia** > **SSH** > **X11**. Wybierz **przekazywania Włącz X11** pole wyboru.

   ![Zrzut ekranu przedstawiający SSH X11 przekazywania opcji](./media/oracle-asm/enablex11.png)

8. W **kategorii** okienka, przejdź do **sesji**. Wprowadź maszyny Wirtualnej funkcji ASM Oracle `<publicIPaddress>` w oknie dialogowym nazwy hosta Wypełnij nowy `Saved Session` nazwę, a następnie kliknij polecenie `Save`.  Po zapisaniu kliknij `open` nawiązywania połączenia z maszyną wirtualną funkcji ASM Oracle.  Podczas pierwszego łączenia zostanie wyświetlone ostrzeżenie, że w systemie zdalnym nie jest buforowana w rejestrze. Polecenie `yes` Aby dodać go i kontynuować.

   ![Zrzut ekranu przedstawiający opcje sesji programu PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalowanie infrastruktury siatki Oracle

Aby zainstalować program Oracle siatki infrastruktury, wykonaj następujące czynności:

1. Zaloguj się jako **siatki**. (Można się zalogować bez konieczności podawania hasła.) 

   > [!NOTE]
   > Jeśli korzystasz z systemu Windows, upewnij się, że została uruchomiona Xming przed rozpoczęciem instalacji.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Otwiera Oracle siatki infrastruktury 12c wersji 1 Instalatora. (Może upłynąć kilka minut uruchomić Instalatora.)

2. Na **wybierz opcję instalacji** wybierz **Instalowanie i konfigurowanie infrastruktury siatki Oracle na autonomicznym serwerze**.

   ![Zrzut ekranu przedstawiający stronę Wybieranie opcji instalacji Instalatora](./media/oracle-asm/install01.png)

3. Na **wybierz języki produktu** upewnij się, **angielski** lub po wybraniu języka, który ma.  Kliknij pozycję `next` (Dalej).

4. Na **Utwórz grupę dysku ASM** strony:
   - Wprowadź nazwę grupy dysków.
   - W obszarze **nadmiarowość**, wybierz pozycję **zewnętrznych**.
   - W obszarze **rozmiar jednostki alokacji**, wybierz pozycję **4**.
   - W obszarze **dodawanie dysków**, wybierz pozycję **ORCLASMSP**.
   - Kliknij pozycję `next` (Dalej).

5. Na **Określ hasło ASM** wybierz pozycję **użyć takich samych haseł dla tych kont** opcję i wprowadź hasło.

   ![Zrzut ekranu przedstawiający stronę Określ hasło ASM Instalatora](./media/oracle-asm/install04.png)

6. Na **Określ opcje zarządzania** strony, istnieje możliwość konfigurowania EM chmury kontroli. Ta opcja jest pomijane — kliknij przycisk `next` aby kontynuować. 

7. Na **uprzywilejowanych grup systemu operacyjnego** Użyj ustawień domyślnych. Kliknij przycisk `next` aby kontynuować.

8. Na **Określ lokalizację instalacji** Użyj ustawień domyślnych. Kliknij przycisk `next` aby kontynuować.

9. Na **tworzenia spisu** pozycję Zmień katalog na spisu `/u01/app/grid/oraInventory`. Kliknij przycisk `next` aby kontynuować.

   ![Zrzut ekranu strony tworzenia spisu Instalatora](./media/oracle-asm/install08.png)

10. Na **konfigurację wykonywania skryptu głównego** wybierz pozycję **automatycznego uruchamiania skryptów konfiguracyjnych** pole wyboru. Następnie wybierz opcję **Użyj poświadczeń użytkownika "root"** opcji, a następnie wprowadź hasło użytkownika głównego.

    ![Zrzut ekranu przedstawiający stronę wykonywania skryptu Instalatora głównego w konfiguracji](./media/oracle-asm/install09.png)

11. Na **wykonać wymagań wstępnych sprawdza** strony, bieżącej konfiguracji zakończy się niepowodzeniem z błędami. Jest to oczekiwane zachowanie. Wybierz pozycję `Fix & Check Again`.

12. W **skryptu naprawy** okno dialogowe, kliknij przycisk `OK`.

13. Na **Podsumowanie** , przejrzyj wybrane ustawienia, a następnie kliknij przycisk `Install`.

    ![Zrzut ekranu przedstawiający stronę podsumowania Instalatora](./media/oracle-asm/install12.png)

14. Okno dialogowe zostanie wyświetlone informowania możesz konfigurację, którą należy uruchomić jako użytkownika uprzywilejowanego skryptów. Kliknij przycisk `Yes` aby kontynuować.

15. Na **Zakończ** kliknij przycisk `Close` do zakończenia instalacji.

## <a name="set-up-your-oracle-asm-installation"></a>Konfiguruje instalację Oracle ASM

Aby skonfigurować instalację Oracle ASM, wykonaj następujące czynności:

1. Upewnij się, jest nadal zalogowany jako **siatki**, z Twojego X11 sesji. Konieczne może być trafień `enter` do zostanie przywrócony terminala. Następnie uruchom Oracle automatycznego magazyn zarządzania konfiguracji Asystencie:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Otwiera Asystenta konfiguracji ASM Oracle.

2. W **skonfigurować ASM: grupy dysków** okno dialogowe, kliknij przycisk `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options`.

3. W **Utwórz grupę dysku** okno dialogowe:

   - Wprowadź nazwę grupy dysku **danych**.
   - W obszarze **Wybierz dyski elementu członkowskiego**, wybierz pozycję **ORCL_DATA** i **ORCL_DATA1**.
   - W obszarze **rozmiar jednostki alokacji**, wybierz pozycję **4**.
   - Kliknij przycisk `ok` do utworzenia grupy dysków.
   - Kliknij przycisk `ok` aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysku](./media/oracle-asm/asm02.png)

4. W **skonfigurować ASM: grupy dysków** okno dialogowe, kliknij przycisk `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options`.

5. W **Utwórz grupę dysku** okno dialogowe:

   - Wprowadź nazwę grupy dysku **FRA**.
   - W obszarze **nadmiarowość**, wybierz pozycję **zewnętrzne (Brak)**.
   - W obszarze **Wybierz dyski elementu członkowskiego**, wybierz pozycję **ORCL_FRA**.
   - W obszarze **rozmiar jednostki alokacji**, wybierz pozycję **4**.
   - Kliknij przycisk `ok` do utworzenia grupy dysków.
   - Kliknij przycisk `ok` aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysku](./media/oracle-asm/asm04.png)

6. Wybierz **zakończenia** zamknąć Asystenta konfiguracji ASM.

   ![Zrzut ekranu przedstawiający Konfigurowanie ASM: okno dialogowe grupy dysków z przycisk Zakończ](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Utwórz bazę danych

Oprogramowanie Oracle bazy danych jest już zainstalowana na obrazie portalu Azure Marketplace. Aby utworzyć bazę danych, wykonaj następujące kroki:

1. Przełącz użytkowników administratora Oracle, a następnie zainicjować odbiornika dla rejestrowania:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Otwiera Asystenta konfiguracji bazy danych.

2. Na **operacji bazy danych** kliknij `Create Database`.

3. Na **tryb tworzenia** strony:

   - Wprowadź nazwę dla bazy danych.
   - Dla **typ magazynu**, upewnij się, **automatyczne zarządzanie magazynu (ASM)** jest zaznaczone.
   - Dla **lokalizacji plików bazy danych**, użyj domyślnej ASM sugerowane lokalizacji.
   - Dla **Szybkie odzyskiwanie obszaru**, użyj domyślnej ASM sugerowane lokalizacji.
   - Wpisz w **hasło administracyjne** i **Potwierdź hasło**.
   - Upewnij się, `create as container database` jest zaznaczone.
   - Wpisz `pluggable database name` wartość.

4. Na **Podsumowanie** , przejrzyj wybrane ustawienia, a następnie kliknij przycisk `Finish` utworzyć bazę danych.

   ![Zrzut ekranu przedstawiający stronę podsumowania](./media/oracle-asm/createdb03.png)

5. Baza danych została utworzona. Na **Zakończ** strony, istnieje możliwość odblokowania dodatkowych kont do tej bazy danych i zmiany hasła. Jeśli chcesz to zrobić, wybierz **zarządzania hasłami** — w przeciwnym razie kliknij `close`.

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Pomyślnie skonfigurowano Oracle automatyczne zarządzanie pamięcią masową na obrazie bazy danych Oracle w witrynie Azure Marketplace.  Podczas tej maszyny Wirtualnej nie są już potrzebne, służy następujące polecenie, aby usunąć grupę zasobów, maszyny Wirtualnej i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

[Samouczek: Konfigurowanie Oracle DataGuard](configure-oracle-dataguard.md)

[Samouczek: Konfigurowanie Oracle GoldenGate](Configure-oracle-golden-gate.md)

Przegląd [projektowania baz danych Oracle](oracle-design.md)
