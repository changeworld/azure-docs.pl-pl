---
title: Przykłady usługi Azure Cache for Redis
description: 'Dowiedz się, jak używać usługi Azure Cache for Redis za pomocą tych przykładów kodu: łączenia się z pamięcią podręczną, odczytywania i zapisywania danych w pamięci podręcznej ASP.NET usługi Azure Cache dla dostawców redis.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433405"
---
# <a name="azure-cache-for-redis-samples"></a>Przykłady usługi Azure Cache for Redis
Ten temat zawiera listę przykładów usługi Azure Cache for Redis, obejmujące scenariusze, takie jak łączenie się z pamięcią podręczną, odczytywanie i zapisywanie danych do i z pamięci podręcznej oraz przy użyciu ASP.NET usługi Azure Cache dla dostawców Redis. Niektóre przykłady są projekty do pobrania, a niektóre zawierają wskazówki krok po kroku i zawierają fragmenty kodu, ale nie łączą się z projektem do pobrania.

## <a name="hello-world-samples"></a>Witaj próbki świata
Przykłady w tej sekcji pokazują podstawy łączenia się z pamięci podręcznej platformy Azure dla wystąpienia Redis i odczytywania i zapisywania danych w pamięci podręcznej przy użyciu różnych języków i klientów Redis.

W przykładzie [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) pokazano, jak wykonywać różne operacje pamięci podręcznej przy użyciu [stackexchange.redis](https://github.com/StackExchange/StackExchange.Redis) .NET klienta.

W tym przykładzie pokazano, jak:

* Korzystanie z różnych opcji połączenia
* Odczytywanie i zapisywanie obiektów do i z pamięci podręcznej przy użyciu operacji synchroniowych i asynchronicznych
* Polecenia Redis MGET/MSET za pomocą poleceń Redis MGET/MSET zwracają wartości określonych kluczy
* Wykonywanie operacji transakcyjnych Redis
* Praca z listami Redis i zestawami posortowanymi
* Przechowywanie obiektów .NET przy użyciu serializatorów JsonConvert
* Implementowanie tagowania za pomocą zestawów Redis
* Praca z klastrem Redis

Aby uzyskać więcej informacji, zobacz [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) dokumentacji w usłudze GitHub i więcej scenariuszy użycia zobacz [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) testów jednostkowych.

[Jak używać usługi Azure Cache for Redis z pythonem](cache-python-get-started.md) pokazano, jak rozpocząć pracę z usługą Azure Cache for Redis przy użyciu języka Python i klienta [redis-py.](https://github.com/andymccurdy/redis-py)

[Praca z obiektami .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) pokazuje jeden ze sposobów serializacji obiektów .NET, dzięki czemu można je zapisać i odczytać je z pamięci podręcznej platformy Azure dla wystąpienia Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Używanie pamięci podręcznej Azure Cache for Redis jako warstwy montażowej skalowania w poziomie dla ASP.NET SignalR
[Użyj pamięci podręcznej azure dla redis jako skalować w poziomie backplane dla ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) przykład pokazuje, jak można używać usługi Azure Cache for Redis jako backplane SignalR. Aby uzyskać więcej informacji na temat zaokrążenia, zobacz [SignalR Scaleout with Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Przykład zapytania klienta usługi Azure Cache for Redis
W tym przykładzie pokazano porównuje wydajność między uzyskiwania dostępu do danych z pamięci podręcznej i uzyskiwania dostępu do danych z magazynu trwałości. Ten przykład ma dwa projekty.

* [Demonstracja, w jaki sposób usługa Azure Cache for Redis może zwiększyć wydajność przez buforowanie danych](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Seed bazy danych i pamięci podręcznej dla demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET buforowanie stanu sesji i wyjścia
[Użyj usługi Azure Cache for Redis do przechowywania ASP.NET SessionState i OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) przykład pokazuje, jak używać pamięci podręcznej Azure cache dla redis do przechowywania ASP.NET sesji i pamięci podręcznej wyjściowej przy użyciu sessionstate i outputcache dostawców dla redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Zarządzanie pamięcią podręczną platformy Azure dla redis za pomocą usługi MAML
Przykład [zarządzania pamięcią podręczną platformy Azure dla programu Redis przy użyciu bibliotek zarządzania platformy Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) pokazuje, jak można używać bibliotek zarządzania platformy Azure do zarządzania — (Tworzenie/ aktualizowanie/ usuwanie) pamięci podręcznej. 

## <a name="custom-monitoring-sample"></a>Próbka monitorowania niestandardowego
Przykład [danych usługi Access Azure Cache for Redis Monitoring](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) pokazuje, jak można uzyskać dostęp do danych monitorowania pamięci podręcznej platformy Azure dla programu Redis poza witryną Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Klon w stylu Twittera napisany za pomocą PHP i Redis
Próbka [Retwis](https://github.com/SyntaxC4-MSFT/retwis) to Redis Hello World. Jest to minimalny klon sieci społecznościowej w stylu Twittera napisany przy użyciu Redis i PHP za pomocą klienta [Prdis.](https://github.com/nrk/predis) Kod źródłowy ma być bardzo prosty i jednocześnie wyświetlał różne struktury danych Redis.

## <a name="bandwidth-monitor"></a>Monitor przepustowości
Przykład [monitora przepustowości](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) umożliwia monitorowanie przepustowości używanej na kliencie. Aby zmierzyć przepustowość, uruchom próbkę na komputerze klienckim pamięci podręcznej, nawiązuj połączenia z pamięcią podręczną i obserwuj przepustowość zgłaszaną przez przykład monitora przepustowości.
