---
title: Azure Cache for Redis — często zadawane pytania
description: Poznaj odpowiedzi na typowe pytania, wzorce i najlepsze rozwiązania dotyczące usługi Azure Cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 6ba292850c057284fff265c8a77386d21374942a
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010226"
---
# <a name="azure-cache-for-redis-faq"></a>Azure Cache for Redis — często zadawane pytania
Poznaj odpowiedzi na typowe pytania, wzorce i najlepsze rozwiązania dotyczące usługi Azure Cache for Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli nie ma tutaj odpowiedzi na moje pytanie?
Jeśli twojego pytania nie ma na liście, poinformuj nas o tym, a my pomożemy Ci znaleźć odpowiedź.

* Możesz opublikować pytanie w komentarzach na końcu tego często zadawanych pytań i nawiązać kontakt z zespołem usługi Azure Cache i innymi członkami społeczności na temat tego artykułu.
* Aby dotrzeć do szerszego grona odbiorców, możesz zadać pytanie na [forum usługi Azure Cache MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) i nawiązać kontakt z zespołem usługi Azure Cache i innymi członkami społeczności.
* Jeśli chcesz złożyć żądanie funkcji, możesz przesłać swoje żądania i pomysły do [usługi Azure Cache for Redis User Voice](https://feedback.azure.com/forums/169382-cache).
* Możesz również wysłać do nas wiadomość e-mail na [adres Azure Cache External Feedback](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Podstawowe informacje o pamięci podręcznej platformy Azure dla redis
Często zadawane pytania w tej sekcji obejmują niektóre z podstaw usługi Azure Cache for Redis.

* [Co to jest usługa Azure Cache for Redis?](#what-is-azure-cache-for-redis)
* [Jak rozpocząć pracę z usługą Azure Cache for Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Poniższe często zadawane pytania obejmują podstawowe pojęcia i pytania dotyczące usługi Azure Cache for Redis i są odpowiedzi w jednej z innych sekcji często zadawanych pytań.

* [Jakiej oferty i rozmiaru usługi Azure Cache for Redis należy używać?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Jakiej usługi Azure Cache dla klientów Redis można użyć?](#what-azure-cache-for-redis-clients-can-i-use)
* [Czy istnieje lokalny emulator dla usługi Azure Cache for Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak monitorować kondycję i wydajność pamięci podręcznej?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planowanie często zadawanych pytań
* [Jakiej oferty i rozmiaru usługi Azure Cache for Redis należy używać?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Usługa Azure Cache dla wydajności Redis](#azure-cache-for-redis-performance)
* [W jakim regionie należy zlokalizować pamięć podręczną?](#in-what-region-should-i-locate-my-cache)
* [Jak rozliczane są płatności za usługę Azure Cache for Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Czy mogę używać usługi Azure Cache for Redis za pomocą usługi Azure Government Cloud, Azure China Cloud lub Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Często zadawane pytania dotyczące rozwoju
* [Do czego działają opcje konfiguracji StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jakiej usługi Azure Cache dla klientów Redis można użyć?](#what-azure-cache-for-redis-clients-can-i-use)
* [Czy istnieje lokalny emulator dla usługi Azure Cache for Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak uruchomić polecenia Redis?](#how-can-i-run-redis-commands)
* [Dlaczego usługa Azure Cache for Redis nie ma odwołania do biblioteki klasy MSDN, podobnie jak niektóre inne usługi platformy Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Czy mogę używać pamięci podręcznej usługi Azure Cache for Redis jako pamięci podręcznej sesji PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co to są bazy danych Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Często zadawane pytania dotyczące zabezpieczeń
* [Kiedy należy włączyć port nienawiązywania TLS/SSL do podłączenia do redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Często zadawane pytania dotyczące produkcji
* [Jakie są najlepsze praktyki produkcyjne?](#what-are-some-production-best-practices)
* [Jakie są niektóre z rozważań podczas korzystania z typowych poleceń Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak mogę sprawdzić i przetestować wydajność mojej pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Ważne szczegóły dotyczące rozwoju ThreadPool](#important-details-about-threadpool-growth)
* [Włącz gc serwera, aby uzyskać większą przepływność na kliencie podczas korzystania z StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Zagadnienia dotyczące wydajności wokół połączeń](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Często zadawane pytania dotyczące monitorowania i rozwiązywania problemów
Często zadawane pytania w tej sekcji obejmują typowe pytania dotyczące monitorowania i rozwiązywania problemów. Aby uzyskać więcej informacji na temat monitorowania i rozwiązywania problemów z pamięcią podręczną platformy Azure dla wystąpień Redis, zobacz [Jak monitorować pamięć podręczną Azure Cache for Redis](cache-how-to-monitor.md) i różne przewodniki rozwiązywania problemów.

* [Jak monitorować kondycję i wydajność pamięci podręcznej?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Dlaczego widzę limity czasu?](#why-am-i-seeing-timeouts)
* [Dlaczego mój klient został odłączony od pamięci podręcznej?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Wcześniejsze często zadawane pytania dotyczące pamięci podręcznej
* [Która oferta usługi Azure Cache jest dla mnie odpowiednia?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Co to jest usługa Azure Cache for Redis?
Usługa Azure Cache for Redis jest oparta na popularnym oprogramowaniu typu open source [Redis.](https://redis.io/) Zapewnia dostęp do bezpiecznej, dedykowanej pamięci podręcznej Azure dla firmy Redis, zarządzanej przez firmę Microsoft i dostępnej z dowolnej aplikacji na platformie Azure. Aby uzyskać bardziej szczegółowe omówienie, zobacz stronę produktu [Usługi Azure Cache for Redis](https://azure.microsoft.com/services/cache/) na Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak rozpocząć pracę z usługą Azure Cache for Redis?
Istnieje kilka sposobów rozpoczęcia pracy z usługą Azure Cache for Redis.

* Możesz sprawdzić jeden z naszych tutoriali dostępnych dla [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)i [Python](cache-python-get-started.md).
* Możesz obejrzeć [jak tworzyć aplikacje o wysokiej wydajności przy użyciu pamięci podręcznej Microsoft Azure dla redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Można sprawdzić dokumentację klienta dla klientów, które pasują do języka programowania projektu, aby zobaczyć, jak używać Redis. Istnieje wiele klientów Redis, które mogą być używane z pamięcią podręczną Azure dla Redis. Aby uzyskać listę klientów Redis, zobacz [https://redis.io/clients](https://redis.io/clients).

Jeśli nie masz jeszcze konta platformy Azure, możesz:

* [Utworzyć bezpłatne konto platformy Azure ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Otrzymasz środki, które możesz wykorzystać do wypróbowania płatnych usług Azure. Nawet po wyczerpaniu tych środków możesz zachować konto i korzystać z bezpłatnych usług i funkcji platformy Azure.
* [Aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Jakiej oferty i rozmiaru usługi Azure Cache for Redis należy używać?
Każda oferta usługi Azure Cache for Redis zapewnia różne poziomy **rozmiaru,** **przepustowości,** **wysokiej dostępności**i opcje **umowy SLA.**

Poniżej przedstawiono zagadnienia dotyczące wyboru oferty pamięci podręcznej.

* **Pamięć:** Warstwy Basic i Standard oferują 250 MB – 53 GB. Warstwa Premium oferuje do 1,2 TB (jako klaster) lub 120 GB (nieklastrowane). Aby uzyskać więcej informacji, zobacz [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Wydajność sieci:** Jeśli masz obciążenie, które wymaga wysokiej przepływności, warstwa Premium oferuje większą przepustowość w porównaniu do standardowego lub podstawowego. Również w każdej warstwie pamięci podręczne większy rozmiar mają większą przepustowość ze względu na podstawowej maszyny Wirtualnej, która obsługuje pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [poniższą tabelę](#cache-performance).
* **Przepływność:** Warstwa Premium oferuje maksymalną dostępną przepustowość. Jeśli serwer pamięci podręcznej lub klient osiągnie limity przepustowości, mogą pojawić się limity czasu po stronie klienta. Aby uzyskać więcej informacji, zobacz poniższą tabelę.
* **Wysoka dostępność/umowa SLA:** Usługa Azure Cache for Redis gwarantuje, że pamięć podręczna standard/Premium jest dostępna przez co najmniej 99,9% czasu. Aby dowiedzieć się więcej o naszej umowy SLA, zobacz [Azure Cache for Redis Pricing](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Umowy SLA obejmuje tylko łączność z punktami końcowymi pamięci podręcznej. Umowy SLA nie obejmuje ochrony przed utratą danych. Zalecamy użycie funkcji trwałości danych Redis w warstwie Premium, aby zwiększyć odporność na utratę danych.
* **Redis Data Persistence:** Warstwa Premium umożliwia utrwalanie danych pamięci podręcznej na koncie usługi Azure Storage. W podstawowej/standardowej pamięci podręcznej wszystkie dane są przechowywane tylko w pamięci. Podstawowe problemy z infrastrukturą może spowodować potencjalne utraty danych. Zalecamy użycie funkcji trwałości danych Redis w warstwie Premium, aby zwiększyć odporność na utratę danych. Usługa Azure Cache for Redis oferuje opcje RDB i AOF (wkrótce) w redis trwałość. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować trwałość dla pamięci podręcznej premium Azure dla redis.](cache-how-to-premium-persistence.md)
* **Redis Cluster:** Aby utworzyć pamięci podręczne większe niż 120 GB lub dane niezależnego fragmentu w wielu węzłach Redis, można użyć klastrowania Redis, który jest dostępny w warstwie Premium. Każdy węzeł składa się z pary pamięci podręcznej podstawowej/repliki dla wysokiej dostępności. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować klastrowanie dla pamięci podręcznej premium Azure dla programu Redis](cache-how-to-premium-clustering.md).
* **Zwiększone zabezpieczenia i izolacja sieci:** wdrożenie sieci wirtualnej platformy Azure (VNET) zapewnia zwiększone bezpieczeństwo i izolację pamięci podręcznej platformy Azure dla programu Redis, a także podsieci, zasad kontroli dostępu i innych funkcji w celu dalszego ograniczenia dostępu. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować obsługę sieci wirtualnej dla pamięci podręcznej premium Azure dla programu Redis](cache-how-to-premium-vnet.md).
* **Konfigurowanie redis:** Zarówno w warstwach Standardowy, jak i Premium można skonfigurować funkcje redis dla powiadomień o przestrzeni kluczy.
* **Maksymalna liczba połączeń klientów:** Warstwa Premium oferuje maksymalną liczbę klientów, którzy mogą łączyć się z redis, z większą liczbą połączeń dla większych pamięci podręcznych. Klastrowanie nie zwiększa liczby połączeń dostępnych dla klastrowanej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Azure Cache for Redis cennika](https://azure.microsoft.com/pricing/details/cache/).
* **Dedykowany rdzeń dla serwera Redis:** W warstwie Premium wszystkie rozmiary pamięci podręcznej mają dedykowany rdzeń dla Redis. W warstwach Podstawowe/Standardowe rozmiar C1 i powyżej mają dedykowany rdzeń dla serwera Redis.
* **Redis jest jednowątkowy,** więc posiadanie więcej niż dwóch rdzeni nie zapewnia dodatkowych korzyści w związku z posiadaniem tylko dwóch rdzeni, ale większe rozmiary maszyn wirtualnych zazwyczaj mają większą przepustowość niż mniejsze rozmiary. Jeśli serwer lub klient pamięci podręcznej osiągnie limity przepustowości, po stronie klienta są odbierane limity czasu.
* **Ulepszenia wydajności:** pamięci podręczne w warstwie Premium są wdrażane na sprzęcie, który ma szybsze procesory, co daje lepszą wydajność w porównaniu do warstwy Podstawowa lub Standardowa. Pamięci podręczne warstwy premium mają wyższą przepływność i mniejsze opóźnienia.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Usługa Azure Cache dla wydajności Redis
W poniższej tabeli przedstawiono maksymalne wartości przepustowości obserwowane `redis-benchmark.exe` podczas testowania różnych rozmiarów pamięci podręcznych standard i premium przy użyciu maszyny Wirtualnej IaaS względem pamięci podręcznej Azure Cache dla punktu końcowego Redis. W przypadku przepływności protokołu TLS wskaźnik porównawczy redis jest używany z stunnel, aby połączyć się z punktem końcowym usługi Azure Cache for Redis.

>[!NOTE] 
>Te wartości nie są gwarantowane i nie ma umowy SLA dla tych liczb, ale powinny być typowe. Należy załadować test własnej aplikacji, aby określić odpowiedni rozmiar pamięci podręcznej dla aplikacji.
>Liczby te mogą ulec zmianie w miarę okresowego publikowania nowszych wyników.
>

Z tej tabeli możemy wyciągnąć następujące wnioski:

* Przepływność dla pamięci podręcznych, które mają ten sam rozmiar jest wyższa w warstwie Premium w porównaniu do warstwy standardowej. Na przykład w pamięci podręcznej 6 GB przepływność P1 wynosi 180 000 żądań na sekundę (RPS) w porównaniu do 100 000 rps dla C3.
* W klastrze Redis przepływność zwiększa się liniowo w miarę zwiększania liczby fragmentów (węzłów) w klastrze. Na przykład jeśli utworzysz klaster P4 10 fragmentów, dostępna przepływność wynosi 400 000 * 10 = 4 miliony RPS.
* Przepustowość dla większych rozmiarów kluczy jest wyższa w warstwie Premium w porównaniu do warstwy standardowej.

| Warstwa cenowa | Rozmiar | Rdzenie procesora CPU | Dostępna przepustowość | Rozmiar wartości 1 KB | Rozmiar wartości 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Standardowe rozmiary pamięci podręcznej** | | |**Megabity na sekundę (Mb/s) / megabajty na sekundę (MB/s)** |**Żądania na sekundę (RPS) inne niż SSL** |**SSL żądań na sekundę (RPS)** |
| C0 | 250 MB | Udostępnione | 100 / 12.5  |  15 000 |   7500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 (w języku) |   6 GB | 4      | 1000 / 125  | 100 000 |  90 000 |
| C4 (w języku) |  13 GB | 2      | 500 / 62.5  |  60 000 |  55,000 |
| C5 (w języku) |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 (w języku) |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Rozmiary pamięci podręcznej premium** | |**Rdzenie procesora CPU na odłamek** | **Megabity na sekundę (Mb/s) / megabajty na sekundę (MB/s)** |**Żądania na sekundę (RPS) inne niż SSL, na fragment** |**Żądania na sekundę (RPS) SSL, na fragment** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400 000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400 000 | 373,000 |

Instrukcje dotyczące konfigurowania stunnel lub pobierania narzędzi `redis-benchmark.exe`Redis, takich jak , zobacz [sekcję Jak mogę uruchomić polecenia Redis?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>W jakim regionie należy zlokalizować pamięć podręczną?
Aby uzyskać najlepszą wydajność i najniższe opóźnienia, znajdź pamięć podręczną Azure cache dla firmy Redis w tym samym regionie co aplikacja kliencka pamięci podręcznej.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak rozliczane są płatności za usługę Azure Cache for Redis?
Cennik usługi Azure Cache for Redis jest [tutaj](https://azure.microsoft.com/pricing/details/cache/). Strona z cennikiem zawiera ceny jako stawkę godzinową. Bufory są rozliczane na podstawie na minutę od momentu utworzenia pamięci podręcznej do czasu usunięcia pamięci podręcznej. Nie ma opcji zatrzymywania lub wstrzymywania rozliczeń pamięci podręcznej.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Czy mogę używać usługi Azure Cache for Redis za pomocą usługi Azure Government Cloud, Azure China Cloud lub Microsoft Azure Germany?
Tak, usługa Azure Cache for Redis jest dostępna w usłudze Azure Government Cloud, Azure China 21Vianet Cloud i Microsoft Azure Germany. Adresy URL dostępu do usługi Azure Cache for Redis i zarządzania nimi są różne w tych chmurach w porównaniu z usługą Azure Public Cloud.

| Chmura   | Sufiks DNS dla Redis            |
|---------|---------------------------------|
| Public  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Niemcy | *.redis.cache.cloudapi.de       |
| Chiny   | *.redis.cache.chinacloudapi.cn  |

Aby uzyskać więcej informacji na temat zagadnień podczas korzystania z usługi Azure Cache dla redis z innymi chmurami, zobacz następujące łącza.

- [Bazy danych platformy Azure dla instytucji rządowych — pamięć podręczna Azure dla programu Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Chmura Azure China 21Vianet — usługa Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)

Aby uzyskać informacje na temat korzystania z usługi Azure Cache for Redis z programem PowerShell w usłudze Azure Government Cloud, Azure China 21Vianet Cloud i Microsoft Azure Germany, zobacz [Jak połączyć się z innymi chmurami — Usługa Azure Cache for Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Do czego działają opcje konfiguracji StackExchange.Redis?
StackExchange.Redis ma wiele opcji. W tej sekcji o mówi o niektórych typowych ustawieniach. Aby uzyskać bardziej szczegółowe informacje na temat opcji StackExchange.Redis, zobacz [Konfiguracja StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Opis | Zalecenie |
| --- | --- | --- |
| AbortOnConnectFail |Po ustawieniu na wartość true połączenie nie zostanie ponownie nawiązane po awarii sieci. |Ustaw wartość false i pozwól StackExchange.Redis ponownie połączyć się automatycznie. |
| ConnectRetry |Liczba prób powtórzenia połączenia podczas początkowego połączenia. |Zapoznaj się z poniższą wskazówką. |
| ConnectTimeout |Limit czasu w ms dla operacji łączenia. |Zapoznaj się z poniższą wskazówką. |

Zazwyczaj domyślne wartości klienta są wystarczające. Opcje można dostosować w zależności od obciążenia.

* **Ponowne próby**
  * Dla ConnectRetry i ConnectTimeout ogólne wskazówki jest, aby zakończyć się niepowodzeniem szybko i ponownie ponowić próbę. Te wskazówki są oparte na obciążeniu i ile czasu średnio zajmuje klientowi wydanie polecenia Redis i otrzymanie odpowiedzi.
  * Pozwól StackExchange.Redis automatycznie ponownie połączyć zamiast sprawdzanie stanu połączenia i ponowne połączenie siebie. **Należy unikać używania właściwości ConnectionMultiplexer.IsConnected**.
  * Snowballing - czasami może napotkać problem, w którym są próby i ponownych snowball i nigdy nie odzyskuje. W przypadku wystąpienia snowballing, należy rozważyć użycie wykładniczego algorytmu ponawiania wycofywania, zgodnie z opisem w [ponów próbę ogólnych wskazówek](../best-practices-retry-general.md) opublikowanych przez grupę Microsoft Patterns & Practices.
  
* **Wartości limitu czasu**
  * Należy wziąć pod uwagę obciążenie i odpowiednio ustawić wartości. Jeśli przechowujesz duże wartości, ustaw limit czasu na wyższą wartość.
  * Ustaw `AbortOnConnectFail` wartość false i pozwól StackExchange.Redis ponownie połączyć dla Ciebie.
  * Użyj pojedynczego connectionmultiplexer wystąpienia dla aplikacji. Za pomocą LazyConnection można utworzyć pojedyncze wystąpienie zwracane przez właściwość Connection, jak pokazano w [połącz do pamięci podręcznej przy użyciu klasy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ustaw `ConnectionMultiplexer.ClientName` właściwość na unikatową nazwę wystąpienia aplikacji do celów diagnostycznych.
  * Użyj `ConnectionMultiplexer` wielu wystąpień dla obciążeń niestandardowych.
      * Możesz śledzić ten model, jeśli masz różne obciążenie w aplikacji. Przykład:
      * Możesz mieć jeden multiplekser do czynienia z dużymi kluczami.
      * Możesz mieć jeden multiplekser do czynienia z małymi kluczami.
      * Można ustawić różne wartości dla limitów czasu połączenia i logiki ponawiania próby dla każdego ConnectionMultiplexer, którego używasz.
      * Ustaw `ClientName` właściwość na każdym multiplekserze, aby ułatwić diagnostykę.
      * Te wskazówki mogą prowadzić do `ConnectionMultiplexer`bardziej uproszczonego opóźnienia na .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Jakiej usługi Azure Cache dla klientów Redis można użyć?
Jedną z wielkich rzeczy na temat Redis jest to, że istnieje wiele klientów obsługujących wiele różnych języków programowania. Aby uzyskać aktualną listę klientów, zobacz [Redis clients](https://redis.io/clients). W przypadku samouczków obejmujących kilka różnych języków i klientów zobacz [Jak używać usługi Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i jej równorzędnych artykułów w spisie treści.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Czy istnieje lokalny emulator dla usługi Azure Cache for Redis?
Nie ma lokalnego emulatora dla usługi Azure Cache for Redis, ale można uruchomić wersję msopentech redis-server.exe z [narzędzi wiersza polecenia Redis](https://github.com/MSOpenTech/redis/releases/) na komputerze lokalnym i połączyć się z nim, aby uzyskać podobne środowisko do emulatora lokalnej pamięci podręcznej, jak pokazano w poniższym przykładzie:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Opcjonalnie można skonfigurować plik [redis.conf,](https://redis.io/topics/config) aby w razie potrzeby dokładniej dopasować [domyślne ustawienia pamięci podręcznej](cache-configure.md#default-redis-server-configuration) dla pamięci podręcznej online Azure cache dla programu Redis.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak uruchomić polecenia Redis?
Można użyć dowolnego z poleceń wymienionych w [poleceniach Redis,](https://redis.io/commands#) z wyjątkiem poleceń wymienionych w [poleceniach Redis, które nie są obsługiwane w usłudze Azure Cache for Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Istnieje kilka opcji uruchamiania poleceń Redis.

* Jeśli masz pamięć podręczną Standard lub Premium, możesz uruchamiać polecenia Redis za pomocą [konsoli Redis](cache-configure.md#redis-console). Konsola Redis udostępnia bezpieczny sposób uruchamiania poleceń Redis w witrynie Azure portal.
* Można również użyć narzędzi wiersza polecenia Redis. Aby z nich korzystać, wykonaj następujące czynności:
* Pobierz [narzędzia wiersza polecenia Redis](https://github.com/MSOpenTech/redis/releases/).
* Połącz się `redis-cli.exe`z pamięcią podręczną za pomocą programu . Przekaż w punkcie końcowym pamięci podręcznej za pomocą przełącznika -h i klucza za pomocą -a, jak pokazano w poniższym przykładzie:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Narzędzia wiersza polecenia Redis nie działają z portem TLS, ale `stunnel` można użyć narzędzia, takiego jak bezpieczne połączenie narzędzi z portem TLS, postępujących zgodnie ze wskazówkami w artykule [Jak używać narzędzia wiersza polecenia Redis z usługą Azure Cache for Redis.](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool)
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Dlaczego usługa Azure Cache for Redis nie ma odwołania do biblioteki klasy MSDN, podobnie jak niektóre inne usługi platformy Azure?
Usługa Microsoft Azure Cache for Redis jest oparta na popularnym rozwiązaniu open source Azure Cache for Redis. Jest dostępny dla wielu języków programowania przez wielu [różnych klientów Redis.](https://redis.io/clients) Każdy klient ma swój własny interfejs API, który wywołuje pamięć podręczną Azure dla wystąpienia Redis przy użyciu [poleceń Redis.](https://redis.io/commands)

Ponieważ każdy klient jest inny, nie ma jednego scentralizowanego odwołania do klasy w msdn, a każdy klient przechowuje własną dokumentację referencyjną. Oprócz dokumentacji referencyjnej istnieje kilka samouczków pokazujących, jak rozpocząć pracę z pamięcią podręczną Azure dla firmy Redis przy użyciu różnych języków i klientów pamięci podręcznej. Aby uzyskać dostęp do tych samouczków, zobacz [Jak używać usługi Azure Cache dla programu Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i jej równorzędnych artykułów w spisie treści.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Czy mogę używać pamięci podręcznej usługi Azure Cache for Redis jako pamięci podręcznej sesji PHP?
Tak, aby używać usługi Azure Cache for Redis jako pamięci podręcznej sesji PHP, określ ciąg połączenia z pamięcią podręczną platformy Azure dla wystąpienia Redis w `session.save_path`.

> [!IMPORTANT]
> Podczas korzystania z usługi Azure Cache for Redis jako pamięci podręcznej sesji PHP, należy kodować adres URL klucz zabezpieczeń używany do łączenia się z pamięcią podręczną, jak pokazano w poniższym przykładzie:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Jeśli klucz nie jest zakodowany w adresie URL, może pojawić się wyjątek z komunikatem, takim jak:`Failed to parse session.save_path`
>
>

Aby uzyskać więcej informacji na temat używania usługi Azure Cache for Redis jako pamięci podręcznej sesji PHP z klientem PhpRedis, zobacz [program obsługi sesji PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co to są bazy danych Redis?

Redis Baz danych są tylko logiczne oddzielenie danych w tym samym wystąpieniu Redis. Pamięć pamięci podręcznej jest współużytkowana między wszystkimi bazami danych, a rzeczywiste zużycie pamięci danej bazy danych zależy od kluczy/wartości przechowywanych w tej bazie danych. Na przykład pamięć podręczna C6 ma 53 GB pamięci, a P5 ma 120 GB. Możesz umieścić wszystkie 53 GB / 120 GB w jednej bazie danych lub podzielić ją między wiele baz danych. 

> [!NOTE]
> W przypadku korzystania z pamięci podręcznej Premium Azure cache dla programu Redis z włączoną obsługą klastrowania dostępna jest tylko baza danych 0. To ograniczenie jest nieodłącznym ograniczeniem Redis i nie jest specyficzne dla usługi Azure Cache for Redis. Aby uzyskać więcej informacji, zobacz [Czy muszę wprowadzić zmiany w aplikacji klienckiej, aby używać klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Kiedy należy włączyć port nienawiązywania TLS/SSL do podłączenia do redis?
Serwer Redis nie obsługuje natywnie protokołu TLS, ale usługa Azure Cache for Redis nie. Jeśli łączysz się z pamięcią podręczną Azure dla programu Redis, a klient obsługuje protokół TLS, taki jak StackExchange.Redis, należy użyć protokołu TLS.

>[!NOTE]
>Port nieobjęty TLS jest domyślnie wyłączony dla nowej pamięci podręcznej platformy Azure dla wystąpień Redis. Jeśli klient nie obsługuje protokołu TLS, należy włączyć port nie-TLS, postępając zgodnie ze wskazówkami w sekcji [Porty dostępu](cache-configure.md#access-ports) [w artykule Konfigurowanie pamięci podręcznej w pamięci podręcznej platformy Azure dla programu Redis.](cache-configure.md)
>
>

Narzędzia Redis, `redis-cli` takie jak nie działają z portem TLS, `stunnel` ale można użyć narzędzia, takiego jak bezpieczne połączenie narzędzi z portem TLS, postępując zgodnie ze wskazówkami w blogu [Dostawca stanu sesji ogłaszania ASP.NET dla komunikatu redis Preview Release.](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

Aby uzyskać instrukcje dotyczące pobierania narzędzi Redis, zobacz sekcję [Jak uruchomić polecenia Redis?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Jakie są najlepsze praktyki produkcyjne?
* [StackExchange.Redis najlepsze praktyki](#stackexchangeredis-best-practices)
* [Konfiguracja i koncepcje](#configuration-and-concepts)
* [Testowanie wydajności](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis najlepsze praktyki
* Ustaw `AbortConnect` wartość false, a następnie pozwól ConnectionMultiplexer ponownie połączyć się automatycznie. [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Ponownie użyć ConnectionMultiplexer - nie należy tworzyć nowy dla każdego żądania. Zaleca `Lazy<ConnectionMultiplexer>` [się pokazanie](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) wzoru.
* Redis działa najlepiej z mniejszymi wartościami, więc rozważ posiekanie większych danych na wiele klawiszy. W [tej dyskusji Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb jest uważany za duży. Przeczytaj [ten artykuł,](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) aby zapoznać się z przykładowym problemem, który może być spowodowany przez duże wartości.
* Skonfiguruj [ustawienia threadpool,](#important-details-about-threadpool-growth) aby uniknąć przesuń czasu.
* Użyj co najmniej domyślnego connectTimeout 5 sekund. Ten interwał daje StackExchange.Redis wystarczająco dużo czasu, aby ponownie ustanowić połączenie w przypadku blip sieci.
* Należy pamiętać o kosztach wydajności związanych z różnymi operacjami, które są uruchomione. Na przykład `KEYS` polecenie jest operacją O(n) i należy jej unikać. [Witryna redis.io](https://redis.io/commands/) zawiera szczegółowe informacje dotyczące złożoności czasu dla każdej operacji, którą obsługuje. Kliknij każde polecenie, aby zobaczyć złożoność każdej operacji.

#### <a name="configuration-and-concepts"></a>Konfiguracja i koncepcje
* Użyj warstwy standardowej lub premium dla systemów produkcyjnych. Warstwa Podstawowa to system z jednym węzłem bez replikacji danych i bez umowy SLA. Ponadto można użyć przynajmniej pamięci podręcznej C1. Pamięci podręczne C0 są zwykle używane dla prostych scenariuszy deweloperskich/testowych.
* Należy pamiętać, że Redis jest magazynem danych **w pamięci.** Przeczytaj [ten artykuł,](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) aby poznać scenariusze, w których może wystąpić utrata danych.
* Opracowanie systemu w taki sposób, że może obsługiwać blips połączenia [z powodu poprawek i pracy awaryjnej](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testowanie wydajności
* Zacznij od `redis-benchmark.exe` użycia, aby uzyskać odczucie możliwe przepływności przed napisaniem własnych testów perf. Ponieważ `redis-benchmark` nie obsługuje protokołu TLS, przed uruchomieniem testu należy [włączyć port non-TLS za pośrednictwem witryny Azure portal.](cache-configure.md#access-ports) Aby uzyskać przykłady, zobacz [Jak sprawdzić poziom danych i przetestować wydajność pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Maszyna wirtualna klienta używana do testowania powinna znajdować się w tym samym regionie co pamięć podręczna usługi Azure dla wystąpienia Redis.
* Zalecamy korzystanie z serii Dv2 VM dla klienta, ponieważ mają one lepszy sprzęt i powinny dać najlepsze wyniki.
* Upewnij się, że wybrana maszyna wirtualna klienta ma co najmniej tyle możliwości przetwarzania i przepustowości, co testowana pamięć podręczna.
* Włącz usługę VRSS na komputerze klienckim, jeśli korzystasz z systemu Windows. [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://technet.microsoft.com/library/dn383582.aspx).
* Wystąpienia Redis warstwy premium mają lepsze opóźnienie sieci i przepustowość, ponieważ działają na lepszym sprzęcie zarówno dla procesora CPU, jak i sieci.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jakie są niektóre z rozważań podczas korzystania z typowych poleceń Redis?

* Należy unikać używania niektórych poleceń Redis, które zajmują dużo czasu, chyba że w pełni zrozumieć wpływ tych poleceń. Na przykład nie należy uruchamiać polecenia [KEYS](https://redis.io/commands/keys) w produkcji. W zależności od liczby kluczy może upłynąć dużo czasu, aby powrócić. Redis jest serwerem jednowątkowym i przetwarza polecenia pojedynczo. Jeśli masz inne polecenia wydane po KEYS, nie będą przetwarzane, dopóki Redis nie przetworzy polecenia KEYS. [Witryna redis.io](https://redis.io/commands/) zawiera szczegółowe informacje dotyczące złożoności czasu dla każdej operacji, którą obsługuje. Kliknij każde polecenie, aby zobaczyć złożoność każdej operacji.
* Rozmiary kluczy - czy powinienem używać małych kluczy/wartości lub dużych kluczy/wartości? To zależy od scenariusza. Jeśli scenariusz wymaga większych kluczy, można dostosować ConnectionTimeout, a następnie ponów próbę wartości i dostosować logikę ponawiania. Z punktu widzenia serwera Redis mniejsze wartości zapewniają lepszą wydajność.
* Te zagadnienia nie oznaczają, że nie można przechowywać większych wartości w Redis; użytkownik musi być świadomy następujących kwestii. Opóźnienia będą wyższe. Jeśli masz jeden zestaw danych, który jest większy i jeden, który jest mniejszy, można użyć wielu ConnectionMultiplexer wystąpień, każdy skonfigurowany z innym zestawem limit czasu i ponawiania wartości, zgodnie z opisem w poprzednim [Co zrobić StackExchange.Redis opcji konfiguracji zrobić](#cache-configuration) sekcji.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak mogę sprawdzić i przetestować wydajność mojej pamięci podręcznej?
* [Włącz diagnostykę pamięci podręcznej](cache-how-to-monitor.md#enable-cache-diagnostics), aby móc [monitorować](cache-how-to-monitor.md) jej kondycję. Możesz wyświetlić metryki w witrynie Azure portal, a także [pobrać i przejrzeć](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) je za pomocą narzędzi do wyboru.
* Aby załadować serwer Redis, można użyć programu redis-benchmark.exe.
* Upewnij się, że klient testowania obciążenia i pamięci podręcznej Azure dla Redis znajdują się w tym samym regionie.
* Użyj redis-cli.exe i monitoruj pamięć podręczną za pomocą polecenia INFO.
* Jeśli obciążenie powoduje fragmentację pamięci o dużej wysokości, należy skalować do większego rozmiaru pamięci podręcznej.
* Aby uzyskać instrukcje dotyczące pobierania narzędzi Redis, zobacz sekcję [Jak uruchomić polecenia Redis?](#cache-commands)

Poniższe polecenia zawierają przykład użycia programu redis-benchmark.exe. Aby uzyskać dokładne wyniki, uruchom te polecenia z maszyny Wirtualnej w tym samym regionie co pamięć podręczna.

* Testowanie żądań SET potoku przy użyciu ładunku 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test pipelined get żądań przy użyciu ładunku 1k.
  UWAGA: Uruchom test SET pokazany powyżej, aby wypełnić pamięć podręczną

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Ważne szczegóły dotyczące rozwoju ThreadPool
Clr ThreadPool ma dwa typy wątków - "Pracownik" i "Port zakończenia we/wy" (IOCP) wątki.

* Wątki robocze są używane `Task.Run(…)`do `ThreadPool.QueueUserWorkItem(…)` takich rzeczy jak przetwarzanie , lub metod. Te wątki są również używane przez różne składniki w clr, gdy praca musi się zdarzyć na wątku tła.
* Wątki IOCP są używane, gdy dzieje się asynchronikowa we/wy, na przykład podczas odczytu z sieci.

Pula wątków udostępnia nowe wątki robocze lub wątki zakończenia we/wy na żądanie (bez ograniczania przepustowości), dopóki nie osiągnie ustawienia "Minimum" dla każdego typu wątku. Domyślnie minimalna liczba wątków jest ustawiona na liczbę procesorów w systemie.

Gdy liczba istniejących (zajęty) wątków osiągnie "minimalną" liczbę wątków, ThreadPool będzie ograniczać szybkość, z jaką wstrzykuje nowe wątki do jednego wątku na 500 milisekund. Zazwyczaj jeśli system pobiera serię pracy wymagającej wątku IOCP, będzie przetwarzać, że działa szybko. Jednak jeśli seria pracy jest więcej niż skonfigurowane ustawienie "Minimum", będzie pewne opóźnienie w przetwarzaniu niektórych prac, jak ThreadPool czeka na jedną z dwóch rzeczy się zdarzyć.

1. Istniejący wątek staje się wolny do przetworzenia pracy.
2. Żaden istniejący wątek nie jest wolny dla 500 ms, więc tworzony jest nowy wątek.

Zasadniczo oznacza to, że gdy liczba zajęty wątków jest większa niż minimalne wątki, prawdopodobnie płacisz opóźnienie 500-ms, zanim ruch sieciowy jest przetwarzany przez aplikację. Ponadto ważne jest, aby pamiętać, że gdy istniejący wątek pozostaje bezczynny przez ponad 15 sekund (w oparciu o to, co pamiętam), zostanie oczyszczony i ten cykl wzrostu i skurczu może się powtórzyć.

Jeśli przyjrzymy się przykładowy komunikat o błędzie z StackExchange.Redis (kompilacja 1.0.450 lub nowsze), zobaczysz, że teraz drukuje statystyki ThreadPool (zobacz IOCP i WORKER szczegóły poniżej).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

W poprzednim przykładzie widać, że dla wątku IOCP istnieje sześć zajęty wątków i system jest skonfigurowany, aby umożliwić cztery minimalne wątki. W takim przypadku klient prawdopodobnie widziałby dwa opóźnienia 500-ms, ponieważ 6 > 4.

Należy zauważyć, że StackExchange.Redis może trafić limity czasu, jeśli wzrost wątków IOCP lub WORKER zostanie ograniczona.

### <a name="recommendation"></a>Zalecenie

Biorąc pod uwagę te informacje, zdecydowanie zaleca się, aby klienci ustawili minimalną wartość konfiguracji dla wątków IOCP i WORKER na coś większego niż wartość domyślna. Nie możemy udzielić uniwersalnych wskazówek dotyczących tej wartości, ponieważ właściwa wartość dla jednej aplikacji będzie prawdopodobnie zbyt wysoka lub niska dla innej aplikacji. To ustawienie może również mieć wpływ na wydajność innych części skomplikowanych aplikacji, więc każdy klient musi dostosować to ustawienie do swoich specyficznych potrzeb. Dobrym miejscem startowym jest 200 lub 300, a następnie przetestuj i podrasuj w razie potrzeby.

Jak skonfigurować to ustawienie:

* Zalecamy zmianę tego ustawienia programowo przy użyciu [metody ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) w `global.asax.cs`. Przykład:

```cs
private readonly int minThreads = 200;
void Application_Start(object sender, EventArgs e)
{
    // Code that runs on application startup
    AreaRegistration.RegisterAllAreas();
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
    ThreadPool.SetMinThreads(minThreads, minThreads);
}
```

  > [!NOTE]
  > Wartość określona przez tę metodę jest ustawieniem globalnym, wpływającym na całą Domenę Aplikacji. Na przykład, jeśli masz komputer 4-rdzeniowy i chcesz ustawić *minWorkerThreads* i *minIoThreads* na procesor w czasie wykonywania, należy użyć **ThreadPool.SetMinThreads(200, 200)**.

* Możliwe jest również określenie ustawienia minimalnych wątków za pomocą ustawienia konfiguracji [ *minIoThreads* lub *minWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) pod elementem `<processModel>` konfiguracyjnym w `Machine.config`, zwykle znajduje się w `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`. **Ustawienie liczby minimalnych wątków w ten sposób zazwyczaj nie jest zalecane, ponieważ jest to ustawienie dla całego systemu.**

  > [!NOTE]
  > Wartość określona w tym elemencie konfiguracji jest ustawieniem *dla rdzenia.* Na przykład, jeśli masz komputer 4-rdzeniowy i chcesz, aby ustawienie *minIoThreads* było `<processModel minIoThreads="50"/>`200 w czasie wykonywania, należy użyć .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Włącz gc serwera, aby uzyskać większą przepływność na kliencie podczas korzystania z StackExchange.Redis
Włączenie GC serwera można zoptymalizować klienta i zapewnić lepszą wydajność i przepływność podczas korzystania z StackExchange.Redis. Aby uzyskać więcej informacji na temat gc serwera i jak go włączyć, zobacz następujące artykuły:

* [Aby włączyć gc serwera](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Podstawy dotyczące odzyskiwania pamięci](/dotnet/standard/garbage-collection/fundamentals)
* [Wyrzucanie elementów bezużytecznych i wydajność](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Zagadnienia dotyczące wydajności wokół połączeń

Każda warstwa cenowa ma różne limity dla połączeń klientów, pamięci i przepustowości. Podczas gdy każdy rozmiar pamięci podręcznej umożliwia *do* określonej liczby połączeń, każde połączenie z Redis ma obciążenie skojarzone z nim. Przykładem takiego obciążenia może być użycie procesora CPU i pamięci w wyniku szyfrowania TLS/SSL. Maksymalny limit połączenia dla danego rozmiaru pamięci podręcznej przyjmuje lekko załadowaną pamięć podręczną. Jeśli obciążenie z narzutu połączenia *plus* obciążenie z operacji klienta przekracza pojemność systemu, pamięć podręczna może wystąpić problemy z pojemnością, nawet jeśli nie przekroczono limitu połączenia dla bieżącego rozmiaru pamięci podręcznej.

Aby uzyskać więcej informacji na temat różnych limitów połączeń dla każdej warstwy, zobacz [Azure Cache for Redis cennika](https://azure.microsoft.com/pricing/details/cache/). Aby uzyskać więcej informacji o połączeniach i innych konfiguracjach domyślnych, zobacz [Domyślna konfiguracja serwera Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak monitorować kondycję i wydajność pamięci podręcznej?
Microsoft Azure Cache for Redis instances mogą być monitorowane w [witrynie Azure portal](https://portal.azure.com). Możesz wyświetlać metryki, przypinać wykresy metryk do tablicy startowej, dostosowywać zakres daty i czasu wykresów monitorowania, dodawać i usuwać dane z wykresów oraz ustawiać alerty, gdy spełnione są określone warunki. Aby uzyskać więcej informacji, zobacz [Monitorowanie pamięci podręcznej Azure dla redis](cache-how-to-monitor.md).

Menu Usługi Azure Cache for Redis **Resource** zawiera również kilka narzędzi do monitorowania i rozwiązywania problemów z pamięciami podręcznymi.

* **Diagnozowanie i rozwiązywanie problemów** dostarcza informacji na temat typowych problemów i strategii ich rozwiązywania.
* **Kondycja zasobów** obserwuje zasób i informuje, czy działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat usługi kondycji zasobów platformy Azure, zobacz [omówienie kondycji zasobów platformy Azure.](../resource-health/resource-health-overview.md)
* **Nowe żądanie pomocy technicznej** udostępnia opcje otwierania żądania pomocy technicznej dla pamięci podręcznej.

Te narzędzia umożliwiają monitorowanie kondycji pamięci podręcznej platformy Azure dla wystąpień Redis i ułatwiają zarządzanie aplikacjami buforowania. Aby uzyskać więcej informacji, zobacz sekcję "Obsługa & ustawienia rozwiązywania problemów" w sekcji [Jak skonfigurować pamięć podręczną Azure Cache for Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Dlaczego widzę limity czasu?
Limity czasu w kliencie, którego używasz do rozmowy z redis. Gdy polecenie jest wysyłane do serwera Redis, polecenie jest umieszczane w kolejce, a serwer Redis ostatecznie odbiera polecenie i wykonuje je. Jednak klient może limit czasu podczas tego procesu i jeśli robi wyjątek jest wywoływany po stronie wywołującej. Aby uzyskać więcej informacji na temat rozwiązywania problemów z limitem czasu, zobacz [rozwiązywanie problemów po stronie klienta](cache-troubleshoot-client.md) i [wyjątki limitu czasu StackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Dlaczego mój klient został odłączony od pamięci podręcznej?
Poniżej przedstawiono niektóre typowe przyczyny rozłączenia pamięci podręcznej.

* Przyczyny po stronie klienta
  * Aplikacja kliencka została ponownie rozmieszczona.
  * Aplikacja kliencka wykonała operację skalowania.
    * W przypadku usług w chmurze lub aplikacji sieci Web może to być spowodowane skalowaniem automatycznym.
  * Zmieniono warstwę sieci po stronie klienta.
  * Błędy przejściowe wystąpiły w kliencie lub w węzłach sieciowych między klientem a serwerem.
  * Osiągnięto limity progowe przepustowości.
  * Operacje związane z procesorem CPU trwały zbyt długo.
* Przyczyny po stronie serwera
  * W przypadku standardowej oferty pamięci podręcznej usługa Azure Cache for Redis zainicjowała tryb fail-over z węzła podstawowego do węzła pomocniczego.
  * Platforma Azure poprawiała wystąpienie, w którym została wdrożona pamięć podręczna
    * Może to być dla aktualizacji serwera Redis lub ogólnej konserwacji maszyny Wirtualnej.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Która oferta usługi Azure Cache jest dla mnie odpowiednia?
> [!IMPORTANT]
> Zgodnie z zeszłorocznym [ogłoszeniem](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)usługa Azure Managed Cache Service i usługa Azure In-Role Cache **zostały wycofane** 30 listopada 2016 r. Naszym zaleceniem jest użycie [usługi Azure Cache for Redis.](https://azure.microsoft.com/services/cache/) Aby uzyskać informacje dotyczące migracji, zobacz [Migrowanie z usługi zarządzanej pamięci podręcznej do usługi Azure Cache for Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Usługa Azure Cache for Redis jest ogólnie dostępna w rozmiarach do 120 GB i ma umowę SLA o dostępności 99,9%. Nowa [warstwa premium](cache-premium-tier-intro.md) oferuje rozmiary do 1,2 TB i obsługę klastrowania, sieci wirtualnej i trwałości, z 99,9% SLA.

Usługa Azure Cache for Redis umożliwia klientom korzystanie z bezpiecznej, dedykowanej pamięci podręcznej azure dla firmy Redis zarządzanej przez firmę Microsoft. Dzięki tej ofercie możesz wykorzystać bogaty zestaw funkcji i ekosystem dostarczony przez Redis oraz niezawodny hosting i monitorowanie firmy Microsoft.

W przeciwieństwie do tradycyjnych pamięci podręcznych, które zajmują się tylko parami klucz-wartość, Redis jest popularny ze względu na wysoce wydajne typy danych. Redis obsługuje również uruchamianie operacji niepodzielnych na tych typach, takich jak dołączanie do ciągu; zwiększanie wartości w mieszaniu; pchanie do listy; przecięcie, zrost i różnica; lub uzyskanie członka z najwyższym rankingiem w posortowanym zestawie. Inne funkcje obejmują obsługę transakcji, pub / sub, skrypty Lua, klucze z ograniczonym czasem na żywo i ustawienia konfiguracji, aby Redis zachowywał się bardziej jak tradycyjna pamięć podręczna.

Kolejnym kluczowym aspektem sukcesu Redis jest zdrowy, tętniący życiem ekosystem open-source zbudowany wokół niego. Znajduje to odzwierciedlenie w zróżnicowanym zestawie klientów Redis dostępnych w wielu językach. Ten ekosystem i szeroki zakres klientów umożliwiają korzystanie z usługi Azure Cache for Redis przez prawie każde obciążenie, które można utworzyć wewnątrz platformy Azure.

Aby uzyskać więcej informacji na temat rozpoczynania pracy z usługą Azure Cache for Redis, zobacz [Jak używać pamięci podręcznej Azure Cache dla redis](cache-dotnet-how-to-use-azure-redis-cache.md) i [usługi Azure Cache dla dokumentacji Redis.](index.yml)

### <a name="managed-cache-service"></a>Usługa zarządzanej pamięci podręcznej
[Usługa Zarządzana pamięć podręczna została wycofana 30 listopada 2016 r.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić zarchiwizowaną dokumentację, zobacz [Dokumentacja usługi zarchiwizowanej zarządzanej pamięci podręcznej](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Pamięć podręczna roli
[Pamięć podręczna in-Role została wycofana 30 listopada 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić zarchiwizowaną dokumentację, zobacz [Dokumentacja zarchiwizowanej pamięci podręcznej w roli](/previous-versions/azure/azure-services/dn386103(v=azure.100))głównej .

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
