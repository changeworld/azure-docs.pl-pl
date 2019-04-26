---
title: Konfigurowanie trwałości danych dla usługi Azure Cache w warstwie Premium dla usługi Redis
description: Dowiedz się, jak skonfigurować i zarządzać funkcji trwałości danych usługi w warstwie Premium usługi Azure Cache dla wystąpień usługi Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: de0b2e3ef7b0268540ef4896ade132a297ee88ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543505"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Konfigurowanie trwałości danych dla usługi Azure Cache w warstwie Premium dla usługi Redis
Pamięć podręczna systemu Azure dla usługi Redis zawiera ofert różnych pamięci podręcznych, które zapewniają elastyczność przy wyborze rozmiar pamięci podręcznej i funkcji, takich jak funkcje warstwy Premium, takich jak klastrowanie, trwałość i obsługę sieci wirtualnej. W tym artykule opisano sposób konfigurowania trwałości w warstwie premium usługi Azure Cache dla wystąpienia usługi Redis.

Aby uzyskać informacje o innych funkcjach pamięci podręcznej — wersja premium, zobacz [wprowadzenie do usługi Azure Cache w warstwie Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Co to jest stan trwały danych?
[Trwałość redis](https://redis.io/topics/persistence) umożliwia utrwalanie danych przechowywanych w pamięci podręcznej Redis. Można też tworzyć migawki i utworzyć kopię zapasową danych, które można załadować w razie wystąpienia awarii sprzętu. Jest to dużą przewagę nad warstwy podstawowa lub standardowa, gdzie wszystkie dane są przechowywane w pamięci i mogą być możliwej utracie danych w razie awarii, w których węzłach pamięci podręcznej w dół. 

Pamięć podręczna systemu Azure dla usługi Redis zapewnia trwałość pamięci podręcznej Redis przy użyciu następujących modeli:

* **Trwałość RDB** -trwałości po RDB (baza danych usługi Redis) jest skonfigurowany, pamięci podręcznej Redis Azure będzie nadal występować po migawkę pamięci podręcznej Azure redis Cache w pamięci podręcznej Redis, format binarny na dysku, oparte na można skonfigurować częstotliwość wykonywania kopii zapasowych. Sytuacji katastrofy wyłączającą podstawowej i replice pamięci podręcznej pamięć podręczna jest odtworzone przy użyciu najnowszej migawki. Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#rdb-advantages) i [wady](https://redis.io/topics/persistence#rdb-disadvantages) trwałość RDB.
* **Trwałość AOF** -trwałości po AOF (Dołącz plik tylko) jest skonfigurowany, pamięci podręcznej Redis Azure zapisuje każdej operacji zapisu w dzienniku, który jest zapisywany w co najmniej raz na sekundę do konta usługi Azure Storage. Sytuacji katastrofy wyłączającą podstawowej i replice pamięci podręcznej pamięć podręczna jest odtworzone przy użyciu operacji zapisu przechowywanych. Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#aof-advantages) i [wady](https://redis.io/topics/persistence#aof-disadvantages) AOF trwałość.

Trwałości jest skonfigurowany z **nowej usługi Azure Cache dla usługi Redis** bloku, podczas tworzenia pamięci podręcznej i na **menu zasobów** dla istniejące premie zapisuje w pamięci podręcznej.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po wybraniu warstwy cenowej premium kliknij **trwałość Redis**.

![Trwałość redis][redis-cache-persistence]

W następnej sekcji opisano sposób konfigurowania stanu trwałego pamięci podręcznej Redis na nowe cache w warstwie premium. Po skonfigurowaniu funkcji trwałości Redis kliknij **Utwórz** do utworzenia nowego cache w warstwie premium z trwałość pamięci podręcznej Redis.

## <a name="enable-redis-persistence"></a>Włącz trwałość pamięci podręcznej Redis

Redis trwałości jest włączona na **trwałość danych Redis** bloku, wybierając opcję **RDB** lub **AOF** trwałości. Dla nowych pamięci podręcznych ten blok jest dostępny w trakcie procesu tworzenia pamięci podręcznej, zgodnie z opisem w poprzedniej sekcji. Dla dostępnych pamięci podręcznych **trwałość danych Redis** bloku jest dostępny z **menu zasobów** dla pamięci podręcznej.

![Redis ustawienia][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurowanie trwałości RDB

Aby włączyć opcję trwałości RDB, kliknij przycisk **RDB**. Aby wyłączyć trwałości RDB na włączonym uprzednio premium pamięci podręcznej, kliknij pozycję **wyłączone**.

![RDB trwałość redis][redis-cache-rdb-persistence]

Aby skonfigurować interwał wykonywania kopii zapasowej, zaznacz **częstotliwość wykonywania kopii zapasowych** z listy rozwijanej. Można także wybrać opcję **15 minut**, **30 minut**, **60 minut**, **6 godzin**, **12 godzin**i **24 godziny**. Ten interwał odlicza w dół po pomyślnym zakończeniu poprzedniej operacji tworzenia kopii zapasowej i po jego upływie inicjowane jest nową kopię zapasową.

Kliknij przycisk **konta magazynu** o wybranie konta magazynu do użycia, a następnie wybierz opcję **klucza podstawowego** lub **klucz pomocniczy** do użycia z **klucza magazynu** listy rozwijanej. W tym samym regionie co pamięć podręczna, musisz wybrać konto magazynu i **usługi Premium Storage** konta jest zalecane, ponieważ usługa premium storage ma większą przepływność. 

> [!IMPORTANT]
> Zostanie ponownie wygenerowany klucz magazynu dla konta usługi trwałości, należy ponownie skonfigurować żądany klucz z **klucza magazynu** listy rozwijanej.
> 
> 

Kliknij przycisk **OK** można zapisać konfiguracji trwałości.

Następną kopią zapasową (lub pierwsza kopia zapasowa w przypadku nowych pamięci podręcznych) jest inicjowane, gdy upłynie interwał częstotliwości tworzenia kopii zapasowej.

## <a name="configure-aof-persistence"></a>Konfigurowanie trwałości AOF

Aby włączyć opcję trwałości AOF, kliknij przycisk **AOF**. Aby wyłączyć trwałości AOF na włączonym uprzednio premium pamięci podręcznej, kliknij pozycję **wyłączone**.

![AOF trwałość redis][redis-cache-aof-persistence]

Konfigurowanie trwałości AOF, określ **pierwszego konta magazynu**. To konto magazynu musi należeć do tego samego regionu pamięci podręcznej, a **usługi Premium Storage** konta jest zalecane, ponieważ usługa premium storage ma większą przepływność. Opcjonalnie można skonfigurować dodatkowe konto magazynu o nazwie **drugiego konta magazynu**. Jeśli drugie konto magazynu jest skonfigurowany, zapisuje w pamięci podręcznej repliki są zapisywane do tego drugiego konta magazynu. Dla każdego skonfigurowanego konta magazynu, wybierz **klucza podstawowego** lub **klucz pomocniczy** do użycia z **klucza magazynu** listy rozwijanej. 

> [!IMPORTANT]
> Zostanie ponownie wygenerowany klucz magazynu dla konta usługi trwałości, należy ponownie skonfigurować żądany klucz z **klucza magazynu** listy rozwijanej.
> 
> 

Włączenie trwałości AOF zapisu operacji do pamięci podręcznej są zapisywane do konta magazynu wyznaczonego (lub konta, po skonfigurowaniu drugiego konta magazynu). W przypadku poważnej awarii trwa dół podstawowej i replice pamięci podręcznej przechowywane dziennika AOF służy do Odbuduj pamięć podręczną.

## <a name="persistence-faq"></a>Trwałość — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Cache potrzeby stanu trwałego pamięci podręcznej Redis.

* [Można włączyć trwałość dla utworzonej wcześniej pamięci podręcznej?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Można włączyć trwałość AOF i pliku RDB jest w tym samym czasie?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Model trwałości, który należy wybrać?](#which-persistence-model-should-i-choose)
* [Co się stanie, jeśli mam była skalowana do innego rozmiaru i przywróceniu kopii zapasowej został utworzony przed wykonaniem operacji skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trwałość RDB
* [Częstotliwość wykonywania kopii zapasowych pliku RDB można zmienić po utworzeniu pamięci podręcznej?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Dlaczego mam częstotliwość tworzenia kopii zapasowej RDB 60 minut czy ponad 60 minut między kopią zapasową?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co się dzieje starych kopii zapasowych pliku RDB po wysłaniu nową kopię zapasową?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Trwałość AOF
* [Kiedy należy używać drugie konto magazynu?](#when-should-i-use-a-second-storage-account)
* [Czy AOF trwałości mogą wpłynąć na całym, opóźnienia lub wydajność przepełnieniu pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak usunąć drugie konto magazynu?](#how-can-i-remove-the-second-storage-account)
* [Co to jest nadpisania i jak wpływa na przepełnieniu pamięci podręcznej?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Co mogę oczekiwać, gdy Skalowanie pamięci podręcznej przy użyciu funkcja aof jest włączona?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Jak Moje dane AOF jest zorganizowana w magazynie?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Można włączyć trwałość dla utworzonej wcześniej pamięci podręcznej?
Trwałość pamięci podręcznej Redis może być skonfigurowany tak, zarówno podczas tworzenia pamięci podręcznej, jak i dostępnych pamięci podręcznych premium.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Można włączyć trwałość AOF i pliku RDB jest w tym samym czasie?

Nie można włączyć tylko RDB lub AOF, ale nie obu jednocześnie.

### <a name="which-persistence-model-should-i-choose"></a>Model trwałości, który należy wybrać?

Trwałość AOF zapisuje każdego zapisu do dziennika, który ma kilka wpływu na przepustowość, w porównaniu z pliku RDB trwałości, co powoduje zapisanie kopii zapasowych oparte na skonfigurowanym interwałem wykonywania kopii zapasowej z minimalnym wpływem na wydajność. Wybierz AOF trwałości, jeśli podstawowym celem jest zminimalizować ryzyko utraty danych i może obsługiwać spadek przepustowości dla pamięci podręcznej. Wybierz opcję trwałości RDB, jeśli chcesz zachować optymalnej przepływności dla pamięci podręcznej, ale nadal ma mechanizm odzyskiwania danych.

* Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#rdb-advantages) i [wady](https://redis.io/topics/persistence#rdb-disadvantages) trwałość RDB.
* Dowiedz się więcej o [zalety](https://redis.io/topics/persistence#aof-advantages) i [wady](https://redis.io/topics/persistence#aof-disadvantages) AOF trwałość.

Aby uzyskać więcej informacji o wydajności, korzystając z AOF trwałości, zobacz [AOF jest trwałości mogą wpłynąć na całym, opóźnienia lub wydajność przepełnieniu pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co się stanie, jeśli mam była skalowana do innego rozmiaru i przywróceniu kopii zapasowej został utworzony przed wykonaniem operacji skalowania?

Aby uzyskać RDB i AOF trwałości:

* Jeśli była skalowana na większy rozmiar, nie ma żadnego wpływu.
* Jeśli mają być skalowane do mniejszego rozmiaru i ma niestandardowy [baz danych](cache-configure.md#databases) ustawienie, który jest większy niż [limit bazy danych](cache-configure.md#databases) dla Twojego nowego rozmiaru danych w tych bazach danych nie jest przywrócony. Aby uzyskać więcej informacji, zobacz [to niestandardowe ustawienie dotyczy podczas skalowania baz danych?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Jeśli mają być skalowane do mniejszego rozmiaru i mniejszy rozmiar do przechowywania wszystkich danych z ostatniej kopii zapasowej nie ma wystarczająco dużo miejsca, kluczy zostanie wykluczona podczas procesu przywracania, zazwyczaj przy użyciu [allkeys lru](https://redis.io/topics/lru-cache) zasady eksmisji.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Częstotliwość wykonywania kopii zapasowych pliku RDB można zmienić po utworzeniu pamięci podręcznej?
Tak, można zmienić częstotliwość wykonywania kopii zapasowych dla trwałości RDB na **trwałość danych Redis** bloku. Aby uzyskać instrukcje zobacz Konfigurowanie Redis trwałości.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Dlaczego mam częstotliwość tworzenia kopii zapasowej RDB 60 minut czy ponad 60 minut między kopią zapasową?
Interwał częstotliwości tworzenia kopii zapasowej trwałości RDB nie uruchamia, dopóki poprzedniego procesu tworzenia kopii zapasowej zakończyła się pomyślnie. Jeśli częstotliwości tworzenia kopii zapasowej jest 60 minut i potrzebny procesu tworzenia kopii zapasowej 15 minut do pomyślnego ukończenia, następnej kopii zapasowej nie będą naliczane do dnia 75 minut od czasu rozpoczęcia z poprzedniej kopii zapasowej.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co się dzieje starych kopii zapasowych pliku RDB po wysłaniu nową kopię zapasową?
Wszystkie kopie zapasowe trwałości RDB z wyjątkiem najnowszego są automatycznie usuwane. Takiego usunięcia nie może być realizowane natychmiast, ale starsze kopie zapasowe nie zostaną utrwalone przez czas nieokreślony.


### <a name="when-should-i-use-a-second-storage-account"></a>Kiedy należy używać drugie konto magazynu?

Drugie konto magazynu należy użyć potrzeby stanu trwałego AOF, gdy uznasz, że masz wyższe niż operacje oczekiwanego zestawu w pamięci podręcznej.  Konfigurowanie konta magazynu pomocniczego pomaga upewnić się, że pamięć podręczna nie dociera do limity przepustowości magazynu.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Czy AOF trwałości mogą wpłynąć na całym, opóźnienia lub wydajność przepełnieniu pamięci podręcznej?

Trwałość AOF ma wpływ na przepustowość przez około 15 – 20% po pamięci podręcznej znajduje się poniżej maksymalnego obciążenia (procesora CPU i serwera załadować zarówno w obszarze 90%). Nie powinno być opóźnień w przypadku pamięci podręcznej w tych granicach. Jednak pamięć podręczna będzie korzystał te limity wcześniej przy użyciu funkcja aof jest włączona.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak usunąć drugie konto magazynu?

Można usunąć konto magazynu pomocniczego trwałości AOF, ustawiając drugiego konta magazynu, aby być taka sama jak pierwszego konta magazynu. Aby uzyskać instrukcje, zobacz [AOF Konfigurowanie trwałości](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co to jest nadpisania i jak wpływa na przepełnieniu pamięci podręcznej?

Gdy plików AOF staje się wystarczająco duży, nadpisania automatycznie jest kolejkowana w pamięci podręcznej. Poprawione zmienia rozmiar plików AOF minimalny zestaw operacji wymaganych do utworzenia w bieżącym zestawie danych. Podczas modyfikacji oprogramowania należy oczekiwać wcześniej osiągnięcie limity wydajności, szczególnie w przypadku pracy z dużymi zestawami danych. Modyfikacji oprogramowania występuje mniej często, jak plików AOF staje się większy, ale będzie zająć dużo czasu, kiedy się dzieje.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Co mogę oczekiwać, gdy Skalowanie pamięci podręcznej przy użyciu funkcja aof jest włączona?

W przypadku znacznie dużych plików AOF w czasie skalowania się spodziewać na dłużej, niż oczekiwano, ponieważ jej będzie można ponownego ładowania pliku po zakończeniu skalowanie operacji skalowania.

Aby uzyskać więcej informacji na temat skalowania, zobacz [co się stanie, jeśli mam była skalowana do innego rozmiaru i przywróceniu kopii zapasowej został utworzony przed wykonaniem operacji skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Jak Moje dane AOF jest zorganizowana w magazynie?

Dane przechowywane w plikach AOF jest podzielony na wiele stronicowe obiekty BLOB w każdym węźle, aby zwiększyć wydajność zapisywania danych do magazynu. W poniższej tabeli przedstawiono, jak wiele stronicowe obiekty BLOB są używane dla każdej warstwy cenowej:

| Warstwa Premium | Obiekty blob |
|--------------|-------|
| P1           | 4 na fragment    |
| P2           | 8 na fragment    |
| P3           | 16 na fragment   |
| P4           | 20 na fragment   |

Gdy klaster jest włączona, każdy fragment w pamięci podręcznej ma swój własny zestaw stronicowych obiektów blob, jak wskazano w powyższej tabeli. Na przykład P2 pamięć podręczną z trzech fragmentów rozkłada jego plików AOF na 24 stronicowych obiektów blob (8 obiektów blob na fragment, za pomocą fragmentów 3).

Po nadpisania dwa zestawy plików AOF istnieje w magazynie. Ponownego występują w tle i Dołącz do pierwszego zestawu plików, podczas gdy operacje na zestawie, które są wysyłane do pamięci podręcznej podczas ponownego zapisywania dołączania do drugiego zestawu. Tworzenie kopii zapasowej są tymczasowo przechowywane podczas modyfikacji oprogramowania w przypadku awarii, ale niezwłocznie zostanie usunięty po zakończeniu ponownego zapisywania.


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak korzystać z funkcji premium więcej w pamięci podręcznej.

* [Wprowadzenie do platformy Azure pamięci podręcznej Redis w warstwie Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
