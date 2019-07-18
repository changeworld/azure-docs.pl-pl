---
title: 'Szybki start: Tworzenie standardowego modułu równoważenia obciążenia — Azure Resource Manager szablonu'
titlesuffix: Azure Load Balancer
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
ms.date: 06/19/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e1a04cad7fe5c9c95397ffad2faa80c7d657d0f8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273792"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Szybki start: Tworzenie standardowego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu Azure Resource Manager

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. W tym przewodniku szybki start przedstawiono sposób wdrażania szablonu Azure Resource Manager, który tworzy standardowy moduł równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Usługa Load Balancer w warstwie Standardowa obsługuje tylko standardowy publiczny adres IP. Podczas tworzenia standardowego modułu równoważenia obciążenia należy również utworzyć nowy, publiczny adres IP, który jest skonfigurowany jako fronton dla standardowej usługi równoważenia obciążenia.

Do utworzenia standardowego modułu równoważenia obciążenia można użyć wielu metod. W tym przewodniku szybki start użyjesz Azure PowerShell, aby wdrożyć [szablon Menedżer zasobów](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania.

Aby zrozumieć koncepcje związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [dokumentację Azure Resource Manager](/azure/azure-resource-manager/). Aby znaleźć więcej szablonów związanych z Azure Load Balancer, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

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

Wdrożenie szablonu trwa około 10 minut.

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślną nazwą grupy zasobów jest nazwa projektu z dołączoną **RG** .

1. Wybierz moduł równoważenia obciążenia. Nazwa domyślna to nazwa projektu z dołączonym **-lb** .

1. Skopiuj tylko część adresu IP z publicznego adresu IP, a następnie wklej ją na pasku adresu przeglądarki. W przeglądarce zostanie wyświetlona domyślna strona serwera sieci Web Internet Information Services (IIS).

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można wymusić odświeżenie przeglądarki sieci Web od komputera klienckiego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby. W tym celu przejdź do Azure Portal, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono moduł równoważenia obciążenia w warstwie Standardowa, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu modułu równoważenia obciążenia, wykonano sondę kondycji, a następnie przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej, przejdź do samouczków dotyczących Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 