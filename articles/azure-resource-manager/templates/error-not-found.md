---
title: Nie znaleziono błędów zasobu
description: W tym artykule opisano sposób rozwiązywania błędów, gdy nie można odnaleźć zasobu podczas wdrażania z szablonem usługi Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773258"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Rozwiązywanie nie znalezionych błędów zasobów platformy Azure

W tym artykule opisano błędy, które mogą być widoczne, gdy nie można odnaleźć zasobu podczas wdrażania.

## <a name="symptom"></a>Objaw

Gdy szablon zawiera nazwę zasobu, którego nie można rozpoznać, pojawia się błąd podobny do:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Jeśli używasz funkcji [odwołania](template-functions-resource.md#reference) lub [listKeys](template-functions-resource.md#listkeys) z zasobem, którego nie można rozwiązać, zostanie wyświetlony następujący błąd:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Przyczyna

Menedżer zasobów musi pobrać właściwości zasobu, ale nie może zidentyfikować zasobu w subskrypcji.

## <a name="solution-1---set-dependencies"></a>Rozwiązanie 1 - ustawianie zależności

Jeśli próbujesz wdrożyć brakujący zasób w szablonie, sprawdź, czy musisz dodać zależność. Menedżer zasobów optymalizuje wdrażanie, tworząc zasoby równolegle, jeśli to możliwe. Jeśli jeden zasób musi być wdrożony po innym zasobie, należy użyć **dependsOn** element w szablonie. Na przykład podczas wdrażania aplikacji sieci web plan usługi app service musi istnieć. Jeśli nie określono, że aplikacja sieci web zależy od planu usługi app service, Menedżer zasobów tworzy oba zasoby w tym samym czasie. Pojawi się błąd informujący, że nie można odnaleźć zasobu planu usługi app service, ponieważ nie istnieje on jeszcze podczas próby ustawienia właściwości w aplikacji sieci web. Można zapobiec ten błąd, ustawiając zależność w aplikacji sieci web.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Ale chcesz uniknąć ustawiania zależności, które nie są potrzebne. Jeśli masz niepotrzebne zależności, należy przedłużyć czas trwania wdrożenia, uniemożliwiając zasoby, które nie są zależne od siebie nawzajem z wdrażania równolegle. Ponadto można utworzyć cykliczne zależności, które blokują wdrożenie. Funkcja [odwołania](template-functions-resource.md#reference) i [funkcja listy*](template-functions-resource.md#list) tworzy niejawną zależność od zasobu, do którego istnieje odwołanie, gdy ten zasób jest wdrażany w tym samym szablonie i odwołuje się do niego nazwą (nie identyfikatorem zasobu). W związku z tym może mieć więcej zależności niż zależności określone w **dependsOn** właściwości. Funkcja [resourceId](template-functions-resource.md#resourceid) nie tworzy niejawnej zależności ani nie sprawdza, czy zasób istnieje. Funkcja [odwołania](template-functions-resource.md#reference) i [funkcja listy*](template-functions-resource.md#list) nie tworzą niejawnej zależności, gdy zasób jest określany przez jego identyfikator zasobu. Aby utworzyć niejawną zależność, przekaż nazwę zasobu, który jest wdrażany w tym samym szablonie.

Gdy widzisz problemy z zależnościami, należy uzyskać wgląd w kolejność wdrażania zasobów. Aby wyświetlić kolejność operacji wdrażania:

1. Wybierz historię wdrażania dla grupy zasobów.

   ![wybieranie historii wdrażania](./media/error-not-found/select-deployment.png)

2. Wybierz wdrożenie z historii i wybierz pozycję **Zdarzenia**.

   ![wybieranie zdarzeń wdrażania](./media/error-not-found/select-deployment-events.png)

3. Sprawdź sekwencję zdarzeń dla każdego zasobu. Należy zwrócić uwagę na stan każdej operacji. Na przykład na poniższej ilustracji przedstawiono trzy konta magazynu, które wdrożone równolegle. Należy zauważyć, że trzy konta magazynu są uruchamiane w tym samym czasie.

   ![wdrożenie równoległe](./media/error-not-found/deployment-events-parallel.png)

   Następny obraz przedstawia trzy konta magazynu, które nie są wdrażane równolegle. Drugie konto magazynu zależy od pierwszego konta magazynu, a trzecie konto magazynu zależy od drugiego konta magazynu. Pierwsze konto magazynu jest uruchamiane, akceptowane i ukończone przed rozpoczęciem następnego.

   ![wdrażanie sekwencyjne](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Rozwiązanie 2 — pobierz zasób z innej grupy zasobów

Jeśli zasób istnieje w innej grupie zasobów niż ta wdrażana, użyj [funkcji resourceId,](template-functions-resource.md#resourceid) aby uzyskać w pełni kwalifikowaną nazwę zasobu.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Rozwiązanie 3 - sprawdź funkcję referencyjną

Poszukaj wyrażenia, które zawiera funkcję [odwołania.](template-functions-resource.md#reference) Podane wartości różnią się w zależności od tego, czy zasób znajduje się w tym samym szablonie, grupie zasobów i subskrypcji. Sprawdź, czy są podane wartości parametrów wymaganych dla scenariusza. Jeśli zasób należy do innej grupy zasobów, podaj pełny identyfikator zasobu. Na przykład, aby odwołać się do konta magazynu w innej grupie zasobów, należy użyć:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Rozwiązanie 4 - uzyskaj tożsamość zarządzaną z zasobu

Jeśli wdrażasz zasób, który niejawnie tworzy [tożsamość zarządzaną,](../../active-directory/managed-identities-azure-resources/overview.md)należy poczekać, aż ten zasób zostanie wdrożony przed pobraniem wartości dla tożsamości zarządzanej. Jeśli przekażesz nazwę tożsamości zarządzanej do funkcji [odwołania,](template-functions-resource.md#reference) Menedżer zasobów próbuje rozpoznać odwołanie przed wdrożeniem zasobu i tożsamości. Zamiast tego należy przekazać nazwę zasobu, do których jest stosowana tożsamość. Takie podejście zapewnia zasób i tożsamość zarządzana są wdrażane, zanim Menedżer zasobów rozpoznaje funkcję odwołania.

W funkcji odwołania `Full` użyj, aby uzyskać wszystkie właściwości, w tym tożsamości zarządzanej.

Na przykład, aby uzyskać identyfikator dzierżawy dla tożsamości zarządzanej, która jest stosowana do zestawu skalowania maszyny wirtualnej, należy użyć:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```