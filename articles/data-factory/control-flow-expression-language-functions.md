---
title: Wyrażenie i funkcje w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o wyrażeniach i funkcje, których można używać w tworzenie jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 1625b37a41082f8536d103701b1356a13a5dd837
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Wyrażeń i funkcji w fabryce danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-functions-variables.md)
> * [Wersja 2 — wersja zapoznawcza](control-flow-expression-language-functions.md)

Ten artykuł zawiera szczegółowe informacje o wyrażeniach i funkcje obsługiwane przez usługi fabryka danych Azure (wersja 2). 

## <a name="introduction"></a>Wprowadzenie
Wartości JSON w definicji może być literał lub wyrażeń, które są oceniane w czasie wykonywania. Na przykład:  
  
```json
"name": "value"
```

 (lub)  
  
```json
"name": "@pipeline().parameters.password"
```


> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [funkcje i zmienne w V1 fabryki danych](v1/data-factory-functions-variables.md).


## <a name="expressions"></a>Wyrażenia  
Wyrażenia mogą występować w dowolnym miejscu w wartości ciągu JSON i zawsze powoduje inną wartość JSON. Jeśli wartość JSON jest wyrażenie, treść wyrażenia został wyodrębniony przez usunięcie znaku (@). Jeśli wymagane jest literałem, która rozpoczyna się od @, należy użyć znaków ucieczki przy użyciu@. W poniższych przykładach pokazano, jak są analizowane wyrażenia.  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"Parametry"|Znaki "parameters" są zwracane.|  
|"parametry [1]"|Zwracane są znaki "parametry [1]".|  
|"@@"|Ciąg 1 znaku, który zawiera "@" jest zwracany.|  
|" @"|Ciąg znaków 2, który zawiera "@" jest zwracany.|  
  
 Wyrażenia może również wystąpić wewnątrz ciągi, przy użyciu funkcji o nazwie *ciągu interpolacji* gdzie wyrażenia są ujęte w `@{ ... }`. Na przykład: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Przy użyciu ciągu interpolacji, wynik jest zawsze ciąg. Powiedz zdefiniowana `myNumber` jako `42` i `myString` jako `foo`:  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"@pipeline(). parameters.myString"| Zwraca `foo` jako ciąg.|  
|"@{pipeline().parameters.myString}"| Zwraca `foo` jako ciąg.|  
|"@pipeline(). parameters.myNumber"| Zwraca `42` jako *numer*.|  
|"@{pipeline().parameters.myNumber}"| Zwraca `42` jako *ciąg*.|  
|"Odpowiedź brzmi: @{potoku.parameters.myNumber ()}"| Zwraca ciąg `Answer is: 42`.|  
|"@concat(" Odpowiedź brzmi: ", string(pipeline().parameters.myNumber))"| Zwraca ciąg `Answer is: 42`|  
|"Odpowiedź brzmi: @@ {potoku.parameters.myNumber ()}"| Zwraca ciąg `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Przykłady

#### <a name="a-dataset-with-a-parameter"></a>Zestaw danych z parametrem
W poniższym przykładzie BlobDataset przyjmuje parametr o nazwie **ścieżki**. Jego wartość jest używana do ustawiania wartości dla **folderPath** właściwości przy użyciu następujących wyrażeń: `@{dataset().path}`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Potok z parametrem
W poniższym przykładzie przyjmuje potoku **inputPath** i **outputPath** parametrów. **Ścieżki** obiektu blob sparametryzowane zestawu danych jest ustawiony za pomocą wartości tych parametrów. Składnia używana w tym miejscu: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>Funkcje  
 Można wywołać funkcji w wyrażeniach. Poniższe sekcje zawierają informacje na temat funkcji, których można użyć w wyrażeniu.  

## <a name="string-functions"></a>Funkcje ciągów  
 Następujące funkcje dotyczą tylko ciągi. Umożliwia także wiele funkcji kolekcji ciągów.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|concat|Łączy ze sobą dowolną liczbę ciągów. Na przykład, jeśli jest parametr1 `foo,` zwróci następujące wyrażenie `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: ciąg *n*<br /><br /> **Opis elementu**: wymagane. Ciągi są łączone w pojedynczy ciąg.|  
|substring|Zwraca podzbiór znaków z ciągu. Na przykład poniższe wyrażenie:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> Zwraca:<br /><br /> `foo`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, z którego jest pobierany podciąg.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: indeks początkowy<br /><br /> **Opis elementu**: wymagane. Indeks, gdzie podciąg rozpoczyna się w parametrze 1.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: długość<br /><br /> **Opis elementu**: wymagane. Długość podciąg.|  
|Zamień|Zamienia ciąg dany ciąg znaków. Na przykład, wyrażenie:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> Zwraca:<br /><br /> `the new string`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane.  Jeśli parametr 2 znajduje się w parametrze 1, ciąg, który jest przeszukiwany parametru 2 i zaktualizowane z parametrem 3.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: stary ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, aby zastąpić parametr 3 po znalezieniu dopasowania w parametrze 1<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: nowe parametry<br /><br /> **Opis elementu**: wymagane. Ciąg, który jest używany do zastępowania ciągów w parametrze 2, po znalezieniu dopasowania w parametrze 1.|  
|Identyfikator GUID| Generuje globalnie unikatowy ciąg (alias. guid). Na przykład następujące dane wyjściowe zostanie wygenerowany `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Specyfikator formatu jednej, która wskazuje [sposób formatowania wartość tego identyfikatora Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Format parametru może być "N", "D", "B", "P" lub "X". Jeśli format nie jest podany, "D" jest używany.|  
|toLower|Konwertuje ciąg na małe litery. Na przykład następujące zwraca `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg do przekonwertowania na niższych wielkości liter. Jeśli znak w ciągu nie ma odpowiednika małe litery, jest uwzględniony bez zmian w zwracany ciąg.|  
|toUpper|Konwertuje ciąg na wielkie litery. Na przykład poniższe wyrażenie zwraca `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg do przekonwertowania na górnym wielkości liter. Jeśli znak w ciągu nie ma odpowiednika wielkie litery, jest uwzględniony bez zmian w zwracany ciąg.|  
|indexof|Znajdź insensitively indeks wartości w przypadku ciągu. Na przykład poniższe wyrażenie zwraca `7`: `indexof('hello, world.', 'world')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Wartość do wyszukiwania indeksu.|  
|lastindexof|Znajdź ostatni indeks wartości w przypadku ciągu insensitively. Na przykład poniższe wyrażenie zwraca `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Wartość do wyszukiwania indeksu.|  
|StartsWith|Sprawdza, czy ciąg rozpoczyna się od liter wartość insensitively. Na przykład poniższe wyrażenie zwraca `true`: `lastindexof('hello, world', 'hello')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Mogą rozpoczynać się wartość ciągu.|  
|EndsWith|Sprawdza, czy ciąg kończy się wyrazem przypadku wartość insensitively. Na przykład poniższe wyrażenie zwraca `true`: `lastindexof('hello, world', 'world')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, który może zawierać wartości.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Wartość ciągu mogą kończyć się.|  
|split|Dzieli ciągu za pomocą separatora. Na przykład poniższe wyrażenie zwraca `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, który jest podzielona.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Separator.|  
  
  
## <a name="collection-functions"></a>Kolekcja funkcji  
 Funkcje te działają na kolekcji, takie jak tablic ciągów i czasami słowników.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|zawiera|Zwraca wartość PRAWDA, jeśli słownik zawiera listę kluczy, zawiera wartość lub ciąg zawiera podciąg. Na przykład poniższe wyrażenie zwraca `true:``contains('abacaba','aca')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: W kolekcji<br /><br /> **Opis elementu**: wymagane. Kolekcja do przeszukania.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: obiekt Find<br /><br /> **Opis elementu**: wymagane. Obiekt, aby znaleźć wewnątrz **w kolekcji**.|  
|długość|Zwraca liczbę elementów w tablicy lub ciągu. Na przykład poniższe wyrażenie zwraca `3`:  `length('abc')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji<br /><br /> **Opis elementu**: wymagane. Kolekcja można pobrać długości.|  
|pusty|Zwraca wartość PRAWDA, jeśli obiekt, tablicy lub ciągu jest pusty. Na przykład poniższe wyrażenie zwraca `true`:<br /><br /> `empty('')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji<br /><br /> **Opis elementu**: wymagane. Kolekcja, aby sprawdzić, czy jest pusta.|  
|część wspólną|Zwraca pojedynczą tablicę lub obiektu o wspólnych elementach między tablicami lub przekazywane do niej obiekty. Na przykład, funkcja zwraca `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametry funkcji może być zestaw obiektów lub zestaw tablic (nie kombinacja jego). Jeśli występują dwa obiekty o takiej samej nazwie, ostatni obiekt o tej nazwie pojawia się w końcowym obiektu.<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: kolekcja *n*<br /><br /> **Opis elementu**: wymagane. Kolekcje do oceny. Obiekt musi być we wszystkich zbiorach przekazany do są wyświetlane w wynikach.|  
|Unii|Zwraca pojedynczą tablicę lub obiektu z wszystkich elementów, które w tablicy lub obiekt są przekazywane do niego. Na przykład funkcja zwraca `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametry funkcji może być zestaw obiektów lub zestaw tablic (nie kombinacja jego). Jeśli występują dwa obiekty o takiej samej nazwie w ostateczne dane wyjściowe, ostatni obiekt o tej nazwie pojawia się w końcowym obiektu.<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: kolekcja *n*<br /><br /> **Opis elementu**: wymagane. Kolekcje do oceny. Obiekt, który pojawia się w dowolnej kolekcji zostanie wyświetlony w wyniku.|  
|pierwszy|Zwraca pierwszy element tablicy lub ciągu przekazany. Na przykład, funkcja zwraca `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji<br /><br /> **Opis elementu**: wymagane. Podjęcie pierwszy obiekt z kolekcji.|  
|ostatni|Zwraca ostatni element w tablicy lub ciągu przekazany. Na przykład, funkcja zwraca `3`:<br /><br /> `last('0123')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji<br /><br /> **Opis elementu**: wymagane. Kolekcja podjęcie ostatni obiekt z.|  
|podejmij|Zwraca pierwszy **liczba** przekazano elementów z tablicy lub ciągu, na przykład ta funkcja zwraca `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji<br /><br /> **Opis elementu**: wymagane. Kolekcja podjęcie pierwszy **liczba** obiektów z.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: liczba<br /><br /> **Opis elementu**: wymagane. Liczba obiektów z **kolekcji**. Musi być dodatnią liczbą całkowitą.|  
|Pomiń|Zwraca elementy w tablicy, zaczynając od indeksu **liczba**, na przykład ta funkcja zwraca `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji<br /><br /> **Opis elementu**: wymagane. Kolekcja, aby pominąć pierwszy **liczba** obiektów z.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: liczba<br /><br /> **Opis elementu**: wymagane. Liczba obiektów, aby usunąć z przodu **kolekcji**. Musi być dodatnią liczbą całkowitą.|  
  
## <a name="logical-functions"></a>Funkcje logiczne  
 Funkcje te są przydatne w warunkach, może służyć do oceny dowolnego typu logiki.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|równa się|Zwraca wartość PRAWDA, jeśli dwie wartości są równe. Na przykład, jeśli parameter1 foo, poniższe wyrażenie zwróci `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt o 1<br /><br /> **Opis elementu**: wymagane. Obiekt do porównania z **2 obiektu**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis elementu**: wymagane. Obiekt do porównania z **1 obiektu**.|  
|mniej|Zwraca wartość PRAWDA, jeśli pierwszy argument jest mniejszy od drugiego. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład poniższe wyrażenie zwraca `true`:  `less(10,100)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt o 1<br /><br /> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest mniejsza niż **2 obiektu**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż **1 obiektu**.|  
|lessOrEquals|Zwraca wartość PRAWDA, jeśli pierwszy argument jest mniejsze niż lub równe drugiemu. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład poniższe wyrażenie zwraca `true`:  `lessOrEquals(10,10)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt o 1<br /><br /> **Opis elementu**: wymagane. Obiekt do Sprawdź, czy jest mniejsza lub równa **2 obiektu**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż lub równa **1 obiektu**.|  
|większa|Zwraca wartość PRAWDA, jeśli pierwszy argument jest większa od drugiej. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład poniższe wyrażenie zwraca `false`:  `greater(10,10)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt o 1<br /><br /> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż **2 obiektu**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest mniejsza niż **1 obiektu**.|  
|greaterOrEquals|Zwraca wartość PRAWDA, jeśli pierwszy argument jest większe lub równe drugiemu. Uwaga: wartości może być tylko typu integer, float lub ciąg. Na przykład poniższe wyrażenie zwraca `false`:  `greaterOrEquals(10,100)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt o 1<br /><br /> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest większa niż lub równa **2 obiektu**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis elementu**: wymagane. Obiekt, aby sprawdzić, czy jest mniejsze niż lub równe **1 obiektu**.|  
|i|Zwraca wartość PRAWDA, jeśli spełnione są obydwa parametry. Oba argumenty muszą być wartości logiczne. Zwraca następujące `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość logiczna 1<br /><br /> **Opis elementu**: wymagane. Pierwszy argument, który musi być `true`.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: wartość logiczna 2<br /><br /> **Opis elementu**: wymagane. Drugi argument musi być `true`.|  
|lub|Zwraca wartość PRAWDA, jeśli jest spełniony jeden z parametrów. Oba argumenty muszą być wartości logiczne. Zwraca następujące `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość logiczna 1<br /><br /> **Opis elementu**: wymagane. Pierwszy argument, który może być `true`.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: wartość logiczna 2<br /><br /> **Opis elementu**: wymagane. Drugi argument może być `true`.|  
|nie|Zwraca wartość true, jeśli parametr jest `false`. Oba argumenty muszą być wartości logiczne. Zwraca następujące `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość logiczna<br /><br /> **Opis elementu**: zwraca wartość true, jeśli parametr jest `false`. Oba argumenty muszą być wartości logiczne. Zwraca następujące `true`:  `not(contains('200 Success','Fail'))`|  
|if|Zwraca określoną wartość na podstawie, gdy wyniki dostarczonego wyrażenia w `true` lub `false`.  Na przykład następujące zwraca `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wyrażenie<br /><br /> **Opis elementu**: wymagane. Wartość logiczna, która określa wartość, która jest zwracana przez wyrażenie.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: True<br /><br /> **Opis elementu**: wymagane. Wartość zwracana, gdy wyrażenie jest `true`.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: False<br /><br /> **Opis elementu**: wymagane. Wartość zwracana, gdy wyrażenie jest `false`.|  
  
## <a name="conversion-functions"></a>Funkcje konwersji  
 Funkcje te służą do konwersji między każdego typu natywnego w języku:  
  
-   ciąg  
  
-   integer  
  
-   Float  
  
-   wartość logiczna  
  
-   Tablice  
  
-   słowników  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|int|Parametr należy przekonwertować na liczbę całkowitą. Na przykład poniższe wyrażenie zwraca 100 jako liczbę zamiast ciągu:  `int('100')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis elementu**: wymagane. Wartość, która jest konwertowana na liczbę całkowitą.|  
|ciąg|Parametr jest skonwertowana do ciągu. Na przykład poniższe wyrażenie zwraca `'10'`: `string(10)` można również przekonwertować obiektu na ciąg, na przykład, jeśli **foo** parametr jest obiekt z jedną właściwość `bar : baz`, a następnie spowoduje następujące Zwraca `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis elementu**: wymagane. Wartość, która jest konwertowana na ciąg.|  
|JSON|Konwertuj parametr na wartość typu JSON. Jest przeciwieństwem string(). Na przykład poniższe wyrażenie zwraca `[1,2,3]` jako tablica zamiast ciągu:<br /><br /> `parse('[1,2,3]')`<br /><br /> Podobnie ciąg można przekonwertować na obiekt. Na przykład `json('{"bar" : "baz"}')` zwraca:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg, który jest konwertowany na wartość typu macierzystego.<br /><br /> Funkcja json obsługuje również dane wejściowe xml. Na przykład, wartość parametru:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> jest konwertowana na następujących json:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Float|Liczba zmiennoprzecinkowa przekonwertować argumentu parametru. Na przykład poniższe wyrażenie zwraca `10.333`:  `float('10.333')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis elementu**: wymagane. Wartość, która jest konwertowana na liczba zmiennoprzecinkowa.|  
|bool|Konwersji parametru na wartość logiczną. Na przykład poniższe wyrażenie zwraca `false`:  `bool(0)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis elementu**: wymagane. Wartość, która jest konwertowana na wartość logiczną.|  
|połączenie|Zwraca pierwszy obiekt zerowy przekazanych argumentów. Uwaga: pusty ciąg nie jest pusty. Na przykład, jeśli nie zdefiniowano parametrów 1 i 2, to zwraca `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: obiekt*n*<br /><br /> **Opis elementu**: wymagane. Sprawdź, czy obiekty `null`.|  
|base64|Zwraca reprezentację ciągu wejściowego base64. Na przykład poniższe wyrażenie zwraca `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String 1<br /><br /> **Opis elementu**: wymagane. Ciąg do zakodowania w reprezentację base64.|  
|base64ToBinary|Zwraca wartość binarna reprezentacja ciągu w kodowaniu base64. Na przykład poniższe wyrażenie zwraca binarna reprezentacja ciągu niektórych: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg kodowany w formacie base64.|  
|base64ToString|Zwraca reprezentację ciągu based64 zakodowany ciąg. Na przykład poniższe wyrażenie zwraca niektórych ciąg: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg kodowany w formacie base64.|  
|Binarny|Zwraca wartość binarna reprezentacja wartości.  Na przykład poniższe wyrażenie zwraca to binarna reprezentacja niektóre parametry: `binary(‘some string’).`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis elementu**: wymagane. Wartość, która jest konwertowana na format binarny.|  
|dataUriToBinary|Zwraca wartość binarna reprezentacja identyfikatora URI danych. Na przykład poniższe wyrażenie zwraca binarna reprezentacja niektóre parametry: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Identyfikator URI do przekonwertowania na binarna reprezentacja danych.|  
|dataUriToString|Zwraca reprezentację ciągu identyfikatora URI danych. Na przykład poniższe wyrażenie zwraca niektórych ciąg: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br />**Opis elementu**: wymagane. Identyfikator URI do przekonwertowania na ciąg reprezentujący dane.|  
|dataUri|Zwraca identyfikator URI wartości danych. Na przykład poniższe wyrażenie zwraca dane: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: wartość<br /><br />**Opis elementu**: wymagane. Wartość można przekonwertować na identyfikator URI danych.|  
|decodeBase64|Zwraca reprezentację ciągu wejściowego based64 w ciągu. Na przykład poniższe wyrażenie zwraca `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: zwraca reprezentację ciągu wejściowego based64 w ciągu.|  
|encodeuricomponent —|Adres URL specjalne ciąg, który jest przekazywany w. Na przykład poniższe wyrażenie zwraca `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg niezabezpieczony adres URL znaki ucieczki.|  
|decodeuricomponent —|NZ — adres URL-specjalne ciąg, który jest przekazywany w. Na przykład poniższe wyrażenie zwraca `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. Ciąg do zdekodowania niezabezpieczony adres URL znaki z.|  
|decodeDataUri|Zwraca wartość binarna reprezentacja danych wejściowych w postaci ciągu identyfikatora URI. Na przykład poniższe wyrażenie zwraca reprezentacja binarna `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis elementu**: wymagane. DataURI zdekodować do reprezentacja binarna.|  
|uriComponent|Zwraca identyfikator URI zakodowany reprezentację wartości. Na przykład poniższe wyrażenie zwraca `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Szczegóły parametru: Liczba: 1, nazwa: ciąg, opis: wymagane. Ciąg, który jest kodowany identyfikator URI.|  
|uriComponentToBinary|Zwraca ciąg kodowany w formacie to binarna reprezentacja identyfikatora URI. Na przykład poniższe wyrażenie zwraca to binarna reprezentacja `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br />**Opis elementu**: wymagane. Ciąg kodowany w formacie identyfikatora URI.|  
|uriComponentToString|Zwraca ciąg kodowany w formacie reprezentację ciągu identyfikatora URI. Na przykład poniższe wyrażenie zwraca `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: ciąg<br /><br />**Opis elementu**: wymagane. Ciąg kodowany w formacie identyfikatora URI.|  
|xml|Zwraca reprezentację xml wartości. Na przykład poniższe wyrażenie zwraca zawartości xml reprezentowany przez `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Funkcja xml obsługuje również wprowadzanie obiekt JSON. Na przykład parametr `{ "abc": "xyz" }` jest konwertowana na zawartości xml `\<abc>xyz\</abc>`<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: wartość<br /><br />**Opis elementu**: wymagane. Wartość do przekonwertowania na format XML.|  
|wyrażenie XPath|Zwraca tablicę węzłów xml odpowiadającym wyrażenie xpath wartości, która daje w wyniku wyrażenia xpath.<br /><br />  **Przykład 1**<br /><br /> Przyjęto założenie, że wartość parametru "p1" jest reprezentację ciągu następujący kod XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ten kod: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> zwróci<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> natomiast<br /><br /> 2. Ten kod: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> zwróci<br /><br /> `13`<br /><br /> <br /><br /> **Przykład 2**<br /><br /> Podane następującą zawartość XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Ten kod: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> lub<br /><br /> 2. Ten kod: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Zwraca<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> i<br /><br /> 3. Ten kod: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Zwraca<br /><br /> ``bar``<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: Xml<br /><br />**Opis elementu**: wymagane. Kod XML, na którym można oszacować wyrażenia XPath.<br /><br /> **Liczba parametrów**: 2<br /><br />**Nazwa**: XPath<br /><br />**Opis elementu**: wymagane. Wyrażenie XPath do oceny.|  
|tablica|Parametr można przekonwertować na typ tablicy.  Na przykład poniższe wyrażenie zwraca `["abc"]`: `array('abc')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis elementu**: wymagane. Wartość, która jest konwertowany na tablicę.|
|createArray|Tworzy tablicę z parametrów.  Na przykład poniższe wyrażenie zwraca `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Liczba parametrów**: 1... n<br /><br /> **Nazwa**: wszystkie n<br /><br /> **Opis elementu**: wymagane. Wartości do łączenia w tablicy.|

## <a name="math-functions"></a>Funkcje matematyczne  
 Te funkcje mogą być używane dla obu typów liczb: **liczb całkowitych** i **przesunięty**.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|dodaj|Zwraca wynik dodanie dwóch liczb. Na przykład, funkcja zwraca `20.333`:  `add(10,10.333)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Summand 1<br /><br /> **Opis elementu**: wymagane. Numer, aby dodać do **Summand 2**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Summand 2<br /><br /> **Opis elementu**: wymagane. Numer, aby dodać do **Summand 1**.|  
|Sub|Zwraca wynik odejmowania dwóch liczb. Na przykład, funkcja zwraca: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Minuend<br /><br /> **Opis elementu**: wymagane. Liczba który **Subtrahend** zostanie usunięty z.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Subtrahend<br /><br /> **Opis elementu**: wymagane. Numer do usunięcia z **Minuend**.|  
|mul|Zwraca wynik iloczyn dwóch liczb. Na przykład następujące zwraca `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Multiplicand 1<br /><br /> **Opis elementu**: wymagane. Liczbę Aby pomnożyć **Multiplicand 2** z.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Multiplicand 2<br /><br /> **Opis elementu**: wymagane. Liczbę Aby pomnożyć **Multiplicand 1** z.|  
|div|Zwraca wynik ilorazu dwóch liczb. Na przykład następujące zwraca `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: dzielna<br /><br /> **Opis elementu**: wymagane. Numer do dzielenia przez **dzielnik**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: dzielnik.<br /><br /> **Opis elementu**: wymagane. Numer do dzielenia **dzielna** przez.|  
|mod|Zwraca wynik resztę po ilorazu dwóch liczb (modulo). Na przykład poniższe wyrażenie zwraca `2`:<br /><br /> `mod(10,4)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: dzielna<br /><br /> **Opis elementu**: wymagane. Numer do dzielenia przez **dzielnik**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: dzielnik.<br /><br /> **Opis elementu**: wymagane. Numer do dzielenia **dzielna** przez. Po podziału pozostałe jest zajęta.|  
|min.|Istnieją dwa różne wzorce wywoływania tej funkcji: `min([0,1,2])` tutaj min pobiera tablicę. To wyrażenie zwraca `0`. Alternatywnie, może to trwać listę wartości rozdzielaną przecinkami: `min(0,1,2)` funkcja zwraca wartość 0. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą musi zawierać tylko cyfry w nim.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji lub wartość<br /><br /> **Opis elementu**: wymagane. Może być ona tablicy wartości, aby znaleźć wartość minimalną lub wartość pierwszego zestawu.<br /><br /> **Liczba parametrów**: 2... *n*<br /><br /> **Nazwa**: wartość *n*<br /><br /> **Opis elementu**: opcjonalne. Jeśli wartość jest pierwszym parametrem, przekazuj dodatkowe wartości i minimum wszystkich wartości przekazanych są zwracane.|  
|maks.|Istnieją dwa różne wzorce wywoływania tej funkcji:  `max([0,1,2])`<br /><br /> W tym miejscu max pobiera tablicę. To wyrażenie zwraca `2`. Alternatywnie, może to trwać listę wartości rozdzielaną przecinkami: `max(0,1,2)` ta funkcja zwraca wartość 2. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą musi zawierać tylko cyfry w nim.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: kolekcji lub wartość<br /><br /> **Opis elementu**: wymagane. Może być ona tablicy wartości, aby znaleźć maksymalną wartość lub wartość pierwszego zestawu.<br /><br /> **Liczba parametrów**: 2... *n*<br /><br /> **Nazwa**: wartość *n*<br /><br /> **Opis elementu**: opcjonalne. Jeśli wartość jest pierwszym parametrem, przekazuj dodatkowe wartości, a maksimum wszystkich wartości przekazane zostaną zwrócone.|  
|Zakres| Generuje tablicę liczby całkowite począwszy od liczby i długość tablicy zwracane. Na przykład, funkcja zwraca `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: indeks początkowy<br /><br /> **Opis elementu**: wymagane. Jest pierwszej liczby całkowitej w tablicy.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: liczba<br /><br /> **Opis elementu**: wymagane. Liczba liczb całkowitych, które znajdują się w tablicy.|  
|RAND| Generuje losową liczbę całkowitą określonego zakresu (włącznie z obu końców. Na przykład może to zwrócić `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: minimalna<br /><br /> **Opis elementu**: wymagane. Najniższa liczba całkowita, która może zostać zwrócone.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: maksymalna<br /><br /> **Opis elementu**: wymagane. Największa liczba całkowita, która może zostać zwrócone.|  
  
## <a name="date-functions"></a>Funkcje daty  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|utcnow|Zwraca bieżącą sygnaturę czasową jako ciąg. Na przykład `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addSeconds|Dodaje całkowitą liczbę sekund do sygnatury czasowej ciągu przekazany. Liczba sekund może być liczbą dodatnią lub ujemną. Wynik jest na ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że specyfikator formatu. Na przykład `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: sekund<br /><br /> **Opis elementu**: wymagane. Liczba sekund do dodania. Może być ujemna do odjęcia sekund.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addminutes|Dodaje całkowitą liczbę minut do sygnatury czasowej ciągu przekazany. Liczba minut, może być liczbą dodatnią lub ujemną. Wynik jest na ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że specyfikator formatu. Na przykład `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: minut<br /><br /> **Opis elementu**: wymagane. Liczba minut do dodania. Może być ujemna do odjęcia minut.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addhours|Dodaje całkowitą liczbę godzin do sygnatury czasowej ciągu przekazany. Liczba godzin może być liczbą dodatnią lub ujemną. Wynik jest na ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że specyfikator formatu. Na przykład `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: godziny<br /><br /> **Opis elementu**: wymagane. Liczba godzin do dodania. Może być ujemna do odjęcia godzin.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|addDays|Dodaje liczby całkowitej określającej liczbę dni do sygnatury czasowej ciągu przekazany. Liczba dni korzystania z może być liczbą dodatnią lub ujemną. Wynik jest na ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że specyfikator formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis elementu**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: dni<br /><br /> **Opis elementu**: wymagane. Liczba dni do dodania. Może być ujemna do odjęcia dni.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  
|formatDateTime|Zwraca ciąg w formacie daty. Wynik jest na ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że specyfikator formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Data<br /><br /> **Opis elementu**: wymagane. Ciąg, który zawiera datę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Format<br /><br /> **Opis elementu**: opcjonalne. Albo [pojedynczy znak specyfikator formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) wskazujący sposób formatowania wartości sygnatura czasowa. Jeśli format nie jest podany, jest używany w formacie ISO 8601 ("o").|  

## <a name="next-steps"></a>Kolejne kroki
Listę można używać w wyrażeniach zmiennych systemowych, zobacz [zmienne systemowe](control-flow-system-variables.md).
