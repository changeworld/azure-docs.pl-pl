---
title: Samouczek — eksportowanie szablonu z witryny Azure portal
description: Dowiedz się, jak użyć wyeksportowanego szablonu do ukończenia tworzenia szablonów.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5a0d373fdf75f19c8fc1082593c15c14770f79c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369897"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Samouczek: Używanie wyeksportowanego szablonu z witryny Azure portal

W tej serii samouczków utworzono szablon do wdrożenia konta magazynu platformy Azure. W następnych dwóch samouczkach dodajesz *plan usługi app service* i *witrynę sieci Web*. Zamiast tworzyć szablony od podstaw, dowiesz się, jak eksportować szablony z witryny Azure portal i jak używać przykładowych szablonów z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/). Dostosuj te szablony do użytku. Ten samouczek koncentruje się na eksportowaniu szablonów i dostosowywaniu wyniku dla szablonu. Trwa około **14 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zaleca się ukończenie [samouczka na temat wyjść,](template-tutorial-add-outputs.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Szablon recenzji

Na końcu poprzedniego samouczka szablon miał następujący JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Ten szablon działa dobrze do wdrażania kont magazynu, ale można dodać więcej zasobów do niego. Można wyeksportować szablon z istniejącego zasobu, aby szybko uzyskać JSON dla tego zasobu.

## <a name="create-app-service-plan"></a>Tworzenie planu usługi App Service

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz pozycję **Utwórz zasób**.
1. W **obszarze Wyszukaj w portalu Marketplace**wprowadź plan usługi app **service,** a następnie wybierz pozycję **Plan usługi aplikacji**.  Nie wybieraj **planu usługi aplikacji (klasyczny)**
1. Wybierz **pozycję Utwórz**.
1. Wprowadź:

    - **Subskrypcja:** wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: Wybierz pozycję **Utwórz nowy,** a następnie określ nazwę. Podaj inną nazwę grupy zasobów niż ta, której używasz w tej serii samouczków.
    - **Nazwa**: wprowadź nazwę planu usługi aplikacji.
    - **System operacyjny**: wybierz **Linux**.
    - **Region**: wybierz lokalizację platformy Azure. Na przykład **Środkowe stany USA**.
    - **Warstwa cenowa:** aby zaoszczędzić koszty, zmień jednostkę SKU na **Podstawową B1** (w obszarze Dev/Test).

    ![Portal szablonu eksportu szablonu Menedżera zasobów](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Wybierz **pozycję Przejrzyj i utwórz**.
1. Wybierz **pozycję Utwórz**. Utworzenie zasobu zajmuje kilka chwil.

## <a name="export-template"></a>Eksportowanie szablonu

1. Wybierz pozycję **Przejdź do zasobu**.

    ![Przechodzenie do zasobu](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Wybierz **pozycję Eksportuj szablon**.

    ![Szablon eksportu szablonu Menedżera zasobów](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Funkcja szablonu eksportu przyjmuje bieżący stan zasobu i generuje szablon, aby go wdrożyć. Eksportowanie szablonu może być pomocnym sposobem szybkiego uzyskania JSON potrzebne do wdrożenia zasobu.

1. Skopiuj do szablonu definicję **farmy microsoft.Web/serverfarms** i definicję parametru.

    ![Szablon eksportu szablonu Menedżera zasobów wyeksportowany szablon](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Zazwyczaj eksportowany szablon jest bardziej szczegółowy niż podczas tworzenia szablonu. Na przykład obiekt SKU w eksportowanym szablonie ma pięć właściwości. Ten szablon działa, ale można po prostu użyć właściwości **name.** Możesz zacząć od wyeksportowanego szablonu, a następnie zmodyfikować go tak, jak chcesz, aby dopasować go do swoich wymagań.

## <a name="revise-existing-template"></a>Poprawianie istniejącego szablonu

Wyeksportowany szablon zapewnia większość potrzebnych JSON, ale należy dostosować go do szablonu. Należy zwrócić szczególną uwagę na różnice w parametrach i zmiennych między szablonem a eksportowanym szablonem. Oczywiście proces eksportowania nie zna parametrów i zmiennych, które zostały już zdefiniowane w szablonie.

W poniższym przykładzie wyróżniono dodatki do szablonu. Zawiera eksportowany kod oraz pewne zmiany. Najpierw zmienia nazwę parametru, aby dopasować konwencję nazewnictwa. Po drugie używa parametru lokalizacji dla lokalizacji planu usługi aplikacji. Po trzecie, usuwa **nazwę** wewnątrz obiektu **właściwości,** ponieważ ta wartość jest nadmiarowa z **właściwością name** na poziomie zasobu.

Skopiuj cały plik i zastąp szablon jego zawartością.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby wdrożyć szablon.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupę zasobów z witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Grupa zasobów zawiera konto magazynu i plan usługi app service.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak wyeksportować szablon z witryny Azure portal i jak używać eksportowanego szablonu dla tworzenia szablonów. Można również użyć szablonów szybki start platformy Azure, aby uprościć tworzenie szablonów.

> [!div class="nextstepaction"]
> [Korzystanie z szablonów szybki start platformy Azure](template-tutorial-quickstart-template.md)
