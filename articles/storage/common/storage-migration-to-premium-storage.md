---
title: Migrowanie maszyn wirtualnych do magazynu Azure Premium | Dokumentacja firmy Microsoft
description: Migrowanie istniejących maszyn wirtualnych do usługi Azure Premium Storage. Magazyn w warstwie Premium oferuje obsługę dysków o wysokiej wydajności i małych opóźnieniach/O wykonujących obciążeń uruchomionych na maszynach wirtualnych platformy Azure.
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: 36ff73d36c752fb342dcfff2360b4f6f7013740e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "27993918"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrowanie do magazynu Azure Premium (niezarządzany dysków)

> [!NOTE]
> W tym artykule omówiono sposób migracji maszyny Wirtualnej, który używa niezarządzane standardowych dysków do maszyny Wirtualnej używającej dysków premium niezarządzane. Zaleca się używać dysków zarządzanych Azure dla maszyn wirtualnych i przekonwertować poprzedniej dysków niezarządzanych do zarządzanych dysków. Zarządzane dojścia dysków podstawowych kont magazynu, nie trzeba. Aby uzyskać więcej informacji, zobacz nasze [omówienie dysków zarządzanych](../../virtual-machines/windows/managed-disks-overview.md).
>

Usługa Azure Premium Storage oferuje obsługę wysokiej wydajności i małych opóźnieniach dysku dla maszyn wirtualnych uruchomionych/O wykonujących obciążeń. Przeprowadź migrację aplikacji dysków maszyny Wirtualnej do usługi Azure Premium Storage może zająć wykorzystują szybkości i wydajności tych dysków.

Przeznaczenie tego przewodnika jest pomoc nowych użytkowników usługi Azure Premium Storage lepsze przygotowanie do płynne przejście z bieżącego systemu do magazynu w warstwie Premium. Przewodnik dotyczy trzy najważniejsze składniki w tym procesie:

* [Planowanie migracji do magazyn w warstwie Premium](#plan-the-migration-to-premium-storage)
* [Przygotowanie i skopiuj wirtualne dyski twarde (VHD) na magazyn w warstwie Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Utwórz maszynę wirtualną platformy Azure przy użyciu magazyn w warstwie Premium](#create-azure-virtual-machine-using-premium-storage)

Można dokonać migracji maszyn wirtualnych z innych platform do magazynu Azure Premium lub migracji istniejących maszyn wirtualnych platformy Azure z magazynu w warstwie standardowa do magazyn w warstwie Premium. W tym przewodniku opisano kroki dla obu dwa scenariusze. Wykonaj kroki określone w odpowiedniej sekcji w zależności od danego scenariusza.

> [!NOTE]
> Omówienie funkcji i cenowych Premium magazynu w warstwie Premium Storage można znaleźć: [magazynu o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](../../virtual-machines/windows/premium-storage.md). Zaleca się migrowanie dyskami maszyny wirtualnej wymagające wysoką wartość IOPS dla usługi Azure Premium Storage, aby uzyskać najlepszą wydajność aplikacji. Jeśli dysk nie wymaga wysokiej IOPS, przechowując go w Standard Storage, która przechowuje dane dysku maszyny wirtualnej na stacje dysków twardych (HDD) zamiast dysków SSD można ograniczyć koszty.
>

Kończenie procesu migracji, w całości może wymagać dodatkowych akcji przed i po zgodnie z krokami opisanymi w tym przewodniku. Przykładami konfigurowania sieci wirtualnych lub punktów końcowych lub zmiany kodu samej aplikacji, które mogą wymagać przestój w aplikacji. Te akcje są unikatowe dla każdej aplikacji i należy je ukończyć wraz z krokami opisanymi w tym przewodniku, aby wprowadzić pełną przejścia magazyn w warstwie Premium jako bezproblemowe, jak to możliwe.

## <a name="plan-the-migration-to-premium-storage"></a>Planowanie migracji do magazyn w warstwie Premium
W tej sekcji gwarantuje, że są gotowe do wykonania tych kroków migracji, w tym artykule i ułatwia podjęcie najlepszych decyzji w typach maszyny Wirtualnej i dysku.

### <a name="prerequisites"></a>Wymagania wstępne
* Konieczne będzie subskrypcji platformy Azure. Jeśli nie masz, możesz utworzyć jeden miesiąc [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) subskrypcji lub odwiedź stronę [cennik usługi Azure](https://azure.microsoft.com/pricing/) wyświetlić więcej opcji.
* Do wykonania polecenia cmdlet programu PowerShell, konieczne będzie modułu Microsoft Azure PowerShell. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Jeśli planujesz używać usługi Azure maszyn wirtualnych uruchomionych na magazyn w warstwie Premium, należy użyć maszyn wirtualnych obsługujących magazyn w warstwie Premium. Dyski w wersjach Standard i Premium Storage służy magazyn w warstwie Premium obsługuje maszyn wirtualnych. Dyski magazynu Premium będą dostępne z większą liczbę typów maszyny Wirtualnej w przyszłości. Aby uzyskać więcej informacji na wszystkich dostępnych typów dysku maszyny Wirtualnej platformy Azure i rozmiarów, zobacz [rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [rozmiary dla usług w chmurze](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Zagadnienia do rozważenia
Maszyny Wirtualnej platformy Azure obsługuje dołączanie kilka dysków Premium Storage, dzięki czemu aplikacje mogą mieć maksymalnie 256 TB pamięci masowej dla maszyny Wirtualnej. Z magazyn w warstwie Premium aplikacje można osiągnąć 80 000 IOPS (operacje wejścia/wyjścia na sekundę) dla maszyny Wirtualnej i 2000 MB na drugi przepływność dysku dla maszyny Wirtualnej z bardzo małych opóźnień operacji odczytu. Dostępne są opcje wiele maszyn wirtualnych i dysków. W tej sekcji jest ułatwiają znajdowanie opcję najlepiej pasującą do obciążenia.

#### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych
Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Przejrzyj charakterystyki wydajności maszyn wirtualnych, które pracy z magazyn w warstwie Premium i wybierz najbardziej odpowiedni rozmiar maszyny Wirtualnej, który najlepiej odpowiada obciążenie. Należy upewnić się, że wystarczającą przepustowość dostępne na maszynie Wirtualnej do kierowania ruchu dysku.

#### <a name="disk-sizes"></a>Rozmiary dysków
Istnieje pięć typów dysków, które mogą być używane z maszyny Wirtualnej, a każdy ma określonych IOPs i przepływność limity. Wziąć pod uwagę następujące limity gdy wybieranie typu dysku do maszyny Wirtualnej na podstawie potrzeb aplikacji pod względem wydajności, wydajności, skalowalności i ładuje godzinami szczytu.

| Typ dysków Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Rozmiar dysku           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Przepływność na dysk | 100 MB na sekundę | 150 MB na sekundę | 200 MB / s | 250 MB na sekundę | 250 MB na sekundę |

W zależności od obciążenia należy sprawdzić, czy dyski dodatkowe dane są niezbędne dla maszyny Wirtualnej. Możesz dołączyć kilka dysków danych trwałych do maszyny Wirtualnej. W razie potrzeby można paskowych na dyskach w celu zwiększenia pojemności i wydajności woluminu. (Zobacz, co rozkładanie [tutaj](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Jeśli paskowych magazyn w warstwie Premium dysków danych za pomocą [miejsca do magazynowania][4], należy ją skonfigurować z jedną kolumną dla każdego dysku, który jest używany. W przeciwnym razie ogólną wydajność woluminów rozłożonych może być niższa niż oczekiwano z powodu nierówna Dystrybucja ruchu na dyskach. W przypadku maszyn wirtualnych systemu Linux można użyć *mdadm* narzędzie w celu osiągnięcia takie same. Zobacz artykuł [skonfigurować RAID oprogramowania w systemie Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szczegółowe informacje.

#### <a name="storage-account-scalability-targets"></a>Wartości docelowe skalowalności konta magazynu
Konta Premium magazynu ma następujące wartości docelowe skalowalności oprócz [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md). Jeśli wymagań aplikacji może przekraczać wartości docelowe skalowalności konta magazynu pojedynczego, skompilować aplikację do korzystania z wielu kont magazynu i partycji danych przez tych kont magazynu.

| Konto łączna pojemność | Przepustowość konto magazyn lokalnie nadmiarowy |
|:--- |:--- |
| Pojemność dysku: 35TB<br />Migawki pojemności: 10 TB |Maksymalnie 50 GB na sekundę dla ruchu przychodzącego i wychodzącego |

Aby uzyskać więcej informacji dotyczących specyfikacji magazyn w warstwie Premium, zapoznaj się z [skalowalność i cele wydajności przy użyciu magazyn w warstwie Premium](../../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Dyskowej pamięci podręcznej zasad
Domyślnie jest dyskowej pamięci podręcznej zasad *tylko do odczytu* dla wszystkich danych dysków Premium i *odczytu i zapisu* dla dysku systemu operacyjnego Premium dołączony do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane w celu osiągnięcia optymalnej wydajności dla aplikacji systemu IOs. W przypadku dysków ciężki zapisu lub w trybie tylko do zapisu danych (takich jak pliki dziennika programu SQL Server) Wyłącz buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji. Ustawienia pamięci podręcznej dla istniejących dysków danych można zaktualizować przy użyciu [Azure Portal](https://portal.azure.com) lub *- HostCaching* parametr *AzureDataDisk zestaw* polecenia cmdlet.

#### <a name="location"></a>Lokalizacja
Wybierz lokalizację, gdzie dostępna jest usługa Azure Premium Storage. Zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/#services) aktualne informacje o dostępnych lokalizacji. Maszyny wirtualne znajdujące się w tym samym regionie co konto magazynu, że magazynów dysków dla maszyny Wirtualnej zapewni znacznie lepszą wydajność niż jeśli znajdują się w oddzielnych regionach.

#### <a name="other-azure-vm-configuration-settings"></a>Inne ustawienia konfiguracji maszyny Wirtualnej Azure
Podczas tworzenia maszyny Wirtualnej platformy Azure, użytkownik jest proszony o Konfigurowanie niektórych ustawień maszyny Wirtualnej. Należy pamiętać, że w ustaleniu ustawień kilka przez czas ich istnienia maszyny wirtualnej, podczas gdy można zmodyfikować lub dodać inne później. Przejrzyj te ustawienia konfiguracji maszyny Wirtualnej platformy Azure i upewnij się, że te są skonfigurowane odpowiednio dostosować do swoich potrzeb obciążenia.

### <a name="optimization"></a>Optymalizacja
[Usługa Azure Premium Storage: Projekt o wysokiej wydajności](../../virtual-machines/windows/premium-storage-performance.md) zawiera wskazówki dotyczące tworzenia aplikacji wysokiej wydajności za pomocą usługi Azure Premium Storage. Można użyć wskazówki łączyć się z najlepszymi rozwiązaniami wydajności mające zastosowanie do technologii używanych przez aplikację.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Przygotowanie i skopiuj wirtualne dyski twarde (VHD) na magazyn w warstwie Premium
Poniższa sekcja zawiera wskazówki dotyczące przygotowania wirtualne dyski twarde z maszyny Wirtualnej i kopiowania wirtualnych dysków twardych do magazynu Azure.

* [Scenariusz 1: "I am migracji istniejących maszyn wirtualnych platformy Azure do magazynu Azure Premium."](#scenario1)
* [Scenariusz 2: "I am Migrowanie maszyn wirtualnych z innych platform do magazynu Azure Premium."](#scenario2)

### <a name="prerequisites"></a>Wymagania wstępne
Aby przygotować dyski VHD do migracji, potrzebne są:

* Subskrypcja platformy Azure, konta magazynu i kontener na tym koncie magazynu, do którego można skopiować dysk VHD. Należy pamiętać, że konto magazynu docelowego może być kontem Standard lub Premium Storage, w zależności od wymagań.
* Narzędzie do uogólnienia wirtualny dysk twardy, jeśli zamierzasz utworzyć wiele wystąpień maszyn wirtualnych z niego. Na przykład, program sysprep dla systemu Windows lub programu sysprep virt dla Ubuntu.
* Narzędzie można przekazać pliku wirtualnego dysku twardego do konta magazynu. Zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md) lub użyj [Eksploratora usługi Azure storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Ten przewodnik zawiera opis kopiowanie dysk VHD za pomocą narzędzia AzCopy.

> [!NOTE]
> Jeśli wybierzesz opcję kopiowania synchroniczne z narzędzia AzCopy, aby uzyskać optymalną wydajność, skopiuj dysk VHD, uruchamiając jeden z tych narzędzi z maszyny Wirtualnej platformy Azure, który znajduje się w tym samym regionie co konto magazynu docelowego. Jeśli kopiujesz dysk VHD maszyny Wirtualnej platformy Azure w innym regionie, wydajność może przebiegać wolniej.
>
> W przypadku kopiowania dużych ilości danych w ograniczonej przepustowości, rozważ [na przesyłanie danych do magazynu obiektów Blob za pomocą usługi Import/Eksport Azure](../storage-import-export-service.md); dzięki temu można przenieść dane poprzez wysyłanie dysków twardych do centrum danych Azure. Usługa Import/Eksport Azure umożliwia kopiowanie danych na konto magazynu w warstwie standardowa tylko. Po zaimportowaniu danych na koncie magazynu w warstwie standardowa, możesz użyć dowolnej [kopiowania obiektu Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) lub AzCopy do transferu danych do swojego konta magazynu premium.
>
> Należy pamiętać, że Microsoft Azure obsługuje tylko pliki wirtualnego dysku twardego o stałym rozmiarze. Pliki VHDX lub dynamiczne wirtualne dyski twarde nie są obsługiwane. Jeśli masz dysk dynamiczny VHD można przekonwertować go na o ustalonym rozmiarze przy użyciu [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) polecenia cmdlet.
>
>

### <a name="scenario1"></a>Scenariusz 1: "I am migracji istniejących maszyn wirtualnych platformy Azure do magazynu Azure Premium."
W przypadku migracji istniejących maszyn wirtualnych platformy Azure, Zatrzymaj maszynę Wirtualną, przygotowanie dysków VHD na typ wirtualnego dysku twardego mają, a następnie skopiuj wirtualny dysk twardy z narzędzia AzCopy lub środowiska PowerShell.

Maszyna wirtualna musi być całkowicie w dół do migracji stanu czystego. Będzie przestoju dopiero po zakończeniu migracji.

#### <a name="step-1-prepare-vhds-for-migration"></a>Krok 1. Przygotowanie do migracji dysków VHD
W przypadku migracji istniejących maszyn wirtualnych platformy Azure do magazyn w warstwie Premium, może być dysk VHD:

* Obraz systemu operacyjnego ogólnych
* Dysk systemu operacyjnego
* Dysk z danymi

Poniżej opisano firma Microsoft za pośrednictwem tych scenariuszy 3 przygotowania dysk VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Umożliwia tworzenie wielu wystąpień maszyny Wirtualnej uogólniony wirtualny dysk twardy systemu operacyjnego
Podczas przekazywania wirtualnego dysku twardego używanego do utworzenia wielu wystąpień maszyny Wirtualnej Azure ogólny, należy najpierw generalize plik VHD za pomocą narzędzia sysprep. Dotyczy to do wirtualnego dysku twardego, który jest lokalnie lub w chmurze. Program Sysprep usuwa wszystkie informacje dotyczące komputera z dysku VHD.

> [!IMPORTANT]
> Utwórz migawkę lub kopii zapasowej maszyny Wirtualnej przed jej uogólnianie. Uruchomiony program sysprep będzie zatrzymać i cofnięcia przydzielenia wystąpienia maszyny Wirtualnej. Należy wykonać poniższe czynności, aby program sysprep wirtualnego dysku twardego systemu operacyjnego Windows. Należy pamiętać, że polecenia Sysprep będzie wymagać Zamknij maszynę wirtualną. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Omówienie narzędzia Sysprep](http://technet.microsoft.com/library/hh825209.aspx) lub [techniczne dotyczące narzędzia Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Otwórz okno wiersza polecenia z uprawnieniami administratora.
2. Wprowadź następujące polecenie, aby otworzyć narzędzie Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Narzędzie przygotowywania systemu wybierz System wprowadź Out-of-Box Experience (OOBE), zaznacz pole wyboru Generalize, wybierz **zamknięcia**, a następnie kliknij przycisk **OK**, jak pokazano na poniższej ilustracji. Narzędzie Sysprep uogólnienia systemu operacyjnego, a następnie zamknij system.

    ![][1]

Dla maszyny Wirtualnej systemu Ubuntu należy użyć narzędzia sysprep na virt do osiągnięcia takie same. Zobacz [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) więcej szczegółów. Zobacz też niektóre typu open source [rozbudowy serwerów Linux oprogramowania](http://www.cyberciti.biz/tips/server-provisioning-software.html) dla innych systemów operacyjnych Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Użyj wirtualnego dysku twardego systemu operacyjnego unikatowy, aby utworzyć jedno wystąpienie maszyny Wirtualnej
Jeśli masz aplikacji działających na maszynie Wirtualnej, która wymaga danych określonego komputera nie generalize wirtualnego dysku twardego. Uogólniony wirtualny dysk twardy może służyć do utworzenia unikatowego wystąpienia maszyny Wirtualnej platformy Azure. Na przykład jeśli kontroler domeny na dysk VHD, wykonywanie programu sysprep, spowoduje to nieefektywne jako kontroler domeny. Przejrzyj aplikacji uruchomionych na maszynie Wirtualnej i wpływ program sysprep na nich przed uogólnianie wirtualnego dysku twardego.

##### <a name="register-data-disk-vhd"></a>Zarejestruj dane dysku VHD
Jeśli masz dysków z danymi na platformie Azure do migracji, należy się upewnić się, że maszyny wirtualne korzystające z tych dysków danych są zamknięte.

Wykonaj kroki opisane poniżej, skopiuj wirtualny dysk twardy do usługi Azure Premium Storage i zarejestruj go jako dysk danych elastycznie.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Utwórz obiekt docelowy dysk VHD
Utwórz konto magazynu do przechowywania dyski VHD. Podczas planowania miejsca przechowywania dyski VHD, należy wziąć pod uwagę następujące kwestie:

* Element docelowy konta magazynu w warstwie Premium.
* Lokalizacja konta magazynu musi być taki sam jak magazyn w warstwie Premium obsługuje maszyny wirtualne Azure utworzysz w ostatnim etapie. Można skopiować do nowego konta magazynu lub planu, aby używać tego samego konta magazynu, na podstawie Twoich potrzeb.
* Skopiuj i Zapisz klucz konta magazynu docelowego konta magazynu do kolejnego etapu.

W przypadku dysków danych można zachować niektóre dyski danych w ramach konta magazynu w warstwie standardowa (na przykład dysków, które mają lodówki magazynu), ale zdecydowanie zalecamy przeniesienie wszystkich danych produkcyjnych obciążenie korzysta Magazyn w warstwie premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Krok 3. Skopiuj wirtualny dysk twardy z narzędzia AzCopy lub programu PowerShell
Należy znaleźć kontenera ścieżkę i magazynu klucz konta do przetwarzania jednej z tych dwóch opcji. Kontener ścieżki i przechowywania klucza konta znajdują się w **Azure Portal** > **magazynu**. Kontener adres URL będzie takie jak "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opcja 1: Skopiuj dysk VHD o AzCopy (asynchroniczne kopia)
Przy użyciu narzędzia AzCopy, można łatwo przekazanie dysku VHD w Internecie. W zależności od rozmiaru dysków VHD może to potrwać. Pamiętaj, aby sprawdzić wejście/wyjście limity konta magazynu w przypadku użycia tej opcji. Zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md) szczegółowe informacje.

1. Pobierz i zainstaluj narzędzie AzCopy stąd: [najnowszą wersję programu AzCopy](http://aka.ms/downloadazcopy)
2. Otwórz program Azure PowerShell i przejdź do folderu, w którym zainstalowano narzędzia AzCopy.
3. Użyj następującego polecenia, aby skopiować plik wirtualnego dysku twardego z "Source" do "Miejsce docelowe".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Przykład:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Poniżej przedstawiono opisy parametrów polecenia AzCopy:

   * **/ Źródło:  *&lt;źródła&gt;:***  lokalizację folderu lub adresu URL kontenera magazynu, która zawiera wirtualny dysk twardy.
   * **/ SourceKey:  *&lt;źródła konta klucza&gt;:***  klucz konta magazynu źródłowego konta magazynu.
   * **/ Dest:  *&lt;docelowego&gt;:***  adresu URL kontenera magazynu, aby skopiować dysk VHD do.
   * **/ DestKey:  *&lt;dest konta klucza&gt;:***  klucz konta magazynu docelowego konta magazynu.
   * **/ Wzorzec:  *&lt;nazwę pliku&gt;:***  Określ nazwę pliku wirtualnego dysku twardego do skopiowania.

Aby uzyskać więcej informacji na temat używania narzędzia AzCopy narzędzie, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opcja 2: Kopiować dysku VHD przy użyciu programu PowerShell (Synchronized kopia)
Możesz również skopiować plik VHD za pomocą polecenia cmdlet programu PowerShell Start-AzureStorageBlobCopy. Użyj następującego polecenia na programu Azure PowerShell, aby skopiować wirtualny dysk twardy. Zastąp wartości w <> odpowiednie wartości w ramach konta magazynu źródłowego i docelowego. Aby użyć tego polecenia, musi mieć kontener o nazwie wirtualne dyski twarde na koncie magazynu docelowego. Jeśli kontener nie istnieje, utwórz je przed uruchomieniem polecenia.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Przykład:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenariusz 2: "I am Migrowanie maszyn wirtualnych z innych platform do magazynu Azure Premium."
W przypadku migracji wirtualnego dysku twardego z innych niż - Azure magazynu w chmurze na platformie Azure, możesz wyeksportować wirtualnego dysku twardego do katalogu lokalnego. Ścieżka źródłowej pełną katalogu lokalnego wirtualnego dysku twardego przechowywania przydatną i przekaż go do usługi Azure Storage za pomocą narzędzia AzCopy.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Krok 1. Eksportowanie do katalogu lokalnego wirtualnego dysku twardego
##### <a name="copy-a-vhd-from-aws"></a>Skopiuj wirtualny dysk twardy z usług AWS
1. Jeśli używasz usług AWS wyeksportować wystąpienia EC2 do dysku VHD w zasobniku Amazon S3. Wykonaj kroki opisane w dokumentacji usługi Amazon eksportowanie Amazon EC2 wystąpień Zainstaluj narzędzie Amazon EC2 interfejsu wiersza polecenia (CLI) i uruchom polecenie eksportu zadań, Utwórz wystąpienie w — Aby wyeksportować wystąpienia EC2 do pliku dysku VHD. Należy użyć **wirtualnego dysku twardego** dysku&#95;obrazu&#95;FORMAT zmiennej podczas uruchamiania **tworzenie — wystąpienie export zadania** polecenia. Wyeksportowany plik wirtualnego dysku twardego jest zapisany w zasobniku Amazon S3, którą określisz podczas tego procesu.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Pobierz plik VHD z przedział S3. Wybierz plik dysku VHD, a następnie **akcje** > **Pobierz**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Skopiuj wirtualny dysk twardy z innych chmury Azure z systemem innym niż
W przypadku migracji wirtualnego dysku twardego z innych niż - Azure magazynu w chmurze na platformie Azure, możesz wyeksportować wirtualnego dysku twardego do katalogu lokalnego. Skopiuj ścieżkę źródłową pełny katalog lokalny, w którym przechowywana jest wirtualnego dysku twardego.

##### <a name="copy-a-vhd-from-on-premises"></a>Skopiuj wirtualny dysk twardy z lokalnymi
W przypadku migracji ze środowiska lokalnego wirtualnego dysku twardego, konieczne będzie ścieżki źródłowej pełną przechowywania wirtualnego dysku twardego. Ścieżka źródłowa może być serwerem lokalizacji lub w udziale plików.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Krok 2. Utwórz obiekt docelowy dysk VHD
Utwórz konto magazynu do przechowywania dyski VHD. Podczas planowania miejsca przechowywania dyski VHD, należy wziąć pod uwagę następujące kwestie:

* Docelowe konto magazynu może być standard lub premium magazynu w zależności od wymagań aplikacji.
* Region konta magazynu musi być taki sam jak magazyn w warstwie Premium obsługuje maszyny wirtualne Azure utworzysz w ostatnim etapie. Można skopiować do nowego konta magazynu lub planu, aby używać tego samego konta magazynu, na podstawie Twoich potrzeb.
* Skopiuj i Zapisz klucz konta magazynu docelowego konta magazynu do kolejnego etapu.

Ale zdecydowanie zalecamy przeniesienie wszystkich danych produkcyjnych obciążenie korzysta Magazyn w warstwie premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Krok 3. Przekazywanie wirtualnego dysku twardego do magazynu Azure
Dysk VHD jest już w katalogu lokalnym, można użyć narzędzia AzCopy lub AzurePowerShell można przekazać pliku VHD do magazynu Azure. Obie te opcje są dostępne w tym miejscu:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opcja 1: Przy użyciu usługi Azure PowerShell Add-AzureVhd przekazać plik VHD

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Przykład <Uri> może być ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Przykład <FileInfo> może być ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opcja 2: Przy użyciu narzędzia AzCopy można przekazać pliku VHD
Przy użyciu narzędzia AzCopy, można łatwo przekazanie dysku VHD w Internecie. W zależności od rozmiaru dysków VHD może to potrwać. Pamiętaj, aby sprawdzić wejście/wyjście limity konta magazynu w przypadku użycia tej opcji. Zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md) szczegółowe informacje.

1. Pobierz i zainstaluj narzędzie AzCopy stąd: [najnowszą wersję programu AzCopy](http://aka.ms/downloadazcopy)
2. Otwórz program Azure PowerShell i przejdź do folderu, w którym zainstalowano narzędzia AzCopy.
3. Użyj następującego polecenia, aby skopiować plik wirtualnego dysku twardego z "Source" do "Miejsce docelowe".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Przykład:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Poniżej przedstawiono opisy parametrów polecenia AzCopy:

   * **/ Źródło:  *&lt;źródła&gt;:***  lokalizację folderu lub adresu URL kontenera magazynu, która zawiera wirtualny dysk twardy.
   * **/ SourceKey:  *&lt;źródła konta klucza&gt;:***  klucz konta magazynu źródłowego konta magazynu.
   * **/ Dest:  *&lt;docelowego&gt;:***  adresu URL kontenera magazynu, aby skopiować dysk VHD do.
   * **/ DestKey:  *&lt;dest konta klucza&gt;:***  klucz konta magazynu docelowego konta magazynu.
   * **/ BlobType: strony:** Określa, że docelowy stronicowych obiektów blob.
   * **/ Wzorzec:  *&lt;nazwę pliku&gt;:***  Określ nazwę pliku wirtualnego dysku twardego do skopiowania.

Aby uzyskać więcej informacji na temat używania narzędzia AzCopy narzędzie, zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Inne opcje przekazywanie wirtualnego dysku twardego
Możesz również przekazywać dysku VHD do konta magazynu przy użyciu jednej z następujących sposobów:

* [Obiektu Blob magazynu Azure kopiowania interfejsu API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Obiekty BLOB magazynu Azure Explorer przekazywania](https://azurestorageexplorer.codeplex.com/)
* [Dokumentacja interfejsu API REST usługi Import/Eksport magazynu](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Zaleca się za pomocą usługi Import/eksport, jeśli szacowany czas przekazywania jest dłuższa niż 7 dni. Można użyć [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) oszacowanie godzinę z jednostki rozmiaru i transferu danych.
>
> Narzędzie importu/eksportu może służyć do skopiowania do konta magazynu w warstwie standardowa. Należy skopiować z magazynu w warstwie standardowa do konta magazynu premium za pomocą narzędzia, takiego jak narzędzie AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Tworzenie maszyn wirtualnych platformy Azure przy użyciu magazyn w warstwie Premium
Po wirtualny dysk twardy jest przekazany lub kopiowane do odpowiednie konto magazynu, postępuj zgodnie z instrukcjami w tej sekcji, aby zarejestrować wirtualnego dysku twardego jako obraz systemu operacyjnego lub dysku systemu operacyjnego, w zależności od scenariusza, a następnie utwórz wystąpienie maszyny Wirtualnej z niego. Dane dysku VHD może zostać dołączona do maszyny Wirtualnej, po jego utworzeniu.
Przykładowy skrypt migracji znajduje się na końcu tej sekcji. Ten prosty skrypt nie jest zgodna wszystkie scenariusze. Może być konieczne zaktualizowanie skryptu pasuje do konkretnego scenariusza. Aby sprawdzić, czy ten skrypt ma zastosowanie do danego scenariusza, zobacz poniżej [A przykładowy skrypt migracji](#a-sample-migration-script).

### <a name="checklist"></a>Lista kontrolna
1. Zaczekaj na wszystkie dyski VHD Kopiowanie zostało ukończone.
2. Upewnij się, że magazyn w warstwie Premium jest dostępna w regionie, który w przypadku migracji do.
3. Zdecyduj, nowej serii maszyny Wirtualnej, który będzie używany. Powinna być funkcją Magazyn w warstwie Premium, a rozmiar powinien być w zależności od dostępności w regionie i na podstawie Twoich potrzeb.
4. Zdecyduj, dokładne rozmiar maszyny Wirtualnej, które będą używane. Rozmiar maszyny Wirtualnej musi być wystarczająco duży, aby obsługiwał liczba dysków danych, do których masz. Na przykład Jeśli masz 4 dysków danych maszyny Wirtualnej musi mieć co najmniej 2 rdzeni. Należy również rozważyć przetwarzania zasilania, pamięci i musi przepustowości sieci.
5. Tworzenie konta Premium Storage w obszarze docelowym. Jest to konto, które będą używane dla nowej maszyny Wirtualnej.
6. Mieć bieżące szczegóły maszyny Wirtualnej pod ręką, w tym listę dysków i odpowiednie obiekty BLOB dysków VHD.

Przygotowanie aplikacji dla przestoju. Przeprowadzenie czystej migracji, należy zatrzymać wszystkie przetwarzania w systemie. Następnie możesz pobrać go do spójnego stanu, które można migrować do nowej platformie. Czas trwania przestoju zależy od ilości danych na dyskach, aby przeprowadzić migrację.

> [!NOTE]
> W przypadku tworzenia maszyny Wirtualnej platformy Azure Resource Manager z specjalne dysku VHD, zapoznaj się z [ten szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) wdrażania Menedżera zasobów maszyny Wirtualnej przy użyciu istniejącego dysku.
>
>

### <a name="register-your-vhd"></a>Zarejestruj dysk VHD
Aby utworzyć Maszynę wirtualną z wirtualnego dysku twardego systemu operacyjnego lub można dołączyć dysku danych do nowej maszyny Wirtualnej, należy je najpierw zarejestrować. Wykonaj poniższe kroki w zależności od scenariusza z wirtualnego dysku twardego.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony wirtualny dysk twardy systemu operacyjnego do utworzenia wielu wystąpień maszyny Wirtualnej Azure
Po obrazu systemu operacyjnego uogólniony wirtualny dysk twardy zostanie przekazany do konta magazynu, należy zarejestrować go w formie **obrazu maszyny Wirtualnej Azure** tak, aby z niego można utworzyć co najmniej jedno wystąpienie maszyny Wirtualnej. Aby zarejestrować dysk VHD jako obrazu systemu operacyjnego maszyny Wirtualnej Azure, użyj następujących poleceń cmdlet programu PowerShell. Podaj adres URL kontenera pełną, gdzie wirtualnego dysku twardego został skopiowany do.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Skopiuj i Zapisz nazwę tego nowego obrazu maszyny Wirtualnej Azure. W powyższym przykładzie jest *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowy wirtualnego dysku twardego systemu operacyjnego można utworzyć jedno wystąpienie maszyny Wirtualnej Azure
Po przekazaniu unikatowy wirtualnego dysku twardego systemu operacyjnego z kontem magazynu, zarejestruj je jako **dysk systemu operacyjnego Azure** tak, aby z niego można utworzyć wystąpienia maszyny Wirtualnej. Aby zarejestrować dysk VHD jako dysk systemu operacyjnego Azure, użyj tych poleceń cmdlet programu PowerShell. Podaj adres URL kontenera pełną, gdzie wirtualnego dysku twardego został skopiowany do.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Skopiuj i Zapisz nazwę tego nowego dysku systemu operacyjnego Azure. W powyższym przykładzie jest *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Dane na dysku VHD jest dołączony do nowego wystąpienia maszyny Wirtualnej Azure
Po przekazaniu danych dysk VHD do konta magazynu, zarejestruj go jako dysk danych Azure, dzięki czemu będzie można dołączyć do nowej serii DS, DSv2 serii lub wystąpienie maszyny Wirtualnej Azure serii GS.

Aby zarejestrować dysk VHD jako dysk danych Azure, użyj tych poleceń cmdlet programu PowerShell. Podaj adres URL kontenera pełną, gdzie wirtualnego dysku twardego został skopiowany do.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Skopiuj i Zapisz nazwę tego nowego dysku danych Azure. W powyższym przykładzie jest *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Tworzenie maszyny Wirtualnej obsługuje magazyn w warstwie Premium
Raz obrazu systemu operacyjnego lub dysku systemu operacyjnego są rejestrowane, tworzenie nowej serii DS, dsv2 lub GS-series maszyny Wirtualnej. Będziesz używać obrazu systemu operacyjnego lub nazwa dysku systemu operacyjnego, który został zarejestrowany. Wybierz typ maszyny Wirtualnej z warstwy Premium Storage. W poniższym przykładzie użyto *Standard_DS2* rozmiar maszyny Wirtualnej.

> [!NOTE]
> Zaktualizuj rozmiar dysku do upewnij się, że jest on zgodny wydajność i wymagania dotyczące wydajności i rozmiary dysku platformy Azure.
>
>

Wykonaj krok po kroku poleceń cmdlet programu PowerShell poniżej, aby utworzyć nową maszynę Wirtualną. Najpierw należy ustawić wspólne parametry:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Najpierw należy utworzyć usługi w chmurze w którym będą obsługiwać nowych maszyn wirtualnych.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Następnie w zależności od scenariusza, należy utworzyć wystąpienie maszyny Wirtualnej platformy Azure z obrazu systemu operacyjnego lub dysku systemu operacyjnego, który został zarejestrowany.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Uogólniony wirtualny dysk twardy systemu operacyjnego do utworzenia wielu wystąpień maszyny Wirtualnej Azure
Utwórz co najmniej jeden nowej maszyny Wirtualnej Azure serii DS wystąpienia przy użyciu **obrazu systemu operacyjnego Azure** który został zarejestrowany. Określ nazwę tego obrazu systemu operacyjnego w konfiguracji maszyny Wirtualnej, podczas tworzenia nowej maszyny Wirtualnej, jak pokazano poniżej.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unikatowy wirtualnego dysku twardego systemu operacyjnego można utworzyć jedno wystąpienie maszyny Wirtualnej Azure
Tworzenie nowego DS serii maszyny Wirtualnej Azure wystąpienia przy użyciu **dysk systemu operacyjnego Azure** który został zarejestrowany. Określ nazwę tego dysku systemu operacyjnego w konfiguracji maszyny Wirtualnej, podczas tworzenia nowej maszyny Wirtualnej, jak pokazano poniżej.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Określ inne informacje maszyny Wirtualnej platformy Azure, takich jak usługi w chmurze, region, konta magazynu, zestawu dostępności i zasad buforowania. Należy pamiętać, że wystąpienie maszyny Wirtualnej musi być wspólnie z skojarzonego z nim systemu operacyjnego lub dysków danych, wybrana chmura konto usługi, regionu i magazynu musi być w tej samej lokalizacji co podstawowych dysków VHD tych dysków.

### <a name="attach-data-disk"></a>Dołączanie dysku danych
Ponadto jeśli zarejestrowano danych dysku VHD, dołącz je do nowy magazyn w warstwie Premium obsługuje maszyny Wirtualnej Azure.

Skorzystaj z następujących polecenia cmdlet programu PowerShell dołączyć dysku danych do nowej maszyny Wirtualnej i określ zasad buforowania. W poniższym przykładzie ustawiono zasad buforowania *tylko do odczytu*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Mogą istnieć dodatkowe kroki niezbędne do obsługi tej aplikacji, która jest nie obejmuje się w tym przewodniku.
>
>

### <a name="checking-and-plan-backup"></a>Sprawdzanie i Planowanie tworzenia kopii zapasowej
Po nowa maszyna wirtualna jest uruchomiona, dostęp do niego przy użyciu tego samego identyfikatora logowania i hasła jest co oryginalna maszyna wirtualna i sprawdź, czy że wszystko działa zgodnie z oczekiwaniami. Wszystkie ustawienia, w tym woluminy rozłożone będą obecne w nowej maszyny Wirtualnej.

Ostatnim krokiem jest planowanie tworzenia kopii zapasowej i harmonogram konserwacji dla nowej maszyny Wirtualnej na podstawie potrzeb aplikacji.

### <a name="a-sample-migration-script"></a>Przykładowy skrypt migracji
Jeśli masz wiele maszyn wirtualnych do migracji automatyzacji za pomocą skryptów środowiska PowerShell będą przydatne. Oto przykładowy skrypt, który zautomatyzuje migracji maszyny wirtualnej. Uwaga poniżej skryptu jest tylko przykładem i istnieje kilka założeniom o bieżącym dysków maszyny Wirtualnej. Może być konieczne zaktualizowanie skryptu pasuje do konkretnego scenariusza.

Założeń są:

* Tworzysz klasycznych maszyn wirtualnych platformy Azure.
* Źródła dysków systemu operacyjnego i dysków danych źródłowych znajdują się w tym samym koncie magazynu i tego samego kontenera. Dysków systemu operacyjnego i dysków z danymi nie są w tym samym miejscu, aby skopiować wirtualne dyski twarde za pośrednictwem konta magazynu i kontenery można użyć narzędzia AzCopy lub Azure PowerShell. Można znaleźć w poprzednim kroku: [kopii wirtualnego dysku twardego z narzędzia AzCopy lub środowiska PowerShell](#copy-vhd-with-azcopy-or-powershell). Edytowanie tego skryptu w celu spełnienia danego scenariusza innym rozwiązaniem jest, ale zaleca się użycie narzędzia AzCopy lub programu PowerShell, ponieważ jest łatwiejsze i szybsze.

Skrypt automatyzacji podano poniżej. Zastąp tekst z informacjami i zaktualizować ten skrypt, aby dopasować z konkretnego scenariusza.

> [!NOTE]
> Przy użyciu istniejącego skryptu nie zachowa konfiguracji sieci źródła maszyny Wirtualnej. Konieczne będzie ponowna konfiguracja ustawień sieciowych na zmigrowanych maszyn wirtualnych.
>
>

```
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
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

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

    # how frequently to report the copy status in sceconds
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
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
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
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
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

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
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
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
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
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
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
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
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
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
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

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optymalizacja
Bieżąca konfiguracja maszyny Wirtualnej można dostosować w szczególności do pracy ze standardowych dysków. Na przykład aby zwiększyć wydajność przy użyciu wielu dysków w woluminy rozłożone. Na przykład zamiast 4 dyski osobno na magazyn w warstwie Premium, można zoptymalizować koszt przez jednego dysku. Optymalizacje takie jak konieczność obsługi na podstawie przypadku i wymagają niestandardowej czynności po migracji. Należy również zauważyć, że ten proces może nie również działać dla baz danych i aplikacji, które są zależne od zdefiniowany w ustawieniach układu dysku.

##### <a name="preparation"></a>Przygotowanie
1. Wykonaj proste migracji, zgodnie z opisem w we wcześniejszej sekcji. Optymalizacje odbędzie się w nowej maszyny Wirtualnej po migracji.
2. Zdefiniuj nowe rozmiary dysku wymagane do konfiguracji zoptymalizowane.
3. Należy określić mapowanie bieżącego dysków/woluminów do specyfikacji nowego dysku.

##### <a name="execution-steps"></a>Wykonanie czynności
1. Utwórz nowe dyski o odpowiednim rozmiarze na Maszynie wirtualnej — wersja Premium magazynu.
2. Zaloguj się do maszyny Wirtualnej i kopiowania danych z bieżącego woluminu na nowy dysk, który jest mapowany na tym woluminie. W tym wszystkie woluminy bieżącego, które należy mapować na nowy dysk.
3. Następnie zmienić ustawienia aplikacji, aby przełączyć się do nowych dysków i odłączyć starszych woluminów.

Dostrajanie aplikacji w celu zapewnienia lepszej wydajności dysku, można znaleźć na stronie [optymalizacji wydajności aplikacji](../../virtual-machines/windows/premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migracja aplikacji
Baz danych i innych złożonych aplikacji może wymagać specjalne kroki zgodnie z definicją w dostawcy aplikacji do migracji. Zapoznaj się z dokumentacją odpowiedniej aplikacji. Na przykład zwykle baz danych można poddać migracji za pomocą kopii zapasowej i przywracania.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące zasoby w określonych scenariuszach migracji maszyn wirtualnych:

* [Migrowanie maszyn wirtualnych platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Tworzenie i przekazywanie wirtualnego dysku twardego serwera Windows Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrowanie maszyn wirtualnych z Amazon usług AWS na platformie Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zobacz też następujące zasoby, aby dowiedzieć się więcej na temat usługi Azure Storage i maszyn wirtualnych platformy Azure:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure](../../virtual-machines/windows/premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
