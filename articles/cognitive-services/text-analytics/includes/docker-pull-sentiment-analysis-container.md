---
title: Wypychanie platformy Docker dla kontenera analiza tonacji
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera analiza tonacji
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966685"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Wypychanie platformy Docker dla kontenera analiza tonacji

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [Analiza tonacji](https://go.microsoft.com/fwlink/?linkid=2018654) w usłudze Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
