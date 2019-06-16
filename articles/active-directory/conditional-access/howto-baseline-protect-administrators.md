---
title: Bazowe zasady wymagają uwierzytelniania Wieloskładnikowego dla administratorów — usługi Azure Active Directory
description: Zasady dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego dla administratorów
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
ms.openlocfilehash: 4f8b7f281ad5ed8424110696544ffdb49e50ce59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112437"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>Zasady punktu odniesienia: Wymaganie uwierzytelniania MFA dla administratorów

Użytkownicy z dostępem do kont uprzywilejowanych mają nieograniczony dostęp do danego środowiska. Ze względu na mocy, posiadane przez te konta należy potraktować je za pomocą szczególną uwagę. Jednej wspólnej metody w celu zwiększenia ochrony uprzywilejowanych kont jest wymagają silniejsze formularz weryfikacji konta, gdy są one używane do logowania. W usłudze Azure Active Directory możesz uzyskać silniejsze weryfikacji konta, wymagając uwierzytelniania wieloskładnikowego (MFA).

**Wymagać uwierzytelniania Wieloskładnikowego dla administratorów** jest [bazowymi zasadami](concept-baseline-protection.md) wymagającym uwierzytelniania Wieloskładnikowego za każdym razem, gdy jeden z następujących ról uprzywilejowanych administrator loguje:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń
* Administrator pomocy technicznej / administrator haseł
* Administrator rozliczeń
* Administrator użytkowników

Podczas włączania uwierzytelniania wymagają MFA, które Administratorzy zasad, powyżej ról administratora dziewięć będą musieli zarejestrować usługi MFA przy użyciu aplikacji uwierzytelniającej. Po zakończeniu rejestracji usługi MFA, Administratorzy muszą wykonać uwierzytelnianie wieloskładnikowe w każdym pojedynczego logowania.

![Wymagać uwierzytelniania Wieloskładnikowego dla administratorów bazowymi zasadami](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Ponieważ **Wymagaj uwierzytelniania Wieloskładnikowego dla administratorów** zasady mają zastosowanie do wszystkich administratorów krytycznych, kilka zagadnień, które należy podjąć, aby zapewnić bezproblemowe wdrożenie. Te zagadnienia obejmują identyfikowanie użytkowników i zasad usługi w usłudze Azure AD, która nie może lub nie należy wykonywać uwierzytelnianie wieloskładnikowe, a także aplikacji i używanych przez Twoją organizację klientów, które nie obsługują nowoczesnego uwierzytelniania.

### <a name="legacy-protocols"></a>Starszych protokołów

Protokoły uwierzytelniania starszej wersji (IMAP, SMTP, POP3, itp.) są używane przez klientów poczty na wysyłanie żądań uwierzytelniania. Te protokoły nie obsługują uwierzytelniania Wieloskładnikowego. Większość naruszeń konta widoczne dla firmy Microsoft są spowodowane przez nieupoważnione osoby przeprowadzania ataków na starszych protokołów próby Pomiń uwierzytelnianie wieloskładnikowe. Aby upewnić się, że usługa MFA jest wymagana podczas logowania się do konta administratora i nieupoważnione osoby nie pozwalają na pomijanie usługi MFA, ta zasada blokuje wszystkie żądania uwierzytelniania do kont administratorów starszych protokołów.

> [!WARNING]
> Przed włączeniem tej zasady, upewnij się, że administratorów nie są używane protokoły uwierzytelniania w starszej wersji. Zapoznaj się z artykułem [jak: Blokuj starsze uwierzytelnianie do usługi Azure AD przy użyciu dostępu warunkowego](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) Aby uzyskać więcej informacji.

### <a name="user-exclusions"></a>Wykluczenia użytkownika

Ta zasada linii bazowej zapewnia opcji, aby wykluczyć użytkowników. Przed włączeniem zasad dla swojej dzierżawy, zaleca się, z wyłączeniem następujących kont:

* **Dostęp awaryjny** lub **break szkła** konta, aby uniknąć zablokowania konta obowiązujące w dzierżawie. W mało prawdopodobnym scenariuszu, który wszyscy administratorzy z zablokowanym dostępem do Twojej dzierżawy Twoje konto administracyjne dostępu awaryjnego może służyć do logowania się do dzierżawy podejmij kroki, aby odzyskać dostęp.
   * Więcej informacji można znaleźć w artykule [zarządzania kont dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Konta usług** i **usługi zasady**, takich jak konto Azure AD Connect Sync. Konta usług są nieinteraktywnych kont, które nie są powiązane z żadnym określonym użytkownikiem. One są zwykle używane przez usługi zaplecza i zezwolić na dostęp programistyczny do aplikacji. Konta usług powinny być wyłączone, ponieważ usługi MFA nie można wykonać programowo.
   * Jeśli Twoja organizacja ma konta te używane w skryptach lub kod, należy wziąć pod uwagę zastępowała je za pomocą [zarządzanych tożsamości](../managed-identities-azure-resources/overview.md). Jako rozwiązanie tymczasowe możesz wykluczyć te określonych kont z bazowymi zasadami.
* Użytkownicy, którzy nie ma lub nie będzie mógł używać smartfonie.
   * Te zasady wymagają Administratorzy rejestracji usługi MFA za pomocą aplikacji Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Włącz zasady linii bazowej

Zasady **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego dla administratorów** ma wstępnie skonfigurowany i pojawi się u góry po przejściu do bloku dostępu warunkowego w witrynie Azure portal.

Aby włączyć te zasady i chronić administratorów:

1. Zaloguj się do **witryny Azure portal** jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
1. Na liście zasad wybierz **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego dla administratorów**.
1. Ustaw **Włącz zasady** do **Użyj zasad natychmiast**.
1. Dodaj wykluczenia użytkownika, klikając **użytkowników** > **wybierz wykluczonych użytkowników** i wybierając pozycję Użytkownicy, którzy muszą być wyłączone. Kliknij przycisk **wybierz** następnie **gotowe**.
1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony linii bazowej dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków do zabezpieczania infrastruktury tożsamości](../../security/azure-ad-secure-steps.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)
