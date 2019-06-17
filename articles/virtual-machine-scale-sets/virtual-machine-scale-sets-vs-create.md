---
title: Wdrażanie zestawu skalowania maszyn wirtualnych przy użyciu programu Visual Studio | Dokumentacja firmy Microsoft
description: Wdrażanie zestawów skalowania maszyn wirtualnych przy użyciu szablonu usługi Resource Manager i programu Visual Studio
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108068"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Jak utworzyć zestaw skalowania maszyn wirtualnych z programem Visual Studio
W tym artykule przedstawiono sposób wdrażania maszyny wirtualnej zestawu skalowania platformy Azure przy użyciu programu Visual Studio wdrożenia grupy zasobów.

[Usługa Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) jest zasobem usługi Azure Compute, wdrażanie i zarządzanie kolekcją podobne maszyny wirtualne z automatycznego skalowania i równoważenia obciążenia. Można aprowizować i wdrażać za pomocą programu Virtual Machine Scale Sets [szablonów usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Szablony usługi Azure Resource Manager można wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, REST, a także bezpośrednio z programu Visual Studio. Program Visual Studio udostępnia zestaw przykładowych szablonów, które można wdrożyć jako część projektu wdrożenia grupy zasobów platformy Azure.

Wdrożenia grupy zasobów platformy Azure to sposób grupowania i opublikować zestaw powiązanych zasobów platformy Azure w ramach operacji pojedynczego wdrożenia. Więcej informacji o nich można znaleźć tutaj: [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Wymagania wstępne
Aby rozpocząć wdrażanie zestawów skalowania maszyn wirtualnych w programie Visual Studio, potrzebne są następujące elementy:

* Visual Studio 2013 lub nowszy
* Zestaw Azure SDK 2.7, 2.8 lub 2.9

>[!NOTE]
>W poniższych instrukcjach przyjęto, w przypadku korzystania z programu Visual Studio z [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Tworzenie projektu
1. Utwórz nowy projekt w programie Visual Studio, wybierając **pliku | Nowe | Projekt**.
   
    ![Nowy plik][file_new]

2. W obszarze **Visual C# | Chmura**, wybierz **usługi Azure Resource Manager** do utworzenia projektu do wdrożenia szablonu usługi Azure Resource Manager.
   
    ![Tworzenie projektu][create_project]

3. Z listy szablonów wybierz Linux lub Windows szablonu zestawu skalowania maszyn wirtualnych.
   
   ![Wybierz szablon][select_Template]

4. Po utworzeniu projektu zobaczysz skryptów wdrażania programu PowerShell, szablonu usługi Azure Resource Manager i w pliku parametrów dla zestawu skalowania maszyn wirtualnych.
   
    ![Eksplorator rozwiązań][solution_explorer]

## <a name="customize-your-project"></a>Dostosować swój projekt
Możesz teraz edytować szablonu Aby dostosować ją do potrzeb swojej aplikacji, takich jak dodawanie właściwości rozszerzenia maszyny Wirtualnej lub edytowanie reguły równoważenia obciążenia. Domyślnie szablony zestawu skali maszyny wirtualnej są skonfigurowane do wdrożenia rozszerzenia AzureDiagnostics, która ułatwia dodawanie reguły skalowania automatycznego. Wdraża ona też z publicznym adresem IP skonfigurowane za pomocą reguł NAT dla ruchu przychodzącego modułu równoważenia obciążenia. 

Moduł równoważenia obciążenia umożliwia podłączenie do wystąpień maszyn wirtualnych przy użyciu protokołu SSH (Linux) lub protokołu RDP (Windows). Zakres portów frontonu rozpoczyna się od 50000. Dla systemu linux, oznacza to, że jeśli użytkownik SSH port 50000, możesz są kierowane do portu 22 pierwsza maszyna wirtualna w zestawie skalowania. Nawiązywanie połączenia z portu 50001 jest kierowany do portu 22 drugiej maszyny Wirtualnej i tak dalej.

 Dobrym sposobem edytowania szablonów za pomocą programu Visual Studio jest używanie konspekt pliku JSON do organizowania parametrów, zmienne i zasobów. Opisu schematu programu Visual Studio może wskazywać błędy w szablonie, przed jego wdrożeniem.

![Eksplorator JSON][json_explorer]

## <a name="deploy-the-project"></a>Wdrażanie projektu
1. Wdrażanie szablonu usługi Resource Manager platformy Azure w celu utworzenia zasobu zestawu skalowania maszyn wirtualnych. Kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Wdróż | Nowe wdrożenie**.
   
    ![Wdrażanie szablonu][5deploy_Template]
    
2. Wybierz swoją subskrypcję, w oknie dialogowym "Wdrażanie do grupy zasobów".
   
    ![Wdrażanie szablonu][6deploy_Template]

3. W tym miejscu można utworzyć grupę zasobów platformy Azure, aby wdrożyć szablon.
   
    ![Nowa grupa zasobów][new_resource]

4. Następnie kliknij przycisk **Edytuj parametry** o podanie parametrów, które są przekazywane do szablonu. Podaj nazwę użytkownika i hasło dla systemu operacyjnego, który jest wymagany do utworzenia wdrożenia. Jeśli nie masz narzędzia programu PowerShell dla programu Visual Studio, zaleca się sprawdzanie **zapisywać hasła** Aby uniknąć ukryty wiersz polecenia programu PowerShell lub użyj [pomocy technicznej usługi keyvault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Edytuj parametry][edit_parameters]

5. Teraz klikaj polecenie **Wdróż**. **Dane wyjściowe** okno pokazuje postęp wdrażania. Należy zauważyć, że akcja jest wykonywana **AzureResourceGroup.ps1 Wdróż** skryptu.
   
   ![Okno wyniku][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Eksplorowanie zestawu skalowania maszyn wirtualnych
Po zakończeniu wdrożenia można wyświetlać nowego zestawu skalowania maszyn wirtualnych w programie Visual Studio **programu Cloud Explorer** (odświeżenie listy). Eksplorator chmury umożliwia zarządzanie zasobami platformy Azure w programie Visual Studio podczas tworzenia aplikacji. Można również wyświetlić zestawie skalowania maszyn wirtualnych w [witryny Azure portal](https://portal.azure.com) i [Eksploratora zasobów Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 Portal zawiera najlepszym sposobem wizualnie zarządzających infrastrukturą platformy Azure za pomocą przeglądarki sieci web, gdy Eksploratora zasobów Azure umożliwia łatwe Eksplorowanie i debugować zasobów platformy Azure, zapewniając okna w widoku"wystąpienia" i również wyświetlanie poleceń programu PowerShell dla zasobów przeglądasz.

## <a name="next-steps"></a>Kolejne kroki
Po pomyślnym wdrożeniu Virtual Machine Scale Sets za pomocą programu Visual Studio, można dostosować projekt, aby odpowiadał wymaganiom Twojej aplikacji. Na przykład skonfigurować automatyczne skalowanie, dodając **Insights** zasobu, dodawanie infrastruktury do szablonu (na przykład autonomiczne maszyny wirtualne) lub wdrażanie aplikacji przy użyciu rozszerzenia niestandardowego skryptu. Dobrym przykładem szablonów można znaleźć w [szablony szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates) repozytorium GitHub (wyszukaj frazę "zestawu skalowania maszyn wirtualnych").

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
