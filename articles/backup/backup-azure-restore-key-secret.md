---
title: Przywróć klucz magazynu kluczy & kluczem tajnym dla zaszyfrowanej maszyny Wirtualnej
description: Dowiedz się, jak przywrócić klucz i klucz tajny usługi Key Vault w usłudze Azure Backup przy użyciu programu PowerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450067"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Przywracanie klucza i wpisu tajnego usługi Key Vault dla szyfrowanych maszyn wirtualnych za pomocą usługi Azure Backup

W tym artykule o mówi o użyciu usługi Azure VM Backup do wykonywania przywracania zaszyfrowanych maszyn wirtualnych platformy Azure, jeśli klucz i klucz tajny nie istnieją w magazynie kluczy. Te kroki mogą być również używane, jeśli chcesz zachować oddzielną kopię klucza (klucz szyfrowania klucza) i klucz tajny (klucz szyfrowania funkcją BitLocker) dla przywróconej maszyny Wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* **Tworzenie kopii zapasowych zaszyfrowanych maszyn wirtualnych** — szyfrowane maszyny wirtualne platformy Azure zostały utworzone przy użyciu usługi Azure Backup. Zapoznaj się [z artykułem Zarządzanie tworzeniem kopii zapasowych i przywracania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell,](backup-azure-vms-automation.md) aby uzyskać szczegółowe informacje na temat tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych platformy Azure.
* **Konfigurowanie usługi Azure Key Vault** — upewnij się, że magazyn kluczy, do którego klucze i wpisy tajne muszą zostać przywrócone, jest już obecny. Zapoznaj się z artykułem [Wprowadzenie do usługi Azure Key Vault, aby](../key-vault/key-vault-get-started.md) uzyskać szczegółowe informacje na temat zarządzania magazynem kluczy.
* **Przywróć dysk** — upewnij się, że zostało wyzwolone zadanie przywracania w celu przywrócenia dysków zaszyfrowanej maszyny Wirtualnej przy użyciu [kroków programu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Dzieje się tak, ponieważ to zadanie generuje plik JSON na koncie magazynu zawierający klucze i wpisy tajne dla zaszyfrowanej maszyny Wirtualnej do przywrócenia.

## <a name="get-key-and-secret-from-azure-backup"></a>Uzyskaj klucz i klucz tajny z usługi Azure Backup

> [!NOTE]
> Po przywróceniu dysku dla zaszyfrowanej maszyny Wirtualnej upewnij się, że:
>
> * $details jest wypełniona szczegółami zadania przywracania dysku, jak wspomniano w [krokach programu PowerShell w sekcji Przywracanie dysków](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Maszyna wirtualna powinna być tworzona z przywróconych dysków tylko **po przywróceniu klucza i klucza tajnego do magazynu kluczy**.

Kwerenda przywrócona właściwości dysku dla szczegółów zadania.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Ustaw kontekst magazynu platformy Azure i przywróć plik konfiguracyjny JSON zawierający klucz i tajne szczegóły zaszyfrowanej maszyny Wirtualnej.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Klucz przywracania

Po wygenerowaniu pliku JSON w ścieżce docelowej wymienionej powyżej wygeneruj plik obiektu blob klucza z JSON i podaj go, aby przywrócić polecenie cmdlet klucza, aby umieścić klucz (KEK) z powrotem w magazynie kluczy.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Przywracanie klucza tajnego

Użyj pliku JSON wygenerowanego powyżej, aby uzyskać tajną nazwę i wartość i podać go, aby ustawić tajne polecenie cmdlet, aby umieścić klucz tajny (BEK) z powrotem w magazynie kluczy.Użyj tych poleceń cmdlet, jeśli maszyna **wirtualna jest szyfrowana przy użyciu BEK i KEK**.

**Użyj tych poleceń cmdlet, jeśli maszyna wirtualna systemu Windows jest szyfrowana przy użyciu bek i KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Użyj tych poleceń cmdlet, jeśli maszyna wirtualna z systemem Linux jest szyfrowana przy użyciu BEK i KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Użyj pliku JSON wygenerowanego powyżej, aby uzyskać tajną nazwę i wartość i podać go, aby ustawić tajne polecenie cmdlet, aby umieścić klucz tajny (BEK) z powrotem w magazynie kluczy.Użyj tych poleceń cmdlet, jeśli maszyna **wirtualna jest szyfrowana tylko przy użyciu bek.**

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * Wartość dla $secretname można uzyskać, odwołując się do danych wyjściowych $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl i używając tekstu po https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 wpisach tajnych/ np.
> * Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa tajnego.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Tworzenie maszyny wirtualnej z przywróconego dysku

Jeśli wykonano kopię zapasową zaszyfrowanej maszyny Wirtualnej przy użyciu kopii zapasowej maszyn wirtualnych platformy Azure, wyżej wymienione polecenia cmdlet programu PowerShell ułatwiają przywracanie klucza i klucza tajnego z powrotem do magazynu kluczy. Po ich przywróceniu zapoznaj się z [artykułem Zarządzanie tworzeniem kopii zapasowych i przywracaniem maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) w celu utworzenia zaszyfrowanych maszyn wirtualnych z przywróconego dysku, klucza i klucza tajnego.

## <a name="legacy-approach"></a>Starsze podejście

Powyższe podejście sprawdziłoby się we wszystkich punktach odzyskiwania. Jednak starsze podejście uzyskiwania kluczowych i tajnych informacji z punktu odzyskiwania będzie ważne dla punktów odzyskiwania starszych niż 11 lipca 2017 r. dla maszyn wirtualnych zaszyfrowanych przy użyciu BEK i KEK. Po zakończeniu zadania przywracania dysku dla zaszyfrowanej maszyny Wirtualnej przy użyciu [kroków programu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)upewnij się, że $rp jest wypełniona prawidłową wartością.

### <a name="restore-key"></a>Klucz przywracania

Użyj następujących poleceń cmdlet, aby uzyskać informacje o kluczu (KEK) z punktu odzyskiwania i umieścić je, aby przywrócić polecenie cmdlet klucza, aby umieścić je z powrotem w magazynie kluczy.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Przywracanie klucza tajnego

Użyj następujących poleceń cmdlet, aby uzyskać tajne informacje (BEK) z punktu odzyskiwania i karmić je, aby ustawić tajne polecenie cmdlet, aby umieścić je z powrotem w magazynie kluczy.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * Wartość dla $secretname można uzyskać, odwołując się do danych wyjściowych $rp1. KeyAndSecretDetails.SecretUrl i przy użyciu tekstu po secrets / https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 np. wyjście tajny adres URL jest i tajne nazwa jest B3284AAA-DAAA-4AAA-B393-60CAA848AAAAA
> * Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa tajna.
> * Wartość dla DiskEncryptionKeyEncryptionKeyURL można uzyskać z magazynu kluczy po przywróceniu kluczy z powrotem i użyciu polecenia cmdlet [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey)
>
>

## <a name="next-steps"></a>Następne kroki

Po przywróceniu klucza i klucza tajnego z powrotem do magazynu kluczy, zapoznaj się z [artykułem Zarządzanie tworzeniem kopii zapasowych i przywracania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) do tworzenia zaszyfrowanych maszyn wirtualnych z przywróconego dysku, klucza i klucza tajnego.
