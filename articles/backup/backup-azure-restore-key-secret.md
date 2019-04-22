---
title: Przywróć klucz usługi Key Vault i klucz tajny dla szyfrowanych maszyn wirtualnych przy użyciu usługi Azure Backup
description: Dowiedz się, jak przywrócić klucz usługi Key Vault i klucz tajny w usłudze Azure Backup przy użyciu programu PowerShell
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: geetha
ms.openlocfilehash: 13eb800cd64e0de736b1fdea308a03d8a8d0f046
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358202"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Przywróć klucz usługi Key Vault i klucz tajny dla szyfrowanych maszyn wirtualnych przy użyciu usługi Azure Backup

Ten artykuł zawiera informacje o przy użyciu kopii zapasowych maszyn wirtualnych platformy Azure przeprowadzić przywracanie zaszyfrowanych maszyn wirtualnych platformy Azure, z kluczem i wpisem tajnym nie istnieją w magazynie kluczy. Te kroki można również Jeśli chcesz zachować oddzielna kopia key (klucz szyfrowania klucza) i klucz tajny (klucz szyfrowania funkcją BitLocker) dla przywróconej maszyny Wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* **Kopii zapasowej zaszyfrowanych maszyn wirtualnych** — zaszyfrowanych maszyn wirtualnych platformy Azure utworzone kopie zapasowe przy użyciu usługi Azure Backup. Zobacz artykuł [Zarządzanie kopia zapasowa i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md) szczegółowe informacje na temat sposobu tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych platformy Azure.
* **Konfigurowanie usługi Azure Key Vault** — upewnij się, w tym magazynie kluczy, do którego mają być przywracane kluczy i wpisów tajnych jest już obecny. Zobacz artykuł [Rozpoczynanie pracy z usługą Azure Key Vault](../key-vault/key-vault-get-started.md) szczegółowe informacje na temat Zarządzanie usługą key vault.
* **Przywracanie dysku** — upewnij się, że zostało wyzwolone zadanie przywracania przywracania dysków dla zaszyfrowanej maszyny Wirtualnej przy użyciu [— kroki programu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Jest to spowodowane to zadanie generuje plik w formacie JSON na koncie magazynu, zawierający klucze i wpisy tajne zaszyfrowane maszyny wirtualnej do przywrócenia.

## <a name="get-key-and-secret-from-azure-backup"></a>Pobieranie klucza i wpisu tajnego z usługi Azure Backup

> [!NOTE]
> Gdy dysk został przywrócony do zaszyfrowanej maszyny Wirtualnej, upewnij się, że:
> * $details jest wypełniana szczegóły zadania przywracania dysku, zgodnie z opisem w [PowerShell kroki przywracania sekcji dysków](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Należy utworzyć maszyny Wirtualnej z przywróconych dysków tylko **po przywróceniu klucza i wpisu tajnego usługi key vault**.

Tworzenie zapytań o właściwości przywróconego dysku, aby uzyskać szczegóły zadania.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Ustaw kontekst magazynu platformy Azure i przywrócić pliku konfiguracji JSON zawierający klucza i wpisu tajnego szczegóły dla zaszyfrowanej maszyny Wirtualnej.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Przywracanie klucza

Po wygenerowaniu pliku JSON w ścieżce docelowej w wymienionych powyżej wygenerować plik obiektu blob klucza z danych JSON i źródła danych do przywrócenia klucza polecenia cmdlet, aby przełączyć klucza (KEK), wróć do usługi key vault.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Przywróć klucz tajny

Użyj pliku JSON, wygenerowany powyżej, nazwa wpisu tajnego i wartości do niego, aby ustawić wpisu tajnego polecenia cmdlet, aby umieścić wpis tajny (klucz szyfrowania bloków) ponownie w usłudze key vault. Użyj tych poleceń cmdlet, jeśli Twoja **maszyna wirtualna jest zaszyfrowana przy użyciu BEK i KEK**.

**Użyj tych poleceń cmdlet, jeśli maszyna wirtualna Windows jest zaszyfrowana przy użyciu BEK i KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Jeśli maszyna wirtualna systemu Linux jest zaszyfrowany przy użyciu BEK i KEK, należy używać tych poleceń cmdlet.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Użyj pliku JSON, wygenerowany powyżej, nazwa wpisu tajnego i wartości do niego, aby ustawić wpisu tajnego polecenia cmdlet, aby umieścić wpis tajny (klucz szyfrowania bloków) ponownie w usłudze key vault. Użyj tych poleceń cmdlet, jeśli Twoja **maszyna wirtualna jest zaszyfrowana przy użyciu klucza szyfrowania bloków** tylko.

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
> * Wartość $secretname można uzyskać przez odwołujący się do danych wyjściowych $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl i przy użyciu tekstu po wpisów tajnych / np. adres URL wpisu tajnego danych wyjściowych jest https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 i nazwa wpisu tajnego jest B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa wpisu tajnego.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Jeśli utworzono kopię zapasową zaszyfrowanej maszyny Wirtualnej przy użyciu kopii zapasowych maszyn wirtualnych platformy Azure, poleceń cmdlet programu PowerShell wymienionej powyżej pozwalają Przywracanie klucza i wpisu tajnego wstecz do magazynu kluczy. Po przywróceniu ich, zobacz artykuł [Zarządzanie kopia zapasowa i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) Aby utworzyć zaszyfrowanych maszyn wirtualnych na podstawie przywróconego dysku, klucza i wpisu tajnego.

## <a name="legacy-approach"></a>Podejście starszej wersji

Podejście, o których wspomniano powyżej, będzie działać dla wszystkich punktów odzyskiwania. Jednak starsze metody pobierania klucza i wpisu tajnego informacje z punktu odzyskiwania będzie obowiązywać punkty odzyskiwania starsze niż 11 lipca 2017 r. za maszyny wirtualne szyfrowane przy użyciu BEK i KEK. Po zakończeniu przywracania dysku zadania dla zaszyfrowanej maszyny Wirtualnej przy użyciu [— kroki programu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), upewnij się, $rp jest wypełniana prawidłową wartość.

### <a name="restore-key"></a>Przywracanie klucza

Użyj następujących poleceń cmdlet, aby uzyskać informacje o kluczu (KEK) z punktu odzyskiwania i źródła danych do przywrócenia klucza polecenia cmdlet, aby umieścić go w magazynie kluczy.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Przywróć klucz tajny

Użyj następujących poleceń cmdlet pobrać informacji o secret (klucz szyfrowania bloków) z punktu odzyskiwania do niego można ustawić klucza tajnego polecenia cmdlet, aby umieścić go w magazynie kluczy.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> * Wartość $secretname można uzyskać, odwołując się do danych wyjściowych po1 $. KeyAndSecretDetails.SecretUrl i przy użyciu tekstu po wpisów tajnych / np. wpis tajny w danych wyjściowych adres URL jest https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 i nazwa wpisu tajnego jest B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa wpisu tajnego.
> * Wartość DiskEncryptionKeyEncryptionKeyURL można uzyskać z magazynu kluczy po przywracania kluczy powrót i używanie [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) polecenia cmdlet
>
>

## <a name="next-steps"></a>Kolejne kroki

Po przywróceniu klucza i wpisu tajnego wstecz do magazynu kluczy, zobacz artykuł [Zarządzanie kopia zapasowa i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) Aby utworzyć zaszyfrowanych maszyn wirtualnych na podstawie przywróconego dysku, klucza i wpisu tajnego.
