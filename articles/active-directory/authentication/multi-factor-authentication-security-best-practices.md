---
title: Wskazówki dotyczące zabezpieczeń dla usługi Azure Multi-Factor Authentication — usługi Azure Active Directory
description: Ten dokument zawiera wskazówki dotyczące korzystania z usługi Azure MFA przy użyciu konta platformy Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 436b7899b1a9d4f9cab1ca2581ff9b5b162de8ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357972"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Wskazówki dotyczące zabezpieczeń dla przy użyciu usługi Azure Multi-Factor Authentication przy użyciu konta usługi Azure AD

Weryfikacji dwuetapowej jest preferowane wyborem dla większości organizacji, które chcesz zwiększyć ich procesu uwierzytelniania. Usługa Azure Multi-Factor Authentication (MFA) pomaga firmom spełnia ich wymagań bezpieczeństwa i zgodności przy jednoczesnym zapewnieniu proste środowisko logowania dla użytkowników. Ten artykuł zawiera pewne wskazówki, które należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Wdrażanie usługi Azure MFA w chmurze

Istnieją dwa sposoby [włączyć uwierzytelnianie wieloskładnikowe Azure dla wszystkich użytkowników](howto-mfa-getstarted.md).

* Kupowanie licencji dla każdego użytkownika (albo usługi Azure MFA, Azure AD Premium lub Enterprise Mobility + Security)
* Tworzenie dostawcy usługi Multi-Factor Authentication i płatność na użytkownika lub wg uwierzytelnienia

### <a name="licenses"></a>Licencje

![Stosowanie licencji do użytkowników, Włącz, Powiadom](./media/multi-factor-authentication-security-best-practices/ems.png)

Jeśli masz usługi Azure AD Premium lub pakietu Enterprise Mobility + Security licencji, masz już usługę Azure MFA. Twoja organizacja nie musi nic więcej, aby rozszerzyć możliwości weryfikacji dwuetapowej dla wszystkich użytkowników. Musisz przypisać licencję do użytkownika, a następnie włączyć uwierzytelnianie wieloskładnikowe.

Podczas konfigurowania uwierzytelniania wieloskładnikowego, należy wziąć pod uwagę następujące wskazówki:

* Nie należy tworzyć dostawcy usługi Multi-Factor Authentication na uwierzytelnienie. Jeśli to zrobisz, może wystąpić płacić za weryfikację żądań od użytkowników, które już masz licencje.
* Jeśli nie masz wystarczającą liczbę licencji dla wszystkich użytkowników, można utworzyć dostawcy usługi Multi-Factor Authentication na użytkownika, aby pokrywał pozostałej części organizacji. 
* Program Azure AD Connect jest tylko wymagane, jeśli synchronizacji w lokalnym środowisku usługi Active Directory z katalogiem usługi Azure AD. Jeśli używasz katalog usługi Azure AD, która nie jest zsynchronizowany z lokalnym wystąpieniem usługi Active Directory, nie trzeba program Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Dostawcy usługi Multi-Factor Authentication

![Dostawcy uwierzytelniania wieloskładnikowego](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Jeśli nie masz licencji, które obejmują usługi Azure MFA, a następnie możesz [utworzyć dostawcę usługi MFA](concept-mfa-authprovider.md).

Podczas tworzenia dostawcy usługi, musisz wybierz katalog, a następnie należy wziąć pod uwagę następujące informacje:

* Nie jest potrzebny katalog usługi Azure AD, do utworzenia dostawcy usługi Multi-Factor Authentication, ale uzyskać więcej funkcji przy użyciu jednego. Następujące funkcje są włączone, jeśli użytkownik jest kojarzony z katalogiem usługi Azure AD dostawcy uwierzytelniania:
  * Rozszerzanie weryfikację dwuetapową dla wszystkich użytkowników
  * Oferują administratorów globalnych dodatkowe funkcje, takie jak portal zarządzania, niestandardowe pozdrowienia i raporty.
* Jeśli w lokalnym środowisku usługi Active Directory z katalogiem usługi Azure AD, należy narzędzia DirSync i AAD Sync. Jeśli używasz katalog usługi Azure AD, która nie jest zsynchronizowany z lokalnym wystąpieniem usługi Active Directory, nie trzeba narzędzia DirSync i AAD Sync.
* Wybierz model zużycie, najlepiej pasujące do działalności. Po wybraniu modelu użycia, nie możesz go zmienić. Są dwa modele:
  * Na uwierzytelnienie: nalicza opłaty za każdy weryfikację. Jeśli chcesz, aby Weryfikacja dwuetapowa dla każdego, kto uzyskuje dostęp do niektórych aplikacji, a nie dla konkretnych użytkowników, należy użyć tego modelu.
  * Na włączonego użytkownika: pobiera opłaty dla poszczególnych użytkowników, pozwalających na korzystanie z usługi Azure MFA. Użyj tego modelu, w przypadku niektórych użytkowników przy użyciu usługi Azure AD Premium lub pakietu Enterprise Mobility Suite licencji, a inne nie.

### <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników są przyzwyczajeni do korzystania z tylko hasła do uwierzytelniania, ważne jest, czy Twoja firma udostępnia rozpoznawanie dla wszystkich użytkowników dotyczące tego procesu. Informowanie o tym, można zmniejszyć prawdopodobieństwo, że użytkownicy wywołania pomocy technicznej dla drobne problemy związane z usługi MFA. Jednak istnieją pewne scenariusze, w których tymczasowo wyłączyć uwierzytelnianie wieloskładnikowe jest konieczne. Skorzystaj z poniższych wskazówek, aby zrozumieć sposób obsługi tych scenariuszy:

* Szkolenie działu pomocy technicznej do obsługi scenariuszy, w których użytkownik nie może zalogować, ponieważ aplikacja mobilna lub telefon nie odbiera powiadomienie lub połączenie telefoniczne. Pomoc techniczna może [Włącz jednorazowe obejście](howto-mfa-mfasettings.md#one-time-bypass) umożliwia użytkownikowi uwierzytelnienie jeden raz, pomijając"" weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund.
* Należy wziąć pod uwagę [możliwości zaufane adresy IP](howto-mfa-mfasettings.md#trusted-ips) w usłudze Azure MFA jako sposób, aby zminimalizować weryfikacji dwuetapowej. Dzięki tej funkcji Administratorzy dzierżawy zarządzanych lub federacyjnego można pominąć weryfikacji dwuetapowej dla użytkowników, którzy logują z lokalnej sieci intranet. Funkcje są dostępne dla dzierżaw usługi Azure AD, które mają licencje usług Azure AD Premium, pakietu Enterprise Mobility Suite lub usługi Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Najlepsze rozwiązania dotyczące wdrażania w środowisku lokalnym

Jeśli Twoja firma zdecydowała się wykorzystać własną infrastrukturę, aby włączyć usługę MFA, a następnie należy [wdrożenia usługi Azure Multi-Factor Authentication w środowisku lokalnym](howto-mfaserver-deploy.md). Składniki serwera usługi MFA są pokazane na poniższym diagramie:

![Domyślne składniki serwera usługi MFA](./media/multi-factor-authentication-security-best-practices/server.png) \*niezainstalowanych domyślnie \** zainstalowany, lecz nie jest włączona domyślnie

Usługa Azure Multi-Factor Authentication można zabezpieczyć chmury zasobów lokalnych i w zasoby przy użyciu federacji. Konieczne jest posiadanie programu AD FS oraz jest Sfederowane za pomocą dzierżawy usługi Azure AD.
Podczas konfigurowania serwera Multi-Factor Authentication, należy wziąć pod uwagę następujące informacje:

* Jeśli są zabezpieczanie zasobów usługi Azure AD przy użyciu usługi Active Directory Federation Services (AD FS), a następnie pierwszy etap weryfikacji odbywa się lokalnie przy użyciu usług AD FS. Drugi etap odbywa się lokalnie i polega na uznaniu oświadczenia.
* Nie masz zainstalować serwer Azure Multi-Factor Authentication serwerze federacyjnym usług AD FS. Jednak Adapter uwierzytelniania Multi-Factor Authentication dla usług AD FS musi być zainstalowany w systemie Windows Server 2012 R2 uruchomionymi usługami AD FS. Możesz zainstalować serwer na innym komputerze, tak długo, jak jego wersja jest obsługiwana i oddzielnie zainstalować adapter AD FS na serwerze federacyjnym usług AD FS. 
* Uwierzytelnianie wieloskładnikowe AD FS karty Kreator instalacji tworzy grupę zabezpieczeń o nazwie PhoneFactor Admins w usłudze Active Directory, a następnie dodaje swoje konto usług AD FS do tej grupy. Sprawdź, czy grupa PhoneFactor Admins została utworzona na kontrolerze domeny oraz czy konto usług AD FS jest członkiem tej grupy. W razie potrzeby ręcznie dodaj konto usług AD FS do grupy PhoneFactor Admins na kontrolerze domeny.

### <a name="user-portal"></a>Portal użytkowników

Portal użytkowników umożliwia usługi możliwości samoobsługi i zapewnia pełny zestaw możliwości administracyjnych na użytkowników. Działa w witrynie sieci web usług Internet Information Server (IIS). Aby skonfigurować ten składnik, skorzystaj z poniższych wskazówek:

* Korzystanie z usług IIS 6 lub nowszego
* Instalowanie i rejestrowanie v2.0.507207 platformy ASP.NET
* Upewnij się, że ten serwer można wdrożyć w sieci obwodowej

### <a name="app-passwords"></a>Hasła aplikacji

Jeśli Twoja organizacja jest sfederowana z logowania jednokrotnego z usługą Azure AD i zamierzasz używać usługi Azure MFA, następnie należy pamiętać o następujących szczegółach:

* Hasło aplikacji jest weryfikowana przez usługę Azure AD i w związku z tym pomija federacji. Federacyjny jest używana tylko podczas konfigurowania hasła aplikacji.
* Dla użytkowników federacyjnych (SSO) hasła są przechowywane w identyfikatorze organizacyjnym. Jeśli użytkownik opuści firmę, że informacje o musi przepływać do Identyfikatora organizacyjnego przy użyciu narzędzia DirSync. Wyłączenie/usunięcie konta może potrwać do trzech godzin do synchronizacji, w którym opóźnienie wyłączenia/usunięcia haseł aplikacji w usłudze Azure AD.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasło aplikacji.
* Możliwość rejestrowania/inspekcji nie lokalnego uwierzytelniania jest dostępna dla hasła aplikacji.
* Niektóre zaawansowane architektury projekty mogą wymagać przy użyciu kombinacji organizacji nazwy użytkownika i hasła oraz haseł aplikacji, gdy z klientami, w zależności od tego, gdzie się uwierzytelniają przy użyciu weryfikacji dwuetapowej. Dla klientów, którzy uwierzytelniać się na infrastrukturę lokalną należy użyć organizacji użytkownika i hasło. W przypadku klientów, które przeprowadzają uwierzytelnianie w usłudze Azure AD należy użyć hasła aplikacji.
* Domyślnie użytkownicy nie mogą tworzyć hasła aplikacji. Jeśli chcesz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz opcję **Zezwalaj użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki** opcji.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Użyj tej listy, aby uzyskać dodatkowe informacje i wskazówki dla każdego składnika, który jest wdrożony w środowisku lokalnym:

* Konfigurowanie usługi Multi-Factor Authentication w usługach [Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md).
* Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [uwierzytelniania usługi RADIUS](howto-mfaserver-dir-radius.md).
* Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [uwierzytelnianie usług IIS](howto-mfaserver-iis.md).
* Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [uwierzytelniania Windows](howto-mfaserver-windows.md).
* Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [uwierzytelniania LDAP](howto-mfaserver-dir-ldap.md).
* Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [bramy usług pulpitu zdalnego i korzystanie z usługi RADIUS serwera Azure Multi-Factor Authentication](howto-mfaserver-nps-rdg.md).
* Instalowanie i Konfigurowanie synchronizacji między serwera usługi Azure MFA i [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
* [Wdrażanie usługi sieci Web aplikacji mobilnej serwera Azure Multi-Factor Authentication](howto-mfaserver-deploy-mobileapp.md).
* [Zaawansowana konfiguracja sieci VPN za pomocą usługi Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) dla urządzenia Cisco ASA, Netscaler firmy Citrix i Juniper/Pulse Secure w sieci VPN przy użyciu protokołu LDAP lub serwera RADIUS.

## <a name="next-steps"></a>Kolejne kroki

Chociaż w tym artykule opisano najlepsze rozwiązania dla usługi Azure MFA, istnieją inne zasoby, które można również użyć podczas planowania wdrożenia usługi MFA. Poniższa lista zawiera niektóre kluczowe artykułów, które mogą pomóc w trakcie tego procesu:

* [Raporty w usłudze Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Proces rejestracji weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Uwierzytelnianie wieloskładnikowe systemu Azure — często zadawane pytania](multi-factor-authentication-faq.md)
