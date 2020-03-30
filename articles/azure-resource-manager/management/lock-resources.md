---
title: Blokowanie zasobów w celu zapobiegania zmianom
description: Uniemożliwić użytkownikom aktualizowanie lub usuwanie krytycznych zasobów platformy Azure przez zastosowanie blokady dla wszystkich użytkowników i ról.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274010"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian

Jako administrator możesz zablokować subskrypcję, grupę zasobów lub zasób, aby zapobiec przypadkowemu usunięciu lub zmodyfikowaniu kluczowych zasobów przez innych użytkowników w organizacji. Poziom blokady można ustawić na wartość **CanNotDelete** lub **ReadOnly**. W portalu blokady są **nazywane Delete** i **Tylko do odczytu** odpowiednio.

* **CanNotDelete** oznacza, że autoryzowani użytkownicy mogą nadal odczytywać i modyfikować zasób, ale nie mogą usunąć zasobu. 
* **ReadOnly** oznacza, że autoryzowani użytkownicy mogą odczytywać zasób, ale nie mogą usuwać ani aktualizować zasobu. Zastosowanie tej blokady jest podobne do ograniczania wszystkich autoryzowanych użytkowników do uprawnień przyznanych przez rolę **czytelnika.**

## <a name="how-locks-are-applied"></a>Jak stosowane są blokady

Po zastosowaniu blokady w zakresie nadrzędnym wszystkie zasoby w tym zakresie dziedziczą tę samą blokadę. Nawet zasoby, które można dodać później dziedziczą blokadę z nadrzędnego. Najbardziej restrykcyjna blokada w dziedziczeniu ma pierwszeństwo.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania są używane do stosowania ograniczenia do wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień dla użytkowników i ról, zobacz [Kontrola dostępu oparta na rolach platformy Azure](../../role-based-access-control/role-assignments-portal.md).

Blokady usługi Resource Manager dotyczą tylko operacji wykonywanych na płaszczyźnie zarządzania, która składa się z operacji wysyłanych do witryny `https://management.azure.com`. Blokady nie ograniczają sposobu wykonywania własnych funkcji przez zasoby. Zmiany zasobów są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokada readonly w bazie danych SQL uniemożliwia usunięcie lub zmodyfikowanie bazy danych. Nie uniemożliwia tworzenia, aktualizowania ani usuwania danych w bazie danych. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do witryny `https://management.azure.com`.

Zastosowanie **ReadOnly** może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które nie wydają się modyfikować zasobu rzeczywiście wymagają akcji, które są blokowane przez blokadę. Blokada **Tylko do odczytu** może być zastosowana do zasobu lub do grupy zasobów zawierającej zasób. Oto kilka typowych przykładów operacji, które są blokowane przez blokadę **ReadOnly:**

* Blokada **tylko** do odczytu na koncie magazynu uniemożliwia wszystkim użytkownikom wyświetlanie listy kluczy. Operacje listy kluczy są obsługiwane za pomocą żądania POST, ponieważ zwrócone klucze są dostępne dla operacji zapisu.

* Blokada **tylko** do odczytu zasobu usługi app service uniemożliwia Visual Studio Server Explorer wyświetlania plików dla zasobu, ponieważ ta interakcja wymaga dostępu do zapisu.

* Blokada **tylko** do odczytu w grupie zasobów, która zawiera maszynę wirtualną, uniemożliwia wszystkim użytkownikom uruchamianie lub ponowne uruchamianie maszyny wirtualnej. Operacje te wymagają żądania POST.

## <a name="who-can-create-or-delete-locks"></a>Kto może tworzyć lub usuwać blokady

Aby utworzyć lub usunąć blokady zarządzania, musisz mieć dostęp do `Microsoft.Authorization/*` lub `Microsoft.Authorization/locks/*` akcje. Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.

## <a name="managed-applications-and-locks"></a>Aplikacje zarządzane i blokady

Niektóre usługi platformy Azure, takie jak Azure Databricks, używają [zarządzanych aplikacji](../managed-applications/overview.md) do zaimplementowania usługi. W takim przypadku usługa tworzy dwie grupy zasobów. Jedna grupa zasobów zawiera omówienie usługi i nie jest zablokowana. Druga grupa zasobów zawiera infrastrukturę dla usługi i jest zablokowana.

Jeśli spróbujesz usunąć grupę zasobów infrastruktury, zostanie wyświetlony błąd informujący, że grupa zasobów jest zablokowana. Jeśli spróbujesz usunąć blokadę dla grupy zasobów infrastruktury, zostanie wyświetlony błąd informujący, że blokada nie może zostać usunięta, ponieważ jest własnością aplikacji systemowej.

Zamiast tego usuń usługę, która również usuwa grupę zasobów infrastruktury.

W przypadku aplikacji zarządzanych wybierz wdrożoną usługę.

![Wybierz usługę](./media/lock-resources/select-service.png)

Zwróć uwagę, że usługa zawiera łącze dla **zarządzanej grupy zasobów**. Ta grupa zasobów przechowuje infrastrukturę i jest zablokowana. Nie można go bezpośrednio usunąć.

![Pokaż grupę zarządzana](./media/lock-resources/show-managed-group.png)

Aby usunąć wszystko dla usługi, w tym grupę zasobów zablokowanej infrastruktury, wybierz **pozycję Usuń** dla usługi.

![Usuń usługę](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Kopie zapasowe i blokady platformy Azure

Jeśli zablokujesz grupę zasobów utworzoną przez usługę Azure Backup Service, kopie zapasowe zaczną się niepowodzeniem. Usługa obsługuje maksymalnie 18 punktów przywracania. Za pomocą blokady **CanNotDelete** usługa tworzenia kopii zapasowych nie może wyczyścić punktów przywracania. Aby uzyskać więcej informacji, zobacz Często zadawane pytania — zapasowy maszyn [wirtualnych platformy Azure.](../../backup/backup-azure-vm-backup-faq.md)

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Szablon

Podczas korzystania z szablonu Menedżera zasobów do wdrażania blokady, należy użyć różnych wartości dla nazwy i typu w zależności od zakresu blokady.

Podczas stosowania blokady do **zasobu**należy użyć następujących formatów:

* nazwa -`{resourceName}/Microsoft.Authorization/{lockName}`
* typ -`{resourceProviderNamespace}/{resourceType}/providers/locks`

Podczas stosowania blokady do **grupy zasobów** lub **subskrypcji**należy użyć następujących formatów:

* nazwa -`{lockName}`
* typ -`Microsoft.Authorization/locks`

W poniższym przykładzie przedstawiono szablon, który tworzy plan usługi aplikacji, witrynę sieci Web i blokadę w witrynie sieci Web. Typ zasobu blokady jest typem zasobu do blokowania i **/providers/locks**. Nazwa blokady jest tworzona przez łączenie nazwy zasobu z **/Microsoft.Authorization/** i nazwą blokady.

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

Aby uzyskać przykład ustawienia blokady w grupie zasobów, zobacz [Tworzenie grupy zasobów i blokowanie jej](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Zablokuj wdrożone zasoby za pomocą programu Azure PowerShell przy użyciu polecenia [New-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Aby zablokować zasób, podaj nazwę zasobu, jego typ zasobu i nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Aby uzyskać informacje o blokadzie, użyj [get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Aby uzyskać wszystkie blokady w subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceLock
```

Aby uzyskać wszystkie blokady dla zasobu, użyj:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Aby usunąć blokadę, użyj:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zablokowanie wdrożonych zasobów za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [tworzenia blokady az.](/cli/azure/lock#az-lock-create)

Aby zablokować zasób, podaj nazwę zasobu, jego typ zasobu i nazwę grupy zasobów.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Aby uzyskać informacje o blokadzie, użyj [listy blokad az](/cli/azure/lock#az-lock-list). Aby uzyskać wszystkie blokady w subskrypcji, użyj:

```azurecli
az lock list
```

Aby uzyskać wszystkie blokady dla zasobu, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Aby usunąć blokadę, użyj:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>Interfejs API REST
Za pomocą interfejsu API REST można zablokować wdrożone zasoby za pomocą [interfejsu API REST dla blokad zarządzania](https://docs.microsoft.com/rest/api/resources/managementlocks). Interfejs API REST umożliwia tworzenie i usuwanie blokad oraz pobieranie informacji o istniejących blokadach.

Aby utworzyć blokadę, uruchom:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Zakres może być subskrypcją, grupą zasobów lub zasobem. Nazwa blokady jest cokolwiek chcesz wywołać blokadę. W przypadku wersji api użyj **2016-09-01**.

W żądaniu dołącz obiekt JSON, który określa właściwości blokady.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o logicznym organizowaniu zasobów, zobacz [Organizowanie zasobów za pomocą tagów](tag-resources.md)
* Ograniczenia i konwencje można stosować w całej subskrypcji z niestandardowych zasad. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

