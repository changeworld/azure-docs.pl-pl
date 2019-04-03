---
title: Jak skonfigurować pamięć podręczna systemu Azure dla pamięci podręcznej Redis | Dokumentacja firmy Microsoft
description: Zrozumienie domyślną konfigurację pamięci podręcznej Redis dla usługi Azure Cache Redis i Dowiedz się, jak skonfigurować pamięć podręczną Azure dla wystąpienia usługi Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 6bf42406c97ccb67251a14a7a963d3da2e01dbb4
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849969"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Jak skonfigurować pamięć podręczna systemu Azure dla usługi Redis
W tym temacie opisano konfiguracje dla pamięci podręcznej Azure dla wystąpienia usługi Redis. W tym temacie omówiono również domyślne Redis konfiguracji serwera dla usługi Azure Cache do wystąpienia usługi Redis.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania i używania funkcje pamięci podręcznej w warstwie premium, zobacz [Konfigurowanie trwałości](cache-how-to-premium-persistence.md), [Konfigurowanie klastrowania](cache-how-to-premium-clustering.md), i [Konfigurowanie obsługi sieci wirtualnej ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurowanie usługi Azure Cache ustawień pamięci podręcznej Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Pamięć podręczna systemu Azure dla ustawień pamięci podręcznej Redis są wyświetlane i skonfigurowane na **pamięci podręcznej Redis Azure** za pomocą bloku **Menu zasobów**.

![Pamięć podręczna systemu Azure dla ustawień pamięci podręcznej Redis](./media/cache-configure/redis-cache-settings.png)

Można wyświetlić i skonfigurować następujące ustawienia przy użyciu **Menu zasobów**.

* [Omówienie](#overview)
* [Dziennik aktywności](#activity-log)
* [Kontrola dostępu (IAM)](#access-control-iam)
* [Tagi](#tags)
* [Diagnozowanie i rozwiązywanie problemów](#diagnose-and-solve-problems)
* [Ustawienia](#settings)
    * [Klucze dostępu](#access-keys)
    * [Ustawienia zaawansowane](#advanced-settings)
    * [Pamięć podręczna systemu Azure dla usługi redis Cache Advisor](#azure-cache-for-redis-advisor)
    * [Skalowanie](#scale)
    * [Rozmiar klastra redis](#cluster-size)
    * [Trwałość danych Redis](#redis-data-persistence)
    * [Aktualizacje harmonogramu](#schedule-updates)
    * [Geo-replication](#geo-replication) (Replikacja geograficzna)
    * [Virtual Network](#virtual-network)
    * [Zapora](#firewall)
    * [Właściwości](#properties)
    * [Blokady](#locks)
    * [Skrypt automatyzacji](#automation-script)
* Administracja
    * [Importowanie danych](#importexport)
    * [Eksportowanie danych](#importexport)
    * [Ponowne uruchamianie](#reboot)
* [Monitorowanie](#monitoring)
    * [Metryki pamięci podręcznej redis](#redis-metrics)
    * [Reguły alertów](#alert-rules)
    * [Diagnostyka](#diagnostics)
* Pomoc techniczna i rozwiązywanie problemów z ustawieniami
    * [Usługa Resource health](#resource-health)
    * [Nowe żądanie obsługi](#new-support-request)


## <a name="overview"></a>Przegląd

**Omówienie** zapewnia użytkownikowi podstawowe informacje o pamięci podręcznej, takie jak nazwa, portów, warstwa cenowa i metryki pamięci podręcznej wybrane.

### <a name="activity-log"></a>Dziennik aktywności

Kliknij przycisk **dziennika aktywności** Aby wyświetlić akcje wykonywane dla pamięci podręcznej. Umożliwia także filtrowanie rozwinąć ten widok, aby zostaną umieszczone inne zasoby. Aby uzyskać więcej informacji na temat pracy z dziennikami inspekcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](../azure-resource-manager/resource-group-audit.md). Aby uzyskać więcej informacji na temat monitorowania usługi Azure Cache dla zdarzeń dotyczących pamięci podręcznej Redis, zobacz [działania i alerty](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Kontrola dostępu (IAM)

**Kontrola dostępu (IAM)** sekcji zapewnia obsługę kontroli dostępu opartej na rolach (RBAC) w witrynie Azure portal. Taka konfiguracja pozwala organizacjom wymagań ich dostępu do zarządzania po prostu i dokładnie. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach w witrynie Azure portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Tagi

**Tagi** sekcja ułatwia organizowanie zasobów. Aby uzyskać więcej informacji, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnozowanie i rozwiązywanie problemów

Kliknij przycisk **diagnozowanie i rozwiązywanie problemów** dostarczanych z najczęściej występujących problemów i strategie dotyczące rozwiązywania tych problemów.



## <a name="settings"></a>Ustawienia
**Ustawienia** sekcja umożliwia dostęp i skonfiguruj następujące ustawienia dla swojej pamięci podręcznej.

* [Klucze dostępu](#access-keys)
* [Ustawienia zaawansowane](#advanced-settings)
* [Pamięć podręczna systemu Azure dla usługi redis Cache Advisor](#azure-cache-for-redis-advisor)
* [Skalowanie](#scale)
* [Rozmiar klastra redis](#cluster-size)
* [Trwałość danych Redis](#redis-data-persistence)
* [Aktualizacje harmonogramu](#schedule-updates)
* [Geo-replication](#geo-replication) (Replikacja geograficzna)
* [Virtual Network](#virtual-network)
* [Zapora](#firewall)
* [Właściwości](#properties)
* [Blokady](#locks)
* [Skrypt automatyzacji](#automation-script)



### <a name="access-keys"></a>Klawisze dostępu
Kliknij przycisk **klucze dostępu** można wyświetlić lub ponownie wygenerować klucze dostępu pamięci podręcznej. Te klucze są używane przez klientów nawiązywania połączenia z pamięci podręcznej.

![Pamięć podręczna systemu Azure, aby uzyskać klucze dostępu pamięci podręcznej Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Ustawienia zaawansowane
Następujące ustawienia są konfigurowane na **Zaawansowane ustawienia** bloku.

* [Porty dostępu](#access-ports)
* [Zasady pamięci](#memory-policies)
* [Powiadomienia przestrzeni kluczy (ustawienia zaawansowane)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Porty dostępowe
Domyślnie dostęp inny niż za pomocą protokołu SSL jest zablokowany dla nowych pamięci podręcznych. Aby włączyć port bez protokołu SSL, kliknij przycisk **nie** dla **zezwolić na dostęp tylko za pomocą protokołu SSL** na **Zaawansowane ustawienia** bloku, a następnie kliknij przycisk **Zapisz**.

> [!NOTE]
> Domyślnie SSL dostęp do pamięci podręcznej Azure redis Cache obsługuje protokół TLS 1.0. Minimalna obsługiwana wersja protokołu TLS można go zwiększyć do protokołu TLS 1.2, w razie potrzeby za pomocą **wersję protokołu TLS minimalne** menu rozwijane **Zaawansowane ustawienia** bloku, a następnie kliknij przycisk **Zapisz**.

![Pamięć podręczna systemu Azure dla portów dostępu do pamięci podręcznej Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zasady pamięci
**Zasad dotyczących maksymalnego rozmiaru pamięci**, **zastrzeżone maxmemory**, i **zastrzeżone maxfragmentationmemory** ustawień na **Zaawansowane ustawienia** Blok konfigurowania zasad pamięci dla pamięci podręcznej.

![Pamięć podręczna systemu Azure dla zasad dotyczących maksymalnego rozmiaru pamięci Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zasad dotyczących maksymalnego rozmiaru pamięci** konfiguruje zasady eksmisji pamięci podręcznej, można wybrać z następujących zasad eksmisji:

* `volatile-lru` — To domyślne zasady eksmisji.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Aby uzyskać więcej informacji na temat `maxmemory` zasad, zobacz [zasady eksmisji](https://redis.io/topics/lru-cache#eviction-policies).

**Zastrzeżone maxmemory** ustawienie określa ilość pamięci w Megabajtach, zarezerwowane dla operacji pamięć podręczna, takich jak replikacji podczas pracy awaryjnej. Ustawienie tej wartości umożliwia mają spójniejsze środowisko pracy serwera Redis, gdy zmienia się obciążenia. Nowsze dla obciążeń, które są zapisu ciężkich należy ustawić tę wartość. Jeśli pamięci jest zarezerwowana dla takich operacji, jest niedostępna dla magazynu danych w pamięci podręcznej.

**Zastrzeżone maxfragmentationmemory** ustawienie określa ilość pamięci w Megabajtach, zarezerwowaną w taki sposób, aby pomieścić fragmentacji pamięci. Ustawienie tej wartości umożliwia bardziej spójne środowisko serwera Redis po zapełnieniu pamięci podręcznej lub blisko pełnej i fragmentacji jest wysoki współczynnik. Jeśli pamięci jest zarezerwowana dla takich operacji, jest niedostępna dla magazynu danych w pamięci podręcznej.

Jedną z rzeczy, wybierając nową wartość rezerwacji pamięci (**zastrzeżone maxmemory** lub **zastrzeżone maxfragmentationmemory**) jest, jak ta zmiana może mieć wpływ na pamięć podręczną, która jest już uruchomiony z duże ilości danych w nim. Na przykład jeśli masz 53 GB pamięci podręcznej z 49 GB danych, a następnie zmień wartość rezerwacji na 8 GB, ta zmiana spowoduje porzucenie maksymalna ilość dostępnej pamięci systemu do 45 GB. Jeśli bieżące `used_memory` lub `used_memory_rss` wartości są większe niż nowy limit równy 45 GB, a następnie system będzie musiał eksmitują dane przed zakończeniem `used_memory` i `used_memory_rss` znajdują się poniżej 45 GB. Wykluczenia może zwiększyć fragmentacji obciążenia i pamięci serwera. Aby uzyskać więcej informacji dotyczących metryk pamięci podręcznej, takie jak `used_memory` i `used_memory_rss`, zobacz [dostępne metryki i raportowanie interwałów](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> **Zastrzeżone maxmemory** i **zastrzeżone maxfragmentationmemory** ustawienia są dostępne tylko dla Standard i Premium buforuje.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Powiadomienia przestrzeni kluczy (ustawienia zaawansowane)
Redis na skonfigurowano powiadomienia przestrzeni kluczy **Zaawansowane ustawienia** bloku. Powiadomienia przestrzeni kluczy pozwalają klientom odbierać powiadomienia w przypadku wystąpienia określonych zdarzeń.

![Ustawienia zaawansowane pamięć podręczna systemu Azure dla usługi Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Powiadomienia przestrzeni kluczy i **powiadomienia przestrzeni kluczy — zdarzenia** ustawienia są dostępne tylko dla pamięci podręcznych Standard i Premium.
> 
> 

Aby uzyskać więcej informacji, zobacz [Redis powiadomienia przestrzeni kluczy](https://redis.io/topics/notifications). Przykładowy kod, zobacz [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) w pliku [Witaj, świecie](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) próbki.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Pamięć podręczna systemu Azure dla usługi redis Cache Advisor
**Pamięci podręcznej Azure redis Cache Advisor** bloku wyświetlane są zalecenia dotyczące pamięci podręcznej. Podczas normalnego działania są wyświetlane nie zalecenia. 

![Zalecenia](./media/cache-configure/redis-cache-no-recommendations.png)

Jeśli występują warunki podczas operacji takich jak wysokiego użycia pamięci, przepustowości i obciążenie serwera pamięci podręcznej, zostanie wyświetlony alert, na **pamięci podręcznej Redis Azure** bloku.

![Zalecenia](./media/cache-configure/redis-cache-recommendations-alert.png)

Więcej informacji można znaleźć w **zalecenia** bloku.

![Zalecenia](./media/cache-configure/redis-cache-recommendations.png)

Możesz monitorować te metryki na [wykresy monitorowania](cache-how-to-monitor.md#monitoring-charts) i [wykresy użycia](cache-how-to-monitor.md#usage-charts) sekcje **pamięci podręcznej Redis Azure** bloku.

Każda warstwa cenowa ma różne limity dla połączeń klienta, pamięci i przepustowość. Jeśli pamięć podręczna zbliża się do maksymalnej pojemności dla następujących metryk przez dłuższy czas, zalecenie jest tworzone. Aby uzyskać więcej informacji na temat metryk i przeglądane przez limity **zalecenia** narzędzie, zobacz poniższą tabelę:

| Pamięć podręczna systemu Azure dla metryki pamięci podręcznej Redis | Więcej informacji |
| --- | --- |
| Wykorzystanie przepustowości sieci |[Wydajność pamięci podręcznej — dostępnej przepustowości](cache-faq.md#cache-performance) |
| Podłączeni klienci |[Domyślna konfiguracja serwera Redis — maxclients](#maxclients) |
| Obciążenie serwera |[Wykresy użycia - obciążenie serwera Redis](cache-how-to-monitor.md#usage-charts) |
| Użycie pamięci |[Wydajność pamięci podręcznej — rozmiar](cache-faq.md#cache-performance) |

Aby uaktualnić pamięć podręczną, kliknij przycisk **Uaktualnij teraz** Aby zmienić warstwę cenową i [skalowania](#scale) pamięci podręcznej. Aby uzyskać więcej informacji na temat wybierania warstwy cenowej, zobacz [jakich pamięć podręczna systemu Azure, oferty pamięci podręcznej Redis i rozmiaru należy używać?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Skalowanie
Kliknij przycisk **skalowania** Aby wyświetlić lub zmienić warstwę cenową dla pamięci podręcznej. Aby uzyskać więcej informacji na temat skalowania, zobacz [jak skalowanie pamięci podręcznej Azure dla usługi Redis](cache-how-to-scale.md).

![Pamięć podręczna systemu Azure dla usługi Redis warstwy cenowej](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Rozmiar klastra redis
Kliknij przycisk **rozmiar klastra redis Cache (wersja ZAPOZNAWCZA)** można zmienić rozmiar klastra działającego cache w warstwie premium przy włączonym klastrowaniu.

> [!NOTE]
> Należy pamiętać, że podczas udostępnieniu ogólną dostępność pamięci podręcznej Redis w warstwie Premium usługi Azure Redis rozmiar klastra funkcja jest obecnie dostępna w wersji zapoznawczej.
> 
> 

![Rozmiar klastra redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Aby zmienić rozmiar klastra, za pomocą suwaka, lub wpisz liczbę z zakresu od 1 do 10 w **liczba fragmentów** polu tekstowym i kliknij **OK** do zapisania.

> [!IMPORTANT]
> Klastrowanie redis jest dostępna tylko dla pamięci podręcznych Premium. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrowania dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Trwałość danych Redis
Kliknij przycisk **trwałość danych Redis** włączyć, wyłączyć lub konfigurowanie trwałości danych dla swojej pamięci podręcznej w warstwie premium. Pamięć podręczna systemu Azure dla usługi Redis zapewnia trwałość pamięci podręcznej Redis przy użyciu [trwałości RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) lub [trwałości AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie trwałości dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trwałość danych redis Cache jest dostępna tylko dla pamięci podręcznych Premium.
> 
> 

### <a name="schedule-updates"></a>Aktualizacje harmonogramu
**Lizacje** bloku pozwala na określenie okna obsługi aktualizacji serwera Redis dla pamięci podręcznej. 

> [!IMPORTANT]
> Okna obsługi dotyczą tylko aktualizacje serwera Redis i nie Azure aktualizacji lub aktualizacje systemu operacyjnego maszyn wirtualnych, które hostują pamięci podręcznej.
> 
> 

![Aktualizacje harmonogramu](./media/cache-configure/redis-schedule-updates.png)

Aby określić okna obsługi, sprawdź odpowiednią dni i określ godzina rozpoczęcia okna konserwacji za każdy dzień i kliknij **OK**. Czas okna obsługi jest w formacie UTC.

> [!IMPORTANT]
> **Lizacje** funkcja jest dostępna tylko dla warstwy Premium w pamięci podręcznej. Aby uzyskać więcej informacji oraz instrukcje, zobacz [pamięci podręcznej Azure dla administracji usługi Redis — Zaplanuj aktualizacje](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Replikacja geograficzna

**Geografickou replikaci** bloku udostępnia mechanizm do łączenia dwóch warstwa Premium usługi Azure Cache wystąpienia usługi Redis. Jeden pamięci podręcznej nazw jest wyznaczony jako podstawowy połączonej pamięci podręcznej, a drugi jako pomocniczy połączonej pamięci podręcznej. Pomocniczy połączonej pamięci podręcznej staje się tylko do odczytu, a dane zapisywane w głównej pamięci podręcznej są replikowane do dodatkowej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej w różnych regionach platformy Azure.

> [!IMPORTANT]
> **Replikacja geograficzna** jest dostępna tylko dla warstwy Premium w pamięci podręcznej. Aby uzyskać więcej informacji oraz instrukcje, zobacz [jak skonfigurować replikację geograficzną dla usługi Azure Cache dla pamięci podręcznej Redis](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
**Sieci wirtualnej** sekcja umożliwia konfigurowanie ustawień sieci wirtualnej dla pamięci podręcznej. Informacje dotyczące tworzenia cache w warstwie premium z sieciami Wirtualnymi obsługuje i aktualizowanie jej ustawienia, zobacz [jak skonfigurować obsługę sieci wirtualnej dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Ustawienia sieci wirtualnej są dostępne tylko dla pamięci podręcznych premium, które zostały skonfigurowane za pomocą obsługi sieci Wirtualnej podczas tworzenia pamięci podręcznej. 
> 
> 

### <a name="firewall"></a>Zapora

Konfigurowanie reguł zapory jest dostępna dla wszystkich pamięci podręcznej Azure redis Cache w warstwach.

Kliknij przycisk **zapory** można wyświetlić i skonfigurować reguły zapory dla pamięci podręcznej.

![Zapora](./media/cache-configure/redis-firewall-rules.png)

Początek i koniec zakresu adresów IP można określić reguły zapory. Po skonfigurowaniu reguły zapory są tylko połączenia klienckie z podanych zakresów adresów IP mogą łączyć się z pamięci podręcznej. Po zapisaniu reguły zapory jest krótkie opóźnienie przed obowiązuje reguły. To opóźnienie jest zazwyczaj mniej niż minutę.

> [!IMPORTANT]
> Połączenia z usługi Azure Cache dla systemów monitorowania usługi Redis będą zawsze dozwolone, nawet jeśli reguły zapory są skonfigurowane.
> 
> 

### <a name="properties"></a>Właściwości
Kliknij przycisk **właściwości** do wyświetlania informacji o pamięci podręcznej, łącznie z punktu końcowego pamięci podręcznej i portami.

![Pamięć podręczna systemu Azure dla właściwości pamięci podręcznej Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Blokady
**Blokuje** sekcji służy do blokowania subskrypcji, grupy zasobów lub zasobu, aby uniemożliwić innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie zasoby o znaczeniu krytycznym. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

### <a name="automation-script"></a>Skrypt automatyzacji

Kliknij przycisk **skrypt automatyzacji** do tworzenia i eksportowania szablonu wdrożonych zasobów do przyszłych wdrożeń. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Ustawienia administracji
Ustawienia w **administracji** sekcji pozwalają wykonywać następujące zadania administracyjne dla pamięci podręcznej. 

![Administracja](./media/cache-configure/redis-cache-administration.png)

* [Importowanie danych](#importexport)
* [Eksportowanie danych](#importexport)
* [Ponowne uruchamianie](#reboot)


### <a name="importexport"></a>Import/Export
Import/Export jest usługa Azure Cache dla operacji zarządzania danych Redis, dzięki czemu można importować i eksportować dane z pamięci podręcznej przez importowanie i eksportowanie usługi Azure Cache dla migawki bazy danych Redis (RDB) z cache w wersji premium do stronicowych obiektów blob na koncie usługi Azure Storage. Import/Export umożliwia migrację między różne usługi Azure Cache dla wystąpień usługi Redis lub wypełnienie pamięci podręcznej z danymi, przed użyciem.

Import może służyć do dostosowania zgodne pliki RDB pamięci podręcznej Redis z dowolnego serwera Redis, działające w dowolnej chmurze lub środowisku, w tym Redis działających w systemie Linux, Windows lub wśród dostawców chmury, takich jak usługi Amazon Web Services i inne. Importowanie danych jest łatwe tworzenie pamięci podręcznej z wstępnie wypełniony danymi. Podczas procesu importowania pamięci podręcznej Redis Azure ładuje pliki RDB z usługi Azure storage do pamięci i wstawia klucze w pamięci podręcznej.

Eksport umożliwia eksportowanie danych przechowywanych w pamięci podręcznej Azure zgodne pliki RDB pamięci podręcznej Redis do pamięci podręcznej Redis. Ta funkcja służy do przenoszenia danych z jednej usługi Azure Cache dla wystąpienia usługi Redis do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy zostanie utworzony na maszynie Wirtualnej, który jest hostem usługi Azure Cache dla wystąpienia serwera Redis, a plik jest przekazywany do konta magazynu wyznaczonego. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie, plik tymczasowy zostanie usunięty.

> [!IMPORTANT]
> Import/Export jest dostępna tylko dla warstwy Premium w pamięci podręcznej. Aby uzyskać więcej informacji oraz instrukcje, zobacz [importowanie i eksportowanie danych w pamięci podręcznej Azure dla usługi Redis](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Ponowne uruchamianie
**Ponowny rozruch** bloku pozwala na ponowne uruchomienie węzłów pamięci podręcznej. Ta możliwość ponownego uruchomienia umożliwia testowanie aplikacji pod kątem odporności, jeśli wystąpi awaria węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot.png)

Jeśli masz cache w warstwie premium przy włączonym klastrowaniu można wybrać które fragmentu pamięci podręcznej, ponowne uruchomienie.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić co najmniej jeden węzeł pamięci podręcznej, wybierz żądaną węzłów i kliknij **ponowny rozruch**. Jeśli masz cache w warstwie premium przy włączonym klastrowaniu Wybierz fragmenty do ponownego rozruchu, a następnie kliknij przycisk **ponowny rozruch**. Po kilku minutach ponowne uruchomienie wybranych węzłów oraz że do trybu online później za kilka minut.

> [!IMPORTANT]
> Ponowne uruchomienie jest teraz dostępna dla wszystkich warstw cenowych. Aby uzyskać więcej informacji oraz instrukcje, zobacz [pamięci podręcznej Azure dla administracji usługi Redis — ponowny rozruch](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitorowanie

**Monitorowanie** sekcja umożliwia konfigurowanie Diagnostyka i monitorowanie dla pamięci podręcznej Azure redis Cache. Aby uzyskać więcej informacji na temat usługi Azure Cache do pamięci podręcznej Redis, monitorowania i diagnostyki, zobacz [jak monitorować pamięć podręczna systemu Azure dla usługi Redis](cache-how-to-monitor.md).

![Diagnostyka](./media/cache-configure/redis-cache-diagnostics.png)

* [Metryki pamięci podręcznej redis](#redis-metrics)
* [Reguły alertów](#alert-rules)
* [Diagnostyka](#diagnostics)

### <a name="redis-metrics"></a>Metryki pamięci podręcznej Redis
Kliknij przycisk **metryki pamięci podręcznej Redis** do [wyświetlaj metryki](cache-how-to-monitor.md#view-cache-metrics) dla pamięci podręcznej.

### <a name="alert-rules"></a>Reguły alertów

Kliknij przycisk **reguły alertów** Konfigurowanie alertów w oparciu o pamięć podręczna systemu Azure dla metryki pamięci podręcznej Redis. Aby uzyskać więcej informacji, zobacz [alerty](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostyka

Domyślnie są metryki pamięci podręcznej w usłudze Azure Monitor [przechowywane przez 30 dni](../azure-monitor/platform/data-platform-metrics.md) a następnie usuwane. Aby zachować swoje metryki pamięci podręcznej przez czas dłuższy niż 30 dni, kliknij przycisk **diagnostyki** do [Konfigurowanie konta magazynu](cache-how-to-monitor.md#export-cache-metrics) używany do przechowywania diagnostykę pamięci podręcznej.

>[!NOTE]
>Oprócz archiwizacji metryk pamięci podręcznej do magazynu, możesz również [przekazywać je strumieniowo do Centrum zdarzeń lub wysłać je do usługi Azure Monitor dzienniki](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Pomoc techniczna i rozwiązywanie problemów z ustawieniami
Ustawienia w **pomoc techniczna i rozwiązywanie problemów z** sekcji Podaj opcje dotyczące rozwiązywania problemów związanych z pamięci podręcznej.

![Pomoc techniczna i rozwiązywanie problemów](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Usługa Resource health](#resource-health)
* [Nowe żądanie obsługi](#new-support-request)

### <a name="resource-health"></a>Kondycja zasobów
**Usługa Resource health** monitoruje zasób i informuje, czy działa on zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat usługi kondycji zasobów platformy Azure, zobacz [Przegląd kondycji zasobów platformy Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> Usługa Resource health jest obecnie nie można raportować kondycję pamięć podręczna systemu Azure dla wystąpienia usługi Redis hostowanych w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [wszystkie funkcje pamięci podręcznej działają w przypadku hostowania w sieci Wirtualnej pamięci podręcznej?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nowe żądanie pomocy technicznej
Kliknij przycisk **nowe żądanie obsługi** otwarcia żądania pomocy technicznej dla swojej pamięci podręcznej.





## <a name="default-redis-server-configuration"></a>Domyślna konfiguracja serwera Redis
Nowej usługi Azure Cache dla wystąpień usługi Redis są konfigurowane przy użyciu następujących domyślnych wartości konfiguracji pamięci podręcznej Redis:

> [!NOTE]
> Nie można zmienić ustawienia w tej sekcji przy użyciu `StackExchange.Redis.IServer.ConfigSet` metody. Jeśli ta metoda jest wywoływana za pomocą jednego polecenia w tej sekcji, jest zgłaszany wyjątek podobny do poniższego przykładu:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Wszelkie wartości, które można skonfigurować, takie jak **Maksymalna pamięć zasad**, można skonfigurować za pomocą witryny Azure portal lub narzędzi wiersza polecenia do zarządzania, takich jak wiersza polecenia platformy Azure lub programu PowerShell.
> 
> 

| Ustawienie | Wartość domyślna | Opis |
| --- | --- | --- |
| `databases` |16 |Domyślna liczba baz danych ma wartość 16, ale można skonfigurować różne liczby na podstawie warstwy cenowej. <sup>1</sup> domyślna baza danych jest DB 0, możesz wybrać inną na poszczególnych połączeń przy użyciu `connection.GetDatabase(dbid)` gdzie `dbid` jest liczbą z zakresu od `0` i `databases - 1`. |
| `maxclients` |Zależy od warstwy cenowej<sup>2</sup> |Ta wartość jest maksymalna liczba podłączonych klientów jest dozwolona w tym samym czasie. Po osiągnięciu limitu pamięci podręcznej Redis zamyka wszystkie nowe połączenia zwróci komunikat "Osiągnięto maksymalną liczbę klientów". |
| `maxmemory-policy` |`volatile-lru` |Zasad dotyczących maksymalnego rozmiaru pamięci jest jak Redis wybiera co do usunięcia, kiedy to ustawienie `maxmemory` osiągnięciu (rozmiar pamięci podręcznej, oferty, wybrane podczas tworzenia pamięci podręcznej). Z pamięci podręcznej Azure redis Cache jest ustawieniem domyślnym `volatile-lru`, co spowoduje usunięcie kluczy z wygaśnięcia, można ustawić przy użyciu algorytmu LRU. To ustawienie można skonfigurować w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [zasad pamięci](#memory-policies). |
| `maxmemory-samples` |3 |Zapisanie w pamięci, LRU i minimalny czas wygaśnięcia algorytmy są przybliżonego algorytmy zamiast dokładne algorytmów. Domyślnie usługa Redis trzy klucze kontrole i wybór ten, który był ostatnio używany podczas mniej. |
| `lua-time-limit` |5000 |Maksymalny czas wykonywania skryptu Lua (w milisekundach). Jeśli osiągnięty zostanie maksymalny czas wykonywania, Redis, rejestruje nadal trwa wykonywanie po maksymalny dozwolony czas skryptu i uruchamia na udzielenie odpowiedzi na zapytania z powodu błędu. |
| `lua-event-limit` |500 |Maksymalny rozmiar kolejki zdarzeń skryptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Limity buforu danych wyjściowych klienta można wymusić odłączenie klientów, którzy nie są odczytywanie danych z serwera wystarczająco szybko przyczyny (typową przyczyną jest, czy klient Pub/Sub nie mogą korzystać z tak szybko, jak Wydawca może tworzyć je wiadomości). Aby uzyskać więcej informacji, zobacz [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>limit `databases` różni się dla każdej usługi Azure Cache dla warstwy cenowej usługi Redis i mogą zostać ustawione podczas tworzenia pamięci podręcznej. Jeśli nie `databases` ustawienie jest określane podczas tworzenia pamięci podręcznej, wartość domyślna to 16.

* Podstawowa i standardowa pamięci podręcznych
  * C0 (250 MB) pamięci podręcznej — maksymalnie 16 baz danych
  * C1 (1 GB) pamięci podręcznej — maksymalnie 16 baz danych
  * C2 (2,5 GB) pamięci podręcznej — maksymalnie 16 baz danych
  * C3 (6 GB) pamięci podręcznej — maksymalnie 16 baz danych
  * C4 (13 GB) pamięci podręcznej — maksymalnie 32 bazami danych
  * C5 (26 GB) pamięci podręcznej — maksymalnie 48 baz danych
  * C6 (53 GB) pamięci podręcznej — maksymalnie 64 baz danych
* Pamięci podręczne w warstwie Premium
  * P1 (6 GB do 60 GB) — maksymalnie 16 baz danych
  * P2 (13 GB - 130 GB) — maksymalnie 32 bazami danych
  * P3 (26 GB - 260 GB) — maksymalnie 48 baz danych
  * P4 (53 GB do 530 GB) — maksymalnie 64 baz danych
  * Wszystkich premium pamięci podręcznych z klastra pamięci podręcznej Redis enabled - klastra pamięci podręcznej Redis obsługuje wyłącznie korzystanie z bazy danych 0 więc `databases` ograniczenia dla dowolnego cache w warstwie premium przy użyciu klastra pamięci podręcznej Redis, włączone jest faktycznie 1 i [wybierz](https://redis.io/commands/select) polecenie nie jest dozwolone. Aby uzyskać więcej informacji, zobacz [należy wprowadzać żadnych zmian Moja aplikacja kliencka, aby użyć klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Aby uzyskać więcej informacji na temat baz danych, zobacz [co to są bazy danych Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases` Ustawienie może być skonfigurowane tylko podczas tworzenia pamięci podręcznej i tylko przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania. Aby uzyskać przykład konfigurowania `databases` podczas tworzenia pamięci podręcznej przy użyciu programu PowerShell, zobacz [New AzRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` różni się dla każdej usługi Azure Cache dla warstwy cenowej usługi Redis.

* Podstawowa i standardowa pamięci podręcznych
  * C0 (250 MB) pamięci podręcznej — maksymalnie 256 połączeń
  * C1 (1 GB) pamięci podręcznej — maksymalnie 1000 połączeń
  * C2 (2,5 GB) pamięci podręcznej — maksymalnie 2000 połączeń
  * C3 (6 GB) pamięci podręcznej — maksymalnie 5000 połączeń
  * C4 (13 GB) pamięci podręcznej — maksymalnie 10 000 połączeń
  * C5 (26 GB) pamięci podręcznej — maksymalnie 15 000 połączeń
  * C6 (53 GB) pamięci podręcznej — maksymalnie 20 000 połączeń
* Pamięci podręczne w warstwie Premium
  * P1 (6 GB do 60 GB) — maksymalnie 7500 połączeń
  * P2 (13 GB - 130 GB) — maksymalnie 15 000 połączeń
  * P3 (26 GB - 260 GB) — maksymalnie 30 000 połączeń
  * P4 (53 GB do 530 GB) — maksymalnie 40 000 połączeń

> [!NOTE]
> Podczas każdego rozmiaru pamięci podręcznej umożliwia *do* określoną liczbę połączeń, każde połączenie z magazynem Redis obciążenie jest skojarzony z nim. Przykładem takiego obciążenie byłoby użycie Procesora i pamięci, w wyniku szyfrowania TLS/SSL. Limit maksymalnej liczby połączeń dla rozmiaru pamięć podręczna zakłada mniej obciążonych pamięci podręcznej. Jeśli ładowanie z połączenia obciążenie *oraz* obciążenia z operacjami klienta przekracza wydajność systemu, pamięci podręcznej mogą wystąpić problemy pojemności, nawet jeśli nie przekroczono limit połączeń dla bieżącego rozmiaru pamięci podręcznej.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Nieobsługiwane w pamięci podręcznej Azure dla usługi Redis polecenia redis
> [!IMPORTANT]
> Ponieważ konfiguracji i zarządzania usługi Azure cache dla wystąpień usługi Redis jest zarządzany przez firmę Microsoft, poniższe polecenia są wyłączone. Jeśli zostanie podjęta próba ich wywołania, pojawi się komunikat o błędzie podobny do `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRACJA
> * ZAPISZ
> * ZAMYKANIE
> * SLAVEOF
> * KLASTER - klaster zapisu poleceń są wyłączone, ale tylko do odczytu klastra polecenia są dozwolone.
> 
> 

Aby uzyskać więcej informacji na temat polecenia Redis zobacz [ https://redis.io/commands ](https://redis.io/commands).

## <a name="redis-console"></a>Konsola pamięci podręcznej redis
Można bezpiecznie wysyłać polecenia do wystąpienia pamięci podręcznej Redis przy użyciu pamięci podręcznej Azure **konsolę pamięci podręcznej Redis**, który jest dostępny w witrynie Azure portal dla wszystkich warstw pamięci podręcznej.

> [!IMPORTANT]
> - Konsolę pamięci podręcznej Redis nie działa z [sieci Wirtualnej](cache-how-to-premium-vnet.md). Gdy pamięć podręczna jest częścią sieci Wirtualnej, tylko klienci w sieci Wirtualnej można uzyskać dostęp do pamięci podręcznej. Ponieważ konsolę pamięci podręcznej Redis działa w przeglądarce lokalnego znajduje się poza siecią Wirtualną, nie można połączyć pamięci podręcznej.
> - Nie wszystkie polecenia usługi Redis są obsługiwane w pamięci podręcznej Azure dla usługi Redis. Aby uzyskać listę poleceń pamięci podręcznej Redis, które są wyłączone dla usługi Azure Cache dla pamięci podręcznej Redis, zobacz poprzedni [polecenia nie są obsługiwane w usłudze Azure Cache, dla usługi Redis Redis](#redis-commands-not-supported-in-azure-cache-for-redis) sekcji. Aby uzyskać więcej informacji na temat polecenia Redis zobacz [ https://redis.io/commands ](https://redis.io/commands).
> 
> 

Aby otworzyć konsolę pamięci podręcznej Redis, kliknij przycisk **konsoli** z **pamięci podręcznej Redis Azure** bloku.

![Konsola pamięci podręcznej redis](./media/cache-configure/redis-console-menu.png)

Do wysyłania poleceń dla wystąpienia pamięci podręcznej, wpisz żądane polecenie do konsoli.

![Konsola pamięci podręcznej redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Pomocą konsolę pamięci podręcznej Redis cache w warstwie premium jest klastrowany

Gdy za pomocą konsoli usługi Redis z premium w klastrze pamięci podręcznej można wysyłać polecenia do jednego fragmentu pamięci podręcznej. Aby wydać polecenie do określonego fragmentu, najpierw połączyć się z żądanego fragmentu, klikając selektor fragmentów.

![Konsola pamięci podręcznej redis](./media/cache-configure/redis-console-premium-cluster.png)

Jeśli spróbujesz uzyskać dostępu do klucza, który jest przechowywany w innym fragmencie niż połączony fragment, pojawi się komunikat o błędzie podobny do następującego:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

W poprzednim przykładzie fragment 1 jest wybranego fragmentu, ale `myKey` znajduje się w jednym fragmencie 0, wskazane przez `(shard 0)` część komunikatu o błędzie. W tym przykładzie, aby uzyskać dostęp do `myKey`wybierz fragmentu 0 za pomocą selektora fragmentów, a następnie wydać polecenie żądaną.


## <a name="move-your-cache-to-a-new-subscription"></a>Przenoszenie pamięci podręcznej do nowej subskrypcji
Pamięć podręczną można przenieść do nowej subskrypcji, klikając **przenieść**.

![Przenieś pamięć podręczna systemu Azure dla usługi Redis](./media/cache-configure/redis-cache-move.png)

Aby uzyskać informacje dotyczące przenoszenia zasobów między grupami zasobów do innej, a także z jednej subskrypcji do innej, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat pracy z poleceniami pamięci podręcznej Redis, zobacz [jak uruchomić polecenia Redis?](cache-faq.md#how-can-i-run-redis-commands)

