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
ms.openlocfilehash: 6bddfa048e4cb9cb7728c236417d4672df6f25b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476111"
---
1. [Utwórz obszar roboczy Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

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