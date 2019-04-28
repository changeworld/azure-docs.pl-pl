---
title: Pamięć podręczna systemu Azure dla przykładów pamięci podręcznej Redis | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Cache for Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: multiple
ms.topic: article
origin.date: 01/23/2017
ms.date: 02/27/2019
ms.author: v-junlch
ms.openlocfilehash: 73c771ab18d1cc2944298818c1cab90eb2f277ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829807"
---
# <a name="azure-cache-for-redis-samples"></a>Przykłady usługi Azure Cache for Redis
Ten temat zawiera listę przykładów pamięci podręcznej Redis, obejmujące scenariusze, takie jak łączenie z pamięci podręcznej, odczytywania i zapisywania danych do i z pamięci podręcznej i za pomocą usługi Azure Cache ASP.NET dla dostawców usługi Redis pamięć podręczna systemu Azure. Niektóre przykłady to projekty do pobrania, a niektóre zapewniają wskazówki krok po kroku i umieszczania fragmentów kodu, ale nie należy przeprowadzać konsolidacji z projektem do pobrania.

## <a name="hello-world-samples"></a>Witaj świecie przykłady
Przykłady w tej sekcji pokazują podstawowe informacje dotyczące nawiązywania połączenia z usługi Azure Cache dla wystąpienia usługi Redis i odczytywania i zapisywania danych w pamięci podręcznej przy użyciu różnych języków i klienci Redis.

[Witaj, świecie](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) przykładowych pokazano, jak wykonywać różne operacje pamięci podręcznej przy użyciu [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) klienta platformy .NET.

Ten przykład pokazuje, jak:

- Użyj różnych opcji połączenia
- Odczytywanie i zapisywanie obiektów do i z pamięci podręcznej, za pomocą operacje synchroniczne i asynchroniczne
- Użyj polecenia Redis MGET/MSET, aby zwrócić wartości kluczy określony
- Wykonaj operacje transakcyjne w pamięci podręcznej Redis
- Praca z pamięcią podręczną Redis zawiera listę i zestawy posortowane
- Store obiektów platformy .NET przy użyciu JsonConvert serializatorów
- Implementowanie znakowanie przy użyciu zestawów pamięci podręcznej Redis
- Praca z klastra pamięci podręcznej Redis

Aby uzyskać więcej informacji, zobacz [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) zobacz dokumentację w witrynie GitHub i więcej scenariuszy użycia [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) testów jednostkowych.

[Jak używać usługi Azure Cache dla pamięci podręcznej Redis przy użyciu języka Python](cache-python-get-started.md) pokazuje, jak rozpocząć pracę z usługą Azure Cache dla pamięci podręcznej Redis przy użyciu języka Python i [redis-py](https://github.com/andymccurdy/redis-py) klienta.

[Praca z obiektami platformy .NET w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) przedstawiono sposób wykonywania serializacji obiektów platformy .NET, dzięki czemu mogą być zapisanie ich do i odczytywać je z usługi Azure Cache dla wystąpienia usługi Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Na użytek pamięć podręczna systemu Azure dla usługi Redis jako płyty montażowej skalowania SignalR platformy ASP.NET
[Używać usługi Azure Cache dla usługi Redis jako płyty montażowej skalowania dla programu ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) w przykładzie pokazano, jak używać usługi Azure Cache dla usługi Redis jako płyty montażowej SignalR. Aby uzyskać więcej informacji na temat systemu backplane zobacz [SignalR — skalowanie w poziomie przy użyciu usługi Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Pamięć podręczna systemu Azure dla przykładowych zapytań klienta pamięci podręcznej Redis
Niniejszy przykład pokazuje porównuje wydajności między uzyskiwanie dostępu do danych z pamięci podręcznej i uzyskiwanie dostępu do danych z magazynu stanu trwałego. Ten przykład ma dwa projekty.

- [Pokaz, jak poprawić wydajność, buforując dane pamięci podręcznej Redis Azure](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
- [Inicjator bazy danych i pamięć podręczna swój udział w pokazie](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Stan sesji programu ASP.NET oraz buforowania danych wyjściowych
[Używać usługi Azure Cache dla usługi Redis do przechowywania ASP.NET SessionState i OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) przykład pokazuje, jak przy użyciu pamięci podręcznej Redis Azure do przechowywania sesja programu ASP.NET i wyjściowej pamięci podręcznej dla pamięci podręcznej Redis przy użyciu dostawcy SessionState i OutputCache .

## <a name="manage-azure-cache-for-redis-with-maml"></a>Zarządzanie pamięć podręczna systemu Azure dla usługi Redis z MAML
[Zarządzana pamięć podręczna Azure dla pamięci podręcznej Redis przy użyciu bibliotek zarządzania platformy Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) w przykładzie pokazano, jak można użyć bibliotek zarządzania platformy Azure do zarządzania — (Tworzenie / aktualizowanie / usuwanie) pamięci podręcznej. 

## <a name="custom-monitoring-sample"></a>Niestandardowe monitorowanie próbki
[Dostępu do usługi Azure Cache danych Redis monitorowania](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) w przykładzie pokazano, jak dostęp danych monitorowania dla pamięci podręcznej Azure dla usługi Redis poza witryny Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Klonuj stylu serwisu Twitter, napisane przy użyciu języka PHP i pamięci podręcznej Redis
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) przykładowe dane stanowią Redis Hello World. Jest napisane przy użyciu usługi Redis i PHP minimalny klonowania sieci społecznościowej stylu serwisu Twitter przy użyciu [Predis](https://github.com/nrk/predis) klienta. Kod źródłowy jest przeznaczony do być bardzo proste, a w tym samym czasie, aby wyświetlić różne Redis struktur danych.

## <a name="bandwidth-monitor"></a>Monitor przepustowości
[Monitor przepustowości](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) próbki pozwala na monitorowanie przepustowość używana przez klienta. W celu mierzenia przepustowości, uruchom przykład na komputerze klienckim pamięci podręcznej, wykonywanie wywołań do pamięci podręcznej i obserwować przepustowości zgłoszone przez przykład monitor przepustowości.

<!-- Update_Description: update metedata properties -->

