---
title: Jak zmienić, usunąć lub zarządzać grupami zarządzania - Azure rządów
description: Dowiedz się, jak wyświetlać, obsługi, aktualizacji i usuwania hierarchia grup zarządzania.
author: rthorn17
ms.service: governance
ms.date: 05/22/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 028b4cbf62bf9ed0b3b38f54d3b787a8c1368da0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242948"
---
# <a name="manage-your-resources-with-management-groups"></a>Zarządzanie zasobami przy użyciu grup zarządzania

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.  Aby dowiedzieć się więcej na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>Zmień nazwę grupy zarządzania

Możesz zmienić nazwę grupy zarządzania przy użyciu portalu, programu PowerShell lub wiersza polecenia platformy Azure.

### <a name="change-the-name-in-the-portal"></a>Zmień nazwę w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Wybierz grupę zarządzania, którą chcesz zmienić.

1. Wybierz **szczegóły**.

1. Wybierz **Zmień nazwę grupy** opcji w górnej części strony.

   ![Na stronie grupy zarządzania przy użyciu opcji Zmień nazwę grupy](./media/detail_action_small.png)

1. Po otwarciu menu, wprowadź nową nazwę, które Twoim zdaniem, które zostaną wyświetlone.

   ![Zmień nazwę grupy okienka, aby zmienić nazwę grupy zarządzania](./media/rename_context.png)

1. Wybierz pozycję **Zapisz**.

### <a name="change-the-name-in-powershell"></a>Zmień nazwę w programie PowerShell

Można zaktualizować Użyj nazwę wyświetlaną **AzManagementGroup aktualizacji**. Na przykład Zarządzanie zmianami grup nazwy wyświetlanej, od "Contoso IT", aby "Grupa firmy Contoso", uruchom następujące polecenie:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Zmień nazwę w interfejsie wiersza polecenia platformy Azure

Wiersza polecenia platformy Azure użyj polecenia update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Usuwanie grupy zarządzania

Aby usunąć grupę zarządzania, muszą być spełnione następujące wymagania:

1. Nie istnieją żadne podrzędne grupy zarządzania lub subskrypcji w ramach grupy zarządzania.

   - Aby przenieść subskrypcję z grupą zarządzania, zobacz [przenieść subskrypcję do innej grupy zarządzania](#move-subscriptions-in-the-hierarchy).

   - Aby przenieść grupę zarządzania do innej grupy zarządzania, zobacz [przenieść grup zarządzania w hierarchii](#move-management-groups-in-the-hierarchy).

1. Masz uprawnienia do zapisu w grupie zarządzania ("Owner", "Współautor" lub "Współautor grupy zarządzania"). Aby zobaczyć, jakie uprawnienia ma, wybierz grupę zarządzania a następnie wybierz **IAM**. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [zarządzanie dostępem i uprawnieniami przy użyciu RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Usuwanie w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Wybierz grupę zarządzania, którą chcesz usunąć.

1. Wybierz **szczegóły**.

1. Wybierz **Usuń**

    > [!TIP]
    > Jeśli ikona jest wyłączona, kursor nad swoje selektor myszy na ikonie dowiesz się, powód.

   ![Usuń opcję grupy](./media/delete.png)

1. Brak okno które otwiera potwierdzenie, że chcesz usunąć grupę zarządzania.

   ![Usuwanie grupy oknie potwierdzenia](./media/delete_confirm.png)

1. Wybierz **tak**.

### <a name="delete-in-powershell"></a>Usuwanie w programie PowerShell

Użyj **AzManagementGroup Usuń** polecenia w ramach programu PowerShell, można usunąć grupy zarządzania.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Usuwanie w interfejsie wiersza polecenia platformy Azure

Za pomocą wiersza polecenia platformy Azure Użyj usunięcia grupy zarządzania polecenia az konta.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Wyświetlanie grup zarządzania

Możesz wyświetlić wszystkie grupy zarządzania już bezpośredniego lub dziedziczonego rolę RBAC z.  

### <a name="view-in-the-portal"></a>Wyświetl w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Na stronie hierarchii grupy zarządzania zostanie załadowany. Ta strona jest, gdzie możesz eksplorować wszystkie grupy zarządzania, a subskrypcje mają dostęp do. Wybieranie nazwy grupy przejście w dół poziom w hierarchii. Nawigacja działa tak samo, jak Eksplorator plików.

1. Aby wyświetlić szczegóły grupy zarządzania, wybierz **(szczegóły)** łącze obok tytułu grupy zarządzania. Jeśli ten link nie jest dostępna, nie masz uprawnień do wyświetlania tej grupy zarządzania.

   ![Main](./media/main.png)

### <a name="view-in-powershell"></a>Widok w programie PowerShell

Polecenie Get-AzManagementGroup do pobrania wszystkich grup.  Zobacz [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) modułów, aby uzyskać pełną listę zarządzania grupy poleceń pobieranie programu Powershell.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Grupę zarządzania pojedynczego informacji Użyj parametru - GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Aby zwrócić określonej grupy zarządzania i wszystkie poziomy hierarchii na jej podstawie, użyj **-rozwiń** i **-Recurse** parametrów.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Widok wiersza polecenia platformy Azure

Polecenie listy jest używane do pobierania wszystkich grup.  

```azurecli-interactive
az account management-group list
```

Grupę zarządzania pojedynczego informacji użyj polecenia show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Aby zwrócić określonej grupy zarządzania i wszystkie poziomy hierarchii na jej podstawie, użyj **-rozwiń** i **-Recurse** parametrów.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Przenieś subskrypcje w hierarchii

Jednym z powodów tworzenia grupy zarządzania jest powiązać razem subskrypcji. Elementy podrzędne w innej grupie zarządzania można wprowadzić tylko grupy zarządzania i subskrypcje. Subskrypcję, która przenosi do grupy zarządzania dziedziczy wszystkie dostępu użytkownika i zasad z nadrzędnej grupy zarządzania.

Aby przenieść subskrypcję, wszystkie z następujących uprawnień RBAC, muszą być spełnione:

- Rola "Właściciel" w subskrypcji podrzędnych.
- "Właściciel", "Współautor" lub "Współautor grupy zarządzania" Rola nadrzędna grupa zarządzania docelowego.
- Rola "Owner", "Współautor" lub "Współautor grupy zarządzania" w istniejącej grupie zarządzania nadrzędnej.

W przypadku istniejącej grupy zarządzania nadrzędnej lub docelowe głównej grupy zarządzania, wymagania dotyczące uprawnień nie mają zastosowania. Ponieważ głównej grupy zarządzania jest domyślnie docelowa dodatkowego dla wszystkich nowych grup zarządzania, jak i subskrypcje, nie potrzebujesz uprawnienia do niego, aby przenieść element.

Jeśli rola właściciela subskrypcji jest dziedziczona z bieżącej grupy zarządzania, Przenieś elementy docelowe są ograniczone. Można przenosić tylko subskrypcji do innej grupy zarządzania, w których masz rolę właściciela. Nie można przenieść ją do grupy zarządzania których jesteś współautorem, ponieważ spowoduje utratę własności subskrypcji. Jeśli bezpośrednio masz przypisaną rolę właściciela subskrypcji (nie dziedziczone z grupy zarządzania), można przenieść ją do żadnej grupy zarządzania, w których jesteś współautorem.

Aby zobaczyć, jakie posiadasz uprawnienia w witrynie Azure portal, wybierz opcję zarządzania w grupie, a następnie wybierz pozycję **IAM**. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [zarządzanie dostępem i uprawnieniami przy użyciu RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Przeniesienie subskrypcji w portalu

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Dodać istniejącą subskrypcję do grupy zarządzania

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Wybierz grupę zarządzania, którą zamierzasz jako nadrzędną.

1. W górnej części strony wybierz **Dodaj subskrypcję**.

1. Wybierz subskrypcję z listy poprawny identyfikator.

   ![Dostępne subskrypcje, aby dodać do grupy zarządzania](./media/add_context_sub.png)

1. Wybierz pozycję "Zapisz".

#### <a name="remove-a-subscription-from-a-management-group"></a>Usuń subskrypcję z grupy zarządzania

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Wybierz grupę zarządzania, planowane jest to znaczy bieżącym elementem nadrzędnym.  

1. Na liście, który chcesz przenieść, wybierz Wielokropek na końcu wiersza dla subskrypcji.

   ![Opcja Przenieś grupy zarządzania](./media/move_small.png)

1. Wybierz **przenieść**.

1. W wyświetlonym menu wybierz **nadrzędna grupa zarządzania**.

   ![Przesuń okienko, aby zmienić grupy nadrzędnej](./media/move_small_context.png)

1. Wybierz pozycję **Zapisz**.

### <a name="move-subscriptions-in-powershell"></a>Przenoszenie subskrypcji w programie PowerShell

Aby przenieść subskrypcję, w programie PowerShell, należy użyć polecenia New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Aby usunąć połączenie między i użyj polecenia Remove-AzManagementGroupSubscription, subskrypcję i grupę zarządzania.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Przenieś subskrypcje w interfejsie wiersza polecenia platformy Azure

Aby przenieść subskrypcję, w interfejsie wiersza polecenia, należy użyć polecenia Dodaj.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Aby usunąć subskrypcję z grupy zarządzania, użyj polecenia remove subskrypcji.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Przeniesienie grup zarządzania w hierarchii  

Podczas przenoszenia nadrzędna grupa zarządzania hierarchię w ramach tej grupy przenosi się z nim. Dostępu należy przenieść grupy zarządzania, zobacz [dostęp do grupy zarządzania](index.md#management-group-access).

### <a name="move-management-groups-in-the-portal"></a>Przeniesienie grup zarządzania w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkich usług** > **grup zarządzania**.

1. Wybierz grupę zarządzania, którą zamierzasz jako nadrzędną.

1. W górnej części strony wybierz **Dodaj grupę zarządzania**.

1. W wyświetlonym menu wybierz nową lub użyj istniejącej grupy zarządzania.

   - Wybieranie nowego utworzy nową grupę zarządzania.
   - Wybieranie istniejącego spowoduje wyświetlenie menu rozwijane wszystkich grup zarządzania, który można przenieść do tej grupy zarządzania.  

   ![Przenoszenie grupy zarządzania do nowej lub istniejącej grupy](./media/add_context_MG.png)

1. Wybierz pozycję **Zapisz**.

### <a name="move-management-groups-in-powershell"></a>Przeniesienie grup zarządzania w programie PowerShell

Użyj polecenia AzManagementGroup aktualizacji w programie PowerShell, aby przenieść grupy zarządzania do innej grupy.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Przeniesienie grup zarządzania w interfejsie wiersza polecenia platformy Azure

Użyj polecenia aktualizacji, aby przenieść grupy do zarządzania przy użyciu wiersza polecenia platformy Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md). Można tworzyć zapytania wszystkie zdarzenia, które odbywa się w grupie zarządzania w tej samej lokalizacji centralnej, innych zasobów platformy Azure.  Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

![Dzienniki aktywności przy użyciu grup zarządzania](media/al-mg.png)

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Odwoływanie się do grup zarządzania, od innych dostawców zasobów

Podczas odwoływania się do grup zarządzania, z akcji inny dostawca zasobów, należy użyć następującej ścieżki, jako zakres. Ta ścieżka jest używana w przypadku korzystania z programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsów API REST.  

>"/ providers/Microsoft.Management/managementGroups/{yourMgID}"

Przykładem użycia tej ścieżki jest podczas przypisywania nowe przypisanie roli do grupy zarządzania w programie PowerShell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Tej samej ścieżce zakres jest używany podczas pobierania definicji zasad w grupie zarządzania.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)