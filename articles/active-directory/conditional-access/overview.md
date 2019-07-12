---
title: Co to jest dostęp warunkowy w usłudze Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak dostęp warunkowy w usłudze Azure Active Directory pomaga w celu zaimplementowania automatyczny dostęp decyzje, które nie są oparte wyłącznie na kto próbuje uzyskać dostęp do zasobu, ale także sposób dostępu do zasobu.
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
ms.openlocfilehash: 370771d44e2fbbf0fca7af453f7425ebc1b1bdcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612617"
---
# <a name="what-is-conditional-access"></a>Co to jest dostęp warunkowy?

Zabezpieczenia są niezwykle ważne dla organizacji korzystających z chmury. Kluczowym aspektem zabezpieczeń w chmurze jest zarządzanie tożsamościami i dostępem do zasobów w chmurze. W świecie zdominowanym przez urządzenia mobilne i rozwiązania chmurowe użytkownicy mogą uzyskiwać dostęp do zasobów organizacji z dowolnego miejsca, za pomocą wielu różnych urządzeń i aplikacji. Dlatego nie wystarczy już tylko kontrolować tego, kto może uzyskać dostęp do zasobu. Aby zapewnić właściwą równowagę między bezpieczeństwem a produktywnością, należy w decyzjach dotyczących kontroli dostępu uwzględnić też to, w jaki sposób uzyskuje się dostęp do zasobu. Przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD) można rozwiązać tego wymagania. Dostęp warunkowy jest funkcją usługi Azure Active Directory. Przy użyciu dostępu warunkowego można zaimplementować decyzji dotyczących kontroli automatyczny dostęp do uzyskiwania dostępu do Twoich aplikacji w chmurze, które są oparte na warunkach.

Po zakończeniu pierwszego czynnika, wymuszane są zasady dostępu warunkowego. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwszy defense wiersza dla scenariuszy, takich jak ataki (DoS) typu "odmowa usługi", ale mogą korzystać z sygnałów z tych zdarzeń (na przykład poziom ryzyka logowania, lokalizacja żądania, i tak dalej) w celu określenia dostępu do.  

![Formant](./media/overview/81.png)

Ten artykuł zawiera omówienie pojęć dotyczących dostępu warunkowego w usłudze Azure AD.

## <a name="common-scenarios"></a>Typowe scenariusze

W świecie zdominowanym przez urządzenia mobilne i rozwiązania chmurowe usługa Azure Active Directory umożliwia logowanie jednokrotne na urządzeniach oraz w usługach i aplikacjach, z dowolnego miejsca. Upowszechnienie różnorodnych urządzeń (w tym urządzeń prywatnych używanych do pracy), pracy poza siecią firmową i zewnętrznych aplikacji SaaS spowodowało, że stoją przed Tobą dwa przeciwstawne cele:

- Umożliwienie użytkownikom produktywnej pracy w dowolnym czasie i miejscu.
- Ochrona zasobów firmowych przez cały czas.

Za pomocą zasad dostępu warunkowego, można zastosować formantów prawo dostępu do wymaganych warunkach. Azure AD warunkowego dostępu zapewnia większe bezpieczeństwo, w zależności od potrzeb i pozostaje poza sposób użytkownika, gdy nie jest.

Poniżej przedstawiono niektóre typowe zagadnienia dostępu, które dostępu warunkowego mogą pomóc Ci:

- **[Ryzyko logowania](conditions.md#sign-in-risk)** : usługa Azure AD Identity Protection wykrywa zagrożenia związane z logowaniem. Jak można ograniczyć dostęp w przypadku wykrycia ryzykownego logowania, wskazującego na złośliwego użytkownika? Co zrobić, aby uzyskać potwierdzenie, że logowanie pochodziło od uprawnionego użytkownika? Co, jeśli wątpliwości są na tyle poważne, że uzasadniają zablokowanie określonym użytkownikom dostępu do aplikacji?  
- **[Lokalizacja sieciowa](location-condition.md)** : usługa Azure AD jest dostępna z każdego miejsca. Co zrobić w przypadku próby uzyskania dostępu z lokalizacji sieciowej poza kontrolą Twojego działu IT? Kombinacja nazwy użytkownika i hasła może wystarczyć do potwierdzenia tożsamości w przypadku prób dostępu z poziomu sieci firmowej. Co zrobić, jeśli potrzebujesz pewniejszego potwierdzenia tożsamości w przypadku prób dostępu z nieoczekiwanych lokalizacji, takich jak inne kraje czy regiony świata? Co, jeśli chcesz zablokować próby dostępu z określonych lokalizacji?  
- **[Zarządzanie urządzeniami](conditions.md#device-platforms)** : w usłudze Azure AD użytkownicy mogą uzyskać dostęp do aplikacji w chmurze za pomocą wielu różnych urządzeń, w tym urządzeń mobilnych i prywatnych. Co zrobić, jeśli chcesz zezwolić tylko na próby dostępu z urządzeń zarządzanych przez Twój dział IT? Co, jeśli chcesz zablokować próby dostępu do aplikacji chmurowych w Twoim środowisku z określonych typów urządzeń?
- **[Aplikacja kliencka](conditions.md#client-apps)** : obecnie można uzyskiwać dostęp do wielu aplikacji w chmurze za pomocą różnego typu aplikacji, na przykład aplikacji internetowych, mobilnych i klasycznych. Co zrobić w przypadku próby dostępu za pomocą typu aplikacji klienckiej, który powoduje znane problemy? Co, jeśli chcesz, aby określone typy aplikacji były używane tylko na urządzeniach zarządzanych przez Twój dział IT?

Te pytania i odpowiedzi powiązanych reprezentują typowych scenariuszy dostępu dla dostępu warunkowego usługi Azure AD.
Dostęp warunkowy jest funkcją usługi Azure Active Directory, która umożliwia obsługę scenariuszy dostępu przy użyciu podejścia opartego na zasadach.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Zasady dostępu warunkowego

Zasady dostępu warunkowego jest definicja scenariusza dostępu przy użyciu następującego wzorca:

![Formant](./media/overview/10.png)


Element **W takiej sytuacji:** w zasadach określa przyczynę uaktywnienia zasad. Taka przyczyna występuje w przypadku spełnienia określonej grupy warunków. W funkcji dostępu warunkowego usługi Azure AD przypisania dwa warunki odtwarzania specjalne uprawnienia:

- **[Użytkownicy](conditions.md#users-and-groups)** : użytkownicy podejmujący próbę uzyskania dostępu ( **„kto”** ).
- **[Aplikacje w chmurze](conditions.md#cloud-apps-and-actions)** : docelowe elementy, do których użytkownicy próbują uzyskać dostęp ( **„co”** ).

Te dwa warunki są obowiązkowe w zasadach dostępu warunkowego. Oprócz dwóch warunków obowiązkowych można zastosować dodatkowe warunki określające sposób uzyskiwania dostępu. Typowe przykłady to użycie urządzenia mobilnego lub próba dostępu z lokalizacji poza siecią firmową. Aby uzyskać więcej informacji, zobacz [warunków w usłudze Azure Active Directory dostępu warunkowego](conditions.md).

Kombinacja warunków przy użyciu usługi kontroli dostępu reprezentuje zasady dostępu warunkowego.

![Formant](./media/overview/51.png)

Przy użyciu dostępu warunkowego usługi Azure AD, można kontrolować sposób autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Cel zasad dostępu warunkowego jest wymuszanie kontroli dostępu dodatkowych w momencie próby dostępu oparte na jak odbywa się próba dostępu do aplikacji w chmurze.

Ochrona dostępu do aplikacji w chmurze na podstawie zasad umożliwia rozpoczęcie projektowania wymagań dotyczących zasad w środowisku, zgodnie ze strukturą omówioną w tym artykule, bez zastanawiania się nad technicznym wdrożeniem tych wymagań.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Dostęp warunkowy usługi Azure AD i uwierzytelniania federacyjnego

Zasady dostępu warunkowego bezproblemowej współpracy z [uwierzytelnianie federacyjne](../../security/azure-ad-choose-authn.md#federated-authentication). Ta obsługa obejmuje wszystkie obsługiwane warunki i kontrolki i wgląd w jaki sposób zasady są stosowane do aktywnego użytkownika logowania [raportów usługi Azure AD](../reports-monitoring/concept-sign-ins.md).

*Uwierzytelnianie federacyjne w usłudze Azure AD* umożliwia obsługę uwierzytelniania użytkowników w usłudze Azure AD przez zaufaną usługę uwierzytelniania. Zaufana usługa uwierzytelniania to na przykład usługi Active Directory Federation Services (AD FS) lub dowolna inna usługa federacyjna. W tej konfiguracji podstawowe uwierzytelnianie użytkownika jest wykonywane w usłudze, a logowanie się do poszczególnych aplikacji jest wykonywane w usłudze Azure AD. Dostęp warunkowy usługi Azure AD jest stosowana przed uzyskaniem dostępu do aplikacji, którą użytkownik uzyskuje dostęp. 

Jeśli skonfigurowane zasady dostępu warunkowego wymaga uwierzytelniania wieloskładnikowego, usługi Azure AD domyślnie przy użyciu usługi Azure MFA. Jeśli używasz usługi federacyjnej na potrzeby uwierzytelniania MFA, możesz skonfigurować usługę Azure AD w celu przekierowywania do usługi federacyjnej, gdy uwierzytelnianie wieloskładnikowe jest wymagane, przez ustawienie wartości `$true` dla elementu `-SupportsMFA` w programie [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). To ustawienie działa w przypadku usług uwierzytelniania federacyjnego, które obsługują żądanie uwierzytelniania MFA wystawione przez usługę Azure AD przy użyciu elementu `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

Gdy użytkownik zaloguje się w usłudze uwierzytelniania federacyjnego, pozostałe wymagania dotyczące zasad, takie jak zgodność urządzeń lub zatwierdzona aplikacja, zostaną obsłużone przez usługę Azure AD.

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Klienci z [licencji firmy Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mają także dostęp do funkcji dostępu warunkowego. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć dostęp warunkowy w danym środowisku, zobacz [Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md).
