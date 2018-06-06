---
title: Skalowanie odnajdywania i ocena za pomocą migracji Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób oceny dużej liczby komputerów lokalnych za pomocą usługi Azure migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: 89c9cfd4bdc1c483764983c886ba9f96cc75c69e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736834"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Odnajdź i oceń duże środowisko programu VMware

Azure migracji ma limit 1500 maszyn według projektu, w tym artykule opisano sposób oceny dużej liczby lokalnych maszyn wirtualnych (VM) przy użyciu [migracji Azure](migrate-overview.md).   

## <a name="prerequisites"></a>Wymagania wstępne

- **VMware**: maszyny wirtualne, które mają zostać poddane migracji muszą być zarządzane przez vCenter Server 5.5, 6.0 lub 6.5. Ponadto należy jeden ESXi hosta wersji 5.0 lub nowszej w celu wdrożenia modułu zbierającego maszyny Wirtualnej.
- **Konto vCenter**: konieczne konto tylko do odczytu, aby uzyskać dostęp serwer vCenter. To konto jest używane w usłudze Azure Migrate do odnajdowania lokalnych maszyn wirtualnych.
- **Uprawnienia**: W programie vCenter Server, musisz mieć uprawnienia do tworzenia maszyny Wirtualnej przez zaimportowanie pliku w formacie komórek jajowych.
- **Ustawienia statystyki**: ustawienia statystyki dla serwera vCenter powinien być ustawiony na poziomie 3, przed rozpoczęciem wdrażania. Jeśli poziom jest niższy niż 3, oceny będą działać, ale nie można zebrać danych wydajności magazynu i sieci. Zalecenia dotyczące rozmiaru w takim przypadku będzie opierać się na dane wydajności dotyczące Procesora i pamięci, a dane konfiguracji kart dysku i sieci.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planowanie migracji projektów i odnajdywania

Jeden moduł zbierający migracji Azure obsługuje odnajdywania z vCenter wielu serwerów (jeden po drugim), a także odnajdywania do wielu projektów migracji (jeden po drugim). Moduł zbierający działa w fire i zapomnij modelu, po zakończeniu odnajdywania można użyć tego samego modułu zbierającego do zbierania danych z innego serwera vCenter lub wysłanie ich do różnych migrację.

Zaplanuj odnajdywania i oceny na podstawie limitów następujące:

| **Jednostki** | **Limit komputera** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Odnajdowanie  | 1,500             |
| Ocena | 1,500             |

Należy pamiętać, te zagadnienia dotyczące planowania:

- Po wykonaniu odnajdywania przy użyciu modułu zbierającego Azure migracji można ustawić zakresu odnajdowania do folderu na serwerze vCenter, datacenter, klastra lub hosta.
- Aby zrobić więcej niż jedne operacje odnajdywania, sprawdź, czy serwer, który ma zostać przeprowadzone odnajdywanie maszyn wirtualnych znajdują się w foldery, centrów danych, klastrów i hostów, które obsługują ograniczenie 1500 maszyny vCenter.
- Firma Microsoft zaleca do celów oceny maszyn z zależnościami, w tym samym projekcie i oceny. W programie vCenter Server upewnij się, że zależnych maszyny są w tym samym folderze, datacenter lub klastra na potrzeby oceny.

W zależności od scenariusza można podzielić odnajdywania sieci, jak opisano poniżej:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>VCenter wiele serwerów z mniej niż 1500 maszyny wirtualne

Jeśli masz vCenter wielu serwerów w środowisku, a liczba maszyn wirtualnych jest mniejsza niż 1500, jeden moduł zbierający i projektu migracji można użyć do odnajdywania wszystkich maszyn wirtualnych na wszystkich serwerach vCenter. Ponieważ moduł zbierający odnajduje jeden serwer vCenter w czasie, można uruchomić tego samego modułu zbierającego przed wszystkie serwery vCenter, jeden po drugim i wskaż modułu zbierającego w tym samym projekcie migracji. Po zakończeniu operacji odnajdywania może następnie utworzyć ocen dla maszyn.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>VCenter wiele serwerów z ponad 1500 maszyny wirtualne

Jeśli masz wiele serwery vCenter z mniej niż 1500 maszyn wirtualnych dla serwera vCenter, ale ponad 1500 maszyn wirtualnych we wszystkich służy vCenter, należy utworzyć wiele projektów migracji (jeden projekt migracji może zawierać tylko 1500 maszyn wirtualnych). Można to osiągnąć, tworząc projekt migracji na serwer vCenter i dzielenia odnajdywania. Jeden moduł zbierający służy do odnajdywania każdego serwera vCenter (jeden po drugim). Jeśli chcesz odnajdywania można uruchomić w tym samym czasie, mogą także wdrażać wiele urządzeń oraz równolegle odnajdywania.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Ponad 1500 maszyny w jednym programie vCenter Server

Jeśli masz ponad 1500 maszyny wirtualne w jednym programie vCenter Server, należy podzielić odnajdywania na wielu projektów migracji. Aby podzielić odnajdywania, można wykorzystać pole zakresu w urządzeniu i określić hostów, klastra, folder lub centrum danych, które chcesz odnajdywać. Na przykład, jeśli występują dwa foldery w programie vCenter Server, z 1000 maszyn wirtualnych (Folder1), a druga z 800 maszyn wirtualnych (Folder2), można użyć jeden moduł zbierający i wykonania dwóch odnajdywania. W przypadku pierwszego odnajdywania można określić Folder1 jako zakres i wskaż na nim pierwszy projekt migracji, po zakończeniu pierwszego odnajdywania można użyć tego samego modułu zbierającego, zmień jej zakres Folder2 i migracji szczegółami projektu do drugiego migrację i czy drugi odnajdywania.

### <a name="multi-tenant-environment"></a>Środowiska z wieloma dzierżawami

Jeśli masz środowisko, który jest współużytkowany przez dzierżawców i nie chcesz odnajdywać maszyn wirtualnych z jednego dzierżawcy w subskrypcji innej dzierżawy, można użyć pola zakresu w urządzeniu modułu zbierającego do określania zakresu odnajdywania. Dzierżawcy udostępniania hostów, Utwórz poświadczeniami, które mają dostęp tylko do odczytu, aby tylko maszyny wirtualne należące do określonej dzierżawy, a następnie użyć tych poświadczeń w urządzeniu moduł zbierający i określ zakres jako hosta w celu odnajdywania. Alternatywnie można także utworzyć foldery w programie vCenter Server (Załóżmy, że folder1 dla tenant1 folder2 dla tenant2), w obszarze udostępnionego hosta przenoszenia maszyn wirtualnych dla tenant1 do folder1 i tenant2 do folder2 i odpowiednio określić zakres odnajdywania w moduł zbierający określając odpowiedni folder.

## <a name="discover-on-premises-environment"></a>Odnajdywanie w środowisku lokalnym

Po zakończeniu planu można następnie uruchom odnajdywanie maszyn wirtualnych lokalnie:

### <a name="create-a-project"></a>Tworzenie projektu

Tworzenie projektu migracji Azure zgodnie z wymaganiami:

1. W portalu Azure wybierz **Utwórz zasób**.
2. Wyszukaj ciąg **Azure Migrate** i w wynikach wyszukiwania wybierz usługę **Azure Migrate (wersja zapoznawcza)**. Następnie wybierz przycisk **Utwórz**.
3. Określ nazwę projektu i subskrypcji platformy Azure dla projektu.
4. Utwórz nową grupę zasobów.
5. Określ lokalizację, w której chcesz utworzyć projekt, a następnie wybierz **Utwórz**. Należy pamiętać, że nadal można ocenić maszyn wirtualnych na inną lokalizację docelową. Lokalizacja określona dla projektu jest używany do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.

### <a name="set-up-the-collector-appliance"></a>Konfigurowanie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę wirtualną, nazywaną modułem zbierającym. Tej maszyny Wirtualnej umożliwia odnalezienie lokalnych maszyn wirtualnych VMware, a następnie wysyła metadane dotyczące ich do usługi Azure migracji. Aby skonfigurować urządzenia modułu zbierającego, Pobierz plik komórek jajowych i zaimportować go do lokalnego wystąpienia serwera vCenter.

#### <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Jeśli masz wiele projektów, należy pobrać urządzenia modułu zbierającego tylko raz do serwera vCenter. Po pobraniu i konfigurowanie urządzenia zostanie uruchomiony dla każdego projektu, a następnie określ projektu Unikatowy identyfikator i klucz.

1. W projekcie migracji Azure wybierz **wprowadzenie** > **odnajdź & oceny** > **odnajdywanie maszyn**.
2. W **Odkryj maszyny**, wybierz pozycję **Pobierz**, aby pobrać plik komórek jajowych.
3. W **skopiuj poświadczenia projektu**, skopiować identyfikator i klucz dla projektu. Będą potrzebne do skonfigurowania modułu zbierającego.


#### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Sprawdź, czy plik komórek jajowych jest bezpieczne, przed przystąpieniem do wdrażania:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Upewnij się, czy wygenerowane skrótu zgodna następujące ustawienia.

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

Pobrany plik należy zaimportować do programu vCenter Server:

1. W konsoli klienta vSphere wybierz **pliku** > **wdrażanie szablonu OVF**.

    ![Wdrażanie pliku OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. W Kreatorze wdrażania szablonu OVF > **źródła**, określ lokalizację pliku komórek jajowych.
3. W polach **Name** (Nazwa) i **Location** (Lokalizacja) wprowadź przyjazną nazwę maszyny wirtualnej modułu zbierającego oraz obiekt magazynu, w którym będzie hostowana maszyna wirtualna.
4. W polu **Host/Cluster** (Host/klaster) określ host lub klaster, na którym będzie działać maszyna wirtualna modułu zbierającego.
5. W obszarze Magazyn określ docelowy magazyn dla maszyny wirtualnej modułu zbierającego.
6. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
7. W obszarze **Network Mapping** (Mapowanie sieci) określ sieć, z którą będzie się łączyć maszyna wirtualna modułu zbierającego. Sieci wymaga łączności z Internetem można wysłać metadanych na platformie Azure.
8. Przejrzyj i Potwierdź ustawienia, a następnie wybierz **Zakończ**.

### <a name="identify-the-id-and-key-for-each-project"></a>Określ identyfikator i klucz dla każdego projektu

Jeśli masz wiele projektów, pamiętaj zidentyfikować identyfikator i klucz dla każdej z nich. Należy klucza podczas uruchamiania modułu zbierającego, aby odnaleźć maszyn wirtualnych.

1. W projekcie, wybierz **wprowadzenie** > **odnajdź & oceny** > **odnajdywanie maszyn**.
2. W **skopiuj poświadczenia projektu**, skopiować identyfikator i klucz dla projektu.
    ![Skopiuj poświadczenia projektu](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Ustaw poziom statystyki vCenter
Poniżej znajduje się lista liczników wydajności, które zostały zebrane podczas odnajdywania. Te liczniki są domyślnie dostępne na różnych poziomach w programie vCenter Server.

Zalecane ustawienie najwyższego poziomu wspólnych (3) dla poziomu statystyk, tak aby wszystkie liczniki są poprawnie zbierane. Jeśli masz vCenter ustawiony na niższym poziomie tylko kilka liczniki mogą być zbierane całkowicie z resztą równa 0. Ocena następnie mogą być wyświetlane niekompletne dane.

W poniższej tabeli wymieniono także wyniki oceny, które zostaną zmienione, jeśli nie ma określonego licznika.

| Licznik                                 | Poziom | Poziom na urządzenie | Ocena wpływu                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| cpu.usage.average                       | 1     | Nie dotyczy               | Zalecany rozmiar maszyny Wirtualnej i kosztów         |
| mem.usage.average                       | 1     | Nie dotyczy               | Zalecany rozmiar maszyny Wirtualnej i kosztów         |
| virtualDisk.read.average                | 2     | 2                | Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej |
| virtualDisk.write.average               | 2     | 2                | Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Rozmiar dysku, kosztem magazynowania i rozmiar maszyny Wirtualnej |
| NET.RECEIVED.AVERAGE                    | 2     | 3                | Koszt rozmiar i sieci maszyny Wirtualnej             |
| net.transmitted.average                 | 2     | 3                | Koszt rozmiar i sieci maszyny Wirtualnej             |

> [!WARNING]
> Jeśli właśnie ustawione na wyższy poziom statystyki potrwa na dzień do generowania liczników wydajności. Tak firma Microsoft zaleca uruchomienia odnajdywania po jednym dniu.

### <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

Dla każdego odnajdowania, które należy wykonać możesz uruchomić moduł zbierający do odnajdywanie maszyn wirtualnych w zakresie wymaganych. Uruchom odnajdywanie jeden po drugim. Równoczesne operacje odnajdywania nie są obsługiwane, a poszczególne zadania odnajdywania musi mieć inny zakres.

1.  W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2.  Określ preferencje dotyczące języka, strefy czasowej i hasła dla urządzenia.
3.  Na pulpicie, wybierz **uruchom moduł zbierający** skrótów.
4.  Moduł zbierający migracji Azure Otwórz **konfigurowanie wymagań wstępnych** , a następnie:

    a. Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.

    Moduł zbierający sprawdzi, czy maszyna wirtualna ma dostęp do Internetu.

    b. Jeśli maszyna wirtualna uzyskuje dostęp do Internetu za pośrednictwem serwera proxy, zaznacz **ustawienia serwera Proxy**i określić adres serwera proxy i port nasłuchiwania. Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.

    Moduł zbierający sprawdzi, czy usługa modułu zbierającego jest uruchomiona. Jest ona instalowana domyślnie na maszynie wirtualnej modułu zbierającego.

    c. Pobierz i zainstaluj program VMware PowerCLI.

5.  W obszarze **Specify vCenter Server details** (Określ szczegóły serwera vCenter) wykonaj następujące czynności:
    - Określ nazwę (FQDN) lub adres IP serwera vCenter.
    - W **nazwy użytkownika** i **hasło**, określ poświadczenia konta tylko do odczytu, które moduł zbierający będzie używane do wykrywania maszyn wirtualnych w programie vCenter Server.
    - W obszarze **Select scope** (Zakres wybierania) wybierz zakres odnajdowania maszyn wirtualnych. Moduł zbierający może odnajdywać tylko maszyny wirtualne w podanym zakresie. Zakresem może być określony folder, centrum danych albo klaster. Nie powinien on zawierać więcej niż 1000 maszyn wirtualnych.

6.  W **Określ migrację**, podaj identyfikator i klucz dla projektu. Jeśli nie zostaną skopiowane, otwórz Azure portal z modułu zbierającego maszyny Wirtualnej. Do projektu **omówienie** wybierz pozycję **odnajdywanie maszyn** i skopiuj wartości.  
7.  W **wyświetlić postęp kolekcji**, monitorować proces odnajdywania i sprawdź, że metadane zbierane z maszyn wirtualnych znajduje się w zakresie. Moduł zbierający informuje o szacowanym czasie odnajdowania.


#### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Czas odnajdowania zależy od liczby odnajdowanych maszyn wirtualnych. Zazwyczaj 100 maszyn wirtualnych, odnajdywanie zakończy się wokół godzinę po zakończeniu modułu zbierającego.

1. W projekcie planowania migracji, zaznacz **Zarządzaj** > **maszyny**.
2. Sprawdź, czy maszyny wirtualne, które miały zostać odnalezione, są widoczne w portalu.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Utwórz grupę](how-to-create-a-group.md) do oceny.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
