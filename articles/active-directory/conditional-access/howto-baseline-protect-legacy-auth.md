---
title: Blokada zasad linii bazowej starszego uwierzytelniania (wersja zapoznawcza) — Azure Active Directory
description: Zasady dostępu warunkowego do blokowania starszych protokołów uwierzytelniania
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91cc2707034eabf85afae70278f2448b77d3ee14
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608077"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Zasady linii bazowej: Blokuj starsze uwierzytelnianie (wersja zapoznawcza)

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure Active Directory (Azure AD) obsługuje szeroką gamę protokołów uwierzytelniania, w tym starsze uwierzytelnianie. Starsza wersja uwierzytelniania to termin, który odwołuje się do żądania uwierzytelnienia wykonanego przez:

* Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klient pakietu Office 2010)
* Każdy klient korzystający ze starszych protokołów poczty, takich jak IMAP/SMTP/POP3

Dzisiaj większość wszelkich niezwiązanych z logowaniem nie pochodzi ze starszej wersji uwierzytelniania. Starsza wersja uwierzytelniania nie obsługuje uwierzytelniania wieloskładnikowego (MFA). Nawet jeśli w Twoim katalogu włączono zasady MFA, nieprawidłowy aktor może być uwierzytelniany przy użyciu starszego protokołu i pomijania usługi MFA.

Najlepszym sposobem na ochronę konta przed złośliwymi żądaniami uwierzytelniania podejmowanymi przez starsze protokoły jest zablokowanie tych prób w ogóle. Aby ułatwić zabezpieczanie środowiska, utworzyliśmy te zasady odniesienia w celu blokowania starszego uwierzytelniania.

**Blokada starszego uwierzytelniania** to [zasada odniesienia](concept-baseline-protection.md) , która blokuje wszystkie żądania uwierzytelniania pochodzące ze starszych protokołów. Nowoczesnego uwierzytelniania należy użyć, aby pomyślnie zalogować się dla wszystkich użytkowników. W połączeniu z innymi zasadami odniesienia wszystkie żądania pochodzące ze starszych protokołów będą blokowane, a wszyscy użytkownicy będą musieli być zobowiązani do uwierzytelniania wieloskładnikowego w razie potrzeby. Te zasady nie blokują programu Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identyfikuj użycie starszego uwierzytelniania

Przed zablokowaniem starszego uwierzytelniania w katalogu należy najpierw zrozumieć, czy użytkownicy mają aplikacje korzystające ze starszego uwierzytelniania i jak mają wpływ na ogólny katalog. Korzystając z dzienników logowania usługi Azure AD, można zrozumieć, czy używane jest starsze uwierzytelnianie.

1. Przejdź do **Azure Portal** > **Azure Active Directory** > **logowania**.
1. Dodaj kolumnę aplikacji klienckiej, jeśli nie jest ona wyświetlana po kliknięciu pozycji **kolumny** > **aplikacja kliencka**.
1. Filtruj według**innych klientów** **aplikacji** > klienckiej, a następnie kliknij przycisk **Zastosuj**.

Filtrowanie będzie zawierać tylko próby logowania, które zostały wykonane przez starsze protokoły uwierzytelniania. Kliknięcie każdej próby logowania spowoduje wyświetlenie dodatkowych szczegółów. Pole **aplikacji klienckiej** na karcie **Informacje podstawowe** wskazuje, który z starszych wersji protokołu uwierzytelniania był używany.

Te dzienniki wskazują, którzy użytkownicy nadal są w stanie w zależności od starszego uwierzytelniania i które aplikacje używają starszych protokołów do przesyłania żądań uwierzytelniania. W przypadku użytkowników, którzy nie znajdują się w tych dziennikach i potwierdzają, że nie używają starszego uwierzytelniania, zaimplementuj zasady dostępu warunkowego lub Włącz **zasady linii bazowej: Blokuj starsze uwierzytelnianie** dla tych użytkowników.

## <a name="moving-away-from-legacy-authentication"></a>Przejście z starszego uwierzytelniania

Po lepszym założeniu, kto korzysta z starszego uwierzytelniania w katalogu i które aplikacje są od niego zależne, następnym krokiem jest uaktualnienie użytkowników w celu korzystania z nowoczesnego uwierzytelniania. Nowoczesne uwierzytelnianie to metoda zarządzania tożsamościami, która oferuje bezpieczniejsze uwierzytelnianie i autoryzację użytkowników. Jeśli w katalogu znajdują się zasady MFA, nowoczesne uwierzytelnianie zapewnia, że użytkownik jest monitowany o MFA w razie potrzeby. Jest to bezpieczniejszy alternatywa dla starszych protokołów uwierzytelniania.

Ta sekcja zawiera omówienie krok po kroku dotyczące aktualizowania środowiska do nowoczesnego uwierzytelniania. Zapoznaj się z poniższymi krokami przed włączeniem zasad blokowania uwierzytelniania w organizacji.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Krok 1: Włącz nowoczesne uwierzytelnianie w katalogu

Pierwszym krokiem w celu włączenia nowoczesnego uwierzytelniania jest upewnienie się, że katalog obsługuje nowoczesne uwierzytelnianie. Nowoczesne uwierzytelnianie jest domyślnie włączone dla katalogów utworzonych w dniu lub po 1 sierpnia 2017. Jeśli katalog został utworzony przed tą datą, musisz ręcznie włączyć nowoczesne uwierzytelnianie dla katalogu, wykonując następujące czynności:

1. Sprawdź, czy katalog już obsługuje nowoczesne uwierzytelnianie, uruchamiając `Get-CsOAuthConfiguration` go z poziomu [modułu programu PowerShell w usłudze Skype dla firm Online](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Jeśli polecenie zwróci pustą `OAuthServers` właściwość, nowoczesne uwierzytelnianie jest wyłączone. Zaktualizuj ustawienie, aby umożliwić nowoczesne uwierzytelnianie przy `Set-CsOAuthConfiguration`użyciu programu. Jeśli Twoja `OAuthServers` Właściwość zawiera wpis, możesz go wybrać.

Pamiętaj, aby ukończyć ten krok przed przejściem do przodu. Ważne jest, aby konfiguracje katalogów były najpierw zmieniane, ponieważ określają, który protokół będzie używany przez wszystkich klientów pakietu Office. Nawet jeśli używasz klientów pakietu Office obsługujących nowoczesne uwierzytelnianie, domyślnie będą używać starszych protokołów, jeśli nowoczesne uwierzytelnianie jest wyłączone w Twoim katalogu.

### <a name="step-2-office-applications"></a>Krok 2: Aplikacje pakietu Office

Po włączeniu nowoczesnego uwierzytelniania w katalogu można rozpocząć aktualizowanie aplikacji, włączając nowoczesne uwierzytelnianie dla klientów pakietu Office. Klienci z pakietem Office 2016 lub nowszym domyślnie obsługują nowoczesne uwierzytelnianie. Nie są wymagane żadne dodatkowe czynności.

Jeśli używasz klientów systemu Windows z pakietem Office 2013 lub starszej wersji, zalecamy uaktualnienie do pakietu Office 2016 lub nowszego. Nawet po zakończeniu poprzedniego kroku włączania nowoczesnego uwierzytelniania w katalogu starsze aplikacje pakietu Office będą nadal używać starszych protokołów uwierzytelniania. Jeśli używasz klientów pakietu Office 2013 i nie możesz natychmiast przeprowadzić uaktualnienia do pakietu Office 2016 lub nowszego, wykonaj kroki opisane w następującym artykule, aby [włączyć nowoczesne uwierzytelnianie dla pakietu office 2013 na urządzeniach z systemem Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Aby chronić Twoje konto podczas korzystania ze starszego uwierzytelniania, zalecamy używanie silnych haseł w katalogu. Sprawdź [ochronę hasłem usługi Azure AD](../authentication/concept-password-ban-bad.md) , aby zablokować słabe hasła w katalogu.

Pakiet Office 2010 nie obsługuje nowoczesnego uwierzytelniania. Należy uaktualnić wszystkich użytkowników z pakietem Office 2010 do nowszej wersji pakietu Office. Zalecamy uaktualnienie do pakietu Office 2016 lub nowszego, ponieważ domyślnie blokuje ono starsze uwierzytelnianie.

Jeśli używasz MacOS, zalecamy uaktualnienie do pakietu Office dla komputerów Mac 2016 lub nowszego. Jeśli używasz natywnego klienta poczty, na wszystkich urządzeniach musi być zainstalowany program MacOS w wersji 10,14 lub nowszej.

### <a name="step-3-exchange-and-sharepoint"></a>Krok 3: Exchange i SharePoint

W przypadku korzystania z nowoczesnego uwierzytelniania przez klientów programu Outlook opartych na systemie Windows Usługa Exchange Online musi być również włączona. Jeśli nowoczesne uwierzytelnianie jest wyłączone dla usługi Exchange Online, klienci programu Outlook korzystający z systemu Windows, którzy obsługują nowoczesne uwierzytelnianie (Outlook 2013 lub nowsza), będą używać uwierzytelniania podstawowego do nawiązywania połączenia z skrzynkami pocztowymi usługi Exchange Online.

Program SharePoint Online jest domyślnie włączony dla nowoczesnego uwierzytelniania. W przypadku katalogów utworzonych po 1 sierpnia 2017, nowoczesne uwierzytelnianie jest domyślnie włączone w usłudze Exchange Online. Jeśli jednak wcześniej wyłączono nowoczesne uwierzytelnianie lub jeśli korzystasz z katalogu utworzonego przed tą datą, wykonaj kroki opisane w następującym artykule, aby [włączyć nowoczesne uwierzytelnianie w usłudze Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Krok 4: Skype dla firm

Aby uniemożliwić starsze żądania uwierzytelniania wykonywane przez usługę Skype dla firm, należy włączyć nowoczesne uwierzytelnianie dla usługi Skype dla firm Online. W przypadku katalogów utworzonych po 1 sierpnia 2017, nowoczesne uwierzytelnianie dla usługi Skype dla firm jest domyślnie włączone.

Zalecamy przechodzenie do programu Microsoft Teams, który domyślnie obsługuje nowoczesne uwierzytelnianie. Jeśli jednak w tej chwili nie można przeprowadzić migracji, należy włączyć nowoczesne uwierzytelnianie dla usługi Skype dla firm Online, aby klienci programu Skype dla firm mogli zacząć korzystać z nowoczesnego uwierzytelniania. Wykonaj kroki opisane w tym artykule [dotyczące topologii programu Skype dla firm obsługiwanych przy użyciu nowoczesnego uwierzytelniania](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), aby włączyć nowoczesne uwierzytelnianie dla programu Skype dla firm.

Oprócz włączenia nowoczesnego uwierzytelniania dla usługi Skype dla firm Online zaleca się włączenie nowoczesnego uwierzytelniania w usłudze Exchange Online w przypadku włączenia nowoczesnego uwierzytelniania dla programu Skype dla firm. Ten proces pomoże zsynchronizować stan nowoczesnego uwierzytelniania w usłudze Exchange Online i usłudze Skype dla firm Online, co uniemożliwi wielokrotne logowanie klientów programu Skype dla firm.

### <a name="step-5-using-mobile-devices"></a>Krok 5. Korzystanie z urządzeń przenośnych

Aplikacje na urządzeniu przenośnym muszą blokować również starsze uwierzytelnianie. Zalecamy korzystanie z programu Outlook dla urządzeń przenośnych. Program Outlook dla urządzeń przenośnych domyślnie obsługuje nowoczesne uwierzytelnianie i będzie spełniać inne zasady ochrony linii bazowej usługi MFA.

Aby można było korzystać z natywnego klienta poczty systemu iOS, należy uruchomić system iOS w wersji 11,0 lub nowszej, aby upewnić się, że klient poczty został zaktualizowany w celu blokowania starszego uwierzytelniania.

### <a name="step-6-on-premises-clients"></a>Krok 6: Klienci lokalni

Jeśli jesteś hybrydowym klientem korzystającym lokalnie z lokalnego programu Exchange Server i lokalnego programu Skype dla firm, należy zaktualizować obie usługi, aby umożliwić nowoczesne uwierzytelnianie. W przypadku korzystania z nowoczesnego uwierzytelniania w środowisku hybrydowym nadal są uwierzytelniani użytkownicy lokalnie. Historię autoryzacji dostępu do zasobów (plików lub wiadomości e-mail).

Przed rozpoczęciem włączania nowoczesnego uwierzytelniania lokalnego upewnij się, że spełniono wymagania wstępne.
Teraz można przystąpić do włączania nowoczesnego uwierzytelniania w środowisku lokalnym.

Kroki umożliwiające włączenie nowoczesnego uwierzytelniania można znaleźć w następujących artykułach:

* [Jak skonfigurować lokalny serwer Exchange Server do korzystania z nowoczesnego uwierzytelniania hybrydowego](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Jak używać nowoczesnego uwierzytelniania (ADAL) w usłudze Skype dla firm](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Włączanie zasad linii bazowej

Zasady linii **bazowej zasad: Blok starszego uwierzytelniania (wersja** zapoznawcza) jest wstępnie skonfigurowany i będzie widoczny u góry po przejściu do bloku dostęp warunkowy w Azure Portal. To ustawienie zacznie obowiązywać dopiero po pomyślnym zalogowaniu, dlatego użytkownicy będą nadal mieć możliwość próby użycia starszego uwierzytelniania.

Aby włączyć te zasady i chronić organizację:

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **dostęp warunkowy**.
1. Na liście zasad wybierz pozycję **zasady linii bazowej: Blokuj starsze uwierzytelnianie (wersja zapoznawcza)** .
1. Ustaw opcję **Włącz zasady** , aby od **razu używać zasad**.
1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony punktu odniesienia dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md)
* [Co to jest dostęp warunkowy w Azure Active Directory?](overview.md)
