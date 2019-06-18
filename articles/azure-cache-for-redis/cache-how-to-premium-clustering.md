---
title: Konfigurowanie pamięci podręcznej Redis klastrowania dla usługi Azure Cache w warstwie Premium dla usługi Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać nimi klastrowanie Redis w warstwie Premium usługi Azure Cache dla wystąpień usługi Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: 602d77f3d4e8ed10c2c964462bc2dc21240cef5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60541390"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Konfigurowanie pamięci podręcznej Redis klastrowania dla usługi Azure Cache w warstwie Premium dla usługi Redis
Pamięć podręczna systemu Azure dla usługi Redis zawiera pamięci podręcznej różnych ofert, które zapewniają elastyczność przy wyborze rozmiar pamięci podręcznej i funkcji, takich jak funkcje warstwy Premium, takich jak klastrowanie, trwałość i obsługę sieci wirtualnej. W tym artykule opisano jak skonfigurować klaster w warstwie premium usługi Azure Cache dla wystąpienia usługi Redis.

Aby uzyskać informacje o innych funkcjach pamięci podręcznej — wersja premium, zobacz [wprowadzenie do usługi Azure Cache w warstwie Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Co to jest klaster pamięci podręcznej Redis?
Pamięć podręczna systemu Azure dla usługi Redis zapewnia klastrowania Redis jako [zaimplementowane w pamięci podręcznej Redis](https://redis.io/topics/cluster-tutorial). Za pomocą klaster pamięci podręcznej Redis możesz uzyskać następujące korzyści: 

* Zdolność do automatycznego dzielenia zestawu danych między wieloma węzłami. 
* Możliwość kontynuowania operacji po podzbioru węzłów występują błędy, lub nie można nawiązać komunikacji z pozostałą częścią klastra. 
* Więcej przepływności: Przepływność zwiększa liniowo, jak zwiększyć liczbę fragmentów. 
* Większym rozmiarze pamięci: Zwiększa liniowo, jak zwiększyć liczbę fragmentów.  

Klaster nie zwiększa liczbę połączeń, które są dostępne dla klastra pamięci podręcznej. Aby uzyskać więcej informacji na temat rozmiaru, przepływności i przepustowości w pamięciach podręcznych premium, zobacz [jakich pamięć podręczna systemu Azure, oferty pamięci podręcznej Redis i rozmiaru należy używać?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Na platformie Azure klastra pamięci podręcznej Redis jest oferowana jako model podstawowy i węzeł repliki, gdzie każdy fragment ma parę podstawowy i węzeł repliki za pomocą replikacji, gdy replikacja jest zarządzana przez pamięć podręczna systemu Azure dla usługi Redis. 

## <a name="clustering"></a>Klastrowanie
Klastrowanie jest włączona na **nowej usługi Azure Cache dla usługi Redis** bloku podczas tworzenia pamięci podręcznej. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Klaster jest skonfigurowany na **klaster pamięci podręcznej Redis** bloku.

![Klastrowanie][redis-cache-clustering]

Może mieć maksymalnie 10 fragmentów w klastrze. Kliknij przycisk **włączone** i przesuń suwak lub wpisz liczbę z zakresu od 1 do 10 **liczba fragmentów** i kliknij przycisk **OK**.

Każdy fragment jest podstawowy i węzeł repliki pary pamięci podręcznej, zarządzane przez platformę Azure, a całkowity rozmiar pamięci podręcznej jest obliczane przez pomnożenie liczbę fragmentów przez wybrany w warstwie cenowej rozmiar pamięci podręcznej. 

![Klastrowanie][redis-cache-clustering-selected]

Po utworzeniu pamięci podręcznej możesz nawiązać z nim, a następnie użyj po prostu jak pamięć podręczna nieklastrowanych i Redis rozprowadza dane w pamięci podręcznej fragmenty. Jeśli Diagnostyka jest [włączone](cache-how-to-monitor.md#enable-cache-diagnostics), metryk jest przechwytywana osobno dla każdego fragmentu i może być [wyświetlane](cache-how-to-monitor.md) w pamięci podręcznej bloku pamięci podręcznej Redis Azure. 

> [!NOTE]
> 
> Istnieją pewne niewielkie różnice, wymagane w aplikacji klienckiej, gdy klaster jest skonfigurowany. Aby uzyskać więcej informacji, zobacz [należy wprowadzać żadnych zmian Moja aplikacja kliencka, aby użyć klastrowania?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Przykładowy kod na temat pracy z usługą klastrowania z klienta StackExchange.Redis, zobacz [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) część [Witaj, świecie](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) próbki.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Zmiana rozmiaru klastra, na uruchomione cache w warstwie premium
Aby zmienić rozmiar klastra, na uruchomione cache w warstwie premium z klastrowaniem włączona, kliknij przycisk **rozmiar klastra Redis** z **menu zasobów**.

> [!NOTE]
> Gdy pamięci podręcznej Redis w warstwie Premium Azure został zwolniony ogólnie dostępne, funkcja rozmiar klastra Redis jest obecnie w wersji zapoznawczej.
> 
> 

![Rozmiar klastra redis][redis-cache-redis-cluster-size]

Aby zmienić rozmiar klastra, za pomocą suwaka, lub wpisz liczbę z zakresu od 1 do 10 w **liczba fragmentów** polu tekstowym i kliknij **OK** do zapisania.

Zwiększenie rozmiaru klastra maksymalna przepływność i zwiększa rozmiar pamięci podręcznej. Zwiększenie rozmiaru klastra nie zwiększyć wartość maksymalną. połączenia są dostępne dla klientów.

> [!NOTE]
> Skalowanie klastra działa [migracji](https://redis.io/commands/migrate) polecenia, które jest kosztowne polecenia, więc dla minimalnym wpływem, należy wziąć pod uwagę wykonywania tej operacji podczas poza szczytem. Podczas procesu migracji zobaczysz wzrost obciążenia serwera. Skalowanie klastra jest bardzo długo uruchomiony proces i ilość czasu potrzebną zależy od liczby kluczy i rozmiar wartości skojarzone z tych kluczy.
> 
> 

## <a name="clustering-faq"></a>Klastrowanie — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Cache klastrowania Redis.

* [Należy wprowadzać żadnych zmian do mojej aplikacji klienta do korzystania z klastrowania?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Jak klucze są dystrybuowane w klastrze?](#how-are-keys-distributed-in-a-cluster)
* [Co to jest największy rozmiar pamięci podręcznej, który mogę utworzyć?](#what-is-the-largest-cache-size-i-can-create)
* [Wszyscy klienci Redis obsługują klastra?](#do-all-redis-clients-support-clustering)
* [Jak nawiązać połączenie z przepełnieniu pamięci podręcznej po włączeniu klastrowania?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Czy mogę bezpośrednio nawiązać poszczególne fragmenty przepełnieniu pamięci podręcznej?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Czy mogę skonfigurować klaster w utworzonej wcześniej pamięci podręcznej?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Czy mogę skonfigurować klaster pamięci podręcznej podstawowa lub standardowa?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Czy mogę użyć klastrowania, za pomocą dostawcy stanu sesji ASP.NET dla usługi Redis i buforowanie danych wyjściowych?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Otrzymuję przenoszenie wyjątków, korzystając z StackExchange.Redis i klastrowania, co należy zrobić?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Należy wprowadzać żadnych zmian do mojej aplikacji klienta do korzystania z klastrowania?
* Gdy klaster jest włączona, tylko bazy danych 0 jest dostępna. Jeśli Twoja aplikacja kliencka używa wielu baz danych i próby odczytu lub zapisu w bazie danych innych niż 0, zostanie zgłoszony następujący wyjątek. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Aby uzyskać więcej informacji, zobacz [Redis specyfikacja klastra — zaimplementowano podzbioru](https://redis.io/topics/cluster-spec#implemented-subset).
* Jeśli używasz [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), należy użyć 1.0.481 lub nowszej. Łączenie z pamięcią podręczną, korzystając z tych samych [punktów końcowych, porty i klucze](cache-configure.md#properties) używanego podczas nawiązywania połączenia z pamięcią podręczną, które ma włączone klastrowanie. Jedyna różnica polega na tym, że wszystkie operacje odczytu i zapisu musi odbywać się do bazy danych 0.
  
  * Inni klienci mogą mieć inne wymagania. Zobacz [wszystkich klientów usługi Redis obsługują klastra?](#do-all-redis-clients-support-clustering)
* Jeśli aplikacja używa wielu kluczy operacji przetwarzane wsadowo w pojedynczym poleceniu, wszystkie klucze muszą znajdować się w tym samym fragmencie. Aby zlokalizować kluczy, w tym samym fragmencie, zobacz [klucze rozkład w klastrze?](#how-are-keys-distributed-in-a-cluster)
* Jeśli używasz dostawcy stanu sesji platformy ASP.NET w pamięci podręcznej Redis, należy użyć 2.0.1 lub nowszej. Zobacz [Użyj, klastrowania z dostawcy stanu sesji ASP.NET dla usługi Redis i buforowanie danych wyjściowych?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Jak klucze są dystrybuowane w klastrze?
Dla usługi Redis [modelu dystrybucji kluczy](https://redis.io/topics/cluster-spec#keys-distribution-model) dokumentacji: Przestrzeń kluczy jest dzielony na miejsc 16384. Każdy klucz jest wyznaczany skrót i przypisane do jednej z tych miejsc, które są rozproszone na węzłach klastra. Można skonfigurować, która część klucza jest przekazywane do upewnij się, że wiele kluczy znajdują się w tym samym fragmencie, za pomocą tagów wyznaczania wartości skrótu.

* Klucze z tagiem wyznaczania wartości skrótu — Jeśli dowolnej części klucza jest ujęty w `{` i `}`, tylko część klucza jest wyznaczana wartość skrótu dla celów określenia miejsca wyznaczania wartości skrótu klucza. Na przykład, następujące klucze 3 znajduje się w tym samym fragmencie: `{key}1`, `{key}2`, i `{key}3` ponieważ tylko `key` część nazwy jest wyznaczana wartość skrótu. Pełną listę kluczy skrótu tag specyfikacje zobacz [klawiszy skrótu tagi](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Klucze bez tagu hash - całą nazwę klucza jest używana do wyznaczania wartości skrótu. Skutkuje to statystycznie rozdzielenie między fragmentami pamięci podręcznej.

Aby uzyskać najlepszą wydajność i przepływność firma Microsoft zaleca równomiernie dystrybucji kluczy. Jeśli używasz kluczy z tagu wyznaczania wartości skrótu jest aplikacji ponosić odpowiedzialność za zapewnienie klucze są rozmieszczane równomiernie.

Aby uzyskać więcej informacji, zobacz [modelu dystrybucji kluczy](https://redis.io/topics/cluster-spec#keys-distribution-model), [fragmentowania danych klaster pamięci podręcznej Redis](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), i [klawiszy skrótu tagi](https://redis.io/topics/cluster-spec#keys-hash-tags).

Przykładowy kod na temat pracy z klastra i lokalizowania kluczy w jednym fragmencie z klienta StackExchange.Redis, zobacz [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) część [Witaj, świecie](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) próbki.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Co to jest największy rozmiar pamięci podręcznej, który mogę utworzyć?
Największy rozmiar pamięci podręcznej — wersja premium jest 53 GB. Można utworzyć do 10 fragmentów, dzięki czemu maksymalny rozmiar 530 GB. Jeśli potrzebujesz większego rozmiaru [Zażądaj więcej](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Aby uzyskać więcej informacji, zobacz [pamięci podręcznej Azure redis Cache cennik](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Wszyscy klienci Redis obsługują klastra?
W chwili obecnej, których nie wszyscy klienci nie obsługują Redis klastrowania. StackExchange.Redis to taki, który obsługuje jej. Aby uzyskać więcej informacji na temat innych klientów, zobacz [odtwarzanie z klastrem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) części [samouczek dotyczący klastra Redis](https://redis.io/topics/cluster-tutorial). 

Protokół klastrowania pamięci podręcznej Redis wymaga każdego połączenia klienta z każdego fragmentu bezpośrednio w trybie klastrowania. Podjęto próbę użycia klienta, który nie obsługuje klastrowania prawdopodobnie spowoduje w partii [wyjątki przekierowania PRZENIESIONO](https://redis.io/topics/cluster-spec#moved-redirection).

> [!NOTE]
> Jeśli są używane jako klienta StackExchange.Redis, upewnij się, przy użyciu najnowszej wersji [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 lub nowszej dla klastra, aby działać poprawnie. W przypadku jakichkolwiek problemów z wyjątkami przenoszenia zobacz [przenoszenie wyjątków](#move-exceptions) Aby uzyskać więcej informacji.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Jak nawiązać połączenie z przepełnieniu pamięci podręcznej po włączeniu klastrowania?
Możesz nawiązać połączenie z pamięci podręcznej, korzystając z tych samych [punktów końcowych](cache-configure.md#properties), [porty](cache-configure.md#properties), i [klucze](cache-configure.md#access-keys) używanego podczas nawiązywania połączenia z pamięcią podręczną, które ma włączone klastrowanie. Usługa redis zarządza usługi klastrowania na wewnętrznej bazie danych, dzięki czemu nie trzeba zarządzać nim z komputera klienckiego z.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Czy mogę bezpośrednio nawiązać poszczególne fragmenty przepełnieniu pamięci podręcznej?
Protokół klastrowania wymaga, że klient nawiązuj kontakty poprawne fragmentu. Dlatego klient należy to zrobić poprawnie dla Ciebie. Dzięki temu powiedział każdego fragmentu składa się z pary pamięci podręcznej podstawowy i węzeł repliki, nazywane zbiorczo wystąpienie pamięci podręcznej. Można połączyć się z tych wystąpień pamięci podręcznej przy użyciu narzędzia pamięci podręcznej redis — interfejs wiersza polecenia w [niestabilne](https://redis.io/download) gałąź repozytorium pamięci podręcznej Redis w witrynie GitHub. Ta wersja implementuje podstawowej pomocy technicznej, gdy pracę z usługą `-c` przełącznika. Aby uzyskać więcej informacji, zobacz [odtwarzanie z klastrem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) na [ https://redis.io ](https://redis.io) w [samouczek dotyczący klastra Redis](https://redis.io/topics/cluster-tutorial).

Aby uzyskać bez obsługi protokołu ssl Użyj następujących poleceń.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Dla protokołu ssl, należy zastąpić `1300N` z `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Czy mogę skonfigurować klaster w utworzonej wcześniej pamięci podręcznej?
Obecnie można włączyć tylko klastra po utworzeniu pamięci podręcznej. Po utworzeniu pamięci podręcznej, ale nie można dodać klastra cache w wersji premium lub usunąć klastrowanie z cache w warstwie premium, po utworzeniu pamięci podręcznej, można zmienić rozmiar klastra. Cache w warstwie premium przy włączonym klastrowaniu i tylko jeden fragment jest inny niż ten sam rozmiar z klastrowaniem nie cache w warstwie premium.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Czy mogę skonfigurować klaster pamięci podręcznej podstawowa lub standardowa?
Klastrowanie jest dostępna tylko dla pamięci podręcznych premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Czy mogę użyć klastrowania, za pomocą dostawcy stanu sesji ASP.NET dla usługi Redis i buforowanie danych wyjściowych?
* **Dostawca wyjściowej pamięci podręcznej redis** — bez konieczności wprowadzania zmian.
* **Dostawca stanu sesji w pamięci podręcznej redis** — Aby użyć klastrowania, należy użyć [pakietu RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub wyższą lub wyjątek, który jest generowany. To jest zmianą przerywającą; Aby uzyskać więcej informacji, zobacz [v2.0.0 istotne szczegóły zmian](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Otrzymuję przenoszenie wyjątków, korzystając z StackExchange.Redis i klastrowania, co należy zrobić?
Jeśli używasz StackExchange.Redis i otrzymywać `MOVE` wyjątki podczas korzystania z klastra, upewnij się, że używasz [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) lub nowszej. Aby uzyskać instrukcje na temat konfigurowania aplikacji .NET do korzystania z programu StackExchange.Redis, zobacz [Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do platformy Azure pamięci podręcznej Redis w warstwie Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







