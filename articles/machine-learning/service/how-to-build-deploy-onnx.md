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
ms.openlocfilehash: cc259e89bf6e4bcde5635d13951eaedb6f7b8849
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035015"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX i Azure Machine Learning: tworzenie i wdrażanie interoperacyjne modeli sztucznej Inteligencji

[Otwórz Exchange sieci neuronowych](http://onnx.ai) formatu (ONNX) jest standardem otwartym do reprezentowania modeli uczenia maszynowego. ONNX jest obsługiwana przez [społeczności partnerów](http://onnx.ai/supported-tools), łącznie z firmy Microsoft, który utworzyć zgodne strukturami i narzędziami. Firma Microsoft poświęca otwarte i interoperacyjne sztucznej Inteligencji, aby analitycy danych i deweloperzy mogą:

+ Struktura wybranych przez nich umożliwia tworzenie i uczenie modeli
+ Wdrażanie modeli dla wielu platform przy minimalnym integracji pracy

Firma Microsoft obsługuje ONNX jej produktów, w tym Azure i Windows, aby pomóc w osiąganiu tych celów.  

## <a name="why-choose-onnx"></a>Dlaczego warto wybrać ONNX?
Współdziałanie, który jest pobierany z ONNX sprawia, że można szybciej uzyskać dobre pomysły w środowisku produkcyjnym. Za pomocą ONNX analitykom danych można wybrać preferowaną szkielet dla zadania. Podobnie deweloperzy mogą poświęcać mniej czasu na przygotowanie modele do produkcji i wdrożyć między chmurą i urządzeniami brzegowymi.  

Możesz wyeksportować modelami ONNX z wielu platform, w tym PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet i strukturze ML.Net. Konwertery istnieje dla innych platform, takich jak TensorFlow, Keras, SciKit-Learn i nie tylko.

Istnieje również ekosystemu narzędzi do wizualizacji i skróceniu modelami ONNX. Liczbę wstępnie szkolone modele ONNX są również dostępne dla typowych scenariuszy.

[Można wdrażać modele ONNX](#deploy) w chmurze za pomocą usługi Azure Machine Learning i środowiska uruchomieniowego ONNX. Można je również wdrożyć na urządzeniach z systemem Windows 10 przy użyciu [Windows ML](https://docs.microsoft.com/windows/ai/). Nawet wdrażania ich na innych platformach przy użyciu konwertery są dostępne przez społeczność ONNX. 

[ ![Diagram przepływu ONNX pokazujący, szkolenia, konwerterów i wdrażania](media/concept-onnx/onnx.png) ] (. / media/concept-onnx/onnx.png#lightbox)

## <a name="create-onnx-models-in-azure"></a>Twórz modele ONNX na platformie Azure

Możesz tworzyć modele ONNX na kilka sposobów:
+ Uczenie modelu w usłudze Azure Machine Learning i przekonwertować lub wyeksportować je do ONNX (Zobacz przykład w dolnej części tego artykułu)

+ Pobierz wstępnie przeszkolonych modelu ONNX z [Zoo modelu ONNX](https://github.com/onnx/models)

+ Generowanie dostosowane modelu ONNX z [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="exportconvert-your-models-to-onnx"></a>Eksport/przekonwertować swoje modele, aby ONNX

Możesz również przeprowadzić konwersję swoje istniejące modele ONNX.
+ Aby uzyskać **PyTorch** modeli, wypróbuj [ten notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)

+ Aby uzyskać **Microsoft Cognitive Toolkit (CNTK)** modeli, wypróbuj [ten notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)

+ Aby uzyskać **Chainer** modeli, wypróbuj [ten notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)

+ Aby uzyskać **MXNet** modeli, wypróbuj [ten notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)

+ Aby uzyskać **TensorFlow** modeli, użyj [konwerter tensorflow onnx](https://github.com/onnx/tensorflow-onnx).

+ Aby uzyskać **Keras**, **ScitKit-Learn**, **CoreML**, **XGBoost**, i **libSVM** przekonwertować modelami ONNX za pomocą [WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools) pakietu.

Można znaleźć najnowszą listę obsługiwanych platform i konwertery na [lokacji samouczki ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Wdrażaj modele ONNX na platformie Azure

Za pomocą usługi Azure Machine Learning można wdrażać, zarządzanie i monitorowanie swoimi modelami ONNX. Przy użyciu standardu [przepływ pracy wdrażania](concept-model-management-and-deployment.md) i środowisko uruchomieniowe ONNX, można utworzyć punktu końcowego REST, hostowane w chmurze. Zobacz pełny przykład notesu Jupyter na końcu tego artykułu, aby wypróbować to samodzielnie. 

### <a name="install-and-configure-the-onnx-runtime"></a>Instalowanie i konfigurowanie środowiska uruchomieniowego ONNX

Środowisko uruchomieniowe ONNX jest aparatem wnioskowania o wysokiej wydajności modelami ONNX. Go dołączono interfejs API języka Python i zapewnia przyspieszanie sprzętowe na GPU i CPU. Obecnie obsługuje modele ONNX 1.2 i działa w systemie Ubuntu 16.04 Linux.

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

1. Inicjowanie usługi aplikacji Azure Machine Learning Workspace. Jeśli nie masz jeszcze, Dowiedz się, jak utworzyć obszar roboczy w [ten przewodnik Szybki Start](quickstart-get-started.md).

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

   ```
   name: myenv
   channels:
     - defaults
   dependencies:
     - pip:
       - onnxruntime
       - azureml-core
   ```

4. Wdrażanie modelu ONNX za pomocą usługi Azure Machine Learning, aby:
   + Azure Container Instances (ACI): [Dowiedz się, jak...](how-to-deploy-to-aci.md)

   + Usługa Azure Kubernetes Service (AKS): [Dowiedz się, jak...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Przykłady
 
Następujące notesów pokazują, jak wdrażać modele ONNX z usługą Azure Machine Learning: 
+ `/onnx/onnx-inference-mnist.ipynb`
 
+ `/onnx/onnx-inference-emotion-recognition.ipynb`
 
Pobierz ten notes:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Więcej informacji

Dowiedz się więcej o ONNX lub współtworzenia projektu:
+ [ONNX projektu witryny sieci Web](http://onnx.ai)

+ [Kod ONNX w serwisie GitHub](https://github.com/onnx/onnx)
