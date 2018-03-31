# <a name="azure-managed-disks-overview"></a>Informacje o dyskach zarządzanych platformy Azure

Zarządzane dysku systemu Azure upraszcza zarządzanie dysku dla maszyn wirtualnych IaaS platformy Azure przez zarządzanie [kont magazynu](../articles/storage/common/storage-introduction.md) skojarzone z dysków maszyny Wirtualnej. Należy określić typ ([Premium](../articles/virtual-machines/windows/premium-storage.md) lub [standardowe](../articles/virtual-machines/windows/standard-storage.md)) należy rozmiar dysku i Azure tworzy i zarządza dysku.

## <a name="benefits-of-managed-disks"></a>Korzyści wynikające z dysków zarządzanych

Spójrzmy na kilka korzyści, uzyskasz przy użyciu dysków zarządzanych w programie ten film Channel 9 [lepsze odporności maszyny Wirtualnej Azure z dyskami zarządzane](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Proste i skalowalne wdrażanie maszyny Wirtualnej

Zarządzane dyski magazynu uchwytów dla Ciebie w tle. Poprzednio konieczne było utworzenie kont magazynu do przechowywania dysków (pliki VHD) na maszynach wirtualnych platformy Azure. Podczas skalowania w, trzeba było upewnij się, że utworzona dodatkowych kont magazynu, więc nie przekracza limitu IOPS dla magazynu za pomocą dowolnego z dysków. W przypadku zarządzanych dysków obsługi magazynu nie jest już ograniczeniem limity konta magazynu (takie jak IOPS 20 000 / konta). Masz już również skopiować niestandardowe obrazy (pliki VHD) na wielu kont magazynu. Można nimi zarządzać w centralnej lokalizacji — jedno konto magazynu na region platformy Azure — i używać ich do tworzenia setki maszyn wirtualnych w ramach subskrypcji.

Dyski zarządzanych umożliwia tworzenie maszyny Wirtualnej do 10 000 **dysków** w ramach subskrypcji, która umożliwi tysiące **maszyn wirtualnych** w ramach jednej subskrypcji. Ta funkcja także dodatkowo zwiększa skalowalność [zestawy skalowania maszyny wirtualnej (VMSS)](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , umożliwiając tworzenie maszyn wirtualnych do tysięcy w VMSS, przy użyciu obrazu z witryny Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Niezawodność zestawów dostępności

Dyski zarządzane zapewnia większą niezawodność zestawów dostępności przez zapewnienie, że dyski [maszyn wirtualnych w zestawie dostępności](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) wystarczająco izolowane od siebie, aby uniknąć pojedynczych punktów awarii. Robi to automatycznie umieszczając dyski w jednostki skalowania innego magazynu (sygnatury). Sygnatura nie powiedzie się z powodu awarii sprzętu lub oprogramowania, tylko wystąpienia maszyny Wirtualnej z dyskami tych sygnatur zakończyć się niepowodzeniem. Na przykład, załóżmy, że korzystasz z aplikacji działających na pięciu maszynach, a maszyny wirtualne znajdują się w zestawie dostępności. Dyski dla tych maszyn wirtualnych nie będą wszystkie są przechowywane w tej samej sygnaturze, więc jeśli jednej sygnatury ulegnie awarii, pozostałe wystąpienia aplikacji kontynuować działanie.

### <a name="highly-durable-and-available"></a>Duża trwałość i wysoka dostępność

Dyski platformy Azure zaprojektowano tak, aby zapewniały 99,999% dostępności. Zatrzymaj, łatwiej wiedząc, że użytkownik ma trzy repliki danych, która umożliwia wysoka trwałość. Jeśli w jednej lub nawet w dwóch replikach wystąpią błędy, pozostałe repliki pomogą w zapewnieniu trwałości danych i dużej tolerancji w przypadku awarii. Ta architektura pomogła platformie Azure w zapewnieniu niezawodności klasy korporacyjnej dla dysków IaaS przez długi czas z rocznym współczynnikiem awarii w wysokości 0%, co stawia ją w czołówce branży. 

### <a name="granular-access-control"></a>Precyzyjną kontrolę dostępu

Można użyć [based kontroli dostępu (RBAC)](../articles/active-directory/role-based-access-control-what-is.md) Aby przypisać uprawnienia określone dla dysków zarządzanych do co najmniej jednego użytkownika. Zarządzane dyski ujawnia różne operacje, w tym do odczytu, zapisu (Utwórz/Aktualizuj), usuwania i pobierania [sygnatury dostępu współdzielonego (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) dla dysku. Można przyznać dostęp do działań osoby musi wykonać swoje zadania. Na przykład jeśli nie chcesz, aby osoby w celu kopiowania dysków zarządzanych na konto magazynu, możesz nie udzielić dostępu do akcji eksportu dla tego dysku zarządzanego. Podobnie jeśli nie chcesz, aby osoby na potrzeby kopiowania dysków zarządzanych przez identyfikator URI sygnatury dostępu Współdzielonego, można nie przyznać uprawnienie do dysków zarządzanych.

### <a name="azure-backup-service-support"></a>Obsługa usługi Kopia zapasowa Azure
Tworzenie zadania tworzenia kopii zapasowej na podstawie czasu tworzenia kopii zapasowych, łatwe przywrócenie maszyny Wirtualnej i zasady przechowywania kopii zapasowych za pomocą usługi Kopia zapasowa Azure z zarządzania dyskami. Dyski zarządzane obsługują tylko lokalnie nadmiarowego magazynu (LRS) jako opcję replikacji; oznacza to, że przechowuje trzy kopie danych w pojedynczym regionie. Regionalnej awarii, należy wykonać kopię zapasową dysków maszyny Wirtualnej w innym regionie przy użyciu [usługi Kopia zapasowa Azure](../articles/backup/backup-introduction-to-azure-backup.md) i konto magazynu GRS jako magazynu kopii zapasowych. Obecnie dysk danych w usłudze Kopia zapasowa Azure obsługuje rozmiar maksymalnie 1TB dla kopii zapasowej. Dowiedz się więcej o tym w [usługi przy użyciu kopii zapasowej Azure dla maszyn wirtualnych z dyskami zarządzane](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Korzystając z dysków zarządzanych, zastosuj następujące zagadnienia dotyczące rozliczeń:
* Typ magazynu

* Rozmiar dysku

* Liczba transakcji

* Wychodzące transfery danych

* Zarządzane migawki dysków (kopia zapełniony dysk)

Spójrzmy bliższe spojrzenie na te.

**Typ magazynu:** zarządzane dysków oferuje 2 warstwy wydajności: [Premium](../articles/virtual-machines/windows/premium-storage.md) (oparte na dysk SSD) i [standardowe](../articles/virtual-machines/windows/standard-storage.md) (oparte na dysk twardy). Rozliczenia dysków zarządzanych zależy od tego, jakiego typu magazynu wybranego dysku.


**Rozmiar dysku**: rozliczeń dla dysków zarządzanych zależy od elastycznie rozmiaru dysku. Azure mapuje elastycznie rozmiar (zaokrąglona w górę) do najbliższej opcja dysków zarządzanych w określonych w poniższych tabelach. Każdy dysk zarządzany mapuje jedną z obsługiwanych rozmiarów elastycznie i jest on rozliczany odpowiednio. Na przykład jeśli tworzenie standardowych dysków zarządzanych i określ elastycznie rozmiaru 200 GB, są rozliczane zgodnie z harmonogramem cennik typ dysku S20 w warstwie.

W tym miejscu są dostępne dla dysków zarządzanych w warstwie premium rozmiary dysków:

| **Premium zarządzane <br>typ dysku** | **P4** | **P6** |**P10** | **P15** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Rozmiar dysku        | 32 GiB   | 64 GiB   | 128 GiB  | 256 GiB  | 512 GiB  | 1024 GiB (1 TiB) | GiB 2048 (2 TiB) | GiB 4095 (4 TiB) | 


W tym miejscu są dostępne dla standardowych dysków zarządzanych rozmiary dysków:

| **Standard zarządzane <br>typ dysku** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| Rozmiar dysku        | 32 GiB   | 64 GiB   | 128 GiB | 512 GiB | 1024 GiB (1 TiB) | GiB 2048 (2 TiB) | GiB 4095 (4 TiB) | 


**Liczba transakcji**: rozliczenie jest liczba transakcji, które można wykonywać na standardowych dysków zarządzanych. Nie ma żadnych kosztów transakcji dla dysków zarządzanych w warstwie premium.

**Transfer danych wychodzących**: [transfery danych wychodzących](https://azure.microsoft.com/pricing/details/data-transfers/) (danych wychodzących z centrów danych Azure) powodują Naliczanie opłat za zużycie przepustowości.

Aby uzyskać szczegółowe informacje o cenach dla dysków zarządzanych, zobacz [zarządzane cennik dysków](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Dyski zarządzane migawki

Migawka zarządzanych jest tylko do odczytu pełnej kopii dysku zarządzanego, który jest przechowywany jako standardowych dysków zarządzanych przez domyślny. Z migawki można tworzyć kopie zapasowe dysków zarządzanych w dowolnym momencie w czasie. Te migawki istnieje niezależnie od dysku źródłowego i mogą służyć do tworzenia nowych dysków zarządzanych. Są one rozliczane na podstawie rozmiaru używane. Na przykład po utworzeniu migawki dysków zarządzanych z elastycznie pojemność 64 GiB i rozmiaru rzeczywistego używanych danych 10 GiB migawki będą naliczane tylko za 10 GiB rozmiar używanych danych.  

[Przyrostowe migawki](../articles/virtual-machines/windows/incremental-snapshots.md) nie są obecnie obsługiwane w przypadku dysków zarządzanych, ale będzie możliwe w przyszłości.

Aby dowiedzieć się więcej na temat tworzenia migawek dysków zarządzanych, sprawdź następujące zasoby:

* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Tworzenie kopii wirtualnego dysku twardego przechowywanej jako dysk zarządzany przy użyciu migawek w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Obrazy

Dyski zarządzane obsługują także tworzenie zarządzanych niestandardowego obrazu. Można utworzyć obrazu, z Twojego niestandardowego pliku VHD na koncie magazynu lub bezpośrednio z ogólnych maszyny Wirtualnej (sys prepped). To są zapisywane w jednym obrazie wszystkie zarządzane dysku skojarzonego z maszyną Wirtualną, w tym zarówno systemu operacyjnego i dysków z danymi. Dzięki temu tworzenie setki maszyn wirtualnych przy użyciu niestandardowego obrazu bez konieczności kopiowania ani zarządzanie nimi żadnych kont magazynu.

Informacje dotyczące tworzenia obrazów zobacz następujące artykuły:
* [Jak przechwycić do zarządzanego obrazu uogólniony maszyny Wirtualnej na platformie Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Jak generalize i przechwytywanie maszyny wirtualnej systemu Linux przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Obrazy i migawki

Często widoczny wyraz "obrazu" używana z maszynami wirtualnymi i spowoduje to wyświetlenie "migawki" również. Należy zrozumieć różnicę między nimi. W przypadku zarządzanych dysków może potrwać obraz uogólniony maszynę Wirtualną, która alokację. Ten obraz będzie zawierać wszystkie dysków dołączonych do maszyny Wirtualnej. Ten obraz umożliwia utworzenie nowej maszyny Wirtualnej, a uwzględni wszystkie dyski.

Migawka jest kopii dysku w punkcie w czasie, który przyjmuje. Dotyczy tylko jeden dysk. Jeśli masz maszynę Wirtualną, która zawiera tylko jeden dysk (system operacyjny), można wykonać migawki lub obraz go i tworzenie maszyny Wirtualnej z migawki lub obrazu.

Co zrobić, jeśli maszyna wirtualna ma pięć dysków i ich są rozkładane? Można utworzyć migawkę każdy z tych dysków, ale nie Brak świadomości w Maszynie wirtualnej stanu dyski — migawek tylko wiedzieć o tym jeden dysk. W takim przypadku migawki musi być z sobą, i który nie jest obecnie obsługiwany.

## <a name="managed-disks-and-encryption"></a>Dyski zarządzane i szyfrowania

Istnieją dwa rodzaje szyfrowania omówimy w odniesieniu do zarządzanych dysków. Pierwsza z nich jest magazynu usługi szyfrowania (SSE), które jest wykonywane przez usługę magazynu. Drugim jest szyfrowania dysków Azure, które można włączyć na dyskach systemu operacyjnego i danych dla maszyn wirtualnych.

### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Magazyn (SSE)

[Szyfrowanie usługi Magazyn Azure](../articles/storage/common/storage-service-encryption.md) zapewnia szyfrowanie na rest i ochrony danych w celu spełnienia Twojej organizacji zobowiązań zabezpieczeń i zgodności. SSE jest domyślnie włączona dla wszystkich dysków zarządzanych, migawki i obrazów we wszystkich regionach, gdzie dostępna jest opcja dysków zarządzanych. Uruchamianie 10 czerwca 2017 wszystkie nowe zarządzane dyski/migawek/obrazów i nowych danych istniejących dysków zarządzanych są automatycznie szyfrowane podczas spoczynku z kluczami zarządzany przez firmę Microsoft, domyślnie. Istnieje możliwość własnego klucza szyfrowania dla plików i obiektów blob Azure. Microsoft zarządzanych kluczy zawsze będzie korzystać z szyfrowania dla tabel i kolejek.
Należy pamiętać, że po włączeniu szyfrowanie usługi Magazyn tylko nowe dane będą szyfrowane, a wszystkie istniejące pliki na tym koncie magazynu będzie pobrać zaszyfrowana wstecz przez proces szyfrowania w tle. Odwiedź stronę [strony często zadawane pytania dotyczące dysków zarządzanych](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) więcej szczegółów.


### <a name="azure-disk-encryption-ade"></a>Szyfrowanie dysków Azure (ADE)

Szyfrowanie dysków Azure umożliwia szyfrowanie dysków systemu operacyjnego i danych, używanych przez maszyny wirtualne IaaS. W tym dyski zarządzanych. W systemie Windows dyski są szyfrowane za pomocą technologii szyfrowania BitLocker standardowych. Dla systemu Linux dyski są szyfrowane za pomocą technologii DM-Crypt. To jest zintegrowany z usługą Azure Key Vault, co pozwala na kontrolowanie i zarządzać kluczami szyfrowania dysku. Aby uzyskać więcej informacji, zobacz [Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat zarządzanych dysków można znaleźć w następujących artykułach.

### <a name="get-started-with-managed-disks"></a>Rozpoczynanie pracy z usługą Managed Disks

* [Tworzenie maszyny wirtualnej przy użyciu usługi Resource Manager i programu PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

* [Dołączenie dysku danych zarządzanych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Dodawanie dysku zarządzanego do maszyny wirtualnej z systemem Linux](../articles/virtual-machines/linux/add-disk.md)

* [Zarządzane dysków programu PowerShell przykładowe skrypty](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Dysków zarządzanych w szablonach usługi Azure Resource Manager](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Porównanie dysków zarządzanych opcje magazynu

* [Magazyn w warstwie Premium i dysków](../articles/virtual-machines/windows/premium-storage.md)

* [Standardowy magazyn i dyski](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Wskazówki dotyczące obsługi

* [Migrowanie z usług AWS i innych platform do zarządzanych dysków na platformie Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Konwertuj maszynach wirtualnych platformy Azure do zarządzanych dysków na platformie Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
