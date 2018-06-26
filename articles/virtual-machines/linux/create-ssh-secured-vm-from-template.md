---
title: Utworzyć Maszynę wirtualną systemu Linux na platformie Azure na podstawie szablonu | Dokumentacja firmy Microsoft
description: Sposób użycia interfejsu wiersza polecenia Azure można utworzyć Maszynę wirtualną systemu Linux na podstawie szablonu usługi Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/30/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc2b4dde9796336112d6c8a68d16d0b3006b3fee
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936418"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Tworzenie maszyny wirtualnej systemu Linux przy użyciu szablonów usługi Azure Resource Manager
W tym artykule przedstawiono szybki sposób wdrożenia maszyny wirtualnej systemu Linux (VM) z szablonów usługi Azure Resource Manager i wiersza polecenia platformy Azure. 


## <a name="templates-overview"></a>Przegląd szablonów
Szablony usługi Azure Resource Manager są plikami JSON, definiujące infrastrukturze i konfiguracji rozwiązania Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Aby dowiedzieć się więcej o formacie szablonu i sposobu tworzenia, zobacz [Tworzenie pierwszego szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-create-first-template.md). Aby wyświetlić składnię JSON dla typów zasobów, zobacz [Define resources in Azure Resource Manager templates](/azure/templates/) (Definiowanie zasobów w szablonach usługi Azure Resource Manager).


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroupVM* w *eastus* regionu:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Poniższy przykład tworzy Maszynę wirtualną z [tego szablonu usługi Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create). Dozwolone jest tylko uwierzytelnianie SSH. Po wyświetleniu monitu podaj wartość własny klucz publiczny SSH, takie jak zawartość *~/.ssh/id_rsa.pub*. Jeśli musisz utworzyć parę kluczy SSH, zobacz [sposobu tworzenia i używania parę kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

W poprzednim przykładzie wybrano szablon przechowywany w serwisie GitHub. Można również pobrać lub utworzyć szablon i określ ścieżkę lokalną z `--template-file` parametru.


## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
Aby SSH do maszyny Wirtualnej, należy uzyskać publiczny adres IP z [az maszyny wirtualnej pokazu](/cli/azure/vm#az-vm-show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

Można następnie SSH do maszyny Wirtualnej, jak zwykle. Podaj własny publiczny adres IP z poprzedniego polecenia:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie utworzono podstawowej maszyny Wirtualnej systemu Linux. Aby uzyskać więcej szablonów Menedżera zasobów, które obejmują struktur aplikacji lub utworzyć bardziej złożonych środowiskach, przejdź [galerię szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/).