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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752955"
---
|Szkolenie &nbsp;elementów docelowych| Obsługa procesora GPU |[Automatyczne ML](../articles/machine-learning/service/concept-automated-ml.md) | [Potokach uczenia Maszynowego](../articles/machine-learning/service/concept-ml-pipelines.md) | [Interfejs wizualny](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Być może | tak | &nbsp; | &nbsp; |
|[Usługi Azure Machine Learning obliczeń](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| tak | Tak & <br/>hiperparametrycznego&nbsp;dostrajania | tak | tak |
|[Zdalnego maszyny Wirtualnej](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |tak | Tak & <br/>do strojenia hiperparametrycznego | tak | &nbsp; |
|[Azure&nbsp;usługi Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | tak | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | tak | &nbsp; |
|[Usługa Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | tak | &nbsp; |

**Wszystkie zasoby obliczeniowe elementy docelowe mogą być ponownie używane dla wielu zadań szkoleniowych**. Na przykład po dołączeniu maszyny Wirtualnej z systemem zdalnym do swojego obszaru roboczego, można ponownie użyć go dla wielu zadań.