---
title: Co to jest dostęp warunkowy w usłudze Azure Active Directory?
description: Dowiedz się, jak dostęp warunkowy znajduje się w centrum nowej płaszczyzny sterowania opartej na tożsamości.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b044a4fd4e29bfe35abff7a4b36e5bae783328b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240912"
---
# <a name="what-is-conditional-access"></a>Co to jest dostęp warunkowy?

Nowoczesny obwód zabezpieczeń wykracza obecnie poza sieć organizacji, aby uwzględnić tożsamość użytkownika i urządzenia. Organizacje mogą korzystać z tych sygnałów tożsamości w ramach swoich decyzji dotyczących kontroli dostępu. 

Dostęp warunkowy to narzędzie używane przez usługę Azure Active Directory do łączenia sygnałów, podejmowania decyzji i wymuszania zasad organizacyjnych. Dostęp warunkowy znajduje się w centrum nowej płaszczyzny sterowania sterowanej tożsamością.

![Koncepcyjny sygnał warunkowy plus decyzja o wymuszaniu](./media/overview/conditional-access-signal-decision-enforcement.png)

Zasady dostępu warunkowego w najprostszych są if-then instrukcji, jeśli użytkownik chce uzyskać dostęp do zasobu, a następnie muszą wykonać akcję. Przykład: Menedżer listy płac chce uzyskać dostęp do aplikacji listy płac i jest wymagany do wykonania uwierzytelniania wieloskładnikowego, aby uzyskać do niego dostęp.

Administratorzy mają do czynienia z dwoma podstawowymi celami:

- Umożliwienie użytkownikom produktywnej pracy w dowolnym czasie i miejscu.
- Ochrona zasobów organizacji

Korzystając z zasad dostępu warunkowego, można zastosować odpowiednie kontrolki dostępu w razie potrzeby, aby zapewnić bezpieczeństwo organizacji i pozostać poza sposobem użytkownika, gdy nie jest to potrzebne.

![Koncepcyjny przepływ procesu dostępu warunkowego](./media/overview/conditional-access-overview-how-it-works.png)

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego czynnika. Dostęp warunkowy nie jest przeznaczony jako pierwsza linia obrony organizacji dla scenariuszy, takich jak ataki typu odmowa usługi (DoS), ale może używać sygnałów z tych zdarzeń do określenia dostępu.

## <a name="common-signals"></a>Typowe sygnały

Typowe sygnały, które dostęp warunkowy może uwzględnić przy podejmowaniu decyzji o zasadach, obejmują następujące sygnały:

- Członkostwo użytkownika lub grupy
   - Zasady mogą być kierowane do określonych użytkowników i grup dając administratorom szczegółową kontrolę nad dostępem.
- Informacje o lokalizacji IP
   - Organizacje mogą tworzyć zaufane zakresy adresów IP, które mogą być używane podczas podejmowania decyzji dotyczących zasad. 
   - Administratorzy mogą określać zakresy adresów IP całych krajów, z których mają być blokowe lub zezwalane na ruch.
- Urządzenie
   - Użytkownicy z urządzeniami określonych platform lub oznaczone określonym stanem mogą być używane podczas wymuszania zasad dostępu warunkowego.
- Aplikacja
   - Użytkownicy próbujący uzyskać dostęp do określonych aplikacji mogą wyzwalać różne zasady dostępu warunkowego. 
- Wykrywanie ryzyka w czasie rzeczywistym i obliczane
   - Integracja sygnałów z usługą Azure AD Identity Protection umożliwia zasadom dostępu warunkowego identyfikowanie ryzykownego zachowania logowania. Zasady mogą następnie zmusić użytkowników do zmiany hasła lub uwierzytelniania wieloskładnikowego w celu zmniejszenia poziomu ryzyka lub zablokowania dostępu, dopóki administrator nie podejmie ręcznych działań.
- Zabezpieczenia aplikacji w chmurze firmy Microsoft (MCAS)
   - Umożliwia monitorowanie i kontrolowanie dostępu do aplikacji użytkowników oraz kontrolowanie ich w czasie rzeczywistym, zwiększając widoczność i kontrolę nad dostępem do i działaniami wykonywanymi w środowisku chmury.

## <a name="common-decisions"></a>Wspólne decyzje

- Blokowanie dostępu
   - Najbardziej restrykcyjna decyzja
- Udzielanie dostępu
   - Najmniej restrykcyjna decyzja może nadal wymagać co najmniej jednej z następujących opcji:
      - Wymagaj uwierzytelniania wieloskładnikowego
      - Wymagaj, aby urządzenie było oznaczone jako zgodne
      - Wymagaj urządzenia przyłączającego do usługi Azure AD
      - Wymaganie zatwierdzonej aplikacji klienckiej
      - Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)

## <a name="commonly-applied-policies"></a>Powszechnie stosowane zasady

Wiele organizacji ma typowe problemy z dostępem, z którymi mogą być pomocne zasady dostępu warunkowego, takie jak:

- Wymaganie uwierzytelniania wieloskładnikowego dla użytkowników z rolami administracyjnymi
- Wymaganie uwierzytelniania wieloskładnikowego dla zadań zarządzania platformy Azure
- Blokowanie logowania dla użytkowników próbujących używać starszych protokołów uwierzytelniania
- Wymaganie zaufanych lokalizacji dla rejestracji uwierzytelniania wieloskładnikowego platformy Azure
- Blokowanie lub udzielanie dostępu z określonych lokalizacji
- Blokowanie ryzykownych zachowań logowania
- Wymaganie urządzeń zarządzanych przez organizację dla określonych aplikacji

## <a name="customer-case-studies"></a>Analizy przypadków klientów

Dowiedz się, jak inne organizacje używają dostępu warunkowego usługi Azure AD do definiowania i implementowania decyzji dotyczących automatycznej kontroli dostępu. Poniższe opisy pokazują, w jaki sposób te potrzeby klientów są zaspokojone.

* [Wipro zwiększa produktywność urządzeń mobilnych dzięki narzędziom zabezpieczeń w chmurze firmy Microsoft, aby zwiększyć zaangażowanie klientów.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Zasady dostępu warunkowego w usłudze Azure AD umożliwiły firmie udostępnianie dokumentów, zasobów i aplikacji zaufanym jednostkom zewnętrznym---, które mogą używać własnych poświadczeń--- zachowując kontrolę nad własnymi danymi firmowymi.
* [Aramex delivery limited - Globalna firma logistyczno-transportowa tworzy biuro połączone z chmurą z rozwiązaniem do zarządzania tożsamością i dostępem.](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en) Zapewnienie bezpiecznego dostępu było szczególnie trudne dla zdalnych pracowników Aramex. Firma stosuje teraz dostęp warunkowy, aby umożliwić tym pracownikom zdalnym dostęp do aplikacji SaaS spoza sieci. Reguła dostępu warunkowego decyduje, czy wymusić uwierzytelnianie wieloskładnikowe, dając tylko właściwym osobom odpowiedni dostęp.

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Klienci [posiadający licencje usługi Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mają również dostęp do funkcji dostępu warunkowego. 

## <a name="next-steps"></a>Następne kroki

[Tworzenie zasad dostępu warunkowego kawałek po kawałku](concept-conditional-access-policies.md)

Aby dowiedzieć się, jak zaimplementować dostęp warunkowy w swoim środowisku, zobacz [Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md).

[Dowiedz się więcej o ochronie tożsamości](../identity-protection/overview-v2.md)

[Dowiedz się więcej o usłudze Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)

[Dowiedz się więcej o usłudze Microsoft Intune](/intune/index)