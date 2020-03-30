---
title: Wdrażanie szablonu — IntelliJ IDEA
description: Dowiedz się, jak utworzyć pierwszy szablon usługi Azure Resource Manager przy użyciu intellij idea i jak go wdrożyć.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: a5015a018f905b4353afd6bf25a48a2d942b3b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153356"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Tworzenie i wdrażanie szablonów ARM przy użyciu intellij idea

Dowiedz się, jak wdrożyć szablon usługi Azure Resource Manager (ARM) na platformie Azure przy użyciu intellij idea i proces edycji i aktualizacji szablonu bezpośrednio z IDE. Szablony ARM to pliki JSON, które definiują zasoby potrzebne do wdrożenia dla rozwiązania. Aby zapoznać się z pojęciami związanymi z wdrażaniem rozwiązań platformy Azure i zarządzanie nimi, zobacz [omówienie wdrażania szablonów.](overview.md)

![Diagram portalu szablonów Menedżera zasobów](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces umożliwia wdrażanie innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Zainstalowane środowisko [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) w wersji Ultimate Edition lub Community Edition
* Zainstalowany zestaw narzędzi [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053). Aby uzyskać więcej informacji, zobacz [Przewodnik po zarządzaniu wtyczkami platformy IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html)
* Należy [być zalogowanym](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) do konta platformy Azure dla zestawu narzędzi Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Wdrażanie szablonu przewodnika Szybki start

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybki start platformy Azure to repozytorium szablonów ARM. Szablon użyty w tym artykule nosi nazwę [Utwórz standardowe konto magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Definiuje zasób konta usługi Azure Storage.

1. Kliknij prawym przyciskiem [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) myszy i zapisz i na komputerze lokalnym.

1. Jeśli zestaw narzędzi platformy Azure jest poprawnie zainstalowany i zalogowany, eksplorator azure powinien zostać wyświetlony na pasku bocznym intellij idea. Kliknij prawym przyciskiem myszy **pozycję Zarządzanie zasobami** i wybierz polecenie **Utwórz wdrożenie**.

    ![Kliknięcie prawym przyciskiem myszy szablonu Menedżera zasobów w celu utworzenia wdrożenia](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Config **nazwę wdrożenia**, **subskrypcja**, **grupa zasobów**i **region**. W tym miejscu wdrażamy szablon `testRG`w nowej grupie zasobów . Następnie wybierz ścieżkę dla `azuredeploy.json` **szablonu zasobów** jako i **parametrów zasobu** `azuredeploy.parameters.json` podczas pobierania.

    ![Szablon Menedżera zasobów wybiera pliki do utworzenia wdrożenia](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Po kliknięciu przycisku OK wdrożenie zostanie uruchomione. Do czasu zakończenia wdrażania można znaleźć postęp z **paska stanu** IntelliJ IDEA na dole.

    ![Stan wdrożenia szablonu Menedżera zasobów](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Przeglądanie istniejącego wdrożenia

1. Po zakończeniu wdrażania można zobaczyć nową grupę `testRG` zasobów i utworzone nowe wdrożenie. Kliknij prawym przyciskiem myszy wdrożenie, aby wyświetlić listę możliwych działań. Teraz wybierz pozycję **Pokaż właściwości**.

    ![Wdrażanie szablonów Menedżera zasobów](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Widok karty będzie otwarty, aby wyświetlić niektóre przydatne właściwości, takie jak stan wdrożenia i struktura szablonu.

    ![Szablon Menedżera zasobów pokazuje właściwości wdrażania](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Edytowanie i aktualizowanie istniejącego wdrożenia

1. Wybierz **polecenie Edytuj wdrożenie** z menu po kliknięciu prawym przyciskiem myszy lub wyświetl widok właściwości przed. Inny widok karty będzie otwarty, pokazując pliki szablonów i parametrów dla wdrożenia na platformie Azure. Aby zapisać te pliki na lokalnym, można kliknąć pozycję **Eksportuj plik szablonu** lub **Eksportuj pliki parametrów**.

    ![Wdrożenie edycji szablonów Menedżera zasobów](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Można edytować dwa pliki na tej stronie i wdrożyć zmiany na platformie Azure. W tym miejscu modyfikujemy wartość **storageAccountType** w plikach parametrów, od `Standard_LRS` do `Standard_GRS`. Następnie kliknij przycisk **Aktualizuj wdrożenie** na dole i potwierdź aktualizację.

    ![Wdrożenie edycji szablonów Menedżera zasobów](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Po zakończeniu wdrażania aktualizacji można sprawdzić w portalu, czy `Standard_GRS`utworzone konto magazynu zostało zmienione na .

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

1. Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Można to zrobić za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. W Eksploratorze Platformy Azure z intellij idea kliknij prawym przyciskiem myszy **utworzoną grupę zasobów** i wybierz pozycję Usuń.

    ![Usuwanie grupy zasobów w Eksploratorze Platformy Azure z intellij idea](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Należy zauważyć, że usunięcie wdrożenia nie spowoduje usunięcia zasobów utworzonych przez wdrożenie. Usuń odpowiednią grupę zasobów lub określone zasoby, jeśli nie są już potrzebne.

## <a name="next-steps"></a>Następne kroki

Głównym celem tego artykułu jest użycie intellij idea do wdrożenia istniejącego szablonu z szablonów szybki start platformy Azure. Dowiesz się również, jak wyświetlać i aktualizować istniejące wdrożenie na platformie Azure. Szablony spośród szablonów szybkiego startu platformy Azure mogą nie zaspokajać wszystkich Twoich potrzeb. Aby dowiedzieć się więcej o tworzeniu szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Odwiedź witrynę Java w centrum deweloperów platformy Azure](https://docs.microsoft.com/azure/java)
