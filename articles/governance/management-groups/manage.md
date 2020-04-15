---
title: Jak pracować z grupami zarządzania — Azure Governance
description: Dowiedz się, jak wyświetlać, konserwować, aktualizować i usuwać hierarchię grup zarządzania.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 423d1837c3d5710e24abb94f5411200319e8a8aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381682"
---
# <a name="manage-your-resources-with-management-groups"></a>Zarządzanie zasobami za pomocą grup zarządzania

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji. Aby dowiedzieć się więcej o grupach zarządzania, zobacz [Organizowanie zasobów za pomocą grup zarządzania platformy Azure](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Tokeny użytkowników usługi Azure Resource Manager i pamięć podręczna grup zarządzania trwa 30 minut, zanim zostaną zmuszone do odświeżenia. Po wykonaniu jakiejkolwiek czynności, takiej jak przeniesienie grupy zarządzania lub subskrypcji, może upłynąć do 30 minut. Aby wyświetlić aktualizacje wcześniej, musisz zaktualizować token, odświeżając przeglądarkę, logując się i wyloguj lub żądając nowego tokenu.  

## <a name="change-the-name-of-a-management-group"></a>Zmienianie nazwy grupy zarządzania

Nazwę grupy zarządzania można zmienić za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="change-the-name-in-the-portal"></a>Zmienianie nazwy w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję Wszystkie > **grupy zarządzania** **usługami**.

1. Wybierz grupę zarządzania, której nazwę chcesz zmienić.

1. Wybierz **szczegóły**.

1. Wybierz opcję **Zmień nazwę grupy** u góry strony.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Zmień nazwę opcji Grupa na stronie grupy zarządzania" border="false":::

1. Po otwarciu menu wprowadź nową nazwę, którą chcesz wyświetlić.

   :::image type="content" source="./media/rename_context.png" alt-text="Zmień nazwę okienka Grupa, aby zmienić nazwę grupy zarządzania" border="false":::

1. Wybierz pozycję **Zapisz**.

### <a name="change-the-name-in-powershell"></a>Zmienianie nazwy w programie PowerShell

Aby zaktualizować nazwę wyświetlaną, należy użyć **update-AzManagementGroup**. Na przykład, aby zmienić nazwę wyświetlaną grup zarządzania z "Contoso IT" na "Grupa Contoso", należy uruchomić następujące polecenie:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Zmienianie nazwy w usłudze Azure CLI

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia aktualizacji.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Usuwanie grupy zarządzania

Aby usunąć grupę zarządzania, muszą być spełnione następujące wymagania:

1. W ramach grupy zarządzania nie ma grup zarządzania podrzędnymi ani subskrypcji.

   - Aby przenieść subskrypcję lub grupę zarządzania do innej grupy zarządzania, zobacz [Przenoszenie grup zarządzania i subskrypcji w hierarchii](#moving-management-groups-and-subscriptions).

1. Musisz uprawnienia do zapisu w grupie zarządzania ("Właściciel", "Współautor" lub "Współautor grupy zarządzania"). Aby zobaczyć, jakie masz uprawnienia, wybierz grupę zarządzania, a następnie wybierz pozycję **IAM**. Aby dowiedzieć się więcej o rolach RBAC, zobacz  
   [Zarządzanie dostępem i uprawnieniami za pomocą pliku RBAC](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Usuwanie w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję Wszystkie > **grupy zarządzania** **usługami**.

1. Wybierz grupę zarządzania, którą chcesz usunąć.

1. Wybierz **szczegóły**.

1. Wybierz **pozycję Usuń**

   :::image type="content" source="./media/delete.png" alt-text="Opcja Usuń grupę" border="false":::

   > [!TIP]
   > Jeśli ikona jest wyłączona, najechanie kursorem myszy na ikonę pokazuje przyczynę.

1. Jest okno, które otwiera się z potwierdzeniem, że chcesz usunąć grupę zarządzania.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Okno potwierdzenia usuwania grupy" border="false":::

1. Wybierz **pozycję Tak**.

### <a name="delete-in-powershell"></a>Usuwanie w programie PowerShell

Polecenie **Usuń-AzManagementGroup** w programie PowerShell służy do usuwania grup zarządzania.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Usuwanie w interfejsie wiersza polecenia platformy Azure

W przypadku interfejsu wiersza polecenia platformy Azure należy użyć polecenia usuniętego grupy zarządzania kontem az.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Wyświetlanie grup zarządzania

Można wyświetlić dowolną grupę zarządzania, na której masz bezpośrednią lub dziedziczoną rolę RBAC.  

### <a name="view-in-the-portal"></a>Zobacz w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję Wszystkie > **grupy zarządzania** **usługami**.

1. Zostanie załadowana strona hierarchii grupy zarządzania. Na tej stronie można eksplorować wszystkie grupy zarządzania i subskrypcje, do których masz dostęp. Wybranie nazwy grupy powoduje zmniejszenie poziomu w hierarchii. Nawigacja działa tak samo jak eksplorator plików.

1. Aby wyświetlić szczegóły grupy zarządzania, wybierz łącze **(szczegóły)** obok tytułu grupy zarządzania. Jeśli to łącze nie jest dostępne, nie masz uprawnień do wyświetlania tej grupy zarządzania.

   :::image type="content" source="./media/main.png" alt-text="Główne" border="false":::

### <a name="view-in-powershell"></a>Wyświetlanie w programie PowerShell

Polecenie Get-AzManagementGroup służy do pobierania wszystkich grup. Pełną listę poleceń grupy zarządzania GET PowerShell można znaleźć w modułach [Az.Resources.](/powershell/module/az.resources/Get-AzManagementGroup)  

```azurepowershell-interactive
Get-AzManagementGroup
```

Aby uzyskać informacje o pojedynczej grupie zarządzania, użyj parametru -GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Aby zwrócić określoną grupę zarządzania i wszystkie poziomy hierarchii pod nią, należy użyć **parametrów -Expand** i **-Recurse.**  

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

### <a name="view-in-azure-cli"></a>Wyświetlanie w usłudze Azure CLI

Polecenie listy służy do pobierania wszystkich grup.  

```azurecli-interactive
az account management-group list
```

Aby uzyskać informacje o pojedynczej grupie zarządzania, użyj polecenia show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Aby zwrócić określoną grupę zarządzania i wszystkie poziomy hierarchii pod nią, należy użyć **parametrów -Expand** i **-Recurse.**

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Przenoszenie grup zarządzania i subskrypcji   

Jednym z powodów, aby utworzyć grupę zarządzania jest powiązanie subskrypcji razem. Tylko grupy zarządzania i subskrypcje mogą być podrzędne innej grupy zarządzania. Subskrypcja, która przenosi się do grupy zarządzania dziedziczy cały dostęp użytkowników i zasady z nadrzędnej grupy zarządzania

Podczas przenoszenia grupy zarządzania lub subskrypcji, aby być dziewką innej grupy zarządzania trzy reguły muszą być oceniane jako true.

Jeśli wykonujesz akcję przenoszenia, musisz: 

- Uprawnienia do zapisu grupy zarządzania i przypisania roli w podrzędnej subskrypcji lub grupie zarządzania.
  - Wbudowany przykład roli **Właściciel**
- Dostęp do zapisu grupy zarządzania w docelowej nadrzędnej grupie zarządzania.
  - Przykład roli wbudowanej: **właściciel**, **współautor**, **współautor grupy zarządzania**
- Dostęp do zapisu grupy zarządzania w istniejącej nadrzędnej grupie zarządzania.
  - Przykład roli wbudowanej: **właściciel**, **współautor**, **współautor grupy zarządzania**

**Wyjątek:** Jeśli obiekt docelowy lub istniejąca nadrzędna grupa zarządzania jest główną grupą zarządzania, wymagania dotyczące uprawnień nie mają zastosowania. Ponieważ główna grupa zarządzania jest domyślnym miejscem docelowym dla wszystkich nowych grup zarządzania i subskrypcji, nie potrzebujesz uprawnień do przenoszenia elementu.

Jeśli rola Właściciela w subskrypcji jest dziedziczona z bieżącej grupy zarządzania, cele przenoszenia są ograniczone. Subskrypcję można przenieść tylko do innej grupy zarządzania, w której masz rolę Właściciel. Nie można przenieść go do grupy zarządzania, w której jesteś współautorem, ponieważ utracisz własność subskrypcji. Jeśli jesteś bezpośrednio przypisany do roli Właściciel dla subskrypcji (nie dziedziczone z grupy zarządzania), możesz przenieść go do dowolnej grupy zarządzania, w której jesteś współautorem.

Aby zobaczyć, jakie masz uprawnienia w witrynie Azure Portal, wybierz grupę zarządzania, a następnie wybierz pozycję **IAM**. Aby dowiedzieć się więcej o rolach RBAC, zobacz [Zarządzanie dostępem i uprawnieniami za pomocą funkcji RBAC](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Przenoszenie subskrypcji 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Dodawanie istniejącej subskrypcji do grupy zarządzania w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję Wszystkie > **grupy zarządzania** **usługami**.

1. Wybierz grupę zarządzania, którą zamierzasz być elementem nadrzędnym.

1. U góry strony wybierz pozycję **Dodaj subskrypcję**.

1. Wybierz subskrypcję na liście z poprawnym identyfikatorem.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Dostępne subskrypcje do dodania do grupy zarządzania" border="false":::

1. Wybierz "Zapisz".

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Usuwanie subskrypcji z grupy zarządzania w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję Wszystkie > **grupy zarządzania** **usługami**.

1. Wybierz grupę zarządzania, którą planujesz, która jest bieżącym elementem nadrzędnym.  

1. Wybierz elipsę na końcu wiersza dla subskrypcji na liście, którą chcesz przenieść.

   :::image type="content" source="./media/move_small.png" alt-text="Opcja Przenoszenie w grupie zarządzania" border="false":::

1. Wybierz **pozycję Przenieś**.

1. W menu, które zostanie otwarte, wybierz **grupę zarządzania nadrzędnego**.

   :::image type="content" source="./media/move_small_context.png" alt-text="Przenoszenie okienka w celu zmiany grupy nadrzędnej" border="false":::

1. Wybierz pozycję **Zapisz**.

### <a name="move-subscriptions-in-powershell"></a>Przenoszenie subskrypcji w programie PowerShell

Aby przenieść subskrypcję w programie PowerShell, należy użyć polecenia New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Aby usunąć łącze między i subskrypcji i grupy zarządzania należy użyć Polecenia Usuń-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Przenoszenie subskrypcji w usłudze Azure CLI

Aby przenieść subskrypcję w wierszu polecenia, należy użyć polecenia dodaj.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Aby usunąć subskrypcję z grupy zarządzania, użyj polecenia usuń subskrypcję.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Przenoszenie grup zarządzania 

### <a name="move-management-groups-in-the-portal"></a>Przenoszenie grup zarządzania w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję Wszystkie > **grupy zarządzania** **usługami**.

1. Wybierz grupę zarządzania, którą zamierzasz być elementem nadrzędnym.

1. U góry strony wybierz pozycję **Dodaj grupę zarządzania**.

1. W menu, które zostanie otwarte, wybierz, jeśli chcesz nowy lub użyć istniejącej grupy zarządzania.

   - Wybranie nowego spowoduje utworzenie nowej grupy zarządzania.
   - Wybranie istniejącej spowoduje, że pojawi się rozwijana część wszystkich grup zarządzania, które można przenieść do tej grupy zarządzania.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Przenoszenie grupy zarządzania do nowej lub istniejącej grupy" border="false":::

1. Wybierz pozycję **Zapisz**.

### <a name="move-management-groups-in-powershell"></a>Przenoszenie grup zarządzania w programie PowerShell

Użyj polecenia Update-AzManagementGroup w programie PowerShell, aby przenieść grupę zarządzania w innej grupie.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Przenoszenie grup zarządzania w interfejsie wiersza polecenia platformy Azure

Użyj polecenia aktualizacji, aby przenieść grupę zarządzania za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md). Można zbadać wszystkie zdarzenia, które zdarzają się w grupie zarządzania w tej samej centralnej lokalizacji, jak inne zasoby platformy Azure. Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

:::image type="content" source="./media/al-mg.png" alt-text="Dzienniki aktywności z grupami zarządzania" border="false":::

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Odwoływanie się do grup zarządzania od innych dostawców zasobów

Podczas odwoływania się do grup zarządzania z działań innego dostawcy zasobów, należy użyć następującej ścieżki jako zakresu. Ta ścieżka jest używana podczas korzystania z interfejsów API programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsów API REST.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Przykładem użycia tej ścieżki jest przypisywanie nowego przypisania roli do grupy zarządzania w programie PowerShell:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Ta sama ścieżka zakresu jest używana podczas pobierania definicji zasad w grupie zarządzania.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](./create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](./manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)
