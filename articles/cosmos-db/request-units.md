---
title: Żądania jednostek i przepływności w usłudze Azure Cosmos DB
description: Dowiedz się, jak określić i oszacować wymagania jednostki żądań w usłudze Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246593"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądania w usłudze Azure Cosmos DB

W usłudze Azure Cosmos DB płacisz za aprowizowaną przepływność i ilość zużytego miejsca do magazynowania rozliczanych co godzinę. Przepływność należy aprowizować, aby zapewnić stałą dostępność wystarczających zasobów systemowych dla bazy danych usługi Azure Cosmos. Potrzebujesz wystarczającej ilości zasobów, aby spełnić lub przekroczyć [100 000 000 000 000 000 000 000 000 000 000 000](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

Usługa Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje wahają się od prostych odczytów punktowych i zapisów do złożonych zapytań. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji. 

Koszt wszystkich operacji bazy danych jest znormalizowany przez usługę Azure Cosmos DB i jest wyrażany przez *jednostki żądań* (lub jednostek RUs, w skrócie). Liczbę jednostek RU na sekundę można traktować jak walutę przepływności. Liczba jednostek RU na sekundę to waluta oparta na stawce. Tworzy ona abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonywania operacji bazy danych obsługiwanych przez usługę Azure Cosmos DB. 

Koszt odczytania 1 KB elementu to 1 jednostka żądania (lub 1 RU). Do przechowywania każdego 1 GB danych wymagane jest co najmniej 10 ru/s. Wszystkim innym operacjom bazy danych koszt jest przypisywany w podobny sposób za pomocą jednostek RU. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, odczytem czy zapytaniem, koszty są zawsze mierzone w jednostkach RU.

Na poniższej ilustracji przedstawiono ogólną koncepcję jednostek RU:

![Operacje bazy danych zużywają jednostki żądań](./media/request-units/request-units.png)

Aby można było zarządzać pojemnością i ją planować, usługa Azure Cosmos DB zapewnia, że liczba jednostek RU dla danej operacji bazy danych w danym zestawie danych jest deterministyczna. Możesz sprawdzić nagłówek odpowiedzi, aby śledzić liczbę jednostek RU zużywanych przez każdą operację bazy danych. Po zrozumieniu [czynników, które wpływają na opłaty RU](request-units.md#request-unit-considerations) i wymagania dotyczące przepływności aplikacji, można uruchomić aplikację efektywnie.

Dla swojej aplikacji aprowizujesz liczbę jednostek RU na sekundę w przyrostach wynoszących 100 jednostek RU na sekundę. Aby skalować aprowizowaną przepływność dla aplikacji, możesz w dowolnym momencie zwiększyć lub zmniejszyć liczbę jednostek RU. Możesz skalować w przedziałach co 100 jednostek RU. Zmiany można wprowadzać programowo lub za pomocą witryny Azure Portal. Opłaty są naliczane godzinowo.

Można aprowizować przepływność w dwóch różnych ziarnistości: 

* **Kontenery:** Aby uzyskać więcej informacji, zobacz [Aprowizuj przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)
* **Bazy danych:** Aby uzyskać więcej informacji, zobacz [Aprowizuj przepływność w bazie danych usługi Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Kwestie dotyczące jednostek żądań

W przypadku szacowania liczby jednostek RU na sekundę do aprowizowania należy wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu**: Wraz ze wzrostem rozmiaru elementu zwiększa się również liczba procesorów RU zużytych do odczytu lub zapisu elementu.

* **Indeksowanie towarów**: Domyślnie każdy element jest automatycznie indeksowany. Jeśli wyłączysz indeksowanie dla niektórych elementów w kontenerze, zostanie użytych mniej jednostek RU.

* **Liczba właściwości towaru:** Przy założeniu, że domyślne indeksowanie dotyczy wszystkich właściwości, liczba obiektów RU zużytych do zapisu towaru wzrasta wraz ze wzrostem liczby właściwości towaru.

* **Właściwości indeksowane:** Zasady indeksu dla każdego kontenera określają, które właściwości są domyślnie indeksowane. Aby zmniejszyć zużycie jednostek RU operacji zapisu, ogranicz liczbę indeksowanych właściwości.

* **Spójność danych**: poziom spójności silnego i ograniczonego nieaktualności zużywa około dwa razy więcej procesorów RU podczas wykonywania operacji odczytu w porównaniu z innymi poziomami spójności zrelaksowanej.

* **Wzorce zapytań:** Złożoność kwerendy wpływa na liczbę jednostek rr są używane dla operacji. Czynniki mające wpływ na koszt operacji zapytań obejmują następujące elementu: 
    
    - Liczba wyników zapytań
    - Liczba predykatów
    - Rodzaj predykatów
    - Liczba funkcji zdefiniowanych przez użytkownika
    - Rozmiar danych źródłowych
    - Rozmiar zestawu wyników
    - Projekcje

  Usługa Azure Cosmos DB pozwala zagwarantować, że to samo zapytanie dotyczące tych samych danych zawsze kosztuje tyle samo jednostek RU w przypadku wielokrotnego wykonania.

* **Użycie skryptu:** Podobnie jak w przypadku zapytań, procedury przechowywane i wyzwalacze zużywają procesory RU na podstawie złożoności wykonywanych operacji. Podczas opracowywania aplikacji sprawdź [nagłówek opłaty za żądanie](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query), aby lepiej zrozumieć, ile jednostek RU używa każda operacja.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [sposobie aprowizowania przepływności w kontenerach i bazach danych usługi Azure Cosmos.](set-throughput.md)
* Dowiedz się więcej o [partycjach logicznych](partition-data.md).
* Dowiedz się więcej o [globalnej skalowanej przepływności aprowizowanej](scaling-throughput.md).
* Dowiedz się, jak [aprowizować przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)
* Dowiedz się, jak [aprowizować przepływność w bazie danych usługi Azure Cosmos.](how-to-provision-database-throughput.md)
* Dowiedz się, jak [znaleźć opłatę za jednostkę żądania dla operacji](find-request-unit-charge.md).
* Dowiedz się, jak [zoptymalizować aprowizowaną przepustowość w usłudze Azure Cosmos DB.](optimize-cost-throughput.md)
* Dowiedz się, jak [zoptymalizować koszty odczytów i zapisów w usłudze Azure Cosmos DB.](optimize-cost-reads-writes.md)
* Dowiedz się, jak [zoptymalizować koszt zapytania w usłudze Azure Cosmos DB.](optimize-cost-queries.md)
* Dowiedz się, jak [używać danych do monitorowania przepływności.](use-metrics.md)
