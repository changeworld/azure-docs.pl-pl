---
title: Wyrażenie i funkcje w Azure Data Factory | Microsoft Docs
description: Ten artykuł zawiera informacje o wyrażeniach i funkcjach, których można użyć podczas tworzenia jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9fc8dd1e22e16ef5342b405b602f8baaf8ce7edf
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692856"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Wyrażenia i funkcje w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-functions-variables.md)
> * [Bieżąca wersja](control-flow-expression-language-functions.md)

Ten artykuł zawiera szczegółowe informacje na temat wyrażeń i funkcji obsługiwanych przez Azure Data Factory. 

## <a name="introduction"></a>Wprowadzenie
Wartości JSON w definicji mogą być literałami lub wyrażeniami, które są oceniane w czasie wykonywania. Na przykład:  
  
```json
"name": "value"
```

 oraz  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Wyrażenia  
Wyrażenia mogą znajdować się w dowolnym miejscu w wartości ciągu JSON i zawsze dawać inną wartość JSON. Jeśli wartość JSON jest wyrażeniem, treść wyrażenia jest wyodrębniana przez usunięcie znaku "(\@). Jeśli jest wymagany ciąg literału, który rozpoczyna się od \@, należy użyć polecenia \@ \@. W poniższych przykładach pokazano, jak są oceniane wyrażenia.  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|wejściowe|Znaki "Parameters" są zwracane.|  
|"parametry [1]"|Znaki "parametry [1]" są zwracane.|  
|"\@ \@"|Zwracany jest 1 ciąg znaków zawierający "\@".|  
|"\@"|Jest zwracany 2-znakowy ciąg zawierający "\@".|  
  
 Wyrażenia mogą być również wyświetlane wewnątrz ciągów, przy użyciu funkcji o nazwie *interpolacji ciągu* , w której wyrażenia są opakowane w `@{ ... }`. Na przykład: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Przy użyciu interpolacji ciągów wynik jest zawsze ciągiem. Załóżmy, że zdefiniowano `myNumber` jako `42` i `myString` jako `foo`:  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"\@pipeline (). Parameters. ciąg"| Zwraca `foo` jako ciąg.|  
|"\@ {Pipeline (). Parameters.| Zwraca `foo` jako ciąg.|  
|"\@pipeline (). Parameters. mój numer"| Zwraca `42` jako *liczbę*.|  
|"\@ {potok (). Parameters}"| Zwraca `42` jako *ciąg*.|  
|"Odpowiedź to: @ {potoku (). Parameters.! Number}"| Zwraca ciąg `Answer is: 42`.|  
|"\@concat (odpowiedź to:", String (potok (). Parameters.-Number)) "| Zwraca ciąg `Answer is: 42`|  
|"Odpowiedź to: \@ \@ {potok (). Parameters.! Number}"| Zwraca ciąg `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Przykłady

#### <a name="a-dataset-with-a-parameter"></a>Zestaw danych z parametrem
W poniższym przykładzie BlobDataset przyjmuje parametr o nazwie **Path**. Jej wartość służy do ustawiania wartości właściwości **folderPath** przy użyciu wyrażenia: `dataset().path`. 

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
W poniższym przykładzie potok przyjmuje parametry **inputPath** i **outputPath** . **Ścieżka** sparametryzowanego zestawu danych obiektów BLOB jest ustawiana za pomocą wartości tych parametrów. Składnia używana tutaj: `pipeline().parameters.parametername`. 

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
#### <a name="tutorial"></a>Samouczek
Ten [samouczek](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) przeprowadzi Cię przez proces przekazywania parametrów między potokiem a działaniem oraz między działaniami.

  
## <a name="functions"></a>Functions  
 Możesz wywoływać funkcje w wyrażeniach. Poniższe sekcje zawierają informacje o funkcjach, których można użyć w wyrażeniu.  

## <a name="string-functions"></a>Funkcje ciągów  
 Poniższe funkcje dotyczą tylko ciągów. Można również użyć szeregu funkcji kolekcji dla ciągów.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|Concat|Łączy każdą liczbę ciągów razem. Na przykład jeśli parametr1 jest `foo,` następujące wyrażenie zwróci `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: ciąg *n*<br /><br /> **Opis**: wymagane. Ciągi, które mają zostać połączone w jeden ciąg.|  
|podciąg|Zwraca podzbiór znaków z ciągu. Na przykład następujące wyrażenie:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> typu<br /><br /> `foo`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, z którego jest pobierany podciąg.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Uruchom indeks<br /><br /> **Opis**: wymagane. Indeks lokalizacji podciągu rozpoczynającego się w parametrze 1. Indeks początkowy jest liczony od zera. <br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: Długość<br /><br /> **Opis**: wymagane. Długość podciągu.|  
|stępować|Zastępuje ciąg z danym ciągiem. Na przykład wyrażenie:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> typu<br /><br /> `the new string`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane.  Jeśli parametr 2 zostanie znaleziony w parametrze 1, ciąg wyszukiwany dla parametru 2 i zaktualizowany przy użyciu parametru 3.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: stary ciąg<br /><br /> **Opis**: wymagane. Ciąg, który ma zostać zastąpiony parametrem 3, jeśli dopasowanie zostanie znalezione w parametrze 1<br /><br /> **Numer parametru**: 3<br /><br /> **Name**: nowy ciąg<br /><br /> **Opis**: wymagane. Ciąg, który jest używany do zastępowania ciągu w parametrze 2 w przypadku znalezienia dopasowania w parametrze 1.|  
|Ident| Generuje unikatowy ciąg globalny (alias. Identyfikator GUID). Na przykład następujące dane wyjściowe mogą zostać wygenerowane `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: format<br /><br /> **Opis**: opcjonalny. Pojedynczy specyfikator formatu, który wskazuje [sposób formatowania wartości tego identyfikatora GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Parametr formatu może mieć wartość "N", "D", "B", "P" lub "X". Jeśli format nie zostanie podany, używany jest "D".|  
|toLower|Konwertuje ciąg na małe litery. Na przykład następujące polecenie zwraca `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg do przekonwertowania na niższą wielkość liter. Jeśli znak w ciągu nie ma odpowiednika małymi literami, jest on uwzględniany niezmieniony w zwracanym ciągu.|  
|toUpper|Konwertuje ciąg na wielkie litery. Na przykład następujące wyrażenie zwraca `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg do przekonwertowania na wielkie litery. Jeśli znak w ciągu nie ma wielkiej litery, jest on uwzględniany niezmieniony w zwracanym ciągu.|  
|IndexOf|Odszukaj indeks wartości w przypadku ciągu bez uwzględniania wielkości liter. Indeks jest liczony od zera. Na przykład następujące wyrażenie zwraca `7`: `indexof('hello, world.', 'world')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Wartość do przeszukania indeksu.|  
|LastIndexOf|Odszukaj ostatni indeks wartości w przypadku ciągu bez uwzględniania wielkości liter. Indeks jest liczony od zera. Na przykład następujące wyrażenie zwraca `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Wartość do przeszukania indeksu.|  
|StartsWith|Sprawdza, czy ciąg rozpoczyna się od wartości bez uwzględniania wielkości liter. Na przykład następujące wyrażenie zwraca `true`: `startswith('hello, world', 'hello')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Wartość, z którą może zaczynać się ciąg.|  
|EndsWith|Sprawdza, czy ciąg jest zakończony bez uwzględniania wielkości liter. Na przykład następujące wyrażenie zwraca `true`: `endswith('hello, world', 'world')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Wartość, z którą może kończyć się ciąg.|  
|split|Dzieli ciąg za pomocą separatora. Na przykład następujące wyrażenie zwraca `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, który jest dzielony.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Separator.|  
  
  
## <a name="collection-functions"></a>Funkcje kolekcji  
 Funkcje te działają nad kolekcjami, takimi jak tablice, ciągi i czasami słowniki.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|wyświetlana|Zwraca wartość PRAWDA, jeśli słownik zawiera klucz, lista zawiera wartość lub ciąg zawiera podciąg. Na przykład następujące wyrażenie zwraca `true:``contains('abacaba','aca')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: w kolekcji<br /><br /> **Opis**: wymagane. Kolekcja do przeszukiwania.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: find Object<br /><br /> **Opis**: wymagane. Obiekt, który ma znajdować się wewnątrz **kolekcji**.|  
|Długość|Zwraca liczbę elementów w tablicy lub ciągu. Na przykład następujące wyrażenie zwraca `3`: `length('abc')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: kolekcja<br /><br /> **Opis**: wymagane. Kolekcja, w której ma zostać pobrana długość.|  
|ciągiem|Zwraca wartość true, jeśli obiekt, tablica lub ciąg są puste. Na przykład następujące wyrażenie zwraca `true`:<br /><br /> `empty('')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: kolekcja<br /><br /> **Opis**: wymagane. Kolekcja, aby sprawdzić, czy jest pusta.|  
|Część wspólną|Zwraca pojedynczą tablicę lub obiekt ze wspólnymi elementami między tablicami lub obiektami, do których przechodzą. Na przykład ta funkcja zwraca `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametry funkcji mogą być zestawem obiektów lub zestawem tablic (a nie ich mieszaniną). Jeśli istnieją dwa obiekty o tej samej nazwie, ostatni obiekt z tą nazwą pojawia się w obiekcie końcowym.<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: kolekcja *n*<br /><br /> **Opis**: wymagane. Kolekcje do obliczenia. Obiekt musi znajdować się we wszystkich kolekcjach, które przekazały, aby były wyświetlane w wyniku.|  
|Unii|Zwraca pojedynczą tablicę lub obiekt ze wszystkimi elementami, które znajdują się w tablicy lub obiekcie przekazaną do niego. Na przykład ta funkcja zwraca `[1, 2, 3, 10, 101]:`<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametry funkcji mogą być zestawem obiektów lub zestawem tablic (a nie ich mieszaniną). Jeśli istnieją dwa obiekty o tej samej nazwie w końcowym danych wyjściowych, ostatni obiekt z tą nazwą pojawia się w obiekcie końcowym.<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: kolekcja *n*<br /><br /> **Opis**: wymagane. Kolekcje do obliczenia. Obiekt, który pojawia się w dowolnej kolekcji, pojawi się w wyniku.|  
|Pierwszego|Zwraca pierwszy element w tablicy lub przekazaniu. Na przykład ta funkcja zwraca `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: kolekcja<br /><br /> **Opis**: wymagane. Kolekcja, z której ma zostać pożądany pierwszy obiekt.|  
|ostatniego|Zwraca ostatni element w tablicy lub przekazaniu ciągu. Na przykład ta funkcja zwraca `3`:<br /><br /> `last('0123')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: kolekcja<br /><br /> **Opis**: wymagane. Kolekcja, z której ma zostać pożądany ostatni obiekt.|  
|Czasochłonn|Zwraca pierwsze elementy **Count** z tablicy lub przekazaną wartość, na przykład ta funkcja zwraca `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: kolekcja<br /><br /> **Opis**: wymagane. Kolekcja, w której mają zostać uwzględnione pierwsze obiekty **Count** z.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: liczba<br /><br /> **Opis**: wymagane. Liczba obiektów do pobrania z **kolekcji**. Musi to być dodatnia liczba całkowita.|  
|Skocz|Zwraca elementy w tablicy zaczynające się od **liczby**indeksów, na przykład ta funkcja zwraca `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: kolekcja<br /><br /> **Opis**: wymagane. Kolekcja, w której mają zostać pominięte pierwsze obiekty **Count** z.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: liczba<br /><br /> **Opis**: wymagane. Liczba obiektów, które mają zostać usunięte z przodu **kolekcji**. Musi to być dodatnia liczba całkowita.|  
  
## <a name="logical-functions"></a>Funkcje logiczne  
 Te funkcje są przydatne w warunkach, ale mogą służyć do szacowania dowolnego typu logiki.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|Ubiegł|Zwraca wartość true, jeśli dwie wartości są równe. Na przykład jeśli parametr1 to foo, następujące wyrażenie zwróci `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: wymagane. Obiekt, który ma zostać porównany z **obiektem 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis**: wymagane. Obiekt, który ma zostać porównany z **obiektem 1**.|  
|wcześniejsz|Zwraca wartość true, jeśli pierwszy argument jest mniejszy od drugiego. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `true`: `less(10,100)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: wymagane. Obiekt, aby sprawdzić, czy jest on mniejszy niż **obiekt 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis**: wymagane. Obiekt, który ma być sprawdzany, jeśli jest większy niż **obiekt 1**.|  
|lessOrEquals|Zwraca wartość true, jeśli pierwszy argument jest mniejszy lub równy drugiemu. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `true`: `lessOrEquals(10,10)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: wymagane. Obiekt do sprawdzenia, czy jest mniejszy lub równy **obiektowi 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis**: wymagane. Obiekt do sprawdzenia, czy jest on większy niż lub równy **obiektowi 1**.|  
|mniejszą|Zwraca wartość true, jeśli pierwszy argument jest większy od drugiego. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `false`: `greater(10,10)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: wymagane. Obiekt do sprawdzenia, czy jest większy niż **obiekt 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis**: wymagane. Obiekt, który ma być sprawdzany, jeśli jest mniejszy niż **obiekt 1**.|  
|greaterOrEquals|Zwraca wartość true, jeśli pierwszy argument jest większy lub równy drugiemu. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `false`: `greaterOrEquals(10,100)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: obiekt 1<br /><br /> **Opis**: wymagane. Obiekt do sprawdzenia, czy jest większy lub równy **obiektowi 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: obiekt 2<br /><br /> **Opis**: wymagane. Obiekt do sprawdzenia, czy jest on mniejszy niż lub równy **obiektowi 1**.|  
|oraz|Zwraca wartość true, jeśli oba parametry mają wartość true. Oba argumenty muszą być wartością logiczną. Następujące zwraca `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość logiczna 1<br /><br /> **Opis**: wymagane. Pierwszy argument, który musi być `true`.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: wartość logiczna 2<br /><br /> **Opis**: wymagane. Drugi argument musi być `true`.|  
|lub|Zwraca wartość true, jeśli jeden z parametrów ma wartość true. Oba argumenty muszą być wartością logiczną. Następujące zwraca `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość logiczna 1<br /><br /> **Opis**: wymagane. Pierwszy argument, który może być `true`.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: wartość logiczna 2<br /><br /> **Opis**: wymagane. Drugi argument może być `true`.|  
|Niemożliwe|Zwraca wartość true, jeśli parametr jest `false`. Oba argumenty muszą być wartością logiczną. Następujące zwraca `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość logiczna<br /><br /> **Opis**: zwraca wartość true, jeśli parametr jest `false`. Oba argumenty muszą być wartością logiczną. Następujące zwraca `true`: `not(contains('200 Success','Fail'))`|  
|przypadku|Zwraca określoną wartość na podstawie tego, czy podane wyrażenie daje w wyniku `true` lub `false`.  Na przykład następujące polecenie zwraca `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: wyrażenie<br /><br /> **Opis**: wymagane. Wartość logiczna określająca, która wartość jest zwracana przez wyrażenie.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: prawda<br /><br /> **Opis**: wymagane. Wartość, która ma zostać zwrócona, jeśli wyrażenie jest `true`.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: FAŁSZ<br /><br /> **Opis**: wymagane. Wartość, która ma zostać zwrócona, jeśli wyrażenie jest `false`.|  
  
## <a name="conversion-functions"></a>Funkcje konwersji  
 Te funkcje są używane do konwersji między poszczególnymi typami natywnymi w języku:  
  
-   string  
  
-   liczba całkowita  
  
-   float  
  
-   wartość logiczna  
  
-   Tablice  
  
-   słownik  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|int|Przekonwertuj parametr na liczbę całkowitą. Na przykład następujące wyrażenie zwraca 100 jako liczbę, a nie ciąg: `int('100')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis**: wymagane. Wartość, która jest konwertowana na liczbę całkowitą.|  
|string|Przekonwertuj parametr na ciąg. Na przykład następujące wyrażenie zwraca `'10'`: `string(10)` można również przekonwertować obiekt na ciąg, na przykład jeśli parametr **foo** jest obiektem z jedną właściwością `bar : baz`, wówczas następujące zwróci `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis**: wymagane. Wartość, która jest konwertowana na ciąg.|  
|kodu|Przekonwertuj parametr na wartość typu JSON. Jest przeciwieństwem ciągu (). Na przykład następujące wyrażenie zwraca `[1,2,3]` jako tablicę, a nie ciąg:<br /><br /> `json('[1,2,3]')`<br /><br /> Analogicznie, można przekonwertować ciąg na obiekt. Na przykład `json('{"bar" : "baz"}')` zwraca:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, który jest konwertowany na wartość typu natywnego.<br /><br /> Funkcja JSON obsługuje również dane wejściowe XML. Na przykład wartość parametru:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> jest konwertowany na następujący kod JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Przekonwertuj argument parametru na liczbę zmiennoprzecinkową. Na przykład następujące wyrażenie zwraca `10.333`: `float('10.333')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis**: wymagane. Wartość, która jest konwertowana na liczbę zmiennoprzecinkową.|  
|bool|Przekonwertuj parametr na wartość logiczną. Na przykład następujące wyrażenie zwraca `false`: `bool(0)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis**: wymagane. Wartość, która zostanie przekonwertowana na wartość logiczną.|  
|Łączonych|Zwraca pierwszy obiekt o wartości innej niż null w argumentach przekazane. Uwaga: pusty ciąg nie ma wartości null. Na przykład jeśli nie zdefiniowano parametrów 1 i 2, spowoduje to zwrócenie `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: obiekt*n*<br /><br /> **Opis**: wymagane. Obiekty do sprawdzenia dla `null`.|  
|zakodowan|Zwraca reprezentację Base64 ciągu wejściowego. Na przykład następujące wyrażenie zwraca `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: ciąg 1<br /><br /> **Opis**: wymagane. Ciąg, który ma zostać zakodowany w reprezentacji Base64.|  
|base64ToBinary|Zwraca binarną reprezentację ciągu zakodowanego algorytmem Base64. Na przykład następujące wyrażenie zwraca reprezentację binarną pewnego ciągu: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Zakodowany ciąg Base64.|  
|base64ToString|Zwraca reprezentację ciągu zakodowanego ciągu based64. Na przykład następujące wyrażenie zwraca ciąg: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Zakodowany ciąg Base64.|  
|Binarny|Zwraca binarną reprezentację wartości.  Na przykład następujące wyrażenie zwraca binarną reprezentację pewnego ciągu: `binary(‘some string’).`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis**: wymagane. Wartość, która jest konwertowana na format binarny.|  
|dataUriToBinary|Zwraca binarną reprezentację identyfikatora URI danych. Na przykład następujące wyrażenie zwraca reprezentację binarną pewnego ciągu: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Identyfikator URI danych do przekonwertowania na reprezentację binarną.|  
|dataUriToString|Zwraca ciąg reprezentujący identyfikator URI danych. Na przykład następujące wyrażenie zwraca ciąg: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br />**Opis**: wymagane. Identyfikator URI danych do przekonwertowania na reprezentację ciągu.|  
|dataUri|Zwraca identyfikator URI danych wartości. Na przykład następujące wyrażenie zwraca dane: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Numer parametru**: 1<br /><br />**Nazwa**: wartość<br /><br />**Opis**: wymagane. Wartość do przekonwertowania na identyfikator URI danych.|  
|decodeBase64|Zwraca ciąg reprezentujący wejściowy ciąg based64. Na przykład następujące wyrażenie zwraca `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: zwraca ciąg reprezentujący wejściowy ciąg based64.|  
|encodeURIComponent —|URL — wyprowadza ciąg, który został przesłany. Na przykład następujące wyrażenie zwraca `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg do niebezpiecznego znaku w adresie URL.|  
|decodeUriComponent|Unadres URL — wyprowadza ciąg, który jest przesyłany. Na przykład następujące wyrażenie zwraca `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. Ciąg, w którym ma zostać zdekodowany adres URL-niebezpieczne znaki z.|  
|decodeDataUri|Zwraca binarną reprezentację ciągu identyfikatora URI danych wejściowych. Na przykład następujące wyrażenie zwraca binarną reprezentację `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: wymagane. DataURI do dekodowania w reprezentacji binarnej.|  
|uriComponent|Zwraca reprezentację wartości z zakodowaną URI. Na przykład następujące wyrażenie zwraca `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Szczegóły parametru: Number: 1, Name: String, Description: Required. Ciąg do kodowania identyfikatora URI.|  
|uriComponentToBinary|Zwraca binarną reprezentację ciągu zakodowanego w identyfikatorze URI. Na przykład następujące wyrażenie zwraca binarną reprezentację `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br />**Opis**: wymagane. Zakodowany ciąg identyfikatora URI.|  
|uriComponentToString|Zwraca ciąg reprezentujący ciąg zakodowany w identyfikatorze URI. Na przykład następujące wyrażenie zwraca `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Numer parametru**: 1<br /><br />**Name**: ciąg<br /><br />**Opis**: wymagane. Zakodowany ciąg identyfikatora URI.|  
|xml|Zwraca reprezentację XML wartości. Na przykład następujące wyrażenie zwraca zawartość XML reprezentowaną przez `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Funkcja XML obsługuje również dane wejściowe obiektu JSON. Na przykład parametr `{ "abc": "xyz" }` jest konwertowany na zawartość XML `\<abc>xyz\</abc>`<br /><br /> **Numer parametru**: 1<br /><br />**Nazwa**: wartość<br /><br />**Opis**: wymagane. Wartość do przekonwertowania na format XML.|  
|Lokalizacja|Zwraca tablicę węzłów XML pasującą do wyrażenia XPath wartości zwracanej przez wyrażenie XPath.<br /><br />  **Przykład 1**<br /><br /> Załóżmy, że wartość parametru "P1" to ciąg reprezentujący następujący kod XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. ten kod: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> zwróci<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> określić<br /><br /> 2. ten kod: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> zwróci<br /><br /> `13`<br /><br /> <br /><br /> **Przykład 2**<br /><br /> Uwzględniając następującą zawartość XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1. ten kod: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> lub<br /><br /> 2. ten kod: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Typu<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> oraz<br /><br /> 3. ten kod: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Typu<br /><br /> ``bar``<br /><br /> **Numer parametru**: 1<br /><br />**Nazwa**: XML<br /><br />**Opis**: wymagane. KOD XML, dla którego ma zostać obliczone wyrażenie XPath.<br /><br /> **Numer parametru**: 2<br /><br />**Name**: XPath<br /><br />**Opis**: wymagane. Wyrażenie XPath, które ma zostać obliczone.|  
|tablica|Przekonwertuj parametr na tablicę.  Na przykład następujące wyrażenie zwraca `["abc"]`: `array('abc')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: wartość<br /><br /> **Opis**: wymagane. Wartość, która jest konwertowana na tablicę.|
|przearray|Tworzy tablicę z parametrów.  Na przykład następujące wyrażenie zwraca `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Numer parametru**: 1... Azotan<br /><br /> **Nazwa**: dowolne n<br /><br /> **Opis**: wymagane. Wartości, które mają zostać połączone do tablicy.|

## <a name="math-functions"></a>Funkcje matematyczne  
 Te funkcje mogą być używane dla każdego typu liczb: **liczb całkowitych** i **zmiennoprzecinkowych**.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|add|Zwraca wynik dodania dwóch liczb. Na przykład ta funkcja zwraca `20.333`: `add(10,10.333)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: summand 1<br /><br /> **Opis**: wymagane. Liczba, która ma zostać dodana do **summand 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: summand 2<br /><br /> **Opis**: wymagane. Liczba, która ma zostać dodana do **summand 1**.|  
|Sub|Zwraca wynik odejmowania dwóch liczb. Na przykład ta funkcja zwraca: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: odjemna<br /><br /> **Opis**: wymagane. Liczba, z której zostanie usunięty **odjemnik** .<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: odjemnik<br /><br /> **Opis**: wymagane. Liczba, która ma zostać usunięta z **odjemna**.|  
|mul|Zwraca wynik mnożenia dwóch liczb. Na przykład następujące polecenie zwraca `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: multiplicand 1<br /><br /> **Opis**: wymagane. Liczba, do której ma zostać pomnożona wartość **multiplicand 2** .<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: multiplicand 2<br /><br /> **Opis**: wymagane. Liczba do pomnożenia **multiplicand 1** z.|  
|służąc|Zwraca wynik dzielenia dwóch liczb. Na przykład następujące polecenie zwraca `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: dzielną<br /><br /> **Opis**: wymagane. Liczba, która ma zostać podzielona przez **dzielnik**.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: dzielnik<br /><br /> **Opis**: wymagane. Liczba, przez którą ma zostać podzielona **dywidenda** .|  
|Funkcja|Zwraca wynik reszty po rozdziale dwóch liczb (modulo). Na przykład następujące wyrażenie zwraca `2`:<br /><br /> `mod(10,4)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: dzielną<br /><br /> **Opis**: wymagane. Liczba, która ma zostać podzielona przez **dzielnik**.<br /><br /> **Numer parametru**: 2<br /><br /> **Name**: dzielnik<br /><br /> **Opis**: wymagane. Liczba, przez którą ma zostać podzielona **dywidenda** . Po rozdziale zostaje pozostała reszta.|  
|min.|Istnieją dwa różne wzorce wywołania tej funkcji: `min([0,1,2])` w tym miejscu minimalna liczba pobiera tablicę. To wyrażenie zwraca `0`. Alternatywnie ta funkcja może przyjmować listę wartości rozdzielonych przecinkami: `min(0,1,2)` ta funkcja zwraca również wartość 0. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą, musi zawierać tylko cyfry.<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: kolekcja lub wartość<br /><br /> **Opis**: wymagane. Może być tablicą wartości, aby znaleźć wartość minimalną lub pierwszą wartość zestawu.<br /><br /> **Numer parametru**: 2... *n*<br /><br /> **Name**: wartość *n*<br /><br /> **Opis**: opcjonalny. Jeśli pierwszy parametr jest wartością, można przekazać dodatkowe wartości i zwrócić minimum wszystkich przekazanych wartości.|  
|Maksymalny|Istnieją dwa różne wzorce wywołania tej funkcji: `max([0,1,2])`<br /><br /> W tym miejscu maksymalna przyjmuje tablicę. To wyrażenie zwraca `2`. Alternatywnie ta funkcja może przyjmować listę wartości rozdzielonych przecinkami: `max(0,1,2)` ta funkcja zwraca wartość 2. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą, musi zawierać tylko cyfry.<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: kolekcja lub wartość<br /><br /> **Opis**: wymagane. Może być tablicą wartości, aby znaleźć wartość maksymalną lub pierwszą wartość zestawu.<br /><br /> **Numer parametru**: 2... *n*<br /><br /> **Name**: wartość *n*<br /><br /> **Opis**: opcjonalny. Jeśli pierwszy parametr jest wartością, można przekazać wartości dodatkowe i wszystkie przekazane wartości są zwracane.|  
|zakresu| Generuje tablicę liczb całkowitych, zaczynając od określonej liczby, i definiuje długość zwróconej tablicy. Na przykład ta funkcja zwraca `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Uruchom indeks<br /><br /> **Opis**: wymagane. Jest to pierwsza liczba całkowita w tablicy.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: liczba<br /><br /> **Opis**: wymagane. Liczba liczb całkowitych, które znajdują się w tablicy.|  
|Rand| Generuje losową liczbę całkowitą w określonym zakresie (włącznie na obu końcach. Na przykład może to spowodować zwrócenie `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: minimum<br /><br /> **Opis**: wymagane. Najmniejsza liczba całkowita, która może zostać zwrócona.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Maksymalna<br /><br /> **Opis**: wymagane. Największa liczba całkowita, która może zostać zwrócona.|  
  
## <a name="date-functions"></a>Funkcje daty  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|utcnow|Zwraca bieżącą sygnaturę czasową jako ciąg. Na przykład `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: format<br /><br /> **Opis**: opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|AddSeconds|Dodaje liczbę całkowitą sekund do przekazaną sygnaturę czasową ciągu. Liczba sekund może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: s<br /><br /> **Opis**: wymagane. Liczba sekund do dodania. Może być ujemna, aby odjąć liczbę sekund.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: format<br /><br /> **Opis**: opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|addminut|Dodaje liczbę całkowitą minut do przekazaną sygnaturę czasową ciągu. Liczba minut może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: minuty<br /><br /> **Opis**: wymagane. Liczba minut do dodania. Może być ujemna, aby odjąć minuty.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: format<br /><br /> **Opis**: opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|addgodz.|Dodaje liczbę całkowitą godzin do przekazaną sygnaturę czasową ciągu. Liczba godzin może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: godziny<br /><br /> **Opis**: wymagane. Liczba godzin do dodania. Może być ujemna do odejmowania godzin.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: format<br /><br /> **Opis**: opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|Liczba dni|Dodaje liczbę całkowitą dni do przekazaną sygnaturę czasową ciągu. Liczba dni może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: znacznik czasu<br /><br /> **Opis**: wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: dni<br /><br /> **Opis**: wymagane. Liczba dni do dodania. Może być ujemna, aby odjąć liczbę dni.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: format<br /><br /> **Opis**: opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|formatDateTime|Zwraca ciąg w formacie daty. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br />Aby sformatować datę w "RRRR/MM/DD", użyj formatDateTime (UtcNow (), "RRRR/MM/DD").</br>Aby dołączyć nazwę do daty, użyj @concat ("foo-", "/", formatDateTime (UtcNow (), "RRRR/MM/DD").<br><br> **Numer parametru**: 1<br /><br /> **Nazwa**: Data<br /><br /> **Opis**: wymagane. Ciąg, który zawiera datę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: format<br /><br /> **Opis**: opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o"). |  

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę zmiennych systemowych, których można używać w wyrażeniach, zobacz [zmienne systemowe](control-flow-system-variables.md).
