---
title: 'Wykonaj skrypt języka R: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uruchomić kod języka R przy użyciu modułu uruchamiania skryptu języka R w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 710d64b445953ae3124830931c8cbb9315d32b83
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875710"
---
# <a name="execute-r-script"></a>Wykonywanie skryptu języka R

W tym artykule opisano, jak używać modułu **skryptu języka r** do uruchamiania kodu języka r w eksperymentie interfejsu wizualizacji.

Za pomocą języka R można wykonywać zadania, które nie są obecnie obsługiwane przez istniejące moduły, takie jak: 
- Tworzenie niestandardowych transformacji danych
- Używanie własnych metryk do szacowania prognoz
- Tworzenie modeli przy użyciu algorytmów, które nie są implementowane jako moduły autonomiczne w interfejsie wizualizacji

## <a name="r-version-support"></a>Obsługa wersji języka R

Interfejs wizualny usługi Azure Machine Learning używa dystrybucji CRAN (kompleksowa usługa R Archive Network). Obecnie używana wersja to CRAN 3.5.1.

## <a name="supported-r-packages"></a>Obsługiwane pakiety języka R

Środowisko języka R jest wstępnie instalowane z ponad 100 pakietów. Aby uzyskać pełną listę, zapoznaj się z sekcją [wstępnie zainstalowanych pakietów języka R](#pre-installed-r-packages).

Możesz również dodać następujący kod do dowolnego modułu **skryptu języka R** i zobaczyć zainstalowane pakiety.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalowanie pakietów języka R
Aby zainstalować dodatkowe pakiety języka R, użyj `install.packages()` metody. Pamiętaj, aby określić repozytorium CRAN. Pakiety są instalowane dla każdego modułu **wykonywania skryptu języka r** i nie są współużytkowane przez inne moduły **wykonywania skryptów języka r** .

Ten przykład pokazuje, jak zainstalować system ZOO:
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

## <a name="how-to-configure-execute-r-script"></a>Jak skonfigurować skrypt wykonywania skryptu języka R

Moduł **wykonywania skryptu języka R** zawiera przykładowy kod, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł **wykonywania skryptu języka R** , podaj zestaw wejść i kod do wykonania.

![R-module](media/module/execute-r-script.png)

Zestawy danych przechowywane w interfejsie wizualizacji są automatycznie konwertowane na ramkę z danymi języka R po załadowaniu tego modułu.

1.  Dodaj moduł **wykonywania skryptu języka R** do Twojego eksperymentu.

    > [!NOTE]
    > Wszystkie dane przesłane do modułu **skryptu Execute języka r** są konwertowane na format języka `data.frame` r.

1. Połącz wszystkie dane wejściowe, które są potrzebne przez skrypt. Wejścia są opcjonalne i mogą zawierać dane oraz dodatkowy kod R.

    * **Pozycję DataSet1**: Odwołuje się do pierwszego `dataframe1`danych wejściowych jako. Wejściowy zestaw danych musi być sformatowany jako CSV, TSV, ARFF lub połączony z zestawem danych Azure Machine Learning.

    * **Dataset2**: Odwołuje się do drugiego `dataframe2`danych wejściowych jako. Ten zestaw danych musi być również sformatowany jako plik CSV, TSV, ARFF lub jako zestaw danych Azure Machine Learning.

    * **Pakiet skryptu**: Trzecia wartość wejściowa akceptuje pliki ZIP. Spakowany plik może zawierać wiele plików i wiele typów plików.

1. W polu tekstowym **skrypt języka r** wpisz lub wklej prawidłowy skrypt języka r.

    Aby ułatwić rozpoczęcie pracy, pole tekstowe **skryptu języka R** jest wstępnie wypełnione z przykładowym kodem, który można edytować lub zamienić.

    * Skrypt musi zawierać funkcję o nazwie `azureml_main`, która jest punktem wejścia dla tego modułu.

    * Funkcja punktu wejścia może zawierać maksymalnie dwa argumenty wejściowe: `Param<dataframe1>` i`Param<dataframe2>`
 
    > [!NOTE]
    >  Istniejący kod R może potrzebować drobnych zmian do uruchomienia w eksperymentie interfejsu wizualizacji. Na przykład dane wejściowe, które podano w formacie CSV, powinny być jawnie konwertowane na zestaw danych, zanim będzie można używać go w kodzie. Typy danych i kolumn używane w języku R również różnią się w zależności od typu danych i kolumn używanych w interfejsie wizualizacji.

1.  **Losowy inicjator**: Wpisz wartość, która ma być używana w środowisku języka R jako wartość losowego inicjatora. Ten parametr jest odpowiednikiem wywołania `set.seed(value)` w kodzie R.  

1. Uruchom eksperyment.  

## <a name="results"></a>Wyniki

Moduły **Execute skryptu języka R** mogą zwracać wiele danych wyjściowych, ale muszą one być podane jako ramki z danymi języka r. Ramki danych są automatycznie konwertowane na zestaw danych interfejsu wizualizacji w celu zapewnienia zgodności z innymi modułami.

Standardowe komunikaty i błędy w języku R są zwracane do dziennika modułu.

## <a name="sample-scripts"></a>Przykładowe skrypty

Istnieje wiele sposobów na rozciągnięcie eksperymentu za pomocą niestandardowego skryptu języka R.  Ta sekcja zawiera przykładowy kod dla typowych zadań.


### <a name="add-r-script-as-an-input"></a>Dodaj skrypt języka R jako dane wejściowe

Moduł **wykonywania skryptu języka r** obsługuje dowolne pliki skryptów języka r jako dane wejściowe. W tym celu należy przekazać je do obszaru roboczego jako część pliku ZIP.

1. Aby przekazać plik ZIP zawierający kod R do obszaru roboczego, kliknij pozycję **Nowy**, kliknij pozycję **zestaw danych**, a następnie wybierz pozycję **z pliku lokalnego** i opcję **plik zip** .  

1. Sprawdź, czy spakowany plik jest dostępny na liście **zapisane zestawy danych** .

1.  Połącz zestaw danych z portem wejściowym **pakietu skryptu** .

1. Wszystkie pliki znajdujące się w pliku ZIP są dostępne podczas eksperymentu czasu wykonywania. 

    Jeśli plik pakietu skryptu zawierał strukturę katalogów, struktura jest zachowywana. Należy jednak zmienić kod, aby dołączyć do niego pakiet Directory **./Script** .

### <a name="process-data"></a>Przetwarzanie danych

Poniższy przykład pokazuje, jak skalować i znormalizować dane wejściowe:

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

### <a name="read-a-zip-file-as-input"></a>Odczytaj plik ZIP jako dane wejściowe

Ten przykład pokazuje, jak używać zestawu danych w pliku ZIP jako dane wejściowe do modułu **wykonywania skryptu języka R** .

1. Utwórz plik danych w formacie CSV, a następnie nadaj mu nazwę "plik. csv".
1. Utwórz plik ZIP i Dodaj plik CSV do archiwum.
1. Przekaż spakowany plik do obszaru roboczego Azure Machine Learning. 
1. Połącz zestaw danych z danymi wejściowymi z ScriptBundleem **wykonywania modułu skryptu języka R** .
1. Użycie poniższego kodu w celu odczytania danych CSV z pliku spakowanego.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikuj wiersze

Ten przykład pokazuje, jak replikować rekordy pozytywne w zestawie danych, aby zrównoważyć przykład:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Przekazywanie obiektów R między modułami wykonywania skryptu języka R

Można przekazać obiekty R między wystąpieniami modułu **wykonywania skryptu języka r** przy użyciu wewnętrznego mechanizmu serializacji. W tym przykładzie przyjęto założenie, że chcesz przenieść obiekt `A` R o nazwie między dwa moduły **wykonywania skryptu języka r** .

1. Dodaj do eksperymentu pierwszy moduł **skryptu wykonania R** , a następnie wpisz następujący kod w polu tekstowym **skryptu języka r** , aby utworzyć serializowany obiekt `A` jako kolumnę w tabeli danych wyjściowych modułu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Jawna konwersja na typ Integer jest wykonywana, ponieważ funkcja serializacji wyprowadza dane w formacie R `Raw` , co nie jest obsługiwane przez interfejs graficzny.

1. Dodaj drugie wystąpienie modułu **wykonywania skryptu języka R** i połącz je z portem wyjściowym poprzedniego modułu.

1. Wpisz następujący kod w polu tekstowym **skryptu języka R** , aby wyodrębnić obiekt `A` z tabeli danych wejściowych. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Wstępnie zainstalowane pakiety języka R

Bieżąca lista wstępnie zainstalowanych pakietów języka R dostępnych do użycia:

|              |            | 
|--------------|------------| 
| Pakiet      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| opiera         | 3.5.1      | 
| base64enc    | 0,1 – 3      | 
| BH           | 1.69.0-1   | 
| powiązanie        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| co górne       | 1.0-6      | 
| rozruch         | 1.3 — 22     | 
| Broom        | 0.5.2      | 
| wywołujący        | 3.2.0      | 
| użyciu        | 6.0 — 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| Interfejs          | 1.1.0      | 
| Program Cliper        | 0.6.0      | 
| Hosta      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
| colorspace   | 1.4-1      | 
| Compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| odsłon         | 3.3        | 
| Data. Table   | 1.12.2     | 
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
| obce      | 0,8-71     | 
| systemu           | 1.3.1      | 
| gdata        | 2.18.0     | 
| typy ogólne     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| Glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| elementów     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| Siatki         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| duża        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| Iteratory    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23 — 15    | 
| knitr        | 1,23       | 
| etykietowania     | 0,3        | 
| kratowych      | 0,20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1,5        | 
| promocji     | 1          | 
| MASOWA         | 7.3 — 51.4   | 
| Matrix       | 1.2 – 17     | 
| form      | 3.5.1      | 
| mgcv         | 1.8 — 28     | 
| MIME         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| Modeler       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| nnet         | 7.3 — 12     | 
| numDeriv     | 2016.8 — 1.1 | 
| OpenSSL      | 1.4        | 
| parallel     | 3.5.1      | 
| słup       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| wykonywane     | 1.2.2      | 
| iloczyn           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1,5 – 7      | 
| quantmod     | 0.4 — 15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6 — 14     | 
| RColorBrewer | 1.1 — 2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| element odczytujący        | 1.3.1      | 
| readxl       | 1.3.1      | 
| przepisy      | 0.1.5      | 
| dopasowanie      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1.0 — 7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| skalowany       | 1.0.0      | 
| Selektor      | 0.4-1      | 
| przestrzennych      | 7.3 — 11     | 
| Krzywe      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| Statystyki        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| ciąg tekstowy      | 1.3.1      | 
| ratownicz     | 2.44 — 1.1   | 
| widoku          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043,102   | 
| tinytex      | 0,13       | 
| narzędzia        | 3.5.1      | 
| tseries      | 0.10-47    | 
| REMEDIATE          | 0.23-4     | 
| kodowania         | 1.1.4      | 
| uaktualniania        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0,3-2      | 
| z usługą        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| XTS          | 0,11-2     | 
| YAML         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| Zoo          | 1.8 – 6      | 

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 