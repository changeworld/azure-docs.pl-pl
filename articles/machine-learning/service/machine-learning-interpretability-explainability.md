---
title: Możliwość interpretowania modelu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyjaśnić, dlaczego model wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning. Może być używany podczas szkoleń i wnioskowania, aby zrozumieć, jak model wykonuje przewidywania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 6b825e61542dabc92baf482ede6c93edc486e059
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002361"
---
# <a name="model-interpretability-with-azure-machine-learning"></a>Interpretowanie modeli przy użyciu Azure Machine Learning

W tym artykule dowiesz się, jak wyjaśnić, dlaczego model wykonał przewidywania, z różnymi pakietami interpretacji Azure Machine Learning Python SDK.

Korzystając z klas i metod w zestawie SDK, można uzyskać:
+ Wartości ważności funkcji dla funkcji nieprzetworzonych i skonstruowanych
+ Interpretacja rzeczywistych zestawów danych na dużą skalę podczas szkoleń i wnioskowania.
+ Interaktywne wizualizacje pomagające w odnajdywaniu wzorców danych i wyjaśnień w czasie uczenia

W fazie szkoleniowej cyklu programowania projektanci modeli i oceniający mogą wykorzystywać dane wyjściowe z możliwością interpretacji modelu do weryfikowania postanowień i tworzenia relacji zaufania z uczestnikami projektu.  Wykorzystują one również wgląd w model do debugowania, sprawdzanie poprawności zachowania modelu dopasowuje się do ich celów i sprawdzają poprawność.

W uczeniu maszynowym **funkcje** są polami danych używanymi do przewidywania docelowego punktu danych. Na przykład w celu przewidywania ryzyka kredytowego mogą być używane pola danych dla wieku, rozmiaru konta i wieku konta. W tym przypadku wiek, rozmiar konta i wiek konta są **funkcjami**. Ważność funkcji informuje o tym, jak każde pole danych ma wpływ na przewidywania modelu. Na przykład wiek może być wielokrotnie używany w prognozie, podczas gdy rozmiar i wiek konta nie wpływają znacząco na dokładność przewidywania. Ten proces umożliwia analitykom danych wyjaśnienie wynikających z przewidywaniami, dzięki czemu zainteresowane strony mają wgląd w to, jakie punkty danych są najważniejsze w modelu.

Korzystając z tych narzędzi, można wyjaśnić modele uczenia maszynowego **globalnie dla wszystkich danych**lub **lokalnie w konkretnym punkcie danych** przy użyciu technologii najnowocześniejszych i skalowalnych w łatwy w użyciu sposób.

Klasy interpretacji są udostępniane za pomocą wielu pakietów SDK. Dowiedz się, jak [zainstalować pakiety SDK dla Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)Pakiet główny zawierający funkcje obsługiwane przez firmę Microsoft.

* `azureml.contrib.explain.model`, wersja zapoznawcza i eksperymentalne funkcje, które można wypróbować.

* `azureml.train.automl.automlexplainer`Pakiet służący do interpretacji automatycznych modeli uczenia maszynowego.

> [!IMPORTANT]
> Zawartość w `contrib` przestrzeni nazw nie jest w pełni obsługiwana. Gdy eksperymentalne funkcje stają się dojrzałe, zostaną stopniowo przenoszone do głównej przestrzeni nazw.

## <a name="how-to-interpret-your-model"></a>Jak interpretować model

Można zastosować klasy interpretacji i metody, aby zrozumieć globalne zachowanie modelu lub określone przewidywania. Dawna nazwa jest nazywana globalnym wyjaśnieniem, a drugie — wyjaśnienie lokalne.

Metody można również klasyfikować w oparciu o to, czy metoda jest modelem niezależny od czy specyficznym dla modelu. Niektóre metody są przeznaczone dla określonego typu modeli. Na przykład, objaśnienie drzewa kształtu ma zastosowanie tylko do modeli opartych na drzewie. Niektóre metody traktują model jako czarne pole, takie jak wyjaśnienie śladu lub objaśnienie jądra kształtu. `explain` Pakiet wykorzystuje te różne podejścia w oparciu o zestawy danych, typy modeli i przypadki użycia.

Dane wyjściowe to zestaw informacji na temat sposobu, w jaki dany model wykonuje prognozowanie, na przykład:
* Globalna i lokalna ważność funkcji względnej

* Funkcja globalna/lokalna i relacja prognozowania

### <a name="explainers"></a>Wyjaśnienia

Istnieją dwa zestawy objaśnień: Bezpośrednich wyjaśnień i meta dewyjaśnione w zestawie SDK.

__Bezpośrednie wyjaśnienia__ pochodzą z bibliotek zintegrowanych. Zestaw SDK zawija wszystkie wyjaśnienia, aby uwidaczniali wspólny interfejs API i format wyjściowy. Jeśli nie możesz bezpośrednio korzystać z tych objaśnień, możesz je bezpośrednio wywołać zamiast używać wspólnego interfejsu API i formatu wyjściowego. Poniżej znajduje się lista bezpośrednich objaśnień dostępnych w zestawie SDK:

* **Objaśnienie drzewa kształtu**: Klasyfikator drzewa kształtu, który koncentruje się na algorytmie oceny wartości szybkiego kształtu czasu wielomianu, który jest specyficzny dla drzew i ich kompletów drzew.
* Szczegółowy **Opis kształtu**: W oparciu o objaśnienie z kształtu, szczegółowy opis "jest algorytmem zbliżeniowym o dużej szybkości, dla wartości kształtu w modelach uczenia głębokiego, które kompilują się w połączeniu z DeepLIFT opisanym w dokumencie kształt NIPS. Obsługiwane są modele TensorFlow i modele Keras korzystające z zaplecza TensorFlow (istnieje również wstępna pomoc techniczna dla PyTorch) ".
* **Wyjaśnienie jądra kształtu**: Wyjaśnienie jądra kształtu używa specjalnie ważonej lokalnej regresji liniowej do oszacowania wartości kształtu dla dowolnego modelu.
* **Objaśnienie śladowe**: Wyjaśnienie śladu jest oparte na koncepcji globalnych modeli zastępczych. Globalny model zastępczy jest modelem, który jest interpretowany wewnętrznie, aby przybliżyć przewidywania modelu czarnego pudełka, jak to możliwe. Analityk danych może interpretować model zastępczy, aby rysować wnioski o modelu czarnego pudełka. Możesz użyć jednego z następujących modeli interpretowanych jako model zastępczy: LightGBM (LinearExplainableModel), regresja liniowa (LinearExplainableModel), Gradient stochastycznego, który wyjaśnia model (SGDExplainableModel) i drzewo decyzyjne (DecisionTreeExplainableModel).


* **Objaśnienie ważności funkcji permutacji**: Ważność funkcji permutacji jest techniką używaną do wyjaśnienia modeli klasyfikacji i regresji, które są [sponsorowane przez papier Breimanych lasów](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (patrz sekcja 10). Na wysokim poziomie, w jaki działa, jest to spowodowane losowo Shuffling danych jedną funkcją w danym czasie dla całego zestawu danych i obliczaniem, ile metryki wydajności zmniejsza się odsetki. Im większa zmiana, tym bardziej ważna jest funkcja.

* **Wyjaśnienie wapna** (`contrib`): W oparciu o WAPNo, w przypadku użycia przez program do tworzenia lokalnych modeli, można użyć najnowocześniejszego algorytmu "niezależny od" W przeciwieństwie do globalnych modeli zastępczych, WAPNo koncentruje się na lokalnych modelach dwuskładnikowych, aby wyjaśnić poszczególne przewidywania.
* **Objaśnienie tekstu Han** (`contrib`): Objaśnienie tekstu HAN używa sieci o hierarchicznej uwagi do uzyskiwania wyjaśnień modelu z danych tekstowych dla danego modelu tekstu czarnego pudełka. Firma Microsoft szkoli model wieloskładnikowy HAN w przypadku danych wyjściowych przewidywanych przez model nauczycieli. Po przeprowadzeniu szkolenia globalnie w korpus tekstowym dodaliśmy krok dokładniejszy dla określonego dokumentu w celu poprawienia dokładności wyjaśnień. HAN używa dwukierunkowej RNN z dwiema warstwami uwagi, w których należy zwrócić uwagę na zdanie i słowo. Gdy DNN jest szkolony w modelu nauczyciela i dostrojony do określonego dokumentu, możemy wyodrębnić znaczenia wyrazów z warstw uwagi. Znaleźliśmy HAN, aby mieć dokładniejszy niż WAPNo lub KSZTAŁTowanie danych tekstowych, ale tańsze w odniesieniu do czasu szkolenia. Jednak wprowadziliśmy ulepszenia dotyczące czasu uczenia się, dając użytkownikowi możliwość zainicjowania sieci za pomocą dokładne osadzania wyrazów, chociaż nadal jest to powolne. Czas uczenia można znacznie ulepszyć, uruchamiając HAN na zdalnej maszynie wirtualnej procesora GPU platformy Azure. Implementacja HAN jest opisana w temacie ["hierarchiczne sieci do klasyfikacji dokumentów (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


__Meta wyjaśnień__ automatycznie wybierają odpowiedni bezpośredni wyjaśnień i generują najważniejsze informacje na podstawie danego modelu i zestawów danych. Meta deobjaśniający wykorzystuje wszystkie biblioteki (kształt, WAPNo, ślad itp.), które zostały zintegrowane lub rozwinięte. Poniżej znajdują się wyjaśnienia meta dostępne w zestawie SDK:

* **Objaśnienie tabelaryczne**: Używany z tabelarycznymi zestawami danych.
* **Objaśnienie tekstu**: Używany z zestawami danych tekstowych.
* **Objaśnienie obrazu**: Używany z zestawami danych obrazów.

Oprócz wielokrotnego wyboru wyjaśnień bezpośrednich, meta dewyjaśnione opracowują dodatkowe funkcje na podstawie podstawowych bibliotek, a także zwiększają szybkość i skalowalność w porównaniu bezpośrednio do wyjaśnień.

Obecnie `TabularExplainer` wykorzystuje następującą logikę do wywołania bezpośrednich objaśnień kształtu:

1. Jeśli jest to model oparty na drzewie, Zastosuj kształt `TreeExplainer`, inny
2. Jeśli jest to model DNN, Zastosuj kształt `DeepExplainer`, inny
3. Traktuj ją jako model czarno-Box i Zastosuj kształt`KernelExplainer`

Analiza wbudowana w `TabularExplainer` stanie się bardziej zaawansowana, ponieważ większa liczba bibliotek jest zintegrowana z zestawem SDK i uczymy się o profesjonalistach i wadach poszczególnych objaśnień.

`TabularExplainer`Ponadto wprowadzono znaczące ulepszenia funkcji i wydajności w porównaniu bezpośrednio do wyjaśnień:

* **Podsumowanie zestawu danych inicjalizacji**. W przypadkach, gdy najważniejsza jest szybkość wyjaśnienia, podsumowujemy zestaw danych inicjalizacji i wygenerujemy mały zestaw reprezentatywnych próbek, co przyspiesza globalne i lokalne wyjaśnienie.
* **Próbkowanie zestawu danych oceny**. Jeśli użytkownik przejdzie do dużego zestawu próbek ewaluacyjnych, ale nie potrzebuje wszystkich ich do oceny, parametr próbkowania może być ustawiony na wartość true, aby przyspieszyć ogólne wyjaśnienie.

Na poniższym diagramie przedstawiono bieżącą strukturę wyjaśnień bezpośrednich i meta.

[![Architektura Machine Learningj interpretacji](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Obsługiwane modele

Każdy model, który jest szkolony na zestawach danych `numpy.array`w `pandas.DataFrame`języku `iml.datatypes.DenseData`Python, `scipy.sparse.csr_matrix` , `explain` lub format jest obsługiwany przez pakiet interpretera zestawu SDK.

Funkcje wyjaśniające akceptują zarówno modele, jak i potoki jako dane wejściowe. Jeśli jest dostarczany model, model musi implementować funkcję `predict` przewidywania lub `predict_proba` , która jest zgodna z Konwencją Scikit. Jeśli potoku (Nazwa skryptu potoku) jest podany, funkcja wyjaśnień zakłada, że skrypt uruchomionego potoku zwraca prognozę. Obsługujemy modele przeszkolone przez PyTorch, TensorFlow i Kerase platformy uczenia głębokiego.

### <a name="local-and-remote-compute-target"></a>Lokalne i zdalne miejsce docelowe obliczeń

`explain` Pakiet jest przeznaczony do pracy z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi. W przypadku uruchomienia lokalnego funkcje zestawu SDK nie będą kontaktować się z żadnymi usługami platformy Azure. Wyjaśnienie można uruchomić zdalnie na Azure Machine Learning COMPUTE i zalogować informacje o wyjaśnieniu do Azure Machine Learning uruchamiania usług historii. Po zarejestrowaniu tych informacji raporty i wizualizacje z wyjaśnienia są łatwo dostępne w portalu Obszar roboczy usługi Azure Machine Learning na potrzeby analizy użytkowników.

## <a name="interpretability-in-training"></a>Interpretacja w szkoleniu

### <a name="train-and-explain-locally"></a>Uczenie i objaśnienie lokalne

1. Nauczenie modelu w lokalnym notesie Jupyter.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Wywołaj wyjaśnienie: Aby zainicjować obiekt objaśniający, należy przekazać model i niektóre dane szkoleniowe do konstruktora programu wyjaśniającego. Opcjonalnie można również przekazać nazwy funkcji i nazwy klas wyjściowych (jeśli jest to klasyfikacja), które będą używane do wprowadzania wyjaśnień i wizualizacji więcej informacji. Poniżej przedstawiono sposób tworzenia wystąpienia obiektu wyjaśniającego za pomocą [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)i [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) lokalnie. `TabularExplainer`wywołuje jeden z trzech objaśnień kształtu poniżej (`TreeExplainer`, `DeepExplainer`, lub `KernelExplainer`) i automatycznie wybiera najbardziej odpowiedni dla przypadku użycia. Można jednak wywoływać każdego z trzech podstawowych objaśnień bezpośrednio.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    lub

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   lub

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. Pobierz wartości ważności funkcji globalnej.

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. Pobierz wartości ważności funkcji lokalnych: Użyj następujących wywołań funkcji, aby wyjaśnić pojedyncze wystąpienie lub grupę wystąpień. Należy pamiętać, że PFIExplainer nie obsługuje lokalnych wyjaśnień.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    lub

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Uczenie i wyjaśnienie zdalne

Chociaż można przeprowadzić uczenie na różnych obiektach docelowych obliczeń obsługiwanych przez Azure Machine Learning, w przykładzie w tej sekcji pokazano, jak to zrobić za pomocą Azure Machine Learning celu obliczeń.

1. Utwórz skrypt szkoleniowy w lokalnym notesie Jupyter (na przykład run_explainer. PR).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Postępuj zgodnie z instrukcjami dotyczącymi [konfigurowania celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md#amlcompute) , aby dowiedzieć się, jak skonfigurować Azure Machine Learning obliczenia jako obiekt docelowy obliczeń i przesłać przebieg szkoleniowy.

3. Pobierz wyjaśnienie w lokalnym notesie Jupyter.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Wizualizacje

Użyj pulpitu nawigacyjnego wizualizacji, aby zrozumieć i zinterpretować model:

### <a name="global-visualizations"></a>Wizualizacje globalne

Poniższe wykresy przedstawiają globalny widok modelu przeszkolonego wraz z jego przewidywaniami i objaśnieniami.

|Wykres|Opis|
|----|-----------|
|Eksploracja danych| Przegląd zestawu danych wraz z wartościami przewidywania.|
|Ważność globalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Ten wykres jest przydatny do poznania globalnego zachowania modelu bazowego.|
|Wyjaśnienie eksploracji|Pokazuje, w jaki sposób funkcja jest odpowiedzialna za wprowadzanie zmian w wartości przewidywania modelu (lub prawdopodobieństwo wartości prognoz). |
|Podsumowanie| Program używa podpisanych wartości ważności funkcji lokalnych dla wszystkich punktów danych, aby pokazać rozkład wpływu każdej funkcji na wartość przewidywania.|

[![Pulpit nawigacyjny wizualizacji](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Wizualizacje lokalne

Możesz kliknąć dowolny punkt danych w dowolnym momencie z powyższych wykresów, aby załadować wykres ważności funkcji lokalnych dla danego punktu danych.

|Wykres|Opis|
|----|-----------|
|Ważność lokalna|Pokazuje najważniejsze funkcje z góry K (konfigurowalne K) globalnie. Ten wykres jest przydatny do poznania lokalnego zachowania modelu bazowego w określonym punkcie danych.|
|Eksploracja perturbation|Umożliwia zmianę wartości funkcji wybranego punktu danych i przestrzeganie sposobu, w jaki te zmiany wpłyną na wartość przewidywania.|
|Oczekiwanie na pojedyncze warunkowe (lód)| Pozwala na zmianę wartości funkcji z wartości minimalnej na wartość maksymalną, aby zobaczyć, w jaki sposób Prognoza punktu danych zmienia się w przypadku zmiany funkcji.|

[![Ważność funkcji lokalnej pulpitu nawigacyjnego wizualizacji](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Funkcja wizualizacji pulpitu nawigacyjnego perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Wizualizacje — wykresy z lodem](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Należy pamiętać, że przed rozpoczęciem Jupyter jądra należy mieć rozszerzenia widżetu dostępne na pulpicie nawigacyjnym wizualizacji.

* Notesy programu Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Aby załadować pulpit nawigacyjny wizualizacji, użyj następującego kodu:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Nieprzetworzone przekształcenia funkcji

Opcjonalnie można przekazać potok transformacji funkcji do narzędzia wyjaśniającego, aby otrzymywać wyjaśnienia w postaci funkcji nieprzetworzonych przed przekształceniem (a nie funkcją programu). W przypadku pominięcia tego elementu wyjaśnienie zawiera wyjaśnienia dotyczące funkcji wbudowanych.

Format obsługiwanych transformacji jest taki sam jak opisany w [skryptu sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Ogólnie rzecz biorąc, wszystkie przekształcenia są obsługiwane, o ile działają one w jednej kolumnie i dlatego są wyraźnie od siebie do wielu. 

Możemy wyjaśnić pierwotne funkcje przy użyciu `sklearn.compose.ColumnTransformer` lub listy zamocowanych krotek transformatora. Komórka poniżej używa `sklearn.compose.ColumnTransformer`. 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Jeśli chcesz uruchomić przykład z listą dopasowanych krotek transformatora, użyj następującego kodu: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>Interpretowanie w czasie inferencing

Program objaśniający można wdrożyć wraz z oryginalnym modelem i można go użyć w czasie oceniania w celu udostępnienia informacji o lokalnym wyjaśnieniu. Oferujemy także jaśniejsze wyjaśnienia dotyczące oceniania wagi, aby zapewnić możliwość interpretacji w czasie inferencing. Proces wdrażania bardziej jaśniejszej oceny oceniania jest podobny do wdrożenia modelu i obejmuje następujące kroki:




1. Utwórz obiekt wyjaśnień (np. przy użyciu TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Utwórz wyjaśnienie oceniania przy użyciu obiektu wyjaśnienia:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Skonfiguruj i zarejestruj obraz, który używa modelu oceny oceniania.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Obowiązkowe Pobierz wyjaśnienie oceniania z chmury i przetestowanie wyjaśnień

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Wdróż obraz w obiekcie docelowym obliczeń:

   1. Utwórz plik oceniania (przed wykonaniem tego kroku postępuj zgodnie z instrukcjami w temacie [Wdrażanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) , aby zarejestrować oryginalny model predykcyjny)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Zdefiniuj konfigurację wdrożenia (Ta konfiguracja zależy od wymagań modelu. Poniższy przykład definiuje konfigurację, która używa jednego rdzenia procesora CPU i 1 GB pamięci)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Utwórz plik ze zależnościami środowiska

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Utwórz niestandardową pliku dockerfile z zainstalowanym programem g + +

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Wdróż utworzony obraz (Szacowanie czasu: 5 minut)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. Testowanie wdrożenia

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Wyczyść: Aby usunąć wdrożonej usługi sieci web, użyj `service.delete()`.




## <a name="next-steps"></a>Następne kroki

Aby wyświetlić kolekcję notesów Jupyter, które przedstawiają powyższe instrukcje, zobacz [przykładowe notesy do interpretowania Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
