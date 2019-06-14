---
title: Omówienie zabezpieczeń usługi Azure Active Directory (Azure AD) Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Omówienie zabezpieczeń umożliwia wgląd w stan zabezpieczeń swojej organizacji.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 610668768c7baca13cb60caf1d810cced31ebec3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60452953"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Usługa Azure Active Directory Identity Protection — Omówienie zabezpieczeń

[Przegląd zabezpieczeń](https://aka.ms/IdentityProtectionRefresh) zapewnia wgląd w stan zabezpieczeń swojej organizacji. Ułatwia identyfikację potencjalnych ataków i zrozumieć skuteczności zasad.

Omówienie zabezpieczeń szeroko jest podzielona na dwie sekcje:

- Trendy, po lewej stronie, podaj osi czasu ryzyka w organizacji.

- Kafelki po prawej stronie, wyróżnij kluczowe trwające problemy w Twojej organizacji, a także sugerują jak do szybkiego podejmowania działań.


![Omówienie zabezpieczeń](./media/security-overview/01.png)
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>Wykryto nowe ryzykownych użytkowników

Ten wykres przedstawia liczbę nowych ryzykownych użytkowników, które zostały wykryte w wybranym okresie. Można filtrować widok ten wykres przy poziomie ryzyka użytkownika (niskiej, średniej, wysokiej). Umieść kursor nad przyrosty daty UTC, aby wyświetlić liczbę ryzykowne wykryto użytkowników dla tego samego dnia. Kliknięcie na tym wykresie spowoduje wyświetlenie raportu "Ryzykownych użytkowników". Aby skorygować użytkowników, którzy są zagrożone, należy rozważyć zmianę hasła.

### <a name="new-risky-sign-ins-detected"></a>Nowe ryzykownych logowań wykryto

Ten wykres przedstawia liczbę wykrytych w wybranym okresie ryzykowne logowania. Można filtrować widok ten wykres według typu ryzyka logowania (w czasie rzeczywistym lub agregacji) i poziomu ryzyka logowania (niskiej, średniej, wysokiej). Niechronione logowania są logowania pomyślne ryzyka w czasie rzeczywistym, które nie zostały MFA wezwaniem. (Uwaga: Nie można chronić sign-ins, które są ryzykowne z powodu wykrycia w trybie offline w czasie rzeczywistym przy użyciu zasad ryzyka logowania). Umieść kursor nad przyrosty daty UTC, aby wyświetlić liczbę logowań wykryte zagrożenie dla tego samego dnia. Kliknięcie na tym wykresie spowoduje wyświetlenie raportu "Ryzykowne logowania".

## <a name="tiles"></a>Kafelki
 
### <a name="high-risk-users"></a>Użytkownicy o wysokim ryzyku

Kafelek "użytkowników o wysokim ryzyku" pokazuje najnowsze liczba użytkowników z dużym prawdopodobieństwem naruszenia tożsamości. Powinny być priorytetowo do badania. Kliknij Kafelek "użytkowników o wysokim ryzyku" spowoduje przekierowanie do widok filtrowany raportu "Ryzykownych użytkowników", pokazujący tylko użytkownicy mający wysoki poziom ryzyka. Korzystając z tego raportu, możesz dowiedzieć się więcej i korygowanie tych użytkowników za pomocą funkcji resetowania hasła.

![Omówienie zabezpieczeń](./media/security-overview/02.png)


### <a name="medium-risk-users"></a>Użytkownicy średniego ryzyka
Kafelek "średniego ryzyka użytkownicy" przedstawia najnowsze liczba użytkowników z Średnie prawdopodobieństwo naruszenia tożsamości. Kliknij Kafelek "średniego ryzyka użytkownicy" spowoduje przekierowanie do widok filtrowany raportu "Ryzykownych użytkowników" wyświetlanie tylko użytkownicy z poziomem ryzyka średniej. Korzystając z tego raportu, możesz dokładniej zbadać i skorygować tych użytkowników.

### <a name="unprotected-risky-sign-ins"></a>Niechronione ryzykowne logowania

Kafelek "Niechronione ryzykownych logowań" pokazuje ostatniego tygodnia Liczba pomyślnie w czasie rzeczywistym ryzykowne logowania, które nie zostały zablokowane, ani nie zostaną zakwestionowane przez zasady dostępu warunkowego, zasady dotyczące ryzyka związanego z Identity Protection lub usługi MFA na użytkownika uwierzytelniania Wieloskładnikowego. Są to mogą mieć złamane zabezpieczenia logowania, które zakończyły się pomyślnie i MFA nie zostaną zakwestionowane. Aby chronić takich operacji logowania w przyszłości, stosowanie zasad ryzyka logowania. Kliknij Kafelek "Niechronione ryzykownych logowań" nastąpi przekierowanie do bloku konfiguracji zasad ryzyka logowania, w którym można skonfigurować zasad ryzyka logowania, aby wymagać uwierzytelniania Wieloskładnikowego na logowanie z poziomu określonego ryzyka.


### <a name="legacy-authentication"></a>Starsze uwierzytelnianie

Kafelek "Uwierzytelnianie starszych" pokazuje ostatniego tygodnia liczbę uwierzytelnień starszej wersji w Twojej organizacji. Protokoły uwierzytelniania starsze nie obsługują zabezpieczeń nowoczesnych metod, takich jak uwierzytelnianie wieloskładnikowe. Aby zapobiec uwierzytelniania starszej wersji, można zastosować zasady dostępu warunkowego. Kliknij Kafelek "Uwierzytelnianie starszych" nastąpi przekierowanie do "Tożsamość Secure wynik".


### <a name="identity-secure-score"></a>Wynik bezpiecznego tożsamości

Wynik zabezpieczyć tożsamość miary i porównuje poziomu bezpieczeństwa do wzorców branży. Jeśli klikniesz Kafelek "Tożsamość Secure wynik (wersja zapoznawcza)", nastąpi przekierowanie do bloku "Tożsamość Secure wynik (wersja zapoznawcza)", gdzie można dowiedzieć się więcej o zwiększanie poziomu bezpieczeństwa.


## <a name="next-steps"></a>Kolejne kroki

- [Witryna Channel 9: Usługa Azure AD i wyświetlanie tożsamości: Identity Protection w wersji zapoznawczej](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Włączanie usługi Azure Active Directory Identity Protection](enable.md)

