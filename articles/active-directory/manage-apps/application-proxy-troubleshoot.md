---
title: Rozwiązywanie problemów z serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: W tym temacie omówiono rozwiązywanie problemów na serwerze Proxy aplikacji usługi Azure AD.
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
ms.openlocfilehash: 6cf5b6d4cadf4592fbaa19c89a969b20e9eb69b6
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424263"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach

W przypadku rozwiązywania problemów z serwerem proxy aplikacji zalecamy rozpoczęcie od przejrzenia przepływu rozwiązywania problemów, [Debugowanie problemów dotyczących łącznika serwera proxy aplikacji](application-proxy-debug-connectors.md)w celu ustalenia, czy łączniki serwera proxy aplikacji są prawidłowo skonfigurowane. Jeśli nadal występują problemy z nawiązywaniem połączenia z aplikacją, postępuj zgodnie z przepływem rozwiązywania problemów, aby [debugować problemy aplikacji serwera proxy aplikacji](application-proxy-debug-apps.md).

Jeśli wystąpią błędy podczas uzyskiwania dostępu do opublikowanej aplikacji lub publikowania aplikacji, sprawdź następujące opcje, aby zobaczyć, czy serwer Proxy aplikacji programu Microsoft Azure AD działa prawidłowo:

* Otwórz konsolę usług systemu Windows. Sprawdź, czy usługa **łącznika serwera proxy aplikacji usługi Microsoft AAD** jest włączona i uruchomiona. Również można spojrzeć na stronie właściwości usługi Serwer Proxy aplikacji, jak pokazano na poniższej ilustracji:  
  ![Zrzut ekranu okna właściwości łącznika serwera Proxy aplikacji usługi AAD firmy Microsoft](./media/application-proxy-troubleshoot/connectorproperties.png)
* Otwórz Podgląd zdarzeń i poszukaj zdarzeń łącznika serwera Proxy aplikacji w **Dzienniki aplikacji i usług** > **Microsoft** > **AadApplicationProxy**  >  **Łącznika** > **administratora**.
* Jeśli to konieczne, bardziej szczegółowe dzienniki są dostępne przez [włączenie dzienników sesji łącznika serwera Proxy aplikacji](application-proxy-connectors.md#under-the-hood).

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
| Rejestracja łącznika nie powiodła się: Upewnij się, że włączono serwer proxy aplikacji w usłudze Azure portal zarządzania i wprowadzono poprawną nazwę użytkownika Active Directory i hasło. Błąd: Wystąpił co najmniej jeden błąd. | Jeśli okno Rejestracja została zamknięta bez rejestrowania się w usłudze Azure AD, uruchom ponownie Kreatora łącznika i rejestrowanie łącznika. <br><br> Jeśli okno rejestracja zostanie otwarte, a następnie natychmiast zamknięte bez zezwolenia na zalogowanie, prawdopodobnie ten błąd wystąpi. Ten błąd występuje, gdy występuje błąd sieci w Twoim systemie. Upewnij się, że można nawiązać połączenie z przeglądarki z publiczną witryną sieci Web i że porty są otwarte zgodnie z [wymaganiami wstępnymi serwera proxy aplikacji](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Wyczyść błędu są prezentowane w oknie rejestracji. Nie można kontynuować | Jeśli zostanie wyświetlony ten błąd, a następnie okno zostanie zamknięte, wprowadzono nieprawidłową nazwą użytkownika lub hasło. Spróbuj ponownie. |
| Rejestracja łącznika nie powiodła się: Upewnij się, że włączono serwer proxy aplikacji w usłudze Azure portal zarządzania i wprowadzono poprawną nazwę użytkownika Active Directory i hasło. Błąd: 'AADSTS50059: Nie znaleziono informacji identyfikacyjnych dzierżawy w żądaniu lub IMPLIKOWANYCH przez żadne podane poświadczenia i wyszukiwanie według identyfikatora URI nazwy głównej usługi nie powiodło się. | Podjęto próbę zalogowania się przy użyciu konta Microsoft, a nie domeny, która jest częścią identyfikatora organizacji katalogu, do którego próbujesz uzyskać dostęp. Upewnij się, że administrator jest częścią tej samej nazwy domeny jako domena dzierżawy, na przykład, jeśli domena usługi Azure AD to contoso.com, administratorem powinien być admin@contoso.com. |
| Nie można pobrać bieżące zasady wykonywania w uruchomionych skryptów PowerShell. | Jeśli instalacja łącznika nie powiedzie się, upewnij się, że zasady wykonywania programu PowerShell nie są wyłączone. <br><br>1. Otwórz Edytor zasad grupy.<br>2. Przejdź do **konfiguracji komputera** > **Szablony administracyjne** > **składników Windows**  >   **Program Windows PowerShell** i kliknij dwukrotnie **Włącz wykonywanie skryptu**.<br>3. Zasady wykonywania może być ustawiony na **nieskonfigurowane** lub **włączone**. Jeśli ustawiono **włączone**, upewnij się, że w obszarze Opcje, zasady wykonywania jest ustawiony na **Zezwalaj na lokalne i zdalne podpisanych skryptów** lub **zezwolić na uruchamianie wszystkich skryptów**. |
| Łącznik nie może pobrać konfigurację. | Certyfikat klienta łącznika, który jest używany do uwierzytelniania, wygasł. Może to również nastąpić, jeśli masz zainstalowanego używasz serwera proxy łącznika. W takim przypadku łącznik nie może uzyskać dostęp do Internetu i nie będzie w stanie zapewnić aplikacje do użytkowników zdalnych. Odnowić relacji zaufania ręcznie przy użyciu `Register-AppProxyConnector` polecenia cmdlet programu Windows PowerShell. Jeśli Twój łącznik znajduje się za serwerem proxy, należy udzielić dostępu do Internetu dla konta łącznika "usługi sieciowej" i "system lokalny". Można to osiągnąć przez nadawania im praw do serwera Proxy lub zmieniając ustawienie w celu obejścia serwera proxy. |
| Rejestracja łącznika nie powiodła się: Upewnij się, że jesteś administratorem aplikacji Active Directory, aby zarejestrować łącznik. Błąd: "Żądanie rejestracji zostało odrzucone." | Alias, którego próbujesz się zalogować przy użyciu nie jest administratorem tej domeny. Łącznik jest zawsze instalowany w katalogu, który jest właścicielem domeny użytkownika. Upewnij się, że konto administratora, za pomocą którego próbujesz się zalogować, ma co najmniej uprawnienia administratora aplikacji do dzierżawy usługi Azure AD. |
| Łącznik nie może nawiązać połączenia z usługą ze względu na problemy z siecią. Łącznik próbował uzyskać dostęp do następującego adresu URL. | Łącznik nie może nawiązać połączenia z usługą serwera proxy aplikacji w chmurze. Może się tak zdarzyć, jeśli masz regułę zapory blokującą połączenie. Upewnij się, że masz dozwolony dostęp do poprawnych portów i adresów URL wymienionych w [wymaganiach wstępnych serwera proxy aplikacji](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

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
| Witryna sieci Web nie można wyświetlić strony. | Błąd ten może wystąpić użytkownika, podczas próby dostępu do aplikacji, która została opublikowana, jeśli aplikacja jest aplikacją programu OWA. Może to być spowodowane przez jedną z następujących czynności:<br><li>Zdefiniowane nazwy SPN dla tej aplikacji jest nieprawidłowy. Upewnij się, że nazwy SPN skonfigurowana dla tej aplikacji jest poprawna.</li><li>Użytkownik próbował uzyskać dostęp do aplikacji jest logowanie przy użyciu konta Microsoft, a nie prawidłowego konta firmowego lub użytkownik jest użytkownikiem-gościem. Upewnij się, że użytkownik loguje się przy użyciu swojego konta firmowego, zgodną z domeną opublikowanej aplikacji. Użytkownicy Account firmy Microsoft i gości nie mają dostępu Zintegrowane aplikacje.</li><li>Użytkownik próbujący uzyskać dostęp do aplikacji nie został poprawnie zdefiniowany dla tej aplikacji na stronie lokalnej. Upewnij się, że ten użytkownik ma odpowiednie uprawnienia zdefiniowane dla tej aplikacji zaplecza na maszynie lokalnej. |
| Nie można uzyskać dostępu do tej aplikacji firmowej. Nie masz uprawnień dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się przypisać użytkownika z dostępem do tej aplikacji. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji w przypadku korzystania z kont Microsoft zamiast konta firmowego w celu zalogowania się. Użytkownicy-goście mogą również otrzymać ten błąd. Użytkownicy Account firmy Microsoft i gości nie mają dostępu Zintegrowane aplikacje. Upewnij się, że użytkownik loguje się przy użyciu swojego konta firmowego, zgodną z domeną opublikowanej aplikacji.<br><br>Może nie przypisano użytkowników do tej aplikacji. Przejdź do **aplikacji** kartę, a następnie w obszarze **użytkowników i grup**, przypisz tego użytkownika lub grupę użytkowników do tej aplikacji. |
| Tej aplikacji firmowych nie jest dostępny w tej chwili. Spróbuj ponownie później... Upłynął limit czasu łącznika. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli nie są one prawidłowo zdefiniowane dla tej aplikacji na stronie lokalnej. Upewnij się, że użytkownicy mają odpowiednie uprawnienia zdefiniowane dla tej aplikacji zaplecza na maszynie lokalnej. |
| Nie można uzyskać dostępu do tej aplikacji firmowej. Nie masz uprawnień dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się, że użytkownik ma licencję usługi Azure Active Directory — wersja Premium lub podstawowa. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli nie zostały one jawnie przypisane do licencji Premium/Basic przez administratora subskrybenta. Przejdź do usługi Active Directory subskrybenta **licencji** kartę i upewnij się, że ten użytkownik lub grupa użytkowników ma przypisanej licencji wersji Premium lub podstawowa. |
| Nie można znaleźć serwera o określonej nazwie hosta. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli domena niestandardowa aplikacji nie jest prawidłowo skonfigurowana. Upewnij się, że został przekazany certyfikat dla domeny i poprawnie skonfigurowano rekord DNS, wykonując kroki opisane w temacie [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Moje błąd nie został wymieniony w tym miejscu

Jeśli wystąpi błąd lub problem z aplikacji serwera Proxy Azure AD, nie ma na liście w tym przewodniku rozwiązywania problemów, prosimy o poświęcenie czekamy na jego temat. Wyślij wiadomość e-mail do naszej [zespołu opinii](mailto:aadapfeedback@microsoft.com) ze szczegółowymi informacjami o błędzie.

## <a name="see-also"></a>Zobacz także
* [Włącz serwer Proxy aplikacji usługi Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji](application-proxy-add-on-premises-application.md)
* [Włącz logowanie jednokrotne](application-proxy-configure-single-sign-on-with-kcd.md)
* [Włącz dostęp warunkowy](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
