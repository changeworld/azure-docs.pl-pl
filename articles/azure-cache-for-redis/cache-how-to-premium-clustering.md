---
title: Konfigurowanie klastrowania Redis — premium azure cache for Redis
description: Dowiedz się, jak tworzyć klastry Redis dla pamięci podręcznej platformy Azure w warstwie Premium dla wystąpień Redis i zarządzać nimi.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 4a0e5b0c18264e1f7a98e81bcdfd56a7159235da
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010923"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Jak skonfigurować klastrowanie Redis dla pamięci podręcznej premium Azure dla redis
Usługa Azure Cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru pamięci podręcznej i funkcji, w tym funkcji warstwy Premium, takich jak klastrowanie, trwałość i obsługa sieci wirtualnej. W tym artykule opisano sposób konfigurowania klastrowania w pamięci podręcznej platformy Azure w warstwie Premium dla wystąpienia Redis.

Aby uzyskać informacje na temat innych funkcji pamięci podręcznej w warstwie Premium, zobacz [Wprowadzenie do warstwy Azure Cache for Redis Premium.](cache-premium-tier-intro.md)

## <a name="what-is-redis-cluster"></a>Co to jest Klaster Redis?
Usługa Azure Cache for Redis oferuje klaster Redis [zgodnie z implementacji w Redis](https://redis.io/topics/cluster-tutorial). Dzięki Redis Cluster otrzymujesz następujące korzyści: 

* Możliwość automatycznego dzielenia zestawu danych między wiele węzłów. 
* Możliwość kontynuowania operacji, gdy podzbiór węzłów występuje błędy lub nie są w stanie komunikować się z resztą klastra. 
* Większa przepustowość: Przepływność zwiększa się liniowo w miarę zwiększania liczby fragmentów. 
* Większy rozmiar pamięci: Zwiększa liniowo wraz ze wzrostem liczby fragmentów.  

Klastrowanie nie zwiększa liczby połączeń dostępnych dla klastrowanej pamięci podręcznej. Aby uzyskać więcej informacji na temat rozmiaru, przepływności i przepustowości z pamięciami podręcznymi premium, [zobacz, jakiej usługi Azure Cache dla oferty i rozmiaru redis należy używać?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Na platformie Azure klaster Redis jest oferowany jako model podstawowy/replika, w którym każdy niezależnego fragmentu ma pary podstawowej/repliki z replikacją, gdzie replikacja jest zarządzana przez usługę Azure Cache for Redis. 

## <a name="clustering"></a>Klastrowanie
Klastrowanie jest włączone w **nowej pamięci podręcznej azure dla redis** bloku podczas tworzenia pamięci podręcznej. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Klastrowanie jest skonfigurowane w bloku **klastra Redis.**

![Klastrowanie][redis-cache-clustering]

W klastrze może być maksymalnie 10 fragmentów. Kliknij **pozycję Włączone** i przesuń suwak lub wpisz liczbę z 1 do 10 dla liczby **niezależnego fragmentu** i kliknij przycisk **OK**.

Każdy fragment jest parą podstawowej/repliki pamięci podręcznej zarządzanej przez platformę Azure, a całkowity rozmiar pamięci podręcznej jest obliczany przez pomnożenie liczby fragmentów przez rozmiar pamięci podręcznej wybrany w warstwie cenowej. 

![Klastrowanie][redis-cache-clustering-selected]

Po utworzeniu pamięci podręcznej łączysz się z nią i używasz jej tak samo jak pamięci podręcznej nieklastrowanej, a Redis dystrybuuje dane w ramach fragmentów pamięci podręcznej. Jeśli diagnostyka jest [włączona,](cache-how-to-monitor.md#enable-cache-diagnostics)metryki są przechwytywane oddzielnie dla każdego niezależnego fragmentu i mogą być [wyświetlane](cache-how-to-monitor.md) w pamięci podręcznej azure dla bloku Redis. 

> [!NOTE]
> 
> Istnieją pewne niewielkie różnice wymagane w aplikacji klienckiej podczas konfigurowania klastrowania. Aby uzyskać więcej informacji, zobacz [Czy muszę wprowadzić zmiany w aplikacji klienckiej, aby używać klastrowania?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Przykładowy kod dotyczący pracy z klastrem z klientem StackExchange.Redis można znaleźć w [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) części przykładu [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Zmienianie rozmiaru klastra w uruchomionej pamięci podręcznej premium
Aby zmienić rozmiar klastra w uruchomionej pamięci podręcznej premium z włączoną klastrem, kliknij polecenie **Rozmiar klastra** z **menu Zasób**.

![Rozmiar klastra Redis][redis-cache-redis-cluster-size]

Aby zmienić rozmiar klastra, użyj suwaka lub wpisz liczbę z 1 do 10 w polu tekstowym **Liczba niezależnego fragmentu** i kliknij przycisk **OK,** aby zapisać.

Zwiększenie rozmiaru klastra zwiększa maksymalną przepływność i rozmiar pamięci podręcznej. Zwiększenie rozmiaru klastra nie zwiększa maks. połączeń dostępnych dla klientów.

> [!NOTE]
> Skalowanie klastra uruchamia polecenie [MIGRATE,](https://redis.io/commands/migrate) które jest kosztownym poleceniem, więc dla minimalnego wpływu należy rozważyć uruchomienie tej operacji w godzinach poza szczytem. Podczas procesu migracji zostanie wyświetlony skok obciążenia serwera. Skalowanie klastra jest procesem długotrwałym, a czas trwania zależy od liczby kluczy i rozmiaru wartości skojarzonych z tymi kluczami.
> 
> 

## <a name="clustering-faq"></a>Często zadawane pytania dotyczące klastrowania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Cache dla klastrowania Redis.

* [Czy muszę wprowadzić zmiany w aplikacji klienckiej, aby używać klastrowania?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Jak klucze są dystrybuowane w klastrze?](#how-are-keys-distributed-in-a-cluster)
* [Jaki jest największy rozmiar pamięci podręcznej, jaki mogę utworzyć?](#what-is-the-largest-cache-size-i-can-create)
* [Czy wszyscy klienci Redis obsługują klastrowanie?](#do-all-redis-clients-support-clustering)
* [Jak połączyć się z pamięcią podręczną, gdy klastrowanie jest włączone?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Czy mogę bezpośrednio połączyć się z poszczególnymi fragmentami pamięci podręcznej?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Czy można skonfigurować klastrowanie dla wcześniej utworzonej pamięci podręcznej?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Czy można skonfigurować klastrowanie dla podstawowej lub standardowej pamięci podręcznej?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Czy można używać klastrowania z dostawcami buforowania ASP.NET redis i wyjściowych?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Oprowadzam wyjątki MOVE podczas korzystania z StackExchange.Redis i klastrowania, co należy zrobić?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Czy muszę wprowadzić zmiany w aplikacji klienckiej, aby używać klastrowania?
* Gdy klastrowanie jest włączone, dostępna jest tylko baza danych 0. Jeśli aplikacja kliencka używa wielu baz danych i próbuje odczytać lub zapisać do bazy danych innych niż 0, zgłaszany jest następujący wyjątek. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Aby uzyskać więcej informacji, zobacz [Specyfikacja klastra Redis — zaimplementowany podzbiór](https://redis.io/topics/cluster-spec#implemented-subset).
* Jeśli używasz [StackExchange.Redis,](https://www.nuget.org/packages/StackExchange.Redis/)należy użyć 1.0.481 lub nowszej. Łączysz się z pamięcią podręczną przy użyciu tych [samych punktów końcowych, portów i kluczy,](cache-configure.md#properties) których używasz podczas łączenia się z pamięcią podręczną, która nie ma włączonego klastrowania. Jedyną różnicą jest to, że wszystkie odczyty i zapisy muszą być wykonane do bazy danych 0.
  
  * Inni klienci mogą mieć różne wymagania. Zobacz [Czy wszyscy klienci Redis obsługują klastrowanie?](#do-all-redis-clients-support-clustering)
* Jeśli aplikacja używa wielu operacji klucza wsadowe do jednego polecenia, wszystkie klucze muszą znajdować się w tym samym niezależnego fragmentu. Aby zlokalizować klucze w tym samym niezależniu, zobacz [Jak klucze są dystrybuowane w klastrze?](#how-are-keys-distributed-in-a-cluster)
* Jeśli używasz dostawcy Redis ASP.NET Session State, musisz użyć 2.0.1 lub nowszego. Zobacz [Czy mogę używać klastrowania z dostawcami buforowania ASP.NET redis i wyjściowych?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Jak klucze są dystrybuowane w klastrze?
Zgodnie z dokumentacją [modelu dystrybucji Kluczy](https://redis.io/topics/cluster-spec#keys-distribution-model) Redis: Przestrzeń klucza jest podzielona na 16384 slotów. Każdy klucz jest mieszany i przypisany do jednego z tych gniazd, które są rozproszone między węzłami klastra. Można skonfigurować, która część klucza jest mieszana, aby upewnić się, że wiele kluczy znajdują się w tym samym niezależnego fragmentu przy użyciu znaczników skrótu.

* Klucze z hasztagiem - jeśli dowolna `{` `}`część klucza jest ujęta i , tylko ta część klucza jest mieszana w celu określenia gniazda mieszania klucza. Na przykład następujące 3 klucze znajdują się w `{key}1` `{key}2`tym `{key}3` samym fragment: , i ponieważ tylko `key` część nazwy jest hashed. Aby uzyskać pełną listę specyfikacji znaczników skrótu kluczy, zobacz [Znaczniki skrótu kluczy](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Klucze bez znacznika skrótu — cała nazwa klucza jest używana do mieszania. Powoduje to statystycznie równomierny rozkład między fragmentami pamięci podręcznej.

Aby uzyskać najlepszą wydajność i przepływność, zaleca się równomierne rozłożenie kluczy. Jeśli używasz kluczy z hasztagiem, jest to odpowiedzialność aplikacji, aby upewnić się, że klucze są dystrybuowane równomiernie.

Aby uzyskać więcej informacji, zobacz [Model dystrybucji kluczy,](https://redis.io/topics/cluster-spec#keys-distribution-model) [Dzielenie fragmentów klastra Redis](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)i [Znaczniki skrótów Kluczy](https://redis.io/topics/cluster-spec#keys-hash-tags).

Przykładowy kod dotyczący pracy z klastrowania i lokalizowania kluczy w tym samym niezależnego fragmentu z klientem StackExchange.Redis, zobacz [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) część przykładu [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Jaki jest największy rozmiar pamięci podręcznej, jaki mogę utworzyć?
Największy rozmiar pamięci podręcznej premium to 120 GB. Można utworzyć maksymalnie 10 fragmentów o maksymalnym rozmiarze 1,2 TB GB. Jeśli potrzebujesz większego rozmiaru, możesz [poprosić o więcej](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Aby uzyskać więcej informacji, zobacz [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Czy wszyscy klienci Redis obsługują klastrowanie?
Nie wszyscy klienci obsługują klastrowanie Redis! Sprawdź dokumentację używanej biblioteki, aby sprawdzić, czy używasz biblioteki i wersji obsługujących klastrowanie. StackExchange.Redis jest jedną biblioteką, która obsługuje klastrowanie w nowszych wersjach. Aby uzyskać więcej informacji na temat innych klientów, zobacz sekcję [Odtwarzanie z klastrem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [samouczka klastra Redis](https://redis.io/topics/cluster-tutorial). 

Protokół klastrowania Redis wymaga, aby każdy klient łączył się z każdym fragmentem bezpośrednio w trybie klastrowania, a także definiuje nowe odpowiedzi na błędy, takie jak "PRZENIESIONY" na 'CROSSSLOTS'. Próba użycia klienta, który nie obsługuje klastrowania z pamięci podręcznej trybu klastra może spowodować wiele [wyjątków przekierowania przeniesiony,](https://redis.io/topics/cluster-spec#moved-redirection)lub po prostu przerwać aplikację, jeśli robisz cross-slot żądań wielu kluczy.

> [!NOTE]
> Jeśli używasz StackExchange.Redis jako klienta, upewnij się, że używasz najnowszej wersji [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 lub nowszej do klastrowania do poprawnego działania. Jeśli masz jakiekolwiek problemy z wyjątkami przenoszenia, zobacz [przenieść wyjątki, aby](#move-exceptions) uzyskać więcej informacji.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Jak połączyć się z pamięcią podręczną, gdy klastrowanie jest włączone?
Można połączyć się z pamięcią podręczną przy użyciu tych [samych punktów końcowych,](cache-configure.md#properties) [portów](cache-configure.md#properties)i [kluczy,](cache-configure.md#access-keys) których używasz podczas łączenia się z pamięcią podręczną, która nie ma włączonego klastrowania. Redis zarządza klastrowania w wewnętrznej bazy danych, więc nie trzeba zarządzać nim z klienta.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Czy mogę bezpośrednio połączyć się z poszczególnymi fragmentami pamięci podręcznej?
Protokół klastrowania wymaga, aby klient nawiązywania poprawnych połączeń niezależnego fragmentu. Więc klient powinien zrobić to poprawnie dla Ciebie. Z powiedział, że każdy fragment składa się z pary podstawowej/repliki pamięci podręcznej, łącznie znany jako wystąpienie pamięci podręcznej. Można połączyć się z tych wystąpień pamięci podręcznej za pomocą narzędzia redis-cli w [gałęzi niestabilne](https://redis.io/download) repozytorium Redis w GitHub. Ta wersja implementuje podstawową `-c` obsługę po uruchomieniu z przełącznikiem. Aby uzyskać więcej informacji, zobacz [https://redis.io](https://redis.io) [Odtwarzanie z klastrem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) w [samouczku klastra Redis](https://redis.io/topics/cluster-tutorial).

W przypadku innych niż TLS należy użyć następujących poleceń.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

W przypadku protokołu `1300N` `1500N`TLS należy zastąpić .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Czy można skonfigurować klastrowanie dla wcześniej utworzonej pamięci podręcznej?
Tak. Najpierw upewnij się, że pamięć podręczna jest premium, skalowanie, jeśli nie jest. Następnie powinny być widoczne opcje konfiguracji klastra, w tym opcja włączenia klastra. Rozmiar klastra można zmienić po utworzeniu pamięci podręcznej lub po włączeniu klastrowania po raz pierwszy.

   >[!IMPORTANT]
   >Nie można cofnąć włączania klastrowania. A pamięć podręczna z włączoną klastrem i tylko jeden fragment zachowuje się *inaczej* niż pamięć podręczna o tym samym rozmiarze *bez* klastrowania.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Czy można skonfigurować klastrowanie dla podstawowej lub standardowej pamięci podręcznej?
Klastrowanie jest dostępne tylko dla pamięci podręcznych premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Czy można używać klastrowania z dostawcami buforowania ASP.NET redis i wyjściowych?
* **Dostawca pamięci podręcznej wyjścia Redis** — nie są wymagane żadne zmiany.
* **Dostawca stanu sesji Redis** — aby używać klastrowania, należy użyć [redisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub nowszego lub zostanie zgłoszony wyjątek. Jest to przełomowa zmiana; Aby uzyskać więcej informacji, zobacz [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Oprowadzam wyjątki MOVE podczas korzystania z StackExchange.Redis i klastrowania, co należy zrobić?
Jeśli używasz StackExchange.Redis `MOVE` i otrzymywać wyjątki podczas korzystania z klastrowania, upewnij się, że używasz [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) lub nowsze. Aby uzyskać instrukcje dotyczące konfigurowania aplikacji platformy .NET do używania stackexchange.Redis, zobacz [Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak korzystać z większej liczby funkcji pamięci podręcznej premium.

* [Wprowadzenie do warstwy Azure Cache for Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
