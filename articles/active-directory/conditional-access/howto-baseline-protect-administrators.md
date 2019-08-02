---
title: Zasady linii bazowej wymagają uwierzytelniania wieloskładnikowego dla administratorów — Azure Active Directory
description: Zasady dostępu warunkowego wymagające uwierzytelniania wieloskładnikowego dla administratorów
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
ms.openlocfilehash: 213540a5b6c77146155365133f2cca08eea25351
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608163"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Zasady linii bazowej: Wymagaj uwierzytelniania wieloskładnikowego dla administratorów (wersja zapoznawcza)

Użytkownicy mający dostęp do kont uprzywilejowanych mają nieograniczony dostęp do Twojego środowiska. Ze względu na moc tych kont należy traktować je z uwzględnieniem specjalnych zaopieki. Jedną z typowych metod ulepszania ochrony uprzywilejowanych kont jest wymaganie, aby w przypadku logowania się do nich była wymagana silniejsza weryfikacja konta. W Azure Active Directory można uzyskać silniejszą weryfikację konta przez wymaganie uwierzytelniania wieloskładnikowego (MFA).

**Wymagaj uwierzytelniania wieloskładnikowego dla administratorów (wersja zapoznawcza)**  to [zasady linii bazowej](concept-baseline-protection.md) , które wymagają uwierzytelniania wieloskładnikowego za każdym razem, gdy jedna z następujących ról uprzywilejowanego administratora:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń
* Administrator pomocy technicznej/administrator haseł
* Administrator rozliczeń
* Administrator użytkownika

Po włączeniu zasad Wymagaj uwierzytelniania wieloskładnikowego dla administratorów w celu zarejestrowania usługi MFA przy użyciu aplikacji uwierzytelniającej wymagane są powyższe dziewięć ról administratora. Po zakończeniu rejestracji usługi MFA administratorzy będą musieli wykonywać uwierzytelnianie wieloskładnikowe przy każdym logowaniu.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Ponieważ zasady **Wymagaj uwierzytelniania wieloskładnikowego dla administratorów (wersja zapoznawcza)** mają zastosowanie do wszystkich administratorów o znaczeniu krytycznym, należy wprowadzić kilka kwestii w celu zapewnienia bezproblemowego wdrożenia. Te zagadnienia obejmują Identyfikowanie użytkowników i zasad usługi w usłudze Azure AD, które nie mogą ani nie powinny wykonywać uwierzytelniania MFA, a także aplikacje i klientów używane przez organizację, które nie obsługują nowoczesnego uwierzytelniania.

### <a name="legacy-protocols"></a>Starsze protokoły

Starsze protokoły uwierzytelniania (IMAP, SMTP, POP3 itp.) są używane przez klientów poczty do przesyłania żądań uwierzytelniania. Te protokoły nie obsługują usługi MFA. Większość naruszeń kont widzianych przez firmę Microsoft jest spowodowana przez niewłaściwym uczestnikom ataków na starsze protokoły próbujących obejść usługę MFA. Aby zapewnić, że uwierzytelnianie wieloskładnikowe jest wymagane podczas logowania do konta administracyjnego, a niewłaściwe podmioty nie mogą obejść usługi MFA, te zasady blokują wszystkie żądania uwierzytelnienia skierowane do kont administratorów ze starszych protokołów.

> [!WARNING]
> Przed włączeniem tych zasad upewnij się, że administratorzy nie używają starszych protokołów uwierzytelniania. Zapoznaj się [z artykułem How to: Zablokuj starsze uwierzytelnianie w usłudze Azure AD](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) przy użyciu dostępu warunkowego, aby uzyskać więcej informacji.

## <a name="enable-the-baseline-policy"></a>Włączanie zasad linii bazowej

Zasady linii **bazowej zasad: Funkcja Wymagaj uwierzytelniania wieloskładnikowego dla** administratorów (wersja zapoznawcza) jest wstępnie skonfigurowana i zostanie wyświetlona u góry po przejściu do bloku dostęp warunkowy w Azure Portal.

Aby włączyć te zasady i chronić administratorów:

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **dostęp warunkowy**.
1. Na liście zasad wybierz pozycję **zasady linii bazowej: Wymagaj uwierzytelniania wieloskładnikowego dla administratorów**(wersja zapoznawcza).
1. Ustaw opcję **Włącz zasady** , aby od **razu używać zasad**.
1. Kliknij przycisk **Zapisz**.

> [!WARNING]
> W przyszłości włączono opcję **automatycznego włączania zasad,** gdy te zasady były w wersji zapoznawczej. Firma Microsoft usunęła tę opcję, aby zminimalizować nagły wpływ na użytkowników. W przypadku wybrania tej opcji, gdy była dostępna, nie należy automatycznie wybierać **zasad** . Jeśli chcesz użyć tych zasad linii bazowej, zobacz kroki opisane powyżej, aby je włączyć.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony punktu odniesienia dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md)
* [Co to jest dostęp warunkowy w Azure Active Directory?](overview.md)
