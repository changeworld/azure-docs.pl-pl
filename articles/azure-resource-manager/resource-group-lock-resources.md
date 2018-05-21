---
title: Blokowanie zasobów platformy Azure, aby zapobiec zmianom | Dokumentacja firmy Microsoft
description: Uniemożliwić użytkownikom aktualizowanie lub usuwanie kluczowych zasobów platformy Azure, stosując blokady dla wszystkich użytkowników i ról.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 57e1226a263a045784f0278c68246c0e953f6e22
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Blokowanie zasobów, aby uniemożliwić nieoczekiwane zmiany 

Jako administrator może być konieczne zablokować subskrypcji, grupy zasobów lub zasobów uniemożliwić innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie kluczowych zasobów. Można ustawić poziom blokady **CanNotDelete** lub **tylko do odczytu**. W portalu, są nazywane blokad **usunąć** i **tylko do odczytu** odpowiednio.

* **CanNotDelete** oznacza, że autoryzowani użytkownicy mogą nadal odczytywać i modyfikować zasobu, ale ich nie można usunąć tego zasobu. 
* **Tylko do odczytu** oznacza, że autoryzowani użytkownicy mogą odczytywać zasobu, ale nie można usunąć lub zaktualizować zasobu. Stosowanie Ta blokada jest podobny do ograniczania wszystkim uprawnionym użytkownikom uprawnienia przyznane przez **czytnika** roli. 

## <a name="how-locks-are-applied"></a>Sposób stosowania blokad

Po zastosowaniu blokady w zakresie nadrzędnym, wszystkie zasoby w ramach tego zakresu dziedziczą tego samego blokady. Nawet zasoby, które później zostaną dodane dziedziczą blokady z obiektu nadrzędnego. Najbardziej restrykcyjne blokady w dziedziczenia ma pierwszeństwo.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania służy do stosowania ograniczenia we wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień dla użytkowników i ról, zobacz [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md).

Menedżer zasobów blokad mają zastosowanie tylko do operacji, które pojawiają się w płaszczyźnie zarządzania, która składa się z operacji wysyłane do `https://management.azure.com`. Blokad nie ograniczają jak zasoby wykonywanie własnych funkcji. Zmiany zasobu jest ograniczony, ale operacje zasobów nie są ograniczone. Na przykład tylko do odczytu blokady na bazę danych SQL pozwala usuwanie i modyfikowanie bazy danych, ale go nie uniemożliwiają tworzenie, aktualizowanie lub usuwanie danych w bazie danych. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do `https://management.azure.com`.

Stosowanie **tylko do odczytu** może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które się wydawać odczytu operacje rzeczywiście wymagają dodatkowych czynności. Na przykład wprowadzenie **tylko do odczytu** blokady na koncie magazynu uniemożliwia wyświetlanie kluczy wszystkich użytkowników. Listy kluczy operacji jest obsługiwany za pomocą żądania POST, ponieważ zwrócony klucze są dostępne do zapisu. Innym przykładem umieszczenie **tylko do odczytu** blokada na zasób usługi aplikacji uniemożliwia wyświetlanie plików dla zasobu, ponieważ dostęp do zapisu wymaga interakcji Eksploratora serwera w usłudze Visual Studio.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Który można utworzyć lub usunąć blokady w organizacji
Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do `Microsoft.Authorization/*` lub `Microsoft.Authorization/locks/*` akcje. Wbudowanych ról, tylko **właściciela** i **Administrator dostępu użytkowników** otrzymują te akcje.

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Szablon
W poniższym przykładzie przedstawiono szablon, który tworzy plan usługi aplikacji, witryn sieci web i blokady w witrynie sieci web. Typ zasobu blokowania jest typ zasobu zasobu do zablokowania i **/dostawców/blokad**. Nazwa blokady jest tworzony przez łączenie Nazwa zasobu z **/Microsoft.Authorization/** i nazwy blokady.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
Zablokuj należy wdrożyć zasobów przy użyciu programu Azure PowerShell przy użyciu [AzureRmResourceLock nowy](/powershell/module/azurerm.resources/new-azurermresourcelock) polecenia.

Aby zablokować zasobu, należy podać nazwę zasobu, jego typ zasobu i jego nazwa grupy zasobów.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Aby uzyskać informacje o blokadę, użyj [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Aby uzyskać wszystkie blokady w ramach subskrypcji, należy użyć:

```powershell
Get-AzureRmResourceLock
```

Aby uzyskać wszystkie blokady dla zasobu, należy użyć:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby uzyskać wszystkie blokady dla grupy zasobów, należy użyć:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Aby usunąć blokadę, należy użyć:

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zablokuj należy wdrożyć zasobów przy użyciu wiersza polecenia platformy Azure przy użyciu [utworzyć blokady az](/cli/azure/lock#az_lock_create) polecenia.

Aby zablokować zasobu, należy podać nazwę zasobu, jego typ zasobu i jego nazwa grupy zasobów.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Aby uzyskać informacje o blokadę, użyj [listy blokady az](/cli/azure/lock#az_lock_list). Aby uzyskać wszystkie blokady w ramach subskrypcji, należy użyć:

```azurecli
az lock list
```

Aby uzyskać wszystkie blokady dla zasobu, należy użyć:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Aby uzyskać wszystkie blokady dla grupy zasobów, należy użyć:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Aby usunąć blokadę, należy użyć:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>Interfejs API REST
Można zablokować wdrożonych zasobów z [interfejsu API REST zarządzania blokad](https://docs.microsoft.com/rest/api/resources/managementlocks). Interfejs API REST umożliwia tworzenie i usuwanie blokad i pobierania informacji o istniejące blokady.

Aby utworzyć blokadę, uruchom polecenie:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Zakres może być subskrypcji, grupy zasobów lub zasobów. Nazwa blokady jest dowolne wywołać blokady. Wersja interfejsu api, można użyć **2015-01-01**.

W żądaniu obejmują obiekt JSON, który określa właściwości blokady.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat logicznie organizowania zasobów, zobacz [przy użyciu tagów do organizowania zasobów](resource-group-using-tags.md)
* Aby zmienić grupę zasobów, zasób znajduje się w, zobacz [przenoszenia zasobów do nowej grupy zasobów](resource-group-move-resources.md)
* Ograniczenia i konwencje można stosować w subskrypcji z niestandardowych zasad. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../azure-policy/azure-policy-introduction.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

