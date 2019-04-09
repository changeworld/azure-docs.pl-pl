---
title: Tworzenie i wdrażanie modelami ONNX międzyoperacyjnych
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o ONNX i jak tworzyć i wdrażać modele ONNX za pomocą usługi Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: 33a93aa01499beb978f616f633588ba75e4b62a3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259188"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX i usługi Azure Machine Learning: Tworzenie i wdrażanie interoperacyjne modeli sztucznej Inteligencji

[Otwórz Exchange sieci neuronowych](https://onnx.ai) formatu (ONNX) jest standardem otwartym do reprezentowania modeli uczenia maszynowego. ONNX jest obsługiwana przez [społeczności partnerów](https://onnx.ai/supported-tools), łącznie z firmy Microsoft, który utworzyć zgodne strukturami i narzędziami. Firma Microsoft poświęca otwarte i interoperacyjne sztucznej Inteligencji, aby analitycy danych i deweloperzy mogą:

+ Struktura wybranych przez nich umożliwia tworzenie i uczenie modeli
+ Wdrażanie modeli dla wielu platform przy minimalnym integracji pracy

Firma Microsoft obsługuje ONNX jej produktów, w tym Azure i Windows, aby pomóc w osiąganiu tych celów.  

## <a name="why-choose-onnx"></a>Dlaczego warto wybrać ONNX?

Współdziałanie, który jest pobierany z ONNX sprawia, że można szybciej uzyskać dobre pomysły w środowisku produkcyjnym. Za pomocą ONNX analitykom danych można wybrać preferowaną szkielet dla zadania. Podobnie deweloperzy mogą poświęcać mniej czasu na przygotowanie modele do produkcji i wdrożyć między chmurą i urządzeniami brzegowymi.  

Można tworzyć modele ONNX z wielu platform, w tym PyTorch, Chainer, MXNet, strukturze ML.Net, TensorFlow, Keras, SciKit-Learn, Microsoft Cognitive Toolkit i inne.

Istnieje również ekosystemu narzędzi do wizualizacji i skróceniu modelami ONNX. Liczbę wstępnie szkolone modele ONNX są również dostępne dla typowych scenariuszy.

[Można wdrażać modele ONNX](#deploy) w chmurze za pomocą usługi Azure Machine Learning i środowisko uruchomieniowe ONNX. Można je również wdrożyć na urządzeniach z systemem Windows 10 przy użyciu [Windows ML](https://docs.microsoft.com/windows/ai/). Nawet wdrażania ich na innych platformach przy użyciu konwertery są dostępne przez społeczność ONNX. 

[![ODiagram przepływu NNX pokazujący, szkolenia, konwerterów i wdrażanie](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

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
|TensorFlow|[Konwerter tensorflow onnx](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, Dowiedz się ScitKit CoreML<br/>XGBoost i libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit|[Notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|

Można znaleźć najnowszą listę obsługiwanych platform i konwertery na [lokacji samouczki ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Wdrażaj modele ONNX na platformie Azure

Za pomocą usługi Azure Machine Learning można wdrażać, zarządzanie i monitorowanie swoimi modelami ONNX. Przy użyciu standardu [przepływ pracy wdrażania](concept-model-management-and-deployment.md) i ONNX środowiska uruchomieniowego, można utworzyć punktu końcowego REST, hostowane w chmurze. Zobacz pełny przykład notesu Jupyter na końcu tego artykułu, aby wypróbować to samodzielnie. 

### <a name="install-and-configure-onnx-runtime"></a>Instalowanie i konfigurowanie środowiska uruchomieniowego ONNX

Środowisko uruchomieniowe ONNX jest aparat typu open source wnioskowania o wysokiej wydajności dla modelami ONNX. Zapewnia przyspieszanie sprzętowe na procesor CPU i procesora GPU z interfejsami API dostępnymi dla języka Python, C#, i środowiska uruchomieniowego ONNX C. obsługuje ONNX 1.2 + modeli i działa w systemie Linux, Windows i Mac. Pakiety języka Python są dostępne na [PyPi.org](https://pypi.org) ([procesora CPU](https://pypi.org/project/onnxruntime), [procesora GPU](https://pypi.org/project/onnxruntime-gpu)), a [ C# pakietu](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/) znajduje się na [Nuget.org](https://www.nuget.org). Zobacz więcej informacji o projekcie w [GitHub](https://github.com/Microsoft/onnxruntime). Przeczytaj [wymagania systemowe](https://github.com/Microsoft/onnxruntime#system-requirements) przed rozpoczęciem instalacji.

Aby zainstalować środowisko uruchomieniowe ONNX dla języka Python, należy użyć:
```python
pip install onnxruntime
```

Aby wywołać ONNX w czasie wykonywania w skrypcie języka Python, należy użyć:
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

Aby uzyskać pełną dokumentację interfejsu API języka Python, zobacz [dokumentacja środowiska uruchomieniowego ONNX](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Przykład kroki związane z wdrażaniem

Oto przykład wdrażania modelu ONNX:

1. Zainicjuj obszaru roboczego usługi Azure Machine Learning. Jeśli nie masz jeszcze, Dowiedz się, jak [Utwórz obszar roboczy](setup-create-workspace.md).

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

   > [!TIP]
   > W poprzednim przykładzie użyto domyślnego obrazu udostępniane przez usługę Azure Machine Learning. Można również użyć obrazu niestandardowego. Aby uzyskać więcej informacji, zobacz Konfigurowanie i rejestrowanie części obrazu [wdrażanie modeli](how-to-deploy-and-where.md#configureimage).

   Plik `score.py` zawiera logikę, oceniania i musi być uwzględniony w obrazie. Ten plik jest używany do uruchamiania modelu na obrazie. Zobacz ten [samouczek](tutorial-deploy-models-with-aml.md#create-scoring-script) dla skryptu instrukcje dotyczące sposobu tworzenia oceniania. Poniżej przedstawiono przykładowy plik z modelu ONNX:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global session
       model = Model.get_model_path(model_name = 'MyONNXModel')
       session = onnxruntime.InferenceSession(model)

   def preprocess(input_data_json):
       # convert the JSON data into the tensor input
       return np.array(json.loads(input_data_json)['data']).astype('float32')

   def postprocess(result):
       return np.array(result).tolist()

   def run(input_data_json):
       try:
           start = time.time()   # start timer
           input_data = preprocess(input_data_json)
           input_name = session.get_inputs()[0].name  # get the id of the first input of the model   
           result = session.run([], {input_name: input_data})
           end = time.time()     # stop timer
           return {"result": postprocess(result),
                   "time": end - start}
       except Exception as e:
           result = str(e)
           return {"error": result}
   ```

   Plik `myenv.yml` opisano zależności niezbędne dla obrazu. Zobacz ten [samouczek](tutorial-deploy-models-with-aml.md#create-environment-file) instrukcje dotyczące sposobu tworzenia pliku środowiska, takie jak ten przykładowy plik:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies.create(pip_packages=["numpy","onnxruntime","azureml-core"])

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Aby wdrożyć modelu, zobacz [sposób wdrażania i gdzie](how-to-deploy-and-where.md) dokumentu.


## <a name="examples"></a>Przykłady

Zobacz [jak-to-użyj-usługi Azure ml/wdrażanie/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) na przykład notesów, które umożliwiają tworzenie i wdrażanie modelami ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Więcej informacji

Dowiedz się więcej o ONNX lub współtworzenia projektu:
+ [ONNX project website](https://onnx.ai)

+ [Kod ONNX w serwisie GitHub](https://github.com/onnx/onnx)

Dowiedz się więcej na temat środowiska uruchomieniowego ONNX lub współtworzenia projektu:
+ [Repozytorium GitHub, aby ONNX środowiska uruchomieniowego](https://github.com/Microsoft/onnxruntime)


