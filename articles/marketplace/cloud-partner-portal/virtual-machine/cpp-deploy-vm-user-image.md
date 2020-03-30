---
title: Wdrażanie maszyny Wirtualnej platformy Azure z dysku VHD użytkownika | Azure Marketplace
description: W tym artykule wyjaśniono, jak wdrożyć obraz dysku wirtualnego użytkownika w celu utworzenia wystąpienia maszyny Wirtualnej platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 957bc187b0123338947f62333c913cf82a6c66a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277960"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Wdrażanie maszyny Wirtualnej platformy Azure z dysku twardego użytkownika

W tym artykule wyjaśniono, jak wdrożyć uogólniony obraz wirtualnej maszyny Wirtualnej w celu utworzenia nowego zasobu maszyny Wirtualnej platformy Azure przy użyciu dostarczonego szablonu usługi Azure Resource Manager i skryptu programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Szablon wdrożenia VHD

Skopiuj szablon usługi Azure Resource Manager `VHDtoImage.json`dla [wdrożenia VHD](cpp-deploy-json-template.md) do pliku lokalnego o nazwie .  Edytuj ten plik, aby podać wartości dla następujących parametrów. 

|  **Parametr**             |   **Opis**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Istniejąca nazwa grupy zasobów platformy Azure.  Zazwyczaj używaj tego samego RG skojarzonego z magazynem kluczy  |
| Plik szablonu               | Pełna nazwa ścieżki do pliku`VHDtoImage.json`                                    |
| użytkownikStorageAccountName     | Nazwa konta magazynu                                                    |
| sNameForPublicIP           | Nazwa DNS publicznego adresu IP. Musi być mała litera                                  |
| subscriptionId             | Identyfikator subskrypcji platformy Azure                                                  |
| Lokalizacja                   | Standardowa lokalizacja geograficzna platformy Azure grupy zasobów                       |
| vmName                     | Nazwa maszyny wirtualnej                                                    |
| nazwa skarbca                  | Nazwa magazynu kluczy                                                          |
| vaultResourceGroup         | Grupa zasobów magazynu kluczy
| certyfikatUrl             | Adres URL certyfikatu, w tym wersja przechowywana w magazynie kluczy, na przykład:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl ( vhdUrl )                     | Adres URL wirtualnego dysku twardego                                                   |
| vmSize                     | Rozmiar wystąpienia maszyny wirtualnej                                           |
| publicIPAddressName        | Nazwa publicznego adresu IP                                                  |
| virtualNetworkName         | Nazwa sieci wirtualnej                                                    |
| nazwa nicName                    | Nazwa karty interfejsu sieciowego dla sieci wirtualnej                     |
| adminUserName              | Nazwa użytkownika konta administratora                                          |
| adminPassword              | Hasło administratora                                                          |
|  |  |


## <a name="powershell-script"></a>Skrypt programu PowerShell

Skopiuj i edytuj następujący `$storageaccount` `$vhdUrl` skrypt, aby podać wartości dla zmiennych i zmiennych.  Wykonaj go, aby utworzyć zasób maszyny Wirtualnej platformy Azure z istniejącego uogólnionego dysku wirtualnego.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu maszyny Wirtualnej można przystąpić do [certyfikacji obrazu maszyny Wirtualnej.](./cpp-certify-vm.md)
