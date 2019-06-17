---
title: Jak dodać role aplikacji w aplikacji zarejestrowanych w usłudze Azure Active Directory i odbierać je w tokenie
description: Dowiedz się, jak dodać role aplikacji w aplikacji zarejestrowanych w usłudze Azure Active Directory, Przypisz użytkowników i grup do tych ról i odbierać je w `roles` oświadczenia w tokenie.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24e933399454942f4ee50440cffd791599679074
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299148"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Instrukcje: Dodawanie ról aplikacji w aplikacji i odbierać je w tokenie

Kontrola dostępu oparta na rolach (RBAC) to mechanizm popularne, aby wymusić autoryzację w aplikacji. Korzystając z funkcji RBAC, administrator przyznaje uprawnienia do ról, a nie poszczególnych użytkowników lub grup. Administrator może następnie przypisać role do różnych użytkowników i grup w celu kontrolowania, kto ma dostęp do jakich funkcji i zawartości.

Przy użyciu RBAC, role aplikacji i oświadczeń roli, deweloperzy bezpiecznie wymusić autoryzacji w swoich aplikacjach, przy minimalnym nakładzie pracy na ich stronie.

Innym podejściem jest używać grup usługi Azure AD i oświadczenia grupy, jak pokazano na [aplikacji sieci Web-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Azure AD grup i ról aplikacji w żadnym wypadku nie wykluczają się wzajemnie; one może służyć w połączeniu sekcją do zapewnienia jeszcze bardziej precyzyjną kontrolę dostępu do szczegółowych.

## <a name="declare-roles-for-an-application"></a>Zadeklaruj ról aplikacji

Te role aplikacji są definiowane w [witryny Azure portal](https://portal.azure.com) w manifeście rejestracji aplikacji.  Gdy użytkownik zaloguje się do aplikacji, usługi Azure AD emituje `roles` oświadczenie dla każdej roli, użytkownikowi udzielono indywidualnie dla użytkownika, z ich członkostwa w grupie.  Przypisywanie użytkowników i grup do ról mogą być gotowe za pośrednictwem portalu interfejsu użytkownika lub programowo przy użyciu [programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Zadeklaruj ról aplikacji za pomocą witryny Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto, a następnie **Przełącz katalog**.
1. Gdy **katalog i Subskrypcja** zostanie otwarte okienko wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację, z **ulubione** lub **wszystkie katalogi**listy.
1. Wybierz **wszystkich usług** w okienku nawigacji po lewej stronie, a wybierz **usługi Azure Active Directory**.
1. W **usługi Azure Active Directory** okienku wybierz **rejestracje aplikacji** Aby wyświetlić listę wszystkich aplikacji.

     Jeśli nie widzisz aplikacji mają być wyświetlane tutaj użyć różnych filtrów w górnej części **rejestracje aplikacji** listy, aby ograniczyć listę lub przewiń w dół na liście, aby zlokalizować aplikację.

1. Wybierz aplikację, aby zdefiniować role aplikacji w programie.
1. W bloku aplikacji wybierz **manifestu**.
1. Edytuj manifest aplikacji, znajdując `appRoles` ustawienie i Dodawanie ról aplikacji.

     > [!NOTE]
     > Każda definicja roli aplikacji, w tym manifeście musi mieć inny prawidłowy identyfikator GUID dla `id` właściwości. 
     > 
     > `value` Właściwości każdego definicję roli aplikacji powinny być identyczne ciągi, które są używane w kodzie aplikacji. `value` Właściwość nie może zawierać spacji. Jeśli tak jest, otrzymasz błąd podczas zapisywania manifestu.
     
1. Umożliwia zapisanie manifestu.

### <a name="examples"></a>Przykłady

W poniższym przykładzie przedstawiono `appRoles` przypisywanych do `users`.

> [!NOTE]
>`id` Musi być unikatowy identyfikator GUID.

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
>`displayName` Nie może zawierać spacji.

Można zdefiniować role aplikacji do obiektu docelowego `users`, `applications`, lub obu. Jeśli jest dostępny do `applications`, role aplikacji są wyświetlane jako uprawnienia aplikacji w **wymagane uprawnienia** bloku. W poniższym przykładzie pokazano roli aplikacji skierowany `Application`.

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

Liczba zdefiniowanych ról wpływa na ograniczeń, które zawiera manifest aplikacji. Zostały one omówione szczegółowo na [manifestu limity](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) strony.

### <a name="assign-users-and-groups-to-roles"></a>Przypisywanie użytkowników i grup do ról

Po dodaniu role aplikacji w aplikacji, można przypisać użytkowników i grup do tych ról.

1. W **usługi Azure Active Directory** okienku wybierz **aplikacje dla przedsiębiorstw** z **usługi Azure Active Directory** menu nawigacji po lewej stronie.
1. Wybierz **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

     Jeśli nie widzisz aplikacji mają być wyświetlane tutaj użyć różnych filtrów w górnej części **wszystkie aplikacje** listy, aby ograniczyć listę lub przewiń w dół na liście, aby zlokalizować aplikację.

1. Wybierz aplikację, w której chcesz przypisać do ról użytkowników lub grupy zabezpieczeń.
1. Wybierz **użytkowników i grup** okienko w menu nawigacji po lewej stronie aplikacji.
1. W górnej części **użytkowników i grup** listy wybierz **Dodaj użytkownika** przycisk, aby otworzyć **Dodaj przydziału** okienka.
1. Wybierz **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

     Lista użytkowników i grup zabezpieczeń będą wyświetlane wraz z pola tekstowego do wyszukiwania i Znajdź niektórych użytkownika lub grupy. Na tym ekranie można wybrać wielu użytkowników i grup w jednym z rzeczywistym użyciem.

1. Po zakończeniu wybierania użytkowników i grup, naciśnij klawisz **wybierz** przycisk na dole, aby przejść do następnej części.
1. Wybierz **wybierz rolę** selektor z **Dodaj przypisanie** okienka. Zostaną wyświetlone wszystkie role, które są zadeklarowane w manifeście aplikacji.
1. Wybierz rolę i naciśnij klawisz **wybierz** przycisku.
1. Naciśnij klawisz **przypisać** przycisk w dolnej części, aby zakończyć przypisań użytkowników i grup do aplikacji.
1. Upewnij się, że użytkownicy i grupy dodane są wyświetlane w zaktualizowanej **użytkowników i grup** listy.

## <a name="more-information"></a>Więcej informacji

- [Autoryzacji w aplikacji sieci web przy użyciu ról aplikacji usługi Azure AD &amp; oświadczeń roli (przykład)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapp-roleclaims/)
- [Korzystanie z grup zabezpieczeń i ról aplikacji w aplikacji (wideo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, teraz oświadczenia grupy i role aplikacji](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Manifest aplikacji w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Tokeny dostępu usługi AAD](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
