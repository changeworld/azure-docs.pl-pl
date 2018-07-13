---
title: Konfigurowanie sieci wirtualnej dla usługi Azure Redis Cache w warstwie Premium | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać nimi obsługi sieci wirtualnej dla warstwy Premium usługi Azure Redis Cache wystąpień
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 250c66c3a39519a6eddc1ecb51259ec1944c88a9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38671126"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Jak skonfigurować obsługę sieci wirtualnych usługi Premium Azure Redis Cache
Usługa Azure Redis Cache ma pamięci podręcznej różnych ofert, które zapewniają elastyczność przy wyborze rozmiar pamięci podręcznej i funkcji, takich jak funkcje warstwy Premium, takich jak klastrowanie, trwałość i obsługę sieci wirtualnej. Sieć wirtualna jest prywatna sieć w chmurze. Gdy wystąpienie usługi Azure Redis Cache jest skonfigurowane z siecią wirtualną, nie jest adresowany publicznie i może zostać oceniony jedynie z maszyn wirtualnych i aplikacji w ramach sieci wirtualnej. W tym artykule opisano sposób konfigurowania obsługi sieci wirtualnej dla wystąpienia usługi Azure Redis Cache — wersja premium.

> [!NOTE]
> Usługa Azure Redis Cache obsługuje zarówno Model Klasyczny i sieci wirtualnych Menedżera zasobów.
> 
> 

Aby uzyskać informacje o innych funkcjach pamięci podręcznej — wersja premium, zobacz [wprowadzenie do warstwy Premium usługi Azure Redis Cache](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Dlaczego sieci wirtualnej?
[Usługa Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) wdrożenie zapewnia lepsze zabezpieczenia i izolację usługi Azure Redis Cache, a także podsieci, zasady kontroli dostępu i inne funkcje ograniczające dostęp.

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej
Obsługa usługi Virtual Network (VNet) jest skonfigurowany na **nowej pamięci podręcznej Redis** bloku podczas tworzenia pamięci podręcznej. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po wybraniu warstwy cenowej premium można skonfigurować integracji Redis sieci wirtualnej przez wybranie sieci wirtualnej, która znajduje się w tej samej subskrypcji i lokalizacji co pamięć podręczna. Aby korzystać z nowej sieci wirtualnej, go najpierw utworzyć, wykonując kroki opisane w [Utwórz sieć wirtualną przy użyciu witryny Azure portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) lub [tworzenie sieci wirtualnej (klasycznej) przy użyciu witryny Azure portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) , a następnie wróć do **Nowej pamięci podręcznej Redis** bloku, aby utworzyć i skonfigurować pamięć podręczną w warstwie premium.

Aby skonfigurować sieć wirtualną dla nowa pamięć podręczna, kliknij **sieci wirtualnej** na **nowej pamięci podręcznej Redis** bloku, a następnie wybierz odpowiednią sieć wirtualną z listy rozwijanej.

![Sieć wirtualna][redis-cache-vnet]

Wybierz odpowiednią podsieć z **podsieci** listy rozwijanej listy, a następnie określ żądany **statycznego adresu IP**. Jeśli używasz klasycznej sieci wirtualnej **statycznego adresu IP** pole jest opcjonalne, a jeśli nie zostanie określona, wybierany jest jeden z wybranej podsieci.

> [!IMPORTANT]
> Podczas wdrażania usługi Azure Redis Cache do sieci wirtualnej usługi Resource Manager, pamięci podręcznej musi być w dedykowanej podsieci, która nie zawiera żadnych innych zasobów, z wyjątkiem wystąpień usługi Azure Redis Cache. Jeśli jest podejmowana próba wdrożenia usługi Azure Redis Cache do sieci wirtualnej usługi Resource Manager do podsieci zawierający inne zasoby, wdrożenie zakończy się niepowodzeniem.
> 
> 

![Sieć wirtualna][redis-cache-vnet-ip]

> [!IMPORTANT]
> Platforma Azure rezerwuje pewnych adresów IP w każdej podsieci, a nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane dla zgodności protokołów, oraz trzy dodatkowe adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Oprócz adresy IP używane przez infrastrukturę sieci Wirtualnej platformy Azure Redis każdego wystąpienia w podsieci adresów IP używa dwóch na fragment i jeden dodatkowy adres IP modułu równoważenia obciążenia. Pamięć podręczna nieklastrowanych została uznana za jednego fragmentu.
> 
> 

Po utworzeniu pamięci podręcznej, konfiguracja sieci wirtualnej można wyświetlić, klikając **sieci wirtualnej** z **menu zasobów**.

![Sieć wirtualna][redis-cache-vnet-info]

Aby połączyć się z wystąpienia pamięci podręcznej Azure Redis, korzystając z sieci wirtualnej, należy określić nazwę hosta w pamięci podręcznej w parametrach połączenia, jak pokazano w poniższym przykładzie:

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

## <a name="azure-redis-cache-vnet-faq"></a>Usługi Azure Redis Cache z sieci wirtualnej — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące skalowania usługi Azure Redis Cache.

* [Jakie są niektóre typowe problemy z błędną konfiguracją przy użyciu usługi Azure Redis Cache i sieciami wirtualnymi?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Jak sprawdzić, czy przepełnieniu pamięci podręcznej działa w sieci Wirtualnej?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Podczas próby połączenia do mojego pamięci podręcznej Redis w sieci Wirtualnej, Dlaczego otrzymuję komunikat o błędzie informujący, że certyfikat zdalny jest nieprawidłowy?](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [Czy można używać sieci wirtualne, z pamięcią podręczną standard lub basic?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Dlaczego Tworzenie pamięci podręcznej Redis nie powiodło się w niektórych podsieci, a nie innych?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Jakie są wymagania dotyczące miejsca na adres podsieci?](#what-are-the-subnet-address-space-requirements)
* [Wszystkie funkcje pamięci podręcznej działają w przypadku hostowania w sieci Wirtualnej pamięci podręcznej?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Jakie są niektóre typowe problemy z błędną konfiguracją przy użyciu usługi Azure Redis Cache i sieciami wirtualnymi?
Gdy usługi Azure Redis Cache znajduje się w sieci wirtualnej, są używane porty w poniższych tabelach. 

>[!IMPORTANT]
>Porty w poniższych tabelach są zablokowane, pamięci podręcznej może nie działać poprawnie. Co najmniej jeden z tych portów, zablokowane jest najczęściej spotykanym problemem błędnej konfiguracji podczas korzystania z usługi Azure Redis Cache w sieci wirtualnej.
> 
> 

- [Wymagania dotyczące portów wychodzących](#outbound-port-requirements)
- [Wymagania dotyczące portów dla ruchu przychodzącego](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących

Istnieje siedem wymagań wychodząca przez port.

- Jeśli żądany, wszystkie wychodzące połączenia z Internetem, może się za pośrednictwem klienta lokalnej inspekcji urządzenia.
- Trzy porty kierować ruch do punkty końcowe platformy Azure, obsługę usługi Azure Storage i Azure DNS.
- Pozostałe zakresy portów i komunikacji podsieci wewnętrznej pamięci podręcznej Redis. Nie reguły sieciowej grupy zabezpieczeń w podsieci są wymagane do komunikacji podsieci wewnętrznej pamięci podręcznej Redis.

| Porty | Kierunek | Protokół transportowy | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Wychodzący |TCP |Redis zależności w usłudze Azure Storage/infrastruktury kluczy publicznych (Internet) | (Redis podsieci) |* |
| 53 |Wychodzący |TCP/UDP |Redis zależności w systemie DNS (Internet/sieć wirtualna) | (Redis podsieci) |* |
| 8443 |Wychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) | (Redis podsieci) |
| 10221-10231 |Wychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) | (Redis podsieci) |
| 20226 |Wychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) |(Redis podsieci) |
| 13000-13999 |Wychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) |(Redis podsieci) |
| 15000-15999 |Wychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) |(Redis podsieci) |
| 6379-6380 |Wychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) |(Redis podsieci) |


#### <a name="inbound-port-requirements"></a>Wymagania dotyczące portów dla ruchu przychodzącego

Ma osiem wymagań zakresu portów przychodzących. Przychodzące żądania w tych zakresach są ruch przychodzący z innych usług hostowanych w tej samej sieci Wirtualnej lub wewnętrznego komunikacja podsieci pamięci podręcznej Redis.

| Porty | Kierunek | Protokół transportowy | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Przychodzący |TCP |Komunikacja klienta z pamięci podręcznej Redis, równoważenia obciążenia platformy Azure | (Redis podsieci) | (Redis podsieć), sieć wirtualną, usługa Azure Load Balancer |
| 8443 |Przychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) |(Redis podsieci) |
| 8500 |Przychodzący |TCP/UDP |Równoważenie obciążenia Azure | (Redis podsieci) |Azure Load Balancer |
| 10221-10231 |Przychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) |(Redis podsieć), usługa Azure Load Balancer |
| 13000-13999 |Przychodzący |TCP |Komunikacja klienta z usługi Redis klastrów równoważenia obciążenia platformy Azure | (Redis podsieci) |Sieć wirtualna, usługa Azure Load Balancer |
| 15000-15999 |Przychodzący |TCP |Komunikacja klienta z klastrów usługi redis cache, Azure obciążenia równoważenia | (Redis podsieci) |Sieć wirtualna, usługa Azure Load Balancer |
| 16001 |Przychodzący |TCP/UDP |Równoważenie obciążenia Azure | (Redis podsieci) |Azure Load Balancer |
| 20226 |Przychodzący |TCP |Wewnętrzny komunikację z usługą redis Cache | (Redis podsieci) |(Redis podsieci) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>Dodatkowe wymagania łączności sieci VNET

Brak wymagań dotyczących łączności sieciowej dla usługi Azure Redis Cache, nie może być początkowo spełniony w sieci wirtualnej. Usługa Azure Redis Cache wymaga wszystkich następujących elementów, aby działać prawidłowo, gdy jest używana w ramach sieci wirtualnej.

* Połączenia sieciowego ruchu wychodzącego do usługi Azure Storage, punktów końcowych na całym świecie. Dotyczy to punktów końcowych znajdujących się w tym samym regionie co wystąpienie usługi Azure Redis Cache, a także punkty końcowe usługi storage znajduje się w **innych** regiony platformy Azure. Punkty końcowe usługi Azure Storage rozwiązania w ramach następujących domen DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*i *file.core.windows.net*. 
* Połączenia sieciowego ruchu wychodzącego do *ocsp.msocsp.com*, *mscrl.microsoft.com*, i *crl.microsoft.com*. To połączenie jest potrzebny do obsługi funkcji protokołu SSL.
* Konfiguracja DNS dla sieci wirtualnej musi umożliwiać rozpoznawanie wszystkie punkty końcowe i domen wymienionych w starszych punktów. Te wymagania DNS mogą zostać spełnione przez zapewnienie im prawidłowe infrastruktura DNS jest konfigurowane i obsługiwane dla sieci wirtualnej.
* Połączenia sieciowego ruchu wychodzącego do następujących Azure Monitoring punktów końcowych, rozpoznawane w ramach następujących domen DNS: północno prod2.prod2.metrics.nsatc.net shoebox2-black.shoebox2.metrics.nsatc.net azglobal-black.azglobal.metrics.nsatc.net , wschód prod2.prod2.metrics.nsatc.net shoebox2-red.shoebox2.metrics.nsatc.net azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak sprawdzić, czy przepełnieniu pamięci podręcznej działa w sieci Wirtualnej?

>[!IMPORTANT]
>Podczas łączenia się z wystąpieniem usługi Azure Redis Cache, który znajduje się w sieci Wirtualnej, klienci pamięci podręcznej musi być w tej samej sieci Wirtualnej lub w sieci Wirtualnej za pomocą komunikacji równorzędnej sieci wirtualnych włączone. Obejmuje to wszystkie aplikacje testu lub narzędzia diagnostyczne pingowanie. Niezależnie od tego, w którym jest hostowana aplikacja kliencka sieciowe grupy zabezpieczeń musi być skonfigurowany w taki sposób, że ruch sieciowy klienta może dotrzeć do wystąpienia pamięci podręcznej Redis.
>
>

Gdy wymagania dotyczące portów są skonfigurowane zgodnie z opisem w poprzedniej sekcji, możesz sprawdzić, czy pamięć podręczna działa, wykonując następujące kroki.

- [Ponowne uruchomienie](cache-administration.md#reboot) wszystkich węzłów pamięci podręcznej. Jeśli wszystkie zależności wymagane pamięci podręcznej nie można nawiązać połączenia (zgodnie z opisem w [dla ruchu przychodzącego wymagania dotyczące portów](cache-how-to-premium-vnet.md#inbound-port-requirements) i [wymagania dotyczące portów wychodzących](cache-how-to-premium-vnet.md#outbound-port-requirements)), pamięć podręczna nie będzie można pomyślnie uruchomić ponownie.
- Gdy węzłami pamięci podręcznej zostały uruchomione ponownie (określone przez stan pamięci podręcznej w witrynie Azure portal), należy wykonać następujące testy:
  - wysłać polecenie ping do punktu końcowego pamięci podręcznej (przy użyciu portu 6380) z komputera, który znajduje się w tej samej sieci Wirtualnej jako pamięci podręcznej, za pomocą [tcping](https://www.elifulkerson.com/projects/tcping.php). Na przykład:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Jeśli `tcping` narzędzie informuje, że port jest otwarty, pamięć podręczna jest dostępna dla połączeń z klientami w sieci Wirtualnej.

  - Innym sposobem, aby przetestować jest utworzenie klienta pamięci podręcznej testu (może stać się prostej aplikacji konsolowej przy użyciu StackExchange.Redis) który nawiązuje połączenie z pamięcią podręczną dodaje i pobiera niektóre elementy z pamięci podręcznej. Zainstaluj klienta przykładowej na maszynę Wirtualną, która znajduje się w tej samej sieci Wirtualnej jako pamięci podręcznej i uruchom go, aby zweryfikować łączności z pamięcią podręczną.


### <a name="when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Podczas próby połączenia do mojego pamięci podręcznej Redis w sieci Wirtualnej, Dlaczego otrzymuję komunikat o błędzie informujący, że certyfikat zdalny jest nieprawidłowy?

Próbując nawiązać połączenie z pamięci podręcznej Redis w sieci Wirtualnej, zostanie wyświetlony błąd sprawdzania poprawności certyfikatu, takie jak to:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Może to być spowodowane że łączysz się do hosta przy użyciu adresu IP. Firma Microsoft zaleca korzystanie z nazwą hosta. Innymi słowy należy użyć następującego polecenia:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Należy unikać przy użyciu adresu IP, podobnie jak następujący ciąg połączenia:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Jeśli nie można rozpoznać nazwy DNS, niektóre biblioteki klienta obejmują opcje konfiguracji, takich jak `sslHost` przekazywane przez klienta StackExchange.Redis. Dzięki temu można zastąpić nazwę hosta, używane do weryfikacji certyfikatu. Na przykład:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Czy można używać sieci wirtualne, z pamięcią podręczną standard lub basic?
Sieci wirtualne można używać tylko w pamięciach podręcznych premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Dlaczego Tworzenie pamięci podręcznej Redis nie powiodło się w niektórych podsieci, a nie innych?
Jeśli wdrażasz usługi Azure Redis Cache do sieci wirtualnej usługi Resource Manager, pamięci podręcznej musi być w dedykowanej podsieci, która zawiera inny typ zasobu. Jeśli jest podejmowana próba wdrożenia usługi Azure Redis Cache do podsieci sieci wirtualnej usługi Resource Manager zawierający inne zasoby, wdrożenie zakończy się niepowodzeniem. Aby można było utworzyć nową pamięć podręczną Redis, należy usunąć istniejące zasoby w tej podsieci.

Wiele typów zasobów można wdrożyć do klasycznej sieci wirtualnej, tak długo, jak długo ma za mało dostępnych adresów IP.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jakie są wymagania dotyczące miejsca na adres podsieci?
Platforma Azure rezerwuje pewnych adresów IP w każdej podsieci, a nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane dla zgodności protokołów, oraz trzy dodatkowe adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresy IP używane przez infrastrukturę sieci Wirtualnej platformy Azure Redis każdego wystąpienia w podsieci adresów IP używa dwóch na fragment i jeden dodatkowy adres IP modułu równoważenia obciążenia. Pamięć podręczna nieklastrowanych została uznana za jednego fragmentu.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Wszystkie funkcje pamięci podręcznej działają w przypadku hostowania w sieci Wirtualnej pamięci podręcznej?
Gdy pamięć podręczna jest częścią sieci Wirtualnej, tylko klienci w sieci Wirtualnej można uzyskać dostęp do pamięci podręcznej. W rezultacie następujące funkcje zarządzania pamięci podręcznej nie działają w tej chwili.

* Konsola pamięci podręcznej redis — ponieważ konsolę pamięci podręcznej Redis działa w przeglądarce lokalnego znajduje się poza siecią Wirtualną, nie można połączyć do pamięci podręcznej.


## <a name="use-expressroute-with-azure-redis-cache"></a>Usługi ExpressRoute za pomocą usługi Azure Redis Cache

Klienci mogą się łączyć [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) obwód do infrastruktury sieci wirtualnej, w związku z tym rozszerzanie sieci lokalnych na platformę Azure. 

Domyślnie nowo utworzony obwód usługi ExpressRoute nie wykonuje wymuszonego tunelowania (anonsowanie trasy domyślnej 0.0.0.0/0) w sieci Wirtualnej. W rezultacie wychodzące połączenie z Internetem jest dozwolona bezpośrednio z sieci Wirtualnej, a aplikacje klienckie są w stanie połączyć się z innych punktów końcowych platformy Azure, w tym usługi Azure Redis Cache.

Jednak to typowa konfiguracja klienta do użycia wymuszonego tunelowania (anonsowanie trasy domyślnej) który wymusza ruch wychodzący z Internetu zamiast tego przepływ w środowisku lokalnym. Ten przepływ ruchu przerywa połączenie z usługą Azure Redis Cache, jeśli ruch wychodzący jest następnie zablokowana w środowisku lokalnym w taki sposób, że wystąpienia usługi Azure Redis Cache nie będzie mógł komunikować się z jego zależności.

Rozwiązaniem jest zdefiniowanie jednego (lub więcej) tras zdefiniowanych przez użytkownika (Udr) w tej podsieci, który zawiera usługi Azure Redis Cache. Trasa zdefiniowana przez użytkownika definiuje trasy specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.

Jeśli to możliwe zaleca się użyć następującej konfiguracji:

* Konfiguracji usługi ExpressRoute anonsuje 0.0.0.0/0 i domyślnie życie tuneli całego ruchu wychodzącego środowiska lokalnego.
* Trasa zdefiniowana przez użytkownika zastosowane do podsieci zawierającej usługi Azure Redis Cache definiuje 0.0.0.0/0 z trasą pracy dla ruchu protokołu TCP/IP do publicznego Internetu; na przykład przez ustawienie typu następnego przeskoku na "Internet".

Połączony wpływ tych kroków jest, czy poziomu podsieci trasy zdefiniowanej przez użytkownika ma pierwszeństwo przed ExpressRoute wymuszonego tunelowania, co pozwala na zapewnienie ruch wychodzący do Internetu z usługi Azure Redis Cache.

Nawiązywanie połączenia z wystąpieniem usługi Azure Redis Cache z aplikacji w środowisku lokalnym przy użyciu usługi ExpressRoute nie jest typowy scenariusz z powodu ze względu na wydajność (Aby uzyskać najlepszą wydajność usługi Azure Redis Cache klientów musi należeć do tego samego regionu Azure Redis Cache).

>[!IMPORTANT] 
>Trasy zdefiniowane przez użytkownika **musi** być wystarczająco szczegółowe, pierwszeństwo względem wszelkich tras anonsowanych przez konfigurację usługi ExpressRoute. Poniższy przykład używa szerokiego zakresu adresów 0.0.0.0/0 i jako takie mogą zostać przypadkowo zastąpione przez anonsy tras przy użyciu bardziej szczegółowymi zakresami adresów.

>[!WARNING]  
>Usługa Azure Redis Cache nie jest obsługiwana w przypadku konfiguracji usługi ExpressRoute, **niepoprawnie cross anonsować tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej**. Konfiguracje usługi ExpressRoute, które mają skonfigurowaną, publiczną komunikacją równorzędną otrzymują anonsy tras od firmy Microsoft dla duży zestaw zakresów adresów IP platformy Microsoft Azure. Przypadku niepoprawnie anonsowania krzyżowego ścieżką prywatnej sieci równorzędnej tych zakresów adresów, wynik jest, że wszystkie pakiety sieciowe wychodzące z podsieci wystąpienia usługi Azure Redis Cache są niepoprawnie sposób wymuszony tunelowany do infrastruktury sieci lokalnej klienta . Ten przepływ sieciowy przerywa usługi Azure Redis Cache. Rozwiązanie tego problemu jest zatrzymanie anonsowania krzyżowego tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej.


Ogólne informacje o trasach definiowanych przez użytkownika są dostępne w tym [Przegląd](../virtual-network/virtual-networks-udr-overview.md).

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [ExpressRoute — opis techniczny](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do usługi Azure Redis Cache w warstwie Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

