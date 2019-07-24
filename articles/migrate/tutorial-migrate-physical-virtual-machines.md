---
title: Migrowanie lokalnych maszyn fizycznych lub maszyn wirtualnych na platformę Azure za pomocą migracji Azure Migrate Server | Microsoft Docs
description: W tym artykule opisano sposób migrowania lokalnych maszyn fizycznych lub maszyn wirtualnych na platformę Azure przy użyciu migracji Azure Migrate Server.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 728bf785edebcd17599b6a56edea1e26ed2d2fbc
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311789"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrowanie serwerów fizycznych lub zwirtualizowanych na platformę Azure 

W tym artykule pokazano, jak przeprowadzić migrację fizycznych lub zwirtualizowanych serwerów na platformę Azure. Narzędzie migracji Azure Migrate Server oferuje migrację serwerów fizycznych i zwirtualizowanych przy użyciu replikacji opartej na agentach. Za pomocą tego narzędzia można migrować szeroką gamę maszyn na platformę Azure:

- Migruj lokalne serwery fizyczne.
- Migrowanie maszyn wirtualnych zwirtualizowanych przez platformy takie jak Xen, KVM.
- Migrowanie maszyn wirtualnych funkcji Hyper-V lub VMware. Jest to przydatne, jeśli z jakiegoś powodu nie można użyć standardowego przepływu migracji, który Azure Migrate migracji serwera dla [funkcji Hyper-V](tutorial-migrate-hyper-v.md), migracji [bez agenta VMware](tutorial-migrate-vmware.md) lub migracji [opartej na agencie VMware](tutorial-migrate-vmware-agent.md) .
- Migrowanie maszyn wirtualnych działających w chmurach prywatnych.
- Migrowanie maszyn wirtualnych działających w chmurach publicznych, takich jak Amazon Web Services (AWS) lub Google Cloud Platform (GCP).


[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz wystąpień maszyn wirtualnych w chmurze do platformy Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Przygotuj platformę Azure do migracji za pomocą narzędzia migracji Azure Migrate Server.
> * Sprawdź wymagania dotyczące maszyn, które mają zostać poddane migracji, i przygotuj maszynę do Azure Migrate urządzenia do replikacji, które służy do odnajdywania i migrowania maszyn na platformę Azure.
> * Dodaj narzędzie do migracji Azure Migrate Server w centrum Azure Migrate.
> * Skonfiguruj urządzenie do replikacji.
> * Zainstaluj usługę mobilności na maszynach, które chcesz zmigrować.
> * Włącz replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z tematem Porady dotyczące Azure Migrate.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przejrzyj](migrate-architecture.md) architekturę migracji.
2. Upewnij się, że Twoje konto platformy Azure ma przypisaną rolę współautor maszyny wirtualnej, dzięki czemu masz uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapisz na dysku zarządzanym platformy Azure. 

3. [Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Podczas replikowania na platformę Azure maszyny wirtualne platformy Azure są tworzone i dołączane do sieci platformy Azure, którą określisz podczas konfigurowania migracji.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Skonfiguruj uprawnienia platformy Azure przed migracją do Azure Migrate migracji serwera.

- **Utwórz projekt**: Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu Azure Migrate. 
- **Zarejestruj urządzenie replikacji Azure Migrate**: Urządzenie do replikacji tworzy i rejestruje aplikację Azure Active Directory na koncie platformy Azure. Delegowanie uprawnień dla tego elementu.
- **Utwórz Key Vault**: Aby przeprowadzić migrację maszyn, Azure Migrate tworzy Key Vault w grupie zasobów, aby zarządzać kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, musisz mieć uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia  współautora lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Przypisywanie uprawnień do rejestrowania urządzenia replikacji

W przypadku migracji opartej na agencie delegowanie uprawnień do migracji serwera Azure Migrate do tworzenia i rejestrowania aplikacji usługi Azure AD na Twoim koncie. Uprawnienia można przypisywać przy użyciu jednej z następujących metod:

- Dzierżawa/Administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby tworzyć i rejestrować aplikacje usługi Azure AD.
- Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji (z uprawnieniami) do konta.

Warto zauważyć, że:

- Aplikacje nie mają żadnych innych uprawnień dostępu do subskrypcji innych niż opisane powyżej.
- Te uprawnienia są potrzebne tylko podczas rejestrowania nowego urządzenia replikacji. Po skonfigurowaniu urządzenia replikacji można usunąć uprawnienia. 


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawy/Administrator globalny mogą udzielić uprawnień w następujący sposób

1. W usłudze Azure AD dzierżawca/Administrator globalny powinien przejść do**ustawień użytkownika** **Azure Active Directory** > **Użytkownicy** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **wartość tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisywanie roli Deweloper aplikacji 

Dzierżawa/Administrator globalny może przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Przypisywanie uprawnień do tworzenia Key Vault

Przypisz uprawnienia do przypisywania ról do grupy zasobów, w której znajduje się projekt Azure Migrate, w następujący sposób:

1. W grupie zasobów w Azure Portal wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia. Wymagane są uprawnienia **właściciela** (  lub współautora oraz **administrator dostępu użytkowników**).
3. Jeśli nie masz wymaganych uprawnień, poproś ich od właściciela grupy zasobów. 

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

### <a name="check-machine-requirements-for-migration"></a>Sprawdź wymagania dotyczące maszyn pod kątem migracji

Upewnij się, że maszyny są zgodne z wymaganiami dotyczącymi migracji na platformę Azure. 

> [!NOTE]
> Migracja oparta na agencie z migracją Azure Migrate Server jest oparta na funkcjach Azure Site Recovery usługi. Niektóre wymagania mogą być połączone z dokumentacją Site Recovery.

1. [Zweryfikuj](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) wymagania dotyczące serwerów VMware.
2. [Sprawdź](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) Wymagania dotyczące migracji maszyn wirtualnych.
3. Sprawdź ustawienia maszyny wirtualnej. Lokalne maszyny wirtualne replikowane na platformę Azure muszą być zgodne z [wymaganiami maszyny wirtualnej platformy Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotuj komputer do urządzenia replikacji

Migracja serwera Azure Migrate przy użyciu urządzenia replikacji umożliwia replikowanie maszyn na platformę Azure. Urządzenie replikacji uruchamia następujące składniki.

- **Serwer konfiguracji**: Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
- **Serwer przetwarzania**: Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. 

Przed rozpoczęciem należy przygotować maszynę z systemem Windows Server 2016 do hostowania urządzenia replikacji. Komputer powinien spełniać [te wymagania](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji Azure Migrate Server

Skonfiguruj projekt Azure Migrate, a następnie Dodaj do niego narzędzie do migracji Azure Migrate Server.

1. W Azure Portal > **wszystkie usługi**Znajdź **Azure Migrate**.
2. W obszarze **usługi**wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
4. W obszarze **odnajdywanie, ocenianie i Migrowanie serwerów**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. W **odnajdywaniu, oceniania i migracji serwerów**kliknij przycisk **Dodaj narzędzia**.
6. W obszarze **Migrowanie projektu**wybierz subskrypcję platformy Azure i Utwórz grupę zasobów, jeśli jej nie masz.
7. W obszarze **szczegóły projektu**Określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt, a następnie kliknij przycisk **dalej** .

    ![Tworzenie projektu Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Projekt Azure Migrate można utworzyć w dowolnym z tych lokalizacje geograficzne.

    **Lokalizacja geograficzna** | **Region**
    --- | ---
    Azja | Azja Południowo-Wschodnia
    Europa | Europa Północna lub Europa Zachodnia
    Stany Zjednoczone | Wschodnie stany USA lub Zachodnio-środkowe stany USA

    Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Można wybrać dowolny region docelowy dla rzeczywistej migracji.
8. W **narzędziu Wybierz ocenę**wybierz pozycję **Pomiń Dodawanie narzędzia do oceny teraz** >  **.**
9. W obszarze **Wybieranie narzędzia migracji**wybierz **pozycję Azure Migrate: **Następnie**należy**przeprowadzić migrację > serwera.
10. W oknie **Recenzja + Dodawanie narzędzi**przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia** .
11. Po dodaniu narzędzia pojawia się ono w Azure Migrate**Narzędzia migracji** **serwerów** > > projektu.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia do replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia do replikacji. Pobierzesz plik Instalatora dla urządzenia i uruchomisz go na [przygotowanej maszynie](#prepare-a-machine-for-the-replication-appliance). Po zainstalowaniu urządzenia należy zarejestrować je na Azure Migrate migracji serwera.


### <a name="download-the-replication-appliance-installer"></a>Pobierz Instalatora urządzenia replikacji

1. Na **serwerach**Azure Migrate Project > w programie ***Azure Migrate: Migracja**serwera, kliknij przycisk **odkryj**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. W  > obszarze odnajdywanie maszyn**są zwirtualizowane maszyny?** kliknij pozycję niezwirtualizowane **/inne**.
4. W **obszarze region docelowy**wybierz region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację maszyn.
5. Wybierz pozycję **Potwierdź, że docelowy region migracji to nazwa regionu**.
6. Kliknij pozycję **Utwórz zasoby**. Spowoduje to utworzenie magazynu Azure Site Recovery w tle.
    - Jeśli już skonfigurowano migrację z Azure Migrate migracji do serwera, nie można skonfigurować opcji docelowej, ponieważ zasoby zostały wcześniej skonfigurowane.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje znajdują się w tym regionie.

7. W **czy chcesz zainstalować nowe urządzenie do replikacji?** wybierz pozycję **Zainstaluj urządzenie replikacji**.
9. W obszarze **pobieranie i instalowanie oprogramowania do replikacji urządzenia**Pobierz Instalatora urządzenia i klucz rejestracji. Aby zarejestrować urządzenie, musisz być kluczem. Klucz jest ważny przez pięć dni od jego pobrania.

    ![Pobierz dostawcę](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Skopiuj plik Instalatora urządzenia i plik klucza do komputera z systemem Windows Server 2016, który został utworzony dla urządzenia.
11. Uruchom plik instalacyjny urządzenia replikacji, zgodnie z opisem w następnej procedurze.
12. Po ponownym uruchomieniu urządzenia po zakończeniu instalacji w obszarze **odnajdywanie maszyn**wybierz nowe urządzenie na liście **Wybierz serwer konfiguracji**, a następnie kliknij pozycję **finalizowanie rejestracji**. Finalizowanie rejestracji wykonuje kilka zadań końcowych w celu przygotowania urządzenia do replikacji.

    ![Finalizowanie rejestracji](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Zakończenie rejestracji może potrwać do 15 minut, dopóki odnalezione maszyny nie pojawią się w Azure Migrate migracji na serwer. Jako że maszyny wirtualne są odnajdywane, wzrasta liczba **odnalezionych serwerów** .

![Odnalezione serwery](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalowanie usługi mobilności

Na maszynach, które mają zostać zmigrowane, należy zainstalować agenta usługi mobilności. Instalatory agentów są dostępne na urządzeniu replikacji. Możesz znaleźć odpowiedni Instalator i zainstalować agenta na każdej maszynie, która ma zostać zmigrowana. Wykonaj następujące czynności:

1. Zaloguj się do urządzenia replikacji.
2. Przejdź do **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Znajdź Instalatora dla systemu operacyjnego i wersji maszyny. Przejrzyj [obsługiwane systemy operacyjne](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Skopiuj plik Instalatora na maszynę, którą chcesz zmigrować.
5. Upewnij się, że hasło zostało wygenerowane podczas wdrażania urządzenia.
    - Zapisz plik w tymczasowym pliku tekstowym na komputerze.
    - Hasło można uzyskać na urządzeniu replikacji. W wierszu polecenia Uruchom polecenie **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** , aby wyświetlić bieżące hasło.
    - Nie generuj ponownie hasła. Spowoduje to przerwanie łączności i konieczne będzie ponowne zarejestrowanie urządzenia replikacji.


### <a name="install-on-windows"></a>Instalowanie w systemie Windows

1. Wyodrębnij zawartość pliku Instalatora do folderu lokalnego (na przykład C:\Temp) na komputerze w następujący sposób:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Uruchom Instalatora usługi mobilności:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalowanie w systemie Linux

1. Wyodrębnij zawartość Instalatora plik tar do folderu lokalnego (na przykład/tmp/MobSvcInstaller) na komputerze w następujący sposób:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Uruchom skrypt Instalatora:
    ```
    sudo ./install -r MS -q
    ```
3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikowanie maszyn

1. Na **serwerach** **Azure Migrate Project > Azure Migrate: Migracja**serwera, kliknij przycisk **replikacja**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. W obszarze **replikacja**> **Ustawienia** > źródła**są zwirtualizowane na maszynach?** wybierz opcję **tak, z VMware vSphere**.
3. W **urządzeniu lokalnym**wybierz nazwę skonfigurowanego urządzenia Azure Migrate.
4. W programie **vCenter Server**Określ nazwę serwera vCenter do zarządzania maszynami wirtualnymi lub serwer vSphere, na którym są hostowane maszyny wirtualne.
5. W obszarze **serwer przetwarzania**wybierz nazwę urządzenia replikacji.
6. W obszarze **poświadczenia gościa**należy określić konto administratora maszyny wirtualnej, które będzie używane do instalacji wypychanej usługi mobilności. W tym samouczku zainstalowano usługę mobilności ręcznie, więc można dodać dowolne konto fikcyjne. Następnie kliknij **przycisk Dalej: Maszyny**wirtualne.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. W **Virtual Machines**w **ustawieniach migracji importu z oceny?** pozostaw ustawienie domyślne **nie, określ ustawienia migracji ręcznie**.
8. Sprawdź wszystkie maszyny wirtualne, które chcesz zmigrować. Następnie kliknij **przycisk Dalej: Ustawienia**docelowe.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. W obszarze **ustawienia docelowe**wybierz subskrypcję i docelowy region do migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
10. W **Virtual Network**wybierz sieć wirtualną platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W **korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **nie** , jeśli nie chcesz stosować korzyść użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Wybierz opcję **tak** , jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Next** (Dalej).

    ![Ustawienia docelowe](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. W obszarze **obliczenia**Sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: Domyślnie Migracja serwera Azure Migrate wybiera rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w **rozmiarze maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program inicjujący i Instalatora systemu operacyjnego. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeń](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. W obszarze **dyski**Określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowy dysk SSD lub dyski w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Dyski można wykluczać z replikacji.
    - Jeśli wykluczasz dyski, program nie będzie znajdował się na maszynie wirtualnej platformy Azure po migracji. 

    ![Ustawienia dysku](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. W obszarze **Przegląd i rozpoczęcie replikacji**Sprawdź ustawienia, a następnie kliknij pozycję **Replikuj** , aby rozpocząć replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzanie** > **maszynami replikowanymi**. Nie można zmienić ustawień po rozpoczęciu replikacji.



## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.


Stan zadania można śledzić w powiadomieniach portalu.

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja**serwera.
![Monitoruj replikację](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze**serwery** > celówmigracji > Azure Migrate:  **Migracja**serwera, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Testowanie migrowanych serwerów](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. W obszarze **migracja testowa**wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Rozpocznie się zadanie **migracji testowej** . Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji Sprawdź zmigrowane maszyny wirtualne platformy Azure w **Virtual Machines** w Azure Portal. Nazwa maszyny ma **test**sufiksu.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **replikowanie maszyn**, a następnie kliknij pozycję **Oczyść test migracja**.

    ![Czyszczenie migracji](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. Na **serwerach** > AzureMigrateProject>AzureMigrate: **Migracja**serwera, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. W obszarze **replikowanie maszyn**kliknij prawym przyciskiem myszy maszynę wirtualną > **migracji**.
3. W  > obszarze Migrowanie**Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych**wybierz pozycję **tak** > **OK**.
    - Domyślnie Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby synchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Zapewnia to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **nie**
4. Rozpocznie się zadanie migracji dla maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania można wyświetlić maszynę wirtualną i zarządzać nią na stronie **Virtual Machines** .

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
    - Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.
