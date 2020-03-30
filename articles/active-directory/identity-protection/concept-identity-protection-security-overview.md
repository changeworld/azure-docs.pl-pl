---
title: Omówienie zabezpieczeń usługi Azure Active Directory Identity Protection
description: Dowiedz się, jak omówienie zabezpieczeń zapewnia wgląd w stan bezpieczeństwa organizacji.
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
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382198"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Ochrona tożsamości usługi Azure Active Directory — omówienie zabezpieczeń

Omówienie [zabezpieczeń](https://aka.ms/IdentityProtectionRefresh) w witrynie Azure portal zapewnia wgląd w stan bezpieczeństwa organizacji. Pomaga zidentyfikować potencjalne ataki i zrozumieć skuteczność zasad.

"Przegląd zabezpieczeń" jest zasadniczo podzielony na dwie sekcje:

- Trendy po lewej stronie zapewniają harmonogram ryzyka w organizacji.
- Kafelki po prawej stronie podkreślają najważniejsze bieżące problemy w organizacji i podpowiadają, jak szybko podjąć działania.

![Omówienie zabezpieczeń](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>Wykryto nowych, ryzykownych użytkowników

Ten wykres przedstawia liczbę nowych ryzykownych użytkowników, którzy zostali wykryci w wybranym okresie. Widok tego wykresu można filtrować według poziomu ryzyka użytkownika (niski, średni, wysoki). Umieść wskaźnik myszy na przyrostach daty UTC, aby zobaczyć liczbę ryzykownych użytkowników wykrytych w tym dniu. Kliknij w ten temat o "Ryzykownych użytkownikach". Aby skorygować użytkowników, którzy są zagrożeni, należy rozważyć zmianę hasła.

### <a name="new-risky-sign-ins-detected"></a>Wykryto nowe ryzykowne logowania

Ten wykres przedstawia liczbę ryzykownych logów wykrytych w wybranym okresie. Widok tego wykresu można filtrować według typu ryzyka logowania (w czasie rzeczywistym lub agregacji) i poziomu ryzyka logowania (niski, średni, wysoki). Niechronione logowania są pomyślne logowania ryzyka w czasie rzeczywistym, które nie zostały zakwestionowane usługi MFA. (Uwaga: logowania, które są ryzykowne z powodu wykrycia w trybie offline, nie mogą być chronione w czasie rzeczywistym przez zasady ryzyka logowania). Umieść wskaźnik myszy na przyrostach daty UTC, aby zobaczyć liczbę wykrytych logowań na ryzyko dla tego dnia. Kliknięcie tego wykresu spowoduje, że przejdziesz do raportu "Ryzykowne logowania".

## <a name="tiles"></a>Kafelki
 
### <a name="high-risk-users"></a>Użytkownicy wysokiego ryzyka

Kafelek "Użytkownicy wysokiego ryzyka" pokazuje najnowszą liczbę użytkowników z wysokim prawdopodobieństwem naruszenia tożsamości. Powinny one być priorytetem dochodzenia. Kliknięcie kafelka "Użytkownicy wysokiego ryzyka" przekieruje do filtrowanego widoku raportu "Ryzykowni użytkownicy", pokazującego tylko użytkowników o wysokim poziomie ryzyka. Korzystając z tego raportu, można dowiedzieć się więcej i skorygować tych użytkowników za pomocą resetowania hasła.

![Omówienie zabezpieczeń](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Użytkownicy średniego ryzyka
Kafelek "Użytkownicy średniego ryzyka" pokazuje najnowszą liczbę użytkowników ze średnim prawdopodobieństwem naruszenia tożsamości. Kliknięcie kafelka "Użytkownicy średniego ryzyka" przekieruje do filtrowanego widoku raportu "Ryzykowni użytkownicy", pokazującego tylko użytkowników z poziomem ryzyka na nośniku. Korzystając z tego raportu, można dalej badać i korygować tych użytkowników.

### <a name="unprotected-risky-sign-ins"></a>Niechronione ryzykowne logowania

Kafelek "Niechronione ryzykowne logowania" kafelek pokazuje w zeszłym tygodniu liczbę udanych, ryzykownych logów w czasie rzeczywistym, które nie zostały zablokowane lub mfa zakwestionowane przez zasady dostępu warunkowego, zasady ryzyka ochrony tożsamości lub mfa na użytkownika. Są to potencjalnie zagrożone logowania, które zakończyły się powodzeniem, a nie wieloskładnikowe zakwestionowane. Aby chronić takie logowania w przyszłości, należy zastosować zasady ryzyka logowania. Kliknięcie kafelka "Niechronione ryzykowne logowania" przekieruje do bloku konfiguracji zasad ryzyka logowania, gdzie można skonfigurować zasady ryzyka logowania, aby wymagać usługi MFA podczas logowania z określonym poziomem ryzyka.

### <a name="legacy-authentication"></a>Uwierzytelnianie starsze

Kafelek "Uwierzytelnianie starsze" pokazuje liczbę starszych uwierzytelniania w organizacji w zeszłym tygodniu. Starsze protokoły uwierzytelniania nie obsługują nowoczesnych metod zabezpieczeń, takich jak uwierzytelnianie wieloskładnikowe. Aby zapobiec starszemu uwierzytelnianiu, można zastosować zasady dostępu warunkowego. Kliknięcie kafelka "Uwierzytelnianie starsze" przekieruje Cię do "Bezpiecznego wyniku tożsamości".

### <a name="identity-secure-score"></a>Bezpieczny wynik tożsamości

Ocena bezpiecznego tożsamości mierzy i porównuje twoją postawę zabezpieczeń z wzorcami branżowymi. Jeśli klikniesz na kafelku "Identity Secure Score (Preview)", zostanie przekierowany do bloku "Identity Secure Score", gdzie możesz dowiedzieć się więcej o poprawie swojej postawy bezpieczeństwa.

## <a name="next-steps"></a>Następne kroki

- [Co to jest ryzyko](concept-identity-protection-risks.md)

- [Dostępne zasady ograniczania ryzyka](concept-identity-protection-policies.md)
