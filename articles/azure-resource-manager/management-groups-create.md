---
title: Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć grupy zarządzania platformy Azure do zarządzania wieloma zasobami.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: rithorn
ms.openlocfilehash: b35803de2cb503418d4373fe3429b81ec5474de4
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358695"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Tworzenie grupy zarządzania do organizacji zasobów i zarządzania

Grupy zarządzania są kontenery, które ułatwiają zarządzanie dostępem, zasady i zgodność w wielu subskrypcjach. Tworzenie tych kontenerów, aby tworzyć hierarchie skuteczny i wydajny, które mogą być używane z [usługi Azure Policy](../azure-policy/azure-policy-introduction.md) i [kontroli dostępu na podstawie roli Azure](../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure ](management-groups-overview.md).

Pierwsza grupa zarządzania tworzony w katalogu może potrwać do 15 minut. Brak procesów, które są uruchamiane po raz pierwszy do skonfigurowania usługi grupy zarządzania w obrębie platformy Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie.  

## <a name="create-a-management-group"></a>Utwórz grupę zarządzania

Za pomocą witryny portal, programu PowerShell lub wiersza polecenia platformy Azure, można utworzyć grupy zarządzania.

### <a name="create-in-portal"></a>Tworzenie w portalu

1. Zaloguj się do witryny [Azure Portal](http://portal.azure.com).
2. Wybierz **wszystkich usług** > **grup zarządzania**.
3. Na stronie głównej wybierz **nowym rozwiązaniem do zarządzania grupy.**

    ![Główna grupa](media/management-groups/main.png)
4.  Wypełnij pola Identyfikator grupy zarządzania.
    - **Identyfikator grupy zarządzania** jest unikatowy identyfikator katalogu, który służy do przesyłania poleceń w tej grupie zarządzania. Ten identyfikator nie jest edytowalny po utworzeniu, ponieważ jest używany do identyfikowania tej grupy w systemie Azure.
    - Nazwa wyświetlana tego pola jest nazwa, która jest wyświetlana w witrynie Azure portal. Nazwę wyświetlaną osobne pole jest opcjonalne, tworząc zarządzania grupy i można ją zmienić w dowolnym momencie.  

    ![Przycisk Utwórz](media/management-groups/create_context_menu.png)  
5.  Wybierz **Zapisz**

### <a name="create-in-powershell"></a>Tworzenie w programie PowerShell

W programie PowerShell możesz użyć polecenia cmdlet Add-AzureRmManagementGroups:

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso
```

**GroupName** jest unikatowym identyfikatorem tworzona. Ten identyfikator jest używany przez inne polecenia, aby odwoływać się do tej grupy i nie można zmienić później.

Jeśli chce się grupy zarządzania, aby pokazać inną nazwę w witrynie Azure portal, należy dodać **DisplayName** parametr na ciąg. Na przykład jeśli chcesz utworzyć grupę zarządzania z GroupName Contoso i nazwę wyświetlaną grupy"Contoso", użyj następującego polecenia cmdlet:

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
```

Użyj **ParentId** parametru tej grupy zarządzania można utworzyć w ramach różnych zarządzania.  

### <a name="create-in-azure-cli"></a>Tworzenie w wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure, użyj az polecenia create grupy zarządzania kontem.

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
```

---

## <a name="next-steps"></a>Kolejne kroki 
Aby dowiedzieć się więcej na temat grup zarządzania, zobacz: 
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure ](management-groups-overview.md)
- [Jak zmienić, usunąć lub zarządzać grupami zarządzania](management-groups-manage.md)
- [Instalowanie modułu Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Przegląd specyfikacji interfejsu API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Zainstaluj rozszerzenie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
