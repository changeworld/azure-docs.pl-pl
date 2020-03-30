---
title: Programowo twórz subskrypcje platformy Azure
description: Dowiedz się, jak programowo tworzyć dodatkowe subskrypcje platformy Azure.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460402"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programowo tworzenie subskrypcji platformy Azure (wersja zapoznawcza)

Klienci platformy Azure z kontem rozliczeniowym [umowy Enterprise Agreement (EA),](https://azure.microsoft.com/pricing/enterprise-agreement/) [umowy z klientami firmy Microsoft (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) lub [umowy z partnerami firmy Microsoft (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) mogą programowo tworzyć subskrypcje. W tym artykule dowiesz się, jak programowo tworzyć subskrypcje przy użyciu usługi Azure Resource Manager.

Podczas tworzenia subskrypcji platformy Azure programowo, że subskrypcja jest regulowana przez umowę, na mocy której uzyskano usługi platformy Azure od firmy Microsoft lub autoryzowanego sprzedawcy. Aby dowiedzieć się więcej, zobacz [Informacje prawne platformy Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Tworzenie subskrypcji dla konta rozliczeniowego EA

Użyj informacji w poniższych sekcjach, aby utworzyć subskrypcje EA.

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję, musisz mieć rolę właściciela na koncie rejestracji. Istnieją dwa sposoby, aby uzyskać rolę:

* Administrator przedsiębiorstwa rejestracji może [uczynić cię właścicielem konta](https://ea.azure.com/helpdocs/addNewAccount) (wymagane logowanie), co czyni Cię właścicielem konta rejestracji.

* Istniejący właściciel konta rejestracji może [udzielić ci dostępu](grant-access-to-create-subscription.md). Podobnie, jeśli chcesz użyć jednostki usługi do utworzenia subskrypcji EA, należy [przyznać tej jednostki usługi możliwość tworzenia subskrypcji.](grant-access-to-create-subscription.md)

### <a name="find-accounts-you-have-access-to"></a>Znajdź konta, do których masz dostęp

Po dodaniu do konta rejestracji skojarzonego z właścicielem konta platforma Azure używa relacji konto do rejestracji, aby określić, gdzie należy naliczać opłaty za subskrypcję. Wszystkie subskrypcje utworzone w ramach konta są rozliczane z rejestracją EA, w trakcie których znajduje się konto. Aby utworzyć subskrypcje, należy przekazać wartości dotyczące konta rejestracji i podmiotów użytkownika do posiadania subskrypcji.

Aby uruchomić następujące polecenia, musisz być zalogowany do *katalogu macierzystego*właściciela konta, który jest katalogiem, w którym subskrypcje są tworzone domyślnie.

### <a name="rest"></a>[Reszta](#tab/rest)

Poproś o wyświetlenie listy wszystkich kont rejestracji, do których masz dostęp:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Odpowiedź interfejsu API zawiera listę wszystkich kont rejestracji, do których masz dostęp:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Użyj `principalName` właściwości, aby zidentyfikować konto, na które mają być rozliczane subskrypcje. Skopiuj `name` to konto. Na przykład, jeśli chcesz utworzyć subskrypcje w SignUpEngineering@contoso.com ramach konta ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```rejestracji, należy skopiować . Ten identyfikator jest identyfikatorem obiektu konta rejestracji. Wklej tę wartość gdzieś, aby można było `enrollmentAccountObjectId`jej użyć w następnym kroku jako .

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Otwórz [usługę Azure Cloud Shell](https://shell.azure.com/) i wybierz pozycję PowerShell.

Polecenie cmdlet [Get-AzEnrollmentAccount służy](/powershell/module/az.billing/get-azenrollmentaccount) do listy wszystkich kont rejestracji, do których masz dostęp.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Platforma Azure odpowiada za pomocą listy kont rejestracji, do których masz dostęp:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Użyj `principalName` właściwości, aby zidentyfikować konto, na które mają być rozliczane subskrypcje. Skopiuj `ObjectId` to konto. Na przykład, jeśli chcesz utworzyć subskrypcje w SignUpEngineering@contoso.com ramach konta ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```rejestracji, należy skopiować . Wklej ten identyfikator obiektu gdzieś, aby można było go `enrollmentAccountObjectId`użyć w następnym kroku jako .

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [listy kont rejestracji rozliczeń az,](https://aka.ms/EASubCreationPublicPreviewCLI) aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp.

```azurecli-interactive
az billing enrollment-account list
```

Platforma Azure odpowiada za pomocą listy kont rejestracji, do których masz dostęp:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Użyj `principalName` właściwości, aby zidentyfikować konto, na które mają być rozliczane subskrypcje. Skopiuj `name` to konto. Na przykład, jeśli chcesz utworzyć subskrypcje w SignUpEngineering@contoso.com ramach konta ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```rejestracji, należy skopiować . Ten identyfikator jest identyfikatorem obiektu konta rejestracji. Wklej tę wartość gdzieś, aby można było `enrollmentAccountObjectId`jej użyć w następnym kroku jako .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Tworzenie subskrypcji na określonym koncie rejestracji

Poniższy przykład tworzy subskrypcję o nazwie *Dev Team Subscription* na koncie rejestracji wybranym w poprzednim kroku. Oferta subskrypcji to *MS-AZR-0017P* (zwykła umowa Microsoft Enterprise Agreement). Opcjonalnie dodaje również dwóch użytkowników jako właścicieli RBAC dla subskrypcji.

### <a name="rest"></a>[Reszta](#tab/rest)

Wykonaj następujące żądanie, zastępując wartość `<enrollmentAccountObjectId>` wartością `name` skopiowaną z pierwszego kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jeśli chcesz określić właścicieli, dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników.](grant-access-to-create-subscription.md#userObjectId)

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nie      | Ciąg | Wyświetlana nazwa subskrypcji. Jeśli nie zostanie określony, jest ustawiona na nazwę oferty, na przykład "Microsoft Azure Enterprise".                                 |
| `offerType`   | Tak      | Ciąg | Oferta abonamentu. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (zastosowanie produkcyjne) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, musi być [włączony za pomocą portalu EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `owners`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia.  |

W odpowiedzi można uzyskać `subscriptionOperation` z powrotem obiekt do monitorowania. Po zakończeniu tworzenia subskrypcji `subscriptionOperation` obiekt zwróci `subscriptionLink` obiekt, który ma identyfikator subskrypcji.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Najpierw zainstaluj ten moduł `Install-Module Az.Subscription -AllowPrerelease`podglądu, uruchamiając program . Aby upewnić się, że `-AllowPrerelease` działa, zainstaluj najnowszą wersję programu PowerShellGet z [modułu Get PowerShellGet](/powershell/scripting/gallery/installing-psget).

Uruchom polecenie [New-AzSubscription](/powershell/module/az.subscription) poniżej, zastępując `<enrollmentAccountObjectId>` `ObjectId` zebrane w```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```pierwszym kroku ( ). Jeśli chcesz określić właścicieli, dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników.](grant-access-to-create-subscription.md#userObjectId)

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nie      | Ciąg | Wyświetlana nazwa subskrypcji. Jeśli nie zostanie określony, jest ustawiona na nazwę oferty, na przykład "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Tak      | Ciąg | Oferta abonamentu. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (zastosowanie produkcyjne) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, musi być [włączony za pomocą portalu EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `EnrollmentAccountObjectId`      | Tak       | Ciąg | Identyfikator obiektu konta rejestracji, na które jest tworzona subskrypcja i na które są naliczane opłaty. Ta wartość jest identyfikatorem GUID, który można uzyskać z `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia.  |
| `OwnerSignInName`    | Nie       | Ciąg | Adres e-mail dowolnego użytkownika, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru `OwnerObjectId`można użyć zamiast pliku .|
| `OwnerApplicationId` | Nie       | Ciąg | Identyfikator aplikacji dowolnego podmiotu usługi, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru `OwnerObjectId`można użyć zamiast pliku . Podczas korzystania z tego parametru podmiot usługi musi mieć [dostęp do odczytu do katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Aby wyświetlić pełną listę wszystkich parametrów, zobacz [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw zainstaluj to rozszerzenie `az extension add --name subscription`podglądu, uruchamiając program .

Uruchom polecenie [tworzenia konta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) poniżej, zastępując `<enrollmentAccountObjectId>` `name` skopiowanym```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```w pierwszym kroku ( ). Jeśli chcesz określić właścicieli, dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników.](grant-access-to-create-subscription.md#userObjectId)

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nie      | Ciąg | Wyświetlana nazwa subskrypcji. Jeśli nie zostanie określony, jest ustawiona na nazwę oferty, na przykład "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Tak      | Ciąg | Oferta abonamentu. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (zastosowanie produkcyjne) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, musi być [włączony za pomocą portalu EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `enrollment-account-object-id`      | Tak       | Ciąg | Identyfikator obiektu konta rejestracji, na które jest tworzona subskrypcja i na które są naliczane opłaty. Ta wartość jest identyfikatorem GUID, który można uzyskać z `az billing enrollment-account list`. |
| `owner-object-id`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia.  |
| `owner-upn`    | Nie       | Ciąg | Adres e-mail dowolnego użytkownika, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru `owner-object-id`można użyć zamiast pliku .|
| `owner-spn` | Nie       | Ciąg | Identyfikator aplikacji dowolnego podmiotu usługi, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru `owner-object-id`można użyć zamiast pliku . Podczas korzystania z tego parametru podmiot usługi musi mieć [dostęp do odczytu do katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Aby wyświetlić pełną listę wszystkich parametrów, zobacz [tworzenie konta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji usługi Azure Enterprise

- Za pomocą tego interfejsu API można tworzyć tylko subskrypcje usługi Azure Enterprise.
- Istnieje limit 500 subskrypcji na konto rejestracji. Następnie więcej subskrypcji dla konta można utworzyć tylko w witrynie Azure portal. Jeśli chcesz utworzyć więcej subskrypcji za pośrednictwem interfejsu API, utwórz inne konto rejestracji.
- Użytkownicy, którzy nie są właścicielami kont, ale zostali dodani do konta rejestracji za pośrednictwem rbac, nie mogą tworzyć subskrypcji w witrynie Azure portal.
- Nie można wybrać dzierżawy dla subskrypcji, która ma zostać utworzona w. Subskrypcja jest zawsze tworzona w dzierżawie domowej właściciela konta. Aby przenieść subskrypcję do innej dzierżawy, zobacz [zmień dzierżawę subskrypcji](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Tworzenie subskrypcji dla konta MCA

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcje, musisz mieć rolę właściciela, współautora lub twórcy subskrypcji platformy Azure w sekcji faktury lub roli właściciela lub współautora w profilu rozliczeniowym lub na koncie rozliczeniowym. Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

W przykładzie pokazanym poniżej użyto interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Znajdowanie kont rozliczeniowych, do których masz dostęp

Zgłać poniższe żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę kont rozliczeniowych, do których masz dostęp.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Użyj `displayName` właściwości, aby zidentyfikować konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że agreeementType konta jest *MicrosoftCustomerAgreement*. Skopiuj `name` konto.  Na przykład, jeśli chcesz utworzyć subskrypcję dla konta rozliczeniowego, `Contoso` skopiuj `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`program . Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Znajdowanie sekcji faktur w celu tworzenia subskrypcji

Opłaty za subskrypcję są wyświetlane w sekcji faktury profilu rozliczeniowego. Użyj następującego interfejsu API, aby uzyskać listę sekcji faktur i profilów rozliczeń, na których masz uprawnienia do tworzenia subskrypcji platformy Azure.

Wykonaj następujące żądanie, zastępując wartość `<billingAccountName>` wartością `name` skopiowaną z pierwszego kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę wszystkich sekcji faktur i ich profili rozliczeniowych, na których masz dostęp do tworzenia subskrypcji:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Użyj `invoiceSectionDisplayName` właściwości, aby zidentyfikować sekcję faktury, dla której chcesz utworzyć subskrypcje. Skopiuj sekcję `invoiceSectionId` `billingProfileId` `skuId` , i jedną z sekcji faktury. Na przykład, jeśli chcesz utworzyć subskrypcję `Microsoft Azure plan` typu dla `Development` sekcji `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`faktury, należy skopiować , `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` i `0001`. Wklej te wartości w miejscu, aby można było ich użyć w następnym kroku.

### <a name="create-a-subscription-for-an-invoice-section"></a>Tworzenie subskrypcji dla sekcji faktury

Poniższy przykład tworzy subskrypcję o nazwie *Subskrypcja zespołu deweloperów* typu *Microsoft Azure Plan* dla sekcji *faktury dewelopera.* Subskrypcja zostanie naliczona do profilu *rozliczeniowego contoso finance* i pojawi się w sekcji *Rozwoju* faktury.

Złożyć następujące żądanie, `<invoiceSectionId>` `invoiceSectionId` zastępując skopiowanym z```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```drugiego kroku ( ). Należy przekazać `billingProfileId` i `skuId` skopiować z drugiego kroku w parametrach żądania interfejsu API. Jeśli chcesz określić właścicieli, dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników.](grant-access-to-create-subscription.md#userObjectId)

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Tak      | Ciąg | Wyświetlana nazwa subskrypcji.|
| `billingProfileId`   | Tak      | Ciąg | Identyfikator profilu rozliczeniowego, który będzie rozliczany z opłat za subskrypcję.  |
| `skuId` | Tak      | Ciąg | Identyfikator sku, który określa typ planu platformy Azure. |
| `owners`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika lub jednostki usługi, który chcesz dodać jako właściciela RBAC w ramach subskrypcji podczas jej tworzenia.  |
| `costCenter` | Nie      | Ciąg | Centrum kosztów skojarzone z subskrypcją. Pojawia się w pliku csv użycia. |
| `managementGroupId` | Nie      | Ciąg | Identyfikator grupy zarządzania, do której zostanie dodana subskrypcja. Aby uzyskać listę grup zarządzania, zobacz [Grupy zarządzania — Lista interfejsu API](/rest/api/resources/managementgroups/list). Użyj identyfikatora grupy zarządzania z interfejsu API. |

W odpowiedzi można uzyskać `subscriptionCreationResult` z powrotem obiekt do monitorowania. Po zakończeniu tworzenia subskrypcji `subscriptionCreationResult` obiekt zwróci `subscriptionLink` obiekt, który ma identyfikator subskrypcji.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Tworzenie subskrypcji dla konta rozliczeniowego mpa

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję konta rozliczeniowego, musisz mieć rolę administratora globalnego lub agenta administracyjnego na koncie dostawcy rozwiązań w chmurze w organizacji. Aby uzyskać więcej informacji, zobacz [Centrum partnerów — przypisywanie ról i uprawnień użytkowników](https://docs.microsoft.com/partner-center/permissions-overview).

W przykładzie pokazanym poniżej użyto interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Znajdź konta rozliczeniowe, do których masz dostęp

Zgłoś poniższe żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych, do których masz dostęp.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę kont rozliczeniowych.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Użyj `displayName` właściwości, aby zidentyfikować konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że agreeementType konta jest *MicrosoftPartnerAgreement*. Skopiuj `name` dla konta. Na przykład, jeśli chcesz utworzyć subskrypcję dla konta rozliczeniowego, `Contoso` skopiuj `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`program . Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="find-customers-that-have-azure-plans"></a>Znajdowanie klientów, którzy mają plany platformy Azure

Złóż następujące żądanie, `<billingAccountName>` `name` zastępując skopiowanym z```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```pierwszego kroku ( ), aby wyświetlić listę wszystkich klientów na koncie rozliczeniowym, dla których można utworzyć subskrypcje platformy Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę klientów na koncie rozliczeniowym z planami platformy Azure. Można utworzyć subskrypcje dla tych klientów.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Użyj `displayName` właściwości, aby zidentyfikować odbiorcę, dla którego chcesz utworzyć subskrypcje. Skopiuj `id` dla klienta. Na przykład, jeśli chcesz utworzyć `Fabrikam toys`subskrypcję dla `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`, chcesz skopiować . Wklej tę wartość gdzieś, aby użyć jej w kolejnych krokach.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcjonalnie dla dostawców pośrednich: Pobierz odsprzedawców dla klienta

Jeśli jesteś dostawcą pośrednim w modelu dwuwarstwowym dostawcy usług kryptograficznych, możesz określić sprzedawcę podczas tworzenia subskrypcji dla klientów.

Złóż następujące żądanie, `<customerId>` `id` zastępując skopiowanym z```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```drugiego kroku ( ), aby wyświetlić listę wszystkich sprzedawców, którzy są dostępni dla odbiorcy.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę odsprzedawców dla klienta:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Użyj `description` właściwości, aby zidentyfikować sprzedawcę, który będzie skojarzony z subskrypcją. Skopiuj `resellerId` dla sprzedawcy. Na przykład, jeśli chcesz `Wingtip`skojarzyć `3xxxxx`, chcesz skopiować . Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="create-a-subscription-for-a-customer"></a>Tworzenie subskrypcji dla klienta

Poniższy przykład tworzy subskrypcję o nazwie *Dev Team subskrypcji* dla *zabawek Fabrikam* i skojarzyć odsprzedawcy *Wingtip* do subskrypcji. T

Złożyć następujące żądanie, `<customerId>` `id` zastępując skopiowanym z```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```drugiego kroku ( ). Przekaż *opcjonalny resellerId* skopiowany z drugiego kroku w parametrach żądania interfejsu API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Tak      | Ciąg | Wyświetlana nazwa subskrypcji.|
| `skuId` | Tak      | Ciąg | Identyfikator sku planu platformy Azure. Użyj *0001* dla subskrypcji typu Plan platformy Microsoft Azure |
| `resellerId`      | Nie       | Ciąg | Identyfikator MPN sprzedawcy, który będzie skojarzony z subskrypcją.  |

W odpowiedzi można uzyskać `subscriptionCreationResult` z powrotem obiekt do monitorowania. Po zakończeniu tworzenia subskrypcji `subscriptionCreationResult` obiekt zwróci `subscriptionLink` obiekt, który ma identyfikator subskrypcji.

## <a name="next-steps"></a>Następne kroki

* Na przykład podczas tworzenia subskrypcji umowy Enterprise Agreement (EA) przy użyciu platformy .NET można znaleźć [przykładowy kod w usłudze GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Po utworzeniu subskrypcji można przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Udzielanie dostępu do tworzenia subskrypcji platformy Azure Enterprise (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej o zarządzaniu dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [Organizowanie zasobów za pomocą grup zarządzania platformy Azure](../../governance/management-groups/overview.md)
