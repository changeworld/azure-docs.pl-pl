---
title: Konfigurowanie trwałości danych — pamięć podręczna premium Azure dla programu Redis
description: Dowiedz się, jak skonfigurować i zarządzać trwałością danych w pamięci podręcznej platformy Azure w warstwie Premium dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 84a5b4784a36fb22ae50a7a1ec4fcb7e5ef5b7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245280"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Jak skonfigurować trwałość danych dla pamięci podręcznej platformy Azure w warstwie Premium dla redis
Usługa Azure Cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru pamięci podręcznej i funkcji, w tym funkcji warstwy Premium, takich jak klastrowanie, trwałość i obsługa sieci wirtualnej. W tym artykule opisano sposób konfigurowania trwałości w pamięci podręcznej platformy Azure w warstwie Premium dla wystąpienia Redis.

Aby uzyskać informacje na temat innych funkcji pamięci podręcznej w warstwie Premium, zobacz [Wprowadzenie do warstwy Azure Cache for Redis Premium.](cache-premium-tier-intro.md)

## <a name="what-is-data-persistence"></a>Co to jest trwałość danych?
[Trwałość Redis](https://redis.io/topics/persistence) umożliwia utrwalanie danych przechowywanych w redis. Można również robić migawki i tworzenie kopii zapasowych danych, które można załadować w przypadku awarii sprzętu. Jest to ogromna przewaga nad warstwą Podstawowa lub Standardowa, w której wszystkie dane są przechowywane w pamięci i może dopaść się potencjalna utrata danych w przypadku awarii, gdy węzły pamięci podręcznej są w dół. 

Usługa Azure Cache for Redis oferuje trwałość funkcji Redis przy użyciu następujących modeli:

* **Trwałość RDB** — po skonfigurowaniu trwałości bazy danych RDB (redis) funkcja Azure Cache for Redis utrzymuje migawkę pamięci podręcznej Azure Cache for Redis w formacie binarnym Redis na dysku na podstawie konfigurowalnej częstotliwości tworzenia kopii zapasowej. Jeśli wystąpi katastrofalne zdarzenie, które wyłącza zarówno podstawową, jak i replikę pamięci podręcznej, pamięć podręczna jest rekonstruowana przy użyciu najnowszej migawki. Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#rdb-advantages) i [wadach](https://redis.io/topics/persistence#rdb-disadvantages) trwałości RDB.
* **Trwałość AOF** — po skonfigurowaniu trwałości AOF (Append only file) usługa Azure Cache for Redis zapisuje każdą operację zapisu w dzienniku, który jest zapisywany co najmniej raz na sekundę na koncie usługi Azure Storage. Jeśli wystąpi katastrofalne zdarzenie, które wyłącza zarówno podstawową, jak i replikę pamięci podręcznej, pamięć podręczna jest rekonstruowana przy użyciu przechowywanych operacji zapisu. Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#aof-advantages) i [wadach](https://redis.io/topics/persistence#aof-disadvantages) trwałości AOF.

Trwałość zapisuje dane Redis na koncie usługi Azure Storage, które jesteś właścicielem i którymi zarządzasz. Można skonfigurować z **nowej pamięci podręcznej azure dla redis** bloku podczas tworzenia pamięci podręcznej i w **menu zasobów** dla istniejących pamięci podręcznych premium.

> [!NOTE]
> 
> Usługa Azure Storage automatycznie szyfruje dane po ich utrwaleniu. Do szyfrowania można używać własnych kluczy. Aby uzyskać więcej informacji, zobacz [Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault](/azure/storage/common/storage-service-encryption).
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po wybraniu warstwy cenowej premium kliknij przycisk **Redis .**

![Wytrwałość Redis][redis-cache-persistence]

Kroki opisane w następnej sekcji opisano, jak skonfigurować trwałość Redis na nowej pamięci podręcznej premium. Po skonfigurowaniu trwałości Redis kliknij przycisk **Utwórz,** aby utworzyć nową pamięć podręczną premium z trwałością Redis.

## <a name="enable-redis-persistence"></a>Włącz trwałość Redis

Trwałość redis jest włączona w bloku **Trwałość danych,** wybierając **rdb** lub **aof** trwałości. W przypadku nowych pamięci podręcznych ten blok jest dostępny podczas procesu tworzenia pamięci podręcznej, zgodnie z opisem w poprzedniej sekcji. W przypadku istniejących pamięci podręcznych blok **Trwałość danych** jest dostępny z **menu Zasób** dla pamięci podręcznej.

![Ustawienia Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurowanie trwałości RDB

Aby włączyć trwałość RDB, kliknij przycisk **RDB**. Aby wyłączyć trwałość RDB w poprzednio włączonej pamięci podręcznej premium, kliknij przycisk **Wyłączone**.

![Trwałość Redis RDB][redis-cache-rdb-persistence]

Aby skonfigurować interwał tworzenia kopii zapasowej, wybierz **częstotliwość tworzenia kopii zapasowej** z listy rozwijanej. Do wyboru są **15 minut,** **30 minut,** **60 minut,** **6 godzin,** **12 godzin**i **24 godziny.** Ten interwał rozpoczyna odliczanie po pomyślnym zakończeniu poprzedniej operacji tworzenia kopii zapasowej i po jej zakończeniu zostanie zainicjowana nowa kopia zapasowa.

Kliknij **pozycję Konto magazynu,** aby wybrać konto magazynu, którego chcesz użyć, i wybierz **klucz podstawowy** lub klucz **pomocniczy** do użycia z listy rozwijanej **Klucz magazynu.** Musisz wybrać konto magazynu w tym samym regionie co pamięć podręczna, a konto **magazynu w warstwie Premium** jest zalecane, ponieważ magazyn w warstwie Premium ma wyższą przepływność. 

> [!IMPORTANT]
> Jeśli klucz magazynu dla konta trwałości jest generowany ponownie, należy ponownie skonfigurować żądany klucz z listy rozwijanej **Klucz magazynu.**
> 
> 

Kliknij **przycisk OK,** aby zapisać konfigurację trwałości.

Następna kopia zapasowa (lub pierwsza kopia zapasowa dla nowych pamięci podręcznych) jest inicjowana po upływie interwału częstotliwości tworzenia kopii zapasowej.

## <a name="configure-aof-persistence"></a>Konfigurowanie trwałości AOF

Aby włączyć trwałość AOF, kliknij przycisk **AOF**. Aby wyłączyć trwałość AOF w poprzednio włączonej pamięci podręcznej premium, kliknij przycisk **Wyłączone**.

![Trwałość Redis AOF][redis-cache-aof-persistence]

Aby skonfigurować trwałość AOF, należy określić **pierwsze konto magazynu**. To konto magazynu musi znajdować się w tym samym regionie co pamięć podręczna, a konto **magazynu w warstwie Premium** jest zalecane, ponieważ magazyn w warstwie Premium ma wyższą przepływność. Opcjonalnie można skonfigurować dodatkowe konto magazynu o nazwie **Drugie konto magazynu**. Jeśli skonfigurowano drugie konto magazynu, zapisy w pamięci podręcznej repliki są zapisywane na tym drugim koncie magazynu. Dla każdego skonfigurowane konto magazynu wybierz **klucz podstawowy** lub **klucz pomocniczy** do użycia z listy rozwijanej **Klucz magazynu.** 

> [!IMPORTANT]
> Jeśli klucz magazynu dla konta trwałości jest generowany ponownie, należy ponownie skonfigurować żądany klucz z listy rozwijanej **Klucz magazynu.**
> 
> 

Po włączeniu trwałości AOF operacje zapisu w pamięci podręcznej są zapisywane na wyznaczonym koncie magazynu (lub kontach, jeśli skonfigurowano drugie konto magazynu). W przypadku katastrofalnego błędu, który usuwa zarówno podstawowej i repliki pamięci podręcznej, przechowywany dziennik AOF jest używany do odbudowy pamięci podręcznej.

## <a name="persistence-faq"></a>Często zadawane pytania dotyczące trwałości
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Cache dla trwałości Redis.

* [Czy mogę włączyć trwałość w poprzednio utworzonej pamięci podręcznej?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Czy można włączyć trwałość AOF i RDB w tym samym czasie?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Jaki model trwałości wybrać?](#which-persistence-model-should-i-choose)
* [Co się stanie, jeśli przeskaluję się do innego rozmiaru i zostanie przywrócona kopia zapasowa, która została wykonana przed operacją skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trwałość RDB
* [Czy mogę zmienić częstotliwość tworzenia kopii zapasowej RDB po utworzeniu pamięci podręcznej?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Dlaczego, jeśli mam częstotliwość tworzenia kopii zapasowych RDB 60 minut jest więcej niż 60 minut między kopiami zapasowymi?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co się stanie ze starymi kopiami zapasowymi RDB po wykonaniu nowej kopii zapasowej?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Trwałość AOF
* [Kiedy należy używać drugiego konta magazynu?](#when-should-i-use-a-second-storage-account)
* [Czy trwałość AOF wpływa na całej, opóźnienia lub wydajności mojej pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak mogę usunąć drugie konto magazynu?](#how-can-i-remove-the-second-storage-account)
* [Co to jest przepisanie i jak wpływa na moją pamięć podręczną?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Czego należy się spodziewać podczas skalowania pamięci podręcznej z włączoną funkcją AOF?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [W jaki sposób moje dane AOF są zorganizowane w pamięci masowej?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Czy mogę włączyć trwałość w poprzednio utworzonej pamięci podręcznej?
Tak, trwałość Redis można skonfigurować zarówno podczas tworzenia pamięci podręcznej, jak i w istniejących pamięciach podręcznych premium.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Czy można włączyć trwałość AOF i RDB w tym samym czasie?

Nie, można włączyć tylko RDB lub AOF, ale nie oba w tym samym czasie.

### <a name="which-persistence-model-should-i-choose"></a>Jaki model trwałości wybrać?

Trwałość AOF zapisuje każdy zapis w dzienniku, co ma pewien wpływ na przepływność, w porównaniu z trwałością RDB, która zapisuje kopie zapasowe na podstawie skonfigurowanego interwału tworzenia kopii zapasowych, przy minimalnym wpływie na wydajność. Wybierz trwałość AOF, jeśli głównym celem jest zminimalizowanie utraty danych i można obsłużyć spadek przepływności pamięci podręcznej. Wybierz trwałość RDB, jeśli chcesz zachować optymalną przepływność w pamięci podręcznej, ale nadal chcesz mechanizm odzyskiwania danych.

* Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#rdb-advantages) i [wadach](https://redis.io/topics/persistence#rdb-disadvantages) trwałości RDB.
* Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#aof-advantages) i [wadach](https://redis.io/topics/persistence#aof-disadvantages) trwałości AOF.

Aby uzyskać więcej informacji na temat wydajności podczas korzystania z trwałości AOF, zobacz [AOF trwałość wpływa na całej, opóźnienia lub wydajności mojej pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co się stanie, jeśli przeskaluję się do innego rozmiaru i zostanie przywrócona kopia zapasowa, która została wykonana przed operacją skalowania?

Dla trwałości RDB i AOF:

* Jeśli skalowane do większego rozmiaru, nie ma wpływu.
* Jeśli zostały przeskalowane do mniejszego rozmiaru i masz niestandardowe ustawienia [baz danych,](cache-configure.md#databases) który jest większy niż [limit baz danych](cache-configure.md#databases) dla nowego rozmiaru, dane w tych bazach danych nie zostanie przywrócony. Aby uzyskać więcej informacji, zobacz [Czy podczas skalowania ma wpływ na ustawienie niestandardowych baz danych?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Jeśli skalowano do mniejszego rozmiaru i nie ma wystarczająco dużo miejsca w mniejszym rozmiarze, aby pomieścić wszystkie dane z ostatniej kopii zapasowej, klucze zostaną eksmitowane podczas procesu przywracania, zazwyczaj przy użyciu zasad eksmisji [allkeys-lru.](https://redis.io/topics/lru-cache)

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Czy mogę zmienić częstotliwość tworzenia kopii zapasowej RDB po utworzeniu pamięci podręcznej?
Tak, można zmienić częstotliwość tworzenia kopii zapasowych dla trwałości RDB w bloku **trwałość danych.** Aby uzyskać instrukcje, zobacz Konfigurowanie trwałości redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Dlaczego, jeśli mam częstotliwość tworzenia kopii zapasowych RDB 60 minut jest więcej niż 60 minut między kopiami zapasowymi?
Interwał częstotliwości trwałości trwałości RDB nie uruchamia się, dopóki poprzedni proces tworzenia kopii zapasowej nie zostanie pomyślnie zakończony. Jeśli częstotliwość tworzenia kopii zapasowej wynosi 60 minut i trwa proces tworzenia kopii zapasowej 15 minut, aby pomyślnie zakończyć, następna kopia zapasowa nie rozpocznie się dopiero 75 minut po godzinie rozpoczęcia poprzedniej kopii zapasowej.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co się stanie ze starymi kopiami zapasowymi RDB po wykonaniu nowej kopii zapasowej?
Wszystkie kopie zapasowe trwałości RDB z wyjątkiem najnowszej są automatycznie usuwane. To usunięcie może nie nastąpić natychmiast, ale starsze kopie zapasowe nie są utrwalone przez czas nieokreślony.


### <a name="when-should-i-use-a-second-storage-account"></a>Kiedy należy używać drugiego konta magazynu?

Należy użyć drugiego konta magazynu dla trwałości AOF, jeśli uważasz, że masz wyższe niż oczekiwano operacji zestawu w pamięci podręcznej.  Skonfigurowanie dodatkowego konta magazynu pomaga upewnić się, że pamięć podręczna nie osiągnie limitów przepustowości magazynu.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Czy trwałość AOF wpływa na całej, opóźnienia lub wydajności mojej pamięci podręcznej?

Trwałość AOF wpływa na przepustowość o około 15% – 20%, gdy pamięć podręczna jest poniżej maksymalnego obciążenia (obciążenie procesora i serwera zarówno poniżej 90%). Nie powinno być problemy z opóźnieniem, gdy pamięć podręczna znajduje się w tych granicach. Jednak pamięć podręczna osiągnie te limity wcześniej z włączoną funkcją AOF.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak mogę usunąć drugie konto magazynu?

Można usunąć aof trwałość dodatkowe konto magazynu, ustawiając drugie konto magazynu jest taka sama jak pierwsze konto magazynu. Aby uzyskać instrukcje, zobacz [Konfigurowanie trwałości AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co to jest przepisanie i jak wpływa na moją pamięć podręczną?

Gdy plik AOF staje się wystarczająco duży, przepisanie jest automatycznie umieszczane w kolejce w pamięci podręcznej. Przepisanie zmienia rozmiar pliku AOF z minimalnym zestawem operacji potrzebnych do utworzenia bieżącego zestawu danych. Podczas przepisywania należy spodziewać się wcześniejszego osiągnięcia limitów wydajności, zwłaszcza w przypadku dużych zestawów danych. Przepisywanie występuje rzadziej, gdy plik AOF staje się większy, ale zajmie dużo czasu, gdy to się stanie.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Czego należy się spodziewać podczas skalowania pamięci podręcznej z włączoną funkcją AOF?

Jeśli plik AOF w momencie skalowania jest znacznie duży, należy oczekiwać, że operacja skalowania potrwa dłużej niż oczekiwano, ponieważ będzie ponownie załadować plik po zakończeniu skalowania.

Aby uzyskać więcej informacji na temat skalowania, zobacz [Co się stanie, jeśli przeskalowałem do innego rozmiaru i kopia zapasowa zostanie przywrócona, która została wykonana przed operacją skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>W jaki sposób moje dane AOF są zorganizowane w pamięci masowej?

Dane przechowywane w plikach AOF jest podzielony na wiele stronicowych obiektów blob na węzeł, aby zwiększyć wydajność zapisywania danych do magazynu. W poniższej tabeli przedstawiono liczbę obiektów blob stron używanych dla każdej warstwy cenowej:

| Warstwa Premium | Obiekty blob |
|--------------|-------|
| P1           | 4 na odłamek    |
| P2           | 8 na odłamek    |
| P3           | 16 na odłamek   |
| P4           | 20 na odłamek   |

Gdy klastrowanie jest włączone, każdy fragment w pamięci podręcznej ma swój własny zestaw stronicowych obiektów blob, jak wskazano w poprzedniej tabeli. Na przykład pamięć podręczna P2 z trzema fragmentami dystrybuuje swój plik AOF w 24 stronicowych obiektach blob (8 obiektów blob na obiekt niezależnego fragmentu, z 3 fragmentami).

Po przepisaniu w magazynie istnieją dwa zestawy plików AOF. Przepisywania występują w tle i dołączyć do pierwszego zestawu plików, podczas gdy operacje zestawu, które są wysyłane do pamięci podręcznej podczas ponownego zapisywania dołączyć do drugiego zestawu. Kopia zapasowa jest tymczasowo przechowywana podczas przepisywania w przypadku awarii, ale jest natychmiast usuwana po zakończeniu ponownego zapisu.


## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak korzystać z większej liczby funkcji pamięci podręcznej premium.

* [Wprowadzenie do warstwy Azure Cache for Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
