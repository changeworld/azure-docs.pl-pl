---
title: Rozpoznawanie jednostek biomedycznych — zespołu danych dla celów naukowych — usługa Azure Machine Learning | Dokumentacja firmy Microsoft
description: W procesie nauki o danych zespołu projektu Przewodnik Szybki Start używająca uczenia głębokiego na potrzeby rozpoznawanie jednostek biomedycznych w aplikacji Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 0ca8e1081b514d5569c84a6364d55e8f49bee533
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239009"
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Rozpoznawanie jednostek biomedycznych przy użyciu szablonu Team Data Science naukowych

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Wyodrębnianie jednostek jest podzadaniem wyodrębniania informacji (znany także jako [rozpoznawania nazwane jednostek (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), jednostki segmentu i identyfikator jednostki). W tym scenariuszu rzeczywistych ma na celu wyróżnić jak rozwiązywania skomplikowanych zadań przetwarzania języka naturalnego (NLP) takich jak działania funkcji wydobywania podmiotów z tekstu bez struktury za pomocą usługi Azure Machine Learning Workbench:

1. W jaki sposób to w opracowywaniu słowem neuronowej osadzenia modelu na korpus tekstu programu o 18 milionów PubMed streszczenia przy użyciu [implementacji Spark Word2Vec](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Jak utworzyć szczegółowe modelu powtarzającego się sieci neuronowej długie krótkoterminowe pamięci (LSTM) dla działania funkcji wydobywania podmiotów na obsługą procesorów GPU platformy Azure maszyny wirtualnej analizy danych (maszyny Wirtualnej DS procesora GPU) na platformie Azure.
2. Pokazują, że modelu osadzenia word specyficznego dla domeny można przewyższyć modeli osadzenia ogólnego programu word w zadaniu rozpoznawania jednostek. 
3. Pokazują, jak i operacjonalizuj modele uczenia głębokiego przy użyciu usługi Azure Machine Learning Workbench.

4. Pokazują następujące funkcje w ramach usługi Azure Machine Learning Workbench:

    * Wystąpienia [Team Data Science naukowych struktury i szablony](how-to-use-tdsp-in-azure-ml.md)
    * Automatyczne zarządzanie zależności projektu w tym pliki do pobrania i instalacji
    * Wykonywanie skryptów w języku Python w środowiskach obliczeniowej
    * Uruchom śledzenie danych historycznych dotyczących skryptów w języku Python
    * Wykonywanie zadań na platformie Spark w zdalnym obliczenia kontekstów przy użyciu klastrów usługi HDInsight Spark 2.1
    * Wykonywanie zadań w zdalnym maszynach wirtualnych procesorów GPU na platformie Azure
    * Łatwe operacjonalizacji modeli uczenia głębokiego w postaci usług sieci web w usłudze Azure Container Services (ACS)

## <a name="use-case-overview"></a>Omówienie przypadków użycia
Rozpoznawanie jednostek biomedycznych nazwanych jest niezwykle ważne dla złożonych biomedycznych zadań NLP, takich jak: 
* Wyodrębnianie wzmianki nazwane jednostek takie choroby, farmaceutycznych, chemiczne i objawy elektronicznych rekordów medycznych lub kondycji.
* Substancji leczniczych
* Informacje o interakcji między różne jednostki typy takie jak interakcji leków drug, drug choroby relacji i białka gene relacji.

Nasze scenariuszami przypadków użycia koncentruje się na sposób dużej ilości danych bez struktury korpus, takich jak streszczenia Medline PubMed mogą być analizowane w taki sposób, to w opracowywaniu wyrazem osadzania modelu. Następnie osadzenia dane wyjściowe są traktowane jako funkcje automatycznie generowanych do nauczenia ekstraktor neuronowych jednostki.

Nasze wyniki wskazują, że szkoleń modelowych wyodrębniania rozpoznawanie jednostek biomedycznych wyraz specyficznego dla domeny osadzania funkcji przewyższa stosowane przekształcania modelu skonfigurowanych pod kątem w typu rodzajowego funkcji. Modelu specyficznego dla domeny, można wykrywać 7012 jednostek poprawnie (poza 9475) wynik F1 0.73 5274 jednostek w porównaniu z F1 wynik 0,61 rodzajowy modelu.

Na poniższej ilustracji przedstawiono architekturę, który został użyty do przetwarzania danych i uczenie modeli.

![Architektura](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Opis danych

### <a name="1-word2vec-model-training-data"></a>1. Dane szkoleniowe modelu Word2Vec
Najpierw pobraliśmy dane pierwotne MEDLINE abstrakcyjna z [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Dane są publicznie dostępne w postaci plików XML na ich [serwera FTP](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Na serwerze są dostępne 892 plików XML i każdy z plików XML ma informacje o ponad 30 000 artykułów. Więcej informacji na temat kroku zbierania danych znajdują się w sekcji struktury projektu. Te pola, które są obecne w każdym pliku to 
        
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

### <a name="2-lstm-model-training-data"></a>2. Dane szkoleniowe modelu LSTM

Model wyodrębniania neuronowych jednostki skonfigurowanych pod kątem i dokonano publicznie dostępnych zestawów danych. Aby uzyskać szczegółowy opis o tych zestawów danych, można odwoływać się do następujących źródeł:
 * [Biografia jednostki zadania rozpoznawania BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Korpus zadań BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 — zadanie 9.1 (leków rozpoznawanie)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Połącz z repozytorium GitHub w galerii systemu Azure
Oto link do publicznego repozytorium GitHub w ramach rzeczywistych scenariuszy, która zawiera kod i bardziej szczegółowego opisu: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Wymagania wstępne 

* Azure [subskrypcji](https://azure.microsoft.com/free/)
* Usługa Azure Machine Learning Workbench. Zobacz [Przewodnik instalacji](quickstart-installation.md). Obecnie Azure Machine Learning Workbench można zainstalować w następujących systemach operacyjnych tylko: 
    * Windows 10 lub Windows Server 2016
    * System macOS Sierra (lub nowszy)


### <a name="azure-services"></a>Usługi platformy Azure
* [Klaster HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) wersją Spark 2.1 w systemie Linux (HDI 3.6) do obliczeń skalowalnego w poziomie. Do przetworzenia całej kwoty streszczenia MEDLINE omówiono poniżej, potrzebne minimalnej konfiguracji: 
    * Węzeł główny: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) rozmiar
    * Węzły procesu roboczego: co najmniej 4 [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). W naszych pracach użyliśmy 11 węzłów procesu roboczego D12_V2 rozmiaru.
* [NC6 danych do analizy Virtual Machine (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) obliczeń skalowanie w górę.

### <a name="python-packages"></a>Pakiety języka Python

Wszystkie wymagane zależności są definiowane w pliku aml_config/conda_dependencies.yml w folderze projektu scenariusza. Zależności zdefiniowane w tym pliku są automatycznie konfigurowani do uruchomienia przed platformy docker, maszyny Wirtualnej i usługi HDI klastra elementów docelowych. Aby uzyskać szczegółowe informacje o formacie pliku środowiska Conda, zapoznaj się [tutaj](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Biblioteki Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Podstawowe instrukcje dotyczące usługi Azure Machine Learning (AML) w aplikacji workbench
* [Omówienie](../service/overview-what-is-azure-ml.md)
* [Instalacja](quickstart-installation.md)
* [Przy użyciu przetwarzania TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Jak odczytywać i zapisywać pliki](how-to-read-write-files.md)
* [Jak używać notesów programu Jupyter](how-to-use-jupyter-notebooks.md)
* [Sposób użycia procesora GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Struktura scenariusza
W scenariuszu, korzystamy z przetwarzania TDSP struktury i dokumentacji szablonów projektów (rysunek 1), które następuje [przetwarzania TDSP w cyklu życia](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Projekt jest oparta na instrukcjami [tutaj](./how-to-use-tdsp-in-azure-ml.md).


![Wypełnij informacje o projekcie](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Przepływ pracy do nauki o danych krok po kroku jest następująca:

### <a name="1-data-acquisition-and-understanding"></a>1. Pozyskiwanie danych i ich analiza

Zobacz [danych pozyskiwania i zrozumienia](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

Nieprzetworzone korpus MEDLINE ma daje w sumie streszczenia 27 milionów gdzie artykuły około 10 milionów mają puste pole abstrakcyjne. Usługi Azure HDInsight Spark zostanie użyty do przetwarzania danych big data, którego nie można załadować do pamięci z jednego komputera jako [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Po pierwsze dane są pobierane do klastra Spark. Poniższe kroki są wykonywane w, a następnie [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* Analizowanie plików XML przy użyciu analizatora XML Medline
* Przetwarzanie wstępne abstrakcyjne tekstu, w tym dzielenie zdania, tokenizacji i normalizacji wielkości liter.
* Wyklucz artykuły, gdzie pole abstrakcyjnej jest pusty lub ma krótki tekst 
* Tworzenie słownika programu word z streszczenia szkolenia
* szkolenie word osadzania neuronowych modelu. Aby uzyskać więcej informacji, zobacz [link do kodu GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) na rozpoczęcie pracy.


Po zakończeniu analizowania plików XML, dane ma następujący format: 

![Próbka danych](./media/scenario-tdsp-biomedical-recognition/datasample.png)

Model wyodrębniania neuronowych jednostki skonfigurowanych pod kątem i dokonano publicznie dostępnych zestawów danych. Aby uzyskać szczegółowy opis o tych zestawów danych, można odwoływać się do następujących źródeł:
 * [Biografia jednostki zadania rozpoznawania BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Korpus zadań BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 — zadanie 9.1 (leków rozpoznawanie)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modelowanie

Zobacz [modelowania](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modelowanie jest na etapie, w którym pokazano, jak korzystać z danych pobranego w poprzedniej sekcji dotyczącej szkolenia własnych word osadzania modelu i używać go do innych zadań podrzędnych. Chociaż używamy danych PubMed potoku, aby nie generować osadzenia jest ogólny i mogą być ponownie używane do nauczenia osadzenia wyrazów do innej domeny. Aby uzyskać osadzenia być dokładne reprezentację danych istotne jest, że word2vec zostały przeszkolone na dużą ilość danych.
Gdy będziemy już mieć gotowy osadzenia wyrazów, firma Microsoft uczenie modelu sieci neuronowej, który wykorzystuje nauczony osadzenia zainicjować warstwy osadzania. Firma Microsoft oznaczyć osadzania warstwy jako trainable, ale nie jest obowiązkowe. Szkolenie word osadzania modelu jest nienadzorowane, i dlatego możemy korzystać z zalet teksty bez etykiety. Jednakże szkolenie modeli rozpoznawania jednostek jest zadaniem uczenia nadzorowanego i jej dokładność zależy od kwoty oraz określa jego jakość danych ręcznie dodawać adnotacje. 


#### <a name="21-feature-generation"></a>2.1. Generowanie funkcji

Zobacz [funkcji generowania](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec to słowa, osadzanie algorytmu uczenia nienadzorowanych, który przygotowuje modelu sieci neuronowej z głównej części szkolenia bez etykiety. Generuje wektor ciągłe dla każdego wyrazu w głównej części, który reprezentuje jej informacje semantyczne. Te modele są proste sieci neuronowych, za pomocą pojedynczego ukrytej warstwie. Wektory/osadzenia wyrazów są rozpoznawane przez różnicowaniem i stochastycznego spadku gradientu. Istnieją dwa typy modeli word2vec, to znaczy, Gram Pomiń i ciągłe — zbiór z wyrazy (Sprawdź [tutaj](https://arxiv.org/pdf/1301.3781.pdf) Aby uzyskać więcej informacji). Ponieważ używamy implementacji MLlib word2vec, który obsługuje model Pomiń gram, firma Microsoft Zwięźle opisz ją tutaj (obraz z [łącze](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Pomiń Gram modelu](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

Model wykorzystuje Softmax hierarchiczne i ujemne próbkowania w celu zoptymalizowania wydajności. Hierarchiczna SoftMax (H-SoftMax) jest przybliżeniem przez drzew binarnych. H SoftMax zasadniczo zamienia warstwy płaski SoftMax hierarchiczne warstwy, która zawiera wyrazy, jak pozostawia. Dzięki temu do rozkładania obliczanie prawdopodobieństwa o jeden wyraz w sekwencji obliczeń prawdopodobieństwa oszczędza nam konieczności Oblicz między kosztowne normalizacji wszystkie wyrazy. Ponieważ o zrównoważonym obciążeniu drzewa binarnego głębokość log2 (| V |) (V jest słownictwa), potrzebujemy do oceny, co najwyżej log2 (| V |) węzły można uzyskać końcowego prawdopodobieństwo, że wyraz. Prawdopodobieństwo, że w program word, biorąc pod uwagę jej c kontekstu jest po prostu produktu prawdopodobieństwa podjęcia prawo, a następnie po lewej stronie odpowiednio konwertuje, które zachęcają do jego węzeł liścia. Firma Microsoft można utworzyć drzewa Huffmana na podstawie częstotliwości słów w zestawie danych, aby mieć pewność, że wyrazy częściej reprezentacje krótsze. Aby uzyskać więcej informacji, zobacz [ten link](http://sebastianruder.com/word-embeddings-softmax/).
Obraz z [tutaj](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Wizualizacja

Gdy będziemy już mieć osadzenia wyrazów, prosimy o poświęcenie Utwórz ich wizualizację i zobaczyć relację pomiędzy semantycznie podobne słowa. 

![Podobieństwa W2V](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Firma Microsoft wykazały dwa różne sposoby wizualizowania osadzenia. Pierwsza z nich korzysta UPW do projektu wysokiej wektor wymiarowej przestrzeni wektor 2-D. Prowadzi to do znaczącą utratę informacji i wizualizacji nie jest tak dokładny. Druga służy wykorzystaniu UPW z [Oddelegowany t](https://distill.pub/2016/misread-tsne/). t Oddelegowany to technika redukcji nieliniowych wymiarach, która dobrze nadaje się do osadzania o wielu wymiarach danych do obszaru dwóch lub trzech wymiarów, które mogą być następnie wizualizowane w wykres punktowy.  Modeluje ona każdego obiektu o wielu wymiarach, dwóch lub trzech dimensional punkt w taki sposób, że podobne obiekty są modelowane przez pobliskich punkty i różne obiekty są modelowane przez punkty w odległości. Działa w dwóch częściach. Najpierw tworzy rozkład prawdopodobieństwa za pośrednictwem pary w wyższej przestrzeni trójwymiarowej w taki sposób, że podobne obiekty mają wysokie prawdopodobieństwo pobierania i różne punkty mają niskie prawdopodobieństwo wprowadzenie pobrane. Po drugie definiuje podobne rozkładu prawdopodobieństwa punktów na mapie wymiarowego niski i minimalizuje KL zapobiec możliwemu wystąpieniu rozbieżności między dwoma dystrybucje względem lokalizacji punktów na mapie. Położenie punktów w wymiarze niskie są uzyskiwane, minimalizując rozbieżności KL przy użyciu spadku gradientu. Ale Oddelegowany t może nie być zawsze niezawodne. Można znaleźć szczegółów implementacji [tutaj](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Jak pokazano na poniższej ilustracji, wizualizacja Oddelegowany t zapewnia większe rozdzielenie wektorów programu word i potencjalne wzorców klastrowania. 


* Wizualizacja dzięki analizie PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Wizualizacja w za pomocą języka t Oddelegowany

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Punkty najbliżej "Raka" (są one wszystkie podtypy raka)

![Punkty najbliżej raka](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Szkolenie ekstraktor neuronowych jednostki

Zobacz [szkolenie ekstraktor neuronowych jednostki](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

Architektura sieci neuronowej do przodu kanału informacyjnego odczuwa problem traktują każdy dane wejściowe i wyjściowe jako niezależne od innych dane wejściowe i wyjściowe. W tej architekturze nie można modelować sekwencji do sekwencji zadań etykietowania, takich jak tłumaczenia maszynowego i działania funkcji wydobywania podmiotów. Modele sieci neuronowych powtarzającego się rozwiązać ten problem, ponieważ przekazywanie informacji obliczane co do momentu teraz do kolejnego węzła. Właściwość ta jest wywoływana, mających pamięci w sieci, ponieważ jest w stanie użyć wcześniej obliczanej informacji, jak pokazano na poniższej ilustracji:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Rnn podstawowego, ale faktycznie cierpią [podczas wykonywania gradientu Problem](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) ze względu na które one nie jest możliwe korzystanie z wszystkich informacji o przejrzane wcześniej. Problem staje się oczywisty, tylko wtedy, gdy dużą ilość kontekstu jest wymagany do prognozowania. Ale modeli, takie jak LSTM cierpią taki problem, w rzeczywistości są one przeznaczone do zapamiętania długoterminowe zależności. W przeciwieństwie do wanilii Rnn, które mają jednej sieci neuronowych LSTMs mają interakcje między cztery sieci neuronowych dla każdej komórki. Aby uzyskać szczegółowy opis działania LSTM dotyczą [ten wpis](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![Komórka LSTM](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Spróbujmy dozą własną LSTM na podstawie powtarzającego się sieci neuronowej a następnie spróbuj umożliwiają typów jednostek, takie jak wzmianki drug, chorób i objaw z PubMed danych. Pierwszym krokiem jest uzyskanie dużej ilości danych oznaczonych i jak można będzie mieć złamać, który nie jest łatwe. Większość danych medycznych zawiera wiele ważnych informacji dotyczących osoby i dlatego nie są publicznie dostępne. Polegamy na kombinację dwóch różnych zestawów danych, które są publicznie dostępne. Pierwszy zestaw danych pochodzi z 2013 Semeval — zadanie 9.1 (leków rozpoznawanie), a drugi to BioCreative V CDR zadania. Firma Microsoft łączenia i automatycznie etykietowania tymi dwoma zestawami danych, można wykryć zarówno farmaceutycznych, jak i chorób z teksty medycznych i ocenić nasze osadzenia wyrazów. Szczegóły implementacji, można znaleźć [link do kodu GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

Architektura modelu, który użyliśmy wszystkie kody i porównanie przedstawiono poniżej. Parametr, który zmienia się dla różnych zestawów danych jest sekwencji maksymalną długość (w tym miejscu 613).

![LSTM model](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Ocena modelu
Zobacz [modelu oceny](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Używamy skryptu oceny udostępnionych zadań [zadania rozpoznawania biografia jednostki w 2004 NLP/NLPBA biografia](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) do oceny dokładności, odwołania i ocena F1 modelu. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>Domeny i ogólny program word osadzania modeli

Poniżej przedstawiono porównanie dokładność dwa typy funkcji: osadzenia wyrazów (1), skonfigurowanych pod kątem w streszczenia PubMed i osadzenia wyrazów (2) skonfigurowanych pod kątem w grup dyskusyjnych Google. Wyraźnie widzimy, że model w domenie przewyższa stosowane przekształcania model ogólny. Dlatego o wybranym słowem osadzania modelu, a nie przy użyciu jednego ogólnego jest znacznie bardziej użyteczne. 

* Zadanie #1: Leki i wykrywanie chorób

![Porównanie modeli 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Firma Microsoft przeprowadzania oceny osadzenia wyrazów innych zestawów danych w podobny sposób i zobacz, że zawsze jest lepiej tego modelu w domenie.

* Zadanie #2: Białek, wiersz komórek, typ komórki, DNA i wykrywanie RNA

![Porównanie modeli 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Zadanie #3: Chemiczne i wykrywanie chorób

![Porównanie modeli 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Zadanie #4: Wykrywanie leki

![Porównanie modeli 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Zadanie #5: Wykrywanie genów

![Porównanie modeli 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow i CNTK
Wszystkie zgłoszone modele są uczone z użyciem Keras TensorFlow jako wewnętrznej bazy danych. Keras z CNTK wewnętrznej bazy danych nie obsługuje "Odwróć", w czasie tej pracy zostało wykonanej. W związku z tym w celu porównania, firma Microsoft ma skonfigurowanych pod kątem jednokierunkowe modelu LSTM z zapleczem CNTK i porównuje go jednokierunkowe modelu LSTM z zapleczem TensorFlow. Zainstaluj CNTK w wersji 2.0 dla biblioteki Keras z [tutaj](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Porównanie modeli 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Firma Microsoft zawartych, że CNTK działa jako dobre, jak Tensorflow, zarówno pod względem szkolenia czas na epoki (60 sekund, CNTK i Tensorflow w sekundach 75) oraz liczby jednostek testowych wykryte. Używamy jednokierunkowe warstwy do oceny.


### <a name="3-deployment"></a>3. Wdrożenie

Zobacz [wdrożenia](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Wdrożenie usługi sieci web w taki sposób, w klastrze w [usługi Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Środowisko obsługi operacji Inicjuje obsługę platformy Docker i Kubernetes w klastrze, aby zarządzać wdrożeniem usługi sieci web. Można znaleźć więcej informacji na temat procesu operacjonalizacji [tutaj](model-management-service-deploy.md ).


## <a name="conclusion"></a>Podsumowanie

Przeszliśmy na szczegóły sposobu można wytrenuj model osadzania programu word za pomocą algorytmu Word2Vec na platformie Spark i następnie użyć wyodrębnione osadzenia jako funkcji uczenie sieci neuronowej do działania funkcji wydobywania podmiotów. Potok szkolenia zostały zastosowane w domenie biomedycznych. Potok jest jednak rodzajowe mają być stosowane do wykryć typy jednostki niestandardowej z innej domeny. Po prostu potrzebujesz wystarczającej ilości danych i mogą łatwo dostosować przepływ pracy przedstawionych w tym miejscu w innej domenie.

## <a name="references"></a>Dokumentacja

* Mikolov Tomasowi Kai Chen, Greg Corrado i Jeffrey Dean. 2013a. Efektywne oszacowanie reprezentacje programu word w przestrzeni wektor. W postępowaniu ICLR.
* Mikolov Tomasowi Ilyę Sutskevera, Kai Chen, Greg S Corrado i Jeff Dean. 2013b. Rozproszone reprezentacje słów i fraz i ich compositionality. W postępowaniu NIPS strony 3111 — 3119.
* Billy Chiu Gamal Crichton, Anna Korhonen i Sampo Pyysalo. 2016. [Jak dobre osadzenia wyrazów szkolenie dla biomedycznych NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), postępowania w piętnasty warsztatów na biomedycznych przetwarzania języka naturalnego, strony 166 — 174.
* [Wektor reprezentacje słów](https://www.tensorflow.org/tutorials/word2vec)
* [Sieci neuronowych](https://www.tensorflow.org/tutorials/recurrent)
* [Problemy z Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: Lekcja wyniesiona](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

