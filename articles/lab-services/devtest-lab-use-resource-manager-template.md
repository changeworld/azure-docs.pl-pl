---
title: Wyświetlanie szablonu usługi Azure Resource Manager maszyny wirtualnej i używanie go
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169115"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Tworzenie maszyn wirtualnych przy użyciu szablonu usługi Azure Resource Manager 

Podczas tworzenia maszyny wirtualnej (VM) w DevTest Labs za pośrednictwem [witryny Azure portal,](https://go.microsoft.com/fwlink/p/?LinkID=525040)można wyświetlić szablon usługi Azure Resource Manager przed zapisaniem maszyny wirtualnej. Szablon może następnie służyć jako podstawa do tworzenia większej liczby maszyn wirtualnych w laboratorium z tymi samymi ustawieniami.

W tym artykule opisano szablony Multi-VM vs. Single-VM Resource Manager i pokazano, jak wyświetlić i zapisać szablon podczas tworzenia maszyny Wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Szablony Multi-VM vs. Single-VM Resource Manager
Istnieją dwa sposoby tworzenia maszyn wirtualnych w laboratoriach DevTest przy użyciu szablonu Menedżera zasobów: aprowizowanie zasobu Microsoft.DevTestLab/labs/virtualmachines lub aprowizowanie zasobu Microsoft.Compute/virtualmachines. Każdy z nich jest używany w różnych scenariuszach i wymaga różnych uprawnień.

- Szablony Menedżera zasobów, które używają typu zasobu Microsoft.DevTestLab/labs/virtualmachines (zadeklarowanego we właściwości "zasób" w szablonie) mogą aprowizować poszczególne maszyny wirtualne w laboratorium. Każda maszyna wirtualna jest następnie wyświetlana jako pojedynczy element na liście maszyn wirtualnych DevTest Labs:

   ![Lista maszyn wirtualnych jako pojedynczych elementów na liście maszyn wirtualnych DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ten typ szablonu Menedżera zasobów można aprowizować za pomocą polecenia Azure PowerShell **New-AzResourceGroupDeployment** lub za pomocą polecenia Azure CLI **az group deployment create**. Wymaga uprawnień administratora, więc użytkownicy, którzy są przypisani z devtest labs roli użytkownika nie można wykonać wdrożenia. 

- Szablony Menedżera zasobów, które używają typu zasobu Microsoft.Compute/virtualmachines, mogą aprowizować wiele maszyn wirtualnych jako pojedyncze środowisko na liście maszyn wirtualnych DevTest Labs:

   ![Lista maszyn wirtualnych jako pojedynczych elementów na liście maszyn wirtualnych DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Maszyny wirtualne w tym samym środowisku mogą być zarządzane razem i współużytkować ten sam cykl życia. Użytkownicy, którzy są przypisani z devtest labs roli użytkownika można tworzyć środowiska przy użyciu tych szablonów, tak długo, jak administrator skonfigurował laboratorium w ten sposób.

W dalszej części tego artykułu omówiono szablony Menedżera zasobów korzystające z witryn Microsoft.DevTestLab/labs/virtualmachines. Są one używane przez administratorów laboratorium do automatyzacji tworzenia maszyn wirtualnych w laboratorium (na przykład maszyn wirtualnych z roszczeniem) lub generowania złotego obrazu (na przykład fabryki obrazów).

[Najważniejsze wskazówki dotyczące tworzenia szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferują wiele wskazówek i sugestii ułatwiających tworzenie szablonów usługi Azure Resource Manager, które są niezawodne i łatwe w użyciu.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Wyświetlanie i zapisywanie szablonu Menedżera zasobów maszyny wirtualnej
1. Wykonaj kroki opisane w [temacie Tworzenie pierwszej maszyny wirtualnej w laboratorium,](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) aby rozpocząć tworzenie maszyny wirtualnej.
1. Wprowadź wymagane informacje dla maszyny wirtualnej i dodaj wszystkie artefakty, które chcesz dla tej maszyny wirtualnej.
1. Swtich do karty **Ustawienia zaawansowane.** 
1. U dołu okna Konfigurowanie ustawień wybierz pozycję **Wyświetl szablon ARM**.
1. Skopiuj i zapisz szablon Menedżera zasobów, aby później użyć do utworzenia innej maszyny wirtualnej.

   ![Szablon Menedżera zasobów do zapisania do późniejszego użycia](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po zapisaniu szablonu Menedżera zasobów należy zaktualizować sekcję parametrów szablonu, aby można było go użyć. Można utworzyć parametr.json, który dostosowuje tylko parametry, poza rzeczywistym szablonem Menedżera zasobów. 

![Dostosowywanie parametrów przy użyciu pliku JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Szablon Menedżera zasobów jest teraz gotowy do użycia do [utworzenia maszyny Wirtualnej](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Ustawianie daty wygaśnięcia
W scenariuszach, takich jak szkolenia, pokazy i wersje próbne, można utworzyć maszyny wirtualne i usunąć je automatycznie po stałym czasie trwania, tak aby nie ponosić niepotrzebnych kosztów. Maszynę wirtualną laboratorium z datą wygaśnięcia można utworzyć, określając właściwość **data wygaśnięcia** maszyny Wirtualnej. Sprawdź ten sam szablon Menedżera zasobów w [naszym repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [tworzyć środowiska wielu maszyn wirtualnych za pomocą szablonów Menedżera zasobów](devtest-lab-create-environment-from-arm.md).
* [Wdrażanie szablonu Menedżera zasobów w celu utworzenia maszyny Wirtualnej](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Zapoznaj się z bardziej szybkimi szablonami Menedżera zasobów dla automatyzacji DevTest Labs z [publicznego repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
