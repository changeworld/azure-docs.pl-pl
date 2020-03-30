---
title: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure na podstawie szablonu
description: Jak utworzyć maszynę wirtualną systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure na podstawie szablonu Menedżera zasobów
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969525"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak utworzyć maszynę wirtualną systemu Linux za pomocą szablonów usługi Azure Resource Manager

Dowiedz się, jak utworzyć maszynę wirtualną systemu Linux (VM) przy użyciu szablonu usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure z powłoki usługi Azure Cloud. Aby utworzyć maszynę wirtualną systemu Windows, zobacz [Tworzenie maszyny wirtualnej systemu Windows na podstawie szablonu Menedżera zasobów](../windows/ps-template.md).

## <a name="templates-overview"></a>Przegląd szablonów

Szablony usługi Azure Resource Manager to pliki JSON, które definiują infrastrukturę i konfigurację rozwiązania platformy Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Aby dowiedzieć się więcej o formacie szablonu i sposobie jego konstruowania, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Aby wyświetlić składnię JSON dla typów zasobów, zobacz [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definiowanie zasobów w szablonach usługi Azure Resource Manager).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Tworzenie maszyny wirtualnej platformy Azure zwykle obejmuje dwa kroki:

1. Utwórz grupę zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną.
1. Tworzy maszynę wirtualną.

Poniższy przykład tworzy maszynę wirtualną z [szablonu Szybki start platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tylko uwierzytelnianie SSH jest dozwolone dla tego wdrożenia. Po wyświetleniu monitu podaj wartość własnego klucza publicznego SSH, takiego jak zawartość *pliku ~/.ssh/id_rsa.pub*. Jeśli chcesz utworzyć parę kluczy SSH, zobacz [Jak utworzyć i używać pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](mac-create-ssh-keys.md). Oto kopia szablonu:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Aby uruchomić skrypt interfejsu wiersza polecenia, wybierz pozycję **Spróbuj,** aby otworzyć powłokę usługi Azure Cloud. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**:

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

Ostatnie polecenie interfejsu wiersza polecenia platformy Azure zawiera publiczny adres IP nowo utworzonej maszyny Wirtualnej. Aby połączyć się z maszyną wirtualną, potrzebny jest publiczny adres IP. Zobacz następną sekcję tego artykułu.

W poprzednim przykładzie określono szablon przechowywany w usłudze GitHub. Można również pobrać lub utworzyć szablon i określić ścieżkę lokalną z parametrem. `--template-file`

Oto kilka dodatkowych zasobów:

- Aby dowiedzieć się, jak tworzyć szablony Usługi Resource Manager, zobacz [dokumentację usługi Azure Resource Manager](/azure/azure-resource-manager/).
- Aby wyświetlić schematy maszyny wirtualnej platformy Azure, zobacz [odwołanie do szablonu platformy Azure](/azure/templates/microsoft.compute/allversions).
- Aby wyświetlić więcej przykładów szablonów maszyn wirtualnych, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Następnie można SSH do maszyny Wirtualnej w zwykły sposób. Podaj własny publiczny adres IP z poprzedniego polecenia:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Następne kroki

W tym przykładzie utworzono podstawową maszynę wirtualną z systemem Linux. Aby uzyskać więcej szablonów Menedżera zasobów, które zawierają struktury aplikacji lub tworzenie bardziej złożonych środowisk, przejrzyj [szablony Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Aby dowiedzieć się więcej na temat tworzenia szablonów, wyświetl składnię JSON i właściwości wdrożonych typów zasobów:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Sieć Microsoft.Network/virtual Sieci](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
