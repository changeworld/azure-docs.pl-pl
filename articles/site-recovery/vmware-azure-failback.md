---
title: Niepowodzenie powrót po awarii z platformy Azure podczas odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i powrócić po awarii lokacji lokalnej po włączeniu trybu failover na platformę Azure podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 7773a2f43eb076075be484d92fde31094a2b584b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318125"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Powrót po awarii serwerów fizycznych i maszyn wirtualnych VMware z platformy Azure do lokacji lokalnej

W tym artykule opisano, jak niepowodzenie wstecz maszyn wirtualnych w usłudze Azure Virtual Machines w lokalnym środowisku VMware. Postępuj zgodnie z instrukcjami w tym artykule nie powiedzie się kopii maszyn wirtualnych VMware ani systemu Windows/Linux lokacji serwerów fizycznych, po ich przełączeniu w tryb failover ze środowiska lokalnego na platformę Azure za pomocą [trybu Failover w usłudze Azure Site Recovery](site-recovery-failover.md) samouczka.

## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że znasz szczegółowe informacje o [różnego rodzaju powrotu po awarii](concepts-types-of-failback.md) i odpowiedniego ostrzeżenia.

> [!WARNING]
> Nie można powracać po użytkownik ma [ukończyć migracji](migrate-overview.md#what-do-we-mean-by-migration)maszynę wirtualną do innej grupy zasobów lub przeniesiony, usunięty z maszyny wirtualnej platformy Azure. Wyłączenie ochrony maszyny wirtualnej, użytkownik nie może wykonać powrotu po awarii.

> [!WARNING]
> Serwera fizycznego systemu Windows Server 2008 R2 z dodatkiem SP1, jeśli chroniony, a następnie przełączone w tryb failover Azure, nie może być powrót po awarii.

> [!NOTE]
> Jeśli Failover maszyn wirtualnych programu VMware nie powrotu po awarii do hosta funkcji Hyper-V.


- Przed kontynuowaniem wykonaj kroki ponowne objęcie ochroną, tak aby maszyny wirtualne są w stanie replikowanych i można uruchomić tryb failover do lokacji lokalnej. Aby uzyskać więcej informacji, zobacz [jak ponownie włączyć ochronę z platformy Azure do środowiska lokalnego](vmware-azure-reprotect.md).

- Upewnij się, że serwer vCenter jest niepodłączony przed wykonaniem powrotu po awarii. W przeciwnym razie nie działa odłączanie dysków i dołączania ich do maszyny wirtualnej.

- Podczas pracy w trybie failover na platformie Azure lokalnej lokacji może nie być dostępny, a serwer konfiguracji mogą być niedostępne lub wyłączone w dół. Podczas ponownego włączania ochrony i powrotu po awarii na lokalnym serwerze konfiguracji powinna być uruchomiona i niepodłączony OK. 

- Podczas powrotu po awarii maszyny wirtualnej musi istnieć w bazie danych serwera konfiguracji lub powrotu po awarii nie powiodła się. Upewnij się, korzystać z regularnie zaplanowanej kopii zapasowych serwera. W przypadku poważnej awarii, należy przywrócić na serwerze przy użyciu oryginalnego adresu IP w przypadku powrotu po awarii do pracy.

- Główny serwer docelowy nie powinna mieć wszystkie migawki przed włączeniem ponownej ochrony/powrotu po awarii.

## <a name="overview-of-failback"></a>Omówienie powrotu po awarii
Po za pośrednictwem powiodły się na platformie Azure, można powrotu po awarii do lokacji lokalnej, wykonując następujące czynności:

1. [Ponowne włączanie ochrony](vmware-azure-reprotect.md) maszyn wirtualnych na platformie Azure, aby zaczynają replikowane do maszyn wirtualnych programu VMware w lokacji lokalnej. W ramach tego procesu trzeba będzie również:

    * Konfigurowanie głównego elementu docelowego w środowisku lokalnym. Użyj Windows główny element docelowy dla maszyn wirtualnych Windows i [Linux główny serwer docelowy](vmware-azure-install-linux-master-target.md) dla maszyn wirtualnych systemu Linux.
    * Konfigurowanie [serwera przetwarzania](vmware-azure-set-up-process-server-azure.md).
    * Rozpocznij [ponownie włączyć ochronę](vmware-azure-reprotect.md) Wyłącz maszynę wirtualną w środowisku lokalnym i synchronizowanie danych maszyny wirtualnej platformy Azure z dyskami w środowisku lokalnym.

2. Po replikacji maszyn wirtualnych na platformie Azure do lokacji lokalnej, możesz rozpocząć pracy w trybie failover z platformy Azure, do lokacji lokalnej.

3. Po danych ponownie nie powiedzie się, możesz ponownie włączyć ochronę maszyn wirtualnych w środowisku lokalnym ponownie, aby rozpoczęcia replikacji do platformy Azure.

Aby uzyskać szybki przegląd Obejrzyj poniższy film o powrót po awarii do lokacji lokalnej:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Instrukcje powrotu po awarii

> [!IMPORTANT]
> Przed rozpoczęciem powrotu po awarii, upewnij się, zostało zakończone ponownego włączania ochrony maszyn wirtualnych. Maszyny wirtualne powinny znajdować się w stanie chronionym i należy ich kondycję **OK**. Aby ponownie włączyć ochronę maszyn wirtualnych, przeczytaj [jak do ponownego włączenia ochrony](vmware-azure-reprotect.md).

1. Na stronie zreplikowanych elementów wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy, aby zaznaczyć **nieplanowany tryb Failover**.
2. W **Potwierdź tryb Failover**, sprawdź wybrano kierunek trybu failover (z platformy Azure). Następnie wybierz punkt odzyskiwania (najnowsze lub najnowszą spójna na poziomie aplikacji), którego chcesz użyć dla trybu failover. Spójnego punktu aplikacji znajduje się za najnowszego punktu w czasie i powoduje utratę danych.
3. Podczas pracy w trybie failover Usługa Site Recovery wyłącza maszyny wirtualne na platformie Azure. Po sprawdzeniu, że powrót po awarii zakończone zgodnie z oczekiwaniami, możesz sprawdzić, czy wyłączona maszyn wirtualnych na platformie Azure.
4. **Zatwierdź** jest wymagane do usunięcia maszyny wirtualnej w trybie Failover na platformie Azure. Kliknij prawym przyciskiem myszy chronionego elementu, a następnie wybierz **zatwierdzić**. Zadanie powoduje usunięcie maszyn wirtualnych w trybie Failover na platformie Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Do jakich punktu odzyskiwania można można wykonać powrotu po awarii maszyn wirtualnych?

Podczas powrotu po awarii masz dwie opcje do planu odzyskiwania/na maszynie wirtualnej powrotu po awarii.

- Jeśli wybierzesz najnowszy przetworzony punkt w czasie, wszystkie maszyny wirtualne tryb failover do ostatniego dostępnego punktu w czasie. W przypadku grupy replikacji w ramach planu odzyskiwania, poszczególnych maszyn wirtualnych w grupie replikacji nie za pośrednictwem jego niezależnie od ostatniego punktu w czasie.

  Nie można przerwać ponownie maszynę wirtualną, dopóki nie ma ona co najmniej jeden punkt odzyskiwania. Nie można przerwać ponownie plan odzyskiwania do momentu jej maszyny wirtualne mają co najmniej jednego punktu odzyskiwania.

  > [!NOTE]
  > Najnowszy punkt odzyskiwania jest punktu odzyskiwania spójnego na poziomie awarii.

- Jeśli wybierzesz punktu odzyskiwania spójnego z aplikacją, pojedynczej maszyny wirtualnej powrotu po awarii są przywracane do jego najnowszego dostępnego spójna na poziomie aplikacji punktu odzyskiwania. W przypadku planu odzyskiwania z grupą replikacji każdej grupy replikacji są przywracane do jego wspólnego punktu odzyskiwania.
Punktów odzyskiwania zapewniających spójność aplikacji może być starsza w czasie, a mogą powodować utraty danych.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co się dzieje podczas powrotu po awarii programu VMware narzędzia wpis?

W przypadku maszyny wirtualnej Windows usługa Site Recovery wyłącza narzędzi VMware podczas pracy awaryjnej. Podczas powrotu po awarii maszyny wirtualnej Windows są reenabled narzędzi VMware. 


## <a name="reprotect-from-on-premises-to-azure"></a>Ponowne włączanie ochrony ze środowiska lokalnego do platformy Azure
Po zakończeniu powrotu po awarii i uruchomieniu zatwierdzenia, są usuwane odzyskanej maszyny wirtualne na platformie Azure. Teraz maszyna wirtualna jest ponownie w lokacji lokalnej, ale nie będą one chronione. Aby uruchomić ponownie zreplikowana na platformę Azure, wykonaj następujące czynności:

1. Wybierz **magazynu** > **ustawienie** > **zreplikowane elementy**wybierz maszyny wirtualne, które powrót po awarii, a następnie wybierz pozycję  **Włącz ponownie ochronę**.
2. Wprowadź wartość serwera przetwarzania, który musi być używane do wysyłania danych do platformy Azure.
3. Wybierz **OK** aby rozpocząć zadanie włączania ponownej ochrony.

> [!NOTE]
> Po rozruchu maszyny wirtualnej w środowisku lokalnym, dopiero po pewnym czasie dla agenta zarejestrować serwer konfiguracji (maksymalnie 15 minut). W tym czasie ponowne objęcie ochroną zakończy się niepowodzeniem i zwraca komunikat o błędzie informujący, że agent nie jest zainstalowany. Poczekaj kilka minut, a następnie spróbuj ponownie ponowne objęcie ochroną.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu zadania ponownego włączania ochrony są replikowane maszyny wirtualnej do platformy Azure, a można wykonać [trybu failover](site-recovery-failover.md) ponownie przenoszenia maszyn wirtualnych na platformie Azure.


