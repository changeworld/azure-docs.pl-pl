---
title: Zarządzanie obszarem roboczym usługi Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Zarządzanie dostępem do obszarów roboczych usługi Azure Machine Learning Studio, wdrażanie i zarządzanie usługami sieci web interfejsu API usługi Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: a947f9a94dd4ceed624e6b04a671b21b8926d25e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322791"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio

> [!NOTE]
> Aby uzyskać informacje o zarządzaniu usługami sieci Web w portalu usług sieci Web Machine Learning, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md).
> 
> 

Możesz zarządzać obszarami roboczymi usługi Machine Learning Studio, w witrynie Azure portal.



## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby zarządzać obszar roboczy Studio w witrynie Azure portal:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta administratora subskrypcji platformy Azure.
2. W polu wyszukiwania w górnej części strony, wprowadź "usługi machine learning Studio obszary robocze", a następnie wybierz pozycję **obszary robocze usługi Machine Learning Studio**.
3. Kliknij obszar roboczy, który chcesz zarządzać.

Oprócz informacji o zarządzaniu standardowych zasobów i dostępnych opcji możesz wykonywać następujące czynności:

- Widok **właściwości** — ta strona wyświetla informacje o obszarze roboczym i zasobów, i możesz zmienić subskrypcję i grupę zasobów, ten obszar roboczy jest połączony z.
- **Ponownie Synchronizuj klucze magazynu** — obszar roboczy przechowuje klucze do konta magazynu. Jeśli zmieni się na koncie magazynu kluczy, a następnie kliknięcie **ponowna synchronizacja kluczy** do zsynchronizowania kluczy z obszaru roboczego.

Aby zarządzać usługami sieci web skojarzony z tym obszarem roboczym Studio, użyj portalu usług sieci Web Machine Learning. Zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md) pełne informacje.

> [!NOTE]
> Do wdrażania i zarządzania nią nowych usług sieci web musi mieć przypisaną rolę współautorem lub administratorem subskrypcji, w której wdrażana jest usługa sieci web. Jeśli użytkownik Zaproś innego użytkownika do usługi machine learning Studio w obszarze roboczym, należy je przypisać do roli administratora lub współautora w subskrypcji przed wdrożeniem lub zarządzać usługami sieci web. 
> 
>Aby uzyskać więcej informacji o ustawianiu uprawnień dostępu, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [wdrażanie uczenie maszynowe przy użyciu szablonów usługi Azure Resource Manager](deploy-with-resource-manager-template.md). 
