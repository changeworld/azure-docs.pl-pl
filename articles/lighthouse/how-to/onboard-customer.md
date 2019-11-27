---
title: Dołączanie klienta do zarządzania zasobami delegowanymi na platformie Azure
description: Dowiedz się, jak dołączyć klienta do zarządzania zasobami delegowanymi przez platformę Azure, umożliwiając dostęp do zasobów i zarządzanie nimi za pomocą własnej dzierżawy.
ms.date: 11/7/2019
ms.topic: conceptual
ms.openlocfilehash: fde0e82ff2dcf048643524b5a2d076d66a4f5a50
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463955"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Dołączanie klienta do zarządzania zasobami delegowanymi na platformie Azure

W tym artykule wyjaśniono, jak usługodawca może dołączyć klienta do zarządzania zasobami delegowanymi przez platformę Azure, umożliwiając dostęp do swoich delegowanych zasobów (subskrypcji i/lub grup zasobów) oraz zarządzanie nimi za pomocą własnej dzierżawy usługi Azure Active Directory (Azure AD). Mimo że będziemy odnieść się do dostawców usług i klientów w tym miejscu, przedsiębiorstwa zarządzające wieloma dzierżawcami mogą używać tego samego procesu do konsolidacji ich środowiska zarządzania.

Ten proces można powtórzyć, Jeśli zarządzasz zasobami dla wielu klientów. Następnie, gdy autoryzowany użytkownik loguje się do dzierżawy, ten użytkownik może być autoryzowany w zakresach dzierżawy klienta w celu wykonywania operacji zarządzania bez konieczności logowania się do każdej dzierżawy klienta.

Możesz skojarzyć swój identyfikator Microsoft Partner Network (MPN) z subskrypcjami dołączanymi, aby śledzić wpływ klientów i na otrzymywanie rozpoznawania. Aby uzyskać więcej informacji, zobacz [łączenie identyfikatora partnera z kontami platformy Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Zwróć uwagę, że musisz wykonać to skojarzenie w dzierżawie dostawcy usług.

> [!NOTE]
> Po zakupieniu oferty usług zarządzanych (publicznej lub prywatnej) opublikowanej w portalu Azure Marketplace można również dołączyć klientów. Aby uzyskać więcej informacji, zobacz temat [Publikowanie usług zarządzanych w witrynie Azure Marketplace](publish-managed-services-offers.md). Możesz również użyć procesu dołączania opisanego tutaj z ofertą opublikowaną w witrynie Azure Marketplace.

Proces dołączania wymaga wykonania akcji z poziomu dzierżawy dostawcy usług i dzierżawy klienta. Wszystkie te kroki opisano w tym artykule.

> [!IMPORTANT]
> Obecnie nie można dołączyć subskrypcji (lub grupy zasobów w ramach subskrypcji) do zarządzania zasobami delegowanymi przez platformę Azure, jeśli subskrypcja używa Azure Databricks. Podobnie, jeśli subskrypcja została zarejestrowana w celu dołączenia do dostawcy zasobów **Microsoft. ManagedServices** , nie będzie można w tej chwili utworzyć obszaru roboczego dla tej subskrypcji.

## <a name="gather-tenant-and-subscription-details"></a>Zbierz szczegóły dzierżawy i subskrypcji

Aby dołączyć dzierżawcę klienta, musi on mieć aktywną subskrypcję platformy Azure. Musisz znać następujące informacje:

- Identyfikator dzierżawy dzierżawy dostawcy usług (gdzie będziesz zarządzać zasobami klienta)
- Identyfikator dzierżawy dzierżawy klienta (który będzie mieć zasoby zarządzane przez dostawcę usługi)
- Identyfikatory subskrypcji dla każdej określonej subskrypcji w dzierżawie klienta, która będzie zarządzana przez dostawcę usług (lub zawierający grupy zasobów, które będą zarządzane przez dostawcę usług)

Jeśli te informacje nie są już dostępne, możesz je pobrać w jeden z następujących sposobów.

### <a name="azure-portal"></a>Azure Portal

Identyfikator dzierżawy może być widoczny przez umieszczenie kursora nad nazwą konta w prawym górnym rogu Azure Portal lub przez wybranie pozycji **Przełącz katalog**. Aby wybrać i skopiować identyfikator dzierżawy, wyszukaj frazę "Azure Active Directory" w portalu, a następnie wybierz pozycję **Właściwości** i skopiuj wartość podaną w polu **Identyfikator katalogu** . Aby znaleźć identyfikator subskrypcji, wyszukaj frazę "subskrypcje", a następnie wybierz odpowiedni identyfikator subskrypcji.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Podczas dołączania subskrypcji (lub jednej lub większej liczby grup zasobów w ramach subskrypcji) przy użyciu procesu opisanego w tym miejscu dostawca zasobów **Microsoft. ManagedServices** zostanie zarejestrowany dla tej subskrypcji.

## <a name="define-roles-and-permissions"></a>Definiowanie ról i uprawnień

Jako dostawca usług możesz chcieć wykonać wiele zadań dla pojedynczego klienta, wymagając innego dostępu dla różnych zakresów. Można zdefiniować dowolną liczbę autoryzacji wymaganych do przypisywania [ról wbudowanej kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) do użytkowników w dzierżawie.

Aby ułatwić zarządzanie, zalecamy korzystanie z grup użytkowników usługi Azure AD dla każdej roli, co pozwala na dodawanie lub usuwanie poszczególnych użytkowników do grupy zamiast przypisywania uprawnień bezpośrednio do tego użytkownika. Możesz również przypisać role do jednostki usługi. Upewnij się, że przestrzegasz zasady najniższych uprawnień, aby użytkownicy mieli tylko uprawnienia do wykonywania swoich zadań. Aby uzyskać zalecenia i informacje o obsługiwanych rolach, zobacz [dzierżawy, użytkownicy i role w scenariuszach usługi Azure Lighthouse](../concepts/tenants-users-roles.md).

Aby zdefiniować autoryzacje, musisz znać wartości identyfikatora dla każdego użytkownika, grupy użytkowników lub nazwy głównej usługi, do której chcesz udzielić dostępu. Wymagany jest również identyfikator definicji roli dla każdej wbudowanej roli, która ma zostać przypisana. Jeśli ich nie masz, możesz je pobrać w jeden z następujących sposobów.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Zalecamy przypisanie [roli usuwania przypisania rejestracji usług zarządzanych](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) podczas dołączania klienta, dzięki czemu użytkownicy w dzierżawie mogą później w razie potrzeby [usunąć dostęp do delegowania](#remove-access-to-a-delegation) . Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

## <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

Aby dołączyć klienta, musisz utworzyć szablon [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) dla oferty z następującymi informacjami. Wartości **mspOfferName** i **mspOfferDescription** są widoczne dla klienta podczas wyświetlania szczegółów oferty na [stronie dostawcy usług](view-manage-service-providers.md) Azure Portal.

|Pole  |Definicja  |
|---------|---------|
|**mspOfferName**     |Nazwa opisująca tę definicję. Ta wartość jest wyświetlana klientowi jako tytuł oferty.         |
|**mspOfferDescription**     |Krótki opis oferty (na przykład "Oferta zarządzania maszyną wirtualną firmy Contoso")      |
|**managedByTenantId**     |Identyfikator dzierżawy         |
|**autoryzacji**     |**PrincipalId** wartości dla użytkowników/grup/nazw SPN z dzierżawy, z których każdy ma **principalIdDisplayName** , aby pomóc klientowi zrozumieć cel autoryzacji i zamapować na wbudowaną wartość **zduplikowanych** , aby określić poziom dostępu         |

Aby dołączać subskrypcję klienta, należy użyć odpowiedniego szablonu Azure Resource Manager dostarczanego w naszym [repozytorium przykładów](https://github.com/Azure/Azure-Lighthouse-samples/)oraz odpowiedniego pliku parametrów, który można zmodyfikować, aby dopasować konfigurację i zdefiniować autoryzacje. Poszczególne szablony są udostępniane w zależności od tego, czy dołączysz całą subskrypcję, grupę zasobów lub wiele grup zasobów w ramach subskrypcji. Udostępniamy również szablon, który może być używany dla klientów, którzy kupili ofertę usługi zarządzanej opublikowaną w portalu Azure Marketplace, jeśli wolisz dołączyć swoje subskrypcje w ten sposób.

|Aby dołączyć to  |Użyj tego szablonu Azure Resource Manager  |I zmodyfikuj ten plik parametrów |
|---------|---------|---------|
|Subskrypcja   |[delegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Grupa zasobów   |[rgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Wiele grup zasobów w ramach subskrypcji   |[multipleRgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subskrypcja (w przypadku korzystania z oferty opublikowanej w portalu Azure Marketplace)   |[marketplaceDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Opisany tutaj proces wymaga oddzielnego wdrożenia dla każdej subskrypcji. W przypadku dołączania wielu grup zasobów w ramach różnych subskrypcji wymagane są również oddzielne wdrożenia. Istnieje jednak możliwość dołączania wielu grup zasobów w ramach jednej subskrypcji w jednym wdrożeniu.
>
> Oddzielne wdrożenia są również wymagane dla wielu ofert, które są stosowane do tej samej subskrypcji (lub grup zasobów w ramach subskrypcji). Każda zastosowana oferta musi używać innego **mspOfferName**.

W poniższym przykładzie przedstawiono plik **delegatedResourceManagement. Parameters. JSON** , który zostanie użyty do dołączenia subskrypcji. Pliki parametrów grupy zasobów (znajdujące się w folderze [RG-delegowani-Resource-Management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) ) są podobne, ale zawierają także parametr **rgName** w celu zidentyfikowania określonych grup zasobów, które mają zostać dołączone.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
Ostatnia autoryzacja w powyższym przykładzie dodaje **principalId** z rolą administratora dostępu użytkownika (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Podczas przypisywania tej roli należy uwzględnić Właściwość **delegatedRoleDefinitionIds** i jedną lub więcej ról wbudowanych. Użytkownik utworzony w ramach tej autoryzacji będzie mógł przypisać te wbudowane role do [zarządzanych tożsamości](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), które są wymagane w celu [wdrożenia zasad, które można skorygować](deploy-policy-remediation.md). Dla tego użytkownika nie są stosowane żadne inne uprawnienia zwykle skojarzone z rolą administratora dostępu użytkownika.

## <a name="deploy-the-azure-resource-manager-templates"></a>Wdrażanie szablonów Azure Resource Manager

Po zaktualizowaniu pliku parametrów klient musi wdrożyć szablon Azure Resource Manager w dzierżawie klienta jako wdrożenie na poziomie subskrypcji. Dla każdej subskrypcji, która ma zostać dołączona do zarządzania zasobami delegowanymi przez platformę Azure (lub dla każdej subskrypcji zawierającej grupy zasobów, które chcesz dołączyć), wymagane jest oddzielne wdrożenie.

Ponieważ jest to wdrożenie na poziomie subskrypcji, nie można go zainicjować w Azure Portal. Wdrożenie może odbywać się przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, jak pokazano poniżej.

> [!IMPORTANT]
> Wdrożenie musi zostać wykonane przez konto niebędące Gośćmi w dzierżawie klienta, które ma [wbudowaną rolę właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) subskrypcji (lub która zawiera grupy zasobów, które są dołączane). Aby wyświetlić wszystkich użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie może wybrać subskrypcję w Azure Portal, otworzyć funkcję **Kontrola dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą właściciela](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Potwierdzenie pomyślnego dołączenia

Po pomyślnym dodaniu subskrypcji klienta do zarządzania zasobami delegowanymi przez platformę Azure użytkownicy w dzierżawie dostawcy usług będą mogli zobaczyć subskrypcję i jej zasoby (Jeśli udzielono im dostępu za pomocą powyższego procesu, indywidualnie lub jako członek grupy usługi Azure AD z odpowiednimi uprawnieniami. Aby to potwierdzić, upewnij się, że subskrypcja jest wyświetlana w jeden z następujących sposobów.  

### <a name="azure-portal"></a>Azure Portal

W dzierżawie dostawcy usług:

1. Przejdź do [strony moi klienci](view-manage-customers.md).
2. Wybierz pozycję **klienci**.
3. Upewnij się, że można zobaczyć subskrypcje o nazwie oferty podanej w szablonie Menedżer zasobów.

> [!IMPORTANT]
> Aby można było wyświetlić delegowaną subskrypcję w [swoich klientach](view-manage-customers.md), użytkownicy w dzierżawie dostawcy usługi muszą mieć przydaną rolę [czytelnika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika), gdy subskrypcja została dołączona na platformę Azure delegowane zarządzanie zasobami.

W dzierżawie klienta:

1. Przejdź do [strony dostawcy usług](view-manage-service-providers.md).
2. Wybierz pozycję **oferty dostawcy usług**.
3. Upewnij się, że można zobaczyć subskrypcje o nazwie oferty podanej w szablonie Menedżer zasobów.

> [!NOTE]
> Po zakończeniu wdrożenia może upłynąć kilka minut, zanim aktualizacje zostaną odzwierciedlone w Azure Portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Usuwanie dostępu do delegowania

Domyślnie użytkownik w dzierżawie klienta, który ma odpowiednie uprawnienia, może usunąć dostęp do zasobów, które zostały delegowane do dostawcy usług na [stronie dostawcy usług](view-manage-service-providers.md#add-or-remove-service-provider-offers) w Azure Portal.

Jeśli podczas dołączania klienta do zarządzania zasobami delegowanymi przez platformę Azure zarejestrowano rolę użytkowników z [przypisaniem usługi zarządzanej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) , użytkownicy w Twojej dzierżawie mogą również usunąć delegowanie. Gdy to zrobisz, żaden użytkownik w dzierżawie dostawcy usług nie będzie w stanie uzyskać dostępu do zasobów, które zostały wcześniej delegowane.

W poniższym przykładzie przedstawiono przypisanie przyznanie **roli usuwania przypisania rejestracji usług zarządzanych** , która może być uwzględniona w pliku parametrów:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Użytkownik mający to uprawnienie może usunąć delegowanie w jeden z następujących sposobów.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete –assignment <id or full resourceId>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlaj klientów i zarządzaj nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
