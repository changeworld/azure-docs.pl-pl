---
title: Migrowanie lokalnych maszyn fizycznych lub maszynach zwirtualizowanych na platformie Azure przy użyciu usługi Azure Migration migracji serwera | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób migrowania lokalnej maszyny fizyczne lub zwirtualizowane maszyn na platformę Azure za pomocą usługi Azure Migration migracji serwera.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 006f7270570eba17fb44421b1c181d9ad68ad2ee
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854178"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrowanie serwerów fizyczne lub zwirtualizowane na platformie Azure 

W tym artykule pokazano, jak migrować serwery fizyczne lub zwirtualizowane na platformę Azure. Narzędzie do migracji serwera migracji platformy Azure oferuje migracji serwerów fizycznych i zwirtualizowanych przy użyciu replikacji opartej na agenta. Za pomocą tego narzędzia, można migrować szeroką gamę maszyn na platformę Azure:

- Migrowanie lokalnych serwerów fizycznych.
- Migrowanie maszyn wirtualnych, zwirtualizowanych według platformy, takie jak Xen, KVM.
- Migrowanie funkcji Hyper-V lub maszyny wirtualne VMware. Jest to przydatne, jeśli z jakiegoś powodu nie możesz się korzystać z tego przepływu Standardowa migracja, która umożliwia migrację serwera migracji platformy Azure [funkcji Hyper-V](tutorial-migrate-hyper-v.md), [VMware bez wykorzystania agentów](tutorial-migrate-vmware.md) migracji lub [VMware oparte na agentach](tutorial-migrate-vmware-agent.md) migracji.
- Migrowanie maszyn wirtualnych w chmurach prywatnych.
- Migrowanie maszyn wirtualnych działających w chmurach publicznych, takich jak Amazon Web Services (AWS) lub systemu nazw domen (GCP, Google Cloud Platform).


[Usługa Azure Migrate](migrate-services-overview.md) zapewnia wystąpień koncentratorów do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i maszyn wirtualnych w chmurze, na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Przygotowywanie platformy Azure do migracji za pomocą narzędzia migracji serwera migracji platformy Azure.
> * Sprawdź wymagania dotyczące maszyn, że użytkownik chce migrować i przygotowywanie maszyny dla urządzenia replikacji usługi Azure Migrate służy do odnajdywania i migrację maszyn na platformę Azure.
> * Dodaj narzędzie do migracji serwera migracji platformy Azure w Centrum migracji platformy Azure.
> * Skonfiguruj urządzenie replikacji.
> * Instalowanie usługi mobilności na maszynach, które mają zostać zmigrowane.
> * Włącz replikację.
> * Uruchamianie migracji testowej, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Wykonywanie pełnej migracji na platformę Azure.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania dla scenariusza, aby szybko skonfigurować się weryfikacji koncepcji. Samouczki użyj opcji domyślnych, jeśli jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Aby uzyskać szczegółowe instrukcje zapoznaj się z How-tos dla usługi Azure Migrate.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przegląd](migrate-architecture.md) architekturę migracji.
2. Upewnij się, że Twoje konto platformy Azure jest przypisana rola Współautor maszyny wirtualnej tak, że masz uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapis na platformie Azure dysku zarządzanego. 

3. [Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). W przypadku replikacji na platformie Azure maszyn wirtualnych platformy Azure są tworzone i połączone z siecią platformy Azure, które można określić podczas konfigurowania migracji.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Skonfiguruj uprawnienia platformy Azure, można było migrować przy użyciu usługi Azure Migration migracji serwera.

- **Utwórz projekt**: Twoje konto platformy Azure wymaga uprawnień do tworzenia projektu usługi Azure Migrate. 
- **Zarejestruj urządzenie replikacji usługi Azure Migrate**: Urządzenie replikacji tworzącego i rejestrującego aplikację usługi Azure Active Directory w ramach Twojego konta platformy Azure. Delegowanie uprawnień dla tego.
- **Tworzenie magazynu kluczy**: Aby przeprowadzić migrację maszyn, usługa Azure Migrate tworzy usługę Key Vault w grupie zasobów, aby zarządzać kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, potrzebujesz uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt usługi Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W witrynie Azure portal Otwórz subskrypcję, a następnie wybierz **kontrola dostępu (IAM)** .
2. W **Sprawdź dostęp**, Znajdź odpowiednie konto i kliknij go, aby wyświetlić uprawnienia.
3. Musisz mieć **Współautor** lub **właściciela** uprawnienia.
    - Jeśli bezpłatne konto platformy Azure jest właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Współpracuj z właścicielem Aby przypisać rolę.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Przypisz uprawnienia, aby zarejestrować urządzenie replikacji

W przypadku migracji opartej o agentów delegować uprawnienia dla Azure migracji migracji serwera do tworzenia i rejestrowania aplikacji usługi Azure AD w ramach Twojego konta. Możesz przypisywać uprawnienia przy użyciu jednej z następujących metod:

- Administrator globalny/dzierżawy mogą udzielać uprawnień użytkowników w dzierżawie, aby utworzyć i zarejestrować aplikacje usługi Azure AD.
- Administrator dzierżawy/globalny przypisać rolę dewelopera aplikacji (który ma uprawnienia) do konta.

Warto zauważyć, że:

- Aplikacje nie mają żadnych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
- Wystarczy tylko te uprawnienia podczas rejestrowania nowego urządzenia replikacji. Po skonfigurowaniu urządzenia replikacji można usunąć uprawnienia. 


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Administrator dzierżawy/globalny może nadać uprawnienia w następujący sposób

1. W usłudze Azure AD administratora globalnego/dzierżawy należy przejść do **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
2. Administrator powinien być ustawiony **rejestracje aplikacji** do **tak**.

    ![Uprawnienia platformy Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> To ustawienie domyślne, które nie są poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisanie roli aplikacji dla deweloperów 

Administrator dzierżawy/globalny przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-permissions-to-create-key-vault"></a>Przypisywanie uprawnień do tworzenia usługi Key Vault

Przypisz uprawnienia do przypisywania ról w grupie zasobów, w której usługa Azure Migrate znajduje się projekt, w następujący sposób:

1. W tej grupie zasobów w witrynie Azure portal wybierz **kontrola dostępu (IAM)** .
2. W **Sprawdź dostęp**, Znajdź odpowiednie konto i kliknij go, aby wyświetlić uprawnienia. Potrzebujesz **właściciela** (lub **Współautor** i **Administrator dostępu użytkowników**) uprawnień.
3. Jeśli nie masz wymaganych uprawnień, należy zażądać ich z właścicielem grupy zasobów. 

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

### <a name="check-machine-requirements-for-migration"></a>Sprawdź wymagania maszyny do migracji

Sprawdź, czy maszyny są zgodne z wymaganiami dotyczącymi migracji na platformę Azure. 

> [!NOTE]
> Oparte na agentach migracji za pomocą usługi Azure Migration migracji serwera jest oparta na funkcjach usługi Azure Site Recovery. Dokumentacja usługi Site Recovery połączyć pewnych wymagań.

1. [Zweryfikuj](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) wymagania dotyczące serwerów VMware.
2. [Sprawdź](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) maszyn wirtualnych obsługują wymagania dotyczące migracji.
3. Sprawdź ustawienia maszyny Wirtualnej. Lokalne maszyny wirtualne można replikować do platformy Azure muszą być zgodne z [wymagania maszyny Wirtualnej platformy Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotowywanie maszyny dla urządzenia replikacji

Migrowanie serwera migracji na platformę Azure używa urządzenia replikacji można replikować maszyny do platformy Azure. Urządzenie replikacji działa następujące składniki.

- **Serwer konfiguracji**: Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
- **Serwer przetwarzania**: Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. 

Przed rozpoczęciem należy przygotować maszynę systemu Windows Server 2016 do obsługi urządzenia replikacji. Maszyny powinny być zgodne z [tych wymagań](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodaj narzędzie do migracji serwera migracji platformy Azure

Skonfiguruj projekt usługi Azure Migrate, a następnie dodaj narzędzie do migracji serwera migracji platformy Azure, do niego.

1. W witrynie Azure portal > **wszystkich usług**, wyszukaj **usługi Azure Migrate**.
2. W obszarze **usług**, wybierz opcję **usługi Azure Migrate**.
3. W **Przegląd**, kliknij przycisk **oceny i migracji serwerów**.
4. W obszarze **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **oceny i migracji serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. W **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **Dodawanie narzędzi**.
6. W **projektu migracji**, a następnie wybierz swoją subskrypcję platformy Azure i Utwórz grupę zasobów, jeśli nie masz.
7. W **Project Details**, określ nazwę projektu i lokalizacji geograficznej, w której chcesz utworzyć projekt, a następnie kliknij przycisk **dalej**

    ![Utwórz projekt usługi Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Projekt usługi Azure Migrate można utworzyć w dowolnym z tych obszarów geograficznych.

    **Lokalizacja geograficzna** | **Region**
    --- | ---
    Azja | Azja Południowo-Wschodnia
    Europa | Europa Północna lub Europa Zachodnia
    Stany Zjednoczone | Wschodnie stany USA lub Zachodnio-środkowe stany USA

    Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Można wybrać dowolny region docelowy, aby uzyskać rzeczywistą migrację.
8. W **narzędzia do oceny wybierz**, wybierz opcję **pominąć Dodawanie narzędzie do oceny teraz** > **dalej**.
9. W **narzędzie do migracji wybierz**, wybierz opcję **usługi Azure Migrate: Migracja serwera** > **dalej**.
10. W **Przejrzyj + Dodaj narzędzia**, przejrzyj ustawienia i kliknij przycisk **Dodawanie narzędzi**
11. Po dodaniu narzędzie, pojawia się w projekcie usługi Azure Migrate > **serwerów** > **narzędzia migracji**.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia replikacji

Pierwszym krokiem migracji jest skonfigurować urządzenie replikacji. Pobierz plik Instalatora dla urządzenia i uruchomić ją na [maszyny przygotowanym](#prepare-a-machine-for-the-replication-appliance). Po zainstalowaniu urządzenia należy zarejestrować za pomocą usługi Azure Migration migracji serwera.


### <a name="download-the-replication-appliance-installer"></a>Pobierz Instalator urządzenia replikacji

1. W projekcie usługi Azure Migrate > **serwerów**w ***usługi Azure Migrate: Migracja serwera**, kliknij przycisk **odnajdź**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. W **odnajdź maszyny** > **są maszynach zwirtualizowane?** , kliknij przycisk **bez wirtualizacji/inne**.
4. W **region docelowy**, wybierz region platformy Azure, do którego chcesz przeprowadzić migrację maszyn.
5. Wybierz **upewnij się, że region docelowy dla migracji jest nazwa regionu**.
6. Kliknij przycisk **tworzyć zasoby**. Spowoduje to utworzenie magazynu usługi Azure Site Recovery w tle.
    - Jeśli migrację, korzystając z usługi Azure Migration migracji serwera został już skonfigurowany, opcja docelowej nie można skonfigurować, ponieważ zasoby zostały utworzone wcześniej.
    - Region docelowy dla tego projektu nie można zmienić po kliknięciu tego przycisku.
    - Wszystkie kolejne migracje są do tego regionu.

7. W **czy chcesz zainstalować nowe urządzenie replikacji?** , wybierz opcję **instalowanie urządzenia replikacji**.
9. W **pobranie i zainstalowanie oprogramowania urządzenia replikacji**, Pobierz Instalator urządzenia i klucz rejestracji. Należy do klucza do zarejestrowania urządzenia. Klucz jest ważny przez pięć dni po jej pobraniu.

    ![Pobieranie dostawcy](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Skopiuj plik instalacyjny urządzenia i plikiem klucza do systemu Windows Server 2016 maszyna, na której został utworzony dla urządzenia.
11. Uruchom plik instalacji urządzenia replikacji, zgodnie z opisem w następnej procedurze.
12. Gdy urządzenie zostanie ponownie uruchomiony po zakończeniu instalacji, w **odnajdź maszyny**, wybierz nowe urządzenie na **wybierz serwer konfiguracji**i kliknij przycisk **Zakończ rejestrację**. Zakończ rejestrację wykonuje kilka zadań końcowych przygotować urządzenia replikacji.

    ![Zakończ rejestrację](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Może upłynąć do 15 minut po finalizowanie rejestracji, dopóki odnalezione maszyny są wyświetlane w procesie migracji serwera migracji platformy Azure. Ponieważ maszyny wirtualne są wykrywane, **odnalezione serwery** liczba rośnie wraz.

![Odnajdowanych serwerów](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalowanie usługi mobilności

Na komputerach, które chcesz przeprowadzić migrację należy zainstalować agenta usługi mobilności. Pliki instalacyjne agenta są dostępne na urządzeniu replikacji. Znajdź odpowiednie Instalatora i zainstaluj agenta na każdym komputerze, który chcesz przeprowadzić migrację. To w następujący sposób:

1. Zaloguj się do urządzenia replikacji.
2. Przejdź do **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Znajdź Instalator dla systemu operacyjnego maszyny i jego wersji. Przegląd [obsługiwane systemy operacyjne](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Skopiuj plik Instalatora na komputerze, na którym chcesz przeprowadzić migrację.
5. Upewnij się, że hasło, który został wygenerowany podczas wdrażania urządzenia.
    - Store pliku w pliku tekstowym tymczasowego na komputerze.
    - Możesz uzyskać hasło na urządzeniu replikacji. W wierszu polecenia Uruchom polecenie **C:\ProgramData\ASR\svsystems\bin\genpassphrase.exe - v** Aby wyświetlić bieżące hasło.
    - Nie Generuj ponownie hasło. Spowoduje to przerwanie łączności i trzeba będzie ponownie zarejestrować urządzenie w replikacji.


### <a name="install-on-windows"></a>Instalowanie w systemie Windows

1. Wyodrębnij zawartość pliku Instalatora do folderu lokalnego (np. C:\Temp) na komputerze, w następujący sposób:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Uruchom Instalatora usługi mobilności:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Rejestracja agenta przy użyciu urządzenia replikacji:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalowanie w systemie Linux

1. Wyodrębnij zawartość pliku Instalatora tar do folderu lokalnego (na przykład /tmp/MobSvcInstaller) na komputerze, w następujący sposób:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Uruchom skrypt Instalatora:
    ```
    sudo ./install -r MS -q
    ```
3. Rejestracja agenta przy użyciu urządzenia replikacji:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikowanie maszyn

1. W projekcie usługi Azure Migrate > **serwerów**, **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikować**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. W **replikować**, > **ustawienia źródła** > **są maszynach zwirtualizowane?** , wybierz opcję **tak, przy użyciu oprogramowania VMware vSphere**.
3. W **lokalne urządzenie**, wybierz nazwę usługi Azure Migrate urządzenia, który został ustawiony.
4. W **serwera vCenter**, określ nazwę serwera vCenter, zarządzanie maszynami wirtualnymi lub serwera vSphere, na którym znajdują się maszyny wirtualne.
5. W **serwera przetwarzania**, wybierz nazwę urządzenia replikacji.
6. W **poświadczenia gościa**, określ konto administratora maszyny Wirtualnej, która będzie służyć do instalacji wypychanej usługi mobilności. W tym samouczku instalujemy usługi mobilności ręcznie, więc można dodać żadnego konta fikcyjnego z rolą. Następnie kliknij przycisk **dalej: Maszyny wirtualne**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. W **maszyn wirtualnych**w **zaimportować ustawienia migracji z oceny?** , pozostaw domyślne ustawienie **nie będzie określić ustawienia migracji ręcznie**.
8. Sprawdź poszczególne maszyny Wirtualne, które mają zostać zmigrowane. Następnie kliknij przycisk **dalej: Docelowe ustawienia**.

    ![Wybierz maszyny wirtualne](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. W **docelowe ustawienia**, wybierz subskrypcję i docelowy region, do którego można będzie migracji i określ grupę zasobów, w której maszyny wirtualne Azure będą znajdować się po zakończeniu migracji.
10. W **sieci wirtualnej**, wybierz pozycję Azure sieci wirtualnej/podsieci do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W **korzyść użycia hybrydowego platformy Azure**:

    - Wybierz **nie** Jeśli nie chcesz zastosować korzyść użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Wybierz **tak** Jeśli masz maszyny z systemem Windows Server, które są objęte mających aktywne subskrypcje i pakietem Software Assurance lub Windows Server i chcesz zastosować korzyść dla maszyn, którego przenosisz. Następnie kliknij przycisk **Next** (Dalej).

    ![Ustawienia obiektu docelowego](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. W **obliczenia**, sprawdź nazwę maszyny Wirtualnej, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności. Maszyny wirtualne muszą być zgodne z [wymagania dotyczące usługi Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **Rozmiar maszyny Wirtualnej**: Domyślnie migracja serwera migracji platformy Azure wybiera najlepsze dopasowanie w subskrypcji platformy Azure w oparciu o rozmiarze. Alternatywnie, wybierz ręczne rozmiar w **rozmiar maszyny Wirtualnej Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) dla maszyny Wirtualnej. Dysk systemu operacyjnego jest dysk systemu operacyjnego, programu rozruchowego oraz Instalatora. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w dostępności platformy Azure po migracji, należy określić zestaw. Zestaw musi być w docelowej grupie zasobów, które określisz dla migracji.

    ![Ustawienia obliczeń](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. W **dysków**, określ, czy dyski maszyny Wirtualnej powinny być replikowane na platformę Azure i wybierz typ dysku (dysk twardy na dysk SSD/lub premium zarządzane dyski w warstwie standardowa) na platformie Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Dyski można wykluczyć z replikacji.
    - Jeśli Wyklucz dyski będą obecne na maszynie Wirtualnej platformy Azure po migracji. 

    ![Ustawienia dysku](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. W **Przejrzyj i Rozpocznij replikację**, przejrzyj ustawienia i kliknij przycisk **replikować** rozpoczyna się Replikacja początkowa dla serwerów.

> [!NOTE]
> Możesz zaktualizować ustawienia replikacji w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzaj** > **replikowania maszyn**. Nie można zmienić ustawienia, po uruchomieniu replikacji.



## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po kliknięciu **replikować** rozpoczyna się zadanie Rozpocznij replikację. 
- Zadanie replikacji Start zakończy się pomyślnie, używany przez maszyny rozpocząć ich replikacji początkowej na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany dysków w środowisku lokalnym okresowo są replikowane do repliki dysków na platformie Azure.


Można śledzić stan zadania w powiadomieniach portalu.

Możesz monitorować stan replikacji, klikając **replikowania serwerów** w **usługi Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Kiedy rozpoczyna się replikacja różnicowa, jest testowa migracja maszyn wirtualnych, można uruchomić przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, że w tym co najmniej raz dla każdej maszyny, przed przeprowadzeniem migracji.

- Uruchamianie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wywierania wpływu na komputerach lokalnych, które działać i nadal replikowane. 
- Migracja testowa symuluje migracji, tworząc Maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migracji z siecią wirtualną spoza środowiska produkcyjnego, w ramach subskrypcji platformy Azure).
- Test zreplikowanej maszyny Wirtualnej platformy Azure umożliwiają sprawdzanie poprawności migracji, wykonaj testowanie aplikacji i rozwiązać wszelkie problemy przed pełnej migracji.

Przeprowadzić migrację testu w następujący sposób:


1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **testu migracji serwerów**.

     ![Testowanie serwerów zmigrowane](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę Wirtualną, aby przetestować, a następnie kliknij przycisk **Test Migrowanie**.

    ![Migracja testowa](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. W **migracji testowej**, wybierz sieć wirtualną platformy Azure, w którym maszyna wirtualna platformy Azure zostaną umieszczone po zakończeniu migracji. Zaleca się użyć sieci wirtualnej spoza środowiska produkcyjnego.
4. **Migracja testowa** rozpoczyna się zadanie. Monitorowanie zadania w powiadomieniach portalu.
5. Po zakończeniu migracji, należy wyświetlić zmigrowanej maszyny Wirtualnej platformy Azure w **maszyn wirtualnych** w witrynie Azure portal. Nazwa komputera ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszyny Wirtualnej platformy Azure w **replikowania maszyn**i kliknij przycisk **wyczyścić migracji testowej**.

    ![Czyszczenie migracji](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, należy przeprowadzić migrację maszyn lokalnych.

1. W projekcie usługi Azure Migrate > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikowania serwerów**.

    ![Replikacja serwerów](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

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
    - Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Kolejne kroki

Badanie [podróży migracji do chmury](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) w ramach wdrażania chmury platformy Azure.
