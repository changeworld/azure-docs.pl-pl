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
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875461"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań.  W przypadku potoków uczenia maszynowego Użyj odpowiedniego [kroku potoku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) dla każdego elementu docelowego obliczeń.

|Szkolenia &nbsp;cele|[Automatyczna ML](../articles/machine-learning/service/concept-automated-ml.md) | [Potoki ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Projektant Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| tak | &nbsp; | &nbsp; |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| tak & <br/>dostrojenie&nbsp; | tak | tak |
|[Zdalnego maszyny Wirtualnej](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | tak & <br/>Dostrajanie parametrów | tak | &nbsp; |
|[Azure&nbsp;datakostki](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| tak | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | tak | &nbsp; |
|[Usługa Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | tak | &nbsp; |
