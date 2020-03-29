---
title: Rozwiązywanie problemów z samoobsługowym resetowaniem hasła — usługa Azure Active Directory
description: Rozwiązywanie problemów z resetowaniem hasła samoobsługowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22c0cc922e021edc37dfbb2d89fdd20c77b2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848769"
---
# <a name="troubleshoot-self-service-password-reset"></a>Troubleshoot self-service password reset (Rozwiązywanie problemów z samoobsługowym resetowaniem hasła)

Czy masz problem z samoobsługowym resetowaniem hasła usługi Azure Active Directory (Azure AD)? Następujące informacje mogą pomóc w ponownym sprawdnienie pracy.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Rozwiązywanie problemów z samoobsługowymi błędami resetowania hasła, które użytkownik może zobaczyć

| Błąd | Szczegóły | Szczegóły techniczne |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Przykro nam, że nie możesz zresetować hasła w tej chwili, ponieważ administrator wyłączył resetowanie hasła w organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać tę sytuację. Skontaktuj się z administratorem i poproś go o włączenie tej funkcji. Aby dowiedzieć się więcej, zobacz [Pomoc, nie pamiętam hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Wykryliśmy, że resetowanie hasła nie zostało włączone przez administratora. Skontaktuj się z administratorem i poproś go o włączenie resetowania hasła w organizacji. |
| WritebackNotEnabled = 10 |Przykro nam, że nie możesz zresetować hasła w tej chwili, ponieważ administrator nie włączył niezbędnej usługi dla Twojej organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać tę sytuację. Skontaktuj się z administratorem i poproś go o sprawdzenie konfiguracji organizacji. Aby dowiedzieć się więcej o tej niezbędnej usłudze, zobacz [Konfigurowanie storamentu hasła .](howto-sspr-writeback.md) | SSPR_0010: Wykryliśmy, że zapisywanie zwrotne hasła nie zostało włączone. Skontaktuj się z administratorem i poproś go o włączenie stornia hasła. |
| SsprNotEnabledInUserPolicy = 11 | Przykro nam, że nie możesz zresetować hasła w tej chwili, ponieważ administrator nie skonfigurował resetowania hasła w organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać tę sytuację. Skontaktuj się z administratorem i poproś go o skonfigurowanie resetowania hasła. Aby dowiedzieć się więcej o konfiguracji resetowania hasła, zobacz [Szybkie uruchamianie: samoobsługowe resetowanie hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Organizacja nie zdefiniowała zasad resetowania hasła. Skontaktuj się z administratorem i poproś go o zdefiniowanie zasad resetowania hasła. |
| Użytkownik Nielicencjonowany = 12 | Przykro nam, że nie możesz zresetować hasła w tej chwili, ponieważ w organizacji brakuje wymaganych licencji. Nie ma dalszych działań, które można podjąć, aby rozwiązać tę sytuację. Skontaktuj się z administratorem i poproś go o sprawdzenie przypisania licencji. Aby dowiedzieć się więcej o licencjonowaniu, zobacz [Wymagania licencyjne dotyczące samoobsługowego resetowania hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Twoja organizacja nie ma wymaganych licencji niezbędnych do resetowania hasła. Skontaktuj się z administratorem i poproś go o sprawdzenie przypisań licencji. |
| UserNotMemberOfScopedAccessGroup = 13 | Przykro nam, że nie możesz zresetować hasła w tej chwili, ponieważ administrator nie skonfigurował twojego konta do resetowania hasła. Nie ma dalszych działań, które można podjąć, aby rozwiązać tę sytuację. Skontaktuj się z administratorem i poproś go o skonfigurowanie konta do resetowania hasła. Aby dowiedzieć się więcej o konfiguracji konta w celu resetowania hasła, zobacz [Wdrażanie resetowania hasła dla użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Nie jesteś członkiem grupy włączonej do resetowania hasła. Skontaktuj się z administratorem i poproś o dodaniem do grupy. |
| UserNotProperlyConfigured = 14 | Przykro nam, że nie możesz zresetować hasła w tej chwili, ponieważ na twoim koncie brakuje niezbędnych informacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać tę sytuację. Skontaktuj się z administratorem i poproś go o zresetowanie hasła. Po ponownym dostępie do konta musisz zarejestrować niezbędne informacje. Aby zarejestrować informacje, wykonaj czynności opisane w artykule [Rejestrowanie samoobsługowego resetowania hasła.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) | SSPR_0014: Aby zresetować hasło, potrzebne są dodatkowe informacje zabezpieczające. Aby kontynuować, skontaktuj się z administratorem i poproś go o zresetowanie hasła. Po dokonaniu dostępu do konta można zarejestrować https://aka.ms/ssprsetupdodatkowe informacje zabezpieczające pod adresem . Administrator może dodać dodatkowe informacje zabezpieczające do konta, wykonując czynności opisane w [obszarze Ustawianie i odczytywanie danych uwierzytelniania w celu zresetowania hasła.](howto-sspr-authenticationdata.md) |
| Wymagane działania OnPremisesAdminAction = 29 | Przykro nam, że nie możemy zresetować hasła w tej chwili z powodu problemu z konfiguracją resetowania hasła w organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać tę sytuację. Skontaktuj się z administratorem i poproś go o zbadanie sprawy. Aby dowiedzieć się więcej o potencjalnym problemie, zobacz [Rozwiązywanie problemów z zapisywaniem hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Nie możemy zresetować hasła z powodu błędu w konfiguracji lokalnej. Skontaktuj się z administratorem i poproś go o zbadanie sprawy. |
| OnPremisesConnectivityError = 30 | Przykro nam, nie możemy zresetować hasła w tej chwili z powodu problemów z łącznością z Twoją organizacją. Nie ma żadnych działań do podjęcia w tej chwili, ale problem może zostać rozwiązany, jeśli spróbujesz ponownie później. Jeśli problem będzie się powtarzał, skontaktuj się z administratorem i poproś o zbadanie sprawy. Aby dowiedzieć się więcej o problemach z łącznością, zobacz [Rozwiązywanie problemów z łącznością podczas zapisywania haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Nie możemy zresetować hasła z powodu słabego połączenia ze środowiskiem lokalnym. Skontaktuj się z administratorem i poproś go o zbadanie sprawy.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Rozwiązywanie problemów z konfiguracją resetowania hasła w witrynie Azure Portal

| Błąd | Rozwiązanie |
| --- | --- |
| Nie widzę sekcji **Resetowanie hasła** w obszarze Usługi Azure AD w witrynie Azure portal. | Może się tak zdarzyć, jeśli nie masz licencji usługi Azure AD przypisane do administratora wykonującego operację. <br> <br> Przypisz licencję do danego konta administratora. Można wykonać kroki opisane w [artykule Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)|
| Nie widzę konkretnej opcji konfiguracji. | Wiele elementów interfejsu użytkownika są ukryte, dopóki nie są potrzebne. Spróbuj włączyć wszystkie opcje, które chcesz zobaczyć. |
| Nie widzę lokalnej **karty integracji.** | Ta opcja staje się widoczna tylko wtedy, gdy pobrano usługę Azure AD Connect i skonfigurowano zapisywanie zwrotne hasła. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure AD Connect przy użyciu ustawień ekspresowych](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Rozwiązywanie problemów z raportowaniem resetowania hasła

| Błąd | Rozwiązanie |
| --- | --- |
| Nie widzę żadnych typów działań związanych z zarządzaniem hasłami w kategorii zdarzenia inspekcji **samoobsługowego zarządzania hasłami.** | Może się tak zdarzyć, jeśli nie masz licencji usługi Azure AD przypisane do administratora wykonującego operację. <br> <br> Ten problem można rozwiązać, przypisując licencję do danego konta administratora. Postępuj zgodnie z instrukcjami w artykule [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| Rejestracje użytkowników są wyświetlane wiele razy. | Obecnie, gdy użytkownik rejestruje, rejestrujemy każdy pojedynczy fragment danych, który jest zarejestrowany jako oddzielne zdarzenie. <br> <br> Jeśli chcesz agregować te dane i mieć większą elastyczność w sposobie ich wyświetlania, możesz pobrać raport i otworzyć dane jako tabelę przestawną w programie Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Rozwiązywanie problemów z portalem rejestracji resetowania hasła

| Błąd | Rozwiązanie |
| --- | --- |
| Katalog nie jest włączony do resetowania hasła. **Administrator nie włączył korzystania z tej funkcji.** | Przełącz flagę **Włączone resetowanie hasła samoobsługowego** na **Zaznaczone** lub **Wszystkie,** a następnie wybierz pozycję **Zapisz**. |
| Użytkownik nie ma przypisanej licencji usługi Azure AD. **Administrator nie włączył korzystania z tej funkcji.** | Może się tak zdarzyć, jeśli nie masz licencji usługi Azure AD przypisane do administratora wykonującego operację. <br> <br> Ten problem można rozwiązać, przypisując licencję do danego konta administratora. Postępuj zgodnie z instrukcjami w artykule [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)|
| Wystąpił błąd przetwarzania żądania. | Może to być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany awarią usługi lub problem z konfiguracją. Jeśli ten błąd jest widoczny i dotyczy twojej firmy, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać dodatkową pomoc. |

## <a name="troubleshoot-the-password-reset-portal"></a>Rozwiązywanie problemów z portalem resetowania hasła

| Błąd | Rozwiązanie |
| --- | --- |
| Katalog nie jest włączony do resetowania hasła. | Przełącz flagę **Włączone resetowanie hasła samoobsługowego** na **Zaznaczone** lub **Wszystkie,** a następnie wybierz pozycję **Zapisz**. |
| Użytkownik nie ma przypisanej licencji usługi Azure AD. | Może się tak zdarzyć, jeśli nie masz licencji usługi Azure AD przypisane do administratora wykonującego operację. <br> <br> Ten problem można rozwiązać, jeśli do danego konta administratora zostanie przypisana licencja. Postępuj zgodnie z instrukcjami w artykule [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| Katalog jest włączony do resetowania hasła, ale użytkownik ma brakujące lub nieprawidłowo sformułowane informacje uwierzytelniania. | Przed kontynuowaniem upewnij się, że użytkownik prawidłowo uformował dane kontaktowe w pliku w katalogu. Aby uzyskać więcej informacji, zobacz [Dane używane przez samoobsługowe resetowanie hasła usługi Azure AD](howto-sspr-authenticationdata.md). |
| Katalog jest włączony do resetowania hasła, ale użytkownik ma tylko jeden fragment danych kontaktowych w pliku, gdy zasady są ustawione na wymaganie dwóch metod weryfikacji. | Przed kontynuowaniem upewnij się, że użytkownik ma co najmniej dwie prawidłowo skonfigurowane metody kontaktu. Przykładem jest posiadanie zarówno numeru telefonu komórkowego, jak *i* numeru telefonu biurowego. |
| Katalog jest włączony do resetowania hasła i użytkownik jest poprawnie skonfigurowany, ale nie można się z nim skontaktować. | Może to być wynikiem tymczasowego błędu usługi lub jeśli istnieją nieprawidłowe dane kontaktowe, których nie możemy poprawnie wykryć. <br> <br> Jeśli użytkownik odczeka 10 sekund, pojawią się linki "spróbuj ponownie" i "skontaktuj się z administratorem". Jeśli użytkownik wybierze opcję "spróbuj ponownie", ponawia próbę wywołania. Jeśli użytkownik wybierze opcję "skontaktuj się z administratorem", wyśle wiadomość e-mail do swoich administratorów z prośbą o zresetowanie hasła dla tego konta użytkownika. |
| Użytkownik nigdy nie odbiera wiadomości SMS lub telefonicznej resetowania hasła. | Może to być wynikiem nieprawidłowo sformułowanego numeru telefonu w katalogu. Upewnij się, że numer telefonu jest w formacie "+ccc xxxyyyyzzzzXeeee". <br> <br> Resetowanie hasła nie obsługuje rozszerzeń, nawet jeśli określisz ich w katalogu. Rozszerzenia są usuwane przed wywołaniem jest wywoływana. Użyj numeru bez rozszerzenia lub zintegruj rozszerzenie z numerem telefonu w prywatnej giełdzie oddziałów (PBX). |
| Użytkownik nigdy nie otrzymuje wiadomości e-mail z resetowaniem hasła. | Najczęstszą przyczyną tego problemu jest odrzucenie wiadomości przez filtr spamu. Sprawdź folder spamu, wiadomości-śmieci lub usuniętych elementów dla wiadomości e-mail. <br> <br> Upewnij się również, że sprawdzasz poprawne konto e-mail dla wiadomości. |
| Ustawiłem zasady resetowania hasła, ale gdy konto administratora używa resetowania hasła, ta zasada nie jest stosowana. | Firma Microsoft zarządza zasadami resetowania haseł administratora i kontroluje go, aby zapewnić najwyższy poziom zabezpieczeń. |
| Użytkownik nie może próbować resetować hasła zbyt wiele razy w ciągu dnia. | Zaimplementowaliśmy mechanizm automatycznego ograniczania przepustowości, aby uniemożliwić użytkownikom zbyt wiele razy zresetować hasła. Ograniczanie występuje, gdy: <br><ul><li>Użytkownik próbuje zweryfikować numer telefonu pięć razy w ciągu jednej godziny.</li><li>Użytkownik próbuje użyć bramy pytań zabezpieczających pięć razy w ciągu jednej godziny.</li><li>Użytkownik próbuje zresetować hasło dla tego samego konta użytkownika pięć razy w ciągu jednej godziny.</li></ul>Aby rozwiązać ten problem, poinstruuj użytkownika, aby odczekać 24 godziny po ostatniej próbie. Użytkownik może następnie zresetować swoje hasło. |
| Użytkownik widzi błąd podczas sprawdzania poprawności swojego numeru telefonu. | Ten błąd występuje, gdy wprowadzony numer telefonu nie jest zgodny z numerem telefonu w pliku. Upewnij się, że użytkownik wprowadza pełny numer telefonu, w tym numer obszaru i kraju, podczas próby użycia metody resetowania hasła w telefonie. |
| Wystąpił błąd przetwarzania żądania. | Może to być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany awarią usługi lub problem z konfiguracją. Jeśli ten błąd jest widoczny i dotyczy twojej firmy, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać dodatkową pomoc. |
| Naruszenie zasad lokalnych | Hasło nie spełnia lokalnych zasad haseł usługi Active Directory. |
| Hasło nie jest zgodne z zasadami rozmytymi | Hasło, które zostało [użyte,](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) pojawia się na liście zablokowanych haseł i nie może być używane. |

## <a name="troubleshoot-password-writeback"></a>Rozwiązywanie problemów z zapisywaniem haseł

| Błąd | Rozwiązanie |
| --- | --- |
| Usługa resetowania hasła nie uruchamia się lokalnie. Błąd 6800 pojawia się w dzienniku zdarzeń aplikacji komputera usługi Azure AD Connect. <br> <br> Po dołączaniu, federacyjne, uwierzytelniania przekazywanego lub haszowania hasła użytkownicy nie mogą zresetować swoich haseł. | Gdy funkcja zapisywania zwrotnego hasła jest włączona, aparat synchronizacji wywołuje bibliotekę stornowania, aby wykonać konfigurację (dołączanie) przez komunikację z usługą dołączania do chmury. Wszelkie błędy napotkane podczas dołączania lub podczas uruchamiania punktu końcowego programu Windows Communication Foundation (WCF) dla zapisywania zwrotnego hasła powoduje błędy w dzienniku zdarzeń na komputerze usługi Azure AD Connect. <br> <br> Podczas ponownego uruchamiania usługi Azure AD Sync (ADSync), jeśli stornowanie zostało skonfigurowane, punkt końcowy WCF uruchamia się. Ale jeśli uruchomienie punktu końcowego nie powiedzie się, zarejestrujemy zdarzenie 6800 i pozwolimy na uruchomienie usługi synchronizacji. Obecność tego zdarzenia oznacza, że punkt końcowy stornia hasła nie został uruchomiony. Szczegóły dziennika zdarzeń dla tego zdarzenia 6800, wraz z wpisami dziennika zdarzeń generowanych przez składnik PasswordResetService, wskazują, dlaczego nie można uruchomić punktu końcowego. Przejrzyj te błędy dziennika zdarzeń i spróbuj ponownie uruchomić usługę Azure AD Connect, jeśli zapisywanie zwrotne hasła nadal nie działa. Jeśli problem będzie się powtarzał, spróbuj wyłączyć, a następnie ponownie włączyć zapisywanie hasła.
| Gdy użytkownik próbuje zresetować hasło lub odblokować konto z włączonym zapisywaniem hasła, operacja kończy się niepowodzeniem. <br> <br> Ponadto w dzienniku zdarzeń usługi Azure AD Connect zostanie wyświetlenie zdarzenia zawierającego: "Aparat synchronizacji zwrócił błąd hr=800700CE, message=Nazwa pliku lub rozszerzenie jest zbyt długie" po wystąpieniu operacji odblokowania. | Znajdź konto usługi Active Directory dla usługi Azure AD Connect i zresetuj hasło, aby zawierało nie więcej niż 256 znaków. Następnie otwórz **usługę synchronizacji** z menu **Start.** Przejdź do **łączników** i znajdź **łącznik usługi Active Directory**. Zaznacz go, a następnie wybierz pozycję **Właściwości**. Przejdź do strony **Poświadczenia** i wprowadź nowe hasło. Wybierz **przycisk OK,** aby zamknąć stronę. |
| W ostatnim kroku procesu instalacji usługi Azure AD Connect zostanie wyświetlony błąd wskazujący, że nie można skonfigurować zapisywania zwrotnego hasła. <br> <br> Dziennik zdarzeń aplikacji usługi Azure AD Connect zawiera błąd 32009 z tekstem "Błąd podczas uzyskiwania tokenu omytu". | Ten błąd występuje w następujących dwóch przypadkach: <br><ul><li>Określono nieprawidłowe hasło dla konta administratora globalnego określonego na początku procesu instalacji usługi Azure AD Connect.</li><li>Podjęto próbę użycia użytkownika federacyjnego dla konta administratora globalnego określonego na początku procesu instalacji usługi Azure AD Connect.</li></ul> Aby rozwiązać ten problem, upewnij się, że nie używasz konta federacyjnego dla administratora globalnego określonego na początku procesu instalacji. Upewnij się również, że podane hasło jest poprawne. |
| Dziennik zdarzeń komputera usługi Azure AD Connect zawiera błąd 32002, który jest generowany przez uruchomienie PasswordResetService. <br> <br> Błąd brzmi: "Błąd podczas podłączania do usługi ServiceBus. Dostawca tokenu nie może dostarczyć tokenu zabezpieczającego." | Środowisko lokalne nie może połączyć się z punktem końcowym usługi Azure Service Bus w chmurze. Ten błąd jest zwykle spowodowany przez regułę zapory blokującą połączenie wychodzące z określonym portem lub adresem internetowym. Aby uzyskać więcej informacji, [zobacz Wymagania wstępne dotyczące łączności.](../hybrid/how-to-connect-install-prerequisites.md) Po zaktualizowaniu tych reguł uruchom ponownie komputer usługi Azure AD Connect, a ponowne rozpoczęcie zapisywania hasła powinno rozpocząć pracę ponownie. |
| Po pewnym czasie pracy użytkownicy federowani, uwierzytelniający przekazywany lub zsynchronizowany haszyszem haseł nie mogą zresetować swoich haseł. | W niektórych rzadkich przypadkach usługa zapisywania zwrotnego hasła może nie zostać ponownie uruchomiona po ponownym uruchomieniu usługi Azure AD Connect. W takich przypadkach najpierw sprawdź, czy zapisywanie zwrotne hasła wydaje się być włączone lokalnie. Można sprawdzić za pomocą kreatora usługi Azure AD Connect lub programu PowerShell (zobacz poprzednią sekcję HowTos). Jeśli funkcja wydaje się być włączona, spróbuj ponownie włączyć lub wyłączyć tę funkcję za pośrednictwem interfejsu użytkownika lub programu PowerShell. Jeśli to nie zadziała, spróbuj wykonać pełne odinstalowanie i ponowne zainstalowanie usługi Azure AD Connect. |
| Użytkownicy federowani, uwierzytelniający przekazujący lub zsynchronizowany haszyszem hasła, którzy próbują zresetować hasła, widzą błąd po próbie przesłania hasła. Błąd wskazuje, że wystąpił problem z usługą. <br ><br> Oprócz tego problemu podczas operacji resetowania hasła może pojawić się błąd, że agent zarządzania odmówiono dostępu w lokalnych dziennikach zdarzeń. | Jeśli te błędy są widoczne w dzienniku zdarzeń, upewnij się, że konto agenta zarządzania usługą Active Directory (ADMA) określone w kreatorze w czasie konfiguracji ma niezbędne uprawnienia do zapisywania haseł. <br> <br> Po podaniu tego uprawnienia może upłynąć do jednej godziny, aby `sdprop` uprawnienia spływały za pośrednictwem zadania w tle na kontrolerze domeny.After this permission is given, it can take up to one hour for the permissions to spływy down via the background task on the domain controller (DC). <br> <br> Aby resetowanie hasła działało, uprawnienie musi zostać ostemplowane na deskryptorze zabezpieczeń obiektu użytkownika, którego hasło jest resetowane. Dopóki to uprawnienie nie pojawi się na obiekcie użytkownika, resetowanie hasła nadal kończy się niepowodzeniem z komunikatem odmowy dostępu. |
| Użytkownicy federowani, uwierzytelniający przekazywany lub zsynchronizowany haszyszem hasła, którzy próbują zresetować swoje hasła, zobacz błąd po przesłaniu hasła. Błąd wskazuje, że wystąpił problem z usługą. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła może pojawić się błąd w dziennikach zdarzeń z usługi Azure AD Connect wskazujący błąd "Nie można odnaleźć obiektu". | Ten błąd zwykle wskazuje, że aparat synchronizacji nie może znaleźć obiektu użytkownika w przestrzeni łącznika usługi Azure AD lub połączonego obiektu metaverse (MV) lub obszaru spacji łącznika usługi Azure AD. <br> <br> Aby rozwiązać ten problem, upewnij się, że użytkownik jest rzeczywiście synchronizowane z lokalnego do usługi Azure AD za pośrednictwem bieżącego wystąpienia usługi Azure AD Connect i sprawdzić stan obiektów w przestrzeni łącznika i MV. Upewnij się, że obiekt Usług certyfikatów Active Directory (AD CS) jest połączony z obiektem MV za pomocą reguły "Microsoft.InfromADUserAccountEnabled.xxx".|
| Użytkownicy federowani, uwierzytelniający przekazywany lub zsynchronizowany haszyszem hasła, którzy próbują zresetować hasła, widzą błąd po przesłaniu hasła. Błąd wskazuje, że wystąpił problem z usługą. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła może pojawić się błąd w dziennikach zdarzeń z usługi Azure AD Connect, który wskazuje, że występuje błąd "Znaleziono wiele dopasowań". | Oznacza to, że aparat synchronizacji wykrył, że obiekt MV jest połączony z więcej niż jednym obiektem USŁUGI AD CS za pomocą "Microsoft.InfromADUserAccountEnabled.xxx". Oznacza to, że użytkownik ma włączone konto w więcej niż jednym lesie. Ten scenariusz nie jest obsługiwany dla stornuje hasło. |
| Operacje hasła nie powiodą się z błędem konfiguracji. Dziennik zdarzeń aplikacji zawiera błąd usługi Azure AD Connect 6329 z tekstem "0x8023061f (Operacja nie powiodła się, ponieważ synchronizacja haseł nie jest włączona w tym agencie zarządzania)". | Ten błąd występuje, jeśli konfiguracja usługi Azure AD Connect zostanie zmieniona w celu dodania nowego lasu usługi Active Directory (lub usunięcia i odczytania istniejącego lasu) po włączeniu funkcji zapisywania hasła. Operacje na hasłach dla użytkowników w tych niedawno dodanych lasach nie powiodą się. Aby rozwiązać ten problem, wyłącz, a następnie ponownie włącz funkcję zapisywania hasła po zakończeniu zmian konfiguracji lasu. |

## <a name="password-writeback-event-log-error-codes"></a>Kody błędów dziennika zdarzeń stortowania hasła

Najlepszym rozwiązaniem podczas rozwiązywania problemów z zapisywaniem hasła jest sprawdzenie dziennika zdarzeń aplikacji na komputerze usługi Azure AD Connect. Ten dziennik zdarzeń zawiera zdarzenia z dwóch źródeł zainteresowania dla stortowania hasła. Źródło PasswordResetService opisuje operacje i problemy związane z działaniem stornia hasła. Źródło usługi ADSync opisuje operacje i problemy związane z ustawianiem haseł w środowisku usługi Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Jeśli źródłem zdarzenia jest ADSync

| Code | Nazwa lub wiadomość | Opis |
| --- | --- | --- |
| 6329 | KAUCJA: MMS(4924) 0x80230619: "Ograniczenie zapobiega zmianie hasła na bieżące określone." | To zdarzenie występuje, gdy usługa stornia hasła próbuje ustawić hasło w katalogu lokalnym, które nie spełnia wymagań dotyczących wieku haseł, historii, złożoności lub filtrowania domeny. <br> <br> Jeśli masz minimalny wiek hasła i niedawno zmieniłeś hasło w tym przedziale czasu, nie możesz ponownie zmienić hasła, dopóki nie osiągnie określonego wieku w domenie. Do celów testowych minimalny wiek powinien być ustawiony na 0. <br> <br> Jeśli masz włączone wymagania dotyczące historii haseł, należy wybrać hasło, które nie było używane w ostatnich *n* razy, gdzie *N* jest ustawieniem historii haseł. Jeśli wybierzesz hasło, które zostało użyte w ostatnich *n* razy, a następnie pojawi się błąd w tym przypadku. Do celów testowych historia haseł powinna być ustawiona na 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik próbuje zmienić lub zresetować hasło. <br> <br> Jeśli masz włączone filtry haseł, a użytkownik wybierze hasło, które nie spełnia kryteriów filtrowania, operacja resetowania lub zmiany nie powiedzie się. |
| 6329 | MMS(3040): admaexport.cpp(2837): Serwer nie zawiera kontroli zasad haseł LDAP. | Ten problem występuje, jeśli LDAP_SERVER_POLICY_HINTS_OID control (1.2.840.113556.1.4.2066) nie jest włączona na kontrolerach domeny. Aby użyć funkcji stornia hasła, należy włączyć formant. Aby to zrobić, kontrolery domeny muszą znajdować się w systemie Windows Server 2008R2 lub nowszym. |
| HR 8023042 | Aparat synchronizacji zwrócił błąd hr=80230402, message=Próba uzyskania obiektu nie powiodła się, ponieważ istnieją zduplikowane wpisy z tą samą kotwicą. | Ten błąd występuje, gdy ten sam identyfikator użytkownika jest włączony w wielu domenach. Przykładem jest, jeśli synchronizujesz konta i lasy zasobów i masz ten sam identyfikator użytkownika obecny i włączony w każdym lesie. <br> <br> Ten błąd może również wystąpić, jeśli używasz nieunikatowego atrybutu kotwicy, takiego jak alias lub nazwa UPN, a dwóch użytkowników współużytkuje ten sam atrybut zakotwiczenia. <br> <br> Aby rozwiązać ten problem, upewnij się, że nie masz żadnych zduplikowanych użytkowników w domenach i że używasz unikatowego atrybutu kotwicy dla każdego użytkownika. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Jeśli źródłem zdarzenia jest PasswordResetService

| Code | Nazwa lub wiadomość | Opis |
| --- | --- | --- |
| 31001 | Początek zestawu haseł | To zdarzenie wskazuje, że usługa lokalna wykryła żądanie resetowania hasła dla użytkownika federacyjnego, uwierzytelniania przekazywanego lub synchronizowanego hasłem, który pochodzi z chmury. To zdarzenie jest pierwszym zdarzeniem w każdej operacji resetowania hasła stornia. |
| 31002 | Sukces HasResetSuccess | To zdarzenie wskazuje, że użytkownik wybrał nowe hasło podczas operacji resetowania hasła. Ustaliliśmy, że to hasło spełnia wymagania dotyczące haseł firmowych. Hasło zostało pomyślnie zapisane w lokalnym środowisku usługi Active Directory. |
| 31003 | HasłoResetFail | To zdarzenie wskazuje, że użytkownik wybrał hasło i hasło dotarło pomyślnie do środowiska lokalnego. Ale gdy próbowaliśmy ustawić hasło w lokalnym środowisku usługi Active Directory, wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności ani filtrowania domeny. Aby rozwiązać ten problem, utwórz nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień do ustawiania nowego hasła na danym koncie użytkownika.</li><li>Konto użytkownika znajduje się w grupie chronionej, takiej jak domena lub grupa administracyjna przedsiębiorstwa, która nie zezwala na operacje ustawiania haseł.</li></ul>|
| 31004 | Początek dołączania | To zdarzenie występuje, jeśli włączysz zapisywanie zwrotne haseł za pomocą usługi Azure AD Connect i rozpoczęliśmy dołączanie organizacji do usługi sieci web stornowania hasła. |
| 31005 | Sukces onboardingusuccess | To zdarzenie wskazuje, że proces dołączania zakończył się pomyślnie i że funkcja zapisywania zwrotnego hasła jest gotowa do użycia. |
| 31006 | ZmianaPaswordStart | To zdarzenie wskazuje, że usługa lokalna wykryła żądanie zmiany hasła dla użytkownika federacyjnego, uwierzytelniania przekazywanego lub synchronizowanego hasłem, który pochodzi z chmury. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zmiany hasła stornia. |
| 31007 | ZmianaPaswordSukces | To zdarzenie wskazuje, że użytkownik wybrał nowe hasło podczas operacji zmiany hasła, ustaliliśmy, że hasło spełnia wymagania dotyczące haseł firmowych i że hasło zostało pomyślnie zapisane z powrotem do lokalnego środowiska usługi Active Directory. |
| 31008 | Zmień Hasło | To zdarzenie wskazuje, że użytkownik wybrał hasło i że hasło dotarło pomyślnie do środowiska lokalnego, ale gdy próbowaliśmy ustawić hasło w lokalnym środowisku usługi Active Directory, wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności ani filtrowania domeny. Aby rozwiązać ten problem, utwórz nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień do ustawiania nowego hasła na danym koncie użytkownika.</li><li>Konto użytkownika znajduje się w grupie chronionej, takiej jak administratorzy domeny lub przedsiębiorstwa, co nie zezwala na operacje ustawiania haseł.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Usługa lokalna wykryła żądanie resetowania hasła dla użytkownika federacyjnego, uwierzytelniania przekazywanego lub synchronizowanego hasłem, pochodzącego od administratora w imieniu użytkownika. To zdarzenie jest pierwszym zdarzeniem w każdej operacji resetowania hasła, która jest inicjowana przez administratora. |
| 31010 | ResetUserPasswordByAdsukces | Administrator wybrał nowe hasło podczas operacji resetowania hasła zainicjowanego przez administratora. Ustaliliśmy, że to hasło spełnia wymagania dotyczące haseł firmowych. Hasło zostało pomyślnie zapisane w lokalnym środowisku usługi Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Administrator wybrał hasło w imieniu użytkownika. Hasło dotarło pomyślnie do środowiska lokalnego. Ale gdy próbowaliśmy ustawić hasło w lokalnym środowisku usługi Active Directory, wystąpił błąd. Ten błąd może się zdarzyć z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności ani filtrowania domeny. Spróbuj użyć nowego hasła, aby rozwiązać ten problem.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień do ustawiania nowego hasła na danym koncie użytkownika.</li><li>Konto użytkownika znajduje się w grupie chronionej, takiej jak administratorzy domeny lub przedsiębiorstwa, co nie zezwala na operacje ustawiania haseł.</li></ul>  |
| 31012 | OffboardingEventStart | To zdarzenie występuje, jeśli wyłączysz zapisywanie haseł za pomocą usługi Azure AD Connect i wskazuje, że rozpoczęliśmy odkwaliwanie organizacji do usługi sieci web stornowania hasła. |
| 31013| OffboardingEventSuccess| To zdarzenie wskazuje, że proces wyłączania zakończył się pomyślnie i że funkcja zapisywania zwrotnego hasła została pomyślnie wyłączona. |
| 31014| OffboardingEventFail (Niefedlić)| To zdarzenie wskazuje, że proces wyłączania nie zakończył się pomyślnie. Może to być spowodowane błędem uprawnień na koncie administratora w chmurze lub lokalnym koncie administratora określonym podczas konfiguracji. Błąd może również wystąpić, jeśli próbujesz użyć administratora globalnego chmury federacyjnej podczas wyłączania stornowania hasła. Aby rozwiązać ten problem, sprawdź uprawnienia administracyjne i upewnij się, że nie używasz konta federacyjnego podczas konfigurowania funkcji zapisywania zwrotnego hasła.|
| 31015| WriteBackServiceStarted (Początek usługi WriteBackService| To zdarzenie wskazuje, że usługa stornia hasła została pomyślnie uruchomiona. Jest gotowa do akceptowania żądań zarządzania hasłami z chmury.|
| 31016| WriteBackServiceStopped| To zdarzenie wskazuje, że usługa stornia hasła została zatrzymana. Wszelkie żądania zarządzania hasłami z chmury nie zostaną uwzględnione.|
| 31017| AuthTokenSukces| To zdarzenie wskazuje, że pomyślnie pobrano token autoryzacji dla administratora globalnego określonego podczas konfigurowania usługi Azure AD Connect, aby rozpocząć proces odkładania lub dołączania.|
| 31018| KeyPairCreationSukces| To zdarzenie wskazuje, że pomyślnie utworzyliśmy klucz szyfrowania hasła. Ten klucz służy do szyfrowania haseł z chmury, które mają być wysyłane do środowiska lokalnego.|
| 32000| NieznanyError| To zdarzenie wskazuje, że wystąpił nieznany błąd podczas operacji zarządzania hasłami. Aby uzyskać więcej informacji, zapoznaj się z tekstem wyjątku w zdarzeniu. Jeśli masz problemy, spróbuj wyłączyć, a następnie ponownie włączyć zapisywanie haseł. Jeśli to nie pomoże, dołącz kopię dziennika zdarzeń wraz z identyfikatorem śledzenia określonym poufnych do inżyniera pomocy technicznej.|
| 32001| SerwisError| To zdarzenie wskazuje, że wystąpił błąd podczas łączenia się z usługą resetowania hasła w chmurze. Ten błąd zazwyczaj występuje, gdy usługa lokalna nie może połączyć się z usługą sieci web resetowania hasła.|
| 32002| UsługaBusError| To zdarzenie wskazuje, że wystąpił błąd podczas łączenia się z wystąpieniem usługi Service Bus dzierżawy. Może się tak zdarzyć, jeśli blokujesz połączenia wychodzące w środowisku lokalnym. Sprawdź zaporę, aby upewnić się, że zezwalasz https://ssprdedicatedsbprodncu.servicebus.windows.netna połączenia za pomocą protokołu TCP 443 i do programu , a następnie spróbuj ponownie. Jeśli nadal występują problemy, spróbuj wyłączyć, a następnie ponownie włączyć zapisywanie hasła.|
| 32003| InPutValidationError| To zdarzenie wskazuje, że dane wejściowe przekazane do naszego interfejsu API usługi sieci web były nieprawidłowe. Spróbuj ponownie wykonać operację.|
| 32004| DescryptionError| To zdarzenie wskazuje, że wystąpił błąd odszyfrowywania hasła, które dotarło z chmury. Może to być spowodowane niezgodnością klucza odszyfrowywania między usługą w chmurze a środowiskiem lokalnym. Aby rozwiązać ten problem, należy wyłączyć, a następnie ponownie włączyć zapisywanie haseł w środowisku lokalnym.|
| 32005| KonfiguracjaError| Podczas dołączania zapisujemy informacje specyficzne dla dzierżawy w pliku konfiguracyjnym w środowisku lokalnym. To zdarzenie wskazuje, że wystąpił błąd podczas zapisywania tego pliku lub że po uruchomieniu usługi wystąpił błąd podczas odczytu pliku. Aby rozwiązać ten problem, spróbuj wyłączyć, a następnie ponownie włączyć zapisywanie hasła, aby wymusić przepisanie pliku konfiguracyjnego.|
| 32007| OnBoardingConfigUpdateError| Podczas dołączania wysyłamy dane z chmury do lokalnej usługi resetowania haseł. Dane te są następnie zapisywane w pliku w pamięci przed wysłaniem ich do usługi synchronizacji w celu bezpiecznego przechowywania na dysku. To zdarzenie wskazuje, że występuje problem z zapisywaniem lub aktualizowaniem tych danych w pamięci. Aby rozwiązać ten problem, spróbuj wyłączyć, a następnie ponownie włączyć zapisywanie hasła, aby wymusić przepisanie tego pliku konfiguracyjnego.|
| 32008| Validationerror| To zdarzenie wskazuje, że otrzymaliśmy nieprawidłową odpowiedź z usługi sieci web resetowania hasła. Aby rozwiązać ten problem, spróbuj wyłączyć, a następnie ponownie włączyć zapisywanie hasła.|
| 32009| AuthTokenError (AuthTokenError)| To zdarzenie wskazuje, że nie możemy uzyskać tokenu autoryzacji dla konta administratora globalnego określonego podczas konfigurowania usługi Azure AD Connect. Ten błąd może być spowodowany przez złą nazwę użytkownika lub hasło określone dla globalnego konta administratora. Ten błąd może również wystąpić, jeśli określone konto administratora globalnego jest sfederowane. Aby rozwiązać ten problem, uruchom ponownie konfigurację przy użyciu poprawnej nazwy użytkownika i hasła i upewnij się, że administrator jest zarządzanym kontem (tylko w chmurze lub zsynchronizowanym hasłem).|
| 32010| CryptoError ( CryptoError )| To zdarzenie wskazuje, że wystąpił błąd podczas generowania klucza szyfrowania hasła lub odszyfrowywania hasła, które pochodzi z usługi w chmurze. Ten błąd prawdopodobnie wskazuje na problem ze środowiskiem. Zapoznaj się ze szczegółami dziennika zdarzeń, aby dowiedzieć się więcej o tym, jak rozwiązać ten problem. Można również spróbować wyłączyć, a następnie ponownie włączyć usługę zapisywania zwrotnego hasła.|
| 32011| Usługa OnBoardingError| To zdarzenie wskazuje, że usługa lokalna nie może poprawnie komunikować się z usługą sieci web resetowania hasła w celu zainicjowania procesu dołączania. Może się to zdarzyć w wyniku reguły zapory lub jeśli występuje problem z uzyskaniem tokenu uwierzytelniania dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że nie blokujesz połączeń wychodzących za 100 000 TCP 443 i TCP 9350-9354 lub . https://ssprdedicatedsbprodncu.servicebus.windows.net Upewnij się również, że konto administratora usługi Azure AD, którego używasz na pokładzie, nie jest sfederowane.|
| 32013| OffBoardingError| To zdarzenie wskazuje, że usługa lokalna nie może poprawnie komunikować się z usługą sieci web resetowania hasła w celu zainicjowania procesu wyłączania. Może się to zdarzyć w wyniku reguły zapory lub jeśli występuje problem z uzyskaniem tokenu autoryzacji dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że nie blokujesz połączeń https://ssprdedicatedsbprodncu.servicebus.windows.netwychodzących w ciągu 443 lub do programu , a konto administratora usługi Azure Active Directory, którego używasz poza burtą, nie jest sfederowane.|
| 32014| ServiceBusWarning (Usługi AutobusoweOwapowanie)| To zdarzenie wskazuje, że musieliśmy ponowić próbę połączenia z wystąpieniem usługi Service Bus dzierżawy. W normalnych warunkach nie powinno to być problemem, ale jeśli zobaczysz to zdarzenie wiele razy, należy rozważyć sprawdzenie połączenia sieciowego z usługą Service Bus, zwłaszcza jeśli jest to połączenie o dużym opóźnieniu lub niskiej przepustowości.|
| 32015| ReportServiceHealthError| Aby monitorować kondycję usługi zapisywania zwrotnego hasła, co pięć minut wysyłamy dane pulsu do naszej usługi resetowania haseł. To zdarzenie wskazuje, że wystąpił błąd podczas wysyłania tych informacji o kondycji z powrotem do usługi sieci web w chmurze. Te informacje o kondycji nie zawierają danych umożliwiających identyfikację obiektu (OII) ani danych umożliwiających identyfikację użytkownika i są wyłącznie biciem serca i podstawowymi statystykami usług, dzięki czemu możemy dostarczać informacje o stanie usługi w chmurze.|
| 33001| ADUnKnownError| To zdarzenie wskazuje, że wystąpił nieznany błąd zwrócony przez usługi Active Directory. Sprawdź dziennik zdarzeń serwera usługi Azure AD Connect pod kątem zdarzeń ze źródła usługi ADSync, aby uzyskać więcej informacji.|
| 33002| AdUserNotFoundError| To zdarzenie wskazuje, że użytkownik, który próbuje zresetować lub zmienić hasło nie został znaleziony w katalogu lokalnym. Ten błąd może wystąpić, gdy użytkownik został usunięty lokalnie, ale nie w chmurze. Ten błąd może również wystąpić, jeśli występuje problem z synchronizacją. Sprawdź dzienniki synchronizacji i kilka ostatnich szczegółów przebiegu synchronizacji, aby uzyskać więcej informacji.|
| 33003| ADMutliMatchError| Gdy żądanie resetowania hasła lub zmiany pochodzi z chmury, używamy kotwicy chmury określonej podczas procesu instalacji usługi Azure AD Connect, aby określić sposób łączenia tego żądania z użytkownikiem w środowisku lokalnym. To zdarzenie wskazuje, że firma Wenauj dwóch użytkowników w katalogu lokalnym z tym samym atrybutem zakotwiczenia w chmurze. Sprawdź dzienniki synchronizacji i kilka ostatnich szczegółów przebiegu synchronizacji, aby uzyskać więcej informacji.|
| 33004| AdPermissionsError| To zdarzenie wskazuje, że konto usługi Agent zarządzania usługą Active Directory (ADMA) nie ma odpowiednich uprawnień do danego konta do ustawienia nowego hasła. Upewnij się, że konto ADMA w lesie użytkownika zresetowało i zmieniło uprawnienia do haseł do wszystkich obiektów w lesie. Aby uzyskać więcej informacji na temat ustawiania uprawnień, zobacz Krok 4: Konfigurowanie odpowiednich uprawnień usługi Active Directory. Ten błąd może również wystąpić, gdy atrybut użytkownika AdminCount jest ustawiony na 1.|
| 33005| AdUserAccountDisabled (AdUserAccountDisabled)| To zdarzenie wskazuje, że próbowano zresetować lub zmienić hasło dla konta, które zostało wyłączone lokalnie. Włącz konto i spróbuj ponownie wykonać operację.|
| 33006| AdUserAccountLockedOut| To zdarzenie wskazuje, że próbowaliśmy zresetować lub zmienić hasło dla konta, które zostało zablokowane lokalnie. Blokady mogą wystąpić, gdy użytkownik próbował zmienić lub zresetować operację hasła zbyt wiele razy w krótkim okresie. Odblokuj konto i spróbuj ponownie wykonać operację.|
| 33007| ADUserIncorrectPassword| To zdarzenie wskazuje, że użytkownik określił niepoprawne bieżące hasło podczas wykonywania operacji zmiany hasła. Określ poprawne bieżące hasło i spróbuj ponownie.|
| 33008| ADPasswordPolicyError| To zdarzenie występuje, gdy usługa stornia hasła próbuje ustawić hasło w katalogu lokalnym, które nie spełnia wymagań dotyczących wieku haseł, historii, złożoności lub filtrowania domeny. <br> <br> Jeśli masz minimalny wiek hasła i niedawno zmieniłeś hasło w tym przedziale czasu, nie możesz ponownie zmienić hasła, dopóki nie osiągnie określonego wieku w domenie. Do celów testowych minimalny wiek powinien być ustawiony na 0. <br> <br> Jeśli masz włączone wymagania dotyczące historii haseł, należy wybrać hasło, które nie było używane w ostatnich *n* razy, gdzie *N* jest ustawieniem historii haseł. Jeśli wybierzesz hasło, które zostało użyte w ostatnich *n* razy, a następnie pojawi się błąd w tym przypadku. Do celów testowych historia haseł powinna być ustawiona na 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik próbuje zmienić lub zresetować hasło. <br> <br> Jeśli masz włączone filtry haseł, a użytkownik wybierze hasło, które nie spełnia kryteriów filtrowania, operacja resetowania lub zmiany nie powiedzie się.|
| 33009| AdConfigurationError| To zdarzenie wskazuje, że wystąpił problem z zapisywaniem hasła z powrotem do katalogu lokalnego z powodu problemu z konfiguracją usługi Active Directory. Sprawdź dziennik zdarzeń aplikacji komputera usługi Azure AD Connect, aby uzyskać informacje z usługi ADSync, aby uzyskać więcej informacji na temat wystąpienia błędu.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Rozwiązywanie problemów z łącznością zapisywania zwrotnego haseł

Jeśli występują przerwy w świadczeniu usług za pomocą składnika stornowania hasła usługi Azure AD Connect, oto kilka szybkich kroków, które można wykonać, aby rozwiązać ten problem:

* [Potwierdzanie łączności sieciowej](#confirm-network-connectivity)
* [Ponowne uruchamianie usługi Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Wyłączanie i ponowne włączanie funkcji zapisywania zwrotnego hasła](#disable-and-re-enable-the-password-writeback-feature)
* [Instalowanie najnowszej wersji usługi Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Rozwiązywanie problemów z zapisywaniem haseł](#troubleshoot-password-writeback)

Ogólnie rzecz biorąc, aby odzyskać usługę w najbardziej szybki sposób, zaleca się wykonanie tych kroków w kolejności omówione wcześniej.

### <a name="confirm-network-connectivity"></a>Potwierdzanie łączności sieciowej

Najczęstszym punktem awarii jest niepoprawna konfiguracja portów zapory i lub serwera proxy oraz limitów czasu bezczynności. 

W przypadku usługi Azure AD Connect w wersji 1.1.443.0 lub wyższej, musisz wychodzący dostęp HTTPS do następujących czynności:

* \*passwordreset.microsoftonline.com .passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Aby uzyskać bardziej szczegółowość, odwołaj się do zaktualizowanej listy [zakresów adresów IP centrum danych platformy Microsoft Azure aktualizowanych](https://www.microsoft.com/download/details.aspx?id=41653) w każdą środę i wprowadzanych w życie w następny poniedziałek.

Aby uzyskać więcej informacji, zapoznaj się z wymaganiami wstępnymi łączności w [artykule Wymagania wstępne dotyczące usługi Azure AD Connect.](../hybrid/how-to-connect-install-prerequisites.md)

> [!NOTE]
> Samooprząsk może również zakończyć się niepowodzeniem, jeśli ustawienia "Hasło nigdy nie wygasa" lub "Użytkownik nie może zmienić hasła" są skonfigurowane na koncie w usługach AD DS w środowisku lokalnym. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Ponowne uruchamianie usługi Azure AD Connect Sync

Aby rozwiązać problemy z łącznością lub inne przejściowe problemy z usługą, uruchom ponownie usługę Azure AD Connect Sync:

1. Jako administrator wybierz pozycję **Start** na serwerze z uruchomieniem usługi Azure AD Connect.
1. Wprowadź **services.msc** w polu wyszukiwania i **wybierz**enter .
1. Poszukaj wpisu **Microsoft Azure AD Sync.**
1. Kliknij prawym przyciskiem myszy wpis usługi, wybierz **polecenie Uruchom ponownie**, a następnie poczekaj na zakończenie operacji.

   ![Ponowne uruchamianie usługi Azure AD Sync przy użyciu interfejsu GUI][Service restart]

Te kroki ponownie nawiązują połączenie z usługą w chmurze i rozwiązują wszelkie przerwy, które mogą wystąpić. Jeśli ponowne uruchomienie usługi ADSync nie rozwiąże problemu, zaleca się, aby spróbować wyłączyć, a następnie ponownie włączyć funkcję zapisywania zwrotnego hasła.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Wyłączanie i ponowne włączanie funkcji zapisywania zwrotnego hasła

Aby rozwiązać problemy z łącznością, wyłącz, a następnie ponownie włącz funkcję zapisywania zwrotnego hasła:

   1. Jako administrator otwórz kreatora konfiguracji usługi Azure AD Connect.
   1. W **obszarze Połącz z usługą Azure AD**wprowadź poświadczenia administratora globalnego usługi Azure AD.
   1. W **obszarze Połącz z usługami AD DS**wprowadź poświadczenia administratora usług domenowych usług AD.
   1. W **obszarze Unikatowa identyfikacja użytkowników**wybierz przycisk **Dalej.**
   1. W **obszarze Funkcje opcjonalne**wyczyść pole wyboru **Zapisywanie hasła.**
   1. Wybierz **pozycję Dalej** na pozostałych stronach dialogowych bez zmiany kolwiek, dopóki nie przejdziesz do strony Gotowe do **skonfigurowania.**
   1. Upewnij się, że **strona Gotowe do skonfigurowania** pokazuje opcję **zapisywania hasła** jako **wyłączona,** a następnie wybierz zielony przycisk **Konfiguruj,** aby zatwierdzić zmiany.
   1. W **obszarze Zakończone**wyczyść opcję **Synchronizuj teraz,** a następnie wybierz pozycję **Zakończ,** aby zamknąć kreatora.
   1. Otwórz ponownie kreatora konfiguracji usługi Azure AD Connect.
   1. Powtórz kroki 2-8, z wyjątkiem upewnij się, że wybierz opcję **Zapisywanie za pomocą hasła** na stronie **Funkcje opcjonalne,** aby ponownie włączyć usługę.

Te kroki ponownie nawiązują połączenie z naszą usługą w chmurze i rozwiązują wszelkie przerwy, które mogą wystąpić.

Jeśli wyłączenie, a następnie ponowne włączenie funkcji zapisywania zwrotnego hasła nie rozwiąże problemu, zaleca się ponowne zainstalowanie usługi Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalowanie najnowszej wersji usługi Azure AD Connect

Ponowna instalacja usługi Azure AD Connect może rozwiązać problemy z konfiguracją i łącznością między naszymi usługami w chmurze a lokalnym środowiskiem usługi Active Directory.

Zaleca się wykonanie tego kroku dopiero po wykonaniu dwóch pierwszych kroków opisanych wcześniej.

> [!WARNING]
> Jeśli dostosowano reguły synchronizacji out-of-the-box, *wykonaj ich tworzenie kopii zapasowych przed przystąpieniem do uaktualnienia, a następnie ręcznie wdrożyć je ponownie po zakończeniu.*

1. Pobierz najnowszą wersję usługi Azure AD Connect z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Ponieważ usługa Azure AD Connect została już zainstalowana, należy przeprowadzić uaktualnienie w miejscu, aby zaktualizować instalację usługi Azure AD Connect do najnowszej wersji.
1. Wykonaj pobrany pakiet i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zaktualizować komputer usługi Azure AD Connect.

Poprzednie kroki należy ponownie ustanowić połączenie z naszą usługą w chmurze i rozwiązać wszelkie przerwy, które mogą wystąpić.

Jeśli zainstalowanie najnowszej wersji serwera usługi Azure AD Connect nie rozwiąże problemu, zaleca się, aby spróbować wyłączyć, a następnie ponownie włączyć zapisywanie hasła jako ostatni krok po zainstalowaniu najnowszej wersji.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Sprawdź, czy usługa Azure AD Connect ma wymagane uprawnienia do zapisywania haseł

Usługa Azure AD Connect wymaga uprawnienia do **resetowania hasła** w usłudze Active Directory w celu wykonania polecenia zapisywania hasła. Aby dowiedzieć się, czy usługa Azure AD Connect ma wymagane uprawnienia dla danego lokalnego konta użytkownika usługi Active Directory, można użyć funkcji Efektywne uprawnienia systemu Windows:

1. Zaloguj się do serwera usługi Azure AD Connect i uruchom **Menedżera usług synchronizacji,** wybierając pozycję **Uruchom** > **usługę synchronizacji**.
1. Na karcie **Łączniki** wybierz lokalny łącznik **Usług domenowych Active Directory,** a następnie wybierz pozycję **Właściwości**.  
   ![Menedżer usługi synchronizacji pokazujący sposób edytowania właściwości](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. W wyskakującym oknie wybierz pozycję **Połącz z lasem usługi Active Directory** i zanotuj właściwość **Nazwa użytkownika.** Ta właściwość jest kontem usług AD DS używanym przez usługę Azure AD Connect do wykonywania synchronizacji katalogów. Aby usługa Azure AD Connect mogła dokonać stortowania hasła, konto usług AD DS musi mieć uprawnienia do resetowania hasła.  

   ![Znajdowanie konta użytkownika usługi synchronizacji Usługi Active Directory](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Zaloguj się do lokalnego kontrolera domeny i uruchom aplikację **Użytkownicy i komputery usługi Active Directory.**
1. Wybierz **pozycję Widok** i upewnij się, że opcja **Funkcje zaawansowane** jest włączona.  

   ![Użytkownicy i komputery usługi Active Directory pokazują funkcje zaawansowane](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Poszukaj konta użytkownika usługi Active Directory, które chcesz zweryfikować. Kliknij prawym przyciskiem myszy nazwę konta i wybierz polecenie **Właściwości**.  
1. W wyskakującym oknie przejdź do karty **Zabezpieczenia** i wybierz pozycję **Zaawansowane**.  
1. W oknie podręcznym **Zaawansowane ustawienia zabezpieczeń dla administratora** przejdź do karty **Efektywny dostęp.**
1. Wybierz **pozycję Wybierz użytkownika**, wybierz konto usług AD DS używane przez usługę Azure AD Connect (patrz krok 3), a następnie wybierz pozycję Wyświetl **efektywny dostęp**.

   ![Karta Efektywny dostęp z kontem synchronizacji](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Przewiń w dół i **poszukaj resetu hasła**. Jeśli wpis jest zaznaczony, konto usług AD DS ma uprawnienia do resetowania hasła wybranego konta użytkownika usługi Active Directory.  

   ![Sprawdzanie, czy konto synchronizacji ma uprawnienie Resetowanie hasła](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fora usługi Azure AD

Jeśli masz ogólne pytanie dotyczące usługi Azure AD i samoobsługowego resetowania hasła, możesz poprosić społeczność o pomoc na [forach usługi Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Członkami społeczności są inżynierowie, menedżerowie produktów, MVPs i inni informatycy.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Jeśli nie możesz znaleźć odpowiedzi na problem, nasze zespoły pomocy technicznej są zawsze dostępne, aby pomóc Ci dalej.

Aby odpowiednio pomóc, prosimy o podanie jak największej ilości szczegółów podczas otwierania sprawy. Szczegóły te obejmują:

* **Ogólny opis błędu**: Co to jest błąd? Jakie zachowanie zostało zauważone? Jak możemy odtworzyć błąd? Zapewnij jak najwięcej szczegółów.
* **Strona**: Na jakiej stronie byłeś, gdy zauważyłeś błąd? Jeśli możesz to zrobić, dołącz adres URL i zrzut ekranu strony.
* **Kod pomocy technicznej:** Jaki był kod pomocy technicznej, który został wygenerowany, gdy użytkownik zobaczył błąd?
   * Aby znaleźć ten kod, odtwórz błąd, a następnie wybierz łącze **Kod pomocy technicznej** u dołu ekranu i wyślij inżynierowi pomocy technicznej identyfikator GUID, który powoduje.

   ![Znajdź kod pomocy technicznej u dołu ekranu][Support code]

  * Jeśli jesteś na stronie bez kodu pomocy technicznej u dołu, wybierz F12 i wyszukaj identyfikator SID i CID i wyślij te dwa wyniki do inżyniera pomocy technicznej.
* **Data, godzina i strefa czasowa:** Podaj dokładną datę i godzinę *ze strefą czasową,* w której wystąpił błąd.
* **Identyfikator użytkownika:** Kto widział błąd? Przykładem jest *contoso.com użytkownika\@*.
   * Czy jest to użytkownik federowany?
   * Czy jest to użytkownik uwierzytelniania przekazywanego?
   * Czy jest to użytkownik zsynchronizowany haszem hasłem?
   * Czy jest to użytkownik tylko w chmurze?
* **Licencjonowanie:** Czy użytkownik ma przypisaną licencję usługi Azure AD?
* **Dziennik zdarzeń aplikacji:** Jeśli używasz stornowania hasła, a błąd znajduje się w infrastrukturze lokalnej, dołącz spakowaną kopię dziennika zdarzeń aplikacji z serwera usługi Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Ponowne uruchamianie usługi Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kod wsparcia znajduje się w prawym dolnym rogu okna"

## <a name="next-steps"></a>Następne kroki

Następujące artykuły zawierają dodatkowe informacje dotyczące resetowania hasła za pośrednictwem usługi Azure AD:

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
