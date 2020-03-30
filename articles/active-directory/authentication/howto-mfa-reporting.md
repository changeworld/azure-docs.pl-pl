---
title: Raporty dostępu i użycia usługi Azure MFA — usługa Azure Active Directory
description: W tym artykule opisano sposób korzystania z funkcji uwierzytelniania wieloskładnikowego platformy Azure — raporty.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47e6bac031a5f2cffc8734fee976fbf8dadc666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129079"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Raporty w usłudze Azure uwierzytelniania wieloskładnikowego

Uwierzytelnianie wieloskładnikowe platformy Azure zawiera kilka raportów, które mogą być używane przez Ciebie i Twoją organizację dostępne za pośrednictwem witryny Azure portal. W poniższej tabeli wymieniono dostępne raporty:

| Raport | Lokalizacja | Opis |
|:--- |:--- |:--- |
| Zablokowana historia użytkownika | > bloku/odblokowywania użytkowników usługi Azure AD > Security > MFA | Pokazuje historię żądań blokowania lub odblokowywania użytkowników. |
| Powiadomienia o użytkowaniu i oszustwach | Logowania > usługi Azure AD | Zawiera informacje o ogólnym użyciu, podsumowaniu użytkownika i szczegółach użytkownika; jak również historię wpisów o nadużyciach finansowych złożonych w określonym zakresie dat. |
| Użycie składników lokalnych | Raport aktywności > zabezpieczeń usługi Azure AD > > mfa | Zawiera informacje na temat ogólnego użycia usługi MFA za pośrednictwem rozszerzenia NPS, serwera usługi ADFS i usługi MFA. |
| Pominięto historię użytkownika | Obejście usługi Azure > Security > mfa > Jednorazowe obejście | Zawiera historię żądań ominięcie uwierzytelniania wieloskładnikowego dla użytkownika. |
| Stan serwera | Stan serwera > usługi Azure AD > Security > MFA | Wyświetla stan serwerów uwierzytelniania wieloskładnikowego skojarzonych z Twoim kontem. |

## <a name="view-mfa-reports"></a>Wyświetlanie raportów usługi MFA

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Usługa Azure Active Directory** > **Security Security** > **MFA**.
3. Wybierz raport, który chcesz wyświetlić.

   ![Raport o stanie serwera usługi MFA w portalu Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Raport logowania usługi Azure AD

Za pomocą **raportu aktywności logowania w** [witrynie Azure portal](https://portal.azure.com)można uzyskać informacje potrzebne do określenia, jak środowisko działa.

Raport logowania może dostarczyć informacji o użyciu aplikacji zarządzanych i działań logowania użytkownika, które zawierają informacje o użyciu uwierzytelniania wieloskładnikowego (MFA). Dane usługi MFA dają wgląd w sposób działania usługi MFA w Twojej organizacji. Umożliwiają one udzielenie odpowiedzi na takie pytania, jak:

- Czy logowanie zostało zakwestionowane przez usługę MFA?
- Jak użytkownik ukończył uwierzytelnianie MFA?
- Dlaczego użytkownik nie mógł ukończyć uwierzytelniania MFA?
- Ilu użytkowników zostało zakwestionowanych przez usługę MFA?
- Ilu użytkowników nie mogło odpowiedzieć na wezwania usługi MFA?
- Jakie są typowe problemy z usługą MFA, na które natykają się użytkownicy końcowi?

Te dane są dostępne za pośrednictwem [witryny Azure portal](https://portal.azure.com) i [interfejsu API raportowania.](../reports-monitoring/concept-reporting-api.md)

![Raport logowania usługi Azure AD w witrynie Azure portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktura raportu logowania

Raporty działania logowania dla usługi MFA umożliwiają dostęp do następujących informacji:

**Wymagana usługa MFA:** czy usługa MFA jest wymagana do logowania, czy też nie. Usługa MFA może być wymagana z powodu usługi MFA dla użytkownika, dostępu warunkowego lub z innych przyczyn. Możliwe wartości to **Tak** lub **Nie**.

**Wynik usługi MFA:** więcej informacji na temat tego, czy uwierzytelnianie MFA zostało przeprowadzone pomyślnie, czy też nie:

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

**Metoda uwierzytelniania usługi MFA:** metoda uwierzytelniania stosowana przez użytkownika w celu zakończenia uwierzytelniania MFA. Możliwe wartości to:

- Wiadomość SMS
- Powiadomienie aplikacji mobilnej
- Połączenie telefoniczne (numer telefonu uwierzytelniania)
- Kod weryfikacyjny aplikacji mobilnej
- Połączenie telefoniczne (numer telefonu służbowego)
- Połączenie telefoniczne (alternatywny numer telefonu uwierzytelniania)

**Szczegóły uwierzytelniania usługi MFA:** wyczyszczona wersja numeru telefonu, na przykład +X XXXXXXXX64.

**Dostęp warunkowy** Znajdź informacje o zasadach dostępu warunkowego, które miały wpływ na próbę logowania, w tym:

- Nazwa zasady
- Kontrola dotacji
- Kontrolki sesji
- Wynik

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Raportowanie programu PowerShell dotyczące użytkowników zarejestrowanych w u źródła mfa

Najpierw upewnij się, że masz zainstalowany [moduł MSOnline V1 PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)

Identyfikowanie użytkowników, którzy zarejestrowali się dla usługi MFA przy użyciu programu PowerShell, który następuje. Ten zestaw poleceń wyklucza niepełnosprawnych użytkowników, ponieważ te konta nie mogą uwierzytelniać się za pomocą usługi Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods -ne $null -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identyfikowanie użytkowników, którzy nie zarejestrowali się dla usługi MFA przy użyciu programu PowerShell, który następuje. Ten zestaw poleceń wyklucza niepełnosprawnych użytkowników, ponieważ te konta nie mogą uwierzytelniać się za pomocą usługi Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods.Count -eq 0 -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identyfikowanie użytkowników i zarejestrowanych metod wyjściowych. 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Możliwe wyniki w raportach aktywności

Poniższa tabela może służyć do rozwiązywania problemów z uwierzytelnianiem wieloskładnikowym przy użyciu pobranej wersji raportu aktywności uwierzytelniania wieloskładnikowego. Nie będą one wyświetlane bezpośrednio w witrynie Azure portal.

| Wynik połączenia | Opis | Szeroki opis |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Wprowadzony kod PIN | Użytkownik wprowadził kod PIN. Jeśli uwierzytelnianie powiodło się, wprowadzono poprawny kod PIN. Jeśli uwierzytelnianie zostanie odrzucone, wprowadzono nieprawidłowy numer PIN lub użytkownik jest ustawiony na tryb standardowy. |
| SUCCESS_NO_PIN | Tylko # Wprowadzony | Jeśli użytkownik jest ustawiony na tryb NUMERU PIN, a uwierzytelnianie zostanie odrzucone, oznacza to, że użytkownik nie wprowadził kodu PIN i wprowadził tylko #.  Jeśli użytkownik jest ustawiony na tryb standardowy, a uwierzytelnianie powiedzie się oznacza, że użytkownik wprowadził tylko # co jest poprawną rzeczą do zrobienia w trybie standardowym. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Nie naciśnięty po wejściu | Użytkownik nie wysłał żadnych cyfr DTMF, ponieważ # nie został wprowadzony. Inne wprowadzone cyfry nie są wysyłane, chyba że wpisany zostanie # wskazujący zakończenie wpisu. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Brak wejścia telefonicznego - limit czasu | Połączenie zostało odebrane, ale nie było odpowiedzi. Zazwyczaj oznacza to, że połączenie zostało odebrane przez pocztę głosową. |
| SUCCESS_PIN_EXPIRED | Kod PIN wygasł i nie został zmieniony | Kod PIN użytkownika wygasł i został poproszony o jego zmianę, ale zmiana numeru PIN nie została pomyślnie ukończona. |
| SUCCESS_USED_CACHE | Używana pamięć podręczna | Uwierzytelnianie powiodło się bez wywołania uwierzytelniania wieloskładnikowego, ponieważ poprzednie pomyślne uwierzytelnianie dla tej samej nazwy użytkownika wystąpiło w skonfigurowanym przedziale czasowym pamięci podręcznej. |
| SUCCESS_BYPASSED_AUTH | Ominięty Auth | Uwierzytelnianie powiodło się przy użyciu jednorazowego bypass zainicjowane dla użytkownika. Zobacz ominięty raport historii użytkownika, aby uzyskać więcej informacji na temat obejścia. |
| SUCCESS_USED_IP_BASED_CACHE | Używana pamięć podręczna oparta na protokoczowym protokãoł | Uwierzytelnianie powiodło się bez wywołania uwierzytelniania wieloskładnikowego, ponieważ poprzednie pomyślne uwierzytelnianie dla tej samej nazwy użytkownika, typu uwierzytelniania, nazwy aplikacji i adresu IP wystąpiło w skonfigurowanym przedziale czasowym pamięci podręcznej. |
| SUCCESS_USED_APP_BASED_CACHE | Używana pamięć podręczna oparta na aplikacjach | Uwierzytelnianie powiodło się bez wywołania uwierzytelniania wieloskładnikowego od poprzedniego pomyślnego uwierzytelnienia dla tej samej nazwy użytkownika, typu uwierzytelniania i nazwy aplikacji w skonfigurowanym przedziale czasowym pamięci podręcznej. |
| SUCCESS_INVALID_INPUT | Nieprawidłowe wejście telefoniczne | Odpowiedź wysłana z telefonu jest nieprawidłowa. Może to być z faksu lub modemu lub użytkownik może wprowadzić * jako część kodu PIN. |
| SUCCESS_USER_BLOCKED | Użytkownik jest zablokowany | Numer telefonu użytkownika jest zablokowany. Zablokowany numer może być zainicjowany przez użytkownika podczas połączenia uwierzytelniania lub przez administratora korzystającego z witryny Azure portal. <br> UWAGA: Zablokowany numer jest również produktem ubocznym alertu o oszustwie. |
| SUCCESS_SMS_AUTHENTICATED | Uwierzytelniona wiadomość tekstowa | W przypadku dwukierunkowej wiadomości testowej użytkownik poprawnie odpowiedział jednorazowym kodem dostępu (OTP) lub OTP + PIN. |
| SUCCESS_SMS_SENT | Wiadomość tekstowa wysłana | W przypadku wiadomości tekstowych wiadomość tekstowa zawierająca jednorazowy kod dostępu (OTP) została pomyślnie wysłana. Użytkownik wprowadzi do aplikacji kod OTP lub OTP + PIN, aby zakończyć uwierzytelnianie. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Uwierzytelniona aplikacja mobilna | Użytkownik pomyślnie uwierzytelniony za pośrednictwem aplikacji mobilnej. |
| SUCCESS_OATH_CODE_PENDING | Kod OATH oczekujące | Użytkownik został poproszony o podanie kodu OATH, ale nie odpowiedział. |
| SUCCESS_OATH_CODE_VERIFIED | Zweryfikowano kod OATH | Po wyświetleniu monitu użytkownik wprowadził prawidłowy kod OATH. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Rezerwowy kod OATH zweryfikowany | Użytkownikowi odmówiono uwierzytelniania przy użyciu podstawowej metody uwierzytelniania wieloskładnikowego, a następnie podano prawidłowy kod OATH dla rezerwowego. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Odpowiedzi na rezerwowe pytania zabezpieczające | Użytkownikowi odmówiono uwierzytelniania przy użyciu podstawowej metody uwierzytelniania wieloskładnikowego, a następnie udzielono mu poprawnie odpowiedzi na pytania zabezpieczające w celu uzyskania rezerwowego. |
| FAILED_PHONE_BUSY | Auth już w toku | Uwierzytelnianie wieloskładnikowe już przetwarza uwierzytelnianie dla tego użytkownika. Jest to często spowodowane przez klientów usługi RADIUS, którzy wysyłają wiele żądań uwierzytelniania podczas tego samego logowania. |
| CONFIG_ISSUE | Telefon nieosiągalny | Podjęto próbę połączenia, ale nie można było ich nabyć lub nie odebrano odpowiedzi. Obejmuje to sygnał zajętości, szybki sygnał zajętości (odłączony), trójtonowe (numer nie jest już w eksploatacji), limit czasu podczas dzwonienia itp. |
| FAILED_INVALID_PHONENUMBER | Nieprawidłowy format numeru telefonu | Numer telefonu ma nieprawidłowy format. Numery telefonów muszą być numeryczne i muszą mieć 10 cyfr dla kodu kraju +1 (Stany Zjednoczone & Kanada). |
| FAILED_USER_HUNGUP_ON_US | Użytkownik zawiesił telefon | Użytkownik odebrał telefon, ale potem rozłączył się bez naciskania żadnych przycisków. |
| FAILED_INVALID_EXTENSION | Nieprawidłowe rozszerzenie | Rozszerzenie zawiera nieprawidłowe znaki. Dozwolone są tylko cyfry, przecinki, *i #. Można również użyć prefiksu @. |
| FAILED_FRAUD_CODE_ENTERED | Wprowadzony kod oszustwa | Użytkownik zdecydował się zgłosić oszustwo podczas połączenia, w wyniku czego odmówiono uwierzytelnienia i zablokowanego numeru telefonu.| 
| FAILED_SERVER_ERROR | Nie można nałożyć połączenia | Usługa uwierzytelniania wieloskładnikowego nie może nałożyć połączenia. |
| FAILED_SMS_NOT_SENT | Nie można wysłać wiadomości tekstowej | Nie można wysłać wiadomości tekstowej. Odmówiono uwierzytelnienia. |
| FAILED_SMS_OTP_INCORRECT | Wiadomość tekstowa OTP niepoprawna | Użytkownik wprowadził niepoprawny jednorazowy kod dostępu (OTP) z otrzymanej wiadomości tekstowej. Odmówiono uwierzytelnienia. |
| FAILED_SMS_OTP_PIN_INCORRECT | Wiadomość tekstowa OTP + kod PIN niepoprawny | Użytkownik wprowadził niepoprawny jednorazowy kod dostępu (OTP) i/lub nieprawidłowy numer PIN użytkownika. Odmówiono uwierzytelnienia. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Przekroczono maksymalną liczbę prób OTP wiadomości tekstowych | Użytkownik przekroczył maksymalną liczbę prób jednorazowego kodu dostępu (OTP). |
| FAILED_PHONE_APP_DENIED | Odmowa aplikacji mobilnej | Użytkownik odmówił uwierzytelnienia w aplikacji mobilnej, naciskając przycisk Odmów. |
| FAILED_PHONE_APP_INVALID_PIN | Nieprawidłowy kod PIN aplikacji mobilnej | Podczas uwierzytelniania w aplikacji mobilnej użytkownik wprowadził nieprawidłowy kod PIN. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Kod PIN aplikacji mobilnej nie został zmieniony | Użytkownik nie pomyślnie wykonał wymaganą zmianę numeru PIN w aplikacji mobilnej. |
| FAILED_FRAUD_REPORTED | Zgłoszone oszustwa | Użytkownik zgłosił oszustwo w aplikacji mobilnej. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplikacja mobilna Brak odpowiedzi | Użytkownik nie odpowiedział na żądanie uwierzytelnienia aplikacji mobilnej. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Aplikacja mobilna Wszystkie urządzenia zablokowane | Urządzenia aplikacji mobilnej dla tego użytkownika nie odpowiadają już na powiadomienia i zostały zablokowane. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Powiadomienie aplikacji mobilnej nie powiodło się | Wystąpił błąd podczas próby wysłania powiadomienia do aplikacji mobilnej na urządzeniu użytkownika. |
| FAILED_PHONE_APP_INVALID_RESULT | Nieprawidłowy wynik aplikacji mobilnej | Aplikacja mobilna zwróciła nieprawidłowy wynik. |
| FAILED_OATH_CODE_INCORRECT | Nieprawidłowy kod OATH | Użytkownik wprowadził nieprawidłowy kod OATH.  Odmówiono uwierzytelnienia. |
| FAILED_OATH_CODE_PIN_INCORRECT | Nieprawidłowy kod OATH + kod PIN | Użytkownik wprowadził nieprawidłowy kod OATH i/lub nieprawidłowy numer PIN użytkownika.  Odmówiono uwierzytelnienia. |
| FAILED_OATH_CODE_DUPLICATE | Zduplikowany kod OATH | Użytkownik wprowadził kod OATH, który był wcześniej używany.  Odmówiono uwierzytelnienia. |
| FAILED_OATH_CODE_OLD | Kod OATH nieaktualny | Użytkownik wprowadził kod OATH, który poprzedza wcześniej używany kod OATH.  Odmówiono uwierzytelnienia. |
| FAILED_OATH_TOKEN_TIMEOUT | Limit czasu wynikowy kodu OATH | Użytkownik potrzebował zbyt dużo czasu, aby wprowadzić kod OATH, a próba uwierzytelniania wieloskładnikowego została już upłynie. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Limit czasu wynik pytań zabezpieczających | Użytkownik zbyt długo, aby wprowadzić odpowiedź na pytania zabezpieczające i próba uwierzytelniania wieloskładnikowego już upłynął limit czasu. |
| FAILED_AUTH_RESULT_TIMEOUT | Limit czasu wynika YUTH | Użytkownik trwał zbyt długo, aby zakończyć próbę uwierzytelniania wieloskładnikowego. |
| FAILED_AUTHENTICATION_THROTTLED | Uwierzytelnianie ograniczone | Próba uwierzytelniania wieloskładnikowego została ograniczona przez usługę. |

## <a name="next-steps"></a>Następne kroki

* [Raportowanie użycia i analiz dotyczących użycia i analiz sspr i usługi MFA](howto-authentication-methods-usage-insights.md)
* [Dla użytkowników](../user-help/multi-factor-authentication-end-user.md)
* [Gdzie wdrożyć](concept-mfa-whichversion.md)
