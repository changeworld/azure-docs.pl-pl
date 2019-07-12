---
title: Dołączanie klientów na platformie Azure delegowane Zarządzanie zasobów - morskiej platformy Azure
description: Dowiedz się, jak dołączyć klienta na platformie Azure delegowane zarządzanie zasobami co ich zasobów będzie mieć dostęp i zarządzane za pośrednictwem własnej dzierżawy.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809862"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Dołączanie klientów na platformie Azure delegowane zarządzanie zasobami

W tym artykule wyjaśniono, jak, jako dostawca usług, można dodać klienta do środowiska zarządzania usługi Azure resource delegowanego, umożliwiając ich delegowanego zasobów (subskrypcje i/lub grupy zasobów) może być dostępne i zarządzane za pomocą własnego (Azure Active Directory Dzierżawy usługi Azure AD). Gdy będziemy odnosić się do dostawców usług i klientów w tym miejscu, przedsiębiorstw Zarządzanie wieloma dzierżawcami umożliwia ten sam proces konsolidować swoje środowisko zarządzania.

Ten proces można powtarzać w przypadku zarządzania zasobami dla wielu klientów. Następnie gdy uwierzytelniony użytkownik loguje się do swojej dzierżawy, ten użytkownik może uprawnień w zakresach dzierżawy klienta do wykonywania operacji zarządzania bez konieczności logowania się do każdego dzierżawcę poszczególnych klientów.

Identyfikator sieci Microsoft Partner Network (MPN) można skojarzyć z subskrypcjami dołączone do śledzenia Twojej wpływu na podczas pracy z klientami. Aby uzyskać więcej informacji, zobacz [połączyć Identyfikatora partnera kont systemu Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

> [!NOTE]
> Klienci mogą być dołączone automatycznie, gdy dokupić oferty usług zarządzanych (publiczny lub prywatny), który opublikowanych w portalu Azure Marketplace. Aby uzyskać więcej informacji, zobacz [publikowania usług zarządzanych przez oferty w portalu Azure Marketplace](publish-managed-services-offers.md). Można również użyć procesu dołączania, opisane w tym miejscu przy użyciu oferty publikowane w witrynie Azure Marketplace.

Proces dołączania wymaga akcje do wykonania z w ramach dzierżawy zarówno dostawcy usług i dzierżawy klienta. Wszystkie te kroki są opisane w tym artykule.

> [!IMPORTANT]
> Obecnie nie można dołączyć subskrypcji (lub grupy zasobów w ramach subskrypcji) na platformie Azure delegować zarządzanie zasobami, jeśli subskrypcja używa usługi Azure Databricks. Podobnie jeśli subskrypcja została zarejestrowana dla dołączania przy użyciu **Microsoft.ManagedServices** dostawcy zasobów nie można utworzyć obszar roboczy usługi Databricks dla tej subskrypcji w tej chwili.

## <a name="gather-tenant-and-subscription-details"></a>Zbieranie szczegółów dzierżawę i subskrypcję

Aby dołączyć dzierżawy klienta musi on mieć aktywną subskrypcję platformy Azure. Należy uwzględnić następujące czynniki:

- Identyfikator dzierżawy dzierżawy dostawcy usług (gdzie będzie zarządzany przez klienta zasobów)
- Identyfikator dzierżawy w dzierżawie klienta, (który będzie miał zasobów zarządzanych przez dostawcę usługi)
- Identyfikator subskrypcji dla każdej określonej subskrypcji w dzierżawie klienta, które będą zarządzane przez dostawcę usług (lub grupy zasobów, które będą zarządzane przez dostawcę usług, który zawiera)

Jeśli nie masz już te informacje, można je pobrać w jednym z następujących sposobów.

### <a name="azure-portal"></a>Azure Portal

Identyfikator dzierżawy są widoczne, ustawiając kursor nad nazwę swojego konta w prawym górnym po prawej stronie w witrynie Azure Portal lub wybierając **Przełącz katalog**. Aby wybrać i skopiować swój identyfikator dzierżawy, wyszukaj "Azure Active Directory" z poziomu portalu, a następnie wybierz **właściwości** i skopiuj wartość pokazana w **identyfikator katalogu** pola. Aby znaleźć identyfikator subskrypcji, wyszukaj "Subskrypcje", a następnie wybierz pozycję identyfikatora odpowiedniej subskrypcji.

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


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Upewnij się, że subskrypcja klienta jest zarejestrowana dla dołączania

Każda subskrypcja musi być autoryzowana do dołączenia do ręcznie rejestrując **Microsoft.ManagedServices** dostawcy zasobów. Klienta można zarejestrować subskrypcji, wykonując czynności opisane w temacie [dostawcy zasobów platformy Azure i ich typy](../../azure-resource-manager/resource-manager-supported-services.md).

Klienta można potwierdzić, czy subskrypcja jest gotowe do dołączenia w jednym z następujących sposobów.

### <a name="azure-portal"></a>Azure Portal

1. W witrynie Azure portal Wybierz subskrypcję.
1. Wybierz **dostawców zasobów**.
1. Upewnij się, że **Microsoft.ManagedServices** jest wyświetlany jako **zarejestrowanej**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Powinny zostać zwrócone wyniki podobne do następujących:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

Powinny zostać zwrócone wyniki podobne do następujących:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Definiowanie ról i uprawnień

Jako dostawca usług można użyć wiele ofert z jednego klienta, wymagających różny dostęp do różnych zakresów.

Aby ułatwić zarządzanie, zalecamy używanie grupy użytkowników usługi Azure AD dla każdej roli, co pozwala dodawać i usuwać pojedynczych użytkowników do grupy, a nie przypisywania uprawnień bezpośrednio do tego użytkownika. Można również przypisać role do nazwy głównej usługi. Należy koniecznie wykonaj zasadę najmniejszych uprawnień, aby użytkownicy mają tylko uprawnienia wymagane do wykonania swojej pracy, aby zmniejszyć prawdopodobieństwo przypadkowego błędy. Aby uzyskać więcej informacji, zobacz [zalecane rozwiązania w zakresie zabezpieczeń](../concepts/recommended-security-practices.md).

> [!NOTE]
> Przypisania ról, należy użyć kontroli dostępu opartej na rolach (RBAC) [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Wszystkie wbudowane role są obecnie obsługiwane za pomocą funkcji zarządzania usługi Azure resource delegowanego z wyjątkiem właściciela i wszystkie wbudowane role z [elementy DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) uprawnień. Wbudowana rola administratora dostępu użytkowników jest obsługiwana dla ograniczone zastosowanie, zgodnie z poniższym opisem. Role niestandardowe i [ról administratora klasyczni](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) również nie są obsługiwane.

Aby zdefiniować autoryzacji, należy znać identyfikatory dla każdego użytkownika, grupy użytkowników lub jednostki usługi, do którego chcesz udzielić dostępu. Należy także identyfikator definicji roli dla poszczególnych ról wbudowanych, które chcesz przypisać. Jeśli ich nie masz jeszcze, możesz pobrać je w jednym z następujących sposobów.



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
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

Aby dołączyć klienta, musisz utworzyć [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) szablon, który obejmuje następujące elementy:

|Pole  |Definicja  |
|---------|---------|
|**mspName**     |Nazwa dostawcy usługi         |
|**mspOfferDescription**     |Krótki opis oferty (na przykład "Contoso oferty maszyny Wirtualnej zarządzania")      |
|**managedByTenantId**     |Identyfikator dzierżawy         |
|**Autoryzacje**     |**PrincipalId** wartości dla użytkowników/grup/nazw SPN w dzierżawie, każdy z **principalIdDisplayName** ułatwiające zrozumienie przeznaczenia autoryzacji klienta i mapowane na wbudowane **roleDefinitionId** wartością do określenia poziomu dostępu         |

Dołączyć subskrypcji klienta, użyj odpowiedniego szablonu usługi Azure Resource Manager, które firma Microsoft zapewnia w naszym [przykłady repozytorium](https://github.com/Azure/Azure-Lighthouse-samples/), wraz z odpowiedniego pliku parametrów, który zmodyfikować tak, aby dopasować go do konfiguracji i zdefiniować usługi autoryzacje. Osobnymi szablonami znajdują się w zależności od tego, czy jesteś dołączania całej subskrypcji, grupy zasobów lub wiele grup zasobów w ramach subskrypcji. Oferujemy również szablon, który może służyć do klientów, którzy zakupili oferta zarządzana usługa, która została opublikowana w witrynie Azure Marketplace, jeśli chcesz dodać swoje subskrypcje w ten sposób.

|**Dołączyć to**  |**Użyj tego szablonu usługi Azure Resource Manager**  |**I zmodyfikuj parametr** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Wiele grup zasobów w ramach subskrypcji   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subskrypcja (przy użyciu oferty publikowane w witrynie Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Proces opisany w tym miejscu wymaga oddzielnych wdrożenia dla każdej subskrypcji, które są dołączone.
> 
> Oddzielnych wdrożeń są również wymagane, jeśli jesteś dołączanie do grupy wielu zasobów w ramach różnych subskrypcji. Jednak dołączania do wielu grup zasobów w ramach jednej subskrypcji może odbywać się w jedno wdrożenie.

W poniższym przykładzie pokazano zmodyfikowane **resourceProjection.parameters.json** pliku, który ma być używane do dołączenia subskrypcji. Pliki parametru grupy zasobów (znajdujący się w [rg delegowane zarządzania zasobami](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) folder) są podobne, ale także **rgName** parametru do identyfikacji grup określonego zasobu jako dołączone.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
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
Ostatnia autoryzacja w powyższym przykładzie dodaje **principalId** z rolą Administrator dostępu użytkowników (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Podczas przypisywania tej roli, należy uwzględnić **delegatedRoleDefinitionIds** właściwości i co najmniej jedną wbudowaną rolę. Utworzonego w tym autoryzacji użytkownika będzie można przypisać te role wbudowane do zarządzanych tożsamości. Należy pamiętać, że żadne inne uprawnienia, które zwykle są skojarzone z rolą Administrator dostępu użytkowników będzie stosować do tego użytkownika.

## <a name="deploy-the-azure-resource-manager-templates"></a>Wdrażanie szablonów usługi Azure Resource Manager

Po zaktualizowaniu pliku parametrów klienta należy wdrożyć szablon funkcji zarządzania zasobami w dzierżawie klienta jako wdrożenie poziomu subskrypcji. Dla każdej subskrypcji, którą chcesz dołączyć, zarządzanie zasobami platformy Azure delegowanego (lub dla każdej subskrypcji, która zawiera grupy zasobów, które chcesz dołączyć), wymagany jest wdrażane pojedynczo.

> [!IMPORTANT]
> Wdrożenie musi odbywać się przy użyciu konta innego niż Gość w dzierżawie klienta, który ma [wbudowana Rola właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) subskrypcji dołączanej (lub który zawiera grupy zasobów, które są dołączone).

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

Podczas subskrypcji klienta została pomyślnie dołączona do zarządzanie zasobami platformy Azure delegowanego, użytkowników w dzierżawie dostawcy usług będą mogli zobaczyć subskrypcji i jej zasobów (jeśli przyznano im dostęp do niego za pośrednictwem procesu powyżej, indywidualnie lub jako członek grupy usługi Azure AD z odpowiednimi uprawnieniami). Można to potwierdzić, sprawdź, upewnij się, że subskrypcja jest wyświetlana w jednym z następujących sposobów.  

### <a name="azure-portal"></a>Azure Portal

W dzierżawie dostawcy usług:

1. Przejdź do [strony Moje klientów](view-manage-customers.md).
2. Wybierz **klientów**.
3. Upewnij się, czy można wyświetlić subskrypcji o nazwie oferty udostępnionego w szablonie usługi Resource Manager.

W dzierżawie klienta:

1. Przejdź do [strony dostawców usługi](view-manage-service-providers.md).
2. Wybierz **oferty dostawcy usług**.
3. Upewnij się, czy można wyświetlić subskrypcji o nazwie oferty udostępnionego w szablonie usługi Resource Manager.

> [!NOTE]
> Może upłynąć kilka minut, po wdrożeniu aktualizacji są odzwierciedlane w witrynie Azure portal.

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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [międzydzierżawowa środowiska zarządzania](../concepts/cross-tenant-management-experience.md).
- [Wyświetlanie i zarządzanie nimi klienci](view-manage-customers.md) , przechodząc do **moich klientów** w witrynie Azure portal.
