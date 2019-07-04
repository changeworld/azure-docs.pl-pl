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
ms.openlocfilehash: a9cf0b848e551d3c0e7d7275af9eb2c8e970dbd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113396"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Raporty w usłudze Azure Multi-Factor Authentication

Usługa Azure Multi-Factor Authentication zawiera kilka raportów, które mogą być używane przez Ciebie i Twojej organizacji, które są dostępne za pośrednictwem witryny Azure portal. W poniższej tabeli wymieniono dostępne raporty:

| Raport | Lokalizacja | Opis |
|:--- |:--- |:--- |
| Historia zablokowanych użytkowników | Usługa Azure AD > serwer usługi MFA > Blokowanie/odblokowywanie użytkowników | Przedstawia historię żądań zablokowania lub odblokowania użytkowników. |
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

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Raportowanie dotyczące użytkowników zarejestrowanych dla usługi MFA programu PowerShell

Najpierw upewnij się, że [modułu MSOnline V1 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) zainstalowane.

Zidentyfikuj użytkowników, którzy zarejestrowali na potrzeby usługi MFA za pomocą programu PowerShell, który następuje po.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Zidentyfikuj użytkowników, którzy nie zarejestrowano usługi MFA za pomocą programu PowerShell, która jest zgodna.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Możliwe wyniki w raportach działań

Poniższa tabela może służyć do rozwiązywania z uwierzytelnianie wieloskładnikowe przy użyciu pobranej wersji raportu działanie usługi Multi-Factor authentication. Nie pojawią się one bezpośrednio w witrynie Azure portal.

| Wynik wywołania | Opis | Opis szerokiej |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN Entered | Użytkownik wprowadzi numer PIN.  Jeśli uwierzytelnianie powiodło się one umieszczone prawidłowego numeru PIN.  Jeśli odmówiono uwierzytelniania następnie one wprowadzono niepoprawny numer PIN lub użytkownik jest ustawiona na tryb standardowy. |
| SUCCESS_NO_PIN | Tylko # wprowadzono | Jeśli użytkownik jest ustawiona na tryb kodu PIN i uwierzytelnianie zostanie odrzucone, oznacza to, użytkownik nie wprowadził numeru PIN i wprowadzać tylko #.  Jeśli użytkownik jest ustawiona na tryb standardowy, a uwierzytelnianie zakończy się powodzeniem, oznacza to użytkownik wprowadzi tylko #, która jest poprawny rzeczą, którą należy wykonać w trybie standardowym. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Nie naciśnięto symbolu po wejściu | Użytkownik nie wysłał wszystkie cyfry DTMF, ponieważ nie wprowadzono #.  Inne numer wprowadzony nie są wysyłane, chyba że # zostanie podana, wskazujący zakończenia wpisu. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Brak wprowadzenia telefonu - przekroczony | Połączenie zostało odebrane, ale nie ma odpowiedzi.  Zwykle oznacza to, że połączenie zostało odebrane przez pocztę głosową. |
| SUCCESS_PIN_EXPIRED | Numer PIN wygasł i nie został zmieniony | Kod PIN wygasł i monitowani je zmienić, ale zmiana numeru PIN nie została ukończona pomyślnie. |
| SUCCESS_USED_CACHE | Użyta pamięć podręczna | Uwierzytelnianie zakończyło się pomyślnie bez wywołania usługi Multi-Factor Authentication od czasu poprzedniego pomyślnego uwierzytelnienia dla tej samej nazwy użytkownika wystąpił w przedziale czasowym skonfigurowanej pamięci podręcznej. |
| SUCCESS_BYPASSED_AUTH | Pominięto uwierzytelnianie | Uwierzytelnianie zakończyło się pomyślnie, za pomocą jednokrotnego obejścia, inicjowane dla użytkownika.  Zobacz raport historii użytkownika pominąć, aby uzyskać więcej informacji na obejście. |
| SUCCESS_USED_IP_BASED_CACHE | Użyta pamięć podręczna, które są oparte na adresie IP | Uwierzytelnianie zakończyło się pomyślnie bez wywołania usługi Multi-Factor Authentication, od czasu poprzedniego pomyślnego uwierzytelnienia dla tej samej nazwy użytkownika, typ uwierzytelniania, nazwa aplikacji, a IP wystąpił w przedziale czasowym skonfigurowanej pamięci podręcznej. |
| SUCCESS_USED_APP_BASED_CACHE | Użyta pamięć podręczna opartego na aplikacji | Uwierzytelnianie zakończyło się pomyślnie bez wywołania usługi Multi-Factor Authentication od czasu poprzedniego pomyślnego uwierzytelnienia dla tej samej nazwy użytkownika, typ uwierzytelniania i nazwę aplikacji w ramach czasowych skonfigurowanej pamięci podręcznej. |
| SUCCESS_INVALID_INPUT | Nieprawidłowy numer telefonu | Odpowiedzi wysyłane z telefonu jest nieprawidłowy.  Może to być na komputerze faksu lub modemu lub użytkownik może wprowadzono * jako część numeru PIN. |
| SUCCESS_USER_BLOCKED | Użytkownik jest zablokowany | Numer telefonu użytkownika jest zablokowane.  Liczba zablokowanych może być inicjowane przez użytkownika podczas połączenia uwierzytelniającego lub przez administratora przy użyciu witryny Azure portal. <br> UWAGA:  Liczba zablokowanych jest również byproduct alertu na oszustwa. |
| SUCCESS_SMS_AUTHENTICATED | Uwierzytelniono wiadomość tekstową | Dla wiadomości testowej dwukierunkowe, użytkownik prawidłowo udzielił odpowiedzi z ich jednorazowy kod dostępu (OTP) lub OTP + kod PIN. |
| SUCCESS_SMS_SENT | Wysłano wiadomość tekstową | Dla wiadomości SMS wiadomości SMS zawierająca jednorazowy kod dostępu (OTP) został pomyślnie wysłany.  Użytkownik będzie wprowadzać OTP lub OTP + numer PIN w aplikacji, aby zakończyć uwierzytelnianie. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplikacja mobilna-uwierzytelniono | Użytkownik pomyślnie uwierzytelniony za pośrednictwem aplikacji mobilnej. |
| SUCCESS_OATH_CODE_PENDING | Oczekujący kod OATH | Użytkownik został zostanie wyświetlony monit o kod OATH, ale nie odpowiedział. |
| SUCCESS_OATH_CODE_VERIFIED | Zweryfikowano kod OATH | Użytkownik wprowadzi prawidłowy kod OATH po wyświetleniu monitu. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Zweryfikowano rezerwowy kod OATH | Użytkownik został odmowy uwierzytelniania przy użyciu ich podstawowej metody uwierzytelniania wieloskładnikowego i następnie podać prawidłowy kod OATH dla działania rezerwowego. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Rezerwowe pytania zabezpieczające | Użytkownikowi odmówiono uwierzytelniania przy użyciu ich podstawowej metody uwierzytelniania wieloskładnikowego i odpowiedzi na pytania zabezpieczające poprawnie jako metody rezerwowej. |
| FAILED_PHONE_BUSY | Uwierzytelnianie jest już w toku | Uwierzytelnianie wieloskładnikowe jest już przetwarzana uwierzytelniania dla tego użytkownika.  Jest to często spowodowane przez klientów usługi RADIUS, które wysyłania wielu żądań uwierzytelniania podczas logowania ten sam. |
| CONFIG_ISSUE | Numer telefonu jest nieosiągalny | Podjęto próbę wywołania, ale nie można albo umieścić lub nie zostało odebrane.  Obejmuje to zajęta, szybkie zajęta (odłączony), tri tony (liczba nie jest już w usłudze) przekroczył limit czasu podczas dzwonienie itp. |
| FAILED_INVALID_PHONENUMBER | Nieprawidłowy Format numeru telefonu | Numer telefonu ma nieprawidłowy format.  Numery telefonów musi być wartością liczbową i musi mieć 10 cyfr dla kodu kraju + 1 (Stany Zjednoczone i Kanada). |
| FAILED_USER_HUNGUP_ON_US | Użytkownik rozłączył się | Użytkownik odpowiedzi na telefon, ale następnie rozłączył bez naciskania klawisza wszystkie przyciski. |
| FAILED_INVALID_EXTENSION | Nieprawidłowy numer wewnętrzny | Rozszerzenie zawiera nieprawidłowe znaki.  Tylko cyfry, przecinki oraz znaki *, a # są dozwolone.  @ Prefiks może też być użyte. |
| FAILED_FRAUD_CODE_ENTERED | Wprowadzono kod oszustwa | Użytkownik, wybrany do zgłaszania oszustwa podczas wywołania, co spowoduje odrzucenie uwierzytelnienia i numer telefonu zablokowane.| 
| FAILED_SERVER_ERROR | Nie można nawiązać połączenia | Usługa Multi-Factor Authentication nie może nawiązać połączenie. |
| FAILED_SMS_NOT_SENT | Nie można wysłać wiadomości SMS | Nie można wysłać wiadomości SMS.  Uwierzytelnianie zostanie odrzucone. |
| FAILED_SMS_OTP_INCORRECT | Nieprawidłowa wiadomość tekstowa OTP | Użytkownik wprowadził nieprawidłowy jednorazowy kod dostępu (OTP) w wiadomości SMS, otrzymany.  Uwierzytelnianie zostanie odrzucone. |
| FAILED_SMS_OTP_PIN_INCORRECT | Wiadomość tekstowa OTP oraz kod PIN nieprawidłowy | Użytkownik wprowadził nieprawidłowy jednorazowy kod dostępu (OTP) i/lub niepoprawny numer PIN użytkownika.  Uwierzytelnianie zostanie odrzucone. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Przekroczono prób uzyskania hasła Jednorazowego komunikat tekstu | Użytkownik przekroczył maksymalną liczbę prób jednorazowy kod dostępu (OTP). |
| FAILED_PHONE_APP_DENIED | Odmówiono użycia aplikacji mobilnej | Użytkownikowi odmówiono uwierzytelniania w aplikacji mobilnej, naciskając przycisk Odmów. |
| FAILED_PHONE_APP_INVALID_PIN | Aplikacja mobilna-nieprawidłowy kod PIN | Użytkownik wprowadził nieprawidłowy kod PIN podczas uwierzytelniania w aplikacji mobilnej. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Aplikacja mobilna nie zmienić numer PIN | Użytkownik nie została pomyślnie ukończona wymagana zmiana kodu PIN w aplikacji mobilnej. |
| FAILED_FRAUD_REPORTED | Zgłoszono oszustwo | Użytkownik zgłosił oszustwo w aplikacji mobilnej. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplikacja mobilna Brak odpowiedzi | Użytkownik nie odpowiada na żądania uwierzytelnienia aplikacji mobilnej. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Aplikacja mobilna-wszystkie urządzenia zablokowane | Urządzenia aplikacji mobilnej dla tego użytkownika nie odpowiada na powiadomienia i został zablokowany. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Powiadomienia aplikacji mobilnej nie powiodło się | Wystąpił błąd podczas próby wysłania powiadomienia do aplikacji mobilnej na urządzeniu użytkownika. |
| FAILED_PHONE_APP_INVALID_RESULT | Aplikacja mobilna-nieprawidłowy wynik | Aplikacja mobilna zwróciła nieprawidłowy wynik. |
| FAILED_OATH_CODE_INCORRECT | Nieprawidłowy kod OATH | Użytkownik wprowadził nieprawidłowy kod OATH.  Uwierzytelnianie zostanie odrzucone. |
| FAILED_OATH_CODE_PIN_INCORRECT | Kod OATH i numeru PIN jest niepoprawny | Użytkownik wprowadził nieprawidłowy kod OATH i/lub niepoprawny numer PIN użytkownika.  Uwierzytelnianie zostanie odrzucone. |
| FAILED_OATH_CODE_DUPLICATE | Zduplikowany kod OATH | Użytkownik wprowadzi kod OATH, który był wcześniej używany.  Uwierzytelnianie zostanie odrzucone. |
| FAILED_OATH_CODE_OLD | Nieaktualny kod OATH | Użytkownik wprowadzi kod OATH, który poprzedza kod OATH, który był wcześniej używany.  Uwierzytelnianie zostanie odrzucone. |
| FAILED_OATH_TOKEN_TIMEOUT | Limit czasu wyniku kodu OATH | Użytkownik trwało za długo wprowadź kod OATH i próba uwierzytelniania Multi-Factor Authentication ma już Przekroczono limit czasu. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Limit czasu wyniku pytań zabezpieczeń | Użytkownik trwało za długo, aby wprowadzić odpowiedzi na pytania zabezpieczające i próba uwierzytelniania Multi-Factor Authentication ma już Przekroczono limit czasu. |
| FAILED_AUTH_RESULT_TIMEOUT | Limit czasu wyniku uwierzytelnienia | Użytkownik trwało zbyt długo próba uwierzytelniania wieloskładnikowego. |
| FAILED_AUTHENTICATION_THROTTLED | Uwierzytelnianie z ograniczoną przepływnością | Próba uwierzytelniania wieloskładnikowego została ograniczona przez usługę. |

## <a name="next-steps"></a>Kolejne kroki

* [Dla użytkowników](../user-help/multi-factor-authentication-end-user.md)
* [Miejsca wdrożenia](concept-mfa-whichversion.md)
