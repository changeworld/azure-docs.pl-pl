---
title: Typy limitów przydziału w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Wyświetl i Edytuj typy inny limit przydziału dostępne dla usług i zasobów w usłudze Azure Stack.
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
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 848b2a0c912a00a2185d7e4b7b8d8446bc1f6aca
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497181"
---
# <a name="quota-types-in-azure-stack"></a>Typy limitów przydziału w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

[Przydziały](azure-stack-plan-offer-quota-overview.md#plans) definiowania ograniczeń zasobów, które subskrypcji użytkownika można aprowizować lub zużywają. Na przykład limit przydziału może zezwolić użytkownikowi na utworzenie maksymalnie pięciu maszyn wirtualnych. Każdy zasób może mieć własne typy limitów przydziałów.

> [!IMPORTANT]
> Może upłynąć do dwóch godzin nowe przydziały, które mają być dostępne w portalu użytkowników, lub przed zmienionych limitu przydziału jest wymuszany.

## <a name="compute-quota-types"></a>Typy limitów przydziału obliczeniowych

| **Typ** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna liczba maszyn wirtualnych | 50 | Maksymalna liczba maszyn wirtualnych, które można utworzyć subskrypcji, w tej lokalizacji. |
| Maksymalna liczba rdzeni maszyn wirtualnych | 100 | Maksymalna liczba rdzeni, które można utworzyć subskrypcji, w tym miejscu (na przykład maszyna wirtualna A3 ma cztery rdzenie). |
| Maksymalna liczba zestawów dostępności | 10 | Maksymalna liczba zestawów dostępności, które mogą być tworzone w tej lokalizacji. |
| Maksymalna liczba maszyn wirtualnych zestawów skalowania | 100 | Maksymalna liczba zestawów skalowania maszyn wirtualnych, które mogą być tworzone w tej lokalizacji. |
| Maksymalną pojemność dysku zarządzanego w warstwie standardowa (w GB) | 2048 | Maksymalna pojemność standardowych dysków zarządzanych, które mogą być tworzone w tej lokalizacji. |
| Maksymalną pojemność dysku zarządzanego w warstwie premium (w GB) | 2048 | Maksymalna pojemność premium usługa managed disks, które mogą być tworzone w tej lokalizacji. |

> [!NOTE]  
> Maksymalna pojemność dysk niezarządzany (stronicowych obiektów blob) różni się od limitu przydziału dysku zarządzanego. Tę wartość można ustawić **przydziały pamięci masowej**.

## <a name="storage-quota-types"></a>Typy limitów przydziału magazynu

| **Element** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna pojemność (GB) |2048 |Pojemność pamięci masowej (w tym obiektów blob i wszystkie skojarzone migawki, tabele i kolejki), które mogą być używane przez subskrypcji w tej lokalizacji. |
| Łączna liczba kont magazynu |20 |Maksymalna liczba kont magazynu, które można utworzyć subskrypcji, w tej lokalizacji. |

> [!NOTE]  
> Maksymalna pojemność dysków zarządzanych jest niezależna od limitu przydziału pamięci masowej. Tę wartość można ustawić **obliczenia przydziały**.

## <a name="network-quota-types"></a>Typy przydziałów sieci

| **Element** | **Wartość domyślna** | **Opis** |
| --- | --- | --- |
| Maksymalna publicznych adresów IP |50 |Maksymalna liczba publicznych adresów IP, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna sieci wirtualnych |50 |Maksymalna liczba sieci wirtualnych, które można utworzyć subskrypcji, w tej lokalizacji. |
| Bramy sieci wirtualnej |1 |Maksymalna liczba bram sieci wirtualnej (bram sieci VPN), które subskrypcji można utworzyć w tej lokalizacji. |
| Połączenia sieciowe maksymalna |2 |Maksymalna liczba połączeń sieciowych (point-to-point lub site-to-site), które subskrypcji można utworzyć we wszystkich bram sieci wirtualnej w tej lokalizacji. |
| Moduły równoważenia obciążenia maksymalna |50 |Maksymalna liczba modułów równoważenia obciążenia, które subskrypcji można utworzyć w tej lokalizacji. |
| Maksymalna kart sieciowych |100 |Maksymalna liczba interfejsów sieciowych, które można utworzyć subskrypcji, w tej lokalizacji. |
| Grupy zabezpieczeń sieci |50 |Maksymalna liczba sieciowych grup zabezpieczeń, które subskrypcji można utworzyć w tej lokalizacji. |

## <a name="view-an-existing-quota"></a>Wyświetlanie istniejących limitu przydziału

Istnieją dwa różne sposoby, aby wyświetlić istniejące limitu przydziału:

### <a name="plans"></a>Plany

1. W okienku nawigacji po lewej stronie portalu administratora, wybierz **plany**.
2. Wybierz plan, który chcesz wyświetlić szczegółowe informacje, klikając jego nazwę.
3. W otwartym bloku wybierz **usług i przydziałów**.
4. Wybierz limit przydziału, czy chcesz wyświetlić, klikając go w **nazwa** kolumny.

    [![Przydziały](media/azure-stack-quota-types/quotas1sm.png "Wyświetl limity przydziału")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Dostawcy zasobów

1. Na domyślny pulpit nawigacyjny portalu administracyjnym, należy znaleźć **dostawców zasobów** kafelka.
2. Wybierz usługę z limitem przydziału, który chcesz wyświetlić, takie jak **obliczenia**, **sieci**, lub **magazynu**.
3. Wybierz **przydziały**, a następnie wybierz limit przydziału, którą chcesz wyświetlić.

## <a name="edit-a-quota"></a>Edytowanie przydziału

Istnieją dwa różne sposoby, aby edytować limit przydziału:

### <a name="edit-a-plan"></a>Edytuj plan

1. W okienku nawigacji po lewej stronie portalu administratora, wybierz **plany**.
2. Wybierz plan, dla którego chcesz edytować limit przydziału, klikając jego nazwę.
3. W otwartym bloku wybierz **usług i przydziałów**.
4. Wybierz limit przydziału, czy chcesz edytować, klikając go w **nazwa** kolumny.
    [![Przydziały](media/azure-stack-quota-types/quotas1sm.png "Wyświetl limity przydziału")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. W otwartym bloku wybierz **edytować w obliczeniowej**, **edytowania w sieci**, lub **edycji w magazynie**.
    ![Przydziały](media/azure-stack-quota-types/quotas3.png "Wyświetl limity przydziału")

Alternatywnie możesz wykonać tę procedurę, aby edytować limit przydziału:

1. Domyślny pulpit nawigacyjny z portalu administratora, znaleźć **dostawców zasobów** kafelka.
2. Wybierz usługę z limitem przydziału, który chcesz zmodyfikować, takie jak **obliczenia**, **sieci**, lub **magazynu**.
3. Następnie wybierz pozycję **przydziały**, a następnie wybierz limit przydziału, o których chcesz zmienić.
4. Na **limitami przydziału magazynów ustaw**, **obliczenia ustawić przydziały**, lub **limitów przydziałów sieci ustaw** okienko (w zależności od typu przydziału z wybraniu do edycji), zmodyfikuj wartości progu, a następnie wybierz **Zapisz**.

### <a name="edit-original-configuration"></a>Edytuj oryginalnej konfiguracji
  
Użytkownik może edytować oryginalną konfigurację przydziału zamiast [przy użyciu planu dodatku](create-add-on-plan.md). Podczas edytowania limit przydziału nową konfigurację automatycznie stosuje globalnie wszystkie plany, które używają tego limitu przydziału, a wszystkie istniejące subskrypcje, korzystających z tych planów. Edytowanie przydziału jest inny niż zastosowania planu dodatku zapewnienie zmodyfikowane limit przydziału, który użytkownik zdecyduje, aby subskrybować.

Nowe wartości dla limitu przydziału stosowane globalnie do wszystkie plany, które używają przydziału zmodyfikowane i wszystkie istniejące subskrypcje, korzystających z tych planów.

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o planów, ofert i przydziałów.](azure-stack-plan-offer-quota-overview.md)
- [Tworzenie przydziałów podczas tworzenia planu.](azure-stack-create-plan.md)
