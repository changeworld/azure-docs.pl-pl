---
title: Migrowanie lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure za pomocą migracji Azure Migrate Server | Microsoft Docs
description: W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu migracji Azure Migrate serwera
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 755bb6d019418cf9dae22ebf7ee6a3c94af3c750
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309445"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrowanie maszyn wirtualnych funkcji Hyper-V do platformy Azure 

W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych funkcji Hyper-V do platformy Azure przy użyciu migracji bez wykorzystania agentów za pomocą narzędzia migracji Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ten samouczek jest trzecią częścią serii, która przedstawia sposób oceny i migracji funkcji Hyper-V do platformy Azure przy użyciu Azure Migrate oceny i migracji serwera. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:


> [!div class="checklist"]
> * Przygotowywanie platformy Azure i lokalnego środowiska funkcji Hyper-V
> * Skonfiguruj środowisko źródłowe i Wdróż urządzenie do replikacji.
> * Skonfiguruj element docelowy environmen..
> * Włącz replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przejrzyj](migrate-architecture.md) architekturę migracji funkcji Hyper-V.
2. [Wykonaj pierwszy samouczek](tutorial-prepare-hyper-v.md) z tej serii, aby skonfigurować platformę Azure i funkcję Hyper-V do migracji. W pierwszym samouczku:
    - [Przygotuj platformę Azure](tutorial-prepare-hyper-v.md#prepare-azure) do migracji.
    - [Przygotuj środowisko lokalne](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) do migracji.
3. Zalecamy, aby przed migracją na platformę Azure spróbować ocenić maszyny wirtualne funkcji Hyper-V przy użyciu oceny serwera Azure Migrate. Aby to zrobić, [Wykonaj drugi samouczek](tutorial-assess-hyper-v.md) z tej serii. Chociaż zalecamy wypróbowanie oceny, nie trzeba wykonywać oceny przed migracją maszyn wirtualnych.
4. Upewnij się, że Twoje konto platformy Azure ma przypisaną rolę współautor maszyny wirtualnej, dzięki czemu masz uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapisz na dysku zarządzanym platformy Azure.   
5. [Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Podczas migracji na platformę Azure utworzone maszyny wirtualne platformy Azure są przyłączone do sieci platformy Azure, którą określisz podczas konfigurowania migracji.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji Azure Migrate Server

Jeśli nie korzystasz z drugiego samouczka do oceny maszyn wirtualnych funkcji Hyper-V, musisz [postępować zgodnie z poniższymi instrukcjami](how-to-add-tool-first-time.md) , aby skonfigurować projekt Azure Migrate i dodać do projektu narzędzie do migracji z serwera Azure Migrate.

Jeśli wykonano drugi samouczek i masz już Azure Migrate konfigurację projektu, Dodaj narzędzie do migracji Azure Migrate Server w następujący sposób:

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**. 
2. W obszarze **odnajdywanie, ocenianie i**Migrowanie serwerów kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
3. W obszarze **Narzędzia migracji**wybierz **pozycję kliknij tutaj, aby dodać narzędzie do migracji, gdy wszystko jest gotowe do migracji**.

    ![Wybór narzędzia](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Z listy narzędzia wybierz **Azure Migrate:** **Narzędzie Dodawanie** do migracji > serwera

    ![Narzędzie do migracji serwera](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia Azure Migrate

Migracja serwera Azure Migrate uruchamia lekkim urządzeniem maszyny wirtualnej funkcji Hyper-V.

- Urządzenie wykonuje odnajdywanie maszyn wirtualnych i wysyła metadane maszyny wirtualnej i dane wydajności do migracji serwera Azure Migrate.
- To samo urządzenie jest również używane przez narzędzie do oceny serwera Azure Migrate.

Aby skonfigurować urządzenie:
- Jeśli wykonano drugi samouczek dotyczący oceny maszyn wirtualnych funkcji Hyper-V, urządzenie zostało już skonfigurowane w tym samouczku.
- Jeśli nie korzystasz z tego samouczka, musisz skonfigurować urządzenie teraz. W tym celu: 

    - Pobierz skompresowany wirtualny dysk twardy funkcji Hyper-V z Azure Portal.
    - Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate. 
    - Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

    Postępuj zgodnie z instrukcjami w [tym artykule](how-to-set-up-appliance-hyper-v.md) , aby skonfigurować urządzenie.

## <a name="prepare-hyper-v-hosts"></a>Przygotuj hosty funkcji Hyper-V

1. Na **serwerach**Azure Migrate Project > w **Azure Migrate: Migracja**serwera, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie** > maszyn**są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji Hyper-V**.
3. W **obszarze region docelowy**wybierz region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację maszyn.
6. Wybierz pozycję **Potwierdź, że docelowy region migracji to nazwa regionu**.
7. Kliknij pozycję **Utwórz zasoby**. Spowoduje to utworzenie magazynu Azure Site Recovery w tle.
    - Jeśli już skonfigurowano migrację z Azure Migrate migracji do serwera, ta opcja nie zostanie wyświetlona, ponieważ zasoby zostały wcześniej skonfigurowane.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje znajdują się w tym regionie.
    
8. W obszarze **Przygotuj serwery hosta funkcji Hyper-v**Pobierz dostawcę replikacji funkcji Hyper-v i plik klucza rejestracji.
    - Klucz rejestracji jest wymagany do zarejestrowania hosta funkcji Hyper-V z migracją na serwer Azure Migrate.
    - Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Pobierz dostawcę i klucz](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Skopiuj plik Instalatora dostawcy i plik klucza rejestracji do każdego hosta funkcji Hyper-V (lub węzła klastra) z uruchomionymi maszynami wirtualnymi, które chcesz replikować.
5. Uruchom plik Instalatora dostawcy na każdym hoście, zgodnie z opisem w następnej procedurze.
6. Po zainstalowaniu dostawcy na hostach w obszarze **odnajdywanie maszyn**kliknij pozycję **finalizowanie rejestracji**.

    ![Finalizowanie rejestracji](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Po zakończeniu rejestracji może upłynąć do 15 minut, dopóki odnalezione maszyny wirtualne nie pojawią się w ramach migracji na serwer Azure Migrate. Jako że maszyny wirtualne są odnajdywane, wzrasta liczba **odnalezionych serwerów** .

![Odnalezione serwery](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Rejestrowanie hostów funkcji Hyper-V

Zainstaluj pobrany plik instalacyjny (AzureSiteRecoveryProvider. exe) na każdym odpowiednim hoście funkcji Hyper-V.

1. Uruchom plik Instalatora dostawcy na każdym hoście lub węźle klastra.
2. W Kreatorze instalacji dostawcy > **Microsoft Update**zapoznaj się z tematem, aby wyszukać aktualizacje dostawcy przy użyciu Microsoft Update.
3. W obszarze **Instalacja**zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a następnie wybierz pozycję **Zainstaluj**.
4. Po zakończeniu instalacji w Kreatorze rejestracji > **ustawienia magazynu**, wybierz pozycję **Przeglądaj**, a następnie w polu **plik klucza**wybierz pobrany plik klucza magazynu.
5. W obszarze **Ustawienia serwera proxy**Określ, jak dostawca uruchomiony na hoście ma łączyć się z Internetem.
    - Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę serwera proxy jako **http://ip-address** lub. **http://FQDN** Serwery proxy HTTPS nie są obsługiwane.
   

6. Upewnij się, że dostawca może dotrzeć do [wymaganych adresów URL](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. W obszarze **rejestracja**po zarejestrowaniu hosta kliknij przycisk **Zakończ**.

## <a name="replicate-hyper-v-vms"></a>Replikowanie maszyn wirtualnych funkcji Hyper-V

Po ukończeniu odnajdywania można rozpocząć replikację maszyn wirtualnych funkcji Hyper-V na platformę Azure.

> [!NOTE]
> Można replikować do 10 maszyn jednocześnie. Jeśli chcesz replikować więcej, a następnie Replikuj je jednocześnie w partiach 10.

1. W projekcie usługi Azure Migrate wybierz pozycję > **Serwery**, **Azure Migrate: Migracja serwera** i kliknij pozycję **Replikuj**.
2. W obszarze **replikacja**> **Ustawienia** > źródła**są zwirtualizowane na maszynach?** wybierz opcję **tak, z funkcją Hyper-V**. Następnie kliknij pozycję **Dalej: Maszyny**wirtualne.
3. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

        ![Wybieranie oceny](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. W obszarze **Maszyny wirtualne** wyszukaj potrzebne maszyny wirtualne i sprawdź każdą maszynę wirtualną, którą chcesz migrować. Następnie kliknij przycisk **dalej: Ustawienia elementu docelowego**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-hyper-v/select-vms.png)

5. W obszarze **ustawienia docelowe**wybierz region docelowy, do którego zostanie przeprowadzona migracja, subskrypcja i Grupa zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
7. W obszarze **konto magazynu replikacji**wybierz konto usługi Azure Storage, w którym będą przechowywane zreplikowane dane na platformie Azure.
8. **Virtual Network**wybierz sieć wirtualną platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
9. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Next** (Dalej).

    ![Ustawienia elementu docelowego](./media/tutorial-migrate-hyper-v/target-settings.png)

10. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: jeśli używasz rekomendacji dotyczących oceny, lista rozwijana rozmiarów maszyny wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: określ dysk systemu operacyjnego (rozruchowy) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności**: jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ ten zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeniowe maszyny wirtualnej](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. W obszarze **Dyski** określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowe dyski SSD/dyski twarde lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Dyski](./media/tutorial-migrate-hyper-v/disks.png)

10. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji możesz zaktualizować w dowolnym momencie przed rozpoczęciem replikacji w obszarze **Zarządzanie** > **Replikowanie maszyn**. Ustawień nie można zmienić po rozpoczęciu replikacji.

### <a name="provisioning-for-the-first-time"></a>Inicjowanie obsługi po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, która jest replikowana w projekcie Azure Migrate, Azure Migrate migracji serwera automatycznie zainicjuje te zasoby w tej samej grupie zasobów co projekt.

- **Usługa Service Bus**: Migracja serwera Azure Migrate przy użyciu usługi Service Bus wysyła do urządzenia komunikaty aranżacji replikacji.
- **Konto magazynu bramy**: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dzienników**: Urządzenie Azure Migrate przekazuje dzienniki replikacji dla maszyn wirtualnych do konta magazynu dzienników. Azure Migrate stosuje informacje o replikacji do dysków zarządzanych repliki.
- **Magazyn kluczy**: Urządzenie Azure Migrate używa magazynu kluczy do zarządzania parametrami połączenia dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji. Należy skonfigurować uprawnienia, które Magazyn kluczy musi uzyskać dostęp do konta magazynu podczas przygotowywania. [Przejrzyj te uprawnienia](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie


- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane na platformę Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja**serwera.
![Monitoruj replikację](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: migracja serwera** kliknij pozycję **Przeprowadzono migrację testową serwerów**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. W projekcie usługi Azure Migrate wybierz pozycję > **Serwery** > **Azure Migrate: migracja serwera** i kliknij pozycję **Replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie** > **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych** wybierz pozycję **Tak** > **OK**.
    - Domyślnie usługa Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby zsynchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Gwarantuje to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to zatrzymanie replikacji maszyny lokalnej i oczyszczenie informacji o stanie replikacji dla maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.
