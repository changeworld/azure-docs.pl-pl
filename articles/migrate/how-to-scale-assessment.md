---
title: Skalowanie odnajdywania i oceny za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Opisuje sposób oceny dużej liczby maszyn lokalnych przy użyciu usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/25/2018
ms.author: raynew
ms.openlocfilehash: 553e0d6569a3a35802703ef58e1460878dcd930b
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127518"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Odnajdź i oceń duże środowisko programu VMware

Usługa Azure Migrate ma limit 1500 maszyn według projektu, w tym artykule opisano sposób oceny dużą liczbę lokalnych maszyn wirtualnych (VM) przy użyciu [usługi Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Wymagania wstępne

- **VMware**: maszyn wirtualnych, które mają być poddane migracji, musi być zarządzane przez program vCenter Server w wersji 5.5, 6.0 lub 6.5. Ponadto należy jeden host ESXi z wersją 5.0 lub nowszej, aby wdrożyć maszyny Wirtualnej modułu zbierającego.
- **Konto vCenter**: musisz mieć konto tylko do odczytu do dostępu do serwera vCenter. To konto jest używane w usłudze Azure Migrate do odnajdowania lokalnych maszyn wirtualnych.
- **Uprawnienia**: W programie vCenter Server, są wymagane uprawnienia do tworzenia maszyny Wirtualnej, importując plik w formacie OVA.
- **Ustawienia statystyk**: ustawieniach statystyk programu vCenter Server powinien być ustawiony na poziom 3, przed rozpoczęciem wdrażania. Poziom statystyki jest należy ustawić na 3 dla każdego dnia, tygodnia i interwału zbierania miesiąca. Jeśli poziom jest niższy niż 3 dla każdego interwału zbierania trzy, ocena będzie działać, ale nie będą zbierane dane wydajności dotyczące magazynu i sieci. Zalecenia dotyczące rozmiaru, następnie będzie zależeć od dane wydajności dotyczące procesora CPU i pamięci, a dane konfiguracji dla dysku i kart sieciowych.


### <a name="set-up-permissions"></a>Ustawianie uprawnień

Usługa Azure Migrate wymaga dostępu do serwerów VMware w celu automatycznego wykrywania maszyn wirtualnych do oceny. Konto VMware wymaga następujących uprawnień:

- Typ użytkownika: co najmniej użytkownik tylko do odczytu
- Uprawnienia: obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu
- Szczegóły: użytkownik przypisany na poziomie centrum danych z dostępem do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, przypisz rolę Bez dostępu z propagacją do obiektu podrzędnego do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

Jeśli jest wdrażane w środowisku dzierżawy, w tym miejscu jest jednym ze sposobów konfigurowania tego:

1.  Tworzenie użytkownika na dzierżawę i przy użyciu [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), przypisać uprawnienia tylko do odczytu do wszystkich maszyn wirtualnych należących do określonej dzierżawy. Następnie należy użyć tych poświadczeń do odnajdywania. RBAC gwarantuje, że odpowiednie użytkownik vCenter będzie miał dostęp do dzierżawy tylko określonej maszyny Wirtualnej.
2. Konfigurowania funkcji RBAC dla użytkowników innej dzierżawy zgodnie z opisem w poniższym przykładzie dla użytkownika nr 1 i 2 użytkownika:

    - W **nazwa_użytkownika** i **hasło**, określ poświadczenia konta tylko do odczytu, które moduł zbierający ma użyć do odnalezienia maszyn wirtualnych w
    - Datacenter1 - przyznać uprawnienia tylko do odczytu do 1 użytkownika i użytkownika nr 2. Nie propagować te uprawnienia do wszystkich obiektów podrzędnych, ponieważ uprawnienia zostanie ustawiona na poszczególnych maszyn wirtualnych.

      - Maszyna VM1 (dzierżawy #1) (tylko uprawnienia do odczytu użytkownika nr 1)
      - VM2 (dzierżawy #1) (tylko uprawnienia do odczytu użytkownika nr 1)
      - Maszyna VM3 (dzierżawy nr 2) (tylko uprawnienia do odczytu użytkownika nr 2)
      - VM4 (dzierżawy nr 2) (tylko uprawnienia do odczytu użytkownika nr 2)

   - Jeśli przeprowadzisz odnajdywanie przy użyciu poświadczeń użytkownika nr 1, następnie tylko maszyna VM1 i VM2 zostaną odnalezione.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planowanie migracji projektów i odnajdywania

Jeden moduł zbierający usługi Azure Migrate obsługuje odnajdywania z wielu serwerów programu vCenter, (jeden po drugim) i umożliwia wykrywanie do wielu projektów migracji (jeden po drugim). Moduł zbierający działa w pożar i zapomnij modelu, po zakończeniu odnajdowania, można użyć tego samego modułu zbierającego do zbierania danych z innego serwera vCenter lub wysyłać różne migrację.

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

Jeśli masz wiele serwery vCenter w danym środowisku, a łączna liczba maszyn wirtualnych jest mniejszy niż 1500, jeden moduł zbierający i projekt migracji można użyć do odnajdywania wszystkich maszyn wirtualnych na wszystkich serwerach vCenter. Ponieważ moduł zbierający odnajdzie jednego serwera vCenter w danym momencie, możesz uruchamiać tego samego modułu zbierającego wszystkie serwery vCenter, po kolei i punktu modułu zbierającego, w tym samym projekcie migracji. Po zakończeniu wszystkich odnalezionych danych dotyczących następnie można utworzyć oceny dla maszyn.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>VCenter wiele serwerów z więcej niż 1500 maszyn wirtualnych

Jeśli masz wiele serwery vCenter z mniej niż 1500 maszyn wirtualnych na serwer vCenter, ale więcej niż 1500 maszyn wirtualnych na wszystkich serwerach vCenter, konieczne jest utworzenie kilku projektów migracji (jeden projekt migracji może zawierać tylko 1500 maszyn wirtualnych). Można to osiągnąć, tworząc projekt migracji na serwer vCenter i dzielenia odnajdywania. Jeden moduł zbierający służy do odnajdywania każdego serwera vCenter, (jeden po drugim). Jeśli chcesz, aby odnajdywania można uruchomić w tym samym czasie, możesz również wdrażanie wielu urządzeń i równoległego uruchamiania odnajdywania.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Więcej niż 1500 maszyn w jednym programie vCenter Server

Jeśli masz więcej niż 1500 maszyn wirtualnych w jednym programie vCenter Server, należy podzielić odnajdywania na wielu projektów migracji. Aby podzielić odnajdywania, można wykorzystać pola zakresu w urządzeniu i określić hosta, klaster, folder lub centrum danych, które chcesz odnajdywać. Na przykład, jeśli masz dwa foldery w programie vCenter Server, jednym z 1000 maszyn wirtualnych (Folder1), a drugie z 800 maszyn wirtualnych (Folder2), możesz użyć jeden moduł zbierający i wykonania dwóch operacji odnajdywania. W przypadku pierwszego odnajdywania można określić Folder1 jako zakres i wskazać na pierwszy projekt migracji po zakończeniu pierwszego odnajdywania, możesz używać tego samego modułu zbierającego, zmień jego zakres, Folder2 i migracji szczegółów projektu do drugiego projektu migracji i do drugiego odnajdywania.

### <a name="multi-tenant-environment"></a>Środowiska z wieloma dzierżawami

Jeśli masz środowisko, który jest współużytkowany przez dzierżawców i nie chcesz odnajdywanie maszyn wirtualnych z jednej dzierżawy w innej dzierżawie subskrypcji, można użyć pola zakresu w urządzenia modułu zbierającego do określania zakresu odnajdywania. Jeśli dzierżawy współużytkują hostów, Utwórz poświadczenie, które ma dostęp tylko do odczytu do tylko maszyny wirtualne należące do określonej dzierżawy, a następnie korzystać z poświadczenia urządzenia modułu zbierającego i określ zakres jako hosta w celu odnajdywania. Alternatywnie można także utworzyć foldery, w programie vCenter Server (Załóżmy, że folder1 dla tenant1 folder2 dla tenant2), w obszarze udostępnionego hosta, Przenieś maszyny wirtualne dla tenant1 do folder1 i tenant2 do folder2 i zakres odnajdywania w module zbierającym w związku z tym określając odpowiedni folder.

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

1. W projekcie usługi Azure Migrate wybierz **wprowadzenie** > **Odnajdź i oceń** > **odnajdź maszyny**.
2. W **odnajdź maszyny**, wybierz opcję **Pobierz**, aby pobrać plik OVA.
3. W **skopiuj poświadczenia projektu**, skopiuj identyfikator i klucz projektu. Będą potrzebne do skonfigurowania modułu zbierającego.


#### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Sprawdź, czy plik OVA jest bezpieczne, przed przystąpieniem do wdrażania:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Upewnij się, że wygenerowanego skrótu odpowiada następujące ustawienia.

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

    OVA w wersji 1.0.9.5

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    OVA w wersji 1.0.9.2

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

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
Poniżej przedstawiono listę liczników wydajności, które są zbierane podczas odnajdywania. Te liczniki są domyślnie dostępne na różnych poziomach w programie vCenter Server.

Zalecane ustawienie najwyższy poziom wspólnych (3) do poziomu statystyk, tak, aby wszystkie liczniki są poprawnie zbierane. Jeśli masz ustawiony na niższym poziomie vCenter, tylko kilka liczniki mogą być zbierane w całkowicie, z użyciem usług rest ustawione na 0. Ocena może następnie wyświetlać niepełne dane.

W poniższej tabeli wymieniono także wyniki oceny, które zostaną zmienione, jeśli nie ma określonego licznika.

| Licznik                                 | Poziom | Poziom na urządzenie | Ocena wpływu                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| cpu.usage.average                       | 1     | Nie dotyczy               | Zalecany rozmiar maszyny Wirtualnej i kosztów         |
| mem.usage.average                       | 1     | Nie dotyczy               | Zalecany rozmiar maszyny Wirtualnej i kosztów         |
| virtualDisk.read.average                | 2     | 2                | Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej |
| virtualDisk.write.average               | 2     | 2                | Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Rozmiar dysku, koszt magazynu i rozmiaru maszyny Wirtualnej |
| NET.RECEIVED.AVERAGE                    | 2     | 3                | Koszt rozmiar i sieć maszyny Wirtualnej             |
| net.transmitted.average                 | 2     | 3                | Koszt rozmiar i sieć maszyny Wirtualnej             |

> [!WARNING]
> Jeśli właśnie zostały ustawione na wyższy poziom statystyk, potrwa na dzień wygenerowania liczników wydajności. Dlatego zaleca się, uruchom odnajdywanie po jednym dniu.

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

Czas odnajdowania zależy od liczby odnajdowanych maszyn wirtualnych. Zazwyczaj do 100 maszyn wirtualnych, odnajdywanie zakończy się około godziny po zakończeniu modułu zbierającego.

1. W projekcie planowania migracji należy wybrać **Zarządzaj** > **maszyn**.
2. Sprawdź, czy maszyny wirtualne, które miały zostać odnalezione, są widoczne w portalu.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Utwórz grupę](how-to-create-a-group.md) dla oceny.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
