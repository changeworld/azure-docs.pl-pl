---
title: Azure elementu interfejsu użytkownika SizeSelector | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Compute.SizeSelector interfejsu użytkownika do portalu Azure.
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
ms.openlocfilehash: 9009d29e281ace179ad1dd2021c7cf35e3dc611a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084811"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Element Microsoft.Compute.SizeSelector interfejsu użytkownika
Formant wyboru rozmiar dla co najmniej jedno wystąpienie maszyny wirtualnej.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika

Użytkownik widzi selektora z wartościami domyślnymi w definicji elementu.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Po wybraniu kontroli, użytkownik widzi rozwiniętego widoku dostępne rozmiary.

![Microsoft.Compute.SizeSelector rozwinięty](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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
- `recommendedSizes` powinny mieć rozmiar co najmniej jeden. Zalecany rozmiar pierwszy jest używany jako domyślny. Listę dostępnych rozmiarów nie są posortowane według stanu zalecane. Użytkownik może wybrać tej kolumny, aby posortować według stanu zalecane.
- Jeśli zalecany rozmiar nie jest dostępny w wybranej lokalizacji, rozmiar automatycznie zostanie pominięty. Zamiast tego dalej zalecany rozmiar jest używany.
- `constraints.allowedSizes` i `constraints.excludedSizes` są opcjonalne, ale nie mogą być używane jednocześnie. Listę dostępnych rozmiarów można ustalić wywołując [listy dostępne rozmiary maszyny wirtualnej w ramach subskrypcji](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Dowolnej wielkości, nie jest określona w `constraints.allowedSizes` jest ukryta i rozmiarze nie jest określona w `constraints.excludedSizes` jest wyświetlany.
- `osPlatform` należy określić i mogą być **Windows** lub **Linux**. Służy do określenia koszty sprzętu, maszyn wirtualnych.
- `imageReference` Pominięto obrazów firmy, ale podany dla obrazów innych firm. Służy do określania oprogramowania kosztów maszyn wirtualnych.
- `count` Służy do ustawiania odpowiednich mnożnik dla elementu. Obsługuje ona wartość statyczną, takie jak **2**, lub wartość dynamiczną z innego elementu, tak jak `[steps('step1').vmCount]`. Wartość domyślna to **1**.
- `numAvailabilityZonesRequired` Może być 1, 2 lub 3.
- Domyślnie `hideDiskTypeFilter` jest **false**. Filtr typu dysku umożliwia użytkownikowi wyświetlenie wszystkich typów dysków lub tylko dysków SSD.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"Standard_D1"
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
