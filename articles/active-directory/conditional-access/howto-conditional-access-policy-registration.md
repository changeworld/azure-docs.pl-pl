---
title: Dostęp warunkowy — połączone informacje o zabezpieczeniach — Azure Active Directory
description: Utwórz niestandardowe zasady dostępu warunkowego, aby wymagać zaufanej lokalizacji rejestracji informacji zabezpieczających
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 866b682c9e1ebcb1b3458d26c638237905d06b05
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889738"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Dostęp warunkowy: Wymagaj zaufanej lokalizacji rejestracji usługi MFA

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się w usłudze Azure Multi-Factor Authentication i samoobsługowego resetowania hasła jest teraz możliwe z działaniami użytkowników w zasadach dostępu warunkowego. Ta funkcja w wersji zapoznawczej jest dostępna dla organizacji, które włączyły [Podgląd rejestracji połączonej](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcjonalność może być włączona w organizacjach, w których chcą korzystać z takich warunków, jak w przypadku zaufanej lokalizacji sieciowej, aby ograniczyć dostęp do rejestracji w usłudze Azure Multi-Factor Authentication i SSPR. Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w dostępie warunkowym, zobacz artykuł [jaki jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Utwórz zasady, aby wymagać rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji i blokują dostęp, chyba że nawiązują połączenie z lokalizacji oznaczonej jako zaufane sieci.

1. W **Azure Portal**przejdź do **Azure Active Directory** > **zabezpieczenia** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. W polu Nazwa wprowadź nazwę dla tych zasad. Na przykład **połączona informacja dotycząca zabezpieczeń Rejestracja w zaufanych sieciach**.
1. W obszarze **przypisania**kliknij pozycję **Użytkownicy i grupy**, a następnie wybierz użytkowników i grupy, do których te zasady mają być stosowane.

   > [!WARNING]
   > Użytkownicy muszą mieć włączoną funkcję [Podgląd rejestracji połączonej](../authentication/howto-registration-mfa-sspr-combined.md).

1. W obszarze **aplikacje lub akcje w chmurze**wybierz pozycję **akcje użytkownika**, a następnie sprawdź pozycję **zarejestruj informacje zabezpieczające (wersja zapoznawcza)** .
1. W obszarze **warunki** > **lokalizacje**.
   1. Skonfiguruj **tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie Zaufane lokalizacje**.
   1. Kliknij przycisk **gotowe** w bloku lokalizacje.
   1. Kliknij przycisk **gotowe** w bloku warunki.
1. W obszarze **kontroli dostępu** > **Udziel**.
   1. Kliknij przycisk **Blokuj dostęp**.
   1. Następnie kliknij pozycję **Wybierz**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Następnie kliknij przycisk **Save** (Zapisz).

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
