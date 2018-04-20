---
title: "Uaktualnianie programu Azure serwera usługi MFA | Dokumentacja firmy Microsoft"
description: "Kroki i wskazówki dotyczące uaktualnienia do nowszej wersji serwera usługi Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 456f2c64c91249f3ec2de4f2d79a2f77cc7598da
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Uaktualnij do najnowszej Azure aplikacji serwer Multi-Factor Authentication

Ten artykuł przeprowadzi Cię przez proces uaktualniania serwera usługi Azure Multi-Factor Authentication (MFA) w wersji 6.0 lub nowszej. Jeśli musisz uaktualnić stara wersja PhoneFactor Agent, zapoznaj się [uaktualnienia PhoneFactor Agent do serwera usługi Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-upgrade.md).

Jeśli uaktualniasz z 6.x lub w starszej do v7.x lub nowszej, wszystkie składniki zmienić z .NET 2.0 na .NET 4.5. Wszystkie składniki wymagają programu Microsoft Visual C++ 2015 Redistributable Update 1 lub nowszej. Instalator serwera usługi MFA instaluje x86 i x64 wersje tych składników, jeśli nie są już zainstalowane. Jeśli Portal użytkowników i Mobile App Web Service uruchomione na różnych serwerach, należy zainstalować te pakiety przed rozpoczęciem uaktualniania tych składników. Możesz wyszukać najnowszą aktualizację programu Microsoft Visual C++ 2015 Redistributable na [Microsoft Download Center](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Zainstaluj najnowszą wersję serwera usługi Azure MFA

1. Postępuj zgodnie z instrukcjami w [pobierania serwera usługi Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md#download-the-mfa-server) można pobrać najnowszą wersję serwera usługi Azure MFA.
2. Wykonaj kopię zapasową pliku danych serwera usługi MFA w C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (przy założeniu, domyślna lokalizacja instalacji) na serwerze głównym usługi MFA.
3. Po uruchomieniu wielu serwerów w celu zapewnienia wysokiej dostępności, zmień klienckie systemy, które uwierzytelnienie na serwerze usługi MFA, aby zatrzymać one wysyłać ruch do serwerów, które są uaktualnianie. Użycie modułu równoważenia obciążenia, usuwany jest serwer usługi MFA z usługi równoważenia obciążenia wykonaj uaktualnienia, a następnie dodaj serwer do farmy.
4. Uruchom nowe Instalatora na każdym serwerze usługi MFA. Serwery podrzędne należy najpierw uaktualnić, ponieważ będzie mogła ją odczytać starego pliku danych replikowana przez główny. 

  Nie trzeba przed uruchomieniem Instalatora należy odinstalować bieżącego serwera usługi MFA. Instalator wykonuje uaktualnienie w miejscu. Ścieżka instalacji jest pobierana z rejestru z poprzedniej instalacji, więc instaluje go w tym samym miejscu (na przykład C:\Program Files\Multi-Factor Authentication Server). 
  
5. Jeśli zostanie wyświetlony monit, aby zainstalować pakiet aktualizacji programu Microsoft Visual C++ 2015 Redistributable, należy zaakceptować monit. X86 i x64 wersje pakietu są zainstalowane.
5. Jeśli korzystasz z zestawem SDK usług sieci Web, zostanie wyświetlony monit zainstalować nowy zestaw SDK usług sieci Web. Po zainstalowaniu nowego zestawu SDK usług sieci Web upewnij się, czy nazwa katalogu wirtualnego odpowiada zainstalowane wcześniej katalogu wirtualnego (na przykład MultiFactorAuthWebServiceSdk).
6. Powtórz kroki na wszystkich serwerach podrzędnych. Podwyższ poziom jednego podwładnych jako nowy wzorzec, a następnie uaktualnić starego serwera głównego. 

## <a name="upgrade-the-user-portal"></a>Uaktualnianie portalu użytkowników

1. Wykonaj kopię zapasową pliku web.config, który znajduje się w katalogu wirtualnego w wybranej lokalizacji instalacji portalu użytkowników (na przykład C:\inetpub\wwwroot\MultiFactorAuth). Jeśli zmiany zostały wprowadzone w motyw domyślny, należy wykonać kopię zapasową do folderu App_Themes\Default. Warto utworzyć kopię domyślny folder i utworzyć nową kompozycję niż Aby zmienić motyw domyślny.
2. Jeśli Portal użytkownika jest uruchomiony na tym samym serwerze co inne składniki serwera usługi MFA, pojawi się monit instalacji serwera usługi MFA do zaktualizowania aplikacji Portal użytkowników. Zaakceptować monit i zainstaluj aktualizację portalu użytkowników. Sprawdź, czy nazwa katalogu wirtualnego zgodna zainstalowane wcześniej katalogu wirtualnego (na przykład MultiFactorAuth).
3. Jeśli Portal użytkownika jest na osobnym serwerze, skopiuj plik MultiFactorAuthenticationUserPortalSetup64.msi z lokalizacji instalacji jednego z serwerów usługi MFA i umieścić go na serwerze sieci web portalu użytkowników. Uruchom Instalatora. 

  Jeśli wystąpi błąd określania, "Microsoft Visual C++ 2015 Redistributable Update 1 lub nowszy jest wymagany," Pobierz i zainstaluj najnowszy pakiet aktualizacji z [Microsoft Download Center](https://www.microsoft.com/download/). Zainstalowanie wersji x86 i x64.

4. Po zainstalowaniu zaktualizowanej oprogramowania portalu użytkowników, porównaj kopii zapasowej pliku web.config, wprowadzone w kroku 1 nowy plik web.config. Jeśli nie istnieją żadne nowe atrybuty w nowego pliku web.config, skopiuj kopii zapasowej pliku web.config w katalogu wirtualnego, aby zastąpić nową. Innym rozwiązaniem jest kopiowania/wklejania wartości appSettings i adres URL zestawu SDK usługi sieci Web z pliku kopii zapasowej do nowego pliku web.config.

Jeśli Portal użytkowników na wielu serwerach, powtórz instalacji na wszystkich z nich. 


## <a name="upgrade-the-mobile-app-web-service"></a>Uaktualnianie usługi sieci Web aplikacji mobilnej

1. Wykonaj kopię zapasową pliku web.config, który znajduje się w katalogu wirtualnego w lokalizacji instalacji usługi Mobile App Web Service (na przykład C:\inetpub\wwwroot\app lub C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. Skopiuj plik MultiFactorAuthenticationMobileAppWebServiceSetup64.msi z lokalizacji instalacji serwerów MFA i umieść je na serwerze sieci web rejestracji aplikacji mobilnej.
3. Uruchom Instalatora. 

  Jeśli wystąpi błąd informujący, że program Microsoft Visual C++ 2015 Redistributable Update 1 lub nowszy jest wymagany, Pobierz i zainstaluj najnowszy pakiet aktualizacji z [Microsoft Download Center](https://www.microsoft.com/download/). Zainstalowanie wersji x86 i x64.

4. Po zainstalowaniu zaktualizowanej oprogramowania Mobile App Web Service, porównaj plik web.config, która została skopiowana w kroku 1 nowy plik web.config. Jeśli nie istnieją żadne nowe atrybuty w nowego pliku web.config, możesz skopiować zapisanego pliku web.config wrócić do katalogu wirtualnego i zastąpić nowym. Innym rozwiązaniem jest kopiowania/wklejania wartości appSettings i adres URL zestawu SDK usługi sieci Web z pliku kopii zapasowej do nowego pliku web.config.

Jeśli masz usługi Mobile App Web Service na wielu serwerach, powtórz instalacji na wszystkich z nich. 

## <a name="upgrade-the-ad-fs-adapters"></a>Uaktualnienie karty AD FS


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Jeśli uwierzytelnianie wieloskładnikowe jest uruchomiony na różnych serwerach niż usługi AD FS

Te instrukcje stosowane tylko wtedy, jeśli serwer Multi-Factor Authentication należy uruchomić niezależnie od serwerów usług AD FS. Jeśli obie usługi są uruchomione na tym samym serwerze, Pomiń tę sekcję i przejdź do procedury instalacji. 

1. Zapisz kopię pliku MultiFactorAuthenticationAdfsAdapter.config, który został zarejestrowany w usługach AD FS lub wyeksportować konfigurację za pomocą następującego polecenia programu PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Nazwa karty jest "element WindowsAzureMultiFactorAuthentication" lub "AzureMfaServerAuthentication" w zależności od wcześniej zainstalowanej wersji.
2. Skopiuj następujące pliki z lokalizacji instalacji serwera usługi MFA do serwerów usług AD FS:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Edytowanie skryptu Register-MultiFactorAuthenticationAdfsAdapter.ps1 przez dodanie `-ConfigurationFilePath [path]` na końcu `Register-AdfsAuthenticationProvider` polecenia. Zastąp *[ścieżka]* z pełną ścieżką do MultiFactorAuthenticationAdfsAdapter.config pliku lub pliku konfiguracji wyeksportowane w poprzednim kroku. 

  Sprawdź atrybuty w MultiFactorAuthenticationAdfsAdapter.config nowy, aby zobaczyć, jeśli spełniają starego pliku konfiguracji. Jeśli wszystkie atrybuty zostały dodane lub usunięte w nowej wersji, skopiuj wartości atrybutów ze starego pliku konfiguracji na nową lub zmodyfikować starego pliku konfiguracji w celu dopasowania.

### <a name="install-new-ad-fs-adapters"></a>Zainstaluj nowe karty usług AD FS

> [!IMPORTANT] 
> Użytkownicy nie będą wymagane do przeprowadzenia weryfikacji dwuetapowej etapach 3-8 w tej sekcji. Jeśli masz skonfigurowane w wielu klastrach usług AD FS, Usuń, uaktualniania i przywracanie poszczególnych klastrów w farmie serwerów, niezależnie od innych klastrów, aby uniknąć przestoju.

1. Usuń niektóre serwery usług AD FS w farmie. Zaktualizuj te serwery, gdy nadal są uruchomione inne.
2. Zainstaluj nowy adapter AD FS na każdym serwerze usunięte z farmy usług AD FS. Jeśli serwer usługi MFA jest zainstalowany na każdym serwerze usług AD FS, możesz je zaktualizować przez administratora serwera usługi MFA UX. W przeciwnym razie należy zaktualizować, uruchamiając MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Jeśli wystąpi błąd określania, "Microsoft Visual C++ 2015 Redistributable Update 1 lub nowszy jest wymagany," Pobierz i zainstaluj najnowszy pakiet aktualizacji z [Microsoft Download Center](https://www.microsoft.com/download/). Zainstalowanie wersji x86 i x64.

3. Przejdź do **usług AD FS** > **zasady uwierzytelniania** > **Edytuj globalne uwierzytelnianie wieloskładnikowe zasady**. Usuń zaznaczenie pola wyboru **element WindowsAzureMultiFactorAuthentication** lub **AzureMFAServerAuthentication** (w zależności od bieżącej wersji zainstalowanej). 

  Po wykonaniu tego kroku weryfikację dwuetapową, za pomocą serwera usługi MFA jest niedostępna w tym klastrze usług AD FS dopiero po wykonaniu kroku 8.

4. Wyrejestruj starszej wersji produktu adapter AD FS, uruchamiając skrypt programu PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Upewnij się, że *— nazwa* parametr ("Element WindowsAzureMultiFactorAuthentication" lub "AzureMFAServerAuthentication") jest zgodna z nazwą, który był wyświetlany w kroku 3. Dotyczy to wszystkich serwerów w tym samym klastrze usług AD FS, ponieważ istnieje konfiguracja centralnych.
5. Zarejestruj nowy adapter AD FS, uruchamiając skrypt programu PowerShell Register-MultiFactorAuthenticationAdfsAdapter.ps1. Dotyczy to wszystkich serwerów w tym samym klastrze usług AD FS, ponieważ istnieje konfiguracja centralnych.
6. Uruchom ponownie usługę AD FS na każdym serwerze usunięte z farmy usług AD FS.
7. Dodaj zaktualizowanych serwerów do farmy usług AD FS i usuń pozostałe serwery z farmy.
8. Przejdź do **usług AD FS** > **zasady uwierzytelniania** > **Edytuj globalne uwierzytelnianie wieloskładnikowe zasady**. Sprawdź **AzureMfaServerAuthentication**.
9. Powtórz krok 2, aby zaktualizować serwery teraz usunięte z farmy usług AD FS i uruchom ponownie usługę AD FS na tych serwerach.
10. Dodaj tych serwerów do farmy usług AD FS.

## <a name="next-steps"></a>Następne kroki

- Pobierz przykłady [zaawansowanych scenariuszy z usługi Azure Multi-Factor Authentication i sieci VPN innych firm](multi-factor-authentication-advanced-vpn-configurations.md)

- [Synchronizowanie serwera usługi MFA z usługi Active Directory systemu Windows Server](multi-factor-authentication-get-started-server-dirint.md)

- [Skonfiguruj uwierzytelnianie systemu Windows](multi-factor-authentication-get-started-server-windows.md) dla aplikacji
