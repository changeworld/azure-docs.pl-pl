---
title: Co to jest zautomatyzowany ML/AutoML
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób Azure Machine Learning automatycznie wybierać algorytm i generować z niego model, aby zaoszczędzić czas, korzystając z parametrów i kryteriów, które podajesz, aby wybrać najlepszy algorytm dla modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: 4ed27009a3549757881c84d92b3b29b60ecbfbc1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790568"
---
# <a name="what-is-automated-machine-learning"></a>Co to jest automatyczne Uczenie maszynowe?

Zautomatyzowana Uczenie maszynowe, nazywana również automatycznym ML, to proces automatyzacji czasochłonnych, iteracyjnych zadań tworzenia modelu uczenia maszynowego. Umożliwia ona analitykom danych, specjalistom i deweloperom tworzenie modeli ML o wysokiej skalowalności, wydajności i produktywności, a jednocześnie zapewnia wysoką jakość modelu. Automatyczna ML jest oparta na przełomie od naszego [działu badawczego firmy Microsoft](https://arxiv.org/abs/1705.05355).

Tradycyjny rozwój modelu uczenia maszynowego jest czasochłonny i wymaga znacznej wiedzy o domenie oraz czasu na wyprodukowanie i porównanie dziesiątek modeli. Zastosuj automatyczne ML, gdy chcesz, aby Azure Machine Learning szkolić i dostrajania model przy użyciu określonej metryki docelowej. Następnie usługa iteruje przez algorytmy ML sparowane z opcjami wyboru funkcji, gdzie każda iteracja tworzy model z wynikiem uczenia. Im wyższy wynik, tym lepszy model jest traktowany jako "dopasowane" do danych.

Dzięki automatycznemu uczeniu maszynowego skracasz czas potrzebny do uzyskania gotowych do produkcji modeli ML z doskonałą prostotą i wydajnością.

## <a name="when-to-use-automated-ml"></a>Kiedy używać zautomatyzowanej ML

Zautomatyzowana tablica demokratyzuje proces tworzenia modelu uczenia maszynowego i umożliwia jego użytkownikom, bez względu na swoją wiedzę o nauce danych, identyfikację kompleksowego potoku uczenia maszynowego dla każdego problemu.

Analitycy danych, analitykowie i deweloperzy w różnych branżach mogą używać zautomatyzowanych ML do:

+ Implementowanie rozwiązań uczenia maszynowego bez obszernej wiedzy programistycznej
+ Oszczędność czasu i zasobów
+ Korzystanie z najlepszych rozwiązań dotyczących analizy danych
+ Zapewnianie problemu Agile — Rozwiązywanie problemów

W poniższej tabeli przedstawiono typowe zautomatyzowane przypadki użycia. 

Klasyfikacja| Regresja | Prognozowanie szeregów czasowych
---|---|---
[Wykrywanie oszustw](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Prognoza wydajności procesora CPU](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) |[Prognozowanie popytu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
[Przewidywania marketingowe](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Przewidywanie trwałości materiału](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-concrete-strength/auto-ml-regression-concrete-strength.ipynb)|[Prognozowanie sprzedaży](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)

## <a name="how-automated-ml-works"></a>Jak działa zautomatyzowany ML

Korzystając z **Azure Machine Learning**, można projektować i uruchamiać zautomatyzowane eksperymenty szkoleniowe ml z następującymi krokami:

1. **Określ problem związany z ml** , który ma zostać rozwiązany: Klasyfikacja, prognozowanie lub regresja

1. **Określ źródło i format etykiet danych szkoleniowych**: Numpy Arrays lub Pandas Dataframe

1. **Skonfiguruj cel obliczeń dla szkolenia modelu**, takiego jak [komputer lokalny, Azure Machine Learning obliczeń, zdalnych maszyn wirtualnych lub Azure Databricks](how-to-set-up-training-targets.md).  Dowiedz się więcej o zautomatyzowanym szkoleniu [dla zasobu zdalnego](how-to-auto-train-remote.md).

1. **Skonfiguruj zautomatyzowane parametry uczenia maszynowego** , które określają, ile iteracji przekroczy różne modele, ustawienia parametrów, zaawansowane przetwarzanie wstępne/cechowania i jakie metryki mają być sprawdzane podczas określania najlepszego modelu.  Można skonfigurować ustawienia automatycznego eksperymentu szkoleniowego w programie [Azure Machine Learning Studio](https://ml.azure.com)lub [za pomocą zestawu SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Prześlij przebieg szkoleniowy.**

  ![Automatyczne Uczenie maszynowe](./media/how-to-automated-ml/automl-concept-diagram2.png)

Podczas uczenia Azure Machine Learning tworzy wiele potoków równoległych, które wypróbuje różne algorytmy i parametry. Zostanie ona zatrzymana po trafieniu kryteriów zakończenia zdefiniowanych w eksperymentie.

Możesz również sprawdzić zarejestrowane informacje o uruchomieniu, które [zawierają metryki](how-to-understand-automated-ml.md) zebrane podczas uruchamiania. Uruchomienie szkoleniowe powoduje utworzenie serializowanego obiektu języka Python (pliku`.pkl`) zawierającego model i przetwarzanie wstępne przetwarzania danych.

Chociaż Kompilowanie modelu jest zautomatyzowane, można również [dowiedzieć się, jak ważne lub istotne funkcje są](how-to-configure-auto-train.md#explain) wygenerowane modele.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Przetwarzania wstępnego

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są wstępnie przetwarzane przy użyciu metod domyślnych i opcjonalnie w ramach zaawansowanego przetwarzania wstępnego.

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="automatic-preprocessing-standard"></a>Automatyczne przetwarzanie wstępne (standard)

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są automatycznie skalowane lub znormalizowane w celu zapewnienia prawidłowego wykonywania algorytmów.  Podczas uczenia modelu jedna z następujących technik skalowania lub normalizacji zostanie zastosowana do każdego modelu.

|Skalowanie&nbsp;&&nbsp;normalizacji| Opis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Ujednolicenie funkcji przez usunięcie średniej i skalowania do wariancji jednostek  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Przekształca funkcje, przeskalowane każdą funkcję według minimalnej i maksymalnej wartości tej kolumny  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skaluj każdą funkcję przez maksymalną wartość bezwzględną |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ta funkcja skalowania według ich zakresu quantile |
| [Z](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Zmniejszenie liczby liniowej przy użyciu jednoznacznej dekompozycji danych w celu ich zaprojektowania do mniejszej przestrzeni wymiarowej |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ta metoda przekształcania dokonuje redukcji liniowej, dzięki obcięciu pojedynczej dekompozycji wartości (SVD). W przeciwieństwie do UPW, ten szacowania nie Wyśrodkowuje danych przed przetwarzaniem niepojedynczej dekompozycji wartości. Oznacza to, że może wydajnie współpracować z scipy. rozrzedzonych macierzy |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Każdy przykład (to oznacza, że każdy wiersz macierzy danych) z co najmniej jednym składnikiem niezerowym jest ponownie skalowany niezależnie od innych próbek, dzięki czemu jego norma (L1 lub L2) jest równa 1 |

### <a name="advanced-preprocessing-optional-featurization"></a>Zaawansowane przetwarzanie wstępne: opcjonalne cechowania

Dostępne są również dodatkowe zaawansowane procesy przetwarzania wstępnego i cechowania, takie jak brakujące wartości, które nie są przypisywaniem, kodowaniem i transformacjem. [Dowiedz się więcej na temat tego, co obejmuje cechowania](how-to-create-portal-experiments.md#preprocess). Włącz to ustawienie przy użyciu:

+ Azure Machine Learning Studio: wybierz opcję **Wyświetl ustawienia cechowania** w sekcji **uruchomienie konfiguracji** , [wykonując następujące kroki](how-to-create-portal-experiments.md).

+ Zestaw SDK języka Python: Określanie `"feauturization": auto' / 'off' / FeaturizationConfig` dla [klasy`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py).

## <a name="prevent-over-fitting"></a>Zapobiegaj nadmiernemu dopasowaniu

Nadmierne dopasowanie do uczenia maszynowego występuje, gdy model dopasowuje dane szkoleniowe, a w efekcie nie może precyzyjnie przewidzieć niewidocznych danych testowych. Innymi słowy, model ma po prostu Memorized określonych wzorców i szumów w danych szkoleniowych, ale nie jest wystarczająco elastyczny, aby wykonywać przewidywania dotyczące rzeczywistych danych. W większości przypadków egregious model z nadmiernym dopasowaniem zakłada, że kombinacje wartości funkcji widoczne podczas uczenia będą zawsze powodowały dokładne te same dane wyjściowe dla elementu docelowego. 

Najlepszym sposobem, aby uniknąć nadmiernego dopasowania, jest przestrzeganie najlepszych rozwiązań z zakresu, w tym:

* Użycie większej ilości danych szkoleniowych i wyeliminowanie statystycznej różnicy
* Zapobieganie wyciekowi docelowemu
* Korzystanie z mniej funkcji
* **Optymalizacja pod kątem i przeparametrem**
* **Ograniczenia złożoności modelu**
* **Wzajemne sprawdzanie poprawności**

W kontekście zautomatyzowanej ML, pierwsze trzy elementy powyżej są **implementacją najlepszych**rozwiązań. Ostatnie trzy elementy pogrubione to najlepsze w obsłużeniu **Automatyczne implementacje** , które domyślnie chronią przed nadmiernym dopasowaniem. W obszarze Ustawienia inne niż automatyczna ML warto uzyskać wszystkie sześć najlepszych rozwiązań, aby uniknąć nadmiernego dopasowania modeli.

### <a name="best-practices-you-implement"></a>Najlepsze rozwiązania zaimplementowane

Użycie **większej ilości danych** jest najprostszym i najlepszym sposobem, aby zapobiec nadmiernemu dopasowywaniu, a w miarę wzrostu zwiększy się dokładność. W przypadku korzystania z większej ilości danych stać się trudniejsze dla modelu, aby znają dokładne wzorce i wymusić osiągnięcie rozwiązań, które są bardziej elastyczne, aby pomieścić więcej warunków. Ważne jest również, aby rozpoznawać **odchylenie statystyczne**, aby upewnić się, że dane szkoleniowe nie obejmują izolowanych wzorców, które nie znajdują się w danych prognozowania na żywo. Ten scenariusz może być trudny do rozwiązania, ponieważ nie można nadmiernie zamontować między zestawem danych i zestawów testów, ale może istnieć nadmierne dopasowanie w porównaniu z danymi testu na żywo.

Docelowy wyciek jest podobnym problemem, gdzie nie można zobaczyć nadmiernego dopasowania między zestawami uczenia i testu, ale nie jest wyświetlany w czasie przewidywania. Docelowy wyciek występuje, gdy model "oszustwo" podczas uczenia się przez uzyskanie dostępu do danych, które nie powinny być zwykle w czasie przewidywania. Na przykład jeśli Twój problem jest przewidywany w poniedziałek, co cena asortymentu będzie w piątek, ale jedna z funkcji przypadkowo dodaliśmy dane z czwartki, które byłyby dane, model nie będzie miał w czasie przewidywania, ponieważ nie będzie widoczny w przyszłości. Docelowy wyciek jest łatwą pomyłką, ale często charakteryzuje się nietypowej dokładnością problemu. Jeśli próbujesz przewidzieć cenę giełdową i przeszkolony model z dokładnością do 95%, w swoich funkcjach występuje bardzo prawdopodobnie docelowy wyciek.

Usuwanie funkcji może być również pomocne w przypadku nadmiernego dopasowania, uniemożliwiając modelowi posiadanie zbyt wielu pól do użycia w celu znają określonych wzorców, co sprawia, że jest to bardziej elastyczne. Może być trudne zmierzenie ilości, ale jeśli można usunąć funkcje i zachować taką samą dokładność, prawdopodobnie model jest bardziej elastyczny i zmniejsza ryzyko nadmiernego dopasowania.

### <a name="best-practices-automated-ml-implements"></a>Implementacje zautomatyzowanych rozwiązań z najlepszymi rozwiązaniami

Uregulowanie to proces minimalizowania funkcji kosztu w celu karania złożonych i nadmiernie dopasowanych modeli. Istnieją różne rodzaje funkcji uregulowania, ale ogólnie rzecz biorąc, wszyscy mają kara rozmiar, wariancję i złożoność modelu. Zautomatyzowana ML używa L1 (Lasso), L2 (pierścieni) i ElasticNet (L1 i L2 jednocześnie) w różnych kombinacjach z różnymi ustawieniami parametrów modelu, które sterują nadmiernym dopasowywaniem. W prostych terminach, zautomatyzowany ML będzie różnicować, jak dużo modelu jest regulowany, i wybrać najlepszy wynik.

Zautomatyzowanej ML również implementuje jawne ograniczenia złożoności modelu, aby zapobiec nadmiernemu dopasowywaniu. W większości przypadków jest to przeznaczone tylko dla algorytmów drzewa decyzyjnego lub lasu, w których maksymalna głębokość dla poszczególnych drzew jest ograniczona, a łączna liczba drzew używanych w ramach technik lasu lub kompletu jest ograniczona.

Krzyżowe sprawdzanie poprawności (CV) to proces tworzenia wielu podzbiorów danych pełnego szkolenia i uczenia modelu w każdym podzestawie. Pomysłem jest to, że model może uzyskać "Cieszymy" i mieć dużą dokładność z jednym podzbiorem, ale przy użyciu wielu podzestawów model nie będzie osiągał tej dużej dokładności za każdym razem. Podczas wykonywania operacji CV należy dostarczyć zestaw danych wstrzymania weryfikacji, określić swoje zgięcia CV (liczba podzbiorów), a zautomatyzowany ML będzie szkolić model i dostrajał parametry w celu zminimalizowania błędu w zestawie walidacji. Jedno zgięcie CV może być nadmiernie dopasowane, ale przy użyciu wielu z nich zmniejsza prawdopodobieństwo, że ostateczny model jest nadmiernie dopasowany. Wadą jest to, że CV skutkuje dłuższym czasem uczenia się, a tym samym większym kosztem, ponieważ zamiast uczenia się modelu jeden raz, przeszkoluje go raz dla każdego podzestawu *n* CV.

> [!NOTE]
> Sprawdzanie krzyżowe nie jest domyślnie włączone; musi być skonfigurowany w ustawieniach zautomatyzowanej ML. Jednak po skonfigurowaniu CV i otrzymaniu zestawu danych sprawdzania poprawności proces jest zautomatyzowany.

### <a name="identifying-over-fitting"></a>Identyfikowanie nadmiernego dopasowania

Należy wziąć pod uwagę następujące przeszkolone modele i ich odpowiadające im uczenia i testy.

| Model | Dokładność szkolenia | Dokładność testu |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Biorąc pod uwagę model **A**, istnieje powszechna koncepcja, że jeśli dokładność testu dla niewidocznych danych jest mniejsza niż dokładność szkolenia, model jest nadmiernie dopasowany. Jednak dokładność testu powinna być zawsze mniejsza od dokładności szkolenia, a rozróżnienie dla nadmiernego dopasowania a odpowiednio dopasowane, przydaje się do *znacznie* mniejszej dokładności. 

**W przypadku** porównywania modeli a i **B**model **a** jest lepszym modelem, ponieważ ma wyższą dokładność testu, a chociaż dokładność testu jest nieco mniejsza o 95%, nie jest to znacząca różnica, która sugeruje nadmierne dopasowanie. Nie możesz wybrać modelu **B** po prostu, ponieważ ścisłe nauczenie i testy są bliżej siebie.

Model **C** reprezentuje wyraźny przypadek nadmiernego dopasowania; dokładność uczenia jest bardzo wysoka, ale dokładność testu nie jest w dowolnym miejscu w dużej okolicy. Takie rozróżnienie jest nieco subiektywne, ale pochodzi z wiedzy o problemie i danych oraz o tym, jakie wielkości błędów są akceptowalne. 

## <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych

Tworzenie prognoz jest integralną częścią dowolnej firmy, bez względu na to, czy chodzi o dochody, spisy, sprzedaż czy zapotrzebowanie na klienta. Możesz użyć zautomatyzowanej ML do łączenia technik i podejścia i uzyskania zalecanej wysokiej jakości prognozy szeregów czasowych.

Zautomatyzowany eksperyment szeregów czasowych jest traktowany jako problem z regresją wieloczynnikowa. Poprzednie wartości serii czasowych są "przestawne", aby stać się dodatkowymi wymiarami regresor wraz z innymi predykcyjnymi. Takie podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma zaletę naturalnie dołączania wielu zmiennych kontekstowych i ich relacji do siebie podczas uczenia się. Zautomatyzowana ML zdobywa pojedynczy, ale często wewnętrznie rozgałęzienie modelu dla wszystkich elementów w zestawie danych i prognozowanie Horizons. W tym celu można uzyskać więcej danych w celu oszacowania parametrów modelu i generalizacji do niewidocznej serii.

Dowiedz się więcej i zobacz przykład [automatycznej uczenia maszynowego na potrzeby prognozowania szeregów czasowych](how-to-auto-train-forecast.md). Lub zapoznaj się z [notesem zapotrzebowania na energię](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) , aby zapoznać się ze szczegółowymi przykładami zaawansowanej konfiguracji prognozowania, w tym:

* Wykrywanie świąt i cechowania
* DNN (autoARIMA, Prophet, ForecastTCN)
* Obsługa wielu modeli przy użyciu grupowania
* krzyżowe sprawdzanie poprawności źródła
* Konfigurowalne spowolnienia
* funkcje agregujące okna stopniowego

## <a name="ensemble"></a>Modele kompletów

Automatyczne Uczenie maszynowe obsługuje modele kompletów, które są domyślnie włączone. Program dblearning podnosi wyniki uczenia maszynowego i wydajność predykcyjną przez połączenie wielu modeli w przeciwieństwie do korzystania z pojedynczych modeli. Iteracje kompletka są wyświetlane jako ostateczne iteracje przebiegu. Automatyczne Uczenie maszynowe używa metody "głosowania" i "stosu" do łączenia modeli:

* **Głosowanie**: przewidywania w oparciu o średnią ważoną przewidywanych prawdopodobieństw zajęć (dla zadań klasyfikacji) lub przewidywane cele regresji (dla zadań regresji).
* **Stosowanie**: stosy łączy heterogenicznych modele i pociąga za siebie model na podstawie danych wyjściowych z poszczególnych modeli. Bieżące domyślne meta models to LogisticRegression dla zadań klasyfikacji i ElasticNet dla zadań regresji/prognozowania.

[Algorytm wyboru Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) DBZ posortowaną inicjalizacją kompletną służy do decydowania, które modele mają być używane w ramach tego modułu. Na wysokim poziomie ten algorytm inicjuje zespół z maksymalnie 5 modelami z najlepszymi wynikami i sprawdza, czy te modele znajdują się w przedziale od 5% do największej wartości, aby uniknąć słabego początkowego elementu. Następnie dla każdej iteracji, nowy model jest dodawany do istniejącej, a wynik jest obliczany. Jeśli nowy model poprawi istniejący wynik, jego kompletność zostanie zaktualizowana w celu uwzględnienia nowego modelu.

Zapoznaj się z [tematem jak](how-to-configure-auto-train.md#ensemble) zmienić domyślne ustawienia zestawu w usłudze automat Machine Learning.

## <a name="imbalance"></a>Dane niezrównoważone

Niezrównoważone dane często znajdują się w danych dla scenariuszy klasyfikacji uczenia maszynowego i odwołują się do danych, które zawierają nieproporcjonalny współczynnik obserwacji w każdej klasie. Takie niezrównoważone może prowadzić do fałszywego odczuwania pozytywnego skutku dokładności modelu, ponieważ dane wejściowe mają odchylenia względem jednej klasy, co skutkuje modelem szkolonym do naśladowania tej odchylenia. 

W ramach tego celu uproszczenie przepływu pracy usługi Machine Learning funkcja zautomatyzowanej wagi jest wbudowana w funkcje, które ułatwiają rozwiązywanie niezrównoważonych danych, takich jak, 

- **Kolumna wagi**: automatyczna ml obsługuje ważone kolumny jako dane wejściowe, co sprawia, że wiersze w danych są ważone w górę lub w dół, co może spowodować, że Klasa jest bardziej lub mniej "ważna". Zobacz ten [przykład notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/sample-weight/auto-ml-sample-weight.ipynb) 

- Algorytmy używane przez zautomatyzowaną ML mogą prawidłowo obsłużyć nierównowagi do 20:1, co oznacza, że najbardziej typowa Klasa może mieć 20 razy więcej wierszy w danych niż najmniejsza wspólna Klasa.

### <a name="identify-models-with-imbalanced-data"></a>Identyfikowanie modeli z niezrównoważonymi danymi

Ponieważ algorytmy klasyfikacji są często oceniane według dokładności, sprawdzanie dokładności modelu jest dobrym sposobem na ustalenie, czy miało to wpływ na dane niezrównoważone. Czy w przypadku niektórych klas jest naprawdę wysoka dokładność, czy naprawdę niska dokładność?

Dodatkowo automatyczne przebiegi w sieci automatycznie generują następujące wykresy, co może pomóc zrozumieć prawidłowość klasyfikacji modelu i zidentyfikować modele potencjalnie wpływać na dane niezrównoważone.

Wykres| Opis
---|---
[Zamieszanie macierzy](how-to-understand-automated-ml.md#confusion-matrix)| Oblicza prawidłowo sklasyfikowane etykiety pod kątem rzeczywistych etykiet danych. 
[Precyzja — odwoływanie](how-to-understand-automated-ml.md#precision-recall-chart)| Oblicza stosunek prawidłowych etykiet względem stosunku do znalezionych wystąpień etykiet danych 
[Krzywe ROC](how-to-understand-automated-ml.md#roc)| Oblicza stosunek poprawnych etykiet względem stosunku do etykiet z fałszywą liczbą dodatnią.

### <a name="handle-imbalanced-data"></a>Obsługa niezrównoważonych danych 

Następujące techniki są dodatkowymi opcjami obsługi niezrównoważonych danych poza zautomatyzowaną ML. 

- Ponowne próbkowanie do nawet nierównowagi klasy, przez zwiększenie próbkowania mniejszych klas lub próbkowanie w dół do większych klas. Te metody wymagają ekspertyzy do przetworzenia i analizowania.

- Użyj metryki wydajności, która zajmuje się lepszym użyciem niezrównoważonych danych. Na przykład, wynik F1 jest średnią ważoną dokładności i odwołania. Precyzja mierzy stopień dokładności klasyfikatora — niska precyzja wskazuje dużą liczbę fałszywych dodatnich--, podczas odwoływania miar klasyfikatora — niska funkcja odwoływania wskazuje dużą liczbę fałszywych wartości ujemnych. 

## <a name="use-with-onnx-in-c-apps"></a>Używanie z ONNX w C# aplikacjach

Za pomocą Azure Machine Learning można użyć zautomatyzowanej ML do skompilowania modelu języka Python i przekonwertowania go na format ONNX. Środowisko uruchomieniowe ONNX C#obsługuje, dzięki czemu można użyć modelu skompilowanego automatycznie w C# aplikacjach bez konieczności ponownego kodowania lub dowolnych opóźnień sieci, które wprowadzają punkty końcowe Rest. Wypróbuj przykład tego przepływu [w tym notesie Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatyczna ML w firmie Microsoft

Zautomatyzowana ML jest również dostępna w innych rozwiązaniach firmy Microsoft, takich jak:

|Integracje|Opis|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatyczny wybór modelu i szkolenia w aplikacjach .NET przy użyciu programu Visual Studio i Visual Studio Code ze ML.NET zautomatyzowanej ML (wersja zapoznawcza).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Równolegle Skaluj swoje zautomatyzowane zadania szkoleniowe dotyczące platformy Spark w klastrach usługi HDInsight.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Wywołaj modele uczenia maszynowego bezpośrednio w Power BI (wersja zapoznawcza).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Twórz nowe modele uczenia maszynowego za pośrednictwem danych w klastrach danych Big Data w SQL Server 2019.|

## <a name="next-steps"></a>Następne kroki

Zobacz przykłady i Dowiedz się, jak tworzyć modele przy użyciu automatycznego uczenia maszynowego:

+ Postępuj zgodnie z [samouczkiem: automatyczne uczenie modelu regresji przy użyciu automatycznego Machine Learning platformy Azure](tutorial-auto-train-models.md)

+ Skonfiguruj ustawienia dla eksperymentu automatycznego szkolenia:
  + W programie Azure Machine Learning Studio [wykonaj te kroki](how-to-create-portal-experiments.md).
  + Za pomocą zestawu SDK języka Python [wykonaj te kroki](how-to-configure-auto-train.md).

+ Dowiedz się, jak korzystać z funkcji autouczenia przy użyciu danych szeregów czasowych, [wykonując następujące kroki](how-to-auto-train-forecast.md).

+ Wypróbuj [Jupyter Notebook przykłady w celu automatycznego uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
