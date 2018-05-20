---
title: Skalowanie odnajdywania i ocena za pomocą migracji Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób oceny dużej liczby komputerów lokalnych za pomocą usługi Azure migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: e0bd62710c47cfdf81535470ef96bad2ab675bb0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Odnajdź i oceń duże środowisko programu VMware

W tym artykule opisano sposób oceny dużej liczby lokalnych maszyn wirtualnych (VM) przy użyciu [migracji Azure](migrate-overview.md). Azure migracji ocenia maszyny, aby sprawdzić, czy są one odpowiednie dla migracji na platformie Azure. Usługa zawiera kosztów i zmiany rozmiaru uzyskać szacunkowe wartości do uruchamiania na komputerach w systemie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- **VMware**: maszyny wirtualne, które mają zostać poddane migracji muszą być zarządzane przez vCenter Server 5.5, 6.0 lub 6.5. Ponadto należy jeden ESXi hosta wersji 5.0 lub nowszej w celu wdrożenia modułu zbierającego maszyny Wirtualnej.
- **Konto vCenter**: konieczne konto tylko do odczytu, aby uzyskać dostęp serwer vCenter. To konto jest używane w usłudze Azure Migrate do odnajdowania lokalnych maszyn wirtualnych.
- **Uprawnienia**: W programie vCenter Server, musisz mieć uprawnienia do tworzenia maszyny Wirtualnej przez zaimportowanie pliku w formacie komórek jajowych.
- **Ustawienia statystyki**: ustawienia statystyki dla serwera vCenter powinien być ustawiony na poziomie 3, przed rozpoczęciem wdrażania. Jeśli poziom jest niższy niż 3, oceny będą działać, ale nie można zebrać danych wydajności magazynu i sieci. Zalecenia dotyczące rozmiaru w takim przypadku będzie opierać się na dane wydajności dotyczące Procesora i pamięci, a dane konfiguracji kart dysku i sieci.

## <a name="plan-azure-migrate-projects"></a>Planowanie migracji Azure projektów

Zaplanuj odnajdywania i oceny na podstawie limitów następujące:

| **Jednostki** | **Limit komputera** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Odnajdowanie  | 1,500             |
| Ocena | 1,500             |

<!-- 
- If you have fewer than 400 machines to discover and assess, you need a single project and a single discovery. Depending on your requirements, you can either assess all the machines in a single assessment or split the machines into multiple assessments. 
- If you have 400 to 1,000 machines to discover, you need a single project with a single discovery. But you will need multiple assessments to assess these machines, because a single assessment can hold up to 400 machines.
- If you have 1,001 to 1,500 machines, you need a single project with two discoveries in it.
- If you have more than 1,500 machines, you need to create multiple projects, and perform multiple discoveries, according to your requirements. For example:
    - If you have 3,000 machines, you can set up two projects with two discoveries, or three projects with a single discovery.
    - If you have 5,000 machines, you can set up four projects: three with a discovery of 1,500 machines, and one with a discovery of 500 machines. Alternatively, you can set up five projects with a single discovery in each one. 
      -->

## <a name="plan-multiple-discoveries"></a>Planowanie wielu odnajdywania

Można użyć tego samego modułu zbierającego migracji Azure celu odkrycia wiele do jednego lub więcej projektów. Należy pamiętać, te zagadnienia dotyczące planowania:

- Po wykonaniu odnajdywania przy użyciu modułu zbierającego Azure migracji można ustawić zakresu odnajdowania do folderu na serwerze vCenter, datacenter, klastra lub hosta.
- Aby zrobić więcej niż jedne operacje odnajdywania, sprawdź, czy serwer, który ma zostać przeprowadzone odnajdywanie maszyn wirtualnych znajdują się w foldery, centrów danych, klastrów i hostów, które obsługują ograniczenie 1500 maszyny vCenter.
- Firma Microsoft zaleca do celów oceny maszyn z zależnościami, w tym samym projekcie i oceny. W programie vCenter Server upewnij się, że zależnych maszyny są w tym samym folderze, datacenter lub klastra na potrzeby oceny.


## <a name="create-a-project"></a>Tworzenie projektu

Tworzenie projektu migracji Azure zgodnie z wymaganiami:

1. W portalu Azure wybierz **Utwórz zasób**.
2. Wyszukaj ciąg **Azure Migrate** i w wynikach wyszukiwania wybierz usługę **Azure Migrate (wersja zapoznawcza)**. Następnie wybierz przycisk **Utwórz**.
3. Określ nazwę projektu i subskrypcji platformy Azure dla projektu.
4. Utwórz nową grupę zasobów.
5. Określ lokalizację, w której chcesz utworzyć projekt, a następnie wybierz **Utwórz**. Należy pamiętać, że nadal można ocenić maszyn wirtualnych na inną lokalizację docelową. Lokalizacja określona dla projektu jest używany do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.

## <a name="set-up-the-collector-appliance"></a>Konfigurowanie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę wirtualną, nazywaną modułem zbierającym. Tej maszyny Wirtualnej umożliwia odnalezienie lokalnych maszyn wirtualnych VMware, a następnie wysyła metadane dotyczące ich do usługi Azure migracji. Aby skonfigurować urządzenia modułu zbierającego, Pobierz plik komórek jajowych i zaimportować go do lokalnego wystąpienia serwera vCenter.

### <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Jeśli masz wiele projektów, należy pobrać urządzenia modułu zbierającego tylko raz do serwera vCenter. Po pobraniu i konfigurowanie urządzenia zostanie uruchomiony dla każdego projektu, a następnie określ projektu Unikatowy identyfikator i klucz.

1. W projekcie migracji Azure wybierz **wprowadzenie** > **odnajdź & oceny** > **odnajdywanie maszyn**.
2. W **Odkryj maszyny**, wybierz pozycję **Pobierz**, aby pobrać plik komórek jajowych.
3. W **skopiuj poświadczenia projektu**, skopiować identyfikator i klucz dla projektu. Będą potrzebne do skonfigurowania modułu zbierającego.


### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Sprawdź, czy plik komórek jajowych jest bezpieczne, przed przystąpieniem do wdrażania:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Upewnij się, czy wygenerowane skrótu zgodna następujące ustawienia.

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

    OVA w wersji 1.0.8.59

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    OVA w wersji 1.0.8.49

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    OVA w wersji 1.0.8.40:

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Tworzenie maszyny wirtualnej modułu zbierającego

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

## <a name="identify-the-id-and-key-for-each-project"></a>Określ identyfikator i klucz dla każdego projektu

Jeśli masz wiele projektów, pamiętaj zidentyfikować identyfikator i klucz dla każdej z nich. Należy klucza podczas uruchamiania modułu zbierającego, aby odnaleźć maszyn wirtualnych.

1. W projekcie, wybierz **wprowadzenie** > **odnajdź & oceny** > **odnajdywanie maszyn**.
2. W **skopiuj poświadczenia projektu**, skopiować identyfikator i klucz dla projektu. 
    ![Skopiuj poświadczenia projektu](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Ustaw poziom statystyki vCenter
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

## <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

Dla każdego odnajdowania, które należy wykonać możesz uruchomić moduł zbierający do odnajdywanie maszyn wirtualnych w zakresie wymaganych. Uruchom odnajdywanie jeden po drugim. Równoczesne operacje odnajdywania nie są obsługiwane, a poszczególne zadania odnajdywania musi mieć inny zakres.

1.  W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2.  Określ preferencje dotyczące języka, strefy czasowej i hasła dla urządzenia.
3.  Na pulpicie, wybierz **uruchom moduł zbierający** skrótów.
4.  Moduł zbierający migracji Azure Otwórz **konfigurowanie wymagań wstępnych** , a następnie:

    a. Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.

    Moduł zbierający sprawdzi, czy maszyna wirtualna ma dostęp do Internetu.

    b. Jeśli maszyna wirtualna uzyskuje dostęp do Internetu za pośrednictwem serwera proxy, zaznacz **ustawienia serwera Proxy**i określić adres serwera proxy i port nasłuchiwania. Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.

    Moduł zbierający sprawdzi, czy usługa modułu zbierającego jest uruchomiona. Jest ona instalowana domyślnie na maszynie wirtualnej modułu zbierającego.

    c. Pobierz i zainstaluj VMware PowerCLI.

5.  W obszarze **Specify vCenter Server details** (Określ szczegóły serwera vCenter) wykonaj następujące czynności:
    - Określ nazwę (FQDN) lub adres IP serwera vCenter.
    - W **nazwy użytkownika** i **hasło**, określ poświadczenia konta tylko do odczytu, które moduł zbierający będzie używane do wykrywania maszyn wirtualnych w programie vCenter Server.
    - W obszarze **Select scope** (Zakres wybierania) wybierz zakres odnajdowania maszyn wirtualnych. Moduł zbierający może odnajdywać tylko maszyny wirtualne w podanym zakresie. Zakresem może być określony folder, centrum danych albo klaster. Nie powinien on zawierać więcej niż 1000 maszyn wirtualnych. 

6.  W **Określ migrację**, podaj identyfikator i klucz dla projektu. Jeśli nie zostaną skopiowane, otwórz Azure portal z modułu zbierającego maszyny Wirtualnej. Do projektu **omówienie** wybierz pozycję **odnajdywanie maszyn** i skopiuj wartości.  
7.  W **wyświetlić postęp kolekcji**, monitorować proces odnajdywania i sprawdź, że metadane zbierane z maszyn wirtualnych znajduje się w zakresie. Moduł zbierający informuje o szacowanym czasie odnajdowania.


### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Czas odnajdowania zależy od liczby odnajdowanych maszyn wirtualnych. Zazwyczaj 100 maszyn wirtualnych, odnajdywanie zakończy się wokół godzinę po zakończeniu modułu zbierającego. 

1. W projekcie planowania migracji, zaznacz **Zarządzaj** > **maszyny**.
2. Sprawdź, czy maszyny wirtualne, które miały zostać odnalezione, są widoczne w portalu.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Utwórz grupę](how-to-create-a-group.md) do oceny.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
