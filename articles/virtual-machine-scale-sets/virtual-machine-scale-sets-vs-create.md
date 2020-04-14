---
title: Wdrażanie zestawu skalowania maszyny wirtualnej przy użyciu programu Visual Studio
description: Wdrażanie zestawów skalowania maszyny wirtualnej przy użyciu programu Visual Studio i szablonu Menedżera zasobów
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mimckitt
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: mimckitt
ms.openlocfilehash: adc91d5f4f79be8a85dfed7d10a882493f6427b0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273345"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Jak utworzyć zestaw skalowania maszyny wirtualnej za pomocą programu Visual Studio

W tym artykule pokazano, jak wdrożyć zestaw skalowania maszyny wirtualnej platformy Azure przy użyciu wdrożenia grupy zasobów programu Visual Studio.

[Zestawy skalowania maszyny wirtualnej platformy Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) to zasób obliczeniowy platformy Azure do wdrażania i zarządzania kolekcją podobnych maszyn wirtualnych z automatyczną skalą i równoważeniem obciążenia. Można aprowizować i wdrażać zestawy skalowania maszyny wirtualnej przy użyciu [szablonów usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Szablony usługi Azure Resource Manager można wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, REST, a także bezpośrednio z programu Visual Studio. Visual Studio zawiera zestaw przykładowych szablonów, które można wdrożyć w ramach projektu wdrażania grupy zasobów platformy Azure.

Wdrożenia grupy zasobów platformy Azure to sposób na grupowanie i publikowanie zestawu powiązanych zasobów platformy Azure w ramach jednej operacji wdrażania. Aby uzyskać więcej informacji, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pośrednictwem programu Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wdrażanie zestawów skalowania maszyn wirtualnych w programie Visual Studio, potrzebne są następujące wymagania wstępne:

* Visual Studio 2013 lub nowsze
* Zestaw SDK platformy Azure 2.7, 2.8 lub 2.9

>[!NOTE]
>W tym artykule używa się programu Visual Studio 2019 z [zestawem SDK 2.8 platformy Azure](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Tworzenie projektu<a name="creating-a-project"></a> 

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W **obszarze Tworzenie nowego projektu**wybierz pozycję Grupa zasobów platformy **Azure** dla języka C#, a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfiguruj nowy projekt**wprowadź nazwę i wybierz pozycję **Utwórz**.

    ![Nadawanie nazwy i tworzenie projektu](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Z listy szablonów wybierz szablon **Zestaw skalowania maszyny wirtualnej systemu Windows** lub zestaw **skalowania maszyny wirtualnej systemu Linux.** Kliknij przycisk **OK**.

   ![Wybieranie szablonu maszyny wirtualnej](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Po utworzeniu projektu **Eksplorator rozwiązań** zawiera skrypt wdrażania programu PowerShell, szablon usługi Azure Resource Manager i plik parametru dla zestawu skalowania maszyny wirtualnej.

## <a name="customize-your-project"></a>Dostosowywanie projektu

Teraz możesz edytować szablon, aby dostosować go do potrzeb aplikacji. Można dodać właściwości rozszerzenia maszyny wirtualnej lub edytować reguły równoważenia obciążenia. Domyślnie szablony zestawu skalowania maszyny wirtualnej są skonfigurowane do wdrażania rozszerzenia **AzureDiagnostics,** co ułatwia dodawanie reguł skalowania automatycznego. Szablony wdrażają również moduł równoważenia obciążenia z publicznym adresem IP skonfigurowanym z regułami przychodzącego translatora adresów sieciowych.

Moduł równoważenia obciążenia umożliwia łączenie się z wystąpieniami maszyny wirtualnej za pomocą protokołu SSH (Linux) lub RDP (Windows). Zakres portów front-end zaczyna się od 50000. W przypadku systemu Linux, jeśli SSH do portu 50000, równoważenie obciążenia trasy do portu 22 pierwszej maszyny wirtualnej w zestawie skalowania. Łączenie się z portem 50001 jest kierowane do portu 22 drugiej maszyny wirtualnej i tak dalej.

 Dobrym sposobem edytowania szablonów w programie Visual Studio jest użycie **konspektu JSON**. Można organizować parametry, zmienne i zasoby. Ze zrozumieniem schematu visual studio można wskazać błędy w szablonie przed wdrożeniem go.

![Eksplorator JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Wdrażanie projektu

Wdrażanie szablonu usługi Azure Resource Manager w celu utworzenia zasobu zestawu skalowania maszyny wirtualnej:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Wdrażanie** > **nowego**.

    ![Wdrażanie projektu](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. W **obszarze Wdrażanie w grupie zasobów**wybierz subskrypcję, której chcesz użyć, i wybierz grupę zasobów. W razie potrzeby można utworzyć grupę zasobów.

1. Następnie wybierz **polecenie Edytuj parametry,** aby wprowadzić parametry przekazywane do szablonu.

   ![Wprowadzanie subskrypcji i grupy zasobów](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Podaj nazwę użytkownika i hasło dla systemu operacyjnego. Te wartości są wymagane do utworzenia wdrożenia. Jeśli nie masz zainstalowanych narzędzi programu PowerShell Tools for Visual Studio, wybierz pozycję **Zapisz hasła,** aby uniknąć ukrytego wiersza polecenia programu PowerShell, lub użyj [obsługi usługi Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Wybierz **pozycję Zapisz,** aby kontynuować.

    ![Edytowanie parametrów wdrażania](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. W **obszarze Wdrażanie w grupie zasobów**wybierz pozycję **Wdrażanie**. Akcja uruchamia skrypt **Deploy-AzureResourceGroup.ps1.** Okno **Dane wyjściowe** pokazuje postęp wdrażania.

   ![Dane wyjściowe pokazują wyniki](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Poznaj zestaw skalowania maszyny wirtualnej<a name="exploring-your-virtual-machine-scale-set"></a>

Wybierz **pozycję Wyświetl** > **Eksploratora chmury,** aby wyświetlić nowy zestaw skalowania maszyny wirtualnej. W razie potrzeby użyj **funkcji Odśwież wszystko.**

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** umożliwia zarządzanie zasobami platformy Azure w programie Visual Studio podczas tworzenia aplikacji. Zestaw skalowania maszyny wirtualnej można również wyświetlić w [witrynie Azure portal](https://portal.azure.com) i [Eksploratorze zasobów platformy Azure](https://resources.azure.com/).

 Portal zapewnia najlepszy sposób zarządzania infrastrukturą platformy Azure za pomocą przeglądarki sieci Web. Eksplorator zasobów platformy Azure zapewnia łatwy sposób eksplorowania i debugowania zasobów platformy Azure. Eksplorator zasobów platformy Azure oferuje widok wystąpienia, a także pokazuje polecenia programu PowerShell dla zasobów, które przeglądasz.

## <a name="next-steps"></a>Następne kroki

Po pomyślnym wdrożeniu zestawów skalowania maszyny wirtualnej za pośrednictwem programu Visual Studio można dodatkowo dostosować projekt do wymagań aplikacji. Na przykład skonfiguruj skalowanie automatyczne przez dodanie zasobu **aplikacji Insights.** Można dodać infrastrukturę do szablonu, takich jak autonomiczne maszyny wirtualne, lub wdrożyć aplikacje przy użyciu niestandardowego rozszerzenia skryptu. Dobre przykładowe szablony można znaleźć w repozytorium [Szablony programu Azure Szybki start](https://github.com/Azure/azure-quickstart-templates) GitHub. Wyszukaj `vmss`.
