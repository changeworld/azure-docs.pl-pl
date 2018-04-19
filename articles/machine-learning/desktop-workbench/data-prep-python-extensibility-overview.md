---
title: "Rozszerzalność języka Python za pomocą usługi Azure Machine Learning danych przygotowania | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera omówienie i szczegółowe przykłady rozszerzyć funkcjonalność Przygotowanie danych za pomocą kodu języka Python"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: cc1aef7ed7c4a7d03a7fa63e71c8c27aca10095a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparations-python-extensions"></a>Rozszerzenia języka Python przygotowań danych
Sposób wypełniania funkcji luki pomiędzy wbudowane funkcje usługi Azure Machine Learning danych przygotowania zawiera rozszerzalności na różnych poziomach. W tym dokumencie możemy konspektu rozszerzalność dzięki skrypt w języku Python. 

## <a name="custom-code-steps"></a>Kroki kod niestandardowy 
Przygotowań danych ma następujące kroki niestandardowe, w którym użytkownicy mogą zapisywać kodu:

* Plik czytnika *
* Moduł zapisujący *
* Dodaj kolumnę
* Filtr zaawansowany
* Przekształć przepływu danych
* Przekształć partycji

* Te kroki nie są obecnie obsługiwane w wykonywania Spark.

## <a name="code-block-types"></a>Typy blok kodu 
Dla każdej z tych kroków firma Microsoft obsługuje dwa typy bloku kodu. Po pierwsze firma Microsoft obsługuje wyrażenia języka Python systemu od zera, która zostanie wykonana, ponieważ jest. Po drugie firma Microsoft obsługuje moduł Python, gdzie nazywamy konkretną funkcję za pomocą znanych podpisu kodu, który podasz.

Na przykład można dodać nowej kolumny, która oblicza dziennika innej kolumny w dwóch sposobów:

Wyrażenie 

```python    
    math.log(row["Score"])
```

Moduł 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Przekształcenie Add Column w trybie modułu oczekuje można znaleźć funkcji o nazwie `newvalue` akceptuje zmienną wiersza i zwraca wartość w kolumnie. Ten moduł może zawierać żadnych ilość kodu języka Python z innych funkcji, Importy, itp.

W poniższych sekcjach omówiono szczegóły każdego punktu rozszerzenia. 

## <a name="imports"></a>Importy 
Użycie wyrażenia typu bloku, nadal możesz dodać **zaimportować** instrukcje w kodzie. Wszystkie muszą być zgrupowane w górnym wierszach kodu.

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
 
 
Jeśli używasz modułu typ bloku, można wykonać wszystkich zwykłe Python zasady przy użyciu **zaimportować** instrukcji. 

## <a name="default-imports"></a>Importy domyślne
Następujące instrukcje importu, są zawsze dołączone i można go użyć w kodzie. Nie trzeba ponownie zaimportować je. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Zainstaluj nowe pakiety
Aby użyć pakietu, który nie jest instalowany domyślnie, należy najpierw zainstalować go w środowiskach, które używa przygotowania danych. Musi odbywać się zarówno na komputerze lokalnym, jak i obiektów docelowych obliczeniowe, które ma zostać uruchomione tej instalacji.

Aby zainstalować pakiety w celu obliczeń, należy zmodyfikować plik conda_dependencies.yml znajdujące się w folderze aml_config w katalogu głównym projektu.

### <a name="windows"></a>Windows 
Aby znaleźć lokalizację w systemie Windows, Znajdź instalacji specyficzny dla aplikacji, Python i jego katalogu skryptów. Domyślna lokalizacja to:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Następnie uruchom jedno z następujących poleceń: 

`conda install <libraryname>` 

lub 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Aby znaleźć lokalizację na komputerze Mac, Znajdź instalacji specyficzny dla aplikacji, Python i jego katalogu skryptów. Domyślna lokalizacja to: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Następnie uruchom jedno z następujących poleceń: 

`./conda install <libraryname>`

lub 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Użyj niestandardowych modułów
W transformacji przepływu danych (skrypt) wpisz następujący kod języka Python

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Dodaj kolumny (skrypt), należy ustawić typ bloku kodu = moduł i wpisz następujący kod Python

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Do wykonywania różnych kontekstach (lokalny, Docker, Spark) wskaż ścieżkę bezwzględną we właściwym miejscu. Można użyć "os.getcwd() + relativePath", aby go zlokalizować.


## <a name="column-data"></a>Kolumny danych 
Kolumny danych jest możliwy z wiersza przy użyciu kropkowego lub notacji klucz wartość. Nie można uzyskać dostępu do nazw kolumn, które zawierają spacje lub znaki specjalne za pomocą kropkowego. `row` Zmiennej zawsze powinien być zdefiniowany w obu trybach rozszerzenia języka Python (modułu i wyrażenie). 

Przykłady 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Czytnik plików 
### <a name="purpose"></a>Przeznaczenie 
Punkt rozszerzenia pliku czytnika pozwala w pełni kontroli procesu czytania pliku do przepływu danych. System wywołuje kod i przekazuje na liście plików, które ma być przetwarzane. Kod musi utworzyć i zwracać Pandas dataframe. 

>[!NOTE]
>Ten punkt rozszerzenia nie działa w łączniku Spark. 


### <a name="how-to-use"></a>Jak stosować 
Możesz uzyskać dostępu do tego punktu rozszerzenia z **typu Open Source danych** kreatora. Wybierz **pliku** na pierwszej stronie, a następnie wybierz lokalizację pliku. Na **Wybierz parametry pliku** strony w **typ pliku** listy rozwijanej wybierz pozycję **niestandardowego pliku (skrypt)**. 

Podany kod jest dataframe Pandas, o nazwie "df", który zawiera informacje o pliki potrzebne do odczytu. Jeśli wybrano opcję Otwórz katalog, który zawiera wiele plików, dataframe zawiera więcej niż jeden wiersz.  

Dataframe ten zawiera następujące kolumny:

- Ścieżka: Plik do odczytu.
- PathHint: Określa, w którym znajduje się plik. Wartości: Lokalne, AzureBlobStorage i AzureDataLakeStorage.
- AuthenticationType: Typ uwierzytelniania używany do uzyskania dostępu do pliku. Wartości: None, SasToken i OAuthToken.
- AuthenticationValue: Zawiera None lub token do użycia.

### <a name="syntax"></a>Składnia 
Wyrażenie 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Moduł  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Składnik zapisywania 
### <a name="purpose"></a>Przeznaczenie 
Punkt rozszerzenia składnika zapisywania pozwala w pełni kontroli proces zapisywania danych z przepływu danych. System wywołuje kod i przekazuje w dataframe. Kod umożliwia dataframe zapisywać dane w dowolny sposób. 

>[!NOTE]
>Punkt rozszerzenia moduł zapisujący nie działa w łączniku Spark.


### <a name="how-to-use"></a>Jak stosować 
Aby dodać ten punkt rozszerzenia, należy za pomocą bloku przepływu danych zapisu (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu.

### <a name="syntax"></a>Składnia 
Wyrażenie

```python
    df.to_csv('c:\\temp\\output.csv')
```

Moduł

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Ten blok niestandardowych zapisu może istnieć środku listę kroków. Jeśli używasz modułu funkcji zapisu musi zwracać dataframe, będący w danych wejściowych kroku, który następuje. 

## <a name="add-column"></a>Dodaj kolumnę 
### <a name="purpose"></a>Przeznaczenie
Punkt rozszerzenia Add Column umożliwia zapisanie Python do obliczenia nowej kolumny. Kod zostanie zapisany ma dostęp do pełnego wiersza. Musi zwracać wartość nowej kolumny, dla każdego wiersza. 

### <a name="how-to-use"></a>Jak stosować
Aby dodać ten punkt rozszerzenia, należy za pomocą bloku Dodawanie kolumny (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu, jak również na **kolumny** menu kontekstowego. 

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
Punkt rozszerzenia filtr zaawansowany umożliwia zapisanie filtru niestandardowego. Masz dostęp do całego wiersza i kod musi zwracać wartość True (Dołącz wiersz) lub False (wykluczyć wiersza). 

### <a name="how-to-use"></a>Jak stosować
Aby dodać ten punkt rozszerzenia, należy za pomocą bloku zaawansowane filtru (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu. 

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
 

## <a name="transform-dataflow"></a>Przekształć przepływu danych
### <a name="purpose"></a>Przeznaczenie 
Punkt rozszerzenia przekształcenie przepływu danych umożliwia całkowicie przekształcenie przepływu danych. Masz dostęp do dataframe Pandas, która zawiera wszystkich kolumn i wierszy w przypadku przetwarzania. Kod musi zwracać dataframe Pandas, przy użyciu nowych danych. 

>[!NOTE]
>W języku Python wszystkie dane do załadowania do pamięci jest Pandas dataframe Jeśli to rozszerzenie jest używane. 
>
>W łączniku Spark wszystkie dane są zbierane na węźle pojedynczego procesu roboczego. Jeśli dane są bardzo duże, pracownik może zabraknąć pamięci. Ostrożnie korzystaj z go.

### <a name="how-to-use"></a>Jak stosować 
Aby dodać ten punkt rozszerzenia, należy za pomocą bloku przepływu danych przekształcania (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu. 
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
  

## <a name="transform-partition"></a>Przekształć partycji  
### <a name="purpose"></a>Przeznaczenie 
Punkt rozszerzenia przekształcenie partycji umożliwia przekształcanie partycji przepływu danych. Masz dostęp do dataframe Pandas, który zawiera wszystkich kolumn i wierszy dla tej partycji. Kod musi zwracać dataframe Pandas, przy użyciu nowych danych. 

>[!NOTE]
>W języku Python mogą wystąpić z jednej partycji lub wiele partycji, w zależności od rozmiaru danych. W łączniku Spark z którymi pracujesz dataframe, w której przechowywane są dane dla partycji w węźle danego pracownika. W obu przypadkach nie założono, że masz dostęp do całego zestawu danych. 


### <a name="how-to-use"></a>Jak stosować
Aby dodać ten punkt rozszerzenia, należy za pomocą bloku przekształcenie partycji (skrypt). Jest ona dostępna na najwyższym poziomie **przekształcenia** menu. 

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
W danych przygotowania istnieje pojęcie wartości błędów. 

Istnieje możliwość wystąpienia wartości błędów w kodzie języka Python niestandardowym. Są one wystąpień klasy Python o nazwie `DataPrepError`. Ta klasa jest zawijana wyjątek języka Python i ma kilka właściwości. Właściwości zawierają informacje dotyczące błędu, który wystąpił podczas przetwarzania oryginalnej wartości, a także oryginalnej wartości. 


### <a name="datapreperror-class-definition"></a>Definicja klasy DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Tworzenie DataPrepError w ramach Python przygotowań danych zwykle wygląda następująco: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Jak stosować 
Możliwe jest uruchomienie Python punkcie rozszerzenia do generowania DataPrepErrors jako wartości zwracane przy użyciu poprzedniej metody tworzenia. Jest znacznie bardziej prawdopodobne, że DataPrepErrors wystąpią podczas przetwarzania danych w punkcie rozszerzenia. W tym momencie niestandardowy kod języka Python musi obsługiwać DataPrepError jako prawidłowy typ danych.

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
