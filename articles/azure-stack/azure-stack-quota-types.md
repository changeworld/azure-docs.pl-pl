---
title: Typy limitów przydziału w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Sprawdź typy inny limit przydziału dostępne dla usług i zasobów w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.openlocfilehash: 3c0ab236dd6fce10be0a50c435f04517e14c1387
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077599"
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


## <a name="edit-a-quota"></a>Edytowanie przydziału  
Użytkownik może edytować oryginalną konfigurację przydziału zamiast [przy użyciu planu dodatku](create-add-on-plan.md). Podczas edytowania limit przydziału nową konfigurację automatycznie stosuje globalnie wszystkie plany, które używają tego limitu przydziału, a wszystkie istniejące subskrypcje, korzystających z tych planów. Edytowanie przydziału jest inny niż zastosowania planu dodatku zapewnienie zmodyfikowane limit przydziału, który użytkownik zdecyduje, aby subskrybować. 

### <a name="to-edit-a-quota"></a>Aby edytować limit przydziału  
1. Na domyślny pulpit nawigacyjny portalu administracyjnym, należy znaleźć **dostawców zasobów** kafelka.
2. Wybierz usługę z limitem przydziału, który chcesz zmodyfikować, takie jak **obliczenia**, **sieci**, lub **magazynu**.
3. Następnie wybierz pozycję **przydziały**, a następnie wybierz limit przydziału, o których chcesz zmienić.
4. Na **ustawić przydziały** okienku, zmodyfikuj wartości progu, a następnie wybierz **Zapisz**. 

Nowe wartości dla limitu przydziału stosowane globalnie do wszystkie plany, które używają przydziału zmodyfikowane i wszystkie istniejące subskrypcje, korzystających z tych planów. 



## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o planów, ofert i przydziałów.](azure-stack-plan-offer-quota-overview.md)
- [Tworzenie przydziałów podczas tworzenia planu.](azure-stack-create-plan.md)
