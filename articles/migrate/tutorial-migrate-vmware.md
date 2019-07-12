---
title: Migrowanie maszyn wirtualnych VMware na platformę Azure za pomocą migracji serwera migracji platformy Azure bez wykorzystania agentów lokalnych | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wykonywania i bez wykorzystania agentów migrację lokalnych maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3510c0505a5a3c1353642baf5060a83d13fdd43a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809127"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrowanie maszyn wirtualnych VMware do platformy Azure (bez agentów)

W tym artykule pokazano, jak przeprowadzić migrację lokalnych maszyn wirtualnych z programu VMware na platformę Azure za pomocą migracji bez agenta za pomocą narzędzia migracji serwera migracji platformy Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i wystąpień maszyn wirtualnych usług AWS/GCP, na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.

Niniejszy samouczek jest trzeci odcinek serii, który demonstruje sposób ocenę i migrację maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure migracji serwera oceny i migracji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowywanie maszyn wirtualnych do migracji.
> * Dodaj narzędzie do migracji serwera migracji platformy Azure.
> * Dowiedz się, chcesz przeprowadzić migrację maszyn wirtualnych.
> * Rozpocznij replikację maszyn wirtualnych.
> * Uruchamianie migracji testowej, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację maszyny Wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="migration-methods"></a>Metody migracji

Można przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure za pomocą narzędzia do migracji serwera migracji platformy Azure. To narzędzie oferuje kilka opcji do migracji maszyn wirtualnych programu VMware:

- Migracja za pomocą replikacji bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania żadnych narzędzi na nich.
- Migracja z agenta replikacji. Zainstaluj agenta na maszynie Wirtualnej w celu replikacji.

Aby zdecydować, czy chcesz użyć migracji bez wykorzystania agentów lub oparte na agentach, sprawdź następujące artykuły:

- [Dowiedz się, jak](server-migrate-overview.md) działa bez wykorzystania agentów migracji i [sprawdzanie ograniczeń](server-migrate-overview.md#agentless-migration-limitations).
- [Przeczytaj ten artykuł](tutorial-migrate-vmware-agent.md) Jeśli chcesz użyć tej metody oparte na agentach.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Zrozumienie](migrate-architecture.md) architekturę migracji programu VMware.
2. [Ukończenie pierwszego samouczka dotyczącego](tutorial-prepare-vmware.md) w tej serii, aby skonfigurować platformy Azure i programu VMware do migracji. W szczególności w ramach tego samouczka należy:
    - [Przygotowywanie platformy Azure](tutorial-prepare-vmware.md#prepare-azure) do migracji.
    - [Przygotowanie środowiska lokalnego](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) do migracji.
    
3. Zalecamy wypróbowanie oceny maszyn wirtualnych VMware z oceny Server migracji platformy Azure przed migracją je na platformie Azure. Aby skonfigurować ocenę, [ukończenia tego samouczka drugi](tutorial-assess-vmware.md) w tej serii. Jeśli nie chcesz ocenić maszyny wirtualne można pominąć ten samouczek. Mimo że zalecamy, aby wypróbować oceny, ale nie masz, można uruchomić oceny, przed podjęciem próby migracji.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodaj narzędzie do migracji serwera migracji platformy Azure

Jeśli nie wykonasz to drugi samouczek, aby ocenić maszyny wirtualne VMware, należy [wykonaj te instrukcje](how-to-add-tool-first-time.md) Skonfiguruj projekt usługi Azure Migrate, a następnie wybierz pozycję Narzędzia do migracji serwera migracji platformy Azure. 

Jeśli po drugim samouczku i jeszcze projektu Azure Migrate, konfigurowanie, Dodaj narzędzie do migracji serwera migracji platformy Azure w następujący sposób:

1. W projekcie usługi Azure Migrate kliknij **Przegląd**. 
2. W **Odkrywaj, ocenić i migracji serwerów**, kliknij przycisk **oceny i migracji serwerów**.

     ![Ocenianie i Migrowanie serwerów](./media/tutorial-migrate-vmware/assess-migrate.png)

3. W **narzędzia migracji**, wybierz opcję **kliknij tutaj, aby dodać narzędzia migracji, gdy wszystko jest gotowe do migracji**.

    ![Wybór narzędzia](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Na liście Narzędzia wybierz **usługi Azure Migrate: Migracja serwera** > **Dodaj narzędzie**

    ![Narzędzia do migracji serwera](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia w usłudze Azure Migrate

Migrowanie serwera migracji na platformę Azure działa z uproszczonego urządzenia maszyny Wirtualnej VMware. Urządzenie wykonuje odnajdowania maszyn wirtualnych i wysyła dane wydajności i metadanych maszyny Wirtualnej do migracji serwera migracji platformy Azure. Urządzenia w tej samej również jest używana przez narzędzia do oceny migracji serwera w Azure.

Jeśli wykonano to drugi samouczek, aby ocenić maszyny wirtualne VMware, masz już skonfigurowane urządzenia podczas tego samouczka. Jeśli nie została zgodna z ten samouczek, należy teraz skonfigurować urządzenie. Aby to zrobić, możesz: 

- Pobieranie pliku OVA szablonu, a następnie zaimportować go do serwera vCenter.
- Utworzyć to urządzenie, a następnie sprawdź, czy można nawiązać oceny Server migracji platformy Azure. 
- Konfigurowanie urządzenia po raz pierwszy, a następnie zarejestruj go w projekcie usługi Azure Migrate.

Postępuj zgodnie z instrukcjami w [w tym artykule](how-to-set-up-appliance-vmware.md) do skonfigurowania urządzenia.


## <a name="prepare-vms-for-migration"></a>Przygotowywanie maszyn wirtualnych do migracji

Usługa Azure Migrate wymaga pewnych zmian maszyny Wirtualnej, aby upewnić się, że maszyny wirtualne można migrować do platformy Azure.

- W przypadku niektórych [systemów operacyjnych](server-migrate-overview.md#agentless-migration-limitations), usługa Azure Migrate sprawia, że te zmiany automatycznie.
- W przypadku migrowania maszyny Wirtualnej, która nie ma jednego z następujących systemów operacyjnych, postępuj zgodnie z instrukcjami, aby przygotować maszynę Wirtualną.
- Należy wprowadzić te zmiany przed rozpoczęciem migracji. Przed wprowadzeniem zmian w przypadku migrowania maszyny Wirtualnej, maszyna wirtualna może się nie uruchomić się na platformie Azure.
- Zmiany konfiguracji na lokalne maszyny wirtualne są replikowane do platformy Azure po włączeniu replikacji dla maszyny Wirtualnej. Aby upewnić się, że zmiany są replikowane, upewnij się, że punkt odzyskiwania, które migrujesz do jest późniejszy niż czas, w którym zmiany konfiguracji zostały wprowadzone w środowisku lokalnym.


### <a name="prepare-windows-server-vms"></a>Przygotowanie maszyny wirtualne z systemem Windows Server

**Akcja** | **Szczegóły** | **Instrukcje**
--- | --- | ---
Upewnij się, że woluminy Windows w maszynie Wirtualnej platformy Azure Użyj tych samych liter dysków jako lokalną maszynę Wirtualną. | Konfigurowanie zasad sieci SAN jako Online wszystkich. | 1. Zaloguj się do maszyny Wirtualnej przy użyciu konta administratora, a następnie otwórz okno poleceń.<br/> 2. Typ **narzędzia diskpart** do uruchamiania narzędzia Diskpart.<br/> 3. Typ **zasad sieci SAN = OnlineAll**<br/> 4. Wpisz polecenie Exit, aby pozostawić narzędzia Diskpart, a następnie Zamknij wiersz polecenia.
Włącz konsoli szeregowej dostępu na platformie Azure dla maszyny Wirtualnej platformy Azure | Pomaga to w rozwiązywaniu problemów. Nie musisz ponownie uruchomić maszynę Wirtualną. Maszyna wirtualna platformy Azure zostanie uruchomiony przy użyciu obrazu dysku i odpowiada to ponowne uruchomienie komputera dla nowej maszyny Wirtualnej. | Postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console#enable-serial-console-in-custom-or-older-images) włączyć.
Zainstaluj integracji gościa funkcji Hyper-V | W przypadku migrowania maszyn z systemem Windows Server 2003, należy zainstalować usługi integracji gościa funkcji Hyper-V w systemie operacyjnym maszyny Wirtualnej. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Pulpit zdalny | Włączanie pulpitu zdalnego na maszynie Wirtualnej i sprawdź, czy Zapora Windows nie blokuje dostęp do usług pulpitu zdalnego profile żadnych sieci. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Przygotowywanie maszyn wirtualnych systemu Linux

**Akcja** | **Szczegóły** 
--- | --- | ---
Instalacja usług integracji funkcji Hyper-V w systemie Linux | Większość nowych wersji dystrybucje systemu Linux ma to domyślnie włączone.
Ponowne tworzenie obrazu systemu Linux init zawiera niezbędne sterowniki funkcji Hyper-V | Zapewnia to, że maszyna wirtualna uruchomi się na platformie Azure i jest wymagana tylko w niektórych dystrybucjach.
Włączanie rejestrowania platformy Azure konsoli szeregowej | Pomaga to w rozwiązywaniu problemów. Nie musisz ponownie uruchomić maszynę Wirtualną. Maszyna wirtualna platformy Azure zostanie uruchomiony przy użyciu obrazu dysku i odpowiada to ponowne uruchomienie komputera dla nowej maszyny Wirtualnej.<br/> Postępuj zgodnie z [w instrukcjach](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) włączyć.
Zaktualizuj plik mapy urządzenia | Zaktualizuj plik mapy urządzenie, którego nazwa urządzenia do skojarzenia woluminów, używać identyfikatorów urządzeń trwałego
Aktualizowanie wpisów w fstab | Zaktualizuj wpisy, aby używać identyfikatorów trwały wolumin.
Usuń regułę udev | Usuń wszystkie reguły udev, które rezerwuje nazwy interfejsu, na podstawie adresu mac itp.
Interfejsy sieciowe aktualizacji | Zaktualizuj interfejsy sieciowe do odbierania adresu IP, w oparciu o DHCP.
Włączanie protokołu ssh | Upewnij się, ssh jest włączony i sshd service jest ustawiony do automatycznego uruchamiania podczas ponownego uruchamiania komputera.<br/> Upewnij się, czy przychodzące ssh żądania połączenia nie są blokowane przez zaporę systemu operacyjnego lub reguły za pomocą skryptów.

[Postępuj zgodnie z tym artykule](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) w tym artykule omówiono następujące kroki dotyczące uruchamiania maszyny Wirtualnej z systemem Linux na platformie Azure i zawiera instrukcje dotyczące niektórych popularnych dystrybucji systemu Linux.  


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Za pomocą odnajdywanie zostało zakończone możesz przystąpić do replikacji maszyn wirtualnych programu VMware do platformy Azure.

1. W projekcie usługi Azure Migrate > **serwerów**, **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikować**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-replicate.png)

2. W **replikować**, > **ustawienia źródła** > **są maszynach zwirtualizowane?** , wybierz opcję **tak, przy użyciu oprogramowania VMware vSphere**.
3. W **lokalne urządzenie**, wybierz nazwę urządzenia usługi Azure Migrate, skonfigurowanej > **OK**. 

    ![Ustawienia źródła](./media/tutorial-migrate-vmware/source-settings.png)

    - W tym kroku przyjęto założenie, że już zdefiniowano urządzenie po ukończeniu tego samouczka.
    - Jeśli nie skonfigurowano urządzenie, postępuj zgodnie z instrukcjami [w tym artykule](how-to-set-up-appliance-vmware.md).

4. W **maszyn wirtualnych**, wybrać maszyny, którą chcesz replikować.
    - Jeśli uruchamiasz ocenę dla maszyn wirtualnych, można zastosować zmiany rozmiaru maszyn wirtualnych i zaleceń (w warstwie premium/standardowa) typ dysku wyniki oceny. Aby to zrobić, w **zaimportować ustawienia migracji z oceny usługi Azure Migrate?** , wybierz opcję **tak** opcji.
    - Jeśli nie została wykonana oceny lub nie chcesz używać ustawień oceny, wybierz **nie** opcje.
    - Jeśli została wybrana do oceny, wybierz grupy maszyn wirtualnych, a nazwa oceny.

    ![Wybierz ocenę](./media/tutorial-migrate-vmware/select-assessment.png)

5. W **maszyn wirtualnych**wyszukiwania dla maszyn wirtualnych, zgodnie z potrzebami i sprawdź poszczególnych maszyn wirtualnych, które mają zostać zmigrowane. Następnie kliknij przycisk **dalej: Docelowe ustawienia**.

    ![Wybierz maszyny wirtualne](./media/tutorial-migrate-vmware/select-vms.png)

6. W **docelowe ustawienia**, wybierz subskrypcję i docelowy region, do którego można będzie migracji i określ grupę zasobów, w której maszyny wirtualne Azure będą znajdować się po zakończeniu migracji. W **sieci wirtualnej**, wybierz pozycję Azure sieci wirtualnej/podsieci do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
7. W **korzyść użycia hybrydowego platformy Azure**:

    - Wybierz **nie** Jeśli nie chcesz zastosować korzyść użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Wybierz **tak** Jeśli masz maszyny z systemem Windows Server, które są objęte mających aktywne subskrypcje i pakietem Software Assurance lub Windows Server i chcesz zastosować korzyść dla maszyn, którego przenosisz. Następnie kliknij przycisk **Next** (Dalej).

    ![Ustawienia obiektu docelowego](./media/tutorial-migrate-vmware/target-settings.png)

8. W **obliczenia**, sprawdź nazwę maszyny Wirtualnej, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności. Maszyny wirtualne muszą być zgodne z [wymagania dotyczące usługi Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Rozmiar maszyny Wirtualnej**: Jeśli korzystasz zaleceń dotyczących oceny, lista rozwijana rozmiar maszyny Wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybiera najlepsze dopasowanie w subskrypcji platformy Azure w oparciu o rozmiarze. Alternatywnie, wybierz ręczne rozmiar w **rozmiar maszyny Wirtualnej Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) dla maszyny Wirtualnej. Dysk systemu operacyjnego jest dysk systemu operacyjnego, programu rozruchowego oraz Instalatora. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w dostępności platformy Azure po migracji, należy określić zestaw. Zestaw musi być w docelowej grupie zasobów, które określisz dla migracji.

    ![Ustawienia obliczeń maszyny Wirtualnej](./media/tutorial-migrate-vmware/compute-settings.png)

9. W **dysków**, określ, czy dyski maszyny Wirtualnej powinny być replikowane na platformę Azure i wybierz typ dysku (standardowy dysk SSD/dysk twardy lub dyski zarządzane w warstwie premium) na platformie Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Dyski można wykluczyć z replikacji.
    - Jeśli Wyklucz dyski będą obecne na maszynie Wirtualnej platformy Azure po migracji. 

    ![Dyski](./media/tutorial-migrate-vmware/disks.png)

10. W **Przejrzyj i Rozpocznij replikację**, przejrzyj ustawienia i kliknij przycisk **replikować** rozpoczyna się Replikacja początkowa dla serwerów.

> [!NOTE]
> Możesz zaktualizować ustawienia replikacji w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzaj** > **replikowania maszyn**. Nie można zmienić ustawienia, po uruchomieniu replikacji.

### <a name="provisioning-for-the-first-time"></a>Inicjowanie obsługi administracyjnej po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, w przypadku przeprowadzania replikacji w projekcie usługi Azure Migrate, migracji serwera migracji platformy Azure automatycznie aprowizuje zasoby w tej samej grupie zasobów projektu.

- **Usługa Service bus**: Migrowanie serwera migracji na platformę Azure używa usługi service bus wysyłać replikacji aranżacji do urządzenia.
- **Konto magazynu bramy**: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie dotyczące maszyn wirtualnych jest replikowana.
- **Konto magazynu dzienników**: Urządzenie Azure Migrate służy do przekazywania dzienników replikacji dla maszyn wirtualnych na konto magazynu dzienników. Usługa Azure Migrate dotyczy informacje o replikacji dyski zarządzane repliki.
- **Magazyn kluczy**: Urządzenie Azure Migrate używa usługi key vault do zarządzania, parametry połączenia usługi service bus i klawisze dostępu dla konta magazynu używana w replikacji. Należy mieć skonfigurować uprawnienia, które magazynu kluczy musi uzyskać dostęp do konta magazynu, podczas przygotowywania. [Przejrzyj te uprawnienia](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie


- Po kliknięciu **replikować** rozpoczyna się zadanie Rozpocznij replikację. 
- Zadanie replikacji Start zakończy się pomyślnie, używany przez maszyny rozpocząć ich replikacji początkowej na platformie Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny Wirtualnej. Dysk danych z migawki są replikowane do zarządzanych dyskach repliki w systemie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany dysków w środowisku lokalnym okresowo są replikowane do repliki dysków na platformie Azure.

Można śledzić stan zadania w powiadomieniach portalu.

Możesz monitorować stan replikacji, klikając **replikowania serwerów** w **usługi Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Kiedy rozpoczyna się replikacja różnicowa, jest testowa migracja maszyn wirtualnych, można uruchomić przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, że w tym co najmniej raz dla każdej maszyny, przed przeprowadzeniem migracji.

- Uruchamianie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wywierania wpływu na komputerach lokalnych, które działać i nadal replikowane. 
- Migracja testowa symuluje migracji, tworząc Maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migracji z siecią wirtualną spoza środowiska produkcyjnego, w ramach subskrypcji platformy Azure).
- Test zreplikowanej maszyny Wirtualnej platformy Azure umożliwiają sprawdzanie poprawności migracji, wykonaj testowanie aplikacji i rozwiązać wszelkie problemy przed pełnej migracji.

Przeprowadzić migrację testu w następujący sposób:


1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **testu migracji serwerów**.

     ![Testowanie serwerów zmigrowane](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę Wirtualną, aby przetestować, a następnie kliknij przycisk **Test Migrowanie**.

    ![Migracja testowa](./media/tutorial-migrate-vmware/test-migrate.png)

3. W **migracji testowej**, wybierz sieć wirtualną platformy Azure, w którym maszyna wirtualna platformy Azure zostaną umieszczone po zakończeniu migracji. Zaleca się użyć sieci wirtualnej spoza środowiska produkcyjnego.
4. **Migracja testowa** rozpoczyna się zadanie. Monitorowanie zadania w powiadomieniach portalu.
5. Po zakończeniu migracji, należy wyświetlić zmigrowanej maszyny Wirtualnej platformy Azure w **maszyn wirtualnych** w witrynie Azure portal. Nazwa komputera ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszyny Wirtualnej platformy Azure w **replikowania maszyn**i kliknij przycisk **wyczyścić migracji testowej**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, należy przeprowadzić migrację maszyn lokalnych.

1. W projekcie usługi Azure Migrate > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikowania serwerów**.

    ![Replikacja serwerów](./media/tutorial-migrate-vmware/replicate-servers.png)

2. W **replikowania maszyn**, kliknij prawym przyciskiem myszy maszynę Wirtualną > **migracji**.
3. W **migracji** > **zamknąć maszyny wirtualne i wykonywać planowanej migracji bez utraty danych**, wybierz opcję **tak** > **OK** .
    - Domyślnie usługa Azure Migrate zamyka lokalnej maszyny Wirtualnej i uruchamia replikacji na żądanie, aby zsynchronizować zmiany, które wystąpiły, ponieważ wystąpił podczas ostatniej replikacji maszyny Wirtualnej. Dzięki temu bez utraty danych.
    - Jeśli nie chcesz zamknąć maszynę Wirtualną, wybrać **nr**
4. Uruchamia zadanie migracji dla maszyny Wirtualnej. Śledź zadania w powiadomieniach platformy Azure.
5. Po ukończeniu zadania można wyświetlać i zarządzać maszyną Wirtualną z **maszyn wirtualnych** strony.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji, kliknij prawym przyciskiem myszy maszynę Wirtualną > **Zatrzymaj migrację**. Zatrzymanie replikacji maszyny w środowisku lokalnym i czyści informacje o stanie replikacji dla maszyny Wirtualnej.
2. Zainstaluj na maszynie Wirtualnej platformy Azure [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) agenta na migrowanych maszyn.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Wytnij nad ruchem do migrowanych wystąpienia maszyny Wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Po przeprowadzeniu migracji najlepszych rozwiązań

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokowanie i ograniczanie dostępu ruchu przychodzącego z [Azure Security Center — dokładnie na czas administracji](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Badanie [podróży migracji do chmury](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) w ramach wdrażania chmury platformy Azure.
