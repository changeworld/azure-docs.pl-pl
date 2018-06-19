---
title: Obsługa języka Gremlin w usłudze Azure Cosmos DB | Microsoft Docs
description: Poznaj język Gremlin ze struktury Apache TinkerPop. Dowiedz się, jakie funkcjonalności i kroki są dostępne w usłudze Azure Cosmos DB
services: cosmos-db
author: LuisBosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: overview
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: c675f37e50f5b8a259048d9a92fcdbe5b947068c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797621"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Obsługa grafu Gremlin w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje język przechodzenia przez graf struktury [Apache TinkerPop](http://tinkerpop.apache.org), [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), stanowiący interfejs API programu Graph do tworzenia jednostek grafu oraz wykonywania operacji zapytań na grafie. Język Gremlin służy do tworzenia jednostek grafu (wierzchołków i krawędzi), modyfikacji właściwości w ramach tych elementów, wykonywania zapytań i przejść oraz usuwania elementów. 

Usługa Azure Cosmos DB oferuje funkcje gotowe do użycia w firmie do baz danych grafu. Obejmują one dystrybucję globalną, niezależne skalowanie magazynu i przepływności, przewidywalne opóźnienia wyrażone w milisekundach za pomocą pojedynczej cyfry, automatyczne indeksowanie, umowy SLA, dostępność odczytu dla kont baz danych obejmujących co najmniej dwa regiony świadczenia usługi Azure. Ze względu na to, że usługa Azure Cosmos DB obsługuje strukturę TinkerPop/język Gremlin, można łatwo migrować aplikacje napisane przy użyciu innej bazy danych grafów bez konieczności wprowadzania zmian w kodzie. Ponadto, ze względu na obsługę języka Gremlin usługa Azure Cosmos DB płynnie integruje się ze strukturami analizy z obsługą TinkerPop, np. [Apache Spark GraphX](http://spark.apache.org/graphx/). 

Ten artykuł zawiera krótki przewodnik na temat języka Gremlin oraz listę funkcji języka Gremlin oraz kroków obsługiwanych przez interfejs API programu Graph.

## <a name="gremlin-by-example"></a>Język Gremlin w przykładach
Przykładowy graf pomoże dowiedzieć się, jak można wyrazić zapytania w języku Gremlin. Na poniższej ilustracji przedstawiono w formie grafu aplikację biznesową, która zarządza danymi o użytkownikach, zainteresowaniach i urządzeniach.  

![Przykładowa baza danych przedstawiająca osoby, urządzenia i zainteresowania](./media/gremlin-support/sample-graph.png) 

Ten graf obejmuje następujące typy wierzchołków (nazywanych w języku Gremlin „label”, czyli etykieta):

- People (Osoby): graf przedstawia trzy osoby, Robina, Thomasa i Bena
- Interests (Zainteresowania): ich zainteresowaniem w tym przykładzie jest football
- Devices (Urządzenia): urządzenia używane przez osoby
- Operating Systems (Systemy operacyjne): systemy operacyjne uruchomione na urządzeniach

Przedstawiamy relacje między tymi jednostkami przy użyciu następujących typów krawędzi/etykiet:

- Knows (Zna): na przykład „Thomas zna Robina”
- Interested (Interesuje się): służy do przedstawienia zainteresowań osób znajdujących się na grafie, na przykład „Ben interesuje się footballem”
- RunsOS (Ma system operacyjny): na laptopie jest uruchomiony system operacyjny Windows
- Uses (Używa): służy do przedstawienia, którego urządzenia używa dana osoba. Na przykład Robin używa telefonu firmy Motorola o numerze seryjnym 77

Możemy uruchomić pewne operacje zgodnie z tym grafem przy użyciu [Konsoli Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Można również wykonywać te operacje przy użyciu sterowników Gremlin na wybranej platformie (Java, Node.js, Python lub .NET).  Zanim zajmiemy się tym, co jest obsługiwane w usłudze Azure Cosmos DB, przyjrzymy się kilku przykładom, aby zapoznać się ze składnią.

Najpierw zajmijmy się CRUD. Następująca instrukcja języka Gremlin wstawia do grafu wierzchołek „Thomas”:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Następnie poniższa instrukcja języka Gremlin wstawia krawędź „knows” między wierzchołkami „Thomas” i „Robin”.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Poniższe zapytanie zwraca wierzchołki „person” w porządku malejącym według ich imion:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Wyjątkowość grafów docenia się, gdy trzeba odpowiedzieć na takie pytania jak: „Jakich systemów operacyjnych używają znajomi Thomasa?” Aby uzyskać te informacje z grafu, można uruchomić proste przejście w języku Gremlin, jak poniżej:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Teraz sprawdźmy, co oferuje usługa Azure Cosmos DB deweloperom języka Gremlin.

## <a name="gremlin-features"></a>Funkcje języka Gremlin
TinkerPop jest standardem, który obejmuje szeroki zakres technologii grafów. Dlatego ma standardową terminologię do opisywania, jakie funkcje są udostępniane przez dostawcę grafu. Usługa Azure Cosmos DB zapewnia trwałą, zapisywalną bazę danych grafów o dużej współbieżności, którą można podzielić na partycje w wielu serwerach lub klastrach. 

W poniższej tabeli wymieniono funkcje struktury TinkerPop wdrażane przez usługę Azure Cosmos DB: 

| Kategoria | Wdrożenie usługi Azure Cosmos DB |  Uwagi | 
| --- | --- | --- |
| Funkcjonalności grafu | Zapewnia funkcjonalności Persistence i ConcurrentAccess. Zaprojektowana obsługa funkcjonalności Transactions | Metody komputera mogą być wdrażane przy użyciu łącznika Spark. |
| Funkcjonalności zmiennych | Obsługuje zmienne Boolean, Byte, Double, Float, Integer, Long, String | Obsługuje typy pierwotne, a zgodność z typami złożonymi jest osiągana za pomocą modelu danych |
| Funkcjonalności wierzchołków | Obsługuje funkcje RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Obsługuje tworzenie, modyfikowanie i usuwanie wierzchołków |
| Funkcjonalności właściwości wierzchołków | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości wierzchołków |
| Funkcjonalności krawędzi | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Obsługuje tworzenie, modyfikowanie i usuwanie krawędzi |
| Funkcjonalności właściwości krawędzi | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości krawędzi |

## <a name="gremlin-wire-format-graphson"></a>Format połączeń Gremlin: GraphSON

Usługa Azure Cosmos DB korzysta z [formatu GraphSON](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) podczas zwracania wyników z operacji Gremlin. GraphSON jest standardowym formatem środowiska Gremlin do przedstawiania wierzchołków, krawędzi i właściwości (jedno- i wielowartościowych) przy użyciu formatu JSON. 

Na przykład poniższy fragment kodu przedstawia widok w formacie GraphSON wierzchołka *zwracanego do klienta* z usługi Azure Cosmos DB. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Właściwości używane przez format GraphSON dla wierzchołków są następujące:

| Właściwość | Opis |
| --- | --- |
| id | Identyfikator wierzchołka. Musi być unikatowy (w połączeniu z wartością _partition w stosownych przypadkach) |
| label | Etykieta wierzchołka. Jest opcjonalna i służy do opisu typu jednostki. |
| type | Służy do odróżnienia wierzchołków od dokumentów bez grafów |
| properties | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z wierzchołkiem. Każda właściwość może mieć wiele wartości. |
| _partition (można konfigurować) | Klucz partycji wierzchołka. Może służyć do skalowania grafów na wielu serwerach |
| outE | Zawiera listę krawędzi wyjściowych z wierzchołka. Przechowywanie informacji sąsiedztwa razem z wierzchołkiem umożliwia szybkie wykonanie przejść. Krawędzie są pogrupowane w oparciu o etykiety. |

Krawędź zawiera następujące informacje, aby pomóc w nawigacji do innych części grafu.

| Właściwość | Opis |
| --- | --- |
| id | Identyfikator krawędzi. Musi być unikatowy (w połączeniu z wartością _partition w stosownych przypadkach) |
| label | Etykieta krawędzi. Ta właściwość jest opcjonalna i służy do opisu typu relacji. |
| inV | Zawiera listę wierzchołków wejściowych dla krawędzi. Przechowywanie informacji sąsiedztwa razem z krawędzią umożliwia szybkie wykonanie przejść. Wierzchołki są pogrupowane w oparciu o etykiety. |
| properties | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z krawędzią. Każda właściwość może mieć wiele wartości. |

Każda właściwość może przechowywać wiele wartości w tablicy. 

| Właściwość | Opis |
| --- | --- |
| wartość | Wartość właściwości

## <a name="gremlin-steps"></a>Kroki w środowisku Gremlin
Teraz przyjrzyjmy się krokom w środowisku Gremlin obsługiwanym przez usługę Azure Cosmos DB. Aby uzyskać pełną dokumentację dotyczącą języka Gremlin, zobacz [odwołanie do struktury TinkerPop](http://tinkerpop.apache.org/docs/current/reference).

| krok | Opis | Dokumentacja dotycząca struktury TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Dodaje krawędź między dwoma wierzchołkami | [krok addE](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Dodaje wierzchołek do grafu | [krok addV](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Gwarantuje, że wszystkie przejścia zwracają wartość | [krok and](http://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Modulator kroku do przypisania zmiennej do wyniku kroku | [krok as](http://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | Modulator kroku używany z elementami `group` i `order` | [krok by](http://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Zwraca pierwsze przejście, które zwraca wynik | [krok coalesce](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Zwraca wartość stałą. Używany z krokiem `coalesce`| [krok constant](http://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Zwraca liczbę z przejścia | [krok count](http://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Zwraca wartości z usuniętymi duplikatami | [krok dedup](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Upuszcza wartości (wierzchołek/krawędź) | [krok drop](http://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | Działa jak bariera, która oblicza agregację wyników| [krok fold](http://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Grupuje wartości w oparciu o określone etykiety| [krok group](http://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Służy do filtrowania właściwości, wierzchołków i krawędzi. Obsługuje warianty `hasLabel`, `hasId`, `hasNot` i `has`. | [krok step](http://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Wstawia wartości do strumienia| [krok inject](http://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Służy do wykonywania filtru przy użyciu wyrażenia logicznego | [krok is](http://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | Pozwala ograniczyć liczbę elementów podczas przechodzenia| [krok limit](http://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Krok local opakowuje sekcję przejścia, podobnie jak podzapytanie | [krok local](http://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Służy do tworzenia negacji filtru | [krok not](http://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | Zwraca wynik określonego przejścia, jeśli wstrzymuje wynik lub zwraca wywołujący element | [krok optional](http://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Gwarantuje, że co najmniej jedno przejście zwróci wartość | [krok or](http://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | Zwraca wyniki w określonej kolejności sortowania | [krok order](http://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Zwraca pełną ścieżkę przejścia | [krok path](http://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Projektuje właściwości jako mapę | [krok project](http://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Zwraca właściwości dla określonych etykiet | [krok properties](http://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | Filtruje do określonego zakresu wartości| [krok range](http://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | Powtarza krok określoną liczbę razy. Używany do zapętlenia | [krok repeat](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Służy do próbkowania wyników z przejścia | [krok sample](http://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Służy do projektowania wyników z przejścia |  [krok select](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Używany do nieblokujących agregacji z przejścia | [krok store](http://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Agreguje ścieżki z wierzchołka do drzewa | [krok tree](http://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Odwija iterator w ramach kroku| [krok unfold](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Scalanie wyników z wielu przejść| [krok union](http://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | Zawiera kroki niezbędne do przejść między wierzchołkami i krawędziami `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, oraz `otherV` do | [kroki vertex](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | Służy do filtrowania wyników z przejścia. Obsługuje operatory `eq`, `neq`, `lt`, `lte`, `gt`, `gte` i `between`  | [krok where](http://tinkerpop.apache.org/docs/current/reference/#where-step) |

Aparat zoptymalizowany pod kątem zapisu oferowany w usłudze Azure Cosmos DB obsługuje domyślnie automatyczne indeksowanie wszystkich właściwości w wierzchołkach i krawędziach. W związku z tym zapytania z filtrami, zapytania zakresu, sortowanie lub agregacje na dowolnej właściwości są przetwarzane z indeksu i skutecznie obsługiwane. Więcej informacji na temat działania indeksowania w usłudze Azure Cosmos DB znajduje się w dokumencie dotyczącym [indeksowania niezależnie od schematu](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Następne kroki
* Rozpocznij tworzenie aplikacji grafu [przy użyciu zestawów SDK firmy Microsoft](create-graph-dotnet.md) 
* Dowiedz się więcej na temat [obsługi grafów](graph-introduction.md) w usłudze Azure Cosmos DB
