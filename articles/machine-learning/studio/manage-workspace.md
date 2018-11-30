---
title: Zarządzanie obszarem roboczym usługi Machine Learning — usługi Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Zarządzanie dostępem do obszarów roboczych usługi Azure Machine Learning, wdrażanie i zarządzanie usługami sieci web interfejsu API uczenia Maszynowego
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: c470d10b933b31d4cfe151fb541c2182562a2805
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314108"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Zarządzanie obszarem roboczym usługi Azure Machine Learning

> [!NOTE]
> Aby uzyskać informacje o zarządzaniu usługami sieci Web w portalu usług sieci Web Machine Learning, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md).
> 
> 

Możesz zarządzać obszarami roboczymi usługi Machine Learning, w witrynie Azure portal.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby zarządzanie obszarem roboczym w witrynie Azure portal:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta administratora subskrypcji platformy Azure.
2. W polu wyszukiwania w górnej części strony, wprowadź "maszyna obszarów roboczych uczenia", a następnie wybierz pozycję **Machine Learning w obszarach roboczych**.
3. Kliknij obszar roboczy, który chcesz zarządzać.

Oprócz informacji o zarządzaniu standardowych zasobów i dostępnych opcji możesz wykonywać następujące czynności:

- Widok **właściwości** — ta strona wyświetla informacje o obszarze roboczym i zasobów, i możesz zmienić subskrypcję i grupę zasobów, ten obszar roboczy jest połączony z.
- **Ponownie Synchronizuj klucze magazynu** — obszar roboczy przechowuje klucze do konta magazynu. Jeśli zmieni się na koncie magazynu kluczy, a następnie kliknięcie **ponowna synchronizacja kluczy** do zsynchronizowania kluczy z obszaru roboczego.

Aby zarządzać usługami sieci web skojarzony z tym obszarem roboczym, użyj portalu usług sieci Web Machine Learning. Zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md) pełne informacje.

> [!NOTE]
> Do wdrażania i zarządzania nią nowych usług sieci web musi mieć przypisaną rolę współautorem lub administratorem subskrypcji, w której wdrażana jest usługa sieci web. Jeśli użytkownik Zaproś innego użytkownika do obszaru roboczego usługi machine learning, należy przypisać je do roli administratora lub współautora w subskrypcji, przed wdrożeniem lub zarządzać usługami sieci web. 
> 
>Aby uzyskać więcej informacji o ustawianiu uprawnień dostępu, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [wdrażanie uczenie maszynowe przy użyciu szablonów usługi Azure Resource Manager](deploy-with-resource-manager-template.md). 