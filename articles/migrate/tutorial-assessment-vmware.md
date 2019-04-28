---
title: Odnajdowanie lokalnych maszyn wirtualnych VMware i ocenianie ich gotowości do migracji na platformę Azure za pomocą usługi Azure Migrate | Microsoft Docs
description: W tym artykule opisano sposób odnajdowania lokalnych maszyn wirtualnych VMware i oceniania ich gotowości do migracji na platformę Azure za pomocą usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9eab8a29db40118f2a15064c52419ecebcd4aecb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749890"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Odnajdowanie i ocenianie lokalnych maszyn wirtualnych VMware pod kątem migracji na platformę Azure

Usługa [Azure Migrate](migrate-overview.md) ocenia obciążenia lokalne pod kątem migracji na platformę Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta używanego przez usługę Azure Migrate do odnajdowania lokalnych maszyn wirtualnych.
> * Tworzenie projektu w usłudze Azure Migrate.
> * Konfigurowanie lokalnej maszyny wirtualnej modułu zbierającego w celu odnajdowania lokalnych maszyn wirtualnych VMware do oceny.
> * Grupowanie maszyn wirtualnych i tworzenie oceny.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

- **VMware**: do zarządzania maszynami wirtualnymi, które mają być poddane migracji, musi być używany program vCenter Server w wersji 5.5, 6.0, 6.5 lub 6.7. Ponadto wymagany jest jeden host ESXi z wersją 5.5 lub nowszą w celu wdrożenia maszyny wirtualnej modułu zbierającego.
- **Konto serwera vCenter Server**: wymagane jest konto tylko do odczytu w celu uzyskania dostępu do serwera vCenter Server. To konto jest używane w usłudze Azure Migrate do odnajdowania lokalnych maszyn wirtualnych.
- **Uprawnienia**: na serwerze vCenter Server są wymagane uprawnienia do tworzenia maszyn wirtualnych przez zaimportowanie pliku w formacie OVA.

## <a name="create-an-account-for-vm-discovery"></a>Tworzenie konta na potrzeby odnajdowania maszyny wirtualnej

Usługa Azure Migrate wymaga dostępu do serwerów VMware w celu automatycznego wykrywania maszyn wirtualnych do oceny. Utwórz konto VMware z poniższymi właściwościami. To konto jest wybierane podczas instalowania usługi Azure Migrate.

- Typ użytkownika: Co najmniej użytkownik tylko do odczytu
- Uprawnienia: Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu
- Szczegóły: Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, przypisz do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci) rolę Bez dostępu z propagacją do obiektu podrzędnego.


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-project"></a>Tworzenie projektu

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**.
2. Wyszukaj ciąg **Azure Migrate** i w wynikach wyszukiwania wybierz usługę **Azure Migrate**. Następnie kliknij pozycję **Utwórz**.
3. Wprowadź nazwę projektu i wybierz subskrypcję platformy Azure używaną na jego potrzeby.
4. Utwórz nową grupę zasobów.
5. Określ lokalizację geograficzną, w której chcesz utworzyć projekt, a następnie kliknij pozycję **Utwórz**. Projekt usługi Azure Migrate można utworzyć tylko w poniższych lokalizacjach geograficznych. Można jednak zaplanować migrację do dowolnej docelowej lokalizacji platformy Azure. Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.

**Lokalizacja geograficzna** | **Lokalizacja usługi Storage**
--- | ---
Azure Government | Administracja USA — Wirginia
Azja | Azja Południowo-Wschodnia
Europa | Europa Północna lub Europa Zachodnia
Stany Zjednoczone | Wschodnie stany USA lub Zachodnio-środkowe stany USA

![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę wirtualną, nazywaną modułem zbierającym. Ta maszyna wirtualna służy do odnajdowania lokalnych maszyn wirtualnych VMware i wysyłania dotyczących ich metadanych do usługi Azure Migrate. Aby skonfigurować urządzenie modułu zbierającego, należy pobrać plik OVA i zaimportować go na lokalny serwer vCenter w celu utworzenia maszyny wirtualnej.

1. W projekcie usługi Azure Migrate kliknij pozycję **Wprowadzenie** > **Odnajdź i oceń** > **Odnajdź maszyny**.
2. W obszarze **Odnajdywanie maszyn** kliknij pozycję **Pobierz**, aby pobrać urządzenie.

    Urządzenie usługi Azure Migrate komunikuje się z programem vCenter Server i stale profiluje środowisko lokalne w celu zbierania danych użycia w czasie rzeczywistym dla każdej maszyny wirtualnej. Zbiera ono liczniki szczytowe dla każdej metryki (użycie procesora CPU, użycie pamięci itp.). Ten model nie zależy od ustawień statystyk serwera vCenter na potrzeby zbierania danych o wydajności. Z urządzenia możesz w dowolnym momencie zatrzymać ciągłe profilowanie.

    > [!NOTE]
    > Urządzenie jednorazowego odnajdywania jest już przestarzałe, ponieważ ta metoda opierała się na ustawieniach statystyk programu vCenter Server w zakresie dostępności punktów danych wydajności i zbierała średnią liczników wydajności, co powodowało określanie zbyt małego rozmiaru maszyn wirtualnych na potrzeby migracji na platformę Azure.

    **Szybkie oceny:** w przypadku urządzenia ciągłego odnajdywania po ukończeniu odnajdywania (które trwa kilka godzin w zależności od liczby maszyn wirtualnych) możesz od razu tworzyć oceny. Ponieważ zbieranie danych o wydajności rozpoczyna się wraz z rozpoczęciem odnajdywania, jeśli potrzebujesz szybkich ocen, wybierz w ocenie kryterium ustalania rozmiaru *zgodnie ze środowiskiem lokalnym*. W przypadku ocen na podstawie wydajności zaleca się poczekanie przez co najmniej jeden dzień po rozpoczęciu odnajdywania, aby uzyskać miarodajne zalecenia dotyczące rozmiaru.

    Urządzenie zbiera w sposób ciągły tylko dane dotyczące wydajności i nie wykrywa żadnych zmian konfiguracji w środowisku lokalnym (tzn. dodania lub usunięcia maszyny wirtualnej, dodania dysku itp.). W przypadku zmiany konfiguracji w środowisku lokalnym możesz wykonać następujące działania, aby odzwierciedlić zmiany w portalu:

    - Dodanie elementów (maszyn wirtualnych, dysków, rdzeni itp.): aby uwzględnić te zmiany w witrynie Azure Portal, możesz zatrzymać odnajdywanie z urządzenia i następnie uruchomić je ponownie. Zapewni to, że zmiany zostaną zaktualizowane w projekcie usługi Azure Migrate.

    - Usunięcie maszyn wirtualnych: ze względu na konstrukcję urządzenia, usunięcie maszyny wirtualnej nie zostanie uwzględnione, nawet jeśli zatrzymasz odnajdywanie i uruchomisz je ponownie. Przyczyną jest to, że dane z kolejnych operacji odnajdywania są dołączane do starszych danych, a nie nadpisywane. W takim przypadku możesz po prostu zignorować maszynę wirtualną w portalu, usuwając ją z grupy i obliczając ponownie ocenę.


3. W obszarze **Skopiuj poświadczenia projektu** skopiuj identyfikator projektu i klucz. Będą potrzebne do skonfigurowania modułu zbierającego.

    ![Pobieranie pliku OVA](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Przed wdrożeniem pliku OVA sprawdź, czy jest on bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Wygenerowany skrót powinien odpowiadać następującym ustawieniom.

#### <a name="continuous-discovery"></a>Ciągłe odnajdywanie

  OVA w wersji 1.0.10.11

  **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  OVA w wersji 1.0.10.9

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  Do OVA w wersji 1.0.10.4

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>Jednorazowe odnajdywanie (obecnie przestarzałe)

Ten model jest już przestarzały, pomoc techniczna będzie zapewniania dla istniejących urządzeń.

  OVA w wersji 1.0.9.15

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  OVA w wersji 1.0.9.14

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  OVA w wersji 1.0.9.12

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

## <a name="create-the-collector-vm"></a>Tworzenie maszyny wirtualnej modułu zbierającego

Zaimportuj pobrany plik na serwer vCenter.

1. W konsoli klienta vSphere kliknij pozycję **File** (Plik)  >  **Deploy OVF Template** (Wdróż szablon OVF).

    ![Wdrażanie pliku OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. W kreatorze wdrażania szablonu OVF na stronie **Source** (Źródło) określ lokalizację pliku OVA.
3. W polach **Name** (Nazwa) i **Location** (Lokalizacja) wprowadź przyjazną nazwę maszyny wirtualnej modułu zbierającego oraz obiekt magazynu, w którym będzie hostowana maszyna wirtualna.
5. W polu **Host/Cluster** (Host/klaster) określ host lub klaster, na którym będzie działać maszyna wirtualna modułu zbierającego.
7. W obszarze Magazyn określ docelowy magazyn dla maszyny wirtualnej modułu zbierającego.
8. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
9. W obszarze **Network Mapping** (Mapowanie sieci) określ sieć, z którą będzie się łączyć maszyna wirtualna modułu zbierającego. Sieć musi mieć połączenie z Internetem w celu wysyłania metadanych do platformy Azure.
10. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).

## <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Określ preferencje dotyczące języka, strefy czasowej i hasła dla urządzenia.
3. Na pulpicie kliknij skrót **Run collector** (Uruchom moduł zbierający).
4. Kliknij przycisk **Sprawdź aktualizacje** na górnym pasku modułu zbierającego interfejsu użytkownika i sprawdź, czy działający moduł zbierający to najnowsza wersja. Jeśli tak nie jest, można pobrać najnowszy pakiet aktualizacji przy użyciu łącza i zaktualizować moduł zbierający.
5. W usłudze Azure Migrate Collector otwórz obszar **Set up prerequisites** (Skonfiguruj elementy wymagane wstępnie).
   - Wybierz chmurę platformy Azure, do której planujesz przeprowadzić migrację (Azure Global lub Azure Government).
   - Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
   - Moduł zbierający sprawdzi, czy maszyna wirtualna ma dostęp do Internetu.
   - Jeśli maszyna wirtualna uzyskuje dostęp do Internetu za pośrednictwem serwera proxy, kliknij pozycję **Proxy settings** (Ustawienia serwera proxy) i wprowadź adres serwera proxy oraz port nasłuchujący. Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites) na temat wymagań dotyczących łączności z Internetem i [listy adresów URL](https://docs.microsoft.com/azure/migrate/concepts-collector), do których moduł zbierający uzyskuje dostęp.

     > [!NOTE]
     > Adres serwera proxy musi zostać wprowadzony w protokole http formularza:\//ProxyIPAddress lub http:\//ProxyFQDN. Obsługiwane są tylko serwery proxy HTTP. Jeśli masz przechwytujący serwer proxy, nawiązanie połączenia internetowego może początkowo się nie udać, jeśli nie zaimportowano certyfikatu serwera proxy; [dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-collector) na temat rozwiązywania tego problemu przez zaimportowanie certyfikatu serwera proxy jako zaufanego certyfikatu na maszynie wirtualnej modułu zbierającego.

   - Moduł zbierający sprawdzi, czy usługa modułu zbierającego jest uruchomiona. Jest ona instalowana domyślnie na maszynie wirtualnej modułu zbierającego.
   - Pobierz i zainstaluj program VMware PowerCLI.

6. W obszarze **Specify vCenter Server details** (Określ szczegóły serwera vCenter) wykonaj następujące czynności:
    - Określ nazwę FQDN lub adres IP serwera vCenter.
    - W polach **User name** (Nazwa użytkownika) i **Password** (Hasło) wprowadź poświadczenia konta tylko do odczytu, którego moduł zbierający ma użyć do odnalezienia maszyn wirtualnych na serwerze vCenter.
    - W obszarze **Collection scope** (Zakres zbierania) wybierz zakres odnajdowania maszyn wirtualnych. Moduł zbierający odnajdzie tylko maszyny wirtualne we wskazanym zakresie. Zakresem może być określony folder, centrum danych albo klaster. Zakres nie powinien zawierać więcej niż 1500 maszyn wirtualnych. [Dowiedz się więcej](how-to-scale-assessment.md) o sposobach odnajdywania większego środowiska.

       > [!NOTE]
       > **Zakres zbierania** Wyświetla listę tylko folderów hostów i klastrów. Foldery maszyn wirtualnych nie może bezpośrednio pełnić roli zakres zbierania. Jednak można odnajdywać za pomocą konta vCenter, który ma dostęp do poszczególnych maszyn wirtualnych. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/how-to-scale-assessment#set-up-permissions) o tym, jak do zakresu do folderu maszyn wirtualnych.

7. W obszarze **Specify migration project** (Określ projekt migracji) podaj identyfikator i klucz projektu usługi Azure Migrate, skopiowane z portalu. Jeśli nie zostały skopiowane, otwórz witrynę Azure Portal na maszynie wirtualnej modułu zbierającego. Na stronie **Omówienie** projektu kliknij polecenie **Odnajdź maszyny**, a następnie skopiuj wartości.  
8. W obszarze **View collection progress** (Wyświetl postęp zbierania) monitoruj stan odnajdywania. [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-collector) o tym, jakie dane są zbierane przez usługę Azure Migrate Collector.

> [!NOTE]
> Moduł zbierający obsługuje wyłącznie „Angielski (Stany Zjednoczone)” jako język systemu operacyjnego i język interfejsu modułu zbierającego.
> Jeśli zmienisz ustawienia na ocenianej maszynie, przed uruchomieniem oceny ponownie wyzwól odnajdywanie. W tym celu w module zbierającym użyj opcji **Ponownie uruchom zbieranie**. Po zakończeniu zbierania wybierz opcję **Oblicz ponownie** dla oceny w portalu, aby pobrać zaktualizowane wyniki oceny.


### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Urządzenie zbierające będzie stale profilować środowisko lokalne i będzie nadal wysyłać dane dotyczące wydajności w godzinnych odstępach. Możesz przejrzeć maszyny w portalu po godzinie od rozpoczęcia odnajdywania.

1. W projekcie migracji kliknij pozycję **Zarządzaj** > **Maszyny**.
2. Sprawdź, czy maszyny wirtualne, które miały zostać odnalezione, są widoczne w portalu.


## <a name="create-and-view-an-assessment"></a>Tworzenie i wyświetlanie oceny

Po odnalezieniu maszyn wirtualnych w portalu należy je pogrupować i utworzyć oceny. Oceny zgodnie ze środowiskiem lokalnym możesz tworzyć od razu po odnalezieniu maszyn wirtualnych w portalu. Zalecamy poczekać co najmniej dzień przed utworzeniem jakichkolwiek ocen na podstawie wydajności, aby uzyskać miarodajne zalecenia dotyczące rozmiaru.

1. Na stronie **Omówienie** projektu kliknij pozycję **+Utwórz ocenę**.
2. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.
3. Utwórz grupę i określ jej nazwę.
4. Wybierz maszyny wirtualne, które chcesz dodać do grupy.
5. Kliknij pozycję **Utwórz ocenę**, aby utworzyć grupę i ocenę.
6. Po utworzeniu oceny możesz wyświetlić ją w obszarze **Omówienie** > **Pulpit nawigacyjny**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.

> [!NOTE]
> Przed utworzeniem oceny zdecydowanie zalecamy poczekać co najmniej dzień od uruchomienia odnajdywania. Jeśli chcesz zaktualizować istniejącą ocenę przy użyciu najnowszych danych wydajności, możesz użyć polecenia **Oblicz ponownie** w ramach oceny, aby ją zaktualizować.

### <a name="assessment-details"></a>Szczegóły oceny

Ocena zawiera informacje na temat tego, czy lokalne maszyny wirtualne są zgodne na platformie Azure, jaki byłyby właściwy rozmiar maszyny wirtualnej do uruchamiania maszyny wirtualnej na platformie Azure oraz miesięczne szacowane koszty platformy Azure.

![Raport z oceny](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Gotowość na platformę Azure

Widok gotowości platformy Azure w ocenie przedstawia stan gotowości każdej maszyny wirtualnej. W zależności od właściwości maszyny wirtualnej każda maszyna wirtualna może być oznaczona jako:
- Gotowa na platformę Azure
- Warunkowo gotowa na platformę Azure
- Niegotowa na platformę Azure
- Nieznana gotowość

W przypadku maszyn wirtualnych, które są gotowe do migracji, usługa Azure Migrate wyświetla zalecany rozmiar maszyny wirtualnej na platformie Azure. Zalecenie dotyczące rozmiaru określane przez usługę Azure Migrate zależy od kryterium ustalania rozmiaru określonego we właściwościach oceny. Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, zalecenie dotyczące rozmiaru jest ustalane z uwzględnieniem historii wydajności maszyn wirtualnych (procesor i pamięć) oraz dysków (liczba operacji we/wy na sekundę i przepływność). Jeśli kryterium ustalania rozmiaru brzmi „tak jak lokalnie”, usługa Azure Migrate nie bierze pod uwagę danych dotyczących wydajności maszyny wirtualnej i dysków. Zalecenia dla rozmiaru maszyny wirtualnej na platformie Azure są ustalane na podstawie analizy rozmiaru lokalnej maszyny wirtualnej, ustalanie rozmiaru dysku zależy natomiast od typu magazynu określonego we właściwościach oceny (wartość domyślna to dyski premium). [Dowiedz się więcej](concepts-assessment-calculation.md) o sposobie określania rozmiaru w usłudze Azure Migrate.

Dla maszyn wirtualnych, które nie są gotowe lub są warunkowo gotowe na platformę Azure, usługa Azure Migrate opisuje problemy z gotowością i proponuje instrukcje rozwiązania problemu.

Maszyny wirtualne, dla których usługa Azure Migrate nie może ustalić gotowości na platformę Azure (z powodu niedostępności danych), zostały oznaczone jako nieznana gotowość.

Oprócz gotowości na platformę Azure i ustalenia rozmiaru, usługa Azure Migrate sugeruje również narzędzia, których możesz użyć do migracji maszyny wirtualnej. Wymaga to bardziej szczegółowego odnajdowania w środowisku lokalnym. [Dowiedz się więcej](how-to-get-migration-tool.md) na temat sposobu przeprowadzania bardziej szczegółowego odnajdowania przez zainstalowanie agentów na komputerach lokalnych. Jeśli na maszynach lokalnych nie zainstalowano agentów, zalecane jest przeprowadzenie migracji metodą „lift-and-shift” przy użyciu usługi [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Jeśli na maszynach lokalnych zainstalowano agentów, usługa Azure Migrate analizuje procesy uruchomione na maszynie i sprawdza, czy dana maszyna to maszyna bazy danych. W przypadku maszyny bazy danych sugerowane jest użycie usługi [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). W przeciwnym razie sugerowanym narzędziem migracji jest usługa Azure Site Recovery.

  ![Ocena gotowości](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Szacowany koszt miesięczny

Ten widok przedstawia łączne koszty zasobów obliczeniowych i magazynowych w przypadku korzystania z maszyn wirtualnych na platformie Azure oraz szczegóły dla poszczególnych maszyn. Koszty są szacowane z uwzględnieniem zalecanych przez usługę Azure Migrate rozmiarów maszyny i jej dysków oraz właściwości oceny.

> [!NOTE]
> Szacowany koszt podany w usłudze Azure Migrate dotyczy korzystania z lokalnych maszyn wirtualnych jako maszyn wirtualnych platformy Azure w ramach rozwiązania typu IaaS (infrastruktura jako usługa). Usługa Azure Migrate nie uwzględnia kosztów rozwiązań typu PaaS (platforma jako usługa) i SaaS (oprogramowanie jako usługa).

Szacowany miesięczny koszt zasobów obliczeniowych i magazynowych jest agregowany dla wszystkich maszyn wirtualnych w grupie.

![Ocena kosztu maszyn wirtualnych](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Ocena zaufania

Każda bazująca na wydajności ocena w usłudze Azure Migrate jest skojarzona z oceną zaufania obejmującą zakres od 1 gwiazdki do 5 gwiazdek (1 gwiazdka to najniższa ocena, 5 gwiazdek — najwyższa). Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny. Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate. Ocena zaufania nie ma przełożenia na oceny lokalne.

W przypadku ustalania rozmiaru na podstawie wydajności usługa Azure Migrate potrzebuje danych o użyciu procesora CPU i pamięci maszyny wirtualnej. Ponadto dla każdego dysku podłączonego do maszyny wirtualnej potrzebuje ona danych o liczbie operacji odczytu/zapisu na sekundę i przepływności. Analogicznie dla każdej karty sieciowej podłączonej do maszyny wirtualnej usługa Azure Migrate potrzebuje danych o ruchu wchodzącym/wychodzącym sieci do ustalenia rozmiaru na podstawie wydajności. Jeśli którekolwiek z powyższych danych użycia są niedostępne w programie vCenter Server, zalecenie dotyczące rozmiaru określone przez usługę Azure Migrate może nie być wiarygodne. W zależności od odsetka dostępnych punktów danych ocena zaufania dla oceny jest określana w następujący sposób:

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

Ocena może nie mieć dostępnych wszystkich punktów danych z jednego z następujących powodów:

- Nie profilujesz swojego środowiska przez czas trwania, dla którego tworzysz ocenę. Jeśli na przykład tworzysz ocenę z czasem trwania wydajności ustawionym na 1 dzień, musisz poczekać co najmniej dzień po uruchomieniu odnajdywania, aby zebrać wszystkie punkty danych.

- Kilka maszyn wirtualnych zostało wyłączonych w czasie, dla którego jest obliczana ocena. Jeśli którakolwiek maszyna wirtualna została odłączona od zasilania na pewien czas, nie będziemy mogli zebrać danych o wydajności dla tego okresu.

- Kilka maszyn wirtualnych zostało utworzonych w czasie, dla którego jest obliczana ocena. Jeśli na przykład tworzysz ocenę dla historii wydajności za ostatni miesiąc, ale kilka maszyn wirtualnych zostało utworzonych w środowisku tylko tydzień temu. W takich przypadkach historia wydajności nowych maszyn wirtualnych nie będzie dotyczyła całego czasu oceny.

> [!NOTE]
> Jeśli ocena zaufania dowolnej oceny jest niższa niż 5 gwiazdek, poczekaj co najmniej dzień, aby urządzenie przeprowadziło profilowanie w środowisku, a następnie *Oblicz ponownie* ocenę. Jeśli nie można wykonać powyższego, ustalanie rozmiaru na podstawie wydajności może nie być wiarygodne i zaleca się, aby przełączyć się na *ustalanie rozmiaru jako lokalnego*, zmieniając właściwości oceny.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się](how-to-modify-assessment.md), jak dostosować ocenę zgodnie z wymaganiami.
- Dowiedz się, jak tworzyć grupy o wysokim stopniu pewności na potrzeby oceny za pomocą [mapowania zależności maszyn](how-to-create-group-machine-dependencies.md).
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
- [Dowiedz się](how-to-scale-assessment.md), jak odnajdować i oceniać maszyny wirtualne w dużym środowisku VMware.
- [Dowiedz się więcej](resources-faq.md) na temat usługi Azure Migrate z odpowiedzi na Często zadawane pytania
