---
title: Wdrażanie maszyny wirtualnej z poziomu portalu Azure Marketplace
description: Wyjaśnia, jak wdrożyć maszynę wirtualną na podstawie wstępnie skonfigurowanej maszyny wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 2888d7643fd4f624634dc2ec520bec6e753382f1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816828"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Wdrażanie maszyny wirtualnej z poziomu portalu Azure Marketplace

W tym artykule wyjaśniono, jak wdrożyć wstępnie skonfigurowaną maszynę wirtualną z portalu Azure Marketplace przy użyciu podanego skryptu Azure PowerShell.  Ten skrypt udostępnia również punkty końcowe HTTP i HTTPS usługi WinRM na maszynie wirtualnej.  Skrypt wymaga już przekazania certyfikatu do Azure Key Vault, zgodnie z opisem w temacie [Tworzenie certyfikatów dla Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Szablon wdrożenia maszyny wirtualnej

Szablon wdrożenia maszyny wirtualnej z przewodnikiem Szybki Start jest dostępny jako plik online [azuredeploy. JSON](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Zawiera następujące parametry:

|  **Konstruktora**        |   **Opis**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Nazwa konta magazynu                       |
| dnsNameForPublicIP    | Nazwa DNS publicznego adresu IP. Musi być małymi literami.    |
| adminUserName         | Nazwa użytkownika administratora                          |
| adminPassword         | Hasło administratora                          |
| imagePublisher        | Wydawca obrazu                                   |
| imageOffer            | Oferta obrazu                                       |
| imageSKU              | Jednostka SKU obrazu                                         |
| vmSize                | Rozmiar maszyny wirtualnej                                    |
| vmName                | Nazwa maszyny wirtualnej                                    |
| vaultName             | Nazwa magazynu kluczy                             |
| vaultResourceGroup    | Grupa zasobów magazynu kluczy                   |
| certificateUrl        | Adres URL certyfikatu, w tym wersja w magazynie kluczy, na przykład `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Skrypt wdrażania

Edytuj następujący skrypt Azure PowerShell i wykonaj go w celu wdrożenia określonej maszyny wirtualnej portalu Azure Marketplace.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Następne kroki

Po wdrożeniu wstępnie skonfigurowanej maszyny wirtualnej można skonfigurować i uzyskać dostęp do rozwiązań i usług, które zawiera, lub użyć jej do dalszej kompilacji. 
