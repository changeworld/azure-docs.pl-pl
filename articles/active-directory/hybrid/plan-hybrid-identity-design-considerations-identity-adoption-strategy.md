---
title: Projektowania tożsamości hybrydowej — strategii wdrażania platformy Azure | Dokumentacja firmy Microsoft
description: Kontrola dostępu warunkowego usługi Azure Active Directory sprawdza, czy określonych warunków, wybranego podczas uwierzytelniania użytkownika, a jeśli tak, to przed zezwoleniem na dostęp do aplikacji. Po spełnieniu tych warunków, użytkownik jest uwierzytelniony i zezwolenie na dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d64cac3812d8daf8ac34b93c91338e1dfab88a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60382003"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiowanie strategii wdrażania tożsamości hybrydowej
To zadanie służy do definiowania strategii wdrażania tożsamości hybrydowej dla Twojego rozwiązania tożsamości hybrydowej w celu spełnienia wymagań biznesowych, które zostały omówione w:

* [Określanie potrzeb biznesowych](plan-hybrid-identity-design-considerations-business-needs.md)
* [Określić wymagania dotyczące synchronizacji katalogu](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiowanie strategii potrzeb biznesowych
Musi mieć pierwszy adresy zadań określania biznesowych organizacji.  Może to być bardzo ogólny i pełzania zakresu może wystąpić, jeśli nie zostaną zaktualizowane.  Na początku Zachowaj prostotę, ale należy pamiętać o Planowanie projektu, który będzie dostosowania i Ułatw zmianę w przyszłości.  Niezależnie od tego, czy jest prosty projekt lub jeden bardzo złożone, usługi Azure Active Directory jest platformą Microsoft Identity, który obsługuje usługi Office 365, Microsoft Online Services i w chmurze aplikacji współpracujących.

## <a name="define-an-integration-strategy"></a>Definiowanie strategii integracji
Firma Microsoft ma trzy scenariusze integracji głównego, będące tożsamości w chmurze, synchronizacja tożsamości i tożsamości federacyjnych.  Należy zaplanować na przyjmującą jeden z następujących strategii integracji.  Strategii, którą wybierzesz mogą się różnić i mogą obejmować decyzje, wybierając jeden, jakiego rodzaju środowisko użytkownika, które chcesz udostępnić, czy masz istniejącą infrastrukturę, co to jest najbardziej ekonomiczne.  

![](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scenariusze zdefiniowane na powyższej ilustracji są następujące:

* **Tożsamości w chmurze**: są to tożsamości, które istnieją wyłącznie w chmurze.  W przypadku usługi Azure AD czy zostaną one umieszczone w katalogu usługi Azure AD.
* **Synchronizowane**: są to tożsamości, które istnieją w środowisku lokalnym i w chmurze.  Za pomocą programu Azure AD Connect, ci użytkownicy są tworzone lub łączone z istniejących kont usługi Azure AD.  Skrót hasła użytkownika jest synchronizowany ze środowiska lokalnego do chmury w tak zwany skrót hasła.  Podczas synchronizowania za pomocą jednego zastrzeżenie: to, jeśli użytkownik jest wyłączony w środowisku lokalnym, może potrwać do trzech godzin stan konta pojawienie się w usłudze Azure AD.  Jest to spowodowane przedział czasu synchronizacji.
* **Federacyjna**: tych tożsamości istnieje zarówno w środowisku lokalnym i w chmurze.  Za pomocą programu Azure AD Connect, ci użytkownicy są tworzone lub łączone z istniejących kont usługi Azure AD.  

> [!NOTE]
> Aby uzyskać więcej informacji na temat opcji synchronizacji, przeczytaj [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Poniższa tabela pomoże w określeniu, zalety i wady każdego z następujących strategii:

| Strategia | Zalety | Wady |
| --- | --- | --- |
| **Tożsamości w chmurze** |Łatwiejsze zarządzanie w małych organizacji. <br> Wymaga instalacji żadnych składników w środowisku lokalnym. Żaden dodatkowy sprzęt, wymagane<br>Łatwo wyłączone, jeśli użytkownik opuści firmę |Użytkownicy będą musieli zarejestrować się w przypadku uzyskiwania dostępu do obciążeń w chmurze <br> Hasła mogą lub nie może być taka sama dla tożsamości w chmurze i lokalnych |
| **Zsynchronizowany** |Hasło uwierzytelniania, zarówno w lokalnych, jak i w chmurze lokalnie katalogów <br>Łatwiejsza w zarządzaniu małe, średnie lub duże organizacje <br>Użytkownicy mogą mieć logowania jednokrotnego (SSO) dla niektórych zasobów <br> Microsoft preferowana metoda synchronizacji <br> Łatwiejsze zarządzanie |Niektórzy klienci mogą być zapewniony zsynchronizować ich katalogów przy użyciu chmury powodu policyjnych określonej firmy |
| **Federacyjna** |Użytkownicy mogą mieć logowania jednokrotnego (SSO) <br>Jeśli użytkownik zostanie zakończony, pozostawia konto zostać natychmiast wyłączone i odwołać dostęp,<br> Obsługuje zaawansowane scenariusze, w których nie da się osiągnąć za pomocą synchronizacji |Więcej instrukcje instalowania i konfigurowania <br> Wyższe konserwacji <br> Może wymagać dodatkowego sprzętu dla infrastruktury usługi STS <br> Może wymagać dodatkowego sprzętu, aby zainstalować serwer federacyjny. Wymagane jest dodatkowe oprogramowanie, jeśli jest używany przez usługi AD FS <br> Wymagaj rozbudowane konfiguracji logowania jednokrotnego <br> Punkt krytyczny błąd, jeśli działa na serwerze federacyjnym, użytkownicy nie będą mogli uwierzytelniania |

### <a name="client-experience"></a>Środowisko pracy klienta
Środowisko logowania użytkowników określają strategii, którego używasz.  Poniższe tabele zawierają informacje o jakie użytkowników należy się spodziewać ich logowania się.  Nie wszyscy dostawcy tożsamości federacyjnych obsługuje logowanie Jednokrotne we wszystkich scenariuszach.

**Aplikacje sieciowe przyłączonych do domeny, jak i prywatnych**:

|  | Tożsamości synchronizowane | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki sieci Web |Uwierzytelnianie oparte na formularzach |Logowanie jednokrotne, czasami trzeba podać identyfikator organizacji |
| Outlook |Monit o poświadczenia |Monit o poświadczenia |
| Skype dla firm (Lync) |Monit o poświadczenia |Logowanie jednokrotne do usługi Lync, zostanie wyświetlony monit poświadczeń dla programu Exchange |
| OneDrive dla Firm |Monit o poświadczenia |Logowanie jednokrotne |
| Office Pro Plus subskrypcji |Monit o poświadczenia |Logowanie jednokrotne |

**Źródła zewnętrznego lub niezaufanych**:

|  | Tożsamości synchronizowane | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki sieci Web |Uwierzytelnianie oparte na formularzach |Uwierzytelnianie oparte na formularzach |
| Program Outlook, Skype dla firm (Lync), usługi OneDrive dla firm, subskrypcja usługi Office |Monit o poświadczenia |Monit o poświadczenia |
| Exchange ActiveSync |Monit o poświadczenia |Logowanie jednokrotne do usługi Lync, zostanie wyświetlony monit poświadczeń dla programu Exchange |
| Aplikacji mobilnych |Monit o poświadczenia |Monit o poświadczenia |

W przypadku podjęcia decyzji w zadaniu 1, że masz innych firm dostawcy tożsamości lub czy będzie się go użyć do zapewnienia federacji z usługą Azure AD, trzeba pamiętać o następujących obsługiwanych możliwości:

* Wszystkich dostawców SAML 2.0, który jest zgodny, SP Lite profil, który może obsługiwać uwierzytelnianie w usłudze Azure AD i skojarzone aplikacje
* Obsługuje uwierzytelnianie pasywnym, które ułatwia uwierzytelnianie programu OWA, SPO itp.
* Klienci usługi Exchange Online mogą być obsługiwane za pośrednictwem protokołu SAML 2.0 rozszerzone klienta profilu (ECP)

Należy również pamiętać o jakie funkcje nie będą dostępne:

* Bez obsługi protokołu WS-Trust/Federation przerwanie wszystkich aktywnych klientów
  * Oznacza to nie klienta Lync, klient usługi OneDrive, subskrypcja usługi Office, pakietu Office Mobile przed pakiety Office 2016
* Przejście pakietu Office uwierzytelnianiem pasywnym pozwala, aby zapewnić obsługę czystego SAML 2.0 dostawców, ale nadal będzie pomocy technicznej na podstawie danych klienta przez klienta

> [!NOTE]
> Aby uzyskać najbardziej zaktualizowanej listy, przeczytaj artykuł [listę zgodności federacyjnych usługi Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definiowanie strategii synchronizacji
W tym zadaniu zostanie zdefiniujesz, narzędzia, które będą używane do synchronizowania organizacji danych lokalnych do chmury i topologii, należy użyć.  Ponieważ większość organizacji używa usługi Active Directory, informacji o korzystaniu z usługi Azure AD Connect rozwiązywać powyższe pytania znajduje się w kilku szczegółów.  W środowiskach, które nie mają usługi Active Directory Brak informacji o używaniu programu FIM 2010 R2 lub programu MIM 2016, aby ułatwić zaplanowanie tej strategii.  Jednak przyszłych wersjach programu Azure AD Connect będzie obsługiwać katalogów LDAP, więc w zależności od Twojej osi czasu, te informacje można uzyskać pomoc.

### <a name="synchronization-tools"></a>Narzędzia do synchronizacji
W ciągu lat kilka narzędzi synchronizacji mają istniał i używane dla różnych scenariuszy.  Obecnie usługa Azure AD Connect jest przejdź do narzędzia wyborem dla wszystkich obsługiwanych scenariuszach.  AAD Sync oraz narzędzia DirSync są nadal wokół i może nawet znajdować się w danym środowisku teraz. 

> [!NOTE]
> Aby uzyskać najnowsze informacje dotyczące obsługiwanych możliwości każdego z narzędzi, przeczytaj [porównanie narzędzi integracji katalogu](plan-hybrid-identity-design-considerations-tools-comparison.md) artykułu.  
> 
> 

### <a name="supported-topologies"></a>Obsługiwane topologie
Podczas definiowania strategii synchronizacji, należy określić topologię, która jest używana. W zależności od informacji, które zostało określone w kroku 2 można określić, która topologia jest odpowiednią z nich do użycia. Pojedynczy las, pojedynczy topologii usługi Azure AD jest najbardziej typowe i składa się z jednego lasu usługi Active Directory i jedno wystąpienie usługi Azure AD.  To ma być używane w większości scenariuszy i jest oczekiwany topologii, gdy za pomocą usługi Azure AD Connect instalacji programu Express, jak pokazano na poniższej ilustracji.

![](./media/plan-hybrid-identity-design-considerations/single-forest.png) Scenariusz pojedynczego lasu jest typowe dla nawet małych i dużych organizacji mają wiele lasów, jak pokazano na rysunku 5.

> [!NOTE]
> Aby uzyskać więcej informacji na temat różnych lokalnych i topologii usługi Azure AD za pomocą usługi Azure AD Connect sync, przeczytaj artykuł [topologie obsługiwane w programie Azure AD Connect](plan-connect-topologies.md).
> 
> 

![](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenariusza obejmującego wiele lasów

Jeśli jest to wymagane, a następnie jedną obejmującego wiele lasów topologii usługi Azure AD należy rozważyć, gdy spełnione są następujące elementy:

* Użytkownicy mają tylko 1 tożsamości we wszystkich lasach — jednoznacznie identyfikujące użytkowników poniższej sekcji opisano to bardziej szczegółowo.
* Użytkownik jest uwierzytelniany w lesie, w którym znajduje się ich tożsamości
* Nazwy UPN i zakotwiczenie źródła (niemodyfikowalny identyfikator) będą pochodzić z tego lasu
* Wszystkich lasach są dostępne przy użyciu usługi Azure AD Connect-oznacza to, że nie musi być przyłączony do domeny i można umieścić w sieci obwodowej Jeśli ułatwia to.
* Użytkownicy mają tylko jedną skrzynkę pocztową
* Lasu, który hostuje skrzynki pocztowej użytkownika ma najlepszą jakość danych dla atrybutów widoczne w globalnej listy adresów (GAL) programu Exchange
* Jeśli jest nie skrzynki pocztowej użytkownika, na następnie każdego lasu może służyć do współtworzenia te wartości
* Jeśli masz połączoną skrzynkę pocztową, to ma również innego konta w innym lesie użyte do zalogowania.

> [!NOTE]
> Obiekty, które istnieją w swoich lokalnych i w chmurze są "połączone" za pomocą unikatowego identyfikatora. W kontekście synchronizacji katalogów ten unikatowy identyfikator jest określany jako SourceAnchor. W kontekście z logowania jednokrotnego to jest określane jako wartość ImmutableId. [Projektowanie pojęcia dotyczące programu Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) więcej uwagi dotyczące użycia programu SourceAnchor.
> 
> 

Jeśli masz więcej niż jedno konto active lub więcej niż jedną skrzynkę pocztową powyższe warunki nie zostaną spełnione, program Azure AD Connect wybierz jedną, a druga Ignoruj.  Jeśli zostały połączone skrzynek pocztowych, ale żadne inne konto, te konta nie zostaną wyeksportowane do usługi Azure AD, a ten użytkownik nie będą członkami żadnych grup.  To różni się od sposobu z przeszłości w przypadku narzędzia DirSync i jest zamierzone, aby lepiej Obsługa tych scenariuszy obejmującego wiele lasów. Na poniższej ilustracji przedstawiono scenariusza obejmującego wiele lasów.

![](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Wiele lasów wielu scenariuszy usługi Azure AD**

Zaleca się mają jeden katalog, w usłudze Azure AD dla organizacji, ale jest obsługiwana między serwerem synchronizacji Azure AD Connect i katalog usługi Azure AD jest pozwoliło relacji 1:1.  Dla każdego wystąpienia usługi Azure AD należy zainstalować program Azure AD Connect.  Ponadto usługa Azure AD, zgodnie z projektem jest izolowane i użytkowników w jednym wystąpieniu usługi Azure AD nie będzie można wyświetlić użytkowników w innym wystąpieniu.

Jest to możliwe i obsługiwanych połączyć lokalne wystąpienie usługi Active Directory wiele katalogów usługi Azure AD, jak pokazano na poniższej ilustracji:

![](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Scenariusz filtrowania obejmujących jeden las**

Aby to zrobić, należy spełnienia następujących warunków:

* Serwery synchronizacji programu Azure AD Connect musi być skonfigurowany w celu filtrowania, aby każda z nich wzajemnie wykluczającym się zestawie obiektów.  To, na przykład według zakresu każdego serwera do określonej domeny lub jednostki Organizacyjnej.
* Domena DNS można rejestrować tylko w jednym katalogu usługi Azure AD więc nazwy UPN użytkowników w lokalnej usługi AD musi używać oddzielnych przestrzeni nazw
* Użytkownicy w jednym wystąpieniu usługi Azure AD będą tylko mogli zobaczyć użytkowników z ich wystąpienia.  Nie będą mogli zobaczyć użytkowników w innych wystąpieniach
* Tylko jeden z katalogów usługi Azure AD można włączyć wdrożenie hybrydowe programu Exchange przy użyciu lokalnej usługi AD
* Wzajemne wyłączności dotyczy również do zapisu.  To sprawia, że niektóre funkcje zapisu nie są obsługiwane przy użyciu tej topologii, ponieważ te zakładają konfiguracji pojedynczym lokalnym.  Obejmuje to:
  * Grupa zapisu z konfiguracji domyślnej
  * Zapisywanie zwrotne urządzeń

Następujące nie jest obsługiwane i nie powinna być wybrana jako implementację:

* Wiele serwerów synchronizacji Azure AD Connect, łączenie z tego samego katalogu usługi Azure AD, nawet jeśli są one skonfigurowane do synchronizacji wzajemnie wykluczającym się zestawie obiektów nie jest obsługiwane
* Go nie jest obsługiwany do synchronizacji z tego samego użytkownika do wielu katalogów usługi Azure AD. 
* Również obsługiwane Konfiguracja się użytkowników w usłudze Azure AD są wyświetlane jako kontakty w innym katalogu usługi Azure AD. 
* Jest również nieobsługiwany zmodyfikować synchronizacji Azure AD Connect, aby nawiązać połączenie z wieloma katalogami usługi Azure AD.
* Katalogi usługi Azure AD są celowe samodzielnie. Jest nieobsługiwany w celu zmiany konfiguracji synchronizacji Azure AD Connect można odczytać danych z innego katalogu usługi Azure AD w celu podjęcia próby kompilacji typowe i ujednolicone GAL między katalogami. Obsługiwane jest również Eksportuj użytkowników jako kontakty do innego lokalnej usługi AD za pomocą synchronizacji programu Azure AD Connect.

> [!NOTE]
> Jeśli Twoja organizacja uniemożliwia łączenie się z Internetem komputerów w sieci, w tym artykule wymieniono punkty końcowe (nazw FQDN, IPv4 i IPv6, zakresy adresów), należy uwzględnić w swojej ruchu wychodzącego Zezwalaj, list i Internet Explorer Zaufane witryny klienta komputery, aby upewnić się, komputery pomyślnie użyć usługi Office 365. Aby uzyskać więcej informacji, przeczytaj [URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiowanie strategii uwierzytelniania wieloskładnikowego
W tym zadaniu zdefiniujesz strategii uwierzytelniania wieloskładnikowego do użycia.  Usługa Azure Multi-Factor Authentication jest dostępna w dwóch różnych wersji.  Jeden jest oparty na chmurze, a drugi to lokalne przy użyciu serwera Azure MFA.  Na podstawie oceny powyżej, które można określić, które rozwiązanie jest poprawne strategii.  Aby określić, która opcja Projekt najlepiej spełnia wymagania zabezpieczeń w firmie, należy użyć poniższej tabeli:

Opcje projektu usługi Multi-Factor Authentication:

| Zasób, aby zabezpieczyć | Usługa MFA w chmurze | Lokalna usługa MFA |
| --- | --- | --- |
| Aplikacje firmy Microsoft |tak |tak |
| Aplikacje SaaS w galerii aplikacji |tak |tak |
| Aplikacje usług IIS opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD |tak |tak |
| Aplikacje usług IIS nie zostały opublikowane za pośrednictwem serwera Proxy aplikacji usługi Azure AD |nie |tak |
| Dostępu zdalnego VPN i Brama usług pulpitu zdalnego |nie |tak |

Mimo że możesz mogą mieć rozliczane na rozwiązanie strategii, nadal należy korzystać z wersji ewaluacyjnej z powyższych na gdzie są Twoi użytkownicy.  Może to spowodować, że rozwiązanie, aby zmienić.  Użyj poniższej tabeli, aby pomóc w określaniu, to:

| Lokalizacja użytkownika | Opcja preferowane rozwiązanie |
| --- | --- |
| Usługa Azure Active Directory |Multi-FactorAuthentication w chmurze |
| Usługa Azure AD i lokalna usługa AD przy użyciu federacji z usługami AD FS |Oba |
| Usługa Azure AD i lokalnej usługi AD za pomocą usługi Azure AD Connect Brak synchronizacji haseł |Oba |
| Usługa Azure AD i lokalnych przy użyciu usługi Azure AD Connect z funkcją synchronizacji haseł |Oba |
| Lokalnej usługi AD |Serwer Multi-Factor Authentication |

> [!NOTE]
> Należy upewnić się, że zaznaczonej opcji projektu usługi Multi-Factor authentication obsługuje funkcje, które są wymagane dla projektu.  Aby uzyskać więcej informacji, przeczytaj [wybierz rozwiązania zabezpieczeń w usłudze Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Dostawcy usługi Multi-Factor Authentication
Uwierzytelnianie wieloskładnikowe jest domyślnie dostępny dla administratorów globalnych dzierżawiących usługę Azure Active Directory. Jednak jeśli chcesz rozszerzyć uwierzytelniania wieloskładnikowego dla wszystkich użytkowników i/lub mają administratorom globalnym, aby można było wykonać funkcje korzyści, takie jak portal zarządzania, niestandardowe pozdrowienia i raporty, dzięki następnie należy zakupić i skonfigurować Dostawca usługi Multi-Factor Authentication.

> [!NOTE]
> Należy upewnić się, że zaznaczonej opcji projektu usługi Multi-Factor authentication obsługuje funkcje, które są wymagane dla projektu. 
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określanie wymagań dotyczących ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

