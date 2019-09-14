---
title: Wypychanie platformy Docker dla kontenera wyodrębnianie kluczowych fraz
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera wyodrębnianie kluczowych fraz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966701"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Wypychanie platformy Docker dla kontenera wyodrębnianie kluczowych fraz

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [wyodrębnianie kluczowych fraz](https://go.microsoft.com/fwlink/?linkid=2018757) w usłudze Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
