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
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857449"
---
1. [Utwórz obszar roboczy usługi Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Dodaj plik konfiguracji obszaru roboczego do sklonowanego katalogu przy użyciu jednej z następujących metod:

    * W [Azure Portal](https://ms.portal.azure.com)wybierz pozycję **Pobierz plik config. JSON** z sekcji **Przegląd** w obszarze roboczym. 

    ![Pobierz plik config.json](./media/aml-dsvm-server/download-config.png)

    * Utwórz nowy obszar roboczy za pomocą kodu w notesie [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) w sklonowanym katalogu.

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

    ```shell
    jupyter notebook
    ```