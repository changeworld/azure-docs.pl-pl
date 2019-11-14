---
title: Skonfiguruj Key Vault dla maszyn wirtualnych z systemem Windows w programie Azure Resource Manager
description: Jak skonfigurować Key Vault do użycia z Azure Resource Manager maszyną wirtualną.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038205"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie Key Vault dla maszyn wirtualnych w programie Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

W Azure Resource Manager stosy klucze tajne/certyfikaty są modelowane jako zasoby dostarczone przez dostawcę zasobów Key Vault. Aby dowiedzieć się więcej na temat Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. Aby Key Vault były używane z maszynami wirtualnymi Azure Resource Manager, właściwość **EnabledForDeployment** w Key Vault musi mieć wartość true. Można to zrobić na różnych klientach.
> 2. Key Vault należy utworzyć w tej samej subskrypcji i lokalizacji co maszyna wirtualna.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Konfigurowanie Key Vault przy użyciu programu PowerShell
Aby utworzyć magazyn kluczy przy użyciu programu PowerShell, zobacz [Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu programu PowerShell](../../key-vault/quick-create-powershell.md).

W przypadku nowych magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

W przypadku istniejących magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Użyj interfejsu wiersza polecenia, aby skonfigurować Key Vault
Aby utworzyć magazyn kluczy za pomocą interfejsu wiersza polecenia (CLI), zobacz [Manage Key Vault using CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

W przypadku interfejsu wiersza polecenia należy utworzyć magazyn kluczy przed przypisaniem zasad wdrażania. Możesz to zrobić za pomocą następującego polecenia:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Następnie aby włączyć Key Vault do użycia z wdrożeniem szablonów, uruchom następujące polecenie:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Użyj szablonów, aby skonfigurować Key Vault
Podczas używania szablonu należy ustawić właściwość `enabledForDeployment` na `true` dla zasobu Key Vault.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Aby poznać inne opcje, które można skonfigurować podczas tworzenia magazynu kluczy za pomocą szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
