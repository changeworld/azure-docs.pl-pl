---
title: Wymuszanie zabezpieczeń za pomocą zasad na Windows maszyn wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zastosować zasady do usługi Resource Manager Windows maszynę wirtualną platformy Azure
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 654c23474ebc96a6873f82f51f969da5c7d2d20c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60780806"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Stosowanie zasad do maszyn wirtualnych Windows za pomocą usługi Azure Resource Manager
Za pomocą zasad, organizacja może wymusić różnych konwencji i reguł w całym przedsiębiorstwie. Wymuszanie żądane zachowanie może pomóc w zmniejszeniu ryzyka, przyczyniając się do sukcesu organizacji. W tym artykule opisano sposób można użyć zasad usługi Azure Resource Manager do definiowania żądane zachowanie w przypadku maszyn wirtualnych w organizacji.

Wprowadzenie do zasad, zobacz [co to jest usługa Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Dozwolone maszyn wirtualnych
Aby upewnić się, że maszyny wirtualne na potrzeby Twojej organizacji są zgodne z aplikacji, można ograniczyć dozwolone systemów operacyjnych. W poniższym przykładzie zasad Zezwalaj na tylko systemu Windows Server 2012 R2 Datacenter maszyn wirtualnych ma zostać utworzony:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Użyj symbolu wieloznacznego, aby zmodyfikować zasady poprzedni, aby umożliwić dowolnego obrazu systemu Windows Server Datacenter:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Aby zmodyfikować poprzedni zasady umożliwiające dowolnego systemu Windows Server 2012 R2 Datacenter lub nowszej obrazu, użyj anyOf:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Aby uzyskać informacji o polach zasad, zobacz [aliasy zasad](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Dyski zarządzane

Aby korzystają z dysków zarządzanych, należy użyć następujących zasad:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Obrazy maszyn wirtualnych

Ze względów bezpieczeństwa może wymagać, że tylko obrazy zatwierdzone przez niestandardowe są wdrażane w środowisku. Można określić albo grupę zasobów, która zawiera obrazy zatwierdzone lub zatwierdzona obrazów.

Poniższy przykład wymaga obrazów z grupy zasobów zatwierdzone:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

W poniższym przykładzie określono identyfikatorów zatwierdzonym obrazie:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Rozszerzenia maszyn wirtualnych

Możesz chcieć zabraniają użycie niektórych rodzajów rozszerzeń. Na przykład rozszerzenie nie może być zgodne z niektórych obrazy niestandardowych maszyn wirtualnych. Poniższy przykład pokazuje, jak zablokować określone rozszerzenie. Aby określić, które rozszerzenia, aby zablokować używa wydawcy i typu.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Jeśli masz licencję w środowisku lokalnym, można zapisać opłata za licencję na maszynach wirtualnych. Jeśli nie masz licencję, powinna zabraniają opcji. Następujące zasady zabrania użycia korzyść użycia Azure hybrydowe (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
* Po zdefiniowaniu regułę zasad (jak pokazano w poprzednich przykładach), musisz utworzyć definicję zasad i przypisać je do zakresu. Zakres może być subskrypcji, grupy zasobów lub zasobu. Aby przypisać zasady, zobacz [użycia Azure portal, aby przypisać i zarządzaniu zasadami zasobów](../../governance/policy/assign-policy-portal.md), [Użyj programu PowerShell, aby przypisać zasady](../../governance/policy/assign-policy-powershell.md), lub [interfejsu wiersza polecenia użyj Azure, aby przypisać zasady](../../governance/policy/assign-policy-azurecli.md).
* Wprowadzenie do zasad zasobów, zobacz [co to jest usługa Azure Policy?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).