---
title: Skalowanie odnajdywania i oceny za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Opisuje sposób oceny dużej liczby maszyn lokalnych przy użyciu usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: f289e9745eb220e735cc5c58ce8006c9fea7e61e
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086662"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Odnajdź i oceń duże środowisko programu VMware

Usługa Azure Migrate ma limit 1500 maszyn według projektu, w tym artykule opisano sposób oceny dużą liczbę lokalnych maszyn wirtualnych (VM) przy użyciu [usługi Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Wymagania wstępne

- **VMware**: maszyn wirtualnych, które mają być poddane migracji, musi być zarządzane przez program vCenter Server w wersji 5.5, 6.0 lub 6.5. Ponadto należy jeden host ESXi z wersją 5.0 lub nowszej, aby wdrożyć maszyny Wirtualnej modułu zbierającego.
- **Konto vCenter**: musisz mieć konto tylko do odczytu do dostępu do serwera vCenter. To konto jest używane w usłudze Azure Migrate do odnajdowania lokalnych maszyn wirtualnych.
- **Uprawnienia**: W programie vCenter Server, są wymagane uprawnienia do tworzenia maszyny Wirtualnej, importując plik w formacie OVA.
- **Ustawienia statystyk**: to wymaganie dotyczy tylko [modelu jednorazowe](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods). W przypadku modelu jednorazowe ustawieniach statystyk programu vCenter Server powinna być równa poziom 3 przed rozpoczęciem wdrażania. Poziom statystyki jest należy ustawić na 3 dla każdego dnia, tygodnia i interwału zbierania miesiąca. Jeśli poziom jest niższy niż 3 dla każdego interwału zbierania trzy, ocena będzie działać, ale nie będą zbierane dane wydajności dotyczące magazynu i sieci. Zalecenia dotyczące rozmiaru, następnie będzie zależeć od dane wydajności dotyczące procesora CPU i pamięci, a dane konfiguracji dla dysku i kart sieciowych.

### <a name="set-up-permissions"></a>Ustawianie uprawnień

Usługa Azure Migrate wymaga dostępu do serwerów VMware w celu automatycznego wykrywania maszyn wirtualnych do oceny. Konto VMware wymaga następujących uprawnień:

- Typ użytkownika: co najmniej użytkownik tylko do odczytu
- Uprawnienia: obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu
- Szczegóły: użytkownik przypisany na poziomie centrum danych z dostępem do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, przypisz do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci) rolę Bez dostępu z propagacją do obiektu podrzędnego.

Jeśli jest wdrażane w środowisku dzierżawy, w tym miejscu jest jednym ze sposobów konfigurowania tego:

1.  Tworzenie użytkownika na dzierżawę i przy użyciu [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), przypisać uprawnienia tylko do odczytu do wszystkich maszyn wirtualnych należących do określonej dzierżawy. Następnie należy użyć tych poświadczeń do odnajdywania. RBAC gwarantuje, że odpowiednie użytkownik vCenter będzie miał dostęp do maszyn wirtualnych tylko specyficznym dla dzierżawy.
2. Konfigurowania funkcji RBAC dla użytkowników innej dzierżawy zgodnie z opisem w poniższym przykładzie dla użytkownika nr 1 i 2 użytkownika:

    - W **nazwa_użytkownika** i **hasło**, określ poświadczenia konta tylko do odczytu, które moduł zbierający ma użyć do odnalezienia maszyn wirtualnych w
    - Datacenter1 - przyznać uprawnienia tylko do odczytu do 1 użytkownika i użytkownika nr 2. Nie propagować te uprawnienia do wszystkich obiektów podrzędnych, ponieważ uprawnienia zostanie ustawiona na poszczególnych maszynach wirtualnych.

      - Maszyna VM1 (dzierżawy #1) (uprawnienia tylko do odczytu do 1 użytkownika)
      - VM2 (dzierżawy #1) (uprawnienia tylko do odczytu do 1 użytkownika)
      - Maszyna VM3 (dzierżawy nr 2) (uprawnienia tylko do odczytu do użytkownika nr 2)
      - VM4 (dzierżawy nr 2) (uprawnienia tylko do odczytu do użytkownika nr 2)

   - Jeśli przeprowadzisz odnajdywanie przy użyciu poświadczeń użytkownika nr 1, następnie tylko maszyna VM1 i VM2 zostaną odnalezione.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planowanie migracji projektów i odnajdywania

Jeden moduł zbierający usługi Azure Migrate obsługuje odnajdywania z wielu serwerów programu vCenter, (jeden po drugim) i umożliwia wykrywanie do wielu projektów migracji (jeden po drugim).

Moduł zbierający, w przypadku jednorazowego odnajdywania działa w pożar i zapomnij modelu, po zakończeniu odnajdowania, można użyć tego samego modułu zbierającego do zbierania danych z innego serwera vCenter lub wysyłać różne migrację. W przypadku ciągłego odnajdywania jednego urządzenia jest podłączony do pojedynczego projektu, więc nie możesz użyć tego samego modułu zbierającego do wyzwolenia odnajdywania drugiego.

Zaplanuj odnajdywania i oceny, w oparciu o następujące limity:

| **Jednostki** | **Limit komputera** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Odnajdowanie  | 1,500             |
| Ocena | 1,500             |

Należy pamiętać następujące kwestie:

- Po wykonaniu odnajdywanie przy użyciu usługi Azure Migrate collector można ustawić zakres odnajdywania do folderu na serwerze vCenter, centrum danych, klaster lub hosta.
- Aby zrobić więcej niż jedne operacje odnajdywania, sprawdź w programie vCenter Server, które maszyny wirtualne, które miały zostać odnalezione znajdują się w foldery, centrów danych, klastrów i hostów, które obsługują ograniczenie 1500 maszyn.
- Firma Microsoft zaleca, aby do celów oceny pamiętać maszyn za pomocą współzależności w ramach tego samego projektu i oceny. W programie vCenter Server upewnij się, że zależne maszyny znajdują się w tym samym folderze, centrum danych lub klastra na potrzeby oceny.

Zależnie od scenariusza można podzielić czemu poszerzysz swoją wiedzę, jak określono poniżej:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>VCenter wiele serwerów z mniej niż 1500 maszyn wirtualnych
Jeśli masz wiele serwery vCenter w danym środowisku, a łączna liczba maszyn wirtualnych jest mniejszy niż 1500, można użyć następujących podejście, w zależności od danego scenariusza:

**Jednorazowe:** jeden moduł zbierający i projekt migracji można użyć do odnajdywania wszystkich maszyn wirtualnych na wszystkich serwerach vCenter. Ponieważ moduł zbierający jednorazowe odnajdywania umożliwia odnalezienie jednego serwera vCenter w danym momencie, możesz uruchamiać tego samego modułu zbierającego wszystkie serwery vCenter, jeden po drugim i punktu modułu zbierającego, w tym samym projekcie migracji. Po zakończeniu wszystkich odnalezionych danych dotyczących następnie można utworzyć oceny dla maszyn.

**Ciągłe odnajdywania:** w przypadku ciągłego odnajdywania jednego urządzenia można je podłączyć do pojedynczego projektu. Dlatego należy wdrożyć jeden urządzenie dla każdej serwery vCenter, a następnie utworzenie jednego projektu dla każdego urządzenia i operacje odnajdywania wyzwalacza odpowiednio.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>VCenter wiele serwerów z więcej niż 1500 maszyn wirtualnych

Jeśli masz wiele serwery vCenter z mniej niż 1500 maszyn wirtualnych na serwer vCenter, ale więcej niż 1500 maszyn wirtualnych na wszystkich serwerach vCenter, konieczne jest utworzenie kilku projektów migracji (jeden projekt migracji może zawierać tylko 1500 maszyn wirtualnych). Można to osiągnąć, tworząc projekt migracji na serwer vCenter i dzielenia odnajdywania.

**Jednorazowe:** jeden moduł zbierający służy do odnajdywania każdego serwera vCenter, (jeden po drugim). Jeśli chcesz, aby odnajdywania można uruchomić w tym samym czasie, możesz również wdrażanie wielu urządzeń i równoległego uruchamiania odnajdywania.

**Ciągłe odnajdywania:** należy utworzyć wiele urządzenia modułu zbierającego (po jednym dla każdego serwera vCenter) i nawiązać połączenie z każdego urządzenia odnajdywania projektu i wyzwalacza odpowiednio.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Więcej niż 1500 maszyn w jednym programie vCenter Server

Jeśli masz więcej niż 1500 maszyn wirtualnych w jednym programie vCenter Server, należy podzielić odnajdywania na wielu projektów migracji. Aby podzielić odnajdywania, można wykorzystać pola zakresu w urządzeniu i określić hosta, klaster, folder lub centrum danych, które chcesz odnajdywać. Na przykład, jeśli masz dwa foldery w programie vCenter Server, jednym z 1000 maszyn wirtualnych (Folder1) i drugi z 800 maszyn wirtualnych (Folder2), można użyć pola zakresu do dzielenia odnajdywania między te foldery.

**Jednorazowe:** można użyć tego samego modułu zbierającego do wyzwolenia odnajdywania obu. W przypadku pierwszego odnajdywania można określić Folder1 jako zakres i wskazać na pierwszy projekt migracji po zakończeniu pierwszego odnajdywania, możesz używać tego samego modułu zbierającego, zmień jego zakres, Folder2 i migracji szczegółów projektu do drugiego projektu migracji i do drugiego odnajdywania.

**Ciągłe odnajdywania:** w takim przypadku należy utworzyć dwa urządzenia modułu zbierającego, pierwszy modułu zbierającego, określić zakres Folder1 i podłącz go do pierwszy projekt migracji. Możesz równolegle uruchomić odnajdywanie Folder2 przy użyciu drugiego urządzenia modułu zbierającego i podłącz go do drugiego projekt migracji.

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
2. W obszarze **Odnajdź maszyny** dostępne są dwie opcje dla urządzenia. Kliknij przycisk **Pobierz**, aby pobrać odpowiednie urządzenie na podstawie swoich preferencji.

    a. **Odnajdywanie jednorazowe:** urządzenie dla tego modelu komunikuje się z serwerem vCenter, aby zebrać metadane dotyczące maszyn wirtualnych. Podczas zbierania danych wydajności maszyn wirtualnych opiera się ono na historycznych danych wydajności przechowywanych na serwerze vCenter i zbiera historię wydajności za ostatni miesiąc. W tym modelu usługi Azure Migrate zbiera dane licznika średni, (a licznik szczytowa) dla każdego metryki [więcej](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected). Ponieważ jest jednorazowe, zmiany w środowisku lokalnym nie są odzwierciedlane po ukończeniu odnajdywania. Jeśli chcesz odzwierciedlić zmiany, musisz przeprowadzić ponowne odnajdywanie w tym samym środowisku dla tego samego projektu.

    b. **Ciągłe odnajdywanie:** urządzenie dla tego modelu stale profiluje środowisko lokalne w celu zebrania danych użycia w czasie rzeczywistym dla każdej maszyny wirtualnej. W tym modelu liczniki szczytowe są zbierane dla każdej metryki (użycie procesora CPU, użycie pamięci itp.). Ten model nie zależy od ustawień statystyk serwera vCenter na potrzeby zbierania danych o wydajności. Z urządzenia możesz w dowolnym momencie zatrzymać ciągłe profilowanie.

    > [!NOTE]
    > Funkcjonalność ciągłego odnajdywania jest dostępna w wersji zapoznawczej.

3. W **skopiuj poświadczenia projektu**, skopiuj identyfikator i klucz projektu. Będą potrzebne do skonfigurowania modułu zbierającego.


#### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Sprawdź, czy plik OVA jest bezpieczne, przed przystąpieniem do wdrażania:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Upewnij się, że wygenerowanego skrótu odpowiada następujące ustawienia.

#### <a name="one-time-discovery"></a>Jednorazowe odnajdywanie

Ova w wersji 1.0.9.15 (wydania 10/23/2018 r.)

**Algorytm** | **wartość skrótu**
>>>>>>> 20dc93529e7c0a4d17f2f4524752b5e2bead4e37---| ---MD5 | Algorytm SHA1 e9ef16b0c837638c506b5fc0ef75ebfa | 37b4b1e92b3c6ac2782ff5258450df6686c89864 SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

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

#### <a name="continuous-discovery"></a>Ciągłe odnajdywanie

Do OVA w wersji 1.0.10.4

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

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

### <a name="set-the-vcenter-statistics-level"></a>Ustaw poziom statystyki programu vCenter

Urządzenie modułu zbierającego umożliwia odnalezienie następujących statyczne metadane dotyczące wybranych maszyn wirtualnych.

1. Nazwa wyświetlana maszyny Wirtualnej (na klawiaturze vCenter)
2. Ścieżka magazynu maszyny Wirtualnej (w programie vCenter hosta/folder)
3. Adres IP
4. Adres MAC
5. System operacyjny
5. Liczba rdzeni, dysków, kart sieciowych
6. Rozmiar pamięci, rozmiary dysków
7. Liczniki wydajności i maszyny Wirtualnej, dysku i sieci, zgodnie z opisem w poniższej tabeli.

Jednorazowe odnajdywania w poniższej tabeli wymieniono liczniki wydajności dokładnie, które są zbierane i zawiera również listę wyników oceny, które ma wpływ, jeśli nie ma określonego licznika.

Ciągłe odnajdywania tych samych liczników są zbierane w czasie rzeczywistym (interwał 20 sekund), więc nie ma żadnych zależności na poziom statystyki programu vCenter. Urządzenie następnie ustala telefoniczny przykłady 20 sekund do utworzenia pojedynczego punktu danych co 15 minut, wybierając wartość szczytowa z próbek 20 sekund i wysyła je do platformy Azure.

|Licznik                                  |Poziom    |Poziom na urządzenie  |Ocena wpływu                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |Nie dotyczy                |Zalecany rozmiar maszyny Wirtualnej i kosztów                    |
|mem.usage.average                        | 1       |Nie dotyczy                |Zalecany rozmiar maszyny Wirtualnej i kosztów                    |
|virtualDisk.read.average                 | 2       |2                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|virtualDisk.write.average                | 2       |2                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej         |
|NET.RECEIVED.AVERAGE                     | 2       |3                 |Koszt rozmiar i sieć maszyny Wirtualnej                        |
|net.transmitted.average                  | 2       |3                 |Koszt rozmiar i sieć maszyny Wirtualnej                        |

> [!WARNING]
> Jednorazowe odnajdywania jeśli zostały ustawione po prostu wyższy poziom statystyk, potrwa na dzień do generowania liczników wydajności. Dlatego zaleca się, uruchom odnajdywanie po jednym dniu. Poczekaj co najmniej jednego dnia po uruchomieniu odnajdywania dla urządzenia na potrzeby profilowania środowiska i następnie utworzyć oceny dla modelu ciągłego odnajdywania.

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

W przypadku jednorazowego odnajdywania czas odnajdywania zależy od liczby odnajdywanych maszyn wirtualnych. Zazwyczaj do 100 maszyn wirtualnych, po uruchomieniu modułu zbierającego wynosi około godziny do zbierania danych konfiguracji i wydajności zakończyć. Możesz utworzyć oceny (zarówno oparte na wydajności, jak i oceny lokalne) natychmiast po zakończeniu odnajdywania.

W przypadku ciągłego odnajdywania (w wersji zapoznawczej) moduł zbierający będzie stale profilował środowisko lokalne i będzie nadal wysyłał dane dotyczące wydajności w godzinnych odstępach. Możesz przejrzeć maszyny w portalu po godzinie od rozpoczęcia odnajdywania. Zdecydowanie zalecamy poczekać co najmniej dzień przed utworzeniem jakichkolwiek ocen na podstawie wydajności dla maszyn wirtualnych.

1. W projekcie migracji kliknij pozycję **Zarządzaj** > **Maszyny**.
2. Sprawdź, czy maszyny wirtualne, które miały zostać odnalezione, są widoczne w portalu.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Utwórz grupę](how-to-create-a-group.md) dla oceny.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
