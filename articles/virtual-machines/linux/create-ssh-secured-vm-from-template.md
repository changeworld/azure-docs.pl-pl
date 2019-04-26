---
title: Tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure na podstawie szablonu | Dokumentacja firmy Microsoft
description: Jak utworzyć Maszynę wirtualną systemu Linux na podstawie szablonu usługi Resource Manager za pomocą wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 334f69390e4506c6db76c1814f8ec8f1e4417ee9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328004"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonów usługi Azure Resource Manager

Dowiedz się, jak utworzyć maszynę wirtualną (VM) systemu Linux przy użyciu szablonu usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud shell. Aby utworzyć maszynę wirtualną Windows, zobacz [Utwórz maszynę wirtualną Windows za pomocą szablonu usługi Resource Manager](../windows/ps-template.md).

## <a name="templates-overview"></a>Przegląd szablonów

Szablony usługi Azure Resource Manager są plikami JSON definiującymi infrastruktury i konfiguracji rozwiązania platformy Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Aby dowiedzieć się więcej o formacie szablonu i sposobie jego konstruowania, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Aby wyświetlić składnię JSON dla typów zasobów, zobacz [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definiowanie zasobów w szablonach usługi Azure Resource Manager).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure, zwykle obejmuje dwa kroki:

1. Utwórz grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną.
1. Tworzy maszynę wirtualną.

Poniższy przykład obejmuje tworzenie maszyny Wirtualnej z [szablonu szybkiego startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tylko uwierzytelnianie SSH jest dozwolone dla tego wdrożenia. Po wyświetleniu monitu podaj wartość własny klucz publiczny SSH, takie jak zawartość *~/.ssh/id_rsa.pub*. Jeśli musisz utworzyć parę kluczy SSH, zobacz [jak tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure](mac-create-ssh-keys.md). Oto kopię szablonu:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Aby uruchomić skrypt interfejsu wiersza polecenia, wybierz **wypróbuj** otworzyć usługa Azure Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey='$key' &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Ostatnie polecenie wiersza polecenia platformy Azure zawiera publiczny adres IP nowo utworzonej maszyny Wirtualnej. Potrzebujesz publicznego adresu IP, aby połączyć się z maszyną wirtualną. Zobacz następną sekcję tego artykułu.

W poprzednim przykładzie określono szablonu przechowywanego w usłudze GitHub. Możesz również pobrać lub utworzyć szablon i określ ścieżkę lokalną za pomocą `--template-file` parametru.

Poniżej przedstawiono dodatkowe zasoby:

- Aby dowiedzieć się, jak opracowywać szablony usługi Resource Manager, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/).
- Aby wyświetlić schematy maszyny wirtualnej platformy Azure, zobacz [odwołanie do szablonu usługi Azure](/azure/templates/microsoft.compute/allversions).
- Aby zobaczyć więcej przykładów szablonu maszyny wirtualnej, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Możesz następnie SSH z maszyną wirtualną, jak zwykle. Podaj własny publiczny adres IP z poprzedniego polecenia:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie utworzono Maszynę wirtualną systemu Linux podstawowe. Aby uzyskać dodatkowe szablony usługi Resource Manager, które obejmują struktur aplikacji lub utworzyć bardziej złożonych środowisk, przejdź [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Aby dowiedzieć się więcej na temat tworzenia szablonów, wyświetlić składnię JSON i właściwości dla typów zasobów, które można wdrożyć:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
