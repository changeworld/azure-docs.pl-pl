---
title: 'ONNX: wysoki perf, wnioskowanie między platformami'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystanie z otwartej wymiany sieci neuronowych (ONNX) może pomóc w optymalizacji wnioskowania modelu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270175"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX i Azure Machine Learning: Tworzenie i przyspieszanie modeli uczenia maszynowego

Dowiedz się, jak korzystanie z [otwartej wymiany sieci neuronowych](https://onnx.ai) (ONNX) może pomóc w optymalizacji wnioskowania modelu uczenia maszynowego. Wnioskowanie lub ocenianie modelu jest fazą, w której wdrożony model jest używany do przewidywania, najczęściej na danych produkcyjnych. 

Optymalizacja modeli uczenia maszynowego do wnioskowania (lub oceniania modelu) jest trudne, ponieważ trzeba dostroić model i biblioteki wnioskowania, aby w pełni wykorzystać możliwości sprzętowe. Problem staje się niezwykle trudny, jeśli chcesz uzyskać optymalną wydajność na różnych rodzajach platform (chmura / krawędź, procesor / GPU, itp.), ponieważ każdy z nich ma różne możliwości i cechy. Złożoność zwiększa się, jeśli masz modele z różnych struktur, które muszą działać na różnych platformach. Optymalizacja wszystkich kombinacji struktur i sprzętu jest bardzo czasochłonna. Potrzebne jest rozwiązanie do szkolenia raz w preferowanej ramach i uruchomić w dowolnym miejscu w chmurze lub krawędzi jest potrzebne. W tym miejscu pojawia się ONNX.

Firma Microsoft i społeczność partnerów stworzyły ONNX jako otwarty standard reprezentowania modeli uczenia maszynowego. Modele z [wielu platform,](https://onnx.ai/supported-tools) w tym TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet i MATLAB mogą być eksportowane lub konwertowane do standardowego formatu ONNX. Gdy modele są w formacie ONNX, mogą być uruchamiane na różnych platformach i urządzeniach.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) to wysokowydajny aparat wnioskowania do wdrażania modeli ONNX w produkcji. Jest zoptymalizowany pod kątem chmury i krawędzi i działa na Systemach Linux, Windows i Mac. Napisany w języku C++, ma również interfejsy API języka C, Python i C#. ONNX Runtime zapewnia obsługę wszystkich specyfikacji ONNX-ML, a także integruje się z akceleratorami na różnych urządzeniach, takich jak TensorRT na procesorach graficznych NVidia.

Środowisko wykonawcze ONNX jest używane w usługach firmy Microsoft na dużą skalę, takich jak Bing, Office i Cognitive Services. Wzrost wydajności zależy od wielu czynników, ale te usługi Firmy Microsoft odnotowały __średni 2-krotny wzrost wydajności procesora.__ OnNX Runtime jest również używany jako część systemu Windows ML na setkach milionów urządzeń. Można użyć środowiska wykonawczego z usługi Azure Machine Learning. Korzystając ze środowiska wykonawczego ONNX, można korzystać z szeroko zakrojonych optymalizacji klasy produkcyjnej, testowania i ciągłych ulepszeń.

[![Diagram przepływu ONNX przedstawiający szkolenia, konwertery i wdrażanie](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Pobierz modele ONNX

Modele ONNX można uzyskać na kilka sposobów:
+ Trenuj nowy model ONNX w usłudze Azure Machine Learning (zobacz przykłady na dole tego artykułu)
+ Konwersja istniejącego modelu z innego formatu na ONNX (patrz [samouczki)](https://github.com/onnx/tutorials) 
+ Uzyskaj wstępnie przeszkolony model ONNX z [ONNX Model Zoo](https://github.com/onnx/models) (zobacz przykłady na dole tego artykułu)
+ Generowanie dostosowanego modelu ONNX z [usługi Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Wiele modeli, w tym klasyfikacja obrazów, wykrywanie obiektów i przetwarzanie tekstu, może być reprezentowanych jako modele ONNX. Jednak niektóre modele mogą nie być w stanie zostać pomyślnie przekonwertowane. Jeśli napotkasz tę sytuację, zgłoś problem w usłudze GitHub odpowiedniego konwertera, który był używany. Możesz kontynuować korzystanie z istniejącego modelu formatu, dopóki problem nie zostanie rozwiązany.

## <a name="deploy-onnx-models-in-azure"></a>Wdrażanie modeli ONNX na platformie Azure

Dzięki usłudze Azure Machine Learning możesz wdrażać modele ONNX, zarządzać nimi i monitorować je. Korzystając ze standardowego [przepływu pracy wdrażania](concept-model-management-and-deployment.md) i środowiska uruchomieniowego ONNX, można utworzyć punkt końcowy REST hostowany w chmurze. Zobacz przykładowe notesy Jupyter na końcu tego artykułu, aby wypróbować go dla siebie. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalowanie i używanie środowiska wykonawczego ONNX w języku Python

Pakiety Python dla ONNX Runtime są dostępne [na PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Przed [instalacją](https://github.com/Microsoft/onnxruntime#system-requirements) należy zapoznać się z wymaganiami systemowymi. 

 Aby zainstalować onnx runtime dla języka Python, użyj jednego z następujących poleceń: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Aby wywołać onnx środowiska uruchomieniowego w skrypcie języka Python, należy użyć:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Dokumentacja towarzysząca modelowi zwykle informuje o wejściach i wyjściach dla korzystania z modelu. Można również użyć narzędzia do wizualizacji, takiego jak [Netron,](https://github.com/lutzroeder/Netron) aby wyświetlić model. OnNX Runtime umożliwia również kwerendy metadanych modelu, dane wejściowe i wyjściowe:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Aby wnioskować modelu, `run` należy użyć i przekazać na liście wyjść, które chcesz zwrócić (pozostawić puste, jeśli chcesz wszystkie z nich) i mapę wartości wejściowych. Wynikiem jest lista wyjść.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Aby uzyskać pełne odwołanie do interfejsu API języka Python, zobacz [dokumenty odwołania do środowiska wykonawczego ONNX](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Przykłady

Zobacz [jak używać-azureml/deployment/onnx na](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) przykład notesów, które tworzą i wdrażają modele ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Więcej informacji

Dowiedz się więcej o ONNX lub wnieś udział w projekcie:
+ [Witryna internetowa firmy ONNX](https://onnx.ai)
+ [Kod ONNX w serwisie GitHub](https://github.com/onnx/onnx)

Dowiedz się więcej o onnx runtime lub przyczynić się do projektu:
+ [Onnx Środowisko uruchomieniowe GitHub Repo](https://github.com/Microsoft/onnxruntime)


