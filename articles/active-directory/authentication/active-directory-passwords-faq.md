---
title: Samoobsługowe resetowanie haseł — często zadawane pytania — usługi Azure Active Directory
description: Często zadawane pytania dotyczące haseł usługi Azure AD z samoobsługowego resetowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77154ef35242c55724becb77595dbd5ecf8a4da9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60359062"
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

## <a name="password-reset-registration"></a>Rejestracja w funkcji resetowania haseł

* **Pyt.:  Moi użytkownicy, można zarejestrować własne dane resetowania hasła?**

  > **Odp.:** Tak. Tak długo, jak resetowania hasła jest włączona, i są one licencjonowane, użytkownicy mogą przejść do portalu rejestracji resetowania haseł (https://aka.ms/ssprsetup) zarejestrować swoich informacji uwierzytelniania. Użytkownicy mogą również rejestrować za pomocą panelu dostępu (https://myapps.microsoft.com). Aby zarejestrować się za pomocą panelu dostępu, muszą wybierz swój obraz profilu, wybierz pozycję **profilu**, a następnie wybierz pozycję **rejestracji resetowania haseł** opcji.
  >
  >
* **Pyt.:  Jeśli włączyć hasła zresetować dla grupy, a następnie zdecydować, aby ją włączyć dla wszystkich moich użytkowników wymagane ponownie zarejestrować?**

  > **Odp.:** Nie. Użytkownicy, którzy wypełnili dane uwierzytelniania nie trzeba ponownie zarejestrować.
  >
  >
* **Pyt.:  Czy można zdefiniować dane resetowania haseł w imieniu użytkowników?**

  > **Odp.:** Tak, możesz to zrobić za pomocą usługi Azure AD Connect, programu PowerShell, [witryny Azure portal](https://portal.azure.com), lub [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com). Aby uzyskać więcej informacji, zobacz [dane używane przez haseł usługi Azure AD resetowania](howto-sspr-authenticationdata.md).
  >
  >
* **Pyt.:  Można synchronizować dane odpowiedzi na pytania zabezpieczające ze środowiska lokalnego?**

  > **Odp.:** Nie, nie jest to możliwe już dziś.
  >
  >
* **Pyt.:  Moi użytkownicy można zarejestrować danych w taki sposób, że inni użytkownicy nie widzą te dane?**

  > **Odp.:** Tak. Kiedy użytkownik rejestruje portal rejestracji resetowania danych przy użyciu hasła, dane są zapisywane do pól prywatnych uwierzytelniania, które są widoczne tylko dla administratorów globalnych i użytkownika.
  >
  >
* **Pyt.:  Moi użytkownicy mają zarejestrowane, zanim rozpoczną korzystanie z resetowania haseł?**

  > **Odp.:** Nie. Jeśli zdefiniujesz wystarczających informacji do uwierzytelniania w ich imieniu, użytkownicy nie musieli zarejestrować. Działania resetowania hasła, tak długo, jak poprawnie sformatowanym danych przechowywanych w odpowiednich polach w katalogu.
  >
  >
* **Pyt.:  Można synchronizować lub ustaw pola Telefon uwierzytelniania alternatywny numer telefonu uwierzytelniania, adres e-mail uwierzytelniania lub w imieniu użytkowników?**

  > **Odp.:** Pola, które mogą być ustawione przez administratora globalnego są zdefiniowane w artykule [wymagania dotyczące funkcji samoobsługowego resetowania HASEŁ danych](howto-sspr-authenticationdata.md).
  >
  >
* **Pyt.:  Jak portal rejestracji określić które opcje, aby pokazać Moi użytkownicy?**

  > **Odp.:** Portal rejestracji resetowania haseł wyświetlane są tylko opcje czy włączono dla użytkowników. Te opcje można znaleźć w obszarze **zasady resetowania hasła użytkownika** sekcji swojego katalogu **Konfiguruj** kartę. Na przykład jeśli nie włączysz pytań zabezpieczających, następnie użytkownicy nie będą mogli zarejestrować dla tej opcji.
  >
  >
* **Pyt.:  Gdy użytkownik uznaje się zarejestrować?**

  > **Odp.:** Użytkownik jest traktowany jako zarejestrowany do samoobsługowego resetowania HASEŁ, gdy zarejestrowanych co najmniej **liczba metod wymaganych do zresetowania** hasła, które zostało ustawione w [witryny Azure portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetowanie hasła

* **Pyt.:  Czy można uniemożliwić użytkownikom wielokrotne próby zresetowania hasła w krótkim czasie?**

  > **Odp.:** Tak, są funkcje zabezpieczeń wbudowane w resetowania hasła, aby chronić je przed niewłaściwym użyciem również. 
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
* **Pyt.:  Jak długo należy czekać do odbierania wiadomości e-mail, SMS lub połączenie telefoniczne z resetowania haseł?**

  > **Odp.:** Wiadomości e-mail, wiadomości SMS i połączeń telefonicznych powinny przybycie w niż minutę. Normalna sytuacja jest 5-20 sekund.
  > Jeśli nie otrzymasz powiadomienie, w tym horyzoncie czasowym:
  > * Sprawdź folder wiadomości-śmieci.
  > * Sprawdź, czy numer lub adres e-mail, podejmowana jest jeden, których oczekujesz.
  > * Upewnij się, że dane uwierzytelniania w katalogu jest poprawnie sformatowana, na przykład 4255551234 + 1 lub *użytkownika\@contoso.com*. 
* **Pyt.:  Jakie języki są obsługiwane przez resetowania haseł?**

  > **Odp.:** Resetowania hasła użytkownika wiadomości SMS i połączeń głosowych są zlokalizowane w te same języki, które są obsługiwane w usłudze Office 365.
  >
  >
* **Pyt.:  Jakie części środowiska resetowania hasła Pobierz marki, gdy ustawić organizacyjne znakowania elementy w moim katalogu użytkownika Skonfiguruj kartę?**

  > **Odp.:** Portal resetowania haseł pokazuje logo organizacji i pozwala na skonfigurowanie link "Skontaktuj się z administratorem", aby wskazywać niestandardowy adres e-mail lub adres URL. Każdą wiadomość e-mail wysyłana przez resetowania haseł obejmuje logo organizacji, kolory i nazwy w treści wiadomości e-mail i dostosowanych ustawień dla tej określonej nazwy.
  >
  >
* **Pyt.:  Jak można Poinformuj użytkowników o tym, gdzie przejść do resetowania swoich haseł?**

  > **Odp.:** Wypróbuj sugestii w naszym [wdrożenie funkcji samoobsługowego resetowania HASEŁ](howto-sspr-deployment.md#sample-communication) artykułu.
  >
  >
* **Pyt.:  Czy można używać tej strony z urządzenia przenośnego?**

  > **Odp.:** Tak, ta strona działa na urządzeniach przenośnych.
  >
  >
* **Pyt.:  Odblokowywanie konta lokalne usługi Active Directory są obsługiwane podczas użytkownikom Resetowanie swoich haseł?**

  > **Odp.:** Tak. Po użytkownik resetuje hasła, jeśli został wdrożony funkcja zapisywania zwrotnego haseł przy użyciu usługi Azure AD Connect, konto tego użytkownika zostanie automatycznie odblokowana, gdy ich zresetują swoje hasło.
  >
  >
* **Pyt.:  Jak zintegrować resetowania bezpośrednio do mojego użytkownika logowania środowisko pulpitu**

  > **Odp.:** Jeśli jesteś klientem usługi Azure AD Premium, można zainstalować programu Microsoft Identity Manager bez ponoszenia dodatkowych kosztów i wdrażać rozwiązania resetowania hasła lokalnego.
  >
  >
* **Pyt.:  Można ustawić pytania zabezpieczające różne dla różnych ustawień regionalnych?**

  > **Odp.:** Nie, nie jest to możliwe już dziś.
  >
  >
* **Pyt.:  Jak najwięcej pytań do odpowiedzi można skonfigurować opcję uwierzytelniania pytania zabezpieczeń?**

  > **Odp.:** Można skonfigurować maksymalnie 20 niestandardowe pytania zabezpieczające w [witryny Azure portal](https://portal.azure.com).
  >
  >
* **Pyt.:  Jak długo może być pytania zabezpieczające?**

  > **Odp.:** Pytania zabezpieczeń może być 3 do 200 znaków.
  >
  >
* **Pyt.:  Jak długo może być odpowiedzi na pytania zabezpieczające?**

  > **Odp.:** Odpowiedzi mogą być 3 do 40 znaków.
  >
  >
* **Pyt.:  Zduplikowane odpowiedzi na pytania zabezpieczające odrzuconych wąskich**

  > **Odp.:** Tak, mamy odrzucić zduplikowane odpowiedzi na pytania zabezpieczające.
  >
  >
* **Pyt.:  Użytkownik zarejestrować na tym samym pytanie zabezpieczające więcej niż jeden raz?**

  > **Odp.:** Nie. Po użytkownik rejestruje określone pytanie, ich nie można zarejestrować na to pytanie po raz drugi.
  >
  >
* **Pyt.:  Czy jest możliwe, aby ustawić minimalny limit pytania zabezpieczające dla rejestracji i resetowania**

  > **Odp.:** Tak, można ustawić limitu dla rejestracji i inny wpis dla resetowania. Trzech do pięciu pytań zabezpieczających, może być wymagane do rejestracji, a trzech do pięciu pytań, może być wymagane do resetowania.
  >
  >
* **Pyt.:  Po skonfigurowaniu Moje zasad, aby wymagać od użytkowników użyj pytań zabezpieczających w celu zresetowania, ale administratorów platformy Azure wydaje się, że można skonfigurować inaczej.**

  > **Odp.:** Jest to oczekiwane zachowanie. Firma Microsoft wymusza silną domyślną zasadę resetowania hasła z użyciem dwóch bram dla dowolnej roli administratora platformy Azure. Zapobiega to za pomocą pytań zabezpieczających przez administratorów. Można znaleźć więcej informacji na temat tych zasad w [zasady i ograniczenia w usłudze Azure Active Directory haseł](concept-sspr-policy.md) artykułu.
  >
  >
* **Pyt.:  Jeśli użytkownik został zarejestrowany w więcej niż maksymalna liczba pytań wymaganych do zresetowania, jak są pytania zabezpieczające wybranych podczas resetowania?**

  > **Odp.:** *N* liczbę pytań zabezpieczających jest wybranych losowo poza całkowita liczba pytań, które użytkownik zarejestrował w którym *N* jest ustawiona dla **liczba pytań wymaganych do zresetowania** opcji. Na przykład jeśli użytkownik został zarejestrowany pięć pytań zabezpieczających, ale tylko trzy są wymagane, aby zresetować hasło, trzy pięć pytań są wybierane losowo i są prezentowane w resetowania. Aby zapobiec pytanie atakowaniu, jeśli użytkownik uzyska odpowiedzi na pytania niewłaściwego procesu wyboru uruchamiany ponownie.
  >
  >
* **Pyt.:  Jak długo są poczty e-mail i SMS jednorazowe kodów dostępu prawidłowy?**

  > **Odp.:** Okres istnienia sesji do resetowania hasła jest 15 minut. Od czasu rozpoczęcia operacji resetowania hasła użytkownik musi 15 minut do zresetowania swojego hasła. Po upływie tego czasu do czasu poczty e-mail i SMS jednorazowy kod dostępu są nieprawidłowe.
  >
  >
* **Pyt.:  Czy można zablokować użytkownikom możliwość resetowania hasła?**

  > **Odp.:** Tak, w przypadku używania grupy można włączyć funkcji samoobsługowego resetowania HASEŁ, możesz usunąć użytkownika z grupy, która umożliwia użytkownikom zresetowanie hasła. Jeśli użytkownik jest administratorem globalnym one zachowają możliwość zresetowania hasła, a nie może być wyłączony.
  >
  >

## <a name="password-change"></a>Zmiana hasła

* **Pyt.:  Gdzie Moi użytkownicy należy zmieniać swoje hasła?**

  > **Odp.:** Użytkownicy mogą zmieniać swoich haseł w dowolnym miejscu zobaczy ich zdjęcie profilowe lub ikonę, np. w prawym górnym rogu ich [usługi Office 365](https://portal.office.com) portalu lub [panelu dostępu](https://myapps.microsoft.com) napotyka. Użytkownicy mogą zmieniać swoje hasła niezależnie od [profilu panelu dostępu do strony](https://account.activedirectory.windowsazure.com/r#/profile). Użytkownicy mogą się prośba o zmiany haseł automatycznie strony logowania usługi Azure AD, jeśli ich hasła wygasły. Ponadto użytkownicy mogą przeglądać do [portal zmieniania haseł usługi Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) bezpośrednio zmienić swoje hasła.
  >
  >
* **Pyt.:  Moi użytkownicy może zostać poinformowany w portalu usługi Office, po wygaśnięciu hasła w środowisku lokalnym?**

  > **Odp.:** Tak, jest to możliwe już dziś, jeśli korzystasz z usługi Active Directory Federation Services (AD FS). Jeśli używasz usług AD FS, postępuj zgodnie z instrukcjami [wysyłania oświadczenia zasad haseł za pomocą usług AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artykułu. Jeśli używasz synchronizacji skrótów haseł, nie jest to możliwe już dziś. Firma Microsoft nie są synchronizowane zasady haseł z katalogami lokalnymi, więc nie jest możliwe w firmie Microsoft w celu wpis powiadomień o wygasaniu do środowiska w chmurze. W obu przypadkach istnieje również możliwość [powiadomić użytkowników, których hasła są wygaśnie za pomocą programu PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **Pyt.:  Czy można zablokować użytkownikom możliwość zmieniania swojego hasła?**

  > **Odp.:** Dla użytkowników tylko w chmurze nie można zablokować zmiany hasła. Dla użytkowników w środowisku lokalnym, możesz ustawić **użytkownik nie może zmienić hasła** opcję, aby wybrane. Wybrani użytkownicy nie można zmienić swoje hasło.
  >
  >

## <a name="password-management-reports"></a>Raporty dotyczące zarządzania hasłami

* **Pyt.:  Jak długo trwa dane wyświetlane na raporty dotyczące zarządzania hasłami?**

  > **Odp.:** Dane powinny się pojawić na raporty dotyczące zarządzania hasłami w ciągu 5 – 10 minut. W niektórych przypadkach może potrwać do godziny się pojawić.
  >
  >
* **Pyt.:  Jak filtrować raporty dotyczące zarządzania hasłami**

  > **Odp.:** Aby filtrować raporty dotyczące zarządzania hasłami, wybierz mały szkła powiększającego się najdalej po prawej stronie etykiety kolumn, w górnej części raportu. Jeśli chcesz bardziej zaawansowane filtrowanie, możesz pobrać raport do programu Excel i Utwórz tabelę przestawną.
  >
  >
* **Pyt.: Co to jest maksymalną liczbę zdarzeń, które są przechowywane w raporty dotyczące zarządzania hasłami?**

  > **Odp.:** Maksymalnie 75,000 Resetowanie hasła lub hasło zdarzenia rejestracji resetowania są przechowywane w raporty dotyczące zarządzania hasłami, obejmujące Wstecz, o ile to 30 dni. Pracujemy, aby rozwinąć tę liczbę zarejestrować więcej zdarzeń.
  >
  >
* **Pyt.:  Jak daleko Przejdź raporty dotyczące zarządzania hasłami?**

  > **Odp.:** Raporty dotyczące zarządzania hasłami Pokaż operacje, które wystąpiły w ciągu ostatnich 30 dni. Teraz Jeśli chcesz zarchiwizować te dane można można pobrać raportów okresowo i zapisywać je w innej lokalizacji.
  >
  >
* **Pyt.:  Czy istnieje maksymalna liczba wierszy, które mogą być wyświetlane w raportach zarządzania hasło?**

  > **Odp.:** Tak. Maksymalnie 75,000 wierszy mogą występować w dowolnym raporty dotyczące zarządzania hasłami, czy są wyświetlane w interfejsie użytkownika, czy zostały pobrane.
  >
  >
* **Pyt.:  Czy istnieje interfejs API umożliwiający dostęp do resetowania hasła lub rejestracji dane raportowania?**

  > **Odp.:** Tak. Aby dowiedzieć się, jak można pobrać resetowania haseł, strumień danych raportowania, zobacz [Dowiedz się, jak dostęp do resetowania hasła zdarzeń do raportowania programowo](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Zapisywanie zwrotne haseł

* **Pyt.:  Jak działa funkcja zapisywania zwrotnego haseł w tle**

  > **Odp.:** Zapoznaj się z artykułem [sposobu działania funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md) wyjaśnienie co się stanie po włączeniu funkcji zapisywania zwrotnego haseł i sposób przepływu danych przez system z powrotem do środowiska lokalnego.
  >
  >
* **Pyt.:  Jak funkcja zapisywania zwrotnego haseł długo pracę? Jest tam synchronizacji opóźnienie tak jak ma to za pomocą synchronizacji skrótów haseł?**

  > **Odp.:** Zapisywanie zwrotne haseł jest natychmiastowe. Jest synchroniczne potok, który działa w sposób całkowicie inny niż synchronizacji skrótów haseł. Zapisywanie zwrotne haseł umożliwia użytkownikom do pobrania w czasie rzeczywistym informacje o udanym resetowania swoich haseł lub operacji zmiany. Średni czas na pomyślne funkcji zapisywania zwrotnego haseł jest poniżej 500 ms.
  >
  >
* **Pyt.:  Jeśli Moje konto lokalne jest wyłączone, w jaki sposób Mój konta w chmurze i dostępu, których dotyczy problem?**

  > **Odp.:** Jeśli Twój identyfikator lokalnych jest wyłączone, swój identyfikator chmury i dostęp również zostaną wyłączone w następnym interwale synchronizacji za pomocą usługi Azure AD Connect. Domyślnie ta synchronizacja jest co 30 minut.
  >
  >
* **Pyt.:  Jeśli Moje konto lokalne jest ograniczony przez w lokalnych zasadach haseł usługi Active Directory, samoobsługowego resetowania HASEŁ przestrzegają tych zasad podczas zmiany hasła?**

  > **Odp.:** Tak, opiera się na funkcji samoobsługowego resetowania HASEŁ i przestrzega zasad haseł usługi Active Directory w środowisku lokalnym. Te zasady zawierają typowe zasady haseł domeny usługi Active Directory, a także żadnych zasad zdefiniowanych, precyzyjny hasła, które są stosowane do użytkownika.
  >
  >
* **Pyt.:  Jakie rodzaje kont zwrotne haseł działa w przypadku?**

  > **Odp.:** Zapisywanie zwrotne haseł działa dla kont użytkowników, które są synchronizowane z usługi Active Directory w środowisku lokalnym, do usługi Azure AD, w tym federacyjnych, użytkownicy Autentication zsynchronizowane i przekazywania wyznaczania wartości skrótu hasła.
  >
  >
* **Pyt.:  Zapisywanie zwrotne haseł wymusza zasady haseł w mojej domeny?**

  > **Odp.:** Tak. Zapisywanie zwrotne haseł wymusza okres ważności hasła, historii, złożoność, filtry i inne ograniczenia, które można umieścić w miejscu na hasłach, w domenie lokalnej.
  >
  >
* **Pyt.:  Zapisywanie zwrotne haseł jest bezpieczny?  Jak mogę mieć pewność, że nie mogę włamania?**

  > **Odp.:** Tak, zapisywanie zwrotne haseł jest bezpieczne. Aby dowiedzieć się więcej na temat wielu wbudowanym warstwom zabezpieczeń implementowany przez usługi zapisywania zwrotnego haseł, zapoznaj się z [zabezpieczeń funkcji zapisywania zwrotnego haseł](concept-sspr-writeback.md#password-writeback-security) sekcji [Przegląd funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md) artykułu.
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
