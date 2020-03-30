---
title: Zasoby podrzędne w szablonach
description: W tym artykule opisano sposób ustawiania nazwy i typu zasobów podrzędnych w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 6de2b476fe19a057a62e4a54963dd8fde0d11579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207897"
---
# <a name="set-name-and-type-for-child-resources"></a>Ustawianie nazwy i typu zasobów podrzędnych

Zasoby podrzędne to zasoby, które istnieją tylko w kontekście innego zasobu. Na przykład [rozszerzenie maszyny wirtualnej](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) nie może istnieć bez [maszyny wirtualnej](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). Zasób rozszerzenia jest podrzędnym modułem podrzędnym maszyny wirtualnej.

W szablonie Menedżera zasobów można określić zasób podrzędny w zasobie nadrzędnym lub poza zasobem nadrzędnym. W poniższym przykładzie przedstawiono zasób podrzędny zawarty we właściwości zasobów zasobu nadrzędnego.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

W następnym przykładzie pokazano zasób podrzędny poza zasobem nadrzędnym. Można użyć tej metody, jeśli zasób nadrzędny nie jest wdrażany w tym samym szablonie lub jeśli chcesz użyć [kopii](copy-resources.md) do utworzenia więcej niż jednego zasobu podrzędnego.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Wartości podane dla nazwy i typu zasobu różnią się w zależności od tego, czy zasób podrzędny jest zdefiniowany wewnątrz lub na zewnątrz zasobu nadrzędnego.

## <a name="within-parent-resource"></a>W zasobie nadrzędnym

Po zdefiniowaniu w nadrzędnym typie zasobu należy sformatować wartości typu i nazwy jako pojedynczego wyrazu bez ukośników.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Poniższy przykład przedstawia sieć wirtualną i podsieć. Należy zauważyć, że podsieć znajduje się w tablicy zasobów dla sieci wirtualnej. Nazwa jest ustawiona na **Podsieć1,** a typ jest ustawiony na **podsieci**. Zasób podrzędny jest oznaczony jako zależny od zasobu nadrzędnego, ponieważ zasób nadrzędny musi istnieć przed wdrożeniem zasobu podrzędnego.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Cały typ zasobu to nadal **Microsoft.Network/virtualNetworks/subnets**. Nie podasz **Microsoft.Network/virtualNetworks/ponieważ** zakłada się, że z nadrzędnego typu zasobu.

Nazwa zasobu podrzędnego jest ustawiona na **Podsieć1,** ale pełna nazwa zawiera nazwę nadrzędną. Nie należy podawać **sieci wirtualnej1,** ponieważ zakłada się, że z zasobu nadrzędnego.

## <a name="outside-parent-resource"></a>Zewnętrzny zasób nadrzędny

Po zdefiniowaniu poza zasobem nadrzędnym należy sformatować typ i ukośniki, aby uwzględnić typ nadrzędny i nazwę.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Poniższy przykład przedstawia sieć wirtualną i podsieć, które są zdefiniowane na poziomie głównym. Należy zauważyć, że podsieć nie jest uwzględniona w tablicy zasobów dla sieci wirtualnej. Nazwa jest ustawiona na **Sieć Wirtualna1/Podsieć1,** a typ jest ustawiony na **Microsoft.Network/virtualNetworks/subnets**. Zasób podrzędny jest oznaczony jako zależny od zasobu nadrzędnego, ponieważ zasób nadrzędny musi istnieć przed wdrożeniem zasobu podrzędnego.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów](template-syntax.md).

* Aby dowiedzieć się więcej o formacie nazwy zasobu podczas odwoływania się do zasobu, zobacz [funkcję odwołania](template-functions-resource.md#reference).
