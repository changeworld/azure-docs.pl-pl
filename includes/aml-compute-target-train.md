---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156331"
---
**Obiekty docelowe obliczeń mogą być ponownie zażywane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny Wirtualnej do obszaru roboczego, można użyć go ponownie dla wielu zadań.  W przypadku potoków uczenia maszynowego użyj odpowiedniego [kroku potoku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) dla każdego obiektu docelowego obliczeń.

|Cele &nbsp;szkoleniowe|[Zautomatyzowane uczenie maszynowe](../articles/machine-learning/concept-automated-ml.md) | [Potoki uczenia maszynowego](../articles/machine-learning/concept-ml-pipelines.md) | [Projektant usługi Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/how-to-set-up-training-targets.md#local)| tak | &nbsp; | &nbsp; |
|[Klaster obliczeniowy usługi Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Tak & <br/>strojenie hiperparametryczne&nbsp; | tak | tak |
|[Zdalna maszyna wirtualna](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Tak & <br/>strojenie hiperparametryczne | tak | &nbsp; |
|[Usługi&nbsp;Azure Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| tak (tylko tryb lokalny SDK) | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | tak | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | tak | &nbsp; |
