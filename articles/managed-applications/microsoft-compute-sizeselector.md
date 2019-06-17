---
title: Azure elementu interfejsu użytkownika SizeSelector | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Compute.SizeSelector interfejsu użytkownika dla witryny Azure portal.
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
ms.openlocfilehash: e5be5635964ebeedc7be4d1d1f5403e4d281b55c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64722350"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI element
Kontrolka służąca do wybierania rozmiar co najmniej jedno wystąpienie maszyny wirtualnej.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika

Użytkownik zobaczy selektora z wartościami domyślnymi w definicji elementu.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Po wybraniu kontrolki, użytkownik zobaczy rozwinięty widok dostępnych rozmiarów.

![Microsoft.Compute.SizeSelector expanded](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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
- `recommendedSizes` powinny mieć rozmiar co najmniej jeden. Pierwszy zalecany rozmiar jest używany jako domyślny. Listę dostępnych rozmiarów nie są sortowane według stanu zalecane. Użytkownik może wybrać tej kolumny, aby posortować według stanu zalecane.
- Jeśli zalecany rozmiar nie jest dostępna w wybranej lokalizacji, rozmiar zostanie automatycznie pominięty. Zamiast tego należy dalej zalecany rozmiar jest używany.
- `constraints.allowedSizes` i `constraints.excludedSizes` są opcjonalne, ale nie mogą być używane jednocześnie. Można określić listę dostępnych rozmiarów, wywołując [listy dostępne rozmiary maszyny wirtualnej dla subskrypcji](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Dowolnej wielkości, które nie są określone w `constraints.allowedSizes` jest ukryta i dowolnego rozmiaru, nie jest określona w `constraints.excludedSizes` jest wyświetlany.
- `osPlatform` należy określić, i nie może być **Windows** lub **Linux**. Służy do określenia koszty sprzętu, maszyny wirtualne.
- `imageReference` Pominięto obrazów firmy Microsoft, ale dostarczone do celów obrazów innych firm. Służy do określenia koszty oprogramowania maszyn wirtualnych.
- `count` Służy do ustawiania odpowiednie mnożnik dla tego elementu. Obsługuje ona wartość statyczną, takie jak **2**, lub wartość dynamiczna z innego elementu, tak jak `[steps('step1').vmCount]`. Wartość domyślna to **1**.
- `numAvailabilityZonesRequired` Może być 1, 2 lub 3.
- Domyślnie `hideDiskTypeFilter` jest **false**. Filtr typu dysku umożliwia użytkownikowi wyświetlanie wszystkich typów dysków lub tylko dyski SSD.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"Standard_D1"
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
