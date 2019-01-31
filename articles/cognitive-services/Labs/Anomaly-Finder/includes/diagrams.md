---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228971"
---
Zwracane dane z oczekiwaną wartością oraz domyślnymi marginesami (górnym i dolnym). W praktyce możesz zdefiniować parametr [sensitivity], a następnie użyć polecenia (ExpectedValue + sensitivity * UpperMargin) jako górnej granicy oraz (ExpectedValue - sensitivity * LowerMargin) jako dolnej granicy, aby samodzielnie dostosować punkt anomalii. Wartość parametru [sensitivity] powinna być wyższa niż 1. Poniżej przedstawiono niektóre diagramy dotyczące dostosowywania.

> [!NOTE]
> Diagramy nie są generowane przez przykładową aplikację. Są one tworzone przez osobne narzędzie przy użyciu przykładowej aplikacji.

![Tunning: sensitivity = 1.0](../media/sensitivity_1.png)
![Tunning: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Tunning: sensitivity = 2](../media/sensitivity_2.png)
![Tunning: sensitivity = 3.5](../media/sensitivity_3.5.png)
