---
title: Dokumentowanie analiza kolekcji — Azure | Dokumentacja firmy Microsoft
description: Sposób podsumowywania i analizowanie duży zbiór dokumentów, łącznie z technik, takich jak learning frazy, tematu, modelowanie i analizy modelu tematu przy użyciu aplikacji Azure ML Workbench.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 29f493449d48df26919a98452fa7f832d653d45e
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861786"
---
# <a name="document-collection-analysis"></a>Analiza kolekcji dokumentów

W tym scenariuszu pokazano, jak sumować i analizować duży zbiór dokumentów, łącznie z technik, takich jak learning frazy, tematu, modelowanie i analizy modelu tematu przy użyciu aplikacji Azure ML Workbench. Usługa Azure Machine Learning Workbench umożliwia łatwe skalowanie dla kolekcji bardzo dużych dokumentów i udostępnia mechanizmy do nauczenia i dostrajania modeli w ramach różnych konteksty obliczeniowe, od lokalnego obliczeń do maszyn wirtualnych do nauki o danych do klastra Spark. Łatwe projektowanie jest oferowana w ramach notesów programu Jupyter w usłudze Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii

Publicznego repozytorium GitHub, w tym scenariuszu rzeczywistych zawiera wszystkie materiały, w tym przykłady kodu, potrzebne w tym przykładzie:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Przegląd

Z dużą ilością danych (dane tekstowe szczególnie niestrukturalnych) zebrane codziennie znaczące wyzwane jest organizowania, wyszukiwanie i zrozumieć ogromnych ilości tekstów. Ten scenariusz analiza kolekcji dokumentów pokazuje efektywne i automatyczne end-to-end przepływu pracy do analizowania dużych dokumentów kolekcji i włączenie zadania podrzędne przetwarzaniu języka naturalnego.

Dostępne są następujące kluczowe elementy, które są dostarczane przez ten scenariusz:

1. Nauki najważniejsze fraza słowa wielu z dokumentów.

1. Odnajdywanie podstawowych tematów przedstawionych w kolekcji dokumentów.

1. Reprezentującymi dokumenty przez funkcję dystrybucji istotna.

1. Przedstawienie metody organizowania, wyszukiwanie i podsumowywanie dokumentów na podstawie zawartości istotna.

Metod przedstawionych w tym scenariuszu można włączyć szereg krytycznych obciążeń przemysłowych, takich jak odnajdywania anomalii trendów tematów, podsumowania kolekcji dokumentu i podobne wyszukiwania dokumentu. Mogą być stosowane dla wielu typów analizy dokumentu, takich jak przestrzegania dla instytucji rządowych, najnowsze, recenzje produktów, opinie klientów i artykuły badań naukowych.

To algorytmów uczenia maszynowego technik/używaną w tym scenariuszu:

1. Przetwarzanie tekstu i czyszczenia

1. Learning frazy

1. Modelowanie tematu

1. Podsumowanie korpus

1. Istotna trendów i anomalii

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

* Upewnij się, że poprawnie zainstalowano [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) postępując zgodnie z [Instalowanie i Tworzenie szybkiego startu](../service/quickstart-installation.md).

* W tym przykładzie, może uruchomić w dowolnym kontekście obliczeniowym. Jednak zaleca się ją uruchomić na maszynie wielordzeniowych z co najmniej 16GB pamięci RAM i miejsca na dysku 5GB.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwieranie usługi Azure Machine Learning Workbench
2.  Na **projektów** kliknij **+** zalogować się i wybrać **nowy projekt**
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz "Analiza kolekcji dokumentów" i wybierz szablon
5.  Kliknij przycisk **Utwórz**

## <a name="data-description"></a>Opis danych

Zestaw danych, w tym scenariuszu zawiera tekst podsumowania i skojarzone metadane dla każdej akcji legislacyjne podjęte przez Kongres USA. Dane są zbierane z [GovTrack.us](https://www.govtrack.us/), który śledzi działania Kongres Stanów Zjednoczonych i pomaga Amerykanów uczestniczyć w ich krajowych legislacyjne procesu. Zbiorcze danych można pobrać za pośrednictwem [ten link](https://www.govtrack.us/data/congress/) przy użyciu skryptu ręczne, która nie znajduje się w tym scenariuszu. Szczegóły dotyczące sposobu pobierania danych zostało odnalezione w [dokumentacji interfejsu API GovTrack](https://www.govtrack.us/developers).

### <a name="data-source"></a>Źródło danych

W tym scenariuszu nieprzetworzone dane zebrane jest szereg działań legislacyjne wprowadzona przez nas Kongres (proponowane rachunków i rozwiązania) od 1973 do czerwca 2017 r. (93rd 115th kongresy). Zebranych danych jest w formacie JSON i zawiera bogaty zestaw informacji o akcjach prawnych. Zapoznaj się [ten link GitHub](https://github.com/unitedstates/congress/wiki/bills) szczegółowy opis pola danych. Dla celów demonstracyjnych w ramach tego scenariusza tylko podzbiór pól danych zostały wyodrębnione pliki w formacie JSON. Wstępnie skompilowany plik TSV `CongressionalDataAll_Jun_2017.tsv` zawierającego rekordy te czynności prawne znajduje się w tym scenariuszu. Plik TSV mogły być pobierane automatycznie przez notesów `1_Preprocess_Text.ipynb` w folderze notesu lub `preprocessText.py` w pakiet języka Python.

### <a name="data-structure"></a>Struktura danych

Istnieje dziewięciu pól danych w pliku danych. Nazwy pól i opisy są wyświetlane w następujący sposób.

| Nazwa pola | Typ | Opis | Zawiera wartości Brak |
|------------|------|-------------|---------------|
| `ID` | Ciąg | Identyfikator rachunek/rozwiązania. Format tego pola jest [bill_type] [liczba]-[Kongres]. Na przykład "hconres1 93" oznacza, że typ rachunek jest "hconres" (stoisk dla rozpoznawania współbieżnych DOM odnoszą się do [w tym dokumencie](https://github.com/unitedstates/congress/wiki/bills#basic-information)), liczba rachunek jest "1"i liczba Kongres jest 93". | Nie |
| `Text` | Ciąg | Zawartość rachunek/rozwiązania. | Nie |
| `Date` | Ciąg | Data początkowo proponowane rachunek/rozwiązania. W formacie "rrrr mm-dd". | Nie |
| `SponsorName` | Ciąg | Nazwa głównej sponsora, który proponowane rozwiązanie/na rachunku. | Yes |
| `Type` | Ciąg | Tytuł typu podstawowego sponsoruj "rep" (przedstawiciel) lub "Wyślij" (senator). | Yes |
| `State` | Ciąg | Stan sponsora podstawowego. | Yes |
| `District` | Liczba całkowita | Numer district podstawowego sponsora, jeśli tytuł sponsora przedstawicielem. | Yes |
| `Party` | Ciąg | Strona sponsora podstawowego. | Yes |
| `Subjects` | Ciąg | Warunki podmiotu, na rachunku, a następnie dodać łącznie przez bibliotekę Kongres. Warunki są łączone przecinkami. Te warunki są zapisywane przez człowieka w bibliotece Kongres, a nie są zwykle obecne po pierwszym opublikowaniu informacje o rachunku. Mogą one dodane w dowolnym momencie. Dlatego do końca cyklu życia rachunek niektóre podmiotu mogą nie być odpowiednie już. | Yes |

## <a name="scenario-structure"></a>Struktura scenariusza

Przykład analiza kolekcji dokumentów jest podzielony na dwa rodzaje cele do zrealizowania. Pierwszy typ to seria iPython notesów, przedstawiających szczegółowe opisy całego przepływu pracy. Drugi typ to pakiet języka Python, a także przykłady kodu dotyczące używania tego pakietu. Pakiet języka Python jest ogólny do wielu zastosowań.

Pliki w tym przykładzie są zorganizowane w następujący sposób.

| Nazwa pliku | Typ | Opis |
|-----------|------|-------------|
| `aml_config` | Folder | Folder konfiguracji w usłudze Azure Machine Learning Workbench, zapoznaj się [tej dokumentacji](./experimentation-service-configuration-reference.md) konfiguracji wykonywania szczegółowe eksperymentu |
| `Code` | Folder | Folder kodu, używany do zapisywania skryptów w języku Python i pakiet języka Python |
| `Data` | Folder | Folder danych używany do zapisywania plików pośrednich |
| `notebooks` | Folder | Folder Notesy programu Jupyter |
| `Code/documentAnalysis/__init__.py` | Soubor Pythonu | Plik init pakietami języka Python |
| `Code/documentAnalysis/configs.py` | Soubor Pythonu | Plik konfiguracji używany podczas analizy dokumentu pakiet języka Python, w tym wstępnie zdefiniowanych stałych |
| `Code/documentAnalysis/preprocessText.py` | Soubor Pythonu | Plik języka Python, używany do wstępnego przetworzenia danych dla zadań podrzędnych |
| `Code/documentAnalysis/phraseLearning.py` | Soubor Pythonu | Plik języka Python, używany fraz z danych i przekształcaj pierwotne dane |
| `Code/documentAnalysis/topicModeling.py` | Soubor Pythonu | Plik języka Python, używane do trenowania modelu tematu ukrytego Bayesian alokacji (LDA) |
| `Code/step1.py` | Soubor Pythonu | Krok 1 analiza kolekcji dokumentów: wstępne przetwarzanie tekstu |
| `Code/step2.py` | Soubor Pythonu | Krok 2 analiza kolekcji dokumentów: frazę nauki |
| `Code/step3.py` | Soubor Pythonu | Krok 3 Analiza kolekcji dokumentów: nauczanie i ocena LDA tematu modelu |
| `Code/runme.py` | Soubor Pythonu | Przykład wykonać wszystkie czynności w jednym pliku |
| `notebooks/1_Preprocess_Text.ipynb` | Program iPython Notebook | Wstępne przetwarzanie tekstu i przekształcanie danych pierwotnych |
| `notebooks/2_Phrase_Learning.ipynb` | Program iPython Notebook | Dowiedz się frazy z tekstu danych (po przekształcania danych) |
| `notebooks/3_Topic_Model_Training.ipynb` | Program iPython Notebook | Szkolenie LDA tematu modelu |
| `notebooks/4_Topic_Model_Summarization.ipynb` | Program iPython Notebook | Podsumowanie zawartości z kolekcji dokumentów na podstawie uczonego modelu tematu LDA |
| `notebooks/5_Topic_Model_Analysis.ipynb` | Program iPython Notebook | Analizowanie miejscowego zawartości z kolekcji dokumenty tekstowe i korelowanie miejscowego informacje pod kątem innych metadane skojarzone z kolekcji dokumentów |
| `notebooks/6_Interactive_Visualization.ipynb` | Program iPython Notebook | Interaktywna wizualizacja tematu nauczony model |
| `notebooks/winprocess.py` | Soubor Pythonu | Skrypt języka python dla przetwarzania wieloprocesowego posługują się notesów |
| `README.md` | Plik markdown | Plik README w języku znaczników markdown |

### <a name="data-ingestion-and-transformation"></a>Pozyskiwanie danych i transformacji

Skompilowany zestaw danych `CongressionalDataAll_Jun_2017.tsv` zostanie zapisany w magazynie obiektów Blob i jest dostępny zarówno z poziomu w ramach notesów i skryptów w języku Python. Istnieją dwa kroki pozyskiwania i przekształcania: wstępne przetwarzanie danych tekstowych i frazę uczenia.

#### <a name="preprocess-text-data"></a>Przetwarzanie wstępne danych tekstowych

Przetwarzania wstępnego krok ma zastosowanie technik przetwarzania języka naturalnego, aby oczyszczaj i przygotowuj dane nieprzetworzony tekst. Służy on jako wstępnym i niezbędnym warunkiem celów edukacyjnych nienadzorowanych frazy i modelowanie ukrytego tematu. Szczegółowy opis krok po kroku można znaleźć w notesie `1_Preprocess_Text.ipynb`. Jest także skrypt w języku Python `step1.py` odnosi się do tego notesu.

W tym kroku plik TSV danych jest pobierane z usługi Azure Blob Storage i zaimportować jako Pandas DataFrame. Każdy element wiersz ramki danych jest spójnych pojedynczego długi ciąg tekstu lub dokumentu. Każdy dokument następnie jest dzielony na fragmenty tekstu, które mogą być zdania, fraz lub subphrases. Podział jest przeznaczony do Stanów Zjednoczonych zabraniają frazy uczenia procesu z korzystania z ciągów cross zdania lub krzyżowe fraza słowa podczas nauki fraz.

Istnieje wiele funkcji, zdefiniowanych na potrzeby przetwarzania wstępnego etapu, zarówno w notesie i pakiet języka Python. Większość zadań można osiągnąć przez wywołanie metody te dwa wiersze kodów.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Learning frazy

Krok uczenia frazy implementuje podstawowe struktury stosowane do informacje kluczowe frazy wśród dużych kolekcji dokumentów. Opisano w dokumencie pt. "[modelowania wyrażeń Multiword za pomocą ograniczonego drzewa wyrażeń ulepszone tematu modelowania z konwersacji rozpoznawania mowy](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", który pierwotnie został przedstawiony w Workshop IEEE 2012 na używany język Technologia. Szczegółowe implementacji frazy, krok uczenia jest wyświetlany w iPython Notebook `2_Phrase_Learning.ipynb` i skrypt w języku Python `step2.py`.

Ten krok zajmuje oczyszczony tekst jako dane wejściowe i uczy się najbardziej istotne fraz, istnieje w dużych kolekcji dokumentów. Nauka frazy jest procesem iteracyjnym, które mogą zostać podzielone na trzy zadania: liczba n gramy, rank potencjalnych fraz ważona Pointwise wzajemnego informacje o ich składowe słów i napisz ponownie wyrażenie na tekst. Te trzy zadania są wykonywane sekwencyjnie w wiele iteracji, dopóki zawarto określonego wyrażenia.

W pakiecie Python analizy dokumentu, klasa Python `PhraseLearner` jest zdefiniowany w `phraseLearning.py` pliku. Poniżej przedstawiono fragment kodu umożliwia poznanie fraz.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> Krok uczenia frazy jest implementowany z przetwarzaniem wieloprocesorowym. Jednak zrobić więcej rdzeni procesora CPU **nie** oznacza krótszy czas wykonywania. W naszym testy wydajności nie jest widokiem z więcej niż osiem rdzeni ze względu na obciążenie przetwarzania wieloprocesowego. Aby dowiedzieć się, 25 000 fraz na maszynie z ośmioma rdzeniami (3,6 GHz) zajęło to około dwóch i pół godziny.
>

### <a name="topic-modeling"></a>Modelowanie tematu

Użyj modelu ukrytego temacie uczenia LDA stanowi trzeci krok w tym scenariuszu. [Gensim](https://radimrehurek.com/gensim/) pakiet języka Python jest wymagany w tym kroku, aby dowiedzieć się więcej [modelu tematu LDA](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Notes odpowiednie dla tego kroku jest `3_Topic_Model_Training.ipynb`. Może również dotyczyć `step3.py` dotyczącymi używania pakietu analizy dokumentu.

W tym kroku głównym zadaniem jest model LDA tematu i dostrajanie parametrów funkcji hyper. Istnieje wiele parametrów muszą być dostosowane podczas uczenia modelu LDA, ale parametr najważniejsze jest to liczba tematów. Zbyt mało tematy nie może mieć wgląd w kolekcji dokumentów; Podczas wybierania zbyt wiele wynikiem będzie "nadmiernie grupowania" korpus do wielu małych, wysoce podobnego tematów. Ten scenariusz ma na celu pokazują, jak dostosować liczbę tematów. Usługa Azure Machine Learning Workbench zapewnia swobodę uruchamiania eksperymentów przy użyciu różnych parametrów konfiguracji w kontekstach obliczeniowej.

W pakiecie Python analizy dokumentu zdefiniowano kilka funkcji ułatwiających użytkowników ustalenie najlepszych liczba tematów. Pierwszym sposobem jest poprzez ocenę spójność modelu tematu. Istnieją cztery macierzy oceny obsługiwane: `u_mass`, `c_v`, `c_uci`, i `c_npmi`. Szczegółowe informacje o tych czterech metryk zostały omówione w [w tym dokumencie](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Drugiej metody jest oceny perplexity na korpus przechowywanych w poziomie.

Oceny perplexity kształtu krzywej "U" oczekuje się, aby dowiedzieć się, najlepsze liczba tematów. I pozycja kątowa jest najlepszym wyborem. Zalecane jest ocena wiele razy przy użyciu różnych Inicjator losowy i Uzyskaj średnią. Oceń spójności powinien być "n" kształtu, co oznacza, że zwiększa spójność uwzględni się rosnącą liczbę tematów, a następnie zmniejszyć. Przykładowy wykres perplexity i `c_v` spójności jest wyświetlane w następujący sposób.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v spójności](./media/scenario-document-collection-analysis/c_v_Coherence.png)

W tym scenariuszu perplexity zwiększa się znacznie po 200 tematów, a wartość spójności znacznie zmniejsza się po także tematy 200. Oparte na tych wykresach i wymaganą dla bardziej ogólnych tematów w porównaniu z klastrowanego tematy, wybierz 200 tematy powinny być dobrym rozwiązaniem.

Możesz uczyć jeden LDA modelu tematu w jednego eksperymentu uruchamiać, lub nauczanie i ocena wielu modeli LDA z konfiguracjami numer innego tematu w eksperymencie Uruchom. Zalecane jest wykonywany wielokrotnie dla jednej konfiguracji, a następnie średnią ocen spójności i/lub perplexity. Szczegółowe informacje dotyczące używania pakietu analizy dokumentu można znaleźć w `step3.py` pliku. Fragment kodu przykładu są następujące:

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> Czas wykonywania w celu nauczenia modelu tematu LDA zależy od wielu czynników, takich jak rozmiar korpus, konfiguracja hyper parametru, a także liczby rdzeni na maszynie. Za pomocą wiele rdzeni procesora CPU przygotowuje szybciej modelu. Za pomocą tego samego parametru hyper ustawienie więcej rdzeni oznacza jednak mniejszej liczby aktualizacji podczas szkolenia. Zaleca się mieć **co najmniej 100 aktualizacji do nauczenia modelu LDA osiągnięcia zbieżności**. Relacja między liczbą aktualizacji i parametry hyper została omówiona w [ten wpis](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) i [ten wpis](http://miningthedetails.com/blog/python/lda/GensimLDA/). W naszym testy, jaki zajęło około 3 godziny do nauczenia modelu LDA 200 tematy, przy użyciu konfiguracji `workers=15`, `passes=10`, `chunksize=1000` na maszynie z 16 rdzeni (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Podsumowanie tematu i analiza

Podsumowania tematu i analizy składa się z dwa notesy, gdy istnieją żadne odpowiednie funkcje w pakiecie analizy dokumentu.

W `4_Topic_Model_Summarization.ipynb`, przedstawiono podsumowanie zawartości dokumentu na podstawie uczonego modelu tematu LDA. Podsumowanie jest stosowany do modelu tematu LDA uzyskane w kroku 3. Widoczny jest sposób mierzenia ważność lub jakości tematu przy użyciu tematu, aby zmierzyć czystości dokumentu. Ta miara czystości przyjęto założenie, że ukryte tematów, które dominują dokumentów, w których są wyświetlane są bardziej semantycznie ważne, niż ukrytego tematów, które są słabo rozkładają się na wiele dokumentów. To pojęcie została wprowadzona w dokumencie "[ukrytego modelowanie temat podsumowania korpus Audio](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)."

Notes `5_Topic_Model_Analysis.ipynb` pokazuje, jak analizować miejscowego zawartość w kolekcji dokumentów, możesz też skorelować miejscowego informacje pod kątem innych metadane skojarzone z kolekcji dokumentów. W ten notes, aby ułatwić użytkownikom lepsze zrozumienie nauczony tematu i kolekcji dokumentów, wprowadzono kilka powierzchni.

Notes `6_Interactive_Visualization.ipynb` pokazuje, jak interaktywnie wizualizować tematu nauczony model. Obejmuje to cztery zadania interaktywnej wizualizacji.

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu rzeczywistych prezentuje sposób użycia WKT text analytics technik (na w tym przypadku, nauki frazy i modelowanie tematu LDA) do tworzenia niezawodnych modeli i jak Azure Machine Learning Workbench może pomóc śledzić wydajność modelu i bezproblemowo uruchomić uczących się w większej skali. Bardziej szczegółowo:

* Użyj frazy uczenia i modelowanie tematu do przetwarzania kolekcji dokumentów i twórz niezawodne modelu. W przypadku duży zbiór dokumentów, Azure Machine Learning Workbench można łatwo skalować ją w górę i out. Ponadto użytkownicy mają możliwość uruchamiania eksperymentów w kontekście obliczeniowej z w ramach usługi Azure Machine Learning Workbench.

* Usługa Azure Machine Learning Workbench udostępnia zarówno funkcje umożliwiające uruchamianie notesów w sposób krok po kroku oraz skrypt języka Python, aby uruchomić eksperyment całego.

* Potrzebne parametru funkcji Hyper dostrajanie, za pomocą usługi Azure Machine Learning Workbench można znaleźć najlepszą liczba tematów dowiedzieć się więcej. Usługa Azure Machine Learning Workbench może pomóc śledzić wydajność modelu i bezproblemowo uruchomić różne uczących w większej skali.

* Usługa Azure Machine Learning Workbench można zarządzać w historii uruchamiania i nauczony modeli. Umożliwia on analitykom danych szybkie identyfikowanie najlepiej modeli oraz skrypty i dane używane do generowania ich.

## <a name="references"></a>Dokumentacja

* **Hazen J. Tymotka, Leonard Fred**, [ _modelowania Multiword fraz z drzewa wyrażeń ograniczone dla ulepszone modelowania temat konwersacji mowy_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Język mówiony technologii Workshop (SLT) 2012 IEEE. IEEE, 2012.

* **Timothy J. Hazen**, [ _modelowanie ukrytego temat podsumowania Audio korpus_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12 corocznej konferencji skojarzeń komunikacji międzynarodowych mowy. 2011.

* **Michael Roder, Andreas obu, Alexander Hinneburg**, [ _Eksplorowanie miejsca na temat spójności środków_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Postępowanie ósmego ACM międzynarodowej konferencji dotyczącej wyszukiwania w Internecie i analizą danych. ACM, 2015 R.
