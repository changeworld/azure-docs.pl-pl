---
title: Konfigurowanie klucza magazynu dla Windows maszyn wirtualnych w usłudze Azure Resource Manager | Dokumentacja firmy Microsoft
description: Jak skonfigurować magazyn kluczy do użycia z maszyną wirtualną usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: 599b16f633d9a0de5165bdf5cb3d7b82abca655b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597714"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie usługi Key Vault dla maszyn wirtualnych w usłudze Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

W stosie usługi Azure Resource Manager wpisów tajnych/certyfikaty są modelowane jako zasoby, które są dostarczane przez dostawcę zasobów usługi Key Vault. Aby dowiedzieć się więcej o usłudze Key Vault, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Aby usługi Key Vault do użycia z maszynami wirtualnymi usługi Azure Resource Manager **EnabledForDeployment** właściwość usługi Key Vault musi być ustawiona na wartość true. Można to zrobić w różnych klientów.
> 2. Key Vault musi być utworzone w tej samej subskrypcji i lokalizacji co maszyna wirtualna.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault przy użyciu programu PowerShell
Aby utworzyć magazyn kluczy przy użyciu programu PowerShell, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Dla nowych magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Dla istniejących magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Skonfigurować usługi Key Vault za pomocą interfejsu wiersza polecenia
Aby utworzyć magazyn kluczy przy użyciu interfejsu wiersza polecenia (CLI), zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Dla interfejsu wiersza polecenia należy utworzyć magazyn kluczy przed przypisaniem zasady wdrażania. Możesz to zrobić za pomocą następującego polecenia:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault przy użyciu szablonów
Gdy używasz szablonu, musisz ustawić `enabledForDeployment` właściwość `true` dla zasobu usługi Key Vault.

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

Aby uzyskać inne opcje, które można skonfigurować podczas tworzenia magazynu kluczy przy użyciu szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
