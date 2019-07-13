---
title: Ocenianie maszyn wirtualnych VMware do migracji na platformę Azure za pomocą usługi Azure Migrate
description: W tym artykule opisano, jak i ocenianie lokalnych maszyn wirtualnych VMware do migracji na platformę Azure za pomocą usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 5f70037b1e6ce284b55ff5ff0ae38eb50c320122
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868656"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Ocenianie maszyn wirtualnych VMware z platformą Azure migracji: Server Assessment

W tym artykule dowiesz się, jak i ocenianie lokalnych maszyn wirtualnych VMware, za pomocą usługi Azure Migrate: Narzędzie do oceny wydajności serwera.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm.



Ten samouczek to druga serii, który demonstruje sposób ocenę i migrację maszyn wirtualnych VMware na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj projekt usługi Azure Migrate.
> * Skonfiguruj usługę Azure Migrate urządzenia z systemem lokalnym w celu oceny maszyn wirtualnych.
> * Rozpocznij ciągłe odnajdywanie lokalnych maszyn wirtualnych. Urządzenie wysyła dane dotyczące konfiguracji i wydajności dla odnalezionych maszyn wirtualnych na platformie Azure.
> * Grupować odnalezione maszyny wirtualne, a następnie oceny grupy maszyn wirtualnych.
> * Przejrzyj oceny.



> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania dla scenariusza, aby szybko skonfigurować się weryfikacji koncepcji. Samouczki użyj opcji domyślnych, jeśli jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Aby uzyskać szczegółowe instrukcje Przejrzyj artykuły z poradami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

- [Pełne](tutorial-prepare-vmware.md) pierwszym samouczku tej serii. Jeśli nie, nie będą działać instrukcje w tym samouczku.
- Oto, co powinno przeprowadzonych w pierwszym samouczku:
    - [Ustawianie uprawnień platformy Azure](tutorial-prepare-vmware.md#prepare-azure) dla usługi Azure Migrate.
    - [Przygotowywanie wdrożenia oprogramowania VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) dla oceny. Ustawienia oprogramowania VMware, należy zweryfikować, a powinien mieć uprawnienia do tworzenia maszyny Wirtualnej programu VMware przy użyciu szablonu OVA. Należy również mieć skonfigurowanego konta odnajdowania maszyn wirtualnych. Wymagane porty powinny być dostępne. Ponadto należy pamiętać adresów URL potrzebnych do uzyskania dostępu do platformy Azure.


## <a name="set-up-an-azure-migrate-project"></a>Skonfiguruj projekt usługi Azure Migrate

Skonfiguruj nowy projekt usługi Azure Migrate w następujący sposób.

1. W witrynie Azure portal > **wszystkich usług**, wyszukaj **usługi Azure Migrate**.
2. W obszarze **usług**, wybierz opcję **usługi Azure Migrate**.
3. W **Przegląd**w obszarze **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **oceny i migracji serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-vmware/assess-migrate.png)

4. W **wprowadzenie**, kliknij przycisk **Dodawanie narzędzi**.
5. W **projektu migracji**, a następnie wybierz swoją subskrypcję platformy Azure i Utwórz grupę zasobów, jeśli nie masz.     
6. W **Project Details**, określ nazwę projektu i lokalizacji geograficznej, w której chcesz utworzyć projekt. Azja, Europa, Wielka Brytania i Stanów Zjednoczonych są obsługiwane.

    - Lokalizacja geograficzna projektu jest używany tylko do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.
    - Po uruchomieniu migracji, można wybrać dowolny region docelowy.

    ![Utwórz projekt usługi Azure Migrate](./media/tutorial-assess-vmware/migrate-project.png)


7. Kliknij przycisk **Dalej**.
8. W **narzędzia do oceny wybierz**, wybierz opcję **usługi Azure Migrate: Ocena serwera** > **dalej**.

    ![Utwórz projekt usługi Azure Migrate](./media/tutorial-assess-vmware/assessment-tool.png)

9. W **narzędzie do migracji wybierz**, wybierz opcję **pominąć Dodawanie narzędzia migracji teraz** > **dalej**.
10. W **Przejrzyj + Dodaj narzędzia**, przejrzyj ustawienia i kliknij przycisk **Dodawanie narzędzi**.
11. Poczekaj kilka minut, zanim projekt usługi Azure Migrate do wdrożenia. Powoduje przejście do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać dostęp z **serwerów** na pulpicie nawigacyjnym usługi Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Konfigurowanie urządzenia maszyny Wirtualnej

Azure Migrate: Ocena serwera działa z uproszczonego urządzenia maszyny Wirtualnej VMware.

- Tego urządzenia odnajduje maszyny Wirtualnej i wysyła dane wydajności i metadanych maszyny Wirtualnej do oceny Server migracji platformy Azure.
- Aby skonfigurować urządzenie możesz:
    - Pobieranie pliku OVA szablonu, a następnie zaimportować go do serwera vCenter.
    - Utworzyć to urządzenie, a następnie sprawdź, czy można nawiązać oceny Server migracji platformy Azure.
    - Konfigurowanie urządzenia po raz pierwszy, a następnie zarejestruj go w projekcie usługi Azure Migrate.
- Można skonfigurować wiele urządzeń dla pojedynczego projektu Azure Migrate. We wszystkich urządzeń odnajdywanie maksymalnie 35 000 maszyn wirtualnych jest obsługiwana. Maksymalnie 10 000 serwerów można odnaleźć urządzenia.

### <a name="download-the-ova-template"></a>Pobierz szablon OVA

1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Ocena serwera**, kliknij przycisk **odnajdź**.
2. W **odnajdź maszyny** > **są maszynach zwirtualizowane?** , kliknij przycisk **tak, przy użyciu funkcji VMWare vSphere hypervisor**.
3. Kliknij przycisk **Pobierz** do pobrania. Plik szablonu OVA.

    ![Pobieranie pliku OVA](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Weryfikacja zabezpieczeń

Sprawdź, czy plik OVA jest bezpieczne, przed przystąpieniem do wdrażania.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Dla wersji 1.19.06.27 wygenerowany skrót powinien odpowiadać te wartości. 

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 605d208ac5f4173383f616913441144e
  SHA256 | 447d16bd55f20f945164a1189381ef6e98475b573d6d1c694f3e5c172cfc30d4


### <a name="create-the-appliance-vm"></a>Tworzenie urządzenia maszyny Wirtualnej

Zaimportuj pobrany plik, a następnie utwórz Maszynę wirtualną.

1. W konsoli klienta vSphere kliknij pozycję **File** (Plik)  >  **Deploy OVF Template** (Wdróż szablon OVF).

    ![Wdrażanie pliku OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. W Kreatorze wdrażania szablonu OVF > **źródła**, określ lokalizację pliku OVA.
3. W **nazwa** i **lokalizacji**, określ przyjazną nazwę dla maszyny Wirtualnej. Wybierz obiekt magazynu, w którym będzie hostowana maszyna wirtualna.
5. W **hostem/klastrem**, określ host lub klaster, na które uruchomi maszynę Wirtualną.
6. W **magazynu**, określ docelowy magazyn dla maszyny Wirtualnej.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W **mapowanie sieci**, określ sieć, z którą połączy się maszyna wirtualna. Sieć musi mieć połączenie z Internetem w celu wysyłania metadanych do oceny Server migracji platformy Azure.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


### <a name="verify-appliance-access-to-azure"></a>Sprawdź dostęp urządzenia do platformy Azure

Upewnij się, że urządzenie maszyn wirtualnych można połączyć się z [adresów URL usługi Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie wykonując następujące kroki.

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Zapewniają języka, strefy czasowej i hasła dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną Wirtualną i otwórz adres URL aplikacji sieci web urządzenia: **https://*urządzenia nazwa lub adres IP*: 44368**.

   Alternatywnie możesz otworzyć aplikację na pulpicie urządzenia, klikając skrót aplikacji.
4. W aplikacji sieci web > **Skonfiguruj wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność**: Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij przycisk **ustawienia serwera Proxy**, a następnie określ adres serwera proxy i port nasłuchujący w formie http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Czas synchronizacji**: czas na urządzeniu powinien być zsynchronizowany z czasem internetowym do odnajdowania zapewnić prawidłowe działanie.
    - **Instalowanie aktualizacji**: Urządzenie gwarantuje, że są zainstalowane najnowsze aktualizacje.
    - **Zainstaluj VDDK**: Urządzenie sprawdza zainstalowanym programem VMWare vSphere wirtualnego dysku Development Kit (VDDK) jest zainstalowany.
        - Azure Migrate: Migracja serwera używa VDDK do replikowania maszyn podczas migracji na platformę Azure.
        - Pobierz VDDK 6.7 z programu VMware i Wyodrębnij zawartość pliku zip pobranego w określonej lokalizacji na urządzeniu.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w usłudze Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli nie pojawia się, upewnij się, że zostały wyłączone, blokowanie wyskakujących okienek w przeglądarce.
2. Na nowej karcie Zaloguj się przy użyciu swoich poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Zaloguj się przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Przejdź wstecz do aplikacji sieci web.
2. Wybierz subskrypcję, w której został utworzony projekt usługi Azure Migrate, a następnie wybierz projekt.
3. Określ nazwę dla tego urządzenia. Nazwa powinna być alfanumeryczne z 14 znaków lub mniej.
4. Kliknij przycisk **zarejestrować**.


## <a name="start-continuous-discovery"></a>Uruchom odnajdowanie ciągłego

Teraz nawiązać połączenie z urządzenia do programu vCenter Server i uruchom odnajdywanie maszyn wirtualnych.

1. W **Określ szczegóły serwera vCenter**, określ nazwę (FQDN) lub adres IP serwera vcenter. Pozostaw domyślny port lub określić numery portów, na którym nasłuchuje serwer vCenter.
2. W **nazwa_użytkownika** i **hasło**, określ poświadczenia konta tylko do odczytu, które urządzenie ma użyć do odnalezienia maszyn wirtualnych na serwerze vCenter. Upewnij się, że konto ma [wymaganych uprawnień do odnajdywania](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Można określić zakres odnajdywania, ograniczając dostęp do konta vCenter. Dowiedz się więcej na temat określania zakresu odnajdowania [tutaj](tutorial-assess-vmware.md#scoping-discovery).
3. Kliknij przycisk **sprawdzanie poprawności połączenia** aby upewnić się, czy urządzenie może połączyć się serwerem vCenter.
4. Po nawiązaniu połączenia kliknij **Zapisz i uruchom odnajdywanie**.

Spowoduje to uruchomienie odnajdywania. Trwa około 15 minut metadanych wykrytych maszynach wirtualnych są wyświetlane w portalu.

### <a name="scoping-discovery"></a>Wyznaczanie zakresu odnajdywania

Można zmniejszyć zakres odnajdywania, ograniczając dostęp konto vCenter używane na potrzeby odnajdywania. Można ustawić zakresu vCenter Server w centrach danych, klastrów, folder klastry i hosty, folder hostów lub poszczególne maszyny wirtualne. 

> [!NOTE]
> Już dziś oceny Server nie jest w stanie do odnalezienia maszyn wirtualnych, jeśli konto vCenter ma dostęp udzielany na poziomie folderu programu vCenter maszyny Wirtualnej. Jeśli chcesz ograniczyć zakres odnajdywania przez foldery maszyn wirtualnych, możesz to zrobić, zapewniając vCenter konto ma dostęp tylko do odczytu, przypisana na poziomie maszyny Wirtualnej.  Poniżej przedstawiono instrukcje jak to zrobić:
>
> 1. Przypisz uprawnienia tylko do odczytu na wszystkich maszynach wirtualnych w folderach maszyny Wirtualnej, do których chcesz ograniczyć zakres odnajdywania. 
> 2. Udziel dostępu tylko do odczytu do wszystkich obiektów nadrzędnych, gdzie znajdują się maszyny wirtualne. Wszystkie obiekty nadrzędne - host, folder hostów, klastrów, folder klastrów — w hierarchii do centrum danych mają być włączone. Nie trzeba Propaguj uprawnienia do wszystkich obiektów podrzędnych.
> 3. Użyj poświadczeń do odnajdowania, wybierając centrum danych jako *zakres zbierania*. ROLACH Ustawianie zapewnia odpowiedniego użytkownika vCenter będzie dostęp do maszyn wirtualnych tylko specyficznym dla dzierżawy.
>
> Zanotuj ten folder hosty i klastry są obsługiwane.

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po odnalezieniu możesz sprawdzić, czy maszyny wirtualne są wyświetlane w witrynie Azure portal.

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. W **usługi Azure Migrate — serwery** > **usługi Azure Migrate: Ocena serwera** kliknij ikonę, która wyświetla liczbę elementów w **odnalezione serwery**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można utworzyć przy użyciu usługi Azure Migrate: Ocena serwera.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Na podstawie danych wydajności zebranych ocen | **Zalecany rozmiar maszyny Wirtualnej**: Na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (standardowa lub premium dysku zarządzanego)** : Na podstawie operacji We/Wy i przepływność dysków w środowisku lokalnym.
**Jako lokalne** | Oceny na podstawie lokalnie zmiany rozmiaru. | **Zalecany rozmiar maszyny Wirtualnej**: Na podstawie rozmiaru maszyny Wirtualnej w środowisku lokalnym<br/><br> **Zalecany typ dysku**: Na podstawie magazynu typu ustawienia wybrane dla oceny.


### <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Przegląd [najlepsze praktyki](best-practices-assessment.md) tworzenia oceny.
2. W **serwerów** na karcie **usługi Azure Migrate: Ocena serwera** kafelków, kliknij przycisk **oceny**.

    ![Ocena](./media/tutorial-assess-vmware/assess.png)

2. W **oceny serwerów**, określ nazwę dla oceny.
3. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-vmware/view-all.png)

3. W **wybierz lub Utwórz grupę**, wybierz opcję **Utwórz nowy**, a następnie określ nazwę grupy. Grupy zbiera przynajmniej jednej maszyny wirtualnej w celu oceny.
4. W **Dodaj maszyny do grupy**, wybierz maszyny wirtualne, aby dodać do grupy.
5. Kliknij przycisk **Utwórz ocenę** można utworzyć grupy i uruchomimy ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-vmware/assessment-create.png)

6. Po utworzeniu oceny możesz wyświetlić ją w **serwerów** > **usługi Azure Migrate: Ocena serwera** > **ocen**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.



## <a name="review-an-assessment"></a>Przejrzyj oceny

W tym artykule opisano oceny:

- **Gotowość na platformę Azure**: Czy maszyny wirtualne są odpowiednie dla migracji na platformę Azure.
- **Szacowanie miesięcznych kosztów**: Szacowany miesięczny obliczeń i magazynu koszt korzystania z maszyn wirtualnych na platformie Azure.
- **Szacowania kosztów magazynowania miesięczne**: Szacowane koszty na magazyn na dysku po zakończeniu migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W **celów migracji** >  **serwerów**, kliknij przycisk **ocen** w **usługi Azure Migrate: Ocena serwera**.
2. W **ocen**, kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Sprawdź gotowość na platformę Azure

1. W **gotowości na platformę Azure**, sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
2. Sprawdź stan maszyny Wirtualnej:
    - **Gotowa na platformę Azure**: Usługa Azure Migrate zaleca się rozmiar maszyny Wirtualnej, a koszt szacuje się dla maszyn wirtualnych w ocenie.
    - **Gotowa warunkowo**: Przedstawia problemy i sugerowane rozwiązanie problemu.
    - **Niegotowa na platformę Azure**: Przedstawia problemy i sugerowane rozwiązanie problemu.
    - **Nieznana gotowość**: Używane, gdy usługa Azure Migrate nie może ocenić gotowość, z powodu problemów dotyczących dostępności danych.

2. Kliknij pozycję **gotowości na platformę Azure** stanu. Możesz wyświetlić szczegóły gotowości maszyn wirtualnych i przejść do szczegółów maszyny Wirtualnej, w tym obliczeń, magazynu i ustawienia sieci.



### <a name="review-cost-details"></a>Szczegóły przeglądu kosztów

Ten widok przedstawia szacowany koszt zasobów obliczeniowych i magazynowych uruchamiania maszyn wirtualnych na platformie Azure.

1. Przejrzyj miesięczne koszty zasobów obliczeniowych i magazynowych. Koszty są agregowane dla wszystkich maszyn wirtualnych w grupie ocenione.

    - Szacowanie kosztów są oparte na zaleceń dotyczących rozmiarów maszyny i jej właściwości i dysków.
    - Szacowany miesięczny koszt zasobów obliczeniowych i magazynu są wyświetlane.
    - Szacowanie kosztów dotyczy uruchamiania lokalnych maszyn wirtualnych jako maszyn wirtualnych IaaS. Ocena serwera migracji platformy Azure nie bierze pod uwagę koszty PaaS i SaaS.

2. Możesz przejrzeć miesięczne szacowane koszty magazynowania. Ten widok pokazuje kosztów magazynowania zagregowane grupy ocenić Podziel za pośrednictwem różnych typów dysków w magazynie.
3. Przechodzić do szczegółów dla określonych maszyn wirtualnych.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Po uruchomieniu oceny na podstawie wydajności, ocena zaufania jest przypisana do oceny.

![Ocena zaufania](./media/tutorial-assess-vmware/confidence-rating.png)

- Ocena od 1 gwiazdki (najniższe) do 5 gwiazdek (najwyżej) są przyznawane.
- Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiarów, dostarczone przez ocenę.
- Ocena zaufania jest na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.

Poniżej znajdują się oceny zaufania dla oceny.

**Dostępność punktu danych.** | **Ocenę zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](best-practices-assessment.md#best-practices-for-confidence-ratings) najlepszymi rozwiązaniami dotyczącymi oceny zaufania.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie urządzenia w usłudze Azure Migrate
> * Utworzone i przeglądu oceny

W dalszym ciągu to trzeci samouczek z tej serii, aby dowiedzieć się, jak przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure Migration migracji serwera.

> [!div class="nextstepaction"]
> [Migrowanie maszyn wirtualnych VMware](./tutorial-migrate-vmware.md)
