---
title: Ograniczanie dostępu użytkowników do operacji danych tylko za pomocą usługi Azure Cosmos DB
description: Dowiedz się, jak ograniczyć dostęp do operacji danych tylko za pomocą usługi Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980376"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Ograniczanie dostępu użytkowników tylko do operacji na danych

W usłudze Azure Cosmos DB istnieją dwa sposoby uwierzytelniania interakcji z usługą bazy danych:
- używanie tożsamości usługi Azure Active Directory podczas interakcji z witryną Azure portal,
- przy użyciu [kluczy](secure-access-to-data.md#master-keys) usługi Azure Cosmos DB lub [tokenów zasobów](secure-access-to-data.md#resource-tokens) podczas wystawiania wywołań z interfejsów API i zestawów SDK.

Każda metoda uwierzytelniania daje dostęp do różnych zestawów operacji, z pewnym nakładaniem się: ![Dzielenie operacji na typ uwierzytelniania](./media/how-to-restrict-user-data/operations.png)

W niektórych scenariuszach można ograniczyć niektórych użytkowników organizacji do wykonywania operacji danych (czyli żądań CRUD i kwerend) tylko. Zazwyczaj dotyczy to deweloperów, którzy nie muszą tworzyć ani usuwać zasobów ani zmieniać aprowizowanej przepływności kontenerów, nad którymi pracują.

Dostęp można ograniczyć, wykonując następujące kroki:
1. Tworzenie niestandardowej roli usługi Azure Active Directory dla użytkowników, których chcesz ograniczyć dostęp. Niestandardowa rola usługi Active Directory powinna mieć szczegółowy poziom dostępu do operacji przy użyciu [szczegółowych akcji](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)usługi Azure Cosmos DB.
1. Nie zezwalanie na wykonywanie operacji innych niż dane za pomocą kluczy. Można to osiągnąć, ograniczając te operacje tylko do wywołań usługi Azure Resource Manager.

W następnych sekcjach tego artykułu pokazano, jak wykonać te kroki.

> [!NOTE]
> Aby wykonać polecenia w następnych sekcjach, należy zainstalować moduł 3.0.0 lub nowszy programu Azure PowerShell, a także [rolę właściciela platformy Azure](../role-based-access-control/built-in-roles.md#owner) w ramach subskrypcji, którą próbujesz zmodyfikować.

W skryptach programu PowerShell w następnych sekcjach zastąp następujące symbole zastępcze wartościami specyficznymi dla danego środowiska:
- `$MySubscriptionId`- Identyfikator subskrypcji, który zawiera konto usługi Azure Cosmos, gdzie chcesz ograniczyć uprawnienia. Na przykład: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- Grupa zasobów zawierająca konto usługi Azure Cosmos. Na przykład: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- Nazwa konta usługi Azure Cosmos. Na przykład: `mycosmosdbsaccount`.
- `$MyUserName`- Loginusername@domain( ) użytkownika, dla którego chcesz ograniczyć dostęp. Na przykład: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Wybierz subskrypcję platformy Azure

Polecenia programu Azure PowerShell wymagają logowania i wybierania subskrypcji w celu wykonania poleceń:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Tworzenie niestandardowej roli usługi Azure Active Directory

Poniższy skrypt tworzy przypisanie roli usługi Azure Active Directory z dostępem "Tylko klucz" dla kont usługi Azure Cosmos. Rola jest oparta na [rolach niestandardowych dla zasobów platformy Azure](../role-based-access-control/custom-roles.md) i akcji [granulowany dla usługi Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Te role i akcje `Microsoft.DocumentDB` są częścią obszaru nazw usługi Azure Active Directory.

1. Najpierw utwórz dokument JSON o nazwie `AzureCosmosKeyOnlyAccess.json` z następującą zawartością:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Uruchom następujące polecenia, aby utworzyć przypisanie roli i przypisać je do użytkownika:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Nie zezwalaj na wykonywanie operacji niezwiązanych z danymi

Następujące polecenia odbierają możliwość używania klawiszy do:
- tworzenie, modyfikowanie lub usuwanie zasobów
- aktualizować ustawienia kontenera (w tym zasady indeksowania, przepływność itp.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kontroli dostępu usługi Cosmos DB opartej na rolach](role-based-access-control.md)
- Omówienie [bezpiecznego dostępu do danych w usłudze Cosmos DB](secure-access-to-data.md)
