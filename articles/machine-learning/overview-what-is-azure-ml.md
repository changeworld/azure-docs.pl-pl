---
title: Co to jest uczenie maszynowe platformy Azure
description: Omówienie usługi Azure Machine Learning — zintegrowane, kompleksowe rozwiązanie do nauki o danych dla profesjonalnych analityków danych do opracowywania, eksperymentowania i wdrażania zaawansowanych aplikacji analitycznych w skali chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: b8dbbb2810277bef20cb3b9b47a63deeea3e0ff9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241612"
---
# <a name="what-is-azure-machine-learning"></a>Co to jest usługa Azure Machine Learning?

W tym artykule dowiesz się więcej o usłudze Azure Machine Learning, środowisku chmurowym, którego można używać do uczenia, wdrażania, automatyzacji, zarządzania i śledzenia modeli uczenia maszynowego. 

Usługa Azure Machine Learning może być używana do dowolnego rodzaju uczenia maszynowego, od klasycznego uczenia maszynowego po uczenie głębokie, nadzorowane i nienadzorowane uczenie. Niezależnie od tego, czy wolisz pisać kod Języka Python lub R, czy opcje zerowego kodu/kodu, takie jak [projektant,](tutorial-designer-automobile-price-train-score.md)możesz tworzyć, szkolić i śledzić bardzo dokładne modele uczenia maszynowego i uczenia głębokiego w obszarze roboczym usługi Azure Machine Learning. 

Rozpocznij szkolenie na komputerze lokalnym, a następnie skaluj w poziomie do chmury. 

Usługa współpracuje również z popularnymi narzędziami open source, takimi jak PyTorch, TensorFlow i scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Bezpłatna wersja próbna!**  Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś. Możesz uzyskać środki do wydania na usługi platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.


## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to technika przetwarzania danych, która umożliwia wykorzystanie przez komputery istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Przy użyciu techniki uczenia maszynowego komputery uczą się bez ich jawnego programowania.

Dzięki prognozom lub przewidywaniom uzyskiwanym za pomocą uczenia maszynowego aplikacje i urządzenia są bardziej inteligentne. Na przykład podczas zakupów w Internecie uczenie maszynowe wspomaga proces rekomendowania innych produktów, którymi kupujący może być zainteresowany, na podstawie jego dotychczasowych zakupów. W przypadku płacenia kartą kredytową uczenie maszynowe porównuje transakcję z bazą danych transakcji i ułatwia wykrycie oszustwa. Gdy robot odkurzający sprząta pomieszczenie, uczenie maszynowe pomaga mu zdecydować, czy praca została wykonana.

## <a name="machine-learning-tools-to-fit-each-task"></a>Narzędzia uczenia maszynowego dopasowane do każdego zadania 

Usługa Azure Machine Learning udostępnia wszystkie narzędzia potrzebne deweloperom i analitykom danych do ich przepływów pracy uczenia maszynowego, w tym:
+ [Projektant usługi Azure Machine Learning](tutorial-designer-automobile-price-train-score.md) (wersja zapoznawcza): moduły przeciągania i upuszczania do tworzenia eksperymentów, a następnie wdrażania potoków.

+ Notebooki Jupyter: użyj naszych [przykładowych notesów](https://aka.ms/aml-notebooks) lub utwórz własne notesy, aby wykorzystać nasz <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestaw SDK dla</a> przykładów języka Python do uczenia maszynowego. 

+ Skrypty języka R lub notesy, w których używasz <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK dla języka R</a> do pisania własnego kodu lub używają modułów języka R w projektancie.

+ [Rozszerzenie programu Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [Interfejs wiersza polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md)

+ Platformy open-source, takie jak PyTorch, TensorFlow i scikit-learn i wiele innych

Przepływ mlflow może nawet używać [do śledzenia metryk i wdrażania modeli](how-to-use-mlflow.md) lub Kubeflow do tworzenia [kompleksowych potoków przepływu pracy.](https://www.kubeflow.org/docs/azure/)

## <a name="build-ml-models-in-python-or-r"></a>Tworzenie modeli ml w języku Python lub R

Rozpocznij szkolenie na komputerze lokalnym przy użyciu <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK języka Python usługi</a> Azure Machine Learning lub <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK języka R.</a> Następnie można skalować w poziomie do chmury. 

Dzięki wielu dostępnym [docelowym obiektom obliczeniowym](how-to-set-up-training-targets.md), takim jak środowisko obliczeniowe usługi Azure Machine Learning i [usługa Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), oraz [zaawansowanym usługom dostrajania hiperparametrów](how-to-tune-hyperparameters.md), można tworzyć lepsze modele szybciej, wykorzystując możliwości chmury.

Można również [zautomatyzować szkolenia modelu i strojenie](tutorial-auto-train-models.md) za pomocą sdk.

## <a name="build-ml-models-with-no-code-tools"></a>Tworzenie modeli ml za pomocą narzędzi bez kodu

Aby uzyskać bezpłatne lub niskie kod szkolenia i wdrażania, spróbuj:

+ **Projektant usługi Azure Machine Learning (wersja zapoznawcza)**

  Projektanta służy do przygotowywania danych, uczenia, testowania, wdrażania, zarządzania i śledzenia modeli uczenia maszynowego bez pisania kodu. Nie jest wymagane programowanie, wizualnie połączyć zestawy danych i moduły do konstruowania modelu. Wypróbuj [samouczek projektanta](tutorial-designer-automobile-price-train-score.md).

  Dowiedz się więcej w [artykule projektanta usługi Azure Machine Learning](concept-designer.md). 

  ![Przykład projektanta usługi Azure Machine Learning](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Zautomatyzowany interfejs użytkownika uczenia maszynowego**

  Dowiedz się, jak tworzyć [zautomatyzowane eksperymenty uczenia maszynowego](tutorial-first-experiment-automated-ml.md) w łatwym w użyciu interfejsie. 

  [![Okienko nawigacji studio usługi Azure Machine Learning](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MOPS: Wdrażanie zarządzania cyklem życia &
Jeśli masz odpowiedni model, możesz go łatwo używać w usłudze sieci web, na urządzeniu IoT lub w usłudze Power BI. Aby uzyskać więcej informacji, zobacz artykuł opisujący, [jak i gdzie wdrażać](how-to-deploy-and-where.md).

Następnie można zarządzać wdrożonymi modelami przy użyciu [zestawu SDK usługi Azure Machine Learning dla języka Python,](https://aka.ms/aml-sdk) [studia azure machine learning](https://ml.azure.com)lub interfejsu [wiersza polecenia uczenia maszynowego.](reference-azure-machine-learning-cli.md)

Te modele mogą być używane i zwracać prognozy w [czasie rzeczywistym](how-to-consume-web-service.md) lub [asynchronicznie](how-to-use-parallel-run-step.md) na dużych ilościach danych.

A dzięki zaawansowanym [potokom uczenia maszynowego](concept-ml-pipelines.md)możesz współpracować na każdym etapie, od przygotowania danych, szkolenia i oceny modelu, poprzez wdrażanie. Potoki umożliwiają:

* Automatyzacja kompleksowego procesu uczenia maszynowego w chmurze
* Ponowne używanie komponentów i powtarzanie tylko w razie potrzeby
* Używanie różnych zasobów obliczeniowych w każdym kroku
* Uruchamianie zadań oceniania partii

Jeśli chcesz użyć skryptów do automatyzacji przepływu pracy uczenia maszynowego, [narzędzie polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md) zawiera narzędzia wiersza polecenia, które wykonują typowe zadania, takie jak przesyłanie przebiegu szkolenia lub wdrażanie modelu.

Aby rozpocząć korzystanie z usługi Azure Machine Learning, zobacz [Następne kroki](#next-steps).

## <a name="integration-with-other-services"></a>Integracja z innymi usługami

Usługa Azure Machine Learning współpracuje z innymi usługami na platformie Azure, a także integruje się z narzędziami open source, takimi jak Git i MLFlow.

+ Obiekty docelowe obliczeń, takie jak __usługa Azure Kubernetes Service,__ __wystąpienia kontenerów platformy Azure,__ __usługi Azure Databricks,__ __usługa Azure Data Lake Analytics__i usługa __Azure HDInsight__. Aby uzyskać więcej informacji na temat celów obliczeniowych, zobacz [Co to są obiekty docelowe obliczeń?](concept-compute-target.md).
+ __Usługa Azure Event Grid__. Aby uzyskać więcej informacji, zobacz [Korzystanie ze zdarzeń usługi Azure Machine Learning](concept-event-grid-integration.md).
+ __Monitor platformy Azure__. Aby uzyskać więcej informacji, zobacz [Monitorowanie usługi Azure Machine Learning](monitor-azure-machine-learning.md).
+ Magazyny danych, takie jak __konta usługi Azure Storage,__ __usługa Azure Data Lake Storage__, usługa Azure __SQL Database__, usługa Azure Database __for PostgreSQL__i __Azure Open Datasets__. Aby uzyskać więcej informacji, zobacz [Dostęp do danych w usługach magazynu platformy Azure](how-to-access-data.md) i Tworzenie [zestawów danych za pomocą zestawów danych Azure Open](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Sieci wirtualne platformy Azure__. Aby uzyskać więcej informacji, zobacz [Bezpieczne eksperymentowanie i wnioskowanie w sieci wirtualnej](how-to-enable-virtual-network.md).
+ __Potoki platformy Azure__. Aby uzyskać więcej informacji, zobacz [Szkolenie i wdrażanie modeli uczenia maszynowego](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Git dzienniki repozytorium__. Aby uzyskać więcej informacji, zobacz [Integracja git](concept-train-model-git-integration.md).
+ __MLFlow__. Aby uzyskać więcej informacji, zobacz [MLflow, aby śledzić metryki i wdrażać modele](how-to-use-mlflow.md) 
+ __Kubeflow__. Aby uzyskać więcej informacji, zobacz [tworzenie kompleksowych potoków przepływu pracy kompilacji](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Bezpieczna komunikacja

Twoje konto usługi Azure Storage, obiekty docelowe obliczeń i inne zasoby mogą być bezpiecznie używane w sieci wirtualnej do szkolenia modeli i wykonywania wniosków. Aby uzyskać więcej informacji, zobacz [Bezpieczne eksperymentowanie i wnioskowanie w sieci wirtualnej](how-to-enable-virtual-network.md).

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Podstawowe wersje & Enterprise

Usługa Azure Machine Learning oferuje dwie wersje dostosowane do twoich potrzeb uczenia maszynowego:
+ Podstawowe (ogólnie dostępne)
+ Przedsiębiorstwo (wersja zapoznawcza)

Te wersje określają, które narzędzia uczenia maszynowego są dostępne dla deweloperów i analityków danych z ich obszaru roboczego.   

Podstawowe obszary robocze umożliwiają dalsze korzystanie z usługi Azure Machine Learning i płacenie tylko za zasoby platformy Azure używane podczas procesu uczenia maszynowego. Obszary robocze wersji Enterprise będą naliczane tylko za zużycie platformy Azure, gdy wersja jest w wersji zapoznawczej. Dowiedz się więcej o tym, co jest dostępne na [stronie wyceny usługi](https://azure.microsoft.com/pricing/details/machine-learning/)Azure Machine Learning & . 

Wersję przypisywaną jest przy każdym utworzeniu obszaru roboczego. I wcześniej istniejących obszarów roboczych zostały przekonwertowane na edycję Podstawową dla Ciebie. Edycja podstawowa zawiera wszystkie funkcje, które były już ogólnie dostępne od października 2019. Wszelkie eksperymenty w tych obszarach roboczych, które zostały utworzone przy użyciu funkcji wersji Enterprise, będą nadal dostępne tylko do odczytu, dopóki nie zostanie uaktualniony do wersji Enterprise. Dowiedz się, jak [uaktualnić podstawowy obszar roboczy do wersji Enterprise.](how-to-manage-workspace.md#upgrade) 

Klienci są odpowiedzialni za koszty poniesione w tym czasie na zasoby obliczeniowe i inne zasoby platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszy eksperyment z preferowaną metodą:
  + [Szkolenie & wdrażania modeli modułów ML za pomocą notesów języka Python](tutorial-1st-experiment-sdk-setup.md)
  + [Użyj R Markdown, aby szkolić & wdrażania modeli ml](tutorial-1st-r-experiment.md) 
  + [Użyj automatycznego uczenia maszynowego, aby szkolić & wdrażać modele ml](tutorial-first-experiment-automated-ml.md) 
  + [Użyj funkcji przeciągania & upuszczania projektanta, aby szkolić & wdrażania](tutorial-designer-automobile-price-train-score.md) 
  + [Szkolenie i wdrażanie modelu za pomocą interfejsu wiersza polecenia uczenia maszynowego](tutorial-train-deploy-model-cli.md)

- Dowiedz się więcej o [potokach uczenia maszynowego](concept-ml-pipelines.md) w celu tworzenia i optymalizowania scenariuszy uczenia maszynowego oraz zarządzania nimi.

- Przeczytaj dogłębną [architekturę usługi Azure Machine Learning i pojęć.](concept-azure-machine-learning-architecture.md)
