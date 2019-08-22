---
title: Wyrażenie i funkcje w Azure Data Factory | Microsoft Docs
description: Ten artykuł zawiera informacje o wyrażeniach i funkcjach, których można użyć podczas tworzenia jednostek fabryki danych.
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
ms.openlocfilehash: 533d0c7461530a00e6f640ee53c0c87d336e799d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876312"
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
Wyrażenia mogą znajdować się w dowolnym miejscu w wartości ciągu JSON i zawsze dawać inną wartość JSON. Jeśli wartość JSON jest wyrażeniem, treść wyrażenia jest wyodrębniana przez usunięcie znaku (\@). Jeśli ciąg literału jest wymagany \@, który zaczyna się od, należy użyć polecenia. \@ \@ W poniższych przykładach pokazano, jak są oceniane wyrażenia.  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|wejściowe|Znaki "Parameters" są zwracane.|  
|"parametry [1]"|Znaki "parametry [1]" są zwracane.|  
|"\@\@"|Zostanie zwrócony 1 ciąg znaków zawierający element\@"".|  
|" \@"|Zostanie zwrócony ciąg 2 znaków zawierający znak \@"".|  
  
 Wyrażenia mogą być również wyświetlane wewnątrz ciągów, przy użyciu funkcji zwanej *interpolacją ciągu* , w `@{ ... }`której wyrażenia są opakowane. Na przykład: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Przy użyciu interpolacji ciągów wynik jest zawsze ciągiem. Załóżmy, że zostały `myNumber` zdefiniowane `42` jako `myString` i `foo`:  
  
|Wartość JSON|Wynik|  
|----------------|------------|  
|"\@potok (). Parameters. substring"| Zwraca `foo` jako ciąg.|  
|"\@{Pipeline (). Parameters.| Zwraca `foo` jako ciąg.|  
|"\@potok (). Parameters. mój numer"| Zwraca `42` jako *liczbę*.|  
|"\@{potok (). Parameters}"| Zwraca `42` jako *ciąg*.|  
|"Odpowiedź to: @ {potoku (). Parameters.! Number}"| Zwraca ciąg `Answer is: 42`.|  
|"\@concat (odpowiedź:", String (potok (). Parameters.-Number)) "| Zwraca ciąg.`Answer is: 42`|  
|"Odpowiedź to: \@ \@{Pipeline (). Parameters.! Number}"| Zwraca ciąg `Answer is: @{pipeline().parameters.myNumber}`.|  
  
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

  
## <a name="functions"></a>Funkcje  
 Możesz wywoływać funkcje w wyrażeniach. Poniższe sekcje zawierają informacje o funkcjach, których można użyć w wyrażeniu.  

## <a name="string-functions"></a>Funkcje ciągów  
 Poniższe funkcje dotyczą tylko ciągów. Można również użyć szeregu funkcji kolekcji dla ciągów.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|Concat|Łączy każdą liczbę ciągów razem. Na przykład jeśli parametr1 to `foo,` następujące wyrażenie zwróci `somevalue-foo-somevalue`wartość:`concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: Ciąg *n*<br /><br /> **Opis**: Wymagana. Ciągi, które mają zostać połączone w jeden ciąg.|  
|podciąg|Zwraca podzbiór znaków z ciągu. Na przykład następujące wyrażenie:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> typu<br /><br /> `foo`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. Ciąg, z którego jest pobierany podciąg.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Indeks początkowy<br /><br /> **Opis**: Wymagane. Indeks lokalizacji podciągu rozpoczynającego się w parametrze 1. Indeks początkowy jest liczony od zera. <br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: Długość<br /><br /> **Opis**: Wymagany. Długość podciągu.|  
|stępować|Zastępuje ciąg z danym ciągiem. Na przykład wyrażenie:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> typu<br /><br /> `the new string`<br /><br /> **Numer parametru**: 1<br /><br /> **Name**: ciąg<br /><br /> **Opis**: Wymagany.  Jeśli parametr 2 zostanie znaleziony w parametrze 1, ciąg wyszukiwany dla parametru 2 i zaktualizowany przy użyciu parametru 3.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Stary ciąg<br /><br /> **Opis**: Wymagany. Ciąg, który ma zostać zastąpiony parametrem 3, jeśli dopasowanie zostanie znalezione w parametrze 1<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: Nowy ciąg<br /><br /> **Opis**: Wymagana. Ciąg, który jest używany do zastępowania ciągu w parametrze 2 w przypadku znalezienia dopasowania w parametrze 1.|  
|ident| Generuje unikatowy ciąg globalny (alias. guid). Na przykład następujące dane wyjściowe mogą zostać wygenerowane `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. Pojedynczy specyfikator formatu, który wskazuje [sposób formatowania wartości tego identyfikatora GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Parametr formatu może mieć wartość "N", "D", "B", "P" lub "X". Jeśli format nie zostanie podany, używany jest "D".|  
|toLower|Konwertuje ciąg na małe litery. Na przykład następujące zwraca `two by two is four`:`toLower('Two by Two is Four')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagana. Ciąg do przekonwertowania na niższą wielkość liter. Jeśli znak w ciągu nie ma odpowiednika małymi literami, jest on uwzględniany niezmieniony w zwracanym ciągu.|  
|toUpper|Konwertuje ciąg na wielkie litery. Na przykład następujące wyrażenie zwraca `TWO BY TWO IS FOUR`:`toUpper('Two by Two is Four')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg do przekonwertowania na wielkie litery. Jeśli znak w ciągu nie ma wielkiej litery, jest on uwzględniany niezmieniony w zwracanym ciągu.|  
|indexof|Odszukaj indeks wartości w przypadku ciągu bez uwzględniania wielkości liter. Indeks jest liczony od zera. Na przykład następujące wyrażenie zwraca `7`:`indexof('hello, world.', 'world')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagana. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagana. Wartość do przeszukania indeksu.|  
|lastindexof|Odszukaj ostatni indeks wartości w przypadku ciągu bez uwzględniania wielkości liter. Indeks jest liczony od zera. Na przykład następujące wyrażenie zwraca `3`:`lastindexof('foofoo', 'foo')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. Wartość do przeszukania indeksu.|  
|StartsWith|Sprawdza, czy ciąg rozpoczyna się od wartości bez uwzględniania wielkości liter. Na przykład następujące wyrażenie zwraca `true`:`startswith('hello, world', 'hello')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagana. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagana. Wartość, z którą może zaczynać się ciąg.|  
|EndsWith|Sprawdza, czy ciąg jest zakończony bez uwzględniania wielkości liter. Na przykład następujące wyrażenie zwraca `true`:`endswith('hello, world', 'world')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. Ciąg, który może zawierać wartość.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Wartość, z którą może kończyć się ciąg.|  
|split|Dzieli ciąg za pomocą separatora. Na przykład następujące wyrażenie zwraca `["a", "b", "c"]`:`split('a;b;c',';')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. Ciąg, który jest dzielony.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. Separator.|  
  
  
## <a name="collection-functions"></a>Funkcje kolekcji  
 Funkcje te działają nad kolekcjami, takimi jak tablice, ciągi i czasami słowniki.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|zawiera|Zwraca wartość PRAWDA, jeśli słownik zawiera klucz, lista zawiera wartość lub ciąg zawiera podciąg. Na przykład następujące wyrażenie zwraca`true:``contains('abacaba','aca')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: W kolekcji<br /><br /> **Opis**: Wymagana. Kolekcja do przeszukiwania.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Znajdź obiekt<br /><br /> **Opis**: Wymagany. Obiekt, który ma znajdować się wewnątrz **kolekcji**.|  
|length|Zwraca liczbę elementów w tablicy lub ciągu. Na przykład następujące wyrażenie zwraca `3`:`length('abc')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagana. Kolekcja, w której ma zostać pobrana długość.|  
|ciągiem|Zwraca wartość true, jeśli obiekt, tablica lub ciąg są puste. Na przykład następujące wyrażenie zwraca `true`:<br /><br /> `empty('')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagane. Kolekcja, aby sprawdzić, czy jest pusta.|  
|część wspólną|Zwraca pojedynczą tablicę lub obiekt ze wspólnymi elementami między tablicami lub obiektami, do których przechodzą. Na przykład ta funkcja zwraca `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametry funkcji mogą być zestawem obiektów lub zestawem tablic (a nie ich mieszaniną). Jeśli istnieją dwa obiekty o tej samej nazwie, ostatni obiekt z tą nazwą pojawia się w obiekcie końcowym.<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: Kolekcja *n*<br /><br /> **Opis**: Wymagany. Kolekcje do obliczenia. Obiekt musi znajdować się we wszystkich kolekcjach, które przekazały, aby były wyświetlane w wyniku.|  
|Unii|Zwraca pojedynczą tablicę lub obiekt ze wszystkimi elementami, które znajdują się w tablicy lub obiekcie przekazaną do niego. Na przykład ta funkcja zwraca`[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametry funkcji mogą być zestawem obiektów lub zestawem tablic (a nie ich mieszaniną). Jeśli istnieją dwa obiekty o tej samej nazwie w końcowym danych wyjściowych, ostatni obiekt z tą nazwą pojawia się w obiekcie końcowym.<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: Kolekcja *n*<br /><br /> **Opis**: Wymagany. Kolekcje do obliczenia. Obiekt, który pojawia się w dowolnej kolekcji, pojawi się w wyniku.|  
|pierwszego|Zwraca pierwszy element w tablicy lub przekazaniu. Na przykład ta funkcja zwraca `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagana. Kolekcja, z której ma zostać pożądany pierwszy obiekt.|  
|ostatniego|Zwraca ostatni element w tablicy lub przekazaniu ciągu. Na przykład ta funkcja zwraca `3`:<br /><br /> `last('0123')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagana. Kolekcja, z której ma zostać pożądany ostatni obiekt.|  
|czasochłonn|Zwraca pierwsze elementy **Count** z tablicy lub przekazaną wartość, na przykład ta funkcja zwraca `[1, 2]`:`take([1, 2, 3, 4], 2)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagana. Kolekcja, w której mają zostać uwzględnione pierwsze obiekty **Count** z.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Count<br /><br /> **Opis**: Wymagany. Liczba obiektów do pobrania z **kolekcji**. Musi to być dodatnia liczba całkowita.|  
|pomiń|Zwraca elementy w tablicy zaczynające się od **liczby**indeksów, na przykład ta funkcja zwraca `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Collection<br /><br /> **Opis**: Wymagany. Kolekcja, w której mają zostać pominięte pierwsze obiekty **Count** z.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Count<br /><br /> **Opis**: Wymagana. Liczba obiektów, które mają zostać usunięte z przodu **kolekcji**. Musi to być dodatnia liczba całkowita.|  
  
## <a name="logical-functions"></a>Funkcje logiczne  
 Te funkcje są przydatne w warunkach, ale mogą służyć do szacowania dowolnego typu logiki.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|równa się|Zwraca wartość true, jeśli dwie wartości są równe. Na przykład jeśli parametr1 to foo, następujące wyrażenie zwróci `true`wartość:`equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt, który ma zostać porównany z **obiektem 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt, który ma zostać porównany z **obiektem 1**.|  
|mniej|Zwraca wartość true, jeśli pierwszy argument jest mniejszy od drugiego. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `true`:`less(10,100)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt, aby sprawdzić, czy jest on mniejszy niż **obiekt 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt, który ma być sprawdzany, jeśli jest większy niż **obiekt 1**.|  
|lessOrEquals|Zwraca wartość true, jeśli pierwszy argument jest mniejszy lub równy drugiemu. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `true`:`lessOrEquals(10,10)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Obiekt 1<br /><br /> **Opis**: Wymagany. Obiekt do sprawdzenia, czy jest mniejszy lub równy obiektowi **2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt do sprawdzenia, czy jest on większy niż lub równy **obiektowi 1**.|  
|mniejszą|Zwraca wartość true, jeśli pierwszy argument jest większy od drugiego. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `false`:`greater(10,10)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Obiekt 1<br /><br /> **Opis**: Wymagane. Obiekt do sprawdzenia, czy jest większy niż **obiekt 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagane. Obiekt, który ma być sprawdzany, jeśli jest mniejszy niż **obiekt 1**.|  
|greaterOrEquals|Zwraca wartość true, jeśli pierwszy argument jest większy lub równy drugiemu. Zwróć uwagę, że wartości mogą być tylko typu Integer, float lub String. Na przykład następujące wyrażenie zwraca `false`:`greaterOrEquals(10,100)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Obiekt 1<br /><br /> **Opis**: Wymagane. Obiekt do sprawdzenia, czy jest większy lub równy **obiektowi 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Obiekt 2<br /><br /> **Opis**: Wymagany. Obiekt do sprawdzenia, czy jest on mniejszy niż lub równy **obiektowi 1**.|  
|i|Zwraca wartość true, jeśli oba parametry mają wartość true. Oba argumenty muszą być wartością logiczną. Następujące zwraca `false`:`and(greater(1,10),equals(0,0))`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Wartość logiczna 1<br /><br /> **Opis**: Wymagane. Pierwszy argument, który musi być `true`.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Wartość logiczna 2<br /><br /> **Opis**: Wymagana. Drugi argument musi być `true`.|  
|lub|Zwraca wartość true, jeśli jeden z parametrów ma wartość true. Oba argumenty muszą być wartością logiczną. Następujące zwraca `true`:`or(greater(1,10),equals(0,0))`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Wartość logiczna 1<br /><br /> **Opis**: Wymagane. Pierwszy argument, który może być `true`.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Wartość logiczna 2<br /><br /> **Opis**: Wymagana. Drugi argument może być `true`.|  
|nie|Zwraca wartość true, jeśli parametr `false`jest. Oba argumenty muszą być wartością logiczną. Następujące zwraca `true`:`not(contains('200 Success','Fail'))`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Boolean<br /><br /> **Opis**: Zwraca wartość true, jeśli parametr `false`jest. Oba argumenty muszą być wartością logiczną. Następujące zwraca `true`:`not(contains('200 Success','Fail'))`|  
|if|Zwraca określoną wartość na podstawie tego, czy podane wyrażenie powoduje wyniki `true` w `false`lub.  Na przykład następujące zwraca `"yes"`:`if(equals(1, 1), 'yes', 'no')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Wyrażenie<br /><br /> **Opis**: Wymagane. Wartość logiczna określająca, która wartość jest zwracana przez wyrażenie.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Prawda<br /><br /> **Opis**: Wymagana. Wartość, która ma zostać zwrócona, `true`Jeśli wyrażenie jest.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: False<br /><br /> **Opis**: Wymagane. Wartość, która ma zostać zwrócona, `false`Jeśli wyrażenie jest.|  
  
## <a name="conversion-functions"></a>Funkcje konwersji  
 Te funkcje są używane do konwersji między poszczególnymi typami natywnymi w języku:  
  
-   ciąg  
  
-   integer  
  
-   float  
  
-   boolean  
  
-   Tablice  
  
-   słownik  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|int|Przekonwertuj parametr na liczbę całkowitą. Na przykład następujące wyrażenie zwraca 100 jako liczbę, a nie ciąg:`int('100')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Value<br /><br /> **Opis**: Wymagana. Wartość, która jest konwertowana na liczbę całkowitą.|  
|ciąg|Przekonwertuj parametr na ciąg. Na przykład następujące wyrażenie zwraca `'10'`:  `string(10)`Możesz również przekonwertować obiekt na ciąg, na przykład jeśli parametr **foo** jest obiektem z jedną właściwością `bar : baz`, następujące zwróci `{"bar" : "baz"}``string(pipeline().parameters.foo)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Value<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na ciąg.|  
|json|Przekonwertuj parametr na wartość typu JSON. Jest przeciwieństwem ciągu (). Na przykład następujące wyrażenie zwraca `[1,2,3]` jako tablicę, a nie ciąg:<br /><br /> `json('[1,2,3]')`<br /><br /> Analogicznie, można przekonwertować ciąg na obiekt. Na przykład `json('{"bar" : "baz"}')` zwraca:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagana. Ciąg, który jest konwertowany na wartość typu natywnego.<br /><br /> Funkcja JSON obsługuje również dane wejściowe XML. Na przykład wartość parametru:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> jest konwertowany na następujący kod JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Przekonwertuj argument parametru na liczbę zmiennoprzecinkową. Na przykład następujące wyrażenie zwraca `10.333`:`float('10.333')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Value<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na liczbę zmiennoprzecinkową.|  
|bool|Przekonwertuj parametr na wartość logiczną. Na przykład następujące wyrażenie zwraca `false`:`bool(0)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Value<br /><br /> **Opis**: Wymagany. Wartość, która zostanie przekonwertowana na wartość logiczną.|  
|łączonych|Zwraca pierwszy obiekt o wartości innej niż null w argumentach przekazane. Uwaga: pusty ciąg nie ma wartości null. Na przykład jeśli nie zdefiniowano parametrów 1 i 2, zwraca `fallback`:`coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Numer parametru**: 1... *n*<br /><br /> **Nazwa**: Obiekt*n*<br /><br /> **Opis**: Wymagane. Obiekty do sprawdzenia `null`.|  
|base64|Zwraca reprezentację Base64 ciągu wejściowego. Na przykład następujące wyrażenie zwraca `c29tZSBzdHJpbmc=`:`base64('some string')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Ciąg 1<br /><br /> **Opis**: Wymagane. Ciąg, który ma zostać zakodowany w reprezentacji Base64.|  
|base64ToBinary|Zwraca binarną reprezentację ciągu zakodowanego algorytmem Base64. Na przykład następujące wyrażenie zwraca reprezentację binarną pewnego ciągu: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Zakodowany ciąg Base64.|  
|base64ToString|Zwraca reprezentację ciągu zakodowanego ciągu based64. Na przykład następujące wyrażenie zwraca ciąg: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagana. Zakodowany ciąg Base64.|  
|Binary|Zwraca binarną reprezentację wartości.  Na przykład następujące wyrażenie zwraca binarną reprezentację pewnego ciągu:`binary(‘some string’).`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Value<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na format binarny.|  
|dataUriToBinary|Zwraca binarną reprezentację identyfikatora URI danych. Na przykład następujące wyrażenie zwraca reprezentację binarną pewnego ciągu:`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. Identyfikator URI danych do przekonwertowania na reprezentację binarną.|  
|dataUriToString|Zwraca ciąg reprezentujący identyfikator URI danych. Na przykład następujące wyrażenie zwraca ciąg:`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br />**Opis**: Wymagana. Identyfikator URI danych do przekonwertowania na reprezentację ciągu.|  
|dataUri|Zwraca identyfikator URI danych wartości. Na przykład następujące wyrażenie zwraca dane:`text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Numer parametru**: 1<br /><br />**Nazwa**: Value<br /><br />**Opis**: Wymagana. Wartość do przekonwertowania na identyfikator URI danych.|  
|decodeBase64|Zwraca ciąg reprezentujący wejściowy ciąg based64. Na przykład następujące wyrażenie zwraca `some string`:`decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Zwraca ciąg reprezentujący wejściowy ciąg based64.|  
|encodeURIComponent —|URL — wyprowadza ciąg, który został przesłany. Na przykład następujące wyrażenie zwraca `You+Are%3ACool%2FAwesome`:`encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagany. Ciąg do niebezpiecznego znaku w adresie URL.|  
|decodeUriComponent|Unadres URL — wyprowadza ciąg, który jest przesyłany. Na przykład następujące wyrażenie zwraca `You Are:Cool/Awesome`:`encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. Ciąg, w którym ma zostać zdekodowany adres URL-niebezpieczne znaki z.|  
|decodeDataUri|Zwraca binarną reprezentację ciągu identyfikatora URI danych wejściowych. Na przykład następujące wyrażenie zwraca reprezentację `some string`binarną:`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br /> **Opis**: Wymagane. DataURI do dekodowania w reprezentacji binarnej.|  
|uriComponent|Zwraca reprezentację wartości z zakodowaną URI. Na przykład następujące wyrażenie zwraca`You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Szczegóły parametru: Liczba 1, nazwa: Ciąg, opis: Wymagana. Ciąg do kodowania identyfikatora URI.|  
|uriComponentToBinary|Zwraca binarną reprezentację ciągu zakodowanego w identyfikatorze URI. Na przykład następujące wyrażenie zwraca binarną reprezentację `You Are:Cool/Awesome`:`uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: String<br /><br />**Opis**: Wymagany. Zakodowany ciąg identyfikatora URI.|  
|uriComponentToString|Zwraca ciąg reprezentujący ciąg zakodowany w identyfikatorze URI. Na przykład następujące wyrażenie zwraca `You Are:Cool/Awesome`:`uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Numer parametru**: 1<br /><br />**Nazwa**: String<br /><br />**Opis**: Wymagana. Zakodowany ciąg identyfikatora URI.|  
|xml|Zwraca reprezentację XML wartości. Na przykład następujące wyrażenie zwraca zawartość XML reprezentowaną przez `'\<name>Alan\</name>'`:. `xml('\<name>Alan\</name>')` Funkcja XML obsługuje również dane wejściowe obiektu JSON. Na przykład parametr `{ "abc": "xyz" }` jest konwertowany na zawartość XML`\<abc>xyz\</abc>`<br /><br /> **Numer parametru**: 1<br /><br />**Nazwa**: Value<br /><br />**Opis**: Wymagany. Wartość do przekonwertowania na format XML.|  
|Lokalizacja|Zwraca tablicę węzłów XML pasującą do wyrażenia XPath wartości zwracanej przez wyrażenie XPath.<br /><br />  **Przykład 1**<br /><br /> Załóżmy, że wartość parametru "P1" to ciąg reprezentujący następujący kod XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ten kod:`xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> zwróci<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> określić<br /><br /> 2. Ten kod:`xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> zwróci<br /><br /> `13`<br /><br /> <br /><br /> **Przykład 2**<br /><br /> Uwzględniając następującą zawartość XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Ten kod:`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> lub<br /><br /> 2. Ten kod:`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> typu<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> i<br /><br /> 3. Ten kod:`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> typu<br /><br /> ``bar``<br /><br /> **Numer parametru**: 1<br /><br />**Nazwa**: Xml<br /><br />**Opis**: Wymagany. KOD XML, dla którego ma zostać obliczone wyrażenie XPath.<br /><br /> **Numer parametru**: 2<br /><br />**Nazwa**: XPath<br /><br />**Opis**: Wymagane. Wyrażenie XPath, które ma zostać obliczone.|  
|array|Przekonwertuj parametr na tablicę.  Na przykład następujące wyrażenie zwraca `["abc"]`:`array('abc')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Value<br /><br /> **Opis**: Wymagany. Wartość, która jest konwertowana na tablicę.|
|createArray|Tworzy tablicę z parametrów.  Na przykład następujące wyrażenie zwraca `["a", "c"]`:`createArray('a', 'c')`<br /><br /> **Numer parametru**: 1... Azotan<br /><br /> **Nazwa**: Wszystkie n<br /><br /> **Opis**: Wymagane. Wartości, które mają zostać połączone do tablicy.|

## <a name="math-functions"></a>Funkcje matematyczne  
 Te funkcje mogą być używane dla każdego typu liczb: **liczb całkowitych** i **zmiennoprzecinkowych**.  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|dodaj|Zwraca wynik dodania dwóch liczb. Na przykład ta funkcja zwraca `20.333`:`add(10,10.333)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Summand 1<br /><br /> **Opis**: Wymagane. Liczba, która ma zostać dodana do **summand 2**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Summand 2<br /><br /> **Opis**: Wymagana. Liczba, która ma zostać dodana do **summand 1**.|  
|sub|Zwraca wynik odejmowania dwóch liczb. Na przykład ta funkcja zwraca: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Odjemna<br /><br /> **Opis**: Wymagany. Liczba, z której zostanie usunięty **odjemnik** .<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Odjemnik<br /><br /> **Opis**: Wymagane. Liczba, która ma zostać usunięta z **odjemna**.|  
|mul|Zwraca wynik mnożenia dwóch liczb. Na przykład następujące zwraca `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Multiplicand 1<br /><br /> **Opis**: Wymagana. Liczba, do której ma zostać pomnożona wartość **multiplicand 2** .<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Multiplicand 2<br /><br /> **Opis**: Wymagane. Liczba do pomnożenia **multiplicand 1** z.|  
|div|Zwraca wynik dzielenia dwóch liczb. Na przykład następujące zwraca `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Płacone<br /><br /> **Opis**: Wymagany. Liczba, która ma zostać podzielona przez **dzielnik**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Dzielnik<br /><br /> **Opis**: Wymagane. Liczba, przez którą ma zostać podzielona dywidenda.|  
|Funkcja|Zwraca wynik reszty po rozdziale dwóch liczb (modulo). Na przykład następujące wyrażenie zwraca `2`:<br /><br /> `mod(10,4)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Płacone<br /><br /> **Opis**: Wymagane. Liczba, która ma zostać podzielona przez **dzielnik**.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Dzielnik<br /><br /> **Opis**: Wymagane. Liczba, przez którą ma zostać podzielona dywidenda. Po rozdziale zostaje pozostała reszta.|  
|min|Istnieją dwa różne wzorce wywołania tej funkcji: `min([0,1,2])`W tym miejscu minimalna pobiera tablicę. To wyrażenie zwraca `0`wartość. Alternatywnie ta funkcja może przyjmować listę wartości rozdzielonych przecinkami:  `min(0,1,2)`Ta funkcja zwraca również wartość 0. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą, musi zawierać tylko cyfry.<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Kolekcja lub wartość<br /><br /> **Opis**: Wymagana. Może być tablicą wartości, aby znaleźć wartość minimalną lub pierwszą wartość zestawu.<br /><br /> **Numer parametru**: 2... *n*<br /><br /> **Nazwa**: Wartość *n*<br /><br /> **Opis**: Opcjonalny. Jeśli pierwszy parametr jest wartością, można przekazać dodatkowe wartości i zwrócić minimum wszystkich przekazanych wartości.|  
|maks.|Istnieją dwa różne wzorce wywołania tej funkcji:`max([0,1,2])`<br /><br /> W tym miejscu maksymalna przyjmuje tablicę. To wyrażenie zwraca `2`wartość. Alternatywnie ta funkcja może przyjmować listę wartości rozdzielonych przecinkami:  `max(0,1,2)`Ta funkcja zwraca wartość 2. Należy pamiętać, że wszystkie wartości muszą być liczbami, więc jeśli parametr jest tablicą, musi zawierać tylko cyfry.<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Kolekcja lub wartość<br /><br /> **Opis**: Wymagana. Może być tablicą wartości, aby znaleźć wartość maksymalną lub pierwszą wartość zestawu.<br /><br /> **Numer parametru**: 2... *n*<br /><br /> **Nazwa**: Wartość *n*<br /><br /> **Opis**: Opcjonalny. Jeśli pierwszy parametr jest wartością, można przekazać wartości dodatkowe i wszystkie przekazane wartości są zwracane.|  
|zakresu| Generuje tablicę liczb całkowitych, zaczynając od określonej liczby, i definiuje długość zwróconej tablicy. Na przykład ta funkcja zwraca `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Indeks początkowy<br /><br /> **Opis**: Wymagana. Jest to pierwsza liczba całkowita w tablicy.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Count<br /><br /> **Opis**: Wymagany. Liczba liczb całkowitych, które znajdują się w tablicy.|  
|Rand| Generuje losową liczbę całkowitą w określonym zakresie (włącznie na obu końcach. Na przykład może to zwrócić `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Minimalne<br /><br /> **Opis**: Wymagane. Najmniejsza liczba całkowita, która może zostać zwrócona.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Maksimum<br /><br /> **Opis**: Wymagana. Największa liczba całkowita, która może zostać zwrócona.|  
  
## <a name="date-functions"></a>Funkcje daty  
  
|Nazwa funkcji|Opis|  
|-------------------|-----------------|  
|utcnow|Zwraca bieżącą sygnaturę czasową jako ciąg. Na przykład `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|AddSeconds|Dodaje liczbę całkowitą sekund do przekazaną sygnaturę czasową ciągu. Liczba sekund może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Timestamp<br /><br /> **Opis**: Wymagany. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Sekundy<br /><br /> **Opis**: Wymagany. Liczba sekund do dodania. Może być ujemna, aby odjąć liczbę sekund.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|addminut|Dodaje liczbę całkowitą minut do przekazaną sygnaturę czasową ciągu. Liczba minut może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Timestamp<br /><br /> **Opis**: Wymagane. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Minuty<br /><br /> **Opis**: Wymagany. Liczba minut do dodania. Może być ujemna, aby odjąć minuty.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalny. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|addgodz.|Dodaje liczbę całkowitą godzin do przekazaną sygnaturę czasową ciągu. Liczba godzin może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Timestamp<br /><br /> **Opis**: Wymagany. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Godziny<br /><br /> **Opis**: Wymagany. Liczba godzin do dodania. Może być ujemna do odejmowania godzin.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalna. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|Liczba dni|Dodaje liczbę całkowitą dni do przekazaną sygnaturę czasową ciągu. Liczba dni może być dodatnia lub ujemna. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Timestamp<br /><br /> **Opis**: Wymagany. Ciąg, który zawiera godzinę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Dni<br /><br /> **Opis**: Wymagana. Liczba dni do dodania. Może być ujemna, aby odjąć liczbę dni.<br /><br /> **Numer parametru**: 3<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalna. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  
|formatDateTime|Zwraca ciąg w formacie daty. Wynik jest ciągiem w formacie ISO 8601 ("o") domyślnie, chyba że podano specyfikator formatu. Na przykład `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Numer parametru**: 1<br /><br /> **Nazwa**: Date<br /><br /> **Opis**: Wymagana. Ciąg, który zawiera datę.<br /><br /> **Numer parametru**: 2<br /><br /> **Nazwa**: Format<br /><br /> **Opis**: Opcjonalna. [Pojedynczy znak specyfikatora formatu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) lub [wzorzec formatu niestandardowego](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , który wskazuje, jak sformatować wartość tej sygnatury czasowej. Jeśli format nie zostanie podany, zostanie użyty format ISO 8601 ("o").|  

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę zmiennych systemowych, których można używać w wyrażeniach, zobacz [zmienne systemowe](control-flow-system-variables.md).
