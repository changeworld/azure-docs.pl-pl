---
title: Rozwiązywanie problemów z funkcją BitLocker błędy rozruchu na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z funkcją BitLocker błędy rozruchu na Maszynie wirtualnej platformy Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: e60188496e060eeea14fc7b7f1cc9a662551b286
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485169"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Funkcja BitLocker błędy rozruchu na Maszynie wirtualnej platformy Azure

 W tym artykule opisano funkcję BitLocker błędy, które mogą wystąpić podczas uruchamiania Windows maszyny wirtualnej (VM) w systemie Microsoft Azure.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

 Nie zaczyna się maszyny Wirtualnej z systemem Windows. Podczas ewidencjonowania zrzuty ekranu [diagnostykę rozruchu](../windows/boot-diagnostics.md) oknie zostanie wyświetlony następujący komunikat o błędzie:

- Podłącz sterownik USB, który ma klucz funkcji BitLocker

- Możesz teraz blokady! Wprowadź klucz odzyskiwania, aby zacząć ponownie (układ klawiatury: Informacje logowania dla Stanów Zjednoczonych) nieprawidłowa został wprowadzony zbyt wiele razy, aby komputer został zablokowany, aby chronić Twoją prywatność. Aby pobrać klucz odzyskiwania, przejdź do https://windows.microsoft.com/recoverykeyfaq z innego komputera lub urządzenia przenośnego. W przypadku, gdy będą potrzebne, klucz ID jest XXXXXXX. Alternatywnie można zresetować komputera PC.

- Wprowadź hasło, aby odblokować ten dysk [] naciśnij klawisz Insert, aby wyświetlić hasło podczas wpisywania.
- Wprowadź klucz odzyskiwania obciążenia klucz odzyskiwania z urządzenia USB.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli maszyna wirtualna nie może zlokalizować pliku klucza odzyskiwania funkcji BitLocker (BEK) można odszyfrować zaszyfrowanego dysku.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Zatrzymaj i cofnięcie przydziału maszyny Wirtualnej, a następnie uruchom go ponownie. Ta operacja powoduje maszynę Wirtualną, aby pobrać plik klucza szyfrowania bloków z usługi Azure Key Vault, a następnie przekaż go na dysku zaszyfrowanego. 

Jeśli ta metoda nie Rozwiąż problem, wykonaj następujące kroki, aby przywrócić plik klucza szyfrowania bloków ręcznie:

1. Utwórz migawkę dysku systemowego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).
2. [Dołącz dysk systemu do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md). Aby uruchomić [Zarządzanie bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) w kroku 7, polecenie **szyfrowania dysków funkcją BitLocker** musi być włączona funkcja odzyskiwania maszyny Wirtualnej.

    Po dołączeniu dysku zarządzanego, może zostać wyświetlony komunikat o błędzie "zawiera ustawienia szyfrowania i dlatego nie można użyć jako dysku danych". W takiej sytuacji Uruchom następujący skrypt, aby spróbować ponownie podłączyć dysk:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Nie można dołączyć dysku zarządzanego do maszyny Wirtualnej, który został przywrócony na podstawie obiektu blob obrazu.

3. Po dołączeniu dysku należy połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania, aby można było uruchomić niektóre skrypty programu Azure PowerShell. Upewnij się, że masz [najnowszą wersję programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) zainstalowane na maszynie Wirtualnej odzyskiwania.

4. Otwórz sesję programu Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia, aby zalogować się do subskrypcji platformy Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Uruchom następujący skrypt, aby sprawdzić nazwę pliku klucza szyfrowania bloków:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe. Znajdź nazwę pliku klucza szyfrowania bloków dla dołączonych dysków. W tym przypadku przyjęto założenie, że literę dysku dołączonym jest F, a plik klucza szyfrowania bloków jest EF7B2F5A 50C 6-4637-9F13-7F599C12F85C okresu. KLUCZ SZYFROWANIA BLOKÓW.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Jeśli zobaczysz dwa woluminy zduplikowane, wolumin, który ma nowszą sygnatura czasowa jest bieżący plik klucza szyfrowania bloków, który jest używany przez maszynę Wirtualną odzyskiwania.

    Jeśli **typu zawartości** wartość **opakowane klucz szyfrowania bloków**, przejdź do [scenariuszy klucz szyfrowania klucza (KEK)](#key-encryption-key-scenario).

    Teraz, gdy masz nazwę pliku klucza szyfrowania bloków dla dysku, należy utworzyć nazwy pliku klucza tajnego. Plik klucza szyfrowania bloków do odblokowania dysku.

6.  Pobierz plik klucza szyfrowania bloków na dysku odzyskiwania. Poniższy przykład zapisuje plik klucza szyfrowania bloków w folderze C:\BEK. Upewnij się, że `C:\BEK\` ścieżka istnieje, przed uruchomieniem skryptów.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Aby odblokować dysk dołączony przy użyciu pliku klucza szyfrowania bloków, uruchom następujące polecenie.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    W tym przykładzie dołączonym dysku systemu operacyjnego jest dysku F. Upewnij się, że używasz prawidłowej literę dysku. 

    - Jeśli dysk został pomyślnie odblokowane za pomocą klucza klucz szyfrowania bloków. Firma Microsoft uważa, można rozwiązać problem funkcji BitLocker. 

    - Jeśli przy użyciu klucza klucz szyfrowania bloków nie odblokuje dysku, możesz użyć zawiesić ochrony, aby tymczasowo wyłączyć funkcję BitLocker, uruchamiając następujące polecenie
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Jeśli chcesz ponownie utworzyć maszynę Wirtualną przy użyciu dysku dytem, możesz w pełni odszyfrować dysk. Aby to zrobić, uruchom następujące polecenie:

        ```powershell
        manage-bde -off F:
        ```
8.  Odłącz dysk od maszyny Wirtualnej odzyskiwania, a następnie ponownie Dołącz dysk do których to dotyczy maszyny Wirtualnej jako dysk systemowy. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyny Wirtualnej z systemem Windows, dołączając dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Scenariusz klucza szyfrowania

W scenariuszu klucza szyfrowania wykonaj następujące kroki:

1. Upewnij się, że konta zalogowanego użytkownika wymaga uprawnienia "odkodowany" w zasadach dostęp do klucza magazynu w **użytkownika | Uprawnienia klucza | Operacje kryptograficzne | Odpakuj klucz**.
2. Zapisz następujące skrypty w celu. Plik PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Ustaw parametry. Skrypt przetworzyć wpisu tajnego klucza KEK można utworzyć klucza klucz szyfrowania bloków, a następnie zapisać go w folderze lokalnym na maszynie Wirtualnej odzyskiwania.

4. Zostaną wyświetlone następujące dane wyjściowe, po rozpoczęciu skryptu:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Po zakończeniu działania skryptu zostanie wyświetlony następujące dane wyjściowe:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Aby odblokować dysk dołączony przy użyciu pliku klucza szyfrowania bloków, uruchom następujące polecenie:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    W tym przykładzie dołączonym dysku systemu operacyjnego jest dysku F. Upewnij się, że używasz prawidłowej literę dysku. 

    - Jeśli dysk został pomyślnie odblokowane za pomocą klucza klucz szyfrowania bloków. Firma Microsoft uważa, można rozwiązać problem funkcji BitLocker. 

    - Jeśli przy użyciu klucza klucz szyfrowania bloków nie odblokuje dysku, możesz użyć zawiesić ochrony, aby tymczasowo wyłączyć funkcję BitLocker, uruchamiając następujące polecenie
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Jeśli chcesz ponownie utworzyć maszynę Wirtualną przy użyciu dysku dytem, możesz w pełni odszyfrować dysk. Aby to zrobić, uruchom następujące polecenie:

        ```powershell
        manage-bde -off F:
        ```

6. Odłącz dysk od maszyny Wirtualnej odzyskiwania, a następnie ponownie Dołącz dysk do których to dotyczy maszyny Wirtualnej jako dysk systemowy. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyny Wirtualnej z systemem Windows, dołączając dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](troubleshoot-recovery-disks-windows.md).
