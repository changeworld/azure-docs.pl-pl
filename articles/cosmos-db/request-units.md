---
title: Jednostek żądania i przepływności w usłudze Azure Cosmos DB
description: Dowiedz się więcej o tym, jak określić i ocenić wymagania jednostek żądania w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 3801c19fbef70bf5d67670c4d9acc950291853e6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990159"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądań w usłudze Azure Cosmos DB

Za pomocą usługi Azure Cosmos DB płacisz za przepływność możesz aprowizować i magazynu, których możesz używać w systemie godzinowym. Przepływność musi być obsługiwana, aby upewnić się, że wystarczających zasobów systemu są dostępne dla bazy danych Azure Cosmos cały czas. Konieczne jest za mało zasobów, które spełniają lub przekraczają najbardziej umowę SLA usługi Azure Cosmos DB.

Usługa Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i tabeli. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Rozwiązania z zakresu operacji od prostych punktu wykonującej Odczyt i zapis złożonych zapytań. Każda operacja bazy danych wykorzystuje zasoby systemowe, zależnie od stopnia złożoności operacji. 

Koszt wszystkie operacje bazy danych jest znormalizować przez usługę Azure Cosmos DB i jest wyrażona w jednostkach żądań (ru). Koszt można odczytać elementu 1 KB jest 1 jednostki żądań (RU). Wszystkie inne operacje dotyczące bazy danych podobnie przypisany jest koszt za pomocą jednostek RU. Niezależnie od tego, którego interfejs API umożliwia interakcję z kontenera usługi Azure Cosmos koszty są zawsze mierzone przez (RUS). Operacja bazy danych zapisu, czy odczytu lub zapytania, koszty są zawsze mierzona w (RUS).

Jednostki zarezerwowane można traktować na sekundę przepływności jako waluta. Jednostek ru na sekundę jest oparte na stawkach walutę. Przenosi jego zasobów systemowych, takich jak procesor CPU, operacje We/Wy i pamięci, które są wymagane do wykonywania operacji bazy danych, obsługiwane przez usługę Azure Cosmos DB. Na poniższej ilustracji przedstawiono ru zużywanych przez operacje innej bazy danych:

![Operacje bazy danych zużywają jednostek żądań](./media/request-units/request-units.png)

Aby zarządzać i planowanie pojemności, usługi Azure Cosmos DB zapewnia liczbę jednostek zarezerwowanych dla operacji bazy danych za pośrednictwem określonego zestawu danych deterministyczna. Sprawdź nagłówka odpowiedzi, aby śledzić liczbę jednostek ru zużywanych przez żadnych operacji bazy danych. Gdy już poznasz czynników wpływających na Naliczanie opłat RU i wymagań w zakresie przepływności aplikacji, możesz uruchomić skutecznie koszt aplikacji.

Opłaty są naliczane godzinowo. Liczba jednostek ru możesz aprowizować dla aplikacji na podstawie na sekundę w przyrostach 100 jednostek ru na sekundę. Aby skalować aprowizowanej przepływności dla aplikacji, można zwiększyć lub zmniejszyć liczbę jednostek żądań w dowolnym momencie. Wprowadź zmiany w zwiększa lub zmniejsza 100 jednostek RU. Można wprowadź zmiany, programowo lub za pomocą witryny Azure portal.

Użytkownik może aprowizować przepływność mierzoną w dwóch odrębnych stopniach szczegółowości: 

* **Kontenery**. Aby uzyskać więcej informacji, zobacz [Aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* **Bazy danych**. Aby uzyskać więcej informacji, zobacz [Aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania

Gdy należy oszacować liczbę jednostek ru na sekundę do aprowizowania, należy wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu**: W miarę zwiększania rozmiaru elementu zwiększa się również liczbę ru do odczytu lub zapisu elementu

* **Indeksowanie elementów**: Domyślnie każdy element jest automatycznie indeksowane. Mniej jednostek żądania są używane, jeśli nie chcesz indeksować niektórych elementów w kontenerze.

* **Element właściwości liczba**: Zakładając, że domyślnie indeksowanie wszystkich właściwości, liczbę jednostek zarezerwowanych wykorzystywane do zapisu elementu zwiększa się wraz ze wzrostem liczby właściwości elementu.

* **Właściwości indeksowanych**: Zasady indeks na każdy kontener określa właściwości, które są indeksowane domyślnie. Aby zmniejszyć zużycie RU dla operacji zapisu, należy ograniczyć liczbę tych właściwości indeksowanych.

* **Wyjaśnienie pojęcia spójności danych**: Poziomy spójności silnych i powiązana nieaktualność zużywa jednostki ru w około dwa razy więcej podczas wykonywania operacji odczytu, w porównaniu do innych złagodzone poziomów spójności.

* **Zapytanie wzorców**: Złożoność zapytania ma wpływ na liczbę jednostek żądania są używane dla operacji. Czynniki mające wpływ na koszt operacji zapytania obejmują: 
    
    - Liczba wyników zapytania.
    - Liczba predykatów.
    - Rodzaj predykaty.
    - Liczba funkcji zdefiniowanych przez użytkownika.
    - Rozmiar źródła danych.
    - Rozmiar zestawu wyników.
    - Projekcji.

  Usługa Azure Cosmos DB gwarantuje, że tego samego zapytania na tych samych danych zawsze koszty taką samą liczbę jednostek żądań na wielokrotnego wykonania.

* **Skrypt użycia**: Podobnie jak w przypadku zapytań, procedury składowane i wyzwalacze zużywa jednostki RU, zależnie od stopnia złożoności działań, które są wykonywane. Podczas opracowywania aplikacji, sprawdź, czy nagłówek żądania opłat, aby lepiej zrozumieć RU pojemność zużywa każdej operacji.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat [aprowizowanie przepływności baz danych i kontenerów usługi Azure Cosmos](set-throughput.md).
* Dowiedz się więcej o [partycjami logicznymi](partition-data.md).
* Dowiedz się więcej na temat [globalnie Skaluj aprowizowanej przepływności](scaling-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
