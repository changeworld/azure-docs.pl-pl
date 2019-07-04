---
title: 'Szybki start: Tworzenie standardowego modułu równoważenia obciążenia — szablon usługi Azure Resource Manager'
titlesuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia standardowego modułu równoważenia obciążenia przy użyciu szablonu usługi Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 00dbb29cac30f2a3bbecf71727c79617e24af3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441312"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Szybki start: Tworzenie standardowego modułu równoważenia obciążenia do równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Ten przewodnik Szybki Start pokazano, jak wdrożyć szablon usługi Azure Resource Manager, który tworzy standardowego modułu równoważenia obciążenia do równoważenia obciążenia maszyn wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Load Balancer w warstwie standardowa obsługuje tylko standardowego publicznego adresu IP. Podczas tworzenia modułu równoważenia obciążenia standardowego, możesz również utworzyć nowy adres standardowego publicznego adresu IP, który jest skonfigurowany jako fronton modułu równoważenia obciążenia standardowego.

Wiele metod umożliwia tworzenie standardowego modułu równoważenia obciążenia. W tym przewodniku Szybki Start użyjesz programu Azure PowerShell, aby wdrożyć [szablonu usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania.

Aby zrozumieć pojęcia związane z wdrażaniem i zarządzaniem nimi Twoich rozwiązań platformy Azure, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/). Aby znaleźć więcej szablonów, które są powiązane z usługi Azure Load Balancer, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Wybierz **wypróbuj** z poniższy blok kodu, aby otworzyć usługi Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do usługi Azure.

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

1. Wybierz **kopiowania** z poprzedni blok kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy w okienku konsoli shell, a następnie wybierz pozycję **Wklej**.

1. Wprowadź wartości.

   Wdrożenie szablonu tworzy trzy strefy dostępności. Strefy dostępności są obsługiwane tylko w [niektóre regiony](../availability-zones/az-overview.md). Użyj jednej z obsługiwanych regionów. Jeśli nie masz pewności, wprowadź **centralus**.

   Nazwa grupy zasobów jest nazwą projektu **rg** dołączane. Potrzebna jest nazwa grupy zasobów w następnej sekcji.

Trwa około 10 minut do wdrożenia szablonu.

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **grup zasobów** z okienka po lewej stronie.

1. Wybierz grupę zasobów, który został utworzony w poprzedniej sekcji. Nazwa grupy zasobów domyślną jest nazwa projektu za pomocą **rg** dołączane.

1. Wybierz moduł równoważenia obciążenia. Jego nazwą domyślną jest nazwa projektu za pomocą **-lb** dołączane.

1. Kopiuj tylko część adresu IP publicznego adresu IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce pojawi się domyślna strona serwera sieci web usług Internet Information Services (IIS).

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć moduł równoważenia obciążenia rozdziela ruch między wszystkie trzy maszyny wirtualne, możesz wymusić odświeżenie przeglądarki sieci web z komputera klienckiego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, moduł równoważenia obciążenia i wszystkich powiązanych zasobów. Aby to zrobić, przejdź do witryny Azure portal, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie wybierz **Usuń grupę zasobów**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start zostało utworzone standardowego modułu równoważenia obciążenia, dołączono do jej maszyny wirtualne, skonfigurowano regułę ruchu modułu równoważenia obciążenia, czy sondę kondycji i następnie przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej, przejdź do samouczków dla modułu równoważenia obciążenia.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 