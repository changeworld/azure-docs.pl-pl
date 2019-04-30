---
title: Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server | Dokumentacja firmy Microsoft
description: Dyskusję na temat różnych typów strategie HADR programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 463ef5f4a655617074915078fb4ced9e596f8957
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478369"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Wysoka dostępność i odzyskiwanie awaryjne na potrzeby programu SQL Server na maszynach wirtualnych platformy Azure

Microsoft Azure maszyny wirtualne (VM) z programem SQL Server może pomóc zmniejszyć koszt rozwiązania wysokiej dostępności i awaryjnego (HADR) odzyskiwania bazy danych. Większość rozwiązań HADR serwera SQL są obsługiwane w maszynach wirtualnych platformy Azure, tylko z platformą Azure oraz w hybrydowych rozwiązań. W rozwiązaniu tylko z platformą Azure cały system HADR działa na platformie Azure. W konfiguracji hybrydowej część rozwiązania działa na platformie Azure i innych części przebiegów lokalnych w Twojej organizacji. Elastyczność środowiska platformy Azure umożliwia przenoszenie całkowicie lub częściowo na platformie Azure, do zaspokojenia budżetu i wymagania HADR systemy bazy danych programu SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Informacje o konieczności rozwiązania HADR
Jest przekonać się, że system bazy danych, posiada możliwości HADR, które wymaga umowę dotyczącą poziomu usług (SLA). Fakt, że platforma Azure zapewnia mechanizmy wysokiej dostępności, takich jak usługa naprawiania usług cloud services i wykrywanie odzyskiwania awarii dla maszyn wirtualnych sam nie gwarantuje, że spełniasz żądaną umowy SLA. Te mechanizmy ochrony wysoką dostępność maszyn wirtualnych, ale nie wysokiej dostępności programu SQL Server działającym wewnątrz maszyn wirtualnych. Istnieje możliwość dla wystąpienia programu SQL Server zakończyć się niepowodzeniem, gdy maszyna wirtualna jest online, jak i dobrej kondycji. Ponadto nawet wysokiej dostępności, mechanizmów udostępnianych przez platformę Azure umożliwiającej przestojów maszyn wirtualnych z powodu zdarzenia, takie jak odzyskiwanie z poziomu oprogramowania lub awarie sprzętu i uaktualnień systemu operacyjnego.

Ponadto magazyn geograficznie nadmiarowy (GRS) na platformie Azure, która jest implementowane za pomocą funkcję replikacji geograficznej, może nie być rozwiązanie odzyskiwania po awarii w odpowiedniej bazy danych. Ponieważ replikacja geograficzna wysyła dane asynchronicznie, najnowsze aktualizacje mogą być utracone na wypadek awarii. Więcej informacji na temat replikacji geograficznej ograniczenia są objęte [replikacja geograficzna nie jest obsługiwane dla plików danych i dziennika na oddzielnych dyskach](#geo-replication-support) sekcji.

## <a name="hadr-deployment-architectures"></a>HADR architektur wdrożeń
Technologie HADR serwera SQL, które są obsługiwane na platformie Azure obejmują:

* [Zawsze włączone grupy dostępności](https://technet.microsoft.com/library/hh510230.aspx)
* [Zawsze włączone wystąpienia klastra trybu Failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Wysyłanie dziennika](https://technet.microsoft.com/library/ms187103.aspx)
* [Kopia zapasowa programu SQL Server i przywracania przy użyciu usługi Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx)
* [Funkcja dublowania baz danych](https://technet.microsoft.com/library/ms189852.aspx) — przestarzałe w programie SQL Server 2016

Istnieje możliwość łączenia technologii razem w celu zaimplementowania rozwiązania programu SQL Server, która ma wysoką dostępność i możliwości odzyskiwania po awarii. W zależności od używanej technologii wdrożenia hybrydowego może wymagać tunelu sieci VPN z siecią wirtualną platformy Azure. W poniższych sekcjach pokazano niektóre przykładowe architektury wdrożenia.

## <a name="azure-only-high-availability-solutions"></a>Tylko z platformą Azure: Rozwiązania wysokiej dostępności

Masz rozwiązanie zapewniające wysoką dostępność dla programu SQL Server z zawsze włączonymi grupami dostępności - o nazwie grupy dostępności na poziomie bazy danych. Można również utworzyć rozwiązanie zapewniające wysoką dostępność na poziomie wystąpienia za pomocą zawsze na wystąpienia klastra trybu Failover — trybu failover wystąpienia klastra. Dodatkowe nadmiarowość można utworzyć nadmiarowości na obu poziomach przez utworzenie grupy dostępności w trybie failover wystąpienia klastra. 

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności uruchamianych na maszynach wirtualnych platformy Azure, w tym samym regionie zapewniają wysoką dostępność. Należy skonfigurować kontroler domeny maszyny Wirtualnej, ponieważ klaster pracy awaryjnej Windows wymaga domeny usługi Active Directory.<br/> ![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności platformy Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Wystąpienia klastra trybu failover** |Tryb failover klastra wystąpienia (FCI), które wymagają magazynu udostępnionego, mogą być tworzone na 3 sposoby.<br/><br/>1. Klaster trybu failover z dwoma węzłami na maszynach wirtualnych Azure z dołączonym magazynem za pomocą [systemu Windows Server 2016 bezpośrednimi miejscami do magazynowania \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) zapewnienie wirtualnej sieci SAN na podstawie oprogramowania.<br/><br/>2. Klaster trybu failover z dwoma węzłami, uruchomione na maszynach wirtualnych Azure z usługą storage obsługiwane przez rozwiązanie innej firmy. Na przykład określonych, który używa oprogramowanie SIOS DataKeeper, zobacz [wysokiej dostępności dla udziału plików przy użyciu klastra trybu failover i 3 oprogramowania oprogramowanie SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Klaster trybu failover z dwoma węzłami na maszynach wirtualnych platformy Azure za pomocą zdalnego obiektów docelowych iSCSI udostępnionego magazynu blokowego za pośrednictwem usługi ExpressRoute. Na przykład NetApp Private Storage (NPS) udostępnia obiektu docelowego iSCSI, za pośrednictwem usługi ExpressRoute za pomocą Equinix maszynach wirtualnych platformy Azure.<br/><br/>Dla firm udostępnionego magazynu i rozwiązań do replikacji danych możesz skontaktować się producenta wszelkie problemy dotyczące uzyskiwania dostępu do danych w trybie failover.<br/><br/>Należy pamiętać, że przy użyciu infrastruktury klasyfikacji plików w górnej części [usługi Azure File storage](https://azure.microsoft.com/services/storage/files/) nie jest jeszcze obsługiwana, ponieważ to rozwiązanie jest wykorzystanie magazynu w warstwie Premium. Pracujemy nad obsługuje to szybko. |

## <a name="azure-only-disaster-recovery-solutions"></a>Tylko z platformą Azure: Rozwiązania odzyskiwania po awarii
Możesz mieć rozwiązanie odzyskiwania po awarii dla bazy danych programu SQL Server na platformie Azure przy użyciu grup dostępności, dublowania bazy danych lub kopii zapasowej i przywracania z magazynu obiektów blob.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności jest uruchomiona w wielu centrach danych na maszynach wirtualnych Azure w celu odzyskiwania po awarii. To rozwiązanie między regionami chroni przed awarią całej lokacji. <br/> ![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>W danym regionie, w ramach jednej usługi w chmurze i tej samej sieci wirtualnej należy wszystkie repliki. Ponieważ każdy region będzie mieć oddzielną sieć wirtualną, te rozwiązania wymagają sieci wirtualnej połączenie między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna za pomocą witryny Azure portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Aby uzyskać szczegółowe instrukcje, zobacz [konfigurować grupy dostępności programu SQL Server na maszynach wirtualnych Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Funkcja dublowania baz danych** |Podmiot zabezpieczeń i dublowania i serwerów działających w różnych centrach danych na potrzeby odzyskiwania po awarii. Należy wdrożyć przy użyciu certyfikatów serwera, ponieważ domeny usługi active directory nie może obejmować wiele centrów danych.<br/>![Funkcja dublowania baz danych](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Kopia zapasowa i przywracanie za pomocą usługi Azure Blob Storage** |Produkcji kopie zapasowe baz danych bezpośrednio do usługi blob storage w innym centrum danych, odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Aby uzyskać więcej informacji, zobacz [kopia zapasowa i przywracanie programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikuj i trybu Failover programu SQL Server w systemie Azure za pomocą usługi Azure Site Recovery** |Produkcyjne programu SQL Server z jednego centrum danych platformy Azure replikowane bezpośrednio do usługi Azure Storage innego centrum danych platformy Azure w celu odzyskiwania po awarii.<br/>![Są replikowane przy użyciu usługi Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrona programu SQL Server za pomocą odzyskiwania po awarii programu SQL Server i usługi Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrydowe IT: Rozwiązania odzyskiwania po awarii
Możesz mieć rozwiązanie odzyskiwania po awarii dla bazy danych programu SQL Server w środowisku IT hybrydowych przy użyciu grup dostępności, dublowania bazy danych, wysyłania dziennika i kopii zapasowej i przywracania przy użyciu magazyn obiektów blob platformy Azure.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Niektóre repliki dostępności w maszynach wirtualnych platformy Azure i innymi replikami lokalnie do odzyskiwania po awarii między lokacjami. Witryny produkcyjnej może być lokalnie lub w centrum danych platformy Azure.<br/>![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Ponieważ wszystkie repliki dostępności muszą być tego samego klastra trybu failover, klaster musi obejmować obie sieci (klastra trybu failover wiele podsieci). Ta konfiguracja wymaga połączenia sieci VPN między platformą Azure i siecią lokalną.<br/><br/>Podczas odzyskiwania systemu pomyślne baz danych należy również zainstalować repliki kontrolera domeny w lokacji odzyskiwania po awarii.<br/><br/>Istnieje możliwość użyć Kreatora dodawania repliki w programie SSMS można dodać repliki platformy Azure do istniejącego zawsze włączonej grupy dostępności. Aby uzyskać więcej informacji zobacz samouczek: Rozszerzanie zawsze włączone grupy dostępności na platformę Azure. |
| **Funkcja dublowania baz danych** |Jednego partnera w Maszynie wirtualnej platformy Azure i innych uruchamiania lokalnego odzyskiwania po awarii między lokacjami przy użyciu certyfikatów serwera. Partnerzy nie muszą znajdować się w tej samej domenie usługi Active Directory, a nie połączenie sieci VPN jest wymagane.<br/>![Funkcja dublowania baz danych](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Innej bazy danych, dublowanie scenariusz polega na jednym partnerem w Maszynie wirtualnej platformy Azure i inne uruchomione lokalnie w tej samej domenie usługi Active Directory w celu odzyskiwania po awarii między lokacjami. A [połączenia sieci VPN między sieci wirtualnej platformy Azure i siecią lokalną](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) jest wymagana.<br/><br/>Podczas odzyskiwania systemu pomyślne baz danych należy również zainstalować repliki kontrolera domeny w lokacji odzyskiwania po awarii. |
| **Wysyłanie dziennika** |Jeden serwer z systemem w Maszynie wirtualnej platformy Azure i innych uruchamiania lokalnego odzyskiwania po awarii między lokacjami. Wysyłanie dziennika zależy od udostępniania plików w Windows, więc wymagane jest połączenie sieci VPN między sieci wirtualnej platformy Azure i siecią lokalną.<br/>![Wysyłanie dziennika](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Podczas odzyskiwania systemu pomyślne baz danych należy również zainstalować repliki kontrolera domeny w lokacji odzyskiwania po awarii. |
| **Kopia zapasowa i przywracanie za pomocą usługi Azure Blob Storage** |Lokalne produkcyjnych bazach danych kopii zapasowej bezpośrednio do magazynu obiektów blob platformy Azure w celu odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Aby uzyskać więcej informacji, zobacz [kopia zapasowa i przywracanie programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikuj i trybu Failover programu SQL Server w systemie Azure za pomocą usługi Azure Site Recovery** |Lokalne programu SQL Server replikowane bezpośrednio do usługi Azure Storage na potrzeby odzyskiwania po awarii w środowisku produkcyjnym.<br/>![Są replikowane przy użyciu usługi Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrona programu SQL Server za pomocą odzyskiwania po awarii programu SQL Server i usługi Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Ważne uwagi dotyczące HADR serwera SQL na platformie Azure
Maszyny wirtualne platformy Azure, magazynu i sieci charakteryzują się operacyjnej cechami niż w systemach lokalnych, niezwirtualizowanych infrastruktury IT. Pomyślne wdrożenie rozwiązania HADR programu SQL Server na platformie Azure wymaga zrozumieć te różnice, a następnie zaprojektować rozwiązanie do ich potrzeb.

### <a name="high-availability-nodes-in-an-availability-set"></a>Węzły o wysokiej dostępności w zestawie dostępności
Zestawy dostępności na platformie Azure umożliwiają umieszczenie węzłów o wysokiej dostępności w oddzielnych błędów domenach i Aktualizacja domeny. Dla maszyn wirtualnych platformy Azure mają być umieszczone w tym samym zestawie dostępności możesz je wdrożyć w tej samej usłudze w chmurze. Tylko węzły w tej samej usłudze w chmurze mogą brać udział w tym samym zestawie dostępności. Aby uzyskać więcej informacji, zobacz [Manage the Availability of Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Zarządzanie dostępnością usługi Virtual Machines).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Zachowanie klastra pracy awaryjnej w sieci platformy Azure
RFC niezgodne ze specyfikacją usługi DHCP w systemie Azure może spowodować utworzenie niektórych przejścia w tryb failover konfiguracje klastra, aby zakończyć się niepowodzeniem z powodu nazwy sieciowej klastra, które są przypisywane zduplikowanego adresu IP, takich jak ten sam adres IP jako jeden z węzłów klastra. Jest to problem podczas implementowania grup dostępności, która zależy od funkcji klastra pracy awaryjnej Windows.

Rozważmy scenariusz, jeśli dwa węzły klastra jest tworzony i przełączone w tryb online:

1. Klaster przejściu do trybu online, a następnie Węzeł1 żądań przydzielony dynamicznie adres IP dla nazwy sieci klastra.
2. Żaden adres IP innego niż własny Węzeł1, adres IP jest nadawana przez usługę DHCP, ponieważ usługa DHCP rozpozna, że żądania pochodzą z Węzeł1 sam.
3. Windows wykrywa, że zduplikowany adres jest przydzielany Węzeł1 i nazwy sieciowej klastra trybu failover i domyślna grupa klastra nie przechodzi do trybu online.
4. Domyślna grupa klastra przenosi Węzeł2, traktuje firmy Węzeł1, adres IP jako adres IP klastra, która udostępnia grupy klastra domyślne do trybu online.
5. Gdy Węzeł2 próbuje nawiązać łączność z usługą Węzeł1, pakiety skierowany przeciwko Węzeł1 nigdy nie opuszczają Węzeł2, ponieważ jest on rozpoznawany jako adres IP firmy Węzeł1 sam. Węzeł2 nie można ustanowić łączności z Węzeł1, a następnie nastąpi utrata kworum i zamknięcie klastra.
6. W międzyczasie Węzeł1 może wysyłać pakietów do Węzeł2, ale Węzeł2 nie może odpowiedzieć. Węzeł1 nastąpi utrata kworum i zamknięcie klastra.

W tym scenariuszu można uniknąć, przypisując nieużywany statyczny adres IP, takich jak połączenia lokalnego adresu IP, takich jak 169.254.1.1, aby nazwa sieciowa klastra w celu dostosowania nazwy sieciowej klastra w trybie online. Aby uprościć ten proces, zobacz [Windows konfigurowania klastra trybu failover na platformie Azure dla grup dostępności](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności platformy Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Obsługa odbiornika grupy dostępności
Odbiorników grup dostępności są obsługiwane na maszynach wirtualnych platformy Azure z systemem Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Ta funkcja jest możliwe przy użyciu punktów końcowych ze zrównoważonym obciążeniem, włączone na maszynach wirtualnych platformy Azure, które są węzły grupy dostępności. Należy wykonać kroki specjalnej konfiguracji dla detektorów można działać dla obu aplikacji klienckich, które są uruchomione na platformie Azure, a także uruchomione w środowisku lokalnym.

Istnieją dwie główne opcje dotyczące konfigurowania z odbiornikiem: zewnętrznych (publicznych) lub wewnętrzny. Zewnętrznych (publicznych) odbiornik używa modułu równoważenia obciążenia nakierowanego na internet i jest skojarzona z publicznych Virtual IP (VIP), który jest dostępny za pośrednictwem Internetu. Wewnętrznego odbiornika używa wewnętrznego modułu równoważenia obciążenia i obsługuje tylko klientów w ramach tej samej sieci wirtualnej. Typ modułu równoważenia obciążenia dla dowolnego, należy włączyć bezpośredniego zwrotu serwera. 

Jeśli grupy dostępności obejmuje wiele podsieci platformy Azure (na przykład wdrożenia, które przecina regiony platformy Azure), musi zawierać ciąg połączenia klienta "**MultisubnetFailover = True**". Skutkiem próby nawiązania połączenia równoległego do replik w różnych podsieciach. Aby uzyskać instrukcje na temat konfigurowania odbiornik zobacz

* [Konfigurowanie odbiornika ILB dla grupy dostępności na platformie Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurowanie odbiornika zewnętrznego dla grup dostępności na platformie Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Możesz nadal łączyć się każdej repliki dostępności osobno przez bezpośrednie połączenie z wystąpieniem usługi. Ponadto ponieważ grupy dostępności są zgodne wstecz z klientami dublowania bazy danych, mogą łączyć się repliki dostępności, takich jak dublowania partnerów, tak długo, jak repliki są skonfigurowane podobne do funkcji dublowania baz danych:

* Jedna replika podstawowa i jedna replika pomocnicza
* Replika pomocnicza została skonfigurowana jako bez możliwości odczytu (**pomocniczego do odczytu** opcja równa **nr**)

Przykładowe parametry połączenia klienta, który odpowiada tej konfiguracji podobne do funkcji dublowania bazy danych za pomocą ADO.NET lub SQL Server Native Client znajduje się poniżej:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Aby uzyskać więcej informacji dotyczących łączności klienta Zobacz:

* [Za pomocą słów kluczowych parametrów połączenia przy użyciu programu SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Łączenie klientów (SQL Server) sesji dublowania bazy danych](https://technet.microsoft.com/library/ms175484.aspx)
* [Nawiązywanie połączenia z odbiornikiem grupy dostępności w hybrydowym IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Odbiorniki grupy dostępności, łączności klienta i aplikacji trybu Failover (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Przy użyciu parametrów połączenia dublowania bazy danych z grupami dostępności](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Opóźnienie sieci w hybrydowym IT
Należy wdrożyć swoje rozwiązanie HADR przy założeniu, że może być okresy czasu, opóźnienie sieci wysokiego między siecią lokalną i Azure. W przypadku wdrażania replik na platformie Azure, należy użyć zatwierdzania asynchronicznego zamiast zatwierdzania synchronicznego dla trybu synchronizacji. Podczas wdrażania serwerów dublowania bazy danych — lokalnych i na platformie Azure, użyj trybu wysokiej wydajności, zamiast trybu wysokiego poziomu zabezpieczeń.

### <a name="geo-replication-support"></a>Obsługa replikacji geograficznej
Replikacja geograficzna w dyskami platformy Azure nie obsługuje plików danych i plik dziennika dotyczący tej samej bazy danych mają być przechowywane na oddzielnych dyskach. Magazyn GRS replikuje zmiany na poszczególnych dyskach niezależnie i asynchronicznie. Ten mechanizm gwarantuje kolejności zapisu, w ramach jednego dysku na kopię replikowanej geograficznie, a nie na replikowanej geograficznie kopie wiele dysków. Jeśli skonfigurujesz bazę danych do przechowywania jego plików danych i jego pliku dziennika na oddzielnych dyskach, dyski odzyskany po awarii może zawierać aktualniejszej kopii pliku danych niż plik dziennika, który przerywa zapisu z wyprzedzeniem dziennika programu SQL Server i właściwości ACID pobieranie na komputerze. Jeśli nie masz możliwość wyłączenia replikacji geograficznej na koncie magazynu, należy zachować wszystkich danych i plików dziennika dla danej bazy danych na tym samym dysku. Jeśli musisz użyć więcej niż jeden dysk, ze względu na rozmiar bazy danych, należy wdrożyć rozwiązania odzyskiwania po awarii, które są wymienione powyżej, aby zapewnić nadmiarowość danych.

## <a name="next-steps"></a>Kolejne kroki
Jeśli musisz utworzyć maszynę wirtualną platformy Azure z programem SQL Server, zobacz [inicjowania obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

Aby uzyskać najlepszą wydajność programu SQL Server uruchomionego na Maszynie wirtualnej platformy Azure, zobacz wskazówki zawarte w [Performance Best Practices for SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Aby uzyskać inne tematy związane z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Inne zasoby
* [Instalowanie nowego lasu usługi Active Directory na platformie Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Tworzenie klastra trybu Failover dla grup dostępności w maszynie Wirtualnej platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

