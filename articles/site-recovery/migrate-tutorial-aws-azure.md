---
title: Migrowanie maszyn wirtualnych z usług AWS na platformę Azure za pomocą usługi Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób migrowania maszyn wirtualnych z systemem Windows działających w usługach Amazon Web Sevices (AWS) na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3ad4f46585be9cf61e3ef8343b5cb05308c972d6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrowanie maszyn wirtualnych usług Amazon Web Services (AWS) na platformę Azure

W tym samouczku pokazano, jak przeprowadzić migrację maszyn wirtualnych usługi Amazon Web Services (AWS) do maszyn wirtualnych platformy Azure za pomocą usługi Site Recovery. Podczas migrowania wystąpień usługi EC2 na platformę Azure maszyny wirtualne są traktowane tak, jakby były fizycznymi komputerami lokalnymi. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Weryfikowanie wymagań wstępnych
> * Przygotowywanie zasobów platformy Azure
> * Przygotowywanie wystąpień usługi EC2 usług AWS do migracji
> * Wdrażanie serwera konfiguracji
> * Włączanie replikacji maszyn wirtualnych
> * Testowanie trybu failover w celu upewnienia się, że wszystkie funkcje działają
> * Uruchamianie jednokrotnego przejścia w tryb failover na platformie Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że na maszynach wirtualnych, która mają zostać zmigrowane, jest uruchomiona obsługiwana wersja systemu operacyjnego. Obsługiwane systemy to: 
    - 64-bitowa wersja systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszym 
    - Windows Server 2012
    - Windows Server 2012 R2 
    - Windows Server 2016
    - Red Hat Enterprise Linux 6.7 (tylko wystąpienia z wirtualizacją sprzętową) wyłącznie ze sterownikami Citrix PV lub AWS PV. Wystąpienia ze sterownikami RedHat PV **nie są** obsługiwane.

- Usługa mobilności musi być zainstalowana na każdej maszynie wirtualnej, która ma być replikowana. 

> [!IMPORTANT]
> Usługa Site Recovery automatycznie instaluje tę usługę po włączeniu replikacji dla danej maszyny wirtualnej. Instalacja automatyczna wymaga przygotowania na wystąpieniach usługi EC2 konta, za pomocą którego usługa Site Recovery będzie uzyskiwać dostęp do maszyny wirtualnej. Możesz użyć domeny lub konta lokalnego. 
> - W przypadku maszyn wirtualnych z systemem Linux konto powinno być użytkownikiem głównym na serwerze źródłowym z systemem Linux. 
> - W przypadku maszyn wirtualnych z systemem Windows, jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej: w kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** dodaj wpis typu DWORD **LocalAccountTokenFilterPolicy** i ustaw jego wartość na 1.

- Potrzebne jest oddzielne wystąpienie usługi EC2, którego można używać jako serwera konfiguracji usługi Site Recovery. Na tym wystąpieniu musi być uruchomiony system Windows Server 2012 R2.

## <a name="prepare-azure-resources"></a>Przygotowywanie zasobów platformy Azure

Należy mieć przygotowanych kilka zasobów na platformie Azure do użytku przez zmigrowane wystąpienia usługi EC2, takich jak konto magazynu, magazyn i sieć wirtualna.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Obrazy replikowanych maszyn są przechowywane w usłudze Azure Storage. Maszyny wirtualne Azure tworzy się na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure.

1. W menu witryny [Azure Portal](https://portal.azure.com) kliknij pozycję **Utwórz zasób** > **Magazyn** > **Konto magazynu**.
2. Wprowadź nazwę konta magazynu. W tych samouczkach użyjemy nazwy **awsmigrated2017**. Nazwa musi mieć od 3 do 24 znaków długości, zawierać wyłącznie cyfry i małe litery oraz musi być unikatowa w obrębie platformy Azure.
3. Zachowaj wartości domyślne dla ustawień **Model wdrażania**, **Rodzaj konta**, **Wydajność** i **Wymagany bezpieczny transfer**.
5. Wybierz wartość domyślną **RA-GRS** w obszarze **Replikacja**.
6. Wybierz subskrypcję, której chcesz użyć w tym samouczku.
7. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. W tym przykładzie jako nazwy używamy ciągu **migrationRG**.
8. Jako lokalizację wybierz **Europę Zachodnią**.
9. Kliknij pozycję **Utwórz**, aby utworzyć konto magazynu.

### <a name="create-a-vault"></a>Tworzenie magazynu

1. W lewym panelu nawigacyjnym w witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Wszystkie usługi**, a następnie wyszukaj i wybierz pozycję **Magazyny usługi Recovery Services**.
2. Na stronie Magazyny usług Recovery Services kliknij pozycję **+ Dodaj** w lewym górnym rogu.
3. W obszarze **Nazwa** wpisz wartość *myVault*.
4. W obszarze **Subskrypcja** wybierz odpowiednią subskrypcję.
4. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej** i wybierz pozycję *migrationRG*.
5. W obszarze **Lokalizacja** wybierz pozycję *Europa Zachodnia*.
5. Aby szybko uzyskać dostęp do nowego magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
7. Po zakończeniu kliknij przycisk **Utwórz**.

Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

### <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Gdy po migracji (przejściu w tryb failover) zostaną utworzone maszyny wirtualne platformy Azure, zostaną one przyłączone do tej sieci.

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Utwórz zasób** > **Sieć** >
   **Sieć wirtualna**.
3. W obszarze **Nazwa** wpisz wartość *myMigrationNetwork*.
4. W obszarze **Przestrzeń adresowa** pozostaw wartość domyślną.
5. W obszarze **Subskrypcja** wybierz odpowiednią subskrypcję.
6. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej** i wybierz pozycję *migrationRG* z listy rozwijanej.
7. W obszarze **Lokalizacja** wybierz pozycję **Europa Zachodnia**.
8. W obszarach **Podsieć**, **Nazwa** i **Zakres adresów IP** pozostaw wartości domyślne.
9. Pozostaw funkcję **Punkty końcowe usługi** wyłączoną.
10. Po zakończeniu kliknij przycisk **Utwórz**.


## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury

Na stronie portalu swojego magazynu wybierz pozycję **Site Recovery** w sekcji **Wprowadzenie**, a następnie kliknij pozycję **Przygotuj infrastrukturę**.

### <a name="1-protection-goal"></a>1. Cel ochrony

Na stronie **Cel ochrony** wybierz następujące wartości:

|    |  |
|---------|-----------|
| Gdzie znajdują się maszyny? | **Środowisko lokalne**|
| Gdzie chcesz zreplikować maszyny? |**Platforma Azure**|
| Czy maszyny są zwirtualizowane? | **Bez wirtualizacji / inne**|

Gdy skończysz, kliknij przycisk **OK**, aby przejść do następnej sekcji.

### <a name="2-source-prepare"></a>2. Przygotowywanie źródła

Na stronie **Przygotowywanie źródła** kliknij pozycję **+ Serwer konfiguracji**.

1. Za pomocą wystąpienia usługi EC2 z systemem Windows Server 2012 R2 utwórz serwer konfiguracji i zarejestruj go w magazynie usługi Recovery Services.

2. Skonfiguruj serwer proxy na maszynie wirtualnej wystąpienia usługi EC2, której używasz jako serwera konfiguracji, aby możliwe było uzyskanie dostępu do [adresów URL usługi](site-recovery-support-matrix-to-azure.md).

3. Pobierz program [Microsoft Azure Site Recovery Unified Setup](http://aka.ms/unifiedinstaller_wus). Możesz pobrać go na maszynę lokalną, a następnie skopiować na maszynę wirtualną, której używasz jako serwera konfiguracji.

4. Kliknij przycisk **Pobierz**, aby pobrać klucz rejestracji magazynu. Skopiuj pobrany plik na maszynę wirtualną, której używasz jako serwera konfiguracji.

5. Na maszynie wirtualnej kliknij prawym przyciskiem myszy pobrany plik instalatora programu **Microsoft Azure Site Recovery Unified Setup** i wybierz polecenie **Uruchom jako administrator**.

    1. W obszarze **Przed rozpoczęciem** wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**, a następnie kliknij przycisk **Dalej**.
    2. W obszarze **Licencja oprogramowania innej firmy** zaznacz opcję **Akceptuję umowę licencyjną innej firmy**, a następnie kliknij przycisk **Dalej**.
    3. W obszarze **Rejestracja** kliknij pozycję Przeglądaj i przejdź do lokalizacji, w której umieszczono plik klucza rejestracji magazynu, a następnie kliknij przycisk **Dalej**.
    4. W obszarze **Ustawienia internetowe** wybierz pozycję **Połącz z usługą Azure Site Recovery bez serwera proxy**, a następnie kliknij przycisk **Dalej**.
    5. Na stronie **Sprawdzanie wymagań wstępnych** zostanie uruchomione sprawdzanie kilku elementów. Po zakończeniu kliknij przycisk **Dalej**.
    6. W obszarze **Konfiguracja programu MySQL** podaj wymagane hasła, a następnie kliknij przycisk **Dalej**.
    7. W obszarze **Szczegóły środowiska** wybierz pozycję **Nie**, ochrona maszyn wirtualnych programu VMware nie jest konieczna, a następnie kliknij przycisk **Dalej**.
    8. W obszarze **Lokalizacja instalacji** kliknij przycisk **Dalej**, aby zaakceptować wartości domyślne.
    9. W obszarze **Wybór sieci** kliknij przycisk **Dalej**, aby zaakceptować wartości domyślne.
    10. W obszarze **Podsumowanie** kliknij pozycję **Zainstaluj**.
    11. W obszarze **Postęp instalacji** wyświetlane są informacje dotyczące tego, na jakim etapie instalacji jesteś. Po zakończeniu kliknij przycisk **Zakończ**. Zostanie wyświetlone okno podręczne z informacją o możliwej konieczności ponownego uruchomienia. Kliknij przycisk **OK**. Zostanie również wyświetlone okno podręczne zawierające hasło połączenia z serwerem konfiguracji. Skopiuj to hasło do schowka i zapisz je w bezpiecznym miejscu.

6. Na maszynie wirtualnej uruchom plik **cspsconfigtool.exe**, aby utworzyć co najmniej jedno konto zarządzania na serwerze konfiguracji. Upewnij się, że konta zarządzania mają uprawnienia administratora na wystąpieniach usługi EC2, które chcesz zmigrować.

Po zakończeniu konfigurowania serwera konfiguracji wróć do portalu i wybierz utworzony przez siebie serwer jako **Serwer konfiguracji** i kliknij przycisk *OK** , aby przejść do kroku 3. Przygotowywanie celu.

### <a name="3-target-prepare"></a>3. Przygotowywanie celu

W tej sekcji wprowadzisz informacje dotyczące zasobów utworzonych w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources) we wcześniejszej części tego samouczka.

1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, której użyto w samouczku [Przygotowywanie platformy Azure](tutorial-prepare-azure.md).
2. Wybierz pozycję **Resource Manager** jako model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. Powinny to być zasoby utworzone w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources) we wcześniejszej części tego samouczka.
4. Gdy skończysz, kliknij przycisk **OK**.


### <a name="4-replication-settings-prepare"></a>4. Przygotowywanie ustawień replikacji

Aby móc włączyć replikację, musisz najpierw utworzyć zasady replikacji.

1. Kliknij pozycję **+ Replikuj i skojarz**.
2. W obszarze **Nazwa** wpisz wartość **myReplicationPolicy**.
3. Pozostaw resztę ustawień domyślnych bez zmian i kliknij przycisk **OK**, aby utworzyć zasady. Nowe zasady zostaną automatycznie skojarzone z serwerem konfiguracji.

### <a name="5-deployment-planning-select"></a>5. Wybieranie planowania wdrożenia

W obszarze **Czy ukończono planowanie wdrożenia?** wybierz z listy rozwijanej pozycję **Zrobię to później**, a następnie kliknij przycisk **OK**.

Gdy wykonasz czynności opisane we wszystkich 5 punktach sekcji **Przygotowywanie infrastruktury**, kliknij przycisk **OK**.


## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla każdej maszyny wirtualnej, która ma zostać zmigrowana. Po włączeniu replikacji usługa Site Recovery automatycznie zainstaluje usługę mobilności.

1. Otwórz [portal Azure](htts://portal.azure.com).
1. Otwórz stronę swojego magazynu, a następnie w obszarze **Wprowadzenie** kliknij pozycję **Site Recovery**.
2. W obszarze **Dla maszyn lokalnych i maszyn wirtualnych platformy Azure** kliknij pozycję **Krok 1. Replikowanie aplikacji**. Na stronach kreatora wprowadź następujące informacje i kliknij przycisk **OK** na każdej ze stron po wprowadzeniu informacji:
    - 1. Konfigurowanie źródła:

    |  |  |
    |-----|-----|
    | Źródło: | **Lokalnie**|
    | Lokalizacja źródła:| Nazwa wystąpienia usługi EC2 serwera konfiguracji.|
    |Typ maszyny: | **Maszyny fizyczne**|
    | Serwer przetwarzania: | Wybierz serwer konfiguracji z listy rozwijanej.|

    - 2. Konfigurowanie celu

    |  |  |
    |-----|-----|
    | Cel: | Pozostaw wartość domyślną.|
    | Subskrypcja: | Wybierz subskrypcję, której używasz.|
    | Grupa zasobów po przełączeniu w tryb failover:| Użyj grupy zasobów utworzonej w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).|
    | Model wdrażania po przełączeniu w tryb failover: | Wybierz pozycję **Resource Manager**.|
    | Konto magazynu: | Wybierz konto magazynu utworzone w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).|
    | Sieć platformy Azure: | Wybierz pozycję **Konfiguruj teraz dla wybranych maszyn**.|
    | Sieć platformy Azure po przełączeniu w tryb failover: | Wybierz sieć utworzoną w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).|
    | Podsieć: | Wybierz z listy rozwijanej pozycję **domyślna**.|

    - 3. Wybieranie maszyn fizycznych

        Kliknij pozycję **+Maszyna fizyczna**, wypełnij pola **Nazwa**, **Adres IP** i **Typ systemu operacyjnego** dotyczące wystąpienia usługi EC2, które ma zostać zmigrowane, a następnie kliknij przycisk **OK**.

    - 4. Konfigurowanie właściwości

        Wybierz z listy rozwijanej konto utworzone na serwerze konfiguracji i kliknij przycisk **OK**.

    - 5. Konfigurowanie ustawień replikacji

        Upewnij się, że zasady replikacji wybrane z listy rozwijanej to zasady **myReplicationPolicy**, a następnie kliknij przycisk **OK**.

3. Po zakończeniu pracy kreatora kliknij pozycję **Włącz replikację**.


Możesz śledzić postępy zadania **Włącz ochronę** w obszarze **Monitorowanie i raporty** > **Zadania** > **Zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.        

Po włączeniu replikacji maszyny wirtualnej zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
2. Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
3. Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

W portalu wykonaj próbę przejścia w tryb failover w następujący sposób:

1. Na stronie swojego magazynu przejdź do pozycji **Chronione elementy** > **Zreplikowane elementy**> kliknij maszynę wirtualną > **Test pracy w trybie failover**.

2. Wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:
    - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
    - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
    - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.
3. W obszarze **Test pracy w trybie failover** wybierz sieć platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu w tryb failover. Powinna to być sieć utworzona w sekcji [Przygotowywanie zasobów platformy Azure](#prepare-azure-resources).
4. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Możesz śledzić postęp, klikając maszynę wirtualną i otwierając jej właściwości. Lub możesz kliknąć zadanie **Test pracy w trybie failover** na stronie magazynu w obszarze **Monitorowanie i raporty** > **Zadania** >
   **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna ma prawidłowy rozmiar, jest połączona z odpowiednią siecią i jest uruchomiona.
6. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure.
7. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowania trybu failover, kliknij pozycję **Wyczyść test pracy w trybie failover** w planie odzyskiwania. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut.


## <a name="migrate-to-azure"></a>Migracja na platformę Azure

Uruchom rzeczywisty tryb failover dla wystąpień usługi EC2, aby zmigrować je do maszyn wirtualnych platformy Azure.

1. W obszarze **Chronione elementy** > **Zreplikowane elementy** kliknij pozycję Wystąpienia usług AWS > **Tryb failover**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, którego chcesz użyć do przełączenia w tryb failover. Wybierz najnowszy punkt odzyskiwania.
3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**, jeśli usługa Site Recovery ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.
4. Sprawdź, czy maszyna wirtualna jest wyświetlana w obszarze **Zreplikowane elementy**.
5. Kliknij prawym przyciskiem myszy każdą maszynę wirtualną i wybierz polecenie **Zakończ migrację**. Spowoduje to zakończenie procesu migracji, zatrzymanie replikacji maszyny wirtualnej usług AWS oraz zatrzymanie naliczania opłat za usługę Site Recovery dla maszyny wirtualnej.

    ![Kończenie migracji](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: przed uruchomieniem trybu failover replikacja maszyny wirtualnej zostanie zatrzymana. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.  




## <a name="next-steps"></a>Następne kroki

W tym temacie opisano migrowanie wystąpień usługi EC2 usług AWS do maszyn wirtualnych platformy Azure. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczków dotyczących maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Windows](../virtual-machines/windows/tutorial-manage-vm.md)
