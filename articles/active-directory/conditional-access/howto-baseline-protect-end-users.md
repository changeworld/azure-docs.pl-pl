---
title: Bazowymi zasadami ochrony użytkownika końcowego (wersja zapoznawcza) — usługi Azure Active Directory
description: Zasady dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego dla użytkowników
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
ms.openlocfilehash: e5b72be0dbe35cf95eed404c7c1407c53f5f2ecb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112350"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Zasady punktu odniesienia: Ochrona użytkowników końcowych (wersja zapoznawcza)

Firma Microsoft zwykle myśleć, że konta administratora, są to jedyne konta, które wymagają ochrony z uwierzytelnianiem wieloskładnikowym (MFA). Administratorzy mają szeroki dostęp do poufnych informacji i wprowadzać zmiany do ustawień na poziomie subskrypcji. Jednakże nieupoważnione osoby zwykle użytkownikom końcowym docelowego. Po uzyskaniu dostępu, nieupoważnione osoby te mogą żądać dostępu do uprzywilejowanych informacje w imieniu oryginalnego właściciela konta lub pobrać cały katalog do wykonania ataku w całej organizacji. Jednej wspólnej metody w celu zwiększenia ochrony dla wszystkich użytkowników jest wymagane mocniejsze formularz weryfikacji konta, takie jak multi factor authentication (MFA).

Do uzyskania odpowiedniej równowagi zabezpieczeń i użyteczności, użytkownicy nie powinien wyświetlony monit o każdym pojedynczego logowania. Żądania uwierzytelniania, które odzwierciedlają zachowanie zwykłego użytkownika, np. z tego samego urządzenia z tej samej lokalizacji ma niskie ryzyko naruszenia zabezpieczeń. Tylko operacje logowania, które zostaną uznane za ryzykowne i Wyświetl właściwości nieuprawnione powinien monit za pomocą usługi MFA wyzwania.

![Wymagać uwierzytelniania Wieloskładnikowego dla użytkowników](./media/howto-baseline-protect-end-users/baseline-policy-end-user-protection.png)

Ochrona użytkowników końcowych jest uwierzytelnianie wieloskładnikowe na podstawie ryzyka [bazowymi zasadami](concept-baseline-protection.md) , która chroni wszyscy użytkownicy w katalogu, w tym wszystkich ról administratora. Włączenie tych zasad wymaga od wszystkich użytkowników do rejestracji usługi MFA przy użyciu aplikacji uwierzytelniającej. Użytkownikom można zignorować wiersz rejestracji uwierzytelniania Wieloskładnikowego przez 14 dni, po upływie których będą blokowani logowanie do momentu rejestracji usługi MFA. Po zarejestrowaniu usługi MFA użytkownicy otrzymują monit dla usługi MFA tylko podczas prób ryzykowne logowania. Naruszenia bezpieczeństwa kont użytkowników są blokowane, aż do resetowania jego hasła i zwinięciu sekcji zdarzeń o podwyższonym ryzyku.

> [!NOTE]
> Ta zasada ma zastosowanie do wszystkich użytkowników, w tym konta gościa i będą oceniane podczas logowania się do wszystkich aplikacji.

## <a name="recovering-compromised-accounts"></a>Odzyskiwanie naruszenia zabezpieczeń kont

Aby lepiej chronić naszych klientów, usługa ujawnione poświadczenia firmy Microsoft znajduje pary publicznie dostępne nazwy użytkownika i hasła. Jeśli są zgodne z jednego z naszych użytkowników zabezpieczamy tego konta natychmiast. Użytkownicy określone jako mające ujawnione poświadczenia zostały potwierdzone naruszenia zabezpieczeń. Ci użytkownicy będą mieć logowanie, dopóki nie zostanie zresetowane swoje hasło.

Użytkownicy z przypisaną licencję usługi Azure AD Premium można przywrócić dostęp za pośrednictwem samoobsługowego resetowania haseł (SSPR), jeśli ta funkcja jest włączona w ich katalogu. Użytkownicy bez licencji premium, które stają się zablokowane, musisz skontaktować się z administratorem, aby przeprowadzić resetowanie ręczne hasła i zamknąć zdarzenie o podwyższonym ryzyku użytkownik z flagą.

### <a name="steps-to-unblock-a-user"></a>Kroki, aby odblokować użytkownika

Upewnij się, że użytkownik został zablokowany przez zasady, sprawdzając dzienniki logowania użytkownika.

1. Administrator musi zalogować się do **witryny Azure portal** i przejdź do **usługi Azure Active Directory** > **użytkowników** > kliknij nazwę użytkownika i nawigowanie do logowania.
1. Aby zainicjować resetowania haseł na zablokowany użytkownik, administrator musi przejść do **usługi Azure Active Directory** > **użytkownicy oflagowani w związku z ryzykiem**
1. Kliknij użytkownika, którego konto jest zablokowane, aby wyświetlić informacje o najnowszych rejestrowania aktywności użytkownika.
1. Kliknij przycisk Resetuj hasło, aby przypisać hasło tymczasowe, które należy zmienić przy następnym logowaniu.
1. Kliknij przycisk Odrzuć wszystkie zdarzenia można zresetować ocenę ryzyka użytkownika.

Użytkownik może teraz logować, zresetować hasło i uzyskać dostęp do aplikacji.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Ponieważ **ochrony użytkownik końcowy** zasady mają zastosowanie do wszystkich użytkowników w katalogu, kilka zagadnień, które należy podjąć, aby zapewnić bezproblemowe wdrożenie. Te zagadnienia obejmują identyfikowanie użytkowników i zasad usługi w usłudze Azure AD, która nie może lub nie należy wykonywać uwierzytelnianie wieloskładnikowe, a także aplikacji i używanych przez Twoją organizację klientów, które nie obsługują nowoczesnego uwierzytelniania.

### <a name="legacy-protocols"></a>Starszych protokołów

Protokoły uwierzytelniania starszej wersji (IMAP, SMTP, POP3, itp.) są używane przez klientów poczty na wysyłanie żądań uwierzytelniania. Te protokoły nie obsługują uwierzytelniania Wieloskładnikowego.  Większość naruszeń konta widoczne dla firmy Microsoft są spowodowane przez nieupoważnione osoby przeprowadzania ataków na starszych protokołów próby Pomiń uwierzytelnianie wieloskładnikowe. Aby upewnić się, że usługa MFA jest wymagana podczas logowania się do konta usługi i nieupoważnione osoby nie pozwalają na pomijanie usługi MFA, ta zasada blokuje wszystkie żądania uwierzytelniania do kont administratorów starszych protokołów.

> [!WARNING]
> Przed włączeniem tej zasady, upewnij się, że użytkownicy nie są za pomocą protokołów uwierzytelniania starszej wersji. Zapoznaj się z artykułem [jak: Blokuj starsze uwierzytelnianie do usługi Azure AD przy użyciu dostępu warunkowego](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) Aby uzyskać więcej informacji.

### <a name="user-exclusions"></a>Wykluczenia użytkownika

Ta zasada linii bazowej zapewnia opcji, aby wykluczyć użytkowników. Przed włączeniem zasad dla swojej dzierżawy, zaleca się, z wyłączeniem następujących kont:

* **Dostęp awaryjny** lub **break szkła** konta, aby uniknąć zablokowania konta obowiązujące w dzierżawie. W mało prawdopodobnym scenariuszu, który wszyscy administratorzy z zablokowanym dostępem do Twojej dzierżawy Twoje konto administracyjne dostępu awaryjnego może służyć do logowania się do dzierżawy podejmij kroki, aby odzyskać dostęp.
   * Więcej informacji można znaleźć w artykule [zarządzania kont dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Konta usług** i **usługi zasady**, takich jak konto Azure AD Connect Sync. Konta usług są nieinteraktywnych kont, które nie są powiązane z żadnym określonym użytkownikiem. One są zwykle używane przez usługi zaplecza i zezwolić na dostęp programistyczny do aplikacji. Konta usług powinny być wyłączone, ponieważ usługi MFA nie można wykonać programowo.
   * Jeśli Twoja organizacja ma konta te używane w skryptach lub kod, należy wziąć pod uwagę zastępowała je za pomocą [zarządzanych tożsamości](../managed-identities-azure-resources/overview.md). Jako rozwiązanie tymczasowe możesz wykluczyć te określonych kont z bazowymi zasadami.
* Użytkownicy, którzy nie ma lub nie będzie mógł używać smartfonie.
   * Ta zasada wymaga od użytkowników rejestracji usługi MFA za pomocą aplikacji Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Włącz zasady linii bazowej

Zasady **bazowymi zasadami: Ochrona użytkowników końcowych (wersja zapoznawcza)** ma wstępnie skonfigurowany i pojawi się u góry po przejściu do bloku dostępu warunkowego w witrynie Azure portal.

Aby włączyć te zasady i chronić użytkowników:

1. Zaloguj się do **witryny Azure portal** jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
1. Na liście zasad wybierz **bazowymi zasadami: Ochrona użytkowników końcowych (wersja zapoznawcza)** .
1. Ustaw **Włącz zasady** do **Użyj zasad natychmiast**.
1. Dodaj wykluczenia użytkownika, klikając **użytkowników** > **wybierz wykluczonych użytkowników** i wybierając pozycję Użytkownicy, którzy muszą być wyłączone. Kliknij przycisk **wybierz** następnie **gotowe**.
1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony linii bazowej dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków do zabezpieczania infrastruktury tożsamości](../../security/azure-ad-secure-steps.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)
