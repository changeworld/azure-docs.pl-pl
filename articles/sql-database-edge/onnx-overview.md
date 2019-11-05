---
title: Uczenie maszynowe i AI z ONNX w wersji zapoznawczej Azure SQL Database Edge | Microsoft Docs
description: Usługa Machine Learning w Azure SQL Database Edge w wersji zapoznawczej obsługuje modele w formacie Open neuronowych Network Exchange (ONNX). ONNX to otwarty format, którego można użyć do wymiany modeli między różnymi strukturami i narzędziami uczenia maszynowego.
keywords: Wdróż krawędź bazy danych SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514019"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Uczenie maszynowe i AI z ONNX w wersji zapoznawczej SQL Database Edge

Usługa Machine Learning w Azure SQL Database Edge w wersji zapoznawczej obsługuje modele w formacie [Open neuronowych Network Exchange (ONNX)](https://onnx.ai/) . ONNX to otwarty format, którego można użyć do wymiany modeli między różnymi [strukturami i narzędziami uczenia maszynowego](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Obsługiwane zestawy narzędzi

ONNXMLTools umożliwia konwertowanie modeli z różnych zestawów narzędzi uczenia maszynowego na model ONNX. Obecnie w przypadku typów danych liczbowych i danych wejściowych z pojedynczymi kolumnami obsługiwane są następujące zestawy narzędzi:

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (wersja eksperymentalna)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>Pobierz modele ONNX

Istnieje kilka sposobów uzyskania modelu w formacie ONNX:

- [Model ONNX zoo](https://github.com/onnx/models): zawiera kilka wstępnie przeszkolonych modeli ONNX dla różnych typów zadań. Wersje, które są obsługiwane przez system Windows Machine Learning, można pobrać i używać.

- [Eksport natywny z platform uczenia maszynowego](https://onnx.ai/supported-tools): kilka platform szkoleniowych obsługuje funkcje eksportu natywnego ONNX, co pozwala na zapisywanie przeszkolonych modeli w określonej wersji formatu ONNX. Na przykład, Łańcucher, Caffee2 i PyTorch. Ponadto usługi, takie jak [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning-service/) i [Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) , udostępniają również natywny eksport ONNX.

  - Aby dowiedzieć się, jak nauczyć i wyeksportować model ONNX w chmurze przy użyciu Custom Vision, zobacz [Samouczek: Używanie modelu ONNX z Custom Vision z systemem Windows ml (wersja zapoznawcza)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Konwertuj istniejące modele przy użyciu WinMLTools](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools): Ten pakiet języka Python pozwala na konwersję modeli z kilku formatów struktury szkoleniowej na ONNX. Możesz określić, która wersja ONNX ma zostać przekonwertowana na model, w zależności od tego, które kompilacje obiektów docelowych aplikacji. Jeśli nie znasz języka Python, możesz użyć [pulpitu nawigacyjnego opartego na interfejsie użytkownika systemu Windows Machine Learning](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) , aby skonwertować modele.

> [!IMPORTANT]
> Nie wszystkie wersje ONNX są obsługiwane przez brzeg usługi Azure SQL Database. Obecnie obsługiwane są tylko przewidywania liczbowych typów danych za pomocą modelu ONNX.

Po utworzeniu modelu ONNX można wdrożyć model w Azure SQL Database Edge. Można następnie użyć [natywnej oceny z przewidywaną funkcją T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Ograniczenia

Obecnie ta obsługa jest ograniczona do modeli o **liczbowych typach danych**:

- [int i bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [rzeczywiste i zmiennoprzecinkowe](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Inne typy liczbowe mogą być konwertowane na obsługiwane typy przy użyciu CAST i CONVERT [Cast i Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Dane wejściowe modelu powinny mieć strukturę, aby każde wejście do modelu odnosi się do pojedynczej kolumny w tabeli. Na przykład jeśli używasz Pandas Dataframe do uczenia modelu, każdy element wejściowy powinien być oddzielną kolumną do modelu.

## <a name="next-steps"></a>Następne kroki

- [Wdróż SQL Database Edge przez Azure Portal](deploy-portal.md)
- [Wdróż model ONNX na Azure SQL Database Edge w wersji zapoznawczej](deploy-onnx.md)