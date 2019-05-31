---
title: Punkt odniesienia zasad bloku starszych uwierzytelniania (wersja zapoznawcza) — usługi Azure Active Directory
description: Protokoły uwierzytelniania starszych bloku zasady dostępu warunkowego
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
ms.openlocfilehash: e7eebc68ae8a55d636f3bc85e179bd7d6813be8d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235554"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Zasady punktu odniesienia: Blokuj starsze uwierzytelnianie (wersja zapoznawcza)

Aby dać użytkownikom łatwy dostęp do aplikacji w chmurze, Azure Active Directory (Azure AD) obsługuje szerokiej gamy protokołów uwierzytelniania, w tym starsze uwierzytelnianie. Uwierzytelnianie starszych jest terminu, który odwołuje się do żądania uwierzytelnienia przez:

* Starsi klienci pakietu Office, które nie korzystają z nowoczesnego uwierzytelniania (na przykład klienta pakietu Office 2010)
* Dowolnego klienta, który korzysta z protokołów poczty starszej wersji, takich jak IMAP/SMTP/POP3

Obecnie większość wszystkich uszczerbku prób logowania pochodzą z uwierzytelniania starszej wersji. Uwierzytelnianie starszych nie obsługuje uwierzytelnianie wieloskładnikowe (MFA). Nawet w przypadku zasad MFA włączone w Twoim katalogu, nieuprawnione można uwierzytelniać przy użyciu starszej wersji protokołu i Pomiń uwierzytelnianie wieloskładnikowe.

Najlepszym sposobem, aby chronić swoje konto z żądań uwierzytelniania złośliwego przez starszych protokołów jest zablokować te próby wszystko ze sobą. Aby ułatwić zablokować wszystkie żądania logowania dokonanych przez starszych protokołów, utworzyliśmy zasady linii bazowej, które właśnie to robi.

![Blokuj starsze uwierzytelnianie przy użyciu dostępu warunkowego](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**Blokuj starsze uwierzytelnianie** jest [bazowymi zasadami](concept-baseline-protection.md) , która blokuje wszystkie żądania uwierzytelniania starszych protokołów. Nowoczesnego uwierzytelniania musi być użyte do zalogowania się pomyślnie dla wszystkich użytkowników. Używany w połączeniu z innymi zasadami linii bazowej, wszystkie żądania pochodzące z starszych protokołów zostanie zablokowane, a wszyscy użytkownicy będą musieli usługi MFA w każdym przypadku, gdy wymagane. Ta zasada nie blokuje programu Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identyfikowanie starszych uwierzytelniania użycia

Zanim zablokujesz starsze uwierzytelnianie w katalogu, należy najpierw zrozumieć, jeśli użytkownicy mają aplikacje, które używają starszych uwierzytelniania i jak wpływa na ogólne katalogu. Dzienniki logowania usługi AD platformy Azure może służyć do zrozumienia, jeśli używasz starszej wersji uwierzytelniania.

1. Przejdź do **witryny Azure portal** > **usługi Azure Active Directory** > **logowania**.
1. Dodaj kolumnę aplikacji klienckiej, jeśli nie jest wyświetlany, klikając **kolumn** > **aplikację kliencką**.
1. Filtruj według **aplikację kliencką** > **innych klientów** i kliknij przycisk **Zastosuj**.

Filtrowanie będzie tylko nieudane próby show logowania wprowadzono protokoły uwierzytelniania w starszej wersji. Kliknięcie każdego poszczególnych prób logowania zostanie wyświetlona dodatkowe szczegóły. **Aplikację kliencką** pola pod **podstawowe informacje o** kartę poinformuje, użyty protokół uwierzytelniania starszej wersji.

Te dzienniki będą wskazywać użytkowników, którzy są nadal w zależności od starszego uwierzytelniania i aplikacji, które używają starszych protokołów uwierzytelniania żądań. Dla użytkowników, którzy nie są wyświetlane w tych dziennikach i zostało potwierdzone nie korzystać z uwierzytelniania starszej wersji, należy zaimplementować zasady dostępu warunkowego lub Włącz **bazowymi zasadami: Blokuj starsze uwierzytelnianie** te tylko dla użytkowników.

## <a name="moving-away-from-legacy-authentication"></a>Przejście od starszego uwierzytelniania

Po utworzeniu, aby lepiej zrozumieć, który używa uwierzytelniania starszej wersji w Twoim katalogu i aplikacji, które są od niego zależne, następnym krokiem jest uaktualniany użytkowników, aby korzystała z nowoczesnego uwierzytelniania. Nowoczesnego uwierzytelniania to metoda zarządzania tożsamościami, oferującego bardziej bezpiecznego uwierzytelniania i autoryzacji użytkowników. Jeśli masz zasady usługi MFA w miejscu w Twoim katalogu nowoczesnego uwierzytelniania gwarantuje, że użytkownik jest monitowany o uwierzytelnianie wieloskładnikowe, gdy jest to wymagane. Jest bardziej bezpieczne alternatywę dla uwierzytelniania starszych protokołów.

Ta sekcja zawiera omówienie krok po kroku na temat aktualizowania środowiska do nowoczesnego uwierzytelniania. Zapoznaj się z artykułem poniższe kroki przed włączeniem uwierzytelniania starszej wersji, blokuje zasady w danej organizacji.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Krok 1: Włączenie nowoczesnego uwierzytelniania w katalogu

Włączanie nowoczesnego uwierzytelniania pierwszym krokiem jest upewnienie się, że Twój katalog obsługuje nowoczesnego uwierzytelniania. Nowoczesnego uwierzytelniania jest domyślnie włączone dla katalogi utworzone po 1 sierpnia 2017 r. Jeśli Twój katalog został utworzony przed tą datą, musisz ręcznie włączyć nowoczesne uwierzytelnianie dla katalogu wykonując następujące czynności:

1. Sprawdź, czy Twój katalog już obsługuje nowoczesnego uwierzytelniania, uruchamiając `Get-CsOAuthConfiguration` z [Skype dla firm Online PowerShell module](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Jeśli polecenie zwraca pustą `OAuthServers` właściwości, a następnie nowoczesne uwierzytelnianie jest wyłączone. Zaktualizuj ustawienie, aby włączyć nowoczesne uwierzytelnianie przy użyciu `Set-CsOAuthConfiguration`. Jeśli Twoje `OAuthServers` właściwości zawiera pozycję, możesz startować.

Pamiętaj ukończyć ten krok, aby przejść dalej. Ważne jest, że konfiguracje katalogu są zmieniane, ponieważ są wymagane, protokół, który będzie używany przez wszystkich klientów pakietu Office jest. Nawet wtedy, gdy używasz klientów pakietu Office, które obsługują nowoczesnego uwierzytelniania, ich domyślne nazwy będą się przy użyciu starszych protokołów, jeśli nowoczesne uwierzytelnianie jest wyłączone w Twoim katalogu.

### <a name="step-2-office-applications"></a>Krok 2: Aplikacje pakietu Office

Po włączeniu nowoczesnego uwierzytelniania w katalogu, możesz rozpocząć aktualizowanie aplikacji, należy włączyć nowoczesne uwierzytelnianie dla klientów pakietu Office. Pakiet Office 2016 lub nowsze klienci domyślnie obsługują nowoczesnego uwierzytelniania. Żadne dodatkowe czynności nie są wymagane.

Jeśli używasz pakietu Office 2013 Windows klientów lub starsze, zaleca się uaktualnienie do pakietu Office 2016 lub nowszego. Nawet po ukończeniu poprzedniego kroku Włączanie nowoczesnego uwierzytelniania w katalogu starsze aplikacje pakietu Office będzie używać protokoły uwierzytelniania w starszej wersji. Jeśli używasz klientów pakietu Office 2013 i nie można bezpośrednio uaktualnić do pakietu Office 2016 lub nowszego, wykonaj kroki w poniższym artykułem, aby [włączyć nowoczesne uwierzytelnianie dla pakietu Office 2013 na urządzeniach Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Aby lepiej chronić Twoje konto podczas korzystania z uwierzytelniania starszej wersji, zaleca się przy użyciu silnych haseł dla katalogu. Zapoznaj się z [ochrona za pomocą hasła usługi Azure AD](../authentication/concept-password-ban-bad.md) zablokować słabe hasła w katalogu.

Pakiet Office 2010 nie obsługują nowoczesnego uwierzytelniania. Należy uaktualnić każdy użytkownik z pakietem Office 2010 do nowszej wersji pakietu Office. Firma Microsoft zaleca, uaktualnienie do pakietu Office 2016 lub nowszym, ponieważ domyślnie blokuje starszych uwierzytelniania.

Jeśli używasz systemu MacOS zaleca się uaktualnienie do pakietu Office dla komputerów Mac 2016 lub nowszego. Jeśli używasz klienta natywnego programu pocztowego, należy z systemem MacOS w wersji 10.14 lub później na wszystkich urządzeniach.

### <a name="step-3-exchange-and-sharepoint"></a>Krok 3: Programy Exchange i SharePoint

Dla klientów programu Outlook z systemem Windows korzystała z nowoczesnego uwierzytelniania Exchange Online musi być nowoczesnego uwierzytelniania, także włączone. Jeśli z nowoczesnego uwierzytelniania jest wyłączona w przypadku Usługa Exchange Online, klientów programu Outlook z systemem Windows obsługujące nowoczesnego uwierzytelniania (Outlook 2013 lub nowszy) będą używane uwierzytelnianie podstawowe do łączenia do skrzynek pocztowych usługi Exchange Online.

SharePoint Online jest włączony dla domyślnej nowoczesnego uwierzytelniania. W przypadku katalogów utworzonych po 1 sierpnia 2017 r. nowoczesnego uwierzytelniania jest domyślnie włączona, w usłudze Exchange Online. Jednak jeśli wcześniej była wyłączona nowoczesnego uwierzytelniania lub używasz Katalog utworzony przed tą datą, postępuj zgodnie z instrukcjami w artykule aby [Włączanie nowoczesnego uwierzytelniania w usłudze Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Krok 4: Skype dla firm

Aby zapobiec starsze uwierzytelnianie żądań przez program Skype dla firm, należy włączyć nowoczesne uwierzytelnianie dla usługi Skype dla firm Online. W przypadku katalogów utworzonych po 1 sierpnia 2017 r. nowoczesne uwierzytelnianie dla aplikacji Skype dla firm jest domyślnie włączona.

Aby włączyć nowoczesnego uwierzytelniania w programie Skype dla firm, zalecamy przejście do Microsoft Teams, która domyślnie obsługuje nowoczesnego uwierzytelniania. Jednak nie jest możliwe do tr w tej chwili, należy włączyć nowoczesne uwierzytelnianie dla usługi Skype dla firm Online, Skype dla firm klientów uruchamia korzystających z nowoczesnego uwierzytelniania. Wykonaj następujące kroki w artykule [Skype dla firm topologie obsługiwane z nowoczesnym uwierzytelnianiem](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), kroki włączyć nowoczesne uwierzytelnianie dla aplikacji Skype dla firm.

Oprócz Włączanie nowoczesnego uwierzytelniania dla usługi Skype dla firm Online, firma Microsoft zaleca nowoczesnego uwierzytelniania można włączyć dla usługi Exchange Online, gdy włączanie nowoczesnego uwierzytelniania dla usługi Skype dla firm. Ten proces ułatwi Synchronizuj stan nowoczesnego uwierzytelniania w usłudze Exchange Online i Skype dla firm online i uniemożliwi wielu monity o logowanie do usługi Skype dla firm klientów.

### <a name="step-5-using-mobile-devices"></a>Krok 5. Za pomocą urządzeń przenośnych

Aplikacje na urządzeniu przenośnym należy zablokować starsze uwierzytelnianie także. Firma Microsoft zaleca używanie programu Outlook dla urządzeń przenośnych. Outlook Mobile domyślnie obsługuje nowoczesnego uwierzytelniania i będzie spełniać inne zasady ochrony linii bazowej MFA.

Aby korzystać z klienta poczty e-mail natywnych dla systemów iOS, należy być uruchomiony system iOS w wersji 11.0 lub nowszej, aby upewnić się, że klient poczty został zaktualizowany, aby zablokować starsze uwierzytelnianie.

### <a name="step-6-on-premises-clients"></a>Krok 6: Klienci w środowisku lokalnym

Jeśli jesteś klientem hybrydowy, za pomocą lokalnego programu Exchange Server i programu Skype dla firm w środowisku lokalnym, obie te usługi należy zaktualizować, aby włączyć nowoczesne uwierzytelnianie. Korzystając z nowoczesnego uwierzytelniania w środowisku hybrydowym, są nadal uwierzytelniania użytkowników w środowisku lokalnym. Historia autoryzowania dostępu do zasobów (plików lub wiadomości e-mail) zmiany.

Zanim będzie można rozpocząć włączanie nowoczesnego uwierzytelniania w środowisku lokalnym, należy spełnić theIf spełniają wymagania, możesz teraz włączyć nowoczesne uwierzytelnianie w środowisku lokalnym.

Procedurę włączania nowoczesnego uwierzytelniania można znaleźć w następujących artykułach:

* [Jak skonfigurować serwer programu Exchange w środowisku lokalnym do użycia z nowoczesnym uwierzytelnianiem hybrydowym](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Jak używać nowoczesnego uwierzytelniania (ADAL) za pomocą programu Skype dla firm](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Włącz zasady linii bazowej

Zasady **bazowymi zasadami: Blokuj starsze uwierzytelnianie (wersja zapoznawcza)** ma wstępnie skonfigurowany i pojawi się u góry po przejściu do bloku dostępu warunkowego w witrynie Azure portal.

Aby włączyć te zasady i chronić swoją organizację:

1. Zaloguj się do **witryny Azure portal** jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
1. Na liście zasad wybierz **bazowymi zasadami: Blokuj starsze uwierzytelnianie (wersja zapoznawcza)** .
1. Ustaw **Włącz zasady** do **Użyj zasad natychmiast**.
1. Dodaj wykluczenia użytkownika, klikając **użytkowników** > **wybierz wykluczonych użytkowników** i wybierając pozycję Użytkownicy, którzy muszą być wyłączone. Kliknij przycisk **wybierz** następnie **gotowe**.
1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz:

* [Zasady ochrony linii bazowej dostępu warunkowego](concept-baseline-protection.md)
* [Pięć kroków do zabezpieczania infrastruktury tożsamości](../../security/azure-ad-secure-steps.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)
