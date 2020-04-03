---
title: Przykładowe skrypty usługi Azure Disk Encryption
description: Ten artykuł jest dodatkiem do szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b54f9f3466fe5f7e2da622077f53575d6f43f72d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585957"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Przykładowe skrypty usługi Azure Disk Encryption 

Ten artykuł zawiera przykładowe skrypty do przygotowywania wstępnie zaszyfrowanych dysków VHD i innych zadań.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Przykładowe skrypty programu PowerShell do szyfrowania dysków platformy Azure 

- **Wyświetlanie listy wszystkich zaszyfrowanych maszyn wirtualnych w ramach subskrypcji**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Wyświetlanie listy wszystkich wpisów tajnych szyfrowania dysków używanych do szyfrowania maszyn wirtualnych w magazynie kluczy** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Używanie skryptu wstępnego szyfrowania dysków platformy Azure
Jeśli znasz już wymagania wstępne dotyczące szyfrowania dysków platformy Azure, możesz użyć [skryptu wstępnego powershell wymagań wstępnych szyfrowania dysków platformy Azure.](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ) Na przykład użycia tego skryptu programu PowerShell zobacz [Szybki start szyfrowania maszyny Wirtualnej](disk-encryption-powershell-quickstart.md). Można usunąć komentarze z sekcji skryptu, począwszy od wiersza 211, aby zaszyfrować wszystkie dyski dla istniejących maszyn wirtualnych w istniejącej grupie zasobów. 

W poniższej tabeli przedstawiono, które parametry mogą być używane w skrypcie programu PowerShell: 


|Parametr|Opis|Obowiązkowe?|
|------|------|------|
|$resourceGroupName| Nazwa grupy zasobów, do której należy keyvault.  Nowa grupa zasobów o tej nazwie zostanie utworzona, jeśli jej nie istnieje.| True|
|$keyVaultName|Nazwa keyvault, w którym mają być umieszczone klucze szyfrowania. Nowy magazyn o tej nazwie zostanie utworzony, jeśli nie istnieje.| True|
|$location|Lokalizacja keyvault. Upewnij się, że keyvault i maszyny wirtualne, które mają być zaszyfrowane są w tej samej lokalizacji. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.|True|
|$subscriptionId|Identyfikator subskrypcji platformy Azure, który ma być używany.  Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.|True|
|$aadAppName|Nazwa aplikacji usługi Azure AD, która będzie używana do zapisywania wpisów tajnych do usługi KeyVault. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli ta aplikacja już istnieje, przekaż aadClientSecret parametr do skryptu.|False|
|$aadClientSecret|Klucz tajny klienta aplikacji usługi Azure AD, który został utworzony wcześniej.|False|
|$keyEncryptionKeyName|Nazwa opcjonalnego klucza szyfrowania klucza w keyvault. Nowy klucz o tej nazwie zostanie utworzony, jeśli nie istnieje.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych bez aplikacji usługi Azure AD

- [Włączanie szyfrowania dysku na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Wyłącz szyfrowanie na uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Wyłączenie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych za pomocą aplikacji usługi Azure AD (poprzednia wersja) 
 
- [Włączanie szyfrowania dysku na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Wyłącz szyfrowanie na uruchomionej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Wyłączenie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux. 


- [Tworzenie nowego zaszyfrowanego dysku zarządzanego ze wstępnie zaszyfrowanego obiektu blob VHD/storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Tworzy nowy zaszyfrowany dysk zarządzany pod warunkiem wstępnie zaszyfrowanego dysku VHD i odpowiadających mu ustawień szyfrowania





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Szyfrowanie dysku systemu operacyjnego na uruchomionej maszynie wirtualnej z systemem Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Wymagania wstępne dotyczące szyfrowania dysku systemu operacyjnego

* Maszyna wirtualna musi używać dystrybucji zgodnej z szyfrowaniem dysku systemu operacyjnego, zgodnie z wymienionymi w [obsługiwanych systemach operacyjnych Azure Disk Encryption](disk-encryption-overview.md#supported-vms) 
* Maszyna wirtualna musi zostać utworzona na podstawie obrazu portalu Marketplace w usłudze Azure Resource Manager.
* Maszyna wirtualna platformy Azure z co najmniej 4 GB pamięci RAM (zalecany rozmiar to 7 GB).
* (Dla RHEL i CentOS) Wyłącz SELinux. Aby wyłączyć SELinux, zobacz "4.4.2. Wyłączenie SELinux" w [Przewodniku użytkownika i administratora SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na maszynie wirtualnej.
* Po wyłączeniu SELinux uruchom ponownie maszynę wirtualną co najmniej raz.

### <a name="steps"></a>Kroki
1. Utwórz maszynę wirtualną przy użyciu jednej z wcześniej określonych dystrybucji.

   W przypadku systemu CentOS 7.2 szyfrowanie dysku systemu operacyjnego jest obsługiwane za pomocą specjalnego obrazu. Aby użyć tego obrazu, należy określić "7.2n" jako jednostkę SKU podczas tworzenia maszyny Wirtualnej:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Skonfiguruj maszynę wirtualną zgodnie z potrzebami. Jeśli masz zamiar zaszyfrować wszystkie dyski (OS + dane), dyski danych muszą być określone i można go zainstalować z /etc/fstab.

   > [!NOTE]
   > Użyj UUID=... , aby określić dyski danych w /etc/fstab zamiast określać nazwę urządzenia bloku (na przykład /dev/sdb1). Podczas szyfrowania kolejność dysków zmienia się na maszynie Wirtualnej. Jeśli maszyna wirtualna opiera się na określonej kolejności urządzeń blokowych, nie można ich zainstalować po szyfrowaniu.

3. Wyloguj się z sesji SSH.

4. Aby zaszyfrować system operacyjny, po włączeniu szyfrowania należy określić typ woluminu jako **wszystkie** lub **system operacyjny.**

   > [!NOTE]
   > Wszystkie procesy przestrzeni użytkownika, `systemd` które nie są `SIGKILL`uruchomione jako usługi powinny zostać zabite za pomocą pliku . Uruchom ponownie maszynę wirtualną. Po włączeniu szyfrowania dysku systemu operacyjnego na uruchomionej maszynie wirtualnej należy zaplanować przestoje maszyny Wirtualnej.

5. Okresowo monitoruj postęp szyfrowania, korzystając z instrukcji w [następnej sekcji](#monitoring-os-encryption-progress).

6. Po Get-AzVmDiskEncryptionStatus pokazuje "VMRestartPending", uruchom ponownie maszynę wirtualną albo logując się do niego lub za pomocą portalu, Programu PowerShell lub interfejsu wiersza polecenia.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Przed ponownym uruchomieniem zaleca się [zapisanie diagnostyki rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) maszyny Wirtualnej.

## <a name="monitoring-os-encryption-progress"></a>Monitorowanie postępu szyfrowania systemu operacyjnego
Postęp szyfrowania systemu operacyjnego można monitorować na trzy sposoby:

* Użyj `Get-AzVmDiskEncryptionStatus` polecenia cmdlet i sprawdź pole ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Po osiągnięciu "szyfrowania dysku systemu operacyjnego rozpoczęte", trwa około 40 do 50 minut na maszynie wirtualnej z magazynu premium.

  Z powodu [problemu #388](https://github.com/Azure/WALinuxAgent/issues/388) w WALinuxAgent `OsVolumeEncrypted` i `Unknown` `DataVolumesEncrypted` pojawiają się jak w niektórych dystrybucjach. W przypadku programu WALinuxAgent w wersji 2.1.5 lub nowszej ten problem został rozwiązany automatycznie. Jeśli widzisz `Unknown` w danych wyjściowych, można zweryfikować stan szyfrowania dysku przy użyciu Eksploratora zasobów platformy Azure.

  Przejdź do [Usługi Azure Resource Explorer](https://resources.azure.com/), a następnie rozwiń tę hierarchię w panelu wyboru po lewej stronie:

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

  W widoku Wystąpienia przewiń w dół, aby wyświetlić stan szyfrowania dysków.

  ![Widok wystąpienia maszyny Wirtualnej](./media/disk-encryption/vm-instanceview.png)

* Spójrz na [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Komunikaty z rozszerzenia ADE powinny `[AzureDiskEncryption]`być poprzedzone .

* Zaloguj się do maszyny Wirtualnej za pośrednictwem protokołu SSH i pobierz dziennik rozszerzeń z:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Zaleca się, aby nie logować się do maszyny Wirtualnej, gdy szyfrowanie systemu operacyjnego jest w toku. Kopiowanie dzienników tylko wtedy, gdy pozostałe dwie metody nie powiodły się.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Przygotowanie wstępnie zaszyfrowanego dysku VHD systemu Linux
Przygotowanie do wstępnie zaszyfrowanych dysków VHD może się różnić w zależności od dystrybucji. Dostępne są przykłady dotyczące przygotowywania Ubuntu 16, openSUSE 13.2 i CentOS 7. 

### <a name="ubuntu-16"></a>Ubuntu 16
Skonfiguruj szyfrowanie podczas instalacji dystrybucyjnej, wykonując następujące czynności:

1. Wybierz **pozycję Konfiguruj zaszyfrowane woluminy** podczas partycjonowania dysków.

   ![Ubuntu 16.04 Setup - Konfigurowanie zaszyfrowanych woluminów](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Utwórz oddzielny dysk rozruchowy, który nie może być zaszyfrowany. Zaszyfruj swój dysk główny.

   ![Ubuntu 16.04 Setup - Wybierz urządzenia do szyfrowania](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Podaj hasło. Jest to hasło, które zostały przekazane do magazynu kluczy.

   ![Ubuntu 16.04 Setup - Podaj hasło](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Zakończ partycjonowanie.

   ![Konfiguracja Ubuntu 16.04 - Zakończ partycjonowanie](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Po uruchomieniu maszyny Wirtualnej i są monity o hasło, użyj hasła podane w kroku 3.

   ![Ubuntu 16.04 Setup - Podaj hasło podczas rozruchu](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Przygotuj maszynę wirtualną do przekazania na platformę Azure, korzystając z [tych instrukcji.](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/) Nie uruchamiaj jeszcze ostatniego kroku (anulowanie obsługi administracyjnej maszyny Wirtualnej).

Skonfiguruj szyfrowanie do pracy z platformą Azure, wykonując następujące czynności:

1. Utwórz plik w obszarze /usr/local/sbin/azure_crypt_key.sh, z zawartością w poniższym skrypcie. Należy zwrócić uwagę na KeyFileName, ponieważ jest to nazwa pliku hasła używane przez platformę Azure.

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

2. Zmień konfigur crypt w */etc/crypttab*. Powinny wyglądać następująco:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Dodawanie uprawnień wykonywalnych do skryptu:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Edytuj */etc/initramfs-tools/modules* przez dołączenie linii:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Uruchom, `update-initramfs -u -k all` aby zaktualizować initramfs, aby wejść w `keyscript` życie.

7. Teraz można anulować aprowizję maszyny Wirtualnej.

   ![Ubuntu 16.04 Setup - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Przejdź do następnego kroku i przekaż dysk VHD na platformę Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Aby skonfigurować szyfrowanie podczas instalacji dystrybucyjnej, wykonaj następujące czynności:
1. Podczas partycjonowania dysków wybierz pozycję **Szyfruj grupę woluminów,** a następnie wprowadź hasło. Jest to hasło, które zostanie przesłane do magazynu kluczy.

   ![openSUSE 13.2 Setup — grupa woluminów szyfrowania](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Uruchom maszynę wirtualną przy użyciu hasła.

   ![openSUSE 13.2 Setup - Podaj hasło podczas rozruchu](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Przygotuj maszynę wirtualną do przekazania na platformę Azure, postępując zgodnie z instrukcjami w [sprawie Przygotowywanie maszyny wirtualnej SLES lub openSUSE dla platformy Azure.](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131) Nie uruchamiaj jeszcze ostatniego kroku (anulowanie obsługi administracyjnej maszyny Wirtualnej).

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:
1. Edytuj /etc/dracut.conf i dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Skomentuj te wiersze do końca pliku /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. Dołącz następujący wiersz na początku pliku /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I zmienić wszystkie wystąpienia:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na:
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh i dołącz go do "# Otwórz urządzenie LUKS":

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
5. Uruchom, `/usr/sbin/dracut -f -v` aby zaktualizować initrd.

6. Teraz możesz anulować aprowizję maszyny Wirtualnej i przekazać dysk VHD na platformę Azure.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 i RHEL 8.1

Aby skonfigurować szyfrowanie podczas instalacji dystrybucyjnej, wykonaj następujące czynności:
1. Wybierz **pozycję Szyfruj moje dane** podczas partycjonowania dysków.

   ![CentOS 7 Setup -Miejsce docelowe instalacji](./media/disk-encryption/centos-encrypt-fig1.png)

2. Upewnij się, że **opcja Encrypt** jest wybrana dla partycji głównej.

   ![Konfiguracja CentOS 7 — wybierz szyfrowanie dla partycji głównej](./media/disk-encryption/centos-encrypt-fig2.png)

3. Podaj hasło. Jest to hasło, które zostanie przesłane do magazynu kluczy.

   ![CentOS 7 Setup - podaj hasło](./media/disk-encryption/centos-encrypt-fig3.png)

4. Po uruchomieniu maszyny Wirtualnej i są monity o hasło, użyj hasła podane w kroku 3.

   ![CentOS 7 Setup - Wprowadź hasło podczas uruchamiania](./media/disk-encryption/centos-encrypt-fig4.png)

5. Przygotuj maszynę wirtualną do przekazania na platformę Azure, korzystając z instrukcji "CentOS 7.0+" w [programie Przygotowywanie maszyny wirtualnej opartej na centosie dla platformy Azure.](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70) Nie uruchamiaj jeszcze ostatniego kroku (anulowanie obsługi administracyjnej maszyny Wirtualnej).

6. Teraz możesz anulować aprowizję maszyny Wirtualnej i przekazać dysk VHD na platformę Azure.

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:

1. Edytuj /etc/dracut.conf i dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Skomentuj te wiersze do końca pliku /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. Dołącz następujący wiersz na początku pliku /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I zmienić wszystkie wystąpienia:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh i dołącz następujące elementy po "# Otwórz urządzenie LUKS":
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
5. Uruchom "/usr/sbin/dracut -f -v", aby zaktualizować initrd.

    ![CentOS 7 Setup - uruchom /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Przekazywanie zaszyfrowanego dysku twardego VHD na konto magazynu platformy Azure
Po włączeniu szyfrowania DM-Crypt, lokalny zaszyfrowany dysk wirtualny musi zostać przekazany na twoje konto magazynu.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Przekazywanie klucza tajnego wstępnie zaszyfrowanej maszyny Wirtualnej do magazynu kluczy
Podczas szyfrowania przy użyciu aplikacji usługi Azure AD (poprzednia wersja), klucz tajny szyfrowania dysku, który został wcześniej uzyskany, musi zostać przekazany jako klucz tajny w magazynie kluczy. Magazyn kluczy musi mieć włączone szyfrowanie dysku i uprawnienia dla klienta usługi Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Klucz tajny szyfrowania dysku nie zaszyfrowany za pomocą klucza KEK
Aby skonfigurować klucz tajny w magazynie kluczy, użyj [funkcji Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Hasło jest zakodowane jako ciąg base64, a następnie przekazywane do magazynu kluczy. Ponadto upewnij się, że następujące tagi są ustawiane podczas tworzenia klucza tajnego w magazynie kluczy.

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


Użyj `$secretUrl` w następnym kroku do [podłączenia dysku systemu operacyjnego bez użycia KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Klucz tajny szyfrowania dysku zaszyfrowany za pomocą klucza KEK
Przed przekazaniem klucza tajnego do magazynu kluczy można go opcjonalnie zaszyfrować przy użyciu klucza szyfrowania klucza. Użyj [interfejsu API](https://msdn.microsoft.com/library/azure/dn878066.aspx) zawijania, aby najpierw zaszyfrować klucz tajny przy użyciu klucza szyfrowania klucza. Dane wyjściowe tej operacji zawijania jest base64 adres URL zakodowany ciąg, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) który można następnie przekazać jako klucz tajny przy użyciu polecenia cmdlet.

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

Użyj `$KeyEncryptionKey` `$secretUrl` i w następnym kroku [do podłączenia dysku systemu operacyjnego za pomocą KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Określanie tajnego adresu URL podczas dołączania dysku systemu operacyjnego

###  <a name="without-using-a-kek"></a>Bez użycia KEK
Podczas podłączania dysku systemu operacyjnego należy przejść `$secretUrl`. Adres URL został wygenerowany w sekcji "Klucz tajny szyfrowania dysku nie zaszyfrowany za pomocą KEK".
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
Po podłączeniu dysku systemu `$KeyEncryptionKey` operacyjnego przekaż i `$secretUrl`. Adres URL został wygenerowany w sekcji "Klucz tajny szyfrowania dysku zaszyfrowany za pomocą KEK".
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
