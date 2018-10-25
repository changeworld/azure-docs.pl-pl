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
ms.date: 10/17/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 62bc29495bab3446b6131223110c694e53412db9
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407643"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure portal

Dowiedz się, jak utworzyć swój pierwszy szablon usługi Azure Resource Manager, generując go przy użyciu witryny Azure Portal, oraz poznaj proces edytowania i wdrażania tego szablonu z poziomu witryny Azure Portal. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Instrukcje w tym samouczku umożliwiają utworzenie konta usługi Azure Storage. Ten sam proces można zastosować do tworzenia innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="generate-a-template-using-the-portal"></a>Generowanie szablonu przy użyciu witryny Azure Portal

W tej sekcji utworzysz konto magazynu przy użyciu witryny Azure Portal. Przed wdrożeniem konta magazynu możesz eksplorować szablon wygenerowany w witrynie Azure Portal na podstawie konfiguracji. Szablon możesz zapisać i użyć go ponownie w przyszłości.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób** > **Storage** > **Konto usługi Storage — Blob, File, Table, Queue**.

    ![Tworzenie konta usługi Azure Storage za pomocą witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Wprowadź następujące informacje. 

    - **Grupa zasobów**: utwórz nową grupę zasobów platformy Azure o wybranej nazwie. Na zrzucie ekranu nazwa grupy zasobów to *mystorage1016rg*.
    - **Nazwa**: nadaj unikatową nazwę kontu magazynu. Na zrzucie ekranu nazwa to *mystorage1016*.

    Dla pozostałych właściwości możesz użyć wartości domyślnych.

    ![Tworzenie konfiguracji konta usługi Azure Storage za pomocą witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > W niektórych z wyeksportowanych szablonów należy wprowadzić pewne zmiany przed ich wdrożeniem.

4. Wybierz pozycję **Przeglądanie + tworzenie** w dolnej części ekranu. 
5. Wybierz pozycję **Pobierz szablon do automatyzacji** w dolnej części ekranu. W portalu zostanie wyświetlony wygenerowany szablon:

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    W okienku głównym wyświetlany jest szablon. Jest to plik JSON z czterema elementami najwyższego poziomu – `schema`, `contentVersion`, `parameters` i `resources`. Aby uzyskać więcej informacji, zobacz [Understand the structure and syntax of Azure Resource Manager templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](./resource-group-authoring-templates.md).

    Istnieje sześć zdefiniowanych parametrów. Jeden z nich jest nazywany **storageAccountName**. Druga wyróżniona część pokazuje, jak tego parametru należy użyć w szablonie. W następnej sekcji będziesz edytować szablon w celu użycia wygenerowanej nazwy konta magazynu.

    W szablonie zdefiniowany jest jeden zasób platformy Azure. Typ to [Microsoft.Storage/storageAccounts]. Poznaj sposób definiowania zasobu i strukturę definicji.
6. Wybierz przycisk **Download** (Pobierz). Zapisz plik **template.json** z pobranego pakietu na swoim komputerze. W następnej sekcji użyjesz narzędzia do wdrażania szablonu do edycji szablonu.
7. Wybierz kartę **Parametr**, aby zobaczyć wartości podane dla parametrów. Zapisz te wartości, ponieważ będą potrzebne w następnej sekcji podczas wdrażania szablonu.

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Za pomocą pliku szablonu i pliku parametrów możesz utworzyć konto usługi Azure Storage.

## <a name="edit-and-deploy-the-template"></a>Edytowanie i wdrażanie szablonu

Witryna Azure Portal może służyć do wykonywania niektórych podstawowych czynności edycji szablonu. W tym przewodniku Szybki start użyjesz narzędzia portalu o nazwie *Wdrożenie szablonu*. Aby edytować bardziej złożony szablon, rozważ użycie programu [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), który zapewnia bardziej zaawansowane funkcje edycji.

Platforma Azure wymaga, aby każda usługa miała unikatową nazwę. Wdrożenie zakończy się niepowodzeniem, jeśli zostanie wprowadzona już istniejąca nazwa konta magazynu. Aby uniknąć tego problemu, można użyć wywołania funkcji szablonu `uniquestring()` w celu wygenerowania unikatowej nazwy konta magazynu.

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

    W tym miejscu są używane dwie funkcje: `concat()` i `uniqueString()`.

8. Usuń parametr **storageAccountName** wyróżniony na poprzednim zrzucie ekranu.
9. Zaktualizuj element name zasobu **Microsoft.Storage/storageAccounts**, aby użyta została nowo zdefiniowana zmienna zamiast parametru:

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    Ostateczny szablon powinien wyglądać następująco:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

    - **Grupa zasobów**: nadaj grupie zasobów unikatową nazwę.
    - **Lokalizacja**: wybierz lokalizację grupy zasobów.
    - **Lokalizacja**: wybierz lokalizację konta magazynu.  Możesz użyć tej samej lokalizacji co w przypadku grupy zasobów.
    - **Typ konta**: wprowadź wartość **Standard_LRS** na potrzeby tego przewodnika Szybki start.
    - **Rodzaj**: wprowadź wartość **StorageV2** na potrzeby tego przewodnika Szybki start.
    - **Warstwa dostępu**: wprowadź wartość **Gorąca** na potrzeby tego przewodnika Szybki start.
    - **Włączone tylko dla ruchu HTTPS**.  Wybierz wartość **true** na potrzeby tego przewodnika Szybki start.
    - **Wyrażam zgodę na powyższe warunki i postanowienia**: (wybierz)

    Poniżej przedstawiono zrzut ekranu przedstawiający przykładowe wdrożenie:

    ![Wdrażanie szablonów usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Wybierz pozycję **Kup**.
11. Wybierz ikonę dzwonka (powiadomienia) w górnej części ekranu, aby wyświetlić stan wdrożenia. Poczekaj na zakończenie wdrożenia.

    ![Powiadomienie dotyczące wdrażania szablonów usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. W okienku powiadomień wybierz pozycję **Przejdź do grupy zasobów**. Zobaczysz ekran podobny do poniższego:

    ![Grupa zasobów w ramach wdrażania szablonów usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Możesz zobaczyć, że stan wdrożenia został oznaczony jako zakończony pomyślnie, natomiast w grupie zasobów jest tylko jedno konto magazynu. Nazwa konta magazynu jest unikatowym ciągiem wygenerowanym przez szablon. Aby dowiedzieć się więcej o korzystaniu z kont magazynu platformy Azure, zobacz przewodnik [Szybki start — przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

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
