---
title: Porównaj & odtworzenia danych wraz z upływem czasu przy użyciu migawek
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak porównywanie danych wraz z upływem czasu i upewnij się, odtwarzaniem przy użyciu migawek zestawu danych
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: 51d0dcfc543834e9a8725d11fa82b566a5132a6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204975"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Porównywanie danych i upewnij się, odtwarzaniem przy użyciu migawek (wersja zapoznawcza)

W tym artykule dowiesz się, jak tworzyć i zarządzać nimi migawki usługi [zestawów danych Learning maszyny Azure](how-to-create-register-datasets.md) (zbiory danych), co pozwala na Przechwytywanie zawartości lub porównywania danych wraz z upływem czasu. Zestawy danych ułatwiają dostęp i pracować z danymi w chmurze w różnych scenariuszach.

**Zestaw danych migawki** przechowywać profil (statystyki podsumowujące) danych w czasie jego tworzenia. Można również przechowywać kopię danych w swojej migawki odtwarzaniem.

>[!Important]
> Migawki ponosić kosztów magazynowania. Przechowywanie kopii danych w migawek wymaga nawet więcej pamięci masowej. Użyj [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) kiedy są już potrzebne.

## <a name="when-to-use-snapshots"></a>Kiedy należy używać migawek

Istnieją trzy główne zastosowania migawki:

+ **Walidacja modelu**: Porównaj profil danych z różnych migawek między przebiegów szkoleniowych lub względem danych produkcyjnych.

+ **Model odtwarzaniem**: Odtwórz wyniki przez wywołanie metody migawki, która zawiera dane podczas szkolenia.

+ **Śledzenie danych wraz z upływem czasu**: Zobacz, jak zestaw danych został przekształcony za [porównanie profilów](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć zestaw danych migawki, należy zarejestrowanych Azure Machine Learning zestaw danych. Jeśli nie masz, zobacz [zestawów danych utwórz i Zarejestruj](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Tworzenie migawek zestawu danych

Aby utworzyć migawkę zestawu danych, użyj [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) z zestawu SDK usługi Azure Machine Learning.

Domyślnie migawki są przechowywane profilu (statystyki podsumowujące) danych przy użyciu najnowszej [definicji zestawu danych](how-to-manage-dataset-definitions.md) stosowane. Definicja zestawu danych zawiera rekord wszelkie kroki przekształcania zdefiniowane dla danych. Jest doskonałym sposobem tworzenia swojej przygotowywanie danych, pracy do odtworzenia.

Opcjonalnie można także kopii danych w migawek, dodając `create_data_snapshot = True`.  Może to być przydatne w przypadku powtarzalności.

W tym przykładzie użyto [przykładowe dane crime](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) i zestaw danych o nazwie `dataset_crime` utworzone za pomocą tego artykułu ["Utwórz i zarejestruj zestawy danych"](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Ponieważ asynchronicznie tworzone są migawki, należy użyć [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) metodę, aby monitorować proces.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Dane wyjściowe:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Użyj [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) kiedy są już potrzebne.

## <a name="find-snapshots"></a>Znajdź migawek

Aby uzyskać istniejącą migawkę, należy użyć [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Aby uzyskać listę Twoje zapisane migawki danego zestawu danych, użyj [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Pobieranie profilów i danych z migawki

Każdy migawki generuje profil zestaw danych, który zawiera podsumowanie statystyk i udostępnia opcję, aby zapisać kopię danych.

### <a name="get-the-data-profile"></a>Pobierz profil danych

Użyj [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) do uzyskania dostępu do profilu danych.  Aby porównać dane szkoleniowe i produkcji, na przykład, można użyć tego profilu.

```Python
snapshot.get_profile()
```

||Type|Min.|Maks.|Liczba|Liczba brakujących|Liczba niebrakujących|Odsetek brakujących|Liczba błędów|Liczba pustych|Kwantyl 0,1%|Kwantyl 1%|Kwantyl 5%|Kwantyl 25%|Kwantyl 50%|Kwantyl 75%|Kwantyl 95%|Kwantyl 99%|Kwantyl 99,9%|Średnia|Odchylenie standardowe|Wariancja|Skośność|Kurtoza
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Liczba przypadków|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Blokuj|FieldType.STRING|004XX APISZ KILBOURN|113XX APISZ PIESKÓW|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Typ podstawowy|FieldType.STRING|PRAKTYKI OSZUKAŃCZYM|PRZED KRADZIEŻĄ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Opis|FieldType.STRING|SFAŁSZOWANY WYBORU|ZA POŚREDNICTWEM 500 USD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Opis lokalizacji|FieldType.STRING||SZKOŁY, PUBLICZNEJ I TWORZENIE|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Aresztowania|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Krajowych|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Sygnał|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Okręg|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Lej|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Obszar społeczności|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Pobieranie danych z migawki

Aby uzyskać kopię dane zapisane w migawce zestawu danych, należy wygenerować pandas DataFrame z [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) metody.

Ta metoda kończy się niepowodzeniem, jeśli nie zażądano kopię danych podczas tworzenia migawki.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Liczba przypadków|Date|Blokuj|IUCR|Typ podstawowy|Opis|Opis lokalizacji|Aresztowania|Krajowych|Przyciski ...|Lej|Obszar społeczności|FBI kodu|Współrzędna x|Współrzędna Y|Rok|Aktualizacja:|Szerokość geograficzna|Długość geograficzna|Lokalizacja
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|PRAKTYKI OSZUKAŃCZYM|KRADZIEŻE TOŻSAMOŚCI FINANSOWYCH ZA POŚREDNICTWEM 300 USD|INNE|False|False|Przyciski ...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|PRZED KRADZIEŻĄ|OD TWORZENIA|W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA|False|False|Przyciski ...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|1154|PRAKTYKI OSZUKAŃCZYM|KRADZIEŻE TOŻSAMOŚCI FINANSOWYCH 300 USD I W OBSZARZE|W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA|False|False|Przyciski ...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Kolejne kroki

* Zobacz zestaw SDK [Przegląd](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) wzorców projektowych i przykłady użycia.

* Przykład przy użyciu zestawu danych migawek, zobacz [przykładowy notesów](https://aka.ms/dataset-tutorial).
