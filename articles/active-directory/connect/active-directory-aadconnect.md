---
title: Łączenie usługi Active Directory z usługą Azure Active Directory. | Microsoft Docs
description: Program Azure AD Connect umożliwia integrowanie katalogów lokalnych z usługą Azure Active Directory. Dzięki temu można posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD.
keywords: wprowadzenie do programu Azure AD Connect, omówienie programu Azure AD Connect, co to jest program Azure AD Connect, instalowanie usługi Active Directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: bd2b65c31e7736346957dde4edfb7c32894c833e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="integrate-your-on-premises-directories-with-azure-active-directory"></a>Integrowanie katalogów lokalnych z usługą Azure Active Directory
Program Azure AD Connect umożliwia integrowanie katalogów lokalnych z usługą Azure Active Directory. Dzięki temu użytkownicy mogą posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD. W tym temacie przedstawiono kroki związane z planowaniem, wdrażaniem i obsługą. Zawiera on zbiór linków do tematów związanych z tym obszarem.

> [!IMPORTANT]
> [Użycie programu Azure AD Connect to najlepszy sposób na połączenie katalogu lokalnego z usługami Azure AD i Office 365. To doskonały moment na uaktualnienie do programu Azure AD Connect z narzędzia Windows Azure Active Directory Sync (DirSync) lub Azure AD Sync, ponieważ są one przestarzałe, a wsparcie dla nich zakończy się 13 kwietnia 2017 r.](active-directory-aadconnect-dirsync-deprecated.md)  Ponadto:
> 
> - Synchronizowanie użytkowników z usługą Azure AD jest **bezpłatną funkcją** i nie wymaga posiadania jakiejkolwiek płatnej subskrypcji od klientów.
>- Zsynchronizowanym użytkownikom **nie przydziela się automatycznie** *żadnej* licencji. Administratorzy nadal mają całkowitą kontrolę nad przydzielaniem licencji. 
> - Firma Microsoft zaleca, aby administratorzy IT zsynchronizowali wszystkich swoich użytkowników. Dzięki temu nie tylko odblokowuje się dostęp użytkowników do wszelkich zintegrowanych zasobów usługi Azure AD, ale też zapewnia się znacznie szerszy widok dla administratorów IT, którzy mogą zobaczyć, do których aplikacji uzyskują dostęp użytkownicy. 

![Co to jest program Azure AD Connect](media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>Dlaczego warto korzystać z programu Azure AD Connect
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów, zarówno lokalnych, jak i w chmurze. Użytkownicy i organizacje uzyskują następujące korzyści:

* Użytkownicy mogą korzystać z jednej tożsamości w celu uzyskiwania dostępu do aplikacji lokalnych oraz usług w chmurze, takich jak Office 365.
* Jedno narzędzie zapewniające łatwe w użyciu środowisko wdrażania na potrzeby synchronizacji i logowania.
* Najnowsze możliwości we wszystkich scenariuszach. Program Azure AD Connect zastępuje starsze wersje narzędzi do integracji tożsamości, takie jak DirSync i Azure AD Sync. Aby uzyskać więcej informacji, zobacz [Porównanie narzędzi do integracji katalogów tożsamości hybrydowej](../active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Jak działa program Azure AD Connect
Program Azure Active Directory Connect obejmuje trzy główne składniki: usługi synchronizacji, opcjonalny składnik usług federacyjnych Azure Directory oraz składnik monitorowania o nazwie [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).

<center>![Stos programu Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Synchronizacja — ten składnik odpowiada za tworzenie użytkowników, grup i innych obiektów. Odpowiada także za zapewnienie zgodności informacji o tożsamości lokalnych użytkowników i grup z informacjami w chmurze.
* AD FS — usługi federacyjne to opcjonalny składnik programu Azure AD Connect, za pomocą którego można skonfigurować środowisko hybrydowe przy użyciu lokalnej infrastruktury usług AD FS. Za jego pomocą organizacje mogą obsługiwać złożone wdrożenia, na przykład obejmujące logowanie jednokrotne w domenie, wymuszanie stosowania zasad logowania usługi AD lub korzystanie z karty inteligentnej bądź usługi MFA innych firm.
* Monitorowanie kondycji — składnik Azure AD Connect Health zapewnia zaawansowane monitorowanie z możliwością wyświetlania aktywności w centralnej lokalizacji w portalu Azure. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Connect Health](../connect-health/active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Instalowanie programu Azure AD Connect
Program Azure AD Connect można pobrać z [Centrum pobierania Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).

| Rozwiązanie | Scenariusz |
| --- | --- |
| Przed rozpoczęciem — [Sprzęt i wymagania wstępne](active-directory-aadconnect-prerequisites.md) |<li>Czynności, które należy wykonać przed rozpoczęciem instalacji programu Azure AD Connect.</li> |
| [Ustawienia ekspresowe](active-directory-aadconnect-get-started-express.md) |<li>Jeśli masz usługę AD z jednym lasem, jest to zalecana opcja.</li> <li>Użytkownicy logują się przy użyciu tego samego hasła dzięki synchronizacji haseł.</li> |
| [Ustawienia dostosowane](active-directory-aadconnect-get-started-custom.md) |<li>Opcja używana, gdy masz większą liczbę lasów. Obsługuje wiele [topologii](active-directory-aadconnect-topologies.md) lokalnych.</li> <li>Możesz dostosować opcje logowania, takie jak usługi federacyjne AD FS lub inny dostawca tożsamości.</li> <li>Możesz dostosować funkcje synchronizacji, na przykład filtrowanie i zapisywanie zwrotne.</li> |
| [Uaktualnianie z narzędzia DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Używane, jeśli masz już działający serwer DirSync.</li> |
| [Uaktualnienie z narzędzia Azure AD Sync lub Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Istnieje kilka różnych metod do wyboru w zależności od preferencji.</li> |

[Po instalacji](active-directory-aadconnect-whats-next.md) należy sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, i przydzielić licencje do użytkowników.

### <a name="next-steps-to-install-azure-ad-connect"></a>Następne kroki instalowania programu Azure AD Connect
|Temat |Link|  
| --- | --- |
|Pobieranie programu Azure AD Connect | [Pobieranie programu Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalowanie przy użyciu ustawień ekspresowych | [Ekspresowa instalacja programu Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalowanie przy użyciu ustawień dostosowanych | [Niestandardowa instalacja programu Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Uaktualnianie przy użyciu narzędzia DirSync | [Uaktualnianie z narzędzia Azure AD Sync (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Po instalacji | [Weryfikowanie instalacji i przypisywanie licencji](active-directory-aadconnect-whats-next.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Więcej informacji o instalowaniu programu Azure AD Connect
Warto również przygotować się na kwestie związane z [działaniem](active-directory-aadconnectsync-operations.md). Może być wskazane zastosowanie serwera zapasowego umożliwiającego łatwe przełączenie w przypadku [awarii](active-directory-aadconnectsync-operations.md#disaster-recovery). Jeśli planujesz częste wprowadzanie zmian konfiguracji, należy zaplanować serwer [trybu przejściowego](active-directory-aadconnectsync-operations.md#staging-mode).

|Temat |Link|  
| --- | --- |
|Obsługiwane topologie | [Topologie obsługiwane w programie Azure AD Connect](active-directory-aadconnect-topologies.md)|
|Zagadnienia dotyczące projektowania | [Zagadnienia dotyczące projektowania przy korzystaniu z programu Azure AD Connect](active-directory-aadconnect-design-concepts.md)|
|Konta używane do instalacji | [Więcej informacji na temat poświadczeń i uprawnień dla programu Azure AD Connect](./active-directory-aadconnect-accounts-permissions.md)|
|Planowanie operacyjne | [Synchronizacja programu Azure AD Connect: zagadnienia i zadania operacyjne](active-directory-aadconnectsync-operations.md)|
|Opcje logowania użytkowników | [Opcje logowania użytkowników w programie Azure AD Connect](active-directory-aadconnect-user-signin.md)|

## <a name="configure-sync-features"></a>Konfigurowanie funkcji synchronizacji
Program Azure AD Connect zawiera szereg funkcji, które są domyślnie włączone lub które można włączyć opcjonalnie. Niektóre funkcje mogą wymagać wykonania dodatkowych czynności konfiguracyjnych w określonych scenariuszach i topologiach.

[Filtrowanie](active-directory-aadconnectsync-configure-filtering.md) umożliwia ograniczenie zakresu obiektów synchronizowanych z usługą Azure AD. Domyślnie synchronizacja obejmuje wszystkich użytkowników, kontakty, grupy i komputery z systemem Windows 10. Możesz zmienić ustawienia filtrowania na podstawie domen, jednostek organizacyjnych lub atrybutów.

[Synchronizacja skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md) umożliwia synchronizowanie skrótów haseł w usłudze Active Directory z usługą Azure AD. Użytkownik końcowy może korzystać z tego samego hasła lokalnie i w chmurze, zarządzając nim w jednej lokalizacji. Ponieważ źródłem jest lokalna usługa Active Directory, można używać również własnych zasad haseł.

[Zapisywanie zwrotne haseł](../active-directory-passwords-getting-started.md) umożliwia użytkownikom zmienianie i resetowanie haseł w chmurze i stosowanie lokalnych zasad haseł.

[Zapisywanie zwrotne urządzeń](active-directory-aadconnect-feature-device-writeback.md) umożliwia zapisywanie w lokalnej usłudze Active Directory urządzeń zarejestrowanych w usłudze Azure AD w celu użycia ich na potrzeby dostępu warunkowego.

Funkcja [zapobiegania przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) jest domyślnie włączona i zabezpiecza katalog w chmurze przed usunięciem dużej liczby elementów jednocześnie. Domyślnie dozwolone jest usunięcie 500 elementów w jednym przebiegu. Możesz zmienić to ustawienie w zależności od wielkości organizacji.

[Automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md) jest domyślnie włączone w przypadku instalacji z ustawieniami ekspresowymi i zapewnia korzystanie z najnowszej wersji programu Azure AD Connect.

### <a name="next-steps-to-configure-sync-features"></a>Następne kroki konfigurowania funkcji synchronizacji
|Temat |Link|  
| --- | --- |
|Konfigurowanie filtrowania | [Synchronizacja programu Azure AD Connect: konfigurowanie filtrowania](active-directory-aadconnectsync-configure-filtering.md)|
|Synchronizacja skrótów haseł | [Synchronizacja programu Azure AD Connect: implementowanie synchronizacji skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md)|
|Zapisywanie zwrotne haseł | [Wprowadzenie do zarządzania hasłami](../active-directory-passwords-getting-started.md)|
|Zapisywanie zwrotne urządzeń | [Włączanie zapisywania zwrotnego urządzeń w programie Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md)|
|Zapobieganie przypadkowemu usuwaniu | [Synchronizacja programu Azure AD Connect: zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)|
|Automatycznie uaktualnianie | [Azure AD Connect: automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Dostosowywanie synchronizacji w programie Azure AD Connect
Składnik synchronizacji programu Azure AD Connect ma konfigurację domyślną, która powinna działać w przypadku większości klientów i topologii. Zawsze jednak mogą występować sytuacje, w których konfiguracja domyślna nie sprawdza się i wymaga dostosowania. Obsługiwane jest wprowadzanie zmian zgodnie z opisem w tej sekcji i w połączonych tematach.

Jeśli nie masz doświadczenia z topologią synchronizacji, warto zapoznać się z podstawowymi informacjami i używanymi pojęciami przedstawionymi w temacie [zagadnienia techniczne](active-directory-aadconnectsync-technical-concepts.md). Program Azure AD Connect powstał na podstawie narzędzi MIIS2003, ILM2007 i FIM2010. Mimo że niektóre elementy są identyczne, wprowadzono także wiele zmian.

[Konfiguracja domyślna](active-directory-aadconnectsync-understanding-default-configuration.md) dopuszcza istnienie więcej niż jednego lasu w konfiguracji. W takich topologiach obiekt użytkownika może być reprezentowany jako kontakt w innym lesie. Użytkownik może także mieć połączoną skrzynkę pocztową w innym lesie zasobów. Działanie konfiguracji domyślnej przedstawiono w temacie [użytkownicy i kontakty](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Model konfiguracji synchronizacji jest nazywany [aprowizacją deklaratywną](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Zaawansowane przepływy atrybutów używają [funkcji](active-directory-aadconnectsync-functions-reference.md) do wyrażania przekształceń atrybutów. Całą konfigurację można wyświetlić i sprawdzić za pomocą narzędzi dostarczanych wraz z programem Azure AD Connect. Jeśli konieczne jest wprowadzenie zmian w konfiguracji, pamiętaj o postępowaniu zgodnie z [najlepszymi rozwiązaniami](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), co ułatwi przyjmowanie nowych wersji.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Następne kroki dostosowywania synchronizacji w programie Azure AD Connect
|Temat |Link|  
| --- | --- |
|Wszystkie artykuły dotyczące synchronizacji programu Azure AD Connect | [Synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md)|
|Zagadnienia techniczne | [Synchronizacja programu Azure AD Connect: zagadnienia techniczne](active-directory-aadconnectsync-technical-concepts.md)|
|Opis konfiguracji domyślnej | [Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej](active-directory-aadconnectsync-understanding-default-configuration.md)|
|Opis użytkowników i kontaktów | [Synchronizacja programu Azure AD Connect: opis użytkowników i kontaktów](active-directory-aadconnectsync-understanding-users-and-contacts.md)|
|Aprowizacja deklaratywna | [Synchronizacja programu Azure AD Connect: wyjaśnienie wyrażeń związanych z aprowizacją deklaratywną](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)|
|Zmienianie konfiguracji domyślnej | [Najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Konfigurowanie funkcji federacyjnych

Program Azure AD Connect udostępnia pewne funkcje, które upraszczają sfederowanie z usługą Azure AD za pomocą usług AD FS i zarządzanie relacjami zaufania federacji. Program Azure AD Connect obsługuje usługi AD FS w systemie Windows Server 2012 R2 lub nowszym.

[Aktualizuj certyfikat SSL farmy usług AD FS](active-directory-aadconnectfed-ssl-update.md), nawet jeśli nie używasz programu Azure AD Connect do zarządzania relacjami zaufania federacji.

[Dodaj serwer usług AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) do farmy, aby rozwinąć farmę zgodnie z potrzebami.

[Napraw zaufanie](active-directory-aadconnect-federation-management.md#repairthetrust) przy użyciu usługi Azure AD za pomocą kilku prostych kliknięć.

Usługi AD FS można skonfigurować pod kątem obsługi [wielu domen](active-directory-aadconnect-multiple-domains.md). Możesz na przykład mieć wiele domen najwyższego poziomu, których musisz użyć na potrzeby federacji.

Jeśli serwer usług AD FS nie został skonfigurowany tak, aby automatycznie aktualizował certyfikaty z usługi Azure AD, lub jeśli korzystasz z rozwiązania innego niż AD FS, otrzymasz powiadomienie o konieczności [zaktualizowania certyfikatów](active-directory-aadconnect-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Następne kroki konfigurowania funkcji federacyjnych
|Temat |Link|  
| --- | --- |
|Wszystkie artykuły dotyczące usług AD FS | [Program Azure AD Connect a federacja](active-directory-aadconnectfed-whatis.md)|
|Konfigurowanie usług AD FS z poddomenami | [Obsługa wielu domen do federowania w usłudze Azure AD](active-directory-aadconnect-multiple-domains.md)|
|Zarządzanie farmą usług AD FS | [Dostosowywanie usług AD FS i zarządzanie nimi za pomocą programu Azure AD Connect](active-directory-aadconnect-federation-management.md)|
|Ręczne aktualizowanie certyfikatów federacji | [Odnawianie certyfikatów federacji dla usług Office 365 i Azure AD](active-directory-aadconnect-o365-certs.md)|

## <a name="more-information-and-references"></a>Więcej informacji i materiały referencyjne
|Temat |Link|  
| --- | --- |
|Historia wersji | [Historia wersji](active-directory-aadconnect-version-history.md)|
|Porównanie narzędzi DirSync, Azure ADSync i Azure AD Connect | [Porównanie narzędzi do integracji katalogów](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)|
|Lista zgodności usługi Azure AD z usługami innymi niż AD FS | [Lista zgodności usługi Azure AD z usługami federacyjnymi](active-directory-aadconnect-federation-compatibility.md)|
|Konfigurowanie dostawcy tożsamości SAML 2.0|[Korzystanie z dostawcy tożsamości SAML 2.0 do celów rejestracji jednokrotnej](active-directory-aadconnect-federation-saml-idp.md)|
|Synchronizowane atrybuty | [Synchronizowane atrybuty](active-directory-aadconnectsync-attributes-synchronized.md)|
|Monitorowanie za pomocą narzędzia Azure AD Connect Health | [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)|
|Często zadawane pytania | [Azure AD Connect — Często zadawane pytania](active-directory-aadconnect-faq.md)|

**Dodatkowe zasoby**

Prezentacja z konferencji Ignite 2015 dotycząca rozszerzania katalogów lokalnych do chmury.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 

