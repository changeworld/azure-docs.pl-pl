---
title: Jednostki żądań i przepływność w Azure Cosmos DB
description: Dowiedz się, jak określić i oszacować wymagania jednostki żądań w Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383108"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądań w Azure Cosmos DB

Dzięki Azure Cosmos DB płacisz za zainicjowaną przepływność i ilość miejsca do magazynowania zużywanego co godzinę. Obsługa przepływności musi być dostępna, aby zapewnić, że dla bazy danych usługi Azure Cosmos są wystarczające zasoby systemowe. Potrzebujesz wystarczającej ilości zasobów, aby spełnić lub przekroczyć [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji. 

Koszt wszystkich operacji bazy danych jest znormalizowany przez Azure Cosmos DB i jest wyrażony przez *jednostki żądania* (lub jednostek ru, dla krótkich). Jednostek ru na sekundę można traktować jako walutę dla przepływności. Jednostek ru na sekundę to waluta oparta na stawkach. Jest to abstrakcyjne zasoby systemowe, takie jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB. 

Koszt odczytania 1 KB elementu to 1 jednostka żądania (lub 1 RU). Co najmniej 10 RU/s jest wymagany do przechowywania każdego 1 GB danych. Wszystkie inne operacje bazy danych są podobne do kosztów za pomocą jednostek ru. Niezależnie od tego, który interfejs API jest używany do współpracy z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone przez jednostek ru. Niezależnie od tego, czy operacja bazy danych jest zapisem, odczytem lub zapytaniem, koszty są zawsze mierzone w jednostek ru.

Na poniższej ilustracji przedstawiono ogólny pomysł jednostek ru:

![Operacje bazy danych zużywają jednostki żądań](./media/request-units/request-units.png)

Aby zarządzać pojemnością i planować ją, Azure Cosmos DB zapewnia, że liczba jednostek ru dla danej operacji bazy danych w danym zestawie danych jest deterministyczna. Możesz sprawdzić nagłówek odpowiedzi, aby śledzić liczbę jednostek ru używanych przez dowolną operację bazy danych. Gdy zrozumiesz [czynniki wpływające na opłaty za usługę ru](request-units.md#request-unit-considerations) i wymagania dotyczące przepływności aplikacji, możesz efektywnie uruchamiać swoją aplikację.

Liczba jednostek ru dla aplikacji jest inicjowana na sekundę w przyrostach wynoszących 100 jednostek ru na sekundę. Aby skalować zainicjowaną przepływność dla aplikacji, możesz w dowolnym momencie zwiększyć lub zmniejszyć liczbę jednostek ru. Możesz skalować przyrosty lub zmniejszyć liczbę 100 jednostek ru. Zmiany można wprowadzać programowo lub za pomocą Azure Portal. Opłaty są naliczane godzinowo.

Przepustowość można zainicjować na dwa różne szczegóły: 

* **Kontenery**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* **Bazy danych**: Aby uzyskać więcej informacji, zobacz temat [udostępnianie przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania

Podczas szacowania liczby jednostek ru na sekundę do aprowizacji należy wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu**: w miarę wzrostu rozmiaru elementu, liczba jednostek ru zużytych do odczytu lub zapisu elementu również rośnie.

* **Indeksowanie elementów**: Domyślnie każdy element jest automatycznie indeksowany. Jeśli zdecydujesz się nie indeksować niektórych elementów w kontenerze, są używane mniej jednostek ru.

* **Liczba właściwości elementów**: przy założeniu, że domyślne indeksowanie jest we wszystkich właściwościach, liczba jednostek ru zużytych do napisania elementu zwiększa się wraz ze wzrostem liczby właściwości elementu.

* **Właściwości indeksowane**: zasady indeksu dla każdego kontenera określają, które właściwości są indeksowane domyślnie. Aby zmniejszyć zużycie RU dla operacji zapisu, należy ograniczyć liczbę indeksowanych właściwości.

* **Spójność danych**: mocne i ograniczone nieodświeżone poziomy spójności zużywają około dwa razy więcej jednostek ru podczas wykonywania operacji odczytu w porównaniu z innymi obniżonymi poziomami spójności.

* **Wzorce zapytań**: złożoność zapytania wpływa na liczbę jednostek ru używanych dla operacji. Czynniki wpływające na koszt operacji związanych z kwerendą obejmują: 
    
    - Liczba wyników zapytania
    - Liczba predykatów
    - Charakter predykatów
    - Liczba funkcji zdefiniowanych przez użytkownika
    - Rozmiar danych źródłowych.
    - Rozmiar zestawu wyników.
    - Projekcje

  Azure Cosmos DB gwarantuje, że te same zapytania dotyczące tych samych danych są zawsze kosztami tej samej liczby jednostek ru przy powtarzanych wykonaniach.

* **Użycie skryptu**: podobnie jak w przypadku kwerend, procedury składowane i wyzwalacze wykorzystują jednostek RU w oparciu o złożoność wykonywanych operacji. Podczas opracowywania aplikacji Sprawdź [nagłówek opłata za żądanie](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) , aby lepiej zrozumieć, jak dużo zdolności produkcyjnych zużywa każda operacja.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tym, jak zainicjować przepływność w kontenerach i bazach danych usługi Azure Cosmos](set-throughput.md).
* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się więcej o tym, jak [globalnie skalować zainicjowaną przepływność](scaling-throughput.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
* Dowiedz się [, jak znaleźć opłatę jednostkową żądania dla operacji](find-request-unit-charge.md).
* Informacje na temat [optymalizowania alokowanego kosztu przepływności w Azure Cosmos DB](optimize-cost-throughput.md).
* Dowiedz się [, jak zoptymalizować operacje odczytu i zapisu w Azure Cosmos DB](optimize-cost-reads-writes.md).
* Dowiedz się, jak [zoptymalizować koszt zapytań w Azure Cosmos DB](optimize-cost-queries.md).
* Dowiedz się [, jak używać metryk do monitorowania przepływności](use-metrics.md).
