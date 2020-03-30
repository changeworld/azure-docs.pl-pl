---
title: Jak skonfigurować pamięć podręczną platformy Azure dla programu Redis
description: Poznaj domyślną konfigurację redis dla usługi Azure Cache for Redis i dowiedz się, jak skonfigurować pamięć podręczną platformy Azure dla wystąpień Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: f10be8efcd2d8e838b4b5f62310eb405f6ed0158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278742"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Jak skonfigurować pamięć podręczną platformy Azure dla programu Redis
W tym temacie opisano konfiguracje dostępne dla pamięci podręcznej platformy Azure dla wystąpień Redis. W tym temacie opisano również domyślną konfigurację serwera Redis dla pamięci podręcznej azure dla wystąpień Redis.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania i używania funkcji pamięci podręcznej premium, zobacz [Jak skonfigurować trwałość](cache-how-to-premium-persistence.md), [Jak skonfigurować klastrowanie](cache-how-to-premium-clustering.md)i [Jak skonfigurować obsługę sieci wirtualnej](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurowanie pamięci podręcznej platformy Azure dla ustawień redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Ustawienia usługi Azure Cache for Redis są wyświetlane i konfigurowane w **bloku Azure Cache for Redis** przy użyciu menu **zasobów.**

![Usługa Azure Cache dla ustawień redis](./media/cache-configure/redis-cache-settings.png)

Za pomocą **menu zasobów**można wyświetlać i konfigurować następujące ustawienia.

* [Przegląd](#overview)
* [Dziennik aktywności](#activity-log)
* [Kontrola dostępu (IAM)](#access-control-iam)
* [Tagi](#tags)
* [Diagnozowanie i rozwiązywanie problemów](#diagnose-and-solve-problems)
* [Ustawienia](#settings)
    * [Klawisze dostępu](#access-keys)
    * [Ustawienia zaawansowane](#advanced-settings)
    * [Usługa Azure Cache dla redis advisor](#azure-cache-for-redis-advisor)
    * [Skali](#scale)
    * [Rozmiar klastra](#cluster-size)
    * [Trwałość danych](#redis-data-persistence)
    * [Aktualizacje harmonogramu](#schedule-updates)
    * [Geo-replication](#geo-replication) (Replikacja geograficzna)
    * [Sieć wirtualna](#virtual-network)
    * [Zapora](#firewall)
    * [Właściwości](#properties)
    * [Blokady](#locks)
    * [Skrypt usługi Automation](#automation-script)
* Administracja
    * [Importowanie danych](#importexport)
    * [Eksportowanie danych](#importexport)
    * [Ponowne uruchamianie](#reboot)
* [Monitorowania](#monitoring)
    * [Metryki Redis](#redis-metrics)
    * [Reguły alertów](#alert-rules)
    * [Diagnostyka](#diagnostics)
* Obsługa & ustawienia rozwiązywania problemów
    * [Kondycja zasobów](#resource-health)
    * [Nowe żądanie pomocy technicznej](#new-support-request)


## <a name="overview"></a>Omówienie

**Omówienie** zawiera podstawowe informacje o pamięci podręcznej, takie jak nazwa, porty, warstwa cenowa i wybrane metryki pamięci podręcznej.

### <a name="activity-log"></a>Dziennik aktywności

Kliknij **pozycję Dziennik aktywności,** aby wyświetlić akcje wykonywane w pamięci podręcznej. Można również użyć filtrowania, aby rozwinąć ten widok, aby uwzględnić inne zasoby. Aby uzyskać więcej informacji na temat pracy z dziennikami inspekcji, zobacz [Inspekcja operacji za pomocą Menedżera zasobów](../azure-resource-manager/management/view-activity-logs.md). Aby uzyskać więcej informacji na temat monitorowania pamięci podręcznej azure dla zdarzeń Redis, zobacz [operacje i alerty](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Kontrola dostępu (IAM)

Sekcja **Kontrola dostępu (IAM)** zapewnia obsługę kontroli dostępu opartej na rolach (RBAC) w witrynie Azure portal. Ta konfiguracja pomaga organizacjom w prostej i precyzyjnej realizacji wymagań dotyczących zarządzania dostępem. Aby uzyskać więcej informacji, zobacz [kontrola dostępu oparta na rolach w witrynie Azure portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Tagi

Sekcja **Znaczniki** ułatwia organizowanie zasobów. Aby uzyskać więcej informacji, zobacz [Porządkowanie zasobów na platformie Azure za pomocą tagów](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnozowanie i rozwiązywanie problemów

Kliknij **przycisk Diagnozuj i rozwiązuj problemy,** które mają być dostarczane z typowymi problemami i strategiami ich rozwiązywania.



## <a name="settings"></a>Ustawienia
Sekcja **Ustawienia** umożliwia dostęp do następujących ustawień pamięci podręcznej i konfigurowanie ich.

* [Klawisze dostępu](#access-keys)
* [Ustawienia zaawansowane](#advanced-settings)
* [Usługa Azure Cache dla redis advisor](#azure-cache-for-redis-advisor)
* [Skali](#scale)
* [Rozmiar klastra](#cluster-size)
* [Trwałość danych](#redis-data-persistence)
* [Aktualizacje harmonogramu](#schedule-updates)
* [Geo-replication](#geo-replication) (Replikacja geograficzna)
* [Sieć wirtualna](#virtual-network)
* [Zapora](#firewall)
* [Właściwości](#properties)
* [Blokady](#locks)
* [Skrypt usługi Automation](#automation-script)



### <a name="access-keys"></a>Klawisze dostępu
Kliknij **klawisze programu Access,** aby wyświetlić lub ponownie wygenerować klucze dostępu do pamięci podręcznej. Te klucze są używane przez klientów łączących się z pamięci podręcznej.

![Pamięć podręczna platformy Azure dla kluczy dostępu Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Ustawienia zaawansowane
Następujące ustawienia są skonfigurowane w bloku **Ustawienia zaawansowane.**

* [Porty dostępu](#access-ports)
* [Zasady dotyczące pamięci](#memory-policies)
* [Powiadomienia o przestrzeni kluczy (ustawienia zaawansowane)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Porty dostępu
Domyślnie dostęp inny niż za pomocą protokołu SSL jest zablokowany dla nowych pamięci podręcznych. Aby włączyć port nieobjęty SSL, kliknij przycisk **Nie** dla **zezwalaj na dostęp tylko za pośrednictwem protokołu SSL** w bloku **Ustawienia zaawansowane,** a następnie kliknij przycisk **Zapisz**.

> [!NOTE]
> Dostęp SSL do usługi Azure Cache for Redis obsługuje obecnie protokół TLS 1.0, 1.1 i 1.2, ale wersje 1.0 i 1.1 są wkrótce wycofywane.  Więcej informacji można znaleźć na naszej [stronie Usuń TLS 1.0 i 1.1.](cache-remove-tls-10-11.md)

![Pamięć podręczna platformy Azure dla portów dostępu Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zasady dotyczące pamięci
Ustawienia **maxmemory**, **maxmemory zastrzeżone**i **maxfragmentationmemory-reserved** w bloku **Ustawienia zaawansowane** konfigurują zasady pamięci dla pamięci podręcznej.

![Usługa Azure Cache dla zasad Maxmemory redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zasady Maxmemory** konfiguruje zasady eksmisji dla pamięci podręcznej i pozwala wybrać jedną z następujących zasad eksmisji:

* `volatile-lru`- Jest to domyślna polityka eksmisji.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Aby uzyskać `maxmemory` więcej informacji na temat zasad, zobacz [Zasady eksmisji](https://redis.io/topics/lru-cache#eviction-policies).

Ustawienie **zarezerwowane przez maxmemory** konfiguruje ilość pamięci w MB, która jest zarezerwowana dla operacji innych niż pamięć podręczna, takich jak replikacja podczas pracy awaryjnej. Ustawienie tej wartości pozwala mieć bardziej spójne środowisko serwera Redis, gdy obciążenie jest różna. Ta wartość powinna być ustawiona wyższa dla obciążeń, które są ciężkie zapisu. Gdy pamięć jest zarezerwowana dla takich operacji, jest niedostępna do przechowywania danych w pamięci podręcznej.

Ustawienie **zarezerwowane przez maxfragmentationmemory** konfiguruje ilość pamięci w MB, która jest zarezerwowana do pomięcia pamięci. Ustawienie tej wartości pozwala mieć bardziej spójne środowisko serwera Redis, gdy pamięć podręczna jest pełna lub bliska pełnej, a współczynnik fragmentacji jest wysoki. Gdy pamięć jest zarezerwowana dla takich operacji, jest niedostępna do przechowywania danych w pamięci podręcznej.

Jedną z rzeczy, które należy wziąć pod uwagę przy wyborze nowej wartości rezerwacji pamięci (**maxmemory-reserved** lub **maxfragmentationmemory-reserved)** jest to, jak ta zmiana może wpłynąć na pamięć podręczną, która jest już uruchomiona z dużymi ilościami danych w nim. Na przykład jeśli masz 53 GB pamięci podręcznej z 49 GB danych, a następnie zmienić wartość rezerwacji na 8 GB, ta zmiana spowoduje spadek maksymalnej dostępnej pamięci dla systemu do 45 GB. Jeśli `used_memory` bieżące lub `used_memory_rss` twoje wartości są wyższe niż nowy limit 45 GB, system `used_memory` będzie `used_memory_rss` musiał eksmitować dane, dopóki nie będzie ich 45 GB. Eksmisja może zwiększyć obciążenie serwera i fragmentację pamięci. Aby uzyskać więcej informacji na `used_memory` temat `used_memory_rss`metryk pamięci podręcznej, takich jak i , zobacz [Dostępne metryki i interwały raportowania](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Ustawienia **zarezerwowane przez maxmemory** i **maxfragmentationmemory** są dostępne tylko dla pamięci podręcznych Standard i Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Powiadomienia o przestrzeni kluczy (ustawienia zaawansowane)
Powiadomienia redis keyspace są konfigurowane w bloku **Ustawienia zaawansowane.** Powiadomienia o przestrzeni kluczy umożliwiają klientom otrzymywanie powiadomień o wystąpieniu określonych zdarzeń.

![Usługa Azure Cache dla zaawansowanych ustawień redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Powiadomienia o przestrzeni kluczy i ustawienie **zdarzeń przestrzeni kluczy powiadamiania** są dostępne tylko dla pamięci podręcznych Standard i Premium.
>
>

Aby uzyskać więcej informacji, zobacz [Redis Keyspace Notifications](https://redis.io/topics/notifications). Aby uzyskać przykładowy kod, zobacz plik [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) w przykładzie [Hello world.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Usługa Azure Cache dla redis advisor
**W bloku Usługi Azure Cache for Redis Advisor** są wyświetlane zalecenia dotyczące pamięci podręcznej. Podczas normalnych operacji nie są wyświetlane żadne zalecenia.

![Zalecenia](./media/cache-configure/redis-cache-no-recommendations.png)

Jeśli wystąpią jakiekolwiek warunki podczas operacji pamięci podręcznej, takie jak wysokie użycie pamięci, przepustowość sieci lub obciążenie serwera, alert jest wyświetlany w **pamięci podręcznej azure dla bloku Redis.**

![Zalecenia](./media/cache-configure/redis-cache-recommendations-alert.png)

Więcej informacji można znaleźć na **lemieszu zalecenia.**

![Zalecenia](./media/cache-configure/redis-cache-recommendations.png)

Można monitorować te metryki na [wykresach monitorowania](cache-how-to-monitor.md#monitoring-charts) i [wykresy użycia sekcje](cache-how-to-monitor.md#usage-charts) **azure cache dla redis** bloku.

Każda warstwa cenowa ma różne limity dla połączeń klientów, pamięci i przepustowości. Jeśli pamięć podręczna zbliża się maksymalną pojemność dla tych metryk w dłuższym okresie czasu, zostanie utworzone zalecenie. Aby uzyskać więcej informacji na temat metryk i limitów przeglądanych przez narzędzie **Zalecenia,** zobacz następującą tabelę:

| Metryka Usługi Azure Cache for Redis | Więcej informacji |
| --- | --- |
| Użycie przepustowości sieci |[Wydajność pamięci podręcznej — dostępna przepustowość](cache-faq.md#cache-performance) |
| Połączeni klienci |[Domyślna konfiguracja serwera Redis - maxclients](#maxclients) |
| Obciążenie serwera |[Wykresy użycia — redis Server Load](cache-how-to-monitor.md#usage-charts) |
| Użycie pamięci |[Wydajność pamięci podręcznej — rozmiar](cache-faq.md#cache-performance) |

Aby uaktualnić pamięć podręczną, kliknij **uaktualnij teraz,** aby zmienić warstwę cenową i [skalować](#scale) pamięć podręczną. Aby uzyskać więcej informacji na temat wybierania warstwy cenowej, [zobacz, jakiej oferty i rozmiaru usługi Redis należy używać w pamięci podręcznej Azure dla programu Redis?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Skalowanie
Kliknij **przycisk Skaluj,** aby wyświetlić lub zmienić warstwę cenową pamięci podręcznej. Aby uzyskać więcej informacji na temat skalowania, zobacz [Jak skalować pamięć podręczną platformy Azure dla redis](cache-how-to-scale.md).

![Warstwa cenowa Usługi Azure Cache for Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Rozmiar klastra Redis
Kliknij **pozycję Rozmiar klastra,** aby zmienić rozmiar klastra dla uruchomionej pamięci podręcznej premium z włączoną klastrem.

![Rozmiar klastra](./media/cache-configure/redis-cache-redis-cluster-size.png)

Aby zmienić rozmiar klastra, użyj suwaka lub wpisz liczbę z 1 do 10 w polu tekstowym **Liczba niezależnego fragmentu** i kliknij przycisk **OK,** aby zapisać.

> [!IMPORTANT]
> Klastrowanie Redis jest dostępne tylko dla pamięci podręcznych Premium. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować klastrowanie dla pamięci podręcznej premium Azure dla programu Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Trwałość danych Redis
Kliknij **pozycję Trwałość danych,** aby włączyć, wyłączyć lub skonfigurować trwałość danych dla pamięci podręcznej premium. Usługa Azure Cache for Redis oferuje trwałość redis przy użyciu [trwałości RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) lub [trwałości AOF.](cache-how-to-premium-persistence.md#configure-aof-persistence)

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować trwałość dla pamięci podręcznej premium Azure dla redis.](cache-how-to-premium-persistence.md)


> [!IMPORTANT]
> Trwałość danych Redis jest dostępna tylko dla pamięci podręcznych Premium.
>
>

### <a name="schedule-updates"></a>Aktualizacje harmonogramu
**Załącznik Aktualizacje harmonogramu** umożliwia wyznaczenie okna konserwacji dla aktualizacji serwera Redis dla pamięci podręcznej.

> [!IMPORTANT]
> Okno konserwacji dotyczy tylko aktualizacji serwera Redis, a nie żadnych aktualizacji platformy Azure lub aktualizacji systemu operacyjnego maszyn wirtualnych obsługujących pamięć podręczną.
>
>

![Aktualizacje harmonogramu](./media/cache-configure/redis-schedule-updates.png)

Aby określić okno konserwacji, sprawdź żądane dni i określ godzinę rozpoczęcia okna konserwacji dla każdego dnia, a następnie kliknij przycisk **OK**. Czas okna konserwacji jest w czasie UTC.

> [!IMPORTANT]
> Funkcja **aktualizacji harmonogramu** jest dostępna tylko w pamięci podręcznej warstwy Premium. Aby uzyskać więcej informacji i instrukcji, zobacz [Azure Cache for Redis administration - Planowanie aktualizacji](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Replikacja geograficzna

Blok **replikacji geograficznej** udostępnia mechanizm łączenia dwóch warstwy Premium w pamięci podręcznej azure dla wystąpień Redis. Jedna pamięć podręczna jest wyznaczona jako podstawowa połączona pamięć podręczna, a druga jako pomocnicza połączona pamięć podręczna. Pomocnicza połączona pamięć podręczna staje się tylko do odczytu, a dane zapisane w podstawowej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej w regionach platformy Azure.

> [!IMPORTANT]
> **Replikacja geograficzna** jest dostępna tylko w pamięci podręcznej warstwy Premium. Aby uzyskać więcej informacji i instrukcji, zobacz [Jak skonfigurować replikację geograficzną dla pamięci podręcznej platformy Azure dla programu Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
Sekcja **Sieć wirtualna** umożliwia skonfigurowanie ustawień sieci wirtualnej pamięci podręcznej. Aby uzyskać informacje na temat tworzenia pamięci podręcznej premium z obsługą sieci wirtualnej i aktualizowania jej ustawień, zobacz [Jak skonfigurować obsługę sieci wirtualnej dla pamięci podręcznej premium Azure dla programu Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Ustawienia sieci wirtualnej są dostępne tylko dla pamięci podręcznych premium, które zostały skonfigurowane z obsługą sieci wirtualnej podczas tworzenia pamięci podręcznej.
>
>

### <a name="firewall"></a>Zapora

Konfiguracja reguł zapory jest dostępna dla wszystkich warstw Usługi Azure Cache dla redis.

Kliknij **przycisk Zapora,** aby wyświetlić i skonfigurować reguły zapory dla pamięci podręcznej.

![Zapora](./media/cache-configure/redis-firewall-rules.png)

Można określić reguły zapory z początkowym i końcowym zakresem adresów IP. Gdy skonfigurowano reguły zapory, tylko połączenia klientów z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną. Po zapisaniu reguły zapory istnieje krótkie opóźnienie, zanim reguła będzie skuteczna. To opóźnienie jest zazwyczaj mniej niż jedną minutę.

> [!IMPORTANT]
> Połączenia z usługi Azure Cache dla systemów monitorowania Redis są zawsze dozwolone, nawet jeśli są skonfigurowane reguły zapory.
>
>

### <a name="properties"></a>Właściwości
Kliknij **pozycję Właściwości,** aby wyświetlić informacje o pamięci podręcznej, w tym punkt końcowy pamięci podręcznej i porty.

![Azure Cache for Redis Properties](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Blokady
**Sekcja Blokady** umożliwia zablokowanie subskrypcji, grupy zasobów lub zasobu, aby uniemożliwić innym użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie zasobów krytycznych. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/management/lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

### <a name="automation-script"></a>Skrypt usługi Automation

Kliknij **przycisk Skrypt automatyzacji,** aby utworzyć i wyeksportować szablon wdrożonych zasobów dla przyszłych wdrożeń. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Wdrażanie zasobów za pomocą szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Ustawienia administracyjne
Ustawienia w sekcji **Administracja** umożliwiają wykonywanie następujących zadań administracyjnych dla pamięci podręcznej.

![Administracja](./media/cache-configure/redis-cache-administration.png)

* [Importowanie danych](#importexport)
* [Eksportowanie danych](#importexport)
* [Ponowne uruchamianie](#reboot)


### <a name="importexport"></a>Import/Export
Import/Eksport to operacja zarządzania danymi usługi Azure Cache for Redis, która umożliwia importowanie i eksportowanie danych w pamięci podręcznej przez importowanie i eksportowanie migawki usługi Azure Cache for Redis Database (RDB) z pamięci podręcznej premium do stronicowego obiektu blob na koncie usługi Azure Storage. Import/eksport umożliwia migrację między różnymi pamięciami podręcznymi platformy Azure dla wystąpień Redis lub wypełnić pamięć podręczną danymi przed użyciem.

Import może służyć do przynoszenia zgodnych z Redis plików RDB z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym Redis działającego w systemie Linux, Windows lub dowolnym dostawcy chmury, takiego jak Amazon Web Services i innych. Importowanie danych to prosty sposób na utworzenie pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure Cache for Redis ładuje pliki RDB z magazynu platformy Azure do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Eksport umożliwia eksportowanie danych przechowywanych w pamięci podręcznej platformy Azure dla redis do plików RDB zgodnych z redis. Za pomocą tej funkcji można przenieść dane z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy jest tworzony na maszynie Wirtualnej, która obsługuje wystąpienie serwera usługi Azure Cache for Redis, a plik jest przekazywalny do wyznaczonego konta magazynu. Po zakończeniu operacji eksportowania ze stanem sukcesu lub niepowodzenia plik tymczasowy jest usuwany.

> [!IMPORTANT]
> Import/eksport jest dostępny tylko w pamięci podręcznej warstwy Premium. Aby uzyskać więcej informacji i instrukcji, zobacz [Importowanie i eksportowanie danych w pamięci podręcznej platformy Azure dla redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Ponowne uruchamianie
**Blok restartu** umożliwia ponowne uruchomienie węzłów pamięci podręcznej. Ta funkcja ponownego uruchamiania umożliwia testowanie aplikacji pod kątem odporności w przypadku awarii węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot.png)

Jeśli masz pamięć podręczną premium z włączoną klastrowania, można wybrać, które fragmenty pamięci podręcznej do ponownego uruchomienia.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić jeden lub więcej węzłów pamięci podręcznej, wybierz żądane węzły i kliknij przycisk **Uruchom ponownie**. Jeśli masz włączoną pamięć podręczną premium z włączonym klastrem, wybierz fragmenty do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie**. Po kilku minutach wybrany węzeł (węzły) ponownie uruchomi się ponownie i powróci do trybu online kilka minut później.

> [!IMPORTANT]
> Ponowne uruchomienie jest teraz dostępne dla wszystkich warstw cenowych. Aby uzyskać więcej informacji i instrukcji, zobacz [Azure Cache for Redis administration - Reboot](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorowanie

Sekcja **Monitorowanie** umożliwia skonfigurowanie diagnostyki i monitorowania pamięci podręcznej azure dla programu Redis.
Aby uzyskać więcej informacji na temat monitorowania i diagnostyki usługi Azure Cache for Redis, zobacz [Jak monitorować pamięć podręczną Platformy Azure dla programu Redis.](cache-how-to-monitor.md)

![Diagnostyka](./media/cache-configure/redis-cache-diagnostics.png)

* [Metryki Redis](#redis-metrics)
* [Reguły alertów](#alert-rules)
* [Diagnostyka](#diagnostics)

### <a name="redis-metrics"></a>Metryki Redis
Kliknij **metryki Redis,** aby [wyświetlić dane](cache-how-to-monitor.md#view-cache-metrics) pamięci podręcznej.

### <a name="alert-rules"></a>Reguły alertów

Kliknij **reguły alertów,** aby skonfigurować alerty na podstawie metryki usługi Azure Cache for Redis. Aby uzyskać więcej informacji, zobacz [Alerty](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostyka

Domyślnie metryki pamięci podręcznej w usłudze Azure Monitor są [przechowywane przez 30 dni,](../azure-monitor/platform/data-platform-metrics.md) a następnie usuwane. Aby utrwalić metryki pamięci podręcznej przez okres dłuższy niż 30 dni, kliknij pozycję **Diagnostyka,** aby [skonfigurować konto magazynu](cache-how-to-monitor.md#export-cache-metrics) używane do przechowywania diagnostyki pamięci podręcznej.

>[!NOTE]
>Oprócz archiwizowania metryk pamięci podręcznej do magazynu można również [przesyłać strumieniowo je do Centrum zdarzeń lub wysyłać je do dzienników usługi Azure Monitor.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
>
>

## <a name="support--troubleshooting-settings"></a>Obsługa & ustawienia rozwiązywania problemów
Ustawienia w sekcji **Pomoc techniczna + rozwiązywanie problemów** zapewniają opcje rozwiązywania problemów z pamięcią podręczną.

![Pomoc techniczna i rozwiązywanie problemów](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Kondycja zasobów](#resource-health)
* [Nowe żądanie pomocy technicznej](#new-support-request)

### <a name="resource-health"></a>Kondycja zasobów
**Kondycja zasobów** obserwuje zasób i informuje, czy działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat usługi kondycji zasobów platformy Azure, zobacz [omówienie kondycji zasobów platformy Azure.](../resource-health/resource-health-overview.md)

> [!NOTE]
> Kondycja zasobów nie może obecnie raportować kondycji usługi Azure Cache dla wystąpień Redis hostowanych w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Czy wszystkie funkcje pamięci podręcznej działają podczas hostowania pamięci podręcznej w sieci wirtualnej?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Nowe żądanie pomocy technicznej
Kliknij **pozycję Nowe żądanie pomocy technicznej,** aby otworzyć żądanie pomocy technicznej dla pamięci podręcznej.





## <a name="default-redis-server-configuration"></a>Domyślna konfiguracja serwera Redis
Nowa pamięć podręczna Azure dla wystąpień Redis są konfigurowane z następującymi domyślnymi wartościami konfiguracji Redis:

> [!NOTE]
> Ustawienia w tej sekcji nie `StackExchange.Redis.IServer.ConfigSet` można zmienić za pomocą metody. Jeśli ta metoda jest wywoływana z jednym z poleceń w tej sekcji, wyjątek podobny do następującego przykładu jest generowany:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Wszystkie wartości, które można konfigurować, takie jak **zasady max-memory,** można konfigurować za pomocą portalu Azure lub narzędzi do zarządzania wierszem polecenia, takich jak interfejs wiersza polecenia azure lub program PowerShell.
>
>

| Ustawienie | Wartość domyślna | Opis |
| --- | --- | --- |
| `databases` |16 |Domyślna liczba baz danych wynosi 16, ale można skonfigurować inny numer na podstawie warstwy cenowej. <sup>1</sup> Domyślną bazą danych jest DB 0, można wybrać `connection.GetDatabase(dbid)` inną `dbid` bazę `0` na `databases - 1`podstawie połączenia, używając miejsca, w którym znajduje się liczba między i . |
| `maxclients` |Zależy od warstwy<sup>cenowej 2</sup> |Ta wartość jest maksymalną dozwoloną liczbą połączonych klientów w tym samym czasie. Po osiągnięciu limitu Redis zamyka wszystkie nowe połączenia, zwracając błąd "maksymalna liczba klientów osiągnięty". |
| `maxmemory-policy` |`volatile-lru` |Zasady Maxmemory to ustawienie, w jaki sposób `maxmemory` Redis wybiera, co usunąć, gdy (rozmiar oferty pamięci podręcznej wybranej podczas tworzenia pamięci podręcznej) zostanie osiągnięty. W przypadku usługi Azure Cache `volatile-lru`for Redis domyślnym ustawieniem jest , które usuwa klucze z zestawem wygaśnięcia przy użyciu algorytmu LRU. To ustawienie można skonfigurować w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Zasady pamięci](#memory-policies). |
| `maxmemory-samples` |3 |Aby zaoszczędzić pamięć, algorytmy LRU i minimalny TTL są przybliżonymi algorytmami zamiast precyzyjnych algorytmów. Domyślnie Redis sprawdza trzy klucze i wybiera ten, który był używany mniej niedawno. |
| `lua-time-limit` |5000 |Maksymalny czas wykonywania skryptu Lua w milisekundach. Jeśli zostanie osiągnięty maksymalny czas wykonywania, Redis rejestruje, że skrypt jest nadal w wykonaniu po maksymalnym dozwolonym czasie i zaczyna odpowiadać na zapytania z błędem. |
| `lua-event-limit` |500 |Maksymalny rozmiar kolejki zdarzeń skryptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Limity buforu wyjściowego klienta mogą służyć do wymuszania rozłączenia klientów, którzy nie odczytują danych z serwera wystarczająco szybko z jakiegoś powodu (częstym powodem jest to, że klient Pub/Sub nie może korzystać z wiadomości tak szybko, jak wydawca może je wyprodukować). Aby uzyskać więcej [https://redis.io/topics/clients](https://redis.io/topics/clients)informacji, zobacz . |

<a name="databases"></a>
<sup>1.</sup> Limit dla `databases` jest inny dla każdej warstwy cenowej Azure Cache dla Redis i można ustawić w tworzeniu pamięci podręcznej. Jeśli `databases` podczas tworzenia pamięci podręcznej nie określono żadnego ustawienia, wartość domyślna to 16.

* Podstawowe i standardowe pamięci podręczne
  * Pamięć podręczna C0 (250 MB) — do 16 baz danych
  * C1 (1 GB) pamięci podręcznej - do 16 baz danych
  * C2 (2,5 GB) pamięci podręcznej - do 16 baz danych
  * Pamięć podręczna C3 (6 GB) — do 16 baz danych
  * C4 (13 GB) pamięci podręcznej - do 32 baz danych
  * C5 (26 GB) pamięci podręcznej - do 48 baz danych
  * Pamięć podręczna C6 (53 GB) — do 64 baz danych
* Pamięci podręczne premium
  * P1 (6 GB - 60 GB) - do 16 baz danych
  * P2 (13 GB - 130 GB) - do 32 baz danych
  * P3 (26 GB - 260 GB) - do 48 baz danych
  * P4 (53 GB - 530 GB) - do 64 baz danych
  * Wszystkie pamięci podręczne premium z włączonym klastrem Redis — klaster Redis obsługuje tylko korzystanie z bazy danych 0, więc `databases` limit dla dowolnej pamięci podręcznej premium z włączonym klastrem Redis jest skutecznie 1, a polecenie [Select](https://redis.io/commands/select) nie jest dozwolone. Aby uzyskać więcej informacji, zobacz [Czy muszę wprowadzić zmiany w aplikacji klienckiej, aby używać klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Aby uzyskać więcej informacji na temat baz danych, zobacz [Co to są bazy danych Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> Ustawienie `databases` można skonfigurować tylko podczas tworzenia pamięci podręcznej i tylko przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania. Na przykład konfigurowania `databases` podczas tworzenia pamięci podręcznej przy użyciu programu PowerShell zobacz [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` różni się dla każdej warstwy cenowej Azure Cache for Redis.

* Podstawowe i standardowe pamięci podręczne
  * Pamięć podręczna C0 (250 MB) — do 256 połączeń
  * Pamięć podręczna C1 (1 GB) — do 1000 połączeń
  * Pamięć podręczna C2 (2,5 GB) — do 2000 połączeń
  * Pamięć podręczna C3 (6 GB) — do 5000 połączeń
  * Pamięć podręczna C4 (13 GB) — do 10 000 połączeń
  * Pamięć podręczna C5 (26 GB) — do 15 000 połączeń
  * Pamięć podręczna C6 (53 GB) — do 20 000 połączeń
* Pamięci podręczne premium
  * P1 (6 GB - 60 GB) - do 7500 połączeń
  * P2 (13 GB - 130 GB) - do 15 000 połączeń
  * P3 (26 GB - 260 GB) - do 30 000 połączeń
  * P4 (53 GB - 530 GB) - do 40 000 połączeń

> [!NOTE]
> Podczas gdy każdy rozmiar pamięci podręcznej umożliwia *do* określonej liczby połączeń, każde połączenie z Redis ma obciążenie skojarzone z nim. Przykładem takiego obciążenia może być użycie procesora CPU i pamięci w wyniku szyfrowania TLS/SSL. Maksymalny limit połączenia dla danego rozmiaru pamięci podręcznej przyjmuje lekko załadowaną pamięć podręczną. Jeśli obciążenie z narzutu połączenia *plus* obciążenie z operacji klienta przekracza pojemność systemu, pamięć podręczna może wystąpić problemy z pojemnością, nawet jeśli nie przekroczono limitu połączenia dla bieżącego rozmiaru pamięci podręcznej.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Polecenia Redis nie są obsługiwane w pamięci podręcznej platformy Azure dla redis
> [!IMPORTANT]
> Ponieważ konfiguracja i zarządzanie pamięcią podręczną Azure dla wystąpień Redis jest zarządzany przez firmę Microsoft, następujące polecenia są wyłączone. Jeśli spróbujesz je wywołać, zostanie wyświetlony `"(error) ERR unknown command"`komunikat o błędzie podobny do .
>
> * BGREWRITEAOF (BGREWRITEAOF)
> * BGSAVE (BGSAVE)
> * Config
> * DEBUGUJ
> * Migracji
> * ZAPISZ
> * Zamknięcia
> * NIEWOLNIKOF
> * CLUSTER — polecenia zapisu klastra są wyłączone, ale dozwolone są polecenia klastra tylko do odczytu.
>
>

Aby uzyskać więcej informacji o [https://redis.io/commands](https://redis.io/commands)poleceniach Redis, zobacz .

## <a name="redis-console"></a>Konsola Redis
Można bezpiecznie wydawać polecenia w pamięci podręcznej platformy Azure dla wystąpień Redis przy użyciu **konsoli Redis**, która jest dostępna w witrynie Azure portal dla wszystkich warstw pamięci podręcznej.

> [!IMPORTANT]
> - Konsola Redis nie działa z [siecią wirtualną](cache-how-to-premium-vnet.md). Gdy pamięć podręczna jest częścią sieci wirtualnej, tylko klienci w sieci wirtualnej mogą uzyskać dostęp do pamięci podręcznej. Ponieważ konsola Redis działa w przeglądarce lokalnej, która znajduje się poza siecią wirtualną, nie może połączyć się z pamięcią podręczną.
> - Nie wszystkie polecenia Redis są obsługiwane w pamięci podręcznej Azure dla firmy Redis. Aby uzyskać listę poleceń Redis, które są wyłączone dla usługi Azure Cache for Redis, zobacz poprzednie polecenia Redis nie są obsługiwane w sekcji [Azure Cache for Redis.](#redis-commands-not-supported-in-azure-cache-for-redis) Aby uzyskać więcej informacji o [https://redis.io/commands](https://redis.io/commands)poleceniach Redis, zobacz .
>
>

Aby uzyskać dostęp do konsoli Redis, kliknij pozycję **Konsola** z **bloku Azure Cache for Redis.**

![Konsola Redis](./media/cache-configure/redis-console-menu.png)

Aby wydać polecenia względem wystąpienia pamięci podręcznej, wpisz żądane polecenie w konsoli.

![Konsola Redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Korzystanie z konsoli Redis z klastrowaną pamięcią podręczną premium

Korzystając z konsoli Redis z klasą premium pamięci podręcznej, można wydawać polecenia do pojedynczego fragmentu pamięci podręcznej. Aby wydać polecenie do określonego niezależnego fragmentu, najpierw połącz się z żądanym fragmentem, klikając go na selektorze niezależnego fragmentu.

![Konsola Redis](./media/cache-configure/redis-console-premium-cluster.png)

Jeśli spróbujesz uzyskać dostęp do klucza, który jest przechowywany w innym niezależniu niż połączony niezależnego fragmentu, zostanie wyświetlony komunikat o błędzie podobny do następującego komunikatu:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

W poprzednim przykładzie niezależnego fragmentu 1 `myKey` jest wybranym fragmentem, ale znajduje `(shard 0)` się w niezależnej części 0, zgodnie z częścią komunikatu o błędzie. W tym przykładzie, aby uzyskać dostęp, `myKey`wybierz fragment 0 za pomocą selektora niezależnego fragmentu, a następnie wydaj żądane polecenie.


## <a name="move-your-cache-to-a-new-subscription"></a>Przenoszenie pamięci podręcznej do nowej subskrypcji
Możesz przenieść pamięć podręczną do nowej subskrypcji, klikając przycisk **Przenieś**.

![Przenoszenie pamięci podręcznej platformy Azure dla redis](./media/cache-configure/redis-cache-move.png)

Aby uzyskać informacje dotyczące przenoszenia zasobów z jednej grupy zasobów do drugiej i z jednej subskrypcji do drugiej, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat pracy z poleceniami Redis, zobacz [Jak uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)
