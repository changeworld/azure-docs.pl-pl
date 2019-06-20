---
title: 'Szybki start: Tworzenie standardowego modułu równoważenia obciążenia — szablon usługi Azure Resource Manager'
titlesuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia standardowego modułu równoważenia obciążenia przy użyciu szablonu usługi Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a95bedc8b2b395f856512ec49bae630666fe36da
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272887"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Szybki start: Tworzenie standardowego modułu równoważenia obciążenia do równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Ten przewodnik Szybki Start dowiesz się, jak wdrożyć szablon usługi Azure Resource Manager, aby utworzyć moduł równoważenia obciążenia standardowy, aby równoważyć obciążenie maszyn wirtualnych (VM).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia standardowego modułu równoważenia obciążenia, możesz również utworzyć nowy adres standardowego publicznego adresu IP, który jest skonfigurowany jako fronton dla standardowego modułu równoważenia obciążenia. Istnieje wiele metod, które umożliwiają tworzenie standardowego modułu równoważenia obciążenia. W tym przewodniku Szybki Start użyjesz programu Azure PowerShell, aby wdrożyć [szablonu usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby zrozumieć pojęcia związane z wdrażaniem i zarządzaniem nimi Twoich rozwiązań platformy Azure, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/). Aby znaleźć więcej pokrewne szablonu usługi Azure Load Balancer, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Wybierz **wypróbuj** z poniższy blok kodu, aby otworzyć usługa Azure Cloud shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

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

   Poczekaj, aż zostanie wyświetlony monit z konsoli.
2. Wybierz **kopiowania** z poprzedni blok kodu, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy w okienku konsoli shell, a następnie wybierz pozycję **Wklej**.
4. Wprowadź wartości.

   Wdrożenie szablonu tworzy trzy strefy dostępności.  Strefy dostępności są obsługiwane tylko w [niektóre regiony](../availability-zones/az-overview.md). Użyj jednej z obsługiwanych regionów. Jeśli nie masz pewności, wprowadź **centralus**.

   Nazwa grupy zasobów jest nazwą projektu **rg** dołączane. Potrzebna jest nazwa grupy zasobów w następnej sekcji.

Trwa około 10 minut do wdrożenia szablonu.

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **grup zasobów** z okienka po lewej stronie.
1. Wybierz grupę zasobów utworzoną w ostatniej sekcji.  Nazwa grupy zasobów domyślną jest nazwa projektu za pomocą **rg** dołączane.
1. Wybierz moduł równoważenia obciążenia.  Istnieje tylko jeden moduł równoważenia obciążenia. Domyślna nazwa jest nazwą projektu, przy użyciu **-lb** dołączane.
1. Skopiuj publiczny adres IP (tylko część adresu IP), a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć moduł równoważenia obciążenia rozdziela ruch między wszystkie trzy maszyny wirtualne, możesz wymusić odświeżenie przeglądarki sieci web z komputera klienckiego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, moduł równoważenia obciążenia i wszystkich powiązanych zasobów. Aby to zrobić, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia w witrynie Azure portal, a następnie wybierz **Usuń grupę zasobów**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono standardowego modułu równoważenia obciążenia, dołączono maszyny wirtualne do niego, skonfigurowane reguły modułu równoważenia obciążenia ruchu, sondy kondycji i następnie przetestowano moduł równoważenia obciążenia. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
