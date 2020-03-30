---
title: Zagadnienia dotyczące sieci usług Azure Files | Dokumenty firmy Microsoft
description: Omówienie opcji sieciowych dla usług Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 383ad5e5063a0a207320a517c34f3b41cc57804a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067152"
---
# <a name="azure-files-networking-considerations"></a>Zagadnienia dotyczące sieci usług Azure Files 
Możesz połączyć się z udziałem plików platformy Azure na dwa sposoby:

- Dostęp do udziału bezpośrednio za pośrednictwem protokołów SMB lub FileREST. Ten wzorzec dostępu jest stosowany przede wszystkim wtedy, gdy można wyeliminować jak najwięcej serwerów lokalnych, jak to możliwe.
- Tworzenie pamięci podręcznej udziału plików platformy Azure na serwerze lokalnym za pomocą usługi Azure File Sync i uzyskiwanie dostępu do danych udziału plików z serwera lokalnego za pomocą wybranego protokołu (SMB, NFS, FTPS itp.) w przypadku użycia. Ten wzorzec dostępu jest przydatny, ponieważ łączy w sobie najlepsze z wydajnością lokalną i skalowania w chmurze i bezserwerowych usług dołączanych, takich jak usługa Azure Backup.

W tym artykule koncentruje się na jak skonfigurować sieci, gdy przypadek użycia wywołuje dostęp do udziału plików platformy Azure bezpośrednio, a nie przy użyciu usługi Azure File Sync. Aby uzyskać więcej informacji na temat zagadnień dotyczących sieci wdrożenia usługi Azure File Sync, zobacz [konfigurowanie ustawień serwera proxy i zapory usługi Azure File Sync](storage-sync-files-firewall-and-proxy.md).

Konfiguracja sieci dla udziałów plików platformy Azure odbywa się na koncie magazynu platformy Azure. Konto magazynu jest konstrukcją zarządzania, która reprezentuje udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Konta magazynu udostępniają wiele ustawień, które pomagają zabezpieczyć dostęp do sieci do udziałów plików: sieciowe punkty końcowe, ustawienia zapory konta magazynu i szyfrowanie podczas przesyłania. 

Przed przeczytaniem tego przewodnika koncepcyjnego zalecamy przeczytanie polecenia [Planowanie wdrożenia usługi Azure Files.](storage-files-planning.md)

## <a name="accessing-your-azure-file-shares"></a>Uzyskiwanie dostępu do udziałów plików platformy Azure
Po wdrożeniu udziału plików platformy Azure w ramach konta magazynu, udział plików jest natychmiast dostępny za pośrednictwem publicznego punktu końcowego konta magazynu. Oznacza to, że uwierzytelnione żądania, takie jak żądania autoryzowane przez tożsamość logowania użytkownika, mogą pochodzić bezpiecznie z platformy Azure lub poza nią. 

W wielu środowiskach klientów początkowe instalowanie udziału plików platformy Azure na lokalnej stacji roboczej zakończy się niepowodzeniem, nawet jeśli instalacje z maszyn wirtualnych platformy Azure powiodą się. Powodem tego jest to, że wiele organizacji i dostawców usług internetowych (ISP) blokuje port używany przez SMB do komunikacji, port 445. Ta praktyka pochodzi ze wskazówek dotyczących zabezpieczeń dotyczących starszych i przestarzałych wersji protokołu SMB. Chociaż SMB 3.0 jest protokołem bezpiecznym przez Internet, starsze wersje SMB, zwłaszcza SMB 1.0 nie są. Udziały plików platformy Azure mogą być dostępne tylko zewnętrznie za pośrednictwem protokołu SMB 3.0 i protokołu FileREST (który jest również protokołem bezpieczeństwa internetowego) za pośrednictwem publicznego punktu końcowego.

Ponieważ najprostszym sposobem uzyskania dostępu do udziału plików platformy Azure z lokalnego jest otwarcie sieci lokalnej do portu 445, firma Microsoft zaleca następujące kroki, aby usunąć SMB 1.0 ze środowiska:

1. Upewnij się, że SMB 1.0 jest usuwany lub wyłączony na urządzeniach organizacji. Wszystkie obecnie obsługiwane wersje systemu Windows i windows server obsługują usuwanie lub wyłączanie pliku SMB 1.0, a począwszy od systemu Windows 10, wersja 1709, SMB 1.0 nie jest domyślnie zainstalowany w systemie Windows. Aby dowiedzieć się więcej o tym, jak wyłączyć SMB 1.0, zobacz nasze strony specyficzne dla systemu operacyjnego:
    - [Zabezpieczanie systemu Windows lub Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Zabezpieczanie linuksa](storage-how-to-use-files-linux.md#securing-linux)
2. Upewnij się, że żadne produkty w organizacji nie wymagają protokołu SMB 1.0 i usuń te, które to robią. Utrzymujemy [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1), który zawiera wszystkie produkty pierwszej i innej firmy znane firmie Microsoft wymagają SMB 1.0. 
3. (Opcjonalnie) Użyj zapory innej firmy z siecią lokalną organizacji, aby zapobiec opuszczeniu granicy organizacji przez ruch SMB 1.0.

Jeśli twoja organizacja wymaga, aby port 445 był blokowany zgodnie z zasadami lub regulacjami, lub twoja organizacja wymaga ruchu na platformie Azure w celu podążania ścieżką deterministyczną, można użyć usługi Azure VPN Gateway lub ExpressRoute do tunelowania ruchu do udziałów plików platformy Azure.

> [!Important]  
> Nawet jeśli zdecydujesz się użyć alternatywnej metody dostępu do udziałów plików platformy Azure, firma Microsoft nadal zaleca usunięcie SMB 1.0 ze środowiska.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunelowanie ruchu przez wirtualną sieć prywatną lub expressroute
Podczas ustanawiania tunelu sieciowego między siecią lokalną a platformą Azure, współuczasz swoją sieć lokalną z jedną lub kilkoma sieciami wirtualnymi na platformie Azure. [Sieć wirtualna](../../virtual-network/virtual-networks-overview.md)lub sieć wirtualna jest podobna do tradycyjnej sieci, która będzie działać lokalnie. Podobnie jak konto magazynu platformy Azure lub maszyna wirtualna platformy Azure, witryna wirtualna jest zasobem platformy Azure, który jest wdrażany w grupie zasobów. 

Usługa Azure Files obsługuje następujące mechanizmy tunelowania ruchu między lokalnymi stacjami roboczymi i serwerami a platformą Azure:

- [Brama sieci VPN platformy Azure:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)Brama sieci VPN to określony typ bramy sieci wirtualnej, który jest używany do wysyłania zaszyfrowanego ruchu między siecią wirtualną platformy Azure a alternatywną lokalizacją (taką jak lokalna) za pośrednictwem Internetu. Brama sieci VPN platformy Azure to zasób platformy Azure, który można wdrożyć w grupie zasobów wraz z kontem magazynu lub innymi zasobami platformy Azure. Bramy sieci VPN udostępniają dwa różne typy połączeń:
    - Połączenia bramy [sieci VPN typu punkt-lokacja (P2S),](../../vpn-gateway/point-to-site-about.md) które są połączeniami sieci VPN między platformą Azure a klientem indywidualnym. To rozwiązanie jest przydatne przede wszystkim w przypadku urządzeń, które nie są częścią lokalnej sieci organizacji, takich jak osoby pracujące zdalnie, które chcą mieć możliwość instalowania udziału plików platformy Azure z domu, kawiarni lub hotelu w podróży. Aby korzystać z połączenia sieci VPN P2S z usługą Azure Files, połączenie sieci VPN P2S musi być skonfigurowane dla każdego klienta, który chce się połączyć. Aby uprościć wdrażanie połączenia sieci VPN P2S, zobacz [Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Windows do użytku z plikami azureowymi](storage-files-configure-p2s-vpn-windows.md) i [konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Linux do użytku z plikami azure](storage-files-configure-p2s-vpn-linux.md).
    - [Sieć VPN lokacja-lokacja (S2S),](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)czyli połączenia sieci VPN między platformą Azure a siecią organizacji. Połączenie sieci VPN S2S umożliwia skonfigurowanie połączenia sieci VPN raz dla serwera sieci VPN lub urządzenia hostowanego w sieci organizacji, zamiast wykonywać dla każdego urządzenia klienckiego, które musi uzyskać dostęp do udziału plików platformy Azure. Aby uprościć wdrażanie połączenia sieci VPN S2S, zobacz [Konfigurowanie sieci VPN lokacja lokacja (S2S) do użytku z usługą Azure Files](storage-files-configure-s2s-vpn.md).
- [Usługa ExpressRoute](../../expressroute/expressroute-introduction.md), która umożliwia utworzenie zdefiniowanej trasy między platformą Azure a siecią lokalną, która nie przechodzi przez Internet. Ponieważ usługa ExpressRoute udostępnia dedykowaną ścieżkę między lokalnym centrum danych a platformą Azure, usługa ExpressRoute może być przydatna, gdy bierze się pod uwagę wydajność sieci. Usługa ExpressRoute jest również dobrym rozwiązaniem, gdy wymagania zasad lub przepisów organizacji wymagają deterministycznej ścieżki do zasobów w chmurze.

Niezależnie od metody tunelowania, której używasz do uzyskiwania dostępu do udziałów plików platformy Azure, potrzebny jest mechanizm zapewniający, że ruch na koncie magazynu przechodzi przez tunel, a nie zwykłe połączenie internetowe. Jest technicznie możliwe do trasy do publicznego punktu końcowego konta magazynu, jednak wymaga to twardego kodowania wszystkich adresów IP dla klastrów magazynu platformy Azure w regionie, ponieważ konta magazynu mogą być przenoszone między klastrami magazynu w dowolnym momencie. Wymaga to również ciągłej aktualizacji mapowań adresów IP, ponieważ nowe klastry są dodawane przez cały czas.

Zamiast twardego kodowania adres IP kont magazynu do reguł routingu sieci VPN, zaleca się użycie prywatnych punktów końcowych, które dają kontu magazynu adres IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ponieważ tworzenie tunelu do platformy Azure ustanawia komunikacji równorzędnej między siecią lokalną i jedną lub więcej sieci wirtualnej, umożliwia to poprawne routingu w sposób trwały.

### <a name="private-endpoints"></a>Prywatne punkty końcowe
Oprócz domyślnego publicznego punktu końcowego dla konta magazynu usługa Azure Files udostępnia opcję, aby mieć jeden lub więcej prywatnych punktów końcowych. Prywatny punkt końcowy jest punktem końcowym, który jest dostępny tylko w ramach sieci wirtualnej platformy Azure. Podczas tworzenia prywatnego punktu końcowego dla konta magazynu konto magazynu pobiera prywatny adres IP z przestrzeni adresowej sieci wirtualnej, podobnie jak lokalny serwer plików lub urządzenie NAS odbiera adres IP w dedykowanym adresie przestrzeni sieci lokalnej. 

Indywidualny prywatny punkt końcowy jest skojarzony z określoną podsiecią sieci wirtualnej platformy Azure. Konto magazynu może mieć prywatne punkty końcowe w więcej niż jednej sieci wirtualnej.

Korzystanie z prywatnych punktów końcowych za pomocą usługi Azure Files umożliwia:
- Bezpiecznie połącz się z udziałami plików platformy Azure z sieci lokalnych przy użyciu połączenia VPN lub Usługi ExpressRoute z prywatną komunikacją równorzędną.
- Zabezpiecz udziały plików platformy Azure, konfigurując zaporę konta magazynu, aby zablokować wszystkie połączenia w publicznym punkcie końcowym. Domyślnie utworzenie prywatnego punktu końcowego nie blokuje połączeń z publicznym punktem końcowym.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając blokowanie eksfiltracji danych z sieci wirtualnej (i granic komunikacji równorzędnej).

Aby utworzyć prywatny punkt końcowy, zobacz [Konfigurowanie prywatnych punktów końcowych dla plików platformy Azure](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Prywatne punkty końcowe i system DNS
Podczas tworzenia prywatnego punktu końcowego domyślnie tworzymy również (lub aktualizujemy `privatelink` istniejącą) prywatną strefę DNS odpowiadającą poddomenie. Ściśle rzecz biorąc, tworzenie prywatnej strefy DNS nie jest wymagane do używania prywatnego punktu końcowego dla konta magazynu, ale jest wysoce zalecane w ogóle i jawnie wymagane podczas instalowania udziału plików platformy Azure z podmiotem użytkownika usługi Active Directory lub uzyskiwaniem dostępu z interfejsu API FileREST.

> [!Note]  
> W tym artykule użyto sufiksu DNS `core.windows.net`konta magazynu dla regionów publicznych platformy Azure . Ten komentarz dotyczy również chmur suwerennych platformy Azure, takich jak chmura azure dla instytucji rządowych i chmura Azure China — po prostu zastąp odpowiednie sufiksy dla danego środowiska. 

W prywatnej strefie DNS tworzymy `storageaccount.privatelink.file.core.windows.net` rekord A i rekord CNAME dla zwykłej nazwy `storageaccount.file.core.windows.net`konta magazynu, który jest zgodny ze wzorcem . Ponieważ twoja prywatna strefa DNS platformy Azure jest połączona z siecią wirtualną zawierającą prywatny punkt końcowy, można obserwować konfigurację DNS, gdy wywołujesz `Resolve-DnsName` polecenie cmdlet z programu PowerShell na maszynie Wirtualnej platformy Azure (alternatywnie `nslookup` w systemach Windows i Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

W tym przykładzie `storageaccount.file.core.windows.net` konto magazynu jest rozpoznawane na prywatny adres IP `192.168.0.4`prywatnego punktu końcowego, który się dzieje .

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Jeśli uruchomisz to samo polecenie z lokalnego, zobaczysz, że ta sama nazwa konta magazynu jest rozpoznawana na publiczny adres IP konta magazynu zamiast; `storageaccount.file.core.windows.net` jest rekordem CNAME dla `storageaccount.privatelink.file.core.windows.net`, który z kolei jest rekordem CNAME dla klastra magazynu platformy Azure obsługującego konto magazynu:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Odzwierciedla to fakt, że konto magazynu może udostępnić zarówno publiczny punkt końcowy, jak i jeden lub więcej prywatnych punktów końcowych. Aby upewnić się, że nazwa konta magazynu jest rozpoznawana na prywatny adres IP prywatnego punktu końcowego, należy zmienić konfigurację na lokalnych serwerach DNS. Można to osiągnąć na kilka sposobów:

- Modyfikowanie pliku hosts na klientach, aby rozwiązać `storageaccount.file.core.windows.net` problem z prywatnym adresem IP żądanego prywatnego punktu końcowego. Jest to zdecydowanie odradzane dla środowisk produkcyjnych, ponieważ trzeba będzie wprowadzić te zmiany do każdego klienta, który chce zainstalować udziały plików platformy Azure i zmiany na koncie magazynu lub prywatnego punktu końcowego nie będą obsługiwane automatycznie.
- Tworzenie rekordu A `storageaccount.file.core.windows.net` dla lokalnych serwerów DNS. Ma to tę zaletę, że klienci w środowisku lokalnym będą mogli automatycznie rozwiązać konto magazynu bez konieczności konfigurowania każdego klienta, jednak to rozwiązanie jest podobnie kruche do modyfikowania pliku hosts, ponieważ zmiany nie są Odzwierciedlenie. Mimo że to rozwiązanie jest kruche, może być najlepszym wyborem dla niektórych środowisk.
- Przesyłaj `core.windows.net` dalej strefę z lokalnych serwerów DNS do prywatnej strefy DNS platformy Azure. Do prywatnego hosta DNS platformy Azure można`168.63.129.16`uzyskać za pośrednictwem specjalnego adresu IP ( ), który jest dostępny tylko w sieciach wirtualnych połączonych z prywatną strefą DNS platformy Azure. Aby obejść to ograniczenie, można uruchomić dodatkowe serwery DNS `core.windows.net` w sieci wirtualnej, które będą przekazywane do prywatnej strefy DNS platformy Azure. Aby uprościć tę konfigurację, udostępniliśmy polecenia cmdlet programu PowerShell, które automatycznie wdrażają serwery DNS w sieci wirtualnej platformy Azure i konfigurują je zgodnie z potrzebami. Aby dowiedzieć się, jak skonfigurować przekazywanie usług DNS, zobacz [Konfigurowanie usługi DNS przy plikach Azure .](storage-files-networking-dns.md)

## <a name="storage-account-firewall-settings"></a>Ustawienia zapory konta magazynu
Zapora to zasada sieciowa, która kontroluje, które żądania mogą uzyskiwać dostęp do publicznego punktu końcowego dla konta magazynu. Korzystając z zapory konta magazynu, można ograniczyć dostęp do publicznego punktu końcowego konta magazynu do niektórych adresów IP lub zakresów lub do sieci wirtualnej. Ogólnie rzecz biorąc większość zasad zapory dla konta magazynu ograniczy dostęp do sieci do jednej lub więcej sieci wirtualnych. 

Istnieją dwa podejścia do ograniczania dostępu do konta magazynu do sieci wirtualnej:
- Utwórz jeden lub więcej prywatnych punktów końcowych dla konta magazynu i ogranicz cały dostęp do publicznego punktu końcowego. Gwarantuje to, że tylko ruch pochodzący z żądanych sieci wirtualnych można uzyskać dostęp do udziałów plików platformy Azure w ramach konta magazynu.
- Ogranicz publiczny punkt końcowy do jednej lub więcej sieci wirtualnych. Działa to przy użyciu możliwości sieci wirtualnej o nazwie *punkty końcowe usługi*. Po ograniczeniu ruchu do konta magazynu za pośrednictwem punktu końcowego usługi, nadal uzyskujesz dostęp do konta magazynu za pośrednictwem publicznego adresu IP.

Aby dowiedzieć się więcej o konfigurowaniu zapory konta magazynu, zobacz [konfigurowanie zapór magazynu platformy Azure i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Domyślnie wszystkie konta magazynu platformy Azure mają włączone szyfrowanie podczas przesyłania. Oznacza to, że podczas instalowania udziału plików za pośrednictwem protokołu SMB lub uzyskiwania do niego dostępu za pośrednictwem protokołu FileREST (na przykład za pośrednictwem witryny Azure portal, powershell/cli lub azure SDKs), usługa Azure Files zezwala na połączenie tylko wtedy, gdy zostanie nawiązycona za pomocą protokołu SMB 3.0+ z szyfrowaniem lub protokołem HTTPS. Klienci, którzy nie obsługują SMB 3.0 lub klientów obsługujących szyfrowanie SMB 3.0, ale nie SMB, nie będą mogli zainstalować udziału plików platformy Azure, jeśli szyfrowanie podczas przesyłania jest włączone. Aby uzyskać więcej informacji o tym, które systemy operacyjne obsługują SMB 3.0 z szyfrowaniem, zapoznaj się ze szczegółową dokumentacją [systemu Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)i [Linux.](storage-how-to-use-files-linux.md) Wszystkie bieżące wersje programów PowerShell, CLI i SDK obsługują protokół HTTPS.  

Można wyłączyć szyfrowanie podczas przesyłania dla konta magazynu platformy Azure. Gdy szyfrowanie jest wyłączone, usługi Azure Files umożliwi również SMB 2.1, SMB 3.0 bez szyfrowania i niezaszyfrowane wywołania interfejsu API FileREST za pośrednictwem protokołu HTTP. Głównym powodem wyłączenia szyfrowania podczas przesyłania jest obsługa starszej aplikacji, która musi być uruchamiana w starszym systemie operacyjnym, takim jak Dystrybucja systemu Windows Server 2008 R2 lub starsza. Usługa Azure Files zezwala tylko na połączenia SMB 2.1 w tym samym regionie platformy Azure co udział plików platformy Azure; klient SMB 2.1 poza regionem platformy Azure udziału plików platformy Azure, na przykład lokalnie lub w innym regionie platformy Azure, nie będzie mógł uzyskać dostępu do udziału plików.

Aby uzyskać więcej informacji na temat szyfrowania podczas przesyłania, zobacz [wymaganie bezpiecznego transferu w magazynie platformy Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Zobacz też
- [Omówienie usługi Azure Files](storage-files-introduction.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)