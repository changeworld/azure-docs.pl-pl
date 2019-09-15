---
title: Konfigurowanie Azure Key Vault dla maszyn wirtualnych z systemem Linux | Microsoft Docs
description: Jak skonfigurować Key Vault do użycia z Azure Resource Manager maszyną wirtualną za pomocą interfejsu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: cbc8b6be09fcf4232636b580dc0c62482b83bd60
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002157"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak skonfigurować Key Vault dla maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

W stosie Azure Resource Manager klucze tajne/certyfikaty są modelowane jako zasoby dostarczone przez Key Vault. Aby dowiedzieć się więcej na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/key-vault-overview.md) Aby można było używać Key Vault z maszynami wirtualnymi Azure Resource Manager, właściwość *EnabledForDeployment* w Key Vault musi mieć wartość true. W tym artykule opisano sposób konfigurowania Key Vault do użycia z maszynami wirtualnymi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. 

Aby wykonać te kroki, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Utwórz magazyn kluczy i przypisz zasady wdrażania za pomocą [AZ Key magazynu Create](/cli/azure/keyvault). Poniższy przykład tworzy magazyn kluczy o nazwie `myKeyVault` `myResourceGroup` w grupie zasobów:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizowanie Key Vault do użycia z maszynami wirtualnymi
Ustaw zasady wdrażania dla istniejącego magazynu kluczy za pomocą opcji [AZ Key magazynu Update](/cli/azure/keyvault). Następujące aktualizuje Magazyn kluczy o nazwie `myKeyVault` `myResourceGroup` w grupie zasobów:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Użyj szablonów, aby skonfigurować Key Vault
Korzystając z szablonu, należy ustawić `enabledForDeployment` właściwość na `true` dla zasobu Key Vault w następujący sposób:

```json
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
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać inne opcje, które można skonfigurować podczas tworzenia Key Vault przy użyciu szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
