---
title: Możliwość interpretowania modelu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wyjaśnić, dlaczego modelu sprawia, że prognozy przy użyciu zestawu SDK usługi Azure Machine Learning. Może służyć podczas szkoleń i wnioskowania Aby zrozumieć, jak model wykonuje prognozy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: cba46a277dfce93d0080d8f04a26fd135407de15
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536742"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Model współdziałania z usługą Azure Machine Learning

W tym artykule dowiesz się, jak wyjaśnić, dlaczego modelu wprowadzone prognozy są tym jak za pomocą różnych pakietów współdziałania z zestawu SDK języka Python usługi Azure Machine Learning.

Za pomocą klasy i metody w zestawie SDK, można uzyskać:
+ Funkcja wartości znaczenia dla funkcji zaprojektowanych i raw
+ Współdziałania na rzeczywistych zestawach danych, na dużą skalę, podczas szkoleń i wnioskowania.
+ Interaktywne wizualizacje do odnajdywania wzorce w danych i wyjaśnienia w czasie szkolenia

Podczas fazy szkolenia w cyklu rozwoju modelu projektantów i ewaluatory służy modelem wyjściowym współdziałania w celu sprawdzenia hipotez i tworzenie relacji zaufania z zainteresowanych stron.  Wgląd w modelu są również używane do debugowania, sprawdzanie poprawności zachowania modelu odpowiada ich celów oraz sprawdzenia obecności odchylenie.

W usłudze machine learning **funkcji** pól danych używane do prognozowania docelowy punkt danych. Na przykład przewidzieć ryzyko kredytowe, pola danych wieku, rozmiaru konta i wiek konta mogą być używane. W tym przypadku wieku, rozmiaru konta i wiek konta są **funkcji**. Funkcja znaczenie informuje wpływ określane są przewidywania modelu dla każdego pola danych. Na przykład wiek może intensywnie używany w prognozie podczas rozmiarów kont i wieku nie mają wpływu na dokładność prognozowania znacznie. Ten proces umożliwia analitykom danych wyjaśnić, wynikowy prognozy, tak aby zainteresowane strony mają wgląd w punktów danych, które są dla Ciebie najważniejsze w modelu.

Za pomocą tych narzędzi można wyjaśnić modeli uczenia maszynowego **globalnie na wszystkie dane**, lub **lokalnie w punkcie danych specyficznych dla** przy użyciu technologii z najnowocześniejszych w sposób łatwy w użyciu i skalowalne.

Klasy współdziałania są udostępniane za pośrednictwem wielu pakietów SDK. Dowiedz się, jak [zainstalować pakiety zestawu SDK dla usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), pakietu głównego, zawierający funkcje obsługiwane przez firmę Microsoft.

* `azureml.contrib.explain.model`, w wersji zapoznawczej i eksperymentalne funkcje, które możesz wypróbować.

* `azureml.train.automl.automlexplainer` pakiet do interpretacji automatycznych modeli uczenia maszynowego.

> [!IMPORTANT]
> Zawartość w `contrib` przestrzeni nazw nie jest w pełni obsługiwane. W miarę dojrzała eksperymentalne funkcje stopniowo zostaną przeniesione do głównego obszaru nazw.

## <a name="how-to-interpret-your-model"></a>Sposób interpretowania modelu

Można zastosować współdziałania klasy i metody, aby zrozumieć zachowanie globalnego modelu lub określonych prognozy. Pierwsza nazywa globalnego wyjaśnienie a drugim jest wyjaśnienie lokalnego.

Metody można również podzielić na podstawie tego, czy metoda jest niezależny od modelu lub modelu określonego. Niektóre metody docelowej modeli określonego typu. Na przykład jego kształtu drzewa objaśnienie dotyczy tylko modeli oparta na drzewie. Niektóre metody modelu należy traktować jako czarne pole, takich jak mimic objaśnienie lub objaśnienie jądra dla kształtu. `explain` Pakietu wykorzystuje te różne metody, na podstawie zestawów danych, typy modeli i przypadkami użycia.

Wynik jest zestaw informacji na temat sposobu podanego modelu sprawia, że jej przewidywania, takich jak:
* Znaczenie względną funkcji globalnych/elementu lokalnego

* Relacja funkcji i prognozowania globalny/elementu lokalnego

### <a name="explainers"></a>Explainers

Istnieją dwa rodzaje explainers: Bezpośrednie Explainers i Explainers metadanych w zestawie SDK.

__Bezpośrednie explainers__ pochodzą z biblioteki zintegrowanego. Zestaw SDK opakowuje wszystkich explainers umożliwiające eksponowanie wspólny interfejs API i format danych wyjściowych. Jeśli wolisz bezpośrednio przy użyciu tych explainers, można bezpośrednio wywoływać je zamiast wspólny interfejs API i format danych wyjściowych. Poniżej przedstawiono listę explainers bezpośrednich, dostępne w zestawie SDK:

* **Objaśnienie drzewa kształtu**: Objaśnienie drzewa kształtu, w którym priorytetowo potraktowano wielomianowej szybkie kształtu wartość szacowania algorytm czasowe specyficzne dla drzewa i decyzyjne drzewa.
* **Objaśnienie głębokiego kształtu**: Oparte na wyjaśnienie od kształtu, szczegółowe objaśnienie "jest algorytm szybkiego zbliżenia wartości kształtu w modeli uczenia głębokiego, w oparciu o połączenie z DeepLIFT opisanych w dokumencie NIPS kształtu. Obsługiwane są modele TensorFlow i modele Keras przy użyciu zaplecza TensorFlow (istnieje również wstępną obsługę PyTorch) ".
* **Objaśnienie jądra kształtu**: Objaśnienie jądra dla kształtu używa specjalnie ważona lokalnego regresji liniowej, można oszacować wartości kształtu dla dowolnego modelu.
* **Objaśnienie naśladować**: Objaśnienie mimic opiera się na pomysł modeli zastępczy globalnego. Model globalnego zastępczy jest wewnętrznie interpretowanej modelu, który jest uczony obliczyć przybliżoną przewidywań modelu czarne pole, jak to możliwe. Analityk danych może interpretować modelu zastępczy, aby wyciągnąć wnioski o model czarne pole. Jako model zastępczy, można użyć jednego z następujących modeli interpretowanej: LightGBM (LinearExplainableModel), regresji liniowej (LinearExplainableModel), modelu explainable stochastycznego spadku gradientu (SGDExplainableModel) i drzewa decyzyjnego (DecisionTreeExplainableModel).


* **Objaśnienie znaczenie funkcji permutacji**: Znaczenie funkcji Permutacja to technika używana do wyjaśnienia modeli klasyfikacji i regresji, które są INSPIROWANE przez [Breiman w lasach Random dokument](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (patrz sekcja 10). Na wysokim poziomie jak działa polega na losowo rekonfiguracja danych jedną funkcję na raz dla całego zestawu danych i obliczanie, zmniejsza ilość Metryka wydajności zainteresowania. Im więcej zmian, niezwykle ważne jest tej funkcji.

* **Objaśnienie wapna** (`contrib`): Oparte na wapna, objaśnienie wapna używa algorytmu z najnowocześniejszych lokalnego interpretowanej niezależny od modelu objaśnienia dotyczące (WAPNO) do tworzenia modeli lokalnych zastępczy. Inaczej niż w przypadku modeli globalnego zastępczy wapna koncentruje się na szkolenie modele zastępcze lokalny, aby wyjaśnić poszczególnych prognozy.
* **Objaśnienie tekstu HANOWI** (`contrib`): Objaśnienie tekstu HANOWI korzysta z hierarchicznej sieci uwagi w celu uzyskania objaśnienia dotyczące modelu z danych tekstowych dla danego czarne pole modelu tekstu. Uczenie modelu zastępczy HANOWI od modelu dla danego nauczycieli przewidywane w danych wyjściowych. Po szkoleniu globalnie w głównej części tekstu dodaliśmy krok fine-tune dla określonego dokumentu w celu zwiększenia dokładności wyjaśnienia. HANOWI za pomocą dwukierunkowych RNN dwie warstwy uwagi, o uwagę zdania i word. Po DNN jest uczony model dla nauczycieli i dostosowaniu do określonego dokumentu, firma Microsoft może wyodrębnić importances programu word z warstw uwagi. Znaleźliśmy HANOWI się bardziej precyzyjne niż wapna lub kształtu danych tekstowych, ale bardziej kosztowne warunków w szkolenia także czas. Jednakże ulepszyliśmy do chwili, szkolenia, dając użytkownikowi możliwość inicjowania do sieci za pomocą osadzenia wyrazów w drodze, mimo że nadal wolne. Uruchamiając HANOWI na zdalnej maszynie Wirtualnej procesorów GPU platformy Azure, można znacznie poprawiony czasu szkoleń. Implementacja HANOWI jest opisana w "Hierarchiczne sieciami uwagi do klasyfikowania dokumentów (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).


__Meta explainers__ wybierz odpowiedni objaśnienie bezpośrednie i automatycznie Generuj najważniejsze informacje o wyjaśnienie, na podstawie podanego modelu i zestawów danych. Meta explainers korzystać z wszystkich bibliotek (kształtu, wapna, Podgląd itp.), które firma Microsoft zintegrowane lub rozwinięte. Poniżej przedstawiono dostępne w zestawie SDK explainers metadanych:

* **Objaśnienie tabelarycznych**: Używane z zestawów danych tabelarycznych.
* **Objaśnienie tekst**: Używane z zestawami danych tekstu.
* **Obraz objaśnienie**: Używane z zestawami danych obrazu.

Ponadto do meta-Wybieranie z bezpośredniego explainers, meta explainers twórz dodatkowe funkcje, na podstawie podstawowej biblioteki i zwiększyć szybkość i skalowalność w przypadku bezpośredniego explainers.

Obecnie `TabularExplainer` stosuje następujące logikę w celu wywołania bezpośredniego Explainers kształtu:

1. Jeśli jest to model oparty na drzewie, zastosuj kształtu `TreeExplainer`, inne
2. Jeśli jest to model DNN, zastosuj kształtu `DeepExplainer`, inne
3. Jej traktowała jako model czarne pole i zastosować kształtu `KernelExplainer`

Wbudowana funkcja analizy `TabularExplainer` staną się bardziej zaawansowanych nad dodatkowymi bibliotekami są zintegrowane z zestawem SDK, a firma Microsoft Dowiedz się więcej o zaletach i wadach poszczególnych objaśnienie.

`TabularExplainer` ma również znaczne ulepszenia funkcji i wydajności za pośrednictwem bezpośredniego Explainers:

* **Podsumowanie zestawu inicjowania**. W przypadku, gdy szybkość wyjaśnienie jest najważniejsze firma Microsoft podsumować zestaw danych inicjowania i wygenerować niewielki zestaw reprezentatywnej próbki przyspiesza wyjaśnienie zarówno globalne i lokalne.
* **Próbkowanie zestawu danych oceny**. Jeśli użytkownik przekazuje duży zestaw przykładów oceny, ale faktycznie nie potrzebuje wszystkich z nich ma zostać obliczone, parametr próbkowania można ustawić wartość true, aby przyspieszyć globalnego wyjaśnienie.

Na poniższym diagramie przedstawiono bieżąca struktura bezpośrednich i meta explainers.

[![W usłudze Machine Learning współdziałania architektury](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Obsługiwane modele

Modele, które są uczone zestawów danych w języku Python na `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, lub `scipy.sparse.csr_matrix` format jest obsługiwany przez współdziałania `explain` pakiet zestawu SDK.

Funkcje wyjaśnienie zaakceptować modeli i potoków jako dane wejściowe. Jeśli model zostanie podany, model musi implementować funkcji prognozowania `predict` lub `predict_proba` , zgodne ze Konwencji Scikit. Jeśli nie podano potoku (Nazwa skryptu potoku), funkcji wyjaśnienie przyjęto, że uruchamianie skryptu potoku zwraca prognozę. Firma Microsoft obsługuje modeli skonfigurowanych pod kątem za pośrednictwem PyTorch i TensorFlow, Keras głębokiego uczenia struktury.

### <a name="local-and-remote-compute-target"></a>Lokalne i zdalne obliczeniowego elementu docelowego

`explain` Pakietu jest przeznaczony do pracy z zarówno lokalne i zdalne obliczeniowych elementów docelowych. Jeśli uruchomiony lokalnie, funkcje zestawu SDK nie skontaktuje się z dowolnej usługi platformy Azure. Można zdalnie uruchamiać wyjaśnienie na obliczeniowego usługi Azure Machine Learning i informacje o wyjaśnienie Zaloguj się do uruchamiania usług historii Azure Machine Learning. Po zalogowaniu się te informacje, raporty i wizualizacje z wyjaśnienie są dostępne w portalu analiza użytkowników obszaru roboczego usługi Azure Machine Learning.

## <a name="interpretability-in-training"></a>Współdziałania szkolenia

### <a name="train-and-explain-locally"></a>Szkolenie i wyjaśnić lokalnie

1. Uczenie modelu w lokalnego notesu programu Jupyter.

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

2. Wywołanie objaśnienie: Można zainicjować obiektu objaśnienie, musisz przekazać swój model i pewne dane szkoleniowe objaśnienie konstruktora. Można również przekazać w nazwach funkcji i nazwy klas danych wyjściowych (jeśli klasyfikacja), które będą używane do Twojej wyjaśnień i wizualizacje większą przyjazność. Poniżej przedstawiono sposób tworzenia wystąpienia obiektu objaśnienie przy użyciu [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), i [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) lokalnie. `TabularExplainer` wywołuje jedną z trzech explainers kształtu poniżej (`TreeExplainer`, `DeepExplainer`, lub `KernelExplainer`) i automatycznie wybiera najbardziej odpowiednią dla przypadków użycia. Można jednak każdego z jego trzy podstawowe explainers bezpośrednio wywoływać.

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

3. Pobiera globalne funkcji wartości ważności.

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

4. Pobierz funkcji lokalnej wartości ważności: Użyj następujące wywołania funkcji, aby wyjaśnić, wystąpienie indywidualne lub grupy wystąpień. Należy pamiętać, PFIExplainer nie obsługuje lokalnego wyjaśnienia.

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

### <a name="train-and-explain-remotely"></a>Nauczanie i wyjaśniono, zdalny

Chociaż możesz uczyć się na różnych celów obliczeń, obsługiwane przez usługę Azure Machine Learning, w przykładzie w tej sekcji pokazano, jak zrobić to za pomocą obiektu docelowego obliczeniowego usługi Azure Machine Learning.

1. Utwórz skrypt szkoleniowy w lokalnego notesu programu Jupyter (na przykład run_explainer.py).

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

2. Postępuj zgodnie z instrukcjami [Konfigurowanie obliczeniowych elementów docelowych do trenowania modelu](how-to-set-up-training-targets.md#amlcompute) Aby dowiedzieć się więcej o sposobie konfigurowania Azure obliczeniowego usługi Machine Learning jako obliczeniowego elementu docelowego i przesłać przebieg szkolenia.

3. Pobierz wyjaśnienie lokalnego notesu programu Jupyter.

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

Aby zrozumieć i zinterpretować modelu, należy użyć pulpit nawigacyjny wizualizacji:

### <a name="global-visualizations"></a>Globalne wizualizacji

Poniższe wykresy zawierają globalny widok danych dotyczących uczonego modelu wraz z jej prognozy i wyjaśnienia.

|Wykres|Opis|
|----|-----------|
|Eksploracja danych| Omówienie zestawu danych wraz z wartościami prognozy.|
|Znaczenie globalne|Globalnie zawiera najważniejsze funkcje ważne K (można skonfigurować K). Ten wykres jest przydatne dla zrozumienia, zachowanie globalnego odpowiedni model.|
|Explanation Exploration|Pokazuje, jak funkcja jest odpowiedzialny za wprowadzanie zmian w modelu prognozowania wartości (lub prawdopodobieństwo prognozy wartości). |
|Podsumowanie| Użyto wartości ważności podpisem funkcji lokalnej dla wszystkich punktów danych do przedstawienia dystrybucji wpływu, jaki każdej funkcji ma wartość prognozy.|

[![Pulpit nawigacyjny wizualizacji globalne](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Lokalne wizualizacje

Kliknięcie w dowolnym momencie indywidualne dane w dowolnym momencie poprzedniego powierzchni załadować diagram znaczenie funkcji lokalnej punktu danych.

|Wykres|Opis|
|----|-----------|
|Znaczenie lokalne|Globalnie zawiera najważniejsze funkcje ważne K (można skonfigurować K). Ten wykres jest przydatne dla zrozumienia, lokalnego zachowania odpowiedni model w punkcie określonych danych.|
|Wytwarzania eksploracji|Umożliwia zmianę funkcji wartości wybranych danych punktu i obserwuj, jak te zmiany wpłyną na wartość prognozy.|
|Poszczególne oczekiwania warunkowego (ICE)| Umożliwia zmianę wartości funkcji od wartości minimalnej do maksymalnej wartości, aby zobaczyć, jak prognozowania punktu danych zmienia się podczas zmiany funkcji.|

[![Znaczenie funkcji lokalnej pulpit nawigacyjny wizualizacji](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Wytwarzania funkcji Pulpit nawigacyjny wizualizacji](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Pulpit nawigacyjny wizualizacji ICE geograficzne](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Należy pamiętać, że musisz mieć rozszerzenia elementu widget pulpitu nawigacyjnego wizualizacji włączona przed rozpoczęciem jądra programu Jupyter.

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

## <a name="raw-feature-transformations"></a>Nieprzetworzone funkcji przekształcenia

Opcjonalnie można przekazać potok przekształcania funkcji do objaśnienie do odbierania wyjaśnienia pod względem funkcji pierwotne przed transformacji (zamiast funkcji zaprojektowanych). Jeśli pominiesz ten objaśnienie zawiera wyjaśnienie pod względem funkcji zaprojektowanych.

Format obsługiwane przekształcenia jest taka sama, jak to opisano w [pandas skryptu sklearn](https://github.com/scikit-learn-contrib/sklearn-pandas). Ogólnie rzecz biorąc wszelkie przekształcenia są obsługiwane, tak długo, jak one działają w jednej kolumnie i dlatego są wyraźnie jeden do wielu. 

Możemy wyjaśnić funkcje pierwotne, za pomocą `sklearn.compose.ColumnTransformer` lub Podaj listę dopasowanego transformatora krotek. Komórka poniżej został użyty `sklearn.compose.ColumnTransformer`. 

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

W przypadku, gdy użytkownik chce uruchomić przykład z listą dopasowanego transformatora krotek, użyj następującego kodu: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

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

## <a name="interpretability-at-inferencing-time"></a>Współdziałania w czasie wnioskowania

Objaśnienie można wdrażać wraz z oryginalnym modelu i może służyć w czasie oceniania podanie informacji o wyjaśnienie lokalnego. Oferujemy również lekki explainers oceniania zapewnienie współdziałania w wnioskowania czasu wydajniej. Proces wdrażania objaśnienie do oceniania lekki jest podobny do wdrażania modelu i obejmuje następujące kroki:




1. Utwórz obiekt wyjaśnienie (np. przy użyciu TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Utwórz oceniania objaśnienie, za pomocą obiektu wyjaśnienie:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurowanie i rejestrowanie obrazu, który używa modelu objaśnienie oceniania.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. [Opcjonalnie] Pobieranie oceniania objaśnienie z chmury i przetestować objaśnienia

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

1. Wdróż obraz obliczeniowego elementu docelowego:

   1. Utwórz plik oceniania (przed wykonaniem tego kroku, postępuj zgodnie z instrukcjami [Wdrażaj modele za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) zarejestrować oryginalnego modelu prognozowania)

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

   1. Definiowanie konfiguracji wdrożenia (Ta konfiguracja zależy od wymagań modelu. W poniższym przykładzie zdefiniowano konfiguracji, który używa jednego rdzenia procesora CPU i 1 GB pamięci)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Utwórz plik z zależności środowiska

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

   1. Tworzenie niestandardowego pliku dockerfile przy użyciu zainstalowane g ++

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Wdrażanie utworzonemu obrazowi (Szacowany czas: 5 minut)

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

1. Czyszczenie: Aby usunąć wdrożonej usługi sieci web, użyj `service.delete()`.

## <a name="interpretability-in-automated-ml"></a>Współdziałania w automatycznych ML

Uczenie maszynowe automatycznych zawiera pakiety do interpretacji funkcji znaczenie w modelach skonfigurowanych pod kątem automatycznego. Ponadto scenariusze klasyfikacji umożliwiają pobieranie funkcję klasy na poziomie ważności. Istnieją dwie metody, aby włączyć to zachowanie, w ramach uczenia maszynowego automatyczne:

* Aby włączyć funkcję znaczenie dla modelu uczonego zespołu, użyj [ `explain_model()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) funkcji.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Aby włączyć funkcję znaczenie dla każdego indywidualnego uruchomienia przed szkolenia, ustaw `model_explainability` parametr `True` w `AutoMLConfig` obiektu wraz z dostarczania sprawdzania poprawności danych. Następnie użyj [ `retrieve_model_explanation()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) funkcji.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

Aby uzyskać więcej informacji, zobacz [porad](how-to-configure-auto-train.md#explain-the-model-interpretability) na włączenie funkcji współdziałania automatycznych machine Learning.

## <a name="next-steps"></a>Kolejne kroki

Aby wyświetlić kolekcję notesów programu Jupyter, demonstrujące z powyższymi instrukcjami, zobacz [notesów przykładowej usługi Azure Machine Learning współdziałania](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
