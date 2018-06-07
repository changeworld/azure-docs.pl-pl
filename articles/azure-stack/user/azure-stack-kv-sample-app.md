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
ms.date: 05/11/2018
ms.author: mabrigg
ms.openlocfilehash: 39bce286c756660cd8755358cf98f2c8d35ce351
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807384"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Przykładowa aplikacja, która używa kluczy i kluczy tajnych przechowywane w magazynie kluczy

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Wykonaj kroki opisane w tym artykule, aby uruchomić przykładową aplikację (HelloKeyVault) pobiera kluczy i kluczy tajnych z magazynu kluczy Azure stosu.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne można zainstalować ze stosu Azure [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klienta zewnętrznych w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md).
* Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Tworzenie i uzyskać magazyn kluczy i ustawienia aplikacji

Aby przygotować się do aplikacji przykładowej:

* Tworzenie magazynu kluczy Azure stosu.
* Zarejestrować aplikację w usłudze Azure Active Directory (Azure AD).

Aby przygotować się do przykładowej aplikacji, można użyć portalu Azure lub programu PowerShell. W tym artykule przedstawiono sposób tworzenia magazynu kluczy i zarejestrować aplikację za pomocą programu PowerShell.

>[!NOTE]
>Domyślnie skrypt programu PowerShell tworzy nową aplikację w usłudze Active Directory. Jednak możesz zarejestrować się jeden z istniejących aplikacji.

 Przed uruchomieniem poniższy skrypt, upewnij się, podaj wartości dla `aadTenantName` i `applicationPassword` zmiennych. Jeśli nie określisz wartości `applicationPassword`, ten skrypt generuje losowe hasło.

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

# Create a new resource group and a key vault in that resource group.
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

Dalej zrzucie ekranu przedstawiono dane wyjściowe skryptu użyty do utworzenia magazynu kluczy:

![Klucz magazynu z klawiszy dostępu](media/azure-stack-kv-sample-app/settingsoutput.png)

Zanotuj **VaultUrl**, **AuthClientId**, i **AuthClientSecret** zwróconych przez skrypt poprzedniej wartości. Te wartości są używane do uruchamiania aplikacji HelloKeyVault.

## <a name="download-and-configure-the-sample-application"></a>Pobierz i skonfiguruj przykładowej aplikacji

Pobierz przykładowe magazynu kluczy z platformy Azure [przykłady klienta usługi Key Vault](https://www.microsoft.com/en-us/download/details.aspx?id=45343) strony. Wyodrębnij zawartość pliku ZIP na deweloperskiej stacji roboczej. Istnieją dwie aplikacje w folderze przykłady, w tym artykule wykorzystano HelloKeyVault.

Załadować przykładowe HelloKeyVault:

* Przejdź do **Microsoft.Azure.KeyVault.Samples** > **przykłady** > **HelloKeyVault** folderu.
* Otwórz aplikację HelloKeyVault w programie Visual Studio.

### <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

W programie Visual Studio:

* Otwórz plik HelloKeyVault\App.config i Przeglądaj, aby znaleźć &lt; **appSettings** &gt; elementu.
* Aktualizacja **VaultUrl**, **AuthClientId**, i **AuthClientSecret** klucze o wartości zwracane użyta do utworzenia magazynu kluczy. (Domyślnie plik App.config ma symbol zastępczy *AuthCertThumbprint*. Zamiast tego symbolu zastępczego z *AuthClientSecret*.)

  ![Ustawienia aplikacji](media/azure-stack-kv-sample-app/appconfig.png)

* Ponownie skompiluj rozwiązanie.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu HelloKeyVault aplikacji loguje się do usługi Azure AD, a następnie używa AuthClientSecret token uwierzytelniania do magazynu kluczy Azure stosu.

Możesz użyć przykładu HelloKeyVault do:

* Wykonywać podstawowe operacje, takie jak tworzenie, szyfrowania, zawijać i usuwania kluczy i kluczy tajnych.
* Przekazywanie parametrów, takich jak *szyfrowania* i *odszyfrować* do HelloKeyVault i stosuje się określone zmiany do magazynu kluczy.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie maszyny wirtualnej z hasłem usługi Key Vault](azure-stack-kv-deploy-vm-with-secret.md)

[Wdrożenie maszyny Wirtualnej przy użyciu certyfikatu usługi Key Vault](azure-stack-kv-push-secret-into-vm.md)
