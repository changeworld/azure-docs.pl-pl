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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021733"
---
1. Postępuj zgodnie z instrukcjami w [Utwórz obszar roboczy usługi Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) do:
    * Utwórz środowisko Miniconda
    * Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python
    * Tworzenie obszaru roboczego
    * Wpisywanie do pliku konfiguracji obszaru roboczego (**aml_config/config.json**).
    
1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Dodaj plik konfiguracji obszaru roboczego przy użyciu dowolnej z następujących metod:
    * Kopiuj **aml_config/config.json** pliku utworzonego w kroku 1 w sklonowanym katalogu.

    * W [witryny Azure portal](https://ms.portal.azure.com), wybierz opcję **Pobierz config.json** z **Przegląd** części obszaru roboczego. 

    ![Pobierz plik config.json](./media/aml-dsvm-server/download-config.png)

    * Utwórz nowy obszar roboczy za pomocą kodu w pliku [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.
    
    ```shell
    jupyter notebook
    ```