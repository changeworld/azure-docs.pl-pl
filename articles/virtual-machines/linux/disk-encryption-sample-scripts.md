---
title: Azure Disk Encryption przykładowe skrypty
description: Ten artykuł zawiera dodatek do Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ad0e3bbba729436c3a07f44d989a40f5349dfb3e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326362"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption przykładowe skrypty 

Ten artykuł zawiera przykładowe skrypty do przygotowywania wstępnie zaszyfrowanych dysków VHD i innych zadań.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Przykładowe skrypty programu PowerShell dla usługi Azure Disk Encryption 

- **Wyświetl listę wszystkich szyfrowanych maszyn wirtualnych w ramach subskrypcji**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Wyświetl listę wszystkich kluczy tajnych szyfrowania dysku używanych do szyfrowania maszyn wirtualnych w magazynie kluczy** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Korzystanie z skryptu programu PowerShell dla Azure Disk Encryption wymagań wstępnych
Jeśli masz już doświadczenie z wymaganiami wstępnymi dotyczącymi Azure Disk Encryption, możesz użyć [skryptu programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Przykład korzystania z tego skryptu programu PowerShell można znaleźć w temacie [szyfrowanie maszyny wirtualnej — szybki start](disk-encryption-powershell-quickstart.md). Komentarze można usunąć z części skryptu, zaczynając od wiersza 211, do szyfrowania wszystkich dysków dla istniejących maszyn wirtualnych w istniejącej grupie zasobów. 

W poniższej tabeli przedstawiono, w której parametry mogą być używane w skrypcie programu PowerShell: 


|Parametr|Opis|Wypełnione?|
|------|------|------|
|$resourceGroupName| Nazwa grupy zasobów, do której należy magazynu kluczy.  Będzie można utworzyć nową grupę zasobów o tej nazwie, jeśli nie istnieje.| True|
|$keyVaultName|Nazwa magazynu kluczy, w których szyfrowania mają być umieszczone klucze. Jeśli nie istnieje, zostanie utworzony nowy magazyn o tej nazwie.| True|
|$location|Lokalizacja magazynu kluczy. Upewnij się, że magazyn kluczy i maszyny wirtualne, które były szyfrowane znajdują się w tej samej lokalizacji. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.|True|
|$subscriptionId|Identyfikator subskrypcji platformy Azure do użycia.  Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.|True|
|$aadAppName|Nazwa aplikacji usługi Azure AD, która będzie służyć do zapisu kluczy tajnych do magazynu kluczy. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli ta aplikacja już istnieje, należy przekazać parametr aadClientSecret do skryptu.|False|
|$aadClientSecret|Klucz tajny klienta aplikacji usługi Azure AD, który został utworzony wcześniej.|False|
|$keyEncryptionKeyName|Nazwa opcjonalny klucz szyfrowania klucza w magazynie KeyVault. Jeśli nie istnieje, zostanie utworzony nowy klucz o tej nazwie.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Szyfrowania lub odszyfrowywania maszyn wirtualnych bez aplikacji usługi Azure AD

- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Szyfrowania lub odszyfrowywania maszyn wirtualnych za pomocą aplikacji usługi Azure AD (poprzedniej wersji) 
 
- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux. 


- [Utwórz nowy zaszyfrowany dysk zarządzany na podstawie wstępnie zaszyfrowanego obiektu BLOB dysku VHD/magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Tworzy nowego dysku zarządzanego zaszyfrowanych zaszyfrowane wstępnie wirtualnego dysku twardego i jej odpowiednie ustawienia szyfrowania





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Szyfrowanie dysku systemu operacyjnego na działającej maszynie wirtualnej z systemem Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Wymagania wstępne dotyczące szyfrowania dysku systemu operacyjnego

* Maszyna wirtualna musi używać dystrybucji zgodnej z szyfrowaniem dysków systemu operacyjnego, która jest wymieniona w [Azure Disk Encryption obsługiwanych systemach operacyjnych](disk-encryption-overview.md#supported-vm-sizes) 
* Maszyna wirtualna musi zostać utworzona z obrazu z witryny Marketplace usługi Azure Resource Manager.
* Maszyna wirtualna platformy Azure z co najmniej 4 GB pamięci RAM (zalecany rozmiar to 7 GB).
* (Dla systemu RHEL i CentOS) Wyłącz SELinux. Aby wyłączyć SELinux, zobacz "4.4.2. Wyłączenie SELinux "w [podręczniku użytkownika SELinux i administratora](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na maszynie wirtualnej.
* Po wyłączeniu SELinux ponowny rozruch maszyny Wirtualnej co najmniej raz.

### <a name="steps"></a>Kroki
1. Tworzenie maszyny Wirtualnej przy użyciu jednej z dystrybucji określonej wcześniej.

   CentOS 7.2 szyfrowania dysku systemu operacyjnego jest obsługiwane za pośrednictwem niestandardowy obraz. Aby użyć tego obrazu, "7.2n" jako jednostkę SKU można określić podczas tworzenia maszyny Wirtualnej:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurowanie maszyny Wirtualnej, zgodnie z potrzebami. Jeśli zamierzasz do szyfrowania wszystkich (systemu operacyjnego i danych) dysków, dyski z danymi musi być określona i instalacja z/etc/fstab.

   > [!NOTE]
   > Użyj identyfikatora UUID =... do określenia danych dysków w/etc/fstab zamiast określania nazwy urządzenia bloku (na przykład/dev/sdb1). Podczas szyfrowania, kolejność zmiany dyski na maszynie Wirtualnej. Jeśli maszyna wirtualna opiera się na określonej kolejności zablokować urządzenia, nie będzie można go zainstalować je po zaszyfrowaniu.

3. Wyloguj się z sesji SSH.

4. Aby zaszyfrować system operacyjny, określ wartość volumetype jako **All** lub **OS** po włączeniu szyfrowania.

   > [!NOTE]
   > Wszystkie procesy obszaru użytkownika, które nie są uruchomione jako usługi `systemd`, powinny być zabite z `SIGKILL`. Uruchom ponownie maszynę Wirtualną. Po włączeniu szyfrowania dysku systemu operacyjnego na uruchomionej maszyny Wirtualnej, należy zaplanować przestój maszyny Wirtualnej.

5. Okresowo Monitoruj postęp szyfrowania przy użyciu instrukcji w [następnej sekcji](#monitoring-os-encryption-progress).

6. Gdy polecenie Get-AzVmDiskEncryptionStatus zawiera ciąg "VMRestartPending", uruchom ponownie maszynę wirtualną, logując się do niej lub korzystając z portalu, programu PowerShell lub interfejsu wiersza polecenia.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Przed ponownym uruchomieniem zalecamy zapisanie [diagnostyki rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) maszyny wirtualnej.

## <a name="monitoring-os-encryption-progress"></a>Monitorowanie postępu szyfrowania systemu operacyjnego
Możesz monitorować postęp szyfrowania systemu operacyjnego na trzy sposoby:

* Użyj polecenia cmdlet `Get-AzVmDiskEncryptionStatus` i sprawdź pole komunikat dotyczący postępu:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Po maszyny Wirtualnej osiągnie "Do szyfrowania dysku systemu operacyjnego", trwa około 40 – 50 minut na usługę Premium storage kopii maszyny Wirtualnej.

  Z powodu [problemu #388](https://github.com/Azure/WALinuxAgent/issues/388) w WALinuxAgent, `OsVolumeEncrypted` i `DataVolumesEncrypted` wyświetlane jako `Unknown` w niektórych dystrybucjach. Za pomocą WALinuxAgent w wersji 2.1.5 i nowszy, ten problem jest rozwiązany automatycznie. Jeśli w danych wyjściowych zostanie wyświetlony `Unknown`, można sprawdzić stan szyfrowania dysku przy użyciu Azure Resource Explorer.

  Przejdź do [Azure Resource Explorer](https://resources.azure.com/), a następnie rozwiń tę hierarchię w panelu wyboru po lewej stronie:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  W InstanceView przewiń w dół, aby wyświetlić stan szyfrowania dysków.

  ![Widok wystąpienia maszyny Wirtualnej](./media/disk-encryption/vm-instanceview.png)

* Spójrz na [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Komunikaty z rozszerzenia ADE powinny być poprzedzone `[AzureDiskEncryption]`.

* Zaloguj się do maszyny Wirtualnej za pośrednictwem protokołu SSH i uzyskać dziennika rozszerzeń, od:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Firma Microsoft zaleca się czy nie logowania do maszyny Wirtualnej w trakcie szyfrowania systemu operacyjnego. Skopiuj dzienniki tylko wtedy, gdy te dwie metody nie powiodło się.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux
Przygotowanie do zaszyfrowane wstępnie wirtualne dyski twarde mogą się różnić w zależności od dystrybucji. Przykłady dotyczące przygotowywania Ubuntu 16, openSUSE 13,2 i CentOS 7 są dostępne. 

### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurowanie szyfrowania podczas instalacji dystrybucji, wykonując następujące czynności:

1. Wybierz opcję **Konfiguruj woluminy szyfrowane** podczas partycjonowania dysków.

   ![Ubuntu 16.04 Konfigurowanie — Konfigurowanie zaszyfrowanych woluminów](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Tworzenie dysku rozruchowego oddzielne nie muszą być szyfrowane. Szyfrowanie dysku głównego.

   ![Instalator systemu Ubuntu 16.04 - wybierz urządzenia do zaszyfrowania](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Podaj hasło. Jest to hasło, który został przekazany do magazynu kluczy.

   ![Ubuntu 16.04 Konfigurowanie — Podaj hasło](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Zakończ partycjonowania.

   ![Ubuntu 16.04 Konfigurowanie — Zakończ partycjonowania](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Podczas rozruchu maszyny Wirtualnej i monit o podanie hasła, należy użyć hasło, które podano w kroku 3.

   ![Ubuntu 16.04 Konfigurowanie — Podaj hasło podczas rozruchu](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Przygotuj maszynę wirtualną do przekazania do platformy Azure, korzystając z [tych instrukcji](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nie uruchamiaj w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

Konfigurowanie szyfrowania do pracy z platformą Azure, wykonując następujące czynności:

1. Utwórz plik w obszarze /usr/local/sbin/azure_crypt_key.sh, z zawartością w poniższym skrypcie. Należy zwrócić uwagę na KeyFileName, ponieważ jest to nazwa pliku hasło używane przez platformę Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Zmień konfigurację Crypt w */etc/crypttab*. Powinny wyglądać następująco:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Dodaj uprawnienia pliku wykonywalnego do skryptu:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Edytuj */etc/initramfs-tools/modules* przez dołączenie wierszy:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Uruchom `update-initramfs -u -k all`, aby zaktualizować initramfs, aby zmiany `keyscript` zaczęły obowiązywać.

7. Teraz można anulować aprowizację maszyny Wirtualnej.

   ![Instalator systemu Ubuntu 16.04 - initramfs aktualizacji](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Przejdź do kolejnego etapu i przekazywanie wirtualnego dysku twardego na platformie Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Podczas partycjonowania dysków wybierz pozycję **Szyfruj grupę woluminów**, a następnie wprowadź hasło. To hasło, które należy przekazać do magazynu kluczy.

   ![openSUSE 13.2 instalacji — grupa szyfrowania woluminów](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Rozruch maszyny Wirtualnej, przy użyciu hasła.

   ![openSUSE 13.2 Konfigurowanie — Podaj hasło podczas rozruchu](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Przygotuj MASZYNę wirtualną do przekazania na platformę Azure, postępując zgodnie z instrukcjami w temacie [przygotowanie maszyny wirtualnej SLES lub openSUSE dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nie uruchamiaj w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

Aby skonfigurować szyfrowanie w celu współpracy z platformą Azure, wykonaj następujące czynności:
1. Edytuj /etc/dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Komentarz następujące wiersze do końca /usr/lib/dracut/modules.d/90crypt/module-setup.sh pliku:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Dołącz następujący wiersz na początku /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh pliku:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I Zamień wszystkie wystąpienia klasy:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na:
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh i dołącza je do "# Otwórz LUKS urządzenie":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Uruchom `/usr/sbin/dracut -f -v`, aby zaktualizować oryginalnych initrd.

6. Możesz teraz anulować aprowizację maszyny Wirtualnej i przekazywanie wirtualnego dysku twardego na platformie Azure.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 i RHEL 8,1

Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Wybierz opcję **Szyfruj moje dane** podczas partycjonowania dysków.

   ![Konfigurowanie centOS 7 - miejsce docelowe instalacji](./media/disk-encryption/centos-encrypt-fig1.png)

2. Upewnij się, że wybrano **szyfrowanie** dla partycji głównej.

   ![Konfigurowanie centOS 7 - wybierz szyfrowania dla partycji katalogu głównego](./media/disk-encryption/centos-encrypt-fig2.png)

3. Podaj hasło. Jest to hasło, które należy przekazać do magazynu kluczy.

   ![Instalator centOS 7 - Podaj hasło](./media/disk-encryption/centos-encrypt-fig3.png)

4. Podczas rozruchu maszyny Wirtualnej i monit o podanie hasła, należy użyć hasło, które podano w kroku 3.

   ![CentOS 7 Konfigurowanie — wprowadź hasło w rozruchu](./media/disk-encryption/centos-encrypt-fig4.png)

5. Przygotuj maszynę wirtualną do przekazania na platformę Azure, używając instrukcji "CentOS 7.0 +" w temacie [przygotowanie maszyny wirtualnej opartej na CentOS dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nie uruchamiaj w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

6. Możesz teraz anulować aprowizację maszyny Wirtualnej i przekazywanie wirtualnego dysku twardego na platformie Azure.

Aby skonfigurować szyfrowanie w celu współpracy z platformą Azure, wykonaj następujące czynności:

1. Edytuj /etc/dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Komentarz następujące wiersze do końca /usr/lib/dracut/modules.d/90crypt/module-setup.sh pliku:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Dołącz następujący wiersz na początku /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh pliku:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I Zamień wszystkie wystąpienia klasy:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh i Dołącz następujący po "# Otwórz LUKS urządzenie":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Uruchom "/ usr/sbin/dracut - f - v" można zaktualizować initrd.

    ![CentOS 7 - /usr/sbin/dracut -f - v działanie Instalatora](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Przekazywanie zaszyfrowanego wirtualnego dysku twardego do konta usługi Azure Storage
Po włączeniu szyfrowania DM-Crypt należy przekazać lokalny zaszyfrowany wirtualny dysk twardy do konta magazynu.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Przekaż klucz tajny wstępnie zaszyfrowanej maszyny wirtualnej do magazynu kluczy
W przypadku szyfrowania przy użyciu aplikacji usługi Azure AD (poprzedniej wersji), należy przekazać klucz tajny szyfrowania dysku, który został uzyskany wcześniej w formie wpisu tajnego w magazynie kluczy. Magazyn kluczy wymaga szyfrowania dysku i włączonymi uprawnieniami dla klienta usługi Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku nie został zaszyfrowany za pomocą elementu KEK
Aby skonfigurować wpis tajny w magazynie kluczy, użyj polecenie [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Hasło jest kodowane jako ciąg Base64, a następnie przekazywane do magazynu kluczy. Ponadto upewnij się, że następujące znaczniki są ustawione podczas tworzenia klucza tajnego w magazynie kluczy.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Użyj `$secretUrl` w następnym kroku, aby [dołączyć dysk systemu operacyjnego bez używania KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku zaszyfrowany za pomocą KEK
Przed przekazaniem wpisu tajnego do magazynu kluczy, można opcjonalnie zaszyfrować przy użyciu klucza szyfrowania. Użyj [interfejsu API](https://msdn.microsoft.com/library/azure/dn878066.aspx) Otocz, aby najpierw zaszyfrować klucz tajny przy użyciu klucza szyfrowania klucza. Dane wyjściowe tej operacji zawijania to ciąg zakodowany przez adres URL w formacie Base64, który można następnie przekazać jako wpis tajny za pomocą polecenia cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Użyj `$KeyEncryptionKey` i `$secretUrl` w następnym kroku w celu [dołączenia dysku systemu operacyjnego za pomocą KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Określ tajny adres URL podczas dołączania dysku systemu operacyjnego

###  <a name="without-using-a-kek"></a>Bez użycia KEK
Podczas dołączania dysku systemu operacyjnego należy przekazać `$secretUrl`. Adres URL został wygenerowany w sekcji "nie jest szyfrowana za pomocą klucza KEK tajny szyfrowania dysku".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Korzystanie z KEK
Po dołączeniu dysku systemu operacyjnego Przekaż `$KeyEncryptionKey` i `$secretUrl`. Adres URL został wygenerowany w sekcji "Wpis tajny szyfrowania dysku zaszyfrowane przy użyciu klucza KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
