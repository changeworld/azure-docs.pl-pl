---
title: Wskazówki dotyczące zabezpieczeń uwierzytelniania wieloskładnikowego platformy Azure — usługa Azure Active Directory
description: Ten dokument zawiera wskazówki dotyczące korzystania z usługi Azure MFA z kontami platformy Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e42234e9fcdcfe3ee5ce975babbe03b64a750e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846831"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Wskazówki dotyczące zabezpieczeń dotyczące korzystania z uwierzytelniania wieloskładnikowego platformy Azure za pomocą kont usługi Azure AD

Weryfikacja dwuetapowa jest preferowanym wyborem dla większości organizacji, które chcą usprawnić proces uwierzytelniania. Uwierzytelnianie wieloskładnikowe usługi Azure (MFA) pomaga firmom spełniać wymagania dotyczące zabezpieczeń i zgodności, zapewniając jednocześnie proste środowisko logowania dla swoich użytkowników. W tym artykule opisano kilka wskazówek, które należy wziąć pod uwagę podczas planowania przyjęcia usługi Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Wdrażanie usługi Azure MFA w chmurze

Istnieją dwa sposoby [włączania usługi Azure MFA dla wszystkich użytkowników.](howto-mfa-getstarted.md)

* Kupowanie licencji dla każdego użytkownika (usługi Azure MFA, usługi Azure AD Premium lub Enterprise Mobility + Security)
* Tworzenie wieloskładnikowego dostawcy uwierzytelniania i płacenie za użytkownika lub za uwierzytelnienie

### <a name="licenses"></a>Licencje

![Stosowanie licencji do użytkowników, włączanie, powiadamianie](./media/multi-factor-authentication-security-best-practices/ems.png)

Jeśli masz licencje usługi Azure AD Premium lub Enterprise Mobility + Security, masz już usługi Azure MFA. Twoja organizacja nie potrzebuje żadnych dodatkowych informacji, aby rozszerzyć funkcję weryfikacji dwuetapowej na wszystkich użytkowników. Wystarczy przypisać licencję do użytkownika, a następnie można włączyć uwierzytelnianie wieloskładnikowe.

Podczas konfigurowania uwierzytelniania wieloskładnikowego należy wziąć pod uwagę następujące wskazówki:

* Nie należy tworzyć dostawcy wieloskładnikowego uwierzytelniania dla uwierzytelniania. Jeśli to zrobisz, możesz w końcu zapłacić za żądania weryfikacji od użytkowników, którzy mają już licencje.
* Jeśli nie masz wystarczającej liczby licencji dla wszystkich użytkowników, możesz utworzyć dostawcę auth dla wielu czynników dla użytkownika, aby objąć nią pozostałą część organizacji. 
* Usługa Azure AD Connect jest wymagana tylko wtedy, gdy synchronizujesz lokalne środowisko usługi Active Directory z katalogiem usługi Azure AD. Jeśli używasz katalogu usługi Azure AD, który nie jest synchronizowany z lokalnym wystąpieniem usługi Active Directory, nie potrzebujesz usługi Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Dostawca auth wieloczynnikowych

![Dostawca uwierzytelniania wieloskładnikowego](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Jeśli nie masz licencji, które zawierają usługi Azure MFA, możesz [utworzyć dostawcę usługi MFA Auth](concept-mfa-authprovider.md).

Podczas tworzenia dostawcy śucha wybrano katalog i należy wziąć pod uwagę następujące szczegóły:

* Nie potrzebujesz katalogu usługi Azure AD, aby utworzyć dostawcę auth wieloskładnikowego, ale masz więcej funkcji z jednym. Następujące funkcje są włączone podczas kojarzenia dostawcy urywek z katalogiem usługi Azure AD:
  * Rozszerzenie weryfikacji dwuetapowej na wszystkich użytkowników
  * Zaoferuj administratorom globalnym dodatkowe funkcje, takie jak portal zarządzania, niestandardowe pozdrowienia i raporty.
* Jeśli zsynchronizujesz lokalne środowisko usługi Active Directory z katalogiem usługi Azure AD, potrzebujesz programu DirSync lub usługi Azure AD Sync. Jeśli używasz katalogu usługi Azure AD, który nie jest synchronizowany z lokalnym wystąpieniem usługi Active Directory, nie potrzebujesz programu DirSync ani usługi Azure AD Sync.
* Wybierz model konsumpcji, który najlepiej odpowiada Twojej firmie. Po wybraniu modelu użycia nie można go zmienić. Dwa modele to:
  * Za uwierzytelnienie: pobierasz opłaty za każdą weryfikację. Użyj tego modelu, jeśli chcesz weryfikacji dwuetapowej dla każdego, kto uzyskuje dostęp do określonej aplikacji, a nie dla określonych użytkowników.
  * Na włączonego użytkownika: pobiera opłaty za każdego użytkownika, który można włączyć dla usługi Azure MFA. Użyj tego modelu, jeśli masz niektórych użytkowników z licencjami usługi Azure AD Premium lub Enterprise Mobility Suite, a niektóre bez.

### <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników jest przyzwyczajona do używania tylko haseł do uwierzytelniania, ważne jest, aby twoja firma uświadamia wszystkim użytkownikom informacje o tym procesie. Ta świadomość może zmniejszyć prawdopodobieństwo, że użytkownicy zadzwonią do działu pomocy technicznej w przypadku drobnych problemów związanych z uwierzytelnianiem wieloskładnikowym. Istnieją jednak pewne scenariusze, w których tymczasowe wyłączenie usługi MFA jest konieczne. Aby zrozumieć, jak obsługiwać te scenariusze, należy użyć następujących wskazówek:

* Przeszkolij personel pomocy technicznej, aby obsługiwał scenariusze, w których użytkownik nie może się zalogować, ponieważ aplikacja mobilna lub telefon nie odbiera powiadomień ani połączeń telefonicznych. Pomoc techniczna może [włączyć jednorazowe obejście,](howto-mfa-mfasettings.md#one-time-bypass) aby umożliwić użytkownikowi uwierzytelnianie jeden raz przez "pomijanie" weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund.
* Należy wziąć pod uwagę [możliwości zaufanych usług IP](howto-mfa-mfasettings.md#trusted-ips) w usłudze Azure MFA jako sposób na zminimalizowanie weryfikacji dwuetapowej. Dzięki tej funkcji administratorzy dzierżawy zarządzanej lub sfederowanej mogą pominąć weryfikację dwuetapową dla użytkowników logujących się z lokalnego intranetu firmy. Funkcje są dostępne dla dzierżaw usługi Azure AD, którzy mają licencje Azure AD Premium, Enterprise Mobility Suite lub Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Najważniejsze wskazówki dotyczące wdrażania lokalnego

Jeśli firma zdecydowała się wykorzystać własną infrastrukturę do włączenia usługi MFA, należy [wdrożyć lokalny serwer uwierzytelniania wieloskładnikowego platformy Azure.](howto-mfaserver-deploy.md) Składniki serwera usługi MFA są wyświetlane na poniższym diagramie:

![Domyślne składniki](./media/multi-factor-authentication-security-best-practices/server.png) \*serwera usługi MFA Nie są zainstalowane domyślnie \**Zainstalowane, ale domyślnie nie włączone

Serwer uwierzytelniania wieloskładnikowego platformy Azure może zabezpieczać zasoby w chmurze i zasoby lokalne przy użyciu federacji. Musisz mieć usługi AD FS i mieć go federacyjne z dzierżawy usługi Azure AD.
Podczas konfigurowania serwera uwierzytelniania wieloskładnikowego należy wziąć pod uwagę następujące szczegóły:

* Jeśli zabezpieczasz zasoby usługi Azure AD przy użyciu usług federacyjnych Active Directory (AD FS), pierwszy krok weryfikacji jest wykonywany lokalnie przy użyciu usług AD FS. Drugi etap odbywa się lokalnie i polega na uznaniu oświadczenia.
* Nie trzeba instalować serwera uwierzytelniania wieloskładnikowego platformy Azure na serwerze federacyjnym usług AD FS. Jednak karta uwierzytelniania wieloskładnikowego dla usług AD FS musi być zainstalowana w systemie Windows Server 2012 R2 z systemem AD FS. Serwer można zainstalować na innym komputerze, o ile jest obsługiwana wersja, i zainstalować kartę usług AD FS oddzielnie na serwerze federacyjnym usług AD FS. 
* Kreator instalacji karty usług AD FS uwierzytelniania wieloskładnikowego tworzy grupę zabezpieczeń o nazwie Administratorzy usług PhoneFactor w usłudze Active Directory, a następnie dodaje konto usługi AD FS do tej grupy. Sprawdź, czy grupa PhoneFactor Admins została utworzona na kontrolerze domeny oraz czy konto usług AD FS jest członkiem tej grupy. W razie potrzeby ręcznie dodaj konto usług AD FS do grupy PhoneFactor Admins na kontrolerze domeny.

### <a name="user-portal"></a>Portal użytkowników

Portal użytkownika umożliwia możliwości samoobsługi i zapewnia pełny zestaw możliwości administrowania użytkownikami. Działa w witrynie sieci Web internet information server (IIS). Aby skonfigurować ten składnik, należy użyć następujących wskazówek:

* Korzystanie z iis 6 lub większych
* Zainstaluj i zarejestruj ASP.NET wersji 2.0.507207
* Upewnij się, że ten serwer można wdrożyć w sieci obwodowej

### <a name="app-passwords"></a>Hasła aplikacji

Jeśli twoja organizacja jest sfederowana dla logowania typu SSO z usługą Azure AD i zamierzasz używać usługi Azure MFA, należy pamiętać o następujących szczegółach:

* Hasło aplikacji jest weryfikowane przez usługę Azure AD i w związku z tym omija federację. Federacja jest używana tylko podczas konfigurowania haseł aplikacji.
* W przypadku użytkowników federacyjnych (SSO) hasła są przechowywane w identyfikatorze organizacyjnym. Jeśli użytkownik opuści firmę, te informacje muszą przepływać do identyfikatora organizacji przy użyciu programu DirSync. Synchronizacja/usuwanie konta może potrwać do trzech godzin, co opóźnia wyłączenie/usunięcie haseł aplikacji w usłudze Azure AD.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasło aplikacji.
* Dla haseł aplikacji nie jest dostępna funkcja rejestrowania/inspekcji uwierzytelniania lokalnego.
* Niektóre zaawansowane projekty architektoniczne mogą wymagać użycia kombinacji organizacyjnej nazwy użytkownika i haseł oraz haseł aplikacji podczas korzystania z weryfikacji dwuetapowej z klientami, w zależności od tego, gdzie uwierzytelniają się. W przypadku klientów uwierzytelnianych w infrastrukturze lokalnej należy użyć nazwy użytkownika i hasła organizacyjnego. Dla klientów, którzy uwierzytelniają się za pomocą usługi Azure AD, należy użyć hasła aplikacji.
* Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Jeśli chcesz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz opcję **Zezwalaj użytkownikom na tworzenie haseł aplikacji, aby logować się do aplikacji innych niż przeglądarki.**

## <a name="additional-considerations"></a>Dodatkowe uwagi

Użyj tej listy dla dodatkowych zagadnień i wskazówek dla każdego składnika, który jest wdrażany lokalnie:

* Konfigurowanie usługi Multi-Factor Authentication w usługach [Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md).
* Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [uwierzytelniania usługi RADIUS](howto-mfaserver-dir-radius.md).
* Konfigurowanie i konfigurowanie serwera usługi Azure MFA za pomocą [uwierzytelniania usług IIS](howto-mfaserver-iis.md).
* Konfigurowanie i konfigurowanie serwera usługi Azure MFA server za pomocą [uwierzytelniania systemu Windows](howto-mfaserver-windows.md).
* Konfigurowanie i konfigurowanie serwera usługi Azure MFA za pomocą [uwierzytelniania LDAP](howto-mfaserver-dir-ldap.md).
* Skonfiguruj i skonfiguruj serwer usługi Azure MFA za pomocą [bramy usług pulpitu zdalnego i serwera uwierzytelniania wieloskładnikowego platformy Azure przy użyciu usługi RADIUS](howto-mfaserver-nps-rdg.md).
* Konfigurowanie i konfigurowanie synchronizacji między serwerem usługi Azure MFA Server a [usługą Active Directory systemu Windows Server](howto-mfaserver-dir-ad.md).
* [Wdrażanie usługi sieci Web aplikacji mobilnej serwera Azure Multi-Factor Authentication](howto-mfaserver-deploy-mobileapp.md).
* [Zaawansowana konfiguracja sieci VPN z uwierzytelnianiem wieloskładnikowym platformy Azure](howto-mfaserver-nps-vpn.md) dla urządzeń Cisco ASA, Citrix Netscaler i Juniper/Pulse Secure VPN przy użyciu LDAP lub RADIUS.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono niektóre najważniejsze wskazówki dotyczące usługi Azure MFA, istnieją inne zasoby, których można również użyć podczas planowania wdrożenia usługi MFA. Na poniższej liście znajduje się kilka kluczowych artykułów, które mogą pomóc w tym procesie:

* [Raporty w usłudze Azure uwierzytelniania wieloskładnikowego](howto-mfa-reporting.md)
* [Dwuetapowa rejestracja weryfikacyjna](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Uwierzytelnianie wieloskładnikowe platformy Azure — często zadawane pytania](multi-factor-authentication-faq.md)
