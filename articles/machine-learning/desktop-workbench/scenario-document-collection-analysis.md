---
title: Dokument analiza kolekcji - Azure | Dokumentacja firmy Microsoft
description: Sposób podsumowywania i analizowania dużych kolekcji dokumentów, w tym technik, takich jak learning frazy, temacie modelowania i tematu modelu analizy przy użyciu usługi Azure ML Workbench.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 60d65b17d4cbe8a45ff3fb62b06852d7b945e8f1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832863"
---
# <a name="document-collection-analysis"></a>Analiza kolekcji dokumentów

W tym scenariuszu przedstawiono sposób podsumowywania i analizowania dużych kolekcji dokumentów, w tym technik, takich jak learning frazy, temacie modelowania i tematu modelu analizy przy użyciu usługi Azure ML Workbench. Azure Machine Learning Workbench zapewnia łatwo skalę dla kolekcji dokumentów bardzo duże i zawiera mechanizmy do nauczenia i dostrajania modeli w różnych kontekstach obliczeń, od lokalnego obliczeniowych do danych nauki maszyny wirtualne do klastra Spark. Łatwość programowania jest zapewniana za pomocą notesów Jupyter w ramach usługi Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii

W tym scenariuszu rzeczywistych publicznego repozytorium GitHub zawiera wszystkich materiałów, w tym przykłady kodu, potrzebne w tym przykładzie:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Przegląd

W przypadku dużej ilości danych (szczególnie niestrukturalnych tekst) zbierane codziennie istotne wyzwanie jest organizacji, wyszukiwanie i zrozumieć ogromnych ilości tekstów. Ten scenariusz analizy kolekcji dokumentów pokazuje wydajne i automatyczne end-to-end przepływu pracy do analizowania dużych dokumentów kolekcji i włączenie zadań NLP podrzędne.

Kluczowe elementy dostarczane w tym scenariuszu są:

1. Learning istotne frazy słowa wielu z dokumentów.

1. Odnajdywanie podstawowe tematy przedstawione w kolekcji dokumentów.

1. Reprezentującymi dokumenty przez miejscowego dystrybucji.

1. Przedstawienie metody organizowanie, wyszukiwanie i podsumowywanie dokumentów na podstawie zawartości miejscowego.

Metod przedstawionych w tym scenariuszu można włączyć szereg krytycznych obciążeń przemysłowej, takich jak wykrywania anomalii trendów tematu, podsumowania kolekcji dokumentów i podobne wyszukiwania dokumentu. Można go zastosować dla wielu typów analizy dokumentu, takie jak ustawodawstwa dla instytucji rządowych, wiadomości, recenzje produktów, opinie klientów i artykuły naukowych.

Algorytmów uczenia maszynowego techniki/używaną w tym scenariuszu obejmują:

1. Przetwarzanie tekstu i czyszczenia

1. Learning frazy

1. Temat modelowania

1. Boże podsumowania

1. Miejscowego trendów i wykrywania anomalii

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

* Upewnij się, że poprawnie zainstalowano [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) wykonując [zainstalować i utworzyć szybkiego startu](../service/quickstart-installation.md).

* W tym przykładzie można uruchomić na dowolnym kontekście obliczeń. Jednak zaleca się uruchamiania na maszynie wielordzeniowych z co najmniej 16GB pamięci RAM i miejsca na dysku 5GB.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt Workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablon:
1.  Otwórz Azure Machine Learning Workbench
2.  Na **projekty** kliknij przycisk **+** podpisywania i wybierz **nowy projekt**
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz "Analizy kolekcji dokumentów" i wybierz szablon
5.  Kliknij przycisk **Utwórz**

## <a name="data-description"></a>Opis elementu danych

Zestaw danych, w tym scenariuszu zawiera tekst podsumowania i skojarzone metadane dla każdej prawne akcję wykonywaną przez Kongres NAS. Dane są zbierane z [GovTrack.us](https://www.govtrack.us/), który śledzi działania Kongres Stanów Zjednoczonych i pomaga obywateli brać udziału w procesu prawa krajowego. Dane zbiorcze można pobrać za pomocą [to łącze](https://www.govtrack.us/data/congress/) za pomocą ręcznej skryptu, który nie jest zawarty w tym scenariuszu. Szczegółów dotyczących sposobu pobierania danych można znaleźć w [dokumentacji interfejsu API GovTrack](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Źródło danych

W tym scenariuszu nieprzetworzone dane zbierane jest serią działań prawnych wynikające z NAMI kongresem (rachunków proponowany i rozwiązania) z 1973 do czerwca 2017 (93rd 115th kongresy). Zebrane dane w formacie JSON i zawiera bogaty zestaw informacji o akcjach prawnych. Zapoznaj się [to łącze GitHub](https://github.com/unitedstates/congress/wiki/bills) szczegółowy opis pól danych. W tym celu pokaz w tym scenariuszu tylko podzestaw danych pola zostały wyodrębnione pliki w formacie JSON. Wstępnie skompilowanym plikiem TSV `CongressionalDataAll_Jun_2017.tsv` zawierającego rekordy te akcje prawnych znajduje się w tym scenariuszu. Plik TSV można pobrać automatycznie przez notebooki `1_Preprocess_Text.ipynb` w folderze notesu lub `preprocessText.py` w pakiet języka Python.

### <a name="data-structure"></a>Struktura danych

W pliku danych jest dziewięciu pól danych. Poniżej wymieniono nazwy pól i opisy.

| Nazwa pola | Typ | Opis | Brak wartości zawierają |
|------------|------|-------------|---------------|
| `ID` | Ciąg | Identyfikator rachunku/rozwiązania. Format tego pola jest [bill_type] [numer]-[Kongres]. Na przykład "hconres1-93" oznacza, że typ rachunek jest "hconres" (dokonane dla rozpoznawania równoczesnych DOM odwoływać się do [tego dokumentu](https://github.com/unitedstates/congress/wiki/bills#basic-information)), liczba rachunek jest "1"i numer Kongres jest 93". | Nie |
| `Text` | Ciąg | Zawartość rachunku/rozwiązania. | Nie |
| `Date` | Ciąg | Data początkowo proponowane rachunku/rozwiązania. W formacie "rrrr mm-dd". | Nie |
| `SponsorName` | Ciąg | Nazwa głównej sponsor proponowana rachunku/rozwiązania. | Yes |
| `Type` | Ciąg | Typ podstawowy tytuł sponsorować "rep" (przedstawiciel) lub "Wyślij" (senator). | Yes |
| `State` | Ciąg | Stan sponsor podstawowego. | Yes |
| `District` | Liczba całkowita | Numer regionalnego głównej sponsor Jeśli tytuł sponsor jest przedstawicielem. | Yes |
| `Party` | Ciąg | Strona sponsor podstawowego. | Yes |
| `Subjects` | Ciąg | Warunki podmiotu do zestawienia, a następnie dodać zbiorczo przez biblioteki Kongres. Warunki są łączone przecinkami. Te warunki są zapisywane przez człowieka w bibliotece Kongres i nie są zwykle dostępne po pierwszym opublikowaniu informacji na. Mogą one dodane w dowolnym momencie. W związku z tym do końca okresu rachunek niektórych podmiotu może nie być odpowiednie już. | Yes |

## <a name="scenario-structure"></a>Scenariusz — struktura

Przykład analizy kolekcji dokumentu jest podzielony na dwa rodzaje materiałów. Pierwszy typ jest szereg iPython notebooki, które zawierają szczegółowe opisy całego przepływu pracy. Drugi typ jest pakiet języka Python, jak również przykłady kodu do użycia tego pakietu. Pakiet języka Python jest ogólny, aby obsługiwać wiele przypadków użycia.

Pliki w tym przykładzie są zorganizowane w następujący sposób.

| Nazwa pliku | Typ | Opis |
|-----------|------|-------------|
| `aml_config` | Folder | Folder konfiguracji w usłudze Azure Machine Learning Workbench, zapoznaj się [tej dokumentacji](./experimentation-service-configuration-reference.md) szczegółowe eksperymentu wykonywania konfiguracji |
| `Code` | Folder | Folder kod używany do zapisania skrypty języka Python i pakiet języka Python |
| `Data` | Folder | Folder danych używany do zapisania plików pośrednich |
| `notebooks` | Folder | Folder Notesy Jupyter |
| `Code/documentAnalysis/__init__.py` | Plik Python | Plik init pakiet języka Python |
| `Code/documentAnalysis/configs.py` | Plik Python | Plik konfiguracji używany podczas analizy dokumentu pakiet języka Python, w tym stałe wstępnie zdefiniowane |
| `Code/documentAnalysis/preprocessText.py` | Plik Python | Plik języka Python, używany do wstępnego przetworzenia danych dla zadania podrzędne |
| `Code/documentAnalysis/phraseLearning.py` | Plik Python | Plik Python używany do nauczenia fraz z danych i przekształcania danych pierwotnych |
| `Code/documentAnalysis/topicModeling.py` | Plik Python | Plik języka Python, używany do uczenia modelu tematu ukryty Dirichlet alokacji (LDA) |
| `Code/step1.py` | Plik Python | Krok 1 analizy kolekcji dokumentów: przetwarzanie wstępne tekstu |
| `Code/step2.py` | Plik Python | Krok 2 analizy kolekcji dokumentów: frazę learning |
| `Code/step3.py` | Plik Python | Krok 3 analizy kolekcji dokumentów: nauczanie i Ewaluacja modelu tematu LDA |
| `Code/runme.py` | Plik Python | Przykład uruchomienia wszystkich kroków w jednym pliku |
| `notebooks/1_Preprocess_Text.ipynb` | iPython notesu | Przetwarzanie wstępne tekstu i przekształcanie danych pierwotnych |
| `notebooks/2_Phrase_Learning.ipynb` | iPython notesu | Dowiedz się fraz z danych tekstowych (po przekształcania danych) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython notesu | Train LDA tematu modelu |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython notesu | Podsumowanie zawartości kolekcji dokumentów na podstawie uczonego modelu tematu LDA |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython notesu | Analizowanie miejscowego zawartości kolekcji dokumentów tekstowych i skorelowania miejscowego informacje pod kątem innych metadane skojarzone z tą kolekcją dokumentu |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython notesu | Interakcyjne wizualizacji modelu zapamiętanych tematu |
| `notebooks/winprocess.py` | Plik Python | Skrypt w języku python dla przetwarzanie wieloprocesorowe używany przez komputery przenośne |
| `README.md` | Pliku markdown | Plik README języka znaczników markdown |

### <a name="data-ingestion-and-transformation"></a>Wprowadzanie danych i transformacja

Skompilowany zestaw danych `CongressionalDataAll_Jun_2017.tsv` jest zapisany w magazynie obiektów Blob i jest dostępny zarówno z notesów i skrypty języka Python. Istnieją dwa kroki dla wprowadzanie danych i transformacja: przetwarzanie wstępne danych tekstowych oraz zwrot learning.

#### <a name="preprocess-text-data"></a>Przetwarzanie wstępne danych tekstowych

Krok przetwarzania wstępnego stosuje techniki przetwarzania języka naturalnego do czyszczenia i przygotowania danych pierwotnych tekstu. Służy on jako macierzystych uczenie nienadzorowane frazy i modelowania ukryty tematu. Szczegółowy opis krok po kroku można znaleźć w notesie `1_Preprocess_Text.ipynb`. Jest także skrypt w języku Python `step1.py` odnosi się do tego notesu.

W tym kroku plik danych TSV jest pobierany z magazynu obiektów Blob platformy Azure i zaimportowana jako Pandas DataFrame. Każdy element wiersza DataFrame to pojedynczy spójny ciąg tekstu lub dokumentu. Każdy dokument następnie jest dzielony na fragmenty tekstu, które mogą być zdania, wyrażenia lub subphrases. Podziału zaprojektowano w celu zakazać frazy uczenia procesu z używania ciągów między zdanie lub frazę między wyrazu, podczas nauki fraz.

Istnieje wiele funkcji, zdefiniowanych na potrzeby przetwarzania wstępnego etapu notesu i pakiet języka Python. Większość zadań można osiągnąć przez wywołanie metody te dwa wiersze kodów.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Learning frazy

Krok learning frazy implementuje podstawową strukturę, aby dowiedzieć się klucza fraz między dużą kolekcję dokumentów. Opisano w dokumencie pod tytułem "[modelowania fraz Multiword ograniczone drzewo wyrażeń ulepszone tematu modelowania z konwersacji rozpoznawania mowy](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", który pierwotnie został przedstawiony w Workshop IEEE 2012 na używany język Technologia. Szczegółowe wykonania kroku learning frazy jest wyświetlany w obszarze iPython notesu `2_Phrase_Learning.ipynb` i skrypt w języku Python `step2.py`.

Ten krok zajmuje oczyszczony tekstu jako dane wejściowe i uzyskuje informacje o najbardziej istotne fraz, obecny w dużych kolekcji dokumentów. Learning frazy jest procesem iteracyjnym, które mogą być podzielone na trzy zadania: liczba n g, rank potencjalnych fraz ważone Pointwise wzajemne informacje ich składowych słów i przepisywania frazę do tekstu. Te trzy zadania są wykonywane sekwencyjnie w przejść przez wiele iteracji, dopóki nie znasz określonego wyrażenia.

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
> Krok learning frazy zaimplementowano z przetwarzaniem wieloprocesorowym. Jednak wykonać większej liczby rdzeni procesora CPU **nie** oznacza skrócić czas wykonywania. Nasze testy z więcej niż osiem rdzeni ze względu na obciążenie związane z przetwarzaniem wieloprocesorowym nie jest zwiększenie wydajności. Zajęło około dwóch i pół godziny, aby dowiedzieć się 25 000 fraz na maszynie z osiem rdzeni (3,6 GHz).
>

### <a name="topic-modeling"></a>Temat modelowania

Learning Użyj modelu ukryty tematu LDA stanowi trzeci krok w tym scenariuszu. [Gensim](https://radimrehurek.com/gensim/) pakiet języka Python jest wymagany w tym kroku, aby dowiedzieć się [LDA tematu modelu](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Odpowiednie Notes dla tego kroku jest `3_Topic_Model_Training.ipynb`. Można także odwoływać się do `step3.py` dotyczącymi używania pakietu analizy dokumentu.

W tym kroku głównym zadaniem jest do nauczenia modelu tematu LDA i dostrajanie parametrów funkcji hyper. Istnieje wiele parametrów muszą być dopasowane podczas uczenia modelu LDA najważniejszą parametru jest jednak liczbę tematów. Za mało tematy nie może mieć wgląd w kolekcji dokumentów; Podczas wybierania zbyt wiele spowoduje "nadmiernie grupowania" Boże na wiele małych, bardzo podobne tematy. Ten scenariusz ma na celu pokazują, jak dostosować liczbę tematów. Azure Machine Learning Workbench zapewnia swobody uruchamiać eksperymenty z innym parametrem konfiguracji na obliczeń różnych kontekstach.

W pakiecie dokumentu analizy Python kilka funkcji zostały zdefiniowane ułatwia użytkownikom zorientować się najlepiej liczbę tematów. Pierwszym sposobem jest wyniku obliczenia spójności modelu tematu. Istnieją cztery macierzy oceny obsługiwane: `u_mass`, `c_v`, `c_uci`, i `c_npmi`. Szczegółowe informacje o tych czterech metryk, omówiono w [w tym dokumencie](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Drugi podejście jest dokonanie oceny perplexity na Boże przechowywanych w poziomie.

Oceny perplexity krzywej kształtu "U" powinien sprawdzić najlepsze liczbę tematów. I pozycji kątowa jest najlepszym rozwiązaniem. Zalecane jest aby obliczyć wiele razy z różnych Inicjator losowy i uzyskać średnią. Ocena spójności powinien być "n" kształtu, co oznacza, że zwiększa spójność, zwiększając liczbę tematów, a następnie zmniejszyć. Przykład wykres perplexity i `c_v` spójności jest wyświetlany w następujący sposób.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v spójności](./media/scenario-document-collection-analysis/c_v_Coherence.png)

W tym scenariuszu perplexity zwiększa się znacznie po 200 tematy, a wartość spójności znacznie zmniejsza się po także tematy 200. Na podstawie tych wykresów i potrzeby bardziej ogólne tematy i za pośrednictwem tematy klastrowanych, wybierz 200 tematy powinny być dobrym rozwiązaniem.

Możesz to zrobić później jedną LDA modelu tematu w eksperymencie co uruchomić, lub nauczanie i Ewaluacja wielu modeli LDA z konfiguracjami numer innego tematu w eksperymencie pojedynczego uruchomienia. Zaleca się uruchamiać wiele razy dla jednej konfiguracji, a następnie zachęcić średni oceny spójności i/lub perplexity. Szczegóły dotyczące używania pakietu analizy dokumentu znajdują się w `step3.py` pliku. Poniżej przedstawiono przykład fragment kodu.

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
> Czas wykonania do uczenia modelu tematu LDA zależy od wielu czynników, takich jak rozmiar Boże, hyper parametru konfiguracji, a także liczby rdzeni na maszynie. Przy użyciu wiele rdzeni procesora CPU przygotowuje szybsze modelu. Z tego samego parametru funkcji hyper ustawienie większej liczby rdzeni oznacza jednak mniej aktualizacji podczas uczenia. Zalecane jest aby **co najmniej 100 aktualizacji do uczenia modelu LDA konwergentnej**. Relacja między liczbą aktualizacji i parametry funkcji hyper została szczegółowo opisana w [tego wpisu](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) i [ten wpis](http://miningthedetails.com/blog/python/lda/GensimLDA/). Nasze testy zajęło około 3 godziny do uczenia modelu LDA z 200 tematów za pomocą konfiguracji `workers=15`, `passes=10`, `chunksize=1000` na maszynie z 16 rdzeni (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Podsumowanie tematu i analiza

Podsumowania tematu i analizy składa się z dwóch notesów, gdy istnieją nie odpowiednie funkcje w pakiecie analizy dokumentu.

W `4_Topic_Model_Summarization.ipynb`, przedstawiono podsumowanie zawartości dokumentu na podstawie uczonego modelu tematu LDA. Podsumowanie zastosowano do modelu tematu LDA uzyskane w kroku 3. Widoczny jest sposób mierzenia znaczenie lub jakości tematu przy użyciu tematu miarę czystości dokumentu. To jest miara czystości przyjęto założenie, że ukryty tematy, które dominują w aplikacjach dokumentów, w których są wyświetlane są bardziej semantycznie ważne niż ukryty tematy, które są słabo rozłożyć na wiele dokumentów. To pojęcie została wprowadzona w tym dokumencie "[ukryty modelowania tematu dla podsumowania Boże Audio](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)."

Notesu `5_Topic_Model_Analysis.ipynb` pokazano, jak analizować miejscowego zawartości kolekcji dokumentów i skorelowania miejscowego informacje pod kątem innych metadane skojarzone z tą kolekcją dokumentu. Kilka powierzchni zostały wprowadzone w tym notesie, aby ułatwić użytkownikom lepiej zrozumieć zapamiętanych tematu i kolekcji dokumentów.

Notesu `6_Interactive_Visualization.ipynb` pokazano, jak interaktywnie wizualizować tematu nauczony model. Obejmuje cztery zadania interakcyjne wizualizacje.

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu rzeczywistych prezentuje sposób użycia dobrze znanego tekstu analytics techniki (ten przypadek, learning frazy i modelowania tematu LDA) do tworzenia modelu niezawodny i jak Azure Machine Learning Workbench pomaga śledzić wydajność modelu oraz bezproblemowo Uruchom uczących na zwiększenia skali. Bardziej szczegółowo:

* Użyj frazy uczenie i modelowanie tematu do przetwarzania kolekcję dokumentów i tworzenie niezawodnych modelu. W przypadku dużych kolekcji dokumentów, Azure Machine Learning Workbench można łatwo go skalować w górę i out. Ponadto użytkownicy mają swobody Uruchom eksperymenty w kontekście różnych obliczeń z wewnątrz usługi Azure Machine Learning Workbench.

* Azure Machine Learning Workbench zawiera obie opcje do uruchomienia notesów w sposób krok po kroku oraz skryptu Python całego eksperymentu.

* Potrzebne parametru funkcji Hyper dostrajanie, przy użyciu usługi Azure Machine Learning Workbench można znaleźć najlepsze liczbę tematów dowiedzieć się więcej. Azure Machine Learning Workbench może pomóc śledzenia wydajności modelu i bezproblemowo jednocześnie różnych uczących zwiększenia skali.

* Azure Machine Learning Workbench można zarządzać Historia uruchomień i zapamiętanych modeli. Umożliwia on analityków danych, aby szybko zidentyfikować najbardziej modeli, a także znalezienia skryptów i danych używany do generowania je.

## <a name="references"></a>Dokumentacja

* **Hazen J. Tymotka, Krzysztof Richardson**, [ _Multiword fraz drzewo wyrażeń ograniczone do modelowania ulepszone modelowania tematu konwersacji mowy_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Język rozmowy technologii Workshop (SLT) 2012 IEEE. IEEE, 2012.

* **Tymotka J. Hazen**, [ _modelowania ukryty tematu dla podsumowania Boże Audio_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12 Konferencji roczne skojarzeń komunikacji międzynarodowe mowy. 2011.

* **Jan Roder, Andreasowi zarówno, Alexander Hinneburg**, [ _eksploracji miejsca tematu spójności środków_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Postępowanie ósmego ACM międzynarodowe konferencji wyszukiwanie w sieci Web i wyszukiwania danych. ACM, 2015.
