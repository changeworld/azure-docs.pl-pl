---
title: Raporty dotyczące dostępu i użycia usługi Azure MFA — usługi Azure Active Directory
description: Opisuje sposób użycia funkcji usługi Azure Multi-Factor Authentication — raportów.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f78a3135fca290d50370652b33fe0a4d16a6f83
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369058"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Raporty w usłudze Azure Multi-Factor Authentication

Usługa Azure Multi-Factor Authentication zawiera kilka raportów, które mogą być używane przez Ciebie i Twojej organizacji, które są dostępne za pośrednictwem witryny Azure portal. W poniższej tabeli wymieniono dostępne raporty:

| Raport | Lokalizacja | Opis |
|:--- |:--- |:--- |
| Historia zablokowanego użytkownika | Usługa Azure AD > serwer usługi MFA > Blokowanie/odblokowywanie użytkowników | Przedstawia historię żądań zablokowania lub odblokowania użytkowników. |
| Alerty użycia i nadużyć | Usługa Azure AD > logowania | Zawiera informacje na temat użycia ogólnej, podsumowanie dotyczące użytkowników i szczegóły użytkownika; jak również historię alertów oszustwa przesłanych w zakresie dat., określony. |
| Użycie lokalnych składników | Usługa Azure AD > serwer usługi MFA > raport aktywności | Udostępnia informacje na temat ogólne użycie usługi MFA za pośrednictwem rozszerzenia serwera NPS, usług AD FS, a serwer usługi MFA. |
| Historia pominiętego użytkownika | Usługa Azure AD > serwer usługi MFA > jednorazowe obejście | Zawiera historię żądań ominięcia usługi Multi-Factor Authentication dla użytkownika. |
| Stan serwera | Usługa Azure AD > serwer usługi MFA > Stan serwera | Wyświetla stan serwerów usługi Multi-Factor Authentication skojarzonych z Twoim kontem. |

## <a name="view-mfa-reports"></a>Wyświetl raporty usługi MFA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **serwera MFA**.
3. Wybierz raport, który chcesz wyświetlić.

   ![Raport o stanie serwera serwer usługi MFA w witrynie Azure portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Raport logowań w portalu usługi Azure AD

Za pomocą **raport aktywności logowania** w [witryny Azure portal](https://portal.azure.com), można uzyskać informacje potrzebne do określenia sposobu działania środowiska.

W raporcie logowań można udostępniać informacje na temat użycia zarządzanych aplikacji i rejestrowania działania użytkownika, który zawiera informacje na temat użycia usługi Multi-Factor authentication (MFA). Dane usługi MFA dają wgląd w sposób działania usługi MFA w Twojej organizacji. Umożliwiają one udzielenie odpowiedzi na takie pytania, jak:

- Czy logowanie zostało zakwestionowane przez usługę MFA?
- Jak użytkownik ukończył uwierzytelnianie MFA?
- Dlaczego użytkownik nie mógł ukończyć uwierzytelniania MFA?
- Ilu użytkowników zostało zakwestionowanych przez usługę MFA?
- Ilu użytkowników nie mogło odpowiedzieć na wezwania usługi MFA?
- Jakie są typowe problemy z usługą MFA, na które natykają się użytkownicy końcowi?

Dane te są dostępne za pośrednictwem [witryny Azure portal](https://portal.azure.com) i [interfejsu API raportowania](../reports-monitoring/concept-reporting-api.md).

![Raport logowania usługi Azure AD w witrynie Azure portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktura raportów logowania

Raporty działania logowania dla usługi MFA umożliwiają dostęp do następujących informacji:

**Wymagana usługa MFA:** Czy usługa MFA jest wymagana dla logowania, czy nie. Uwierzytelnianie wieloskładnikowe może być wymagane ze względu na usługę MFA na użytkownika, dostęp warunkowy lub z innych powodów. Możliwe wartości to **tak** lub **nie**.

**Wynik usługi MFA:** Więcej informacji na temat tego, czy spełnione lub odmowa usługi MFA:

- Jeśli uwierzytelnianie MFA powiodło się, ta kolumna zawiera więcej informacji na temat sposobu przeprowadzenia uwierzytelnienia MFA.
   - Azure Multi-Factor Authentication
      - ukończone w chmurze
      - wygasłe z powodu zasad skonfigurowanych w dzierżawie
      - wyświetlono monit o rejestrację
      - zrealizowane przez oświadczenie w tokenie
      - zrealizowane przez oświadczenie dostarczone przez dostawcę zewnętrznego
      - zrealizowane przez silne uwierzytelnianie
      - pominięte, ponieważ zrealizowany przepływ był przepływem logowania brokera systemu Windows
      - pominięte z powodu hasła aplikacji
      - pominięte z powodu lokalizacji
      - pominięte z powodu zarejestrowanego urządzenia
      - pominięte z powodu zapamiętanego urządzenia
      - pomyślnie ukończono
   - Przekierowane do zewnętrznego dostawcy w celu uwierzytelnienia wieloskładnikowego

- Jeśli uwierzytelnianie MFA nie powiodło się, ta kolumna zawiera przyczynę niepowodzenia.
   - Niepowodzenie uwierzytelniania Azure Multi-Factor Authentication;
      - uwierzytelnianie w toku
      - próba zduplikowanego uwierzytelnienia
      - zbyt wiele razy wprowadzono niepoprawny kod
      - nieprawidłowe uwierzytelnienie
      - nieprawidłowy kod weryfikacyjny aplikacji mobilnej
      - błąd konfiguracji
      - połączenie telefoniczne przekierowane do poczty głosowej
      - numer telefonu ma nieprawidłowy format
      - błąd usługi
      - nie można skontaktować się z numerem telefonu użytkownika
      - nie można wysłać powiadomienia aplikacji mobilnej do urządzenia
      - nie można wysłać powiadomienia aplikacji mobilnej
      - uwierzytelnienie odrzucone przez użytkownika
      - użytkownik nie odpowiedział na powiadomienie aplikacji mobilnej
      - użytkownik nie ma żadnych zarejestrowanych metod weryfikacji
      - użytkownik wprowadził nieprawidłowy kod
      - użytkownik wprowadził nieprawidłowy numer PIN
      - użytkownik rozłączył połączenie telefoniczne bez pomyślnego uwierzytelnienia
      - użytkownik jest zablokowany
      - użytkownik nigdy nie wprowadził kodu weryfikacyjnego
      - nie znaleziono użytkownika
      - kod weryfikacyjny został już raz użyty

**Metoda uwierzytelniania usługi MFA:** Metoda uwierzytelniania użytkownika, używany w celu zakończenia uwierzytelniania MFA. Możliwe wartości obejmują:

- Wiadomość SMS
- Powiadomienie aplikacji mobilnej
- Połączenie telefoniczne (numer telefonu uwierzytelniania)
- Kod weryfikacyjny aplikacji mobilnej
- Połączenie telefoniczne (numer telefonu służbowego)
- Połączenie telefoniczne (alternatywny numer telefonu uwierzytelniania)

**Szczegóły uwierzytelniania usługi MFA:** Wyczyszczona wersja numeru telefonu, na przykład: + X XXXXXXXX64.

**Dostęp warunkowy** znaleźć informacje na temat zasad dostępu warunkowego, które wpływ próba logowania, w tym:

- Nazwa zasad
- Udziel kontroli
- Kontrolki sesji
- Wynik

## <a name="powershell-reporting"></a>Raportowanie programu PowerShell

Zidentyfikuj użytkowników, którzy zarejestrowali na potrzeby usługi MFA za pomocą programu PowerShell, który następuje po.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Zidentyfikuj użytkowników, którzy nie zarejestrowano usługi MFA za pomocą programu PowerShell, która jest zgodna.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Kolejne kroki

* [Dla użytkowników](../user-help/multi-factor-authentication-end-user.md)
* [Miejsca wdrożenia](concept-mfa-whichversion.md)
