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
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631424"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Możliwość interpretowania modelu w usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Omówienie możliwości interpretowania modelu

Zdolność interpretacyjną ma kluczowe znaczenie zarówno dla analityków danych, audytorów, jak i decydentów biznesowych, aby zapewnić zgodność z polityką firmy, standardami branżowymi i przepisami rządowymi:

+ Analitycy danych potrzebują możliwości wyjaśnienia swoich modeli kierownictwu i zainteresowanym stronom, aby mogli zrozumieć wartość i dokładność swoich wyników. Wymagają one również interpretability do debugowania swoich modeli i podejmowania świadomych decyzji o tym, jak je poprawić. 

+ Kontrolerzy prawni potrzebują narzędzi do walidacji modeli w odniesieniu do zgodności z przepisami i monitorowania wpływu decyzji modeli na ludzi. 

+ Decydenci biznesowi potrzebują spokoju, mając możliwość zapewnienia przejrzystości użytkownikom końcowym. To pozwala im zdobyć i utrzymać zaufanie.


Umożliwienie wyjaśnienia modelu uczenia maszynowego jest ważne w dwóch głównych fazach opracowywania modelu:
+ W fazie szkolenia, jako projektanci modeli i oceniający mogą używać interpretability danych wyjściowych modelu do weryfikacji hipotez i budowania zaufania z zainteresowanymi stronami. Używają również wgląd w modelu do debugowania, sprawdzania poprawności zachowania modelu odpowiada ich celom i sprawdzić, czy nie jest to nieuczciwość modelu lub nieistotne funkcje.

+ W fazie wnioskowania przejrzystość wokół wdrożonych modeli umożliwia kierownictwu zrozumienie "po wdrożeniu" sposobu działania modelu i sposobu, w jaki jego decyzje traktują ludzi w prawdziwym życiu i wpływają na ich decyzje. 

## <a name="interpretability-with-azure-machine-learning"></a>Możliwość interpretowania za pomocą usługi Azure Machine Learning

Klasy interpretowalności są udostępniane za pośrednictwem wielu pakietów SDK: (Dowiedz się, jak [zainstalować pakiety SDK dla usługi Azure Machine Learning)](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* `azureml.interpret`, pakiet główny, zawierający funkcje obsługiwane przez firmę Microsoft.

* `azureml.contrib.interpret`, podgląd i funkcje eksperymentalne, które można wypróbować.

* `azureml.train.automl.automlexplainer`pakietu do interpretacji zautomatyzowanych modeli uczenia maszynowego.

Użyj `pip install azureml-interpret` `pip install azureml-interpret-contrib` i do ogólnego `pip install azureml-interpret-contrib` użytku oraz do użytku automl, aby uzyskać pakiety interpretability.


> [!IMPORTANT]
> Zawartość w `contrib` obszarze nazw nie jest w pełni obsługiwana. W miarę dojrzalej funkcji eksperymentalnych będą one stopniowo przenoszone do głównej przestrzeni nazw.
.



## <a name="how-to-interpret-your-model"></a>Jak interpretować model

Korzystając z klas i metod w SDK, można:
+ Wyjaśnij przewidywanie modelu, generując wartości ważności operacji dla całego modelu i/lub poszczególnych punktów danych. 
+ Osiągnij możliwość interpretacji modelu na rzeczywistych zestawach danych na dużą skalę, podczas szkolenia i wnioskowania.
+ Użyj interaktywnego pulpitu nawigacyjnego wizualizacji, aby odnajdować wzorce w danych i objaśnieniach w czasie szkolenia


W uczeniu maszynowym **funkcje** są polami danych używanymi do przewidywania docelowego punktu danych. Na przykład, aby przewidzieć ryzyko kredytowe, mogą być używane pola danych dotyczące wieku, rozmiaru konta i wieku konta. W takim przypadku wiek, rozmiar konta i wiek konta to **funkcje**. Znaczenie funkcji informuje, jak każde pole danych wpłynęło na prognozy modelu. Na przykład wiek może być intensywnie używany w przewidywaniu, podczas gdy rozmiar konta i wiek nie mają znaczącego wpływu na wartości przewidywania. Ten proces umożliwia analitykom danych wyjaśnienie wynikowych prognoz, dzięki czemu interesariusze mają wgląd w to, jakie funkcje są najważniejsze w modelu.

Tutaj dowiesz się więcej o obsługiwanych technikach interpretowania, obsługiwanych modelach uczenia maszynowego i obsługiwanych środowiskach uruchamiania.


## <a name="supported-interpretability-techniques"></a>Obsługiwane techniki interpretowania

 `azureml-interpret`wykorzystuje techniki interpretowania opracowane w [Interpret-Community](https://github.com/interpretml/interpret-community/), pakiet python open source do szkolenia modeli interpretacyjnych i pomaga wyjaśnić blackbox systemów AI. [Interpret-Community](https://github.com/interpretml/interpret-community/) służy jako gospodarz dla obsługiwanych objaśnień tego SDK i obecnie obsługuje następujące techniki interpretowania:

|Technika interpretalności|Opis|Typ|
|--|--|--------------------|
|1. Objaśnienie drzewa SHAP| [SHAP](https://github.com/slundberg/shap)'s tree explainer, który koncentruje się na wielomianowym czasie szybkiego algorytmu szacowania wartości SHAP specyficznego dla **drzew i zespołów drzew.**|Specyficzne dla modelu|
|2. Shap Głębokie Objaśnienie| Na podstawie wyjaśnień [shap,](https://github.com/slundberg/shap)Deep Explainer "jest szybkim algorytmem przybliżenia wartości SHAP w modelach uczenia głębokiego, który opiera się na połączeniu z DeepLIFT opisanym w [dokumencie SHAP NIPS.](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions) Obsługiwane są modele **TensorFlow** i modele **Keras korzystające** z zaplecza TensorFlow (istnieje również wstępna obsługa pyTorch)".|Specyficzne dla modelu|
|3. Objaśnienie liniowe SHAP| Objaśnienie liniowe [SHAP](https://github.com/slundberg/shap)oblicza wartości SHAP dla **modelu liniowego,** opcjonalnie uwzględniając korelacje między obiektami.|Specyficzne dla modelu|
|4. Objaśnienie jądra SHAP| Objaśnienie jądra [SHAP](https://github.com/slundberg/shap)używa specjalnie ważonej lokalnej regresji liniowej do oszacowania wartości SHAP dla **dowolnego modelu.**|Model-agnostyk|
|5. Mimic Explainer (Globalny surogat)| Mimic explainer opiera się na idei szkolenia [globalnych modeli zastępczych](https://christophm.github.io/interpretable-ml-book/global.html) naśladować modele blackbox. Globalny model zastępczy jest modelem z natury interpretowalnym, który jest przeszkolony w celu przybliżenia prognoz **dowolnego modelu czarnej skrzynki** tak dokładnie, jak to możliwe. Analitycy danych mogą interpretować model zastępczy, aby wyciągnąć wnioski na temat modelu czarnej skrzynki. Można użyć jednego z następujących modeli interpretowalnych jako modelu zastępczego: LightGBM (LGBMExplainableModel), Regresja liniowa (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) i Decision Tree (DecisionTreeExplainableModel).|Model-agnostyk|
|6. Objaśnienie ważności funkcji permutacji (PFI)| Znaczenie funkcji permutacji to technika używana do wyjaśniania modeli klasyfikacji i regresji, która jest inspirowana [papierem Breiman's Random Forests](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (patrz sekcja 10). Na wysokim poziomie sposób, w jaki działa, polega na losowym tasowaniu danych po jednej funkcji naraz dla całego zestawu danych i obliczaniu, jak bardzo zmienia się metryka wydajności odsetek. Im większa zmiana, tym ważniejsza jest ta funkcja. PFI może wyjaśnić ogólne zachowanie **dowolnego modelu bazowego,** ale nie wyjaśnia poszczególnych prognoz. |Model-agnostyk|




Oprócz opisanych powyżej technik interpretalności, wspieramy inny [objaśnienie oparte na SHAP,](https://github.com/slundberg/shap)tzw. `TabularExplainer` W zależności od `TabularExplainer` modelu, używa jednego z obsługiwanych objaśnień SHAP:

* TreeExplainer dla wszystkich modeli opartych na drzewach
* DeepExplainer dla modeli DNN
* LinearExplainer dla modeli liniowych
* KernelExplainer dla wszystkich innych modeli

`TabularExplainer`poczyniła również znaczące ulepszenia w zakresie funkcji i wydajności w stosunku do bezpośrednich objaśnień SHAP:

* **Podsumowanie zestawu danych inicjowania**. W przypadkach, gdy szybkość wyjaśnienia jest najważniejsza, podsumowujemy zestaw danych inicjowania i generujemy niewielki zestaw reprezentatywnych próbek, co przyspiesza generowanie wartości ogólnego i indywidualnego znaczenia funkcji.
* **Pobieranie próbek zestawu danych ewaluacyjnych**. Jeśli użytkownik przechodzi w dużym zestawie próbek oceny, ale faktycznie nie potrzebuje wszystkich z nich do oceny, próbkowanie parametr może być ustawiona na true, aby przyspieszyć obliczanie ogólnych objaśnień modelu.

Na poniższym diagramie przedstawiono bieżącą strukturę obsługiwanych objaśnień.

[![Architektura tłumaczeń uczenia maszynowego](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Obsługiwane modele uczenia maszynowego

Pakiet `azureml.interpret` zestawu SDK obsługuje modele przeszkolone w następujących formatach zestawu danych:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Funkcje objaśnienia akceptują zarówno modele, jak i potoki jako dane wejściowe. Jeśli model jest dostarczany, model musi `predict` implementować funkcję przewidywania lub `predict_proba` który jest zgodny z konwencją Scikit. Jeśli model nie obsługuje tego, można zawinąć modelu w funkcji, `predict` która `predict_proba` generuje taki sam wynik jak lub w Scikit i użyć tej funkcji otoki z wybranym objaśnienia. Jeśli potok jest podana, funkcja objaśnienia zakłada, że uruchomiony skrypt potoku zwraca przewidywanie. Korzystając z tej `azureml.interpret` techniki zawijania, może obsługiwać modele przeszkolone za pośrednictwem struktur uczenia głębokiego PyTorch, TensorFlow i Keras, a także klasycznych modeli uczenia maszynowego.

## <a name="local-and-remote-compute-target"></a>Lokalny i zdalny cel obliczeniowy

Pakiet `azureml.interpret` jest przeznaczony do pracy z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi. Jeśli działa lokalnie, funkcje SDK nie skontaktują się z żadnymi usługami platformy Azure. 

Można uruchomić wyjaśnienie zdalnie na platformie Azure Machine Learning Compute i rejestrować informacje o objaśnieniu w usłudze historii uruchamiania usługi Azure Machine Learning. Po zarejestrowaniu tych informacji raporty i wizualizacje z wyjaśnienia są łatwo dostępne w usłudze Azure Machine Learning studio do analizy użytkowników.


## <a name="next-steps"></a>Następne kroki

Zobacz [instrukcje dotyczące](how-to-machine-learning-interpretability-aml.md) włączania możliwości interpretowania dla modeli szkoleniowych zarówno lokalnie, jak i na zdalnych zasobach obliczeniowych usługi Azure Machine Learning. Zobacz [przykładowe notesy,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) aby uzyskać dodatkowe scenariusze.
