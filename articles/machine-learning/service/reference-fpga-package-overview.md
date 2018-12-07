---
title: Pakiet FPGA przyspieszanie sprzętowe dla usługi Azure Machine Learning
description: W tym artykule jest przestarzały.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 002f521343d96ee50944428b1df9af2ce96bf88b
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013496"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Pakiet Azure Machine Learning sprzętowego przyspieszania

>[!Note]
>**W tym artykule jest przestarzały.** Ten pakiet FPGA została zakończona. Obsługa tej funkcji został dodany do zestawu SDK usługi Azure ML. Obsługa tego pakietu zakończy się przyrostowo. [Wyświetl oś czasu pomocy technicznej](overview-what-happened-to-workbench.md#timeline). Dowiedz się więcej o aktualizacji [obsługi FPGA](concept-accelerate-with-fpgas.md).

Pakiet Azure Machine Learning sprzętowego przyspieszania jest rozszerzeniem do zainstalowania narzędzia pip języka Python dla usługi Azure Machine Learning, która umożliwia analitykom danych i deweloperom sztucznej Inteligencji szybko:

+ Cechowanie obrazów przy użyciu wersji wykonywanie kwantyzowanych siecią ResNet-50

+ Uczyć klasyfikatory na podstawie tych funkcji

+ Wdrażanie modeli do [pola Tablice bramek programowane (FPGA)](concept-accelerate-with-fpgas.md) na platformie Azure bardzo niskimi opóźnieniami wnioskowania

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLfree).

1. Konto Zarządzanie modelami w usłudze Azure Machine Learning. Aby uzyskać więcej informacji na temat tworzenia konta, zobacz [instalacji usługi Azure Machine Learning Przewodnik Szybki Start i aplikacja Workbench](../desktop-workbench/quickstart-installation.md) dokumentu. 

1. Musi być zainstalowany pakiet. 

 
## <a name="how-to-install-the-package"></a>Jak zainstalować pakiet

1. Pobierz i zainstaluj najnowszą wersję [Git](https://git-scm.com/downloads).

2. Zainstaluj [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

   Aby pobrać wstępnie skonfigurowane środowisko Anaconda, użyj następującego polecenia w wierszu polecenia Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. Aby utworzyć środowisko, otwórz **Anaconda monitu** i użyj następującego polecenia:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. Aby aktywować tę funkcję w środowisku, użyj następującego polecenia:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Przykładowy kod

Ten przykładowy kod opisano sposób korzystania z zestawu SDK do wdrożenia modelu FPGA.

1. Importowanie pakietu:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Wstępne przetwarzanie obrazu:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Cechowanie obrazów:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Tworzenie klasyfikatora:
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
 
1. Przygotuj modelu do uruchamiania na FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Wdrażanie modelu do uruchamiania na FPGA:
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

1. Wywoływanie interfejsu API:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Raportowanie problemów

Użyj [forum](https://aka.ms/aml-forum-service) zgłosić problemy wystąpią z pakietem.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie modelu jako usługi sieci web na FPGA](how-to-deploy-fpga-web-service.md)
