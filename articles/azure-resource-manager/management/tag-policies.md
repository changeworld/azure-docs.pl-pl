---
title: Zasady oznaczania zasobów
description: W tym artykule opisano zasady platformy Azure, które można przypisać, aby zapewnić zgodność tagów.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147020"
---
# <a name="assign-policies-for-tag-compliance"></a>Przypisywanie zasad w celu zapewnienia zgodności z tagami

Zasady [platformy Azure](../../governance/policy/overview.md) są używane do wymuszania reguł i konwencji tagowania. Tworząc zasady, można uniknąć scenariusza zasobów wdrażanych w ramach subskrypcji, które nie mają oczekiwanych tagów dla twojej organizacji. Zamiast ręcznie stosować tagi lub wyszukiwać zasoby, które nie są zgodne, należy utworzyć zasadę, która automatycznie stosuje potrzebne tagi podczas wdrażania. Znaczniki można teraz również stosować do istniejących zasobów za pomocą nowego efektu [modyfikowania](../../governance/policy/concepts/effects.md#modify) i [zadania korygowania](../../governance/policy/how-to/remediate-resources.md). W poniższej sekcji przedstawiono przykładowe zasady tagów.

## <a name="policies"></a>Zasady

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o oznaczaniu zasobów, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów.](tag-resources.md)
* Nie wszystkie typy zasobów obsługują tagi. Aby ustalić, czy można zastosować znacznik do typu zasobu, zobacz [Obsługa tagów dla zasobów platformy Azure](tag-support.md).
