---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72b6c1f86b18df172994827ec78eb109fe82454e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75913777"
---
## <a name="benefits-of-managed-disks"></a>Zalety dysków zarządzanych

Przejdźmy do niektórych korzyści, które można uzyskać przy użyciu dysków zarządzanych.

### <a name="highly-durable-and-available"></a>Wysoka trwałość i dostępna

Dyski zarządzane są przeznaczone do dostępności w 99,999%. Dyski zarządzane osiągnąć ten sposób, zapewniając trzy repliki danych, co pozwala na wysoką trwałość. Jeśli jedna lub nawet dwie repliki występują problemy, pozostałe repliki pomagają zapewnić trwałość danych i wysoką tolerancję na błędy. Ta architektura pomogła platformie Azure stale dostarczać trwałość infrastruktury jako usługi (IaaS) klasy korporacyjnej, z wiodącym w branży wskaźnikiem awaryjności zero% w ujęciu rocznym.

### <a name="simple-and-scalable-vm-deployment"></a>Proste i skalowalne wdrażanie maszyn wirtualnych

Za pomocą dysków zarządzanych można utworzyć do 50 000 **dysków** maszyn wirtualnych typu w ramach subskrypcji na region, co umożliwia tworzenie tysięcy **maszyn wirtualnych** w ramach jednej subskrypcji. Ta funkcja dodatkowo zwiększa skalowalność [zestawów skalowania maszyny wirtualnej,](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) umożliwiając tworzenie do 1000 maszyn wirtualnych w zestawie skalowania maszyny wirtualnej przy użyciu obrazu portalu Marketplace.

### <a name="integration-with-availability-sets"></a>Integracja z zestawami dostępności

Dyski zarządzane są zintegrowane z zestawami dostępności, aby upewnić się, że [dyski maszyn wirtualnych w zestawie dostępności](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Dyski są automatycznie umieszczane w różnych jednostkach skali magazynowania (stempli). Jeśli stempel nie powiedzie się z powodu awarii sprzętu lub oprogramowania, tylko wystąpienia maszyny Wirtualnej z dyskami na tych znaczkach nie powiedzie się. Załóżmy na przykład, że masz aplikację uruchomień na pięciu maszynach wirtualnych, a maszyny wirtualne znajdują się w zestawie dostępności. Dyski dla tych maszyn wirtualnych nie będą przechowywane w tym samym znaczek, więc jeśli jeden znaczek ulegnie w dół, inne wystąpienia aplikacji nadal działać.

### <a name="integration-with-availability-zones"></a>Integracja ze strefami dostępności

Dyski zarządzane obsługują [strefy dostępności,](../articles/availability-zones/az-overview.md)która jest ofertą o wysokiej dostępności, która chroni aplikacje przed awariami centrów danych. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%.

### <a name="azure-backup-support"></a>Pomoc techniczna usługi Azure Backup

Aby chronić przed awariami regionalnymi, [usługa Azure Backup](../articles/backup/backup-overview.md) może służyć do tworzenia zadania tworzenia kopii zapasowych z opartymi na czasie kopiami zapasowymi i zasadami przechowywania kopii zapasowych. Dzięki temu można wykonywać łatwe uzupełnienia maszyn wirtualnych do woli. Obecnie usługa Azure Backup obsługuje dyski o rozmiarach do czterech dysków tebibajtowych (TiB).  Usługa Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie dysków zarządzanych. [Dowiedz się więcej](../articles/backup/backup-support-matrix-iaas.md) o obsłudze kopii zapasowych maszyn wirtualnych platformy Azure.

### <a name="granular-access-control"></a>Szczegółowa kontrola dostępu

Za pomocą [kontroli dostępu opartej na rolach platformy Azure (RBAC)](../articles/role-based-access-control/overview.md) można przypisać określone uprawnienia dla dysku zarządzanego jednemu lub większej liczbie użytkowników. Dyski zarządzane uwidaczniają różne operacje, w tym odczyt, zapis (tworzenie/aktualizowanie), usuwanie i pobieranie [identyfikatora URI podpisu dostępu współdzielonego](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) dla dysku. Można udzielić dostępu tylko do operacji, które dana osoba musi wykonać swoje zadanie. Na przykład jeśli nie chcesz, aby osoba skopiowała dysk zarządzany na konto magazynu, możesz nie udzielać dostępu do akcji eksportu dla tego dysku zarządzanego. Podobnie, jeśli nie chcesz, aby osoba używała identyfikatora URI sygnatury dostępu Współdzielonego do kopiowania dysku zarządzanego, możesz nie udzielać tego uprawnienia dyskowi zarządzanego.

### <a name="upload-your-vhd"></a>Prześlij swój vhd

 Bezpośrednie przekazywanie ułatwia przenoszenie vhd na dysk zarządzany platformy Azure. Wcześniej trzeba było wykonać bardziej zaangażowany proces, który obejmował przemieszczanie danych na koncie magazynu. Teraz jest mniej kroków. Łatwiej jest przekazać na lokalnych maszynach wirtualnych na platformę Azure, przekazać na duże dyski zarządzane, a proces tworzenia kopii zapasowych i przywracania jest uproszczony. Zmniejsza również koszty, umożliwiając przekazywanie danych do dysków zarządzanych bezpośrednio bez dołączania ich do maszyn wirtualnych. Możesz użyć bezpośredniego przesyłania, aby przesłać vhds do 32 TiB w rozmiarze.

 Aby dowiedzieć się, jak przenieść vhd na platformę Azure, zobacz [artykułów interfejsu wiersza polecenia](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) lub [programu PowerShell.](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)

## <a name="encryption"></a>Szyfrowanie

Dyski zarządzane oferują dwa różne rodzaje szyfrowania. Pierwszym z nich jest szyfrowanie po stronie serwera (SSE), które jest wykonywane przez usługę magazynu. Drugi to Szyfrowanie dysków azure (ADE), które można włączyć na dysku systemu operacyjnego i dysków z danymi dla maszyn wirtualnych.

### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

[Szyfrowanie po stronie serwera Azure](../articles/virtual-machines/windows/disk-encryption.md) zapewnia szyfrowanie w spoczynku i chroni dane, aby spełnić zobowiązania dotyczące zabezpieczeń i zgodności organizacji. Szyfrowanie po stronie serwera jest domyślnie włączone dla wszystkich dysków zarządzanych, migawek i obrazów we wszystkich regionach, w których dostępne są dyski zarządzane. Możesz zezwolić platformie Azure na zarządzanie kluczami, są to klucze zarządzane przez platformę lub samodzielnie zarządzać kluczami, są to klucze zarządzane przez klienta. Więcej informacji można znaleźć na stronie Często zadawane pytania dotyczące [dysków zarządzanych.](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption)

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

Szyfrowanie dysków platformy Azure umożliwia szyfrowanie dysków systemu operacyjnego i danych używanych przez maszynę wirtualną IaaS. To szyfrowanie obejmuje dyski zarządzane. W systemie Windows dyski są szyfrowane przy użyciu standardowej w branży technologii szyfrowania funkcją BitLocker. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Proces szyfrowania jest zintegrowany z usługą Azure Key Vault, aby umożliwić kontrolowanie kluczy szyfrowania dysków i zarządzanie nimi. Aby uzyskać więcej informacji, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych IaaS](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Role dysku

Istnieją trzy główne role dysku na platformie Azure: dysk danych, dysk systemu operacyjnego i dysk tymczasowy. Te role są mapowane na dyski, które są dołączone do maszyny wirtualnej.

![Role dysku w akcji](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Dysk z danymi

Dysk danych to dysk zarządzany, który jest dołączony do maszyny wirtualnej do przechowywania danych aplikacji lub innych danych, które należy zachować. Dyski danych są rejestrowane jako dyski SCSI i są oznaczone listem, który wybierzesz. Maksymalna pojemność każdego dysku danych wynosi 32 767 gibibajtów (GiB). Rozmiar maszyny wirtualnej określa, ile dysków danych można dołączyć do niej i typ magazynu, którego można użyć do obsługi dysków.

### <a name="os-disk"></a>Dysk systemu operacyjnego

Każda maszyna wirtualna ma jeden podłączony dysk systemu operacyjnego. Ten dysk systemu operacyjnego ma wstępnie zainstalowany system operacyjny, który został wybrany podczas tworzenia maszyny wirtualnej. Ten dysk zawiera wolumin rozruchowy.

Maksymalna pojemność tego dysku wynosi 2048 GiB.

### <a name="temporary-disk"></a>Dysk tymczasowy

Każda maszyna wirtualna zawiera dysk tymczasowy, który nie jest dyskiem zarządzanym. Dysk tymczasowy zapewnia krótkoterminowe przechowywanie aplikacji i procesów i jest przeznaczony tylko do przechowywania danych, takich jak pliki stron lub wymiany. Dane na dysku tymczasowym mogą zostać utracone podczas zdarzenia [konserwacji](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) lub po [przemieszczeniu maszyny Wirtualnej](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Na maszynach wirtualnych z systemem Azure Linux dysk tymczasowy jest /dev/sdb domyślnie i na maszynach wirtualnych z systemem Windows dysk tymczasowy jest D: domyślnie. Podczas pomyślnego standardowego ponownego uruchomienia maszyny Wirtualnej dane na dysku tymczasowym będą się powtarzać.

## <a name="managed-disk-snapshots"></a>Migawki dysku zarządzanego

Migawka dysku zarządzanego jest pełną kopią dysku zarządzanego, która jest domyślnie przechowywana jako standardowy dysk zarządzany. Za pomocą migawek można w dowolnym momencie wyw. Te migawki istnieją niezależnie od dysku źródłowego i mogą być używane do tworzenia nowych dysków zarządzanych. 

Migawki są rozliczane na podstawie używanego rozmiaru. Na przykład jeśli utworzysz migawkę dysku zarządzanego o aprowizowanym pojemności 64 GiB i rzeczywistym rozmiarze używanych danych 10 GiB, ta migawka jest rozliczana tylko dla używanego rozmiaru danych 10 GiB. Możesz zobaczyć używany rozmiar migawek, patrząc na [raport użycia platformy Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Na przykład jeśli używany rozmiar danych migawki wynosi 10 GiB, **dzienny** raport użycia pokaże 10 GiB/(31 dni) = 0,3226 jako zużytą ilość.

Aby dowiedzieć się więcej o tworzeniu migawek dla dysków zarządzanych, zobacz następujące zasoby:

* [Tworzenie migawki dysku zarządzanego w systemie Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Tworzenie migawki dysku zarządzanego w systemie Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Obrazy

Dyski zarządzane obsługują również tworzenie zarządzanego obrazu niestandardowego. Obraz można utworzyć z niestandardowego dysku wirtualnego VHD na koncie magazynu lub bezpośrednio z uogólnionej (sysprepped) maszyny Wirtualnej. Ten proces przechwytuje pojedynczy obraz. Ten obraz zawiera wszystkie dyski zarządzane skojarzone z maszyną wirtualną, w tym zarówno system operacyjny, jak i dyski z danymi. Ten zarządzany obraz niestandardowy umożliwia tworzenie setek maszyn wirtualnych przy użyciu obrazu niestandardowego bez konieczności kopiowania lub zarządzania kontami magazynu.

Aby uzyskać informacje na temat tworzenia obrazów, zobacz następujące artykuły:

* [Jak przechwycić zarządzany obraz uogólnionej maszyny Wirtualnej na platformie Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Uogólnianie i przechwytywanie maszyny wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Obrazy a migawki

Ważne jest, aby zrozumieć różnicę między obrazami i migawkami. Za pomocą dysków zarządzanych można zrobić obraz uogólnionej maszyny Wirtualnej, która została cofnięta. Ten obraz zawiera wszystkie dyski podłączone do maszyny Wirtualnej. Ten obraz służy do tworzenia maszyny Wirtualnej i obejmuje wszystkie dyski.

Migawka jest kopią dysku w momencie, gdy migawka jest podejmowana. Dotyczy tylko jednego dysku. Jeśli masz maszynę wirtualną, która ma jeden dysk (dysk systemu operacyjnego), można zrobić migawkę lub obraz i utworzyć maszynę wirtualną z migawki lub obrazu.

Migawka nie ma świadomości żadnego dysku, z wyjątkiem tego, który zawiera. To sprawia, że problematyczne do użycia w scenariuszach, które wymagają koordynacji wielu dysków, takich jak rozkładanie. Migawki musiałyby być w stanie koordynować ze sobą i obecnie nie jest obsługiwany.

## <a name="disk-allocation-and-performance"></a>Alokacja i wydajność dysku

Poniższy diagram przedstawia alokację przepustowości w czasie rzeczywistym i we/wy dla dysków przy użyciu trzypoziomowego systemu inicjowania obsługi administracyjnej:

![Trzypoziomowy system inicjowania obsługi administracyjnej pokazujący przepustowość i alokację we/wy](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Aprowizacji pierwszego poziomu ustawia na dysku we/wy i przydział przepustowości.  Na drugim poziomie host serwera obliczeniowego implementuje inicjowanie obsługi administracyjnej dysków SSD, stosując je tylko do danych przechowywanych na dysku SSD serwera, który obejmuje dyski z buforowania (ReadWrite i ReadOnly), a także dyski lokalne i tymczasowe. Na koniec inicjowanie obsługi administracyjnej sieci maszyn wirtualnych odbywa się na trzecim poziomie dla dowolnych we/wy, które host obliczeń wysyła do wewnętrznej bazy danych usługi Azure Storage. W tym schemacie wydajność maszyny Wirtualnej zależy od różnych czynników, od sposobu używania lokalnego dysku SSD, do liczby dołączonych dysków, a także od typu wydajności i buforowania dysków, które ma dołączone.

Jako przykład tych ograniczeń, maszyna wirtualna Standard_DS1v1 nie może osiągnąć potencjału operacji We/Wy 5000 dysków P30, niezależnie od tego, czy jest buforowany, czy nie, z powodu ograniczeń na poziomie SSD i sieci:

![Standard_DS1v1 przykład alokacji](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Platforma Azure używa priorytetowego kanału sieciowego dla ruchu dyskowego, który uzyskuje pierwszeństwo przed innymi niskim priorytetem ruchu sieciowego. Pomaga to dyskom utrzymać oczekiwaną wydajność w przypadku rywalizacji o sieć. Podobnie usługa Azure Storage obsługuje rywalizacji zasobów i innych problemów w tle z automatycznego równoważenia obciążenia. Usługa Azure Storage przydziela wymagane zasoby podczas tworzenia dysku i stosuje proaktywne i reaktywne równoważenie zasobów w celu obsługi poziomu ruchu. Dzięki temu dyski mogą utrzymać oczekiwane we/wy i przepływności docelowe. Metryki na poziomie maszyny Wirtualnej i Dysku można użyć do śledzenia alertów wydajności i konfiguracji w razie potrzeby.

Zapoznaj się z naszym [projektem,](../articles/virtual-machines/windows/premium-storage-performance.md) aby zapoznać się z artykułem o wysokiej wydajności, aby dowiedzieć się, jakie są najlepsze rozwiązania dotyczące optymalizacji konfiguracji maszyn wirtualnych i dysków, aby osiągnąć żądaną wydajność

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz, aby film wideo przechodził bardziej szczegółowo na dyskach zarządzanych, zapoznaj się z [programem: Lepsza odporność maszyny wirtualnej platformy Azure z dyskami zarządzanymi](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Dowiedz się więcej o poszczególnych typach dysków, które oferuje platforma Azure, który typ jest odpowiedni do Twoich potrzeb, i dowiedz się więcej o ich celach wydajności w naszym artykule na temat typów dysków.
