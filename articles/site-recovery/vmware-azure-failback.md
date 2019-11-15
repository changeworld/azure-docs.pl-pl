---
title: Powrót po awarii maszyn wirtualnych VMware/serwerów fizycznych z platformy Azure za pomocą Azure Site Recovery
description: Informacje dotyczące powrotu po awarii do lokacji lokalnej po przejściu do trybu failover na platformie Azure, podczas odzyskiwania danych z maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084056"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Powrót po awarii maszyn wirtualnych VMware i serwerów fizycznych z platformy Azure do lokacji lokalnej

W tym artykule opisano sposób powrotu po awarii maszyn wirtualnych z usługi Azure Virtual Machines do lokalnego środowiska VMware. Postępuj zgodnie z instrukcjami w tym artykule, aby powrócić po awarii maszyn wirtualnych VMware lub serwerów fizycznych z systemem Windows/Linux po przełączeniu ich w tryb failover z lokacji lokalnej do platformy Azure za pomocą funkcji [przełączania w Azure Site Recovery w](site-recovery-failover.md) samouczku.

## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że zostały przeczytane szczegółowe informacje o [różnych typach powrotu po awarii](concepts-types-of-failback.md) i odpowiednich zastrzeżeniach.

> [!WARNING]
> Nie można przeprowadzić powrotu po awarii po zakończeniu [migracji](migrate-overview.md#what-do-we-mean-by-migration), przeniesieniu maszyny wirtualnej do innej grupy zasobów lub usunięciu maszyny wirtualnej platformy Azure. Wyłączenie ochrony maszyny wirtualnej nie powiedzie się.

> [!WARNING]
> Serwer fizyczny z systemem Windows Server 2008 R2 z dodatkiem SP1, w przypadku ochrony i przełączenia w tryb failover na platformę Azure nie może zostać przywrócony.

> [!NOTE]
> Jeśli maszyny wirtualne programu VMware zostały przełączone w tryb failover, nie można wrócić do hosta funkcji Hyper-V.


- Przed kontynuowaniem należy wykonać kroki ponownego włączania ochrony, aby maszyny wirtualne były w stanie zreplikowane i można było uruchomić tryb failover z powrotem do lokacji lokalnej. Aby uzyskać więcej informacji, zobacz [jak ponownie włączyć ochronę z platformy Azure do lokacji lokalnej](vmware-azure-reprotect.md).

- Przed przeprowadzeniem powrotu po awarii upewnij się, że program vCenter jest w stanie połączonym. W przeciwnym razie odłączanie dysków i dołączenie ich z powrotem do maszyny wirtualnej nie powiedzie się.

- Podczas pracy w trybie failover na platformie Azure lokacja lokalna może być niedostępna, a serwer konfiguracji może być niedostępny lub zamknięty. Podczas ponownego włączania ochrony i powrotu po awarii lokalny serwer konfiguracji powinien działać i być w stanie połączony prawidłowy. 

- Podczas powrotu po awarii maszyna wirtualna musi znajdować się w bazie danych serwera konfiguracji lub powrót po awarii nie powiedzie się. Upewnij się, że wykonywane są regularnie zaplanowane kopie zapasowe serwera. Jeśli wystąpi awaria, należy przywrócić serwer z oryginalnym adresem IP w celu poprawnego powrotu po awarii.

- Główny serwer docelowy nie powinien mieć żadnych migawek przed wyzwoleniem ponownej ochrony/powrotu po awarii.

## <a name="overview-of-failback"></a>Przegląd powrotu po awarii
Po przełączeniu w tryb failover na platformę Azure możesz wrócić do lokacji lokalnej, wykonując następujące czynności:

1. Ponownie [Włącz ochronę](vmware-azure-reprotect.md) maszyn wirtualnych na platformie Azure, aby rozpocząć replikację do maszyn wirtualnych VMware w lokacji lokalnej. W ramach tego procesu należy również:

    * Skonfiguruj lokalny główny serwer docelowy. Użyj głównego elementu docelowego systemu Windows dla maszyn wirtualnych z systemem Windows i [głównego elementu docelowego systemu Linux](vmware-azure-install-linux-master-target.md) dla maszyn wirtualnych z systemem Linux.
    * Skonfiguruj [serwer przetwarzania](vmware-azure-set-up-process-server-azure.md).
    * Uruchom ponownie [ochronę](vmware-azure-reprotect.md) , aby wyłączyć lokalną maszynę wirtualną i zsynchronizować dane maszyny wirtualnej platformy Azure z dyskami lokalnymi.

2. Po replikacji maszyn wirtualnych na platformie Azure do lokacji lokalnej należy uruchomić tryb failover z platformy Azure do lokacji lokalnej.

3. Po ponownym uruchomieniu danych ponownie Włącz ochronę lokalnych maszyn wirtualnych, aby umożliwić ich replikację do platformy Azure.

Aby zapoznać się z krótkim omówieniem, zobacz następujące wideo dotyczące sposobu powrotu po awarii do lokacji lokalnej:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Procedura powrotu po awarii

> [!IMPORTANT]
> Przed rozpoczęciem powrotu po awarii upewnij się, że zakończono ponowną ochronę maszyn wirtualnych. Maszyny wirtualne powinny znajdować się w stanie chronionym, a ich kondycja powinna mieć wartość **OK**. Aby ponownie włączyć ochronę maszyn wirtualnych, przeczytaj artykuł [jak ponownie włączyć ochronę](vmware-azure-reprotect.md).

1. Na stronie zreplikowane elementy wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy, aby wybrać **niezaplanowaną pracę w trybie failover**.
2. W obszarze **Potwierdź tryb failover**sprawdź kierunek trybu failover (z platformy Azure). Następnie wybierz punkt odzyskiwania (Najnowsza lub Najnowsza spójna aplikacja), którego chcesz użyć do przejścia w tryb failover. Punkt spójności aplikacji jest za ostatnim punktem w czasie i powoduje utratę danych.
3. Podczas pracy w trybie failover Site Recovery zamyka maszyny wirtualne na platformie Azure. Po sprawdzeniu, czy powrót po awarii został ukończony zgodnie z oczekiwaniami, możesz sprawdzić, czy maszyny wirtualne na platformie Azure zostały zamknięte.
4. **Zatwierdzenie** jest wymagane do usunięcia maszyny wirtualnej przełączonej w tryb failover z platformy Azure. Kliknij prawym przyciskiem myszy chroniony element, a następnie wybierz pozycję **Zatwierdź**. Zadanie usuwa maszyny wirtualne zakończone niepowodzeniem na platformie Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Do jakiego punktu odzyskiwania mogą kończyć się błędy maszyn wirtualnych?

Podczas powrotu po awarii dostępne są dwie opcje ponownego powrotu maszyny wirtualnej/planu odzyskiwania.

- W przypadku wybrania ostatniego przetworzonego punktu w czasie wszystkie maszyny wirtualne są przełączane w tryb failover do najnowszego dostępnego punktu w czasie. Jeśli w ramach planu odzyskiwania istnieje grupa replikacji, każda maszyna wirtualna grupy replikacji zostanie przełączona w tryb failover do jego niezależnego ostatniego punktu w czasie.

  Nie można przeprowadzić powrotu po awarii maszyny wirtualnej, dopóki nie ma ona co najmniej jednego punktu odzyskiwania. Nie można przeprowadzić powrotu po awarii planu odzyskiwania, dopóki wszystkie jego maszyny wirtualne nie mają co najmniej jednego punktu odzyskiwania.

  > [!NOTE]
  > Najnowszy punkt odzyskiwania to punkt odzyskiwania spójny na poziomie awarii.

- W przypadku wybrania punktu odzyskiwania spójnego na poziomie aplikacji pojedynczy powrót po awarii maszyny wirtualnej odzyska dostęp do najnowszego punktu odzyskiwania spójnego z aplikacją. W przypadku planu odzyskiwania z grupą replikacji każda grupa replikacji odzyskuje do wspólnego dostępnego punktu odzyskiwania.
Punkty odzyskiwania spójne z aplikacją mogą znajdować się w czasie i mogą ulec utracie dane.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co się stanie z narzędziami VMware po awarii?

W przypadku maszyny wirtualnej z systemem Windows Site Recovery wyłącza narzędzia VMware podczas pracy w trybie failover. Podczas powrotu po awarii maszyny wirtualnej z systemem Windows narzędzia VMware są ponownie włączane. 


## <a name="reprotect-from-on-premises-to-azure"></a>Ponowne włączanie ochrony z poziomu lokalnego na platformę Azure
Po zakończeniu powrotu po awarii i rozpoczęciu zatwierdzania odzyskane maszyny wirtualne na platformie Azure zostaną usunięte. Teraz maszyna wirtualna jest ponownie włączona w lokacji lokalnej, ale nie będzie chroniona. Aby ponownie rozpocząć replikację do platformy Azure, wykonaj następujące czynności:

1. Wybierz **ustawienie** > **magazynu** > **zreplikowane elementy**, wybierz maszyny wirtualne, które nie powiodło się, a następnie wybierz pozycję **Włącz ponownie ochronę**.
2. Wprowadź wartość serwera przetwarzania, która ma być używana do wysyłania danych z powrotem do platformy Azure.
3. Wybierz **przycisk OK** , aby rozpocząć zadanie ponownego włączania ochrony.

> [!NOTE]
> Po rozruchu lokalnej maszyny wirtualnej trwa jakiś czas na ponowne zarejestrowanie agenta na serwerze konfiguracji (do 15 minut). W tym czasie ponowna ochrona nie powiedzie się i zwróci komunikat o błędzie z informacją, że Agent nie jest zainstalowany. Poczekaj kilka minut, a następnie spróbuj ponownie włączyć ochronę.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu ponownego włączania ochrony maszyna wirtualna jest replikowana z powrotem do platformy Azure i można wykonać [tryb failover](site-recovery-failover.md) , aby ponownie przenieść maszyny wirtualne na platformę Azure.


