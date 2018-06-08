---
title: Zarządzanie obszaru roboczego usługi Machine Learning | Dokumentacja firmy Microsoft
description: Zarządzanie dostępem do usługi Azure Machine Learning obszary robocze oraz wdrażania i zarządzania nimi usług sieci web interfejsu API uczenia Maszynowego
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: 8c8e65d5fd7efd1f9212b76e1a72b91a0ea07272
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835070"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Zarządzanie obszarem roboczym usługi Azure Machine Learning

> [!NOTE]
> Informacje na temat zarządzania usługami sieci Web w portalu usługi sieci Web usługi Machine Learning, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md).
> 
> 

Możesz zarządzać obszarów roboczych uczenia maszynowego w portalu Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby zarządzać obszaru roboczego w portalu Azure:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) przy użyciu konta administratora subskrypcji platformy Azure.
2. W polu wyszukiwania w górnej części strony wprowadź "komputera learning obszary robocze", a następnie wybierz **obszarów roboczych Machine Learning**.
3. Kliknij obszar roboczy, którą chcesz zarządzać.

Oprócz informacji o zarządzaniu standardowych zasobów i dostępne opcje można:

- Widok **właściwości** — ta strona zawiera informacje dotyczące obszaru roboczego i zasobów, a można zmienić ten obszar roboczy jest połączony z grupą subskrypcji i zasobu.
- **Ponownej synchronizacji magazynu kluczy** -obszar roboczy obsługuje kluczy do konta magazynu. Jeśli zmienia się na koncie magazynu kluczy, a następnie kliknięcie **ponownej synchronizacji kluczy** zsynchronizować klucze z obszaru roboczego.

Aby zarządzać usługami sieci web skojarzony z tym obszarem roboczym, należy korzystać z portalu usługi sieci Web usługi Machine Learning. Zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md) pełne informacje.

> [!NOTE]
> Aby wdrożyć lub zarządzać nowych usług sieci web musi mieć przypisaną rolę współautora lub administrator w subskrypcji, w której wdrażana jest usługa sieci web. Jeśli musisz poprosić innego użytkownika do obszaru roboczego uczenia maszynowego, należy je przypisać do roli współautora lub administratora dla subskrypcji przed wdrożeniem lub zarządzania usługami sieci web. 
> 
>Aby uzyskać więcej informacji o ustawianiu uprawnień dostępu, zobacz [Wyświetl przypisania dostępu dla użytkowników i grup w portalu Azure](../../role-based-access-control/role-assignments-users.md).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [wdrażanie uczenie maszynowe Azure Resource Manager szablony](deploy-with-resource-manager-template.md). 