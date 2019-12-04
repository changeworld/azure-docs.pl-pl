---
title: Odłączania wskaźnik platformy Azure | Microsoft Docs
description: Jak usunąć wystąpienie usługi Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 4c0c415235fd290bc47ac402a6b81a1afa7af903
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777438"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Usuń wskaźnik platformy Azure z obszaru roboczego

Jeśli nie chcesz już korzystać z funkcji wskaźnikowej platformy Azure, w tym artykule wyjaśniono, jak usunąć go z obszaru roboczego.

## <a name="how-to-delete-azure-sentinel"></a>Jak usunąć wskaźnik na platformie Azure

W tle podczas instalowania platformy Azure — rozwiązanie **SecurityInsights** jest instalowane w wybranym obszarze roboczym. W pierwszej kolejności należy usunąć rozwiązanie **SecurityInsights** .

1.  Przejdź do obszaru **wskaźnikowego platformy Azure**, a następnie wybierz pozycję **Konfiguracja**, a następnie pozycję **Ustawienia obszarów roboczych**, a następnie **rozwiązania**.

2.  Wybierz `SecurityInsights` i kliknij go.

    ![Znajdź rozwiązanie SecurityInsights](media/offboard/find-solution.png)

3.  W górnej części strony wybierz pozycję **Usuń**.

    > [!IMPORTANT]
    > Usunięcie obszaru roboczego może mieć wpływ na inne rozwiązania i źródła danych, które korzystają z tego obszaru roboczego, w tym Azure Monitor. Aby sprawdzić, które rozwiązania korzystają z tego obszaru roboczego, zobacz [listę zainstalowanych rozwiązań monitorowania](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions). Aby sprawdzić, które rozwiązania zostały pozyskane w obszarze roboczym, zobacz temat informacje o pozyskiwanym [woluminie danych](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume).

    ![Usuń rozwiązanie SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Co się stanie w tle?

Po usunięciu rozwiązania wskaźnik na platformie Azure zajmie do 48 godzin, aby zakończyć pierwszą fazę procesu usuwania.

Po zidentyfikowaniu rozłączenia rozpocznie się proces odłączania.

**Konfiguracja tych łączników została usunięta:**
-   Office 365

-   AWS

-   Alerty zabezpieczeń usług firmy Microsoft (Azure ATP, Microsoft Cloud App Security, w tym Cloud Discovery do raportowania IT w tle, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Analiza zagrożeń

-   Typowe dzienniki zabezpieczeń (w tym dzienniki CEF, Barracuda i dziennik systemu) (Jeśli masz Azure Security Center, dzienniki będą nadal zbierane).

-   Zdarzenia zabezpieczeń systemu Windows (Jeśli masz Azure Security Center, dzienniki będą nadal zbierane).

W ciągu pierwszych 48 godzin reguły danych i alertów (w tym konfiguracja automatyzacji w czasie rzeczywistym) nie będą już dostępne ani Queryable na platformie Azure.

**Po upływie 30 dni te zasoby są usuwane:**

-   Zdarzenia (w tym metadane badania)

-   Reguły alertów

-   zakładki

Elementy PlayBook, zapisane skoroszyty, zapisane zapytania dotyczące polowań i notesy nie są usuwane. **Niektóre mogą zostać przerwane ze względu na usunięte dane. Można je usunąć ręcznie.**

Po usunięciu usługi istnieje 30-dniowy okres prolongaty, w którym można ponownie włączyć rozwiązanie, a Twoje reguły dotyczące danych i alertów zostaną przywrócone, ale skonfigurowane łączniki, które zostały odłączone, muszą zostać ponownie połączone.

> [!NOTE]
> Jeśli usuniesz rozwiązanie, subskrypcja będzie nadal rejestrowana w dostawcy zasobów platformy Azure. **Można je usunąć ręcznie.**




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób usuwania usługi Azure wskaźnikowej. Jeśli zmienisz zdanie i chcesz je zainstalować ponownie:
- Rozpocznij pracę [na płycie wskaźnikowej platformy Azure](quickstart-onboard.md).

