---
title: 'Zapis: zestaw SDK języka Python przygotowywania danych'
titleSuffix: Azure Machine Learning service
description: Więcej informacji na temat zapisywania danych przy użyciu zestawu SDK usługi Azure Machine Learning danych przedprodukcyjnym. Możesz zapisać dane w dowolnym momencie, w ramach przepływu danych i pliki we wszystkich naszych obsługiwanych lokalizacji (lokalnego systemu plików usługi Azure Blob Storage i usługi Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6753be5613b10b64936cddaafbb9859aad837b02
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358629"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Zapisywanie i Konfigurowanie danych za pomocą zestawu SDK przygotowywania danych Azure Machine Learning

W tym artykule przedstawiono różne metody zapisywania danych przy użyciu zestawu SDK języka [python Azure Machine Learning Data](https://aka.ms/data-prep-sdk) preprodukcyjny oraz sposób konfigurowania tych danych na potrzeby eksperymentowania z [zestawem SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Dane wyjściowe można napisać w dowolnym momencie w przepływu danych. Operacje zapisu są dodawane jako kroki do wyniku przepływu danych, a te kroki są uruchamiane przy każdym uruchomieniu przepływu danych. Dane są zapisywane do wielu plików partycji umożliwia zapisywanie równoległej.

> [!Important]
> Jeśli tworzysz nowe rozwiązanie, wypróbuj [Azure Machine Learning zestawy](how-to-explore-prepare-data.md) danych (wersja zapoznawcza), aby przekształcić dane, dane migawek i magazynować definicje zestawów danych. Zbiory danych to następna wersja zestawu SDK przygotowywania, oferująca rozszerzone funkcje zarządzania zestawami elementów w rozwiązaniach AI.
> Jeśli używasz `azureml-dataprep` pakietu do tworzenia przepływu danych z transformacjami zamiast `azureml-datasets` używania pakietu do tworzenia zestawu danych, nie będzie można użyć migawek ani zestawów danych z wersjami w późniejszym czasie.

Ponieważ nie ma ograniczeń ile napisać czynności są w potoku, możesz łatwo dodawać zapisu dodatkowe kroki, aby uzyskać wyniki pośrednie, rozwiązywania problemów lub innych potokach.

Każdorazowo po uruchomieniu kroku zapisu występuje pełną ściąganie danych przepływu danych. Na przykład przepływu danych przy użyciu trzech krokach zapisu odczytywać i przetwarzać trzy razy każdego rekordu w zestawie danych.

## <a name="supported-data-types-and-location"></a>Obsługiwane typy danych i lokalizacji

Obsługiwane są następujące formaty plików
-   Pliki rozdzielane (CSV, TSV itp.)
-   Pliki parquet

Za pomocą zestawu SDK języka Python Azure Machine Learning Data preprodukcyjny można zapisywać dane w:
+ lokalny system plików
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Zagadnienia dotyczące platformy Spark

Podczas uruchamiania przepływu danych platformie Spark, należy napisać do pustego folderu. Przy próbie uruchomienia zapis do istniejącego folderu powoduje błąd. Upewnij się, że folder docelowy jest pusta lub inną lokalizację docelową dla każdego uruchomienia lub zapisu zakończy się niepowodzeniem.

## <a name="monitoring-write-operations"></a>Monitorowanie operacji zapisu

Dla wygody generowany jest plik wartownik o nazwie sukces po zakończeniu zapisu. Jego obecność pomaga zidentyfikować, po zakończeniu zapisu pośrednich bez konieczności oczekiwania dla całego potoku zakończyć.

## <a name="example-write-code"></a>Przykładowy kod zapisu

Na potrzeby tego przykładu Zacznij od załadowania danych do przepływu danych `auto_read_file()`przy użyciu polecenia. Możesz ponownie użyć tych danych w różnych formatach.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Przykładowe dane wyjściowe:

| | Kolumna1 | Kolumna2 | Kolumna3 | Kolumna4 | Column5 | Kolumna6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | Brak | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | Brak | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | Brak | NO | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | Brak | NO | NO |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | Brak | NO | NO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Przykład pliku rozdzielanego

Poniższy kod używa funkcji, [`write_to_csv()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) aby zapisać dane do rozdzielanego pliku.

```python
# Create a new data flow using `write_to_csv`
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Przykładowe dane wyjściowe:

| | Kolumna1 | Kolumna2 | Kolumna3 | Kolumna4 | Column5 | Kolumna6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BŁĄD | NO | NO | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | BŁĄD | NO | NO | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BŁĄD | NO | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BŁĄD | NO | NO |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BŁĄD | NO | NO | ENSO |    59783.0 | 5350.0 |  500.0|

W powyższym danych wyjściowych kilka błędów są wyświetlane w kolumnach liczbowych ze względu na liczby, które nie zostały poprawnie przeanalizować. Podczas zapisywania do pliku CSV, wartości null są zastępowane parametrami "ERROR" domyślnie.

Dodaj parametry jako część Twojego zapisu wywołania i określenia ciąg wykorzystywany do reprezentowania wartości null.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'),
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Powyższy kod generuje te dane wyjściowe:

| | Kolumna1 | Kolumna2 | Kolumna3 | Kolumna4 | Column5 | Kolumna6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NO | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NO | NO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Przykład pliku parquet

Podobnie jak w przypadku [`write_to_parquet()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) ,funkcjazwracanowyprzepływdanychzkrokiemParquetzapisu,któryjestwykonywany,gdyprzepływdanychzostanieuruchomiony.`write_to_csv()`

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
                                     error='MiscreantData')
```

Uruchamianie przepływu danych można uruchomić operacji zapisu.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

Powyższy kod generuje te dane wyjściowe:

|   | Kolumna1 | Kolumna2 | Kolumna3 | Kolumna4 | Column5 | Kolumna6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NO| NO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Konfigurowanie danych do automatycznego uczenia usługi Machine Learning

Przekaż nowo zapisany plik danych do [`AutoMLConfig`](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) obiektu przygotowywanego do automatycznego uczenia maszynowego. 

Poniższy przykład kodu ilustruje sposób konwertowania przepływu danych na ramkę danych Pandas, a następnie podzielenie go na zestawienie szkoleniowe i testowe dla zautomatyzowanych szkoleń dotyczących uczenia maszynowego.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Jeśli nie są wymagane żadne pośrednie kroki przygotowywania danych, takie jak w poprzednim przykładzie, można przekazać przepływu danych bezpośrednio do `AutoMLConfig`programu.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Następne kroki
* Zobacz [Omówienie](https://aka.ms/data-prep-sdk) zestawu SDK dotyczące wzorców projektowych i przykładów użycia 
* Przykład [samouczka](tutorial-auto-train-models.md) automatycznej uczenia maszynowego dotyczącego modelu regresji
