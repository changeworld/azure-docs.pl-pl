---
title: Dodawanie ról aplikacji i wyładowywanych z tokenu | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dodać role aplikacji w aplikacji zarejestrowanej w usłudze Azure Active `roles` Directory, przypisać użytkowników i grupy do tych ról i otrzymać je w żądaniu w tokenie.
services: active-directory
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 144fad249011d547ac6a8cf2d404cb3f8fe74f96
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884260"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Jak: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie

Kontrola dostępu oparta na rolach (RBAC) to popularny mechanizm wymuszania autoryzacji w aplikacjach. Podczas korzystania z funkcji RBAC administrator udziela uprawnień do ról, a nie do poszczególnych użytkowników lub grup. Administrator może następnie przypisać role do różnych użytkowników i grup, aby kontrolować, kto ma dostęp do jakiej zawartości i funkcji.

Za pomocą RBAC z ról aplikacji i oświadczeń ról, deweloperzy mogą bezpiecznie wymusić autoryzacji w swoich aplikacjach przy niewielkim wysiłku z ich strony.

Innym podejściem jest użycie grup usługi Azure AD i oświadczeń grup, jak pokazano w [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Grupy usługi Azure AD i role aplikacji w żadnym wypadku nie wykluczają się wzajemnie; mogą być używane w tandemie, aby zapewnić jeszcze lepszą kontrolę dostępu.

## <a name="declare-roles-for-an-application"></a>Deklarowanie ról dla aplikacji

Te role aplikacji są zdefiniowane w [witrynie Azure portal](https://portal.azure.com) w manifeście rejestracji aplikacji.  Gdy użytkownik loguje się do aplikacji, `roles` usługa Azure AD emituje oświadczenie dla każdej roli, która została przyznana użytkownikowi indywidualnie użytkownikowi i z członkostwa w grupie.  Przypisywanie użytkowników i grup do ról można wykonać za pośrednictwem interfejsu użytkownika portalu lub programowo przy użyciu [programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarowanie ról aplikacji przy użyciu witryny Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu.
1. Na liście **Ulubione** lub **Wszystkie katalogi** wybierz dzierżawę usługi Active Directory, w której chcesz zarejestrować aplikację.
1. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W okienku **usługi Azure Active Directory** wybierz **pozycję Rejestracje aplikacji,** aby wyświetlić listę wszystkich aplikacji.
1. Wybierz aplikację, w której chcesz zdefiniować role aplikacji. Następnie wybierz **pozycję Manifest**.
1. Edytuj manifest aplikacji, lokalizując `appRoles` to ustawienie i dodając wszystkie role aplikacji.

     > [!NOTE]
     > Każda definicja roli aplikacji w tym manifeście musi mieć inny `id` prawidłowy identyfikator GUID w kontekście manifestu dla właściwości.
     >
     > Właściwość `value` każdej definicji roli aplikacji powinna dokładnie odpowiadać ciągom, które są używane w kodzie w aplikacji. Właściwość `value` nie może zawierać spacji. Jeśli tak, podczas zapisywania manifestu zostanie wyświetlony błąd.

1. Zapisz manifest.

### <a name="examples"></a>Przykłady

W poniższym `appRoles` przykładzie przedstawiono, do `users`którego można przypisać program .

> [!NOTE]
>Musi `id` być unikatowy identyfikator GUID.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>Nie `displayName` może zawierać spacji.

Można zdefiniować role `users`aplikacji `applications`do docelowego , lub obu. Jeśli są `applications`dostępne, role aplikacji są wyświetlane jako uprawnienia aplikacji w bloku **Wymagane uprawnienia.** W poniższym przykładzie przedstawiono `Application`rolę aplikacji ukierunkowaną na plik .

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Liczba zdefiniowanych ról wpływa na limity, które ma manifest aplikacji. Zostały one szczegółowo omówione na stronie [limitów manifestu.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits)

### <a name="assign-users-and-groups-to-roles"></a>Przypisywanie użytkowników i grup do ról

Po dodaniu ról aplikacji w aplikacji można przypisać użytkowników i grupy do tych ról.

1. W okienku **usługi Azure Active Directory** wybierz **aplikacje enterprise** z menu nawigacji po lewej stronie usługi Azure Active **Directory.**
1. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

     Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj różnych filtrów u góry listy **Wszystkie aplikacje,** aby ograniczyć listę lub przewiń listę w dół, aby zlokalizować aplikację.

1. Wybierz aplikację, w której chcesz przypisać użytkowników lub grupę zabezpieczeń do ról.
1. Wybierz okienko **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.
1. U góry listy **Użytkownicy i grupy** wybierz przycisk Dodaj **użytkownika,** aby otworzyć okienko **Dodaj przypisanie.**
1. Wybierz selektor **Użytkownicy i grupy** z okienka **Dodaj przypisanie.**

     Zostanie wyświetlona lista użytkowników i grup zabezpieczeń wraz z polem tekstowym do wyszukiwania i lokalizowania określonego użytkownika lub grupy. Ten ekran umożliwia wybranie wielu użytkowników i grup za jednym zamachem.

1. Po wybraniu użytkowników i grup naciśnij przycisk **Wybierz** na dole, aby przejść do następnej części.
1. Wybierz selektor **wybierz rolę** z okienka **Dodaj przypisanie.** Wszystkie role zadeklarowane wcześniej w manifeście aplikacji zostaną wyświetlene.
1. Wybierz rolę i naciśnij przycisk **Wybierz.**
1. Naciśnij przycisk **Przypisz** u dołu, aby zakończyć przypisania użytkowników i grup do aplikacji.
1. Upewnij się, że dodawani użytkownicy i grupy są wyświetlane na zaktualizowanej liście **Użytkownicy i grupy.**

## <a name="more-information"></a>Więcej informacji

- [Dodawanie autoryzacji przy użyciu ról aplikacji & oświadczeń ról do aplikacji sieci web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Korzystanie z grup zabezpieczeń i ról aplikacji w aplikacjach (wideo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Usługa Azure Active Directory z oświadczeń grupowych i ról aplikacji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifest aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Tokeny dostępu AAD](access-tokens.md)
- [Aad`id_tokens`](id-tokens.md)
