---
title: Dodatek-Azure Disk Encryption dla maszyn wirtualnych IaaS | Microsoft Docs
description: Ten artykuł zawiera dodatek do Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemami Windows i Linux IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 4c065e1970a01f7e3737f8bd99672c84f2019bfe
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71822339"
---
# <a name="appendix-for-azure-disk-encryption"></a>Dodatek dla Azure Disk Encryption 

Ten artykuł jest dodatkiem do [Azure Disk Encryption dla maszyn wirtualnych IaaS](azure-security-disk-encryption-overview.md). Najpierw zapoznaj się z artykułami Azure Disk Encryption dla maszyn wirtualnych IaaS, aby zrozumieć kontekst. W tym artykule opisano sposób przygotowywania wstępnie zaszyfrowanych dysków VHD i innych zadań.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-subscription"></a>Nawiązywanie połączenia z subskrypcją
Przed rozpoczęciem należy zapoznać się z artykułem dotyczącym [wymagań wstępnych](azure-security-disk-encryption-prerequisites.md) . Po spełnieniu wszystkich wymagań wstępnych Nawiąż połączenie z subskrypcją, uruchamiając następujące polecenia cmdlet:

### <a name="bkmk_ConnectPSH"></a>Nawiązywanie połączenia z subskrypcją za pomocą programu PowerShell

1. Rozpocznij sesję Azure PowerShell i zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

     ```powershell
     Connect-AzAccount 
     ```
2. Jeśli masz wiele subskrypcji i chcesz określić ją do użycia, wpisz następujące polecenie, aby wyświetlić subskrypcje dla konta:
     
     ```powershell
     Get-AzSubscription
     ```
3. Aby określić subskrypcję, której chcesz użyć, wpisz:
 
     ```powershell
      Select-AzSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Aby sprawdzić, czy subskrypcja jest poprawna, wpisz:
     
     ```powershell
     Get-AzSubscription
     ```
5. W razie potrzeby połącz się z usługą Azure AD za pomocą programu [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Aby potwierdzić, że Azure Disk Encryption są zainstalowane polecenia cmdlet, wpisz:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. W razie konieczności zapoznaj [się z tematem wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps) i [AzureAD](/powershell/module/azuread).

### <a name="bkmk_ConnectCLI"></a>Nawiązywanie połączenia z subskrypcją za pomocą interfejsu wiersza polecenia platformy Azure

1. Zaloguj się do platformy Azure przy użyciu [AZ login](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Jeśli chcesz wybrać dzierżawcę do logowania, użyj:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Jeśli masz wiele subskrypcji i chcesz określić konkretną, Pobierz listę subskrypcji za pomocą opcji [AZ Account List](/cli/azure/account#az-account-list) i określ polecenie [AZ Account Set](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Sprawdź zainstalowaną wersję.
     
     ```azurecli
        az --version
     ``` 

5. W razie konieczności Przejrzyj [wprowadzenie do interfejsu wiersza polecenia platformy Azure 2,0](/cli/azure/get-started-with-azure-cli) . 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Przykładowe skrypty programu PowerShell dla Azure Disk Encryption 

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

### <a name="bkmk_prereq-script"></a>Korzystanie z skryptu programu PowerShell dla Azure Disk Encryption wymagań wstępnych
Jeśli masz już doświadczenie z wymaganiami wstępnymi dotyczącymi Azure Disk Encryption, możesz użyć [skryptu programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Przykład korzystania z tego skryptu programu PowerShell można znaleźć w temacie [szyfrowanie maszyny wirtualnej — szybki start](../virtual-machines/linux/disk-encryption-powershell-quickstart.md). Możesz usunąć komentarze z sekcji skryptu, zaczynając od wiersza 211, aby zaszyfrować wszystkie dyski dla istniejących maszyn wirtualnych w istniejącej grupie zasobów. 

W poniższej tabeli przedstawiono parametry, których można użyć w skrypcie programu PowerShell: 


|Parametr|Opis|Jest obowiązkowy|
|------|------|------|
|$resourceGroupName| Nazwa grupy zasobów, do której należy Magazyn kluczy.  Jeśli jeszcze nie istnieje, zostanie utworzona nowa grupa zasobów o tej nazwie.| True|
|$keyVaultName|Nazwa magazynu kluczy, w którym mają zostać umieszczone klucze szyfrowania. Nowy magazyn o tej nazwie zostanie utworzony, jeśli taki nie istnieje.| True|
|$location|Lokalizacja magazynu kluczy. Upewnij się, że magazyn kluczy i maszyny wirtualne, które mają być szyfrowane, znajdują się w tej samej lokalizacji. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.|True|
|$subscriptionId|Identyfikator subskrypcji platformy Azure, która ma zostać użyta.  Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.|True|
|$aadAppName|Nazwa aplikacji usługi Azure AD, która będzie używana do zapisywania wpisów tajnych w magazynie kluczy. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli ta aplikacja już istnieje, Przekaż parametr aadClientSecret do skryptu.|False|
|$aadClientSecret|Wpis tajny klienta aplikacji usługi Azure AD, który został utworzony wcześniej.|False|
|$keyEncryptionKeyName|Nazwa opcjonalnego klucza szyfrowania klucza w magazynie kluczy. Nowy klucz o tej nazwie zostanie utworzony, jeśli taki nie istnieje.|False|


## <a name="resource-manager-templates"></a>Szablony Menedżera zasobów

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych bez aplikacji usługi Azure AD


- [Włączanie szyfrowania dysków na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Wyłączanie szyfrowania dysków na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem IaaS Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
  - [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux.  

### <a name="encrypt-or-decrypt-virtual-machine-scale-sets"></a>Szyfrowanie lub odszyfrowywanie zestawów skalowania maszyn wirtualnych

- [Włącz szyfrowanie dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Włącz szyfrowanie dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Wdrażanie zestawu skalowania maszyn wirtualnych z systemem Linux przy użyciu serwera przesiadkowego i Włączanie szyfrowania w systemie Linux VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Wdrażanie zestawu skalowania maszyn wirtualnych z systemem Windows za pomocą serwera przesiadkowego i Włączanie szyfrowania w systemie Windows VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Wyłącz szyfrowanie dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Wyłączanie szyfrowania dysków w uruchomionym zestawie skalowania maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych za pomocą aplikacji usługi Azure AD (poprzednia wersja) 
 
- [Włączanie szyfrowania dysków na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Włączanie szyfrowania dysków na istniejącej lub uruchomionej maszynie wirtualnej z systemem IaaS Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Wyłącz szyfrowanie dysków na komputerze z systemem Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Wyłączanie szyfrowania na działającej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Wyłączenie szyfrowania jest dozwolone tylko dla woluminów danych dla maszyn wirtualnych z systemem Linux. 

- [Włącz szyfrowanie dysków na nowej maszynie wirtualnej z systemem IaaS z witryny Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Ten szablon służy do tworzenia nowej zaszyfrowanej maszyny wirtualnej z systemem Windows, która używa obrazu z galerii systemu Windows Server 2012.

- [Utwórz nową zaszyfrowaną maszynę wirtualną z dyskiem zarządzanym systemu Windows IaaS z obrazu galerii](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Ten szablon służy do tworzenia nowej zaszyfrowanej maszyny wirtualnej z systemem Windows z dyskami zarządzanymi przy użyciu obrazu z galerii systemu Windows Server 2012.

- [Utwórz nowy zaszyfrowany dysk zarządzany na podstawie wstępnie zaszyfrowanego obiektu BLOB dysku VHD/magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Tworzy nowy zaszyfrowany dysk zarządzany przy użyciu wstępnie zaszyfrowanego wirtualnego dysku twardego i odpowiednich ustawień szyfrowania

- [Włączanie szyfrowania dysków na uruchomionej maszynie wirtualnej z systemem Windows przy użyciu odcisku palca certyfikatu klienta usługi Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a>Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows
Poniższe sekcje są niezbędne do przygotowania wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows na potrzeby wdrożenia jako zaszyfrowanego wirtualnego dysku twardego w usłudze Azure IaaS. Korzystając z tych informacji, przygotuj i uruchom nową maszynę wirtualną systemu Windows (VHD) na Azure Site Recovery lub na platformie Azure. Aby uzyskać więcej informacji na temat przygotowywania i przekazywania dysku VHD, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizowanie zasad grupy, aby umożliwić ochronę systemu operacyjnego bez modułu TPM
Skonfiguruj ustawienia zasady grupy funkcji BitLocker **szyfrowanie dysków funkcją BitLocker**, które znajdują się w obszarze **zasady komputera lokalnego** > **konfiguracja komputera** > **Szablony administracyjne** > **Windows Składniki**. Zmień to ustawienie na **dyski systemu operacyjnego** > **Wymagaj dodatkowego uwierzytelniania przy uruchamianiu** > **Zezwalaj na używanie funkcji BitLocker bez zgodnego modułu TPM**, jak pokazano na poniższym rysunku:

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Zainstaluj składniki funkcji funkcji BitLocker
W przypadku systemu Windows Server 2012 i nowszych Użyj następującego polecenia:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

W przypadku systemu Windows Server 2008 R2 Użyj następującego polecenia:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Przygotowanie woluminu systemu operacyjnego na potrzeby funkcji BitLocker przy użyciu `bdehdcfg`
Aby skompresować partycję systemu operacyjnego i przygotować maszynę do obsługi funkcji BitLocker, należy w razie potrzeby wykonać [BdeHdCfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) :

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrona woluminu systemu operacyjnego za pomocą funkcji BitLocker
Użyj polecenia [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) , aby włączyć szyfrowanie na woluminie rozruchowym przy użyciu funkcji ochrony klucza zewnętrznego. Umieść również klucz zewnętrzny (plik. klucz szyfrowania bloków) na dysku zewnętrznym lub woluminie. Szyfrowanie jest włączone w woluminie systemowym/rozruchowym po następnym ponownym uruchomieniu.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Przygotuj maszynę wirtualną z osobnym dyskiem VHD danych/zasobów na potrzeby uzyskiwania klucza zewnętrznego przy użyciu funkcji BitLocker.

## <a name="bkmk_LinuxRunning"></a>Szyfrowanie dysku systemu operacyjnego na działającej maszynie wirtualnej z systemem Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Wymagania wstępne dotyczące szyfrowania dysków systemu operacyjnego

* Maszyna wirtualna musi używać dystrybucji zgodnej z szyfrowaniem dysków systemu operacyjnego, która jest wymieniona w [Azure Disk Encryption obsługiwanych systemach operacyjnych: Linux](azure-security-disk-encryption-prerequisites.md#linux) 
* Maszyna wirtualna musi zostać utworzona na podstawie obrazu z witryny Marketplace w Azure Resource Manager.
* Maszyna wirtualna platformy Azure z co najmniej 4 GB pamięci RAM (zalecany rozmiar to 7 GB).
* (Dla RHEL i CentOS) Wyłącz SELinux. Aby wyłączyć SELinux, zobacz "4.4.2. Wyłączenie SELinux "w [podręczniku użytkownika SELinux i administratora](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na maszynie wirtualnej.
* Po wyłączeniu SELinux należy ponownie uruchomić maszynę wirtualną co najmniej raz.

### <a name="steps"></a>Kroki
1. Utwórz maszynę wirtualną przy użyciu jednej z określonych wcześniej dystrybucji.

   W przypadku CentOS 7,2 szyfrowanie dysków systemu operacyjnego jest obsługiwane za pośrednictwem obrazu specjalnego. Aby użyć tego obrazu, należy określić "7.2 n" jako jednostkę SKU podczas tworzenia maszyny wirtualnej:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Skonfiguruj maszynę wirtualną zgodnie z potrzebami. Jeśli chcesz zaszyfrować wszystkie dyski z systemem (OS + Data), dyski danych muszą być określone i instalowalne z katalogu/etc/fstab.

   > [!NOTE]
   > Użyj identyfikatora UUID =... Aby określić dyski danych w/etc/fstab zamiast określać nazwę urządzenia blokowego (na przykład/dev/sdb1). Podczas szyfrowania kolejność dysków zmienia się na maszynie wirtualnej. Jeśli maszyna wirtualna korzysta z określonej kolejności blokowania urządzeń, nie będzie można jej zainstalować po zaszyfrowaniu.

3. Wyloguj się z sesji SSH.

4. Aby zaszyfrować system operacyjny, określ wartość volumetype jako **All** lub **OS** po włączeniu szyfrowania.

   > [!NOTE]
   > Wszystkie procesy obszaru użytkownika, które nie są uruchomione jako usługi `systemd`, powinny być zabite z `SIGKILL`. Uruchom ponownie maszynę wirtualną. Po włączeniu szyfrowania dysków systemu operacyjnego na uruchomionej maszynie wirtualnej Zaplanuj przestoje maszyny wirtualnej.

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
Postęp szyfrowania systemu operacyjnego można monitorować na trzy sposoby:

* Użyj polecenia cmdlet `Get-AzVmDiskEncryptionStatus` i sprawdź pole komunikat dotyczący postępu:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Po osiągnięciu przez maszynę wirtualną "rozpoczęto szyfrowanie dysku systemu operacyjnego, zajmie od 40 do 50 minut na maszynie wirtualnej z magazynem w warstwie Premium.

  Z powodu [problemu #388](https://github.com/Azure/WALinuxAgent/issues/388) w WALinuxAgent, `OsVolumeEncrypted` i `DataVolumesEncrypted` są wyświetlane jako `Unknown` w niektórych dystrybucjach. W programie WALinuxAgent w wersji 2.1.5 lub nowszej ten problem został rozwiązany automatycznie. Jeśli widzisz `Unknown` w danych wyjściowych, możesz zweryfikować stan szyfrowania dysku przy użyciu Azure Resource Explorer.

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

  ![Widok wystąpienia maszyny wirtualnej](./media/azure-security-disk-encryption/vm-instanceview.png)

* Spójrz na [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Komunikaty z rozszerzenia ADE powinny być poprzedzone prefiksem `[AzureDiskEncryption]`.

* Zaloguj się do maszyny wirtualnej za pośrednictwem protokołu SSH i Pobierz dziennik rozszerzeń z:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Zalecamy, aby nie logować się do maszyny wirtualnej, podczas gdy szyfrowanie systemu operacyjnego jest w toku. Skopiuj dzienniki tylko wtedy, gdy inne dwie metody zakończyły się niepowodzeniem.

## <a name="bkmk_preLinux"></a>Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux
Przygotowanie wstępnie zaszyfrowanych dysków VHD może się różnić w zależności od rozkładu. Przykłady dotyczące przygotowywania [Ubuntu 16](#bkmk_Ubuntu), [OpenSUSE 13,2](#bkmk_openSUSE)i [CentOS 7](#bkmk_CentOS) są dostępne. 

### <a name="bkmk_Ubuntu"></a>Ubuntu 16
Skonfiguruj szyfrowanie podczas instalacji dystrybucji, wykonując następujące czynności:

1. Wybierz opcję **Konfiguruj woluminy szyfrowane** podczas partycjonowania dysków.

   ![Konfiguracja Ubuntu 16,04 — Konfigurowanie szyfrowanych woluminów](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Utwórz oddzielny dysk rozruchowy, który nie może być zaszyfrowany. Zaszyfruj dysk główny.

   ![Konfiguracja programu Ubuntu 16,04 — wybierz urządzenia do zaszyfrowania](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Podaj hasło. Jest to hasło przekazane do magazynu kluczy.

   ![Instalator Ubuntu 16,04 — Podaj hasło](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Zakończenie partycjonowania.

   ![Konfiguracja Ubuntu 16,04 — zakończenie partycjonowania](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Podczas uruchamiania maszyny wirtualnej i monitowania o podanie hasła należy użyć hasła podanego w kroku 3.

   ![Instalator Ubuntu 16,04 — Podaj hasło przy rozruchu](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Przygotuj maszynę wirtualną do przekazania do platformy Azure, korzystając z [tych instrukcji](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nie uruchamiaj jeszcze ostatniego kroku (anulowanie aprowizacji maszyny wirtualnej).

Skonfiguruj szyfrowanie do pracy z platformą Azure, wykonując następujące czynności:

1. Utwórz plik w obszarze/usr/local/sbin/azure_crypt_key.sh z zawartością w poniższym skrypcie. Zwróć uwagę na nazwę pliku, ponieważ jest to nazwa pliku hasła używanego przez platformę Azure.

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

3. Jeśli edytujesz *azure_crypt_key. sh* w systemie Windows i skopiujesz go do systemu Linux, uruchom `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Dodaj uprawnienia do pliku wykonywalnego do skryptu:
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
6. Uruchom `update-initramfs -u -k all`, aby zaktualizować initramfs, aby zastosować `keyscript`.

7. Teraz można anulować obsługę administracyjną maszyny wirtualnej.

   ![Konfiguracja Ubuntu 16,04-Update-initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Przejdź do następnego kroku i przekaż dysk VHD na platformę Azure.

### <a name="bkmk_openSUSE"></a>openSUSE 13,2
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Podczas partycjonowania dysków wybierz pozycję **Szyfruj grupę woluminów**, a następnie wprowadź hasło. Jest to hasło, które zostanie przekazane do magazynu kluczy.

   ![Konfiguracja openSUSE 13,2 — Zaszyfruj grupę woluminów](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Wykonaj rozruch maszyny wirtualnej przy użyciu hasła.

   ![Instalator openSUSE 13,2 — Podaj hasło przy rozruchu](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Przygotuj MASZYNę wirtualną do przekazania na platformę Azure, postępując zgodnie z instrukcjami w temacie [przygotowanie maszyny wirtualnej SLES lub openSUSE dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nie uruchamiaj jeszcze ostatniego kroku (anulowanie aprowizacji maszyny wirtualnej).

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:
1. Edytuj/etc/Dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Dodaj komentarz do tych wierszy na końcu pliku/usr/lib/Dracut/modules.d/90crypt/module-Setup.sh:
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

3. Dołącz następujący wiersz na początku pliku/usr/lib/Dracut/modules.d/90crypt/Parse-Crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I Zmień wszystkie wystąpienia:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na:
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh i dołącz go do "# Open LUKS Device":

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

6. Teraz możesz anulować obsługę administracyjną maszyny wirtualnej i przekazać dysk VHD na platformę Azure.

### <a name="bkmk_CentOS"></a>CentOS 7
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Wybierz opcję **Szyfruj moje dane** podczas partycjonowania dysków.

   ![Instalacja CentOS 7 — miejsce docelowe instalacji](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Upewnij się, że wybrano **szyfrowanie** dla partycji głównej.

   ![Instalator programu CentOS 7 — wybierz pozycję Szyfruj dla partycji głównej](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Podaj hasło. Jest to hasło, które zostanie przekazane do magazynu kluczy.

   ![Instalator CentOS 7 — Podaj hasło](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Podczas uruchamiania maszyny wirtualnej i monitowania o podanie hasła należy użyć hasła podanego w kroku 3.

   ![Instalator CentOS 7 — Wprowadź hasło w rozruchu](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Przygotuj maszynę wirtualną do przekazania na platformę Azure, używając instrukcji "CentOS 7.0 +" w temacie [przygotowanie maszyny wirtualnej opartej na CentOS dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nie uruchamiaj jeszcze ostatniego kroku (anulowanie aprowizacji maszyny wirtualnej).

6. Teraz możesz anulować obsługę administracyjną maszyny wirtualnej i przekazać dysk VHD na platformę Azure.

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:

1. Edytuj/etc/Dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Dodaj komentarz do tych wierszy na końcu pliku/usr/lib/Dracut/modules.d/90crypt/module-Setup.sh:
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

3. Dołącz następujący wiersz na początku pliku/usr/lib/Dracut/modules.d/90crypt/Parse-Crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   I Zmień wszystkie wystąpienia:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na
   ```bash
    if [ 1 ]; then
   ```
4. Edytuj/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh i Dołącz następujące elementy po "# Open LUKS Device":
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
5. Uruchom "/usr/sbin/Dracut-f-v", aby zaktualizować oryginalnych initrd.

![Konfiguracja programu CentOS 7 — Uruchamianie/usr/sbin/Dracut-f-v](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a>Przekazywanie zaszyfrowanego wirtualnego dysku twardego do konta usługi Azure Storage
Po włączeniu szyfrowania BitLocker lub szyfrowania DM-Crypt należy przekazać lokalny zaszyfrowany wirtualny dysk twardy do konta magazynu.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a>Przekaż klucz tajny wstępnie zaszyfrowanej maszyny wirtualnej do magazynu kluczy
Podczas szyfrowania przy użyciu aplikacji usługi Azure AD (poprzednia wersja) klucz tajny szyfrowania dysku, który uzyskano wcześniej, musi zostać przekazany jako wpis tajny w magazynie kluczy. Magazyn kluczy musi mieć włączone szyfrowanie dysków i uprawnienia dla klienta usługi Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a>Wpis tajny szyfrowania dysku nie został zaszyfrowany za pomocą elementu KEK
Aby skonfigurować wpis tajny w magazynie kluczy, użyj polecenie [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Jeśli masz maszynę wirtualną z systemem Windows, plik klucz szyfrowania bloków zostanie zakodowany jako ciąg Base64, a następnie przekazany do magazynu kluczy za pomocą polecenia cmdlet `Set-AzKeyVaultSecret`. W przypadku systemu Linux hasło jest kodowane jako ciąg Base64, a następnie przekazywane do magazynu kluczy. Ponadto należy się upewnić, że następujące znaczniki są ustawione podczas tworzenia wpisu tajnego w magazynie kluczy.

#### <a name="windows-bek-file"></a>Plik klucz szyfrowania bloków systemu Windows
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
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


Użyj `$secretUrl` w następnym kroku, aby [dołączyć dysk systemu operacyjnego bez używania KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a>Wpis tajny szyfrowania dysku zaszyfrowany za pomocą KEK
Przed przekazaniem wpisu tajnego do magazynu kluczy można opcjonalnie go zaszyfrować przy użyciu klucza szyfrowania klucza. Użyj [interfejsu API](https://msdn.microsoft.com/library/azure/dn878066.aspx) Otocz, aby najpierw zaszyfrować klucz tajny przy użyciu klucza szyfrowania klucza. Dane wyjściowe tej operacji zawijania to ciąg zakodowany przez adres URL w formacie Base64, który można następnie przekazać jako wpis tajny za pomocą polecenia cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

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

Użyj `$KeyEncryptionKey` i `$secretUrl` w następnym kroku, aby [dołączyć dysk systemu operacyjnego za pomocą KEK](#bkmk_URLKEK).

##  <a name="bkmk_SecretURL"></a>Określ tajny adres URL podczas dołączania dysku systemu operacyjnego

###  <a name="bkmk_URLnoKEK"></a>Bez użycia KEK
Podczas dołączania dysku systemu operacyjnego należy przekazać `$secretUrl`. Adres URL został wygenerowany w sekcji "wpis tajny dysku bez szyfrowania za pomocą KEK".
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
### <a name="bkmk_URLKEK"></a>Korzystanie z KEK
Po dołączeniu dysku systemu operacyjnego należy przekazać `$KeyEncryptionKey` i `$secretUrl`. Ten adres URL został wygenerowany w sekcji "klucz tajny szyfrowania dysku zaszyfrowany za pomocą KEK".
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
