---
title: Wdróż szablon Azure Resource Manager — pomysł IntelliJ
description: Dowiedz się, jak utworzyć pierwszy szablon Azure Resource Manager przy użyciu POMYSŁu IntelliJ oraz jak go wdrożyć.
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: 137a87ad0314fad08f9af0eb567e1d31032ddcb9
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533660"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu POMYSŁu IntelliJ

Dowiedz się, jak wdrożyć szablon Menedżer zasobów na platformie Azure przy użyciu POMYSŁu IntelliJ oraz procesu edytowania i aktualizowania szablonu bezpośrednio z poziomu środowiska IDE. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](resource-group-overview.md).

![Diagram portalu szybkiego startu szablonu Menedżer zasobów](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces umożliwia wdrażanie innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Zainstalowano [INTELLIJ pomysł](https://www.jetbrains.com/idea/download/) lub wersja Community
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) zainstalowany, sprawdź [Przewodnik dotyczący zarządzania wtyczkami](https://www.jetbrains.com/help/idea/managing-plugins.html) w programie IntelliJ, aby uzyskać więcej informacji
* Zaloguj [się do konta](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) platformy Azure, aby uzyskać Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Wdróż szablon szybkiego startu

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybkiego startu platformy Azure to repozytorium szablonów usługi Resource Manager. Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Definiuje zasób konta usługi Azure Storage.

1. Kliknij prawym przyciskiem myszy i Zapisz [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) i [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) na komputerze lokalnym.

1. Jeśli zestaw narzędzi platformy Azure został poprawnie zainstalowany i zalogowany, powinien zostać wyświetlony Eksplorator Azure na pasku bocznym POMYSŁu IntelliJ. Kliknij prawym przyciskiem myszy pozycję **Zarządzanie zasobami** i wybierz pozycję **Utwórz wdrożenie**.

    ![Szablon Menedżer zasobów kliknij prawym przyciskiem myszy, aby utworzyć wdrożenie](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Skonfiguruj **nazwę wdrożenia**, **subskrypcję**, **grupę zasobów**i **region**. W tym miejscu wdrażamy szablon w nowej grupie zasobów `testRG`. Następnie wybierz pozycję ścieżka dla **szablonu zasobu** jako `azuredeploy.json` i **parametry zasobu** jako pobrane `azuredeploy.parameters.json`.

    ![Szablon Menedżer zasobów wybierz pliki do utworzenia wdrożenia](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Po kliknięciu przycisku OK wdrożenie zostanie rozpoczęte. Do momentu ukończenia wdrożenia można znaleźć postęp z **paska stanu** pomysłu IntelliJ na dole.

    ![Stan wdrożenia szablonu Menedżer zasobów](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Przeglądanie istniejącego wdrożenia

1. Po zakończeniu wdrażania zostanie wyświetlona nowa grupa zasobów `testRG` i utworzone nowe wdrożenie. Kliknij prawym przyciskiem myszy wdrożenie i zobaczysz listę możliwych akcji. Teraz wybierz pozycję **Pokaż właściwości**.

    ![Wdrożenie Menedżer zasobówego przeglądania szablonów](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Widok karty zostanie otwarty, aby wyświetlić przydatne właściwości, takie jak stan wdrożenia i Struktura szablonu.

    ![Szablon Menedżer zasobów Pokaż właściwości wdrożenia](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Edytowanie i Aktualizowanie istniejącego wdrożenia

1. Wybierz pozycję **Edytuj wdrożenie** z menu rozwijanego prawym przyciskiem myszy lub widoku Pokaż właściwości przed. Zostanie otwarty inny widok karty, który pokazuje szablon i pliki parametrów wdrożenia na platformie Azure. Aby zapisać te pliki do lokalnego, można kliknąć pozycję **Eksportuj plik szablonu** lub **pliki parametrów eksportu**.

    ![Edytowanie wdrożenia szablonu Menedżer zasobów](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Można edytować te dwa pliki na tej stronie i wdrażać zmiany na platformie Azure. W tym miejscu zmodyfikujemy wartość **storageAccountType** w plikach parametrów, od `Standard_LRS` do `Standard_GRS`. Następnie kliknij pozycję **Aktualizuj wdrożenie** u dołu i Potwierdź aktualizację.

    ![Edytowanie wdrożenia szablonu Menedżer zasobów](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Po zakończeniu wdrażania aktualizacji można sprawdzić, czy w portalu zostało zmienione konto utworzonego magazynu, na `Standard_GRS`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

1. Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Można to zrobić za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure. W Eksploratorze Azure z POMYSŁu IntelliJ kliknij prawym przyciskiem myszy utworzoną **grupę zasobów** i wybierz pozycję Usuń.

    ![Usuwanie grupy zasobów w Eksploratorze platformy Azure z POMYSŁu IntelliJ](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Należy zauważyć, że usunięcie wdrożenia nie spowoduje usunięcia zasobów utworzonych przez wdrożenie. Jeśli nie są już potrzebne, Usuń odpowiednią grupę zasobów lub określone zasoby.

## <a name="next-steps"></a>Następne kroki

Głównym celem tego przewodnika Szybki Start jest użycie IntelliJ pomysł do wdrożenia istniejącego szablonu z szablonów szybkiego startu platformy Azure. Wiesz również, jak wyświetlać i aktualizować istniejące wdrożenie na platformie Azure. Szablony spośród szablonów szybkiego startu platformy Azure mogą nie zaspokajać wszystkich Twoich potrzeb. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki początkującego](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Odwiedź stronę Java w centrum deweloperów Azure](https://docs.microsoft.com/azure/java)
