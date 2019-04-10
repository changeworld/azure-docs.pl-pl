---
title: Zezwalaj na aplikacje do pobierania wpisów tajnych w usłudze Azure Stack Key Vault | Dokumentacja firmy Microsoft
description: Korzystanie z przykładowej aplikacji do pracy z usługą Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 7ca02b35cd8f302b856b2d7fcbfb5bac304f1a00
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358617"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Przykładowa aplikacja, która używa kluczy i wpisów tajnych przechowywanych w magazynie kluczy

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Wykonaj kroki opisane w tym artykule, aby uruchomić przykładową aplikację **HelloKeyVault** , który pobiera klucze i wpisy tajne z klucza magazynu w usłudze Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne

Można zainstalować następujące wstępnie wymagane składniki z [Azure Stack Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klient zewnętrznych przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Zainstaluj [modułów programu Azure PowerShell dla usługi Azure Stack zgodnego](azure-stack-powershell-install.md).
* Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Utwórz i Pobierz magazyn kluczy i ustawień aplikacji

Aby przygotować się do przykładowej aplikacji:

* Tworzenie magazynu kluczy w usłudze Azure Stack.
* Zarejestrować aplikację w usłudze Azure Active Directory (Azure AD).

Aby przygotować się do przykładowej aplikacji, można użyć witryny Azure portal lub programu PowerShell. W tym artykule przedstawiono sposób tworzenia magazynu kluczy i zarejestrowanie aplikacji przy użyciu programu PowerShell.

> [!NOTE]
> Domyślnie skrypt programu PowerShell tworzy nową aplikację w usłudze Active Directory. Jednak można zarejestrować jeden z istniejących aplikacji.

Przed uruchomieniem poniższego skryptu, pamiętaj o podaniu wartości `aadTenantName` i `applicationPassword` zmiennych. Jeśli nie określisz wartości `applicationPassword`, ten skrypt generuje losowe hasło.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
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
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

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

Na poniższej ilustracji przedstawiono dane wyjściowe skryptu użyty do utworzenia magazynu kluczy:

![Magazyn kluczy przy użyciu kluczy dostępu](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Zwróć uwagę na **VaultUrl**, **AuthClientId**, i **AuthClientSecret** wartości zwróconych z poprzednim skrypcie. Te wartości można używać do uruchamiania **HelloKeyVault** aplikacji.

## <a name="download-and-configure-the-sample-application"></a>Pobieranie i konfigurowanie przykładowej aplikacji

Pobierz przykładowe usługi key vault z platformy Azure [usługi Key Vault klient przykłady](https://www.microsoft.com/download/details.aspx?id=45343) strony. Wyodrębnij zawartość pliku .zip na deweloperskiej stacji roboczej. Istnieją dwie aplikacje w folderze samples. W tym artykule wykorzystano **HelloKeyVault**.

Aby załadować **HelloKeyVault** próbki:

1. Przejdź do **Microsoft.Azure.KeyVault.Samples**, **przykłady**, **HelloKeyVault** folderu.
2. Otwórz **HelloKeyVault** aplikacji w programie Visual Studio.

### <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

W programie Visual Studio:

1. Otwórz plik HelloKeyVault\App.config i Znajdź `<appSettings>` elementu.
2. Aktualizacja **VaultUrl**, **AuthClientId**, i **AuthClientSecret** klucze za pomocą wartości zwrócone przez te wykorzystywane do utworzenia magazynu kluczy. Domyślnie plik App.config ma symbol zastępczy `AuthCertThumbprint`. Zamiast tego symbolu zastępczego z `AuthClientSecret`.

   ![Ustawienia aplikacji](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Ponownie skompiluj rozwiązanie.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu **HelloKeyVault**, aplikacji, loguje się do usługi Azure AD, a następnie używa `AuthClientSecret` token uwierzytelniania do magazynu kluczy w usłudze Azure Stack.

Możesz użyć **HelloKeyVault** próbki do:

* Wykonaj podstawowe operacje, takie jak tworzenie, szyfrowanie, opakowywanie i usuwanie kluczy i wpisów tajnych.
* Przekazywanie parametrów, takich jak `encrypt` i `decrypt` do **HelloKeyVault**i stosowanie określonych zmian do magazynu kluczy.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie maszyny Wirtualnej z hasłem usługi Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Wdrażanie maszyny Wirtualnej z certyfikatem usługi Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
