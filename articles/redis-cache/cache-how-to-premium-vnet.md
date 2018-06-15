---
title: Konfigurowanie sieci wirtualnej dla usługi pamięć podręczna Azure Redis Premium | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i zarządzania nimi obsługi sieci wirtualnej dla swoich wystąpień pamięci podręcznej Redis Azure warstwy Premium
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
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522224"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Jak skonfigurować obsługę sieci wirtualnych dla podręczna Redis Azure Premium
Pamięć podręczna Redis Azure ma inną pamięci podręcznej oferty, które zapewniają elastyczność w wyborze rozmiar pamięci podręcznej i funkcji, łącznie z funkcji warstwy Premium, takich jak klastrowanie, trwałości i obsługi sieci wirtualnej. Sieci wirtualnej jest ona prywatną siecią w chmurze. Po skonfigurowaniu wystąpienia pamięci podręcznej Redis Azure z sieci wirtualnej nie jest publicznie adresowana i jest możliwy tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej. W tym artykule opisano sposób konfigurowania obsługi sieci wirtualnej dla wystąpienia pamięci podręcznej Redis Azure premium.

> [!NOTE]
> Pamięć podręczna Redis Azure obsługuje zarówno classic i sieci wirtualnych Menedżera zasobów.
> 
> 

Aby uzyskać informacji o innych funkcjach pamięci podręcznej premium, zobacz [wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Dlaczego sieci wirtualnej?
[Sieć wirtualna platformy Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) wdrożenie zapewnia większe bezpieczeństwo i izolację dla pamięci podręcznej Redis Azure, jak również podsieci, zasad kontroli dostępu i inne funkcje, aby jeszcze bardziej ograniczyć dostęp.

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej
Obsługa usługi Virtual Network (VNet) jest skonfigurowany na **nowa pamięć podręczna Redis** bloku podczas tworzenia pamięci podręcznej. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po wybraniu warstwie cenowej premium można skonfigurować integracji Redis sieci wirtualnej, wybierając sieć wirtualną, która znajduje się w tej samej subskrypcji i lokalizacji co pamięć podręczną. Aby korzystać z nowej sieci wirtualnej, utwórz go najpierw wykonując kroki opisane w [utworzyć sieć wirtualną przy użyciu portalu Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) lub [utworzyć sieć wirtualną (klasyczne) przy użyciu portalu Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) , a następnie wróć do **nowa pamięć podręczna Redis** bloku, aby utworzyć i skonfigurować pamięć podręczną premium.

Kliknij, aby skonfigurować sieć wirtualną dla nowej pamięci podręcznej **sieci wirtualnej** na **nowa pamięć podręczna Redis** bloku, a następnie wybierz odpowiednią sieć wirtualną z listy rozwijanej.

![Sieć wirtualna][redis-cache-vnet]

Wybierz żądaną podsieci z **podsieci** listy rozwijanej liście, a następnie określ żądaną **statyczny adres IP**. Jeśli używasz klasycznej sieci wirtualnej **statyczny adres IP** pole jest opcjonalne, a jeśli nie zostanie określona, wybierany jest jeden z wybranej podsieci.

> [!IMPORTANT]
> Podczas wdrażania pamięć podręczna Redis Azure Resource Manager sieci wirtualnej, pamięci podręcznej musi być w dedykowanym podsieć, która nie zawiera żadnych innych zasobów, z wyjątkiem wystąpienia pamięci podręcznej Redis Azure. Aby wdrożyć pamięć podręczna Redis Azure Resource Manager sieci wirtualnej do podsieci podejmowana jest próba zawiera inne zasoby, wdrożenie zakończy się niepowodzeniem.
> 
> 

![Sieć wirtualna][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure rezerwuje niektórych adresów IP w każdej podsieci, a nie można użyć tych adresów. Imię i nazwisko adresów IP podsieci są zastrzeżone dla protokołu zgodność, wraz z trzech więcej adresów używanych na potrzeby usług Azure. Aby uzyskać więcej informacji, zobacz [istnieją wszystkie ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Oprócz adresy IP używane przez infrastrukturę sieci Wirtualnej Azure Redis każdego wystąpienia w podsieci adresów IP używa dwóch na niezależnych i jeden dodatkowy adres IP usługi równoważenia obciążenia. Pamięć podręczna nieklastrowanych została uznana za jeden identyfikator niezależnego fragmentu.
> 
> 

Po utworzeniu pamięci podręcznej konfiguracji sieci wirtualnej można wyświetlić, klikając **sieci wirtualnej** z **zasobów menu**.

![Sieć wirtualna][redis-cache-vnet-info]

Aby połączyć się wystąpienia pamięci podręcznej Azure Redis, korzystając z sieci wirtualnej, określ nazwę hosta w pamięci podręcznej w parametrach połączenia, jak pokazano w poniższym przykładzie:

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

## <a name="azure-redis-cache-vnet-faq"></a>Sieć wirtualna pamięci podręcznej Azure Redis — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące skalowania pamięć podręczna Redis Azure.

* [Co to są niektórych typowych problemów błędnej konfiguracji sieci wirtualnych i pamięci podręcznej Redis Azure?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Jak sprawdzić, czy Moje pamięci podręcznej działa w sieci Wirtualnej?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Podczas próby nawiązania połączenia do mojego pamięci podręcznej Redis w sieci Wirtualnej, Dlaczego otrzymuję komunikat o błędzie informujący, że zdalny certyfikat jest nieprawidłowy.?](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [Sieci wirtualne można używać z standard lub podstawowa pamięci podręcznej?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Dlaczego Tworzenie pamięci podręcznej Redis powiedzie w niektórych podsieci, a innych nie?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Jakie są wymagania dotyczące miejsca na adres podsieci?](#what-are-the-subnet-address-space-requirements)
* [Wszystkie funkcje pamięci podręcznej działają odnośnie do hostowania pamięci podręcznej w sieci Wirtualnej?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Co to są niektórych typowych problemów błędnej konfiguracji sieci wirtualnych i pamięci podręcznej Redis Azure?
Gdy pamięć podręczna Redis Azure znajduje się w sieci wirtualnej, są używane porty w poniższych tabelach. 

>[!IMPORTANT]
>Porty w poniższych tabelach są zablokowane, pamięć podręczna może nie działać prawidłowo. Co najmniej jeden z tych portów zablokowane jest najbardziej typowe problemy błędnej konfiguracji przy użyciu pamięci podręcznej Redis Azure w sieci wirtualnej.
> 
> 

- [Wymagania dotyczące portów wychodzących](#outbound-port-requirements)
- [Wymagania dotyczące portów dla ruchu przychodzącego](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących

Istnieje siedem wymagań dotyczących portu wychodzącego.

- Jeśli żądany, wszystkie wychodzące połączenia z Internetem mogą być wprowadzane za pośrednictwem klienta lokalnego inspekcji urządzenia.
- Trzy porty kierować ruchem do punkty końcowe systemu Azure obsługi usługi Azure Storage i Azure DNS.
- Pozostałe zakresy portów i komunikacji podsieci wewnętrznej pamięci podręcznej Redis. Brak reguł NSG podsieci są niezbędne do komunikacji podsieci wewnętrznej pamięci podręcznej Redis.

| Porty | Kierunek | Protokół transportu | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Wychodzący |TCP |Redis zależności na platformie Azure magazynu/infrastruktury kluczy publicznych (Internet) | (Redis podsieci) |* |
| 53 |Wychodzący |TCP/UDP |Redis zależności w systemie DNS (Internet/VNet) | (Redis podsieci) |* |
| 8443 |Wychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) | (Redis podsieci) |
| 10221-10231 |Wychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) | (Redis podsieci) |
| 20226 |Wychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) |(Redis podsieci) |
| 13000-13999 |Wychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) |(Redis podsieci) |
| 15000-15999 |Wychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) |(Redis podsieci) |
| 6379-6380 |Wychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) |(Redis podsieci) |


#### <a name="inbound-port-requirements"></a>Wymagania dotyczące portów dla ruchu przychodzącego

Ma osiem wymagań zakresu portów przychodzących. Żądania przychodzące w tych zakresów są ruch przychodzący z innych usług hostowanych w tej samej sieci Wirtualnej lub wewnętrzny komunikacja podsieci Redis.

| Porty | Kierunek | Protokół transportu | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Przychodzący |TCP |Komunikacja klienta do serwera Redis, równoważenia obciążenia Azure | (Redis podsieci) | (Redis podsieć), sieci wirtualnej, usługi równoważenia obciążenia Azure |
| 8443 |Przychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) |(Redis podsieci) |
| 8500 |Przychodzący |TCP/UDP |Równoważenia obciążenia Azure | (Redis podsieci) |Azure Load Balancer |
| 10221-10231 |Przychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) |(Redis podsieć), usługi równoważenia obciążenia Azure |
| 13000-13999 |Przychodzący |TCP |Komunikacja z klientem Redis klastrów równoważenia obciążenia Azure | (Redis podsieci) |Sieć wirtualna Azure Load Balancer |
| 15000-15999 |Przychodzący |TCP |Komunikacji z klientem Redis klastrów równoważenia obciążenia Azure | (Redis podsieci) |Sieć wirtualna Azure Load Balancer |
| 16001 |Przychodzący |TCP/UDP |Równoważenia obciążenia Azure | (Redis podsieci) |Azure Load Balancer |
| 20226 |Przychodzący |TCP |Wewnętrzny komunikację z usługą Redis | (Redis podsieci) |(Redis podsieci) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>Dodatkowe wymagania dotyczące łączności sieci sieci Wirtualnej

Istnieją wymagania dotyczące łączności sieciowej pamięci podręcznej Redis Azure nie może być początkowo spełnieniu w sieci wirtualnej. Pamięć podręczna Redis Azure wymaga następujących elementów działał poprawnie, gdy jest używany w ramach sieci wirtualnej.

* Połączenie sieciowe ruchu wychodzącego punktów końcowych usługi Azure Storage na całym świecie. Obejmuje to punkty końcowe znajduje się w tym samym regionie co wystąpienia pamięci podręcznej Redis Azure, a także punkty końcowe usługi storage znajduje się w **innych** regiony platformy Azure. Punkty końcowe usługi Azure Storage rozwiązania w obszarze następujące domeny DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*, i *file.core.windows.net*. 
* Połączenie sieciowe ruchu wychodzącego *ocsp.msocsp.com*, *mscrl.microsoft.com*, i *crl.microsoft.com*. To połączenie jest potrzebne do obsługi funkcji protokołu SSL.
* Konfiguracja DNS dla sieci wirtualnej musi umożliwiać rozpoznawania wszystkich punktów końcowych i domen wspomnianego z wcześniejszych punktów. W celu zapewnienia prawidłowy infrastruktura DNS jest skonfigurowany i dla sieci wirtualnej można spełnić te wymagania systemu DNS.
* Połączenie sieciowe ruchu wychodzącego do następujących monitorowania Azure punktów końcowych, które rozwiązanie w obszarze następujące domeny DNS: shoebox2 black.shoebox2.metrics.nsatc.net, prod2.prod2.metrics.nsatc.net Północna, azglobal black.azglobal.metrics.nsatc.net, shoebox2 red.shoebox2.metrics.nsatc.net prod2.prod2.metrics.nsatc.net Wschodnia, azglobal red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak sprawdzić, czy Moje pamięci podręcznej działa w sieci Wirtualnej?

>[!IMPORTANT]
>Podczas nawiązywania połączenia z wystąpieniem usługi pamięć podręczna Redis Azure, który znajduje się w sieci Wirtualnej, klienci pamięci podręcznej musi być w tej samej sieci Wirtualnej lub w sieci Wirtualnej z sieci Wirtualnej komunikacji równorzędnej włączone. W tym testowanie aplikacji ani badanie narzędzia diagnostyczne. Niezależnie od tego, gdzie jest hostowana aplikacja kliencka grup zabezpieczeń sieci musi być skonfigurowany w taki sposób, że ruch sieciowy klienta może dotrzeć do wystąpienia pamięci podręcznej Redis.
>
>

Gdy wymagania dotyczące portów są skonfigurowane zgodnie z opisem w poprzedniej sekcji, można sprawdzić, czy pamięć podręczna działa, wykonując następujące czynności.

- [Ponowny rozruch](cache-administration.md#reboot) wszystkich węzłów pamięci podręcznej. Jeśli wszystkie zależności wymagane pamięci podręcznej nie można nawiązać połączenia (zgodnie z opisem w [ruchu przychodzącego wymagania dotyczące portów](cache-how-to-premium-vnet.md#inbound-port-requirements) i [wymagania dotyczące portów wychodzących](cache-how-to-premium-vnet.md#outbound-port-requirements)), pamięć podręczna nie będzie można pomyślnie uruchomić ponownie.
- Po węzłami pamięci podręcznej ponownym uruchomieniu (określone przez stan pamięci podręcznej w portalu Azure), można wykonać następujące testy:
  - polecenie ping punktem końcowym (przy użyciu portu 6380) z komputera, który znajduje się w tej samej sieci Wirtualnej jako pamięci podręcznej, za pomocą [tcping](https://www.elifulkerson.com/projects/tcping.php). Na przykład:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Jeśli `tcping` narzędzie informuje, że port jest otwarty, pamięć podręczna jest dostępny dla połączeń z klientami w sieci Wirtualnej.

  - Innym sposobem testu jest utworzenie klienta pamięci podręcznej testu (które może być prostą aplikację konsoli przy użyciu programie StackExchange.Redis) który łączy do pamięci podręcznej i dodaje oraz pobiera niektóre elementy z pamięci podręcznej. Zainstaluj klienta przykładowe na maszynę Wirtualną, która znajduje się w tej samej sieci Wirtualnej jako pamięci podręcznej i uruchom go do weryfikowania łączności do pamięci podręcznej.


### <a name="when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Podczas próby nawiązania połączenia do mojego pamięci podręcznej Redis w sieci Wirtualnej, Dlaczego otrzymuję komunikat o błędzie informujący, że zdalny certyfikat jest nieprawidłowy.?

Podczas próby połączenia z pamięci podręcznej Redis w sieci Wirtualnej, zostanie wyświetlony błąd sprawdzania poprawności certyfikatu, takich jak ta:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Może to być spowodowane się, że host jest nawiązywane za pomocą adresu IP. Firma Microsoft zaleca korzystanie z nazwą hosta. Innymi słowy Użyj następującego polecenia:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Należy unikać podobne do następujących parametrów połączenia adres IP:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Jeśli nie można rozpoznać nazwy DNS, niektóre biblioteki klienta obejmują m.in `sslHost` przekazywane przez klienta w programie StackExchange.Redis. Dzięki temu można zastąpić nazwę hosta, używany do sprawdzania poprawności certyfikatu. Na przykład:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Sieci wirtualne można używać z standard lub podstawowa pamięci podręcznej?
Sieci wirtualne można używać tylko z pamięci podręcznych premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Dlaczego Tworzenie pamięci podręcznej Redis powiedzie w niektórych podsieci, a innych nie?
Jeśli wdrażasz pamięć podręczna Redis Azure Resource Manager sieci wirtualnej pamięci podręcznej musi być w dedykowanym podsieci, która zawiera inny typ zasobu. Jeśli próby wdrożenia pamięć podręczna Redis Azure do podsieci sieci wirtualnej Menedżera zasobów zawiera inne zasoby, wdrożenie zakończy się niepowodzeniem. Przed utworzeniem nowej pamięci podręcznej Redis, należy usunąć istniejące zasoby w tej podsieci.

Można wdrożyć wiele typów zasobów klasycznych sieci wirtualnej, tak długo, jak długo ma za mało dostępnych adresów IP.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jakie są wymagania dotyczące miejsca na adres podsieci?
Azure rezerwuje niektórych adresów IP w każdej podsieci, a nie można użyć tych adresów. Imię i nazwisko adresów IP podsieci są zastrzeżone dla protokołu zgodność, wraz z trzech więcej adresów używanych na potrzeby usług Azure. Aby uzyskać więcej informacji, zobacz [istnieją wszystkie ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresy IP używane przez infrastrukturę sieci Wirtualnej Azure Redis każdego wystąpienia w podsieci adresów IP używa dwóch na niezależnych i jeden dodatkowy adres IP usługi równoważenia obciążenia. Pamięć podręczna nieklastrowanych została uznana za jeden identyfikator niezależnego fragmentu.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Wszystkie funkcje pamięci podręcznej działają odnośnie do hostowania pamięci podręcznej w sieci Wirtualnej?
Jeśli pamięć podręczna jest częścią sieci Wirtualnej, tylko klienci w sieci Wirtualnej można uzyskać dostępu do pamięci podręcznej. W związku z tym następujące funkcje zarządzania pamięci podręcznej nie działają w tej chwili.

* Redis konsoli — konsoli Redis jest uruchamiana w przeglądarce lokalnego znajduje się poza siecią Wirtualną, nie może nawiązać połączenia z pamięci podręcznej.


## <a name="use-expressroute-with-azure-redis-cache"></a>Użyj usługi ExpressRoute z pamięcią podręczną Azure Redis

Klienci mogą się łączyć [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) obwód do infrastruktury sieci wirtualnej, a więc rozszerzenia sieci lokalnej na platformie Azure. 

Domyślnie nowo utworzony obwodu usługi expressroute nie tunelowanie wymuszone (anons trasę domyślną wartość 0.0.0.0/0) w sieci Wirtualnej. W związku z tym wychodzące połączenie internetowe jest dozwolona bezpośrednio z siecią Wirtualną i aplikacje klienckie mogą nawiązać połączenia z innymi punkty końcowe systemu Azure, łącznie z pamięcią podręczną Redis Azure.

Jednak typowych konfiguracji klienta jest użycie wymuszonego tunelowania (anonsowanie trasy domyślnej) który wymusza wychodzący ruch internetowy, zamiast niego przepływ lokalnymi. Ten przepływ ruchu przerywa połączenie z pamięcią podręczną Redis Azure w przypadku ruchu wychodzącego, a następnie zablokowany lokalnymi w taki sposób, że wystąpienie pamięci podręcznej Redis Azure nie jest w stanie nawiązać połączenia z jego zależności.

Rozwiązanie jest określenie jednego (lub więcej) trasy zdefiniowane przez użytkownika (Udr) na podsieci, która zawiera pamięci podręcznej Redis Azure. PRZEZ definiuje tras specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.

Jeśli to możliwe zaleca się następującej konfiguracji:

* Konfiguracji usługi ExpressRoute anonsuje 0.0.0.0/0 i domyślnie życie tuneli wszystkich ruch wychodzący lokalnymi.
* PRZEZ stosowana do podsieci, zawierający pamięci podręcznej Redis Azure definiuje 0.0.0.0/0 trasa pracy dla ruchu protokołu TCP/IP do publicznego Internetu; na przykład przez ustawienie Typ następnego przeskoku "Internet".

Łączna te kroki powoduje, że poziomie podsieci przez ma pierwszeństwo przed ExpressRoute, wymuszone tunelowanie, w związku z tym zapewnienie wychodzący dostęp do Internetu z pamięci podręcznej Redis Azure.

Łączenie do wystąpienia pamięci podręcznej Redis Azure z aplikacji lokalnych przy użyciu usługi ExpressRoute nie jest typowy sposób scenariusza z powodów wydajności (Aby uzyskać najlepszą wydajność pamięci podręcznej Redis Azure klienci powinni mieć w tym samym regionie co pamięć podręczna Redis Azure).

>[!IMPORTANT] 
>Trasy zdefiniowane w przez **musi** jest wystarczająco konkretny, aby mają pierwszeństwo względem dowolnego trasy anonsowane przez konfiguracji usługi ExpressRoute. Poniższy przykład używa 0.0.0.0/0 szeroki zakres adresów i jako taki może potencjalnie być przypadkowo przesłonięta przez anonsów tras za pomocą bardziej szczegółowych zakresów adresów.

>[!WARNING]  
>Pamięć podręczna Redis Azure nie jest obsługiwany w konfiguracji usługi ExpressRoute który **niepoprawnie cross anonsować tras z publicznej komunikacji równorzędnej ścieżki do ścieżki prywatnej komunikacji równorzędnej**. Konfiguracji usługi ExpressRoute, które mają publicznej komunikacji równorzędnej skonfigurowane, otrzymywać anonsów tras od firmy Microsoft dla dużych zestawów zakresów adresów IP firmy Microsoft Azure. Jeśli te zakresy adresów są niepoprawnie cross anonsowany w ścieżce prywatnej komunikacji równorzędnej, wynik są wszystkie pakiety wychodzącego z podsieci wystąpienia pamięci podręcznej Redis Azure niepoprawnie force-tunneled do infrastruktury sieci lokalnej klienta. Ten przepływ sieci dzieli pamięci podręcznej Redis Azure. Rozwiązanie tego problemu jest zatrzymanie tras między reklam z publicznej komunikacji równorzędnej ścieżki do ścieżki prywatnej komunikacji równorzędnej.


Informacje na trasach zdefiniowanych przez użytkownika jest dostępna w tym [omówienie](../virtual-network/virtual-networks-udr-overview.md).

Aby uzyskać więcej informacji na temat połączenia ExpressRoute, zobacz [opis techniczny ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

