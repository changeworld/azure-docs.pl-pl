---
title: Nie powiodło się platformy Azure z VMware z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Po pracy w trybie failover maszyny wirtualne na platformie Azure możesz uruchomić powrotu po awarii, aby przywrócić maszyn wirtualnych do lokalnego. Dowiedz się więcej czynności w celu wykonaj powrót po awarii.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 8f580fa40bade2bf586dd116729881b249bbba88
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29944011"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Nie powiodło się platformy Azure z lokacją lokalną

W tym artykule opisano, jak niepowodzenie wstecz maszyn wirtualnych z maszyn wirtualnych platformy Azure do środowiska lokalnego VMware. Postępuj zgodnie z instrukcjami w tym artykule, aby zakończyć się niepowodzeniem z powrotem z maszyn wirtualnych VMware lub systemem Windows lub Linux, serwerów fizycznych, po zostały one przejścia w tryb failover z lokalnej lokacji do Azure za pomocą [pracy awaryjnej w usłudze Azure Site Recovery](site-recovery-failover.md) samouczka.

## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że znasz szczegółowe informacje o [różnego rodzaju powrotu po awarii](concepts-types-of-failback.md) i odpowiedniego ostrzeżenia.

> [!WARNING]
> Nie można przerwać po albo mieć [ukończone migracji](migrate-overview.md#what-do-we-mean-by-migration), przenieść maszynę wirtualną do innej grupy zasobów lub usunąć maszyny wirtualnej platformy Azure. Jeśli wyłączysz ochrony maszyny wirtualnej nie można przerwać ponownie.

> [!WARNING]
> Serwera fizycznego systemu Windows Server 2008 R2 z dodatkiem SP1, jeśli chroniony Failover na platformie Azure, nie można przełączyć ponownie.

> [!NOTE]
> Jeśli nie powiodło przez maszyny wirtualne VMware, nie można przerwać do hosta funkcji Hyper-V.


- Przed kontynuowaniem wykonaj kroki ponownej ochrony, tak aby maszyny wirtualne są w stanie replikowanych i można uruchomić trybu failover do lokacji lokalnej. Aby uzyskać więcej informacji, zobacz [jak włączyć ją ponownie z platformy Azure do środowiska lokalnego](vmware-azure-reprotect.md).

- Upewnij się, że vCenter jest w stanie połączenia przed wykonaniem powrotu po awarii. W przeciwnym razie nie działa rozłączanie dysków i dołączać do maszyny wirtualnej.

- Podczas pracy w trybie failover na platformie Azure lokalnej lokacji może nie być dostępny i serwer konfiguracji mogą być niedostępne lub zamknięta w dół. Podczas ponownej ochrony i powrotu po awarii lokalnego serwera konfiguracji powinna być uruchomiona i jest niepodłączony OK. 

- Podczas powrotu po awarii maszyna wirtualna musi istnieć w bazie danych konfiguracji serwera lub powrotu po awarii nie powiodło się. Upewnij się, wykonanie zaplanowanego tworzenia kopii zapasowych serwera. W przypadku awarii należy przywrócić działanie serwera przy użyciu oryginalnego adresu IP do powrotu po awarii do pracy.

- Główny serwer docelowy nie powinna mieć żadnych migawek przed wyzwalania ponownej ochrony/powrotu po awarii.

## <a name="overview-of-failback"></a>Omówienie powrotu po awarii
Po za pośrednictwem powiodły się do platformy Azure, użytkownik może nie powieść do lokacji sieci lokalnej, wykonując następujące czynności:

1. [Włącz ponownie ochronę](vmware-azure-reprotect.md) maszyn wirtualnych na platformie Azure, aby uruchamiają są replikowane do maszyn wirtualnych VMware w lokacji lokalnej. W ramach tego procesu również należy:

    * Skonfiguruj lokalny główny serwer docelowy. Użyj główny cel systemu Windows dla maszyn wirtualnych systemu Windows i [główny cel Linux](vmware-azure-install-linux-master-target.md) dla maszyn wirtualnych systemu Linux.
    * Konfigurowanie [serwera przetwarzania](vmware-azure-set-up-process-server-azure.md).
    * Uruchom [Wznów](vmware-azure-reprotect.md) wyłączyć na lokalnej maszynie wirtualnej i synchronizowanie danych Azure maszyny wirtualnej z dyskami lokalnymi.

2. Po replikacji maszyn wirtualnych na platformie Azure do lokacji lokalnej, uruchamiania trybu failover z platformy Azure do lokacji lokalnej.

3. Po danych z powrotem nie powiedzie się, możesz ponownie Obejmij ochroną maszyn wirtualnych lokalnie aby rozpoczynały się replikację do platformy Azure.

Aby szybko zapoznać Obejrzyj film następujący temat powrót po awarii do lokacji lokalnej:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Kroki, aby wykonaj powrót po awarii

> [!IMPORTANT]
> Przed rozpoczęciem powrotu po awarii, upewnij się, zostało zakończone ponowne włączenie ochrony maszyn wirtualnych. Maszyny wirtualne powinny znajdować się w stanie chronionym i ich kondycję powinna być **OK**. Aby Włącz ponownie ochronę maszyny wirtualnej, przeczytaj [jak włączyć ją ponownie](vmware-azure-reprotect.md).

1. Na stronie zreplikowanych elementów wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy, aby zaznaczyć **nieplanowany tryb Failover**.
2. W **potwierdzić trybu Failover**, sprawdź kierunek pracy awaryjnej (na platformie Azure). Następnie wybierz punkt odzyskiwania (Najnowsza lub najnowszą wersję aplikacji spójne), który ma być używany do pracy awaryjnej. Punkt spójne aplikacji znajduje się za ostatniego punktu w czasie i powoduje utratę danych.
3. Podczas pracy w trybie failover Usługa Site Recovery zamyka maszyny wirtualne na platformie Azure. Po sprawdzeniu powrót po awarii zakończone zgodnie z oczekiwaniami, można sprawdzić, czy wyłączona maszyn wirtualnych na platformie Azure.
4. **Zatwierdź** wymaganego do usunięcia przełączona w tryb failover maszyny wirtualnej z platformy Azure. Kliknij prawym przyciskiem myszy chronionego elementu, a następnie wybierz **zatwierdzić**. Zadanie usuwa przełączona w tryb failover maszyny wirtualnej na platformie Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Do jakich punktu odzyskiwania można I wykonaj powrót po awarii maszyn wirtualnych?

Podczas powrotu po awarii dostępne są dwie opcje, aby powrócić planu maszyny wirtualnej do odzyskiwania danych.

- W przypadku wybrania najnowsze przetworzone punktu w czasie wszystkie maszyny wirtualne przełączone awaryjnie do ich najnowszego dostępnego punktu w czasie. W przypadku grupy replikacji w ramach planu odzyskiwania, każdej maszyny wirtualnej w grupie replikacji nie powiedzie się za pośrednictwem jego niezależnie od ostatniego punktu w czasie.

  Nie można przerwać powrót maszyny wirtualnej do momentu składa się z co najmniej jeden punkt odzyskiwania. Nie można przerwać wstecz planu odzyskiwania, dopóki wszystkie maszyny wirtualne mają co najmniej jednego punktu odzyskiwania.

  > [!NOTE]
  > Najnowszy punkt odzyskiwania jest punkt odzyskiwania spójna w razie awarii.

- W przypadku wybrania punktu odzyskiwania zapewniających spójność aplikacji awarii jednej maszyny wirtualnej odzyskuje do jego najnowszego punktu odzyskiwania zapewniających spójność aplikacji. W przypadku planu odzyskiwania z grupą replikacji każdej grupy replikacji odzyskuje do wspólnego punktu odzyskiwania.
Punktów odzyskiwania zapewniających spójność aplikacji może być za w czasie i może być utrata danych.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co się dzieje VMware narzędzia post powrotu po awarii?

W przypadku maszyny wirtualnej systemu Windows usługi Site Recovery wyłącza narzędzia VMware w trybie failover. Podczas powrotu po awarii maszyny wirtualnej systemu Windows są reenabled narzędzi VMware. 


## <a name="reprotect-from-on-premises-to-azure"></a>Włącz ponownie ochronę z lokalnej na platformie Azure
Po zakończeniu powrotu po awarii i uruchomieniu zatwierdzania, odzyskane maszynach wirtualnych platformy Azure są usuwane. Teraz maszyna wirtualna znajduje się do lokacji lokalnej, ale nie będzie można go chronić. Aby uruchomić ponownie replikację do platformy Azure, wykonaj następujące czynności:

1. Wybierz **magazynu** > **ustawienie** > **elementy replikowane**, wybrać maszyny wirtualne, których nie powiodła się ponownie, a następnie wybierz  **Włącz ochronę ponownie**.
2. Wprowadź wartość z serwera przetwarzania, który ma służyć do wysyłania danych do usługi Azure.
3. Wybierz **OK** do rozpoczęcia zadań ponownej ochrony.

> [!NOTE]
> Po rozruchu maszyny wirtualnej lokalnymi, dopiero po pewnym czasie dla agenta zarejestrować serwer konfiguracji (maksymalnie 15 minut). W tym czasie ponownej ochrony kończy się niepowodzeniem i zwraca komunikat o błędzie informujący, że agent nie jest zainstalowany. Poczekaj kilka minut, a następnie spróbuj ponownie ponownej ochrony.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu działania zadań ponownej ochrony, możliwość replikacja maszyny wirtualnej Azure, i [pracy awaryjnej](site-recovery-failover.md) ponownie przenoszenia maszyn wirtualnych na platformie Azure.


