---
title: Jak korzystać z kontroli dostępu opartej na rolach w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z wbudowanych ról i tworzyć role niestandardowe w usłudze Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: ed0cd51fc686735f2d9c110ce46d5904107cafc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430611"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Jak używać kontroli dostępu opartej na rolach w usłudze Azure API Management

Usługa Azure API Management opiera się na kontroli dostępu opartej na rolach platformy Azure (RBAC), aby włączyć szczegółowe zarządzanie dostępem dla usług i jednostek zarządzania interfejsami API (na przykład interfejsów API i zasad). W tym artykule przedstawiono omówienie wbudowanych i niestandardowych ról w usłudze API Management. Aby uzyskać więcej informacji na temat zarządzania dostępem w witrynie Azure portal, zobacz [Wprowadzenie do zarządzania dostępem w witrynie Azure portal.](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Wbudowane role

Zarządzanie interfejsem API obecnie udostępnia trzy wbudowane role i doda dwie kolejne role w najbliższej przyszłości. Te role można przypisać w różnych zakresach, w tym subskrypcji, grupy zasobów i poszczególnych wystąpień usługi API Management. Na przykład jeśli przypisano rolę "Czytnik usług zarządzania interfejsami API" do użytkownika na poziomie grupy zasobów, użytkownik ma dostęp do odczytu do wszystkich wystąpień zarządzania interfejsami API wewnątrz grupy zasobów. 

Poniższa tabela zawiera krótkie opisy wbudowanych ról. Te role można przypisać za pomocą witryny Azure portal lub innych narzędzi, w tym usługi Azure [PowerShell,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)i interfejsu API [REST.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) Aby uzyskać szczegółowe informacje na temat przypisywania wbudowanych ról, zobacz [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

| Rola          | Dostęp do odczytu<sup>[1]</sup> | Dostęp do zapisu<sup>[2]</sup> | Tworzenie, usuwanie, skalowanie, konfiguracja sieci VPN i domeny niestandardowej | Dostęp do starszego portalu wydawcy | Opis
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Współautor usługi zarządzania interfejsami API | ✓ | ✓ | ✓ | ✓ | Super użytkownik. Ma pełny dostęp CRUD do usług i jednostek api Management (na przykład interfejsów API i zasad). Ma dostęp do starszego portalu wydawcy. |
| Czytnik usług zarządzania interfejsami API | ✓ | | || Ma dostęp tylko do odczytu do usług i jednostek zarządzania interfejsami API. |
| Operator usługi zarządzania interfejsami API | ✓ | | ✓ | | Może zarządzać usługami zarządzania interfejsami API, ale nie encjami.|
| Edytor usług zarządzania interfejsami API<sup>*</sup> | ✓ | ✓ | |  | Może zarządzać jednostkami zarządzania interfejsami API, ale nie usługami.|
| Menedżer zawartości do zarządzania interfejsami API<sup>*</sup> | ✓ | | | ✓ | Może zarządzać portalem dewelopera. Dostęp tylko do odczytu do usług i jednostek.|

<sup>[1] Dostęp do odczytu usług i jednostek zarządzania interfejsami API (na przykład interfejsy API i zasady).</sup>

<sup>[2] Dostęp do zapisu do usług i jednostek zarządzania interfejsami API z wyjątkiem następujących operacji: tworzenie wystąpień, usuwanie i skalowanie; konfiguracja sieci VPN; i niestandardowej konfiguracji domeny.</sup>

<sup>\*Rola Edytor usług będzie dostępna po migracji wszystkich interfejsów użytkownika administratora z istniejącego portalu wydawcy do witryny Azure portal. Rola Menedżera zawartości będzie dostępna po refaktoryzacji portalu wydawcy, aby zawierała tylko funkcje związane z zarządzaniem portalem dewelopera.</sup>  

## <a name="custom-roles"></a>Role niestandardowe

Jeśli żadna z wbudowanych ról nie spełnia określonych potrzeb, można utworzyć role niestandardowe, aby zapewnić bardziej szczegółowe zarządzanie dostępem dla jednostek usługi API Management. Na przykład można utworzyć rolę niestandardową, która ma dostęp tylko do odczytu do usługi zarządzania interfejsem API, ale ma tylko dostęp do zapisu do jednego określonego interfejsu API. Aby dowiedzieć się więcej o rolach niestandardowych, zobacz [Role niestandardowe w usłudze Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Aby móc zobaczyć wystąpienie usługi API Management w witrynie ```Microsoft.ApiManagement/service/read``` Azure portal, rola niestandardowa musi zawierać akcję.

Podczas tworzenia roli niestandardowej łatwiej jest rozpocząć od jednej z wbudowanych ról. Edytuj atrybuty, aby dodać **akcje,** **NotActions**lub **AssignableScopes**, a następnie zapisz zmiany jako nową rolę. Poniższy przykład rozpoczyna się od roli "Czytnik usług zarządzania interfejsami API" i tworzy rolę niestandardową o nazwie "Edytor interfejsu API kalkulatora". Rolę niestandardową można przypisać do określonego interfejsu API. W związku z tym ta rola ma tylko dostęp do tego interfejsu API. 

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

Artykuł [dotyczący operacji dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) zawiera listę uprawnień, które można udzielić na poziomie zarządzania interfejsami API.

## <a name="video"></a>Film wideo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o kontroli dostępu opartej na rolach na platformie Azure, zobacz następujące artykuły:
  * [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
  * [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)
  * [Role niestandardowe w usłudze Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operacje dostawcy zasobów usługi Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
