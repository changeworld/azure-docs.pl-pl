---
title: Co to jest dostęp warunkowy w Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak dostęp warunkowy w Azure Active Directory pomaga zaimplementować zautomatyzowane decyzje dotyczące dostępu, które nie są oparte tylko na tym, kto próbuje uzyskać dostęp do zasobu, ale także o dostępie do zasobu.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d17b354e267d003e23e507ca190b951e3ed4a0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608095"
---
# <a name="what-is-conditional-access"></a>Co to jest dostęp warunkowy?

Zabezpieczenia są niezwykle ważne dla organizacji korzystających z chmury. Kluczowym aspektem zabezpieczeń w chmurze jest zarządzanie tożsamościami i dostępem do zasobów w chmurze. W świecie zdominowanym przez urządzenia mobilne i rozwiązania chmurowe użytkownicy mogą uzyskiwać dostęp do zasobów organizacji z dowolnego miejsca, za pomocą wielu różnych urządzeń i aplikacji. Dlatego nie wystarczy już tylko kontrolować tego, kto może uzyskać dostęp do zasobu. Aby zapewnić właściwą równowagę między bezpieczeństwem a produktywnością, należy w decyzjach dotyczących kontroli dostępu uwzględnić też to, w jaki sposób uzyskuje się dostęp do zasobu. W przypadku dostępu warunkowego Azure Active Directory (Azure AD) można rozwiązać to wymaganie. Dostęp warunkowy jest możliwością Azure Active Directory. Przy użyciu dostępu warunkowego można zaimplementować zautomatyzowane decyzje dotyczące kontroli dostępu w celu uzyskania dostępu do aplikacji w chmurze, które są oparte na warunkach.

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwszy wiersz obrony przed scenariuszami, takimi jak ataki typu "odmowa usługi" (DoS), ale mogą korzystać z sygnałów z tych zdarzeń (na przykład poziomu ryzyka logowania, lokalizacji żądania itp.) w celu określenia dostępu.  

![Formant](./media/overview/81.png)

Ten artykuł zawiera omówienie pojęć związanych z dostępem warunkowym w usłudze Azure AD.

## <a name="common-scenarios"></a>Typowe scenariusze

W świecie zdominowanym przez urządzenia mobilne i rozwiązania chmurowe usługa Azure Active Directory umożliwia logowanie jednokrotne na urządzeniach oraz w usługach i aplikacjach, z dowolnego miejsca. Upowszechnienie różnorodnych urządzeń (w tym urządzeń prywatnych używanych do pracy), pracy poza siecią firmową i zewnętrznych aplikacji SaaS spowodowało, że stoją przed Tobą dwa przeciwstawne cele:

- Umożliwienie użytkownikom produktywnej pracy w dowolnym czasie i miejscu.
- Ochrona zasobów firmowych przez cały czas.

Korzystając z zasad dostępu warunkowego, można zastosować odpowiednie kontrole dostępu w warunkach wymaganych. Dostęp warunkowy usługi Azure AD zapewnia dodatkową ochronę, gdy jest to potrzebne, i pozostaje niezależny od użytkownika, gdy nie jest.

Poniżej przedstawiono niektóre typowe problemy związane z dostępem warunkowym, które mogą ułatwić:

- **[Ryzyko logowania](conditions.md#sign-in-risk)** : usługa Azure AD Identity Protection wykrywa zagrożenia związane z logowaniem. Jak można ograniczyć dostęp w przypadku wykrycia ryzykownego logowania, wskazującego na złośliwego użytkownika? Co zrobić, aby uzyskać potwierdzenie, że logowanie pochodziło od uprawnionego użytkownika? Co, jeśli wątpliwości są na tyle poważne, że uzasadniają zablokowanie określonym użytkownikom dostępu do aplikacji?  
- **[Lokalizacja sieciowa](location-condition.md)** : usługa Azure AD jest dostępna z każdego miejsca. Co zrobić w przypadku próby uzyskania dostępu z lokalizacji sieciowej poza kontrolą Twojego działu IT? Kombinacja nazwy użytkownika i hasła może wystarczyć do potwierdzenia tożsamości w przypadku prób dostępu z poziomu sieci firmowej. Co zrobić, jeśli potrzebujesz pewniejszego potwierdzenia tożsamości w przypadku prób dostępu z nieoczekiwanych lokalizacji, takich jak inne kraje czy regiony świata? Co, jeśli chcesz zablokować próby dostępu z określonych lokalizacji?  
- **[Zarządzanie urządzeniami](conditions.md#device-platforms)** : w usłudze Azure AD użytkownicy mogą uzyskać dostęp do aplikacji w chmurze za pomocą wielu różnych urządzeń, w tym urządzeń mobilnych i prywatnych. Co zrobić, jeśli chcesz zezwolić tylko na próby dostępu z urządzeń zarządzanych przez Twój dział IT? Co, jeśli chcesz zablokować próby dostępu do aplikacji chmurowych w Twoim środowisku z określonych typów urządzeń?
- **[Aplikacja kliencka](conditions.md#client-apps)** : obecnie można uzyskiwać dostęp do wielu aplikacji w chmurze za pomocą różnego typu aplikacji, na przykład aplikacji internetowych, mobilnych i klasycznych. Co zrobić w przypadku próby dostępu za pomocą typu aplikacji klienckiej, który powoduje znane problemy? Co, jeśli chcesz, aby określone typy aplikacji były używane tylko na urządzeniach zarządzanych przez Twój dział IT?

Te pytania i powiązane odpowiedzi reprezentują typowe scenariusze dostępu dla dostępu warunkowego usługi Azure AD.
Dostęp warunkowy jest możliwością Azure Active Directory, która umożliwia obsługę scenariuszy dostępu przy użyciu podejścia opartego na zasadach.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Zasady dostępu warunkowego

Zasady dostępu warunkowego są definicją scenariusza dostępu przy użyciu następującego wzorca:

![Formant](./media/overview/10.png)


Element **W takiej sytuacji:** w zasadach określa przyczynę uaktywnienia zasad. Taka przyczyna występuje w przypadku spełnienia określonej grupy warunków. W przypadku dostępu warunkowego usługi Azure AD dwa warunki przypisania odgrywają specjalną rolę:

- **[Użytkownicy](conditions.md#users-and-groups)** : użytkownicy podejmujący próbę uzyskania dostępu ( **„kto”** ).
- **[Aplikacje w chmurze](conditions.md#cloud-apps-and-actions)** : docelowe elementy, do których użytkownicy próbują uzyskać dostęp ( **„co”** ).

Te dwa warunki są obowiązkowe w zasadach dostępu warunkowego. Oprócz dwóch warunków obowiązkowych można zastosować dodatkowe warunki określające sposób uzyskiwania dostępu. Typowe przykłady to użycie urządzenia mobilnego lub próba dostępu z lokalizacji poza siecią firmową. Aby uzyskać więcej informacji, zobacz [warunki w Azure Active Directory dostęp warunkowy](conditions.md).

Kombinacja warunków z kontrolkami dostępu reprezentuje zasady dostępu warunkowego.

![Formant](./media/overview/51.png)

Za pomocą dostępu warunkowego usługi Azure AD można kontrolować sposób, w jaki autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Celem zasad dostępu warunkowego jest wymuszenie dodatkowych kontroli dostępu podczas próby dostępu do aplikacji w chmurze w oparciu o sposób wykonywania próby dostępu.

Ochrona dostępu do aplikacji w chmurze na podstawie zasad umożliwia rozpoczęcie projektowania wymagań dotyczących zasad w środowisku, zgodnie ze strukturą omówioną w tym artykule, bez zastanawiania się nad technicznym wdrożeniem tych wymagań.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Dostęp warunkowy usługi Azure AD i uwierzytelnianie federacyjne

Zasady dostępu warunkowego działają bezproblemowo z [uwierzytelnianiem federacyjnym](../../security/fundamentals/choose-ad-authn.md#federated-authentication). Ta obsługa obejmuje wszystkie obsługiwane warunki i kontrole oraz wgląd w sposób stosowania zasad do logowania użytkowników aktywnych przy użyciu funkcji [raportowania usługi Azure AD](../reports-monitoring/concept-sign-ins.md).

*Uwierzytelnianie federacyjne w usłudze Azure AD* umożliwia obsługę uwierzytelniania użytkowników w usłudze Azure AD przez zaufaną usługę uwierzytelniania. Zaufana usługa uwierzytelniania to na przykład usługi Active Directory Federation Services (AD FS) lub dowolna inna usługa federacyjna. W tej konfiguracji podstawowe uwierzytelnianie użytkownika jest wykonywane w usłudze, a logowanie się do poszczególnych aplikacji jest wykonywane w usłudze Azure AD. Dostęp warunkowy usługi Azure AD jest stosowany przed uzyskaniem dostępu do aplikacji, do której użytkownik uzyskuje dostęp. 

Gdy skonfigurowane zasady dostępu warunkowego wymagają uwierzytelniania wieloskładnikowego, usługa Azure AD domyślnie korzysta z usługi Azure MFA. Jeśli używasz usługi federacyjnej na potrzeby uwierzytelniania MFA, możesz skonfigurować usługę Azure AD w celu przekierowywania do usługi federacyjnej, gdy uwierzytelnianie wieloskładnikowe jest wymagane, przez ustawienie wartości `$true` dla elementu `-SupportsMFA` w programie [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). To ustawienie działa w przypadku usług uwierzytelniania federacyjnego, które obsługują żądanie uwierzytelniania MFA wystawione przez usługę Azure AD przy użyciu elementu `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

Gdy użytkownik zaloguje się w usłudze uwierzytelniania federacyjnego, pozostałe wymagania dotyczące zasad, takie jak zgodność urządzeń lub zatwierdzona aplikacja, zostaną obsłużone przez usługę Azure AD.

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Klienci z [licencjami Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) również mają dostęp do funkcji dostępu warunkowego. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaimplementować dostęp warunkowy w środowisku, zapoznaj się z tematem [Planowanie wdrożenia dostępu warunkowego w Azure Active Directory](plan-conditional-access.md).
