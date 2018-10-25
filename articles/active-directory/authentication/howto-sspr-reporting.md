---
title: Samoobsługowe resetowanie haseł raporty — usługi Azure Active Directory
description: Raporty dotyczące haseł usługi Azure AD zdarzenia resetowania
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 7d7de02c96b9a17144b446b9ed86ebcbacd51162
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025009"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opcje raportowania w zarządzaniu hasłami w usłudze Azure AD

Po wdrożeniu w wielu organizacjach chcą wiedzieć, jak elementy są używane lub jeśli samoobsługowego resetowania haseł (SSPR). Funkcja raportowania, która zapewnia usługi Azure Active Directory (Azure AD) pomaga w uzyskaniu odpowiedzi na pytania za pomocą wbudowanych raportów. Jeśli masz odpowiednio licencję, można również utworzyć niestandardowe zapytania.

![Raportowanie][Reporting]

Można odpowiedzieć na następujące pytania, przez raporty, które istnieją w [witryny Azure portal](https://portal.azure.com/):

> [!NOTE]
> Musi być [administratora globalnego](../users-groups-roles/directory-assign-admin-roles.md), i wymaga zgody na tych danych do zebrania w imieniu swojej organizacji. Aby włączyć, należy odwiedzić **raportowania** karty lub inspekcji, dzienniki co najmniej raz. W międzyczasie dane nie są zbierane dla Twojej organizacji.
>

* Jak wiele osób zostały zarejestrowane na potrzeby resetowania haseł?
* Który został zarejestrowany do resetowania hasła?
* Jakie dane osoby rejestracji?
* Jak wiele osób do resetowania swoich haseł w ciągu ostatnich siedmiu dni
* Co to są najbardziej popularnych metod, które umożliwia resetowanie haseł użytkowników lub administratorów?
* Jakie są typowe problemy użytkowników lub administratorów twarzy, podczas próby użycia resetowania haseł?
* Jakie Administratorzy są często resetowania ich własnych haseł?
* Czy jakiekolwiek podejrzane działanie, dzieje się z funkcją resetowania hasła?

## <a name="power-bi-content-pack"></a>Pakiet zawartości usługi Power BI

Jeśli jesteś użytkownikiem usługi Power BI, ma pakietu zawartości dla usługi Azure AD, który zawiera łatwy w użyciu zgłoszenie dotyczące funkcji samoobsługowego resetowania HASEŁ. Aby uzyskać więcej informacji na temat sposobu używania i wdrożenie pakietu zawartości, zobacz [sposób użycia pakietu zawartości usługi Azure Active Directory usługi Power BI](../reports-monitoring/howto-power-bi-content-pack.md). Przy użyciu pakietu zawartości możesz utworzyć własne pulpity nawigacyjne i udostępnić je innym osobom w organizacji.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Jak wyświetlić raporty dotyczące zarządzania hasłami w witrynie Azure portal

W środowisku witryny Azure portal firma Microsoft ulepszyła sposobu można wyświetlić resetowania hasła i rejestracji aktywność resetowania haseł. Użyj następujących kroków, aby znaleźć hasło resetowania i zdarzenia rejestracji resetowania hasła:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** w okienku po lewej stronie.
3. Wyszukaj **usługi Azure Active Directory** na liście usług i wybierz ją.
4. Wybierz pozycję **Użytkownicy i grupy**.
5. Wybierz **dzienników inspekcji** z **użytkowników i grup** menu. To pokazuje wszystkie zdarzenia inspekcji, które wystąpiły dla wszystkich użytkowników w katalogu. Możesz filtrować ten widok, aby zobaczyć wszystkie zdarzenia związane z hasłami.
6. Aby odfiltrować ten widok, aby wyświetlić tylko zdarzenia dotyczące resetowania hasła, wybierz **filtru** znajdujący się u góry okienka.
7. Z **filtru** menu, wybierz opcję **kategorii** listy rozwijanej listy i zmień ją na **samoobsługowego zarządzania hasłami** typu kategorii.
8. Opcjonalnie, dokładniej przefiltrować listę, wybierając konkretne **działania** interesuje Cię.

### <a name="converged-registration-preview"></a>Osiągnięcia zbieżności rejestracji (wersja zapoznawcza)

Jeśli uczestniczysz w publicznej wersji zapoznawczej konwergentnej rejestracji informacje dotyczące działań użytkownika w dziennikach inspekcji zostanie znaleziony w kategorii **metod uwierzytelniania**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Opis kolumny raportu w witrynie Azure portal

Poniżej wyjaśniono każdej z kolumn raportu w witrynie Azure portal, szczegóły:

* **Użytkownik**: użytkownik, który podjął próbę hasła resetowania operacja rejestracji.
* **Rola**: roli użytkownika w katalogu.
* **Data i godzina**: Data i godzina próby.
* **Dane zarejestrowane**: dane uwierzytelniania użytkownika oferowane w trakcie hasło rejestracji resetowania.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Opis wartości raport w witrynie Azure portal

W poniższej tabeli opisano różne wartości, które są określane przez użytkownika dla każdej kolumny w witrynie Azure portal:

| Kolumna | Dozwolone wartości i ich znaczenie |
| --- | --- |
| Data zarejestrowana |**Alternatywny adres e-mail**: użytkownik użył alternatywny adres e-mail lub adres e-mail uwierzytelniania do uwierzytelniania.<p><p>**Telefon biurowy**: użytkownik używany telefon biurowy do uwierzytelniania.<p>**Telefon komórkowy**: użytkownik użył telefon komórkowy lub numer telefonu uwierzytelniania do uwierzytelniania.<p>**Pytania zabezpieczające**: użytkownik użył pytań zabezpieczających do uwierzytelniania.<p>**Dowolną kombinację poprzednich metod, na przykład, alternatywny adres e-mail i telefon komórkowy**: występuje, gdy zasada dwóch bram jest określona i dwie metody, które jest wyświetlana nazwa użytkownika używana do uwierzytelniania żądanie zresetowania hasła. |

## <a name="self-service-password-management-activity-types"></a>Samoobsługowe zarządzanie hasłami typy działań

Następujące typy działań są wyświetlane w **samoobsługowego zarządzania hasłami** kategorię zdarzeń inspekcji:

* [Zablokowany samoobsługowego resetowania haseł](#activity-type-blocked-from-self-service-password-reset): wskazuje, że użytkownik próbował zresetować hasło, korzystaj z określonej bramy lub Sprawdź poprawność numeru telefonu więcej niż pięć całkowity czas w ciągu 24 godzin.
* [Zmień hasło (samoobsługa)](#activity-type-change-password-self-service): oznacza, że użytkownik wykonywane dobrowolny lub wymuszonego (z powodu wygaśnięcia) zmianę hasła.
* [Resetuj hasło (przez administratora)](#activity-type-reset-password-by-admin): wskazuje, że administrator wykonywane resetowania w imieniu użytkownika z witryny Azure portal hasła.
* [Resetuj hasło (samoobsługa)](#activity-type-reset-password-self-service): wskazuje, że użytkownik pomyślnie Resetowanie własnego hasła [portal resetowania haseł usługi Azure AD](https://passwordreset.microsoftonline.com).
* [Samoobsługowe resetowanie haseł postęp działania przepływu](#activity-type-self-serve-password-reset-flow-activity-progress): wskazuje każdego określonego etapu obejmującego użytkownika, takie jak przekazywanie konkretnego hasła resetowania bramy uwierzytelniania, proces resetowania części hasła.
* [Odblokuj konto użytkownika (samoobsługa)](#activity-type-unlock-user-account-self-service): wskazuje, że użytkownik pomyślnie odblokowane swojego konta usługi Active Directory bez resetowania hasła z [portal resetowania haseł usługi Azure AD](https://passwordreset.microsoftonline.com) przy użyciu aktywnej Odblokuj funkcji katalogu konta bez resetowania.
* [Użytkownik zarejestrowany do samoobsługowego resetowania haseł](#activity-type-user-registered-for-self-service-password-reset): wskazuje, że użytkownik ma wszystkie wymagane informacje, aby można było do zresetowania swojego hasła, zgodnie z aktualnie określonego dzierżawcę zasady resetowania hasła.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ działania: zablokowany samoobsługowego resetowania haseł

Poniżej opisano to działanie szczegółowo:

* **Opis działania**: wskazuje, że użytkownik próbował zresetować hasło, korzystaj z określonej bramy lub Sprawdź poprawność numeru telefonu więcej niż pięć całkowity czas w ciągu 24 godzin.
* **Działanie aktora**: użytkownik, który został ograniczana wykonywać dodatkowe operacje resetowania. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownik, który został ograniczana wykonywać dodatkowe operacje resetowania. Użytkownik może być użytkownik końcowy lub administrator.
* **Stan działania**:
  * _Powodzenie_: wskazuje, że użytkownik została ograniczona z wykonywania żadnych dodatkowych resetuje, podjęto żadnych dodatkowych metod uwierzytelniania lub Sprawdzanie poprawności dowolnego dodatkowych numerów telefonów przez następne 24 godziny.
* **Przyczyny niepowodzenia stanu działania**: nie dotyczy.

### <a name="activity-type-change-password-self-service"></a>Typ działania: Zmień hasło (samoobsługa)

Poniżej opisano to działanie szczegółowo:

* **Opis działania**: oznacza, że użytkownik wykonywane dobrowolny lub wymuszonego (z powodu wygaśnięcia) zmianę hasła.
* **Działanie aktora**: użytkownik, który ich hasło zostało zmienione. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownik, który ich hasło zostało zmienione. Użytkownik może być użytkownik końcowy lub administrator.
* **Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie zmieniono hasło.
  * _Błąd_: wskazuje, że użytkownik nie można zmienić swoje hasło. Można wybrać wiersz, aby wyświetlić **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.
* **Przyczyny niepowodzenia stanu działania**: 
  * _FuzzyPolicyViolationInvalidPassword_: hasło, które zostało automatycznie zablokowane, ponieważ możliwości wykrywania hasło zakazane Microsoft uznała, że zbyt popularne lub szczególnie słabe wybrany przez użytkownika.

### <a name="activity-type-reset-password-by-admin"></a>Typ działania: resetowanie hasła (przez administratora)

Poniżej opisano to działanie szczegółowo:

* **Opis działania**: wskazuje, że administrator wykonywane resetowania w imieniu użytkownika z witryny Azure portal hasła.
* **Działanie aktora**: administrator, który resetowania w imieniu innego użytkownika lub administratora hasła. Musi być albo administrator globalny, administrator haseł, użytkownika administrator lub administrator pomocy technicznej.
* **Działania docelowego**: użytkownik, którego hasło zostało zresetowane. Użytkownik może być użytkownik końcowy lub innego administratora.
* **Stany działania**:
  * _Powodzenie_: wskazuje, że administrator pomyślnie zresetowano hasło użytkownika.
  * _Błąd_: wskazuje, że administrator nie można zmienić hasła użytkownika. Można wybrać wiersz, aby wyświetlić **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.

### <a name="activity-type-reset-password-self-service"></a>Typ działania: resetowanie hasła (samoobsługa)

Poniżej opisano to działanie szczegółowo:

* **Opis działania**: wskazuje, że użytkownik pomyślnie Resetowanie własnego hasła [portal resetowania haseł usługi Azure AD](https://passwordreset.microsoftonline.com).
* **Działanie aktora**: użytkownik, który zresetują swoje hasło. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownik, który zresetują swoje hasło. Użytkownik może być użytkownik końcowy lub administrator.
* **Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie resetowania ich własnych haseł.
  * _Błąd_: wskazuje, że użytkownik nie można zresetować swoje hasła. Można wybrać wiersz, aby wyświetlić **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.
* **Przyczyny niepowodzenia stanu działania**: 
  * _FuzzyPolicyViolationInvalidPassword_: administrator wybrane hasło, które zostało automatycznie zablokowane, ponieważ możliwości wykrywania hasło zakazane Microsoft uznała, że zbyt popularne lub szczególnie słabe.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ działania: pełnią samodzielnie postęp działania przepływu resetowania hasła

Poniżej opisano to działanie szczegółowo:

* **Opis działania**: wskazuje każdego kroku określonego użytkownika przechodzi przez, (takie jak przekazywanie konkretnego hasła zresetować bramę uwierzytelniania), jako część hasło proces resetowania.
* **Działanie aktora**: użytkownika, który wykonał część hasło Resetowanie usługi flow. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownika, który wykonał część hasło Resetowanie usługi flow. Użytkownik może być użytkownik końcowy lub administrator.
* **Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie ukończone określonego kroku przepływu resetowania hasła.
  * _Błąd_: wskazuje, że określony krok hasła resetowania przepływ zakończył się niepowodzeniem. Można wybrać wiersz, aby wyświetlić **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.
* **Przyczyny stanu działania**: Zobacz w poniższej tabeli [wszystkich resetowania dopuszczalne działania stanu powodów](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ działania: odblokować konto użytkownika (samoobsługa)

Poniżej opisano to działanie szczegółowo:

* **Opis działania**: wskazuje, że użytkownik pomyślnie odblokowane swojego konta usługi Active Directory bez resetowania hasła z [portal resetowania haseł usługi Azure AD](https://passwordreset.microsoftonline.com) przy użyciu funkcji usługi Active Directory odblokowywanie kont bez resetowania.
* **Działanie aktora**: użytkownik, który odblokowania konta bez resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownik, który odblokowania konta bez resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Dozwolone Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie odblokowane własnego konta.
  * _Błąd_: wskazuje, że użytkownik nie można odblokować konto. Można wybrać wiersz, aby wyświetlić **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ działania: Użytkownik zarejestrowany do samoobsługowego resetowania haseł

Poniżej opisano to działanie szczegółowo:

* **Opis działania**: wskazuje, że użytkownik ma wszystkie wymagane informacje, aby można było do zresetowania swojego hasła, zgodnie z aktualnie określonego dzierżawcę zasady resetowania hasła. 
* **Działanie aktora**: użytkownik, który zarejestrował do resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownik, który zarejestrował do resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Dozwolone Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie zarejestrowano na potrzeby resetowania zgodnie z obecnych zasad haseł. 
  * _Błąd_: wskazuje, że użytkownik nie można zarejestrować w celu zresetowania hasła. Można wybrać wiersz, aby wyświetlić **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu. 

     >[!NOTE]
     >Błąd nie oznacza, że użytkownik nie może zresetować własne hasło. Oznacza to, że nie kończą proces rejestracji. W przypadku niezweryfikowanych danych na ich konta, które jest poprawna, takie jak numer telefonu nie zostanie zweryfikowana, nawet jeśli ich nie została zweryfikowana ten numer telefonu, mogą nadal używać go do zresetowania swojego hasła. Aby uzyskać więcej informacji, zobacz [co się stanie po użytkownik rejestruje?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Przykład funkcji samoobsługowego resetowania HASEŁ działania inspekcji dzienników w usłudze Azure AD"
