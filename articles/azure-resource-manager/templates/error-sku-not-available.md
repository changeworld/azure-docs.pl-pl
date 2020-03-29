---
title: Nie dostępne błędy jednostki SKU
description: W tym artykule opisano, jak rozwiązać problem z niedostępnym błędem jednostki SKU podczas wdrażania zasobów w usłudze Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942727"
---
# <a name="resolve-errors-for-sku-not-available"></a>Usuwanie błędów związanych z niedostępną jednostką SKU

W tym artykule opisano sposób rozwiązywania **SkuNotAvailable** błąd. Jeśli nie możesz znaleźć odpowiedniej jednostki SKU w tym regionie/strefie lub alternatywnej strefie/regionu,która spełnia Twoje potrzeby biznesowe, prześlij [żądanie jednostki SKU](https://aka.ms/skurestriction) do pomocy technicznej platformy Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu (zazwyczaj maszyny wirtualnej) pojawia się następujący kod błędu i komunikat o błędzie:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Przyczyna

Ten błąd jest odbierany, gdy wybrana jednostka SKU zasobu (np. rozmiar maszyny Wirtualnej) nie jest dostępna dla wybranej lokalizacji.

Jeśli wdrażasz wystąpienie zestawu maszyn wirtualnych spot platformy Azure lub zestawu skalowania punktowego, w tej lokalizacji nie ma żadnej pojemności dla usługi Azure Spot. Aby uzyskać więcej informacji, zobacz [Spot komunikaty o błędach](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Rozwiązanie 1 - PowerShell

Aby określić, które jednostki SKU są dostępne w regionie/strefie, należy użyć polecenia [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Filtruj wyniki według lokalizacji. Dla tego polecenia musi być dostępna najnowsza wersja programu PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Wyniki obejmują listę jednostek SKU dla lokalizacji i wszelkie ograniczenia dla tej jednostki SKU. Należy zauważyć, że jednostka `NotAvailableForSubscription`SKU może być wymieniona jako .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Kilka dodatkowych próbek:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

Dołączanie "fc" na końcu zwraca więcej szczegółów.

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 — narzędzie interfejsu wiersza polecenia platformy Azure

Aby określić, które jednostki SKU `az vm list-skus` są dostępne w regionie, należy użyć polecenia. Parametr `--location` służy do filtrowania danych wyjściowych do używanej lokalizacji. Użyj `--size` parametru, aby wyszukać według nazwy rozmiaru częściowego.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Polecenie zwraca wyniki, takie jak:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 - Portal Platformy Azure

Aby określić, które jednostki SKU są dostępne w regionie, użyj [portalu](https://portal.azure.com). Zaloguj się do portalu i dodaj zasób za pośrednictwem interfejsu. Podczas ustawiania wartości są widoczne dostępne jednostki SKU dla tego zasobu. Nie trzeba ukończyć wdrożenia.

Na przykład rozpocznij proces tworzenia maszyny wirtualnej. Aby wyświetlić inny dostępny rozmiar, wybierz pozycję **Zmień rozmiar**.

![Tworzenie maszyny wirtualnej](./media/error-sku-not-available/create-vm.png)

Dostępne rozmiary można filtrować i przewijać.

![Dostępne jednostki SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Rozwiązanie 4 - REST

Aby określić, które jednostki SKU są dostępne w regionie, należy użyć operacji [Skus - Lista zasobów.](/rest/api/compute/resourceskus/list)

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

