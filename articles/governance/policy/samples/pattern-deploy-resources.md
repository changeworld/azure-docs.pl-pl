---
title: 'Wzorzec: Wdrażanie zasobów z definicją zasad'
description: Ten wzorzec zasad platformy Azure zawiera przykład sposobu wdrażania zasobów z definicją zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172674"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Wzorzec zasad platformy Azure: wdrażanie zasobów

Efekt [deployIfNotExists](../concepts/effects.md#deployifnotexists) umożliwia wdrożenie [szablonu usługi Azure Resource Manager](../../../azure-resource-manager/templates/overview.md) podczas tworzenia lub aktualizowania zasobu, który nie jest zgodny. Takie podejście może być preferowane przy użyciu [efektu odmowy,](../concepts/effects.md#deny) ponieważ umożliwia tworzenie zasobów, ale zapewnia, że zmiany są wprowadzane w celu ich zgodności.

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad **field** używa operatora `type` pola do oceny zasobu utworzonego lub zaktualizowanego. Gdy ten zasób jest _Microsoft.Network/virtualNetworks_, zasady wyszukuje obserwatora sieci w lokalizacji nowego lub zaktualizowanego zasobu. Jeśli pasujący obserwator sieci nie znajduje się, szablon Menedżera zasobów jest wdrażany w celu utworzenia brakującego zasobu.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Wyjaśnienie

#### <a name="existencecondition"></a>istnienieWarzycie

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

Ten **properties.policyRule.then.details** blok informuje Azure Zasady co szukać pod kątem pokrewny wobec ten wierzyciel albo zaktualizować narada w ten **properties.policyRule.if** kloc. W tym przykładzie obserwator sieci w sieci grupy **zasobówWatcherRG** musi istnieć z **polem** `location` równym lokalizacji nowego lub zaktualizowanego zasobu. Za `field()` pomocą funkcji umożliwia **istnienieWarszyk dostępu** do właściwości na nowy `location` lub zaktualizowany zasób, w szczególności właściwości.

#### <a name="roledefinitionids"></a>.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

Właściwość _tablicy_ **roleDefinitionIds** w bloku **properties.policyRule.then.details** informuje o definicji zasad, które prawa musi mieć tożsamość zarządzana do wdrożenia dołączonego szablonu Menedżera zasobów. Ta właściwość musi być ustawiona na role, które mają uprawnienia wymagane przez wdrożenie szablonu, ale należy użyć pojęcia "zasada najmniejszych uprawnień" i mają tylko potrzebne operacje i nic więcej.

#### <a name="deployment-template"></a>Szablon wdrożenia

Część **wdrożenia** definicji zasad ma blok **właściwości,** który definiuje trzy podstawowe składniki:

- **mode** - Ta właściwość ustawia [tryb wdrażania](../../../azure-resource-manager/templates/deployment-modes.md) szablonu.

- **szablon** — ta właściwość zawiera sam szablon. W tym przykładzie parametr szablonu **lokalizacji** ustawia lokalizację nowego zasobu obserwatora sieci.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parametry** — ta właściwość definiuje parametry, które są dostarczane do **szablonu**. Nazwy parametrów muszą być zgodne z tym, co jest zdefiniowane w **szablonie**. W tym przykładzie parametr jest nazwany **lokalizacja** do dopasowania. Wartość **lokalizacji** używa `field()` funkcji ponownie, aby uzyskać wartość ocenianego zasobu, który jest siecią wirtualną w **policyRule.if** bloku.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).