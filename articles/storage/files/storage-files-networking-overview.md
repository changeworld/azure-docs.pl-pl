---
title: Zagadnienia dotyczące sieci Azure Files | Microsoft Docs
description: Omówienie opcji sieciowych dla Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141794"
---
# <a name="azure-files-networking-considerations"></a>Zagadnienia dotyczące sieci Azure Files 
Możesz połączyć się z udziałem plików platformy Azure na dwa sposoby:

- Uzyskiwanie dostępu do udziału bezpośrednio za pośrednictwem protokołów SMB lub FileREST. Ten wzorzec dostępu jest używany przede wszystkim wtedy, gdy można wyeliminować dowolną liczbę serwerów lokalnych.
- Tworzenie pamięci podręcznej udziału plików platformy Azure na serwerze lokalnym z Azure File Sync i uzyskiwanie dostępu do danych udziału plików z serwera lokalnego za pomocą wybranego protokołu (SMB, NFS, FTPS itp.) dla przypadku użycia. Ten wzorzec dostępu jest przydatny, ponieważ łączy się z najlepszą z nich zarówno w przypadku lokalnego, jak i skalowalnych usług, takich jak Azure Backup.

W tym artykule opisano sposób konfigurowania sieci w przypadku, gdy przypadek użycia wywołuje bezpośrednio dostęp do udziału plików platformy Azure, a nie za pomocą Azure File Sync. Aby uzyskać więcej informacji na temat zagadnień sieciowych dotyczących wdrażania Azure File Sync, zobacz [konfigurowanie Azure File Sync serwera proxy i ustawień zapory](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Ustawienia konta magazynu
Konto magazynu to konstrukcja zarządzania, która reprezentuje udostępnioną pulę magazynu, w którym można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Konta usługi Azure Storage udostępniają dwa podstawowe zestawy ustawień do zabezpieczenia sieci: szyfrowanie w ramach przesyłania i zapór oraz sieci wirtualne (sieci wirtualnych).

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Domyślnie wszystkie konta usługi Azure Storage mają włączone szyfrowanie podczas przesyłania. Oznacza to, że podczas instalowania udziału plików przez protokół SMB lub uzyskiwania dostępu do niego za pośrednictwem protokołu FileREST (na przykład za pośrednictwem Azure Portal, PowerShell/interfejsu wiersza polecenia lub zestawów SDK platformy Azure), Azure Files będzie zezwalać tylko na połączenie, jeśli zostało wykonane przy użyciu protokołu SMB 3.0 + z szyfrowaniem lub HTTPS. Klienci, którzy nie obsługują protokołu SMB 3,0 ani klientów obsługujących protokół SMB 3,0, ale nie szyfrowania SMB, nie będą mogli zainstalować udziału plików platformy Azure, jeśli szyfrowanie jest włączone. Więcej informacji o tym, które systemy operacyjne obsługują protokół SMB 3,0 z szyfrowaniem, można znaleźć w naszej szczegółowej dokumentacji dotyczącej [systemów Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)i [Linux](storage-how-to-use-files-linux.md). Wszystkie bieżące wersje programu PowerShell, interfejsu wiersza polecenia i zestawów SDK obsługują protokół HTTPS.  

Można wyłączyć szyfrowanie podczas przesyłania dla konta usługi Azure Storage. Gdy szyfrowanie jest wyłączone, Azure Files również zezwala na SMB 2,1, SMB 3,0 bez szyfrowania i nieszyfrowane wywołania interfejsu API FileREST za pośrednictwem protokołu HTTP. Podstawowym powodem wyłączenia szyfrowania podczas przesyłania jest obsługa starszej aplikacji, która musi być uruchomiona w starszym systemie operacyjnym, takim jak Windows Server 2008 R2 lub starsza dystrybucja systemu Linux. Azure Files zezwala tylko na połączenia SMB 2,1 w tym samym regionie świadczenia usługi Azure co udział plików platformy Azure; klient SMB 2,1 spoza regionu platformy Azure udziału plików platformy Azure, na przykład lokalnie lub w innym regionie świadczenia usługi Azure, nie będzie mógł uzyskać dostępu do udziału plików.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Zapory i sieci wirtualne 
Zapora to zasada sieciowa, do której żądania mogą uzyskiwać dostęp do udziałów plików platformy Azure i innych zasobów magazynu na koncie magazynu. Po utworzeniu konta magazynu z domyślnymi ustawieniami sieci nie jest ono ograniczone do określonej sieci i dlatego jest dostępne w Internecie. Nie oznacza to, że wszyscy użytkownicy Internetu mogą uzyskać dostęp do danych przechowywanych w udziałach plików platformy Azure hostowanych na koncie magazynu, ale zamiast tego konto magazynu będzie akceptować autoryzowane żądania z dowolnej sieci. Żądania mogą być autoryzowane przy użyciu klucza konta magazynu, tokenu sygnatury dostępu współdzielonego (SAS) (tylko FileREST) lub z podmiotem zabezpieczeń Active Directory. 

Zasady zapory dla konta magazynu mogą służyć do ograniczania dostępu do określonych adresów IP lub zakresów lub do sieci wirtualnej. Ogólnie rzecz biorąc, większość zasad zapory dla konta magazynu ogranicza dostęp sieciowy do sieci wirtualnej. 

[Sieć wirtualna](../../virtual-network/virtual-networks-overview.md)lub wirtualna jest podobna do tradycyjnej sieci, którą można obsługiwać we własnym centrum danych. Umożliwia ona tworzenie bezpiecznego kanału komunikacyjnego dla zasobów platformy Azure, takich jak udziały plików platformy Azure, maszyny wirtualne, bazy danych SQL itp., aby komunikować się ze sobą. Podobnie jak konto usługi Azure Storage lub maszyna wirtualna platformy Azure, Sieć wirtualna jest zasobem platformy Azure wdrożonym w grupie zasobów. W przypadku dodatkowej konfiguracji sieci usługa Azure sieci wirtualnych może być również podłączona do sieci lokalnych.

Gdy do sieci wirtualnej dodawane są zasoby, takie jak maszyna wirtualna platformy Azure, interfejs sieci wirtualnej (NIC) dołączony do maszyny wirtualnej jest ograniczony do tego celu. Jest to możliwe, ponieważ maszyny wirtualne platformy Azure są zwirtualizowanymi komputerami, które są dostępne dla kart interfejsu sieciowego. Usługa Virtual Machines jest oferowana jako część usługi infrastruktura jako usługa lub IaaS. Ponieważ udziały plików platformy Azure to bezserwerowe udziały plików, nie mają karty sieciowej umożliwiającej dodanie do sieci wirtualnej. Jest to inny sposób, Azure Files są oferowane w ramach platformy Azure jako usługi lub PaaS. Aby umożliwić korzystanie z konta magazynu jako części sieci wirtualnej, platforma Azure obsługuje koncepcję dla usług PaaS Services nazywanych punktami końcowymi usługi. Punkt końcowy usługi umożliwia PaaS usług, które są częścią sieci wirtualnej. Aby dowiedzieć się więcej o punktach końcowych usługi, zobacz [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md).

Konto magazynu można dodać do co najmniej jednej sieci wirtualnej. Aby dowiedzieć się więcej na temat dodawania konta magazynu do sieci wirtualnej lub konfigurowania innych ustawień zapory, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Sieć platformy Azure
Domyślnie usługi platformy Azure, w tym Azure Files, są dostępne przez Internet. Ponieważ ruch przychodzący do konta magazynu jest szyfrowany, a instalacje SMB 2,1 nigdy nie są dozwolone poza regionem świadczenia usługi Azure), nie ma żadnego zabezpieczenia w zakresie uzyskiwania dostępu do udziałów plików platformy Azure przez Internet. Na podstawie zasad lub unikatowych wymagań prawnych organizacji może być wymagana bardziej restrykcyjna komunikacja z platformą Azure, a w związku z tym platforma Azure oferuje kilka sposobów ograniczenia ruchu spoza platformy Azure do Azure Files. Po uzyskaniu dostępu do udziału plików platformy Azure można zabezpieczyć sieć przy użyciu następujących ofert usług:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Brama sieci VPN jest określonym typem bramy usługi Virtual Network, która jest używana do wysyłania zaszyfrowanego ruchu między siecią wirtualną platformy Azure a alternatywną lokalizacją (na przykład lokalnie) przez Internet. VPN Gateway platformy Azure to zasób platformy Azure, który można wdrożyć w grupie zasobów obok konta magazynu lub innych zasobów platformy Azure. Bramy sieci VPN uwidaczniają dwa różne typy połączeń:
    - Połączenia bramy [sieci VPN typu punkt-lokacja (P2S)](../../vpn-gateway/point-to-site-about.md) , które są połączeniami sieci VPN między platformą Azure i pojedynczym klientem. To rozwiązanie jest szczególnie przydatne w przypadku urządzeń, które nie są częścią sieci lokalnej w organizacji, takiej jak Telepracownicy, którzy chcą móc instalować udział plików platformy Azure z domu, kawiarnia lub hotelu w podróży. Aby można było użyć połączenia sieci VPN P2S z Azure Files, należy skonfigurować połączenie sieci VPN P2S dla każdego klienta, który chce nawiązać połączenie. 
    - [Sieci VPN typu lokacja-lokacja (S2S)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), które są połączeniami sieci VPN między platformą Azure i siecią organizacji. Połączenie sieci VPN S2S umożliwia skonfigurowanie połączenia sieci VPN raz dla serwera sieci VPN lub urządzenia hostowanego w sieci organizacji, a nie dla każdego urządzenia klienckiego, które wymaga dostępu do udziału plików platformy Azure.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), który umożliwia utworzenie zdefiniowanej trasy między platformą Azure i siecią lokalną, która nie przechodzi przez Internet. Ponieważ ExpressRoute zapewnia dedykowaną ścieżkę między lokalnym centrum danych a platformą Azure, ExpressRoute może być przydatne, gdy wydajność sieci jest uwzględniana. ExpressRoute jest również dobrym rozwiązaniem, gdy zasady lub wymagania prawne organizacji wymagają deterministycznej ścieżki do zasobów w chmurze.

## <a name="securing-access-from-on-premises"></a>Zabezpieczanie dostępu z poziomu lokalnego 
W przypadku migrowania udziałów plików ogólnego przeznaczenia (dla takich elementów, jak dokumenty pakietu Office, plików PDF, dokumentów CAD itp.) do Azure Files użytkownicy zazwyczaj będą nadal musieli uzyskać dostęp do swoich plików z urządzeń lokalnych, takich jak stacje robocze, laptopy i tablety. Główne zagadnienia dotyczące udziału plików ogólnego przeznaczenia polega na tym, jak użytkownicy lokalni mogą bezpiecznie uzyskać dostęp do swoich udziałów plików za pomocą Internetu lub sieci WAN.

Najprostszym sposobem uzyskania dostępu do udziału plików platformy Azure z lokalnego programu jest otwarcie sieci lokalnej na porcie 445, porcie używanym przez protokół SMB i zainstalowanie ścieżki UNC dostarczonej przez Azure Portal. Nie wymaga to żadnej specjalnej sieci. Wielu klientów zniechęcić otworzyć port 445 ze względu na nieaktualne wskazówki dotyczące zabezpieczeń w odróżnieniu od protokołu SMB 1,0, którego firma Microsoft nie uważa za bezpieczny protokół internetowy. Azure Files nie implementuje protokołu SMB 1,0. 

Protokół SMB 3,0 został zaprojektowany z wyraźnym wymaganiem w przypadku internetowych protokołów udziałów plików. W związku z tym, w przypadku korzystania z protokołu SMB 3.0 lub z perspektywy sieci komputerowej, otwarcie portu 445 nie różni się od otwarcia portu 443, port używany do połączeń HTTPS. Zamiast blokować port 445, aby zapobiec niezabezpieczonemu ruchowi SMB 1,0, firma Microsoft zaleca następujące czynności:

> [!Important]  
> Nawet jeśli zdecydujesz, że port 445 został zamknięty dla ruchu wychodzącego, firma Microsoft zaleca, aby w celu usunięcia protokołu SMB 1,0 ze środowiska był nadal zalecane wykonanie tych czynności.

1. Upewnij się, że protokół SMB 1,0 jest usuwany lub wyłączony na urządzeniach organizacji. Wszystkie obecnie obsługiwane wersje systemu Windows i systemu Windows Server obsługują usunięcie lub wyłączenie protokołu SMB 1,0 i począwszy od systemu Windows 10, wersja 1709, protokół SMB 1,0 nie jest domyślnie zainstalowany w systemie Windows. Aby dowiedzieć się więcej o tym, jak wyłączyć protokół SMB 1,0, zobacz nasze strony specyficzne dla systemu operacyjnego:
    - [Zabezpieczanie systemu Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Zabezpieczanie systemu Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Upewnij się, że żadne produkty w organizacji nie wymagają protokołu SMB 1,0 i usuń te, które to zrobią. Utrzymujemy pakiet wydawania [produktów SMB1](https://aka.ms/stillneedssmb1), który zawiera wszystkie produkty pierwszej i innej firmy znane firmie Microsoft w celu wymagania protokołu SMB 1,0. 
1. Obowiązkowe Używaj zapory innej firmy z siecią lokalną w organizacji, aby zapobiec ruchowi SMB 1,0.

Jeśli organizacja wymaga blokowania portu 445 dla zasad lub regulacji, możesz użyć platformy Azure VPN Gateway lub ExpressRoute do tunelowania ruchu przez port 443. Aby dowiedzieć się więcej na temat konkretnych kroków wdrażania, zobacz nasze szczegółowe instrukcje:
- [Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z usługą Azure Files](storage-files-configure-s2s-vpn.md)
- [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użytku z usługą Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z usługą Azure Files](storage-files-configure-p2s-vpn-linux.md)

Twoja organizacja może mieć dodatkowe wymagania, które ruch wychodzący z lokacji lokalnej musi być zgodny ze deterministyczną ścieżką do zasobów w chmurze. Jeśli tak, ExpressRoute jest w stanie obsłużyć to wymaganie.

## <a name="securing-access-from-cloud-resources"></a>Zabezpieczanie dostępu z zasobów w chmurze
Ogólnie rzecz biorąc, gdy aplikacja lokalna zostanie podniesiona i przesunięta do chmury, aplikacja i dane aplikacji są przenoszone w tym samym czasie. Oznacza to, że podstawowe zagadnienia związane z przebiciem i przesunięciami polega na zablokowaniu dostępu do udziału plików platformy Azure do określonych maszyn wirtualnych lub usług platformy Azure, które wymagają dostępu do udziału plików. 

Możesz użyć sieci wirtualnych, aby ograniczyć, które maszyny wirtualne lub inne zasoby platformy Azure mogą nawiązywać połączenia sieciowe (instalacje SMB lub wywołania interfejsu API REST do udziału plików platformy Azure). Zawsze zalecamy umieszczenie udziału plików platformy Azure w sieci wirtualnej, Jeśli zezwolisz na niezaszyfrowaną ruch do konta magazynu. W przeciwnym razie bez względu na to, czy należy używać sieci wirtualnych, jest to decyzja, która powinna być zgodna z wymaganiami biznesowymi i zasadami organizacji.

Główną przyczyną zezwolenia na niezaszyfrowany ruch do udziału plików platformy Azure jest obsługa systemu Windows Server 2008 R2, Windows 7 lub innego starszego systemu operacyjnego, który uzyskuje dostęp do udziału plików platformy Azure przy użyciu protokołu SMB 2,1 (lub SMB 3,0 bez szyfrowania dla niektórych dystrybucji systemu Linux). Nie zaleca się używania protokołu SMB 2,1 ani protokołu SMB 3,0 bez szyfrowania w systemach operacyjnych, które obsługują protokół SMB 3.0 + z szyfrowaniem.

## <a name="see-also"></a>Zobacz także
- [Omówienie usługi Azure Files](storage-files-introduction.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)