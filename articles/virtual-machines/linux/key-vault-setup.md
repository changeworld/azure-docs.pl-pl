---
title: Konfigurowanie usługi Azure Key Vault dla maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Jak skonfigurować magazyn kluczy do użycia z maszyny wirtualnej platformy Azure Resource Manager 2.0 interfejsu wiersza polecenia.
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
ms.author: singhkay
ms.openlocfilehash: eca03a221014aafe89f07842b5ba9cfed0176faf
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936513"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Jak skonfigurować magazyn kluczy dla maszyn wirtualnych z 2.0 interfejsu wiersza polecenia platformy Azure

W stosie usługi Azure Resource Manager kluczy tajnych/certyfikaty są modelowane jako zasoby, które są udostępniane przez usługi Key Vault. Aby dowiedzieć się więcej na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-whatis.md) W kolejności dla usługi Key Vault ma być używany z maszyn wirtualnych Menedżera zasobów Azure *EnabledForDeployment* właściwość na magazynu kluczy musi być ustawiona na wartość true. W tym artykule przedstawiono sposób konfigurowania usługi Key Vault do użycia z programem Azure maszynach wirtualnych (VM) używa interfejsu wiersza polecenia Azure w wersji 2.0. 

Aby wykonać te kroki, należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Tworzenie magazynu kluczy i przypisać zasady wdrażania z [az keyvault utworzyć](/cli/azure/keyvault#az_keyvault_create). Poniższy przykład tworzy magazyn kluczy o nazwie `myKeyVault` w `myResourceGroup` grupy zasobów:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Zaktualizuj magazyn kluczy do użytku z maszynami wirtualnymi
Ustaw zasady wdrażania na istniejącego klucza magazynu z [az keyvault aktualizacji](/cli/azure/keyvault#az_keyvault_update). Następujące aktualizacje magazyn kluczy o nazwie `myKeyVault` w `myResourceGroup` grupy zasobów:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault za pomocą szablonów
Gdy używasz szablonu, musisz ustawić `enabledForDeployment` właściwości `true` klucza magazynu zasobów w następujący sposób:

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
Inne opcje, które można skonfigurować podczas tworzenia usługi Key Vault przy użyciu szablonów, zobacz [utworzyć magazyn kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
