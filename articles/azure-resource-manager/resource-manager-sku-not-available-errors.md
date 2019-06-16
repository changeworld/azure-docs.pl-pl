---
title: Jednostka SKU systemu Azure nie jest dostępna błędy | Dokumentacja firmy Microsoft
description: Opisuje, jak rozwiązywać problemy z jednostki SKU nie jest dostępna wystąpił błąd podczas wdrażania.
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
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: 1dd0532452c3558e53f0236998953d2055ed328c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390770"
---
# <a name="resolve-errors-for-sku-not-available"></a>Rozwiązywanie błędów dla jednostki SKU nie jest dostępna

W tym artykule opisano sposób rozwiązywania **komunikatu SkuNotAvailable** błędu. Jeśli nie uda się znaleźć odpowiedniej jednostki SKU w danym regionie lub inny region, spełniające Twojej firmy wymaga Prześlij [żądania jednostki SKU](https://aka.ms/skurestriction) do pomocy technicznej systemu Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobów (zazwyczaj maszyny wirtualnej), pojawi się następujący kod błędu oraz komunikat o błędzie:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Przyczyna

Ten błąd jest wyświetlany, gdy zasób jednostki SKU wybrano (np. rozmiar maszyny Wirtualnej) nie jest dostępne dla lokalizacji, które wybrałeś.

## <a name="solution-1---powershell"></a>Rozwiązanie 1 — PowerShell

Aby określić, które jednostek SKU są dostępne w regionie, użyj [Get AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) polecenia. Filtruj wyniki według lokalizacji. Konieczne jest posiadanie najnowszej wersji programu PowerShell dla tego polecenia.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Wyniki obejmują listę jednostek SKU dla lokalizacji i zastosowanie jakiekolwiek ograniczenia dotyczące tej jednostki SKU. Należy zauważyć, że jednostka SKU mogą być wyświetlane jako `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 — interfejs wiersza polecenia Azure

Aby określić, które jednostek SKU są dostępne w regionie, użyj `az vm list-skus` polecenia. Użyj `--location` parametru, aby filtrować dane wyjściowe do lokalizacji, którego używasz. Użyj `--size` parametru, aby przeprowadzić wyszukiwanie według nazwy częściowej rozmiar.

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


## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 - w witrynie Azure portal

Aby określić, które jednostek SKU są dostępne w regionie, użyj [portal](https://portal.azure.com). Zaloguj się do portalu, a następnie dodaj zasób za pomocą interfejsu. Po ustawieniu wartości, zobaczysz dostępne jednostki SKU dla tego zasobu. Nie potrzebujesz zakończyć wdrożenie.

Na przykład uruchomić proces tworzenia maszyny wirtualnej. Aby wyświetlić inne dostępny rozmiar, wybierz **Zmień rozmiar**.

![Tworzenie maszyny wirtualnej](./media/resource-manager-sku-not-available-errors/create-vm.png)

Można filtrować i przewiń do dostępnych rozmiarów.

![Dostępne jednostki SKU](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Rozwiązanie 4 — REST

Aby określić, które jednostek SKU są dostępne w regionie, użyj [jednostek SKU zasobów — lista](/rest/api/compute/resourceskus/list) operacji.

Zwraca dostępne jednostki SKU i regionów w następującym formacie:

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

