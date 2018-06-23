---
title: Składnia poleceń wyszukiwania lambda w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Więcej informacji na temat składni wyszukiwania Lambda używanego w Academic Knowledge API w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f486368e1d0258087091acb846a84b125712db40
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346937"
---
# <a name="lambda-search-syntax"></a>Składnia poleceń wyszukiwania lambda

Każdy *lambda* zapytania wyszukiwania ciąg opisuje wzorca wykresu. Zapytanie musi mieć co najmniej jeden węzeł początkowy, określając, z których węzła wykresu Rozpoczniemy podczas przechodzenia. Aby określić węzeł początkowy, należy wywołać *MAGNETYCZNY. StartFrom()* — metoda i przekaż identyfikatory jeden lub więcej węzłów lub kwerendy obiekt, który określa ograniczenia wyszukiwania. *StartFrom()* metoda ma trzech przeciążeń. Wszystkie z nich podjąć dwa argumenty z drugim jest opcjonalne. Pierwszy argument może być długich liczb całkowitych wyliczalny zbiór długich liczb całkowitych, lub obiekt ciąg reprezentujący JSON, z tej samej semantyki jako w *json* wyszukiwania:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Proces zapisywania zapytania wyszukiwania lambda jest przeprowadzenie z jednego węzła do innego. Aby określić typ krawędzi kroków, użyj *FollowEdge()* i przekazywanie typów pożądanej krawędzi. *FollowEdge()* przyjmuje dowolnej liczby argumenty typu string:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Jeśli nie Szanujemy typów czasie, które należy wykonać, po prostu pominąć *FollowEdge()* między dwoma węzłami: zapytanie przeprowadzi wszystkie możliwe krawędzi między tymi dwoma węzłami.

Można określić akcje Przechodzenie do wykonania w węźle, za pomocą *VisitNode()*, oznacza to, czy zatrzymać w tym węźle i zwrócenia wyniku bieżącej ścieżki lub aby kontynuować zapoznać się z wykresu.  Typ wyliczeniowy *akcji* definiują dwa typy działań: *Action.Return* i *Action.Continue*. Firma Microsoft może przekazać takie wartości wyliczenia bezpośrednio do *VisitNode()*, lub połączyć je z bitowego- i operatora "&". Dwa działania są połączone, oznacza to, podjęcia działań zarówno. Uwaga: nie używaj bitowo- lub operator "|" w akcji. To spowoduje, że zapytanie, aby zakończyć bez powrotu żadnych czynności. Pomijanie *VisitNode()* między dwiema *FollowEdge()* wywołania spowoduje, że kwerenda bezwarunkowo Eksploruj wykres po węzła.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Dla *VisitNode()*, firma Microsoft może również przekazać w wyrażeniu lambda typu *wyrażenie\<Func\<węzeł i, Akcja\>\>*, który przyjmuje *Węzeł i* i zwraca przechodzenie akcję:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*Węzeł i* 

*Węzeł i* zapewnia *tylko do odczytu* interfejsów i kilka wbudowane funkcje pomocników w węźle dostępu do danych. 

### <a name="basic-data-access-interfaces"></a>Interfejsy dostępu do danych podstawowych

##### <a name="long-cellid"></a>długie CellID

64-bitowy identyfikator węzła. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(ciągów fieldName)

Pobiera wartość określonej właściwości. *T* jest żądanego typu, który powinien być interpretowane jako pole. Rzutowanie typów automatyczne zostanie podjęta, jeśli żądanego typu nie można niejawnie przekonwertować typu pola. Uwaga: Jeśli ta właściwość jest wielowartościowe, *GetField\<ciąg\>*  spowoduje, że na liście, aby można serializować do ciągu Json ["val1", "Wart2",...]. Jeśli właściwość nie istnieje, spowoduje zgłoszenie wyjątku i przerwania bieżącego eksploracji wykresu.

##### <a name="bool-containsfieldstring-fieldname"></a>wartość logiczna ContainsField (nazwa pola ciągu)

Informuje, czy pole o podanej nazwie istnieje w bieżącym węźle.

##### <a name="string-getstring-fieldname"></a>ciąg get (nazwa pola ciągu)

Jak działa *GetField\<ciąg\>(fieldName)*. Jednak go nie zgłaszają wyjątki, gdy nie można odnaleźć pola, zwraca puste string("") zamiast tego.

##### <a name="bool-hasstring-fieldname"></a>wartość logiczna ma (nazwa pola ciągu)

Informuje, czy dana właściwość istnieje w bieżącym węźle. Taki sam jak *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>wartość logiczna ma (fieldName ciąg, wartość ciągu)

Informuje, jeśli właściwość nie ma podanej wartości. 

##### <a name="int-countstring-fieldname"></a>Liczba int (nazwa pola ciągu)

Pobierz liczbę wartości właściwości. Gdy właściwość nie istnieje, zwraca wartość 0.

### <a name="built-in-helper-functions"></a>Wbudowane funkcje pomocników

##### <a name="action-returnifbool-condition"></a>Akcja return_if (wartość logiczna warunek)

Zwraca *Action.Return* po spełnieniu warunku *true*. Jeśli warunek nie jest *false* i tego wyrażenia nie jest połączony z innymi działaniami z bitowego- i operatora eksploracji wykres zostanie przerwana.

##### <a name="action-continueifbool-condition"></a>Akcja continue_if (wartość logiczna warunek)

Zwraca *Action.Continue* po spełnieniu warunku *true*. Jeśli warunek nie jest *false* i tego wyrażenia nie jest połączony z innymi działaniami z bitowego- i operatora eksploracji wykres zostanie przerwana.

##### <a name="bool-dicedouble-p"></a>selekcji bool (dwa razy p)

Generuje liczbę losową, która jest większa niż lub równa 0,0 i mniejszą niż 1,0. Ta funkcja zwraca *true* tylko wtedy, gdy liczba jest mniejsza lub równa *p*.

W porównaniu z *json* wyszukiwania, *lambda* wyszukiwanie jest bardziej obszerne: wyrażenia lambda w języku C# bezpośrednio można określić wzorcami zapytań. Poniżej przedstawiono dwa przykłady.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
