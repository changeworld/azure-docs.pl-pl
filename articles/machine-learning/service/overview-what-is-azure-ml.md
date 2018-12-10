---
title: Co to jest usługa Azure Machine Learning?
description: Omówienie uczenia maszynowego w chmurze i sposobów jego zastosowania. Omówienie usługi Azure Machine Learning Service — zintegrowanego, kompleksowego rozwiązania do nauki o danych przeznaczonego dla profesjonalnych analityków zajmujących się opracowywaniem, testowaniem i wdrażaniem zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.openlocfilehash: fc241b284ab9370e03debf3e0851394c32087a57
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870697"
---
# <a name="what-is-azure-machine-learning-service"></a>Co to jest Azure Machine Learning Service?

Azure Machine Learning Service to usługa w chmurze, która pozwala trenować, wdrażać i automatyzować modele uczenia maszynowego oraz zarządzać nimi. Wszystko to jest możliwe na dużą skalę zapewnianą przez chmurę.

## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika przetwarzania danych, która umożliwia wykorzystanie przez komputery istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Za pomocą techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom lub przewidywaniom uzyskiwanym za pomocą uczenia maszynowego aplikacje i urządzenia są bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, które mogą się spodobać kupującemu, na podstawie dotychczasowych zakupów. W przypadku płacenia kartą kredytową uczenie maszynowe porównuje transakcję z bazą danych transakcji i ułatwia wykrycie oszustwa. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została wykonana.

## <a name="what-is-azure-machine-learning-service"></a>Co to jest usługa Azure Machine Learning?

Usługa Azure Machine Learning zapewnia środowisko oparte na chmurze, które służy do tworzenia, szkolenia, testowania, wdrażania i śledzenia modeli uczenia maszynowego oraz zarządzania nimi.

[ ![Przepływ pracy usługi Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Usługa Azure Machine Learning w pełni obsługuje technologie typu „open source”, dzięki czemu można używać dziesiątków tysięcy pakietów języka Python typu „open source” razem ze składnikami uczenia maszynowego, takimi jak TensorFlow i scikit-learn.
Obsługa zaawansowanych narzędzi, takich jak notesy usługi [Jupyter Notebook](http://jupyter.org) lub rozszerzenie [Azure Machine Learning for Visual Studio Code](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/), ułatwia interaktywną eksplorację danych, ich przekształcanie, a następnie tworzenie i testowanie modeli.
Usługa Azure Machine Learning obejmuje również funkcje, które [automatyzują generowanie i dostrajanie modeli](tutorial-auto-train-models.md), co ułatwia tworzenie modeli w sposób prosty, wydajny i prawidłowy.

Usługa Azure Machine Learning umożliwia rozpoczęcie szkolenia na komputerze lokalnym, a następnie skalowanie do chmury. Dzięki wielu dostępnym [docelowym obiektom obliczeń](how-to-set-up-training-targets.md), takim jak środowisko obliczeniowe usługi Azure Machine Learning i [usługa Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), oraz [zaawansowanym usługom dostrajania hiperparametrów](how-to-tune-hyperparameters.md), możesz tworzyć lepsze modele szybciej, wykorzystując możliwości chmury.

Jeśli masz odpowiedni model, możesz go łatwo wdrożyć w kontenerze, takim jak Docker. Oznacza to, że można łatwo wdrażać do usługi [Azure Container Instances](how-to-deploy-to-aci.md) lub [Azure Kubernetes Service](how-to-deploy-to-aks.md), lub można użyć kontenera w swoim własnych wdrożeniach, lokalnie lub w chmurze.
Można zarządzać wdrożonymi modelami i śledzić wiele przebiegów, eksperymentując w celu znalezienia najlepszego rozwiązania.
Po wdrożeniu Twój model może zwracać przewidywania dla dużych ilości danych w [czasie rzeczywistym](how-to-consume-web-service.md) lub [asynchronicznie](how-to-run-batch-predictions.md).

Natomiast zaawansowane [potoki uczenia maszynowego](concept-ml-pipelines.md) pozwalają współpracować z innymi nad wszystkimi krokami przygotowywania danych, trenowania i oceniania modelu oraz jego wdrażania.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Jakie czynności można wykonać za pomocą usługi Azure Machine Learning?

Usługa Azure Machine Learning może automatycznie wytrenować model i automatycznie go dla Ciebie dostroić.
Na przykład zobacz temat [Tutorial: Automatically train a classification model with Azure Automated Machine Learning](tutorial-auto-train-models.md) (Samouczek: szkolenie modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego w usłudze Azure Machine Learning).

Używając zestawu Azure Machine Learning <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> dla języka Python razem z pakietami języka Python typu „open source”, możesz również samodzielnie tworzyć i szkolić bardzo precyzyjne modele uczenia maszynowego i uczenia głębokiego w obszarze roboczym usługi Azure Machine Learning Service.
Możesz korzystać z wielu składników uczenia maszynowego dostępnych w pakietach języka Python typu „open source”, np. z następujących:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Po utworzeniu modelu użyj go do utworzenia kontenera (np. Docker), który można wdrożyć lokalnie do testowania. Po zakończeniu testowania model można wdrożyć jako produkcyjną usługę internetową w usłudze [Azure Container Instances](how-to-deploy-to-aci.md) lub usłudze [Azure Kubernetes Service](how-to-deploy-to-aks.md).

Następnie można zarządzać wdrożonymi modelami przy użyciu [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) lub [witryny Azure Portal](https://portal.azure.com/).
Można ocenić metryki modelu, ponownie szkolić i ponownie wdrażać nowe wersje modelu, a wszystko to w trakcie śledzenia eksperymentów związanych z modelem.

Aby rozpocząć pracę przy użyciu usługi Azure Machine Learning, zobacz temat [Następne kroki](#next-steps) poniżej.

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Czym różni się usługa Azure Machine Learning od usługi Studio?

Usługa Azure Machine Learning Studio jest wizualnym obszarem roboczym do współpracy typu przeciągnij i upuść, w którym można tworzyć, testować i wdrażać rozwiązania uczenia maszynowego bez potrzeby pisania kodu. Używa ona wstępnie utworzonych i wstępnie skonfigurowanych algorytmów uczenia maszynowego i modułów obsługi danych.

Używaj usługi Machine Learning Studio, gdy chcesz w szybki i prosty sposób poeksperymentować z modelami uczenia maszynowego, a wbudowane algorytmy uczenia maszynowego są wystarczające dla Twoich rozwiązań.

Używaj usługi uczenia maszynowego, jeśli pracujesz w środowisku języka Python, chcesz mieć większą kontrolę nad algorytmami uczenia maszynowego lub chcesz użyć bibliotek uczenia maszynowego typu „open source”.

> [!NOTE]
> Modele utworzone w usłudze Azure Machine Learning Studio nie mogą być wdrażane ani zarządzane przez usługę Azure Machine Learning.

## <a name="free-trial"></a>Bezpłatna wersja próbna
Jeśli nie masz subskrypcji, możesz [otworzyć bezpłatne konto platformy Azure](https://aka.ms/amlfree). Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie. Możesz też [aktywować korzyści subskrybenta MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), dzięki którym co miesiąc otrzymasz środki do wykorzystania na płatne usługi platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Utwórz obszar roboczy usługi Machine Learning Service, aby zacząć pracę przy użyciu [witryny Azure Portal](quickstart-get-started.md) lub [języka Python](quickstart-create-workspace-with-python.md).

- Ukończ pełnowymiarowy samouczek [Trenowanie i wdrażanie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

- [Użyj usługi Azure Machine Learning, aby automatycznie wygenerować i automatycznie dostroić model](tutorial-auto-train-models.md).

- Dowiedz się więcej o [potokach uczenia maszynowego](/azure/machine-learning/service/concept-ml-pipelines) w celu tworzenia i optymalizowania scenariuszy uczenia maszynowego oraz zarządzania nimi.

- Przeczytaj uważnie artykuł poświęcony [architekturze usługi Azure Machine Learning i powiązanym pojęciom](concept-azure-machine-learning-architecture.md).

- Aby uzyskać więcej informacji na temat innych produktów do uczenia maszynowego firmy Microsoft, zobacz temat [Inne produkty do uczenia maszynowego firmy Microsoft](./overview-more-machine-learning.md)


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
