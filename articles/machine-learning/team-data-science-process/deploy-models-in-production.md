---
title: Wdrażanie modeli w produkcji — proces nauki o danych zespołowych
description: Jak wdrożyć modele w produkcji, umożliwiając im odgrywanie aktywnej roli w podejmowaniu decyzji biznesowych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722241"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Wdrażanie modeli w produkcji w celu odgrywania aktywnej roli w podejmowaniu decyzji biznesowych

Wdrożenie produkcyjne umożliwia modelowi odgrywanie aktywnej roli w firmie. Prognozy z wdrożonego modelu mogą być używane do podejmowania decyzji biznesowych.

## <a name="production-platforms"></a>Platformy produkcyjne

Istnieją różne podejścia i platformy do wprowadzenia modeli do produkcji. Oto kilka opcji:

- [Gdzie wdrożyć modele za pomocą usługi Azure Machine Learning](../how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Przed wdrożeniem należy ubezpieczyć opóźnienie oceniania modelu jest wystarczająco niska, aby użyć w produkcji.
>

>[!NOTE]
>Aby wdrożyć przy użyciu usługi Azure Machine Learning Studio, zobacz [Wdrażanie usługi sieci web usługi Azure Machine Learning.](../studio/deploy-a-machine-learning-web-service.md)
>

## <a name="ab-testing"></a>Testowanie A/B

Gdy wiele modeli jest w produkcji, [testy A/B](https://en.wikipedia.org/wiki/A/B_testing) mogą być używane do porównywania wydajności modelu. 
 
## <a name="next-steps"></a>Następne kroki

Dostępne są również wskazówki, które pokazują wszystkie kroki w procesie dla **określonych scenariuszy.** Są one wyświetlane i połączone z opisami miniatur w [przykładowym przewodniku](walkthroughs.md) artykułu. Ilustrują one sposób łączenia narzędzi i usług w chmurze, narzędzia lokalne i usługi w przepływ pracy lub potok, aby utworzyć inteligentną aplikację. 
