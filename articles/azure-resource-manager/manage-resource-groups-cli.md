---
title: Zarządzanie grupami Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Użyj interfejsu wiersza polecenia platformy Azure, aby zarządzać grupami Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: e55755e3edcc54d0d75c937c4d18b845b0282833
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972734"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Zarządzanie grupami zasobów Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak zarządzać grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure z [Azure Resource Manager](resource-group-overview.md) . Aby zarządzać zasobami platformy Azure, zobacz [Zarządzanie zasobami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](./manage-resources-cli.md).

Inne artykuły dotyczące zarządzania grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure Portal](./manage-resources-portal.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Co to jest Grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc, Dodaj zasoby, które mają ten sam cykl życia do tej samej grupy zasobów, dzięki czemu możesz łatwo wdrażać, aktualizować i usuwać je jako grupę.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. W przypadku określania lokalizacji grupy zasobów należy określić miejsce przechowywania metadanych.

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

Poniższy skrypt interfejsu wiersza polecenia tworzy grupę zasobów, a następnie wyświetla grupę zasobów.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Wyświetl listę grup zasobów

Poniższy skrypt interfejsu wiersza polecenia zawiera listę grup zasobów w ramach subskrypcji.

```azurecli-interactive
az group list
```

Aby uzyskać jedną grupę zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Usuń grupy zasobów

Następujący skrypt interfejsu wiersza polecenia usuwa grupę zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Resource Manager kolejności usuwania zasobów, zobacz [Azure Resource Manager Usuwanie grupy zasobów](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów w istniejącej grupie zasobów

Zobacz [wdrażanie zasobów w istniejącej grupie zasobów](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrożyć zasoby w grupie przy użyciu szablonu Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie w przypadku niepowodzenia wdrożenia

Ta funkcja jest również znana jako *Rollback w przypadku błędu*. Aby uzyskać więcej informacji, zobacz [ponowne wdrażanie w przypadku niepowodzenia wdrożenia](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Zasoby w grupie można przenieść do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Zablokuj grupy zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, Grupa zasobów lub zasób. 

Poniższy skrypt blokuje grupę zasobów, aby nie można było usunąć grupy zasobów.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Następujący skrypt pobiera wszystkie blokady dla grupy zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Następujący skrypt usuwa blokadę:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resource-groups"></a>Dodawanie tagów do grup zasobów

Możesz zastosować znaczniki do grup zasobów i zasobów, aby logicznie organizować zasoby. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grup zasobów do szablonów

Po pomyślnym skonfigurowaniu grupy zasobów warto wyświetlić szablon Menedżer zasobów dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Automatyzuj przyszłe wdrożenia rozwiązania, ponieważ szablon zawiera całą kompletną infrastrukturę.
- Poznaj składnię szablonu, przeglądając JavaScript Object Notation (JSON), który reprezentuje Twoje rozwiązanie.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skrypt wyświetla szablon w konsoli programu.  Skopiuj kod JSON i Zapisz go jako plik.

Aby uzyskać więcej informacji, zobacz [Eksportowanie jednego i wielu zasobów do szablonu w Azure Portal](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).