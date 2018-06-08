---
title: Pakiet układu FPGA dla przyspieszanie sprzętowe dla usługi Azure Machine Learning
description: Więcej informacji na temat dostępnych dla użytkowników usługi Azure Machine Learning pakietów języka python.
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: routlaw
author: rloutlaw
ms.date: 05/07/2018
ms.openlocfilehash: e680ef34be1d5dae2942c432de5e81fe620bbdc4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832982"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Pakiet Azure Machine Learning sprzętowego przyspieszania

Pakiet Azure Machine Learning sprzętowego przyspieszania to rozszerzenie instalowalnych pip języka Python dla usługi Azure Machine Learning, który umożliwia szybkie danych i analityków AI do:

+ Obrazy Featurize wersją quantized ResNet 50

+ Szkolenie klasyfikatory na podstawie tych funkcji

+ Wdrażanie modeli do [pola Tablice bramek programowane (układu FPGA)](concept-accelerate-with-fpgas.md) na platformie Azure dla inferencing niezwykle małe opóźnienia

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Należy utworzyć konta Azure Machine Learning Model zarządzania. Aby uzyskać więcej informacji na temat tworzenia konta, zobacz [Szybki Start Azure Machine Learning i Workbench instalacji](../service/quickstart-installation.md) dokumentu. 

1. Musi być zainstalowany pakiet. 

 
## <a name="how-to-install-the-package"></a>Jak zainstalować pakiet

1. Pobierz i zainstaluj najnowszą wersję [Git](https://git-scm.com/downloads).

2. Zainstaluj [Anaconda (Python 3,6)](https://conda.io/miniconda.html)

3. Aby pobrać wstępnie skonfigurowane środowisko Anaconda, użyj następującego polecenia w wierszu Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
5. Aby utworzyć środowisko, otwórz **Anaconda monitu** i użyj następującego polecenia:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

6. Aby aktywować środowiska, użyj następującego polecenia:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Przykładowy kod

Ten przykładowy kod przedstawia przy użyciu zestawu SDK, aby wdrożyć model układu FPGA.

1. Importowanie pakietu:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Wstępnie przetworzyć obrazu:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Featurize obrazów:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Utworzenie klasyfikatora programu:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Tworzenie definicji usługi:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Przygotowanie do uruchomienia na układu FPGA modelu:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Wdróż model do uruchamiania na układu FPGA:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Tworzenie klienta:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Wywołanie interfejsu API:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Zgłaszanie problemów

Użyj [forum](https://aka.ms/aml-forum) zgłosić jakiekolwiek problemy wystąpią z pakietem.

## <a name="next-steps"></a>Kolejne kroki

[Wdróż model jako usługę sieci web na układu FPGA](how-to-deploy-fpga-web-service.md)