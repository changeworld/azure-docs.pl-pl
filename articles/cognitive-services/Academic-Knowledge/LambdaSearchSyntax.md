---
title: Składnia wyszukiwania lambda — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat składni wyszukiwania Lambda, których można używać w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61337792"
---
# <a name="lambda-search-syntax"></a>Składnia wyszukiwania lambda

Każdy *lambda* zapytania wyszukiwania ciągu w tym artykule opisano wzorzec graficzny. Zapytanie musi mieć co najmniej jeden węzeł początkowy, określając, z których węzła grafu Zaczniemy podczas przechodzenia. Aby określić węzeł początkowy, należy wywołać *MAG. StartFrom()* metody i przekaż identyfikatory jednego lub więcej węzłów lub kwerendy obiektu, który określa ograniczenia wyszukiwania. *StartFrom()* metoda ma trzy przeciążenia. Wszystkie z nich wykonać dwa argumenty z drugim jest opcjonalne. Pierwszy argument może być liczba całkowita typu long, wyliczalny zbiór długa liczba całkowita lub ciąg JSON reprezentujący obiekt, z tą samą semantyką jako w *json* wyszukiwania:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Proces pisania zapytania wyszukiwania lambda jest zapoznaj się z jednego węzła do drugiego. Aby określić typ urządzeniami brzegowymi w celu zaprezentowania, użyj *FollowEdge()* i przekazać w typach pożądanej krawędzi. *FollowEdge()* przyjmuje dowolną liczbę argumentów ciągów:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Jeśli firma Microsoft nie są istotne typów parametrów w czasie do wykonania, po prostu pominąć *FollowEdge()* między dwoma węzłami: zapytanie przeprowadzi wszystkich możliwych krawędzi między tymi dwoma węzłami.

Można określić akcje Przechodzenie do wykonania w węźle za pomocą *VisitNode()* , oznacza to, czy można zatrzymać w tym węźle i zwracać bieżącą ścieżkę w wyniku lub w dalszym ciągu zrozumienie wykresu.  Typ wyliczeniowy *akcji* definiuje dwa rodzaje działań: *Action.Return* i *Action.Continue*. Możemy przekazać wartość wyliczenia bezpośrednio do *VisitNode()* , lub łączyć je przy użyciu bitowego operatora- i operator 'i'. Połączeniu dwa działania, oznacza to, że obie akcje zostaną wykonane. Uwaga: nie używaj bitowe — lub operatora "|" w akcji. To spowoduje, że zapytanie, aby zakończyć bez zwracania niczego. Pomijanie *VisitNode()* między dwoma *FollowEdge()* wywołania spowoduje, że kwerenda bezwarunkowo zrozumienie wykresu po wprowadzeniu na węzeł.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Dla *VisitNode()* , możemy również przekazać w wyrażeniu lambda typu *wyrażenie\<Func\<węzeł i, Akcja\>\>* , który przyjmuje *Węzeł i* i zwraca przechodzenia przez akcję:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*Węzeł i* 

*Węzeł i* zapewnia *tylko do odczytu* dostępu do danych, interfejsy i kilka wbudowane funkcje pomocników w węźle. 

### <a name="basic-data-access-interfaces"></a>Interfejsy dostępu do danych podstawowych

##### <a name="long-cellid"></a>długi CellID

64-bitowy identyfikator węzła. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

Pobiera wartość określonej właściwości. *T* to żądany typ, który powinien być interpretowane jako pole. Rzutowanie typów automatycznego zostanie podjęta, jeśli żądany typ nie można niejawnie przekonwertować z typu pola. Uwaga: Jeśli właściwość jest wielowartościowy *GetField\<ciąg\>*  spowoduje, że na liście, aby można serializować do ciągu Json ["val1", "val2",...]. Jeśli właściwość nie istnieje, wówczas zgłoszenie wyjątku i przerwać bieżącą eksplorację wykresu.

##### <a name="bool-containsfieldstring-fieldname"></a>wartość logiczna ContainsField (nazwa pola ciągu)

Informuje, czy pole o podanej nazwie istnieje w bieżącym węźle.

##### <a name="string-getstring-fieldname"></a>ciąg Pobierz (nazwa pola ciągu)

Działa jak *GetField\<ciąg\>(NazwaPola)* . Jednak go nie generuje wyjątków, gdy pole nie zostanie znaleziony, zwraca pusty string("") zamiast tego.

##### <a name="bool-hasstring-fieldname"></a>bool ma (nazwa pola ciągu)

Informuje, czy dana właściwość istnieje w bieżącym węźle. Taki sam jak *ContainsField(fieldName)* .

##### <a name="bool-hasstring-fieldname-string-value"></a>bool ma (Nazwa ciągu, pola wartości ciągu)

Informuje o tym, jeśli właściwość ma podanej wartości. 

##### <a name="int-countstring-fieldname"></a>Liczba int (nazwa pola ciągu)

Pobierz wartości danej właściwości. Gdy właściwość nie istnieje, zwraca wartość 0.

### <a name="built-in-helper-functions"></a>Wbudowane funkcje pomocników

##### <a name="action-returnifbool-condition"></a>Akcja return_if (warunków, wartość logiczna)

Zwraca *Action.Return* Jeśli warunek nie jest *true*. Jeśli warunek nie jest *false* i to wyrażenie nie jest połączony z innymi akcjami z bitowej — i operatora eksploracji wykresu zostanie przerwane.

##### <a name="action-continueifbool-condition"></a>Akcja continue_if (warunków, wartość logiczna)

Zwraca *Action.Continue* Jeśli warunek nie jest *true*. Jeśli warunek nie jest *false* i to wyrażenie nie jest połączony z innymi akcjami z bitowej — i operatora eksploracji wykresu zostanie przerwane.

##### <a name="bool-dicedouble-p"></a>wartość logiczna dice (podwójny p)

Generuje losową liczbę, która jest mniejsza niż 0,0 i mniejszą niż 1,0. Ta funkcja zwraca *true* tylko wtedy, gdy liczba jest mniejsza niż lub równa *p*.

W porównaniu z *json* wyszukiwania, *lambda* wyszukiwanie jest bardziej ekspresyjnego: C#wyrażenia lambda mogą bezpośrednio używane do określania wzorców zapytań. Poniżej przedstawiono dwa przykłady.

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
