---
title: Wdrażanie maszyny Wirtualnej z zabezpieczonym hasłem w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć Maszynę wirtualną przy użyciu hasła przechowywane w usłudze Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3318e52b29723eaa08d8c3a4fba18e278e6cfe9c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487757"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Utwórz maszynę wirtualną przy użyciu bezpiecznego hasła przechowywane w usłudze Azure Stack Key Vault

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Tym artykule omówiono wdrażanie maszyny wirtualnej systemu Windows Server przy użyciu hasła przechowywane w usłudze Azure Stack Key Vault. Przy użyciu hasła usługi key vault jest bezpieczniejszy niż przekazywanie hasła w postaci zwykłego tekstu.

## <a name="overview"></a>Przegląd

Można przechowywać wartości, takie jak hasła, jako wpisu tajnego w magazynie kluczy usługi Azure Stack. Po utworzeniu wpisu tajnego, możesz odwoływać się do niej w szablonach usługi Azure Resource Manager. Przy użyciu kluczy tajnych w usłudze Resource Manager zapewnia następujące korzyści:

* Nie trzeba ręcznie wprowadzać hasła za każdym razem, wdrażanie zasobu.
* Można określić, które użytkownicy lub jednostki usługi dostęp do klucza tajnego.

## <a name="prerequisites"></a>Wymagania wstępne

* Należy subskrybować ofertę, która obejmuje usługę Key Vault.
* [Zainstaluj program PowerShell dla usługi Azure Stack.](azure-stack-powershell-install.md)
* [Konfigurowanie środowiska programu PowerShell.](azure-stack-powershell-configure-user.md)

W poniższych krokach opisano proces wymagany do utworzenia maszyny wirtualnej przez pobieranie hasła przechowywane w usłudze Key Vault:

1. Utwórz magazyn kluczy tajnych.
2. Aktualizowanie pliku azuredeploy.parameters.json.
3. Wdróż szablon.

> [!NOTE]  
> Można użyć tych kroków z usługi Azure Stack Development Kit lub z klienta zewnętrznego, jeśli są połączone za pośrednictwem sieci VPN.

## <a name="create-a-key-vault-secret"></a>Tworzenie wpisu tajnego usługi Key Vault

Poniższy skrypt tworzy magazyn kluczy i przechowuje hasła w usłudze key vault jako klucz tajny. Użyj `-EnabledForDeployment` parametru podczas tworzenia magazynu kluczy. Tego parametru gwarantuje, że usługi key vault można odwoływać się za pomocą szablonów usługi Azure Resource Manager.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Po uruchomieniu skryptu poprzedniej, dane wyjściowe obejmują identyfikator URI klucza tajnego. Zanotuj ten identyfikator URI. Należy odwoływać się do niego w [Windows wdrażanie maszyny wirtualnej za pomocą hasła w szablonie usługi key vault](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Pobierz [101-hasło maszyny wirtualnej bezpieczny](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) folderu na komputerze deweloperskim. Ten folder zawiera `azuredeploy.json` i `azuredeploy.parameters.json` pliki, które będą potrzebne w następnych krokach.

Modyfikowanie `azuredeploy.parameters.json` zgodnie z własnymi wartościami środowiska. Parametry interesujące są nazwę magazynu, grupa zasobów magazynu i klucz tajny identyfikatora URI (tak jak, w poprzednim skrypcie). Następujący plik jest przykładowy plik parametrów:

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizowanie pliku azuredeploy.parameters.json

Za pomocą identyfikatora URI magazynu kluczy, secretName, adminUsername wartości maszyny wirtualnej dla każdego środowiska, należy zaktualizować pliku azuredeploy.parameters.json. Następujący plik JSON przykładowy plik parametrów szablonu:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Teraz można wdrożyć szablon przy użyciu poniższy skrypt programu PowerShell:

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Po pomyślnym wdrożeniu szablonu powoduje następujące dane wyjściowe:

![Dane wyjściowe wdrożenia](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wdróż przykładową aplikację w usłudze Key Vault](azure-stack-key-vault-sample-app.md)
* [Wdrażanie maszyny Wirtualnej z certyfikatem usługi Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
