---
title: Przewidywanie Twitter wskaźniki nastrojów klientów z programu word osadzeń przy użyciu procesu nauki zespołu danych na platformie Azure | Dokumentacja firmy Microsoft
description: Czynności, które są wymagane do wykonania projektów analizy danych.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: deguhath
ms.openlocfilehash: f47668cd706b78977418925d64eca583d7878cd3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838313"
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Przewidywanie Twitter wskaźniki nastrojów klientów z programu word osadzeń przy użyciu procesu nauki danych zespołu

W tym artykule przedstawiono sposób współpracy przy użyciu _Word2Vec_ word osadzanie algorytmu i _osadzanie Word specyficzne dla wskaźniki nastrojów klientów (SSWE)_ algorytmu do prognozowania Twitter wskaźniki nastrojów klientów z [Uczenie maszynowe azure](../service/index.yml). Aby uzyskać więcej informacji na Prognozowanie Twitter bieguna wskaźniki nastrojów klientów, zobacz [repozytorium MachineLearningSamples TwitterSentimentPrediction](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) w witrynie GitHub. Kluczem do ułatwienia współpraca z zespołem efektywne w przypadku projektów analizy danych jest normalizacji struktury i dokumentacji projektów z cyklem nawiązane połączenie analiz danych. [Zespołu danych nauki procesu (TDSP)](overview.md) zawiera tego typu strukturalnego [cyklu życia](lifecycle.md). 

Tworzenie projektów nauki danych z _szablonu TDSP_ zapewnia standardowych ramy dla projektów usługi Azure Machine Learning. Wcześniej wydane przez zespół TDSP [repozytorium GitHub TDSP struktury projektu i szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Teraz projektów uczenia maszynowego, które zostały utworzone z [TDSP szablonów dla usługi Azure Machine Learning](https://github.com/amlsamples/tdsp) są włączone. Aby uzyskać instrukcje, zobacz temat jak korzystać [TDSP struktury projektów przy użyciu szablonu TDSP](../desktop-workbench/how-to-use-tdsp-in-azure-ml.md) w usłudze Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Przykładowe bieguna wskaźniki nastrojów klientów w usłudze Twitter

W tym artykule używa próbkę pokazanie sposobu tworzenia wystąpienia i wykonać projektu Machine Learning. Próbki używa struktury TDSP oraz szablonów usługi Azure Machine Learning Workbench. Kompletnego przykładu znajduje się w [tego przewodnika](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). Zadanie modelowania prognozuje bieguna wskaźniki nastrojów klientów (dodatnie lub ujemne) przy użyciu tekstu z tweetów. W tym artykule opisano zadania modelowania danych, które zostały opisane w tym przewodnikiem. Instruktaż obejmuje następujące zadania:

- Eksploracja danych, szkolenia i wdrażanie modelu uczenia maszynowego, który rozwiązać problem prognozowania opisaną w przeglądzie przypadków użycia. [Wskaźniki nastrojów klientów danych w usłudze Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) służy do wykonywania tych zadań.
- Realizacji projektu przy użyciu szablonu TDSP z usługi Azure Machine Learning dla tego projektu. Wykonanie projektu i raportowania cyklu życia TDSP jest używany.
- Operationalization rozwiązanie bezpośrednio z usługi Azure Machine Learning usługi kontenera platformy Azure.

Projekt wyróżnia się następujące funkcje usługi Azure Machine Learning:

- Tworzenie wystąpień i używanie struktury TDSP.
- Wykonywanie kodu w konsoli usługi Azure Machine Learning Workbench.
- Łatwe operationalization w usłudze kontenera przy użyciu rozwiązania Docker i Kubernetes.

## <a name="team-data-science-process"></a>Zespołowe przetwarzanie danych dla celów naukowych
Do wykonania tego przykładu, Szablony struktury i dokumentacji projektu TDSP programu Azure Machine Learning Workbench. Implementuje próbki [cyklu życia TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), jak pokazano na poniższej ilustracji:

![Cykl życia TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

W konsoli usługi Azure Machine Learning Workbench na podstawie utworzeniu projektu TDSP [tych instrukcji](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), jak pokazano na poniższej ilustracji:

![Utwórz TDSP w konsoli usługi Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Uzyskiwanie danych i przygotowanie](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Pierwszym krokiem w tym przykładzie jest pobrać sentiment140 zestawu danych i dzielenia danych na celów szkoleniowych i testów zestawów danych. Zestaw danych sentiment140 zawiera rzeczywistej zawartości tweet (z emotikony usunięte). Zestaw danych zawiera także bieguna z każdym tweet (ujemna = 0, dodatnią = 4) z neutralną tweetów usunięte. Po podzieleniu danych, dane szkoleniowe ma 1.3 mln wierszy i testowania danych ma 320,000 wierszy.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Projektowanie modelu](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Następnym krokiem w próbce jest opracowanie modelu danych. Zadanie modelowania jest podzielone na trzy części:

- Inżynieria: generowanie funkcje dla modelu przy użyciu różnych word osadzanie algorytmów. 
- Tworzenie modelu: uczenia różne modele do prognozowania wskaźniki nastrojów klientów wejściowego tekstu. Przykłady te modele _Regresja logistyczna_ i _gradientu zwiększania_.
- Model oceny: ocena przeszkolone modeli danych testowych.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Funkcja zespołu inżynieryjnego](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Algorytmy Word2Vec i SSWE są używane do generowania osadzeń programu word. 


#### <a name="word2vec-algorithm"></a>Algorytm Word2Vec

W modelu Skip Gram jest używany algorytm Word2Vec. Ten model jest wyjaśnione w dokumencie przy użyciu Mikolov Tomasowi i wsp. "[Rozproszonych reprezentacje słów i wyrażeń oraz ich Compositionality. Postępy w neuronowej systemów przetwarzania informacji. ](https://arxiv.org/abs/1310.4546)" 2013.

Model Skip Gram jest skrócona sieci neuronowej. Dane wejściowe jest słowo docelowych, które są kodowane jako wektor hot jednego, który służy do prognozowania w pobliżu słowa. Jeśli **V** jest rozmiarem słownictwa, następnie rozmiar warstwy danych wyjściowych jest __C * V__ gdzie C jest rozmiar okna kontekstu. Na poniższej ilustracji przedstawiono architekturę, która jest oparta na modelu Skip Gram:

![Pomiń Gram modelu](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Szczegółowe mechanika algorytm Word2Vec i Pomiń Gram modelu wykraczają poza zakres tego przykładu. Aby uzyskać więcej informacji zobacz następujące informacje:

- [Kod 02_A_Word2Vec.PY, z którym związane są odwołania przykładami TensorFlow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Wektor reprezentacje słowa](https://www.tensorflow.org/tutorials/word2vec)
- [Jak dokładnie działa word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Uwagi dotyczące szacowania Contrastive szumu i ujemną próbkowania](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Osadzanie Word wskaźniki nastrojów klientów dotyczące algorytmu
Algorytm SSWE próbuje rozwiązać słabe strony algorytmu Word2Vec, gdzie wyrazów z kontekstami podobne i przeciwne bieguna może mieć podobne wektory programu word. Podobieństwa może spowodować algorytm Word2Vec są wykonywane prawidłowo dla zadań, takich jak analiza wskaźniki nastrojów klientów. Algorytm SSWE próbuje obsługi tego osłabienia przez włączenie funkcji utraty zarówno bieguna zdania, jak i kontekst słowo.

Przykład używa wariant algorytmu SSWE w następujący sposób:

- Zarówno oryginalny _ngram_ i uszkodzony _ngram_ są używane jako dane wejściowe.
- A Klasyfikacja styl zawiasów utraty funkcji jest używana dla utraty składni i utraty semantycznego.
- Funkcja ultimate utraty jest kombinacją ważoną utraty składni i utratę semantycznego.
- Dla uproszczenia tylko semantycznego cross entropii służy jako funkcję utraty.

Przykład pokazuje, że nawet w przypadku prostszych funkcję utraty wydajności osadzenia SSWE lepszym rozwiązaniem niż osadzanie Word2Vec. Na poniższej ilustracji przedstawiono model convolutional, który służy do generowania osadzanie word specyficzne dla wskaźniki nastrojów klientów:

![Modelu sieci neuronowej przez SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Po zakończeniu procesu szkolenia dwóch osadzanie plików w formacie wartości tabulatorami (TSV) są generowane na etapie modelowania.

Aby uzyskać więcej informacji na temat algorytmów SSWE, zobacz dokument przez Duyu Tang i wsp. "[Uczenia specyficzne dla wskaźniki nastrojów klientów programu Word osadzanie klasyfikacji wskaźniki nastrojów klientów Twitter](http://www.aclweb.org/anthology/P14-1146)." Skojarzenie Linguistics obliczeniową (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Tworzenie modelu](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Po wygenerowaniu wektory word za pomocą algorytmu SSWE lub Word2Vec modele klasyfikacji są uczone do prognozowania bieguna rzeczywiste wskaźniki nastrojów klientów. Dwa typy funkcji: Word2Vec i SSWE, są stosowane do dwa modele: zwiększania gradientu i Regresja logistyczna modelu. W związku z tym cztery różne modele są uczone.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Ocena modelu](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Po modele są uczone, modeli są używane do testowanie danych tekstowych Twitter i ocena wydajności każdego modelu. Przykład ocenia następujące cztery modele:

- Gradient Boosting za pośrednictwem SSWE osadzania.
- Regresja logistyczna za pośrednictwem SSWE osadzania.
- Gradient Boosting za pośrednictwem Word2Vec osadzania.
- Regresja logistyczna za pośrednictwem Word2Vec osadzania.

Porównanie wydajności cztery modele pokazano na poniższej ilustracji:

![Odbiornik operacyjnego charakterystyczny porównanie modeli (ROC)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Model gradientu zwiększanie wyniku z funkcji SSWE zapewnia najlepszą wydajność podczas porównywania modeli przy użyciu obszarze Metryka krzywej (AUC).


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Wdrożenie](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Ostatnim krokiem jest wdrożenie modelu prognozowania przeszkolone wskaźniki nastrojów klientów do usługi sieci web w klastrze usługi kontenera platformy Azure. Przykład używa zwiększania gradientu modelu przy użyciu algorytmu osadzania SSWE jako trenowanego modelu. W środowisku operationalization postanowienia Docker i Kubernetes w klastrze, aby zarządzać wdrażaniem usługi sieci web, jak pokazano na poniższej ilustracji: 

![Pulpit nawigacyjny rozwiązania Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Aby uzyskać więcej informacji na temat procesu operationalization, zobacz [wdrażanie usługi Azure Machine Learning model jako usługę sieci web](../desktop-workbench/model-management-service-deploy.md).

## <a name="conclusion"></a>Podsumowanie

W tym artykule przedstawiono sposób uczenie modelu przy użyciu osadzanie word przy użyciu algorytmów Word2Vec i osadzanie Word specyficzne dla wskaźniki nastrojów klientów. Wyodrębnionego osadzeń były używane jako funkcje do uczenia kilka modeli do przewidywania wyników wskaźniki nastrojów klientów w przypadku danych tekstowych Twitter. Funkcja SSWE używana z modelem gradientu zwiększania udzielił najlepszą wydajność. Model następnie została wdrożona jako usługę sieci web w czasie rzeczywistym usługi kontenera przy użyciu usługi Azure Machine Learning Workbench.


## <a name="references"></a>Dokumentacja

* [Zespołowe przetwarzanie danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Jak używać zespołu danych nauki procesu (TDSP) w usłudze Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Szablony projektów TDSP dla usługi Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Środowisko robocze usługi Azure Machine Learning](../service/index.yml)
* [USA przychody zestaw danych z repozytorium UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)
* [Rozpoznawanie jednostek biomedycznych przy użyciu szablonów TDSP](../desktop-workbench/scenario-tdsp-biomedical-recognition.md)
* [Mikolov, Tomasowi, i wsp. "Reprezentacje słów i wyrażeń oraz ich Compositionality rozproszonych. Zmienia informacje neuronowej systemów przetwarzania." 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, et al. "Uczenia wskaźniki nastrojów klientów specyficzne dla programu Word osadzanie klasyfikacji wskaźniki nastrojów klientów usługi Twitter". LISTY KONTROLI DOSTĘPU (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
