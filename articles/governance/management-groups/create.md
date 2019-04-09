---
title: Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure — Azure rządów
description: Dowiedz się, jak tworzyć grupy zarządzania platformy Azure do zarządzania wieloma zasobami przy użyciu portalu, programu Azure PowerShell i wiersza polecenia platformy Azure.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 928cb790bd97270870618534a73316bba5eeb070
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057442"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Tworzenie grupy zarządzania do organizacji zasobów i zarządzania

Grupy zarządzania są kontenery, które ułatwiają zarządzanie dostępem, zasady i zgodność w wielu subskrypcjach. Tworzenie tych kontenerów, aby tworzyć hierarchie skuteczny i wydajny, które mogą być używane z [usługi Azure Policy](../policy/overview.md) i [kontroli dostępu na podstawie roli Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Pierwsza grupa zarządzania tworzony w katalogu może potrwać do 15 minut. Brak procesów, które są uruchamiane po raz pierwszy do skonfigurowania usługi grupy zarządzania w obrębie platformy Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>Utwórz grupę zarządzania

Za pomocą witryny portal, programu PowerShell lub wiersza polecenia platformy Azure, można utworzyć grupy zarządzania. Obecnie nie możesz użyć szablonów usługi Resource Manager do tworzenia grup zarządzania.

### <a name="create-in-portal"></a>Tworzenie w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Na stronie głównej wybierz **nowym rozwiązaniem do zarządzania grupy**.

   ![Strona do pracy z grupami zarządzania](./media/main.png)

1. Wypełnij pola Identyfikator grupy zarządzania.

   - **Identyfikator grupy zarządzania** jest unikatowy identyfikator katalogu, który służy do przesyłania poleceń w tej grupie zarządzania. Ten identyfikator nie można edytować, po utworzeniu, ponieważ jest używany do identyfikowania tej grupy w systemie Azure. [Głównej grupy zarządzania](index.md#root-management-group-for-each-directory) jest tworzone automatycznie za pomocą Identyfikatora, który jest identyfikatorem usługi Azure Active Directory. Dla wszystkich innych grupach zarządzania należy przypisać unikatowy identyfikator.
   - Nazwa wyświetlana tego pola jest nazwa, która jest wyświetlana w witrynie Azure portal. Nazwę wyświetlaną osobne pole jest opcjonalne, tworząc zarządzania grupy i można ją zmienić w dowolnym momencie.  

   ![Okienko opcji tworzenia nowej grupy zarządzania](./media/create_context_menu.png)  

1. Wybierz pozycję **Zapisz**.

### <a name="create-in-powershell"></a>Tworzenie w programie PowerShell

W programie PowerShell, możesz użyć polecenia cmdlet New-AzManagementGroup:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** jest unikatowym identyfikatorem tworzona. Ten identyfikator jest używany przez inne polecenia, aby odwoływać się do tej grupy i nie można zmienić później.

Jeśli chce się grupy zarządzania, aby pokazać inną nazwę w witrynie Azure portal, należy dodać **DisplayName** parametr na ciąg. Na przykład jeśli chcesz utworzyć grupę zarządzania z GroupName Contoso i nazwę wyświetlaną grupy"Contoso", użyj następującego polecenia cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoTenant'
```

Użyj **ParentId** parametru tej grupy zarządzania można utworzyć w ramach różnych zarządzania.

### <a name="create-in-azure-cli"></a>Tworzenie w wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure, użyj az polecenia create grupy zarządzania kontem.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](create.md)
- [Jak zmienić, usunąć lub zarządzać grupami zarządzania](manage.md)
- [Przejrzyj grupy zarządzania w Module zasoby programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Przejrzyj grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Przegląd grup zarządzania w wiersza polecenia platformy Azure](/cli/azure/account/management-group)