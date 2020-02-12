---
title: Analizowanie zmian klientów
titleSuffix: ML Studio (classic) - Azure
description: Analiza przypadku przy opracowywaniu zintegrowanego modelu do analizowania i oceniania zmian klientów przy użyciu Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 903e3f3dcbcc72289fc82ec59dec0305b6adbc17
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150922"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analizowanie zmian klientów przy użyciu Azure Machine Learning Studio (klasyczny)
## <a name="overview"></a>Omówienie
W tym artykule przedstawiono implementację referencyjną projektu analizy zmian klientów, który jest zbudowany przy użyciu Azure Machine Learning Studio (klasyczny). W tym artykule omówiono powiązanych modelach ogólny na potrzeby całościowe rozwiązanie problemu odpływu klientów przemysłowe. Możemy również mierzenia dokładności modeli, które są tworzone przy użyciu uczenia maszynowego i ocenić używany na potrzeby dalszego rozwoju.  

### <a name="acknowledgements"></a>Podziękowania
Ten eksperyment został opracowany i przetestowany przez Serge Berger, główny analityk danych firmy Microsoft i Roger Barks, dawniej menedżer produktu dla Microsoft Azure Machine Learning Studio (klasyczny). Zespół dokumentacji platformy Azure gratefully potwierdza swoją wiedzę i Dziękujemy udostępnianie ten oficjalny dokument.

> [!NOTE]
> Dane używane w tym eksperymencie nie jest publicznie dostępna. Przykład tworzenia modelu uczenia maszynowego na potrzeby analizy zmian można znaleźć w temacie: [Szablon modelu zmian w sieci sprzedaży](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) w [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problem zmienności klientów
Firmom na rynku odbiorców i sektora przedsiębiorstw bez konieczności poświęcania czasu współczynnika zmian. Czasami zmian jest nadmierne i ma wpływ na decyzje dotyczące zasad. Tradycyjne rozwiązanie jest przewidzieć churners tendencje wysokiej i ich potrzeb za pośrednictwem usługi Konsjerż, kampanii marketingowych lub stosując zwolnień specjalne. Te metody mogą się różnić z branży branży. One może nawet różnią się od klastra danego konsumenta do drugiej w ramach jednej branży (na przykład telekomunikacja).

Typowe współczynnik jest firm należy zminimalizować starania związane z przechowywaniem tych specjalnych klienta. W związku z tym naturalnych metodologii będzie ocena każdy klient korzystający z prawdopodobieństwo, że współczynnik zmian i N pierwszych tych adresów. Najważniejszych klientów może być najbardziej dochodowe z nich. Na przykład w bardziej zaawansowanych scenariuszach funkcja zysk są stosowane podczas wybór kandydatów specjalne zwolnienie. Jednak te zagadnienia są tylko część strategii związanych ze zmian. Firmy muszą uwzględniać konto o podwyższonym ryzyku i tolerancji ryzyka skojarzony, poziom i koszt interwencji i segmentacją klientów wiarygodne.  

## <a name="industry-outlook-and-approaches"></a>Metody i branży programu outlook
Obsługa zaawansowanych zmian jest znakiem dojrzała branży. Klasycznym przykładem jest branży telekomunikacyjnych, w którym wiadomo, subskrybenci często przełączać się między jednego dostawcę. Tych zmian dobrowolne jest podstawowym problemem. Ponadto dostawcy zgromadziły znaczną wiedzę na temat *sterowników*zmian, które są czynnikami umożliwiającymi przełączenie klientów.

Na przykład słuchawki lub urządzenia to dobrze znanych sterownik zmian w firmie telefonu komórkowego. W wyniku popularnych zasad jest subsydiować cena słuchawki dla nowych subskrybentów i opłaty za pełną cenę dla istniejących klientów do uaktualnienia. W przeszłości klienci przeskokiem od jednego dostawcy do innego uzyskać rabat w wysokości nowe spowodowało tych zasad. To, z kolei, zostanie wyświetlony monit, dostawcy, aby uzyskać dokładniejsze ich strategie.

Wysoka zmienności słuchawki oferty jest czynnikiem, który szybko unieważnia modeli zmian, które są oparte na bieżących słuchawki modeli. Ponadto telefony komórkowe nie są tylko urządzenia usług telekomunikacyjnych, są one również instrukcje sposób (rozważ telefon iPhone). Te zmienne społecznościowych predykcyjne wykraczają poza zakres regularne telekomunikacyjnych zestawów danych.

Wynikiem do modelowania jest, że nie należy opracować dźwięku zasad poprzez wyeliminowanie znane przyczyny postęp dokonany w. W rzeczywistości ciągła strategia modelowania, w tym modele klasyczne, które określają zmienne kategorii (na przykład drzewa decyzyjne), jest **obowiązkowa**.

Przy użyciu zestawów danych big Data na swoim klientom, organizacje wykonywania analizy danych big data (w szczególności, wykrywanie zmian na podstawie danych big Data) jako efektywne podejście do problemu. Można znaleźć więcej informacji na temat podejścia danych big data do problemu postępu dokonanego w zaleceń w sekcji ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia do modelu zmienności klientów
Typowe proces rozwiązywania problemów do rozwiązywania zmienności klientów jest przedstawiony w rysunki 1-3:  

1. Model o podwyższonym ryzyku umożliwia należy wziąć pod uwagę wpływ akcji prawdopodobieństwo i ryzyka.
2. Model interwencji umożliwia należy wziąć pod uwagę, jak poziom interwencji mogą mieć wpływ na prawdopodobieństwo zmian i ilość klienta wartość okresu istnienia (CLV).
3. Ta analiza jest przydatna Analiza jakościowa, która jest przekazany do aktywnego kampanii marketingowej, który jest przeznaczony dla segmentów klientów, aby przedstawić ofertę optymalne.  

![Diagram przedstawiający sposób, w jaki tolerancja ryzyka Plus modele decyzyjne dają szczegółowe informacje umożliwiające podjęcie odpowiednich działań](./media/azure-ml-customer-churn-scenario/churn-1.png)

To podejście do przodu wyglądających jest najlepszym sposobem traktowania współczynnika zmian, ale powoduje dodanie jej złożoność: konieczne jest opracowanie archetype wielu modeli i śledzenia zależności między modelami. Interakcje między modelami można hermetyzować, jak pokazano na poniższym diagramie:  

![Diagram interakcji modelu zmian](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Rysunek 4: ujednolicony Archetype wielomodelowy*  

Interakcja między modelami jest klucz, jeśli możemy dostarczać kompleksowe podejście do utrzymania klientów. Każdy model musi być niezbędny do obniżenia wydajności w czasie; w związku z tym architektura jest pętlą niejawną (podobną do Archetype ustawioną w standardzie wyszukiwania danych przez WYRAZISTy DM, [***3***]).  

Ogólnym cyklu obrotu w przypadku decyzji o podwyższonym ryzyku segmentacji/dekompozycji jest nadal uogólnionego struktury, która ma zastosowanie do wielu problemów biznesowych. Analizy zmienności to po prostu silne reprezentacja tej grupy problemów, ponieważ wskazuje cechy problem złożone biznesowe, które nie zezwala na uproszczonego rozwiązania do analizy predykcyjnej. Społecznościowych aspektów nowoczesnym podejściem do zmian danych nie są szczególnie wyróżnione w przypadku zastosowania podejścia, ale aspekty społecznościowych są hermetyzowane w archetype modelowania, jakie byłyby dowolnego modelu.  

W tym miejscu interesujące dodanie jest analizy danych big data. Sprzedawców detalicznych i telekomunikacji współczesnych zbierać wyczerpujące dane o swoich klientów i możemy łatwo przewidzieć, że stanie się potrzebę wielomodelowa łączności wspólnej trend danego pojawiających się trendów, takich jak Internet rzeczy i uniwersalnych urządzenia, dzięki czemu firmy mogą wykorzystać inteligentne rozwiązania w wielu warstwach.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementacja Archetype modelowania w Machine Learning Studio (klasyczny)
Co to jest najlepszy sposób wdrożenia zintegrowanego podejścia modelowania i oceniania? W tej sekcji pokazano, jak to zrobić za pomocą Azure Machine Learning Studio (klasyczny).  

Wielomodelowa podejście to podczas projektowania globalnego archetype dla współczynnika zmian. Nawet oceniania (predykcyjnej) części podejście powinna być wielu modeli.  

Na poniższym diagramie przedstawiono prototyp utworzony przez siebie, który wykorzystuje cztery algorytmy oceniania w Machine Learning Studio (klasyczny) do przewidywania zmian. Przyczyna wielu modeli podejście nie jest tylko do utworzenia Klasyfikator zespołu, aby zwiększyć dokładność, ale także do ochrony przed nadmiernego dopasowywania i poprawić wybór funkcji przetestowanego rozwiązania ze szczegółami.  

![Zrzut ekranu przedstawiający złożony obszar roboczy programu Studio (klasyczny) z wieloma połączonymi modułami](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Rysunek 5. prototyp podejścia do modelowania zmian*  

Poniższe sekcje zawierają więcej szczegółów na temat prototypowego modelu oceniania, który został zaimplementowany przy użyciu Machine Learning Studio (klasyczny).  

### <a name="data-selection-and-preparation"></a>Wybór danych i przygotowanie
Dane używane do tworzenia modeli i klientom ocenę zostały pobrane z rozwiązaniem pionowy CRM z danymi zaciemniony plikach, aby chronić prywatność klientów. Dane zawierają informacje dotyczące subskrypcji 8000 w Stanach Zjednoczonych i składa się z trzech źródeł: Inicjowanie obsługi administracyjnej danych (metadane subskrypcji), dane o aktywności (użycie systemu) i dane o pomocy technicznej klienta. Dane nie obejmują żadnych informacji związanych z działalnością klienta; nie obejmują na przykład metadanych lojalności ani kredytów.  

Dla uproszczenia ETL i procesów do czyszczenia danych, są poza zakresem, ponieważ przyjęto założenie, że przygotowywania danych ma już zostały wykonane gdzie indziej.

Funkcji wyboru cech dla modelowania opiera się na wstępne istotności oceniania zestawu czynniki prognostyczne, uwzględnione w procesie, który używa modułu losowe lasu. Dla implementacji w Machine Learning Studio (klasyczny) obliczamy średnią, medianę i zakresy dla funkcji reprezentatywnych. Na przykład dodaliśmy agregacje jakościowe danych, takich jak minimalne i maksymalne wartości dla działań użytkownika.

Możemy również przechwytywane danych czasowych informacji dla ostatnich sześciu miesięcy. Przeanalizowaliśmy dane przez jeden rok i możemy nawiązać, nawet jeśli są statystycznie istotne trendów, wpływ na postęp dokonany w będzie znacznie mniejsza po upływie sześciu miesięcy.  

Najważniejszym punktem jest to, że cały proces, w tym ETL, wybór funkcji i modelowanie, został zaimplementowany w Machine Learning Studio (klasyczny), przy użyciu źródeł danych w Microsoft Azure.   

Następujące diagramy przedstawiają dane, które zostało użyte.  

![Zrzut ekranu przedstawiający przykład danych używanych z nieprzetworzonymi wartościami](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Ilustracja 6. fragment źródła danych (zasłonięty)*  

![Zrzut ekranu przedstawiający funkcje statystyczne wyodrębnione ze źródła danych](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Rysunek 7. funkcje wyodrębnione ze źródła danych*
 

> Należy pamiętać, że te dane są prywatne, jak i w związku z tym nie może być współużytkowana modelu i danych.
> Jednak w przypadku podobnego modelu korzystającego z publicznie dostępnych danych zapoznaj się z tym przykładowym eksperymentem w [Azure AI Gallery](https://gallery.azure.ai/): [odpływ](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Aby dowiedzieć się więcej o tym, jak zaimplementować model analizy zmian przy użyciu Cortana Intelligence Suite, zalecamy także [ten film wideo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) według starszego menedżera programów wee Hyong tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algorytmów używanych w prototypie
Użyliśmy następujące cztery algorytmów uczenia maszynowego do tworzenia prototypów (Brak dostosowanych wartości):  

1. Regresja logistyczna (LR)
2. Wzmocnione drzewo decyzyjnego (BT)
3. Uśrednionej perceptron (AP)
4. Obsługa wektor maszyny (SVM)  

Na poniższym diagramie przedstawiono część powierzchni projektowej eksperymentu, który wskazuje sekwencję, w którym zostały utworzone modele:  

![Zrzut ekranu przedstawiający małą sekcję kanwy eksperymentu dotyczącej programu Studio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Ilustracja 8. Tworzenie modeli w Machine Learning Studio (klasyczny)*  

### <a name="scoring-methods"></a>Metody oceniania
Firma Microsoft oceniane czterech modeli przy użyciu zestaw danych szkoleniowych etykietami.  

Możemy także przesyłane oceniania zestawu danych do modelu porównywalne skompilowanych przy użyciu klasycznej wersji sygnatury dostępu Współdzielonego Enterprise Miner 12. Mierzy dokładność modelu SAS i wszystkie cztery modele Machine Learning Studio (klasyczne).  

## <a name="results"></a>Wyniki
W tej sekcji firma Microsoft przedstawia naszych wnioski o dokładność modeli, w oparciu o oceniania zestawu danych.  

### <a name="accuracy-and-precision-of-scoring"></a>Dokładność i dokładności oceniania
Ogólnie rzecz biorąc, implementacja w Azure Machine Learning Studio (klasyczna) znajduje się za SYGNATURą dostępu współdzielonego o 10-15% (obszar poniżej krzywej lub AUC).  

Jednak najważniejszym metryką w przypadku zmian jest współczynnik niepowodzeń klasyfikacji: to jest, z górnych zmian, które są przewidywane przez klasyfikator, który faktycznie **nie** został zmieniony, i jeszcze otrzymało specjalne traktowanie? Poniższy diagram porównuje ten współczynnik błędów klasyfikacji dla wszystkich modeli:  

![Obszar pod wykresem krzywej porównujący wydajność 4 algorytmów](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Rysunek 9: obszar prototypu Passau pod krzywą*

### <a name="using-auc-to-compare-results"></a>Za pomocą AUC do porównywania wyników
Obszar pod krzywą (AUC) jest metryką, która przedstawia globalną miarę *separability* między dystrybucją wyników dla populacji dodatniej i ujemnej. Jest on podobny do tradycyjnego wykresu odbiorcy cechy — Operator (ROC), ale jedną istotną różnicą jest to, czy metryka AUC nie wymagają wybrania wartość progową. Zamiast tego podsumowuje wyniki na **wszystkie** możliwe opcje. Z kolei tradycyjnych wykres ROC przedstawia dodatnich na osi pionowej i wyników fałszywie dodatnich na osi poziomej i różni się od wartości progowej klasyfikacji.   

AUC jest używany jako miara wartości dla różnych algorytmów (lub różnych systemów), ponieważ umożliwia porównywanie modeli za pomocą ich AUCch. Jest to popularny podejście w branżach, takich jak meteorologia i biosciences. W efekcie AUC reprezentuje popularne narzędzia do oceny wydajności klasyfikatora.  

### <a name="comparing-misclassification-rates"></a>Porównanie błędów klasyfikacji stawki
Firma Microsoft porównywane stawki błędu klasyfikacji na tym zestawie danych przy użyciu danych CRM około 8000 subskrypcji.  

* Współczynnik błędów klasyfikacji sygnatury dostępu Współdzielonego był 10 – 15%.
* Częstotliwość niepowodzeń klasyfikacji Machine Learning Studio (klasyczna) była 15-20% dla najbardziej pożądanych zmian 200-300.  

W branży usług telekomunikacyjnych jest ważne, aby rozwiązać tylko klientów, którzy mają najwyższą ryzyko zmian danych, oferując im usługi Konsjerż ani specjalnego traktowania, inne. W tym względzie implementacja Machine Learning Studio (klasyczna) osiąga wyniki na wartości nominalnej z modelem SAS.  

W ten sam sposób dokładności jest ważniejsze niż precyzja, ponieważ przede wszystkim jesteśmy zainteresowani poprawnie klasyfikowania potencjalne churners.  

Przyjrzyjmy się relacji w grafikę ich ożywienia, łatwych do zrozumienia przedstawiono na poniższym diagramie w Wikipedii:  

![Dwa elementy docelowe. Jeden element docelowy pokazuje luźno pogrupowane znaczniki, ale blisko byków oznaczonych jako "niska dokładność: Dobry stopień precyzji. Inna lokalizacja docelowa jest ściśle zgrupowana, ale daleko od buhajów — oczy oznaczonej jako "niska dokładność: niska wartość rzeczywista, dobra precyzja"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Rysunek 10. kompromis między dokładnością i dokładnością*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Dokładność i dokładność wyników dla modelu drzewa decyzyjnego
Poniższy wykres przedstawia nieprzetworzonych wyników z oceniania modelu drzewa decyzyjnego, które akurat jest najbardziej dokładna spośród czterech modeli przy użyciu prototypu usługi Machine Learning:  

![Fragment tabeli przedstawiający dokładność, precyzję, odwołanie, F-Score, AUC, średnią utratę dziennika i utratę dzienników szkolenia dla czterech algorytmów](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Rysunek 11: Charakterystyka podwyższania modelu drzewa decyzyjnego*

## <a name="performance-comparison"></a>Porównanie wydajności
Porównano szybkość, z jaką dane były oceniane przy użyciu modeli Machine Learning Studio (klasycznych) i porównywalny model utworzony przy użyciu wersji klasycznej SAS Enterprise Miner 12,1.  

Poniższa tabela zawiera podsumowanie wydajności algorytmów:  

*Tabela 1. Ogólna wydajność (dokładność) algorytmów*

| LR | BT | Interfejs API | SVM |
| --- | --- | --- | --- |
| Model średni |Najlepszy Model |Gorszych wynikach w przypadku |Model średni |

Modele hostowane w Machine Learning Studio (klasyczne) przeprowadzili SYGNATURę dostępu współdzielonego o 15-25% w celu przyspieszenia wykonywania, ale dokładność była wysoka na wartości nominalnej.  

## <a name="discussion-and-recommendations"></a>Omówienie i zalecenia
W branży telekomunikacyjnych pojawiło się kilka rozwiązań do analizowania współczynnika zmian, w tym:  

* Pochodzi metryki dotyczące cztery podstawowe kategorie:
  * **Jednostka (na przykład subskrypcja)** . Aprowizuj podstawowe informacje o subskrypcji i/lub klienta, który jest przedmiotem współczynnika zmian.
  * **Działanie**. Uzyskaj wszystkie informacje możliwości użycia, które jest związane z jednostką, na przykład liczba logowań.
  * **Obsługa klienta**. Zebrać informacje z dzienników pomocy technicznej klientów, aby wskazać, czy subskrypcja ma problemy lub interakcji z obsługą klienta.
  * **Dane konkurencyjne i biznesowe**. Uzyskaj możliwe informacje dotyczące klienta (na przykład może być niedostępny lub trudne do śledzenia).
* Użyj znaczenie dla dysku funkcji wyboru cech. Oznacza to, modelu drzewa decyzyjnego jest zawsze obietnic podejście.  

Użycie tych czterech kategorii tworzy złudzenie, *że proste podejście* , oparte na indeksach utworzonych na odpowiednich czynnikach na kategorię, powinno być wystarczające do identyfikowania klientów narażonych na zmiany. Niestety pojęcie to jest prawdopodobnie wiarygodne, ale jest zrozumienie wartość false. Przyczyną jest to, współczynnika zmian to wpływu danych czasowych i czynniki przyczyniające się do zmian danych zwykle przejściowe stanów. Co prowadzi klientowi należy wziąć pod uwagę, pozostawiając już dziś mogą się różnić jutro i pewnością będzie różnych sześciu miesięcy od teraz. W związku z tym model *probabilistyczne* jest konieczny.  

To ważne obserwacji jest często pomijane w firmie, która jest ogólnie preferuje podejście korzystający z analizy biznesowej do analizy, przede wszystkim, ponieważ jest łatwiejsze sprzedaży i dopuszcza proste automatyzacji.  

Jednak obietnica samoobsługowej analizy przy użyciu Machine Learning Studio (klasyczny) polega na tym, że cztery kategorie informacji, które są klasyfikowane według działu lub działu, stają się cennym źródłem dla uczenia maszynowego na temat zmian.  

Kolejną atrakcyjną możliwością Azure Machine Learning Studio (klasyczną) jest możliwość dodawania niestandardowego modułu do repozytorium wstępnie zdefiniowanych modułów, które są już dostępne. Ta funkcja tworzy zasadniczo możliwość wybierz bibliotek i tworzenie szablonów dla rynków pionowych. Jest to ważny odróżniający Azure Machine Learning Studio (klasyczny) w miejscu na rynku.  

Mamy nadzieję, że kontynuować z tego tematu w przyszłości, szczególnie związane z analizą danych big data.
  

## <a name="conclusion"></a>Podsumowanie
W tym dokumencie opisano rozsądne podejście na co dzień do czynienia powszechny problem odpływu klientów przy użyciu ogólnych framework. Uważamy prototyp dla modeli oceniania i zaimplementowano go przy użyciu Azure Machine Learning Studio (klasyczne). Na koniec możemy ocenić niezawodność i wydajność rozwiązania prototypu w odniesieniu do porównywalnych algorytmy sygnatury dostępu Współdzielonego.  

 

## <a name="references"></a>Dokumentacja
[1] Analiza predykcyjna: poza przewidywaniami, W. McKnight, zarządzanie informacjami, lipiec/sierpień 2011, p. 18 – 20.  

[2] Artykuł witryny Wikipedia: [dokładność i precyzja](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [wyraziste-DM 1,0: Przewodnik wyszukiwania danych krok po kroku](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing danych Big Data: zwiększaj efektywność klientów i zwiększ wartość](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Szablon modelu zmian odpływ](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) w [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Dodatek
![Migawka prezentacji na prototypie zmian](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Ilustracja 12. migawka prezentacji na prototypie zmian*
