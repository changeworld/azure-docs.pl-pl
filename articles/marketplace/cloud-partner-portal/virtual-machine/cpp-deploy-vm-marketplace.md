---
title: Wdrażanie maszyny wirtualnej z portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak wdrożyć maszynę wirtualną z wstępnie skonfigurowanej maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 7d5269cf8865faeb65356bc8fd3eea087cb7653c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277977"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Wdrażanie maszyny wirtualnej z portalu Azure Marketplace

W tym artykule wyjaśniono, jak wdrożyć wstępnie skonfigurowaną maszynę wirtualną (VM) z portalu Azure Marketplace przy użyciu dostarczonego skryptu programu Azure PowerShell.  Ten skrypt udostępnia również punkty końcowe HTTP i HTTPS w programie WinRM na maszynie wirtualnej.  Skrypt wymaga, aby certyfikat został już przekazany do usługi Azure Key Vault, zgodnie z opisem w [artykule Tworzenie certyfikatów dla usługi Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Szablon wdrażania maszyny Wirtualnej

Szablon wdrażania maszyny Wirtualnej platformy Azure szybki start jest dostępny jako plik online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Zawiera następujące parametry:

|  **Parametr**        |   **Opis**                                 |
|  -------------        |   ---------------                                 |
| nowyName konta administracyjnego | Nazwa konta magazynu                       |
| dnsNameForPublicIP    | Nazwa DNS dla publicznego adresu IP. Musi być małe litery.    |
| adminUserName         | Nazwa użytkownika administratora                          |
| adminPassword         | Hasło administratora                          |
| imagePublisher        | Wydawca obrazu                                   |
| imageOffer            | Oferta graficzna                                       |
| imageSKU              | SKU obrazu                                         |
| vmSize                | Rozmiar maszyny Wirtualnej                                    |
| vmName                | Nazwa maszyny Wirtualnej                                    |
| nazwa skarbca             | Nazwa magazynu kluczy                             |
| vaultResourceGroup    | Grupa zasobów magazynu kluczy                   |
| certyfikatUrl        | Adres URL certyfikatu, w tym wersji w keyvault, na przykład`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Skrypt wdrażania

Edytuj następujący skrypt programu Azure PowerShell i wykonaj go, aby wdrożyć określoną maszynę wirtualną w portalu Azure Marketplace.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Następne kroki

Po wdrożeniu wstępnie skonfigurowane maszyny Wirtualnej, można skonfigurować i uzyskać dostęp do rozwiązań i usług, które zawiera lub używać go do dalszego rozwoju. 
