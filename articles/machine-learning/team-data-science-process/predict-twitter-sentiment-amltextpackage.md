---
title: W usłudze Twitter klasyfikacji wskaźniki nastrojów klientów z pakietem Azure Machine Learning (AML) Analiza tekstu (AMLPTA) i zespołu danych nauki procesu (TDSP) | Dokumentacja firmy Microsoft
description: W tym artykule opisano stosowania TDSP (proces nauki zespołu danych) i AMLPTA klasyfikacji wskaźniki nastrojów klientów
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: 559af47bcf41cd6af59f8ba1b27ff8e64e849925
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296169"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Klasyfikacja wskaźniki nastrojów klientów z pakietem Azure Machine Learning (AML) Analiza tekstu (AMLPTA) i zespołu danych nauki procesu (TDSP) w usłudze Twitter

## <a name="introduction"></a>Wprowadzenie
Normalizacji struktury i analizy danych w dokumentacji projektów, który jest zakotwiczona ustalonych [analizy danych w cyklu życia](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), ma kluczowe znaczenie dla ułatwienia skutecznej współpracy w zespołach analizy danych.

Była wcześniej opublikowane [repozytorium GitHub TDSP struktury projektu i szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Firma Microsoft ma teraz włączone tworzenia projektów usługi Azure Machine Learning, które zostały utworzone z [TDSP struktury i dokumentacja szablonów dla usługi Azure Machine Learning](https://github.com/amlsamples/tdsp). Podano instrukcje dotyczące sposobu używania struktury TDSP i szablonów w usłudze Azure Machine Learning [tutaj](https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

W tym przykładzie zamierzamy pokazują obciążenie Azure Machine Learning pakiet dla Analiza tekstu i TDSP do opracowywania i wdrażania modeli predykcyjnych Twitter klasyfikacji wskaźniki nastrojów klientów.


## <a name="use-case"></a>Przypadek użycia
### <a name="twitter-sentiment-polarity-sample"></a>Przykładowe bieguna wskaźniki nastrojów klientów w usłudze Twitter
W tym artykule używa próbkę pokazanie sposobu tworzenia wystąpienia i wykonać projektu Machine Learning. Próbki używa struktury TDSP oraz szablonów usługi Azure Machine Learning Workbench. Kompletnego przykładu znajduje się w tym przewodniku. Zadanie modelowania prognozuje bieguna wskaźniki nastrojów klientów (dodatnie lub ujemne) przy użyciu tekstu z tweetów. W tym artykule opisano zadania modelowania danych, które zostały opisane w tym przewodnikiem. Instruktaż obejmuje następujące zadania:

1. Eksploracja danych, szkolenia i wdrażanie modelu uczenia maszynowego, który rozwiązać problem prognozowania opisaną w przeglądzie przypadków użycia. Wskaźniki nastrojów klientów, które dane są używane do wykonywania tych zadań w usłudze Twitter.
2. Realizacji projektu przy użyciu szablonu TDSP z usługi Azure Machine Learning dla tego projektu. Wykonanie projektu i raportowania cyklu życia TDSP jest używany.
3. Operationalization rozwiązanie bezpośrednio z usługi Azure Machine Learning usługi kontenera platformy Azure.

Projekt przedstawia użycie pakietu Analytics tekstu dla usługi Azure Machine Learning.


## <a name="link-to-github-repository"></a>Połącz z repozytorium GitHub
Łącze do repozytorium GitHub jest [tutaj](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Przeznaczenie
Głównym celem tego przykładu jest pokazują, jak utworzyć wystąpienia, a następnie wykonaj projektu przy użyciu struktury zespołu danych nauki procesu (TDSP) i szablonów w Azure Machine Learning pracy stanowisku uczenia maszynowego. W tym celu możemy użyć [danych w usłudze Twitter wskaźniki nastrojów klientów](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). Zadanie modelowania jest do prognozowania bieguna wskaźniki nastrojów klientów (dodatnie lub ujemne) przy użyciu tekstu z tweetów.

### <a name="scope"></a>Zakres
- Eksploracja danych, szkolenia i wdrażanie modelu uczenia maszynowego, której adres prognozowania problem opisany w przeglądzie przypadek użycia.
- Wykonanie projektu w usłudze Azure Machine Learning przy użyciu szablonu zespołu danych nauki procesu (TDSP) z usługi Azure Machine Learning dla tego projektu. Do wykonania projektu i raportowania chcemy użyć cyklu życia TDSP.
- Operationalization rozwiązanie bezpośrednio z usługi Azure Machine Learning w usługi kontenera platformy Azure.

Projekt zawiera wyróżnione kilka funkcji usługi Azure Machine Learning takie podczas tworzenia wystąpienia struktury TDSP i użycia, wykonanie kodu w Azure Machine Learning pracy stanowisku i łatwe operationalization w usługi kontenera platformy Azure przy użyciu rozwiązania Docker i Kubernetes.

## <a name="team-data-science-process-tds"></a>Proces nauki danych zespołu (TDS)
Używamy Szablony struktury i dokumentacji projektu TDSP do wykonania tego przykładu. Jest on zgodny [cyklu życia TDSP](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/lifecycle). Projekt zostanie utworzony zgodnie z instrukcjami [tutaj](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Omówienie przypadków użycia
Zadanie jest do prognozowania bieguna binarne wskaźniki nastrojów klientów każdego twitter przy użyciu funkcji osadzeń programu word wyodrębniony z tekstu w serwisie twitter. Aby uzyskać szczegółowe informacje, zapoznaj się to [repozytorium](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Uzyskiwanie danych i zrozumienie](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Pierwszym krokiem w tym przykładzie jest pobranie zestawu danych sentiment140 i podział pociągu i przetestować zestawów danych. Sentiment140 dataset zawiera rzeczywistej zawartości tweet (z emotikony usunięty) oraz bieguna każdego tweet (ujemna = 0, dodatnią = 4) również z neutralną tweetów usunięte. Wynikowe dane szkoleniowe ma 1.3 mln wierszy i testowanie danych ma 320 KB wierszy.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modelowanie](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Ta część próbki dalsze jest podzielona na trzy podczęści: 
- **Inżynieria** odpowiada Generowanie funkcji za pomocą różnych word osadzanie algorytmu, czyli Word2Vec. 
- **Tworzenie modelu** transakcje z szkolenia różne modele, takich jak _Regresja logistyczna_ i _gradientu zwiększania_ do prognozowania wskaźniki nastrojów klientów wejściowego tekstu. 
- **Model oceny** stosuje trenowanego modelu danych testowych.

#### <a name="feature-engineering"></a>Inżynieria funkcji
Używamy <b>Word2Vec</b> do generowania osadzeń programu word. Najpierw używamy algorytmu Word2Vec w trybie Skipgram zgodnie z objaśnieniem w tym dokumencie [Mikolov, Tomasowi, i wsp. Rozproszone reprezentacje słów i wyrażeń oraz ich compositionality. Postępy w neuronowej systemów przetwarzania informacji. 2013.](https://arxiv.org/abs/1310.4546) Aby wygenerować osadzeń programu word.

Pomiń gram jest skrócona sieci neuronowej biorąc word docelowy zakodowane jako jeden wektor gorących jako dane wejściowe i użyciem jej do prognozowania w pobliżu słowa. Jeśli rozmiar słownictwa jest V, a następnie rozmiar warstwy danych wyjściowych będzie __C * V__ gdzie C jest rozmiar okna kontekstu. Architektura Pomiń gramów na podstawie przedstawiono na poniższej ilustracji.
 
<table class="image" align="center">
<caption align="bottom">Pomiń gram modelu</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Szczegółowe informacje o modelu algorytmu i Pomiń gram Word2Vec wykraczają poza zakres tego przykładu i wymagane są zainteresowani czytelnicy podąża poniższe łącza, aby uzyskać więcej informacji. 02_A_Word2Vec.py kodu, do których odwołuje się [tensorflow przykłady](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Wektor reprezentację słowa](https://www.tensorflow.org/tutorials/word2vec)
* [Jak dokładnie działa word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Uwagi dotyczące szacowania Contrastive szumu i ujemną próbkowania](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

Po zakończeniu procesu szkolenia dwóch osadzanie plików w formacie TSV są generowane na etapie modelowania.

#### <a name="model-training"></a>szkolenie modelu
Gdy wektory word zostały wygenerowane za pomocą algorytmu SSWE lub Word2vec, następnym krokiem jest uczenia modele klasyfikacji do prognozowania bieguna rzeczywiste wskaźniki nastrojów klientów. Trwa stosowanie dwa typy funkcji: Word2Vec i SSWE na dwa modele: Regresja logistyczna i Convolutional sieci Neuronowej (CNN). 

#### <a name="model-evaluation"></a>Ocena modelu
Udostępniamy kodu na temat obciążenia i ocenić wielu przeszkolone modele testowego zestawu danych.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Wdrożenie](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Ta część możemy zawierają wskaźniki do instrukcji na temat operacjonalizacji modelu prognozowania wstępnie przeszkolone wskaźniki nastrojów klientów do usługi sieci web w klastrze w usługi kontenera platformy Azure (AKS). Operationalization środowiska przepisy Docker i Kubernetes w klastrze, aby zarządzać wdrażaniem usługi sieci web. Można znaleźć dodatkowe informacje na temat procesu operationalization [tutaj](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Podsumowanie
Poszliśmy za pośrednictwem szczegółowe informacje na temat sposobu train model osadzania word przy użyciu Word2Vec, a następnie użyć wyodrębnionego osadzeń jako funkcji w celu przeszkolenia dwa różne modele do prognozowania danych tekstowych Twitter wynik wskaźniki nastrojów klientów. Jeden z tych modeli jest wdrażana w usługi kontenera platformy Azure (AKS). 

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj więcej dokumentacji na [Azure maszyny Learning pakietu dla analityka tekst (AMLPTA)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) i [zespołu danych nauki procesu (TDSP)](https://aka.ms/tdsp) rozpocząć pracę.

## <a name="references"></a>Dokumentacja
* [Zespołowe przetwarzanie danych dla celów naukowych](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Jak używać zespołu danych nauki procesu (TDSP) w usłudze Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Szablon projektu TDSP dla usługi Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Testów porównawczych Azure ML pracy](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [Mikolov, Tomasowi, i wsp. Rozproszone reprezentacje słów i wyrażeń oraz ich compositionality. Postępy w neuronowej systemów przetwarzania informacji. 2013.](https://arxiv.org/abs/1310.4546)
