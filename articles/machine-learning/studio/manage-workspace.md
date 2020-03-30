---
title: Zarządzanie obszarami roboczymi
titleSuffix: ML Studio (classic) - Azure
description: Zarządzanie dostępem do obszarów roboczych usługi Azure Machine Learning Studio (klasyczne) oraz wdrażanie usług sieci web interfejsu API usługi usługi azure uczenie maszynowe i zarządzanie nimi
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217948"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Zarządzanie obszarem roboczym usługi Azure Machine Learning Studio (klasycznym)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Aby uzyskać informacje na temat zarządzania usługami sieci Web w portalu usług sieci Web uczenia [maszynowego, zobacz Zarządzanie usługą sieci Web przy użyciu portalu usług azure machine learning web.](manage-new-webservice.md)
> 
> 

Możesz zarządzać obszarami roboczymi Machine Learning Studio (klasycznymi) w witrynie Azure portal.



## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby zarządzać obszarem roboczym Studio (klasycznym) w witrynie Azure portal:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta administratora subskrypcji platformy Azure.
2. W polu wyszukiwania u góry strony wprowadź "machine learning Studio (klasyczne) obszary robocze", a następnie wybierz **machine Learning Studio (klasyczne) obszary robocze**.
3. Kliknij obszar roboczy, który chcesz zarządzać.

Oprócz standardowych dostępnych informacji i opcji zarządzania zasobami można:

- Wyświetl **właściwości** — na tej stronie są wyświetlane informacje o obszarze roboczym i zasobach oraz można zmienić subskrypcję i grupę zasobów, z którymi połączony jest ten obszar roboczy.
- **Ponowne synchronizowanie kluczy magazynu** — obszar roboczy przechowuje klucze do konta magazynu. Jeśli konto magazynu zmieni klucze, możesz kliknąć przycisk **Ponowniesynkowanie kluczy,** aby zsynchronizować klucze z obszarem roboczym.

Aby zarządzać usługami sieci web skojarzonymi z tym obszarem roboczym Studio (klasycznym), należy użyć portalu usług sieci Web uczenia maszynowego. Aby uzyskać pełne [informacje, zobacz Zarządzanie usługą sieci Web przy użyciu portalu usług azure machine learning web services.](manage-new-webservice.md)

> [!NOTE]
> Aby wdrożyć nowe usługi sieci web lub zarządzać nimi, należy przypisać rolę współautora lub administratora w subskrypcji, do której wdrożona jest usługa sieci web. Jeśli zaprosisz innego użytkownika do obszaru roboczego studia uczenia maszynowego (klasycznego), musisz przypisać go do roli współautora lub administratora w ramach subskrypcji, zanim będzie mógł wdrożyć usługi sieci web lub zarządzać nimi. 
> 
>Aby uzyskać więcej informacji na temat ustawiania uprawnień dostępu, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i portalu Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [wdrażaniu usługi Machine Learning za pomocą szablonów usługi Azure Resource Manager](deploy-with-resource-manager-template.md). 
