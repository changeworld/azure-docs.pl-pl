---
title: Tworzenie i wdrażanie szablonu Azure Resource Manager przy użyciu Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć pierwszy szablon Azure Resource Manager przy użyciu Azure Portal i jak go wdrożyć.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 708656ee88f5bacaa498bcd84fde34d69228d28b
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170286"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal

Dowiedz się, jak wygenerować szablon Menedżer zasobów przy użyciu Azure Portal i proces edytowania i wdrażania szablonu z poziomu portalu. Szablony Menedżer zasobów to pliki JSON definiujące zasoby, które należy wdrożyć dla rozwiązania. Aby zrozumieć koncepcje związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Azure Resource Manager omówienie](resource-group-overview.md).

![Diagram portalu szybkiego startu szablonu w usłudze Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces może służyć do wdrażania innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="generate-a-template-using-the-portal"></a>Generowanie szablonu przy użyciu portalu

Tworzenie szablonu Menedżer zasobów od podstaw nie jest łatwym zadaniem, szczególnie jeśli jesteś nowym wdrożeniem platformy Azure, a nie masz doświadczenia z formatem JSON. Korzystając z Azure Portal, można skonfigurować zasób, na przykład konto usługi Azure Storage. Przed wdrożeniem zasobu można wyeksportować konfigurację do szablonu Menedżer zasobów. Szablon można zapisać i ponownie użyć w przyszłości.

Wielu doświadczonych deweloperów szablonów używa tej metody do generowania szablonów podczas próby wdrożenia zasobów platformy Azure, których nie znają. Aby uzyskać więcej informacji na temat eksportowania szablonów przy użyciu portalu, zobacz [Eksportowanie grup zasobów do szablonów](./manage-resource-groups-portal.md#export-resource-groups-to-templates). Innym sposobem znalezienia szablonu roboczego jest korzystanie z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/).

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób** > **Magazyn** > **konto magazynu — obiekt BLOB, plik, tabela, kolejka**.

    ![Utwórz konto usługi Azure Storage przy użyciu Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Wprowadź następujące informacje:

    |Nazwa|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz pozycję **Utwórz nową**, a następnie określ wybraną nazwę grupy zasobów. Na zrzucie ekranu nazwa grupy zasobów to *mystorage1016rg*. Grupa zasobów to kontener zasobów platformy Azure. Grupa zasobów ułatwia zarządzanie zasobami platformy Azure. |
    |**Nazwa**|Określ unikatową nazwę konta magazynu. Nazwa konta magazynu musi być unikatowa we wszystkich wersjach systemu Azure i zawierać tylko małe litery i cyfry. Nazwa musi mieć długość od 3 do 24 znaków. Jeśli zostanie wyświetlony komunikat o błędzie informujący o tym, że "nazwa konta magazynu" mystorage1016 "jest już zajęta, spróbuj użyć **nazwy &lt;your > Storage @ no__t-2Today's w > MMDD**, na przykład **johndolestorage1016**. Aby uzyskać więcej informacji, zobacz [reguły nazewnictwa i ograniczenia](/azure/cloud-adoption-framework/ready/considerations/naming-and-tagging#resource-naming).|

    Można użyć wartości domyślnych dla pozostałej części właściwości.

    ![Utwórz konfigurację konta usługi Azure Storage przy użyciu Azure Portal](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Niektóre z wyeksportowanych szablonów wymagają modyfikacji przed ich wdrożeniem.

4. Wybierz pozycję **Przegląd + Utwórz** w dolnej części ekranu. Nie wybieraj opcji **Utwórz** w następnym kroku.
5. Wybierz pozycję **Pobierz szablon do automatyzacji** w dolnej części ekranu. W portalu jest wyświetlany wygenerowany szablon:

    ![Generowanie szablonu z poziomu portalu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    W okienku głównym zostanie wyświetlony szablon. Jest to plik JSON z sześcioma elementami najwyższego poziomu — `schema`, `contentVersion`, `parameters`, `variables`, `resources` i `output`. Aby uzyskać więcej informacji, zobacz [Opis struktury i składni szablonów Azure Resource Manager](./resource-group-authoring-templates.md)

    Zdefiniowano sześć parametrów. Jeden z nich nosi nazwę **storageAccountName**. Druga podświetlona część na poprzednim zrzucie ekranu pokazuje, jak odwołać się do tego parametru w szablonie. W następnej sekcji edytujesz szablon, aby użyć wygenerowanej nazwy dla konta magazynu.

    W szablonie jest zdefiniowany jeden zasób platformy Azure. Typ to `Microsoft.Storage/storageAccounts`. Zapoznaj się z definicją zasobu i strukturą definicji.
6. Wybierz pozycję **Pobierz** w górnej części ekranu.
7. Otwórz pobrany plik zip, a następnie Zapisz **szablon Template. JSON** na komputerze. W następnej sekcji można edytować szablon przy użyciu narzędzia wdrażania szablonów.
8. Wybierz kartę **parametr** , aby wyświetlić wartości podane dla parametrów. Zapisz te wartości, które są potrzebne w następnej sekcji podczas wdrażania szablonu.

    ![Generowanie szablonu z poziomu portalu](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Korzystając z pliku szablonu i pliku parametrów, można utworzyć zasób, w tym samouczku, koncie usługi Azure Storage.

## <a name="edit-and-deploy-the-template"></a>Edytuj i Wdróż szablon

Azure Portal może służyć do wykonywania niektórych podstawowych edycji szablonów. W tym przewodniku szybki start użyjesz narzędzia portalu o nazwie *wdrożenie szablonu*. *Wdrożenie szablonu* jest używane w tym samouczku, aby można było ukończyć cały samouczek przy użyciu jednego interfejsu — Azure Portal. Aby edytować bardziej skomplikowany szablon, należy rozważyć użycie [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), który zapewnia bardziej zaawansowane funkcje edycji.

> [!IMPORTANT]
> Wdrożenie szablonu udostępnia interfejs do testowania prostych szablonów. Nie zaleca się korzystania z tej funkcji w środowisku produkcyjnym. Zamiast tego należy przechowywać szablony na koncie usługi Azure Storage lub w repozytorium kodu źródłowego, takim jak GitHub.

Platforma Azure wymaga, aby Nazwa każdej usługi platformy Azure była unikatowa. Wdrożenie może się nie powieść, jeśli wprowadzono nazwę konta magazynu, która już istnieje. Aby uniknąć tego problemu, należy zmodyfikować szablon w celu użycia wywołania funkcji szablonu `uniquestring()` w celu wygenerowania unikatowej nazwy konta magazynu.

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **Wyszukaj w portalu Marketplace**wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
3. Wybierz **Template Deployment**.

    ![Biblioteka szablonów Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Wybierz pozycję **Utwórz**.
5. Wybierz opcję **Kompiluj własny szablon w edytorze**.
6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami dotyczącymi ładowania pliku Template. JSON pobranego w ostatniej sekcji.
7. Wprowadź następujące trzy zmiany w szablonie:

    ![Szablony Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Usuń parametr **storageAccountName** , jak pokazano na poprzednim zrzucie ekranu.
   - Dodaj jedną zmienną o nazwie **storageAccountName** , jak pokazano na poprzednim zrzucie ekranu:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       W tym miejscu są używane dwie funkcje szablonu: `concat()` i `uniqueString()`.
   - Zaktualizuj element Name zasobu **Microsoft. Storage/storageAccounts** , aby używał nowo zdefiniowanej zmiennej zamiast parametru:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     Końcowy szablon powinien wyglądać następująco:

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

    |Nazwa|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz nazwę grupy zasobów utworzoną w ostatniej sekcji. |
    |**Lokalizacja**|Wybierz lokalizację dla konta magazynu. Na przykład **środkowe stany USA**. |
    |**Typ konta**|Wprowadź **Standard_LRS** dla tego przewodnika Szybki Start. |
    |**Natur**|Wprowadź **StorageV2** dla tego przewodnika Szybki Start. |
    |**Warstwa dostępu**|Wprowadź **gorącą** dla tego przewodnika Szybki Start. |
    |**Ruch https jest włączony tylko**| Dla tego przewodnika Szybki Start wybierz **wartość true** . |
    |**Wyrażam zgodę na powyższe warunki i postanowienia**|zaznaczenia|

    Oto zrzut ekranu przedstawiający przykładowe wdrożenie:

    ![Wdrażanie szablonów Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Wybierz pozycję **Kup**.
11. Wybierz ikonę dzwonka (powiadomienia) w górnej części ekranu, aby zobaczyć stan wdrożenia. Zobaczysz, że **wdrożenie jest w toku**. Poczekaj na zakończenie wdrożenia.

    ![Powiadomienie dotyczące wdrażania szablonów Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. Wybierz pozycję **Przejdź do grupy zasobów** w okienku powiadomień. Zobaczysz ekran podobny do:

    ![Grupa zasobów wdrożenia szablonów Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Można zobaczyć, że stan wdrożenia zakończył się pomyślnie, a grupa zasobów zawiera tylko jedno konto magazynu. Nazwa konta magazynu jest unikatowym ciągiem wygenerowanym przez szablon. Aby dowiedzieć się więcej o korzystaniu z kont usługi Azure Storage, zobacz [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów.

1. W Azure Portal wybierz pozycję **Grupa zasobów** w menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy** .
3. Wybierz nazwę grupy zasobów.  Zobaczysz konto magazynu w grupie zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się generować szablon z Azure Portal oraz jak wdrożyć szablon przy użyciu portalu. Szablon używany w tym przewodniku szybki start to prosty szablon z jednym zasobem platformy Azure. Gdy szablon jest skomplikowany, łatwiej jest używać Visual Studio Code lub Visual Studio do tworzenia szablonu. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki początkującego](./template-tutorial-create-first-template.md)