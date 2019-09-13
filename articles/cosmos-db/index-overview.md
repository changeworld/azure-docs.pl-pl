---
title: Indeksowanie w Azure Cosmos DB
description: Dowiedz się, jak indeksowanie działa w Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 4d961f8635a52a09011543b793ce8a87eaa4ea9e
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914194"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indeksowanie w Azure Cosmos DB — Omówienie

Azure Cosmos DB to baza danych niezależny od schematu, która pozwala na iterację aplikacji bez konieczności rozwiązywania problemów z zarządzaniem schematami i indeksami. Domyślnie Azure Cosmos DB automatycznie indeksuje każdą właściwość dla wszystkich elementów w [kontenerze](databases-containers-items.md#azure-cosmos-containers) bez konieczności definiowania schematu lub konfigurowania indeksów pomocniczych.

Celem tego artykułu jest wyjaśnienie, jak Azure Cosmos DB indeksów danych i jak używa indeksów w celu zwiększenia wydajności zapytań. Zaleca się przechodzenie przez tę sekcję przed rozpoczęciem dostosowywania [zasad indeksowania](index-policy.md).

## <a name="from-items-to-trees"></a>Z elementów do drzew

Za każdym razem, gdy element jest przechowywany w kontenerze, jego zawartość jest rzutowana jako dokument JSON, a następnie konwertowana na reprezentację drzewa. Oznacza to, że każda Właściwość tego elementu jest reprezentowana jako węzeł w drzewie. Węzeł pseudo root jest tworzony jako element nadrzędny dla wszystkich właściwości pierwszego poziomu elementu. Węzły liścia zawierają rzeczywiste wartości skalarne przenoszone przez element.

Rozważmy na przykład ten element:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Powinna być reprezentowana przez następujące drzewo:

![Poprzedni element reprezentowany jako drzewo](./media/index-overview/item-as-tree.png)

Zwróć uwagę, jak tablice są kodowane w drzewie: każdy wpis w tablicy pobiera pośredni węzeł z etykietą z indeksem tego wpisu w tablicy (0, 1 itd.).

## <a name="from-trees-to-property-paths"></a>Z drzew do ścieżek właściwości

Powód, dla którego Azure Cosmos DB przekształcania elementów na drzewa, jest, ponieważ umożliwia odwoływanie się do właściwości przez ich ścieżki w ramach tych drzew. Aby uzyskać ścieżkę do właściwości, można przejść przez drzewo z węzła głównego do tej właściwości i połączyć etykiety każdego przechodzącego węzła.

Poniżej znajdują się ścieżki każdej właściwości z przykładowego elementu opisanego powyżej:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Po zapisaniu elementu Azure Cosmos DB efektywnie indeksuje ścieżkę każdej właściwości i odpowiadającą jej wartość.

## <a name="index-kinds"></a>Rodzaje indeksów

Azure Cosmos DB obecnie obsługuje trzy rodzaje indeksów:

Rodzaj indeksu **zakresu** jest używany dla:

- Zapytania o równość:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

- Zapytania zakresu:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (działa dla `>`, `<`, `>=` `<=`,, )`!=`

- `ORDER BY`wybiera

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`wybiera

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Indeksów zakresu można używać w przypadku wartości skalarnych (String lub Number).

Rodzaj indeksu **przestrzennego** jest używany dla:

- Zapytania dotyczące odległości geograficznej: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoprzestrzenne w ramach zapytań: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

Indeksów przestrzennych można używać w poprawnie sformatowanych obiektach [GEOJSON](geospatial.md) . Punkty, LineStrings, wielokąty i wielowielokąty są obecnie obsługiwane.

Typ indeksu **złożonego** jest używany dla:

- `ORDER BY`zapytania dotyczące wielu właściwości:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Wykonuje zapytania z filtrem `ORDER BY`i. Te zapytania mogą korzystać z indeksu złożonego, jeśli do `ORDER BY` klauzuli zostanie dodana Właściwość Filter.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Wykonuje zapytania z filtrem dla dwóch lub więcej właściwości, w których co najmniej jedna właściwość jest filtrem równości

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

## <a name="querying-with-indexes"></a>Wykonywanie zapytań przy użyciu indeksów

Ścieżki wyodrębnione podczas indeksowania danych ułatwiają wyszukiwanie w indeksie podczas przetwarzania zapytania. Dopasowując `WHERE` klauzulę zapytania z listą ścieżek indeksowanych, można łatwo zidentyfikować elementy, które pasują do predykatu zapytania.

Rozważmy na przykład następujące zapytanie: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Predykat zapytania (filtrowanie dla elementów, gdzie każda lokalizacja ma wartość "Francja", ponieważ jego kraj) będzie odpowiadał ścieżce wyróżnionej w kolorze czerwonym poniżej:

![Dopasowanie określonej ścieżki w drzewie](./media/index-overview/matching-path.png)

> [!NOTE]
> Klauzula, która porządkuje według pojedynczej właściwości, zawsze wymaga indeksu zakresu i zakończy się niepowodzeniem, jeśli ścieżka, do której się odwołuje, nie ma takiej wartości. `ORDER BY` Podobnie, `ORDER BY` zapytanie, które porządkuje wiele właściwości, *zawsze* wymaga indeksu złożonego.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat indeksowania w następujących artykułach:

- [Zasady indeksowania](index-policy.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
