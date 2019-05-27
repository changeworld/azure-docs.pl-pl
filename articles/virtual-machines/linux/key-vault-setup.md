---
title: Konfigurowanie usługi Azure Key Vault dla maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Jak skonfigurować magazyn kluczy do użycia z maszyną wirtualną usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
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
ms.openlocfilehash: 61be027cd1c919897ff62dd8b1beec4c7fb9b420
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966216"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak skonfigurować magazyn kluczy dla maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure

W stosie usługi Azure Resource Manager wpisów tajnych/certyfikaty są modelowane jako zasoby, które są dostarczane przez usługę Key Vault. Aby dowiedzieć się więcej na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-whatis.md) Aby usługi Key Vault do użycia z maszynami wirtualnymi Azure Resource Manager *EnabledForDeployment* właściwość usługi Key Vault musi być ustawiona na wartość true. W tym artykule pokazano, jak skonfigurować magazyn kluczy do użycia w usłudze Azure virtual machines (VMs) przy użyciu wiersza polecenia platformy Azure. 

Aby wykonać te kroki, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Tworzenie magazynu kluczy i przypisz zasady wdrażania z [tworzenie az keyvault](/cli/azure/keyvault). Poniższy przykład obejmuje tworzenie usługi key vault o nazwie `myKeyVault` w `myResourceGroup` grupy zasobów:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizacja usługi Key Vault do użytku z maszynami wirtualnymi
Ustaw zasady wdrażania na istniejący klucz magazynu za pomocą [az keyvault update](/cli/azure/keyvault). Następujące aktualizacje usługi key vault o nazwie `myKeyVault` w `myResourceGroup` grupy zasobów:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault przy użyciu szablonów
Gdy używasz szablonu, musisz ustawić `enabledForDeployment` właściwość `true` dla klucza magazynu zasobów w następujący sposób:

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

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać inne opcje, które można skonfigurować podczas tworzenia usługi Key Vault przy użyciu szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
