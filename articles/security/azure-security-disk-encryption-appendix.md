---
title: Dodatek — usługa Azure Disk Encryption dla maszyn wirtualnych IaaS | Dokumentacja firmy Microsoft
description: Ten artykuł stanowi dodatek dla programu Microsoft Azure dysku szyfrowanie dla Windows i maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 675ce2de91dd4e53f4945ceef3b174c33be001f6
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649666"
---
# <a name="appendix-for-azure-disk-encryption"></a>Dodatek dla usługi Azure Disk Encryption 

Ten artykuł stanowi dodatek do [usługi Azure Disk Encryption dla maszyn wirtualnych IaaS](azure-security-disk-encryption-overview.md). Upewnij się, że odczytu usługa Azure Disk Encryption dla maszyn wirtualnych IaaS artykułów najpierw po to, aby zrozumieć kontekst. W tym artykule opisano sposób przygotowania zaszyfrowane wstępnie wirtualnych dysków twardych i innych zadań.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-subscription"></a>Nawiązywanie połączenia z subskrypcją
Przed rozpoczęciem należy przejrzeć [wymagania wstępne](azure-security-disk-encryption-prerequisites.md) artykułu. Po spełnieniu wszystkich wymagań wstępnych, połącz się z subskrypcją, uruchamiając następujące polecenia cmdlet:

### <a name="bkmk_ConnectPSH"></a> Połącz z subskrypcją za pomocą programu PowerShell

1. Uruchom sesję programu Azure PowerShell i zaloguj się do konta platformy Azure za pomocą następującego polecenia:

     ```powershell
     Connect-AzAccount 
     ```
2. Jeśli masz wiele subskrypcji i chcesz określić, należy użyć, wpisz następujące polecenie, aby zobaczyć subskrypcje dla konta:
     
     ```powershell
     Get-AzSubscription
     ```
3. Aby określić subskrypcję, której chcesz użyć, wpisz:
 
     ```powershell
      Select-AzSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Aby sprawdzić, czy subskrypcja skonfigurowane jest poprawna, wpisz:
     
     ```powershell
     Get-AzSubscription
     ```
5. Jeśli to konieczne, połączyć się z usługi Azure AD z [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Aby upewnić się, że są zainstalowane polecenia cmdlet usługi Azure Disk Encryption, wpisz:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Przegląd [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps) i [AzureAD](/powershell/module/azuread), jeśli to konieczne.

### <a name="bkmk_ConnectCLI"></a> Nawiązać połączenie z subskrypcji przy użyciu wiersza polecenia platformy Azure

1. Logowanie do platformy Azure za pomocą [az login](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Jeśli chcesz wybrać dzierżawę, zaloguj się w obszarze, należy użyć:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Jeśli masz wiele subskrypcji i chcesz, aby określić jeden z nich, Pobierz listę subskrypcji o [listy kont az](/cli/azure/account#az-account-list) i określ [zestaw konta az](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Sprawdź zainstalowaną wersję.
     
     ```azurecli
        az --version
     ``` 

5. Przegląd [Rozpoczynanie pracy z usługą Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) w razie potrzeby. 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Przykładowe skrypty programu PowerShell dla usługi Azure Disk Encryption 

- **Lista wszystkich zaszyfrowanych maszyn wirtualnych w ramach subskrypcji**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Listę wszystkich dysków szyfrowania wpisów tajnych używany do szyfrowania maszyn wirtualnych w magazynie kluczy** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Za pomocą skryptu programu PowerShell wymagania wstępne dotyczące usługi Azure Disk Encryption
Jeśli już znasz wymagania wstępne dotyczące usługi Azure Disk Encryption, możesz użyć [skrypt programu PowerShell wymagania wstępne dotyczące usługi Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Aby uzyskać przykład użycia ten skrypt programu PowerShell, zobacz [szyfrowania VM Quickstart](quick-encrypt-vm-powershell.md). Komentarze można usunąć z części skryptu, zaczynając od wiersza 211, do szyfrowania wszystkich dysków dla istniejących maszyn wirtualnych w istniejącej grupie zasobów. 

W poniższej tabeli przedstawiono, w której parametry mogą być używane w skrypcie programu PowerShell: 


|Parametr|Opis|Jest wymagany|
|------|------|------|
|$resourceGroupName| Nazwa grupy zasobów, do której należy magazynu kluczy.  Będzie można utworzyć nową grupę zasobów o tej nazwie, jeśli nie istnieje.| True|
|$keyVaultName|Nazwa magazynu kluczy, w których szyfrowania mają być umieszczone klucze. Jeśli nie istnieje, zostanie utworzony nowy magazyn o tej nazwie.| True|
|$location|Lokalizacja magazynu kluczy. Upewnij się, że magazyn kluczy i maszyny wirtualne, które były szyfrowane znajdują się w tej samej lokalizacji. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.|True|
|$subscriptionId|Identyfikator subskrypcji platformy Azure do użycia.  Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.|True|
|$aadAppName|Nazwa aplikacji usługi Azure AD, która będzie służyć do zapisu kluczy tajnych do magazynu kluczy. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli ta aplikacja już istnieje, należy przekazać parametr aadClientSecret do skryptu.|False|
|$aadClientSecret|Klucz tajny klienta aplikacji usługi Azure AD, który został utworzony wcześniej.|False|
|$keyEncryptionKeyName|Nazwa opcjonalny klucz szyfrowania klucza w magazynie KeyVault. Jeśli nie istnieje, zostanie utworzony nowy klucz o tej nazwie.|False|


## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Szyfrowania lub odszyfrowywania maszyn wirtualnych bez aplikacji usługi Azure AD


- [Włącz szyfrowanie dysku dla istniejących lub działających maszyn wirtualnych Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Wyłącz szyfrowanie dysków dla istniejących lub działających maszyn wirtualnych Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Włącz szyfrowanie dysku dla istniejących lub uruchamianie systemu Linux Maszynie wirtualnej IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
  - [Wyłącz szyfrowanie dla uruchomionej maszyny Wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.  

### <a name="encrypt-or-decrypt-vm-scale-sets"></a>Szyfrowania lub odszyfrowywania usługi VM scale sets

- [Włączanie szyfrowania dysków na uruchamianie zestawu skalowania maszyn wirtualnych systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Włączanie szyfrowania dysków na uruchamianie zestawu skalowania maszyn wirtualnych Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Wdrażanie maszyny Wirtualnej zestawu skalowania systemu Linux maszyn wirtualnych z serwera przesiadkowego i włącza szyfrowanie na zestawu skalowania maszyn wirtualnych w systemie Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Wdrażanie maszyny Wirtualnej zestawu skalowania systemu Windows maszyn wirtualnych z serwera przesiadkowego i umożliwia szyfrowanie w usłudze Windows VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Wyłączanie szyfrowania dysków na uruchamianie zestawu skalowania maszyn wirtualnych systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Wyłączanie szyfrowania dysków na uruchamianie zestawu skalowania maszyn wirtualnych Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Szyfrowania lub odszyfrowywania maszyn wirtualnych za pomocą aplikacji usługi Azure AD (poprzedniej wersji) 
 
- [Włącz szyfrowanie dysku dla istniejących lub działających maszyn wirtualnych Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Włącz szyfrowanie dysku dla istniejących lub uruchamianie systemu Linux Maszynie wirtualnej IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Wyłącz szyfrowanie dysków na temat uruchamiania Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Wyłącz szyfrowanie dla uruchomionej maszyny Wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux. 

- [Włączanie szyfrowania dysków na nowej maszynie Wirtualnej Windows IaaS z witryny Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Ten szablon umożliwia utworzenie nowego zaszyfrowanych Windows maszynę Wirtualną, która używa obrazu z galerii systemu Windows Server 2012.

- [Tworzenie nowej zaszyfrowanych Windows IaaS zarządzanych dysków maszyny Wirtualnej na podstawie obrazu z galerii](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Ten szablon umożliwia utworzenie nowej maszyny Wirtualnej Windows zaszyfrowanych za pomocą dysków zarządzanych przy użyciu obrazu z galerii systemu Windows Server 2012.

- [Tworzenie nowego zaszyfrowanego dysku zarządzanego na podstawie zaszyfrowane wstępnie wirtualnego dysku twardego/magazynu obiektów blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Tworzy nowego dysku zarządzanego zaszyfrowanych zaszyfrowane wstępnie wirtualnego dysku twardego i jej odpowiednie ustawienia szyfrowania

- [Włączanie szyfrowania dysków na uruchomionej maszynie Wirtualnej Windows za pomocą odcisku palca certyfikatu klienta usługi Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a> Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie
W kolejnych sekcjach są niezbędne do przygotowania zaszyfrowane wstępnie wirtualnego dysku twardego Windows do wdrożenia jako zaszyfrowanego dysku VHD w modelu IaaS platformy Azure. Skorzystaj z informacji do przygotowania i rozruchu świeże Windows maszyny Wirtualnej (VHD) w usłudze Azure Site Recovery lub na platformie Azure. Aby uzyskać więcej informacji na temat przygotowywania i przekazywanie wirtualnego dysku twardego, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i użyć go do utworzenia nowych maszyn wirtualnych na platformie Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizacja zasad grupy, aby umożliwić bez modułu TPM do ochrony systemu operacyjnego
Ustawienia zasad grupy funkcji BitLocker **szyfrowania dysków funkcją BitLocker**, które znajdują się w obszarze **lokalne zasady komputera** > **konfiguracji komputera**  >  **Szablony administracyjne** > **składników Windows**. Zmień to ustawienie, aby **dyski systemu operacyjnego** > **wymagają dodatkowego uwierzytelniania przy uruchamianiu** > **Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM**, jak pokazano na poniższej ilustracji:

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Zainstaluj składniki funkcji BitLocker
Dla systemu Windows Server 2012 i nowszych należy użyć następującego polecenia:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Dla systemu Windows Server 2008 R2 należy użyć następującego polecenia:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Przygotowanie woluminu systemu operacyjnego do używania funkcji BitLocker przy użyciu `bdehdcfg`
Aby skompresować partycji systemu operacyjnego i przygotowuje komputer do używania funkcji BitLocker, należy wykonać [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) w razie potrzeby:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrona woluminu systemu operacyjnego za pomocą funkcji BitLocker
Użyj [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) polecenie, aby włączyć szyfrowanie na wolumin rozruchowy przy użyciu zewnętrznego ochrony klucza. Również umieścić klucz zewnętrzny (plik .bek), na zewnętrznym dysku lub woluminie. Szyfrowanie jest włączone na wolumin systemowy/rozruchowy po następnym ponownym uruchomieniu.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Przygotowywanie maszyny Wirtualnej przy użyciu oddzielnych danych/zasobów wirtualnego dysku twardego w celu uzyskania klucza zewnętrznego za pomocą funkcji BitLocker.

## <a name="bkmk_LinuxRunning"></a> Szyfrowanie dysku systemu operacyjnego na uruchomionej maszynie Wirtualnej systemu Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Wymagania wstępne dotyczące szyfrowania dysku systemu operacyjnego

* Maszyna wirtualna musi używać dystrybucji, które są zgodne z szyfrowania dysku systemu operacyjnego zgodnie z zaleceniami z [Azure dysku szyfrowania — często zadawane pytania](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 
* Maszyna wirtualna musi zostać utworzona z obrazu z witryny Marketplace usługi Azure Resource Manager.
* Maszyna wirtualna platformy Azure z co najmniej 4 GB pamięci RAM (zalecany rozmiar to 7 GB).
* (Dla systemu RHEL i CentOS) Wyłącz SELinux. Aby wyłączyć SELinux, zobacz "4.4.2. Wyłączanie SELinux"w [Przewodnik administratora i użytkownika SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na maszynie Wirtualnej.
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

4. Aby zaszyfrować systemu operacyjnego, należy określić volumeType jako **wszystkich** lub **OS** po włączeniu szyfrowania.

   > [!NOTE]
   > Wszystkie procesy przestrzeń użytkownika, które nie są uruchomione jako `systemd` usług powinny skasowane z `SIGKILL`. Uruchom ponownie maszynę Wirtualną. Po włączeniu szyfrowania dysku systemu operacyjnego na uruchomionej maszyny Wirtualnej, należy zaplanować przestój maszyny Wirtualnej.

5. Okresowo monitorować postęp szyfrowania zgodnie z instrukcjami podanymi w [następnej sekcji](#monitoring-os-encryption-progress).

6. Po Get AzVmDiskEncryptionStatus zawiera "VMRestartPending", należy ponownie uruchomić maszynę Wirtualną, logując się do niego albo za pomocą witryny portal, programu PowerShell lub interfejsu wiersza polecenia.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Przed ponownym uruchomieniu, zaleca się zapisanie [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) maszyny wirtualnej.

## <a name="monitoring-os-encryption-progress"></a>Monitorowanie postępu szyfrowania systemu operacyjnego
Możesz monitorować postęp szyfrowania systemu operacyjnego na trzy sposoby:

* Użyj `Get-AzVmDiskEncryptionStatus` polecenia cmdlet i sprawdź pole komunikat dotyczący postępu:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Po maszyny Wirtualnej osiągnie "Do szyfrowania dysku systemu operacyjnego", trwa około 40 – 50 minut na usługę Premium storage kopii maszyny Wirtualnej.

  Z powodu [wystawiać #388](https://github.com/Azure/WALinuxAgent/issues/388) w WALinuxAgent, `OsVolumeEncrypted` i `DataVolumesEncrypted` są wyświetlane jako `Unknown` w niektórych dystrybucjach. Za pomocą WALinuxAgent w wersji 2.1.5 i nowszy, ten problem jest rozwiązany automatycznie. Jeśli widzisz `Unknown` w danych wyjściowych, można sprawdzić stanu szyfrowania dysków za pomocą Eksploratora zasobów Azure.

  Przejdź do [Eksploratora zasobów Azure](https://resources.azure.com/), a następnie rozwiń tę hierarchię w panelu wyboru po lewej stronie:

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

  ![Widok wystąpienia maszyny Wirtualnej](./media/azure-security-disk-encryption/vm-instanceview.png)

* Przyjrzyj się [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Komunikaty z rozszerzeniem ADE powinien być poprzedzony `[AzureDiskEncryption]`.

* Zaloguj się do maszyny Wirtualnej za pośrednictwem protokołu SSH i uzyskać dziennika rozszerzeń, od:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Firma Microsoft zaleca się czy nie logowania do maszyny Wirtualnej w trakcie szyfrowania systemu operacyjnego. Skopiuj dzienniki tylko wtedy, gdy te dwie metody nie powiodło się.

## <a name="bkmk_preLinux"></a> Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie
Przygotowanie do zaszyfrowane wstępnie wirtualne dyski twarde mogą się różnić w zależności od dystrybucji. Przykłady na temat przygotowywania [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE), i [CentOS 7](#bkmk_CentOS) są dostępne. 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Konfigurowanie szyfrowania podczas instalacji dystrybucji, wykonując następujące czynności:

1. Wybierz **Konfigurowanie woluminów zaszyfrowanych** po partycji dysków.

   ![Ubuntu 16.04 Konfigurowanie — Konfigurowanie zaszyfrowanych woluminów](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Tworzenie dysku rozruchowego oddzielne nie muszą być szyfrowane. Szyfrowanie dysku głównego.

   ![Instalator systemu Ubuntu 16.04 - wybierz urządzenia do zaszyfrowania](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Podaj hasło. Jest to hasło, który został przekazany do magazynu kluczy.

   ![Ubuntu 16.04 Konfigurowanie — Podaj hasło](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Zakończ partycjonowania.

   ![Ubuntu 16.04 Konfigurowanie — Zakończ partycjonowania](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Podczas rozruchu maszyny Wirtualnej i monit o podanie hasła, należy użyć hasło, które podano w kroku 3.

   ![Ubuntu 16.04 Konfigurowanie — Podaj hasło podczas rozruchu](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Przygotowywanie maszyny Wirtualnej do przekazywania na platformie Azure przy użyciu [w instrukcjach](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nie uruchamiaj w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

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

2. Zmienianie konfiguracji crypt w */etc/crypttab*. Powinny wyglądać następująco:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Jeśli edytujesz *azure_crypt_key.sh* w Windows i skopiowany na systemie Linux Uruchom `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Dodaj uprawnienia pliku wykonywalnego do skryptu:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Edytuj */etc/initramfs-tools/modules* , dodając wiersze:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Uruchom `update-initramfs -u -k all` initramfs, aby zaktualizować `keyscript` zaczęły obowiązywać.

7. Teraz można anulować aprowizację maszyny Wirtualnej.

   ![Instalator systemu Ubuntu 16.04 - initramfs aktualizacji](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Przejdź do kolejnego etapu i przekazywanie wirtualnego dysku twardego na platformie Azure.

### <a name="bkmk_openSUSE"></a>  openSUSE 13.2
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Podczas dzielenia dysków na partycje wybrać **szyfrowanie woluminu grupy**, a następnie wprowadź hasło. To hasło, które należy przekazać do magazynu kluczy.

   ![openSUSE 13.2 instalacji — grupa szyfrowania woluminów](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Rozruch maszyny Wirtualnej, przy użyciu hasła.

   ![openSUSE 13.2 Konfigurowanie — Podaj hasło podczas rozruchu](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Przygotowywanie maszyny Wirtualnej do przekazywania na platformie Azure, postępując zgodnie z instrukcjami wyświetlanymi w [przygotowywanie maszyny wirtualnej systemu SLES lub openSUSE dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nie uruchamiaj w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

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
5. Uruchom `/usr/sbin/dracut -f -v` można zaktualizować initrd.

6. Możesz teraz anulować aprowizację maszyny Wirtualnej i przekazywanie wirtualnego dysku twardego na platformie Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Wybierz **szyfrowanie danych** po partycji dysków.

   ![Konfigurowanie centOS 7 - miejsce docelowe instalacji](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Upewnij się, że **Szyfruj** jest zaznaczone dla partycji katalogu głównego.

   ![Konfigurowanie centOS 7 - wybierz szyfrowania dla partycji katalogu głównego](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Podaj hasło. Jest to hasło, które należy przekazać do magazynu kluczy.

   ![Instalator centOS 7 - Podaj hasło](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Podczas rozruchu maszyny Wirtualnej i monit o podanie hasła, należy użyć hasło, które podano w kroku 3.

   ![CentOS 7 Konfigurowanie — wprowadź hasło w rozruchu](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Przygotowywanie maszyny Wirtualnej do przekazywania na platformie Azure przy użyciu instrukcji "CentOS 7.0 +" w [przygotowywanie maszyny wirtualnej z systemem CentOS dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nie uruchamiaj w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

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

![CentOS 7 - /usr/sbin/dracut -f - v działanie Instalatora](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Przekazywanie zaszyfrowanego dysku VHD do konta usługi Azure storage
Po włączeniu funkcji BitLocker szyfrowania lub szyfrowania DM-Crypt lokalny wirtualny dysk twardy zaszyfrowanych wymaga do przekazania do swojego konta magazynu.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Przekaż klucz tajny dla wstępnie zaszyfrowanej maszyny Wirtualnej do magazynu kluczy
W przypadku szyfrowania przy użyciu aplikacji usługi Azure AD (poprzedniej wersji), należy przekazać klucz tajny szyfrowania dysku, który został uzyskany wcześniej w formie wpisu tajnego w magazynie kluczy. Magazyn kluczy wymaga szyfrowania dysku i włączonymi uprawnieniami dla klienta usługi Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Wpis tajny szyfrowania dysku nie jest szyfrowana za pomocą klucza KEK
Aby skonfigurować wpisu tajnego w magazynie kluczy, użyj [AzKeyVaultSecret zestaw](/powershell/module/az.keyvault/set-azkeyvaultsecret). Jeśli masz maszynę wirtualną Windows, plik klucza szyfrowania bloków jest zakodowany jako ciąg w formacie base64 i następnie przekazywane do usługi key vault przy użyciu `Set-AzKeyVaultSecret` polecenia cmdlet. Dla systemu Linux hasło jest zakodowany jako ciąg w formacie base64, a następnie przekazywane do magazynu kluczy. Ponadto upewnij się, że następujące znaczniki są ustawione podczas tworzenia klucza tajnego w magazynie kluczy.

#### <a name="windows-bek-file"></a>Plik klucza szyfrowania bloków Windows
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


Użyj `$secretUrl` w następnym kroku zapoznać [dołączenie dysku systemu operacyjnego bez użycia klucza KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Wpis tajny szyfrowania dysku zaszyfrowane przy użyciu klucza KEK
Przed przekazaniem wpisu tajnego do magazynu kluczy, można opcjonalnie zaszyfrować przy użyciu klucza szyfrowania. Użyj zawijania [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) najpierw szyfrowania klucza tajnego przy użyciu klucza szyfrowania. Dane wyjściowe tej operacji zawijania to ciąg zakodowany w adresie URL base64, możesz następnie przekazać jako wpis tajny przy użyciu [ `Set-AzKeyVaultSecret` ](/powershell/module/az.keyvault/set-azkeyvaultsecret) polecenia cmdlet.

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

Użyj `$KeyEncryptionKey` i `$secretUrl` w następnym kroku zapoznać [dołączenie dysku systemu operacyjnego przy użyciu klucza KEK](#bkmk_URLKEK).

##  <a name="bkmk_SecretURL"></a> Określ adres URL wpisu tajnego po dołączeniu dysku systemu operacyjnego

###  <a name="bkmk_URLnoKEK"></a>Bez użycia klucza KEK
Gdy dołączasz dysku systemu operacyjnego, musisz przekazać `$secretUrl`. Adres URL został wygenerowany w sekcji "nie jest szyfrowana za pomocą klucza KEK tajny szyfrowania dysku".
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
### <a name="bkmk_URLKEK"></a>Za pomocą klucza KEK
Po dołączeniu dysku systemu operacyjnego, należy przekazać `$KeyEncryptionKey` i `$secretUrl`. Adres URL został wygenerowany w sekcji "Wpis tajny szyfrowania dysku zaszyfrowane przy użyciu klucza KEK".
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
