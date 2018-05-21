---
title: Nie znaleziono błędy zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób naprawić błędy, gdy nie można odnaleźć zasobu.
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
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: f5da2a74b3a399c60c518f386ccf2e60a617aeda
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Rozwiązywanie błędów nie została znaleziona dla zasobów platformy Azure

W tym artykule opisano błędy, które mogą wystąpić, gdy nie można odnaleźć zasobu podczas wdrażania.

## <a name="symptom"></a>Objaw

Jeśli szablon zawiera nazwę zasobu, którego nie można rozwiązać, komunikat o błędzie podobny do:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Jeśli próba użycia [odwołania](resource-group-template-functions-resource.md#reference) lub [listKeys](resource-group-template-functions-resource.md#listkeys) funkcje z zasobów, których nie można rozpoznać, zostanie wyświetlony następujący błąd:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Przyczyna

Menedżer zasobów musi pobrać właściwości dla zasobu, ale nie można zidentyfikować zasobów w ramach subskrypcji.

## <a name="solution-1---set-dependencies"></a>Rozwiązanie 1 - zależności zestawu

Jeśli próbujesz wdrożyć brakującego zasobu w szablonie, sprawdź, czy konieczne jest dodanie zależności. Menedżer zasobów optymalizuje wdrożenia przez utworzenie zasobów równoległe, gdy jest to możliwe. Jeśli jeden zasób należy wdrożyć po inny zasób, należy użyć **dependsOn** elementu w szablonie. Na przykład w przypadku wdrażania aplikacji sieci web, musi istnieć plan usługi aplikacji. Jeśli nie określono, że aplikacja sieci web zależy od planu usług aplikacji, usługi Resource Manager tworzy oba zasoby w tym samym czasie. Otrzymasz komunikat o błędzie informujący, że usługi aplikacji nie można odnaleźć zasobu planu, ponieważ nie istnieje jeszcze podczas próby ustawienia właściwości w aplikacji sieci web. Ten błąd uniemożliwia ustawiając zależności w aplikacji sieci web.

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

Jednak aby uniknąć ustawienie zależności, które nie są wymagane. Gdy niepotrzebne zależności, można przedłużyć czas wdrażania, zapobiegając zasoby, które nie są wzajemnie zależne od wdrażany równolegle. Ponadto można utworzyć zależności cykliczne, które blokuje wdrożenie. [Odwołania](resource-group-template-functions-resource.md#reference) funkcja tworzy zależność niejawnych na zasobu dołączonego przez odwołanie, po wdrożeniu tego zasobu w tym samym szablonie. W związku z tym może mieć zależności więcej niż określa zależności **dependsOn** właściwości. [ResourceId](resource-group-template-functions-resource.md#resourceid) funkcji nie utworzyć niejawnego zależności lub Sprawdzanie, czy zasób istnieje.

W przypadku wystąpienia problemów zależności, musisz uzyskać wgląd w kolejności wdrażania zasobów. Aby wyświetlić kolejność operacji wdrażania:

1. Wybierz historii wdrożenia dla grupy zasobów.

   ![Wybierz Historia wdrażania](./media/resource-manager-not-found-errors/select-deployment.png)

2. Wybierz wdrożenie z historii, a następnie wybierz **zdarzenia**.

   ![Wybierz zdarzenia wdrożenia](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Sprawdź, czy sekwencję zdarzeń dla każdego zasobu. Należy zwrócić uwagę na stan każdej operacji. Na przykład na poniższej ilustracji przedstawiono trzy konta magazynu, które wdrożone równolegle. Zwróć uwagę, że trzy konta magazynu są uruchamiane w tym samym czasie.

   ![Wdrożenie równoległe](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   Na następnej ilustracji przedstawiono trzy konta magazynu, które nie są wdrażane równolegle. Pierwsze konto magazynu zależy od drugiego konta magazynu, a trzeci konta magazynu zależy od drugiego konta magazynu. Pierwsze konto magazynu jest uruchomiona, zaakceptowane i ukończone przed uruchomieniem następnego.

   ![kolejne wdrożenia](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>2 — rozwiązanie uzyskać zasobu w innej grupie zasobów

Gdy zasób istnieje w innej grupie zasobów niż podczas wdrażania, użyj [funkcja resourceId](resource-group-template-functions-resource.md#resourceid) można uzyskać w pełni kwalifikowana nazwa zasobu.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Rozwiązanie 3 - wyboru odwołanie funkcji

Wyszukaj wyrażenie, które zawiera [odwołania](resource-group-template-functions-resource.md#reference) funkcji. Wartości podane różnić w zależności od tego, czy zasób jest w szablonie, grupy zasobów i subskrypcji. Sprawdź, że udostępniasz wartości wymaganego parametru dla danego scenariusza. Jeśli zasób znajduje się w innej grupie zasobów, podaj identyfikator zasobu pełna. Na przykład aby odwołać się do konta magazynu w innej grupie zasobów, należy użyć:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```