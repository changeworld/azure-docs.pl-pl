---
title: Monitorowanie usługi Azure Site Recovery serwera przetwarzania
description: W tym artykule opisano sposób monitorowania serwera przetwarzania usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: rayne
ms.openlocfilehash: 5fac369f15edb3ef0be31d3dc7d7434104c18dfe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928170"
---
# <a name="monitor-the-process-server"></a>Monitorowanie serwera przetwarzania

W tym artykule opisano sposób monitorowania [Site Recovery](site-recovery-overview.md) serwera przetwarzania.

- Serwer przetwarzania jest używana podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na platformę Azure.
- Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji. Jest instalowany domyślnie podczas wdrażania serwera konfiguracji.
- Opcjonalnie skalowania i obsługują większej liczby replikowanych maszyn i zwiększenia liczby ruch związany z replikacją, można wdrożyć serwery dodatkowych, skalowalnego w poziomie procesu.

[Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md) o roli i wdrożenie serwerów przetwarzania.

## <a name="monitoring-overview"></a>Omówienie monitorowania

Ponieważ serwer przetwarzania ma wiele ról, szczególnie w zreplikowanych danych pamięci podręcznej, kompresji i przesyłanie danych do platformy Azure, jest ważne, aby monitorować kondycję serwera przetwarzania na bieżąco.

Istnieje wiele sytuacji, które często wpływać na wydajność serwera przetwarzania. Problemów wpływających na wydajność mają kaskadowych wpływa na kondycji maszyny Wirtualnej, po pewnym czasie wypychanie serwera przetwarzania i jego replikowanych maszyn do stanu krytycznego. Sytuacje:

- Dużej liczby maszyn wirtualnych Użyj serwera przetwarzania zbliża się lub większej niż zalecane ograniczenia.
- Maszyny wirtualne, za pomocą serwera przetwarzania mają wysokie tempo.
- Przepustowość sieci między maszynami wirtualnymi i serwer przetwarzania jest niewystarczająca do przekazywania danych replikacji do serwera przetwarzania.
- Przepustowość sieci między serwerem przetwarzania a platformą Azure jest niewystarczająca do przekazywania danych replikacji z serwera przetwarzania na platformie Azure.

Wszystkie te problemy mogą wpłynąć na cel punktu odzyskiwania (RPO) maszyn wirtualnych. 

**Dlaczego?** Ponieważ generowanie punkt odzyskiwania dla maszyny Wirtualnej wymaga wszystkich dysków na maszynie Wirtualnej wspólnego punktu. Jeśli dysk ma wysokie tempo, replikacja przebiega powoli lub serwer przetwarzania nie są optymalne, wpływa to, jak wydajne są tworzone punkty odzyskiwania.

## <a name="monitor-proactively"></a>Aktywne monitorowanie

Aby uniknąć problemów z serwera przetwarzania, jest ważne, aby:

- Zrozumienie określonych wymagań dotyczących serwerów przetwarzania przy użyciu [pojemności i wskazówki dotyczące rozmiaru](site-recovery-plan-capacity-vmware.md#capacity-considerations), upewnij się, proces serwery są wdrażane i uruchomione zgodnie z zaleceniami.
- Monitorowanie alertów i rozwiązywanie problemów w miarę ich występowania, aby zachować serwerów przetwarzania wydajne działanie.


## <a name="process-server-alerts"></a>Alerty serwera przetwarzania

Serwer przetwarzania generuje liczbę alertów dotyczących kondycji, podsumowane w poniższej tabeli.

**Typ alertu** | **Szczegóły**
--- | ---
![W dobrej kondycji][green] | Serwer przetwarzania jest połączony i działa prawidłowo.
![Ostrzeżenie][yellow] | Wykorzystanie procesora CPU > 80% w przypadku ostatnich 15 minut
![Ostrzeżenie][yellow] | Użycie pamięci > 80% w przypadku ostatnich 15 minut
![Ostrzeżenie][yellow] | % < 30 wolnego miejsca na folder pamięci podręcznej dla ostatnich 15 minut
![Ostrzeżenie][yellow] | Usługi serwera przetwarzania nie są uruchomione dla ostatnich 15 minut
![Krytyczny][red] | Wykorzystanie procesora CPU > 95% ostatnich 15 minut
![Krytyczny][red] | Użycie pamięci > 95% ostatnich 15 minut
![Krytyczny][red] | Pamięć podręczna folderu wolnego miejsca < 25% dla ostatnich 15 minut
![Krytyczny][red] | Brak pulsu z serwera przetwarzania przez 15 minut.

![Klucz tabeli](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Ogólny stan kondycji na serwerze przetwarzania jest oparty na najgorszy alert, wygenerowany.



## <a name="monitor-process-server-health"></a>Monitorowanie kondycji serwera przetwarzania

Możesz monitorować stan kondycji serwerów przetwarzania w następujący sposób: 

1. Monitorowanie kondycji replikacji i statusu replikowanej maszyny i jej serwer przetwarzania w magazynie > **zreplikowane elementy**, kliknij maszynę, którą chcesz monitorować.
2. W **kondycja replikacji**, można monitorować stan kondycji maszyny Wirtualnej. Kliknij stan, aby przejść do szczegółów, aby uzyskać szczegóły błędu.

    ![Kondycja serwera przetwarzania na pulpicie nawigacyjnym maszyn wirtualnych](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. W **Kondycja serwera procesu**, możesz monitorować stan serwera przetwarzania. Przejść do szczegółów, aby uzyskać szczegółowe informacje.

    ![Szczegółów dotyczących serwera przetwarzania na pulpicie nawigacyjnym maszyn wirtualnych](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Kondycja można również monitorować za pomocą graficzną reprezentację na stronie maszyny Wirtualnej.
    - Serwera przetwarzania skalowalnego w poziomie będzie wyróżniony kolorem pomarańczowym, jeśli wystąpiły ostrzeżenia skojarzonych z nim, a kolor czerwony, jeśli ma ona wszelkie problemy krytyczne. 
    - Jeśli serwer przetwarzania jest uruchomiona w domyślnym wdrożeniu na serwerze konfiguracji, serwer konfiguracji zostaną wyróżnione w związku z tym.
    - Aby przejść do szczegółów, kliknij na serwerze konfiguracji lub serwerze przetwarzania. Należy pamiętać, wszelkie problemy, a żadnych zaleceń dotyczących korygowania.

Można również monitorować serwery w magazynie w ramach przetwarzania **infrastruktura usługi Site Recovery**. W **zarządzania infrastrukturą usługi Site Recovery**, kliknij przycisk **serwery konfiguracji**. Wybierz serwer konfiguracji skojarzone z serwerem przetwarzania i testowania odzyskiwania po awarii w dół do szczegółów dotyczących serwera przetwarzania.


## <a name="next-steps"></a>Kolejne kroki

- Jeśli masz jakiekolwiek przetwarzać serwerów problemy, postępuj zgodnie z naszym [wskazówki dotyczące rozwiązywania problemów](vmware-physical-azure-troubleshoot-process-server.md)
- Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie w [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
