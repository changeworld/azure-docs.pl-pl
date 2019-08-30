---
title: Zasady linii bazowej ochrona użytkowników końcowych (wersja zapoznawcza) — Azure Active Directory
description: Zasady dostępu warunkowego wymagające uwierzytelniania wieloskładnikowego dla użytkowników
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1a2e0bad39b54edc153416e4120bbc6912578c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125453"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Zasady linii bazowej: Ochrona użytkowników końcowych (wersja zapoznawcza)

Chcemy myśleć, że konta administratorów są jedynymi kontami, które wymagają ochrony przy użyciu uwierzytelniania wieloskładnikowego (MFA). Administratorzy mają szeroki dostęp do poufnych informacji i mogą wprowadzać zmiany w ustawieniach całej subskrypcji. Niemniej jednak w przypadku nieprawidłowych uczestników chcemy kierować użytkowników końcowych. Po uzyskaniu dostępu te złe osoby mogą zażądać dostępu do informacji uprzywilejowanych w imieniu oryginalnego właściciela konta lub pobrać cały katalog w celu przeprowadzenia ataku phishingowego w całej organizacji. Jedną z typowych metod ulepszania ochrony dla wszystkich użytkowników jest wymaganie silniejszej weryfikacji konta, takiej jak uwierzytelnianie wieloskładnikowe (MFA).

Aby zapewnić rozsądną równowagę w zakresie bezpieczeństwa i użyteczności, użytkownicy nie powinni otrzymywać monitów za każdym razem, gdy zalogują się. Żądania uwierzytelniania odzwierciedlające normalne zachowanie użytkowników, takie jak logowanie z tego samego urządzenia z tej samej lokalizacji, mają niewielki kompromis. Tylko logowania uznawane za ryzykowne i pokazujące właściwości niewłaściwego aktora powinny być monitowani z wyzwaniami MFA.

Ochrona użytkowników końcowych jest [zasadami odniesienia](concept-baseline-protection.md) MFA opartymi na ryzyku, które chronią wszystkich użytkowników w katalogu, w tym wszystkich ról administratorów. Włączenie tych zasad wymaga, aby wszyscy użytkownicy rejestrowali się w usłudze MFA przy użyciu aplikacji Authenticator. Użytkownicy mogą zignorować monit rejestracji usługi MFA przez 14 dni, po upływie którego nie będzie można zalogować się do momentu zarejestrowania się w usłudze MFA. Po zarejestrowaniu usługi MFA zostanie wyświetlony monit dotyczący uwierzytelniania wieloskładnikowego tylko podczas ryzykownych prób logowania. Naruszone konta użytkowników są blokowane do momentu zresetowania hasła i wykrycia ryzyka zostały odrzucone.

> [!NOTE]
> Te zasady mają zastosowanie do wszystkich użytkowników, w tym kont gościa, i zostaną ocenione podczas logowania do wszystkich aplikacji.

## <a name="recovering-compromised-accounts"></a>Odzyskiwanie naruszonych kont

Aby pomóc w ochronie naszych klientów, usługa nieujawniona poświadczeń firmy Microsoft znajduje publicznie dostępną parę nazw i haseł. Jeśli są one zgodne z jednym z naszych użytkowników, możemy bezpiecznie zabezpieczyć to konto. Użytkownicy zidentyfikowani jako mający ujawnione poświadczenia są potwierdzane. Użytkownicy będą mogli logować się do momentu zresetowania hasła.

Użytkownicy, którym przypisano licencję Azure AD — wersja Premium, mogą przywrócić dostęp za pomocą funkcji samoobsługowego resetowania hasła (SSPR), jeśli ta funkcja jest włączona w swoim katalogu. Użytkownicy bez licencji Premium, która stanie się zablokowana, muszą skontaktować się z administratorem w celu przeprowadzenia ręcznego resetowania hasła i odrzucić wykrywanie ryzyka przez oflagowane użytkownika.

### <a name="steps-to-unblock-a-user"></a>Procedura odblokowywania użytkownika

Sprawdź, czy użytkownik został zablokowany przez zasady, sprawdzając dzienniki logowania użytkownika.

1. Administrator musi zalogować się do **Azure Portal** i przejść do **Azure Active Directory** > **użytkowników** > kliknij nazwę użytkownika i przejdź do logowania.
1. Aby można było inicjować Resetowanie hasła na zablokowanym użytkowniku, administrator musi przejść do **Azure Active Directory** > **użytkowników oflagowanych w celu ryzyka**
1. Kliknij użytkownika, którego konto zostało zablokowane, aby wyświetlić informacje o ostatnim działaniu logowania użytkownika.
1. Kliknij przycisk Resetuj hasło, aby przypisać hasło tymczasowe, które należy zmienić przy następnym logowaniu.
1. Kliknij pozycję Odrzuć wszystkie zdarzenia, aby zresetować ocenę ryzyka użytkownika.

Użytkownik może teraz zalogować się, zresetować swoje hasło i uzyskać dostęp do aplikacji.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Ponieważ zasady **ochrony użytkowników końcowych** mają zastosowanie do wszystkich użytkowników w katalogu, należy wprowadzić kilka kwestii w celu zapewnienia bezproblemowego wdrożenia. Te zagadnienia obejmują Identyfikowanie użytkowników i zasad usługi w usłudze Azure AD, które nie mogą ani nie powinny wykonywać uwierzytelniania MFA, a także aplikacje i klientów używane przez organizację, które nie obsługują nowoczesnego uwierzytelniania.

### <a name="legacy-protocols"></a>Starsze protokoły

Starsze protokoły uwierzytelniania (IMAP, SMTP, POP3 itp.) są używane przez klientów poczty do przesyłania żądań uwierzytelniania. Te protokoły nie obsługują usługi MFA.  Większość naruszeń kont widzianych przez firmę Microsoft jest spowodowana przez niewłaściwym uczestnikom ataków na starsze protokoły próbujących obejść usługę MFA. Aby zapewnić, że uwierzytelnianie wieloskładnikowe jest wymagane w przypadku logowania się do konta, a niewłaściwe podmioty nie mogą obejść usługi MFA, te zasady blokują wszystkie żądania uwierzytelniania skierowane do kont administratorów ze starszych protokołów.

> [!WARNING]
> Przed włączeniem tych zasad upewnij się, że użytkownicy nie używają starszych protokołów uwierzytelniania. Zapoznaj się [z artykułem How to: Zablokuj starsze uwierzytelnianie w usłudze Azure AD](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) przy użyciu dostępu warunkowego, aby uzyskać więcej informacji.

## <a name="enable-the-baseline-policy"></a>Włączanie zasad linii bazowej

Zasady linii **bazowej zasad: Ochrona użytkowników końcowych (wersja** zapoznawcza) jest wstępnie skonfigurowana i zostanie wyświetlona u góry po przejściu do bloku dostęp warunkowy w Azure Portal.

Aby włączyć te zasady i chronić użytkowników:

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **dostęp warunkowy**.
1. Na liście zasad wybierz pozycję **zasady linii bazowej: Ochrona użytkowników końcowych (wersja**zapoznawcza).
1. Ustaw opcję **Włącz zasady** , aby od **razu używać zasad**.
1. Kliknij polecenie **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony punktu odniesienia dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md)
* [Co to jest dostęp warunkowy w Azure Active Directory?](overview.md)
