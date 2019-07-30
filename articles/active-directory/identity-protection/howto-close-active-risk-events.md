---
title: Jak zamknąć aktywne zdarzenia ryzyka w Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się więcej o opcjach zamykania aktywnych zdarzeń dotyczących ryzyka.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5e24c12b72852ee7009533c8dc24d231fe636f2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334002"
---
# <a name="how-to-close-active-risk-events"></a>Instrukcje: Zamykanie aktywnych zdarzeń o podwyższonym ryzyku

[Zdarzenia](../reports-monitoring/concept-risk-events.md)o podwyższonym ryzyku Azure Active Directory wykrywają wskaźniki dla potencjalnie naruszonych kont użytkowników. Jako administrator chcesz, aby wszystkie zdarzenia dotyczące ryzyka zostały zamknięte, dzięki czemu zagrożoni użytkownicy nie będą już zagrożeni.

Ten artykuł zawiera omówienie dodatkowych opcji, które należy wykonać, aby zamknąć aktywne zdarzenia o podwyższonym ryzyku.

## <a name="options-to-close-risk-events"></a>Opcje zamykania zdarzeń ryzyka 

Stan zdarzenia ryzyka jest **aktywny** lub **zamknięty**. Wszystkie aktywne zdarzenia ryzyka przyczyniają się do obliczenia wartości o nazwie poziom ryzyka użytkownika. Poziom ryzyka użytkownika to wskaźnik (niski, średni, wysoki) dla prawdopodobieństwa naruszenia zabezpieczeń konta. 

Aby zamknąć aktywne zdarzenia dotyczące ryzyka, dostępne są następujące opcje:

- Wymagaj resetowania hasła przy użyciu zasad ryzyka użytkownika
- Ręczne resetowanie hasła
- Odrzuć wszystkie zdarzenia ryzyka 
- Ręczne zamykanie poszczególnych zdarzeń ryzyka

## <a name="require-password-reset-with-a-user-risk-policy"></a>Wymagaj resetowania hasła przy użyciu zasad ryzyka użytkownika

Konfigurując [zasady dostępu warunkowego dotyczącego ryzyka użytkownika](howto-user-risk-policy.md), można wymagać zmiany hasła w przypadku automatycznego wykrycia określonego poziomu ryzyka użytkownika. 

![Resetowanie hasła](./media/howto-close-active-risk-events/13.png)

Resetowanie hasła zamyka wszystkie aktywne zdarzenia związane z użytkownikiem i przywraca tożsamość z powrotem do stanu bezpiecznego. Użycie zasad ryzyka dla użytkowników jest preferowaną metodą zamykania aktywnych zdarzeń ryzyka, ponieważ ta metoda jest zautomatyzowana. Między użytkownikiem, którego dotyczy problem, a działem pomocy technicznej lub administratorem nie jest wymagana żadna interakcja.

Jednak korzystanie z zasad ryzyka użytkownika nie zawsze jest stosowane. Dotyczy to na przykład:

- Użytkownicy, którzy nie zostali zarejestrowani do uwierzytelniania wieloskładnikowego (MFA).
- Użytkownicy z aktywnymi zdarzeniami dotyczącymi ryzyka, które zostały usunięte.
- Badanie, które informuje, że zgłoszone zdarzenie związane z ryzykiem zostało wykonane przez uprawnionego użytkownika.

## <a name="manual-password-reset"></a>Ręczne resetowanie hasła

Jeśli wymaganie resetowania hasła przy użyciu zasad ryzyka użytkownika nie jest opcją, można pobrać wszystkie zdarzenia dotyczące ryzyka dla użytkownika zamkniętego przy użyciu ręcznego resetowania hasła.

![Resetowanie hasła](./media/howto-close-active-risk-events/04.png)

Powiązane okno dialogowe udostępnia dwie różne metody resetowania hasła:

![Resetowanie hasła](./media/howto-close-active-risk-events/05.png)

**Generuj hasło tymczasowe** — przez wygenerowanie hasła tymczasowego możesz natychmiast przywrócić tożsamość z powrotem do stanu bezpiecznego. Ta metoda wymaga współdziałania z użytkownikami, których to dotyczy, ponieważ chcą wiedzieć, jakie jest hasło tymczasowe. Możesz na przykład wysłać nowe hasło tymczasowe na alternatywny adres e-mail użytkownika lub do Menedżera użytkownika. Ponieważ hasło jest tymczasowe, użytkownik jest monitowany o zmianę hasła podczas kolejnego logowania.

**Wymagaj od użytkownika zresetowania hasła** — wymaganie od użytkowników resetowania haseł umożliwia samoodzyskiwanie bez kontaktowania się z pomocą techniczną lub administratorem. Podobnie jak w przypadku zasad ryzyka użytkownika, ta metoda ma zastosowanie tylko do użytkowników zarejestrowanych w ramach usługi MFA. W przypadku użytkowników, którzy nie zostali jeszcze zarejestrowani dla usługi MFA, ta opcja jest niedostępna.

## <a name="dismiss-all-risk-events"></a>Odrzuć wszystkie zdarzenia ryzyka

Jeśli Resetowanie hasła nie jest opcją dla Ciebie, można również odrzucić wszystkie zdarzenia o podwyższonym ryzyku. 

![Resetowanie hasła](./media/howto-close-active-risk-events/03.png)

Po kliknięciu przycisku **Odrzuć wszystkie zdarzenia**wszystkie zdarzenia zostaną zamknięte, a zagrożony użytkownik nie jest już narażony. Jednak ponieważ ta metoda nie ma wpływu na istniejące hasło, nie powoduje, że powiązana tożsamość nie zostanie przywrócona do stanu bezpiecznego. Preferowanym przypadkiem użycia tej metody jest usunięty użytkownik z aktywnymi zdarzeniami o podwyższonym ryzyku. 

## <a name="close-individual-risk-events-manually"></a>Ręczne zamykanie poszczególnych zdarzeń ryzyka

Możesz ręcznie zamknąć poszczególne zdarzenia ryzyka. Po ręcznym zamykaniu zdarzeń ryzyka można obniżyć poziom ryzyka użytkownika. Zwykle zdarzenia o podwyższonym ryzyku są zamykane ręcznie w odpowiedzi na powiązane badanie. Na przykład podczas rozmowy z użytkownikiem wykaże, że aktywne zdarzenie ryzyka nie jest już wymagane. 
 
W przypadku ręcznego zamykania zdarzeń związanych z ryzykiem można wykonać jedną z następujących czynności, aby zmienić stan zdarzenia dotyczącego ryzyka:

![Akcje](./media/howto-close-active-risk-events/06.png)

- **Rozpoznaj** — Jeśli po zbadaniu zdarzenia dotyczącego ryzyka podjęto odpowiednie działania naprawcze poza usługą Identity Protection i uważasz, że zdarzenie ryzyka powinno być uznawane za zamknięte, Oznacz zdarzenie jako rozwiązane. Rozwiązane zdarzenia spowodują ustawienie stanu zdarzenia na zamknięte, a zdarzenie ryzyka nie przyczynia się już do ryzyka użytkownika.
- **Oznacz jako fałszywie-dodatni** — w niektórych przypadkach możesz zbadać zdarzenie o podwyższonym ryzyku i odkryć, że zostało ono niepoprawnie oflagowane jako ryzykowne. Możesz zmniejszyć liczbę takich wystąpień, zaznaczając zdarzenie ryzyka jako FAŁSZ-pozytywne. Dzięki temu algorytmy uczenia maszynowego mogą ulepszyć klasyfikację podobnych zdarzeń w przyszłości. Stan fałszywie dodatnich zdarzeń jest zamknięty i nie będzie już współtworzyć ryzyka użytkownika.
- **Ignoruj** — Jeśli nie podjęto żadnej akcji korygowania, ale chcesz, aby zdarzenie ryzyka zostało usunięte z aktywnej listy, możesz oznaczyć zdarzenie ryzyka Ignoruj, a stan zdarzenia zostanie zamknięty. Zignorowane zdarzenia nie przyczyniają się do ryzyka użytkownika. Tej opcji należy używać tylko w nietypowych warunkach.
- Ponowne uaktywnianie zdarzeń ryzyka, które zostały ręcznie zamknięte (poprzez wybranie opcji Rozwiąż, fałszywie dodatni lub Ignoruj) można ponownie uaktywnić, ustawiając stan zdarzenia z powrotem na aktywny. Ponownie aktywowane zdarzenia ryzyka przyczyniają się do obliczenia poziomu ryzyka użytkownika. Zdarzenia ryzyka zamknięte przez skorygowanie (na przykład bezpieczne Resetowanie hasła) nie mogą zostać ponownie aktywowane.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview.md).
