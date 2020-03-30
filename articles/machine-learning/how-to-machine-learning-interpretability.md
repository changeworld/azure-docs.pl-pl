---
title: Możliwość interpretowania modelu w usłudze Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyjaśnić, dlaczego model tworzy prognozy przy użyciu sdk usługi Azure Machine Learning. Może służyć podczas szkolenia i wnioskowania, aby zrozumieć, jak model sprawia, że prognozy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063999"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Możliwość interpretowania modelu w usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Omówienie możliwości interpretowania modelu

Zdolność interpretacyjną ma kluczowe znaczenie zarówno dla analityków danych, jak i decydentów biznesowych, aby zapewnić zgodność z polityką firmy, standardami branżowymi i przepisami rządowymi:
+ Analitycy danych potrzebują możliwości wyjaśnienia swoich modeli kierownictwu i zainteresowanym stronom, aby mogli zrozumieć wartość i dokładność swoich ustaleń 
+ Decydenci biznesowi potrzebują spokoju w zakresie zdolności do zapewnienia przejrzystości użytkownikom końcowym w celu zdobycia i utrzymania zaufania

Umożliwienie wyjaśnienia modelu uczenia maszynowego jest ważne w dwóch głównych fazach opracowywania modelu:
+ W fazie szkolenia cyklu rozwoju modelu uczenia maszynowego. Projektanci modeli i oceniający mogą używać interpretability danych wyjściowych modelu do weryfikacji hipotez i budowania zaufania z zainteresowanymi stronami. Używają również wgląd w modelu do debugowania, sprawdzania poprawności zachowania modelu odpowiada ich celom i sprawdzić, czy nie ma błędów lub nieistotnych funkcji.
+ W fazie wnioskowania przejrzystość wokół wdrożonych modeli umożliwia kierownictwu zrozumienie "po wdrożeniu" sposobu działania modelu i sposobu, w jaki jego decyzje traktują ludzi w prawdziwym życiu i wpływają na ich decyzje. 

## <a name="interpretability-with-azure-machine-learning"></a>Możliwość interpretowania za pomocą usługi Azure Machine Learning

W tym artykule dowiesz się, jak pojęcia dotyczące interpretacji modelu są implementowane w sdk.

Korzystając z klas i metod w SDK, można uzyskać:
+ Wartości ważności funkcji zarówno dla obiektów nieprzetworzonych, jak i zaprojektowanych
+ Możliwość interpretacji na rzeczywistych zestawach danych na dużą skalę, podczas szkolenia i wnioskowania.
+ Interaktywne wizualizacje pomagające w odkrywaniu wzorców w danych i wyjaśnieniach w czasie szkolenia


W uczeniu maszynowym **funkcje** są polami danych używanymi do przewidywania docelowego punktu danych. Na przykład, aby przewidzieć ryzyko kredytowe, mogą być używane pola danych dotyczące wieku, rozmiaru konta i wieku konta. W takim przypadku wiek, rozmiar konta i wiek konta to **funkcje**. Znaczenie funkcji informuje, jak każde pole danych wpłynęło na prognozy modelu. Na przykład wiek może być intensywnie używany w przewidywaniu, podczas gdy rozmiar konta i wiek nie mają znaczącego wpływu na dokładność przewidywania. Ten proces umożliwia analitykom danych wyjaśnienie wynikowych prognoz, dzięki czemu interesariusze mają wgląd w to, jakie punkty danych są najważniejsze w modelu.

Korzystając z tych narzędzi, można wyjaśnić modele uczenia maszynowego **globalnie na wszystkich danych**lub **lokalnie w określonym punkcie danych** przy użyciu najnowocześniejszych technologii w łatwy w użyciu i skalowalny sposób.

Klasy interpretability są udostępniane za pośrednictwem wielu pakietów SDK. Dowiedz się, jak [zainstalować pakiety SDK dla usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, pakiet główny, zawierający funkcje obsługiwane przez firmę Microsoft.

* `azureml.contrib.interpret`, podgląd i funkcje eksperymentalne, które można wypróbować.

* `azureml.train.automl.automlexplainer`pakietu do interpretacji zautomatyzowanych modeli uczenia maszynowego.

> [!IMPORTANT]
> Zawartość w `contrib` obszarze nazw nie jest w pełni obsługiwana. W miarę dojrzalej funkcji eksperymentalnych będą one stopniowo przenoszone do głównej przestrzeni nazw.

## <a name="how-to-interpret-your-model"></a>Jak interpretować model

Można zastosować klasy i metody interpretability, aby zrozumieć globalne zachowanie modelu lub określonych prognoz. Pierwsza z nich nazywana jest wyjaśnieniem globalnym, a druga nazywana jest lokalnym wyjaśnieniem.

Metody można również kategoryzować na podstawie tego, czy metoda jest niezależna od modelu, czy specyficzne dla modelu. Niektóre metody są przeznaczone dla niektórych typów modeli. Na przykład objaśnienie drzewa SHAP dotyczy tylko modeli opartych na drzewach. Niektóre metody traktują model jako czarne pole, takie jak objaśnienie mimicznych lub objaśnienie jądra SHAP. Pakiet `interpret` wykorzystuje te różne podejścia na podstawie zestawów danych, typów modelu i przypadków użycia.

Dane wyjściowe to zestaw informacji o tym, jak dany model dokonuje prognozowania, takich jak:
* Globalne/lokalne względne znaczenie funkcji
* Globalna/lokalna relacja funkcji i prognozowania

### <a name="explainers"></a>Objaśnienia

Ten pakiet wykorzystuje techniki interpretowania opracowane w [Interpret-Community](https://github.com/interpretml/interpret-community/), pakiecie python open source do szkoleń modeli interpretowalnych i pomaga wyjaśnić systemy Blackbox AI. [Interpret-Community](https://github.com/interpretml/interpret-community/) służy jako gospodarz dla obsługiwanych objaśnień tego SDK i obecnie obsługuje następujące techniki interpretowania:

* **SHAP Tree Explainer**: [SHAP](https://github.com/slundberg/shap)'s tree explainer, który koncentruje się na wielomianowym czasie szybkiego algorytmu szacowania wartości SHAP specyficznego dla drzew i zespołów drzew.
* **SHAP Deep Explainer**: Na podstawie wyjaśnienia z [SHAP](https://github.com/slundberg/shap), Deep Explainer "jest szybkim algorytmem przybliżenia wartości SHAP w modelach uczenia głębokiego, który opiera się na połączeniu z DeepLIFT opisanym w [dokumencie SHAP NIPS.](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions) Obsługiwane są modele TensorFlow i modele Keras korzystające z zaplecza TensorFlow (istnieje również wstępna obsługa pyTorch)".
* **Objaśnienie liniowe SHAP:** OBJaśnienie liniowe [SHAP](https://github.com/slundberg/shap)oblicza wartości SHAP dla modelu liniowego, opcjonalnie uwzględniając korelacje między obiektami.

* **Shap Kernel Explainer**: [SHAP](https://github.com/slundberg/shap)'s Kernel explainer używa specjalnie ważonej lokalnej regresji liniowej do oszacowania wartości SHAP dla dowolnego modelu.
* **Mimic Explainer**: Mimic explainer opiera się na idei szkolenia [globalnych modeli zastępczych](https://christophm.github.io/interpretable-ml-book/global.html) naśladować modele blackbox. Globalny model zastępczy jest modelem z natury interpretowalnym, który jest przeszkolony w celu przybliżenia prognoz modelu czarnej skrzynki tak dokładnie, jak to możliwe. Analityk danych może interpretować model zastępczy, aby wyciągnąć wnioski na temat modelu czarnej skrzynki. Jako model zastępczy można użyć jednego z następujących modeli interpretacyjnych: LightGBM (LGBMExplainableModel), Regresja liniowa (LinearExplainableModel), Model zstępowania gradientu stochastycznego (SGDExplainableModel) i Drzewo decyzyjne ( DecisionTreeExplainableModel).


* **Objaśnienie ważności funkcji permutacji:** Znaczenie funkcji permutacji jest techniką używaną do wyjaśnienia modeli klasyfikacji i regresji, która jest inspirowana [papierem Losowe lasy Breimana](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (patrz sekcja 10). Na wysokim poziomie sposób, w jaki działa, polega na losowym tasowaniu danych po jednej funkcji naraz dla całego zestawu danych i obliczaniu, jak bardzo zmienia się metryka wydajności odsetek. Im większa zmiana, tym ważniejsza jest ta funkcja.

* **LIME Explainer** (`contrib`): W oparciu o [LIME,](https://github.com/marcotcr/lime)LIME Explainer wykorzystuje najnowocześniejszy algorytm lokalnego interpretowania interpretacji -agnostyczne objaśnienia (LIME) do tworzenia lokalnych modeli zastępczych. W przeciwieństwie do globalnych modeli zastępczych, LIME koncentruje się na szkoleniu lokalnych modeli zastępczych w celu wyjaśnienia indywidualnych prognoz.
* **Han Text Explainer** (`contrib`): Han Text Explainer używa hierarchicznej sieci uwagi do uzyskiwania wyjaśnień modelu z danych tekstowych dla danego modelu tekstu w czarnym polu. Trenuje model zastępczy HAN na przewidywanych wyjściach danego modelu czarnej skrzynki. Po szkoleniu na całym świecie w korpusie tekstowym, dodaje krok dostrajania dla określonego dokumentu w celu zwiększenia dokładności wyjaśnień. Han używa dwukierunkowego RNN z dwiema warstwami uwagi, dla uwagi zdania i słowa. Po przeszkoleniu DNN w modelu czarnej skrzynki i dostrojeniu do określonego dokumentu użytkownik może wyodrębnić znaczenie słowa z warstw uwagi. Han jest pokazany jako dokładniejszy niż LIME lub SHAP dla danych tekstowych, ale bardziej kosztowny pod względem czasu szkolenia. Wprowadzono ulepszenia, aby dać użytkownikowi możliwość zainicjowania sieci za pomocą osadzania wyrazów GloVe w celu skrócenia czasu szkolenia. Czas szkolenia można znacznie poprawić, uruchamiając han na zdalnej maszynie wirtualnej platformy Azure GPU. Implementacja HAN jest opisana w ["Hierarchiczne sieci uwagi do klasyfikacji dokumentów (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Tabelaryczne Objaśnienie**: `TabularExplainer` wykorzystuje następującą logikę do wywołania direct [SHAP](https://github.com/slundberg/shap) Explainers:

    1. Jeśli jest to model oparty na `TreeExplainer`drzewie, zastosuj SHAP ,
    2. Jeśli jest to model DNN, zastosuj SHAP `DeepExplainer`, w innym
    3. Jeśli jest to model liniowy, zastosuj SHAP `LinearExplainer`, inaczej
    3. Potraktuj go jako model black-box i zastosuj SHAP`KernelExplainer`


`TabularExplainer`poczyniła również znaczące ulepszenia w zakresie funkcji i wydajności w stosunku do bezpośrednich objaśnień SHAP:

* **Podsumowanie zestawu danych inicjowania**. W przypadkach, gdy szybkość wyjaśnienia jest najważniejsza, podsumowujemy zestaw danych inicjowania i generujemy niewielki zestaw reprezentatywnych próbek, co przyspiesza zarówno globalne, jak i lokalne wyjaśnienie.
* **Pobieranie próbek zestawu danych ewaluacyjnych**. Jeśli użytkownik przechodzi w dużym zestawie próbek oceny, ale faktycznie nie potrzebuje wszystkich z nich do oceny, próbkowanie parametr może być ustawiona na true, aby przyspieszyć globalne wyjaśnienie.

Na poniższym diagramie przedstawiono aktualną strukturę bezpośrednich i meta-objaśniaczych.

[![Architektura tłumaczeń uczenia maszynowego](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Obsługiwane modele

Wszystkie modele, które są uczone `numpy.array`na `pandas.DataFrame` `iml.datatypes.DenseData`zestawach `scipy.sparse.csr_matrix` danych w pythonie `explain` , , lub format są obsługiwane przez pakiet interpretability zestawu SDK.

Funkcje objaśnienia akceptują zarówno modele, jak i potoki jako dane wejściowe. Jeśli model jest dostarczany, model musi `predict` implementować funkcję przewidywania lub `predict_proba` który jest zgodny z konwencją Scikit. Jeśli potok (nazwa skryptu potoku) jest podana, funkcja objaśnienia zakłada, że uruchomiony skrypt potoku zwraca przewidywanie. Obsługujemy modele wyszkolone przez struktury uczenia głębokiego PyTorch, TensorFlow i Keras.

### <a name="local-and-remote-compute-target"></a>Lokalny i zdalny cel obliczeniowy

Pakiet `explain` jest przeznaczony do pracy z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi. Jeśli działa lokalnie, funkcje SDK nie skontaktują się z żadnymi usługami platformy Azure. Można uruchomić wyjaśnienie zdalnie na platformie Azure Machine Learning Compute i rejestrować informacje objaśnienia w usłudze Azure Machine Learning Run History Services. Po zarejestrowaniu tych informacji raporty i wizualizacje z wyjaśnienia są łatwo dostępne w obszarze roboczym usługi Azure Machine Learning do analizy użytkownika.


## <a name="next-steps"></a>Następne kroki

Zobacz [instrukcje dotyczące](how-to-machine-learning-interpretability-aml.md) włączania możliwości interpretowania dla modeli szkoleniowych zarówno lokalnie, jak i na zdalnych zasobach obliczeniowych usługi Azure Machine Learning. Zobacz [przykładowe notesy,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) aby uzyskać dodatkowe scenariusze.
