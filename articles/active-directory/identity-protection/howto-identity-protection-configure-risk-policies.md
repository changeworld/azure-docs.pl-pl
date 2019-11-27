---
title: Zasady dotyczące ryzyka — Azure Active Directory Identity Protection
description: Włączanie i Konfigurowanie zasad ryzyka w Azure Active Directory Identity Protection
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
ms.openlocfilehash: 37091b2551d68e241c7179949c3eb1db9a381de6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382174"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Instrukcje: Konfigurowanie i Włączanie zasad ryzyka

Jak opisano w poprzednim artykule, [Zasady ochrony tożsamości](concept-identity-protection-policies.md) mają dwie zasady dotyczące ryzyka, które możemy włączyć w naszym katalogu. 

- Zasady dotyczące ryzyka związanego z logowaniem
- Zasady ryzyka dla użytkowników

![Strona Przegląd zabezpieczeń umożliwiająca Włączanie zasad ryzyka użytkownika i logowania](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Obie zasady działają w celu zautomatyzowania reakcji na wykrycie zagrożeń w środowisku i umożliwiają użytkownikom samodzielne korygowanie w przypadku wykrycia ryzyka. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Wymagania wstępne 

Jeśli organizacja chce zezwolić użytkownikom na samodzielne korygowanie w przypadku wykrycia zagrożeń, użytkownicy muszą być zarejestrowani do samoobsługowego resetowania haseł i platformy Azure Multi-Factor Authentication. Zalecamy [włączenie usługi rejestracji informacji o zabezpieczeniach](../authentication/howto-registration-mfa-sspr-combined.md) w celu uzyskania najlepszego środowiska. Umożliwienie użytkownikom samodzielnego korygowania umożliwia szybsze pobieranie stanu wydajności bez konieczności interwencji administratora. Administratorzy nadal mogą zobaczyć te zdarzenia i zbadać je po fakcie. 

## <a name="choosing-acceptable-risk-levels"></a>Wybór dopuszczalnych poziomów ryzyka

Organizacje muszą decydować o tym, jaki jest poziom ryzyka, aby akceptować możliwości użytkowników i stan zabezpieczeń. 

Zalecenie firmy Microsoft polega na ustawieniu wartości progowej dla zasad ryzyka dla użytkowników na **wysoki** i zasady ryzyka związanego **z logowaniem.**

Wybranie **wysokiego** progu zmniejsza liczbę wyzwalanych zasad i minimalizuje wpływ na użytkowników. Nie dotyczy to jednak wykrycia **niskiego** i **średniego** ryzyka z zasad, co może uniemożliwić atakującemu wykorzystanie tożsamości. Wybranie **niskiego** progu powoduje wprowadzenie dodatkowych przerwań przez użytkownika, ale zwiększoną stan zabezpieczeń.

## <a name="exclusions"></a>Wykluczenia

Wszystkie zasady zezwalają na wykluczenie użytkowników, takich jak [dostęp awaryjny lub konta administratora z przerwaniem](../users-groups-roles/directory-emergency-access.md). Organizacje mogą określić, że muszą wykluczać inne konta z określonych zasad w zależności od sposobu korzystania z kont. Wszystkie wykluczenia powinny być regularnie przeglądane w celu sprawdzenia, czy nadal mają zastosowanie.

## <a name="enable-policies"></a>Włącz zasady

Aby włączyć ryzyko dla użytkownika i zasady dotyczące ryzyka związanego z logowaniem, wykonaj następujące czynności.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **Azure Active Directory** > **zabezpieczenia** > usługi **Identity Protection** > **Omówienie**.
1. Wybierz pozycję **Konfiguruj zasady ryzyka użytkownika**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
      1. **Warunki** - **ryzyko związane z użytkownikiem** polega na ustawieniu tej opcji na **wysoki**.
   1. W obszarze **Controls**
      1. **Dostęp** — zalecenia firmy Microsoft mają **na celu umożliwienie dostępu** i **wymaganie zmiany hasła**.
   1. **Wymuś** - zasad **na**
   1. **Zapisz** — ta akcja spowoduje powrót do strony **Przegląd** .
1. Wybierz pozycję **Konfiguruj zasady dotyczące ryzyka związanego z logowaniem**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
      1.  - **warunki** dotyczące **ryzyka związanego z zalogowaniem** firma Microsoft zaleca ustawienie tej opcji na **średni i wyższy**.
   1. W obszarze **Controls**
      1. **Dostęp** — zalecenia firmy Microsoft mają na celu **umożliwienie dostępu** i **wymaganie uwierzytelniania wieloskładnikowego**.
   1. **Wymuś** - zasad **na**
   1. **Pisał**

## <a name="next-steps"></a>Następne kroki

- [Włączanie zasad rejestracji w usłudze Azure Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Co to jest ryzyko](concept-identity-protection-risks.md)

- [Zbadaj wykryte zagrożenia](howto-identity-protection-investigate-risk.md)

- [Symulowanie wykrywania ryzyka](howto-identity-protection-simulate-risk.md)
