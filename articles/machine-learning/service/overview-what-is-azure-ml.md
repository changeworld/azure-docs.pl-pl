---
title: Co to jest usługa Azure Machine Learning? | Microsoft Docs
description: W tym temacie objaśniono podstawowe pojęcia dotyczące uczenia maszynowego w chmurze, opisano jego możliwe zastosowania i zdefiniowano terminy dotyczące uczenia maszynowego. Omówienie usługi Azure Machine Learning — zintegrowanego, kompleksowego rozwiązania do nauki o danych przeznaczonego dla profesjonalnych analityków zajmujących się opracowywaniem, testowaniem i wdrażaniem zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 868647780bafe9117a15891264a38535edff7250
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika przetwarzania danych, która umożliwia wykorzystanie przez komputery istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Za pomocą techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom lub przewidywaniom uzyskiwanym za pomocą uczenia maszynowego aplikacje i urządzenia są bardziej inteligentne. Podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, które mogą się spodobać kupującemu, na podstawie dotychczasowych zakupów. W przypadku płacenia kartą kredytową uczenie maszynowe porównuje transakcję z bazą danych transakcji i ułatwia wykrycie oszustwa. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została wykonana.

## <a name="what-is-azure-machine-learning"></a>Co to jest usługa Azure Machine Learning?
Usługa Azure Machine Learning to zintegrowane, kompleksowe rozwiązanie do nauki o danych i przeprowadzania zaawansowanych analiz. Pozwala ono analitykom przygotowywać dane, opracowywać eksperymenty i wdrażać modele na skalę chmury.

Oto główne składniki usługi Azure Machine Learning:
- Środowisko robocze usługi Azure Machine Learning
- Eksperymentowanie w usłudze Azure Machine Learning
- Zarządzanie modelami w usłudze Azure Machine Learning
- Biblioteki usługi Microsoft Machine Learning dla platformy Apache Spark (biblioteka MMLSpark)
- Narzędzia Visual Studio Code dla sztucznej inteligencji

Połączenie tych aplikacji i usług znacznie przyspiesza opracowywanie oraz wdrażanie projektów związanych z przetwarzaniem danych naukowych. 

![Pojęcia dotyczące usługi Azure Machine Learning](./media/overview-what-is-azure-ml/aml-concepts.png)


## <a name="open-source-compatible"></a>Zgodność z oprogramowaniem typu open source

Usługa Azure Machine Learning w pełni obsługuje technologie typu open source. Można korzystać z dziesiątków tysięcy pakietów open source języka Python, takich jak następujące struktury uczenia maszynowego:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Zestaw narzędzi usług Microsoft Cognitive](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Eksperymenty można przeprowadzać w środowiskach zarządzanych, takich jak kontenery Docker i klastry Spark. Aby przyspieszyć wykonywanie eksperymentów, można również użyć zaawansowanego sprzętu, takiego jak [maszyny wirtualne z włączonymi procesorami GPU na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

Usługa Azure Machine Learning jest oparta na następujących technologiach typu open source:

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Zawiera także technologie typu open source firmy Microsoft, takie jak [biblioteka usługi Microsoft Machine Learning dla platformy Apache Spark](https://github.com/Azure/mmlspark) i zestaw narzędzi usług Cognitive.

Dodatkową korzyścią jest dostęp do niektórych najbardziej zaawansowanych, sprawdzonych technologii uczenia maszynowego opracowanych przez firmę Microsoft i przeznaczonych do rozwiązywania wielkoskalowych problemów. Te przetestowane w praktyce technologie są używane w wielu produktach firmy Microsoft, takich jak:

- Windows
- Bing
- Xbox
- Office
- Oprogramowanie SQL Server

Poniżej wymieniono niektóre technologie uczenia maszynowego firmy Microsoft wykorzystywane w usłudze Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Środowisko robocze usługi Azure Machine Learning
Obsługiwane w systemach Windows i macOS środowisko robocze usługi Azure Machine Learning obejmuje aplikację klasyczną oraz narzędzia wiersza polecenia. Umożliwia ono zarządzanie rozwiązaniami uczenia maszynowego w całym cyklu życiowym danych naukowych:

- Wprowadzanie i przygotowywanie danych
- Opracowywanie modeli i zarządzanie eksperymentami
- Wdrażanie modeli w różnych środowiskach docelowych

Oto podstawowe funkcje oferowane przez środowisko robocze usługi Azure Machine Learning:

- Narzędzie umożliwiające przygotowywanie danych, które może uczyć się logiki przekształcania danych na przykładzie.
- Abstrakcja źródła danych dostępna za pośrednictwem środowiska użytkownika i kodu Python.
- Zestaw SDK języka Python do wywoływania wizualnie skonstruowanych pakietów przygotowywania danych.
- Wbudowana usługa notesu Jupyter i środowisko użytkownika klienta.
- Monitorowanie eksperymentów i zarządzanie nimi za pośrednictwem widoków historii uruchamiania.
- Kontrola dostępu oparta na rolach, która umożliwia udostępnianie i współpracę za pomocą usługi Azure Active Directory.
- Automatyczne tworzenie migawek projektu przy każdym uruchomieniu i jawna kontrola wersji dzięki natywnej integracji z usługą Git. 
- Integracja z popularnymi środowiskami IDE języka Python.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Data Preparation User Guide (Podręcznik użytkownika dotyczący przygotowywania danych)](../desktop-workbench/data-prep-user-guide.md)
- [Using Git with Azure Machine Learning (Używanie usługi Git z usługą Azure Machine Learning)](../desktop-workbench/using-git-ml-project.md)
- [Using Jupyter Notebook in Azure Machine Learning (Używanie notesu Jupyter w usłudze Azure Machine Learning)](../desktop-workbench/how-to-use-jupyter-notebooks.md)
- Roaming and Sharing (Roaming i udostępnianie)
- [Run History Guide (Przewodnik po historii uruchamiania)](../desktop-workbench/how-to-use-run-history-model-metrics.md)
- [IDE Integration (Integracja środowiska IDE)](../desktop-workbench/how-to-configure-your-ide.md)

## <a name="azure-machine-learning-experimentation-service"></a>Eksperymentowanie w usłudze Azure Machine Learning
Usługa Eksperymentowanie zarządza wykonywaniem eksperymentów z uczeniem maszynowym. Współdziała ona również ze środowiskiem roboczym — zapewnia zarządzanie projektami, integrację z usługą Git, kontrolę dostępu, roaming i udostępnianie. 

Konfiguracja tej usługi jest łatwa i pozwala przeprowadzać eksperymenty w pewnym zakresie środowisk obliczeniowych:

- Lokalne środowisko natywne
- Lokalny kontener Docker
- Kontener Docker na zdalnej maszynie wirtualnej
- Skalowanie klastra Spark na platformie Azure

Usługa Eksperymentowanie tworzy środowiska wirtualne, aby zapewnić wykonywanie skryptu w izolacji z powtarzalnymi wynikami. Historia uruchamiania jest rejestrowana i przedstawiana wizualnie. Można łatwo wybrać najlepszy model z serii przebiegów eksperymentu. 

Aby uzyskać więcej informacji, zobacz [Experimentation Service Configuration (Konfiguracja usługi eksperymentowania)](../desktop-workbench/experimentation-service-configuration.md).

## <a name="azure-machine-learning-model-management-service"></a>Zarządzanie modelami w usłudze Azure Machine Learning

Usługa Zarządzanie modelami umożliwia analitykom danych oraz zespołom deweloperów i operatorów wdrażanie modeli predykcyjnych w bardzo różnych środowiskach. Wersje i pochodzenie modeli są śledzone od przebiegów szkoleniowych do wdrożeń. Modele są przechowywane, rejestrowane i zarządzane w chmurze. 

Przy użyciu prostych poleceń interfejsu wiersza polecenia można umieścić model w kontenerze, zapisując skrypty i zależności w obrazach Docker. Obrazy te są rejestrowane we własnym rejestrze Docker hostowanym na platformie Azure (za pośrednictwem usługi Azure Container Registry). Można je niezawodnie wdrażać w następujących lokalizacjach docelowych:

- Maszyny lokalne
- Serwery lokalne
- Chmura
- Urządzenia brzegowe IoT

Na potrzeby wdrożenia skalowalnego w poziomie w chmurze jest używana platforma Kubernetes uruchomiona w usłudze Azure Container Service (ACS). Dane telemetryczne wykonywania modelu są przechwytywane za pomocą usługi AppInsights w celu analizy wizualnej. 

Więcej informacji o usłudze Zarządzanie modelami zawiera artykuł [Omówienie zarządzania modelami](../desktop-workbench/model-management-overview.md).


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Biblioteka usługi Microsoft Machine Learning dla platformy Apache Spark

Biblioteka [MMLSpark](https://github.com/Azure/mmlspark) (biblioteka usługi Microsoft Machine Learning dla platformy Apache Spark) to pakiet open source platformy Spark, który udostępnia narzędzia uczenia głębokiego i analizy danych dla platformy Apache Spark. Zapewnia on integrację [potoków uczenia maszynowego platformy Spark](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) z [zestawem narzędzi usług Microsoft Cognitive](https://www.microsoft.com/en-us/cognitive-toolkit/) i biblioteką [OpenCV](http://opencv.org/). Biblioteka MMLSpark pozwala szybko tworzyć zaawansowane, wysoko skalowane modele predykcyjne i analityczne na potrzeby dużych zestawów danych tekstowych i obrazów. Jej najważniejsze funkcje obejmują:

- Łatwe wprowadzanie obrazów z systemu plików HDFS do elementów DataFrame platformy Spark
- Wstępne przetwarzanie danych obrazów przy użyciu transformacji z biblioteki OpenCV
- Cechowanie obrazów przy użyciu wstępnie przeszkolonych sieci neuronowych i zestawu narzędzi usług Microsoft Cognitive 
- Wyodrębnianie jednostek medycznych za pomocą wstępnie przeszkolonych, dwukierunkowych sieci LSTM z modułu Keras
- Szkolenie modeli opartej na platformie DNN klasyfikacji obrazów na maszynach wirtualnych serii N z procesorem GPU na platformie Azure
- Cechowanie danych tekstowych o formacie swobodnym przy użyciu wygodnych interfejsów API na typach pierwotnych uczenia maszynowego platformy Spark za pośrednictwem jednego elementu przekształcającego
- Łatwe szkolenie modeli klasyfikacji i regresji za pośrednictwem niejawnego cechowania danych
- Przetwarzanie obszernego zestawu metryk oceny, takich jak metryki dla wystąpienia

Aby uzyskać więcej informacji, zobacz [Using MMLSpark in Azure Machine Learning (Korzystanie z biblioteki MMLSpark w usłudze Azure Machine Learning)](../desktop-workbench/how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Narzędzia Visual Studio Code dla sztucznej inteligencji
Narzędzia Visual Studio Code dla sztucznej inteligencji to rozszerzenie programu Visual Studio Code umożliwiające tworzenie, testowanie i wdrażanie rozwiązań uczenia głębokiego i sztucznej inteligencji. Zawiera ono wiele punktów integracji z usługą Azure Machine Learning, takich jak:
- Widok historii uruchamiania przedstawiający wydajność przebiegów szkoleniowych i zarejestrowanych metryk.
- Widok galerii umożliwiający użytkownikom przeglądanie i uruchamianie nowych projektów przy użyciu zestawu narzędzi usług Microsoft Cognitive, platformy TensorFlow i wielu innych platform uczenia głębokiego. 
- Widok eksploratora umożliwiający wybieranie celów obliczeń dla uruchamianych skryptów.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Jakie są opcje uczenia maszynowego udostępniane przez firmę Microsoft?
Oprócz usługi Azure Machine Learning na platformie Azure dostępnych jest wiele różnych opcji umożliwiających tworzenie i wdrażania modeli uczenia maszynowego oraz zarządzanie nimi. [Więcej informacji na ten temat możesz uzyskać tutaj.](../desktop-workbench/overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Następne kroki
* [Install and create Azure Machine Learning (Instalowanie i tworzenie kont usługi Azure Machine Learning)](quickstart-installation.md)
