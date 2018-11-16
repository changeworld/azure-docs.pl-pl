---
title: Przygotować dane za pomocą zestawu SDK usługi Machine Learning Data Prep dla języka Python — Azure
description: Dowiedz się, jak używać usługi Azure Machine Learning Prep zestawu SDK usługi Data dla języka Python do ładowania danych z różnych formatów, przekształcania, aby była bardziej użyteczne i zapisu danych do lokalizacji w celu swoje modele, aby uzyskać dostęp.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: f6f669bd9ab45ba3800722eb3bcdba88f2e72f5e
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710243"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Przygotowuje dane do modelowania za pomocą usługi Azure Machine Learning
 
W tym artykule poznasz przypadków użycia i funkcji usługi Azure Machine Learning Prep zestawu SDK usługi Data. Przygotowywanie danych to najważniejszy element usługi machine learning przepływu pracy. Rzeczywiste dane często są dzielone, niezgodne lub ma być używany jako dane szkoleniowe bez znaczących czyszczenia i transformacji. Poprawianie błędów i anomalii w danych pierwotnych i tworzenia nowych funkcji, które mają zastosowanie do problemu, który próbujesz rozwiązać, zwiększy dokładność modelu.

Można przygotować swoje dane w języku Python za pomocą [zestawu SDK usługi Azure Machine Learning danych Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py).

## <a name="azure-machine-learning-data-prep-sdk"></a>Usługi Azure Machine Learning przygotowywanie danych zestawu SDK

Azure Machine Learning Prep zestawu SDK usługi Data to biblioteka języka Python, który zawiera wiele popularnych narzędzi wstępnego przetwarzania danych. Ponadto dodano, zaawansowane funkcje, takie jak funkcja automatyczna, inżynieria i przekształcenia pochodzące z przykładów. Zestaw SDK jest podobna do popularnych bibliotek, takich jak Pandas i PySpark podstawowych funkcji, ale zapewnia większą elastyczność. Pandas jest zazwyczaj najbardziej użyteczne na mniejszych zestawów danych (< 2 do 5 GB), zanim ograniczeń wydajności pamięci mają wpływ na wydajność. Z kolei PySpark jest zazwyczaj dla aplikacji przetwarzających dane big Data, ale niesie ze sobą obciążenie, które ułatwia pracę z małych zestawów danych znacznie wolniejsze.

Zestaw SDK udostępnia:

- Praktycznych i wygodę, pracując z małymi zestawami danych
- Skalowalność nowoczesne aplikacje danych big data
- Możliwość użycia i skalować ten sam kod dla obu przypadków użycia

Poniższe przykłady omówiono niektóre z unikatowych funkcji zestawu SDK.

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

Zainstaluj zestaw SDK w środowisku Python przy użyciu następującego polecenia.

```shell
pip install azureml-dataprep
```

Użyj poniższego kodu, aby zaimportować pakiet.

```python
import azureml.dataprep as dprep
```

### <a name="automatic-file-type-detection"></a>Plik automatyczne wykrywanie typu

Użyj `smart_read_file()` funkcję, aby załadować dane bez konieczności określania typu pliku. Ta funkcja automatycznie rozpoznaje i analizuje typu pliku.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

### <a name="automated-feature-engineering"></a>Inżynieria funkcji automatycznego

Dzielenie i pobieraj kolumny według przykładu i wnioskowania w celu zautomatyzowania technicznego opracowywania funkcji przy użyciu zestawu SDK. Założono, że pole w obiekcie przepływu danych o nazwie `datetime` o wartości `2018-09-15 14:30:00`.

Aby automatycznie podzielić `datetime` pola, wywołaj funkcję następujące.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

Definiując nie parametrze przykładu, funkcja automatycznie zostanie podzielona `datetime` pole do dwa nowe pola `datetime_1` i `datetime_2`. Wyniki są `2018-09-15` i `14:30:00`, odpowiednio. Istnieje również możliwość określenia przykładowy wzorzec, a zestaw SDK będzie przewidywania i wykonać swoją transformację zamierzone. Korzystając z tych samych `datetime` obiektu, poniższy kod utworzy nową kolumnę `datetime_weekday` dla dnia tygodnia, na podstawie podanego przykładu.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

### <a name="summary-statistics"></a>Statystyki podsumowujące

Można wygenerować szybkiego statystyk podsumowujących dla przepływu za pomocą jednego wiersza kodu. Ta metoda oferuje wygodny sposób, aby zrozumieć dane i sposób dystrybucji.

```python
dataflow.get_profile()
```

Wywołanie tej funkcji dla obiektu przepływu danych spowoduje dane wyjściowe podobne do poniższej tabeli.

![Statystyki podsumowujące dane wyjściowe](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>Wiele możliwości środowiska

Zestaw SDK umożliwia również obiekty przepływu danych może być serializowany i otwarty w *wszelkie* środowiska Python. Środowisko, w której jest otwarty może być inny niż środowisko, w którym został zapisany. Ta funkcja umożliwia łatwe przesyłanie między środowiska Python i szybkie integrowanie przy użyciu modeli usługi Azure Machine Learning.

Użyj poniższego kodu, aby zapisać przepływ danych obiektów.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

Użyj poniższego kodu, aby ponownie otworzyć pakiet w dowolnym środowisku i pobrać listę obiektów przepływu danych.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>Potok przygotowywania danych

Aby zobaczyć szczegółowe przykłady i kodu dla każdego kroku przygotowania, użyj następujących przewodnikach instruktażowych:

1. [Ładowanie danych](how-to-load-data.md), które mogą znajdować się w różnych formatach
2. [Przekształcanie](how-to-transform-data.md) go do bardziej użyteczne struktury
3. [Zapis](how-to-write-data.md) tych danych do lokalizacji dostępnej dla modeli

![Proces przygotowywania danych](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Kolejne kroki
Przegląd [przykładzie Notes](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) przygotowania danych przy użyciu usługi Azure Machine Learning Prep zestawu SDK usługi Data.

Azure Machine Learning Prep zestawu SDK usługi Data [dokumentację referencyjną](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).
