---
title: Optymalizacja kosztów wdrożeń wieloregionowych w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami wdrożeń wieloregiona w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753318"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów korzystania z wielu regionów w usłudze Azure Cosmos DB

Regiony usługi Azure Cosmos można w dowolnym momencie dodawać i usuwać. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos jest zarezerwowana w każdym regionie skojarzonym z Twoim kontem. Jeśli przepływność aprowizowana na godzinę, jest to suma usług RU/s skonfigurowanych we `T` wszystkich bazach danych i kontenerach dla `N`konta usługi Azure Cosmos, a liczba regionów platformy Azure skojarzonych z kontem bazy danych jest , a następnie całkowita aprowizowana przepływność dla konta usługi Cosmos, dla danej godziny jest równa:

1. `T x N RU/s`jeśli twoje konto usługi Azure Cosmos jest skonfigurowane z jednym regionem zapisu. 

1. `T x (N+1) RU/s`jeśli twoje konto usługi Azure Cosmos jest skonfigurowane ze wszystkimi regionami zdolnymi do przetwarzania zapisów. 

Aprowizowana przepływność z pojedynczym regionem zapisu kosztuje 0,008 USD/godz. na każde 100 RU/s, natomiast aprowizowana przepływność z wieloma regionami zapisu kosztuje 0,016 USD/godz. na każde 100 RU/s. Aby dowiedzieć się więcej, zobacz [stronę Cennik](https://azure.microsoft.com/pricing/details/cosmos-db/)bazy danych Usługi Azure Cosmos DB .

## <a name="costs-for-multiple-write-regions"></a>Koszty dla wielu regionów zapisu

W systemie wielozadaniowym netto dostępne w `N` sieci `N` dla operacji zapisu zwiększa czas, w którym jest liczba regionów zapisu. W przeciwieństwie do zapisów w jednym regionie każdy region jest teraz zapisywalny i powinien obsługiwać rozwiązywanie konfliktów. Zwiększyła się ilość obciążenia dla modułów zapisu. Z punktu widzenia planowania kosztów, aby wykonać `M` wartość R/s zapisów `RUs` na całym świecie, należy aprowizować M na poziomie kontenera lub bazy danych. Następnie można dodać dowolną liczbę regionów i używać ich do `M` zapisów do wykonywania wartości RU na całym świecie. 

### <a name="example"></a>Przykład

Należy wziąć pod uwagę kontener w zachodnie stany USA aprowizować z przepływnością 10K RU/s i przechowuje 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony — wschodnie stany USA, Europa Północna i Azja Wschodnia, z których każdy ma ten sam magazyn i przepływność, a także chcesz mieć możliwość zapisu w kontenerach we wszystkich czterech regionach z aplikacji dystrybuowanych globalnie. Całkowity miesięczny rachunek (przy założeniu 31 dni) w miesiącu jest następujący:

|**Element**|**Użycie (miesięczne)**|**Stawka**|**Koszt miesięczny**|
|----|----|----|----|
|Rachunek przepływności dla kontenera w zachodnie stany USA (wiele regionów zapisu) |10K RU/s * 24 * 31 |$0.016 za 100 RU/s na godzinę |1 190,40 usd |
|Rachunek przepływności dla 3 dodatkowych regionów - Wschodnie stany USA, Europa Północna i Azja Wschodnia (wiele regionów zapisu) |(3 + 1) * 10K RU/s * 24 * 31 |$0.016 za 100 RU/s na godzinę |4 761,60 usd |
|Rachunek za magazyn kontenera w zachodnich stanach USA |1 TB (lub 1024 GB) |zł./GB |zł. |
|Rachunek za magazynowanie dla 3 dodatkowych regionów - Wschodnie stany USA, Europa Północna i Azja Wschodnia |3 * 1 TB (lub 3072 GB) |zł./GB |zł. |
|**Łącznie**|||**6 976 usd** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zwiększ wykorzystanie przepustowości w zależności od regionu

Jeśli masz nieefektywne wykorzystanie, na przykład jeden lub więcej niedostateczne wykorzystanie lub nadmiernie wykorzystywane regiony, można podjąć następujące kroki w celu zwiększenia wykorzystania przepustowości:  

1. Upewnij się, że najpierw zoptymalizować aprowizowaną przepływność (RUs) w regionie zapisu, a następnie maksymalne użycie procesorów RU w regionach odczytu przy użyciu źródła danych zmian z regionu odczytu itp. 

2. Wiele regionów zapisu odczytuje i zapisuje można skalować we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. 

3. Monitoruj aktywność w regionach i możesz dodawać i usuwać regiony na żądanie, aby skalować przepływność odczytu i zapisu.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB, aby uzyskać następujące artykuły:

* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [opisie rachunku za usługę Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)

