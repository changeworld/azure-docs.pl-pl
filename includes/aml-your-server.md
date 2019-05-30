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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391793"
---
1. Postępuj zgodnie z instrukcjami w [Utwórz obszar roboczy usługi Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) wykonać następujące czynności:
    * Utwórz środowisko Miniconda
    * Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python
    * Tworzenie obszaru roboczego
    * Wpisywanie do pliku konfiguracji obszaru roboczego (**aml_config/config.json**).

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

    ```shell
    jupyter notebook
    ```