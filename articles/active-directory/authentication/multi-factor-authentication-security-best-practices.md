---
title: Wskazówki dotyczące zabezpieczeń na potrzeby uwierzytelniania Wieloskładnikowego | Dokumentacja firmy Microsoft
description: Ten dokument zawiera wskazówki związane z kontami Azure przy użyciu usługi Azure MFA
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 50e6cd3c067e09ebf9ace442894d5d066141e0b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Wskazówki dotyczące zabezpieczeń korzystania z konta usługi Azure AD przy użyciu usługi Azure Multi-Factor Authentication

Weryfikacja dwuetapowa to preferowane wyborem w przypadku większości organizacji, które chcesz zwiększyć ich procesu uwierzytelniania. Usługa Azure Multi-Factor Authentication (MFA) pomaga firm, które spełnia ich wymagania dotyczące zabezpieczeń i zgodności, zapewniając prostych środowiska logowania dla użytkowników. W tym artykule przedstawiono kilka wskazówek, które należy wziąć pod uwagę podczas planowania przyjęcia usługi Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Wdrażanie usługi Azure MFA w chmurze

Istnieją dwa sposoby [włączyć usługi Azure MFA dla wszystkich użytkowników](howto-mfa-getstarted.md).

* Kupowanie licencji dla każdego użytkownika (albo usługi Azure MFA, Azure AD Premium lub Enterprise Mobility + Security)
* Tworzenie dostawcy uwierzytelniania wieloskładnikowego i płatności dla poszczególnych użytkowników lub uwierzytelnienia

### <a name="licenses"></a>Licencje
![PAKIET EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Jeśli masz Azure AD Premium lub pakietu Enterprise Mobility + Security licencji, masz już usługi Azure MFA. Twoja organizacja nie wymaga żadnych dodatkowych, aby rozszerzyć możliwości weryfikacji dwuetapowej dla wszystkich użytkowników. Należy przypisać licencję do użytkownika, a następnie włączyć uwierzytelnianie wieloskładnikowe.

Podczas konfigurowania uwierzytelniania wieloskładnikowego, należy wziąć pod uwagę następujące wskazówki:

* Nie należy tworzyć dostawcę uwierzytelniania wieloskładnikowego na uwierzytelniania. Jeśli to zrobisz, może się okazać płatności weryfikacji żądań od użytkowników, którzy już mają licencje.
* Jeśli nie masz wystarczającą liczbę licencji dla wszystkich użytkowników, można utworzyć użytkownika usługi Dostawca usługi MFA, aby pokrywał się z resztą organizacji. 
* Azure AD Connect jest tylko wymagane, jeśli synchronizacji w lokalnym środowisku usługi Active Directory z katalogu usługi Azure AD. Jeśli używasz katalog usługi Azure AD, która nie jest zsynchronizowany z lokalnego wystąpienia usługi Active Directory, nie trzeba Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Dostawca uwierzytelniania MFA
![Dostawca uwierzytelniania MFA](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Jeśli nie masz licencji, które obejmują usługi Azure MFA, a następnie możesz [Tworzenie dostawcy uwierzytelniania MFA](concept-mfa-authprovider.md).

Podczas tworzenia dostawcy uwierzytelniania, musisz wybrać katalog i należy wziąć pod uwagę następujące informacje:

* Nie trzeba katalog usługi Azure AD, aby utworzyć dostawcy uwierzytelniania wieloskładnikowego, ale uzyskać więcej funkcji z jednym. Następujące funkcje są włączone, gdy dostawca usługi MFA można skojarzyć z katalogu usługi Azure AD:
  * Rozszerzanie weryfikacji dwuetapowej dla wszystkich użytkowników
  * Oferują dodatkowe funkcje, takie jak portalu zarządzania, niestandardowe pozdrowienia i raporty z administratorów globalnych.
* Po zsynchronizowaniu w lokalnym środowisku usługi Active Directory z katalogiem Azure AD, należy narzędzia DirSync i AAD Sync. Jeśli używasz katalog usługi Azure AD, która nie jest zsynchronizowany z lokalnego wystąpienia usługi Active Directory, nie trzeba narzędzia DirSync i AAD Sync.
* Wybierz model zużycie, który najlepiej odpowiada firmy. Po wybraniu model zastosowania, nie można go zmienić. Są dwa modele:
  * Na uwierzytelniania: można opłaty za każdy weryfikacji. Ten model należy użyć, jeśli włączono weryfikację dwuetapową dla każdego, który uzyskuje dostęp do niektórych aplikacji, a nie dla konkretnych użytkowników.
  * Każdego włączonego użytkownika: użytkownik opłaty za każdy użytkownik, który można włączyć usługi Azure MFA. Ten model użycia w przypadku niektórych użytkowników z usługi Azure AD Premium lub pakietu Enterprise Mobility Suite licencji, a niektóre bez.

### <a name="supportability"></a>Możliwości obsługi
Ponieważ większość użytkowników są zapoznanie się przy użyciu tylko hasła do uwierzytelnienia, ważne jest, że firmy oferuje pogłębianie wiedzy na temat wszystkich użytkowników dotyczące tego procesu. Tego pogłębianie wiedzy na temat zmniejszyć prawdopodobieństwo, że użytkownicy wywołania dział pomocy technicznej dla drobne problemy związane z usługi MFA. Istnieją sytuacje, w których czasowo wyłączyć uwierzytelnianie wieloskładnikowe jest konieczne. Skorzystaj z poniższych wskazówek, aby zrozumieć sposób obsługi tych scenariuszy:

* Szkolenie działu pomocy technicznej do obsługi scenariuszy, w których użytkownik nie może zalogować, ponieważ aplikacji mobilnej lub telefon nie odbiera powiadomienia lub połączeń telefonicznych. Pomoc techniczna może [włączyć jednorazowe obejście](howto-mfa-mfasettings.md#one-time-bypass) Aby zezwolić na uwierzytelnianie tylko raz, pomijając"" weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po upływie określonej liczby sekund.
* Należy wziąć pod uwagę [możliwości zaufanych adresów IP](howto-mfa-mfasettings.md#trusted-ips) w usługi Azure MFA w sposób, aby zminimalizować weryfikacji dwuetapowej. Przy użyciu tej funkcji Administratorzy dzierżawy zarządzane lub federacyjnych można pominąć weryfikacji dwuetapowej dla użytkowników, którzy są logujący się z lokalny intranet firmy. Funkcje są dostępne dla dzierżaw usługi Azure AD, którzy mają licencje usługi Azure AD Premium, Enterprise Mobility Suite lub Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Najlepsze rozwiązania dotyczące wdrożenia lokalnego
Jeśli firma zdecydowała się korzystać z własnej infrastruktury, aby włączyć uwierzytelnianie wieloskładnikowe, konieczna [wdrażanie Azure aplikacji serwer Multi-Factor Authentication lokalnej](howto-mfaserver-deploy.md). W poniższym diagramie przedstawiono składniki serwera usługi MFA:

![Domyślne składniki serwera usługi MFA: konsoli, aparatu synchronizacji, portalu zarządzania usługą w chmurze](./media/multi-factor-authentication-security-best-practices/server.png) \*nie jest instalowany domyślnie \** zainstalowany, lecz nie jest włączona domyślnie

Azure aplikacji serwer Multi-Factor Authentication można zabezpieczyć chmury zasobów lokalnych i w zasoby przy użyciu federacji. Musisz mieć usług AD FS i jego Sfederowanych z dzierżawy usługi Azure AD.
Podczas konfigurowania serwera Multi-Factor Authentication, należy wziąć pod uwagę następujące informacje:

* Jeśli to zabezpieczania zasobów usługi Azure AD przy użyciu usługi Active Directory Federation Services (AD FS), a następnie w pierwszym krokiem weryfikacji jest wykonywane lokalnie za pomocą usług AD FS. Drugi etap odbywa się lokalnie i polega na uznaniu oświadczenia.
* Nie trzeba instalować serwera usługi Azure Multi-Factor Authentication serwera federacyjnego usług AD FS. Jednak karty uwierzytelniania wieloskładnikowego usług AD FS musi być zainstalowany w systemie Windows Server 2012 R2 uruchomionymi usługami AD FS. Można zainstalować serwer na innym komputerze, tak długo, jak jest obsługiwana wersja i oddzielnie zainstalować adapter AD FS na serwerze federacyjnym usług AD FS. 
* Uwierzytelnianie wieloskładnikowe AD FS karty Kreator instalacji tworzy grupę zabezpieczeń o nazwie PhoneFactor Admins w usłudze Active Directory, a następnie dodanie konta usługi AD FS do tej grupy. Sprawdź, czy grupa PhoneFactor Admins została utworzona na kontrolerze domeny oraz czy konto usług AD FS jest członkiem tej grupy. W razie potrzeby ręcznie dodaj konto usług AD FS do grupy PhoneFactor Admins na kontrolerze domeny.

### <a name="user-portal"></a>Portal użytkowników
Portal użytkowników umożliwia funkcji samoobsługi i zapewnia pełny zestaw możliwości administrowania użytkownika. Działa on w witrynie sieci web usług Internet Information Server (IIS). Aby skonfigurować ten składnik, skorzystaj z poniższych wskazówek:

* Korzystanie z usług IIS 6 lub nowszego
* Zainstaluj i zarejestruj ASP.NET v2.0.507207
* Upewnij się, że ten serwer można wdrożyć w sieci obwodowej

### <a name="app-passwords"></a>Hasła aplikacji
Jeśli Twoja organizacja jest Sfederowane dla rejestracji Jednokrotnej z usługą Azure AD i zamierzasz używać usługi Azure MFA, następnie należy pamiętać o następujących szczegółach:

* Hasła aplikacji jest weryfikowany przez usługę Azure AD i w związku z tym pomija federacji. Federacyjnej jest używana tylko podczas konfigurowania haseł aplikacji.
* Dla użytkowników federacyjnych (SSO) hasła są przechowywane w identyfikatora organizacyjnego. Gdy użytkownik opuści firmę, że informacje zostaną przekazane do identyfikatora organizacyjnego przy użyciu narzędzia DirSync. Wyłączenie/usunięcie konta może potrwać do trzech godzin synchronizacji opóźnienie wyłączenia/usunięcia hasła aplikacji w usłudze Azure AD.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasło aplikacji.
* Możliwość rejestrowania/inspekcji nie lokalnego uwierzytelniania jest dostępna dla hasła aplikacji.
* Niektóre zaawansowane architektury projekty mogą wymagać przy użyciu kombinacji organizacji nazwy użytkownika i hasła i haseł aplikacji, gdy z klientami, w zależności od tego, gdzie uwierzytelniania przy użyciu weryfikacji dwuetapowej. Dla klientów, którzy uwierzytelniania względem infrastruktury lokalnej użyje organizacyjnej nazwy użytkownika i hasła. Dla klientów, którzy uwierzytelniania usługi Azure AD można użyć hasła aplikacji.
* Domyślnie użytkownicy nie mogą tworzyć hasła aplikacji. Jeśli chcesz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz opcję **Zezwalaj użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji korzystających z przeglądarki** opcji.

## <a name="additional-considerations"></a>Dodatkowe uwagi
Użyj tej listy, aby uzyskać dodatkowe informacje i wskazówki dotyczące każdego składnika, który jest wdrożony na lokalnym:

- Konfigurowanie usługi Multi-Factor Authentication w usługach [Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md).
- Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [uwierzytelniania usługi RADIUS](howto-mfaserver-dir-radius.md).
- Instalowanie i konfigurowanie serwera usługi Azure MFA z [uwierzytelniania usług IIS](howto-mfaserver-iis.md).
- Instalowanie i konfigurowanie serwera usługi Azure MFA z [uwierzytelniania systemu Windows](howto-mfaserver-windows.md).
- Instalowanie i konfigurowanie serwera usługi Azure MFA z [uwierzytelniania LDAP](howto-mfaserver-dir-ldap.md).
- Instalowanie i konfigurowanie serwera usługi Azure MFA z [bramy usług pulpitu zdalnego i Azure przy użyciu usługi RADIUS serwera Multi-Factor Authentication](howto-mfaserver-nps-rdg.md).
- Instalowanie i Konfigurowanie synchronizacji między serwera usługi Azure MFA i [usługi Active Directory systemu Windows Server](howto-mfaserver-dir-ad.md).
- [Wdrażanie usługi sieci Web aplikacji mobilnej serwera Azure Multi-Factor Authentication](howto-mfaserver-deploy-mobileapp.md).
- [Zaawansowana konfiguracja sieci VPN z uwierzytelnianiem wieloskładnikowym Azure](howto-mfaserver-nps-vpn.md) dla urządzenia Cisco ASA, Citrix Netscaler i Juniper/Pulse Secure VPN przy użyciu protokołu LDAP lub serwera RADIUS.

## <a name="next-steps"></a>Kolejne kroki
Chociaż ten artykuł zawiera opis najlepsze rozwiązania dla usługi Azure MFA, istnieją inne zasoby, które można również użyć podczas planowania wdrożenia usługi MFA. Na poniższej liście zawiera niektóre klucza artykuły, które mogą pomóc w trakcie tego procesu:

* [Raporty w uwierzytelnianie wieloskładnikowe platformy Azure](howto-mfa-reporting.md)
* [Środowisko rejestracji weryfikacji dwuetapowej](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)
* [Uwierzytelnianie wieloskładnikowe platformy Azure — często zadawane pytania](multi-factor-authentication-faq.md)
