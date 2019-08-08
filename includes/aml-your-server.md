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
ms.date: 01/25/2019
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846029"
---
- Azure Machine Learning zestawu SDK dla języka Python. Użyj instrukcji w [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) , aby wykonać następujące czynności:


1. Użyj instrukcji w [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) , aby wykonać następujące czynności:
    * Tworzenie środowiska Miniconda [Tworzenie obszarów roboczych usługi Azure Machine Learning i zarządzanie nimi]
    * Instalowanie zestawu SDK Azure Machine Learning dla języka Python

1. Utwórz [obszar roboczy usługi Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Napisz plik [konfiguracyjny](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config. JSON**).

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

    ```shell
    jupyter notebook
    ```