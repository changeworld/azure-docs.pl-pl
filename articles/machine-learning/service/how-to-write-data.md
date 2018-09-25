---
title: Zapisywanie danych w zestawie Azure Machine Learning danych Prep SDK — Python
description: Więcej informacji na temat zapisywania danych przy użyciu zestawu SDK usługi Azure Machine Learning danych przedprodukcyjnym. Możesz zapisać dane w dowolnym momencie, w ramach przepływu danych i pliki we wszystkich naszych obsługiwanych lokalizacji (lokalnego systemu plików usługi Azure Blob Storage i usługi Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946769"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>Zapisywanie danych przy użyciu usługi Azure Machine Learning Prep zestawu SDK usługi Data
Możesz zapisać dane w dowolnym miejscu przepływu danych. Te operacje zapisu są dodawane jako kroki, aby wynikowe przepływu danych i są uruchamiane za każdym razem, gdy przepływ danych. Ponieważ nie ma ograniczeń ile napisać czynności są w potoku, to proste, zapisać wyniki pośrednie do rozwiązywania problemów lub zostać pobrana przez innych potokach. Należy zauważyć, że wykonywania każdego kroku zapisu powoduje pełne ściąganie danych przepływu danych. Na przykład przepływu danych przy użyciu trzech krokach zapisu odczytywać i przetwarzać trzy razy każdego rekordu w zestawie danych.

## <a name="writing-to-files"></a>Zapisywanie w plikach
Za pomocą [zestawu SDK usługi Azure Machine Learning danych Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py), możesz zapisać dane do plików w jednym z naszych obsługiwanych lokalizacji (lokalnego systemu plików usługi Azure Blob Storage i usługi Azure Data Lake Storage). Dane są zapisywane do wielu plików partycji umożliwia zapisywanie równoległej. Po zakończeniu zapisu pliku wartownik o nazwie powodzenia zostanie również wygenerowany. Pomaga to identyfikować po zakończeniu zapisu pośrednich bez konieczności oczekiwania dla całego potoku zakończyć.

Podczas uruchamiania przepływu danych platformie Spark, należy napisać do pustego folderu. przy próbie uruchomienia zapis do istniejącego folderu zakończy się niepowodzeniem. Upewnij się, że folder docelowy jest pusta lub inną lokalizację docelową dla każdego uruchomienia lub zapisu zakończy się niepowodzeniem.

Azure Machine Learning Prep zestawu SDK usługi Data obsługuje następujące formaty plików:
-   Pliki rozdzielane (CSV, TSV itp.)
-   Pliki parquet

Na przykład uruchomić ładowanie danych do przepływu danych. Firma Microsoft będzie ponownie użyć tych danych w różnych formatach.

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

|   |  Kolumna1 |    Kolumna2 | Kolumna3 | Kolumna4  |Column5   | Kolumna6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   Brak|       NIE|     NIE  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   Brak|       NIE|     NIE  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    Brak|   NIE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    Brak|   NIE| NIE| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    Brak|   NIE| NIE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    Brak|   NIE| NIE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   Brak|   NIE| NIE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    Brak|   NIE| NIE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    Brak|   NIE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    Brak|   NIE| SV|     |77000.0|   15500.0|    120.0|

## <a name="delimited-files"></a>Pliki rozdzielane
Wiersz poniżej tworzy nowego przepływu danych, kończąc je krokiem zapisu, ale nie przeprowadzono jeszcze rzeczywistego zapisu. Podczas przebiegów przepływu danych, zostanie wykonany zapis.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
Teraz Uruchom przepływ danych, który uruchamia operację zapisu.
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Kolumna1 |    Kolumna2 | Kolumna3 | Kolumna4  |Column5   | Kolumna6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BŁĄD |       NIE|     NIE  |   ENRS    |BŁĄD    |   BŁĄD |   BŁĄD|    
|   1|      10003.0 |   99999.0 |   BŁĄD |       NIE|     NIE  |   ENSO|       BŁĄD|        BŁĄD |BŁĄD|   
|   2|  10010.0|    99999.0|    BŁĄD |   NIE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BŁĄD |   NIE| NIE| |   BŁĄD|    BŁĄD|    BŁĄD|
|4| 10014.0|    99999.0|    BŁĄD |   NIE| NIE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BŁĄD |   NIE| NIE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BŁĄD |   NIE| NIE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BŁĄD |   NIE| NIE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BŁĄD |   NIE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BŁĄD |   NIE| SV|     |77000.0|   15500.0|    120.0|

Zapisanych danych zawiera kilka błędów w kolumnach liczbowych, ze względu na liczby, które nie zostały poprawnie przeanalizować. Podczas zapisywania do pliku CSV, te wartości null są zastępowane parametrami "ERROR" domyślnie. Można dodać parametry jako część Twojego zapisu wywołania i określenia ciąg wykorzystywany do reprezentowania wartości null.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Kolumna1 |    Kolumna2 | Kolumna3 | Kolumna4  |Column5   | Kolumna6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NIE|     NIE  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NIE|     NIE  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NIE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NIE| NIE| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NIE| NIE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NIE| NIE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NIE| NIE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NIE| NIE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NIE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NIE| SV|     |77000.0|   15500.0|    120.0|
## <a name="parquet-files"></a>Pliki parquet

 Podobnie jak `write_to_csv` powyżej, funkcja `write_to_parquet` zwraca nowego przepływu danych przy użyciu zapisu kroku Parquet, które zostaną wykonane podczas przebiegów przepływu danych.

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 Przeprowadzamy teraz przepływu danych, która jest wykonywana operacja zapisu.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
|   |  Kolumna1 |    Kolumna2 | Kolumna3 | Kolumna4  |Column5   | Kolumna6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NIE|     NIE  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NIE|     NIE  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NIE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NIE| NIE| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NIE| NIE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NIE| NIE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NIE| NIE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NIE| NIE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NIE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NIE| SV|     |77000.0|   15500.0|    120.0|
