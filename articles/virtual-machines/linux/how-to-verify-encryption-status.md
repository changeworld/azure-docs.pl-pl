---
title: Jak zweryfikować stan szyfrowania dla systemu Linux
description: Ten artykuł zawiera instrukcje dotyczące sprawdzania stanu szyfrowania z poziomu platformy i systemu operacyjnego.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123424"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Jak zweryfikować stan szyfrowania dla systemu Linux 

**Ten scenariusz dotyczy rozszerzeń dwuprzebiegowych i jednoprzebiegowych ADE.**  
Ten zakres dokumentu służy do sprawdzania poprawności stanu szyfrowania maszyny wirtualnej przy użyciu różnych metod.

### <a name="environment"></a>Środowisko

- Dystrybucje Linuksa

### <a name="procedure"></a>Procedura

Maszyna wirtualna została zaszyfrowana przy użyciu dwuprzebiegowego lub jednoprzebiegowego.

Stan szyfrowania można sprawdzić podczas lub po szyfrowaniu przy użyciu różnych metod.

>[!NOTE] 
>Używamy zmiennych w całym dokumencie, odpowiednio zastąp wartości.

### <a name="verification"></a>Weryfikacja

Weryfikację można przeprowadzić z portalu, programu PowerShell, interfejsu wiersza polecenia AZ i lub po stronie systemu operacyjnego maszyny Wirtualnej. 

Tę weryfikację można przeprowadzić, sprawdzając dyski podłączone do określonej maszyny Wirtualnej. 

Lub przez zapytanie ustawień szyfrowania na każdym dysku, czy dysk jest dołączony lub nieprzyłączony.

Poniżej różne metody sprawdzania poprawności:

## <a name="using-the-portal"></a>Korzystanie z portalu

Sprawdź poprawność stanu szyfrowania, sprawdzając sekcję rozszerzeń w witrynie Azure portal.

W sekcji **Rozszerzenia** zobaczysz rozszerzenie ADE na liście. 

Kliknij go i spójrz na komunikat o **stanie**, wskaże aktualny stan szyfrowania:

![Numer czeku portalu 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na liście rozszerzeń zobaczysz odpowiednią wersję rozszerzenia ADE. Wersja 0.x odpowiada ADE Dual-Pass, a wersja 1.x odpowiada ADE Single-pass.

Możesz uzyskać więcej szczegółów klikając na rozszerzenie, a następnie na *Zobacz szczegółowy stan*.

Zobaczysz bardziej szczegółowy stan procesu szyfrowania w formacie json:

![Numer czeku portalu 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Numer czeku portalu 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Innym sposobem sprawdzania poprawności stanu szyfrowania jest przyjrzenie się sekcji **Dyski.**

![Numer czeku portalu 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Ten stan oznacza, że dyski mają opieczętowane ustawienia szyfrowania, ale nie, że były one rzeczywiście zaszyfrowane na poziomie systemu operacyjnego. Zgodnie z projektem dyski są najpierw ostemplowane, a później zaszyfrowane. Jeśli proces szyfrowania zakończy się niepowodzeniem, dyski mogą zakończyć się opieczętowane, ale nie zaszyfrowane. Aby potwierdzić, czy dyski są naprawdę zaszyfrowane, można dokładnie sprawdzić szyfrowanie każdego dysku na poziomie systemu operacyjnego.

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

**Ogólny** stan szyfrowania zaszyfrowanej maszyny Wirtualnej można sprawdzić, czy można sprawdzić poprawność następującego poleceń programu PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![sprawdzanie programu PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Ustawienia szyfrowania z każdego dysku można przechwycić za pomocą następujących poleceń programu PowerShell:

### <a name="single-pass"></a>Jednoprzebiegowy
Jeśli jednoprzebiegowe, ustawienia szyfrowania są stemplowane na każdym z dysków (OS i Dane), można przechwycić ustawienia szyfrowania dysku systemu operacyjnego w jednym przebiegu w następujący sposób:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Weryfikowanie pojedynczego przebiegu systemu operacyjnego 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Jeśli na dysku nie ma wybitych ustawień szyfrowania, dane wyjściowe będą puste, jak pokazano poniżej:

![Ustawienia szyfrowania systemu operacyjnego 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Ustawienia szyfrowania dysków danych przechwytywania:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Weryfikowanie danych pojedynczych ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dwuprzebiegowy
W dual pass ustawienia szyfrowania są stemplowane w modelu maszyny Wirtualnej, a nie na każdym dysku.

Aby sprawdzić, czy ustawienia szyfrowania zostały oznaczone w trybie dual-pass, można użyć następujących poleceń:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Weryfikacja dwuprzebiegowego programu PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Dyski niezałączone

Sprawdź ustawienia szyfrowania dla dysków, które nie są dołączone do maszyny Wirtualnej.

### <a name="managed-disks"></a>Dyski zarządzane
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Korzystanie z interfejsu wiersza polecenia AZ

Ogólny stan **szyfrowania** zaszyfrowanej maszyny Wirtualnej można sprawdzić, korzystając z następujących poleceń interfejsu wiersza polecenia AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Weryfikowanie ogólne przy użyciu interfejsu wiersza polecenia ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Pojedynczy przełęcz
Ustawienia szyfrowania z każdego dysku można sprawdzić, korzystając z następujących poleceń interfejsu wiersza polecenia AZ:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Ustawienia szyfrowania danych](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> W przypadku, gdy dysk nie ma ustawień szyfrowania opieczętowane, zostanie wyświetlony jako "Dysk nie jest zaszyfrowany"

Szczegółowe ustawienia stanu i szyfrowania:

Dysk systemu operacyjnego:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI (OSSingleCLI)](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Dyski z danymi:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Pojedynczy wiersz polecenia danych ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Podwójna przepustka

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Sprawdź ogólne podwójne ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) przy użyciu interfejsu wiersza polecenia Można również sprawdzić ustawienia szyfrowania w profilu magazynu modelu maszyny Wirtualnej na dysku systemu operacyjnego:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Weryfikowanie podwójnego profilu maszyny wirtualnej przy użyciu interfejsu wiersza polecenia ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Dyski niezałączone

Sprawdź ustawienia szyfrowania dla dysków, które nie są dołączone do maszyny Wirtualnej.

### <a name="managed-disks"></a>Dyski zarządzane

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Dyski niezarządzane

Dyski niezarządzane to pliki VHD, które są przechowywane jako obiekty blob stron na kontach magazynu platformy Azure.

Aby uzyskać szczegółowe informacje o określonym dysku, należy podać:

Identyfikator konta magazynu zawierającego dysk.
Parametry połączenia dla tego konta magazynu.
Nazwa kontenera, który przechowuje dysk.
Nazwa dysku.

To polecenie zawiera listę wszystkich identyfikatorów dla wszystkich kont magazynu:

```bash
az storage account list --query [].[id] -o tsv
```
Identyfikatory kont magazynu są wyświetlane w następującym formularzu:

/subscriptions/\<subscription id>/resourceGroups/\<resource group name>/providers/Microsoft.Storage/storageAccounts/storage\<account name>

Wybierz odpowiedni identyfikator i zapisz go na zmiennej:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Parametry połączenia.

To polecenie pobiera parametry połączenia dla jednego określonego konta magazynu i przechowuje go na zmiennej:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Nazwa kontenera.

Następujące polecenie zawiera listę wszystkich kontenerów w ramach konta magazynu:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Kontener używany dla dysków jest zwykle nazwany "vhds"

Przechowywanie nazwy kontenera na zmiennej 
```bash
ContainerName="name of the container"
```

Nazwa dysku.

To polecenie służy do wystawiania wszystkich obiektów blob w określonym kontenerze
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Wybierz dysk, który chcesz zbadać, i przechowuj jego nazwę na zmiennej.
```bash
DiskName="diskname.vhd"
```
Zapytanie o ustawienia szyfrowania dysku
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Z systemu operacyjnego
Sprawdzanie poprawności, czy partycje dysku danych są szyfrowane (a dysk systemu operacyjnego nie jest)

Gdy partycja/dysk jest szyfrowana, jest wyświetlana jako typ **krypty,** gdy nie jest szyfrowana, jest wyświetlana jako **typ części/dysku**

``` bash
lsblk
```

![Warstwa krypty systemu operacyjnego ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Więcej informacji można uzyskać, korzystając z następującego wariantu "lsblk". 

Zobaczysz warstwę typu **krypty,** która jest zamontowana przez rozszerzenie.

Poniższy przykład przedstawia woluminy logiczne i dyski normalne o "**crypto\_LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Warstwa krypty systemu operacyjnego 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Jako dodatkowy krok można również sprawdzić, czy na dysku danych są załadowane jakieś klucze

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

A które urządzenia dm są wymienione jako krypta

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
