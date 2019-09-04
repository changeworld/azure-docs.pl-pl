---
title: Usuń grupę zasobów i zasoby — usługi Azure Resource Manager
description: Opisuje sposób usuwania grup zasobów i zasobów. Opisano w nim, jak Azure Resource Manager zamówienia usunięcia zasobów podczas usuwania grupy zasobów. Opisano w nim kodów odpowiedzi i jak Menedżera zasobów obsługuje je, aby określić, jeśli usunięcie powiodło się.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 30a394fd33ed5d928175fc27e003661c2b53de9a
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275090"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager grupy zasobów i usuwania zasobów

W tym artykule przedstawiono sposób usuwania grup zasobów i zasobów. Opisano w nim, jak Azure Resource Manager zamówienia usunięcia zasobów podczas usuwania grupy zasobów.

## <a name="how-order-of-deletion-is-determined"></a>Sposób określania kolejności usuwania

Podczas usuwania grupy zasobów usługi Resource Manager, określa kolejność, aby usunąć zasoby. Używa następującej kolejności:

1. Wszystkie zasoby podrzędne (zagnieżdżona) są usuwane.

2. Zasoby, które zarządzają inne zasoby są następnie usuwane. Zasób może mieć `managedBy` właściwość ustawioną na wskazywanie, czy inny zasób zarządza nim. Gdy ta właściwość jest ustawiona, zasobu, który zarządza inne zasoby zostaną usunięte przed inne zasoby.

3. Pozostałe zasoby są usuwane po poprzednich dwóch kategorii.

Po kolejność jest określana, Menedżer zasobów wystawia operację usuwania dla każdego zasobu. Czeka on na wszystkie zależności zakończyć działanie przed kontynuowaniem.

W przypadku operacji synchronicznych kody Oczekiwana odpowiedź oznaczająca Powodzenie to:

* 200
* 204
* 404

Dla operacji asynchronicznych Oczekiwana odpowiedź oznaczająca Powodzenie to 202. Menedżer zasobów śledzi nagłówek lokalizacji lub nagłówek operacji asynchronicznych platformy azure, można ustalić stanu operacji asynchronicznych usuwania.
  
### <a name="deletion-errors"></a>Błędy usuwania

Podczas operacji usuwania zwraca błąd, Menedżer zasobów ponawia próbę wywołania usunięcia. 5xx, 429 i kodów stanu 408 się zdarzyć ponownych prób. Domyślnie okres czasu ponawiania prób to 15 minut.

## <a name="after-deletion"></a>Po usunięciu

Menedżer zasobów wystawia wywołanie GET dla każdego zasobu, do którego próbowano usunąć. Odpowiedź to wywołanie GET powinien być 404. Gdy Menedżera zasobów odbiera odpowiedź 404, które uzna za usuwania zostały ukończone pomyślnie. Menedżer zasobów spowoduje usunięcie zasobu z pamięci podręcznej.

Jednak jeśli wywołanie GET dla zasobu zwraca 200 lub 201, Menedżer zasobów odtwarza zasobu.

Jeśli operacja GET zwróci błąd, Menedżer zasobów ponawia próbę GET dla następujący kod błędu:

* Mniej niż 100
* 408
* 429
* Większe niż 500

Dla innych kodów błędu usługi Resource Manager nie usunięcia zasobu.

## <a name="delete-resource-group"></a>Usuń grupę zasobów

Aby usunąć grupę zasobów, użyj jednej z poniższych metod.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. W [portalu](https://portal.azure.com)wybierz grupę zasobów, którą chcesz usunąć.

1. Wybierz pozycję **Usuń grupę zasobów**.

   ![Usuń grupę zasobów](./media/resource-group-delete/delete-group.png)

1. Aby potwierdzić usunięcie, wpisz nazwę grupy zasobów

---

## <a name="delete-resource"></a>Usuń zasób

Użyj jednej z poniższych metod, aby usunąć zasób.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. W [portalu](https://portal.azure.com)wybierz zasób, który chcesz usunąć.

1. Wybierz pozycję **Usuń**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla maszyny wirtualnej.

   ![Usuń zasób](./media/resource-group-delete/delete-resource.png)

1. Po wyświetleniu monitu potwierdź usunięcie.

---


## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć pojęcia usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).
* Usuwanie poleceń można znaleźć [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [wiersza polecenia platformy Azure](/cli/azure/group?view=azure-cli-latest#az-group-delete), i [interfejsu API REST](/rest/api/resources/resourcegroups/delete).
