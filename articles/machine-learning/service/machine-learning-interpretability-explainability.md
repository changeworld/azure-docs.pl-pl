---
title: Możliwość interpretowania modelu
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać zestawu SDK usługi Azure Machine Learning współdziałania wyjaśniający, dlaczego wykonuje prognozy w modelu. Może służyć podczas szkolenia oraz wnioskowania Aby zrozumieć, jak model wykonuje prognozy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: fbcafb61ecd69f58bb3c14d1b15f36f1b21f2833
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469781"
---
# <a name="azure-machine-learning-interpretability-sdk"></a>Współdziałania usługi Azure Machine Learning zestawu SDK

W tym artykule nauczysz wyjaśniający, dlaczego modelu wprowadzone prognozy go przy użyciu zestawu SDK usługi Azure Machine Learning współdziałania. Możliwość wyjaśnić modelu ważne jest, z następujących powodów:

* Klienci i uczestnicy projektu chce wiedzieć, **Jeśli ufają przewidywań modelu sprawia, że**.
* Jako analitykiem danych, którą chcesz poznać **sposób tworzenia zapytań względem modelu uzyskiwanie przydatnego wglądu**. Należy również narzędzia do podejmowania świadomych decyzji na **ulepszenie modelu**.
* Jako firma, należy zrozumieć **zachowanie modelu za pomocą zróżnicowanych danych wejściowych dystrybucje** i **będzie modelu zachowanie podczas analizowania określone dane wejściowe**.

Machine learning współdziałania jest ważne w dwóch fazach cyklu programowania uczenia maszynowego: **szkolenia** czasu i **wnioskowania** czasu:

* Podczas **szkolenia**: Projektanci modelu i ewaluatory wymagają współdziałania narzędzia do wyjaśnienia danych wyjściowych modelu do uczestników projektu do tworzenia relacji zaufania. Muszą szczegółowych informacji o modelu tak, aby ich debugowania modelu i podejmowania decyzji o czy zachowanie jest zgodne swoje cele. Na koniec należy upewnić się, że model nie jest obciążona.
* Podczas **wnioskowania**: Prognozy trzeba explainable do osób, które korzystają z modelu. Na przykład, dlaczego modelu odmawianie pożyczki hipoteczny i przewidzieć, że portfelu inwestycji niesie ze sobą większe ryzyko?

Zestaw SDK usługi Azure Machine Learning współdziałania zawiera technologii opracowany przez firmę Microsoft oraz sprawdzonych bibliotek innych firm (na przykład, kształtu i wapna). Zestaw SDK tworzy wspólny interfejs API różnych bibliotek zintegrowane i integruje usługi Azure Machine Learning. Korzystając z tego zestawu SDK, można wyjaśnić modeli uczenia maszynowego **globalnie na wszystkie dane**, lub **lokalnie w punkcie danych specyficznych dla** przy użyciu technologii z najnowocześniejszych w sposób łatwy w użyciu i skalowalne.

## <a name="how-does-it-work"></a>Jak to działa?

Usługa Azure Machine Learning współdziałania można zastosować zrozumienie zachowania globalnego lub predykcje określonego modelu. Pierwsza nazywa globalnego wyjaśnienie a drugim jest wyjaśnienie lokalnego.

Azure Machine Learning współdziałania metody można również podzielić na podstawie tego, czy metoda jest niezależny od modelu lub modelu określonego. Niektóre metody docelowej modeli określonego typu. Na przykład jego kształtu drzewa objaśnienie dotyczy tylko modeli oparta na drzewie. Niektóre metody modelu należy traktować jako czarne pole, takich jak mimic objaśnienie lub objaśnienie jądra dla kształtu. Zestaw SDK usługi Azure Machine Learning współdziałania wykorzystuje te różne metody, na podstawie zestawów danych, typy modeli i przypadkami użycia.

Usługa Azure Machine Learning współdziałania zwraca zestaw informacji, jak model sprawia, że jej prognozy. Informacje obejmują elementy, takie jak:

* Znaczenie względną funkcji globalnych/elementu lokalnego
* Relacja funkcji i prognozowania globalny/elementu lokalnego

## <a name="architecture"></a>Architektura

Zestaw SDK usługi Azure Machine Learning współdziałania są skonstruowane do dwóch pakietów języka Python:

* [azureml.Explain.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -pakiet główny, który zawiera funkcje, które są obsługiwane przez firmę Microsoft.
* `azureml.contrib.explain.model` — W wersji zapoznawczej i eksperymentalne funkcje, które możesz wypróbować.

    > [!IMPORTANT]
    > Elementy w contrib nie są w pełni obsługiwane. W miarę dojrzała eksperymentalne funkcje stopniowo zostaną przeniesione do pakietu głównego.

### <a name="explainers"></a>Explainers

Zestaw SDK usługi Azure Machine Learning współdziałania wprowadzono dwa zestawy explainers: Bezpośrednie Explainers i Meta Explainers.

__Bezpośrednie explainers__ pochodzą z biblioteki zintegrowanego. Zestaw SDK opakowuje wszystkich explainers umożliwiające eksponowanie wspólny interfejs API i format danych wyjściowych. Poniżej przedstawiono listę explainers bezpośrednich, dostępne w zestawie SDK:

> [!TIP]
> Jeśli wolisz bezpośrednio przy użyciu tych explainers, można bezpośrednio wywoływać je zamiast wspólny interfejs API i format danych wyjściowych.

* **Objaśnienie drzewa**: Objaśnienie drzewa kształtu, w którym priorytetowo potraktowano wielomianowej szybkie kształtu wartość szacowania algorytm czasowe specyficzne dla drzewa i decyzyjne drzewa.
* **Szczegółowe objaśnienie**: Oparte na wyjaśnienie od kształtu, szczegółowe objaśnienie "jest algorytm szybkiego zbliżenia wartości kształtu w modeli uczenia głębokiego, w oparciu o połączenie z DeepLIFT opisanych w dokumencie NIPS kształtu. Obsługiwane są modele TensorFlow i modele Keras przy użyciu zaplecza TensorFlow (istnieje również wstępną obsługę PyTorch) ".
* **Objaśnienie jądra**: Objaśnienie jądra dla kształtu używa specjalnie ważona lokalnego regresji liniowej, można oszacować wartości kształtu dla dowolnego modelu.
* **Objaśnienie naśladować**: Objaśnienie mimic opiera się na pomysł modeli zastępczy globalnego. Model globalnego zastępczy jest wewnętrznie interpretowanej modelu, który jest uczony obliczyć przybliżoną przewidywań modelu czarne pole, jak to możliwe. Analityk danych może interpretować modelu zastępczy, aby wyciągnąć wnioski o model czarne pole.
* **Objaśnienie wapna**: Oparte na wapna, objaśnienie wapna używa algorytmu z najnowocześniejszych lokalnego interpretowanej niezależny od modelu objaśnienia dotyczące (WAPNO) do tworzenia modeli lokalnych zastępczy. Inaczej niż w przypadku modeli globalnego zastępczy wapna koncentruje się na szkolenie modele zastępcze lokalny, aby wyjaśnić poszczególnych prognozy.
* **Objaśnienie tekstu HANOWI**: Objaśnienie tekstu HANOWI korzysta z hierarchicznej sieci uwagi w celu uzyskania objaśnienia dotyczące modelu z danych tekstowych dla danego czarne pole modelu tekstu. Uczenie modelu zastępczy HANOWI od modelu dla danego nauczycieli przewidywane w danych wyjściowych. Po szkoleniu globalnie w głównej części tekstu dodaliśmy krok fine-tune dla określonego dokumentu w celu zwiększenia dokładności wyjaśnienia. HANOWI za pomocą dwukierunkowych RNN dwie warstwy uwagi, o uwagę zdania i word. Po DNN jest uczony model dla nauczycieli i dostosowaniu do określonego dokumentu, firma Microsoft może wyodrębnić importances programu word z warstw uwagi. Znaleźliśmy HANOWI się bardziej precyzyjne niż wapna lub kształtu danych tekstowych, ale bardziej kosztowne warunków w szkolenia także czas. Jednakże ulepszyliśmy do chwili, szkolenia, dając użytkownikowi możliwość inicjowania do sieci za pomocą osadzenia wyrazów w drodze, mimo że nadal wolne. Uruchamiając HANOWI na zdalnej maszynie Wirtualnej procesorów GPU platformy Azure, można znacznie poprawiony czasu szkoleń. Implementacja HANOWI jest opisana w "Hierarchiczne sieciami uwagi do klasyfikowania dokumentów (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ wybierz odpowiedni objaśnienie bezpośrednie i automatycznie Generuj najważniejsze informacje o wyjaśnienie, na podstawie podanego modelu i zestawów danych. Meta explainers korzystać z wszystkich bibliotek (kształtu, wapna, Podgląd itp.), które firma Microsoft zintegrowane lub rozwinięte. Poniżej przedstawiono dostępne w zestawie SDK explainers metadanych:

* **Objaśnienie tabelarycznych**: Używane z zestawów danych tabelarycznych.
* **Objaśnienie tekst**: Używane z zestawami danych tekstu.

Ponadto do meta-Wybieranie z bezpośredniego explainers, meta explainers twórz dodatkowe funkcje, na podstawie podstawowej biblioteki i zwiększyć szybkość i skalowalność w przypadku bezpośredniego explainers.

Obecnie `TabularExplainer` stosuje następujące logikę w celu wywołania bezpośredniego Explainers:

1. Jeśli jest to model oparty na drzewie, zastosuj `TreeExplainer`, inne
2. Jeśli jest to model DNN, zastosuj `DeepExplainer`, inne
3. Jej traktowała jako model czarne pole i Zastosuj `KernelExplainer`

Wbudowana funkcja analizy `TabularExplainer` staną się bardziej zaawansowanych nad dodatkowymi bibliotekami są zintegrowane z zestawem SDK, a firma Microsoft Dowiedz się więcej o zaletach i wadach poszczególnych objaśnienie.

`TabularExplainer` ma również znaczne ulepszenia funkcji i wydajności za pośrednictwem bezpośredniego Explainers:

* **Podsumowanie zestawu inicjowania**. W przypadku, gdy szybkość wyjaśnienie jest najważniejsze firma Microsoft podsumować zestaw danych inicjowania i wygenerować niewielki zestaw reprezentatywnej próbki przyspiesza wyjaśnienie zarówno globalne i lokalne.
* **Próbkowanie zestawu danych oceny**. Jeśli użytkownik przekazuje duży zestaw przykładów oceny, ale faktycznie nie potrzebuje wszystkich z nich ma zostać obliczone, parametr próbkowania można ustawić wartość true, aby przyspieszyć globalnego wyjaśnienie.

Na poniższym diagramie przedstawiono relację między dwoma zestawami bezpośrednio i meta explainers.

[![MArchitektura współdziałania Learning achine](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Obsługiwane modele

Modele, które są uczone zestawów danych w języku Python na `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, lub `scipy.sparse.csr_matrix` format jest obsługiwany przez zestaw SDK współdziałania usługi Machine Learning.

Funkcje wyjaśnienie zaakceptować modeli i potoków jako dane wejściowe. Jeśli model zostanie podany, model musi implementować funkcji prognozowania `predict` lub `predict_proba` , zgodne ze Konwencji Scikit. Jeśli nie podano potoku (Nazwa skryptu potoku), funkcji wyjaśnienie przyjęto, że uruchamianie skryptu potoku zwraca prognozę.

### <a name="local-and-remote-compute-target"></a>Lokalne i zdalne obliczeniowego elementu docelowego

Zestaw SDK Machine Learning współdziałania jest przeznaczona do pracy z zarówno lokalne i zdalne obliczeniowych elementów docelowych. Jeśli uruchomiony lokalnie, funkcje zestawu SDK nie skontaktuje się z dowolnej usługi platformy Azure. Można zdalnie uruchamiać wyjaśnienie na obliczeniowego usługi Azure Machine Learning i informacje o wyjaśnienie Zaloguj się do uruchamiania usług historii Azure Machine Learning. Po zalogowaniu się te informacje, raporty i wizualizacje z wyjaśnienie są łatwo dostępne w witrynie Azure Machine Learning obszaru roboczego Portal analiza użytkowników.

## <a name="train-and-explain-locally"></a>Szkolenie i wyjaśnić lokalnie

1. Uczenie modelu w lokalnego notesu programu Jupyter. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Wywołanie objaśnienie: Aby zainicjować obiekt objaśnienie, należy przekazać model, dane szkoleniowe, funkcje zainteresowania (opcjonalnie) i nazwy klas danych wyjściowych (jeśli klasyfikacja) do objaśnienie. Poniżej przedstawiono sposób tworzenia wystąpienia obiektu objaśnienie przy użyciu [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), i `LimeExplainer` lokalnie. `TabularExplainer` to wywołanie jednej z trzech explainers poniżej (`TreeExplainer`, `DeepExplainer`, lub `KernelExplainer`) i automatycznie wybiera najbardziej odpowiednią dla przypadków użycia. Można jednak każdego z jego trzy podstawowe explainers bezpośrednio wywoływać.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    lub
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Pobiera globalne funkcji wartości ważności.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Wartości ważności lokalnych funkcji: Użyj następujące wywołania funkcji, aby wyjaśnić, wystąpienie indywidualne lub grupy wystąpień.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    lub
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Nauczanie i wyjaśniono, zdalny

Chociaż możesz uczyć się na różnych celów obliczeń, obsługiwane przez usługę Azure Machine Learning, w przykładzie w tej sekcji pokazano, jak to zrobić za pomocą AMLCompute.

1. Utwórz skrypt szkoleniowy w lokalnego notesu programu Jupyter (na przykład run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Postępuj zgodnie z instrukcjami [Konfigurowanie obliczeniowych elementów docelowych do trenowania modelu](how-to-set-up-training-targets.md#amlcompute) Aby dowiedzieć się więcej o sposobie konfigurowania Azure obliczeniowego usługi Machine Learning jako obliczeniowego elementu docelowego i przesłać przebieg szkolenia.

3. Pobierz wyjaśnienie lokalnego notesu programu Jupyter. 
    > [!IMPORTANT]
    > Elementy w contrib nie są w pełni obsługiwane. W miarę dojrzała eksperymentalne funkcje stopniowo zostaną przeniesione do pakietu głównego.

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić kolekcję notesów programu Jupyter, demonstrujące z powyższymi instrukcjami, zobacz [notesów przykładowej usługi Azure Machine Learning współdziałania](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).