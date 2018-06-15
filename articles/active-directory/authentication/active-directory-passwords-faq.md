---
title: Samoobsługowego resetowania hasła często zadawane pytania — Azure Active Directory
description: Często zadawane pytania dotyczące usługi Azure AD samoobsługi hasła resetowania
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 7f54877f9218cbb0c2ac8cca86c580b7f1aa6c83
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869754"
---
# <a name="password-management-frequently-asked-questions"></a>Często zadawane pytania dotyczące zarządzania hasłami

Poniżej przedstawiono niektóre często zadawane pytania (FAQ) dla wszystkich czynności związane z resetowania hasła.

Jeśli masz pytania ogólne dotyczące usługi Azure Active Directory (Azure AD) i samoobsługowego resetowania hasła (SSPR), który nie jest tutaj odpowiedzi, poproś społeczność pomocy na [forum usługi Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Członkowie społeczności obejmują engineers, menedżerów produktu MVP i innymi specjalistów IT.

Często zadawane pytania jest podzielony na następujące sekcje:

* [Pytania dotyczące hasła rejestracji resetowania](#password-reset-registration)
* [Pytania dotyczące resetowania hasła](#password-reset)
* [Pytania dotyczące zmiany hasła](#password-change)
* [Pytania dotyczące raportów zarządzania hasła](#password-management-reports)
* [Pytania dotyczące funkcji zapisywania zwrotnego haseł](#password-writeback)

## <a name="password-reset-registration"></a>Rejestracja w resetowaniu haseł

* **Pytanie: czy Moi użytkownicy rejestrowanie własnych danych resetowania hasła?**

  > **Odpowiedź:** tak. Tak długo, jak resetowania hasła jest włączona i są one licencjonowane, użytkownicy mogą przejść do portalu rejestracji resetowania haseł (https://aka.ms/ssprsetup) zarejestrować swoje informacje o uwierzytelnianiu. Użytkownicy mogą rejestrować się również za pomocą panelu dostępu (http://myapps.microsoft.com). Można zarejestrować za pomocą panelu dostępu, muszą wybrać obraz ich profilu, wybierz **profilu**, a następnie wybierz **zarejestrować do resetowania hasła** opcji.
  >
  >
* **Pytanie: czy włączyć hasła zresetowanie grupy i zdecydować włączyć ją dla wszystkich są moje użytkowników wymagane ponownie zarejestrować?**

  > **Odpowiedź:** Nie. Użytkownicy, którzy mają wypełnione dane uwierzytelniania nie jest konieczne ponowne zarejestrowanie.
  >
  >
* **Q: czy można zdefiniować dane resetowania hasła w imieniu użytkowników?**

  > **Odpowiedź:** tak, możesz to zrobić z programem Azure AD Connect, programu PowerShell, [portalu Azure](https://portal.azure.com), lub Centrum administracyjne usługi Office 365. Aby uzyskać więcej informacji, zobacz [zresetować dane używane przez usługi Azure AD samoobsługi hasła](howto-sspr-authenticationdata.md).
  >
  >
* **Pytanie: czy można synchronizować dane odpowiedzi na pytania zabezpieczające z lokalnej?**

  > **Odpowiedź:** nie, nie jest to możliwe dzisiaj.
  >
  >
* **Pytanie: czy Moi użytkownicy zarejestrować danych w taki sposób, że inni użytkownicy nie widzą te dane?**

  > **Odpowiedź:** tak. Po rejestracji użytkownicy portalu rejestracji resetowania danych przy użyciu hasła, dane są zapisywane do pól prywatnych uwierzytelniania, które są widoczne tylko dla administratorów globalnych i użytkownika.
  >
  >
* **Pytanie: czy Moi użytkownicy muszą być zarejestrowane, aby móc korzystać z resetowania hasła?**

  > **Odpowiedź:** Nie. Za mało informacji uwierzytelniania w przypadku definiowania w ich imieniu, użytkownicy nie mają do zarejestrowania. Działania resetowania hasła, tak długo, jak długo ma prawidłowo sformatowane dane przechowywane w odpowiednich polach w katalogu.
  >
  >
* **Pytanie: można zsynchronizować lub ustaw pola telefonu uwierzytelniania alternatywny numer telefonu uwierzytelniania, uwierzytelniania wiadomości e-mail lub w imieniu użytkowników?**

  > **Odpowiedź:** pola, które mogą być ustawione przez administratora globalnego są zdefiniowane w artykule [wymagania dotyczące danych SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **Pytanie: jak portal rejestracji ustalić jakie opcje Pokaż moich użytkowników?**

  > **Odpowiedź:** pokazuje portalu rejestracji resetowania haseł, opcje, które zostały włączone dla użytkowników. Te opcje są dostępne w **zasady resetowania hasła użytkownika** Twoim katalogu **Konfiguruj** kartę. Na przykład jeśli nie włączysz pytań zabezpieczających, następnie użytkownicy nie są mogą rejestrować się w tej opcji.
  >
  >
* **Pytanie: Jeśli użytkownik jest uznawany zarejestrowane?**

  > **A:** użytkownik jest uznawany w zarejestrowany dla usługi SSPR, gdy zarejestrowanych co najmniej **wiele metod wymaganych do zresetowania** hasła, która została zdefiniowana w [portalu Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetowanie hasła

* **Pytanie: czy można uniemożliwić użytkownikom Resetowanie hasła w krótkim czasie wielu prób?**

  > **Odpowiedź:** tak, są wbudowane w resetowania hasła, aby chronić przed niewłaściwym użyciem funkcje zabezpieczeń. 
  >
  > Użytkownicy, można spróbować tylko pięć prób resetowania hasła w ciągu 24 godzin jest zablokowane przez 24 godziny. 
  >
  > Użytkownicy mogą próbować sprawdza, czy numer telefonu, Wyślij wiadomość SMS lub zweryfikować pytań zabezpieczających i odpowiedzi tylko pięć razy w ciągu godziny jest zablokowane przez 24 godziny. 
  >
  > Użytkownicy mogą wysyłać wiadomości e-mail maksymalnie 10 razy w ciągu 10 minut przed jest zablokowane przez 24 godziny.
  >
  > Liczniki są resetowane po użytkownika spowoduje zresetowanie hasła.
  >
  >
* **Pytanie: jak długo należy czekać do odbierania wiadomości e-mail, SMS lub połączeń telefonicznych z resetowania hasła?**

  > **Odpowiedź:** wiadomości E-mail, wiadomości SMS i połączeń telefonicznych powinno nastąpić w obszarze minutę. Normalna sytuacja wynosi 5-20 sekund.
    >Jeśli użytkownik nie otrzyma powiadomienie w tym czasie:
        > * Sprawdź folder wiadomości-śmieci.
        > * Sprawdź, czy numer lub adres e-mail jest nawiązywane połączenie, którego można spodziewać się.
        > * Sprawdź, czy dane uwierzytelniania w katalogu jest poprawnie sformatowana, na przykład 4255551234 + 1 lub *user@contoso.com*. 
  >
  >
* **Pytanie: jakie języki są obsługiwane przez resetowania hasła?**

  > **Odpowiedź:** resetowania hasła użytkownika wiadomości SMS i połączeń głosowych są zlokalizowane w te same języki, które są obsługiwane w usłudze Office 365.
  >
  >
* **Karta na konfigurowanie Q: części środowisko resetowania hasła uzyskać marki, gdy ustawić organizacji znakowania elementów w katalogu Moje?**

  > **Odpowiedź:** portal resetowania haseł pokazuje logo organizacji i pozwala skonfigurować łącze "Skontaktuj się z administratorem", aby wskazywał niestandardowe wiadomości e-mail lub adres URL. Każdej wiadomości e-mail wysyłanej przez resetowania haseł obejmuje logo organizacji, kolory i nazwę w treści wiadomości e-mail i jest dostosowane ustawienia dla tej określonej nazwy.
  >
  >
* **Pytanie: jak można Poinformuj użytkowników o tym, gdzie można przejść na resetowanie haseł?**

  > **A:** wypróbować niektóre sugestii w naszym [wdrożenia SSPR](howto-sspr-deployment.md#email-based-rollout) artykułu.
  >
  >
* **Pytanie: czy można użyć tej strony z urządzenia przenośnego?**

  > **Odpowiedź:** tak, ta strona działa na urządzeniach przenośnych.
  >
  >
* **Pyt.: czy odblokowywaniem lokalnych kont usługi Active Directory są obsługiwane podczas użytkownikom resetowania swoich haseł w?**

  > **Odpowiedź:** tak. Gdy użytkownik resetuje hasła, jeśli wdrożono funkcję zapisywania zwrotnego haseł za pośrednictwem usługi Azure AD Connect, konto użytkownika jest odblokowany automatycznie podczas resetowania hasła.
  >
  >
* **Pytanie: jak można zintegrować resetowania bezpośrednio do mojego użytkownika logowania środowisko pulpitu**

  > **Odpowiedź:** w przypadku klienta programu Azure AD Premium, można zainstalować programu Microsoft Identity Manager bez ponoszenia dodatkowych kosztów i wdrożenia lokalnego rozwiązania resetowania hasła.
  >
  >
* **Pytanie: czy można ustawić pytania zabezpieczeń dla różnych ustawień regionalnych?**

  > **Odpowiedź:** nie, nie jest to możliwe dzisiaj.
  >
  >
* **Pytanie: jak wiele pytań można skonfigurować dla opcji uwierzytelniania pytania zabezpieczeń?**

  > **Odpowiedź:** można skonfigurować maksymalnie 20 pytania zabezpieczające niestandardowych w [portalu Azure](https://portal.azure.com).
  >
  >
* **Pytanie: jak długo może być pytania zabezpieczające?**

  > **Odpowiedź:** pytania zabezpieczeń może zawierać 3 do 200 znaków.
  >
  >
* **Pytanie: jak długo może być odpowiedzi na pytania zabezpieczające?**

  > **Odpowiedź:** odpowiedzi może zawierać 3 do 40 znaków.
  >
  >
* **Pytanie: czy zduplikowane odpowiedzi na pytania zabezpieczające odrzucone?**

  > **Odpowiedź:** tak, możemy Odrzuć zduplikowane odpowiedzi na pytania zabezpieczające.
  >
  >
* **Pytanie: czy użytkownik zarejestrować na tym samym pytanie zabezpieczające więcej niż raz?**

  > **Odpowiedź:** Nie. Po użytkownik rejestruje dane pytanie, ich nie można zarejestrować dla tego zapytania po raz drugi.
  >
  >
* **Pytanie: czy jest możliwe ustawić minimalny limit pytania zabezpieczające dla rejestracji i zresetować?**

  > **Odpowiedź:** tak, można ustawić limit jeden dla rejestracji i drugi dla resetowania. Pytania zabezpieczające trzech do pięciu może być wymagany w czasie rejestracji, pytania i trzech do pięciu może być wymagana dla resetowania.
  >
  >
* **Pytanie: po skonfigurowaniu Moje zasady, aby wymagać od użytkowników użyj pytań zabezpieczających do resetowania, ale administratorzy Azure wydają się inaczej skonfigurowana.**

  > **Odpowiedź:** jest to oczekiwane zachowanie. Microsoft wymusza silne domyślnych zasad resetowania hasła bramy dwa dla każdej roli administratora platformy Azure. Zapobiega to za pomocą pytań zabezpieczeń Administratorzy. Można znaleźć więcej informacji na temat tych zasad w [ograniczenia w usłudze Azure Active Directory i zasadami haseł](concept-sspr-policy.md#administrator-password-policy-differences) artykułu.
  >
  >
* **Pytanie: Jeśli użytkownik został zarejestrowany w więcej niż maksymalna liczba pytań wymaganych do zresetowania, jak są pytania zabezpieczające wybrane podczas resetowania?**

  > **Odpowiedź:** *N* liczbę pytań zabezpieczających są losowo wybrane poza łączna liczba pytań, użytkownik jest zarejestrowany dla miejsca *N* jest wartość ustawioną dla **numer pytań wymaganych do zresetowania** opcji. Na przykład jeśli użytkownik został zarejestrowany pięć pytań zabezpieczeń, ale tylko trzy są wymagane, aby zresetować hasło, trzy z pięciu pytania są losowo dobierane i są prezentowane w resetowania. Aby zapobiec pytanie atakowaniu, jeśli użytkownik pobiera odpowiedzi na pytania niewłaściwy procesu wyboru rozpoczyna się za pośrednictwem.
  >
  >
* **Pytanie: jak długo są poczty e-mail i SMS jednorazowe kody dostępu prawidłowy?**

  > **Odpowiedź:** okres istnienia sesji do resetowania hasła jest 15 minut. Od początku operacji resetowania hasła użytkownika ma 15 minut do zresetowania swojego hasła. Po tym okresie poczty e-mail i SMS jednorazowy kod dostępu są nieprawidłowe.
  >
  >
* **Pytanie: czy można zablokować użytkownikom zresetowania hasła?**

  > **Odpowiedź:** tak, jeśli używasz grupy w celu włączenia funkcji SSPR, można usunąć użytkownika z grupy, który umożliwia użytkownikom resetowania hasła. Jeśli użytkownik jest administratorem globalnym będzie zachowują możliwość resetowania hasła i nie można wyłączyć.
  >
  >

## <a name="password-change"></a>Zmiana hasła

* **Pytanie: gdzie Moi użytkownicy powinien przejść do zmiany haseł?**

  > **A:** użytkownicy mogą zmieniać swoje hasła dowolnym zobaczy ich obraz profilu lub ikony, jak w prawym górnym rogu ich [usługi Office 365](https://portal.office.com) portalu lub [panelu dostępu](https://myapps.microsoft.com) wykonywania. Użytkownicy mogą zmieniać swoje hasła z [profilu panelu dostępu do strony](https://account.activedirectory.windowsazure.com/r#/profile). Użytkownicy mogą również poproszony o zmiany haseł automatycznie na stronę logowania w usłudze Azure AD, jeśli ich hasła wygasły. Ponadto użytkownicy mogą przeglądać do [portalu zmiany hasła usługi Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) bezpośrednio Jeśli chcą, aby zmienić swoje hasła.
  >
  >
* **Pytanie: czy Moi użytkownicy powiadamianych w portalu usługi Office po wygaśnięciu hasła lokalnego?**

  > **Odpowiedź:** tak, jest to możliwe dzisiaj korzystania z usługi Active Directory Federation Services (AD FS). Jeśli używasz usług AD FS, postępuj zgodnie z instrukcjami [wysyłanie oświadczeń zasad haseł z usługami AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artykułu. Jeśli używasz synchronizacji skrótu hasła nie jest to możliwe dzisiaj. Firma Microsoft nie są synchronizowane zasady haseł z katalogów lokalnych, nie jest możliwe w firmie Microsoft w celu publikowania powiadomień o wygasaniu do środowiska chmury. W obu przypadkach istnieje również możliwość [Powiadom użytkowników, których hasła zbliża się termin wygaśnięcia za pomocą programu PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **Pytanie: czy można zablokować użytkownikom zmianę ich haseł?**

  > **Odpowiedź:** dla użytkowników tylko w chmurze, nie mogą być blokowane zmiany hasła. Dla użytkowników lokalnych, można ustawić **użytkownik nie może zmienić hasła** opcję, aby wybrać. Wybrani użytkownicy nie można zmienić swoje hasło.
  >
  >

## <a name="password-management-reports"></a>Raporty zarządzania hasła

* **Pytanie: jak długo trwa danych wyświetlani w raportach zarządzania hasło?**

  > **Odpowiedź:** danych powinny być wyświetlane w raportach zarządzania hasło w 5 – 10 minut. W niektórych przypadkach go może potrwać do godziny są wyświetlane.
  >
  >
* **Pytanie: jak można filtrować raporty zarządzania hasło?**

  > **Odpowiedź:** do filtrowania raportów zarządzania hasło, wybierz małych Lupa extreme po prawej stronie etykiety kolumn, w górnej części raportu. Jeśli chcesz bardziej rozbudowane filtrowanie, można pobrać raportu do programu Excel i utworzyć tabelę przestawną.
  >
  >
* **Pytanie: jaka jest maksymalna liczba zdarzeń, które są przechowywane w raportach dotyczących zarządzania hasła?**

  > **Odpowiedź:** do hasła 75,000 zdarzenia rejestracji resetowania resetowania lub hasło są przechowywane w hasło raporty zarządzania, obejmujących Wstecz, o ile to 30 dni. Pracujemy nad Rozwiń ten numer, aby zarejestrować więcej zdarzeń.
  >
  >
* **Pytanie: jak daleko raporty zarządzania hasło go?**

  > **Odpowiedź:** zarządzania hasłami raporty operacji show, które wystąpiły w ciągu ostatnich 30 dni. Teraz Jeśli chcesz zarchiwizować te dane, musisz można pobrać raporty okresowo i zapisać je w innej lokalizacji.
  >
  >
* **Pytanie: istnieje już maksymalną liczbę wierszy, które mogą być wyświetlane w raportach zarządzania hasło?**

  > **Odpowiedź:** tak. Maksymalnie 75,000 wierszy może występować w zależności od raporty zarządzania hasło, czy są wyświetlane w interfejsie użytkownika lub zostaną pobrane.
  >
  >
* **Pytanie: jest interfejs API dostępu do resetowania hasła lub rejestracji dane raportowania?**

  > **Odpowiedź:** tak. Aby dowiedzieć się, jak można pobrać resetowania hasła, strumień danych raportowania, zobacz [Dowiedz się, jak uzyskać dostępu do zdarzeń do raportowania programowane Resetowanie hasła](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Zapisywanie zwrotne haseł

* **Pytanie: jak działa funkcji zapisywania zwrotnego haseł w tle?**

  > **Odpowiedź:** zapoznaj się z artykułem [sposób działania funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md) wyjaśnienie co się stanie po włączeniu funkcji zapisywania zwrotnego haseł i sposób przepływu danych za pośrednictwem systemu ponownie w środowisku lokalnym.
  >
  >
* **Pytanie: jak długo funkcji zapisywania zwrotnego haseł podąża pracę? Jest brak synchronizacji opóźnienie tak jak w przypadku synchronizacji skrótów haseł?**

  > **Odpowiedź:** błyskawicznych jest funkcji zapisywania zwrotnego haseł. Jest synchroniczne potok, który działa zasadniczo inaczej niż synchronizacji skrótu hasła. Zapisywanie zwrotne haseł umożliwia użytkownikom Uzyskaj opinie w czasie rzeczywistym o udanym ich resetowania hasła lub zmienić operację. Średni czas na pomyślne zapisywania zwrotnego hasła jest w obszarze 500 ms.
  >
  >
* **Pytanie: wyłączenie konta lokalnego, jak jest mojego konta w chmurze i dostępu, których dotyczy problem?**

  > **Odpowiedź:** wyłączenie Identyfikatora lokalnego dostępu i identyfikator chmury również zostaną wyłączone w następnym interwale synchronizacji za pośrednictwem usługi Azure AD Connect. Domyślnie tej synchronizacji jest co 30 minut.
  >
  >
* **Pytanie: czy Moje konto lokalne jest ograniczane przez zasady haseł lokalnej usługi Active Directory, SSPR przestrzegać tych zasad po zmianie hasła?**

  > **Odpowiedź:** tak, zależy od usługi SSPR i przestrzega zasad haseł lokalnej usługi Active Directory. Ta zasada zawiera typowe zasady haseł domeny usługi Active Directory, a także żadnych zasad zdefiniowanych, szczegółowych hasło, które są przeznaczone dla użytkownika.
  >
  >
* **Pytanie: jakie typy kont zapisywania zwrotnego haseł działa dla?**

  > **Odpowiedź:** działania funkcji zapisywania zwrotnego haseł dla kont użytkowników, które są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD, w tym federacyjnych, synchronizacji skrótów haseł i przekazywanego Autentication użytkowników.
  >
  >
* **Pytanie: czy funkcji zapisywania zwrotnego haseł wymusić zasady haseł mojej domeny?**

  > **Odpowiedź:** tak. Zapisywanie zwrotne haseł wymusza okres ważności hasła, historii złożoności, filtrów i inne ograniczenia, które może umieścić w miejscu na haseł w domenie lokalnej.
  >
  >
* **Pytanie: czy jest bezpieczna funkcji zapisywania zwrotnego haseł?  Jak można mieć pewność, że I nie będzie włamania?**

  > **Odpowiedź:** tak, zapisywanie zwrotne haseł jest bezpieczne. Aby uzyskać więcej informacji na temat cztery warstwy zabezpieczeń zaimplementowanych przez usługę zapisywania zwrotnego haseł, zapoznaj się [model zabezpieczeń zapisywania zwrotnego haseł](howto-sspr-writeback.md#password-writeback-security-model) sekcji [Przegląd funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md) artykułu.
  >
  >

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
