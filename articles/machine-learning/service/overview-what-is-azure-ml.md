---
title: Co to jest usługa Azure Machine Learning?
description: W tym temacie objaśniono podstawowe pojęcia dotyczące uczenia maszynowego w chmurze, opisano jego możliwe zastosowania i zdefiniowano terminy dotyczące uczenia maszynowego. Omówienie usługi Azure Machine Learning — zintegrowanego, kompleksowego rozwiązania do nauki o danych przeznaczonego dla profesjonalnych analityków zajmujących się opracowywaniem, testowaniem i wdrażaniem zaawansowanych aplikacji analitycznych na skalę chmury.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 6e0294f99d1c2291e84bf2ac5d5b1f771222b551
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433416"
---
# <a name="what-is-azure-machine-learning-service-preview"></a>Co to jest usługa Azure Machine Learning (wersja zapoznawcza)?

Usługa Azure Machine Learning (wersja zapoznawcza) to usługa w chmurze, która służy do tworzenia i wdrażania modeli uczenia maszynowego. Za pomocą usługi Azure Machine Learning można śledzić swoje modele podczas ich tworzenia, szkolenia, wdrażania i zarządzania nimi, a to wszystko na szeroką skalę, którą oferuje chmura.

## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika przetwarzania danych, która umożliwia wykorzystanie przez komputery istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Za pomocą techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom lub przewidywaniom uzyskiwanym za pomocą uczenia maszynowego aplikacje i urządzenia są bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, które mogą się spodobać kupującemu, na podstawie dotychczasowych zakupów. W przypadku płacenia kartą kredytową uczenie maszynowe porównuje transakcję z bazą danych transakcji i ułatwia wykrycie oszustwa. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została wykonana.

## <a name="what-is-azure-machine-learning-service"></a>Co to jest usługa Azure Machine Learning?

Usługa Azure Machine Learning zapewnia środowisko oparte na chmurze, które służy do tworzenia, szkolenia, testowania, wdrażania i śledzenia modeli uczenia maszynowego oraz zarządzania nimi.

[ ![Przepływ pracy usługi Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Usługa Azure Machine Learning w pełni obsługuje technologie typu „open source”, dzięki czemu można używać dziesiątków tysięcy pakietów języka Python typu „open source” razem ze składnikami uczenia maszynowego, takimi jak TensorFlow i scikit-learn.
Zaawansowane narzędzia, takie jak [Jupyter Notebooks](http://jupyter.org) lub [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/), ułatwiają interaktywną eksplorację danych, ich przekształcanie, a następnie tworzenie i testowanie modeli.
Usługa Azure Machine Learning obejmuje również funkcje, które [automatyzują generowanie i dostrajanie modeli](tutorial-auto-train-models.md), co ułatwia tworzenie modeli w sposób prosty, wydajny i prawidłowy.

Usługa Azure Machine Learning umożliwia rozpoczęcie szkolenia na komputerze lokalnym, a następnie skalowanie do chmury. Za pomocą natywnej obsługi [Azure Batch AI](https://azure.microsoft.com/services/batch-ai/) oraz [usług zaawansowanego dostrajania hiperparametrycznego](how-to-tune-hyperparameters.md) można szybciej tworzyć lepsze modele, wykorzystując możliwości chmury. 

Jeśli masz odpowiedni model, możesz go łatwo wdrożyć w kontenerze, takim jak Docker. Oznacza to, że można łatwo wdrażać do usługi [Azure Container Instances](how-to-deploy-to-aci.md) lub [Azure Kubernetes Service](how-to-deploy-to-aks.md), lub można użyć kontenera w swoim własnych wdrożeniach, lokalnie lub w chmurze.
Można zarządzać wdrożonymi modelami i śledzić wiele przebiegów, eksperymentując w celu znalezienia najlepszego rozwiązania.

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Jakie czynności można wykonać za pomocą usługi Azure Machine Learning?

Usługa Azure Machine Learning może automatycznie wygenerować model i automatycznie go dla Ciebie dostroić.
Na przykład zobacz temat [Tutorial: Automatically train a classification model with Azure Automated Machine Learning](tutorial-auto-train-models.md) (Samouczek: szkolenie modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego w usłudze Azure Machine Learning).

Używając zestawu <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> usługi Azure Machine Learning dla języka Python razem z pakietami języka Python typu „open source”, możesz również samodzielnie tworzyć i szkolić bardzo precyzyjne modele uczenia maszynowego i uczenia głębokiego w obszarze roboczym usługi Azure Machine Learning.
Możesz korzystać z wielu składników uczenia maszynowego dostępnych w pakietach języka Python typu „open source”, np. z następujących:

- <a href="http://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Po utworzeniu modelu należy go użyć do utworzenia kontenera (np. Docker), który można wdrożyć lokalnie do testowania, a następnie jako produkcyjną usługę sieci Web w usłudze [Azure Container Instances](how-to-deploy-to-aci.md) lub [Azure Kubernetes Service](how-to-deploy-to-aks.md).

Następnie można zarządzać wdrożonymi modelami przy użyciu [witryny Azure Portal](https://portal.azure.com/) lub [rozszerzenia interfejsu wiersza polecenia usługi Azure Machine Learning](https://review.docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
Można ocenić metryki modelu, ponownie szkolić i ponownie wdrażać nowe wersje modelu, a wszystko to w trakcie śledzenia eksperymentów związanych z modelem.

Aby rozpocząć pracę przy użyciu usługi Azure Machine Learning, zobacz temat [Następne kroki](#next-steps) poniżej.

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Czym różni się usługa Azure Machine Learning od usługi Studio?

Usługa Azure Machine Learning Studio jest wizualnym obszarem roboczym do współpracy typu przeciągnij i upuść, w którym można tworzyć, testować i wdrażać rozwiązania uczenia maszynowego bez potrzeby pisania kodu. Używa ona wstępnie utworzonych i wstępnie skonfigurowanych algorytmów uczenia maszynowego i modułów obsługi danych.

Używaj usługi Machine Learning Studio, gdy chcesz w szybki i prosty sposób poeksperymentować z modelami uczenia maszynowego, a wbudowane algorytmy uczenia maszynowego są wystarczające dla Twoich rozwiązań.

Używaj usługi uczenia maszynowego, jeśli pracujesz w środowisku języka Python, chcesz mieć większą kontrolę nad algorytmami uczenia maszynowego lub chcesz użyć bibliotek uczenia maszynowego typu „open source”.

> [!NOTE]
> Modele utworzone w usłudze Azure Machine Learning Studio nie mogą być wdrażane ani zarządzane przez usługę Azure Machine Learning.

## <a name="free-trial"></a>Bezpłatna wersja próbna
Jeśli nie masz subskrypcji, możesz [otworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie. Możesz też [aktywować korzyści subskrybenta MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) — w ramach subskrypcji MSDN co miesiąc otrzymasz kredyt, który można przeznaczyć na płatne usługi platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Utwórz obszar roboczy uczenia maszynowego, korzystając z informacji zawartych w artykule [Use Azure Portal to get started](quickstart-get-started.md) (Korzystanie z witryny Azure Portal, aby rozpocząć pracę)
 
- Postępuj zgodnie z instrukcjami zawartymi w gotowym artykule [Train an image classification model with Azure Machine Learning](tutorial-train-models-with-aml.md) (Uczenie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning), aby dowiedzieć się, jak uczyć i wdrażać modele za pomocą usługi Azure Machine Learning

- Aby uzyskać informacje dotyczące zezwalania usłudze Azure Machine Learning na automatyczne generowanie i automatyczne dostrajanie Twojego modelu, zobacz temat [Samouczek: Szkolenie modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego w usłudze Azure Machine Learning](tutorial-auto-train-models.md)

- Aby zapoznać się z technicznym, bardziej szczegółowym opisem usługi, zobacz temat [Architecture and concepts: How does Azure Machine Learning service work?](concept-azure-machine-learning-architecture.md) (Architektura i koncepcje: Jak działa usługa Azure Machine Learning?)

- Aby uzyskać więcej informacji na temat innych produktów do uczenia maszynowego firmy Microsoft, zobacz temat [Produkty i usługi firmy Microsoft związane z uczeniem maszynowym](./overview-more-machine-learning.md)


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
