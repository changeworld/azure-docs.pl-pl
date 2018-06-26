---
title: Jak zmienić, usunąć lub zarządzać grupami zarządzania - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak obsługa oraz aktualizowanie hierarchii grupy zarządzania.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: rithorn
ms.openlocfilehash: 0a13627232904f4b14cdb5cbf5c3ca927d9ea167
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754671"
---
# <a name="manage-your-resources-with-management-groups"></a>Zarządzanie zasobami z grupy zarządzania 
Grupy zarządzania to kontenery ułatwiające zarządzanie dostępu, zasad i zgodności między wieloma subskrypcjami. Można zmienić, usuwanie i zarządzanie kontenery do hierarchii, które mogą być używane z [zasadami Azure](../azure-policy/azure-policy-introduction.md) i [Azure na podstawie ról dostęp do formantów (RBAC)](../role-based-access-control/overview.md). Aby dowiedzieć się więcej na temat grup zarządzania, zobacz [organizowania zasobów z grupami zarządzania platformy Azure ](management-groups-overview.md).

Funkcja grupy zarządzania jest dostępna w publicznej wersji zapoznawczej. Aby rozpocząć korzystanie z grup zarządzania, zaloguj się w [portalu Azure](https://portal.azure.com) lub użyć [programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), lub [interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) do Zarządzanie grupami zarządzania.

Aby wprowadzić zmiany w grupie zarządzania, musi mieć rolę właściciela lub współautora w grupie zarządzania. Aby zobaczyć, jakie uprawnienia ma, wybierz grupę zarządzania a następnie wybierz **IAM**. Aby dowiedzieć się więcej o rolach RBAC, zobacz [zarządzać dostępem i uprawnieniami RBAC](../role-based-access-control/overview.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Zmień nazwę grupy zarządzania 
Za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia Azure, można zmienić nazwę grupy zarządzania.

### <a name="change-the-name-in-the-portal"></a>Zmień nazwę w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com)
2. Wybierz **wszystkie usługi** > **grup zarządzania**  
3. Wybierz grupę zarządzania, którą chcesz zmienić. 
4. Wybierz **Zmień nazwę grupy** opcji w górnej części strony. 

    ![Zmień nazwę grupy](media/management-groups/detail_action_small.png)
5. Po otwarciu menu, wprowadź nową nazwę, którą chcesz są wyświetlane.

    ![Zmień nazwę grupy](media/management-groups/rename_context.png) 
4. Wybierz pozycję **Zapisz**. 

### <a name="change-the-name-in-powershell"></a>Zmień nazwę w programie PowerShell

Aby zaktualizować używana nazwa wyświetlana **AzureRmManagementGroup aktualizacji**. Na przykład aby zmienić nazwę grupy zarządzania z "IT firmy Contoso" do "Grupa firmy Contoso", uruchom następujące polecenie: 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Zmień nazwę w wiersza polecenia platformy Azure

Dla wiersza polecenia platformy Azure należy użyć polecenia update. 

```azurecli-interactive
az account management-group update --name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>Usuwanie grupy zarządzania
Aby usunąć grupę zarządzania, muszą być spełnione następujące wymagania:
1. Nie ma żadnych grup zarządzania podrzędnych ani subskrypcje w grupie zarządzania. 
    - Aby przenieść subskrypcję, poza grupą zarządzania, zobacz [przenieść subskrypcję do innej grupy managemnt](#Move-subscriptions-in-the-hierarchy). 
    - Aby przenieść grupy zarządzania do innej grupy zarządzania, zobacz [przeniesienie grup zarządzania w hierarchii](#Move-management-groups-in-the-hierarchy). 
2. Masz uprawnienia do zapisu w grupie właścicielem lub współautorem roli zarządzania w grupie zarządzania. Aby zobaczyć, jakie uprawnienia ma, wybierz grupę zarządzania a następnie wybierz **IAM**. Aby uzyskać więcej informacji na temat role RBAC, zobacz [zarządzać dostępem i uprawnieniami RBAC](../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Usuwanie w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com)
2. Wybierz **wszystkie usługi** > **grup zarządzania**  
3. Wybierz grupy zarządzania, który chcesz usunąć. 
    
    ![Usuwanie grupy](media/management-groups/delete.png)
4. Wybierz pozycję **Usuń**. 
    - Po wyłączeniu ikony aktywowania Twojego selektora myszy na ikonie przedstawiono przyczyny. 
5. Istnieje okno, którego kliknięcie spowoduje otwarcie potwierdzenie, że chcesz usunąć grupę zarządzania. 

    ![Usuwanie grupy](media/management-groups/delete_confirm.png) 
6. Wybierz **tak** 


### <a name="delete-in-powershell"></a>Usuwanie w programie PowerShell

Użyj **AzureRmManagementGroup Usuń** polecenia w programie PowerShell, aby usunąć grupy zarządzania. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Usuwanie w interfejsie wiersza polecenia platformy Azure
Za pomocą interfejsu wiersza polecenia Azure, Usuń polecenie az konta grupy zarządzania. 

```azurecli-interactive
az account management-group delete --name Contoso
```
---

## <a name="view-management-groups"></a>Wyświetlanie grup zarządzania
Można wyświetlić grup zarządzania, do których masz bezpośrednie lub dziedziczonej roli RBAC na.  

### <a name="view-in-the-portal"></a>Wyświetl w portalu
1. Zaloguj się do [portalu Azure](https://portal.azure.com)
2. Wybierz **wszystkie usługi** > **grup zarządzania** 
3. Hierarchia grupy zarządzania strony obciążenia, w którym można sprawdzić wszystkie grupy zarządzania i subskrypcje, do których masz dostęp do. Wybieranie nazwy grupy przejście poziom w dół hierarchii. Nawigacja działa tak samo jak w Eksploratorze plików. 
    ![Main](media/management-groups/main.png)
4. Aby wyświetlić szczegóły grupy zarządzania, wybierz **(szczegóły)** link obok nazwy grupy zarządzania. Jeśli to łącze nie jest dostępny, nie masz uprawnień do wyświetlania tej grupy zarządzania.  

### <a name="view-in-powershell"></a>Wyświetl w programie PowerShell
Polecenie Get-AzureRmManagementGroup można pobrać wszystkich grup.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Grupę zarządzania pojedynczego informacji Użyj parametru - GroupName

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Widok w interfejsu wiersza polecenia platformy Azure
Polecenie listy można pobrać wszystkich grup.  

```azurecli-interactive
az account management-group list
```
Grupę zarządzania pojedynczego informacji użyj polecenia Pokaż

```azurecli-interactive
az account management-group show --name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Przenoszenie subskrypcji w hierarchii
Jednym z powodów tworzenia grupy zarządzania jest udostępnianie pakietów subskrypcji ze sobą. Tylko grupy zarządzania i subskrypcje mogą być dokonywane dzieci innej grupy zarządzania. Subskrypcja, który przenosi do grupy zarządzania dziedziczy wszystkie użytkownika zasad dostępu i z nadrzędnej grupy zarządzania. 

Aby przenieść subskrypcję, istnieje kilka uprawnienia, które muszą mieć: 
- Rola "Właściciela" w subskrypcji podrzędnych.
- Rola "Właściciela" lub "Współautora" w grupie zarządzania nadrzędnej. 
- Rola "Właściciela" lub "Współautora" na starym nadrzędnej grupy zarządzania.
Aby zobaczyć, jakie uprawnienia ma, wybierz grupę zarządzania a następnie wybierz **IAM**. Aby uzyskać więcej informacji na temat role RBAC, zobacz [zarządzać dostępem i uprawnieniami RBAC](../role-based-access-control/overview.md). 

### <a name="move-subscriptions-in-the-portal"></a>Przenoszenie subskrypcji w portalu

**Dodaj istniejącą subskrypcję do grupy zarządzania**
1. Zaloguj się do [portalu Azure](https://portal.azure.com)
2. Wybierz **wszystkie usługi** > **grup zarządzania** 
3. Wybierz grupę zarządzania, który planujesz jako nadrzędną.      
5. W górnej części strony, wybierz **Dodaj istniejącą**. 
6. Wybierz z menu otwarty, **typu zasobu** elementu próbujesz przenieść, która jest **subskrypcji**.
7. Wybierz subskrypcję z listy poprawny identyfikator. 

    ![Elementy podrzędne](media/management-groups/add_context_2.png)
8. Wybierz opcję "Zapisz"

**Usuń subskrypcję z grupą zarządzania**
1. Zaloguj się do [portalu Azure](https://portal.azure.com)
2. Wybierz **wszystkie usługi** > **grup zarządzania** 
3. Wybierz grupę zarządzania planujesz czyli bieżącego elementu nadrzędnego.  
4. Wybierz elipsy na końcu wiersza subskrypcji na liście, który chcesz przenieść.

    ![Move](media/management-groups/move_small.png)
5. Wybierz **Przenieś**
6. W wyświetlonym menu wybierz **nadrzędna grupa zarządzania**.

    ![Move](media/management-groups/move_small_context.png)
7. Wybierz **Zapisz**

### <a name="move-subscriptions-in-powershell"></a>Przenoszenie subskrypcji w programie PowerShell
Aby przenieść subskrypcję w programie PowerShell, należy użyć polecenia AzureRmManagementGroupSubscription Dodaj.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

Aby usunąć połączenie między i subskrypcji i grupy zarządzania za pomocą polecenia Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Przenieś subskrypcje w wiersza polecenia platformy Azure
Aby przenieść subskrypcję w interfejsu wiersza polecenia, należy użyć polecenia Dodaj. 

```azurecli-interactive
az account management-group subscription add --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

Aby usunąć subskrypcji z grupą zarządzania, użyj polecenia remove subskrypcji.  

```azurecli-interactive
az account management-group subscription remove --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Przeniesienie grup zarządzania w hierarchii  
Po przeniesieniu nadrzędnej grupy zarządzania, wszystkie zasoby podrzędne obejmujących grup zarządzania, subskrypcji, grupy zasobów i przeniesienie zasobów z obiektu nadrzędnego.   

### <a name="move-management-groups-in-the-portal"></a>Przeniesienie grup zarządzania w portalu

1. Zaloguj się do [portalu Azure](https://portal.azure.com)
2. Wybierz **wszystkie usługi** > **grup zarządzania** 
3. Wybierz grupę zarządzania, który planujesz jako nadrzędną.      
5. W górnej części strony, wybierz **Dodaj istniejącą**.
6. Wybierz z menu otwarty, **typu zasobu** elementu próbujesz przenieść, która jest **grupy zarządzania**.
7. Wybierz grupę zarządzania z poprawny identyfikator i nazwę.

    ![Przenieś](media/management-groups/add_context.png)
8. Wybierz **Zapisz**

### <a name="move-management-groups-in-powershell"></a>Przeniesienie grup zarządzania w programie PowerShell
Użyj polecenia AzureRmManagementGroup aktualizacji w programie PowerShell, można przenieść grupy zarządzania w ramach innej grupy.  

```powershell
Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Przeniesienie grup zarządzania w wiersza polecenia platformy Azure
Polecenie aktualizacji można przenieść grupy zarządzania z wiersza polecenia platformy Azure. 

```azurecli-interactive
az account management-group update --name Contoso --parent "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>Kolejne kroki 
Aby dowiedzieć się więcej na temat grup zarządzania, zobacz: 
- [Organizowanie zasobów z grupami zarządzania platformy Azure ](management-groups-overview.md)
- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](management-groups-create.md)
- [Zainstaluj moduł Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Przejrzyj specyfikacji interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
