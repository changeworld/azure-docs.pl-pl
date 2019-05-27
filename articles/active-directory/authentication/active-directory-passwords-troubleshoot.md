---
title: Samoobsługowe resetowanie haseł Rozwiązywanie problemów — usługi Azure Active Directory
description: Rozwiązywania problemów z usługi Azure AD samoobsługowego resetowania haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89c76ad0739edea4c0541ace76ca2311453de71d
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963026"
---
# <a name="troubleshoot-self-service-password-reset"></a>Rozwiązywanie problemów z samoobsługowego resetowania haseł

Należy rozwiązać problem z usługi Azure Active Directory (Azure AD) samoobsługowego resetowania haseł (SSPR)? Następujące informacje mogą ułatwić wszystko ponownie.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Rozwiązywanie problemów z błędami resetowania haseł, które użytkownik może zobaczyć

| Błąd | Szczegóły | Szczegóły techniczne |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Niestety, nie możesz zresetować hasło w tej chwili, ponieważ administrator wyłączył resetowanie haseł dla Twojej organizacji. Nie istnieje żadne dalsze akcje, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o włączenie tej funkcji. Aby dowiedzieć się więcej, zobacz [pamiętam mojego hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Wykryliśmy, że resetowanie hasła nie został włączony przez administratora. Skontaktuj się z administratorem i poproś go o włączenie resetowania hasła dla Twojej organizacji. |
| WritebackNotEnabled = 10 |Niestety, nie możesz zresetować hasło w tej chwili, ponieważ administrator nie włączył wymaganej usługi dla Twojej organizacji. Nie istnieje żadne dalsze akcje, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o sprawdzenie konfiguracji organizacji. Aby dowiedzieć się więcej o tej wymaganej usłudze, zobacz [Konfigurowanie funkcji zapisywania zwrotnego haseł](howto-sspr-writeback.md). | SSPR_0010: Wykryliśmy, że tej funkcji zapisywania zwrotnego haseł nie zostało włączone. Skontaktuj się z administratorem i poproś go o włączenie funkcji zapisywania zwrotnego haseł. |
| SsprNotEnabledInUserPolicy = 11 | Niestety, nie możesz zresetować hasło w tej chwili, ponieważ administrator nie skonfigurował resetowania haseł dla Twojej organizacji. Nie istnieje żadne dalsze akcje, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o skonfigurowanie resetowania haseł. Aby dowiedzieć się więcej na temat hasło to zresetowanie konfiguracji, zobacz [— szybki start: Usługa Azure AD samoobsługowego resetowania haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Twoja organizacja nie zdefiniowała zasad resetowania haseł. Skontaktuj się z administratorem i poproś go o zdefiniowanie zasad resetowania haseł. |
| UserNotLicensed = 12 | Niestety, nie możesz zresetować hasło w tej chwili, ponieważ wymagane licencje są w Twojej organizacji. Nie istnieje żadne dalsze akcje, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o sprawdzenie przypisania licencji. Aby dowiedzieć się więcej na temat licencjonowania, zobacz [licencjonowania wymagania dotyczące hasła usługi Azure AD z samoobsługowego resetowania](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Twoja organizacja nie ma licencji wymaganych do wykonania resetowania hasła. Skontaktuj się z administratorem i poproś go o sprawdzenie przydziałów licencji. |
| UserNotMemberOfScopedAccessGroup = 13 | Niestety, nie możesz zresetować hasło w tej chwili, ponieważ administrator nie skonfigurował Twoje konto do użycia resetowania hasła. Nie istnieje żadne dalsze akcje, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o skonfigurowanie Twojego konta do resetowania hasła. Aby dowiedzieć się więcej na temat konfiguracji konta w celu zresetowania hasła, zobacz [wdrożyć resetowanie haseł dla użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Nie jesteś członkiem grupy uprawnień do resetowania hasła. Skontaktuj się z administratorem i poproś o dodanie do grupy. |
| UserNotProperlyConfigured = 14 | Niestety, nie możesz zresetować hasło w tej chwili, ponieważ brakuje niezbędne informacji z Twojego konta. Nie istnieje żadne dalsze akcje, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o zresetowania hasła. Po użytkownik ma dostęp do Twojego konta ponownie, musisz zarejestrować niezbędne informacje. Aby zarejestrować informacje, wykonaj kroki opisane w [rejestracji samoobsługowego resetowania haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) artykułu. | SSPR_0014: Do zresetowania hasła wymagane jest dodatkowe informacje zabezpieczające. Aby kontynuować, skontaktuj się z administratorem i poproś go, aby zresetować hasło. Po użytkownik ma dostęp do Twojego konta, możesz zarejestrować dodatkowe informacje zabezpieczające na https://aka.ms/ssprsetup. Administrator można dodać dodatkowe informacje zabezpieczające do Twojego konta, wykonując kroki opisane w [zestawu i dane odczytu uwierzytelniania do resetowania hasła](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Niestety, nie możemy zresetować Twojego hasła w tej chwili ze względu na problem z konfiguracją resetowania haseł w organizacji. Nie istnieje żadne dalsze akcje, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o zbadanie. Aby dowiedzieć się więcej o potencjalnym problemie, zobacz [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Nie możemy zresetować Twojego hasła ze względu na błąd w konfiguracji lokalnej. Skontaktuj się z administratorem i poproś go o zbadanie. |
| OnPremisesConnectivityError = 30 | Niestety, nie możemy zresetować Twojego hasła w tej chwili ze względu na problemy z łącznością z Twojej organizacji. Brak trzeba teraz podejmować żadnych działań, ale problem może być rozwiązany, jeśli ponowisz próbę później. Jeśli problem będzie się powtarzać, skontaktuj się z administratorem i poproś go o zbadanie. Aby dowiedzieć się więcej na temat problemów z łącznością, zobacz [Rozwiązywanie problemów z łącznością zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Nie możemy zresetować Twojego hasła ze względu na słabe połączenie ze środowiska lokalnego. Skontaktuj się z administratorem i poproś go o zbadanie.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Rozwiązywanie problemów z konfiguracji resetowania hasła w witrynie Azure portal

| Błąd | Rozwiązanie |
| --- | --- |
| Nie widzę **resetowania hasła** w sekcji usługi Azure AD w witrynie Azure portal. | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Przypisz licencję do konta administratora w danym. Możesz wykonać kroki opisane w [przypisania, sprawdź i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artykułu.|
| Nie widzę opcji określoną konfigurację. | Wiele elementów interfejsu użytkownika są ukryte, dopóki nie są potrzebne. Spróbuj, włączyć wszystkie opcje, które mają być wyświetlane. |
| Nie widzę **integracji lokalnego** kartę. | Tej opcji tylko staje się widoczny, jeśli pobrano program Azure AD Connect i skonfigurowano funkcję zapisywania zwrotnego haseł. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure AD Connect przy użyciu ustawień ekspresowych](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Rozwiązywanie problemów z raportowania resetowania hasła

| Błąd | Rozwiązanie |
| --- | --- |
| Nie widzę żadnych typów działań zarządzania hasło w **samoobsługowego zarządzania hasłami** kategorię zdarzeń inspekcji. | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Aby rozwiązać ten problem, należy przypisywania licencji do danego konta administratora. Postępuj zgodnie z instrukcjami w [przypisania, sprawdź i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artykułu. |
| Rejestracje użytkownika pokazują wiele razy. | Obecnie po użytkownik rejestruje, firma Microsoft Zaloguj się każdej z części danych, która jest zarejestrowana jako oddzielne zdarzenie. <br> <br> Jeśli chcesz, możesz zagregować te dane, w którym można mają większą elastyczność, w jaki sposób można je wyświetlić, możesz pobrać raport i otworzyć te dane jako tabelę przestawną w programie Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Rozwiązywanie problemów z portalem rejestracji resetowania haseł

| Błąd | Rozwiązanie |
| --- | --- |
| Katalog nie jest włączona w celu zresetowania hasła. **Administrator nie włączył pozwala korzystać z tej funkcji.** | Przełącznik **samoobsługowego resetowania hasła jest włączona** flaga **wybrane** lub **wszystkich** , a następnie wybierz **Zapisz**. |
| Użytkownik nie ma usługi Azure AD Premium lub podstawowa przypisaną licencję. **Administrator nie włączył pozwala korzystać z tej funkcji.** | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Aby rozwiązać ten problem, należy przypisywania licencji do danego konta administratora. Postępuj zgodnie z instrukcjami w [przypisania, sprawdź i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artykułu.|
| Występuje błąd podczas przetwarzania żądania. | Może to być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany przez awarię usług lub problem z konfiguracją. Jeśli zostanie wyświetlony ten błąd ma wpływ na działalność, skontaktuj się z pomocy technicznej firmy Microsoft, aby uzyskać dodatkową pomoc. |

## <a name="troubleshoot-the-password-reset-portal"></a>Rozwiązywanie problemów z portalem resetowania haseł

| Błąd | Rozwiązanie |
| --- | --- |
| Katalog nie jest włączona w celu zresetowania hasła. | Przełącznik **samoobsługowego resetowania hasła jest włączona** flaga **wybrane** lub **wszystkich** , a następnie wybierz **Zapisz**. |
| Użytkownik nie ma usługi Azure AD Premium lub podstawowa przypisaną licencję. | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Jeśli przypiszesz licencję do konta administratora w danym może rozwiązać ten problem. Postępuj zgodnie z instrukcjami w [przypisania, sprawdź i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artykułu. |
| Katalog jest włączone do resetowania haseł, ale użytkownik ma brakujące lub źle skonstruowany informacje dotyczące uwierzytelniania. | Przed kontynuowaniem upewnij się, że użytkownik ma poprawnie sformułowany dane kontaktowe w pliku w katalogu. Aby uzyskać więcej informacji, zobacz [dane używane przez haseł usługi Azure AD resetowania](howto-sspr-authenticationdata.md). |
| Katalog jest włączone do resetowania haseł, ale użytkownik ma tylko jeden fragment dane kontaktowe w pliku, gdy zasady zostały ustawione wymaganie dwóch metod weryfikacji. | Przed kontynuowaniem upewnij się, że użytkownik ma co najmniej dwóch metod kontaktu prawidłowo skonfigurowane. Przykładem jest posiadanie numeru telefonu komórkowego *i* numer telefonu w biurze. |
| Katalog jest włączone do resetowania hasła i użytkownik jest prawidłowo skonfigurowany, ale użytkownik nie może otrzymywać. | Może to wynikać z błędu tymczasowej usługi lub ma nieprawidłowe dane kontaktowe, który firma Microsoft nie może poprawnie wykryć. <br> <br> Jeśli użytkownik czeka 10 sekund, "spróbuj ponownie" i "Skontaktuj się z administratorem" łącza są wyświetlane. Jeśli użytkownik wybierze "spróbuj ponownie", ponawia próbę wywołania. Jeśli użytkownik wybierze "Skontaktuj się z administratorem", wysyła wiadomość e-mail z formularza do ich administratorów, którzy zgłosili resetowania hasła, aby wykonać dla tego konta użytkownika. |
| Użytkownik nigdy nie otrzymuje resetowania haseł programu SMS lub połączenie telefoniczne. | Może to być wynikiem numeru telefonu źle sformułowane w katalogu. Upewnij się, że numer telefonu jest w formacie "+ ccc xxxyyyzzzzXeeee". <br> <br> Resetowanie hasła nie obsługuje rozszerzeń, nawet jeśli zostanie określony w katalogu. Wywoływane jest wywołanie rozszerzenia są usuwane. Użyj numeru bez rozszerzenia lub zintegrować rozszerzenie numer telefonu w prywatnych gałęzi programu exchange (PBX). |
| Użytkownik nigdy nie otrzymuje wiadomość e-mail resetowania hasła. | Najczęstszą przyczyną tego problemu jest, że komunikat zostanie odrzucone przez filtr wiadomości-śmieci. Sprawdź swoje wiadomości-śmieci, folder wiadomości-śmieci lub usuniętych elementów wiadomości e-mail. <br> <br> Upewnij się również sprawdzamy konta prawidłowy adres e-mail dla wiadomości. |
| Ustawiam zasad resetowania haseł, ale podczas resetowania hasła korzysta z konta administratora, nie jest stosowane te zasady. | Firma Microsoft zarządza i kontrolki zasad, aby zapewnić najwyższy poziom zabezpieczeń resetowania hasła administratora. |
| Użytkownik będzie mógł próby resetowania hasła zbyt wiele razy dziennie. | Wdrażamy mechanizm ograniczania automatycznego aby uniemożliwić użytkownikom z próby zresetowania hasła zbyt wiele razy w krótkim czasie. Ograniczanie występuje, gdy: <br><ul><li>Użytkownik próbuje zweryfikować numeru telefonu pięć razy w ciągu jednej godziny.</li><li>Użytkownik próbuje użyć bramy pytania zabezpieczeń pięć razy w ciągu jednej godziny.</li><li>Użytkownik próbuje zresetować hasło dla tego samego konta użytkownika pięć razy w ciągu jednej godziny.</li></ul>Aby rozwiązać ten problem, poinstruuj użytkownika, poczekaj 24 godziny po ostatniej próby. Użytkownik następnie mogą zresetować swoje hasło. |
| Użytkownik zobaczy następujący błąd podczas sprawdzania poprawności numeru telefonu. | Ten błąd występuje, jeśli podany numer telefonu i numer telefonu w pliku nie są zgodne. Upewnij się, że użytkownik jest wprowadzenie pełnego numeru telefonu, tym kod obszaru i kraj, w przypadku, gdy próbują użyć metody oparte na telefon do resetowania hasła. |
| Występuje błąd podczas przetwarzania żądania. | Może to być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany przez awarię usług lub problem z konfiguracją. Jeśli zostanie wyświetlony ten błąd ma wpływ na działalność, skontaktuj się z pomocy technicznej firmy Microsoft, aby uzyskać dodatkową pomoc. |
| Naruszenie zasad w środowisku lokalnym | Hasło nie spełnia w lokalnych zasadach haseł usługi Active Directory. |
| Hasło jest niezgodne z zasadami rozmytego | Hasło, którego użyto pojawia się w [zakazane liście haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) i nie mogą być używane. |

## <a name="troubleshoot-password-writeback"></a>Rozwiązywanie problemów z zapisywaniem zwrotnym haseł

| Błąd | Rozwiązanie |
| --- | --- |
| Usługa resetowania hasła nie zostanie uruchomiona w środowisku lokalnym. Błąd 6800 pojawia się w dzienniku zdarzeń aplikacji na komputerze program Azure AD Connect. <br> <br> Po dołączeniu, Sfederowane uwierzytelnianie przekazywane lub synchronizację skrótów haseł użytkowników nie można zresetować swoje hasła. | Po włączeniu funkcji zapisywania zwrotnego haseł, aparat synchronizacji wywołuje biblioteki funkcji zapisywania zwrotnego, który umożliwia skonfigurowanie (dołączenie), komunikując się do dołączania do usługi w chmurze. Wszelkie napotkano błędy podczas dołączania lub podczas uruchamiania usługi Windows Communication Foundation (WCF) punktu końcowego dla wyników funkcji zapisywania zwrotnego haseł błędy w dzienniku zdarzeń na komputerze program Azure AD Connect. <br> <br> Podczas ponownego uruchamiania usługi Azure AD Sync (ADSync) Jeśli skonfigurowano funkcję zapisywania zwrotnego, punkt końcowy usługi WCF jest uruchamiany. Jednak w przypadku niepowodzenia uruchomienia punktu końcowego, firma Microsoft dziennika zdarzeń 6800 i umożliwić uruchomiona usługa synchronizacji. Obecność to zdarzenie oznacza, że punkt końcowy funkcji zapisywania zwrotnego haseł nie została uruchomiona. Szczegóły dziennika zdarzeń dla tego zdarzenia 6800, wraz z dziennika zdarzeń, które wpisy wygenerować przez składnik PasswordResetService wskazywać, dlaczego nie można uruchomić punkt końcowy. Przejrzyj te błędy dziennika zdarzeń, a następnie spróbuj ponownie uruchomić program Azure AD Connect, jeśli funkcja zapisywania zwrotnego haseł nie działa. Jeśli problem będzie się powtarzać, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.
| Gdy użytkownik próbuje zresetować hasło lub odblokować konto z zapisywaniem zwrotnym haseł włączone, kończy się niepowodzeniem. <br> <br> Ponadto zobaczysz zdarzenia w dzienniku zdarzeń programu Azure AD Connect, który zawiera: "Aparat synchronizacji zwrócił błąd hr = 800700CE, komunikat = nazwa pliku lub rozszerzenie jest zbyt długa" po wystąpieniu operacji odblokowania. | Znajdź konto usługi Active Directory dla programu Azure AD Connect i zresetuj hasło, tak aby zawierała co najwyżej 256 znaków. Następnie otwórz **usługi synchronizacji** z **Start** menu. Przejdź do **łączników** i Znajdź **łącznika usługi Active Directory**. Zaznacz go, a następnie wybierz pozycję **właściwości**. Przejdź do **poświadczenia** strony, a następnie wprowadź nowe hasło. Wybierz **OK** aby zamknąć stronę. |
| Na ostatnim etapie procesu instalacji program Azure AD Connect zostanie wyświetlony komunikat o błędzie informujący, że nie można skonfigurować tej funkcji zapisywania zwrotnego haseł. <br> <br> W dzienniku zdarzeń aplikacji Azure AD Connect zawiera błąd 32009 z tekstem "Błąd podczas pobierania informacji uwierzytelniania tokenu." | Ten błąd występuje w dwóch przypadkach: <br><ul><li>Określono nieprawidłowe hasło dla konta administratora globalnego, określony na początku procesu instalacji program Azure AD Connect.</li><li>Podjęto próbę użycia użytkownika federacyjnego dla konta administratora globalnego, określony na początku procesu instalacji program Azure AD Connect.</li></ul> Aby rozwiązać ten problem, upewnij się, że nie używasz federacyjnego konta administratora globalnego, określony na początku procesu instalacji. Upewnij się również, że podane hasło jest poprawne. |
| W dzienniku zdarzeń programu Azure AD Connect maszyny zawiera błąd 32002, który jest generowany przez uruchomienie PasswordResetService. <br> <br> Odczytuje błąd: "Błąd podczas nawiązywania połączenia magistrali usług. Dostawca tokenu nie mógł zapewnić tokenu zabezpieczającego." | Usługi w środowisku lokalnym nie jest w stanie połączyć się z punktu końcowego usługi Azure Service Bus w chmurze. Reguła zapory blokuje połączenie wychodzące do określonego adresu sieci web lub port zazwyczaj przyczyną tego błędu. Zobacz [wymagania wstępne dotyczące łączności](../hybrid/how-to-connect-install-prerequisites.md) Aby uzyskać więcej informacji. Po zaktualizowaniu tych reguł ponownego rozruchu komputera program Azure AD Connect i zapisywania zwrotnego haseł powinny działać ponownie. |
| Po zakończeniu pracy przez pewien czas federacyjnych, uwierzytelnianie przekazywane lub synchronizację skrótów haseł użytkowników nie można zresetować swoje hasła. | W rzadkich przypadkach usługi zapisywania zwrotnego haseł można przełączać się do ponownego uruchomienia po ponownym uruchomieniu program Azure AD Connect. W takich przypadkach należy najpierw sprawdź, czy funkcja zapisywania zwrotnego haseł wydaje się być włączone w środowisku lokalnym. Możesz sprawdzić za pomocą Kreatora Azure AD Connect lub programu PowerShell (zobacz poprzednią sekcję HowTos). Jeśli funkcja wydaje się być włączone, spróbuj włączania lub wyłączania tej funkcji ponownie przy użyciu interfejsu użytkownika lub środowiska PowerShell. Jeśli to nie rozwiąże problemu, spróbuj pełnego odinstalowania i ponowne zainstalowanie programu Azure AD Connect. |
| Uwierzytelnianie federacyjne, przekazywania lub synchronizację skrótów haseł użytkowników, którzy spróbują resetowania swoich haseł wyświetlony błąd po próbie Prześlij swoje hasło. Ten błąd wskazuje, że wystąpił problem z usługi. <br ><br> Oprócz tego problemu podczas wykonywania operacji resetowania hasła mogą pojawiać się błąd, agent zarządzania nastąpiła odmowa dostępu do dzienników zdarzeń w środowisku lokalnym. | Jeśli widzisz te błędy w dzienniku zdarzeń, upewnij się, że konto Active Directory Management Agent (ADMA), które zostało określone w Kreatorze w czasie konfiguracji ma niezbędne uprawnienia do zapisywania zwrotnego haseł. <br> <br> Po podano to uprawnienie, może potrwać maksymalnie godzinę uprawnień ścieknie w dół za pośrednictwem `sdprop` zadanie w tle na kontrolerze domeny (DC). <br> <br> Do resetowania haseł w pracy uprawnień musi być dołączana do deskryptora zabezpieczeń obiektu użytkownika, resetowania haseł. Dopóki to uprawnienie pojawia się w obiekcie użytkownika, resetowanie haseł w dalszym ciągu kończy się niepowodzeniem z komunikatem o odmowie dostępu. |
| Uwierzytelnianie federacyjne, przekazywania lub synchronizację skrótów haseł użytkowników, którzy spróbują resetowania swoich haseł wyświetlony błąd, po przesyłanie swoje hasło. Ten błąd wskazuje, że wystąpił problem z usługi. <br> <br> Oprócz tego problemu podczas wykonywania operacji resetowania hasła, możesz zobaczyć błąd w dziennikach zdarzeń z usługi Azure AD Connect, co wskazuje na błąd "Nie można odnaleźć obiektu". | Błąd ten zwykle wskazuje, że aparat synchronizacji jest nie można odnaleźć obiektu użytkownika w obszarze łącznika usługi Azure AD lub połączone metaverse (MV) lub obiektu przestrzeni łącznika usługi Azure AD. <br> <br> Aby rozwiązać ten problem, upewnij się, że użytkownik faktycznie jest zsynchronizowany ze środowiska lokalnego do usługi Azure AD za pośrednictwem bieżącego wystąpienia programu Azure AD Connect i sprawdzić stan obiektów łącznika miejsca do magazynowania i MV. Upewnij się, że obiekt usługi certyfikatów w usłudze Active Directory (AD CS) jest podłączony do obiektu MV za pomocą reguły "Microsoft.InfromADUserAccountEnabled.xxx".|
| Uwierzytelnianie federacyjne, przekazywania lub synchronizację skrótów haseł użytkowników, którzy spróbują resetowania swoich haseł wyświetlony błąd, po przesyłanie swoje hasło. Ten błąd wskazuje, że wystąpił problem z usługi. <br> <br> Oprócz tego problemu podczas wykonywania operacji resetowania hasła, możesz zobaczyć błąd w dziennikach zdarzeń z usługi Azure AD Connect, która wskazuje, że jest błąd "Znaleziono wiele dopasowań". | Oznacza to, że aparat synchronizacji wykryła, że obiektu MV jest podłączony do więcej niż jeden obiekt usług AD CS za pomocą "Microsoft.InfromADUserAccountEnabled.xxx". Oznacza to, że użytkownik ma włączone konto w więcej niż jednym lesie. W tym scenariuszu nie jest obsługiwana na potrzeby zapisywania zwrotnego haseł. |
| Operacje hasło kończyć się niepowodzeniem z powodu błędu konfiguracji. W dzienniku zdarzeń aplikacji zawiera błąd usługi Azure AD Connect 6329 z tekstem "0x8023061f (operacja nie powiodła się, ponieważ synchronizacja haseł nie jest włączona dla tego agenta zarządzania)". | Ten błąd występuje, jeśli zostało zmienione konfiguracji usługi Azure AD Connect, można dodać nowego lasu usługi Active Directory (lub, aby usunąć, a ich istniejącego lasu) po funkcji zapisywania zwrotnego haseł została już włączona. Operacje haseł dla użytkowników w takich niedawno dodano lasów kończyć się niepowodzeniem. Aby rozwiązać ten problem, wyłącz, a następnie ponowne włączenie funkcji zapisywania zwrotnego haseł, po zakończeniu zmiany konfiguracji lasu. |

## <a name="password-writeback-event-log-error-codes"></a>Kody błędów w dzienniku zdarzeń zapisywania zwrotnego haseł

Najlepszym rozwiązaniem podczas rozwiązywania problemów z zapisywaniem zwrotnym haseł jest Sprawdź w dzienniku zdarzeń aplikacji na komputerze program Azure AD Connect. Ten dziennik zdarzeń zawiera zdarzenia z dwóch źródeł znaczenie w odniesieniu do zapisywania zwrotnego haseł. Źródło PasswordResetService opisuje operacje, jak i problemów związanych z operacji zapisywania zwrotnego haseł. Źródło ADSync opisuje operacje, jak i problemów związanych z ustawieniem hasła w środowisku usługi Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Jeśli źródłem zdarzenia jest ADSync

| Kod | Nazwa lub wiadomości | Opis |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0X80230619: "Ograniczenie zapobiega hasło zmieniany z bieżącym określoną." | To zdarzenie występuje, gdy próbuje ustawić hasło w Twoim katalogu lokalnego, który nie spełnia okres ważności hasła, historii, złożoność i filtrowania wymagania dotyczące domeny usługi zapisywania zwrotnego haseł. <br> <br> Jeśli masz minimalny okres ważności hasła i niedawno zmieniono hasło, w tym przedziale czasu, nie możesz ponownie zmienić hasło, aż do napotkania określonej wiek w domenie. Do celów testowych, minimalny wiek powinna być równa 0. <br> <br> Jeśli masz wymagania dotyczące historii haseł włączone, a następnie należy wybrać hasło, który nie został jeszcze użyty w ciągu ostatnich *N* razy, gdzie *N* jest ustawienie historii haseł. Jeśli zaznaczysz hasła, który został użyty w ciągu ostatnich *N* razy, a następnie w tym przypadku wyświetlony błąd. Do celów testowych, historii haseł powinna być równa 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik podejmuje próbę zmiany lub resetowania hasła. <br> <br> Jeśli mają włączone filtry hasła, a użytkownik wybierze hasło nie spełnia kryteriów filtrowania, a następnie resetowania lub zmiany, operacja zakończy się niepowodzeniem. |
| 6329 | MMS(3040): admaexport.cpp(2837): Serwer nie zawiera kontrolki zasad haseł LDAP. | Ten problem występuje, gdy kontrolka LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) nie jest włączona na kontrolerów domeny. Aby użyć funkcji zapisywania zwrotnego haseł, możesz włączyć formant. Aby to zrobić, kontrolerów domeny musi być w systemie Windows Server 2008 R2 lub nowszym. |
| HR 8023042 | Aparat synchronizacji zwrócił błąd hr = 80230402, komunikat = próba pobrania obiektu nie powiodło się, ponieważ istnieją zduplikowane wpisy z tego samego zakotwiczenia. | Ten błąd występuje, gdy ten sam identyfikator użytkownika jest włączona w wielu domenach. To na przykład jeśli synchronizowania lasów kont i zasobów i mieć tej samej nazwy użytkownika, które są obecne i włączona w każdym lesie. <br> <br> Ten błąd może również wystąpić, jeśli używasz atrybutem zakotwiczenia nie jest unikatowa, takie jak alias lub nazwy UPN i dwóch użytkowników mają ten sam atrybut zakotwiczenia. <br> <br> Aby rozwiązać ten problem, upewnij się, że nie masz żadnych zduplikowanych użytkowników w ramach Twoich domen i użyć atrybutu zakotwiczenia unikatowy dla każdego użytkownika. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Jeśli źródłem zdarzenia jest PasswordResetService

| Kod | Nazwa lub wiadomości | Opis |
| --- | --- | --- |
| 31001 | PasswordResetStart | To zdarzenie oznacza, że Usługa lokalna wykrył, że żądanie uwierzytelniania federacyjnego, przekazywania lub synchronizacji skrótu hasła użytkownika, który pochodzi z chmury resetowania hasła. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zapisywania zwrotnego resetowania haseł. |
| 31002 | PasswordResetSuccess | To zdarzenie oznacza, że użytkownik wybrane nowe hasło podczas operacji resetowania hasła. Ustaliliśmy, że to hasło spełnia wymagania dotyczące haseł firmy. Hasło został pomyślnie zapisany ponownie lokalnego środowiska usługi Active Directory. |
| 31003 | PasswordResetFail | To zdarzenie oznacza, że hasło wybrane przez użytkownika i hasło pomyślnie dotarła do środowiska lokalnego. Ale jeśli próbowano ustawić hasło w lokalnym środowisku Active Directory wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełniają wiek, historii, złożoność ani filtrowania wymagania dla domeny. Aby rozwiązać ten problem, należy utworzyć nowe hasło.</li><li>Konto usługi Menedżera ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takie jak grupy administratorów domeny lub przedsiębiorstwa, które nie zezwalają na operacji ustawiania haseł.</li></ul>|
| 31004 | OnboardingEventStart | To zdarzenie występuje, jeśli włączysz funkcję zapisywania zwrotnego haseł w programie Azure AD Connect i zaczęliśmy dołączania do organizacji, aby usługi sieci web zapisywania zwrotnego haseł. |
| 31005 | OnboardingEventSuccess | To zdarzenie oznacza, że proces dołączania zakończyło się pomyślnie i czy funkcja zapisywania zwrotnego haseł jest gotowy do użycia. |
| 31006 | ChangePasswordStart | To zdarzenie oznacza, że Usługa lokalna nie wykryto żądanie zmiany hasła, uwierzytelnianie federacyjne, przekazywania lub synchronizacji skrótu hasła użytkownika, który pochodzi z chmury. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zapisywania zwrotnego zmiany hasła. |
| 31007 | ChangePasswordSuccess | To zdarzenie oznacza, że użytkownik wybrane nowe hasło podczas operacji zmiany hasła, Ustaliliśmy, że hasło spełnia wymagania dotyczące haseł firmy oraz że hasło został zapisany pomyślnie powrót do lokalnego środowiska usługi Active Directory. |
| 31008 | ChangePasswordFail | To zdarzenie oznacza, że użytkownik wybrał hasła i że hasło pomyślnie dotarła do środowiska lokalnego, ale jeśli próbowano ustawić hasło w lokalnym środowisku Active Directory wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełniają wiek, historii, złożoność ani filtrowania wymagania dla domeny. Aby rozwiązać ten problem, należy utworzyć nowe hasło.</li><li>Konto usługi Menedżera ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takich jak Administratorzy domeny lub przedsiębiorstwa, które nie zezwalają na operacji ustawiania haseł.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Lokalna Usługa wykryła, że żądanie dotyczące uwierzytelniania federacyjnego, przekazywania lub synchronizację skrótów haseł użytkowników pochodzących od administratora w imieniu użytkownika resetowania hasła. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zapisywania zwrotnego resetowania haseł, może być inicjowane przez administratora. |
| 31010 | ResetUserPasswordByAdminSuccess | Administrator wybrać nowe hasło podczas operacji resetowania hasła zainicjowane przez administratora. Ustaliliśmy, że to hasło spełnia wymagania dotyczące haseł firmy. Hasło został pomyślnie zapisany ponownie lokalnego środowiska usługi Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Administrator wybrany hasła w imieniu użytkownika. Hasło pomyślnie dotarła do środowiska lokalnego. Ale jeśli próbowano ustawić hasło w lokalnym środowisku Active Directory wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełniają wiek, historii, złożoność ani filtrowania wymagania dla domeny. Wypróbuj nowe hasło, aby rozwiązać ten problem.</li><li>Konto usługi Menedżera ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takich jak Administratorzy domeny lub przedsiębiorstwa, które nie zezwalają na operacji ustawiania haseł.</li></ul>  |
| 31012 | OffboardingEventStart | To zdarzenie występuje, gdy należy wyłączyć funkcję zapisywania zwrotnego haseł w programie Azure AD Connect i wskazuje, że Rozpoczęliśmy odłączania od organizacji, aby usługi sieci web zapisywania zwrotnego haseł. |
| 31013| OffboardingEventSuccess| To zdarzenie oznacza, że proces odłączania od zakończyło się pomyślnie i został pomyślnie wyłączony funkcji zapisywania zwrotnego haseł. |
| 31014| OffboardingEventFail| To zdarzenie oznacza, że proces odłączania od zakończyła się niepowodzeniem. Może to być z powodu błędu w chmurze lub lokalnie konto administratora określone podczas konfigurowania uprawnień. Ten błąd może również wystąpić, jeśli próbujesz użyć konta administratora globalnego federacyjnego chmury, wyłączając zapisywanie zwrotne haseł. Aby rozwiązać ten problem, sprawdź swoje uprawnienia administracyjne i upewnij się, że używasz nie kontem federacyjnym podczas konfigurowania możliwości zapisywania zwrotnego haseł.|
| 31015| WriteBackServiceStarted| To zdarzenie oznacza, że usługi zapisywania zwrotnego haseł została uruchomiona pomyślnie. Jest gotowy do akceptowania żądań zarządzania haseł w chmurze.|
| 31016| WriteBackServiceStopped| To zdarzenie oznacza, że usługi zapisywania zwrotnego haseł została zatrzymana. Wszelkie żądania zarządzania haseł w chmurze nie powiedzie się.|
| 31017| AuthTokenSuccess| To zdarzenie oznacza, firma Microsoft pomyślnie pobrano token autoryzacji dla administratora globalnego, określony podczas instalacji program Azure AD Connect, aby rozpocząć proces odłączania od lub dołączania.|
| 31018| KeyPairCreationSuccess| To zdarzenie oznacza, firma Microsoft pomyślnie utworzono klucz szyfrowania hasła. Ten klucz jest używany do szyfrowania haseł z chmury do wysłania do środowiska lokalnego.|
| 32000| Nieznany błąd zostanie| To zdarzenie oznacza, że wystąpił nieznany błąd podczas operacji zarządzania hasła. Przyjrzyj się tekst wyjątku w zdarzeniu, aby uzyskać więcej informacji. Jeśli występują problemy, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł. Jeśli to nie pomoże, dołączenie kopii dziennika zdarzeń wraz ze śledzenia identyfikator określonego niejawnego programu testów do specjalistą pomocy technicznej.|
| 32001| ServiceError| To zdarzenie oznacza, wystąpił błąd podczas łączenia z hasłem chmury resetowania usługi. Ten błąd występuje zazwyczaj, gdy Usługa lokalna nie może połączyć się z usługą resetowania hasła w sieci web.|
| 32002| ServiceBusError| To zdarzenie oznacza, wystąpił błąd podczas łączenia się z wystąpieniem usługi Service Bus Twojej dzierżawy. Może to nastąpić, jeśli one blokuje połączenia wychodzące w środowisku w środowisku lokalnym. Sprawdź w taki sposób, aby upewnić się, Zezwalaj na połączenia za pośrednictwem protokołu TCP 443 i do https://ssprsbprodncu-sb.accesscontrol.windows.net/, a następnie spróbuj ponownie. Jeśli nadal występują problemy, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.|
| 32003| InPutValidationError| To zdarzenie oznacza, że dane wejściowe przekazane do naszego interfejsu API usługi sieci web była nieprawidłowa. Spróbuj ponownie wykonać operację.|
| 32004| DecryptionError| To zdarzenie oznacza, że wystąpił błąd podczas odszyfrowywania hasła, które już korzystać z chmury. Może to być spowodowane odszyfrowywania klucza niezgodności z usługą w chmurze i sieci w środowisku lokalnym. Aby rozwiązać ten problem, wyłącz i ponownie włączyć funkcję zapisywania zwrotnego haseł w środowisku w środowisku lokalnym.|
| 32005| ConfigurationError| Zestawu dokumentacji podczas dołączania oszczędzamy informacje specyficzne dla dzierżawy w pliku konfiguracji w danym środowisku w środowisku lokalnym. To zdarzenie oznacza, że wystąpił błąd podczas zapisywania tego pliku lub, jeśli usługa została uruchomiona, wystąpił błąd podczas odczytywania pliku. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł w celu wymuszenia nadpisania pliku konfiguracji.|
| 32007| OnBoardingConfigUpdateError| Zestawu dokumentacji podczas dołączania wyślemy danych z chmury do środowiska lokalnego usługi resetowania hasła. Czy dane są następnie zapisywane do pliku w pamięci, przed wysłaniem go do usługi synchronizacji można bezpiecznie przechowywane na dysku. To zdarzenie oznacza, że występuje problem z zapisu lub aktualizowanie danych w pamięci. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł w celu wymuszenia nadpisania ten plik konfiguracji.|
| 32008| ValidationError| To zdarzenie oznacza, że firma Microsoft otrzymał nieprawidłową odpowiedź z usługi sieci web resetowania hasła. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.|
| 32009| AuthTokenError| To zdarzenie oznacza, że firma Microsoft nie można pobrać autoryzacji tokenu dla konta administratora globalnego, określony podczas instalacji program Azure AD Connect. Ten błąd może być spowodowany przez nieprawidłowe nazwy użytkownika i hasło określone dla konta administratora globalnego. Ten błąd może również wystąpić, jeśli określone konto administratora globalnego jest sfederowana. Aby rozwiązać ten problem, uruchom ponownie konfigurację z prawidłową nazwę użytkownika i hasło i upewnij się, że administrator konta zarządzanego (tylko w chmurze lub synchronizacji haseł).|
| 32010| CryptoError| To zdarzenie oznacza, wystąpił błąd podczas generowania hasło klucza szyfrowania lub odszyfrowywania hasła, która pojawi się w usłudze w chmurze. Ten błąd prawdopodobnie wskazuje na problem ze środowiskiem. Przyjrzyj się szczegółowe informacje z dziennika zdarzeń, aby dowiedzieć się więcej o sposobie rozwiązania tego problemu. Możesz również spróbować wyłączenia i ponownego włączenia usługi zapisywania zwrotnego haseł.|
| 32011| OnBoardingServiceError| To zdarzenie oznacza, że Usługa lokalna poprawnie nie można nawiązać połączenia z usługą resetowania hasła w sieci web w celu zainicjowania procesu dołączania. Taka sytuacja może wystąpić w wyniku reguły zapory lub jeśli występuje problem podczas uzyskiwania tokenu do uwierzytelniania dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że możesz teraz nie blokuje połączenia wychodzące za pośrednictwem protokołu TCP 443 i TCP 9350-9354 lub do https://ssprsbprodncu-sb.accesscontrol.windows.net/. Ponadto upewnij się, że konto administratora usługi Azure AD za pomocą dołączanie nie jest federacyjna.|
| 32013| OffBoardingError| To zdarzenie oznacza, że Usługa lokalna poprawnie nie można nawiązać połączenia z usługą resetowania hasła w sieci web, aby zainicjować proces odłączania od. Taka sytuacja może wystąpić w wyniku reguły zapory lub jeśli występuje problem podczas uzyskiwania tokenu do autoryzacji dla dzierżawy. Aby rozwiązać ten problem, upewnij się, masz nie blokuje połączenia wychodzące przez 443 lub do https://ssprsbprodncu-sb.accesscontrol.windows.net/, oraz że konto administratora usługi Azure Active Directory za pomocą odłączony nie jest federacyjna.|
| 32014| ServiceBusWarning| To zdarzenie oznacza, że musimy ponów próbę, aby nawiązać połączenie z wystąpieniem usługi Service Bus dzierżawy usługi. W normalnych warunkach to nie powinny być istotna, lecz jeśli to zdarzenie wiele razy, należy wziąć pod uwagę sprawdzić połączenie sieciowe z magistralą usług, zwłaszcza, jeśli jest połączenia z dużym opóźnieniem lub o niskiej przepustowości.|
| 32015| ReportServiceHealthError| Aby monitorować kondycję swojej usługi zapisywania zwrotnego haseł, wyślemy danych pulsu dla usługi sieci web resetowania hasła co pięć minut. To zdarzenie oznacza, że wystąpił błąd podczas wysyłania informacji o tym kondycji do usługi sieci web w chmurze. Te informacje o kondycji nie zawiera obiektu danych osobowych (OII) lub dane identyfikowalne dane osobowe (PII) i jest wyłącznie pulsu i statystyki usług w warstwie podstawowa, dzięki czemu możemy udzielić informacji o stanie usługi w chmurze.|
| 33001| ADUnKnownError| To zdarzenie oznacza, że wystąpił nieznany błąd zwrócony przez usługę Active Directory. Sprawdź dziennik zdarzeń serwera Azure AD Connect dla zdarzeń ze źródła ADSync, aby uzyskać więcej informacji.|
| 33002| ADUserNotFoundError| To zdarzenie oznacza, że użytkownik, który próbuje Resetowanie lub zmienianie hasła nie został znaleziony w katalogu lokalnego. Ten błąd może wystąpić, gdy użytkownik został usunięty w środowisku lokalnym, ale nie w chmurze. Ten błąd może również wystąpić, jeśli występuje problem z synchronizacją. Sprawdź dzienniki synchronizacji i ostatniego kilka szczegółów synchronizacji Uruchom, aby uzyskać więcej informacji.|
| 33003| ADMutliMatchError| Podczas resetowania hasła lub zmian, żądanie pochodzi z chmury, używamy zakotwiczenia chmury, określone w procesie instalacji programu Azure AD Connect do określenia, jak połączyć tego żądania do użytkownika w środowisku w środowisku lokalnym. To zdarzenie oznacza, że firma Microsoft znajduje dwóch użytkowników w Twoim katalogu w środowisku lokalnym za pomocą tego samego atrybutu zakotwiczenia w chmurze. Sprawdź dzienniki synchronizacji i ostatniego kilka szczegółów synchronizacji Uruchom, aby uzyskać więcej informacji.|
| 33004| ADPermissionsError| To zdarzenie oznacza, że konto usługi Active Directory Management Agent (ADMA) nie ma odpowiednich uprawnień na konto, aby ustawić nowe hasło. Upewnij się, że konta ADMA w lesie użytkownika został zresetowany i zmienić hasło uprawnienia do wszystkich obiektów w lesie. Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień, zobacz krok 4: Konfigurowanie odpowiednich uprawnień usługi Active Directory.|
| 33005| ADUserAccountDisabled| To zdarzenie oznacza, że staraliśmy się Resetowanie lub zmienianie hasła dla konta które zostało wyłączone w środowisku lokalnym. Włącz konto i spróbuj ponownie wykonać operację.|
| 33006| ADUserAccountLockedOut| To zdarzenie oznacza, że staraliśmy się Resetowanie lub zmienianie hasła dla konta, któremu został zablokowany w środowisku lokalnym. Blokady może wystąpić, gdy użytkownik ma nastąpiła zmiana lub zresetować operacji hasła zbyt wiele razy w krótkim czasie. Odblokowanie konta i spróbuj ponownie wykonać operację.|
| 33007| ADUserIncorrectPassword| To zdarzenie oznacza, że użytkownik określił niepoprawnego bieżącego hasła podczas wykonywania hasła operacji zmiany. Określ poprawny bieżące hasło i spróbuj ponownie.|
| 33008| ADPasswordPolicyError| To zdarzenie występuje, gdy próbuje ustawić hasło w Twoim katalogu lokalnego, który nie spełnia okres ważności hasła, historii, złożoność i filtrowania wymagania dotyczące domeny usługi zapisywania zwrotnego haseł. <br> <br> Jeśli masz minimalny okres ważności hasła i niedawno zmieniono hasło, w tym przedziale czasu, nie możesz ponownie zmienić hasło, aż do napotkania określonej wiek w domenie. Do celów testowych, minimalny wiek powinna być równa 0. <br> <br> Jeśli masz wymagania dotyczące historii haseł włączone, a następnie należy wybrać hasło, który nie został jeszcze użyty w ciągu ostatnich *N* razy, gdzie *N* jest ustawienie historii haseł. Jeśli zaznaczysz hasła, który został użyty w ciągu ostatnich *N* razy, a następnie w tym przypadku wyświetlony błąd. Do celów testowych, historii haseł powinna być równa 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik podejmuje próbę zmiany lub resetowania hasła. <br> <br> Jeśli mają włączone filtry hasła, a użytkownik wybierze hasło nie spełnia kryteriów filtrowania, a następnie resetowania lub zmiany, operacja zakończy się niepowodzeniem.|
| 33009| ADConfigurationError| To zdarzenie oznacza, że wystąpił problem podczas zapisywania hasła z powrotem do katalogu lokalnego, ze względu na problem z konfiguracją przy użyciu usługi Active Directory. Sprawdź dziennik zdarzeń aplikacji na komputerze program Azure AD Connect komunikatów z usługi ADSync, aby uzyskać więcej informacji, na którym wystąpił błąd.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Rozwiązywanie problemów z łącznością zapisywania zwrotnego haseł

Jeśli występują przerw w świadczeniu usługi za pomocą składnika zapisywania zwrotnego haseł programu Azure AD Connect, poniżej przedstawiono kilka szybkich kroków, które można wykonać w celu rozwiązania tego problemu:

* [Upewnij się, łączności sieciowej](#confirm-network-connectivity)
* [Uruchom ponownie usługę Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł](#disable-and-re-enable-the-password-writeback-feature)
* [Zainstalowanie najnowszej wersji Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](#troubleshoot-password-writeback)

Ogólnie rzecz biorąc, aby odzyskać usługę w taki sposób, najszybszych, firma Microsoft zaleca wykonaj następujące kroki w kolejności omówionych wcześniej.

### <a name="confirm-network-connectivity"></a>Upewnij się, łączności sieciowej

Najbardziej typowe punkt awarii jest zaporą i lub nieprawidłowo skonfigurowanych portów serwera proxy i limity czasu bezczynności. 

Dla usługi Azure AD Connect w wersji 1.1.443.0 lub nowszej możesz potrzebować wychodzącego HTTPS dostępu do następującego:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Większą szczegółowość odwoływać się zaktualizowaną listę [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) aktualizowany co środę i zaczyna obowiązywać od następnego poniedziałku.

Aby uzyskać więcej informacji, zapoznaj się z wymaganiami wstępnymi łączności w [wymagania wstępne programu Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md) artykułu.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Uruchom ponownie usługę Azure AD Connect Sync

Aby rozwiązać problemy z łącznością lub inne przejściowe problemy z usługą, uruchom ponownie usługę Azure AD Connect Sync:

1. Jako administrator, wybierz **Start** na serwerze z programem Azure AD Connect.
1. Wprowadź **services.msc** w polu wyszukiwania i wybierz pozycję **Enter**.
1. Wyszukaj **Microsoft Azure AD Sync** wpisu.
1. Kliknij prawym przyciskiem myszy wpisu usługi, wybierz **ponowne uruchomienie**, a następnie poczekaj na zakończenie operacji.

   ![Uruchom ponownie usługę Azure AD Sync, przy użyciu graficznego interfejsu użytkownika][Service restart]

Te kroki ponownie ustanowić połączenia z usługą w chmurze i rozwiązać przerw, który może występować. Jeśli ponowne uruchomienie usługi ADSync nie rozwiąże problemu, firma Microsoft zaleca, aby próbować wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł

Aby rozwiązać problemy z łącznością, wyłącz i ponownie włączyć funkcję zapisywania zwrotnego haseł:

   1. Jako administrator Otwórz kreatora Konfiguracja usłudze Azure AD Connect.
   1. W **nawiązywanie połączenia z usługi Azure AD**, wprowadź swoje poświadczenia administratora globalnego usługi Azure AD.
   1. W **łączenie z usługami AD DS**, wprowadź swoje poświadczenia administratora usługi AD Domain Services.
   1. W **unikatowa identyfikacja użytkowników**, wybierz opcję **dalej** przycisku.
   1. W **funkcje opcjonalne**, wyczyść **funkcji zapisywania zwrotnego haseł** pole wyboru.
   1. Wybierz **dalej** na pozostałych stronach okno dialogowe bez wprowadzania żadnych zmian, aż dojdziesz do **wszystko gotowe do skonfigurowania** strony.
   1. Upewnij się, że **wszystko gotowe do skonfigurowania strony** pokazuje **funkcji zapisywania zwrotnego haseł** opcję **wyłączone** , a następnie wybierz zielony **Konfiguruj** przycisk, aby zatwierdzić zmiany.
   1. W **Zakończono**, wyczyść **Synchronizuj teraz** opcji, a następnie wybierz **Zakończ** aby zamknąć kreatora.
   1. Otwórz kreatora Konfiguracja usłudze Azure AD Connect.
   1. Powtórz kroki od 2 do 8, z wyjątkiem upewnij się, że **funkcji zapisywania zwrotnego haseł** opcja **funkcje opcjonalne** strony, aby ponownie włączyć usługę.

Te kroki ponownie ustanowić połączenie przy użyciu naszej usługi w chmurze i rozwiązać przerw, który może występować.

Jeśli wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł nie rozwiąże problemu, firma Microsoft zaleca, ponownie zainstaluj program Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Zainstalowanie najnowszej wersji Azure AD Connect

Ponownie zainstalować program Azure AD Connect można rozwiązać, konfiguracji i problemy z połączeniem między jej usług w chmurze i lokalnym środowisku usługi Active Directory.

Firma Microsoft zaleca wykonanie tego kroku, tylko wtedy, gdy spróbujesz pierwsze dwa kroki, które opisano wcześniej.

> [!WARNING]
> Jeśli dostosowano reguły synchronizacji poza pole *wykonać ich kopię zapasową przed kontynuowaniem uaktualniania, a następnie ręcznie je wdrożyć ponownie po zakończeniu.*

1. Pobierz najnowszą wersję programu Azure AD Connect z [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Ponieważ masz już zainstalowany program Azure AD Connect, musisz wykonać uaktualnienie w miejscu do aktualizacji instalacji programu Azure AD Connect do najnowszej wersji.
1. Wykonaj pobranego pakietu i postępuj zgodnie z wyświetlanymi instrukcjami, aby zaktualizować swojej maszyny, program Azure AD Connect.

Poprzednie kroki, należy ponownie ustanowić połączenie przy użyciu naszej usługi w chmurze i rozwiązać przerw, który może występować.

Zainstalowanie najnowszej wersji serwera Azure AD Connect nie rozwiąże problemu, zaleca się, spróbuj wyłączyć, a następnie ponowne włączenie funkcji zapisywania zwrotnego haseł w ostatnim kroku po zainstalowaniu najnowszej wersji.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Sprawdź, czy program Azure AD Connect ma wymagane uprawnienia do zapisywania zwrotnego haseł

Azure AD Connect wymaga usługi Active Directory **Resetuj hasło** uprawnienia do wykonywania funkcji zapisywania zwrotnego haseł. Aby dowiedzieć się, jeśli program Azure AD Connect ma wymagane uprawnienia dla danego środowiska lokalnego konta użytkownika usługi Active Directory, możesz użyć funkcji Windows czynnych uprawnień:

1. Zaloguj się do serwera Azure AD Connect i uruchom **Menedżera usługi synchronizacji** , wybierając **Start** > **usługi synchronizacji**.
1. W obszarze **łączników** , a następnie wybierz lokalną **Active Directory Domain Services** łącznika, a następnie wybierz **właściwości**.  
   ![Przedstawiająca sposób edytować właściwości Menedżera usługi synchronizacji](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. W oknie podręcznym wybierz **nawiązywanie połączenia z lasu usługi Active Directory** i zanotuj **nazwa_użytkownika** właściwości. Ta właściwość jest konto usług AD DS używany przez program Azure AD Connect do wykonywania synchronizacji katalogów. Dla usługi Azure AD Connect, aby wykonać funkcję zapisywania zwrotnego haseł konto usług AD DS należy zresetować hasło uprawnień.  

   ![Wyszukiwanie konta użytkownika usługi Active Directory usługi synchronizacji](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Zaloguj się do kontrolera domeny w środowisku lokalnym i rozpocząć **użytkownicy usługi Active Directory i komputery** aplikacji.
1. Wybierz **widoku** i upewnij się, że **funkcje zaawansowane** opcja jest włączona.  

   ![Active Directory Użytkownicy i komputery Pokaż opcje zaawansowane](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Wyszukaj konta użytkownika usługi Active Directory, którą chcesz zweryfikować. Kliknij prawym przyciskiem myszy nazwę konta i wybierz **właściwości**.  
1. W oknie podręcznym, przejdź do **zabezpieczeń** kartę, a następnie wybierz pozycję **zaawansowane**.  
1. W **Zaawansowane ustawienia zabezpieczeń dla administratora** oknie podręcznym, przejdź do **dostęp czynny** kartę.
1. Wybierz **wybierz użytkownika**, wybierz konto usług AD DS używany przez usługę Azure AD Connect (zobacz krok 3), a następnie wybierz **wyświetlić skutecznego dostępu**.

   ![Skuteczne karcie dostęp do przedstawiający konto synchronizacji](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Przewiń w dół i poszukaj **Resetuj hasło**. Jeśli zaznaczono pozycję konto usług AD DS ma uprawnienia do resetowania hasła wybranego konta użytkownika usługi Active Directory.  

   ![Weryfikowanie, czy konto synchronizacji ma uprawnienia do resetowania hasła](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fora poświęcone systemowi Azure AD

Jeśli masz pytanie ogólne dotyczące usługi Azure AD i Samoobsługowe resetowanie haseł, możesz zadawać społeczności o pomoc na [Forum usługi Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Członkowie społeczności obejmują inżynierów, menedżerów produktów, specjalistami MVP i innymi specjalistów IT.

## <a name="contact-microsoft-support"></a>Skontaktuj się z pomocą techniczną firmy Microsoft

Jeśli nie możesz znaleźć odpowiedź na problem, nasze zespoły pomocy technicznej są zawsze dostępne ułatwić dalsze.

Aby prawidłowo pomóc, poprosimy Podaj jak najdokładniejsze informacje jak to możliwe, podczas otwierania przypadek. Obejmują one:

* **Ogólny opis błędu**: Co to jest błąd? Jaki był zachowanie, które zostało już? Jak można odtworzyć błąd? Jak to możliwe, podaj jak najdokładniejsze informacje.
* **Strona**: Które strony były na kiedy zauważyć błąd? To adres URL, jeśli jesteś w stanie i zrzut ekranu przedstawiający stronę.
* **Obsługuje kodu**: Jaki był kod pomocy technicznej, który został wygenerowany, gdy użytkownik był wyświetlany błąd?
   * Aby znaleźć ten kod, należy odtworzyć błąd, a następnie wybierz **obsługuje kodu** link w dolnej części ekranu i wysłać ze specjalistą pomocy technicznej identyfikator GUID, który powoduje.

   ![Znajdź kod pomocy technicznej w dolnej części ekranu][Support code]

  * Jeśli na stronie bez kodu pomocy technicznej u dołu wybierz F12 i wyszukaj identyfikator SID i CID i wysyłać te dwa wyniki ze specjalistą pomocy technicznej.
* **Daty, godziny i strefy czasowej**: Obejmują dokładnej daty i godziny *ze strefą czasową* których wystąpił błąd.
* **Identyfikator użytkownika**: Kto był użytkownika, który był wyświetlany błąd? Na przykład *użytkownika\@contoso.com*.
   * Jest to użytkownik federacyjny?
   * Jest to użytkownik uwierzytelniania przekazywanego
   * Jest to użytkownik synchronizację skrótów haseł?
   * Jest to użytkowników tylko w chmurze?
* **Licencjonowanie**: Czy użytkownik ma przypisaną licencję usługi Azure AD Premium lub usługi Azure AD podstawowa?
* **Dziennik zdarzeń aplikacji**: Jeśli używasz zapisywania zwrotnego haseł i błąd, to w infrastrukturze lokalnej, zawierać kopię plików z rozszerzeniem zip dzienniku zdarzeń aplikacji z serwera usługi Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Uruchom ponownie usługę Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kod pomocy technicznej znajduje się w prawym dolnym rogu okna"

## <a name="next-steps"></a>Kolejne kroki

Poniższe artykuły zawierają dodatkowe informacje na temat resetowania haseł za pomocą usługi Azure AD:

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
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
