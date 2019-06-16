---
title: Analizowanie zmienności klientów
titleSuffix: Azure Machine Learning Studio
description: Analiza przypadku związanych z tworzeniem zintegrowane modelu do analizowania i oceniania zmienności klientów za pomocą usługi Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: e6a7eaa94e7196c830a66b2d77023bd562119c92
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699439"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio"></a>Analizowanie zmienności klientów za pomocą usługi Azure Machine Learning Studio
## <a name="overview"></a>Omówienie
Ten artykuł przedstawia informacje o referencyjnej implementacji projektu analizy zmienności klientów, która powstała przy użyciu usługi Azure Machine Learning Studio. W tym artykule omówiono powiązanych modelach ogólny na potrzeby całościowe rozwiązanie problemu odpływu klientów przemysłowe. Możemy również mierzenia dokładności modeli, które są tworzone przy użyciu uczenia maszynowego i ocenić używany na potrzeby dalszego rozwoju.  

### <a name="acknowledgements"></a>Potwierdzanie
Ten eksperyment został opracowany i przetestowane przez Serge Berger, badacz danych jednostki w firmie Microsoft i Rogera Barga, wcześniej menedżerem produktu Microsoft Azure Machine Learning Studio. Zespół dokumentacji platformy Azure gratefully potwierdza swoją wiedzę i Dziękujemy udostępnianie ten oficjalny dokument.

> [!NOTE]
> Dane używane w tym eksperymencie nie jest publicznie dostępna. Aby uzyskać przykład sposobu tworzenia modelu uczenia maszynowego, analizy zmienności zobacz: [Postęp dokonany w sprzedaży detalicznej szablon modelu](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) w [Galeria sztucznej Inteligencji platformy Azure](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problem zmienności klientów
Firmom na rynku odbiorców i sektora przedsiębiorstw bez konieczności poświęcania czasu współczynnika zmian. Czasami zmian jest nadmierne i ma wpływ na decyzje dotyczące zasad. Tradycyjne rozwiązanie jest przewidzieć churners tendencje wysokiej i ich potrzeb za pośrednictwem usługi Konsjerż, kampanii marketingowych lub stosując zwolnień specjalne. Te metody mogą się różnić z branży branży. One może nawet różnią się od klastra danego konsumenta do drugiej w ramach jednej branży (na przykład telekomunikacja).

Typowe współczynnik jest firm należy zminimalizować starania związane z przechowywaniem tych specjalnych klienta. W związku z tym naturalnych metodologii będzie ocena każdy klient korzystający z prawdopodobieństwo, że współczynnik zmian i N pierwszych tych adresów. Najważniejszych klientów może być najbardziej dochodowe z nich. Na przykład w bardziej zaawansowanych scenariuszach funkcja zysk są stosowane podczas wybór kandydatów specjalne zwolnienie. Jednak te zagadnienia są tylko część strategii związanych ze zmian. Firmy muszą uwzględniać konto o podwyższonym ryzyku i tolerancji ryzyka skojarzony, poziom i koszt interwencji i segmentacją klientów wiarygodne.  

## <a name="industry-outlook-and-approaches"></a>Metody i branży programu outlook
Obsługa zaawansowanych zmian jest znakiem dojrzała branży. Klasycznym przykładem jest branży telekomunikacyjnych, w którym wiadomo, subskrybenci często przełączać się między jednego dostawcę. Tych zmian dobrowolne jest podstawowym problemem. Ponadto dostawców współdzielenia znaczące wiedzy o *zmienności sterowniki*, służą do czynników, które wpływają na klientów, aby przełączyć się.

Na przykład słuchawki lub urządzenia to dobrze znanych sterownik zmian w firmie telefonu komórkowego. W wyniku popularnych zasad jest subsydiować cena słuchawki dla nowych subskrybentów i opłaty za pełną cenę dla istniejących klientów do uaktualnienia. W przeszłości klienci przeskokiem od jednego dostawcy do innego uzyskać rabat w wysokości nowe spowodowało tych zasad. To, z kolei, zostanie wyświetlony monit, dostawcy, aby uzyskać dokładniejsze ich strategie.

Wysoka zmienności słuchawki oferty jest czynnikiem, który szybko unieważnia modeli zmian, które są oparte na bieżących słuchawki modeli. Ponadto telefony komórkowe nie są tylko urządzenia usług telekomunikacyjnych, są one również instrukcje sposób (rozważ telefon iPhone). Te zmienne społecznościowych predykcyjne wykraczają poza zakres regularne telekomunikacyjnych zestawów danych.

Wynikiem do modelowania jest, że nie należy opracować dźwięku zasad poprzez wyeliminowanie znane przyczyny postęp dokonany w. W rzeczywistości jest strategii ciągłej modelowania klasycznych modeli, które określają ilościowo zmienne podzielonych na kategorie (na przykład drzewa decyzyjne), w tym **obowiązkowe**.

Przy użyciu zestawów danych big Data na swoim klientom, organizacje wykonywania analizy danych big data (w szczególności, wykrywanie zmian na podstawie danych big Data) jako efektywne podejście do problemu. Można znaleźć więcej informacji na temat podejścia danych big data do problemu postępu dokonanego w zaleceń w sekcji ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia do modelu zmienności klientów
Typowe proces rozwiązywania problemów do rozwiązywania zmienności klientów jest przedstawiony w rysunki 1-3:  

1. Model o podwyższonym ryzyku umożliwia należy wziąć pod uwagę wpływ akcji prawdopodobieństwo i ryzyka.
2. Model interwencji umożliwia należy wziąć pod uwagę, jak poziom interwencji mogą mieć wpływ na prawdopodobieństwo zmian i ilość klienta wartość okresu istnienia (CLV).
3. Ta analiza jest przydatna Analiza jakościowa, która jest przekazany do aktywnego kampanii marketingowej, który jest przeznaczony dla segmentów klientów, aby przedstawić ofertę optymalne.  

![Diagram przedstawiający sposób modele na uszkodzenia oraz decyzja o podwyższonym ryzyku daje uzyskiwanie przydatnych wyników analiz](./media/azure-ml-customer-churn-scenario/churn-1.png)

To podejście do przodu wyglądających jest najlepszym sposobem traktowania współczynnika zmian, ale powoduje dodanie jej złożoność: konieczne jest opracowanie archetype wielu modeli i śledzenia zależności między modelami. Interakcje między modelami można hermetyzować, jak pokazano na poniższym diagramie:  

![Postęp dokonany w diagram interakcji z modelu](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Rysunek 4: Ujednolicone archetype wielu modeli*  

Interakcja między modelami jest klucz, jeśli możemy dostarczać kompleksowe podejście do utrzymania klientów. Każdy model niekoniecznie spadku wraz z upływem czasu; w związku z tym, architektura jest niejawne pętli (podobnie jak archetype ustawione przez standard wyszukiwania danych WYSOKĄ-DM, [***3***]).  

Ogólnym cyklu obrotu w przypadku decyzji o podwyższonym ryzyku segmentacji/dekompozycji jest nadal uogólnionego struktury, która ma zastosowanie do wielu problemów biznesowych. Analizy zmienności to po prostu silne reprezentacja tej grupy problemów, ponieważ wskazuje cechy problem złożone biznesowe, które nie zezwala na uproszczonego rozwiązania do analizy predykcyjnej. Społecznościowych aspektów nowoczesnym podejściem do zmian danych nie są szczególnie wyróżnione w przypadku zastosowania podejścia, ale aspekty społecznościowych są hermetyzowane w archetype modelowania, jakie byłyby dowolnego modelu.  

W tym miejscu interesujące dodanie jest analizy danych big data. Sprzedawców detalicznych i telekomunikacji współczesnych zbierać wyczerpujące dane o swoich klientów i możemy łatwo przewidzieć, że stanie się potrzebę wielomodelowa łączności wspólnej trend danego pojawiających się trendów, takich jak Internet rzeczy i uniwersalnych urządzenia, dzięki czemu firmy mogą wykorzystać inteligentne rozwiązania w wielu warstwach.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementowanie archetype modelowania w usłudze Machine Learning Studio
Biorąc pod uwagę problem opisany, co to jest najlepszym sposobem na wdrożenie zintegrowane modelowania i oceniania podejście? W tej sekcji pokażemy, jak możemy zrobić to za pomocą usługi Azure Machine Learning Studio.  

Wielomodelowa podejście to podczas projektowania globalnego archetype dla współczynnika zmian. Nawet oceniania (predykcyjnej) części podejście powinna być wielu modeli.  

Na poniższym diagramie przedstawiono prototypu, że firma Microsoft tworzone zatrudniające cztery oceniania algorytmy w usłudze Machine Learning Studio przewidywanie zmienności. Przyczyna wielu modeli podejście nie jest tylko do utworzenia Klasyfikator zespołu, aby zwiększyć dokładność, ale także do ochrony przed nadmiernego dopasowywania i poprawić wybór funkcji przetestowanego rozwiązania ze szczegółami.  

![Zrzut ekranu przedstawiający obszar roboczy Studio złożone z wielu połączonych ze sobą modułów](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Rysunek 5: Prototyp zmian, podejście do modelowania*  

Poniższe sekcje zawierają więcej szczegółów na temat prototypu oceniania modelu, który wdrożyliśmy przy użyciu usługi Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Wybór danych i przygotowanie
Dane używane do tworzenia modeli i klientom ocenę zostały pobrane z rozwiązaniem pionowy CRM z danymi zaciemniony plikach, aby chronić prywatność klientów. Dane zawierają informacje dotyczące subskrypcji 8000 w Stanach Zjednoczonych i składa się z trzech źródeł: Inicjowanie obsługi administracyjnej danych (metadane subskrypcji), dane o aktywności (użycie systemu) i dane o pomocy technicznej klienta. Dane nie zawiera żadnych informacji związanych z firmą klientów; na przykład nie obejmuje wyniki metadanych lub zmienić ilość środków dostępnych na urządzeniach.  

Dla uproszczenia ETL i procesów do czyszczenia danych, są poza zakresem, ponieważ przyjęto założenie, że przygotowywania danych ma już zostały wykonane gdzie indziej.

Funkcji wyboru cech dla modelowania opiera się na wstępne istotności oceniania zestawu czynniki prognostyczne, uwzględnione w procesie, który używa modułu losowe lasu. Do wykonania w usłudze Machine Learning Studio firma Microsoft obliczona średnia, mediana i zakresy dla funkcji językiem. Na przykład dodaliśmy agregacje jakościowe danych, takich jak minimalne i maksymalne wartości dla działań użytkownika.

Możemy również przechwytywane danych czasowych informacji dla ostatnich sześciu miesięcy. Przeanalizowaliśmy dane przez jeden rok i możemy nawiązać, nawet jeśli są statystycznie istotne trendów, wpływ na postęp dokonany w będzie znacznie mniejsza po upływie sześciu miesięcy.  

Najbardziej istotną kwestią jest to, że cały proces, w tym ETL, wybór funkcji i modelowania została wdrożona w usłudze Machine Learning Studio, za pomocą źródeł danych w systemie Microsoft Azure.   

Następujące diagramy przedstawiają dane, które zostało użyte.  

![Zrzut ekranu przedstawiający przykładowy danych używanych wartości w wierszach](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Rysunek 6: Fragment źródła danych (zaciemniony plikach)*  

![Zrzut ekranu przedstawiający funkcji statystycznych wyodrębnione ze źródła danych](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Rysunek 7: Funkcje wyodrębnione ze źródła danych*
 

> Należy pamiętać, że te dane są prywatne, jak i w związku z tym nie może być współużytkowana modelu i danych.
> Jednak dla podobnych modelu przy użyciu publicznie dostępnych danych, zobacz w tym przykładzie przeprowadzanie eksperymentów [galerii Azure AI](https://gallery.azure.ai/): [Odpływ klientów firmy Telco](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Aby dowiedzieć się więcej na temat sposobu implementacji modelu analizy zmienności, przy użyciu pakietu Cortana Intelligence, zalecamy również [ten film wideo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) przez Tok Hyong Wee starszy Menedżer programu. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algorytmów używanych w prototypie
Użyliśmy następujące cztery algorytmów uczenia maszynowego do tworzenia prototypów (Brak dostosowanych wartości):  

1. Regresja logistyczna (LR)
2. Wzmocnione drzewo decyzyjnego (BT)
3. Uśrednionej perceptron (AP)
4. Obsługa wektor maszyny (SVM)  

Na poniższym diagramie przedstawiono część powierzchni projektowej eksperymentu, który wskazuje sekwencję, w którym zostały utworzone modele:  

![Zrzut ekranu przedstawiający niewielki fragment eksperymentów w studio kanwy](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Rysunek 8: Tworzenie modeli w usłudze Machine Learning Studio*  

### <a name="scoring-methods"></a>Metody oceniania
Firma Microsoft oceniane czterech modeli przy użyciu zestaw danych szkoleniowych etykietami.  

Możemy także przesyłane oceniania zestawu danych do modelu porównywalne skompilowanych przy użyciu klasycznej wersji sygnatury dostępu Współdzielonego Enterprise Miner 12. Firma Microsoft mierzy dokładności modelu sygnatur dostępu Współdzielonego i wszystkich czterech modeli usługi Machine Learning Studio.  

## <a name="results"></a>Wyniki
W tej sekcji firma Microsoft przedstawia naszych wnioski o dokładność modeli, w oparciu o oceniania zestawu danych.  

### <a name="accuracy-and-precision-of-scoring"></a>Dokładność i dokładności oceniania
Ogólnie rzecz biorąc wdrożenia w usłudze Azure Machine Learning Studio znajduje się za sygnatury dostępu Współdzielonego dokładność przez około 10 – 15% (powierzchni pod krzywą lub AUC).  

Jednak najważniejsze metryki fragmentów jest współczynnik błędów klasyfikacji: oznacza to, z churners pierwszych N jako przewidywane według klasyfikatora, który z nich faktycznie zrobił **nie** współczynnika zmian i jeszcze odebrane specjalnego traktowania? Poniższy diagram porównuje ten współczynnik błędów klasyfikacji dla wszystkich modeli:  

![Obszar wykresu krzywej porównywanie wydajności algorytmów 4](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Rysunek 9: Passau prototypu powierzchni pod krzywą*

### <a name="using-auc-to-compare-results"></a>Za pomocą AUC do porównywania wyników
Powierzchni pod krzywą (AUC) jest metryk, który reprezentuje globalny miara *odrębność* między dystrybucjami wyniki dla tych grup dodatnie i ujemne. Jest on podobny do tradycyjnego wykresu odbiorcy cechy — Operator (ROC), ale jedną istotną różnicą jest to, czy metryka AUC nie wymagają wybrania wartość progową. Zamiast tego podsumowuje wyniki za pośrednictwem **wszystkich** możliwych opcji. Z kolei tradycyjnych wykres ROC przedstawia dodatnich na osi pionowej i wyników fałszywie dodatnich na osi poziomej i różni się od wartości progowej klasyfikacji.   

AUC jest używany w postaci wartości dla różnych algorytmów (lub w innych systemach), ponieważ zezwala ona na modeli, które mają być porównane, za pomocą ich wartości AUC. Jest to popularny podejście w branżach, takich jak meteorologia i biosciences. W efekcie AUC reprezentuje popularne narzędzia do oceny wydajności klasyfikatora.  

### <a name="comparing-misclassification-rates"></a>Porównanie błędów klasyfikacji stawki
Firma Microsoft porównywane stawki błędu klasyfikacji na tym zestawie danych przy użyciu danych CRM około 8000 subskrypcji.  

* Współczynnik błędów klasyfikacji sygnatury dostępu Współdzielonego był 10 – 15%.
* Współczynnik błędów klasyfikacji usługi Machine Learning Studio był 15 – 20% churners pierwszych 200-300.  

W branży usług telekomunikacyjnych jest ważne, aby rozwiązać tylko klientów, którzy mają najwyższą ryzyko zmian danych, oferując im usługi Konsjerż ani specjalnego traktowania, inne. W tym zakresie implementacji usługi Machine Learning Studio realizuje wyniki podłączonego modelu sygnatur dostępu Współdzielonego.  

W ten sam sposób dokładności jest ważniejsze niż precyzja, ponieważ przede wszystkim jesteśmy zainteresowani poprawnie klasyfikowania potencjalne churners.  

Przyjrzyjmy się relacji w grafikę ich ożywienia, łatwych do zrozumienia przedstawiono na poniższym diagramie w Wikipedii:  

![Dwa obiekty docelowe. Przedstawia jeden element docelowy trafień znaczniki luźno grupowane, ale oka byków oznaczone w pobliżu "mało dokładność: dobre poprawność, niska dokładności. Innym elementem docelowym ściśle grupowane, ale dalekie od wartości oka byków oznaczone jako "mało dokładność: poprawność niska, dobrym dokładności"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Rysunek 10. Zależności między dokładność i precyzja*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Dokładność i dokładność wyników dla modelu drzewa decyzyjnego
Poniższy wykres przedstawia nieprzetworzonych wyników z oceniania modelu drzewa decyzyjnego, które akurat jest najbardziej dokładna spośród czterech modeli przy użyciu prototypu usługi Machine Learning:  

![Fragment kodu Tabela przedstawiająca dokładności, dokładności, wycofanie, wynik F, AUC, średnia strata dziennika i utraty dziennika szkolenia algorytmów cztery](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Rysunek 11: Właściwości modelu drzewa decyzyjnego*

## <a name="performance-comparison"></a>Porównanie wydajności
Możemy porównać szybkością, jaką danych zostało ocenione przy użyciu modeli usługi Machine Learning Studio i porównywalne model utworzony przy użyciu klasycznej wersji 12.1 Miner Enterprise sygnatury dostępu Współdzielonego.  

Poniższa tabela zawiera podsumowanie wydajności algorytmów:  

*Tabela 1. Te algorytmy ogólnej wydajności (dokładności)*

| LR | BT | Interfejs API | SVM |
| --- | --- | --- | --- |
| Model średni |Najlepszy Model |Gorszych wynikach w przypadku |Model średni |

Modele hostowanej w usłudze Machine Learning Studio pokonał usługę sygnatury dostępu Współdzielonego o 15-25% na szybkość wykonywania, ale dokładności był głównie od par.  

## <a name="discussion-and-recommendations"></a>Omówienie i zalecenia
W branży telekomunikacyjnych pojawiło się kilka rozwiązań do analizowania współczynnika zmian, w tym:  

* Pochodzi metryki dotyczące cztery podstawowe kategorie:
  * **Jednostka (na przykład subskrypcja)** . Aprowizuj podstawowe informacje o subskrypcji i/lub klienta, który jest przedmiotem współczynnika zmian.
  * **Działanie**. Uzyskaj wszystkie informacje możliwości użycia, które jest związane z jednostką, na przykład liczba logowań.
  * **Dział obsługi klienta**. Zebrać informacje z dzienników pomocy technicznej klientów, aby wskazać, czy subskrypcja ma problemy lub interakcji z obsługą klienta.
  * **Dane konkurencyjnym i biznesowych**. Uzyskaj możliwe informacje dotyczące klienta (na przykład może być niedostępny lub trudne do śledzenia).
* Użyj znaczenie dla dysku funkcji wyboru cech. Oznacza to, modelu drzewa decyzyjnego jest zawsze obietnic podejście.  

Korzystanie z tych czterech kategorii tworzy wrażenie prostej *deterministyczne* podejście oparte na indeksach utworzonym na uzasadnione czynników dla każdej kategorii, powinien wystarczyć do identyfikowania klientów ryzyka pod kątem zmian. Niestety pojęcie to jest prawdopodobnie wiarygodne, ale jest zrozumienie wartość false. Przyczyną jest to, współczynnika zmian to wpływu danych czasowych i czynniki przyczyniające się do zmian danych zwykle przejściowe stanów. Co prowadzi klientowi należy wziąć pod uwagę, pozostawiając już dziś mogą się różnić jutro i pewnością będzie różnych sześciu miesięcy od teraz. W związku z tym *probabilistic* modelu jest to konieczne.  

To ważne obserwacji jest często pomijane w firmie, która jest ogólnie preferuje podejście korzystający z analizy biznesowej do analizy, przede wszystkim, ponieważ jest łatwiejsze sprzedaży i dopuszcza proste automatyzacji.  

Obietnica samoobsługowej analizy przy użyciu usługi Machine Learning Studio jest jednak, że cztery kategorie informacji, posortowanych według wydziału lub działu, staje się cennym źródłem dla usługi machine learning na temat współczynnika zmian.  

Inny atrakcyjnych możliwości dostępne w usłudze Azure Machine Learning Studio jest możliwość dodawania niestandardowego modułu do repozytorium wstępnie zdefiniowanych modułów, które są już dostępne. Ta funkcja tworzy zasadniczo możliwość wybierz bibliotek i tworzenie szablonów dla rynków pionowych. Jest ważne różnicą Azure Machine Learning Studio na rynek.  

Mamy nadzieję, że kontynuować z tego tematu w przyszłości, szczególnie związane z analizą danych big data.
  

## <a name="conclusion"></a>Podsumowanie
W tym dokumencie opisano rozsądne podejście na co dzień do czynienia powszechny problem odpływu klientów przy użyciu ogólnych framework. Firma Microsoft traktowane jako prototyp do oceniania modeli i wdrożył ją za pomocą usługi Azure Machine Learning Studio. Na koniec możemy ocenić niezawodność i wydajność rozwiązania prototypu w odniesieniu do porównywalnych algorytmy sygnatury dostępu Współdzielonego.  

 

## <a name="references"></a>Dokumentacja
[1] predykcyjne analizy: Poza prognozy McKnight Zach. zarządzania informacją, lipiec/sierpień 2011 r. p.18 20.  

[2] artykułu w Wikipedii: [Dokładność i precyzja](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Przewodnik dotyczący wyszukiwania danych krok po kroku](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: Bardziej efektywne angażować swoich klientów i Zwiększaj wartość](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco postęp dokonany w szablonie model](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) w [Galeria sztucznej Inteligencji platformy Azure](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Dodatek
![Migawka prezentacji na prototypu współczynnika zmian](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Ilustracja 12 Migawka prezentacji na prototypu współczynnika zmian*
