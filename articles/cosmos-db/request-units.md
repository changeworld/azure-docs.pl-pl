---
title: Jednostek żądania i przepływności w usłudze Azure Cosmos DB
description: Dowiedz się więcej o sposobie określania i Szacowanie wymagań jednostki żądań w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: dd9fb81b37627518af1375aa9876e6c509b917c9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033931"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednostki żądań w usłudze Azure Cosmos DB

Za pomocą usługi Azure Cosmos DB płacisz za przepływność możesz aprowizować i magazynu, których możesz używać w systemie godzinowym. Przepływność musi być obsługiwana, aby zapewnić wystarczających zasobów systemu dostępne bazy danych Azure Cosmos przez cały czas spełniają lub przekraczają najbardziej umowę SLA usługi Azure Cosmos DB.

Usługa Azure Cosmos DB obsługuje wiele interfejsów API (SQL, MongoDB, Cassandra, Gremlin i tabeli). Każdy interfejs API ma swój własny zestaw operacji bazy danych, począwszy od prostego punktu odczytuje i zapisuje złożonych zapytań. Każda operacja bazy danych wykorzystuje zasoby systemowe, w zależności od złożoności operacji.  Koszt wszystkie operacje bazy danych jest znormalizować przez usługę Azure Cosmos DB i jest wyrażona jako liczba jednostek żądań (ru). Koszt można odczytać elementu 1 KB jest 1 jednostka żądania (1 RU), a minimalny (RUS), które są wymagane do korzystania z 1 GB miejsca do magazynowania jest 40. Wszystkie inne operacje dotyczące bazy danych są przypisywane podobnie koszt pod względem jednostek RU. Niezależnie od używanego do interakcji z kontenera usługi Azure Cosmos i operacji bazy danych (zapisu, odczytu, zapytanie) interfejsu API koszty są zawsze mierzy (RUS).

Jednostek RU/s można traktować jako walutę przepływności. Jednostek RU/s jest oparte na stawkach waluty, która przenosi zasobów systemowych, takich jak procesor CPU, operacje We/Wy i pamięci, które są wymagane do wykonywania operacji bazy danych, obsługiwane przez usługę Azure Cosmos DB. Na poniższej ilustracji przedstawiono jednostek żądania, używanych przez operacje innej bazy danych:

![Operacje bazy danych zużywają jednostek żądania](./media/request-units/request-units.png)

Aby zarządzać i planowanie pojemności, usługi Azure Cosmos DB zapewnia liczbę jednostek zarezerwowanych dla operacji bazy danych za pośrednictwem określonego zestawu danych deterministyczna. Można śledzić liczbę jednostek ru zużywanych przez żadnych operacji bazy danych, sprawdzając nagłówka odpowiedzi. Po zrozumieniu czynników wpływających na opłat za jednostki żądań i wymagań w zakresie przepływności aplikacji możesz tanio Uruchom aplikację.

Chociaż opłaty są naliczane w systemie godzinowym, możesz aprowizować liczbę jednostek zarezerwowanych dla aplikacji na podstawie na sekundę w przyrostach 100 RU/s. Aby skalować aprowizowanej przepływności dla aplikacji, można zwiększyć lub zmniejszyć liczbę jednostek żądania (w zwiększa lub zmniejsza 100 jednostek RU) w dowolnym momencie, programowo lub za pomocą witryny Azure portal.

Użytkownik może aprowizować przepływność mierzoną w dwóch odrębnych stopniach szczegółowości: 

* **Kontenery**. Aby uzyskać więcej informacji, zobacz [jak aprowizować przepływność na kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)
* **Bazy danych**. Aby uzyskać więcej informacji, zobacz [jak aprowizować przepływność mierzoną w bazie danych Azure Cosmos.](how-to-provision-database-throughput.md)

## <a name="request-unit-considerations"></a>Zagadnienia dotyczące jednostki żądania

Podczas szacowania, liczba jednostek RU/s do aprowizowania, warto wziąć pod uwagę następujące czynniki:

* **Rozmiar elementu** — w miarę zwiększania rozmiaru elementu liczba ru do odczytu lub zapisu elementu również się zwiększa.

* **Indeksowanie elementów** — domyślnie automatycznie indeksowane każdego elementu. Mniej jednostek żądania są używane, jeśli nie chcesz indeksować niektórych elementów w kontenerze.

* **Element właściwości liczba** — zakładając, że domyślnie indeksowanie wszystkich właściwości, liczbę jednostek zarezerwowanych używane do zapisania elementu zwiększa się wraz ze wzrostem liczby właściwości elementu.

* **Właściwości indeksowanych** — zasady indeks na każdy kontener określa właściwości, które są indeksowane domyślnie. Można zmniejszyć użycie jednostek żądania dla operacji zapisu przez ograniczenie liczby właściwości indeksowanych.

* **Wyjaśnienie pojęcia spójności danych** -poziomów spójności silnych i powiązana nieaktualność używanie około 2 X więcej (RUS) podczas wykonywania operacji odczytu, w porównaniu do innych złagodzone poziomów spójności.

* **Zapytanie wzorców** -złożoność zapytania, który wpływa na liczbę jednostek żądania są używane dla operacji. Liczba wyników zapytania, liczba predykatów, rodzaj predykaty, liczba funkcji zdefiniowanych przez użytkownika, rozmiar danych źródłowych, rozmiar wynik zestawu i projekcji mają wpływ na koszt operacje zapytań. Usługa Azure Cosmos DB gwarantuje, że tego samego zapytania na tych samych danych zawsze będzie kosztować taką samą liczbę jednostek żądań na wykonań Powtórz tę procedurę.

* **Skrypt użycia** — za pomocą zapytań, procedury składowane i wyzwalacze zużywać jednostki żądania na podstawie stopnia złożoności wykonywanych operacji. Podczas opracowywania aplikacji, sprawdź, czy nagłówek żądania opłat, aby lepiej zrozumieć, jaka pojemność jednostki żądania zużywa każdej operacji.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [przepływności inicjowania obsługi administracyjnej baz danych i kontenerów usługi Azure Cosmos](set-throughput.md)
* Dowiedz się więcej o [partycjami logicznymi](partition-data.md)
* Dowiedz się więcej o [globalnie skalowania aprowizowanej przepływności.](scaling-throughput.md)
* Dowiedz się, [jak aprowizować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md)
* Dowiedz się, [jak aprowizować przepływność mierzoną w bazie danych Azure Cosmos](how-to-provision-database-throughput.md)
