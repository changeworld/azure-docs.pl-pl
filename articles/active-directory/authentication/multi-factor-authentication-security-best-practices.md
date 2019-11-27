---
title: Wskazówki dotyczące zabezpieczeń dla usługi Azure Multi-Factor Authentication — Azure Active Directory
description: Ten dokument zawiera wskazówki dotyczące korzystania z usługi Azure MFA z kontami platformy Azure
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
ms.openlocfilehash: 2756d39a93751271c8c7bf2a51108b9fe5b09b1e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208431"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Wskazówki dotyczące zabezpieczeń dotyczące korzystania z usługi Azure Multi-Factor Authentication z kontami usługi Azure AD

Weryfikacja dwuetapowa jest preferowanym wyborem dla większości organizacji, które chcą ulepszyć proces uwierzytelniania. Usługa Azure Multi-Factor Authentication (MFA) pomaga firmom spełnić wymagania dotyczące zabezpieczeń i zgodności, zapewniając użytkownikom proste środowisko logowania. W tym artykule opisano niektóre wskazówki, które należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Wdrażanie usługi Azure MFA w chmurze

Istnieją dwa sposoby [włączania usługi Azure MFA dla wszystkich użytkowników](howto-mfa-getstarted.md).

* Kupowanie licencji dla każdego użytkownika (Azure MFA, Azure AD — wersja Premium lub Enterprise Mobility + Security)
* Tworzenie dostawcy usługi uwierzytelniania wieloskładnikowego i płatność za użytkownika lub za uwierzytelnianie

### <a name="licenses"></a>Liczba

![Stosowanie licencji do użytkowników, Włączanie, powiadamianie](./media/multi-factor-authentication-security-best-practices/ems.png)

Jeśli masz licencje na Azure AD — wersja Premium lub Enterprise Mobility + Security, masz już usługę Azure MFA. Organizacja nie potrzebuje dodatkowych informacji, aby zwiększyć możliwości weryfikacji dwuetapowej dla wszystkich użytkowników. Musisz tylko przypisać licencję do użytkownika, a następnie włączyć uwierzytelnianie wieloskładnikowe.

Podczas konfigurowania Multi-Factor Authentication należy wziąć pod uwagę następujące wskazówki:

* Nie należy tworzyć dostawcy usługi MFA dla uwierzytelniania wieloskładnikowego. Jeśli to zrobisz, możesz zaprzestać płacenia za żądania weryfikacji od użytkowników, którzy już mają licencje.
* Jeśli nie masz wystarczającej liczby licencji dla wszystkich użytkowników, możesz utworzyć dostawcę usługi uwierzytelniania wieloskładnikowego dla poszczególnych użytkowników, aby pokryć resztę organizacji. 
* Azure AD Connect jest wymagana tylko w przypadku synchronizowania lokalnego środowiska Active Directory z katalogiem usługi Azure AD. Jeśli używasz katalogu usługi Azure AD, który nie jest synchronizowany z lokalnym wystąpieniem Active Directory, nie musisz Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Dostawca usługi MFA

![Dostawca Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Jeśli nie masz licencji obejmujących usługę Azure MFA, możesz [utworzyć dostawcę uwierzytelniania MFA](concept-mfa-authprovider.md).

Podczas tworzenia dostawcy uwierzytelniania należy wybrać katalog i uwzględnić następujące informacje:

* Nie potrzebujesz katalogu usługi Azure AD, aby utworzyć dostawcę uwierzytelniania wieloskładnikowego, ale możesz uzyskać więcej funkcji. Następujące funkcje są włączane po skojarzeniu dostawcy uwierzytelniania z katalogiem usługi Azure AD:
  * Rozszerzona weryfikacja dwuetapowa dla wszystkich użytkowników
  * Oferuje administratorom globalnym dodatkowe funkcje, takie jak Portal zarządzania, niestandardowe pozdrowienia i raporty.
* Jeśli zsynchronizujesz lokalne środowisko Active Directory z katalogiem usługi Azure AD, potrzebujesz narzędzia DirSync lub Azure AD Sync. Jeśli używasz katalogu usługi Azure AD, który nie jest synchronizowany z lokalnym wystąpieniem Active Directory, nie potrzebujesz narzędzia DirSync ani Azure AD Sync.
* Wybierz model zużycia najlepiej pasujący do Twojej firmy. Po wybraniu modelu użycia nie można go zmienić. Są to dwa modele:
  * Za uwierzytelnienie: naliczanie opłat za każdą weryfikację. Ten model umożliwia weryfikację dwuetapową dla każdego, kto uzyskuje dostęp do określonej aplikacji, a nie dla określonych użytkowników.
  * Dla każdego włączonego użytkownika: opłata za każdego użytkownika, który jest włączany dla usługi Azure MFA. Użyj tego modelu, jeśli masz niektórych użytkowników z licencjami na Azure AD — wersja Premium lub Enterprise Mobility Suite, a niektóre z nich bez.

### <a name="supportability"></a>Możliwości obsługi

Ze względu na to, że większość użytkowników jest przyzwyczajonych do korzystania tylko z haseł do uwierzytelniania, ważne jest, aby firma mogła mieć świadomość wszystkim użytkownikom dotyczącym tego procesu. Dzięki tej świadomości można zmniejszyć prawdopodobieństwo, że użytkownicy będą mogli skontaktować się z działem pomocy technicznej w celu uzyskania drobnych problemów dotyczących usługi MFA. Istnieją jednak sytuacje, w których konieczne jest tymczasowe wyłączenie usługi MFA. Skorzystaj z poniższych wskazówek, aby zrozumieć, jak obsługiwać te scenariusze:

* Przeszkol personel pomocy technicznej, aby obsługiwał scenariusze, w których użytkownik nie może się zalogować, ponieważ aplikacja mobilna lub telefon nie otrzymuje powiadomienia ani rozmowy telefonicznej. Pomoc techniczna może [włączyć jednorazowe obejście](howto-mfa-mfasettings.md#one-time-bypass) , aby umożliwić użytkownikowi uwierzytelnianie jednokrotne przez "Obejście" weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund.
* W celu zminimalizowania weryfikacji dwuetapowej należy wziąć pod uwagę [możliwość używania zaufanych adresów IP](howto-mfa-mfasettings.md#trusted-ips) w usłudze Azure MFA. Dzięki tej funkcji Administratorzy zarządzanej lub federacyjnej dzierżawy mogą ominąć weryfikację dwuetapową dla użytkowników logujących się z lokalnego intranetu firmy. Funkcje są dostępne dla dzierżaw usługi Azure AD, które mają Azure AD — wersja Premium, Enterprise Mobility Suite lub Azure Multi-Factor Authentication licenses.

## <a name="best-practices-for-an-on-premises-deployment"></a>Najlepsze rozwiązania dotyczące wdrożenia lokalnego

Jeśli firma zdecyduje się wykorzystać własną infrastrukturę do włączenia uwierzytelniania wieloskładnikowego, należy [wdrożyć serwer Multi-Factor Authentication platformy Azure lokalnie](howto-mfaserver-deploy.md). Składniki serwera usługi MFA przedstawiono na poniższym diagramie:

![domyślnych składników serwera usługi MFA](./media/multi-factor-authentication-security-best-practices/server.png) \*nie zostały zainstalowane domyślnie \** zainstalowane, ale nie włączone domyślnie

Usługa Azure Serwer Multi-Factor Authentication umożliwia Zabezpieczanie zasobów w chmurze i zasobów lokalnych przy użyciu Federacji. Musisz mieć AD FS i mieć do niej federacyjny dostęp do dzierżawy usługi Azure AD.
Podczas konfigurowania Serwer Multi-Factor Authentication należy wziąć pod uwagę następujące informacje:

* W przypadku zabezpieczania zasobów usługi Azure AD za pomocą Active Directory Federation Services (AD FS) pierwszy krok weryfikacji jest wykonywany lokalnie przy użyciu AD FS. Drugi etap odbywa się lokalnie i polega na uznaniu oświadczenia.
* Nie musisz instalować platformy Azure Serwer Multi-Factor Authentication serwera federacyjnego AD FS. Jednak adapter Multi-Factor Authentication dla AD FS musi być zainstalowany w systemie Windows Server 2012 R2 z systemem AD FS. Serwer można zainstalować na innym komputerze, pod warunkiem, że jest to obsługiwana wersja, i zainstalować kartę AD FS oddzielnie na serwerze federacyjnym AD FS. 
* Kreator instalacji karty Multi-Factor Authentication AD FS tworzy grupę zabezpieczeń o nazwie PhoneFactor Administratorzy w Active Directory, a następnie dodaje do tej grupy konto usługi AD FS. Sprawdź, czy grupa PhoneFactor Admins została utworzona na kontrolerze domeny oraz czy konto usług AD FS jest członkiem tej grupy. W razie potrzeby ręcznie dodaj konto usług AD FS do grupy PhoneFactor Admins na kontrolerze domeny.

### <a name="user-portal"></a>Portal użytkowników

Portal użytkowników umożliwia korzystanie z funkcji samoobsługowych i zapewnia pełen zestaw możliwości administrowania użytkownikami. Jest ona uruchamiana w witrynie sieci Web serwera Internet Information Server (IIS). Skorzystaj z poniższych wskazówek, aby skonfigurować ten składnik:

* Korzystanie z usług IIS w wersji 6 lub nowszej
* Instalowanie i rejestrowanie ASP.NET v 2.0.507207
* Upewnij się, że ten serwer można wdrożyć w sieci obwodowej

### <a name="app-passwords"></a>Hasła aplikacji

Jeśli Twoja organizacja jest federacyjnym logowaniem jednokrotnym w usłudze Azure AD i zamierzasz korzystać z usługi Azure MFA, weź pod uwagę następujące informacje:

* Hasło aplikacji jest weryfikowane przez usługę Azure AD i w związku z tym pomija Federacji. Federacja jest używana tylko podczas konfigurowania haseł aplikacji.
* W przypadku użytkowników federacyjnych (SSO) hasła są przechowywane w IDENTYFIKATORze organizacji. Jeśli użytkownik opuści firmę, informacja ta musi być przepływa do identyfikatora organizacji przy użyciu narzędzia DirSync. Synchronizacja/usunięcie konta może potrwać do 3 godzin, co opóźnia wyłączenie/usunięcie haseł aplikacji w usłudze Azure AD.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasło aplikacji.
* Nie jest dostępna funkcja rejestrowania/inspekcji lokalnego uwierzytelniania dla haseł aplikacji.
* Niektóre zaawansowane projekty architektury mogą wymagać użycia kombinacji nazwy użytkownika i haseł w organizacji oraz haseł aplikacji podczas przeprowadzania weryfikacji dwuetapowej z klientami, w zależności od tego, gdzie są uwierzytelniane. W przypadku klientów, którzy uwierzytelniają się w infrastrukturze lokalnej, należy użyć nazwy użytkownika i hasła organizacji. W przypadku klientów, którzy uwierzytelniają się w usłudze Azure AD, Użyj hasła aplikacji.
* Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Jeśli musisz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz opcję **Zezwalaj użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki** .

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Skorzystaj z tej listy, aby uzyskać dodatkowe informacje i wskazówki dotyczące każdego składnika wdrożonego lokalnie:

* Konfigurowanie usługi Multi-Factor Authentication w usługach [Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md).
* Instalowanie i konfigurowanie serwera usługi Azure MFA przy użyciu [uwierzytelniania usługi RADIUS](howto-mfaserver-dir-radius.md).
* Skonfiguruj i skonfiguruj serwer usługi Azure MFA przy użyciu [uwierzytelniania usług IIS](howto-mfaserver-iis.md).
* Skonfiguruj i skonfiguruj serwer usługi Azure MFA przy użyciu [uwierzytelniania systemu Windows](howto-mfaserver-windows.md).
* Skonfiguruj i skonfiguruj serwer usługi Azure MFA przy użyciu [uwierzytelniania LDAP](howto-mfaserver-dir-ldap.md).
* Skonfiguruj i skonfiguruj serwer usługi Azure MFA za pomocą [bramy pulpit zdalny i usługi azure serwer Multi-Factor Authentication przy użyciu usługi RADIUS](howto-mfaserver-nps-rdg.md).
* Skonfiguruj i skonfiguruj synchronizację między serwerem usługi Azure MFA i [Active Directory systemu Windows Server](howto-mfaserver-dir-ad.md).
* [Wdrażanie usługi sieci Web aplikacji mobilnej serwera Azure Multi-Factor Authentication](howto-mfaserver-deploy-mobileapp.md).
* [Zaawansowana konfiguracja sieci VPN za pomocą usługi Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) dla urządzeń Cisco ASA, Citrix Juniper i/Pulse Secure sieci VPN przy użyciu protokołu LDAP lub RADIUS.

## <a name="next-steps"></a>Następne kroki

Chociaż w tym artykule przedstawiono niektóre najlepsze rozwiązania dotyczące usługi Azure MFA, istnieją inne zasoby, których można również użyć podczas planowania wdrożenia usługi MFA. Poniższa lista zawiera kilka najważniejszych artykułów, które mogą pomóc w tym procesie:

* [Raporty w usłudze Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Środowisko rejestracji weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Azure Multi-Factor Authentication — często zadawane pytania](multi-factor-authentication-faq.md)
