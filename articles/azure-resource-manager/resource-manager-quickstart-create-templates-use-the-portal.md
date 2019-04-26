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
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 4fe859b1343e2aea71bd41a3b7742a29bbff389f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464223"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Szybki start: tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal

Dowiedz się, jak wygenerować szablon usługi Resource Manager przy użyciu witryny Azure Portal, oraz poznaj proces edytowania i wdrażania tego szablonu z poziomu portalu. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](resource-group-overview.md).

![diagram portalu szybkiego startu szablonu Menedżera zasobów](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces umożliwia wdrażanie innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="generate-a-template-using-the-portal"></a>Generowanie szablonu przy użyciu witryny Azure Portal

Tworzenie szablonu usługi Resource Manager od podstaw nie jest łatwym zadaniem, zwłaszcza gdy dopiero zaczynasz korzystać z wdrażania na platformie Azure i nie znasz dobrze formatu JSON. W witrynie Azure Portal możesz skonfigurować zasób, na przykład konto usługi Azure Storage. Przed przystąpieniem do wdrażania zasobu możesz wyeksportować konfigurację do szablonu usługi Resource Manager. Szablon możesz zapisać i użyć go ponownie w przyszłości.

Wielu programistów doświadczonym szablonu umożliwia generowanie szablonów, gdy użytkownik próbuje Wdrażaj zasoby platformy Azure, które nie są zaznajomieni z tej metody. Aby uzyskać więcej informacji na temat eksportowania szablonów przy użyciu portalu, zobacz [eksportowanie grupy zasobów do szablonów](./manage-resource-groups-portal.md#export-resource-groups-to-templates). Inny sposób, aby znaleźć szablonu pracy pochodzi z [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób** > **Storage** > **Konto usługi Storage — Blob, File, Table, Queue**.

    ![Tworzenie konta usługi Azure Storage za pomocą witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Wprowadź następujące informacje:

    |Name (Nazwa)|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz pozycję **Utwórz nową** i podaj wybraną nazwę grupy zasobów. Na zrzucie ekranu nazwa grupy zasobów to *mystorage1016rg*. Grupa zasobów jest kontenerem zasobów platformy Azure. Grupa zasobów ułatwia zarządzanie zasobami platformy Azure. |
    |**Nazwa**|nadaj unikatową nazwę kontu magazynu. Nazwa konta magazynu musi być unikatowa na platformie Azure, a ona zawierać tylko małe litery i cyfry. Nazwa musi być od 3 do 24 znaków. Jeśli zostanie wyświetlony komunikat o błędzie informujący o tym, "Nazwa konta magazynu"mystorage1016"jest już zajęta", spróbuj użyć  **&lt;Twoja nazwa > magazynu&lt;bieżącą datę w MMDD >**, na przykład  **johndolestorage1016**. Aby uzyskać więcej informacji, zobacz [ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions).|

    Dla pozostałych właściwości możesz użyć wartości domyślnych.

    ![Tworzenie konfiguracji konta usługi Azure Storage za pomocą witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > W niektórych z wyeksportowanych szablonów należy wprowadzić pewne zmiany przed ich wdrożeniem.

4. Wybierz pozycję **Przeglądanie + tworzenie** w dolnej części ekranu. Nie należy wybierać **Utwórz** w następnym kroku.
5. Wybierz pozycję **Pobierz szablon do automatyzacji** w dolnej części ekranu. W portalu zostanie wyświetlony wygenerowany szablon:

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    W okienku głównym wyświetlany jest szablon. Jest to plik JSON z sześcioma elementami najwyższego poziomu — `schema`, `contentVersion`, `parameters`, `variables`, `resources` i `output`. Aby uzyskać więcej informacji, zobacz [Understand the structure and syntax of Azure Resource Manager templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](./resource-group-authoring-templates.md).

    Istnieje sześć zdefiniowanych parametrów. Jeden z nich jest nazywany **storageAccountName**. Druga wyróżniona część na poprzednim zrzucie ekranu pokazuje, jak odwoływać się do tego parametru w szablonie. W następnej sekcji będziesz edytować szablon w celu użycia wygenerowanej nazwy konta magazynu.

    W szablonie zdefiniowany jest jeden zasób platformy Azure. Typ jest `Microsoft.Storage/storageAccounts`. Trwa wyglądu, jak zasób jest zdefiniowany, a struktura definicji.
6. Wybierz **Pobierz** w górnej części ekranu. 
7. Otwórz plik zip pobrany, a następnie Zapisz **template.json** do komputera. W następnej sekcji użyjesz narzędzia do wdrażania szablonu do edycji szablonu.
8. Wybierz kartę **Parametr**, aby zobaczyć wartości podane dla parametrów. Zapisz te wartości, ponieważ będą potrzebne w następnej sekcji podczas wdrażania szablonu.

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Korzystając z pliku szablonu i pliku parametrów, można utworzyć zasobu, w tym samouczku, konto magazynu platformy Azure.

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
7. Wprowadź następujące zmiany trzy do szablonu:

    ![Szablony usługi Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Usuń **storageAccountName** parametru, jak pokazano na poprzednim zrzucie ekranu.
   - Dodaj jedną zmienną o nazwie **storageAccountName** jak pokazano na poprzednim zrzucie ekranu:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       W tym miejscu są używane dwie funkcje szablonów: `concat()` i `uniqueString()`.
   - Zaktualizuj element name zasobu **Microsoft.Storage/storageAccounts**, aby użyta została nowo zdefiniowana zmienna zamiast parametru:

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
           "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
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
8. Wybierz pozycję **Zapisz**.
9. Wprowadź następujące wartości:

    |Name (Nazwa)|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz nazwę grupy zasobów utworzonej w ostatniej sekcji. |
    |**Lokalizacja**|Wybierz lokalizację dla konta magazynu. Na przykład **Środkowe stany USA**. |
    |**Typ konta**|wprowadź wartość **Standard_LRS** na potrzeby tego przewodnika Szybki start. |
    |**rodzaj**|wprowadź wartość **StorageV2** na potrzeby tego przewodnika Szybki start. |
    |**Warstwa dostępu**|wprowadź wartość **Gorąca** na potrzeby tego przewodnika Szybki start. |
    |**Włączone tylko ruch HTTPS**| Wybierz wartość **true** na potrzeby tego przewodnika Szybki start. |
    |**Wyrażam zgodę na warunki i postanowienia, o których wspomniano powyżej**|(Wybierz)|

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

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób generowania i wdrażania szablonu w witrynie Azure Portal. Szablon używany w tym przewodniku Szybki start to prosty szablon z jednym zasobem platformy Azure. Gdy szablon jest złożony, do jego tworzenia łatwiej jest użyć programu Visual Studio Code lub Visual Studio. W kolejnym przewodniku Szybki start pokazano również sposób wdrażania szablonów przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia (CLI) platformy Azure.

> [!div class="nextstepaction"]
> [Tworzenie szablonów przy użyciu programu Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
