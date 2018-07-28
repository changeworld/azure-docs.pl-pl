---
title: Typy limitów przydziału w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Sprawdź typy inny limit przydziału dostępne dla usług i zasobów w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/27/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: 172c32c1f3796ec95336543d7d0ea149e63cfb22
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331144"
---
# <a name="quota-types-in-azure-stack"></a>Typy limitów przydziału w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

[Przydziały](azure-stack-plan-offer-quota-overview.md#plans) definiowania ograniczeń zasobów, które subskrypcji użytkownika można aprowizować lub zużywają. Na przykład limit przydziału może zezwolić użytkownikowi na utworzenie maksymalnie pięć maszyn wirtualnych. Każdy zasób może mieć własne typy przydziałów.

## <a name="compute-quota-types"></a>Typy limitów przydziału obliczeniowych

| **Typ** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna liczba maszyn wirtualnych | 20 | Maksymalna liczba maszyn wirtualnych, które można utworzyć subskrypcji, w tej lokalizacji. |
| Maksymalna liczba rdzeni maszyn wirtualnych | 50 | Maksymalna liczba rdzeni, które można utworzyć subskrypcji, w tym miejscu (na przykład maszyna wirtualna A3 ma cztery rdzenie). |
| Maksymalna liczba zestawów dostępności | 10 | Maksymalna liczba zestawów dostępności, które mogą być tworzone w tej lokalizacji. |
| Maksymalna liczba maszyn wirtualnych zestawów skalowania | 20 | Maksymalna liczba zestawów skalowania maszyn wirtualnych, które mogą być tworzone w tej lokalizacji. |

## <a name="storage-quota-types"></a>Typy limitów przydziału magazynu

| **Element** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna pojemność (GB) |500 |Pojemność pamięci masowej, które mogą być używane przez subskrypcji w tej lokalizacji. |
| Łączna liczba kont magazynu |20 |Maksymalna liczba kont magazynu, które można utworzyć subskrypcji, w tej lokalizacji. |

> [!NOTE]  
> Może upłynąć do dwóch godzin, zanim przydział magazynowania jest wymuszany.

## <a name="network-quota-types"></a>Typy przydziałów sieci

| **Element** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna liczba publicznych adresów IP |50 |Maksymalna liczba publicznych adresów IP, który subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba sieci wirtualnych |50 |Maksymalna liczba sieci wirtualnych, które można utworzyć subskrypcji, w tej lokalizacji. |
| Maksymalna liczba bram sieci wirtualnej |1 |Maksymalna liczba bram sieci wirtualnej (bram sieci VPN), które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba połączeń sieciowych |2 |Maksymalna liczba połączeń sieciowych (point-to-point lub site-to-site), które subskrypcji można utworzyć we wszystkich bram sieci wirtualnej w tej lokalizacji. |
| Moduły równoważenia obciążenia maksymalna |50 |Maksymalna liczba modułów równoważenia obciążenia, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba kart sieciowych |100 |Maksymalna liczba interfejsów sieciowych, które można utworzyć subskrypcji, w tej lokalizacji. |
| Maksymalna liczba sieciowych grup zabezpieczeń |50 |Maksymalna liczba sieciowych grup zabezpieczeń, które subskrypcji można utworzyć w tej lokalizacji. |

## <a name="view-an-existing-quota"></a>Wyświetlanie istniejących limitu przydziału

1. Na domyślny pulpit nawigacyjny portalu administracyjnym, należy znaleźć **dostawców zasobów** kafelka.
2. Wybierz usługę z limitem przydziału, który chcesz wyświetlić, takie jak **obliczenia** lub **magazynu**.
3. Wybierz **przydziały**, a następnie wybierz limit przydziału, którą chcesz wyświetlić.

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o planów, ofert i przydziałów.](azure-stack-plan-offer-quota-overview.md)
- [Tworzenie przydziałów podczas tworzenia planu.](azure-stack-create-plan.md)
