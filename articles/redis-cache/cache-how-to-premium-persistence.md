---
title: Jak skonfigurować trwałości danych dla podręczna Redis Azure Premium
description: Dowiedz się, jak skonfigurować i zarządzać nią trwałości danych swoich wystąpień pamięci podręcznej Redis Azure warstwy Premium
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: wesmc
ms.openlocfilehash: 270158bbf85a58a48a367a091ad2b09a9d114b2b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910853"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Jak skonfigurować trwałości danych dla podręczna Redis Azure Premium
Pamięć podręczna Redis Azure ma ofert różnych pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiar pamięci podręcznej i funkcji, łącznie z funkcji warstwy Premium, takich jak klastrowanie, trwałości i obsługi sieci wirtualnej. W tym artykule opisano sposób konfigurowania trwałości w wystąpienia pamięci podręcznej Redis Azure premium.

Aby uzyskać informacji o innych funkcjach pamięci podręcznej premium, zobacz [wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Co to jest trwałości danych?
[Trwałość redis](https://redis.io/topics/persistence) służy do utrwalenia danych przechowywanych w pamięci podręcznej Redis. Można również wykonać migawki i kopii zapasowej danych, które można załadować w przypadku awarii sprzętu. Jest to bardzo dużą zaletą za pośrednictwem warstwy Basic lub Standard, gdy wszystkie dane są przechowywane w pamięci i może być utracie danych w przypadku awarii skutkującej węzłów pamięci podręcznej w dół. 

Pamięć podręczna Redis Azure oferuje trwałość Redis przy użyciu następujących modeli:

* **Trwałość RDB** -trwałości po RDB (bazy danych Redis) jest skonfigurowany, pamięć podręczna Redis Azure migawki pamięci podręcznej Redis w pamięci podręcznej Redis binarny format dysku oparty na można skonfigurować częstotliwość wykonywania kopii zapasowych będzie się powtarzał. Jeśli krytycznego zdarzenie występuje, który powoduje wyłączenie pamięci podręcznej podstawowy i repliki, pamięć podręczna jest odtworzone przy użyciu najnowszych migawki. Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#rdb-advantages) i [wady](https://redis.io/topics/persistence#rdb-disadvantages) o trwałości RDB.
* **Trwałość AOF** -trwałości po AOF (Dołącz tylko plików) jest skonfigurowany, pamięć podręczna Redis Azure zapisuje każdej operacji zapisu do dziennika, który jest zapisywany w co najmniej raz na sekundę na konto magazynu Azure. W przypadku krytycznego zdarzenia, który powoduje wyłączenie pamięci podręcznej podstawowy i repliki, pamięci podręcznej jest odtworzyć za pomocą operacji zapisu przechowywane. Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#aof-advantages) i [wady](https://redis.io/topics/persistence#aof-disadvantages) z AOF trwałości.

Trwałości jest skonfigurowany z **nowa pamięć podręczna Redis** bloku podczas tworzenia pamięci podręcznej i na **zasobów menu** istniejących Premium przechowuje w pamięci podręcznej.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po wybraniu warstwie cenowej premium kliknij **trwałość Redis**.

![Trwałość redis][redis-cache-persistence]

W następnej sekcji opisano sposób konfigurowania trwałość Redis na nowa pamięć podręczna premium. Po skonfigurowaniu trwałość Redis, kliknij przycisk **Utwórz** do utworzenia nowej pamięci podręcznej premium z trwałość Redis.

## <a name="enable-redis-persistence"></a>Włącz trwałość Redis

Redis trwałości jest włączona na **trwałość danych Redis** bloku, wybierając opcję **RDB** lub **AOF** trwałości. Dla nowych pamięci podręcznych ten blok jest dostępny w trakcie procesu tworzenia pamięci podręcznej, zgodnie z opisem w poprzedniej sekcji. Dla istniejącej pamięci podręcznych **trwałość danych Redis** bloku jest dostępny z **zasobów menu** dla pamięci podręcznej.

![Ustawienia redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurowanie trwałości RDB

Aby włączyć RDB trwałości, kliknij **RDB**. Aby wyłączyć trwałość RDB na pamięć podręczna premium wcześniej włączony, kliknij przycisk **wyłączone**.

![RDB trwałość redis][redis-cache-rdb-persistence]

Aby skonfigurować interwał wykonywania kopii zapasowej, wybierz **częstotliwość wykonywania kopii zapasowych** z listy rozwijanej. Można także wybrać opcję **15 minut**, **30 minut**, **60 minut**, **6 godzin**, **12 godzin**i **24 godziny**. Ten interwał odlicza w dół po pomyślnym ukończeniu poprzedniej operacji tworzenia kopii zapasowej i po jego upływie nową kopię zapasową jest inicjowana.

Kliknij przycisk **konta magazynu** wybierz konto magazynu, a następnie wybrać opcję **klucza podstawowego** lub **klucza pomocniczego** z **klucza magazynu** listy rozwijanej. Musisz wybrać konto magazynu w tym samym regionie co pamięci podręcznej, a **magazyn w warstwie Premium** konta jest zalecane, ponieważ Magazyn w warstwie premium ma wyższej przepustowości. 

> [!IMPORTANT]
> Podczas ponownego generowania klucza magazynu dla Twojego konta trwałości, należy ponownie skonfigurować inny klawisz z **klucza magazynu** listy rozwijanej.
> 
> 

Kliknij przycisk **OK** Aby zapisać konfigurację trwałości.

Następnej kopii zapasowej (lub pierwszej kopii zapasowej dla nowych pamięci podręcznych) jest inicjowana po upływie interwału częstotliwość wykonywania kopii zapasowych.

## <a name="configure-aof-persistence"></a>Konfigurowanie trwałości AOF

Aby włączyć AOF trwałości, kliknij **AOF**. Aby wyłączyć trwałość AOF na pamięć podręczna premium wcześniej włączony, kliknij przycisk **wyłączone**.

![AOF trwałość redis][redis-cache-aof-persistence]

Aby skonfigurować AOF trwałości, określ **pierwsze konto magazynu**. To konto magazynu musi być w tym samym regionie co pamięci podręcznej, a **magazyn w warstwie Premium** konta jest zalecane, ponieważ Magazyn w warstwie premium ma wyższej przepustowości. Opcjonalnie można skonfigurować konto dodatkowego magazynu o nazwie **drugiego konta magazynu**. Jeśli skonfigurowano drugiego konta magazynu, zapisuje w pamięci podręcznej repliki są zapisywane tego drugiego konta magazynu. Dla każdego konta skonfigurowanego magazynu, wybierz **klucza podstawowego** lub **klucza pomocniczego** z **klucza magazynu** listy rozwijanej. 

> [!IMPORTANT]
> Podczas ponownego generowania klucza magazynu dla Twojego konta trwałości, należy ponownie skonfigurować inny klawisz z **klucza magazynu** listy rozwijanej.
> 
> 

Po włączeniu trwałości AOF zapisu operacji do pamięci podręcznej są zapisywane do konta magazynu wyznaczonego (lub konta, jeśli skonfigurowano drugiego konta magazynu). W przypadku poważnej awarii przyjmuje dół podstawowy i repliki pamięci podręcznej przechowywane dziennika AOF służy do odbudowania pamięci podręcznej.

## <a name="persistence-faq"></a>Trwałość — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące pamięci podręcznej Redis Azure trwałości.

* [Można włączyć trwałości na utworzonej wcześniej pamięci podręcznej?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Można włączyć AOF i RDB trwałości, w tym samym czasie?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Model trwałości, który należy wybrać?](#which-persistence-model-should-i-choose)
* [Co się stanie, jeśli mają skalowana inny rozmiar i przywróceniu kopii zapasowej został utworzony przed wykonaniem operacji skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trwałość RDB
* [Częstotliwość wykonywania kopii zapasowych RDB można zmienić po utworzeniu pamięci podręcznej?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Dlaczego Jeśli częstotliwość wykonywania kopii zapasowych RDB 60 minut istnieje ponad 60 minut między kopii zapasowych?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co się dzieje starych kopii zapasowych RDB podczas tworzenia nowej kopii zapasowej?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Trwałość AOF
* [Kiedy należy używać drugiego konta magazynu?](#when-should-i-use-a-second-storage-account)
* [Czy AOF trwałości mają wpływ na całym, opóźnienia lub wydajności Moje pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak usunąć drugiego konta magazynu?](#how-can-i-remove-the-second-storage-account)
* [Co to jest ponownego zapisywania i jak wpływa na mój pamięci podręcznej?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Co należy oczekiwać podczas skalowania pamięci podręcznej z AOF włączone?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Sposób organizowania danych AOF w magazynie?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Można włączyć trwałości na utworzonej wcześniej pamięci podręcznej?
Tak, trwałość Redis można skonfigurować zarówno na tworzenie pamięci podręcznej, jak i na istniejące premium pamięci podręcznych.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Można włączyć AOF i RDB trwałości, w tym samym czasie?

Nie można włączyć tylko RDB lub AOF, ale nie oba jednocześnie.

### <a name="which-persistence-model-should-i-choose"></a>Model trwałości, który należy wybrać?

Trwałość AOF zapisuje każdego zapisu do dziennika, która ma pewien wpływ na przepustowość, w porównaniu z RDB trwałości, który zapisuje kopie zapasowe oparte na skonfigurowany interwał wykonywania kopii zapasowej z minimalnym wpływem na wydajność. Wybierz AOF trwałości, jeśli jest poznasz podstawowy, aby zminimalizować ryzyko utraty danych, a może obsłużyć spadek przepustowości dla pamięci podręcznej. Wybierz opcję trwałości RDB, jeśli chcesz zachować optymalną wydajność w pamięci podręcznej, ale nadal chcesz mechanizm odzyskiwania danych.

* Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#rdb-advantages) i [wady](https://redis.io/topics/persistence#rdb-disadvantages) o trwałości RDB.
* Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#aof-advantages) i [wady](https://redis.io/topics/persistence#aof-disadvantages) z AOF trwałości.

Aby uzyskać więcej informacji o wydajności, korzystając z AOF trwałości, zobacz [AOF jest wpływ trwałości w całym, opóźnienia lub wydajności Moje pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co się stanie, jeśli mają skalowana inny rozmiar i przywróceniu kopii zapasowej został utworzony przed wykonaniem operacji skalowania?

Zarówno RDB, jak i AOF trwałości:

* Możesz mieć skalowania do większy rozmiar, nie ma żadnego wpływu.
* Jeśli skalowana w mniejszy rozmiar, a masz niestandardowego [baz danych](cache-configure.md#databases) ustawienie jest większa niż [limit bazy danych](cache-configure.md#databases) dla Twojego nowego rozmiaru danych w tych baz danych nie jest przywrócony. Aby uzyskać więcej informacji, zobacz [jest niestandardowe ustawienie dotyczy podczas skalowania baz danych?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Jeśli skalowana w mniejszy rozmiar, a nie ma wystarczającej ilości miejsca do przechowywania wszystkich danych z ostatniej kopii zapasowej mają mniejszy rozmiar, kluczy zostanie usunięty podczas procesu przywracania, zwykle za pomocą [allkeys lru](http://redis.io/topics/lru-cache) zasady wykluczania.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Częstotliwość wykonywania kopii zapasowych RDB można zmienić po utworzeniu pamięci podręcznej?
Tak, można zmienić częstotliwość wykonywania kopii zapasowych trwałości RDB **trwałość danych Redis** bloku. Aby uzyskać instrukcje, zobacz [trwałość Redis skonfigurować](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Dlaczego Jeśli częstotliwość wykonywania kopii zapasowych RDB 60 minut istnieje ponad 60 minut między kopii zapasowych?
Nie można uruchomić interwał częstotliwości tworzenia kopii zapasowej trwałości RDB, aż do poprzedniego procesu tworzenia kopii zapasowej zostało ukończone pomyślnie. Częstotliwość wykonywania kopii zapasowych wynosi 60 min, trwa proces tworzenia kopii zapasowej 15 minut do pomyślnego ukończenia następnej kopii zapasowej nie uruchamia się do 75 minut po uruchomieniu z poprzedniej kopii zapasowej.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co się dzieje starych kopii zapasowych RDB podczas tworzenia nowej kopii zapasowej?
Wszystkie kopie zapasowe trwałości RDB z wyjątkiem najnowszego są automatycznie usuwane. To usunięcie może się nie powtórzyła natychmiast, ale starsze kopie zapasowe nie są zachowywane przez czas nieograniczony.


### <a name="when-should-i-use-a-second-storage-account"></a>Kiedy należy używać drugiego konta magazynu?

Jeśli uważasz, że masz wyższe niż operacje oczekiwanego zestawu w pamięci podręcznej, należy używać drugiego konta magazynu AOF trwałości.  Konfigurowanie konta magazynu pomocniczego pomaga upewnić się, że pamięć podręczna nie osiągnąć limity przepustowości magazynu.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Czy AOF trwałości mają wpływ na całym, opóźnienia lub wydajności Moje pamięci podręcznej?

Trwałości AOF ma wpływ na przepustowość przez około 15 – 20% przy poniżej maksymalne obciążenie pamięci podręcznej (Procesora i serwera załadować zarówno w obszarze 90%). Nie należy opóźnień gdy pamięci podręcznej znajduje się w tych granicach. Jednak pamięć podręczna osiągną te limity wcześniej z AOF włączone.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak usunąć drugiego konta magazynu?

Można usunąć konto magazynu pomocniczego trwałości AOF, ustawiając drugiego konta magazynu, aby być taka sama jak pierwsze konto magazynu. Aby uzyskać instrukcje, zobacz [AOF Konfigurowanie trwałości](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co to jest ponownego zapisywania i jak wpływa na mój pamięci podręcznej?

Gdy plik AOF staje się wystarczająco duży, napisz ponownie automatycznie jest przechowywane w kolejce w pamięci podręcznej. Poprawione zmienia rozmiar pliku AOF z minimalny zestaw operacji potrzebne do utworzenia bieżącego zestawu danych. Podczas modyfikacji oprogramowania oczekuje się wcześniej osiągnięcie ograniczeń wydajności, szczególnie w przypadku pracy z dużymi zestawami danych. Ponownego występować mniej często pliku AOF staje się większy, ale będzie zająć dużo czasu, gdy nastąpi.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Co należy oczekiwać podczas skalowania pamięci podręcznej z AOF włączone?

Jeśli plik AOF w czasie skalowania jest znacznie duży, następnie oczekiwać na trwać dłużej, niż oczekiwano, ponieważ jej będzie można ponownego załadowania pliku po zakończeniu skalowanie operacji skalowania.

Aby uzyskać więcej informacji na temat skalowania, zobacz [co się stanie, jeśli mają skalowana inny rozmiar i przywróceniu kopii zapasowej został utworzony przed wykonaniem operacji skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Sposób organizowania danych AOF w magazynie?

Dane przechowywane w plikach AOF jest podzielony na wiele stronicowe obiekty BLOB w każdym węźle, aby zwiększyć wydajność zapisywania danych do magazynu. W poniższej tabeli przedstawiono, jak wiele stronicowe obiekty BLOB są używane dla każdej warstwy cenowej:

| Warstwa Premium | Obiekty blob |
|--------------|-------|
| P1           | 4 na niezależnego fragmentu    |
| P2           | 8 za niezależnego fragmentu    |
| P3           | 16 na niezależnego fragmentu   |
| P4           | 20 za niezależnego fragmentu   |

Gdy klaster jest włączona, każdy identyfikator niezależnego fragmentu w pamięci podręcznej ma własny zestaw stronicowe obiekty BLOB, jak wskazano w powyższej tabeli. Na przykład P2 pamięci podręcznej z trzech odłamków rozdziela jego pliku AOF między 24 stronicowych obiektów blob (8 obiekty BLOB na niezależnego fragmentu z 3 odłamków).

Po ponownego zapisywania dwa zestawy plików AOF istnieje w magazynie. Ponownego wystąpienia w tle i Dołącz do pierwszego zestawu plików, podczas gdy operacje na zestawie, które są wysyłane do pamięci podręcznej podczas poprawione Dołącz do drugiego zestawu. Kopii zapasowej są tymczasowo przechowywane podczas ponownego w razie awarii, ale jest usuwany natychmiast, po zakończeniu ponownego zapisywania.


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
