---
title: Przekazywanie informacji zwrotnych o ryzyku w usłudze Azure Active Directory Identity Protection
description: Jak i dlaczego należy przekazywać opinie na temat wykrywania ryzyka ochrony tożsamości.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382097"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Jak: Przekazywanie opinii o ryzyku w usłudze Azure AD Identity Protection

Usługa Azure AD Identity Protection umożliwia przekazywanie opinii na temat jego oceny ryzyka. W poniższym dokumencie wymieniono scenariusze, w których chcesz przekazać opinię na temat oceny ryzyka usługi Azure AD Identity Protection i jak ją uwzględnić.

## <a name="what-is-a-detection"></a>Co to jest wykrywanie?

Wykrywanie ochrony tożsamości jest wskaźnikiem podejrzanej aktywności z punktu widzenia ryzyka tożsamości. Te podejrzane działania są nazywane wykrywaniem ryzyka. Te wykrywania oparte na tożsamości mogą być oparte na heurystyki, uczeniu maszynowym lub mogą pochodzić z produktów partnerskich. Wykrywania te służą do określenia ryzyka logowania i ryzyka

* Ryzyko użytkownika reprezentuje prawdopodobieństwo, że tożsamość zostanie naruszona.
* Ryzyko logowania reprezentuje prawdopodobieństwo, że logowanie zostanie naruszone (na przykład logowanie nie jest autoryzowane przez właściciela tożsamości).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Dlaczego warto przekazywać informacje zwrotne o ryzyku do ocen ryzyka usługi Azure AD? 

Istnieje kilka powodów, dla których należy przekazywać opinie o ryzyku usługi Azure AD:

- **Stwierdzono, że użytkownik usługi Azure AD lub ocena ryzyka logowania niepoprawne**. Na przykład logowanie wyświetlane w raporcie "Ryzykowne logowania" było łagodne, a wszystkie wykrycia na tym logowanie były fałszywymi alarmami.
- **Potwierdzono, że ocena ryzyka użytkownika lub logowania**użytkownika usługi Azure AD była poprawna. Na przykład logowania pokazano w raporcie "Ryzykowne logowania" rzeczywiście złośliwy i chcesz usługi Azure AD wiedzieć, że wszystkie wykrycia na tym logowanie były prawdziwe pozytywy.
- **Naprawiono ryzyko dla tego użytkownika poza usługą Azure AD Identity Protection** i chcesz zaktualizować poziom ryzyka użytkownika.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>W jaki sposób usługa Azure AD korzysta z moich opinii o ryzyku?

Usługa Azure AD używa opinii do aktualizacji ryzyka użytkownika i/lub logowania oraz dokładności tych zdarzeń. Ta opinia pomaga zabezpieczyć użytkownika końcowego. Na przykład po potwierdzeniu logowania jest zagrożona, usługa Azure AD natychmiast zwiększa ryzyko użytkownika i logowania zagregowane ryzyko (nie ryzyko w czasie rzeczywistym) do wysokiego. Jeśli ten użytkownik jest uwzględniony w zasadach ryzyka użytkownika, aby zmusić użytkowników wysokiego ryzyka do bezpiecznego resetowania haseł, użytkownik automatycznie naprawi się przy następnym logowaniem.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Jak przekazać informację zwrotną o ryzyku i co dzieje się pod maską?

Poniżej przedstawiono scenariusze i mechanizmy przekazywania informacji zwrotnych o ryzyku do usługi Azure AD.

| Scenariusz | Jak przekazać opinię? | Co dzieje się pod maską? | Uwagi |
| --- | --- | --- | --- |
| **Nieprawidłowe logowanie (false positive)** <br> Raport "Ryzykowne logowania" pokazuje ryzyko logowania [Stan ryzyka = Ryzyko], ale to logowanie nie zostało naruszone. | Wybierz opcję logowania i kliknij "Potwierdź bezpieczeństwo logowania". | Usługa Azure AD przeniesie zagregowane ryzyko logowania na brak [Stan ryzyka = Potwierdzone bezpieczne; Poziom ryzyka (agregacja) = -] i odwróci jego wpływ na ryzyko użytkownika. | Obecnie opcja "Potwierdź bezpieczne logowanie" jest dostępna tylko w raporcie "Ryzykowne logowania". |
| **Naruszone logowanie (true positive)** <br> Raport "Ryzykowne logowania" pokazuje ryzyko logowania [Stan ryzyka = Ryzyko] z niskim ryzykiem [Poziom ryzyka (zbiorczo) = niski] i że logowanie było rzeczywiście zagrożone. | Wybierz opcję logowania i kliknij "Potwierdź bezpieczeństwo logowania". | Usługa Azure AD przeniesie zagregowane ryzyko logowania i ryzyko użytkownika do stanu wysokiego ryzyka = potwierdzone naruszenia zabezpieczeń; Poziom ryzyka = wysoki]. | Obecnie opcja "Potwierdź bezpieczeństwo logowania" jest dostępna tylko w raporcie "Ryzykowne logowania". |
| **Naruszone przez użytkownika (true positive)** <br> Raport "Ryzykowni użytkownicy" pokazuje użytkownika zagrożonego [Stan ryzyka = Ryzyko] z niskim ryzykiem [Poziom ryzyka = Niski] i że użytkownik został rzeczywiście naruszone. | Wybierz użytkownika i kliknij "Potwierdź użytkownika zagrożonego". | Usługa Azure AD przeniesie ryzyko użytkownika do stanu wysokiego ryzyka =Potwierdzone naruszenia zabezpieczeń; Poziom ryzyka = Wysoki] i doda nowe wykrywanie "Administrator potwierdził użytkownika zagrożonego". | Obecnie opcja "Potwierdź bezpieczeństwo użytkownika" jest dostępna tylko w raporcie "Ryzykowni użytkownicy". <br> Wykrywanie "Administrator potwierdzony użytkownik zagrożony" jest pokazany na karcie "Wykrywanie ryzyka nie związane z logowaniem" w raporcie "Ryzykowni użytkownicy". |
| **Użytkownik korygowany poza usługą Azure AD Identity Protection (True positive + Remediated)** <br> Raport "Ryzykowni użytkownicy" pokazuje użytkownika zagrożonego, a następnie skorygowałem użytkownika poza usługą Azure AD Identity Protection. | 1. Wybierz użytkownika i kliknij przycisk "Potwierdź, że użytkownik został naruszony". (Ten proces potwierdza usługi Azure AD, że użytkownik został rzeczywiście naruszone.) <br> 2. Poczekaj, aż "Poziom ryzyka" użytkownika przejdzie do Wysokiego. (Ten czas daje usługi Azure AD potrzebny czas, aby podjąć powyższe opinie do aparatu ryzyka.) <br> 3. Wybierz użytkownika i kliknij przycisk "Odrzuć ryzyko użytkownika". (Ten proces potwierdza usługi Azure AD, że użytkownik nie jest już zagrożona.) |  Usługa Azure AD przenosi ryzyko użytkownika na brak [Stan ryzyka = Odrzucone; Poziom ryzyka = -] i zamyka ryzyko dla wszystkich istniejących logów o aktywnym ryzyku. | Kliknięcie przycisku "Odrzuć ryzyko użytkownika" spowoduje zamknięcie całego ryzyka związanego z użytkownikiem i przeszłymi logowaniami. Tej akcji nie można cofnąć. |
| **Użytkownik nie został naruszony (fałszywy alarm)** <br> Raport "Ryzykowni użytkownicy" pokazuje na użytkownika zagrożonego, ale użytkownik nie jest zagrożona. | Wybierz użytkownika i kliknij przycisk "Odrzuć ryzyko użytkownika". (Ten proces potwierdza usługi Azure AD, że użytkownik nie jest zagrożona.) | Usługa Azure AD przenosi ryzyko użytkownika na brak [Stan ryzyka = Odrzucone; Poziom ryzyka = -]. | Kliknięcie przycisku "Odrzuć ryzyko użytkownika" spowoduje zamknięcie całego ryzyka związanego z użytkownikiem i przeszłymi logowaniami. Tej akcji nie można cofnąć. |
| Chcę zamknąć ryzyko użytkownika, ale nie jestem pewien, czy użytkownik jest zagrożona / bezpieczne. | Wybierz użytkownika i kliknij przycisk "Odrzuć ryzyko użytkownika". (Ten proces potwierdza usługi Azure AD, że użytkownik nie jest już zagrożona.) | Usługa Azure AD przenosi ryzyko użytkownika na brak [Stan ryzyka = Odrzucone; Poziom ryzyka = -]. | Kliknięcie przycisku "Odrzuć ryzyko użytkownika" spowoduje zamknięcie całego ryzyka związanego z użytkownikiem i przeszłymi logowaniami. Tej akcji nie można cofnąć. Zalecamy skorygowanie użytkownika, klikając przycisk "Resetuj hasło" lub żądając od użytkownika bezpiecznego zresetowania/zmiany poświadczeń. |

Opinie na temat wykrywania ryzyka użytkownika w ochronie tożsamości są przetwarzane w trybie offline i może zająć trochę czasu, aby zaktualizować. Kolumna stanu przetwarzania ryzyka zapewni bieżący stan przetwarzania opinii.

![Stan przetwarzania ryzyka dla raportu użytkownika o ryzyku](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Następne kroki

- [Odwołanie do wykrywania ryzyka usługi Azure Active Directory Identity Protection](risk-events-reference.md)
