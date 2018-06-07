---
title: Przywróć klucz magazynu kluczy oraz klucz tajny dla zaszyfrowanych maszyn wirtualnych za pomocą usługi Kopia zapasowa Azure
description: Dowiedz się, jak można przywrócić klucza magazynu kluczy i klucz tajny w kopii zapasowej Azure przy użyciu programu PowerShell
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b703b4511f9fefb48546b23feaa33ca7da34da1f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606107"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Przywróć klucz magazynu kluczy oraz klucz tajny dla zaszyfrowanych maszyn wirtualnych za pomocą usługi Kopia zapasowa Azure
Ten artykuł zawiera informacje o przy użyciu kopii zapasowej maszyny Wirtualnej Azure przeprowadzić przywracanie zaszyfrowanych maszynach wirtualnych platformy Azure, jeśli klucz, a klucz tajny nie istnieje w magazynie kluczy. Te kroki można także jeśli chcesz zachować kopię klucza (klucz szyfrowania klucza) i klucz tajny (klucza szyfrowania funkcją BitLocker) przywróconej maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne
* **Kopia zapasowa szyfrowane maszyn wirtualnych** — zaszyfrowanych Azure maszyny wirtualne utworzone kopie zapasowe przy użyciu usługi Kopia zapasowa Azure. Zobacz artykuł [Zarządzanie kopii zapasowej i przywracania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md) szczegółowe informacje o sposobie tworzenia kopii zapasowej zaszyfrowanego maszynach wirtualnych platformy Azure.
* **Konfigurowanie usługi Azure Key Vault** — upewnij się, że magazyn kluczy, do której konieczne jest przywrócenie kluczy i kluczy tajnych znajduje się już. Zobacz artykuł [wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md) szczegółowe informacje na temat zarządzania magazynu kluczy.
* **Przywracanie dysku** — upewnij się, że ma wyzwolone zadanie przywracania przywracania dysku zaszyfrowanego maszynę Wirtualną przy użyciu [kroki PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Jest to spowodowane tego zadania generuje plik JSON na koncie magazynu zawierające kluczy i kluczy tajnych zaszyfrowanych maszyny wirtualnej do przywrócenia.

## <a name="get-key-and-secret-from-azure-backup"></a>Pobierz klucz i klucz tajny usługi Kopia zapasowa Azure

> [!NOTE]
> Po przywróceniu dysku zaszyfrowanego maszyny wirtualnej, upewnij się, że:
> 1. $details jest wypełniana przywracania dysku szczegóły zadania, jak wspomniano w [programu PowerShell opisanych w sekcjach przywracania sekcji dysków](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. Należy utworzyć maszyny Wirtualnej z dysków przywróconej tylko **po klucz i klucz tajny są odzyskiwane do magazynu kluczy**.
>
>

Wyślij zapytanie do właściwości dysku przywróconej szczegóły zadania.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Ustaw kontekst magazynu Azure i przywrócić plik JSON konfiguracji zawierający klucz i tajny szczegóły dla zaszyfrowanych maszyny Wirtualnej.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Przywracanie klucza
Wygenerowany plik JSON w ścieżce docelowej wymienione powyżej Generowanie pliku obiektu blob klucza w formacie JSON i źródła danych do przywrócenia klucza polecenia cmdlet, aby umieścić klucza (KEK) Wstecz w magazynie kluczy.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Przywróć klucz tajny
Użyj pliku JSON wygenerowany powyżej uzyskać tajny nazwą i wartością i umieść go, aby ustawić tajny polecenia cmdlet, aby umieścić klucz tajny (BEK) Wstecz w magazynie kluczy. **Jeśli maszyna wirtualna jest szyfrowana przy użyciu BEK i KEK, należy używać tych poleceń cmdlet.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Jeśli maszyna wirtualna jest **zaszyfrowane za pomocą BEK tylko**, generowanie pliku blob tajny w formacie JSON i umieść go przywrócić poufne polecenia cmdlet, aby umieścić klucz tajny (BEK) w magazynie kluczy.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. Wartość można uzyskać $secretname odwołujących się do danych wyjściowych $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl i przy użyciu tekstu po kluczy tajnych / np. adres URL poufne dane wyjściowe jest https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 i B3284AAA-DAAA-4AAA-B393-60CAA848AAAA jest nazwa klucza tajnego
> 2. Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa klucza tajnego.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Tworzenie maszyny wirtualnej z przywróconą dysku
Jeśli wykonano kopię zapasową zaszyfrowanych maszyny Wirtualnej przy użyciu kopii zapasowej maszyny Wirtualnej Azure, poleceń cmdlet programu PowerShell wymienione powyżej pomocy Przywracanie klucza i z powrotem tajnego do magazynu kluczy. Po przywróceniu ich, zobacz artykuł [Zarządzanie kopii zapasowej i przywracania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) do tworzenia zaszyfrowanego maszyn wirtualnych z przywróconą dysku, kluczy i klucz tajny.

## <a name="legacy-approach"></a>Podejście starszej wersji
Wspomnianego powyżej będzie działać we wszystkich punktach odzyskiwania. Jednak starsze podejście pobierania klucza i tajnych informacji z punktu odzyskiwania, będzie ważny dla punktów odzyskiwania, starsze niż 11 lipca 2017 zaszyfrowane za pomocą BEK i KEK maszyn wirtualnych. Po zakończeniu zadania przywracania dysku dla zaszyfrowanych maszynę Wirtualną przy użyciu [kroki PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), upewnij się, że $rp jest wypełniane przy użyciu prawidłowej wartości.

### <a name="restore-key"></a>Przywracanie klucza
Pobieranie informacji klucza (KEK) z punktu odzyskiwania i umieść go do przywrócenia klucza polecenia cmdlet, aby ją ponownie w magazynie kluczy, należy użyć następujących poleceń cmdlet.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Przywróć klucz tajny
Użyj następujących poleceń cmdlet uzyskać tajnych informacji (BEK) z punktu odzyskiwania i umieść go ustawić umieszcza je w magazynie kluczy tajnych polecenia cmdlet.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. Wartość $secretname można uzyskać, odwołując się do danych wyjściowych $po1. KeyAndSecretDetails.SecretUrl i przy użyciu tekstu po kluczy tajnych / np. klucz tajny dane wyjściowe adres URL jest https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 i B3284AAA-DAAA-4AAA-B393-60CAA848AAAA jest nazwa klucza tajnego
> 2. Wartość tagu DiskEncryptionKeyFileName jest taka sama jak nazwa klucza tajnego.
> 3. Wartość DiskEncryptionKeyEncryptionKeyURL można uzyskać z magazynu kluczy po Przywracanie kluczy powrót i używanie [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) polecenia cmdlet
>
>

## <a name="next-steps"></a>Kolejne kroki
Po przywróceniu klucza i z powrotem tajnego do magazynu kluczy, zobacz artykuł [Zarządzanie kopii zapasowej i przywracania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) do tworzenia zaszyfrowanego maszyn wirtualnych z przywróconą dysku, kluczy i klucz tajny.
