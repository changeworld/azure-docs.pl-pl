---
title: Obsługa rozwiązania Cosmos Gremlin bazy danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat języka Gremlin z Apache TinkerPop. Dowiedz się, które funkcje i kroki są dostępne w usłudze Azure DB rozwiązania Cosmos
services: cosmos-db
documentationcenter: ''
author: LuisBosquez
manager: kfile
ms.assetid: 6016ccba-0fb9-4218-892e-8f32a1bcc590
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: c3d80fcaa38d0f1d7fa1770879ca9b40642bb796
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Pomoc techniczna platformy Azure wykres Gremlin DB rozwiązania Cosmos
Obsługuje bazę danych systemu Azure rozwiązania Cosmos [Apache Tinkerpop](http://tinkerpop.apache.org) wykresu języka przechodzenie [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), która jest interfejs API programu Graph do tworzenia jednostek wykres i wykonywanie operacji zapytania wykresu. Język Gremlin służy do tworzenia jednostek graph (wierzchołki i krawędzi), zmodyfikuj właściwości w ramach tych jednostek, wykonywania zapytań i traversals i usuwania jednostek. 

Azure DB rozwiązania Cosmos oferuje funkcje gotowe enterprise baz danych wykresu. Obejmuje dystrybucji globalnych, niezależne skalowanie magazynu i przepływności, opóźnienia przewidywalną milisekund jednocyfrowej automatycznego indeksowania umów SLA, przeczytaj dostępności dla bazy danych kont spanning co najmniej dwóch regionach platformy Azure. Ponieważ bazy danych rozwiązania Cosmos Azure obsługuje TinkerPop/Gremlin, możesz łatwo przeprowadzić migrację aplikacji napisanych przy użyciu innej bazy danych wykresu bez wprowadzania zmian w kodzie. Ponadto z obsługi Gremlin bazy danych rozwiązania Cosmos Azure bezproblemowo integruje się z włączony TinkerPop analytics platform takich jak [Apache Spark GraphX](http://spark.apache.org/graphx/). 

W tym artykule firma Microsoft stanowią Przewodnik Szybki Gremlin i wyliczania Gremlin funkcji i kroków, które są obsługiwane przez interfejs API programu Graph.

## <a name="gremlin-by-example"></a>Gremlin przykładzie
Aby zrozumieć, jak zapytania może zostać wyrażona w Gremlin Użyjmy wykres próbki. Na poniższej ilustracji przedstawiono aplikacja biznesowa, która zarządza dane o użytkownikach, udziałów i urządzeń w formie wykresu.  

![Przykładowa baza danych przedstawiający osób, urządzeń i udziałów](./media/gremlin-support/sample-graph.png) 

Ten wykres ma następujące typy wierzchołków (o nazwie "label" w Gremlin):

- Osoby: Wykres ma trzy osób, działanie okrężne blogu Thomasa i Ben
- Zainteresowań: Ich zainteresowaniami, w tym przykładzie gry Football
- Urządzenia: Urządzenia, które osoby użycia
- Systemy operacyjne: Systemy operacyjne uruchamianych na urządzeniach

Firma Microsoft reprezentują relacje między tych jednostek za pośrednictwem następujących typów krawędzi/etykiety:

- Zna: na przykład "blogu Thomasa zna działania okrężnego"
- Planuje: Do reprezentowania do zainteresowań osoby w naszym wykresu, na przykład "Ben jest zainteresowana Football"
- RunsOS: Laptop działa system operacyjny Windows
- Używa: Do reprezentowania urządzenia, które osoba używa. Na przykład działanie okrężne używa telefonu firmie o numerze seryjnym 77

Teraz uruchom kilka operacji względem tego wykresu przy użyciu [konsoli Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Można również wykonywać te operacje, przy użyciu Gremlin sterowników na platformie wybór (Java, Node.js, Python lub .NET).  Zanim przyjrzymy co to jest obsługiwana w usłudze Azure DB rozwiązania Cosmos, Oto kilka przykładów, aby zapoznać się ze składnią.

Pierwszy Przyjrzyjmy się CRUD. Następująca instrukcja Gremlin wstawia wierzchołka "Blogu Thomasa" do wykresu:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Następnie następująca instrukcja Gremlin wstawia krawędź "wie", między blogu Thomasa i działania okrężnego.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Następujące zapytanie zwraca "osoba" wierzchołków w kolejności ich imiona:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

W przypadku wykresów świecisz podczas należy odpowiedzieć na pytania "systemy operacyjne, które korzystają znajomych blogu Thomasa?". Możesz uruchomić to proste przechodzenie Gremlin można pobrać informacji z wykresu:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Teraz Przyjrzyjmy się bazy danych rozwiązania Cosmos Azure udostępnia deweloperom Gremlin.

## <a name="gremlin-features"></a>Funkcje gremlin
TinkerPop jest standard, który obejmuje wiele różnych technologii wykresu. Dlatego ma terminologii do opisywania, jakie funkcje są udostępniane przez dostawcę wykresu. Azure DB rozwiązania Cosmos zapewnia współbieżność trwałe, wysoka, bazy danych wykresu zapisywalny, która może zostać podzielony na partycje w wielu serwerów lub klastrów. 

W poniższej tabeli wymieniono funkcje TinkerPop implementowane za pomocą bazy danych rozwiązania Cosmos Azure: 

| Kategoria | Implementacja rozwiązania Cosmos bazy danych Azure |  Uwagi | 
| --- | --- | --- |
| Funkcje wykresu | Zapewnia trwałość i ConcurrentAccess. Przeznaczony do obsługi transakcji | Można zaimplementować metody komputera za pośrednictwem łącznika Spark. |
| Funkcje zmiennych | Obsługuje wartość logiczną, liczbą całkowitą, Byte, kliknij dwukrotnie, Float, Integer, Long, ciąg | Obsługuje typy pierwotne, jest niezgodny z typami złożonymi za pośrednictwem modelu danych |
| Funkcje wierzchołków | Obsługuje RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Obsługuje tworzenie, modyfikowanie i usuwanie wierzchołków |
| Funkcje właściwości wierzchołków | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości wierzchołków |
| Funkcje krawędzi | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Obsługuje tworzenie, modyfikowanie i usuwanie krawędzi. |
| Funkcje właściwości Edge | Właściwości, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości edge |

## <a name="gremlin-wire-format-graphson"></a>Format przesyłania gremlin: GraphSON

Używa Azure DB rozwiązania Cosmos [GraphSON format](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) podczas zwracania wyników z Gremlin operacji. GraphSON jest Gremlin standardowy format wierzchołków, krawędzi oraz właściwości (jedno- i wielowartościowych właściwości), za pomocą formatu JSON. 

Na przykład poniższy fragment kodu przedstawia GraphSON reprezentację wierzchołek *zwracana do klienta* z bazy danych usługi Azure rozwiązania Cosmos. 

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

Właściwości używanych przez GraphSON dla wierzchołków są następujące:

| Właściwość | Opis |
| --- | --- |
| id | Identyfikator wierzchołka. Muszą być unikatowe (w połączeniu z wartością _partition, jeśli ma to zastosowanie) |
| Etykiety | Etykieta wierzchołka. Jest to opcjonalne i używany do opisu typu jednostki. |
| type | Używany do odróżnienia wierzchołki z systemem innym niż wykres — dokumenty |
| properties | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z wierzchołka. Każda właściwość może mieć wielu wartości. |
| _partition (można konfigurować) | Klucz partycji wierzchołka. Może służyć do skalowania w poziomie wykresów na wielu serwerach |
| outE | Zawiera listę limit krawędzi wierzchołka. Przechowuje informacje sąsiedztwa z wierzchołków umożliwia szybkie wykonywanie traversals. Krawędzi są pogrupowane w oparciu o ich etykiety. |

I krawędzi zawiera następujące informacje, aby pomóc w nawigacji do innych części wykresu.

| Właściwość | Opis |
| --- | --- |
| id | Identyfikator dla granicy. Muszą być unikatowe (w połączeniu z wartością _partition, jeśli ma to zastosowanie) |
| Etykiety | Etykieta krawędzi. Ta właściwość jest opcjonalna i używany do opisu typu relacji. |
| inV | Zawiera listę w wierzchołków Edge. Przechowuje informacje sąsiedztwa z krawędzią umożliwia szybkie wykonywanie traversals. Wierzchołków są pogrupowane w oparciu o ich etykiety. |
| properties | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z krawędzią. Każda właściwość może mieć wielu wartości. |

Każda właściwość może przechowywać wiele wartości w tablicy. 

| Właściwość | Opis |
| --- | --- |
| wartość | Wartość właściwości

## <a name="gremlin-steps"></a>Kroki gremlin
Teraz Przyjrzyjmy się kroki Gremlin obsługiwane przez bazy danych Azure rozwiązania Cosmos. Aby uzyskać pełną dokumentację Gremlin, zobacz [odwołania TinkerPop](http://tinkerpop.apache.org/docs/current/reference).

| Krok | Opis | Dokumentacja TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Dodaje krawędź między dwoma wierzchołków | [krok addE](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Dodaje wierzchołka do wykresu | [krok addV](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Zapewnia, że wszystkie traversals zwracają wartość | [i kroku](http://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Modulator krok można przypisać zmiennej do wyniku kroku | [krok](http://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | Używane z modulator krok `group` i `order` | [krok](http://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Zwraca pierwszy przechodzenie, która zwraca wynik | [połączenie kroku](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Zwraca wartość stałą. Używane z `coalesce`| [krok stałej](http://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Zwraca liczbę z podczas przechodzenia | [Liczba kroku](http://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Zwraca wartości usunięciu duplikatów | [krok deduplikacji](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Porzucania wartości (wierzchołków/krawędź) | [Upuść krok](http://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | Działa jako barierę oblicza agregacji wyników| [fold kroku](http://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Grupy na podstawie etykiet określonej wartości| [krok grupa](http://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Używane do filtrowania właściwości, wierzchołki i krawędzi. Obsługuje `hasLabel`, `hasId`, `hasNot`, i `has` wariantów. | [ma kroku](http://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Wstaw wartości do strumienia| [Wstaw kroku](http://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Używane w celu wykonania filtru za pomocą wyrażenia logicznego | [jest kroku](http://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | Pozwala ograniczyć liczbę elementów w podczas przechodzenia| [krok limit](http://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Lokalny opakowuje sekcję przechodzenie, podobnie jak podzapytania | [krok lokalnego](http://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Użyta do wyprodukowania negacji filtru | [nie kroku](http://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | Zwraca wynik określonej przechodzenie, jeśli jego daje w wyniku przeciwnym wypadku zwraca wartość elementu wywołującego | [krok opcjonalny](http://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Gwarantuje, że co najmniej jeden z traversals zwraca wartość | [lub krok](http://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | Zwraca wyniki w kolejności określonej sortowania | [krok kolejności](http://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Zwraca pełną ścieżkę podczas przechodzenia | [krok ścieżki](http://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Projekty jako Mapowanie właściwości | [krok projektu](http://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Zwraca właściwości dla określonej etykiety | [krok właściwości](http://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | Filtry do wartości z określonego zakresu| [krok zakresu](http://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | Powtarza kroku określoną liczbę razy. Używany do pętli | [Powtórz krok](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Używany do przykładowe wyniki z podczas przechodzenia | [krok próbki](http://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Używany do wyników projektu z podczas przechodzenia |  [Zaznacz krok](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Używane dla nieblokujące wartości zagregowanych z podczas przechodzenia | [krok magazynu](http://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Łączny ścieżek wierzchołków w drzewie | [krok drzewa](http://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Skorzystaj z odwijania krokiem iteratora| [unfold — krok](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Wyniki z wielu traversals scalania| [krok Unii](http://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | Zawiera opis kroków wymaganych do traversals między wierzchołki i krawędzi `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, i `otherV` dla | [kroki wierzchołków](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | Używane do filtrowania wyników z podczas przechodzenia. Obsługuje `eq`, `neq`, `lt`, `lte`, `gt`, `gte`, i `between` operatorów  | [gdy krok](http://tinkerpop.apache.org/docs/current/reference/#where-step) |

Aparat zoptymalizowanych pod kątem zapisu pochodzącymi z bazy danych rozwiązania Cosmos Azure obsługuje automatycznego indeksowania wszystkie właściwości w wierzchołki i krawędzi domyślnie. W związku z tym zapytania z filtrami, zakres kwerendy, sortowanie, lub agregacji w dowolnej właściwości są przetwarzane z indeksem i skutecznie obsługiwane. Aby uzyskać więcej informacji na działa jak indeksowania w usłudze Azure DB rozwiązania Cosmos, zobacz nasze dokument na [indeksowania niezależny od schematu](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Kolejne kroki
* Rozpocząć tworzenie aplikacji wykres [przy użyciu nasze zestawy SDK](create-graph-dotnet.md) 
* Dowiedz się więcej o [pomocy technicznej programu graph](graph-introduction.md) w usłudze Azure DB rozwiązania Cosmos
