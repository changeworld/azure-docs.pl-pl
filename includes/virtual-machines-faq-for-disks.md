---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e8005da056c08b21bf0b91dc71b3dafac281de1f
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40239022"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Często zadawane pytania dotyczące dysków maszyn wirtualnych IaaS platformy Azure i dyski zarządzane i niezarządzane — wersja premium

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące dysków Azure Managed Disks i usługi Azure Premium SSD.

## <a name="managed-disks"></a>Managed Disks

**Co to jest Azure Managed Disks?**

Managed Disks to funkcja, która upraszcza zarządzanie dyskami maszyn wirtualnych IaaS platformy Azure dzięki obsłudze Zarządzanie kontem magazynu za Ciebie. Aby uzyskać więcej informacji, zobacz [omówienie Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Czy jeśli utworzę standardowa dysku zarządzanego z istniejącego dysku VHD, który jest 80 GB będzie, kosztem, jaki mnie?**

Standardowa dysku zarządzanego utworzonego na podstawie 80 GB, wirtualny dysk twardy jest traktowany jako rozmiar dysku w warstwie standardowa dostępne następny dysk S10. Opłaty są naliczane zgodnie z cennikiem dysk S10. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czy istnieją wszystkie koszty transakcji dla dysków zarządzanych w warstwie standardowa?**

Tak. Opłaty są naliczane za każdą transakcję. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Dla standardowych dysków zarządzanych zostanie naliczona do rzeczywistego rozmiaru danych na dysku lub zaprowizowaną pojemnością dysku?**

Opłaty są naliczane na podstawie pojemności aprowizowanego dysku. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Jak jest ceny dysków zarządzanych w warstwie premium różni się od dysków niezarządzanych?**

Ceny dysków zarządzanych w warstwie premium jest taka sama jak dysków niezarządzanych w warstwie premium.

**Czy można zmienić typ (standardowa / Premium) konta magazynu z dysków zarządzanych?**

Tak. Możesz zmienić typ konta magazynu z dysków zarządzanych przy użyciu witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

**Do utworzenia dysku zarządzanego z innej subskrypcji można użyć pliku VHD na koncie usługi Azure storage?**

Tak.

**Do utworzenia dysku zarządzanego w różnych regionach można używać pliku VHD na koncie usługi Azure storage?**

Nie.

**Czy istnieją jakiekolwiek ograniczenia skalowania w przypadku klientów, którzy korzystają z dysków zarządzanych?**

Dyski zarządzane eliminuje limity skojarzone z kontami magazynu. Jednak maksymalny limit to 50 000 dyski zarządzane według regionu i typ dysku w ramach subskrypcji.

**Czy można wykonać przyrostową migawkę dysku zarządzanego?**

Nie. Bieżąca funkcja migawki sprawia, że pełna kopia dysku zarządzanego.

**Maszyny wirtualne w zestawie dostępności może zawierać kombinację dysków zarządzanych i niezarządzanych?**

Nie. Maszyn wirtualnych w zestawie dostępności muszą używać wszystkich dysków zarządzanych lub wszystkich dysków niezarządzanych. Podczas tworzenia zestawu dostępności, można wybrać typy dysków, w której chcesz użyć.

**Jest Managed Disks to opcja domyślna, w witrynie Azure portal?**

Tak.

**Można utworzyć pusty dysk zarządzany?**

Tak. Można utworzyć pusty dysk. Dysku zarządzanego można tworzyć niezależnie od maszyny Wirtualnej, na przykład, bez dołączania ich do maszyny Wirtualnej.

**Co to liczba domen błędów obsługiwanych dla dostępności ustawiono korzystającej z dysków zarządzanych?**

W zależności od regionu, w którym znajduje się zestaw dostępności, który korzysta z dysków zarządzanych liczba domen błędów obsługiwanych jest 2 lub 3.

**Jak są standardowe konto magazynu diagnostyki skonfigurować?**

Możesz skonfigurować konto prywatnego magazynu dla maszyny Wirtualnej diagnostyki.

**Jakiego rodzaju pomocy technicznej kontroli dostępu opartej na rolach jest dostępna dla dysków Managed Disks?**

Managed Disks obsługuje trzy kluczy domyślne role:

* Właściciel: Mogą zarządzać wszystkim łącznie z dostępem
* Współautor: Może zarządzać wszystkim oprócz dostępu
* Czytnik: Mogą przeglądać wszystko, ale nie może wprowadzać zmian

**Czy istnieje sposób, aby I kopiowanie lub eksportowanie dysku zarządzanego na koncie magazynu prywatnego?**

Można wygenerować sygnaturę dostępu współdzielonego tylko do odczytu (SAS) identyfikator URI dla dysku zarządzanego i go użyć do kopiowania zawartości do magazynu prywatnego magazynu lub konta System lokalny. Można użyć identyfikatora URI sygnatury dostępu Współdzielonego, za pomocą witryny Azure portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub [narzędzia AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Można utworzyć kopię dysku zarządzanego?**

Klienci mogą migawki dysków zarządzanych i następnie utworzyć inny dysk zarządzany przy użyciu migawki.

**Czy dysków niezarządzanych są nadal obsługiwane?**

Tak, dyski zarządzane i niezarządzane są obsługiwane. Zaleca się, że korzystają z dysków zarządzanych dla nowych obciążeń i migracji bieżących obciążeń do usługi managed disks.

**Jeśli I Utwórz dysk 128 GB, a dopiero potem zwiększyć jej rozmiar 130 GB, czy opłata dalej rozmiar dysku (256 GB)?**

Tak.

**Można utworzyć magazyn lokalnie nadmiarowy, Magazyn geograficznie nadmiarowy i Magazyn strefowo nadmiarowy dysków zarządzanych?**

Usługa Azure Managed Disks obsługuje obecnie tylko lokalnie nadmiarowy magazyn zarządzane dyski.

**Można zmniejszyć lub downsize Moje dyski zarządzane?**

Nie. Ta funkcja nie jest obecnie obsługiwana. 

**Czy można przerwać dzierżawy, na tym dysku?**

Nie. To nie jest obecnie obsługiwana dzierżawy jest obecna, aby zapobiec przypadkowemu usunięciu, gdy dysk jest używany.

**Można zmienić właściwości Nazwa komputera w przypadku wyspecjalizowanego (nie utworzone przy użyciu narzędzia przygotowywania systemu lub uogólniony) dysk systemu operacyjnego używany w celu obsługi administracyjnej maszyny Wirtualnej?**

Nie. Nie można zaktualizować właściwości Nazwa komputera. Nowa maszyna wirtualna dziedziczy z elementu nadrzędnego maszyny Wirtualnej, który został użyty do utworzenia dysku systemu operacyjnego. 

**Gdzie można znaleźć przykładowe szablony usługi Azure Resource Manager do tworzenia maszyn wirtualnych z dyskami zarządzanymi**
* [Lista szablonów przy użyciu dysków Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Wspólnie znaleźć dysków niezarządzanych i zarządzanych w tej samej maszyny Wirtualnej?**

Nie.

**Podczas tworzenia dysku z obiektu blob, jest dowolnym stale istniejącą relację, z tym źródłowego obiektu blob?**

Nie, gdy zostanie utworzony nowy dysk jest autonomiczny pełną kopię tego obiektu blob w tym czasie, nie ma połączenia między nimi. Jeśli chcesz po utworzeniu dysku źródłowego obiektu blob mogą zostać usunięte bez wywierania wpływu na nowo utworzonego dysku w dowolny sposób.

**Dysków zarządzanych lub niezarządzanych można zmienić po jego utworzeniu?**

Za dyski zarządzane nie można ich zmienić. Jednak tak długo, jak nie jest obecnie dołączony do maszyny Wirtualnej lub wirtualnego dysku twardego może zmienić nazwy dysku niezarządzanego.

## <a name="standard-ssd-disks-preview"></a>Dyski SSD w warstwie standardowa (wersja zapoznawcza)

**Co to są dyski SSD w warstwie standardowa usługi Azure?**
Dyski SSD w warstwie standardowa są wspierane przez nośnik SSD, zoptymalizowane pod kątem jako niskie koszty magazynowania dla obciążeń wymagających spójną wydajność na niższych poziomach operacji We/Wy dysków w warstwie standardowa. W wersji zapoznawczej są one dostępne w ograniczonej liczbie regionów, przy użyciu ograniczone możliwości zarządzania (dostępne za pomocą szablonów usługi Resource Manager).

<a id="standard-ssds-azure-regions"></a>**Co to są regiony obecnie obsługiwane w przypadku dysków SSD w warstwie standardowa?**
Wszystkie regiony platformy Azure obsługują teraz dysków SSD w warstwie standardowa.

**Jak utworzyć dyski SSD w warstwie standardowa?**
Można utworzyć dysków SSD w warstwie standardowa przy użyciu szablonów usługi Azure Resource Manager, zestawu SDK, programu PowerShell lub interfejsu wiersza polecenia. Poniżej przedstawiono parametry potrzebne w szablonie usługi Resource Manager do utworzenia dysków SSD w warstwie standardowa:

* *wersja interfejsu API* dla dostawcy Microsoft.Compute musi być ustawiona jako `2018-04-01` (lub nowszy)
* Określ *managedDisk.storageAccountType* jako `StandardSSD_LRS`

W poniższym przykładzie przedstawiono *properties.storageProfile.osDisk* dotyczącej maszyny Wirtualnej, która korzysta z dysków SSD w warstwie standardowa:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Aby uzyskać kompletny szablon przykład sposobu tworzenia dysku SSD w warstwie standardowa przy użyciu szablonu, zobacz [Utwórz Maszynę wirtualną z obrazu Windows przy użyciu standardowych dysków z danymi SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Czy mogę przekonwertować istniejące dysków do SSD w warstwie standardowa?**
Tak, możesz. Zapoznaj się [przekonwertować zarządzanego usługi Azure dyski magazynu od planu standard do premium i na odwrót](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/convert-disk-storage) ogólne wytyczne do konwertowania Managed Disks. Ponadto za pomocą następującej wartości w celu zaktualizowania typu dysku na SSD w warstwie standardowa.
-AccountType StandardSSD_LRS

**Zaletą używania dysków SSD w warstwie standardowa zamiast HDD co to jest?**
Dyski SSD w warstwie standardowa dostarczać lepsze opóźnienia spójnością, dostępnością i niezawodnością w porównaniu do dysków HDD. Obciążenia aplikacji znacznie lepsze działanie na SSD w warstwie standardowa z tego powodu. Pamiętaj, że dyski SSD w warstwie Premium są zalecanym rozwiązaniem dla większości obciążeń produkcyjnych intensywnie korzystających z operacji We/Wy. 

**Czy można użyć standardowych dysków SSD, jako dyski niezarządzane?**
Nie, dyski standardowe dyski SSD są dostępne tylko jako dyski Managed Disks.

**Dyski SSD w warstwie standardowa obsługują "SLA pojedynczego wystąpienia maszyny Wirtualnej"?**
Nie, standardowe dyski SSD występuje pojedynczego wystąpienia maszyny Wirtualnej umowy SLA. Używanie dysków Premium SSD dla pojedynczego wystąpienia maszyny Wirtualnej umowy SLA.

## <a name="migrate-to-managed-disks"></a>Migrowanie do usługi Managed Disks

**Jakie zmiany są wymagane w przypadku istniejących kopia zapasowa Azure usługi konfiguracji przed lub po migracji do usługi Managed Disks?**

Żadne zmiany nie są wymagane.

**Moje kopie zapasowe maszyn wirtualnych utworzone za pomocą usługi Azure Backup przed migracją będzie działać?**

Tak, kopie zapasowe działają bezproblemowo.

**Jakie zmiany są wymagane w przypadku istniejących szyfrowania dysków Azure konfiguracji przed lub po migracji do usługi Managed Disks?**

Żadne zmiany nie są wymagane.

**Jest automatycznej migracji istniejących skalowania maszyn wirtualnych zestawów z dysków niezarządzanych do usługi Managed Disks, które są obsługiwane?**

Nie. Można utworzyć nowego zestawu skalowania z przy użyciu obrazu z swoje stare zestawu skalowania przy użyciu dysków niezarządzanych dysków Managed Disks.

**Można utworzyć dysku zarządzanego z migawki obiektu blob strony przed migracją do usługi Managed Disks?**

Nie. Można wyeksportować strony migawki obiektu blob jako obiekt blob typu page i następnie tworzenie dysku zarządzanego na podstawie wyeksportowanego stronicowych obiektów blob.

**Czy mogę w trybie Failover moich maszyn w środowisku lokalnym, chronione przez usługę Azure Site Recovery do maszyny Wirtualnej z usługą Managed Disks?**

Tak, możesz przełączyć się awaryjnie na Maszynę wirtualną z usługą Managed Disks.

**Czy istnieje dowolnego wpływ migracji na maszyny wirtualne platformy Azure chronione przez usługę Azure Site Recovery za pomocą replikacji Azure – Azure?**

Tak. Obecnie usługa Azure Site Recovery do platformy ochrony dla maszyn wirtualnych z usługą Managed Disks jest dostępna tylko jako usługa w publicznej wersji zapoznawczej.

**Można przeprowadzić migrację maszyn wirtualnych z dyskami niezarządzanymi, które znajdują się na kontach magazynu, które są lub wcześniej zostały zaszyfrowane do usługi managed disks**

Yes

## <a name="managed-disks-and-storage-service-encryption"></a>Zarządzane dyski i szyfrowanie usługi Storage

**Szyfrowanie usługi Azure Storage włączono domyślnie podczas tworzenia dysku zarządzanego?**

Tak.

**Kto zarządza kluczami szyfrowania?**

Firma Microsoft zarządza kluczami szyfrowania.

**Mogę wyłączyć szyfrowanie usługi Storage dla dysków zarządzanych?**

Nie.

**Jest szyfrowanie usługi Storage dostępna tylko w określonych regionach?**

Nie. Jest ona dostępna we wszystkich regionach, w której są dostępne dyski Managed Disks. Managed Disks jest dostępna we wszystkich regionach publicznych i Niemcy. Jest również dostępna w Chinach, jednak tylko w przypadku zarządzanych przez Microsoft klucze nie klucze zarządzane przez klienta.

**Jak mogę sprawdzić Mój dysk zarządzany są szyfrowane?**

Godzina utworzenia dysku zarządzanego z witryny Azure portal, interfejsu wiersza polecenia platformy Azure i programu PowerShell można znaleźć. Gdy czas po 9 czerwca 2017 r. są szyfrowane na dysku.

**Jak mogę zaszyfrować moje istniejące dyski, które zostały utworzone przed 10 czerwca 2017 r.**

Od 10 czerwca 2017 r. nowe dane zapisane na istniejących dyskach zarządzanych są automatycznie szyfrowane. Planujemy także szyfrowanie istniejących danych i szyfrowanie nastąpi asynchronicznie w tle. Jeśli teraz musisz zaszyfrować istniejących danych, należy utworzyć kopię dysku. Nowe dyski będą szyfrowane.

* [Kopiowanie dysków zarządzanych przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopiowanie dysków zarządzanych przy użyciu programu PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Czy na zarządzanych migawek i obrazów, szyfrowane?**

Tak. Wszystkie zarządzane migawki i obrazy, utworzonych po 9 czerwca 2017 r. są automatycznie szyfrowane. 

**Czy mogę przekonwertować maszyny wirtualne z dyskami niezarządzanymi, które znajdują się na kontach magazynu, które są lub wcześniej zostały zaszyfrowane do usługi managed disks?**

Yes

**Wyeksportowane wirtualnego dysku twardego z zarządzanego dysku lub migawkę także będą zaszyfrowane?**

Nie. Ale jeśli eksportujesz kolekcję wirtualnego dysku twardego do zaszyfrowanego konta magazynu z zaszyfrowanego zarządzane dysku lub migawki, a następnie jest zaszyfrowany. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dyski w warstwie Premium: zarządzanych i niezarządzanych

**Jeśli maszyna wirtualna używa serii rozmiar, który obsługuje dyski SSD w warstwie Premium, takich jak DSv2, można dołączyć dyski danych w warstwie standardowa i premium?** 

Tak.

**Serii rozmiar, który nie obsługuje dysków w warstwie Premium SSD, takich jak seria D "," Dv2 "," G "lub" F można podłączyć disks w warstwach premium i danych w warstwie standardowa?**

Nie. Tylko dyski danych w warstwie standardowa można dołączać do maszyn wirtualnych, które nie używają serii rozmiar, który obsługuje dyski w warstwie Premium SSD.

**Czy jeśli utworzę dysku danych w warstwie premium z istniejącego dysku VHD, który był 80 GB, ile będzie tego koszt?**

Utworzone na podstawie wirtualny dysk twardy 80 GB dysku danych premium jest traktowany jako rozmiar dysku premium dostępne dalej to dysk typu P10. Opłaty są naliczane zgodnie z cennikiem dysku P10.

**Czy istnieją koszty transakcji, aby korzystać z dysków SSD w warstwie Premium?**

Brak kosztu stałego dla każdego rozmiaru dysku, który jest zainicjowana przy użyciu określonych ograniczeń na operacje We/Wy i przepływność. Innych kosztów są przepustowości wychodzącej i pojemność migawki, jeśli ma to zastosowanie. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Jakie są limity dla operacji We/Wy i przepływność, którą można pobrać z pamięci podręcznej dysku?**

Połączonej limitów pamięci podręcznej i lokalny dysk SSD dla serii DS są 4000 operacje We/Wy na rdzeń i 33 MB na sekundę na podstawowe. Seria GS zapewnia 5000 operacji We/Wy na rdzeń procesora i 50 MB na sekundę na podstawowe.

**Lokalny dysk SSD obsługę zarządzane dyski maszyny Wirtualnej z systemem?**

Lokalny dysk SSD to magazyn tymczasowy, który jest dołączony zarządzane dyski maszyny Wirtualnej z systemem. Jest bez dodatkowych kosztów dla tego magazynu tymczasowego. Zaleca się, że należy używać tego lokalny dysk SSD do przechowywania danych aplikacji, ponieważ nie jest on utrwalonych w magazynie obiektów Blob platformy Azure.

**Czy istnieją wszystkie następstwa dla użytkowania TRIM dysków w warstwie premium?**

Nie ma żadnych wadą wykorzystania PRZYCINANIEM na dyskach platformy Azure w wersji premium albo lub dyski w warstwie standardowa.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nowe rozmiary dysków: zarządzanych i niezarządzanych

**Co to jest największy rozmiar dysku systemu operacyjnego i dysków z danymi obsługiwane?**

Typ partycji, które platforma Azure obsługuje dla dysku systemu operacyjnego jest głównym rekordem rozruchowym (MBR). Obsługuje formacie MBR dysk rozmiar do 2 TB. Największy rozmiar, jak platforma Azure obsługuje dla dysku systemu operacyjnego jest 2 TB. Platforma Azure obsługuje do 4 TB dla dysków z danymi. 

**Co to jest największy rozmiar stronicowego obiektu blob, który jest obsługiwany?**

Największy rozmiar stronicowego obiektu blob platformy Azure obsługuje to 8 TB (8191 GB). Maksymalny rozmiar strony, do blogu, gdy dołączony do maszyny Wirtualnej jako dane lub dyski systemu operacyjnego jest 4 TB (4095 GB).

**Należy używać nowej wersji narzędzi platformy Azure do tworzenia, dołączania, zmienianie rozmiaru i przekaż dysków większych niż 1 TB?**

Nie ma potrzeby uaktualniania z istniejącymi narzędziami platformy Azure do tworzenia, dołączania i zmienianie rozmiaru dysków większych niż 1 TB. Aby przesłać plik wirtualnego dysku twardego ze środowiska lokalnego bezpośrednio na platformie Azure jako stronicowy obiekt blob lub dysk niezarządzany, należy użyć najnowszej zestawów narzędzi:

|Narzędzia platformy Azure      | Obsługiwane wersje                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numer wersji 4.1.0: wersji z czerwca 2017 r. lub nowszej|
|Wiersza polecenia platformy Azure w wersji 1     | Numer wersji 0.10.13: maj 2017 release lub nowszy|
|Narzędzie AzCopy           | Numer wersji 6.1.0: wersji z czerwca 2017 r. lub nowszej|

Wsparcie dla wiersza polecenia platformy Azure w wersji 2 i Eksploratora usługi Azure Storage będzie dostępna wkrótce. 

**Rozmiary dysków P4 i P6 obsługujące dyski niezarządzane i stronicowe obiekty BLOB?**

Nie. P4 (32 GB) i P6 (64 GB) rozmiary dysków są obsługiwane tylko w przypadku dysków zarządzanych. Obsługa dyski niezarządzane i stronicowe obiekty BLOB będzie dostępna wkrótce.

**Jeśli Mój istniejący zarządzanych w warstwie premium dysku z mniej niż 64 GB został utworzony przed włączeniem małego dysku (około 15 czerwca 2017 r.), jak jest rozliczany?**

Istniejące premium małe dyski mniejsze niż 64 GB w dalszym ciągu naliczane zgodnie z warstwą cenową P10. 

**Jak można przełączyć dysku warstwy dysków w warstwie premium w małych, mniej niż 64 GB z P10 P4 lub P6**

Można migawki małych dysków, a następnie utworzyć automatyczne przełączanie warstwy cenowej P4 lub P6, w zależności od rozmiaru aprowizowanego dysku. 


## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli moje pytanie nie ma tutaj odpowiedzi?

Jeśli Twoje pytanie nie ma na liście, Daj nam znać, a my pomożemy Ci znaleźć odpowiedzi. Możesz zadać pytanie na końcu tego artykułu w komentarzach. Aby skontaktuj się z zespołem usługi Azure Storage i inni członkowie społeczności, informacje o tym artykule, należy użyć MSDN [forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Funkcje na żądanie, przesłać żądania i pomysłów dotyczących [forum opinii w usłudze Azure Storage](https://feedback.azure.com/forums/217298-storage).
