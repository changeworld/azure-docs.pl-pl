---
title: Zasady ryzyka — ochrona tożsamości usługi Azure Active Directory
description: Włączanie i konfigurowanie zasad ryzyka w usłudze Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707009"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Jak: Konfigurowanie i włączanie zasad ryzyka

Jak dowiedzieliśmy się w poprzednim artykule, [zasady ochrony tożsamości](concept-identity-protection-policies.md) mamy dwie zasady ryzyka, które możemy włączyć w naszym katalogu. 

- Zasady dotyczące ryzyka logowania
- Zasady dotyczące ryzyka użytkownika

![Strona przegląd zabezpieczeń umożliwiająca obsługę zasad ryzyka użytkownika i logowania](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Obie zasady działają w celu zautomatyzowania reagowania na wykrywanie ryzyka w twoim środowisku i umożliwiają użytkownikom samodzielne korygowanie po wykryciu ryzyka. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Wymagania wstępne 

Jeśli organizacja chce umożliwić użytkownikom samodzielne korygowanie po wykryciu zagrożeń, użytkownicy muszą być zarejestrowani zarówno dla samoobsługowego resetowania hasła, jak i uwierzytelniania wieloskładnikowego platformy Azure. Zalecamy [włączenie połączonego środowiska rejestracji informacji o zabezpieczeniach,](../authentication/howto-registration-mfa-sspr-combined.md) aby uzyskać najlepsze środowisko. Umożliwienie użytkownikom samodzielnego korygowania przywraca ich do stanu produkcyjnego szybciej bez konieczności interwencji administratora. Administratorzy nadal mogą zobaczyć te zdarzenia i zbadać je po fakcie. 

## <a name="choosing-acceptable-risk-levels"></a>Wybór dopuszczalnych poziomów ryzyka

Organizacje muszą zdecydować o poziomie ryzyka, które są skłonne zaakceptować równoważenie doświadczenia użytkownika i postawy zabezpieczeń. 

Zalecenie firmy Microsoft polega na ustawieniu progu zasad dotyczących ryzyka użytkownika na **Wysoki,** a zasady ryzyka logowania na **średni i wyższy**.

Wybranie **wysokiego** progu zmniejsza liczbę wyzwolonych zasad i minimalizuje wpływ na użytkowników. Jednak wyklucza wykrywanie **niskiego** i **średniego** ryzyka z zasad, które mogą nie blokować osobie atakującej możliwości wykorzystania naruszonej tożsamości. Wybranie **niskiego** progu powoduje wprowadzenie dodatkowych przerwań użytkownika, ale zwiększoną postawę zabezpieczeń.

## <a name="exclusions"></a>Wykluczenia

Wszystkie zasady pozwalają na wykluczenie użytkowników, takich jak [dostęp awaryjny lub break-glass konta administratora](../users-groups-roles/directory-emergency-access.md). Organizacje mogą określić, że muszą wykluczyć inne konta z określonych zasad na podstawie sposobu, w jaki konta są używane. Wszystkie wyłączenia powinny być regularnie poddawane przeglądowi, aby sprawdzić, czy nadal mają zastosowanie.

Skonfigurowane zaufane [lokalizacje sieciowe](../conditional-access/location-condition.md) są używane przez ochronę tożsamości w niektórych wykrywaniach ryzyka w celu zmniejszenia liczby fałszywych alarmów.

## <a name="enable-policies"></a>Włącz zasady

Aby włączyć zasady ryzyka użytkownika i ryzyka logowania wykonaj następujące kroki.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do**przeglądu****ochrony** > tożsamości**zabezpieczeń** >  **usługi Azure Active Directory** > .
1. Wybierz **pozycję Konfiguruj zasady ryzyka użytkownika**.
   1. W obszarze **Przydziały**
      1. **Użytkownicy** — wybierz **wszystkich użytkowników** lub Wybierz osoby **i grupy,** jeśli ograniczysz wdrożenie.
         1. Opcjonalnie można wykluczyć użytkowników z zasad.
      1. **Warunki** - **Użytkownik ryzyko** Microsoft zalecenie jest ustawienie tej opcji na **Wysoki**.
   1. W obszarze **Kontrole**
      1. **Dostęp** — zalecenie firmy Microsoft polega **na zezwoleniu na dostęp** i **wymaganie zmiany hasła**.
   1. **Wymuszaj zasady** - **włączone**
   1. **Zapisz** — ta akcja spowoduje powrót do strony **Przegląd.**
1. Wybierz **pozycję Konfiguruj zasady ryzyka logowania**.
   1. W obszarze **Przydziały**
      1. **Użytkownicy** — wybierz **wszystkich użytkowników** lub Wybierz osoby **i grupy,** jeśli ograniczysz wdrożenie.
         1. Opcjonalnie można wykluczyć użytkowników z zasad.
      1. **Warunki** - **Ryzyko logowania Microsoft** zaleca, aby ustawić tę opcję na Średni i **wyższy**.
   1. W obszarze **Kontrole**
      1. **Dostęp** — zalecenie firmy Microsoft polega **na zezwoleniu na dostęp** i **wymaganie uwierzytelniania wieloskładnikowego**.
   1. **Wymuszaj zasady** - **włączone**
   1. **Zapisz**

## <a name="next-steps"></a>Następne kroki

- [Włączanie zasad rejestracji uwierzytelniania wieloskładnikowego platformy Azure](howto-identity-protection-configure-mfa-policy.md)

- [Co to jest ryzyko](concept-identity-protection-risks.md)

- [Badanie wykryć ryzyka](howto-identity-protection-investigate-risk.md)

- [Symuluj wykrywanie ryzyka](howto-identity-protection-simulate-risk.md)
