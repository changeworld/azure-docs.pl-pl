---
title: Wyrażenia i funkcje w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o wyrażeniach i funkcje, które można użyć podczas tworzenia jednostek usługi fabryka danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 4c51974498539a0305312d6501bcfa9ebc3b2e88
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573553"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Wyrażenia i funkcje w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-functions-variables.md)
> * [Bieżąca wersja](control-flow-expression-language-functions.md)

Ten artykuł zawiera szczegółowe informacje o wyrażeniach i funkcji obsługiwanych przez usługę Azure Data Factory. 

## <a name="introduction"></a>Wprowadzenie
Wartości JSON w definicji może być literałem lub wyrażeń, które są oceniane w czasie wykonywania. Na przykład:  
  
```json
"name": "value"
```

 (lub)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Wyrażenia  
Wyrażenia może występować w dowolnym miejscu w wartości ciągu JSON i zawsze skutkuje inną wartość JSON. Jeśli wartość JSON jest wyrażeniem, treść wyrażenia jest wyodrębniany, usuwając znaku (\@). Jeśli potrzebne jest ciągiem literału, zaczynający się od \@, należy użyć znaków ucieczki, za pomocą \@ \@. W poniższych przykładach pokazano, jak są obliczane wyrażenia.  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"parameters"|Znaki "parameters" są zwracane.|  
|"parametry [1]"|Zwracane są znaki "parameters [1]".|  
|"\@\@"|Ciąg znaków: 1, który zawiera "\@" jest zwracany.|  
|" \@"|Ciąg znaków 2, który zawiera " \@" jest zwracany.|  
  
 Wyrażenia może również wystąpić wewnątrz ciągów, przy użyciu funkcji o nazwie *Interpolacja ciągów* którym określeń są opakowane w `@{ ... }`. Na przykład: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Przy użyciu interpolacji ciągu, wynik to zawsze ciąg. Powiedz, zdefiniowana `myNumber` jako `42` i `myString` jako `foo`:  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Zwraca `foo` jako ciąg.|  
|"\@{pipeline().parameters.myString}"| Zwraca `foo` jako ciąg.|  
|"\@pipeline().parameters.myNumber"| Zwraca `42` jako *numer*.|  
|"\@{pipeline().parameters.myNumber}"| Zwraca `42` jako *ciąg*.|  
|"Odpowiedź brzmi: @{potoku ().parameters.myNumber}"| Zwraca ciąg `Answer is: 42`.|  
|"\@concat (" odpowiedź brzmi: ", string(pipeline().parameters.myNumber))"| Zwraca ciąg `Answer is: 42`|  
|"Odpowiedź brzmi: \@ \@{potoku ().parameters.myNumber}"| Zwraca ciąg `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Przykłady

#### <a name="a-dataset-with-a-parameter"></a>Za pomocą parametru zestawu danych
W poniższym przykładzie BlobDataset przyjmuje parametr o nazwie **ścieżki**. Jego wartość jest używana do ustawiania wartości dla **folderPath** właściwości przy użyciu wyrażenia: `dataset().path`. 

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

#### <a name="a-pipeline-with-a-parameter"></a>Potok za pomocą parametru
W poniższym przykładzie potok przyjmuje **inputPath** i **outputPath** parametrów. **Ścieżki** sparametryzowane obiektu blob dataset są ustawiane przy użyciu wartości tych parametrów. Składnia używana w tym miejscu: `pipeline().parameters.parametername`. 

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
|concat|Łączy ze sobą dowolną liczbę ciągów. Na przykład parametr parameter1 ma `foo,` zwróci następujące wyrażenie `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: Ciąg *n*<br /><br /> **Opis**: Wymagany. Ciągi do połączenia w pojedynczy ciąg.|  
|podciąg|Zwraca podciąg znaków z ciągu. Na przykład następujące wyrażenie:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> Zwraca:<br /><br /> `foo`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, z którego został pobrany podciąg.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Indeks początkowy<br /><br /> **Opis**: Wymagany. Indeks, z którym podciąg rozpoczyna się w parametrze 1.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Długość<br /><br /> **Opis**: Wymagany. Długość podciągu.|  
|Zastąp|Zastępuje ciąg podanym ciągiem. Na przykład, wyrażenie:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> Zwraca:<br /><br /> `the new string`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: ciąg<br /><br /> **Opis**: Wymagany.  Jeśli parametr 2 zostanie znaleziony w parametrze 1, ciąg, który jest wyszukiwany dla parametru 2 i aktualizowany przy użyciu parametru 3.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Stary ciąg<br /><br /> **Opis**: Wymagany. Ciąg do zastąpienia przy użyciu parametru 3, gdy w parametrze 1 zostanie znalezione dopasowanie<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Nowy ciąg<br /><br /> **Opis**: Wymagany. Ciąg, który jest używany do zastępowania ciągu w parametrze 2, gdy w parametrze 1 zostanie znalezione dopasowanie.|  
|Identyfikator GUID| Generuje globalnie unikatowy ciąg (alias. guid). Na przykład następujące dane wyjściowe zostanie wygenerowany `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Pojedynczy specyfikator formatu wskazującą [sposób formatowania wartości tego identyfikatora Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Parametr formatu może być "N", "D", "B", "P" lub "X". Jeśli format nie zostanie podany, "D" jest używany.|  
|toLower|Konwertuje ciąg na małe litery. Na przykład następujące zwroty `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg do przekonwertowania na wielkość liter w wyrazie niższe. Jeśli znak w ciągu nie ma odpowiadającej małej litery, jest uwzględnione bez zmian w zwracanym ciągu.|  
|toUpper|Konwertuje ciąg na wielkie litery. Na przykład poniższe wyrażenie zwraca `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg do przekonwertowania na górnym wielkości liter. Jeśli znak w ciągu nie ma odpowiadającej wielkiej litery, jest uwzględnione bez zmian w zwracanym ciągu.|  
|indexof|Znajdź insensitively indeks wartości w przypadku ciągu. Na przykład poniższe wyrażenie zwraca `7`: `indexof('hello, world.', 'world')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, który może zawierać wartość.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Wartość do wyszukiwania indeksu.|  
|lastindexof|Znajdź ostatni indeks wartości w przypadku ciągu insensitively. Na przykład poniższe wyrażenie zwraca `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, który może zawierać wartość.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Wartość do wyszukiwania indeksu.|  
|StartsWith|Sprawdza, czy ciąg rozpoczyna się od przypadku wartość insensitively. Na przykład poniższe wyrażenie zwraca `true`: `startswith('hello, world', 'hello')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, który może zawierać wartość.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Wartość ciągu może się rozpoczynać.|  
|EndsWith|Sprawdza, czy ciąg kończy się sprawą wartość insensitively. Na przykład poniższe wyrażenie zwraca `true`: `endswith('hello, world', 'world')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, który może zawierać wartość.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Wartość ciągu mogą kończyć się znakiem.|  
|split|Dzieli ciąg, używając separatora. Na przykład poniższe wyrażenie zwraca `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, który zostanie podzielona.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Separator.|  
  
  
## <a name="collection-functions"></a>Kolekcja funkcji  
 Funkcje te działają w kolekcji, takich jak tablice, ciągi i czasami słowników.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|zawiera|Zwraca wartość PRAWDA, jeśli słownik zawiera listę kluczy, zawiera wartość lub ciąg zawiera podciąg. Na przykład poniższe wyrażenie zwraca `true:``contains('abacaba','aca')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: W ramach kolekcji<br /><br /> **Opis**: Wymagany. Kolekcja do przeszukania.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Znajdź obiektu<br /><br /> **Opis**: Wymagany. Obiekt do wyszukiwania wewnątrz **w obrębie kolekcji**.|  
|Długość|Zwraca liczbę elementów w tablicy lub ciągu. Na przykład poniższe wyrażenie zwraca `3`:  `length('abc')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagany. Kolekcja do uzyskania długości.|  
|pusty|Zwraca wartość PRAWDA, jeśli obiekt, tablica lub ciąg jest pusty. Na przykład poniższe wyrażenie zwraca `true`:<br /><br /> `empty('')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagany. Kolekcja do sprawdzenia, czy jest pusta.|  
|Część wspólna|Zwraca pojedynczą tablicę lub obiekt z wspólne elementy między przekazanymi tablicami i obiektami do niego. Na przykład, ta funkcja zwraca `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametry dla tej funkcji może być zestawem obiektów lub zestawem tablic (nie ich kombinacją). Jeśli istnieją dwa obiekty o tej samej nazwie, ostatni obiekt o danej nazwie jest wyświetlany w obiekcie końcowym.<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: Kolekcja *n*<br /><br /> **Opis**: Wymagany. Kolekcje do wyliczenia wartości. Obiekt musi być w wszystkich przekazanych kolekcjach, aby są wyświetlane w wynikach.|  
|Unia|Zwraca pojedynczą tablicę lub obiekt z wszystkich elementów w tablicy lub obiektu są przekazywane do niego. Na przykład ta funkcja zwraca `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametry dla tej funkcji może być zestawem obiektów lub zestawem tablic (nie ich kombinacją). Jeśli istnieją dwa obiekty o tej samej nazwie w końcowych danych wyjściowych, ostatni obiekt o danej nazwie jest wyświetlany w obiekcie końcowym.<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: Kolekcja *n*<br /><br /> **Opis**: Wymagany. Kolekcje do wyliczenia wartości. Obiekt, który pojawia się w dowolnej kolekcji, pojawia się w wyniku.|  
|pierwszy|Zwraca pierwszy element w tablicy lub ciągu przekazany. Na przykład, ta funkcja zwraca `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagany. Kolekcja, aby pobrać pierwszy obiekt.|  
|ostatni|Zwraca ostatni element w tablicy lub ciągu przekazany. Na przykład, ta funkcja zwraca `3`:<br /><br /> `last('0123')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagany. Kolekcja, aby pobrać ostatni obiekt.|  
|Wypełnij|Zwraca pierwszy **liczba** przekazanych elementów z tablicy lub ciągu, na przykład ta funkcja zwraca `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagany. Kolekcja do wykonania pierwszego **liczba** obiekty.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Licznik<br /><br /> **Opis**: Wymagany. Liczba obiektów do usunięcia z **kolekcji**. Wartość musi być dodatnią liczbą całkowitą.|  
|pomiń|Zwraca elementy w tablicy, zaczynając od indeksu **liczba**, na przykład ta funkcja zwraca `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagany. Kolekcja, aby pominąć pierwszy **liczba** obiekty.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Licznik<br /><br /> **Opis**: Wymagany. Liczba obiektów do usunięcia z początku **kolekcji**. Wartość musi być dodatnią liczbą całkowitą.|  
  
## <a name="logical-functions"></a>Funkcje logiczne  
 Te funkcje są przydatne w warunkach, może służyć do oceny dowolnego typu logiki.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|równa się|Zwraca wartość PRAWDA, jeśli dwie wartości są równe. Na przykład, jeśli parametr parameter1 ma foo, poniższe wyrażenie zwróci `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt do porównania z **obiektem 2**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt do porównania z **obiektem 1**.|  
|mniej|Zwraca wartość PRAWDA, jeśli pierwszy argument jest mniejszy od drugiego. Należy pamiętać, że wartości mogą być tylko typu całkowitego, zmiennoprzecinkowego lub ciąg. Na przykład poniższe wyrażenie zwraca `true`:  `less(10,100)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest mniejsza niż **obiektem 2**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest on większy niż **obiektem 1**.|  
|lessOrEquals|Zwraca wartość PRAWDA, jeśli pierwszy argument jest mniejszy niż lub równe drugiemu. Należy pamiętać, że wartości mogą być tylko typu całkowitego, zmiennoprzecinkowego lub ciąg. Na przykład poniższe wyrażenie zwraca `true`:  `lessOrEquals(10,10)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest on mniejszy lub równy **obiektem 2**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest większa niż lub równa **obiektem 1**.|  
|większa|Zwraca wartość PRAWDA, jeśli pierwszy argument jest większy od drugiego. Należy pamiętać, że wartości mogą być tylko typu całkowitego, zmiennoprzecinkowego lub ciąg. Na przykład poniższe wyrażenie zwraca `false`:  `greater(10,10)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest on większy niż **obiektem 2**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest mniejsza niż **obiektem 1**.|  
|greaterOrEquals|Zwraca wartość PRAWDA, jeśli pierwszy argument jest większy niż lub równe drugiemu. Należy pamiętać, że wartości mogą być tylko typu całkowitego, zmiennoprzecinkowego lub ciąg. Na przykład poniższe wyrażenie zwraca `false`:  `greaterOrEquals(10,100)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest większa niż lub równa **obiektem 2**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest on mniejszy niż lub równe **obiektem 1**.|  
|i|Zwraca wartość PRAWDA, jeśli są spełnione oba parametry. Oba argumenty muszą być wartościami logicznymi. Zwraca następujące `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Boolean 1<br /><br /> **Opis**: Wymagany. Pierwszy argument, który musi być `true`.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Wartość logiczna 2<br /><br /> **Opis**: Wymagany. Drugi argument musi być `true`.|  
|lub|Zwraca wartość PRAWDA, jeśli jest spełniony jeden z parametrów. Oba argumenty muszą być wartościami logicznymi. Zwraca następujące `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Boolean 1<br /><br /> **Opis**: Wymagany. Pierwszy argument, który może być `true`.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Wartość logiczna 2<br /><br /> **Opis**: Wymagany. Drugi argument może być `true`.|  
|nie|Zwraca wartość PRAWDA, jeśli parametr jest `false`. Oba argumenty muszą być wartościami logicznymi. Zwraca następujące `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Boolean<br /><br /> **Opis**: Zwraca wartość PRAWDA, jeśli parametr jest `false`. Oba argumenty muszą być wartościami logicznymi. Zwraca następujące `true`:  `not(contains('200 Success','Fail'))`|  
|if|Zwraca określoną wartość na podstawie Jeśli wyrażenie podane skutkuje `true` lub `false`.  Na przykład następujące zwroty `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Wyrażenie<br /><br /> **Opis**: Wymagany. Wartość logiczna, która określa, która wartość jest zwracany przez wyrażenie.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: True<br /><br /> **Opis**: Wymagany. Wartość zwracana, jeśli wyrażenie ma `true`.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: False<br /><br /> **Opis**: Wymagany. Wartość zwracana, jeśli wyrażenie ma `false`.|  
  
## <a name="conversion-functions"></a>Funkcje konwersji  
 Te funkcje są używane do konwersji między poszczególnymi typy natywne w języku:  
  
-   string  
  
-   liczba całkowita  
  
-   float  
  
-   wartość logiczna  
  
-   Tablice  
  
-   słowniki  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|int|Konwertuj parametr na liczbę całkowitą. Na przykład poniższe wyrażenie zwraca 100 numeru zamiast ciągu:  `int('100')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Wartość<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na liczbę całkowitą.|  
|string|Konwertuj parametr na ciąg. Na przykład poniższe wyrażenie zwraca `'10'`:  `string(10)` Można również konwersji obiektu na ciąg, na przykład, jeśli **foo** parametru jest obiektem z jedną właściwością `bar : baz`, a następnie zwróci następujące `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Wartość<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na ciąg.|  
|json|Konwertuj parametr na wartość typu JSON. Jest to odwrotność string(). Na przykład poniższe wyrażenie zwraca `[1,2,3]` jako tablicę zamiast ciągu:<br /><br /> `json('[1,2,3]')`<br /><br /> Podobnie ciąg można przekonwertować na obiekt. Na przykład `json('{"bar" : "baz"}')` zwraca:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, który jest konwertowany na wartość typu natywnego.<br /><br /> Funkcja json obsługuje także dane wejściowe xml. Na przykład, wartość parametru:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> jest konwertowany na następujące dane json:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Konwertuj argument parametru na liczbę zmiennoprzecinkową. Na przykład poniższe wyrażenie zwraca `10.333`:  `float('10.333')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Wartość<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na liczbę zmiennoprzecinkową.|  
|bool|Konwertuj parametr na wartość logiczną. Na przykład poniższe wyrażenie zwraca `false`:  `bool(0)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Wartość<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na wartość logiczną.|  
|COALESCE|Zwraca pierwszy obiekt inną niż null w przekazanych argumentach. Uwaga: pusty ciąg nie ma wartość null. Na przykład, jeśli nie zdefiniowano parametrów 1 i 2, spowoduje to zwrócenie `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Liczba parametrów**: 1... *n*<br /><br /> **Nazwa**: Obiekt*n*<br /><br /> **Opis**: Wymagany. Obiekty pod kątem `null`.|  
|base64|Zwraca reprezentację base64 ciągu wejściowego. Na przykład poniższe wyrażenie zwraca `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: W ciągu 1<br /><br /> **Opis**: Wymagany. Ciąg do zakodowania w reprezentacji base64.|  
|base64ToBinary|Zwraca binarną reprezentację ciągu zakodowanego w formacie base64. Na przykład poniższe wyrażenie zwraca binarną reprezentację ciągu niektóre: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg zakodowany w formacie base64.|  
|base64ToString|Zwraca ciągową reprezentację ciągu based64 zakodowany. Na przykład poniższe wyrażenie zwraca niektóre parametry: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg zakodowany w formacie base64.|  
|Binarny|Zwraca binarną reprezentację wartości.  Na przykład poniższe wyrażenie zwraca binarną reprezentację niektórych parametrów: `binary(‘some string’).`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Wartość<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na format binarny.|  
|dataUriToBinary|Zwraca binarną reprezentację identyfikatora URI danych. Na przykład poniższe wyrażenie zwraca binarną reprezentację niektórych parametrów: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Identyfikator URI danych do przekonwertowania na reprezentację binarną.|  
|dataUriToString|Zwraca reprezentację ciągu identyfikatora URI danych. Na przykład poniższe wyrażenie zwraca niektóre parametry: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br />**Opis**: Wymagany. Identyfikator URI do przekonwertowania na reprezentację w postaci ciągu danych.|  
|dataUri|Zwraca identyfikator URI wartości danych. Na przykład poniższe wyrażenie zwraca dane: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: Wartość<br /><br />**Opis**: Wymagany. Wartość do przekonwertowania na dane identyfikatora URI.|  
|decodeBase64|Zwraca ciągową reprezentację wejściowego ciągu based64. Na przykład poniższe wyrażenie zwraca `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Zwraca ciągową reprezentację wejściowego ciągu based64.|  
|encodeUriComponent|Sekwencje ucieczki adresu URL ciąg, który jest przekazywany w. Na przykład poniższe wyrażenie zwraca `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg do adresu URL niebezpieczne znaki z ucieczki.|  
|decodeUriComponent|NZ — adres URL — wyprowadza ciąg, który jest przekazywany w. Na przykład poniższe wyrażenie zwraca `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciągu do odkodowania adresu URL niebezpiecznych znaków z.|  
|decodeDataUri|Zwraca binarną reprezentację ciągu identyfikatora URI danych wejściowych. Na przykład poniższe wyrażenie zwraca binarną reprezentację `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Identyfikator URI do zdekodowania do reprezentacji binarnej.|  
|uriComponent|Zwraca identyfikator URI zakodowany reprezentację wartości. Na przykład poniższe wyrażenie zwraca `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Szczegóły parametrów: Liczba: 1, nazwa: Ciąg, opis: Wymagany. Ciąg do zakodowania jako identyfikator URI.|  
|uriComponentToBinary|Zwraca ciąg zakodowany w formacie binarną reprezentację identyfikatora URI. Na przykład poniższe wyrażenie zwraca binarną reprezentację `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: String<br /><br />**Opis**: Wymagany. Ciąg zakodowany w formacie identyfikatora URI.|  
|uriComponentToString|Zwraca ciąg zakodowany w formacie reprezentację ciągu identyfikatora URI. Na przykład poniższe wyrażenie zwraca `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: String<br /><br />**Opis**: Wymagany. Ciąg zakodowany w formacie identyfikatora URI.|  
|xml|Zwraca reprezentację xml wartości. Na przykład poniższe wyrażenie zwraca zawartości xml reprezentowany przez `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Funkcja xml obsługuje obiektu danych wejściowych JSON także. Na przykład parametr `{ "abc": "xyz" }` jest konwertowane na zawartość xml `\<abc>xyz\</abc>`<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: Wartość<br /><br />**Opis**: Wymagany. Wartość do przekonwertowania na dane XML.|  
|wyrażenie XPath|Zwraca tablicę węzłów xml dopasowania wyrażenia xpath wartość, która daje w wyniku wyrażenia xpath.<br /><br />  **Przykład 1**<br /><br /> Przyjęto założenie, że wartość parametru "p1" jest ciąg reprezentujący następujący kod XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ten kod: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> zwróci<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> natomiast<br /><br /> 2. Ten kod: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> zwróci<br /><br /> `13`<br /><br /> <br /><br /> **Przykład 2**<br /><br /> Biorąc pod uwagę następujące zawartość XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Ten kod: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> lub<br /><br /> 2. Ten kod: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Zwraca<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> i<br /><br /> 3. Ten kod: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Zwraca<br /><br /> ``bar``<br /><br /> **Liczba parametrów**: 1<br /><br />**Nazwa**: Xml<br /><br />**Opis**: Wymagany. Plik XML, na którym można obliczyć wartości wyrażenia XPath.<br /><br /> **Liczba parametrów**: 2<br /><br />**Nazwa**: XPath<br /><br />**Opis**: Wymagany. Wyrażenie XPath do oceny.|  
|tablica|Konwertuj parametr na tablicę.  Na przykład poniższe wyrażenie zwraca `["abc"]`: `array('abc')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Wartość<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na tablicę.|
|createArray|Tworzy tablicę z parametrów.  Na przykład poniższe wyrażenie zwraca `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Liczba parametrów**: 1... n<br /><br /> **Nazwa**: Wszelkie n<br /><br /> **Opis**: Wymagany. Wartości do połączenia jako tablica.|

## <a name="math-functions"></a>Funkcje matematyczne  
 Te funkcje mogą być używane dla obu typów liczb: **liczb całkowitych** i **liczby zmiennoprzecinkowe**.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|add|Zwraca wynik dodawania dwóch liczb. Na przykład, ta funkcja zwraca `20.333`:  `add(10,10.333)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Dodawana 1<br /><br /> **Opis**: Wymagany. Numer do dodania do **2 dodawana**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Dodawana 2<br /><br /> **Opis**: Wymagany. Numer do dodania do **1 dodawana**.|  
|Sub|Zwraca wynik odejmowania dwóch liczb. Na przykład, ta funkcja zwraca: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: odjemna<br /><br /> **Opis**: Wymagany. Liczba, **Odjemnik** zostanie usunięty z.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: odjemnik<br /><br /> **Opis**: Wymagany. Liczba odejmowana od **Odjemna**.|  
|mul|Zwraca wynik mnożenia dwóch liczb. Na przykład następujące zwroty `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Którą mnożona jest mnożna 1<br /><br /> **Opis**: Wymagany. Liczbę Aby pomnożyć **którą mnożona jest mnożna 2** za pomocą.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Którą mnożona jest mnożna 2<br /><br /> **Opis**: Wymagany. Liczbę Aby pomnożyć **którą mnożona jest mnożna 1** za pomocą.|  
|div|Zwraca wynik dzielenia dwóch liczb. Na przykład następujące zwroty `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: dzielna<br /><br /> **Opis**: Wymagany. Liczba do podzielenia przez **dzielnik**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: dzielnik.<br /><br /> **Opis**: Wymagany. Liczba do podzielenia **dzielna** przez.|  
|dzielenie modulo|Zwraca wynik resztę po wykonaniu dzielenia dwóch liczb (modulo). Na przykład poniższe wyrażenie zwraca `2`:<br /><br /> `mod(10,4)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: dzielna<br /><br /> **Opis**: Wymagany. Liczba do podzielenia przez **dzielnik**.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: dzielnik.<br /><br /> **Opis**: Wymagany. Liczba do podzielenia **dzielna** przez. Po wykonaniu dzielenia reszta jest zajęta.|  
|min.|Istnieją dwa różne wzorce wywoływania tej funkcji: `min([0,1,2])` W tym miejscu min pobiera tablicę. To wyrażenie zwraca `0`. Alternatywnie może to trwać listę wartości rozdzielonych przecinkami:  `min(0,1,2)` Ponadto ta funkcja zwraca wartość 0. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą musi zawierać wyłącznie cyfry w nim.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Kolekcja lub wartość<br /><br /> **Opis**: Wymagany. Może być on tablicę wartości, aby znaleźć wartości minimalnej lub pierwsza wartość zestawu.<br /><br /> **Liczba parametrów**: 2... *n*<br /><br /> **Nazwa**: Wartość *n*<br /><br /> **Opis**: Opcjonalny. Jeśli wartość jest pierwszy parametr, następnie można przekazać dodatkowe wartości i minimum wszystkich wartości przekazane są zwracane.|  
|maks.|Istnieją dwa różne wzorce wywoływania tej funkcji:  `max([0,1,2])`<br /><br /> W tym miejscu max pobiera tablicę. To wyrażenie zwraca `2`. Alternatywnie może to trwać listę wartości rozdzielonych przecinkami:  `max(0,1,2)` Ta funkcja zwraca wartość 2. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą musi zawierać wyłącznie cyfry w nim.<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Kolekcja lub wartość<br /><br /> **Opis**: Wymagany. Może być on tablicę wartości, aby znaleźć wartości maksymalnej lub pierwsza wartość zestawu.<br /><br /> **Liczba parametrów**: 2... *n*<br /><br /> **Nazwa**: Wartość *n*<br /><br /> **Opis**: Opcjonalny. Jeśli wartość jest pierwszy parametr, następnie można przekazać dodatkowe wartości, a maksimum wszystkich wartości przekazane są zwracane.|  
|Zakres| Generuje tablicę wartości całkowitych, zaczynając od konkretnej liczby, i zdefiniowania długości zwróconej tablicy. Na przykład, ta funkcja zwraca `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Indeks początkowy<br /><br /> **Opis**: Wymagany. Jest to pierwsza liczba całkowita w tablicy.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Licznik<br /><br /> **Opis**: Wymagany. Liczb całkowitych, które znajdują się w tablicy.|  
|RAND| Generuje losową wartość całkowitą z określonego zakresu (włącznie z wartościami na obu końcach. Na przykład może to zwrócić `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Minimalne<br /><br /> **Opis**: Wymagany. Najmniejsza liczba całkowita możliwej do zwrócenia.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Maksimum<br /><br /> **Opis**: Wymagany. Największej liczbie całkowitej możliwej do zwrócenia.|  
  
## <a name="date-functions"></a>Funkcje daty  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|utcnow|Zwraca bieżącą sygnaturę czasową jako ciąg. Na przykład `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Albo [pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [niestandardowy wzorzec formatu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) który określa sposób formatowania wartości tej sygnatury czasowej. Jeśli format nie zostanie podany, jest używany format ISO 8601 ("o").|  
|addSeconds|Dodaje liczbę całkowitą sekund do przekazanej sygnatury czasowej ciągu. Liczba sekund może być dodatnia lub ujemna. Wynik jest ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że podano specyfikatora formatu. Na przykład `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Znacznik czasu<br /><br /> **Opis**: Wymagany. Ciąg zawierający godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Sekundy<br /><br /> **Opis**: Wymagany. Liczba sekund do dodania. Może być ujemna na potrzeby odejmowania sekund.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Albo [pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [niestandardowy wzorzec formatu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) który określa sposób formatowania wartości tej sygnatury czasowej. Jeśli format nie zostanie podany, jest używany format ISO 8601 ("o").|  
|addminutes|Dodaje liczbę całkowitą minut do przekazanej sygnatury czasowej ciągu. Liczba minut może być dodatnia lub ujemna. Wynik jest ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że podano specyfikatora formatu. Na przykład `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Znacznik czasu<br /><br /> **Opis**: Wymagany. Ciąg zawierający godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Minuty<br /><br /> **Opis**: Wymagany. Liczba minut, aby dodać. Może być ujemna na potrzeby odejmowania minut.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Albo [pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [niestandardowy wzorzec formatu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) który określa sposób formatowania wartości tej sygnatury czasowej. Jeśli format nie zostanie podany, jest używany format ISO 8601 ("o").|  
|addhours|Dodaje liczbę całkowitą godzin do przekazanej sygnatury czasowej ciągu. Liczba godzin może być dodatnia lub ujemna. Wynik jest ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że podano specyfikatora formatu. Na przykład `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Znacznik czasu<br /><br /> **Opis**: Wymagany. Ciąg zawierający godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Godziny<br /><br /> **Opis**: Wymagany. Liczba godzin do dodania. Może być ujemna na potrzeby odejmowania godzin.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Albo [pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [niestandardowy wzorzec formatu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) który określa sposób formatowania wartości tej sygnatury czasowej. Jeśli format nie zostanie podany, jest używany format ISO 8601 ("o").|  
|addDays|Dodaje liczbę całkowitą dni do przekazanej sygnatury czasowej ciągu. Liczba dni, może być dodatnia lub ujemna. Wynik jest ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że podano specyfikatora formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Znacznik czasu<br /><br /> **Opis**: Wymagany. Ciąg zawierający godzinę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Dni<br /><br /> **Opis**: Wymagany. Liczba dni do dodania. Może być ujemna na potrzeby odejmowania dni.<br /><br /> **Liczba parametrów**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Albo [pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [niestandardowy wzorzec formatu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) który określa sposób formatowania wartości tej sygnatury czasowej. Jeśli format nie zostanie podany, jest używany format ISO 8601 ("o").|  
|formatDateTime|Zwraca ciąg w formacie daty. Wynik jest ciąg w formacie ISO 8601 ("o"), domyślnie, chyba że podano specyfikatora formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Liczba parametrów**: 1<br /><br /> **Nazwa**: Date<br /><br /> **Opis**: Wymagany. Ciąg, który zawiera datę.<br /><br /> **Liczba parametrów**: 2<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Albo [pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [niestandardowy wzorzec formatu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) który określa sposób formatowania wartości tej sygnatury czasowej. Jeśli format nie zostanie podany, jest używany format ISO 8601 ("o").|  

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę zmiennych systemowych można używać w wyrażeniach, zobacz [zmiennych systemowych](control-flow-system-variables.md).
