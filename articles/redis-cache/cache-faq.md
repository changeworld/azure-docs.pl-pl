---
title: Pamięć podręczna Azure Redis — często zadawane pytania | Dokumentacja firmy Microsoft
description: Dowiedz się odpowiedzi na często zadawane pytania, wzorców i najlepszych rozwiązań dla pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: 66340e690e5a6ac3e440b8b4d26e1a8b2abab266
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-redis-cache-faq"></a>Azure Redis Cache — często zadawane pytania
Dowiedz się odpowiedzi na często zadawane pytania, wzorców i najlepszych rozwiązań dla pamięci podręcznej Redis Azure.

## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli mojego pytania nie ma odpowiedzi w tym miejscu?
Jeśli Twoje pytanie nie ma na liście w tym miejscu, Daj nam znać, a pomożemy Ci znaleźć odpowiedź.

* Możesz wysłać zapytanie do komentarze na końcu tego — często zadawane pytania i kontaktowaniu się z team pamięć podręczna Azure i innymi członkami społeczności informacje w tym artykule.
* Aby uzyskać dostęp do większej liczby osób, należy wysłać zapytanie na [Forum MSDN pamięci podręcznej Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) i kontaktowaniu się z team pamięć podręczna Azure i innych członków społeczności.
* Jeśli chcesz wykonać żądania funkcji, można przesłać żądania i pomysłami, które [głos użytkownika pamięci podręcznej Redis Azure](https://feedback.azure.com/forums/169382-cache).
* Możesz również wysłać wiadomość e-mail do nas na [opinii zewnętrznych pamięci podręcznej Azure](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Podstawy pamięci podręcznej Redis Azure
Często zadawane pytania w tej sekcji opisano niektóre podstawowe informacje o pamięci podręcznej Redis Azure.

* [Co to jest usługa Azure Redis Cache?](#what-is-azure-redis-cache)
* [Jak można zacząć korzystać z pamięci podręcznej Redis Azure?](#how-can-i-get-started-with-azure-redis-cache)

Poniższe często zadawane pytania dotyczą podstawowych pojęć i pytania dotyczące pamięci podręcznej Redis Azure i są odbierane w innej sekcji często zadawane pytania.

* [Której oferty i jakiego rozmiaru usługi Redis Cache mam użyć?](#what-redis-cache-offering-and-size-should-i-use)
* [Jakie klientów pamięci podręcznej Redis można użyć?](#what-redis-cache-clients-can-i-use)
* [Czy istnieje lokalne emulatora dla pamięci podręcznej Redis Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Jak monitorować kondycję i wydajność Moje pamięci podręcznej](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Często zadawane pytania dotyczące planowania
* [Której oferty i jakiego rozmiaru usługi Redis Cache mam użyć?](#what-redis-cache-offering-and-size-should-i-use)
* [Wydajność pamięci podręcznej Redis Azure](#azure-redis-cache-performance)
* [W jakim regionie należy znaleźć Moje pamięci podręcznej?](#in-what-region-should-i-locate-my-cache)
* [Jak m rozliczane dla pamięci podręcznej Redis Azure?](#how-am-i-billed-for-azure-redis-cache)
* [Z chmury Azure dla instytucji rządowych, chmury Azure Chin lub Microsoft Azure Niemcy można używać usługi pamięć podręczna Redis Azure?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Programowanie — często zadawane pytania
* [Co zrobić opcje konfiguracji w programie StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jakie klientów pamięci podręcznej Redis można użyć?](#what-redis-cache-clients-can-i-use)
* [Czy istnieje lokalne emulatora dla pamięci podręcznej Redis Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Jak uruchomić polecenia Redis?](#how-can-i-run-redis-commands)
* [Dlaczego nie pamięci podręcznej Redis Azure ma MSDN dotyczące biblioteki klas takich jak niektóre z innymi usługami platformy Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Pamięć podręczna Redis Azure można używać jako pamięci podręcznej sesji PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Co to są Redis baz danych?](#what-are-redis-databases)

## <a name="security-faqs"></a>Często zadawane pytania dotyczące zabezpieczeń
* [Jeśli włączenie portu bez protokołu SSL w celu nawiązania Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Produkcja — często zadawane pytania
* [Jakie są najlepsze rozwiązania produkcyjnym?](#what-are-some-production-best-practices)
* [Jakie są kwestii dotyczących za pomocą wspólnego poleceń Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak testu wydajności i testowania wydajności Moje pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Ważne informacje dotyczące wzrostu puli wątków](#important-details-about-threadpool-growth)
* [Włącz serwer wykazu Globalnego uzyskać więcej przepływności na kliencie, używając programie StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Zagadnienia dotyczące wydajności wokół połączeń](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorowanie i rozwiązywanie problemów — często zadawane pytania
Często zadawane pytania w tej sekcji opisano typowe monitorowania i rozwiązywania problemów pytania. Aby uzyskać więcej informacji dotyczących monitorowania i rozwiązywania problemów z wystąpienia pamięci podręcznej Redis Azure, zobacz [jak monitorować pamięć podręczna Redis Azure](cache-how-to-monitor.md) i [jak rozwiązywać problemy z pamięcią podręczną Redis Azure](cache-how-to-troubleshoot.md).

* [Jak monitorować kondycję i wydajność Moje pamięci podręcznej](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Dlaczego widzę limitów czasu?](#why-am-i-seeing-timeouts)
* [Dlaczego moja klient został odłączony od pamięci podręcznej?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Wcześniejsze oferty pamięci podręcznej często zadawane pytania
* [Które oferty Azure w pamięci podręcznej jest dla mnie odpowiednia?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>Co to jest usługa Azure Redis Cache?
Pamięć podręczna Redis Azure jest oparta na popularnych open source [pamięci podręcznej Redis](http://redis.io). Go umożliwia dostęp do bezpiecznego, dedykowanej pamięci podręcznej Redis, zarządzane przez firmę Microsoft i jest dostępny z poziomu dowolnej aplikacji w obrębie platformy Azure. Aby uzyskać bardziej szczegółowe informacje, zobacz [pamięć podręczna Redis Azure](https://azure.microsoft.com/services/cache/) stronę produktu w witrynie Azure.com.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Jak można zacząć korzystać z pamięci podręcznej Redis Azure?
Istnieje kilka metod, które możesz rozpocząć pracę z pamięci podręcznej Redis Azure.

* Można wyewidencjonować jedną z naszymi samouczkami, aby uzyskać [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), i [Python](cache-python-get-started.md).
* Możesz obserwować [jak tworzenie wydajnych aplikacji przy użyciu Microsoft pamięci podręcznej Redis Azure](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Możesz sprawdzić się w dokumentacji klienta dla klientów, którzy język tworzenia projektu, aby zobaczyć, jak używać pamięci podręcznej Redis. Istnieje wielu klientów pamięci podręcznej Redis, które mogą być używane z pamięci podręcznej Redis Azure. Lista klientów pamięci podręcznej Redis, zobacz [ http://redis.io/clients ](http://redis.io/clients).

Jeśli nie masz jeszcze konta platformy Azure, możesz:

* [Utworzyć bezpłatne konto platformy Azure ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Otrzymasz środki, które możesz wykorzystać do wypróbowania płatnych usług Azure. Nawet po wyczerpaniu tych środków możesz zachować konto i korzystać z bezpłatnych usług i funkcji platformy Azure.
* [Aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Jakie oferty i rozmiary pamięci podręcznej Redis mam wybrać?
Każde rozwiązanie pamięci podręcznej Redis Azure zapewnia różne poziomy **rozmiar**, **przepustowości**, **wysokiej dostępności**, i **SLA** opcje.

Poniżej przedstawiono informacje dotyczące wybierania oferty pamięci podręcznej.

* **Pamięć**: warstwy Basic i Standard oferują 250 MB – 53 GB. Warstwy Premium oferuje do 530 GB. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Redis pamięci podręcznej](https://azure.microsoft.com/pricing/details/cache/).
* **Wydajność sieci**: Jeśli masz obciążenia, która wymaga wysokiej przepływności, warstwy Premium oferuje więcej przepustowości w porównaniu do Standard lub Basic. Również w każdej warstwie większy rozmiar pamięci podręcznej ma więcej przepustowości z powodu podstawowej maszyny Wirtualnej, który jest hostem pamięci podręcznej. Zobacz [następujących tabeli](#cache-performance) Aby uzyskać więcej informacji.
* **Przepływność**: warstwy Premium oferuje maksymalną przepustowość dostępne. Jeśli pamięć podręczna serwera lub klienta osiągnie limity przepustowości, limity czasu po stronie klienta może zostać wyświetlony. Aby uzyskać więcej informacji zobacz poniższej tabeli.
* **Wysoka dostępność/SLA**: pamięć podręczna Redis Azure gwarantuje, że pamięć podręczna Standard/Premium jest dostępna co najmniej 99,9% czasu. Aby dowiedzieć się więcej na temat naszych umowy SLA, zobacz [cennik usługi Azure Redis pamięci podręcznej](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Umowa SLA obejmuje tylko łączności z punktami końcowymi pamięci podręcznej. Nie zapewnia ona ochrony przed utratą danych. Firma Microsoft zaleca, aby zwiększyć odporność przed utratą danych przy użyciu funkcji trwałości danych pamięci podręcznej Redis w warstwie Premium.
* **Trwałość danych redis**: warstwy Premium pozwala zachować dane pamięci podręcznej na koncie magazynu Azure. W pamięci podręcznej Basic/Standard wszystkie dane są przechowywane tylko w pamięci. W przypadku podstawowej problemów infrastruktury może być ryzyko utraty danych. Firma Microsoft zaleca, aby zwiększyć odporność przed utratą danych przy użyciu funkcji trwałości danych pamięci podręcznej Redis w warstwie Premium. Pamięć podręczna Redis Azure oferuje RDB i AOF (wkrótce) opcje w trwałość Redis. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trwałości dla podręczna Redis Azure Premium](cache-how-to-premium-persistence.md).
* **Klaster redis**: do utworzenia buforuje większych niż 53 GB i współdzielenie danych między wieloma węzłami Redis służy Redis klastrowania, która jest dostępna w warstwie Premium. Każdy węzeł składa się z pary pamięć podręczna podstawowy/repliki wysokiej dostępności. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie usługi klastrowania dla usługi Azure Redis Cache w warstwie Premium](cache-how-to-premium-clustering.md).
* **Zwiększonych zabezpieczeń i sieci izolacji**: wdrożenie wirtualne sieci Azure (VNET) zapewnia większe bezpieczeństwo i izolację dla pamięci podręcznej Redis Azure, jak również podsieci, zasad kontroli dostępu i inne funkcje, aby jeszcze bardziej ograniczyć dostęp. Więcej informacji można znaleźć w temacie [Konfigurowanie obsługi sieci wirtualnej dla usługi Azure Redis Cache w warstwie Premium](cache-how-to-premium-vnet.md).
* **Konfigurowanie pamięci podręcznej Redis**: W warstwach zarówno standardowa i Premium, możesz skonfigurować dla powiadomienia przestrzeni kluczy pamięci podręcznej Redis.
* **Maksymalna liczba połączeń klienckich**: warstwy Premium oferuje maksymalną liczbę klientów, które można łączyć z pamięci podręcznej Redis z większej liczby połączeń dla większy rozmiar pamięci podręcznej. Klaster nie zwiększa liczbę połączeń dostępnych dla klastra pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [cennik usługi pamięć podręczna Redis Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Dedykowane Core dla serwera Redis**: W warstwie Premium różnej wielkości pamięci podręcznej ma dedykowany core Redis. W warstwach Basic/Standard, rozmiar C1 lub nowszym mają dedykowanych core dla serwera Redis.
* **Redis jest jednowątkowy** mających więcej niż dwa rdzenie nie zapewnia dodatkowych korzyści za pośrednictwem o tylko dwa rdzenie, ale większych rozmiarów maszyn wirtualnych zwykle mają więcej przepustowości niż mniejsze. Jeśli klienta lub serwera pamięci podręcznej przez nią miejsce osiągnie limit przepustowości, zostanie wyświetlony przekroczeń limitu czasu po stronie klienta.
* **Ulepszenia wydajności**: pamięci podręcznych w warstwie Premium są wdrażane na sprzęcie, który ma szybkich procesorów, zapewniając większą wydajność w porównaniu do warstwy podstawowa lub standardowa. Pamięci podręczne warstwy Premium ma wyższą przepływność i dolnym opóźnienia.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Wydajność pamięci podręcznej Redis Azure
W poniższej tabeli przedstawiono wartości maksymalnej przepustowości obserwowanych podczas badania różnych rozmiarach Standard i Premium buforuje przy użyciu `redis-benchmark.exe` z maszyn wirtualnych Iaas względem punktu końcowego pamięci podręcznej Redis Azure. Przepływność SSL testu porównawczego redis jest używany z stunnel nawiązać połączenia z punktem końcowym usługi pamięć podręczna Redis Azure.

>[!NOTE] 
>Te wartości nie ma gwarancji i ma nie umowy SLA dla tych numerów, ale powinien być typowych. Powinny zostać załadowane przetestować aplikację do określenia rozmiaru pamięci podręcznej prawo aplikacji.
>Te liczby może ulec zmianie, okresowo ogłoszeniem nowszych wyników.
>

Z tej tabeli firma Microsoft może wykonywać Rysowanie następujących wniosków:

* Przepływność dla buforów, które mają taki sam rozmiar jest większa w warstwie Premium w porównaniu do warstwy standardowa. Na przykład o 6 GB pamięci podręcznej, przepływność P1 wynosi 180 000 RPS w porównaniu do 100 000 dla C3.
* Z pamięci podręcznej Redis klaster przepływności zwiększa liniowo zwiększyć liczbę fragmentów (węzłów) w klastrze. Na przykład po utworzeniu klastra P4 10 odłamków następnie dostępne przepływność wynosi 400 000 * 10 = 4 miliony RPS.
* Przepływność o większych rozmiarach klucza jest większa w warstwie Premium w porównaniu do warstwy standardowa.

| Warstwa cenowa | Rozmiar | Rdzenie procesora CPU | Dostępna przepustowość | Rozmiar wartości 1 KB | Rozmiar wartości 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Rozmiary standardowe pamięci podręcznej** | | |**Megabity na sekundę (Mb/s) / MB na sekundę (MB/s)** |**Liczba żądań na drugim (RPS) bez protokołu SSL** |**Liczba żądań na drugi SSL (RPS)** |
| C0 |250 MB |Udostępniona |100 / 12.5 |15,000 |7500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37,000 |
| C3 |6 GB |4 |1000 / 125 |100,000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102,000 |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Rozmiar pamięci podręcznej — wersja Premium** | |**Rdzenie procesora CPU na niezależnego fragmentu** | **Megabity na sekundę (Mb/s) / MB na sekundę (MB/s)** |**Liczba żądań na drugim (RPS) bez protokołu SSL, na niezależnego fragmentu** |**Liczba żądań na drugi SSL (RPS), na niezależnego fragmentu** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400 000 |373,000 |

Aby uzyskać instrukcje dotyczące konfigurowania stunnel lub takie jak pobieranie narzędzi Redis `redis-benchmark.exe`, zobacz [jak uruchomić polecenia Redis?](#cache-commands) sekcji.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>W jakim regionie należy znaleźć Moje pamięci podręcznej?
Aby uzyskać najlepszą wydajność i najniższym opóźnieniu zlokalizować pamięć podręczna Redis Azure w tym samym regionie co aplikacja klienta pamięci podręcznej.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Jak m rozliczane dla pamięci podręcznej Redis Azure?
Cennik usługi pamięć podręczna Redis Azure jest [tutaj](https://azure.microsoft.com/pricing/details/cache/). Cenowych znajduje się lista cenach za godzinę. Pamięci podręczne są rozliczane na podstawie na minutę od czasu utworzenia pamięci podręcznej czasu pamięci podręcznej zostanie usunięty. Nie ma żadnych opcji dla zatrzymanie lub wstrzymanie rozliczeń pamięci podręcznej.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Z chmury Azure dla instytucji rządowych, chmury Azure Chin lub Microsoft Azure Niemcy można używać usługi pamięć podręczna Redis Azure?
Tak, pamięć podręczna Redis Azure jest dostępna w chmurze platformy Azure dla instytucji rządowych, chmury Azure Chin i Microsoft platformy Azure w Niemczech. Adresy URL dostęp i zarządzanie pamięcią podręczną Redis Azure różnią się w tych chmurach w porównaniu z chmury publicznej Azure. 

| Chmura   | Sufiks DNS dla pamięci podręcznej Redis            |
|---------|---------------------------------|
| Publiczne  | *.redis.cache.windows.net       |
| Rząd USA  | *.redis.cache.usgovcloudapi.net |
| Niemcy | *.redis.cache.cloudapi.de       |
| Chiny   | *.redis.cache.chinacloudapi.cn  |

Aby uzyskać więcej informacji na uwagi dotyczące korzystania z innych chmur pamięć podręczna Redis Azure zobacz następujące łącza.

- [Baz danych Azure dla instytucji rządowych — pamięć podręczna Azure Redis](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Chmury Azure Chin — pamięć podręczna Azure Redis](https://www.azure.cn/documentation/services/redis-cache/)
- [Niemcy platformy Microsoft Azure](https://azure.microsoft.com/overview/clouds/germany/)

Uzyskać informacji o użyciu pamięci podręcznej Redis Azure przy użyciu programu PowerShell w chmurze platformy Azure dla instytucji rządowych, chmury Azure Chin i Microsoft Azure Niemczech, zobacz [sposób nawiązywania połączenia z innych chmur — pamięć podręczna Redis Azure PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Co zrobić opcje konfiguracji w programie StackExchange.Redis?
Programie StackExchange.Redis ma wiele opcji. Ta sekcja zawiera informacje o niektórych typowych ustawień. Aby uzyskać szczegółowe informacje o programie StackExchange.Redis opcjach, zobacz [konfiguracji w programie StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Opis | Zalecenie |
| --- | --- | --- |
| AbortOnConnectFail |Gdy ma wartość true, połączenie nie zostanie ponownie połączyć po awarii sieci. |Wartość false i pozwól programie StackExchange.Redis automatycznie ponownie. |
| ConnectRetry |Liczba powtórzeń próby połączenia podczas początkowego połączenia. |Zobacz poniższe uwagi, aby uzyskać wskazówki. |
| ConnectTimeout |Limit czasu w ms na operacje connect. |Zobacz poniższe uwagi, aby uzyskać wskazówki. |

Ustawienia domyślne klienta są zwykle wystarczające. Można dostosować opcje, w oparciu o obciążenie.

* **Ponowne próby**
  * ConnectRetry i ConnectTimeout ogólne wskazówki jest szybkie zakończyć się niepowodzeniem i ponów próbę. W tych wskazówkach opiera się na obciążenie i ilość czasu na średni przyjmuje klienta wydać polecenie Redis i odpowiedzi.
  * Let programie StackExchange.Redis automatycznie ponownie zestawione, zamiast sprawdzanie stanu połączenia i ponowne łączenie samodzielnie. **Unikaj używania właściwości ConnectionMultiplexer.IsConnected**.
  * Snowballing — czasami może wystąpić problem, gdy są ponawianie próby i liczby ponownych prób snowball i nigdy nie odzyskuje. W przypadku snowballing, należy rozważyć przy użyciu algorytmu ponawiania wykładniczego wycofywania, zgodnie z opisem w [ponów ogólne wskazówki](../best-practices-retry-general.md) opublikowane przez grupę Microsoft Patterns & rozwiązań.
* **Wartości limitu czasu**
  * Należy wziąć pod uwagę obciążenie i ustaw odpowiednie wartości. Jeśli przechowujesz dużych wartości, należy ustawić limitu czasu na wartość większą.
  * Ustaw `AbortOnConnectFail` połączyć programie StackExchange.Redis false i pozwala ponownie dla Ciebie.
  * Używać jednego wystąpienia ConnectionMultiplexer dla aplikacji. LazyConnection umożliwia utworzenie jednego wystąpienia zwracane przez właściwości połączenia, jak pokazano w [Połącz z pamięci podręcznej, za pomocą klasy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ustaw `ConnectionMultiplexer.ClientName` właściwości aplikacji wystąpienia unikatową nazwę w celach diagnostycznych.
  * Używanych jest wiele `ConnectionMultiplexer` wystąpień dla niestandardowych obciążeń.
      * Jeśli masz różne obciążenia w aplikacji, można wykonać tego modelu. Na przykład:
      * Może mieć jeden multiplekser dotyczące postępowania w przypadku dużych kluczy.
      * Może mieć jeden multiplekser dotyczące postępowania z kluczami mała.
      * Można ustawić różne wartości limitów czasu połączenia i Logika ponawiania próby dla każdego ConnectionMultiplexer, którego używasz.
      * Ustaw `ClientName` właściwości na każdym multiplekser ułatwiające diagnostyki.
      * W tych wskazówkach może prowadzić do jednego usprawnić czas oczekiwania na `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Jakie klientów pamięci podręcznej Redis można użyć?
Jedną z najważniejszych funkcji Redis zakłada, że wielu klientów, obsługa wielu języków programowania inny. Aby uzyskać bieżącą listę klientów, zobacz [Redis klientów](http://redis.io/clients). Samouczki, które obejmują kilka różnych języków i klientów, zobacz [jak używać usługi pamięć podręczna Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md) i kliknij żądany język z tym przełącznikiem języka u góry tego artykułu.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Czy istnieje lokalne emulatora dla pamięci podręcznej Redis Azure?
Nie jest nie emulator lokalnej pamięci podręcznej Redis Azure, ale można uruchomić wersji MSOpenTech redis-server.exe z [Redis narzędzia wiersza polecenia](https://github.com/MSOpenTech/redis/releases/) na lokalnym komputerze, a następnie połączyć się go, aby podobne możliwości na emulatorze lokalnej pamięci podręcznej, jak pokazano w poniższym przykładzie:

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


Opcjonalnie można skonfigurować [redis.conf](http://redis.io/topics/config) pliku, aby lepiej dopasować [domyślne ustawienia pamięci podręcznej](cache-configure.md#default-redis-server-configuration) dla Twojego online pamięć podręczna Redis Azure w razie potrzeby.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak uruchomić polecenia Redis?
Można użyć dowolnego z poleceń w [Redis polecenia](http://redis.io/commands#) z wyjątkiem poleceń w [polecenia nie są obsługiwane w pamięci podręcznej Redis Azure Redis](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Istnieje kilka możliwości do uruchamiania polecenia Redis.

* Jeśli masz pamięci podręcznej Standard lub Premium, możesz uruchamiać polecenia Redis przy użyciu [Redis konsoli](cache-configure.md#redis-console). Konsola Redis zapewnia bezpieczny sposób do uruchamiania polecenia Redis w portalu Azure.
* Umożliwia także narzędzi wiersza polecenia w pamięci podręcznej Redis. Aby korzystać z nich, wykonaj następujące czynności:
* Pobierz [Redis narzędzia wiersza polecenia](https://github.com/MSOpenTech/redis/releases/).
* Połącz przy użyciu pamięci podręcznej `redis-cli.exe`. Przekaż w pamięci podręcznej punktu końcowego za pomocą przełącznika -h i klucz przy użyciu jak pokazano w poniższym przykładzie:
* `redis-cli -h <redis cache name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Narzędzia wiersza polecenia Redis nie działa z portem SSL, ale można użyć narzędzia, takie jak `stunnel` nawiązać bezpiecznego narzędzia SSL port przez zgodnie z instrukcjami w [Announcing ASP.NET dostawcę stanu sesji dla wersji zapoznawczej Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) wpis w blogu.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Dlaczego nie pamięci podręcznej Redis Azure ma MSDN dotyczące biblioteki klas takich jak niektóre z innymi usługami platformy Azure?
Pamięć podręczna Redis Azure firmy Microsoft jest oparta na popularnych typu open source pamięci podręcznej Redis i są dostępne dla różnych typów [Redis klientów](http://redis.io/clients) dla wielu języków programowania. Każdy klient ma własny interfejs API, który nawiązuje połączeń używa wystąpienia pamięci podręcznej Redis [Redis polecenia](http://redis.io/commands).

Dlatego różni się każdego klienta, nie nie jedno odwołanie scentralizowane klasy w witrynie MSDN, i każdy klient przechowuje własnej dokumentacji. Oprócz dokumentacji istnieje kilka samouczki przedstawiająca sposób zacząć korzystać z pamięci podręcznej Redis Azure za pomocą różnych języków i klientów pamięci podręcznej. Aby uzyskać dostęp do tych samouczków, zobacz [jak używać usługi pamięć podręczna Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md) i kliknij żądany język z tym przełącznikiem języka u góry tego artykułu.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Pamięć podręczna Redis Azure można używać jako pamięci podręcznej sesji PHP?
Tak, aby użyć pamięci podręcznej Redis Azure jako pamięci podręcznej sesji PHP, określ parametry połączenia do wystąpienia pamięci podręcznej Redis Azure w `session.save_path`.

> [!IMPORTANT]
> Przy użyciu pamięci podręcznej Redis Azure w pamięci podręcznej sesji PHP, muszą być adres URL kodowania klucz zabezpieczeń używane do połączenia z pamięci podręcznej, jak pokazano w poniższym przykładzie:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Jeśli klucz nie jest kodowany adres URL, może zostać wyświetlony wyjątek z następującym komunikatem, takich jak: `Failed to parse session.save_path`
>
>

Aby uzyskać więcej informacji o korzystaniu z pamięci podręcznej Redis jako pamięci podręcznej sesji PHP z klientem PhpRedis, zobacz [programu obsługi PHP sesji](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co to są Redis baz danych?

Bazy danych redis są logicznej rozdzielenie danych w ramach tego samego wystąpienia pamięci podręcznej Redis. Pamięć podręczna jest wspólne dla wszystkich baz danych i rzeczywistego pamięci zużycie danego bazy danych zależy od kluczy wartości przechowywane w bazie danych. Na przykład pamięć podręczną C6 ma 53 GB pamięci. Użytkownik może umieścić wszystkie 53 GB w jednej bazy danych lub Podziel między wieloma bazami danych. 

> [!NOTE]
> Korzystając z włączoną funkcją klastrowania podręczna Redis Azure Premium, tylko bazy danych 0 jest dostępna. To ograniczenie wewnętrzne ograniczenie pamięci podręcznej Redis i nie jest specyficzne dla pamięci podręcznej Redis Azure. Aby uzyskać więcej informacji, zobacz [należy wprowadzać żadnych zmian do mojej aplikacji klienta do korzystania z klastra?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Jeśli włączenie portu bez protokołu SSL w celu nawiązania Redis?
Redis serwer nie obsługuje natywnie SSL, ale nie w pamięci podręcznej Redis Azure. Jeśli łączysz się z pamięcią podręczną Redis Azure i klient obsługuje protokół SSL, takich jak programie StackExchange.Redis, należy użyć protokołu SSL.

>[!NOTE]
>Port bez protokołu SSL jest domyślnie wyłączona, dla nowego wystąpienia pamięci podręcznej Redis Azure. Jeśli klient nie obsługuje protokołu SSL, a następnie należy włączyć portu bez protokołu SSL przez zgodnie z instrukcjami w [portów](cache-configure.md#access-ports) sekcji [Konfigurowanie pamięci podręcznej w pamięci podręcznej Redis Azure](cache-configure.md) artykułu.
>
>

Redis narzędzi, takich jak `redis-cli` nie działa z portem SSL, ale można użyć narzędzia, takie jak `stunnel` nawiązać bezpiecznego narzędzia SSL port przez zgodnie z instrukcjami w [Announcing ASP.NET dostawcę stanu sesji dla wersji zapoznawczej Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) wpis w blogu.

Aby uzyskać instrukcje dotyczące pobierania narzędzia Redis, zobacz [jak uruchomić polecenia Redis?](#cache-commands) sekcji.

### <a name="what-are-some-production-best-practices"></a>Jakie są najlepsze rozwiązania produkcyjnym?
* [Najlepsze rozwiązania w programie StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfiguracja i pojęć](#configuration-and-concepts)
* [Testowanie wydajności](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Najlepsze rozwiązania w programie StackExchange.Redis
* Ustaw `AbortConnect` na wartość false, a następnie umożliwił ConnectionMultiplexer automatycznie ponownie. [Aby uzyskać szczegółowe informacje, zobacz](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Ponowne użycie ConnectionMultiplexer — nie należy tworzyć nowego filtru dla każdego żądania. `Lazy<ConnectionMultiplexer>` Wzorzec [pokazane](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) jest zalecane.
* Redis działa najlepiej o mniejszej wartości, dlatego należy rozważyć siekanie zapasową większy danych do wielu kluczy. W [rozważania Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb jest uznawany za duża. Odczyt [w tym artykule](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) na przykład problem, który może być spowodowany dużej wartości.
* Konfigurowanie sieci [ustawienia puli wątków](#important-details-about-threadpool-growth) w celu uniknięcia przekroczenia limitu czasu.
* Użyj co najmniej connectTimeout domyślne 5 sekund. Ten interwał spowodowałoby to nadanie programie StackExchange.Redis wystarczającą ilość czasu, aby ponownie ustanowić połączenia, w przypadku blip sieci.
* Należy pamiętać o koszty wydajności związane z różne operacje, na którym jest uruchomiony. Na przykład `KEYS` polecenia jest operacją O(n) i należy unikać. [Lokacji redis.io](http://redis.io/commands/) zawiera szczegóły dotyczące złożoności czasu dla każdej operacji, który go obsługuje. Kliknij każdy polecenie, aby wyświetlić złożoności dla każdej operacji.

#### <a name="configuration-and-concepts"></a>Konfiguracja i pojęć
* Użyj Standard lub Premium warstwy dla systemów produkcyjnych. Warstwa podstawowa jest systemem jednego węzła z nie danych replikacji i nie umowy dotyczącej poziomu usług. Należy także użyć co najmniej C1 pamięci podręcznej. Pamięci podręczne C0 są zazwyczaj używane w scenariuszach proste tworzenie/testowanie oprogramowania.
* Należy pamiętać, że jest Redis **w pamięci** magazynu danych. Odczyt [w tym artykule](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) tak, aby poznać scenariuszy, w których może wystąpić utrata danych.
* Opracowanie systemu tak, aby mogły obsługiwać połączenia blips [z powodu stosowania poprawek i pracy awaryjnej](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testowanie wydajności
* Uruchom przy użyciu `redis-benchmark.exe` uzyskać pewne pojęcie o przepływności możliwe przed zapisaniem własnych danych o wydajności testów. Ponieważ `redis-benchmark` nie obsługuje protokołu SSL, należy najpierw [włączenie portu bez protokołu SSL za pośrednictwem portalu Azure](cache-configure.md#access-ports) przed uruchomieniem testu. Przykłady można znaleźć [jak testu wydajności i testowania wydajności Moje pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Klient do testowania maszyny Wirtualnej musi należeć do tego samego regionu wystąpienia pamięci podręcznej Redis.
* Zalecamy używanie Dv2 serii maszyn wirtualnych klienta lepsze sprzęt i nadać uzyskania najlepszych wyników.
* Upewnij się, klient zostanie wybrana maszyna wirtualna ma co najmniej tyle obliczeniowych i testowanie możliwości przepustowości jako pamięci podręcznej.
* Włącz VRSS na komputerze klienckim, jeśli są w systemie Windows. [Aby uzyskać szczegółowe informacje, zobacz](https://technet.microsoft.com/library/dn383582.aspx).
* Wystąpienia pamięci podręcznej Redis warstwy Premium mają lepiej sieci opóźnienia i przepływności, ponieważ działają na lepsze sprzętu dla procesora CPU i sieci.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jakie są kwestii dotyczących za pomocą wspólnego poleceń Redis?
* Nie należy uruchamiać niektórych poleceń pamięci podręcznej Redis, które długo trwać, aby zakończyć bez zrozumienia wpływu tych poleceń.
  * Na przykład nie uruchamiaj [KLUCZE](http://redis.io/commands/keys) command w środowisku produkcyjnym może potrwać długo w zależności od liczby kluczy. Redis jest serwerem jednowątkowe i przetwarza polecenia co naraz. Jeśli masz inne polecenia wydane po KLUCZE ich nie będą przetwarzane do momentu Redis przetwarza polecenia KLUCZY. [Lokacji redis.io](http://redis.io/commands/) zawiera szczegóły dotyczące złożoności czasu dla każdej operacji, który go obsługuje. Kliknij każdy polecenie, aby wyświetlić złożoności dla każdej operacji.
* Rozmiary kluczy — należy używać małych klucza/wartości lub dużych klucza/wartości? Ogólnie rzecz biorąc zależy od scenariusza. Scenariusz wymaga większych kluczy, można dopasować wartość parametru ConnectionTimeout i spróbuj ponownie wartości i dostosować Logika ponawiania. Z perspektywy serwera Redis aby mieć lepszą wydajność przestrzegane są mniejsze wartości.
* Te zagadnienia nie oznacza, że nie można przechowywać większe wartości w pamięci podręcznej Redis; należy pamiętać o następujących kwestiach. Opóźnienia będzie wyższa. Jeśli jeden zestaw danych, który jest większy i jedną, która jest mniejsza, można użyć wielu wystąpień ConnectionMultiplexer, każdy skonfigurowany zestaw różne wartości limitu czasu i ponów próbę, zgodnie z opisem w poprzedniej [co zrobić w programie StackExchange.Redis Opcje konfiguracji czy](#cache-configuration) sekcji.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak testu wydajności i testowania wydajności Moje pamięci podręcznej?
* [Włącz diagnostykę pamięci podręcznej](cache-how-to-monitor.md#enable-cache-diagnostics), aby móc [monitorować](cache-how-to-monitor.md) jej kondycję. Możesz wyświetlać metryki w witrynie Azure Portal oraz [pobierać i przeglądać](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) je przy użyciu wybranych przez siebie narzędzi.
* Umożliwia redis benchmark.exe test obciążenia serwera Redis.
* Upewnij się, że obciążenie testowanie klienta i pamięć podręczna Redis znajdują się w tym samym regionie.
* Użyj redis cli.exe i monitorować za pomocą polecenia informacji o pamięci podręcznej.
* Jeżeli obciążenie jest przyczyną fragmentacji pamięci wysokiej, powinien skalowanie w górę na większy rozmiar pamięci podręcznej.
* Aby uzyskać instrukcje dotyczące pobierania narzędzia Redis, zobacz [jak uruchomić polecenia Redis?](#cache-commands) sekcji.

Następujące polecenia. podać przykład za pomocą redis benchmark.exe. Aby uzyskać dokładne wyniki uruchom następujące polecenia z maszyny Wirtualnej w tym samym regionie co pamięć podręczną.

* Przy użyciu 1 ładunku k żądań potokowe SET testu

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test potokowe pobrać żądania przy użyciu 1 ładunku k.
  Uwaga: Uruchom zestaw testów pokazano powyżej najpierw w celu wypełnienia pamięci podręcznej

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Ważne informacje dotyczące wzrostu puli wątków
Puli wątków CLR ma dwa typy wątków - "Pracownik" i "We/wy wykonania Port" (alias portu) wątków.

* Wątki robocze są używane podczas dla elementów, jak przetwarzanie `Task.Run(…)` lub `ThreadPool.QueueUserWorkItem(…)` metody. Wątki te są również używane przez różne składniki środowiska CLR podczas pracy musi zostać przeprowadzona na wątku w tle.
* Wątki portu są używane w przypadku asynchroniczne We/Wy (np. zawartości z sieci).

Pula wątków zapewnia nowych wątków roboczych lub wątków zakończenia We/Wy na żądanie (bez żadnych ograniczania) dopóki nie osiągnie ustawienia "Minimalna" dla każdego typu wątku. Minimalna liczba wątków domyślnie do liczby procesorów w systemie.

Gdy liczba istniejących wątków (zajęty) trafienia "minimalna" Liczba wątków, puli wątków będzie przepustowości jaką go injects nowych wątków jeden wątek na każdym 500 milisekund. Zwykle jeśli system pobiera serii pracy wymagające wątku portu, zostanie przetworzony pracy bardzo szybko. Jednak jeśli serii pracy jest większa niż skonfigurowane ustawienia "Minimalna", będą pewne opóźnienie podczas przetwarzania część pracy zgodnie z puli wątków czeka na jedno z następujących niepożądane.

1. Wątek istniejących staje się wolnego podczas pracy.
2. Nie istniejącego wątku staje się bezpłatnie do 500 MS., dzięki utworzeniu nowego wątku.

Zasadniczo oznacza to, że gdy liczba wątków zajęty jest większa niż minimalna wątków, prawdopodobnie płatność opóźnienie 500 MS. przed przetworzeniem ruchu sieciowego przez aplikację. Ponadto należy należy pamiętać, że podczas istniejących wątku pozostanie bezczynności przez czas dłuższy niż 15 sekund (oparte na I Zapamiętaj), zostaną go wyczyszczone i powtórzyć ten cykl wzrostu i kurczenia.

Jeśli przyjrzymy się komunikat o błędzie na przykład w programie StackExchange.Redis (kompilacji 1.0.450 lub nowsza), zobaczysz, że teraz Wyświetla statystyki puli wątków (zobacz poniżej szczegóły portu i proces ROBOCZY).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

W poprzednim przykładzie widać, że dla portu wątku są 6 zajęty wątków i system został skonfigurowany, aby umożliwić 4 minimalna liczba wątków. W takim przypadku klient będzie prawdopodobnie przejrzane dwóch opóźnienia 500 ms ponieważ 6 > 4.

Należy pamiętać, programie StackExchange.Redis można trafień limitów czasu Jeśli pobiera ograniczenie wzrostu wątków portu lub procesu roboczego.

### <a name="recommendation"></a>Zalecenie
Biorąc pod uwagę te informacje, zdecydowanie zaleca się czy klientów ustawioną wartość minimalnej konfiguracji portu i proces ROBOCZY wątków coś większa niż wartość domyślna. Firma Microsoft nie można nadać pasującego wskazówki dotyczące co ta wartość powinna być, ponieważ wartość prawej strony dla jednej aplikacji będzie zbyt wysoki/niska dla innej aplikacji. To ustawienie może również wpłynąć na wydajność innych części złożonych aplikacji, więc musi każdego klienta w celu dopasowania, to ustawienie, aby ich własnych potrzeb. Dobrym miejscem początkowy jest 200 lub 300, przetestowanie i dostosowanie zgodnie z potrzebami.

Jak skonfigurować tego ustawienia:

* W programie ASP.NET, użyj [ustawienie konfiguracji "minIoThreads"] [ "minIoThreads" configuration setting] w obszarze `<processModel>` element konfiguracji w pliku web.config. Jeśli korzystasz z wewnątrz witryn sieci Web platformy Azure, to ustawienie nie jest uwidaczniana, za pomocą opcji konfiguracji. Jednakże, nadal można skonfigurować to ustawienie programowo (patrz poniżej), od wybranej metody Application_Start w global.asax.cs.

  > [!NOTE] 
  > Wartość określona w tym elemencie konfiguracji jest *-core* ustawienie. Na przykład jeśli korzystasz z 4-rdzeniową maszyną i chcesz ustawienia minIOThreads się 200 w czasie wykonywania, czy użyć `<processModel minIoThreads="50"/>`.
  >

* Poza ASP.NET, użyj [ThreadPool.SetMinThreads(...) ](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) Interfejsu API.

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Włącz serwer wykazu Globalnego uzyskać więcej przepływności na kliencie, używając programie StackExchange.Redis
Włączenie serwera wykazu Globalnego można zoptymalizować klienta i zapewnić lepszą wydajność i Przepływność przy użyciu programie StackExchange.Redis. Aby uzyskać więcej informacji na serwerze wykazu Globalnego i jak je włączyć zobacz następujące artykuły:

* [Aby włączyć serwer wykazu Globalnego](https://msdn.microsoft.com/library/ms229357.aspx)
* [Podstawy dotyczące wyrzucania elementów bezużytecznych](https://msdn.microsoft.com/library/ee787088.aspx)
* [Wyrzucanie elementów bezużytecznych i wydajność](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Zagadnienia dotyczące wydajności wokół połączeń

Każda warstwa cenowa ma inną limity dla połączeń klienckich, pamięci oraz przepustowości. Podczas każdego rozmiaru pamięci podręcznej umożliwia *do* określonej liczby połączeń, każdego połączenia Redis obciążenie ma skojarzonych z nim. Przykładem takich obciążenie będzie użycia Procesora i pamięci w wyniku szyfrowania TLS/SSL. Limit maksymalnej liczby połączeń dla rozmiaru pamięci podręcznej danego zakłada lekkim załadować pamięci podręcznej. Czy ładowanie z połączenia koszty *plus* obciążenia z operacjami klienta przekracza pojemność dla systemu, pamięci podręcznej mogą doświadczyć problemów dotyczących pojemności, nawet jeśli nie przekroczono limit połączeń dla bieżący rozmiar pamięci podręcznej.

Aby uzyskać więcej informacji na temat limitów różnych połączeń dla każdej warstwy, zobacz [cennik usługi pamięć podręczna Redis Azure](https://azure.microsoft.com/pricing/details/cache/). Aby uzyskać więcej informacji na temat połączeń i inne konfiguracje domyślne, zobacz [Redis domyślnej konfiguracji serwera](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak monitorować kondycję i wydajność Moje pamięci podręcznej
Wystąpienia pamięci podręcznej Redis Azure firmy Microsoft mogą być monitorowane w [portalu Azure](https://portal.azure.com). Można wyświetlić metryki, Przypnij wykresy metryk do tablicy startowej, Dostosuj zakres dat i godzin monitorowania wykresy, dodać i usunąć metryki z wykresy i ustawić alerty, gdy są spełnione następujące warunki. Aby uzyskać więcej informacji, zobacz [pamięci podręcznej Redis Azure Monitor](cache-how-to-monitor.md).

Pamięć podręczna Redis **zasobów menu** zawiera również kilka narzędzi do monitorowania i rozwiązywania problemów pamięci podręczne.

* **Diagnozowanie i rozwiązywanie problemów** zawiera informacje o najczęściej występujących problemów i strategii w celu ich rozwiązania.
* **Kondycja zasobów** oczekuje zasobu i informuje, czy działa on zgodnie z oczekiwaniami. Aby uzyskać więcej informacji o usłudze kondycji zasobów Azure, zobacz [Przegląd kondycji zasobów Azure](../resource-health/resource-health-overview.md).
* **Nowe żądanie pomocy technicznej** udostępnia opcje otwarcia żądania pomocy technicznej dla pamięci podręcznej.

Te narzędzia umożliwiają monitorowanie kondycji wystąpień z pamięci podręcznej Redis Azure i pomocne w zarządzaniu aplikacjami buforowania. Aby uzyskać więcej informacji, zobacz sekcję "Ustawienia dotyczące rozwiązywania problemów i pomocy technicznej" [Konfigurowanie pamięci podręcznej Redis Azure](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Dlaczego widzę limitów czasu?
Limity czasu wprowadzenia w klienta, który umożliwia rozmowę z pamięci podręcznej Redis. Po wysłaniu polecenia do serwera Redis polecenia jest umieszczone w kolejce i serwera Redis ostatecznie przejmuje polecenia i go uruchomi. Jednak klient może przekroczono limit czasu podczas tego procesu, a jeśli nie wystąpił wyjątek zgłoszony podczas wywołania po stronie. Aby uzyskać więcej informacji na temat rozwiązywania problemów limitu czasu, zobacz [rozwiązywania problemów klienta](cache-how-to-troubleshoot.md#client-side-troubleshooting) i [wyjątków przekroczenia limitu czasu w programie StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Dlaczego moja klient został odłączony od pamięci podręcznej?
Poniżej przedstawiono niektóre typowe Przyczyna rozłączenia pamięci podręcznej.

* Powoduje, że po stronie klienta
  * Aplikacja kliencka zostało wdrożone.
  * Aplikacja kliencka wykonać operacji skalowania.
    * W przypadku usługi w chmurze lub aplikacji sieci Web może to być spowodowane Skalowanie automatyczne.
  * Zmienić warstwy sieci po stronie klienta.
  * Przejściowy błąd klienta lub w węzłach sieci między klientem a serwerem.
  * Limity przepustowości próg były dostępne.
  * Procesora powiązany operacji trwało zbyt długo.
* Powoduje, że po stronie serwera
  * W buforze standardowe oferty usługi pamięć podręczna Redis Azure inicjowane awaryjnej z podstawowej węzła do węzła pomocniczego.
  * Azure została poprawki wystąpienia wdrożonym pamięci podręcznej
    * Może to być aktualizacje serwera Redis lub ogólne konserwacji maszyny Wirtualnej.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Która oferta pamięci podręcznej systemu Azure jest dla mnie najlepsza?
> [!IMPORTANT]
> Zgodnie z ostatniego roku [anonsu](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), usługa usługi zarządzana pamięć podręczna Azure i pamięci podręcznej na roli Azure **zostały wycofane** w dniu 30 listopada 2016 r. Firma Microsoft zaleca, aby użyć [pamięć podręczna Redis Azure](https://azure.microsoft.com/services/cache/). Aby uzyskać informacje na temat migracji, zobacz [migracji z usługi zarządzana pamięć podręczna pamięci podręcznej Redis Azure](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Azure Redis Cache
Pamięć podręczna Redis Azure jest ogólnie dostępna w rozmiarów do 53 GB i ma dostępność na poziomie 99,9%. Nowy [warstwy premium](cache-premium-tier-intro.md) oferuje do rozmiarów 530 GB i pomoc techniczna dla klastra sieć Wirtualną i trwałości z SLA 99,9%.

Pamięć podręczna Redis Azure zapewnia klientom możliwość korzystania z bezpiecznego, dedykowanej pamięci podręcznej Redis, zarządzany przez firmę Microsoft. Z tej oferty Pobierz bogatemu zestawowi funkcji i ekosystemem pochodzącymi z pamięci podręcznej Redis i niezawodnych hosting i monitorowania firmy Microsoft.

W przeciwieństwie do tradycyjnych pamięci podręczne, które dotyczą tylko z pary klucz wartość Redis jest popularnych jego wysokiej wydajności typów danych. Redis również obsługuje uruchamianie niepodzielne operacje na tych typów, takich jak dołączanie ciąg. Zwiększanie wartości mieszania; Wypychanie do listy; Obliczanie przecięcia zestawu, Unii i różnica; lub pobieranie element członkowski o najwyższej klasyfikacji w zestawie posortowane. Inne funkcje obejmują obsługę transakcji, pub/sub i Lua skryptów, klucze z ograniczoną time-to-live oraz ustawienia konfiguracji, aby zachowują się podobnie tradycyjnych pamięci podręcznej Redis.

Innym aspektem klucza pamięci podręcznej Redis Powodzenie jest ekosystemu dobrej kondycji, żywe typu open source, wbudowane wokół niego. Znajduje to odzwierciedlenie w różnorodnych klientów pamięci podręcznej Redis dostępne w wielu językach. Ten ekosystem i szerokiego zakresu klientów Zezwalaj pamięci podręcznej Redis Azure mają być używane przez niemal dowolnym obciążeniu, które tworzysz wewnątrz Azure.

Aby uzyskać więcej informacji na temat rozpoczynania pracy z pamięcią podręczną Redis Azure, zobacz [jak pamięć podręczna Redis Azure użyj](cache-dotnet-how-to-use-azure-redis-cache.md) i [dokumentacji pamięć podręczna Redis Azure](index.md).

### <a name="managed-cache-service"></a>Zarządzane usługi pamięć podręczna
[Zarządzana pamięć podręczna usługi została wycofana w dniu 30 listopada 2016 r.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić zarchiwizowaną dokumentacji, zobacz [zarchiwizowane dokumentację usługi pamięć podręczna zarządzane](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Pamięć podręczna oparta na roli
[Pamięć podręczna w roli została wycofana w dniu 30 listopada 2016 r.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Aby wyświetlić zarchiwizowaną dokumentacji, zobacz [zarchiwizowane dokumentacji pamięci podręcznej w roli](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
