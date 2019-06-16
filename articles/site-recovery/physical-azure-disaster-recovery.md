---
title: Konfigurowanie odzyskiwania po awarii na platformie Azure dla serwerów lokalnych z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla Windows i Linux serwerów lokalnych, za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: c3b9aa6fcf5cf96e3ef1f3bdd76e9f1d19be5c5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66400100"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla lokalnych serwerów fizycznych

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

Ten samouczek pokazuje, jak skonfigurować odzyskiwanie po awarii w środowisku lokalnym Windows i Linux serwerów fizycznych na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne platformy Azure i w środowisku lokalnym
> * Tworzenie magazynu usługi Recovery Services dla usługi Site Recovery 
> * Konfigurowanie źródła i docelowych środowisk replikacji
> * Tworzenie zasad replikacji
> * Włączanie replikacji dla serwera

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Upewnij się, że rozumiesz [składnikach i architekturze](physical-azure-architecture.md) dla tego scenariusza.
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że chcesz replikować serwery spełniają [wymagania maszyny Wirtualnej platformy Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Przygotowywanie platformy Azure. Potrzebujesz subskrypcji platformy Azure, siecią wirtualną platformy Azure i konto magazynu.
- Przygotowywanie konta do automatycznej instalacji usługi mobilności na każdym serwerze, który chcesz replikować.

Przed rozpoczęciem należy pamiętać, że:

- Po przejściu w tryb failover na platformie Azure nie może serwerów fizycznych do fizycznych komputerów w środowisku lokalnym. Można tylko powrotu po awarii do maszyn wirtualnych VMware. 
- W tym samouczku konfiguruje serwer fizyczny odzyskiwania po awarii na platformie Azure przy użyciu ustawień najprostszym. Jeśli chcesz dowiedzieć się więcej na temat innych opcji, zapoznaj się z artykułem nasze wskazówki How to:
    - Konfigurowanie [źródła replikacji](physical-azure-set-up-source.md), łącznie z serwera konfiguracji usługi Site Recovery.
    - Konfigurowanie [miejsca docelowego replikacji](physical-azure-set-up-target.md).
    - Konfigurowanie [zasad replikacji](vmware-azure-set-up-replication.md) i [włączanie replikacji](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Konfigurowanie konta platformy Azure

Pobieranie programu Microsoft [konta platformy Azure](https://azure.microsoft.com/).

- Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Dowiedz się więcej o [cenach usługi Site Recovery](site-recovery-faq.md#pricing)i Uzyskaj [cennik](https://azure.microsoft.com/pricing/details/site-recovery/).
- Dowiedz się, które [regiony są obsługiwane](https://azure.microsoft.com/pricing/details/site-recovery/) usługi Site Recovery.

### <a name="verify-azure-account-permissions"></a>Sprawdzanie uprawnień konta platformy Azure

Upewnij się, że Twoje konto platformy Azure ma uprawnienia do replikacji maszyn wirtualnych na platformie Azure.

- Przegląd [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) wymaganą do replikowania maszyn na platformę Azure.
- Sprawdź i zmodyfikuj [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) uprawnienia. 



### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Konfigurowanie [sieci platformy Azure](../virtual-network/quick-create-portal.md).

- Maszyny wirtualne platformy Azure są umieszczane w tej sieci po ich utworzeniu po włączeniu trybu failover.
- Sieć powinna znajdować się w tym samym regionie co magazyn usługi Recovery Services


## <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Konfigurowanie [konta usługi Azure storage](../storage/common/storage-quickstart-create-account.md).

- Usługa Site Recovery replikuje maszyn lokalnych do usługi Azure storage. Maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu do trybu failover.
- Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdym serwerze, który chcesz replikować. Usługa Site Recovery automatycznie instaluje tę usługę po włączeniu replikacji dla serwera. Aby automatycznie zainstalować, należy przygotować konta, które Site Recovery będzie uzyskiwać dostęp do serwera.

- Można użyć konta domeny lub lokalnego
- For Windows VMs, jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej. Aby to zrobić, w kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, Dodaj wpis DWORD **LocalAccountTokenFilterPolicy**, o wartości 1.
- Aby dodać wpis rejestru, aby wyłączyć to ustawienie z interfejsu wiersza polecenia, wpisz:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- W przypadku systemu Linux konto powinno być użytkownikiem głównym na serwerze źródłowym w systemie Linux.


## <a name="create-a-vault"></a>Tworzenie magazynu

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Wybieranie celu ochrony

Wybierz, co do replikacji i jej do replikacji.

1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu zasobów kliknij pozycję **Site Recovery** > **Przygotowanie infrastruktury** > **Cel ochrony**.
3. W **cel ochrony**, wybierz opcję **na platformę Azure** > **bez wirtualizacji/inne**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Konfigurowanie serwera konfiguracji, zarejestruj go w magazynie i odnajdywanie maszyn wirtualnych.

1. Kliknij przycisk **Site Recovery** > **przygotowanie infrastruktury** > **źródła**.
2. Jeśli nie masz serwera konfiguracji, kliknij przycisk **+ serwer konfiguracji**.
3. W **Dodaj serwer**, sprawdź, czy **serwera konfiguracji** pojawia się w **typ serwera**.
4. Pobierz plik instalacyjny Site Recovery Unified Setup.
5. Pobierz klucz rejestracji magazynu. Jest to potrzebne po uruchomieniu ujednoliconego Instalatora. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Konfiguracja źródła](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Rejestrowanie serwera konfiguracji w magazynie

Przed rozpoczęciem, wykonaj następujące czynności: 

#### <a name="verify-time-accuracy"></a>Sprawdź czas dokładności
Na komputerze z serwerem konfiguracji, upewnij się, że zegar systemowy jest zsynchronizowany z [serwer czasu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Powinien on odpowiadać. Jeśli jest 15 minut z przodu lub tyłu, instalacja może zakończyć się niepowodzeniem.

#### <a name="verify-connectivity"></a>Zweryfikuj łączność
Upewnij się, że komputer ma dostęp do tych adresów URL, w zależności od używanego środowiska: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Reguły zapory oparte na adresie IP powinien zezwalać na komunikację do wszystkich adresów URL usługi Azure, które są wymienione powyżej za pośrednictwem portu HTTPS (443). Aby uprościć i ograniczać zakresy adresów IP, zalecane jest, że filtrowanie adresów URL można zrobić.

- **Adresy IP komercyjnych** — Zezwalaj na [zakresów adresów IP centrum danych Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)oraz portu HTTPS (443). Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji, do obsługi usługi AAD, kopia zapasowa, replikacji i adresy URL magazynu.  
- **Dla instytucji rządowych adresy IP** — Zezwalaj na [zakresów IP centrum danych platformy Azure dla instytucji rządowych](https://www.microsoft.com/en-us/download/details.aspx?id=57063)oraz portu HTTPS (443) we wszystkich regionach USGov (Wirginia, Teksasie, Arizona i Iowa) do obsługi usługi AAD, kopia zapasowa, replikacji i adresy URL magazynu.  

#### <a name="run-setup"></a>Uruchamianie instalatora
Uruchom ujednoliconego Instalatora jako administratora lokalnego, aby zainstalować serwer konfiguracji. Serwer przetwarzania i głównym serwerze docelowym są również instalowane domyślnie na serwerze konfiguracji.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po zakończeniu rejestracji serwer konfiguracji jest wyświetlany na **ustawienia** > **serwerów** strony w magazynie.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij kolejno pozycje **Przygotowywanie infrastruktury** > **Docelowa** i wybierz subskrypcję platformy Azure do użycia.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Cel](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Aby utworzyć nowe zasady replikacji, kliknij pozycję **Infrastruktura usługi Site Recovery** > **Zasady replikacji** >  **+Zasady replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Ta wartość określa częstość tworzenia punktów odzyskiwania danych. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
4. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie jest okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Przechowywanie do 24 godzin jest obsługiwane dla maszyn replikowanych do magazynu w warstwie Premium, zaś do 72 godzin dla magazynu w warstwie Standardowa.
5. W **częstotliwość migawek spójności aplikacji**, określić, jak często (w minutach) będą tworzone punkty odzyskiwana zawierające migawki spójne z aplikacjami. Kliknij przycisk **OK**, aby utworzyć zasady.

    ![Zasady replikacji](./media/physical-azure-disaster-recovery/replication-policy.png)


Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład, jeśli zasady replikacji **rep-policy** następnie zasady powrotu po awarii **rep-policy-failback** zostanie utworzony. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla każdego serwera.

- Usługa Site Recovery zainstaluje usługę mobilności, po włączeniu replikacji.
- Po włączeniu replikacji dla serwera, może to potrwać 15 minut lub dłużej, aby zmiany zaczęły obowiązywać i wyświetlane w portalu.

1. Kliknij kolejno pozycje **Replikowanie aplikacji** > **Źródło**.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W **typ maszyny**, wybierz opcję **maszyn fizycznych**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W **docelowej**, wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne platformy Azure po włączeniu trybu failover. Wybierz model wdrażania, który chcesz użyć na platformie Azure (Model Klasyczny lub zasobu).
6. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych. 
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn. 
9. W **maszyn fizycznych**i kliknij przycisk **+ maszyna fizyczna**. Określ nazwę i adres IP. Wybierz system operacyjny maszyny, którą chcesz replikować. Trwa kilka minut na serwerach, aby być wykrywane i wyświetlane. 
10. W **właściwości** > **skonfigurować właściwości**, wybierz konto które będzie używane przez serwer przetwarzania do automatycznego instalowania usługi mobilności na maszynie.
11. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji. 
12. Kliknij pozycję **Włącz replikację**. Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.


Aby monitorować serwery, które możesz dodać, można sprawdzić czas ostatniego odnalezienia ich w **serwery konfiguracji** > **ostatni kontakt**. W celu dodania maszyn, bez czekania na okres zaplanowane odnajdowanie, wyróżnij serwer konfiguracji (nie należy kliknąć ją) i kliknij przycisk **Odśwież**.

## <a name="next-steps"></a>Kolejne kroki

[Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md).
