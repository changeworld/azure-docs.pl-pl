---
title: Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera
description: Dowiedz się, jak rozwiązać typowe problemy po stronie serwera z usługą Azure Cache for Redis, takie jak ciśnienie pamięci, wysoki procesor, długotrwałe polecenia lub ograniczenia przepustowości.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277936"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera

W tej sekcji omówiono rozwiązywanie problemów, które występują z powodu warunku w pamięci podręcznej platformy Azure dla programu Redis lub maszyny wirtualnej obsługującej go.

- [Wykorzystanie pamięci na serwerze Redis](#memory-pressure-on-redis-server)
- [Wysokie zużycie procesora lub obciążenie serwera](#high-cpu-usage-or-server-load)
- [Długotrwałe polecenia](#long-running-commands)
- [Ograniczenie przepustowości po stronie serwera](#server-side-bandwidth-limitation)

> [!NOTE]
> Kilka kroków rozwiązywania problemów w tym przewodniku zawiera instrukcje uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcji, zobacz artykuły w sekcji [Dodatkowe informacje.](#additional-information)
>

## <a name="memory-pressure-on-redis-server"></a>Wykorzystanie pamięci na serwerze Redis

Ciśnienie pamięci po stronie serwera prowadzi do wszelkiego rodzaju problemów z wydajnością, które mogą opóźniać przetwarzanie żądań. Po naciśnięciu ciśnienia pamięci system może przesyłać dane na dysk. Błąd _tej strony_ powoduje znaczne spowolnienie systemu. Istnieje kilka możliwych przyczyn tego ciśnienia pamięci:

- Pamięć podręczna jest wypełniona danymi w pobliżu maksymalnej pojemności.
- Redis widzi fragmentację pamięci o wysokiej jakości. To fragmentacja jest najczęściej spowodowane przechowywaniem dużych obiektów, ponieważ Redis jest zoptymalizowany pod kątem małych obiektów.

Redis udostępnia dwa statystyki za pomocą polecenia [INFO,](https://redis.io/commands/info) które mogą pomóc w zidentyfikowaniu tego problemu: "used_memory" i "used_memory_rss". Te [metryki](cache-how-to-monitor.md#view-metrics-with-azure-monitor) można wyświetlić za pomocą portalu.

Istnieje kilka możliwych zmian, które można wprowadzić, aby zachować zdrowe użycie pamięci:

- [Skonfiguruj zasady pamięci](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) i ustaw czas wygaśnięcia kluczy. Ta zasada może nie być wystarczająca, jeśli masz fragmentacji.
- [Skonfiguruj wartość zarezerwowaną maxmemory,](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) która jest wystarczająco duża, aby skompensować fragmentację pamięci.
- Podziel duże obiekty w pamięci podręcznej na mniejsze powiązane obiekty.
- [Tworzenie alertów](cache-how-to-monitor.md#alerts) na metryki, takie jak używane pamięci, aby otrzymywać powiadomienia na początku o potencjalnych skutków.
- [Skalowanie](cache-how-to-scale.md) do większego rozmiaru pamięci podręcznej z większą pojemnością pamięci.

## <a name="high-cpu-usage-or-server-load"></a>Wysokie zużycie procesora lub obciążenie serwera

Wysokie obciążenie serwera lub użycie procesora CPU oznacza, że serwer nie może przetwarzać żądań w odpowiednim czasie. Serwer może być powolny, aby odpowiedzieć i nie może nadążyć za szybkością żądania.

[Monitorowanie metryk,](cache-how-to-monitor.md#view-metrics-with-azure-monitor) takich jak obciążenie procesora lub serwera. Uważaj na skoki użycia procesora CPU, które odpowiadają limitom czasu.

Istnieje kilka zmian, które można wprowadzić w celu ograniczenia wysokiego obciążenia serwera:

- Sprawdź, co jest przyczyną skoków procesora, takich jak [długotrwałe polecenia](#long-running-commands) wymienione poniżej lub błędów strony z powodu wysokiego ciśnienia pamięci.
- [Tworzenie alertów](cache-how-to-monitor.md#alerts) dotyczących metryk, takich jak obciążenie procesora CPU lub serwera, aby otrzymywać powiadomienia o potencjalnych skutkach.
- [Skalowanie](cache-how-to-scale.md) do większego rozmiaru pamięci podręcznej z większą pojemnością procesora CPU.

## <a name="long-running-commands"></a>Długotrwałe polecenia

Niektóre polecenia Redis są droższe do wykonania niż inne. [Dokumentacja poleceń Redis](https://redis.io/commands) pokazuje złożoność czasu każdego polecenia. Ponieważ przetwarzanie polecenia Redis jest jednowątkowe, polecenie, które wymaga czasu, aby uruchomić, zablokuje wszystkie inne, które przychodzą po nim. Należy przejrzeć polecenia, które są wystawiane na serwerze Redis, aby zrozumieć ich wpływ na wydajność. Na przykład polecenie [KEYS](https://redis.io/commands/keys) jest często używane bez wiedzy, że jest to operacja O(N). Można uniknąć kluczy za pomocą [skanowania,](https://redis.io/commands/scan) aby zmniejszyć skoki procesora.

Za pomocą polecenia [SLOWLOG](https://redis.io/commands/slowlog) można zmierzyć kosztowne polecenia wykonywane na serwerze.

## <a name="server-side-bandwidth-limitation"></a>Ograniczenie przepustowości po stronie serwera

Różne rozmiary pamięci podręcznej mają różne możliwości przepustowości sieci. Jeśli serwer przekroczy dostępną przepustowość, dane nie zostaną wysłane do klienta tak szybko. Żądania klientów może limit czasu, ponieważ serwer nie może wypchnąć danych do klienta wystarczająco szybko.

Metryki "Odczyt pamięci podręcznej" i "Zapis pamięci podręcznej" mogą być używane do wykrywania, ile przepustowości po stronie serwera jest używane. Te [metryki](cache-how-to-monitor.md#view-metrics-with-azure-monitor) można wyświetlić w portalu.

Aby ograniczyć sytuacje, w których użycie przepustowości sieci jest bliskie maksymalnej pojemności:

- Zmień zachowanie wywołania klienta, aby zmniejszyć zapotrzebowanie sieciowe.
- [Tworzenie alertów](cache-how-to-monitor.md#alerts) na metryki, takie jak odczyt pamięci podręcznej lub cache zapisu, aby otrzymywać powiadomienia na początku o potencjalnych skutków.
- [Skalowanie](cache-how-to-scale.md) do większego rozmiaru pamięci podręcznej z większą przepustowością sieci.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta](cache-troubleshoot-client.md)
- [Jakiej oferty i rozmiaru usługi Azure Cache for Redis należy używać?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Jak mogę sprawdzić i przetestować wydajność mojej pamięci podręcznej?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak monitorować pamięć podręczną platformy Azure dla redis](cache-how-to-monitor.md)
- [Jak uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)
