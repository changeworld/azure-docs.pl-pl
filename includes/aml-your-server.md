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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395649"
---
1. Postępuj zgodnie z instrukcjami w [Utwórz obszar roboczy usługi Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) Aby utworzyć środowisko Miniconda, Utwórz obszar roboczy, a następnie zapisać pliku konfiguracji obszaru roboczego (**aml_config/config.json**) .

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Dodaj plik konfiguracji obszaru roboczego przy użyciu jednej z następujących metod:
    * Kopiuj **aml_config/config.json** plików, które zostały utworzone za pomocą przewodnika Szybki Start wymagań wstępnych w sklonowanym katalogu.
    * Utwórz nowy obszar roboczy za pomocą kodu w pliku [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Uruchom serwer notesów z poziomu sklonowanego katalogu.
    
    ```shell
    jupyter notebook
    ```