---
title: Migrowanie maszyn wirtualnych programu VMware do platformy Azure z opartej o agentów usługi Azure migracji migracji serwera lokalnego | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przeprowadzić migrację maszyn lokalnych na platformę Azure za pomocą usługi Azure Migration migracji serwera opartej o agentów
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d222936f93f90573a46cd7f6216fbde8043332c7
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261415"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrowanie maszyn wirtualnych VMware do platformy Azure (w oparciu o agenta)

W tym artykule pokazano, jak przeprowadzić migrację lokalnych maszyn wirtualnych z programu VMware na platformę Azure za pomocą oparte na agentach migracji za pomocą narzędzia migracji serwera migracji platformy Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i wystąpień maszyn wirtualnych usług AWS/GCP, na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie środowiska źródłowego, a następnie wdrożyć urządzenie replikacji usługi Azure Migrate oparte na agentach migracji.
> * Konfigurowanie środowiska docelowego dla migracji.
> * Skonfiguruj zasady replikacji.
> * Włącz replikację.
> * Uruchamianie migracji testowej, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Wykonywanie pełnej migracji na platformę Azure.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania dla scenariusza, aby szybko skonfigurować się weryfikacji koncepcji. Samouczki użyj opcji domyślnych, jeśli jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Aby uzyskać szczegółowe instrukcje Przejrzyj How-tos VMware oceny i migracji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zalecamy wypróbować oceny maszyn wirtualnych VMware za pomocą migracji serwera oceny Azure, przed przeprowadzeniem migracji maszyn wirtualnych na platformie Azure. Skonfiguruj sposób oceny w następujący sposób:

1. Postępuj zgodnie z samouczkiem, aby [przygotowanie platformy Azure i programu VMware](tutorial-prepare-vmware.md) dla oceny.
2. Następnie wykonaj [w tym samouczku](tutorial-assess-vmware.md) skonfigurować urządzenie Azure Migrate do oceny i odnajdywać i oceniać maszyny wirtualne.


Mimo że firma Microsoft zaleca, wypróbuj ocenę, nie masz można uruchomić oceny, przed przeprowadzeniem migracji maszyn wirtualnych.

## <a name="migration-methods"></a>Metody migracji

Można przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure za pomocą narzędzia do migracji serwera migracji platformy Azure. To narzędzie oferuje kilka opcji do migracji maszyn wirtualnych programu VMware:

- Bez wykorzystania agentów replikacji. Migrowanie maszyn wirtualnych bez konieczności instalowania żadnych narzędzi na nich.
- Migracja oparte na agentach. lub replikacji. Agent (agent usługi mobilności) należy zainstalować na maszynie Wirtualnej w celu replikacji.

Aby zdecydować, czy chcesz użyć migracji bez wykorzystania agentów lub oparte na agentach, sprawdź następujące artykuły:

- [Dowiedz się więcej o](server-migrate-overview.md) opcje migracji programu VMware.
- [Sprawdzanie ograniczeń](server-migrate-overview.md#agentless-migration-limitations) migracji bez wykorzystania agentów.
- [Postępuj zgodnie z tym artykule](tutorial-migrate-vmware.md) możesz wypróbować bez wykorzystania agentów migracji.



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Przegląd](migrate-architecture.md) architekturę migracji programu VMware.
2. Upewnij się, że Twoje konto platformy Azure jest przypisana rola Współautor maszyny wirtualnej tak, że masz uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapis na platformie Azure dysku zarządzanego. 

3. [Skonfiguruj sieć platformy Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokalne maszyny są replikowane do usługi Azure managed disks. Podczas przejścia w tryb failover na platformę Azure do migracji, maszyn wirtualnych platformy Azure są tworzone na podstawie tych dysków zarządzanych i połączone z siecią platformy Azure, należy określić podczas konfigurowania migracji.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Jeśli ocena został już uruchomiony wraz z oceną serwera migracji platformy Azure, możesz pominąć instrukcje w tej sekcji, ponieważ zostały już wykonane następujące czynności. 

Jeśli nie uruchomisz ocenę, należy skonfigurować uprawnienia platformy Azure można migrować, za pomocą usługi Azure Migration migracji serwera.

- **Utwórz projekt**: Twoje konto platformy Azure wymaga uprawnień do tworzenia projektu usługi Azure Migrate. 
- **Zarejestruj urządzenie replikacji usługi Azure Migrate**: Urządzenie replikacji tworzącego i rejestrującego aplikację usługi Azure Active Directory w ramach Twojego konta platformy Azure. Musisz delegować uprawnienia dla tego.
- **Tworzenie magazynu kluczy**: Aby przeprowadzić migrację maszyn wirtualnych VMware przy użyciu migracji serwera migracji platformy Azure, usługa Azure Migrate tworzy usługę Key Vault w grupie zasobów, aby zarządzać kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, potrzebujesz uprawnienia do przypisywania ról w grupie zasobów, w której znajduje się projekt usługi Azure Migrate. 


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

    ![Uprawnienia platformy Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> To ustawienie domyślne, które nie są poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisanie roli aplikacji dla deweloperów 

Administrator dzierżawy/globalny przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Przypisywanie uprawnień do tworzenia usługi Key Vault

Przypisz uprawnienia do przypisywania ról w grupie zasobów, w której usługa Azure Migrate znajduje się projekt, w następujący sposób:

1. W tej grupie zasobów w witrynie Azure portal wybierz **kontrola dostępu (IAM)** .
2. W **Sprawdź dostęp**, Znajdź odpowiednie konto i kliknij go, aby wyświetlić uprawnienia. Potrzebujesz **właściciela** (lub **Współautor** i **Administrator dostępu użytkowników**) uprawnień.
3. Jeśli nie masz wymaganych uprawnień, należy zażądać ich z właścicielem grupy zasobów. 


## <a name="prepare-on-premises-vmware"></a>Przygotowywanie lokalnego wdrożenia oprogramowania VMware

### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Migrowanie serwera migracji na platformę Azure, musi mieć dostęp do serwerów VMware w celu:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebne jest konto, na którym można uruchamiać operacje, takie jak tworzenie i usuwanie dysków, a także włączanie maszyn wirtualnych.

Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Site_Recovery**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

#### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Pełna replikacja, tryb failover i powrót po awarii** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługę Mobility należy zainstalować na każdej maszynie, która ma być replikowana.

- Urządzenie replikacji usługi Azure Migrate można wykonać instalacji wypychanej usługi, gdy należy włączyć replikację dla maszyny lub można ją zainstalować ręcznie lub za pomocą narzędzia do instalacji.
- W tym samouczku zamierzamy zainstalować usługę Mobility przy użyciu instalacji typu push.
- Wypychana instalacja wymaga przygotowania konta, które migracji serwera migracji platformy Azure umożliwia dostęp do maszyny Wirtualnej.

Przygotuj konto w następujący sposób:

1. Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.
2. W przypadku maszyn wirtualnych Windows, jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na maszynie lokalnej, dodając wpis DWORD **LocalAccountTokenFilterPolicy**, o wartości w rejestrze, w obszarze **HKEY_ LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. W przypadku maszyn wirtualnych systemu Linux Przygotuj konto superużytkownika na serwerze źródłowym w systemie Linux.


### <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne spełniają wymagania dotyczące migracji na platformę Azure. 


> [!NOTE]
> Oparte na agentach migracji za pomocą usługi Azure Migration migracji serwera jest oparta na funkcjach usługi Azure Site Recovery. Dokumentacja usługi Site Recovery połączyć pewnych wymagań.

1. [Zweryfikuj](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) wymagania dotyczące serwerów VMware.
2. [Sprawdź](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) maszyn wirtualnych obsługują wymagania dotyczące migracji.
3. Sprawdź ustawienia maszyny Wirtualnej. Lokalne maszyny wirtualne można replikować do platformy Azure muszą być zgodne z [wymagania maszyny Wirtualnej platformy Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodaj narzędzie do migracji serwera migracji platformy Azure

Jeśli nie wykonasz samouczka, aby ocenić maszyny wirtualne VMware, skonfiguruj projekt usługi Azure Migrate, a następnie dodaj narzędzie do migracji serwera migracji platformy Azure:

1. W witrynie Azure portal > **wszystkich usług**, wyszukaj **usługi Azure Migrate**.
2. W obszarze **usług**, wybierz opcję **usługi Azure Migrate**.

    ![Konfigurowanie usługi Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. W **Przegląd**, kliknij przycisk **oceny i migracji serwerów**.
4. W obszarze **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **oceny i migracji serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. W **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **Dodawanie narzędzi**.
2. W **projektu migracji**, a następnie wybierz swoją subskrypcję platformy Azure i Utwórz grupę zasobów, jeśli nie masz.
3. W **Project Details**, określ nazwę projektu i lokalizacji geograficznej, w której chcesz utworzyć projekt, a następnie kliknij przycisk **dalej**

    ![Utwórz projekt usługi Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Projekt usługi Azure Migrate można utworzyć w dowolnym z tych obszarów geograficznych.

    **Lokalizacja geograficzna** | **Region**
    --- | ---
    Azja | Azja Południowo-Wschodnia
    Europa | Europa Północna lub Europa Zachodnia
    Stany Zjednoczone | Wschodnie stany USA lub Zachodnio-środkowe stany USA

    Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Można wybrać dowolny region docelowy, aby uzyskać rzeczywistą migrację.
4. W **narzędzia do oceny wybierz**, wybierz opcję **pominąć Dodawanie narzędzie do oceny teraz** > **dalej**.
5. W **narzędzie do migracji wybierz**, wybierz opcję **usługi Azure Migrate: Migracja serwera** > **dalej**.
6. W **Przejrzyj + Dodaj narzędzia**, przejrzyj ustawienia i kliknij przycisk **Dodawanie narzędzi**
7. Po dodaniu narzędzie, pojawia się w projekcie usługi Azure Migrate > **serwerów** > **narzędzia migracji**.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia replikacji

Pierwszym krokiem migracji jest skonfigurować urządzenie replikacji. Urządzenie replikacji to pojedyncza o wysokiej dostępności, lokalnej maszyny Wirtualnej VMware, który hostuje składniki:

- **Serwer konfiguracji**: Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
- **Serwer przetwarzania**: Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. Serwer przetwarzania instaluje także agenta usługi mobilności na maszynach wirtualnych, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie lokalnych maszyn wirtualnych programu VMware.


Aby skonfigurować urządzenie replikacji, możesz pobrać przygotowany szablon Open Virtualization aplikacji (OVA). Zaimportuj szablon do programu VMware, a następnie utwórz urządzenie replikacji maszyny Wirtualnej. 

### <a name="download-the-replication-appliance-template"></a>Pobierz szablon urządzenia replikacji

Pobierz szablon w następujący sposób:

1. W projekcie usługi Azure Migrate kliknij **serwerów** w obszarze **celów migracji**.
2. W **usługi Azure Migrate — serwery** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **odnajdź**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. W **odnajdź maszyny** > **są maszynach zwirtualizowane?** , kliknij przycisk **tak, przy użyciu funkcji VMWare vSphere hypervisor**.
4. W **jak chcesz migrować?** , wybierz opcję **przy użyciu replikacji opartej o agentów**.
5. W **region docelowy**, wybierz region platformy Azure, do którego chcesz przeprowadzić migrację maszyn.
6. Wybierz **upewnij się, że region docelowy dla migracji jest nazwa regionu**.
7. Kliknij przycisk **tworzyć zasoby**. Spowoduje to utworzenie magazynu usługi Azure Site Recovery w tle.
    - Region docelowy dla tego projektu nie można zmienić po kliknięciu tego przycisku.
    - Wszystkie kolejne migracje są do tego regionu.

    ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. W **czy chcesz zainstalować nowe urządzenie replikacji?** , wybierz opcję **instalowanie urządzenia replikacji**.
9. Kliknij przycisk **Pobierz**, aby pobrać urządzenia replikacji. Spowoduje to pobranie szablonu pakietu OVF, używanej do tworzenia nowej maszyny Wirtualnej VMware, uruchamianego przez urządzenie.
    ![Pobierz OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Zanotuj nazwę grupy zasobów i magazyn usługi Recovery Services. Jest to konieczne podczas wdrażania urządzenia.


### <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

Po pobraniu szablonu pakietu OVF, możesz go zaimportować do programu VMware do tworzenia aplikacji replikacji na maszynie Wirtualnej VMware z systemem Windows Server 2016.

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić **Kreatora wdrażania szablonu OVF**. 
3. W **wybierz źródło**, wprowadź lokalizację pobranego szablonu OVF.
4. W **Przejrzyj szczegóły**, wybierz opcję **dalej**.
5. W **wybierz nazwę i folder** i **wybierz konfigurację**, zaakceptuj ustawienia domyślne.
6. W **wybierz magazyn** > **wybierz format dysku wirtualnego**, aby uzyskać najlepszą wydajność, wybierz **Thick Provision Eager Zeroed**.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. W **gotowa do zakończenia**, aby skonfigurować maszynę Wirtualną z ustawieniami domyślnymi, wybierz opcję **Włącz po wdrożeniu** > **Zakończ**.

   > [!TIP]
   > Jeśli chcesz dodać więcej kart sieciowych, wyczyść pole **Power on after deployment** (Włącz po wdrożeniu) > **Finish** (Zakończ). Domyślnie szablon zawiera jedną kartę sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

### <a name="kick-off-replication-appliance-setup"></a>Uruchomienie Instalatora urządzenia replikacji

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji Zaloguj się do maszyny Wirtualnej jako administrator, przy użyciu hasła administratora.
4. Podczas pierwszego logowania w narzędziu konfiguracji urządzenia replikacji (narzędzie usługi Azure Site Recovery konfiguracji) rozpoczyna się w ciągu kilku sekund.
5. Wprowadź nazwę do użycia do zarejestrowania urządzenia z usługi Azure Migration migracji serwera. Następnie kliknij przycisk **Next** (Dalej).
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.
7. Poczekaj, aż narzędzie zakończyć rejestrowanie aplikacji usługi Azure AD, aby zidentyfikować urządzenia. Ponowne uruchomienie urządzenia.
1. Ponownie zaloguj się do maszyny. W ciągu kilku sekund zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

### <a name="register-the-replication-appliance"></a>Zarejestruj urządzenie replikacji

Zakończ konfigurowanie i rejestrowanie urządzenia w replikacji.

1. W Kreatorze konfiguracji serwera zarządzania zaznacz **Konfiguracja łączności**.
2. Wybierz kartę Sieciową (domyślnie jest tylko jedna karta sieciowa), urządzenia replikacji używa odnajdowania maszyn wirtualnych i wykonywania instalacji wypychanej usługi mobilności na komputerach źródłowych.
3. Wybierz kartę Sieciową, która urządzenia replikacji używa do połączenia z platformą Azure. Następnie wybierz pozycję **Zapisz**. Nie można zmienić to ustawienie, po skonfigurowaniu go.
4. Jeśli urządzenie znajduje się za serwerem proxy, należy określić ustawienia serwera proxy.
    - Określ nazwę serwera proxy jako **http://ip-address** , lub **http://FQDN** . Serwery proxy HTTPS nie są obsługiwane.
5. Po wyświetleniu monitu dla subskrypcji, grupy zasobów i szczegóły magazynu, należy dodać szczegóły podane podczas pobrano szablon urządzenia.
6. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server.
7. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
8. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
9. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
10. Wprowadź poświadczenia dla konta możesz [utworzone](#prepare-an-account-for-automatic-discovery) odnajdowania programu VMware. Wybierz **Dodaj** > **nadal**.
11. W **skonfiguruj poświadczenia maszyny wirtualnej**, wprowadź poświadczenia, możesz [utworzone](#prepare-an-account-for-mobility-service-installation) dotyczące instalacji wypychanej usługi mobilności, po włączeniu replikacji dla maszyn wirtualnych.  
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
12. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.


Po zarejestrowaniu urządzenia replikacji Azure migracji serwera oceny nawiązanie połączenia z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne. Możesz wyświetlić odnalezione maszyny wirtualne w **Zarządzaj** > **odnaleziono elementy**w **innych** kartę.


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

1. W projekcie usługi Azure Migrate > **serwerów**, **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikować**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. W **replikować**, > **ustawienia źródła** > **są maszynach zwirtualizowane?** , wybierz opcję **tak, przy użyciu oprogramowania VMware vSphere**.
3. W **lokalne urządzenie**, wybierz nazwę usługi Azure Migrate urządzenia, który został ustawiony.
4. W **serwera vCenter**, określ nazwę serwera vCenter, zarządzanie maszynami wirtualnymi lub serwera vSphere, na którym znajdują się maszyny wirtualne.
5. W **serwera przetwarzania**, wybierz nazwę urządzenia replikacji.
6. W **poświadczenia gościa**, określ konto administratora maszyny Wirtualnej, która będzie służyć do instalacji wypychanej usługi mobilności. Następnie kliknij przycisk **dalej: Maszyny wirtualne**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. W **maszyn wirtualnych**, wybierz maszyny, które mają być replikowane.

    - Jeśli uruchamiasz ocenę dla maszyn wirtualnych, można zastosować zmiany rozmiaru maszyn wirtualnych i zaleceń (w warstwie premium/standardowa) typ dysku wyniki oceny. Aby to zrobić, w **zaimportować ustawienia migracji z oceny usługi Azure Migrate?** , wybierz opcję **tak** opcji.
    - Jeśli nie została wykonana oceny lub nie chcesz używać ustawień oceny, wybierz **nie** opcje.
    - Jeśli została wybrana do oceny, wybierz grupy maszyn wirtualnych, a nazwa oceny.

8. Sprawdź poszczególne maszyny Wirtualne, które mają zostać zmigrowane. Następnie kliknij przycisk **dalej: Docelowe ustawienia**.
9. W **docelowe ustawienia**, wybierz subskrypcję i docelowy region, do którego można będzie migracji i określ grupę zasobów, w której maszyny wirtualne Azure będą znajdować się po zakończeniu migracji.
10. W **sieci wirtualnej**, wybierz pozycję Azure sieci wirtualnej/podsieci do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W **korzyść użycia hybrydowego platformy Azure**:

    - Wybierz **nie** Jeśli nie chcesz zastosować korzyść użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Wybierz **tak** Jeśli masz maszyny z systemem Windows Server, które są objęte mających aktywne subskrypcje i pakietem Software Assurance lub Windows Server i chcesz zastosować korzyść dla maszyn, którego przenosisz. Następnie kliknij przycisk **Next** (Dalej).

12. W **obliczenia**, sprawdź nazwę maszyny Wirtualnej, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności. Maszyny wirtualne muszą być zgodne z [wymagania dotyczące usługi Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Rozmiar maszyny Wirtualnej**: Jeśli korzystasz zaleceń dotyczących oceny, lista rozwijana rozmiar maszyny Wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybiera najlepsze dopasowanie w subskrypcji platformy Azure w oparciu o rozmiarze. Alternatywnie, wybierz ręczne rozmiar w **rozmiar maszyny Wirtualnej Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) dla maszyny Wirtualnej. Dysk systemu operacyjnego jest dysk systemu operacyjnego, programu rozruchowego oraz Instalatora. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w dostępności platformy Azure po migracji, należy określić zestaw. Zestaw musi być w docelowej grupie zasobów, które określisz dla migracji.

13. W **dysków**, określ, czy dyski maszyny Wirtualnej powinny być replikowane na platformę Azure i wybierz typ dysku (dysk twardy na dysk SSD/lub premium zarządzane dyski w warstwie standardowa) na platformie Azure. Następnie kliknij przycisk **Next** (Dalej).
    - Dyski można wykluczyć z replikacji.
    - Jeśli Wyklucz dyski będą obecne na maszynie Wirtualnej platformy Azure po migracji. 

14. W **Przejrzyj i Rozpocznij replikację**, przejrzyj ustawienia i kliknij przycisk **replikować** rozpoczyna się Replikacja początkowa dla serwerów.

> [!NOTE]
> Możesz zaktualizować ustawienia replikacji w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzaj** > **replikowania maszyn**. Nie można zmienić ustawienia, po uruchomieniu replikacji.




## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po kliknięciu **replikować** rozpoczyna się zadanie Rozpocznij replikację. 
- Zadanie replikacji Start zakończy się pomyślnie, używany przez maszyny rozpocząć ich replikacji początkowej na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany dysków w środowisku lokalnym okresowo są replikowane do repliki dysków na platformie Azure.


Można śledzić stan zadania w powiadomieniach portalu.

![Śledź zadania](./media/tutorial-migrate-vmware-agent/jobs.png)

Możesz monitorować stan replikacji, klikając **replikowania serwerów** w **usługi Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Kiedy rozpoczyna się replikacja różnicowa, jest testowa migracja maszyn wirtualnych, można uruchomić przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, że w tym co najmniej raz dla każdej maszyny, przed przeprowadzeniem migracji.

- Uruchamianie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wywierania wpływu na komputerach lokalnych, które działać i nadal replikowane. 
- Migracja testowa symuluje migracji, tworząc Maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migracji z siecią wirtualną spoza środowiska produkcyjnego, w ramach subskrypcji platformy Azure).
- Test zreplikowanej maszyny Wirtualnej platformy Azure umożliwiają sprawdzanie poprawności migracji, wykonaj testowanie aplikacji i rozwiązać wszelkie problemy przed pełnej migracji.

Przeprowadzić migrację testu w następujący sposób:


1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **testu migracji serwerów**.

     ![Testowanie serwerów zmigrowane](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę Wirtualną, aby przetestować, a następnie kliknij przycisk **Test Migrowanie**.

    ![Migracja testowa](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. W **migracji testowej**, wybierz sieć wirtualną platformy Azure, w którym maszyna wirtualna platformy Azure zostaną umieszczone po zakończeniu migracji. Zaleca się użyć sieci wirtualnej spoza środowiska produkcyjnego.
4. **Migracja testowa** rozpoczyna się zadanie. Monitorowanie zadania w powiadomieniach portalu.
5. Po zakończeniu migracji, należy wyświetlić zmigrowanej maszyny Wirtualnej platformy Azure w **maszyn wirtualnych** w witrynie Azure portal. Nazwa komputera ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszyny Wirtualnej platformy Azure w **replikowania maszyn**i kliknij przycisk **wyczyścić migracji testowej**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, należy przeprowadzić migrację maszyn lokalnych.

1. W projekcie usługi Azure Migrate > **serwerów** > **usługi Azure Migrate: Migracja serwera**, kliknij przycisk **replikowania serwerów**.

    ![Replikacja serwerów](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

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
