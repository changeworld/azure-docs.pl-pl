---
title: Typy indeksu w usłudze Azure Cosmos DB
description: Przegląd typów indeksu w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: f3c5d7bc1907e94ff2e590fe77cc531ac4b01f4c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629081"
---
# <a name="index-types-in-azure-cosmos-db"></a>Typy indeksu w usłudze Azure Cosmos DB

Istnieje wiele opcji gdzie konfigurowania zasad indeksowania dla ścieżki. Można określić jedną lub więcej definicji indeksowania dla każdej ścieżki:

- **Typ danych:** ciąg, liczba, punkt, wielokąta lub LineString (może zawierać tylko jeden wpis dla typu danych na każdej ścieżce).

- **Indeks rodzaj:** wyznaczania wartości skrótu (zapytań o równość), zakresu (równości, zakresu lub zapytania w klauzuli ORDER BY) lub przestrzenne (zapytań przestrzennych).

- **Dokładność:** dla indeksu skrótu to różni się od 1 do 8 na ciągi i liczby, a wartość domyślna to 3. Wartość maksymalna dozwolona dokładność: indeks zakresu jest wartość -1. Może się różnić od 1 do 100 (maksymalna dozwolona dokładność) dla parametrów lub wartości liczbowe.

## <a name="index-kind"></a>Typ indeksu

Usługa Azure Cosmos DB obsługuje indeks zakresu i wyznaczania wartości skrótu dla każdej ścieżce, które można skonfigurować ciąg lub liczba typów danych lub obu.

- **Indeks skrótów** obsługuje wydajne równości i łączenia zapytań. W większości przypadków użycia większą precyzję niż domyślna wartość 3 bajtów nie ma potrzeby indeksów skrótu. Typ danych może być ciąg lub liczba.

- **Indeks zakresu** obsługuje zapytań o równość wydajne, zapytań o zakres (przy użyciu >, <>, =, < =,! =) i zapytania w klauzuli ORDER BY. Zapytania w klauzuli ORDER By, domyślnie wymagają także precyzja maksymalna wartość indeksu (-1). Typ danych może być ciąg lub liczba.

- **Indeks przestrzenny** obsługuje wydajne przestrzennego (w ramach i odległość) zapytania. Typ danych może być punkt, wielokąta lub LineString. Usługa Azure Cosmos DB obsługuje również rodzaj indeks przestrzenny dla każdej ścieżki, który może być określony dla typów danych punkt, wielokąta lub LineString. Wartość w określonej ścieżce musi być prawidłowy fragment GeoJSON, takich jak {"type": "Point", "coordinates": [0.0, 10.0]}. Usługa Azure Cosmos DB obsługuje automatyczne indeksowanie punkt wielokąta i LineString typów danych.

Poniżej przedstawiono przykłady kwerend, które wyznaczania wartości skrótu, zakres, i Indeksy przestrzenne może służyć do obsługi:

| **Typ indeksu** | **Opis elementu/użycia** |
| ---------- | ---------------- |
| Skrót  | Wyznaczania wartości skrótu, za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Skrót/właściwości / [] /? (i / lub/właściwości /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz tag z kolekcji c sprzężenia tagu w c.props, gdzie tag = 5  |
| Zakres  | Zakres za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop > 5<br><br>Wybierz z kolekcji c ORDER BY c.prop   |
| Przestrzenne     | Zakres za pośrednictwem/prop /? (lub /) może służyć do efektywnie obsługiwać następujące zapytania:<br><br>Wybierz z kolekcji języka c<br><br>GDZIE ST_DISTANCE (c.prop, {"type": "Punkt", "coordinates": [0.0, 10.0]}) < 40<br><br>Wybierz z kolekcji c gdzie ST_WITHIN(c.prop, {"type": "Polygon",...}) — za pomocą indeksowania w punktach włączone<br><br>Wybierz z kolekcji c gdzie ST_WITHIN({"type": "Point",...}, c.prop) — za pomocą wielokątów włączone indeksowanie.     |

## <a name="default-behavior-of-index-kinds"></a>Domyślne zachowanie rodzaju indeksu

- Jeśli nie początkowemu indeksowi wszelkie dokładności do sygnalizowania, że skanowanie może być niezbędne do obsługi zapytań, w takim przypadku domyślnie, zwracany jest błąd dla zapytania z zakresu operatorów, takich jak > =.

- Zapytania zakresowe można wykonać bez indeks zakresu przy użyciu nagłówek "x-ms-bazy danych documentdb — Włączanie — skanowania" w interfejsie API REST lub opcja "EnableScanInQuery" przy użyciu zestawu .NET SDK. Jeśli istnieją inne filtry w zapytaniu usługi Azure Cosmos DB można użyć indeksu do filtrowania względem, nie błąd jest zwracany.

- Domyślnie błąd jest zwracany dla zapytań przestrzennych, jeśli nie ma indeksu przestrzennego lub inne filtry, które mogą być udostępniane przez indeks. Takich zapytań może zostać wykonana jako skanowania, używając x-ms-bazy danych documentdb — enable skanowania lub EnableScanInQuery.

## <a name="index-precision"></a>Precyzja indeksu

- Zapewnienie kompromis między magazyn indeksów obciążenie i wydajność zapytań, można użyć indeksu dokładności. W przypadku liczb zaleca się przy użyciu domyślnej konfiguracji precision-1 (maksimum). Ponieważ cyfry 8 bajtów w formacie JSON, jest to równoważne do konfiguracji 8 bajtów. Wybieranie niższa wartość dokładności, np. od 1 do 7, oznacza, że wartości w niektórych zakresach mapy do tej samej pozycja indeksu. W związku z tym można zmniejszyć miejsce do magazynowania indeksu, ale wykonanie zapytania może być konieczne przetwarzanie większej liczby elementów. W związku z tym zużywa więcej jednostek przepływności/żądań.

- Precyzja indeks ma praktyczniejsze w aplikacji przy użyciu ciągu zakresów. Ponieważ ciągi mogą być każdej dowolnej długości, wybór dokładności indeks może mieć wpływ na wydajność zapytań o zakres ciągu. To również może wpłynąć na ilość miejsca do magazynowania indeksów, które są wymagane. Zakres ciąg indeksów można skonfigurować z dokładnością indeks, od 1 do 100 lub wartość -1 (maksimum). Jeśli chcesz wykonywać zapytania w klauzuli ORDER BY, w odniesieniu do właściwości ciągów, należy określić dokładności-1 dla odpowiednich ścieżek.

- Indeksy przestrzenne zawsze używaj domyślna dokładność indeksu dla wszystkich typów (punkt, LineString i wielokąta). Nie można zastąpić domyślną precyzję indeksu dla indeksów przestrzennych.

Usługa Azure Cosmos DB zwraca błąd, gdy zapytanie używa klauzuli ORDER BY, ale nie ma indeksu zakresu na ścieżce kwerendy z maksymalną dokładnością.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat indeksowanie w usłudze Azure Cosmos DB, zobacz następujące artykuły:

- [Omówienie indeksowania](index-overview.md)
- [Zasady indeksowania](indexing-policies.md)
- [Indeks ścieżki](index-paths.md)

