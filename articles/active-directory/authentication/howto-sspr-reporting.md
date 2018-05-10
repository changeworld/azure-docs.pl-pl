---
title: Samoobsługowego resetowania hasła raporty — Azure Active Directory
description: Raporty dotyczące usługi Azure AD samoobsługi hasła Resetowanie zdarzenia
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 45183e51e9071647e07cfd0738522f0dfa28bd7b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opcje raportowania dla usługi Azure AD zarządzania hasłami

Po wdrożeniu w wielu organizacjach chcieli wiedzieć, jak elementy są używane lub jeśli samoobsługowego resetowania hasła (SSPR). Funkcja raportowania, która zapewnia usługi Azure Active Directory (Azure AD) pomaga w odpowiedzi na pytania za pomocą wbudowane raporty. Jeśli jest odpowiednio licencję, można również tworzyć zapytania niestandardowe.

![Raportowanie][Reporting]

Może być odpowiedzieć na następujące pytania przez raporty, które istnieją w ([portalu Azure]https://portal.azure.com/):

> [!NOTE]
> Musi być [administratora globalnego](../active-directory-assign-admin-roles-azure-portal.md), a użytkownik musi zdecydować się na tych danych do zebrania w imieniu swojej organizacji. Aby włączyć, należy odwiedzić **raportowania** tab lub inspekcji logowania co najmniej raz. Do tego czasu dane nie są zbierane dla Twojej organizacji.
>

* Ile osób zarejestrowano do resetowania hasła?
* Kto został zarejestrowany do resetowania hasła?
* Jakie dane osób rejestracji?
* Ile osób do resetowania hasła w ciągu ostatnich siedmiu dni
* Co to są najbardziej popularnych metod, które umożliwia resetowanie haseł użytkowników lub administratorów?
* Jakie są typowe problemy użytkownicy lub Administratorzy krój podczas próby użycia resetowania hasła?
* Jakie Administratorzy są często Resetowanie własnych haseł?
* Czy istnieje wszelkich podejrzanych działań z przejściem z funkcją resetowania hasła?

## <a name="power-bi-content-pack"></a>Pakiet zawartości Power BI

Jeśli jesteś użytkownikiem usługi Power BI istnieje pakiet zawartości usługi Azure AD, która zawiera łatwy w użyciu raportowania dla usługi SSPR. Aby uzyskać więcej informacji na temat użycia i wdrożyć pakiet zawartości, zobacz [jak używać usługi Azure Active Directory Power BI content pack](../active-directory-reporting-power-bi-content-pack-how-to.md). Pakiet zawartości można tworzyć własne pulpity nawigacyjne i udostępniać je z innymi osobami w organizacji.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Jak wyświetlić raporty zarządzania hasło w portalu Azure

W portalu Azure doświadczenia firma Microsoft ulepszyła sposób, że można wyświetlić resetowania hasła i rejestracji aktywność resetowania haseł. Należy użyć następującego resetowania kroki, aby znaleźć hasło i zdarzenia rejestracji resetowania haseł:

1. Przejdź do [portalu Azure](https://portal.azure.com).
2. Wybierz **wszystkie usługi** w okienku po lewej stronie.
3. Wyszukaj **usługi Azure Active Directory** na liście usług i zaznacz je.
4. Wybierz **użytkowników i grup**.
5. Wybierz **dzienników inspekcji** z **użytkowników i grup** menu. To pokazuje wszystkie zdarzenia inspekcji, które wystąpiły względem wszystkich użytkowników w katalogu. Ten widok, aby wyświetlić wszystkie zdarzenia związane z hasła można filtrować.
6. Aby filtrować ten widok, aby wyświetlić tylko zdarzenia dotyczące resetowania hasła, wybierz **filtru** u góry okienka.
7. Z **filtru** menu, wybierz opcję **kategorii** rozwijania listy i zmień go na **Samoobsługowe zarządzanie hasłami** typu kategorii.
8. Opcjonalnie dokładniej przefiltrować listę, wybierając konkretnym **działania** Cię interesują.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Jak pobrać hasło zdarzeń zarządzania raportów usługi Azure AD i interfejsu API zdarzenia

Raporty usługi Azure AD i interfejsu API zdarzenia obsługuje pobierania wszystkich informacji objętych resetowania hasła i raporty rejestracji resetowania hasła. Przy użyciu tego interfejsu API, można pobrać hasła poszczególnych resetowania hasła resetowania rejestracji zdarzeń i i ich integracji z wykorzystaniem technologii raportowania wybranych przez użytkownika.

> [!IMPORTANT]
> Obecnie raportów usługi Azure AD i interfejsu API zdarzenia pobiera do *75,000 pojedynczych zdarzeń* z [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) i [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) typów. Interfejs API obejmuje ostatnich 30 dni.
> 
> Jeśli musisz pobrać ani do przechowywania danych po przekroczeniu tego okna, zalecamy utrwalanie go w zewnętrznej bazy danych, korzystając z interfejsu API do badania delty, które powodują. Zaleca się zacząć pobrać dane, po rozpoczęciu pracy SSPR w Twojej organizacji. Utrwalić go zewnętrznie, a następnie przejdź do śledzenia może od tej pory.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>Jak rozpocząć pracę z interfejsem API raportowania

Aby uzyskać dostęp do tych danych, należy napisać małych aplikacji lub skryptu, można pobrać go ze swoich serwerów. Aby uzyskać więcej informacji, zobacz [wprowadzenie do raportowania interfejsu API usługi Azure AD](../active-directory-reporting-api-getting-started-azure-portal.md).

Po utworzeniu skryptu pracy chcesz zbadać hasła resetowania i rejestracji zdarzenia, które można pobrać, aby spełnić scenariuszy:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): Wyświetla listę dostępnych kolumn zdarzeń resetowania hasła.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): Wyświetla listę dostępnych kolumn dla zdarzenia rejestracji resetowania hasła.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Opis kolumn raportu w portalu Azure

Poniżej wyjaśniono poszczególnych kolumn raportu w portalu Azure w szczegółach:

* **Użytkownik**: użytkownika, które próbowały hasła zresetować operacji rejestracji.
* **Rola**: roli użytkownika w katalogu.
* **Data i godzina**: Data i godzina próby.
* **Dane zarejestrowane**: dane uwierzytelniania użytkownika podana podczas hasło rejestracji resetowania.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Opis wartości raportu w portalu Azure

W poniższej tabeli opisano różne wartości, które można ustawić dla każdej kolumny w portalu Azure:

| Kolumna | Dozwolone wartości i ich znaczenie |
| --- | --- |
| Data zarejestrowana |**Alternatywny adres e-mail**: użytkownik używany do uwierzytelniania alternatywny adres e-mail lub adres e-mail uwierzytelniania.<p><p>**Telefon biurowy**: użytkownik używany telefon biurowy do uwierzytelniania.<p>**Telefon komórkowy**: użytkownik używany do uwierzytelniania telefon komórkowy lub numer telefonu uwierzytelniania.<p>**Pytania zabezpieczające**: użytkownik pytania zabezpieczeń używane do uwierzytelniania.<p>**Dowolną kombinację wcześniejszych metod, na przykład alternatywny poczta + telefon komórkowy**: występuje, gdy zasady dwa bramy jest określona i przedstawia które dwie metody użytkownika służące do uwierzytelniania żądanie zresetowania hasła. |

## <a name="self-service-password-management-activity-types"></a>Samoobsługowe zarządzanie hasłami typów działań

Następujące typy działań są wyświetlane w **Samoobsługowe zarządzanie hasłami** kategorię zdarzenia inspekcji:

* [Zablokowano dostęp do samodzielnego resetowania hasła](#activity-type-blocked-from-self-service-password-reset): wskazuje, że użytkownik próbował zresetować hasło, użyj określonych bramy lub sprawdzania poprawności numeru telefonu więcej niż pięć razy całkowita w ciągu 24 godzin.
* [Zmień hasło (internetowy)](#activity-type-change-password-self-service): oznacza, że użytkownik wykonać dobrowolny lub wymusić (z powodu wygaśnięcia) zmiany hasła.
* [Zresetuj hasło (Administrator)](#activity-type-reset-password-by-admin): Określa, że administrator wykonane resetowania w imieniu użytkownika z portalu Azure hasła.
* [Zresetuj hasło (internetowy)](#activity-type-reset-password-self-service): wskazuje, że użytkownik pomyślnie resetowania hasła z [portal resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com).
* [Samoobsługowego resetowania hasła postępu działań przepływu](#activity-type-self-serve-password-reset-flow-activity-progress): wskazuje każdego kroku określonego obejmującego użytkownika, takich jak przekazywanie określonego hasła zresetować bramę uwierzytelniania, jako część hasło procesu resetowania.
* [Odblokować konto użytkownika (internetowy)](#activity-type-unlock-user-account-self-service): wskazuje, że użytkownik pomyślnie odblokowane swojego konta usługi Active Directory bez zresetowania hasła z [portal resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com) przy użyciu aktywne Funkcja katalogu konta odblokowanie bez konieczności resetowania.
* [Użytkownik jest zarejestrowany dla Samoobsługowe Resetowanie hasła](#activity-type-user-registered-for-self-service-password-reset): wskazuje, że użytkownik ma wszystkie wymagane informacje, aby można było do zresetowania swojego hasła zgodnie z obecnie określonego dzierżawcę zasady resetowania hasła.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ działania: zablokowano samoobsługowego resetowania hasła

Poniżej wyjaśniono tego działania szczegółowo:

* **Opis działania**: wskazuje, że użytkownik próbował zresetować hasło, użyj określonych bramy lub sprawdzania poprawności numeru telefonu więcej niż pięć razy całkowita w ciągu 24 godzin.
* **Działanie aktora**: użytkownika, który został ograniczony wykonywać dodatkowe operacje resetowania. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownika, który został ograniczony wykonywać dodatkowe operacje resetowania. Użytkownik może być użytkownik końcowy lub administrator.
* **Stan działania**:
  * _Powodzenie_: wskazuje, że użytkownik został ograniczony z wykonywania żadnych dodatkowych resetuje, podjęciem próby wykonania żadnych dodatkowych metod uwierzytelniania lub sprawdzania poprawności żadnych dodatkowych numerów telefonów przez następne 24 godziny.
* **Przyczyna niepowodzenia stanu działania**: nie dotyczy.

### <a name="activity-type-change-password-self-service"></a>Typ działania: Zmień hasło (internetowy)

Poniżej wyjaśniono tego działania szczegółowo:

* **Opis działania**: oznacza, że użytkownik wykonać dobrowolny lub wymusić (z powodu wygaśnięcia) zmiany hasła.
* **Działanie aktora**: użytkownika, który zmienił swoje hasło. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownika, który zmienił swoje hasło. Użytkownik może być użytkownik końcowy lub administrator.
* **Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie zmieniono hasło.
  * _Błąd_: wskazuje, że użytkownik nie można zmienić swoje hasło. Możesz wybrać wiersz, aby zobaczyć **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.
* **Przyczyna niepowodzenia stanu działania**: 
  * _FuzzyPolicyViolationInvalidPassword_: użytkownik wybrał hasło, które zostało automatycznie zakazane, ponieważ możliwości wykrywania hasła zakazane znaleźć się zbyt popularne lub szczególnie słabe.

### <a name="activity-type-reset-password-by-admin"></a>Typ działania: resetowania hasła (Administrator)

Poniżej wyjaśniono tego działania szczegółowo:

* **Opis działania**: Określa, że administrator wykonane resetowania w imieniu użytkownika z portalu Azure hasła.
* **Działanie aktora**: administrator, który resetowania w imieniu innego użytkownika lub administratora hasła. Musi być administratorem globalnym, hasło administratora, administrator użytkownika albo administratora pomocy technicznej.
* **Działania docelowego**: użytkownika, którego hasło zostało zresetowane. Użytkownik może być użytkownik końcowy lub innego administratora.
* **Stany działania**:
  * _Powodzenie_: wskazuje, że administrator pomyślnie zresetowano hasło użytkownika.
  * _Błąd_: wskazuje, że administrator nie można zmienić hasła użytkownika. Możesz wybrać wiersz, aby zobaczyć **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.

### <a name="activity-type-reset-password-self-service"></a>Typ działania: resetowania hasła (internetowy)

Poniżej wyjaśniono tego działania szczegółowo:

* **Opis działania**: wskazuje, że użytkownik pomyślnie resetowania hasła z [portal resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com).
* **Działanie aktora**: użytkownika, który resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownika, który resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Stany działania**:
  * _Powodzenie_: wskazuje, czy użytkownik pomyślnie resetować swoje hasła.
  * _Błąd_: wskazuje, że użytkownik nie można zresetować swoje hasła. Możesz wybrać wiersz, aby zobaczyć **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.
* **Przyczyna niepowodzenia stanu działania**: 
  * _FuzzyPolicyViolationInvalidPassword_: administrator wybrane hasło, które zostało automatycznie zakazane, ponieważ możliwości wykrywania hasła zakazane znaleźć się zbyt popularne lub szczególnie słabe.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ działania: Self służyć postępu działań przepływu resetowania hasła

Poniżej wyjaśniono tego działania szczegółowo:

* **Opis działania**: wskazuje każdego kroku określonego użytkownika obejmującego (takich jak przekazywanie określonego hasła zresetować bramę uwierzytelniania), jako część hasło procesu resetowania.
* **Działanie aktora**: użytkownik, który wykonał część hasło zresetować przepływu. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownik, który wykonał część hasło zresetować przepływu. Użytkownik może być użytkownik końcowy lub administrator.
* **Stany działania**:
  * _Powodzenie_: oznacza, że użytkownik pomyślnie zakończony określonego kroku przepływu resetowania hasła.
  * _Błąd_: wskazuje, że określonego kroku hasła są resetowane przepływu nie powiodło się. Możesz wybrać wiersz, aby zobaczyć **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.
* **Przyczyny stanu działania**: Zobacz poniższą tabelę dla [wszystkich resetowania dopuszczalne działania stanu powodów](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ działania: odblokować konto użytkownika (internetowy)

Poniżej wyjaśniono tego działania szczegółowo:

* **Opis działania**: wskazuje, że użytkownik pomyślnie odblokowane swojego konta usługi Active Directory bez zresetowania hasła z [portal resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com) za pomocą funkcji usługi Active Directory odblokowywanie kont bez resetowania.
* **Działanie aktora**: użytkownika, który odblokowane ich kont bez zresetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownika, który odblokowane ich kont bez zresetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Dozwolone Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie odblokowane ich własnego konta.
  * _Błąd_: wskazuje, że użytkownik nie można odblokować konto. Możesz wybrać wiersz, aby zobaczyć **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ działania: użytkownik jest zarejestrowany dla samoobsługowego resetowania hasła

Poniżej wyjaśniono tego działania szczegółowo:

* **Opis działania**: wskazuje, że użytkownik ma wszystkie wymagane informacje, aby można było do zresetowania swojego hasła zgodnie z obecnie określonego dzierżawcę zasady resetowania hasła. 
* **Działanie aktora**: użytkownik, który zarejestrował do resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Działania docelowego**: użytkownik, który zarejestrował do resetowania hasła. Użytkownik może być użytkownik końcowy lub administrator.
* **Dozwolone Stany działania**:
  * _Powodzenie_: wskazuje, że użytkownik pomyślnie zarejestrowany do resetowania zgodnie z zasadami bieżącego hasła. 
  * _Błąd_: wskazuje, że użytkownik nie można zarejestrować na potrzeby resetowania haseł. Możesz wybrać wiersz, aby zobaczyć **przyczyny stanu działania** kategorię, aby dowiedzieć się więcej na temat przyczyny błędu. 

     >[!NOTE]
     >Błąd nie oznaczają, że użytkownik nie może zresetować swoje hasła. Oznacza to, że nie zostało zakończone się procesu rejestracji. Jeśli konto jest prawidłowe, takich jak numer telefonu, który nie jest weryfikowany, nawet jeśli nie zweryfikowano ten numer telefonu jest niezweryfikowane dane, ich go użyć do zresetowania swojego hasła. Aby uzyskać więcej informacji, zobacz [co się dzieje, gdy użytkownik rejestruje?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Przykład SSPR działania inspekcji logowania w usłudze Azure AD"
