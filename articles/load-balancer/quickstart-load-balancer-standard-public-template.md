---
title: 'Szybki start: tworzenie modułu równoważenia obciążenia — szablon platformy Azure'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki start pokazuje, jak utworzyć moduł równoważenia obciążenia przy użyciu szablonu Usługi Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 175c5a36c873d16d50d5192a489133a01018e335
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474602"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Szybki start: tworzenie modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Ten przewodnik Szybki start pokazuje, jak wdrożyć szablon usługi Azure Resource Manager, który tworzy standardowy moduł równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych. Za pomocą szablonu Menedżera zasobów wykonuje mniej kroków w porównaniu do innych metod wdrażania.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Moduł równoważenia obciążenia i publiczne jednostki SKU IP muszą być zgodne. Podczas tworzenia standardowego modułu równoważenia obciążenia należy również utworzyć nowy standardowy publiczny adres IP, który jest skonfigurowany jako frontend dla standardowego modułu równoważenia obciążenia. Jeśli chcesz utworzyć podstawowy moduł równoważenia obciążenia, użyj [tego szablonu](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Firma Microsoft zaleca używanie standardowej jednostki SKU dla obciążeń produkcyjnych.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="58-122":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses)dla modułu równoważenia obciążenia i dla każdej z trzech maszyn wirtualnych.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Sieć Microsoft.Network/virtual Sieci**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 z nich)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 z nich)
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 z nich): służy do konfigurowania usług IIS i stron internetowych

Aby znaleźć więcej szablonów związanych z modułem równoważenia obciążenia platformy Azure, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz **pozycję Wypróbuj z** następującego bloku kodu, aby otworzyć usługę Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na platformie Azure.

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

   Poczekaj, aż pojawi się monit z konsoli.

1. Wybierz **opcję Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz polecenie **Wklej**.

1. Wprowadź wartości.

   Wdrożenie szablonu tworzy trzy strefy dostępności. Strefy dostępności są obsługiwane tylko w [niektórych regionach](../availability-zones/az-overview.md). Użyj jednego z obsługiwanych regionów. Jeśli nie masz pewności, wprowadź **centralus**.

   Nazwa grupy zasobów to nazwa projektu z **dołączenym rg.** Nazwa grupy zasobów jest potrzebna w następnej sekcji.

Wdrożenie szablonu zajmuje około 10 minut. Po zakończeniu dane wyjściowe są podobne do:

![Dane wyjściowe wdrożenia usługi Azure Standard Load Balance Balancer Resource Manager](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Program Azure PowerShell jest używany do wdrażania szablonu. Oprócz programu Azure PowerShell można również użyć witryny Azure portal, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Wybierz **grupę zasobów** z lewego okienka.

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to nazwa projektu z **dołączenym rg.**

1. Wybierz moduł równoważenia obciążenia. Jego domyślna nazwa to nazwa projektu z **dodatkiem -lb.**

1. Skopiuj tylko część adresu IP publicznego adresu IP, a następnie wklej go do paska adresu przeglądarki.

   ![Publiczny adres IP szablonu menedżera zasobów usługi Azure standard load balance balance balanceer](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    Przeglądarka wyświetla domyślną stronę serwera sieci Web internetowych internetowych usług informacyjnych (IIS).

   ![Internetowy serwer usług IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Aby wyświetlić moduł równoważenia obciążenia rozprowadzać ruch na wszystkich trzech maszyn wirtualnych, można wymusić odświeżenie przeglądarki sieci web z komputera klienckiego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne, usuń grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby. Aby to zrobić, przejdź do witryny Azure Portal, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono standardowy moduł równoważenia obciążenia, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu równoważenia obciążenia, wykonano sondę kondycji, a następnie przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej, przejdź do samouczków dotyczących modułu równoważenia obciążenia.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
