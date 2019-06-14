---
title: Jak zamknąć zdarzeń aktywnego ryzyka w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Poznaj opcje, że masz zdarzeń Zamknij aktywne o podwyższonym ryzyku.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e003aec8fa5aeab587fa07acdae3a13b370a535
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60453507"
---
# <a name="how-to-close-active-risk-events"></a>Instrukcje: Zamykanie aktywnych zdarzeń o podwyższonym ryzyku

Za pomocą [zdarzeń o podwyższonym ryzyku](../reports-monitoring/concept-risk-events.md), Azure Active Directory wykrywa wskaźniki dla kont użytkowników mogą mieć złamane zabezpieczenia. Jako administrator chcesz pobrać wszystkie zdarzenia o podwyższonym ryzyku zamknięte, tak aby narażeni użytkownicy nie są już na ryzyko.

Ten artykuł zawiera omówienie opcji dodatkowych, musisz zamknąć zdarzeń o podwyższonym ryzyku w active.

## <a name="options-to-close-risk-events"></a>Opcje, aby zamknąć zdarzeń o podwyższonym ryzyku 

Stan zdarzenia o podwyższonym ryzyku jest **active** lub **zamknięte**. Wszystkie zdarzenia ryzyka active przyczyniają się do obliczania wartości o nazwie poziomie ryzyka użytkownika. Poziom ryzyka użytkownika jest wskaźnikiem (niskiej, średniej, wysokiej) prawdopodobieństwo, że konto zostało zaatakowane. 

Aby zamknąć zdarzeń o podwyższonym ryzyku w aktywnej, dostępne są następujące opcje:

- Wymagaj hasła, resetowanie zasad ryzyka dla użytkownika

- Resetowanie hasła usługi ręczna
 
- Odrzuć wszystkie zdarzenia o podwyższonym ryzyku 

- Ręczne zamykanie zdarzeń o podwyższonym ryzyku w poszczególnych



## <a name="require-password-reset-with-a-user-risk-policy"></a>Wymagaj hasła, resetowanie zasad ryzyka dla użytkownika

Konfigurując [zasady dostępu warunkowego ryzyka związanego z użytkownikiem](howto-user-risk-policy.md), możesz wymagać zmiany hasła, jeśli zostanie automatycznie wykryty określony poziom ryzyka związanego z użytkownikiem. 

![Resetowanie hasła](./media/howto-close-active-risk-events/13.png)

Resetowanie hasła zamyka wszystkie zdarzenia ryzyka active powiązane użytkownika, a tożsamość ponownie do to bezpieczne. Za pomocą zasad ryzyka dla użytkownika jest preferowaną metodą zamknąć zdarzenia aktywnego ryzyka, ponieważ ta metoda jest zautomatyzowane. Nie zachodzi żadna interakcja między dotyczy użytkownika i pomocy technicznej lub administratorem.

Jednak przy użyciu zasad ryzyka dla użytkownika nie zawsze jest stosowane. To dotyczy, na przykład:

- Użytkownicy, którzy nie został zarejestrowany do uwierzytelniania wieloskładnikowego (MFA).
- Użytkownicy ze zdarzeniami aktywnego ryzyka, które zostały usunięte.
- Badanie, które wykaże, że wykonano zdarzenia ryzyka zgłaszanej przez wiarygodnego użytkownika.


## <a name="manual-password-reset"></a>Resetowanie hasła usługi ręczna

W przypadku konieczności resetowania haseł za pomocą zasad ryzyka dla użytkownika nie jest opcją, możesz uzyskać wszystkie zdarzenia o podwyższonym ryzyku dla użytkownika, zamknięte za pomocą funkcji resetowania hasła ręcznie.

![Resetowanie hasła](./media/howto-close-active-risk-events/04.png)


Określone okno oferuje dwie różne metody, aby zresetować hasło:

![Resetowanie hasła](./media/howto-close-active-risk-events/05.png)


**Generowanie hasła tymczasowego** -przez wygenerować hasło tymczasowe, użytkownik może natychmiast przywrócić tożsamości do stanu bezpiecznego. Ta metoda wymaga interakcji z dotkniętych użytkowników, ponieważ muszą znać hasło tymczasowe. Można na przykład wysłać nowe hasło tymczasowe, alternatywny adres e-mail użytkownika lub jego menedżera. Ponieważ hasło tymczasowe, użytkownik jest monitowany, aby zmienić hasło podczas następnego logowania.


**Wymagaj od użytkowników zresetowania hasła** — wymaganie od użytkowników, resetowania haseł pozwala samoobsługowego odzyskiwania bez łączenia się z działem pomocy technicznej lub administratorem. Podobnie jak w przypadku zasad ryzyka dla użytkownika, ta metoda dotyczy tylko użytkowników, które są zarejestrowane dla usługi MFA. Dla użytkowników, które nie zostały zarejestrowane dla usługi MFA jeszcze ta opcja jest niedostępna.


## <a name="dismiss-all-risk-events"></a>Odrzuć wszystkie zdarzenia o podwyższonym ryzyku

Jeśli hasło resetowania nie jest dostępną opcją w, można również odrzucić wszystkich zdarzeń o podwyższonym ryzyku. 

![Resetowanie hasła](./media/howto-close-active-risk-events/03.png)

Po kliknięciu **Odrzuć wszystkie zdarzenia**, wszystkie zdarzenia są zamknięte i użytkownika, którego dotyczy nie jest już na ryzyko. Jednakże ponieważ ta metoda nie ma wpływu na istniejące hasło, go nie przywrócić związane z tożsamościami to bezpieczne. Przypadek użycia preferowanych dla tej metody jest usuniętego użytkownika ze zdarzeniami aktywnego ryzyka. 


## <a name="close-individual-risk-events-manually"></a>Ręczne zamykanie zdarzeń o podwyższonym ryzyku w poszczególnych

Zdarzenia o podwyższonym ryzyku w poszczególnych można zamknąć ręcznie. Przez ręczne zamykanie zdarzeń o podwyższonym ryzyku, można obniżyć poziom ryzyka użytkownika. Zazwyczaj zdarzenia o podwyższonym ryzyku są zamykane ręcznie w odpowiedzi na powiązane badania. Na przykład po rozmowie z użytkownikiem wykaże, że zdarzenie aktywnego ryzyka jest nie wymagane dłużej. 
 
Ręczne zamykanie zdarzeń o podwyższonym ryzyku, można mieć jedną z następujących czynności, aby zmienić stan zdarzenia o podwyższonym ryzyku:

![Akcje](./media/howto-close-active-risk-events/06.png)

- **Rozwiąż** — Jeśli po przeanalizowaniu zdarzenie o podwyższonym ryzyku, zajęło akcji korygowania odpowiednie poza ochrony tożsamości i uważasz, że zdarzenie o podwyższonym ryzyku powinna być uznana zamknięte, oznaczenia zdarzeń jako rozwiązane. Rozwiązane zdarzenia wartość stanu zdarzenia o podwyższonym ryzyku zamknięte i nie jest już przyczynia się zdarzenie o podwyższonym ryzyku do ryzyka związanego z użytkownikiem.

- **Oznacz jako wyników fałszywie dodatnich** — w niektórych przypadkach może zbadać zdarzenie o podwyższonym ryzyku i odnajdywanie, że został niepoprawnie oflagowana jako ryzykowne. Możesz pomóc zmniejszyć liczbę takich wystąpień, oznaczanie zdarzenia ryzyka jako fałszywie dodatnie. Ułatwi to algorytmów w celu klasyfikacji zdarzenia podobne w przyszłości uczenia maszynowego. Stan zdarzenia fałszywie dodatnie jest zamknięty, a nie wpływają one ryzyka związanego z użytkownikiem.

- **Ignoruj** — Jeśli to ustawienie nie miały żadnych akcji korygowania, ale ma zdarzenia o podwyższonym ryzyku, który ma zostać usunięty z listy aktywnych, można oznaczyć zdarzenie o podwyższonym ryzyku Ignoruj i stanu zdarzenia zostaną zamknięte. Zignorowano zdarzenia nie przyczyniają się do ryzyka związanego z użytkownikiem. Ta opcja powinna być używana tylko w nietypowych sytuacjach.

- **Uaktywnij ponownie** -zdarzeń o podwyższonym ryzyku, które zostały ręcznie zamknięte (przez wybranie rozwiązania, wynik fałszywie dodatni lub Ignoruj) można ponownie uaktywnić, ustawienie stanu zdarzenia aktywne. Zdarzenia ryzyka ponownie uaktywnione przyczyniają się do obliczania poziomu ryzyka użytkownika. Nie można ponownie uaktywnić zdarzenia o podwyższonym ryzyku zamknięty przy użyciu funkcji korygowania (takie jak Resetowanie hasła bezpiecznego).
  

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
