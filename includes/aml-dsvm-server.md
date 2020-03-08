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
ms.openlocfilehash: e289cf7aea6e0ea46ff049f3ea8bf9e1517e8aaf
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673650"
---
1. [Utwórz obszar roboczy Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Dodaj plik konfiguracji obszaru roboczego do sklonowanego katalogu przy użyciu jednej z następujących metod:

    * W [Azure Portal](https://ms.portal.azure.com)wybierz pozycję **Pobierz plik config. JSON** z sekcji **Przegląd** w obszarze roboczym. 

    ![Pobierz plik config. JSON](./media/aml-dsvm-server/download-config.png)

    * Utwórz nowy obszar roboczy za pomocą kodu w notesie [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) w sklonowanym katalogu.

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

    ```shell
    jupyter notebook
    ```