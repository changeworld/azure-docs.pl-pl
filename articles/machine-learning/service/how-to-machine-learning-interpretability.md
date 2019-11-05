---
title: Interpretowanie modeli w usłudze Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyjaśnić, dlaczego model wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning. Może być używany podczas szkoleń i wnioskowania, aby zrozumieć, jak model wykonuje przewidywania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 5f1008e8fcbbf7b82a694fd151a9dea9ca7f001e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515345"
---
# <a name="model-interpretability-in-azure-machine-learning-service"></a>Interpretowanie modeli w usłudze Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Przegląd interpretacji modelu

Interpretacja jest niezwykle ważna dla analityków danych i osób podejmujących decyzje biznesowe, aby zapewnić zgodność z zasadami firmy, standardami branżowymi i przepisami rządowymi:
+ Osoby odpowiedzialne za dane muszą wyjaśnić swoje modele do kierownictwa i udziałowców, aby mogli zrozumieć wartość i dokładność ich wyników. 
+ Przedsiębiorcy zajmujący się decyzjami biznesowymi muszą mieć świadomość, że użytkownicy końcowi mogą zapewnić przejrzystość i zachować zaufanie dla użytkowników końcowych

Umożliwienie wyjaśnienia modelu uczenia maszynowego jest ważne w przypadku dwóch głównych etapów tworzenia modeli:
+ W fazie szkoleniowej cyklu projektowania modelu uczenia maszynowego. Projektanci modeli i oceniający mogą wykorzystywać dane wyjściowe z możliwością interpretacji modelu do weryfikowania pożądanych elementów i tworzenia relacji zaufania z uczestnikami projektu. Wykorzystują one również wgląd w model do debugowania, sprawdzanie poprawności zachowania modelu dopasowuje się do ich celów i sprawdzają poprawność lub nieznaczące funkcje.
+ W fazie inferencing, tak jakby miał przezroczystość w porównaniu ze wdrożonymi modelami, pozwala kierownictwu zrozumieć "po wdrożeniu", jak działa model i jakie decyzje są traktowane i wpływają na użytkowników w czasie rzeczywistym. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretowanie przy użyciu Azure Machine Learning

W tym artykule dowiesz się, jak są implementowane koncepcje interpretowania modeli w zestawie SDK.

Korzystając z klas i metod w zestawie SDK, można uzyskać:
+ Wartości ważności funkcji dla funkcji nieprzetworzonych i skonstruowanych
+ Interpretacja rzeczywistych zestawów danych na dużą skalę podczas szkoleń i wnioskowania.
+ Interaktywne wizualizacje pomagające w odnajdywaniu wzorców danych i wyjaśnień w czasie uczenia


W uczeniu maszynowym **funkcje** są polami danych używanymi do przewidywania docelowego punktu danych. Na przykład w celu przewidywania ryzyka kredytowego mogą być używane pola danych dla wieku, rozmiaru konta i wieku konta. W tym przypadku wiek, rozmiar konta i wiek konta są **funkcjami**. Ważność funkcji informuje o tym, jak każde pole danych ma wpływ na przewidywania modelu. Na przykład wiek może być wielokrotnie używany w prognozie, podczas gdy rozmiar i wiek konta nie wpływają znacząco na dokładność przewidywania. Ten proces umożliwia analitykom danych wyjaśnienie wynikających z przewidywaniami, dzięki czemu zainteresowane strony mają wgląd w to, jakie punkty danych są najważniejsze w modelu.

Korzystając z tych narzędzi, można wyjaśnić modele uczenia maszynowego **globalnie dla wszystkich danych**lub **lokalnie w konkretnym punkcie danych** przy użyciu technologii najnowocześniejszych i skalowalnych w łatwy w użyciu sposób.

Klasy interpretacji są udostępniane za pomocą wielu pakietów SDK. Dowiedz się, jak [zainstalować pakiety SDK dla Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, główny pakiet zawierający funkcje obsługiwane przez firmę Microsoft.

* `azureml.contrib.interpret`, wersja zapoznawcza i eksperymentalne funkcje, które można wypróbować.

* `azureml.train.automl.automlexplainer` pakiet do interpretacji automatycznych modeli uczenia maszynowego.

> [!IMPORTANT]
> Zawartość w przestrzeni nazw `contrib` nie jest w pełni obsługiwana. Gdy eksperymentalne funkcje stają się dojrzałe, zostaną stopniowo przenoszone do głównej przestrzeni nazw.

## <a name="how-to-interpret-your-model"></a>Jak interpretować model

Można zastosować klasy interpretacji i metody, aby zrozumieć globalne zachowanie modelu lub określone przewidywania. Dawna nazwa jest nazywana globalnym wyjaśnieniem, a drugie — wyjaśnienie lokalne.

Metody można również klasyfikować w oparciu o to, czy metoda jest modelem niezależny od czy specyficznym dla modelu. Niektóre metody są przeznaczone dla określonego typu modeli. Na przykład, objaśnienie drzewa kształtu ma zastosowanie tylko do modeli opartych na drzewie. Niektóre metody traktują model jako czarne pole, takie jak wyjaśnienie śladu lub objaśnienie jądra kształtu. Pakiet `interpret` wykorzystuje te różne podejścia w oparciu o zestawy danych, typy modeli i przypadki użycia.

Dane wyjściowe to zestaw informacji na temat sposobu, w jaki dany model wykonuje prognozowanie, na przykład:
* Globalna i lokalna ważność funkcji względnej
* Funkcja globalna/lokalna i relacja prognozowania

### <a name="explainers"></a>Wyjaśnienia

Ten pakiet używa technik interpretacji opracowanych w ramach [interpretacji Community](https://github.com/interpretml/interpret-community/), pakietu języka Python Open Source na potrzeby uczenia modeli, które mogą być interpretowane i pomagają w wyjaśnieniu systemów Blackbox AI. [Interpretuj — społeczność](https://github.com/interpretml/interpret-community/) służy jako host dla obsługiwanych przez niego OBJAŚNIEŃ zestawu SDK i obecnie obsługuje następujące techniki interpretowania:

* **Objaśnienie drzewa kształtu**: wyjaśnienie drzewa [kształtu](https://github.com/slundberg/shap), które koncentruje się na algorytmie szacowania wartości czasu wielomianowego, charakterystycznym dla drzew i ich części drzewa.
* Szczegółowy **Opis kształtu**: na podstawie wyjaśnienia z [kształtu](https://github.com/slundberg/shap), głębokiego objaśniania "jest algorytmem zbliżeniowym o dużej szybkości dla wartości kształtu w modelach uczenia głębokiego, które kompilują się w połączeniu z DeepLIFT opisaną w [dokumencie kształt NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Obsługiwane są modele TensorFlow i modele Keras korzystające z zaplecza TensorFlow (istnieje również wstępna pomoc techniczna dla PyTorch) ".
* **Objaśnienie liniowe kształtu**: funkcja wyjaśniająca liniowego [kształtu](https://github.com/slundberg/shap)oblicza wartości kształtu dla modelu liniowego, opcjonalnie rozliczanie korelacji między funkcjami.

* **Wyjaśnienie jądra kształtu**: wyjaśnienie jądra [kształtu](https://github.com/slundberg/shap)używa specjalnie ważonej lokalnej regresji liniowej do oszacowania wartości kształtu dla dowolnego modelu.
* **Objaśnienie śladowe**: wyjaśnienie śladów opiera się na koncepcji, w [której można wyśladować modele Blackbox](https://christophm.github.io/interpretable-ml-book/global.html) . Globalny model zastępczy jest modelem, który jest interpretowany wewnętrznie, aby przybliżyć przewidywania modelu czarnego pudełka, jak to możliwe. Analityk danych może interpretować model zastępczy, aby rysować wnioski o modelu czarnego pudełka. Można użyć jednego z następujących modeli interpretowanych jako model zastępczy: LightGBM (LGBMExplainableModel), regresja liniowa (LinearExplainableModel), Gradient stochastycznego, który jest bardziej wyjaśniony model (SGDExplainableModel) i drzewo decyzyjne ( DecisionTreeExplainableModel).


* **Wyjaśnienie ważności funkcji permutacji**: ważność funkcji permutacji jest techniką używaną do wyjaśnienia modeli klasyfikacji i regresji, które są [sponsorowane przez papier Breiman lasów](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (patrz sekcja 10). Na wysokim poziomie, w jaki działa, jest to spowodowane losowo Shuffling danych jedną funkcją w danym czasie dla całego zestawu danych i obliczaniem, ile jest Metryka wydajności. Im większa zmiana, tym bardziej ważna jest funkcja.

* **Wyjaśnienie wapna** (`contrib`): w oparciu o [wapno](https://github.com/marcotcr/lime), w przypadku użycia przez program do tworzenia lokalnych modeli niezależny od (limonowego) algorytmem "% dekompozycji" można utworzyć lokalne modele. W przeciwieństwie do globalnych modeli zastępczych, WAPNo koncentruje się na lokalnych modelach dwuskładnikowych, aby wyjaśnić poszczególne przewidywania.
* **Objaśnienie tekstu Han** (`contrib`): objaśnienie tekstu Han używa sieci o hierarchicznej uwagi do uzyskiwania wyjaśnień modelu z danych tekstowych dla danego czarnego modelu tekstu. Pociąga za niego model zastępczy HAN w przypadku danych wyjściowych przewidywanego modelu czarnego pudełka. Po rozpoczęciu szkolenia globalnie w korpus tekstowym dodaje krok szczegółowy dla określonego dokumentu w celu poprawienia dokładności wyjaśnień. HAN używa dwukierunkowej RNN z dwiema warstwami uwagi, w których należy zwrócić uwagę na zdanie i słowo. Gdy DNN jest przeszkolony na czarnym pudełku i dostrojony do określonego dokumentu, użytkownik może wyodrębnić znaczenie wyrazów z warstw uwagi. HAN jest przedstawiany jako dokładniejszy niż WAPNo lub KSZTAŁTowanie danych tekstowych, ale tańsze w zakresie czasu szkolenia. Wprowadzono ulepszenia pozwalające użytkownikowi na zainicjowanie sieci przy użyciu osadzania dokładne programu Word w celu skrócenia czasu uczenia. Czas uczenia można znacznie ulepszyć, uruchamiając HAN na zdalnej maszynie wirtualnej procesora GPU platformy Azure. Implementacja HAN jest opisana w temacie ["hierarchiczne sieci do klasyfikacji dokumentów (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Objaśnienie tabelaryczne**: `TabularExplainer` korzysta z następującej logiki, aby wywoływać objaśnienie bezpośredniego [kształtu](https://github.com/slundberg/shap) :

    1. Jeśli jest to model oparty na drzewie, Zastosuj kształt `TreeExplainer`, w przeciwnym razie
    2. Jeśli jest to model DNN, Zastosuj kształt `DeepExplainer`, w przeciwnym razie
    3. Jeśli jest to model liniowy, Zastosuj kształt `LinearExplainer`, w przeciwnym razie
    3. Traktuj ją jako model czarno-Box i Zastosuj `KernelExplainer` kształtu


w `TabularExplainer` wprowadzono również znaczące ulepszenia funkcji i wydajności w odniesieniu do wyjaśnień bezpośredniego kształtu:

* **Podsumowanie zestawu danych inicjalizacji**. W przypadkach, gdy najważniejsza jest szybkość wyjaśnienia, podsumowujemy zestaw danych inicjalizacji i wygenerujemy mały zestaw reprezentatywnych próbek, co przyspiesza globalne i lokalne wyjaśnienie.
* **Próbkowanie zestawu danych oceny**. Jeśli użytkownik przejdzie do dużego zestawu próbek ewaluacyjnych, ale nie potrzebuje wszystkich ich do oceny, parametr próbkowania może być ustawiony na wartość true, aby przyspieszyć ogólne wyjaśnienie.

Na poniższym diagramie przedstawiono bieżącą strukturę wyjaśnień bezpośrednich i meta.

[![Machine Learning architektura interpretacji](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Obsługiwane modele

Wszystkie modele, które są przeszkolone na zestawach danych w języku Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`lub `scipy.sparse.csr_matrix` są obsługiwane przez pakiet `explain` interpretacji zestawu SDK.

Funkcje wyjaśniające akceptują zarówno modele, jak i potoki jako dane wejściowe. Jeśli jest dostarczany model, model musi implementować funkcję przewidywania `predict` lub `predict_proba`, która jest zgodna z Konwencją Scikit. Jeśli potoku (Nazwa skryptu potoku) jest podany, funkcja wyjaśnień zakłada, że skrypt uruchomionego potoku zwraca prognozę. Obsługujemy modele przeszkolone przez PyTorch, TensorFlow i Kerase platformy uczenia głębokiego.

### <a name="local-and-remote-compute-target"></a>Lokalne i zdalne miejsce docelowe obliczeń

Pakiet `explain` jest przeznaczony do pracy z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi. W przypadku uruchomienia lokalnego funkcje zestawu SDK nie będą kontaktować się z żadnymi usługami platformy Azure. Wyjaśnienie można uruchomić zdalnie na Azure Machine Learning COMPUTE i zalogować informacje o wyjaśnieniu do Azure Machine Learning uruchamiania usług historii. Po zarejestrowaniu tych informacji raporty i wizualizacje z wyjaśnienia są łatwo dostępne w portalu Obszar roboczy usługi Azure Machine Learning na potrzeby analizy użytkowników.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem jak włączyć funkcję interpretacji dla modeli — zarówno lokalnie, [jak](how-to-machine-learning-interpretability-aml.md) i na Azure Machine Learning zdalnych zasobów obliczeniowych. Zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/tabular-data) , aby uzyskać dodatkowe scenariusze.
