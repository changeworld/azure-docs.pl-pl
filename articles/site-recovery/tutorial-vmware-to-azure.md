---
title: Konfigurowanie odzyskiwania po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cfb5e266a8454f1604e9e697100e89a5791368ab
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677536"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware

Z tego samouczka dowiesz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware z systemem Windows. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wprowadzanie źródła i celu replikacji.
> * Konfigurowanie środowiska źródłowego replikacji wraz z lokalnymi składnikami usługi Azure Site Recovery oraz środowiska docelowego replikacji.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

Jest to trzeci samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:

* [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
* [Przygotowywanie lokalnego wdrożenia oprogramowania VMware](tutorial-prepare-on-premises-vmware.md)

Przed rozpoczęciem warto [zapoznać się z architekturą](concepts-vmware-to-azure-architecture.md) scenariuszy odzyskiwania po awarii.


## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu: **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Aby skonfigurować środowisko źródłowe, potrzebna jest jedna maszyna lokalna o wysokiej dostępności, na której hostowane będą składniki lokalne usługi Site Recovery. Obejmują one serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy:

- Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage. Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.
- Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.

Aby skonfigurować serwer konfiguracji jako maszynę wirtualną VMware o wysokiej dostępności, należy pobrać przygotowany szablon Open Virtualization Format (OVF) i zaimportować go do programu VMware w celu utworzenia maszyny wirtualnej. Po skonfigurowaniu serwera konfiguracji należy zarejestrować go w magazynie. Po rejestracji usługa Site Recovery odnajduje lokalne maszyny wirtualne VMware.

### <a name="download-the-vm-template"></a>Pobieranie szablonu maszyny wirtualnej

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon OVF dla serwera konfiguracji.

  > [!TIP]
  Najnowszą wersję szablonu serwera konfiguracji można pobrać bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF. 

     ![Szablon OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. Na stronie **Select source** (Wybierz źródło) wprowadź lokalizację pobranego szablonu OVF.
4. Na stronie **Review details** (Przejrzyj szczegóły) wybierz pozycję **Next** (Dalej).
5. Na stronach **Select name and folder** (Wybierz nazwę i folder) oraz **Select configuration** (Wybierz konfigurację) zaakceptuj ustawienia domyślne.
6. Aby uzyskać najlepszą wydajność, na stronie **Select storage** (Wybierz magazyn) wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Select virtual disk format** (Wybierz format dysku wirtualnego).
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Ready to complete** (Gotowe do ukończenia):

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycje  **Power on after deployment** > **Finish** (Włącz po wdrożeniu, Zakończ).

    * Jeśli chcesz dodać więcej kart sieciowych, wyczyść pole **Power on after deployment** (Włącz po wdrożeniu). Następnie wybierz pozycję **Finish** (Zakończ). Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

Aby dodać kolejną kartę sieciową do serwera konfiguracji, zrób to przed zarejestrowaniem serwera w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edit Settings** (Edytuj ustawienia).
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Next** (Dalej).
3. Wybierz typ karty i sieć. 
4. Aby nawiązać połączenie z wirtualną kartą sieciową po włączeniu maszyny wirtualnej, zaznacz pole **Connect at power on** (Połącz po włączeniu). Wybierz pozycje **Next** > **Finish** (Dalej, Zakończ). Następnie wybierz przycisk **OK**.


## <a name="register-the-configuration-server"></a>Rejestrowanie serwera konfiguracji 

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Po pierwszym zalogowaniu zostanie uruchomione narzędzie do konfiguracji usługi Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

### <a name="configure-settings-and-connect-to-vmware"></a>Konfigurowanie ustawień i nawiązywanie połączenia z programem VMware

1. W kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Konfiguracja łączności**, a następnie wybierz kartę sieciową, która ma odbierać ruch związany z replikacją. Następnie wybierz pozycję **Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić.
2. Na stronie **Wybierz magazyn usługi Recovery Services** wybierz swoją subskrypcję platformy Azure, grupę zasobów i magazyn.
3. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server.
4. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
5. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
6. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
7. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Kontynuuj**.
8. W obszarze **Skonfiguruj poświadczenia maszyny wirtualnej** wprowadź nazwę użytkownika i hasło, które będą używane do automatycznego instalowania usługi mobilności na maszynach po włączeniu replikacji. W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach. W przypadku systemu Linux należy podać dane superużytkownika.
9. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**. 
10. Po zakończeniu rejestracji sprawdź w witrynie Azure Portal, czy serwer konfiguracji i serwer VMware są widoczne na stronie **Źródło** w magazynie. Następnie wybierz pozycję **OK**, aby skonfigurować ustawienia środowiska docelowego.


Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

> [!NOTE]
> Zanim nazwa konta pojawi się w portalu, może minąć 15 minut lub więcej. Aby wykonać natychmiastową aktualizację, wybierz pozycje **Serwery konfiguracji** > ***nazwa serwera*** > **Odśwież serwer**.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Wybierz pozycje **Przygotowanie infrastruktury** > **Cel**. Wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ docelowy model wdrażania: oparty na usłudze Resource Manager lub klasyczny.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

   ![Karta celu](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i wybierz pozycję **Wszystkie zasoby**.
2. Wybierz magazyn usługi Recovery Service o nazwie **ContosoVMVault**.
3. Aby utworzyć zasady replikacji, wybierz pozycję **Infrastruktura usługi Site Recovery** > **Zasady replikacji** > **+Zasady replikacji**.
4. W obszarze **Tworzenie zasad replikacji** wprowadź wartość **VMwareRepPolicy** jako nazwę zasad replikacji.
5. W polu **Wartość progowa celu punktu odzyskiwania** użyj domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
6. W obszarze **Przechowywanie punktu odzyskiwania**, umożliwiającym określenie, jak długie będzie okno przechowywania każdego punktu odzyskiwania, użyj domyślnej wartości 24 godzin. W tym samouczku użyj wartości 72 godziny. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
7. W obszarze **Częstotliwość wykonywania migawek na poziomie aplikacji**, umożliwiającym określenie, jak często tworzone są migawki, użyj domyślnej wartości 60 minut. Wybierz pozycję **OK**, aby utworzyć zasady.

   ![Tworzenie zasad replikacji](./media/tutorial-vmware-to-azure/replication-policy.png)

Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład jeśli zasady replikacji noszą nazwę **rep-policy**, zasady powrotu po awarii będą nosić nazwę **rep-policy-failback**. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

## <a name="enable-replication"></a>Włączanie replikacji

Po włączeniu replikacji maszyny wirtualnej usługa Site Recovery instaluje usługę mobilności. Zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.

Aby włączyć replikację:

1. Wybierz pozycje **Replikowanie aplikacji** > **Źródło**.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W obszarze **Typ maszyny** wybierz pozycję **Maszyny wirtualne**.
4. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta.
5. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie wybierz przycisk **OK**.
6. W obszarze **Cel** wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failover. Wybierz model wdrażania, którego chcesz użyć na platformie Azure (model usługi Resource Manager lub model klasyczny) dla maszyn wirtualnych w trybie failover.
7. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych.
8. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
9. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn.
10. W pozycji **Maszyny wirtualne** > **Wybierz maszyny wirtualne** wybierz każdą maszynę, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**.
11. W obszarze **Właściwości** > **Konfigurowanie właściwości** wybierz konto, które będzie używane przez serwer przetwarzania w celu automatycznego zainstalowania usługi mobilności na maszynie.
12. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji.
13. Wybierz pozycję **Włącz replikację**.

Możesz śledzić postęp zadania **Włącz ochronę** w pozycji **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.

Aby monitorować dodawane maszyny wirtualne, możesz sprawdzić czas ostatniego odnalezienia maszyn wirtualnych w obszarze **Serwery konfiguracji** > **Ostatni kontakt**. Aby dodać maszyny wirtualne, nie czekając na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (nie wybieraj go), a następnie wybierz pozycję **Odśwież**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](site-recovery-test-failover-to-azure.md)
