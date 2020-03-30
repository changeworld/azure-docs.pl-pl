---
title: Rozwiązywanie problemów z serwerem proxy aplikacji | Dokumenty firmy Microsoft
description: Opisano sposób rozwiązywania problemów z błędami w usłudze Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7be9a17bed2a39d16f813332c2d6effc03393264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244227"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach

Podczas rozwiązywania problemów z serwerem proxy aplikacji zaleca się rozpoczęcie od przejrzenia przepływu rozwiązywania problemów, problemów z [łącznikiem serwera proxy aplikacji debugowania,](application-proxy-debug-connectors.md)aby ustalić, czy łączniki serwera proxy aplikacji są poprawnie skonfigurowane. Jeśli nadal występują problemy z połączeniem się z aplikacją, postępuj zgodnie z przepływem rozwiązywania problemów z [aplikacjami serwera proxy aplikacji debugowania.](application-proxy-debug-apps.md)

Jeśli wystąpią błędy podczas uzyskiwania dostępu do opublikowanej aplikacji lub publikowania aplikacji, sprawdź następujące opcje, aby sprawdzić, czy serwer proxy aplikacji usługi Microsoft Azure AD działa poprawnie:

* Otwórz konsolę Usług systemu Windows. Sprawdź, czy usługa **łącznika serwera proxy aplikacji AAD** firmy Microsoft jest włączona i uruchomiona. Można również spojrzeć na właściwości usługi usługi Application Proxy, jak pokazano na poniższej ilustracji:  
  ![Zrzut ekranu okna Właściwości łącznika serwera proxy aplikacji microsoft AAD](./media/application-proxy-troubleshoot/connectorproperties.png)
* Otwórz Podgląd zdarzeń i poszukaj zdarzeń łącznika serwera proxy aplikacji w **dziennikach** > aplikacji i usług**Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* W razie potrzeby bardziej szczegółowe dzienniki są dostępne po [włączeniu dzienników sesji złącza serwera proxy aplikacji](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>Strona nie jest poprawnie renderowana
Mogą wystąpić problemy z renderowaniem aplikacji lub nieprawidłowym działaniem bez otrzymywania określonych komunikatów o błędach. Może to nastąpić, jeśli opublikowano ścieżkę artykułu, ale aplikacja wymaga zawartości, która istnieje poza tą ścieżką.

Na przykład jeśli opublikujesz ścieżkę, `https://yourapp/app` `https://yourapp/media`ale aplikacja wywoła obrazy w , nie będą one renderowane. Upewnij się, że publikujesz aplikację przy użyciu ścieżki najwyższego poziomu, którą należy dołączyć całą odpowiednią zawartość. W tym przykładzie `http://yourapp/`będzie to .

Jeśli zmienisz ścieżkę, aby uwzględnić zawartość, do której istnieją odwołania, ale nadal potrzebujesz, aby użytkownicy mogli wylądować na głębszym łączu w ścieżce, zobacz wpis w blogu [Ustawianie odpowiedniego łącza dla aplikacji proxy aplikacji w panelu dostępu usługi Azure AD i programie Uruchamianie aplikacji usługi Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Błędy łącznika

Jeśli rejestracja nie powiedzie się podczas instalacji kreatora łącznika, istnieją dwa sposoby, aby wyświetlić przyczynę błędu. Albo poszukaj w dzienniku zdarzeń w obszarze **Dzienniki aplikacji i usług\Microsoft\AadApplicationProxy\Connector\Admin**, albo uruchom następujące polecenie programu Windows PowerShell:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Po znalezieniu błędu łącznika z dziennika zdarzeń użyj tej tabeli typowych błędów, aby rozwiązać problem:

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Rejestracja łącznika nie powiodła się: upewnij się, że w portalu azure management portal włączono serwer proxy aplikacji i że poprawnie wprowadzono nazwę użytkownika i hasło usługi Active Directory. Błąd: "Wystąpił co najmniej jeden błąd". | Jeśli okno rejestracji zostało zamknięte bez logowania się do usługi Azure AD, uruchom ponownie kreatora łącznika i zarejestruj łącznik. <br><br> Jeśli okno rejestracji zostanie otwarte, a następnie natychmiast zostanie zamknięte bez zezwolenia na zalogowanie się, prawdopodobnie pojawi się ten błąd. Ten błąd występuje, gdy w systemie występuje błąd sieci. Upewnij się, że możliwe jest połączenie z przeglądarki z publiczną witryną sieci Web i że porty są otwarte zgodnie z [warunkami wstępnymi serwera proxy aplikacji](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| W oknie rejestracji zostanie wyświetlony wyraźny błąd. Nie można kontynuować | Jeśli widzisz ten błąd, a następnie okno zostanie zamknięte, wprowadzono niewłaściwą nazwę użytkownika lub hasło. Spróbuj ponownie. |
| Rejestracja łącznika nie powiodła się: upewnij się, że w portalu azure management portal włączono serwer proxy aplikacji i że poprawnie wprowadzono nazwę użytkownika i hasło usługi Active Directory. Błąd: "AADSTS50059: Nie znaleziono informacji identyfikujących dzierżawę w żądaniu lub dorozumianych przez dostarczone poświadczenia i wyszukiwanie według identyfikatora URI jednostki usługi nie powiodło się. | Próbujesz zalogować się przy użyciu konta Microsoft, a nie domeny, która jest częścią identyfikatora organizacji katalogu, do którego próbujesz uzyskać dostęp. Upewnij się, że administrator jest częścią tej samej nazwy domeny co domena dzierżawy, na przykład, admin@contoso.comjeśli domena usługi Azure AD jest contoso.com, administratorem powinien być . |
| Nie można pobrać bieżącej zasady wykonywania dla uruchamiania skryptów programu PowerShell. | Jeśli instalacja łącznika nie powiedzie się, sprawdź, czy zasady wykonywania programu PowerShell nie są wyłączone. <br><br>1. Otwórz Edytor zasad grupy.<br>2. Przejdź do **konfiguracji komputera** > **Szablony administracyjne** > **Windows Components** > **Windows PowerShell** i kliknij dwukrotnie **włącz wykonywanie skryptu**.<br>3. Zasady wykonywania można ustawić na **Nieskonfigurowane** lub **Włączone**. Jeśli **ustawiono na Włączone,** upewnij się, że w obszarze Opcje zasady wykonywania są ustawione **na Zezwalaj na skrypty lokalne i skrypty podpisane zdalnie** lub **Zezwalaj na wszystkie skrypty**. |
| Nie można pobrać konfiguracji przez łącznik. | Certyfikat klienta łącznika, który jest używany do uwierzytelniania, wygasł. Może to również wystąpić, jeśli łącznik jest zainstalowany za serwerem proxy. W takim przypadku łącznik nie może uzyskać dostępu do Internetu i nie będzie mógł udostępniać aplikacji użytkownikom zdalnym. Odnawianie zaufania ręcznie `Register-AppProxyConnector` przy użyciu polecenia cmdlet w programie Windows PowerShell. Jeśli łącznik znajduje się za serwerem proxy, konieczne jest udzielenie dostępu do Internetu do kont łącznika "usługi sieciowe" i "system lokalny". Można to osiągnąć poprzez przyznanie im dostępu do serwera proxy lub ustawienie ich, aby pominąć serwer proxy. |
| Rejestracja łącznika nie powiodła się: upewnij się, że jesteś administratorem aplikacji usługi Active Directory, aby zarejestrować łącznik. Błąd: "Żądanie rejestracji zostało odrzucone". | Alias, którego próbujesz się zalogować, nie jest administratorem tej domeny. Łącznik jest zawsze zainstalowany dla katalogu, który jest właścicielem domeny użytkownika. Upewnij się, że konto administratora, za pomocą którego próbujesz się zalogować, ma conajmniej uprawnienia administratora aplikacji do dzierżawy usługi Azure AD. |
| Łącznik nie może połączyć się z usługą z powodu problemów z siecią. Łącznik próbował uzyskać dostęp do następującego adresu URL. | Łącznik nie może połączyć się z usługą chmury serwera proxy aplikacji. Może się tak zdarzyć, jeśli połączenie blokuje regułę zapory. Upewnij się, że masz prawo dostępu do odpowiednich portów i adresów URL wymienionych w [wymaganiach wstępnych serwera proxy aplikacji](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Błędy protokołu Kerberos

Ta tabela obejmuje bardziej typowe błędy, które pochodzą z konfiguracji i konfiguracji protokołu Kerberos i zawiera sugestie dotyczące rozwiązania.

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Nie można pobrać bieżącej zasady wykonywania dla uruchamiania skryptów programu PowerShell. | Jeśli instalacja łącznika nie powiedzie się, sprawdź, czy zasady wykonywania programu PowerShell nie są wyłączone.<br><br>1. Otwórz Edytor zasad grupy.<br>2. Przejdź do **konfiguracji komputera** > **Szablony administracyjne** > **Windows Components** > **Windows PowerShell** i kliknij dwukrotnie **włącz wykonywanie skryptu**.<br>3. Zasady wykonywania można ustawić na **Nieskonfigurowane** lub **Włączone**. Jeśli **ustawiono na Włączone,** upewnij się, że w obszarze Opcje zasady wykonywania są ustawione **na Zezwalaj na skrypty lokalne i skrypty podpisane zdalnie** lub **Zezwalaj na wszystkie skrypty**. |
| 12008 — usługa Azure AD przekroczyła maksymalną liczbę dozwolonych prób uwierzytelniania Kerberos na serwerze wewnętrznej bazy danych. | Ten błąd może wskazywać niepoprawną konfigurację między usługą Azure AD a serwerem aplikacji wewnętrznej bazy danych lub problem w konfiguracji czasu i daty na obu komputerach. Serwer zaplecza odrzucił bilet protokołu Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji wewnętrznej bazy danych są poprawnie skonfigurowane. Upewnij się, że konfiguracja godziny i daty na usłudze Azure AD i serwerze aplikacji wewnętrznej bazy danych są synchronizowane. |
| 13016 — usługa Azure AD nie może pobrać biletu Protokołu Kerberos w imieniu użytkownika, ponieważ nie ma nazwy UPN w tokenie krawędzi lub w pliku cookie dostępu. | Istnieje problem z konfiguracją usługi STS. Napraw Konfiguracja oświadczenia nazwy UPN w usłudze STS. |
| 13019 — usługa Azure AD nie może pobrać biletu Protokołu Kerberos w imieniu użytkownika z powodu następującego błędu ogólnego interfejsu API. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD a serwerem kontrolera domeny lub problem w konfiguracji czasu i daty na obu komputerach. Kontroler domeny odrzucił bilet protokołu Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji wewnętrznej bazy danych są poprawnie skonfigurowane, zwłaszcza konfiguracji SPN. Upewnij się, że usługa Azure AD jest domeną przyłączona do tej samej domeny co kontroler domeny, aby upewnić się, że kontroler domeny ustanawia zaufanie za pomocą usługi Azure AD. Upewnij się, że konfiguracja godziny i daty na usłudze Azure AD i kontrolerze domeny są synchronizowane. |
| 13020 — usługa Azure AD nie może pobrać biletu Protokołu Kerberos w imieniu użytkownika, ponieważ nie zdefiniowano nazwy SPN serwera wewnętrznej bazy danych. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD a serwerem kontrolera domeny lub problem w konfiguracji czasu i daty na obu komputerach. Kontroler domeny odrzucił bilet protokołu Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji wewnętrznej bazy danych są poprawnie skonfigurowane, zwłaszcza konfiguracji SPN. Upewnij się, że usługa Azure AD jest domeną przyłączona do tej samej domeny co kontroler domeny, aby upewnić się, że kontroler domeny ustanawia zaufanie za pomocą usługi Azure AD. Upewnij się, że konfiguracja godziny i daty na usłudze Azure AD i kontrolerze domeny są synchronizowane. |
| 13022 — usługa Azure AD nie może uwierzytelnić użytkownika, ponieważ serwer wewnętrznej bazy danych odpowiada na próby uwierzytelniania Kerberos z błędem HTTP 401. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD a serwerem aplikacji wewnętrznej bazy danych lub problem w konfiguracji czasu i daty na obu komputerach. Serwer zaplecza odrzucił bilet protokołu Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji wewnętrznej bazy danych są poprawnie skonfigurowane. Upewnij się, że konfiguracja godziny i daty na usłudze Azure AD i serwerze aplikacji wewnętrznej bazy danych są synchronizowane. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z konfiguracjami delegowania ograniczonego protokołu Kerberos dla serwera proxy aplikacji](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Błędy użytkownika końcowego

Ta lista zawiera błędy, które użytkownicy końcowi mogą napotkać podczas próby uzyskania dostępu do aplikacji i niepowodzenia. 

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Witryna sieci Web nie może wyświetlić strony. | Użytkownik może uzyskać ten błąd podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli aplikacja jest aplikacją IWA. Zdefiniowana nazwa SPN dla tej aplikacji może być niepoprawna. W przypadku aplikacji IWA upewnij się, że nazwa SPN skonfigurowana dla tej aplikacji jest poprawna. |
| Witryna sieci Web nie może wyświetlić strony. | Użytkownik może uzyskać ten błąd podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli aplikacja jest aplikacją OWA. Może to być spowodowane przez jedną z następujących czynności:<br><li>Zdefiniowana nazwa SPN dla tej aplikacji jest niepoprawna. Upewnij się, że nazwa SPN skonfigurowana dla tej aplikacji jest poprawna.</li><li>Użytkownik, który próbował uzyskać dostęp do aplikacji, używa konta Microsoft, a nie odpowiedniego konta firmowego do logowania lub użytkownik jest użytkownikiem-gościem. Upewnij się, że użytkownik loguje się przy użyciu konta firmowego, które pasuje do domeny opublikowanej aplikacji. Użytkownicy kont Microsoft i gość nie mogą uzyskać dostępu do aplikacji IWA.</li><li>Użytkownik, który próbował uzyskać dostęp do aplikacji nie jest poprawnie zdefiniowany dla tej aplikacji po stronie lokalnej. Upewnij się, że ten użytkownik ma odpowiednie uprawnienia zdefiniowane dla tej aplikacji wewnętrznej bazy danych na komputerze lokalnym. |
| Nie można uzyskać dostępu do tej aplikacji firmowych. Nie masz dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się, aby przypisać użytkownika z dostępem do tej aplikacji. | Użytkownik może uzyskać ten błąd podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli do logowania się używa kont Microsoft zamiast konta firmowego. Użytkownicy-goście mogą również uzyskać ten błąd. Użytkownicy i goście kont Microsoft nie mogą uzyskiwać dostępu do aplikacji IWA. Upewnij się, że użytkownik loguje się przy użyciu konta firmowego, które pasuje do domeny opublikowanej aplikacji.<br><br>Być może nie przypisano użytkownika do tej aplikacji. Przejdź do karty **Aplikacja,** a następnie w obszarze **Użytkownicy i grupy**przypisz tę grupę użytkowników lub użytkowników do tej aplikacji. |
| Tej aplikacji firmowa nie można uzyskać dostępu w tej chwili. Spróbuj ponownie później... Limit czasu złącza. | Użytkownik może uzyskać ten błąd podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli nie są poprawnie zdefiniowane dla tej aplikacji po stronie lokalnej. Upewnij się, że użytkownicy mają odpowiednie uprawnienia zdefiniowane dla tej aplikacji wewnętrznej bazy danych na komputerze lokalnym. |
| Nie można uzyskać dostępu do tej aplikacji firmowych. Nie masz dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się, że użytkownik ma licencję na usługę Azure Active Directory Premium. | Użytkownik może uzyskać ten błąd podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli nie zostały one jawnie przypisane z licencją Premium przez administratora subskrybenta. Przejdź do karty **Licencje** usługi Active Directory subskrybenta i upewnij się, że temu użytkownikowi lub grupie użytkowników jest przypisana licencja Premium. |
| Nie można odnaleźć serwera o określonej nazwie hosta. | Użytkownik może uzyskać ten błąd podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli domena niestandardowa aplikacji nie jest poprawnie skonfigurowana. Upewnij się, że udostępniłeś certyfikat dla domeny i poprawnie skonfigurowałeś rekord DNS, wykonując czynności opisane w [temacie Praca z domenami niestandardowymi w serwerze proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Mój błąd nie został wymieniony tutaj

Jeśli wystąpi błąd lub problem z usługą Azure AD Application Proxy, którego nie ma na liście w tym przewodniku rozwiązywania problemów, chcielibyśmy o tym dowiedzieć się. Wyślij wiadomość e-mail do naszego [zespołu opinii](mailto:aadapfeedback@microsoft.com) ze szczegółami napotkanego błędu.

## <a name="see-also"></a>Zobacz też
* [Włączanie serwera proxy aplikacji dla usługi Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publikowanie aplikacji za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md)
* [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
* [Włączanie dostępu warunkowego](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
