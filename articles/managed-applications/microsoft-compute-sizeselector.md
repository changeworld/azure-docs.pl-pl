---
title: Element interfejsu użytkownika usługi Azure SizeSelector | Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. SizeSelector dla Azure Portal. Służy do wybierania rozmiaru maszyny wirtualnej.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 288ea7e887a170c8560b0126fa53c9132da35db6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332665"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft. COMPUTE. SizeSelector — element interfejsu użytkownika
Kontrolka służąca do wybierania rozmiaru dla co najmniej jednego wystąpienia maszyny wirtualnej.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Użytkownik widzi selektor z wartościami domyślnymi z definicji elementu.

![Microsoft. COMPUTE. SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Po wybraniu kontrolki użytkownik zobaczy rozwinięty widok dostępnych rozmiarów.

![Rozwinięto Microsoft. COMPUTE. SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- `recommendedSizes` powinien mieć co najmniej jeden rozmiar. Pierwszy zalecany rozmiar jest używany jako domyślny. Lista dostępnych rozmiarów nie jest posortowana według zalecanego stanu. Użytkownik może wybrać tę kolumnę, aby posortować według zalecanego stanu.
- Jeśli zalecany rozmiar nie jest dostępny w wybranej lokalizacji, rozmiar jest automatycznie pomijany. Zamiast tego jest używany następny zalecany rozmiar.
- `constraints.allowedSizes` i `constraints.excludedSizes` są opcjonalne, ale nie mogą być używane jednocześnie. Listę dostępnych rozmiarów można określić, wywołując [listę dostępnych rozmiarów maszyn wirtualnych w ramach subskrypcji](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Dowolny rozmiar nieokreślony w `constraints.allowedSizes` jest ukryty, a wszystkie rozmiary nieokreślone w `constraints.excludedSizes` są wyświetlane.
- należy określić `osPlatform` i może to być **system Windows** lub **Linux**. Służy do określania kosztów sprzętu maszyn wirtualnych.
- `imageReference` jest pomijany dla obrazów pierwszej strony, ale udostępnianych dla obrazów innych firm. Służy do określania kosztów oprogramowania maszyn wirtualnych.
- `count` służy do ustawiania odpowiedniego mnożnika dla elementu. Obsługuje ona wartość statyczną, taką jak **2**, lub wartość dynamiczną z innego elementu, np. `[steps('step1').vmCount]`. Wartość domyślna to **1**.
- @No__t-0 może mieć wartość 1, 2 lub 3.
- Domyślnie `hideDiskTypeFilter` ma **wartość false**. Filtr typ dysku umożliwia użytkownikowi wyświetlanie wszystkich typów dysku lub tylko dysków SSD.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"Standard_D1"
```

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
