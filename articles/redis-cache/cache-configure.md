---
title: Konfigurowanie pamięci podręcznej Redis Azure | Dokumentacja firmy Microsoft
description: Omówienie domyślną konfigurację pamięci podręcznej Redis w pamięci podręcznej Redis Azure i Dowiedz się, jak skonfigurować swoich wystąpień w pamięci podręcznej Redis Azure
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: 0cd21c0367a95d3e866137797ac32fc5bdd196c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31420735"
---
# <a name="how-to-configure-azure-redis-cache"></a>Konfigurowanie pamięci podręcznej Redis Azure
W tym temacie opisano konfiguracje, które są dostępne dla swoich wystąpień w pamięci podręcznej Redis Azure. W tym temacie omówiono również domyślną konfigurację serwera Redis wystąpienia pamięci podręcznej Redis Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania przy użyciu funkcji pamięci podręcznej premium, zobacz [Konfigurowanie trwałości](cache-how-to-premium-persistence.md), [sposobu konfigurowania klastra](cache-how-to-premium-clustering.md), i [Konfigurowanie obsługi sieci wirtualnej ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Skonfiguruj ustawienia pamięci podręcznej Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Ustawienia pamięci podręcznej Redis Azure są wyświetlane i skonfigurowane na **pamięci podręcznej Redis** za pomocą bloku **zasobów Menu**.

![Ustawienia pamięci podręcznej redis](./media/cache-configure/redis-cache-settings.png)

Możesz wyświetlić i skonfigurować następujące ustawienia za pomocą **zasobów Menu**.

* [Omówienie](#overview)
* [Dziennik aktywności](#activity-log)
* [Kontrola dostępu (IAM)](#access-control-iam)
* [Tagi](#tags)
* [Diagnozowanie i rozwiązywanie problemów](#diagnose-and-solve-problems)
* [Ustawienia](#settings)
    * [Klawisze dostępu](#access-keys)
    * [Ustawienia zaawansowane](#advanced-settings)
    * [Klasyfikator pamięci podręcznej redis](#redis-cache-advisor)
    * [Skalowanie](#scale)
    * [Rozmiar klastra redis](#cluster-size)
    * [Trwałość danych redis](#redis-data-persistence)
    * [Aktualizacje harmonogramu](#schedule-updates)
    * [Geo-replication](#geo-replication) (Replikacja geograficzna)
    * [Virtual Network](#virtual-network)
    * [Zapora](#firewall)
    * [właściwości](#properties)
    * [Blokady](#locks)
    * [Skrypt automatyzacji](#automation-script)
* [Administracja](#administration)
    * [Importowanie danych](#importexport)
    * [Eksportowanie danych](#importexport)
    * [Ponowne uruchamianie](#reboot)
* [Monitorowanie](#monitoring)
    * [Redis metryk](#redis-metrics)
    * [Reguły alertów](#alert-rules)
    * [Diagnostyka](#diagnostics)
* [Rozwiązywanie problemów z ustawieniami i pomoc techniczna](#support-amp-troubleshooting-settings)
    * [Kondycja zasobów](#resource-health)
    * [nowe żądanie pomocy technicznej](#new-support-request)


## <a name="overview"></a>Przegląd

**Omówienie** zapewnia możesz podstawowych informacji o pamięci podręcznej, takie jak nazwa, porty, warstwa cenowa i metryki wybranego pamięci podręcznej.

### <a name="activity-log"></a>Dziennik aktywności

Kliknij przycisk **dziennik aktywności** Aby wyświetlić akcje wykonywane w pamięci podręcznej. Aby rozszerzyć ten widok ma zawierać inne zasoby, możesz również użyć filtrowania. Aby uzyskać więcej informacji na temat pracy z dziennikami inspekcji, zobacz [inspekcji operacji za pomocą Menedżera zasobów](../azure-resource-manager/resource-group-audit.md). Aby uzyskać więcej informacji dotyczących monitorowania zdarzeń pamięć podręczna Redis Azure, zobacz [operacji i alerty](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Kontrola dostępu (IAM)

**(IAM) kontroli dostępu** sekcji zapewnia obsługę kontroli dostępu opartej na rolach (RBAC) w portalu Azure. Taka konfiguracja pozwala organizacjom, które spełnia ich wymagania dotyczące zarządzania dostępu, wystarczy i dokładnie. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach w portalu Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Tagi

**Tagi** sekcji pomaga organizować zasobów. Aby uzyskać więcej informacji, zobacz [organizowania zasobów na platformie Azure przy użyciu tagów](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnozowanie i rozwiązywanie problemów

Kliknij przycisk **diagnozowanie i rozwiązywanie problemów** dostarczanych z najczęściej występujących problemów i strategii w celu ich rozwiązania.



## <a name="settings"></a>Ustawienia
**Ustawienia** sekcja umożliwia dostęp i skonfiguruj następujące ustawienia dla pamięci podręcznej.

* [Klawisze dostępu](#access-keys)
* [Ustawienia zaawansowane](#advanced-settings)
* [Klasyfikator pamięci podręcznej redis](#redis-cache-advisor)
* [Skalowanie](#scale)
* [Rozmiar klastra redis](#cluster-size)
* [Trwałość danych redis](#redis-data-persistence)
* [Aktualizacje harmonogramu](#schedule-updates)
* [Geo-replication](#geo-replication) (Replikacja geograficzna)
* [Virtual Network](#virtual-network)
* [Zapora](#firewall)
* [właściwości](#properties)
* [Blokady](#locks)
* [Skrypt automatyzacji](#automation-script)



### <a name="access-keys"></a>Klawisze dostępu
Kliknij przycisk **klucze dostępu** do wyświetlania lub ponownie wygenerować klucze dostępu pamięci podręcznej. Klucze te są używane przez klientów nawiązywania połączenia z pamięci podręcznej.

![Klucze dostępu pamięci podręcznej redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Ustawienia zaawansowane
Następujące ustawienia są konfigurowane na **Zaawansowane ustawienia** bloku.

* [Porty dostępu](#access-ports)
* [Zasady pamięci](#memory-policies)
* [Powiadomienia przestrzeni kluczy (ustawienia zaawansowane)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Porty dostępowe
Domyślnie dostęp inny niż za pomocą protokołu SSL jest zablokowany dla nowych pamięci podręcznych. Aby włączyć port bez protokołu SSL, kliknij przycisk **nr** dla **zezwolić na dostęp tylko za pośrednictwem protokołu SSL** na **Zaawansowane ustawienia** bloku, a następnie kliknij przycisk **zapisać**.

![Porty dostępu pamięci podręcznej redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zasady pamięci
**Zasad Maxmemory**, **zastrzeżone maxmemory**, i **zastrzeżone maxfragmentationmemory** ustawień na **Zaawansowane ustawienia** Blok skonfigurować zasady pamięci dla pamięci podręcznej.

![Zasady Maxmemory pamięci podręcznej redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zasady Maxmemory** konfiguruje zasady wykluczania dla pamięci podręcznej i można wybrać z następujących zasad wykluczenia:

* `volatile-lru` -To jest domyślne zasady wykluczania.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Aby uzyskać więcej informacji na temat `maxmemory` zasad, zobacz [zasady wykluczania](http://redis.io/topics/lru-cache#eviction-policies).

**Zastrzeżone maxmemory** ustawienie określa ilość pamięci w Megabajtach, które jest zastrzeżone dla operacji-cache, takich jak replikacji w trybie failover. Ustawienie tej wartości pozwala na lepsze środowisko serwera Redis, gdy zmienia się obciążenia. Ta wartość musi być ustawiona wyższych obciążeń, które są zapisu ciężki. Pamięć jest zarezerwowana dla takich operacji, jest niedostępna dla magazynu danych z pamięci podręcznej.

**Zastrzeżone maxfragmentationmemory** ustawienie określa ilość pamięci w Megabajtach, aby pomieścić fragmentacji pamięci zarezerwowanej. Ustawienie tej wartości pozwala na bardziej spójne środowisko serwera Redis, gdy pamięć podręczna jest pełna lub bliski pełnej i fragmentacji stosunek jest wysoka. Pamięć jest zarezerwowana dla takich operacji, jest niedostępna dla magazynu danych z pamięci podręcznej.

Rzecz wziąć pod uwagę podczas wybierania nową wartość rezerwacji pamięci (**zastrzeżone maxmemory** lub **zastrzeżone maxfragmentationmemory**) jest jak tej zmiany mogą wpłynąć na pamięci podręcznej, który jest już uruchomione z duże ilości danych. Na przykład jeśli masz 53 GB pamięci podręcznej z 49 GB danych, a następnie zmień wartość rezerwacji na 8 GB, ta zmiana spowoduje porzucenie maksymalną ilość dostępnej pamięci systemu do 45 GB. Jeśli bieżące `used_memory` lub `used_memory_rss` wartości są większe od nowego limitu 45 GB, a następnie system, należy wykluczyć danych przed zakończeniem `used_memory` i `used_memory_rss` są poniżej 45 GB. Wykluczanie może zwiększyć fragmentacji pamięci i obciążenia serwera. Aby uzyskać więcej informacji w pamięci podręcznej metryk, takich jak `used_memory` i `used_memory_rss`, zobacz [dostępne metryki i raportowania interwałów](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> **Zastrzeżone maxmemory** i **zastrzeżone maxfragmentationmemory** ustawienia są dostępne tylko dla Standard i Premium przechowuje w pamięci podręcznej.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Powiadomienia przestrzeni kluczy (ustawienia zaawansowane)
Powiadomienia są skonfigurowane na przestrzeni kluczy redis **Zaawansowane ustawienia** bloku. Powiadomienia przestrzeni kluczy pozwalają klientom odbierać powiadomienia w przypadku wystąpienia określonych zdarzeń.

![Zaawansowane ustawienia pamięci podręcznej redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Powiadomienia przestrzeni kluczy i **powiadomienia przestrzeni kluczy zdarzenia** ustawienia są dostępne tylko dla pamięci podręcznej standardowa i Premium.
> 
> 

Aby uzyskać więcej informacji, zobacz [Redis powiadomienia przestrzeni kluczy](http://redis.io/topics/notifications). Przykładowy kod, zobacz [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) w pliku [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) próbki.


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Doradca usługi Redis Cache
**Advisor pamięci podręcznej Redis** blok zawiera zalecenia dotyczące pamięci podręcznej. Podczas wykonywania normalnych operacji zostaną wyświetlone żadnych zaleceń. 

![Zalecenia](./media/cache-configure/redis-cache-no-recommendations.png)

Jeśli wszystkie warunki wystąpić podczas operacji takich jak wysokie użycie pamięci przepustowości sieci i obciążenie serwera pamięci podręcznej, zostanie wyświetlony alert na **pamięci podręcznej Redis** bloku.

![Zalecenia](./media/cache-configure/redis-cache-recommendations-alert.png)

Więcej informacji można znaleźć w **zalecenia** bloku.

![Zalecenia](./media/cache-configure/redis-cache-recommendations.png)

Można monitorować te metryki na [monitorowania wykresy](cache-how-to-monitor.md#monitoring-charts) i [wykresy użycia](cache-how-to-monitor.md#usage-charts) sekcje **pamięci podręcznej Redis** bloku.

Każda warstwa cenowa ma inną limity dla połączeń klienckich, pamięci oraz przepustowości. Jeśli pamięć podręczna zbliża się do maksymalnej pojemności dla tych metryk przez dłuższy czas, zostanie utworzony zalecenia. Aby uzyskać więcej informacji o metryki i limity przeglądane przez **zalecenia** narzędzie, zobacz poniższą tabelę:

| Metryki pamięci podręcznej redis | Więcej informacji |
| --- | --- |
| Wykorzystanie przepustowości sieci |[Wydajność pamięci podręcznej - dostępną przepustowość](cache-faq.md#cache-performance) |
| Podłączeni klienci |[Domyślna konfiguracja serwera Redis - maxclients](#maxclients) |
| Obciążenie serwera |[Wykresy użycia - obciążenia serwera Redis](cache-how-to-monitor.md#usage-charts) |
| Użycie pamięci |[Wydajność pamięci podręcznej — rozmiar](cache-faq.md#cache-performance) |

Aby uaktualnić pamięć podręczną, kliknij przycisk **Uaktualnij teraz** Aby zmienić warstwę cenową i [skali](#scale) pamięci podręcznej. Aby uzyskać więcej informacji o wyborze warstwy cenowej, zobacz [jakie oferty pamięć podręczna Redis i rozmiar należy używać?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="scale"></a>Skalowanie
Kliknij przycisk **skali** Aby wyświetlić lub zmienić warstwę cenową dla pamięci podręcznej. Aby uzyskać więcej informacji na temat skalowania, zobacz [jak pamięć podręczna Redis Azure skali](cache-how-to-scale.md).

![Warstwa cenowa pamięci podręcznej redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Rozmiar klastra redis
Kliknij przycisk **rozmiar klastra Redis (wersja ZAPOZNAWCZA)** Aby zmienić rozmiar klastra dla bieżącej pamięci podręcznej premium z włączoną funkcją klastrowania.

> [!NOTE]
> Należy pamiętać, że podczas warstwy pamięci podręcznej Redis Azure Premium został zwolniony ogólnej dostępności, funkcja Redis rozmiar klastra obecnie w wersji zapoznawczej.
> 
> 

![Rozmiar klastra redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Aby zmienić rozmiar klastra, za pomocą suwaka, lub wpisz liczbę z zakresu od 1 do 10 w **liczby niezależnych** polu tekstowym i kliknij przycisk **OK** do zapisania.

> [!IMPORTANT]
> Klaster redis jest dostępna tylko dla pamięci podręcznej Premium. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie usługi klastrowania dla usługi Azure Redis Cache w warstwie Premium](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Trwałość danych Redis
Kliknij przycisk **trwałość danych Redis** Aby włączyć, wyłączyć lub skonfiguruj trwałości danych dla pamięci podręcznej premium. Pamięć podręczna Redis Azure oferuje trwałość Redis przy użyciu [trwałości RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) lub [trwałości AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie trwałości dla podręczna Redis Azure Premium](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trwałości danych pamięci podręcznej redis jest dostępna tylko dla pamięci podręcznej Premium. 
> 
> 

### <a name="schedule-updates"></a>Aktualizacje harmonogramu
**Zaplanuj aktualizacje** bloku umożliwia wyznaczenie okna obsługi aktualizacji serwera Redis dla pamięci podręcznej. 

> [!IMPORTANT]
> Okna obsługi dotyczą tylko aktualizacje serwera Redis i nie do dowolnego Azure aktualizacji lub aktualizuje systemowi operacyjnemu maszyn wirtualnych, które hostują pamięci podręcznej.
> 
> 

![Aktualizacje harmonogramu](./media/cache-configure/redis-schedule-updates.png)

Aby określić okna obsługi, sprawdź odpowiednią dni i określ godzina rozpoczęcia okna konserwacji codziennie i kliknij **OK**. Należy pamiętać, że czas okna obsługi w formacie UTC. 

> [!IMPORTANT]
> **Zaplanuj aktualizacje** funkcjonalność jest dostępna tylko dla pamięci podręcznej warstwy Premium. Aby uzyskać więcej informacji oraz instrukcje, zobacz [administrowanie pamięć podręczna Redis Azure — Zaplanuj aktualizacje](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Replikacja geograficzna

**— Replikacja geograficzna** bloku udostępnia mechanizm do konsolidacji dwa wystąpienia pamięci podręcznej Redis Azure warstwy Premium. Jedna pamięć podręczna jest wyznaczony jako głównej połączonego pamięci podręcznej, a drugi jako dodatkowej pamięci podręcznej połączony. Dodatkowej pamięci podręcznej połączonego staje się tylko do odczytu, a dane zapisywane w pamięci podręcznej podstawowej są replikowane do dodatkowej połączonego pamięci podręcznej. Ta funkcja umożliwia replikowanie pamięci podręcznej w regionach platformy Azure.

> [!IMPORTANT]
> **Replikacja geograficzna** jest dostępna tylko dla pamięci podręcznej warstwy Premium. Aby uzyskać więcej informacji oraz instrukcje, zobacz [jak skonfigurować replikację geograficzną dla pamięci podręcznej Redis Azure](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
**Sieci wirtualnej** sekcja umożliwia konfigurowanie ustawień sieci wirtualnej dla pamięci podręcznej. Informacje na temat tworzenia pamięci podręcznej premium z sieciami Wirtualnymi obsługują i aktualizowanie jej ustawień, zobacz [Konfigurowanie obsługi sieci wirtualnej dla podręczna Redis Azure Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Ustawienia sieci wirtualnej są dostępne tylko dla pamięci podręcznej premium, które zostały skonfigurowane z obsługą sieci Wirtualnej podczas tworzenia pamięci podręcznej. 
> 
> 

### <a name="firewall"></a>Zapora

Konfigurowanie reguł zapory jest dostępna dla wszystkich warstw pamięci podręcznej Redis Azure.

Kliknij przycisk **zapory** Aby wyświetlić i skonfigurować reguły zapory dla pamięci podręcznej.

![Zapora](./media/cache-configure/redis-firewall-rules.png)

Początek i koniec zakresu adresów IP można określić reguły zapory. Reguły zapory są skonfigurowane, tylko połączenia klienckie z podanych zakresów adresów IP umożliwia nawiązywanie pamięci podręcznej. Po zapisaniu regułę zapory jest krótkie opóźnienie przed obowiązuje zasada. To opóźnienie jest zazwyczaj mniej niż minutę.

> [!IMPORTANT]
> Połączenia z pamięcią podręczną Redis Azure monitorowania systemów będą zawsze dozwolone, nawet jeśli reguły zapory są skonfigurowane.
> 
> 

### <a name="properties"></a>Właściwości
Kliknij przycisk **właściwości** do wyświetlania informacji o pamięci podręcznej, łącznie z punktu końcowego pamięci podręcznej i portów.

![Właściwości pamięci podręcznej redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Blokady
**Blokuje** sekcji służy do blokowania subskrypcji, grupy zasobów lub zasobów uniemożliwić innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie kluczowych zasobów. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

### <a name="automation-script"></a>Skrypt automatyzacji

Kliknij przycisk **skryptu automatyzacji** do tworzenia i eksportowania szablonu wdrożonych zasobów do przyszłych wdrożeń. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrożenie zasobów przy użyciu szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Ustawienia administracji
Ustawienia w **administracji** sekcji umożliwiają wykonywanie następujących zadań administracyjnych dla pamięci podręcznej. 

![Administracja](./media/cache-configure/redis-cache-administration.png)

* [Importowanie danych](#importexport)
* [Eksportowanie danych](#importexport)
* [Ponowne uruchamianie](#reboot)


### <a name="importexport"></a>Import/Export
Import/Eksport jest operacji zarządzania pamięcią podręczną Redis Azure danych, która umożliwia importowanie i eksportowanie danych w pamięci podręcznej przez importowanie i Eksportowanie migawki Redis pamięci podręcznej bazy danych (RDB) z usługi pamięć podręczna premium do stronicowych obiektów blob na koncie magazynu Azure. Importu/eksportu umożliwia migrację między różnymi wystąpieniami usługi pamięć podręczna Redis Azure lub wypełnienie pamięci podręcznej danych przed użyciem.

Import może służyć do pamięci podręcznej Redis zgodne RDB plików z dowolnego serwera Redis uruchomiona w chmurze lub środowiska, w tym Redis w systemie Linux, Windows albo dowolnego dostawcy chmury, takich jak usług Amazon Web Services i inne. Importowanie danych jest w prosty sposób tworzenia pamięci podręcznej z wstępnie wypełnione danych. Podczas procesu importowania pamięć podręczna Redis Azure ładuje RDB pliki z magazynu Azure do pamięci i wstawia klucze do pamięci podręcznej.

Eksport pozwala na wyeksportowanie danych przechowywanych w pamięci podręcznej Redis Azure do Redis zgodne pliki RDB. Ta funkcja służy do przenoszenia danych z jednego wystąpienia pamięci podręcznej Redis Azure do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy zostanie utworzony na maszynie Wirtualnej, która obsługuje wystąpienie serwera pamięci podręcznej Redis Azure, a plik jest przekazywany do konta magazynu wyznaczonego. Po zakończeniu operacji eksportowania stan powodzenia lub błędu, plik tymczasowy zostanie usunięty.

> [!IMPORTANT]
> Narzędzie importu/eksportu jest dostępna tylko dla pamięci podręcznej warstwy Premium. Aby uzyskać więcej informacji oraz instrukcje, zobacz [importować i eksportować dane w pamięci podręcznej Redis Azure](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Ponowne uruchamianie
**Ponowny rozruch** bloku umożliwia ponownego uruchomienia węzłów pamięci podręcznej. Ta możliwość ponownego uruchomienia pozwala testować aplikację odporności w przypadku awarii węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot.png)

Jeśli pamięć podręczna premium z włączoną funkcją klastrowania, można wybrać, które odłamków pamięci podręcznej, aby ponownie uruchomić system.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot-cluster.png)

Ponownie uruchomić co najmniej jeden węzeł z pamięci podręcznej, wybierz żądany węzeł i kliknij przycisk **ponowny rozruch**. Jeśli masz usługi pamięć podręczna premium z włączoną funkcją klastrowania shard(s) ponowny rozruch, a następnie kliknij przycisk Wybierz **ponowny rozruch**. Po kilku minutach ponowne uruchomienie wybranych węzłów i można je do trybu online później za kilka minut.

> [!IMPORTANT]
> Ponowne uruchomienie jest teraz dostępna dla wszystkich warstw cenowych. Aby uzyskać więcej informacji oraz instrukcje, zobacz [administrowanie pamięć podręczna Redis Azure — ponowne uruchomienie](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitorowanie

**Monitorowanie** sekcji pozwala na skonfigurowanie Diagnostyka i monitorowanie dla pamięci podręcznej Redis. Aby uzyskać więcej informacji o pamięci podręcznej Redis Azure monitorowania i diagnostyki, zobacz [jak monitorować pamięć podręczna Redis Azure](cache-how-to-monitor.md).

![Diagnostyka](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis metryk](#redis-metrics)
* [Reguły alertów](#alert-rules)
* [Diagnostyka](#diagnostics)

### <a name="redis-metrics"></a>Metryki pamięci podręcznej Redis
Kliknij przycisk **Redis metryki** do [wyświetlić metryki](cache-how-to-monitor.md#view-cache-metrics) dla pamięci podręcznej.

### <a name="alert-rules"></a>Reguły alertów

Kliknij przycisk **reguły alertów** Konfigurowanie alertów w oparciu metryki pamięci podręcznej Redis. Aby uzyskać więcej informacji, zobacz [alerty](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostyka

Domyślnie są metryki pamięci podręcznej w monitorze Azure [przechowywane przez 30 dni](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) , a następnie usuwany. Aby zachować Twoje metryki pamięci podręcznej przez czas dłuższy niż 30 dni, kliknij przycisk **diagnostyki** do [Konfigurowanie konta magazynu](cache-how-to-monitor.md#export-cache-metrics) używany do przechowywania Diagnostyka pamięci podręcznej.

>[!NOTE]
>Oprócz archiwizacji Twoje metryki pamięci podręcznej do magazynu, możesz również [strumienia je do Centrum zdarzeń lub wysłać je do analizy dzienników](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

## <a name="support--troubleshooting-settings"></a>Rozwiązywanie problemów z ustawieniami i pomoc techniczna
Ustawienia w **pomocy technicznej i rozwiązywania problemów** sekcja zawiera opcje rozwiązywania problemów związanych z pamięci podręcznej.

![Pomoc i rozwiązywanie problemów](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Kondycja zasobów](#resource-health)
* [nowe żądanie pomocy technicznej](#new-support-request)

### <a name="resource-health"></a>Kondycja zasobów
**Kondycja zasobów** oczekuje zasobu i informuje, czy działa on zgodnie z oczekiwaniami. Aby uzyskać więcej informacji o usłudze kondycji zasobów Azure, zobacz [Przegląd kondycji zasobów Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> Kondycja zasobu nie może obecnie raport dotyczący kondycji wystąpień pamięci podręcznej Redis Azure hostowanej w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [wszystkie funkcje pamięci podręcznej działają odnośnie do hostowania pamięci podręcznej w sieci Wirtualnej?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nowe żądanie pomocy technicznej
Kliknij przycisk **nowy obsługuje żądania** otwarcia żądania pomocy technicznej dla pamięci podręcznej.





## <a name="default-redis-server-configuration"></a>Domyślna konfiguracja serwera Redis
Nowe wystąpienia pamięci podręcznej Redis Azure są skonfigurowane następujące wartości konfiguracji Redis domyślne:

> [!NOTE]
> Nie można zmienić ustawienia w tej sekcji przy użyciu `StackExchange.Redis.IServer.ConfigSet` metody. Jeśli ta metoda jest wywoływana z jednego z poleceń w tej sekcji, jest zgłaszany wyjątek podobny do poniższego przykładu:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Wartości, które można skonfigurować, takie jak **Maksymalna pamięć zasady**, można skonfigurować za pomocą portalu Azure lub narzędzia do zarządzania wiersza polecenia takich jak wiersza polecenia platformy Azure lub programu PowerShell.
> 
> 

| Ustawienie | Wartość domyślna | Opis |
| --- | --- | --- |
| `databases` |16 |Domyślna liczba baz danych jest 16, ale można skonfigurować wiele różnych oparte na warstwie cenowej. <sup>1</sup> domyślna baza danych jest DB 0, możesz wybrać inną na podstawę dla każdego połączenia przy użyciu `connection.GetDatabase(dbid)` gdzie `dbid` jest liczbą z zakresu od `0` i `databases - 1`. |
| `maxclients` |Zależy od warstwy cenowej<sup>2</sup> |Ta wartość jest maksymalna liczba podłączonych klientów dozwolone w tym samym czasie. Po osiągnięciu limitu pamięci podręcznej Redis zamyka wszystkie nowe połączenia, zwróci komunikat "Osiągnięto maksymalną liczbę klientów". |
| `maxmemory-policy` |`volatile-lru` |Zasady Maxmemory jest jak Redis wybiera co do usunięcia, gdy to ustawienie `maxmemory` osiągnięciu (rozmiar pamięci podręcznej oferty wybranej podczas tworzenia pamięci podręcznej). Z pamięci podręcznej Redis Azure jest ustawieniem domyślnym `volatile-lru`, które powoduje usunięcie kluczy z wygaśnięciem ustawić za pomocą algorytmu LRU. To ustawienie można skonfigurować w portalu Azure. Aby uzyskać więcej informacji, zobacz [zasady pamięci](#memory-policies). |
| `maxmemory-samples` |3 |Aby zapisać pamięci, LRU i minimalny czas wygaśnięcia algorytmy są przybliżona algorytmy zamiast dokładne algorytmów. Domyślnie Redis kontroli trzy klucze i pobrania ten, który został użyty mniej ostatnio. |
| `lua-time-limit` |5000 |Maksymalny czas wykonywania skryptu Lua (w milisekundach). Po osiągnięciu maksymalnego czasu wykonywania Redis rejestruje skryptu nadal trwa wykonywanie po maksymalny dozwolony czas i uruchamia na udzielenie odpowiedzi na zapytania z powodu błędu. |
| `lua-event-limit` |500 |Maksymalny rozmiar kolejki zdarzeń skryptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Limity buforu wyjściowego klienta można wymusić odłączenie klientów, którzy nie są odczytywanie danych z serwera wystarczająca jakiegoś powodu (typową przyczyną jest, że klient Pub/Sub nie mogą korzystać z wiadomości tak szybko, jak wydawcy można tworzyć je). Aby uzyskać więcej informacji, zobacz [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>limit `databases` jest różne dla każdego pamięć podręczna Redis Azure warstwy cenowej i można ustawić na tworzenie pamięci podręcznej. Jeśli nie `databases` ustawienie jest określone podczas tworzenia pamięci podręcznej, wartość domyślna to 16.

* Podstawowa i standardowa pamięci podręczne
  * C0 (250 MB) pamięci podręcznej - maksymalnie 16 baz danych
  * C1 pamięci podręcznej (1 GB) — maksymalnie 16 baz danych
  * C2 pamięci podręcznej (2,5 GB) — maksymalnie 16 baz danych
  * C3 pamięci podręcznej (6 GB) — maksymalnie 16 baz danych
  * C4 (13 GB) pamięci podręcznej - maksymalnie 32 baz danych
  * C5 (26 GB) pamięci podręcznej - maksymalnie 48 baz danych
  * C6 (53 GB) pamięci podręcznej - maksymalnie 64 baz danych
* Pamięci podręczne — wersja Premium
  * P1 (6 GB — 60 GB) — maksymalnie 16 baz danych
  * P2 (13 GB - 130 GB) — maksymalnie 32 baz danych
  * P3 (26 GB - 260 GB) — maksymalnie 48 baz danych
  * P4 (53 GB - 530 GB) — maksymalnie 64 baz danych
  * Wszystkie bufory premium z klastrem Redis enabled - klaster Redis obsługuje tylko bazy danych 0 tak `databases` limit pamięci podręcznej premium żadnych włączono klaster Redis skutecznie to 1 i [wybierz](http://redis.io/commands/select) polecenie nie jest dozwolone. Aby uzyskać więcej informacji, zobacz [należy wprowadzać żadnych zmian do mojej aplikacji klienta do korzystania z klastra?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Aby uzyskać więcej informacji na temat baz danych, zobacz [co to są Redis baz danych?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases` Ustawienie może być skonfigurowany tylko podczas tworzenia pamięci podręcznej i tylko przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania. Aby uzyskać przykład konfigurowania `databases` podczas tworzenia pamięci podręcznej przy użyciu programu PowerShell, zobacz [AzureRmRedisCache nowy](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` jest różne dla każdego pamięć podręczna Redis Azure warstwy cenowej.

* Podstawowa i standardowa pamięci podręczne
  * C0 (250 MB) pamięci podręcznej - maksymalnie 256 połączeń
  * C1 pamięci podręcznej (1 GB) — maksymalnie 1000 połączeń
  * C2 pamięci podręcznej (2,5 GB) — maksymalnie 2000 połączeń
  * C3 pamięci podręcznej (6 GB) — maksymalnie 5000 połączeń
  * C4 (13 GB) pamięci podręcznej - maksymalnie 10 000 połączeń
  * C5 (26 GB) pamięci podręcznej - maksymalnie 15 000 połączeń
  * C6 (53 GB) pamięci podręcznej - maksymalnie 20 000 połączeń
* Pamięci podręczne — wersja Premium
  * P1 (6 GB — 60 GB) — maksymalnie 7500 połączeń
  * P2 (13 GB - 130 GB) — maksymalnie 15 000 połączeń
  * P3 (26 GB - 260 GB) — maksymalnie 30 000 połączeń
  * P4 (53 GB - 530 GB) — do 40 000 połączeń

> [!NOTE]
> Podczas każdego rozmiaru pamięci podręcznej umożliwia *do* określonej liczby połączeń, każdego połączenia Redis obciążenie ma skojarzonych z nim. Przykładem takich obciążenie będzie użycia Procesora i pamięci w wyniku szyfrowania TLS/SSL. Limit maksymalnej liczby połączeń dla rozmiaru pamięci podręcznej danego zakłada lekkim załadować pamięci podręcznej. Czy ładowanie z połączenia koszty *plus* obciążenia z operacjami klienta przekracza pojemność dla systemu, pamięci podręcznej mogą doświadczyć problemów dotyczących pojemności, nawet jeśli nie przekroczono limit połączeń dla bieżący rozmiar pamięci podręcznej.
> 
> 



## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis polecenia nie są obsługiwane w pamięci podręcznej Redis Azure
> [!IMPORTANT]
> Ponieważ konfiguracji i zarządzania wystąpienia pamięci podręcznej Redis Azure jest zarządzany przez firmę Microsoft, poniższe polecenia są wyłączone. Jeśli spróbujesz ich wywołać, pojawi się komunikat o błędzie podobny do `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * KONFIGURACJI
> * DEBUG
> * MIGRACJA
> * ZAPISZ
> * ZAMKNIĘCIE
> * SLAVEOF
> * KLASTER - klaster zapisu, które polecenia są wyłączone, ale tylko do odczytu klastra polecenia są dozwolone.
> 
> 

Aby uzyskać więcej informacji o poleceniach Redis, zobacz [ http://redis.io/commands ](http://redis.io/commands).

## <a name="redis-console"></a>Redis konsoli
Można bezpiecznie wysyłać polecenia do Twojego wystąpienia pamięci podręcznej Redis Azure za pomocą **Redis konsoli**, która jest dostępna w portalu Azure dla wszystkich warstw pamięci podręcznej.

> [!IMPORTANT]
> - Konsola Redis nie działa z [sieci Wirtualnej](cache-how-to-premium-vnet.md). Jeśli pamięć podręczna jest częścią sieci Wirtualnej, tylko klienci w sieci Wirtualnej można uzyskać dostępu do pamięci podręcznej. Ponieważ Redis konsola jest uruchamiana w przeglądarce lokalnego znajduje się poza siecią Wirtualną, nie może nawiązać połączenia z pamięci podręcznej.
> - Nie wszystkie polecenia Redis są obsługiwane w pamięci podręcznej Redis Azure. Listę poleceń pamięci podręcznej Redis, które są wyłączone dla pamięci podręcznej Redis Azure, zobacz poprzedni [polecenia nie są obsługiwane w pamięci podręcznej Redis Azure Redis](#redis-commands-not-supported-in-azure-redis-cache) sekcji. Aby uzyskać więcej informacji o poleceniach Redis, zobacz [ http://redis.io/commands ](http://redis.io/commands).
> 
> 

Aby uzyskać dostęp do konsoli Redis, kliknij przycisk **konsoli** z **pamięci podręcznej Redis** bloku.

![Redis konsoli](./media/cache-configure/redis-console-menu.png)

Aby wystawić poleceń w odniesieniu do wystąpienia pamięci podręcznej, po prostu wpisz odpowiedniego polecenia do konsoli.

![Redis konsoli](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Za pomocą konsoli Redis z premium w klastrze pamięci podręcznej

Gdy za pomocą konsoli Redis z premium w klastrze pamięci podręcznej, można wydać polecenia jednego niezależnego fragmentu pamięci podręcznej. Można wydać polecenie do określonych niezależnego fragmentu, najpierw połączyć się z odpowiednią niezależnego fragmentu, klikając selektor niezależnego fragmentu.

![Redis konsoli](./media/cache-configure/redis-console-premium-cluster.png)

Jeśli użytkownik spróbuje uzyskać dostępu do klucza przechowywanego w różnych niezależnych od połączonych niezależnego fragmentu, pojawi się komunikat o błędzie podobny do następującego:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

W poprzednim przykładzie niezależnego fragmentu 1 jest wybrany niezależnego fragmentu, ale `myKey` znajduje się w niezależnych 0, co wynika z `(shard 0)` części komunikatu o błędzie. W tym przykładzie, aby uzyskać dostęp do `myKey`wybierz niezależnego fragmentu 0 za pomocą selektora niezależnego fragmentu, a następnie wystawiania odpowiedniego polecenia.


## <a name="move-your-cache-to-a-new-subscription"></a>Przenieś pamięć podręczną na nową subskrypcję
Pamięć podręczną można przenieść do nowej subskrypcji, klikając **Przenieś**.

![Przenieś pamięci podręcznej Redis](./media/cache-configure/redis-cache-move.png)

Aby uzyskać informacje na przenoszenie zasobów między grupami zasobów do innej i z jedną subskrypcję do innego, zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat pracy z pamięci podręcznej Redis poleceń, zobacz [jak uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)

