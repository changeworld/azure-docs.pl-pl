---
title: 'Uruchom skrypt języka R: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu wykonywania skryptu języka R w usłudze Azure Machine Learning do uruchomienia kodu R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518055"
---
# <a name="execute-r-script"></a>Wykonywanie skryptu języka R

W tym artykule opisano sposób używania **wykonanie skryptu języka R** modułu do uruchomienia kodu języka R w eksperymencie interfejsu wizualnego.

Przy użyciu języka R należy wykonać zadania, które nie są obecnie obsługiwane przez istniejące moduły takich jak: 
- Tworzenie danych niestandardowych przekształceń
- Użyj własnych metryk, aby ocenić prognozy
- Tworzenie modeli przy użyciu algorytmów, które nie są implementowane jako autonomicznych modułów w interfejsie visual

## <a name="r-version-support"></a>Obsługa wersji języka R

Interfejs graficzny usługi Azure Machine Learning korzysta z dystrybucji (Comprehensive R Archive Network) z sieci CRAN języka r. Aktualnie używana wersja jest CRAN 3.5.1.

## <a name="supported-r-packages"></a>Obsługiwane pakiety języka R

Środowisko R jest wstępnie zainstalowane za pomocą ponad 100 pakietów. Aby uzyskać pełną listę, zobacz sekcję [pakiety języka R preinstalowany](#pre-installed-r-packages).

Można również dodać następujący kod do dowolnej **wykonanie skryptu języka R** modułu i zobaczyć zainstalowane pakiety.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalowanie pakietów języka R
Aby zainstalować dodatkowe pakiety R, użyj `install.packages()` metody. Pamiętaj określić repozytorium CRAN. Pakiety są instalowane dla każdego **wykonanie skryptu języka R** modułu, nie są współdzielone w innych **wykonanie skryptu języka R** modułów.

W tym przykładzie przedstawiono sposób instalowania Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>Jak skonfigurować wykonanie skryptu języka R

**Wykonanie skryptu języka R** moduł zawiera przykładowy kod, który można użyć jako punktu wyjścia. Aby skonfigurować **wykonanie skryptu języka R** modułu, udostępniają zestaw danych wejściowych i wykonywanie kodu.

![Moduł R](media/module/execute-r-script.png)

Zestawy danych przechowywanych w interfejs graficzny automatycznie są konwertowane na R ramki danych, gdy ładowany za pomocą tego modułu.

1.  Dodaj **wykonanie skryptu języka R** modułu do eksperymentu.

    > [!NOTE]
    > Wszystkie dane są przekazywane do **wykonanie skryptu języka R** modułu jest konwertowana na R `data.frame` formatu.

1. Połącz wszystkie dane wejściowe wymagane przez skrypt. Dane wejściowe są opcjonalne i może zawierać dane i dodatkowy kod R.

    * **Dataset1**: Odwoływać się do pierwszej danej wejściowej, które są dostępne jako `dataframe1`. Wejściowy zestaw danych musi być sformatowany jako woluminu CSV, TSV, ARFF lub możesz połączyć z zestawem danych usługi Azure Machine Learning.

    * **Dataset2**: Odwoływać się do drugiej danej wejściowej, które są dostępne jako `dataframe2`. Tego zestawu danych również musi być sformatowany jako plik CSV, TSV, plik ARFF lub jako zestaw danych usługi Azure Machine Learning.

    * **Skrypt pakietu**: Trzeci dane wejściowe akceptuje plików ZIP. Spakowany plik może zawierać wiele plików i wiele typów plików.

1. W **skrypt języka R** pole tekstowe, wpisz lub Wklej prawidłowy skrypt języka R.

    Aby pomóc Ci rozpocząć pracę, **skrypt języka R** pole tekstowe jest wstępnie wypełniony przykładowy kod, który można edytować lub zamienić.

    * Skrypt musi zawierać funkcję o nazwie `azureml_main`, który jest punktem wejścia dla tego modułu.

    * Funkcja punktu wejścia może zawierać maksymalnie dwa argumenty wejściowe: `Param<dataframe1>` i `Param<dataframe2>`
 
    > [!NOTE]
    >  Istniejący kod R może być konieczne drobne zmiany do uruchamiania w eksperymencie interfejsu wizualnego. Na przykład dane wejściowe, które należy podać w formacie CSV należy jawnie przekonwertować do zestawu danych, zanim użyjesz go w kodzie. Typy danych i kolumn w języku R różnią się także pod pewnymi względami z typów danych i kolumny, używany w interfejsie visual.

1.  **Inicjator losowy**: Wpisz wartość do użycia w środowisku R jako wartość losowa. Ten parametr jest równoważne z wywoływaniem `set.seed(value)` w kodzie R.  

1. Uruchom eksperyment.  

## <a name="results"></a>Wyniki

**Wykonanie skryptu języka R** moduły mogą zwrócić wiele wyjść, ale musi być podana jako ramki danych języka R. Ramki danych są automatycznie konwertowane na zestawy danych interfejs graficzny zgodność z innymi modułami.

Standardowe komunikaty i błędy z języka R są zwracane do modułu dziennika.

## <a name="sample-scripts"></a>Przykładowe skrypty

Istnieje wiele sposobów, które eksperymentu można rozszerzyć za pomocą niestandardowego skryptu języka R.  Ta sekcja zawiera przykładowy kod do wykonywania typowych zadań.


### <a name="add-r-script-as-an-input"></a>Dodaj skrypt języka R jako dane wejściowe

**Wykonanie skryptu języka R** Moduł obsługuje dowolne pliki skryptów języka R jako dane wejściowe. Aby to zrobić, należy można przekazać do swojego obszaru roboczego jako część pliku ZIP.

1. Aby przekazać plik ZIP zawierający kod R do swojego obszaru roboczego, kliknij przycisk **nowy**, kliknij przycisk **zestawu danych**, a następnie wybierz pozycję **z pliku lokalnego** i **pliku Zip**opcji.  

1. Sprawdź, czy plik z rozszerzeniem zip jest dostępna w **zapisane zestawów danych** listy.

1.  Łączenie zestawu danych na **pakietu skryptu** portem wejściowym modułu.

1. Wszystkie pliki, które są zawarte w pliku ZIP są dostępne podczas eksperymentu w czasie wykonywania. 

    Jeśli struktura katalogów znajdujących się w pliku pakietu skryptu, struktury są zachowywane. Jednak może zmieniać swój kod, aby dołączana katalogu **. / pakietu skryptu** do ścieżki.

### <a name="process-data"></a>Przetwarzanie danych

Poniższy przykład pokazuje, jak skalować i normalizacji danych wejściowych:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Odczytywanie pliku ZIP jako dane wejściowe

W tym przykładzie pokazano, jak używać zestawu danych w formie pliku ZIP jako dane wejściowe **wykonanie skryptu języka R** modułu.

1. Tworzenie pliku danych w formacie CSV, a następnie nadaj mu nazwę "mydatafile.csv".
1. Tworzenie pliku ZIP i Dodaj plik CSV do archiwum.
1. Przekaż plik zip do obszaru roboczego usługi Azure Machine Learning. 
1. Wynikowy zestaw danych, aby połączyć **ScriptBundle** wejściowego z sieci **wykonanie skryptu języka R** modułu.
1. Używając następującego kodu, aby odczytać dane w formacie CSV z pliku zip.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikowanie wierszy

W tym przykładzie pokazano, jak replikowanie dodatnią rekordów w zestawie danych do zrównoważenia przykładu:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Przekazać obiekty R między modułami wykonanie skryptu języka R

Można przekazać obiekty R między wystąpieniami **wykonanie skryptu języka R** modułu za pomocą mechanizmu wewnętrznego serializacji. W tym przykładzie założono, że chcesz przenieść obiektu języka R, o nazwie `A` między dwoma **wykonanie skryptu języka R** modułów.

1. Dodaj pierwszy **wykonanie skryptu języka R** modułu do eksperymentów i wpisz poniższy kod w **skrypt języka R** pole tekstowe, aby utworzyć obiekt Zserializowany `A` zgodnie z kolumną w module danych wyjściowych tabeli danych:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Jawna konwersja na typ Liczba całkowita jest wykonywana, ponieważ funkcja serializacji danych wyjściowych danych w języku R `Raw` formatu, który nie jest obsługiwana przez interfejs graficzny.

1. Dodawanie drugiego wystąpienia **wykonanie skryptu języka R** moduł i połączyć ją z portem wyjściowym modułu poprzedniego.

1. Wpisz następujący kod w **skrypt języka R** pole tekstowe, aby wyodrębnić obiektu `A` z tabeli danych wejściowych. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pakiety języka R wstępnie zainstalowane

Bieżącą listę wstępnie zainstalowane pakiety języka R jest dostępne do użycia:

|              |            | 
|--------------|------------| 
| Pakiet      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| podstawowy         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| rozruch         | 1.3-22     | 
| miotła        | 0.5.2      | 
| callr        | 3.2.0      | 
| Karetki        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| Interfejs wiersza polecenia          | 1.1.0      | 
| clipr        | 0.6.0      | 
| Klastra      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| kolorów   | 1.4-1      | 
| Kompilator     | 3.5.1      | 
| Kredka       | 1.3.4      | 
| curl         | 3.3        | 
| Data.TABLE   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0,14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| obcy      | 0.8-71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Typy ogólne     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| Przyklej         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| Grafiki     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| Siatka         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| Iteratory    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| Etykietowania     | 0.3        | 
| struktury      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1,5        | 
| Markdown     | 1          | 
| URZĄDZEŃ PAMIĘCI MASOWEJ         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| Metody      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| Słupka       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| Postęp     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| przepisy      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| język rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| skale       | 1.0.0      | 
| selectr      | 0.4-1      | 
| Przestrzenne      | 7.3-11     | 
| krzywe      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| Statystyki        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| pozostawanie w mocy     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| narzędzia        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| Utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 