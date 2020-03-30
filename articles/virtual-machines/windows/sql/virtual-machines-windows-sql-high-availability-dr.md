---
title: Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server | Dokumenty firmy Microsoft
description: Omówienie różnych typów strategii HADR dla programu SQL Server uruchomionych w maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249765"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server na maszynach wirtualnych platformy Azure

Maszyny wirtualne platformy Microsoft Azure (maszyny wirtualne) z programem SQL Server mogą pomóc obniżyć koszt rozwiązania bazy danych o wysokiej dostępności i odzyskiwania po awarii (HADR). Większość rozwiązań SQL Server HADR są obsługiwane na maszynach wirtualnych platformy Azure, zarówno jako rozwiązania tylko platformy Azure, jak i jako rozwiązania hybrydowe. W rozwiązaniu tylko do platformy Azure cały system HADR działa na platformie Azure. W konfiguracji hybrydowej część rozwiązania jest uruchamiana na platformie Azure, a druga część jest uruchamiana lokalnie w organizacji. Elastyczność środowiska platformy Azure umożliwia częściowe lub całkowite przejście na platformę Azure w celu spełnienia wymagań dotyczących budżetu i usługi HADR w systemach bazy danych programu SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Zrozumienie potrzeby rozwiązania HADR
To do Ciebie, aby upewnić się, że system bazy danych posiada możliwości HADR, że umowa na poziomie usługi (SLA) wymaga. Fakt, że platforma Azure zapewnia mechanizmy wysokiej dostępności, takie jak gojenie usług w chmurze i wykrywanie odzyskiwania awarii dla maszyn wirtualnych, nie gwarantuje, że można spełnić żądaną umowy SLA. Mechanizmy te chronią wysoką dostępność maszyn wirtualnych, ale nie wysoką dostępność programu SQL Server działającego wewnątrz maszyn wirtualnych. Jest możliwe dla wystąpienia programu SQL Server zakończyć się niepowodzeniem, gdy maszyna wirtualna jest w trybie online i w dobrej kondycji. Ponadto nawet mechanizmy wysokiej dostępności dostarczane przez platformę Azure umożliwiają przestoje maszyn wirtualnych z powodu zdarzeń, takich jak odzyskiwanie z powodu awarii oprogramowania lub sprzętu oraz uaktualnień systemu operacyjnego.

Ponadto magazyn geograficznie nadmiarowy (GRS) na platformie Azure, który jest implementowany z funkcją o nazwie replikacji geograficznej, może nie być odpowiednie rozwiązanie odzyskiwania po awarii dla baz danych. Ponieważ replikacja geograficzna wysyła dane asynchronicznie, ostatnie aktualizacje mogą zostać utracone w przypadku awarii. Więcej informacji dotyczących ograniczeń replikacji geograficznej jest ujętych w sekcji [Replikacja geograficzna, która nie jest obsługiwana dla plików danych i dzienników na oddzielnych dyskach.](#geo-replication-support)

## <a name="hadr-deployment-architectures"></a>Architektury wdrażania HADR
Sql Server HADR technologie, które są obsługiwane na platformie Azure obejmują:

* [Zawsze włączone grupy dostępności](https://technet.microsoft.com/library/hh510230.aspx)
* [Zawsze włączone wystąpienia klastra trybu failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Wysyłka dziennika](https://technet.microsoft.com/library/ms187103.aspx)
* [Tworzenie kopii zapasowych i przywracanie programu SQL Server za pomocą usługi Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx)
* [Dublowanie bazy danych](https://technet.microsoft.com/library/ms189852.aspx) — przestarzałe w programie SQL Server 2016

Istnieje możliwość łączenia technologii razem, aby zaimplementować rozwiązanie PROGRAMU SQL Server, które ma zarówno wysoką dostępność, jak i funkcje odzyskiwania po awarii. W zależności od używanej technologii wdrożenie hybrydowe może wymagać tunelu sieci VPN z siecią wirtualną platformy Azure. Poniższe sekcje przedstawiają niektóre przykładowe architektury wdrażania.

## <a name="azure-only-high-availability-solutions"></a>Tylko do platformy Azure: rozwiązania o wysokiej dostępności

Możesz mieć rozwiązanie o wysokiej dostępności dla programu SQL Server na poziomie bazy danych z grupami zawsze na dostępności — nazywane grupami dostępności. Można również utworzyć rozwiązanie o wysokiej dostępności na poziomie wystąpienia za pomocą wystąpień klastra trybu failover zawsze włączone — wystąpień klastra trybu failover. Aby uzyskać dodatkową nadmiarowość, można utworzyć nadmiarowość na obu poziomach, tworząc grupy dostępności w wystąpieniach klastra trybu failover. 

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności uruchomione na maszynach wirtualnych platformy Azure w tym samym regionie zapewniają wysoką dostępność.  Należy skonfigurować maszynę wirtualną kontrolera domeny, ponieważ klastrowanie trybu failover systemu Windows wymaga domeny usługi Active Directory.<br/><br/> Aby uzyskać większą nadmiarowość i dostępność, maszyny wirtualne platformy Azure można wdrożyć w różnych [strefach dostępności,](../../../availability-zones/az-overview.md) zgodnie z [omówieniem grupy dostępności.](virtual-machines-windows-portal-sql-availability-group-overview.md) Jeśli maszyny wirtualne programu SQL Server w grupie dostępności są wdrażane w strefach dostępności, użyj [standardowego modułu równoważenia obciążenia](../../../load-balancer/load-balancer-standard-overview.md) dla odbiornika, zgodnie z tymi artykułami —[szablonami szybki startów platformy](virtual-machines-windows-sql-availability-group-quickstart-template.md)Azure SQL [VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & platformy Azure .<br/> ![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności na platformie Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Wystąpienia klastra trybu failover** |Wystąpienia klastra trybu failover (FCI), które wymagają udostępnionego magazynu, można utworzyć na 4 różne sposoby.<br/><br/>1. Dwuwęzłyowy klaster trybu failover działający na maszynach wirtualnych platformy Azure z dołączonym magazynem przy użyciu [miejsca do magazynowania w systemie Windows Server 2016 Direct \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) w celu zapewnienia opartej na oprogramowaniu wirtualnej sieci SAN.<br/><br/> 2. Klaster trybu failover z dwoma węzłami uruchomiony na maszynach wirtualnych platformy Azure przy użyciu [udziału plików premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md). Udziały plików premium są współużytkowane przez dysk SSD stale-niskich opóźnieniach udziałów plików, które są w pełni obsługiwane do użytku z wystąpieniem klastra trybu failover.<br/><br/>3. Klaster trybu failover z dwoma węzłami uruchomiony na maszynach wirtualnych platformy Azure z magazynem obsługiwanym przez rozwiązanie klastrowania innych firm. W konkretnym przykładzie, który używa SIOS DataKeeper, zobacz [Wysoka dostępność dla udziału plików przy użyciu klastra trybu failover i oprogramowania innej firmy SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. Klaster trybu failover z dwoma węzłami uruchomiony na maszynach wirtualnych platformy Azure ze zdalnym udostępnionym magazynem bloków iSCSI za pośrednictwem usługi ExpressRoute. Na przykład NetApp Private Storage (NPS) udostępnia obiekt docelowy iSCSI za pośrednictwem usługi ExpressRoute z Equinix do maszyn wirtualnych platformy Azure.<br/><br/>W przypadku rozwiązań przeznaczonych do udostępniania magazynu i replikacji danych innych firm należy skontaktować się z dostawcą w przypadku wszelkich problemów związanych z uzyskiwaniem dostępu do danych w umorzyniu awaryjnym.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Tylko do platformy Azure: rozwiązania odzyskiwania po awarii
Możesz mieć rozwiązanie odzyskiwania po awarii dla baz danych programu SQL Server na platformie Azure przy użyciu grup dostępności, dublowanie bazy danych lub tworzenie kopii zapasowych i przywracanie za pomocą obiektów blob magazynu.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności działające w wielu centrach danych na maszynach wirtualnych platformy Azure w celu odzyskiwania po awarii. To rozwiązanie międzyregionowe chroni przed całkowitą awarią lokacji. <br/> ![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>W obrębie regionu wszystkie repliki powinny znajdować się w ramach tej samej usługi w chmurze i tej samej sieci wirtualnej. Ponieważ każdy region będzie miał oddzielną sieci wirtualną, te rozwiązania wymagają łączności sieci wirtualnej do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną przy użyciu portalu Azure.](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) Aby uzyskać szczegółowe [instrukcje, zobacz Konfigurowanie grupy dostępności programu SQL Server na maszynach wirtualnych platformy Azure w różnych regionach](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Dublowanie bazy danych** |Dublowanie i dublowanie oraz serwery działające w różnych centrach danych do odzyskiwania po awarii. Należy wdrożyć przy użyciu certyfikatów serwera. Dublowanie bazy danych programu SQL Server nie jest obsługiwane dla programu SQL Server 2008 ani programu SQL Server 2008 R2 na maszynie Wirtualnej platformy Azure. <br/>![Dublowanie bazy danych](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Tworzenie kopii zapasowych i przywracanie za pomocą usługi Azure Blob Storage Service** |Produkcyjne bazy danych kopii zapasowej bezpośrednio do magazynu obiektów blob w innym centrum danych do odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server w maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikowanie i przetwarzanie awaryjne programu SQL Server na platformę Azure za pomocą usługi Azure Site Recovery** |Produkcja programu SQL Server jednego centrum danych platformy Azure replikowanego bezpośrednio do usługi Azure Storage w różnych centrach danych platformy Azure w celu odzyskiwania po awarii.<br/>![Replikowanie przy użyciu usługi Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [Ochrona programu SQL Server przy użyciu odzyskiwania po awarii programu SQL Server i usługi Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrydowy IT: Rozwiązania do odzyskiwania po awarii
Rozwiązanie do odzyskiwania po awarii dla baz danych programu SQL Server w środowisku hybrydowym-IT przy użyciu grup dostępności, dublowania bazy danych, wysyłania dzienników oraz tworzenia kopii zapasowych i przywracania kopii zapasowych za pomocą magazynu blogu platformy Azure.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Niektóre repliki dostępności uruchomione na maszynach wirtualnych platformy Azure i innych replikach działających lokalnie w celu odzyskiwania po awarii w wielu lokacjach. Lokacja produkcyjna może być lokalna lub w centrum danych platformy Azure.<br/>![Grupy dostępności](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Ponieważ wszystkie repliki dostępności muszą znajdować się w tym samym klastrze trybu failover, klaster musi obejmować obie sieci (klaster trybu failover wielu podsieci). Ta konfiguracja wymaga połączenia sieci VPN między platformą Azure a siecią lokalną.<br/><br/>Aby pomyślnie odzyskać bazy danych po awarii, należy również zainstalować kontroler domeny repliki w lokacji odzyskiwania po awarii.<br/><br/>Kreatora dodawania replik w programie SSMS można użyć kreatora dodawania repliki, aby dodać replikę platformy Azure do istniejącej grupy dostępności zawsze włączone. Aby uzyskać więcej informacji, zobacz Samouczek: Rozszerzanie grupy zawsze dostępnej dostępności na platformę Azure. |
| **Dublowanie bazy danych** |Jeden partner działający na maszynie wirtualnej platformy Azure, a drugi uruchomiony lokalnie do odzyskiwania po awarii między lokacjami przy użyciu certyfikatów serwera. Partnerzy nie muszą znajdować się w tej samej domenie usługi Active Directory i nie jest wymagane połączenie sieci VPN.<br/>![Dublowanie bazy danych](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Inny scenariusz dublowania bazy danych obejmuje jednego partnera działającego na maszynie wirtualnej platformy Azure, a drugiego uruchomionego lokalnie w tej samej domenie usługi Active Directory do odzyskiwania po awarii między lokacjami. Wymagane jest [połączenie sieci VPN między siecią wirtualną platformy Azure a siecią lokalną.](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br/><br/>Aby pomyślnie odzyskać bazy danych po awarii, należy również zainstalować kontroler domeny repliki w lokacji odzyskiwania po awarii. Dublowanie bazy danych programu SQL Server nie jest obsługiwane dla programu SQL Server 2008 ani programu SQL Server 2008 R2 na maszynie Wirtualnej platformy Azure. |
| **Wysyłka dziennika** |Jeden serwer uruchomiony na maszynie wirtualnej platformy Azure, a drugi uruchomiony lokalnie w celu odzyskiwania po awarii między lokacjami. Wysyłka dziennika zależy od udostępniania plików systemu Windows, dlatego wymagane jest połączenie sieci VPN między siecią wirtualną platformy Azure a siecią lokalną.<br/>![Wysyłka dziennika](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Aby pomyślnie odzyskać bazy danych po awarii, należy również zainstalować kontroler domeny repliki w lokacji odzyskiwania po awarii. |
| **Tworzenie kopii zapasowych i przywracanie za pomocą usługi Azure Blob Storage Service** |Lokalne produkcyjne bazy danych kopii zapasowej bezpośrednio do magazynu obiektów blob platformy Azure w celu odzyskania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie dla programu SQL Server w maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikowanie i przetwarzanie awaryjne programu SQL Server na platformę Azure za pomocą usługi Azure Site Recovery** |Lokalna produkcja SQL Server replikowane bezpośrednio do usługi Azure Storage w celu odzyskiwania po awarii.<br/>![Replikowanie przy użyciu usługi Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [Ochrona programu SQL Server przy użyciu odzyskiwania po awarii programu SQL Server i usługi Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Bezpłatna replika odzyskiwania po awarii na platformie Azure

Jeśli masz [pakiet Software Assurance,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)możesz zaimplementować plany odzyskiwania po awarii hybrydowej (DR) za pomocą programu SQL Server bez ponoszenia dodatkowych kosztów licencjonowania dla pasywnego wystąpienia odzyskiwania po awarii.

Na poniższej ilustracji instalator używa programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure, wykorzystując 12 rdzeni jako replikę odzyskiwania po awarii dla lokalnego wdrożenia programu SQL Server przy użyciu 12 rdzeni. W przeszłości należy licencjonować 12 rdzeni programu SQL Server dla środowiska lokalnego i wdrożenia maszyny wirtualnej platformy Azure. Nowa korzyść oferuje pasywne korzyści repliki uruchomione na maszynie wirtualnej platformy Azure. Teraz trzeba będzie licencjonować tylko 12 rdzeni programu SQL Server z systemem lokalnym, o ile spełnione są kryteria odzyskiwania po awarii dla repliki pasywnej na maszynie wirtualnej platformy Azure.

![Bezpłatna replika odzyskiwania po awarii na platformie Azure](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

Aby uzyskać więcej informacji, zobacz [Postanowienia licencyjne dotyczące produktów](https://www.microsoft.com/licensing/product-licensing/products). 

Aby włączyć tę korzyść, przejdź do [zasobu maszyny wirtualnej programu SQL Server](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource), wybierz pozycję **Konfiguruj** w obszarze Ustawienia, a następnie wybierz opcję **Odzyskiwanie po awarii** w obszarze **Licencja programu SQL Server**. Zaznacz to pole wyboru, aby potwierdzić, że ta maszyna wirtualna programu SQL Server będzie używana jako replika pasywna, a następnie wybierz pozycję **Zastosuj,** aby zapisać ustawienia. 

![Konfigurowanie repliki odzyskiwania po awarii na platformie Azure](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Ważne zagadnienia dotyczące programu SQL Server HADR na platformie Azure
Maszyny wirtualne platformy Azure, magazyn i sieć mają inne właściwości operacyjne niż lokalna, niewirtualizowane infrastruktury IT. Pomyślna implementacja rozwiązania PROGRAMU SQL SERVER firmy HADR na platformie Azure wymaga zrozumienia tych różnic i zaprojektowania rozwiązania, aby je uwzględnić.

### <a name="high-availability-nodes-in-an-availability-set"></a>Węzły wysokiej dostępności w zestawie dostępności
Zestawy dostępności na platformie Azure umożliwiają umieszczanie węzłów o wysokiej dostępności w oddzielnych domenach błędów (FD) i domenach aktualizacji (UD). Każda maszyna wirtualna w zestawie dostępności ma przypisaną domenę aktualizacji i domenę błędów z odpowiedniej platformy Azure. Ta konfiguracja w centrum danych zapewnia, że podczas zdarzenia planowanej lub nieplanowanej konserwacji dostępna jest co najmniej jedna maszyna wirtualna i spełnia 99,95% umowy SLA platformy Azure. Aby skonfigurować konfigurację wysokiej dostępności, umieść wszystkie uczestniczące maszyny wirtualne SQL w tym samym zestawie dostępności, aby uniknąć utraty aplikacji lub danych podczas zdarzenia konserwacji. Tylko węzły w tej samej usłudze w chmurze mogą uczestniczyć w tym samym zestawie dostępności. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Węzły wysokiej dostępności w strefie dostępności
Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Fizyczne oddzielenie stref dostępności w regionie chroni aplikacje i dane przed awariami centrów danych, zapewniając dostępność co najmniej jednej maszyny wirtualnej i spełnia 99,99% umowy SLA platformy Azure. Aby skonfigurować wysoką dostępność, umieść uczestniczące maszyny wirtualne SQL rozłożone na dostępne strefy dostępności w regionie. Będą dodatkowe opłaty za transfer danych maszyny wirtualnej do maszyny wirtualnej w strefie między dostępnością. Aby uzyskać więcej informacji, zobacz [Strefy dostępności](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Zachowanie klastra trybu failover w sieci platformy Azure
Usługa DHCP niezgodna z zasadami RFC na platformie Azure może spowodować niepowodzenie tworzenia niektórych konfiguracji klastra trybu failover, ponieważ nazwa sieci klastra jest przypisywana zduplikowany adres IP, taki jak ten sam adres IP co jeden z węzłów klastra. Jest to problem podczas implementowania grup dostępności, który zależy od funkcji klastra trybu failover systemu Windows.

Należy wziąć pod uwagę scenariusz, gdy klaster z dwoma węzłami jest tworzony i przenoszony do trybu online:

1. Klaster jest dostarczany w trybie online, a następnie NODE1 żąda dynamicznie przypisanego adresu IP dla nazwy sieci klastra.
2. Usługa DHCP nie podaje żadnego adresu IP innego niż adres IP node1, ponieważ usługa DHCP rozpoznaje, że żądanie pochodzi z samego node1.
3. System Windows wykrywa, że zduplikowany adres jest przypisany zarówno do node1, jak i do nazwy sieciowej klastra trybu failover, a domyślna grupa klastrów nie może przejść do trybu online.
4. Domyślna grupa klastrów przechodzi do node2, który traktuje adres IP NODE1 jako adres IP klastra i przenosi domyślną grupę klastrów w tryb online.
5. Gdy NODE2 próbuje ustanowić łączność z NODE1, pakiety skierowane do NODE1 nigdy nie opuszczają NODE2, ponieważ rozwiązuje adres IP NODE1 do siebie. NODE2 nie może ustanowić łączności z NODE1, a następnie traci kworum i zamyka klaster.
6. W międzyczasie NODE1 może wysyłać pakiety do NODE2, ale NODE2 nie może odpowiedzieć. NODE1 traci kworum i zamyka klaster.

Tego scenariusza można uniknąć, przypisując nieużywane statyczny adres IP, taki jak lokalny adres IP łącza, taki jak 169.254.1.1, do nazwy sieci klastra w celu przełączenia nazwy sieci klastra do trybu online. Aby uprościć ten proces, zobacz [Konfigurowanie klastra trybu failover systemu Windows na platformie Azure w poszukiwaniu grup dostępności](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności na platformie Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Obsługa odbiornika grup dostępności
Detektory grup dostępności są obsługiwane na maszynach wirtualnych platformy Azure z systemami Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Ta obsługa jest możliwa dzięki zastosowaniu punktów końcowych z równoważenia obciążenia włączonych na maszynach wirtualnych platformy Azure, które są węzłami grup dostępności. Należy wykonać specjalne kroki konfiguracji dla detektorów do pracy zarówno dla aplikacji klienckich, które są uruchomione na platformie Azure, jak również tych, które są uruchomione lokalnie.

Istnieją dwie główne opcje konfigurowania odbiornika: zewnętrzny (publiczny) lub wewnętrzny. Odbiornik zewnętrzny (publiczny) korzysta z internetowego modułu równoważenia obciążenia i jest skojarzony z publicznym wirtualnym adresem IP (VIP), który jest dostępny przez Internet. Wewnętrzny odbiornik używa wewnętrznego modułu równoważenia obciążenia i obsługuje tylko klientów w tej samej sieci wirtualnej. Dla obu typów modułu równoważenia obciążenia należy włączyć bezpośrednie zwracanie serwera. 

Jeśli grupa dostępności obejmuje wiele podsieci platformy Azure (takich jak wdrożenie, które przecina regiony platformy Azure), parametry połączenia klienta musi zawierać "**MultisubnetFailover=True**". Powoduje to równoległe próby połączenia z replikami w różnych podsieciach. Aby uzyskać instrukcje dotyczące konfigurowania odbiornika, zobacz

* [Konfigurowanie odbiornika równoważenia obciążenia](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)sieciowego dla grup dostępności na platformie Azure .
* [Konfigurowanie zewnętrznego odbiornika dla grup dostępności na platformie Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Nadal można połączyć się z każdą repliką dostępności oddzielnie, łącząc się bezpośrednio z wystąpieniem usługi. Ponadto ponieważ grupy dostępności są wstecznie zgodne z klientami dublowania bazy danych, można połączyć się z replikami dostępności, takimi jak partnerzy dublowania bazy danych, o ile repliki są skonfigurowane podobnie do dublowania bazy danych:

* Jedna replika podstawowa i jedna replika pomocnicza
* Replika pomocnicza jest skonfigurowana jako nieczytalna **(opcja Czytaj ą pomocniczą** ustawiona na **Nie**)

Przykładowy ciąg połączenia klienta, który odpowiada tej konfiguracji dublowania bazy danych przy użyciu ADO.NET lub klienta macierzystego programu SQL Server, znajduje się poniżej:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Aby uzyskać więcej informacji na temat łączności z klientem, zobacz:

* [Używanie słów kluczowych ciągu połączenia z klientem macierzystym programu SQL Server](https://msdn.microsoft.com/library/ms130822.aspx)
* [Łączenie klientów z sesją dublowania bazy danych (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Łączenie się z odbiornikiem grupy dostępności w hybrydowym it](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Odbiorniki grup dostępności, łączność klienta i praca awaryjna aplikacji (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Używanie ciągów połączeń dublowania bazy danych z grupami dostępności](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Opóźnienie sieci w hybrydowym it
Należy wdrożyć rozwiązanie HADR przy założeniu, że mogą występować okresy czasu z dużym opóźnieniem sieci między siecią lokalną a platformą Azure. Podczas wdrażania replik na platformie Azure należy użyć zatwierdzenia asynchronii zamiast zatwierdzenia synchroniczowego w trybie synchronizacji. Podczas wdrażania serwerów dublowania bazy danych zarówno lokalnie, jak i na platformie Azure, należy użyć trybu wysokiej wydajności zamiast trybu wysokiego bezpieczeństwa.

### <a name="geo-replication-support"></a>Obsługa replikacji geograficznej
Replikacja geograficzna na dyskach platformy Azure nie obsługuje pliku danych i pliku dziennika tej samej bazy danych, które mają być przechowywane na oddzielnych dyskach. GRS replikuje zmiany na każdym dysku niezależnie i asynchronicznie. Mechanizm ten gwarantuje kolejność zapisu w ramach jednego dysku na kopii replikowanej geograficznie, ale nie w kopiach replikowanych geograficznie wielu dysków. Jeśli baza danych zostanie skonfigurowana do przechowywania jej pliku danych i pliku dziennika na oddzielnych dyskach, odzyskane dyski po awarii mogą zawierać bardziej aktualną kopię pliku danych niż plik dziennika, który przerywa dziennik zapisu z wyprzedzeniem w programie SQL Server i właściwości ACID Transakcji. Jeśli nie masz możliwości wyłączenia replikacji geograficznej na koncie magazynu, należy zachować wszystkie pliki danych i dzienników dla danej bazy danych na tym samym dysku. Jeśli musisz użyć więcej niż jednego dysku ze względu na rozmiar bazy danych, należy wdrożyć jedno z rozwiązań odzyskiwania po awarii wymienionych powyżej, aby zapewnić nadmiarowość danych.

## <a name="next-steps"></a>Następne kroki
Jeśli chcesz utworzyć maszynę wirtualną platformy Azure za pomocą programu SQL Server, zobacz [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

Aby uzyskać najlepszą wydajność z programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure, zobacz wskazówki dotyczące [najlepszych rozwiązań dotyczących wydajności programu SQL Server w maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-performance.md)

Aby zapoznać się z innymi tematami związanymi z uruchamianiem programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md)

### <a name="other-resources"></a>Inne zasoby
* [Instalowanie nowego lasu usługi Active Directory na platformie Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Tworzenie klastra trybu failover dla grup dostępności w usłudze Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

