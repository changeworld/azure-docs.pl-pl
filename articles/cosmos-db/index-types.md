---
title: Typy indeksu w usłudze Azure Cosmos DB
description: Przegląd typów indeksu w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 5e7ee7c0bdfd0cff6be182e6d087cc264910e440
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271564"
---
# <a name="index-types-in-azure-cosmos-db"></a>Typy indeksu w usłudze Azure Cosmos DB

Istnieje wiele opcji, podczas konfigurowania zasad indeksowania dla ścieżki. Można określić jedną lub więcej definicji indeksowania dla każdej ścieżki:

- **Typ danych:** Ciąg, liczba, punkt, wielokąta lub LineString (może zawierać tylko jeden wpis dla typu danych na każdej ścieżce).

- **Typ indeksu:** Zakres (w przypadku równości, zakresu lub zapytania w klauzuli ORDER BY) lub przestrzenne (dla zapytań przestrzennych).

- **Dokładność:** Dla indeks zakresu maksymalna dozwolona dokładność: ma wartość -1, która jest również wartość domyślna.

## <a name="index-kind"></a>Typ indeksu

Usługa Azure Cosmos DB obsługuje indeks zakresu dla każdej ścieżce, które można skonfigurować ciąg lub liczba typów danych lub obu.

- **Indeks zakresu** obsługuje zapytań o równość wydajne, sprzężenia zapytania, zapytań o zakres (przy użyciu >, <>, =, < =,! =) i zapytania w klauzuli ORDER BY. Klauzula ORDER BY zapytania domyślnie również wymagają precyzja maksymalna wartość indeksu (-1). Typ danych może być ciąg lub liczba.

- **Indeks przestrzenny** obsługuje wydajne przestrzennego (w ramach i odległość) zapytania. Typ danych może być punkt, wielokąta lub LineString. Usługa Azure Cosmos DB obsługuje również rodzaj indeks przestrzenny dla każdej ścieżki, który może być określony dla typów danych punkt, wielokąta lub LineString. Wartość w określonej ścieżce musi być prawidłowy fragment GeoJSON, takich jak {"type": "Point", "coordinates": [0.0, 10.0]}. Usługa Azure Cosmos DB obsługuje automatyczne indeksowanie punkt wielokąta i LineString typów danych.

Oto przykłady zapytań, które zakresu i indeksów przestrzennych może służyć do obsługi:

| **Typ indeksu** | **Opis elementu/użycia** |
| ---------- | ---------------- |
| Zakres      | Zakres za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop > 5<br><br>Wybierz z kolekcji c ORDER BY c.prop<br><br>Zakres za pośrednictwem/właściwości / [] /? (i / lub/właściwości /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz tag z kolekcji c sprzężenia tagu w c.props, gdzie tag = 5  |
| Przestrzenne    | Zakres za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c gdzie ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>Wybierz z kolekcji języka c gdzie ST_WITHIN(c.prop, {"type": "Point",...}) — za pomocą indeksowania w punktach włączone<br><br>Wybierz z kolekcji języka c gdzie ST_WITHIN({"type": "Polygon",...}, c.prop) — przy użyciu wielokątów włączone indeksowanie. |

## <a name="default-behavior-of-index-kinds"></a>Domyślne zachowanie rodzaju indeksu

- Jeśli nie początkowemu indeksowi wszelkie dokładności do sygnalizowania, że skanowanie może być niezbędne do obsługi zapytań, w takim przypadku domyślnie, zwracany jest błąd dla zapytania z zakresu operatorów, takich jak > =.

- Można wykonać zapytania zakresowe bez indeks zakresu przy użyciu **x-ms-bazy danych documentdb — enable skanowania** nagłówka w interfejsie API REST lub **EnableScanInQuery** zażądać opcja przy użyciu zestawu .NET SDK. Jeśli istnieją inne filtry w zapytaniu usługi Azure Cosmos DB można użyć indeksu do filtrowania względem, nie błąd jest zwracany.

- Domyślnie błąd jest zwracany dla zapytań przestrzennych, jeśli nie ma indeksu przestrzennego lub inne filtry, które mogą być udostępniane przez indeks. Takich zapytań może zostać wykonana jako skanowania za pomocą **x-ms-bazy danych documentdb — enable skanowania** lub **EnableScanInQuery**.

## <a name="index-precision"></a>Precyzja indeksu

> [!NOTE]
> Kontenery usługi Azure Cosmos obsługuje nowy układ indeksu, który nie wymaga już dokładności indeksie niestandardowym niż wartość maksymalna dokładność (-1). Przy użyciu tej metody ścieżki zawsze jest indeksowana z maksymalną dokładnością. Jeśli określisz wartość dokładności pola na zasady indeksowania żądań CRUD w kontenerach będzie po cichu ignorować wartość dokładności pola i odpowiedzi z kontenera, tylko zawiera wartość maksymalna dokładność (-1).  Domyślnie wszystkie nowe kontenery Cosmos używają nowego układu indeksu.

- Precyzja indeksu można użyć się zależność między narzut na przechowywanie indeksu i wydajność zapytań. W przypadku liczb zaleca się przy użyciu domyślnej konfiguracji precision-1 (maksymalna). Ponieważ cyfry 8 bajtów w formacie JSON, jest to równoważne do konfiguracji 8 bajtów. Wybieranie niższa wartość dokładności, np. od 1 do 7, oznacza, że wartości w niektórych zakresach mapy do tej samej pozycja indeksu. W związku z tym można zmniejszyć miejsce do magazynowania indeksu, ale wykonanie zapytania może być konieczne przetwarzanie większej liczby elementów. W związku z tym zużywa więcej jednostek przepływności/żądań.

- Precyzja indeks ma praktyczniejsze w aplikacji przy użyciu ciągu zakresów. Ponieważ ciągi mogą być każdej dowolnej długości, wybór dokładności indeks może mieć wpływ na wydajność zapytań o zakres ciągu. To również może wpłynąć na ilość miejsca do magazynowania indeksu, który jest wymagany. Zakres ciąg indeksów można skonfigurować z dokładnością indeks, od 1 do 100 lub wartość -1 (maksimum). Jeśli chcesz wykonywać zapytania w klauzuli ORDER BY, w odniesieniu do właściwości ciągów, należy określić dokładności-1 dla odpowiednich ścieżek.

- Indeksy przestrzenne zawsze używaj domyślna dokładność indeksu dla wszystkich typów (punkt, LineString i wielokąta). Nie można zastąpić domyślną precyzję indeksu dla indeksów przestrzennych.

Usługa Azure Cosmos DB zwraca błąd, gdy zapytanie używa klauzuli ORDER BY, ale nie ma indeksu zakresu na ścieżce kwerendy z maksymalną dokładnością.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat indeksowanie w usłudze Azure Cosmos DB, zobacz następujące artykuły:

- [Omówienie indeksowania](index-overview.md)
- [Zasady indeksowania](indexing-policies.md)
- [Ścieżki indeksów](index-paths.md)

