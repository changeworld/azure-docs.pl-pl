---
title: Blokowanie zasobów platformy Azure, aby uniemożliwić zmiany | Dokumentacja firmy Microsoft
description: Uniemożliwianie użytkownikom aktualizowanie i usuwanie zasoby platformy Azure o znaczeniu krytycznym, stosując blokady dla wszystkich użytkowników i ról.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: 8942ae9a24613f7b7896cf7124b344d9d9315954
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360443"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian 

Jako administrator może być konieczne zablokowania subskrypcji, grupy zasobów lub zasobu, aby uniemożliwić innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie zasoby o znaczeniu krytycznym. Poziom blokady można ustawić na wartość **CanNotDelete** lub **ReadOnly**. W portalu, są nazywane blokady **Usuń** i **tylko do odczytu** odpowiednio.

* **CanNotDelete** oznacza, że autoryzowani użytkownicy nadal może odczytywać i modyfikować zasobu, ale ich nie można usunąć zasobu. 
* **Tylko do odczytu** oznacza, że autoryzowani użytkownicy mogą odczytywać zasobu, ale nie mogą usunąć ani zaktualizować zasobu. Zastosowanie Ta blokada jest podobny do ograniczania wszystkim uprawnionym użytkownikom uprawnienia przyznane przez **czytnika** roli. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Jak są stosowane blokady

Po zastosowaniu blokady w zakresie nadrzędnej wszystkie zasoby w tym zakresie dziedziczenia tego samego blokady. Nawet zasoby, które zostaną dodane później dziedziczą blokadę z obiektu nadrzędnego. Najbardziej restrykcyjne blokady w dziedziczenia ma pierwszeństwo.

W przeciwieństwie do kontroli dostępu opartej na rolach umożliwia zarządcze stosowania ograniczeń dla wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień dla użytkowników i ról, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).

Blokad usługi Resource Manager mają zastosowanie tylko do operacji, które odbywa się w płaszczyzny zarządzania, który składa się z operacji wysyłane do `https://management.azure.com`. Blokady nie Ograniczaj, jak zasoby wykonać swoje własne funkcje. Zmiany zasobu są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokadę tylko do odczytu na bazę danych SQL uniemożliwia usunięcie lub zmodyfikowanie bazy danych. Nie uniemożliwia z tworzenia, aktualizowania lub usuwania danych w bazie danych. Transakcje są dozwolone, ponieważ te operacje nie są wysyłane do `https://management.azure.com`.

Stosowanie **tylko do odczytu** może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które promieniowe wydają się być odczytana operacje rzeczywiście wymagają dodatkowych akcji. Na przykład umieszczenie **tylko do odczytu** blokadę konta magazynu uniemożliwia wszystkim użytkownikom wyświetlanie listy kluczy. Na liście, którą zwrócone klucze nie są dostępne dla operacji klucze odbywa się za pomocą żądania POST operacji zapisu. Inny przykład umieszczając **tylko do odczytu** blokady zasobu usługi App Service uniemożliwia wyświetlanie plików dla zasobu, ponieważ interakcji wymaga dostępu do zapisu Eksploratora serwera w usłudze Visual Studio.

## <a name="who-can-create-or-delete-locks"></a>Kto może utworzyć lub usunąć blokady
Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do `Microsoft.Authorization/*` lub `Microsoft.Authorization/locks/*` akcji. Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.

## <a name="managed-applications-and-locks"></a>Zarządzane aplikacje i blokowania

Użyj usług platformy Azure, takich jak usługi Azure Databricks [zarządzanych aplikacji](../managed-applications/overview.md) wdrażania usługi. W takiej sytuacji usługa tworzy dwie grupy zasobów. Jedna grupa zasobów zawiera omówienie usługi i nie jest zablokowany. Grupa zasobów zawiera infrastruktury usługi i jest zablokowany.

Jeśli spróbujesz usunąć grupę zasobów infrastruktury, otrzymasz komunikat o błędzie informujący, że grupa zasobów jest ograniczona. Jeśli zostanie podjęta próba usunięcia blokady dla grupy zasobów infrastruktury, otrzymasz komunikat o błędzie informujący, że blokada nie można usunąć, ponieważ jej właścicielem jest aplikacja systemowa

Zamiast tego należy usunąć usługę, która spowoduje również usunięcie grupy zasobów infrastruktury.

Dla zarządzanych aplikacji wybierz wdrożonej usługi.

![Wybierz usługę](./media/resource-group-lock-resources/select-service.png)

Powiadomienie usługi zawiera również link do **grupy zarządzanej z wykorzystaniem zasobów**. Tej grupy zasobów przechowuje infrastruktury i jest zablokowany. Go nie można bezpośrednio usunąć.

![Grupa zarządzana przez show](./media/resource-group-lock-resources/show-managed-group.png)

Aby usunąć wszystkie usługi, w tym infrastruktury zablokowanej grupy zasobów, wybierz pozycję **Usuń** dla usługi.

![Usuwanie usługi](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Szablon

Za pomocą szablonu usługi Resource Manager wdrażać blokadę, używasz różnych wartości dla nazwy i typu w zależności od zakresu blokady.

Podczas stosowania blokady w celu **zasobów**, użyj następujących formatów:

* Nazwa — `{resourceName}/Microsoft.Authorization/{lockName}`
* Typ — `{resourceProviderNamespace}/{resourceType}/providers/locks`

Podczas stosowania blokady w celu **grupy zasobów** lub **subskrypcji**, użyj następujących formatów:

* Nazwa — `{lockName}`
* Typ — `Microsoft.Authorization/locks`

Poniższy przykład przedstawia szablon, który tworzy plan usługi app service, witryny sieci web i blokady w witrynie sieci web. Typ zasobu blokowania jest typ zasobu zasobu do zablokowania i **/dostawców/blokad**. Nazwa blokady jest tworzona przez dołączenie nazwy zasobu z **/Microsoft.Authorization/** i nazwę blokady.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Na przykład ustawienie blokady dla grupy zasobów, zobacz [Utwórz grupę zasobów i blokowanie](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Blokady możesz wdrożyć zasoby przy użyciu programu Azure PowerShell przy użyciu [New AzResourceLock](/powershell/module/az.resources/new-azresourcelock) polecenia.

Aby zablokować zasobu, podaj nazwę zasobu, jego typ zasobu i nazwy grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Aby uzyskać informacje na temat blokady, należy użyć [Get AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock). Aby uzyskać wszystkie blokady w ramach subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceLock
```

Aby uzyskać wszystkie blokady dla zasobu, należy użyć:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Aby usunąć blokadę, należy użyć:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zablokuj możesz wdrożyć zasobów przy użyciu wiersza polecenia platformy Azure przy użyciu [tworzenie az lock](/cli/azure/lock#az-lock-create) polecenia.

Aby zablokować zasobu, podaj nazwę zasobu, jego typ zasobu i nazwy grupy zasobów.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Aby uzyskać informacje na temat blokady, należy użyć [az lock list](/cli/azure/lock#az-lock-list). Aby uzyskać wszystkie blokady w ramach subskrypcji, użyj:

```azurecli
az lock list
```

Aby uzyskać wszystkie blokady dla zasobu, należy użyć:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Aby usunąć blokadę, należy użyć:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>Interfejs API REST
Możesz zablokować wdrożonych zasobów za pomocą [interfejsu API REST dla blokady zarządzania](https://docs.microsoft.com/rest/api/resources/managementlocks). Interfejs API REST umożliwia tworzenie i usuwanie blokad i pobierania informacji o istniejących blokad.

Aby utworzyć blokadę, uruchom polecenie:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Zakres może być subskrypcji, grupy zasobów lub zasobu. Nazwa blokady jest, niezależnie od rodzaju, który chcesz wybrać blokady. W przypadku wersji interfejsu api, użyj **2016-09-01**.

W żądaniu zawierać obiekt JSON, który określa właściwości blokady.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat logicznie organizowania zasobów, zobacz [organizowanie zasobów za pomocą tagów](resource-group-using-tags.md)
* W ramach subskrypcji za pomocą zasad niestandardowych, można zastosować ograniczenia i Konwencji. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

