---
title: Korygowanie ryzyka i odblokowywanie użytkowników w usłudze Azure AD Identity Protection
description: Dowiedz się więcej o opcjach zamykania aktywnych wykrywania ryzyka.
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
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382101"
---
# <a name="remediate-risks-and-unblock-users"></a>Korygowanie ryzyka i odblokowywanie użytkowników

Po zakończeniu [dochodzenia,](howto-identity-protection-investigate-risk.md)będziemy chcieli podjąć działania w celu skorygowanie ryzyka lub odblokować użytkowników. Organizacje mają również możliwość umożliwienia automatycznego korygowania przy użyciu [ich zasad ryzyka.](howto-identity-protection-configure-risk-policies.md) Organizacje powinny starać się zamknąć wszystkie wykrywania ryzyka, które są one prezentowane w okresie czasu, w jaki organizacja jest wygodne. Firma Microsoft zaleca zamykanie zdarzeń tak szybko, jak to możliwe, ponieważ czas ma znaczenie podczas pracy z ryzykiem.

## <a name="remediation"></a>Korekty

Wszystkie aktywne wykrywanie ryzyka przyczyniają się do obliczenia wartości zwanej poziomem ryzyka użytkownika. Poziom ryzyka użytkownika jest wskaźnikiem (niski, średni, wysoki) prawdopodobieństwa naruszenia konta. Jako administrator chcesz zamknąć wszystkie wykrywanie ryzyka, aby użytkownicy, których dotyczy problem, nie stanowili już zagrożenia.

Niektóre wykrywanie zagrożeń może być oznaczone przez ochronę tożsamości jako "Zamknięte (system)", ponieważ zdarzenia nie zostały już uznane za ryzykowne.

Administratorzy mają następujące opcje korygowania:

- Samorekulacja z polityką ryzyka
- Ręczne resetowanie hasła
- Odrzucanie ryzyka użytkownika
- Ręczne zamykanie indywidualnych wykrywania ryzyka

### <a name="self-remediation-with-risk-policy"></a>Samorekulacja z polityką ryzyka

Jeśli zezwolisz użytkownikom na samodzielne korygowanie, za pomocą usługi Azure Multi-Factor Authentication (MFA) i samoobsługowego resetowania hasła (SSPR) w zasadach ryzyka, mogą odblokować się po wykryciu ryzyka. Wykrywania te są następnie uważane za zamknięte. Użytkownicy muszą wcześniej zarejestrować dla usługi Azure MFA i sspr w celu użycia po wykryciu ryzyka.

Niektóre wykrycia nie może zwiększyć ryzyko do poziomu, w którym użytkownik samoremediacji byłoby wymagane, ale administratorzy powinni nadal ocenić te wykrycia. Administratorzy mogą ustalić, że konieczne są dodatkowe środki, takie jak [blokowanie dostępu z lokalizacji](../conditional-access/howto-conditional-access-policy-location.md) lub obniżanie dopuszczalnego ryzyka w swoich zasadach.

### <a name="manual-password-reset"></a>Ręczne resetowanie hasła

Jeśli wymaganie zresetowania hasła przy użyciu zasad ryzyka użytkownika nie jest opcją, administratorzy mogą zamknąć wszystkie wykrywanie ryzyka dla użytkownika za pomocą ręcznego resetowania hasła.

Podczas resetowania hasła dla swoich użytkowników administratorzy mają dwie opcje:

- **Wygeneruj hasło tymczasowe** — generując hasło tymczasowe, można natychmiast przywrócić tożsamość do stanu bezpiecznego. Ta metoda wymaga skontaktowania się z użytkownikami, których dotyczy problem, ponieważ muszą wiedzieć, jakie jest hasło tymczasowe. Ponieważ hasło jest tymczasowe, użytkownik jest monitowany o zmianę hasła na coś nowego podczas następnego logowania.

- **Wymagaj od użytkownika resetowania hasła** — wymaganie od użytkowników resetowania haseł umożliwia samodzielne odzyskiwanie bez kontaktowania się z działem pomocy technicznej lub administratorem. Ta metoda dotyczy tylko użytkowników, którzy są zarejestrowani dla usługi Azure MFA i sspr. Dla użytkowników, którzy nie zostali zarejestrowani, ta opcja nie jest dostępna.

### <a name="dismiss-user-risk"></a>Odrzucanie ryzyka użytkownika

Jeśli resetowanie hasła nie jest opcją, ponieważ na przykład użytkownik został usunięty, można odrzucić wykrywanie ryzyka użytkownika.

Po **kliknięciu przycisku Odrzuć ryzyko użytkownika**wszystkie zdarzenia są zamykane, a użytkownik, którego dotyczy problem, nie jest już zagrożony. Jednak ponieważ ta metoda nie ma wpływu na istniejące hasło, nie przynosi powiązanej tożsamości z powrotem do stanu bezpiecznego. 

### <a name="close-individual-risk-detections-manually"></a>Ręczne zamykanie indywidualnych wykrywania ryzyka

Wykrywanie poszczególnych zagrożeń można zamknąć ręcznie. Zamykając wykrywanie ryzyka ręcznie, można obniżyć poziom ryzyka użytkownika. Zazwyczaj wykrywanie ryzyka są zamykane ręcznie w odpowiedzi na powiązane dochodzenie. Na przykład podczas rozmowy z użytkownikiem ujawnia, że aktywne wykrywanie ryzyka nie jest już wymagane. 
 
Podczas ręcznego zamykania wykrywania ryzyka można podjąć dowolną z następujących czynności, aby zmienić stan wykrywania ryzyka:

- Potwierdź, że użytkownik został naruszony
- Odrzucanie ryzyka użytkownika
- Potwierdzanie bezpieczeństwa logowania
- Potwierdzanie naruszenia zabezpieczeń logowania

## <a name="unblocking-users"></a>Odblokowywanie użytkowników

Administrator może zablokować logowanie na podstawie zasad dotyczących ryzyka lub dochodzeń. Blok może wystąpić na podstawie ryzyka logowania lub użytkownika.

### <a name="unblocking-based-on-user-risk"></a>Odblokowywanie na podstawie ryzyka użytkownika

Aby odblokować konto zablokowane ze względu na ryzyko użytkownika, administratorzy mają następujące opcje:

1. **Resetowanie hasła** — możesz zresetować hasło użytkownika.
1. **Odrzuć ryzyko użytkownika** — zasady ryzyka użytkownika blokuje użytkownika, jeśli skonfigurowany poziom ryzyka użytkownika dla blokowania dostępu został osiągnięty. Można zmniejszyć poziom ryzyka użytkownika, odrzucając ryzyko użytkownika lub ręcznie zamykając zgłoszone wykrywanie ryzyka.
1. **Wyklucz użytkownika z zasad** — jeśli uważasz, że bieżąca konfiguracja zasad logowania powoduje problemy dla określonych użytkowników, możesz wykluczyć z niej użytkowników. Aby uzyskać więcej informacji, zobacz sekcję Wykluczenia w [artykule Jak: Konfigurowanie i włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Wyłącz zasady** — jeśli uważasz, że konfiguracja zasad powoduje problemy dla wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz artykuł [Jak: Konfigurowanie i włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Odblokowanie na podstawie ryzyka logowania

Aby odblokować konto na podstawie ryzyka logowania, administratorzy mają następujące opcje:

1. **Zaloguj się ze znanej lokalizacji lub urządzenia** — częstym powodem zablokowanych podejrzanych logów są próby logowania z nieznanych lokalizacji lub urządzeń. Użytkownicy mogą szybko określić, czy ten powód jest przyczyną blokowania, próbując zalogować się ze znanej lokalizacji lub urządzenia.
1. **Wyklucz użytkownika z zasad** — jeśli uważasz, że bieżąca konfiguracja zasad logowania powoduje problemy dla określonych użytkowników, możesz wykluczyć z niej użytkowników. Aby uzyskać więcej informacji, zobacz sekcję Wykluczenia w [artykule Jak: Konfigurowanie i włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Wyłącz zasady** — jeśli uważasz, że konfiguracja zasad powoduje problemy dla wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz artykuł [Jak: Konfigurowanie i włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie usługi Azure AD Identity Protection, zobacz [omówienie usługi Azure AD Identity Protection](overview-identity-protection.md).
