---
title: Uczenie maszynowe i sztuczna inteligencja z ONNX w usłudze Azure SQL Database Edge Preview | Dokumenty firmy Microsoft
description: Uczenie maszynowe w usłudze Azure SQL Database Edge Preview obsługuje modele w formacie Open Neural Network Exchange (ONNX). ONNX to otwarty format, którego można używać do wymiany modeli między różnymi strukturami i narzędziami uczenia maszynowego.
keywords: wdrażanie krawędzi bazy danych SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366269"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Uczenie maszynowe i AI z ONNX w podglądzie krawędzi bazy danych SQL

Uczenie maszynowe w usłudze Azure SQL Database Edge Preview obsługuje modele w formacie [Open Neural Network Exchange (ONNX).](https://onnx.ai/) ONNX to otwarty format, którego można używać do wymiany modeli między różnymi [strukturami i narzędziami uczenia maszynowego.](https://onnx.ai/supported-tools)

## <a name="overview"></a>Omówienie

Aby wywnioskować modele uczenia maszynowego w usłudze Azure SQL Database Edge, najpierw musisz uzyskać model. Może to być wstępnie przeszkolony model lub model niestandardowy przeszkolony z wybranej struktury. Usługa Azure SQL Database Edge obsługuje format ONNX i należy przekonwertować model na ten format. Nie powinno mieć wpływu na dokładność modelu, a po uzyskaniu modelu ONNX można wdrożyć model w usłudze Azure SQL Database Edge i używać [oceniania macierzystego za pomocą funkcji PREDICT T-SQL.](/sql/advanced-analytics/sql-native-scoring/)

## <a name="get-onnx-models"></a>Pobierz modele ONNX

Aby uzyskać model w formacie ONNX:

- **Model usługi budowlane:** Usługi, takie jak [funkcja automatycznego uczenia maszynowego w usłudze Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) i azure custom vision [service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) wsparcia bezpośrednio eksportowania przeszkolonego modelu w formacie ONNX.

- [**Konwertowanie i/lub eksportowanie istniejących modeli:**](https://github.com/onnx/tutorials#converting-to-onnx-format) [PyTorch](https://pytorch.org/docs/stable/onnx.html)Kilka struktur szkoleniowych (np. W przypadku struktur, które nie obsługują eksportu natywnego, istnieją autonomiczne pakiety instalowane ONNX Converter, które umożliwiają konwertowanie modeli przeszkolonych z różnych struktur uczenia maszynowego na format ONNX.

     **Obsługiwane struktury**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Przepływ tensoru](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-uczyć się](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Aby uzyskać pełną listę obsługiwanych struktur i przykładów, zobacz [Konwertowanie na format ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Ograniczenia

Obecnie nie wszystkie modele ONNX są obsługiwane przez usługę Azure SQL Database Edge. Obsługa jest ograniczona do modeli z **typami danych liczbowych:**

- [int i bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [prawdziwe i float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Inne typy liczbowe można konwertować na obsługiwane typy za pomocą [funkcji CAST i CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Dane wejściowe modelu powinny być tak skonstruowane, aby każde dane wejściowe do modelu odpowiadały jednej kolumnie w tabeli. Na przykład jeśli używasz pandas dataframe do szkolenia modelu, a następnie każde dane wejściowe powinny być oddzielne kolumny do modelu.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi SQL Database Edge w witrynie Azure portal](deploy-portal.md)
- [Wdrażanie modelu ONNX w usłudze Azure SQL Database Edge Preview](deploy-onnx.md)
