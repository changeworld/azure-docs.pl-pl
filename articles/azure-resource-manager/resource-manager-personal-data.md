---
title: Usługa Azure Resource Manager dane osobowe | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie danymi osobowymi skojarzone z operacjami usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 71928be07080ed14fdcb93f33ea64d2572955b53
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38466028"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Zarządzanie danymi osobowymi skojarzonych z usługą Azure Resource Manager

Aby uniknąć ujawnienia poufnych informacji, należy usunąć wszelkie informacje osobiste, które zostały podane w wdrożenia, grupy zasobów lub tagi. Usługa Azure Resource Manager udostępnia operacje, które pozwalają na zarządzanie danymi osobowymi, które zostały podane w wdrożenia, grupy zasobów lub tagów.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Usunięcie danych osobowych w historii wdrożenia

W przypadku wdrożeń usługi Resource Manager przechowuje wartości parametrów i komunikaty o stanie w historii wdrażania. Te wartości są zachowywane dopóki nie zostaną usunięte z historii wdrożenia. Aby sprawdzić, czy podano danych osobowych w tych wartości, lista wdrożeń. Jeśli okaże się dane osobowe, usuwanie historii wdrożenia.

Do listy **wdrożeń** w historii, należy użyć:

* [Listy według grupy zasobów](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [AZ grupy wdrożenia listy](/cli/azure/group/deployment#az-group-deployment-list)

Aby usunąć **wdrożeń** z historii, możesz użyć:

* [Usuwanie](/rest/api/resources/deployments/delete)
* [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [Usuń wdrożenie grupy az](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Usuwanie danych osobowych na nazwy grup zasobów

Nazwa grupy zasobów będzie się powtarzać, dopóki nie usuniesz grupę zasobów. Aby sprawdzić, czy podano danych osobowych w nazwach, wyświetlanie listy grup zasobów. Jeśli okaże się dane osobowe [przenieść zasoby](resource-group-move-resources.md) do nowej grupy zasobów i Usuń grupę zasobów za pomocą danych osobowych w nazwie.

Do listy **grup zasobów**, użyj:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [Lista grup az](/cli/azure/group#az-group-list)

Aby usunąć **grup zasobów**, użyj:

* [Usuwanie](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Usunięcie danych osobowych w znacznikach

Nazwy tagów i wartości utrzymują się, dopóki nie można usunąć ani zmodyfikować tagu. Aby sprawdzić, czy podano danych osobowych w tagów, lista tagów. Jeśli okaże się dane osobowe, usuń znaczniki.

Do listy **tagi**, użyj:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [AZ tag list](/cli/azure/tag#az-tag-list)

Aby usunąć **tagi**, użyj:

* [Usuwanie](/rest/api/resources/tags/delete)
* [Usuń AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [AZ tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Kolejne kroki
* Omówienie usługi Azure Resource Manager, zobacz [nowości usługi Resource Manager?](resource-group-overview.md)