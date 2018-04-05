---
title: Jak używać opartej na rolach kontroli dostępu w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć wbudowanych ról i tworzyć role niestandardowe w usłudze Azure API Management
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
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c775780a39c4d423c62bf88f55d35675c70442c7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Jak używać opartej na rolach kontroli dostępu w usłudze Azure API Management
Zarządzanie interfejsami API Azure polega na kontroli dostępu based (RBAC) umożliwia precyzyjne zarządzanie dostępem dla usługi interfejsu API zarządzania i jednostek (na przykład interfejsów API i zasady). Ten artykuł zawiera przegląd ról wbudowanych i niestandardowych w usłudze API Management. Aby uzyskać więcej informacji dotyczących zarządzania dostępem w portalu Azure, zobacz [wprowadzenie do zarządzania dostępem w portalu Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Wbudowane role
Zarządzanie interfejsami API obecnie udostępnia trzy role wbudowane i doda dwa dodatkowe role w najbliższej przyszłości. W innych zakresach, łącznie z subskrypcji, grupy zasobów, a poszczególne wystąpienia interfejsu API zarządzania można przypisać tych ról. Na przykład jeśli roli "Azure API Management czytnika usługi" zostanie przypisana do użytkownika na poziomie grupy zasobów, użytkownik ma dostęp do odczytu do wszystkich wystąpień usługi API Management znajdującej się w grupie zasobów. 

Poniższa tabela zawiera krótkie opisy wbudowane role. Te role można przypisać za pomocą portalu Azure lub innych narzędzi, takich jak Azure [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli), i [interfejsu API REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Aby uzyskać szczegółowe informacje dotyczące sposobu przypisywania ról wbudowanych, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

| Rola          | Dostęp do odczytu<sup>[1]</sup> | Dostęp do zapisu<sup>[2]</sup> | Tworzenie usług, usuwanie, skalowanie sieci VPN i Konfiguracja domeny niestandardowej | Dostęp do portalu starszych wydawcy | Opis
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Azure API Management usługi współautora | ✓ | ✓ | ✓ | ✓ | Użytkownika nadrzędnego. Ma pełny dostęp CRUD do usługi API Management i jednostek (na przykład interfejsów API i zasady). Ma dostęp do portalu starszych wydawcy. |
| Czytnik usługi zarządzania interfejsu API platformy Azure | ✓ | | || Ma dostęp tylko do odczytu do usługi API Management i jednostek. |
| Operator usługi zarządzania interfejsu API platformy Azure | ✓ | | ✓ | | Można zarządzać usługi API Management, ale nie jednostek.|
| Edytor usługi zarządzania w usłudze Azure interfejsu API<sup>*</sup> | ✓ | ✓ | |  | Można zarządzać zarządzanie interfejsami API jednostek, ale nie usług.|
| Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Można zarządzać portalu dla deweloperów. Dostęp tylko do odczytu do usługi i jednostek.|

<sup>[1] dostęp do odczytu do usługi API Management i jednostek (na przykład interfejsów API i zasady).</sup>

<sup>[2] do zapisu usługi API Management i jednostek, z wyjątkiem następujących operacji: wystąpienie tworzenie, usuwanie i skalowanie; Konfiguracja sieci VPN; i Konfiguracja domeny niestandardowej.</sup>

<sup>\* Edytor usługi roli będą dostępne po możemy migracji wszystkich admin interfejsu użytkownika z istniejącego portalu wydawcy do portalu Azure. Rola menedżera zawartości będą dostępne po portalu wydawcy został zrefaktoryzowany obejmowało tylko funkcje związane z zarządzaniem portalu dla deweloperów.</sup>  

## <a name="custom-roles"></a>Role niestandardowe
Jeśli żadna z wbudowanych ról nie spełnia określonych potrzeb, można tworzyć role niestandardowe do zapewnienia bardziej szczegółowe zarządzanie dostępem dla interfejsu API zarządzania jednostek. Na przykład można utworzyć niestandardową rolę, która ma dostęp tylko do odczytu do usługi API Management, ale ma zapisu dostęp tylko do jednego interfejsu API. Aby dowiedzieć się więcej o niestandardowych rolach, zobacz [niestandardowych ról w Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). 

Po utworzeniu niestandardowej roli zabezpieczeń, łatwiej rozpocząć od jednego z wbudowanych ról. Edycja atrybutów do dodania **akcje**, **NotActions**, lub **AssignableScopes**, a następnie zapisz zmiany jako nową rolę. W poniższym przykładzie rozpoczyna się od roli "Azure API Management usługi czytnika" i tworzy niestandardowej roli zabezpieczeń o nazwie "Kalkulator Edytor interfejsu API". Tworzona rola niestandardowa można przypisać do interfejsu API. W rezultacie tej roli ma dostęp tylko do tego interfejsu API. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

[Operacji dostawcy zasobów usługi Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement) artykuł zawiera listę uprawnień, które można udzielić na poziomie interfejsu API zarządzania.

## <a name="video"></a>Połączenia wideo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o opartej na rolach kontroli dostępu na platformie Azure, zobacz następujące artykuły:
  * [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Role niestandardowe w Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)
  * [Operacje dostawcy zasobów Menedżera zasobów platformy Azure](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement)

