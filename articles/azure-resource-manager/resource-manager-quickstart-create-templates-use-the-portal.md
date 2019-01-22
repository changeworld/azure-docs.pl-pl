---
title: Tworzenie i wdrażanie szablonu usługi Azure Resource Manager przy użyciu witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć i wdrożyć swój pierwszy szablon usługi Azure Resource Manager przy użyciu witryny Azure Portal.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/11/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: f989a006251313a8439432861477dc133374af35
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304674"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Szybki start: tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal

Dowiedz się, jak wygenerować szablon usługi Resource Manager przy użyciu witryny Azure Portal, oraz poznaj proces edytowania i wdrażania tego szablonu z poziomu portalu. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](resource-group-overview.md).

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces umożliwia wdrażanie innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="generate-a-template-using-the-portal"></a>Generowanie szablonu przy użyciu witryny Azure Portal

Tworzenie szablonu usługi Resource Manager od podstaw nie jest łatwym zadaniem, zwłaszcza gdy dopiero zaczynasz korzystać z wdrażania na platformie Azure i nie znasz dobrze formatu JSON. W witrynie Azure Portal możesz skonfigurować zasób, na przykład konto usługi Azure Storage. Przed przystąpieniem do wdrażania zasobu możesz wyeksportować konfigurację do szablonu usługi Resource Manager. Szablon możesz zapisać i użyć go ponownie w przyszłości.

Wielu doświadczonych twórców szablonów używa tej metody do generowania szablonów roboczych, gdy próbują wdrażać zasoby platformy Azure, których nie znają.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób** > **Storage** > **Konto usługi Storage — Blob, File, Table, Queue**.

    ![Tworzenie konta usługi Azure Storage za pomocą witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Wprowadź następujące informacje:

    - **Grupa zasobów**: Wybierz pozycję **Utwórz nową** i podaj wybraną nazwę grupy zasobów. Na zrzucie ekranu nazwa grupy zasobów to *mystorage1016rg*. Grupa zasobów jest kontenerem zasobów platformy Azure. Grupa zasobów ułatwia zarządzanie zasobami platformy Azure.
    - **Nazwa**: nadaj unikatową nazwę kontu magazynu. Na zrzucie ekranu nazwa to *mystorage1016*.

    Dla pozostałych właściwości możesz użyć wartości domyślnych.

    ![Tworzenie konfiguracji konta usługi Azure Storage za pomocą witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > W niektórych z wyeksportowanych szablonów należy wprowadzić pewne zmiany przed ich wdrożeniem.

4. Wybierz pozycję **Przeglądanie + tworzenie** w dolnej części ekranu.
5. Wybierz pozycję **Pobierz szablon do automatyzacji** w dolnej części ekranu. W portalu zostanie wyświetlony wygenerowany szablon:

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    W okienku głównym wyświetlany jest szablon. Jest to plik JSON z czterema elementami najwyższego poziomu — `schema`, `contentVersion`, `parameters`, `variables`, `resources` i `output`. Aby uzyskać więcej informacji, zobacz [Understand the structure and syntax of Azure Resource Manager templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](./resource-group-authoring-templates.md).

    Istnieje sześć zdefiniowanych parametrów. Jeden z nich jest nazywany **storageAccountName**. Druga wyróżniona część na poprzednim zrzucie ekranu pokazuje, jak odwoływać się do tego parametru w szablonie. W następnej sekcji będziesz edytować szablon w celu użycia wygenerowanej nazwy konta magazynu.

    W szablonie zdefiniowany jest jeden zasób platformy Azure. Typ to [Microsoft.Storage/storageAccounts]. Poznaj sposób definiowania zasobu i strukturę definicji.
6. Wybierz przycisk **Download** (Pobierz). Zapisz plik **template.json** z pobranego pakietu na swoim komputerze. W następnej sekcji użyjesz narzędzia do wdrażania szablonu do edycji szablonu.
7. Wybierz kartę **Parametr**, aby zobaczyć wartości podane dla parametrów. Zapisz te wartości, ponieważ będą potrzebne w następnej sekcji podczas wdrażania szablonu.

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Za pomocą pliku szablonu i pliku parametrów możesz utworzyć zasób — w tym samouczku jest to konto magazynu platformy Azure.

## <a name="edit-and-deploy-the-template"></a>Edytowanie i wdrażanie szablonu

Witryna Azure Portal może służyć do wykonywania niektórych podstawowych czynności edycji szablonu. W tym przewodniku Szybki start użyjesz narzędzia portalu o nazwie *Wdrożenie szablonu*. Narzędzie *Wdrożenie szablonu* jest używane w tym samouczku, aby umożliwić ukończenie całego samouczka przy użyciu jednego interfejsu — witryny Azure Portal. Aby edytować bardziej złożony szablon, rozważ użycie programu [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), który zapewnia bardziej zaawansowane funkcje edycji.

Platforma Azure wymaga, aby każda usługa miała unikatową nazwę. Wdrożenie może zakończyć się niepowodzeniem, jeśli zostanie wprowadzona już istniejąca nazwa konta magazynu. W celu uniknięcia tego problemu zmodyfikuj szablon, aby użyć wywołania funkcji szablonu `uniquestring()` do wygenerowania unikatowej nazwy konta magazynu.

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**.
2. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.
3. Wybierz pozycję **Wdrożenie szablonu**.

    ![Biblioteka szablonów usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Wybierz pozycję **Utwórz**.
5. Wybierz pozycję **Utwórz własny szablon w edytorze**.
6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować plik template.json pobrany w poprzedniej sekcji.
7. Dodaj jedną zmienną, jak pokazano na poniższym zrzucie ekranu:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
    ```
    ![Szablony usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

    W tym miejscu są używane dwie funkcje szablonów: `concat()` i `uniqueString()`.

8. Usuń parametr **storageAccountName** wyróżniony na poprzednim zrzucie ekranu.
9. Zaktualizuj element name zasobu **Microsoft.Storage/storageAccounts**, aby użyta została nowo zdefiniowana zmienna zamiast parametru:

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    Ostateczny szablon powinien wyglądać następująco:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string"
            },
            "accountType": {
                "type": "string"
            },
            "kind": {
                "type": "string"
            },
            "accessTier": {
                "type": "string"
            },
            "supportsHttpsTrafficOnly": {
                "type": "bool"
            }
        },
        "variables": {
            "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
            {
                "name": "[variables('storageAccountName')]",
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2018-07-01",
                "location": "[parameters('location')]",
                "properties": {
                    "accessTier": "[parameters('accessTier')]",
                    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
                },
                "dependsOn": [],
                "sku": {
                    "name": "[parameters('accountType')]"
                },
                "kind": "[parameters('kind')]"
            }
        ],
        "outputs": {}
    }
    ```
7. Wybierz pozycję **Zapisz**.
8. Wprowadź następujące wartości:

    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj grupie zasobów unikatową nazwę.
    - **Lokalizacja**: wybierz lokalizację grupy zasobów. Na przykład **Środkowe stany USA**. 
    - **Lokalizacja**: wybierz lokalizację konta magazynu. Na przykład **Środkowe stany USA**.
    - **Typ konta**: wprowadź wartość **Standard_LRS** na potrzeby tego przewodnika Szybki start.
    - **Rodzaj**: wprowadź wartość **StorageV2** na potrzeby tego przewodnika Szybki start.
    - **Warstwa dostępu**: wprowadź wartość **Gorąca** na potrzeby tego przewodnika Szybki start.
    - **Włączone tylko dla ruchu HTTPS**.  Wybierz wartość **true** na potrzeby tego przewodnika Szybki start.
    - **Wyrażam zgodę na powyższe warunki i postanowienia**: (wybierz)

    Poniżej przedstawiono zrzut ekranu przedstawiający przykładowe wdrożenie:

    ![Wdrażanie szablonów usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Wybierz pozycję **Kup**.
11. Wybierz ikonę dzwonka (powiadomienia) w górnej części ekranu, aby wyświetlić stan wdrożenia. Powinien zostać wyświetlony komunikat **Wdrożenie jest w toku**. Poczekaj na zakończenie wdrożenia.

    ![Powiadomienie dotyczące wdrażania szablonów usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. W okienku powiadomień wybierz pozycję **Przejdź do grupy zasobów**. Zobaczysz ekran podobny do poniższego:

    ![Grupa zasobów w ramach wdrażania szablonów usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Możesz zobaczyć, że stan wdrożenia został oznaczony jako zakończony pomyślnie, natomiast w grupie zasobów jest tylko jedno konto magazynu. Nazwa konta magazynu jest unikatowym ciągiem wygenerowanym przez szablon. Aby dowiedzieć się więcej o korzystaniu z kont usługi Azure Storage, zobacz [Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  Zostanie wyświetlone konto magazynu w grupie zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** w górnym menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób generowania i wdrażania szablonu w witrynie Azure Portal. Szablon używany w tym przewodniku Szybki start to prosty szablon z jednym zasobem platformy Azure. Gdy szablon jest złożony, do jego tworzenia łatwiej jest użyć programu Visual Studio Code lub Visual Studio. W kolejnym przewodniku Szybki start pokazano również sposób wdrażania szablonów przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia (CLI) platformy Azure.

> [!div class="nextstepaction"]
> [Tworzenie szablonów przy użyciu programu Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
