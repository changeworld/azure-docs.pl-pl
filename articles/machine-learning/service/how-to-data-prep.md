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
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446436"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Przygotowuje dane do modelowania za pomocą usługi Azure Machine Learning

W tym artykule poznasz przypadków użycia i funkcji usługi Azure Machine Learning Prep zestawu SDK usługi Data. Przygotowywanie danych to najważniejszy element usługi machine learning przepływu pracy. Rzeczywiste dane często są dzielone, niezgodne lub ma być używany jako dane szkoleniowe bez znaczących czyszczenia i transformacji. Poprawianie błędów i anomalii w danych pierwotnych i tworzenia nowych funkcji, które mają zastosowanie do problemu, który próbujesz rozwiązać, zwiększy dokładność modelu. Zestaw SDK jest przeznaczony do mu dobrze znanym użytkownikom innych typowych danych przygotowywania biblioteki, podczas gdy oferty dodatkowych korzyści w przypadku kluczowych scenariuszy i utrzymywanie współdziałanie z tych innych bibliotek.

Można przygotować swoje dane w języku Python za pomocą [zestawu SDK usługi Azure Machine Learning danych Prep](https://aka.ms/data-prep-sdk).

## <a name="azure-machine-learning-data-prep-sdk"></a>Usługi Azure Machine Learning przygotowywanie danych zestawu SDK

[Zestawu SDK usługi Azure Machine Learning danych Prep](https://aka.ms/data-prep-sdk) to biblioteka języka Python, która oferuje:

* Transformacje inteligentne zaoszczędzić trochę czasu, takie jak rozmyte grupowania, pochodnej kolumny według przykładu, automatycznie podziału, inteligentne Odczyt pliku i niewyrównane prawo przetwarzania schematu.
* Pojedynczy interfejs API, który działa na małych danych lokalnie lub dużych ilości danych w chmurze, z **mało, aby bez zmian w kodzie**.
* Możliwość skalowania w bardziej efektywnie na jednym komputerze przy użyciu przesyłania strumieniowego podejście do przetwarzania danych, zamiast ładowania do pamięci.

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

Zainstaluj zestaw SDK w środowisku Python przy użyciu następującego polecenia.

```shell
pip install azureml-dataprep
```

Użyj poniższego kodu, aby zaimportować pakiet.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>Dokumentacja i przykłady

Aby dowiedzieć się, modułów i funkcji zestaw SDK, zobacz [dokumentacja zestawu SDK usługi Data Prep](https://aka.ms/data-prep-sdk).

Poniższe przykłady omówiono niektóre z unikatowych funkcji zestawu SDK, w tym:

* Plik automatyczne wykrywanie typu
* Inteligentne przekształceń
* Statystyki podsumowujące
* Funkcje dla wielu środowisk


#### <a name="automatic-file-type-detection"></a>Plik automatyczne wykrywanie typu

Użyj `smart_read_file()` funkcję, aby załadować dane bez konieczności określania typu pliku. Ta funkcja automatycznie rozpoznaje i analizuje typu pliku.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>Inteligentne przekształceń

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

#### <a name="summary-statistics"></a>Statystyki podsumowujące

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
