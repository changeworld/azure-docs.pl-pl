---
title: RozmiarUelwiat UI
description: Zawiera opis elementu interfejsu użytkownika microsoft.compute.sizeSelector dla witryny Azure portal. Służy do wybierania rozmiaru maszyny wirtualnej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652049"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector Element interfejsu użytkownika

Formant do wybierania rozmiaru dla jednego lub więcej wystąpień maszyny wirtualnej.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

Użytkownik widzi selektor z wartościami domyślnymi z definicji elementu.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Po wybraniu formantu użytkownik zobaczy rozwinięty widok dostępnych rozmiarów.

![Microsoft.Compute.SizeSelector rozszerzony](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Standard_D1"
```

## <a name="remarks"></a>Uwagi

- `recommendedSizes`powinien mieć co najmniej jeden rozmiar. Pierwszy zalecany rozmiar jest używany jako wartość domyślna. Lista dostępnych rozmiarów nie jest sortowana według zalecanego stanu. Użytkownik może wybrać tę kolumnę, aby posortować według zalecanego stanu.
- Jeśli zalecany rozmiar nie jest dostępny w wybranej lokalizacji, rozmiar jest automatycznie pomijany. Zamiast tego używany jest następny zalecany rozmiar.
- `constraints.allowedSizes`i `constraints.excludedSizes` są opcjonalne, ale nie mogą być używane jednocześnie. Listę dostępnych rozmiarów można określić, wywołując [listę dostępnych rozmiarów maszyn wirtualnych dla subskrypcji](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Każdy rozmiar nie `constraints.allowedSizes` określony w jest ukryty, `constraints.excludedSizes` a każdy rozmiar, który nie jest określony w jest wyświetlany.
- `osPlatform`musi być określony i może być albo **Windows** lub **Linux**. Służy do określania kosztów sprzętowych maszyn wirtualnych.
- `imageReference`obrazów innych firm, ale jest przeznaczony dla obrazów innych firm. Służy do określania kosztów oprogramowania maszyn wirtualnych.
- `count`służy do ustawiania odpowiedniego mnożnika dla elementu. Obsługuje wartość statyczną, taką jak **2**lub wartość dynamiczną z innego elementu, na przykład. `[steps('step1').vmCount]` Wartość domyślna to **1**.
- `numAvailabilityZonesRequired` Może to być 1, 2 lub 3.
- Domyślnie `hideDiskTypeFilter` jest **false**. Filtr typu dysku umożliwia użytkownikowi wyświetlanie wszystkich typów dysków lub tylko dysków SSD.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
