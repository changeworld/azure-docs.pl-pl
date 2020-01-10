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
ms.openlocfilehash: 06800e7bb18634f1cbe847ced5450172106ec6f9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75840674"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań.  W przypadku potoków uczenia maszynowego Użyj odpowiedniego [kroku potoku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) dla każdego elementu docelowego obliczeń.

|Szkolenia &nbsp;cele|[Automatyczna ML](../articles/machine-learning/concept-automated-ml.md) | [Potoki ML](../articles/machine-learning/concept-ml-pipelines.md) | [Projektant Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/how-to-set-up-training-targets.md#local)| tak | &nbsp; | &nbsp; |
|[Wystąpienie obliczeniowe Azure Machine Learning (wersja zapoznawcza)](../articles/machine-learning/concept-compute-instance.md) | | tak |  |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| tak & <br/>dostrojenie&nbsp; | tak | tak |
|[Zdalnego maszyny Wirtualnej](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | tak & <br/>Dostrajanie parametrów | tak | &nbsp; |
|[Azure&nbsp;datakostki](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| tak (tylko tryb lokalny zestawu SDK) | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | tak | &nbsp; |
|[Usługa Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | tak | &nbsp; |
