---
title: Jak zamknąć aktywne wykrycia ryzyka w Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się więcej na temat opcji zamykających aktywne wykrywania zagrożeń.
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
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126469"
---
# <a name="how-to-close-active-risk-detections"></a>Instrukcje: Zamknij aktywne wykrywanie ryzyka

W przypadku [wykrycia ryzyka](../reports-monitoring/concept-risk-events.md)Azure Active Directory wykrywa wskaźniki dla potencjalnie naruszonych kont użytkowników. Jako administrator chcesz, aby wszystkie wykrycia ryzyka były zamknięte, dzięki czemu zagrożoni użytkownicy nie będą już zagrożeni.

Ten artykuł zawiera omówienie dodatkowych opcji, które należy wykonać, aby zamknąć aktywne wykrywania zagrożeń.

## <a name="options-to-close-risk-detections"></a>Opcje zamykania wykrywania ryzyka 

Stan wykrywania ryzyka jest **aktywny** lub **zamknięty**. Wszystkie aktywne wykrycia ryzyka przyczyniają się do obliczenia wartości o nazwie poziom ryzyka użytkownika. Poziom ryzyka użytkownika to wskaźnik (niski, średni, wysoki) dla prawdopodobieństwa naruszenia zabezpieczeń konta. 

Aby zamknąć aktywne wykrywania ryzyka, dostępne są następujące opcje:

- Wymagaj resetowania hasła przy użyciu zasad ryzyka użytkownika
- Ręczne resetowanie hasła
- Odrzuć wszystkie wykrycia ryzyka 
- Ręczne zamykanie indywidualnych wykrycia ryzyka

## <a name="require-password-reset-with-a-user-risk-policy"></a>Wymagaj resetowania hasła przy użyciu zasad ryzyka użytkownika

Konfigurując [zasady dostępu warunkowego dotyczącego ryzyka użytkownika](howto-user-risk-policy.md), można wymagać zmiany hasła w przypadku automatycznego wykrycia określonego poziomu ryzyka użytkownika. 

![Resetowanie hasła](./media/howto-close-active-risk-events/13.png)

Resetowanie hasła zamyka wszystkie aktywne zdarzenia związane z użytkownikiem i przywraca tożsamość z powrotem do stanu bezpiecznego. Użycie zasad ryzyka dla użytkowników jest preferowaną metodą zamykania aktywnych funkcji wykrywania ryzyka, ponieważ ta metoda jest zautomatyzowana. Między użytkownikiem, którego dotyczy problem, a działem pomocy technicznej lub administratorem nie jest wymagana żadna interakcja.

Jednak korzystanie z zasad ryzyka użytkownika nie zawsze jest stosowane. Dotyczy to na przykład:

- Użytkownicy, którzy nie zostali zarejestrowani do uwierzytelniania wieloskładnikowego (MFA).
- Użytkownicy z aktywnymi wykryciami ryzyka, które zostały usunięte.
- Badanie, które informuje, że wykrycie zgłoszonego ryzyka zostało wykonane przez uprawnionego użytkownika.

## <a name="manual-password-reset"></a>Ręczne resetowanie hasła

Jeśli wymaganie resetowania hasła przy użyciu zasad ryzyka użytkownika nie jest opcją, można uzyskać wszystkie wykrycia ryzyka dla użytkownika zamkniętego za pomocą ręcznego resetowania hasła.

![Resetowanie hasła](./media/howto-close-active-risk-events/04.png)

Powiązane okno dialogowe udostępnia dwie różne metody resetowania hasła:

![Resetowanie hasła](./media/howto-close-active-risk-events/05.png)

**Generuj hasło tymczasowe** — przez wygenerowanie hasła tymczasowego możesz natychmiast przywrócić tożsamość z powrotem do stanu bezpiecznego. Ta metoda wymaga współdziałania z użytkownikami, których to dotyczy, ponieważ chcą wiedzieć, jakie jest hasło tymczasowe. Możesz na przykład wysłać nowe hasło tymczasowe na alternatywny adres e-mail użytkownika lub do Menedżera użytkownika. Ponieważ hasło jest tymczasowe, użytkownik jest monitowany o zmianę hasła podczas kolejnego logowania.

**Wymagaj od użytkownika zresetowania hasła** — wymaganie od użytkowników resetowania haseł umożliwia samoodzyskiwanie bez kontaktowania się z pomocą techniczną lub administratorem. Podobnie jak w przypadku zasad ryzyka użytkownika, ta metoda ma zastosowanie tylko do użytkowników zarejestrowanych w ramach usługi MFA. W przypadku użytkowników, którzy nie zostali jeszcze zarejestrowani dla usługi MFA, ta opcja jest niedostępna.

## <a name="dismiss-all-risk-detections"></a>Odrzuć wszystkie wykrycia ryzyka

Jeśli Resetowanie hasła nie jest opcją dla Ciebie, można również odrzucić wszystkie wykryte zagrożenia. 

![Resetowanie hasła](./media/howto-close-active-risk-events/03.png)

Po kliknięciu przycisku **Odrzuć wszystkie zdarzenia**wszystkie zdarzenia zostaną zamknięte, a zagrożony użytkownik nie jest już narażony. Jednak ponieważ ta metoda nie ma wpływu na istniejące hasło, nie powoduje, że powiązana tożsamość nie zostanie przywrócona do stanu bezpiecznego. Preferowanym przypadkiem użycia tej metody jest usunięty użytkownik z aktywnymi mechanizmami wykrywania ryzyka. 

## <a name="close-individual-risk-detections-manually"></a>Ręczne zamykanie indywidualnych wykrycia ryzyka

Wykrywanie indywidualnych zagrożeń można zamknąć ręcznie. Poprzez ręczne zamykanie wykrywania ryzyka można obniżyć poziom ryzyka użytkownika. Zazwyczaj wykrycia ryzyka są zamykane ręcznie w odpowiedzi na powiązane badanie. Na przykład podczas rozmowy z użytkownikiem wykaże, że aktywne wykrywanie ryzyka nie jest już wymagane. 
 
Podczas ręcznego zamykania wykrywania ryzyka można wykonać jedną z następujących czynności, aby zmienić stan wykrywania ryzyka:

![Akcje](./media/howto-close-active-risk-events/06.png)

- **Rozwiąż** — Jeśli po zbadaniu wykrywania ryzyka podjęto odpowiednie działania naprawcze poza usługą Identity Protection i uważasz, że wykrywanie ryzyka powinno być uznawane za zamknięte, Oznacz zdarzenie jako rozwiązane. Rozwiązane zdarzenia spowodują, że stan wykrywania ryzyka zostanie zamknięty, a wykrywanie ryzyka nie będzie już współtworzyć ryzyka użytkownika.
- **Oznacz jako fałszywe-pozytywne** — w niektórych przypadkach możesz zbadać wykrywanie ryzyka i wykryć, że zostało ono niepoprawnie oflagowane jako ryzykowne. Możesz zmniejszyć liczbę takich wystąpień, oznaczając wykrywanie ryzyka jako FAŁSZ-pozytywne. Dzięki temu algorytmy uczenia maszynowego mogą ulepszyć klasyfikację podobnych zdarzeń w przyszłości. Stan fałszywie dodatnich zdarzeń jest zamknięty i nie będzie już współtworzyć ryzyka użytkownika.
- **Ignoruj** — Jeśli nie podjęto żadnej akcji korygowania, ale chcesz, aby wykrycie ryzyka zostało usunięte z aktywnej listy, możesz oznaczyć wykrycie ryzyka ignorowanie, a stan zdarzenia zostanie zamknięty. Zignorowane zdarzenia nie przyczyniają się do ryzyka użytkownika. Tej opcji należy używać tylko w nietypowych warunkach.
- **Ponowne uaktywnianie** wykrycia ryzyka, które zostały ręcznie zamknięte (poprzez wybranie opcji Rozwiąż, fałszywie dodatni lub Ignoruj) można ponownie uaktywnić, ustawiając stan zdarzenia z powrotem na aktywny. Ponownie uaktywnione wykrycia ryzyka przyczyniają się do obliczenia poziomu ryzyka użytkownika. Wykrywanie ryzyka zamknięte przy użyciu korygowania (na przykład bezpiecznego resetowania hasła) nie może zostać ponownie uaktywnione.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview.md).
