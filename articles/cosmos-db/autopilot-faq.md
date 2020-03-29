---
title: Często zadawane pytania dotyczące przepływności w trybie autopilota usługi Azure Cosmos DB
description: Często zadawane pytania dotyczące trybu autopilota dla baz danych i kontenerów usługi Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483312"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Często zadawane pytania dotyczące aprowizowanej przepływności w trybie autopilota usługi Azure Cosmos DB (Wersja zapoznawcza)
W trybie autopilota usługa Azure Cosmos DB automatycznie zarządza i skaluje ru/s kontenera lub bazy danych na podstawie użycia. W tym artykule odpowiedzi na często zadawane pytania dotyczące trybu autopilota. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Czy tryb autopilota jest obsługiwany dla wszystkich interfejsów API?
Tak, tryb autopilota jest obsługiwany dla wszystkich interfejsów API: Core (SQL), Gremlin, Table, Cassandra i API dla MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>Czy tryb autopilota jest obsługiwany dla kont wielo-głównych?
Tak, tryb autopilota jest obsługiwany dla kont wielo-głównych. Maksymalna liczba ru/s jest dostępna w każdym regionie, który jest dodawany do konta Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Jakie są ceny autopilota?
Szczegółowe informacje można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/cosmos-db/) usługi Azure Cosmos DB. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Jak włączyć autopilota dla moich kontenerów lub baz danych?
Tryb autopilota można włączyć w nowych kontenerach i bazach danych utworzonych za pomocą witryny Azure portal. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Czy istnieje obsługa interfejsu wiersza lub sdk do tworzenia kontenerów lub baz danych w trybie autopilota?
Obecnie w wersji zapoznawczej można tworzyć zasoby tylko w trybie autopilota z witryny Azure portal. Obsługa interfejsu WIERSZA POLECENIA i SDK nie jest jeszcze dostępna.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Czy mogę włączyć autopilota w istniejącym kontenerze lub bazie danych?
Obecnie można włączyć autopilota na nowych kontenerów i baz danych podczas ich tworzenia. Obsługa włączania trybu autopilota w istniejących kontenerach i bazach danych nie jest jeszcze dostępna. Istniejące kontenery można migrować do nowego kontenera przy użyciu [usługi Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) lub [zmienić źródło danych](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Czy mogę wyłączyć tryb autopilota w kontenerze lub bazie danych?
Tak, możesz wyłączyć autopilota, przełączając się na opcję "Ręczny" dla aprowizowanej przepływności. W wersji zapoznawczej po przełączeniu z trybu autopilota na tryb ręczny nie można ponownie włączyć autopilota dla tego samego zasobu. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>Czy tryb autopilota jest obsługiwany dla udostępnionych baz danych przepływności?
Tak, tryb autopilota jest obsługiwany dla udostępnionych baz danych przepływności. Aby włączyć tę funkcję, podczas tworzenia bazy danych wybierz tryb autopilota i opcję **Aprowizuj przepływność.** 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Jaka jest liczba dozwolonych kolekcji na udostępnioną bazę danych przepływności, gdy autopilot jest włączony?
W przypadku udostępnionych baz danych przepływności z włączonym trybem autopilota liczba dozwolonych kolekcji to: MIN(25, Max RU/s bazy danych / 1000). Na przykład jeśli maksymalna przepływność bazy danych wynosi 20 000 RU/s, baza danych może mieć MIN(25, 20 000 RU/s / 1000) = 20 kolekcji. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Jaki jest limit magazynowania skojarzony z każdą opcją max RU/s?  
Limit miejsca w GB dla każdego max RU/s wynosi: Max RU/s bazy danych lub kontenera / 100. Na przykład jeśli maksymalna wartość RU/s wynosi 20 000 ru/s, zasób może obsługiwać 200 GB pamięci masowej. Zobacz artykuł o [limitach autopilota,](provision-throughput-autopilot.md#autopilot-limits) aby zapoznać się z dostępnymi maksymalnymi urządzeniami RU/s i opcjami pamięci masowej. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Co się stanie, jeśli przekroczę limit magazynowania skojarzony z maksymalną przepustowością?
Jeśli limit magazynu skojarzony z maksymalną przepływnością bazy danych lub kontenera zostanie przekroczony, usługa Azure Cosmos DB automatycznie zwiększy maksymalną przepływność do następnej najwyższej warstwy, która może obsługiwać ten poziom magazynu. Załóżmy na przykład, że baza danych lub kontener jest aprowizowana za pomocą opcji maksymalnej przepływności 4000 RU/s, która ma limit magazynowania 50 GB. Jeśli magazyn zasobu wzrośnie do 100 GB, maksymalna liczba ru/s bazy danych lub kontenera zostanie automatycznie zwiększona do 20 000 ru/s, co może obsługiwać do 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Jak szybko autopilot będzie skalować w górę iw dół w oparciu o skoki w ruchu?
Autopilot natychmiast skaluje się w górę lub skaluj w dół RU/s w ramach minimalnego i maksymalnego zakresu RU/s, w zależności od ruchu przychodzącego. Rozliczenie odbywa się z 1-godzinną szczegółowością, gdzie pobierana jest opłata za najwyższy wskaźnik RU/s w określonej godzinie. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Czy mogę określić niestandardową wartość maksymalnej przepustowości (RU/s) dla trybu autopilota?
Obecnie podczas wersji zapoznawczej można wybrać jedną z [czterech opcji](provision-throughput-autopilot.md#autopilot-limits) dla maksymalnej przepływności (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Czy mogę zwiększyć maksymalną liczbę ru/s (przejście do wyższej warstwy) w bazie danych lub kontenerze? 
Tak. W opcji **Skalowanie & ustawienia** dla kontenera lub Opcja **Skaluj** bazę danych można wybrać wyższy maksymalny poziom RU/s dla autopilota. Jest to asynchroniowa operacja skalowania w górę, która może potrwać kiedyś (zazwyczaj 4-6 godzin, w zależności od wybranego różu RU/s), ponieważ usługa zapewnia więcej zasobów do obsługi wyższej skali. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Czy mogę zmniejszyć maksymalną liczbę ru/s (przejście do niższej warstwy) w bazie danych lub kontenerze?
Tak. Tak długo, jak bieżące przechowywanie bazy danych lub kontenera jest poniżej [limitu magazynu skojarzonego](#what-is-the-storage-limit-associated-with-each-max-rus-option) z warstwą max RU/s, do której chcesz skalować w dół, można zmniejszyć maksymalną liczbę RU/s do tej warstwy. Na przykład jeśli wybrano 20 000 RU/s jako max RU/s, można skalować w dół maksymalną liczbę RU/s do 4000 RU/s, jeśli masz mniej niż 50 GB miejsca (limit miejsca pracy skojarzony z 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Co to jest mapowanie między max RU/s i partycji fizycznych?
Po pierwszym wybraniu maksymalnej ru/s, usługa Azure Cosmos DB będzie aprowizować: Max RU/s / 10,000 RU/s = # partycji fizycznych. Każda [partycja fizyczna](partition-data.md#physical-partitions) może obsługiwać do 10 000 dysków RU/s i 50 GB. Wraz ze wzrostem rozmiaru magazynu usługa Azure Cosmos DB automatycznie podzieli partycje, aby dodać więcej partycji fizycznych do obsługi zwiększenia magazynu lub zwiększyć maksymalną warstwę RU/s, jeśli magazyn [przekracza skojarzony limit.](#what-is-the-storage-limit-associated-with-each-max-rus-option) 

Max RU/s bazy danych lub kontenera jest podzielony równomiernie na wszystkie partycje fizyczne. Tak więc całkowita przepływność dowolnej pojedynczej partycji fizycznej może być skalowana do: Max RU/s bazy danych lub kontenera / # partycje fizyczne. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Co się stanie, jeśli przychodzące żądania przekraczają maksymalną liczbę ru/s bazy danych lub kontenera?
Jeśli całkowita zużyta liczba zużytych ru/s przekracza maksymalną liczbę ru/s kontenera lub bazy danych, żądania przekraczające maksymalną liczbę ru/s zostaną ograniczone i zwróci kod stanu 429. Żądania, które powodują ponad 100% znormalizowane wykorzystanie będzie również ograniczona. Znormalizowane wykorzystanie jest definiowane jako maksymalne wykorzystanie RU/s we wszystkich partycjach fizycznych. Załóżmy na przykład, że maksymalna przepływność wynosi 20 000 ru/s i masz dwie partycje fizyczne, P_1 i P_2, z których każda może być skalowane do 10 000 RU/s. W danym drugim, jeśli P_1 użył 6000 RU i P_2 8000 RU, znormalizowanym wykorzystaniem jest MAX(6000 RU / 10 000 RU, 8000 RU / 10 000 RU) = 0,8.

> [!NOTE]
> Zestawy SDK klienta usługi Azure Cosmos DB i narzędzia importowania danych (usługa Azure Data Factory, biblioteka zbiorczego executora) automatycznie ponawiają próbę w 429s, więc sporadyczne narzędzia 429s są w porządku. Utrzymująca się duża liczba 429s może wskazywać, że trzeba zwiększyć max RU / s lub przeglądu strategii partycjonowania dla [gorącej partycji](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Czy nadal można zobaczyć 429s (ograniczanie przepustowości / ograniczenia szybkości) po włączeniu autopilota? 
Tak. Jest możliwe, aby zobaczyć 429s w dwóch scenariuszach. Po pierwsze, gdy całkowita zużyta liczba zużytych ru/s przekracza maksymalną liczbę ru/s kontenera lub bazy danych, usługa odpowiednio ograniczy żądania. 

Po drugie, jeśli istnieje partycja gorąca, czyli wartość klucza partycji logicznej, która ma nieproporcjonalnie większą ilość żądań w porównaniu z innymi wartościami klucza partycji, możliwe jest, że podstawowa partycja fizyczna przekroczy budżet RU/s. Najlepszym rozwiązaniem, aby uniknąć gorących partycji, [wybierz dobry klucz partycji,](partitioning-overview.md#choose-partitionkey) który powoduje równomierną dystrybucję magazynu i przepływności. 

Na przykład jeśli wybierzesz opcję przepływności 20 000 RU/s max i masz 200 GB miejsca na magazyn, z czterema partycjami fizycznymi, każda partycja fizyczna może być skalowaną automatycznie do 5000 RU/s. Jeśli nie było gorącej partycji na określonym kluczu partycji logicznej, zobaczysz 429s, gdy podstawowa partycja fizyczna znajduje się w przekracza 5000 RU/s, czyli przekracza 100% znormalizowane wykorzystanie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [włączyć autopilota w kontenerze lub bazie danych usługi Azure Cosmos.](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode)
* Dowiedz się więcej o [zaletach autopilota](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Dowiedz się więcej o [partycjach logicznych i fizycznych](partition-data.md).
