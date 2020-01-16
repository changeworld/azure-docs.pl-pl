---
title: Ocenianie maszyn wirtualnych VMware na potrzeby migracji na platformę Azure za pomocą oceny serwera Azure Migrate
description: Opisuje sposób oceny lokalnych maszyn wirtualnych VMware na potrzeby migracji na platformę Azure przy użyciu Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 58e6e7b3cdf749909165d7ff071a2f3885d7e8b9
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028731"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Ocenianie maszyn wirtualnych VMware za pomocą oceny serwera Azure Migrate

W tym artykule opisano sposób oceny lokalnych maszyn wirtualnych VMware przy użyciu narzędzia do oceny serwera w Azure Migrate.

[Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) od partnerów firmy Microsoft.

Ten samouczek jest drugą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware na platformę Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj projekt Azure Migrate.
> * Skonfiguruj urządzenie Azure Migrate uruchamiane lokalnie w celu oceny maszyn wirtualnych.
> * Rozpocznij ciągłe wykrywanie lokalnych maszyn wirtualnych. Urządzenie wysyła dane dotyczące konfiguracji i wydajności dla odnalezionych maszyn wirtualnych na platformę Azure.
> * Grupuj odnalezione maszyny wirtualne i Oceń grupę maszyn wirtualnych.
> * Przejrzyj ocenę.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami z instrukcjami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

[Wykonaj pierwszy samouczek](tutorial-prepare-vmware.md) z tej serii. Jeśli tego nie zrobisz, instrukcje podane w tym samouczku nie będą działały.

Oto co należy zrobić w pierwszym samouczku:

- [Skonfiguruj uprawnienia platformy Azure](tutorial-prepare-vmware.md#prepare-azure) dla Azure Migrate.
- [Przygotuj oprogramowanie VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) do oceny:
   - [Sprawdź](migrate-support-matrix-vmware.md#vmware-requirements) Ustawienia programu VMware.
   - Skonfiguruj uprawnienia w oprogramowaniu VMware, aby utworzyć maszynę wirtualną VMware z szablonem komórki jajowe.
   - Skonfiguruj [konto do odnajdywania maszyn wirtualnych](migrate-support-matrix-vmware.md#vmware-requirements). 
   - Udostępnienie [wymaganych portów](migrate-support-matrix-vmware.md#port-access) .
   - Należy pamiętać o [adresach URL wymaganych](migrate-replication-appliance.md#url-access) do uzyskania dostępu do platformy Azure.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu Azure Migrate

Skonfiguruj nowy projekt Azure Migrate w następujący sposób:

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
1. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
1. W obszarze **Przegląd**w obszarze **odnajdywanie, ocenianie i Migrowanie serwerów**wybierz pozycję **Oceń i Przeprowadź migrację serwerów**.

   ![Przycisk umożliwiający ocenę i migrację serwerów](./media/tutorial-assess-vmware/assess-migrate.png)

1. W obszarze **wprowadzenie**wybierz pozycję **Dodaj narzędzia**.
1. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.     
1. W obszarze **szczegóły projektu**Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Obsługiwane są Azja, Europa, Wielka Brytania i Stany Zjednoczone.

   Lokalizacja geograficzna projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

   ![Pola nazwy i regionu projektu](./media/tutorial-assess-vmware/migrate-project.png)

1. Wybierz opcję **Dalej**.
1. W **narzędziu Wybierz ocenę**wybierz pozycję **Azure Migrate: Ocena serwera** > **dalej**.

   ![Wybór dla narzędzia do oceny serwera](./media/tutorial-assess-vmware/assessment-tool.png)

1. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
1. W oknie **Recenzja + Dodawanie narzędzi**przejrzyj ustawienia, a następnie wybierz pozycję **Dodaj narzędzia**.
1. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.

## <a name="set-up-the-appliance-vm"></a>Konfigurowanie maszyny wirtualnej urządzenia

Azure Migrate oceny serwera uruchamia lekkie urządzenie maszyny wirtualnej VMware. To urządzenie wykonuje odnajdywanie maszyn wirtualnych i zbiera dane o wydajności i metadanych maszyny wirtualnej.

Aby skonfigurować urządzenie, możesz:

- Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

Dla jednego projektu Azure Migrate można skonfigurować wiele urządzeń. Na wszystkich urządzeniach Ocena serwera obsługuje odnajdywanie maksymalnie 35 000 maszyn wirtualnych. Może odnajdywać maksymalnie 10 000 serwerów na urządzenie.

### <a name="download-the-ova-template"></a>Pobierz szablon komórki jajowe

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, wybierz pozycję **odkryj**.
1. W obszarze **odnajdywanie maszyn** > **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji hypervisor programu VMware vSphere**.
1. Wybierz pozycję **Pobierz** , aby pobrać plik szablonu komórki jajowe.

   ![Wybrane do pobrania plik komórki jajowe](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
1. Uruchom następujące polecenie, aby wygenerować skrót dla pliku komórki jajowe:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

W przypadku wersji 2.19.07.30 wygenerowany skrót powinien pasować do następujących wartości:

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną:

1. W konsoli klienta vSphere wybierz pozycję **plik** > **Wdróż szablon OVF**.

   ![Polecenie menu do wdrażania szablonu OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. W Kreatorze wdrażania szablonu OVF > **Źródło**Określ lokalizację pliku komórek jajowych.
1. W polu **Nazwa** i **Lokalizacja**Określ przyjazną nazwę maszyny wirtualnej. Wybierz obiekt spisu, w którym będzie hostowana maszyna wirtualna.
1. W obszarze **host/klaster**Określ hosta lub klaster, na którym będzie URUCHAMIANA maszyna wirtualna.
1. W obszarze **Magazyn**określ miejsce docelowe magazynu dla maszyny wirtualnej.
1. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
1. W polu **mapowanie sieci**Określ sieć, z którą zostanie nawiązane połączenie z maszyną wirtualną. Sieć wymaga łączności z Internetem w celu wysyłania metadanych do oceny serwera Azure Migrate.
1. Przejrzyj i Potwierdź ustawienia, a następnie wybierz pozycję **Zakończ**.

### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z [adresami URL platformy Azure](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie, wykonując następujące czynności:

1. W konsoli klienta vSphere kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz polecenie **Otwórz konsolę**.
1. Podaj język, strefę czasową i hasło dla urządzenia.
1. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, wybierając skrót do aplikacji.
1. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
   - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
   - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
     - Wybierz pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN.
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     - Należy zauważyć, że obsługiwany jest tylko serwer proxy HTTP.
   - **Synchronizacja czasu**: czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby odnajdywanie działało prawidłowo.
   - **Zainstaluj aktualizacje**: urządzenie zapewnia zainstalowanie najnowszych aktualizacji.
   - **Zainstaluj VDDK**: Urządzenie sprawdza, czy jest zainstalowany program VMware VSphere Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu.

     Migracja serwera Azure Migrate przy użyciu programu VDDK umożliwia replikowanie maszyn podczas migracji na platformę Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wybierz pozycję **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
1. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
1. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate, a następnie wybierz projekt.
1. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Wybierz pozycję **Zarejestruj**.

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

### <a name="specify-vcenter-server-details"></a>Określanie szczegółów programu vCenter Server
1. W obszarze **określ vCenter Server Szczegóły**Określ nazwę (FQDN) lub adres IP wystąpienia vCenter Server. Można pozostawić port domyślny lub określić port niestandardowy, dla którego vCenter Server nasłuchuje.
1. W polu **Nazwa użytkownika** i **hasło**określ poświadczenia konta vCenter Server, które będą używane przez urządzenie do odnajdywania maszyn wirtualnych w wystąpieniu vCenter Server. 

   Upewnij się, że konto ma [wymagane uprawnienia do odnajdywania](migrate-support-matrix-vmware.md#vmware-requirements). [Zakres odnajdywania](tutorial-assess-vmware.md#set-the-scope-of-discovery) można ograniczyć, ograniczając dostęp do konta vCenter.
1. Wybierz pozycję **Weryfikuj połączenie** , aby upewnić się, że urządzenie może połączyć się z vCenter Server.

### <a name="specify-vm-credentials"></a>Określ poświadczenia maszyny wirtualnej
W przypadku odnajdywania aplikacji, ról i funkcji oraz wizualizacji zależności maszyn wirtualnych można podać poświadczenia maszyny wirtualnej, które mają dostęp do maszyn wirtualnych VMware. Można dodać jedno poświadczenie dla maszyn wirtualnych z systemem Windows i jedno poświadczenie dla maszyn wirtualnych z systemem Linux. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) na temat niezbędnych uprawnień dostępu.

> [!NOTE]
> Dane wejściowe są opcjonalne, ale są potrzebne, jeśli chcesz włączyć funkcję odnajdywania aplikacji i wizualizacji zależności bez wykorzystania agentów.

1. W obszarze **Znajdź aplikacje i zależności na maszynach wirtualnych**wybierz pozycję **Dodaj poświadczenia**.
1. Wybierz opcję dla **systemu operacyjnego**.
1. Podaj przyjazną nazwę dla poświadczenia.
1. W polu **Nazwa użytkownika** i **hasło**Określ konto, które ma co najmniej dostęp gościa na maszynach wirtualnych.
1. Wybierz pozycję **Dodaj**.

Po określeniu wystąpienia vCenter Server i poświadczeniami maszyny wirtualnej (opcjonalnie) wybierz pozycję **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć odnajdywanie środowiska lokalnego.

Metadane wykrytych maszyn wirtualnych będą wyświetlane w portalu na około 15 minut. Odnajdywanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu. Czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku maszyn wirtualnych 500 potrzeba około 1 godziny, aby spis aplikacji był wyświetlany w portalu Azure Migrate.

### <a name="set-the-scope-of-discovery"></a>Ustaw zakres odnajdowania

Odnajdywanie może być ograniczone przez ograniczenie dostępu do konta vCenter używanego do odnajdywania. Można ustawić zakres vCenter Server centrów danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych.

Aby ustawić zakres, należy wykonać poniższe procedury.

#### <a name="1-create-a-vcenter-user-account"></a>1. Utwórz konto użytkownika vCenter
1.  Zaloguj się do klienta sieci Web vSphere jako administrator vCenter Server.
1.  Wybierz pozycję **administracja** > **Użytkownicy i grupy logowania jednokrotnego**, a następnie wybierz kartę **Użytkownicy** .
1.  Wybierz ikonę **nowego użytkownika** .
1.  Wprowadź wymagane informacje, aby utworzyć nowego użytkownika, a następnie wybierz przycisk **OK**.

#### <a name="2-define-a-new-role-with-required-permission"></a>2. Zdefiniuj nową rolę z wymaganym uprawnieniem
Ta procedura jest wymagana w przypadku migracji serwera bez agenta.
1.  Zaloguj się do klienta sieci Web vSphere jako administrator vCenter Server.
1.  Przejdź do widoku **administracja** > **Menedżer ról**.
1.  Wybierz wystąpienie vCenter Server z menu rozwijanego.
1.  Wybierz pozycję **Utwórz rolę**.
1.  Wprowadź nazwę nowej roli (na przykład <em>Azure_Migrate</em>).
1.  Przypisz [uprawnienia](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) do nowo zdefiniowanej roli.
1.  Kliknij przycisk **OK**.

#### <a name="3-assign-permissions-on-vcenter-objects"></a>3. Przypisz uprawnienia do obiektów vCenter

Istnieją dwie podejścia do przypisywania uprawnień do obiektów spisu w programie vCenter do konta użytkownika vCenter z przypisaną rolą.

W celu oceny serwera należy zastosować rolę **tylko do odczytu** dla konta użytkownika vCenter dla wszystkich obiektów nadrzędnych, w których są hostowane maszyny wirtualne, które mają zostać odnalezione. Wszystkie obiekty nadrzędne zostaną uwzględnione: host, folder hostów, klaster i folder klastrów w hierarchii do centrum danych. Te uprawnienia zostaną przekazane do obiektów podrzędnych w hierarchii.

Podobnie w przypadku migracji serwera należy zastosować rolę zdefiniowaną przez użytkownika z [uprawnieniami](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) konta użytkownika vCenter dla wszystkich obiektów nadrzędnych, w których są hostowane maszyny wirtualne do migracji. Ta rola może być nazywana <em>_Migrate platformy Azure</em>.

![Przypisywanie uprawnień](./media/tutorial-assess-vmware/assign-perms.png)

Alternatywnym podejściem jest przypisanie konta użytkownika i roli na poziomie centrum danych i propagowanie ich do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu (na przykład maszyny wirtualnej), którego nie chcesz odnajdywać/migrować. 

Alternatywna konfiguracja jest nieposkomplikowana. Udostępnia on przypadkowe kontrole dostępu, ponieważ każdy nowy obiekt podrzędny ma również automatycznie udzielony dostęp Dziedziczony z elementu nadrzędnego. Zalecamy korzystanie z pierwszej metody.

> [!NOTE]
> Obecnie Ocena serwera nie może odnaleźć maszyn wirtualnych, jeśli konto vCenter ma dostęp udzielony na poziomie folderu programu vCenter VM. Jeśli chcesz ograniczyć zakres odnajdywania według folderów maszyn wirtualnych, możesz to zrobić przy użyciu poniższej procedury. Gwarantuje to, że konto vCenter ma dostęp tylko do odczytu na poziomie maszyny wirtualnej.
>
> 1. Przypisz uprawnienia tylko do odczytu wszystkim maszynom wirtualnym w folderach maszyny wirtualnej, do których chcesz określić zakres odnajdywania.
> 1. Przyznaj dostęp tylko do odczytu do wszystkich obiektów nadrzędnych, w których są hostowane maszyny wirtualne. Zostaną uwzględnione wszystkie obiekty nadrzędne (host, folder hostów, klaster, folder klastrów) w hierarchii do centrum danych. Nie musisz propagować uprawnień do wszystkich obiektów podrzędnych.
> 1. Użyj poświadczeń do odnajdowania, wybierając pozycję Centrum **danych jako zakres kolekcji**. Skonfigurowana kontrola dostępu oparta na rolach zapewnia, że odpowiedni użytkownik vCenter będzie miał dostęp tylko do maszyn wirtualnych specyficznych dla dzierżawy.
>
> Należy zauważyć, że foldery hostów i klastrów są obsługiwane.

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po przeprowadzeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne są wyświetlane w Azure Portal:

1. Otwórz pulpit nawigacyjny Azure Migrate.
1. W **Azure Migrate serwery** > **Azure Migrate: Ocena serwera**, wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Za pomocą oceny serwera Azure Migrate można utworzyć dwa typy ocen:

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny oparte na zebranych danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)** : w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych | **Zalecany rozmiar maszyny wirtualnej**: na podstawie rozmiaru lokalnej maszyny wirtualnej.<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu, które wybrano do oceny.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen.
1. Na karcie **serwery** w kafelku **Azure Migrate: Ocena serwera** wybierz pozycję **Oceń**.

   ![Lokalizacja przycisku oceny](./media/tutorial-assess-vmware/assess.png)

1. W obszarze **ocenianie serwerów**Określ nazwę oceny.
1. Wybierz pozycję **Wyświetl wszystko**, a następnie przejrzyj właściwości oceny.

   ![Właściwości oceny](./media/tutorial-assess-vmware/view-all.png)

1. W obszarze **Wybierz lub Utwórz grupę**wybierz pozycję **Utwórz nową**, a następnie określ nazwę grupy. Grupa zbiera co najmniej jedną maszynę wirtualną w celu oceny.
1. W obszarze **Dodawanie maszyn do grupy**Wybierz Maszyny wirtualne, które mają zostać dodane do grupy.
1. Wybierz pozycję **Utwórz ocenę** , aby utworzyć grupę i uruchomić ocenę.

   ![Ocenianie serwerów](./media/tutorial-assess-vmware/assessment-create.png)

1. Po utworzeniu oceny Wyświetl ją w obszarze **serwery** > **Azure Migrate: Ocena serwera** > **ocen**.
1. Wybierz pozycję **Ocena eksportu** , aby pobrać ją jako plik programu Excel.

## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy maszyny wirtualne są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeniowe i magazynowe związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Oszacowanie kosztu miesięcznego magazynu**: szacowane koszty magazynu dyskowego po migracji.

Aby wyświetlić ocenę:

1. W obszarze **cele migracji** > **serwery**wybierz pozycję **oceny** w **Azure Migrate: Ocena serwera**.
1. W obszarze **oceny**Wybierz ocenę, aby ją otworzyć.

   ![Podsumowanie oceny](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości platformy Azure

1. W obszarze **gotowość platformy Azure**Sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
1. Sprawdź stan maszyny wirtualnej:
    - **Gotowe do użycia na platformie Azure**: używane, gdy Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości z powodu problemów z dostępnością danych.

1. Wybierz stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły gotowości maszyn wirtualnych. Możesz również przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Podsumowanie oceny przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie. Możesz przejść do szczegółów, aby wyświetlić szczegóły kosztów dla określonych maszyn wirtualnych.

> [!NOTE]
> Oszacowania kosztów są oparte na zaleceniach dotyczących rozmiaru maszyny, jej dysków i właściwości. Oszacowania dotyczą uruchamiania lokalnych maszyn wirtualnych jako maszyn wirtualnych IaaS. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.

Zagregowane koszty magazynu dla ocenianej grupy są podzielone na różne typy dysków magazynu. 

### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Azure Migrate oceny serwera przypisuje ocenę zaufania do oceny opartej na wydajności, od 1 gwiazdki (najniższego) do 5 gwiazdek (najwyższa).

![Ocena zaufania](./media/tutorial-assess-vmware/confidence-rating.png)

Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru oceny. Klasyfikacja jest oparta na dostępności punktów danych niezbędnych do obliczenia oceny:

**Dostępność punktu danych** | **Ocenę zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Poznaj najlepsze rozwiązania](best-practices-assessment.md#best-practices-for-confidence-ratings) dotyczące klasyfikacji zaufania.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujesz urządzenie Azure Migrate. Można również utworzyć i przejrzeć ocenę.

Aby dowiedzieć się, jak przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure przy użyciu migracji serwera Azure Migrate, przejdź do trzeciego samouczka w serii:

> [!div class="nextstepaction"]
> [Migrowanie maszyn wirtualnych VMware](./tutorial-migrate-vmware.md)
