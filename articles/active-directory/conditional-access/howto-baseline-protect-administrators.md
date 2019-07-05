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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560948"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Zasady punktu odniesienia: Wymagać uwierzytelniania Wieloskładnikowego dla administratorów (wersja zapoznawcza)

Użytkownicy z dostępem do kont uprzywilejowanych mają nieograniczony dostęp do danego środowiska. Ze względu na mocy, posiadane przez te konta należy potraktować je za pomocą szczególną uwagę. Jednej wspólnej metody w celu zwiększenia ochrony uprzywilejowanych kont jest wymagają silniejsze formularz weryfikacji konta, gdy są one używane do logowania. W usłudze Azure Active Directory możesz uzyskać silniejsze weryfikacji konta, wymagając uwierzytelniania wieloskładnikowego (MFA).

**Wymagać uwierzytelniania Wieloskładnikowego dla administratorów (wersja zapoznawcza)**  jest [bazowymi zasadami](concept-baseline-protection.md) wymagającym uwierzytelniania Wieloskładnikowego za każdym razem, gdy jeden z następujących ról uprzywilejowanych administrator loguje:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń
* Administrator pomocy technicznej / administrator haseł
* Administrator rozliczeń
* Administrator użytkowników

Podczas włączania uwierzytelniania wymagają MFA, które Administratorzy zasad, powyżej ról administratora dziewięć będą musieli zarejestrować usługi MFA przy użyciu aplikacji uwierzytelniającej. Po zakończeniu rejestracji usługi MFA, Administratorzy muszą wykonać uwierzytelnianie wieloskładnikowe w każdym pojedynczego logowania.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Ponieważ **Wymagaj uwierzytelniania Wieloskładnikowego dla administratorów (wersja zapoznawcza)** zasady mają zastosowanie do wszystkich administratorów krytycznych, kilka zagadnień, które należy podjąć, aby zapewnić bezproblemowe wdrożenie. Te zagadnienia obejmują identyfikowanie użytkowników i zasad usługi w usłudze Azure AD, która nie może lub nie należy wykonywać uwierzytelnianie wieloskładnikowe, a także aplikacji i używanych przez Twoją organizację klientów, które nie obsługują nowoczesnego uwierzytelniania.

### <a name="legacy-protocols"></a>Starszych protokołów

Protokoły uwierzytelniania starszej wersji (IMAP, SMTP, POP3, itp.) są używane przez klientów poczty na wysyłanie żądań uwierzytelniania. Te protokoły nie obsługują uwierzytelniania Wieloskładnikowego. Większość naruszeń konta widoczne dla firmy Microsoft są spowodowane przez nieupoważnione osoby przeprowadzania ataków na starszych protokołów próby Pomiń uwierzytelnianie wieloskładnikowe. Aby upewnić się, że usługa MFA jest wymagana podczas logowania się do konta administratora i nieupoważnione osoby nie pozwalają na pomijanie usługi MFA, ta zasada blokuje wszystkie żądania uwierzytelniania do kont administratorów starszych protokołów.

> [!WARNING]
> Przed włączeniem tej zasady, upewnij się, że administratorów nie są używane protokoły uwierzytelniania w starszej wersji. Zapoznaj się z artykułem [jak: Blokuj starsze uwierzytelnianie do usługi Azure AD przy użyciu dostępu warunkowego](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) Aby uzyskać więcej informacji.

## <a name="enable-the-baseline-policy"></a>Włącz zasady linii bazowej

Zasady **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego dla administratorów (wersja zapoznawcza)** ma wstępnie skonfigurowany i pojawi się u góry po przejściu do bloku dostępu warunkowego w witrynie Azure portal.

Aby włączyć te zasady i chronić administratorów:

1. Zaloguj się do **witryny Azure portal** jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
1. Na liście zasad wybierz **bazowymi zasadami: Wymagać uwierzytelniania Wieloskładnikowego dla administratorów (wersja zapoznawcza)** .
1. Ustaw **Włącz zasady** do **Użyj zasad natychmiast**.
1. Kliknij przycisk **Zapisz**.

> [!WARNING]
> Wystąpił opcję **automatycznie Włącz zasady w przyszłości** podczas tej zasady były w wersji zapoznawczej. Firma Microsoft usunęła tę opcję, aby zminimalizować jej wpływ nagłe użytkownika. Jeśli ta opcja jest zaznaczona, gdy był on dostępny, **nie używaj zasad** jest automatycznie wybierane. Jeśli firma chce korzystać z tych zasad linii bazowej, zobacz powyższe kroki, aby ją włączyć.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony linii bazowej dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków do zabezpieczania infrastruktury tożsamości](../../security/azure-ad-secure-steps.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)
