---
title: Użytkownicy i grupy w zasadach dostępu warunkowego — usługa Azure Active Directory
description: Kim są użytkownicy i grupy w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17312e44714c8bdb20e22ad9aeb950e46eb71e3e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755257"
---
# <a name="conditional-access-users-and-groups"></a>Dostęp warunkowy: użytkownicy i grupy

Zasady dostępu warunkowego muszą zawierać przypisanie użytkownika jako jeden z sygnałów w procesie decyzyjnym. Użytkownicy mogą być uwzględniane lub wykluczane z zasad dostępu warunkowego. 

![Użytkownik jako sygnał w decyzjach podejmowanych przez dostęp warunkowy](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Uwzględnij użytkowników

Ta lista użytkowników zazwyczaj zawiera wszystkich użytkowników, na które organizacja jest kierowana w zasadach dostępu warunkowego. 

Następujące opcje są dostępne do uwzględnienia podczas tworzenia zasad dostępu warunkowego.

- Brak
   - Nie wybrano użytkowników
- Wszyscy użytkownicy
   - Wszyscy użytkownicy, którzy istnieją w katalogu, w tym goście B2B.
- Wybieranie użytkowników i grup
   - Wszyscy użytkownicy -gościnni i zewnętrzni
      - Ten wybór obejmuje wszystkich gości B2B i `user type` użytkowników zewnętrznych, `guest`w tym każdego użytkownika z atrybutem ustawionym na . Ten wybór dotyczy również każdego użytkownika zewnętrznego zalogowanego z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
   - Role katalogu
      - Umożliwia administratorom wybranie określonych ról katalogu usługi Azure AD używanych do określania przypisania. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dotyczące użytkowników przypisanych roli administratora globalnego.
   - Użytkownicy i grupy
      - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu HR, gdy aplikacja HR jest zaznaczona jako aplikacja w chmurze. Grupa może być dowolnym typem grupy w usłudze Azure AD, w tym dynamiczne lub przypisane grupy zabezpieczeń i dystrybucji.

## <a name="exclude-users"></a>Wykluczanie użytkowników

Jeśli organizacje obejmują i wykluczają użytkownika lub grupę, użytkownik lub grupa jest wykluczona z zasad, ponieważ akcja wykluczania zastępuje uwzględnić w zasadach. Wykluczenia są powszechnie używane do dostępu awaryjnego lub break-glass kont. Więcej informacji na temat kont dostępu awaryjnego i powodów, dla których są one ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Tworzenie strategii zarządzania elastyczną kontrolą dostępu za pomocą usługi Azure Active Directory](../authentication/concept-resilient-controls.md)

Następujące opcje są dostępne do wykluczenia podczas tworzenia zasad dostępu warunkowego.

- Wszyscy użytkownicy -gościnni i zewnętrzni
   - Ten wybór obejmuje wszystkich gości B2B i `user type` użytkowników zewnętrznych, `guest`w tym każdego użytkownika z atrybutem ustawionym na . Ten wybór dotyczy również każdego użytkownika zewnętrznego zalogowanego z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
- Role katalogu
   - Umożliwia administratorom wybranie określonych ról katalogu usługi Azure AD używanych do określania przypisania. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dotyczące użytkowników przypisanych roli administratora globalnego.
- Użytkownicy i grupy
   - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu HR, gdy aplikacja HR jest zaznaczona jako aplikacja w chmurze. Grupa może być dowolnym typem grupy w usłudze Azure AD, w tym dynamiczne lub przypisane grupy zabezpieczeń i dystrybucji.

### <a name="preventing-administrator-lockout"></a>Zapobieganie blokadzie administratora

Aby uniemożliwić administratorowi zablokowanie się z katalogu podczas tworzenia zasad zastosowanych do **wszystkich użytkowników** i **wszystkich aplikacji,** zostaną wyświetlone następujące ostrzeżenie.

> Nie zamykaj się! Zalecamy zastosowanie zasad do małego zestawu użytkowników, aby najpierw sprawdzić, czy zachowuje się zgodnie z oczekiwaniami. Zalecamy również wyłączenie co najmniej jednego administratora z tych zasad. Dzięki temu nadal masz dostęp i można zaktualizować zasady, jeśli zmiana jest wymagana. Zapoznaj się z problemami użytkowników i aplikacji, których to dotyczy.

Domyślnie zasady zapewniają opcję wykluczenia bieżącego użytkownika z zasad, ale ta wartość domyślna może zostać zastąpiona przez administratora, jak pokazano na poniższej ilustracji. 

![Uwaga, nie zamykaj się!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: aplikacje lub akcje w chmurze](concept-conditional-access-cloud-apps.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
