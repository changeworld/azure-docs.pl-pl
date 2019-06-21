---
title: Zarządzanie grupami usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Użyj wiersza polecenia platformy Azure, aby zarządzać grupami usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c50a96b2598b89d5072a9441162d198163156c8d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296269"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Zarządzanie grupami zasobów usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure

Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure za pomocą [usługi Azure Resource Manager](resource-group-overview.md) na zarządzanie grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [zarządzanie zasobami platformy Azure przy użyciu wiersza polecenia platformy Azure](./manage-resources-cli.md).

Inne artykuły o zarządzaniu grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu witryny Azure portal](./manage-resources-portal.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu programu Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Co to jest grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc Dodaj zasoby, które współużytkują ten sam cykl życia w tej samej grupie zasobów, więc łatwe wdrażanie, aktualizowanie i usuwanie ich jako grupa.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. Po określeniu lokalizacji grupy zasobów, określasz, gdzie są przechowywane metadane.

## <a name="create-resource-groups"></a>Tworzenie grupy zasobów

Poniższy skrypt interfejsu wiersza polecenia tworzy grupę zasobów, a następnie wyświetla grupy zasobów.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Listy grup zasobów

Poniższy skrypt interfejsu wiersza polecenia wyświetla listę grup zasobów w ramach Twojej subskrypcji.

```azurecli-interactive
az group list
```

Aby uzyskać jedna grupa zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Usuwanie grupy zasobów

Poniższy skrypt interfejsu wiersza polecenia usuwa grupę zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Aby uzyskać więcej informacji o jak usługi Azure Resource Manager porządkuje usuwania zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów do istniejącej grupy zasobów

Zobacz [wdrażania zasobów istniejącą grupę zasobów](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrażania zasobów w grupie przy użyciu szablonu usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie, gdy wdrożenie zakończy się niepowodzeniem

Ta funkcja jest nazywana *wycofywania w przypadku błędu*. Aby uzyskać więcej informacji, zobacz [ponownego wdrażania w przypadku niepowodzenia wdrożenia](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Możesz przenieść zasoby w grupie, do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [przenoszenia zasobów](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Zablokuj grup zasobów

Zablokowanie uniemożliwia innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie krytyczne zasoby, takie jak subskrypcji platformy Azure, grupę zasobów lub zasobu. 

Poniższy skrypt blokuje grupę zasobów, dlatego nie można usunąć grupy zasobów.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Poniższy skrypt pobiera wszystkie blokady dla grupy zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Poniższy skrypt powoduje usunięcie blokady:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resource-groups"></a>Tag grupy zasobów

Znaczniki można dodawać do grup zasobów i zasobów w celu logicznego uporządkowania Twoich zasobów. Aby uzyskać informacje, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grupy zasobów do szablonów

Po pomyślnym skonfigurowaniu grupy zasobów można wyświetlić szablon usługi Resource Manager dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Zautomatyzuj przyszłych wdrożeń rozwiązania, ponieważ szablon zawiera całej infrastruktury.
- Dowiedz się składni szablonu, spoglądając na JavaScript Object Notation (JSON) reprezentujący rozwiązania.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skrypt w konsoli wyświetlany jest szablon.  Skopiuj kod JSON i Zapisz jako plik.

Aby uzyskać więcej informacji, zobacz [eksportu pojedynczych i wielu zasobów do szablonu w witrynie Azure portal](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby poznać usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby dowiedzieć się składni szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).