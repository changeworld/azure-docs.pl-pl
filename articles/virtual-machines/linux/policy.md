---
title: Wymuszanie zabezpieczeń przy użyciu zasad na maszynach wirtualnych z systemem Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zastosować zasady do Menedżera zasobów systemu Linux maszyny wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 0c7b1488921e0708a71caade4599cef367b4b3eb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667232"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Stosowanie zasad do maszyn wirtualnych systemu Linux przy użyciu usługi Azure Resource Manager
Za pomocą zasad, organizacja może wymusić różnych konwencji i reguł w całym przedsiębiorstwie. Wymuszanie żądane zachowanie może pomóc w zmniejszeniu ryzyka, przyczyniając się do sukcesu organizacji. W tym artykule opisano sposób można użyć zasad usługi Azure Resource Manager do definiowania żądane zachowanie w przypadku maszyn wirtualnych w organizacji.

Wprowadzenie do zasad, zobacz [co to jest usługa Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Dozwolone maszyn wirtualnych
Aby upewnić się, że maszyny wirtualne na potrzeby Twojej organizacji są zgodne z aplikacji, można ograniczyć dozwolone systemów operacyjnych. W poniższym przykładzie zasad możesz zezwolić tylko Ubuntu 14.04.2-LTS maszyn wirtualnych ma zostać utworzony.

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
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
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

Aby zmodyfikować poprzedni zasady umożliwiające dowolny obraz Ubuntu LTS, należy użyć symbolu wieloznacznego: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
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


## <a name="next-steps"></a>Następne kroki
* Po zdefiniowaniu regułę zasad (jak pokazano w poprzednich przykładach), musisz utworzyć definicję zasad i przypisać je do zakresu. Zakres może być subskrypcji, grupy zasobów lub zasobu. Aby przypisać zasady, zobacz [użycia Azure portal, aby przypisać i zarządzaniu zasadami zasobów](../../governance/policy/assign-policy-portal.md), [Użyj programu PowerShell, aby przypisać zasady](../../governance/policy/assign-policy-powershell.md), lub [interfejsu wiersza polecenia użyj Azure, aby przypisać zasady](../../governance/policy/assign-policy-azurecli.md).
* Wprowadzenie do zasad zasobów, zobacz [co to jest usługa Azure Policy?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
