---
title: Używanie programu Visual Studio Code do tworzenia szablonu usługi Azure Resource Manager | Microsoft Docs
description: Rozszerzenie Narzędzia usługi Azure Resource Manager służy do pracy z szablonami usługi Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/17/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: a66d845fcf7507613cda475bbc96225f8a7cc7eb
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126875"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Szybki start: Tworzenie szablonów usługi Azure Resource Manager przy użyciu programu Visual Studio Code

Dowiedz się, jak tworzyć szablony usługi Azure Resource Manager przy użyciu programu Visual Studio Code i rozszerzenia Narzędzia usługi Azure Resource Manager. Szablony usługi Resource Manager można tworzyć w programie Visual Studio Code bez korzystania z rozszerzenia, ale rozszerzenie udostępnia opcje autouzupełniania, które upraszczają proces tworzenia szablonu. Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](resource-group-overview.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

- [Program Visual Studio Code](https://code.visualstudio.com/)
- Rozszerzenie Narzędzia usługi Resource Manager. Aby przeprowadzić instalację, wykonaj następujące kroki:

    1. Otwórz program Visual Studio Code.
    2. Naciśnij klawisze **CTRL + SHIFT + X**, aby otworzyć okienko rozszerzenia.
    3. Wyszukaj pozycję **Azure Resource Manager Tools**, a następnie wybierz pozycję **Install (Zainstaluj)**.
    4. Wybierz pozycję **Reload (Załaduj ponownie)**, aby zakończyć instalację rozszerzenia.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager.

Szablon używany w tym przewodniku Szybki start ma nazwę [Create a standard storage account (Tworzenie standardowego konta magazynu)](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Szablon definiuje zasób konta usługi Azure Storage.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **File (Plik)**>**Save As (Zapisz jako)**, aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="edit-the-template"></a>Edytowanie szablonu

Aby dowiedzieć się, jak edytować szablon za pomocą programu Visual Studio Code, dodaj co najmniej jeden element do sekcji danych wyjściowych.

1. W programie Visual Studio Code dodaj co najmniej jeden element danych wyjściowych do wyeksportowanego szablonu:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Po wykonaniu wszystkich czynności sekcja danych wyjściowych będzie wyglądać następująco:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Jeśli skopiowano i wklejono kod w programie Visual Studio Code, spróbuj ponownie wpisać element **value**, aby użyć funkcji intellisense rozszerzenia Narzędzia usługi Resource Manager.

    ![Funkcja intellisense programu Visual Studio Code szablonu usługi Resource Manager](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Wybierz pozycję **File (Plik)**>**Save (Zapisz)**, aby zapisać plik.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Istnieje wiele metod wdrażania szablonów.  W tym przewodniku Szybki Start zostanie użyta usługa Cloud Shell z poziomu witryny Azure Portal. Usługa Cloud Shell obsługuje zarówno wiersz polecenia platformy Azure, jak i program Azure PowerShell. W instrukcjach podanych w tym miejscu używany jest interfejs wiersza polecenia.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Cloud Shell** w prawym górnym rogu, jak pokazano na poniższym obrazie:

    ![Usługa Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell.png)

3. Wybierz strzałkę w dół, a następnie wybierz pozycję **Bash**, aby przełączyć się do interfejsu wiersza polecenia z programu PowerShell.

    ![Interfejs wiersza polecenia usługi Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)
4. Wybierz pozycję **Uruchom ponownie**, aby ponownie uruchomić powłokę.
5. Wybierz pozycję **Przekaż/pobierz pliki**, a następnie wybierz pozycję **Przekaż**.

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
4. Wybierz plik, który został wcześniej zapisany w ramach tego przewodnika Szybki start. Nazwa domyślna to **azuredeploy.json**.
5. W usłudze Cloud shell uruchom polecenie **ls**, aby zweryfikować, czy plik został pomyślnie przekazany. Można również użyć polecenia **cat**, aby zweryfikować zawartość szablonu.

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
6. W usłudze Cloud Shell uruchom następujące polecenia:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
    Poniżej przedstawiono zrzut ekranu przedstawiający przykładowe wdrożenie:

    ![Wdrażanie szablonu w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    Na zrzucie ekranu zostały użyte następujące wartości:

    - **&lt;ResourceGroupName>**: myresourcegroup0709. Istnieją dwa wystąpienia tego parametru.  Pamiętaj, aby użyć tej samej wartości.
    - **&lt;AzureLocation>**: eastus2
    - **&lt;DeployName>**: mydeployment0709
    - **&lt;TemplateFile>**: azuredeploy.json

    W danych wyjściowych na zrzucie ekranu nazwa konta magazynu to *3tqebj3slyfyestandardsa*. 

7. Uruchom następujące polecenie programu PowerShell, aby wyświetlić nowo utworzone konto magazynu:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia szablonu za pomocą programu Visual Studio Code oraz sposób wdrażania szablonu przy użyciu witryny usługi Cloud Shell w witrynie Azure Portal. Szablon używany w tym przewodniku Szybki start zawiera tylko jeden zasób platformy Azure.  W następnym samouczku zostanie utworzony szablon z wieloma zasobami.  Niektóre zasoby zawierają zasoby zależne.

> [!div class="nextstepaction"]
> [Tworzenie szablonów przy użyciu programu Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
