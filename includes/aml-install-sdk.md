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
ms.date: 07/27/2018
ms.openlocfilehash: 12f0f17a7c25696d2c8ff5b427f4b103617e5678
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431523"
---
W oknie wiersza polecenia lub oknie powłoki należy utworzyć i aktywować środowisko menedżera pakietów numpy i cython. W tym przykładzie użyto języka Python 3.6.

  + W systemie Windows:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       conda activate myenv
       ```

  + W systemie Linux lub MacOS:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       source activate myenv
       ```

Zainstaluj zestaw SDK.
   ```sh 
   pip install azureml-sdk
   ```
