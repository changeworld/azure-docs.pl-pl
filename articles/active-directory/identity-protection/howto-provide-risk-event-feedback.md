---
title: Reagowanie na zdarzenia o podwyższonym ryzyku w Azure AD Identity Protection — usługi Azure Active Directory
description: Jak i dlaczego należy możesz przekazać opinię dotyczącą zdarzeń o podwyższonym ryzyku Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827908"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Instrukcje: Prześlij opinię o podwyższonym ryzyku w usłudze Azure AD Identity Protection

Usługa Azure AD Identity Protection umożliwia Prześlij opinię na temat oceny ryzyka. Ten dokument zawiera listę scenariuszy, w którym chcesz nadać opinii na temat usługi Azure AD Identity Protection oceny ryzyka i omówi sposób włączenia jej.

## <a name="what-is-a-detection"></a>Co to jest wykrycie?

Wykrywanie Identity Protection jest wskaźnikiem podejrzanych działań z perspektywy ryzyka tożsamości. Te podejrzane działania są nazywane zdarzeń o podwyższonym ryzyku. Wykrywane odmiany oparta na tożsamości może bazować na Algorytm heurystyczny, uczenie maszynowe lub mogą pochodzić z produkty partnerskie. Wykrywane odmiany są używane do określania ryzyka logowania i ryzyka związanego z użytkownikiem

* Ryzyko związane z użytkownikiem odpowiada prawdopodobieństwo naruszenia zabezpieczeń tożsamości.
* Ryzyko logowania reprezentuje prawdopodobieństwo logowania złamania zabezpieczeń (na przykład identyfikator logowania nie jest autoryzowany przez właściciela tożsamości).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Dlaczego przekazać opinię o podwyższonym ryzyku do oceny ryzyka w usłudze Azure AD? 

Istnieje kilka powodów dlaczego należy przekazać opinie o podwyższonym ryzyku Azure AD:

1. **Ocena ryzyka użytkownika lub logowania usługi Azure AD znaleziono niepoprawne**. Na przykład w raporcie "Ryzykowne logowania" Logowanie niegroźne i wszystkie wykrycia tego zalogowaniu zostały wyników fałszywie dodatnich.
1. **Można sprawdzić poprawności użytkownika przez usługę Azure AD lub oceny ryzyka logowania była poprawna**. Na przykład w raporcie "Ryzykowne logowania" Logowanie w rzeczywistości złośliwego i chcesz, aby usługa Azure AD, aby dowiedzieć się, że wszystkie wykrycia tego zalogowaniu były prawdziwie dodatnie.
1. **Skorygowane zagrożenie na tego użytkownika poza programem Azure AD Identity Protection** i chcesz, aby poziom ryzyka użytkownika do zaktualizowania.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Jak program Azure AD używa mojej opinii o podwyższonym ryzyku

Usługa Azure AD używa opinii do aktualizacji ryzyko podstawowego użytkownika i/lub logowania. Opinia ta pomaga zabezpieczyć użytkownika końcowego. Na przykład po upewnieniu się, że logowanie zostanie naruszony, usługi Azure AD natychmiast ryzyka związanego z użytkownikiem i zwiększa jego agregacji ryzyka logowania (nie w czasie rzeczywistym ryzyko) na wysoki. Jeśli ten użytkownik jest uwzględniona w zasadach ryzyka użytkownika do wymuszenia użytkowników o wysokim ryzyku bezpiecznie resetowania swoich haseł, użytkownik zostanie automatycznie korygować się podczas następnego logowania.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Jak należy nadać ryzyka opinie i co się stanie, kulisy?

Poniżej przedstawiono scenariusze i mechanizmy, aby przesłać opinię ryzyka do usługi Azure AD.

| Scenariusz | Jak przesłać opinię? | Co się dzieje kulisy? | Uwagi |
| --- | --- | --- | --- |
| **Logowanie nie złamany (wynik fałszywie dodatni)** <br> Raport "Ryzykowne logowania" przedstawia odpływowi logowania [ryzyka stan = zagrożone], ale że logowania nie zostało naruszone. | Wybierz identyfikator logowania, a następnie kliknij pozycję "Potwierdź logowanie safe". | Usługa Azure AD zostanie przesunięty w agregacji ryzyka logowania None [ryzyka stan = została potwierdzona, bezpieczne. (Agregacji) poziom ryzyka =-] i wycofasz wpływa na ryzyko związane z użytkownikiem. | Obecnie, "Potwierdź logowanie safe" opcja jest dostępna tylko w raporcie "Ryzykowne logowania". |
| **Zaloguj się naruszenia zabezpieczeń (prawdziwie dodatni)** <br> Raport "Ryzykowne logowania" przedstawia odpływowi logowania [ryzyka stan = zagrożone] o niskim ryzyku [(agregacji) poziom ryzyka = niski] i że logowania w rzeczywistości zostało naruszone. | Wybierz identyfikator logowania, a następnie kliknij pozycję "Potwierdź logowanie naruszenia zabezpieczeń". | Usługi Azure AD zostanie przesunięty w agregacji ryzyka logowania i ryzyka związanego z użytkownikiem na wysoki [ryzyka stan = została potwierdzona, naruszenia zabezpieczeń; Poziom ryzyka wysoki =]. | Obecnie, "Potwierdź logowanie złamane" opcja jest dostępna tylko w raporcie "Ryzykowne logowania". |
| **Naruszenia zabezpieczeń użytkownika (prawdziwie dodatni)** <br> "Ryzykownych użytkowników" przedstawia zagrożonych użytkowników [ryzyka stan = zagrożone] o niskim ryzyku [poziom ryzyka = niski] i rzeczywiście naruszenia tego użytkownika. | Wybierz użytkownika, a następnie kliknij pozycję "Potwierdź użytkownik naruszenia zabezpieczeń". | Usługa Azure AD zostanie przesunięty ryzyka związanego z użytkownikiem na wysoki [ryzyka stan = została potwierdzona, naruszenia zabezpieczeń; Poziom ryzyka wysoki =] i doda nowe wykrycie "Administrator potwierdza, naruszenia zabezpieczeń użytkownika". | Obecnie, "Potwierdź użytkownik złamane" opcja jest dostępna tylko w raporcie "Ryzykownych użytkowników". <br> Wykrywanie "Administrator potwierdza, naruszenia zabezpieczeń użytkownika" jest wyświetlany na karcie "Zdarzenia o podwyższonym ryzyku niepołączone z zalogowaniem się" w raporcie "Ryzykownych użytkowników". |
| **Użytkownik skorygowane poza programem Azure AD Identity Protection (prawdziwie dodatni + Remediated)** <br> Raport "Ryzykownych użytkowników" przedstawia zagrożonych użytkowników i I następnie zostały skorygowane użytkownika poza programem Azure AD Identity Protection. | 1. Wybierz użytkownika, a następnie kliknij przycisk "Potwierdź użytkownik naruszenia zabezpieczeń". (Ten proces potwierdza do usługi Azure AD w rzeczywistości naruszenia użytkownika.) <br> 2. Poczekaj, aż użytkownika "poziom zagrożenia" przejść na wysoki. (To czas umożliwia usłudze Azure AD potrzebny czas aparatu ryzyka umożliwiające usunięcie powyższych informacji zwrotnych.) <br> 3. Wybierz użytkownika, a następnie kliknij przycisk "Odrzuć ryzyka związanego z użytkownikiem". (Ten proces potwierdza do usługi Azure AD nie jest już złamane użytkownika.) |  Usługa Azure AD przenosi ryzyka związanego z użytkownikiem na Brak [ryzyka stan = odrzucone; Poziom ryzyka =-] i zamyka ryzyka na wszystkich istniejących logowań posiadanie aktywnego ryzyka. | Klikając przycisk "Odrzuć ryzyka użytkownika" spowoduje zamknięcie wszystkich ryzyka użytkownika, a ostatnie logowania. Tej akcji nie można cofnąć. |
| **Użytkownik nie został złamany (wynik fałszywie dodatni)** <br> Przedstawia "Ryzykownych użytkowników" w zagrożonych użytkowników, ale użytkownik nie zostanie naruszony. | Wybierz użytkownika, a następnie kliknij przycisk "Odrzuć ryzyka związanego z użytkownikiem". (Ten proces potwierdza do usługi Azure AD użytkownika nie są zagrożone.) | Usługa Azure AD przenosi ryzyka związanego z użytkownikiem na Brak [ryzyka stan = odrzucone; Poziom ryzyka =-]. | Klikając przycisk "Odrzuć ryzyka użytkownika" spowoduje zamknięcie wszystkich ryzyka użytkownika, a ostatnie logowania. Tej akcji nie można cofnąć. |
| Czy chcesz zamknąć ryzyka związanego z użytkownikiem, ale nie mam pewności, czy użytkownik jest, których bezpieczeństwo zostało naruszone / bezpieczne. | Wybierz użytkownika, a następnie kliknij przycisk "Odrzuć ryzyka związanego z użytkownikiem". (Ten proces potwierdza do usługi Azure AD nie jest już złamane użytkownika.) | Usługa Azure AD przenosi ryzyka związanego z użytkownikiem na Brak [ryzyka stan = odrzucone; Poziom ryzyka =-]. | Klikając przycisk "Odrzuć ryzyka użytkownika" spowoduje zamknięcie wszystkich ryzyka użytkownika, a ostatnie logowania. Tej akcji nie można cofnąć. Firma Microsoft zaleca korygowanie użytkownika, klikając pozycję "Resetuj hasło" lub żądania użytkownika do bezpiecznego Resetowanie/zmiana poświadczeń. |

Opinie na temat zdarzeń o podwyższonym ryzyku użytkownika w Identity Protection są przetwarzane w trybie offline i może zająć trochę czasu, aby zaktualizować. Ryzyko przetwarzanie kolumny stan będzie udostępniać bieżący stan przetwarzania opinii.

![Ryzyko stan przetwarzania raportu ryzykownych użytkowników](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Kolejne kroki

[Dokumentacja zdarzeń o podwyższonym ryzyku w usłudze Azure Active Directory Identity Protection](risk-events-reference.md)