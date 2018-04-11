---
title: "Tworzenie niestandardowych ról dla Azure RBAC | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak zdefiniować role niestandardowe z kontroli dostępu dla bardziej precyzyjne zarządzanie tożsamościami w ramach subskrypcji platformy Azure."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2871ff5eea8fb99040dfab2593d1640d79f51092
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Tworzenie niestandardowych ról dla kontroli dostępu
Utwórz niestandardową rolę based kontroli dostępu (RBAC), jeśli żadna wbudowanych ról nie spełnia Twoje potrzeby określonym dostępu. Role niestandardowe można tworzyć przy użyciu [programu Azure PowerShell](role-based-access-control-manage-access-powershell.md), [interfejsu wiersza polecenia platformy Azure](role-based-access-control-manage-access-azure-cli.md) (CLI) i [interfejsu API REST](role-based-access-control-manage-access-rest.md). Podobnie jak wbudowane role role niestandardowe można przypisać do użytkowników, grup i aplikacji w subskrypcji, grupy zasobów i zakresy zasobów. Role niestandardowe są przechowywane w dzierżawie usługi Azure AD i mogą być udostępniane między subskrypcjami.

Każdy Dzierżawca może tworzyć role niestandardowe do 2000. 

W poniższym przykładzie przedstawiono niestandardowej roli zabezpieczeń dotyczące monitorowania i ponowne uruchamianie maszyn wirtualnych:

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Akcje
**Akcje** operacje platformy Azure, do których rola przyznaje dostęp określa właściwości niestandardowej roli zabezpieczeń. Jest kolekcją operacji ciągów, które identyfikują zabezpieczanego operacje dostawców zasobów platformy Azure. Operacja ciągów wykonaj format `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Ciągi operacji, które zawierają symbole wieloznaczne (\*) udzielić dostępu do wszystkich operacji, które zgodny z ciągiem operacji. Na wystąpienie:

* `*/read`przyznaje dostęp do odczytu dla operacji dla wszystkich typów zasobów wszystkich dostawców zasobów platformy Azure.
* `Microsoft.Compute/*`zapewnia dostęp do wszystkich operacji dla wszystkich typów zasobów w dostawcy zasobów Microsoft.Compute.
* `Microsoft.Network/*/read`przyznaje dostęp do odczytu dla operacji dla wszystkich typów zasobów Microsoft.Network dostawcy zasobów platformy Azure.
* `Microsoft.Compute/virtualMachines/*`przyznaje dostęp do wszystkich operacji maszyn wirtualnych i jego podrzędny typów zasobów.
* `Microsoft.Web/sites/restart/Action`udziela dostępu do ponownego uruchomienia witryny sieci Web.

Użyj `Get-AzureRmProviderOperation` (w programie PowerShell) lub `azure provider operations show` (w Azure CLI) do operacji listy dostawców zasobów platformy Azure. Może również używać tych poleceń, sprawdź, czy parametry operacji są prawidłowe i rozwiń ciągi operacji symboli wieloznacznych.

```powershell
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Zrzut ekranu PowerShell - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```azurecli
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure CLI zrzut ekranu - azure dostawcy operacji Pokaż "Microsoft.Compute/virtualMachines/\*Action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Użyj **NotActions** właściwości, jeśli zestaw działań, które mają być dozwolone łatwiej jest definiowana za pomocą z wyjątkiem operacji ograniczone. Prawa dostępu przyznane przez niestandardowej roli zabezpieczeń jest obliczana przez odjęcie **NotActions** operacji **akcje** operacji.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, który wyklucza operacji w **NotActions**i ma przypisaną rolę drugiego, która udziela dostępu do tej samej operacji, użytkownik może wykonać tej operacji. **NotActions** nie jest odmowy reguły — jest po prostu wygodny sposób utworzyć zestaw dozwolonych operacji podczas określonych operacji muszą być wykluczone.
>
>

## <a name="assignablescopes"></a>AssignableScopes
**AssignableScopes** właściwości niestandardowej roli określa zakresów (subskrypcji, grupy zasobów lub zasobów) w obrębie których są dostępne do przypisania roli niestandardowych. Udostępnienie tworzona rola niestandardowa przydziału w subskrypcji lub grupy zasobów, które wymagają i nie zajmowały czynności użytkownika dla pozostałej części subskrypcji lub grupy zasobów.

Prawidłowe zakresy można przypisać należą:

* "/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/ subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624" - sprawia, że rola jest dostępne do przypisania w dwóch subskrypcji.
* "/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e" - sprawia, że rola jest dostępne do przypisania w ramach jednej subskrypcji.
* "/ subskrypcji i c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/sieci" - sprawia, że rola dostępne do przypisania tylko w grupie zasobów sieciowych.

> [!NOTE]
> Należy użyć co najmniej jednego subskrypcji, grupy zasobów lub identyfikator zasobu.
>
>

## <a name="custom-roles-access-control"></a>Kontrola dostępu role niestandardowe
**AssignableScopes** właściwości niestandardowej roli również określać, kto może wyświetlać, modyfikowanie i usuwanie roli.

* Który można utworzyć niestandardową rolę?
    Właściciele (i administratorów dostępu użytkownika) subskrypcji, grupy zasobów i zasoby można tworzyć role niestandardowe do użycia w tych zakresów.
    Tworzenie roli użytkownika musi mieć możliwość wykonania `Microsoft.Authorization/roleDefinition/write` operację na wszystkich **AssignableScopes** roli.
* Kto może zmodyfikować niestandardową rolę?
    Właściciele (i administratorów dostępu użytkowników) z subskrypcji, grupy zasobów i zasoby można zmodyfikować role niestandardowe w tych zakresów. Użytkownicy muszą mieć możliwość wykonania `Microsoft.Authorization/roleDefinition/write` operację na wszystkich **AssignableScopes** z niestandardowej roli zabezpieczeń.
* Kto może wyświetlać role niestandardowe?
    Wszystkie role wbudowane w Azure RBAC umożliwiają wyświetlanie ról, które są dostępne do przypisania. Użytkownicy, którzy mogą wykonywać `Microsoft.Authorization/roleDefinition/read` operacji w zakresie mogą wyświetlać role RBAC, które są dostępne do przypisania w tym zakresie.

## <a name="see-also"></a>Zobacz także
* [Kontroli dostępu opartej na rolach](role-based-access-control-configure.md): rozpoczynanie pracy z RBAC w portalu Azure.
* Aby uzyskać listę dostępnych operacji, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](role-based-access-control-resource-provider-operations.md).
* Dowiedz się, jak Zarządzaj dostępem za pomocą:
  * [Program PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](role-based-access-control-manage-access-azure-cli.md)
  * [Interfejs API REST](role-based-access-control-manage-access-rest.md)
* [Wbudowane role](role-based-access-built-in-roles.md): uzyskiwanie szczegółowych informacji dotyczących ról, które standardowo w RBAC.
