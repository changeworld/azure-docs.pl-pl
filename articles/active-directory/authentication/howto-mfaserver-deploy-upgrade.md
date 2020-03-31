---
title: Uaktualnianie usługi Azure MFA Server — usługa Azure Active Directory
description: Kroki i wskazówki dotyczące uaktualniania serwera uwierzytelniania wieloskładnikowego platformy Azure do nowszej wersji.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 756c45541907c52448805376e1b054180c31fdf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848106"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgrade to the latest Azure Multi-Factor Authentication Server (Uaktualnianie do najnowszej wersji serwera Azure Multi-Factor Authentication)

W tym artykule oproszą Cię proces uaktualniania serwera wieloskładnikowego usługi Azure Multi-Factor Authentication (MFA) w wersji 6.0 lub nowszej. Jeśli chcesz uaktualnić starą wersję agenta PhoneFactor, zapoznaj się [z poleceniem Uaktualnienie agenta PhoneFactor do serwera uwierzytelniania wieloskładnikowego platformy Azure](howto-mfaserver-deploy-upgrade-pf.md).

W przypadku uaktualniania z wersji 6.x lub starszej do wersji 7.x lub nowszej wszystkie składniki zmieniają się z platformy .NET 2.0 na .NET 4.5. Wszystkie składniki wymagają również programu Microsoft Visual C++ 2015 Redystrybucjowa aktualizacja 1 lub nowsza. Instalator serwera usługi MFA server instaluje wersje x86 i x64 tych składników, jeśli nie są jeszcze zainstalowane. Jeśli portal użytkownika i usługa sieci Web aplikacji mobilnych działają na oddzielnych serwerach, należy zainstalować te pakiety przed uaktualnieniem tych składników. W [Centrum pobierania Microsoft](https://www.microsoft.com/download/)można wyszukać najnowszą redystrybucjową aktualizację programu Microsoft Visual C++ 2015 . 

> [!IMPORTANT]
> Od 1 lipca 2019 r. firma Microsoft nie będzie już oferować serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy chcieliby wymagać uwierzytelniania wieloskładnikowego od swoich użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego platformy Azure w chmurze. Obecni klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

Krok uaktualnienia w skrócie:

* Uaktualnianie serwerów usługi Azure MFA (podwładnych, a następnie wzorca)
* Uaktualnianie wystąpień portalu użytkownika
* Uaktualnianie wystąpień karty usług AD FS

## <a name="upgrade-azure-mfa-server"></a>Uaktualnianie serwera usługi Azure MFA

1. Użyj instrukcji w [pobierz serwer uwierzytelniania wieloskładnikowego platformy Azure,](howto-mfaserver-deploy.md#download-the-mfa-server) aby uzyskać najnowszą wersję instalatora serwera usługi Azure MFA.
2. Utwórz kopię zapasową pliku danych serwera usługi MFA znajdującego się w witrynie C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (przy założeniu domyślnej lokalizacji instalacji) na głównym serwerze usługi MFA.
3. Jeśli uruchamiasz wiele serwerów w celu uzyskania wysokiej dostępności, zmień systemy klienckie uwierzytelniane na serwerze usługi MFA, aby przestały wysyłać ruch do serwerów, które są uaktualniane. Jeśli używasz modułu równoważenia obciążenia, usuń podrzędny serwer usługi MFA z modułu równoważenia obciążenia, wykonaj uaktualnienie, a następnie dodaj serwer z powrotem do farmy.
4. Uruchom nowy instalator na każdym serwerze usługi MFA. Najpierw uaktualnij serwery podrzędne, ponieważ mogą odczytać stary plik danych replikowany przez wzorzec.

   > [!NOTE]
   > Podczas uaktualniania serwera należy go usunąć z dowolnego obciążenia lub udostępniania ruchu z innymi serwerami usługi MFA.
   >
   > Przed uruchomieniem instalatora nie trzeba odinstalowywać bieżącego serwera usługi MFA. Instalator wykonuje uaktualnienie w miejscu. Ścieżka instalacji jest pobierana z rejestru z poprzedniej instalacji, więc instaluje się w tej samej lokalizacji (na przykład C:\Program Files\Multi-Factor Authentication Server).
  
5. Jeśli zostanie wyświetlony monit o zainstalowanie pakietu aktualizacji redystrybucyjnej programu Microsoft Visual C++ 2015, zaakceptuj ten monit. Instalowane są zarówno wersje x86, jak i x64 pakietu.
6. Jeśli używasz pakietu SDK usługi sieci Web, zostanie wyświetlony monit o zainstalowanie nowego pakietu SDK usługi sieci Web. Podczas instalowania nowego pakietu SDK usługi sieci Web upewnij się, że nazwa katalogu wirtualnego jest zgodna z wcześniej zainstalowanym katalogiem wirtualnym (na przykład MultiFactorAuthWebServiceSdk).
7. Powtórz kroki na wszystkich serwerach podrzędnych. Promuj jednego z podwładnych, aby był nowym wzorcem, a następnie uaktualnij stary serwer główny.

## <a name="upgrade-the-user-portal"></a>Uaktualnianie portalu użytkownika

Przed przejściem do tej sekcji należy ukończyć uaktualnienie serwerów usługi MFA.

1. Utwórz kopię zapasową pliku web.config znajdującego się w katalogu wirtualnym lokalizacji instalacji portalu użytkownika (na przykład C:\inetpub\wwwroot\MultiFactorAuth). Jeśli wprowadzono jakiekolwiek zmiany w motywie domyślnym, utwórz kopię zapasową folderu App_Themes\Default. Lepiej jest utworzyć kopię folderu Domyślne i utworzyć nowy motyw niż zmienić motyw domyślny.
2. Jeśli portal użytkownika działa na tym samym serwerze co inne składniki serwera usługi MFA, instalacja serwera usługi MFA monituje o zaktualizowanie portalu użytkownika. Zaakceptuj monit i zainstaluj aktualizację Portalu użytkownika. Sprawdź, czy nazwa katalogu wirtualnego jest zgodna z wcześniej zainstalowanym katalogiem wirtualnym (na przykład MultiFactorAuth).
3. Jeśli portal użytkownika znajduje się na własnym serwerze, skopiuj plik MultiFactorAuthenticationUserPortalSetup64.msi z lokalizacji instalacji jednego z serwerów usługi MFA i umieść go na serwerze sieci Web Portalu użytkownika. Uruchom instalatora.

   Jeśli wystąpi błąd informujący, że wymagana jest redystrybucjowa aktualizacja 1 lub nowsza programu Microsoft Visual C++ 2015", pobierz i zainstaluj najnowszy pakiet aktualizacji z [Centrum pobierania Microsoft](https://www.microsoft.com/download/). Zainstaluj zarówno wersje x86, jak i x64.

4. Po zainstalowaniu zaktualizowanego oprogramowania Portalu użytkownika porównaj kopię zapasową web.config wykonano w kroku 1 z nowym plikiem web.config. Jeśli w nowym pliku web.config nie istnieją żadne nowe atrybuty, skopiuj kopię zapasową web.config do katalogu wirtualnego, aby zastąpić nowy. Inną opcją jest skopiowanie/wklejenie wartości appSettings i adresu URL SDK usługi sieci Web z pliku kopii zapasowej do nowego pliku web.config.

Jeśli masz portal użytkownika na wielu serwerach, powtórz instalację na wszystkich z nich.

## <a name="upgrade-the-mobile-app-web-service"></a>Uaktualnianie usługi sieci Web aplikacji mobilnej

> [!NOTE]
> Podczas uaktualniania z wersji usługi Azure MFA Server starszej niż 8.0 do 8.0+ następnie usługę sieci web aplikacji mobilnej można odinstalować po uaktualnieniu

## <a name="upgrade-the-ad-fs-adapters"></a>Uaktualnianie kart usług AD FS

Przed przejściem do tej sekcji należy ukończyć uaktualnienie serwerów usługi MFA i portalu użytkownika.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Jeśli usługa MFA działa na innych serwerach niż usługi AD FS

Te instrukcje mają zastosowanie tylko wtedy, gdy serwer uwierzytelniania wieloskładnikowego jest uruchamiany oddzielnie od serwerów usług AD FS. Jeśli obie usługi działają na tych samych serwerach, pomiń tę sekcję i przejdź do kroków instalacji. 

1. Zapisz kopię pliku MultiFactorAuthenticationAdfsAdapter.config zarejestrowanego w plikach AD FS lub wyeksportuj konfigurację za pomocą następującego polecenia programu PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Nazwa karty to "WindowsAzureMultiFactorAuthentication" lub "AzureMfaServerAuthentication" w zależności od wcześniej zainstalowanej wersji.
2. Skopiuj następujące pliki z lokalizacji instalacji serwera usługi MFA na serwery usług AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Edytuj skrypt Register-MultiFactorAuthenticationAdfsAdapter.ps1, `-ConfigurationFilePath [path]` dodając na `Register-AdfsAuthenticationProvider` końcu polecenia. Zastąp *[ścieżkę]* pełną ścieżką do pliku MultiFactorAuthenticationAdfsAdapter.config lub pliku konfiguracyjnego wyeksportowanego w poprzednim kroku.

   Sprawdź atrybuty w nowym Pliku MultiFactorAuthenticationAdfsAdapter.config, aby sprawdzić, czy pasują do starego pliku konfiguracyjnego. Jeśli w nowej wersji dodano lub usunięto jakieś atrybuty, skopiuj wartości atrybutów ze starego pliku konfiguracyjnego do nowego lub zmodyfikuj stary plik konfiguracyjny, aby był zgodny.

### <a name="install-new-ad-fs-adapters"></a>Instalowanie nowych kart usług AD FS

> [!IMPORTANT]
> Użytkownicy nie będą musieli przeprowadzać weryfikacji dwuetapowej podczas kroków 3-8 tej sekcji. Jeśli w wielu klastrach skonfigurowano usługi AD FS, można usunąć, uaktualnić i przywrócić każdy klaster w farmie niezależnie od innych klastrów, aby uniknąć przestojów.

1. Usuń niektóre serwery usług AD FS z farmy. Zaktualizuj te serwery, gdy pozostałe są nadal uruchomione.
2. Zainstaluj nową kartę usług AD FS na każdym serwerze usuniętym z farmy usług AD FS. Jeśli serwer usługi MFA jest zainstalowany na każdym serwerze usług AD FS, można aktualizować za pośrednictwem środowiska użytkownika administratora serwera usługi MFA. W przeciwnym razie należy zaktualizować przez uruchomienie MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Jeśli wystąpi błąd informujący, że wymagana jest redystrybucjowa aktualizacja 1 lub nowsza programu Microsoft Visual C++ 2015", pobierz i zainstaluj najnowszy pakiet aktualizacji z [Centrum pobierania Microsoft](https://www.microsoft.com/download/). Zainstaluj zarówno wersje x86, jak i x64.

3. Przejdź do strony**Zasady** > uwierzytelniania **usług AD FS** > **Edytuj globalne zasady uwierzytelniania multigeneracyjnego**. Odznacz **nierozznaczone WindowsAzureMultiFactorAuthentication** lub **AzureMFAServerAuthentication** (w zależności od bieżącej wersji zainstalowanej).

   Po zakończeniu tego kroku weryfikacja dwuetapowa za pośrednictwem serwera usługi MFA nie jest dostępna w tym klastrze usług AD FS, dopóki nie ukończysz kroku 8.

4. Wyrejestruj starszą wersję karty usług AD FS, uruchamiając skrypt Programu PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Upewnij się, że *parametr -Name* ("WindowsAzureMultiFactorAuthentication" lub "AzureMFAServerAuthentication") jest zgodny z nazwą wyświetlaną w kroku 3. Dotyczy to wszystkich serwerów w tym samym klastrze usług AD FS, ponieważ istnieje konfiguracja centralna.
5. Zarejestruj nową kartę usług AD FS, uruchamiając skrypt Programu PowerShell Register-MultiFactorAuthenticationAdfsAdapter.ps1. Dotyczy to wszystkich serwerów w tym samym klastrze usług AD FS, ponieważ istnieje konfiguracja centralna.
6. Uruchom ponownie usługę AD FS na każdym serwerze usuniętym z farmy usług AD FS.
7. Dodaj zaktualizowane serwery z powrotem do farmy usług AD FS i usuń inne serwery z farmy.
8. Przejdź do strony**Zasady** > uwierzytelniania **usług AD FS** > **Edytuj globalne zasady uwierzytelniania multigeneracyjnego**. Sprawdź **AzureMfaServerAuthentication**.
9. Powtórz krok 2, aby zaktualizować serwery usunięte z farmy usług AD FS i ponownie uruchomić usługę AD FS na tych serwerach.
10. Dodaj te serwery z powrotem do farmy usług AD FS.

## <a name="next-steps"></a>Następne kroki

* Przykłady [scenariuszy zaawansowanych za pomocą uwierzytelniania wieloskładnikowego platformy Azure i sieci VPN innych firm](howto-mfaserver-nps-vpn.md)

* [Synchronizowanie serwera usługi MFA server z usługą Active Directory systemu Windows Server](howto-mfaserver-dir-ad.md)

* [Konfigurowanie uwierzytelniania systemu Windows](howto-mfaserver-windows.md) dla aplikacji
