---
title: Błędy nie znaleziono zasobów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób naprawić błędy, gdy nie można odnaleźć zasobu.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 0dfbfa73009b5fa47dd7bb0804b612d0c2ad0a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61062279"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Rozwiąż błędy nie znaleziono zasobów platformy Azure

W tym artykule opisano błędów, może zostać wyświetlony, gdy podczas wdrażania, w którym nie można odnaleźć zasobu.

## <a name="symptom"></a>Objaw

Szablon zawiera nazwę zasobu, którego nie można rozwiązać, pojawi się błąd podobny do:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Jeśli używasz [odwołania](resource-group-template-functions-resource.md#reference) lub [listKeys](resource-group-template-functions-resource.md#listkeys) funkcje z zasobem, którego nie można rozpoznać, zostanie wyświetlony następujący błąd:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Przyczyna

Menedżer zasobów musi pobrać właściwości zasobu, ale nie może zidentyfikować zasobów w ramach subskrypcji.

## <a name="solution-1---set-dependencies"></a>Rozwiązanie 1 - zależności zestawu

Jeśli próbujesz wdrożyć brakuje zasobu w szablonie, sprawdź, czy należy dodać zależność. Menedżer zasobów optymalizuje wdrożenia, tworzenia zasobów w sposób równoległy, jeśli jest to możliwe. Jeśli jeden zasób należy wdrożyć po inny zasób, należy użyć **dependsOn** elementu w szablonie. Na przykład w przypadku wdrażania aplikacji sieci web, musi istnieć plan usługi App Service. Jeśli nie określono, że aplikacja sieci web zależy od planu usługi App Service, Menedżer zasobów tworzy oba zasoby w tym samym czasie. Otrzymasz komunikat o błędzie informujący, że usługi App Service, nie można odnaleźć zasobu planu, ponieważ nie istnieje jeszcze podczas próby ustawienia właściwości w aplikacji sieci web. Możesz uniknąć tego błędu, ustawiając zależność w aplikacji sieci web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Jednak aby uniknąć zależności, które nie są wymagane ustawienia. W przypadku zależności niepotrzebne można przedłużyć czas wdrażania poprzez uniemożliwienie zasoby, które są wzajemnie zależne od wdrożenia równoległego. Ponadto możesz utworzyć zależności cykliczne, które blokują wdrożenia. [Odwołania](resource-group-template-functions-resource.md#reference) funkcji i [listy *](resource-group-template-functions-resource.md#list) funkcji tworzy zależność niejawne na przywoływany zasób, gdy ten zasób został wdrożony w tym samym szablonie odwołuje się do jego nazwę (nie identyfikator zasobu ). W związku z tym, może mieć więcej zależności, niż określa zależności **dependsOn** właściwości. [ResourceId](resource-group-template-functions-resource.md#resourceid) funkcji nie tworzenie niejawne zależności lub zweryfikować, że zasób istnieje. [Odwołania](resource-group-template-functions-resource.md#reference) funkcji i [listy *](resource-group-template-functions-resource.md#list) funkcji nie twórz niejawne zależności, gdy zasób jest określany przez jego identyfikator zasobu. Aby utworzyć zależność niejawne, należy przekazać nazwę zasobu, który jest wdrożony w tym samym szablonie.

Gdy pojawi się problemy z zależnościami, musisz uzyskać wgląd w kolejności wdrażania zasobów. Aby wyświetlić kolejność operacji wdrażania:

1. Wybierz historię wdrożenia grupy zasobów.

   ![Wybierz pozycję Historia wdrażania](./media/resource-manager-not-found-errors/select-deployment.png)

2. Wybierz wdrożenie z historii, a następnie wybierz pozycję **zdarzenia**.

   ![Wybierz zdarzenia wdrożenia](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Sprawdź kolejność zdarzeń dla każdego zasobu. Należy zwrócić uwagę na stan każdej operacji. Na przykład na poniższej ilustracji przedstawiono trzy konta magazynu, które wdrożone równolegle. Należy zauważyć, że trzy konta magazynu są uruchamiane w tym samym czasie.

   ![wdrożenie równoległe](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   Na następnej ilustracji przedstawiono trzy konta magazynu, które nie są wdrażane w sposób równoległy. Drugie konto magazynu jest zależny od pierwszego konta magazynu, a trzeci konta magazynu zależy od drugiego konta magazynu. Pierwsze konto magazynu jest pracę, zaakceptowane i ukończone przed rozpoczęciem następnego.

   ![kolejne wdrożenia](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Rozwiązanie 2 — pobieranie zasobów z innej grupy zasobów

Jeśli zasób istnieje w grupie zasobów innej niż wdrożenie, użyj [funkcja resourceId](resource-group-template-functions-resource.md#resourceid) można pobrać w pełni kwalifikowana nazwa zasobu.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Rozwiązanie 3 - wyboru odwołanie funkcji

Wyszukaj wyrażenie zawierające [odwołania](resource-group-template-functions-resource.md#reference) funkcji. Wartości, których udzielasz różnią się zależnie od tego, czy zasób jest w szablonie, grupy zasobów i subskrypcji. Sprawdź, czy udostępniasz wartości wymaganego parametru dla danego scenariusza. Jeśli zasób jest w innej grupie zasobów, należy podać identyfikator zasobu pełne. Na przykład aby odwoływać się do konta magazynu w innej grupie zasobów, należy użyć:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```