---
title: Analizowanie zmian od klientów
titleSuffix: ML Studio (classic) - Azure
description: Studium przypadku tworzenia zintegrowanego modelu do analizowania i oceniania zmian klientów przy użyciu usługi Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204532"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analizowanie rezygnacji klientów przy użyciu usługi Azure Machine Learning Studio (klasyczne)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Omówienie
W tym artykule przedstawiono implementację odwołania projektu analizy zmian klientów, który jest zbudowany przy użyciu usługi Azure Machine Learning Studio (klasyczny). W tym artykule omówimy skojarzone modele ogólne do całościowego rozwiązywania problemu churn klientów przemysłowych. Mierzymy również dokładność modeli, które są tworzone przy użyciu uczenia maszynowego i oceniamy kierunki dalszego rozwoju.  

### <a name="acknowledgements"></a>Podziękowania
Ten eksperyment został opracowany i przetestowany przez Serge Berger, główny analityk danych w firmie Microsoft, i Roger Barga, dawniej Product Manager dla Microsoft Azure Machine Learning Studio (klasyczny). Zespół dokumentacji platformy Azure z wdzięcznością potwierdza ich wiedzę i dziękuje im za udostępnienie tego dokumentu.

> [!NOTE]
> Dane używane w tym eksperymencie nie są publicznie dostępne. Na przykład jak utworzyć model uczenia maszynowego do analizy zmian, zobacz: [Szablon modelu rezygnacji sieci sprzedaży](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) w [galerii sztucznej inteligencji platformy Azure](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problem rezygnacji klientów
Przedsiębiorstwa na rynku konsumenckim i we wszystkich sektorach przedsiębiorstw muszą radzić sobie z odpływem. Czasami churn jest nadmierne i wpływa na decyzje polityczne. Tradycyjnym rozwiązaniem jest przewidywanie churners o wysokiej skłonności i adres ich potrzeb za pośrednictwem usługi concierge, kampanii marketingowych, lub poprzez zastosowanie specjalnych zwolnień. Podejścia te mogą się różnić w zależności od przemysłu. Mogą one nawet różnić się w zależności od klastra konsumenckiego w jednej branży (na przykład telekomunikacji).

Częstym czynnikiem jest to, że firmy muszą zminimalizować te specjalne wysiłki retencji klientów. Tak więc, naturalną metodologią byłoby ocena każdego klienta z prawdopodobieństwem churn i adres top N te. Najlepsi klienci mogą być najbardziej dochodowych. Na przykład w bardziej zaawansowanych scenariuszach funkcja zysku jest stosowana podczas wyboru kandydatów do specjalnej dyspensacji. Jednak te rozważania są tylko częścią pełnej strategii radzenia sobie z churn. Przedsiębiorstwa muszą również wziąć pod uwagę ryzyko (i związaną z tym tolerancję ryzyka), poziom i koszt interwencji oraz prawdopodobną segmentację klientów.  

## <a name="industry-outlook-and-approaches"></a>Perspektywy i podejścia branżowe
Wyrafinowana obsługa churn jest oznaką dojrzałej branży. Klasycznym przykładem jest branża telekomunikacyjna, w której wiadomo, że abonenci często przełączają się z jednego dostawcy do drugiego. Ta dobrowolna rezygnacja jest głównym problemem. Co więcej, dostawcy zgromadzili znaczną wiedzę na temat *sterowników odpływu,* które są czynnikami, które napędzają klientów do przełączania.

Na przykład, wybór słuchawki lub urządzenia jest dobrze znany kierowca churn w branży telefonii komórkowej. W rezultacie popularną polityką jest dotowanie ceny słuchawki dla nowych abonentów i naliczanie pełnej ceny istniejącym klientom za upgrade. Historycznie, ta polityka doprowadziła do klientów hopping z jednego dostawcy do drugiego, aby uzyskać nową zniżkę. To z kolei skłoniło dostawców do udoskonalenia swoich strategii.

Wysoka zmienność w ofertach słuchawki jest czynnikiem, który szybko unieważnia modele zmian, które są oparte na bieżących modelach słuchawki. Dodatkowo, telefony komórkowe są nie tylko urządzenia telekomunikacyjne, są one również oświadczenia mody (należy wziąć pod uwagę iPhone). Te społeczne predyktory są poza zakresem regularnych zestawów danych telekomunikacyjnych.

Wynik netto dla modelowania jest to, że nie można opracować zasady dźwięku po prostu eliminując znane przyczyny churn. W rzeczywistości **obowiązkowa**jest strategia ciągłego modelowania, w tym klasyczne modele, które kwantyfikują zmienne kategorii (takie jak drzewa decyzyjne).

Korzystając z dużych zbiorów danych na swoich klientach, organizacje wykonują analizę dużych zbiorów danych (w szczególności wykrywanie zmian w oparciu o duże zbiory danych) jako skuteczne podejście do problemu. Więcej informacji na temat podejścia do dużych zbiorów danych w kwestii zmian w zakresie zmian w dziale Zalecenia dotyczące ETL można znaleźć.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia modelowania zmian klientów
Wspólny proces rozwiązywania problemów w celu rozwiązania problemu z rezygnacją klienta przedstawiono na rysunkach 1-3:  

1. Model ryzyka pozwala rozważyć, jak działania wpływają na prawdopodobieństwo i ryzyko.
2. Model interwencji pozwala rozważyć, w jaki sposób poziom interwencji może wpłynąć na prawdopodobieństwo zmian i kwotę wartości okresu istnienia klienta (CLV).
3. Analiza ta nadaje się do analizy jakościowej, która jest eskalowana do proaktywnej kampanii marketingowej, która jest skierowana do segmentów klientów, aby dostarczyć optymalną ofertę.  

![Diagram pokazujący, w jaki sposób tolerancja ryzyka i modele decyzji dają zasysanych informacji](./media/azure-ml-customer-churn-scenario/churn-1.png)

To podejście wybiegające w przyszłość jest najlepszym sposobem leczenia churn, ale pochodzi ze złożoności: musimy opracować archetyp wieloszydowy i śledzenia zależności między modelami. Interakcja między modelami może być hermetyzowana, jak pokazano na poniższym diagramie:  

![Diagram interakcji modelu zmian](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Rysunek 4: Ujednolicony archetyp wieloskładnikowy*  

Interakcja między modelami ma kluczowe znaczenie, jeśli mamy zapewnić holistyczne podejście do utrzymania klientów. Każdy model koniecznie ulega degradacji w czasie; w związku z tym architektura jest niejawną pętlą (podobną do archetypu ustawionego przez standard wyszukiwania danych CRISP-DM, [***3***]).  

Ogólny cykl segmentacji/rozkładu w zakresie podejmowania decyzji o ryzyku jest nadal uogólnioną strukturą, która ma zastosowanie do wielu problemów biznesowych. Analiza churn jest po prostu silnym przedstawicielem tej grupy problemów, ponieważ wykazuje wszystkie cechy złożonego problemu biznesowego, który nie pozwala na uproszczone rozwiązanie predykcyjne. Społeczne aspekty nowoczesnego podejścia do churn nie są szczególnie podkreślone w podejściu, ale aspekty społeczne są zamknięte w archetypie modelowania, jak byłoby to w każdym modelu.  

Ciekawym dodatkiem jest tutaj analiza dużych zbiorów danych. Dzisiejsze firmy telekomunikacyjne i detaliczne zbierają wyczerpujące dane o swoich klientach i możemy łatwo przewidzieć, że potrzeba wielowymiarowej łączności stanie się powszechnym trendem, biorąc pod uwagę pojawiające się trendy, takie jak Internet rzeczy i wszechobecne urządzeń, które umożliwiają firmom stosowanie inteligentnych rozwiązań na wielu warstwach.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementowanie archetypu modelowania w udiociepiarskim (klasyczny)
Biorąc pod uwagę opisany problem, jaki jest najlepszy sposób na wdrożenie zintegrowanego modelowania i oceniania? W tej sekcji zademonstrujemy, jak udało nam się to osiągnąć przy użyciu usługi Azure Machine Learning Studio (classic).  

Podejście wielo modelowe jest koniecznością podczas projektowania globalnego archetypu dla zmian. Nawet punktacji (predykcyjne) część podejścia powinny być wielokrzesiciele.  

Na poniższym diagramie przedstawiono prototyp, który utworzyliśmy, który wykorzystuje cztery algorytmy oceniania w udiociepła Machine Learning (classic) do przewidywania zmian. Powodem użycia podejścia wielo modelowego jest nie tylko utworzenie klasyfikatora zespołu w celu zwiększenia dokładności, ale także ochrona przed nadmiernym dopasowaniem i poprawa wyboru funkcji nakazowych.  

![Zrzut ekranu przedstawiający złożony obszar roboczy Studio (klasyczny) z wieloma połączonymi modułami](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Rysunek 5: Prototyp podejścia do modelowania churn*  

Poniższe sekcje zawierają więcej szczegółów na temat prototypu oceniania modelu, który zaimplementowaliśmy przy użyciu usługi Machine Learning Studio (klasyczny).  

### <a name="data-selection-and-preparation"></a>Wybór i przygotowanie danych
Dane używane do tworzenia modeli i ocena klientów uzyskano z rozwiązania pionowego CRM, z danymi zaciemnione w celu ochrony prywatności klientów. Dane zawierają informacje o 8000 subskrypcji w Stanach Zjednoczonych i łączy trzy źródła: inicjowanie obsługi administracyjnej danych (metadane subskrypcji), dane aktywności (użycie systemu) i dane obsługi klienta. Dane nie zawierają żadnych informacji o klientach związanych z działalnością gospodarczą; na przykład nie zawiera metadanych lojalności ani punktów kredytowych.  

Dla uproszczenia etl i procesy oczyszczania danych są poza zakresem, ponieważ zakładamy, że przygotowanie danych zostało już wykonane gdzie indziej.

Wybór funkcji do modelowania opiera się na wstępnej punktacji istotności zestawu predyktorów, zawarte w procesie, który używa modułu lasu losowego. Dla implementacji w udiod Machine Learning Studio (klasyczny) obliczyliśmy średnią, medianę i zakresy dla reprezentatywnych funkcji. Na przykład dodaliśmy agregaty dla danych jakościowych, takich jak minimalne i maksymalne wartości dla aktywności użytkownika.

Uchwyciliśmy również informacje czasowe za ostatnie sześć miesięcy. Przeanalizowaliśmy dane przez jeden rok i ustaliliśmy, że nawet jeśli istnieją statystycznie istotne tendencje, wpływ na churn jest znacznie zmniejszony po sześciu miesiącach.  

Najważniejszą kwestią jest to, że cały proces, w tym ETL, wybór funkcji i modelowanie został zaimplementowany w usłudze Machine Learning Studio (klasyczny), przy użyciu źródeł danych na platformie Microsoft Azure.   

Na poniższych diagramach przedstawiono dane, które zostały użyte.  

![Zrzut ekranu przedstawiający próbkę danych używanych z wartościami nieprzetworzonymi](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Rysunek 6: Fragment źródła danych (zaciemniony)*  

![Zrzut ekranu przedstawiający funkcje statystyczne wyodrębnione ze źródła danych](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Rysunek 7: Funkcje wyodrębnione ze źródła danych*
 

> Należy zauważyć, że te dane są prywatne i dlatego nie można udostępnić modelu i danych.
> Jednak dla podobnego modelu przy użyciu publicznie dostępnych danych, zobacz ten przykładowy eksperyment w [Galerii sztucznej inteligencji azure:](https://gallery.azure.ai/) [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Aby dowiedzieć się więcej o tym, jak można zaimplementować model analizy zmian za pomocą pakietu Cortana Intelligence Suite, zalecamy również [ten film przez](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) Starszego Menedżera programu Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algorytmy stosowane w prototypie
Do utworzenia prototypu użyliśmy następujących czterech algorytmów uczenia maszynowego (bez dostosowywania):  

1. Regresja logistyczna (LR)
2. Wzmocnione drzewo decyzyjne (BT)
3. Uśredniony perceptron (AP)
4. Wektor wektorowy wsparcia (SVM)  

Na poniższym diagramie przedstawiono część powierzchni projektowej eksperymentu, która wskazuje kolejność, w której modele zostały utworzone:  

![Zrzut ekranu przedstawiający małą część płótna eksperymentu studyjnego](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Rysunek 8: Tworzenie modeli w udiociepli (klasyczny)*  

### <a name="scoring-methods"></a>Metody oceniania
Zdobyliśmy cztery modele przy użyciu zestawu danych szkoleniowych oznaczonych etykietą.  

Przesłaliśmy również zestaw danych punktacji do porównywalnego modelu zbudowanego przy użyciu klasycznej wersji SAS Enterprise Miner 12. Zmierzyliśmy dokładność modelu SAS i wszystkich czterech modeli Machine Learning Studio (klasycznych).  

## <a name="results"></a>Wyniki
W tej sekcji przedstawiamy nasze ustalenia dotyczące dokładności modeli na podstawie zestawu danych oceniania.  

### <a name="accuracy-and-precision-of-scoring"></a>Dokładność i precyzja punktacji
Ogólnie rzecz biorąc implementacja w usłudze Azure Machine Learning Studio (klasyczny) jest za sygnatury dostępu Współdzielonego w dokładności o około 10-15% (Obszar w krzywej lub AUC).  

Jednak najważniejszą metryką w churn jest wskaźnik błędnej klasyfikacji: to jest z góry churners N, zgodnie z przewidywaniami klasyfikatora, który z nich faktycznie **nie** churn, a jednak otrzymał specjalne traktowanie? Na poniższym diagramie porównano ten wskaźnik błędnej klasyfikacji dla wszystkich modeli:  

![Obszar pod wykresem krzywej porównujący wydajność 4 algorytmów](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Rysunek 9: Obszar prototypu Passau pod krzywą*

### <a name="using-auc-to-compare-results"></a>Używanie AUC do porównywania wyników
Obszar pod krzywą (AUC) jest metryką reprezentującą globalną miarę *rozdzielności* między rozkładami wyników dla populacji dodatnich i ujemnych. Jest podobny do tradycyjnego wykresu roc (Receiver Operator Characteristic), ale jedną ważną różnicą jest to, że metryka AUC nie wymaga wyboru wartości progowej. Zamiast tego podsumowuje wyniki na **wszystkich** możliwych wyborów. Natomiast tradycyjny wykres ROC pokazuje dodatnią szybkość na osi pionowej i współczynnik fałszywie dodatni na osi poziomej, a próg klasyfikacji jest różny.   

AUC jest używany jako miara wartości dla różnych algorytmów (lub różnych systemów), ponieważ umożliwia modelom porównywanie za pomocą ich wartości AUC. Jest to popularne podejście w branżach takich jak meteorologia i nauki biologiczne. W związku z tym AUC stanowi popularne narzędzie do oceny wydajności klasyfikatora.  

### <a name="comparing-misclassification-rates"></a>Porównywanie wskaźników błędnej klasyfikacji
Porównaliśmy wskaźniki błędnej klasyfikacji danego zestawu danych przy użyciu danych CRM około 8000 subskrypcji.  

* Wskaźnik błędnej klasyfikacji SAS wynosił 10-15%.
* Machine Learning Studio (klasyczny) wskaźnik błędnej klasyfikacji był 15-20% dla najlepszych 200-300 churners.  

W branży telekomunikacyjnej ważne jest, aby zwracać się tylko do tych klientów, którzy są najbardziej narażeni na churn, oferując im usługi concierge lub inne specjalne traktowanie. W związku z tym implementacja programu Machine Learning Studio (klasyczna) osiąga wyniki na równi z modelem SAS.  

Z tego samego powodu dokładność jest ważniejsza niż precyzja, ponieważ interesuje nas głównie prawidłowa klasyfikacja potencjalnych churnerów.  

Poniższy diagram z Wikipedii przedstawia relację w żywej, łatwej do zrozumienia grafice:  

![Dwa cele. Jeden cel pokazuje znaki trafień luźno zgrupowane, ale w pobliżu byków-eye oznaczone "Niska dokładność: dobra prawda, słaba precyzja. Kolejny cel mocno zgrupowane, ale daleko od byków oko oznaczone "Niska dokładność: słaba prawda, dobra precyzja"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Rysunek 10: Kompromis między dokładnością a precyzją*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Dokładność i precyzja wyników dla wzmocnionego modelu drzewa decyzyjnego
Na poniższym wykresie są wyświetlane nieprzetworzone wyniki z oceniania przy użyciu prototypu usługi Machine Learning dla promowanego modelu drzewa decyzyjnego, który jest najdokładniejszy spośród czterech modeli:  

![Fragment tabeli przedstawiający dokładność, precyzję, odwołanie, wynik F, wynik AUC, średnią utratę dziennika i utratę dziennika szkoleniowego dla czterech algorytmów](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Rysunek 11: Wzmocnione charakterystyki modelu drzewa decyzyjnego*

## <a name="performance-comparison"></a>Porównanie wydajności
Porównaliśmy szybkość, z jaką dane zostały ocenione przy użyciu modeli Machine Learning Studio (klasycznych) i porównywalnego modelu utworzonego przy użyciu klasycznej edycji SAS Enterprise Miner 12.1.  

W poniższej tabeli podsumowano wydajność algorytmów:  

*Tabela 1. Ogólna wydajność (dokładność) algorytmów*

| LR | BT | Interfejs API | Svm |
| --- | --- | --- | --- |
| Średni model |Najlepszy model |Gorsze wyniki |Średni model |

Modele hostowane w Machine Learning Studio (klasyczny) wyprzedziły SAS o 15-25% pod względem szybkości wykonania, ale dokładność była w dużej mierze na równi.  

## <a name="discussion-and-recommendations"></a>Dyskusja i zalecenia
W branży telekomunikacyjnej pojawiło się kilka praktyk do analizy churn, w tym:  

* Oblicz metryki dla czterech podstawowych kategorii:
  * **Encja (na przykład subskrypcja)**. Aprowizuj podstawowe informacje o subskrypcji i/lub kliencie, który jest przedmiotem zmian.
  * **Aktywność**. Uzyskaj wszystkie informacje o użyciu, które są związane z jednostką, na przykład liczbę loginów.
  * **Obsługa klienta**. Zbieraj informacje z dzienników obsługi klienta, aby wskazać, czy subskrypcja miała problemy lub interakcje z obsługą klienta.
  * **Dane konkurencyjne i biznesowe**. Uzyskaj wszelkie możliwe informacje o kliencie (na przykład mogą być niedostępne lub trudne do śledzenia).
* Użyj znaczenia, aby zwiększyć wybór funkcji. Oznacza to, że model drzewa decyzyjnego zwiększane jest zawsze obiecujące podejście.  

Zastosowanie tych czterech kategorii stwarza iluzję, że proste podejście *deterministyczne,* oparte na indeksach utworzonych na rozsądnych czynnikach dla kategorii, powinno wystarczyć do zidentyfikowania klientów zagrożonych zmianą. Niestety, chociaż pojęcie to wydaje się wiarygodne, jest to fałszywe zrozumienie. Powodem jest to, że churn jest efekt czasowy i czynniki przyczyniające się do churn są zwykle w stanach przejściowych. To, co skłania klienta do rozważenia odejścia dzisiaj, może być inne jutro, a na pewno będzie inne sześć miesięcy od teraz. Dlatego model *probabilistyczny* jest koniecznością.  

Ta ważna obserwacja jest często pomijana w biznesie, który zazwyczaj preferuje podejście do analityki zorientowane na analizę biznesową, głównie dlatego, że łatwiej jest sprzedawać i przyznaje się do prostej automatyzacji.  

Jednak obietnica samoobsługowej analizy przy użyciu usługi Machine Learning Studio (classic) jest to, że cztery kategorie informacji, klasyfikowane według działu lub działu, stają się cennym źródłem uczenia maszynowego na temat zmian.  

Inną ekscytującą możliwością w usłudze Azure Machine Learning Studio (klasyczna) jest możliwość dodania niestandardowego modułu do repozytorium wstępnie zdefiniowanych modułów, które są już dostępne. Ta funkcja zasadniczo stwarza możliwość wyboru bibliotek i tworzenia szablonów dla rynków pionowych. Jest ważnym wyróżnikiem usługi Azure Machine Learning Studio (klasyczny) na rynku.  

Mamy nadzieję kontynuować ten temat w przyszłości, szczególnie związany z analizą dużych zbiorów danych.
  

## <a name="conclusion"></a>Podsumowanie
W tym dokumencie opisano rozsądne podejście do rozwiązywania wspólnego problemu rezygnacji klientów przy użyciu ogólnych ram. Rozważyliśmy prototyp do oceniania modeli i zaimplementowaliśmy go przy użyciu usługi Azure Machine Learning Studio (classic). Na koniec oceniliśmy dokładność i wydajność prototypowego rozwiązania w odniesieniu do porównywalnych algorytmów w SAS.  

 

## <a name="references"></a>Dokumentacja
[1] Analiza predykcyjna: Beyond the Predictions, W. McKnight, Information Management, lipiec/sierpień 2011, s.18-20.  

[2] Artykuł w Wikipedii: [Dokładność i precyzja](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Przewodnik po eksploracji danych krok po kroku](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: bardziej efektywne angażowanie klientów i zwiększenie wartości](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Szablon modelu rezygnacji telco](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) w [Galerii sztucznej inteligencji platformy Azure](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Dodatek
![Migawka prezentacji na temat prototypu churn](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Rysunek 12: Migawka prezentacji na temat prototypu churn*
