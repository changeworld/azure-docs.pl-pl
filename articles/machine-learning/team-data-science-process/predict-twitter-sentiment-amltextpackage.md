---
title: Klasyfikowanie opinii z pakietem Azure Machine Learning (AML), analiza tekstu (AMLPTA) i Team Data Science naukowych w usłudze Twitter | Dokumentacja firmy Microsoft
description: W tym artykule opisano korzystanie z przetwarzania TDSP (zespołowego danych dla celów naukowych) i AMLPTA klasyfikacji opinii
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: acad053712d237fdb86934faa7c5c4546c630387
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394565"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Klasyfikowanie opinii z pakietem Azure Machine Learning (AML), analiza tekstu (AMLPTA) i Team Data Science naukowych w usłudze Twitter

## <a name="introduction"></a>Wprowadzenie
Normalizacji struktury i dokumentacji do nauki o danych projektów, to znaczy zakotwiczona ustanowionych [cykl życia analizy danych](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), ma kluczowe znaczenie dla ułatwienia skutecznych współpracy w usłudze teams do nauki o danych.

Firma Microsoft ma wcześniej wydane [repozytorium GitHub przeznaczone dla przetwarzania TDSP struktury projektu i szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Teraz włączyliśmy tworzenia projektów usługi Azure Machine Learning, które są tworzone za pomocą [przetwarzania TDSP struktury i dokumentacji szablonów dla usługi Azure Machine Learning](https://github.com/amlsamples/tdsp). Podano instrukcje dotyczące sposobu używania przetwarzania TDSP struktury i szablony w usłudze Azure Machine Learning [tutaj](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

W tym przykładzie użyjemy pokazują użycie usługi Azure Machine Learning pakiet dla analizy tekstu i przetwarzania TDSP w celu opracowywania i wdrażania modeli predykcyjnych pozwalających na klasyfikacji opinii w serwisie Twitter.


## <a name="use-case"></a>Przypadek użycia
### <a name="twitter-sentiment-polarity-sample"></a>Przykładowe bieguna tonacji w usłudze Twitter
W tym artykule używa próbkę do pokazują, jak utworzyć wystąpienia i wykonaj projekt usługi Machine Learning. W przykładzie użyto przetwarzania TDSP struktury i szablony w programie Azure Machine Learning Workbench. Pełny przykład znajduje się w tym przewodniku. Zadanie modelowania przewiduje bieguna tonacji (dodatnia lub ujemna) przy użyciu tekstu z tweetów. W tym artykule opisano zadania modelowania danych, które są opisane w tym przewodnikiem. Instruktaż obejmuje następujące zadania:

1. Eksplorowanie danych, szkolenia i wdrażania modelu uczenia maszynowego, rozwiązać problem prognozowania, opisaną na stronie Przegląd przypadków użycia. Wskaźniki nastrojów klientów, których dane są używane do wykonywania tych zadań w usłudze Twitter.
2. Wykonywanie projektu za pomocą szablonu TDSP w usłudze Azure Machine Learning dla tego projektu. Wykonywanie projektu i raportowania cyklu życia przetwarzania TDSP jest używana.
3. Operacjonalizacja rozwiązania bezpośrednio z usługi Azure Machine Learning w usłudze Azure Container Service.

Projekt prezentuje użycie pakietu analizy tekstu dla usługi Azure Machine Learning.


## <a name="link-to-github-repository"></a>Połącz z repozytorium GitHub
Link do repozytorium GitHub [tutaj](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Przeznaczenie
Głównym celem tego przykładu jest przedstawiają sposób tworzenia wystąpienia i wykonywania usługi machine learning projekt za pomocą Team Data Science naukowych struktury i szablony w kanapie uczenia pracy w programie Azure maszyny. W tym celu użyjemy [dane w usłudze Twitter opinii](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). Zadanie modelowania jest prognozowanie, Biegun tonacji (dodatnia lub ujemna) przy użyciu tekstu z tweetów.

### <a name="scope"></a>Zakres
- Eksploracja danych, szkolenia i wdrażania modelu uczenia maszynowego, która rozwiązać problem prognozowania opisanego w omówieniu przypadku użycia.
- Wykonywanie projektu w usłudze Azure Machine Learning, używając szablonu Team Data Science naukowych z usługi Azure Machine Learning dla tego projektu. Wykonywanie projektu i raportowania będziemy używać cyklu przetwarzania TDSP.
- Operacjonalizacja rozwiązania bezpośrednio z usługi Azure Machine Learning w usłudze Azure Container Service.

Projekt prezentuje kilka funkcji usługi Azure Machine Learning, takie tworzenia wystąpienia struktury przetwarzania TDSP i użycia, wykonywanie kodu w usługi Azure Machine Learning pracy stanowisku i łatwe operacjonalizacji w usłudze Azure Container Service przy użyciu platformy Docker i Kubernetes.

## <a name="team-data-science-process-tds"></a>Zespołowe przetwarzanie danych naukowych (TDS)
Korzystamy z przetwarzania TDSP struktury i dokumentacji szablonów projektów do wykonania tego przykładu. Jest zgodna z [przetwarzania TDSP w cyklu życia](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). Projekt zostanie utworzony w oparciu o instrukcje dostarczone [tutaj](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Omówienie przypadków użycia
Zadanie jest przewidzieć bieguna binarne tonacji każdej usługi twitter za pomocą funkcji osadzenia word wyodrębnione z tekstu w usłudze twitter. Szczegółowy opis można znaleźć na stronie to [repozytorium](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Pozyskiwanie danych i opis](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Pierwszym krokiem, w tym przykładzie jest pobieranie zestawu danych sentiment140 i podziel go na szkolenie i testowanie zestawów danych. Sentiment140 zestaw danych zawiera rzeczywistą zawartość tweetu (z usunięta emotikony) wraz z bieguna każdego tweetu (ujemne = 0, dodatnią = 4), za pomocą neutralne tweetów usunięte. Wynikowe dane szkoleniowe ma 1,3 miliona wierszy i testowanie danych ma 320 tysięcy wierszy.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modelowanie](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Ta część próbki jest podzielona na trzy podczęści: 
- **Inżynieria funkcji** odnosi się do generacji funkcji za pomocą różnych word osadzania algorytm, a mianowicie Word2Vec. 
- **Tworzenie modelu** transakcji za pomocą szkoleń w różnych modeli, takie jak _regresji logistycznej_ i _gradientu zwiększania wyniku_ do prognozowania tonacji tekstu wejściowego. 
- **Model oceny** stosuje uczonego modelu za pośrednictwem danych testowych.

#### <a name="feature-engineering"></a>Inżynieria funkcji
Używamy <b>Word2Vec</b> do generowania osadzenia wyrazów. Najpierw używamy algorytm Word2Vec w trybie Skipgram zgodnie z opisem w dokumencie [Mikolov, Tomasowi, et al. Rozproszone reprezentacje słów i fraz i ich compositionality. Postęp w neuronowych systemów przetwarzania informacji. 2013.](https://arxiv.org/abs/1310.4546) Aby wygenerować osadzenia wyrazów.

Pomiń gram to skrócona sieci neuronowych, biorąc word docelowej zakodowane jako jeden z gorącej wektorów jako dane wejściowe i używanie ich do prognozowania w pobliżu słowa. Jeśli V rozmiar słownictwa, wówczas będzie rozmiar warstwy danych wyjściowych __C * V__ gdzie C jest rozmiar okna kontekstu. Architektura Pomiń gram, na podstawie przedstawiono na poniższej ilustracji.
 
<table class="image" align="center">
<caption align="bottom">Pomiń gram modelu</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Szczegółowe informacje o modelu algorytmu i Pomiń gram Word2Vec wykraczają poza zakres tego przykładu i zainteresowanym osobom przeglądającym są żądane za pośrednictwem następujących linków, aby uzyskać więcej informacji. 02_A_Word2Vec.py kodu, do których odwołuje się [tensorflow przykłady](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Wektor reprezentacja słów](https://www.tensorflow.org/tutorials/word2vec)
* [Jak dokładnie działa word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Uwagi dotyczące szacowania Contrastive hałasu i ujemne próbkowania](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

Po zakończeniu procesu uczenia, dwa pliki osadzania w formacie TSV są generowane dla etapu modelowania.

#### <a name="model-training"></a>Szkolenie modelu
Gdy wektory słowa zostały wygenerowane za pomocą algorytmu SSWE lub Word2vec, następnym krokiem jest szkolenie modeli klasyfikacji do prognozowania bieguna rzeczywiste wskaźniki nastrojów klientów. Możemy zastosować dwa typy funkcji: Word2Vec i SSWE do dwóch modeli: regresji logistycznej i Splotowej sieci Neuronowej (CNN). 

#### <a name="model-evaluation"></a>Ocena modelu
Firma Microsoft zapewnia kod na temat ładowania i ocenić wiele przeszkolone modele na testowego zestawu danych.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Wdrożenie](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Ta część udostępniamy wskaźników, aby uzyskać instrukcje na temat jak operacjonalizować model predykcyjny wstępnie przeszkolonych tonacji do usługi sieci web w taki sposób, w klastrze w usłudze Azure Container Service (AKS). Środowisko obsługi operacji Inicjuje obsługę platformy Docker i Kubernetes w klastrze, aby zarządzać wdrożeniem usługi sieci web. Można znaleźć więcej informacji na temat procesu operacjonalizacji [tutaj](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Podsumowanie
Firma Microsoft kolejno przerobione szczegółowe informacje na temat sposobu uczenia modelu osadzania programu word, przy użyciu Word2Vec, a następnie użyj wyodrębnione osadzenia jako funkcje to w opracowywaniu dwa różne modele do prognozowania tonacji danych serwisu Twitter na tekst. Jedną z tych modeli jest wdrażany w usługi kontenera platformy Azure (AKS). 

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z dokumentacją na [Azure Machine Learning pakietu analizy tekstu (AMLPTA)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) i [Team Data Science naukowych](https://aka.ms/tdsp) na rozpoczęcie pracy.

## <a name="references"></a>Dokumentacja
* [Zespołowe przetwarzanie danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Jak używać Team Data Science naukowych w usłudze Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Szablon projektu przetwarzania TDSP dla usługi Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Usługa Azure ML pracy testów porównawczych](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov, Tomasowi, et al. Rozproszone reprezentacje słów i fraz i ich compositionality. Postęp w neuronowych systemów przetwarzania informacji. 2013.](https://arxiv.org/abs/1310.4546)
