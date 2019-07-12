---
title: Ocenianie maszyn wirtualnych funkcji Hyper-V do migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak i ocenianie lokalnych maszyn wirtualnych funkcji Hyper-V pod kątem migracji do platformy Azure przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 83567a45980b29931f9b68bd6d60df0d427b09de
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813018"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Ocenianie maszyn wirtualnych funkcji Hyper-V, za pomocą platformy Azure migracji serwera oceny

W tym artykule dowiesz się, jak i ocenianie lokalnych maszyn wirtualnych funkcji Hyper-V, za pomocą usługi Azure Migrate: Narzędzie do oceny wydajności serwera.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm.



Ten samouczek to druga serii, który demonstruje sposób ocenę i migrację maszyn wirtualnych funkcji Hyper-V na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj projekt usługi Azure Migrate.
> * Konfigurowanie i rejestrowanie urządzenia w usłudze Azure Migrate.
> * Rozpocznij ciągłe odnajdywanie lokalnych maszyn wirtualnych.
> * Grupować odnalezione maszyny wirtualne, a oceny grupy.
> * Przejrzyj oceny.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania dla scenariusza, aby szybko skonfigurować się weryfikacji koncepcji. Samouczki użyj opcji domyślnych, jeśli jest to możliwe, a nie pokazuj wszystkich możliwych ustawień i ścieżki. Aby uzyskać szczegółowe instrukcje Przejrzyj artykuły z poradami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

- [Pełne](tutorial-prepare-hyper-v.md) pierwszym samouczku tej serii. Jeśli nie, nie będą działać instrukcje w tym samouczku.
- Oto, co powinno przeprowadzonych w pierwszym samouczku:
    - [Ustawianie uprawnień platformy Azure](tutorial-prepare-hyper-v.md#prepare-azure) dla usługi Azure Migrate.
    - [Przygotowywanie funkcji Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) klastrów hostów i maszyn wirtualnych do oceny.

## <a name="set-up-an-azure-migrate-project"></a>Skonfiguruj projekt usługi Azure Migrate

1. W witrynie Azure portal > **wszystkich usług**, wyszukaj **usługi Azure Migrate**.
2. W wynikach wyszukiwania wybierz **usługi Azure Migrate**.
3. W **Przegląd**w obszarze **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **oceny i migracji serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. W **wprowadzenie**, kliknij przycisk **Dodawanie narzędzi**.
5. W **projektu migracji** karty, wybierz swoją subskrypcję platformy Azure i Utwórz grupę zasobów, jeśli nie masz.
6. W **Project Details**, określ nazwę projektu i region, w którym chcesz utworzyć projekt.


    ![Utwórz projekt usługi Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

    Projekt usługi Azure Migrate można utworzyć w tych regionach.

    **Lokalizacja geograficzna** | **Region**
    --- | ---
    Azja  | Azja Południowo-Wschodnia
    Europa | Europa Północna lub Europa Zachodnia
    Zjednoczone Królestwo |  Południowe Zjednoczone Królestwo i zachodnie Zjednoczone Królestwo
    Stany Zjednoczone | Wschodnie stany USA, zachodnie stany USA 2 i środkowe stany USA zachodnie

    - Region projektu jest używany tylko do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.
    - Podczas migracji maszyn wirtualnych, możesz wybrać region innej docelowej platformy Azure. Wszystkie regiony platformy Azure są obsługiwane dla miejsca docelowego migracji.

7. Kliknij przycisk **Dalej**.
8. W **narzędzia do oceny wybierz**, wybierz opcję **usługi Azure Migrate: Ocena serwera** > **dalej**.

    ![Utwórz projekt usługi Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. W **narzędzie do migracji wybierz**, wybierz opcję **pominąć Dodawanie narzędzia migracji teraz** > **dalej**.
10. W **Przejrzyj + Dodaj narzędzia**, przejrzyj ustawienia i kliknij przycisk **Dodawanie narzędzi**.
11. Poczekaj kilka minut, zanim projekt usługi Azure Migrate do wdrożenia. Powoduje przejście do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać dostęp z **serwerów** na pulpicie nawigacyjnym usługi Azure Migrate.




## <a name="set-up-the-appliance-vm"></a>Konfigurowanie urządzenia maszyny Wirtualnej

Ocena serwera migracji platformy Azure działa z uproszczonego urządzenia maszyny Wirtualnej funkcji Hyper-V.

- To urządzenie wykonuje odnajdowanie maszyn wirtualnych i wysyła dane wydajności i metadanych maszyny Wirtualnej do usługi Azure Migrate: Ocena serwera.
- Aby skonfigurować urządzenie możesz:
    - Pobierz skompresowany wirtualnego dysku twardego funkcji Hyper-V w witrynie Azure portal.
    - Utworzyć to urządzenie, a następnie sprawdź, czy można nawiązać oceny Server migracji platformy Azure.
    - Konfigurowanie urządzenia po raz pierwszy, a następnie zarejestruj go w projekcie usługi Azure Migrate.

### <a name="download-the-vhd"></a>Pobieranie wirtualnego dysku twardego

Pobierz skompresowany szablon wirtualnego dysku twardego dla urządzenia.

1. W **celów migracji** > **serwerów** > **usługi Azure Migrate: Ocena serwera**, kliknij przycisk **odnajdź**.
2. W **odnajdź maszyny** > **są maszynach zwirtualizowane?** , kliknij przycisk **tak, przy użyciu funkcji Hyper-V**.
3. Kliknij przycisk **Pobierz** można pobrać pliku wirtualnego dysku twardego.

    ![Pobieranie maszyny Wirtualnej](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Weryfikacja zabezpieczeń

Sprawdzanie pliku zip jest bezpieczne, przed jego wdrożeniem.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku dysku VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Dla wersji urządzenia 1.19.06.27 wygenerowany skrót powinien odpowiadać następującym ustawieniom.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 3681f745fa2b0a0a6910707d85161ec5
  SHA256 | e6ca109afab9657bdcfb291c343b3e3abced9a273d25273059171f9954d25832



### <a name="create-the-appliance-vm"></a>Tworzenie urządzenia maszyny Wirtualnej

Zaimportuj pobrany plik, a następnie utwórz maszynę Wirtualną.

1. Wyodrębnij spakowany plik wirtualnego dysku twardego do folderu na hoście funkcji Hyper-V, który będzie obsługiwać urządzenia maszyny Wirtualnej. Trzy foldery zostały wyodrębnione.
2. Otwórz Menedżera funkcji Hyper-V. W **akcje**, kliknij przycisk **importowanie maszyny wirtualnej**.

    ![Wdrażanie wirtualnego dysku twardego](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. W Kreatorze importowania maszyny wirtualnej > **przed rozpoczęciem**, kliknij przycisk **dalej**.
3. W **zlokalizuj Folder**, określ folder zawierający wyodrębnione wirtualnego dysku twardego. Następnie kliknij przycisk **Next** (Dalej).
1. W **Wybieranie maszyny wirtualnej**, kliknij przycisk **dalej**.
2. W **wybierz typ importu**, kliknij przycisk **kopiowania maszyny wirtualnej (Utwórz nowy unikatowy identyfikator)** . Następnie kliknij przycisk **Next** (Dalej).
3. W **wybierz lokalizację docelową**, pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W **foldery przechowywania**, pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W **sieci wybierz**, określ przełącznik wirtualny, który będzie używany przez maszynę Wirtualną. Przełącznik wymaga połączenia internetowego z wysyłania danych do platformy Azure.
6. W **Podsumowanie**, przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **maszyn wirtualnych**, uruchom maszynę Wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Sprawdź dostęp urządzenia do platformy Azure

Upewnij się, że urządzenie maszyn wirtualnych można połączyć się z [adresów URL usługi Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. W Menedżerze funkcji Hyper-V > **maszyn wirtualnych**, kliknij prawym przyciskiem myszy maszynę Wirtualną > **Connect**.
2. Zapewniają języka, strefy czasowej i hasła dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną Wirtualną i otwórz adres URL aplikacji sieci web urządzenia: **https://*urządzenia nazwa lub adres IP*: 44368**.

   Alternatywnie możesz otworzyć aplikację na pulpicie urządzenia, klikając skrót aplikacji.
1. W aplikacji sieci web > **Skonfiguruj wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność**: Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij przycisk **ustawienia serwera Proxy**, a następnie określ adres serwera proxy i port nasłuchujący w formie http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Czas synchronizacji**: Czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym odnajdowania maszyn wirtualnych zapewnić prawidłowe działanie.
    - **Instalowanie aktualizacji**: Ocena serwera migracji platformy Azure sprawdza, czy urządzenie ma zainstalowane najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w usłudze Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli nie pojawia się, upewnij się, że zostały wyłączone, blokowanie wyskakujących okienek w przeglądarce.
2. Na nowej karcie Zaloguj się przy użyciu swoich poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Zaloguj się przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Przejdź wstecz do aplikacji sieci web.
4. Wybierz subskrypcję, w której został utworzony projekt usługi Azure Migrate. Następnie wybierz projekt.
5. Określ nazwę dla tego urządzenia. Nazwa powinna być alfanumeryczne z 14 znaków lub mniej.
6. Kliknij przycisk **zarejestrować**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla wirtualnych dysków twardych SMB

Jeśli korzystasz z wirtualnych dysków twardych w małych i średnich firmach, należy włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. Jeśli nie możesz tego zrobić na każdym hoście w [poprzedniego samouczka](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), czy to teraz z urządzenia:

1. Na urządzeniu maszyny Wirtualnej Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowe nazwy hosta.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Alternatywnie wykonaj następujące czynności w lokalnej edytorze zasad grupy na urządzeniu:
    - W **lokalne zasady komputera** > **konfiguracji komputera**, kliknij przycisk **Szablony administracyjne** > **systemu**  >  **Poświadczenia delegowania**.
    - Kliknij dwukrotnie **Zezwalaj na delegowanie świeżych poświadczeń**i wybierz **włączone**.
    - W **opcje**, kliknij przycisk **Pokaż**, i Dodaj każdy host funkcji Hyper-V, odnajdywać do listy, za pomocą **wsman /** jako prefiksu.
    - W **delegowanie poświadczeń**, kliknij dwukrotnie **Zezwalaj na delegowanie świeżych poświadczeń przy użyciu uwierzytelniania NTLM — serwer tylko**. Ponownie Dodaj każdy host funkcji Hyper-V, odnajdywać do listy, za pomocą **wsman /** jako prefiksu.

## <a name="start-continuous-discovery"></a>Uruchom odnajdowanie ciągłego

Nawiązywanie połączenia z urządzenia lub klastrów hostów funkcji Hyper-V i uruchom odnajdowanie maszyn wirtualnych.

1. W **nazwa_użytkownika** i **hasło**, określ poświadczenia konta, które urządzenie ma użyć do odnalezienia maszyn wirtualnych. Określ przyjazną nazwę dla poświadczeń, a następnie kliknij przycisk **Zapisz szczegóły**.
2. Kliknij przycisk **Dodaj host**i określ szczegóły host/klaster funkcji Hyper-V.
3. Kliknij przycisk **zweryfikować**. Po zakończeniu walidacji jest wyświetlana liczba maszyn wirtualnych, które mogą być wykrywane na każdy host/klaster.
    - Jeśli sprawdzanie poprawności nie powiedzie się z hostem, Przejrzyj tekst błędu, umieszczając kursor myszy nad ikoną w **stan** kolumny. Rozwiąż problemy i ponownie zweryfikować.
    - Aby usunąć hosty lub klastry, zaznacz > **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Usuwać można tylko te całego klastra.
    - Możesz dodać klaster, nawet jeśli występują problemy z określonych hostów w klastrze.
4. Po zakończeniu walidacji kliknij **Zapisz i uruchom odnajdywanie** można uruchomić procesu odnajdywania.

Spowoduje to uruchomienie odnajdywania. Trwa około 15 minut metadanych wykrytych maszynach wirtualnych są wyświetlane w witrynie Azure portal.

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu odnajdowania, można sprawdzić, czy maszyny wirtualne są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. W **usługi Azure Migrate — serwery** > **usługi Azure Migrate: Ocena serwera** kliknij ikonę, która wyświetla liczbę elementów w **odnalezione serwery**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można uruchomić za pomocą oceny Server migracji platformy Azure.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Na podstawie danych wydajności zebranych ocen | **Zalecany rozmiar maszyny Wirtualnej**: Na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (standardowa lub premium dysku zarządzanego)** : Na podstawie operacji We/Wy i przepływność dysków w środowisku lokalnym.
**Jako lokalne** | Oceny na podstawie lokalnie zmiany rozmiaru. | **Zalecany rozmiar maszyny Wirtualnej**: Na podstawie rozmiaru maszyny Wirtualnej w środowisku lokalnym<br/><br> **Zalecany typ dysku**: Na podstawie magazynu typu ustawienia wybrane dla oceny.



### <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Przegląd [najlepsze praktyki](best-practices-assessment.md) tworzenia oceny.
2. W **serwerów** > **usługi Azure Migrate: Ocena serwera**, kliknij przycisk **oceny**.

    ![Ocena](./media/tutorial-assess-hyper-v/assess.png)

3. W **oceny serwerów**, określ nazwę dla oceny.
4. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. W **wybierz lub Utwórz grupę**, wybierz opcję **Utwórz nowy** i podaj nazwę grupy. Grupy zbiera przynajmniej jednej maszyny wirtualnej w celu oceny.
4. W **Dodaj maszyny do grupy**, wybierz maszyny wirtualne, aby dodać do grupy.
5. Kliknij przycisk **Utwórz ocenę** można utworzyć grupy i uruchomimy ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Po utworzeniu oceny możesz wyświetlić ją w **serwerów** > **usługi Azure Migrate: Ocena serwera**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.


## <a name="review-an-assessment"></a>Przejrzyj oceny

W tym artykule opisano oceny:

- **Gotowość na platformę Azure**: Czy maszyny wirtualne są odpowiednie dla migracji na platformę Azure.
- **Szacowanie miesięcznych kosztów**: Szacowany miesięczny obliczeń i magazynu koszt korzystania z maszyn wirtualnych na platformie Azure.
- **Szacowania kosztów magazynowania miesięczne**: Szacowane koszty na magazyn na dysku po zakończeniu migracji.


### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W **celów migracji** >  **serwerów** > **usługi Azure Migrate: Ocena serwera**, kliknij przycisk **ocen**.
2. W **ocen**, kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Ocena zaufania](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

W dalszym ciągu to trzeci samouczek z tej serii, aby dowiedzieć się, jak przeprowadzić migrację maszyn wirtualnych funkcji Hyper-V na platformę Azure za pomocą usługi Azure Migration migracji serwera.

> [!div class="nextstepaction"]
> [Migrowanie maszyn wirtualnych funkcji Hyper-V](./tutorial-migrate-hyper-v.md)
