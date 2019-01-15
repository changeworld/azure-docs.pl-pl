---
title: Wdrażanie maszyny Wirtualnej portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak wdrożyć maszynę wirtualną na podstawie wstępnie skonfigurowanej maszyny wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 3ec4e0b047e94dc7481c51390c6e4370fdea5efa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258805"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Wdróż maszynę wirtualną w portalu Azure Marketplace

W tym artykule wyjaśniono, jak wdrożyć wstępnie skonfigurowane maszyny wirtualnej (VM) z portalu Azure Marketplace przy użyciu dostarczonego skryptu programu Azure PowerShell.  Ten skrypt udostępnia również punkty końcowe usługi WinRM HTTP i HTTPS na maszynie Wirtualnej.  Ten skrypt wymaga, że masz już certyfikat, który został przekazany do usługi Azure Key Vault, zgodnie z opisem w [Tworzenie certyfikatów dla usługi Azure Key Vault](./cpp-create-key-vault-cert.md). 


## <a name="vm-deployment-template"></a>Szablon wdrożenia maszyny Wirtualnej

Przewodnik Szybki Start Szablon wdrożenia maszyny Wirtualnej platformy Azure, jest dostępna jako pliku online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Zawiera on następujące parametry:

|  **Parametr**        |   **Opis**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Nazwa konta magazynu                       |
| dnsNameForPublicIP    | Nazwa DNS dla publicznego adresu IP. Muszą być małymi literami.    |
| adminUserName         | Nazwa użytkownika administratora                          |
| adminPassword         | Hasło administratora                          |
| imagePublisher        | Wydawca obrazu                                   |
| imageOffer            | Oferta obrazu                                       |
| imageSKU              | Jednostka SKU obrazu                                         |
| vmSize                | Rozmiar maszyny Wirtualnej                                    |
| vmName                | Nazwa maszyny Wirtualnej                                    |
| vaultName             | Nazwa magazynu kluczy                             |
| vaultResourceGroup    | Grupa zasobów magazynu kluczy                   |
| certificateUrl        | Adres URL dla certyfikatu, w tym wersja w magazynie KeyVault, na przykład  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Skrypt wdrożenia

Edytuj następujący skrypt programu Azure PowerShell i wykonaj go, aby wdrożyć określonej maszyny Wirtualnej platformy Azure Marketplace.

```powershell

New-AzureRmResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu wstępnie skonfigurowanych maszyn wirtualnych, można skonfigurować i uzyskać dostęp do rozwiązań i usług, które zawiera lub użyć jej do dalszego rozwoju. 
