---
title: Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure
description: Dowiedz się, jak tworzyć grupy zarządzania platformy Azure do zarządzania wieloma zasobami.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2018
ms.author: rithorn
ms.openlocfilehash: b436336a3484ec27e4b90785d668ba905281f158
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018605"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Tworzenie grupy zarządzania do organizacji zasobów i zarządzania

Grupy zarządzania są kontenery, które ułatwiają zarządzanie dostępem, zasady i zgodność w wielu subskrypcjach. Tworzenie tych kontenerów, aby tworzyć hierarchie skuteczny i wydajny, które mogą być używane z [usługi Azure Policy](../policy/overview.md) i [kontroli dostępu na podstawie roli Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Pierwsza grupa zarządzania tworzony w katalogu może potrwać do 15 minut. Brak procesów, które są uruchamiane po raz pierwszy do skonfigurowania usługi grupy zarządzania w obrębie platformy Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie.

## <a name="create-a-management-group"></a>Utwórz grupę zarządzania

Za pomocą witryny portal, programu PowerShell lub wiersza polecenia platformy Azure, można utworzyć grupy zarządzania. Obecnie nie możesz użyć szablonów usługi Resource Manager do tworzenia grup zarządzania.

### <a name="create-in-portal"></a>Tworzenie w portalu

1. Zaloguj się do witryny [Azure Portal](http://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Na stronie głównej wybierz **nowym rozwiązaniem do zarządzania grupy**.

   ![Główna grupa](./media/main.png)

1. Wypełnij pola Identyfikator grupy zarządzania.

   - **Identyfikator grupy zarządzania** jest unikatowy identyfikator katalogu, który służy do przesyłania poleceń w tej grupie zarządzania. Ten identyfikator nie jest edytowalny po utworzeniu, ponieważ jest używany do identyfikowania tej grupy w systemie Azure.
   - Nazwa wyświetlana tego pola jest nazwa, która jest wyświetlana w witrynie Azure portal. Nazwę wyświetlaną osobne pole jest opcjonalne, tworząc zarządzania grupy i można ją zmienić w dowolnym momencie.  

   ![Przycisk Utwórz](./media/create_context_menu.png)  

1. Wybierz pozycję **Zapisz**.

### <a name="create-in-powershell"></a>Tworzenie w programie PowerShell

W programie PowerShell możesz użyć polecenia cmdlet New-AzureRmManagementGroups:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName** jest unikatowym identyfikatorem tworzona. Ten identyfikator jest używany przez inne polecenia, aby odwoływać się do tej grupy i nie można zmienić później.

Jeśli chce się grupy zarządzania, aby pokazać inną nazwę w witrynie Azure portal, należy dodać **DisplayName** parametr na ciąg. Na przykład jeśli chcesz utworzyć grupę zarządzania z GroupName Contoso i nazwę wyświetlaną grupy"Contoso", użyj następującego polecenia cmdlet:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

Użyj **ParentId** parametru tej grupy zarządzania można utworzyć w ramach różnych zarządzania.

### <a name="create-in-azure-cli"></a>Tworzenie w wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure, użyj az polecenia create grupy zarządzania kontem.

```azurecli-interactive
az account management-group create --group-name 'Contoso'
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](manage.md)
- [Instalowanie modułu Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Przegląd specyfikacji interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Zainstaluj rozszerzenie wiersza polecenia platformy Azure](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
