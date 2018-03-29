# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Często zadawane pytania dotyczące dyski maszyny Wirtualnej Azure IaaS i zarządzane i niezarządzane — wersja premium

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące dysków zarządzanych Azure i usługa Azure Premium Storage.

## <a name="managed-disks"></a>Managed Disks

**Co to jest Azure zarządzane dyski?**

Dyski zarządzane jest funkcja, która ułatwia zarządzanie dyskami dla maszyn wirtualnych IaaS platformy Azure dzięki obsłudze Zarządzanie kontem magazynu dla Ciebie. Aby uzyskać więcej informacji, zobacz [omówienie dysków zarządzanych](../articles/virtual-machines/windows/managed-disks-overview.md).

**Jeśli utworzyć standardowych dysków zarządzanych z istniejącego dysku VHD, 80 GB, ile będzie tego koszt mnie?**

Standardowa dysków zarządzanych utworzone na podstawie wirtualny dysk twardy 80 GB jest traktowany jako dalej rozmiar dostępnych dysków w warstwie standardowa, który jest dyskiem s10 w warstwie. Są naliczane opłaty zgodnie z s10 w warstwie cenowej dysku. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Czy istnieją kosztów transakcji dla standardowych dysków zarządzanych?**

Tak. W przypadku naliczona opłata za każdą transakcję. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Dla standardowych dysków zarządzanych I obciążymy rzeczywisty rozmiar danych na dysku lub elastycznie pojemność dysku?**

Są naliczane opłaty oparte na elastycznie pojemność dysku. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Jak jest inny niż dyski niezarządzane cennik dysków zarządzanych w warstwie premium?**

Cennik dysków zarządzanych w warstwie premium jest taka sama jak dyski premium niezarządzane.

**Można zmienić typu (standardowy lub Premium) konta magazynu z dysków zarządzanych?**

Tak. Można zmienić typu konta magazynu dysków zarządzanych za pomocą portalu Azure, programu PowerShell lub wiersza polecenia platformy Azure.

**Czy istnieje sposób, aby I skopiuj lub wyeksportować dysków zarządzanych do konta magazynu prywatnej?**

Tak. Dyski zarządzane można wyeksportować za pomocą portalu Azure, programu PowerShell lub wiersza polecenia platformy Azure.

**Aby utworzyć dysków zarządzanych za pomocą innej subskrypcji można użyć pliku VHD na koncie magazynu platformy Azure?**

Nie.

**Aby utworzyć dysków zarządzanych w innym regionie można użyć pliku VHD na koncie magazynu platformy Azure?**

Nie.

**Czy istnieją jakiekolwiek ograniczenia skali dla klientów korzystających z zarządzanego dyski?**

Dyski zarządzane eliminuje limity skojarzonego z kontami magazynu. Jednak maksymalny limit, a domyślny limit, jest 10 000 dysków zarządzanych według regionu i typ dysku dla subskrypcji.

**Czy można wykonać przyrostowej migawki dysków zarządzanych?**

Nie. Pełną kopię dysków zarządzanych sprawia, że bieżąca funkcja migawki. Jednak firma Microsoft planuje obsługuje przyrostowe migawek w przyszłości.

**Maszyny wirtualne w zestawie dostępności może zawierać kombinację dysków zarządzane i niezarządzane?**

Nie. Maszyn wirtualnych w zestawie dostępności muszą używać wszystkich zarządzanych dysków lub wszystkie dyski niezarządzane. Podczas tworzenia zestawu dostępności można wybrać typu dysków, którego chcesz użyć.

**Jest domyślną opcją w portalu Azure zarządzane dyski?**

Tak. 

**Można utworzyć pusty dysk zarządzany?**

Tak. Możesz utworzyć pusty dysk. Dysk zarządzany można tworzyć niezależnie od maszyny Wirtualnej, na przykład bez dołączeniu go do maszyny Wirtualnej.

**Co to liczba domen błędów obsługiwanych dostępności ustawiono używającym dysków zarządzanych?**

W zależności od regionu, w którym znajduje się zestaw dostępności, który używa dysków zarządzanych liczba domen błędów obsługiwanych jest 2 lub 3.

**Jak jest to konto magazynu w warstwie standardowa dla diagnostyki Konfigurowanie?**

Skonfiguruj konto magazynu prywatne dla diagnostyki maszyny Wirtualnej. W przyszłości firma Microsoft planuje także przełącznik diagnostyki do zarządzanych dysków.

**Jakiego rodzaju obsługi kontroli dostępu opartej na rolach jest dostępna dla dysków zarządzanych?**

Zarządzane dysków obsługuje trzy kluczowe domyślne role:

* Właściciel: Mogą zarządzać wszystkim łącznie z dostępem
* Współautor: Mogą zarządzać wszystkim poza dostępem
* Czytnik: Można przeglądać wszystko, ale nie można wprowadzić zmian

**Czy istnieje sposób, aby I skopiuj lub wyeksportować dysków zarządzanych do konta magazynu prywatnej?**

Można pobrać sygnatury dostępu współdzielonego tylko do odczytu identyfikatora URI dla dysków zarządzanych i go użyć do kopiowania zawartości do magazynu konta lub lokalnego magazynu prywatnych.

**Można utworzyć kopię dysku zarządzanego?**

Klienci mogą migawki dysków zarządzanych, a następnie użyj migawki do tworzenia dysków zarządzanych w innym.

**Niezarządzane dyski nadal są obsługiwane?**

Tak. Firma Microsoft obsługuje dyski niezarządzane i zarządzane. Zalecamy dysków zarządzanych dla nowych obciążeń, a migracja bieżącego obciążeń do zarządzanych dysków.


**Jeśli I Utwórz dysk 128 GB i dopiero potem zwiększyć rozmiar 130 GB I obciążymy dla następnego rozmiar dysku (512 GB)?**

Tak.

**Magazyn lokalnie nadmiarowy, Magazyn geograficznie nadmiarowy, można utworzyć i dyskach zarządzanych przez Magazyn strefowo nadmiarowy?**

Dyskach zarządzanych platformy Azure obsługuje obecnie tylko lokalnie nadmiarowego magazynu zarządzane dyski.

**Można zmniejszyć lub downsize dysków zarządzanych?**

Nie. Ta funkcja nie jest obecnie obsługiwana. 

**Dzierżawy mogą być dzielone na tym dysku?**

Nie. Nie jest to obsługiwane obecnie dzierżawy jest obecny zapobiega przypadkowemu usunięciu, gdy dysk jest używany.

**Właściwość Nazwa komputera można zmienić po specjalistycznej (nie utworzone przy użyciu narzędzia przygotowywania systemu lub uogólniony) dysku systemu operacyjnego jest używany do udostępnienia maszyny Wirtualnej?**

Nie. Nie można zaktualizować właściwości Nazwa komputera. Nowa maszyna wirtualna dziedziczy z elementu nadrzędnego maszyny Wirtualnej, który został użyty do utworzenia dysku systemu operacyjnego. 

**Gdzie można znaleźć przykładowych szablonów usługi Azure Resource Manager do tworzenia maszyn wirtualnych z dyskami zarządzanych**
* [Lista szablonów przy użyciu dysków zarządzanych](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="migrate-to-managed-disks"></a>Migrowanie do usługi Managed Disks 

**Jakie zmiany są wymagane w przypadku istniejącego kopia zapasowa Azure usługi konfiguracji przed lub po migracji do zarządzanych dysków?**

Zmiany nie są wymagane. 

**Kopiach zapasowych maszyny Wirtualnej utworzone za pomocą usługi Azure Backup przed migracją będzie działać?**

Tak, kopie zapasowe działają bezproblemowo.

**Jakie zmiany są wymagane w przypadku istniejącego szyfrowania dysków Azure konfiguracji przed lub po migracji do zarządzanych dysków?**

Zmiany nie są wymagane. 

**Jest automatycznej migracji z istniejącej maszyny Wirtualnej skali zestawów (VMSS) z dysków niezarządzanych do zarządzanych dysków obsługiwane?**

Nie. Można utworzyć nowego VMSS z dyskami zarządzane przy użyciu obrazu z Twojego starego VMSS z dyskami niezarządzane. 

**Czy można utworzyć dysku zarządzanego z migawką obiektu blob strony przed migracją do zarządzanych dysków**

Nie. Można wyeksportować migawki obiektu blob strony w postaci stronicowych obiektów blob i następnie utwórz dysk zarządzane z wyeksportowanego stronicowych obiektów blob. 

**Można nie za pośrednictwem Moje maszyny lokalnej chronione przez usługę Azure Site Recovery do maszyny Wirtualnej z dyskami zarządzane?**

Tak, możesz przełączyć się awaryjnie na maszynę Wirtualną za pomocą zarządzania dyskami.

**Znajduje wszystkie wpływ migracji na maszynach wirtualnych Azure chronione przez odzyskiwania lokacji Azure (ASR) za pomocą replikacji Azure do platformy Azure?**

Tak. Obecnie funkcja automatycznego odzyskiwania systemu Azure do platformy Azure ochrony dla maszyn wirtualnych z dyskami zarządzane jest dostępne tylko jako usługa publicznej wersji zapoznawczej.

**Czy można migrować maszyny wirtualne z dyskami niezarządzane, które znajdują się na kontach magazynu, które są lub wcześniej były szyfrowane do zarządzanych dysków**

Yes

## <a name="managed-disks-and-storage-service-encryption"></a>Zarządzane dysków i szyfrowanie usługi magazynu 

**Jest szyfrowanie usługi Magazyn Azure domyślnie podczas tworzenia dysków zarządzanych?**

Tak.

**Kto zarządza klucze szyfrowania?**

Firma Microsoft zarządza kluczy szyfrowania.

**Do zarządzanych dysków można wyłączyć szyfrowanie usługi Magazyn?**

Nie.

**Jest szyfrowanie usługi Magazyn dostępna tylko w określonych regionach?**

Nie. Jest dostępna we wszystkich regionach, gdzie dostępna jest opcja dysków zarządzanych. Dyski zarządzane jest dostępna we wszystkich regionach publicznego i Niemczech.

**Jak można sprawdzić w przypadku zarządzanych dysku są szyfrowane?**

Można ustalić czas utworzenia dysków zarządzanych w portalu Azure, interfejsu wiersza polecenia Azure i programu PowerShell. Gdy czas po 9 czerwca 2017 dysku są szyfrowane. 

**Jak można zaszyfrować Moje istniejących dysków, które zostały utworzone przed 10 czerwca 2017 r.**

Począwszy od 10 czerwca 2017 nowych danych istniejących dysków zarządzanych jest szyfrowane automatycznie. Możemy również planowania szyfrowania istniejących danych i szyfrowanie nastąpi asynchronicznie w tle. Jeśli musi teraz zaszyfrować dane, należy utworzyć kopię dysku. Nowe dyski zostaną zaszyfrowane.

* [Kopiowanie dysków zarządzanych przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopiowanie dysków zarządzanych za pomocą programu PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Są zarządzane migawki i obrazy szyfrowane?**

Tak. Wszystkie zarządzane migawki i obrazy utworzone po 9 czerwca 2017 r są szyfrowane automatycznie. 

**Czy mogę przekonwertować maszyny wirtualne z dyskami niezarządzane, które znajdują się na kontach magazynu, które są lub wcześniej były szyfrowane do zarządzanych dysków**

Yes

**Wyeksportowane wirtualnego dysku twardego z zarządzanego dysku lub migawka także będą zaszyfrowane?**

Nie. Ale jeśli możesz wyeksportować do konta magazynu zaszyfrowanego dysku VHD z zaszyfrowanego zarządzane dysku lub migawki, a następnie jest on zaszyfrowany. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dyski Premium: zarządzanych i niezarządzanych

**Jeśli maszyna wirtualna używa rozmiar serii, która obsługuje magazyn w warstwie Premium, takich jak DSv2, można dołączyć zarówno premium i dyski standardowe danych?** 

Tak.

**Do rozmiaru serii, która nie obsługuje usługi Premium Storage, takich jak seria D, Dv2, G lub F można podłączyć zarówno premium i dyski standardowe danych?**

Nie. Tylko dyski standardowe danych można dołączyć do maszyn wirtualnych, które nie używają serii rozmiar, który obsługuje magazyn w warstwie Premium.

**Jeśli dysk danych — warstwa premium można utworzyć z istniejącego dysku VHD, który był 80 GB, ile będzie tego koszt?**

Dysk z danymi premium utworzone na podstawie wirtualny dysk twardy 80 GB jest traktowany jako rozmiaru dysku premium dostępne dalej, który jest dyskiem P10. Są naliczane opłaty zgodnie z cennik P10 dysku.

**Czy istnieją koszty transakcji do użycia magazyn w warstwie Premium?**

Brak koszt stały rozmiar każdego dysku, które pojawia się z określonym limity udostępnionym IOPS i przepustowość. Inne koszty są przepustowości wychodzącej i pojemność migawki, jeśli ma to zastosowanie. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage).

**Jakie są limity dla IOPS i przepływność, którą można pobrać z pamięci podręcznej dysku?**

Łączne limity dla pamięci podręcznej i lokalny dysk SSD dla serii DS są 4000 IOPS na podstawowe i 33 MB na sekundę na podstawowe. Serii GS oferuje 5000 IOPS na podstawowe i 50 MB na sekundę na podstawowe.

**Lokalny dysk SSD jest obsługiwana dla maszyny Wirtualnej, zarządzane dyski?**

Lokalny dysk SSD jest tymczasowego magazynu, który jest dołączony do maszyny Wirtualnej dysków zarządzanych. Jest nie żadnymi dodatkowymi kosztami dla tego magazynu tymczasowego. Firma Microsoft zaleca, aby używać tego lokalny dysk SSD do przechowywania danych aplikacji, ponieważ nie jest on utrwalane w magazynie obiektów Blob platformy Azure.

**Czy istnieją żadnych skutków PRZYCINANIE do użytku na dyski premium?**

Nie ma żadnych wadą interfejsu użyciem PRZYCINANIE na dyskach platformy Azure w warstwie premium albo lub dyski standardowe.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nowy rozmiar dysku: zarządzanych i niezarządzanych

**Co to jest największy rozmiar dysku systemu operacyjnego i dysków z danymi obsługiwane?**

Typ partycji, który Azure obsługuje dla dysku systemu operacyjnego jest główny rekord rozruchowy (MBR). Obsługuje format MBR dysk rozmiar do 2 TB. Największy rozmiar, który Azure obsługuje dla dysku systemu operacyjnego jest 2 TB. Azure obsługuje do 4 TB dla dysków z danymi. 

**Co to jest największy rozmiar strony obiektu blob jest obsługiwana?**

Największy rozmiar strony obiektu blob Azure obsługuje jest 8 TB (8191 GB). Nie obsługujemy stronicowe obiekty BLOB większych niż 4 TB (4,095 GB) dołączona do maszyny Wirtualnej jako dane lub dysków systemu operacyjnego.

**Należy użyć nowej wersji narzędzi platformy Azure do tworzenia, Dołącz, zmienianie rozmiaru i przekaż dysków większych niż 1 TB?**

Nie trzeba uaktualnić Azure istniejących narzędzi do tworzenia, Dołącz lub zmieniać rozmiar dysków większych niż 1 TB. Aby przesłać plik wirtualnego dysku twardego z lokalnymi bezpośrednio na platformie Azure jako stronicowy obiekt blob lub niezarządzane dysku, należy użyć najnowszej zestawów narzędzia:

|Narzędzia platformy Azure      | Obsługiwane wersje                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numer wersji 4.1.0: czerwiec 2017 wersji lub nowszy|
|Interfejs wiersza polecenia platformy Azure w wersji 1     | Numer wersji 0.10.13: 2017 może zwolnić lub nowszy|
|Narzędzie AzCopy           | Numer wersji 6.1.0: czerwiec 2017 wersji lub nowszy|

Obsługa interfejsu wiersza polecenia Azure w wersji 2 i Eksploratora usługi Storage platformy Azure będzie dostępna wkrótce. 

**P4 i P6 rozmiary dysków są obsługiwane dla niezarządzanego dysków lub stronicowych obiektów blob?**

Nie. P4 (32 GB) i P6 rozmiary dysków (64 GB) są obsługiwane tylko w przypadku dysków zarządzanych. Obsługa dysków niezarządzane i stronicowe obiekty BLOB będzie dostępna wkrótce.

**Jeśli Mój istniejący premium zarządzane na dysku z mniej niż 64 GB został utworzony przed włączeniem małego dysku (około 15 czerwca 2017 r), jak jest on rozliczany?**

Istniejące premium małe dyski mniej niż 64 GB nadal będą naliczane opłaty zgodnie z warstwy cenowej P10. 

**Jak można zmienić warstwy dysków premium małe dyski, mniejsze niż 64 GB z P10 P4 lub P6?**

Można utworzyć migawkę małe dyski, a następnie utwórz dysk, aby automatycznie Zmień warstwę cenową P4 lub P6 zależnie od rozmiaru elastycznie. 


## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli mojego pytania nie ma odpowiedzi w tym miejscu?

Jeśli Twoje pytanie nie ma na liście w tym miejscu, Daj nam znać, a pomożemy Ci znaleźć odpowiedź. W komentarzach można Zadaj pytanie na końcu tego artykułu. Aby współpracować z zespołu usługi Magazyn Azure i innymi członkami społeczności informacje w tym artykule, należy użyć MSDN [forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Aby poprosić o funkcje, przesłać żądania i pomysłami, które [forum opinii usługi Azure Storage](https://feedback.azure.com/forums/217298-storage).
