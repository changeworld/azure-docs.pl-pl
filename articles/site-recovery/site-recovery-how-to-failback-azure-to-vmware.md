---
title: Jak się nie powieść platformy Azure z powrotem do programu VMware | Dokumentacja firmy Microsoft
description: Po przełączeniu maszyn wirtualnych na platformie Azure można zainicjować powrotu po awarii, aby przywrócić maszyn wirtualnych do lokalnego. Dowiedz się więcej czynności w celu wykonaj powrót po awarii.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: gauravd
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: nisoneji
ms.openlocfilehash: 50dccf6196b7affd3d21087f851b929d0e850f6d
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768473"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Nie powiodło się platformy Azure z lokacją lokalną

W tym artykule opisano, jak niepowodzenie wstecz maszyn wirtualnych z maszyn wirtualnych platformy Azure do środowiska lokalnego VMware. Postępuj zgodnie z instrukcjami w tym artykule, aby zakończyć się niepowodzeniem z powrotem z maszyn wirtualnych VMware lub systemem Windows lub Linux, serwerów fizycznych, po zostały one przejścia w tryb failover z lokalnej lokacji do Azure za pomocą [pracy awaryjnej w usłudze Site Recovery](site-recovery-failover.md) samouczka.

## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że znasz szczegółowe informacje o [różnego rodzaju powrotu po awarii](concepts-types-of-failback.md) i odpowiedniego ostrzeżenia.

> [!WARNING]
> Nie można wykonać powrotu po awarii po jednej [ukończone migracji](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), przenieść maszynę wirtualną do innej grupy zasobów lub usunąć maszyny wirtualnej platformy Azure. Wyłączenie ochrony maszyny wirtualnej, nie możesz powrotu po awarii.

> [!WARNING]
> Serwera fizycznego systemu Windows Server 2008 R2 z dodatkiem SP1, jeśli chroniony Failover na platformie Azure, nie można przełączyć ponownie.

> [!NOTE]
> Jeśli ma przejścia w tryb failover maszyny wirtualne VMware można powrotu po awarii do hosta funkcji Hyper-v.


- Przed kontynuowaniem należy wykonać kroki ponownej ochrony, aby maszyny wirtualne są w stanie zreplikowane, a można zainicjować trybu failover do lokacji lokalnej. Aby uzyskać więcej informacji, zobacz [jak włączyć ją ponownie z platformy Azure do środowiska lokalnego](site-recovery-how-to-reprotect.md).

- Upewnij się, że vCenter jest w stanie połączenia przed wykonaniem powrotu po awarii. W przeciwnym razie nie działa rozłączanie dysków i dołączać do maszyny wirtualnej.

- Podczas pracy w trybie failover na platformie Azure lokalnej lokacji może nie być dostępna i dlatego serwer konfiguracji mogą być niedostępne lub zamknięcie. Podczas ponownej ochrony i powrotu po awarii lokalnego serwera konfiguracji powinna być uruchomiona i jest niepodłączony OK. 

- Podczas powrotu po awarii maszyna wirtualna musi istnieć w bazie danych konfiguracji serwera lub powrotu po awarii nie powiodło się. W związku z tym upewnij się, wykonanie zaplanowanego tworzenia kopii zapasowych serwera. Wystąpił awarii, należy przywrócić działanie serwera przy użyciu oryginalnego adresu IP do powrotu po awarii do pracy.

- Główny serwer docelowy nie powinna mieć żadnych migawek przed wyzwalania ponownej ochrony/powrotu po awarii.

## <a name="overview-of-failback"></a>Omówienie powrotu po awarii
Po za pośrednictwem powiodły się do platformy Azure, użytkownik może nie powieść do lokacji sieci lokalnej, wykonując następujące czynności:

1. [Włącz ponownie ochronę](site-recovery-how-to-reprotect.md) maszyn wirtualnych na platformie Azure, aby uruchamiają są replikowane do maszyn wirtualnych VMware w lokacji lokalnej. W ramach tego procesu również należy:
    1. Skonfiguruj lokalny główny serwer docelowy: Windows głównego celu dla maszyny wirtualnej systemu Windows i [główny cel Linux](site-recovery-how-to-install-linux-master-target.md) dla maszyn wirtualnych systemu Linux.
    2. Konfigurowanie [serwera przetwarzania](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Zainicjuj [Wznów](site-recovery-how-to-reprotect.md). Spowoduje to wyłączyć na lokalnej maszynie wirtualnej i zsynchronizuj dane platformy Azure maszyny wirtualnej z dyskami lokalnymi.

1. Po replikowania maszyn wirtualnych na platformie Azure do lokacji sieci lokalnej, zainicjowaniu błędu za pośrednictwem z platformy Azure do lokacji lokalnej.

1. Po danych nie powiodło się ponownie, należy ponownie Obejmij ochroną lokalnych maszyn wirtualnych, aby rozpoczynały się replikację do platformy Azure.

Aby szybko zapoznać Obejrzyj film następujący temat powrót po awarii do lokacji lokalnej.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Kroki, aby wykonaj powrót po awarii

> [!IMPORTANT]
> Przed rozpoczęciem powrotu po awarii, upewnij się, że zostały wykonane ponowne włączenie ochrony maszyn wirtualnych. Maszyny wirtualne powinny znajdować się w stanie chronionym i ich kondycję powinna być **OK**. Aby Włącz ponownie ochronę maszyny wirtualnej, przeczytaj [jak włączyć ją ponownie](site-recovery-how-to-reprotect.md).

1. Na stronie zreplikowanych elementów wybierz maszynę wirtualną, a następnie kliknij prawym przyciskiem myszy, aby zaznaczyć **nieplanowany tryb Failover**.
2. W **potwierdzić trybu Failover**Sprawdź kierunek pracy awaryjnej (na platformie Azure), a następnie wybierz punkt odzyskiwania (Najnowsza lub najnowszą wersję aplikacji spójne), który ma być używany do pracy awaryjnej. Punkt spójne aplikacji znajduje się za ostatniego punktu w czasie i powoduje utratę danych.
3. Podczas pracy w trybie failover Usługa Site Recovery zamyka maszyny wirtualne na platformie Azure. Po sprawdzeniu, że powrót po awarii została ukończona, zgodnie z oczekiwaniami, można sprawdzić, zamknij maszyny wirtualne na platformie Azure.
4. **Zatwierdź** jest wymagana, nie można usunąć przez maszynę wirtualną z Azure.Right kliknięciem chronionego elementu, a następnie kliknij przycisk **zatwierdzić**. Zadania spowoduje usunięcie nie powiodło się na maszynach wirtualnych platformy Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Do jakich punktu odzyskiwania można I wykonaj powrót po awarii maszyn wirtualnych?

Podczas powrotu po awarii dostępne są dwie opcje, aby powrócić planu maszyny wirtualnej do odzyskiwania danych.

- W przypadku wybrania najnowsze przetworzone punktu w czasie wszystkich maszyn wirtualnych będzie można przełączyć do ich najnowszego dostępnego punktu w czasie. W przypadku, grupy replikacji w ramach planu odzyskiwania, następnie każdej maszyny wirtualnej w grupie replikacji zostaną przełączone awaryjnie do jego niezależnie od ostatniego punktu w czasie.

    Nie można przerwać powrót maszyny wirtualnej do momentu składa się z co najmniej jeden punkt odzyskiwania. Nie można przerwać wstecz planu odzyskiwania, dopóki wszystkie maszyny wirtualne mają co najmniej jednego punktu odzyskiwania.

> [!NOTE]
> Najnowszy punkt odzyskiwania jest punkt odzyskiwania spójna w razie awarii.

- W przypadku wybrania punktu odzyskiwania na poziomie aplikacji, odzyskuje awarii jednej maszyny wirtualnej, do jego najnowszego punktu odzyskiwania zapewniających spójność aplikacji. W przypadku planu odzyskiwania z grupą replikacji każdej grupy replikacji odzyskuje do wspólnego punktu odzyskiwania.
Punktów odzyskiwania zapewniających spójność aplikacji może być za w czasie i może być utrata danych.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co się dzieje VMware narzędzia post powrotu po awarii?

W przypadku maszyny wirtualnej systemu Windows Azure Site Recovery wyłącza narzędzia VMware w trybie failover. Podczas powrotu po awarii maszyny wirtualnej systemu Windows narzędzia VMware są ponownie włączyć. 


## <a name="reprotect-from-on-premises-to-azure"></a>Włącz ponownie ochronę z lokalnej na platformie Azure
Po zakończeniu powrotu po awarii i zatwierdzeń zainicjowanych przez odzyskane maszynach wirtualnych platformy Azure są usuwane. Teraz maszyna wirtualna znajduje się do lokacji lokalnej, ale nie będzie można go chronić. Aby uruchomić ponownie replikację do platformy Azure, wykonaj następujące czynności:

1. W **magazynu** > **ustawienie** > **elementy replikowane**, wybierz maszyny wirtualne, które nie zostały ponownie, a następnie kliknij przycisk **ponownego włączenia ochrony**.
2. Należy podać wartość serwera przetwarzania, który ma służyć do wysyłania danych do usługi Azure.
3. Kliknij przycisk **OK** do rozpoczęcia zadań ponownej ochrony.

> [!NOTE]
> Po rozruchu maszyny wirtualnej lokalnymi, dopiero po pewnym czasie dla agenta zarejestrować serwer konfiguracji (maksymalnie 15 minut). W tym czasie ponownej ochrony kończy się niepowodzeniem i zwraca komunikat o błędzie informujący, że agent nie jest zainstalowany. Poczekaj kilka minut, a następnie spróbuj ponownie ponownej ochrony.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu zadania ponownej ochrony, maszyna wirtualna wykonuje replikację do platformy Azure i możesz zrobić [pracy awaryjnej](site-recovery-failover.md) ponownie przenoszenia maszyn wirtualnych na platformie Azure.


