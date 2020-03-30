---
title: Dane osobowe
description: Dowiedz się, jak zarządzać danymi osobowymi skojarzonymi z operacjami usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485262"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Zarządzanie danymi osobowymi skojarzonymi z usługą Azure Resource Manager

Aby uniknąć ujawniania poufnych informacji, usuń wszelkie informacje osobiste podane we wdrożeniach, grupach zasobów lub tagach. Usługa Azure Resource Manager udostępnia operacje, które umożliwiają zarządzanie danymi osobistymi, które mogą być podane we wdrożeniach, grupach zasobów lub tagach.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Usuwanie danych osobowych w historii wdrażania

W przypadku wdrożeń Menedżer zasobów zachowuje wartości parametrów i komunikaty o stanie w historii wdrażania. Te wartości utrzymują się do momentu usunięcia wdrożenia z historii. Aby sprawdzić, czy podano dane osobowe w tych wartościach, wyświetl listę wdrożeń. Jeśli znajdziesz dane osobowe, usuń wdrożenia z historii.

Aby wyświetlić listę **wdrożeń** w historii, należy użyć:

* [Lista według grupy zasobów](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [lista wdrożeń grupy az](/cli/azure/group/deployment#az-group-deployment-list)

Aby usunąć **wdrożenia** z historii, należy użyć:

* [Usuwanie](/rest/api/resources/deployments/delete)
* [Usuń-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [Usuwanie wdrożenia grupy az](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Usuwanie danych osobowych w nazwach grup zasobów

Nazwa grupy zasobów będzie się powtarzać do momentu usunięcia grupy zasobów. Aby sprawdzić, czy dane osobowe podane są w nazwach, wyświetl listę grup zasobów. Jeśli znajdziesz dane osobowe, [przenieś zasoby](move-resource-group-and-subscription.md) do nowej grupy zasobów i usuń grupę zasobów z danymi osobowymi w nazwie.

Aby wyświetlić listę **grup zasobów,** należy użyć:

* [Listy](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az lista grup](/cli/azure/group#az-group-list)

Aby usunąć **grupy zasobów,** użyj:

* [Usuwanie](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Usuwanie danych osobowych w tagach

Nazwy znaczników i wartości utrzymują się do momentu usunięcia lub zmodyfikowania znacznika. Aby sprawdzić, czy w tagach podano dane osobowe, wyświetl znaczniki. Jeśli znajdziesz dane osobowe, usuń tagi.

Aby wyświetlić **znaczniki,** użyj:

* [Listy](/rest/api/resources/tags/list)
* [Get-AzTag (Get-AzTag)](/powershell/module/az.resources/Get-AzTag)
* [az lista znaczników](/cli/azure/tag#az-tag-list)

Aby usunąć **znaczniki,** użyj:

* [Usuwanie](/rest/api/resources/tags/delete)
* [Usuń-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete az tag delete az](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z omówieniem usługi Azure Resource Manager, zobacz [Co to jest Menedżer zasobów?](overview.md)