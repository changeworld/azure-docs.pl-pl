---
title: Tworzenie modelu analizy tonacji
titleSuffix: Azure Machine Learning Studio
description: Jak tworzenie modeli analizy tekstu w usłudze Azure Machine Learning Studio przy użyciu modułów wstępne przetwarzanie tekstu, N-gramy lub Tworzenie skrótu funkcji
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 08d62e7a6c9503d415fe144da57eee72ce3bfafd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636614"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Tworzenie modelu analizy tonacji w usłudze Azure Machine Learning Studio

Można użyć usługi Azure Machine Learning Studio, aby tworzyć i uruchamiać modele analizy tekstu. Te modele mogą pomóc rozwiązać, na przykład dokument klasyfikację lub tonacji problemy związane z analizą.

W eksperyment analizy tekstu jak zwykle:

1. Czyszczenie i wstępne przetwarzanie tekstu zestawu danych
2. Prowadzenie wektorów funkcji numerycznych wstępnie przetworzonego tekstu
3. Train model klasyfikacji lub regresji
4. Generowanie wyników i sprawdzania poprawności modelu
5. Wdrożyć model do produkcji

W tym samouczku dowiesz się, te kroki jako części omówimy model analiza tonacji przy użyciu przeglądów książki Amazon zestawu danych (zobacz ten dokument research "Biographies, Bollywood, nagle pola i mieszalni: Domeny dostosowywania klasyfikacji opinii"przez Blitzer Jan należy oznaczyć Dredze i Fernando Pereira; Skojarzenie odpowiednie obliczeniowej (ACL), 2007.) Ten zestaw danych składa się z przeglądu oceny (1 – 2 lub 4-5) i tekst w dowolnej postaci. Celem jest zapewnienie przewidywania wyników przeglądu: Niski (1 - 2) lub wysokiej (4-5).

Można znaleźć eksperymenty omówione w tym samouczku w galerii sztucznej Inteligencji platformy Azure:

[Przewidywanie przeglądy książki](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Przewidywanie przeglądy książki — eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: Czyszczenie i wstępne przetwarzanie tekstu zestawu danych
Zaczniemy eksperymentu, dzieląc wyniki przeglądu na kategorii niski i wysoki zasobników, aby sformułować problem dwuklasowych klasyfikacji. Używamy [edytować metadane](https://msdn.microsoft.com/library/azure/dn905986.aspx) i [wartości podzielonych na kategorie grupy](https://msdn.microsoft.com/library/azure/dn906014.aspx) modułów.

![Utwórz etykietę](./media/text-analytics-module-tutorial/create-label.png)

Następnie możemy wyczyścić tekstu przy użyciu języka [wstępne przetwarzanie tekstu](https://msdn.microsoft.com/library/azure/mt762915.aspx) modułu. Czyszczenie zmniejsza szumu w zestawie danych, odnaleźć najważniejsze funkcje i zwiększyć dokładność końcowego modelu. Usuwamy Stop-słowa - popularne wyrazy, takie jak "" lub "-" i cyfry, znaki specjalne, zduplikowane znaki, adresy e-mail i adresów URL. Możemy również Konwertuj tekst na małe litery, lemmatize wyrazy i wykrywa granic zdanie, które następnie są wskazywane przez "|||" symboli w tekście wstępnie przetworzone.

![Wstępne przetwarzanie tekstu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co zrobić, jeśli chcesz użyć niestandardowej listy Stop-słowa? Możesz przekazać go jako opcjonalne dane wejściowe. Można również użyć niestandardowego C# składni wyrażeń regularnych podciągów, a usuwanie wyrazów za część mowy: rzeczowniki poleceń oraz określeniem.

Po zakończeniu przetwarzania wstępnego, możemy podzielić dane na szkolenie i zestawów testów.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Prowadzenie wektorów funkcji numerycznych wstępnie przetworzonego tekstu
Aby utworzyć model danych tekstowych, zwykle trzeba przekonwertować tekst w dowolnej postaci wektorów funkcji liczbowych. W tym przykładzie używamy [wyodrębnić N-Gram funkcji z pliku tekstowego](https://msdn.microsoft.com/library/azure/mt762916.aspx) modułu do przekształcania danych tekstowych na taki format. Ten moduł przyjmuje kolumny rozdzielonych odstępu słów i oblicza słownika wyrazy lub N-gramy, słów, które pojawiają się w zestawie danych. Następnie zlicza zlicza liczbę razy każdy wyraz lub N-gram, pojawia się w każdym rekordzie, a następnie tworzy funkcję wektorów od tych. W tym samouczku ustawimy N-gram rozmiar 2, dzięki naszym wektorów funkcji obejmują pojedynczego słowa i kombinacji dwa kolejne wyrazy.

![Wyodrębnij N-gramy](./media/text-analytics-module-tutorial/extract-ngrams.png)

Możemy zastosować TF * zlicza wagi N-gram IDF (termin częstotliwość odwrotnej dokumentu częstotliwość). Takie podejście wprowadza wagę wyrazy, które są często wyświetlane w jednym rekordzie, ale są rzadko w obrębie całego zestawu danych. Inne opcje obejmują dane binarne, TF i programu graph o wadze.

Takie funkcje tekstowe często mają wysokie wymiarach. Na przykład jeśli Twoja korpus ma 100 000 unikatowych słów, obszaru funkcji musi wymiary 100 000 lub więcej Jeśli N-gramy są używane. Moduł funkcji N-Gram wyodrębnić zapewnia zestaw opcji, aby zmniejszyć wymiarach. Można wykluczyć wyrazy, które są krótkie lub długie, lub zbyt nietypowe lub zbyt częste znaczące przewidywanych wartości. W tym samouczku Wyłączamy N-gramy, które pojawiają się w mniej niż 5 rekordów lub więcej niż 80% rekordów.

Ponadto możesz użyć funkcji wyboru można wybrać tylko te funkcje, które są najbardziej skorelowane z docelowych prognozowania. Używamy chi funkcji wyboru cech, aby wybrać funkcje 1000. Słownictwa wybrane wyrazy lub N-gramy można wyświetlić, klikając odpowiednie dane wyjściowe wyodrębniania N-gramy modułu.

Jako alternatywne podejście do korzystania z funkcji wyodrębnić N-Gram umożliwia tworzenie skrótu funkcji modułu. Pamiętaj jednak, że [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/dn906018.aspx) nie ma możliwości wyboru funkcji kompilacji lub TF * IDF o wadze.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: Train model klasyfikacji lub regresji
Teraz tekst została przekształcona z kolumnami funkcji liczbowych. Zestaw danych nadal zawiera kolumny parametry z poprzednich etapów, dlatego używamy Wybieranie kolumn w zestawie danych, aby je wykluczyć.

Następnie używamy [regresji logistycznej Two-Class](https://msdn.microsoft.com/library/azure/dn905994.aspx) do prognozowania naszym celem: wysokiej lub niskiej przeglądu oceny. W tym momencie problem regularne klasyfikacji została przekształcona problem analizy tekstu. Narzędzia dostępne w usłudze Azure Machine Learning Studio można użyć w celu ulepszenia modelu. Na przykład możesz eksperymentować z różnych klasyfikatorów, aby dowiedzieć się, jak dokładne wyniki, które zapewniają lub użyć hiperparametrycznego strojenia w celu zwiększenia dokładności.

![Szkolenie i ocenianie](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: Generowanie wyników i sprawdzania poprawności modelu
Jak można sprawdzić uczonego modelu? Firma Microsoft wynik je względem zestawu danych testowych i ocenić dokładności. Jednak model przedstawiono słownictwa używanego w N-gramy i ich wagi z zestawu danych szkoleniowych. Dlatego firma Microsoft należy używać tego słownika i wagi tych podczas wyodrębniania funkcje danych testów, w przeciwieństwie tworzenia słownictwa nową. W związku z tym firma Microsoft Dodaj moduł wyodrębnić funkcji N-Gram do oceniania gałęzi eksperymentu, łączenie słownika danych wyjściowych z gałęzi szkolenia i ustaw tryb słownictwa używanego tylko do odczytu. Możemy również wyłączyć filtrowanie N-gramy przez częstotliwość przez ustawienie minimalna 1 wystąpienie i maksymalnie 100% i wyłącz wybór funkcji.

Po kolumny tekstowej w danych testowych została przekształcona z kolumnami funkcji liczbowych, możemy wyłączyć kolumn ciąg z poprzednich etapów podobnie jak w gałęzi szkolenia. Następnie moduł Score Model do przewidywania przyszłych zdarzeń i używamy modułu Evaluate Model do oceny dokładności.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5. Wdrożyć model do produkcji
Model jest już prawie gotowe do wdrożenia do środowiska produkcyjnego. Po wdrożeniu jako usługę sieci web go przyjmuje tekst w dowolnej postaci ciągu jako dane wejściowe i powrócić prognozowania "wysoka" lub "low". Używa nauczony słownictwa N-gram do przekształcania tekstu do funkcji i regresji logistycznej uczonego modelu do prognozowania na podstawie tych funkcji. 

Aby skonfigurować eksperyment predykcyjny, najpierw oszczędzamy słownictwa N-gram jako zestaw danych i modelu regresji logistycznej uczony z gałęzi szkolenia doświadczenia. Następnie zapisz eksperymentu przy użyciu "Zapisz jako", aby utworzyć wykres eksperymentu na eksperyment predykcyjny. Możemy usunąć moduł dzielenia danych i gałąź szkolenia z eksperymentu. Następnie łączymy uprzednio zapisanego N-gram słownictwa i model do wyodrębnienia N-Gram funkcji i modułów Score Model, odpowiednio. Możemy również usunąć modułu Evaluate Model.

Firma Microsoft Wybieranie kolumn do wstawienia w module zestawu danych przed modułu wstępne przetwarzanie tekstu, aby usunąć tę kolumnę etykiety i usuń zaznaczenie opcji "Dołącz wynik kolumnę do zestawu danych" w Module wynik. Dzięki temu usługi sieci web nie żąda etykieta, którą próbować przewidywać i nie nie echo dane wejściowe funkcji w odpowiedzi.

![Eksperyment predykcyjny](./media/text-analytics-module-tutorial/predictive-text.png)

Teraz mamy eksperymentu, które mogą być publikowane jako usługi sieci web i wywoływany przy użyciu odpowiedzi na żądanie lub partii wykonywania interfejsów API.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o moduły analizy tekstu z [dokumentacji MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

