---
title: Klauzula FROM w Azure Cosmos DB
description: Dowiedz się więcej na temat składni SQL i przykład dla klauzuli FROM dla Azure Cosmos DB. W tym artykule przedstawiono również przykłady zakresu wyników i pobieranie elementów podrzędnych przy użyciu klauzuli FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 4f6d7580ea7ff0e8968c0c3ce4b3ca6111c86ac8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873373"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Klauzula FROM w Azure Cosmos DB

Klauzula FROM (`FROM <from_specification>`) jest opcjonalna, chyba że źródło zostanie odfiltrowane lub zaprojektować w dalszej części zapytania. Zapytanie takie jak `SELECT * FROM Families` wylicza cały kontener `Families`. Zamiast używać nazwy kontenera, można także użyć głównego identyfikatora dla kontenera.

Klauzula FROM wymusza następujące reguły dla każdego zapytania:

* Kontener może mieć alias, taki jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. Tutaj `f` jest alias `Families`. JAKO opcjonalne słowo kluczowe [aliasu](sql-query-aliasing.md) identyfikatora.  

* Po usunięciu aliasu oryginalna nazwa źródła nie może być powiązana. Na przykład `SELECT Families.id FROM Families f` jest syntaktycznie nieprawidłowa, ponieważ identyfikator `Families` ma alias i nie można go rozpoznać.  

* Wszystkie właściwości, do których istnieją odwołania, muszą być w pełni kwalifikowane, aby uniknąć niejednoznacznych powiązań w przypadku braku ścisłego przestrzegania schematu. Na przykład `SELECT id FROM Families f` jest syntaktycznie nieprawidłowa, ponieważ właściwość `id` nie jest powiązana.

## <a name="syntax"></a>Składnia
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<from_source>`  
  
  Określa źródło danych, z lub bez aliasu. Jeśli nie określono aliasu, zostanie wywnioskowany z `<container_expression>` za pomocą następujących reguł:  
  
  -  Jeśli wyrażenie ma container_name, container_name będzie używany jako alias.  
  
  -  Jeśli wyrażenie ma `<container_expression>`, property_name, a następnie property_name zostanie użyta jako alias. Jeśli wyrażenie ma container_name, container_name będzie używany jako alias.  
  
- AS `input_alias`  
  
  Określa, że `input_alias` to zbiór wartości zwróconych z podstawowego wyrażenia kontenera.  
 
- `input_alias` INDIE  
  
  Określa, że `input_alias` powinny reprezentować zbiór wartości uzyskane przez Iterowanie wszystkie elementy tablicy każdej macierzy zwracanym przez wyrażenie bazowego kontenera. Każda wartość zwracana przez bazowego wyrażenie kontenera, który nie jest tablicą jest ignorowany.  
  
- `<container_expression>`  
  
  Określa wyrażenie kontenera, który ma być używany do pobierania dokumentów.  
  
- `ROOT`  
  
  Określa, że dany dokument powinny zostać pobrane z domyślnego kontenera aktualnie połączonych.  
  
- `container_name`  
  
  Określa, że w tym dokumencie mają zostać pobrane z podanego kontenera. Nazwa kontenera musi odpowiadać nazwie kontenera połączone obecnie z usługą.  
  
- `input_alias`  
  
  Określa, że w tym dokumencie mają zostać pobrane ze źródła, zdefiniowane przez podany alias.  
  
- `<container_expression> '.' property_`  
  
  Określa dokumentu powinny zostać pobrane, uzyskując dostęp do `property_name` właściwości lub indeks_tablicy elementu tablicy, dla wszystkich dokumentów, pobierane przez określone wyrażenie kontenera.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Określa dokumentu powinny zostać pobrane, uzyskując dostęp do `property_name` właściwości lub indeks_tablicy elementu tablicy, dla wszystkich dokumentów, pobierane przez określone wyrażenie kontenera.  
  
## <a name="remarks"></a>Uwagi
  
Wszystkie aliasy podany lub wnioskowany w `<from_source>(`s) musi być unikatowa. Składnia `<container_expression>.`property_name jest taka sama jak `<container_expression>' ['"property_name"']'`. Jednak te ostatnie składni można użyć, jeśli nazwa właściwości zawiera znak — identyfikator.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Obsługa brakujących elementów tablicy i wartości niezdefiniowanego nie ma właściwości
  
Wyrażenie kontenerów uzyskuje dostęp do właściwości lub elementów tablicy i wartości nie istnieje, ta wartość będą ignorowane i nie przetwarzane dalszych.  
  
### <a name="container-expression-context-scoping"></a>Wyznaczanie zakresu kontekście wyrażenia kontenera  
  
Wyrażenie kontener może być należące do kontenera lub zakresu w dokumencie:  
  
-   Wyrażenie jest kontenerów należące do zakresu, jeśli bazowe źródło wyrażenia kontenera jest albo głównego lub `container_name`. Takie wyrażenie reprezentuje zestaw dokumenty pobierane z kontenera bezpośrednio, a nie jest zależna od przetwarzania innych wyrażeń kontenera.  
  
-   Wyrażenie jest dokument należące do zakresu, jeśli bazowe źródło wyrażenia kontenera jest `input_alias` wprowadzone wcześniej w zapytaniu. Takie wyrażenie reprezentuje zestaw dokumentów uzyskanych przez obliczenie wyrażenia kontenera w zakresie każdego dokumentu należących do zestawu skojarzonych z danym kontenerem aliasem.  Wynikowy zestaw będzie Unii zestawów uzyskany w wyniku obliczenia wyrażenia kontenera dla poszczególnych dokumentów w podstawowym zestawie. 

## <a name="examples"></a>Przykłady

### <a name="get-subitems-by-using-the-from-clause"></a>Pobierz elementy podelementowe za pomocą klauzuli FROM

Klauzula FROM może zmniejszyć źródło do mniejszego podzestawu. Aby wyliczyć tylko poddrzewo dla każdego elementu, jego element główny może stać się źródłem, jak pokazano w następującym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są następujące:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Poprzednie zapytanie używało tablicy jako źródła, ale można również użyć obiektu jako źródła. Zapytanie uwzględnia wszystkie prawidłowe, zdefiniowane wartości JSON w źródle do uwzględnienia w wyniku. Poniższy przykład wyklucza `Families`, które nie mają wartości `address.state`.

```sql
    SELECT *
    FROM Families.address.state
```

Wyniki są następujące:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Klauzula SELECT](sql-query-select.md)
- [Klauzula WHERE](sql-query-where.md)