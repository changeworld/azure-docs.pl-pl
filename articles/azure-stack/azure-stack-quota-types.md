---
title: Typy przydziału w stosie Azure | Dokumentacja firmy Microsoft
description: Przejrzyj typy różnych przydziału dostępne dla usług i zasobów w stosie Azure.
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
ms.date: 04/20/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: b68a963dae4b3621bfd9ecdcbc20146d7b20c457
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="quota-types-in-azure-stack"></a>Typy przydziału w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

[Przydziały](azure-stack-plan-offer-quota-overview.md#plans) definiowania ograniczeń dotyczących zasobów, które subskrypcji użytkownika można udostępnić lub korzystać. Na przykład limit przydziału może zezwolić użytkownikowi na tworzenie maszyn wirtualnych do pięciu. Każdy zasób może mieć własną typy przydziałów.

## <a name="compute-quota-types"></a>Obliczenia bazy danych typów przydziałów
| **Typ** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna liczba maszyn wirtualnych | 20 | Maksymalna liczba maszyn wirtualnych, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba rdzeni maszyny wirtualnej | 50 | Maksymalna liczba rdzeni, które subskrypcji można utworzyć w tej lokalizacji (na przykład maszyna wirtualna A3 ma cztery rdzenie). |
| Maksymalna liczba zestawów dostępności | 10 | Maksymalna liczba zestawów dostępności, które mogą zostać utworzone w tej lokalizacji. |
| Ustawia maksymalną liczbę skalowania maszyny wirtualnej | 20 | Maksymalna liczba zestawy skalowania maszyn wirtualnych, które mogą zostać utworzone w tej lokalizacji. |



## <a name="storage-quota-types"></a>Typy przydział magazynowania
| **Element** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna pojemność (GB) |500 |Pojemność pamięci masowej, które mogą być używane w ramach subskrypcji w tej lokalizacji. |
| Całkowita liczba kont magazynu |20 |Maksymalna liczba kont magazynu, które subskrypcji można utworzyć w tej lokalizacji. |

> [!NOTE]  
> Może upłynąć do dwóch godzin, zanim nowy przydział magazynowania jest wymuszana. 
> 
> 

## <a name="network-quota-types"></a>Typy przydziałów sieci
| **Element** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna liczba publicznych adresów IP |50 |Maksymalna liczba publicznych adresów IP, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba sieci wirtualnych |50 |Maksymalna liczba sieci wirtualnych, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba bram sieci wirtualnej |1 |Maksymalna liczba bram sieci wirtualnej (bramy sieci VPN), które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba połączeń sieciowych |2 |Maksymalna liczba połączeń sieciowych (point-to-point lub site-to-site), które subskrypcji można utworzyć przez wszystkie bramy sieci wirtualnej w tej lokalizacji. |
| Maksymalna liczba modułów równoważenia obciążenia |50 |Maksymalna liczba modułów równoważenia obciążenia, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba kart sieciowych |100 |Maksymalna liczba interfejsów sieciowych, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna liczba sieciowych grup zabezpieczeń |50 |Maksymalna liczba grup zabezpieczeń sieci, które subskrypcji można utworzyć w tej lokalizacji. |

## <a name="view-an-existing-quota"></a>Wyświetl istniejący przydziału
1. Kliknij przycisk **więcej usług** > **dostawców zasobów**.
2. Wybierz usługę z przydziałem, który chcesz wyświetlić.
3. Kliknij przycisk **przydziały**i wybierz limit przydziału, który chcesz wyświetlić.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o planach, ofertami i przydziałów.](azure-stack-plan-offer-quota-overview.md)

[Tworzenie przydziałów podczas tworzenia planu.](azure-stack-create-plan.md)
