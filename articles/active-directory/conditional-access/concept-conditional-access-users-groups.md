---
title: Użytkownicy i grupy w zasadach dostępu warunkowego — usługa Azure Active Directory
description: Kim są użytkownicy i grupy w zasadach dostępu warunkowego usługi Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192131"
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
   - Wszyscy użytkownicy-goście i użytkownicy zewnętrzni (wersja zapoznawcza)
      - Ten wybór obejmuje wszystkich gości B2B i `user type` użytkowników zewnętrznych, `guest`w tym każdego użytkownika z atrybutem ustawionym na . Ten wybór dotyczy również każdego użytkownika zewnętrznego zalogowanego z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
   - Role katalogu (wersja zapoznawcza)
      - Umożliwia administratorom wybranie określonych ról katalogu usługi Azure AD używanych do określania przypisania. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dotyczące użytkowników przypisanych roli administratora globalnego.
   - Użytkownicy i grupy
      - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu HR, gdy aplikacja HR jest zaznaczona jako aplikacja w chmurze. Grupa może być dowolnym typem grupy w usłudze Azure AD, w tym dynamiczne lub przypisane grupy zabezpieczeń i dystrybucji.

## <a name="exclude-users"></a>Wykluczanie użytkowników

Wykluczenia są powszechnie używane do dostępu awaryjnego lub break-glass kont. Więcej informacji na temat kont dostępu awaryjnego i powodów, dla których są one ważne, można znaleźć w następujących artykułach: 

* [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Tworzenie strategii zarządzania elastyczną kontrolą dostępu za pomocą usługi Azure Active Directory](../authentication/concept-resilient-controls.md)

Następujące opcje są dostępne do wykluczenia podczas tworzenia zasad dostępu warunkowego.

- Wszyscy użytkownicy-goście i użytkownicy zewnętrzni (wersja zapoznawcza)
   - Ten wybór obejmuje wszystkich gości B2B i `user type` użytkowników zewnętrznych, `guest`w tym każdego użytkownika z atrybutem ustawionym na . Ten wybór dotyczy również każdego użytkownika zewnętrznego zalogowanego z innej organizacji, takiej jak dostawca rozwiązań w chmurze (CSP). 
- Role katalogu (wersja zapoznawcza)
   - Umożliwia administratorom wybranie określonych ról katalogu usługi Azure AD używanych do określania przypisania. Na przykład organizacje mogą tworzyć bardziej restrykcyjne zasady dotyczące użytkowników przypisanych roli administratora globalnego.
- Użytkownicy i grupy
   - Umożliwia kierowanie określonych zestawów użytkowników. Na przykład organizacje mogą wybrać grupę, która zawiera wszystkich członków działu HR, gdy aplikacja HR jest zaznaczona jako aplikacja w chmurze. Grupa może być dowolnym typem grupy w usłudze Azure AD, w tym dynamiczne lub przypisane grupy zabezpieczeń i dystrybucji.

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: aplikacje lub akcje w chmurze](concept-conditional-access-cloud-apps.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
