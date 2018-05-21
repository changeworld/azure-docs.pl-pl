---
title: Danych osobistych Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać danych osobowych skojarzone z operacjami usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 5c176dd185a9d2a245045e9c954279506d713e5f
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Zarządzanie danych osobowych skojarzony z usługą Azure Resource Manager

Aby uniknąć udostępnianie poufnych informacji, należy usunąć żadnych informacji osobistych, które zostały podane w wdrożeń, grupy zasobów lub znaczników. Usługa Azure Resource Manager udostępnia operacje, które umożliwiają zarządzanie dane osobowe, które zostały podane w wdrożeń, grupy zasobów lub znaczników.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Usuwanie danych osobowych w historii wdrożenia

W przypadku wdrożeń usługi Resource Manager zachowuje wartości parametrów i komunikaty o stanie w historii wdrożenia. Te wartości będą się powtarzać do momentu usunięcia z historii wdrożenia. Aby zobaczyć, jeśli podano danych osobowych w tych wartości, listy wdrożeń. Jeśli dane osobowe, usunięcie jego wdrożeń z historii.

Do listy **wdrożeń** w historii, należy użyć:

* [Listy według grupy zasobów](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [AZ grupy wdrożenia listy](/cli/azure/group/deployment#az-group-deployment-list)

Aby usunąć **wdrożeń** z historii, należy użyć:

* [Usuwanie](/rest/api/resources/deployments/delete)
* [Usuń AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [Usuń wdrożenie grupy az](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Usuwanie danych osobowych w nazwy grup zasobów

Nazwa grupy zasobów jest zachowywana do czasu Usuń grupę zasobów. Aby zobaczyć, czy zostały podane dane osobowe w nazwach, lista grup zasobów. Jeśli okaże się dane osobowe [przeniesienie zasobów](resource-group-move-resources.md) do nowej grupy zasobów, a usunięcie grupy zasobów z danych osobowych w nazwie.

Do listy **grup zasobów**, użyj:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [Lista grup az](/cli/azure/group#az-group-list)

Aby usunąć **grup zasobów**, użyj:

* [Usuwanie](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Usuwanie danych osobowych w tagach

Nazwy tagów i wartości utrwalić aż do usunięcia lub zmodyfikowania tagu. Aby zobaczyć, czy zostały podane dane osobowe w tagach, listy tagów. Jeśli dane osobowe, usuń znaczniki.

Do listy **tagi**, użyj:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [Lista znaczników az](/cli/azure/tag#az-tag-list)

Aby usunąć **tagi**, użyj:

* [Usuwanie](/rest/api/resources/tags/delete)
* [Usuń AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [Usuń znacznik az](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Kolejne kroki
* Omówienie usługi Azure Resource Manager, zobacz [co to jest Resource Manager?](resource-group-overview.md)