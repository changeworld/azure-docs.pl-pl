---
title: Indeksowanie w usłudze Azure Cosmos DB
description: Dowiedz się, jak działa indeksowanie w usłudze Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 633d0f619132ee93951cfe0dc329a7514a38ef57
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240736"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indeksowanie w usłudze Azure Cosmos DB — omówienie

Usługa Azure Cosmos DB jest od schematów baza danych, która umożliwia powtarzanie czynności w aplikacji bez konieczności zarządzania schematami lub indeksami. Domyślnie usługa Azure Cosmos DB automatycznie indeksuje dla każdej właściwości dla wszystkich elementów w swojej [kontenera](databases-containers-items.md#azure-cosmos-containers) bez konieczności zdefiniować żadnego schematu lub indeksów pomocniczych skonfigurować.

Celem tego artykułu jest wyjaśnienie, jak usługa Azure Cosmos DB indeksuje dane, i sposobem użycia indeksów aby poprawić wydajność zapytań. Zaleca się poświęcić na przejście w tej sekcji, przed rozpoczęciem pracy z sposobu dostosowywania [zasady indeksowania](index-policy.md).

## <a name="from-items-to-trees"></a>Z elementów do drzewa

Za każdym razem, gdy element jest przechowywany w kontenerze, jego zawartość jest przekazywany jako dokument JSON, a następnie konwertowana do reprezentacji drzewa. Oznacza to, że dla każdej właściwości ten element pobiera reprezentowane jako węzeł w drzewie. Węzeł główny pseudo jest tworzony jako element nadrzędny do wszystkich właściwości pierwszego poziomu elementu. Węzły liści zawierają rzeczywiste wartości skalarnych przez element.

Na przykład należy wziąć pod uwagę ten element:

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

Jest przedstawiany przez następujące drzewo:

![Poprzedni element reprezentowany jako drzewo](./media/index-overview/item-as-tree.png)

Należy zauważyć, jak tablice są kodowane w drzewie: każdy wpis w tablicy pobiera węzeł pośredni etykietą indeks ten wpis w tablicy (0, 1 itp.).

## <a name="from-trees-to-property-paths"></a>Z drzewa do ścieżki właściwości

Powód, dlaczego usługi Azure Cosmos DB przy użyciu elementów do drzewa jest, ponieważ umożliwia właściwości, aby odwoływać się do ścieżki wewnątrz tych drzew. Aby uzyskać ścieżkę dla właściwości, firma Microsoft przechodzenie drzewa z węzła głównego do tej właściwości, a złącz etykiety dla każdego węzła korzystania.

Poniżej przedstawiono ścieżki dla każdej właściwości z elementu przykład opisane powyżej:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Przy zapisywaniu element usługi Azure Cosmos DB indeksuje skutecznie ścieżki dla każdej właściwości i jego wartość.

## <a name="index-kinds"></a>Rodzaje indeksu

Usługa Azure Cosmos DB obsługuje dwa rodzaje indeksów:

**Zakres** rodzaj indeks służy do:

- równość zapytania: 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- Zakres kwerendy: 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (działa w przypadku `>`, `<`, `>=`, `<=`, `!=`)

- `ORDER BY` zapytania:

   ```sql SELECT * FROM container c ORDER BY c.property```

- `JOIN` zapytania: 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

Zakres indeksów może służyć w wartości skalarnych (ciąg lub liczba).

**Przestrzenne** rodzaj indeks służy do:

- dane geograficzne odległość zapytania: 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- dane geograficzne w ramach zapytania: 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

Indeksy przestrzenne mogą być używane na format [GeoJSON](geospatial.md) obiektów. LineStrings punktów i wielokątów są obecnie obsługiwane.

**Złożonego** rodzaj indeks służy do:

- `ORDER BY` zapytania na wiele właściwości atrybutu: 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>Wykonywanie zapytania dla indeksów

Ścieżki wyodrębnione podczas indeksowania danych ułatwiają do wyszukiwania indeksu podczas przetwarzania zapytania. Porównując `WHERE` klauzuli kwerendy za pomocą listy ścieżek indeksowane, jest możliwe do identyfikacji elementów, które odpowiadają predykacie zapytania w bardzo szybko.

Na przykład, należy wziąć pod uwagę następujące zapytanie: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Predykacie zapytania (filtrowanie elementów, gdzie dowolnego miejsca i ma "Francja", jako jego kraju) będzie odpowiadać ścieżki wyróżniony na czerwono poniżej:

![Dopasowania określonej ścieżki w obrębie drzewa](./media/index-overview/matching-path.png)

> [!NOTE]
> `ORDER BY` Klauzula, która porządkuje według jedną właściwość *zawsze* musi zakres indeksu i zakończy się niepowodzeniem, jeśli nie ma ścieżki odwołuje się. Podobnie wielu `ORDER BY` zapytania *zawsze* musi indeksie złożonym.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat indeksowania w następujących artykułach:

- [Zasady indeksowania](index-policy.md)
- [Jak zarządzać zasad indeksowania](how-to-manage-indexing-policy.md)
