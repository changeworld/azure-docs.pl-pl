---
title: Dostęp do zestawów danych z biblioteki klienta usługi Machine Learning Python | Dokumentacja firmy Microsoft
description: Zainstaluj i Python biblioteki klienta umożliwiają dostęp do danych i zarządzać nimi usługi Azure Machine Learning bezpiecznie z lokalnego środowiska Python.
services: machine-learning
documentationcenter: python
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 9f84686f8689a40cf002035053236b415481488f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838354"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Dostęp do zestawów danych z językiem Python za pomocą biblioteki klienta Python usługi Azure Machine Learning
Biblioteka klienta usługi Microsoft Azure Machine Learning Python w wersji zapoznawczej można włączyć bezpieczny dostęp do usługi Azure Machine Learning zestawów danych z lokalnego środowiska Python i umożliwia tworzenie i Zarządzanie zestawami danych w obszarze roboczym.

Ten temat zawiera instrukcje dotyczące sposobu:

* Zainstaluj biblioteki klienckie Machine Learning Python 
* dostęp i przekazać zestawów danych, w tym instrukcje dotyczące sposobu uzyskania autoryzacji dostępu do usługi Azure Machine Learning zestawów danych z lokalnego środowiska Python
* dostęp do pośredniego zestawów danych z eksperymentów
* Użyj biblioteki klienckiej Python wyliczyć zestawów danych, dostępu do metadanych, wczytanie zawartości zestawu danych, Utwórz nowy zestaw danych i zaktualizować istniejący zestaw danych

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Biblioteka klienta Python był testowany w następujących środowiskach:

* Windows, Mac i Linux
* Python 2.7 3.3 i 3.4

Ma ona zależność od następujących pakietów:

* żądania/żądań
* dateutil języka Python
* pandas

Firma Microsoft zaleca używanie dystrybucję oprogramowania Python, takie jak [Anaconda](http://continuum.io/downloads#all) lub [korony](https://store.enthought.com/downloads/), które pochodzą z języka Python, IPython i zainstalować trzy pakiety wymienione powyżej. Chociaż IPython nie jest ścisłym wymogiem, jest doskonałe środowisko manipulowanie i wizualizacja danych interaktywnie.

### <a name="installation"></a>Jak zainstalować biblioteki klienta usługi Azure Machine Learning Python
Biblioteka klienta usługi Azure Machine Learning Python musi być zainstalowany także wykonać zadania opisane w tym temacie. Jest ona dostępna z [indeksu pakietów języka Python](https://pypi.python.org/pypi/azureml). Aby zainstalować go w środowisku Python, uruchom następujące polecenie z lokalnego środowiska Python:

    pip install azureml

Alternatywnie można pobrać i zainstalować ze źródeł na [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Jeśli masz zainstalowany na tym komputerze git umożliwia pip zainstalować bezpośrednio z repozytorium git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Wstawki kodu Studio umożliwia dostęp do zestawów danych
Biblioteka klienta Python umożliwia programowy dostęp do istniejących zestawów danych z eksperymentów, które zostały uruchomione.

W interfejsie sieci web Studio mogą generować wstawki kodu, które obejmują wszystkie informacje niezbędne do pobrania i deserializacji zestawy danych jako obiekty Pandas DataFrame na komputerze lokalizacji.

### <a name="security"></a>Zabezpieczenia dostępu do danych
Wstawki kodu podał Studio do użycia z biblioteki klienta Python zawiera identyfikator i autoryzacji tokenu. Te umożliwianie pełnego dostępu do swojego obszaru roboczego i musi być chroniony, takie jak hasła.

Ze względów bezpieczeństwa funkcje wstawek kodu jest dostępna tylko dla użytkowników, którzy mają ich roli Ustaw jako **właściciela** dla obszaru roboczego. Rola użytkownika jest wyświetlany w usłudze Azure Machine Learning Studio na **użytkowników** w obszarze **ustawienia**.

![Bezpieczeństwo][security]

Jeśli rola użytkownika nie jest ustawiony jako **właściciela**, możesz albo żądanie można reinvited jako właściciela lub poproś właściciela obszaru roboczego dostarczają fragmentu kodu.

Aby uzyskać token autoryzacji, wykonaj jedną z następujących czynności:

* Prosić o token właściciela. Właściciele mogą uzyskiwać dostęp do swoich tokenów autoryzacji ze strony ustawień ich obszaru roboczego w Studio. Wybierz **ustawienia** w okienku po lewej stronie, kliknij **TOKENY autoryzacji** wyświetlić tokeny podstawowego i pomocniczego.  Chociaż podstawowej lub dodatkowej autoryzacji tokenów można używać we fragmencie kodu, zaleca się, że właścicieli udostępniać tylko tokeny dodatkowej autoryzacji.

![Tokeny autoryzacji](./media/python-data-access/ml-python-access-settings-tokens.png)

* Poproś funkcjonować jako rolę właściciela.  Aby to zrobić, bieżącego właściciela obszaru roboczego musi najpierw należy usunąć z obszaru roboczego, a następnie ponownie zapraszać do niego jako właściciela.

Po uzyskaniu deweloperzy autoryzacji i identyfikator obszaru roboczego tokenu, są w stanie uzyskać dostępu do obszaru roboczego przy użyciu fragment kodu, niezależnie od ich roli.

Tokeny autoryzacji są zarządzane na **TOKENY autoryzacji** w obszarze **ustawienia**. Ponowne ich wygenerowanie, ale w tej procedurze odwołuje dostęp do poprzednim tokenem.

### <a name="accessingDatasets"></a>Zestawy dostępu do danych z aplikacji lokalnej Python
1. W usłudze Machine Learning Studio, kliknij przycisk **zestawów danych** na pasku nawigacyjnym po lewej stronie.
2. Wybierz zestaw danych, które chcesz uzyskać dostęp. Można wybrać dowolny z zestawów danych z **Moje zestawów danych** listy lub **przykłady** listy.
3. W dolnym pasku narzędzi, kliknij polecenie **Generuj kod dostępu do danych**. Jeśli dane są w formacie niezgodne z biblioteki klienta języka Python, ten przycisk jest wyłączone.
   
    ![Zestawy danych][datasets]
4. Wybierz fragment kodu z okna wyświetlana i skopiuj go do Schowka.
   
    ![Kod dostępu][dataset-access-code]
5. Wklej kod do notesu lokalnych aplikacji Python.
   
    ![Notesu][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Dostęp do pośredniego zestawów danych z eksperymentów uczenia maszynowego
Po uruchomieniu eksperymentu w usłudze Machine Learning Studio istnieje możliwość dostępu do pośredniego zestawów danych z węzłów wyjściowych modułów. Pośredni zestawów danych są dane, które zostały utworzone i jest używany do pośredniego kroki po uruchomieniu narzędzia modelu.

Zestaw pośrednich danych jest możliwy tak długo, jak format danych jest zgodny z biblioteki klienta języka Python.

Obsługiwane są następujące formaty (stałe tych znajdują się w `azureml.DataTypeIds` klasy):

* W postaci zwykłego tekstu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Format można określić, ustawiając kursor nad węzłem danych wyjściowych modułu. Jest on wyświetlany wraz z nazwa węzła w etykietce narzędzia.

Niektóre moduły, takich jak [podziału] [ split] modułu danych wyjściowych do formatu o nazwie `Dataset`, który nie jest obsługiwany przez bibliotekę klienta języka Python.

![Format zestawu danych][dataset-format]

Należy użyć modułu konwersji, takich jak [Konwertuj do formatu CSV][convert-to-csv], aby uzyskać dane wyjściowe do obsługiwanego formatu.

![GenericCSV Format][csv-format]

W poniższej procedurze pokazano przykład, w którym tworzy eksperyment, uruchamia go i uzyskuje dostęp do pośredniego zestawu danych.

1. Tworzenie nowego eksperymentu.
2. Wstaw **zestawu danych dla dorosłych klasyfikacji binarnej dochodu spisu** modułu.
3. Wstaw [podziału] [ split] modułu i połączenia jej danych wejściowych z danymi wyjściowymi modułu zestawu danych.
4. Wstaw [Konwertuj do formatu CSV] [ convert-to-csv] modułu i połączenia jej danych wejściowych do jednego z [podziału] [ split] danych wyjściowych modułu.
5. Zapisz eksperyment, uruchom go i zaczekaj na zakończenie działania.
6. Kliknij węzeł danych wyjściowych na [Konwertuj do formatu CSV] [ convert-to-csv] modułu.
7. Po wyświetleniu menu kontekstowego wybierz **Generuj kod dostępu do danych**.
   
    ![Menu kontekstowe][experiment]
8. Wybierz fragment kodu i skopiuj go do Schowka z wyświetlonym oknie.
   
    ![Kod dostępu][intermediate-dataset-access-code]
9. Wklej kod w Notatniku.
   
    ![Notesu][ipython-intermediate-dataset]
10. Możesz wizualizować dane przy użyciu matplotlib. Spowoduje to wyświetlenie w histogram kolumny wieku:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>Użyj biblioteki klienckiej Machine Learning Python dostępu, odczytu, tworzenie i zarządzanie nimi zbiory danych
### <a name="workspace"></a>Obszar roboczy
Obszar roboczy jest punkt wejścia dla biblioteki klienta języka Python. Podaj `Workspace` token klasy identyfikator obszaru roboczego i autoryzacji do utworzenia wystąpienia:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Wyliczanie obiektów DataSet
Aby wyliczyć wszystkie zestawy danych danego obszaru roboczego:

    for ds in ws.datasets:
        print(ds.name)

Aby wyliczyć właśnie utworzone przez użytkownika zestawy danych:

    for ds in ws.user_datasets:
        print(ds.name)

Aby wyliczyć tylko zestawy danych przykład:

    for ds in ws.example_datasets:
        print(ds.name)

Aby dostęp do zestawu danych, według nazwy (która jest uwzględniana wielkość liter):

    ds = ws.datasets['my dataset name']

Lub zostanie do niego dostęp przez indeks:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadane
Zestaw danych ma metadanych, oprócz zawartości. (Pośredniego zestawów danych są wyjątkiem od tej zasady i nie ma żadnych metadanych).

Niektóre wartości metadanych są przypisane przez użytkownika w czasie tworzenia:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Inne są wartości przypisane przez uczenie Maszynowe Azure:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Zobacz `SourceDataset` klasy, aby uzyskać więcej informacji na temat dostępnych metadanych.

### <a name="read-contents"></a>Odczytaj zawartość
Wstawki kodu udostępniane przez usługi Machine Learning Studio automatycznie Pobierz i deserializacji do obiektu Pandas DataFrame zestawu danych. Jest to zrobić za pomocą `to_dataframe` metody:

    frame = ds.to_dataframe()

Jeśli wolisz pobierania danych pierwotnych i samodzielnie przeprowadzić deserializacji, który jest opcją. W tej chwili jest jedyną opcją dla formatach, takich jak "ARFF", którego nie można deserializować biblioteki klienckiej Python.

Można odczytać zawartości jako tekst:

    text_data = ds.read_as_text()

Można odczytać zawartości jako dane binarne:

    binary_data = ds.read_as_binary()

Możesz także po prostu otworzyć strumienia zawartości:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Utwórz nowy zestaw danych
Biblioteka klienta Python umożliwia przekazywanie zestawów danych z programu Python. Te zestawy danych będą dostępne do użycia w obszarze roboczym.

Jeśli dane znajdują się w Pandas DataFrame, należy użyć poniższego kodu:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Jeśli dane są już serializowane, możesz użyć:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Może serializować DataFrame Pandas, do następujących formatów jest biblioteka klienta języka Python (stałe tych znajdują się w `azureml.DataTypeIds` klasy):

* W postaci zwykłego tekstu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualizuj istniejący zestaw danych
Jeśli użytkownik spróbuje przekazać nowy zestaw danych o nazwie, która odpowiada istniejący zestaw danych, należy pobrać błąd konfliktu.

Aby zaktualizować istniejący zestaw danych, należy najpierw pobrać odwołanie do istniejącego zestawu danych:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Następnie użyj `update_from_dataframe` do serializacji i Zastąp zawartość zestawu danych na platformie Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Jeśli chcesz uszeregować danych w innym formacie, określ wartość opcjonalna `data_type_id` parametru.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcjonalnie możesz ustawić nowy opis, określając wartość `description` parametru.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcjonalnie możesz ustawić nową nazwę, określając wartość `name` parametru. Od teraz będziesz pobrać przy użyciu nowej nazwy zestawu danych. Poniższy kod aktualizuje danych, nazwę i opis.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

`data_type_id`, `name` i `description` parametry są opcjonalne i domyślnie ich poprzedniej wartości. `dataframe` Parametr jest zawsze wymagane.

Jeśli dane są już serializowane, użyj `update_from_raw_data` zamiast `update_from_dataframe`. Jeśli po prostu Przekaż w `raw_data` zamiast `dataframe`, działa w podobny sposób.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

