---
title: Dostęp do zestawów danych z biblioteką klienta języka Python — proces nauki o danych zespołowych
description: Zainstaluj bibliotekę klienta języka Python i korzystać z nich, aby bezpiecznie uzyskiwać dostęp do danych usługi Azure Machine Learning i zarządzać nimi z lokalnego środowiska języka Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720983"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Dostęp do zestawów danych z językiem Python za pomocą biblioteki klienta Python usługi Azure Machine Learning
Podgląd biblioteki klienta usługi Microsoft Azure Machine Learning Python może umożliwić bezpieczny dostęp do zestawów danych usługi Azure Machine Learning z lokalnego środowiska języka Python i umożliwia tworzenie zestawów danych w obszarze roboczym i zarządzanie nimi.

Ten temat zawiera instrukcje dotyczące:

* instalowanie biblioteki klienta usługi Nauczanie maszynowe języka Python
* uzyskiwanie dostępu do zestawów danych usługi Azure Machine Learning i przekazywanie ich, w tym instrukcje dotyczące uzyskiwania autoryzacji dostępu do zestawów danych usługi Azure Machine Learning z lokalnego środowiska języka Python
* dostęp do pośrednich zestawów danych z eksperymentów
* używanie biblioteki klienta języka Python do wyliczania zestawów danych, uzyskiwania dostępu do metadanych, odczytywania zawartości zestawu danych, tworzenia nowych zestawów danych i aktualizowania istniejących zestawów danych

## <a name="prerequisites"></a><a name="prerequisites"></a>Wymagania wstępne
Biblioteka klienta języka Python została przetestowana w następujących środowiskach:

* Windows, Mac i Linux
* Python 2.7, 3.3 i 3.4

Ma zależność od następujących pakietów:

* Żądania
* python-dateutil
* Pandy

Zalecamy użycie dystrybucji Pythona, takiej jak [Anaconda](http://continuum.io/downloads#all) lub [Canopy](https://store.enthought.com/downloads/), które pochodzą z Pythona, IPython i trzech pakietów wymienionych powyżej zainstalowanych. Chociaż IPython nie jest ściśle wymagane, jest to doskonałe środowisko do manipulowania i wizualizacji danych interaktywnie.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Jak zainstalować bibliotekę klienta usługi Azure Machine Learning Python
Zainstaluj bibliotekę klienta usługi Azure Machine Learning Python, aby wykonać zadania opisane w tym temacie. Ta biblioteka jest dostępna w [indeksie pakietów Języka Python](https://pypi.python.org/pypi/azureml). Aby zainstalować go w środowisku Języka Python, uruchom następujące polecenie z lokalnego środowiska Python:

    pip install azureml

Alternatywnie, można pobrać i zainstalować ze źródeł na [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Jeśli masz zainstalowany git na swoim komputerze, możesz użyć pip, aby zainstalować bezpośrednio z repozytorium git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Uzyskiwanie dostępu do zestawów danych za pomocą urywków kodu
Biblioteka klienta języka Python zapewnia programowy dostęp do istniejących zestawów danych z eksperymentów, które zostały uruchomione.

Za pomocą interfejsu internetowego usługi Azure Machine Learning Studio (klasycznego) można wygenerować fragmenty kodu, które zawierają wszystkie informacje niezbędne do pobierania i deserializacji zestawów danych jako obiektów Pandas DataFrame na komputerze lokalnym.

### <a name="security-for-data-access"></a><a name="security"></a>Zabezpieczenia dostępu do danych
Fragmenty kodu dostarczone przez usługę Azure Machine Learning Studio (klasyczne) do użytku z biblioteką klienta języka Python zawierają identyfikator obszaru roboczego i token autoryzacji. Zapewniają one pełny dostęp do obszaru roboczego i muszą być chronione, na przykład hasło.

Ze względów bezpieczeństwa funkcja fragmentu kodu jest dostępna tylko dla użytkowników, którzy mają ustawioną rolę **jako właściciel** obszaru roboczego. Twoja rola jest wyświetlana w usłudze Azure Machine Learning Studio (klasyczna) na stronie **UŻYTKOWNICY** w obszarze **Ustawienia**.

![Zabezpieczenia][security]

Jeśli twoja rola nie jest ustawiona jako **Właściciel,** możesz poprosić o ponowne zaproszenie jako właściciela lub poprosić właściciela obszaru roboczego o podanie fragmentu kodu.

Aby uzyskać token autoryzacji, można wybrać jedną z następujących opcji:

* Poproś o token od właściciela. Właściciele mogą uzyskać dostęp do tokenów autoryzacji na stronie Ustawienia ich obszaru roboczego w usłudze Azure Machine Learning Studio (klasyczny). Wybierz **ustawienia** z lewego okienka i kliknij pozycję **TOKENY AUTORYZACJI,** aby wyświetlić tokeny podstawowe i pomocnicze. Chociaż tokeny autoryzacji podstawowej lub pomocniczej mogą być używane we fragmentie kodu, zaleca się, aby właściciele udostępniali tylko tokeny autoryzacji pomocniczej.

   ![Tokeny autoryzacji](./media/python-data-access/ml-python-access-settings-tokens.png)

* Poproś o awans do roli właściciela: bieżący właściciel obszaru roboczego musi najpierw usunąć Cię z obszaru roboczego, a następnie ponownie poprosić cię o to jako właściciela.

Gdy deweloperzy uzyskali identyfikator obszaru roboczego i token autoryzacji, będą mogli uzyskać dostęp do obszaru roboczego przy użyciu fragmentu kodu, niezależnie od ich roli.

Tokeny autoryzacji są zarządzane na stronie **TOKENY AUTORYZACJI** w obszarze **USTAWIENIA**. Można je ponownie wygenerować, ale ta procedura odwołuje dostęp do poprzedniego tokenu.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Uzyskiwanie dostępu do zestawów danych z lokalnej aplikacji języka Python
1. W programie Machine Learning Studio (klasycznym) kliknij pozycję **ZESTAWY DANYCH** na pasku nawigacyjnym po lewej stronie.
2. Wybierz zestaw danych, do którego chcesz uzyskać dostęp. Można wybrać dowolny z zestawów danych z listy **MY DATASETS** lub z listy **PRZYKŁADY.**
3. Na dolnym pasku narzędzi kliknij pozycję **Generuj kod dostępu do danych**. Jeśli dane są w formacie niezgodnym z biblioteką klienta języka Python, ten przycisk jest wyłączony.
   
    ![Zestawy danych][datasets]
4. Wybierz fragment kodu z wyświetlanych okien i skopiuj go do schowka.
   
    ![Przycisk Generuj kod dostępu do danych][dataset-access-code]
5. Wklej kod do notesu lokalnej aplikacji Python.
   
    ![Wklej kod do notesu][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Dostęp do pośrednich zestawów danych z eksperymentów usługi Machine Learning
Po uruchomieniu eksperymentu w programie Machine Learning Studio (klasycznym) można uzyskać dostęp do pośrednich zestawów danych z węzłów wyjściowych modułów. Pośrednie zestawy danych to dane, które zostały utworzone i używane do etapów pośrednich po uruchomieniu narzędzia modelu.

Pośrednie zestawy danych są dostępne tak długo, jak format danych jest zgodny z biblioteką klienta języka Python.

Obsługiwane są następujące formaty (stałe dla tych formatów znajdują się `azureml.DataTypeIds` w klasie):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Format można określić, najeżdżając kursorem na węzeł wyjściowy modułu. Jest on wyświetlany wraz z nazwą węzła w etykietce narzędzia.

Niektóre moduły, takie jak [split][split] moduł, dane `Dataset`wyjściowe do formatu o nazwie , który nie jest obsługiwany przez bibliotekę klienta języka Python.

![Format zestawu danych][dataset-format]

Aby uzyskać dane wyjściowe do obsługiwanego formatu, należy użyć modułu konwersji, takiego jak Convert to [CSV.][convert-to-csv]

![GenericCSV Format][csv-format]

Poniższe kroki pokazują przykład, który tworzy eksperyment, uruchamia go i uzyskuje dostęp do pośredniego zestawu danych.

1. Utwórz nowy eksperyment.
2. Wstaw moduł **zestawu danych klasyfikacji binarnej dochodu dla dorosłych.**
3. Włóż moduł [Podziału][split] i podłącz jego dane wejściowe do danych wyjściowych modułu zestawu danych.
4. Włóż moduł [Konwertuj na CSV][convert-to-csv] i podłącz jego wejście do jednego z wyjść modułu [Split.][split]
5. Zapisz eksperyment, uruchom go i poczekaj na zakończenie zadania.
6. Kliknij węzeł wyjściowy modułu [Konwertuj na CSV.][convert-to-csv]
7. Po wyświetleniu menu kontekstowego wybierz polecenie **Generuj kod dostępu do danych**.
   
    ![Menu kontekstowe][experiment]
8. Wybierz fragment kodu i skopiuj go do schowka z wyświetlane go okno.
   
    ![Generowanie kodu dostępu z menu kontekstowego][intermediate-dataset-access-code]
9. Wklej kod w notesie.
   
    ![Wklej kod do notesu][ipython-intermediate-dataset]
10. Można wizualizować dane za pomocą matplotlib. Spowoduje to wyświetlenie w histogramie dla kolumny wiekowej:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Korzystanie z biblioteki klienta usługi Python uczenia maszynowego w celu uzyskiwania dostępu do zestawów danych, odczytywania, tworzenia i zarządzania nimi
### <a name="workspace"></a>Workspace
Obszar roboczy jest punktem wejścia dla biblioteki klienta języka Python. Podaj `Workspace` klasie identyfikator obszaru roboczego i token autoryzacji, aby utworzyć wystąpienie:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Wyliczaj zestawy danych
Aby wyliczyć wszystkie zestawy danych w danym obszarze roboczym:

    for ds in ws.datasets:
        print(ds.name)

Aby wyliczyć tylko zestawy danych utworzone przez użytkownika:

    for ds in ws.user_datasets:
        print(ds.name)

Aby wyliczyć tylko przykładowe zestawy danych:

    for ds in ws.example_datasets:
        print(ds.name)

Dostęp do zestawu danych można uzyskać według nazwy (z uwzględnieniem wielkości litery):

    ds = ws.datasets['my dataset name']

Lub można uzyskać do niego dostęp przez indeks:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadane
Zestawy danych mają metadane, oprócz zawartości. (Pośrednie zestawy danych są wyjątkiem od tej reguły i nie mają żadnych metadanych).

Niektóre wartości metadanych są przypisywane przez użytkownika w czasie tworzenia:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Inne to wartości przypisane przez usługę Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Zobacz `SourceDataset` klasę, aby uzyskać więcej informacji na temat dostępnych metadanych.

### <a name="read-contents"></a>Zawartość odczytu
Fragmenty kodu dostarczone przez Machine Learning Studio (classic) automatycznie pobrać i deserialize zestawu danych do pandas DataFrame obiektu. Odbywa się to `to_dataframe` za pomocą metody:

    frame = ds.to_dataframe()

Jeśli wolisz pobrać nieprzetworzone dane i samodzielnie przeprowadzić deserializację, jest to opcja. W tej chwili jest to jedyna opcja dla formatów, takich jak "ARFF", których biblioteka klienta Języka Python nie może deserializacji.

Aby odczytać zawartość jako tekst:

    text_data = ds.read_as_text()

Aby odczytać zawartość jako binarną:

    binary_data = ds.read_as_binary()

Można również po prostu otworzyć strumień do zawartości:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Tworzenie nowego zestawu danych
Biblioteka klienta Języka Python umożliwia przekazywanie zestawów danych z programu Python. Te zestawy danych są następnie dostępne do użycia w obszarze roboczym.

Jeśli masz swoje dane w pandas DataFrame, użyj następującego kodu:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Jeśli dane są już serializowane, można użyć:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Biblioteka klienta języka Python jest w stanie serializować pandas DataFrame do `azureml.DataTypeIds` następujących formatów (stałe dla nich są w klasie):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualizowanie istniejącego zestawu danych
Jeśli spróbujesz przekazać nowy zestaw danych o nazwie zgodnej z istniejącym zestawem danych, powinien zostać wyświetlony błąd konfliktu.

Aby zaktualizować istniejący zestaw danych, należy najpierw uzyskać odwołanie do istniejącego zestawu danych:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Następnie `update_from_dataframe` użyj do serializacji i zastąpienia zawartości zestawu danych na platformie Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Jeśli chcesz serializować dane do innego formatu, określ wartość parametru opcjonalnego. `data_type_id`

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcjonalnie można ustawić nowy opis, określając wartość `description` parametru.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcjonalnie można ustawić nową nazwę, określając wartość `name` parametru. Od tej pory pobierzesz zestaw danych przy użyciu tylko nowej nazwy. Poniższy kod aktualizuje dane, nazwę i opis.

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

Parametry `data_type_id` `name` i `description` parametry są opcjonalne i domyślnie do ich poprzedniej wartości. Parametr `dataframe` jest zawsze wymagany.

Jeśli dane są już serializowane, użyj zamiast `update_from_raw_data` `update_from_dataframe`pliku . Jeśli po prostu `raw_data` przejść `dataframe`zamiast , to działa w podobny sposób.

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

