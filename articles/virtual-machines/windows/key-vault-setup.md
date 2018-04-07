---
title: Ustawianie klucza magazynu dla maszyn wirtualnych systemu Windows w usłudze Azure Resource Manager | Dokumentacja firmy Microsoft
description: Jak skonfigurować magazyn kluczy do użycia z maszyny wirtualnej platformy Azure Resource Manager.
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
ms.openlocfilehash: f8f094bfb0f304123cbdf719bec22185431aca5a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie magazynu kluczy dla maszyn wirtualnych w usłudze Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

W stosie usługi Azure Resource Manager kluczy tajnych/certyfikaty są modelowane jako zasoby, które są udostępniane przez dostawcę zasobów magazynu kluczy. Aby dowiedzieć się więcej na temat usługi Key Vault, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Aby Key Vault ma być używany z maszyn wirtualnych Menedżera zasobów Azure **EnabledForDeployment** właściwości magazynu kluczy musi ustawić na wartość true. Można to zrobić w różnych klientów.
> 2. Magazyn kluczy musi mogą być tworzone w tej samej subskrypcji i lokalizacji co maszyny wirtualnej.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault przy użyciu programu PowerShell
Aby utworzyć magazyn kluczy przy użyciu programu PowerShell, zobacz [wprowadzenie do usługi Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Dla nowych magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Dla istniejących magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Nam interfejsu wiersza polecenia, aby skonfigurować magazyn kluczy
Aby utworzyć magazyn kluczy przy użyciu interfejsu wiersza polecenia (CLI), zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Dla interfejsu wiersza polecenia należy utworzyć magazyn kluczy, przed przypisaniem zasady wdrażania. Możesz to zrobić za pomocą następującego polecenia:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault za pomocą szablonów
Gdy używasz szablonu, musisz ustawić `enabledForDeployment` właściwości `true` dla zasobu magazynu kluczy.

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

Inne opcje, które można skonfigurować podczas tworzenia magazynu kluczy przy użyciu szablonów, zobacz [utworzyć magazyn kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
