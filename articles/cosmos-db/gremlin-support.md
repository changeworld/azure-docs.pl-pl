---
title: Obsługa języka Gremlin w usłudze Azure Cosmos DB
description: Poznaj język Gremlin ze struktury Apache TinkerPop. Dowiedz się, jakie funkcjonalności i kroki są dostępne w usłudze Azure Cosmos DB
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 05/21/2019
ms.author: lbosq
ms.openlocfilehash: b36c041c24a07f89701e78aea4d08270342b8d22
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978932"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Obsługa grafu Gremlin w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje [Apache Tinkerpop](https://tinkerpop.apache.org) wykresu języka przechodzenia, znane jako [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Język Gremlin służy do tworzenia jednostek grafu (wierzchołków i krawędzi), modyfikacji właściwości w ramach tych elementów, wykonywania zapytań i przejść oraz usuwania elementów. 

Usługa Azure Cosmos DB oferuje funkcje gotowe do użycia w firmie do baz danych grafu. Te funkcje obejmują globalną dystrybucję i niezależne skalowanie magazynu i przepływności, przewidywalnych milisekundowe opóźnienia, automatycznego indeksowania umowy SLA, także dostępność do odczytu dla konta bazy danych obejmujące co najmniej dwóch regionach platformy Azure. Ponieważ usługi Azure Cosmos DB obsługuje TinkerPop/Gremlin, możesz z łatwością migrować aplikacje napisane przy użyciu innej bazy danych grafów zgodne. Ponadto, ze względu na obsługę języka Gremlin usługa Azure Cosmos DB płynnie integruje się ze strukturami analizy z obsługą TinkerPop, np. [Apache Spark GraphX](https://spark.apache.org/graphx/). 

W tym artykule oferujemy szybki przewodnik Gremlin i wyliczyć funkcji języka Gremlin, które są obsługiwane przez interfejs API Gremlin.

## <a name="gremlin-by-example"></a>Język Gremlin w przykładach
Przykładowy graf pomoże dowiedzieć się, jak można wyrazić zapytania w języku Gremlin. Na poniższej ilustracji przedstawiono w formie grafu aplikację biznesową, która zarządza danymi o użytkownikach, zainteresowaniach i urządzeniach.  

![Przykładowa baza danych przedstawiająca osoby, urządzenia i zainteresowania](./media/gremlin-support/sample-graph.png) 

Ten graf obejmuje następujące typy wierzchołków (nazywanych w języku Gremlin „label”, czyli etykieta):

- People (Osoby): graf przedstawia trzy osoby, Robina, Thomasa i Bena
- Interests (Zainteresowania): ich zainteresowaniem w tym przykładzie jest piłka nożna (football)
- Devices (Urządzenia): urządzenia używane przez osoby
- Operating Systems (Systemy operacyjne): systemy operacyjne uruchomione na urządzeniach

Przedstawiamy relacje między tymi jednostkami przy użyciu następujących typów krawędzi/etykiet:

- Knows (Zna): na przykład „Thomas zna Robina”
- Interested (Interesuje się): służy do przedstawienia zainteresowań osób znajdujących się na grafie, na przykład „Ben interesuje się piłką nożną (football)”
- RunsOS (Ma system operacyjny): na laptopie jest uruchomiony system operacyjny Windows
- Uses (Używa): służy do przedstawienia, którego urządzenia używa dana osoba. Na przykład Robin używa telefonu firmy Motorola o numerze seryjnym 77

Możemy uruchomić pewne operacje zgodnie z tym grafem przy użyciu [Konsoli Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console). Można również wykonywać te operacje przy użyciu sterowników Gremlin na wybranej platformie (Java, Node.js, Python lub .NET).  Zanim zajmiemy się tym, co jest obsługiwane w usłudze Azure Cosmos DB, przyjrzymy się kilku przykładom, aby zapoznać się ze składnią.

Najpierw zajmijmy się CRUD. Następująca instrukcja języka Gremlin wstawia do grafu wierzchołek „Thomas”:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Następnie poniższa instrukcja języka Gremlin wstawia krawędź „knows” między wierzchołkami „Thomas” i „Robin”.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Poniższe zapytanie zwraca wierzchołki „person” w porządku malejącym według ich imion:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Wyjątkowość grafów docenia się, gdy trzeba odpowiedzieć na takie pytania jak: „Jakich systemów operacyjnych używają znajomi Thomasa?” Można uruchomić tego przechodzenie Gremlin, aby uzyskać te informacje z wykresu:

```java
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

Usługa Azure Cosmos DB korzysta z [formatu GraphSON](https://tinkerpop.apache.org/docs/3.3.2/reference/#graphson-reader-writer) podczas zwracania wyników z operacji Gremlin. GraphSON jest standardowym formatem środowiska Gremlin do przedstawiania wierzchołków, krawędzi i właściwości (jedno- i wielowartościowych) przy użyciu formatu JSON. 

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

Poniżej opisano właściwości używane w formacie GraphSON dla wierzchołków:

| Właściwość | Opis | 
| --- | --- | --- |
| `id` | Identyfikator wierzchołka. Muszą być unikatowe (w połączeniu z wartością `_partition` jeśli ma to zastosowanie). Jeśli wartość nie zostanie podany, będą go automatycznie wprowadzane z identyfikatorem GUID | 
| `label` | Etykieta wierzchołka. Służy do opisywania typu jednostki. |
| `type` | Służy do odróżnienia wierzchołków od dokumentów bez grafów |
| `properties` | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z wierzchołkiem. Każda właściwość może mieć wiele wartości. |
| `_partition` | Klucz partycji wierzchołka. Używany do [partycjonowanie grafu](graph-partitioning.md). |
| `outE` | Ta właściwość zawiera listę się krawędzie z wierzchołka. Przechowywanie informacji sąsiedztwa razem z wierzchołkiem umożliwia szybkie wykonanie przejść. Krawędzie są pogrupowane w oparciu o etykiety. |

Krawędź zawiera następujące informacje, aby pomóc w nawigacji do innych części grafu.

| Właściwość | Opis |
| --- | --- |
| `id` | Identyfikator krawędzi. Muszą być unikatowe (w połączeniu z wartością `_partition` jeśli ma to zastosowanie) |
| `label` | Etykieta krawędzi. Ta właściwość jest opcjonalna i służy do opisu typu relacji. |
| `inV` | Ta właściwość zawiera liście wierzchołków krawędź. Przechowywanie informacji sąsiedztwa razem z krawędzią umożliwia szybkie wykonanie przejść. Wierzchołki są pogrupowane w oparciu o etykiety. |
| `properties` | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z krawędzią. Każda właściwość może mieć wiele wartości. |

Każda właściwość może przechowywać wiele wartości w tablicy. 

| Właściwość | Opis |
| --- | --- |
| `value` | Wartość właściwości

## <a name="gremlin-steps"></a>Kroki w środowisku Gremlin
Teraz przyjrzyjmy się krokom w środowisku Gremlin obsługiwanym przez usługę Azure Cosmos DB. Aby uzyskać pełną dokumentację dotyczącą języka Gremlin, zobacz [odwołanie do struktury TinkerPop](https://tinkerpop.apache.org/docs/3.3.2/reference).

| krok | Opis | Dokumentacja dotycząca struktury TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Dodaje krawędź między dwoma wierzchołkami | [krok addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Dodaje wierzchołek do grafu | [krok addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Gwarantuje, że wszystkie przejścia zwracają wartość | [krok and](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Modulator kroku do przypisania zmiennej do wyniku kroku | [krok as](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Modulator kroku używany z elementami `group` i `order` | [krok by](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Zwraca pierwsze przejście, które zwraca wynik | [krok coalesce](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Zwraca wartość stałą. Używany z krokiem `coalesce`| [krok constant](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Zwraca liczbę z przejścia | [krok count](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Zwraca wartości z usuniętymi duplikatami | [krok dedup](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Upuszcza wartości (wierzchołek/krawędź) | [krok drop](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Tworzy opis wszystkie operacje wygenerowane przez krok wykonywany języka Gremlin | [krok executionProfile](graph-execution-profile.md) |
| `fold` | Działa jak bariera, która oblicza agregację wyników| [krok fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Grupuje wartości w oparciu o określone etykiety| [krok group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Służy do filtrowania właściwości, wierzchołków i krawędzi. Obsługuje warianty `hasLabel`, `hasId`, `hasNot` i `has`. | [krok step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Wstawia wartości do strumienia| [krok inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Służy do wykonywania filtru przy użyciu wyrażenia logicznego | [krok is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Pozwala ograniczyć liczbę elementów podczas przechodzenia| [krok limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Krok local opakowuje sekcję przejścia, podobnie jak podzapytanie | [krok local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Służy do tworzenia negacji filtru | [krok not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Zwraca wynik określonego przejścia, jeśli wstrzymuje wynik lub zwraca wywołujący element | [krok optional](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Gwarantuje, że co najmniej jedno przejście zwróci wartość | [krok or](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Zwraca wyniki w określonej kolejności sortowania | [krok order](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Zwraca pełną ścieżkę przejścia | [krok path](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projektuje właściwości jako mapę | [krok project](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Zwraca właściwości dla określonych etykiet | [krok properties](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtruje do określonego zakresu wartości| [krok range](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Powtarza krok określoną liczbę razy. Używany do zapętlenia | [krok repeat](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Służy do próbkowania wyników z przejścia | [krok sample](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Służy do projektowania wyników z przejścia |  [krok select](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Używany do nieblokujących agregacji z przejścia | [krok store](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Ciąg funkcji filtrowania. Ta funkcja jest używana jako predykat dla `has()` krok do dopasowania właściwości do początku ciągu | [Predykaty TextP](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Ciąg funkcji filtrowania. Ta funkcja jest używana jako predykat dla `has()` krok do dopasowania właściwości z zakończeniem podany ciąg | [Predykaty TextP](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Ciąg funkcji filtrowania. Ta funkcja jest używana jako predykat dla `has()` krok do dopasowania właściwości przy użyciu zawartości ciągu | [Predykaty TextP](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Ciąg funkcji filtrowania. Ta funkcja jest używana jako predykat dla `has()` krok do dopasowania właściwości, która nie zaczyna się od ciągu | [Predykaty TextP](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Ciąg funkcji filtrowania. Ta funkcja jest używana jako predykat dla `has()` krok do dopasowania właściwości, które nie kończy się podanym ciągiem | [Predykaty TextP](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Ciąg funkcji filtrowania. Ta funkcja jest używana jako predykat dla `has()` krok do dopasowania właściwości, która nie zawiera ciągu | [Predykaty TextP](http://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agreguje ścieżki z wierzchołka do drzewa | [krok tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Odwija iterator w ramach kroku| [krok unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Scalanie wyników z wielu przejść| [krok union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Zawiera kroki niezbędne do przejść między wierzchołkami i krawędziami `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, oraz `otherV` do | [kroki vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Służy do filtrowania wyników z przejścia. Obsługuje operatory `eq`, `neq`, `lt`, `lte`, `gt`, `gte` i `between`  | [krok where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Aparat zoptymalizowany pod kątem zapisu oferowany w usłudze Azure Cosmos DB obsługuje domyślnie automatyczne indeksowanie wszystkich właściwości w wierzchołkach i krawędziach. W związku z tym zapytania z filtrami, zapytania zakresu, sortowanie lub agregacje na dowolnej właściwości są przetwarzane z indeksu i skutecznie obsługiwane. Więcej informacji na temat działania indeksowania w usłudze Azure Cosmos DB znajduje się w dokumencie dotyczącym [indeksowania niezależnie od schematu](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Następne kroki
* Rozpocznij tworzenie aplikacji grafu [przy użyciu zestawów SDK firmy Microsoft](create-graph-dotnet.md) 
* Dowiedz się więcej na temat [obsługi grafów](graph-introduction.md) w usłudze Azure Cosmos DB
