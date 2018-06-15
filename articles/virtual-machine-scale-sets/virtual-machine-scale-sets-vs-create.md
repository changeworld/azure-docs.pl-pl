---
title: Wdrażanie zestawu skalowania maszyn wirtualnych za pomocą programu Visual Studio | Dokumentacja firmy Microsoft
description: Wdrażanie zestawów skali maszyny wirtualnej za pomocą szablonu usługi Resource Manager i Visual Studio
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73454abc11a832a1b7f4131bf13699bd0a94edea
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782150"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Tworzenie zestawu skalowania maszyn wirtualnych z programem Visual Studio
W tym artykule przedstawiono sposób wdrażania usługi Azure zestawu skalowania maszyn wirtualnych za pomocą programu Visual Studio wdrożenie grupy zasobów.

[Zestawy skalowania maszyny wirtualnej Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) jest zasobem rozwiązań usługi obliczenia Azure, aby wdrożyć i zarządzanie kolekcją podobnych maszyn wirtualnych z automatycznego skalowania i równoważenie obciążenia. Można udostępnić i wdrożyć zestawy skalowania maszyny wirtualnej przy użyciu [szablony Menedżera zasobów Azure](https://github.com/Azure/azure-quickstart-templates). Szablony usługi Azure Resource Manager można wdrożyć przy użyciu interfejsu wiersza polecenia Azure, programu PowerShell, REST, a także bezpośrednio z programu Visual Studio. Program Visual Studio oferuje zestaw przykład szablonów, które można wdrożyć w ramach projektu wdrożenia grupy zasobów platformy Azure.

Wdrożenia grupy zasobów platformy Azure służą do grupowania i opublikować zestawu powiązanych zasobów systemu Azure w ramach operacji pojedynczego wdrożenia. Więcej informacji o nich w tym miejscu: [tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Wymagania wstępne
Aby rozpocząć wdrażanie zestawy skalowania maszyny wirtualnej w programie Visual Studio, potrzebne są następujące elementy:

* Visual Studio 2013 lub nowszy
* Zestaw Azure SDK 2.8 2.7 i 2.9

>[!NOTE]
>W poniższych instrukcjach przyjęto w przypadku korzystania z programu Visual Studio z [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Tworzenie projektu
1. Utwórz nowy projekt w programie Visual Studio, wybierając **pliku | Nowy | Projekt**.
   
    ![Nowy plik][file_new]

2. W obszarze **Visual C# | Chmura**, wybierz **usługi Azure Resource Manager** do utworzenia projektu wdrażania szablonu usługi Azure Resource Manager.
   
    ![Tworzenie projektu][create_project]

3. Z listy szablonów wybierz Linux albo szablon ustawić skali maszyny wirtualnej systemu Windows.
   
   ![Wybierz szablon][select_Template]

4. Po utworzeniu projektu Zobacz skryptów programu PowerShell wdrażania szablonu usługi Azure Resource Manager i pliku parametrów dla zestawu skalowania maszyn wirtualnych.
   
    ![Eksplorator rozwiązań][solution_explorer]

## <a name="customize-your-project"></a>Dostosowywanie projektu
Można teraz edytować szablonu, aby dostosować go na potrzeby aplikacji, takich jak dodawanie właściwości rozszerzenia maszyny Wirtualnej lub edytowanie reguły równoważenia obciążenia. Domyślnie szablony zestawu skali maszyny wirtualnej są skonfigurowane do rozszerzenia AzureDiagnostics, co ułatwia dodawanie reguł skalowania automatycznego wdrażania. Ponadto wdraża z publicznym adresem IP skonfigurowano reguły NAT ruchu przychodzącego modułu równoważenia obciążenia. 

Moduł równoważenia obciążenia umożliwia połączenie z wystąpień maszyn wirtualnych z protokołu SSH (Linux) lub protokołu RDP (system Windows). Zakres portów frontonu rozpoczyna się od 50000. Dla systemu linux, oznacza to, że jeśli użytkownik SSH do portu 50000, możesz są kierowane do portu 22 pierwsza maszyna wirtualna w zestawie skalowania. Łączenie z porcie 50001 jest kierowany do portu 22 drugiej maszyny wirtualnej i tak dalej.

 Dobrym sposobem Edytuj szablony z programem Visual Studio jest konspekt pliku JSON umożliwia organizowanie parametrów, zmienne i zasobów. Po zrozumieniu schematu programu Visual Studio może wskazywać błędy w szablonie przed przystąpieniem do wdrażania.

![Eksplorator JSON][json_explorer]

## <a name="deploy-the-project"></a>Wdrażanie projektu
1. Wdróż szablon Menedżera zasobów Azure, aby utworzyć zasób zestawu skalowania maszyn wirtualnych. Kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Wdróż | Nowe wdrożenie**.
   
    ![Wdrażanie szablonu][5deploy_Template]
    
2. Wybierz subskrypcję, w oknie dialogowym "Wdrożyć do grupy zasobów".
   
    ![Wdrażanie szablonu][6deploy_Template]

3. W tym miejscu można utworzyć grupy zasobów platformy Azure, aby wdrożyć szablon.
   
    ![Nowa grupa zasobów][new_resource]

4. Następnie kliknij przycisk **Edytuj parametry** o wprowadzenie parametrów, które są przekazywane do szablonu. Podaj nazwę użytkownika i hasło dla systemu operacyjnego, który jest wymagany do tworzenia wdrożenia. Jeśli nie masz narzędzia programu PowerShell dla programu Visual Studio, zainstalowane, zalecane jest Sprawdź **zapisywanie haseł** w celu uniknięcia ukryte wierszu polecenia programu PowerShell lub użyj [Obsługa keyvault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Edytowanie parametrów][edit_parameters]

5. Teraz kliknij **Wdróż**. **Dane wyjściowe** okno będzie wyświetlany postęp wdrażania. Należy pamiętać, że akcja jest wykonywana **AzureResourceGroup.ps1 Wdróż** skryptu.
   
   ![Okno danych wyjściowych][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Eksplorowanie sieci zestawu skali maszyny wirtualnej
Po zakończeniu wdrożenia, można wyświetlić zestawu skali maszyny wirtualnej w programie Visual Studio **Eksplorator chmury** (odświeżenie listy). Eksplorator chmury umożliwia zarządzanie zasobami Azure w programie Visual Studio podczas tworzenia aplikacji. Można również wyświetlić z uprawnieniami w zestawie skalowania maszyn wirtualnych [portalu Azure](https://portal.azure.com) i [Eksploratora zasobów Azure](https://resources.azure.com/).

![Eksplorator chmury][cloud_explorer]

 Portal zawiera najlepszy sposób, aby wizualnie zarządzać infrastruktury platformy Azure przy użyciu przeglądarki sieci web, gdy Eksploratora zasobów Azure pozwala łatwo eksplorować i debugowania zasobów platformy Azure, nadanie okna w widoku"wystąpienia", a także wyświetlanie poleceń programu PowerShell dla zasobów jest wyświetlany.

## <a name="next-steps"></a>Kolejne kroki
Po pomyślnym wdrożeniu zestawy skalowania maszyny wirtualnej za pomocą programu Visual Studio, można dostosować projekt do własnych wymagań aplikacji. Na przykład skonfigurować automatyczne skalowanie, dodając **Insights** zasobów, dodawania infrastruktury do szablonu (na przykład autonomicznych maszyn wirtualnych), lub wdrażania aplikacji za pomocą niestandardowego rozszerzenia skryptu. Dobrym przykładem szablonów można znaleźć w [szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates) repozytorium GitHub (Wyszukaj "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
