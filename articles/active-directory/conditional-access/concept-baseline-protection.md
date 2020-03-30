---
title: Zasady planu bazowego dostępu warunkowego — usługa Azure Active Directory
description: Podstawowe zasady dostępu warunkowego w celu ochrony organizacji przed typowymi atakami
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767572"
---
# <a name="what-are-baseline-policies"></a>Co to są zasady bazowe?

Zasady bazowe to zestaw wstępnie zdefiniowanych zasad, które pomagają chronić organizacje przed wieloma typowymi atakami. Te typowe ataki mogą obejmować rozpylanie hasła, powtarzanie i wyłudzanie informacji. Zasady linii bazowej są dostępne we wszystkich wersjach usługi Azure AD. Firma Microsoft udostępnia te podstawowe zasady ochrony wszystkim, ponieważ ataki oparte na tożsamości rosną w ciągu ostatnich kilku lat. Celem tych czterech zasad jest zapewnienie, że wszystkie organizacje mają poziom bazowy zabezpieczeń włączony bez dodatkowych kosztów.

Zarządzanie niestandardowymi zasadami dostępu warunkowego wymaga licencji usługi Azure AD Premium.

> [!IMPORTANT]
> Zasady linii bazowej są przestarzałe. Zobacz [Co nowego w usłudze Azure Active Directory?](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>Zasady punktu odniesienia

![Zasady planu bazowego dostępu warunkowego w witrynie Azure portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Istnieją cztery podstawowe zasady:

* Wymagaj usługi MFA dla administratorów (wersja zapoznawcza)
* Ochrona użytkownika końcowego (wersja zapoznawcza)
* Blokowanie uwierzytelniania starszego (wersja zapoznawcza)
* Wymagaj usługi MFA do zarządzania usługami (wersja zapoznawcza)

Wszystkie cztery z tych zasad będą miały wpływ na starsze przepływy uwierzytelniania, takie jak POP, IMAP i starszych klientów pulpitu pakietu Office.

### <a name="exclusions"></a>Wykluczenia

Gdy zasady linii bazowej weszły do początkowej publicznej wersji zapoznawczej, pojawiła się opcja wykluczenia użytkowników z zasad. Ta funkcja ewoluowała w wersji zapoznawczej i została usunięta w lipcu 2019. Organizacje, które już utworzyły wykluczenia, mogły nadal utrzymywać nowych użytkowników, nie mogły dodawać wykluczeń do zasad.

### <a name="require-mfa-for-admins-preview"></a>Wymagaj usługi MFA dla administratorów (wersja zapoznawcza)

Ze względu na uprawnienia i dostęp, które mają konta administratora, należy traktować je ze szczególną ostrożnością. Jedną z typowych metod poprawy ochrony kont uprzywilejowanych jest wymaganie silniejszej formy weryfikacji konta, gdy są one używane do logowania. W usłudze Azure Active Directory można uzyskać silniejszą weryfikację konta, wymagając od administratorów rejestrowania się i używania uwierzytelniania wieloskładnikowego platformy Azure.

Wymagaj usługi MFA dla administratorów (w wersji zapoznawczej) to zasada linii bazowej, która wymaga uwierzytelniania wieloskładnikowego (MFA) dla następujących ról katalogu, uważanych za najbardziej uprzywilejowane role usługi Azure AD:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń
* Administrator pomocy technicznej / Administrator haseł
* Administrator rozliczeń
* Administrator użytkownika

Jeśli organizacja ma te konta w użyciu w skryptach lub kod, należy rozważyć zastąpienie ich [tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Ochrona użytkownika końcowego (wersja zapoznawcza)

Administratorzy o wysokich uprawnieniach nie są jedynymi, którzy są celem ataków. Źli aktorzy mają tendencję do kierowania normalnych użytkowników. Po uzyskaniu dostępu te złe podmioty mogą zażądać dostępu do uprzywilejowanych informacji w imieniu pierwotnego posiadacza konta lub pobrać cały katalog i przeprowadzić atak phishingowy na całą organizację. Jedną z typowych metod poprawy ochrony dla wszystkich użytkowników jest wymaganie silniejszej formy weryfikacji konta po wykryciu ryzykownego logowania.

**Ochrona użytkownika końcowego (wersja zapoznawcza)** to zasada bazowa, która chroni wszystkich użytkowników w katalogu. Włączenie tej zasady wymaga, aby wszyscy użytkownicy zarejestrowali się w celu uzyskania uwierzytelniania wieloskładnikowego platformy Azure w ciągu 14 dni. Po zarejestrowaniu użytkownicy zostaną poproszeni o uwierzytelnianie wieloskładnikowe tylko podczas ryzykownych prób logowania. Konta użytkowników, których bezpieczeństwo zostało naruszone, są blokowane do momentu zresetowania hasła i zwolnienia z ryzyka. 

> [!NOTE]
> Wszyscy użytkownicy wcześniej oflagowani ze bezpieczeństwa są blokowani do czasu zresetowania hasła i ryzyka odrzucenia po aktywacji zasad.

### <a name="block-legacy-authentication-preview"></a>Blokowanie uwierzytelniania starszego (wersja zapoznawcza)

Starsze protokoły uwierzytelniania (np. protokoły IMAP, SMTP, POP3) są protokołami zwykle używanymi przez starszych klientów poczty do uwierzytelniania. Starsze protokoły nie obsługują uwierzytelniania wieloskładnikowego. Nawet jeśli masz zasady wymagające uwierzytelniania wieloskładnikowego dla katalogu, zły aktor może uwierzytelnić się przy użyciu jednego z tych starszych protokołów i pominąć uwierzytelnianie wieloskładnikowe.

Najlepszym sposobem ochrony konta przed złośliwymi żądaniami uwierzytelniania złożonymi przez starsze protokoły jest ich zablokowanie.

Zasady **planu bazowego uwierzytelniania w starszej wersji bloku (wersja zapoznawcza)** blokują żądania uwierzytelniania, które są dokonywane przy użyciu starszych protokołów. Nowoczesne uwierzytelnianie musi służyć do pomyślnego logowania dla wszystkich użytkowników. Używane w połączeniu z innymi zasadami linii bazowej żądania pochodzące ze starszych protokołów zostaną zablokowane. Ponadto wszyscy użytkownicy będą musieli uwierzytelniania Wieloskładnikowych w razie potrzeby. Ta zasada nie blokuje programu Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Wymagaj usługi MFA do zarządzania usługami (wersja zapoznawcza)

Organizacje korzystają z różnych usług platformy Azure i zarządzają nimi za pomocą narzędzi opartych na usłudze Azure Resource Manager, takich jak:

* Portal Azure
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure

Używanie dowolnego z tych narzędzi do zarządzania zasobami jest bardzo uprzywilejowaną akcją. Narzędzia te mogą zmieniać konfiguracje obejmujące całą subskrypcję, takie jak ustawienia usługi i rozliczenia subskrypcji.

Aby chronić akcje uprzywilejowane, ta zasada **wymagaj usługi MFA do zarządzania usługami (w wersji zapoznawczej)** będzie wymagać uwierzytelniania wieloskładnikowego dla każdego użytkownika uzyskującego dostęp do witryny Azure portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

* [Włączanie ustawień domyślnych zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md)
* [Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
* [Pięć kroków do zabezpieczenia infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md)
