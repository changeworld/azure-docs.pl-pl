---
title: Tworzenie niestandardowych ról dla Azure RBAC | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdefiniować role niestandardowe z kontroli dostępu dla bardziej precyzyjne zarządzanie tożsamościami w ramach subskrypcji platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e2ea46ea1a6b5bd3f50d4d4c15492c16c5241c0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="create-custom-roles-in-azure"></a>Tworzenie niestandardowych ról na platformie Azure

Jeśli [wbudowane role](built-in-roles.md) nie spełnia Twoich potrzeb określonym dostępu, można tworzyć własne role niestandardowe. Podobnie jak wbudowane role role niestandardowe można przypisać do użytkowników, grup i nazwy główne usług, subskrypcji, grupy zasobów i zakresy zasobów. Role niestandardowe są przechowywane w dzierżawie usługi Azure Active Directory (Azure AD) i mogą być udostępniane między subskrypcjami. Role niestandardowe można tworzyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST. W tym artykule opisano przykładowy sposób rozpocząć tworzenie niestandardowych ról przy użyciu programu PowerShell i interfejsu wiersza polecenia Azure.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Tworzenie niestandardowej roli zabezpieczeń można otworzyć żądania obsługi przy użyciu programu PowerShell

Aby utworzyć niestandardową rolę, może rozpoczynać się od wbudowanej roli, go edytować i następnie utwórz nową rolę. Na przykład wbudowana [czytnika](built-in-roles.md#reader) roli jest dostosowana do utworzenia niestandardowej roli zabezpieczeń o nazwie "biletami pomocy technicznej czytnika poziom dostępu". Umożliwia użytkownikowi przeglądanie wszystko w subskrypcji, a także żądania pomocy technicznej Otwórz.

> [!NOTE]
> Tylko dwa wbudowane role, które umożliwiają użytkownikom otwarcia żądania pomocy technicznej są [właściciela](built-in-roles.md#owner) i [współautora](built-in-roles.md#contributor). Użytkownik może mieć możliwość otwarcia żądania pomocy technicznej on należy przypisać rolę w zakresie subskrypcji, ponieważ wszystkie żądania pomocy technicznej są tworzone na podstawie subskrypcji platformy Azure.

W programie PowerShell, użyj [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) polecenie, aby wyeksportować [czytnika](built-in-roles.md#reader) roli w formacie JSON.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Poniżej pokazano dane wyjściowe JSON dla [czytnika](built-in-roles.md#reader) roli. Typowa rola składa się z trzech głównych sekcji, `Actions`, `NotActions`, i `AssignableScopes`. `Actions` Sekcja zawiera informacje o dozwolonych operacji w roli. Aby wykluczyć operacji `Actions`, dodaj je do `NotActions`. Czynne uprawnienia jest obliczana przez odjęcie `NotActions` operacji `Actions` operacji.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Następnie możesz edytować dane wyjściowe do utworzenia niestandardowej roli zabezpieczeń JSON. W takim przypadku można utworzyć obsługi biletów, `Microsoft.Support/*` operacja musi zostać dodany. Każda operacja staje się dostępny od dostawcy zasobów. Aby uzyskać listę działań dla dostawcy zasobów, można użyć [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) polecenie lub zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md).

Jest to konieczne, że rola zawiera jawne subskrypcji identyfikatorów, w którym została użyta. Identyfikatory subskrypcji są wyświetlane w obszarze `AssignableScopes`, w przeciwnym razie będzie nie można zaimportować rolę do subskrypcji.

Ponadto należy ustawić `IsCustom` właściwości `true` do określenia, że jest to niestandardowej roli zabezpieczeń.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Aby utworzyć nową rolę niestandardowe, należy użyć [AzureRmRoleDefinition nowy](/powershell/module/azurerm.resources/new-azurermroledefinition) poleceń i podaj zaktualizowany plik definicji roli JSON.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Po uruchomieniu [AzureRmRoleDefinition nowy](/powershell/module/azurerm.resources/new-azurermroledefinition), nowej niestandardowej roli są dostępne w portalu Azure, a można przypisać do użytkowników.

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń zaimportowany w portalu Azure](./media/custom-roles/18.png)

![Zrzut ekranu przedstawiający przypisywanie niestandardowej roli zabezpieczeń zaimportowane do użytkownika w tym samym katalogu](./media/custom-roles/19.png)

![Zrzut ekranu przedstawiający uprawnienia niestandardowe importowanych roli](./media/custom-roles/20.png)

Użytkownicy z tą rolą niestandardowe mogą tworzyć nowe żądania pomocy technicznej.

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń tworzenia żądań obsługi](./media/custom-roles/21.png)

Użytkownicy z tę rolę niestandardową nie może wykonywać inne akcje, takie jak tworzenie maszyn wirtualnych lub tworzenia grup zasobów.

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń nie można utworzyć maszyny wirtualne](./media/custom-roles/22.png)

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń nie można utworzyć nowego RGs](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Tworzenie niestandardowej roli zabezpieczeń można otworzyć żądania obsługi przy użyciu wiersza polecenia platformy Azure

Kroki, aby utworzyć niestandardową rolę przy użyciu interfejsu wiersza polecenia Azure przypominają przy użyciu programu PowerShell, różni się dane wyjściowe JSON.

Na przykład można uruchomić z wbudowanej [czytnika](built-in-roles.md#reader) roli. Aby wyświetlić listę działań [czytnika](built-in-roles.md#reader) rolę, użyj [listy definicji roli az](/cli/azure/role/definition#az_role_definition_list) polecenia.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Utwórz plik JSON o następującym formacie. `Microsoft.Support/*` Operacji został dodany w `Actions` sekcjach, aby ten użytkownik może otworzyć żądania pomocy technicznej pozostawiając być do odczytu. Należy dodać identyfikator subskrypcji, w którym ta rola będzie używany w `AssignableScopes` sekcji.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Aby utworzyć nową rolę niestandardowe, użyj [utworzenia definicji roli az](/cli/azure/role/definition#az_role_definition_create) polecenia.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Nowa rola niestandardowy jest teraz dostępna w portalu Azure i procesu, aby użyć tej roli są takie same jak w poprzedniej sekcji środowiska PowerShell.

![Azure portalu zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń utworzone za pomocą interfejsu wiersza polecenia 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>Zobacz także
- [Zrozumienie definicje ról](role-definitions.md)
- [Zarządzanie kontrolą dostępu na podstawie ról z AzurePowerShell](role-assignments-powershell.md)
- [Zarządzanie kontrolą dostępu opartej na rolach przy użyciu wiersza polecenia platformy Azure](role-assignments-cli.md)
- [Zarządzanie kontrolą dostępu opartej na rolach przy użyciu interfejsu API REST](role-assignments-rest.md)
