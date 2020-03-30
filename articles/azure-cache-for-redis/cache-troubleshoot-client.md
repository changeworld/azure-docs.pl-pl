---
title: Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta
description: Dowiedz się, jak rozwiązać typowe problemy po stronie klienta z usługą Azure Cache for Redis, takie jak ciśnienie pamięci klienta Redis, seria ruchu, wysoki procesor, ograniczona przepustowość, duże żądania lub duży rozmiar odpowiedzi.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277949"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta

W tej sekcji omówiono rozwiązywanie problemów, które występują z powodu warunku na kliencie Redis, którego używa aplikacja.

- [Nacisk pamięci na klienta Redis](#memory-pressure-on-redis-client)
- [Ruch pęknie](#traffic-burst)
- [Wysokie zużycie procesora klienta](#high-client-cpu-usage)
- [Ograniczenie przepustowości po stronie klienta](#client-side-bandwidth-limitation)
- [Duży rozmiar żądania lub odpowiedzi](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Nacisk pamięci na klienta Redis

Ciśnienie pamięci na komputerze klienckim prowadzi do wszelkiego rodzaju problemów z wydajnością, które mogą opóźnić przetwarzanie odpowiedzi z pamięci podręcznej. Po naciśnięciu ciśnienia pamięci system może przesyłać dane na dysk. Błąd _tej strony_ powoduje znaczne spowolnienie systemu.

Aby wykryć nacisk pamięci na klienta:

- Monitoruj użycie pamięci na komputerze, aby upewnić się, że nie przekracza dostępnej pamięci.
- Monitoruj licznik `Page Faults/Sec` wydajności klienta. Podczas normalnej pracy większość systemów ma pewne błędy strony. Skoki błędów strony odpowiadające limitom czasu żądania mogą wskazywać na ciśnienie pamięci.

Wysokie ciśnienie pamięci na kliencie można złagodzić na kilka sposobów:

- Zagłębij się w wzorce użycia pamięci, aby zmniejszyć zużycie pamięci na kliencie.
- Uaktualnij maszynę wirtualną klienta do większego rozmiaru z większą pamięcią.

## <a name="traffic-burst"></a>Ruch pęknie

Wybuchy ruchu w `ThreadPool` połączeniu ze słabymi ustawieniami mogą spowodować opóźnienia w przetwarzaniu danych już wysłanych przez serwer Redis, ale jeszcze nie wykorzystanych po stronie klienta.

Monitoruj, `ThreadPool` jak statystyki zmieniają się w czasie, korzystając [z przykładu `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Możesz użyć `TimeoutException` wiadomości z StackExchange.Redis jak poniżej do dalszego zbadania:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

W poprzednim wyjątku istnieje kilka kwestii, które są interesujące:

- Należy zauważyć, `IOCP` że `WORKER` w sekcji `Busy` i sekcji masz `Min` wartość, która jest większa niż wartość. Ta różnica `ThreadPool` oznacza, że ustawienia wymagają dostosowania.
- Można również `in: 64221`zobaczyć . Ta wartość wskazuje, że 64 211 bajtów zostało odebranych w warstwie gniazda jądra klienta, ale nie zostały odczytane przez aplikację. Ta różnica zazwyczaj oznacza, że aplikacja (na przykład StackExchange.Redis) nie odczytuje danych z sieci tak szybko, jak serwer wysyła je do Ciebie.

Można [skonfigurować `ThreadPool` ustawienia,](cache-faq.md#important-details-about-threadpool-growth) aby upewnić się, że pula wątków skaluje się szybko w scenariuszach serii.

## <a name="high-client-cpu-usage"></a>Wysokie zużycie procesora klienta

Wysokie użycie procesora CPU klienta wskazuje, że system nie może nadążyć za pracą, o które został poproszony. Mimo że pamięć podręczna wysłała odpowiedź szybko, klient może nie przetworzyć odpowiedzi w odpowiednim czasie.

Monitorowanie użycia procesora CPU w całym systemie klienta przy użyciu metryk dostępnych w witrynie Azure portal lub za pomocą liczników wydajności na komputerze. Należy uważać, aby nie monitorować procesora CPU *procesu,* ponieważ pojedynczy proces może mieć niskie użycie procesora, ale procesor w całym systemie może być wysoki. Uważaj na skoki użycia procesora CPU, które odpowiadają limitom czasu. Wysoki procesor CPU `in: XXX` może `TimeoutException` również powodować wysokie wartości w komunikatach o błędach, zgodnie z opisem w [sekcji Seria ruchu.](#traffic-burst)

> [!NOTE]
> StackExchange.Redis 1.1.603 i `local-cpu` później zawiera `TimeoutException` metryki w komunikatach o błędach. Upewnij się, że używasz najnowszej wersji [pakietu StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Istnieją błędy stale stałe są naprawiane w kodzie, aby uczynić go bardziej niezawodnym dla limitów czasu, więc posiadanie najnowszej wersji jest ważne.
>

Aby ograniczyć wysokie użycie procesora CPU przez klienta:

- Sprawdź, co jest przyczyną skoków procesora CPU.
- Uaktualnij klienta do większego rozmiaru maszyny Wirtualnej z większą pojemnością procesora CPU.

## <a name="client-side-bandwidth-limitation"></a>Ograniczenie przepustowości po stronie klienta

W zależności od architektury komputerów klienckich mogą one mieć ograniczenia dotyczące przepustowości sieci, którą mają do dyspozycji. Jeśli klient przekracza dostępną przepustowość przez przeciążenie pojemności sieci, dane nie są przetwarzane po stronie klienta tak szybko, jak serwer wysyła go. Ta sytuacja może prowadzić do przesuń limitu czasu.

Monitoruj, jak z czasem zmienia się użycie przepustowości, korzystając [z przykładu `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ten kod może nie działać pomyślnie w niektórych środowiskach z ograniczonymi uprawnieniami (takich jak witryny sieci Web platformy Azure).

Aby ograniczyć, zmniejszyć zużycie przepustowości sieci lub zwiększyć rozmiar maszyny Wirtualnej klienta do jednego z większą pojemnością sieci.

## <a name="large-request-or-response-size"></a>Duży rozmiar żądania lub odpowiedzi

Duże żądanie/odpowiedź może spowodować przesuw czasu. Na przykład załóżmy, że wartość limitu czasu skonfigurowana na kliencie wynosi 1 sekundę. Aplikacja żąda dwóch kluczy (na przykład "A" i "B") w tym samym czasie (przy użyciu tego samego fizycznego połączenia sieciowego). Większość klientów obsługuje żądania "pipelining", gdzie oba żądania "A" i "B" są wysyłane jeden po drugim, nie czekając na ich odpowiedzi. Serwer wysyła odpowiedzi z powrotem w tej samej kolejności. Jeśli odpowiedź "A" jest duża, może zjeść większość limitu czasu dla późniejszych żądań.

W poniższym przykładzie żądania "A" i "B" są wysyłane szybko do serwera. Serwer szybko rozpoczyna wysyłanie odpowiedzi "A" i "B". Ze względu na czas transferu danych odpowiedź "B" musi czekać za odpowiedzią "A" czas przesunie, mimo że serwer szybko zareagował.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

To żądanie/odpowiedź jest trudne do zmierzenia. Można instrument kodu klienta do śledzenia dużych żądań i odpowiedzi.

Rozwiązania dla dużych rozmiarów odpowiedzi są zróżnicowane, ale obejmują:

1. Zoptymalizuj aplikację pod kątem dużej liczby małych wartości, a nie kilku dużych wartości.
    - Preferowanym rozwiązaniem jest podział danych na powiązane mniejsze wartości.
    - Zobacz post [Jaki jest idealny zakres wielkości wartości dla redis? Czy 100 KB jest za duże?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) szczegółowe informacje o tym, dlaczego zalecane są mniejsze wartości.
1. Zwiększ rozmiar maszyny Wirtualnej, aby uzyskać większe możliwości przepustowości
    - Większa przepustowość maszyny Wirtualnej klienta lub serwera może skrócić czas przesyłania danych w przypadku większych odpowiedzi.
    - Porównaj bieżące użycie sieci na obu komputerach z limitami bieżącego rozmiaru maszyny Wirtualnej. Większa przepustowość tylko na serwerze lub tylko na kliencie może nie wystarczyć.
1. Zwiększ liczbę obiektów połączeń używanych przez aplikację.
    - Użyj podejścia okrężnego, aby tworzyć żądania za pomocą różnych obiektów połączenia.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera](cache-troubleshoot-server.md)
- [Jak mogę sprawdzić i przetestować wydajność mojej pamięci podręcznej?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
