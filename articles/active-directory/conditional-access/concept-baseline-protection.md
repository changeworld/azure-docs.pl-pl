---
title: Zasady dostępu warunkowego punktu odniesienia ochrona — Azure Active Directory
description: Zasady dostępu warunkowego punktu odniesienia do ochrony organizacji przed typowymi atakami
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
ms.openlocfilehash: 39a591a335d022ef7b2b99fdec930ddf0496cd47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112589"
---
# <a name="what-are-baseline-policies"></a>Co to są zasady linii bazowej?

Zasady linii bazowej to zestaw wstępnie zdefiniowane zasady, które pomagają w ochronie organizacji przed wieloma typowymi atakami. Te typowymi atakami może zawierać hasła osłony, metodą powtórzeń i wyłudzania informacji. Zasady linii bazowej są dostępne we wszystkich wersjach programu Azure AD. Microsoft udostępnia te zasady ochrony linii bazowej dla wszystkich użytkowników ponieważ atakami opartymi na tożsamości zostały rośnie w ciągu ostatnich kilku lat. Celem tych czterech zasad jest upewnij się, że wszystkie organizacje poziomie linii bazowej zabezpieczeń włączone bez dodatkowych kosztów.  

Zarządzanie dostosowane zasady dostępu warunkowego wymaga licencji usługi Azure AD Premium.

## <a name="baseline-policies"></a>Zasady punktu odniesienia

![Zasady dostępu warunkowego dostępu do linii bazowej w witrynie Azure portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Istnieją cztery zasady linii bazowej, umożliwiające organizacjom:

* [Wymagać uwierzytelniania Wieloskładnikowego dla administratorów](howto-baseline-protect-administrators.md)
* [Ochrona użytkowników końcowych (wersja zapoznawcza)](howto-baseline-protect-end-users.md)
* [Blokuj starsze uwierzytelnianie (wersja zapoznawcza)](howto-baseline-protect-legacy-auth.md)
* [Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)](howto-baseline-protect-azure.md)

Wszystkie cztery te zasady wpłynie na przepływów uwierzytelniania starszej wersji, takich jak POP, IMAP i starszych klientów klasycznych pakietu Office.

### <a name="require-mfa-for-admins"></a>Wymaganie uwierzytelniania MFA dla administratorów

Z powodu siły i dostępu, który ma konta administratora należy potraktować je za pomocą szczególną uwagę. Jednej wspólnej metody w celu zwiększenia ochrony uprzywilejowanych kont jest wymagają silniejsze formularz weryfikacji konta, gdy są one używane do logowania. W usłudze Azure Active Directory możesz uzyskać silniejsze weryfikacji konta, wymagając Administratorzy zarejestrować i używać usługi Azure Multi-Factor Authentication.

[Wymagać uwierzytelniania Wieloskładnikowego dla administratorów](howto-baseline-protect-administrators.md) jest zasada linii bazowej, która wymaga uwierzytelniania wieloskładnikowego (MFA) dla następujących ról katalogów uważane za najbardziej uprzywilejowanych ról usługi Azure AD:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń
* Administrator pomocy technicznej / administrator haseł
* Administrator rozliczeń
* Administrator użytkowników

Jeśli Twoja organizacja ma konta te używane w skryptach lub kod, należy wziąć pod uwagę zastępowała je za pomocą [zarządzanych tożsamości](../managed-identities-azure-resources/overview.md). Jako rozwiązanie tymczasowe można wykluczyć określone konta użytkowników z zasad linii bazowej.

### <a name="end-user-protection-preview"></a>Ochrona użytkowników końcowych (wersja zapoznawcza)

Wysoka uprzywilejowani administratorzy nie są jedynymi osobami użytej w ataki. Nieupoważnione osoby mają tendencję do normalnych użytkowników. Po uzyskaniu dostępu, nieupoważnione osoby te można zażądać dostępu do uprzywilejowanych informacje w imieniu oryginalnego właściciela konta lub pobrać cały katalog i wykonywać na atak wyłudzania informacji w całej organizacji. Jednej wspólnej metody w celu zwiększenia ochrony dla wszystkich użytkowników jest wykryciu ryzykowne logowania będą musieli silniejsze formularz weryfikacji konta.

**Ochrona użytkowników końcowych (wersja zapoznawcza)** to zasady linii bazowej, które chroni wszyscy użytkownicy w katalogu. Włączenie tych zasad wymaga od wszystkich użytkowników do zarejestrowania dla usługi Azure Multi-Factor Authentication w ciągu 14 dni. Po zarejestrowaniu użytkownicy otrzymują monit dla usługi MFA tylko podczas prób ryzykowne logowania. Naruszenia bezpieczeństwa kont użytkowników są blokowane, aż zresetować hasło i ryzyka zwolnienia.

### <a name="block-legacy-authentication-preview"></a>Blokuj starsze uwierzytelnianie (wersja zapoznawcza)

Protokoły uwierzytelniania w starszej wersji (np: IMAP, SMTP, POP3) są protokoły zwykle używane przez starszych klientów poczty do uwierzytelniania. Starszych protokołów nie obsługują uwierzytelniania wieloskładnikowego. Nawet jeśli masz zasady, wymagając uwierzytelniania wieloskładnikowego dla katalogu, nieuprawnione można uwierzytelniać przy użyciu jednej z tych starszych protokołów i obejście usługi Multi-Factor authentication.

Najlepszym sposobem, aby chronić swoje konto z żądań uwierzytelniania złośliwego przez starszych protokołów jest je zablokować.

**Bloku uwierzytelniania starszej wersji (wersja zapoznawcza)** bazowymi zasadami blokuje żądania uwierzytelniania, które są wykonywane przy użyciu starszych protokołów. Nowoczesnego uwierzytelniania musi być użyte do zalogowania się pomyślnie dla wszystkich użytkowników. Używany w połączeniu z innymi zasadami linii bazowej, będą blokowane żądań przesyłanych przez starszych protokołów. Ponadto wszyscy użytkownicy będą musieli usługi MFA w każdym przypadku, gdy wymagane. Ta zasada nie blokuje programu Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)

Organizacje korzystają z różnych usług platformy Azure i zarządzać nimi z usługi Azure Resource Manager, na podstawie narzędzi, takich jak:

* Azure Portal
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure

Przy użyciu dowolnej z tych narzędzi do wykonywania funkcji zarządzania zasobami jest wysoce uprzywilejowanych akcji. Te narzędzia można zmienić konfiguracji na poziomie subskrypcji, takich jak ustawienia usługi i rozliczeń subskrypcji.

Do ochrony uprzywilejowanych akcji to **wymagają usługi MFA do zarządzania usługami (wersja zapoznawcza)** zasad będzie wymagać uwierzytelniania wieloskładnikowego dla każdego użytkownika, dostęp do witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure.

## <a name="enable-a-baseline-policy"></a>Włącz zasady linii bazowej

Aby włączyć zasadę punktu odniesienia:

1. Zaloguj się do **witryny Azure portal** jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
1. Na liście zasad wybierz zasadę linii bazowej, którą chcesz włączyć.
1. Ustaw **Włącz zasady** do **na**.
1. Kliknij przycisk Zapisz.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Pięć kroków do zabezpieczania infrastruktury tożsamości](../../security/azure-ad-secure-steps.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)
* [Wymagać uwierzytelniania Wieloskładnikowego dla administratorów](howto-baseline-protect-administrators.md)
* [Ochrona użytkowników końcowych (wersja zapoznawcza)](howto-baseline-protect-end-users.md)
* [Blokuj starsze uwierzytelnianie (wersja zapoznawcza)](howto-baseline-protect-legacy-auth.md)
* [Wymagać uwierzytelniania Wieloskładnikowego do zarządzania usługami (wersja zapoznawcza)](howto-baseline-protect-azure.md)