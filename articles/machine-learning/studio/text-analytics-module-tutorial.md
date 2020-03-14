---
title: Przypadek użycia analizy tonacji
titleSuffix: ML Studio (classic) - Azure
description: Jak utworzyć modele analizy tekstu w Azure Machine Learning Studio (klasyczny) przy użyciu modułów do przetwarzania wstępnego tekstu, N-gramów lub wyznaczania wartości skrótu funkcji
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217878"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Tworzenie modelu analizy tonacji w Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Możesz użyć Azure Machine Learning Studio (klasyczny) do kompilowania i operacjonalizować modeli analizy tekstu. Te modele mogą pomóc rozwiązać, na przykład dokument klasyfikację lub tonacji problemy związane z analizą.

W eksperyment analizy tekstu jak zwykle:

1. Czyszczenie i wstępne przetwarzanie tekstu zestawu danych
2. Prowadzenie wektorów funkcji numerycznych wstępnie przetworzonego tekstu
3. Train model klasyfikacji lub regresji
4. Generowanie wyników i sprawdzania poprawności modelu
5. Wdrożyć model do produkcji

W ramach tego samouczka nauczysz się, jak przeprowadzimy przez model analizy tonacji, korzystając z zestawu danych usługi Amazon Book Reviews (zobacz ten dokument z badaniami "Biographies, Bollywood, wysięgnik-pudłs and Blends": adaptacja domeny dla klasyfikacji tonacji "przez Jan Blitzer, Mark Dredze i Fernando Pereira; Skojarzenie języków obliczeniowych (ACL), 2007.) Ten zestaw danych składa się z wyników przeglądu (1-2 lub 4-5) i swobodnego tekstu. Celem jest zapewnienie przewidywania wyników przeglądu: Niski (1 - 2) lub wysokiej (4-5).

Można znaleźć eksperymenty omówione w tym samouczku w galerii sztucznej Inteligencji platformy Azure:

[Prognozowanie przeglądów książek](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Prognozowanie przeglądów książek — eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: Czyszczenie i wstępne przetwarzanie tekstu zestawu danych
Zaczniemy eksperymentu, dzieląc wyniki przeglądu na kategorii niski i wysoki zasobników, aby sformułować problem dwuklasowych klasyfikacji. Korzystamy z modułów [Edytuj metadane](https://msdn.microsoft.com/library/azure/dn905986.aspx) i [Grupuj kategorii wartości](https://msdn.microsoft.com/library/azure/dn906014.aspx) .

![Utwórz etykietę](./media/text-analytics-module-tutorial/create-label.png)

Następnie wyczyśćmy tekst przy użyciu [wstępnie przetworzonego modułu tekstu](https://msdn.microsoft.com/library/azure/mt762915.aspx) . Czyszczenie zmniejsza szumu w zestawie danych, odnaleźć najważniejsze funkcje i zwiększyć dokładność końcowego modelu. Usuwamy Stop-słowa - popularne wyrazy, takie jak "" lub "-" i cyfry, znaki specjalne, zduplikowane znaki, adresy e-mail i adresów URL. Możemy również Konwertuj tekst na małe litery, lemmatize wyrazy i wykrywa granic zdanie, które następnie są wskazywane przez "|||" symboli w tekście wstępnie przetworzone.

![Wstępne przetwarzanie tekstu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co zrobić, jeśli chcesz użyć niestandardowej listy Stop-słowa? Możesz przekazać go jako opcjonalne dane wejściowe. Można również użyć niestandardowego C# składni wyrażeń regularnych podciągów, a usuwanie wyrazów za część mowy: rzeczowniki poleceń oraz określeniem.

Po zakończeniu przetwarzania wstępnego, możemy podzielić dane na szkolenie i zestawów testów.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Wyodrębniania tekstu wstępnie przetworzonego wektorów funkcji numerycznych
Aby utworzyć model danych tekstowych, zwykle trzeba przekonwertować tekst w dowolnej postaci wektorów funkcji liczbowych. W tym przykładzie używamy [funkcji Wyodrębnij funkcje N-gramów z modułu tekstowego](https://msdn.microsoft.com/library/azure/mt762916.aspx) do przekształcania danych tekstowych w taki format. Ten moduł przyjmuje kolumny rozdzielonych odstępu słów i oblicza słownika wyrazy lub N-gramy, słów, które pojawiają się w zestawie danych. Następnie zlicza zlicza liczbę razy każdy wyraz lub N-gram, pojawia się w każdym rekordzie, a następnie tworzy funkcję wektorów od tych. W tym samouczku ustawimy N-gram rozmiar 2, dzięki naszym wektorów funkcji obejmują pojedynczego słowa i kombinacji dwa kolejne wyrazy.

![Wyodrębnij N-gramy](./media/text-analytics-module-tutorial/extract-ngrams.png)

Możemy zastosować TF * zlicza wagi N-gram IDF (termin częstotliwość odwrotnej dokumentu częstotliwość). Takie podejście wprowadza wagę wyrazy, które są często wyświetlane w jednym rekordzie, ale są rzadko w obrębie całego zestawu danych. Inne opcje obejmują dane binarne, TF i programu graph o wadze.

Takie funkcje tekstowe często mają wysokie wymiarach. Na przykład jeśli Twoja korpus ma 100 000 unikatowych słów, obszaru funkcji musi wymiary 100 000 lub więcej Jeśli N-gramy są używane. Moduł funkcji N-Gram wyodrębnić zapewnia zestaw opcji, aby zmniejszyć wymiarach. Można wykluczyć wyrazy, które są krótkie lub długie, lub zbyt nietypowe lub zbyt częste znaczące przewidywanych wartości. W tym samouczku Wyłączamy N-gramy, które pojawiają się w mniej niż 5 rekordów lub więcej niż 80% rekordów.

Ponadto możesz użyć funkcji wyboru można wybrać tylko te funkcje, które są najbardziej skorelowane z docelowych prognozowania. Używamy chi funkcji wyboru cech, aby wybrać funkcje 1000. Słownictwa wybrane wyrazy lub N-gramy można wyświetlić, klikając odpowiednie dane wyjściowe wyodrębniania N-gramy modułu.

Jako alternatywne podejście do korzystania z funkcji wyodrębnić N-Gram umożliwia tworzenie skrótu funkcji modułu. Należy pamiętać, że [Funkcja tworzenia skrótów](https://msdn.microsoft.com/library/azure/dn906018.aspx) nie ma możliwości wyboru funkcji kompilacji ani TF * IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: Train model klasyfikacji lub regresji
Teraz tekst została przekształcona z kolumnami funkcji liczbowych. Zestaw danych nadal zawiera kolumny parametry z poprzednich etapów, dlatego używamy Wybieranie kolumn w zestawie danych, aby je wykluczyć.

Następnie korzystamy z [dwuklasowej regresji logistycznej](https://msdn.microsoft.com/library/azure/dn905994.aspx) , aby przewidzieć nasz cel: wysoki lub niski wynik przeglądu. W tym momencie problem regularne klasyfikacji została przekształcona problem analizy tekstu. Aby ulepszyć model, można użyć narzędzi dostępnych w Azure Machine Learning Studio (klasyczne). Na przykład możesz eksperymentować z różnych klasyfikatorów, aby dowiedzieć się, jak dokładne wyniki, które zapewniają lub użyć hiperparametrycznego strojenia w celu zwiększenia dokładności.

![Szkolenie i ocenianie](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: Generowanie wyników i sprawdzania poprawności modelu
Jak można sprawdzić uczonego modelu? Firma Microsoft wynik je względem zestawu danych testowych i ocenić dokładności. Jednak model przedstawiono słownictwa używanego w N-gramy i ich wagi z zestawu danych szkoleniowych. Dlatego firma Microsoft należy używać tego słownika i wagi tych podczas wyodrębniania funkcje danych testów, w przeciwieństwie tworzenia słownictwa nową. W związku z tym firma Microsoft Dodaj moduł wyodrębnić funkcji N-Gram do oceniania gałęzi eksperymentu, łączenie słownika danych wyjściowych z gałęzi szkolenia i ustaw tryb słownictwa używanego tylko do odczytu. Możemy również wyłączyć filtrowanie N-gramy przez częstotliwość przez ustawienie minimalna 1 wystąpienie i maksymalnie 100% i wyłącz wybór funkcji.

Po kolumny tekstowej w danych testowych została przekształcona z kolumnami funkcji liczbowych, możemy wyłączyć kolumn ciąg z poprzednich etapów podobnie jak w gałęzi szkolenia. Następnie moduł Score Model do przewidywania przyszłych zdarzeń i używamy modułu Evaluate Model do oceny dokładności.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5: Wdrożyć model do produkcji
Model jest już prawie gotowe do wdrożenia do środowiska produkcyjnego. Po wdrożeniu jako usługę sieci web go przyjmuje tekst w dowolnej postaci ciągu jako dane wejściowe i powrócić prognozowania "wysoka" lub "low". Używa nauczony słownictwa N-gram do przekształcania tekstu do funkcji i regresji logistycznej uczonego modelu do prognozowania na podstawie tych funkcji. 

Aby skonfigurować eksperyment predykcyjny, najpierw oszczędzamy słownictwa N-gram jako zestaw danych i modelu regresji logistycznej uczony z gałęzi szkolenia doświadczenia. Następnie zapisz eksperymentu przy użyciu "Zapisz jako", aby utworzyć wykres eksperymentu na eksperyment predykcyjny. Możemy usunąć moduł dzielenia danych i gałąź szkolenia z eksperymentu. Następnie łączymy uprzednio zapisanego N-gram słownictwa i model do wyodrębnienia N-Gram funkcji i modułów Score Model, odpowiednio. Możemy również usunąć modułu Evaluate Model.

Firma Microsoft Wybieranie kolumn do wstawienia w module zestawu danych przed modułu wstępne przetwarzanie tekstu, aby usunąć tę kolumnę etykiety i usuń zaznaczenie opcji "Dołącz wynik kolumnę do zestawu danych" w Module wynik. Dzięki temu usługi sieci web nie żąda etykieta, którą próbować przewidywać i nie nie echo dane wejściowe funkcji w odpowiedzi.

![Eksperyment predykcyjny](./media/text-analytics-module-tutorial/predictive-text.png)

Teraz mamy eksperymentu, które mogą być publikowane jako usługi sieci web i wywoływany przy użyciu odpowiedzi na żądanie lub partii wykonywania interfejsów API.

## <a name="next-steps"></a>Następne kroki
Informacje o modułach analizy tekstu z [dokumentacji MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

