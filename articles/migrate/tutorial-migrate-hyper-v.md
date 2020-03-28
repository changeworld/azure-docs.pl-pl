---
title: Migrowanie maszyn wirtualnych funkcji Hyper V na platformę Azure za pomocą migracji serwera migracji usługi Azure
description: Dowiedz się, jak przeprowadzić migrację lokalnych maszyn wirtualnych z funkcji Hyper V na platformę Azure za pomocą migracji serwera migracji usługi Azure
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2b9ed56186649b7644adbd1237ad74af50474cc5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80279712"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrowanie maszyn wirtualnych funkcji Hyper-V na platformę Azure 

W tym artykule pokazano, jak przeprowadzić migrację lokalnych maszyn wirtualnych funkcji Hyper V na platformę Azure przy użyciu migracji bezagentowej za pomocą narzędzia Migracji usługi Azure: Migracja serwera.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdowania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmury prywatnej/publicznej na platformę Azure. Centrum udostępnia narzędzia migracji platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania innych firm (ISV).

Ten samouczek jest trzecim z serii, który pokazuje, jak ocenić i migrować funkcji Hyper-V na platformę Azure przy użyciu oceny i migracji serwera migracji usługi Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:


> [!div class="checklist"]
> * Przygotowywanie platformy Azure i lokalnego środowiska funkcji Hyper-V
> * Skonfiguruj środowisko źródłowe i wdrażaj urządzenie replikacji.
> * Skonfiguruj środowisko docelowe.
> * Włącz replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przejrzyj](hyper-v-migration-architecture.md) architekturę migracji funkcji Hyper-V.
2. [Ukończ pierwszy samouczek](tutorial-prepare-hyper-v.md) z tej serii, aby skonfigurować platformę Azure i funkcji Hyper-V do migracji. W pierwszym samouczku:
    - [Przygotuj platformę Azure](tutorial-prepare-hyper-v.md#prepare-azure) do migracji.
    - [Przygotuj środowisko lokalne](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) do migracji.
3. Zaleca się, aby spróbować oceny funkcji Hyper-VMs przy użyciu usługi Azure Migrate: Server Assessment, przed migracją ich do platformy Azure. Aby to zrobić, [wykonaj drugi samouczek](tutorial-assess-hyper-v.md) z tej serii. Mimo że zaleca się wypróbowanie oceny, nie trzeba uruchamiać oceny przed migracją maszyn wirtualnych.
4. Upewnij się, że twojemu kontu platformy Azure jest przypisana rola współautora maszyny wirtualnej, aby mieć uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapis na dysku zarządzanym platformy Azure.
5. [Konfigurowanie sieci platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Podczas migracji na platformę Azure utworzone maszyny wirtualne platformy Azure są dołączane do sieci platformy Azure, którą określisz podczas konfigurowania migracji.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji serwera migracji platformy Azure

Jeśli nie po drugim samouczku do oceny maszyn wirtualnych funkcji Hyper-V, należy [wykonać te instrukcje,](how-to-add-tool-first-time.md) aby skonfigurować projekt migracji platformy Azure i dodać narzędzie oceny migracji serwera azure do projektu.

Jeśli po drugim samouczku i masz już projekt migracji platformy Azure, dodaj narzędzie Migracji platformy Azure: Migracja serwera w następujący sposób:

1. W projekcie migracji platformy Azure kliknij pozycję **Przegląd**. 
2. W **przypadku serwerów Discover oceniaj i migruj**kliknij pozycję **Oceń i migruj serwery**.
3. W **obszarze Narzędzia migracji**wybierz pozycję Kliknij **tutaj, aby dodać narzędzie do migracji, gdy jesteś gotowy do migracji**.

    ![Wybieranie narzędzia](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Na liście narzędzi wybierz narzędzie **Azure Migrate: Server Migration** > **Add tool**

    ![Narzędzie Do migracji serwera](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia migracji platformy Azure

Migracja serwera migracji usługi Azure migruje uruchamia agenta oprogramowania na hostach funkcji Hyper-V lub węzłach klastra w celu organizowania i replikowania danych do programu Azure Migrate i nie wymaga dedykowanego urządzenia do migracji.

- Urządzenie do przeprowadzania migracji platformy Azure: Server Assessment przeprowadza odnajdowanie maszyn wirtualnych i wysyła metadane maszyny wirtualnej i dane o wydajności do migracji serwera migracji serwera usługi Azure.
- Aranżacja migracji i replikacja danych jest obsługiwana przez dostawcę odzyskiwania witryny platformy Microsoft Azure i agenta usługi odzyskiwania platformy Microsoft Azure.

Aby skonfigurować urządzenie:
- Jeśli po drugim samouczku do oceny maszyn wirtualnych funkcji Hyper-V, już skonfigurować urządzenie podczas tego samouczka i nie trzeba tego robić ponownie.
- Jeśli nie po tym samouczku, musisz skonfigurować urządzenie teraz. Aby to zrobić, możesz: 

    - Pobierz skompresowany dysk VHD funkcji Hyper-V z witryny Azure portal.
    - Utwórz urządzenie i sprawdź, czy można połączyć się z oceną serwera migracji platformy Azure. 
    - Skonfiguruj urządzenie po raz pierwszy i zarejestruj go w projekcie migracji platformy Azure.

    Postępuj zgodnie ze szczegółowymi instrukcjami w [tym artykule,](how-to-set-up-appliance-hyper-v.md) aby skonfigurować urządzenie.

## <a name="prepare-hyper-v-hosts"></a>Przygotowywanie hostów funkcji Hyper-V

1. W projekcie migracji platformy Azure > **serwery**w **programie Azure Migrate: Migracja serwera**kliknij przycisk **Odkryj**.
2. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, wybierz **Tak, z Hyper-V**.
3. W **regionie docelowym**wybierz region platformy Azure, do którego chcesz przeprowadzić migrację komputerów.
6. Wybierz **pozycję Potwierdź, że regionem docelowym migracji jest nazwa regionu**.
7. Kliknij **pozycję Utwórz zasoby**. Spowoduje to utworzenie magazynu usługi Azure Site Recovery w tle.
    - Jeśli migracja została już skonfigurowana za pomocą migracji z usługą Azure Migrate Server, ta opcja nie będzie wyświetlana, ponieważ zasoby zostały skonfigurowane wcześniej.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje są do tego regionu.
    
8. W **polu Przygotowywanie serwerów hosta funkcji Hyper-V**pobierz dostawcę replikacji funkcji Hyper-V i plik klucza rejestracji.
    - Klucz rejestracji jest potrzebny do zarejestrowania hosta funkcji Hyper-V za pomocą migracji serwera migracji programu Azure Migrate Server.
    - Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Dostawca pobierania i klucz](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Skopiuj plik instalatora dostawcy i plik klucza rejestracji do każdego hosta funkcji Hyper-V (lub węzła klastra) z uruchomionymi maszynami wirtualnymi, które chcesz replikować.
5. Uruchom plik konfiguracji dostawcy na każdym hoście, zgodnie z opisem w następnej procedurze.
6. Po zainstalowaniu dostawcy na hostach w **discover maszyny**kliknij **przycisk Finalizuj rejestrację**.

    ![Finalizowanie rejestracji](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Może upłynąć do 15 minut po zakończeniu rejestracji, dopóki nie zostaną wyświetlone maszyny wirtualne w migracji serwera migracji programu Azure Migrate Server. Po wykryciu maszyn wirtualnych liczba **wykrytych serwerów** rośnie.

![Odnalezione serwery](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Rejestrowanie hostów funkcji Hyper-V

Zainstaluj pobrany plik instalacyjny (AzureSiteRecoveryProvider.exe) na każdym odpowiednim hoście funkcji Hyper-V.

1. Uruchom plik konfiguracji dostawcy na każdym hoście lub węźle klastra.
2. W Kreatorze instalacji dostawcy > **microsoft update,** wybierz opcję korzystania z witryny Microsoft Update w celu sprawdzenia dostępności aktualizacji dostawcy.
3. W **obszarze Instalacja**zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a następnie wybierz pozycję **Zainstaluj**.
4. Po instalacji w Kreatorze rejestracji > **ustawieniach przechowalni**wybierz pozycję **Przeglądaj**, a następnie w **polu Plik klucza**wybierz pobrany plik klucza przechowalni.
5. W **ustawieniach serwera proxy**określ, w jaki sposób dostawca działający na hoście łączy się z Internetem.
    - Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę **http://ip-address**serwera **http://FQDN**proxy jako , lub . Serwery proxy HTTPS nie są obsługiwane.
   

6. Upewnij się, że dostawca może dotrzeć do [wymaganych adresów URL](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts).
7. W **2014**r. w 2015 r. po **zarejestrowaniu**hosta kliknij przycisk Zakończ .

## <a name="replicate-hyper-v-vms"></a>Replikowanie maszyn wirtualnych funkcji Hyper V

Po zakończeniu odnajdywania można rozpocząć replikację maszyn wirtualnych z programem Hyper-V na platformie Azure.

> [!NOTE]
> Można replikować do 10 maszyn razem. Jeśli chcesz replikować więcej, a następnie replikuj je jednocześnie w partiach po 10.

1. W projekcie migracji platformy Azure > **serwery**, **Migracja platformy Azure: Migracja serwera**, kliknij przycisk **Replikuj**.
2. W **obszarze Replika**, > **ustawienia** > źródła**Czy twoje maszyny są zwirtualizowane?**, wybierz **tak, z hyper-V**. Następnie kliknij **przycisk Dalej: Maszyny wirtualne**.
3. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

        ![Wybieranie oceny](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. W obszarze **Maszyny wirtualne** wyszukaj potrzebne maszyny wirtualne i sprawdź każdą maszynę wirtualną, którą chcesz migrować. Następnie kliknij przycisk **Dalej: Ustawienia celu**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-hyper-v/select-vms.png)

5. W **ustawieniach docelowych**wybierz region docelowy, do którego będzie przeprowadzać migrację, subskrypcję i grupę zasobów, w której będą rezydować maszyny wirtualne platformy Azure po migracji.
7. W **obszarze Konto magazynu replikacji**wybierz konto usługi Azure Storage, na którym będą przechowywane replikowane dane na platformie Azure.
8. **Sieć wirtualna**, wybierz sieć wirtualną/podsieć platformy Azure, do której będą dołączane maszyny wirtualne platformy Azure po migracji.
9. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-hyper-v/target-settings.png)

10. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny Wirtualnej:** Jeśli używasz zaleceń dotyczących oceny, lista rozwijana rozmiaru maszyny Wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) maszyny Wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności:** Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeniowe maszyny Wirtualnej](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. W obszarze **Dyski** określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowe dyski SSD/dyski twarde lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Dyski](./media/tutorial-migrate-hyper-v/disks.png)

10. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można zaktualizować w dowolnym momencie przed rozpoczęciem replikacji w obszarze **Zarządzanie maszynami** > **replikacji**. Ustawień nie można zmienić po rozpoczęciu replikacji.

## <a name="provisioning-for-the-first-time"></a>Inicjowanie obsługi administracyjnej po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, którą replikujesz w projekcie migracji platformy Azure, migracja usługi Azure: Migracja serwera automatycznie aprowiza te zasoby w tej samej grupie zasobów co projekt.

- **Magistrala usługi:** Migracja platformy Azure: Migracja serwera używa usługi Service Bus do wysyłania komunikatów aranżacji replikacji do urządzenia.
- **Konto magazynu bramy:** Migracja usługi Azure: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dziennika:** urządzenie migracji platformy Azure przekazuje dzienniki replikacji maszyn wirtualnych do konta magazynu dziennika. Usługa Azure Migrate stosuje informacje o replikacji do dysków zarządzanych przez replikę.
- **Magazyn kluczy:** urządzenie migracji platformy Azure używa magazynu kluczy do zarządzania ciągami połączeń dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji. Należy skonfigurować uprawnienia, które magazyn kluczy musi uzyskać dostęp do konta magazynu podczas [przygotowywania platformy Azure](tutorial-prepare-hyper-v.md#prepare-azure) dla oceny i migracji maszyny Wirtualnej funkcji Hyper-V. 


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie


- Po **kliknięciu przycisku Replikacja** rozpoczyna się zadanie replikacji początkowej. 
- Po pomyślnym zakończeniu zadania replikacji początkowej maszyny rozpoczynają replikację początkową na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja delta. Przyrostowe zmiany na dyskach lokalnych są okresowo replikowane na platformę Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Stan replikacji można monitorować, klikając opcję **Replikowanie serwerów** w **programie Azure Migrate: Server Migration**.
![Monitorowanie replikacji](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można uruchomić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, aby zrobić to co najmniej raz dla każdego komputera, przed migracją.

- Uruchomienie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają operacyjne i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migrując do nieprodukcyjnej sieci wirtualnej platformy Azure w ramach subskrypcji platformy Azure).
- Za pomocą replikowanego testu maszyny Wirtualnej platformy Azure można sprawdzić poprawność migracji, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W **celach** > migracji**Serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. W **migracji testowej**wybierz sieć wirtualną platformy Azure, w której będzie znajdować się maszyna wirtualna platformy Azure po migracji. Zaleca się użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po sprawdzeniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację komputerów lokalnych.

1. W projekcie migracji platformy Azure > **serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij pozycję **Replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W **obszarze Migracja** > Zamknij**maszyny wirtualne i wykonaj planowaną migrację bez utraty danych**, wybierz tak **OK** > **OK**.
    - Domyślnie usługa Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby zsynchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Gwarantuje to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Zatrzymuje replikację komputera lokalnego.
    - Usuwa komputer z licznika **serwerów replikacji** w usłudze Azure Migrate: Server Migration.
    - Czyści informacje o stanie replikacji dla maszyny Wirtualnej.
2. Zainstaluj agenta systemu [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) platformy Azure na zmigrowanych komputerach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Przetnij ruch do zmigrowanego wystąpienia maszyny Wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze praktyki po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Blokowanie i ograniczanie dostępu do ruchu przychodzącego za pomocą [usługi Azure Security Center — zarządzanie tylko w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [proces migracji](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) do chmury w ramach wdrażania chmury azure.
