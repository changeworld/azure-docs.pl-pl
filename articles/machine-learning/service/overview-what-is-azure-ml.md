---
title: Co to jest usługa Azure Machine Learning
description: Omówienie usługi Azure Machine Learning — zintegrowanego, kompleksowego rozwiązania do nauki o danych przeznaczonego dla profesjonalnych analityków zajmujących się opracowywaniem, testowaniem i wdrażaniem zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1dea5e174c66685f889595ab15b3edf57dd02493
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018242"
---
# <a name="what-is-azure-machine-learning-service"></a>Co to jest usługa Azure Machine Learning?

Usługa Azure Machine Learning to usługa w chmurze, która pozwala trenować, wdrażać i automatyzować modele uczenia maszynowego oraz zarządzać nimi. Wszystko to jest możliwe na dużą skalę zapewnianą przez chmurę.

## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika badania danych, która dzięki przetwarzaniu istniejących danych jest w stanie przewidzieć przyszłe zachowania, rezultaty i trendy. Przy użyciu techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom uzyskanym za pomocą uczenia maszynowego aplikacje mogą być bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, którymi kupujący może być zainteresowany, na podstawie jego dotychczasowych zakupów. W przypadku płacenia kartą kredytową uczenie maszynowe porównuje transakcję z bazą danych transakcji i ułatwia wykrycie oszustwa. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została wykonana.

## <a name="what-is-azure-machine-learning-service"></a>Co to jest usługa Azure Machine Learning?

Usługa Azure Machine Learning zapewnia środowisko oparte na chmurze, które służy do przygotowywania danych, trenowania, testowania, wdrażania i śledzenia modeli uczenia maszynowego oraz zarządzania nimi. Rozpocznij szkolenie na komputerze lokalnym, a następnie Skaluj w poziomie do chmury. Usługa w pełni obsługuje technologie typu "open source", takie jak PyTorch, TensorFlow i scikit — uczenie się i może być używane do uczenia maszynowego, od klasycznej maszyny do uczenia głębokiego, nadzorowanych i nienadzorowanych szkoleń.

Eksplorowanie i przygotowywanie danych, uczenie i testowanie modeli oraz wdrażanie ich przy użyciu bogatych narzędzi, takich jak:
+ [Interfejs wizualizacji](ui-tutorial-automobile-price-train-score.md) , w którym można przeciągać i upuszczać moduły, aby kompilować eksperymenty, a następnie wdrażać modele
+ [Jupyter notesy](https://jupyter.org) , w których używasz [zestawów SDK](https://docs.microsoft.com/azure/machine-learning/service/#reference) do pisania własnego kodu, takich jak [przykładowe notesy](https://aka.ms/aml-notebooks)
+ [Visual Studio Code rozszerzenie](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Jakie czynności można wykonać za pomocą usługi Azure Machine Learning?

Korzystanie z <a href="https://aka.ms/aml-sdk" target="_blank">zestawu SDK języka python Azure Machine Learning</a> z pakietami Python Open Source lub korzystanie z [interfejsu wizualizacji (wersja zapoznawcza)](ui-tutorial-automobile-price-train-score.md) w celu tworzenia i uczenia wysoce precyzyjne modele uczenia maszynowego i uczenia się w usłudze Azure Machine Learning Obszary.

Możesz wybrać spośród wielu składników uczenia maszynowego dostępnych w pakietach języka Python typu open source, takich jak <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-</a>Learning, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>i <a href="https://mxnet.io" target="_blank">MXNet</a>.

Niezależnie od tego, czy piszesz kod, czy korzystasz z interfejsu wizualizacji, możesz śledzić wiele przebiegów w trakcie eksperymentu, aby znaleźć najlepsze rozwiązanie, a także zarządzać wdrożonymi modelami.

### <a name="code-first-experience"></a>Kod — pierwsze środowisko

Rozpocznij szkolenie na maszynie lokalnej przy użyciu <a href="https://aka.ms/aml-sdk" target="_blank">zestawu Azure Machine Learning Python SDK</a> , a następnie Skaluj w poziomie do chmury. Dzięki wielu dostępnym [docelowym obiektom obliczeniowym](how-to-set-up-training-targets.md), takim jak środowisko obliczeniowe usługi Azure Machine Learning i [usługa Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), oraz [zaawansowanym usługom dostrajania hiperparametrów](how-to-tune-hyperparameters.md), można tworzyć lepsze modele szybciej, wykorzystując możliwości chmury.

Możesz również [zautomatyzować szkolenia i dostrajanie modeli](tutorial-auto-train-models.md) za pomocą zestawu SDK.

### <a name="ui-based-low-code-experience"></a>Interfejs użytkownika, środowisko o niskim kodzie

W przypadku szkoleń bez kodu wypróbuj następujące rozwiązania:

+ Tworzenie [zautomatyzowanych eksperymentów ml](tutorial-first-experiment-automated-ml.md) w łatwym w użyciu interfejsie.
+ [Eksperymentowanie przy użyciu przeciągania i upuszczania w interfejsie wizualizacji](ui-tutorial-automobile-price-train-score.md).
  ![Interfejs wizualny usługi Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)



### <a name="operationalization-mlops"></a>Operacjonalizacji (MLOps)

Jeśli dysponujesz odpowiednim modelem, możesz go łatwo używać w usłudze sieci Web, na urządzeniu IoT lub z Power BI. Aby uzyskać więcej informacji, zobacz artykuł opisujący, [jak i gdzie wdrażać](how-to-deploy-and-where.md).

Następnie możesz zarządzać wdrożonymi modelami przy użyciu [zestawu SDK usługi Azure Machine Learning dla języka Python](https://aka.ms/aml-sdk) lub [witryny Azure Portal](https://portal.azure.com/).

Te modele mogą być używane i zwracać przewidywania w [czasie rzeczywistym](how-to-consume-web-service.md) lub [asynchronicznie](how-to-run-batch-predictions.md) w przypadku dużych ilości danych.

Dzięki zaawansowanym [potokom uczenia maszynowego](concept-ml-pipelines.md)można współpracować nad każdym krokiem w procesie przygotowywania danych, uczenia modelu i oceny, dzięki wdrożeniu. Potoki umożliwiają:

* Automatyzuj kompleksowy proces uczenia maszynowego w chmurze
* Ponowne używanie składników i ponowne uruchamianie kroków w razie potrzeby
* Użyj innych zasobów obliczeniowych w każdym kroku
* Uruchamianie zadań oceniania partii

Aby rozpocząć pracę przy użyciu usługi Azure Machine Learning, zobacz [Następne kroki](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Jak usługa Azure Machine Learning różni się od Studio?

[Machine Learning Studio](../studio/what-is-ml-studio.md) to współpraca, przeciągnij i upuść obszar roboczy wizualizacji, w którym możesz tworzyć, testować i wdrażać rozwiązania do uczenia maszynowego bez konieczności pisania kodu. Używa wstępnie skompilowanych i wstępnie skonfigurowanych algorytmów uczenia maszynowego oraz modułów obsługi danych, a także własnościowej platformy obliczeniowej.

Usługa Azure Machine Learning udostępnia zestawy SDK **i** interfejs wizualny (wersja zapoznawcza), aby szybko przygotowywać dane, uczeniować i wdrażać modele uczenia maszynowego. Ten interfejs wizualny (wersja zapoznawcza) zapewnia podobne środowisko przeciągania i upuszczania do programu Studio. Jednak, w przeciwieństwie do własnościowej platformy obliczeniowej programu Studio, interfejs wizualny używa własnych zasobów obliczeniowych i jest w pełni zintegrowany z usługą Azure Machine Learning.

Oto krótkie porównanie.

|| Machine Learning Studio | Usługa Azure Machine Learning:<br/>Interfejs wizualny|
|---| --- | --- |
|| Ogólnie dostępna (GA) | W wersji zapoznawczej|
|Moduły dla interfejsu| Ilość | Początkowy zestaw popularnych modułów|
|Szkoleniowe cele obliczeniowe| Własnościowy obiekt docelowy obliczeń, tylko obsługa procesora CPU| Obsługuje Azure Machine Learning obliczeń, procesora GPU i procesora CPU.<br/>(Inne obliczenia obsługiwane w zestawie SDK)|
|Cele obliczeń wdrożenia| Własny format usługi sieci Web, niedostosowywalny | Opcje zabezpieczeń przedsiębiorstwa & usługi Azure Kubernetes. <br/>([Inne obliczenia](how-to-deploy-and-where.md) obsługiwane w zestawie SDK) |
|Automatyczne szkolenie modelu i dostrajanie parametrów | Nie | Jeszcze nie w interfejsie wizualizacji. <br/> (Obsługiwane w zestawach SDK i Azure Portal). |

Wypróbuj interfejs wizualny (wersja zapoznawcza [) z samouczkiem: Przewidywanie ceny samochodów za pomocą interfejsu](ui-tutorial-automobile-price-train-score.md)wizualnego.

> [!NOTE]
> Nie można wdrożyć modeli utworzonych w programie Studio ani nimi zarządzać za pomocą usługi Azure Machine Learning. Jednak modele utworzone i wdrożone w interfejsie wizualnym usługi mogą być zarządzane za pomocą obszaru roboczego usługi Azure Machine Learning.

## <a name="free-trial"></a>Bezpłatna wersja próbna

Jeśli nie masz subskrypcji na platformie Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie. Możesz też [aktywować korzyści subskrybenta MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), dzięki którym co miesiąc otrzymasz środki do wykorzystania na płatne usługi platformy Azure.

## <a name="next-steps"></a>Następne kroki

- [Utwórz obszar roboczy usługi Machine Learning](how-to-manage-workspace.md) , aby rozpocząć pracę.

- Wykonaj kompleksowe samouczki:
  + [Uczenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md)
  + [Prepare data and use automated machine learning to auto-train a regression model (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego do automatycznego trenowania modelu regresji)](tutorial-data-prep.md)

- Dowiedz się więcej o [potokach uczenia maszynowego](/azure/machine-learning/service/concept-ml-pipelines) w celu tworzenia i optymalizowania scenariuszy uczenia maszynowego oraz zarządzania nimi.

- Przeczytaj uważnie artykuł poświęcony [architekturze usługi Azure Machine Learning i powiązanym pojęciom](concept-azure-machine-learning-architecture.md).

- Aby uzyskać więcej informacji, zobacz [inne produkty firmy Microsoft do uczenia maszynowego](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
