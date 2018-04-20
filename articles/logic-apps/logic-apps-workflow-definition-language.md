---
title: Schemat języka definicji przepływu pracy — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zdefiniuj przepływów pracy opartych na schemat definicji przepływu pracy dla usługi Azure Logic Apps
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 42932e6d1727a1444c62f565ae3c48dc178aeb2b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Schemat języka definicji przepływu pracy dla usługi Azure Logic Apps

Definicji przepływu pracy zawiera logikę rzeczywista, która wykonuje jako część aplikacji logiki. Ta definicja zawiera jeden lub więcej wyzwalaczy, które uruchamiają aplikację logiki i co najmniej jednej akcji dla aplikacji logiki do wykonania.  
  
## <a name="basic-workflow-definition-structure"></a>Struktura definicji podstawowy przepływ pracy

Oto podstawowa struktura definicji przepływu pracy:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> [Interfejsu API REST zarządzania przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows) dokumentacja zawiera informacje na temat sposobu tworzenia i zarządzania nimi przepływów pracy aplikacji logiki.
  
|Nazwa elementu|Wymagane|Opis|  
|------------------|--------------|-----------------|  
|$schema|Nie|Określa lokalizację pliku schematu JSON, który zawiera opis wersji języka definicji. Tej lokalizacji jest wymagany, gdy odwołanie definicję zewnętrznie. Poniżej przedstawiono lokalizację, w tym dokumencie: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`|  
|contentVersion|Nie|Określa wersję definicji. Podczas wdrażania przy użyciu definicji przepływu pracy tej wartości można użyć, aby upewnić się, czy prawy definicji jest używany.|  
|parameters|Nie|Określa parametry używane do wprowadzania danych do definicji. Można określić maksymalnie 50 parametrów.|  
|Wyzwalacze|Nie|Określa informacje o wyzwalacze, które inicjować przepływ pracy. Można określić maksymalnie 10 wyzwalaczy.|  
|Akcje|Nie|Określa akcje, które są przyjmowane, gdy strumień jest wykonywana. Można zdefiniować maksymalnie 250 akcji.|  
|wyjścia|Nie|Określa informacje dla wdrożonych zasobów. Można określić maksymalnie 10 danych wyjściowych.|  
  
## <a name="parameters"></a>Parametry

Ta sekcja określa parametry, które są używane w definicji przepływu pracy w czasie wdrażania. Wszystkie parametry muszą być zadeklarowane w tej sekcji, przed ich użyciem w innych częściach definicji.  
  
W poniższym przykładzie przedstawiono struktura definicji parametru:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Nazwa elementu|Wymagane|Opis|  
|------------------|--------------|-----------------|  
|type|Yes|**Typ**: ciąg <p> **Deklaracja**: `"parameters": {"parameter1": {"type": "string"}}` <p> **Specyfikacja**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Typ**: securestring <p> **Deklaracja**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **Specyfikacja**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Typ**: int <p> **Deklaracja**: `"parameters": {"parameter1": {"type": "int"}}` <p> **Specyfikacja**: `"parameters": {"parameter1": {"value" : 5}}` <p> **Typ**: wartość logiczna <p> **Deklaracja**: `"parameters": {"parameter1": {"type": "bool"}}` <p> **Specyfikacja**: `"parameters": {"parameter1": { "value": true }}` <p> **Typ**: tablicy <p> **Deklaracja**: `"parameters": {"parameter1": {"type": "array"}}` <p> **Specyfikacja**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Typ**: obiekt <p> **Deklaracja**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specyfikacja**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Typ**: secureobject <p> **Deklaracja**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specyfikacja**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Uwaga:** `securestring` i `secureobject` typy nie są zwracane w `GET` operacji. Haseł, kluczy i kluczy tajnych należy użyć tego typu.|  
|Wartość domyślna|Nie|Określa wartość domyślną dla parametru, gdy nie określono wartości w momencie utworzenia zasobu.|  
|allowedValues|Nie|Określa tablicę dozwolonych wartości dla parametru.|  
|metadane|Nie|Określa dodatkowe informacje na temat parametrów, takich jak czytelny opis lub danych czasu projektowania używana przez Visual Studio lub innych narzędzi.|  
  
Ten przykład przedstawia, jak można użyć parametru w sekcji body akcji:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 Można również użyć parametrów w danych wyjściowych.  
  
## <a name="triggers-and-actions"></a>Wyzwalacze i akcje  

Wyzwalacze i akcje Określ wywołania, które mogą zostać użyte podczas wykonywania przepływu pracy. Aby uzyskać szczegółowe informacje o tej sekcji, zobacz [działania przepływu pracy i wyzwalaczy](logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Dane wyjściowe  

Dane wyjściowe określ informacje, które mogą być zwracane z przepływu pracy Uruchom. Na przykład jeśli masz określonego stanu lub wartości, które mają być śledzone przy każdym uruchomieniu w wykonywania dane wyjściowe można uwzględnić te dane. Dane są wyświetlane w interfejsie API REST zarządzania dla tego uruchomienia i w pakiecie administracyjnym interfejsu użytkownika dla tego uruchomienia w portalu Azure. Te dane wyjściowe do innych systemów zewnętrznych, takich jak usługi Power BI może również przepływać do tworzenia pulpitów nawigacyjnych. Dane wyjściowe są *nie* umożliwia odpowiadanie na przychodzące żądania na interfejsie API REST usługi. Na odpowiadanie na przychodzące żądania przy użyciu `response` typ akcji, Oto przykład:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Nazwa elementu|Wymagane|Opis|  
|------------------|--------------|-----------------|  
|klucz1|Yes|Określa identyfikator klucza dla danych wyjściowych. Zastąp **klucz1** o nazwie, która ma być używany do identyfikowania dane wyjściowe.|  
|wartość|Yes|Określa wartość w danych wyjściowych.|  
|type|Yes|Określa typ wartości, która została określona. Typy możliwe wartości to: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Wyrażenia  

Wartości JSON w definicji może być literałem lub można je wyrażeń, które są oceniane po definicji jest używany. Na przykład:  
  
```json
"name": "value"
```

 lub  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Niektóre wyrażenia pobrać wartości z akcji środowiska uruchomieniowego, które może nie istnieć na początku wykonywania. Można użyć **funkcje** Aby pobrać niektóre z tych wartości.  
  
Wyrażenia mogą występować w dowolnym miejscu w wartości ciągu JSON i zawsze powoduje inną wartość JSON. Gdy wartość JSON został uznany za wyrażenie, treść wyrażenia jest wyodrębniany przez usunięcie znaku (@). Jeśli wymagane jest literałem, która rozpoczyna się od @, że ciąg musi być wpisywany z użyciem@. W poniższych przykładach pokazano, jak są analizowane wyrażenia.  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"Parametry"|Znaki "parameters" są zwracane.|  
|"parametry [1]"|Zwracane są znaki "parametry [1]".|  
|"@@"|Ciąg 1 znaku, który zawiera "@" jest zwracany.|  
|" @"|Ciąg znaków 2, który zawiera "@" jest zwracany.|  
  
Z *ciągu interpolacji*, wyrażenia może również zostać wyświetlony wewnątrz ciągów, których wyrażenia są ujęte w `@{ ... }`. Na przykład: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

Wynik jest zawsze ciąg, co sprawia, że ta funkcja jest podobny do `concat` funkcji. Załóżmy, że określono `myNumber` jako `42` i `myString` jako `sampleString`:  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"@parameters(mójCiąg)"|Zwraca `sampleString` jako ciąg.|  
|"@{parameters('myString')}"|Zwraca `sampleString` jako ciąg.|  
|"@parameters(myNumber)"|Zwraca `42` jako *numer*.|  
|"@{parameters('myNumber')}"|Zwraca `42` jako *ciąg*.|  
|"Odpowiedź brzmi: @{parameters('myNumber')}"|Zwraca ciąg `Answer is: 42`.|  
|"@concat(" Odpowiedź brzmi: ", string(parameters('myNumber')))"|Zwraca ciąg `Answer is: 42`|  
|"Odpowiedź brzmi: @@ {parameters('myNumber')}"|Zwraca ciąg `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operatory  

Operatory są znaki, które można używać wewnątrz wyrażenia lub funkcji. 
  
|Operator|Opis|  
|--------------|-----------------|  
|.|Operator kropki umożliwia odwoływanie się do właściwości obiektu|  
|?|Operator zapytania umożliwia odwołanie właściwości obiektu bez błędów czasu wykonywania o wartości null. Na przykład umożliwia to wyrażenie obsługi wyzwalania zerowy dane wyjściowe: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|Znak pojedynczego cudzysłowu jest jedynym sposobem, aby zawijać literałów ciągów. Nie można użyć podwójnych cudzysłowów wewnątrz wyrażenia, ponieważ ta znaki interpunkcyjne powoduje konflikt z ofertą JSON, który opakowuje całego wyrażenia.|  
|[]|Nawiasy kwadratowe umożliwia pobieranie wartości z tablicy o określonym indeksie. Na przykład, jeśli akcja, która przekazuje `range(0,10)`w celu `forEach` funkcji, można użyć tej funkcji można pobrać elementów poza tablice:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Funkcje  

Można także wywoływać funkcje w wyrażeniach. W poniższej tabeli przedstawiono funkcje, których można użyć w wyrażeniu.  
  
|Wyrażenie|Ocena|  
|----------------|----------------|  
|"@function("Witaj")"|Wywołuje funkcja członkowska definicji z ciągiem literału Hello jako pierwszym parametrem.|  
|"@function(" On "chłodnej s!") "|Wywołuje funkcja członkowska definicji z ciągiem literału 'Jest chłodnej'! jako pierwszego parametru|  
|"@function().prop1"|Zwraca wartość właściwości prop1 `myfunction` Członkowskie definicji.|  
|"@function('Hello').prop1"|Wywołuje funkcja członkowska definicji z ciągiem literału "Hello" jako pierwszym parametrem i zwraca wartość właściwości prop1 obiektu.|  
|"@function(parameters('Hello'))"|Wartość parametru Hello i przekazuje wartość do funkcji|  
  
### <a name="referencing-functions"></a>Odwołanie do funkcji  

Te funkcje umożliwiają odwołania wyjścia z innych działań w aplikacji logiki lub wartości przekazane podczas tworzenia aplikacji logiki. Na przykład możesz odwoływać dane w jednym kroku, aby użyć go w innym.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|parameters|Zwraca wartość parametru, który jest zdefiniowany w definicji. <p>`parameters('password')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: parametr <p> **Opis elementu**: wymagane. Nazwa parametru wartości, których chcesz.|  
|action|Umożliwia wyrażenie ma być jego wartość z innej nazwy JSON i pary wartości lub dane wyjściowe bieżącej akcji środowiska wykonawczego. Właściwość reprezentowany przez propertyPath w poniższym przykładzie jest opcjonalna. Jeśli nie określono propertyPath, odwołanie jest obiekt całej akcji. Tej funkcji można używać tylko wewnątrz czy — do warunków akcji. <p>`action().outputs.body.propertyPath`|  
|Akcje|Umożliwia wyrażenie ma być jego wartość z innej nazwy JSON i pary wartości lub dane wyjściowe działania środowiska wykonawczego. Wyrażenia te jawnie deklarować, że jedno działanie zależy od innej akcji. Właściwość reprezentowany przez propertyPath w poniższym przykładzie jest opcjonalna. Jeśli nie określono propertyPath, odwołanie jest obiekt całej akcji. Aby określić zależności można użyć tego elementu lub element warunków, ale nie trzeba używać obu zasobów zależnych. <p>`actions('myAction').outputs.body.propertyPath` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Nazwa akcji <p> **Opis elementu**: wymagane. Nazwa akcji, których wartości mają. <p> Dostępne właściwości w obiekcie akcji to: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Zobacz [interfejsu API Rest](http://go.microsoft.com/fwlink/p/?LinkID=850646) szczegółowe informacje na temat tych właściwości.|
|Wyzwalacz|Umożliwia wyrażenie ma być jego wartość z innej nazwy JSON i pary wartości lub dane wyjściowe wyzwalacza środowiska uruchomieniowego. Właściwość reprezentowany przez propertyPath w poniższym przykładzie jest opcjonalna. Jeśli nie określono propertyPath, odwołanie jest obiektem wyzwalacza całego. <p>`trigger().outputs.body.propertyPath` <p>Użyta wewnątrz elementu trigger dane wejściowe, funkcja zwraca dane wyjściowe poprzedniego wykonywania. Jednakże, gdy jest używany wewnątrz warunku wyzwalacza `trigger` funkcja zwraca dane wyjściowe wykonania bieżącej. <p> Dostępne właściwości w obiekcie wyzwalacza to: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Zobacz [interfejsu API Rest](http://go.microsoft.com/fwlink/p/?LinkID=850644) szczegółowe informacje na temat tych właściwości.|
|actionOutputs|Ta funkcja jest skrócona forma funkcji `actions('actionName').outputs` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Nazwa akcji <p> **Opis elementu**: wymagane. Nazwa akcji, których wartości mają.|  
|actionBody i treści|Te funkcje są skrócona forma funkcji `actions('actionName').outputs.body` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Nazwa akcji <p> **Opis elementu**: wymagane. Nazwa akcji, których wartości mają.|  
|triggerOutputs|Ta funkcja jest skrócona forma funkcji `trigger().outputs`|  
|triggerBody|Ta funkcja jest skrócona forma funkcji `trigger().outputs.body`|  
|Element|Gdy jest używany wewnątrz powtarzających się akcja, ta funkcja zwraca element w tablicy dla tej iteracji akcji. Na przykład jeśli akcja, która jest uruchamiana dla każdego elementu tablicy wiadomości, należy użyć następującej składni: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Kolekcja funkcji  

Funkcje te działają na kolekcje i zazwyczaj stosowane do tablic ciągów i czasami słowników.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|zawiera|Zwraca wartość PRAWDA, jeśli słownik zawiera listę kluczy, zawiera wartość lub ciąg zawiera podciąg. Na przykład, funkcja zwraca `true`: <p>`contains('abacaba','aca')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: W kolekcji <p> **Opis elementu**: wymagane. Kolekcja do przeszukania. <p> **Liczba parametrów**: 2 <p> **Nazwa**: obiekt Find <p> **Opis elementu**: wymagane. Obiekt, aby znaleźć wewnątrz **w kolekcji**.|  
|długość|Zwraca liczbę elementów w tablicy lub ciągu. Na przykład, funkcja zwraca `3`:  <p>`length('abc')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji <p> **Opis elementu**: wymagane. Kolekcja, do których chcesz pobrać długości.|  
|pusty|Zwraca wartość PRAWDA, jeśli obiekt, tablicy lub ciągu jest pusty. Na przykład, funkcja zwraca `true`: <p>`empty('')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji <p> **Opis elementu**: wymagane. Kolekcja, aby sprawdzić, czy jest pusta.|  
|część wspólną|Zwraca pojedynczą tablicę lub obiekt, który zawiera wspólne elementy między tablicami lub obiektów przekazany. Na przykład, funkcja zwraca `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>Parametry funkcji może być zestaw obiektów lub zestaw tablic (nie kombinację obu). Jeśli występują dwa obiekty o takiej samej nazwie, ostatni obiekt o tej nazwie pojawia się w końcowym obiektu. <p> **Liczba parametrów**: 1... *n* <p> **Nazwa**: kolekcja *n* <p> **Opis elementu**: wymagane. Kolekcje do oceny. Obiekt musi być we wszystkich zbiorach przekazany do są wyświetlane w wynikach.|  
|Unii|Zwraca pojedynczą tablicę lub obiektu z wszystkich elementów, które są w tablicy lub obiekt przekazany do tej funkcji. Na przykład, funkcja zwraca `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>Parametry funkcji może być zestaw obiektów lub zestaw tablic (nie kombinacja jego). Jeśli występują dwa obiekty o takiej samej nazwie w ostateczne dane wyjściowe, ostatni obiekt o tej nazwie pojawia się w końcowym obiektu. <p> **Liczba parametrów**: 1... *n* <p> **Nazwa**: kolekcja *n* <p> **Opis elementu**: wymagane. Kolekcje do oceny. Obiekt, który pojawia się w dowolnej kolekcji pojawia się również w wyniku.|  
|pierwszy|Zwraca pierwszy element tablicy lub ciągu przekazany. Na przykład, funkcja zwraca `0`: <p>`first([0,2,3])` <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji <p> **Opis elementu**: wymagane. Podjęcie pierwszy obiekt z kolekcji.|  
|ostatni|Zwraca ostatni element w tablicy lub ciągu przekazany. Na przykład, funkcja zwraca `3`: <p>`last('0123')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji <p> **Opis elementu**: wymagane. Kolekcja podjęcie ostatni obiekt z.|  
|podejmij|Zwraca pierwszy **liczba** przekazano elementów z tablicy lub ciągu. Na przykład, funkcja zwraca `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji <p> **Opis elementu**: wymagane. Kolekcja z, w którym można wykonać pierwsze **liczba** obiektów. <p> **Liczba parametrów**: 2 <p> **Nazwa**: liczba <p> **Opis elementu**: wymagane. Liczba obiektów z **kolekcji**. Musi być dodatnią liczbą całkowitą.|  
|Pomiń|Zwraca elementy w tablicy, zaczynając od indeksu **liczba**. Na przykład, funkcja zwraca `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji <p> **Opis elementu**: wymagane. Kolekcja, aby pominąć pierwszy **liczba** obiektów z. <p> **Liczba parametrów**: 2 <p> **Nazwa**: liczba <p> **Opis elementu**: wymagane. Liczba obiektów, aby usunąć z przodu **kolekcji**. Musi być dodatnią liczbą całkowitą.|  
|join|Zwraca ciąg z każdym elementem tablicy przyłączone ogranicznikiem, na przykład zwraca `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji<br /><br /> **Opis elementu**: wymagane. Elementy z kolekcji.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: ogranicznika<br /><br /> **Opis elementu**: wymagane. Ciąg do rozdzielenia elementy.|  
  
### <a name="string-functions"></a>Funkcje ciągów

Następujące funkcje dotyczą tylko ciągi. Umożliwia także niektóre funkcje kolekcji ciągów.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|concat|Łączy ze sobą dowolną liczbę ciągów. Na przykład, jeśli parametr 1 jest `p1`, funkcja zwraca `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Liczba parametrów**: 1... *n* <p> **Nazwa**: ciąg *n* <p> **Opis elementu**: wymagane. Ciągi są łączone w pojedynczy ciąg.|  
|substring|Zwraca podzbiór znaków z ciągu. Na przykład, funkcja zwraca `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, z którego jest pobierany podciąg. <p> **Liczba parametrów**: 2 <p> **Nazwa**: indeks początkowy <p> **Opis elementu**: wymagane. Indeks, gdzie podciąg rozpoczyna się w parametrze 1. <p> **Liczba parametrów**: 3 <p> **Nazwa**: długość <p> **Opis elementu**: wymagane. Długość podciąg.|  
|Zamień|Zamienia ciąg dany ciąg znaków. Na przykład, funkcja zwraca `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który jest przeszukiwany parametru 2 i aktualizowane z parametrem 3, gdy parametr 2 znajduje się w parametrze 1. <p> **Liczba parametrów**: 2 <p> **Nazwa**: stary ciąg <p> **Opis elementu**: wymagane. Ciąg, aby zastąpić parametr 3, po znalezieniu dopasowania w parametrze 1 <p> **Liczba parametrów**: 3 <p> **Nazwa**: nowe parametry <p> **Opis elementu**: wymagane. Ciąg, który jest używany do zastępowania ciągów w parametrze 2, po znalezieniu dopasowania w parametrze 1.|  
|Identyfikator GUID|Ta funkcja generuje ciąg globalnie unikatowy (identyfikator globalny GUID). Na przykład ta funkcja może wygenerować tego identyfikatora GUID: `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Format <p> **Opis elementu**: opcjonalne. Specyfikator formatu jednej, która wskazuje [sposób formatowania wartość tego identyfikatora Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Format parametru może być "N", "D", "B", "P" lub "X". Jeśli format nie jest podany, "D" jest używany.|  
|toLower|Konwertuje ciąg na małe litery. Na przykład, funkcja zwraca `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg do przekonwertowania na niższych wielkości liter. Jeśli znak w ciągu nie ma odpowiednika małe litery, znak jest dołączony bez zmian w zwracany ciąg.|  
|toUpper|Konwertuje ciąg na wielkie litery. Na przykład, funkcja zwraca `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg do przekonwertowania na górnym wielkości liter. Jeśli znak w ciągu nie ma odpowiednika wielkie litery, znak jest dołączony bez zmian w zwracany ciąg.|  
|indexof|Znajdź insensitively indeks wartości w przypadku ciągu. Na przykład, funkcja zwraca `7`: <p>`indexof('hello, world.', 'world')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości. <p> **Liczba parametrów**: 2 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Wartość do wyszukiwania indeksu.|  
|lastindexof|Znajdź ostatni indeks wartości w przypadku ciągu insensitively. Na przykład, funkcja zwraca `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości. <p> **Liczba parametrów**: 2 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Wartość do wyszukiwania indeksu.|  
|StartsWith|Sprawdza, czy ciąg rozpoczyna się od liter wartość insensitively. Na przykład, funkcja zwraca `true`: <p>`startswith('hello, world', 'hello')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości. <p> **Liczba parametrów**: 2 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Mogą rozpoczynać się wartość ciągu.|  
|EndsWith|Sprawdza, czy ciąg kończy się wyrazem przypadku wartość insensitively. Na przykład, funkcja zwraca `true`: <p>`endswith('hello, world', 'world')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości. <p> **Liczba parametrów**: 2 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Wartość ciągu mogą kończyć się.|  
|split|Dzieli ciągu za pomocą separatora. Na przykład, funkcja zwraca `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który jest podzielona. <p> **Liczba parametrów**: 2 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Separator.|  
  
### <a name="logical-functions"></a>Funkcje logiczne  

Funkcje te są przydatne w warunkach i może służyć do oceny dowolnego typu logiki.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|równa się|Zwraca wartość PRAWDA, jeśli dwie wartości są równe. Na przykład, jeśli parameter1 someValue, funkcja zwraca `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt o 1 <p> **Opis elementu**: wymagane. Obiekt do porównania z **2 obiektu**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: obiekt 2 <p> **Opis elementu**: wymagane. Obiekt do porównania z **1 obiektu**.|  
|mniej|Zwraca wartość PRAWDA, jeśli pierwszy argument jest mniejszy od drugiego. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład, funkcja zwraca `true`: <p>`less(10,100)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt o 1 <p> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest mniejsza niż **2 obiektu**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: obiekt 2 <p> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż **1 obiektu**.|  
|lessOrEquals|Zwraca wartość PRAWDA, jeśli pierwszy argument jest mniejsze niż lub równe drugiemu. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład, funkcja zwraca `true`: <p>`lessOrEquals(10,10)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt o 1 <p> **Opis elementu**: wymagane. Obiekt do Sprawdź, czy jest mniejsza lub równa **2 obiektu**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: obiekt 2 <p> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż lub równa **1 obiektu**.|  
|większa|Zwraca wartość PRAWDA, jeśli pierwszy argument jest większa od drugiej. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład, funkcja zwraca `false`:  <p>`greater(10,10)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt o 1 <p> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż **2 obiektu**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: obiekt 2 <p> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest mniejsza niż **1 obiektu**.|  
|greaterOrEquals|Zwraca wartość PRAWDA, jeśli pierwszy argument jest większe lub równe drugiemu. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład, funkcja zwraca `false`: <p>`greaterOrEquals(10,100)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt o 1 <p> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż lub równa **2 obiektu**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: obiekt 2 <p> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest mniejsze niż lub równe **1 obiektu**.|  
|i|Zwraca wartość true, jeśli oba parametry mają wartość true. Oba argumenty muszą być wartości logiczne. Na przykład, funkcja zwraca `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość logiczna 1 <p> **Opis elementu**: wymagane. Pierwszy argument, który musi być `true`. <p> **Liczba parametrów**: 2 <p> **Nazwa**: wartość logiczna 2 <p> **Opis elementu**: wymagane. Drugi argument musi być `true`.|  
|lub|Zwraca wartość true, jeśli parametr ma wartość true. Oba argumenty muszą być wartości logiczne. Na przykład, funkcja zwraca `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość logiczna 1 <p> **Opis elementu**: wymagane. Pierwszy argument, który może być `true`. <p> **Liczba parametrów**: 2 <p> **Nazwa**: wartość logiczna 2 <p> **Opis elementu**: wymagane. Drugi argument może być `true`.|  
|nie|Zwraca wartość PRAWDA, jeśli parametry są `false`. Oba argumenty muszą być wartości logiczne. Na przykład, funkcja zwraca `true`: <p>`not(contains('200 Success','Fail'))` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość logiczna <p> **Opis elementu**: zwraca wartość true, jeśli parametry są `false`. Oba argumenty muszą być wartości logiczne. Ta funkcja zwraca `true`:  `not(contains('200 Success','Fail'))`|  
|if|Zwraca określoną wartość, według tego, czy wyrażenie spowodowało `true` lub `false`.  Na przykład, funkcja zwraca `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wyrażenie <p> **Opis elementu**: wymagane. Wartość logiczna, która określa wartość, która powinna zwrócić wyrażenia. <p> **Liczba parametrów**: 2 <p> **Nazwa**: True <p> **Opis elementu**: wymagane. Wartość zwracana, gdy wyrażenie jest `true`. <p> **Liczba parametrów**: 3 <p> **Nazwa**: False <p> **Opis elementu**: wymagane. Wartość zwracana, gdy wyrażenie jest `false`.|  
  
### <a name="conversion-functions"></a>Funkcje konwersji  

Funkcje te służą do konwersji między każdego typu natywnego w języku:  
  
- ciąg  
  
- liczba całkowita  
  
- Float  
  
- wartość logiczna  
  
- Tablice  
  
- słowników  

-   Formularze  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|int|Parametr należy przekonwertować na liczbę całkowitą. Na przykład funkcja zwraca 100 jako liczbę zamiast ciągu: <p>`int('100')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość, która jest konwertowana na liczbę całkowitą.|  
|ciąg|Parametr jest skonwertowana do ciągu. Na przykład, funkcja zwraca `'10'`: <p>`string(10)` <p>Można również przeprowadzić konwersję obiektu na ciąg. Na przykład jeśli `myPar` parametr jest obiekt z jedną właściwość `abc : xyz`, a następnie ta funkcja zwraca `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość, która jest konwertowana na ciąg.|  
|JSON|Konwertuj parametr na wartość typu JSON i jest przeciwieństwem `string()`. Na przykład, funkcja zwraca `[1,2,3]` jako tablica zamiast ciągu: <p>`json('[1,2,3]')` <p>Podobnie ciąg można przekonwertować na obiekt. Na przykład, funkcja zwraca `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który jest konwertowany na wartość typu macierzystego. <p>`json()` Funkcja obsługuje za dane wejściowe XML. Na przykład, wartość parametru: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>jest konwertowana na dane JSON: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Float|Liczba zmiennoprzecinkowa przekonwertować argumentu parametru. Na przykład, funkcja zwraca `10.333`: <p>`float('10.333')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość, która jest konwertowana na liczba zmiennoprzecinkowa.|  
|wartość logiczna|Konwersji parametru na wartość logiczną. Na przykład, funkcja zwraca `false`: <p>`bool(0)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość, która jest konwertowana na wartość logiczną.|  
|base64|Zwraca reprezentację ciągu wejściowego base64. Na przykład, funkcja zwraca `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: String 1 <p> **Opis elementu**: wymagane. Ciąg do zakodowania w reprezentację base64.|  
|base64ToBinary|Zwraca wartość binarna reprezentacja ciągu w kodowaniu base64. Na przykład, ta funkcja zwraca reprezentacja binarna `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg kodowany w formacie base64.|  
|base64ToString|Zwraca reprezentację ciągu based64 zakodowany ciąg. Na przykład, funkcja zwraca `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg kodowany w formacie base64.|  
|Binarny|Zwraca wartość binarna reprezentacja wartości.  Na przykład, ta funkcja zwraca to binarna reprezentacja `some string`: <p>`binary('some string')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość, która jest konwertowana na format binarny.|  
|dataUriToBinary|Zwraca wartość binarna reprezentacja identyfikatora URI danych. Na przykład, ta funkcja zwraca reprezentacja binarna `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Identyfikator URI do przekonwertowania na binarna reprezentacja danych.|  
|dataUriToString|Zwraca reprezentację ciągu identyfikatora URI danych. Na przykład, funkcja zwraca `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg<p> **Opis elementu**: wymagane. Identyfikator URI do przekonwertowania na ciąg reprezentujący dane.|  
|dataUri|Zwraca identyfikator URI wartości danych. Na przykład, funkcja zwraca identyfikator URI danych `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Liczba parametrów**: 1<p> **Nazwa**: wartość<p> **Opis elementu**: wymagane. Wartość można przekonwertować na identyfikator URI danych.|  
|decodeBase64|Zwraca reprezentację ciągu wejściowego based64 w ciągu. Na przykład, funkcja zwraca `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: zwraca reprezentację ciągu wejściowego based64 w ciągu.|  
|encodeuricomponent —|Adres URL specjalne ciąg, który jest przekazywany w. Na przykład, funkcja zwraca `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg niezabezpieczony adres URL znaki ucieczki.|  
|decodeuricomponent —|NZ — adres URL-specjalne ciąg, który jest przekazywany w. Na przykład, funkcja zwraca `You Are:Cool/Awesome`: <p>`decodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg do zdekodowania niezabezpieczony adres URL znaki z.|  
|decodeDataUri|Zwraca wartość binarna reprezentacja danych wejściowych w postaci ciągu identyfikatora URI. Na przykład, ta funkcja zwraca reprezentacja binarna `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. DataURI zdekodować do reprezentacja binarna.|  
|uriComponent|Zwraca identyfikator URI zakodowany reprezentację wartości. Na przykład, funkcja zwraca `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Liczba parametrów**: 1<p> **Nazwa**: ciąg <p> **Opis elementu**: wymagane. Ciąg, który jest kodowany identyfikator URI.|  
|uriComponentToBinary|Zwraca ciąg kodowany w formacie to binarna reprezentacja identyfikatora URI. Na przykład, ta funkcja zwraca to binarna reprezentacja `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: ciąg<p> **Opis elementu**: wymagane. Ciąg kodowany w formacie identyfikatora URI.|  
|uriComponentToString|Zwraca ciąg kodowany w formacie reprezentację ciągu identyfikatora URI. Na przykład, funkcja zwraca `You Are:Cool/Awesome`: <p>`uriComponentToString('You+Are%3ACool%2FAwesome')` <p> **Liczba parametrów**: 1<p> **Nazwa**: ciąg<p> **Opis elementu**: wymagane. Ciąg kodowany w formacie identyfikatora URI.|  
|xml|Zwraca reprezentację XML wartości. Na przykład, funkcja zwraca XML zawartości reprezentowany przez `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>`xml()` Funkcja obsługuje obiekt JSON za dane wejściowe. Na przykład parametr `{ "abc": "xyz" }` jest konwertowana na zawartość XML: `\<abc>xyz\</abc>` <p> **Liczba parametrów**: 1<p> **Nazwa**: wartość<p> **Opis elementu**: wymagane. Wartość do przekonwertowania na format XML.|  
|tablica|Parametr można przekonwertować na typ tablicy. Na przykład, funkcja zwraca `["abc"]`: <p>`array('abc')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość, która jest konwertowany na tablicę.|
|createArray|Tworzy tablicę z parametrów. Na przykład, funkcja zwraca `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Liczba parametrów**: 1... *n* <p> **Nazwa**: wszystkie *n* <p> **Opis elementu**: wymagane. Wartości do łączenia w tablicy.|
|triggerFormDataValue|Zwraca pojedynczą wartość zgodnego z nazwą klucza z danych formularza lub wyzwalacza postać zakodowanych danych wyjściowych.  Jeśli dostępnych jest wiele dopasowuje je spowoduje błąd.  Na przykład zwróci następujące `bar`: `triggerFormDataValue('foo')`<br /><br />**Liczba parametrów**: 1<br /><br />**Nazwa**: Nazwa klucza<br /><br />**Opis elementu**: wymagane. Nazwa klucza wartości formularza danych do zwrócenia.|
|triggerFormDataMultiValues|Zwraca tablicę wartości zgodnego z nazwą klucza z danych formularza lub wyzwalacza postać zakodowanych danych wyjściowych.  Na przykład zwróci następujące `["bar"]`: `triggerFormDataValue('foo')`<br /><br />**Liczba parametrów**: 1<br /><br />**Nazwa**: Nazwa klucza<br /><br />**Opis elementu**: wymagane. Nazwa klucza zwracanych wartości danych formularza.|
|triggerMultipartBody|Zwraca treść część wieloczęściowych danych wyjściowych wyzwalacza.<br /><br />**Liczba parametrów**: 1<br /><br />**Nazwa**: indeks<br /><br />**Opis elementu**: wymagane. Indeks części do pobrania.|
|formDataValue|Zwraca pojedynczą wartość zgodnego z nazwą klucza z danych formularza lub wynik akcji zakodowane w formularzu.  Jeśli dostępnych jest wiele dopasowuje je spowoduje błąd.  Na przykład zwróci następujące `bar`: `formDataValue('someAction', 'foo')`<br /><br />**Liczba parametrów**: 1<br /><br />**Nazwa**: Nazwa akcji<br /><br />**Opis elementu**: wymagane. Nazwa akcji z danych formularza lub postać zakodowanych odpowiedzi.<br /><br />**Liczba parametrów**: 2<br /><br />**Nazwa**: Nazwa klucza<br /><br />**Opis elementu**: wymagane. Nazwa klucza wartości formularza danych do zwrócenia.|
|formDataMultiValues|Zwraca tablicę zgodnego z nazwą klucza z danych formularza lub wynik akcji postać zakodowanych wartości.  Na przykład zwróci następujące `["bar"]`: `formDataMultiValues('someAction', 'foo')`<br /><br />**Liczba parametrów**: 1<br /><br />**Nazwa**: Nazwa akcji<br /><br />**Opis elementu**: wymagane. Nazwa akcji z danych formularza lub postać zakodowanych odpowiedzi.<br /><br />**Liczba parametrów**: 2<br /><br />**Nazwa**: Nazwa klucza<br /><br />**Opis elementu**: wymagane. Nazwa klucza zwracanych wartości danych formularza.|
|multipartBody|Zwraca treść część wieloczęściowych danych wyjściowych akcji.<br /><br />**Liczba parametrów**: 1<br /><br />**Nazwa**: Nazwa akcji<br /><br />**Opis elementu**: wymagane. Nazwa akcji za pomocą wieloczęściowej wiadomości odpowiedzi.<br /><br />**Liczba parametrów**: 2<br /><br />**Nazwa**: indeks<br /><br />**Opis elementu**: wymagane. Indeks części do pobrania.|

### <a name="manipulation-functions"></a>Funkcje manipulowania
 
Funkcje te dotyczą XML i obiektów.
 
|Nazwa funkcji|Opis|  
|-------------------|-----------------| 
|połączenie|Zwraca pierwszy obiekt zerowy przekazanych argumentów. **Uwaga**: pusty ciąg nie jest zerowa. Na przykład, jeśli nie zdefiniowano parametrów 1 i 2, funkcja zwraca `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Liczba parametrów**: 1... *n* <p> **Nazwa**: obiekt*n* <p> **Opis elementu**: wymagane. Obiekty do sprawdzenia wartości null.|
|addProperty|Zwraca obiekt z dodatkowych właściwości. Jeśli właściwość już istnieje w czasie wykonywania, zostanie zgłoszony błąd. Na przykład, ta funkcja zwraca obiekt `{ "abc" : "xyz", "def": "uvw" }`: <p>`addProperty(json('{"abc" : "xyz"}'), 'def', 'uvw')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt <p> **Opis elementu**: wymagane. Obiekt do dodania do nowej właściwości. <p> **Liczba parametrów**: 2 <p> **Nazwa**: Nazwa właściwości <p> **Opis elementu**: wymagane. Nazwa nowej właściwości. <p> **Liczba parametrów**: 3 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość do przypisania do nowej właściwości.|
|Metoda setProperty|Zwraca obiekt z dodatkowych właściwości lub istniejącego zestawu podanej wartości właściwości. Na przykład, ta funkcja zwraca obiekt `{ "abc" : "uvw" }`: <p>`setProperty(json('{"abc" : "xyz"}'), 'abc', 'uvw')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt <p> **Opis elementu**: wymagane. Obiekt, w którym można ustawić właściwości.<p> **Liczba parametrów**: 2 <p> **Nazwa**: Nazwa właściwości<p> **Opis elementu**: wymagane. Nazwa nowej lub istniejącej właściwości. <p> **Liczba parametrów**: 3 <p> **Nazwa**: wartość <p> **Opis elementu**: wymagane. Wartość do przypisania do właściwości.|
|removeProperty|Zwraca obiekt z właściwością usunięte. Jeśli nie istnieje właściwość, aby usunąć oryginalny obiekt jest zwracany. Na przykład, ta funkcja zwraca obiekt `{ "abc" : "xyz" }`: <p>`removeProperty(json('{"abc" : "xyz", "def": "uvw"}'), 'def')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: obiekt <p> **Opis elementu**: wymagane. Obiekt do usunięcia z właściwości.<p> **Liczba parametrów**: 2 <p> **Nazwa**: Nazwa właściwości <p> **Opis elementu**: wymagane. Nazwa właściwości do usunięcia. <p>|
|wyrażenie XPath|Zwraca tablicę węzłów XML odpowiadającym wyrażenie xpath wartości, która daje w wyniku wyrażenia xpath. <p> **Przykład 1** <p>Przyjmuje wartość parametru `p1` jest reprezentację ciągu tego kodu XML: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Ten kod: `xpath(xml(parameters('p1')), '/lab/robot/name')` <p>Zwraca <p>`[ <name>R1</name>, <name>R2</name> ]` <p>Podczas tego kodu: <p>`xpath(xml(parameters('p1')), ' sum(/lab/robot/parts)')` <p>Zwraca <p>`13` <p> <p> **Przykład 2** <p>Podane następującą zawartość XML: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Ten kod: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>lub ten kod: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>Zwraca <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>I ten kod: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>Zwraca <p>``xyz`` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Xml <p> **Opis elementu**: wymagane. Kod XML, na którym można oszacować wyrażenia XPath. <p> **Liczba parametrów**: 2 <p> **Nazwa**: XPath <p> **Opis elementu**: wymagane. Wyrażenie XPath do oceny.|

### <a name="math-functions"></a>Funkcje matematyczne  

Te funkcje mogą być używane dla obu typów liczb: **liczb całkowitych** i **przesunięty**.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|dodaj|Zwraca wynik z dodanie dwóch liczb. Na przykład, funkcja zwraca `20.333`: <p>`add(10,10.333)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Summand 1 <p> **Opis elementu**: wymagane. Numer, aby dodać do **Summand 2**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: Summand 2 <p> **Opis elementu**: wymagane. Numer, aby dodać do **Summand 1**.|  
|Sub|Zwraca wynik z odjęcie dwóch liczb. Na przykład, funkcja zwraca `-0.333`: <p>`sub(10,10.333)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Minuend <p> **Opis elementu**: wymagane. Liczba który **Subtrahend** zostanie usunięty z. <p> **Liczba parametrów**: 2 <p> **Nazwa**: Subtrahend <p> **Opis elementu**: wymagane. Numer do usunięcia z **Minuend**.|  
|mul|Zwraca wynik z pomnożenie dwóch liczb. Na przykład, funkcja zwraca `103.33`: <p>`mul(10,10.333)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Multiplicand 1 <p> **Opis elementu**: wymagane. Liczbę Aby pomnożyć **Multiplicand 2** z. <p> **Liczba parametrów**: 2 <p> **Nazwa**: Multiplicand 2 <p> **Opis elementu**: wymagane. Liczbę Aby pomnożyć **Multiplicand 1** z.|  
|div|Zwraca wynik dzielenia dwóch liczb. Na przykład, funkcja zwraca `1.0333`: <p>`div(10.333,10)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: dzielna <p> **Opis elementu**: wymagane. Numer do dzielenia przez **dzielnik**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: dzielnik. <p> **Opis elementu**: wymagane. Numer do dzielenia **dzielna** przez.|  
|mod|Zwraca resztę po podzieleniu dwóch liczb (modulo). Na przykład, funkcja zwraca `2`: <p>`mod(10,4)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: dzielna <p> **Opis elementu**: wymagane. Numer do dzielenia przez **dzielnik**. <p> **Liczba parametrów**: 2 <p> **Nazwa**: dzielnik. <p> **Opis elementu**: wymagane. Numer do dzielenia **dzielna** przez. Po podziału pozostałe jest zajęta.|  
|min.|Istnieją dwa różne wzorce wywoływania tej funkcji. <p>W tym miejscu `min` ma podjąć tablicy i funkcji zwraca `0`: <p>`min([0,1,2])` <p>Alternatywnie, może to trwać rozdzielaną przecinkami listę wartości, a także zwraca `0`: <p>`min(0,1,2)` <p> **Uwaga**: wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą, tablica musi zawierać tylko cyfry. <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji lub wartość <p> **Opis elementu**: wymagane. Albo tablica wartości, aby znaleźć wartość minimalną lub wartość pierwszego zestawu. <p> **Liczba parametrów**: 2... *n* <p> **Nazwa**: wartość *n* <p> **Opis elementu**: opcjonalne. Jeśli pierwszy parametr ma wartość, można przekazać dodatkowe wartości, a zwracany jest minimum wszystkich wartości przekazany.|  
|maks.|Istnieją dwa różne wzorce wywoływania tej funkcji. <p>W tym miejscu `max` ma podjąć tablicy i funkcji zwraca `2`: <p>`max([0,1,2])` <p>Alternatywnie, może to trwać rozdzielaną przecinkami listę wartości, a także zwraca `2`: <p>`max(0,1,2)` <p> **Uwaga**: wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą, tablica musi zawierać tylko cyfry. <p> **Liczba parametrów**: 1 <p> **Nazwa**: kolekcji lub wartość <p> **Opis elementu**: wymagane. Albo tablica wartości, aby znaleźć wartość maksymalną lub wartość pierwszego zestawu. <p> **Liczba parametrów**: 2... *n* <p> **Nazwa**: wartość *n* <p> **Opis elementu**: opcjonalne. Jeśli wartość jest pierwszym parametrem, można przekazać dodatkowe wartości i maksimum wszystkich wartości przekazanych jest zwracany.|  
|Zakres|Generuje tablicę liczby całkowite począwszy od pewną liczbę. Należy zdefiniować długość tablicy zwracane. <p>Na przykład, funkcja zwraca `[3,4,5,6]`: <p>`range(3,4)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: indeks początkowy <p> **Opis elementu**: wymagane. Pierwszej liczby całkowitej w tablicy. <p> **Liczba parametrów**: 2 <p> **Nazwa**: liczba <p> **Opis elementu**: wymagane. Ta wartość jest liczbę liczb całkowitych, która jest w tablicy.|  
|RAND|Generuje losową liczbę całkowitą określonego zakresu (włącznie tylko na końcu pierwszego). Na przykład, ta funkcja może zwrócić albo `0` lub "1": <p>`rand(0,2)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: minimalna <p> **Opis elementu**: wymagane. Najniższa liczba całkowita, która może być zwracany. <p> **Liczba parametrów**: 2 <p> **Nazwa**: maksymalna <p> **Opis elementu**: wymagane. Ta wartość jest najbliższej liczby całkowitej po największa liczba całkowita, która może zostać zwrócone.|  
 
### <a name="date-functions"></a>Funkcje daty  

|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|utcnow|Zwraca bieżącą sygnaturę czasową jako ciąg, na przykład: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Format <p> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addSeconds|Dodaje całkowitą liczbę sekund do sygnatury czasowej ciągu przekazany. Liczba sekund może być liczbą dodatnią lub ujemną. Domyślnie wynik jest ciągiem w formacie ISO 8601 ("e"), ile specyfikator formatu. Na przykład: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: znacznik czasu <p> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę. <p> **Liczba parametrów**: 2 <p> **Nazwa**: sekund <p> **Opis elementu**: wymagane. Liczba sekund do dodania. Może być ujemna do odjęcia sekund. <p> **Liczba parametrów**: 3 <p> **Nazwa**: Format <p> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addminutes|Dodaje całkowitą liczbę minut do sygnatury czasowej ciągu przekazany. Liczba minut, może być liczbą dodatnią lub ujemną. Domyślnie wynik jest ciągiem w formacie ISO 8601 ("e"), ile specyfikator formatu. Na przykład: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: znacznik czasu <p> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę. <p> **Liczba parametrów**: 2 <p> **Nazwa**: minut <p> **Opis elementu**: wymagane. Liczba minut do dodania. Może być ujemna do odjęcia minut. <p> **Liczba parametrów**: 3 <p> **Nazwa**: Format <p> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addhours|Dodaje całkowitą liczbę godzin do sygnatury czasowej ciągu przekazany. Liczba godzin może być liczbą dodatnią lub ujemną. Domyślnie wynik jest ciągiem w formacie ISO 8601 ("e"), ile specyfikator formatu. Na przykład: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: znacznik czasu <p> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę. <p> **Liczba parametrów**: 2 <p> **Nazwa**: godziny <p> **Opis elementu**: wymagane. Liczba godzin do dodania. Może być ujemna do odjęcia godzin. <p> **Liczba parametrów**: 3 <p> **Nazwa**: Format <p> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addDays|Dodaje liczby całkowitej określającej liczbę dni do sygnatury czasowej ciągu przekazany. Liczba dni korzystania z może być liczbą dodatnią lub ujemną. Domyślnie wynik jest ciągiem w formacie ISO 8601 ("e"), ile specyfikator formatu. Na przykład: `2015-03-13T13:27:36Z`: <p>`adddays('2015-03-15T13:27:36Z', -2)` <p> **Liczba parametrów**: 1 <p> **Nazwa**: znacznik czasu <p> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę. <p> **Liczba parametrów**: 2 <p> **Nazwa**: dni <p> **Opis elementu**: wymagane. Liczba dni do dodania. Może być ujemna do odjęcia dni. <p> **Liczba parametrów**: 3 <p> **Nazwa**: Format <p> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|formatDateTime|Zwraca ciąg w formacie daty. Domyślnie wynik jest ciągiem w formacie ISO 8601 ("e"), ile specyfikator formatu. Na przykład: `2015-03-15T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Liczba parametrów**: 1 <p> **Nazwa**: Data <p> **Opis elementu**: wymagane. Ciąg, który zawiera datę. <p> **Liczba parametrów**: 2 <p> **Nazwa**: Format <p> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|startOfHour|Zwraca początek godziny do sygnatury czasowej ciągu przekazany. Na przykład `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Jest to ciąg, który zawiera godzinę.<br /><br />**Liczba parametrów**: 2<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|startOfDay|Zwraca początek dnia do sygnatury czasowej ciągu przekazany. Na przykład `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Jest to ciąg, który zawiera godzinę.<br /><br />**Liczba parametrów**: 2<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").| 
|startOfMonth|Zwraca początek miesiąca do sygnatury czasowej ciągu przekazany. Na przykład `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Jest to ciąg, który zawiera godzinę.<br /><br />**Liczba parametrów**: 2<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").| 
|DayOfWeek|Zwraca dzień tygodnia składnika sygnaturę czasową ciągu.  Niedziela wynosi 0, od poniedziałku 1 i tak dalej. Na przykład `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Jest to ciąg, który zawiera godzinę.| 
|DayOfMonth|Zwraca dzień miesiąca składnika sygnaturę czasową ciągu. Na przykład `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Jest to ciąg, który zawiera godzinę.| 
|Dzieńroku|Zwraca dzień roku składnika sygnaturę czasową ciągu. Na przykład `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Jest to ciąg, który zawiera godzinę.| 
|znaczniki osi|Zwraca właściwości ciągu sygnatura czasowa to znaczniki osi. Na przykład `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Jest to ciąg, który zawiera godzinę.| 
  
### <a name="workflow-functions"></a>Funkcje przepływu pracy  

Te funkcje pomocne w uzyskaniu informacji na temat sam przepływ pracy w czasie wykonywania.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|listCallbackUrl|Zwraca ciąg, który umożliwia wywołanie invoke, trigger lub akcji. <p> **Uwaga**: tej funkcji można używać tylko w **httpWebhook** i **apiConnectionWebhook**nie w **ręczne**, **cyklu**, **http**, lub **apiConnection**. <p>Na przykład `listCallbackUrl()` funkcja zwraca: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|przepływ pracy|Ta funkcja udostępnia wszystkie szczegóły przepływu w czasie wykonywania. <p> Są dostępne właściwości w obiekcie przepływu pracy: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> Wartość `run` właściwość jest obiekt z następującymi właściwościami: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Zobacz [interfejsu API Rest](http://go.microsoft.com/fwlink/p/?LinkID=525617) szczegółowe informacje na temat tych właściwości.<p> Na przykład, aby uzyskać nazwę bieżący element, należy użyć `"@workflow().run.name"` wyrażenia. |

## <a name="next-steps"></a>Kolejne kroki

[Wyzwalacze i akcje przepływu pracy](logic-apps-workflow-actions-triggers.md)
