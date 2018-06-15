---
title: Usługa sieci Web aplikacji mobilnej serwera usługi Azure MFA | Microsoft Docs
description: Aplikacja Microsoft Authenticator oferuje dodatkową opcję uwierzytelniania poza pasmem.  Aplikacja umożliwia serwerowi MFA obsługę powiadomień wypychanych dla użytkowników.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 7ca5c7bcc82f0a77276f4f39a02d8abf2f47bc10
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "31614139"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Włączanie uwierzytelniania aplikacji mobilnych za pomocą serwera usługi Azure Multi-Factor Authentication

Aplikacja Microsoft Authenticator oferuje dodatkową opcję weryfikacji poza pasmem. Zamiast wykonywać automatyczne połączenia telefoniczne lub wysyłać wiadomości SMS do użytkownika podczas logowania, usługa Azure Multi-Factor Authentication wypycha powiadomienia do aplikacji Microsoft Authenticator na smartfonie lub tablecie użytkownika. Wystarczy, że użytkownik naciśnie pozycję **Weryfikuj** (lub wprowadzi numer PIN i naciśnie pozycję „Uwierzytelnij”) w aplikacji, aby się zalogować.

Korzystanie z aplikacji mobilnej w celu weryfikacji dwuetapowej jest preferowane, jeśli zasięg telefonu jest niestabilny. Jeśli używasz aplikacji jako generatora tokenów OATH, nie wymaga ona żadnego połączenia z siecią ani Internetem.

W zależności od środowiska można wdrożyć internetową usługę aplikacji mobilnych na tym samym serwerze co serwer usługi Azure Multi-Factor Authentication lub na innym serwerze internetowym.

Jeśli zainstalowano serwer usługi MFA w wersji 8.0 lub nowszej, nie trzeba wykonywać większości poniższych czynności. Uwierzytelnianie aplikacji mobilnej można skonfigurować, wykonując czynności opisane w sekcji [Konfigurowanie aplikacji mobilnej](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Wymagania

Aby korzystać z aplikacji Microsoft Authenticator, wymagane jest spełnienie następujących warunków w celu umożliwienia skutecznej komunikacji aplikacji z usługą sieci Web aplikacji mobilnej:

* Korzystanie z serwera Azure Multi-Factor Authentication w wersji 6.x lub nowszej.
* Zainstalowanie usługi sieci Web aplikacji mobilnej na dostępnym z Internetu serwerze sieci Web z uruchomionymi usługami Microsoft® [Internet Information Services (IIS) w wersji 7.x lub nowszej](http://www.iis.net/).
* Zainstalowanie i zarejestrowanie programu ASP.NET w wersji 4.0.30319 oraz wybranie dla niego opcji Dozwolone.
* Wymagane usługi ról obejmują usługę ASP.NET oraz usługę zgodności z metabazą usług IIS 6.
* Udostępnienie usługi sieci Web aplikacji mobilnej za pośrednictwem publicznego adresu URL.
* Zabezpieczenie usługi sieci Web aplikacji mobilnej za pomocą certyfikatu SSL.
* Zainstalowanie zestawu SDK usługi sieci Web usługi Azure Multi-Factor Authentication w usługach IIS w wersji 7.x lub nowszej na tym **samym serwerze, na którym zainstalowano serwer usługi Azure Multi-Factor Authentication**
* Zabezpieczenie zestawu SDK usługi sieci Web usługi Azure Multi-Factor Authentication za pomocą certyfikatu SSL.
* Zapewnienie możliwości nawiązania połączenia między usługą sieci Web aplikacji mobilnej i zestawem SDK usługi sieci Web usługi Azure Multi-Factor Authentication za pośrednictwem protokołu SSL.
* Zapewnienie możliwości wykonywania uwierzytelniania usługi sieci Web aplikacji mobilnej w zestawie SDK sieci Web usługi Azure MFA przy użyciu poświadczeń konta usługi, które należy do grupy zabezpieczeń „PhoneFactor Admins”. To konto usługi i ta grupa istnieją w usłudze Active Directory, jeśli serwer usługi Azure Multi-Factor Authentication znajduje się na serwerze przyłączonym do domeny. To konto usługi i ta grupa istnieją lokalnie na serwerze usługi Azure Multi-Factor Authentication, jeśli nie jest on przyłączony do domeny.

## <a name="install-the-mobile-app-web-service"></a>Instalacja usługi sieci Web aplikacji mobilnej

Przed zainstalowaniem usługi sieci Web aplikacji mobilnej pamiętaj o następujących szczegółach:

* Instalacja usługi internetowej aplikacji mobilnej nie jest konieczna w przypadku wersji 8.0 lub nowszej. Wykonaj tylko kroki opisane w sekcji [Konfigurowanie aplikacji mobilnej](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).
* Musisz mieć konto usługi będą częścią grupy „PhoneFactor Admins”. To konto może być takie same, jak konto używane do instalacji portalu użytkowników.
* Warto otworzyć przeglądarkę sieci Web na dostępnym z Internetu serwerze sieci Web i przejść do adresu URL zestawu SDK usługi sieci Web wprowadzonego w pliku web.config. Jeśli przeglądarka pomyślnie uzyska dostęp do usługi sieci Web, zostanie wyświetlony monit o poświadczenia. Wprowadź nazwę użytkownika i hasło wprowadzone w pliku web.config w dokładnie takiej samej postaci, w jakiej występują w pliku. Upewnij się, że nie są wyświetlane żadne ostrzeżenia ani błędy dotyczące certyfikatów.
* Jeśli zwrotny serwer proxy lub zapora znajdują się przed serwerem sieci Web usługi sieci Web aplikacji mobilnej i odpowiadają za odciążanie protokołu SSL, można edytować plik web.config usługi sieci Web aplikacji mobilnej tak, aby usługa sieci Web aplikacji mobilnej mogła korzystać z protokołu http zamiast https. Protokół SSL jest nadal wymagany w celu nawiązania komunikacji między aplikacją mobilną i zaporą/zwrotnym serwerem proxy. Dodaj następujący klucz do sekcji \<appSettings\>:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Instalowanie zestawu SDK usługi sieci Web

W dowolnym scenariuszu, jeśli zestaw SDK usługi internetowej Azure Multi-Factor Authentication **nie** został jeszcze zainstalowany na serwerze usługi Azure Multi-Factor Authentication (MFA), wykonaj poniższe kroki.

1. Otwórz konsolę serwera usługi Multi-Factor Authentication.
2. Przejdź do **zestawu SDK usługi internetowej** i wybierz pozycję **Zainstaluj zestaw SDK usługi internetowej**.
3. Ukończ instalację przy użyciu ustawień domyślnych, o ile nie trzeba ich zmienić z dowolnej przyczyny.
4. Powiąż certyfikat SSL z witryną w usługach IIS.

Jeśli masz pytania dotyczące konfigurowania certyfikatu SSL na serwerze usług IIS, zobacz artykuł [How to Set Up SSL on IIS (Jak skonfigurować protokół SSL w usługach IIS)](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

Zestaw SDK usługi sieci Web musi zostać zabezpieczony za pomocą certyfikatu SSL. W tym celu wystarczy certyfikat z podpisem własnym. Zaimportuj certyfikat do magazynu „Zaufane główne urzędy certyfikacji” konta komputera lokalnego na serwerze internetowym portalu użytkowników, dzięki czemu magazyn uzna ten certyfikat za zaufany podczas inicjowania połączenia SSL.

![Ustawienia konfiguracji serwera usługi MFA — zestaw SDK usługi internetowej](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>Instalowanie usługi

1. **Na serwerze usługi MFA** przejdź do ścieżki instalacji.
2. Przejdź do folderu, w którym zainstalowano serwer usługi Azure MFA. Wartość domyślna to **C:\Program Files\Azure Multi-Factor Authentication**.
3. Zlokalizuj plik instalacyjny **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Jeśli serwer **nie** jest dostępny z Internetu, skopiuj plik instalacyjny na serwer dostępny z Internetu.
4. Jeśli serwer usługi MFA **nie** jest dostępny z Internetu, przełącz się na **serwer dostępny z Internetu**.
5. Uruchom plik instalacyjny **MultiFactorAuthenticationMobileAppWebServiceSetup64** jako administrator, w miarę potrzeb zmieniając miejsce instalacji, a następnie, jeśli chcesz, zmień nazwę katalogu wirtualnego na krótką.
6. Po zakończeniu instalacji przejdź do lokalizacji **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (lub odpowiedniego katalogu określonego na podstawie nazwy katalogu wirtualnego) i dokonaj edycji pliku Web.Config.

   * Znajdź klucz **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** i zmień wartość **value=""** na **value="DOMAIN\User"**, gdzie DOMAIN\User to konto usługi będące częścią grupy „PhoneFactor Admins”.
   * Znajdź klucz **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** i zmień wartość **value=""** na **value="Password"**, gdzie Password to hasło konta usługi wprowadzonego w poprzednim wierszu.
   * Znajdź ustawienie **pfMobile App Web Service_pfwssdk_PfWsSdk** i zmień jego wartość z **http://localhost:4898/PfWsSdk.asmx** na adres URL zestawu SDK usługi internetowej (na przykład https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).
   * Zapisz plik Web.Config i zamknij Notatnik.

   > [!NOTE]
   > Ponieważ na potrzeby tego połączenia jest używany protokół SSL, do zestawu SDK usługi internetowej musisz odwoływać się za pomocą **w pełni kwalifikowanej nazwy (FQDN)**, a **nie adresu IP**. Certyfikat SSL będzie wystawiony dla nazwy FQDN, a używany adres URL musi być zgodny z nazwą w certyfikacie.

7. Jeśli witryna internetowa, w której została zainstalowana usługa internetowej aplikacji mobilnej, nie została jeszcze powiązana z certyfikatem z podpisem publicznym, zainstaluj certyfikat na serwerze, otwórz Menedżera usług IIS i powiąż certyfikat z witryną internetową.
8. Otwórz przeglądarkę internetową na dowolnym komputerze i przejdź do adresu URL, pod którym zainstalowano usługę internetową aplikacji mobilnej (np. https://mfa.contoso.com/MultiFactorAuthMobileAppWebService). Upewnij się, że nie są wyświetlane żadne ostrzeżenia ani błędy dotyczące certyfikatów.
9. Aby uzyskać więcej informacji o metodach dostępnych w zestawie SDK usług internetowych, zobacz plik pomocy serwera MFA.
10. Po zainstalowaniu usługi sieci Web aplikacji mobilnej należy skonfigurować serwer usługi Azure Multi-Factor Authentication do pracy z portalem.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Skonfigurowanie ustawień aplikacji mobilnej na serwerze usługi Azure Multi-Factor Authentication

1. W konsoli usługi Azure Multi-Factor Authentication kliknij ikonę Portal użytkowników. Jeśli użytkownicy mogą kontrolować metody ich uwierzytelniania, zaznacz opcję **Aplikacja mobilna** na karcie Ustawienia w obszarze **Zezwalaj użytkownikom na wybór metody**. W przeciwnym razie użytkownicy końcowi będą kontaktować się z działem pomocy technicznej w celu ukończenia procesu aktywacji aplikacji mobilnej.
2. Zaznacz pole **Zezwalaj użytkownikom na uaktywnienie aplikacji mobilnej**.
3. Zaznacz pole **Zezwalaj na rejestrację użytkownika**.
4. Kliknij ikonę **aplikacji mobilnej**.
5. Jeśli korzystasz z wersji 8.0 lub nowszej, pomiń następujący krok: wprowadź adres URL używany z katalogiem wirtualnym, który został utworzony podczas instalowania pliku MultiFactorAuthenticationMobileAppWebServiceSetup64 (np.: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) w polu **Adres URL usługi internetowej aplikacji mobilnej:**.
6. W polu **nazwy konta** wprowadź nazwę firmy lub organizacji, aby wyświetlić ją w aplikacji mobilnej dla tego konta.
   ![Ustawienia aplikacji mobilnej konfiguracji serwera usługi MFA](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>Następne kroki

- [Zaawansowane scenariusze obejmujące usługę Azure Multi-Factor Authentication i sieci VPN innych firm](multi-factor-authentication-advanced-vpn-configurations.md).
