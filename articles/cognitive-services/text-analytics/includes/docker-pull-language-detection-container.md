---
title: Wypychanie platformy Docker dla kontenera wykrywanie języka
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera wykrywanie języka
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966782"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Wypychanie platformy Docker dla kontenera wykrywanie języka

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [wykrywanie języka](https://go.microsoft.com/fwlink/?linkid=2018759) w usłudze Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
