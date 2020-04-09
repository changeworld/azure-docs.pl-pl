---
title: Unikaj overfitting & nierówne dane z AutoML
titleSuffix: Azure Machine Learning
description: Identyfikowanie typowych pułapek modeli uczenia maszynowego i zarządzanie nimi za pomocą zautomatyzowanych rozwiązań uczenia maszynowego usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: 76f920ad6aae68defb567a7a6623d1ffd488af5f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874861"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Zapobieganie nadmiernemu dowieraniu i nierównemu wyniszczeniem danych dzięki zautomatyzowanemu uczeniu maszynowemu

Zbyt dopasowane i niezrównoważone dane są typowe pułapki podczas tworzenia modeli uczenia maszynowego. Domyślnie automatyczne uczenie maszynowe usługi Azure Machine Learning udostępnia wykresy i metryki ułatwiające identyfikowanie tych zagrożeń i implementuje najlepsze rozwiązania, aby je ograniczyć. 

## <a name="identify-over-fitting"></a>Identyfikowanie przepasanych

Nadmierne dopasowanie w uczeniu maszynowym występuje, gdy model zbyt dobrze pasuje do danych szkoleniowych i w rezultacie nie można dokładnie przewidzieć na niewidoczne dane testowe. Innymi słowy model po prostu zapamiętał określone wzorce i szum w danych szkoleniowych, ale nie jest wystarczająco elastyczny, aby przewidywać rzeczywiste dane.

Należy wziąć pod uwagę następujące przeszkolone modele i ich odpowiednie dokładności pociągów i testów.

| Model | Dokładność pociągu | Dokładność testu |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Biorąc pod uwagę model **A,** istnieje powszechne błędne przekonanie, że jeśli dokładność testu na danych niewidocznych jest niższa niż dokładność treningu, model jest nadmiernie zamontowany. Jednak dokładność testu powinna być zawsze mniejsza niż dokładność treningu, a rozróżnienie dla over-fit vs. odpowiednio pasuje sprowadza się do *tego, jak bardzo* mniej dokładne. 

Porównując modele **A** i **B,** model **A** jest lepszym modelem, ponieważ ma wyższą dokładność testu i chociaż dokładność testu jest nieco niższa i wynosi 95%, nie jest to znacząca różnica, która sugeruje, że występuje nadmierne dopasowanie. Nie wybrałbyś modelu **B** tylko dlatego, że dokładność pociągu i testu jest bliżej siebie.

Model **C** reprezentuje wyraźny przypadek przekładniania; dokładność treningu jest bardzo wysoka, ale dokładność testu nie jest nigdzie w pobliżu tak wysoka. To rozróżnienie jest subiektywne, ale pochodzi z wiedzy o problemie i danych oraz o tym, jakie rozmiary błędów są dopuszczalne.

## <a name="prevent-over-fitting"></a>Zapobieganie nadmiernemu dopasowaniu

W najbardziej rażących przypadkach model nadmiernie dopasowany zakłada, że kombinacje wartości funkcji widoczne podczas szkolenia zawsze będą skutkować dokładnie tym samym wynikiem wyjściowym dla obiektu docelowego.

Najlepszym sposobem zapobiegania nadmiernemu dopasowaniu jest przestrzeganie najlepszych praktyk w zakresie uczenia się, w tym:

* Wykorzystanie większej ilości danych szkoleniowych i wyeliminowanie stronniczości statystycznej
* Zapobieganie wyciekom celu
* Korzystanie z mniejszej liczby funkcji
* **Legalizacja i optymalizacja hiperparametryczne**
* **Ograniczenia złożoności modelu**
* **Krzyżowe sprawdzanie poprawności**

W kontekście zautomatyzowanego uczenia maszynowego pierwsze trzy powyższe elementy to **najlepsze rozwiązania, które implementujesz**. Ostatnie trzy elementy pogrubione są **najlepsze rozwiązania zautomatyzowane ml implementuje** domyślnie w celu ochrony przed nadmiernym dopasowaniem. W ustawieniach innych niż zautomatyzowane ML, wszystkie sześć najlepszych praktyk są warte naśladowania, aby uniknąć przekładnia modeli.

### <a name="best-practices-you-implement"></a>Najlepsze rozwiązania, które implementujesz

Korzystanie z **większej ilości danych** jest najprostszym i najlepszym możliwym sposobem zapobiegania nadmiernemu dopasowaniu, a jako dodatkowy bonus zazwyczaj zwiększa dokładność. Gdy używasz więcej danych, staje się trudniejsze dla modelu do zapamiętania dokładnych wzorców i jest zmuszony do osiągnięcia rozwiązań, które są bardziej elastyczne, aby pomieścić więcej warunków. Ważne jest również, aby rozpoznać **błąd statystyczny,** aby upewnić się, że dane szkoleniowe nie zawierają izolowanych wzorców, które nie będą istnieć w danych przewidywania na żywo. Ten scenariusz może być trudne do rozwiązania, ponieważ nie może być zbyt dopasowane między pociągiem i zestawów testowych, ale może być zbyt dopasowane obecne w porównaniu do danych testowych na żywo.

Wyciek docelowy jest podobny problem, gdzie może nie zobaczyć zbyt montażu między pociąg / zestawy testowe, ale raczej pojawia się w czasie przewidywania. Wyciek docelowy występuje, gdy model "oszukuje" podczas szkolenia, mając dostęp do danych, które zwykle nie powinny mieć w czasie przewidywania. Na przykład, jeśli problem ma przewidywać w poniedziałek, jaka będzie cena towaru w piątek, ale jedna z twoich funkcji przypadkowo zawierała dane z czwartkych, będą to dane, których model nie będzie miał w czasie przewidywania, ponieważ nie będzie widoczny w przyszłości. Wyciek celu jest łatwym błędem do pominięcia, ale często charakteryzuje się nienormalnie wysoką dokładnością problemu. Jeśli próbujesz przewidzieć cenę akcji i przeszkoliłeś model z dokładnością 95%, prawdopodobnie gdzieś w twoich funkcjach pojawi się docelowy wyciek.

Usuwanie operacji może również pomóc w nadmiernym dopasowaniu, uniemożliwiając modelowi posiadanie zbyt wielu pól do zapamiętania określonych wzorców, co powoduje, że jest bardziej elastyczny. Pomiar ilościowy może być trudny, ale jeśli można usunąć funkcje i zachować tę samą dokładność, prawdopodobnie uczyniłeś model bardziej elastycznym i zmniejszyłeś ryzyko nadmiernego dopasowania.

### <a name="best-practices-automated-ml-implements"></a>Najlepsze praktyki zautomatyzowanych implementów ml

Uregulowanie to proces minimalizacji funkcji kosztowej w celu ukarania złożonych i nadmiernie dopasowanych modeli. Istnieją różne typy funkcji regularyzacji, ale ogólnie wszystkie one karzą rozmiar współczynnika modelu, wariancję i złożoność. Automated ML używa L1 (Lasso), L2 (Ridge) i ElasticNet (L1 i L2 jednocześnie) w różnych kombinacjach z różnymi ustawieniami hiperparametru modelu, które kontrolują nadmierne dopasowanie. W prostych słowach, zautomatyzowane ML będzie się różnić, jak bardzo model jest regulowany i wybrać najlepszy wynik.

Automated ML implementuje również jawne ograniczenia złożoności modelu, aby zapobiec nadmiernemu dopasowaniu. W większości przypadków ta implementacja jest specjalnie dla algorytmów drzewa decyzyjnego lub lasu, gdzie poszczególne drzewa max-głębokość jest ograniczona, a całkowita liczba drzew używanych w lasu lub technik zespołu są ograniczone.

Krzyżowa walidacja (CV) to proces przyjmowania wielu podzbiorów pełnych danych szkoleniowych i szkolenia modelu w każdym podzbiorze. Chodzi o to, że model może uzyskać "szczęście" i mieć dużą dokładność z jednym podzbiorem, ale przy użyciu wielu podzbiorów model nie osiągnie tej wysokiej dokładności za każdym razem. Podczas wykonywania CV, należy podać zestaw danych wstrzymania sprawdzania poprawności, określić swoje fałdy CV (liczba podzbiorów) i automatyczne ML będzie szkolić modelu i dostroić hiperparametry, aby zminimalizować błąd w zestawie sprawdzania poprawności. Jeden krotnie CV może być zbyt dopasowany, ale za pomocą wielu z nich zmniejsza prawdopodobieństwo, że twój ostateczny model jest zbyt dopasowany. Kompromisem jest to, że CV powoduje dłuższy czas szkolenia, a tym samym większe koszty, ponieważ zamiast trenować model raz, trenujesz go raz dla każdego podzbiorów *n* CV. 

> [!NOTE]
> Sprawdzanie poprawności krzyżowej nie jest domyślnie włączone; musi być skonfigurowany w zautomatyzowanych ustawieniach uczenia maszynowego. Jednak po skonfigurowaniu sprawdzania poprawności krzyżowej i udostępnieniu zestawu danych sprawdzania poprawności proces jest zautomatyzowany. Zobacz 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identyfikowanie modeli z niezrównoważonymi danymi

Niezrównoważone dane są często spotykane w danych dla scenariuszy klasyfikacji uczenia maszynowego i odnoszą się do danych, które zawierają nieproporcjonalny stosunek obserwacji w każdej klasie. Ta nierównowaga może prowadzić do fałszywie postrzeganego pozytywnego wpływu dokładności modelu, ponieważ dane wejściowe mają współczynnik uprzedzeń w kierunku jednej klasy, co powoduje, że przeszkolony model naśladuje tę stronniczość. 

Ponieważ algorytmy klasyfikacji są często oceniane przez dokładność, sprawdzanie wyniku dokładności modelu jest dobrym sposobem na określenie, czy wpływ na niego miały niezrównoważone dane. Czy dla niektórych klas miał naprawdę wysoką dokładność lub naprawdę niską dokładność?

Ponadto automatyczne przebiegi uczenia maszynowego automatycznie generują następujące wykresy, co może pomóc w zrozumieniu poprawności klasyfikacji modelu i identyfikowaniu modeli, na które mogą mieć wpływ nierówne dane.

Wykres| Opis
---|---
[Matryca zamieszania](how-to-understand-automated-ml.md#confusion-matrix)| Ocenia poprawnie sklasyfikowane etykiety na tle rzeczywistych etykiet danych. 
[Precyzyjne przywoływanie](how-to-understand-automated-ml.md#precision-recall-chart)| Ocenia stosunek prawidłowych etykiet w stosunku do stosunku znalezionych wystąpień etykiet danych 
[Krzywe ROC](how-to-understand-automated-ml.md#roc)| Ocenia stosunek prawidłowych etykiet względem stosunku etykiet fałszywie dodatnich.

## <a name="handle-imbalanced-data"></a>Obsługa danych niezniechówionych 

W ramach celu uproszczenia przepływu pracy uczenia maszynowego, zautomatyzowane ml ma wbudowane funkcje, aby pomóc radzić sobie z niezrównoważonych danych, takich jak, 

- **Kolumna wagi:** automatyczna kolumna ML obsługuje kolumnę ważoną jako dane wejściowe, powodując, że wiersze w danych mają być ważone w górę lub w dół, co może sprawić, że klasa będzie mniej lub bardziej "ważna".

- Algorytmy używane przez zautomatyzowane ML można poprawnie obsługiwać nierównowagi do 20:1, co oznacza, że najczęściej klasy może mieć 20 razy więcej wierszy w danych niż najmniej wspólnej klasy.

Następujące techniki są dodatkowe opcje do obsługi danych nierówne poza zautomatyzowanych ML. 

- Ponowne próbkowanie do nawet nierównowagi klasy, albo przez up-sampling mniejszych klas lub w dół próbkowania większych klas. Metody te wymagają wiedzy specjalistycznej do przetwarzania i analizowania.

- Użyj metryki wydajności, która lepiej radzi sobie z nieeksmetrycznymi danymi. Na przykład wynik F1 jest średnią ważoną precyzji i odwołania. Precyzja mierzy dokładność klasyfikatora - niska precyzja wskazuje na dużą liczbę fałszywych alarmów--, podczas gdy przywoływanie mierzy kompletność klasyfikatora - niskie przywoływanie wskazuje na dużą liczbę fałszywych negatywów. 

## <a name="next-steps"></a>Następne kroki

Zobacz przykłady i dowiedz się, jak tworzyć modele przy użyciu zautomatyzowanego uczenia maszynowego:

+ Postępuj zgodnie [z samouczkiem: Automatyczne szkolenie modelu regresji za pomocą usługi Azure Machine Learning](tutorial-auto-train-models.md)

+ Skonfiguruj ustawienia eksperymentu automatycznego szkolenia:
  + W studiu usługi Azure Machine Learning [wykonaj następujące kroki.](how-to-use-automated-ml-for-ml-models.md)
  + W przypadku modułu SDK języka Python [wykonaj następujące kroki](how-to-configure-auto-train.md).


