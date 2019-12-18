---
title: Ocenianie maszyn wirtualnych VMware na potrzeby migracji na platformę Azure za pomocą Azure Migrate
description: Opisuje sposób oceny lokalnych maszyn wirtualnych VMware na potrzeby migracji na platformę Azure przy użyciu Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 595b6705b4e876ce5b42a7de831136cb0b62b1f5
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196442"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Ocenianie maszyn wirtualnych VMware za pomocą Azure Migrate: Ocena serwera

W tym artykule przedstawiono sposób oceny lokalnych maszyn wirtualnych VMware przy użyciu narzędzia do oceny serwera Azure Migrate:.

[Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm.



Ten samouczek jest drugą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
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

- [Wykonaj](tutorial-prepare-vmware.md) pierwszy samouczek z tej serii. Jeśli tego nie zrobisz, instrukcje podane w tym samouczku nie będą działały.
- Oto co należy zrobić w pierwszym samouczku:
    - [Skonfiguruj uprawnienia platformy Azure](tutorial-prepare-vmware.md#prepare-azure) dla Azure Migrate.
    - [Przygotuj oprogramowanie VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) do oceny. Należy zweryfikować ustawienia programu VMware i należy mieć uprawnienia do tworzenia maszyny wirtualnej VMware z szablonem komórki jajowe. Należy również skonfigurować konto na potrzeby odnajdywania maszyn wirtualnych. Wymagane porty powinny być dostępne i należy mieć świadomość adresów URL potrzebnych do uzyskania dostępu do platformy Azure.


## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu Azure Migrate

Skonfiguruj nowy projekt usługi Azure Migrate w następujący sposób.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Omówienie**, **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Oceń i zmigruj serwery**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-vmware/assess-migrate.png)

4. W obszarze **Wprowadzenie** kliknij pozycję **Dodaj narzędzia**.
5. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.     
6. W obszarze **szczegóły projektu**Określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. Azja, Europa, Zjednoczone Królestwo i Stany Zjednoczone są obsługiwane.

    - Lokalizacja geograficzna projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-vmware/migrate-project.png)


7. Kliknij przycisk **Dalej**.
8. W **narzędziu Wybierz ocenę**wybierz pozycję **Azure Migrate: Ocena serwera** > **dalej**.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-vmware/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Konfigurowanie maszyny wirtualnej urządzenia

Azure Migrate: Ocena serwera uruchamia lekkie urządzenie maszyny wirtualnej VMware.

- To urządzenie służy do odnajdywania maszyn wirtualnych i wysyłania metadanych maszyn wirtualnych i danych wydajności do oceny serwera Azure Migrate.
- Aby skonfigurować urządzenie:
    - Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server.
    - Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
    - Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.
- Dla jednego projektu Azure Migrate można skonfigurować wiele urządzeń. Na wszystkich urządzeniach obsługiwana jest funkcja odnajdywania maksymalnie 35 000 maszyn wirtualnych. Na urządzenie można odnaleźć maksymalnie 10 000 serwerów.

### <a name="download-the-ova-template"></a>Pobierz szablon komórki jajowe

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **Odnajdź maszyny** > **Czy maszyny są zwirtualizowane?** kliknij pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. Kliknij pozycję **Pobierz**, aby pobrać plik szablonu OVA.

    ![Pobieranie pliku OVA](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. W przypadku wersji 2.19.07.30 wygenerowana wartość skrótu powinna być zgodna z tymi wartościami.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


### <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję **File** (Plik)  >  **Deploy OVF Template** (Wdróż szablon OVF).

    ![Wdrażanie pliku OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. W Kreatorze wdrażania szablonu OVF > **Źródło**Określ lokalizację pliku komórek jajowych.
3. W polu **Nazwa** i **Lokalizacja**Określ przyjazną nazwę maszyny wirtualnej. Wybierz obiekt spisu, w którym będzie hostowana maszyna wirtualna.
5. W obszarze **host/klaster**Określ hosta lub klaster, na którym będzie URUCHAMIANA maszyna wirtualna.
6. W obszarze **Magazyn**określ miejsce docelowe magazynu dla maszyny wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W polu **mapowanie sieci**Określ sieć, z którą zostanie nawiązane połączenie z maszyną wirtualną. Sieć wymaga łączności z Internetem, aby można było wysyłać metadane do oceny serwera Azure Migrate.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z [adresami URL platformy Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie, wykonując następujące kroki.

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
4. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby odnajdywanie działało prawidłowo.
    - **Zainstaluj aktualizacje**: urządzenie zapewnia zainstalowanie najnowszych aktualizacji.
    - **Zainstaluj VDDK**: Urządzenie sprawdza, czy jest zainstalowany program VMware VSphere Virtual Disk Development Kit (VDDK).
        - Azure Migrate: Migracja serwera używa VDDK do replikowania maszyn podczas migracji na platformę Azure.
        - Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
2. Na nowej karcie Zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
2. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate, a następnie wybierz projekt.
3. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
4. Kliknij pozycję **zarejestruj**.

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

### <a name="specify-vcenter-server-details"></a>Określanie szczegółów programu vCenter Server
1. W obszarze **określ vCenter Server Szczegóły**Określ nazwę (FQDN) lub adres IP vCenter Server. Możesz opuścić port domyślny lub określić niestandardowy port, na którym nasłuchuje vCenter Server.
2. W polu **Nazwa użytkownika** i **hasło**określ poświadczenia konta vCenter Server, które będą używane przez urządzenie do odnajdywania maszyn wirtualnych na serwerze vCenter. Upewnij się, że konto ma [wymagane uprawnienia do odnajdywania](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Zakres odnajdywania można ograniczyć, ograniczając dostęp do konta vCenter odpowiednio; Więcej informacji o określaniu zakresu odnajdywania [znajdziesz tutaj](tutorial-assess-vmware.md#scoping-discovery).
3. Kliknij pozycję **Sprawdź poprawność połączenia** , aby upewnić się, że urządzenie może połączyć się z vCenter Server.

### <a name="specify-vm-credentials"></a>Określ poświadczenia maszyny wirtualnej
W przypadku odnajdywania aplikacji, ról i funkcji oraz wizualizacji zależności maszyn wirtualnych można podać poświadczenia maszyny wirtualnej, które mają dostęp do maszyn wirtualnych VMware. Można dodać jedno poświadczenie dla maszyn wirtualnych z systemem Windows i jedno poświadczenie dla maszyn wirtualnych z systemem Linux. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) o wymaganych uprawnieniach dostępu.

> [!NOTE]
> Dane wejściowe są opcjonalne i są konieczne do włączenia funkcji odnajdywania aplikacji i wizualizacji zależności bez wykorzystania agentów.

1. W obszarze **Znajdź aplikacje i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia**.
2. Wybierz **system operacyjny**.
3. Podaj przyjazną nazwę dla poświadczenia.
4. W polu **Nazwa użytkownika** i **hasło**Określ konto, które ma co najmniej dostęp gościa na maszynach wirtualnych.
5. Kliknij pozycję **Add** (Dodaj).

Po określeniu vCenter Server i poświadczeń maszyny wirtualnej (opcjonalnie) kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć odnajdywanie środowiska lokalnego.

Metadane wykrytych maszyn wirtualnych będą wyświetlane w portalu na około 15 minut. Odnajdywanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu, a czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku maszyn wirtualnych 500 potrzeba około 1 godziny, aby spis aplikacji był wyświetlany w portalu Azure Migrate.

### <a name="scoping-discovery"></a>Odnajdowanie zakresu

Odnajdywanie może być ograniczone przez ograniczenie dostępu do konta vCenter używanego do odnajdywania. Można ustawić zakres do vCenter Server centrów danych, klastrów, folderze klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych.

Aby ustawić zakres, należy wykonać następujące czynności:
1.  Utwórz konto użytkownika vCenter.
2.  Zdefiniuj nową rolę z wymaganymi uprawnieniami. (<em>wymagane w przypadku migracji serwera bez agentów</em>)
3.  Przypisywanie uprawnień do konta użytkownika w obiektach vCenter.

**Tworzenie konta użytkownika vCenter**
1.  Zaloguj się do klienta sieci Web vSphere jako administrator vCenter Server.
2.  Kliknij kolejno pozycje **administracja** > **Użytkownicy i grupy logowania jednokrotnego** > karta **Użytkownicy** .
3.  Kliknij ikonę **nowego użytkownika** .
4.  Wprowadź wymagane informacje, aby utworzyć nowego użytkownika, a następnie kliknij przycisk **OK**.

**Zdefiniuj nową rolę z wymaganymi uprawnieniami** (<em>wymaganą do migracji serwera bez agentów</em>)
1.  Zaloguj się do klienta sieci Web vSphere jako administrator vCenter Server.
2.  Przejdź do widoku **administracja** > **Menedżer ról**.
3.  Wybierz vCenter Server z menu rozwijanego.
4.  Kliknij pozycję **Utwórz akcję roli** .
5.  Wpisz nazwę nowej roli. (na przykład <em>Azure_Migrate</em>).
6.  Przypisz te [uprawnienia](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) do nowo zdefiniowanej roli.
7.  Kliknij przycisk **OK**.

**Przypisywanie uprawnień do obiektów vCenter**

Istnieją 2 podejścia do przypisywania uprawnień do obiektów spisu w programie vCenter do konta użytkownika vCenter z przypisaną rolą.
- W celu oceny serwera należy zastosować rolę **tylko do odczytu** dla konta użytkownika vCenter dla wszystkich obiektów nadrzędnych, w których są hostowane maszyny wirtualne, które mają zostać odnalezione. Wszystkie obiekty nadrzędne — host, folder hostów, klaster, folder klastrów w hierarchii do centrum danych. Te uprawnienia są propagowane do obiektów podrzędnych w hierarchii.

    Podobnie w przypadku migracji serwera rola zdefiniowana przez użytkownika (może mieć nazwę <em>Azure _Migrate</em>) z tymi przypisanymi [uprawnieniami](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) musi być stosowana do konta użytkownika vCenter dla wszystkich obiektów nadrzędnych, w których są hostowane maszyny wirtualne do migracji.

  ![Przypisywanie uprawnień](./media/tutorial-assess-vmware/assign-perms.png)

- Alternatywnym podejściem jest przypisanie konta użytkownika i roli na poziomie centrum danych i propagowanie ich do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu (takiego jak maszyny wirtualne), które nie mają być odnajdywane/migrowane. Ta konfiguracja jest nieposkomplikowana. Udostępnia on przypadkowe kontrole dostępu, ponieważ każdy nowy obiekt podrzędny ma również automatycznie udzielony dostęp Dziedziczony z elementu nadrzędnego. W związku z tym zalecamy korzystanie z pierwszej metody.

> [!NOTE]
> Obecnie Ocena serwera nie umożliwia odnajdywania maszyn wirtualnych, jeśli konto vCenter ma dostęp udzielony na poziomie folderu vCenter VM. Jeśli chcesz przeznaczyć zakres odnajdywania według folderów maszyn wirtualnych, możesz to zrobić, upewniając się, że konto vCenter ma dostęp tylko do odczytu na poziomie maszyny wirtualnej.  Poniżej znajdują się instrukcje, jak to zrobić:
>
> 1. Przypisz uprawnienia tylko do odczytu wszystkim maszynom wirtualnym w folderach maszyny wirtualnej, do których chcesz określić zakres odnajdywania.
> 2. Przyznaj dostęp tylko do odczytu do wszystkich obiektów nadrzędnych, w których są hostowane maszyny wirtualne. Wszystkie obiekty nadrzędne — host, folder hostów, klaster, folder klastrów — w hierarchii do centrum danych należy uwzględnić. Nie ma potrzeby propagowania uprawnień do wszystkich obiektów podrzędnych.
> 3. Użyj poświadczeń do odnajdywania, wybierając pozycję Centrum *danych jako zakres kolekcji*. Konfiguracja RBAC gwarantuje, że odpowiedni użytkownik vCenter będzie miał dostęp tylko do maszyn wirtualnych specyficznych dla dzierżawy.
>
> Należy zauważyć, że folder hosty i klastry są obsługiwane.

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po przeprowadzeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne pojawiają się w Azure Portal.

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery** > **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: Ocena serwera.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny oparte na zebranych danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)** : w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie rozmiaru lokalnego maszyny wirtualnej<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu wybieranego do oceny.


### <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen.
2. Na karcie **serwery** w **Azure Migrate: kafelek Ocena serwera** kliknij pozycję **Oceń**.

    ![Ocena](./media/tutorial-assess-vmware/assess.png)

2. W obszarze **ocenianie serwerów**Określ nazwę oceny.
3. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-vmware/view-all.png)

3. W obszarze **Wybierz lub Utwórz grupę**wybierz pozycję **Utwórz nową**, a następnie określ nazwę grupy. Grupa zbiera co najmniej jedną maszynę wirtualną w celu oceny.
4. W obszarze **Dodawanie maszyn do grupy**Wybierz Maszyny wirtualne, które mają zostać dodane do grupy.
5. Kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę, i uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-vmware/assessment-create.png)

6. Po utworzeniu oceny Wyświetl ją w obszarze **serwery** > **Azure Migrate: Ocena serwera** > **ocen**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.



## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy maszyny wirtualne są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeniowe i magazynowe związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Oszacowanie kosztu miesięcznego magazynu**: szacowane koszty magazynu dyskowego po migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W obszarze **cele migracji** >  **serwery**kliknij pozycję **oceny** w **Azure Migrate: Ocena serwera**.
2. W obszarze **oceny**kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości platformy Azure

1. W obszarze **gotowość platformy Azure**Sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
2. Sprawdź stan maszyny wirtualnej:
    - **Gotowe do platformy Azure**: Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

2. Kliknij stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły gotowości maszyn wirtualnych i przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.



### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure.

1. Zapoznaj się z miesięcznymi kosztami obliczeniowymi i magazynem. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Oszacowania kosztów opierają się na zaleceń dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania lokalnych maszyn wirtualnych jako maszyn wirtualnych IaaS. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.

2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły dla określonych maszyn wirtualnych.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Po uruchomieniu ocen opartych na wydajności, do oceny jest przypisywany rating zgodności.

![Ocena zaufania](./media/tutorial-assess-vmware/confidence-rating.png)

- Nadawana jest Ocena z 1 – gwiazdka (najmniejsza) do 5-gwiazdka (najwyższa).
- Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru zapewnianych przez ocenę.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.

Klasyfikacje zaufania dla oceny są następujące.

**Dostępność punktu danych** | **Ocenę zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](best-practices-assessment.md#best-practices-for-confidence-ratings) o najlepszych rozwiązaniach dotyczących klasyfikacji zaufania.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie urządzenia Azure Migrate
> * Tworzenie i przeglądanie oceny

Przejdź do trzeciego samouczka z serii, aby dowiedzieć się, jak migrować maszyny wirtualne VMware na platformę Azure za pomocą migracji Azure Migrate Server.

> [!div class="nextstepaction"]
> [Migrowanie maszyn wirtualnych VMware](./tutorial-migrate-vmware.md)
