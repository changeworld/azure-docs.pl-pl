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
ms.openlocfilehash: a882a874574395095e98079cd0f8aa4a4987c749
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391798"
---
1. [Utwórz obszar roboczy usługi Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md).

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Dodaj plik konfiguracji obszaru roboczego do sklonowanego katalogu przy użyciu jednej z następujących metod:

    * W [witryny Azure portal](https://ms.portal.azure.com), wybierz opcję **Pobierz config.json** z **Przegląd** części obszaru roboczego. 

    ![Pobierz config.json](./media/aml-dsvm-server/download-config.png)

    * Utwórz nowy obszar roboczy za pomocą kodu w notesie [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) w sklonowanym katalogu.

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

    ```shell
    jupyter notebook
    ```