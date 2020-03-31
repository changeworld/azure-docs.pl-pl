---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296872"
---
Po zalogowaniu się zobaczysz listę subskrypcji skojarzonych z twoim kontem platformy Azure. Informacje o `isDefault: true` subskrypcji to aktualnie aktywowana subskrypcja poleceń interfejsu wiersza polecenia platformy Azure. Ta subskrypcja musi być taka sama, która zawiera obszar roboczy usługi Azure Machine Learning. Identyfikator subskrypcji można znaleźć w [witrynie Azure portal,](https://portal.azure.com) odwiedzając stronę przeglądu obszaru roboczego. Można również użyć SDK, aby uzyskać identyfikator subskrypcji z obiektu obszaru roboczego. Na przykład `Workspace.from_config().subscription_id`.
    
Aby wybrać inną [subskrypcję,](https://docs.microsoft.com/cli/azure/account#az-account-set) użyj polecenia az account set z identyfikatorem subskrypcji, aby przełączyć się na. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Używanie wielu subskrypcji platformy Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).