---
title: Przenoszenie zasobów platformy Azure do innej grupy zasobów | Microsoft Docs
description: Dowiedz się, jak przenieść zasoby platformy Azure z jednej grupy zasobów do innej lub z jednej subskrypcji do innej.
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
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 62e35a361f2fcf9d6f484b699084a78f66121957
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310101"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group"></a>Samouczek: Przenoszenie zasobów platformy Azure do innej grupy zasobów

Dowiedz się, jak przenieść zasoby platformy Azure z jednej grupy zasobów do innej. Możesz także przenieść zasoby platformy Azure z jednej subskrypcji platformy Azure do innej. W tym samouczku użyto szablonu usługi Resource Manager do wdrożenia dwóch grup zasobów i jednego konta magazynu. Następnie przeniesiono konto magazynu z jednej grupy zasobów do innej.

![Usługi Azure Resource Manager przenoszenia zasobów diagramu](./media/resource-manager-tutorial-move-resources/resource-manager-template-move-resources.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowanie zasobów.
> * Sprawdzenie, czy zasoby można przenieść.
> * Sporządzenie listy kontrolnej przed przeniesieniem zasobu.
> * Zweryfikowanie operacji przenoszenia.
> * Przeniesienie zasobu.
> * Oczyszczenie zasobów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prepare-the-resources"></a>Przygotowanie zasobów

Szablon został utworzony i umieszczony w [udostępnionym koncie magazynu](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). Szablon ten definiuje dwie grupy zasobów i jedno konto magazynu. Podczas wdrażania szablonu należy podać nazwę projektu. Nazwa projektu służy do wygenerowania unikatowych nazw zasobów.  Z szablonu jest wyodrębniany następujący kod JSON:

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Zwróć uwagę na lokalizacje określone w kodzie JSON — dwie grupy zasobów znajdują się w regionie Wschodnie stany USA i Zachodnie stany USA. Konto magazynu znajduje się w regionie Wschodnie stany USA. Podczas przenoszenia zasobu do innej grupy zasobów w innej lokalizacji operacja przenoszenia nie zmienia lokalizacji zasobu.

Wybierz pozycję **Wypróbuj**, aby otworzyć usługę Cloud Shell, a następnie wykonaj skrypt programu PowerShell wewnątrz usługi Cloud Shell:

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Zaczekaj na pomyślne zakończenie działania skryptu, a następnie otwórz witrynę [Azure Portal](https://portal.azure.com) i sprawdź, czy grupy zasobów oraz konto magazynu zostały wdrożone zgodnie z oczekiwaniami.

> [!NOTE]
> Ponieważ szablon definiuje dwie grupy zasobów, to wdrożenie jest traktowane jako wdrożenie poziomu subskrypcji. Wdrożenie szablonu portalu nie obsługuje wdrożeń poziomu subskrypcji. W tym samouczku program Azure PowerShell jest używany właśnie w ten sposób. Interfejs wiersza polecenia platformy Azure również obsługuje wdrożenia poziomu subskrypcji. Zobacz [Tworzenie grup zasobów i zasobów w subskrypcji](./deploy-to-subscription.md).

## <a name="verify-the-resource-can-be-moved"></a>Sprawdzenie, czy zasoby można przenieść

Nie wszystkie zasoby można przenieść. Zasobem używanym w tym samouczku jest konto magazynu, które można przenieść. Aby sprawdzić, czy dany zasób można przenieść, zobacz [Usługi, które można przenieść](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Sporządzenie listy kontrolnej przed przeniesieniem zasobów

Ten krok jest opcjonalny w tym samouczku, ponieważ został już wykonany.

Przed przeniesieniem zasobu należy wykonać kilka ważnych czynności. Zobacz [Sporządzenie listy kontrolnej przed przeniesieniem zasobów](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Zweryfikowanie przeniesienia

W tym samouczku zweryfikowanie przeniesienia jest opcjonalne, ponieważ zostało już wykonane.

Operacja weryfikowania przenoszenia umożliwia testowanie scenariusza przenoszenia bez faktycznego przenoszenia zasobów. Użyj tej operacji, aby określić, czy przeniesienie się powiodło. Aby uzyskać więcej informacji, zobacz [Zweryfikowanie przeniesienia](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Przenoszenie zasobu

Konto magazynu znajduje się wewnątrz źródłowej grupy zasobów (rg1). Uruchom następujący skrypt programu PowerShell, aby przenieść zasób do docelowej grupy zasobów (rg2). Pamiętaj, aby użyć tej samej nazwy projektu, której użyto podczas wdrażania zasobów.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Otwórz witrynę [Azure Portal](https://portal.azure.com), sprawdź, czy konto magazynu zostało przeniesione do innej grupy zasobów, i sprawdź także, czy lokalizacja konta magazynu to wciąż Wschodnie stany USA.

Podczas przenoszenia zasobów zarówno grupa źródłowa, jak i docelowa są blokowane na czas operacji. Operacje zapisu i usuwania na grupach zasobów są blokowane do momentu zakończenia przenoszenia. Ta blokada oznacza, że nie można dodawać, aktualizować ani usuwać zasobów w tych grupach zasobów, ale nie oznacza to, że zasoby są zamrożone. Jeśli na przykład przeniesiesz program SQL Server i jego bazę danych do nowej grupy zasobów, nie dojdzie do przestoju aplikacji korzystającej z tej bazy danych. Nadal będzie możliwe odczytywanie i zapisywanie danych w bazie danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę źródłowej grupy zasobów.  
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.
5. Wybierz nazwę docelowej grupy zasobów.  
6. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono, jak przenieść konto magazynu z jednej grupy zasobów do innej. Do tej pory opisywano pracę z jednym kontem magazynu lub wieloma wystąpieniami konta magazynu. W następnym samouczku utworzysz szablon z wieloma zasobami i wieloma typami zasobów. Niektóre zasoby zawierają zasoby zależne.

> [!div class="nextstepaction"]
> [Tworzenie zasobów zależnych](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
