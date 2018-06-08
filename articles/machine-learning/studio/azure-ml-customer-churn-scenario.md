---
title: Analizowanie danych churn odbiorcy — przy użyciu usługi Machine Learning | Dokumentacja firmy Microsoft
description: Analiza przypadku powstania zintegrowanego modelu do analizowania i oceniania przenoszenie klienta
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 1beba951a6785aa90eef22a63a8064e9da1bb27f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835121"
---
# <a name="analyzing-customer-churn-using-azure-machine-learning"></a>Analizowanie danych churn odbiorcy — za pomocą usługi Azure Machine Learning
## <a name="overview"></a>Przegląd
Ten artykuł przedstawia wdrożenie odwołanie do projektu analizy przenoszenie klienta skompilowanego za pomocą usługi Azure Machine Learning. W tym artykule omówiono skojarzone ogólnego modeli całościowe rozwiązanie problemu przenoszenie przemysłowych klienta. Firma Microsoft również mierzenia dokładności modeli, które są tworzone za pomocą uczenia maszynowego i ocenić instrukcjami w celu dalszego rozwijania.  

### <a name="acknowledgements"></a>Potwierdzenia
Tego eksperymentu został opracowany i przetestowane przez Serge Berger naukowca danych podmiot zabezpieczeń w firmie Microsoft i Roger Barga, wcześniej produktu programu Microsoft Azure Machine Learning. Zespół Azure dokumentacji gratefully uznaje ich wiedzy i Dziękujemy je do udostępniania tego oficjalny dokument.

> [!NOTE]
> Dane używane do tego eksperymentu nie jest publicznie dostępna. Na przykład sposobu tworzenia modelu uczenia maszynowego, analizy zmian, zobacz: [handlowej churn — szablonu modelu](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) w [galerii Azure AI](http://gallery.cortanaintelligence.com/)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>Problem z tworzeniem klienta
Przedsiębiorstwa na rynku konsumenta i we wszystkich sektorach przedsiębiorstwa mają radzenia sobie z zmian. Czasami zmian jest nadmierne i wpływ decyzji dotyczących zasad. Tradycyjne rozwiązania jest prognozowania churners większego ukierunkowania wysokiej i potrzeb za pośrednictwem usługi Konsjerż, kampanii, lub przez zastosowanie specjalnych zwolnień adresów. Te metody mogą się różnić z branży branży. Można nawet różnią z klastra z określonego klienta do innego w ramach jednego industry (na przykład telekomunikacyjnych).

Typowe współczynnik jest czy firma potrzebuje do zminimalizowania tych działań przechowywania specjalne klienta. W związku z tym metodologię fizyczne będzie wynik każdego klienta z prawdopodobieństwo zmian i N pierwszych tych adresów. Najlepszych klientów może być najbardziej dochodowe te. Na przykład w bardziej zaawansowanych scenariuszy funkcja zysku jest zastosować podczas wybór kandydatów specjalne zwolnienia. Jednak te zagadnienia są tylko część pełnej strategii zajmujących się ilość danych churn. Firm również powinny uwzględniać ryzyka konta (i tolerancji ryzyka skojarzone), poziom i koszt interwencji i segmentacji odbiorców wiarygodne.  

## <a name="industry-outlook-and-approaches"></a>Outlook branżowych i metod
Zaawansowane obsługi zmian jest znak dojrzałe branżowych. Klasycznym przykładem jest branży telekomunikacyjnych, gdy subskrybenci wiadomo, że często przełączać się między jeden dostawca. To dobrowolny zmian jest podstawowym. Ponadto dostawców współdzielenia znaczących wiedzy o *churn — sterowniki*, które są czynników, które dysków klientów do przełącznika.

Na przykład wybór słuchawki lub urządzenia jest dobrze znanego sterownika z postęp dokonany w firmie telefonu komórkowego. W związku z tym popularnych zasad jest subsydiować ceny słuchawki dla nowych subskrybentów i obciążania pełną cenę dla istniejących klientów do uaktualnienia. W przeszłości te zasady spowodowało skaczące z jednego dostawcę rozwiązania do innego klientom uzyskać rabat nowe. To z kolei ma monit dostawcy, aby doprecyzować strategii.

Wysoka zmienności słuchawki oferty jest czynnik, który szybko unieważnia modeli zmian, które są oparte na bieżących słuchawki modeli. Ponadto telefonów nie są tylko telekomunikacyjnych urządzenia, są one również instrukcje sposób (rozważ telefonów iPhone). Te zmienne społecznościowych predykcyjne są poza zakresem regularne telekomunikacyjnych zestawów danych.

Wynikiem do modelowania jest, że po prostu, eliminując znane przyczyny przenoszenia nie można opracować dźwięku zasad. W rzeczywistości jest strategii ciągłego modelowania klasycznych modeli, które określenie zmienne podzielone na kategorie (takie jak drzewa decyzyjne), w tym **obowiązkowe**.

Za pomocą zestawów danych big Data na klientach, organizacje wykonywania analizy danych big data (w szczególności wykrywania zmian na podstawie danych big) jako efektywnym sposobem problem. Można znaleźć więcej informacji na temat podejścia danych big data problemu postęp dokonany w zaleceń w sekcji ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia do modelu przenoszenie klienta
Typowe proces rozwiązywania problemów do rozwiązania przenoszenie klienta jest opisany w wartości 1-3:  

1. Model ryzyka umożliwia należy wziąć pod uwagę wpływ akcje prawdopodobieństwo i ryzyka.
2. Model interwencji umożliwia należy wziąć pod uwagę, jak poziom interwencji mogą wpłynąć na prawdopodobieństwo zmian oraz ilości klienta wartość okresu istnienia (CLV).
3. Analiza pozwalają na analizie jakościowej, który jest przekazany do aktywnego kampanię marketingową przeznaczonego dla segmentów klientów do dostarczania optymalne oferty.  

![][1]

Takie podejście do przodu wyglądającej to najlepszy sposób na traktowanie zmian, ale ma złożoność: konieczne jest opracowanie archetype wiele modeli i śledzenia zależności między modelami. Interakcja między modeli można hermetyzowany, jak pokazano na poniższym diagramie:  

![][2]

*Rysunek 4: Unified archetype wiele modeli*  

Interakcji między modelami jest kluczem, jeśli możemy dostarczyć kompleksowe rozwiązanie do przechowywania klienta. Każdy model powoduje spadek niekoniecznie wraz z upływem czasu; w związku z tym architektury jest niejawne pętli (podobnie jak archetype ustawione przez DM WYSOKĄ standard wyszukiwania danych, [***3***]).  

Ogólny cyklu ryzyka decyzji obrotu segmentacji/rozkładu jest nadal uogólniony struktury, która ma zastosowanie do wielu problemów biznesowych. Przenoszenie analizy jest po prostu przedstawiciela silne tej grupy problemów, ponieważ wskazuje wszystkie cechy o problemie biznesowych, który nie zezwala na uproszczone rozwiązanie predykcyjne. Społecznościowych aspektów nowoczesnych podejście do churn — nie są szczególnie wyróżnione w ujęciu, ale społecznościowych aspekty znajdują się w archetype modelowania, jakie byłyby każdy model.  

W tym miejscu interesujące dodanie jest analizy danych big data. Telekomunikacyjnych i firm handlowych współczesnych zbiera wyczerpujący dane dotyczące klientów i możemy łatwo zapewnienie, że stanie się konieczność wielokrotnego modelu łączności wspólnej trend danego pojawiających się trendy, takie jak Internet rzeczy i uniwersalnych urządzeń, które umożliwia firm fragmentów inteligentnych rozwiązań na wielu warstw.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementowanie archetype modelowania w usłudze Machine Learning Studio
Podana problem opisany właśnie, co to jest najlepszy sposób, aby zaimplementować zintegrowanego modelowania i oceniania podejście? W tej sekcji przedstawiono sposób możemy zrobić to za pomocą usługi Azure Machine Learning Studio.  

Wiele modeli podejście jest koniecznością podczas projektowania globalne archetype dla zmian. Nawet oceniania (predykcyjnej) część podejście powinna być wiele modeli.  

Na poniższym diagramie przedstawiono prototyp, że utworzyliśmy, który wykorzystuje cztery oceniania algorytmy Machine Learning Studio na potrzeby prognozowania zmian. Przyczyna wielu modelu podejście jest nie tylko utworzenie klasyfikatora zespół, zwiększyć dokładność, ale również ochronę przed uwierzytelniając dopasowywania i poprawić wybór przetestowanego funkcji.  

![][3]

*Rysunek 5: Prototyp przenoszenie modelowania podejście*  

Poniższe sekcje zawierają szczegółowe informacje o prototypu oceniania modelu wprowadzonym za pomocą usługi Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Wybór danych i przygotowanie
Dane używane do tworzenia modeli i klienci wynik zostały pobrane z rozwiązania pionowy CRM, z danymi zaciemniona na ochronę prywatności klienta. Dane zawierają informacje na temat 8000 subskrypcji w Stanach Zjednoczonych i składa się z trzech źródeł: Inicjowanie obsługi danych (metadane subskrypcji), dane o aktywności (użycie systemu) i danych obsługi klienta. Dane nie obejmuje dowolnego rodzaju działalności powiązane informacje o klientach; na przykład nie zawiera wyników lojalność metadanych lub faktury.  

Dla uproszczenia ETL i procesy czyszczenia danych są poza zakresem, ponieważ przyjęto założenie, że przygotowanie danych ma już zostały wykonane w innych miejscach.   

Wybieranie funkcji do modelowania jest oparta na wstępne istotności oceniania zestawu zmienne predykcyjne, uwzględnione w procesie, który korzysta z modułu losowe lasu. Do wykonania w usłudze Machine Learning Studio możemy obliczyć średniej, mediana i zakresy dla funkcji reprezentatywny. Na przykład dodawać agreguje jakościowe danych, takie jak wartości minimalną i maksymalną dla działań użytkownika.    

Możemy również przechwycono danych czasowych informacje dotyczące ostatnich sześciu miesięcy. Przeanalizowaliśmy danych przez jeden rok i możemy ustanowić, że nawet wtedy, gdy było trendów statystycznie istotne, wpływa na przenoszenie będzie znacznie mniejsza po upływie sześciu miesięcy.  

Najważniejsze punkt znajduje się czy cały proces, w tym ETL, wybór funkcji i modelowanie został wdrożony w usłudze Machine Learning Studio, korzystanie ze źródeł danych na platformie Microsoft Azure.   

Poniższych diagramach przedstawiono dane, które zostało użyte.  

![][4]

*Rysunek 6: Fragment źródła danych (zaciemniona)*  

![][5]

*Rysunek 7: Funkcje wyodrębnione ze źródła danych*
 

> Należy pamiętać, że te dane jest prywatny i dlatego nie może być współużytkowana modelu i danych.
> Jednak dla podobnych modelu przy użyciu dostępnych publicznie danych, zobacz ten przykład eksperymentu w [galerii Azure AI](http://gallery.cortanaintelligence.com/): [Telco churn klienta —](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Aby dowiedzieć się więcej na temat implementacji zmian modelu analizy przy użyciu pakietu Cortana Intelligence Suite, zalecamy również [ten film](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) przez Tok Hyong Wee starszy Menedżer programu. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algorytmy używane w prototypu
Następujące cztery algorytmów uczenia maszynowego możemy używany do tworzenia prototypu (Brak dostosowanych):  

1. Regresja logistyczna (LR)
2. Drzewo decyzyjne boosted (BT)
3. Perceptron uśrednionej (AP)
4. Obsługa wektor maszyny (SVM)  

Na poniższym diagramie przedstawiono część powierzchnię projektu eksperymentu wskazuje sekwencję, w której utworzono modele:  

![][6]  

*Rysunek 8: Tworzenie modeli w usłudze Machine Learning Studio*  

### <a name="scoring-methods"></a>Metod oceniania
Cztery modele możemy oceniane przy użyciu zestawu danych oznaczonych szkolenia.  

Przesłaliśmy również oceniania zestawu danych do modelu można porównywać pod względem skompilowane przy użyciu pulpitu wersji 12 analizatora Enterprise sygnatury dostępu Współdzielonego. Firma Microsoft mierzy dokładności modelu sygnatur dostępu Współdzielonego i wszystkie cztery modele usługi Machine Learning Studio.  

## <a name="results"></a>Wyniki
W tej sekcji możemy przedstawić uzyskanych danych o dokładności modeli, opartych na oceniania zestawu danych.  

### <a name="accuracy-and-precision-of-scoring"></a>Dokładność i precyzja oceniania
Ogólnie rzecz biorąc do implementacji w usłudze Azure Machine Learning znajduje się za SAS dokładności o około 10 – 15% (obszar w obszarze krzywej lub AUC).  

Jednak najważniejsze Metryka w zmian jest częstotliwość błędów klasyfikacji: oznacza to, z churners pierwszych N jako przewidywane przez klasyfikator, który z nich rzeczywiście została **nie** churn — i jeszcze odebrane szczególnego traktowania? Poniższy diagram porównuje to częstotliwość błędów klasyfikacji dla wszystkich modeli:  

![][7]

*Rysunek 9: Passau prototypu obszarze krzywej*

### <a name="using-auc-to-compare-results"></a>Przy użyciu AUC do porównywania wyników
Obszar w krzywej (AUC) jest metrykę, która reprezentuje globalne miara *odrębność* między dystrybucje wyniki dla populacji dodatnie i ujemne. Jest on podobny do tradycyjnych wykresu cech Operator odbiornika (ROC), ale jest jedną istotną różnicą, że metryka AUC nie wymaga można wybrać wartość progu. Zamiast tego podsumowuje wyniki za pośrednictwem **wszystkie** możliwe opcje. Z kolei tradycyjnych wykres ROC pokazuje współczynnik dodatnią na osi pionowej i częstotliwość dodatnią wartość false na osi poziomej i różni się od wartości progowej klasyfikacji.   

AUC jest zazwyczaj używany jako środek do wartości dla różnych algorytmów (lub w innych systemach) ponieważ zezwala ona modele, aby porównać za pomocą ich wartości AUC. To podejście popularnych w branży, takie jak meteorologia i biosciences. W związku z tym AUC reprezentuje popularne narzędzia do oceny wydajności klasyfikatora.  

### <a name="comparing-misclassification-rates"></a>Porównywanie stawki błędu klasyfikacji
Firma Microsoft porównywany stawek błędu klasyfikacji dla danego zestawu danych przy użyciu danych CRM około 8000 subskrypcji.  

* Częstotliwość błędów klasyfikacji sygnatury dostępu Współdzielonego to 10 – 15%.
* Współczynnik błędów klasyfikacji Machine Learning Studio został 15-20% dla churners pierwsze 200 300.  

W branży telekomunikacyjnych koniecznie tylko klientów, którzy mają najwyższym ryzyku do churn — oferując im usługi Konsjerż lub innych szczególnego traktowania adresów. W tym zakresie implementacji usługi Machine Learning Studio osiąga wyników blokowego modelu sygnatur dostępu Współdzielonego.  

W ten sam sposób dokładność jest ważniejsze niż precyzja, ponieważ przeważnie Dbamy o poprawnie klasyfikacji churners potencjalnych.  

Relacja grafiki barwnym, łatwe do zrozumienia przedstawiono na poniższym diagramie, z Wikipedia:  

![][8]

*Rysunek 10: Zależności między dokładność i dokładność*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Dokładność i precyzja wyników dla modelu drzewa decyzyjnego boosted
Poniższy schemat przedstawia pierwotnych wyników oceniania przy użyciu prototypu uczenia maszynowego dla modelu drzewa decyzyjnego boosted się najbardziej dokładna między cztery modele:  

![][9]

*Rysunek 11: Właściwości modelu drzewa decyzyjnego Boosted*

## <a name="performance-comparison"></a>Porównanie wydajności
Firma Microsoft porównywany szybkości, jaką danych został oceniane przy użyciu modeli Machine Learning Studio i porównywanie modelu utworzone za pomocą wersji pulpitu 12.1 analizatora Enterprise SAS.  

Poniższa tabela zawiera podsumowanie wydajności algorytmów:  

*Tabela 1. Algorytmów ogólnej wydajności (dokładność)*

| LR | BT | Interfejs API | SVM |
| --- | --- | --- | --- |
| Model średni |Najlepsze modelu |Gorszych wynikach w przypadku |Model średni |

Modele hostowanych w usłudze Machine Learning Studio outperformed SAS przez 15-25% do szybkości wykonywania, ale dokładność został przede wszystkim od par.  

## <a name="discussion-and-recommendations"></a>Omówienie i zalecenia
W branży telekomunikacyjnych pojawiło się kilka rozwiązań do analizowania zmian, w tym:  

* Pochodzi metryki czterech podstawowych kategorii:
  * **Jednostka (na przykład subskrypcji)**. Udostępnić podstawowe informacje dotyczące subskrypcji i/lub klienta, który jest przedmiotem zmian.
  * **Działanie**. Uzyskaj wszystkie informacje możliwe użycie powiązanej jednostki, na przykład liczba logowania.
  * **Dział obsługi klienta**. Zbieranie informacji z dzienników Obsługa klienta wskazująca, czy subskrypcja ma problemy lub interakcji z obsługą klienta.
  * **Dane konkurencyjnych i biznesowych**. Uzyskaj możliwe informacje dotyczące klienta (na przykład może być niedostępna lub trudne do śledzenia).
* Użyj znaczenie wybór funkcji dysku. To oznacza, że modelu drzewa decyzyjnego boosted jest zawsze obietnicy podejście.  

Użyj kategorii tworzy wrażenie prostą *deterministyczne* podejścia, opartego na indeksy sformatowany na czynniki uzasadnione według kategorii, powinny wystarczyć do identyfikowania klientów na ryzyko zmian. Niestety Chociaż to pojęcie jest prawdopodobnie wiarygodne, jest FAŁSZ opis. Dzieje się tak że przenoszenie jest efekt danych czasowych i czynniki przyczyniające się do churn — znajdują się zwykle w stan przejściowy. Co skutkuje klientowi należy wziąć pod uwagę, pozostawiając dzisiaj mogą się różnić jutro i oczywiście będzie inny sześciu miesięcy od teraz. W związku z tym *prawdopodobieństwa* modelu jest to konieczne.  

To ważne obserwacji jest często pomijane w firmie zwykle preferuje podejście zorientowane na analizy biznesowej do analityka, przede wszystkim, ponieważ jest łatwiejsze sprzedaży i dopuszcza prostego automatyzacji.  

Jednak promise samoobsługi analizy przy użyciu usługi Machine Learning Studio jest czterech kategorii informacji klasyfikowanych przez oddział lub dział cenne źródło uczenia maszynowego o zmian.  

Kolejna możliwość atrakcyjnych dostępne w usłudze Azure Machine Learning jest możliwość dodawania niestandardowego modułu do repozytorium wstępnie zdefiniowanych modułów, które są już dostępne. Ta funkcja zasadniczo tworzy możliwość wybierz bibliotek i tworzenia szablonów dla pionowego rynkach. Jest ważne różnicą usługi Azure Machine Learning na rynek.  

Mamy nadzieję kontynuować w tym temacie w przyszłości, szczególnie związane z analizy danych big data.
  

## <a name="conclusion"></a>Podsumowanie
W tym dokumencie opisano za pośrednictwem podejście do rozwiązaniu problemu wspólnej przenoszenie klienta przy użyciu ogólnych framework. Firma Microsoft traktowane jako prototyp oceniania modeli i zaimplementowana przy użyciu usługi Azure Machine Learning. Na koniec mamy ocenić niezawodność i wydajność rozwiązania prototypu w odniesieniu do porównywalnych algorytmy sygnatury dostępu Współdzielonego.  

 

## <a name="references"></a>Dokumentacja
[1] analizy predykcyjnej: poza zarządzania informacjami prognoz, McKnight Zachodnia, lipca i sierpnia 2011 r. p.18 20.  

[2] Wikipedia artykuł: [dokładność i dokładność](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [WYSOKĄ DM 1.0: Przewodnik wyszukiwania danych krok po kroku](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing danych big Data: efektywniej kontaktować się z klientami i dysk wartość](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco churn — szablonu modelu](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) w [galerii Azure AI](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Dodatek
![][10]

*Rysunek 12: Migawki prezentacji na prototypie zmian*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png
