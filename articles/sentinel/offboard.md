---
title: Usuń usługę Azure Sentinel| Dokumenty firmy Microsoft
description: Jak usunąć wystąpienie usługi Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581688"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Usuwanie wartownika platformy Azure z obszaru roboczego

Jeśli nie chcesz już używać usługi Azure Sentinel, w tym artykule wyjaśniono, jak usunąć go z obszaru roboczego.

## <a name="how-to-remove-azure-sentinel"></a>Jak usunąć usługę Azure Sentinel

Postępuj zgodnie z tym procesem, aby usunąć usługę Azure Sentinel z obszaru roboczego:

1. Przejdź do **usługi Azure Sentinel**, a następnie **ustawień**i wybierz kartę Usuń usługę **Azure Sentinel**.

1. Przed usunięciem usługi Azure Sentinel użyj pól wyboru, aby poinformować nas, dlaczego go usuwasz.

1. Wybierz **pozycję Usuń wartownika platformy Azure ze swojego obszaru roboczego**.
    
    ![Usuwanie rozwiązania SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Co dzieje się za kulisami?

Po usunięciu rozwiązania usługi Azure Sentinel trwa do 48 godzin, aby zakończyć pierwszą fazę procesu usuwania.

Po zidentyfikowaniu rozłączenia rozpoczyna się proces wyłączania.

**Konfiguracja tych złączy jest usuwana:**
-   Office 365

-   AWS

-   Alerty zabezpieczeń usług firmy Microsoft (usługa Azure ATP, microsoft cloud app security, w tym raportowanie it w tle odnajdowania chmury, ochrona tożsamości usługi Azure AD, usługa Microsoft Defender ATP, Centrum zabezpieczeń platformy Azure)

-   Analiza zagrożeń

-   Typowe dzienniki zabezpieczeń (w tym dzienniki oparte na języku CEF, Barracuda i Syslog) (jeśli masz usługę Azure Security Center, te dzienniki będą nadal zbierane.)

-   Zdarzenia zabezpieczeń systemu Windows (jeśli masz Usługę Azure Security Center, te dzienniki będą nadal zbierane).

W ciągu pierwszych 48 godzin dane i reguły analityczne (w tym konfiguracja automatyzacji w czasie rzeczywistym) nie będą już dostępne ani dostępne do queryable w usłudze Azure Sentinel.

**Po 30 dniach te zasoby są usuwane:**

-   Incydenty (w tym metadane dochodzeniowe)

-   Reguły analityczne

-   Zakładki

Podręczniki, zapisane skoroszyty, zapisane zapytania myśliwskie i notesy nie są usuwane. **Niektóre mogą ulec przerwać z powodu usuniętych danych. Można je usunąć ręcznie.**

Po usunięciu usługi, istnieje okres prolongaty 30 dni, podczas których można ponownie włączyć rozwiązanie i dane i reguły analityczne zostaną przywrócone, ale skonfigurowane łączniki, które zostały odłączone muszą być ponownie podłączone.

> [!NOTE]
> Jeśli usuniesz rozwiązanie, twoja subskrypcja będzie nadal rejestrowana u dostawcy zasobów usługi Azure Sentinel. **Można go usunąć ręcznie.**




## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak usunąć usługę Azure Sentinel. Jeśli zmienisz zdanie i chcesz go zainstalować ponownie:
- Rozpocznij pracę [na pokładzie usługi Azure Sentinel](quickstart-onboard.md).
