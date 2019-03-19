---
title: Usługa Azure Cosmos DB zasady indeksowania
description: Dowiedz się, jak działa indeksowanie w usłudze Azure Cosmos DB. Dowiedz się, jak skonfigurować i zmienić zasady indeksowania do automatycznego indeksowania i większą wydajność.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/1/2019
ms.author: mjbrown
ms.openlocfilehash: 0ba5cdd4f92390634d6d2bea8add8309cb1f4d3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014497"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Indeks zasadach w usłudze Azure Cosmos DB

Można zastąpić domyślnych zasad indeksowania w kontenerze usługi Azure Cosmos, konfigurując następujące parametry:

* **Dołącz lub Wyklucz z indeksu elementów i ścieżek**: Można wyłączyć lub włączyć określonych elementów w indeksie, gdy wstawiasz lub zastąpienie elementów w kontenerze. Można również Dołącz lub Wyklucz określone ścieżki/właściwości mają być indeksowane w kontenerach. Ścieżki mogą obejmować wzorców symboli wieloznacznych, na przykład *.

* **Konfiguruj typy indeksu**: Ponadto do zakresu ścieżki zindeksowane, można dodać inne typy indeksów takich jak przestrzennych.

* **Konfigurowanie trybów indeksu**: Za pomocą zasad indeksowania w kontenerze, można skonfigurować różne tryby indeksowania takich jak *spójność* lub *Brak*.

## <a name="indexing-modes"></a>Tryby indeksowania

Usługa Azure Cosmos DB obsługuje dwa tryby indeksowania, które można skonfigurować w kontenerze usługi Azure Cosmos. Można skonfigurować następujące dwa tryby indeksowania za pośrednictwem zasad indeksowania:

* **Spójne**: Jeśli kontener usługi Azure Cosmos zasady są ustawione na spójność, kwerend w określonym kontenerze postępuj zgodnie z tego samego poziomu spójności, jak określony dla operacji odczytu punktu (na przykład silne, powiązana nieaktualność, "session" lub ostateczna). 

  Indeks jest aktualizowana synchronicznie, w przypadku aktualizowania elementów. Na przykład insert, replace, aktualizacji i operacje usuwania elementu spowoduje aktualizacji indeksu. Spójne indeksowania obsługuje spójne zapytania kosztem wpływ na przepływność zapisu. Zmniejszenie przepływność zapisu zależy od "ścieżki dołączona w indeksowaniu" i "poziom spójności". Spójność indeksowania w trybie zaprojektowano pod kątem zapisu szybko i zapytań od razu obciążeń.

* **Brak**: Kontener, który nie ma żadnego indeksu trybie nie ma indeksu skojarzonych z nim. Jest to często używane, jeśli bazy danych Azure Cosmos jest używany jako magazyn kluczy i wartości, a elementy są dostępne tylko dla ich właściwości Identyfikatora.

  > [!NOTE]
  > Konfigurowanie trybu indeksowania jako Brak ma efektem ubocznym porzucenie wszelkie istniejące indeksy. Tej opcji należy używać czy Twoich wzorców dostępu wymagają Identyfikatora buduje łącze własne tylko.

Poziomy spójności zapytania są obsługiwane podobne do regularnych operacji odczytu. Bazy danych Cosmos Azure zwraca błąd, jeśli zapytanie jest kontenerem, który nie ma żadnego indeksowania w trybie. Można wykonać zapytania jako skanowania za pomocą jawnego **x-ms-bazy danych documentdb — enable skanowania** nagłówka w interfejsie API REST lub **EnableScanInQuery** zażądać opcja przy użyciu zestawu .NET SDK. Niektóre zapytania funkcji, jak w klauzuli ORDER BY nie są obecnie obsługiwane za pomocą **EnableScanInQuery**, ponieważ są one wymagane w odpowiedni indeks.

## <a name="modifying-the-indexing-policy"></a>Modyfikowanie zasad indeksowania

W usłudze Azure Cosmos DB można zaktualizować zasad indeksowania kontenera, w dowolnym momencie. Zmiany w indeksowaniu zasad w kontenerze usługi Azure Cosmos może prowadzić do zmian w kształcie indeksu. Ta zmiana ma wpływ na ścieżki, które mogą być indeksowane i ich dokładności modelu spójności samego indeksu. Zmiana zasad indeksowania skutecznie wymaga transformacji starego indeksu do nowego indeksu.

### <a name="index-transformations"></a>Przekształcenia indeksu

Wszystkie przekształcenia indeksu są wykonywane w trybie online. Elementy indeksowane zgodnie z zasadami stare wydajne są przekształcane na nowe zasady bez wywierania wpływu na dostępność zapisu lub przepływnością aprowizowaną do kontenera. Spójność odczytu i zapisu jest operacji wykonywanych przy użyciu interfejsu API REST zestawów SDK, lub z procedur składowanych i wyzwalaczy, w nie wpływu przekształcania indeksu.

Zmiana zasad indeksowania jest operacja asynchroniczna, a czas wymagany do ukończenia operacji zależy od liczby elementów, aprowizowaną przepływność i rozmiar elementów. Gdy indeksowanie jest w toku, zapytanie może nie zwrócić wszystkich zgodnych wyników, jeśli użyć indeksu, która jest modyfikowana zapytań i zapytania nie będzie zwracać wszystkie błędy/błędy. Gdy indeksowanie jest w toku, zapytania są ostatecznie spójne bez względu na konfigurację trybu indeksowania. Po indeksie przekształcenie zostało zakończone, możesz będą nadal widzieć spójne wyniki. Dotyczy to zapytań, wystawiony przez interfejsów, takich jak interfejs API REST, zestawy SDK, lub procedury składowane i wyzwalacze. Przekształcenie indeksu jest wykonywana asynchronicznie w tle repliki przy użyciu wolnym zasoby, które są dostępne dla określonych repliki.

Wszystkie przekształcenia indeksu są wprowadzane w miejscu. Usługa Azure Cosmos DB nie przechowuje dwie kopie indeksu. Więc nie dodatkowe miejsce na dysku jest wymagane lub spożywane na kontenerów, gdy występuje przekształcania indeksu.

Po zmianie zasad indeksowania, zmiany są stosowane do przenieść z indeksem starego do nowego indeksu opierają się głównie na indeksowania konfiguracji trybu. Indeksowanie konfiguracji trybu odtwarzać główną rolę w porównaniu do innych właściwości, takich jak ścieżki uwzględniony/wykluczony, rodzaje indeksu i dokładność.

Jeśli zarówno stare i nowe zasady indeksowania **spójność** indeksowania, bazy danych Azure Cosmos wykonuje przekształcenie indeksu w trybie online. Nie można zastosować inny zmiany zasad indeksowania, z trybem spójne indeksowania w trakcie przekształcenia. Podczas przenoszenia na brak indeksowania w trybie indeksu zostało porzucone od razu. Przenoszenie na brak jest przydatne w przypadku, gdy chcesz anulować przekształcania w toku i zacznij od różnych zasad indeksowania.

Przekształcenie indeks pomyślnie ukończyć, upewnij się, że kontener ma wystarczającą ilością wolnego miejsca. Jeśli kontener osiągnie przydział magazynowania, przekształcania indeksu zostało wstrzymane. Przekształcenie indeksu zostanie automatycznie wznowione, gdy miejsce do magazynowania jest dostępna, na przykład, usunięcie niektórych elementów.

## <a name="modifying-the-indexing-policy---examples"></a>Modyfikowanie zasad indeksowania — przykłady

Poniżej przedstawiono najbardziej typowe przypadki użycia, w którym aktualizować zasady indeksowania:

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
