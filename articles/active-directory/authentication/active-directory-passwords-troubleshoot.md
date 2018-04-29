---
title: Samoobsługowego resetowania hasła rozwiązywania problemów — Azure Active Directory
description: Rozwiązywania problemów z usługą Azure AD samoobsługowego resetowania hasła
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: b6f201c7c4aab477b2dbd7462653494f956ecc61
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-self-service-password-reset"></a>Rozwiązywanie problemów z samoobsługowego resetowania hasła

Czy występują problemy z usługi Azure Active Directory (Azure AD) samoobsługowego resetowania hasła (SSPR)? Informacje poniżej może ułatwić Ci zwiększanie wydajności pracy ponownie.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Rozwiązywanie błędów resetowania hasła samoobsługi, które użytkownik może zobaczyć

| Błąd | Szczegóły | Szczegóły techniczne |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Niestety, nie można zresetować hasło w tej chwili, ponieważ administrator wyłączył resetowania haseł dla Twojej organizacji. Nie istnieje żadne dalsze akcje, które należy wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o włączenie tej funkcji. Aby dowiedzieć się więcej, zobacz [pomocy, pamiętam hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Wykryto tego resetowania hasła nie został włączony przez administratora. Skontaktuj się z administratorem i poproś go o włączyć resetowania haseł dla Twojej organizacji. |
| WritebackNotEnabled = 10 |Niestety, nie można zresetować hasło w tej chwili, ponieważ administrator nie włączył niezbędne service w organizacji. Nie istnieje żadne dalsze akcje, które należy wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o sprawdzenie konfiguracji w organizacji. Aby dowiedzieć się więcej na temat tej usługi wymagane, zobacz [Konfigurowanie funkcji zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback). | SSPR_0010: Wykryto się, że nie został włączony tej funkcji zapisywania zwrotnego haseł. Skontaktuj się z administratorem i poproś go o włączenie funkcji zapisywania zwrotnego haseł. |
| SsprNotEnabledInUserPolicy = 11 | Niestety, nie można zresetować hasło w tej chwili, ponieważ administrator nie skonfigurował resetowania haseł dla Twojej organizacji. Nie istnieje żadne dalsze akcje, które należy wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o skonfigurowanie resetowania hasła. Aby dowiedzieć się więcej na temat hasła zresetowanie konfiguracji, zobacz [szybki start: usługi Azure AD samoobsługowego resetowania hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Nie zdefiniowano w organizacji zasad resetowania hasła. Skontaktuj się z administratorem i poproś go o zdefiniowanie zasad resetowania hasła. |
| UserNotLicensed = 12 | Niestety, nie można zresetować hasło w tej chwili, ponieważ licencje braku wymaganych ze swojej organizacji. Nie istnieje żadne dalsze akcje, które należy wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o przypisanie licencji Sprawdź. Aby dowiedzieć się więcej na temat licencjonowania, zobacz [zresetować licencjonowania wymagania dotyczące usługi Azure AD samoobsługi hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Organizacji nie ma licencji wymagane konieczne przeprowadzenie resetowania hasła. Skontaktuj się z administratorem i poproś go o Przejrzyj przypisań licencji. |
| UserNotMemberOfScopedAccessGroup = 13 | Niestety, nie można zresetować hasło w tej chwili, ponieważ administrator nie skonfigurował konto, aby korzystanie z funkcji resetowania hasła. Nie istnieje żadne dalsze akcje, które należy wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o skonfigurowanie konta do resetowania hasła. Aby dowiedzieć się więcej na temat konfiguracji konta do resetowania hasła, zobacz [wprowadzana resetowania hasła dla użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Nie jesteś członkiem grupy uprawnień do resetowania hasła. Skontaktuj się z Twojego administratora, jak i poproś o dodanie do grupy. |
| UserNotProperlyConfigured = 14 | Niestety, nie można zresetować hasło w tej chwili, ponieważ brakuje wymaganych informacji, z Twojego konta. Nie istnieje żadne dalsze akcje, które należy wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o zresetowania hasła. Po ponownym uzyskaniu dostępu do swojego konta, musisz zarejestrować niezbędne informacje. Aby zarejestrować informacje, postępuj zgodnie z instrukcjami [rejestracji samoobsługowego resetowania hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) artykułu. | SSPR_0014: Informacje zabezpieczające dodatkowe jest potrzebny do zresetowania hasła. Aby kontynuować, skontaktuj się z administratorem i poproś o zresetowanie hasła. Po uzyskaniu dostępu do konta możesz zarejestrować dodatkowych zabezpieczeń informacji w https://aka.ms/ssprsetup. Administrator może Dodaj informacje zabezpieczające dodatkowe do swojego konta, wykonując kroki opisane w [zestaw i dane uwierzytelniania odczytu do resetowania hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Niestety, nie możemy zresetować hasło w tej chwili z powodu problemu z konfiguracją resetowania hasła w organizacji. Nie istnieje żadne dalsze akcje, które należy wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś go o zbadać. Aby dowiedzieć się więcej o potencjalnym problemie, zobacz [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Nie możemy zresetować hasło z powodu błędu konfiguracji lokalnej. Skontaktuj się z administratorem i poproś go o zbadać. |
| OnPremisesConnectivityError = 30 | Niestety, nie możemy zresetować hasło w tej chwili z powodu problemów z połączeniem z Twojej organizacji. Nie akcji do wykonania od razu, ale może być rozwiązać problem, jeśli ponowisz próbę później. Jeśli problem będzie się powtarzać, skontaktuj się z administratorem i poproś go o zbadać. Aby dowiedzieć się więcej na temat problemów z łącznością, zobacz [rozwiązywania problemów z łącznością funkcji zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Firma Microsoft nie może zresetować hasło, z powodu słabej połączenia ze środowiskiem lokalnym. Skontaktuj się z administratorem i poproś o zbadać.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Rozwiązywanie problemów z konfiguracji resetowania hasła w portalu Azure

| Błąd | Rozwiązanie |
| --- | --- |
| Nie widzę **resetowania hasła** sekcję usługi Azure AD w portalu Azure. | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Przypisywanie licencji do danego konta administratora. Możesz wykonać kroki opisane w [przypisać, sprawdź i rozwiąż problemy z licencjami](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artykułu.|
| Nie ma opcji określoną konfigurację. | Wiele elementów interfejsu użytkownika są ukryte, dopóki nie są potrzebne. Spróbuj, włączyć wszystkie opcje, które mają być wyświetlane. |
| Nie widzę **integracji lokalnego** kartę. | Ta opcja tylko staje się widoczna, jeśli pobrano Azure AD Connect i skonfigurowano funkcję zapisywania zwrotnego haseł. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure AD Connect przy użyciu ustawień ekspresowych](./../connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Rozwiązywanie problemów z raportowania resetowania hasła

| Błąd | Rozwiązanie |
| --- | --- |
| Nie widzę żadnych typów działania zarządzania hasło w **Samoobsługowe zarządzanie hasłami** kategorii zdarzeń inspekcji. | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Aby rozwiązać ten problem, należy przypisywanie licencji do danego konta administratora. Postępuj zgodnie z instrukcjami [przypisać, sprawdź i rozwiąż problemy z licencjami](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artykułu. |
| Rejestracje użytkownika zawierają wiele razy. | Obecnie gdy użytkownik rejestruje, rejestrujemy każdego z nich poszczególnych danych, który jest zarejestrowany jako oddzielne zdarzenie. <br> <br> Jeśli chcesz agregować dane i mieć większą elastyczność w sposób możesz je wyświetlić, można pobrać raportu i Otwórz dane jako tabelę przestawną w programie Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Rozwiązywanie problemów z portalu rejestracji resetowania haseł

| Błąd | Rozwiązanie |
| --- | --- |
| Katalog nie jest włączona w celu resetowania haseł. **Administrator nie włączył można użyć tej funkcji.** | Przełącznik **włączyć samoobsługowe Resetowanie hasła** flaga **wybrane** lub **wszystkie** , a następnie wybierz **zapisać**. |
| Użytkownik nie ma usługi Azure AD Premium lub podstawowa przypisanej licencji. **Administrator nie włączył można użyć tej funkcji.** | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Aby rozwiązać ten problem, należy przypisywanie licencji do danego konta administratora. Postępuj zgodnie z instrukcjami [przypisać, sprawdź i rozwiąż problemy z licencjami](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artykułu.|
| Istnieje błąd podczas przetwarzania żądania. | Może to być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany przez awarię usług lub problem z konfiguracją. Jeśli zostanie wyświetlony ten błąd ma wpływ na firmy, skontaktuj się z pomocy technicznej firmy Microsoft, aby uzyskać dodatkową pomoc. |

## <a name="troubleshoot-the-password-reset-portal"></a>Rozwiązywanie problemów z portalu resetowania haseł

| Błąd | Rozwiązanie |
| --- | --- |
| Katalog nie jest włączona w celu resetowania haseł. | Przełącznik **włączyć samoobsługowe Resetowanie hasła** flaga **wybrane** lub **wszystkie** , a następnie wybierz **zapisać**. |
| Użytkownik nie ma usługi Azure AD Premium lub podstawowa przypisanej licencji. | Może to nastąpić, jeśli nie masz usługi Azure AD Premium lub podstawowa licencją administratorowi wykonywanie operacji. <br> <br> Ten problem można rozwiązać, po przypisaniu licencji do danego konta administratora. Postępuj zgodnie z instrukcjami [przypisać, sprawdź i rozwiąż problemy z licencjami](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artykułu. |
| Katalog jest włączona w celu resetowania haseł, ale użytkownik ma brakujące lub źle sformułowane informacje dotyczące uwierzytelniania. | Przed kontynuowaniem upewnij się, że użytkownik ma poprawnie sformułowany dane kontaktowe w pliku w katalogu. Aby uzyskać więcej informacji, zobacz [zresetować dane używane przez usługi Azure AD samoobsługi hasła](howto-sspr-authenticationdata.md). |
| Katalog jest włączona w celu resetowania haseł, ale użytkownik ma tylko jeden element danych, skontaktuj się z pomocą na plik, jeśli zasady zostały ustawione, aby wymagać dwie metody weryfikacji. | Przed kontynuowaniem upewnij się, że użytkownik ma co najmniej dwóch metod kontaktu poprawnie skonfigurowana. Przykład ma numer telefonu komórkowego *i* numeru telefonu do biura. |
| Katalog jest włączona do resetowania hasła użytkownika jest skonfigurowany prawidłowo, a użytkownik nie będzie mógł można skontaktować się z. | Może to wynikać z błędu tymczasowej usługi lub jest nieprawidłowe dane kontaktowe, który nie jest poprawnie wykryć. <br> <br> Jeśli użytkownik czeka 10 sekund, "spróbuj ponownie" i "Skontaktuj się z administratorem" łącza są wyświetlane. Jeśli użytkownik wybierze "spróbuj ponownie", ponowi próbę połączenia. Jeśli użytkownik wybierze "Skontaktuj się z administratorem", wysyła wiadomość e-mail z formularza do administratorów żądania resetowania hasła, aby przeprowadzić dla tego konta użytkownika. |
| Użytkownik nie otrzymuje resetowania hasła programu SMS lub połączeń telefonicznych. | Może to być wynikiem numeru telefonu utworzonym w katalogu. Upewnij się, że numer telefonu jest w formacie "+ ccc xxxyyyzzzzXeeee". <br> <br> Resetowanie hasła nie obsługuje rozszerzeń, nawet jeśli określono w katalogu. Wywołanie jest wysyłane rozszerzenia są usuwane. Użyj numeru bez rozszerzenia lub zintegrować rozszerzenia numeru telefonu w sieci prywatnej centrali (PBX). |
| Użytkownik nigdy nie odbiera wiadomości e-mail resetowania hasła. | Najczęstszą przyczyną tego problemu jest to, że komunikat zostanie odrzucony przez filtr spamu. Sprawdź Twojej wiadomości-śmieci, folder wiadomości-śmieci lub usuniętych elementów dla wiadomości e-mail. <br> <br> Upewnij się również sprawdzamy konta prawidłowy adres e-mail dla wiadomości. |
| Ustawię zasady resetowania hasła, ale podczas resetowania hasła korzysta z konta administratora, nie jest stosowane te zasady. | Firma Microsoft zarządza i formanty zresetować hasła administratora zasad w celu zapewnienia najwyższego poziomu zabezpieczeń. |
| Użytkownik będzie mógł próby resetowania zbyt wiele razy w ciągu dnia hasła. | Aby uniemożliwić użytkownikom wprowadzania mechanizm ograniczania przepustowości automatycznego z próba zresetowania hasła zbyt wiele razy w krótkim czasie. Ograniczanie występuje, gdy: <br><ul><li>Użytkownik próbuje zweryfikować numeru telefonu 5 razy w ciągu godziny.</li><li>Użytkownik próbuje użyć bramy pytania zabezpieczeń 5 razy w ciągu godziny.</li><li>Użytkownik próbuje zresetować hasło dla tego samego konta użytkownika 5 razy w ciągu godziny.</li></ul>Aby rozwiązać ten problem, poinstruuj użytkowników oczekiwania po 24 godzinach od ostatniej próby. Użytkownik może następnie zresetować swoje hasło. |
| Użytkownik zobaczy następujący błąd podczas sprawdzania poprawności numeru telefonu. | Ten błąd występuje, gdy podany numer telefonu jest niezgodny z numerem telefonu w pliku. Upewnij się, że użytkownik jest wprowadzenie pełnego numeru telefonu, kodu obszaru i kraju, w tym po podjęciu próby używa metody telefoniczną w celu resetowania haseł. |
| Istnieje błąd podczas przetwarzania żądania. | Może to być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany przez awarię usług lub problem z konfiguracją. Jeśli zostanie wyświetlony ten błąd ma wpływ na firmy, skontaktuj się z pomocy technicznej firmy Microsoft, aby uzyskać dodatkową pomoc. |

## <a name="troubleshoot-password-writeback"></a>Rozwiązywanie problemów z zapisywaniem zwrotnym haseł

| Błąd | Rozwiązanie |
| --- | --- |
| Usługa resetowania hasła nie zostanie uruchomiona lokalnymi. Błąd 6800 pojawia się w dzienniku zdarzeń aplikacji na komputerze Azure AD Connect. <br> <br> Po dołączania federacyjnym uwierzytelnianie przekazywane lub synchronizacji skrótu hasła użytkowników nie można zresetować hasła. | Po włączeniu funkcji zapisywania zwrotnego haseł, aparat synchronizacji wywołuje biblioteki zapisywania zwrotnego w celu przeprowadzenia konfiguracji (dołączania) komunikując się do dołączenia do usługi w chmurze. Błędów napotkanych podczas dołączania lub podczas uruchamiania usługi Windows Communication Foundation (WCF) punktu końcowego wyniki zapisywania zwrotnego haseł błędy w dzienniku zdarzeń na tym komputerze Azure AD Connect. <br> <br> Podczas ponownego uruchamiania usługi Azure AD Sync (synchronizacja) Jeśli skonfigurowano zapisywanie zwrotne, punktu końcowego WCF jest uruchamiany. Jednak jeśli uruchomienia punktu końcowego nie powiedzie się, firma Microsoft będzie rejestrowane zdarzenie 6800 i let uruchamiania usługi synchronizacji. Obecność to zdarzenie oznacza, czy punkt końcowy zapisywania zwrotnego haseł nie została uruchomiona w. Szczegóły dziennika zdarzeń dla tego zdarzenia 6800, wraz z dziennika zdarzeń, które wpisy wygenerować przez składnik PasswordResetService wskazywać, dlaczego nie można uruchomić się punkt końcowy. Przejrzyj te błędy dziennika zdarzeń i spróbuj ponownie uruchomić usługę Azure AD Connect, jeśli funkcji zapisywania zwrotnego haseł nie działa. Jeśli problem będzie się powtarzał, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.
| Gdy użytkownik próbuje zresetować hasło lub odblokować konto z włączoną zapisywania zwrotnego haseł, kończy się niepowodzeniem. <br> <br> Ponadto Zobacz zdarzenia w dzienniku zdarzeń programu Azure AD Connect, zawierający: "aparatu synchronizacji zwrócił błąd hr = 800700CE, komunikat = nazwa pliku lub rozszerzenie jest zbyt długa" po zakończeniu operacji odblokowania. | Znajdź konto usługi Active Directory programu Azure AD Connect i zresetuj hasło, tak aby zawierała nie więcej niż 127 znaków. Następnie otwórz **usługi synchronizacji** z **Start** menu. Przejdź do **łączniki** i Znajdź **łącznika usługi Active Directory**. Wybierz go, a następnie wybierz **właściwości**. Przejdź do **poświadczenia** strony i wprowadź nowe hasło. Wybierz **OK** aby zamknąć stronę. |
| W ostatnim kroku procesu instalacji usługi Azure AD Connect zostanie wyświetlony komunikat o błędzie informujący, że nie można skonfigurować tej funkcji zapisywania zwrotnego haseł. <br> <br> W dzienniku zdarzeń aplikacji Azure AD Connect zawiera błąd 32009 z tekstem "Błąd podczas pobierania uwierzytelniania tokenu." | Ten błąd występuje w dwóch następujących przypadków: <br><ul><li>Określono nieprawidłowe hasło dla konta administratora globalnego określony na początku procesu instalacji usługi Azure AD Connect.</li><li>Podjęto próbę użycia federacyjnych użytkownika dla konta administratora globalnego określony na początku procesu instalacji usługi Azure AD Connect.</li></ul> Aby rozwiązać ten problem, upewnij się, że nie używasz federacyjnych konta administratora globalnego określony na początku procesu instalacji. Upewnij się również, że określone hasło jest prawidłowe. |
| Dziennik zdarzeń programu Azure AD Connect maszyny zawiera błąd 32002 zgłaszany przez uruchomienie PasswordResetService. <br> <br> Odczytuje kod błędu: "łączenie błędu magistrali usług. Dostawca tokenu nie była w stanie tokenu zabezpieczającego. " | Nie można nawiązać połączenia z punktem końcowym usługi Azure Service Bus w chmurze jest w lokalnym środowisku. Przyczyną tego błędu jest zwykle reguła zapory blokuje połączenia wychodzące z określonego adresu portu lub sieci web. Zobacz [wymagania wstępne dotyczące łączności](./../connect/active-directory-aadconnect-prerequisites.md) Aby uzyskać więcej informacji. Po zaktualizowaniu tych reguł ponownego rozruchu komputera Azure AD Connect i zapisywania zwrotnego haseł należy uruchomić ponownie. |
| Po zakończeniu pracy przez pewien czas federacyjnym, uwierzytelnianie przekazywane lub synchronizacji skrótu hasła użytkowników nie można zresetować hasła. | W rzadkich przypadkach usługa zapisywania zwrotnego haseł może zakończyć się niepowodzeniem do ponownego uruchomienia po ponownym uruchomieniu usługi Azure AD Connect. W takich sytuacjach należy najpierw upewnij się, czy zapisywanie zwrotne haseł wydaje się być włączone lokalnymi. Można sprawdzić za pomocą Kreatora Azure AD Connect lub środowiska PowerShell (zobacz poprzednią sekcję HowTos). Jeśli funkcja wydaje się być włączone, spróbuj Włączanie lub wyłączanie funkcji ponownie przy użyciu interfejsu użytkownika lub środowiska PowerShell. Jeśli to nie zadziała, spróbuj pełnego odinstalowania i ponownej instalacji programu Azure AD Connect. |
| Federacyjnych, przekazywanego uwierzytelniania lub synchronizacji skrótów haseł użytkowników, którzy próba zresetowania hasła Zobacz błąd po próbie przesłać swoje hasło. Ten błąd oznacza, że wystąpił problem z usługi. <br ><br> Oprócz tego problemu podczas operacji resetowania hasła, można napotkać błąd czy agenta zarządzania odmówiono dostępu w dziennikach zdarzeń lokalnymi. | Jeśli te błędy w dzienniku zdarzeń, upewnij się, że konto Active Directory Management Agent (ADMA), który został określony w Kreatorze podczas konfiguracji ma niezbędne uprawnienia do zapisywania zwrotnego haseł. <br> <br> Należy pamiętać, że po tym pozwolono może potrwać maksymalnie jedną godzinę uprawnienia do ścieknie w dół za pośrednictwem `sdprop` zadania w tle na kontrolerze domeny (DC). <br> <br> Do resetowania hasła, aby pracować uprawnienie musi widnieć deskryptora zabezpieczeń obiektu użytkownika, resetowanie haseł, których. Dopóki te uprawnienia są wyświetlane na obiekt użytkownika, resetowanie hasła zakończy się niepowodzeniem z komunikat o odmowie dostępu. |
| Federacyjnych, przekazywanego uwierzytelniania lub synchronizacji skrótów haseł użytkowników, którzy próba zresetowania hasła, zobacz błąd po przesyłania hasła. Ten błąd oznacza, że wystąpił problem z usługi. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła, można napotkać błąd w dziennikach zdarzeń z usługi Azure AD Connect, co wskazuje na błąd "Nie można odnaleźć obiektu". | Błąd ten zwykle wskazuje, że aparat synchronizacji jest nie można odnaleźć obiektu użytkownika w przestrzeni łącznika usługi Azure AD lub połączonych metaverse (MV) lub obiekt miejsca łącznika usługi Azure AD. <br> <br> Aby rozwiązać ten problem, upewnij się, że użytkownik faktycznie są synchronizowane z lokalnej do usługi Azure AD za pomocą bieżącego wystąpienia programu Azure AD Connect i sprawdzić stan obiektów — obszary łączników i MV. Upewnij się, że obiekt usługi certyfikatów w usłudze Active Directory (AD CS) jest podłączony do obiektu MV za pomocą reguły "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federacyjnych, przekazywanego uwierzytelniania lub synchronizacji skrótów haseł użytkowników, którzy próba zresetowania hasła wyświetlony błąd, po ich przedstawia swoje hasło. Ten błąd oznacza, że wystąpił problem z usługi. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła, można napotkać błąd w dziennikach zdarzeń z usługi Azure AD Connect, który wskazuje na błąd "Znaleziono wiele dopasowań". | Oznacza to, że aparat synchronizacji wykrył, że obiektu MV jest podłączony do więcej niż jeden obiekt usług AD CS za pomocą "Microsoft.InfromADUserAccountEnabled.xxx". Oznacza to, że użytkownik ma włączone konto w więcej niż jednym lesie. Należy pamiętać, że w tym scenariuszu nie jest obsługiwana przez funkcję zapisywania zwrotnego haseł. |
| Operacje na hasłach zakończyć się niepowodzeniem z powodu błędu konfiguracji. Dziennik zdarzeń aplikacji zawiera błąd Azure AD Connect 6329 z tekstem "0x8023061f (operacja nie powiodła się, ponieważ synchronizacja hasła nie jest włączona dla tego agenta zarządzania)". | Ten błąd występuje w przypadku konfiguracji usługi Azure AD Connect można dodać nowego lasu usługi Active Directory (lub usuń i ponownie Dodaj istniejącego lasu) po funkcji zapisywania zwrotnego haseł została już włączona. Operacje na hasłach dla użytkowników w tych ostatnio dodane lasów kończyć się niepowodzeniem. Aby rozwiązać ten problem, wyłącz, a następnie ponownie włącz funkcję zapisywania zwrotnego haseł, po zakończeniu zmiany konfiguracji lasu. |

## <a name="password-writeback-event-log-error-codes"></a>Kody błędów dziennika zdarzeń funkcji zapisywania zwrotnego haseł

Najlepszym rozwiązaniem w przypadku Rozwiązywanie problemów z zapisywaniem zwrotnym haseł jest sprawdzić dziennik zdarzeń aplikacji na tym komputerze Azure AD Connect. Ten dziennik zdarzeń zawiera zdarzenia z dwóch źródeł istotnych dla funkcji zapisywania zwrotnego haseł. Źródło PasswordResetService opis operacji i problemy związane z operacji zapisywania zwrotnego haseł. Źródło ADSync opis operacji i problemy związane z ustawieniem hasła w środowisku usługi Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Jeśli źródło zdarzenia jest ADSync

| Kod | Nazwa lub wiadomości | Opis |
| --- | --- | --- |
| 6329 | PORĘCZENIE: MMS(4924) 0x80230619: "ograniczenie zabezpiecza hasła przed zmianami do bieżącej określony". | To zdarzenie występuje, gdy usługa zapisywania zwrotnego haseł próbuje ustawić hasło dla użytkownika katalogu lokalnego, który nie spełnia okres ważności hasła, historii, złożoności lub filtrowania wymagania domeny. <br> <br> Jeśli masz minimalny okres ważności hasła i niedawno zmieniono hasło w danym przedziale czasu, nie możesz zmienić hasło ponownie, dopóki nie osiągnie określony wiek w domenie. Do celów testowych, minimalnym wieku powinna być równa 0. <br> <br> Jeśli masz włączone wymagania historii hasła, a następnie należy wybrać hasło, które nie było używane w ciągu ostatnich *N* razy, gdzie *N* jest ustawienie historii haseł. Jeśli wybierzesz hasła, który został użyty w ciągu ostatnich *N* razy, a następnie w takim przypadku zostanie wyświetlony błąd. Do celów testowych, historii haseł powinna być równa 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła wszystkich z nich są wymuszane podczas próby zmiany lub resetowania hasła. <br> <br> Jeśli masz włączonych filtrów hasła i użytkownik wybierze hasło nie spełnia kryteriów filtrowania, a następnie resetowania lub operacji zmiany zakończy się niepowodzeniem. |
| 6329 | MMS(3040): admaexport.cpp(2837): serwer nie zawiera formantu zasad haseł LDAP. | Ten problem występuje, gdy formant LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) nie jest włączony na kontrolerów domeny. Aby użyć funkcji zapisywania zwrotnego haseł, należy włączyć formantu. Aby to zrobić, kontrolerów domeny musi być w systemie Windows Server 2008 (z najnowszą SP) lub nowszym. Jeśli Twoje kontrolery domeny są 2008 (pre-R2), a następnie należy również zastosować poprawkę [KB2386717](http://support.microsoft.com/kb/2386717). |
| HR 8023042 | Instalator aparatu synchronizacji zwrócił błąd hr = 80230402, wiadomość = próba pobrania obiektu nie powiodło się, ponieważ istnieją zduplikowane wpisy z tej samej kotwicy. | Ten błąd występuje, gdy ten sam identyfikator użytkownika jest włączona w wielu domenach. Przykładem jest, jeśli jest synchronizowania lasów kont i zasobów i mieć ten sam identyfikator użytkownika istnieje i jest włączona w każdym lesie. <br> <br> Ten błąd może również wystąpić, jeśli można używać atrybutu zakotwiczenia nie jest unikatowa, jak aliasu lub nazwy UPN i dwóch użytkowników udostępnianie tego samego atrybutu zakotwiczenia. <br> <br> Aby rozwiązać ten problem, upewnij się, że nie masz żadnych zduplikowanych użytkowników w ramach domeny i używać atrybutu zakotwiczenia unikatowy dla każdego użytkownika. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Jeśli źródło zdarzenia jest PasswordResetService

| Kod | Nazwa lub wiadomości | Opis |
| --- | --- | --- |
| 31001 | PasswordResetStart | To zdarzenie oznacza, że Usługa lokalna wykrył, że żądanie dla federacyjnych, przekazywanego uwierzytelniania lub synchronizacji skrótu hasła użytkownika, która pochodzi z chmury resetowania hasła. To zdarzenie jest pierwsze zdarzenie w każdej operacji zapisywania zwrotnego resetowania hasła. |
| 31002 | PasswordResetSuccess | To zdarzenie oznacza, że użytkownik wybrać nowe hasło podczas operacji resetowania hasła. Ustaliliśmy, że to hasło spełnia wymagania dotyczące haseł firmy. Hasło został pomyślnie zapisany wstecz do lokalnego środowiska usługi Active Directory. |
| 31003 | PasswordResetFail | To zdarzenie oznacza, że hasło wybrane przez użytkownika i hasło pomyślnie dotarły do środowiska lokalnego. Jednak gdy próbowano ustawić hasło w lokalnym środowisku usługi Active Directory wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełniają wieku, Historia, złożoności lub filtrowanie wymagań dla danej domeny. Aby rozwiązać ten problem, należy utworzyć nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takie jak grupy administratorów domeny lub przedsiębiorstwa, który nie zezwala na operacje na zestawie hasła.</li></ul>|
| 31004 | OnboardingEventStart | To zdarzenie występuje, gdy włączysz zapisywanie zwrotne haseł z usługi Azure AD Connect i firma Microsoft uruchomiony dołączania organizacji z usługą sieci web zapisywania zwrotnego haseł. |
| 31005 | OnboardingEventSuccess | To zdarzenie wskazuje, czy Proces dołączania przebiegło pomyślnie i czy funkcja zapisywania zwrotnego haseł jest gotowe do użycia. |
| 31006 | ChangePasswordStart | To zdarzenie oznacza, że Usługa lokalna nie wykryto żądanie zmiany hasła dla federacyjnych, przekazywanego uwierzytelniania lub synchronizacji skrótu hasła użytkownika, która pochodzi z chmury. To zdarzenie jest pierwsze zdarzenie w każdej operacji zapisywania zwrotnego zmiany hasła. |
| 31007 | ChangePasswordSuccess | To zdarzenie oznacza, że użytkownik wybrać nowe hasło podczas operacji zmiany hasła, Ustaliliśmy, że hasło spełnia wymagania dotyczące haseł firmy i że hasło został zapisany pomyślnie wstecz do lokalnego środowiska usługi Active Directory. |
| 31008 | ChangePasswordFail | To zdarzenie oznacza, że użytkownik wybrał hasła i że hasło pomyślnie dotarły do środowiska lokalnego, ale wystąpił błąd podczas próby ustawić hasło w lokalnym środowisku usługi Active Directory. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełniają wieku, Historia, złożoności lub filtrowanie wymagań dla danej domeny. Aby rozwiązać ten problem, należy utworzyć nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takich jak Administratorzy domeny lub przedsiębiorstwa, który nie zezwala na operacje na zestawie hasła.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Usługa lokalna wykrył, że żądanie dla federacyjnych, przekazywanego uwierzytelniania lub synchronizacji skrótu hasła użytkownika pochodzące od administratora w imieniu użytkownika resetowania hasła. To zdarzenie jest pierwsze zdarzenie w każdej operacji zapisywania zwrotnego resetowania hasła, inicjowanego przez administratora. |
| 31010 | ResetUserPasswordByAdminSuccess | Administrator wybrać nowe hasło podczas operacji resetowania hasła inicjowanych przez administratora. Ustaliliśmy, że to hasło spełnia wymagania dotyczące haseł firmy. Hasło został pomyślnie zapisany wstecz do lokalnego środowiska usługi Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Administrator wybrane hasło w imieniu użytkownika. Hasło pomyślnie dotarły do środowiska lokalnego. Jednak gdy próbowano ustawić hasło w lokalnym środowisku usługi Active Directory wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełniają wieku, Historia, złożoności lub filtrowanie wymagań dla danej domeny. Spróbuj nowe hasło, aby rozwiązać ten problem.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takich jak Administratorzy domeny lub przedsiębiorstwa, który nie zezwala na operacje na zestawie hasła.</li></ul>  |
| 31012 | OffboardingEventStart | To zdarzenie występuje, gdy należy wyłączyć funkcję zapisywania zwrotnego haseł z programem Azure AD Connect i wskazuje, że firma Microsoft uruchamiana zaawansowanej organizacji z usługą sieci web zapisywania zwrotnego haseł. |
| 31013| OffboardingEventSuccess| To zdarzenie oznacza, że proces zaawansowanej powiodła się i został pomyślnie wyłączony możliwości zapisywania zwrotnego haseł. |
| 31014| OffboardingEventFail| To zdarzenie wskazuje, czy Proces zaawansowanej zakończyła się niepowodzeniem. Może to być spowodowane błędem uprawnienia w chmurze lub lokalne konto administratora określone podczas konfigurowania. Ten błąd może również wystąpić, jeśli w przypadku próby użycia chmury federacyjnych administratora globalnego, gdy wyłączenie funkcji zapisywania zwrotnego haseł. Aby rozwiązać ten problem, sprawdź swoje uprawnienia administracyjne i upewnij się, że nie używasz kontem federacyjnym podczas konfigurowania możliwości zapisywania zwrotnego haseł.|
| 31015| WriteBackServiceStarted| To zdarzenie oznacza, że usługa zapisywania zwrotnego haseł została uruchomiona pomyślnie. Jest ona gotowa do akceptowania żądań zarządzania hasła z chmury.|
| 31016| WriteBackServiceStopped| To zdarzenie wskazuje, czy usługa zapisywania zwrotnego haseł została zatrzymana. Wszystkie żądania zarządzania haseł w chmurze nie powiedzie się.|
| 31017| AuthTokenSuccess| To zdarzenie oznacza, możemy pomyślnie pobrać token autoryzacji dla administratora globalnego określony podczas instalacji usługi Azure AD Connect, aby rozpocząć proces zaawansowanej lub dołączania.|
| 31018| KeyPairCreationSuccess| To zdarzenie oznacza pomyślnie utworzyliśmy klucz szyfrowania hasła. Ten klucz jest używany do szyfrowania haseł z chmury do wysłania do środowiska lokalnego.|
| 32000| Nieznany| To zdarzenie oznacza, że wystąpił nieznany błąd podczas operacji zarządzania hasła. Spójrz na tekst wyjątku w zdarzeniu, aby uzyskać więcej informacji. Jeśli masz problemy, spróbuj wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł. Jeśli to nie pomaga, dołączenie kopii dziennika zdarzeń oraz śledzenia niejawnego określony identyfikator do pracownika pomocy technicznej.|
| 32001| ServiceError| To zdarzenie oznacza, wystąpił błąd podczas łączenia z hasłem chmury resetowania usługi. Ten błąd występuje zazwyczaj, gdy Usługa lokalna nie może połączyć się z usługą sieci web resetowania hasła.|
| 32002| ServiceBusError| To zdarzenie oznacza, wystąpił błąd podczas nawiązywania połączenia z wystąpieniem usługi Service Bus swojej dzierżawy. Może to nastąpić, jeśli jest blokuje połączenia wychodzące w środowisku lokalnym. Sprawdź zaporę w taki sposób, aby upewnić się, że musisz zezwolić na połączenia za pośrednictwem protokołu TCP 443 i do https://ssprsbprodncu-sb.accesscontrol.windows.net/, a następnie spróbuj ponownie. Jeśli nadal występują problemy, spróbuj wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł.|
| 32003| InPutValidationError| To zdarzenie oznacza, że dane wejściowe przekazane do naszej interfejsu API usługi sieci web była nieprawidłowa. Spróbuj ponownie wykonać operację.|
| 32004| DecryptionError| To zdarzenie oznacza, że wystąpił błąd podczas odszyfrowywania hasła, które dotarły z chmury. Może to być spowodowane odszyfrowywania klucza niezgodność usługi w chmurze oraz w lokalnym środowisku. Aby rozwiązać ten problem, wyłącz i ponownie włączyć funkcję zapisywania zwrotnego haseł w środowisku lokalnym.|
| 32005| ConfigurationError| Podczas dołączania możemy zapisać informacje specyficzne dla dzierżawy w pliku konfiguracji w środowisku lokalnym. To zdarzenie oznacza, że wystąpił błąd podczas zapisywania tego pliku lub że gdy usługa została uruchomiona, wystąpił błąd podczas odczytywania pliku. Aby rozwiązać ten problem, spróbuj wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł, aby wymusić ponownego zapisywania w pliku konfiguracji.|
| 32007| OnBoardingConfigUpdateError| Podczas dołączania możemy wysyłać dane z chmury do lokalnej usługi resetowania hasła. Czy dane są następnie zapisywane do pliku w pamięci przed wysłaniem do usługi synchronizacji można bezpiecznie przechowywane na dysku. To zdarzenie oznacza, że istnieje problem z zapisywania lub aktualizowania danych w pamięci. Aby rozwiązać ten problem, spróbuj wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł, aby wymusić ponowne zapisywanie adresów tego pliku konfiguracji.|
| 32008| ValidationError| To zdarzenie oznacza, że odebrano nieprawidłową odpowiedź z usługi sieci web resetowania hasła. Aby rozwiązać ten problem, spróbuj wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł.|
| 32009| AuthTokenError| To zdarzenie oznacza, że nie możemy uzyskać autoryzacji tokenu dla konta administratora globalnego określone podczas konfigurowania usługi Azure AD Connect. Przyczyną tego błędu może być nieprawidłowa nazwa użytkownika lub hasło określone dla konta administratora globalnego. Ten błąd może również wystąpić, jeśli określone konto administratora globalnego jest federacyjna. Aby rozwiązać ten problem, uruchom ponownie konfigurację z prawidłową nazwę użytkownika i hasło i upewnij się, że administrator to konto zarządzane (tylko w chmurze lub synchronizacji haseł).|
| 32010| CryptoError| To zdarzenie oznacza, wystąpił błąd podczas generowania hasła klucza szyfrowania lub odszyfrowywania hasła przychodzący z usługi w chmurze. Ten błąd prawdopodobnie wskazuje na problem ze środowiskiem. Przeglądania informacji z dziennika zdarzeń, aby dowiedzieć się więcej na temat sposobu rozwiązania tego problemu. Można też spróbować wyłączenie i ponowne włączenie usługi zapisywania zwrotnego haseł.|
| 32011| OnBoardingServiceError| To zdarzenie oznacza, że Usługa lokalna prawidłowo nie można nawiązać połączenia z usługą sieci web resetowania hasła do zainicjowania procesu dołączania. Może się to zdarzyć w wyniku reguły zapory lub jeśli występuje problem podczas uzyskiwania uwierzytelnienia tokenu dla dzierżawy. Aby rozwiązać ten problem, upewnij się, czy możesz jest nie blokuje połączenia wychodzące przez TCP 443 i TCP 9350-9354 lub do https://ssprsbprodncu-sb.accesscontrol.windows.net/. Ponadto upewnij się, że konto administratora usługi Azure AD, używanego do dołączyć federacyjnych nie jest.|
| 32013| OffBoardingError| To zdarzenie oznacza, że Usługa lokalna prawidłowo nie można nawiązać połączenia z usługą sieci web resetowania hasła, aby zainicjować proces zaawansowanej. Może się to zdarzyć w wyniku reguły zapory lub jeśli występuje problem podczas pobierania tokenu do autoryzacji dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że nie blokuje połączeń wychodzących za pośrednictwem 443 lub do https://ssprsbprodncu-sb.accesscontrol.windows.net/, i że używasz offboard konto administratora usługi Azure Active Directory nie jest federacyjna.|
| 32014| ServiceBusWarning| To zdarzenie oznacza, Musieliśmy spróbuj ponownie nawiązać połączenia z wystąpieniem usługi Service Bus swojej dzierżawy. W normalnych warunkach to nie powinny być problemem, lecz jeśli widzisz to zdarzenie wiele razy, należy wziąć pod uwagę sprawdzić połączenie sieciowe z magistralą usług, zwłaszcza, jeśli jest dużymi opóźnieniami lub niskiej przepustowości połączenia.|
| 32015| ReportServiceHealthError| W celu monitorowania kondycji usługi zapisywania zwrotnego haseł, możemy wysłać pulsu dane do naszej usługi sieci web resetowania hasła co pięć minut. To zdarzenie oznacza, że wystąpił błąd podczas wysyłania informacji o tym kondycji usługi sieci web w chmurze. Te informacje o kondycji nie zawiera informacje umożliwiające identyfikację obiektów (OII) lub danych osobowych (dane osobowe) i jest całkowicie pulsu i statystyki podstawowe usługi, aby firma Microsoft może udostępnić informacje o stanie usługi w chmurze.|
| 33001| ADUnKnownError| To zdarzenie oznacza, że wystąpił nieznany błąd zwrócony przez usługę Active Directory. Sprawdź dziennik zdarzeń serwera Azure AD Connect dla zdarzeń ze źródła ADSync, aby uzyskać więcej informacji.|
| 33002| ADUserNotFoundError| To zdarzenie oznacza, że użytkownik, który próbuje Resetowanie lub zmienianie hasła nie odnaleziono w katalogu lokalnym. Ten błąd może wystąpić, gdy użytkownik został usunięty lokalnymi, ale nie w chmurze. Ten błąd może również wystąpić, jeśli występuje problem z synchronizacją. Sprawdź dzienniki synchronizacji i ostatniego kilka szczegółów synchronizacji, uruchom, aby uzyskać więcej informacji.|
| 33003| ADMutliMatchError| Podczas resetowania hasła lub żądania zmiany pochodzi z chmury, używamy zakotwiczenia chmury określony podczas instalacji programu Azure AD Connect do określania, jak połączyć tego żądania użytkownika w środowisku lokalnym. To zdarzenie oznacza, że znaleziono dwóch użytkowników w katalogu lokalnego z tego samego atrybutu zakotwiczenia chmury. Sprawdź dzienniki synchronizacji i ostatniego kilka szczegółów synchronizacji, uruchom, aby uzyskać więcej informacji.|
| 33004| ADPermissionsError| To zdarzenie oznacza, że konto usługi Active Directory Management Agent (ADMA) nie ma odpowiednie uprawnienia ustawione nowe hasło danego konta. Sprawdź, czy konto ADMA w lesie użytkownika zresetował i zmienić hasło uprawnienia do wszystkich obiektów w lesie. Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień, zobacz krok 4: Konfigurowanie odpowiednich uprawnień usługi Active Directory.|
| 33005| ADUserAccountDisabled| To zdarzenie oznacza, że firma Microsoft próbował zresetować lub zmienić hasło dla konta, które zostało wyłączone lokalnie. Włącz konto i spróbuj ponownie wykonać operację.|
| 33006| ADUserAccountLockedOut| To zdarzenie oznacza, że próby Resetowanie lub zmienianie hasła dla konta, które zostało zablokowane lokalnymi. Blokady może wystąpić, gdy użytkownik ma nastąpiła zmiana lub zresetować operacji hasła zbyt wiele razy w krótkim czasie. Odblokowanie konta i spróbuj ponownie wykonać operację.|
| 33007| ADUserIncorrectPassword| To zdarzenie wskazuje, czy określona przez użytkownika niepoprawnego bieżącego hasła podczas wykonywania hasło zmienić operację. Określ poprawny bieżące hasło i spróbuj ponownie.|
| 33008| ADPasswordPolicyError| To zdarzenie występuje, gdy usługa zapisywania zwrotnego haseł próbuje ustawić hasło dla użytkownika katalogu lokalnego, który nie spełnia okres ważności hasła, historii, złożoności lub filtrowania wymagania domeny. <br> <br> Jeśli masz minimalny okres ważności hasła i niedawno zmieniono hasło w danym przedziale czasu, nie możesz zmienić hasło ponownie, dopóki nie osiągnie określony wiek w domenie. Do celów testowych, minimalnym wieku powinna być równa 0. <br> <br> Jeśli masz włączone wymagania historii hasła, a następnie należy wybrać hasło, które nie było używane w ciągu ostatnich *N* razy, gdzie *N* jest ustawienie historii haseł. Jeśli wybierzesz hasła, który został użyty w ciągu ostatnich *N* razy, a następnie w takim przypadku zostanie wyświetlony błąd. Do celów testowych, historii haseł powinna być równa 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła wszystkich z nich są wymuszane podczas próby zmiany lub resetowania hasła. <br> <br> Jeśli masz włączonych filtrów hasła i użytkownik wybierze hasło nie spełnia kryteriów filtrowania, a następnie resetowania lub operacji zmiany zakończy się niepowodzeniem.|
| 33009| ADConfigurationError| To zdarzenie oznacza, że wystąpił pisania problem, hasła z powrotem do katalogu lokalnego ze względu na problem z konfiguracją z usługą Active Directory. Sprawdź dziennik zdarzeń aplikacji na komputerze Azure AD Connect komunikatów z usługi ADSync, aby uzyskać więcej informacji, na którym wystąpił błąd.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Rozwiązywanie problemów z łącznością zapisywania zwrotnego haseł

Jeśli wystąpią przerw w działaniu usługi za pomocą składnika zapisywania zwrotnego haseł programu Azure AD Connect, Oto niektóre Szybkie kroki, które należy wykonać, aby rozwiązać ten problem:

* [Upewnij się, połączenie sieciowe](#confirm-network-connectivity)
* [Uruchom ponownie usługę synchronizacji Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Wyłącz i ponownie włączyć funkcję zapisywania zwrotnego haseł](#disable-and-re-enable-the-password-writeback-feature)
* [Zainstalowanie najnowszej wersji Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](#troubleshoot-password-writeback)

Ogólnie rzecz biorąc, aby odzyskać usługę w ten sposób najszybszych, firma Microsoft zaleca wykonaj następujące kroki w kolejności omówionych wcześniej.

### <a name="confirm-network-connectivity"></a>Upewnij się, połączenie sieciowe

I czy Zapora jest najbardziej typowych punktu awarii lub porty serwera proxy i limitów czasu bezczynności są nieprawidłowo skonfigurowane. 

Azure AD Connect wersji 1.1.443.0 i nowszych możesz muszą HTTPS wychodzący dostęp do następujące:

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

Więcej szczegółowości odwoływać się zaktualizowaną listę [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) zaktualizować każdą środę i obowiązywać następujące od poniedziałku.

Aby uzyskać więcej informacji, Przejrzyj wymagania wstępne łączność w [wymagania wstępne dotyczące usługi Azure AD Connect](./../connect/active-directory-aadconnect-prerequisites.md) artykułu.



### <a name="restart-the-azure-ad-connect-sync-service"></a>Uruchom ponownie usługę synchronizacji Azure AD Connect

Aby rozwiązać problemy z łącznością lub innych przejściowych problemów z usługą, uruchom ponownie usługę synchronizacji Azure AD Connect:

   1. Jako administrator, wybierz **Start** na serwerze z systemem Azure AD Connect.
   2. Wprowadź **services.msc** w polu wyszukiwania i wybierz **Enter**.
   3. Wyszukaj **Microsoft Azure AD Sync** wpisu.
   4. Kliknij prawym przyciskiem myszy wpis usługi, wybierz **ponowne uruchomienie**, a następnie poczekaj na zakończenie operacji.

   ![Uruchom ponownie usługę Azure AD Sync][Service restart]

Te kroki ponownie ustanowić połączenia z usługą w chmurze i rozwiązać wszelkie przerw w działaniu może być problem. Jeśli ponowne uruchomienie usługi ADSync nie rozwiąże problemu, zaleca się próbie wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Wyłącz i ponownie włączyć funkcję zapisywania zwrotnego haseł

Aby rozwiązać problemy z łącznością, wyłącz i ponownie włączyć funkcję zapisywania zwrotnego haseł:

   1. Jako administrator otwórz Kreator konfiguracji platformy Azure AD Connect.
   2. W **nawiązywanie połączenia z usługą Azure AD**, wprowadź swoje poświadczenia administratora globalnego usługi Azure AD.
   3. W **łączenie z usługami AD DS**, wprowadź swoje poświadczenia administratora usług domenowych w usłudze AD.
   4. W **unikatowa identyfikacja użytkowników**, wybierz pozycję **dalej** przycisku.
   5. W **funkcje opcjonalne**, wyczyść **funkcji zapisywania zwrotnego haseł** pole wyboru.
   6. Wybierz **dalej** na pozostałych stronach okno dialogowe bez wprowadzania żadnych zmian, aż do **wszystko gotowe do skonfigurowania** strony.
   7. Upewnij się, że **wszystko gotowe do skonfigurowania strony** pokazuje **funkcji zapisywania zwrotnego haseł** opcję jako **wyłączone** , a następnie wybierz zielonego **Konfiguruj** Aby zatwierdzić zmiany.
   8. W **Zakończono**, wyczyść **Synchronizuj teraz** opcji, a następnie wybierz **Zakończ** aby zamknąć kreatora.
   9. Uruchom ponownie Kreatora konfiguracji platformy Azure AD Connect.
   10. Powtórz kroki 2-8, ale upewnij się, należy wybrać **funkcji zapisywania zwrotnego haseł** opcja **funkcje opcjonalne** stronę, aby ponownie włączyć usługę.

Te kroki ponownie ustanowić połączenia z usługą chmury i rozwiązać wszelkie przerw w działaniu może być problem.

Wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł nie rozwiąże problemu, zaleca się ponowne zainstalowanie usługi Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Zainstalowanie najnowszej wersji Azure AD Connect

Ponowna instalacja Azure AD Connect można rozwiązać, konfiguracji i problemy z połączeniem między naszych usług w chmurze i lokalnym środowisku usługi Active Directory.

Firma Microsoft zaleca wykonać ten krok tylko wtedy, gdy użytkownik podejmie pierwsze dwa kroki opisane wcześniej.

> [!WARNING]
> Jeśli dostosowano reguły synchronizacji poza pole *utworzyć ich kopię zapasową przed kontynuacją uaktualniania, a następnie ręcznie ponownie wdrożyć je po zakończeniu.*

   1. Pobierz najnowszą wersję programu Azure AD Connect z [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).
   2. Ponieważ użytkownik zainstalował już Azure AD Connect, musisz wykonać uaktualnienie w miejscu do aktualizacji instalacji programu Azure AD Connect do najnowszej wersji.
   3. Wykonaj pobranego pakietu i postępuj zgodnie z wyświetlanymi instrukcjami, aby zaktualizować komputer Azure AD Connect.

Poprzednie kroki należy ponownie ustanowić połączenie z naszej usługi w chmurze i rozwiązać wszelkie przerw w działaniu może być problem.

Jeśli instalowania najnowszej wersji serwera usługi Azure AD Connect nie rozwiąże problemu, zalecamy wypróbowanie wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł jako ostatni krok po zainstalowaniu najnowszej wersji.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Sprawdź, czy usługa Azure AD Connect ma wymagane uprawnienia do zapisywania zwrotnego haseł

Azure AD Connect wymaga usługi Active Directory **resetowania hasła** uprawnienia do wykonywania funkcji zapisywania zwrotnego haseł. Aby dowiedzieć się, jeśli Azure AD Connect ma wymagane uprawnienia dla lokalnych danego konta użytkownika usługi Active Directory, można użyć funkcji Windows czynne uprawnienia:

   1. Zaloguj się do serwera usługi Azure AD Connect i uruchom **Menedżera usługi synchronizacji** wybierając **Start** > **usługi synchronizacji**.
   2. W obszarze **łączniki** , a następnie wybierz lokalną **usług domenowych w usłudze Active Directory** łącznika, a następnie wybierz **właściwości**.  

   ![Czynne uprawnienia — krok 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
   3. W oknie podręcznym wybierz **Połącz z lasu usługi Active Directory** i zanotuj **nazwy użytkownika** właściwości. Ta właściwość jest konto usług AD DS, używane przez program Azure AD Connect w celu wykonania synchronizacji katalogów. Azure AD Connect wykonać funkcję zapisywania zwrotnego haseł konta usług AD DS musi zresetować hasło uprawnień.  
   
   ![Czynne uprawnienia — krok 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
   4. Zaloguj się do kontrolera domeny w lokalnej i uruchomić **użytkownicy usługi Active Directory i komputery** aplikacji.
   5. Wybierz **widoku** i upewnij się, że **funkcje zaawansowane** opcja jest włączona.  
   
   ![Czynne uprawnienia — krok 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
   6. Poszukaj konta użytkownika usługi Active Directory, który chcesz zweryfikować. Kliknij prawym przyciskiem myszy nazwę konta i wybierz **właściwości**.  
   
   ![Czynne uprawnienia — krok 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

   7. W oknie podręcznym wybierz pozycję **zabezpieczeń** i wybierz **zaawansowane**.  
   
   ![Czynne uprawnienia — krok 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
   8. W **Zaawansowane ustawienia zabezpieczeń dla administratora** okno podręczne, przejdź do **dostęp czynny** kartę.
   9. Wybierz **wybierz użytkownika**, wybierz konto usług AD DS używany przez usługę Azure AD Connect (zobacz krok 3), a następnie wybierz **Wyświetl dostęp czynny**.  
   
   ![Czynne uprawnienia - krok 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
   10. Przewiń w dół i poszukaj **resetowania hasła**. Jeśli zaznaczono pozycję konto usług AD DS nie ma uprawnień do resetowania hasła wybranego konta użytkownika usługi Active Directory.  
   
   ![Czynne uprawnienia - kroku 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fora o systemie Azure AD

Jeśli masz pytania ogólne dotyczące usługi Azure AD i Samoobsługowe Resetowanie hasła, poproś społeczność pomocy na [fora usługi Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Członkowie społeczności obejmują engineers, menedżerów produktu MVP i innymi specjalistów IT.

## <a name="contact-microsoft-support"></a>Skontaktuj się z pomocą techniczną firmy Microsoft

Jeśli nie możesz znaleźć odpowiedzi na problemy, nasze zespoły pomocy technicznej są zawsze dostępne ułatwi dalej.

Aby prawidłowo pomóc, poprosimy Podaj tak szczegółowe, jak to możliwe po otwarciu sprawy. Informacje te obejmują:

* **Ogólny opis błędu**: co to jest błąd? Jaki był zachowanie wykryto? Jak można odtworzyć błędu? Podaj tak szczegółowe, jak to możliwe.
* **Strona**: strony, jakie były na kiedy wystąpieniem błędu? To adres URL, jeśli będziesz mieć możliwość i zrzut ekranu strony.
* **Obsługuje kodu**: jaka była kod obsługi, który został wygenerowany, gdy użytkownik był wyświetlany błąd?
    * Aby znaleźć ten kod, występuje błąd, a następnie wybierz **obsługuje kodu** link w dolnej części ekranu i wysłać z pracownikiem pomocy technicznej identyfikator GUID, który daje.

    ![Znajdź kod obsługi w dolnej części ekranu][Support code]

    * Jeśli na stronie bez obsługi kodu na dole, wybierz F12 i wyszukaj identyfikator SID i CID i wysyłać te dwie specjaliście pomocy technicznej.
* **Datę, godzinę i strefę czasową**: obejmują dokładnej daty i godziny *ze strefą czasową* wystąpił błąd.
* **Identyfikator użytkownika**: kto został użytkownika, który był wyświetlany błąd? Na przykład *user@contoso.com*.
    * Jest to użytkownik federacyjny?
    * To jest uwierzytelnianie użytkownika?
    * To jest synchronizowane skrót hasła użytkownika?
    * Jest to użytkownik tylko w chmurze?
* **Licencjonowanie**: użytkownik ma przypisanej licencji usługi Azure AD Premium lub usługi Azure AD podstawowa?
* **Dziennik zdarzeń aplikacji**: Jeśli używasz funkcji zapisywania zwrotnego haseł, a błąd w infrastrukturze lokalnej, dołączenie zip kopii dziennika zdarzeń aplikacji z serwera usługi Azure AD Connect.



[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Uruchom ponownie usługę Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kod obsługi znajduje się w prawym dolnym rogu okna"

## <a name="next-steps"></a>Kolejne kroki

Poniższe artykuły zawierają dodatkowe informacje na temat resetowania za pośrednictwem usługi Azure AD:

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
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
