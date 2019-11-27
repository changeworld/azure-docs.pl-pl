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
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: a45f273078a622de5e256457fc45b6cb6cae512f
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464138"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>Usuń wskaźnik na platformie Azure z dzierżawy

Jeśli nie chcesz już korzystać z platformy Azure, w tym artykule wyjaśniono, jak usunąć go z dzierżawy.

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

-   Zakładki

Elementy PlayBook, zapisane skoroszyty, zapisane zapytania dotyczące polowań i notesy nie są usuwane. **Niektóre mogą zostać przerwane ze względu na usunięte dane. Można je usunąć ręcznie.**

Po usunięciu usługi istnieje 30-dniowy okres prolongaty, w którym można ponownie włączyć rozwiązanie, a Twoje reguły dotyczące danych i alertów zostaną przywrócone, ale skonfigurowane łączniki, które zostały odłączone, muszą zostać ponownie połączone.

> [!NOTE]
> Jeśli usuniesz rozwiązanie, subskrypcja będzie nadal rejestrowana w dostawcy zasobów platformy Azure. **Można je usunąć ręcznie.**




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób usuwania usługi Azure wskaźnikowej. Jeśli zmienisz zdanie i chcesz je zainstalować ponownie:
- Rozpocznij pracę [na płycie wskaźnikowej platformy Azure](quickstart-onboard.md).

