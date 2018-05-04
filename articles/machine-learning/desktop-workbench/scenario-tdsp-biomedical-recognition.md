---
title: Rozpoznawanie biomedycznych jednostki — proces nauki danych zespołu - Azure Machine Learning | Dokumentacja firmy Microsoft
description: Proces nauki danych zespołu projektu szybkiego startu używającego głębokie learning rozpoznawania biomedycznych jednostki w konsoli usługi Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 421af12da6d207a50d12dcbf64aac51aba06b196
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Rozpoznawanie jednostek biomedycznych przy użyciu szablonu zespołu danych nauki procesu (TDSP)

Wyodrębniania jednostki jest podzadaniem wyodrębniania informacji (znanej także jako [rozpoznawanie jednostek o nazwie (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), jednostki podziału i identyfikacji jednostek). W tym scenariuszu rzeczywistych ma na celu Wyróżnij jak używać usługi Azure Machine Learning Workbench rozwiązać skomplikowanych zadań przetwarzania języka naturalnego (NLP) takich jak jednostki wyodrębniania z tekstu bez struktury:

1. W jaki sposób w celu przeszkolenia neuronowej word osadzeń model, w Boże tekstu, z około 18 milionów PubMed streszczenia przy użyciu [Spark Word2Vec implementacji](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Jak utworzyć głębokie modelu powtarzającego się sieci neuronowej długi krótkoterminowe pamięci (LSTM) do wyodrębnienia jednostki na włączone GPU danych nauki maszynie wirtualnej platformy Azure (GPU DS VM) na platformie Azure.
2. Pokazują, że modelu osadzeń word specyficznego dla domeny może przewyższyć modele osadzeń ogólnego programu word w zadaniu rozpoznawanie jednostek. 
3. Pokazują, jak w celu nauczenia i operacjonalizuj modele uczenia głębokie przy użyciu usługi Azure Machine Learning Workbench.

4. Przykładem w ramach usługi Azure Machine Learning Workbench następujące możliwości:

    * Tworzenie wystąpienia [Struktura zespołu danych nauki procesu (TDSP) i szablony](how-to-use-tdsp-in-azure-ml.md)
    * Automatyczne zarządzanie tym pobierania i instalacji zależności projektu
    * Wykonywanie skryptów języka Python w środowiskach różnych obliczeń
    * Uruchom śledzenie danych historycznych dotyczących skrypty języka Python
    * Wykonanie zadania na serwerze zdalnym Spark obliczeniowe kontekstów z użyciem klastrów HDInsight Spark 2.1
    * Wykonywanie zadań w zdalnym GPU maszyn wirtualnych na platformie Azure
    * Łatwe operationalization modeli uczenia głębokie jako usługi sieci web na platformie Azure kontenera Services (ACS)

## <a name="use-case-overview"></a>Omówienie przypadków użycia
Rozpoznawanie biomedycznych nazwanej jednostki jest krytyczne krok w przypadku złożonych zadań NLP biomedycznych, takich jak: 
* Wyodrębnianie uwagi o nazwie jednostek takich chorób, ds chemikaliów i objawy z elektronicznych medyczne lub kondycji rekordów.
* Odnajdywanie narkotyków
* Opis interakcje między inną jednostkę typy narkotyków narkotyków interakcji, choroby narkotyków relacji i białka gen relacji.

Nasze scenariuszem użycia koncentruje się na jak dużą ilość danych niestrukturalnych Boże, takich jak streszczenia Medline PubMed można analizować w celu przeszkolenia wyraz osadzanie modelu. Następnie osadzone dane wyjściowe są traktowane jako funkcje automatycznie generowane w celu przeszkolenia ekstraktor neuronowej jednostki.

Nasze wyniki wskazują, że szkolenie biomedycznych jednostki wyodrębniania modelu słowo specyficznego dla domeny osadzania funkcji outperforms modelu uczenia w typie ogólnym funkcji. Model specyficznego dla domeny może wykryć 7012 jednostek poprawnie (poza 9475) z równym F1 0.73 i 5274 jednostek z F1 wynik 0,61 rodzajowy modelu.

Na poniższej ilustracji przedstawiono architekturę, który został użyty do przetwarzania danych i modele uczenia.

![Architektura](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Opis elementu danych

### <a name="1-word2vec-model-training-data"></a>1. Dane szkoleniowe Word2Vec modelu
Najpierw pobraliśmy nieprzetworzone dane abstrakcyjny MEDLINE z [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Dane są dostępne publicznie w postaci plików XML na ich [serwera FTP](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Na serwerze są dostępne 892 plików XML i każdy z plików XML ma informacji artykuły 30 000. Więcej informacji na temat kroku zbierania danych znajdują się w sekcji struktury projektu. Są obecne w każdym pliku pól 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. Dane szkoleniowe LSTM modelu

Uczenia modelu wyodrębniania neuronowej jednostki i ocenić publicznie dostępne zestawy danych. Aby uzyskać szczegółowy opis tych zestawów danych, można odwoływać się do następujących źródeł:
 * [Zadanie rozpoznawania mnie jednostki w 2004 BioNLP/NLPBA](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR Boże zadań](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 — zadanie 9.1 (rozpoznawanie narkotyków)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii Azure
Poniżej znajduje się łącze do publicznego repozytorium GitHub rzeczywistych scenariuszy, który zawiera kod i bardziej szczegółowego opisu: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Wymagania wstępne 

* Azure [subskrypcji](https://azure.microsoft.com/free/)
* Azure Machine Learning Workbench. Zobacz [Przewodnik instalacji](../service/quickstart-installation.md). Obecnie Azure Machine Learning Workbench można zainstalować tylko dla następujących systemów operacyjnych: 
    * Windows 10 lub Windows Server 2016
    * System macOS Sierra (lub nowszej)


### <a name="azure-services"></a>Usługi platformy Azure
* [Klaster Spark w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) wersji Spark 2.1 w systemie Linux (HDI 3,6) do obliczeń skalowalnego w poziomie. Aby przetwarzać łączną ilość streszczenia MEDLINE omówiony poniżej, należy minimalnej konfiguracji: 
    * Węzeł HEAD: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) rozmiaru
    * Węzłów procesu roboczego: co najmniej 4 [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). W naszym pracy użyliśmy 11 węzłów procesu roboczego o rozmiarze D12_V2.
* [NC6 danych nauki maszyny wirtualnej (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) do obliczeń skalowanie w pionie.

### <a name="python-packages"></a>Pakiety języka Python

Wszystkie wymagane zależności są zdefiniowane w pliku aml_config/conda_dependencies.yml w folderze projektu scenariusza. Zależności zdefiniowane w tym pliku są automatycznie konfigurowani dla przebiegów przed docker, maszyny Wirtualnej i HDI klastra elementów docelowych. Aby uzyskać więcej informacji o formacie pliku środowiska Conda, zapoznaj się [tutaj](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Podstawowe instrukcje dotyczące workbench Azure Machine Learning (AML)
* [Omówienie](../service/overview-what-is-azure-ml.md)
* [Instalacja](../service/quickstart-installation.md)
* [Przy użyciu TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Jak odczytywać i zapisywać pliki](how-to-read-write-files.md)
* [Jak używać notesów Jupyter](how-to-use-jupyter-notebooks.md)
* [Sposób użycia procesora GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Scenariusz — struktura
W scenariuszu, używamy TDSP struktury i dokumentacja szablonów projektu (rysunek 1), które wykonuje [cyklu życia TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Projekt jest tworzone w oparciu instrukcjami [tutaj](./how-to-use-tdsp-in-azure-ml.md).


![Wypełnij informacje o projekcie](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Pracy do analizy danych krok po kroku jest następujący:

### <a name="1-data-acquisition-and-understanding"></a>1. Pozyskiwanie danych i ich analiza

Zobacz [danych nabycia i zrozumienia](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

Nieprzetworzone Boże MEDLINE ma łącznie streszczenia 27 milionów gdzie artykuły około 10 milionów mają puste pole abstrakcyjny. Azure HDInsight Spark jest używane do przetwarzania danych big data nie może zostać załadowane do pamięci w pojedynczym komputerze jako [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Po pierwsze dane są pobierane w klastrze Spark. Następnie następujące czynności są wykonywane na [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* pliki XML przy użyciu analizatora składni XML Medline analizy
* Przetwarzanie wstępne abstrakcyjny tekst w tym dzielenia zdania, tokenizacji i normalizacji wielkości.
* Wyklucz artykuły, gdy pole abstrakcyjnej jest pusty lub zawiera krótki tekst 
* Tworzenie słownictwa programu word z streszczenia szkolenia
* szkolenie word osadzanie neuronowej modelu. Aby uzyskać więcej informacji, zobacz [GitHub kod łącze](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) rozpocząć pracę.


Po analizie plików XML, danych ma następujący format: 

![Przykładowe dane](./media/scenario-tdsp-biomedical-recognition/datasample.png)

Uczenia modelu wyodrębniania neuronowej jednostki i ocenić publicznie dostępne zestawy danych. Aby uzyskać szczegółowy opis tych zestawów danych, można odwoływać się do następujących źródeł:
 * [Zadanie rozpoznawania mnie jednostki w 2004 BioNLP/NLPBA](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR Boże zadań](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 — zadanie 9.1 (rozpoznawanie narkotyków)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modelowanie

Zobacz [modelowania](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modelowania jest na etapie, gdy zostanie przedstawiony sposób korzystania z pobranego w poprzedniej sekcji dotyczącej szkolenia własne word osadzanie modelu danych i używać go do innych zadań podrzędnych. Chociaż korzystamy danych PubMed potoku, aby wygenerować osadzeń jest rodzajowy i mogą być ponownie używane w celu przeszkolenia osadzeń programu word do innej domeny. Dla osadzeń być dokładne reprezentację danych istotne jest, że word2vec jest uczony w dużej ilości danych.
Po mamy osadzeń word gotowe, możemy uczenia modelu sieci neuronowej głębokość, który używa zapamiętanych osadzeń zainicjować warstwy Embedding. Firma Microsoft oznaczyć osadzania warstwy jako trainable, ale nie jest obowiązkowe. Szkolenie word osadzanie modelu jest nienadzorowanych i dlatego jesteśmy w stanie przeprowadzać teksty bez etykiety. Jednak uczenia modelu jednostki rozpoznawania jest zadaniem uczenia nadzorowanego i jej dokładność zależy od ilości i jakości ręcznie adnotacji danych. 


#### <a name="21-feature-generation"></a>2.1. Funkcja generowania

Zobacz [funkcji generowania](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec to słowo osadzanie Algorytm uczenia nienadzorowanych, który przygotowuje modelu sieci neuronowej z Boże szkolenia bez etykiety. Generuje wektor ciągłego dla każdego wyrazu w Boże, który reprezentuje jego informacje semantyczne. Te modele są proste sieci neuronowej z jednym ukrytej warstwie. Word wektory/osadzeń są rozpoznawane przez backpropagation i stochastycznego spadku gradientu. Istnieją dwa typy word2vec modeli, a mianowicie Skip Gram i ciągłe — zbioru z — wyrażenie (Sprawdź [tutaj](https://arxiv.org/pdf/1301.3781.pdf) więcej szczegółów). Ponieważ używamy implementacja MLlib word2vec, obsługujący modelu gram Pomiń krótko opisano go tutaj (obraz z [łącze](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Pomiń Gram modelu](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

Model używa Softmax hierarchicznej i ujemną próbkowania w celu zoptymalizowania wydajności. Hierarchiczna SoftMax (H-SoftMax) jest przybliżeniem przez drzew binarnych. H-SoftMax zasadniczo zastępuje płaskiej warstwy SoftMax hierarchiczna warstwy, która zawiera wyrazy, jak pozostawia. Pozwala to dekompozycji obliczanie prawdopodobieństwo o jedno słowo w sekwencji obliczeń prawdopodobieństwa oszczędza nam konieczności obliczać kosztowne normalizacji za pośrednictwem wszystkich wyrazów. Ponieważ głębokość log2 ma zrównoważone drzewa binarnego (| V |) (V jest słownictwa), musimy oszacować co najwyżej log2 — (| V |) węzły do uzyskania końcowego prawdopodobieństwo wystąpienia wyrazu. Prawdopodobieństwo w word podane jego c kontekstu jest następnie po prostu produktu prawdopodobieństwa podjęcia prawej i lewej odpowiednio włącza wywołujące do węzła typu liść. Firma Microsoft można utworzyć drzewa Huffman na podstawie częstotliwości słów w zestawie danych Aby upewnić się, że słowa częstsze uzyskać reprezentacje krótsze. Aby uzyskać więcej informacji, zobacz [to łącze](http://sebastianruder.com/word-embeddings-softmax/).
Obraz z [tutaj](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Wizualizacja

Gdy mamy osadzeń word chcielibyśmy ich wizualizacji i zobaczyć relację pomiędzy semantycznie podobne słowa. 

![Podobieństwa W2V](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Firma Microsoft wykazały, wizualizowanie osadzeń dwa różne sposoby. Pierwsza z nich używa PCA do projektu wysokiej wektorów wymiarów do miejsca wektorowa 2-D. Prowadzi to do znaczny poziom utraty danych i wizualizacji nie jest tak dokładne. Druga to użycie PCA z [Oddelegowany t](https://distill.pub/2016/misread-tsne/). t Oddelegowany to technika redukcji rożne wymiary, który dobrze nadaje się do osadzania danych wielowymiarowych wysokiej w miejscu z dwóch lub trzech wymiarów, które mogą być następnie wizualizowane w wykres punktowy.  Każdy obiekt wymiarów wysokiej go modele przez dimensional dwóch lub trzech punkt w taki sposób, że podobne obiekty są modelowane przez punkty pobliskich i różnych obiektów są modelowane przez punkty w odległości. Działa on w dwóch częściach. Najpierw tworzy ona prawdopodobieństwa rozkładu w pary w wyższej przestrzeni trójwymiarowej w sposób podobny obiekty mają wysokie prawdopodobieństwo pobierania i punkty niepodobnych mają niskie prawdopodobieństwo pobieranie pobrane. Po drugie definiuje podobne prawdopodobieństwa rozkładu punktów na mapie wymiarów niski i minimalizuje rozbieżności KL między dwa dystrybucje względem lokalizacja punktów na mapie. Położenie punktów w wymiarze niski są uzyskiwane minimalizując rozbieżności KL przy użyciu spadku gradientu. Ale Ekspert t może nie być zawsze niezawodne. Szczegóły implementacji można znaleźć [tutaj](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Jak pokazano na poniższej ilustracji, wizualizacji Oddelegowany t rozdzielenie więcej wektorów word i potencjalne wzorce klastrowania. 


* Wizualizacja z PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Wizualizacja z Oddelegowany t

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Punkty najbliżej "Raka" (są one wszystkich podtypów raka)

![Punkty najbliżej raka](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Szkolenie ekstraktor neuronowej jednostki

Zobacz [uczenia ekstraktor neuronowej jednostki](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

Architektura sieci neuronowej do przodu kanału informacyjnego odczuwa problem Traktuj każdego dane wejściowe i wyjściowe jako niezależne od innych danych wejściowych i wyjściowych. Tej architektury nie modelu sekwencja do sekwencji zadań etykietowania, takich jak tłumaczenia maszynowego i wyodrębniania jednostki. Modele powtarzającego się sieci neuronowej rozwiązać ten problem, jak przekazywanie informacji obliczana teraz do następnego węzła. Ta właściwość jest wywoływana po pamięci w sieci, ponieważ jest ona w stanie wykorzystać te informacje wcześniej obliczanej, jak pokazano na poniższej ilustracji:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

RNNs podstawowego faktycznie boryka się z [podczas wykonywania gradientu Problem](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) ze względu na które nie mają wykorzystać wszystkie informacje mają zauważony. Problem staje się widoczne tylko w przypadku dużych ilości kontekstu jest wymagany do prognozowania. Ale modeli, takie jak LSTM nie doświadczają taki problem, w rzeczywistości zostały zaprojektowane do zapamiętania długoterminowej zależności. W odróżnieniu od waniliowe RNNs mających jednej sieci neuronowej LSTMs ma interakcje między czterech sieci neuronowej dla każdej komórki. Szczegółowy opis działania LSTM, można znaleźć w temacie [ten wpis](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM komórki](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Załóżmy spróbować utworzyć własne LSTM na podstawie powtarzającego się sieci neuronowej i spróbuj wyodrębniania typy jednostek, takich jak uwagi narkotyków, choroby i objaw PubMed danych. Pierwszym krokiem jest uzyskanie dużej ilości danych oznaczonych i jak użytkownik będzie mieć odgadnąć, który nie jest łatwo! Większość medyczne danych zawiera wiele poufne informacje dotyczące osoby i dlatego nie są publicznie dostępne. Firma Microsoft korzystają z różnych dwóch różnych zestawów danych publicznie dostępne. Pierwszego zestawu danych jest z Semeval 2013 — zadanie 9.1 (rozpoznawanie narkotyków), a druga z BioCreative V CDR zadania. Firma Microsoft jest połączenie i automatycznie etykietowania te dwa zestawy danych, dzięki czemu możemy wykryć zarówno DS i chorób z medyczne teksty i ocenić naszych osadzeń programu word. Szczegóły implementacji, można znaleźć w [GitHub kod łącze](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

Architektura modelu, który użyliśmy przez wszystkie kody i do porównania jest przedstawiony poniżej. Parametr, który zmienia dla różnych zestawów danych jest sekwencji maksymalną długość (w tym miejscu 613).

![LSTM model](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Ocena modelu
Zobacz [modelu oceny](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Używamy skryptu oceny z udostępnionych zadań [zadanie rozpoznawania mnie jednostki w 2004 NLP/NLPBA mnie](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) ocena dokładność, odwoływania i F1 oceny modelu. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>Domeny i rodzajowy word osadzanie modeli

Poniżej przedstawiono porównanie dokładność dwa typy funkcji: osadzeń (1) word ćwiczenie PubMed Abstract i (2) word osadzeń ćwiczenie wiadomości Google. Wyraźnie zobaczyć, że model w domenie outperforms rodzajowy modelu. Dlatego mających określony wyraz osadzanie modelu, a nie za pomocą jednego ogólnego jest znacznie bardziej użyteczne. 

* Zadanie #1: DS i wykrywania chorób

![Porównanie modeli 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Możemy wykonać obliczenie osadzeń programu word w innych zestawów danych w podobny sposób i nie zawsze jest lepszym rozwiązaniem tego modelu w domenie.

* Zadanie #2: Białek, wiersza komórki, typ komórki, DNS i wykrywania RNA

![Porównanie modeli 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Zadanie #3: Chemikaliów i wykrywania chorób

![Porównanie modeli 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Zadanie #4: Wykrywanie ds

![Porównanie modeli 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Zadanie #5: Wykrywanie genów

![Porównanie modeli 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow i CNTK
Zgłoszony modele są uczone przy użyciu Keras z TensorFlow jako wewnętrznej bazy danych. Keras z CNTK wewnętrznej bazy danych nie obsługuje "odwrotną", w czasie pracy to zostało zrobione. W związku z tym dla porównania, firma Microsoft ma uczony model LSTM jednokierunkowe z CNTK wewnętrznej bazy danych i porównuje go modelu LSTM jednokierunkowe z TensorFlow wewnętrznej bazy danych. Zainstaluj CNTK 2.0 dla Keras z [tutaj](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Porównanie modeli 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Firma Microsoft wykazał, że CNTK wykonuje jako dobra jako Tensorflow zarówno pod względem szkolenia czas na epoki (60 s CNTK i 75 w sekundach dla Tensorflow) i liczby jednostek testu wykryto. Jednokierunkowe warstwy jest używany do oceny.


### <a name="3-deployment"></a>3. Wdrożenie

Zobacz [wdrożenia](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Wdrożone usługi sieci web w klastrze w [usługi kontenera platformy Azure (ACS)](https://azure.microsoft.com/services/container-service/). Operationalization środowiska przepisy Docker i Kubernetes w klastrze, aby zarządzać wdrażaniem usługi sieci web. Można znaleźć więcej informacji na temat procesu operationalization [tutaj](model-management-service-deploy.md ).


## <a name="conclusion"></a>Podsumowanie

Poszliśmy za pośrednictwem szczegóły jak można uczenia modelu osadzania word przy użyciu algorytmu Word2Vec na Spark i użycie wyodrębnionego osadzeń jako funkcje do uczenia się sieci neuronowej głębokość wyodrębniania jednostki. Firma Microsoft zastosowano potoku szkolenia biomedycznych domeny. Jednak potoku jest rodzajowy ma zostać zastosowany do wykrywania typów jednostek niestandardowych z innej domeny. Wystarczy wystarczającej ilości danych i mogą łatwo dostosować przepływu pracy przedstawionych w tym miejscu w innej domenie.

## <a name="references"></a>Dokumentacja

* Mikolov Tomasowi, Kai Chen Corrado Gregowi i Jeffrey Dean. 2013a. Efektywne oszacowanie reprezentacje słowa w obszarze wektora. W postępowaniu ICLR.
* Tomasowi Mikolov, Ilya Sutskever Kai Chen, Corrado S Gregowi i Jeff Dean. 2013b. Rozproszone reprezentacje słów i wyrażeń oraz ich compositionality. W postępowaniu NIPS strony 3111 — 3119.
* Billy'emu Chiu, Gamal Crichton Anna Korhonen i Sampo Pyysalo. 2016. [Jak Train dobrej Word Osadzeń biomedycznych NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), 166 — 174 stron w postępowaniu 15 Workshop biomedycznych przetwarzania języka naturalnego.
* [Wektor reprezentacje słowa](https://www.tensorflow.org/tutorials/word2vec)
* [Powtarzającego się sieci Neuronowej](https://www.tensorflow.org/tutorials/recurrent)
* [Problemy z Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: wnioski](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

