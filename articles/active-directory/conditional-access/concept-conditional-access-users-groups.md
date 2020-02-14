---
title: Użytkownicy i grupy w zasadach dostępu warunkowego — Azure Active Directory
description: Kto jest użytkownikami i grupami w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192131"
---
# <a name="conditional-access-users-and-groups"></a>Dostęp warunkowy: Użytkownicy i grupy

Zasady dostępu warunkowego muszą zawierać przypisanie użytkownika jako jeden z sygnałów w procesie decyzyjnym. Użytkownicy mogą być dołączeni lub wykluczeni z zasad dostępu warunkowego. 

![Użytkownik jako sygnał w decyzji podejmowanych przez dostęp warunkowy](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Uwzględnij użytkowników

Ta lista użytkowników zazwyczaj obejmuje wszystkich użytkowników, których organizacja ma dla zasad dostępu warunkowego. 

Poniższe opcje są dostępne do uwzględnienia podczas tworzenia zasad dostępu warunkowego.

- None
   - Nie wybrano żadnych użytkowników
- Wszyscy użytkownicy
   - Wszyscy użytkownicy, którzy istnieją w katalogu, w tym Goście B2B.
- Wybieranie użytkowników i grup
   - Wszyscy Goście i użytkownicy zewnętrzni (wersja zapoznawcza)
      - Ten wybór obejmuje wszystkich Gości i użytkowników zewnętrznych, w tym dowolny użytkownik z atrybutem `user type` ustawionym na `guest`. Ten wybór dotyczy również wszystkich użytkowników zewnętrznych zalogowanych z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
   - Role katalogu (wersja zapoznawcza)
      - Umożliwia administratorom wybranie określonych ról katalogu usługi Azure AD używanych do określania przypisania. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dla użytkowników, którym przypisano rolę administratora globalnego.
   - Użytkownicy i grupy
      - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu kadr, gdy aplikacja usługi kadr zostanie wybrana jako aplikacja w chmurze. Grupa może być dowolnymi grupami w usłudze Azure AD, w tym dynamicznymi lub przypisanymi grupami zabezpieczeń i dystrybucji.

## <a name="exclude-users"></a>Wyklucz użytkowników

Wykluczenia są często używane w przypadku dostępu awaryjnego lub kont ze szkłami do awarii. Więcej informacji o kontach dostępu awaryjnego i o tym, dlaczego są ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Utwórz odporną strategię zarządzania kontrolą dostępu za pomocą Azure Active Directory](../authentication/concept-resilient-controls.md)

Poniższe opcje są dostępne do wykluczenia podczas tworzenia zasad dostępu warunkowego.

- Wszyscy Goście i użytkownicy zewnętrzni (wersja zapoznawcza)
   - Ten wybór obejmuje wszystkich Gości i użytkowników zewnętrznych, w tym dowolny użytkownik z atrybutem `user type` ustawionym na `guest`. Ten wybór dotyczy również wszystkich użytkowników zewnętrznych zalogowanych z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
- Role katalogu (wersja zapoznawcza)
   - Umożliwia administratorom wybranie określonych ról katalogu usługi Azure AD używanych do określania przypisania. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dla użytkowników, którym przypisano rolę administratora globalnego.
- Użytkownicy i grupy
   - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu kadr, gdy aplikacja usługi kadr zostanie wybrana jako aplikacja w chmurze. Grupa może być dowolnymi grupami w usłudze Azure AD, w tym dynamicznymi lub przypisanymi grupami zabezpieczeń i dystrybucji.

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: aplikacje lub akcje w chmurze](concept-conditional-access-cloud-apps.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
