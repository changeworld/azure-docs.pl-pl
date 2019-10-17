---
title: Zasady ochrony punktu odniesienia dostępu warunkowego — Azure Active Directory
description: Podstawowe zasady dostępu warunkowego do ochrony organizacji przed typowymi atakami
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd46b8cb734355a8394b7480c6def341cf9700d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430331"
---
# <a name="what-are-baseline-policies"></a>Co to są zasady podstawowe?

Zasady linii bazowej to zestaw wstępnie zdefiniowanych zasad, które pomagają w ochronie organizacji przed wieloma typowymi atakami. Te typowe ataki mogą obejmować rozpylanie, odtwarzanie i wyłudzanie informacji. Zasady linii bazowej są dostępne we wszystkich wersjach usługi Azure AD. Firma Microsoft udostępnia te zasady ochrony linii bazowej wszystkim, ponieważ ataki oparte na tożsamościach zostały spowodowane wzrostem w ciągu kilku ostatnich lat. Celem tych czterech zasad jest upewnienie się, że wszystkie organizacje mają włączony poziom zabezpieczeń na poziomie linii bazowej bez dodatkowych kosztów.  

Zarządzanie dostosowanymi zasadami dostępu warunkowego wymaga licencji Azure AD — wersja Premium.

## <a name="baseline-policies"></a>Zasady punktu odniesienia

![Zasady dostępu warunkowego do punktu odniesienia w Azure Portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Istnieją cztery zasady linii bazowej:

* Wymagaj uwierzytelniania wieloskładnikowego dla administratorów (wersja zapoznawcza)
* Ochrona użytkowników końcowych (wersja zapoznawcza)
* Blokuj starsze uwierzytelnianie (wersja zapoznawcza)
* Wymagaj uwierzytelniania wieloskładnikowego dla zarządzania usługami (wersja zapoznawcza)

Wszystkie cztery z tych zasad będą miały wpływ na starsze przepływy uwierzytelniania, takie jak POP, IMAP i starsze klienckie aplikacje biurowe.

### <a name="require-mfa-for-admins-preview"></a>Wymagaj uwierzytelniania wieloskładnikowego dla administratorów (wersja zapoznawcza)

Ze względu na moc i dostęp do kont administratorów należy je traktować z uwzględnieniem specjalnych zaopieki. Jedną z typowych metod ulepszania ochrony uprzywilejowanych kont jest wymaganie, aby w przypadku logowania się do nich była wymagana silniejsza weryfikacja konta. W Azure Active Directory można uzyskać silniejszą weryfikację konta, wymagając od administratorów rejestracji w usłudze i korzystania z usługi Azure Multi-Factor Authentication.

Wymagaj uwierzytelniania wieloskładnikowego dla administratorów (wersja zapoznawcza) to zasady linii bazowej, które wymagają usługi uwierzytelnianie wieloskładnikowe (MFA) dla następujących ról katalogu, które są traktowane jako największe role usługi Azure AD:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń
* Administrator pomocy technicznej/administrator haseł
* Administrator rozliczeń
* Administrator użytkowników

Jeśli w organizacji są używane te konta w skryptach lub kodzie, należy rozważyć zastępowanie ich [tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Ochrona użytkowników końcowych (wersja zapoznawcza)

Administratorzy o wysokim poziomie uprawnień nie są jedynymi odpowiednimi atakami. Niewłaściwe podmioty mogą kierować do zwykłych użytkowników. Po uzyskaniu dostępu te niewłaściwe osoby mogą zażądać dostępu do informacji uprzywilejowanych w imieniu oryginalnego właściciela konta lub pobrać cały katalog i przeprowadzić atak na wyłudzanie informacji w całej organizacji. Jedną z typowych metod ulepszania ochrony dla wszystkich użytkowników jest wymaganie silniejszej weryfikacji konta w przypadku wykrycia ryzykownego logowania.

**Ochrona użytkowników końcowych (wersja zapoznawcza)** to zasady linii bazowej chroniące wszystkich użytkowników w katalogu. Włączenie tych zasad wymaga, aby wszyscy użytkownicy rejestrowali się w usłudze Azure Multi-Factor Authentication w ciągu 14 dni. Po zarejestrowaniu użytkownicy będą monitowani o uwierzytelnianie MFA tylko w trakcie ryzykownych prób logowania. Naruszone konta użytkowników są blokowane do momentu zresetowania hasła i odrzucenia ryzyka. 

[!NOTE]
Wszyscy użytkownicy, którzy wcześniej oflagowani w związku z ryzykiem, są Zablokowani do momentu zresetowania hasła i odrzucenia ryzyka podczas aktywacji zasad.

### <a name="block-legacy-authentication-preview"></a>Blokuj starsze uwierzytelnianie (wersja zapoznawcza)

Starsze protokoły uwierzytelniania (np. IMAP, SMTP, POP3) to protokoły zwykle używane przez starszych klientów poczty do uwierzytelniania. Starsze protokoły nie obsługują uwierzytelniania wieloskładnikowego. Nawet jeśli masz zasady wymagające uwierzytelniania wieloskładnikowego dla katalogu, niewłaściwy aktor może się uwierzytelnić przy użyciu jednego z tych starszych protokołów i pominąć uwierzytelnianie wieloskładnikowe.

Najlepszym sposobem na ochronę Twojego konta przed złośliwymi żądaniami uwierzytelniania podejmowanymi przez starsze protokoły jest ich zablokowanie.

Zasady linii bazowej **blokowania starszego uwierzytelniania (wersja zapoznawcza)** blokują żądania uwierzytelniania, które są tworzone przy użyciu starszych protokołów. Nowoczesnego uwierzytelniania należy użyć, aby pomyślnie zalogować się dla wszystkich użytkowników. W połączeniu z innymi zasadami odniesienia żądania pochodzące ze starszych protokołów zostaną zablokowane. Dodatkowo wszyscy użytkownicy będą zobowiązani do uwierzytelniania wieloskładnikowego w razie potrzeby. Te zasady nie blokują programu Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Wymagaj uwierzytelniania wieloskładnikowego dla zarządzania usługami (wersja zapoznawcza)

Organizacje korzystają z różnych usług platformy Azure i zarządzają nimi za pomocą narzędzi opartych na Azure Resource Manager, takich jak:

* Azure Portal
* Program Azure PowerShell
* Interfejs wiersza polecenia platformy Azure

Zarządzanie zasobami odbywa się przy użyciu dowolnego z tych narzędzi, co jest wysoce uprzywilejowane. Narzędzia te mogą zmieniać konfiguracje dla całej subskrypcji, takie jak ustawienia usługi i rozliczenia subskrypcji.

Aby chronić uprzywilejowane akcje, ta funkcja wymaga uwierzytelniania wieloskładnikowego **dla** wszystkich użytkowników uzyskujących dostęp do Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

* [Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
* [Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md)
* [Co to jest dostęp warunkowy w Azure Active Directory?](overview.md)
