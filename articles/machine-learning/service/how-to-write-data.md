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
ms.openlocfilehash: 81344d388fbba0db034b8adb06adab6797ec2ce1
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166751"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Zapisywanie danych przy użyciu usługi Azure Machine Learning Prep zestawu SDK usługi Data
Możesz zapisać dane w dowolnym miejscu przepływu danych. Te operacje zapisu są dodawane jako kroki, aby wynikowe przepływu danych i są uruchamiane za każdym razem, gdy przepływ danych. Dane są zapisywane do wielu plików partycji umożliwia zapisywanie równoległej.

Ponieważ nie ma ograniczeń ile napisać czynności są w potoku, możesz łatwo dodawać zapisu dodatkowe kroki, aby uzyskać wyniki pośrednie, rozwiązywania problemów lub innych potokach. 

Każdorazowo po uruchomieniu kroku zapisu występuje pełną ściąganie danych przepływu danych. Na przykład przepływu danych przy użyciu trzech krokach zapisu odczytywać i przetwarzać trzy razy każdego rekordu w zestawie danych.

## <a name="supported-data-types-and-location"></a>Obsługiwane typy danych i lokalizacji

Obsługiwane są następujące formaty plików
-   Pliki rozdzielane (CSV, TSV itp.)
-   Pliki parquet

Za pomocą [zestaw python SDK usługi Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk), możesz zapisać dane do:
+ lokalny system plików
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Zagadnienia dotyczące platformy Spark
Podczas uruchamiania przepływu danych platformie Spark, należy napisać do pustego folderu. Przy próbie uruchomienia zapis do istniejącego folderu powoduje błąd. Upewnij się, że folder docelowy jest pusta lub inną lokalizację docelową dla każdego uruchomienia lub zapisu zakończy się niepowodzeniem.

## <a name="monitoring-write-operations"></a>Monitorowanie operacji zapisu
Dla wygody generowany jest plik wartownik o nazwie sukces po zakończeniu zapisu. Jego obecność pomaga zidentyfikować, po zakończeniu zapisu pośrednich bez konieczności oczekiwania dla całego potoku zakończyć.

## <a name="example-write-code"></a>Przykładowy kod zapisu

Na przykład uruchomić ładowanie danych do przepływu danych. Firma Microsoft będzie ponownie użyć tych danych w różnych formatach.

```python
import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

Przykładowe dane wyjściowe:
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

### <a name="delimited-file-example"></a>Przykład pliku rozdzielanego

Ta sekcja zawiera przykłady dotyczące używania `write_to_csv` funkcja pisania w języku rozdzielonym pliku.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Przykładowe dane wyjściowe:
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

Widać w powyższym danych wyjściowych, który kilka błędy są wyświetlane w kolumnach liczbowych ze względu na liczby, które nie zostały poprawnie przeanalizować. Podczas zapisywania do pliku CSV, te wartości null są zastępowane parametrami "ERROR" domyślnie. 

Można dodać parametry jako część Twojego zapisu wywołania i określenia ciąg wykorzystywany do reprezentowania wartości null. Na przykład:

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Powyższy kod generuje te dane wyjściowe:
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


### <a name="parquet-file-example"></a>Przykład pliku parquet

Podobnie jak `write_to_csv`, `write_to_parquet` :: gettotalsize() zwróciło nowego przepływu danych przy użyciu zapisu krok Parquet, który jest wykonywany, gdy przebiegi przepływu danych.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Następnie można uruchomić przepływu danych można uruchomić operacji zapisu.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

Powyższy kod generuje te dane wyjściowe:
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
