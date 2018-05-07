---
title: Programowe tworzenie subskrypcji Azure Enterprise | Dokumenty Microsoft
description: Dowiedz się, jak utworzyć dodatkowe subskrypcji Azure Enterprise lub Enterprise i testowania programowo.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: 8d1eb3229f22b2da3a356562250fedb3c35c4816
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programowe tworzenie subskrypcji Azure Enterprise (wersja zapoznawcza)

Azure klientów na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), można utworzyć EA (MS-AZR - 0017 P) i subskrypcje EA: tworzenie i testowanie (MS-AZR - 0148 P) programowo. Aby dać innego użytkownika lub nazwy głównej usługi uprawnień do tworzenia subskrypcji rozliczane na koncie, nadaj im [kontroli dostępu opartej na rolach (RBAC)](../active-directory/role-based-access-control-configure.md) dostęp do Twojego konta rejestracji. 

> [!IMPORTANT]
> Umowy, pod którą uzyskano usług Microsoft Azure od firmy Microsoft lub autoryzowanego sprzedawcy zależą subskrypcji platformy Azure utworzona za pośrednictwem tego interfejsu API. Aby dowiedzieć się więcej, zobacz [informacje prawne Microsoft Azure](https://azure.microsoft.com/support/legal/).

W tym artykule umożliwi:

> [!div class="checklist"]
> * Dowiedz się, jak tworzyć subskrypcje programowo przy użyciu usługi Azure Resource Manager
> * Zrozumienie, jak użycie funkcji RBAC udostępnianie może tworzyć subskrypcje rozliczane na koncie Administrator przedsiębiorstwa

Zobacz też [.NET przykładowy kod w serwisie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Skontaktuj się z administratorem rejestracji EA dodać Cię jako właściciela konta

Aby rozpocząć, poproś administratora o rejestracji [dodał Cię jako właściciela konta przy użyciu portalu EA](https://ea.azure.com/helpdocs/addNewAccount) (logowania wymagane). Postępuj zgodnie z instrukcjami w wiadomości e-mail z zaproszeniem otrzymywanych ręcznie utworzyć subskrypcję początkowej.

> [!IMPORTANT]
> Musisz potwierdzić własność konta i ręcznie utworzyć subskrypcję EA początkowej przed przejściem do następnego kroku. Tylko dodanie konta do rejestracji jest niewystarczająca.

## <a name="find-accounts-you-have-access-to"></a>Znajdź kont, do których masz dostęp do

Po dodaniu w przypadku rejestracji Azure EA jako właściciel konta Azure używa relacji konta do rejestracji do określenia miejsca naliczania opłat subskrypcji. Można utworzyć subskrypcji, najpierw ustalić, jakiego konta rejestracji, musisz mieć dostęp do. Obecnie jesteś właścicielem konta Administrator przedsiębiorstwa, spróbuj użyć tego interfejsu API Azure sprawdza następujące warunki:

- Twoje konto został dodany do rejestracji EA
- Masz co najmniej jeden administrator przedsiębiorstwa lub EA: tworzenie i testowanie subskrypcji, co oznacza, że użytkownik został przeszli ręcznego tworzenia konta co najmniej raz
- Logujesz się do właściciela konta *katalogu macierzystego*, która jest subskrypcje domyślnie tworzone w katalogu

Jeśli powyższe dwa warunki są spełnione, `enrollmentAccount` zasobów jest zwracany i rozpoczęciem tworzenia subskrypcji w ramach tego konta. Wszystkie subskrypcje utworzone w ramach konta są rozliczane kierunku rejestracji EA, których konto.

# <a name="resttabrest"></a>[REST](#tab/rest)

Żądanie, aby wyświetlić listę wszystkich kont rejestracji:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Lista wszystkich kont rejestracji, do których masz dostęp do odpowiada Azure:

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

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Użyj [polecenia Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) do tworzenia listy wszystkich kont rejestracji użytkownik ma dostęp do.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure odpowiada listę identyfikatorów obiektów i ich adresy e-mail kont.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Użyj [az rozliczeń konta rejestracji listy](https://aka.ms/EASubCreationPublicPreviewCLI) polecenie, aby wyświetlić listę wszystkich kont rejestracji, musisz mieć dostęp do.

```azurecli-interactive 
az billing enrollment-account list
```
Azure odpowiada listę identyfikatorów obiektów i ich adresy e-mail kont.

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
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Użyj `principalName` właściwości, aby zidentyfikować konto, które mają subskrypcji będą naliczane opłaty do. Użyj `id` jako `enrollmentAccount` wartość, która umożliwia tworzenie subskrypcji w następnym kroku.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Utwórz subskrypcje w obszarze konta określonego rejestracji 

Poniższy przykład tworzy żądanie, aby utworzyć subskrypcję o nazwie *subskrypcji zespół deweloperów* i oferta subskrypcji jest *MS-AZR - 0017P* (regularne EA). Konto rejestracji jest `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (wartość symbolu zastępczego, jest to identyfikator GUID), które jest kontem rejestracji dla SignUpEngineering@contoso.com. On również opcjonalnie dodaje dwóch użytkowników jako właściciele RBAC dla subskrypcji.

# <a name="resttabrest"></a>[REST](#tab/rest)

Użyj `id` z `enrollmentAccount` ścieżka żądania, aby utworzyć subskrypcję.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| Nazwa elementu  | Wymagane | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nie      | Ciąg | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, jest ustawiona na nazwę oferty, takie jak "Microsoft Azure Enterprise."                                 |
| `offerType`   | Yes      | Ciąg | Oferta subskrypcji. Istnieją dwie opcje dla przedsiębiorstw [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (użycia w środowisku produkcyjnym) i [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Tworzenie/testowanie, musi być [włączona przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nie       | Ciąg | Identyfikator obiektu każdy użytkownik, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia.  |

W odpowiedzi, możesz wrócić `subscriptionOperation` obiektu monitorowania. Po zakończeniu tworzenia subskrypcji `subscriptionOperation` zwróci obiektu `subscriptionLink` obiektu, który ma identyfikator subskrypcji.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby użyć tego modułu w wersji zapoznawczej, zainstaluj ją, uruchamiając `Install-Module AzureRM.Subscription -AllowPrerelease` pierwszy. Aby upewnić się, `-AllowPrerelease` works, zainstaluj najnowszą wersję PowerShellGet z [Get PowerShellGet Module](/powershell/gallery/psget/get_psget_module).

Użyj [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) wraz z `enrollmentAccount` obiekt o identyfikatorze jako `EnrollmentAccountObjectId` parametr, aby utworzyć nową subskrypcję. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nazwa elementu  | Wymagane | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nie      | Ciąg | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, jest ustawiona na nazwę oferty, takie jak "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Yes      | Ciąg | Oferta subskrypcji. Istnieją dwie opcje dla przedsiębiorstw [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (użycia w środowisku produkcyjnym) i [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Tworzenie/testowanie, musi być [włączona przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Yes       | Ciąg | Identyfikator obiektu konta rejestracji, że subskrypcja jest tworzony w obszarze i rozliczony. Jest to wartość identyfikatora GUID, który można pobrać z `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Nie       | Ciąg | Identyfikator obiektu każdy użytkownik, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia.  |
| `OwnerSignInName`    | Nie       | Ciąg | Adres e-mail każdego użytkownika, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia. Można użyć tego parametru, zamiast `OwnerObjectId`.|
| `OwnerApplicationId` | Nie       | Ciąg | Identyfikator aplikacji nazwy głównej usługi, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia. Można użyć tego parametru, zamiast `OwnerObjectId`.| 

Aby zapoznać się z listą wszystkich parametrów, zobacz [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby użyć tego rozszerzenia w wersji zapoznawczej, zainstaluj ją, uruchamiając `az extension add --name subscription` pierwszy.

Użyj [Utwórz konto az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) wraz z `enrollmentAccount` obiekt o identyfikatorze jako `enrollment-account-object-id` parametr, aby utworzyć nową subskrypcję.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nazwa elementu  | Wymagane | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nie      | Ciąg | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, jest ustawiona na nazwę oferty, takie jak "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Yes      | Ciąg | Oferta subskrypcji. Istnieją dwie opcje dla przedsiębiorstw [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (użycia w środowisku produkcyjnym) i [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Tworzenie/testowanie, musi być [włączona przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Yes       | Ciąg | Identyfikator obiektu konta rejestracji, że subskrypcja jest tworzony w obszarze i rozliczony. Jest to wartość identyfikatora GUID, który można pobrać z `az billing enrollment-account list`. |
| `owner-object-id`      | Nie       | Ciąg | Identyfikator obiektu każdy użytkownik, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia.  |
| `owner-upn`    | Nie       | Ciąg | Adres e-mail każdego użytkownika, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia. Można użyć tego parametru, zamiast `owner-object-id`.|
| `owner-spn` | Nie       | Ciąg | Identyfikator aplikacji nazwy głównej usługi, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia. Można użyć tego parametru, zamiast `owner-object-id`.| 

Aby zapoznać się z listą wszystkich parametrów, zobacz [Utwórz konto az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegowanie dostępu do konta rejestracji przy użyciu funkcji RBAC

Aby zapewnić możliwość tworzenia subskrypcji do określonego konta innego użytkownika lub nazwy głównej usługi [nadanie im rolę właściciela RBAC w zakresie konta rejestracji](../active-directory/role-based-access-control-manage-access-rest.md). Poniższy przykład umożliwia w dzierżawie z `principalId` z `<userObjectId>` (dla SignUpEngineering@contoso.com) rolę właściciela konta rejestracji. 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
Rola właściciela został pomyślnie przypisany w zakresie rejestracji konta, Azure odpowie informacji przypisania roli:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj [AzureRmRoleAssignment nowy](../active-directory/role-based-access-control-manage-access-powershell.md) umożliwiają innego użytkownika właściciela dostęp do Twojego konta rejestracji.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj [utworzenia przypisania roli az](../active-directory/role-based-access-control-manage-access-azure-cli.md) umożliwiają innego użytkownika właściciela dostęp do Twojego konta rejestracji.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Gdy użytkownik staje się właścicielem RBAC dla Twojego konta rejestracji, można programowo utworzyć subskrypcje w nim. Utworzone przez użytkownika delegowanego subskrypcji nadal ma pierwotnego właściciela konta administratora usługi, ale również ma delegowane użytkownika jako właściciela domyślnie. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Inspekcji, który utworzył subskrypcji przy użyciu dzienników działania

Aby śledzić subskrypcjami za pośrednictwem tego interfejsu API, należy użyć [interfejs API dzierżawcy działania dziennika](/rest/api/monitor/tenantactivitylogs). Obecnie nie jest możliwe użycie programu PowerShell, interfejsu wiersza polecenia lub portalu Azure do śledzenia tworzenia subskrypcji.

1. Jako Administrator dzierżawy dzierżawy usługi Azure AD [podniesienie uprawnień dostępu](../active-directory/role-based-access-control-tenant-admin-access.md) następnie przypisać rolę czytelnika użytkownikowi inspekcji w zakresie `/providers/microsoft.insights/eventtypes/management`.
1. Jako użytkownik inspekcji, wywołaj [interfejs API dzierżawcy działania dziennika](/rest/api/monitor/tenantactivitylogs) wyświetlać działania tworzenia subskrypcji. Przykład:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Wygodny sposób wywołania tego interfejsu API z poziomu wiersza polecenia, spróbuj [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji Azure Enterprise

- Tylko subskrypcji Azure przedsiębiorstwa mogą być tworzone przy użyciu tego interfejsu API.
- Ma limitu 50 subskrypcji dla konta. Następnie można tworzyć przy użyciu Centrum konta tylko subskrypcji.
- Musi istnieć co najmniej jeden administrator przedsiębiorstwa lub EA: tworzenie i testowanie subskrypcji w ramach konta, co oznacza, że właściciel konta został przekazany przez ręcznego tworzenia konta, co najmniej raz.
- Użytkownicy, którzy nie są właścicielami konta, ale zostały dodane do konta rejestracji za pomocą RBAC, nie można utworzyć subskrypcji przy użyciu Centrum konta.
- Nie można wybrać dzierżawy dla subskrypcji mogą być tworzone w. Subskrypcja zawsze jest tworzony w domu dzierżawy właściciela konta. Aby przenieść subskrypcję do innej dzierżawy, zobacz [zmiana dzierżawy subskrypcji](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Kolejne kroki

* Na przykład dotyczące tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowy kod w serwisie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager i jej interfejsów API, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowania zasobów z grupami zarządzania Azure](management-groups-overview.md)
* Aby wyświetlić kompleksowe najlepszych rozwiązań dla dużych organizacji na Zarządzanie subskrypcją, zobacz [szkieletu Azure enterprise — ładu przetestowanego subskrypcji](resource-manager-subscription-governance.md)
