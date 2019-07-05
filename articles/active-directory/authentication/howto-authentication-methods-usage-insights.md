---
title: Użycie metody uwierzytelniania i informacje raportowania (wersja zapoznawcza) — usługi Azure Active Directory
description: Raporty dotyczące haseł usługi Azure AD z samoobsługowego resetowania i użycie metody uwierzytelniania Multi-Factor Authentication
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561023"
---
# <a name="authentication-methods-usage--insights-preview"></a>Użycie metody uwierzytelniania & insights (wersja zapoznawcza)

Użycie & insights umożliwia Ci zrozumieć, jak działają metod uwierzytelniania dla funkcji, takich jak uwierzytelnianie wieloskładnikowe systemu Azure i Samoobsługowe resetowanie haseł w organizacji. Ta funkcja raportowania zapewnia organizacji za pomocą środków, aby zrozumieć, jakie metody są rejestrowane i jak są one używane.

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Następujące role mogą uzyskiwać dostęp do użycia szczegółowych informacji:

- Administrator globalny
- Czytelnik zabezpieczeń
- Administrator zabezpieczeń
- Czytnik raportów

Nie dodatkowych licencji jest wymagana do korzystania z dostępu i szczegółowych informacji. Usługa Azure Multi-Factor Authentication i informacje o licencjonowaniu resetowania haseł można znaleźć na [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Dostęp do użycie metody uwierzytelniania i szczegółowe informacje:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **usługi Azure Active Directory** > **resetowania hasła** > **użycia & insights**.
1. Z **rejestracji** lub **użycia** omówienia, możesz otworzyć wstępnie filtrowane raporty w celu filtrowania na podstawie własnych potrzeb.

![Przegląd użycia i szczegółowych informacji](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Aby uzyskać dostęp do użycia i szczegółowe informacje bezpośrednio, przejdź do [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Ten link spowoduje wyświetlenie omówienie rejestracji.

Zarejestrowanych użytkowników, użytkownicy włączeni i możliwością Kafelki użytkowników Pokaż następujące dane rejestracji dla użytkowników:

- Zarejestrowany: Użytkownik, jest traktowany jako zarejestrowane, jeśli one (lub administratora), zarejestrowano wystarczającej ilości metod uwierzytelniania, aby spełnić zasady samoobsługowego resetowania HASEŁ lub uwierzytelniania wieloskładnikowego w Twojej organizacji.
- Włączone: Użytkownik, jest uznawana za włączone, jeśli są one uwzględnione w zakresie zasad samoobsługowego resetowania HASEŁ. Włączenie samoobsługowego resetowania HASEŁ dla grupy, a następnie użytkownik jest traktowany jako włączona, jeśli w tej grupie. Jeśli samoobsługowego resetowania HASŁA jest włączona dla wszystkich użytkowników, wszyscy użytkownicy w dzierżawie (z wyjątkiem gości) są traktowane jako włączone.
- Zdolne do: Użytkownik jest traktowany jako stanie, jeśli są one zarówno zarejestrowany i włączony. Taki stan oznacza, że mogą wykonywać samoobsługowego resetowania HASEŁ w dowolnym momencie w razie potrzeby.

Kliknięcie dowolnego z tych kafelków lub szczegółowe informacje przedstawiono w nich spowoduje wyświetlenie wstępnie filtrowana lista szczegółów rejestracji.

**Rejestracje** wykres na **rejestracji** karta przedstawia liczbę udane i nieudane uwierzytelnianie metody rejestracji przez metodę uwierzytelniania. **Resetuje** wykres na **użycia** karta przedstawia liczbę pomyślnych i uwierzytelnianie nie powiodło się podczas hasło zresetować przepływ przez metodę uwierzytelniania.

Kliknij jeden z wykresów wyświetlenie wstępnie filtrowana lista rejestracji lub zdarzenia resetowania.

Używanie formantu w rogu górnej, prawej, można zmienić zakres dat dla danych inspekcji, wyświetlany na wykresach rejestracji i resetuje do 24 godzin, 7 dni lub 30 dni.

Data rejestracji z 

### <a name="registration-details"></a>Szczegóły rejestracji

Kliknięcie **użytkowników zarejestrowanych**, **użytkowników**, lub **użytkowników, obsługą** kafelków lub szczegółowych informacjach spowoduje wyświetlenie szczegółów rejestracji.

Raport szczegółów rejestracji zawiera następujące informacje dla każdego użytkownika:

- Name (Nazwa)
- Nazwa użytkownika
- Stan rejestracji (wszystkie, zarejestrowany, nie jest zarejestrowany)
- Włączony stan (wszystkie, włączone, wyłączone)
- Stan możliwością (wszystkie, planowanie, nie stanie)
- Metody (powiadomienia przez aplikacje, kod aplikacji, połączeń telefonicznych, wiadomości SMS, wiadomości E-mail, pytania zabezpieczające)

Za pomocą formantów u góry listy, można wyszukać konkretnego użytkownika i filtrować listę użytkowników, na podstawie kolumn wyświetlane.

### <a name="reset-details"></a>Resetuj szczegóły

Kliknięcie na wykresach rejestracji lub resetuje spowoduje wyświetlenie szczegółów resetowania.

Raport szczegóły resetowania przedstawiono rejestrowanie i Resetowanie zdarzenia z ostatnich 30 dni, w tym:

- Name (Nazwa)
- Nazwa użytkownika
- (Wszystkie wersje, rejestracji, resetowanie) funkcji
- Metoda uwierzytelniania (powiadomienie w aplikacji, kodu aplikacji, połączeń telefonicznych, wiadomości SMS, wiadomości E-mail, pytań zabezpieczających biurowym)
- Stan (wszystkie wersje, Powodzenie, niepowodzenie)

Za pomocą formantów u góry listy, można wyszukać konkretnego użytkownika i filtrować listę użytkowników, na podstawie kolumn wyświetlane.

## <a name="limitations"></a>Ograniczenia

Dane wyświetlane w tych raportach zostanie opóźniony o maksymalnie 60 minut. Pole "Ostatniego odświeżenia" istnieje w witrynie Azure portal, aby zidentyfikować, jak najnowsze dane znajdują się.

Dane dotyczące użycia i szczegółowych informacji nie jest zamiennikiem raporty aktywności usługi Azure Multi-Factor Authentication lub informacje zawarte w raporcie operacji logowania usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- [Praca z raportu użycia metod uwierzytelniania interfejsu API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Wybieranie metod uwierzytelniania dla Twojej organizacji](concept-authentication-methods.md)
- [Połączone środowisko rejestracji](concept-registration-mfa-sspr-combined.md)
