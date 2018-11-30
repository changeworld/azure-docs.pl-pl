---
title: Wdrażanie modeli w środowisku produkcyjnym — usługa Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak wdrażać modele do produkcji, umożliwiając im odtwarzanie aktywną rolę w podejmowaniu decyzji biznesowych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 5b1614f92f7633e008f4f7176723002dc7730b15
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495982"
---
# <a name="deploy-models-in-production"></a>Wdrażanie modeli w środowisku produkcyjnym

Wdrożenie produkcyjne umożliwia modelu do odtwarzania aktywną rolę w biznesie. Za pomocą wdrożonego modelu może służyć do podejmowania decyzji biznesowych.

## <a name="production-platforms"></a>Platform produkcyjnych

Istnieją różne podejścia i platformy, aby wdrożyć modele do produkcji. Poniżej przedstawiono kilka opcji:

- [Gdzie można wdrażać modele przy użyciu usługi Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Przed wdrożeniem jedna ma upewnij się, że opóźnienie oceniania modelu jest niskie, do użycia w środowisku produkcyjnym.
>

>[!NOTE]
>Do wdrożenia przy użyciu usługi Azure Machine Learning Studio, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B, testowanie

W przypadku wielu modeli w środowisku produkcyjnym, może być przydatne do wykonania [A / B, testowanie](https://en.wikipedia.org/wiki/A/B_testing) porównywanie wydajności modeli. 
 
## <a name="next-steps"></a>Kolejne kroki

Wskazówki, które przedstawiają wszystkie kroki procesu **konkretnych scenariuszy** znajdują się także. Wymieniono i połączone z opisami miniatur w [przykładowe przewodniki](walkthroughs.md) artykułu. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 
