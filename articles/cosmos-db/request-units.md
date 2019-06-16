---
title: Jednostek żądania i przepływności w usłudze Azure Cosmos DB
description: Dowiedz się więcej o tym, jak określić i ocenić wymagania jednostek żądania w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.openlocfilehash: 9615aebd345c957c8e401581ff94735f39ba73c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65953536"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądań w usłudze Azure Cosmos DB

Za pomocą usługi Azure Cosmos DB płacisz za przepływność możesz aprowizować i magazynu, których możesz używać w systemie godzinowym. Przepływność musi być obsługiwana, aby upewnić się, że wystarczających zasobów systemu są dostępne dla bazy danych Azure Cosmos przez cały czas. Potrzebujesz wystarczającej liczby zasobów, które spełniają lub przekraczają najbardziej [usługi Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Usługa Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i tabeli. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Rozwiązania z zakresu operacji od prostych punktu wykonującej Odczyt i zapis złożonych zapytań. Każda operacja bazy danych wykorzystuje zasoby systemowe, zależnie od stopnia złożoności operacji. 

Koszt wszystkie operacje bazy danych jest znormalizować przez usługę Azure Cosmos DB i jest wyrażany za *jednostek żądań* (lub jednostek w skrócie). Jednostki zarezerwowane można traktować na sekundę przepływności jako waluta. Jednostek ru na sekundę jest oparte na stawkach walutę. Przenosi jego zasobów systemowych, takich jak procesor CPU, operacje We/Wy i pamięci, które są wymagane do wykonywania operacji bazy danych, obsługiwane przez usługę Azure Cosmos DB. 

Koszt odczytu 1 KB jest element 1 jednostka żądania (lub 1 RU). Wszystkie inne operacje dotyczące bazy danych są przypisywane podobnie kosztami przy użyciu jednostek RU. Niezależnie od tego, którego interfejs API umożliwia interakcję z kontenera usługi Azure Cosmos koszty są zawsze mierzone przez (RUS). Operacja bazy danych zapisu, czy odczytu lub zapytania, koszty są zawsze mierzona w (RUS).

Na poniższej ilustracji przedstawiono koncepcję wysokiego poziomu (RUS):

![Operacje bazy danych zużywają jednostek żądań](./media/request-units/request-units.png)

Aby zarządzać i planowanie pojemności, usługi Azure Cosmos DB zapewnia liczbę jednostek zarezerwowanych dla operacji bazy danych za pośrednictwem określonego zestawu danych deterministyczna. Nagłówek odpowiedzi, aby śledzić liczbę jednostek ru zużywanych przez żadnych operacji bazy danych, można sprawdzić. Gdy rozumiesz [czynniki mające wpływ na opłaty RU](request-units.md#request-unit-considerations) i wymagań przepływność aplikacji, możesz uruchomić aplikację, ekonomiczne.

Liczba jednostek ru możesz aprowizować dla aplikacji na podstawie na sekundę w przyrostach 100 jednostek ru na sekundę. Aby skalować aprowizowanej przepływności dla aplikacji, można zwiększyć lub zmniejszyć liczbę jednostek żądań w dowolnym momencie. Możesz skalować w zwiększa lub zmniejsza 100 jednostek RU. Można wprowadź zmiany, programowo lub za pomocą witryny Azure portal. Opłaty są naliczane godzinowo.

Użytkownik może aprowizować przepływność mierzoną w dwóch odrębnych stopniach szczegółowości: 

* **Kontenery**: Aby uzyskać więcej informacji, zobacz [Aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* **Bazy danych**: Aby uzyskać więcej informacji, zobacz [Aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania

Gdy należy oszacować liczbę jednostek ru na sekundę do aprowizowania, należy wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu**: W miarę zwiększania rozmiaru elementu zwiększa się również liczbę ru do odczytu lub zapisu elementu

* **Indeksowanie elementów**: Domyślnie każdy element jest automatycznie indeksowane. Mniej jednostek żądania są używane, jeśli nie chcesz indeksować niektórych elementów w kontenerze.

* **Element właściwości liczba**: Przy założeniu, że indeksowanie domyślny znajduje się na wszystkie właściwości, liczbę jednostek zarezerwowanych wykorzystywane do zapisu elementu zwiększa się wraz ze wzrostem liczby właściwości elementu.

* **Właściwości indeksowanych**: Zasady indeks na każdy kontener określa właściwości, które są indeksowane domyślnie. Aby zmniejszyć zużycie RU dla operacji zapisu, należy ograniczyć liczbę tych właściwości indeksowanych.

* **Wyjaśnienie pojęcia spójności danych**: Poziomy spójności silnych i powiązana nieaktualność zużywa jednostki ru w około dwa razy więcej podczas wykonywania operacji odczytu, w porównaniu do innych złagodzone poziomów spójności.

* **Zapytanie wzorców**: Złożoność zapytania ma wpływ na liczbę jednostek żądania są używane dla operacji. Czynniki mające wpływ na koszt operacji zapytania obejmują: 
    
    - Liczba wyników zapytania
    - Liczba predykatów
    - Rodzaj predykaty
    - Liczba funkcji zdefiniowanych przez użytkownika
    - Rozmiar źródła danych
    - Rozmiar zestawu wyników
    - Projekcje

  Usługa Azure Cosmos DB gwarantuje, że tego samego zapytania na tych samych danych zawsze koszty taką samą liczbę jednostek żądań na wielokrotnego wykonania.

* **Skrypt użycia**: Podobnie jak w przypadku zapytań, procedury składowane i wyzwalacze zużywa jednostki RU, zależnie od stopnia złożoności działań, które są wykonywane. Podczas opracowywania aplikacji, sprawdź [nagłówek opłaty żądania](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) aby lepiej zrozumieć RU pojemność zużywa każdej operacji.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat [aprowizowanie przepływności na kontenerach Azure Cosmos i bazy danych](set-throughput.md).
* Dowiedz się więcej o [partycjami logicznymi](partition-data.md).
* Dowiedz się więcej na temat [globalnie Skaluj aprowizowanej przepływności](scaling-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
* Dowiedz się, jak [znaleźć opłat za jednostkę żądania dla operacji](find-request-unit-charge.md).
* Dowiedz się, jak [optymalizacji kosztów aprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md).
* Dowiedz się, jak [zoptymalizować odczyty i Zapisy kosztów w usłudze Azure Cosmos DB](optimize-cost-reads-writes.md).
* Dowiedz się, jak [optymalizacji kosztów zapytania w usłudze Azure Cosmos DB](optimize-cost-queries.md).
