---
title: Zezwalaj aplikacjom na pobieranie kluczy tajnych magazyn kluczy Azure stosu | Dokumentacja firmy Microsoft
description: Użyj przykładowej aplikacji do pracy z magazynem kluczy Azure stosu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: da2c733dbb1d1d3c8294ee82e722d33d363b29ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Przykładowa aplikacja korzystająca z kluczy i kluczy tajnych przechowywane w magazynie kluczy

W tym artykule zostanie przedstawiony zostanie sposób uruchamiania aplikacji przykładowej (HelloKeyVault), która pobiera kluczy i kluczy tajnych z magazynu kluczy Azure stosu.

## <a name="prerequisites"></a>Wymagania wstępne 

Uruchom następujące wymagania wstępne, albo z [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klienta zewnętrznych w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).  
* Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Tworzenie i uzyskać magazyn kluczy i ustawienia aplikacji

Najpierw należy utworzyć magazyn kluczy Azure stosu i zarejestrować aplikację w usłudze Azure Active Directory (Azure AD). Można utworzyć ani zarejestrować magazynów kluczy za pomocą portalu Azure lub programu PowerShell. W tym artykule opisano sposób programu PowerShell do wykonywania zadań. Domyślnie ten skrypt programu PowerShell tworzy nową aplikację w usłudze Active Directory. Jednak umożliwia także jeden z istniejących aplikacji. Pamiętaj podać wartość parametru `aadTenantName` i `applicationPassword` zmiennych. Jeśli nie określisz wartości `applicationPassword` zmiennej, ten skrypt generuje losowe hasło. 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

Poniższy zrzut ekranu przedstawia dane wyjściowe poprzedniego skryptu:

![Konfiguracja aplikacji](media/azure-stack-kv-sample-app/settingsoutput.png)

Zanotuj **VaultUrl**, **AuthClientId**, i **AuthClientSecret** zwróconych przez skrypt poprzedniej wartości. Te wartości są używane do uruchamiania aplikacji HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Pobieranie i uruchamianie przykładowej aplikacji

Pobierz przykładowe magazynu kluczy z platformy Azure [przykłady klienta usługi Key Vault](https://www.microsoft.com/en-us/download/details.aspx?id=45343) strony. Wyodrębnij zawartość pliku ZIP na deweloperskiej stacji roboczej. Istnieją dwa przykłady znajdujących się w folderze próbek. Używamy próbki HellpKeyVault w tym artykule. Przejdź do **Microsoft.Azure.KeyVault.Samples** > **przykłady** > **HelloKeyVault** folderu i otworzyć aplikację HelloKeyVault w programie Visual Studio. 

Otwórz plik HelloKeyVault\App.config i Zastąp wartości <appSettings> element z **VaultUrl**, **AuthClientId**, i **AuthClientSecret** wartości zwrócony przez poprzednie skryptu. Należy zauważyć, domyślnie App.config zawiera symbol zastępczy *AuthCertThumbprint*, ale użyj *AuthClientSecret* zamiast tego. Po zastąpić jej ustawienia, ponownie skompiluj rozwiązanie i uruchom aplikację.

![Ustawienia aplikacji](media/azure-stack-kv-sample-app/appconfig.png)
 
Aplikacja loguje się do usługi Azure AD, a następnie używa token uwierzytelniania do magazynu kluczy Azure stosu. Aplikacja wykonuje operacje, takie jak tworzenie, szyfrowania, zawijać i usuwanie kluczy i kluczy tajnych magazynu kluczy. Można również przekazać określone parametry takie jak *szyfrowania* i *odszyfrować* do aplikacji, co czyni się upewnić, że aplikacja wykonuje tylko operacje względem magazynu. 


## <a name="next-steps"></a>Kolejne kroki
[Wdrażanie maszyny wirtualnej z hasłem usługi Key Vault](azure-stack-kv-deploy-vm-with-secret.md)

[Wdrożenie maszyny Wirtualnej przy użyciu certyfikatu usługi Key Vault](azure-stack-kv-push-secret-into-vm.md)



