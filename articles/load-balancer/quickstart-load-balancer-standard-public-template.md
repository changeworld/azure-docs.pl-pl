---
title: 'Szybki Start: Tworzenie standardowego modułu równoważenia obciążenia — szablon platformy Azure'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia standardowego modułu równoważenia obciążenia przy użyciu szablonu Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 28c4ca59b20fcdd8ab0249392dc0f13cbabf1a97
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75970635"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Szybki Start: Tworzenie standardowego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu Azure Resource Manager

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. W tym przewodniku szybki start przedstawiono sposób wdrażania szablonu Azure Resource Manager, który tworzy standardowy moduł równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych. Użycie szablonu Menedżer zasobów ma mniejszą liczbę kroków do porównania z innymi metodami wdrażania.

[Szablon Menedżer zasobów](../azure-resource-manager/templates/overview.md) to plik JavaScript Object Notation (JSON), który definiuje infrastrukturę i konfigurację projektu. Szablon używa składni deklaracyjnej, która pozwala na określenie, co zamierzasz wdrożyć, bez konieczności pisania sekwencji poleceń programowania, aby je utworzyć. Jeśli chcesz dowiedzieć się więcej na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację Menedżer zasobów i dokumentacja](/azure/azure-resource-manager/) [szablonu](/azure/templates/microsoft.network/loadbalancers).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Usługa Load Balancer w warstwie Standardowa obsługuje tylko standardowy publiczny adres IP. Podczas tworzenia standardowego modułu równoważenia obciążenia należy również utworzyć nowy, publiczny adres IP, który jest skonfigurowany jako fronton dla standardowej usługi równoważenia obciążenia.

Szablon używany w tym przewodniku szybki start to [szablon szybkiego startu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

W szablonie zdefiniowano wiele zasobów platformy Azure:

- **Microsoft.Network/loadBalancers**
- **Microsoft. Network/adresów publicipaddress**: dla modułu równoważenia obciążenia.
- **Microsoft.Network/networkSecurityGroups**
- **Microsoft. Network/virtualNetworks**
- **Microsoft. COMPUTE/virutalMachines** (3 z nich)
- **Microsoft. Network/adresów publicipaddress** (3 z nich): dla każdej z trzech maszyn wirtualnych.
- **Microsoft. Network/networkInterfaces** (3 z nich)
- **Microsoft. COMPUTE/virtualMachine/Extensions** (3 z nich): służy do konfigurowania usług IIS i stron sieci Web

Aby znaleźć więcej szablonów związanych z Azure Load Balancer, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Poczekaj, aż zobaczysz monit z konsoli programu.

1. Wybierz pozycję **Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz polecenie **Wklej**.

1. Wprowadź wartości.

   Wdrożenie szablonu tworzy trzy strefy dostępności. Strefy dostępności są obsługiwane tylko w [określonych regionach](../availability-zones/az-overview.md). Użyj jednego z obsługiwanych regionów. Jeśli nie masz pewności, wprowadź **centralną**.

   Nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** . Wymagana jest nazwa grupy zasobów w następnej sekcji.

Wdrożenie szablonu trwa około 10 minut. Po zakończeniu dane wyjściowe są podobne do:

![Szablon platformy Azure usługa Load Balancer w warstwie Standardowa Menedżer zasobów dane wyjściowe wdrożenia programu PowerShell](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell służy do wdrożenia szablonu. Oprócz Azure PowerShell można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślną nazwą grupy zasobów jest nazwa projektu z dołączoną **RG** .

1. Wybierz moduł równoważenia obciążenia. Nazwa domyślna to nazwa projektu z dołączonym **-lb** .

1. Skopiuj tylko część adresu IP z publicznego adresu IP, a następnie wklej ją na pasku adresu przeglądarki.

   ![Publiczny adres IP szablonu usługi równoważenia obciążenia w warstwie Standardowa Azure Menedżer zasobów](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    W przeglądarce zostanie wyświetlona domyślna strona serwera sieci Web Internet Information Services (IIS).

   ![Internetowy serwer usług IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można wymusić odświeżenie przeglądarki sieci Web od komputera klienckiego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby. W tym celu przejdź do Azure Portal, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono moduł równoważenia obciążenia w warstwie Standardowa, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu modułu równoważenia obciążenia, wykonano sondę kondycji, a następnie przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej, przejdź do samouczków dotyczących Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
