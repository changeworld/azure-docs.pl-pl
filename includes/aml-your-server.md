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
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486147"
---
1. Użyj instrukcji w [usłudze Azure Machine Learning SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) aby zainstalować zestaw SDK usługi Azure Machine Learning dla języka Python

1. Utwórz [obszar roboczy usługi Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Napisz plik [pliku konfiguracyjnego](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. [Sklonuj repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

    ```bash
    jupyter notebook
    ```