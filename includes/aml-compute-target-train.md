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
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580599"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań.

|Szkolenia &nbsp;cele| Obsługa procesora GPU |[Automatyczna ML](../articles/machine-learning/service/concept-automated-ml.md) | [Potoki ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Projektant Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| może | tak | &nbsp; | &nbsp; |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| tak | tak & <br/>dostrojenie&nbsp; | tak | tak |
|[Zdalna maszyna wirtualna](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |tak | tak & <br/>Dostrajanie parametrów | tak | &nbsp; |
|[Azure&nbsp;datakostki](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | tak | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | tak | &nbsp; |
|[Usługa Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | tak | &nbsp; |
