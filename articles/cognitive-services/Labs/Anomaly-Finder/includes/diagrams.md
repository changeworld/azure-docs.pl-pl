---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888700"
---
Zwracane dane z oczekiwaną wartością oraz domyślnymi marginesami (górnym i dolnym). W praktyce możesz zdefiniować parametr [sensitivity], a następnie użyć polecenia (ExpectedValue + sensitivity * UpperMargin) jako górnej granicy oraz (ExpectedValue - sensitivity * LowerMargin) jako dolnej granicy, aby samodzielnie dostosować punkt anomalii. Wartość parametru [sensitivity] powinna być wyższa niż 1. Poniżej przedstawiono niektóre diagramy dotyczące dostosowywania.

> [!NOTE]
> Diagramy nie są generowane przez przykładową aplikację. Są one tworzone przez osobne narzędzie przy użyciu przykładowej aplikacji.

![Tunning: sensitivity = 1.0](../media/sensitivity_1.png)
![Tunning: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Tunning: sensitivity = 2](../media/sensitivity_2.png)
![Tunning: sensitivity = 3.5](../media/sensitivity_3.5.png)