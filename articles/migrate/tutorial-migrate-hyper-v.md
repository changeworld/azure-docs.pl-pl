---
title: Migrowanie lokalnych maszyn wirtualnych z funkcją Hyper-V na platformę Azure za pomocą platformy Azure migracji serwera | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych z funkcją Hyper-V na platformę Azure za pomocą usługi Azure Migration migracji serwera
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0d4ab3225501c2573b07f27e40a8f42508a4df88
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809540"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrowanie maszyn wirtualnych funkcji Hyper-V do platformy Azure 

W tym artykule pokazano, jak przeprowadzić migrację lokalnych maszyn wirtualnych z funkcją Hyper-V na platformę Azure za pomocą migracji bez agenta za pomocą narzędzia migracji serwera migracji platformy Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i chmury prywatne/publiczne maszyn wirtualnych na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.

Niniejszy samouczek jest trzeci odcinek serii, który demonstruje, jak ocenianie i Migrowanie funkcji Hyper-V do platformy Azure przy użyciu usługi Azure migracji serwera oceny i migracji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:


> [!div class="checklist"]
> * Przygotowywanie platformy Azure i w lokalnym środowisku funkcji Hyper-V
> * Konfigurowanie środowiska źródłowego i wdrożyć urządzenie replikacji.
> * Konfigurowanie environmen docelowego...
> * Włącz replikację.
> * Uruchamianie migracji testowej, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchamianie pełnej migracji na platformę Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przegląd](migrate-architecture.md) architekturę migracji funkcji Hyper-V.
2. [Ukończenie pierwszego samouczka dotyczącego](tutorial-prepare-hyper-v.md) w tej serii, aby skonfigurować platformy Azure i funkcji Hyper-V do migracji. W pierwszym samouczku możesz:
    - [Przygotowywanie platformy Azure](tutorial-prepare-hyper-v.md#prepare-azure) do migracji.
    - [Przygotowanie środowiska lokalnego](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) do migracji.
3. Zalecamy wypróbowanie oceny maszyn wirtualnych funkcji Hyper-V, za pomocą migracji serwera oceny Azure, przed migracją je na platformie Azure. Aby to zrobić, [ukończenia tego samouczka drugi](tutorial-assess-hyper-v.md) w tej serii. Mimo że firma Microsoft zaleca, wypróbuj ocenę, nie masz można uruchomić oceny, przed przeprowadzeniem migracji maszyn wirtualnych.
4. Upewnij się, że Twoje konto platformy Azure jest przypisana rola Współautor maszyny wirtualnej tak, że masz uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapis na platformie Azure dysku zarządzanego.   
5. [Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Podczas migracji na platformę Azure utworzonych maszyn wirtualnych platformy Azure zostaną połączone z siecią platformy Azure, które można określić podczas konfigurowania migracji.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodaj narzędzie do migracji serwera migracji platformy Azure

Jeśli nie wykonasz drugim samouczku do oceny maszyn wirtualnych funkcji Hyper-V, należy [wykonaj te instrukcje](how-to-add-tool-first-time.md) skonfigurować projekt usługi Azure Migrate, i Dodaj narzędzie do migracji serwera migracji platformy Azure do projektu.

Jeśli po drugim samouczku i masz już usługę Azure Migrate konfiguracji projektu, Dodaj narzędzie do migracji serwera migracji platformy Azure w następujący sposób:

1. W projekcie usługi Azure Migrate kliknij **Przegląd**. 
2. W **Odkrywaj, ocenić i migracji serwerów**, kliknij przycisk **oceny i migracji serwerów**.
3. W **narzędzia migracji**, wybierz opcję **kliknij tutaj, aby dodać narzędzia migracji, gdy wszystko jest gotowe do migracji**.

    ![Wybór narzędzia](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Na liście Narzędzia wybierz **usługi Azure Migrate: Migracja serwera** > **Dodaj narzędzie**

    ![Narzędzia do migracji serwera](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia w usłudze Azure Migrate

Migrowanie serwera migracji na platformę Azure działa z uproszczonego urządzenia maszyny Wirtualnej funkcji Hyper-V.

- Urządzenie wykonuje odnajdowania maszyn wirtualnych i wysyła dane wydajności i metadanych maszyny Wirtualnej do migracji serwera migracji platformy Azure.
- Urządzenia w tej samej również jest używana przez narzędzia do oceny migracji serwera w Azure.

Aby skonfigurować urządzenie:
- Jeśli wykonano to drugi samouczek, aby ocenić maszyny wirtualne funkcji Hyper-V, masz już skonfigurowane urządzenia podczas tego samouczka.
- Jeśli nie została zgodna z ten samouczek, należy teraz skonfigurować urządzenie. Aby to zrobić, możesz: 

    - Pobierz skompresowany wirtualnego dysku twardego funkcji Hyper-V w witrynie Azure portal.
    - Utworzyć to urządzenie, a następnie sprawdź, czy można nawiązać oceny Server migracji platformy Azure. 
    - Konfigurowanie urządzenia po raz pierwszy, a następnie zarejestruj go w projekcie usługi Azure Migrate.

    Postępuj zgodnie z instrukcjami w [w tym artykule](how-to-set-up-appliance-hyper-v.md) do skonfigurowania urządzenia.

## <a name="prepare-hyper-v-hosts"></a>Przygotowanie hostów funkcji Hyper-V

1. W projekcie usługi Azure Migrate > **serwerów**w **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **odnajdź**.
2. W **odnajdź maszyny** > **są maszynach zwirtualizowane?** , wybierz opcję **tak, przy użyciu funkcji Hyper-V**.
3. W **region docelowy**, wybierz region platformy Azure, do którego chcesz przeprowadzić migrację maszyn.
6. Wybierz **upewnij się, że region docelowy dla migracji jest nazwa regionu**.
7. Kliknij przycisk **tworzyć zasoby**. Spowoduje to utworzenie magazynu usługi Azure Site Recovery w tle.
    - Jeśli już skonfigurowano migracji za pomocą usługi Azure Migration migracji serwera, ta opcja nie będzie wyświetlane ponieważ zasoby zostały utworzone wcześniej.
    - Region docelowy dla tego projektu nie można zmienić po kliknięciu tego przycisku.
    - Wszystkie kolejne migracje są do tego regionu.
    
8. W **Serwery hosta funkcji Hyper-V przygotowanie**, Pobierz dostawcę replikacji funkcji Hyper-V i plik klucza rejestracji.
    - Klucz rejestracji jest wymagany do zarejestrowania hosta funkcji Hyper-V za pomocą usługi Azure Migration migracji serwera.
    - Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Pobierz dostawcą i kluczem](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Skopiuj plik Instalatora dostawcy i plik klucza rejestracji do każdego hosta funkcji Hyper-V (lub węzła klastra) uruchamianie maszyn wirtualnych, którą chcesz replikować.
5. Uruchom plik Instalatora na każdym hoście dostawcy zgodnie z opisem w następnej procedurze.
6. Po zainstalowaniu dostawcy na hostach w **odnajdź maszyny**, kliknij przycisk **Zakończ rejestrację**.

    ![Zakończ rejestrację](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Może upłynąć do 15 minut po finalizowanie rejestracji, dopóki odnalezione maszyny wirtualne są widoczne w procesie migracji serwera migracji platformy Azure. Ponieważ maszyny wirtualne są wykrywane, **odnalezione serwery** liczba rośnie wraz.

![Odnajdowanych serwerów](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Rejestrowanie hostów funkcji Hyper-V

Zainstaluj pobrany plik Instalatora (AzureSiteRecoveryProvider.exe) na każdy odpowiedniego hosta funkcji Hyper-V.

1. Uruchom plik Instalatora w każdym węźle hosta lub klastra dostawcy.
2. W Kreatorze instalacji dostawcy > **Microsoft Update**, zgoda na korzystanie z usługi Microsoft Update w celu sprawdzania aktualizacji dostawcy.
3. W **instalacji**Zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a wybierz **zainstalować**.
4. Po zakończeniu instalacji w Kreatorze rejestracji > **ustawienia magazynu**, wybierz opcję **Przeglądaj**, a następnie w **plik klucza**, wybierz plik klucza magazynu, który został pobrany.
5. W **ustawienia serwera Proxy**, określ, jak dostawca uruchomiony na hoście łączy się z Internetem.
    - Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę serwera proxy jako **http://ip-address** , lub **http://FQDN** . Serwery proxy HTTPS nie są obsługiwane.
   

6. Upewnij się, że dostawca może osiągnąć [wymaganych adresów URL](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. W **rejestracji**po zarejestrowaniu hosta, kliknij przycisk **Zakończ**.

## <a name="replicate-hyper-v-vms"></a>Replikowanie maszyn wirtualnych funkcji Hyper-V

Za pomocą odnajdywanie zostało zakończone możesz przystąpić do replikacji maszyn wirtualnych funkcji Hyper-V, na platformie Azure.

1. W projekcie usługi Azure Migrate > **serwerów**, **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikować**.
2. W **replikować**, > **ustawienia źródła** > **są maszynach zwirtualizowane?** , wybierz opcję **tak, przy użyciu funkcji Hyper-V**. Następnie kliknij przycisk **dalej: Maszyny wirtualne**.
3. W **maszyn wirtualnych**, wybrać maszyny, którą chcesz replikować.
    - Jeśli uruchamiasz ocenę dla maszyn wirtualnych, można zastosować zmiany rozmiaru maszyn wirtualnych i zaleceń (w warstwie premium/standardowa) typ dysku wyniki oceny. Aby to zrobić, w **zaimportować ustawienia migracji z oceny usługi Azure Migrate?** , wybierz opcję **tak** opcji.
    - Jeśli nie została wykonana oceny lub nie chcesz używać ustawień oceny, wybierz **nie** opcje.
    - Jeśli została wybrana do oceny, wybierz grupy maszyn wirtualnych, a nazwa oceny.

        ![Wybierz ocenę](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. W **maszyn wirtualnych**wyszukiwania dla maszyn wirtualnych, zgodnie z potrzebami i sprawdź poszczególnych maszyn wirtualnych, które mają zostać zmigrowane. Następnie kliknij przycisk **dalej: Docelowe ustawienia**.

    ![Wybierz maszyny wirtualne](./media/tutorial-migrate-hyper-v/select-vms.png)

5. W **docelowe ustawienia**, wybierz region docelowy, do którego można będzie przeprowadzić migrację, subskrypcję i grupę zasobów, w której maszyny wirtualne Azure będą znajdować się po zakończeniu migracji.
7. W **konto magazynu replikacji**, wybierz konto usługi Azure storage w taki sposób, w którym zreplikowane dane będą przechowywane na platformie Azure.
8. **Sieć wirtualna**, wybierz pozycję Azure sieci wirtualnej/podsieci do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
9. W **korzyść użycia hybrydowego platformy Azure**:

    - Wybierz **nie** Jeśli nie chcesz zastosować korzyść użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz **tak** Jeśli masz maszyny z systemem Windows Server, które są objęte mających aktywne subskrypcje i pakietem Software Assurance lub Windows Server i chcesz zastosować korzyść dla maszyn, którego przenosisz. Następnie kliknij przycisk **Next** (Dalej).

    ![Ustawienia obiektu docelowego](./media/tutorial-migrate-hyper-v/target-settings.png)

10. W **obliczenia**, sprawdź nazwę maszyny Wirtualnej, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności. Maszyny wirtualne muszą być zgodne z [wymagania dotyczące usługi Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Rozmiar maszyny Wirtualnej**: Jeśli korzystasz zaleceń dotyczących oceny, lista rozwijana rozmiar maszyny Wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybiera najlepsze dopasowanie w subskrypcji platformy Azure w oparciu o rozmiarze. Alternatywnie, wybierz ręczne rozmiar w **rozmiar maszyny Wirtualnej Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) dla maszyny Wirtualnej. Dysk systemu operacyjnego jest dysk systemu operacyjnego, programu rozruchowego oraz Instalatora. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w dostępności platformy Azure po migracji, należy określić zestaw. Zestaw musi być w docelowej grupie zasobów, które określisz dla migracji.

    ![Ustawienia obliczeń maszyny Wirtualnej](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. W **dysków**, określ, czy dyski maszyny Wirtualnej powinny być replikowane na platformę Azure i wybierz typ dysku (standardowy dysk SSD/dysk twardy lub dyski zarządzane w warstwie premium) na platformie Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Dyski można wykluczyć z replikacji.
    - Jeśli Wyklucz dyski będą obecne na maszynie Wirtualnej platformy Azure po migracji. 

    ![Dyski](./media/tutorial-migrate-hyper-v/disks.png)

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
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany dysków w środowisku lokalnym okresowo są replikowane do platformy Azure.

Można śledzić stan zadania w powiadomieniach portalu.

Możesz monitorować stan replikacji, klikając **replikowania serwerów** w **usługi Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Kiedy rozpoczyna się replikacja różnicowa, jest testowa migracja maszyn wirtualnych, można uruchomić przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, że w tym co najmniej raz dla każdej maszyny, przed przeprowadzeniem migracji.

- Uruchamianie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wywierania wpływu na komputerach lokalnych, które działać i nadal replikowane. 
- Migracja testowa symuluje migracji, tworząc Maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migracji z siecią wirtualną spoza środowiska produkcyjnego, w ramach subskrypcji platformy Azure).
- Test zreplikowanej maszyny Wirtualnej platformy Azure umożliwiają sprawdzanie poprawności migracji, wykonaj testowanie aplikacji i rozwiązać wszelkie problemy przed pełnej migracji.

Przeprowadzić migrację testu w następujący sposób:


1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **testu migracji serwerów**.

     ![Testowanie serwerów zmigrowane](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę Wirtualną, aby przetestować, a następnie kliknij przycisk **Test Migrowanie**.

    ![Migracja testowa](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. W **migracji testowej**, wybierz sieć wirtualną platformy Azure, w którym maszyna wirtualna platformy Azure zostaną umieszczone po zakończeniu migracji. Zaleca się użyć sieci wirtualnej spoza środowiska produkcyjnego.
4. **Migracja testowa** rozpoczyna się zadanie. Monitorowanie zadania w powiadomieniach portalu.
5. Po zakończeniu migracji, należy wyświetlić zmigrowanej maszyny Wirtualnej platformy Azure w **maszyn wirtualnych** w witrynie Azure portal. Nazwa komputera ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszyny Wirtualnej platformy Azure w **replikowania maszyn**i kliknij przycisk **wyczyścić migracji testowej**.

    ![Czyszczenie migracji](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, należy przeprowadzić migrację maszyn lokalnych.

1. W projekcie usługi Azure Migrate > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikowania serwerów**.

    ![Replikacja serwerów](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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
