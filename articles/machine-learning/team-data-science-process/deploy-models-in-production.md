---
title: Wdrażanie modeli w środowisku produkcyjnym — zespołowe przetwarzanie danych
description: Jak wdrażać modele w środowisku produkcyjnym, dzięki czemu można odgrywać aktywną rolę w podejmowaniu decyzji biznesowe.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 94843bfc30b0d0d44284d533c715ff9632e6a6bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492372"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Wdrażaj modele w środowisku produkcyjnym, aby odgrywać aktywną rolę w podejmowaniu decyzji handlowych

Wdrożenie produkcyjne umożliwia modelowi odtworzenie aktywnej roli w firmie. Przewidywania ze wdrożonego modelu mogą być używane na potrzeby podejmowania decyzji dla firmy.

## <a name="production-platforms"></a>Platformy produkcyjne

Istnieją różne podejścia i platformy umożliwiające umieszczanie modeli w środowisku produkcyjnym. Oto kilka opcji:

- [Gdzie można wdrażać modele przy użyciu Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Przed wdrożeniem należy upewnić się, że opóźnienie oceny modelu jest wystarczająco małe, aby było używane w środowisku produkcyjnym.
>

>[!NOTE]
>Aby uzyskać wdrożenie przy użyciu Azure Machine Learning Studio, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Testowanie A/B

Gdy wiele modeli jest w środowisku produkcyjnym, przydatne może być przeprowadzenie [testowania A/B](https://en.wikipedia.org/wiki/A/B_testing) w celu porównania wydajności modeli. 
 
## <a name="next-steps"></a>Następne kroki

Dostępne są również instruktaże pokazujące wszystkie kroki procesu dla **konkretnych scenariuszy** . Są one wyświetlane i połączone z opisami miniatur w artykule [przykładowe instruktaże](walkthroughs.md) . Ilustrują one sposób łączenia chmur, narzędzi lokalnych i usług w przepływ pracy lub potoku w celu utworzenia inteligentnej aplikacji. 
