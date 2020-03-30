---
title: Przypadek użycia analizy tonacji
titleSuffix: ML Studio (classic) - Azure
description: Jak tworzyć modele analizy tekstu w usłudze Azure Machine Learning Studio (klasyczne) przy użyciu modułów do wstępnego przetwarzania tekstu, N-gramów lub mieszania funkcji
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217878"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Tworzenie modelu analizy tonacji w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Za pomocą usługi Azure Machine Learning Studio (klasycznej) można tworzyć i operacjonalizacji modeli analizy tekstu. Te modele mogą pomóc w rozwiązaniu, na przykład, klasyfikacji dokumentów lub problemów z analizą tonacji.

W eksperymencie analizy tekstu zazwyczaj:

1. Zestaw danych tekstowych Do czyszczenia i przetwarzania wstępnego
2. Wyodrębnianie wektorów operacji liczbowych z wstępnie przetworzonego tekstu
3. Model klasyfikacji lub regresji pociągu
4. Oceniaj i weryfikuj model
5. Wdrażanie modelu w produkcji

W tym samouczku, dowiedzieć się tych kroków, jak przejść przez model analizy nastrojów za pomocą Amazon Book Reviews zestawu danych (patrz ten artykuł badawczy "Biografie, Bollywood, Boom-boxy i Blendery: Domain Adaptation for Sentiment Classification" John Blitzer, Mark Dredze i Fernando Pereira; Association of Computational Linguistics (ACL), 2007.) Ten zestaw danych składa się z wyników recenzji (1-2 lub 4-5) i tekstu w postaci swobodnej. Celem jest przewidywanie wyniku recenzji: niski (1-2) lub wysoki (4-5).

Eksperymenty opisane w tym samouczku można znaleźć w galerii sztucznej inteligencji platformy Azure:

[Przewiduj recenzje książek](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Przewiduj recenzje książek - Eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: Czyszczenie i wstępne przetwarzanie zestawu danych tekstowych
Rozpoczynamy eksperyment, dzieląc wyniki recenzji na kategoryczne wiadra o niskich i wysokich poziomach, aby sformułować problem jako klasyfikację dwuklasową. Używamy modułów [Edytuj metadane](https://msdn.microsoft.com/library/azure/dn905986.aspx) i [Grupowe wartości kategoryczne.](https://msdn.microsoft.com/library/azure/dn906014.aspx)

![Utwórz etykietę](./media/text-analytics-module-tutorial/create-label.png)

Następnie czyścimy tekst za pomocą modułu [Tekst preprocesu.](https://msdn.microsoft.com/library/azure/mt762915.aspx) Czyszczenie zmniejsza szumy w zestawie danych, pomaga znaleźć najważniejsze funkcje i poprawia dokładność ostatecznego modelu. Usuwamy hasła stopwory - typowe słowa, takie jak "the" lub "a" - i liczby, znaki specjalne, zduplikowane znaki, adresy e-mail i adresy URL. Konwertujemy również tekst na małe litery, lemmatyzujemy wyrazy i wykrywamy granice zdań, które są następnie oznaczone symbolem "|||" w wstępnie przetworzonym tekście.

![Wstępne przetwarzanie tekstu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co zrobić, jeśli chcesz użyć niestandardowej listy stopwords? Można przekazać go jako opcjonalne wejście. Można również użyć niestandardowego wyrażenia regularnego składni C#, aby zastąpić podciągi i usunąć wyrazy według części mowy: rzeczowniki, zlecenia lub przymiotniki.

Po zakończeniu przetwarzania wstępnego dzielimy dane na zestawy pociągów i testów.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Wyodrębnianie wektorów operacji liczbowych z wstępnie przetworzonego tekstu
Aby utworzyć model danych tekstowych, zazwyczaj trzeba przekonwertować tekst swobodny na wektory operacji liczbowych. W tym przykładzie używamy [funkcji Wyodrębnij N-Gram z modułu tekstowego,](https://msdn.microsoft.com/library/azure/mt762916.aspx) aby przekształcić dane tekstowe do takiego formatu. Ten moduł przyjmuje kolumnę wyrazów oddzielonych odstępami i oblicza słownik słów lub N-gramów słów, które pojawiają się w zestawie danych. Następnie zlicza, ile razy każde słowo lub N-gram pojawia się w każdym rekordzie i tworzy wektory funkcji z tych liczników. W tym samouczku ustawiliśmy rozmiar N-grama na 2, więc nasze wektory funkcji zawierają pojedyncze słowa i kombinacje dwóch kolejnych słów.

![Ekstrakt N-gramów](./media/text-analytics-module-tutorial/extract-ngrams.png)

Stosujemy tf * IDF (Term Frequency Inverse Document Frequency Frequency) wagi do N-gram liczy. Takie podejście dodaje wagę słów, które pojawiają się często w jednym rekordzie, ale są rzadkie w całym zestawie danych. Inne opcje obejmują binarne, TF i ważenie wykresu.

Takie funkcje tekstu często mają wysoką wymiarowość. Jeśli na przykład korpus ma 100 000 unikatowych słów, przestrzeń obiektowa będzie miała 100 000 wymiarów lub więcej, jeśli używane są N-gramy. Moduł Wyodrębnij funkcje N-Gram daje zestaw opcji zmniejszających wymiarowość. Można wykluczyć wyrazy, które są krótkie lub długie, zbyt rzadkie lub zbyt częste, aby miały znaczącą wartość predykcyjną. W tym samouczku wykluczamy N-gramy, które pojawiają się w mniej niż 5 rekordach lub w ponad 80% rekordów.

Ponadto można użyć wyboru funkcji, aby wybrać tylko te funkcje, które są najbardziej skorelowane z celem przewidywania. Używamy wyboru funkcji Chi-Squared, aby wybrać 1000 funkcji. Można wyświetlić słownictwo wybranych słów lub N-gramów, klikając prawe dane wyjściowe modułu Wyodrębnij N-gramów.

Jako alternatywne podejście do korzystania z funkcji Wyodrębnij N-Gram, można użyć modułu hashing funkcji. Należy jednak pamiętać, że [funkcja Hashing](https://msdn.microsoft.com/library/azure/dn906018.aspx) nie ma wbudowanych funkcji wyboru funkcji lub TF * Ważenia IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: Model klasyfikacji lub regresji pociągu
Teraz tekst został przekształcony w numeryczne kolumny obiektowe. Zestaw danych nadal zawiera kolumny ciągów z poprzednich etapów, więc używamy wybierz kolumny w zestawie danych, aby je wykluczyć.

Następnie używamy [regresji logistycznej dwuklasowej](https://msdn.microsoft.com/library/azure/dn905994.aspx) do przewidywania naszego celu: wysoki lub niski wynik przeglądu. W tym momencie problem analizy tekstu został przekształcony w problem regularnej klasyfikacji. Można użyć narzędzi dostępnych w usłudze Azure Machine Learning Studio (klasyczny) w celu ulepszenia modelu. Na przykład można eksperymentować z różnymi klasyfikatorami, aby dowiedzieć się, jak dokładne wyniki dają, lub użyć dostrajania hiperparametryczne, aby poprawić dokładność.

![Pociąg i wynik](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: Ocena i sprawdź poprawność modelu
Jak sprawdzić poprawność przeszkolonego modelu? Oceniamy go względem zestawu danych testowych i oceniamy dokładność. Jednak model nauczył się słownictwa N-gramów i ich wagi z zestawu danych szkoleniowych. Dlatego powinniśmy używać tego słownictwa i tych wag podczas wyodrębniania funkcji z danych testowych, w przeciwieństwie do tworzenia słownictwa na nowo. W związku z tym dodajemy moduł Wyodrębnij funkcje N-Gram do gałęzi oceniania eksperymentu, łączymy słownictwo wyjściowe z gałęzi szkoleniowej i ustawiamy tryb słownictwa na tylko do odczytu. Wyłączamy również filtrowanie N-gramów według częstotliwości, ustawiając minimalne na 1 wystąpienie i maksymalnie do 100%, i wyłączamy wybór funkcji.

Po przekształceniu kolumny tekstowej w danych testowych na numeryczne kolumny elementów wykluczamy kolumny ciągów z poprzednich etapów, takich jak w gałęzi szkoleniowej. Następnie używamy score model modułu do prognozowania i oceny modelu modułu do oceny dokładności.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5: Wdrażanie modelu w produkcji
Model jest prawie gotowy do wdrożenia w produkcji. Po wdrożeniu jako usługa sieci web, przyjmuje ciąg tekstowy w postaci swobodnej jako dane wejściowe i zwraca prognozę "wysoki" lub "niski". Używa uczętego słownictwa N-gram do przekształcania tekstu do funkcji i przeszkolony model regresji logistycznej, aby przewidzieć z tych funkcji. 

Aby skonfigurować eksperyment predykcyjny, najpierw zapisujemy słownictwo N-gram jako zestaw danych i wyszkolony model regresji logistycznej z gałęzi szkoleniowej eksperymentu. Następnie zapisujemy eksperyment przy użyciu "Zapisz jako", aby utworzyć wykres eksperymentu dla eksperymentu predykcyjnego. Usuwamy moduł Podziel dane i gałąź szkoleniową z eksperymentu. Następnie łączymy wcześniej zapisane słownictwo N-gram i model, aby wyodrębnić funkcje N-Gram i moduły modelu wynik, odpowiednio. Usuwamy również moduł Oceń model.

Wstawiamy Wybierz kolumny w module zestawu danych przed modułem tekstu preprocesu, aby usunąć kolumnę etykiety, i odznacz opcję "Dołącz kolumnę wyników do zestawu danych" w module wyników. W ten sposób usługa sieci web nie żąda etykiety, którą próbuje przewidzieć, i nie odezuje funkcji wejściowych w odpowiedzi.

![Eksperyment predykcyjny](./media/text-analytics-module-tutorial/predictive-text.png)

Teraz mamy eksperyment, który może być opublikowany jako usługa sieci web i wywoływane przy użyciu żądania odpowiedzi lub wsadowego wykonywania interfejsów API.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o modułach analizy tekstu z [dokumentacji MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

