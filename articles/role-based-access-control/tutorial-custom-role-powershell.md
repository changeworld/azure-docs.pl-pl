---
title: Samouczek — tworzenie roli niestandardowej przy użyciu programu Azure PowerShell | Microsoft Docs
description: Rozpocznij pracę, tworząc rolę niestandardową przy użyciu programu Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: f49f6f03b6d9f1c51cada58ae782bbc364fc9d66
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427291"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Samouczek: tworzenie roli niestandardowej przy użyciu programu Azure PowerShell

Jeśli [role wbudowane](built-in-roles.md) nie spełniają specyficznych potrzeb Twojej organizacji, możesz utworzyć własne role niestandardowe. W tym samouczku utworzysz niestandardową rolę o nazwie Reader Support Tickets przy użyciu programu Azure PowerShell. Rola niestandardowa umożliwi użytkownikowi przeglądanie całej zawartości subskrypcji, a także otwieranie biletów pomocy technicznej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie roli niestandardowej
> * Wyświetlanie ról niestandardowych
> * Aktualizacja roli niestandardowej
> * Usuwanie roli niestandardowej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- Program [Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) zainstalowany lokalnie

## <a name="sign-in-to-azure-powershell"></a>Logowanie się do programu Azure PowerShell

Zaloguj się do programu [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Najprostszym sposobem na utworzenie roli niestandardowej jest rozpoczęcie od wbudowanej roli, poddanie jej edycji, a następnie utworzenie nowej roli.

1. W programie PowerShell użyj polecenia [Get AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation), aby uzyskać listę operacji dla dostawcy zasobów Microsoft.Support. Dobrze jest znać operacje, które są dostępne do tworzenia uprawnień. Można także wyświetlić listę wszystkich operacji w temacie [Operacje dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Użyj polecenia [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) w celu wygenerowania roli [Czytelnik](built-in-roles.md#reader) w formacie JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Otwórz plik **ReaderSupportRole.json** w edytorze.

    Poniżej przedstawiono dane wyjściowe w formacie JSON. Informacje o różnych właściwościach zawiera temat [Role niestandardowe](custom-roles.md).

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
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Edytuj plik JSON, aby dodać operację `"Microsoft.Support/*"` do właściwości `Actions`. Pamiętaj o dodaniu przecinka po operacji odczytu. Ta akcja umożliwia użytkownikowi tworzenie biletów pomocy technicznej.

1. Pobierz identyfikator subskrypcji za pomocą polecenia [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. W elemencie `AssignableScopes` dodaj swój identyfikator subskrypcji w następującym formacie: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Musisz jawnie dodać identyfikatory subskrypcji, ponieważ w przeciwnym razie nie będzie można zaimportować roli do subskrypcji.

1. Usuń wiersz właściwości `Id` i zmień właściwość `IsCustom` na `true`.

1. Zmień właściwości `Name` i `Description` na „Reader Support Tickets” i „View everything in the subscription and also open support tickets”.

    Zawartość pliku JSON powinna wyglądać następująco:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. Aby utworzyć nową rolę niestandardową, użyj polecenia [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) i określ plik definicji roli JSON.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Nowa rola niestandardowa jest teraz dostępna w witrynie Azure Portal i można ją przypisywać do użytkowników, grup lub jednostek usług tak samo jak role wbudowane.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

- Aby wyświetlić listę wszystkich ról niestandardowych, użyj polecenia [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Rolę niestandardową można również sprawdzić w witrynie Azure Portal.

    ![zrzut ekranu przedstawiający rolę niestandardową zaimportowaną do witryny Azure Portal](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, zaktualizuj plik JSON lub użyj obiektu `PSRoleDefinition`.

1. Aby zaktualizować plik JSON, użyj polecenia [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) w celu wygenerowania roli niestandardowej w formacie JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Otwórz plik w edytorze.

1. W elemencie `Actions` dodaj operację do tworzenia wdrożeń grupy zasobów i zarządzania nimi `"Microsoft.Resources/deployments/*"`.

    Zaktualizowany plik JSON powinien wyglądać następująco:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. Aby zaktualizować rolę niestandardową, użyj polecenia [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) i określ zaktualizowany plik JSON.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Aby użyć obiektu `PSRoleDefintion` do aktualizacji roli niestandardowej, najpierw użyj polecenia [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) w celu pobrania roli.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Wywołaj metodę `Add` w celu dodania operacji do odczytu ustawień diagnostycznych.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Użyj polecenia [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) w celu zaktualizowania roli.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

1. Użyj polecenia [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) w celu uzyskania identyfikatora roli niestandardowej.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Użyj polecenia [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) i określ identyfikator roli w celu usunięcia roli niestandardowej.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Gdy zostanie wyświetlony monit o potwierdzenie, wpisz **Y** (T).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie ról niestandardowych przy użyciu programu PowerShell](custom-roles-powershell.md)
