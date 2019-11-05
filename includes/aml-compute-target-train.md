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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489579"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań.

|Szkolenia &nbsp;cele| Obsługa procesora GPU |[Automatyczna ML](../articles/machine-learning/service/concept-automated-ml.md) | [Potoki ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Projektant Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| może | tak | &nbsp; | &nbsp; |
|[Wystąpienie obliczeniowe Azure Machine Learning](../articles/machine-learning/service/concept-compute-instance.md)| tak | | tak |  |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| tak | tak & <br/>dostrojenie&nbsp; | tak | tak |
|[Zdalna maszyna wirtualna](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |tak | tak & <br/>Dostrajanie parametrów | tak | &nbsp; |
|[Azure&nbsp;datakostki](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | tak | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | tak | &nbsp; |
|[Usługa Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | tak | &nbsp; |
