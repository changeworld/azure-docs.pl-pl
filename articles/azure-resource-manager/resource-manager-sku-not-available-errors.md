---
title: Błędy niedostępne dla jednostki SKU
description: Opisuje sposób rozwiązywania problemów z niedostępnym jednostką SKU podczas wdrażania zasobów przy użyciu Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.openlocfilehash: 0b3696d3207a88d87b11e65f4697473963f960d5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769153"
---
# <a name="resolve-errors-for-sku-not-available"></a>Rozwiązywanie błędów dla jednostki SKU jest niedostępne

W tym artykule opisano sposób rozwiązywania błędu **SkuNotAvailable** . Jeśli nie możesz znaleźć odpowiedniej jednostki SKU w tym regionie lub alternatywnego regionu, który spełnia Twoje potrzeby biznesowe, Prześlij [żądanie jednostki SKU](https://aka.ms/skurestriction) do pomocy technicznej platformy Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu (zazwyczaj maszyny wirtualnej) pojawia się następujący kod błędu i komunikat o błędzie:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wybrana jednostka SKU zasobu (na przykład rozmiar maszyny wirtualnej) nie jest dostępna dla wybranej lokalizacji.

Jeśli wdrażasz maszynę wirtualną platformy Azure lub wystąpienie zestawu skalowania punktowego, w tej lokalizacji nie ma żadnej pojemności dla platformy Azure. Aby uzyskać więcej informacji, zobacz [dodatkowe komunikaty o błędach](../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Rozwiązanie 1 — PowerShell

Aby określić, które jednostki SKU są dostępne w regionie, użyj polecenia [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) . Filtruj wyniki według lokalizacji. Dla tego polecenia trzeba mieć najnowszą wersję programu PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Wyniki obejmują listę jednostek SKU dla lokalizacji i wszelkie ograniczenia dotyczące tej jednostki SKU. Należy zauważyć, że jednostka SKU może być wyświetlona jako `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 — interfejs wiersza polecenia platformy Azure

Aby określić, które jednostki SKU są dostępne w regionie, użyj polecenia `az vm list-skus`. Użyj parametru `--location`, aby odfiltrować dane wyjściowe do lokalizacji, której używasz. Użyj parametru `--size`, aby wyszukać według częściowej nazwy rozmiaru.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Polecenie zwraca wyniki, takie jak:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 — Azure Portal

Aby określić, które jednostki SKU są dostępne w regionie, użyj [portalu](https://portal.azure.com). Zaloguj się do portalu i Dodaj zasób za pomocą interfejsu. Podczas ustawiania wartości są wyświetlane dostępne jednostki SKU dla tego zasobu. Wdrożenie nie jest konieczne.

Na przykład Rozpocznij proces tworzenia maszyny wirtualnej. Aby wyświetlić inny dostępny rozmiar, wybierz pozycję **Zmień rozmiar**.

![Tworzenie maszyny wirtualnej](./media/resource-manager-sku-not-available-errors/create-vm.png)

Można filtrować i przewijać dostępne rozmiary.

![Dostępne jednostki SKU](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Rozwiązanie 4 — REST

Aby określić, które jednostki SKU są dostępne w danym regionie, użyj operacji [SKU zasobów — lista](/rest/api/compute/resourceskus/list) .

Zwraca dostępne jednostki SKU i regiony w następującym formacie:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

