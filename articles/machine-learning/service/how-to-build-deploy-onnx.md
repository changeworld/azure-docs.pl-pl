---
title: ONNX i usługi Azure Machine Learning | Tworzenie i wdrażanie modeli
description: Dowiedz się więcej o ONNX i jak tworzyć i wdrażać modele ONNX za pomocą usługi Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: 2e5c0e479d5564a48048b9fa9c67ad8870122601
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706062"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX i Azure Machine Learning: tworzenie i wdrażanie interoperacyjne modeli sztucznej Inteligencji

[Otwórz Exchange sieci neuronowych](https://onnx.ai) formatu (ONNX) jest standardem otwartym do reprezentowania modeli uczenia maszynowego. ONNX jest obsługiwana przez [społeczności partnerów](https://onnx.ai/supported-tools), łącznie z firmy Microsoft, który utworzyć zgodne strukturami i narzędziami. Firma Microsoft poświęca otwarte i interoperacyjne sztucznej Inteligencji, aby analitycy danych i deweloperzy mogą:

+ Struktura wybranych przez nich umożliwia tworzenie i uczenie modeli
+ Wdrażanie modeli dla wielu platform przy minimalnym integracji pracy

Firma Microsoft obsługuje ONNX jej produktów, w tym Azure i Windows, aby pomóc w osiąganiu tych celów.  

## <a name="why-choose-onnx"></a>Dlaczego warto wybrać ONNX?
Współdziałanie, który jest pobierany z ONNX sprawia, że można szybciej uzyskać dobre pomysły w środowisku produkcyjnym. Za pomocą ONNX analitykom danych można wybrać preferowaną szkielet dla zadania. Podobnie deweloperzy mogą poświęcać mniej czasu na przygotowanie modele do produkcji i wdrożyć między chmurą i urządzeniami brzegowymi.  

Możesz tworzyć modele ONNX z wielu platform, w tym PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet, strukturze ML.Net, TensorFlow, Keras, SciKit-Learn i nie tylko.

Istnieje również ekosystemu narzędzi do wizualizacji i skróceniu modelami ONNX. Liczbę wstępnie szkolone modele ONNX są również dostępne dla typowych scenariuszy.

[Można wdrażać modele ONNX](#deploy) w chmurze za pomocą usługi Azure Machine Learning i środowiska uruchomieniowego ONNX. Można je również wdrożyć na urządzeniach z systemem Windows 10 przy użyciu [Windows ML](https://docs.microsoft.com/windows/ai/). Nawet wdrażania ich na innych platformach przy użyciu konwertery są dostępne przez społeczność ONNX. 

[ ![Diagram przepływu ONNX pokazujący, szkolenia, konwerterów i wdrażania](media/concept-onnx/onnx.png) ] (. / media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Modele ONNX

Możesz uzyskać modelami ONNX na kilka sposobów:
+ Pobierz wstępnie przeszkolonych modelu ONNX z [Zoo modelu ONNX](https://github.com/onnx/models) (Zobacz przykład w dolnej części tego artykułu)
+ Generowanie dostosowane modelu ONNX z [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Istniejący model przekonwertować z innego formatu ONNX (Zobacz przykład w dolnej części tego artykułu) 
+ Szkolenie nowego modelu ONNX w usłudze Azure Machine Learning (Zobacz przykład w dolnej części tego artykułu)

## <a name="saveconvert-your-models-to-onnx"></a>Zapisz/przekonwertować swoje modele, aby ONNX

Można przekonwertować istniejące modele ONNX lub zapisać je jako ONNX na końcu szkolenia.

|Platforma do modelu|Przykład konwersji lub narzędzie|
|-----|-------|
|PyTorch|[Notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[Konwerter tensorflow onnx](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, Dowiedz się ScitKit CoreML<br/>XGBoost i libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

Można znaleźć najnowszą listę obsługiwanych platform i konwertery na [lokacji samouczki ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Wdrażaj modele ONNX na platformie Azure

Za pomocą usługi Azure Machine Learning można wdrażać, zarządzanie i monitorowanie swoimi modelami ONNX. Przy użyciu standardu [przepływ pracy wdrażania](concept-model-management-and-deployment.md) i środowisko uruchomieniowe ONNX, można utworzyć punktu końcowego REST, hostowane w chmurze. Zobacz pełny przykład notesu Jupyter na końcu tego artykułu, aby wypróbować to samodzielnie. 

### <a name="install-and-configure-the-onnx-runtime"></a>Instalowanie i konfigurowanie środowiska uruchomieniowego ONNX

Środowisko uruchomieniowe ONNX jest aparatem wnioskowania o wysokiej wydajności modelami ONNX. Go dołączono interfejs API języka Python i zapewnia przyspieszanie sprzętowe na GPU i CPU. Obecnie obsługuje modele ONNX 1.2 i działa w systemie Ubuntu 16.04 Linux. Zarówno [Procesora](https://pypi.org/project/onnxruntime) i [procesora GPU](https://pypi.org/project/onnxruntime-gpu) pakiety są dostępne na [PyPi.org](https://pypi.org).

Aby zainstalować środowisko uruchomieniowe ONNX, należy użyć:
```python
pip install onnxruntime
```

Aby wywołać ONNX środowiska uruchomieniowego w skrypcie języka Python, należy użyć:
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

Dokumentację, zwykle towarzyszący modelu informujący o tym, dane wejściowe i wyjściowe dla przy użyciu modelu. Można również użyć narzędzia wizualizacji takich jak [Netron](https://github.com/lutzroeder/Netron) do wyświetlenia modelu. Środowisko uruchomieniowe ONNX umożliwia także zapytania metadanych modelu, dane wejściowe i wyjściowe:
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Do wnioskowania model, użyj `run` i przekazywania na liście, danymi wyjściowymi zwracane (pozostaw puste, jeśli chcesz, aby wszystkie z nich) oraz mapę wartości wejściowych. Wynik jest lista dane wyjściowe.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Aby uzyskać pełną dokumentację interfejsu API, zobacz [dokumentacja środowiska uruchomieniowego ONNX](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Przykład kroki związane z wdrażaniem

Oto przykład wdrażania modelu ONNX:

1. Zainicjuj obszaru roboczego usługi Azure Machine Learning. Jeśli nie masz jeszcze, Dowiedz się, jak utworzyć obszar roboczy w [ten przewodnik Szybki Start](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Za pomocą usługi Azure Machine Learning, należy zarejestrować model.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Tworzenie obrazu za pomocą modelu i wszelkie zależności.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   Plik `score.py` zawiera logikę, oceniania i musi być uwzględniony w obrazie. Ten plik jest używany do uruchamiania modelu na obrazie. Zobacz ten [samouczek](tutorial-deploy-models-with-aml.md#create-scoring-script) dla skryptu instrukcje dotyczące sposobu tworzenia oceniania. Poniżej przedstawiono przykładowy plik z modelu ONNX:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   Plik `myenv.yml` opisano zależności niezbędne dla obrazu. Zobacz ten [samouczek](tutorial-deploy-models-with-aml.md#create-environment-file) instrukcje dotyczące sposobu tworzenia pliku środowiska, takie jak ten przykładowy plik:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Wdrażanie modelu ONNX za pomocą usługi Azure Machine Learning, aby:
   + Azure Container Instances (ACI): [Dowiedz się, jak...](how-to-deploy-to-aci.md)

   + Usługa Azure Kubernetes Service (AKS): [Dowiedz się, jak...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Przykłady
 
Następujące notesów pokazują, jak tworzyć modele ONNX i wdrażać je za pomocą usługi Azure Machine Learning: 
+ [onnx/onnx-modelzoo-aml wdrażanie resnet50.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb)
+ [onnx/onnx-convert-aml wdrażanie tinyyolo.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-convert-aml-deploy-tinyyolo.ipynb)
+ [onnx/onnx-Train-pytorch-AML-Deploy-mnist.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb)

Następujące notesów pokazują, jak wdrażać istniejące modele ONNX za pomocą usługi Azure Machine Learning: 
+ [onnx/onnx wnioskowania-mnist ręcznie zapisanych deploy.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-mnist-deploy.ipynb) 
+ [onnx/onnx-inference-facial-Expression-Recognition-Deploy.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-facial-expression-recognition-deploy.ipynb)
 
Pobierz te notesy:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Więcej informacji

Dowiedz się więcej o ONNX lub współtworzenia projektu:
+ [ONNX projektu witryny sieci Web](https://onnx.ai)

+ [Kod ONNX w serwisie GitHub](https://github.com/onnx/onnx)
