---
title: Usługa Azure Cosmos DB zasady indeksowania
description: Dowiedz się, jak działa indeksowanie w usłudze Azure Cosmos DB. Dowiedz się, jak skonfigurować i zmienić zasady indeksowania do automatycznego indeksowania i większą wydajność.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278568"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Indeks zasadach w usłudze Azure Cosmos DB

Można zastąpić domyślnych zasad indeksowania w kontenerze usługi Azure Cosmos, konfigurując następujące parametry:

* **Dołącz lub Wyklucz z indeksu elementów i ścieżek**: Można wyłączyć lub włączyć określonych elementów do indeksu, gdy wstawiasz lub zastąpienie elementów w kontenerze. Można również Dołącz lub Wyklucz określone ścieżki/właściwości mają być indeksowane w kontenerach. Ścieżki mogą obejmować wzorców symboli wieloznacznych, na przykład *.

* **Konfiguruj typy indeksu**: Ponadto do zakresu ścieżki zindeksowane, można dodać inne typy indeksów, takich jak przestrzennych.

* **Konfigurowanie trybów indeksu**: Za pomocą zasad indeksowania w kontenerze, można skonfigurować różne tryby indeksowania takich jak *spójność* lub *Brak*.

## <a name="indexing-modes"></a>Tryby indeksowania

Usługa Azure Cosmos DB obsługuje dwa tryby indeksowania, które można skonfigurować w kontenerze usługi Azure Cosmos za pośrednictwem zasad indeksowania:

* **Spójne**: Jeśli ustawiono zasady kontenera usługi Azure Cosmos *spójność*, kwerend w określonym kontenerze postępuj zgodnie z tego samego poziomu spójności, jak określony dla operacji odczytu punktu (na przykład silne, powiązana nieaktualność, "session" lub ostateczna). 

  Indeks jest aktualizowana synchronicznie, w przypadku aktualizowania elementów. Na przykład insert, replace, aktualizacji i operacje usuwania elementu spowoduje aktualizacji indeksu. Spójne indeksowania obsługuje spójne zapytania kosztem wpływ na przepływność zapisu. Zmniejszenie przepływność zapisu jest zależna od "ścieżki uwzględnione w indeksie" i "poziom spójności". Spójne tryb indeksowania została zaprojektowana do aktualnych indeksu wszystkie aktualizacje i natychmiast obsługi zapytań.

* **Brak**: Kontener, który nie ma żadnego indeksu trybie nie ma indeksu skojarzonych z nim. Jest to często używane, jeśli bazy danych Azure Cosmos jest używany jako magazyn kluczy i wartości, a elementy są dostępne tylko dla ich właściwości Identyfikatora.

  > [!NOTE]
  > Konfigurowanie trybu indeksowania, jako *Brak* powoduje porzucenie wszelkie istniejące indeksy po stronie. Tej opcji należy używać czy Twoich wzorców dostępu wymagają Identyfikatora buduje łącze własne tylko.

Poziomy spójności zapytania są obsługiwane podobne do regularnych operacji odczytu. Bazy danych Cosmos Azure zwraca błąd, jeśli zapytanie jest kontenerem, który ma *Brak* tryb indeksowania. Można wykonać zapytania jako skanowania za pomocą jawnego **x-ms-bazy danych documentdb — enable skanowania** nagłówka w interfejsie API REST lub **EnableScanInQuery** zażądać opcja przy użyciu zestawu .NET SDK. Niektóre zapytania funkcji, jak w klauzuli ORDER BY nie są obecnie obsługiwane za pomocą **EnableScanInQuery**, ponieważ są one wymagane w odpowiedni indeks.

## <a name="modifying-the-indexing-policy"></a>Modyfikowanie zasad indeksowania

W usłudze Azure Cosmos DB można zaktualizować zasad indeksowania kontenera, w dowolnym momencie. Zmiana zasad indeksowania w kontenerze usługi Azure Cosmos może prowadzić do zmian w kształcie indeksu. Ta zmiana ma wpływ na ścieżki, które mogą być indeksowane i ich dokładności modelu spójności samego indeksu. Zmiana zasad indeksowania skutecznie wymaga transformacji starego indeksu do nowego indeksu.

### <a name="index-transformations"></a>Przekształcenia indeksu

Wszystkie przekształcenia indeksu są wykonywane w trybie online. Elementy indeksowane zgodnie z zasadami stare wydajne są przekształcane na nowe zasady bez wywierania wpływu na dostępność zapisu lub przepływnością aprowizowaną dla kontenera. Spójność odczytu i zapisu, operacje, które są wykonywane za pomocą interfejsu API REST zestawów SDK, lub przy użyciu procedur składowanych i wyzwalaczy nie występuje podczas przekształcania indeksu.

Zmiana zasad indeksowania jest operacja asynchroniczna, a czas wymagany do ukończenia operacji zależy od liczby elementów, aprowizowanej przepływności i rozmiaru elementów. Podczas ponownego indeksowania, jest w toku, zapytanie może nie zwrócić wszystkich zgodnych wyników, jeśli użyć indeksu, która jest modyfikowana zapytań i zapytania nie będzie zwracać wszystkie błędy/błędy. Podczas ponownego indeksowania, jest w toku, zapytania są ostatecznie spójne bez względu na konfigurację trybu indeksowania. Po indeksie przekształcenie zostało zakończone, możesz będą nadal widzieć spójne wyniki. Dotyczy to zapytań, wystawiony przez interfejsów, takich jak interfejs API REST, zestawy SDK, lub procedury składowane i wyzwalacze. Przekształcenie indeksu jest wykonywana asynchronicznie, w tle repliki przy użyciu wolnym zasoby, które są dostępne dla replik określonych.

Wszystkie przekształcenia indeksu są wprowadzane w miejscu. Usługa Azure Cosmos DB nie przechowuje dwie kopie indeksu. Więc nie dodatkowe miejsce na dysku jest wymagane lub spożywane na kontenerów, gdy występuje przekształcania indeksu.

Po zmianie zasad indeksowania zmiany są stosowane, aby przejść od starego indeksu do nowego indeksu i zależą przede wszystkim indeksowania konfiguracji trybu. Indeksowanie konfiguracji trybu odtwarzać główną rolę w porównaniu do innych właściwości, takich jak ścieżki uwzględniony/wykluczony, rodzaje indeksu i dokładność.

Jeśli zarówno stare i nowe zasady indeksowania **spójność** indeksowania, bazy danych Azure Cosmos wykonuje przekształcenie indeksu w trybie online. Nie można zastosować inny zmiany zasad indeksowania, z trybem spójne indeksowania w trakcie przekształcenia. Podczas przenoszenia na brak indeksowania w trybie indeksu zostało porzucone od razu. Przenoszenie na brak jest przydatne w przypadku, gdy chcesz anulować przekształcania w toku i zacznij od różnych zasad indeksowania.

## <a name="modifying-the-indexing-policy---examples"></a>Modyfikowanie zasad indeksowania — przykłady

Poniżej przedstawiono najbardziej typowe przypadki użycia, jeśli chcesz zaktualizować zasady indeksowania:

* Jeśli chcesz mieć spójne wyniki podczas normalnego działania, ale wracać do **Brak** indeksowania tryb podczas importu danych zbiorczego.

* Jeśli chcesz rozpocząć korzystanie z funkcji indeksowania w bieżącym kontenerów usługi Azure Cosmos. Na przykład można użyć zapytania geoprzestrzenne, które wymaga indeksu przestrzennego rodzaj lub ORDER BY / ciąg kwerendy zakresu, wymagających rodzaj indeks zakresu ciągu.

* Jeśli chcesz ręcznie wybierz właściwości, które mają być indeksowane, a następnie zmianę ich wraz z upływem czasu, aby dopasować się do Twoich obciążeń.

* Jeśli chcesz dostosować indeksowania dokładności, aby poprawić wydajność zapytań, albo w celu zmniejszenia użytego miejsca do magazynowania.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

* [Indeksowanie — omówienie](index-overview.md)
* [Typy indeksów](index-types.md)
* [Ścieżki indeksów](index-paths.md)
* [Jak zarządzać zasad indeksowania](how-to-manage-indexing-policy.md)
