---
title: Przykłady pamięci podręcznej Redis Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi pamięć podręczna Redis Azure
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 0b6f89807d3252b750bd5208a7f758a06c9903d6
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909700"
---
# <a name="azure-redis-cache-samples"></a>Przykłady pamięci podręcznej Redis Azure
Ten temat zawiera listę próbek pamięć podręczna Redis Azure, obejmujące scenariuszy, takich jak nawiązywanie połączeń z pamięci podręcznej, odczytywania i zapisywania danych do i z pamięci podręcznej i korzystanie z dostawców usługi pamięć podręczna Redis ASP.NET. Niektóre przykłady są projekty do pobrania, a niektóre zawierają wskazówki krok po kroku i zawierać fragmentów kodu, ale nie są połączone z projektem do pobrania.

## <a name="hello-world-samples"></a>Witaj świecie próbek
Przykłady w tej sekcji wyświetlić podstawowe informacje dotyczące nawiązywania połączenia z wystąpieniem usługi pamięć podręczna Redis Azure i odczytywania i zapisywania danych do pamięci podręcznej przy użyciu różnych języków i Redis klientów.

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) przykładowych pokazano, jak wykonywać różne operacje pamięci podręcznej, za pomocą [programie StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) klienta .NET.

W tym przykładzie pokazano sposób:

* Użyj różnych opcji połączenia
* Odczytywanie i zapisywanie obiektów do i z pamięci podręcznej, za pomocą operacje synchroniczne i asynchroniczne
* Użyj poleceń Redis MGET/MSET, aby zwrócić wartości określone klucze
* Wykonywanie operacji transakcyjnych z pamięci podręcznej Redis
* Praca z listy Redis i posortowane zestawów
* Przechowywania obiektów platformy .NET przy użyciu serializatorów JsonConvert
* Użyj zbiorów Redis do zaimplementowania znakowanie
* Praca z pamięci podręcznej Redis klastra

Aby uzyskać więcej informacji, zobacz [programie StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) zobacz dokumentację w witrynie github, a dla scenariuszy użycia [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) testów jednostkowych.

[Jak używać usługi pamięć podręczna Redis Azure z języka Python](cache-python-get-started.md) pokazano, jak rozpocząć pracę z pamięci podręcznej Redis Azure za pomocą języka Python oraz [redis py](https://github.com/andymccurdy/redis-py) klienta.

[Praca z obiektami .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) przedstawiono jeden ze sposobów serializacji obiektów platformy .NET, co umożliwia zapisanie ich do oraz odczytywać wystąpienia pamięci podręcznej Redis Azure. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Używanie pamięci podręcznej Redis jako płyty montażowej skalowania SignalR platformy ASP.NET
[Użycia pamięci podręcznej Redis jako skalowania płyty montażowej dla produktu ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) przykładzie pokazano, jak skorzystać z pamięci podręcznej Redis Azure jako płyty montażowej SignalR. Aby uzyskać więcej informacji na temat płyty montażowej, zobacz [skalowania SignalR z pamięci podręcznej Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Przykładowe zapytania klienta pamięci podręcznej redis
W tym przykładzie przedstawiono porównanie wydajności między dostęp do danych z pamięci podręcznej i uzyskiwanie dostępu do danych z magazynu trwałości. Ten przykład ma dwa projekty.

* [Pokaz, jak poprawić wydajność przez buforowanie danych pamięci podręcznej Redis](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Inicjatora bazy danych i pamięci podręcznej dla pokaz](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Stan sesji programu ASP.NET i buforowanie danych wyjściowych
[Użycia pamięci podręcznej Redis Azure do przechowywania ASP.NET SessionState i OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) przykładzie pokazano sposób użycia pamięci podręcznej Redis Azure do przechowywania sesji programu ASP.NET i pamięci podręcznej danych wyjściowych za pomocą dostawcy SessionState i OutputCache dla pamięci podręcznej Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Zarządzanie z MAML pamięć podręczna Azure Redis
[Zarządzania pamięcią podręczną Redis Azure za pomocą biblioteki zarządzania Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) przykładzie pokazano, jak można użyć biblioteki zarządzania platformy Azure do zarządzania — (Utwórz / zaktualizuj / delete) pamięci podręcznej. 

## <a name="custom-monitoring-sample"></a>Niestandardowa monitorowania próbki
[Dostępu Redis monitorowanie pamięci podręcznej danych](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) przykładzie pokazano, jak można pobrać danych monitorowania dla pamięć podręczna Redis Azure poza portalem Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Klon stylu serwisu Twitter napisane przy użyciu języka PHP i pamięci podręcznej Redis
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) próbka jest Redis Witaj świecie. Jest minimalnym klonowania sieci społecznościowych stylu serwisu Twitter napisane przy użyciu pamięci podręcznej Redis i PHP za pomocą [Predis](https://github.com/nrk/predis) klienta. Kod źródłowy jest przeznaczona do jest bardzo proste i w tym samym czasie, aby wyświetlić różne Redis struktury danych.

## <a name="bandwidth-monitor"></a>Monitor przepustowości
[Monitor przepustowości](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) próbki pozwala na monitorowanie przepustowość na kliencie. Pomiar przepustowości Uruchom próbkę na komputerze klienckim pamięci podręcznej, wykonywania wywołań do pamięci podręcznej i obserwować przepustowości zgłoszone przez próbki monitor przepustowości.

