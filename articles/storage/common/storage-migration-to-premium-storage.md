---
title: Migrowanie maszyn wirtualnych do usługi Azure Premium Storage | Dokumentacja firmy Microsoft
description: Migrowanie istniejących maszyn wirtualnych do usługi Azure Premium Storage. Usługa Premium Storage oferuje obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla wyjścia — dużych obciążeń wejścia/uruchomione na maszynach wirtualnych platformy Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 5cfb96bd3115c8f3116a28926e93df89dff54351
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153759"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrowanie do usługi Azure Premium Storage (dyski niezarządzane)

> [!NOTE]
> W tym artykule omówiono sposób migrowania maszyny Wirtualnej, która używa standardowych dysków niezarządzanych do maszyny Wirtualnej, która korzysta z dysków niezarządzanych w warstwie premium. Firma Microsoft zaleca używanie usługi Azure Managed Disks dla nowych maszyn wirtualnych i przekonwertowanie wcześniej używanych dysków niezarządzanych do dysków zarządzanych. Zarządzany uchwyt dyski podstawowe konta magazynu, więc nie trzeba. Aby uzyskać więcej informacji, zobacz nasze [Omówienie usługi Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Usługa Azure Premium Storage zapewnia obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych z systemem wyjścia — dużych obciążeń wejścia /. Przy użyciu funkcji migracji dysków maszyn wirtualnych aplikacji do usługi Azure Premium Storage może korzystać z szybkości i wydajności tych dysków.

Ten przewodnik ma na celu pomóc nowych użytkowników usługi Azure Premium Storage jest lepiej przygotować się do płynne przejście z bieżącego systemu do usługi Premium Storage. Przewodnik dotyczy trzy najważniejsze składniki w ramach tego procesu:

* [Planowanie migracji do usługi Premium Storage](#plan-the-migration-to-premium-storage)
* [Przygotowywanie, a następnie skopiuj wirtualne dyski twarde (VHD) do usługi Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Tworzenie maszyny wirtualnej platformy Azure przy użyciu usługi Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Można migrować maszyny wirtualne z innych platform do usługi Azure Premium Storage lub Migrowanie istniejących maszyn wirtualnych platformy Azure z magazynu Standard Storage do usługi Premium Storage. Ten przewodnik obejmuje kroki zarówno w przypadku dwóch scenariuszy. Wykonaj kroki określone w odpowiedniej sekcji w zależności od scenariusza.

> [!NOTE]
> Omówienie funkcji i cen dysków premium SSD w można znaleźć: [Wybierz typ dysku dla maszyn wirtualnych IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). Zalecamy przeprowadzenie migracji dowolnego dysku maszyny wirtualnej, wymagających wysokiej operacje We/Wy do magazynu Azure Premium Storage uzyskać najlepszą wydajność aplikacji. Jeśli dysk nie wymaga wysokiego operacje We/Wy, można ograniczyć koszty dzięki przechowywaniu go w magazynie Standard Storage, która przechowuje dane dysku maszyny wirtualnej na dyski twarde (HDD) zamiast dysków SSD.
>

Kończenie procesu migracji w całości może wymagać dodatkowych akcji przed i po wykonaniu kroków podanych w tym przewodniku. Przykłady obejmują konfigurowanie sieci wirtualnych lub punkty końcowe i zmian kodu samej aplikacji, które mogą wymagać pewien przestój w aplikacji. Te akcje są unikatowe dla każdej aplikacji i należy je ukończyć wraz z kroków podanych w tym przewodniku do pełnego przechodzenia do usługi Premium Storage jako Bezproblemowa, jak to możliwe.

## <a name="plan-the-migration-to-premium-storage"></a>Planowanie migracji do usługi Premium Storage
W tej sekcji gwarantuje, że są gotowe do wykonania tych kroków migracji, w tym artykule i ułatwia najlepszych decyzji o typach maszyn wirtualnych i dysków.

### <a name="prerequisites"></a>Wymagania wstępne
* Będzie niezbędna jest subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć jeden miesiąc [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/) subskrypcji, lub odwiedź [cennika systemu Azure](https://azure.microsoft.com/pricing/) więcej opcji.
* Do wykonania polecenia cmdlet programu PowerShell, konieczne będzie modułu Microsoft Azure PowerShell. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Jeśli planujesz używać maszyn wirtualnych platformy Azure w systemie magazynu w warstwie Premium, musisz użyć zdolne do maszyn wirtualnych usługi Premium Storage. Za pomocą dysków w warstwach Standard i Premium Storage zdolne do maszyn wirtualnych usługi Premium Storage. Dyski magazynu Premium będą dostępne z większej liczby typów maszyn wirtualnych w przyszłości. Aby uzyskać więcej informacji na temat wszystkich dostępnych typów dysków maszyny Wirtualnej platformy Azure i rozmiarów, zobacz [rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [rozmiary usług Cloud Services](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Zagadnienia do rozważenia
Maszynę wirtualną platformy Azure obsługuje dołączanie kilka dysków usługi Premium Storage, aby Twoje aplikacje mogą mieć maksymalnie 256 TB magazynu na maszynę Wirtualną. Dzięki usłudze Premium Storage aplikacje mogą osiągać 80 000 (operacje wejścia/wyjścia na sekundę) na maszynie Wirtualnej i 2000 MB na drugim przepływność dysków na maszynę Wirtualną z tym bardzo małe wartości opóźnienia operacji odczytu. Dostępne są opcje w różnych maszyn wirtualnych i dysków. Ta sekcja dotyczy ułatwiających znajdowanie opcję najlepiej pasującą do obciążenia.

#### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych
Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sprawdź charakterystyki wydajności maszyn wirtualnych, które współpracują z magazynu w warstwie Premium i wybierz odpowiedni rozmiar maszyny Wirtualnej, najlepiej pasujące do obciążenia. Upewnij się, że jest dostępna wystarczająca przepustowość na maszynie Wirtualnej do kierowania ruchu dysku.

#### <a name="disk-sizes"></a>Rozmiary dysków
Istnieje pięć typów dysków, które mogą być używane z maszyny Wirtualnej, a każdy z nich ma określone operacje We/Wy i przepływność limitów. Wziąć pod uwagę te limity Wybieranie typu dysku dla maszyny Wirtualnej odpowiednio do potrzeb aplikacji pod względem wydajności, wydajność, skalowalność, gdy ładuje szczytowego.

| Typ magazynu dysków Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Rozmiar dysku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Przepływność na dysk | 100 MB na sekundę | 150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę |

W zależności od obciążenia należy sprawdzić, czy dyski dodatkowe dane są niezbędne dla maszyny Wirtualnej. Można dołączyć kilka dysków danych trwałych do maszyny Wirtualnej. Jeśli to konieczne, można stripe na dyskach w celu zwiększenia pojemności i wydajności woluminu. (Zobacz, co jest rozkładanie [tutaj](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Jeśli stripe dysków z danymi usługi Premium Storage przy użyciu [miejsca do magazynowania][4], należy ją skonfigurować z jedną kolumnę dla każdego dysku, który jest używany. W przeciwnym razie ogólną wydajność rozłożone wolumin może być mniejszy niż oczekiwano z powodu nierówna Dystrybucja ruchu między dyskami. W przypadku maszyn wirtualnych systemu Linux można użyć *mdadm* narzędzie, aby osiągnąć takie same. Zobacz artykuł [konfigurowanie macierzy RAID oprogramowania w systemie Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby uzyskać szczegółowe informacje.

#### <a name="storage-account-scalability-targets"></a>Cele skalowalności konta magazynu
Kont usługi Premium Storage ma następujące cele skalowalności, oprócz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md). Wymagania aplikacji przekroczy cele skalowalności konta magazynu w jednym, tworzenie aplikacji na używanie wielu kont magazynu, a partycjonowanie danych na tych kontach magazynu.

| Łączna liczba kont pojemności | Całkowitej przepustowości dla konta magazynu lokalnie nadmiarowego |
|:--- |:--- |
| Pojemność dysku: 35TB<br />Pojemność migawki: 10 TB |Maksymalnie 50 gigabity na sekundę dla ruchu przychodzącego i ruchu wychodzącego |

Aby uzyskać więcej informacji o specyfikacjach usługi Premium Storage, zapoznaj się [cele dotyczące skalowalności i wydajności usługi Azure Storage](storage-scalability-targets.md#premium-performance-storage-account-scale-limits).

#### <a name="disk-caching-policy"></a>Zasady buforowania dysku
Domyślnie zasady buforowania dysku jest *tylko do odczytu* wszystkich dysków w warstwie Premium danych, a *odczytu i zapisu* dla dysku systemu operacyjnego w warstwie Premium dołączonych do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane, aby osiągnąć optymalną wydajność dla aplikacji systemu IOs. Dla dysków z danymi zapisu przy odczycie czy tylko do zapisu (takich jak pliki dziennika programu SQL Server) należy wyłączyć buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji. Ustawienia pamięci podręcznej dla istniejących dysków z danymi można zaktualizować przy użyciu [witryny Azure portal](https://portal.azure.com) lub *- HostCaching* parametru *AzureDataDisk zestaw* polecenia cmdlet.

#### <a name="location"></a>Lokalizacja
Wybierz lokalizację, w których usługi Azure Premium Storage jest dostępna. Zobacz [usług platformy Azure według regionu](https://azure.microsoft.com/regions/#services) dla aktualnych informacji o dostępnych lokalizacji. Maszyny wirtualne znajdujące się w tym samym regionie co konto magazynu, że dyski maszyny Wirtualnej będzie sklepy znacznie lepszą wydajność niż jeśli znajdują się w oddzielnych regionach.

#### <a name="other-azure-vm-configuration-settings"></a>Inne ustawienia konfiguracji maszyny Wirtualnej platformy Azure
Podczas tworzenia maszyny Wirtualnej platformy Azure, poprosimy Cię o Konfigurowanie niektórych ustawień maszyny Wirtualnej. Należy pamiętać, że kilka ustawień zostały naprawione dla okresu istnienia maszyny wirtualnej, podczas gdy można zmodyfikować lub dodać inne później. Przejrzyj te ustawienia konfiguracji maszyny Wirtualnej platformy Azure i upewnij się, że te są skonfigurowane odpowiednio do wymagań obciążenia.

### <a name="optimization"></a>Optymalizacja
[Azure Premium Storage: Projektowanie pod kątem wysokiej wydajności](../../virtual-machines/windows/premium-storage-performance.md) zawiera wytyczne dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Możesz wykonać wytycznych w połączeniu z najlepsze rozwiązania w zakresie wydajności mające zastosowanie do technologii wykorzystywanych przez aplikację.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Przygotowywanie, a następnie skopiuj wirtualne dyski twarde (VHD) do usługi Premium Storage
W poniższej sekcji przedstawiono wskazówki dotyczące przygotowywania wirtualnych dysków twardych na podstawie maszyny Wirtualnej i kopiowanie wirtualnych dysków twardych do usługi Azure Storage.

* [Scenariusz 1: "Migruję istniejących maszyn wirtualnych platformy Azure do usługi Azure Premium Storage."](#scenario1)
* [Scenariusz 2: "Migruję maszyn wirtualnych z innych platform do usługi Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Wymagania wstępne
Aby przygotować dyski VHD do migracji, będą potrzebne:

* Subskrypcja platformy Azure, konta magazynu i kontener na tym koncie magazynu, do którego można skopiować wirtualnego dysku twardego. Należy pamiętać, że docelowe konto magazynu może być kontem Standard lub Premium Storage, w zależności od wymagań.
* Narzędzie do uogólnienia wirtualny dysk twardy, jeśli planowane jest tworzenie wielu wystąpień maszyny Wirtualnej z niego. Na przykład, program sysprep dla Windows lub virt programu sysprep dla systemu Ubuntu.
* Narzędzie do przekazania pliku VHD na koncie magazynu. Zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md) lub użyj [Eksplorator usługi Azure storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). W tym przewodniku opisano kopiowania wirtualnego dysku twardego za pomocą narzędzia AzCopy.

> [!NOTE]
> Jeśli wybierzesz opcję Kopia synchroniczna za pomocą narzędzia AzCopy, aby uzyskać optymalną wydajność, należy skopiować wirtualnego dysku twardego, uruchamiając jeden z tych narzędzi z maszyny Wirtualnej platformy Azure, która znajduje się w tym samym regionie co konto magazynu docelowego. Jeśli kopiujesz dysk VHD maszyny wirtualnej platformy Azure w innym regionie, wydajność może przebiegać wolniej.
>
> W przypadku kopiowania dużych ilości danych w ramach ograniczonej przepustowości, rozważ [przy użyciu usługi Azure Import/Export do przesyłania danych do magazynu obiektów Blob](../storage-import-export-service.md); dzięki temu można przenieść dane przez wysyłanie dysków twardych do centrum danych platformy Azure. Usługa Azure Import/Export umożliwia kopiowanie danych na koncie magazynu w warstwie standardowa. Gdy dane znajdują się na koncie magazynu w warstwie standardowa, możesz użyć albo [API obiektu Blob kopiowania](https://msdn.microsoft.com/library/azure/dd894037.aspx) lub narzędzia AzCopy do transferu danych do konta usługi premium storage.
>
> Należy pamiętać, że platforma Microsoft Azure obsługuje tylko pliki wirtualnego dysku twardego o stałym rozmiarze. Pliki VHDX lub dynamicznych wirtualnych dysków twardych nie są obsługiwane. W przypadku dynamicznego wirtualnego dysku twardego można przekonwertować go do korzystania z o stałym rozmiarze [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet.
>
>

### <a name="scenario1"></a>Scenariusz 1: "Migruję istniejących maszyn wirtualnych platformy Azure do usługi Azure Premium Storage."
W przypadku migracji istniejących maszyn wirtualnych platformy Azure, Zatrzymaj maszynę Wirtualną, wirtualne dyski twarde dla typu wirtualnego dysku twardego, należy przygotować, a następnie skopiuj wirtualny dysk twardy za pomocą narzędzia AzCopy lub programu PowerShell.

Maszyna wirtualna musi być całkowicie w dół do migracji stanu czystego. Nastąpi Przestój, do momentu ukończenia migracji.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Przygotuj wirtualnych dysków twardych do migracji
W przypadku migracji istniejących maszyn wirtualnych platformy Azure do usługi Premium Storage może być wirtualnego dysku twardego:

* Obraz systemu operacyjnego uogólnionego
* Dysk systemu operacyjnego
* Dysk z danymi

Poniżej omówimy nielicznych 3 w przypadku przygotowywania wirtualnego dysku twardego.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Użyj uogólnionego wirtualnego dysku twardego systemu operacyjnego, aby utworzyć wiele wystąpień maszyny Wirtualnej
Podczas przekazywania wirtualnego dysku twardego, która będzie służyć do tworzenia wielu ogólnych wystąpień maszyny Wirtualnej platformy Azure, musisz najpierw generalize dysku VHD za pomocą narzędzia sysprep. Dotyczy to do wirtualnego dysku twardego, który działa lokalnie lub w chmurze. Narzędzie Sysprep usuwa wszystkie informacje dotyczące komputera z dysku VHD.

> [!IMPORTANT]
> Tworzenie migawki lub utworzyć kopię zapasową maszyny Wirtualnej przed uogólnieniem go. Uruchamianie programu sysprep spowoduje zatrzymanie i cofnięcie przydziału wystąpienia maszyny Wirtualnej. Wykonaj kroki przedstawione poniżej Sysprep wirtualny dysk twardy systemu Windows. Należy pamiętać, że uruchamianie polecenia Sysprep konieczne będzie można zamknąć maszyny wirtualnej. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Omówienie programu Sysprep](https://technet.microsoft.com/library/hh825209.aspx) lub [techniczne dotyczące narzędzia Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otwórz okno wiersza polecenia jako administrator.
2. Wprowadź następujące polecenie, aby otworzyć program Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Narzędzie przygotowania systemu wybierz środowisko systemu wprowadź Out-of-Box (OOBE), zaznacz pole wyboru Generalize, wybierz **zamknięcia**, a następnie kliknij przycisk **OK**, jak pokazano na poniższej ilustracji. Narzędzie Sysprep będzie uogólnienia systemu operacyjnego i zamykania systemu.

    ![][1]

Dla maszyny Wirtualnej systemu Ubuntu należy użyć narzędzia sysprep virt, aby osiągnąć takie same. Zobacz [virt sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) Aby uzyskać więcej informacji. Zobacz też niektóre "open source" [rozbudowy serwerów Linux oprogramowania](https://www.cyberciti.biz/tips/server-provisioning-software.html) dla innych systemów operacyjnych Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Użyj unikatowy wirtualnego dysku twardego systemu operacyjnego, aby utworzyć pojedyncze wystąpienie maszyny Wirtualnej
W przypadku aplikacji uruchomionej na maszynie Wirtualnej, która wymaga określonych danych maszyny nie generalize wirtualnego dysku twardego. Uogólniony wirtualny dysk twardy może służyć do tworzenia unikatowego wystąpienia maszyny Wirtualnej platformy Azure. Na przykład jeśli masz kontrolera domeny na wirtualnego dysku twardego, wykonywanie programu sysprep, spowoduje to nieefektywne jako kontroler domeny. Przejrzyj aplikacje uruchomione na maszynie Wirtualnej i wpływ na nich uruchomiona narzędzia sysprep przed uogólnieniem wirtualnego dysku twardego.

##### <a name="register-data-disk-vhd"></a>Zarejestruj dysków Twardych z danymi
Jeśli masz dyski z danymi na platformie Azure do migracji, upewnij się, że maszyny wirtualne, które korzystają z tych dysków danych są zamykane.

Wykonaj kroki opisane poniżej, aby skopiować wirtualny dysk twardy do usługi Azure Premium Storage i zarejestruj go jako dysk z danymi elastycznie.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Utwórz miejsce docelowe dla wirtualnego dysku twardego
Utwórz konto magazynu, obsługę sieci wirtualnych dysków twardych. Podczas planowania miejsca przechowywania Twoje dyski VHD, należy wziąć pod uwagę następujące kwestie:

* Miejsce docelowe konto magazynu Premium storage.
* Lokalizacja konta magazynu musi być taki sam jak magazyn w warstwie Premium stanie maszyn wirtualnych platformy Azure utworzonego w ostatnim etapie. Można skopiować do nowego konta magazynu lub planujesz jej używać tego samego konta magazynu, w zależności od potrzeb.
* Skopiuj i Zapisz klucz konta magazynu docelowego konta magazynu do kolejnego etapu.

W przypadku dysków danych można wybrać zachować niektóre dyski danych na koncie magazynu w warstwie standardowa (na przykład, dyski, na których chłodniejszej magazynu), ale zdecydowanie zalecamy przeniesienie wszystkich danych dla obciążenia produkcyjnego do użycia magazynu w warstwie premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3. Skopiuj wirtualny dysk twardy za pomocą narzędzia AzCopy lub programu PowerShell
Musisz znaleźć usługi kontenera ścieżki i klucza konta magazynu do jednej z tych dwóch opcji przetwarzania. Kontener ścieżki i klucza konta magazynu można znaleźć w **witryny Azure Portal** > **magazynu**. Kontener, adres URL będzie miał takie jak "https:\//myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opcja 1: Skopiuj wirtualny dysk twardy za pomocą narzędzia AzCopy (kopia asynchroniczny)
Przy użyciu narzędzia AzCopy, można łatwo przekazywać wirtualny dysk twardy za pośrednictwem Internetu. W zależności od rozmiaru wirtualnych dysków twardych to może potrwać. Pamiętaj sprawdzić ruch przychodzący i wychodzący limity konta magazynu przy użyciu tej opcji. Zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md) Aby uzyskać szczegółowe informacje.

1. Pobierz i zainstaluj narzędzie AzCopy w tym miejscu: [Najnowszą wersję programu AzCopy](https://aka.ms/downloadazcopy)
2. Otwórz program Azure PowerShell i przejdź do folderu, w którym zainstalowano narzędzia AzCopy.
3. Użyj następującego polecenia, aby skopiować plik wirtualnego dysku twardego z "Źródło" do "Miejsce docelowe".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Przykład:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Poniżej przedstawiono opisy parametrów polecenia narzędzia AzCopy:

   * **/ Źródło:  *&lt;źródła&gt;:*** Lokalizacja folderu lub adres URL kontenera magazynu, który zawiera wirtualny dysk twardy.
   * **/ SourceKey:  *&lt;klucza w przypadku konta źródłowego&gt;:*** Klucz konta magazynu źródłowego konta magazynu.
   * **/ Dest:  *&lt;docelowy&gt;:*** Adres URL kontenera magazynu do wirtualnego dysku twardego do skopiowania.
   * **/ DestKey:  *&lt;klucza w przypadku konta dest&gt;:*** Klucz konta magazynu docelowego konta magazynu.
   * **/ Wzorzec:  *&lt;nazwa pliku&gt;:*** Określ nazwę pliku wirtualnego dysku twardego do skopiowania.

Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia AzCopy narzędzia, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opcja 2: Skopiuj wirtualny dysk twardy za pomocą programu PowerShell (Synchronized kopia)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ponadto można kopiować pliku VHD, za pomocą polecenia cmdlet programu PowerShell AzStorageBlobCopy rozpoczęcia. Użyj następującego polecenia w programie Azure PowerShell, aby skopiować wirtualny dysk twardy. Zastąp wartości w <> odpowiadające im wartości z konta magazynu źródłowego i docelowego. Aby użyć tego polecenia, konieczne jest posiadanie kontener o nazwie wirtualne dyski twarde na koncie magazynu docelowego. Jeśli kontener nie istnieje, utwórz ją przed uruchomieniem polecenia.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Przykład:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenariusz 2: "Migruję maszyn wirtualnych z innych platform do usługi Azure Premium Storage."
W przypadku migracji wirtualnego dysku twardego z innych niż - Azure magazynu w chmurze na platformie Azure, możesz wyeksportować wirtualnego dysku twardego do katalogu lokalnego. Ma ścieżkę źródłową pełny katalog lokalny wirtualny dysk twardy przechowywania wygodny, a następnie przekaż go do usługi Azure Storage przy użyciu narzędzia AzCopy.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Eksportuj wirtualnego dysku twardego do katalogu lokalnego
##### <a name="copy-a-vhd-from-aws"></a>Skopiuj wirtualny dysk twardy od usług AWS
1. Jeśli używasz usługi AWS należy wyeksportować wystąpienia usługi EC2 do wirtualnego dysku twardego w zasobniku Amazon S3. Wykonaj kroki opisane w dokumentacji Amazon eksportowanie wystąpień usługi EC2 Amazon zainstalować narzędzie interfejsu wiersza polecenia (CLI) usługi Amazon EC2, a następnie uruchom polecenie export zadanie, tworzenie wystąpień w — Aby wyeksportować wystąpienia usługi EC2 pliku wirtualnego dysku twardego. Należy użyć **wirtualnego dysku twardego** dysku&#95;obraz&#95;zmiennej formatu podczas uruchamiania **Tworzenie wystąpienia — export zadań** polecenia. Wyeksportowany plik wirtualnego dysku twardego jest zapisywany w zasobniku Amazon S3, wyznaczonego podczas tego procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Pobierz plik wirtualnego dysku twardego z przedział S3. Wybierz plik wirtualnego dysku twardego, a następnie **akcje** > **Pobierz**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Skopiuj wirtualny dysk twardy z innych chmur spoza platformy Azure
W przypadku migracji wirtualnego dysku twardego z innych niż - Azure magazynu w chmurze na platformie Azure, możesz wyeksportować wirtualnego dysku twardego do katalogu lokalnego. Skopiuj ścieżkę źródłową pełną katalogu lokalnego, gdzie znajduje się wirtualny dysk twardy.

##### <a name="copy-a-vhd-from-on-premises"></a>Skopiuj wirtualny dysk twardy ze środowiska lokalnego
Jeśli wirtualny dysk twardy są migrowane ze środowiska lokalnego, konieczne będzie ścieżki źródłowej pełną, gdzie znajduje się wirtualny dysk twardy. Ścieżka źródłowa może być serwerem lokalizacji lub w udziale plików.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Utwórz miejsce docelowe dla wirtualnego dysku twardego
Utwórz konto magazynu, obsługę sieci wirtualnych dysków twardych. Podczas planowania miejsca przechowywania Twoje dyski VHD, należy wziąć pod uwagę następujące kwestie:

* Docelowe konto magazynu może być standardowa lub premium storage w zależności od wymagań aplikacji.
* Region konta magazynu musi być taki sam jak magazyn w warstwie Premium stanie maszyn wirtualnych platformy Azure utworzonego w ostatnim etapie. Można skopiować do nowego konta magazynu lub planujesz jej używać tego samego konta magazynu, w zależności od potrzeb.
* Skopiuj i Zapisz klucz konta magazynu docelowego konta magazynu do kolejnego etapu.

Zdecydowanie zalecamy przeniesienie wszystkich danych dla obciążenia produkcyjnego do użycia magazynu w warstwie premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Przekazywanie wirtualnego dysku twardego do usługi Azure Storage
Teraz, gdy masz wirtualnego dysku twardego w katalogu lokalnym, można użyć narzędzia AzCopy lub AzurePowerShell przekazać plik VHD do usługi Azure Storage. Obie opcje są dostępne tutaj:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opcja 1: Przekazywanie pliku VHD za pomocą programu PowerShell Azure Add-AzureVhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Przykład \<Uri > musi być ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Przykład \<FileInfo > może być ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opcja 2: Przekazywanie pliku VHD za pomocą narzędzia AzCopy
Przy użyciu narzędzia AzCopy, można łatwo przekazywać wirtualny dysk twardy za pośrednictwem Internetu. W zależności od rozmiaru wirtualnych dysków twardych to może potrwać. Pamiętaj sprawdzić ruch przychodzący i wychodzący limity konta magazynu przy użyciu tej opcji. Zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md) Aby uzyskać szczegółowe informacje.

1. Pobierz i zainstaluj narzędzie AzCopy w tym miejscu: [Najnowszą wersję programu AzCopy](https://aka.ms/downloadazcopy)
2. Otwórz program Azure PowerShell i przejdź do folderu, w którym zainstalowano narzędzia AzCopy.
3. Użyj następującego polecenia, aby skopiować plik wirtualnego dysku twardego z "Źródło" do "Miejsce docelowe".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Przykład:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Poniżej przedstawiono opisy parametrów polecenia narzędzia AzCopy:

   * **/ Źródło:  *&lt;źródła&gt;:*** Lokalizacja folderu lub adres URL kontenera magazynu, który zawiera wirtualny dysk twardy.
   * **/ SourceKey:  *&lt;klucza w przypadku konta źródłowego&gt;:*** Klucz konta magazynu źródłowego konta magazynu.
   * **/ Dest:  *&lt;docelowy&gt;:*** Adres URL kontenera magazynu do wirtualnego dysku twardego do skopiowania.
   * **/ DestKey:  *&lt;klucza w przypadku konta dest&gt;:*** Klucz konta magazynu docelowego konta magazynu.
   * **/ BlobType: strony:** Określa, czy miejsce docelowe jest stronicowym obiektem blob.
   * **/ Wzorzec:  *&lt;nazwa pliku&gt;:*** Określ nazwę pliku wirtualnego dysku twardego do skopiowania.

Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia AzCopy narzędzia, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Inne opcje do przekazania dysku VHD
Możesz również przekazać dysku VHD do konta magazynu przy użyciu jednej z następujących sposobów:

* [Usługa Azure Storage obiektu Blob kopiowania interfejsu API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Przekazywanie obiektów blob Eksploratora usługi Azure Storage](https://azurestorageexplorer.codeplex.com/)
* [Dokumentacja interfejsu API REST usługi Import/Export magazynu](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Firma Microsoft zaleca, za pomocą usługi Import/Export, jeśli szacowany czas przekazywania jest dłuższy niż 7 dni. Możesz użyć [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) można oszacować, na godzinę z jednostek rozmiaru i transfer danych.
>
> Import/Export może służyć do skopiowania do konta magazynu w warstwie standardowa. Należy skopiować z magazynu standard storage do konta usługi premium storage przy użyciu narzędzia, takiego jak narzędzie AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Tworzenie maszyn wirtualnych platformy Azure przy użyciu usługi Premium Storage
Po wirtualny dysk twardy zostanie przekazany lub kopiowane do odpowiednie konto magazynu, postępuj zgodnie z instrukcjami w tej sekcji, aby zarejestrować wirtualnego dysku twardego jako obraz systemu operacyjnego lub dysk systemu operacyjnego, w zależności od scenariusza, a następnie utwórz wystąpienie maszyny Wirtualnej z niego. Dysk danych wirtualnego dysku twardego może być dołączony do maszyny Wirtualnej, po jego utworzeniu.
Przykładowy skrypt migracji znajduje się na końcu tej sekcji. Ten prosty skrypt jest niezgodna z wszystkich scenariuszy. Może być konieczne zaktualizowanie skryptów do pasowania do określonego scenariusza. Aby dowiedzieć się, jeśli ten skrypt ma zastosowanie do danego scenariusza, zobacz poniżej [przykładowy migracji skrypt](#a-sample-migration-script).

### <a name="checklist"></a>Lista kontrolna
1. Zaczekaj, aż wszystkie dyski VHD Kopiowanie zostało ukończone.
2. Upewnij się, że Usługa Premium Storage jest dostępna w regionie, w którym jest przeprowadzana migracja do.
3. Zdecyduj, nowych seriach maszyn wirtualnych, które będą używane. Powinien być zdolny do usługi Premium Storage, a rozmiar powinien być w zależności od dostępności w regionie i zgodnie z potrzebami.
4. Określić dokładny rozmiar maszyny Wirtualnej, które będą używane. Rozmiar maszyny Wirtualnej musi być wystarczająco duży, aby obsługiwać liczbę dysków z danymi, które należy. Na przykład Jeśli masz 4 dyski z danymi, maszyna wirtualna musi mieć co najmniej 2 rdzeni. Ponadto należy wziąć pod uwagę mocy obliczeniowej, pamięci i przepustowość sieci musi.
5. Utwórz konto usługi Premium Storage w regionie docelowym. Jest to konto, które będą używane dla nowej maszyny Wirtualnej.
6. Mieć bieżące szczegóły maszyny Wirtualnej zawsze pod ręką, w tym listę dysków i odpowiednie obiekty BLOB dysków VHD.

Przygotowanie aplikacji dla przestojów. Aby przeprowadzić migrację czystego, masz Zatrzymaj przetwarzanie wszystkich w obecnym systemie. Następnie możesz pobrać go do spójnego stanu, które można migrować do nowej platformy. Czas trwania przestoju zależy od ilości danych na dyskach, aby przeprowadzić migrację.

> [!NOTE]
> Jeśli tworzysz Maszynę wirtualną platformy Azure Resource Manager z wyspecjalizowanego dysku VHD, można znaleźć [ten szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) dotyczące wdrażania maszyny Wirtualnej usługi Resource Manager przy użyciu istniejącego dysku.
>
>

### <a name="register-your-vhd"></a>Zarejestruj wirtualnego dysku twardego
Aby utworzyć Maszynę wirtualną z wirtualnego dysku twardego systemu operacyjnego lub dołączanie dysku danych do nowej maszyny Wirtualnej, należy je najpierw zarejestrować. Wykonaj poniższe kroki w zależności od scenariusza z wirtualnego dysku twardego.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony wirtualny dysk twardy systemu operacyjnego, umożliwiająca utworzenie wielu wystąpień maszyny Wirtualnej platformy Azure
Po przekazaniu obrazu systemu operacyjnego uogólnionego wirtualnego dysku twardego do konta magazynu, zarejestruj go jako **obrazu maszyny Wirtualnej platformy Azure** tak, aby z niego można utworzyć co najmniej jedno wystąpienie maszyny Wirtualnej. Użyj następujących poleceń cmdlet programu PowerShell, aby zarejestrować wirtualnego dysku twardego jako obraz systemu operacyjnego maszyny Wirtualnej platformy Azure. Podaj adres URL pełną kontenera, w której wirtualny dysk twardy został skopiowany do.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Skopiuj i Zapisz nazwę tego nowego obrazu maszyny Wirtualnej platformy Azure. W powyższym przykładzie jest *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowe wirtualnego dysku twardego systemu operacyjnego do utworzenia pojedynczego wystąpienia maszyny Wirtualnej platformy Azure
Unikatowe wirtualnego dysku twardego systemu operacyjnego przekazane do konta magazynu należy zarejestrować go jako **dysku systemu operacyjnego Azure** tak, aby z niego, można utworzyć wystąpienie maszyny Wirtualnej. Użyj tych poleceń cmdlet programu PowerShell, aby zarejestrować wirtualnego dysku twardego jako dysk systemu operacyjnego platformy Azure. Podaj adres URL pełną kontenera, w której wirtualny dysk twardy został skopiowany do.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Skopiuj i Zapisz nazwę tego nowego dysku systemu operacyjnego platformy Azure. W powyższym przykładzie jest *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Dane wirtualny dysk twardy dołączony do nowego wystąpienia maszyny Wirtualnej platformy Azure
Po przekazaniu danych dysku VHD do konta magazynu, zarejestruj go jako dysk danych platformy Azure, dzięki czemu będzie można dołączyć do nowego serii DS, DSv2 serii lub wystąpienia maszyny Wirtualnej platformy Azure serii GS.

Użyj tych poleceń cmdlet programu PowerShell, aby zarejestrować wirtualnego dysku twardego jako dysk danych platformy Azure. Podaj adres URL pełną kontenera, w której wirtualny dysk twardy został skopiowany do.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Skopiuj i Zapisz nazwę tego nowy dysk danych platformy Azure. W powyższym przykładzie jest *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Tworzenie maszyny Wirtualnej zdolne do magazynu w warstwie Premium
Gdy obraz systemu operacyjnego lub dysk systemu operacyjnego są rejestrowane, tworzenie nowej serii DS, dsv2 lub maszyny Wirtualnej serii GS. Używany będzie obraz systemu operacyjnego lub nazwa dysku systemu operacyjnego, który został zarejestrowany. Wybierz typ maszyny Wirtualnej z warstwy Premium Storage. W poniższym przykładzie użyto *Standard_DS2* rozmiar maszyny Wirtualnej.

> [!NOTE]
> Zaktualizuj rozmiaru dysku, upewnij się, że jest on zgodny z pojemnością i wymagania dotyczące wydajności i rozmiary dysku platformy Azure.
>
>

Postępuj zgodnie z poleceń cmdlet programu PowerShell krok po kroku, które są poniżej, aby utworzyć nową maszynę Wirtualną. Najpierw ustaw parametry wspólne:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Najpierw Utwórz usługę w chmurze w którym będzie obsługiwać nowe maszyny wirtualne.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Następnie w zależności od scenariusza, należy utworzyć wystąpienie maszyny Wirtualnej platformy Azure z obrazu systemu operacyjnego lub dysk systemu operacyjnego, który został zarejestrowany.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony wirtualny dysk twardy systemu operacyjnego, umożliwiająca utworzenie wielu wystąpień maszyny Wirtualnej platformy Azure
Utwórz jeden lub więcej nowych maszyn wirtualnych platformy Azure serii DS wystąpienia przy użyciu **obrazu systemu operacyjnego Azure** zarejestrowaną. Określ nazwę tego obrazu systemu operacyjnego w konfiguracji maszyny Wirtualnej, podczas tworzenia nowej maszyny Wirtualnej, jak pokazano poniżej.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowe wirtualnego dysku twardego systemu operacyjnego do utworzenia pojedynczego wystąpienia maszyny Wirtualnej platformy Azure
Utwórz nowy DS serii maszyny Wirtualnej platformy Azure wystąpienia przy użyciu **dysku systemu operacyjnego Azure** zarejestrowaną. Określ nazwę tego dysku systemu operacyjnego w konfiguracji maszyny Wirtualnej, podczas tworzenia nowej maszyny Wirtualnej, jak pokazano poniżej.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Określ inne informacje maszyny Wirtualnej platformy Azure, takich jak usługi w chmurze, region, konto magazynu, zestaw dostępności i zasad buforowania. Należy zauważyć, że wystąpienia maszyny Wirtualnej musi być kolokowany z skojarzone systemu operacyjnego ani dysków z danymi, więc wybrana chmura usługi, region i konto magazynu muszą być w tej samej lokalizacji co podstawowe wirtualne dyski twarde w tych dysków.

### <a name="attach-data-disk"></a>Dołączanie dysku danych
Ponadto jeśli zarejestrowano dysk VHD z danymi, dołącz je do nowego magazynu w warstwie Premium stanie maszyny Wirtualnej platformy Azure.

Skorzystaj z następujących poleceń cmdlet programu PowerShell do dołączenia dysku danych do nowej maszyny Wirtualnej i określanie zasad pamięci podręcznej. W poniższym przykładzie ustawiono zasady buforowania *tylko do odczytu*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Mogą istnieć dodatkowe kroki niezbędne do obsługi aplikacji, który jest nie być dostępne w tym przewodniku.
>
>

### <a name="checking-and-plan-backup"></a>Sprawdzanie i Planowanie kopii zapasowych
Gdy nowa maszyna wirtualna jest uruchomiona, dostęp do niego przy użyciu tego samego identyfikatora logowania i hasło jako oryginalnej maszyny Wirtualnej i sprawdź, czy wszystko działa zgodnie z oczekiwaniami. Wszystkie ustawienia, w tym woluminy rozłożone będzie znajdować się w nowej maszyny Wirtualnej.

Ostatnim krokiem jest zaplanowanie kopia zapasowa i harmonogram konserwacji dla nowej maszyny Wirtualnej odpowiednio do potrzeb aplikacji.

### <a name="a-sample-migration-script"></a>Przykładowy skrypt migracji
Jeśli masz wiele maszyn wirtualnych do migracji, automatyzacja za pośrednictwem skryptów programu PowerShell będą przydatne. Poniżej przedstawiono przykładowy skrypt, który automatyzuje migracja maszyny Wirtualnej. Uwaga poniżej skrypt jest tylko przykładem, a kilka założeń dotyczących bieżącego dysków maszyny Wirtualnej. Może być konieczne zaktualizowanie skryptów do pasowania do określonego scenariusza.

Dostępne są następujące założenia:

* Tworzysz klasycznych maszyn wirtualnych platformy Azure.
* Dyski systemu operacyjnego źródłowej i dysków danych źródłowych znajdują się w tym samym koncie magazynu i tym samym kontenerze. Dyski systemu operacyjnego i dyski danych nie są w tym samym miejscu, można skopiować wirtualne dyski twarde za pośrednictwem konta magazynu i kontenerów można użyć narzędzia AzCopy lub programu Azure PowerShell. Można znaleźć w poprzednim kroku: [Skopiuj wirtualny dysk twardy za pomocą narzędzia AzCopy lub programu PowerShell](#copy-vhd-with-azcopy-or-powershell). Edytowanie tego skryptu w celu spełnienia danego scenariusza jest inną możliwością, ale zalecamy używanie narzędzia AzCopy lub programu PowerShell, ponieważ jest łatwiejsze i szybsze.

Skrypt automatyzacji znajduje się poniżej. Zamień tekst informacje, a następnie zaktualizować ten skrypt, aby dopasować przy użyciu określonego scenariusza.

> [!NOTE]
> Przy użyciu istniejącego skryptu nie zostaną zachowane konfiguracji sieci źródłowa maszyna wirtualna. Konieczne będzie ponownej konfiguracji ustawień sieciowych na maszynach wirtualnych migrowanych.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optymalizacja
Bieżąca konfiguracja maszyny Wirtualnej można dostosować specjalnie do sprawnej współpracy z dysków w warstwie standardowa. Na przykład aby zwiększyć wydajność przy użyciu wielu dysków w woluminu rozłożonego. Na przykład zamiast 4 dyski oddzielnie w magazynie Premium Storage, można zoptymalizować koszty dzięki jednego dysku. Optymalizacje, takie jak konieczność obsługi na podstawie przypadku i wymagają niestandardowych krokach po zakończeniu migracji. Należy również zauważyć, że ten proces nie może również działać dla baz danych i aplikacji, które są zależne od układu dysku zdefiniowany w ustawieniach.

##### <a name="preparation"></a>Przygotowanie
1. Wykonaj proste migracji, zgodnie z opisem we wcześniejszej sekcji. Optymalizacje odbędzie się w nowej maszyny Wirtualnej po zakończeniu migracji.
2. Zdefiniuj nowe rozmiary dysków niezbędne do skonfigurowania zoptymalizowane.
3. Należy określić mapowanie bieżącego dysków/woluminów do specyfikacji nowego dysku.

##### <a name="execution-steps"></a>Wykonanie czynności
1. Utwórz nowe dyski o odpowiednim rozmiarze na maszynie Wirtualnej z magazynu Premium.
2. Zaloguj się do maszyny Wirtualnej i kopiowania danych z bieżącego woluminu na nowy dysk, który jest mapowany do tego woluminu. W tym wszystkie woluminy bieżącego potrzebnych do mapowania na nowy dysk.
3. Następnie zmień ustawienia aplikacji, aby przełączyć się na nowe dyski i odłączyć starych woluminów.

Dostrajanie aplikacji w celu zapewnienia lepszej wydajności dysków, można znaleźć w sekcji Optymalizacja wydajności aplikacji z naszych [projektowanie pod kątem wysokiej wydajności](../../virtual-machines/windows/premium-storage-performance.md) artykułu.

### <a name="application-migrations"></a>Migracja aplikacji
Baz danych i innych złożone aplikacje mogą wymagać specjalne kroki zgodnie z definicją dostawcy aplikacji, w związku z migracją. Zapoznaj się z dokumentacją odpowiedniej aplikacji. Na przykład Zazwyczaj można migrować bazy danych przy użyciu kopii zapasowej i przywracania.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące zasoby dla konkretnych scenariuszy do migrowania maszyn wirtualnych:

* [Migrowanie maszyn wirtualnych platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Windows Server na platformie Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrowanie maszyn wirtualnych ze środowiska Amazon AWS na platformę Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zobacz też następujące zasoby, aby dowiedzieć się więcej na temat usługi Azure Storage i Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Wybierz typ dysku dla maszyn wirtualnych IaaS](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
