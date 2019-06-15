---
title: Wersja definicji zestawu danych z zestawami danych usługi Azure ml i zarządzania nimi
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak aktualizacja definicji zestawu danych i zarządzanie cyklem życia definicji
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 4476bdd902f054683fa544dc44b548689f3a1881
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241881"
---
# <a name="version-and-manage-your-dataset-definitions"></a>Wersja z definicji zestawu danych i zarządzania nimi

Dowiedz się, jak zaktualizować definicje oraz zarządzać nimi zestawu danych z usługi Azure Machine Learning z zestawami danych (wersja zapoznawcza).

Definicje zestawów danych są przepisy, używane do przygotowania swoich zestawów danych, odwołanie do danych źródłowych i wykonać kroki przekształcania. Jeden zestaw danych może mieć wielu definicji, a każda definicja ma swój własny cykl życia.

Poniższy scenariusz ma przykład korzystając z definicji zestawu danych:

1. Możesz tworzyć usługi machine learning potoku, który używa bieżącej definicji zestawu danych.
1. Zmiany w danych bazowych; Dodano nowe atrybuty.
1. Możesz utworzyć nową wersję swojej definicji, który dodaje dodatkowe przekształceń w celu obsługi nowych atrybutów.

W tym przykładzie istniejący potok w dalszym ciągu korzystać z wersją oryginalną definicją. Nowa definicja może służyć do nowe scenariusze, takie jak uczenia modelu lub do utworzenia potoku.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć subskrypcję platformy Azure i obszar roboczy, aby zarejestrować swój zestaw danych, aby móc zarządzać cyklem życia definicji zestawu danych.

Przykładowy plik używany w przykładach w tym dokumencie znajduje się w temacie [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="create-and-update-definitions"></a>Tworzenie i aktualizowanie definicji

Firma Microsoft będzie najpierw utwórz i zarejestruj zestaw danych z obszarem roboczym.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

Pierwszy definicji zestawu danych (`version_id` = 1) jest tworzony po utworzeniu zestawu danych. Następnie za każdym razem, gdy możesz zaktualizować swojej definicji zestawu danych, nowe version_id zostanie przypisany do najnowszych definicji.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Aresztowania</th>
      <th>Szerokość geograficzna</th>
      <th>Długość geograficzna</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>WARTOŚĆ FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>WARTOŚĆ FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>WARTOŚĆ FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>WARTOŚĆ FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>WARTOŚĆ FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Ta aktualizacja definicji nie spowoduje to wymazanie poprzedniego definicji zestawu danych. Można nadal uzyskać dostęp i korzystać z poprzednim definicje.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Liczba przypadków</th>
      <th>Date</th>
      <th>Blokuj</th>
      <th>IUCR</th>
      <th>Typ podstawowy</th>
      <th>Opis</th>
      <th>Opis lokalizacji</th>
      <th>Aresztowania</th>
      <th>Krajowych</th>
      <th>Przyciski ...</th>
      <th>Lej</th>
      <th>Obszar społeczności</th>
      <th>FBI kodu</th>
      <th>Współrzędna x</th>
      <th>Współrzędna Y</th>
      <th>Rok</th>
      <th>Aktualizacja:</th>
      <th>Szerokość geograficzna</th>
      <th>Długość geograficzna</th>
      <th>Lokalizacja</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>PRAKTYKI OSZUKAŃCZYM</td>
      <td>KRADZIEŻE TOŻSAMOŚCI FINANSOWYCH ZA POŚREDNICTWEM 300 USD</td>
      <td>INNE</td>
      <td>WARTOŚĆ FALSE</td>
      <td>WARTOŚĆ FALSE</td>
      <td>Przyciski ...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>PRZED KRADZIEŻĄ</td>
      <td>OD TWORZENIA</td>
      <td>W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA</td>
      <td>WARTOŚĆ FALSE</td>
      <td>WARTOŚĆ FALSE</td>
      <td>Przyciski ...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTO AVE</td>
      <td>1154</td>
      <td>PRAKTYKI OSZUKAŃCZYM</td>
      <td>KRADZIEŻE TOŻSAMOŚCI FINANSOWYCH 300 USD I W OBSZARZE</td>
      <td>W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA</td>
      <td>WARTOŚĆ FALSE</td>
      <td>WARTOŚĆ FALSE</td>
      <td>Przyciski ...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX APISZ PIESKÓW</td>
      <td>1120</td>
      <td>PRAKTYKI OSZUKAŃCZYM</td>
      <td>FAŁSZOWANIE</td>
      <td>W MIEJSCU ZAMIESZKANIA UŻYTKOWNIKA</td>
      <td>WARTOŚĆ FALSE</td>
      <td>WARTOŚĆ FALSE</td>
      <td>Przyciski ...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>PRZED KRADZIEŻĄ</td>
      <td>OD TWORZENIA</td>
      <td>SZKOŁY, PUBLICZNEJ I TWORZENIE</td>
      <td>WARTOŚĆ FALSE</td>
      <td>WARTOŚĆ FALSE</td>
      <td>Przyciski ...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Jak widać w wyników pierwszą wersję definicji zestawu danych nadal przechowuje wszystkie kolumny z danych przestępczości, niezależnie od tego, nowsza wersja. Na przykład, jeśli masz usługi machine learning obecnie korzystanie z pierwszą wersję zestawu danych i modelu przy użyciu `Date` kolumny z zestawu danych jako jedna z funkcji, nie będzie występować dowolnego błędu o najnowszych aktualizacji definicji przechowuje tylko `ID`, `Arrest`, `Latitude`, `Longitude` kolumn z danych przestępstw.

## <a name="how-to-access-dataset-definitions"></a>Jak uzyskać dostęp do definicji zestawu danych

Aby uzyskać listę wszystkich definicji, użyj `get_definitions()`. Aby uzyskać określoną wersję definicji, użyj `get_definition()`. Poniższy przykład przedstawia pobieranie listy wszystkich definicji, a następnie pobierania wersji 1:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

Dane wyjściowe `get_definitions()` jest podobny do poniższego przykładu:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

Po pobraniu definicji, można użyć z modeli usługi machine learning lub w potoku uczenia maszynowego.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Zarządzanie cyklem życia definicji zestawu danych

Cykl życia Każda definicja zestawu danych można zarządzać niezależnie. Są trzy etapy cyklu życia: aktywny, przestarzałe lub zarchiwizowane.

### <a name="active"></a>Aktywne

Podczas tworzenia nowej definicji zestawu danych jest aktywne, domyślnie. 

### <a name="deprecate"></a>Wycofana

Definicje zestawów danych może staną się przestarzałe użycie nie jest zalecane, gdy zastępczy jest dostępny. Gdy przestarzałe definicji zestawu danych jest używana w potoków uczenia maszynowego, pobiera zwrócił komunikat ostrzegawczy z informacją, ale wykonanie nie będą blokowane.

Gdy wycofano definicji zestawu danych, określ identyfikator zestawu danych i identyfikator wersji definicji zestawu danych, definicja zastępczego. Te informacje są przechowywane i używane w komunikacie ostrzeżenia podczas próby korzystania przestarzałe definicji zestawu danych.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Archiwum

Można je archiwizować definicji zestawu danych, gdy definicje nie powinien być używane dla jakiegokolwiek powodu (na przykład danych bazowych nie są już dostępne). Gdy zarchiwizowane definicji zestawu danych jest używana w potoków uczenia maszynowego, wykonywania zostanie zablokowana z powodu błędu.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Uaktywnij ponownie

Łatwo można ponownie aktywować przestarzałe lub zarchiwizowane definicji zestawu danych.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat pracy z zestawami danych, zobacz [tworzenie i rejestrowanie zestawów danych Learning maszyny Azure](how-to-create-register-datasets.md).

Przykład przy użyciu zestawów danych, zobacz [przykładowy notesów](https://aka.ms/dataset-tutorial).
