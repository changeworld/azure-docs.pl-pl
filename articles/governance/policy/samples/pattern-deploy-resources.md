---
title: 'Wzorzec: wdrażanie zasobów przy użyciu definicji zasad'
description: Ten Azure Policy wzorzec zawiera przykład sposobu wdrażania zasobów przy użyciu definicji zasad.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172674"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Wzorzec Azure Policy: wdrażanie zasobów

Efekt [deployIfNotExists](../concepts/effects.md#deployifnotexists) umożliwia wdrożenie [szablonu Azure Resource Manager](../../../azure-resource-manager/templates/overview.md) podczas tworzenia lub aktualizowania zasobu, który nie jest zgodny. Takie podejście może być preferowane przy użyciu efektu [Odmów](../concepts/effects.md#deny) , ponieważ pozwala na dalsze tworzenie zasobów, ale gwarantuje, że zmiany są zgodne.

## <a name="sample-policy-definition"></a>Przykładowa definicja zasad

Ta definicja zasad używa operatora **pola** do oszacowania `type` utworzonego lub zaktualizowanego zasobu. Gdy ten zasób jest obiektem _Microsoft. Network/virtualNetworks_, zasady szuka obserwatora sieci w lokalizacji nowego lub zaktualizowanego zasobu. Jeśli nie zlokalizowano zgodnego obserwatora sieciowego, szablon Menedżer zasobów zostanie wdrożony w celu utworzenia brakującego zasobu.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Wyjaśnienie

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

Blok **właściwości. Klasa policyrule. then. Details** informuje Azure Policy, co należy szukać względem utworzonego lub zaktualizowanego zasobu w bloku **właściwości. Klasa policyrule. if** . W tym przykładzie obserwator sieciowy w grupie zasobów **networkWatcherRG** musi istnieć z **polem** `location` równe lokalizacji nowego lub zaktualizowanego zasobu. Użycie funkcji `field()` umożliwia **existenceCondition** dostęp do właściwości nowego lub zaktualizowanego zasobu, w zależności od właściwości `location`.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

Właściwość _Array_ roleDefinitionIds w bloku **Properties. Klasa policyrule. then. Details** informuje definicję zasad o tym, które prawa tożsamości zarządzanej muszą wdrożyć dołączony szablon Menedżer zasobów. Ta właściwość musi być ustawiona tak, aby obejmowała role, które mają uprawnienia wymagane przez wdrożenie szablonu, ale powinny używać koncepcji "zasada najniższych uprawnień" i mieć tylko wymagane operacje i nic więcej.

#### <a name="deployment-template"></a>Szablon wdrożenia

Część **wdrożenia** definicji zasad ma blok **Właściwości** , który definiuje trzy podstawowe składniki:

- **mode** — ta właściwość ustawia [tryb wdrażania](../../../azure-resource-manager/templates/deployment-modes.md) szablonu.

- **szablon** — ta właściwość zawiera sam szablon. W tym przykładzie parametr szablonu **lokalizacji** ustawia lokalizację nowego zasobu obserwatora sieci.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **Parametry** — ta właściwość definiuje parametry, które są dostarczane do **szablonu**. Nazwy parametrów muszą być zgodne z definicjami w **szablonie**. W tym przykładzie parametr ma nazwę **Lokalizacja** do dopasowania. Wartość **lokalizacji** używa ponownie funkcji `field()`, aby uzyskać wartość ocenianego zasobu, który jest siecią wirtualną w bloku **Klasa policyrule. if** .

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj inne [wzorce i wbudowane definicje](./index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).