---
title: Konfigurowanie usługi Azure Key Vault dla maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Jak skonfigurować magazyn kluczy do użycia z maszyną wirtualną usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: 736a30fe83ff26cd1dd4f197de9e7db925b7243b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671371"
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

## <a name="next-steps"></a>Następne kroki
Aby uzyskać inne opcje, które można skonfigurować podczas tworzenia usługi Key Vault przy użyciu szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
