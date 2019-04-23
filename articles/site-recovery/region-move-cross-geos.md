---
title: Przenoszenie maszyn wirtualnych IaaS platformy Azure między Azure dla instytucji rządowych i regionach świadczenia publicznej za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Umożliwia przenoszenie maszyn wirtualnych IaaS platformy Azure między Azure Government i regionach świadczenia publicznej usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: fe2620c7a07389b2a86d36420eadd2ef5883f5da
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013235"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Przenoszenie maszyn wirtualnych platformy Azure między Azure dla instytucji rządowych i regionach świadczenia publicznej 

Możesz chcieć przenieść maszyny wirtualne IaaS między Azure dla instytucji rządowych i regionach świadczenia publicznej w celu zwiększenia dostępności istniejących maszyn wirtualnych i ulepszanie możliwości zarządzania lub ze względu na zarządzanie zgodnie z opisem [tutaj](azure-to-azure-move-overview.md).

Oprócz używania usługi [Azure Site Recovery](site-recovery-overview.md) do zarządzania odzyskiwaniem maszyn lokalnych i maszyn wirtualnych platformy Azure po awarii oraz ich organizowania dla celów zapewniania ciągłości działania i odzyskiwania po awarii (BCDR, business continuity and disaster recovery) można ją stosować również do zarządzania przenoszeniem maszyn wirtualnych platformy Azure do regionu pomocniczego.       

W tym samouczku dowiesz się, jak przenieść maszyny wirtualne platformy Azure między Azure dla instytucji rządowych i regionach świadczenia publicznej przy użyciu usługi Azure Site Recovery. Aby przenieść maszyny wirtualne między par regionów, które nie są w tym samym klastrze geograficznych można rozszerzyć takie same. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Weryfikowanie wymagań wstępnych
> * Przygotowywanie źródłowych maszyn wirtualnych
> * Przygotowywanie regionu docelowego
> * Kopiowanie danych do regionu docelowego
> * Testowanie konfiguracji
> * Przenoszenie
> * Odrzucanie zasobów w regionie źródłowym

> [!IMPORTANT]
> W tym samouczku dowiesz się, jak przenieść maszyny wirtualne platformy Azure między Azure dla instytucji rządowych i regionach świadczenia publicznej lub między par regionów, które nie są obsługiwane przez rozwiązanie odzyskiwania po awarii w regularnych maszyn wirtualnych platformy Azure. W przypadku usługi pary regiony źródłowe i docelowe są [obsługiwane](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), skontaktuj się z tym [dokumentu](azure-to-azure-tutorial-migrate.md) na czas przeprowadzki. Jeśli zgodnie z wymaganiami potrzebna jest aby zwiększyć dostępność, przenosząc maszyn wirtualnych w zestawie do przypięte w strefie maszyn wirtualnych w różnych regionach dostępności, zapoznaj się z samouczkiem [tutaj](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Nie zaleca się ta metoda służy do konfigurowania odzyskiwania po awarii między parami nieobsługiwany region pary są zdefiniowane, pamiętając o opóźnieniu przesyłania danych, co jest szczególnie ważne w scenariuszu odzyskiwania po awarii.

## <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych

> [!NOTE]
> Upewnij się, że rozumiesz [składnikach i architekturze](physical-azure-architecture.md) dla tego scenariusza. Ta architektura będzie służyć do przenoszenia maszyn wirtualnych platformy Azure, **traktując maszyny wirtualne jako serwery fizyczne**.

- Zapoznaj się z [wymaganiami dotyczącymi obsługi](vmware-physical-secondary-support-matrix.md) wszystkich składników.
- Upewnij się, że chcesz replikować serwery spełniają [wymagania maszyny Wirtualnej platformy Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Przygotowywanie konta do automatycznej instalacji usługi mobilności na każdym serwerze, który chcesz replikować.

- Należy pamiętać, że po awarii do regionu docelowego na platformie Azure nie można bezpośrednio wykonywać awarii do regionu źródłowego. Należy skonfigurować replikację ponownie do obiektu docelowego.

### <a name="verify-azure-account-permissions"></a>Sprawdzanie uprawnień konta platformy Azure

Upewnij się, że Twoje konto platformy Azure ma uprawnienia do replikacji maszyn wirtualnych na platformie Azure.

- Przegląd [uprawnienia](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) wymaganą do replikowania maszyn na platformę Azure.
- Sprawdź i zmodyfikuj [dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) uprawnienia. 

### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Konfigurowanie docelowego [sieci platformy Azure](../virtual-network/quick-create-portal.md).

- Maszyny wirtualne platformy Azure są umieszczane w tej sieci po ich utworzeniu po włączeniu trybu failover.
- Sieć powinna znajdować się w tym samym regionie co magazyn usługi Recovery Services


### <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Konfigurowanie [konta usługi Azure storage](../storage/common/storage-quickstart-create-account.md).

- Usługa Site Recovery replikuje maszyn lokalnych do usługi Azure storage. Maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu do trybu failover.
- Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.


## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdym serwerze, który chcesz replikować. Usługa Site Recovery automatycznie instaluje tę usługę po włączeniu replikacji dla serwera. Aby automatycznie zainstalować, należy przygotować konta, które Site Recovery będzie uzyskiwać dostęp do serwera.

- Można użyć konta domeny lub lokalnego
- For Windows VMs, jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej. Aby to zrobić, w kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, Dodaj wpis DWORD **LocalAccountTokenFilterPolicy**, o wartości 1.
- Aby dodać wpis rejestru, aby wyłączyć to ustawienie z interfejsu wiersza polecenia, wpisz:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- W przypadku systemu Linux konto powinno być użytkownikiem głównym na serwerze źródłowym w systemie Linux.


## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli dane są kopiowane do miejsca docelowego przy użyciu usługi Site Recovery, usługa sama wybiera ten sam lub najbardziej zbliżony rozmiar dla docelowej maszyny wirtualnej.

3. Upewnij się, że utworzono zasób docelowy dla każdego składnika zidentyfikowanego w układzie sieci źródłowej. Jest to ważne upewnić się, że, Opublikuj cuttinga za pośrednictwem region docelowy, maszyny wirtualne mają wszystkie funkcje i funkcje, które masz w źródle.

    > [!NOTE]
    > Usługa Azure Site Recovery automatycznie odnajduje i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny wirtualnej. Można również wstępnie utworzyć sieć i przypisać ją do maszyny wirtualnej w ramach czynności włączania replikacji. Jednak pozostałe zasoby należy utworzyć ręcznie w regionie docelowym, jak wspomniano poniżej.

     Skorzystaj z poniższych dokumentów, aby utworzyć odpowiednie najczęściej używane zasoby sieciowe, na podstawie konfiguracji źródłowej maszyny wirtualnej.

    - [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Moduły równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Publiczny adres IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Informacje na temat innych składników sieciowych zawiera [dokumentacja](https://docs.microsoft.com/azure/#pivot=products&panel=network) sieci. 

4. Ręcznie [utwórz sieć nieprodukcyjną](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w regionie docelowym, jeśli chcesz przetestować konfigurację przed wykonaniem końcowej migracji do regionu docelowego. Ogranicza to do minimum zaburzenia działania środowiska produkcyjnego i jest zalecane.

## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Poniżej przedstawiono procedurę kopiowania danych do regionu docelowego przy użyciu usługi Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Magazyn można utworzyć w dowolnym regionie, z wyjątkiem regionu źródłowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij kolejno pozycje **Utwórz zasób** > **Narzędzia do zarządzania** > **Backup i Site Recovery**.
3. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz odpowiednią z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Magazyny usługi Recovery Services kliknij **Przegląd** > **ConsotoVMVault** > **+ Replikuj**
7. Wybierz **na platformę Azure** > **bez wirtualizacji/inne**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Skonfiguruj serwer konfiguracji do odnalezienia maszyn wirtualnych.


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

### <a name="configure-target-settings-for-replication"></a>Skonfigurować ustawienia środowiska docelowego dla replikacji

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij kolejno pozycje **Przygotowywanie infrastruktury** > **Docelowa** i wybierz subskrypcję platformy Azure do użycia.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Środowisko docelowe](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Aby utworzyć nowe zasady replikacji, kliknij pozycję **Infrastruktura usługi Site Recovery** > **Zasady replikacji** > **+Zasady replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Ta wartość określa częstość tworzenia punktów odzyskiwania danych. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
4. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie jest okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie. Przechowywanie do 24 godzin jest obsługiwane dla maszyn replikowanych do magazynu w warstwie Premium, zaś do 72 godzin dla magazynu w warstwie Standardowa.
5. W **częstotliwość migawek spójności aplikacji**, określić, jak często (w minutach) będą tworzone punkty odzyskiwana zawierające migawki spójne z aplikacjami. Kliknij przycisk **OK**, aby utworzyć zasady.

    ![Zasady replikacji](./media/physical-azure-disaster-recovery/replication-policy.png)


Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład, jeśli zasady replikacji **rep-policy** następnie zasady powrotu po awarii **rep-policy-failback** zostanie utworzony. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

### <a name="enable-replication"></a>Włączanie replikacji

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

   > [!WARNING]
   > Należy wprowadzić adres IP maszyny Wirtualnej platformy Azure, które zamierzają przenieść

10. W **właściwości** > **skonfigurować właściwości**, wybierz konto które będzie używane przez serwer przetwarzania do automatycznego instalowania usługi mobilności na maszynie.
11. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji. 
12. Kliknij pozycję **Włącz replikację**. Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.


Aby monitorować serwery, które możesz dodać, można sprawdzić czas ostatniego odnalezienia ich w **serwery konfiguracji** > **ostatni kontakt**. W celu dodania maszyn, bez czekania na okres zaplanowane odnajdowanie, wyróżnij serwer konfiguracji (nie należy kliknąć ją) i kliknij przycisk **Odśwież**.

## <a name="test-the-configuration"></a>Testowanie konfiguracji


1. Przejdź do magazynu, w obszarze **Ustawienia** > **Zreplikowane elementy** kliknij maszynę wirtualną, którą chcesz przenieść do regionu docelowego, a następnie kliknij ikonę **+Test pracy w trybie failover**.
2. W obszarze **Test pracy w trybie failover** wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

   > [!IMPORTANT]
   > Zalecamy użycie oddzielnej sieci maszyn wirtualnych platformy Azure do testowania trybu failover, a nie sieci produkcyjnej, do którego chcesz przenieść maszyny wirtualne po pewnym czasie, które zostały skonfigurowane podczas włączania replikacji.

4. Aby rozpocząć testowanie przenoszenia, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę wirtualną utworzoną podczas testowania przeniesienia, kliknij pozycję **Wyczyść test pracy w trybie failover** replikowanego elementu. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Wykonaj przeniesienie do regionu docelowego i potwierdź.

1. Przejdź do magazynu, w obszarze **Ustawienia** > **Replikowane elementy** kliknij maszynę wirtualną, a następnie kliknij pozycję **Tryb failover**.
2. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover. 
4. Po ukończeniu zadania sprawdź, czy maszyna wirtualna jest wyświetlana w regionie docelowym platformy Azure zgodnie z oczekiwaniami.
5. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Zaczekaj na ukończenie zadania zatwierdzania.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym 

- Przejdź do maszyny wirtualnej.  Kliknij pozycję **Wyłącz replikację**.  Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.  

   > [!IMPORTANT]
   > Wykonanie powyższego kroku jest ważne w celu uniknięcia naliczenia opłaty za replikację przy użyciu usługi ASR.

Jeśli nie zamierzasz już używać zasobów źródłowych, przejdź do następnego zestawu kroków.

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym wyszczególnione w kroku 4 w sekcji [Przygotowywanie źródłowych maszyn wirtualnych](#prepare-the-source-vms) 
2. Usuń odpowiednie konto magazynu w regionie źródłowym.



## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przeniesiono maszynę wirtualną platformy Azure do innego regionu świadczenia usługi Azure. Teraz możesz skonfigurować odzyskiwanie po awarii dla przeniesionej maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)
