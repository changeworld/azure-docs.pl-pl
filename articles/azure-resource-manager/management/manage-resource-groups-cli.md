---
title: Zarządzanie grupami zasobów — narzędzie Azure CLI
description: Użyj interfejsu wiersza polecenia platformy Azure do zarządzania grupami zasobów za pośrednictwem usługi Azure Resource Manager. Pokazuje, jak tworzyć, listy i usuwać grupy zasobów.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248335"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Zarządzanie grupami zasobów usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure za pomocą [usługi Azure Resource Manager](overview.md) do zarządzania grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [Zarządzanie zasobami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md).

Inne artykuły dotyczące zarządzania grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu witryny Azure portal](manage-resources-portal.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu programu Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Co to jest grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc należy dodać zasoby, które współużytkują ten sam cykl życia do tej samej grupy zasobów, dzięki czemu można je łatwo wdrożyć, zaktualizować i usunąć jako grupę.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. Po określeniu lokalizacji dla grupy zasobów, określasz, gdzie te metadane są przechowywane.

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

Poniższy skrypt interfejsu wiersza polecenia tworzy grupę zasobów, a następnie pokazuje grupę zasobów.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Lista grup zasobów

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

## <a name="delete-resource-groups"></a>Usuwanie grup zasobów

Następujący skrypt interfejsu wiersza polecenia usuwa grupę zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Aby uzyskać więcej informacji o tym, jak usługa Azure Resource Manager zamawia usuwanie zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów w istniejącej grupie zasobów

Zobacz [Wdrażanie zasobów w istniejącej grupie zasobów](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrożyć zasoby w grupie przy użyciu szablonu Menedżera zasobów. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Ponowne wdrażanie w przypadku niepowodzenia wdrażania

Ta funkcja jest również znana jako *Wycofywanie w sprawie błędu*. Aby uzyskać więcej informacji, zobacz [Ponowne wdrażanie, gdy wdrożenie nie powiedzie się.](../templates/rollback-on-error.md)

## <a name="move-to-another-resource-group-or-subscription"></a>Przejście do innej grupy zasobów lub subskrypcji

Zasoby w grupie można przenieść do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Przenoszenie zasobów](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Blokowanie grup zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, grupa zasobów lub zasób. 

Poniższy skrypt blokuje grupę zasobów, dzięki czemu nie można usunąć grupy zasobów.

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

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resource-groups"></a>Oznaczanie grup zasobów

Znaczniki można stosować do grup zasobów i zasobów, aby logicznie organizować zasoby. Aby uzyskać więcej informacji, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grup zasobów do szablonów

Po pomyślnym skonfigurowaniu grupy zasobów można wyświetlić szablon Menedżera zasobów dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Zautomatyzuj przyszłe wdrożenia rozwiązania, ponieważ szablon zawiera całą kompletną infrastrukturę.
- Dowiedz się składnia szablonu, patrząc na notację obiektu JavaScript (JSON), która reprezentuje rozwiązanie.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skrypt wyświetla szablon na konsoli.  Skopiuj JSON i zapisz jako plik.

Funkcja szablonu eksportu nie obsługuje eksportowania zasobów usługi Azure Data Factory. Aby dowiedzieć się, jak eksportować zasoby usługi Data Factory, zobacz [Kopiowanie lub klonowanie fabryki danych w usłudze Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Aby wyeksportować zasoby utworzone za pomocą klasycznego modelu wdrażania, należy [przeprowadzić migrację do modelu wdrażania Menedżera zasobów](https://aka.ms/migrateclassicresourcetoarm).

Aby uzyskać więcej informacji, zobacz [Eksport pojedynczych i wielozasóbowych do szablonu w witrynie Azure portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżera zasobów, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](../templates/template-syntax.md).
- Aby dowiedzieć się, jak tworzyć szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).