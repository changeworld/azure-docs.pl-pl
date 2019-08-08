---
title: Programowe tworzenie subskrypcji platformy Azure Enterprise | Microsoft Docs
description: Dowiedz się, jak programowo utworzyć dodatkowe subskrypcje tworzenia i testowania platformy Azure Enterprise lub Enterprise.
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: d6ae863aed629f5f5b1497d5a6e0f8108f4703c8
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848710"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programowe tworzenie subskrypcji platformy Azure Enterprise (wersja zapoznawcza)

Jako klient platformy Azure w [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)można programowo utworzyć subskrypcje EA (MS-AZR-0017P) i EA Dev/Test (MS-AZR-0148P). W tym artykule dowiesz się, jak tworzyć subskrypcje programowo przy użyciu Azure Resource Manager.

Po utworzeniu subskrypcji platformy Azure na podstawie tego interfejsu API ta subskrypcja podlega postanowieniom umowy, na podstawie której uzyskano Microsoft Azure usług od firmy Microsoft lub od autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [Microsoft Azure informacje prawne](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć rolę właściciela na koncie rejestracji, w ramach którego chcesz utworzyć subskrypcje. Istnieją dwa sposoby uzyskania tych ról:

* Administrator rejestracji może należeć do [właściciela konta](https://ea.azure.com/helpdocs/addNewAccount) (wymagane jest zalogowanie), co umożliwia właścicielowi konta rejestracji. Postępuj zgodnie z instrukcjami w otrzymanej wiadomości e-mail z zaproszeniem, aby ręcznie utworzyć początkową subskrypcję. Potwierdź własność konta i ręcznie Utwórz początkową subskrypcję EA przed przejściem do następnego kroku. Po prostu dodanie konta do rejestracji nie jest wystarczające.

* Istniejący właściciel konta rejestracji może [udzielić Ci dostępu](grant-access-to-create-subscription.md). Podobnie, jeśli chcesz użyć nazwy głównej usługi do utworzenia subskrypcji EA, należy [przyznać tej jednostce usługi możliwość tworzenia subskrypcji](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Znajdź konta, do których masz dostęp

Po dodaniu do rejestracji w ramach umowy EA platformy Azure jako właściciela konta platforma Azure korzysta z relacji konto-do-Rejestracja, aby określić, gdzie należy rozliczać opłaty za subskrypcję. Wszystkie subskrypcje utworzone w ramach konta są rozliczane w ramach rejestracji w ramach umowy EA, w której znajduje się konto. Aby utworzyć subskrypcje, należy przekazać wartości z konta rejestracji i podmiotów zabezpieczeń użytkowników do subskrypcji. 

Aby uruchomić następujące polecenia, należy zalogować się do *katalogu macierzystego*właściciela konta, który jest katalogiem, w którym domyślnie są tworzone subskrypcje.

## <a name="resttabrest"></a>[REST](#tab/rest)

Żądaj, aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Platforma Azure reaguje na listę wszystkich kont rejestracji, do których masz dostęp:

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

`principalName` Użyj właściwości, aby zidentyfikować konto, na które mają być naliczane opłaty. Skopiuj to `name` konto. Jeśli na przykład chcesz utworzyć subskrypcje na SignUpEngineering@contoso.com koncie rejestracji, skopiujesz. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Ten identyfikator jest IDENTYFIKATORem obiektu konta rejestracji. Wklej tę wartość w miejscu, aby można było użyć jej w następnym kroku jako `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Otwórz [Azure Cloud Shell](https://shell.azure.com/) i wybierz pozycję PowerShell.

Użyj polecenia cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) , aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Platforma Azure reaguje na listę kont rejestracji, do których masz dostęp:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` Użyj właściwości, aby zidentyfikować konto, na które mają być naliczane opłaty. Skopiuj to `ObjectId` konto. Jeśli na przykład chcesz utworzyć subskrypcje na SignUpEngineering@contoso.com koncie rejestracji, skopiujesz. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Wklej ten identyfikator obiektu w miejscu, aby można było go użyć w następnym kroku jako `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp, użyj polecenia [AZ Rozlicz rejestrację konta](https://aka.ms/EASubCreationPublicPreviewCLI) .

```azurecli-interactive 
az billing enrollment-account list
```

Platforma Azure reaguje na listę kont rejestracji, do których masz dostęp:

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

`principalName` Użyj właściwości, aby zidentyfikować konto, na które mają być naliczane opłaty. Skopiuj to `name` konto. Jeśli na przykład chcesz utworzyć subskrypcje na SignUpEngineering@contoso.com koncie rejestracji, skopiujesz. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Ten identyfikator jest IDENTYFIKATORem obiektu konta rejestracji. Wklej tę wartość w miejscu, aby można było użyć jej w następnym kroku jako `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Tworzenie subskrypcji przy użyciu określonego konta rejestracji

Poniższy przykład tworzy subskrypcję o nazwie *Dev Team Subscription* na koncie rejestracji wybranym w poprzednim kroku. Oferta subskrypcji to *MS-AZR-0017P* (zwykła Umowa Enterprise firmy Microsoft). Opcjonalnie dodatkowo dodaje dwóch użytkowników jako właścicieli RBAC dla subskrypcji.

# <a name="resttabrest"></a>[REST](#tab/rest)

Wykonaj następujące żądanie, zastępując `<enrollmentAccountObjectId>` je `name` skopiowanymi z pierwszego kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jeśli chcesz określić właścicieli, Dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

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

| Nazwa elementu  | Wymagane | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nie      | String | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, zostanie ona ustawiona na nazwę oferty, np. "Microsoft Azure Enterprise".                                 |
| `offerType`   | Yes      | String | Oferta subskrypcji. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Product Use) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muszą być [włączone przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nie       | String | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia.  |

W odpowiedzi otrzymujesz `subscriptionOperation` obiekt do monitorowania. Po zakończeniu `subscriptionOperation` tworzenia subskrypcji obiekt `subscriptionLink` zwróci obiekt, który ma identyfikator subskrypcji.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw zainstaluj ten moduł w wersji zapoznawczej, uruchamiając `Install-Module Az.Subscription -AllowPrerelease`polecenie. Aby upewnić `-AllowPrerelease` się, że działa, zainstaluj najnowszą wersję programu PowerShellGet z [modułu Get PowerShellGet](/powershell/gallery/installing-psget).

Uruchom polecenie [New-AzSubscription](/powershell/module/az.subscription) poniżej, zastępując `<enrollmentAccountObjectId>` je `ObjectId` zebranym w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jeśli chcesz określić właścicieli, Dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nazwa elementu  | Wymagane | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nie      | Ciąg | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, zostanie ona ustawiona na nazwę oferty, np. "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Tak      | String | Oferta subskrypcji. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Product Use) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muszą być [włączone przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Tak       | String | Identyfikator obiektu konta rejestracji, w ramach którego utworzono subskrypcję. Ta wartość jest identyfikatorem GUID, z `Get-AzEnrollmentAccount`którego otrzymujesz. |
| `OwnerObjectId`      | Nie       | String | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia.  |
| `OwnerSignInName`    | Nie       | String | Adres e-mail dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `OwnerObjectId`.|
| `OwnerApplicationId` | Nie       | Ciąg | Identyfikator aplikacji dowolnej jednostki usługi, która ma zostać dodana jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `OwnerObjectId`. W przypadku korzystania z tego parametru jednostka usługi musi mieć [dostęp do odczytu do katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Aby wyświetlić pełną listę wszystkich parametrów, zobacz polecenie [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw zainstaluj to rozszerzenie w wersji zapoznawczej, uruchamiając `az extension add --name subscription`polecenie.

Uruchom polecenie [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) poniżej, zastępując `<enrollmentAccountObjectId>` je `name` skopiowanymi w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jeśli chcesz określić właścicieli, Dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nazwa elementu  | Wymagane | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nie      | String | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, zostanie ona ustawiona na nazwę oferty, np. "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Tak      | Ciąg | Oferta subskrypcji. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Product Use) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muszą być [włączone przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Yes       | String | Identyfikator obiektu konta rejestracji, w ramach którego utworzono subskrypcję. Ta wartość jest identyfikatorem GUID, z `az billing enrollment-account list`którego otrzymujesz. |
| `owner-object-id`      | Nie       | String | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia.  |
| `owner-upn`    | Nie       | Ciąg | Adres e-mail dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `owner-object-id`.|
| `owner-spn` | Nie       | String | Identyfikator aplikacji dowolnej jednostki usługi, która ma zostać dodana jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `owner-object-id`. W przypadku korzystania z tego parametru jednostka usługi musi mieć [dostęp do odczytu do katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Aby wyświetlić pełną listę wszystkich parametrów, zobacz [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji przedsiębiorstwa platformy Azure

- Za pomocą tego interfejsu API można tworzyć tylko subskrypcje platformy Azure Enterprise.
- Istnieje limit 200 subskrypcji na konto rejestracji. Następnie więcej subskrypcji dla konta można utworzyć tylko za pomocą centrum konta. Jeśli chcesz utworzyć więcej subskrypcji za pomocą interfejsu API, Utwórz inne konto rejestracji.
- Użytkownicy niebędący właścicielami kont, którzy zostali dodani do konta rejestracji za pośrednictwem RBAC, nie mogą tworzyć subskrypcji przy użyciu centrum konta.
- Nie można wybrać dzierżawy, w której ma zostać utworzona subskrypcja. Subskrypcja jest zawsze tworzona w dzierżawie głównej właściciela konta. Aby przenieść subskrypcję do innej dzierżawy, zobacz [zmiana subskrypcji dzierżawy](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładem tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowy kod w witrynie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i podmiotom usługi. Aby uzyskać więcej informacji, zobacz [udzielanie dostępu do tworzenia subskrypcji platformy Azure Enterprise (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowanie zasobów za pomocą grup zarządzania platformy Azure](management-groups-overview.md) .
