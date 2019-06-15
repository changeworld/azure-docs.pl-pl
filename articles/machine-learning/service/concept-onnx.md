---
title: Wysoka wydajność cross platform wnioskowania o ONNX
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o ONNX i środowiska uruchomieniowego ONNX przyspieszenia modeli
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: f1eca5bdd81a384efe04f769ebd12be9d91fc78a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849724"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX i usługi Azure Machine Learning: Tworzenie i przyspieszyć modeli uczenia Maszynowego

Dowiedz się, jak przy użyciu [Otwórz Exchange sieci neuronowych](https://onnx.ai) (ONNX) może pomóc zoptymalizować wnioskowania o model uczenia maszynowego. Wnioskowanie lub oceniania modelu, jest faza użycia wdrożony model do przewidywania najczęściej w danych produkcyjnych. 

Optymalizacja usługi machine learning modeli wnioskowania (lub modelu oceniania) jest trudne, ponieważ musisz dostosować model i biblioteki wnioskowania, aby maksymalnie wykorzystać możliwości sprzętu. Problem staje się niezmiernie utrudnić, jeśli chcesz uzyskać optymalną wydajność na różnych platformach (chmura/Microsoft edge, procesora CPU/GPU, itp.), ponieważ każda jedna ma różne możliwości i właściwości. Złożoność zwiększa się w przypadku modeli z poziomu wielu różnych platformach, które muszą zostać uruchomione na różnych platformach. Jest bardzo czasochłonne zoptymalizować różnych kombinacji platform i urządzeń. Rozwiązania do uczenia się jeden raz w preferowanej struktury preferowanych i uruchamianie w dowolnym miejscu w chmurze lub Microsoft edge jest wymagana. Jest to, gdzie ONNX jest oferowana w.

Firma Microsoft i społeczności partnerów utworzony ONNX jako otwarty standard do reprezentowania modeli uczenia maszynowego. Modele z [wiele struktur](https://onnx.ai/supported-tools) tym TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet i MATLAB mogą być wyeksportowane lub konwersji do formatu ONNX standardowego. Po modele formatu ONNX, ich może działać na różnych platformach i urządzeniach.

[Środowisko uruchomieniowe ONNX](https://github.com/Microsoft/onnxruntime) jest aparatem wnioskowania o wysokiej wydajności modelami ONNX do wdrożenia produkcyjnego. Jest zoptymalizowany pod kątem zarówno z chmurą i urządzeniami brzegowymi i działa w systemie Linux, Windows i Mac. Napisana C++, ma także C, Python, i C# interfejsów API. Środowisko uruchomieniowe ONNX zapewnia obsługę wszystkich specyfikacji ONNX ML i integruje się również z akceleratorów na różnym sprzęcie, takich jak TensorRT na procesory GPU NVidia.

Środowisko uruchomieniowe ONNX jest używany w dużej skali usług firmy Microsoft, takich jak Bing, Office i usługi Cognitive Services. Wzrost wydajności są zależne od szeregu czynników, ale przejrzane tych usług firmy Microsoft __średni 2 x są bardziej wydajne CPU__. Środowisko uruchomieniowe ONNX służy także jako część ML Windows na setki milionów urządzeń. Za pomocą środowiska uruchomieniowego usługi Azure Machine Learning. Za pomocą środowiska uruchomieniowego ONNX, możesz korzystać z optymalizacje rozbudowane klasy produkcyjnej, testowania i bieżących ulepszenia.

[![Diagram przepływu ONNX pokazujący, szkolenia, konwerterów i wdrażania](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Modele ONNX

Możesz uzyskać modelami ONNX na kilka sposobów:
+ Szkolenie nowego modelu ONNX w usłudze Azure Machine Learning (Zobacz przykłady w dolnej części tego artykułu)
+ Istniejący model przekonwertować z innego formatu ONNX (zobacz [samouczki](https://github.com/onnx/tutorials)) 
+ Pobierz wstępnie przeszkolonych modelu ONNX z [Zoo modelu ONNX](https://github.com/onnx/models) (Zobacz przykłady w dolnej części tego artykułu)
+ Generowanie dostosowane modelu ONNX z [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Wiele modeli, w tym Klasyfikacja obrazów, wykrywanie obiektów i przetwarzanie tekstu, może być reprezentowany jako modelami ONNX. Jednak w niektórych modelach może nie móc zostały pomyślnie skonwertowane. Jeśli napotkasz ten problem w serwisie GitHub odpowiednich konwertera, którego używano pliku wystąpił problem. Możesz kontynuować używanie istniejącego modelu format, dopóki problem został rozwiązany.

## <a name="deploy-onnx-models-in-azure"></a>Wdrażaj modele ONNX na platformie Azure

Za pomocą usługi Azure Machine Learning można wdrażać, zarządzanie i monitorowanie swoimi modelami ONNX. Przy użyciu standardu [przepływ pracy wdrażania](concept-model-management-and-deployment.md) i ONNX środowiska uruchomieniowego, można utworzyć punktu końcowego REST, hostowane w chmurze. Zobacz przykład notesów programu Jupyter na końcu tego artykułu, aby wypróbować to samodzielnie. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalowanie i używanie środowiska uruchomieniowego ONNX za pomocą języka Python

Pakiety języka Python dla programu obsługi ONNX są dostępne na [PyPi.org](https://pypi.org) ([Procesora](https://pypi.org/project/onnxruntime), [procesora GPU](https://pypi.org/project/onnxruntime-gpu)). Przeczytaj [wymagania systemowe](https://github.com/Microsoft/onnxruntime#system-requirements) przed rozpoczęciem instalacji. 

 Aby zainstalować środowisko uruchomieniowe ONNX dla języka Python, użyj jednej z następujących poleceń: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
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

## <a name="examples"></a>Przykłady

Zobacz [jak-to-użyj-usługi Azure ml/wdrażanie/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) na przykład notesów, które umożliwiają tworzenie i wdrażanie modelami ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Więcej informacji

Dowiedz się więcej o ONNX lub współtworzenia projektu:
+ [ONNX projektu witryny sieci Web](https://onnx.ai)
+ [Kod ONNX w serwisie GitHub](https://github.com/onnx/onnx)

Dowiedz się więcej na temat środowiska uruchomieniowego ONNX lub współtworzenia projektu:
+ [Repozytorium GitHub, aby ONNX środowiska uruchomieniowego](https://github.com/Microsoft/onnxruntime)


