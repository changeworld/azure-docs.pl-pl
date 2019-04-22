---
title: Skalowanie odnajdywania i oceny za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Opisuje sposób oceny dużej liczby maszyn lokalnych przy użyciu usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: raynew
ms.openlocfilehash: 1b03cf648ad65960cce4ffc874cf32ad91ef7dc1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59490641"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Odnajdź i oceń duże środowisko programu VMware

Usługa Azure Migrate ma limit 1500 maszyn według projektu, w tym artykule opisano sposób oceny dużą liczbę lokalnych maszyn wirtualnych (VM) przy użyciu [usługi Azure Migrate](migrate-overview.md).

> [!NOTE]
> Mamy wersji zapoznawczej dostępne umożliwiająca odnajdywanie maksymalnie 10 000 maszyn wirtualnych VMware w jednym projekcie przy użyciu pojedynczego urządzenia, jeśli interesują Cię ją wypróbowujesz, zarejestruj się [tutaj.](https://aka.ms/migratefuture)

## <a name="prerequisites"></a>Wymagania wstępne

- **VMware**: Maszyny wirtualne, które mają być poddane migracji, musi być zarządzany przez program vCenter Server w wersji 5.5, 6.0, 6.5 lub 6.7. Ponadto należy jeden host ESXi z wersją 5.5 lub nowszej, aby wdrożyć maszyny Wirtualnej modułu zbierającego.
- **Konto vCenter**: Potrzebujesz konta tylko do odczytu do dostępu do serwera vCenter. To konto jest używane w usłudze Azure Migrate do odnajdowania lokalnych maszyn wirtualnych.
- **Uprawnienia**: W programie vCenter Server, musisz mieć uprawnienia do tworzenia maszyny Wirtualnej, importując plik w formacie OVA.
- **Ustawienia statystyk**: To wymaganie dotyczy tylko [modelu jednorazowe](https://docs.microsoft.com/azure/migrate/concepts-collector) które jest obecnie przestarzałe. W przypadku modelu jednorazowe ustawieniach statystyk programu vCenter Server powinna być równa poziom 3 przed rozpoczęciem wdrażania. Poziom statystyki jest należy ustawić na 3 dla każdego dnia, tygodnia i interwału zbierania miesiąca. Jeśli poziom jest niższy niż 3 dla każdego interwału zbierania trzy, ocena będzie działać, ale nie będą zbierane dane wydajności dotyczące magazynu i sieci. Zalecenia dotyczące rozmiaru, następnie będzie zależeć od dane wydajności dotyczące procesora CPU i pamięci, a dane konfiguracji dla dysku i kart sieciowych.

> [!NOTE]
> Urządzenie jednorazowego odnajdywania jest już przestarzałe, ponieważ ta metoda opierała się na ustawieniach statystyk programu vCenter Server w zakresie dostępności punktów danych wydajności i zbierała średnią liczników wydajności, co powodowało określanie zbyt małego rozmiaru maszyn wirtualnych na potrzeby migracji na platformę Azure.

### <a name="set-up-permissions"></a>Ustawianie uprawnień

Usługa Azure Migrate wymaga dostępu do serwerów VMware w celu automatycznego wykrywania maszyn wirtualnych do oceny. Konto VMware wymaga następujących uprawnień:

- Typ użytkownika: Co najmniej użytkownik tylko do odczytu
- Uprawnienia: Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu
- Szczegóły: Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, przypisz do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci) rolę Bez dostępu z propagacją do obiektu podrzędnego.

Jeśli są wdrażane w środowisku z wieloma dzierżawami i chcesz zakresu folder maszyn wirtualnych dla jednej dzierżawy, nie można bezpośrednio wybierz folder maszyny Wirtualnej, podczas określania zakresu kolekcji w usłudze Azure Migrate. Poniżej przedstawiono instrukcje dotyczące zakresu odnajdowania przez folder maszyn wirtualnych:

1. Utwórz użytkownika na dzierżawę i przypisz uprawnienia tylko do odczytu do wszystkich maszyn wirtualnych należących do określonej dzierżawy. 
2. Przyznaj dostęp użytkownika tylko do odczytu do wszystkich obiektów nadrzędnych, gdzie znajdują się maszyny wirtualne. Wszystkie obiekty nadrzędne - host, folder hostów, klastrów, folder klastrów — w hierarchii do centrum danych mają być włączone. Nie trzeba Propaguj uprawnienia do wszystkich obiektów podrzędnych.
3. Użyj poświadczeń do odnajdowania, wybierając centrum danych jako *zakres zbierania*. ROLACH Ustawianie zapewnia odpowiedniego użytkownika vCenter będzie dostęp do maszyn wirtualnych tylko specyficznym dla dzierżawy.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planowanie migracji projektów i odnajdywania

Na podstawie liczby maszyn wirtualnych są planowane do odnajdywania, można tworzyć wiele projektów i wdrażanie wielu urządzeń w Twoim środowisku. Urządzenia mogą być połączone z jednego serwera vCenter i pojedynczego projektu, (chyba że zatrzymać odnajdywania i rozpocząć od nowa).

W przypadku jednorazowego odnajdywania (przestarzałe teraz), odnajdywania działa w pożar i zapomnij modelu, po zakończeniu odnajdowania, można użyć tego samego modułu zbierającego do zbierania danych z innego serwera vCenter lub wysyłać różne migrację.

> [!NOTE]
> Urządzenie jednorazowego odnajdywania jest już przestarzałe, ponieważ ta metoda opierała się na ustawieniach statystyk programu vCenter Server w zakresie dostępności punktów danych wydajności i zbierała średnią liczników wydajności, co powodowało określanie zbyt małego rozmiaru maszyn wirtualnych na potrzeby migracji na platformę Azure. Zaleca się, aby przejść do urządzenia jednorazowe.

Zaplanuj odnajdywania i oceny, w oparciu o następujące limity:

| **Jednostki** | **Limit komputera** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Odnajdowanie  | 1,500             |
| Ocena | 1,500             |

Należy pamiętać następujące kwestie:

- Po wykonaniu odnajdywanie przy użyciu usługi Azure Migrate collector można ustawić zakres odnajdywania do folderu na serwerze vCenter, centrum danych, klaster lub hosta.
- Aby zrobić więcej niż jedne operacje odnajdywania z tego samego serwera vCenter, sprawdź w programie vCenter Server, które maszyny wirtualne, które miały zostać odnalezione znajdują się w foldery, centrów danych, klastrów i hostów, które obsługują ograniczenie 1500 maszyn.
- Firma Microsoft zaleca, aby do celów oceny pamiętać maszyn za pomocą współzależności w ramach tego samego projektu i oceny. W programie vCenter Server upewnij się, że zależne maszyny znajdują się w tym samym folderze, centrum danych lub klastra na potrzeby oceny.

Zależnie od scenariusza można podzielić czemu poszerzysz swoją wiedzę, jak określono poniżej:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>VCenter wiele serwerów z mniej niż 1500 maszyn wirtualnych
Jeśli masz wiele serwery vCenter w danym środowisku, a łączna liczba maszyn wirtualnych jest mniejszy niż 1500, można użyć następujących podejście, w zależności od danego scenariusza:

**Ciągłe odnajdywania:** W przypadku ciągłego odnajdywania jednego urządzenia może być połączona tylko z jednym projektem. Dlatego należy wdrożyć jeden urządzenie dla każdej serwery vCenter, a następnie utworzenie jednego projektu dla każdego urządzenia i operacje odnajdywania wyzwalacza odpowiednio.

**Jednorazowe odnajdywania (przestarzałe teraz):** Jeden moduł zbierający i projektu migracji służy do odnajdywania wszystkich maszyn wirtualnych na wszystkich serwerach vCenter. Ponieważ moduł zbierający jednorazowe odnajdywania umożliwia odnalezienie jednego serwera vCenter w danym momencie, możesz uruchamiać tego samego modułu zbierającego wszystkie serwery vCenter, jeden po drugim i punktu modułu zbierającego, w tym samym projekcie migracji. Po zakończeniu wszystkich odnalezionych danych dotyczących następnie można utworzyć oceny dla maszyn.


### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>VCenter wiele serwerów z więcej niż 1500 maszyn wirtualnych

Jeśli masz wiele serwery vCenter z mniej niż 1500 maszyn wirtualnych na serwer vCenter, ale więcej niż 1500 maszyn wirtualnych na wszystkich serwerach vCenter, konieczne jest utworzenie kilku projektów migracji (jeden projekt migracji może zawierać tylko 1500 maszyn wirtualnych). Można to osiągnąć, tworząc projekt migracji na serwer vCenter i dzielenia odnajdywania.

**Ciągłe odnajdywania:** Należy utworzyć wiele urządzenia modułu zbierającego (po jednym dla każdego serwera vCenter) i połączyć z każdego urządzenia do odnajdywania projektu i wyzwalacza odpowiednio.

**Jednorazowe odnajdywania (przestarzałe teraz):** Jeden moduł zbierający służy do odnajdywania każdego serwera vCenter, (jeden po drugim). Jeśli chcesz, aby odnajdywania można uruchomić w tym samym czasie, możesz również wdrażanie wielu urządzeń i równoległego uruchamiania odnajdywania.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Więcej niż 1500 maszyn w jednym programie vCenter Server

Jeśli masz więcej niż 1500 maszyn wirtualnych w jednym programie vCenter Server, należy podzielić odnajdywania na wielu projektów migracji. Aby podzielić odnajdywania, można wykorzystać pola zakresu w urządzeniu i określić hosta, klaster, folder hostów, folder klastrów lub centrum danych, które chcesz odnajdywać. Na przykład, jeśli masz dwa foldery w programie vCenter Server, jednym z 1000 maszyn wirtualnych (Folder1) i drugi z 800 maszyn wirtualnych (Folder2), można użyć pola zakresu do dzielenia odnajdywania między te foldery.

**Ciągłe odnajdywania:** W takim przypadku należy utworzyć dwa urządzenia modułu zbierającego, pierwszy modułu zbierającego, określić zakres Folder1 i podłącz go do pierwszy projekt migracji. Możesz równolegle uruchomić odnajdywanie Folder2 przy użyciu drugiego urządzenia modułu zbierającego i podłącz go do drugiego projekt migracji.

**Jednorazowe odnajdywania (przestarzałe teraz):** Możesz używać tego samego modułu zbierającego, do wyzwalania zarówno odnajdywania. W przypadku pierwszego odnajdywania można określić Folder1 jako zakres i wskazać na pierwszy projekt migracji po zakończeniu pierwszego odnajdywania, możesz używać tego samego modułu zbierającego, zmień jego zakres, Folder2 i migracji szczegółów projektu do drugiego projektu migracji i do drugiego odnajdywania.

### <a name="multi-tenant-environment"></a>Środowiska z wieloma dzierżawami

Jeśli masz środowisko, który jest współużytkowany przez dzierżawców i nie chcesz odnajdywanie maszyn wirtualnych z jednej dzierżawy w innej dzierżawie subskrypcji, można użyć pola zakresu w urządzenia modułu zbierającego do określania zakresu odnajdywania. Jeśli dzierżawy współużytkują hostów, Utwórz poświadczenie, które ma dostęp tylko do odczytu do tylko maszyny wirtualne należące do określonej dzierżawy, a następnie korzystać z poświadczenia urządzenia modułu zbierającego i określ zakres jako hosta w celu odnajdywania.

## <a name="discover-on-premises-environment"></a>Odnajdywanie w środowisku lokalnym

Jeśli wszystko jest gotowe w planie, możesz następnie rozpocząć odnajdowanie lokalnych maszyn wirtualnych:

### <a name="create-a-project"></a>Tworzenie projektu

Utwórz projekt usługi Azure Migrate, zgodnie z wymaganiami:

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**.
2. Wyszukaj ciąg **Azure Migrate** i w wynikach wyszukiwania wybierz usługę **Azure Migrate**. Następnie wybierz przycisk **Utwórz**.
3. Określ nazwę projektu i subskrypcji platformy Azure dla projektu.
4. Utwórz nową grupę zasobów.
5. Określ lokalizację, w której chcesz utworzyć projekt, a następnie wybierz **Utwórz**. Należy pamiętać, że nadal można ocenić maszyny wirtualne na inną lokalizację docelową. Lokalizacja określona dla projektu jest używany do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.

### <a name="set-up-the-collector-appliance"></a>Konfigurowanie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę wirtualną, nazywaną modułem zbierającym. Ta maszyna wirtualna odnajduje lokalne maszyny wirtualne z programu VMware i wysyłania dotyczących ich metadanych do usługi Azure Migrate. Aby skonfigurować urządzenie modułu zbierającego, możesz pobrać plik OVA i zaimportować go do lokalnego wystąpienia serwera vCenter.

#### <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Jeśli masz wiele projektów, należy pobrać tylko raz za pomocą urządzenia modułu zbierającego programem vCenter Server. Po pobraniu i skonfiguruj urządzenie, należy uruchomić dla każdego projektu, a następnie określ unikatowy identyfikator i klucz projektu.

1. W projekcie usługi Azure Migrate kliknij pozycję **Wprowadzenie** > **Odnajdź i oceń** > **Odnajdź maszyny**.
2. W obszarze **Odnajdywanie maszyn** kliknij pozycję **Pobierz**, aby pobrać urządzenie.

    Urządzenie usługi Azure Migrate komunikuje się z programem vCenter Server i stale profiluje środowisko lokalne w celu zbierania danych użycia w czasie rzeczywistym dla każdej maszyny wirtualnej. Zbiera ono liczniki szczytowe dla każdej metryki (użycie procesora CPU, użycie pamięci itp.). Ten model nie zależy od ustawień statystyk serwera vCenter na potrzeby zbierania danych o wydajności. Z urządzenia możesz w dowolnym momencie zatrzymać ciągłe profilowanie.

    > [!NOTE]
    > Urządzenie jednorazowego odnajdywania jest już przestarzałe, ponieważ ta metoda opierała się na ustawieniach statystyk programu vCenter Server w zakresie dostępności punktów danych wydajności i zbierała średnią liczników wydajności, co powodowało określanie zbyt małego rozmiaru maszyn wirtualnych na potrzeby migracji na platformę Azure.

    **Natychmiastowa gratyfikacja:** w przypadku urządzenia ciągłego odnajdywania po ukończeniu odnajdywania (które trwa kilka godzin w zależności od liczby maszyn wirtualnych) możesz od razu tworzyć oceny. Ponieważ zbieranie danych o wydajności rozpoczyna się wraz z rozpoczęciem odnajdywania, jeśli potrzebujesz natychmiastowych wyników, wybierz w ocenie kryterium ustalania rozmiaru *zgodnie ze środowiskiem lokalnym*. W przypadku ocen na podstawie wydajności zaleca się poczekanie przez co najmniej jeden dzień po rozpoczęciu odnajdywania, aby uzyskać miarodajne zalecenia dotyczące rozmiaru.

    Należy pamiętać, że urządzenie zbiera w sposób ciągły tylko dane dotyczące wydajności i nie wykrywa żadnych zmian konfiguracji w środowisku lokalnym (tzn. dodania lub usunięcia maszyny wirtualnej, dodania dysku itp.). W przypadku zmiany konfiguracji w środowisku lokalnym możesz wykonać następujące działania, aby odzwierciedlić zmiany w portalu:

    - Dodanie elementów (maszyn wirtualnych, dysków, rdzeni itp.): aby uwzględnić te zmiany w witrynie Azure Portal, możesz zatrzymać odnajdywanie z urządzenia i następnie uruchomić je ponownie. Zapewni to, że zmiany zostaną zaktualizowane w projekcie usługi Azure Migrate.

    - Usunięcie maszyn wirtualnych: ze względu na konstrukcję urządzenia, usunięcie maszyny wirtualnej nie zostanie uwzględnione, nawet jeśli zatrzymasz odnajdywanie i uruchomisz je ponownie. Przyczyną jest to, że dane z kolejnych operacji odnajdywania są dołączane do starszych danych, a nie nadpisywane. W takim przypadku możesz po prostu zignorować maszynę wirtualną w portalu, usuwając ją z grupy i obliczając ponownie ocenę.

3. W **skopiuj poświadczenia projektu**, skopiuj identyfikator i klucz projektu. Będą potrzebne do skonfigurowania modułu zbierającego.


#### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Sprawdź, czy plik OVA jest bezpieczne, przed przystąpieniem do wdrażania:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Upewnij się, że wygenerowanego skrótu odpowiada następujące ustawienia.

#### <a name="continuous-discovery"></a>Ciągłe odnajdywanie

Do OVA w wersji 1.0.10.4

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

#### <a name="one-time-discovery-deprecated-now"></a>Jednorazowe odnajdywanie (obecnie przestarzałe)

Ova w wersji 1.0.9.15 (wydania 10/23/2018 r.)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

Ova w wersji 1.0.9.14 (wydania 8/24/2018 r.)

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

OVA w wersji 1.0.9.8

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | b5d9f0caf15ca357ac0563468c2e6251
SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

OVA w wersji 1.0.9.7

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | d5b6a03701203ff556fa78694d6d7c35
SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

### <a name="create-the-collector-vm"></a>Tworzenie maszyny wirtualnej modułu zbierającego

Zaimportuj pobrany plik na serwer vCenter:

1. W konsoli klienta vSphere wybierz **pliku** > **wdrażania szablonu OVF**.

    ![Wdrażanie pliku OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. W Kreatorze wdrażania szablonu OVF > **źródła**, określ lokalizację pliku OVA.
3. W polach **Name** (Nazwa) i **Location** (Lokalizacja) wprowadź przyjazną nazwę maszyny wirtualnej modułu zbierającego oraz obiekt magazynu, w którym będzie hostowana maszyna wirtualna.
4. W polu **Host/Cluster** (Host/klaster) określ host lub klaster, na którym będzie działać maszyna wirtualna modułu zbierającego.
5. W obszarze Magazyn określ docelowy magazyn dla maszyny wirtualnej modułu zbierającego.
6. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
7. W obszarze **Network Mapping** (Mapowanie sieci) określ sieć, z którą będzie się łączyć maszyna wirtualna modułu zbierającego. Sieć musi mieć łączność z Internetem w celu wysyłania metadanych do platformy Azure.
8. Przejrzyj i Potwierdź ustawienia, a następnie wybierz **Zakończ**.

### <a name="identify-the-id-and-key-for-each-project"></a>Zidentyfikuj identyfikator i klucz dla każdego projektu

Jeśli masz wiele projektów, pamiętaj zidentyfikować identyfikator i klucz dla każdej z nich. Po uruchomieniu modułu zbierającego w celu odnalezienia maszyn wirtualnych, niezbędny jest klucz.

1. W projekcie, wybierz pozycję **wprowadzenie** > **Odnajdź i oceń** > **odnajdź maszyny**.
2. W **skopiuj poświadczenia projektu**, skopiuj identyfikator i klucz projektu.
    ![Skopiuj poświadczenia projektu](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

Dla każdego odnajdywania, które należy wykonać możesz uruchomić moduł zbierający w celu odnalezienia maszyn wirtualnych w zakresie wymagane. Uruchom odnajdywania jeden po drugim. Współbieżne operacje odnajdywania nie są obsługiwane, a poszczególne zadania odnajdywania musi mieć inny zakres.

1.  W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2.  Określ preferencje dotyczące języka, strefy czasowej i hasła dla urządzenia.
3.  Na komputerze stacjonarnym, zaznacz **uruchom moduł zbierający** skrótów.
4.  Moduł usługi Azure Migrate collector Otwórz **Skonfiguruj wymagania wstępne** a następnie:

    a. Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.

    Moduł zbierający sprawdzi, czy maszyna wirtualna ma dostęp do Internetu.

    b. Jeśli maszyna wirtualna uzyskuje dostęp do Internetu za pośrednictwem serwera proxy, zaznacz **ustawienia serwera Proxy**, a następnie określ adres serwera proxy i port nasłuchujący. Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.

    Moduł zbierający sprawdzi, czy usługa modułu zbierającego jest uruchomiona. Jest ona instalowana domyślnie na maszynie wirtualnej modułu zbierającego.

    c. Pobierz i zainstaluj program VMware PowerCLI.

5.  W obszarze **Specify vCenter Server details** (Określ szczegóły serwera vCenter) wykonaj następujące czynności:
    - Określ nazwę (FQDN) lub adres IP serwera vCenter.
    - W **nazwa_użytkownika** i **hasło**, określ poświadczenia konta tylko do odczytu, które moduł zbierający ma użyć do odnalezienia maszyn wirtualnych w programie vCenter Server.
    - W obszarze **Select scope** (Zakres wybierania) wybierz zakres odnajdowania maszyn wirtualnych. Moduł zbierający może odnajdywać tylko maszyny wirtualne w określonym zakresie. Zakresem może być określony folder, centrum danych albo klaster. Nie powinna ona zawierać więcej niż 1000 maszyn wirtualnych.

6.  W **określ projekt migracji**, podaj identyfikator i klucz projektu. Jeśli nie zostaną skopiowane, otwórz witryny Azure portal z maszyny Wirtualnej modułu zbierającego. W projekcie **Przegląd** wybierz opcję **odnajdź maszyny** i skopiuj wartości.  
7.  W **Wyświetl postęp zbierania**, monitorować proces odnajdywania i sprawdź, że metadane zbierane z maszyn wirtualnych znajduje się w zakresie. Moduł zbierający informuje o szacowanym czasie odnajdowania.

#### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Moduł zbierający będzie stale profilu w środowisku lokalnym i będą nadal wysyłać dane dotyczące wydajności z interwałem godzinę. Możesz przejrzeć maszyny w portalu po godzinie od rozpoczęcia odnajdywania. Zdecydowanie zalecamy poczekać co najmniej dzień przed utworzeniem jakichkolwiek ocen na podstawie wydajności dla maszyn wirtualnych.

1. W projekcie migracji kliknij pozycję **Zarządzaj** > **Maszyny**.
2. Sprawdź, czy maszyny wirtualne, które miały zostać odnalezione, są widoczne w portalu.

### <a name="data-collected-from-on-premises-environment"></a>Dane zebrane ze środowiska lokalnego

Modułem zbierającym, odnajduje poniższe dane konfiguracyjne dotyczące wybranych maszyn wirtualnych.

1. Nazwa wyświetlana maszyny Wirtualnej (na klawiaturze vCenter)
2. Ścieżka magazynu maszyny Wirtualnej (w programie vCenter hosta/folder)
3. Adres IP
4. Adres MAC
5. System operacyjny
5. Liczba rdzeni, dysków, kart sieciowych
6. Rozmiar pamięci, rozmiary dysków
7. Liczniki wydajności i maszyny Wirtualnej, dysku i sieci, zgodnie z opisem w poniższej tabeli.

Urządzenie modułu zbierającego zbiera następujące liczniki wydajności dla każdej maszyny Wirtualnej z hosta ESXi w interwału wynoszącego 20 sekund. Te liczniki są liczniki vCenter i chociaż terminologii mówi średnia próbek 20-sekundowe liczników w czasie rzeczywistym. Urządzenie następnie ustala telefoniczny przykłady 20 sekund do utworzenia pojedynczego punktu danych co 15 minut, wybierając wartość szczytowa z próbek 20 sekund i wysyła je do platformy Azure. Dane wydajności dla maszyn wirtualnych zostanie uruchomiony, stają się dostępne w portalu po dwóch godzinach od zostały rozpoczęte odnajdywania. Zdecydowanie zaleca się poczekać co najmniej dzień przed utworzeniem oceny na podstawie wydajności, aby uzyskać dokładne zalecenia dotyczące doboru wielkości. Jeśli szukasz natychmiastowej gratyfikacji, możesz utworzyć oceny przy użyciu kryterium ustalania rozmiaru jako *jako lokalne* zostaną nie będą dane dotyczące wydajności w przypadku ustalania rozmiaru po prawej stronie.

**Counter** |  **Wpływ na ocenę**
--- | ---
cpu.usage.average | Zalecany rozmiar maszyny Wirtualnej i kosztów  
mem.usage.average | Zalecany rozmiar maszyny Wirtualnej i kosztów  
virtualDisk.read.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
virtualDisk.write.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
virtualDisk.numberReadAveraged.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
virtualDisk.numberWriteAveraged.average | Oblicza rozmiar dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
net.received.average | Oblicza rozmiar maszyny Wirtualnej                          
net.transmitted.average | Oblicza rozmiar maszyny Wirtualnej     

> [!WARNING]
> Metoda odnajdywania jednorazowy, który opierał się na ustawienia statystyk serwera vCenter do zbierania danych wydajności jest już przestarzały.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Utwórz grupę](how-to-create-a-group.md) dla oceny.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
