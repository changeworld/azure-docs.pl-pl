---
title: Monitorowanie serwera procesów odzyskiwania witryny platformy Azure
description: W tym artykule opisano sposób monitorowania serwera przetwarzania usługi Azure Site Recovery używanego do odzyskiwania po awarii vmware vmware vmware/server fizycznego
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257071"
---
# <a name="monitor-the-process-server"></a>Monitorowanie serwera przetwarzania

W tym artykule opisano sposób monitorowania serwera przetwarzania [odzyskiwania lokacji.](site-recovery-overview.md)

- Serwer przetwarzania jest używany podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure.
- Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji. Jest instalowany domyślnie podczas wdrażania serwera konfiguracji.
- Opcjonalnie, aby skalować i obsługiwać większą liczbę zreplikowanych maszyn i większe ilości ruchu replikacji, można wdrożyć dodatkowe serwery procesów skalowanych w poziomie.

[Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md) o roli i wdrażaniu serwerów przetwarzania.

## <a name="monitoring-overview"></a>Omówienie monitorowania

Ponieważ serwer przetwarzania ma tak wiele ról, szczególnie w replikowanych danych buforowania, kompresji i transferu na platformę Azure, ważne jest, aby monitorować kondycję serwera procesu na bieżąco.

Istnieje wiele sytuacji, które często wpływają na wydajność serwera przetwarzania. Problemy wpływające na wydajność będą miały kaskadowy wpływ na kondycję maszyny wirtualnej, ostatecznie wypychając serwer przetwarzania i jego zreplikowane maszyny do stanu krytycznego. Sytuacje obejmują:

- Duża liczba maszyn wirtualnych korzysta z serwera przetwarzania, zbliżając się lub przekraczając zalecane ograniczenia.
- Maszyny wirtualne korzystające z serwera przetwarzania mają wysoką szybkość zmian.
- Przepustowość sieciowa między maszynami wirtualnymi a serwerem przetwarzania nie wystarczy do przekazywania danych replikacji do serwera przetwarzania.
- Przepływność sieciowa między serwerem przetwarzania a platformą Azure nie jest wystarczająca do przekazywania danych replikacji z serwera przetwarzania na platformę Azure.

Wszystkie te problemy mogą mieć wpływ na cel punktu odzyskiwania (RPO) maszyn wirtualnych. 

**Dlaczego?** Ponieważ generowanie punktu odzyskiwania dla maszyny Wirtualnej wymaga, aby wszystkie dyski na maszynie wirtualnej miały wspólny punkt. Jeśli jeden dysk ma wysoki współczynnik zmian, replikacja jest powolna lub serwer przetwarzania nie jest optymalny, wpływa to na sposób efektywnego tworzenia punktów odzyskiwania.

## <a name="monitor-proactively"></a>Aktywne monitorowanie

Aby uniknąć problemów z serwerem przetwarzania, ważne jest, aby:

- Zapoznaj się z określonymi wymaganiami dotyczącymi serwerów procesów przy użyciu [pojemności i wskazówki dotyczące rozmiaru](site-recovery-plan-capacity-vmware.md#capacity-considerations)oraz upewnij się, że serwery procesów są wdrażane i uruchamiane zgodnie z zaleceniami.
- Monitoruj alerty i rozwiązuj problemy w miarę ich występowania, aby wydajnie uruchamiać serwery procesów.


## <a name="process-server-alerts"></a>Alerty serwera przetwarzania

Serwer procesów generuje szereg alertów kondycji, podsumowane w poniższej tabeli.

**Typ alertu** | **Szczegóły**
--- | ---
![W dobrej kondycji][green] | Serwer przetwarzania jest podłączony i zdrowy.
![Ostrzeżenie][yellow] | Wykorzystanie procesora > 80% w ciągu ostatnich 15 minut
![Ostrzeżenie][yellow] | Użycie pamięci > 80% w ciągu ostatnich 15 minut
![Ostrzeżenie][yellow] | Wolne miejsce w folderze pamięci podręcznej < 30% w ciągu ostatnich 15 minut
![Ostrzeżenie][yellow] | Usługa Site Recovery monitoruje oczekujące/wychodzące dane co pięć minut i szacuje, że danych w pamięci podręcznej serwera procesu nie można przekazać na platformę Azure w ciągu 30 minut.
![Ostrzeżenie][yellow] | Usługi serwera przetwarzania nie są uruchomione przez ostatnie 15 minut
![Krytyczny][red] | Wykorzystanie procesora > 95% w ciągu ostatnich 15 minut
![Krytyczny][red] | Zużycie pamięci > 95% w ciągu ostatnich 15 minut
![Krytyczny][red] | Wolne miejsce w folderze pamięci podręcznej < 25% w ciągu ostatnich 15 minut
![Krytyczny][red] | Usługa Site Recovery monitoruje oczekujące/wychodzące dane co pięć minut i szacuje, że danych w pamięci podręcznej serwera procesu nie można przekazać na platformę Azure w ciągu 45 minut.
![Krytyczny][red] | Brak pulsu z serwera przetwarzania przez 15 minut.

![Klucz tabeli](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Ogólny stan kondycji serwera przetwarzania jest oparty na najgorszym wygenerowanym alertem.



## <a name="monitor-process-server-health"></a>Monitorowanie kondycji serwera procesów

Stan kondycji serwerów procesów można monitorować w następujący sposób: 

1. Aby monitorować kondycję i stan replikacji replikowanego komputera oraz jego serwera przetwarzania, w przechowalni > **elementy replikowane**kliknij komputer, który chcesz monitorować.
2. W **kondycji replikacji**można monitorować stan kondycji maszyny Wirtualnej. Kliknij stan, aby przejść do szczegółów błędu.

    ![Kondycja serwera przetwarzania na pulpicie nawigacyjnym maszyny Wirtualnej](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. W **kondycji serwera przetwarzania**można monitorować stan serwera przetwarzania. Przejdź do szczegółów.

    ![Przetwarzanie szczegółów serwera na pulpicie nawigacyjnym maszyny Wirtualnej](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Kondycję można również monitorować za pomocą reprezentacji graficznej na stronie maszyny Wirtualnej.
    - Serwer procesów skalowanych w poziomie zostanie wyróżniony na pomarańczowo, jeśli są z nim skojarzone ostrzeżenia, a czerwony, jeśli ma jakieś krytyczne problemy. 
    - Jeśli serwer przetwarzania jest uruchomiony w domyślnym wdrożeniu na serwerze konfiguracji, serwer konfiguracji zostanie odpowiednio wyróżniony.
    - Aby przejść do szczegółów, kliknij serwer konfiguracji lub serwer przetwarzania. Zanotuj wszelkie problemy i wszelkie zalecenia dotyczące korygowania.

Można również monitorować serwery procesów w przechowalni w obszarze **Infrastruktura odzyskiwania lokacji**. W **programie Zarządzanie infrastrukturą odzyskiwania witryny**kliknij pozycję **Serwery konfiguracji**. Wybierz serwer konfiguracji skojarzony z serwerem przetwarzania i przejdź do szczegółów serwera przetwarzania.


## <a name="next-steps"></a>Następne kroki

- Jeśli masz jakiekolwiek problemy z serwerami procesów, postępuj zgodnie z naszymi [wskazówkami dotyczącymi rozwiązywania problemów](vmware-physical-azure-troubleshoot-process-server.md)
- Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie na [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
