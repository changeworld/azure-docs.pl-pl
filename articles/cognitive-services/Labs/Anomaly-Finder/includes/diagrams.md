---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348152"
---
Dane zwrócone z oczekiwaną wartością i domyślne marginesy górny i dolny. W praktyce należy można zdefiniować parametr [czułości], a następnie użyj (ExpectedValue + czułości * UpperMargin) jako górna granica i (ExpectedValue - czułości * LowerMargin) jako dolna granica dostrajania anomalii punktów przez yourselves. Wartość [czułości] powinna być większa niż 1. Poniżej przedstawiono niektóre diagramy dostrojenia.

> [!NOTE]
> Diagramy nie zostały wygenerowane przez przykładowej aplikacji. Są one tworzone przez osobnego narzędzia z przykładowej aplikacji.

![Podpowiedzi: czułość = 1.0](../media/sensitivity_1.png)
![podpowiedzi: czułość = 1.5](../media/sensitivity_1.5.png)
![podpowiedzi: czułość = 2](../media/sensitivity_2.png)
![podpowiedzi: czułość = 3.5](../media/sensitivity_3.5.png)