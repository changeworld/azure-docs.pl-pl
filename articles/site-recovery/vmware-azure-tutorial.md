---
title: Konfigurowanie odzyskiwania po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/10/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: e3c645ea748834340a2e6cfb8d3e7e1b1e876dcf
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540848"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware

W tym artykule opisano sposób włączyć replikację dla maszyn wirtualnych VMware w środowisku lokalnym, na potrzeby odzyskiwania po awarii do platformy Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

Jest to trzeci samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych programu VMware w środowisku lokalnym. W poprzednim samouczku mamy [przygotowany w lokalnym środowisku VMware](vmware-azure-tutorial-prepare-on-premises.md) do odzyskiwania po awarii na platformie Azure.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj ustawienia replikacji źródła i serwera konfiguracji usługi Site Recovery w środowisku lokalnym.
> * Skonfiguruj ustawienia docelowego replikacji.
> * Tworzenie zasad replikacji.
> * Włącz replikację dla maszyny Wirtualnej VMware.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Szczegółowe informacje na ten temat można znaleźć w artykule w sekcji jak tabeli odzyskiwania lokacji zawartości.

## <a name="before-you-start"></a>Zanim rozpoczniesz

Wykonaj poprzednich samouczków:
1. Upewnij się, że masz [Konfigurowanie platformy Azure](tutorial-prepare-azure.md) do odzyskiwania po awarii programu VMware w środowisku lokalnym na platformie Azure.
2. Postępuj zgodnie z [te kroki](vmware-azure-tutorial-prepare-on-premises.md) przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania po awarii na platformie Azure.
3. W tym samouczku pokazujemy, jak być replikowane na jednej maszynie Wirtualnej. Jeśli wdrażasz wiele maszyn wirtualnych programu VMware należy użyć [narzędzie planista wdrażania](https://aka.ms/asr-deployment-planner). [Dowiedz się więcej](site-recovery-deployment-planner.md) na temat tego narzędzia.
4. Ten samouczek używa wielu opcji, które można zrobić inaczej:
    - Samouczka użyto szablonu usługi OVA, aby utworzyć serwer konfiguracji maszyny Wirtualnej VMware. Jeśli z jakiegoś powodu nie można tego zrobić, postępuj zgodnie z [w instrukcjach](physical-manage-configuration-server.md) ręcznego konfigurowania serwera konfiguracji.
    - W tym samouczku Usługa Site Recovery automatycznie pobiera i instaluje MySQL z serwerem konfiguracji. Jeśli wolisz, możesz skonfigurować go ręcznie zamiast tego. [Dowiedz się więcej](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Wybieranie celu ochrony

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu. W tym scenariuszu jest używany magazyn **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.



## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

W środowisku źródłowym należy pojedynczy, wysokiej dostępności na komputerze lokalnym do obsługi tych składników usługi Site Recovery w środowisku lokalnym:

- **Serwer konfiguracji**: Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
- **Serwer przetwarzania**: Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. Serwer przetwarzania instaluje także agenta usługi mobilności na maszynach wirtualnych, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie lokalnych maszyn wirtualnych programu VMware.
- **Główny serwer docelowy**: Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.


Wszystkie te składniki są zainstalowane na maszyn pojedynczym lokalnym, które jest znany jako *serwera konfiguracji*. Domyślnie, odzyskiwania po awarii programu VMware skonfigurujemy serwer konfiguracji jako Maszynę wirtualną VMware o wysokiej dostępności. Aby to zrobić, możesz pobrać przygotowany szablon Open Virtualization aplikacji (OVA) i zaimportuj szablon do programu VMware do utworzenia maszyny Wirtualnej. 

- Najnowszą wersję serwera konfiguracji jest dostępny w portalu. Możesz również pobrać go bezpośrednio [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Jeśli z jakiegoś powodu nie można użyć szablonu usługi OVA skonfigurować Maszynę wirtualną, postępuj zgodnie z [w instrukcjach](physical-manage-configuration-server.md) ręcznego konfigurowania serwera konfiguracji.
- Licencja dostarczane z szablonu pakietu OVF jest ocena prawidłowe przez 180 dni. Windows uruchomione na maszynie Wirtualnej musi być aktywowany z wymaganą licencję. 


### <a name="download-the-vm-template"></a>Pobieranie szablonu maszyny wirtualnej

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon OVF dla serwera konfiguracji.



## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware


1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić **Kreatora wdrażania szablonu OVF**. 

     ![Szablon OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Na stronie **Select source** (Wybierz źródło) wprowadź lokalizację pobranego szablonu OVF.
4. Na stronie **Review details** (Przejrzyj szczegóły) wybierz pozycję **Next** (Dalej).
5. Na stronach **Select name and folder** (Wybierz nazwę i folder) oraz **Select configuration** (Wybierz konfigurację) zaakceptuj ustawienia domyślne.
6. Aby uzyskać najlepszą wydajność, na stronie **Select storage** (Wybierz magazyn) wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Select virtual disk format** (Wybierz format dysku wirtualnego).
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Po osiągnięciu stanu **Gotowe do ukończenia** w celu skonfigurowania maszyny wirtualnej przy użyciu ustawień domyślnych wybierz pozycje **Power on after deployment** (Włącz po wdrożeniu) > **Finish** (Zakończ).

   > [!TIP]
   > Jeśli chcesz dodać więcej kart sieciowych, wyczyść pole **Power on after deployment** (Włącz po wdrożeniu) > **Finish** (Zakończ). Domyślnie szablon zawiera jedną kartę sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

Jeśli chcesz dodać dodatkową kartę Sieciową do serwera konfiguracji, należy go dodać, aby zarejestrować serwer w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Dalej**.
3. Wybierz typ karty i sieć. 
4. Aby nawiązać połączenie z wirtualną kartą sieciową po włączeniu maszyny wirtualnej, zaznacz pole **Połącz po włączeniu**. Wybierz pozycje **Next** > **Finish** (Dalej, Zakończ). Następnie wybierz przycisk **OK**.


## <a name="register-the-configuration-server"></a>Rejestrowanie serwera konfiguracji 

Po skonfigurowaniu serwera konfiguracji należy zarejestrować go w magazynie.

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Po pierwszym zalogowaniu w ciągu kilku sekund zostanie uruchomione narzędzie do konfiguracji usługi Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. W ciągu kilku sekund zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfiguracja ustawień i dodawanie serwera VMware

Zakończ konfigurowanie i rejestrowanie serwera konfiguracji. 


1. W Kreatorze konfiguracji serwera zarządzania zaznacz **Konfiguracja łączności**. Z list rozwijanych najpierw wybierz kartę Sieciową, używanego przez serwer w tworzonym przez proces odnajdywania i wypychanie instalacji usługi mobilności na maszynach źródła, a następnie wybierz kartę Sieciową, która korzysta z serwera konfiguracji dla łączności z platformą Azure. Następnie wybierz pozycję **Zapisz**. Nie można zmienić to ustawienie, po skonfigurowaniu go.
2. Na stronie **Wybierz magazyn usługi Recovery Services** wybierz swoją subskrypcję platformy Azure, grupę zasobów i magazyn.
3. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server. Jeśli serwer MySQL został umieszczony w ścieżce, ten krok zostanie pominięty.
4. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
5. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
6. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
7. Wprowadź poświadczenia użytkownika, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Upewnij się, że nazwa użytkownika i hasło są prawidłowe, oraz że użytkownik należy do grupy administratorów na maszynie wirtualnej, która ma być chroniona. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Kontynuuj**.
8. W obszarze **Skonfiguruj poświadczenia maszyny wirtualnej** wprowadź nazwę użytkownika i hasło, które będą używane do automatycznego instalowania usługi Mobility na maszynach wirtualnych po włączeniu replikacji.
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
9. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
10. Po zakończeniu rejestracji sprawdź w witrynie Azure Portal, czy serwer konfiguracji i serwer VMware są widoczne na stronie **Źródło** w magazynie. Następnie wybierz pozycję **OK**, aby skonfigurować ustawienia środowiska docelowego.


Po zarejestrowaniu serwera konfiguracji, Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

> [!NOTE]
> Zanim nazwa konta pojawi się w portalu, może minąć 15 minut lub więcej. Aby wykonać natychmiastową aktualizację, wybierz pozycje **Serwery konfiguracji** > ***nazwa serwera*** > **Odśwież serwer**.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Wybierz pozycje **Przygotowanie infrastruktury** > **Cel**. Wybierz subskrypcję platformy Azure, której chcesz użyć. Firma Microsoft korzysta z modelu Resource Manager.
2. Usługa Site Recovery sprawdza, czy masz jeden lub więcej sieci wirtualnych. Powinny one być dostępne po skonfigurowaniu składników Azure zgodnie z [pierwszym samouczkiem](tutorial-prepare-azure.md) z serii.

   ![Karta celu](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i wybierz pozycję **Wszystkie zasoby**.
2. Wybierz magazyn usług Recovery Services (**ContosoVMVault** w ramach tego samouczka).
3. Aby utworzyć zasady replikacji, wybierz pozycję **Infrastruktura usługi Site Recovery** > **Zasady replikacji** > **+Zasady replikacji**.
4. W obszarze **Tworzenie zasad replikacji** wprowadź nazwę zasad. W tym scenariuszu są używane zasady **VMwareRepPolicy**.
5. W polu **Wartość progowa celu punktu odzyskiwania** użyj domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
6. W polu **Przechowywanie punktu odzyskiwania** określ czas przechowywania każdego punktu odzyskiwania. Na potrzeby tego samouczka wybierz 72 godziny. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w oknie przechowywania.
7. W polu **Częstotliwość wykonywania migawek na poziomie aplikacji** określ, jak często są tworzone migawki na poziomie aplikacji. W tym scenariuszu jest używana wartość domyślna 60 minut. Wybierz pozycję **OK**, aby utworzyć zasady.

   ![Tworzenie zasad replikacji](./media/vmware-azure-tutorial/replication-policy.png)

- Zasady zostaną automatycznie skojarzone z serwerem konfiguracji.
- Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład jeśli zasady replikacji noszą nazwę **rep-policy**, zasady powrotu po awarii będą nosić nazwę **rep-policy-failback**. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla maszyn wirtualnych w następujący sposób:

1. Wybierz pozycje **Replikowanie aplikacji** > **Źródło**.
1. W polu **Źródło** wybierz pozycję **Lokalne**i wybierz serwer konfiguracji z listy **Lokalizacja źródła**.
1. W obszarze **Typ maszyny** wybierz pozycję **Maszyny wirtualne**.
1. W obszarze **vCenter/vSphere Hypervisor** wybierz hosta vSphere lub serwer vCenter zarządzający tym hostem.
1. Wybierz serwer przetwarzania (domyślnie instalowany na maszynie wirtualnej serwera konfiguracji). Następnie wybierz przycisk **OK**. Stan kondycji wszystkich serwerów procesu jest wskazywany zgodnie z zalecaną limity i inne parametry. Wybierz serwer przetwarzania działa prawidłowo. A [krytyczne](vmware-physical-azure-monitor-process-server.md#process-server-alerts) serwera przetwarzania nie może zostać wybrany. Możesz albo [Rozwiązywanie problemów i eliminowanie](vmware-physical-azure-troubleshoot-process-server.md) błędy **lub** Konfigurowanie [serwera przetwarzania skalowalnego w poziomie](vmware-azure-set-up-process-server-scale.md).
1. W obszarze **Cel** wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failover. Stosujemy model wdrażania korzystający z usługi Resource Manager. 
1. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
1. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wirtualnych, na których ma zostać włączona replikacja. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn.
1. W pozycji **Maszyny wirtualne** > **Wybierz maszyny wirtualne** wybierz każdą maszynę, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**. Jeśli nie jesteś w stanie widoku/wybrać dowolną określoną maszynę wirtualną [więcej](https://aka.ms/doc-plugin-VM-not-showing) o rozwiązaniu problemu.
1. W obszarze **Właściwości** > **Konfigurowanie właściwości** wybierz konto, które będzie używane przez serwer przetwarzania w celu automatycznego zainstalowania usługi mobilności na maszynie.
1. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji.
1. Wybierz pozycję **Włącz replikację**. Po włączeniu replikacji maszyny wirtualnej usługa Site Recovery instaluje usługę Mobility.
1. Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
1. Zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.
1. Aby monitorować dodawane maszyny wirtualne, możesz sprawdzić czas ostatniego odnalezienia maszyn wirtualnych w obszarze **Serwery konfiguracji** > **Ostatni kontakt**. Aby dodać maszyny wirtualne, nie czekając na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (nie wybieraj go), a następnie wybierz pozycję **Odśwież**.

## <a name="next-steps"></a>Kolejne kroki
Po włączeniu replikacji, uruchom awarii, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](site-recovery-test-failover-to-azure.md)
