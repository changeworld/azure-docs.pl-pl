---
title: Rozwiązywanie problemów z serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: W tym temacie omówiono rozwiązywanie problemów na serwerze Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f8c9c6be51a30fd4e30fedc85f8d17d16324391
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359899"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach
Jeśli wystąpią błędy podczas uzyskiwania dostępu do opublikowanej aplikacji lub publikowania aplikacji, sprawdź następujące opcje, aby zobaczyć, czy serwer Proxy aplikacji programu Microsoft Azure AD działa prawidłowo:

* Otwórz konsolę usług Windows, a następnie sprawdź, czy **łącznika serwera Proxy aplikacji usługi AAD Microsoft** usługa jest włączona i uruchomiona. Również można spojrzeć na stronie właściwości usługi Serwer Proxy aplikacji, jak pokazano na poniższej ilustracji:  
  ![Zrzut ekranu okna właściwości łącznika serwera Proxy aplikacji usługi AAD firmy Microsoft](./media/application-proxy-troubleshoot/connectorproperties.png)
* Otwórz Podgląd zdarzeń i poszukaj zdarzeń łącznika serwera Proxy aplikacji w **Dzienniki aplikacji i usług** > **Microsoft** > **AadApplicationProxy**  >  **Łącznika** > **administratora**.
* Jeśli to konieczne, bardziej szczegółowe dzienniki są dostępne przez [włączenie dzienników sesji łącznika serwera Proxy aplikacji](application-proxy-connectors.md#under-the-hood).

Aby uzyskać więcej informacji o narzędziu Azure AD rozwiązywania problemów, zobacz [narzędzia do rozwiązywania problemów do sprawdzania wymagań wstępnych sieci łącznika](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>Strona nie jest poprawnie renderowana.
Masz problemy z aplikacją renderowania lub działać poprawnie bez otrzymania określone komunikaty o błędach. Może to wystąpić, jeśli opublikowane Ścieżka artykułu, ale aplikacja wymaga zawartości, który istnieje poza tej ścieżki.

Na przykład w przypadku publikowania ścieżki `https://yourapp/app` , ale aplikacja wywołuje obrazów `https://yourapp/media`, nie będą renderowane. Upewnij się, że publikowanie aplikacji przy użyciu najwyższego poziomu ścieżki, konieczne jest uwzględnienie wszystkich odpowiedniej zawartości. W tym przykładzie będzie `http://yourapp/`.

Jeśli zmienisz ścieżce obejmują zawartość odwołania, ale nadal potrzebują użytkownicy powiązana z głębiej łączy w ścieżce, zobacz wpis w blogu [ustawienie łącze odpowiednie dla aplikacji serwera Proxy aplikacji w usłudze Azure AD dostęp do panelu i uruchamianie aplikacji usługi Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Błędy łącznika

Jeśli rejestracja zakończy się niepowodzeniem podczas instalacji Kreator łącznika, istnieją dwa sposoby, aby wyświetlić przyczynę błędu. Albo poszukaj w dzienniku zdarzeń w obszarze **aplikacji i usług Logs\Microsoft\AadApplicationProxy\Connector\Admin**, lub uruchomić następujące polecenie programu Windows PowerShell:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Po znalezieniu błąd łącznika z dziennika zdarzeń, użyj tej tabeli typowych błędów, aby rozwiązać ten problem:

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Rejestracja łącznika nie powiodło się: Upewnij się, że włączono serwer Proxy aplikacji w portalu zarządzania systemu Azure i poprawnie wprowadzono nazwę użytkownika usługi Active Directory i hasło. Błąd: "Wystąpił co najmniej jednego błędu." | Jeśli okno Rejestracja została zamknięta bez rejestrowania się w usłudze Azure AD, uruchom ponownie Kreatora łącznika i rejestrowanie łącznika. <br><br> Jeśli okno Rejestracja zostanie otwarty i natychmiast zamykane bez dzięki czemu możesz się zalogować, prawdopodobnie otrzymasz tego błędu. Ten błąd występuje, gdy występuje błąd sieci w Twoim systemie. Upewnij się, że jest możliwość nawiązywania połączeń z przeglądarki do publicznej witryny sieci Web i że porty są otwarte, jak to określono w [wymagania wstępne dotyczące serwera Proxy aplikacji](application-proxy-add-on-premises-application.md). |
| Wyczyść błędu są prezentowane w oknie rejestracji. Nie można kontynuować | Jeśli zostanie wyświetlony ten błąd, a następnie okno zostanie zamknięte, wprowadzono nieprawidłową nazwą użytkownika lub hasło. Spróbuj ponownie. |
| Rejestracja łącznika nie powiodło się: Upewnij się, że włączono serwer Proxy aplikacji w portalu zarządzania systemu Azure i poprawnie wprowadzono nazwę użytkownika usługi Active Directory i hasło. Błąd: 'AADSTS50059: Żadne informacje identyfikujące dzierżawy odnaleziony ani w żądaniu lub implikowana przez żaden podane poświadczenia i wyszukiwanie według usługi URI jednostki nie powiodła się. | Próbujesz zalogować się przy użyciu Account firmy Microsoft i nie domeny należącego do organizacji w katalogu, który próbujesz uzyskać dostęp. Upewnij się, że administrator jest częścią tej samej nazwy domeny jako domena dzierżawy, na przykład, jeśli domena usługi Azure AD to contoso.com, administratorem powinien być admin@contoso.com. |
| Nie można pobrać bieżące zasady wykonywania w uruchomionych skryptów PowerShell. | Jeśli instalacja łącznika nie powiedzie się, sprawdź, upewnij się, że zasady wykonywania programu PowerShell nie jest wyłączone. <br><br>1. Otwórz Edytor zasad grupy.<br>2. Przejdź do **konfiguracji komputera** > **Szablony administracyjne** > **składników Windows**  >   **Program Windows PowerShell** i kliknij dwukrotnie **Włącz wykonywanie skryptu**.<br>3. Zasady wykonywania może być ustawiony na **nieskonfigurowane** lub **włączone**. Jeśli ustawiono **włączone**, upewnij się, że w obszarze Opcje, zasady wykonywania jest ustawiony na **Zezwalaj na lokalne i zdalne podpisanych skryptów** lub **zezwolić na uruchamianie wszystkich skryptów**. |
| Łącznik nie może pobrać konfigurację. | Certyfikat klienta łącznika, który jest używany do uwierzytelniania, wygasł. Może to również nastąpić, jeśli masz zainstalowanego używasz serwera proxy łącznika. W takim przypadku łącznik nie może uzyskać dostęp do Internetu i nie będzie w stanie zapewnić aplikacje do użytkowników zdalnych. Odnowić relacji zaufania ręcznie przy użyciu `Register-AppProxyConnector` polecenia cmdlet programu Windows PowerShell. Jeśli Twój łącznik znajduje się za serwerem proxy, należy udzielić dostępu do Internetu dla konta łącznika "usługi sieciowej" i "system lokalny". Można to osiągnąć przez nadawania im praw do serwera Proxy lub zmieniając ustawienie w celu obejścia serwera proxy. |
| Rejestracja łącznika nie powiodło się: Upewnij się, że jesteś administratorem usługi Active Directory, aby zarejestrować łącznik aplikacji. Błąd: "Żądanie rejestracji zostało odrzucone." | Alias, którego próbujesz się zalogować przy użyciu nie jest administratorem tej domeny. Łącznik jest zawsze instalowany w katalogu, który jest właścicielem domeny użytkownika. Upewnij się, że konto administratora, które próbujesz zarejestrować się za pomocą ma co najmniej uprawnienia administratora aplikacji dzierżawy usługi Azure AD. |

## <a name="kerberos-errors"></a>Błędy protokołu Kerberos

W tej tabeli opisano bardziej typowych błędów, które pochodzą z protokołu Kerberos, instalacja i Konfiguracja i zawiera sugestie dotyczące rozwiązywania.

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Nie można pobrać bieżące zasady wykonywania w uruchomionych skryptów PowerShell. | Jeśli instalacja łącznika nie powiedzie się, sprawdź, upewnij się, że zasady wykonywania programu PowerShell nie jest wyłączone.<br><br>1. Otwórz Edytor zasad grupy.<br>2. Przejdź do **konfiguracji komputera** > **Szablony administracyjne** > **składników Windows**  >   **Program Windows PowerShell** i kliknij dwukrotnie **Włącz wykonywanie skryptu**.<br>3. Zasady wykonywania może być ustawiony na **nieskonfigurowane** lub **włączone**. Jeśli ustawiono **włączone**, upewnij się, że w obszarze Opcje, zasady wykonywania jest ustawiony na **Zezwalaj na lokalne i zdalne podpisanych skryptów** lub **zezwolić na uruchamianie wszystkich skryptów**. |
| 12008 — usługa azure AD Przekroczono maksymalną liczbę dozwolonych prób uwierzytelniania Kerberos serwera wewnętrznej bazy danych. | Ten błąd może wskazywać niepoprawną konfigurację między usługą Azure AD i serwera aplikacji wewnętrznej bazy danych lub problem w konfiguracji datę i godzinę na obu komputerach. Serwer zaplecza odrzucone biletu Kerberos utworzone przez usługę Azure AD. Sprawdź przez usługę Azure AD i serwera aplikacji zaplecza są skonfigurowane poprawnie. Upewnij się, że data i godzina konfigurację w usłudze Azure AD i serwera aplikacji zaplecza są zsynchronizowane. |
| 13016 — usługa azure AD nie można pobrać biletu protokołu Kerberos w imieniu użytkownika, ponieważ nie istnieje żadne nazwy UPN w tokenie krawędzi lub w pliku cookie dostępu. | Występuje problem z konfiguracją usługi STS. Napraw Konfiguracja oświadczenia nazwy UPN w usłudze STS. |
| 13019 — usługa azure AD nie można pobrać biletu protokołu Kerberos w imieniu użytkownika z powodu następującego błędu API ogólne. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD i serwera kontrolera domeny lub problem w konfiguracji datę i godzinę na obu komputerach. Kontroler domeny odrzucił biletu Kerberos utworzone przez usługę Azure AD. Sprawdź przez usługę Azure AD i serwera aplikacji zaplecza są skonfigurowane poprawnie, szczególnie konfiguracji nazwę SPN. Upewnij się, że usługa Azure AD jest przyłączony do tej samej domenie co kontroler domeny, aby upewnić się, że kontroler domeny ustanawia relację zaufania z usługą Azure AD. Upewnij się, że data i godzina konfigurację w usłudze Azure AD i kontrolera domeny są zsynchronizowane. |
| 13020 — usługa azure AD nie można pobrać biletu protokołu Kerberos w imieniu użytkownika, ponieważ nazwa SPN serwera wewnętrznej bazy danych nie jest zdefiniowana. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD i serwera kontrolera domeny lub problem w konfiguracji datę i godzinę na obu komputerach. Kontroler domeny odrzucił biletu Kerberos utworzone przez usługę Azure AD. Sprawdź przez usługę Azure AD i serwera aplikacji zaplecza są skonfigurowane poprawnie, szczególnie konfiguracji nazwę SPN. Upewnij się, że usługa Azure AD jest przyłączony do tej samej domenie co kontroler domeny, aby upewnić się, że kontroler domeny ustanawia relację zaufania z usługą Azure AD. Upewnij się, że data i godzina konfigurację w usłudze Azure AD i kontrolera domeny są zsynchronizowane. |
| 13022 — usługa azure AD nie może uwierzytelnić użytkownika, ponieważ serwer zaplecza odpowiada prób uwierzytelnienia Kerberos z powodu błędu HTTP 401. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD i serwera aplikacji wewnętrznej bazy danych lub problem w konfiguracji datę i godzinę na obu komputerach. Serwer zaplecza odrzucone biletu Kerberos utworzone przez usługę Azure AD. Sprawdź przez usługę Azure AD i serwera aplikacji zaplecza są skonfigurowane poprawnie. Upewnij się, że data i godzina konfigurację w usłudze Azure AD i serwera aplikacji zaplecza są zsynchronizowane. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z protokołu Kerberos ograniczone delegowanie konfiguracji dla serwera Proxy aplikacji](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Błędy użytkowników końcowych

Ta lista zawiera błędy, które użytkownicy końcowi mogą wystąpić, gdy użytkownik próbuje uzyskać dostęp do aplikacji i zakończyć się niepowodzeniem. 

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Witryna sieci Web nie można wyświetlić strony. | Błąd ten może wystąpić użytkownika, podczas próby dostępu do aplikacji, która została opublikowana, jeśli aplikacja jest aplikacją IWA. Zdefiniowane nazwy SPN dla tej aplikacji mogą być niepoprawne. W przypadku aplikacji IWA upewnij się, czy nazwa SPN skonfigurowana dla tej aplikacji jest poprawna. |
| Witryna sieci Web nie można wyświetlić strony. | Błąd ten może wystąpić użytkownika, podczas próby dostępu do aplikacji, która została opublikowana, jeśli aplikacja jest aplikacją programu OWA. Może to być spowodowane przez jedną z następujących czynności:<br><li>Zdefiniowane nazwy SPN dla tej aplikacji jest nieprawidłowy. Upewnij się, że nazwy SPN skonfigurowana dla tej aplikacji jest poprawna.</li><li>Użytkownik próbował uzyskać dostęp do aplikacji jest logowanie przy użyciu konta Microsoft, a nie prawidłowego konta firmowego lub użytkownik jest użytkownikiem-gościem. Upewnij się, że użytkownik loguje się przy użyciu swojego konta firmowego, zgodną z domeną opublikowanej aplikacji. Użytkownicy Account firmy Microsoft i gości nie mają dostępu Zintegrowane aplikacje.</li><li>Użytkownik próbował uzyskać dostęp do aplikacji nie jest poprawnie zdefiniowany dla tej aplikacji na stronie lokalnie. Upewnij się, że ten użytkownik ma odpowiednie uprawnienia, zgodnie z definicją dla tej aplikacji wewnętrznej bazy danych na komputerze lokalnym. |
| Nie można uzyskać dostępu do tej aplikacji firmowej. Nie masz uprawnień dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się przypisać użytkownika z dostępem do tej aplikacji. | Błąd ten może wystąpić użytkownika, podczas próby dostępu do aplikacji, którą można opublikować, jeśli używają kont Microsoft do logowania zamiast swojego konta firmowego. Użytkownicy-goście mogą również otrzymać ten błąd. Użytkownicy Account firmy Microsoft i gości nie mają dostępu Zintegrowane aplikacje. Upewnij się, że użytkownik loguje się przy użyciu swojego konta firmowego, zgodną z domeną opublikowanej aplikacji.<br><br>Może nie przypisano użytkowników do tej aplikacji. Przejdź do **aplikacji** kartę, a następnie w obszarze **użytkowników i grup**, przypisz tego użytkownika lub grupę użytkowników do tej aplikacji. |
| Tej aplikacji firmowych nie jest dostępny w tej chwili. Spróbuj ponownie później... Upłynął limit czasu łącznika. | Błąd ten może wystąpić użytkownika, podczas próby uzyskania dostępu do aplikacji, którą możesz opublikować, jeśli nie są prawidłowo zdefiniowane dla tej aplikacji po stronie w środowisku lokalnym. Upewnij się, czy użytkownicy mają odpowiednie uprawnienia, zgodnie z definicją dla tej aplikacji wewnętrznej bazy danych na komputerze lokalnym. |
| Nie można uzyskać dostępu do tej aplikacji firmowej. Nie masz uprawnień dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się, że użytkownik ma licencję usługi Azure Active Directory — wersja Premium lub podstawowa. | Błąd ten może wystąpić użytkownika, podczas próby dostępu do aplikacji, publikowania, jeśli nie zostały one jawnie przypisane z licencją Premium/podstawowa przez administratora subskrybenta. Przejdź do usługi Active Directory subskrybenta **licencji** kartę i upewnij się, że ten użytkownik lub grupa użytkowników ma przypisanej licencji wersji Premium lub podstawowa. |
| Nie można odnaleźć serwera o nazwie określonym hoście. | Błąd ten może wystąpić użytkownika, podczas próby uzyskania dostępu do aplikacji, którą możesz opublikować, jeśli domeny niestandardowej aplikacji nie jest poprawnie skonfigurowany. Upewnij się, został przekazany certyfikat dla domeny i poprawnie skonfigurowane rekord DNS, wykonując kroki opisane w [Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Moje błąd nie został wymieniony w tym miejscu

Jeśli wystąpi błąd lub problem z aplikacji serwera Proxy Azure AD, nie ma na liście w tym przewodniku rozwiązywania problemów, prosimy o poświęcenie czekamy na jego temat. Wyślij wiadomość e-mail do naszej [zespołu opinii](mailto:aadapfeedback@microsoft.com) ze szczegółowymi informacjami o błędzie.

## <a name="see-also"></a>Zobacz także
* [Włącz serwer Proxy aplikacji usługi Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji](application-proxy-add-on-premises-application.md)
* [Włącz logowanie jednokrotne](application-proxy-configure-single-sign-on-with-kcd.md)
* [Włączanie dostępu warunkowego](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
