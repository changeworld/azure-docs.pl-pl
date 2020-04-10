---
title: Konfigurowanie sieci wirtualnej — premium azure cache for Redis
description: Dowiedz się, jak tworzyć i zarządzać obsługą sieci wirtualnej dla pamięci podręcznej platformy Azure premium dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 2821ee637b2562b5287dd3d59cf943b3dcb7ef97
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010889"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Jak skonfigurować obsługę sieci wirtualnej dla pamięci podręcznej platformy Azure premium dla redis
Usługa Azure Cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru pamięci podręcznej i funkcji, w tym funkcji warstwy Premium, takich jak klastrowanie, trwałość i obsługa sieci wirtualnej. Sieć wirtualna to sieć prywatna w chmurze. Gdy usługa Azure Cache for Redis wystąpienie jest skonfigurowany za pomocą sieci wirtualnej, nie jest publicznie adresowalna i można uzyskać do nich dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej. W tym artykule opisano sposób konfigurowania obsługi sieci wirtualnej dla premium usługi Azure Cache dla wystąpienia Redis.

> [!NOTE]
> Usługa Azure Cache for Redis obsługuje sieci wirtualne usługi Classic i Resource Manager.
> 
> 

Aby uzyskać informacje na temat innych funkcji pamięci podręcznej w warstwie Premium, zobacz [Wprowadzenie do warstwy Azure Cache for Redis Premium.](cache-premium-tier-intro.md)

## <a name="why-vnet"></a>Dlaczego VNet?
Wdrożenie [sieci wirtualnej platformy Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) zapewnia zwiększone zabezpieczenia i izolację pamięci podręcznej platformy Azure dla programu Redis, a także podsieci, zasady kontroli dostępu i inne funkcje w celu dalszego ograniczenia dostępu.

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej
Obsługa sieci wirtualnej (VNet) jest skonfigurowana w **nowej pamięci podręcznej platformy Azure dla redis** bloku podczas tworzenia pamięci podręcznej. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po wybraniu warstwy cenowej premium można skonfigurować integrację sieci wirtualnej Redis, wybierając witrynę wirtualną, która znajduje się w tej samej subskrypcji i lokalizacji co pamięć podręczna. Aby użyć nowej sieci wirtualnej, należy ją najpierw utworzyć, wykonując kroki opisane w [temacie Tworzenie sieci wirtualnej przy użyciu witryny Azure portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) lub tworzenie sieci [wirtualnej (klasycznej) przy użyciu witryny Azure portal,](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a następnie wróć do **nowego bloku Azure Cache for Redis,** aby utworzyć i skonfigurować pamięć podręczną premium.

Aby skonfigurować sieć wirtualną dla nowej pamięci podręcznej, kliknij pozycję **Sieć wirtualna** w **nowej pamięci podręcznej platformy Azure dla programu Redis** i wybierz żądaną sieć wirtualną z listy rozwijanej.

![Sieć wirtualna][redis-cache-vnet]

Wybierz żądaną podsieć z listy rozwijanej Podsieć. **Subnet**  W razie potrzeby określ **statyczny adres IP**. **Statyczne** pole adresu IP jest opcjonalne, a jeśli nie jest określone, jeden jest wybierany z wybranej podsieci.

> [!IMPORTANT]
> Podczas wdrażania pamięci podręcznej azure dla usługi Redis do sieci wirtualnej Menedżera zasobów, pamięci podręcznej musi znajdować się w dedykowanej podsieci, która zawiera żadnych innych zasobów, z wyjątkiem usługi Azure Cache dla wystąpień Redis. Jeśli zostanie podjęta próba wdrożenia pamięci podręcznej Azure Cache for Redis w sieci wirtualnej Menedżera zasobów w podsieci zawierającej inne zasoby, wdrożenie zakończy się niepowodzeniem.
> 
> 

![Sieć wirtualna][redis-cache-vnet-ip]

> [!IMPORTANT]
> Platforma Azure rezerwuje niektóre adresy IP w każdej podsieci i tych adresów nie można używać. Pierwszy i ostatni adres IP podsieci są zarezerwowane dla zgodności protokołu, wraz z trzema adresami używanymi dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure każde wystąpienie Redis w podsieci używa dwóch adresów IP na fragment i jeden dodatkowy adres IP dla modułu równoważenia obciążenia. Nieklastrowana pamięć podręczna jest uważana za jedną niezależną pamięć niezależną.
> 
> 

Po utworzeniu pamięci podręcznej można wyświetlić konfigurację sieci wirtualnej, klikając pozycję **Sieć wirtualna** z **menu Zasób**.

![Sieć wirtualna][redis-cache-vnet-info]

Aby połączyć się z pamięcią podręczną platformy Azure dla wystąpienia Redis podczas korzystania z sieci wirtualnej, należy określić nazwę hosta pamięci podręcznej w ciągu połączenia, jak pokazano w poniższym przykładzie:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-cache-for-redis-vnet-faq"></a>Pamięć podręczna usługi Azure dla sieci wirtualnej Redis — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące skalowania usługi Azure Cache for Redis.

* Jakie są typowe problemy z nieprawidłową konfiguracją z pamięcią podręczną Azure dla sieci Redis i sieci wirtualnych?
* [Jak sprawdzić, czy moja pamięć podręczna działa w sieci wirtualnej?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Dlaczego podczas próby nawiązania połączenia z moją pamięcią podręczną Azure Cache for Redis w sieci wirtualnej pojawia się błąd informujący, że certyfikat zdalny jest nieprawidłowy?
* [Czy mogę używać sieci wirtualnych ze standardową lub podstawową pamięcią podręczną?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Dlaczego tworzenie pamięci podręcznej azure dla redis nie w niektórych podsieci, ale nie innych?
* [Jakie są wymagania dotyczące przestrzeni adresowej podsieci?](#what-are-the-subnet-address-space-requirements)
* [Czy wszystkie funkcje pamięci podręcznej działają podczas hostowania pamięci podręcznej w sieci wirtualnej?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Jakie są typowe problemy z nieprawidłową konfiguracją z pamięcią podręczną Azure dla sieci Redis i sieci wirtualnych?
Gdy usługa Azure Cache for Redis jest hostowana w sieci wirtualnej, używane są porty w poniższych tabelach. 

>[!IMPORTANT]
>Jeśli porty w poniższych tabelach są zablokowane, pamięć podręczna może nie działać poprawnie. Po jeden lub więcej z tych portów zablokowany jest najczęstszym problemem błędnej konfiguracji podczas korzystania z usługi Azure Cache for Redis w sieci wirtualnej.
> 
> 

- [Wymagania dotyczące portów wychodzących](#outbound-port-requirements)
- [Wymagania dotyczące portów przychodzących](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących

Istnieje dziewięć wymagań dotyczących portu wychodzącego. Żądania wychodzące w tych zakresach są albo wychodzące do innych usług niezbędnych do działania pamięci podręcznej lub wewnętrzne do podsieci Redis do komunikacji między węzłowej. W przypadku replikacji geograficznej istnieją dodatkowe wymagania dotyczące ruchu wychodzącego dla komunikacji między podsieciami podstawowej i pomocniczej pamięci podręcznej.

| Porty | Kierunek | Protokół transportu | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Wychodzący |TCP |Zależności redis w usłudze Azure Storage/PKI (Internet) | (Podsieć Redis) |* |
| 443 | Wychodzący | TCP | Zależność redis w usłudze Azure Key Vault | (Podsieć Redis) | Usługa AzureKeyVault <sup>1</sup> |
| 53 |Wychodzący |Protokół TCP/UDP |Zależności redis w systemie DNS (Internet/VNet) | (Podsieć Redis) | 168.63.129.16 i 169.254.169.254 <sup>2</sup> oraz dowolny niestandardowy serwer DNS podsieci <sup>3</sup> |
| 8443 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) | (Podsieć Redis) |
| 10221-10231 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) | (Podsieć Redis) |
| 20226 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 13000-13999 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 15000-15999 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis i replikacji geograficznej | (Podsieć Redis) |(Podsieć Redis) (Podsieć elementu równorzędnego repliki geograficznej) |
| 6379-6380 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |

<sup>1</sup> Możesz użyć tagu usługi "AzureKeyVault" z grupami zabezpieczeń sieciowych usługi Resource Manager.

<sup>2</sup> Te adresy IP należące do firmy Microsoft są używane do adresu maszyny Wirtualnej hosta, która obsługuje usługę Azure DNS.

<sup>3</sup> Nie jest potrzebne w przypadku podsieci bez niestandardowego serwera DNS lub nowszych pamięci podręcznych redis, które ignorują niestandardowy system DNS.

#### <a name="geo-replication-peer-port-requirements"></a>Wymagania dotyczące portów równorzędnych replikacji geograficznej

Jeśli używasz georeplikacji między pamięciami podręcznymi w sieciach wirtualnych platformy Azure, należy pamiętać, że zalecana konfiguracja polega na odblokowaniu portów 15000-15999 dla całej podsieci w obu kierunkach przychodzących i wychodzących do obu pamięci podręcznych, tak aby wszystkie składniki repliki w podsieci mogą komunikować się bezpośrednio ze sobą, nawet w przypadku przyszłej pracy awaryjnej geograficznej.

#### <a name="inbound-port-requirements"></a>Wymagania dotyczące portów przychodzących

Istnieje osiem wymagań zakresu portów przychodzących. Żądania przychodzące w tych zakresach są przychodzące z innych usług hostowanych w tej samej sieci wirtualnej lub wewnętrzne do komunikacji podsieci Redis.

| Porty | Kierunek | Protokół transportu | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Przychodzący |TCP |Komunikacja z klientem z redis, równoważenie obciążenia platformy Azure | (Podsieć Redis) | (podsieć Redis), sieć wirtualna, moduł równoważenia obciążenia platformy Azure <sup>1</sup> |
| 8443 |Przychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 8500 |Przychodzący |Protokół TCP/UDP |Równoważenie obciążenia platformy Azure | (Podsieć Redis) |Azure Load Balancer |
| 10221-10231 |Przychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(podsieć Redis), Moduł równoważenia obciążenia platformy Azure |
| 13000-13999 |Przychodzący |TCP |Komunikacja klienta z klastrami Redis, równoważenie obciążenia platformy Azure | (Podsieć Redis) |Sieć wirtualna, moduł równoważenia obciążenia platformy Azure |
| 15000-15999 |Przychodzący |TCP |Komunikacja klienta z klastrami Redis, równoważeniem obciążenia platformy Azure i replikacją geograficzną | (Podsieć Redis) |Sieć wirtualna, moduł równoważenia obciążenia platformy Azure (podsieć równorzędna repliki geograficznej) |
| 16001 |Przychodzący |Protokół TCP/UDP |Równoważenie obciążenia platformy Azure | (Podsieć Redis) |Azure Load Balancer |
| 20226 |Przychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |

<sup>1</sup> Do tworzenia reguł sieciowej grupy danych można użyć tagu usługi "AzureLoadBalancer" (Resource Manager) (lub "AZURE_LOADBALANCER" dla klasyków.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Dodatkowe wymagania dotyczące łączności sieci wirtualnej

Istnieją wymagania dotyczące łączności sieciowej dla usługi Azure Cache for Redis, które mogą nie być początkowo spełnione w sieci wirtualnej. Usługa Azure Cache for Redis wymaga prawidłowego działania wszystkich następujących elementów, gdy jest używana w sieci wirtualnej.

* Wychodząca łączność sieciowa z punktami końcowymi usługi Azure Storage na całym świecie. Obejmuje to punkty końcowe znajdujące się w tym samym regionie co usługa Azure Cache dla wystąpienia Redis, a także punkty końcowe magazynu znajdujące się w **innych** regionach platformy Azure. Punkty końcowe usługi Azure Storage są rozpoznawane w następujących domenach DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*i *file.core.windows.net*. 
* Wychodząca łączność sieciowa z *ocsp.msocsp.com,* *mscrl.microsoft.com*i *crl.microsoft.com*. Ta łączność jest potrzebna do obsługi funkcji TLS/SSL.
* Konfiguracja DNS dla sieci wirtualnej musi być w stanie rozwiązać wszystkie punkty końcowe i domeny wymienione we wcześniejszych punktach. Te wymagania dns można spełnić, zapewniając prawidłową infrastrukturę DNS jest skonfigurowany i utrzymywany dla sieci wirtualnej.
* Wychodząca łączność sieciowa z następującymi punktami końcowymi monitorowania platformy Azure, które są rozpoznawane w następujących domenach DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak sprawdzić, czy moja pamięć podręczna działa w sieci wirtualnej?

>[!IMPORTANT]
>Podczas łączenia się z pamięcią podręczną Platformy Azure dla wystąpienia Redis, który jest obsługiwany w sieci wirtualnej, klienci pamięci podręcznej muszą znajdować się w tej samej sieci wirtualnej lub w sieci wirtualnej z włączoną komunikacją równorzędną sieci wirtualnej w tym samym regionie platformy Azure. Globalna komunikacja równorzędna sieci wirtualnej nie jest obecnie obsługiwana. Obejmuje to wszelkie aplikacje testowe lub diagnostyczne narzędzia pingowania. Niezależnie od tego, gdzie jest hostowana aplikacja kliencka, sieciowe grupy zabezpieczeń muszą być skonfigurowane w taki sposób, aby ruch sieciowy klienta mógł dotrzeć do wystąpienia Redis.
>
>

Po skonfigurowaniu wymagań dotyczących portów w sposób opisany w poprzedniej sekcji można sprawdzić, czy pamięć podręczna działa, wykonując następujące kroki.

- [Uruchom ponownie](cache-administration.md#reboot) wszystkie węzły pamięci podręcznej. Jeśli nie można osiągnąć wszystkich wymaganych zależności pamięci podręcznej (zgodnie z wymaganiami dotyczącymi [portów przychodzących](cache-how-to-premium-vnet.md#inbound-port-requirements) i [portami wychodzącymi),](cache-how-to-premium-vnet.md#outbound-port-requirements)pamięć podręczna nie będzie mogła pomyślnie zostać ponownie uruchomiona.
- Po ponownym uruchomieniu węzłów pamięci podręcznej (zgodnie ze stanem pamięci podręcznej w witrynie Azure portal) można wykonać następujące testy:
  - ping punktu końcowego pamięci podręcznej (przy użyciu portu 6380) z komputera, który znajduje się w tej samej sieci wirtualnej co pamięć podręczna, przy użyciu [tcping](https://www.elifulkerson.com/projects/tcping.php). Przykład:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Jeśli `tcping` narzędzie zgłasza, że port jest otwarty, pamięć podręczna jest dostępna dla połączenia z klientami w sieci wirtualnej.

  - Innym sposobem testowania jest utworzenie klienta pamięci podręcznej testowego (który może być prostą aplikacją konsoli przy użyciu StackExchange.Redis), która łączy się z pamięcią podręczną i dodaje i pobiera niektóre elementy z pamięci podręcznej. Zainstaluj przykładową aplikację kliencką na maszynie wirtualnej, która znajduje się w tej samej sieci wirtualnej co pamięć podręczna i uruchom ją, aby zweryfikować łączność z pamięcią podręczną.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Dlaczego podczas próby nawiązania połączenia z moją pamięcią podręczną Azure Cache for Redis w sieci wirtualnej pojawia się błąd informujący, że certyfikat zdalny jest nieprawidłowy?

Podczas próby nawiązania połączenia z pamięcią podręczną azure dla funkcji Redis w sieci wirtualnej, widzisz błąd sprawdzania poprawności certyfikatu, taki jak ten:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Przyczyną może być połączenie z hostem przez adres IP. Zalecamy użycie nazwy hosta. Innymi słowy, należy użyć następujących elementów:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Należy unikać używania adresu IP podobnego do następującego ciągu połączenia:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Jeśli nie można rozpoznać nazwy DNS, niektóre biblioteki `sslHost` klienckie zawierają opcje konfiguracji, takie jak który jest dostarczany przez klienta StackExchange.Redis. Dzięki temu można zastąpić nazwa hosta używana do sprawdzania poprawności certyfikatu. Przykład:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Czy mogę używać sieci wirtualnych ze standardową lub podstawową pamięcią podręczną?
Sieci wirtualne mogą być używane tylko z pamięciami podręcznymi premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Dlaczego tworzenie pamięci podręcznej azure dla redis nie w niektórych podsieci, ale nie innych?
Jeśli wdrażasz pamięć podręczną Azure Cache for Redis w sieci wirtualnej Menedżera zasobów, pamięć podręczna musi znajdować się w dedykowanej podsieci, która nie zawiera żadnego innego typu zasobu. Jeśli zostanie podjęta próba wdrożenia pamięci podręcznej Azure Cache for Redis w podsieci sieci wirtualnej Menedżera zasobów zawierającej inne zasoby, wdrożenie zakończy się niepowodzeniem. Przed utworzeniem nowej pamięci podręcznej azure dla usługi Redis należy usunąć istniejące zasoby wewnątrz podsieci.

Można wdrożyć wiele typów zasobów do klasycznej sieci wirtualnej, o ile masz wystarczającą liczbę dostępnych adresów IP.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jakie są wymagania dotyczące przestrzeni adresowej podsieci?
Platforma Azure rezerwuje niektóre adresy IP w każdej podsieci i tych adresów nie można używać. Pierwszy i ostatni adres IP podsieci są zarezerwowane dla zgodności protokołu, wraz z trzema adresami używanymi dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure każde wystąpienie Redis w podsieci używa dwóch adresów IP na fragment i jeden dodatkowy adres IP dla modułu równoważenia obciążenia. Nieklastrowana pamięć podręczna jest uważana za jedną niezależną pamięć niezależną.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Czy wszystkie funkcje pamięci podręcznej działają podczas hostowania pamięci podręcznej w sieci wirtualnej?
Gdy pamięć podręczna jest częścią sieci wirtualnej, tylko klienci w sieci wirtualnej mogą uzyskać dostęp do pamięci podręcznej. W rezultacie następujące funkcje zarządzania pamięcią podręczną nie działają w tej chwili.

* Redis Console — ponieważ konsola Redis działa w przeglądarce lokalnej, która znajduje się poza siecią wirtualną, nie może połączyć się z pamięcią podręczną.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Używanie usługi ExpressRoute z pamięcią podręczną azure dla programu Redis

Klienci mogą połączyć obwód [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) z infrastrukturą sieci wirtualnej, rozszerzając w ten sposób swoją sieć lokalną na platformę Azure. 

Domyślnie nowo utworzony obwód usługi ExpressRoute nie wykonuje wymuszonego tunelowania (anonsowanie trasy domyślnej, 0.0.0.0/0) w sieci wirtualnej. W rezultacie wychodząca łączność z Internetem jest dozwolona bezpośrednio z sieci wirtualnej, a aplikacje klienckie mogą łączyć się z innymi punktami końcowymi platformy Azure, w tym z usługą Azure Cache for Redis.

Jednak wspólna konfiguracja klienta jest użycie wymuszonego tunelowania (anonsowanie trasy domyślnej), co wymusza wychodzący ruch internetowy zamiast przepływu lokalnie. Ten przepływ ruchu przerywa łączność z usługą Azure Cache for Redis, jeśli ruch wychodzący jest następnie blokowany lokalnie, tak że wystąpienie usługi Azure Cache dla programu Redis nie może komunikować się z jego zależnościami.

Rozwiązaniem jest zdefiniowanie jednej (lub więcej) tras zdefiniowanych przez użytkownika (UDR) w podsieci, która zawiera pamięć podręczną Azure cache dla redis. UDR definiuje trasy specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.

Jeśli to możliwe, zaleca się użycie następującej konfiguracji:

* Konfiguracja usługi ExpressRoute anonsuje 0.0.0.0/0 i domyślnie tunele życie wszystkich ruchu wychodzącego w środowisku lokalnym.
* UDR zastosowane do podsieci zawierającej pamięć podręczną Azure dla redis definiuje 0.0.0.0/0 z trasą roboczą dla ruchu TCP/IP do publicznego Internetu; na przykład, ustawiając następny typ przeskoku na "Internet".

Połączony efekt tych kroków jest, że poziom podsieci UDR ma pierwszeństwo przed wymuszania tunelowania wymuszone usługi ExpressRoute, zapewniając w ten sposób wychodzący dostęp do Internetu z pamięci podręcznej Azure dla redis.

Łączenie się z pamięcią podręczną platformy Azure dla wystąpienia Redis z aplikacji lokalnej przy użyciu usługi ExpressRoute nie jest typowym scenariuszem użycia ze względu na wydajność (dla najlepszej wydajności usługi Azure Cache dla klientów Redis powinien znajdować się w tym samym regionie co pamięć podręczna Azure dla redis).

>[!IMPORTANT] 
>Trasy zdefiniowane w UDR **muszą** być wystarczająco specyficzne, aby mieć pierwszeństwo przed wszelkimi trasami anonsowanymi przez konfigurację usługi ExpressRoute. W poniższym przykładzie użyto szerokiego zakresu adresów 0.0.0.0/0 i jako taki może zostać przypadkowo zastąpiony przez anonse trasy przy użyciu bardziej szczegółowych zakresów adresów.

>[!WARNING]  
>Usługa Azure Cache for Redis nie jest obsługiwana w przypadku konfiguracji usługi ExpressRoute, które **niepoprawnie współadponują trasy z publicznej ścieżki równorzędnej do prywatnej ścieżki równorzędnej.** Konfiguracje usługi ExpressRoute, które mają skonfigurowaną komunikację równorzędną publiczną, odbierają anonse tras od firmy Microsoft dla dużego zestawu zakresów adresów IP platformy Microsoft Azure. Jeśli te zakresy adresów są niepoprawnie współadeklamowane w prywatnej ścieżce równorzędnej, wynikiem jest to, że wszystkie wychodzące pakiety sieciowe z pamięci podręcznej Azure dla podsieci wystąpienia Redis są niepoprawnie wymuszane tunelowanie do lokalnej infrastruktury sieciowej klienta. Ten przepływ sieciowy przerywa pamięć podręczną Azure dla redis. Rozwiązaniem tego problemu jest zatrzymanie tras reklamy krzyżowej z publicznej ścieżki równorzędnej do prywatnej ścieżki komunikacji równorzędnej.


W tym [przeglądzie](../virtual-network/virtual-networks-udr-overview.md)dostępne są podstawowe informacje na temat tras zdefiniowanych przez użytkownika.

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [Omówienie techniczne usługi ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak korzystać z większej liczby funkcji pamięci podręcznej premium.

* [Wprowadzenie do warstwy Azure Cache for Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
