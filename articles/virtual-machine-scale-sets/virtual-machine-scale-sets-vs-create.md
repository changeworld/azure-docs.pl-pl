---
title: Wdrażanie zestawu skalowania maszyn wirtualnych przy użyciu programu Visual Studio
description: Wdrażanie Virtual Machine Scale Sets przy użyciu programu Visual Studio i szablonu Menedżer zasobów
ms.custom: H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: 0d0dc3fbb7e48b1f7e6936cfb65473dba882b776
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274235"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Jak utworzyć zestaw skalowania maszyn wirtualnych za pomocą programu Visual Studio

W tym artykule opisano sposób wdrażania zestawu skalowania maszyn wirtualnych platformy Azure przy użyciu wdrożenia grupy zasobów programu Visual Studio.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) to zasób obliczeniowy platformy Azure służący do wdrażania i zarządzania kolekcją podobnych maszyn wirtualnych z funkcją automatycznego skalowania i równoważenia obciążenia. Możesz inicjować i wdrażać Virtual Machine Scale Sets przy użyciu [szablonów Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Szablony Azure Resource Manager można wdrażać za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell, REST i bezpośrednio z programu Visual Studio. Program Visual Studio udostępnia zestaw przykładowych szablonów, które można wdrożyć w ramach projektu wdrożenia grupy zasobów platformy Azure.

Wdrożenia grup zasobów platformy Azure to sposób grupowania i publikowania zestawu powiązanych zasobów platformy Azure w ramach jednej operacji wdrażania. Aby uzyskać więcej informacji, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć wdrażanie Virtual Machine Scale Sets w programie Visual Studio, potrzebne są następujące wymagania wstępne:

* Visual Studio 2013 lub nowszy
* Zestaw Azure SDK 2,7, 2,8 lub 2,9

>[!NOTE]
>W tym artykule jest wykorzystywany program Visual Studio 2019 z [zestawem Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## Tworzenie projektu<a name="creating-a-project"></a> 

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt**wybierz pozycję **Grupa zasobów platformy Azure** , C# a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu**wprowadź nazwę i wybierz pozycję **Utwórz**.

    ![Nazwa i tworzenie projektu](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Z listy szablonów wybierz opcję **zestaw skalowania maszyn wirtualnych z systemem Windows** lub szablon **zestawu skalowania maszyn wirtualnych** z systemem Linux. Kliknij przycisk **OK**.

   ![Wybierz szablon maszyny wirtualnej](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Po utworzeniu projektu **Eksplorator rozwiązań** zawiera skrypt wdrożenia programu PowerShell, szablon Azure Resource Manager i plik parametrów dla zestawu skalowania maszyn wirtualnych.

## <a name="customize-your-project"></a>Dostosowywanie projektu

Teraz można edytować szablon w celu dostosowania go do potrzeb aplikacji. Można dodać właściwości rozszerzenia maszyny wirtualnej lub edytować reguły równoważenia obciążenia. Domyślnie szablony zestawu skalowania maszyn wirtualnych są skonfigurowane tak, aby wdrażać rozszerzenie **AzureDiagnostics** , co ułatwia dodawanie reguł skalowania automatycznego. Szablony również wdrażają moduł równoważenia obciążenia z publicznym adresem IP skonfigurowanym przy użyciu reguł NAT dla ruchu przychodzącego.

Moduł równoważenia obciążenia umożliwia łączenie się z wystąpieniami maszyn wirtualnych przy użyciu protokołu SSH (Linux) lub RDP (Windows). Zakres portów frontonu zaczyna się od 50000. W przypadku systemu Linux w przypadku protokołu SSH do portu 50000 Równoważenie obciążenia kieruje do portu 22 pierwszej maszyny wirtualnej w zestawie skalowania. Połączenie z portem 50001 jest kierowane do portu 22 drugiej maszyny wirtualnej i tak dalej.

 Dobrym sposobem na Edytowanie szablonów przy użyciu programu Visual Studio jest użycie **konspektu JSON**. Można organizować parametry, zmienne i zasoby. Dzięki zrozumieniu schematu program Visual Studio może wskazywać błędy w szablonie przed jego wdrożeniem.

![Eksplorator JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Wdróż projekt

Wdróż szablon Azure Resource Manager, aby utworzyć zasób zestawu skalowania maszyn wirtualnych:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Wdróż** > **nowe**.

    ![Wdrażanie projektu](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. W obszarze **wdrażanie do grupy zasobów**wybierz subskrypcję, która ma zostać użyta, a następnie wybierz grupę zasobów. W razie potrzeby można utworzyć grupę zasobów.

1. Następnie wybierz pozycję **Edytuj parametry** , aby wprowadzić parametry, które są przesyłane do szablonu.

   ![Wprowadź subskrypcję i grupę zasobów](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Podaj nazwę użytkownika i hasło dla systemu operacyjnego. Te wartości są wymagane do utworzenia wdrożenia. Jeśli nie masz zainstalowanego PowerShell Tools for Visual Studio, wybierz pozycję **Zapisz hasła** , aby uniknąć ukrycia wiersza polecenia programu PowerShell, lub Użyj [Key Vault obsługi](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Wybierz pozycję **Zapisz** , aby kontynuować.

    ![Edytuj parametry wdrożenia](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. W obszarze **wdrażanie do grupy zasobów**wybierz pozycję **Wdróż**. Akcja uruchamia skrypt **Deploy-AzureResourceGroup. ps1** . W oknie **danych wyjściowych** zostanie wyświetlony postęp wdrażania.

   ![Dane wyjściowe pokazują wyniki](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## Eksplorowanie zestawu skalowania maszyn wirtualnych<a name="exploring-your-virtual-machine-scale-set"></a>

Wybierz pozycję **wyświetl** > w **Eksploratorze chmury** , aby wyświetlić nowy zestaw skalowania maszyn wirtualnych. W razie potrzeby użyj **Odśwież wszystko**.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

Program **Cloud Explorer** umożliwia zarządzanie zasobami platformy Azure w programie Visual Studio podczas opracowywania aplikacji. Możesz również wyświetlić zestaw skalowania maszyn wirtualnych w [Azure Portal](https://portal.azure.com) i [Azure Resource Explorer](https://resources.azure.com/).

 Portal zapewnia najlepszy sposób zarządzania infrastrukturą platformy Azure za pomocą przeglądarki sieci Web. Azure Resource Explorer zapewnia łatwy sposób eksplorowania i debugowania zasobów platformy Azure. Azure Resource Explorer oferuje widok wystąpienia i zawiera także polecenia programu PowerShell dotyczące zasobów, na których się przeglądasz.

## <a name="next-steps"></a>Następne kroki

Po pomyślnym wdrożeniu Virtual Machine Scale Sets za poorednictwem programu Visual Studio można bardziej dostosować projekt zgodnie z wymaganiami aplikacji. Na przykład skonfiguruj automatyczne skalowanie, dodając zasób usługi **Insights** . Do szablonu można dodać infrastrukturę, taką jak autonomiczne maszyny wirtualne, lub wdrożyć aplikacje przy użyciu rozszerzenia niestandardowego skryptu. Dobre przykładowe szablony można znaleźć w repozytorium GitHub [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates) . Wyszukaj `vmss`.
