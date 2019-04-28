---
title: Wyświetlanie i używanie maszyny wirtualnej, szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać szablonu usługi Azure Resource Manager z maszyny wirtualnej do tworzenia innych maszyn wirtualnych
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 533770d98b146dea01e91e1249115c4b5c074b3c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101573"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Tworzenie maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager 

Podczas tworzenia maszyny wirtualnej (VM) w usłudze DevTest Labs przy użyciu [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), można wyświetlić szablonu usługi Azure Resource Manager, aby zapisać maszynę Wirtualną. Szablon następnie można jako podstawy do tworzenia laboratorium więcej maszyn wirtualnych z tymi samymi ustawieniami.

W tym artykule opisano wiele maszyn wirtualnych i szablonów usługi Resource Manager maszyn wirtualnych na jednym i pokazuje, jak wyświetlać i zapisywać szablonu podczas tworzenia maszyny Wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Wieloma Maszynami wirtualnymi a Maszynami wirtualnymi na jednym szablonów usługi Resource Manager
Istnieją dwa sposoby tworzenia maszyn wirtualnych w usłudze DevTest Labs przy użyciu szablonu usługi Resource Manager: aprowizowanie zasobów Microsoft.DevTestLab/labs/virtualmachines lub aprowizowanie zasobów Microsoft.Compute/virtualmachines. Każda jest używane w różnych scenariuszach i wymaga różnych uprawnień.

- Szablony usługi Resource Manager, korzystających z typu zasobu Microsoft.DevTestLab/labs/virtualmachines (ponieważ nie zadeklarowano we właściwości "resource" w szablonie) laboratorium poszczególne maszyny wirtualne można aprowizować. Każda maszyna wirtualna następnie jest wyświetlany jako pojedynczy element na liście maszyn wirtualnych laboratorium:

   ![Lista maszyn wirtualnych jako pojedyncze elementy na liście maszyny wirtualne usługi DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ten typ szablonu usługi Resource Manager mogą być udostępniane za pomocą polecenia programu Azure PowerShell **AzResourceGroupDeployment nowy** lub za pomocą polecenia interfejsu wiersza polecenia Azure **Utwórz wdrożenie grupy az**. Musi mieć uprawnienia administratora, dzięki czemu użytkownicy z przypisaną rolą użytkownika DevTest Labs, nie można wykonać wdrożenie. 

- Szablony usługi Resource Manager, korzystających z typu zasobu Microsoft.Compute/virtualmachines umożliwia obsługę wielu maszyn wirtualnych jako jednego środowiska na liście maszyn wirtualnych laboratorium:

   ![Lista maszyn wirtualnych jako pojedyncze elementy na liście maszyny wirtualne usługi DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Maszyny wirtualne w tym samym środowisku można zarządzać razem i udostępnić ten sam cykl życia. Użytkownicy z przypisaną rolą użytkownika DevTest Labs można utworzyć środowiska za pomocą tych szablonów, tak długo, jak administrator skonfigurował laboratorium w ten sposób.

W dalszej części tego artykułu opisano szablonów usługi Resource Manager, które używają Microsoft.DevTestLab/labs/virtualmachines. Są one używane przez administratorów laboratorium do automatyzacji tworzenia maszyny Wirtualnej w laboratorium (na przykład maszyny wirtualne przejęcia) lub generowania złotego obrazu (na przykład fabrycznie obrazu).

[Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferuje wiele wytycznych i sugestie ułatwiające tworzenie szablonów usługi Azure Resource Manager, które jest niezawodne i łatwe w użyciu.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Wyświetlanie i zapisywanie maszyny wirtualnej, szablonu usługi Resource Manager
1. Wykonaj kroki opisane w temacie [tworzenie pierwszej maszyny Wirtualnej w laboratorium](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) umożliwiającą utworzenie maszyny wirtualnej.
1. Wprowadź wymagane informacje dotyczące maszyny wirtualnej i Dodaj wszelkie artefakty, które mają dla tej maszyny Wirtualnej.
1. W dolnej części okna Skonfiguruj ustawienia, wybierz **szablonu ARM widoku**.

   ![Przycisk szablonu ARM](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Skopiuj i Zapisz szablon usługi Resource Manager do użycia w przyszłości można utworzyć innej maszyny wirtualnej.

   ![Szablon usługi Resource Manager, aby zapisać do późniejszego użycia](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po zapisaniu szablonu usługi Resource Manager, musisz zaktualizować sekcję parametrów szablonu przed jego użyciem. Można utworzyć parameter.json, który dostosowuje tylko parametry, poza prawdziwy szablon usługi Resource Manager. 

![Dostosuj parametry za pomocą pliku JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Szablon usługi Resource Manager jest teraz gotowa do użytku [Utwórz Maszynę wirtualną](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [tworzenie środowisk z wieloma Maszynami wirtualnymi za pomocą szablonów usługi Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Wdrażanie szablonu usługi Resource Manager, aby utworzyć Maszynę wirtualną](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Poznaj więcej szablonów szybkiego startu usługi Resource Manager, związane z automatyzacją DevTest Labs z [publicznego repozytorium GitHub laboratoria DevTest](https://github.com/Azure/azure-quickstart-templates).
