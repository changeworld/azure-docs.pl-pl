---
title: Jak skonfigurować Redis klastrowania podręczna Redis Azure Premium | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i zarządzania nimi Redis klastrowania warstwę Premium wystąpienia pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: wesmc
ms.openlocfilehash: 4af6545058ab0031d7cd1b38618b6d80204f83b9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-redis-cache"></a>Jak skonfigurować Redis klastrowania podręczna Redis Azure Premium
Pamięć podręczna Redis Azure ma inną pamięci podręcznej oferty, które zapewniają elastyczność w wyborze rozmiar pamięci podręcznej i funkcji, łącznie z funkcji warstwy Premium, takich jak klastrowanie, trwałości i obsługi sieci wirtualnej. W tym artykule opisano sposób konfigurowania klastra w wystąpieniu usługi pamięć podręczna Redis Azure premium.

Aby uzyskać informacji o innych funkcjach pamięci podręcznej premium, zobacz [wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Co to jest Redis klastra?
Pamięć podręczna Redis Azure oferuje klaster Redis jako [zaimplementowana w pamięci podręcznej Redis](http://redis.io/topics/cluster-tutorial). Redis klastra można uzyskać następujące korzyści: 

* Możliwość automatycznie podzielić zestawu danych między wieloma węzłami. 
* Możliwość kontynuowania operacji po podzbioru węzłów występują błędy, lub nie mogą się komunikować z pozostałą częścią klastra. 
* Więcej przepustowości: przepływności zwiększa liniowo zwiększyć liczbę fragmentów. 
* Więcej rozmiar pamięci: liniowo zwiększa zwiększyć liczbę fragmentów.  

Klaster nie zwiększa liczbę połączeń dostępnych dla klastra pamięci podręcznej. Aby uzyskać więcej informacji o rozmiarze, przepływności i przepustowości z pamięci podręcznych premium, zobacz [jakie oferty pamięć podręczna Redis i rozmiar należy używać?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

Na platformie Azure Redis klastra jest oferowany jako model podstawowy/repliki, gdzie każdy identyfikator niezależnego fragmentu ma parę podstawowy/repliki replikacji, gdy replikacja jest zarządzane przez usługę pamięć podręczna Redis Azure. 

## <a name="clustering"></a>Klastrowanie
Włączono klaster **nowa pamięć podręczna Redis** bloku podczas tworzenia pamięci podręcznej. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Klaster jest skonfigurowany na **klaster Redis** bloku.

![Klastrowanie][redis-cache-clustering]

Może mieć maksymalnie 10 odłamków w klastrze. Kliknij przycisk **włączone** i przesuń suwak lub wpisz liczbę z zakresu od 1 do 10 dla **liczby niezależnych** i kliknij przycisk **OK**.

Każdy identyfikator niezależnego fragmentu jest zarządzane przez usługę Azure parę pamięć podręczna podstawowy/repliki, a całkowity rozmiar pamięci podręcznej jest obliczany przez pomnożenie liczba odłamków przez wybrany w warstwie cenowej rozmiar pamięci podręcznej. 

![Klastrowanie][redis-cache-clustering-selected]

Po utworzeniu pamięci podręcznej nawiązania połączenia i użyj go tylko pamięci podręcznej niedziałającego w klastrze, takich jak i Redis dystrybuuje dane w pamięci podręcznej fragmentów. W przypadku diagnostyki [włączone](cache-how-to-monitor.md#enable-cache-diagnostics), metryki są przechwytywane osobno dla każdej niezależnych i może być [wyświetlić](cache-how-to-monitor.md) w bloku pamięci podręcznej Redis. 

> [!NOTE]
> 
> Istnieją pewne niewielkie różnice w aplikacji klienta wymagane, jeśli klaster jest skonfigurowany. Aby uzyskać więcej informacji, zobacz [należy wprowadzać żadnych zmian do mojej aplikacji klienta do korzystania z klastra?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Przykładowy kod na temat pracy z usługą klastrowania z klientem w programie StackExchange.Redis, zobacz [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) część [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) próbki.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Zmień rozmiar klastra w uruchomionej usługi pamięć podręczna premium
Aby zmienić rozmiar klastra w uruchomionej usługi pamięć podręczna premium z usługą klastrowania włączone, kliknij przycisk **Redis rozmiar klastra** z **zasobów menu**.

> [!NOTE]
> Gdy warstwy pamięci podręcznej Redis Azure Premium został zwolniony ogólnej dostępności, funkcja Redis rozmiar klastra jest obecnie w przeglądzie.
> 
> 

![Rozmiar klastra redis][redis-cache-redis-cluster-size]

Aby zmienić rozmiar klastra, za pomocą suwaka, lub wpisz liczbę z zakresu od 1 do 10 w **liczby niezależnych** polu tekstowym i kliknij przycisk **OK** do zapisania.

Rozmiar klastra po zwiększeniu maksymalnej przepływności i rozmiaru pamięci podręcznej. Zwiększyć rozmiar klastra nie zwiększenia maksymalnej liczby. połączenia dostępne dla klientów.

> [!NOTE]
> Skalowanie klastra działa [migracji](https://redis.io/commands/migrate) polecenia, która jest kosztowna polecenia, dlatego dla minimalny wpływ, należy rozważyć uruchomieniem tej operacji podczas godziny poza szczytem. Podczas procesu migracji zobaczysz kolekcji w obciążenia serwera. Skalowanie klastra długi działa procesu i czas poświęcony na zależy od liczby kluczy i rozmiaru wartości skojarzone z tych kluczy.
> 
> 

## <a name="clustering-faq"></a>Klaster — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące klastrowania pamięć podręczna Redis Azure.

* [Należy wprowadzać żadnych zmian do mojej aplikacji klienta do korzystania z klastra?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Jak klucze są rozpowszechniane w klastrze?](#how-are-keys-distributed-in-a-cluster)
* [Co to jest największy rozmiar pamięci podręcznej, który można utworzyć?](#what-is-the-largest-cache-size-i-can-create)
* [Wszyscy klienci Redis obsługują klastra?](#do-all-redis-clients-support-clustering)
* [Jak połączyć Moje pamięci podręcznej po włączeniu klastra?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Może bezpośrednio podłączyć do poszczególnych odłamków Moje pamięci podręcznej?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Można skonfigurować klaster utworzonej wcześniej pamięci podręcznej?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Można skonfigurować klaster na potrzeby pamięci podręcznej planu basic lub standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Czy można użyć klastrowania z dostawcami stanu sesji ASP.NET dla pamięci podręcznej Redis i buforowanie danych wyjściowych](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Otrzymuję przenoszenie wyjątków, używając programie StackExchange.Redis i klastrowania, co należy zrobić?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Należy wprowadzać żadnych zmian do mojej aplikacji klienta do korzystania z klastra?
* Jeśli klaster jest włączone, tylko bazy danych 0 jest dostępna. Jeśli aplikacja kliencka używa wielu baz danych i próby odczytu lub zapisu w bazie danych innych niż 0, zostanie zgłoszony następujący wyjątek. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Aby uzyskać więcej informacji, zobacz [Redis specyfikacji klastra - zaimplementowanym podzestawu](http://redis.io/topics/cluster-spec#implemented-subset).
* Jeśli używasz [programie StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), należy użyć 1.0.481 lub nowszym. Połącz do pamięci podręcznej korzystającej z tego samego [punktów końcowych, portów i kluczy](cache-configure.md#properties) używanego podczas nawiązywania połączenia z pamięcią podręczną, która nie ma włączoną funkcją klastrowania. Jedyna różnica polega na tym, że wszystkie odczyty i zapisy należy wykonać w celu bazy danych 0.
  
  * Inni klienci mogą mieć inne wymagania. Zobacz [wszystkich klientów pamięci podręcznej Redis obsługują klastra?](#do-all-redis-clients-support-clustering)
* Jeśli aplikacja używa wielu operacji klucza przetwarzane wsadowo w jedno polecenie, wszystkie klucze muszą znajdować się w tej samej niezależnego fragmentu. Aby zlokalizować klucze w sam identyfikator niezależnego fragmentu, zobacz [klucze rozkład w klastrze?](#how-are-keys-distributed-in-a-cluster)
* Jeśli używasz dostawcy stanu sesji ASP.NET dla pamięci podręcznej Redis należy użyć 2.0.1 lub nowszej. Zobacz [użyć, klastra z dostawcami stanu sesji ASP.NET dla pamięci podręcznej Redis i buforowanie danych wyjściowych?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Jak klucze są rozpowszechniane w klastrze?
Na Redis [modelu dystrybucji kluczy](http://redis.io/topics/cluster-spec#keys-distribution-model) dokumentacji: klucza miejsca jest podzielony na 16384 gniazda. Każdy klucz jest mieszany i przypisane do jednej z tych gniazd, które są dystrybuowane między węzłami klastra. Można skonfigurować, która część klucza jest wartość skrótu, aby upewnić się, że wiele kluczy znajdują się w sam identyfikator niezależnego fragmentu przy użyciu tagów wyznaczania wartości skrótu.

* Klucze z tagiem skrótu — Jeśli dowolną część klucza jest ujęta w `{` i `}`, tylko część klucza jest wartość skrótu na potrzeby określania miejsca skrótu klucza. Na przykład następujące klucze 3 znajduje się w tej samej niezależnego fragmentu: `{key}1`, `{key}2`, i `{key}3` ponieważ tylko `key` część nazwy jest wartość skrótu. Pełną listę klawiszy skrótu tag specyfikacji zobacz [klawiszy skrótu tagi](http://redis.io/topics/cluster-spec#keys-hash-tags).
* Klucze bez tagu skrótu - całą nazwę klucza jest używany do tworzenia skrótów. W efekcie statystycznie nawet dystrybucji między odłamków pamięci podręcznej.

Aby uzyskać najlepszą wydajność i przepływność zaleca się równomiernie dystrybucji kluczy. Jeśli używane są klucze z tag skrótów odpowiada aplikacji upewnij się, klucze są rozmieszczane równomiernie.

Aby uzyskać więcej informacji, zobacz [modelu dystrybucji kluczy](http://redis.io/topics/cluster-spec#keys-distribution-model), [dzielenia na fragmenty danych Redis klastra](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), i [klawiszy skrótu tagi](http://redis.io/topics/cluster-spec#keys-hash-tags).

Przykładowy kod na temat pracy z klastra i lokalizowania kluczy w sam identyfikator niezależnego fragmentu z klientem w programie StackExchange.Redis, zobacz [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) część [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) próbki.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Co to jest największy rozmiar pamięci podręcznej, który można utworzyć?
Największy rozmiar pamięci podręcznej premium jest 53 GB. Możesz utworzyć maksymalnie 10 odłamków, umożliwiając maksymalny rozmiar 530 GB. Jeśli potrzebujesz większy rozmiar można [żądania więcej](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Redis pamięci podręcznej](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Wszyscy klienci Redis obsługują klastra?
Obecnie nie wszystkie obsługują klientów pamięci podręcznej Redis klastra. Programie StackExchange.Redis to taki, który obsługuje dla niego. Aby uzyskać więcej informacji dotyczących innych klientów, zobacz [odtwarzanie z klastrem](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) sekcji [samouczek klaster Redis](http://redis.io/topics/cluster-tutorial).

> [!NOTE]
> Jeśli używasz programie StackExchange.Redis jako klienta, upewnij się, korzystają z najnowszej wersji [programie StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 lub później dla klastra, aby działać poprawnie. Jeśli masz problemy z wyjątków przenoszenia, zobacz [przenoszenie wyjątków](#move-exceptions) Aby uzyskać więcej informacji.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Jak połączyć Moje pamięci podręcznej po włączeniu klastra?
Możesz połączyć się z pamięci podręcznej korzystającej z tego samego [punkty końcowe](cache-configure.md#properties), [porty](cache-configure.md#properties), i [klucze](cache-configure.md#access-keys) używanego podczas nawiązywania połączenia z pamięcią podręczną, która nie ma włączoną funkcją klastrowania. Redis zarządza usługi klastrowania na wewnętrznej bazy danych, więc nie trzeba zarządzać nim z klienta.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Może bezpośrednio podłączyć do poszczególnych odłamków Moje pamięci podręcznej?
To nie jest oficjalnie obsługiwana. Z tym powiedział każdy identyfikator niezależnego fragmentu składa się z pary pamięci podręcznej podstawowy/repliki, nazywanych zbiorczo wystąpienia pamięci podręcznej. Możesz połączyć się te wystąpienia pamięci podręcznej za pomocą narzędzia interfejsu wiersza polecenia redis w [niestabilny](http://redis.io/download) gałęzi repozytorium Redis w witrynie GitHub. Ta wersja implementuje podstawowa pomoc techniczna podczas pracy z `-c` przełącznika. Aby uzyskać więcej informacji, zobacz [odtwarzanie z klastrem](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) na [ http://redis.io ](http://redis.io) w [samouczek klaster Redis](http://redis.io/topics/cluster-tutorial).

Dla bez użycia protokołu ssl należy użyć następujących poleceń.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Dla protokołu ssl, należy zastąpić `1300N` z `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Można skonfigurować klaster utworzonej wcześniej pamięci podręcznej?
Obecnie można włączyć tylko klastra podczas tworzenia pamięci podręcznej. Rozmiar klastra można zmienić po utworzeniu pamięci podręcznej, ale nie można dodać, klaster do pamięci podręcznej premium lub usunąć klastrowanie z pamięci podręcznej premium po utworzeniu pamięci podręcznej. Pamięć podręczna premium z włączoną funkcją klastrowania i tylko jeden identyfikator niezależnego fragmentu różni się od tego samego rozmiaru z klastrowaniem nie pamięć podręczna premium.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Można skonfigurować klaster na potrzeby pamięci podręcznej planu basic lub standard?
Klaster jest dostępna tylko dla pamięci podręcznej premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Czy można użyć klastrowania z dostawcami stanu sesji ASP.NET dla pamięci podręcznej Redis i buforowanie danych wyjściowych
* **Dostawcy wyjściowej pamięci podręcznej redis** -żadne zmiany wymagane.
* **Dostawca stanu sesji w pamięci podręcznej redis** — do klastrowania, należy użyć [pakietu RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub nowszej albo wyjątek zostanie zgłoszony. Jest to istotne zmiany; Aby uzyskać więcej informacji, zobacz [v2.0.0 fundamentalne zmiany szczegółów](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Otrzymuję przenoszenie wyjątków, używając programie StackExchange.Redis i klastrowania, co należy zrobić?
Jeśli używasz programie StackExchange.Redis i odbierać `MOVE` wyjątków podczas korzystania z klastra, upewnij się, że używasz [programie StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) lub nowszym. Aby uzyskać instrukcje na temat konfigurowania aplikacji platformy .NET do użycia w programie StackExchange.Redis, zobacz [Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







