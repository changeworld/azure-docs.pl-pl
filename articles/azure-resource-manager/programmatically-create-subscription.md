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
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: df66ba1ec2c855a24731387210b0127892f5796f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234788"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programowe tworzenie subskrypcji Azure Enterprise (wersja zapoznawcza)

Azure klientów na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), można utworzyć EA (MS-AZR - 0017 P) i subskrypcje EA: tworzenie i testowanie (MS-AZR - 0148 P) programowo. W tym artykule Dowiedz się jak tworzyć subskrypcje programowo przy użyciu usługi Azure Resource Manager.

Po utworzeniu subskrypcji platformy Azure z tego interfejsu API subskrypcji podlega warunkom umowy, pod którym usług Microsoft Azure został uzyskany od firmy Microsoft lub autoryzowanego sprzedawcy. Aby dowiedzieć się więcej, zobacz [informacje prawne Microsoft Azure](https://azure.microsoft.com/support/legal/).

## <a name="prerequisites"></a>Wymagania wstępne

* Twoje konto musi być właścicielem konta w rejestracji Azure EA. Jeśli nie, poproś administratora o rejestracji [dodał Cię jako właściciela konta przy użyciu portalu EA](https://ea.azure.com/helpdocs/addNewAccount) (logowania wymagane). Postępuj zgodnie z instrukcjami w wiadomości e-mail z zaproszeniem otrzymywanych ręcznie utworzyć subskrypcję początkowej. Potwierdź właściciela konta, a następnie utwórz ręcznie subskrypcji EA początkowej przed przejściem do następnego kroku. Wystarczy dodać konto do rejestracji jest niewystarczające.

* Jeśli chcesz użyć nazwy głównej usługi, aby utworzyć subskrypcję EA, należy najpierw [udzielić tej nazwy głównej usługi może tworzyć subskrypcje](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Znajdź kont, do których masz dostęp do

Po dodaniu w przypadku rejestracji Azure EA jako właściciel konta Azure używa relacji konta do rejestracji do określenia miejsca naliczania opłat subskrypcji. Wszystkie subskrypcje utworzone w ramach konta są rozliczane kierunku rejestracji EA, których konto. Aby tworzyć subskrypcje, należy podać wartości o konta rejestracji i podmiotów zabezpieczeń użytkownika do własnej subskrypcji. 

Aby uruchomić następujące polecenia, należy być zalogowanym do właściciela konta *katalogu macierzystego*, która jest subskrypcje domyślnie tworzone w katalogu.

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

Poniższy przykład tworzy żądanie, aby utworzyć subskrypcję o nazwie *subskrypcji zespół deweloperów* i oferta subskrypcji jest *MS-AZR - 0017P* (regularne EA). Konto rejestracji jest `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (wartość symbolu zastępczego, ta wartość jest identyfikatorem GUID), który jest konto rejestracji dla SignUpEngineering@contoso.com. On również opcjonalnie dodaje dwóch użytkowników jako właściciele RBAC dla subskrypcji.

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

Aby użyć tego modułu w wersji zapoznawczej, zainstaluj ją, uruchamiając `Install-Module AzureRM.Subscription -AllowPrerelease` pierwszy. Aby upewnić się, `-AllowPrerelease` works, zainstaluj najnowszą wersję PowerShellGet z [Get PowerShellGet Module](/powershell/gallery/installing-psget).

Użyj [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) wraz z `enrollmentAccount` obiekt o identyfikatorze jako `EnrollmentAccountObjectId` parametr, aby utworzyć nową subskrypcję. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nazwa elementu  | Wymagane | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nie      | Ciąg | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, jest ustawiona na nazwę oferty, takie jak "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Yes      | Ciąg | Oferta subskrypcji. Istnieją dwie opcje dla przedsiębiorstw [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (użycia w środowisku produkcyjnym) i [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Tworzenie/testowanie, musi być [włączona przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Yes       | Ciąg | Identyfikator obiektu konta rejestracji, że subskrypcja jest tworzony w obszarze i rozliczony. Ta wartość jest identyfikatorem GUID, który można pobrać z `Get-AzureRmEnrollmentAccount`. |
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
| `enrollment-account-object-id`      | Yes       | Ciąg | Identyfikator obiektu konta rejestracji, że subskrypcja jest tworzony w obszarze i rozliczony. Ta wartość jest identyfikatorem GUID, który można pobrać z `az billing enrollment-account list`. |
| `owner-object-id`      | Nie       | Ciąg | Identyfikator obiektu każdy użytkownik, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia.  |
| `owner-upn`    | Nie       | Ciąg | Adres e-mail każdego użytkownika, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia. Można użyć tego parametru, zamiast `owner-object-id`.|
| `owner-spn` | Nie       | Ciąg | Identyfikator aplikacji nazwy głównej usługi, który chcesz dodać jako właściciela RBAC subskrypcji podczas jego tworzenia. Można użyć tego parametru, zamiast `owner-object-id`.| 

Aby zapoznać się z listą wszystkich parametrów, zobacz [Utwórz konto az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji Azure Enterprise

- Tylko subskrypcji Azure przedsiębiorstwa mogą być tworzone przy użyciu tego interfejsu API.
- Ma limitu 50 subskrypcji dla konta. Następnie można tworzyć przy użyciu Centrum konta tylko subskrypcji.
- Musi istnieć co najmniej jeden administrator przedsiębiorstwa lub EA: tworzenie i testowanie subskrypcji w ramach konta, co oznacza, że właściciel konta został przekazany przez ręcznego tworzenia konta, co najmniej raz.
- Użytkownicy, którzy nie są właścicielami konta, ale zostały dodane do konta rejestracji za pomocą RBAC, nie można utworzyć subskrypcji przy użyciu Centrum konta.
- Nie można wybrać dzierżawy dla subskrypcji mogą być tworzone w. Subskrypcja zawsze jest tworzony w domu dzierżawy właściciela konta. Aby przenieść subskrypcję do innej dzierżawy, zobacz [zmiana dzierżawy subskrypcji](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Kolejne kroki

* Na przykład dotyczące tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowy kod w serwisie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teraz, po utworzeniu subskrypcji można przyznać zdolność do innych użytkowników i nazwy główne usług. Aby uzyskać więcej informacji, zobacz [udzielić dostępu do utworzenia subskrypcji Azure Enterprise (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowania zasobów z grupami zarządzania Azure](management-groups-overview.md)
