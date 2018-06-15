---
title: Utwórz Maszynę wirtualną systemu Linux przy użyciu szablonu platformy Azure z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Utwórz Maszynę wirtualną systemu Linux na platformie Azure przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure i szablonu usługi Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 620adcba9e136aa56e7a4867196ce5b9249b6072
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30905555"
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Tworzenie maszyny Wirtualnej systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia Azure szablonu usługi Azure Resource Manager
W tym artykule przedstawiono szybki sposób wdrożenia maszyny wirtualnej systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure i szablonu usługi Azure Resource Manager. Wykonanie czynności opisanych w tym artykule wymaga:

* konta platformy Azure ([skorzystaj z bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/));
* [Azure CLI 1.0](../../cli-install-nodejs.md) logowania `azure login`.
* Interfejs wiersza polecenia platformy Azure *musi działać w* trybie usługi Azure Resource Manager`azure config mode arm`.

Szybkie wdrożenie szablonu maszyny wirtualnej z systemem Linux jest możliwe również przy użyciu witryny [Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-command-summary) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](create-ssh-secured-vm-from-template.md) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami

## <a name="quick-command-summary"></a>Szybkie polecenia — podsumowanie
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik
Szablony umożliwiają tworzenie maszyn wirtualnych na platformie Azure przy użyciu ustawień, które można dostosować podczas uruchomienia, np. nazw użytkowników i nazw hostów. W tym artykule szablon platformy Azure został użyty do utworzenia maszyny wirtualnej z systemem Ubuntu oraz grupy zabezpieczeń sieci z otwartym portem 22 dla usługi SSH.

Szablony programu Azure Resource Manager są plikami JSON, za pomocą których można wykonywać proste, jednorazowe zadania, takie jak uruchamianie maszyny wirtualnej z systemem Ubuntu przedstawione w tym artykule.  Szablony platformy Azure mogą być również używane do tworzenia złożonych konfiguracji całych środowisk na platformie Azure, takich jak stos wdrażania do testowania, programowania lub produkcji.

## <a name="create-the-linux-vm"></a>Tworzenie maszyny wirtualnej systemu Linux
Poniższy przykład kodu pokazuje sposób wywoływania polecenia `azure group create` w celu jednoczesnego utworzenia grupy zasobów i wdrożenia maszyny wirtualnej systemu Linux zabezpieczonej przez protokół SSH za pomocą [szablonu usługi Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Należy pamiętać, że w tym przykładzie trzeba użyć nazw, które będą unikatowe w danym środowisku. W tym przykładzie użyto *myResourceGroup* jako nazwę grupy zasobów i *myVM* jako nazwę maszyny Wirtualnej.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Dane wyjściowe powinny wyglądać podobnie do następującego bloku:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

W tym przykładzie wdrożono maszynę wirtualną przy użyciu parametru `--template-uri`.  Możesz również pobrać bądź utworzyć szablon lokalnie i przekazać go przy użyciu parametru `--template-file` ze ścieżką do pliku szablonu jako argumentem. Interfejs wiersza polecenia platformy Azure monituje o parametry wymagane przez szablon.

## <a name="next-steps"></a>Kolejne kroki
Przeszukaj [galerię szablonów](https://azure.microsoft.com/documentation/templates/), aby odkryć kolejne platformy aplikacji do wdrożenia.

