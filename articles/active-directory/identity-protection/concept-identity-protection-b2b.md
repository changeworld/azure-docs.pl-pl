---
title: Ochrona tożsamości i użytkownicy B2B — usługa Azure Active Directory
description: Korzystanie z ochrony tożsamości z użytkownikami B2B, jak to działa i znane ograniczenia
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881308"
---
# <a name="identity-protection-and-b2b-users"></a>Usługa Identity Protection i użytkownicy B2B

Dzięki współpracy usługi Azure AD B2B organizacje mogą wymuszać zasady oparte na ryzyku dla użytkowników B2B przy użyciu ochrony tożsamości. Te zasady można skonfigurować na dwa sposoby:

- Administratorzy mogą skonfigurować wbudowane zasady oparte na ryzyku ochrony tożsamości, które mają zastosowanie do wszystkich aplikacji, które obejmują użytkowników-gościa.
- Administratorzy mogą skonfigurować swoje zasady dostępu warunkowego, używając ryzyka logowania jako warunku, który obejmuje użytkowników-gości.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Jak ocenia się ryzyko dla użytkowników współpracujących z B2B

Ryzyko dla użytkowników współpracy B2B jest oceniane w ich katalogu macierzystym. Ryzyko logowania w czasie rzeczywistym dla tych użytkowników jest oceniane w katalogu zasobów podczas próby uzyskania dostępu do zasobu.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Ograniczenia ochrony tożsamości dla użytkowników współpracujących z B2B

Istnieją ograniczenia w implementacji ochrony tożsamości dla użytkowników współpracy B2B w katalogu zasobów ze względu na ich tożsamość istniejącą w katalogu macierzystym. Główne ograniczenia są następujące:

- Jeśli użytkownik-gość wyzwoli zasady ryzyka użytkownika ochrony tożsamości, aby wymusić resetowanie hasła, **zostanie on zablokowany.** Ten blok jest spowodowany niemożnością zresetowania haseł w katalogu zasobów.
- **Użytkownicy-goście nie pojawiają się w raporcie ryzykownych użytkowników**. Ta utrata widoczności wynika z oceny ryzyka występującej w katalogu domowym użytkownika B2B.
- Administratorzy **nie mogą odrzucić ani skorygować ryzykownego użytkownika współpracy B2B** w katalogu zasobów. Ta utrata funkcjonalności jest spowodowana tym, że administratorzy w katalogu zasobów nie mają dostępu do katalogu macierzystego użytkownika B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Dlaczego nie mogę skorygować ryzykownych użytkowników współpracy B2B w moim katalogu?

Ocena ryzyka i korygowanie dla użytkowników B2B występuje w ich katalogu macierzystym. Z tego powodu użytkownicy-goście nie pojawiają się w raporcie użytkowników ryzykownych w katalogu zasobów, a administratorzy w katalogu zasobów nie mogą wymusić bezpiecznego resetowania hasła dla tych użytkowników.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Co zrobić, jeśli użytkownik współpracy B2B został zablokowany z powodu zasad opartych na ryzyku w mojej organizacji?

Jeśli ryzykowny użytkownik B2B w katalogu jest zablokowany przez zasady oparte na ryzyku, użytkownik będzie musiał skorygować to ryzyko w katalogu macierzystym. Użytkownicy mogą zaradzić ryzyku, przeprowadzając bezpieczne resetowanie hasła w katalogu macierzystym. Jeśli w katalogu macierzystym nie włączono samoobsługowego resetowania hasła, konieczne będzie skontaktowanie się z personelem informatycznym własnej organizacji, aby administrator ręcznie odrzucił ryzyko lub zresetował hasło.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Jak zapobiec wpływowi zasad opartych na analizie B2B na użytkowników współpracujących z B2B?

Wykluczenie użytkowników B2B z zasad dostępu warunkowego organizacji opartych na ryzyku uniemożliwi użytkownikom B2B wpływanie na ich ocenę ryzyka lub ich zablokowanie. Aby wykluczyć tych użytkowników B2B, utwórz grupę w usłudze Azure AD, która zawiera wszystkich użytkowników-gości w organizacji. Następnie dodaj tę grupę jako wykluczenie dla wbudowanych zasad ryzyka użytkownika ochrony tożsamości i ryzyka logowania, a także wszelkich zasad dostępu warunkowego, które używają ryzyka logowania jako warunku.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

- [Czym jest współpraca B2B w usłudze Azure AD?](../b2b/what-is-b2b.md)
