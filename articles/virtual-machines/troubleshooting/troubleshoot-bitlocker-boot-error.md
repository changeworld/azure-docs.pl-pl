---
title: Rozwiązywanie problemów z błędami rozruchu funkcji BitLocker na maszynie wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z błędami rozruchu funkcji BitLocker na maszynie Wirtualnej platformy Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.openlocfilehash: 80fd91106530c0150a85d508b24041b2263da925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250012"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Błędy rozruchu funkcji BitLocker na maszynie wirtualnej platformy Azure

 W tym artykule opisano błędy funkcji BitLocker, które mogą wystąpić podczas uruchamiania maszyny wirtualnej systemu Windows (VM) na platformie Microsoft Azure.

 

## <a name="symptom"></a>Objaw

 Maszyna wirtualna systemu Windows nie uruchamia się. Podczas sprawdzania zrzutów ekranu w oknie [Diagnostyka rozruchu](../windows/boot-diagnostics.md) jest widoczna jedna z następujących komunikatów o błędach:

- Podłącz sterownik USB z kluczem Funkcji BitLocker

- Jesteś zablokowany! Wprowadź klucz odzyskiwania, aby rozpocząć ponownie (Układ klawiatury: USA) Nieprawidłowe informacje logowania zostały wprowadzone zbyt wiele razy, więc komputer został zablokowany, aby chronić swoją prywatność. Aby pobrać klucz odzyskiwania, https://windows.microsoft.com/recoverykeyfaq przejdź do innego komputera lub urządzenia przenośnego. W przypadku, gdy jest to potrzebne, identyfikator klucza jest XXXXXXX. Możesz też zresetować komputer.

- Wprowadź hasło, aby odblokować ten dysk [ ] Naciśnij klawisz Wstaw, aby wyświetlić hasło podczas pisania.
- Wprowadź klucz odzyskiwania Załaduj klucz odzyskiwania z urządzenia USB.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli maszyna wirtualna nie może zlokalizować pliku bek (BitLocker Recovery Key) w celu odszyfrowania zaszyfrowanego dysku.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, zatrzymaj i zdąż przydziel maszynę wirtualną, a następnie uruchom ją ponownie. Ta operacja wymusza maszynę wirtualną do pobrania pliku BEK z usługi Azure Key Vault, a następnie umieścić go na zaszyfrowanym dysku. 

Jeśli ta metoda nie rozwiąże problemu, wykonaj następujące kroki, aby ręcznie przywrócić plik BEK:

1. Wykonaj migawkę dysku systemowego maszyny Wirtualnej, którego dotyczy problem, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).
2. [Podłącz dysk systemowy do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md). Aby uruchomić polecenie [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) w kroku 7, funkcja **szyfrowania dysków funkcją BitLocker** musi być włączona na maszynie wirtualnej odzyskiwania.

    Po dołączeniu dysku zarządzanego może zostać wyświetlony komunikat o błędzie "zawiera ustawienia szyfrowania i dlatego nie może być używany jako dysk danych". W tej sytuacji uruchom następujący skrypt, aby spróbować ponownie dołączyć dysk:

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
     Nie można dołączyć dysku zarządzanego do maszyny Wirtualnej, która została przywrócona z obrazu obiektu blob.

3. Po podłączeniu dysku nawiązuj połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania, aby można było uruchomić niektóre skrypty programu Azure PowerShell. Upewnij się, że masz [zainstalowaną najnowszą wersję programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) na maszynie wirtualnej odzyskiwania.

4. Otwórz podwyższoną sesję programu Azure PowerShell (uruchom jako administrator). Uruchom następujące polecenia, aby zalogować się do subskrypcji platformy Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Uruchom następujący skrypt, aby sprawdzić nazwę pliku BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Poniżej przedstawiono przykład danych wyjściowych. Znajdź nazwę pliku BEK dla dołączonego dysku. W takim przypadku zakładamy, że literą dysku dołączonego dysku jest F, a plik BEK to EF7B2F5A-50C6-4637-9F13-7F599C12F85C. Bek.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Jeśli widzisz dwa zduplikowane woluminy, wolumin, który ma nowszą sygnaturę czasową, jest bieżącym plikiem BEK używanym przez maszynę wirtualną odzyskiwania.

    Jeśli wartość **Typ zawartości** to **Opakowany BEK,** przejdź do [scenariuszy klucza szyfrowania klucza (KEK).](#key-encryption-key-scenario)

    Teraz, gdy masz nazwę pliku BEK dla dysku, musisz utworzyć nazwę pliku tajnego. bek, aby odblokować dysk.

6.  Pobierz plik BEK na dysk odzyskiwania. Poniższy przykład zapisuje plik BEK w folderze C:\BEK. Upewnij się, `C:\BEK\` że ścieżka istnieje przed uruchomieniem skryptów.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Aby odblokować dołączony dysk za pomocą pliku BEK, uruchom następujące polecenie.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    W tym przykładzie dołączony dysk systemu operacyjnego to dysk F. Upewnij się, że używasz poprawnej litery dysku. 

8. Po pomyślnym odblokowaniu dysku przy użyciu klucza BEK odłącz dysk od odzyskiwania maszyny Wirtualnej, a następnie ponownie stwórz maszynę wirtualną przy użyciu tego nowego dysku systemu operacyjnego.

    > [!NOTE]
    > Zamiana dysku systemu operacyjnego nie jest obsługiwana dla maszyn wirtualnych przy użyciu szyfrowania dysku.

9. Jeśli nowa maszyna wirtualna nadal nie może uruchomić się normalnie, spróbuj wykonać jedną z następujących czynności po odblokowaniu dysku:

    - Wstrzymaj ochronę, aby tymczasowo wyłączyć funkcję BitLocker, uruchamiając następujące czynności:

                    manage-bde -protectors -disable F: -rc 0
           
    - Całkowicie odszyfruj dysk. Aby to zrobić, uruchom następujące polecenie:

                    manage-bde -off F:

### <a name="key-encryption-key-scenario"></a>Scenariusz kluczowego klucza szyfrowania

Aby uzyskać scenariusz klucza szyfrowania klucza, wykonaj następujące kroki:

1. Upewnij się, że zalogowane konto użytkownika wymaga uprawnienia "rozpakowane" w zasadach dostępu do magazynu kluczy w **USER| Uprawnienia klucza| Operacje kryptograficzne| Rozpakuj klucz**.
2. Zapisz następujący skrypt w pliku . Ps1 plik:

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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
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
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
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
3. Ustaw parametry. Skrypt przetworzy klucz tajny KEK, aby utworzyć klucz BEK, a następnie zapisać go w folderze lokalnym na maszynie wirtualnej odzyskiwania. Jeśli podczas uruchamiania skryptu są pojawiane błędy, zobacz sekcję [rozwiązywania problemów ze skryptami.](#script-troubleshooting)

4. Po rozpoczęciu skryptu są widoczne następujące dane wyjściowe:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Po zakończeniu skryptu są widoczne następujące dane wyjściowe:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Aby odblokować dołączony dysk za pomocą pliku BEK, uruchom następujące polecenie:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    W tym przykładzie dołączony dysk systemu operacyjnego to dysk F. Upewnij się, że używasz poprawnej litery dysku. 

6. Po pomyślnym odblokowaniu dysku przy użyciu klucza BEK odłącz dysk od odzyskiwania maszyny Wirtualnej, a następnie ponownie stwórz maszynę wirtualną przy użyciu tego nowego dysku systemu operacyjnego. 

    > [!NOTE]
    > Zamiana dysku systemu operacyjnego nie jest obsługiwana dla maszyn wirtualnych przy użyciu szyfrowania dysku.

7. Jeśli nowa maszyna wirtualna nadal nie może uruchomić się normalnie, spróbuj wykonać jedną z następujących czynności po odblokowaniu dysku:

    - Wstrzymaj ochronę, aby tymczasowo wyłączyć funkcję BitLocker, uruchamiając następujące polecenie:

             manage-bde -protectors -disable F: -rc 0
           
    - Całkowicie odszyfruj dysk. Aby to zrobić, uruchom następujące polecenie:

                    manage-bde -off F:
## <a name="script-troubleshooting"></a>Rozwiązywanie problemów ze skryptami

**Błąd: nie można załadować pliku lub złożenia**

Ten błąd występuje, ponieważ ścieżki zestawów ADAL są błędne. Jeśli moduł AZ jest zainstalowany tylko dla bieżącego użytkownika, zestawy `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>`ADAL będą znajdować się w pliku .

Można również wyszukać `Az.Accounts` folder, aby znaleźć właściwą ścieżkę.

**Błąd: Polecenie Get-AzKeyVaultSecret lub Get-AzKeyVaultSecret nie jest rozpoznawane jako nazwa polecenia cmdlet**

Jeśli używasz starego modułu AZ PowerShell, musisz zmienić dwa polecenia na `Get-AzureKeyVaultSecret` i `Get-AzureKeyVaultSecret`.

**Próbki parametrów**

| Parametry  | Próbka wartości  |Komentarze   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Nazwa magazynu kluczy, w który jest on alokacjowy |
|$kekName   |Mykey   | Nazwa klucza używanego do szyfrowania maszyny Wirtualnej|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Nazwa klucza tajnego klucza maszyny Wirtualnej|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. Bek |Ścieżka do pisania pliku BEK.|
|$adTenant  |contoso.onmicrosoft.com   | Funkcja FQDN lub identyfikator GUID usługi Azure Active Directory, w którym znajduje się magazyn kluczy |
