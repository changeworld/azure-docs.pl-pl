---
title: Dostęp do zestawów danych za pomocą biblioteki klienta Python - zespołu danych dla celów naukowych
description: Zainstaluj i dostęp do danych i zarządzanie nimi usługi Azure Machine Learning bezpieczne od lokalnego środowiska Python za pomocą biblioteki klienta języka Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 241f804b0519fd744e8b980b2d311a72680aafad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427385"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Dostęp do zestawów danych z językiem Python za pomocą biblioteki klienta Python usługi Azure Machine Learning
Biblioteki klienta języka Python usługi Microsoft Azure Machine Learning w wersji zapoznawczej można włączyć bezpieczny dostęp do usługi Azure Machine Learning zestawów danych z lokalnego środowiska Python i umożliwia tworzenie i Zarządzanie zestawami danych w obszarze roboczym.

Ten temat zawiera instrukcje dotyczące sposobu:

* Zainstaluj biblioteki klienta języka Python Machine Learning
* dostęp i przekaż zestawów danych, w tym instrukcje dotyczące sposobu uzyskania autoryzacji dostępu do zestawów danych usługi Azure Machine Learning w lokalnym środowisku Python
* dostęp do zestawów danych pośrednich z eksperymentów
* Wyliczanie zestawów danych, dostęp do metadanych, czytać zawartość zestawu danych, tworzyć nowe zestawy danych i zaktualizować istniejące zestawy danych za pomocą biblioteki klienta języka Python

## <a name="prerequisites"></a>Wymagania wstępne
Biblioteki klienta Python został przetestowany w następujących środowiskach:

* Windows, Mac i Linux
* Python 2.7 3.3 i 3.4

Ma zależności na następujące pakiety:

* Żądania
* dateutil języka Python
* pandas

Firma Microsoft zaleca używanie dystrybucji języka Python, takich jak [Anaconda](http://continuum.io/downloads#all) lub [korony](https://store.enthought.com/downloads/), które pochodzą z języka Python, IPython i zainstalować trzy pakiety wymienione powyżej. Chociaż IPython nie jest bezwzględnie konieczne, to doskonałe środowisko do manipulowania i wizualizowanie danych w interaktywne.

### <a name="installation"></a>Jak zainstalować biblioteki klienta języka Python usługi Azure Machine Learning
Należy także zainstalować biblioteki klienta języka Python usługi Azure Machine Learning w celu wykonania zadań opisanych w tym temacie. Jest ona dostępna z [indeksu pakietów języka Python](https://pypi.python.org/pypi/azureml). Aby go zainstalować w środowisku Python, uruchom następujące polecenie ze środowiska lokalnego środowiska Python:

    pip install azureml

Alternatywnie możesz pobrać i zainstalować ze źródeł w serwisie [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Jeśli masz zainstalowane na komputerze narzędzie git, można użyć narzędzia pip instalować bezpośrednio z repozytorium git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Używanie fragmentów kodu do uzyskiwania dostępu do zestawów danych
Biblioteki klienta Python zapewnia dostęp programistyczny do istniejących zestawów danych z eksperymentów, które zostały uruchomione.

Z poziomu interfejsu sieci Web Azure Machine Learning Studio (klasycznego) można generować fragmenty kodu zawierające wszystkie informacje niezbędne do pobrania i deserializacji zestawów danych jako Pandas obiektów Dataframe na komputerze lokalnym.

### <a name="security"></a>Zabezpieczenia dostępu do danych
Fragmenty kodu udostępniane przez Azure Machine Learning Studio (klasyczne) do użycia z biblioteką klienta języka Python obejmują identyfikator obszaru roboczego i token autoryzacji. Te zapewniają pełny dostęp do obszaru roboczego i muszą być chronione, takie jak hasła.

Ze względów bezpieczeństwa funkcji fragment kodu jest dostępna tylko dla użytkowników, którzy mają ich roli, Ustaw jako **właściciela** dla obszaru roboczego. Twoja rola jest wyświetlana w Azure Machine Learning Studio (klasyczny) na stronie **Użytkownicy** w obszarze **Ustawienia**.

![Zabezpieczenia][security]

Jeśli Twoja rola nie jest ustawiony jako **właściciela**, możesz zażądać można go ponownie zaprosić jako właściciela lub poproś właściciela obszaru roboczego do przedstawienia fragmentu kodu.

Aby uzyskać token autoryzacji, wykonaj jedną z następujących:

* Poproś o token od właściciela. Właściciele mogą uzyskać dostęp do tokenów autoryzacji ze strony ustawień obszaru roboczego w Azure Machine Learning Studio (klasyczny). Wybierz **ustawienia** w okienku po lewej stronie, a następnie kliknij **TOKENACH autoryzacji** się tokeny podstawowego i pomocniczego. Mimo że podstawowej lub tokenów pomocniczych autoryzacji można używać we fragmencie kodu, zaleca się, że właścicieli udostępniać tylko tokenów pomocniczych autoryzacji.

   ![Tokeny autoryzacji](./media/python-data-access/ml-python-access-settings-tokens.png)

* Poproś o podniesiony do roli właściciel. Aby to zrobić, bieżący właściciel obszaru roboczego musi najpierw należy usunąć z obszaru roboczego, a następnie ponownie zaprosić do niego jako właściciela.

Gdy deweloperzy uzyskali identyfikator obszaru roboczego i token autoryzacji, będą mogli uzyskać dostęp do obszaru roboczego przy użyciu fragmentu kodu, niezależnie od ich roli.

Tokeny autoryzacji są zarządzane na **TOKENACH autoryzacji** strony w obszarze **ustawienia**. Można ponownie je wygenerować, ale ta procedura odwołuje dostęp do poprzedni token.

### <a name="accessingDatasets"></a>Dostęp do zestawów danych z lokalnych aplikacji w języku Python
1. W Machine Learning Studio (klasyczny) kliknij pozycję **zestawy danych** na pasku nawigacyjnym po lewej stronie.
2. Wybierz zestaw danych, którego chcesz uzyskać dostęp. Można wybrać dowolny zestawy danych z **Moje zestawy danych** listy lub **przykłady** listy.
3. Dolny pasek narzędzi, kliknij **Generuj kod dostępu do danych**. Jeśli dane są w formacie niezgodne z biblioteki klienta Python, ten przycisk jest wyłączony.
   
    ![Zestawy danych][datasets]
4. Wybierz fragment kodu z okna, które zostanie wyświetlone, a następnie skopiuj go do Schowka.
   
    ![Generowanie przycisku kodu dostępu do danych][dataset-access-code]
5. Wklej kod do notesu lokalnych aplikacji w języku Python.
   
    ![Wklej kod do notesu][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Dostęp do zestawów danych pośrednich od eksperymenty usługi Machine Learning
Po uruchomieniu eksperymentu w Machine Learning Studio (klasyczny) możliwe jest uzyskanie dostępu do pośrednich zestawów danych z węzłów wyjściowych modułów. Pośredni zestawy danych są dane, które zostały utworzone i jest używany dla kroki pośrednie po uruchomieniu narzędzia modelu.

Może zostać oceniony pośrednich zestawów danych, tak długo, jak format danych jest zgodny z biblioteki klienta Python.

Obsługiwane są następujące formaty (stałe te znajdują się w `azureml.DataTypeIds` klasy):

* Zwykły tekst
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Można określić format, ustawiając kursor nad węzłem danych wyjściowych modułu. Jest on wyświetlany wraz z nazwą węzła, w etykietce narzędzia.

Niektóre moduły, takie jak moduł [Split][split] , są wyprowadzane do formatu o nazwie `Dataset`, który nie jest obsługiwany przez bibliotekę kliencką języka Python.

![Format zestawu danych][dataset-format]

Musisz użyć modułu konwersji, takiego jak [Convert to CSV][convert-to-csv], aby uzyskać dane wyjściowe do obsługiwanego formatu.

![GenericCSV Format][csv-format]

Poniższe kroki pokazują przykładowi, który tworzy eksperymentu, uruchomi go i uzyskuje dostęp do zestawu danych pośrednich.

1. Tworzenie nowego eksperymentu.
2. Wstaw **zestawu danych treści dla dorosłych klasyfikacji binarnej dochodu spisu** modułu.
3. Wstaw moduł [podzielony][split] i Połącz jego dane wejściowe z danymi wyjściowymi modułu DataSet.
4. Wstaw moduł [Convert to CSV][convert-to-csv] i Połącz jego dane wejściowe z jednym z danych wyjściowych modułu [Split][split] .
5. Zapisz eksperymentu, uruchom go i poczekaj na zakończenie działania.
6. Kliknij węzeł wyjście w module [Konwertuj na wolumin CSV][convert-to-csv] .
7. Po wyświetleniu menu kontekstowego wybierz **Generuj kod dostępu do danych**.
   
    ![Menu kontekstowe][experiment]
8. Wybierz fragment kodu, a następnie skopiuj go do Schowka w wyświetlonym oknie.
   
    ![Generowanie kodu dostępu z poziomu menu kontekstowego][intermediate-dataset-access-code]
9. Wklej kod w notesie.
   
    ![Wklej kod do notesu][ipython-intermediate-dataset]
10. Można wizualizować dane przy użyciu matplotlib. Spowoduje to wyświetlenie w histogram kolumny okres ważności:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>Użyj biblioteki klienta języka Python Machine Learning, aby uzyskać dostęp, Odczyt, tworzenie i Zarządzanie zestawami danych
### <a name="workspace"></a>Obszar roboczy
Obszar roboczy jest punkt wejścia dla biblioteki klienta Python. Podaj klasę `Workspace` z IDENTYFIKATORem obszaru roboczego i tokenem autoryzacji, aby utworzyć wystąpienie:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Wyliczanie zestawów danych
Aby wyliczyć wszystkie zestawy danych w danym obszarze roboczym:

    for ds in ws.datasets:
        print(ds.name)

Aby wyliczyć właśnie utworzone przez użytkownika zestawy danych:

    for ds in ws.user_datasets:
        print(ds.name)

Aby wyliczyć tylko zestawy danych przykładu:

    for ds in ws.example_datasets:
        print(ds.name)

Zestaw danych można uzyskać dostęp, według nazwy (która jest rozróżniana wielkość liter):

    ds = ws.datasets['my dataset name']

Lub możesz do niego dostęp przez indeks:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadane
Zestawy danych obejmują metadane, oprócz zawartości. (Pośrednie zestawy danych są wyjątkiem od tej reguły i nie masz żadnych metadanych).

Niektóre wartości metadanych przypisanych przez użytkownika w czasie tworzenia:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Inne są wartości przypisane przez uczenie Maszynowe Azure:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Zobacz `SourceDataset` klasy, aby uzyskać więcej informacji na temat dostępnych metadanych.

### <a name="read-contents"></a>Czytaj zawartość
Fragmenty kodu udostępniane przez Machine Learning Studio (klasyczne) automatycznie pobierają i deserializacjiją zestaw danych do obiektu Pandas Dataframe. Jest to zrobić za pomocą `to_dataframe` metody:

    frame = ds.to_dataframe()

Jeśli wolisz pobrać dane pierwotne i samodzielnie wykonać deserializacji, który jest opcją. W tej chwili to jedyną opcją dla formatów, takich jak "ARFF", którego nie można deserializować biblioteki klienta Python.

Aby odczytać zawartość jako tekst:

    text_data = ds.read_as_text()

Aby odczytać zawartość jako wartość binarną:

    binary_data = ds.read_as_binary()

Możesz też po prostu otworzyć strumienia zawartości:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Utwórz nowy zestaw danych
Biblioteki klienta Python umożliwia przekazywanie zestawów danych z programu Python. Te zestawy danych będą dostępne do użycia w obszarze roboczym.

Jeśli masz dane w pandas DataFrame, użyj następującego kodu:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Jeśli dane już jest serializowana, możesz użyć:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Biblioteka klientów języka Python jest w stanie pandas DataFrame do następujących formatów serializacji (stałe te znajdują się w `azureml.DataTypeIds` klasy):

* Zwykły tekst
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Zaktualizuj istniejący zestaw danych
Jeśli próbujesz przekazać nowy zestaw danych o nazwie, który odpowiada istniejący zestaw danych, powinna pojawić się błąd konfliktu.

Aby zaktualizować istniejący zestaw danych, należy najpierw pobrać odwołanie do istniejącego zestawu danych:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Następnie użyj `update_from_dataframe` do serializacji i zastąp jego zawartość zestawu danych na platformie Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Jeśli chcesz do serializowania danych na inny format, należy określić wartość dla opcjonalnego `data_type_id` parametru.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcjonalnie możesz ustawić nowy opis, określając wartość dla `description` parametru.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcjonalnie możesz ustawić nową nazwę, określając wartość dla `name` parametru. Od teraz będzie można pobrać zestawu danych, przy użyciu nowej nazwy. Poniższy kod aktualizuje dane, nazwę i opis.

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

`data_type_id`, `name` i `description` parametry są opcjonalne i domyślnie ich poprzedniej wartości. `dataframe` Parametru jest zawsze wymagana.

Jeśli dane już jest serializowana, użyj `update_from_raw_data` zamiast `update_from_dataframe`. Jeśli po prostu Przekaż w `raw_data` zamiast `dataframe`, działa w podobny sposób.

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

