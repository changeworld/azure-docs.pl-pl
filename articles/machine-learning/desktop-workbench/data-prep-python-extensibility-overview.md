---
title: Rozszerzalność języka Python za pomocą usługi Azure Machine Learning danych przygotowań | Dokumentacja firmy Microsoft
description: Ten dokument zawiera omówienie i niektóre szczegółowe przykłady sposobu użycia kodu w języku Python, aby rozszerzyć funkcje operacji przygotowania danych
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ROBOTS: NOINDEX
ms.openlocfilehash: eceeeb30331031c51e5208e301441d17096b4a00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972984"
---
# <a name="data-preparations-python-extensions"></a>Rozszerzenia języka Python przygotowywanie danych

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Jako sposób wypełnianie luki między wbudowane funkcje usługi Azure Machine Learning danych przygotowań obejmuje rozszerzalności na różnych poziomach. W tym dokumencie możemy konturu rozszerzalności za pośrednictwem skryptu języka Python. 

## <a name="custom-code-steps"></a>Kroki kod niestandardowy 
Przygotowywanie danych składa się z następujących kroków niestandardowych, gdzie użytkownicy mogą wpisać kod:

* Dodaj kolumnę
* Filtr zaawansowany
* Przekształcanie przepływu danych
* Przekształcanie partycji

## <a name="code-block-types"></a>Typów bloków kodu 
Dla każdej z tych kroków firma Microsoft obsługuje dwa typy bloku kodu. Po pierwsze firma Microsoft obsługuje bez wyrażenia języka Python, który jest wykonywany, ponieważ jest. Po drugie firma Microsoft obsługuje moduł języka Python, którego wywołuje określoną funkcję za pomocą znanych podpisu w kodzie, który podasz.

Na przykład można dodać nową kolumnę, która oblicza dziennika innej kolumny w dwóch poniższych sposobów:

Wyrażenie 

```python    
    math.log(row["Score"])
```

Moduł 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Przekształcanie Add Column w trybie modułu spodziewa się znaleźć funkcji o nazwie `newvalue` , zmienna wiersza akceptuje i zwraca wartość kolumny. Ten moduł może zawierać żadnych ilość kodu Python za pomocą innych funkcji, importuje, itp.

Szczegółowe informacje o poszczególnych punktów rozszerzenia zostały omówione w poniższych sekcjach. 

## <a name="imports"></a>Importy 
Jeśli używasz typu blok wyrażenia, można dodać **zaimportować** instrukcji w kodzie. Wszystkie one muszą być zgrupowane w górnym wierszach kodu.

Popraw 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Błąd  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Jeśli używasz modułu typ bloku, możesz wykonać wszystkie zwykłe Python zasady przy użyciu **zaimportować** instrukcji. 

## <a name="default-imports"></a>Domyślne importowanie
Następujące instrukcje importu zawsze są dołączone i można używać w kodzie. Nie trzeba ponownie zaimportować je. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Instalowanie nowych pakietów
Aby korzystać z pakietu, który nie jest instalowane domyślnie, należy najpierw go zainstalować w środowiskach, przygotowywanie danych używane przez. Ta instalacja musi odbywać się zarówno na komputerze lokalnym, jak i na dowolnej obliczeniowych elementów docelowych, które chcesz uruchomić na.

Aby zainstalować pakiety w obliczeniowego elementu docelowego, musisz zmodyfikować plik conda_dependencies.yml znajdujący się w folderze aml_config w katalogu głównym projektu.

### <a name="windows"></a>Windows 
Aby znaleźć lokalizację w Windows, Znajdź instalacji specyficzny dla aplikacji języka Python i jego katalogu skryptów. Domyślna lokalizacja to:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Następnie uruchom jedno z następujących poleceń: 

`conda install <libraryname>` 

lub 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Aby znaleźć lokalizacji na komputerze Mac, znaleźć instalacji specyficzny dla aplikacji języka Python i jego katalogu skryptów. Domyślna lokalizacja to: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Następnie uruchom jedno z następujących poleceń: 

`./conda install <libraryname>`

lub 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Użyj niestandardowych modułów
W Przekształć przepływ danych (skrypt) należy napisać następujący kod języka Python

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Dodawanie kolumny (skrypt), należy ustawić typ bloku kodu = modułu i Zapisz poniższy kod języka Python

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Do wykonywania różnych kontekstach (lokalny, Docker, platformy Spark), punktu ścieżki bezwzględnej we właściwym miejscu. Można go znaleźć za pomocą "os.getcwd() + relativePath".


## <a name="column-data"></a>Dane w kolumnie 
Kolumna danych jest możliwy z wiersza przy użyciu notacji z kropką lub notacji pary klucz wartość. Nie można uzyskać dostępu do nazw kolumn, które zawierają spacje lub znaki specjalne przy użyciu notacji z kropką. `row` Zmiennej powinny być zawsze definiowane w obu trybach rozszerzenia języka Python (moduł i wyrażenia). 

Przykłady 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Dodaj kolumnę 
### <a name="purpose"></a>Przeznaczenie
Dodaj kolumnę punktu rozszerzenia umożliwia zapisanie języka Python, aby obliczyć nową kolumnę. Utworzony kod ma dostęp do pełnego wiersza. Musi ona zwrócona nowa wartość kolumny dla każdego wiersza. 

### <a name="how-to-use"></a>Jak stosować
Można dodać tego punktu rozszerzenia za pomocą bloku Dodaj kolumnę (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu, a także jak na **kolumny** menu kontekstowego. 

### <a name="syntax"></a>Składnia
Wyrażenie

```python
    math.log(row["Score"])
```

Moduł 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Filtr zaawansowany
### <a name="purpose"></a>Przeznaczenie 
Zaawansowany filtr punktu rozszerzenia umożliwia zapisanie niestandardowego filtru. Masz dostęp do całego wiersza, a kod musi zwracać wartość True (Dołącz wiersz), lub wartość FAŁSZ (Wyklucz wiersz). 

### <a name="how-to-use"></a>Jak stosować
Można dodać tego punktu rozszerzenia za pomocą bloku filtr zaawansowany (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu. 

### <a name="syntax"></a>Składnia

Wyrażenie

```python
    row["Score"] > 95
```

Moduł  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Przekształcanie przepływu danych
### <a name="purpose"></a>Przeznaczenie 
Punkt rozszerzenia Przekształć przepływ danych pozwala w całkowicie Przekształcanie przepływu danych. Masz dostęp do biblioteki Pandas ramkę danych zawierającą wszystkie kolumny i wiersze, które są przetwarzania. Kod musi zwracać Pandas ramkę danych przy użyciu nowych danych. 

>[!NOTE]
>W języku Python wszystkie dane, które ma być załadowany do pamięci jest w Pandas dataframe użycie tego rozszerzenia. 
>
>Platformy Spark wszystkie dane są zbierane na węźle pojedynczego procesu roboczego. Jeśli dane są bardzo duże, proces roboczy może zabraknąć pamięci. Użyj dokładnie.

### <a name="how-to-use"></a>Jak stosować 
Można dodać tego punktu rozszerzenia za pomocą bloku Przekształć przepływ danych (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu. 
### <a name="syntax"></a>Składnia 

Wyrażenie

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Moduł 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Przekształcanie partycji  
### <a name="purpose"></a>Przeznaczenie 
Punkt rozszerzenia Przekształcanie partycji umożliwia przekształcanie przepływu danych partycji. Masz dostęp do biblioteki Pandas ramkę danych zawierającą wszystkie kolumny i wiersze dla tej partycji. Kod musi zwracać Pandas ramkę danych przy użyciu nowych danych. 

>[!NOTE]
>W języku Python mogą wystąpić z jednej partycji lub wiele partycji, w zależności od rozmiaru danych. Platformy Spark pracujemy z użyciem ramkę danych, która przechowuje dane dla partycji w węźle danego pracownika. W obu przypadkach nie można zakładać, że masz dostęp do całego zestawu danych. 


### <a name="how-to-use"></a>Jak stosować
Można dodać tego punktu rozszerzenia za pomocą bloku Przekształcanie partycji (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu. 

### <a name="syntax"></a>Składnia 

Wyrażenie 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Moduł 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Wartości błędów  
W przygotowywanie danych istnieje pojęcie wartości błędów. 

Istnieje możliwość wystąpienia wartości błędów w kodzie niestandardowym języka Python. Są one wystąpień klasy języka Python o nazwie `DataPrepError`. Ta klasa jest zawijany wyjątek języka Python i ma kilka właściwości. Właściwości zawierają informacje dotyczące błędu, który wystąpił podczas przetwarzania oryginalnej wartości, a także oryginalną wartość. 


### <a name="datapreperror-class-definition"></a>Definicja klasy DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Tworzenie DataPrepError w ramach przygotowań danych w języku Python ogólnie wygląda następująco: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Jak stosować 
Możliwe jest uruchomienie języka Python w punkcie rozszerzenia do generowania DataPrepErrors jako wartości zwracane przy użyciu poprzedniej metody tworzenia. Jest znacznie bardziej prawdopodobne, że DataPrepErrors zostaną napotkane podczas przetwarzania danych w punkcie rozszerzenia. W tym momencie niestandardowy kod Python musi obsługiwać DataPrepError jako prawidłowego typu danych.

#### <a name="syntax"></a>Składnia 
Wyrażenie 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Moduł 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
