---
title: Uczenie maszynowe i AI z ONNX w wersji zapoznawczej Azure SQL Database Edge | Microsoft Docs
description: Usługa Machine Learning w Azure SQL Database Edge w wersji zapoznawczej obsługuje modele w formacie Open neuronowych Network Exchange (ONNX). ONNX to otwarty format, którego można użyć do wymiany modeli między różnymi strukturami i narzędziami uczenia maszynowego.
keywords: Wdróż krawędź bazy danych SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822844"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Uczenie maszynowe i AI z ONNX w wersji zapoznawczej SQL Database Edge

Usługa Machine Learning w Azure SQL Database Edge w wersji zapoznawczej obsługuje modele w formacie [Open neuronowych Network Exchange (ONNX)](https://onnx.ai/) . ONNX to otwarty format, którego można użyć do wymiany modeli między różnymi [strukturami i narzędziami uczenia maszynowego](https://onnx.ai/supported-tools).

## <a name="overview"></a>Omówienie

Aby wywnioskować modele uczenia maszynowego w Azure SQL Database Edge, najpierw musisz uzyskać model. Może to być model wstępnie szkolony lub model niestandardowy szkolony z Twoją platformą. Azure SQL Database Edge obsługuje format ONNX i należy przekonwertować model na ten format. Nie powinno mieć wpływu na dokładność modelu i po utworzeniu modelu ONNX można wdrożyć model w Azure SQL Database Edge i użyć [natywnego oceniania z przewidywaną funkcją T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Pobierz modele ONNX

Istnieje kilka sposobów uzyskania modelu w formacie ONNX:

- [ONNX model Zoo](https://github.com/onnx/models): zawiera wiele wstępnie przeszkolonych modeli ONNX dla różnych typów zadań, które można pobrać i są gotowe do użycia.

- [Natywne eksporty z platform szkoleniowych ml](https://onnx.ai/supported-tools): kilka platform szkoleniowych obsługuje natywną funkcję eksportu ONNX, co pozwala na zapisywanie przeszkolonego modelu w określonej wersji formatu ONNX, w tym [PyTorch](https://pytorch.org/docs/stable/onnx.html), łańcucher i Caffe2. Ponadto usługi do kompilowania modeli, takie jak [Funkcja automatycznego Machine Learning w systemach Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) i [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) udostępniają eksport ONNX.

- [Konwertuj istniejące modele](https://github.com/onnx/tutorials#converting-to-onnx-format): dla struktur, które nie obsługują eksportu natywnego, istnieją Autonomiczne pakiety umożliwiające konwersję modeli do formatu ONNX. Aby zapoznać się z przykładami i samouczkami, zobacz [konwertowanie do formatu ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Obsługiwane struktury

Konwertery ONNX umożliwiają konwertowanie modeli przeszkolonych z różnych platform uczenia maszynowego w formacie ONNX. Popularne konwertery obejmują: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

Aby zapoznać się z pełną listą obsługiwanych platform, zobacz [konwertowanie do formatu ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Ograniczenia

Obecnie nie wszystkie modele ONNX są obsługiwane przez Azure SQL Database Edge. Obsługa jest ograniczona do modeli o **liczbowych typach danych**:

- [int i bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [rzeczywiste i zmiennoprzecinkowe](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Inne typy liczbowe mogą być konwertowane na obsługiwane typy przy użyciu [Cast i Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Dane wejściowe modelu powinny mieć strukturę, aby każde wejście do modelu odnosi się do pojedynczej kolumny w tabeli. Na przykład jeśli używasz Pandas Dataframe do uczenia modelu, każdy element wejściowy powinien być oddzielną kolumną do modelu.

## <a name="next-steps"></a>Następne kroki

- [Wdróż SQL Database Edge przez Azure Portal](deploy-portal.md)
- [Wdróż model ONNX na Azure SQL Database Edge w wersji zapoznawczej](deploy-onnx.md)
