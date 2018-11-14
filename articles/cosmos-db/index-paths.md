---
title: Praca ze ścieżkami indeksu w usłudze Azure Cosmos DB
description: Omówienie ścieżek indeksu w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 0515397fb9ab0f05b4c763a2b05e9d986960bd91
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629128"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Ścieżki indeksu w usłudze Azure Cosmos DB

Przy użyciu ścieżki indeksu w usłudze Azure Cosmos DB, istnieje możliwość dołączania lub wykluczania określonej ścieżki z indeksowania. Wybieranie określonych ścieżek oferuje zapisu ulepszoną wydajność i niższe magazyn indeksów dla scenariuszy, w których znasz wzorcami zapytań. Indeks ścieżki rozpoczyna się od katalogu głównego (`/`) operator symboli wieloznacznych i zazwyczaj kończy się znakiem `?` operator symboli wieloznacznych. Ten wzorzec wskazuje, czy istnieje wiele możliwych wartości dla prefiksu. Na przykład, aby obsługiwać zapytanie `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, musi zawierać ścieżkę indeksu dla `/familyName/?` w kontenerze indeks zasadach.

Indeks ścieżki można również użyć `*` operator symbolu wieloznacznego do określania zachowania rekursywnie ścieżki w ramach prefiksu. Na przykład `/payload/*` można wykluczyć wszystkie elementy w ramach właściwości ładunku z indeksowania.

## <a name="common-patterns-for-index-paths"></a>Wspólne wzorce dla ścieżek indeksu

Poniżej przedstawiono typowe wzorce do określania ścieżek indeksu:

| **Ścieżka** | **Opis elementu/użycia** |
| ---------- | ------- |
| /   | Domyślna ścieżka dla kolekcji. Cykliczne oraz będzie miało zastosowanie do drzewa całego dokumentu.|
| / prop /?  | Ścieżka indeksu jest wymagana do obsługi zapytań, podobnie do następującej (w przypadku typów wyznaczania wartości skrótu lub zakresu, odpowiednio):<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop > 5<br><br>Wybierz z kolekcji c ORDER BY c.prop  |
| / prop / *  | Ścieżka indeksu dla wszystkich ścieżek w ramach określonej etykiety. Działa z następujących zapytań<br><br>Wybierz z kolekcji języka c WHERE c.prop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop > 5<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop.nextprop = "value"<br><br>Wybierz z kolekcji c ORDER BY c.prop |
| / Właściwości / [] /?  | Ścieżka indeksu są wymagane do obsługi iteracji, a następnie dołącz zapytania względem tablic wartości skalarnych, takich jak ["a", "b", "c"]:<br><br>Wybierz tag z collection.props w tagu, gdzie tag = "value"<br><br>Wybierz tag z kolekcji c sprzężenia tagu w c.props gdzie tag > 5  |
| /subprop/ [] /props/? | Ścieżka indeksu wymagane do obsługi iteracji i zapytań sprzężenia dla tablic obiektów, takich jak [{subprop: ""}, {subprop: "b"}]:<br><br>Wybierz tag z tagu w collection.props tag.subprop gdzie = "value"<br><br>Wybierz tag z kolekcji c sprzężenia tagu w c.props tag.subprop gdzie = "value" |
| / prop/subprop /? | Ścieżka indeksu jest wymagana do obsługi zapytań (w przypadku typów wyznaczania wartości skrótu lub zakresu, odpowiednio):<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop = "value"<br><br>Wybierz z kolekcji języka c WHERE c.prop.subprop > 5  |

Podczas ustawiania ścieżek indeksie niestandardowym, musisz określić domyślną regułę indeksowania cały element, wskazywane przez ścieżkę specjalne `/*`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat indeksowanie w usłudze Azure Cosmos DB w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Zasady indeksowania w usłudze Azure Cosmos DB](indexing-policies.md)
- [Typy indeksu w usłudze Azure Cosmos DB](index-types.md)
