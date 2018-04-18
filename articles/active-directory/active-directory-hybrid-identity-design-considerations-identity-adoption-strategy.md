---
title: Projektowania tożsamości hybrydowej — strategii wdrażania usługi Azure | Dokumentacja firmy Microsoft
description: Z kontroli dostępu warunkowego usługi Azure Active Directory sprawdza określonych warunków, można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji. Gdy te warunki są spełnione, użytkownik jest uwierzytelniony i zezwolenie na dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: e6c9cbc4f158e62092c7a9e401e618880e5ea3b6
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiowanie strategii wdrażania tożsamości hybrydowej
To zadanie służy do definiowania strategii wdrażania tożsamości hybrydowej dla hybrydowych rozwiązań tożsamości do spełnienia wymagań biznesowych, które zostały omówione w:

* [Określanie potrzeb biznesowych](active-directory-hybrid-identity-design-considerations-business-needs.md)
* [Określenie wymagań synchronizacji katalogu](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Określić wymagania dotyczące uwierzytelniania wieloskładnikowego](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiowanie strategii potrzeb biznesowych
Musi mieć pierwszych adresów zadań określania biznesowe organizacji.  Może to być bardzo szeroki i zakres pełzanie może wystąpić, jeśli nie są dokładne.  Na początku należy zachować prostotę, ale należy pamiętać o planowania projektu, który będzie pomieścić a ułatwienia zmian w przyszłości.  Niezależnie od tego, czy jest prostego projektu lub jeden bardzo złożonych, Azure Active Directory jest platformy pakietu Microsoft Identity, która obsługuje usługi Office 365, Microsoft Online Services i chmura aplikacjom obsługującym.

## <a name="define-an-integration-strategy"></a>Definiowanie strategii integracji
Firma Microsoft udostępnia trzy scenariusze integracji głównego tożsamości w chmurze, tożsamości synchronizowane i tożsamości federacyjnych.  Należy zaplanować na przyjmowanie jedną z następujących strategii integracji.  Strategia wybrane może różnić się i mogą obejmować decyzji w wybranie jednej, jakiego rodzaju środowisko użytkownika, aby określić, czy masz niektórych z istniejącej infrastruktury już na miejscu i co to jest najbardziej ekonomiczne.  

![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Scenariusze zdefiniowane na rysunku powyżej są następujące:

* **Tożsamości w chmurze**: są to tożsamości, które istnieją tylko w chmurze.  W przypadku usługi Azure AD czy są one przechowywane w katalogu usługi Azure AD.
* **Zsynchronizowane**: są to tożsamości, które są umiejscowione lokalnie i w chmurze.  Za pomocą usługi Azure AD Connect, użytkownicy są tworzone lub połączony z istniejących kont usługi Azure AD.  Skrót hasła użytkownika są synchronizowane ze środowiska lokalnego do chmury w tzw skrót hasła.  Podczas synchronizacji przy użyciu jedno zastrzeżenie: jest, że jeśli użytkownik jest wyłączony w środowisku lokalnym, może potrwać do trzech godzin, zanim ten stan konta w usłudze Azure AD.  Jest to spowodowane interwał synchronizacji.
* **Federacyjna**: tymi tożsamościami istnieje lokalnie i w chmurze.  Za pomocą usługi Azure AD Connect, użytkownicy są tworzone lub połączony z istniejących kont usługi Azure AD.  

> [!NOTE]
> Aby uzyskać więcej informacji na temat opcji synchronizacji, przeczytaj [integrowanie tożsamości lokalnych z usługą Azure Active Directory](connect/active-directory-aadconnect.md).
> 
> 

Poniższa tabela pomoże w określeniu zalety i wady każdego z następujących strategii:

| Strategia | Zalety | Wady |
| --- | --- | --- |
| **Tożsamości w chmurze** |Łatwiejsze zarządzanie dla małych organizacji. <br> Nie można zainstalować dodatkowy sprzęt na — lokalnym — bez potrzeby<br>Łatwe wyłączenie, gdy użytkownik opuści firmę |Użytkownicy będą musieli zarejestrować się w przypadku uzyskiwania dostępu do obciążeń w chmurze <br> Hasła może lub nie może być taka sama dla tożsamości w chmurze i lokalnie |
| **Zsynchronizowane** |Lokalnego hasła uwierzytelnia lokalnie i w chmurze katalogów <br>Łatwiejsze zarządzanie mały, średni lub dużych organizacjach <br>Użytkownicy mogą mieć rejestracji jednokrotnej (SSO) dla niektórych zasobów <br> Metoda Microsoft preferowany do synchronizacji <br> Łatwiejsze zarządzanie |Niektórzy klienci mogą być chce Zsynchronizuj swoje katalogi z chmurą powodu polecenie określonej firmy |
| **Federacyjna** |Użytkownicy mogą mieć rejestracji jednokrotnej (SSO) <br>Jeśli użytkownik zostanie zakończony lub pozostawia, konto można natychmiast wyłączone i dostępu odwołane,<br> Obsługuje zaawansowane scenariusze, które nie mogą być realizowane za pomocą synchronizacji |Większej liczby kroków do instalowania i konfigurowania <br> Wyższy konserwacji <br> Może wymagać dodatkowego sprzętu infrastruktury usługi STS <br> Może wymagać dodatkowego sprzętu do zainstalowania serwera federacyjnego. Dodatkowe oprogramowanie jest wymagany, jeśli jest używany przez usługi AD FS <br> Wymagaj szeroką gamę Instalatora dla logowania jednokrotnego <br> Krytyczne punktu awarii, jeśli serwer federacyjny działa, użytkownicy nie będą mogli się uwierzytelnić w |

### <a name="client-experience"></a>Środowisko pracy klienta
Strategia, którego używasz wyznaczają środowisko logowania użytkownika.  Poniższe tabele zawierają informacje dotyczące ich środowiska logowania, należy oczekiwać co użytkownicy.  Nie wszyscy dostawcy tożsamości federacyjnych obsługują logowanie SSO we wszystkich scenariuszach.

**Aplikacje przyłączone domena, jak i prywatnych sieci**:

|  | Tożsamości synchronizowane | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki sieci Web |Uwierzytelnianie oparte na formularzach |Logowanie jednokrotne, czasami trzeba podać identyfikator organizacji |
| Outlook |Monit o podanie poświadczeń |Monit o podanie poświadczeń |
| Skype dla firm (Lync) |Monit o podanie poświadczeń |Logowanie jednokrotne Lync, monitowanie o poświadczenia dla programu Exchange |
| SkyDrive Pro |Monit o podanie poświadczeń |Logowanie jednokrotne |
| Office Pro Plus subskrypcji |Monit o podanie poświadczeń |Logowanie jednokrotne |

**Zewnętrznych lub niezaufanych źródeł**:

|  | Tożsamości synchronizowane | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki sieci Web |Uwierzytelnianie oparte na formularzach |Uwierzytelnianie oparte na formularzach |
| Outlook i Skype dla firm (Lync), Skydrive Pro subskrypcji pakietu Office |Monit o podanie poświadczeń |Monit o podanie poświadczeń |
| Exchange ActiveSync |Monit o podanie poświadczeń |Logowanie jednokrotne Lync, monitowanie o poświadczenia dla programu Exchange |
| Aplikacje mobilne |Monit o podanie poświadczeń |Monit o podanie poświadczeń |

Jeśli już wiesz zadanie 1, że będzie innych dostawców tożsamości lub są jej użyć do zapewnienia federacji z usługą Azure AD, należy należy pamiętać o następujących obsługiwane możliwości:

* Wszelkie dostawcy SAML 2.0, który jest zgodny z dodatkiem SP Lite profilu mogą obsługiwać uwierzytelniania do usługi Azure AD i skojarzone aplikacje
* Obsługuje uwierzytelnianie pasywnym, które umożliwia uwierzytelnianie OWA, SPO, itp.
* Klienci usługi Exchange Online mogą być obsługiwane za pośrednictwem SAML 2.0 rozszerzone klienta profilu (ECP)

Należy wziąć pod uwagę jakie funkcje przestaną być dostępne:

* Bez obsługi zaufania/federacyjnych przerwanie wszystkich aktywnych klientów
  * Oznacza to, że nie klienta Lync, klient usługi OneDrive, subskrypcji pakietu Office, Office Mobile przed pakietu Office 2016
* Przejście pakietu Office do uwierzytelnianiem pasywnym umożliwia im to obsługuje czysty SAML 2.0 IdPs, ale nadal będzie pomocy technicznej na podstawie klienta przez klienta

> [!NOTE]
> Listę najnowszych odczytać https://aka.ms/ssoproviders artykułu.
> 
> 

## <a name="define-synchronization-strategy"></a>Definiowanie strategii synchronizacji
To zadanie będzie definiował narzędzia, które będą używane do synchronizowania organizacji danych lokalnych do chmury i topologii, należy użyć.  Ponieważ większość organizacji używa usługi Active Directory, informacji na temat używania usługi Azure AD Connect do odpowiedzi na pytania powyżej podano niektóre szczegółowe.  W środowiskach, które nie ma usługi Active Directory Brak informacji o używaniu programu FIM 2010 R2 lub MIM 2016, aby ułatwić planowanie tej strategii.  Jednak przyszłych wersjach programu Azure AD Connect będzie obsługiwać katalogów LDAP, więc w zależności od osi czasu, te informacje mogą być w stanie udzielić pomocy.

### <a name="synchronization-tools"></a>Narzędzia do synchronizacji
Całościowo kilka narzędzi synchronizacji ma istniał i używane dla różnych scenariuszy.  Przejdź do wybranych we wszystkich scenariuszach obsługiwanych narzędzi jest obecnie Azure AD Connect.  AAD Sync DirSync są nadal wokół i może nawet występować w danym środowisku teraz. 

> [!NOTE]
> Aby uzyskać najnowsze informacje dotyczące obsługiwane możliwości narzędzia, przeczytaj [porównanie narzędzi integracji katalogów](active-directory-hybrid-identity-design-considerations-tools-comparison.md) artykułu.  
> 
> 

### <a name="supported-topologies"></a>Obsługiwane topologie
Podczas definiowania strategii synchronizacji, należy określić topologię, która jest używana. W zależności od informacje, które zostało ustalone w kroku 2 można określić, która topologia jest odpowiednie do użycia. Pojedynczy las, pojedynczy Topologia usługi Azure AD jest najbardziej popularnym i składa się z jednego lasu usługi Active Directory i jedno wystąpienie usługi Azure AD.  To ma być używane w większości scenariuszy i jest oczekiwany topologii, przy użyciu usługi Azure AD Connect Express instalacji, jak pokazano na poniższej ilustracji.

![](./media/hybrid-id-design-considerations/single-forest.png) Scenariusz jeden las jest typowe dla dużych i małych nawet organizacje mogą mieć wiele lasów, jak pokazano na rysunku 5.

> [!NOTE]
> Aby uzyskać więcej informacji na temat różnych lokalnymi i topologii usługi Azure AD z programem Azure AD Connect synchronizacji, przeczytaj artykuł na temat [topologii dla usługi Azure AD Connect](connect/active-directory-aadconnect-topologies.md).
> 
> 

![](./media/hybrid-id-design-considerations/multi-forest.png) 

Scenariusza obejmującego wiele lasów

Jeśli to wielkość liter, a następnie topologii kilku-forest-pojedynczej usługi Azure AD należy rozważyć, gdy spełnione są następujące elementy:

* Użytkownicy mają tylko 1 tożsamości we wszystkich lasach — jednoznacznego identyfikowania użytkowników poniższej sekcji opisano to bardziej szczegółowo.
* Użytkownik jest uwierzytelniany w lesie, w którym znajduje się tożsamości
* UPN i zakotwiczenie źródła (niezmienialnego identyfikatora) będą pochodzić z tego lasu
* Wszystkich lasach są dostępne przy użyciu usługi Azure AD Connect-oznacza to, że nie musi być przyłączony do domeny i można umieścić w sieci obwodowej Jeśli ułatwia to.
* Użytkowników ma tylko jedną skrzynkę pocztową
* Lasu, który hostuje skrzynki pocztowej użytkownika ma najlepszą jakość danych dla atrybutów widoczne w globalnej listy adresów (GAL) programu Exchange
* Jeśli użytkownik jest nie skrzynek pocztowych, następnie dowolnym lesie może służyć do ich współtworzenia te wartości
* Jeśli masz połączoną skrzynkę pocztową, występuje także inne konto w innym lesie użyte do logowania.

> [!NOTE]
> Obiekty, które istnieją w swoich lokalnych i w chmurze "połączenie" za pośrednictwem Unikatowy identyfikator. W kontekście synchronizacji katalogów ten unikatowy identyfikator jest określana jako SourceAnchor. W kontekście z logowania jednokrotnego to jest określana jako nazwę ImmutableId. [Zagadnienia dotyczące projektowania programu Azure AD Connect](connect/active-directory-aadconnect-design-concepts.md#sourceanchor) więcej uwagi dotyczące użycia elementu SourceAnchor.
> 
> 

Jeśli masz więcej niż jedno konto active lub więcej niż jedną skrzynkę pocztową powyższych nie są spełnione, Azure AD Connect wybierz jedną i innych zignorować.  Jeśli istnieje łącze skrzynek pocztowych, ale żadne inne konto, tych kont nie zostaną wyeksportowane do usługi Azure AD i że użytkownik nie będzie członkiem żadnej grupy.  To różni się od sposobu był w przeszłości z narzędzia DirSync i jest zamierzone, aby lepiej obsługi tych scenariuszy obejmujących wiele lasów. Na poniższej ilustracji przedstawiono scenariusza obejmującego wiele lasów.

![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 

**Obejmującego wiele lasów wielu scenariusza usługi Azure AD**

Zalecane w celu zapewnienia jednego katalogu usługi Azure AD dla organizacji, ale możliwe jest ona relację 1:1 jest przechowywana między serwerem synchronizacji Azure AD Connect i katalog usługi Azure AD.  Dla każdego wystąpienia usługi Azure AD należy instalacji programu Azure AD Connect.  Ponadto usługi Azure AD, zgodnie z projektem jest izolowana i użytkownicy w jednym wystąpieniu usługi Azure AD nie będą mogli wyświetlić użytkowników w innym wystąpieniu.

Jest to możliwe i obsługiwanych nawiązać lokalne wystąpienie usługi Active Directory wiele katalogów usługi Azure AD, jak pokazano na poniższej ilustracji:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 

**Scenariusz filtrowania pojedynczego lasu**

W tym celu następujące muszą być spełnione:

* Serwery synchronizacji usługi Azure AD Connect musi być skonfigurowana do filtrowania, każdy z nich ma wykluczają się wzajemnie zestaw obiektów.  To zrobić, na przykład przez zakresu każdego serwera do określonej domeny lub jednostki Organizacyjnej.
* Domeny DNS mogą być rejestrowane tylko w jednym katalogu usługi Azure AD więc nazwy UPN użytkowników w lokalnej AD należy użyć oddzielnych obszarów nazw
* Użytkownicy w jednym wystąpieniu usługi Azure AD tylko będą mogli wyświetlić użytkowników z ich wystąpienia.  Nie będzie mógł wyświetlić użytkowników w innych przypadkach
* Tylko jeden z katalogów usługi Azure AD można włączyć hybrydowym programu Exchange z lokalnej usługi AD
* Wzajemne wyłączności ma również zastosowanie do zapisu.  Dzięki temu niektórych funkcji zapisywania zwrotnego nie są obsługiwane z tej topologii, ponieważ te założono konfiguracji pojedynczym lokalnym.  Obejmuje to:
  * Grupuj zapisu z konfiguracji domyślnej
  * Zapisywanie zwrotne urządzeń

Następujące nie jest obsługiwane i nie powinna być wybrana jako implementacji:

* Wiele serwerów synchronizacji Azure AD Connect, połączenie z tym samym katalogu usługi Azure AD nawet, jeśli zostały skonfigurowane do synchronizacji wykluczają się wzajemnie zbiór obiektu nie jest obsługiwane
* Go nie jest obsługiwana na synchronizowanie tego samego użytkownika do wielu katalogów usługi Azure AD. 
* Nie jest też obsługiwana na Konfiguracja Aby udostępnić użytkownikom w usłudze Azure AD są wyświetlane jako kontakty w innym katalogu usługi Azure AD. 
* Jest również nieobsługiwany można zmodyfikować synchronizacji Azure AD Connect do nawiązania połączenia wielu katalogów usługi Azure AD.
* Katalogów usługi Azure AD są zgodne z założeniami samodzielnie. Jest nieobsługiwany w celu zmiany konfiguracji synchronizacji Azure AD Connect można odczytać danych z innego katalogu usługi Azure AD w celu kompilacji typowe i ujednoliconego usługi GAL między katalogami. Nie jest również obsługiwana eksportuje użytkowników jako kontakty do innego lokalnego AD za pomocą synchronizacji usługi Azure AD Connect.

> [!NOTE]
> Jeśli Twoja organizacja ogranicza komputerów w sieci z połączeniem z Internetem, w tym artykule wymieniono punkty końcowe (nazw FQDN, IPv4 i zakresy adresów IPv6) należy uwzględnić w Twojej wychodzącego Zezwalaj listy i Internet Explorer Zaufane witryny klienta komputerów upewnić się, komputery pomyślnie można korzystać z usługi Office 365. Aby uzyskać więcej informacji, przeczytaj [zakresów adresów IP i URL usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiowanie strategii uwierzytelniania wieloskładnikowego
W tym zadaniu zostanie Definiowanie strategii uwierzytelniania wieloskładnikowego do użycia.  Uwierzytelnianie wieloskładnikowe platformy Azure jest dostarczany w dwóch różnych wersjach.  Jeden jest oparty na chmurze, a drugi to lokalne za pomocą serwera usługi Azure MFA.  Na podstawie oceny powyżej, które można określić, które rozwiązanie jest poprawne dla strategii.  Aby określić, która opcja Projekt najlepiej spełnia wymagania dotyczące zabezpieczeń w firmie, należy użyć w poniższej tabeli:

Opcje projektu wieloskładnikowego:

| Zasób, aby zabezpieczyć | Usługa MFA w chmurze | Lokalna usługa MFA |
| --- | --- | --- |
| Aplikacje firmy Microsoft |tak |tak |
| Aplikacje SaaS w galerii aplikacji |tak |tak |
| Aplikacje usług IIS opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD |tak |tak |
| Aplikacji programu IIS nie jest opublikowana przy użyciu serwera Proxy aplikacji usługi Azure AD |nie |tak |
| Dostępu zdalnego sieci VPN, Brama usług pulpitu zdalnego |nie |tak |

Nawet jeśli użytkownik może mieć rozliczenia na rozwiązanie dla strategii, nadal konieczne Użyj oceny z powyższych, na którym znajdują się użytkownicy.  Może to spowodować rozwiązania zmienić.  Użyj poniższej tabeli, aby ułatwić określenie to:

| Lokalizacja użytkownika | Opcja preferowane rozwiązanie |
| --- | --- |
| Usługa Azure Active Directory |Multi-FactorAuthentication w chmurze |
| Usługa Azure AD i lokalna usługa AD przy użyciu federacji z usługami AD FS |Oba |
| Usługi Azure AD i lokalnymi AD za pomocą usługi Azure AD Connect nie synchronizacji haseł |Oba |
| Usługi Azure AD i lokalnymi za pomocą usługi Azure AD Connect z synchronizacją haseł |Oba |
| Lokalne usługi AD |Serwer Multi-Factor Authentication |

> [!NOTE]
> Ponadto upewnij się, że wybrany opcji Projekt usługi Multi-Factor authentication obsługuje funkcje, które są wymagane dla projektu.  Aby uzyskać więcej informacji, przeczytaj [wybierz rozwiązanie zabezpieczeń wieloskładnikowego](../multi-factor-authentication/multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Dostawca uwierzytelniania MFA
Usługa Multi-Factor authentication jest dostępna domyślnie dla administratorów globalnych dzierżawiących usługę Azure Active Directory. Jednak jeśli chcesz rozszerzyć uwierzytelnianie wieloskładnikowe na wszystkich użytkowników i/lub umożliwia administratorom globalne do wykonania funkcji korzyści, takich jak portalu zarządzania, niestandardowe pozdrowienia i raporty, następnie musisz kupić i Konfiguruj dostawcę usługi Multi-Factor Authentication.

> [!NOTE]
> Ponadto upewnij się, że wybrany opcji Projekt usługi Multi-Factor authentication obsługuje funkcje, które są wymagane dla projektu. 
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określenie wymagań dotyczących ochrony danych](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

