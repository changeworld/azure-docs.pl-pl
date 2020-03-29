---
title: Indeksowanie w usłudze Azure Cosmos DB
description: Dowiedz się, jak działa indeksowanie w usłudze Azure Cosmos DB, różne rodzaje indeksów, takich jak Zakres, Przestrzenne, indeksy złożone obsługiwane.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873628"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indeksowanie w usłudze Azure Cosmos DB — omówienie

Usługa Azure Cosmos DB to niezależna od schematu baza danych, która umożliwia iterację aplikacji bez konieczności zajmowania się zarządzaniem schematem lub indeksem. Domyślnie usługa Azure Cosmos DB automatycznie indeksuje każdą właściwość dla wszystkich elementów w [kontenerze](databases-containers-items.md#azure-cosmos-containers) bez konieczności definiowania dowolnego schematu lub konfigurowania indeksów pomocniczych.

Celem tego artykułu jest wyjaśnienie sposobu indeksowania danych przez usługę Azure Cosmos DB i wykorzystania tych indeksów do podwyższenia wydajności zapytań. Zaleca się, aby przejść przez tę sekcję przed zbadaniem, jak dostosować [zasady indeksowania](index-policy.md).

## <a name="from-items-to-trees"></a>Od przedmiotów do drzew

Za każdym razem, gdy element jest przechowywany w kontenerze, jego zawartość jest rzutowana jako dokument JSON, a następnie konwertowana na reprezentację drzewa. Oznacza to, że każda właściwość tego elementu jest reprezentowana jako węzeł w drzewie. Pseudo węzeł główny jest tworzony jako element nadrzędny do wszystkich właściwości pierwszego poziomu elementu. Węzły liścia zawierają rzeczywiste wartości skalarne przenoszone przez element.

Na przykład należy wziąć pod uwagę ten element:

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

Byłoby reprezentowane przez następujące drzewo:

![Poprzedni element reprezentowany jako drzewo](./media/index-overview/item-as-tree.png)

Należy zauważyć, jak tablice są kodowane w drzewie: każdy wpis w tablicy pobiera węzeł pośredni oznaczony indeksem tego wpisu w tablicy (0, 1 itp.).

## <a name="from-trees-to-property-paths"></a>Od drzew do ścieżek właściwości

Powodem, dla którego usługa Azure Cosmos DB przekształca elementy w drzewa, jest to, że umożliwia właściwości, do których mają być odwoływane przez ich ścieżki w obrębie tych drzew. Aby uzyskać ścieżkę dla właściwości, możemy przechodzić przez drzewo z węzła głównego do tej właściwości i łączyć etykiety każdego węzła przechodzinego.

Oto ścieżki dla każdej właściwości z przykładowego elementu opisanego powyżej:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Po zapisaniu elementu usługa Azure Cosmos DB skutecznie indeksuje ścieżkę każdej właściwości i jej odpowiednią wartość.

## <a name="index-kinds"></a>Rodzaje indeksu

Usługa Azure Cosmos DB obsługuje obecnie trzy rodzaje indeksów.

### <a name="range-index"></a>Indeks zakresu

**Indeks zakresu** jest oparty na uporządkowanej strukturze przypominającej drzewo. Rodzaj indeksu zakresu jest używany dla:

- Zapytania o równość:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Dopasowanie równości w elemencie tablicy
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Zakres zapytań:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (pracuje `>`dla `<` `>=`, `<=` `!=`, , , ),

- Sprawdzanie obecności właściwości:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Dopasowania prefiksu ciągu (słowo kluczowe CONTAINS nie będzie korzystać z indeksu zakresu):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`Kwerendy:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`Kwerendy:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Indeksy zakresu mogą być używane w przypadku wartości skalarnych (ciąg lub liczba).

### <a name="spatial-index"></a>Indeks przestrzenny

**Indeksy przestrzenne** umożliwiają efektywne zapytania dotyczące obiektów geoprzestrzennych, takich jak - punkty, linie, wielokąty i wielobiegi. Te zapytania używają słów kluczowych ST_DISTANCE, ST_WITHIN, ST_INTERSECTS. Oto kilka przykładów, które używają rodzaju indeksu przestrzennego:

- Zapytania dotyczące odległości geoprzestrzennej:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoprzestrzenne w zapytaniach:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Zapytania o przecinanie geograficzne:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Indeksy przestrzenne mogą być używane na poprawnie sformatowanych obiektach [GeoJSON.](geospatial.md) Punkty, LineStrings, Wielokąty i Wielopolony są obecnie obsługiwane.

### <a name="composite-indexes"></a>Indeksy złożone

**Indeksy złożone** zwiększają wydajność podczas wykonywania operacji na wielu polach. Rodzaj indeksu złożonego jest używany do:

- `ORDER BY`zapytania dotyczące wielu właściwości:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Zapytania z filtrem `ORDER BY`i . Te kwerendy mogą korzystać z indeksu złożonego, jeśli właściwość filter jest dodawany do klauzuli. `ORDER BY`

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Kwerendy z filtrem na co najmniej dwie właściwości, w których co najmniej jedna właściwość jest filtrem równości

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Tak długo, jak jeden predykat filtru używa na rodzaju indeksu, aparat zapytań oceni, że najpierw przed skanowaniem reszty. Na przykład, jeśli masz zapytanie SQL, takie jak`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Powyższa kwerenda najpierw filtruje wpisy, w których firstName = "Andrew" przy użyciu indeksu. Następnie przekazuje wszystkie wpisy firstName = "Andrew" za pośrednictwem kolejnego potoku, aby ocenić predykatu filtru CONTAINS.

* Można przyspieszyć kwerendy i uniknąć pełnego skanowania kontenera podczas korzystania z funkcji, które nie używają indeksu (np. zawiera), dodając dodatkowe predykaty filtru, które używają indeksu. Kolejność klauzul filtrowania nie jest ważna. Aparat zapytań jest dowiedzieć się, które predykaty są bardziej selektywne i odpowiednio uruchomić kwerendę.


## <a name="querying-with-indexes"></a>Wykonywanie zapytań z indeksami

Ścieżki wyodrębnione podczas indeksowania danych ułatwiają wyszukiwanie indeksu podczas przetwarzania kwerendy. Dopasowując `WHERE` klauzulę kwerendy z listą ścieżek indeksowanych, można bardzo szybko zidentyfikować elementy, które pasują do predykatu kwerendy.

Rozważmy na przykład następującą kwerendę: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Predykat kwerendy (filtrowanie elementów, gdzie dowolna lokalizacja ma "Francja" jako swój kraj) będzie pasować do ścieżki wyróżnionej na czerwono poniżej:

![Dopasowywanie określonej ścieżki w drzewie](./media/index-overview/matching-path.png)

> [!NOTE]
> Klauzula, `ORDER BY` która zleca jedną właściwość *zawsze* potrzebuje indeksu zakresu i zakończy się niepowodzeniem, jeśli ścieżka, do którego się odwołuje, nie ma. Podobnie kwerendy, `ORDER BY` która zamówienia przez wiele właściwości *zawsze* potrzebuje indeksu złożonego.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania można przeczytać w następujących artykułach:

- [Zasady indeksowania](index-policy.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
