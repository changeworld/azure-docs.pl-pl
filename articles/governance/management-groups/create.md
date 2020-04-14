---
title: Tworzenie grup zarządzania w celu organizowania zasobów — Zarządzanie usługą Azure
description: Dowiedz się, jak tworzyć grupy zarządzania platformy Azure do zarządzania wieloma zasobami przy użyciu portalu, usługi Azure PowerShell i interfejsu wiersza polecenia platformy Azure.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: a06679432d795b5b7854af8dc66b468841978e9c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273192"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Tworzenie grup zarządzania na potrzeby organizowania zasobów i zarządzania nimi

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością z wieloma subskrypcjami. Utwórz te kontenery, aby utworzyć skuteczną i wydajną hierarchię, która może być używana z [zasadami azure](../policy/overview.md) i [kontrolami dostępu opartymi na rolach platformy Azure.](../../role-based-access-control/overview.md) Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [Organizowanie zasobów za pomocą grup zarządzania platformy Azure](overview.md).

Pierwsza grupa zarządzania utworzona w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy, aby skonfigurować usługę grup zarządzania na platformie Azure dla katalogu. Otrzymasz powiadomienie po zakończeniu procesu. Aby uzyskać więcej informacji, zobacz [początkową konfigurację grup zarządzania](./overview.md#initial-setup-of-management-groups). 

## <a name="create-a-management-group"></a>Tworzenie grupy zarządzania

Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisane do tego użytkownika.  Ta nowa grupa zarządzania będzie elementem podrzędnym głównej grupy zarządzania, a twórca otrzyma przypisanie roli "Właściciel". Usługa grupy zarządzania umożliwia tę możliwość, dzięki czemu przypisania ról nie są potrzebne na poziomie głównym. Żaden użytkownik nie ma dostępu do głównej grupy zarządzania podczas jej tworzenia.  Aby uniknąć przeszkód związanych ze znalezieniem globalnych administratorów usługi Azure AD, aby rozpocząć korzystanie z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.      

Grupę zarządzania można utworzyć przy użyciu portalu, [szablonu Menedżera zasobów,](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="create-in-portal"></a>Tworzenie w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję Zarządzanie **wszystkimi usługami** > **+ zarządzanie**.

1. Wybierz **zarządzanie kosztami + rozliczenia**

1. Na stronie Zarządzanie kosztami + Rozliczenia — grupy zarządzania wybierz pozycję **Grupy zarządzania**

1. Wybierz **+ Dodaj grupę zarządzania**.

   ![Strona do pracy z grupami zarządzania](./media/main.png)

1. Wypełnij pole Identyfikator grupy zarządzania.

   - **Identyfikator grupy zarządzania** jest unikatowym identyfikatorem katalogu, który jest używany do przesyłania poleceń w tej grupie zarządzania. Ten identyfikator nie jest edytowalny po utworzeniu, ponieważ jest używany w całym systemie platformy Azure do identyfikowania tej grupy. Główna [grupa zarządzania](overview.md#root-management-group-for-each-directory) jest tworzona automatycznie przy identyfikatorze, który jest identyfikatorem usługi Azure Active Directory. W przypadku wszystkich innych grup zarządzania przypisz unikatowy identyfikator.
   - Pole nazwy wyświetlanej to nazwa wyświetlana w witrynie Azure portal. Oddzielna nazwa wyświetlana jest opcjonalnym polem podczas tworzenia grupy zarządzania i może zostać zmieniona w dowolnym momencie.  

   ![Okienko Opcje tworzenia nowej grupy zarządzania](./media/create_context_menu.png)  

1. Wybierz **pozycję Zapisz**.

### <a name="create-in-powershell"></a>Tworzenie w programie PowerShell

W przypadku programu PowerShell użyj polecenia cmdlet [New-AzManagementGroup,](/powershell/module/az.resources/new-azmanagementgroup) aby utworzyć nową grupę zarządzania.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**Nazwa grupy** jest tworzonym unikatowym identyfikatorem. Ten identyfikator jest używany przez inne polecenia do odwoływania się do tej grupy i nie można go zmienić później.

Jeśli chcesz, aby grupa zarządzania wyświetlała inną nazwę w witrynie Azure portal, dodaj parametr **DisplayName.** Na przykład, aby utworzyć grupę zarządzania o nazwie grupy Contoso i nazwie wyświetlanej "Grupa Contoso", należy użyć następującego polecenia cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

W poprzednich przykładach nowa grupa zarządzania jest tworzona w głównej grupie zarządzania. Aby określić inną grupę zarządzania jako element nadrzędny, należy użyć parametru **ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Tworzenie w usłudze Azure CLI

W przypadku interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure użyj polecenia [tworzenia grupy zarządzania kontem AZ,](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) aby utworzyć nową grupę zarządzania.

```azurecli-interactive
az account management-group create --name Contoso
```

**Nazwa** jest tworzony unikatowy identyfikator. Ten identyfikator jest używany przez inne polecenia do odwoływania się do tej grupy i nie można go zmienić później.

Jeśli chcesz, aby grupa zarządzania wyświetlała inną nazwę w witrynie Azure portal, dodaj parametr **nazwy wyświetlanej.** Na przykład, aby utworzyć grupę zarządzania o nazwie grupy Contoso i nazwie wyświetlanej "Grupa Contoso", użyj następującego polecenia:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

W poprzednich przykładach nowa grupa zarządzania jest tworzona w głównej grupie zarządzania. Aby określić inną grupę zarządzania jako element nadrzędny, należy użyć parametru **nadrzędnego** i podać nazwę grupy nadrzędnej.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)
