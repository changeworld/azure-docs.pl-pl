---
title: Jak używać kontroli dostępu opartej na rolach w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć wbudowanych ról i utworzyć niestandardowe role w usłudze Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 2e53b0d582a69e10de22e85720833800d44058e3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141483"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Jak używać kontroli dostępu opartej na rolach w usłudze Azure API Management

Usługa Azure API Management bazuje na kontroli dostępu based (RBAC) umożliwia precyzyjne zarządzanie dostępem dla usługi API Management i jednostek (na przykład interfejsów API i zasady). Ten artykuł zawiera przegląd ról wbudowanych i niestandardowych w usłudze API Management. Aby uzyskać więcej informacji na temat zarządzania dostępem w witrynie Azure portal, zobacz [wprowadzenie do zarządzania dostępem w witrynie Azure portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Wbudowane role

Usługa API Management obecnie udostępnia trzy role wbudowane i doda dwóch większej liczby ról w niedalekiej przyszłości. Te role można przypisać w różnych zakresach, łącznie z subskrypcji, grupy zasobów i poszczególne wystąpienia usługi API Management. Na przykład w przypadku przypisania roli "Czytelnik usługi zarządzania interfejsu API platformy Azure" do użytkownika na poziomie grupy zasobów, użytkownik ma dostęp do odczytu do wszystkich wystąpień usługi API Management w grupie zasobów. 

Poniższa tabela zawiera krótkie opisy ról wbudowanych. Te role można przypisać za pomocą witryny Azure portal lub innych narzędzi, takich jak Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), i [interfejsu API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Aby uzyskać szczegółowe informacje dotyczące sposobu przypisywania wbudowanych ról, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Rola          | Dostęp do odczytu<sup>[1]</sup> | Dostęp do zapisu<sup>[2]</sup> | Tworzenie usługi, usuwanie, skalowanie, sieci VPN i konfiguracja domen niestandardowych | Dostęp do portalu wydawcy starszej wersji | Opis
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Współautor usługi Azure API Management | ✓ | ✓ | ✓ | ✓ | Administrator. Ma pełny dostęp CRUD do usługi API Management i jednostek (na przykład interfejsów API i zasady). Ma dostęp do portalu wydawcy starszej wersji. |
| Usługi Azure API Management usługi czytnika | ✓ | | || Ma dostęp tylko do odczytu do usługi API Management i jednostek. |
| Operator usługi zarządzania interfejs API platformy Azure | ✓ | | ✓ | | Może zarządzać usługi API Management, ale nie jednostki.|
| Edytor usługi zarządzania w usłudze Azure API<sup>*</sup> | ✓ | ✓ | |  | Można zarządzać jednostkami usługi API Management, ale nie usług.|
| Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Można zarządzać w portalu dla deweloperów. Dostęp tylko do odczytu do usługi i jednostek.|

<sup>[1] dostęp do odczytu do usługi API Management i jednostek (na przykład interfejsów API i zasady).</sup>

<sup>[2] do zapisu usługi API Management i jednostkami, z wyjątkiem następujących operacji: wystąpienia tworzenia, usuwania i skalowania; Konfiguracja sieci VPN; i konfiguracja domen niestandardowych.</sup>

<sup>\* Edytor usług roli będą dostępne po migracji wszystkich administratora interfejsu użytkownika z istniejącego portalu wydawców do witryny Azure portal. Rola menedżera zawartości będą dostępne po został zrefaktoryzowany portalu wydawcy, aby zawierała tylko funkcje związane z zarządzaniem portalu dla deweloperów.</sup>  

## <a name="custom-roles"></a>Role niestandardowe

Jeśli żadna z wbudowanych ról określonych potrzeb, aby zapewnić bardziej szczegółowe zarządzanie dostępem do jednostki usługi API Management można tworzyć role niestandardowe. Na przykład można utworzyć rolę niestandardową, która ma dostęp tylko do odczytu do usługi API Management, ale ma tylko uprawnienia do zapisu w jednym określonego interfejsu API. Aby dowiedzieć się więcej o niestandardowych rolach, zobacz [niestandardowych ról RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Aby można było wyświetlić wystąpienia usługi API Management w witrynie Azure portal, musi zawierać rolę niestandardową ```Microsoft.ApiManagement/service/read``` akcji.

Możesz utworzyć rolę niestandardową, łatwiej zacząć od jednego z wbudowanych ról. Edycja atrybutów do dodania **akcje**, **NotActions**, lub **AssignableScopes**, a następnie zapisz zmiany jako nową rolę. W poniższym przykładzie rozpoczyna się od roli "Czytelnik usługi zarządzania interfejsu API platformy Azure" i tworzy niestandardową rolę o nazwie "Edytor interfejsu API kalkulatora." Rolę niestandardową można przypisać do konkretnego interfejsu API. W związku z tym ta rola ma dostęp do tego interfejsu API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

[Operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) artykuł zawiera listę uprawnień, które mogą być przyznawane na poziomie interfejsu API zarządzania.

## <a name="video"></a>Połączenia wideo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat opartej na rolach kontrola dostępu na platformie Azure, zobacz następujące artykuły:
  * [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
  * [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)
  * [Niestandardowych ról RBAC platformy Azure](../role-based-access-control/custom-roles.md)
  * [Operacje dostawcy zasobów w usłudze Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)