---
title: Wypychanie platformy Docker dla kontenera analiza tonacji
titleSuffix: Azure Cognitive Services
description: Polecenie Docker pull dla kontenera analiza tonacji
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051273"
---
## <a name="pull-the-sentiment-analysis-container"></a>Ściąganie kontenera analiza tonacji

Obrazy kontenerów dla analiza tekstu są dostępne w programie Microsoft Container Registry.

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|-----------|------------|
| Analiza tonacji | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis znaczników dostępnych dla kontenerów analiza tekstu można znaleźć w kontenerze [Analiza tonacji](https://go.microsoft.com/fwlink/?linkid=2018654) w usłudze Docker Hub.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Wypychanie platformy Docker dla kontenera analiza tonacji

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
