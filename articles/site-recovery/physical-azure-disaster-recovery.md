---
title: Konfigurowanie odzyskiwania po awarii fizycznych serwerów lokalnych za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych serwerów Windows i Linux za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 2f92c2b800c6d30cc5f365e6d24925a70d3db55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257929"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla lokalnych serwerów fizycznych

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym samouczku pokazano, jak skonfigurować odzyskiwanie po awarii lokalnych fizycznych serwerów Windows i Linux na platformie Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie platformy Azure i wymagań wstępnych lokalnych
> * Tworzenie magazynu usługi Recovery Services dla usługi Site Recovery 
> * Konfigurowanie środowisk replikacji źródłowej i docelowej
> * Tworzenie zasad replikacji
> * Włączanie replikacji serwera

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Upewnij się, że rozumiesz [architektury i składników](physical-azure-architecture.md) dla tego scenariusza.
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że serwery, które chcesz replikować, są zgodne z [wymaganiami dotyczącymi maszyn wirtualnych platformy Azure.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
- Przygotuj platformę Azure. Potrzebujesz subskrypcji platformy Azure, sieci wirtualnej platformy Azure i konta magazynu.
- Przygotuj konto do automatycznej instalacji usługi mobilności na każdym serwerze, który chcesz replikować.

Zanim zaczniesz, należy pamiętać, że:

- Po przewijaniu awaryjnym na platformie Azure serwerów fizycznych nie można powiodu z powrotem na lokalnych komputerach fizycznych. Powrót do maszyn wirtualnych VMware można przywrócić tylko w sieci. 
- W tym samouczku skonfigurowane odzyskiwanie po awarii serwera fizycznego na platformie Azure z najprostszymi ustawieniami. Jeśli chcesz dowiedzieć się więcej o innych opcjach, przeczytaj nasze przewodniki Jak:
    - Skonfiguruj [źródło replikacji,](physical-azure-set-up-source.md)w tym serwer konfiguracji odzyskiwania lokacji.
    - Konfigurowanie [miejsca docelowego replikacji](physical-azure-set-up-target.md).
    - Konfigurowanie [zasad replikacji](vmware-azure-set-up-replication.md) i [włączanie replikacji](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Konfigurowanie konta platformy Azure

Pobierz [konto platformy](https://azure.microsoft.com/)Microsoft Azure .

- Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Dowiedz się więcej o [cenach usługi Site Recovery](site-recovery-faq.md#pricing)i uzyskaj szczegóły dotyczące [cen.](https://azure.microsoft.com/pricing/details/site-recovery/)
- Dowiedz się, które [regiony są obsługiwane](https://azure.microsoft.com/pricing/details/site-recovery/) w celu odzyskiwania witryny.

### <a name="verify-azure-account-permissions"></a>Weryfikowanie uprawnień konta platformy Azure

Upewnij się, że twoje konto platformy Azure ma uprawnienia do replikacji maszyn wirtualnych na platformie Azure.

- Przejrzyj [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) potrzebne do replikowania maszyn na platformie Azure.
- Weryfikuj i modyfikuj uprawnienia [dostępu oparte na rolach.](../role-based-access-control/role-assignments-portal.md) 



### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Konfigurowanie [sieci platformy Azure](../virtual-network/quick-create-portal.md).

- Maszyny wirtualne platformy Azure są umieszczane w tej sieci, gdy są tworzone po przełączeniu w stan failover.
- Sieć powinna znajdować się w tym samym regionie co magazyn usług odzyskiwania


## <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Konfigurowanie [konta magazynu platformy Azure](../storage/common/storage-account-create.md).

- Usługa Site Recovery replikuje maszyny lokalne do magazynu platformy Azure. Maszyny wirtualne platformy Azure są tworzone z magazynu po przejściu w stan failover.
- Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa mobilności musi być zainstalowana na każdym serwerze, który ma zostać zreplikowany. Usługa Site Recovery instaluje tę usługę automatycznie po włączeniu replikacji serwera. Aby zainstalować się automatycznie, należy przygotować konto, którego program Site Recovery będzie używać do uzyskiwania dostępu do serwera.

- Możesz użyć domeny lub konta lokalnego
- Jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkownika zdalnego na komputerze lokalnym. Aby to zrobić, w rejestrze w obszarze **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, dodaj wpis DWORD **LocalAccountTokenFilterPolicy**, o wartości 1.
- Aby dodać wpis rejestru, aby wyłączyć ustawienie z interfejsu wiersza polecenia, wpisz:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- W przypadku Linuksa konto powinno być głównym źródłowym serwerem Linux.


## <a name="create-a-vault"></a>Tworzenie magazynu

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Wybieranie celu ochrony

Wybierz, co ma być replikowane, i zreplikować ją.

1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu Zasobów kliknij polecenie Przygotowanie**środowiska do** **odzyskiwania** > **witryny** > .
3. W **celu Ochrony**wybierz opcję Aby platforma **Azure** > nie była**zwirtualizowana/Inne**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Skonfiguruj serwer konfiguracji, zarejestruj go w przechowalni i odnajduj maszyny wirtualne.

1. Kliknij pozycję Źródło przygotowania infrastruktury **do odzyskiwania** > **witryny** > **.**
2. Jeśli nie masz serwera konfiguracji, kliknij przycisk **+Serwer konfiguracji**.
3. W **programie Add Server**sprawdź, czy serwer **konfiguracji** jest wyświetlany w **typie serwera**.
4. Pobierz plik instalacyjny ujednoliconej instalacji odzyskiwania witryny.
5. Pobierz klucz rejestracji magazynu. Jest to potrzebne po uruchomieniu Instalatora ujednoliconego. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Konfiguracja źródła](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Rejestrowanie serwera konfiguracji w przechowalni

Przed rozpoczęciem wykonaj następujące czynności: 

#### <a name="verify-time-accuracy"></a>Sprawdź dokładność czasu
Na komputerze serwera konfiguracji upewnij się, że zegar systemowy jest zsynchronizowany z [serwerem czasu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Powinno pasować. Jeśli jest to 15 minut z przodu lub z tyłu, konfiguracja może zakończyć się niepowodzeniem.

#### <a name="verify-connectivity"></a>Weryfikowanie łączności
Upewnij się, że urządzenie może uzyskać dostęp do tych adresów URL w zależności od środowiska: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Reguły zapory oparte na adresie IP powinny zezwalać na komunikację ze wszystkimi adresami URL platformy Azure, które są wymienione powyżej za pośrednictwem portu HTTPS (443). Aby uprościć i ograniczyć zakresy adresów IP, zaleca się filtrowanie adresów URL.

- **Komercyjne adresy IP** — zezwalaj na [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)i port HTTPS (443). Zezwalaj na zakresy adresów IP dla regionu platformy Azure subskrypcji do obsługi adresów URL usługi AAD, kopii zapasowej, replikacji i magazynu.  
- **Rządowe adresy IP** — zezwalaj na [zakresy adresów IP centrum danych platformy Azure dla instytucji Rządowych](https://www.microsoft.com/en-us/download/details.aspx?id=57063)oraz port HTTPS (443) dla wszystkich regionów USGov (Virginia, Texas, Arizona i Iowa) do obsługi adresów URL usług AAD, kopii zapasowych, replikacji i magazynu.  

#### <a name="run-setup"></a>Uruchamianie instalatora
Uruchom ujednoliconą instalację jako administrator lokalny, aby zainstalować serwer konfiguracji. Serwer przetwarzania i główny serwer docelowy są również instalowane domyślnie na serwerze konfiguracji.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po zakończeniu rejestracji serwer konfiguracji jest wyświetlany na stronie**Serwery** **ustawień** > w przechowalni.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij **pozycję Przygotuj** > **obiekt docelowy**infrastruktury i wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Środowisko docelowe](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Aby utworzyć nową zasadę replikacji, kliknij pozycję > Zasady replikacji **infrastruktury** > odzyskiwania**witryny****+Zasady replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Ta wartość określa, jak często tworzone są punkty odzyskiwania danych. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
4. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie jest okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Przechowywanie do 24 godzin jest obsługiwane dla maszyn replikowanych do magazynu w warstwie Premium, zaś do 72 godzin dla magazynu w warstwie Standardowa.
5. W **aplikacji spójne częstotliwości migawki**, określić, jak często (w minutach) punkty odzyskiwania zawierające migawki spójne z aplikacją będą tworzone. Kliknij przycisk **OK**, aby utworzyć zasady.

    ![Zasady replikacji](./media/physical-azure-disaster-recovery/replication-policy.png)


Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład jeśli zasady replikacji jest **rep-policy** następnie rep-policy-rep-policy-failback jest tworzony. **rep-policy-failback** Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla każdego serwera.

- Usługa Site Recovery zainstaluje usługę mobilności po włączeniu replikacji.
- Po włączeniu replikacji dla serwera może upłynąć 15 minut lub dłużej, aby zmiany zostały wprowadzone i pojawią się w portalu.

1. Kliknij pozycję **Replikuj** > **źródło**aplikacji .
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W **obszarze Typ maszyny**wybierz opcję Komputery **fizyczne**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W **obszarze Docelowy**wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne platformy Azure po przemienniu w stan failover. Wybierz model wdrażania, który ma być używany na platformie Azure (zarządzanie klasyczne lub zarządzanie zasobami).
6. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych. 
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz **pozycję Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienie sieciowe do wszystkich maszyn wybranych do ochrony. Wybierz **pozycję Konfiguruj później,** aby wybrać sieć platformy Azure na maszynę. 
9. W **programie Physical Machines**i kliknij **+Fizyczna maszyna**. Określ nazwę i adres IP. Wybierz system operacyjny komputera, który chcesz replikować. Trwa kilka minut, aby serwery zostały wykryte i wymienione. 
10. W **właściwościach** > **Konfiguracja właściwości**wybierz konto, które będzie używane przez serwer przetwarzania do automatycznego instalowania usługi mobilności na komputerze.
11. W **obszarze Ustawienia** > replikacji**Konfigurowanie ustawień replikacji**sprawdź, czy zaznaczono prawidłowe zasady replikacji. 
12. Kliknij pozycję **Włącz replikację**. Postęp zadania Włącz **ochronę** można śledzić w zadaniach**Odzyskiwanie witryny**Zadania**zadań** >  **zadań zadań ustawień** > . Po uruchomieniu zadania **Finalize Protection** maszyna jest gotowa do pracy awaryjnej.


Aby monitorować dodane serwery, można sprawdzić ostatnio odnaleziony czas dla nich w **serwerach** > konfiguracji**Ostatni kontakt w**. Aby dodać maszyny bez oczekiwania na zaplanowany czas odnajdywania, wyróżnij serwer konfiguracji (nie klikaj go) i kliknij przycisk **Odśwież**.

## <a name="next-steps"></a>Następne kroki

[Uruchom ćwiczenie odzyskiwania po awarii](tutorial-dr-drill-azure.md).
