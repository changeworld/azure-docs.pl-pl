---
title: Konfigurowanie odzyskiwania po awarii do platformy Azure na serwerach fizycznych na lokalnych z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwania po awarii do platformy Azure dla systemu Windows i Linux serwerów lokalnych, z usługą Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 207ff17f7b113bf4a94bb6c157cf53e7b1c46b45
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Konfigurowanie odzyskiwania po awarii do platformy Azure dla lokalnych serwerów fizycznych

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym samouczku przedstawiono sposób konfigurowania odzyskiwania po awarii lokalnego systemu Windows i Linux serwerów fizycznych do platformy Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie wymagań wstępnych platformy Azure i lokalnymi
> * Tworzenie magazynu usług odzyskiwania Site Recovery 
> * Ustaw źródła i docelowymi środowisk replikacji
> * Tworzenie zasad replikacji
> * Włącz replikację dla serwera

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Przeanalizuj informacje o [składnikach i architekturze scenariusza](physical-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że spełniają serwerów, które chcesz replikować [wymagania maszyny Wirtualnej Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Przygotuj Azure. Potrzebujesz subskrypcji platformy Azure, sieć wirtualną platformy Azure i konto magazynu.
- Przygotowanie konta do automatycznej instalacji usługi mobilności na każdym serwerze, który chcesz replikować.

> [!NOTE]
> Przed rozpoczęciem należy pamiętać, że po w tryb failover na platformie Azure, serwery fizyczne nie nie do fizycznych komputerów lokalnych. Możesz tylko w trybie do maszyn wirtualnych VMware. 


### <a name="set-up-an-azure-account"></a>Konfigurowanie konta platformy Azure

Firma Microsoft [konta Azure](http://azure.microsoft.com/).

- Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Dowiedz się więcej o [cenach usługi Site Recovery](site-recovery-faq.md#pricing)i uzyskać [szczegóły cennika](https://azure.microsoft.com/pricing/details/site-recovery/).
- Sprawdzić, które [są obsługiwane regiony](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery.

### <a name="verify-azure-account-permissions"></a>Sprawdź uprawnienia konta platformy Azure

Upewnij się, że konto Azure ma uprawnienia do replikacji maszyn wirtualnych na platformie Azure.

- Przegląd [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) trzeba replikować maszyny do platformy Azure.
- Sprawdź i zmodyfikuj [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) uprawnienia. 



### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Konfigurowanie [sieć platformy Azure](../virtual-network/quick-create-portal.md).

- Maszyny wirtualne platformy Azure są umieszczane w tej sieci, gdy są tworzone po pracy awaryjnej.
- Sieć powinna znajdować się w tym samym regionie co magazyn usług odzyskiwania


## <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Konfigurowanie [konto magazynu Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Usługa Site Recovery replikuje maszyny lokalnej do magazynu Azure. Maszyny wirtualne platformy Azure są tworzone z magazynu po pracy awaryjnej.
- Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
- Konto magazynu może być standard lub [premium](../virtual-machines/windows/premium-storage.md).
- Skonfigurowanie konta premium, należy również dodatkowe konta standardowego dla danych dziennika.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Musi być zainstalowana usługa mobilności na każdym serwerze, który chcesz replikować. Usługa Site Recovery automatycznie instaluje tej usługi, po włączeniu replikacji dla serwera. Aby automatycznie zainstalować, należy przygotować konta używanego do uzyskiwania dostępu do serwera usługi Site Recovery.

- Korzystając z domeny lub lokalnego konta
- Dla maszyn wirtualnych systemu Windows, jeśli nie używasz konta domeny, wyłącz kontroli dostępu użytkownika zdalnego na komputerze lokalnym. Aby to zrobić, w rejestrze, w obszarze **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, Dodaj wpis DWORD **LocalAccountTokenFilterPolicy**, o wartości 1.
- Aby dodać wpis rejestru, aby wyłączyć to ustawienie z interfejsu wiersza polecenia, wpisz:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Dla systemu Linux konto powinno być głównym urzędem certyfikacji na serwer źródłowy z systemem Linux.


## <a name="create-a-vault"></a>Tworzenie magazynu

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Wybierz cel ochrony

Wybierz elementy do replikacji i jej do replikacji.

1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu zasobów kliknij pozycję **Site Recovery** > **Przygotowanie infrastruktury** > **Cel ochrony**.
3. W **cel ochrony**, wybierz pozycję **do platformy Azure** > **nie zwirtualizowanych/inne**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Konfigurowanie serwera konfiguracji, zarejestruj go w magazynie i odnajdywanie maszyn wirtualnych.

1. Kliknij przycisk **lokacji odzyskiwania** > **przygotowanie infrastruktury** > **źródła**.
2. Jeśli nie masz serwera konfiguracji, kliknij przycisk **+ serwer konfiguracji**.
3. W **Dodaj serwer**, sprawdź, czy **serwera konfiguracji** pojawia się w **typ serwera**.
4. Pobierz plik instalacyjny instalacja Unified usługi Site Recovery.
5. Pobierz klucz rejestracji magazynu. Należy to po uruchomieniu Instalatora Unified. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Konfiguracja źródła](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Zarejestruj serwer konfiguracji w magazynie

Przed rozpoczęciem, wykonaj następujące czynności: 

- Na komputerze z serwerem konfiguracji, upewnij się, że zegar jest zsynchronizowany z [serwer czasu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Powinna być zgodna. Jeśli jest 15 minut przed lub za, Instalator może zakończyć się niepowodzeniem.
- Upewnij się, że komputer ma dostęp do tych adresów URL:       [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- Reguły zapory oparte na adresie IP powinna zezwalać na komunikację z platformą Azure.
- Zezwól na użycie [zakresów adresów IP centrum danych Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) oraz portu 443 protokołu HTTPS.
- Zezwól na użycie zakresów adresów IP dla regionu platformy Azure Twojej subskrypcji oraz regionu Zachodnie stany USA (służy do kontrolowania dostępu i zarządzania tożsamościami).

Uruchom Instalatora Unified jako administratora lokalnego, aby zainstalować serwer konfiguracji. Serwer przetwarzania i główny serwer docelowy są również instalowane domyślnie na serwerze konfiguracji.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po zakończeniu rejestracji serwer konfiguracji jest wyświetlany na **ustawienia** > **serwerów** strony w magazynie.


## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij kolejno pozycje **Przygotowywanie infrastruktury** > **Docelowa** i wybierz subskrypcję platformy Azure do użycia.
2. Określ model wdrożenia docelowego.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Środowisko docelowe](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Aby utworzyć nowe zasady replikacji, kliknij przycisk **infrastruktura usługi Site Recovery** > **zasady replikacji** > **+ zasad replikacji**.
2. W **Utwórz zasady replikacji**, określ nazwę zasady.
3. W **próg RPO**, określ limit punktu odzyskiwania celu (RPO). Ta wartość określa, jak często są tworzone punkty odzyskiwania danych. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
4. W **przechowywania punktu odzyskiwania**, określić czas (w godzinach) okna przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Do 24 godzin przechowywania jest obsługiwane na komputerach, replikowane do magazynu w warstwie premium i 72 godziny dla magazynu w warstwie standardowa.
5. W **częstotliwość migawek spójności aplikacji**, określ częstotliwość (w minutach) będą tworzone punkty odzyskiwana zawierające migawki spójne z aplikacjami. Kliknij przycisk **OK**, aby utworzyć zasady.

    ![Zasady replikacji](./media/physical-azure-disaster-recovery/replication-policy.png)


Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład, jeśli zasady replikacji jest **zasad rep** następnie zasady powrotu po awarii **rep zasad-powrotu po awarii** jest tworzony. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla każdego serwera.

- Usługa Site Recovery instaluje usługi mobilności, gdy replikacja jest włączona.
- Po włączeniu replikacji dla serwera może zająć 15 minut lub dłużej, aby zmiany zostały wprowadzone i wyświetlane w portalu.

1. Kliknij kolejno pozycje **Replikowanie aplikacji** > **Źródło**.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W **komputera typu**, wybierz pozycję **maszyn fizycznych**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W **docelowego**, wybierz subskrypcji i grupy zasobów, w którym chcesz utworzyć maszynach wirtualnych Azure po pracy awaryjnej. Wybierz model wdrażania, który ma być używany na platformie Azure (Zarządzanie zasobu lub classic).
6. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych. 
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn. 
9. W **maszyn fizycznych**i kliknij przycisk **+ maszyny fizycznej**. Określ nazwę i adres IP. Wybierz system operacyjny maszyny, które mają być replikowane. Trwa kilka minut, aż serwery odnalezione i wyświetlane. 
10. W **właściwości** > **skonfigurować właściwości**, wybierz konto, które będzie używane przez serwer przetwarzania Aby automatycznie zainstalować usługi mobilności na maszynie.
11. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji. 
12. Kliknij pozycję **Włącz replikację**. Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.


Do monitorowania serwerów, możesz dodać, można sprawdzić czas ostatniego odnalezionych ich w **serwery konfiguracji** > **ostatniego kontaktu w**. Aby dodać komputery bez oczekiwania na godziny zaplanowanego odnajdywania, zaznacz serwer konfiguracji (nie klikaj pozycji go) i kliknij przycisk **Odśwież**.

## <a name="next-steps"></a>Kolejne kroki

[Uruchom wyszczególniania odzyskiwania po awarii](tutorial-dr-drill-azure.md).
