---
title: Wdrażanie maszyny wirtualnej z certyfikatem bezpiecznie przechowywane w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć maszynę wirtualną i Wypchnij certyfikatu na niego przy użyciu magazynu kluczy w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.openlocfilehash: 188b14c3199f9e4496a3c197aba0bb7f5b31d9ab
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104565"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Utwórz maszynę wirtualną i instalowania certyfikatu pobrane z usługi key vault usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Dowiedz się, jak utworzyć maszynę wirtualną (VM) Azure Stack z certyfikatem usługi key vault, zainstalowane.

## <a name="overview"></a>Przegląd

Certyfikaty są używane w wielu scenariuszach, takich jak uwierzytelnianie w usłudze Active Directory lub szyfrowanie ruchu w sieci web. Możesz bezpiecznie przechowywać certyfikaty jako wpisy tajne w magazynie kluczy usługi Azure Stack. Korzyści z używania usługi Azure Stack Key Vault to:

* Certyfikaty nie są widoczne w skrypcie, historii wiersza polecenia ani w szablonie.
* Proces zarządzania certyfikatami jest uproszczone.
* Masz kontrolę nad kluczami, uzyskujących dostęp do certyfikatów.

### <a name="process-description"></a>Opis procesu

W poniższych krokach opisano proces wymagany do certyfikatu do maszyny wirtualnej:

1. Utwórz magazyn kluczy tajnych.
2. Aktualizowanie pliku azuredeploy.parameters.json.
3. Wdróż szablon.

> [!NOTE]
> Można użyć tych kroków z usługi Azure Stack Development Kit lub z klienta zewnętrznego, jeśli są połączone za pośrednictwem sieci VPN.

## <a name="prerequisites"></a>Wymagania wstępne

* Należy subskrybować ofertę, która obejmuje usługę Key Vault.
* [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Tworzenie wpisu tajnego usługi Key Vault

Poniższy skrypt tworzy certyfikat w formacie pfx, utworzenie magazynu kluczy i zapisuje certyfikat w magazynie kluczy jako klucz tajny.

> [!IMPORTANT]
> Należy użyć `-EnabledForDeployment` parametru podczas tworzenia magazynu kluczy. Tego parametru gwarantuje, że usługi key vault można odwoływać się za pomocą szablonów usługi Azure Resource Manager.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

Po uruchomieniu skryptu poprzedniej, dane wyjściowe obejmują identyfikator URI klucza tajnego. Zanotuj ten identyfikator URI. Należy odwoływać się do niego w [certyfikatu wypychania do szablonu usługi Resource Manager dla Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Pobierz [maszyny wirtualnej push certyfikatu — windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) folderu szablonu na komputerze dewelopera. Ten folder zawiera `azuredeploy.json` i `azuredeploy.parameters.json` pliki, które będą potrzebne w następnych krokach.

Modyfikowanie `azuredeploy.parameters.json` zgodnie z własnymi wartościami środowiska. Parametry interesujące są nazwę magazynu, grupa zasobów magazynu i klucz tajny identyfikatora URI (tak jak, w poprzednim skrypcie). W poniższej sekcji pokazano przykładowy plik parametrów.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizowanie pliku azuredeploy.parameters.json

Aktualizacja `azuredeploy.parameters.json` plik z `vaultName`, identyfikator URI klucza tajnego, `VmName`i inne wartości dla każdego środowiska. Następujący plik JSON przykładowy plik parametrów szablonu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu przy użyciu poniższy skrypt programu PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Po pomyślnym wdrożeniu szablonu powoduje następujące dane wyjściowe:

![Wyniki wdrożenia szablonu](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Usługa Azure Stack wypycha certyfikatu do maszyny wirtualnej, podczas wdrażania. Lokalizacja certyfikatu zależy od systemu operacyjnego maszyny Wirtualnej:

* W Windows, certyfikat zostanie dodany do **LocalMachine** lokalizacją z magazynu certyfikatów, który użytkownik podał certyfikatu.
* W systemie Linux, certyfikat zostanie umieszczony w obszarze `/var/lib/waagent directory`, z nazwą pliku &lt;UppercaseThumbprint&gt;CRT dla X509 plik certyfikatu i &lt;UppercaseThumbprint&gt;.prv klucza prywatnego.

## <a name="retire-certificates"></a>Wycofaj certyfikaty

Wycofanie certyfikatów jest częścią proces zarządzania certyfikatami. Nie można usunąć starszą wersję certyfikatu, ale można ją wyłączyć za pomocą `Set-AzureKeyVaultSecretAttribute` polecenia cmdlet.

Poniższy przykład pokazuje, jak wyłączyć certyfikatu. Użyj własnych wartości dla **VaultName**, **nazwa**, i **wersji** parametrów.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie maszyny wirtualnej z hasłem usługi Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Zezwolić aplikacji na dostęp do usługi Key Vault](azure-stack-kv-sample-app.md)
