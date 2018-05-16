---
title: Wdrożenie maszyny Wirtualnej z zabezpieczonym hasłem na stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć maszynę Wirtualną przy użyciu hasła przechowywane w magazynie kluczy stosu Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: 4239eb31afd4abc8b3555f0ee353f5d96716d623
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Utwórz maszynę wirtualną przy użyciu bezpiecznego hasła przechowywane w magazynie kluczy stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Kroki tego artykułu, do wdrażania maszyny wirtualnej systemu Windows Server przy użyciu hasła przechowywane w magazynie kluczy stosu Azure. Przy użyciu hasła magazynu kluczy jest bezpieczniejsza niż przekazywanie hasła w postaci zwykłego tekstu.

## <a name="overview"></a>Przegląd

Wartości, takie jak hasła można zapisać jako klucza tajnego w magazynie kluczy Azure stosu. Po utworzeniu klucza tajnego można odwoływać się do niej w szablonach usługi Azure Resource Manager. Użycie kluczy tajnych z usługą Resource Manager zapewnia następujące korzyści:

* Nie trzeba ręcznie wprowadź hasło w każdym wdrożeniu zasobu.
* Można określić, których użytkowników lub nazwy główne usług można uzyskać dostępu do klucza tajnego.

## <a name="prerequisites"></a>Wymagania wstępne

* Oferta obejmuje usługi Key Vault musi subskrybować.
* [Instalowanie programu PowerShell Azure stosu.](azure-stack-powershell-install.md)
* [Konfigurowanie środowiska PowerShell użytkownika stosu Azure.](azure-stack-powershell-configure-user.md)

W poniższych krokach opisano proces wymagane do utworzenia maszyny wirtualnej przez pobranie hasła przechowywane w magazynie kluczy:

1. Tworzenie magazynu klucz tajny.
2. Zaktualizuj plik azuredeploy.parameters.json.
3. Wdrażanie szablonu.

>[UWAGA] Korzystania z tych kroków, z platformy Azure stosu Development Kit lub klientom zewnętrznym, jeśli są połączone za pośrednictwem sieci VPN.

## <a name="create-a-key-vault-secret"></a>Tworzenie magazynu klucz tajny

Poniższy skrypt tworzy magazyn kluczy i przechowuje hasła w magazynie kluczy jako klucz tajny. Użyj `-EnabledForDeployment` parametru podczas tworzenia magazynu kluczy. Tego parametru zapewnia, że magazyn kluczy mogą być przywoływane z szablonów usługi Azure Resource Manager.

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

Po uruchomieniu skryptu poprzednie dane wyjściowe zawiera tajny identyfikatora URI. Zanotuj tego identyfikatora URI. Masz do utworzenia odwołania w [wdrażania maszyny wirtualnej systemu Windows z hasłem w szablonie magazynu kluczy](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Pobierz [101-vm bezpiecznego hasła](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) folderu na komputerze deweloperskim. Ten folder zawiera `azuredeploy.json` i `azuredeploy.parameters.json` pliki, które będą potrzebne w następnych krokach.

Modyfikowanie `azuredeploy.parameters.json` plików zgodnie z własnymi wartościami środowiska. Parametry szczególnie ważne są nazwę magazynu, grupy zasobów magazynu i klucz tajny identyfikatora URI (jak generowane przez poprzednie skryptu). Przykładem pliku parametrów jest następującego pliku:

## <a name="update-the-azuredeployparametersjson-file"></a>Zaktualizuj plik azuredeploy.parameters.json

Zaktualizuj plik azuredeploy.parameters.json o identyfikatorze URI KeyVault, secretName, adminUsername wartości maszyny wirtualnej, zgodnie z harmonogramem środowiska. Następujący plik JSON przedstawiono przykład pliku parametrów szablonu:

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Teraz można wdrożyć szablon przy użyciu następujący skrypt programu PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Jeśli szablon został wdrożony pomyślnie, wynikiem następujące dane wyjściowe:

![Dane wyjściowe wdrażanie](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie przykładowej aplikacji z magazynu kluczy](azure-stack-kv-sample-app.md)

[Wdrożenie maszyny Wirtualnej przy użyciu certyfikatu usługi Key Vault](azure-stack-kv-push-secret-into-vm.md)
