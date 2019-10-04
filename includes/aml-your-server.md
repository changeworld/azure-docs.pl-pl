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
ms.date: 09/26/2019
ms.openlocfilehash: 85d1c1cd294bfc02a2e0e327073bb6a80366548b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841099"
---
1. Aby zainstalować zestaw SDK Azure Machine Learning dla języka Python, Skorzystaj z instrukcji w [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) .

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
