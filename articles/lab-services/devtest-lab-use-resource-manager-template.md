---
title: Wyświetlanie i używanie maszyny wirtualnej, szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć szablonu usługi Azure Resource Manager z maszyny wirtualnej do utworzenia innych maszyn wirtualnych
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1b96c801e3c5a43f23d4c63c13a611c24dd6705b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788676"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Tworzenie maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager 

Podczas tworzenia maszyny wirtualnej (VM) w usłudze DevTest Labs za pośrednictwem [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), można wyświetlić szablonu usługi Azure Resource Manager, aby zapisać maszynę Wirtualną. Szablon można następnie służyć jako podstawa można utworzyć więcej laboratorium maszyn wirtualnych z tymi samymi ustawieniami.

W tym artykule opisano wielu maszyn wirtualnych i szablony Menedżera zasobów maszyn wirtualnych na jednym i przedstawiono sposób wyświetlania i zapisywania szablonu podczas tworzenia maszyny Wirtualnej.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Wielu maszyn wirtualnych i szablony Menedżera zasobów maszyn wirtualnych na jednym
Istnieją dwa sposoby tworzenia maszyn wirtualnych w usłudze DevTest Labs przy użyciu szablonu usługi Resource Manager: udostępnianie zasobów Microsoft.DevTestLab/labs/virtualmachines i inicjowania obsługi administracyjnej zasobów Microsoft.Commpute/virtualmachines. Każda jest używane w różnych scenariuszach i wymaga inne uprawnienia.

- Szablony Menedżera zasobów, korzystających z typu zasobu Microsoft.DevTestLab/labs/virtualmachines (podaną we właściwości "zasobu" w szablonie) można udostępnić laboratorium poszczególnych maszyn wirtualnych. Każda maszyna wirtualna następnie jest wyświetlany jako jeden element na liście DevTest Labs maszyn wirtualnych:

   ![Lista maszyn wirtualnych jako pojedyncze elementy na liście DevTest Labs maszyny wirtualne](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ten typ szablonu usługi Resource Manager można udostępnić za pomocą polecenia programu PowerShell usługi Azure **AzureRmResourceGroupDeployment nowy** lub za pomocą polecenia interfejsu wiersza polecenia Azure **Utwórz wdrożenie grupy az**. Wymagane uprawnienia administratora tak użytkowników, którzy są przypisani z rolą użytkownika DevTest Labs nie można wykonać wdrożenia. 

- Szablony Menedżera zasobów, korzystających z typu zasobu Microsoft.Compute/virtualmachines umożliwia obsługę wielu maszyn wirtualnych jako jednym środowiskiem w usłudze DevTest Labs listy maszyn wirtualnych:

   ![Lista maszyn wirtualnych jako pojedyncze elementy na liście DevTest Labs maszyny wirtualne](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Maszyny wirtualne w tym samym środowisku zarządzane wspólnie i udostępnianie tego samego cyklu życia. Użytkownicy z przypisaną z rolą użytkownika DevTest Labs utworzyć środowiska przy użyciu tych szablonów, tak długo, jak administrator skonfigurował laboratorium w ten sposób.

W dalszej części tego artykułu opisano szablony Menedżera zasobów, używające Mirosoft.DevTestLab/labs/virtualmachines. Są one używane przez administratorów laboratorium do automatyzacji tworzenia maszyny Wirtualnej laboratorium (na przykład claimable VMs) lub generowania złotego obrazu (na przykład obraz fabryka).

[Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferuje wiele wskazówki i sugestie ułatwiające tworzenie szablonów usługi Azure Resource Manager, które są niezawodne i łatwe w użyciu.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Wyświetlanie i zapisać szablon usługi Resource Manager maszynę wirtualną
1. Wykonaj kroki opisane w temacie [tworzenie pierwszej maszyny Wirtualnej w laboratorium](devtest-lab-create-first-vm.md) aby rozpocząć tworzenie maszyny wirtualnej.
1. Wprowadź wymagane informacje dla maszyny wirtualnej, a następnie dodaj wszelkie artefakty dla tej maszyny Wirtualnej.
1. W dolnej części okna Ustawienia konfiguracji, wybierz **szablon ARM widoku**.

   ![Szablon ARM przycisk](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Skopiuj i Zapisz szablonu usługi Resource Manager w celu użycia później, aby utworzyć inną maszynę wirtualną.

   ![Szablon usługi Resource Manager, aby zapisać do późniejszego użycia](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po zapisaniu szablonu usługi Resource Manager, musisz zaktualizować sekcję parametrów szablonu przed jego użyciem. Można utworzyć parameter.json, który dostosowuje tylko parametry, poza rzeczywiste szablonu usługi Resource Manager. 

![Dostosuj parametry, używając pliku JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Szablon Menedżera zasobów jest teraz gotowa do użytku [tworzenie maszyny Wirtualnej](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [utworzyć środowiska wielu maszyn wirtualnych z szablonami usługi Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Wdrażanie szablonu usługi Resource Manager, aby utworzyć Maszynę wirtualną](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Poznaj więcej szablonów usługi Resource Manager szybkiego startu DevTest Labs automatyzacji z [publicznego repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
