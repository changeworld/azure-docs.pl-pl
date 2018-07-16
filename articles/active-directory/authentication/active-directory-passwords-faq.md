---
title: Samoobsługowe resetowanie haseł — często zadawane pytania — usługi Azure Active Directory
description: Często zadawane pytania dotyczące haseł usługi Azure AD z samoobsługowego resetowania
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: c006e448b8da1acaf51c8339cbcd0b6170f29874
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054815"
---
# <a name="password-management-frequently-asked-questions"></a>Zarządzanie hasłami — często zadawane pytania

Poniżej przedstawiono niektóre często zadawane pytania (FAQ) wszystkie elementy związane z do zresetowania hasła.

Jeśli masz pytanie ogólne dotyczące usługi Azure Active Directory (Azure AD) i samoobsługowego resetowania haseł (SSPR), nie ma tutaj odpowiedzi, możesz zadawać społeczności o pomoc na [forum usługi Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Członkowie społeczności obejmują inżynierów, menedżerów produktów, specjalistami MVP i innymi specjalistów IT.

Tych często zadawanych Pytaniach jest podzielona na następujące sekcje:

* [Pytania dotyczące hasło rejestracji resetowania](#password-reset-registration)
* [Pytania dotyczące resetowania hasła](#password-reset)
* [Pytania dotyczące zmiany hasła](#password-change)
* [Pytania dotyczące raporty dotyczące zarządzania hasłami](#password-management-reports)
* [Pytania dotyczące funkcji zapisywania zwrotnego haseł](#password-writeback)

## <a name="password-reset-registration"></a>Rejestracja w resetowaniu haseł

* **P: czy można Moi użytkownicy mogą zarejestrować swoje własne dane resetowania haseł?**

  > **Odpowiedź:** tak. Tak długo, jak resetowania hasła jest włączona, i są one licencjonowane, użytkownicy mogą przejść do portalu rejestracji resetowania haseł (https://aka.ms/ssprsetup) zarejestrować swoich informacji uwierzytelniania. Użytkownicy mogą również rejestrować za pomocą panelu dostępu (http://myapps.microsoft.com). Aby zarejestrować się za pomocą panelu dostępu, muszą wybierz swój obraz profilu, wybierz pozycję **profilu**, a następnie wybierz pozycję **rejestracji resetowania haseł** opcji.
  >
  >
* **Pyt.: Jeśli włączyć hasła Resetowanie dla grupy i zdecydować ją włączyć dla wszystkich moich użytkowników wymagane ponownie zarejestrować?**

  > **Odpowiedź:** Nie. Użytkownicy, którzy wypełnili dane uwierzytelniania nie trzeba ponownie zarejestrować.
  >
  >
* **P: czy można zdefiniować dane resetowania haseł w imieniu użytkowników?**

  > **Odp.:** tak, możesz to zrobić za pomocą usługi Azure AD Connect, programu PowerShell, [witryny Azure portal](https://portal.azure.com), lub w Centrum administracyjnym usługi Office 365. Aby uzyskać więcej informacji, zobacz [dane używane przez haseł usługi Azure AD resetowania](howto-sspr-authenticationdata.md).
  >
  >
* **P: czy można synchronizować dane odpowiedzi na pytania zabezpieczające ze środowiska lokalnego?**

  > **Odp.:** nie, nie jest to możliwe już dziś.
  >
  >
* **P: czy można Moi użytkownicy rejestrowanie danych w taki sposób, że inni użytkownicy nie mogą widzieć tych danych**

  > **Odpowiedź:** tak. Kiedy użytkownik rejestruje portal rejestracji resetowania danych przy użyciu hasła, dane są zapisywane do pól prywatnych uwierzytelniania, które są widoczne tylko dla administratorów globalnych i użytkownika.
  >
  >
* **P: czy Moi użytkownicy muszą być zarejestrowane, zanim rozpoczną korzystanie z resetowania haseł?**

  > **Odpowiedź:** Nie. Jeśli zdefiniujesz wystarczających informacji do uwierzytelniania w ich imieniu, użytkownicy nie musieli zarejestrować. Działania resetowania hasła, tak długo, jak poprawnie sformatowanym danych przechowywanych w odpowiednich polach w katalogu.
  >
  >
* **P: czy mogę synchronizacji lub ustaw pola Telefon uwierzytelniania alternatywny numer telefonu uwierzytelniania, adres e-mail uwierzytelniania lub w imieniu użytkowników?**

  > **Odp.:** pola, które mogą być ustawione przez administratora globalnego są zdefiniowane w artykule [wymagania dotyczące funkcji samoobsługowego resetowania HASEŁ danych](howto-sspr-authenticationdata.md).
  >
  >
* **P: jak portal rejestracji określić które opcje, aby pokazać Moi użytkownicy?**

  > **Odp.:** pokazuje portalu rejestracji resetowania haseł, tylko te opcje, które włączono dla użytkowników. Te opcje można znaleźć w obszarze **zasady resetowania hasła użytkownika** sekcji swojego katalogu **Konfiguruj** kartę. Na przykład jeśli nie włączysz pytań zabezpieczających, następnie użytkownicy nie będą mogli zarejestrować dla tej opcji.
  >
  >
* **Pytanie: kiedy użytkownik uznaje się zarejestrować?**

  > **Odp.:** użytkownik jest traktowany jako zarejestrowany do samoobsługowego resetowania HASEŁ, gdy zarejestrowanych co najmniej **liczba metod wymaganych do zresetowania** hasła, które zostało ustawione w [witryny Azure portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetowanie hasła

* **P: czy można uniemożliwić użytkownikom wielokrotne próby zresetowania hasła w krótkim czasie?**

  > **Odp.:** tak, są dostępne funkcje zabezpieczeń wbudowane w resetowania hasła, aby chronić je przed niewłaściwym użyciem również. 
  >
  > Użytkownikom można spróbować tylko pięć prób resetowania hasła w okresie 24 godzin, zanim one zablokowane przez 24 godziny. 
  >
  > Użytkownicy mogą próbować weryfikacji numeru telefonu, Wyślij wiadomość SMS lub zweryfikuj pytania zabezpieczające i odpowiedzi tylko pięć razy w ciągu godziny są zablokowane przez 24 godziny. 
  >
  > Użytkownicy mogą wysyłać wiadomości e-mail maksymalnie 10 razy w okresie 10 minut, zanim one zablokowane przez 24 godziny.
  >
  > Liczniki zostaną zresetowane po użytkownik resetuje hasła.
  >
  >
* **Pytanie: jak długo należy czekać do odbierania wiadomości e-mail, SMS lub połączenie telefoniczne z resetowania haseł?**

  > **Odp.:** wiadomości E-mail, wiadomości SMS i połączeń telefonicznych powinien pojawić się w niż minutę. Normalna sytuacja jest 5-20 sekund.
    >Jeśli nie otrzymasz powiadomienie, w tym horyzoncie czasowym:
        > * Sprawdź folder wiadomości-śmieci.
        > * Sprawdź, czy numer lub adres e-mail, podejmowana jest jeden, których oczekujesz.
        > * Upewnij się, że dane uwierzytelniania w katalogu jest poprawnie sformatowana, na przykład 4255551234 + 1 lub *user@contoso.com*. 
  >
  >
* **P: jakie języki są obsługiwane przez resetowania haseł?**

  > **Odp.:** resetowania hasła użytkownika wiadomości SMS i połączeń głosowych są zlokalizowane w te same języki, które są obsługiwane w usłudze Office 365.
  >
  >
* **P: jakie części środowiska resetowania hasła Pobierz marki, gdy ustawić elementy znakowania organizacji w moim katalogu użytkownika skonfigurować kartę?**

  > **Odp.:** portal resetowania haseł pokazuje logo organizacji i pozwala na skonfigurowanie link "Skontaktuj się z administratorem", aby wskazywać niestandardowy adres e-mail lub adres URL. Każdą wiadomość e-mail wysyłana przez resetowania haseł obejmuje logo organizacji, kolory i nazwy w treści wiadomości e-mail i dostosowanych ustawień dla tej określonej nazwy.
  >
  >
* **P: jak można Poinformuj użytkowników o tym, gdzie przejść do resetowania swoich haseł?**

  > **A:** wypróbować niektóre sugestie w naszej [wdrożenie funkcji samoobsługowego resetowania HASEŁ](howto-sspr-deployment.md#email-based-rollout) artykułu.
  >
  >
* **P: czy można użyć tej strony z urządzenia przenośnego?**

  > **Odp.:** tak, ta strona działa na urządzeniach przenośnych.
  >
  >
* **P: odblokowywanie konta lokalne usługi Active Directory są obsługiwane podczas użytkownikom Resetowanie swoich haseł?**

  > **Odpowiedź:** tak. Po użytkownik resetuje hasła, jeśli został wdrożony funkcja zapisywania zwrotnego haseł przy użyciu usługi Azure AD Connect, konto tego użytkownika zostanie automatycznie odblokowana, gdy ich zresetują swoje hasło.
  >
  >
* **P: jak mogę zintegrować resetowania bezpośrednio do mojego użytkownika logowania środowisko pulpitu**

  > **Odp.:** Jeśli jesteś klientem usługi Azure AD Premium, można zainstalować programu Microsoft Identity Manager bez ponoszenia dodatkowych kosztów i wdrażać rozwiązania resetowania hasła lokalnego.
  >
  >
* **P: czy można ustawić pytania zabezpieczające różne dla różnych ustawień regionalnych?**

  > **Odp.:** nie, nie jest to możliwe już dziś.
  >
  >
* **P: jak najwięcej pytań do odpowiedzi można skonfigurować opcję uwierzytelniania pytania zabezpieczeń?**

  > **Odp.:** można skonfigurować maksymalnie 20 niestandardowe pytania zabezpieczające w [witryny Azure portal](https://portal.azure.com).
  >
  >
* **Pytanie: jak długo może być pytania zabezpieczające?**

  > **Odp.:** pytania zabezpieczeń może zawierać 3 do 200 znaków.
  >
  >
* **Pytanie: jak długo może być odpowiedzi na pytania zabezpieczające?**

  > **Odp.:** odpowiedzi może zawierać 3 do 40 znaków.
  >
  >
* **P: czy zduplikowane odpowiedzi na pytania zabezpieczające odrzucone?**

  > **Odp.:** tak, mamy Odrzuć zduplikowane odpowiedzi na pytania zabezpieczające.
  >
  >
* **P: czy użytkownik zarejestrować na tym samym pytanie zabezpieczające więcej niż jeden raz?**

  > **Odpowiedź:** Nie. Po użytkownik rejestruje określone pytanie, ich nie można zarejestrować na to pytanie po raz drugi.
  >
  >
* **P: czy można ustawić minimalny limit pytania zabezpieczające dla rejestracji i resetowania?**

  > **Odp.:** tak, można ustawić limitu dla rejestracji i inny wpis dla resetowania. Trzech do pięciu pytań zabezpieczających, może być wymagane do rejestracji, a trzech do pięciu pytań, może być wymagane do resetowania.
  >
  >
* **P: czy mogę skonfigurować Moje zasad, aby wymagać od użytkowników użyj pytań zabezpieczających w celu zresetowania, ale administratorów platformy Azure wydaje się, że można skonfigurować inaczej.**

  > **Odp.:** jest to oczekiwane zachowanie. Firma Microsoft wymusza silne domyślne zasad resetowania haseł brama dwóch dla dowolnej roli administratora platformy Azure. Zapobiega to za pomocą pytań zabezpieczających przez administratorów. Można znaleźć więcej informacji na temat tych zasad w [zasady i ograniczenia w usłudze Azure Active Directory haseł](concept-sspr-policy.md#administrator-password-policy-differences) artykułu.
  >
  >
* **Pyt.: Jeśli użytkownik został zarejestrowany w więcej niż maksymalna liczba pytań wymaganych do zresetowania, jak są pytania zabezpieczające wybranych podczas resetowania?**

  > **Odp.:** *N* liczbę pytań zabezpieczających jest wybranych losowo poza całkowita liczba pytań, które użytkownik zarejestrował w którym *N* jest ustawiona dla **numer pytań wymaganych do zresetowania** opcji. Na przykład jeśli użytkownik został zarejestrowany pięć pytań zabezpieczających, ale tylko trzy są wymagane, aby zresetować hasło, trzy pięć pytań są wybierane losowo i są prezentowane w resetowania. Aby zapobiec pytanie atakowaniu, jeśli użytkownik uzyska odpowiedzi na pytania niewłaściwego procesu wyboru uruchamiany ponownie.
  >
  >
* **Pytanie: jak długo są poczty e-mail i SMS jednorazowe kodów dostępu prawidłowy?**

  > **Odp.:** okres istnienia sesji w celu zresetowania hasła wynosi 15 minut. Od czasu rozpoczęcia operacji resetowania hasła użytkownik musi 15 minut do zresetowania swojego hasła. Po upływie tego czasu do czasu poczty e-mail i SMS jednorazowy kod dostępu są nieprawidłowe.
  >
  >
* **P: czy można zablokować użytkownikom możliwość resetowania hasła?**

  > **Odp.:** tak, jeśli używasz grupy w celu włączenia funkcji samoobsługowego resetowania HASEŁ, możesz usunąć użytkownika z grupy, która umożliwia użytkownikom zresetowanie hasła. Jeśli użytkownik jest administratorem globalnym one zachowają możliwość zresetowania hasła, a nie może być wyłączony.
  >
  >

## <a name="password-change"></a>Zmiana hasła

* **Pyt.: gdzie Moi użytkownicy powinien przejść do zmiany haseł?**

  > **A:** użytkownicy mogą zmieniać swoje hasła wszędzie zobaczy ich zdjęcie profilowe lub ikonę, np. w prawym górnym rogu ich [usługi Office 365](https://portal.office.com) portalu lub [panelu dostępu](https://myapps.microsoft.com) środowisk. Użytkownicy mogą zmieniać swoje hasła niezależnie od [profilu panelu dostępu do strony](https://account.activedirectory.windowsazure.com/r#/profile). Użytkownicy mogą się prośba o zmiany haseł automatycznie strony logowania usługi Azure AD, jeśli ich hasła wygasły. Ponadto użytkownicy mogą przeglądać do [portal zmieniania haseł usługi Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) bezpośrednio zmienić swoje hasła.
  >
  >
* **P: czy można Moi użytkownicy otrzymają powiadomienia w portalu usługi Office po wygaśnięciu hasła w środowisku lokalnym?**

  > **Odp.:** tak, jest to możliwe już dziś, jeśli korzystasz z usługi Active Directory Federation Services (AD FS). Jeśli używasz usług AD FS, postępuj zgodnie z instrukcjami [wysyłania oświadczenia zasad haseł za pomocą usług AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artykułu. Jeśli używasz synchronizacji skrótów haseł, nie jest to możliwe już dziś. Firma Microsoft nie są synchronizowane zasady haseł z katalogami lokalnymi, więc nie jest możliwe w firmie Microsoft w celu wpis powiadomień o wygasaniu do środowiska w chmurze. W obu przypadkach istnieje również możliwość [powiadomić użytkowników, których hasła są wygaśnie za pomocą programu PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P: czy można zablokować użytkownikom możliwość zmieniania swojego hasła?**

  > **Odp.:** dla użytkowników tylko w chmurze, nie można zablokować zmiany hasła. Dla użytkowników w środowisku lokalnym, możesz ustawić **użytkownik nie może zmienić hasła** opcję, aby wybrane. Wybrani użytkownicy nie można zmienić swoje hasło.
  >
  >

## <a name="password-management-reports"></a>Raporty dotyczące zarządzania hasłami

* **Pytanie: jak długo trwa dane wyświetlane na raporty dotyczące zarządzania hasłami?**

  > **Odp.:** dane powinny się pojawić na raporty dotyczące zarządzania hasłami w ciągu 5 – 10 minut. W niektórych przypadkach może potrwać do godziny się pojawić.
  >
  >
* **P: jak filtrować raporty dotyczące zarządzania hasłami**

  > **Odp.:** do filtrowania raportów zarządzania hasła, wybierz mały szkła powiększającego się najdalej po prawej stronie etykiety kolumn, w górnej części raportu. Jeśli chcesz bardziej zaawansowane filtrowanie, możesz pobrać raport do programu Excel i Utwórz tabelę przestawną.
  >
  >
* **Pytanie: jaka jest maksymalna liczba zdarzeń, które są przechowywane w raporty dotyczące zarządzania hasłami?**

  > **Odp.:** maksymalnie 75,000 hasło zdarzenia rejestracji resetowania resetowania lub hasło są przechowywane w raporty dotyczące zarządzania hasłami, obejmujące Wstecz, o ile to 30 dni. Pracujemy, aby rozwinąć tę liczbę zarejestrować więcej zdarzeń.
  >
  >
* **P: jak daleko raporty dotyczące zarządzania hasłami go?**

  > **Odp.:** zarządzania hasłami raporty show operacje, które wystąpiły w ciągu ostatnich 30 dni. Teraz Jeśli chcesz zarchiwizować te dane można można pobrać raportów okresowo i zapisywać je w innej lokalizacji.
  >
  >
* **P: czy istnieje maksymalna liczba wierszy, które mogą być wyświetlane w raportach zarządzania hasło?**

  > **Odpowiedź:** tak. Maksymalnie 75,000 wierszy mogą występować w dowolnym raporty dotyczące zarządzania hasłami, czy są wyświetlane w interfejsie użytkownika, czy zostały pobrane.
  >
  >
* **P: czy istnieje interfejs API umożliwiający dostęp do resetowania hasła lub rejestracji dane raportowania?**

  > **Odpowiedź:** tak. Aby dowiedzieć się, jak można pobrać resetowania haseł, strumień danych raportowania, zobacz [Dowiedz się, jak dostęp do resetowania hasła zdarzeń do raportowania programowo](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Zapisywanie zwrotne haseł

* **P: jak działa funkcja zapisywania zwrotnego haseł w tle**

  > **Odp.:** znajduje się w artykule [sposobu działania funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md) wyjaśnienie co się stanie po włączeniu funkcji zapisywania zwrotnego haseł i sposób przepływu danych przez system z powrotem do środowiska lokalnego.
  >
  >
* **P: jak zapisywanie zwrotne haseł długo pracę? Jest tam synchronizacji opóźnienie tak jak ma to za pomocą synchronizacji skrótów haseł?**

  > **Odp.:** zapisywanie zwrotne haseł jest natychmiastowe. Jest synchroniczne potok, który działa w sposób całkowicie inny niż synchronizacji skrótów haseł. Zapisywanie zwrotne haseł umożliwia użytkownikom do pobrania w czasie rzeczywistym informacje o udanym resetowania swoich haseł lub operacji zmiany. Średni czas na pomyślne funkcji zapisywania zwrotnego haseł jest poniżej 500 ms.
  >
  >
* **P: Jeśli Moje konto lokalne jest wyłączone, w jaki sposób Mój konta w chmurze i dostępu, których dotyczy problem?**

  > **Odp.:** wyłączenie swój identyfikator w środowisku lokalnym swój identyfikator chmury i dostęp również zostaną wyłączone w następnym interwale synchronizacji za pomocą usługi Azure AD Connect. Domyślnie ta synchronizacja jest co 30 minut.
  >
  >
* **P: Jeśli Moje konto lokalne jest ograniczony przez w lokalnych zasadach haseł usługi Active Directory, samoobsługowego resetowania HASEŁ przestrzegają tych zasad podczas zmiany hasła?**

  > **Odp.:** tak, opiera się na funkcji samoobsługowego resetowania HASEŁ i przestrzega zasad haseł usługi Active Directory w środowisku lokalnym. Te zasady zawierają typowe zasady haseł domeny usługi Active Directory, a także żadnych zasad zdefiniowanych, precyzyjny hasła, które są stosowane do użytkownika.
  >
  >
* **P: jakie rodzaje kont zapisywanie zwrotne haseł działa dla?**

  > **Odp.:** działania funkcji zapisywania zwrotnego haseł dla kont użytkowników, które są synchronizowane z usługi Active Directory lokalnych z usługą Azure AD, w tym federacyjnych, skrótów haseł, synchronizacji i przekazywane Autentication użytkowników.
  >
  >
* **Pytanie: czy funkcji zapisywania zwrotnego haseł wymusza zasady haseł w mojej domeny?**

  > **Odpowiedź:** tak. Zapisywanie zwrotne haseł wymusza okres ważności hasła, historii, złożoność, filtry i inne ograniczenia, które można umieścić w miejscu na hasłach, w domenie lokalnej.
  >
  >
* **P: czy funkcja zapisywania zwrotnego haseł bezpieczne?  Jak mogę mieć pewność, że nie mogę włamania?**

  > **Odp.:** tak, zapisywanie zwrotne haseł jest bezpieczne. Aby dowiedzieć się więcej na temat cztery warstwy zabezpieczeń implementowany przez usługi zapisywania zwrotnego haseł, zapoznaj się z [model zabezpieczeń funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md#password-writeback-security-model) sekcji [Przegląd funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md) artykułu.
  >
  >

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
