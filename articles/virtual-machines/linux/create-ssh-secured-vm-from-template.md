---
title: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure na podstawie szablonu
description: Jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonu Menedżer zasobów za pomocą interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
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
ms.openlocfilehash: 3e32e9f17b5c48e18453724eb683ba2e86dd0cdb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036489"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonów Azure Resource Manager

Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu szablonu Azure Resource Manager i interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell. Aby utworzyć maszynę wirtualną z systemem Windows, zobacz [Tworzenie maszyny wirtualnej z systemem Windows na podstawie szablonu Menedżer zasobów](../windows/ps-template.md).

## <a name="templates-overview"></a>Przegląd szablonów

Szablony Azure Resource Manager są plikami JSON, które definiują infrastrukturę i konfigurację rozwiązania platformy Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Aby dowiedzieć się więcej o formacie szablonu i sposobie jego konstruowania, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Aby wyświetlić składnię JSON dla typów zasobów, zobacz [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definiowanie zasobów w szablonach usługi Azure Resource Manager).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure zwykle obejmuje dwa kroki:

1. Utwórz grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną.
1. Tworzy maszynę wirtualną.

Poniższy przykład tworzy maszynę wirtualną na podstawie [szablonu szybkiego startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Dla tego wdrożenia można używać tylko uwierzytelniania SSH. Po wyświetleniu monitu podaj wartość własnego klucza publicznego SSH, na przykład zawartość *~/.ssh/id_rsa. pub*. Jeśli musisz utworzyć parę kluczy SSH, zobacz [jak utworzyć i używać pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md). Oto kopia szablonu:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Aby uruchomić skrypt interfejsu wiersza polecenia, wybierz pozycję **Wypróbuj** , aby otworzyć usługę Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Ostatnie polecenie interfejsu wiersza polecenia platformy Azure pokazuje publiczny adres IP nowo utworzonej maszyny wirtualnej. Do nawiązania połączenia z maszyną wirtualną potrzebny jest publiczny adres IP. Zobacz następną sekcję tego artykułu.

W poprzednim przykładzie określono szablon przechowywany w serwisie GitHub. Możesz również pobrać lub utworzyć szablon i określić ścieżkę lokalną za pomocą parametru `--template-file`.

Oto kilka dodatkowych zasobów:

- Aby dowiedzieć się, jak opracowywać szablony Menedżer zasobów, zobacz [dokumentację dotyczącą Azure Resource Manager](/azure/azure-resource-manager/).
- Aby wyświetlić schematy maszyn wirtualnych platformy Azure, zobacz [Dokumentacja szablonu platformy Azure](/azure/templates/microsoft.compute/allversions).
- Aby zobaczyć więcej przykładów szablonów maszyn wirtualnych, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Następnie można obsłużyć SSH na potrzeby maszyny wirtualnej. Podaj własny publiczny adres IP z poprzedniego polecenia:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Następne kroki

W tym przykładzie utworzono podstawową maszynę wirtualną z systemem Linux. Aby uzyskać więcej Menedżer zasobów szablonów obejmujących struktury aplikacji lub tworzyć bardziej złożone środowiska, przejrzyj [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Aby dowiedzieć się więcej na temat tworzenia szablonów, Wyświetl składnię i właściwości JSON dla wdrożonych typów zasobów:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
