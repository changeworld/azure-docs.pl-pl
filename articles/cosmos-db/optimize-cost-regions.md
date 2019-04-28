---
title: Optymalizowanie kosztów dla wdrożeń w wielu regionach w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami wdrażania w wielu regionach w usłudze Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 012eacb172acfdeb0b82343c484c664a3f75310e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929308"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optymalizuj koszt wielu regionów w usłudze Azure Cosmos DB

Można dodać i usunąć regiony z kontem usługi Azure Cosmos w dowolnym momencie. Przepływność, którą można skonfigurować dla różnych baz danych Azure Cosmos i kontenerów jest zarezerwowana w każdym z regionów skojarzonych z Twoim kontem. Jeśli aprowizowana przepływność na godzinę, to Suma jednostek RU/s jest skonfigurowany we wszystkich bazach danych i kontenerów, jest kontem usługi Azure Cosmos `T` a liczbą regionów platformy Azure skojarzony z Twoim kontem bazy danych jest `N`, następnie łącznie aprowizowana przepływność dla Twojego konta usługi Cosmos, dla danej godziny wynosi:

1. `T x N RU/s` Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowany z regionem zapisu jednego. 

1. `T x (N+1) RU/s` Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowany we wszystkich regionach, która może przetwarzać zapisy. 

Aprowizowana przepływność z regionu zapisu jednego kosztuje $0.008 za godzinę dla 100 jednostek RU/s i aprowizowaną przepływność z wieloma regionami zapisywalny koszty 0,016; $/ za godzinę za 100 jednostek RU/s. Aby dowiedzieć się więcej, zobacz temat usługi Azure Cosmos DB [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Koszty dla wielu regionów zapisu

W systemie wielu wzorców, net dostępne (RUS) dla zapisu operacji zwiększa `N` razy gdzie `N` jest liczbą regionów zapisu. W przeciwieństwie do zapisu w jednym regionie co region teraz jest zapisywalny i powinien obsługiwać rozwiązywanie konfliktów. Zwiększa ilość obciążenia dla modułów zapisujących. Od opłat planowania punktu widzenia, aby wykonać `M` , przez które jednostek RU/s zapisy na całym świecie, musisz aprowizować M `RUs` na poziomie kontenera lub bazy danych. Następnie możesz dodać dowolną liczbę regionów, jak chcesz i używać ich do zapisu do `M` RU, przez które zapisy na całym świecie. 

### <a name="example"></a>Przykład

Należy wziąć pod uwagę w masz kontener w regionie zachodnie stany USA aprowizowany przy przepływności 10 tys jednostek RU/s i zapisuje 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzech regionów — wschodnie stany USA, Europa Północna i Azja Wschodnia, każdy z tego samego magazynu i przepływności i ma możliwość zapisywania do kontenerów we wszystkich czterech regionach z globalnie rozproszonych aplikacji. Twoje łączna kwota rachunku miesięcznego (przy założeniu 31 dni) w danym miesiącu jest następująca:

|**Element**|**Użycie (miesiąc)**|**Kurs**|**Koszt miesięczny**|
|----|----|----|----|
|Rachunek za przepływność dla kontenera w regionie zachodnie stany USA (zapisu wielu regionów) |10 K jednostek RU/s * 24 * 31 |0,016; $ na 100 jednostek RU/s za godzinę |$1,190.40 |
|Rachunek za przepływność dla 3 dodatkowych regionów — wschodnie stany USA, Europa Północna i Azja Wschodnia (zapisu wielu regionów) |(3 + 1) * 10 K jednostek RU/s * 24 * 31 |0,016; $ na 100 jednostek RU/s za godzinę |$4,761.60 |
|Rachunek za przestrzeń dyskową dla kontenera w regionie Zachodnie stany USA |100 GB |0,25 USD/GB |$25 |
|Rachunek za przestrzeń dyskową dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia |3 * 1 TB |0,25 USD/GB |$75 |
|**Łączna liczba**|||**$6,052** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Poprawić wykorzystanie przepływności na poszczególnych regionów

Jeśli wykorzystanie, na przykład, co najmniej jedna niewykorzystywanych lub nadmiernie wykorzystywany regionów, można wykonać poniższe kroki, aby poprawić wykorzystanie przepustowości:  

1. Upewnij się, najpierw zoptymalizować aprowizowana przepływność (ru) w regionie zapisu i dokonaj maksymalnego wykorzystania jednostki zarezerwowane w regionach odczytu przy użyciu zmian źródła danych z regionu odczytu itp. 

2. Odczytuje w wielu regionach zapisu i zapisy mogą być skalowane we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. 

3. Monitoruj aktywność w swojej regionów i może dodawać i usuwać regiony na żądanie, aby skalować swoje odczytu i zapisu przepływności.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz przejść do Dowiedz się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB z następujących artykułów:

* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [informacje o rachunku Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)

