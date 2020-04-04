---
title: Samoobsługowe raporty resetowania haseł — usługa Azure Active Directory
description: Raportowanie zdarzeń resetowania hasła usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348082ad96a5efa4d8f866c3675044edf7b6f8a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652153"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Reporting options for Azure AD password management (Opcje raportowania dotyczące zarządzania hasłami w usłudze Azure AD)

Po wdrożeniu wiele organizacji chce wiedzieć, jak i czy samoobsługowe resetowanie hasła (SSPR) jest naprawdę używane. Funkcja raportowania, którą udostępnia usługa Azure Active Directory (Azure AD) ułatwia odpowiadanie na pytania przy użyciu wstępnie utworzonych raportów. Jeśli masz odpowiednią licencję, możesz również tworzyć zapytania niestandardowe.

![Raportowanie wiele osób korzystających z dzienników inspekcji w usłudze Azure AD][Reporting]

Na następujące pytania można odpowiedzieć na raporty istniejące w [witrynie Azure portal:](https://portal.azure.com/)

> [!NOTE]
> Musisz być [administratorem globalnym](../users-groups-roles/directory-assign-admin-roles.md)i musisz wyrazić zgodę na gromadzenie tych danych w imieniu organizacji. Aby wyrazić zgodę, należy odwiedzić kartę **Raportowanie** lub dzienniki inspekcji co najmniej raz. Do tego czasu dane nie są zbierane dla twojej organizacji.
>

* Ile osób zarejestrowało się do resetowania hasła?
* Kto zarejestrował się do resetowania hasła?
* Jakie dane rejestrują się ludzie?
* Ile osób zresetować swoje hasła w ciągu ostatnich siedmiu dni?
* Jakie są najczęstsze metody resetowania haseł używane przez użytkowników lub administratorów?
* Jakie problemy napotykają użytkownicy lub administratorzy podczas próby użycia hasła?
* Co administratorzy często resetują własne hasła?
* Czy jest jakaś podejrzana aktywność dzieje się z resetowaniem hasła?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Jak wyświetlać raporty dotyczące zarządzania hasłami w witrynie Azure Portal

W usłudze Azure portal środowiska ulepszyliśmy sposób wyświetlania działania resetowania hasła i resetowania hasła. Aby znaleźć zdarzenia rejestracji resetowania hasła i resetowania hasła, należy wykonać następujące czynności:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**.
3. Wyszukaj **usługę Azure Active Directory** na liście usług i wybierz ją.
4. Wybierz **pozycję Użytkownicy** z sekcji Zarządzanie.
5. Wybierz **dzienniki inspekcji** z **bloku Użytkownicy.** Spowoduje to wyświetlenie wszystkich zdarzeń inspekcji, które wystąpiły dla wszystkich użytkowników w katalogu. Możesz filtrować ten widok, aby wyświetlić wszystkie zdarzenia związane z hasłem.
6. Z menu **Filtr** w górnej części okienka wybierz listę rozwijaną **Usługa** i zmień ją na Samoobsługowy typ usługi **zarządzania hasłami.**
7. Opcjonalnie należy dodatkowo filtrować listę, wybierając konkretne **działanie,** które Cię interesuje.

### <a name="converged-registration-preview"></a>Rejestracja konwergentna (wersja zapoznawcza)

Jeśli bierzesz udział w publicznej wersji zapoznawczej rejestracji konwergentnej, informacje dotyczące aktywności użytkownika w dziennikach inspekcji zostaną znalezione w obszarze**Metody uwierzytelniania** **zabezpieczeń** > .

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Opis kolumn raportu w witrynie Azure portal

Poniższa lista szczegółowo wyjaśnia każdą kolumnę raportu w witrynie Azure portal:

* **Użytkownik**: Użytkownik, który podjął próbę zresetowania hasła operacji rejestracji.
* **Rola:** Rola użytkownika w katalogu.
* **Data i godzina:** data i godzina próby.
* **Zarejestrowane dane:** Dane uwierzytelniania podane przez użytkownika podczas rejestracji resetowania hasła.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Opis wartości raportu w witrynie Azure portal

W poniższej tabeli opisano różne wartości, które można ustawić dla każdej kolumny w witrynie Azure portal:

| Kolumna | Dozwolone wartości i ich znaczenie |
| --- | --- |
| Zarejestrowane dane |**Alternatywny adres e-mail**: Użytkownik użył alternatywnej wiadomości e-mail lub uwierzytelniania e-mail do uwierzytelniania.<p><p>**Telefon biurowy**: użytkownik użył telefonu biurowego do uwierzytelniania.<p>**Telefon komórkowy**: Użytkownik użył telefonu komórkowego lub telefonu uwierzytelniającego do uwierzytelniania.<p>**Pytania zabezpieczające:** Użytkownik użył pytań zabezpieczających do uwierzytelnienia.<p>**Dowolna kombinacja poprzednich metod, na przykład alternatywny adres e-mail + telefon komórkowy:** Występuje, gdy określono zasady dwóch bramek i pokazuje, które dwie metody użytkownik użył do uwierzytelniania żądania resetowania hasła. |

## <a name="self-service-password-management-activity-types"></a>Typy działań samoobsługowego zarządzania hasłami

Następujące typy działań są wyświetlane w kategorii zdarzenia inspekcji **samoobsługowego zarządzania hasłami:**

* [Zablokowano samoobsługowe resetowanie hasła:](#activity-type-blocked-from-self-service-password-reset)Wskazuje, że użytkownik próbował zresetować hasło, użyć określonej bramki lub zweryfikować numer telefonu więcej niż pięć razy w ciągu 24 godzin.
* [Zmień hasło (samoobsługa)](#activity-type-change-password-self-service): Wskazuje, że użytkownik wykonał dobrowolną lub wymuszoną (z powodu wygaśnięcia) zmianę hasła.
* [Resetowanie hasła (według administratora)](#activity-type-reset-password-by-admin): Wskazuje, że administrator wykonał resetowanie hasła w imieniu użytkownika z witryny Azure portal.
* [Resetowanie hasła (samoobsługa)](#activity-type-reset-password-self-service): Wskazuje, że użytkownik pomyślnie zresetował swoje hasło z [portalu resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com).
* [Postęp działania przepływu resetowania hasła samoobsługowego:](#activity-type-self-serve-password-reset-flow-activity-progress)wskazuje każdy określony krok, przez który przechodzi użytkownik, na przykład przekazanie określonej bramy uwierzytelniania resetowania hasła w ramach procesu resetowania hasła.
* [Odblokuj konto użytkownika (samoobsługowe)](#activity-type-unlock-a-user-account-self-service)): wskazuje, że użytkownik pomyślnie odblokował swoje konto usługi Active Directory bez resetowania hasła z [portalu resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com) przy użyciu funkcji active directory odblokowania konta bez resetowania.
* [Użytkownik zarejestrowany do samodzielnego resetowania hasła:](#activity-type-user-registered-for-self-service-password-reset)Wskazuje, że użytkownik zarejestrował wszystkie wymagane informacje, aby móc zresetować swoje hasło zgodnie z aktualnie określonymi zasadami resetowania hasła dzierżawy.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ działania: zablokowany przed samoobsługowym resetowaniem hasła

Poniższa lista szczegółowo wyjaśnia to działanie:

* **Opis działania:** Wskazuje, że użytkownik próbował zresetować hasło, użyć określonej bramki lub zweryfikować numer telefonu więcej niż pięć razy w ciągu 24 godzin.
* **Aktor działania:** użytkownik, który został ograniczony od wykonywania dodatkowych operacji resetowania. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Miejsce docelowe działania:** użytkownik, który został ograniczony od wykonywania dodatkowych operacji resetowania. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stan aktywności**:
  * _Powodzenie:_ Wskazuje, że użytkownik został ograniczony od wykonywania dodatkowych resetów, próby dodatkowych metod uwierzytelniania lub sprawdzania poprawności dodatkowych numerów telefonów przez następne 24 godziny.
* **Przyczyna niepowodzenia stanu działania:** Nie dotyczy.

### <a name="activity-type-change-password-self-service"></a>Typ działania: Zmiana hasła (samoobsługa)

Poniższa lista szczegółowo wyjaśnia to działanie:

* **Opis działania:** Wskazuje, że użytkownik wykonał dobrowolną lub wymuszoną (z powodu wygaśnięcia) zmianę hasła.
* **Aktor aktywności**: Użytkownik, który zmienił swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania:** użytkownik, który zmienił swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stany aktywności**:
  * _Powodzenie:_ Wskazuje, że użytkownik pomyślnie zmienił swoje hasło.
  * _Błąd:_ Wskazuje, że użytkownik nie może zmienić hasła. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania,** aby dowiedzieć się więcej o przyczynach wystąpienia błędu.
* **Przyczyna niepowodzenia stanu działania:**
  * _FuzzyPolicyViolationInvalidPassword_: Użytkownik wybrał hasło, które zostało automatycznie zakazane, ponieważ microsoft banned password detection możliwości okazało się zbyt powszechne lub szczególnie słabe.

### <a name="activity-type-reset-password-by-admin"></a>Typ działania: Resetowanie hasła (według administratora)

Poniższa lista szczegółowo wyjaśnia to działanie:

* **Opis działania:** Wskazuje, że administrator wykonał resetowanie hasła w imieniu użytkownika z witryny Azure portal.
* **Aktor aktywności:** Administrator, który wykonał resetowanie hasła w imieniu innego użytkownika końcowego lub administratora. Musi być administratorem haseł, administratorem użytkowników lub administratorem pomocy technicznej.
* **Cel działania:** Użytkownik, którego hasło zostało zresetowane. Użytkownik może być użytkownikiem końcowym lub innym administratorem.
* **Stany aktywności**:
  * _Powodzenie:_ Wskazuje, że administrator pomyślnie zresetował hasło użytkownika.
  * _Błąd:_ Wskazuje, że administrator nie może zmienić hasła użytkownika. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania,** aby dowiedzieć się więcej o przyczynach wystąpienia błędu.

### <a name="activity-type-reset-password-self-service"></a>Typ działania: Resetowanie hasła (samoobsługa)

Poniższa lista szczegółowo wyjaśnia to działanie:

* **Opis działania:** Wskazuje, że użytkownik pomyślnie zresetował swoje hasło z [portalu resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com).
* **Aktor aktywności:** Użytkownik, który zresetował swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania:** użytkownik, który zresetował swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stany aktywności**:
  * _Powodzenie:_ Wskazuje, że użytkownik pomyślnie zresetował własne hasło.
  * _Błąd:_ Wskazuje, że nie udało się zresetować własnego hasła. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania,** aby dowiedzieć się więcej o przyczynach wystąpienia błędu.
* **Przyczyna niepowodzenia stanu działania:**
  * _FuzzyPolicyViolationInvalidPassword_: Administrator wybrał hasło, które zostało automatycznie zakazane, ponieważ możliwości wykrywania haseł zbanowanych przez Microsoft uznały je za zbyt powszechne lub szczególnie słabe.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ działania: Samoobsługowy postęp działania resetowania hasła

Poniższa lista szczegółowo wyjaśnia to działanie:

* **Opis działania:** Wskazuje każdy konkretny krok, przez który przechodzi użytkownik (na przykład przekazanie określonej bramy uwierzytelniania resetowania hasła) w ramach procesu resetowania hasła.
* **Aktor działania:** Użytkownik, który wykonał część przepływu resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania:** Użytkownik, który wykonał część przepływu resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stany aktywności**:
  * _Powodzenie:_ Wskazuje, że użytkownik pomyślnie ukończył określony krok przepływu resetowania hasła.
  * _Błąd:_ Wskazuje, że określony krok przepływu resetowania hasła nie powiódł się. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania,** aby dowiedzieć się więcej o przyczynach wystąpienia błędu.
* **Przyczyny stanu działania**: Zobacz poniższą tabelę dla [wszystkich dopuszczalnych przyczyn stanu działania resetowania](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ działania: odblokowywanie konta użytkownika (samoobsługa)

Poniższa lista szczegółowo wyjaśnia to działanie:

* **Opis działania:** Wskazuje, że użytkownik pomyślnie odblokował swoje konto usługi Active Directory bez resetowania hasła z [portalu resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com) przy użyciu funkcji active directory odblokowania konta bez resetowania.
* **Aktor aktywności**: Użytkownik, który odblokował swoje konto bez resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel aktywności:** Użytkownik, który odblokował swoje konto bez resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Dozwolone stany aktywności:**
  * _Sukces:_ Wskazuje, że użytkownik pomyślnie odblokował własne konto.
  * _Błąd:_ Wskazuje, że użytkownik nie może odblokować swojego konta. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania,** aby dowiedzieć się więcej o przyczynach wystąpienia błędu.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ działania: Użytkownik zarejestrowany do samodzielnego resetowania hasła

Poniższa lista szczegółowo wyjaśnia to działanie:

* **Opis działania:** Wskazuje, że użytkownik zarejestrował wszystkie wymagane informacje, aby móc zresetować swoje hasło zgodnie z aktualnie określonymi zasadami resetowania hasła dzierżawy. 
* **Aktor aktywności:** Użytkownik, który zarejestrował się do resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania:** Użytkownik, który zarejestrował się do resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Dozwolone stany aktywności:**
  * _Powodzenie_: Wskazuje, że użytkownik pomyślnie zarejestrował się do resetowania hasła zgodnie z bieżącymi zasadami. 
  * _Błąd:_ Wskazuje, że użytkownik nie zarejestrował się w celu zresetowania hasła. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania,** aby dowiedzieć się więcej o przyczynach wystąpienia błędu.

     >[!NOTE]
     >Błąd nie oznacza, że użytkownik nie może zresetować własnego hasła. Oznacza to, że nie zakończyli procesu rejestracji. Jeśli na ich koncie znajdują się niezweryfikowane dane, które są poprawne, takie jak numer telefonu, który nie został zweryfikowany, nawet jeśli nie zweryfikowali tego numeru telefonu, nadal mogą go używać do resetowania hasła.

## <a name="next-steps"></a>Następne kroki

* [Raportowanie użycia i analiz dotyczących użycia i analiz sspr i usługi MFA](howto-authentication-methods-usage-insights.md)
* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md).
* [Zarejestruj się, aby samodzielnie zresetować hasło](../user-help/active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś jest zepsute. Jak rozwiązać problem z łatem SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Przykład dzienników inspekcji działań sspr w usłudze Azure AD"
