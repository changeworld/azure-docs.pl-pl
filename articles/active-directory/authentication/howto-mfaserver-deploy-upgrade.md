---
title: Uaktualnianie serwera usługi Azure MFA — usługa Azure Active Directory
description: Kroki i wskazówki dotyczące uaktualniania serwera usługi Azure Multi-Factor Authentication do nowszej wersji.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcafa3e2f2665e84856b80cc0911d2e83df1aa65
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057341"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Uaktualnienie do najnowszej serwera usługi Azure Multi-Factor Authentication

W tym artykule opisano Cię przez proces uaktualniania serwera Azure Multi-Factor Authentication (MFA) w wersji 6.0 lub nowszej. Jeśli zachodzi potrzeba uaktualniania starszej wersji agenta PhoneFactor, zobacz [uaktualnić agenta PhoneFactor do serwera Azure Multi-Factor Authentication](howto-mfaserver-deploy-upgrade-pf.md).

Jeśli wykonujesz uaktualnienie z 6.x lub w starszych do v7.x lub nowszej, zmienione wszystkie składniki programu .NET 2.0 .NET 4.5. Wszystkie składniki również wymagają programu Microsoft Visual C++ 2015 Redistributable Update 1 lub nowszym. Instalator serwera usługi MFA instaluje x86 i x64 wersje tych składników, jeśli nie są już zainstalowane. Jeśli Portal użytkowników i usługa sieci Web aplikacji mobilnej są uruchomione na oddzielnych serwerach, należy zainstalować te pakiety przed rozpoczęciem uaktualniania tych składników. Możesz wyszukać najnowszą aktualizację programu Microsoft Visual C++ 2015 Redistributable na [Microsoft Download Center](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> Począwszy od 1 lipca 2019 firma Microsoft będzie oferować już serwer MFA w przypadku nowych wdrożeń. Nowi klienci, którzy chcesz wymagać uwierzytelniania wieloskładnikowego od użytkowników należy używać oparte na chmurze usługi Azure Multi-Factor Authentication. Istniejący klienci, którzy aktywowali usługę MFA Server przed 1 lipca będzie można pobrać najnowszą wersję, a przyszłe aktualizacje i Generuj poświadczenia aktywacji w zwykły sposób.

Kroki uaktualnienia w skrócie:

* Uaktualnianie serwerów usługi Azure MFA (elementów podrzędnych, a następnie główny)
* Uaktualnij wystąpienia portalu użytkowników
* Uaktualnij wystąpienia adaptera AD FS

## <a name="upgrade-azure-mfa-server"></a>Uaktualnianie serwera Azure MFA

1. Postępuj zgodnie z instrukcjami w [pobieranie serwera Azure Multi-Factor Authentication](howto-mfaserver-deploy.md#download-the-mfa-server) można pobrać najnowszą wersję Instalatora serwera usługi Azure MFA.
2. Wykonaj kopię zapasową pliku danych serwera usługi MFA, znajdującym się w C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (zakładając, że domyślna lokalizacja instalacji) na głównym serwera MFA.
3. Po uruchomieniu wielu serwerów w celu zapewnienia wysokiej dostępności, należy zmienić klienckich systemów, które przeprowadzają uwierzytelnianie na serwerze usługi MFA, tak, aby ich zatrzymać wysyłanie ruchu do serwerów, które są uaktualnianie. Użycie modułu równoważenia obciążenia, Usuń podrzędny serwer MFA z modułu równoważenia obciążenia wykonaj uaktualnienia, a następnie dodaj serwer do farmy.
4. Uruchom nowy Instalator na każdym serwerze usługi MFA. Najpierw uaktualnić serwery podrzędne, ponieważ będzie mogła ją odczytać starego pliku danych jest replikowana za wzorcem.

   > [!NOTE]
   > Podczas uaktualniania serwera powinny być usunięte z dowolnej Równoważenie obciążenia ruchu do udostępniania z innymi serwerami usługi MFA.
   >
   > Nie ma potrzeby odinstalowania bieżącego serwera MFA przed uruchomieniem Instalatora. Instalator przeprowadza uaktualnienie w miejscu. Ścieżka instalacji jest pobierana z rejestru z poprzedniej instalacji, dlatego instalacji w tej samej lokalizacji (na przykład C:\Program Files\Multi-Factor Authentication Server).
  
5. Jeśli zostanie wyświetlony monit, aby zainstalować pakiet aktualizacji programu Microsoft Visual C++ 2015 Redistributable, należy zaakceptować monit. X86 i x64 wersje pakietu są instalowane.
6. Jeśli używasz zestawu SDK usługi sieci Web, pojawia się monit o zainstalowanie nowego zestawu SDK usługi sieci Web. Po zainstalowaniu nowego zestawu SDK usługi sieci Web, upewnij się, czy nazwa katalogu wirtualnego jest zgodna zainstalowanych wcześniej katalogu wirtualnego (na przykład MultiFactorAuthWebServiceSdk).
7. Powtórz kroki na wszystkich serwerach podrzędnych. Podwyższ poziom jednego z elementów podrzędnych do nowego wzorca, a następnie uaktualnić starego serwera głównego.

## <a name="upgrade-the-user-portal"></a>Uaktualnianie portalu użytkowników

Wykonaj uaktualnienie serwerów MFA przed skierowaniem ich do tej sekcji.

1. Wykonaj kopię zapasową pliku web.config, który znajduje się w katalogu wirtualnego w lokalizacji instalacji portalu użytkowników (np. C:\inetpub\wwwroot\MultiFactorAuth). Jeśli wszystkie zmiany wprowadzono motyw domyślny, należy wykonać kopię zapasową folderu App_Themes\Default także. Zaleca się utworzyć kopię domyślnego folderu i Utwórz nowy motyw niż Aby zmienić motyw domyślny.
2. Jeśli Portal użytkowników jest uruchomiony na tym samym serwerze co inne składniki serwera usługi MFA, instalacji serwera MFA monit Aktualizuj Portal użytkowników. Zaakceptować monit i zainstaluj aktualizację portalu użytkowników. Sprawdź, czy nazwa katalogu wirtualnego jest zgodna zainstalowanych wcześniej katalogu wirtualnego (na przykład MultiFactorAuth).
3. W przypadku portalu użytkowników na własnym serwerze, skopiuj plik MultiFactorAuthenticationUserPortalSetup64.msi z lokalizacji instalacji jednego z serwerów usługi MFA i umieścić go na serwerze sieci web portalu użytkowników. Uruchom Instalatora.

   Jeśli błąd wystąpi komunikatem, "Microsoft Visual C++ 2015 Redistributable Update 1 lub nowszy jest wymagany," Pobierz i zainstaluj najnowszy pakiet aktualizacji z [Microsoft Download Center](https://www.microsoft.com/download/). Zainstaluj x86 i x64 wersje.

4. Po zainstalowaniu zaktualizowane oprogramowanie portalu użytkowników, porównaj kopię zapasową pliku web.config, wprowadzone w kroku 1 za pomocą nowego pliku web.config. Jeśli istnieje żadnych nowych atrybutów w nowego pliku web.config, skopiuj z kopii zapasowej pliku web.config w katalogu wirtualnego, aby zastąpić nowym. Innym rozwiązaniem jest kopiowania/wklejania wartości appSettings i adres URL zestawu SDK usługi sieci Web z pliku kopii zapasowej do nowego pliku web.config.

Jeśli masz portalu użytkowników na wielu serwerach, należy powtórzyć instalacji na wszystkich z nich.

## <a name="upgrade-the-mobile-app-web-service"></a>Uaktualnij usługę sieci Web aplikacji mobilnej

> [!NOTE]
> W przypadku uaktualniania z wersji serwera Azure MFA Server starszej niż 8.0 do wersji 8.0 lub nowszej po uaktualnieniu można odinstalować usługę internetową aplikacji mobilnej

## <a name="upgrade-the-ad-fs-adapters"></a>Uaktualnianie usługi AD FS karty

Wykonaj uaktualnienie serwerów MFA i portalu użytkowników przed skierowaniem ich do tej sekcji.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Jeśli uwierzytelnianie wieloskładnikowe działa na różnych serwerach niż usługi AD FS

Instrukcje te mają zastosowanie, jeśli serwer usługi Multi-Factor Authentication oddzielnie z serwerów usług AD FS. Jeśli obie usługi są uruchomione na tych samych serwerów, Pomiń tę sekcję i przejdź do procedury instalacji. 

1. Zapisz kopię pliku MultiFactorAuthenticationAdfsAdapter.config, który został zarejestrowany w usługach AD FS lub eksportowania konfiguracji za pomocą następującego polecenia programu PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Nazwa karty jest "WindowsAzureMultiFactorAuthentication" lub "AzureMfaServerAuthentication" w zależności od wcześniej zainstalowaną wersję.
2. Skopiuj następujące pliki z lokalizacji instalacji serwera usługi MFA na serwerach usług AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Zmodyfikuj skrypt Register-MultiFactorAuthenticationAdfsAdapter.ps1, dodając `-ConfigurationFilePath [path]` na końcu `Register-AdfsAuthenticationProvider` polecenia. Zastąp *[ścieżka]* mającym pełną ścieżkę do MultiFactorAuthenticationAdfsAdapter.config pliku lub plik konfiguracyjny wyeksportowany w poprzednim kroku.

   Sprawdź atrybuty w nowych MultiFactorAuthenticationAdfsAdapter.config, aby zobaczyć, jeśli pasują do starego pliku konfiguracji. Jeśli jakiekolwiek atrybuty zostały dodane lub usunięte w nowej wersji, skopiuj wartości atrybutu ze starego pliku konfiguracji na nową lub zmodyfikować starego pliku konfiguracyjnego do dopasowania.

### <a name="install-new-ad-fs-adapters"></a>Instalowanie nowych kart usługi AD FS

> [!IMPORTANT]
> Użytkownicy nie będą musieli wykonać weryfikacji dwuetapowej kroki 3 – 8 w tej sekcji. W przypadku usług AD FS skonfigurowano w wielu klastrach można usunąć, uaktualniania i przywracanie poszczególnych klastrów w farmie, niezależnie od innych klastrów, aby uniknąć przestoju.

1. Usuń niektóre serwery usług AD FS w farmie. Te serwery aktualizacji, podczas gdy inne nadal działa.
2. Zainstaluj nowy adapter usług AD FS na każdym serwerze usunięte z farmy usług AD FS. Jeśli serwer usługi MFA jest zainstalowany na każdym serwerze usług AD FS, można zaktualizować przez administratora serwera MFA najlepsze środowisko użytkownika. W przeciwnym wypadku zaktualizuj, uruchamiając MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Jeśli błąd wystąpi komunikatem, "Microsoft Visual C++ 2015 Redistributable Update 1 lub nowszy jest wymagany," Pobierz i zainstaluj najnowszy pakiet aktualizacji z [Microsoft Download Center](https://www.microsoft.com/download/). Zainstaluj x86 i x64 wersje.

3. Przejdź do **usług AD FS** > **zasady uwierzytelniania** > **Edytuj globalne uwierzytelnianie wieloskładnikowe zasady**. Usuń zaznaczenie pola wyboru **WindowsAzureMultiFactorAuthentication** lub **AzureMFAServerAuthentication** (w zależności od bieżącej wersji zainstalowanej).

   Po wykonaniu tego kroku weryfikacji dwuetapowej za pomocą serwera usługi MFA nie jest dostępna w tym klastrze usług AD FS, do momentu ukończenia kroku 8.

4. Wyrejestruj starszą wersję adaptera AD FS, uruchamiając skrypt programu PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Upewnij się, że *— nazwa* parametrem ("WindowsAzureMultiFactorAuthentication" lub "AzureMFAServerAuthentication") jest zgodna z nazwą, która była wyświetlana w kroku 3. Dotyczy to wszystkich serwerów w tym samym klastrze usług AD FS ponieważ centralnej konfiguracji.
5. Zarejestruj nowy adapter usług AD FS, uruchamiając skrypt Register-MultiFactorAuthenticationAdfsAdapter.ps1 w programie PowerShell. Dotyczy to wszystkich serwerów w tym samym klastrze usług AD FS ponieważ centralnej konfiguracji.
6. Uruchom ponownie usługę AD FS na każdym serwerze usunięte z farmy usług AD FS.
7. Dodawanie zaktualizowanych serwerów do farmy usług AD FS i usuwanie innych serwerów z farmy.
8. Przejdź do **usług AD FS** > **zasady uwierzytelniania** > **Edytuj globalne uwierzytelnianie wieloskładnikowe zasady**. Sprawdź **AzureMfaServerAuthentication**.
9. Powtórz krok 2, aby zaktualizować serwery usunięte z farmy usług AD FS i uruchom ponownie usługę AD FS na tych serwerach.
10. Dodaj tych serwerów do farmy usług AD FS.

## <a name="next-steps"></a>Kolejne kroki

* Pobierz przykłady [zaawansowane scenariusze obejmujące usługę Azure Multi-Factor Authentication i sieci VPN innych firm](howto-mfaserver-nps-vpn.md)

* [Synchronizowanie serwera usługi MFA za pomocą usługi Active Directory systemu Windows Server](howto-mfaserver-dir-ad.md)

* [Konfigurowanie uwierzytelniania Windows](howto-mfaserver-windows.md) dla aplikacji
