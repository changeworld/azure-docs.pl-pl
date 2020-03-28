---
title: Przenoszenie maszyn wirtualnych platformy Azure między regionami rządowymi a publicznymi za pomocą usługi Azure Site Recovery
description: Użyj usługi Azure Site Recovery, aby przenieść maszyny wirtualne platformy Azure między regionami rządowymi platformy Azure a regionami publicznymi.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: acaf16e7469b3ea4e5e391db91e37dc76be3b261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298534"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Przenoszenie maszyn wirtualnych platformy Azure między usługą Azure Government i regionami publicznymi 

Możesz przenieść maszyny wirtualne IaaS między regionami azure government i public, aby zwiększyć dostępność istniejących maszyn wirtualnych, poprawić możliwości zarządzania lub ze względów nadzoru, jak opisano [tutaj.](azure-to-azure-move-overview.md)

Oprócz używania usługi [Azure Site Recovery](site-recovery-overview.md) do zarządzania odzyskiwaniem maszyn lokalnych i maszyn wirtualnych platformy Azure po awarii oraz ich organizowania dla celów zapewniania ciągłości działania i odzyskiwania po awarii (BCDR, business continuity and disaster recovery) można ją stosować również do zarządzania przenoszeniem maszyn wirtualnych platformy Azure do regionu pomocniczego.       

W tym samouczku pokazano, jak przenieść maszyny wirtualne platformy Azure między regionami platformy Azure dla instytucji rządowych i publicznych przy użyciu usługi Azure Site Recovery. To samo można rozszerzyć, aby przenieść maszyny wirtualne między parami regionów, które nie znajdują się w tym samym klastrze geograficznym. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Weryfikowanie wymagań wstępnych
> * Przygotowywanie źródłowych maszyn wirtualnych
> * Przygotowywanie regionu docelowego
> * Kopiowanie danych do regionu docelowego
> * Testowanie konfiguracji
> * Przenoszenie
> * Odrzucanie zasobów w regionie źródłowym

> [!IMPORTANT]
> W tym samouczku pokazano, jak przenieść maszyny wirtualne platformy Azure między regionami rządowymi i publicznymi platformy Azure lub między parami regionów, które nie są obsługiwane przez zwykłe rozwiązanie do odzyskiwania po awarii dla maszyn wirtualnych platformy Azure. W przypadku, gdy [obsługiwane](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)są pary regionów źródłowych i docelowych, zapoznaj się z tym [dokumentem,](azure-to-azure-tutorial-migrate.md) aby uzyskać przeniesienie. Jeśli wymaga się poprawy dostępności poprzez przeniesienie maszyn wirtualnych w dostępności ustawionej na maszyny wirtualne przypięte do strefy w innym regionie, zapoznaj się z samouczkiem [tutaj](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Nie zaleca się używania tej metody do konfigurowania odzyskiwania po awarii między nieobsługiwanymi parami regionów, ponieważ pary są zdefiniowane, pamiętając o opóźnieniu danych, co ma kluczowe znaczenie dla scenariusza odzyskiwania po awarii.

## <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych

> [!NOTE]
> Upewnij się, że rozumiesz [architektury i składników](physical-azure-architecture.md) dla tego scenariusza. Ta architektura będzie używana do przenoszenia maszyn wirtualnych platformy **Azure, traktując maszyny wirtualne jako serwery fizyczne.**

- Zapoznaj się z [wymaganiami dotyczącymi obsługi](vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że serwery, które chcesz replikować, są zgodne z [wymaganiami dotyczącymi maszyn wirtualnych platformy Azure.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
- Przygotuj konto do automatycznej instalacji usługi mobilności na każdym serwerze, który chcesz replikować.

- Należy zauważyć, że po przepełnienie po awarii do regionu docelowego na platformie Azure, nie można bezpośrednio wykonać po awarii z powrotem do regionu źródłowego. Trzeba będzie skonfigurować replikację ponownie do obiektu docelowego.

### <a name="verify-azure-account-permissions"></a>Weryfikowanie uprawnień konta platformy Azure

Upewnij się, że twoje konto platformy Azure ma uprawnienia do replikacji maszyn wirtualnych na platformie Azure.

- Przejrzyj [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) potrzebne do replikowania maszyn na platformie Azure.
- Weryfikuj i modyfikuj uprawnienia [dostępu oparte na rolach.](../role-based-access-control/role-assignments-portal.md) 

### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Skonfiguruj docelową [sieć platformy Azure](../virtual-network/quick-create-portal.md).

- Maszyny wirtualne platformy Azure są umieszczane w tej sieci, gdy są tworzone po przełączeniu w stan failover.
- Sieć powinna znajdować się w tym samym regionie co magazyn usług odzyskiwania


### <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Konfigurowanie [konta magazynu platformy Azure](../storage/common/storage-account-create.md).

- Usługa Site Recovery replikuje maszyny lokalne do magazynu platformy Azure. Maszyny wirtualne platformy Azure są tworzone z magazynu po przejściu w stan failover.
- Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.


## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa mobilności musi być zainstalowana na każdym serwerze, który ma zostać zreplikowany. Usługa Site Recovery instaluje tę usługę automatycznie po włączeniu replikacji serwera. Aby zainstalować się automatycznie, należy przygotować konto, którego program Site Recovery będzie używać do uzyskiwania dostępu do serwera.

- Możesz użyć domeny lub konta lokalnego
- Jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkownika zdalnego na komputerze lokalnym. Aby to zrobić, w rejestrze w obszarze **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, dodaj wpis DWORD **LocalAccountTokenFilterPolicy**, o wartości 1.
- Aby dodać wpis rejestru, aby wyłączyć ustawienie z interfejsu wiersza polecenia, wpisz:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- W przypadku Linuksa konto powinno być głównym źródłowym serwerem Linux.


## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli dane są kopiowane do miejsca docelowego przy użyciu usługi Site Recovery, usługa sama wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

3. Upewnij się, że utworzono zasób docelowy dla każdego składnika zidentyfikowanego w układzie sieci źródłowej. Jest to ważne, aby upewnić się, że po przecięciu do regionu docelowego, maszyny wirtualne mają wszystkie funkcje i funkcje, które miały w źródle.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie odnajduje i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny wirtualnej. Można również wstępnie utworzyć sieć i przypisać ją do maszyny wirtualnej w ramach czynności włączania replikacji. Jednak pozostałe zasoby należy utworzyć ręcznie w regionie docelowym, jak wspomniano poniżej.

     Skorzystaj z poniższych dokumentów, aby utworzyć odpowiednie najczęściej używane zasoby sieciowe, na podstawie konfiguracji źródłowej maszyny wirtualnej.

    - [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer)
    - [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    
    W przypadku innych składników sieci można znaleźć w [dokumentacji](https://docs.microsoft.com/azure/?pivot=products&panel=network)sieci .

4. Ręcznie [utwórz sieć nieprodukcyjną](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym, jeśli chcesz przetestować konfigurację przed wykonaniem końcowej migracji do regionu docelowego. Ogranicza to do minimum zaburzenia działania środowiska produkcyjnego i jest zalecane.

## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Poniżej przedstawiono procedurę kopiowania danych do regionu docelowego przy użyciu usługi Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się do**usługi odzyskiwania** [portalu](https://portal.azure.com) > Azure .
2. Kliknij **pozycję Utwórz kopię zapasową** > narzędzi do > **zarządzania zasobami****i odzyskiwanie witryny**.
3. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz odpowiednią z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. W magazynach usług odzyskiwania kliknij pozycję **Przegląd** > **ConsotoVMVault** > **+Replikacja**
7. Wybierz **opcję Azure** > **Nie jest zwirtualizowana/Inne**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Skonfiguruj serwer konfiguracji, aby odnajdywać maszyny wirtualne.


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

### <a name="configure-target-settings-for-replication"></a>Konfigurowanie ustawień docelowych replikacji

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij **pozycję Przygotuj** > **obiekt docelowy**infrastruktury i wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Środowisko docelowe](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Aby utworzyć nową zasadę replikacji, kliknij pozycję > Zasady replikacji **infrastruktury** > odzyskiwania**witryny****+Zasady replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Ta wartość określa, jak często tworzone są punkty odzyskiwania danych. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
4. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie jest okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Przechowywanie do 24 godzin jest obsługiwane dla maszyn replikowanych do magazynu w warstwie Premium, zaś do 72 godzin dla magazynu w warstwie Standardowa.
5. W **aplikacji spójne częstotliwości migawki**, określić, jak często (w minutach) punkty odzyskiwania zawierające migawki spójne z aplikacją będą tworzone. Kliknij przycisk **OK**, aby utworzyć zasady.

    ![Zasady replikacji](./media/physical-azure-disaster-recovery/replication-policy.png)


Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład jeśli zasady replikacji jest **rep-policy** następnie rep-policy-rep-policy-failback jest tworzony. **rep-policy-failback** Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

### <a name="enable-replication"></a>Włączanie replikacji

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

   > [!WARNING]
   > Musisz wprowadzić adres IP maszyny wirtualnej platformy Azure, którą zamierzasz przenieść

10. W **właściwościach** > **Konfiguracja właściwości**wybierz konto, które będzie używane przez serwer przetwarzania do automatycznego instalowania usługi mobilności na komputerze.
11. W **obszarze Ustawienia** > replikacji**Konfigurowanie ustawień replikacji**sprawdź, czy zaznaczono prawidłowe zasady replikacji. 
12. Kliknij pozycję **Włącz replikację**. Postęp zadania Włącz **ochronę** można śledzić w zadaniach**Odzyskiwanie witryny**Zadania**zadań** >  **zadań zadań ustawień** > . Po uruchomieniu zadania **Finalize Protection** maszyna jest gotowa do pracy awaryjnej.


Aby monitorować dodane serwery, można sprawdzić ostatnio odnaleziony czas dla nich w **serwerach** > konfiguracji**Ostatni kontakt w**. Aby dodać maszyny bez oczekiwania na zaplanowany czas odnajdywania, wyróżnij serwer konfiguracji (nie klikaj go) i kliknij przycisk **Odśwież**.

## <a name="test-the-configuration"></a>Testowanie konfiguracji


1. Przejdź do przechowalni w **obszarze Ustawienia** > **replikowane elementy**kliknij maszynę wirtualną, którą zamierzasz przenieść do regionu docelowego, kliknij ikonę **+Przetestuj tryb awaryjny.**
2. W obszarze **Test pracy w trybie failover** wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

   > [!IMPORTANT]
   > Zaleca się użycie oddzielnej sieci maszyn wirtualnych platformy Azure dla testowania trybu failover, a nie sieci produkcyjnej, do której chcesz przenieść maszyny wirtualne po pewnym czasie, który został skonfigurowany po włączeniu replikacji.

4. Aby rozpocząć testowanie przenoszenia, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę wirtualną utworzoną podczas testowania przeniesienia, kliknij pozycję **Wyczyść test pracy w trybie failover** replikowanego elementu. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Wykonaj przeniesienie do regionu docelowego i potwierdź.

1. Przejdź do przechowalni w **obszarze Ustawienia** > **replikowane elementy**kliknij maszynę wirtualną, a następnie kliknij pozycję Tryb **failover**.
2. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy awaryjnej można śledzić na stronie **Zadania.** 
4. Po ukończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w regionie docelowym platformy Azure zgodnie z oczekiwaniami.
5. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Zaczekaj na ukończenie zadania zatwierdzania.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym 

- Przejdź do maszyny wirtualnej.  Kliknij pozycję **Wyłącz replikację**.  Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.  

   > [!IMPORTANT]
   > Wykonanie powyższego kroku jest ważne w celu uniknięcia naliczenia opłaty za replikację przy użyciu usługi ASR.

Jeśli nie zamierzasz już używać zasobów źródłowych, przejdź do następnego zestawu kroków.

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym wyszczególnione w kroku 4 w sekcji [Przygotowywanie źródłowych maszyn wirtualnych](#prepare-the-source-vms) 
2. Usuń odpowiednie konto magazynu w regionie źródłowym.



## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono maszynę wirtualną platformy Azure do innego regionu świadczenia usługi Azure. Teraz możesz skonfigurować odzyskiwanie po awarii dla przeniesionej maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)
