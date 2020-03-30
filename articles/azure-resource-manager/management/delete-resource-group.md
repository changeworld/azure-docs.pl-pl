---
title: Usuwanie grupy zasobów i zasobów
description: W tym artykule opisano sposób usuwania grup zasobów i zasobów. Opisano w nim, jak usługa Azure Resource Manager zamawia usuwanie zasobów podczas usuwania grupy zasobów. Opisano kody odpowiedzi i jak Menedżer zasobów obsługuje je, aby ustalić, czy usunięcie zakończyło się pomyślnie.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274023"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Usuwanie grupy zasobów usługi Azure Resource Manager i usuwania zasobów

W tym artykule pokazano, jak usunąć grupy zasobów i zasoby. Opisano w nim, jak usługa Azure Resource Manager zamawia usuwanie zasobów podczas usuwania grupy zasobów.

## <a name="how-order-of-deletion-is-determined"></a>Jak określana jest kolejność usuwania

Po usunięciu grupy zasobów Menedżer zasobów określa kolejność usuwania zasobów. Używa następującej kolejności:

1. Wszystkie podrzędne (zagnieżdżone) zasoby są usuwane.

2. Zasoby, które zarządzają innymi zasobami, są usuwane w następnej kolejności. Zasób może `managedBy` mieć ustawioną właściwość wskazującą, że zarządza nim inny zasób. Po ustawieniu tej właściwości zasób, który zarządza innym zasobem, jest usuwany przed innymi zasobami.

3. Pozostałe zasoby są usuwane po poprzednich dwóch kategoriach.

Po określeniu zamówienia Menedżer zasobów wystawia operację DELETE dla każdego zasobu. Czeka na wszelkie zależności, aby zakończyć przed kontynuowaniem.

W przypadku operacji synchronicznych oczekiwane pomyślne kody odpowiedzi to:

* 200
* 204
* 404

W przypadku operacji asynchronicznych oczekiwana pomyślna odpowiedź to 202. Menedżer zasobów śledzi nagłówek lokalizacji lub nagłówek operacji azure-async, aby określić stan operacji usuwania asynchronii.
  
### <a name="deletion-errors"></a>Błędy usuwania

Gdy operacja usuwania zwraca błąd, Menedżer zasobów ponawia ponawia wywołanie DELETE. Ponownych prób zdarzają się dla 5xx, 429 i 408 kodów stanu. Domyślnie okres ponawiania próby wynosi 15 minut.

## <a name="after-deletion"></a>Po usunięciu

Menedżer zasobów wystawia wywołanie GET dla każdego zasobu, który próbował usunąć. Odpowiedź tego wywołania GET ma być 404. Gdy Menedżer zasobów pobiera 404, uważa, że usunięcie zostało pomyślnie zakończone. Menedżer zasobów usuwa zasób z jego pamięci podręcznej.

Jeśli jednak wywołanie GET w zasobie zwraca wartość 200 lub 201, Menedżer zasobów odtwarza zasób.

Jeśli operacja GET zwraca błąd, Menedżer zasobów ponawia ponowny ponawia proces GET dla następującego kodu błędu:

* Mniej niż 100
* 408
* 429
* Większa niż 500

W przypadku innych kodów błędów Menedżer zasobów nie może wykreślić zasobu.

## <a name="delete-resource-group"></a>Usuwanie grupy zasobów

Użyj jednej z następujących metod, aby usunąć grupę zasobów.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [portalu](https://portal.azure.com)wybierz grupę zasobów, którą chcesz usunąć.

1. Wybierz pozycję **Usuń grupę zasobów**.

   ![Usuwanie grupy zasobów](./media/delete-resource-group/delete-group.png)

1. Aby potwierdzić usunięcie, wpisz nazwę grupy zasobów

---

## <a name="delete-resource"></a>Usuwanie zasobu

Użyj jednej z następujących metod, aby usunąć zasób.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [portalu](https://portal.azure.com)wybierz zasób, który chcesz usunąć.

1. Wybierz pozycję **Usuń**. Poniższy zrzut ekranu przedstawia opcje zarządzania maszyną wirtualną.

   ![Usuwanie zasobu](./media/delete-resource-group/delete-resource.png)

1. Po wyświetleniu monitu potwierdź usunięcie.

---


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pojęciami usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
* Aby uzyskać polecenia usuwania, zobacz [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)i [REST API](/rest/api/resources/resourcegroups/delete).
