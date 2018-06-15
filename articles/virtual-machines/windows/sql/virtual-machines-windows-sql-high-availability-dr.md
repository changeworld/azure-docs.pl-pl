---
title: Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server | Dokumentacja firmy Microsoft
description: Omówienie różnych typów HADR strategii na serwerze SQL działa w maszynach wirtualnych platformy Azure.
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
ms.openlocfilehash: e9b4ca959b93e097bb52a841cec02cc476ef5f48
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401263"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Wysoka dostępność i odzyskiwanie awaryjne na potrzeby programu SQL Server na maszynach wirtualnych platformy Azure

Maszyny wirtualne Microsoft Azure (maszyn wirtualnych) z programem SQL Server może ułatwić niższy koszt wysokiej dostępności i odzyskiwaniem po awarii (HADR) bazy danych rozwiązania służącego do odzyskiwania. Większość rozwiązań HADR serwera SQL są obsługiwane w maszynach wirtualnych platformy Azure, tylko do platformy Azure oraz w hybrydowych rozwiązań. W rozwiązaniu Azure — tylko do całego systemu HADR działa na platformie Azure. W konfiguracji hybrydowych część rozwiązania jest uruchamiany w Azure i innych części działa lokalnie w Twojej organizacji. Elastyczność środowiska platformy Azure umożliwia przenoszenie częściowo lub całkowicie Azure do zaspokojenia budżetu i wymagania HADR systemów bazy danych programu SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Opis potrzebę HADR rozwiązania
Jest zapewnia, że system bazy danych, posiada funkcji HADR, które wymaga Umowa dotycząca poziomu usług (SLA). Fakt, że Azure zapewnia mechanizmy wysokiej dostępności, takich jak usługa korygujący dla usługi w chmurze i wykrywanie odzyskiwania awarii w przypadku maszyn wirtualnych nie gwarantuje, że można odnaleźć żądanego umowy SLA. Te mechanizmy ochrony wysoką dostępność maszyn wirtualnych, ale nie wysokiej dostępności programu SQL Server uruchomionym wewnątrz maszyn wirtualnych. Istnieje możliwość wystąpienia programu SQL Server się niepowodzeniem, gdy maszyna wirtualna jest online i działa prawidłowo. Ponadto nawet wysokiej dostępności, mechanizmów udostępnianych przez usługę Azure zezwala przestoje maszyn wirtualnych z powodu zdarzenia, takie jak odzyskiwanie z oprogramowania lub awarie sprzętu i uaktualnień systemu operacyjnego.

Ponadto z magazynu geograficznie nadmiarowego magazynu (GRS) na platformie Azure, która jest zaimplementowana przy użyciu funkcji o nazwie — replikacja geograficzna, nie może być rozwiązanie odzyskiwania po awarii odpowiednie dla baz danych. Ponieważ replikacja geograficzna wysyła dane asynchronicznie, najnowsze aktualizacje mogą zostać utracone w przypadku awarii. Więcej informacji na temat ograniczeń — replikacja geograficzna są objęte [replikacja geograficzna nie jest obsługiwana dla plików danych i dziennika na oddzielnych dyskach](#geo-replication-support) sekcji.

## <a name="hadr-deployment-architectures"></a>HADR wdrożenia architektury
Technologii HADR serwera SQL, które są obsługiwane na platformie Azure, obejmują:

* [Zawsze włączone grupy dostępności](https://technet.microsoft.com/library/hh510230.aspx)
* [Zawsze włączone wystąpienia klastra trybu Failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Wysyłanie dziennika](https://technet.microsoft.com/library/ms187103.aspx)
* [Kopia zapasowa programu SQL Server i przywracania usługi magazynu obiektów Blob platformy Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [Funkcja dublowania baz danych](https://technet.microsoft.com/library/ms189852.aspx) — przestarzałe w programie SQL Server 2016

Istnieje możliwość łączenia technologii ze sobą w celu wdrożenia rozwiązania programu SQL Server, która ma wysoką dostępność i możliwości odzyskiwania po awarii. W zależności od technologii używanej hybrydowej może wymagać tunel sieci VPN z sieci wirtualnej platformy Azure. W poniższych rozdziałach opisano niektóre architektury przykład wdrożenia.

## <a name="azure-only-high-availability-solutions"></a>Tylko do platformy Azure: rozwiązania wysokiej dostępności

Rozwiązanie zapewniające wysoką dostępność dla programu SQL Server może być na poziomie bazy danych z zawsze włączonych grup dostępności - o nazwie grupy dostępności. Można również utworzyć rozwiązanie zapewniające wysoką dostępność na poziomie wystąpienia z zawsze na wystąpienia klastra trybu Failover — trybu failover wystąpienia klastra. Dla dodatkowych nadmiarowość można utworzyć nadmiarowości na obu poziomach przez utworzenie grupy dostępności na trybu failover wystąpienia klastra. 

| Technologia | Przykład architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności uruchomionych w maszynach wirtualnych platformy Azure, w tym samym regionie zapewnia wysoką dostępność. Należy skonfigurować kontroler domeny maszyny Wirtualnej, ponieważ klaster pracy awaryjnej systemu Windows wymaga domeny usługi Active Directory.<br/> ![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Wystąpienia klastra trybu failover** |Klastra trybu failover wystąpienia (FCI), które wymagają magazynu udostępnionego, mogą być tworzone na 3 sposoby.<br/><br/>1. Klaster trybu failover z dwoma węzłami uruchomionych w maszynach wirtualnych platformy Azure z dołączonym magazynem za pomocą [systemu Windows Server 2016 bezpośrednie miejsca do magazynowania \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) do wybierz wirtualną sieć SAN opartych na oprogramowaniu.<br/><br/>2. Klaster trybu failover z dwoma węzłami uruchomionych w maszynach wirtualnych platformy Azure z magazynem obsługiwanego przez rozwiązanie do klastrowania innych firm. Na przykład określonych, który używa SIOS DataKeeper, zobacz [wysokiej dostępności udziału plików przy użyciu klastra trybu failover i 3 firmy SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Uruchomionych w maszynach wirtualnych platformy Azure z obiektów docelowych iSCSI zdalnego klastra trybu failover z dwoma węzłami udostępnionego magazynu blokowego za pośrednictwem usługi ExpressRoute. Na przykład NetApp prywatnego magazynu (NPS) udostępnia obiektu docelowego iSCSI za pośrednictwem usługi ExpressRoute z Equinix na maszynach wirtualnych platformy Azure.<br/><br/>Magazyn udostępniony innej firmy i rozwiązania dotyczące replikacji danych powinien skontaktuj się z dostawcą wszelkie problemy dotyczące uzyskiwania dostępu do danych w tryb failover.<br/><br/>Należy pamiętać, że przy użyciu infrastruktury klasyfikacji plików nad [magazyn plików Azure](https://azure.microsoft.com/services/storage/files/) nie jest jeszcze obsługiwany, ponieważ Magazyn w warstwie Premium nie korzysta z tego rozwiązania. Pracujemy nad obsługuje to szybko. |

## <a name="azure-only-disaster-recovery-solutions"></a>Tylko do platformy Azure: rozwiązania w zakresie odzyskiwania po awarii
Możesz mieć rozwiązanie odzyskiwania po awarii dla baz danych programu SQL Server na platformie Azure przy użyciu grup dostępności, dublowania bazy danych lub kopii zapasowej i przywracanie z magazynu obiektów blob.

| Technologia | Przykład architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności z wielu centrów w maszynach wirtualnych platformy Azure dla odzyskiwania po awarii. To rozwiązanie między region chroni przed awarią całej lokacji. <br/> ![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>W obrębie regionu wszystkie repliki powinna być w tej samej usługi w chmurze i tej samej sieci wirtualnej. Ponieważ każdy region będzie mieć osobne sieci wirtualnej, te rozwiązania wymagają sieci wirtualnej z łącznością w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [skonfigurować połączenia do wirtualnymi przy użyciu portalu Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Aby uzyskać szczegółowe instrukcje, zobacz [konfigurowania grupy dostępności programu SQL Server na maszynach wirtualnych Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Funkcja dublowania baz danych** |Podmiot zabezpieczeń i dublowane i serwerów działających w różnych centrach danych, odzyskiwania po awarii. Należy wdrożyć przy użyciu certyfikatów serwera, ponieważ domeny usługi active directory nie może obejmować wiele centrów danych.<br/>![Funkcja dublowania baz danych](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Kopia zapasowa i przywracanie z usługi Magazyn obiektów Blob platformy Azure** |Produkcji kopie zapasowe baz danych bezpośrednio do magazynu obiektów blob w różnych centrach danych, odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Aby uzyskać więcej informacji, zobacz [kopii zapasowej i przywracania dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikacja i pracy awaryjnej programu SQL Server na platformie Azure za pomocą usługi Azure Site Recovery** |SQL Server produkcji jednego centrum danych Azure, replikowane bezpośrednio do usługi Azure Storage różnych centrach danych platformy Azure, odzyskiwania po awarii.<br/>![Replikuj za pomocą usługi Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrony programu SQL Server przy użyciu odzyskiwanie po awarii programu SQL Server i usługi Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrydowego IT: Rozwiązania w zakresie odzyskiwania po awarii
Możesz mieć rozwiązanie odzyskiwania po awarii dla baz danych programu SQL Server w środowisku IT hybrydowych przy użyciu grup dostępności, dublowania bazy danych wysyłania dziennika i tworzenia kopii zapasowej i przywracanie z magazynem Azure blog.

| Technologia | Przykład architektury |
| --- | --- |
| **Grupy dostępności** |Niektóre repliki dostępności uruchomionych w maszynach wirtualnych platformy Azure i innych replik uruchamiane lokalnie do odzyskiwania po awarii między witrynami. Lokacji produkcyjnej może być lokalnie lub w centrum danych Azure.<br/>![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Ponieważ wszystkie repliki dostępności muszą być tego samego klastra trybu failover, klaster musi obejmować obu sieci (klastra trybu failover wielu podsieci). Ta konfiguracja wymaga połączenia sieci VPN platformy Azure i siecią lokalną.<br/><br/>Pomyślnym awarii baz danych należy również zainstalować repliki kontrolera domeny w lokacji odzyskiwania po awarii.<br/><br/>Użytkownik może użyć Kreatora dodawania repliki w programie SSMS można dodać repliki Azure do istniejących zawsze włączonej grupy dostępności. Aby uzyskać więcej informacji, zobacz samouczek: rozszerzenie sieci zawsze włączonej grupy dostępności na platformie Azure. |
| **Funkcja dublowania baz danych** |Jednego partnera uruchomione w maszynie Wirtualnej platformy Azure i innych uruchamiania lokalnego do odzyskiwania awaryjnego międzylokacyjnej przy użyciu certyfikatów serwera. Partnerzy nie muszą znajdować się w tej samej domenie usługi Active Directory, a żadne połączenie sieci VPN nie jest wymagane.<br/>![Funkcja dublowania baz danych](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Dublowania scenariusz innej bazy danych wymaga jednego partnera uruchomione w maszynie Wirtualnej platformy Azure i innych uruchomionych lokalnie w tej samej domenie usługi Active Directory do odzyskiwania po awarii między witrynami. A [połączenia sieci VPN między sieci wirtualnej platformy Azure i siecią lokalną](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) jest wymagana.<br/><br/>Pomyślnym awarii baz danych należy również zainstalować repliki kontrolera domeny w lokacji odzyskiwania po awarii. |
| **Wysyłanie dziennika** |Jeden serwer działający w maszynie Wirtualnej platformy Azure i inne uruchomione lokalnego odzyskiwania po awarii między witrynami. Wysyłanie dziennika zależy od udostępnianie plików systemu Windows, dlatego wymagane jest połączenie sieci VPN między sieci wirtualnej platformy Azure i siecią lokalną.<br/>![Wysyłanie dziennika](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Pomyślnym awarii baz danych należy również zainstalować repliki kontrolera domeny w lokacji odzyskiwania po awarii. |
| **Kopia zapasowa i przywracanie z usługi Magazyn obiektów Blob platformy Azure** |Lokalne produkcyjnych baz danych kopii zapasowej bezpośrednio do magazynu obiektów blob platformy Azure dla odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Aby uzyskać więcej informacji, zobacz [kopii zapasowej i przywracania dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikacja i pracy awaryjnej programu SQL Server na platformie Azure za pomocą usługi Azure Site Recovery** |Lokalne replikowane bezpośrednio do magazynu Azure do odzyskiwania po awarii programu SQL Server w środowisku produkcyjnym.<br/>![Replikuj za pomocą usługi Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrony programu SQL Server przy użyciu odzyskiwanie po awarii programu SQL Server i usługi Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Ważne uwagi dotyczące HADR serwera SQL na platformie Azure
Maszyny wirtualne platformy Azure, magazynu i sieci charakteryzują się innymi cechami operacyjne, niż lokalne, niezwirtualizowanych infrastruktury IT. Pomyślne zaimplementowanie rozwiązania HADR programu SQL Server na platformie Azure wymaga, aby zrozumieć te różnice i zaprojektować rozwiązanie, aby dostosować.

### <a name="high-availability-nodes-in-an-availability-set"></a>Węzły o wysokiej dostępności w zestawie dostępności
Zestawy dostępności na platformie Azure umożliwiają umieszczenie węzłów wysokiej dostępności w oddzielnych domen błędów (FDs) i aktualizację domeny (UDs). W przypadku maszyn wirtualnych platformy Azure do umieszczenia w tym samym zestawie dostępności należy wdrożyć je w tej samej usłudze w chmurze. Tylko węzły w samej usłudze w chmurze mogą uczestniczyć w tym samym zestawie dostępności. Aby uzyskać więcej informacji, zobacz [Manage the Availability of Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Zarządzanie dostępnością usługi Virtual Machines).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Zachowanie klastra pracy awaryjnej w sieci Azure
Z systemem innym niż — zgodne ze standardem RFC usługi DHCP w usłudze Azure może spowodować utworzenie niektórych trybu failover konfiguracje klastra, aby zakończyć się niepowodzeniem z powodu nazwy sieciowej klastra, które są przypisywane zduplikowanego adresu IP, takie jak ten sam adres IP, jako jeden z węzłów klastra. Jest to problem podczas implementowania grup dostępności, która jest zależna od funkcji klastra pracy awaryjnej systemu Windows.

Rozważmy scenariusz, podczas tworzenia klastra z dwoma węzłami i przełączony w tryb online:

1. Klaster przejściu do trybu online, a następnie NODE1 żądań przypisywany dynamicznie adres IP dla nazwy sieci klastra.
2. Żaden adres IP innego niż własny NODE1 adres IP jest określany przez usługę DHCP, ponieważ usługa DHCP rozpoznaje, czy żądanie pochodzi z NODE1 samej siebie.
3. System Windows wykryje zduplikowanego adresu jest przypisany do NODE1 i nazwa sieciowa klastra pracy awaryjnej, czy domyślna grupa klastra nie przechodzi do trybu online.
4. Domyślna grupa klastra przenosi Węzeł2, która traktuje NODE1 na adres IP jak adres IP klastra i dostarcza domyślnej grupy klastra online.
5. Gdy NODE2 próbuje nawiązać łączność z Węzeł1, pakiety skierowany NODE1 nigdy nie opuszczają NODE2 ponieważ rozpoznawany NODE1 na adres IP do samej siebie. NODE2 nie może ustanowić połączenia z Węzeł1, następnie utraty kworum i zamyka klastra.
6. Tymczasem NODE1 mogą wysyłać pakietów do Węzeł2, ale NODE2 nie może wysłać odpowiedzi. Węzeł1 utraty kworum i zamyka klastra.

W tym scenariuszu można uniknąć, przypisując nieużywane statyczny adres IP, takie jak adres IP połączenia lokalnego, takie jak 169.254.1.1, do nazwy sieciowej klastra, aby przełączyć Nazwa sieciowa klastra w tryb online. W celu uproszczenia tego procesu, zobacz [Windows konfigurowania klastra trybu failover na platformie Azure dla grup dostępności](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Obsługa odbiornika grupy dostępności
Odbiorniki grupy dostępności są obsługiwane na maszynach wirtualnych Azure systemem Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Ta obsługa jest możliwe za pomocą punktów końcowych z równoważeniem obciążenia, włączone na maszynach wirtualnych platformy Azure, które są węzłami grupy dostępności. Należy wykonać kroki specjalnej konfiguracji w przypadku odbiorników dla obu aplikacji klienckich, które są uruchomione w Azure, a także systemami lokalnymi.

Istnieją dwie główne opcje dotyczące konfigurowania programu odbiornika: zewnętrznych (publicznych) lub wewnętrzny. Zewnętrznych (publicznych) odbiornika używa internetowy modułu równoważenia obciążenia i jest skojarzony z publicznych wirtualnych IP (VIP), który jest dostępny za pośrednictwem Internetu. Odbiornik wewnętrzny używa wewnętrznego modułu równoważenia obciążenia i obsługuje tylko klientów w ramach tej samej sieci wirtualnej. W obu załadować typu modułu równoważenia, należy włączyć bezpośredniego zwrotu serwera. 

Jeśli grupy dostępności obejmujących wiele podsieci platformy Azure (na przykład wdrożenia, które przecina regiony platformy Azure), musi zawierać ciąg połączenia klienta "**MultisubnetFailover = True**". W efekcie próby połączenia równoległe w replikach w różnych podsieciach. Aby uzyskać instrukcje na temat konfigurowania odbiornik zobacz

* [Skonfiguruj odbiornik ILB dla grupy dostępności na platformie Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Skonfiguruj odbiornik zewnętrzny dla grup dostępności w systemie Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Można nadal połączenie z każdą replikę dostępności oddzielnie łącząc się bezpośrednio do wystąpienia usługi. Ponadto ponieważ grupy dostępności są zgodne z klientów dublowania bazy danych, możesz nawiązać połączenie repliki dostępności, takich jak dublowania partnerów, jak długo repliki są skonfigurowane podobne do funkcji dublowania baz danych bazy danych:

* Jedna replika podstawowa i jedna replika pomocnicza
* Replika pomocnicza została skonfigurowana jako bez możliwości odczytu (**czytelny dodatkowej** ustawioną opcję **nr**)

Przykład parametry połączenia klienta, które odpowiada tej konfiguracji podobnych funkcji dublowania bazy danych za pomocą ADO.NET lub SQL Server Native Client znajduje się poniżej:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Aby uzyskać więcej informacji dotyczących łączności klienta Zobacz:

* [Przy użyciu słowa kluczowe parametrów połączenia w usłudze SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Łączenie klientów (program SQL Server) sesji dublowania bazy danych](https://technet.microsoft.com/library/ms175484.aspx)
* [Łączenie z odbiornika grupy dostępności hybrydowy IT](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Odbiorniki grupy dostępności, łączności klienta i pracy awaryjnej aplikacji (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Przy użyciu parametrów połączenia dublowania bazy danych z grupy dostępności](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Opóźnienie sieci hybrydowy IT
Należy wdrożyć rozwiązanie HADR przy założeniu, że mogą być okresów z sieci wysokiego opóźnienia między siecią lokalną a Azure. W przypadku wdrażania replik na platformie Azure, należy użyć zatwierdzania asynchronicznego zamiast zatwierdzanie synchroniczne dla trybu synchronizacji. Podczas wdrażania serwerów dublowania bazy danych zarówno lokalnie i na platformie Azure, użyj trybu wysokiej wydajności, zamiast trybu wysokiego bezpieczeństwa.

### <a name="geo-replication-support"></a>Replikacja geograficzna pomocy technicznej
Replikacja geograficzna, w przypadku dysków Azure nie obsługuje plików danych i plik dziennika dotyczący tej samej bazy danych, aby były przechowywane na oddzielnych dyskach. GRS są replikowane zmian na każdym dysku niezależnie i asynchronicznie. Ten mechanizm gwarantuje kolejności zapisu w ramach jednego dysku na kopii replikacją geograficzną, a nie na replikacją geograficzną kopie wiele dysków. Konfigurując bazę danych do przechowywania jego plików danych i jego pliku dziennika na oddzielnych dyskach, dyski odzyskany po awarii może zawierać aktualniejszej kopii pliku danych niż plik dziennika, który dzieli zapisu z wyprzedzeniem logowania programu SQL Server i właściwości ACID transakcji. Jeśli nie masz możliwość wyłączenia — replikacja geograficzna na koncie magazynu, należy przechowywać wszystkie pliki danych i dziennika określonej bazy danych na tym samym dysku. Jeśli należy użyć więcej niż jednego dysku ze względu na rozmiar bazy danych, należy wdrożyć jeden z wymienionych powyżej, aby zapewnić nadmiarowość danych rozwiązania odzyskiwania po awarii.

## <a name="next-steps"></a>Kolejne kroki
Jeśli musisz utworzyć maszynę wirtualną platformy Azure z programem SQL Server, zobacz [inicjowania obsługi maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

Aby uzyskać najlepszą wydajność z programu SQL Server uruchomionego na maszynie Wirtualnej platformy Azure, zobacz wskazówki zawarte w [wydajności najlepsze rozwiązania dotyczące programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Do innych tematów związanych z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Inne zasoby
* [Instalowanie nowego lasu usługi Active Directory na platformie Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Tworzenie klastra trybu Failover dla grupy dostępności w maszynie Wirtualnej platformy Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

