---
title: Pamięć podręczna systemu Azure dla usługi Redis — często zadawane pytania | Dokumentacja firmy Microsoft
description: Dowiedz się odpowiedzi na często zadawane pytania dotyczące, wzorce i najlepsze rozwiązania dla usługi Azure Cache dla pamięci podręcznej Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: yegu
ms.openlocfilehash: 65e8553969aa92848b1c4496724a7b7754b5d659
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895600"
---
# <a name="azure-cache-for-redis-faq"></a>Azure Cache for Redis — często zadawane pytania
Dowiedz się, jak się odpowiedzi na często zadawane pytania dotyczące, wzorce i najlepsze rozwiązania dla usługi Azure Cache dla pamięci podręcznej Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli moje pytanie nie ma tutaj odpowiedzi?
Jeśli Twoje pytanie nie ma na liście, Daj nam znać, a my pomożemy Ci znaleźć odpowiedzi.

* Możesz zadać pytanie na komentarze na końcu tego — często zadawane pytania i skontaktuj się z zespołem usługi Azure Cache i inni członkowie społeczności, informacje o tym artykule.
* Aby uzyskać dostęp do większej liczby osób, możesz zadać pytanie na [Forum MSDN usługi Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) i skontaktuj się z zespołem usługi Azure Cache i inni członkowie społeczności.
* Jeśli chcesz utworzyć żądanie funkcji, można przesłać żądania i pomysłów dotyczących [pamięci podręcznej Azure redis Cache User Voice](https://feedback.azure.com/forums/169382-cache).
* Można również wysłać wiadomość e-mail do nas pod numer [opinii zewnętrznych pamięć podręczna Azure](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Pamięć podręczna systemu Azure, aby uzyskać podstawowe informacje o pamięci podręcznej Redis
Często zadawane pytania w tej sekcji obejmują niektóre z podstawy pamięć podręczna systemu Azure dla usługi Redis.

* [Co to jest Azure pamięci podręcznej redis Cache?](#what-is-azure-cache-for-redis)
* [Jak mogę rozpocząć pracę z usługą Azure Cache dla usługi Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Poniższe często zadawane pytania obejmuje podstawowe pojęcia i pytania dotyczące usługi Azure Cache dla usługi Redis i odpowiedzi są w innej sekcji często zadawane pytania.

* [Jakie usługi Azure Cache oferty pamięci podręcznej Redis i rozmiaru należy używać?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Jakie usługi Azure Cache dla klientów usługi Redis można używać?](#what-azure-cache-for-redis-clients-can-i-use)
* [Dla usługi Redis jest lokalnym emulator dla usługi Azure Cache?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak monitorować kondycję i wydajność przepełnieniu pamięci podręcznej?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Często zadawane pytania dotyczące planowania
* [Jakie usługi Azure Cache oferty pamięci podręcznej Redis i rozmiaru należy używać?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Pamięć podręczna systemu Azure, wydajność pamięci podręcznej Redis](#azure-cache-for-redis-performance)
* [W jakim regionie ma szukać przepełnieniu pamięci podręcznej?](#in-what-region-should-i-locate-my-cache)
* [Jak są rozliczane dla pamięci podręcznej Azure redis Cache?](#how-am-i-billed-for-azure-cache-for-redis)
* [Dla pamięci podręcznej Redis w chmurze Azure Government, Azure albo chmura lub Microsoft Azure (Niemcy) można używać usługi Azure Cache?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Programowanie — często zadawane pytania
* [Co zrobić opcje konfiguracji StackExchange.Redis](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jakie usługi Azure Cache dla klientów usługi Redis można używać?](#what-azure-cache-for-redis-clients-can-i-use)
* [Dla usługi Redis jest lokalnym emulator dla usługi Azure Cache?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak uruchomić polecenia Redis](#how-can-i-run-redis-commands)
* [Dlaczego nie ma MSDN odwołanie do biblioteki klas np. niektóre z innymi usługami platformy Azure w pamięci podręcznej Redis Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Czy można używać usługi Azure Cache dla usługi Redis jako pamięci podręcznej sesji PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co to są bazy danych Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Często zadawane pytania zabezpieczeń
* [Kiedy należy włączyć port bez protokołu SSL do łączenia się z pamięci podręcznej Redis](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Produkcja — często zadawane pytania
* [Jakie są najważniejsze wskazówki produkcji?](#what-are-some-production-best-practices)
* [Jakie są niektóre zagadnienia podczas korzystania z Typowe polecenia Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak testu wydajności i przetestować wydajność przepełnieniu pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Ważne informacje dotyczące rozwoju puli wątków](#important-details-about-threadpool-growth)
* [Włącz serwer GC w celu uzyskania większej przepustowości na komputerze klienckim, używając StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Zagadnienia dotyczące wydajności w całym połączeń](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorowanie i rozwiązywanie problemów — często zadawane pytania
Często zadawane pytania w tej sekcji obejmują typowe monitorowania i pytania dotyczące rozwiązywania problemów. Aby uzyskać więcej informacji dotyczących monitorowania i rozwiązywanie problemów z pamięci podręcznej dla wystąpień pamięci podręcznej Redis Azure, zobacz [jak monitorować pamięć podręczna systemu Azure dla usługi Redis](cache-how-to-monitor.md) i [jak rozwiązywać problemy z pamięć podręczna systemu Azure dla usługi Redis](cache-how-to-troubleshoot.md).

* [Jak monitorować kondycję i wydajność przepełnieniu pamięci podręcznej?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Dlaczego widzę przekroczeń limitu czasu?](#why-am-i-seeing-timeouts)
* [Dlaczego mój klient został rozłączony z pamięci podręcznej](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Wcześniejsze oferta pamięci podręcznej — często zadawane pytania
* [Która oferta pamięci podręcznej Azure jest dla mnie odpowiednia?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Co to jest usługa Azure Cache for Redis?
Pamięć podręczna systemu Azure dla usługi Redis opiera się na popularnych oprogramowania typu open-source [Redis](https://redis.io/). Daje ona dostęp do zabezpieczonej, dedykowanej pamięci podręcznej Azure dla usługi Redis, zarządzanej przez firmę Microsoft i dostępny z poziomu dowolnej aplikacji na platformie Azure. Aby uzyskać bardziej szczegółowym omówieniem, zobacz [pamięci podręcznej Redis Azure](https://azure.microsoft.com/services/cache/) stronę produktu w witrynie Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak mogę rozpocząć pracę z usługą Azure Cache dla usługi Redis?
Istnieje kilka sposobów, które możesz rozpocząć pracę z usługą Azure Cache dla usługi Redis.

* Możesz zapoznać się z jednego z naszych samouczków dotyczących [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), i [językaPython](cache-python-get-started.md).
* Możesz obejrzeć [sposobu kompilacji o wysokiej wydajności aplikacji za pomocą Microsoft Azure Cache dla usługi Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Możesz zapoznać się w dokumentacji klienta dla klientów, którzy jest zgodny z językiem programowania projekt, aby zobaczyć sposób użycia usługi Redis. Istnieje wielu klientów Redis, które mogą służyć z pamięcią podręczną Azure redis Cache. Aby uzyskać listę klientów usługi Redis, zobacz [ https://redis.io/clients ](https://redis.io/clients).

Jeśli nie masz jeszcze konta platformy Azure, możesz to zrobić:

* [Utworzyć bezpłatne konto platformy Azure ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Otrzymasz środki, które możesz wykorzystać do wypróbowania płatnych usług Azure. Nawet po wyczerpaniu tych środków możesz zachować konto i korzystać z bezpłatnych usług i funkcji platformy Azure.
* [Aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Jakie usługi Azure Cache oferty pamięci podręcznej Redis i rozmiaru należy używać?
Każda pamięć podręczna Azure, oferty pamięci podręcznej Redis zapewnia różne poziomy **rozmiar**, **przepustowości**, **wysokiej dostępności**, i **SLA** opcje.

Poniżej przedstawiono zagadnienia dotyczące wybierania oferty pamięci podręcznej.

* **Pamięć**: Warstwy podstawowa i standardowa oferują 250 MB – 53 GB. Warstwa Premium zapewnia do 530 GB. Aby uzyskać więcej informacji, zobacz [pamięci podręcznej Azure redis Cache cennik](https://azure.microsoft.com/pricing/details/cache/).
* **Wydajność sieci**: Jeśli masz obciążenie, która wymaga wysokiej przepływności, warstwa Premium oferuje większej przepustowości w porównaniu do wersji Standard lub Basic. Również w każdej warstwie większy rozmiar pamięci podręcznych korzystania z większej przepustowości z powodu podstawowej maszyny Wirtualnej, który jest hostem pamięci podręcznej. Zobacz [następujących tabeli](#cache-performance) Aby uzyskać więcej informacji.
* **Przepływność**: Warstwa Premium oferuje maksymalną przepustowość dostępna. Jeśli pamięć podręczna serwera lub klienta osiągnie limity przepustowości, może pojawić się przekroczeń limitu czasu po stronie klienta. Aby uzyskać więcej informacji zobacz w poniższej tabeli.
* **Wysoka dostępność/SLA**: Pamięć podręczna systemu Azure dla usługi Redis gwarantuje, że standardowa/Premium pamięć podręczna jest dostępna co najmniej 99,9% czasu. Aby dowiedzieć się więcej o umowach SLA, zobacz [pamięci podręcznej Azure redis Cache cennik](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Umowa SLA obejmuje jedynie połączenia z punktami końcowymi pamięci podręcznej. Nie zapewnia ona ochrony przed utratą danych. Firma Microsoft zaleca używanie funkcji trwałości danych Redis w warstwie Premium, aby zwiększyć odporność na utratę danych.
* **Trwałość danych redis**: Warstwa Premium umożliwia utrwalanie danych pamięci podręcznej na koncie usługi Azure Storage. W pamięci podręcznej podstawowa i standardowa wszystkie dane są przechowywane tylko w pamięci. W przypadku podstawowych problemy dotyczące infrastruktury może być możliwej utracie danych. Firma Microsoft zaleca używanie funkcji trwałości danych Redis w warstwie Premium, aby zwiększyć odporność na utratę danych. Pamięć podręczna systemu Azure dla usługi Redis zapewnia RDB i AOF (wkrótce) opcje w stan trwały pamięci podręcznej Redis. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trwałości dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-persistence.md).
* **Klaster redis**: Tworzenie pamięci podręcznych większych niż 53 GB i współdzielenie danych między wieloma węzłami Redis, umożliwia klastrowanie Redis, która jest dostępna w warstwie Premium. Każdy węzeł składa się z pary pamięci podręcznej podstawowy i węzeł repliki w celu zapewnienia wysokiej dostępności. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrowania dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-clustering.md).
* **Ulepszone zabezpieczenia i sieci izolacji**: Wdrożenie usługi Azure Virtual Network (VNET) zapewnia lepsze zabezpieczenia i izolację pamięci podręcznej usługi Azure redis cache, a także podsieci, zasady kontroli dostępu, i inne funkcje ograniczające dostęp. Aby uzyskać więcej informacji, zobacz [Konfigurowanie obsługi sieci wirtualnej dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-vnet.md).
* **Konfiguruj magazyn Redis**: W warstwach Standard i Premium możesz skonfigurować dla powiadomienia przestrzeni kluczy pamięci podręcznej Redis.
* **Maksymalna liczba połączeń klienta**: Warstwa Premium oferuje maksymalną liczbę klientów, które można nawiązać połączenia z pamięci podręcznej Redis przy użyciu większej liczby połączeń dla większej wielkości pamięci podręcznych. Klaster nie zwiększa liczbę połączeń, które są dostępne dla klastra pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [pamięci podręcznej Azure Redis cennik](https://azure.microsoft.com/pricing/details/cache/).
* **Dedykowany serwer Redis Core**: W warstwie Premium dowolnej wielkości pamięci podręcznej ma rdzeni dedykowanych dla usługi Redis. W warstwach podstawowa i standardowa, rozmiar C1 powyżej się rdzeni dedykowanych dla serwera Redis.
* **Redis jest jednowątkowym** więc mających więcej niż dwa rdzenie nie zapewnia dodatkowych korzyści w posiadanie tylko dwa rdzenie, ale większe rozmiary maszyny Wirtualnej zwykle mają większą przepustowość niż mniejsze rozmiary. Jeśli pamięć podręczna serwera lub klienta osiągnie limity przepustowości, zostanie wyświetlony przekroczeń limitu czasu po stronie klienta.
* **Ulepszenia wydajności**: Pamięci podręczne w warstwie Premium są wdrażane na sprzęcie, który ma szybszymi procesorami, co zapewnia lepszą wydajność w porównaniu do warstwy podstawowa lub standardowa. Podręczna w warstwie Premium mają większą przepływnością i mniejszymi opóźnieniami.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Pamięć podręczna systemu Azure, wydajność pamięci podręcznej Redis
W poniższej tabeli przedstawiono wartości maksymalnej przepustowości, przestrzegać podczas testowania różne rozmiary Standard i Premium zapisuje w pamięci podręcznej przy użyciu `redis-benchmark.exe` z maszyny Wirtualnej IaaS względem usługi Azure Cache dla punktu końcowego pamięci podręcznej Redis. Przepływność SSL testu porównawczego usługi redis jest używany przy użyciu programu stunnel nawiązywania połączenia z usługą Azure Cache dla punktu końcowego pamięci podręcznej Redis.

>[!NOTE] 
>Te wartości nie są gwarantowane i ma żadnej umowy SLA dla tych numerów, ale powinien być typowe. Powinny zostać załadowane przetestować aplikację, aby określić rozmiar pamięci podręcznej odpowiedniej dla twojej aplikacji.
>Te numery mogą ulec zmianie, jak okresowo publikujemy nowszych wyników.
>

Z tej tabeli firma Microsoft narysować następujących wniosków:

* Przepływność dla pamięci podręcznych, które mają taki sam rozmiar jest większa w warstwie Premium w porównaniu z warstwą standardowa. Na przykład za pomocą 6 GB pamięci podręcznej, przepływność P1 to 180 000 RPS w porównaniu do 100 000 dla C3.
* Przy użyciu klastrów usługi Redis przepływności, wydłuża liniowo zwiększyć liczbę fragmentów (węzłów) w klastrze. Na przykład, jeśli tworzysz klaster P4 10 fragmentów, następnie dostępne przepływność wynosi 400 000 * 10 = 4 mln RPS.
* Przepływność dla większych rozmiarów klucza jest większa w warstwie Premium w porównaniu z warstwą standardowa.

| Warstwa cenowa | Rozmiar | Rdzenie procesora CPU | Dostępna przepustowość | Rozmiar wartości 1 KB | Rozmiar wartości 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Rozmiary Standard pamięci podręcznej** | | |**Megabity na sekundę (Mb/s) / MB / s (MB/s)** |**Żądania na drugi bez protokołu SSL (jednostek Uzależnionych)** |**Żądania na drugim SSL (jednostek Uzależnionych)** |
| C0 |250 MB |Udostępniona |100 / 12.5 |15 000 |7500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37,000 |
| C3 |6 GB |4 |1000 / 125 |100 000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102,000 |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Rozmiar pamięci podręcznej — wersja Premium** | |**Liczba rdzeni procesora CPU na fragment** | **Megabity na sekundę (Mb/s) / MB / s (MB/s)** |**Żądania na drugim (jednostek Uzależnionych) bez protokołu SSL, na fragment** |**Żądania na drugim SSL (jednostek Uzależnionych), na fragment** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400 000 |373,000 |

Aby uzyskać instrukcje dotyczące konfigurowania programu stunnel, lub takie jak pobieranie narzędzi Redis `redis-benchmark.exe`, zobacz [jak uruchomić polecenia Redis?](#cache-commands) sekcji.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>W jakim regionie ma szukać przepełnieniu pamięci podręcznej?
Aby uzyskać najlepszą wydajność i najniższym opóźnieniu zlokalizuj pamięć podręczna Azure dla usługi Redis, w tym samym regionie co aplikacja klienta pamięci podręcznej.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak są rozliczane dla pamięci podręcznej Azure redis Cache?
Pamięć podręczna systemu Azure, cennik usługi Redis jest [tutaj](https://azure.microsoft.com/pricing/details/cache/). Wyświetla listę stronie cennika, ceny za godzinę. Pamięci podręczne są rozliczane na podstawie na minutę od czasu utworzenia pamięci podręcznej aż do czasu, które pamięć podręczna zostanie usunięta. Nie ma opcji dla zatrzymanie lub wstrzymanie Naliczanie opłat w pamięci podręcznej.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Dla pamięci podręcznej Redis w chmurze Azure Government, Azure albo chmura lub Microsoft Azure (Niemcy) można używać usługi Azure Cache?
Tak, pamięci podręcznej Azure redis Cache jest dostępna w chmurze Azure Government, w chmurze platformy Azure (Chiny) i Microsoft Azure (Niemcy). Adresy URL do uzyskiwania dostępu i zarządzania usługi Azure Cache dla pamięci podręcznej Redis różnią się w tych chmurach w porównaniu z chmury publicznej platformy Azure. 

| Chmura   | Sufiks DNS dla usługi Redis            |
|---------|---------------------------------|
| Public  | *.redis.cache.windows.net       |
| Rząd USA  | *.redis.cache.usgovcloudapi.net |
| Niemcy | *.redis.cache.cloudapi.de       |
| Chiny   | *.redis.cache.chinacloudapi.cn  |

Aby uzyskać więcej informacji na temat uwagi dotyczące korzystania z usługi Azure Cache dla pamięci podręcznej Redis przy użyciu innych chmur zobacz poniższe linki.

- [Bazy danych platformy Azure dla instytucji rządowych — pamięć podręczna systemu Azure dla usługi Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Chmura platformy Azure (Chiny) — pamięć podręczna systemu Azure dla usługi Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)

Aby uzyskać informacje na temat korzystania z usługi Azure Cache dla pamięci podręcznej Redis przy użyciu programu PowerShell w chmurze Azure Government, w chmurze platformy Azure (Chiny) i Microsoft Azure (Niemcy), zobacz [jak połączyć się z innych chmur - pamięci podręcznej Azure redis Cache środowiska PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Co zrobić opcje konfiguracji StackExchange.Redis
StackExchange.Redis ma wiele opcji. Ta sekcja zawiera informacje o niektórych typowych ustawień. Aby uzyskać szczegółowe informacje o opcjach StackExchange.Redis, zobacz [konfiguracji StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Opis | Zalecenie |
| --- | --- | --- |
| AbortOnConnectFail |Jeśli ustawiono wartość true, połączenie nie nawiąże połączenie po awarii sieci. |Ustaw na wartość false i pozwól StackExchange.Redis automatycznie ponownie połączenie. |
| ConnectRetry |Liczba powtórzeń prób nawiązania połączenia podczas początkowego połączenia. |Zobacz poniższe informacje o orientacji. |
| ConnectTimeout |Ms na przekroczenie limitu czasu operacji dotyczących połączenia. |Zobacz poniższe informacje o orientacji. |

Zazwyczaj domyślne wartości klienta są wystarczające. Można dostosować opcje, w oparciu o obciążenie.

* **Ponowne próby**
  * ConnectRetry i ConnectTimeout ogólną wytyczną jest szybkiego kończenia działania i spróbuj ponownie. Niniejsze wskazówki opiera się na obciążenie i ilość czasu na średni przyjmuje dla klienta, aby wydać polecenie redis Cache i otrzymać odpowiedź.
  * Pozwól StackExchange.Redis automatycznie ponownie zamiast sprawdzania stanu połączenia i ponowne łączenie samodzielnie. **Unikaj używania właściwości ConnectionMultiplexer.IsConnected**.
  * Snowballing — czasami może wystąpić problem, w której są ponawianie próby i ponowne próby snowball i nigdy nie odzyska sprawność. Jeśli snowballing problem wystąpi, należy rozważyć użycie algorytmu ponawiania wykładniczego wycofywania, zgodnie z opisem w [ogólne wskazówki dotyczące ponawiania prób](../best-practices-retry-general.md) opublikowane przez grupę Microsoft Patterns i praktyk.
* **Wartości limitu czasu**
  * Należy wziąć pod uwagę obciążenie i odpowiednio ustawić wartości. Jeśli przechowujesz dużych wartościach równa wyższa wartość limitu czasu.
  * Ustaw `AbortOnConnectFail` połączyć StackExchange.Redis FAŁSZ i pozwala ponownie dla Ciebie.
  * Pojedyncze wystąpienie ConnectionMultiplexer na użytek aplikacji. LazyConnection można użyć do utworzenia pojedynczego wystąpienia, który jest zwracany przez właściwość połączenia, jak pokazano na [nawiązywanie połączenia z pamięci podręcznej przy użyciu klasy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ustaw `ConnectionMultiplexer.ClientName` właściwości aplikacji unikatową nazwę dla celów diagnostycznych.
  * Używanych jest wiele `ConnectionMultiplexer` wystąpień dla niestandardowych obciążeń.
      * W przypadku zmiennych obciążeń w aplikacji, możesz wykonać ten model. Na przykład:
      * Może mieć jedną multiplekser radzenia sobie z dużych kluczy.
      * Może mieć jedną multiplekser radzenia sobie z małych kluczy.
      * Można ustawić różne wartości limitów czasu połączenia i Logika ponawiania próby dla każdego ConnectionMultiplexer, którego używasz.
      * Ustaw `ClientName` właściwości na każdym multiplekser ułatwiające wykonywanie diagnostyki.
      * Niniejsze wskazówki może prowadzić do więcej usprawnione czas oczekiwania na `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Jakie usługi Azure Cache dla klientów usługi Redis można używać?
Jedną z najważniejszych funkcji usługi Redis zakłada, że wielu klientów, obsługa wielu języków programowania różne. Aby uzyskać bieżącą listę klientów, zobacz [klienci Redis](https://redis.io/clients). Samouczki, które obejmują kilka różnych językach i klientów, zobacz [jak używać usługi Azure Cache dla pamięci podręcznej Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i jej element równorzędny artykułów w tabeli treści.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Dla usługi Redis jest lokalnym emulator dla usługi Azure Cache?
Nie jest brak emulator dla pamięci podręcznej Azure redis cache, ale można uruchomić wersji MSOpenTech redis-server.exe z [Redis narzędzia wiersza polecenia](https://github.com/MSOpenTech/redis/releases/) na lokalnym komputerze, a następnie nawiązać z nim, aby uzyskać podobne możliwości do lokalnej pamięci podręcznej Emulator, jak pokazano w poniższym przykładzie:

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


Opcjonalnie można skonfigurować [redis.conf](https://redis.io/topics/config) pliku, aby lepiej dopasować [domyślne ustawienia pamięci podręcznej](cache-configure.md#default-redis-server-configuration) dla pamięci podręcznej online platformy Azure dla usługi Redis, w razie potrzeby.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak uruchomić polecenia Redis
Można użyć dowolnego polecenia wymienione pod [polecenia Redis](https://redis.io/commands#) z wyjątkiem polecenia wymienione pod [polecenia nie są obsługiwane w usłudze Azure Cache, dla usługi Redis Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Istnieje kilka opcji, aby uruchamiać polecenia usługi Redis.

* Jeśli pamięć podręczna warstwy standardowa lub Premium, możesz uruchamiać polecenia pamięci podręcznej Redis przy użyciu [konsolę pamięci podręcznej Redis](cache-configure.md#redis-console). Konsola pamięci podręcznej Redis zapewnia bezpieczny sposób do uruchamiania poleceń pamięci podręcznej Redis w portalu Azure.
* Można również użyć narzędzia wiersza polecenia usługi Redis. Aby korzystać z nich, wykonaj następujące czynności:
* Pobierz [Redis narzędzia wiersza polecenia](https://github.com/MSOpenTech/redis/releases/).
* Nawiązać połączenie przy użyciu pamięci podręcznej `redis-cli.exe`. Przekazuje się w pamięci podręcznej punktu końcowego za pomocą przełącznika -h i klucz przy użyciu jak pokazano w poniższym przykładzie:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Narzędzia wiersza polecenia usługi Redis nie działają z portem SSL, ale można użyć narzędzia, takie jak `stunnel` nawiązania bezpiecznego połączenia narzędzi z portem SSL, postępując zgodnie z instrukcjami w [ogłoszenie dostawca stanu sesji ASP.NET dla usługi Redis (wersja zapoznawcza) Wersja](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) wpis w blogu.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Dlaczego nie ma MSDN odwołanie do biblioteki klas np. niektóre z innymi usługami platformy Azure w pamięci podręcznej Redis Azure?
Usługa Microsoft Azure Cache pamięci podręcznej Redis jest oparta na popularnej open source pamięć podręczna systemu Azure dla usługi Redis i może zostać oceniony przez cały szereg [klienci Redis](https://redis.io/clients) dla wielu języków programowania. Każdy klient ma własny interfejs API, który sprawia, że wywołania do pamięci podręcznej Azure wystąpienie pamięci podręcznej Redis za pomocą [polecenia Redis](https://redis.io/commands).

Ponieważ różni się każdego klienta, istnieje nie jeden scentralizowane odwołań do klas w witrynie MSDN, a każdy klient zachowuje własną dokumentację referencyjną. Oprócz dokumentacją referencyjną istnieje kilka samouczków, w którym pokazano, jak rozpocząć pracę z usługą Azure Cache dla pamięci podręcznej Redis przy użyciu różnych języków i pamięci podręcznej klientów. Aby uzyskać dostęp do tych samouczków, zobacz [jak używać usługi Azure Cache dla pamięci podręcznej Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i jej element równorzędny artykułów w tabeli treści.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Czy można używać usługi Azure Cache dla usługi Redis jako pamięci podręcznej sesji PHP?
Tak, aby użyć pamięć podręczna systemu Azure dla usługi Redis jako pamięci podręcznej sesji języka PHP, określ parametry połączenia do wystąpienia pamięci podręcznej Redis w pamięci podręcznej Azure `session.save_path`.

> [!IMPORTANT]
> Korzystając z usługi Azure Cache dla usługi Redis jako pamięci podręcznej sesji języka PHP, należy najpierw adresu URL kodowanie klucz zabezpieczeń używany do łączenia z pamięci podręcznej, jak pokazano w poniższym przykładzie:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Jeśli klucz nie jest zakodowany w adresie URL, może pojawić się wyjątek z komunikatem, takich jak: `Failed to parse session.save_path`
>
>

Aby uzyskać więcej informacji na temat przy użyciu usługi Azure Cache dla usługi Redis jako pamięci podręcznej sesji PHP za pomocą klienta PhpRedis zobacz [obsługi sesji PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co to są bazy danych Redis?

Bazy danych usługi redis są logicznie rozdzielić dane w ramach tego samego wystąpienia usługi Redis. Pamięć podręczna jest współużytkowana przez wszystkie bazy danych i pamięci Rzeczywiste użycie określonej bazy danych zależy od kluczy/wartości przechowywane w tej bazie danych. Na przykład pamięć podręczna C6 ma 53 GB pamięci. Możesz umieścić wszystkie 53 GB w jednej bazie danych lub Podziel między wieloma bazami danych. 

> [!NOTE]
> Korzystając z usługi Azure Cache w warstwie Premium dla usługi Redis z włączonym klastrowaniu, tylko bazy danych 0 jest dostępna. To ograniczenie jest wewnętrzna ograniczenie pamięci podręcznej Redis i nie jest określona dla pamięci podręcznej Azure redis Cache. Aby uzyskać więcej informacji, zobacz [należy wprowadzać żadnych zmian Moja aplikacja kliencka, aby użyć klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Kiedy należy włączyć port bez protokołu SSL do łączenia się z pamięci podręcznej Redis
Redis server nie obsługuje natywnie protokołu SSL, ale nie w pamięci podręcznej Azure redis Cache. Jeśli łączysz się pamięć podręczna systemu Azure dla usługi Redis i klient obsługuje protokół SSL, takich jak StackExchange.Redis, należy używać protokołu SSL.

>[!NOTE]
>Port bez protokołu SSL jest domyślnie wyłączona dla nowej usługi Azure Cache dla wystąpień usługi Redis. Jeśli Twój klient nie obsługuje protokołu SSL, a następnie należy włączyć port bez protokołu SSL, postępując zgodnie z instrukcjami w [portów](cache-configure.md#access-ports) części [Konfigurowanie pamięci podręcznej w pamięci podręcznej Azure dla usługi Redis](cache-configure.md) artykułu.
>
>

Redis narzędzi, takich jak `redis-cli` nie działają z portem SSL, ale można użyć narzędzia, takie jak `stunnel` nawiązania bezpiecznego połączenia narzędzi z portem SSL, postępując zgodnie z instrukcjami w [ogłoszenie dostawca stanu sesji ASP.NET dla usługi Redis Wersja zapoznawcza](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) wpis w blogu.

Aby uzyskać instrukcje dotyczące pobierania narzędzia pamięci podręcznej Redis, zobacz [jak uruchomić polecenia Redis?](#cache-commands) sekcji.

### <a name="what-are-some-production-best-practices"></a>Jakie są najważniejsze wskazówki produkcji?
* [Najlepsze rozwiązania StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfiguracja i pojęcia](#configuration-and-concepts)
* [Testowanie wydajności](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Najlepsze rozwiązania StackExchange.Redis
* Ustaw `AbortConnect` na wartość false, a następnie umożliwił ConnectionMultiplexer automatycznie ponownie połączenie. [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Ponowne użycie ConnectionMultiplexer — nie należy tworzyć nowego filtru dla każdego żądania. `Lazy<ConnectionMultiplexer>` Wzorzec [pokazane tutaj](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) jest zalecane.
* Redis Cache działa najlepiej z mniejszej wartości, dlatego należy rozważyć siekanie większe danych na wiele kluczy. W [tej dyskusji Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb jest uznawany za duży. Odczyt [w tym artykule](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) dotyczącej problemu przykład, która może być spowodowany przez dużych wartościach.
* Konfigurowanie usługi [ustawienia puli wątków](#important-details-about-threadpool-growth) w celu uniknięcia przekroczenia limitu czasu.
* Użyj co najmniej connectTimeout domyślnej równej 5 sekund. Ten interwał spowodowałoby to nadanie StackExchange.Redis wystarczająco dużo czasu, aby ponownie ustanowić połączenia, w przypadku blip sieci.
* Należy pamiętać o poprawa wydajności przy użyciu różnych operacji, które są uruchomione. Na przykład `KEYS` polecenia jest operacją O(n) i należy ich unikać. [Witryny redis.io](https://redis.io/commands/) znajdują się wokół złożoności czasu dla każdej operacji, która go obsługuje. Kliknij każdy polecenie, aby wyświetlić złożoności dla każdej operacji.

#### <a name="configuration-and-concepts"></a>Konfiguracja i pojęcia
* Dla systemów produkcyjnych należy używać warstwy standardowa i Premium. Warstwa podstawowa to system jeden węzeł nie replikacji danych i objęta umową SLA. Ponadto można użyć co najmniej C1 pamięci podręcznej. Pamięci podręczne C0 są zwykle używane w scenariuszach proste tworzenie i testowanie.
* Należy pamiętać, że magazyn Redis jest **w pamięci** magazynu danych. Odczyt [w tym artykule](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) tak, że masz świadomość scenariusze, w których może wystąpić utrata danych.
* Tworzenie systemu w taki sposób, że może obsługiwać połączenia blips [ze względu na instalowaniu poprawek i trybu failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testowanie wydajności
* Uruchom przy użyciu `redis-benchmark.exe` aby można było uzyskać pewne pojęcie przepustowości możliwe przed zapisem wydajności własne testy. Ponieważ `redis-benchmark` nie obsługuje protokołu SSL, należy najpierw [włączyć port bez protokołu SSL za pośrednictwem witryny Azure portal](cache-configure.md#access-ports) przed uruchomieniem testu. Aby uzyskać przykłady, zobacz [jak testu wydajności i przetestować wydajność przepełnieniu pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Używane do testowania maszyny Wirtualnej klienta musi należeć do tego samego regionu pamięci podręcznej Azure dla wystąpienia usługi Redis.
* Zalecamy używanie serię maszyn wirtualnych Dv2 dla klienta, zgodnie z ich lepsze sprzętu i powinien zapewnić najlepsze rezultaty.
* Upewnij się, sieci maszyny Wirtualnej możesz wybrać klienta ma co najmniej taką ilość obliczeń i testowanie funkcji przepustowość pamięci podręcznej.
* Włącz VRSS na komputerze klienckim, jeśli użytkownik pracuje na Windows. [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://technet.microsoft.com/library/dn383582.aspx).
* Wystąpienia pamięci podręcznej Redis w warstwie Premium mają lepiej sieci opóźnienia i przepustowości, ponieważ działają na sprzęcie lepsze dla procesora CPU i sieci.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jakie są niektóre zagadnienia podczas korzystania z Typowe polecenia Redis?
* Nie należy uruchamiać niektórych poleceń pamięci podręcznej Redis, które zająć dużo czasu, aby zakończyć bez zrozumienia wpływu tych poleceń.
  * Na przykład nie uruchamiaj [KLUCZE](https://redis.io/commands/keys) polecenia w środowisku produkcyjnym, ponieważ trwa długo w zależności od liczby kluczy. Redis jest serwerem jednowątkowe i przetwarza polecenia jednego naraz. W przypadku innych poleceń wydane po KLUCZE są nie będą przetwarzane do czasu pamięci podręcznej Redis przetwarza polecenia KLUCZE. [Witryny redis.io](https://redis.io/commands/) znajdują się wokół złożoności czasu dla każdej operacji, która go obsługuje. Kliknij każdy polecenie, aby wyświetlić złożoności dla każdej operacji.
* Rozmiary kluczy — należy użyć kluczy/wartości małych lub dużych kluczy/wartości? Ogólnie rzecz biorąc zależy od scenariusza. Dany scenariusz wymaga kluczy, można dopasować ConnectionTimeout i ponów próbę wykonania wartości i Dostosuj swoją logikę ponawiania prób. Z perspektywy serwera Redis mniejsze wartości są przestrzegane mają lepszą wydajność.
* Te zagadnienia nie oznaczają, że nie można przechowywać większe wartości w pamięci podręcznej Redis; należy pamiętać o następujących kwestiach. Opóźnienia będzie wyższa. Jeśli masz jeden zestaw danych, który jest większy lub taki, który jest mniejszy, można użyć wielu wystąpień ConnectionMultiplexer, każdego skonfigurowanego z innym zestawem wartości limitu czasu, a następnie spróbuj ponownie, zgodnie z opisem w poprzedniej [co zrobić StackExchange.Redis Opcje konfiguracji](#cache-configuration) sekcji.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak testu wydajności i przetestować wydajność przepełnieniu pamięci podręcznej?
* [Włącz diagnostykę pamięci podręcznej](cache-how-to-monitor.md#enable-cache-diagnostics), aby móc [monitorować](cache-how-to-monitor.md) jej kondycję. Możesz wyświetlać metryki w witrynie Azure Portal oraz [pobierać i przeglądać](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) je przy użyciu wybranych przez siebie narzędzi.
* Umożliwia redis benchmark.exe test obciążeniowy serwera Redis.
* Upewnij się, że obciążenia, testowanie klienta i usługi Azure Cache pod kątem pamięci podręcznej Redis znajdują się w tym samym regionie.
* Użyj pamięci podręcznej redis-cli.exe i monitorować za pomocą polecenia informacji o pamięci podręcznej.
* Jeśli Twoje obciążenia powoduje fragmentację dużą ilość pamięci, należy skalowanie w górę na większy rozmiar pamięci podręcznej.
* Aby uzyskać instrukcje dotyczące pobierania narzędzia pamięci podręcznej Redis, zobacz [jak uruchomić polecenia Redis?](#cache-commands) sekcji.

Poniższe polecenia zawierają z przykładem użycia benchmark.exe pamięci podręcznej redis. Aby uzyskać dokładne wyniki uruchom następujące polecenia z maszyny Wirtualnej, w tym samym regionie jako pamięci podręcznej.

* Za pomocą 1 ładunku k żądań potokowe zestawu testów

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Żądań GET potokowe testu przy użyciu 1 ładunku k.
  UWAGA: Uruchom test zestaw powyżej pierwszego w celu wypełnienia pamięci podręcznej

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Ważne informacje dotyczące rozwoju puli wątków
CLR ThreadPool ma dwa rodzaje wątków — "Procesu roboczego" i "Portu zakończenia We/Wy%N" (alias portu) wątków.

* Wątki robocze są używane w przypadku elementów, takich jak przetwarzanie `Task.Run(…)`, lub `ThreadPool.QueueUserWorkItem(…)` metody. Podczas pracy musi zostać przeprowadzona w wątku tła, te wątki są również używane przez różne składniki w CLR.
* Wątki portu są używane w sytuacji asynchronicznych operacji We/Wy (np. odczytywać sieci).

Puli wątków zapewnia nowe wątki robocze lub wątki zakończenia operacji We/Wy na żądanie (bez żadnych ograniczania) aż do napotkania ustawienie "Minimalny" dla każdego typu wątku. Minimalna liczba wątków domyślnie do liczby procesorów w systemie.

Gdy liczba istniejących wątkach (zajęty) uderza "minimalny" Liczba wątków, puli wątków będzie ograniczać szybkość jaką go wprowadza nowe wątki na jeden wątek na każdym 500 milisekund. Zazwyczaj Jeśli system serii pracy wymagające wątków portu, go będzie przetwarzać tę pracę bardzo szybko. Jednak jeśli serii pracy jest większa niż skonfigurowane ustawienie "Minimum", będą pewne opóźnienie podczas przetwarzania część obciążenia pracą, zgodnie z puli wątków czeka na jedno z następujących czynności do wykonania.

1. Przetwarzanie zadań zostanie zwolniony istniejącego wątku.
2. Nie istniejącego wątku staje się bezpłatnie 500 MS, dzięki czemu jest tworzony nowy wątek.

Zasadniczo oznacza to, gdy liczba zajętych wątków jest większy niż minimalny wątków, prawdopodobnie realizujesz opóźnienie 500 MS przed ruch sieciowy jest przetwarzany przez aplikację. Ponadto należy zauważyć, że gdy istniejącego wątku pozostanie bezczynny przez czas dłuższy niż 15 sekund (w oparciu I Zapamiętaj), zostanie on wyczyszczone, a powtórzyć cykl rozwoju i kurczenia.

Jeśli spojrzymy na przykład komunikat o błędzie z StackExchange.Redis (Tworzenie 1.0.450 lub nowszej), zobaczysz, że teraz Wyświetla statystyki puli wątków (zobacz poniżej szczegóły portu i proces ROBOCZY).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

W poprzednim przykładzie widać, że dla portu wątku są 6 zajęte wątki, a system jest skonfigurowany do zezwalania 4 minimalną liczbę wątków. W takim przypadku klient będzie prawdopodobnie przejrzane dwóch opóźnienie 500 ms ponieważ 6 > 4.

Należy zwrócić uwagę na to, czy StackExchange.Redis mogą trafić przekroczeń limitu czasu, jeśli wzrostu wątków portu lub proces ROBOCZY pobiera ograniczenia.

### <a name="recommendation"></a>Zalecenie
Biorąc pod uwagę te informacje, zdecydowanie zaleca się że klientów równa wartości minimalnej konfiguracji portu i proces ROBOCZY wątków coś, co jest większa niż wartość domyślna. Nie możemy przyznać opracowanie wskazówki dotyczące co ta wartość powinna być, ponieważ wartość prawej strony w jednej aplikacji będzie zbyt wysoka/niska dla innej aplikacji. To ustawienie może także wpłynąć na wydajność innych części aplikacji złożonych, dzięki czemu każdy klient wymaga precyzyjnie dostosować to ustawienie, aby zgodnie z określonymi wymaganiami. Dobrym miejscem począwszy od 200 lub 300, a następnie przetestuj i dostosować zgodnie z potrzebami.

Jak można skonfigurować tego ustawienia:

* W programie ASP.NET: Użyj ["minIoThreads" lub "minWorkerThreads" Ustawienie konfiguracji] [ "minIoThreads" configuration setting] w obszarze `<processModel>` element konfiguracji w pliku web.config. Jeśli używasz w usłudze Azure WebSites, to ustawienie nie jest uwidaczniany za pomocą opcji konfiguracji. Jednak nadal można skonfigurować tego ustawienia, programowo (zobacz poniżej), ze swojej metody Application_Start w global.asax.cs.

  > [!NOTE] 
  > Wartość określona w tym elemencie konfiguracji jest *za rdzeń* ustawienie. Na przykład, jeśli korzystasz z 4-rdzeniową maszyną i chcesz z ustawieniem minIOThreads to 200 w czasie wykonywania, można użyć `<processModel minIoThreads="50"/>`.
  >

* Poza programem ASP.NET a plik global.asax witryn sieci Web platformy Azure, użyj [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) API.

  > [!NOTE]
  > Wartość określoną przez ten interfejs API jest ustawienie globalne wpływu na cały element AppDomain. Jeśli masz 4-rdzeniową maszyną i chcesz ustawić minWorkerThreads i minIOThreads 50 dla każdego procesora CPU w czasie wykonywania, należy użyć ThreadPool.SetMinThreads (200, 200).

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Włącz serwer GC w celu uzyskania większej przepustowości na komputerze klienckim, używając StackExchange.Redis
Włączenie serwera GC może zoptymalizować klienta i zapewnia większą wydajność i przepływność, korzystając z StackExchange.Redis. Aby uzyskać więcej informacji na serwerze odzyskiwania pamięci i jak go włączyć zobacz następujące artykuły:

* [Aby włączyć serwer GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Podstawy dotyczące odzyskiwania pamięci](/dotnet/standard/garbage-collection/fundamentals)
* [Odzyskiwanie pamięci i wydajność](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Zagadnienia dotyczące wydajności w całym połączeń

Każda warstwa cenowa ma różne limity dla połączeń klienta, pamięci i przepustowość. Podczas każdego rozmiaru pamięci podręcznej umożliwia *do* określoną liczbę połączeń, każde połączenie z magazynem Redis obciążenie jest skojarzony z nim. Przykładem takiego obciążenie byłoby użycie Procesora i pamięci, w wyniku szyfrowania TLS/SSL. Limit maksymalnej liczby połączeń dla rozmiaru pamięć podręczna zakłada mniej obciążonych pamięci podręcznej. Jeśli ładowanie z połączenia obciążenie *oraz* obciążenia z operacjami klienta przekracza wydajność systemu, pamięci podręcznej mogą wystąpić problemy pojemności, nawet jeśli nie przekroczono limit połączeń dla bieżącego rozmiaru pamięci podręcznej.

Aby uzyskać więcej informacji na temat limitów różnych połączeń dla każdej warstwy, zobacz [pamięci podręcznej Azure Redis cennik](https://azure.microsoft.com/pricing/details/cache/). Aby uzyskać więcej informacji na temat połączeń i innych konfiguracji domyślnej, zobacz [Redis domyślną konfigurację serwera](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak monitorować kondycję i wydajność przepełnieniu pamięci podręcznej?
Usługa Microsoft Azure Cache dla wystąpień usługi Redis można monitorować w [witryny Azure portal](https://portal.azure.com). Można wyświetlić metryki, przypinać wykresy metryk do tablicy startowej, dostosować zakres dat i godzin, wykresy monitorowania, Dodaj i Usuń metryki z wykresów i ustawić alerty, gdy są spełnione określone warunki. Aby uzyskać więcej informacji, zobacz [monitora platformy Azure z pamięci podręcznej dla pamięci podręcznej Redis](cache-how-to-monitor.md).

Pamięć podręczna Azure dla usługi Redis **menu zasobów** zawiera także kilka narzędzi do monitorowania i rozwiązywania problemów pamięci podręczne.

* **Diagnozowanie i rozwiązywanie problemów** informacje dotyczące najczęściej występujących problemów i strategie dotyczące rozwiązywania tych problemów.
* **Usługa Resource health** monitoruje zasób i informuje, czy działa on zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat usługi kondycji zasobów platformy Azure, zobacz [Przegląd kondycji zasobów platformy Azure](../resource-health/resource-health-overview.md).
* **Nowe żądanie pomocy technicznej** udostępnia opcje, aby otworzyć żądanie obsługi dotyczące pamięci podręcznej.

Te narzędzia umożliwiają monitorowanie kondycji pamięci podręcznej Azure dla wystąpienia usługi Redis i ułatwia zarządzanie aplikacjami buforowania. Aby uzyskać więcej informacji, zobacz sekcję "Ustawienia dotyczące rozwiązywania problemów i pomocy technicznej" [sposób konfigurowania usługi Azure Cache dla pamięci podręcznej Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Dlaczego widzę przekroczeń limitu czasu?
Limity czasu się tak zdarzyć w kliencie, używanego do komunikacji z magazynem Redis. Gdy polecenie jest wysyłane do serwera Redis, polecenia są kolejkowane i serwera Redis po pewnym czasie przejmuje polecenia i wykonuje ten. Jednak klient może przekroczono limit czasu podczas tego procesu i jeśli wyjątek nie jest wywoływane po stronie wywoływania. Aby uzyskać więcej informacji na temat rozwiązywania problemów limitu czasu, zobacz [rozwiązywania problemów po stronie klienta](cache-how-to-troubleshoot.md#client-side-troubleshooting) i [wyjątków przekroczenia limitu czasu StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Dlaczego mój klient został rozłączony z pamięci podręcznej
Poniżej przedstawiono niektóre z najczęstszych przyczyn Rozłącz pamięci podręcznej.

* Powoduje, że po stronie klienta
  * Aplikacja kliencka była ponownego wdrażania.
  * Aplikacja kliencka wykonać operacji skalowania.
    * W przypadku usług w chmurze lub aplikacji sieci Web może to być spowodowane automatycznego skalowania.
  * Zmienić warstwy sieci po stronie klienta.
  * Błędy przejściowe wystąpił w kliencie lub w węzłach sieci między klientem a serwerem.
  * Limity wartości progowych przepustowości były dostępne.
  * Procesor CPU powiązane operacje trwało za długo.
* Powoduje, że po stronie serwera
  * Na oferty standard pamięci podręcznej pamięć podręczna Azure dla usługi Redis zainicjować trybu failover z węzła podstawowego do węzła pomocniczego.
  * Azure była poprawek wystąpienia wdrożonym pamięci podręcznej
    * Może to być aktualizacje serwera Redis lub ogólna Konserwacja maszyn wirtualnych.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Która oferta pamięci podręcznej systemu Azure jest dla mnie najlepsza?
> [!IMPORTANT]
> Zgodnie z ostatniego roku [ogłoszenie](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), usługa Azure Managed Cache Service i pamięć podręczna oparta na roli Azure **został wycofany** 30 listopada 2016 r. Nasze zalecenie to użycie [pamięci podręcznej Redis Azure](https://azure.microsoft.com/services/cache/). Aby uzyskać informacje na temat migracji, zobacz [migracja z usługi Managed Cache Service do usługi Azure Cache dla usługi Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Pamięć podręczna systemu Azure dla usługi Redis jest ogólnie dostępna w rozmiarach do 53 GB i ma dostępność na poziomie 99,9%. Nowy [w warstwie premium](cache-premium-tier-intro.md) oferuje rozmiarach do 530 GB i pomoc techniczna dla klastra sieć Wirtualną i trwałości, z umową SLA 99,9%.

Pamięć podręczna systemu Azure dla usługi Redis zapewnia klientom możliwość korzystania z bezpiecznej, dedykowanej pamięci podręcznej Azure dla usługi Redis, zarządzanej przez firmę Microsoft. Ta oferta umożliwia skorzystanie z do rozbudowanego zestawu funkcji i ekosystemu udostępnianego udostępnianych przez Redis oraz niezawodnych oraz hostingu i monitorowania od firmy Microsoft.

W przeciwieństwie do tradycyjnych pamięci podręczne, które dotyczą tylko przy użyciu pary klucz wartość usługi Redis jest popularnych dla swoich wysoce wydajne typów danych. Również redis obsługuje uruchamianie niepodzielne operacje na tych typów, takich jak dołączanie do ciąg. Zwiększanie wartości skrótu; Wypychanie do listy; Przetwarzanie zestawu części wspólnych, Unii i różnica; lub elementu członkowskiego o najwyższej klasyfikacji w zestawu posortowanego. Inne funkcje obejmują obsługę transakcji i publikowania/subskrybowania, Lua, skryptów, klucze o ograniczonym czasie wygaśnięcia, ustawienia konfiguracji, że magazyn Redis zachowują się podobnie tradycyjnych pamięci podręcznej.

Innym kluczowym aspektem powodzenia Redis jest ekosystemu dobrej kondycji, aktywny "open source" zbudowane wokół niej. Znajduje to odzwierciedlenie w różnorodnych klientów Redis są dostępne w wielu językach. Ten ekosystem i szerokiej gamy klientów umożliwiają Azure pamięci podręcznej Redis do użycia w przypadku niemal dowolnego obciążenia, jakie można utworzyć w systemie Azure.

Aby uzyskać więcej informacji na temat rozpoczynania pracy z pamięcią podręczną Azure dla usługi Redis, zobacz [sposobu użycia usługi Azure Cache dla usługi Redis](cache-dotnet-how-to-use-azure-redis-cache.md) i [usługi Azure Cache dokumentacja pamięci podręcznej Redis](index.md).

### <a name="managed-cache-service"></a>Usługa Managed Cache service
[Zarządzana pamięć podręczna usługi została wycofana 30 listopada 2016 r.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić zarchiwizowana dokumentacja, zobacz [zarchiwizowana dokumentacja usługi pamięć podręczna zarządzane](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Pamięć podręczna oparta na roli
[Pamięć podręczna oparta na roli została wycofana 30 listopada 2016 r.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić zarchiwizowana dokumentacja, zobacz [zarchiwizowana dokumentacja usługi pamięć podręczna oparta na roli](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
