---
title: Co to jest
titleSuffix: Azure Machine Learning service
description: Omówienie usługi Azure Machine Learning — zintegrowanego, kompleksowego rozwiązania do nauki o danych przeznaczonego dla profesjonalnych analityków zajmujących się opracowywaniem, testowaniem i wdrażaniem zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: c9ebacd8caf992874969106df58531538ea399ed
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510431"
---
# <a name="what-is-azure-machine-learning-service"></a>Co to jest usługa Azure Machine Learning?

Usługa Azure Machine Learning to usługa w chmurze, która pozwala trenować, wdrażać i automatyzować modele uczenia maszynowego oraz zarządzać nimi. Wszystko to jest możliwe na dużą skalę zapewnianą przez chmurę.

## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika badania danych, która dzięki przetwarzaniu istniejących danych jest w stanie przewidzieć przyszłe zachowania, rezultaty i trendy. Przy użyciu techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom uzyskanym za pomocą uczenia maszynowego aplikacje mogą być bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, którymi kupujący może być zainteresowany, na podstawie jego dotychczasowych zakupów. W przypadku płacenia kartą kredytową uczenie maszynowe porównuje transakcję z bazą danych transakcji i ułatwia wykrycie oszustwa. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została wykonana.

## <a name="what-is-azure-machine-learning-service"></a>Co to jest usługa Azure Machine Learning?

Usługa Azure Machine Learning zapewnia środowisko oparte na chmurze, które służy do przygotowywania danych, trenowania, testowania, wdrażania i śledzenia modeli uczenia maszynowego oraz zarządzania nimi. Rozpocznij szkolenie na komputerze lokalnym, a następnie skalować w poziomie w chmurze. Usługi w pełni obsługuje technologie typu open source, takie jak PyTorch, TensorFlow i scikit-Dowiedz się i może służyć do dowolnego rodzaju usługi machine learning od klasycznego ml do uczenia głębokiego, w trybie nadzorowanym i uczenie nienadzorowane. 

Zapoznaj się z i przygotowywania danych, uczenie i testowanie modeli i wdrażać je przy użyciu zaawansowanych narzędzi, takich jak:
+ A [interfejs graficzny](ui-quickstart-run-experiment.md) , w której można następnie przeciągnij i upuść modułów, aby tworzyć eksperymenty, a następnie wdrożyć modele
+ [Notesy Jupyter](https://jupyter.org) w którym używasz [zestawów SDK](https://docs.microsoft.com/azure/machine-learning/service/#reference) do pisania własnego kodu, takich jak [te przykładowe notesy](https://aka.ms/aml-notebooks)
+ [Rozszerzenie programu Visual Studio Code](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Jakie czynności można wykonać za pomocą usługi Azure Machine Learning?

Użyj <a href="https://aka.ms/aml-sdk" target="_blank">zestawu SDK usługi Azure Machine Learning Python</a> z typu open-source pakiety języka Python lub użyj [interfejs graficzny (wersja zapoznawcza)](ui-quickstart-run-experiment.md) tworzenie i szkolenie modeli uczenia głębokiego uczenia maszynowego wysokiej dokładne i samodzielnie w usłudze Azure Machine Learning Workspace.

Możesz korzystać z wielu składników learning maszyny dostępne w pakiety języka Python typu open source, takich jak <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>i <a href="https://mxnet.io" target="_blank">MXNet</a>.

Czy możesz napisać kod, lub użyć interfejs graficzny, można śledzić wiele przebiegów, tak, jak eksperymentów do najlepszych rozwiązań, a także zarządzanie modelami wdrożone.

### <a name="code-first-experience"></a>Najpierw kod środowiska

Rozpocznij szkolenie na Twoim komputerze lokalnym za pomocą <a href="https://aka.ms/aml-sdk" target="_blank">zestawu SDK usługi Azure Machine Learning Python</a> i skalowanie do chmury. Dzięki wielu dostępnym [docelowym obiektom obliczeniowym](how-to-set-up-training-targets.md), takim jak środowisko obliczeniowe usługi Azure Machine Learning i [usługa Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), oraz [zaawansowanym usługom dostrajania hiperparametrów](how-to-tune-hyperparameters.md), można tworzyć lepsze modele szybciej, wykorzystując możliwości chmury.

Możesz również [zautomatyzować szkoleń modelowych i dostrajanie](tutorial-auto-train-models.md) przy użyciu zestawu SDK.

### <a name="code-free--low-code-experience"></a>Środowisko pracy z kodem niekorzystające z kodu i niska

Szkolenia bez kodu, spróbuj wykonać:

+ Interfejs graficzny, przeciągnij i upuść eksperymentowania i wdrażania
    
    ![Interfejs graficzny dla usługi Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)

+ Opcja portalu Azure dla zautomatyzowanych eksperymentów uczenia Maszynowego

### <a name="operationalization-mlops"></a>Operacjonalizacja (MLOps)

Jeśli masz prawo modelu, łatwo służy w usłudze sieci web, na urządzeniu IoT lub z usługi Power BI. Aby uzyskać więcej informacji, zobacz artykuł opisujący, [jak i gdzie wdrażać](how-to-deploy-and-where.md). 

Następnie możesz zarządzać wdrożonymi modelami przy użyciu [zestawu SDK usługi Azure Machine Learning dla języka Python](https://aka.ms/aml-sdk) lub [witryny Azure Portal](https://portal.azure.com/). 

Te modele mogą być wykorzystywane i zwracają prognozy w [czasu rzeczywistego](how-to-consume-web-service.md) lub [asynchronicznie](how-to-run-batch-predictions.md) na dużych ilości danych.

I za pomocą zaawansowanych [potokach uczenia maszynowego](concept-ml-pipelines.md), mogą współpracować dotyczące każdego kroku, od przygotowania danych, uczenie i ocenę modelu, do wdrażania.

Aby rozpocząć pracę przy użyciu usługi Azure Machine Learning, zobacz [Następne kroki](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Czym różni się usługa Azure Machine Learning z programu Studio?

[Usługi Machine Learning Studio](../studio/what-is-ml-studio.md) to współpracy, obsługiwane metodą przeciągania i upuszczania visual obszar roboczy gdzie można tworzenia, testowania i wdrażania rozwiązania uczenia maszynowego bez konieczności pisania kodu. Używa algorytmów uczenia maszynowego wbudowanych i wstępnie skonfigurowanych i moduły obsługi danych, a także własnościowa obliczeń platformy.

Usługa Azure Machine Learning udostępnia oba zestawy SDK **- i -** visual interface(preview), aby szybko przeznaczonego do przygotowania danych, uczenia i wdrażania modeli uczenia maszynowego. Ten interfejs graficzny (wersja zapoznawcza) zapewnia podobne możliwości przeciągania i upuszczania w programie Studio. W odróżnieniu od platformie obliczeniowej własności w Studio interfejs graficzny wykorzystuje zasoby obliczeniowe i jest w pełni zintegrowana usługa Azure Machine Learning.

Poniżej znajduje się porównanie szybkie.

|| Machine Learning Studio | Usługa Azure Machine Learning:<br/>Interfejs wizualny|
|---| --- | --- |
|| Ogólnie dostępna (GA) | W wersji zapoznawczej|
|Moduły interfejsu| wiele | Początkowy zestaw popularnych modułów|
|Szkolenie obliczeniowych elementów docelowych| Zastrzeżone obliczeniowych elementów docelowych, tylko obsługę procesora CPU| Obsługuje mocy obliczeniowej usługi Azure Machine Learning, procesorem GPU lub CPU.<br/>(Inne jednostki obliczeniowe obsługiwane w zestawie SDK)|
|Wdrożenie obliczeniowych elementów docelowych| Format usługi sieci web własności, modyfikowalnych | Opcje zabezpieczeń organizacji i usłudze Azure Kubernetes Service. <br/>([Innych oblicza](how-to-deploy-and-where.md) obsługiwana w zestawie SDK) |
|Automatyczne szkolenia i modelu strojenia hiperparametrycznego | Nie | Jeszcze niedostępne w interfejs graficzny. <br/> (Obsługiwane w portalu usługi SDK i platformy Azure). | 

Wypróbuj interfejs graficzny (wersja zapoznawcza) przy użyciu [Szybki Start: Przygotowanie i wizualizowanie danych bez konieczności pisania kodu](ui-quickstart-run-experiment.md)

> [!NOTE]
> Modele utworzone w programie Studio nie może być wdrożony ani zarządzane przez usługę Azure Machine Learning. Jednak modeli utworzonych i wdrożonych w interfejsie visual usługi mogą być zarządzane za pomocą obszaru roboczego usługi Azure Machine Learning.

## <a name="free-trial"></a>Bezpłatna wersja próbna

Jeśli nie masz subskrypcji na platformie Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie. Możesz też [aktywować korzyści subskrybenta MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), dzięki którym co miesiąc otrzymasz środki do wykorzystania na płatne usługi platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie obszaru roboczego usługi Machine Learning](setup-create-workspace.md) na rozpoczęcie pracy.

- Wykonaj kompleksowe samouczki: 
  + [Uczenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) 
  + [Prepare data and use automated machine learning to auto-train a regression model (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego do automatycznego trenowania modelu regresji)](tutorial-data-prep.md)

- Dowiedz się więcej o [potokach uczenia maszynowego](/azure/machine-learning/service/concept-ml-pipelines) w celu tworzenia i optymalizowania scenariuszy uczenia maszynowego oraz zarządzania nimi.

- Przeczytaj uważnie artykuł poświęcony [architekturze usługi Azure Machine Learning i powiązanym pojęciom](concept-azure-machine-learning-architecture.md).

- Aby uzyskać więcej informacji, zobacz [inne produkty firmy Microsoft do uczenia maszynowego](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
