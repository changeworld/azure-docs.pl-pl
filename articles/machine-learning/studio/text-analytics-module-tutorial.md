---
title: Tworzenie tekstu modele analizy w usłudze Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Jak utworzyć modele analizy tekstu w usłudze Azure Machine Learning Studio przy użyciu modułów przetwarzania wstępnego tekstu, N-g lub Tworzenie skrótu funkcji
services: machine-learning
documentationcenter: ''
author: rastala
ms.author: roastala
manager: cgronlun
editor: ''
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: a9d4ee9015b5781301dcc9c0fe4f8944dbf244e8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835886"
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Tworzenie modeli analizy tekstu w usłudze Azure Machine Learning Studio
Uczenie maszynowe Azure umożliwia tworzenie i operacjonalizuj modele analizy tekstu. Te modele mogą ułatwić rozwiązywanie, na przykład problemy analizy dokumentu klasyfikacji i wskaźniki nastrojów klientów.

W eksperymencie analytics tekst ma zazwyczaj:

1. Czyszczenie i wstępnie Przetwórz tekst zestawu danych
2. Wyodrębnij wektory funkcji numerycznych z wstępnie przetworzonych tekstu
3. Train model klasyfikacji lub regresji
4. Wynik i sprawdzanie poprawności modelu
5. Wdróż model do środowiska produkcyjnego

Z tego samouczka, dowiesz się, te kroki jako możemy przeprowadzenie modelu analizy wskaźniki nastrojów klientów przy użyciu zestawu Amazon książki przeglądy danych (zobacz ten dokument research "Biographies, Bollywood, wysięgnik pola i mieszalni: dostosowanie domeny klasyfikacji wskaźniki nastrojów klientów" Blitzer Jan, oznacz Dredze i Fernando Pereira; Skojarzenie Linguistics obliczeniową (ACL), 2007.) Ten zestaw danych składa się z przeglądu wyniki (1-2 lub 4-5) i dowolnego tekstu. Celem jest przewidywanie wyników przeglądu: Niski (1 - 2) lub high (4 – 5).

Można znaleźć eksperymenty omówione w tym samouczku w galerii Azure AI:

[Przewidywanie książki przeglądów](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Przewidywanie przeglądami Book - eksperyment predykcyjny](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: Clean i wstępnie Przetwórz tekst zestawu danych
Zaczniemy eksperyment przez podzielenie Przejrzyj wyniki w kategorii zasobników duże i małe, aby sformułować ten problem, co klasa dwa klasyfikacji. Używamy [edytowanie metadanych](https://msdn.microsoft.com/library/azure/dn905986.aspx) i [wartości kategorii grupy](https://msdn.microsoft.com/library/azure/dn906014.aspx) modułów.

![Tworzenie etykiety](./media/text-analytics-module-tutorial/create-label.png)

Następnie możemy wyczyścić tekst używając [wstępnie Przetwórz tekst](https://msdn.microsoft.com/library/azure/mt762915.aspx) modułu. Czyszczenie zmniejsza szumu w zestawie danych, pozwalają znaleźć najważniejsze funkcje i zwiększyć dokładność ostatecznego modelu. Usuwamy odrzucanych - popularnych wyrazów, takie jak "" lub "-" i cyfry, znaki specjalne zduplikowanych znaków, adresy e-mail i adresów URL. Możemy również Konwertuj tekst na małe litery, lemmatize wyrazy, a także wykryć granice zdania, które następnie są wskazywane przez "|||" symboli w tekście wstępnie przetworzonych.

![Przetwarzanie wstępne tekstu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co zrobić, jeśli chcesz użyć niestandardowej listy odrzucanych? Można przekazać go jako dane wejściowe opcjonalne. Można również użyć niestandardowej C# składni wyrażenia regularnego, aby zamienić podciągów i usunąć słowa przez część mowy: rzeczowniki poleceń i określeniem.

Po zakończeniu przetwarzania wstępnego możemy podzielić dane na pociągu i testowania zestawów.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Wyodrębniania wstępnie przetworzonych tekst wektory funkcji numerycznych
Aby utworzyć model danych tekstowych, zwykle trzeba Konwersja tekstu dowolnych funkcji numerycznych wektory. W tym przykładzie używamy [wyodrębnić funkcji N-gramów z tekstu](https://msdn.microsoft.com/library/azure/mt762916.aspx) modułu do przekształcania danych tekstowych taki format. Ten moduł ma kolumnę rozdzielone odstępem słów i oblicza słownika wyrazy lub N-gramów wyrazy, które pojawiają się w zestawie danych. Następnie liczy zlicza, ile razy każdego programu word, lub N-gramów, pojawi się we wszystkich rekordach i tworzy wektory funkcji w porównaniu. W tym samouczku firma Microsoft Ustaw rozmiar N-gramów 2, więc naszych wektory funkcji to pojedyncze wyrazy i kombinacje dwóch kolejnych wyrazów.

![Wyodrębnij N-gramów](./media/text-analytics-module-tutorial/extract-ngrams.png)

Trwa stosowanie TF * liczby wagi N-gramów IDF (określenie częstotliwości odwrotny dokumentu częstotliwość). Takie podejście dodaje wagi słowa, które często pojawiają się w jednym rekordzie, ale są rzadko przez cały zestaw danych. Inne opcje obejmują TF, binary oraz wykresów o wadze.

Takie funkcje tekstu często mają wysokie wymiary. Na przykład jeśli Twoje Boże ma 100 000 unikatowych słów, obszaru funkcji miałyby wymiary 100 000 lub więcej Jeśli N-g są używane. Moduł wyodrębnić funkcji N-gramów zapewnia zestaw opcji, aby zmniejszyć wymiary. Można wykluczyć słowa, które są krótka lub długi, lub zbyt rzadko lub zbyt częste znaczących wartości predykcyjnej. W tym samouczku Wyłączamy N-gramów występujące w mniej niż 5 rekordów lub więcej niż 80% rekordów.

Ponadto można użyć funkcji wyboru można wybrać tylko te funkcje, które są najbardziej skorelowane z docelowym prognozowania. Używamy wybór funkcji chi aby wybrać funkcje 1000. Słownictwa wybranego słowa lub N-gramów można wyświetlić, klikając prawym dane wyjściowe wyodrębniania N-gramów modułu.

Jako alternatywne podejście przy użyciu funkcji N-gramów wyodrębnić można użyć funkcji wyznaczania wartości skrótu modułu. Pamiętaj jednak, że [Tworzenie skrótu funkcji](https://msdn.microsoft.com/library/azure/dn906018.aspx) nie ma możliwości wyboru funkcji kompilacji lub TF * IDF o wadze.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: Train model klasyfikacji lub regresji
Teraz tekst została przekształcona do kolumn liczbowych funkcji. Zestaw danych nadal zawiera ciąg kolumny z poprzednich etapów, więc używamy Wybieranie kolumn w zestawie danych, aby wykluczyć je.

Następnie użyj [Regresja logistyczna Two-Class](https://msdn.microsoft.com/library/azure/dn905994.aspx) do prognozowania celem: wynik przeglądu wysoki lub niski. W tym momencie tekst problem analytics została przekształcona problem regularne klasyfikacji. Można użyć narzędzi dostępnych w usłudze Azure Machine Learning do zwiększenia modelu. Na przykład możesz wypróbować różne klasyfikatory, aby dowiedzieć się, jak dokładne wyniki dają one lub użyć hyperparameter strojenia w celu zwiększenia dokładności.

![Pociągu i wynik](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: Wynik i sprawdzanie poprawności modelu
Jak można sprawdzić trenowanego modelu? Firma Microsoft wynik go przed zestawu danych testowych i ocena dokładności. Jednak model pokazaliśmy słownictwa N-g i ich wagi z zestawu danych szkoleniowych. W związku z tym powinniśmy skorzystać w tym słownictwa i wagi tych podczas wyodrębniania funkcji z danych testowych, w przeciwieństwie do tworzenia słownictwa od nowa. W związku z tym możemy dodać moduł wyodrębnić funkcji N-gramów do oceniania gałęzi doświadczenia, Połącz słownika danych wyjściowych z gałęzi szkolenia i ustaw tryb słownictwa tylko do odczytu. Możemy również wyłączyć filtrowanie N-gramów przez częstotliwość przez ustawienie minimalna 1 wystąpienia i maksymalnie 100% i wyłącz wybór funkcji.

Po kolumnie tekstu w danych testowych została przekształcona do kolumn liczbowych funkcji, Wyłączamy kolumnach typu ciąg z poprzednich etapów podobnie jak w gałęzi szkolenia. Będziemy używać modułu Score Model do tworzenia prognoz i modułu Evaluate Model do oceny zgodności.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5: Wdrożenie modelu do środowiska produkcyjnego
Model jest już prawie gotowe do wdrożenia w środowisku produkcyjnym. Po wdrożeniu jako usługi sieci web go przyjmuje ciąg tekstowy dowolnych jako dane wejściowe, a zwracany prediction "high" lub "low". Zapamiętanych słownictwa N-gramów jest używany do transformacji tekstu do funkcji i Regresja logistyczna uczonego modelu do prognozowania na podstawie tych funkcji. 

Aby skonfigurować eksperyment predykcyjny, firma Microsoft najpierw zapisać słownictwa N-gramów jako zestawu danych oraz model przeszkolone Regresja logistyczna gałęzi szkolenia eksperymentu. Następnie zapisz eksperymentów przy użyciu "Zapisz jako" do utworzenia wykresu eksperyment predykcyjny eksperymentu. Możemy usunąć moduł podziału danych i gałęzi szkolenia z eksperymentu. Następnie nawiązać wcześniej zapisany N-gramów słownictwa i model wyodrębnić N-gramów funkcji i modułów Score Model odpowiednio. Możemy również usunąć modułu Evaluate Model.

Firma Microsoft wybierz kolumny do wstawienia w module zestawu danych przed modułu wstępnie Przetwórz tekst, aby usunąć tę kolumnę etykiety i usuń zaznaczenie opcji "Dołącz wynik kolumny do zestawu danych" w Module wynik. Dzięki temu usługi sieci web nie żąda etykietę, która próbuje go do prognozowania i nie nie echo dane wejściowe funkcji w odpowiedzi.

![Eksperyment predykcyjny](./media/text-analytics-module-tutorial/predictive-text.png)

Mamy teraz eksperymentu, które mogą być publikowane jako usługę sieci web i wywoływane przy użyciu żądanie odpowiedź lub wsadowe wykonywanie interfejsów API.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o modułów analytics tekst z [dokumentacji MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

