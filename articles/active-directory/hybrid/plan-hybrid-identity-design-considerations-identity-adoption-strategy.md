---
title: Hybrydowy projekt tożsamości — strategia wdrażania Platformy Azure | Dokumenty firmy Microsoft
description: Dzięki kontroli dostępu warunkowego usługa Azure Active Directory sprawdza określone warunki, które można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji. Po spełnieniu tych warunków użytkownik jest uwierzytelniony i ma dostęp do aplikacji.
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
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109330"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiowanie strategii wdrażania tożsamości hybrydowej
W tym zadaniu można zdefiniować strategię wdrażania tożsamości hybrydowej dla rozwiązania tożsamości hybrydowej, aby spełnić wymagania biznesowe, które zostały omówione w:

* [Określanie potrzeb biznesowych](plan-hybrid-identity-design-considerations-business-needs.md)
* [Określanie wymagań dotyczących synchronizacji katalogów](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiowanie strategii potrzeb biznesowych
Pierwsze zadanie dotyczy określania potrzeb biznesowych organizacji.  Może to być bardzo szeroki i pełzanie zakresu może wystąpić, jeśli nie są ostrożni.  Na początku, zachować to proste, ale zawsze pamiętaj, aby zaplanować projekt, który będzie pomieścić i ułatwić zmiany w przyszłości.  Niezależnie od tego, czy jest to prosty projekt, czy niezwykle złożony, usługa Azure Active Directory to platforma Microsoft Identity obsługująca usługę Office 365, usługi Microsoft Online Services i aplikacje obsługujące chmurę.

## <a name="define-an-integration-strategy"></a>Definiowanie strategii integracji
Firma Microsoft ma trzy główne scenariusze integracji, które są tożsamościami w chmurze, zsynchronizowanymi tożsamościami i tożsamościami federatywnymi.  Należy zaplanować przyjęcie jednej z tych strategii integracji.  Wybrana strategia może się różnić, a decyzje przy wyborze jednego mogą obejmować, jakiego rodzaju środowisko użytkownika chcesz zapewnić, czy masz istniejącą infrastrukturę i co jest najbardziej opłacalne.  

![scenariusze integracji](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scenariusze zdefiniowane na powyższej ilustracji są następujące:

* **Tożsamości w chmurze:** są to tożsamości, które istnieją wyłącznie w chmurze.  W przypadku usługi Azure AD będą one znajdują się w szczególności w katalogu usługi Azure AD.
* **Zsynchronizowane:** są to tożsamości, które istnieją lokalnie i w chmurze.  Korzystając z usługi Azure AD Connect, ci użytkownicy są tworzone lub łączone z istniejącymi kontami usługi Azure AD.  Hash hasła użytkownika jest synchronizowany ze środowiska lokalnego do chmury w tak zwanym hash hasło.  Podczas korzystania z synchronizowanych jedno zastrzeżenie jest, że jeśli użytkownik jest wyłączony w środowisku lokalnym, może potrwać do trzech godzin dla tego stanu konta, aby wyświetlić w usłudze Azure AD.  Jest to spowodowane interwałem czasu synchronizacji.
* **Federacyjne:** te tożsamości istnieją zarówno lokalnie, jak i w chmurze.  Korzystając z usługi Azure AD Connect, ci użytkownicy są tworzone lub łączone z istniejącymi kontami usługi Azure AD.  

> [!NOTE]
> Aby uzyskać więcej informacji na temat opcji synchronizacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Poniższa tabela pomaga w określeniu zalet i wad każdej z następujących strategii:

| Strategia | Zalety | Wady |
| --- | --- | --- |
| **Tożsamości w chmurze** |Łatwiejsze zarządzanie dla małej organizacji. <br> Nic do zainstalowania lokalnie. Nie jest potrzebny dodatkowy sprzęt<br>Łatwo wyłączyć, jeśli użytkownik opuszcza firmę |Użytkownicy będą musieli zalogować się podczas uzyskiwania dostępu do obciążeń w chmurze <br> Hasła mogą, ale nie muszą być takie same dla tożsamości w chmurze i lokalnej |
| **Synchronizowane** |Hasło lokalne uwierzytelnia katalogi lokalne i w chmurze <br>Łatwiejsze zarządzanie dla małych, średnich i dużych organizacji <br>Użytkownicy mogą mieć logowanie jednokrotne (Logowanie jednokrotne) dla niektórych zasobów <br> Preferowana metoda synchronizacji firmy Microsoft <br> Łatwiejsze zarządzanie |Niektórzy klienci mogą być niechętni do synchronizowania swoich katalogów z chmurą ze względu na policję konkretnej firmy |
| **Federacyjni** |Użytkownicy mogą mieć logowanie jednokrotne (Logowanie jednokrotne) <br>Jeśli użytkownik zostanie rozwiązany lub opuści, konto może zostać natychmiast wyłączone i odwołano dostęp,<br> Obsługuje zaawansowane scenariusze, których nie można wykonać za pomocą synchronizacji |Więcej czynności, które należy skonfigurować i skonfigurować <br> Wyższa konserwacja <br> Może wymagać dodatkowego sprzętu dla infrastruktury STS <br> Może wymagać dodatkowego sprzętu do zainstalowania serwera federacyjnego. Dodatkowe oprogramowanie jest wymagane, jeśli używane są usługi AD FS <br> Wymagaj rozbudowanej konfiguracji dla sytego systemu sytego <br> Krytyczny punkt awarii, jeśli serwer federacyjny jest wyłączyny, użytkownicy nie będą mogli uwierzytelniać się |

### <a name="client-experience"></a>Środowisko pracy klienta
Używana strategia będzie dyktować środowisko logowania użytkownika.  W poniższych tabelach znajdują się informacje o tym, czego użytkownicy powinni oczekiwać od ich logowania.  Nie wszyscy dostawcy tożsamości federacyjnej obsługują wiązki sycowe we wszystkich scenariuszach.

**Aplikacje sieci dołączona do Domana i sieci prywatnej:**

|  | Zsynchronizowana tożsamość | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki internetowe |Uwierzytelnianie oparte na formularzach |logowanie jednokrotne, czasami wymagane do podania identyfikatora organizacji |
| Outlook |Monituj o poświadczenia |Monituj o poświadczenia |
| Skype dla firm (Lync) |Monituj o poświadczenia |logowanie jednokrotne dla programu Lync, monitowanie o poświadczenia dla programu Exchange |
| OneDrive dla Firm |Monituj o poświadczenia |logowanie jednokrotne |
| Subskrypcja pakietu Office Pro Plus |Monituj o poświadczenia |logowanie jednokrotne |

**Źródła zewnętrzne lub niezaufane:**

|  | Zsynchronizowana tożsamość | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki internetowe |Uwierzytelnianie oparte na formularzach |Uwierzytelnianie oparte na formularzach |
| Outlook, Skype dla firm (Lync), OneDrive dla Firm, Subskrypcja pakietu Office |Monituj o poświadczenia |Monituj o poświadczenia |
| Exchange ActiveSync |Monituj o poświadczenia |logowanie jednokrotne dla programu Lync, monitowanie o poświadczenia dla programu Exchange |
| Aplikacji mobilnych |Monituj o poświadczenia |Monituj o poświadczenia |

Jeśli na podstawie zadania 1 ustalono, że masz identyfikator innej firmy lub zamierzasz użyć jednego z nich w celu zapewnienia federacji za pomocą usługi Azure AD, musisz pamiętać o następujących obsługiwanych możliwościach:

* Każdy dostawca SAML 2.0 zgodny z profilem SP-Lite może obsługiwać uwierzytelnianie w usłudze Azure AD i skojarzonych aplikacjach
* Obsługuje uwierzytelnianie pasywne, co ułatwia uwierzytelnianie do OWA, SPO itp.
* Klienci usługi Exchange Online mogą być obsługiwani za pośrednictwem rozszerzonego profilu klienta SAML 2.0 (ECP)

Należy również pamiętać, jakie możliwości nie będą dostępne:

* Bez obsługi ws-trust/federation wszyscy inni aktywni klienci
  * Oznacza to, że przed pakietem Office 2016 żaden klient programu Lync, klient usługi OneDrive, subskrypcja pakietu Office, pakiet Office Mobile
* Przejście pakietu Office do uwierzytelniania pasywnego pozwala im obsługiwać czyste identyfikatory SAML 2.0, ale obsługa nadal będzie obsługiwana przez klienta

> [!NOTE]
> Aby uzyskać najbardziej zaktualizowaną listę, przeczytaj artykuł [Lista zgodności federacji usługi Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definiowanie strategii synchronizacji
W tym zadaniu zdefiniujesz narzędzia, które będą używane do synchronizowania danych lokalnych organizacji z chmurą i jakiej topologii należy użyć.  Ponieważ większość organizacji korzysta z usługi Active Directory, informacje dotyczące korzystania z usługi Azure AD Connect w celu rozwiązania powyższych pytań są podane w niektórych szczegółach.  W przypadku środowisk, które nie mają usługi Active Directory, istnieją informacje dotyczące korzystania z programu FIM 2010 R2 lub MIM 2016 w celu zaplanowania tej strategii.  Jednak przyszłe wersje usługi Azure AD Connect będą obsługiwać katalogi LDAP, więc w zależności od osi czasu te informacje mogą być w stanie pomóc.

### <a name="synchronization-tools"></a>Narzędzia synchronizacji
Przez lata istniało i było kilka narzędzi synchronizacji w różnych scenariuszach.  Obecnie usługa Azure AD Connect jest przejdź do wybranego narzędzia dla wszystkich obsługiwanych scenariuszy.  AAD Sync i DirSync są nadal wokół i może nawet być obecny w środowisku teraz. 

> [!NOTE]
> Aby uzyskać najnowsze informacje dotyczące obsługiwanych możliwości każdego narzędzia, przeczytaj artykuł [porównawczy narzędzi integracji katalogów.](plan-hybrid-identity-design-considerations-tools-comparison.md)  
> 
> 

### <a name="supported-topologies"></a>Obsługiwane topologie
Podczas definiowania strategii synchronizacji należy określić topologię, która jest używana. W zależności od informacji, które zostały określone w kroku 2 można określić, która topologia jest właściwa do użycia. Pojedynczy las, pojedyncza topologia usługi Azure AD jest najczęściej spotykana i składa się z jednego lasu usługi Active Directory i pojedynczego wystąpienia usługi Azure AD.  Będzie to używane w większości scenariuszy i jest oczekiwaną topologią podczas korzystania z instalacji usługi Azure AD Connect Express, jak pokazano na poniższym rysunku.

![Obsługiwane topologie](./media/plan-hybrid-identity-design-considerations/single-forest.png) Pojedynczy scenariusz leśny Jest to typowe dla dużych, a nawet małych organizacji, które mają wiele lasów, jak pokazano na rysunku 5.

> [!NOTE]
> Aby uzyskać więcej informacji na temat różnych lokalnych i topologii usługi Azure AD z synchronizacją usługi Azure AD Connect, przeczytaj artykuł [Topologie dla usługi Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologia wielolesowa](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenariusz wielolesowy

W takim przypadku należy wziąć pod uwagę wielolesową topologię usługi Azure AD, jeśli spełnione są następujące elementy:

* Użytkownicy mają tylko 1 tożsamość we wszystkich lasach — w sekcji użytkownicy identyfikujący się z unikatową identyfikacją poniżej opisano to bardziej szczegółowo.
* Użytkownik uwierzytelnia się w lesie, w którym znajduje się jego tożsamość
* UPN i Source Anchor (niezmienny identyfikator) będą pochodzić z tego lasu
* Wszystkie lasy są dostępne przez usługę Azure AD Connect — oznacza to, że nie musi być przyłączony do domeny i mogą być umieszczane w strefie DMZ, jeśli to ułatwia.
* Użytkownicy mają tylko jedną skrzynkę pocztową
* Las, w którym znajduje się skrzynka pocztowa użytkownika, ma najlepszą jakość danych dla atrybutów widocznych na globalnej liście adresów programu Exchange (GAL)
* Jeśli użytkownik nie ma skrzynki pocztowej, każdy las może zostać użyty do współtworzenia tych wartości
* Jeśli masz połączonybox pocztowy, istnieje również inne konto w innym lesie używanym do logowania.

> [!NOTE]
> Obiekty, które istnieją zarówno lokalnie, jak i w chmurze są "połączone" za pomocą unikatowego identyfikatora. W kontekście synchronizacji katalogów ten unikatowy identyfikator jest określany jako SourceAnchor. W kontekście logowania jednokrotnego jest to określane jako ImmutableId. [Pojęcia dotyczące projektu usługi Azure AD Connect w](plan-connect-design-concepts.md#sourceanchor) celu uzyskania więcej zagadnień dotyczących korzystania z funkcji SourceAnchor.
> 
> 

Jeśli powyższe nie są prawdziwe i masz więcej niż jedno aktywne konto lub więcej niż jedną skrzynkę pocztową, usługa Azure AD Connect wybierze jedną z nich i zignoruje drugą.  Jeśli masz połączone skrzynki pocztowe, ale nie inne konto, te konta nie zostaną wyeksportowane do usługi Azure AD i że użytkownik nie będzie członkiem żadnej grupy.  To różni się od tego, jak to było w przeszłości z DirSync i jest zamierzone, aby lepiej obsługiwać te scenariusze wielu lasów. Scenariusz wielolesowy jest pokazany na poniższym rysunku.

![wielu dzierżaw usługi Azure AD](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Wielolesowy scenariusz usługi Azure AD dla wielu lasów**

Zaleca się, aby mieć tylko jeden katalog w usłudze Azure AD dla organizacji, ale jest obsługiwany to relacja 1:1 jest zachowywana między serwerem synchronizacji usługi Azure AD Connect i katalogu usługi Azure AD.  Dla każdego wystąpienia usługi Azure AD potrzebujesz instalacji usługi Azure AD Connect.  Ponadto usługa Azure AD, według projektu jest izolowana, a użytkownicy w jednym wystąpieniu usługi Azure AD nie będą mogli zobaczyć użytkowników w innym wystąpieniu.

Jest możliwe i obsługiwane, aby połączyć jedno lokalne wystąpienie usługi Active Directory z wieloma katalogami usługi Azure AD, jak pokazano na poniższym rysunku:

![filtrowanie pojedynczego lasu](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Scenariusz filtrowania pojedynczego lasu**

Aby to zrobić, muszą być spełnione następujące warunki:

* Serwery synchronizacji usługi Azure AD Connect muszą być skonfigurowane do filtrowania, tak aby każdy z nich miał wzajemnie wykluczający się zestaw obiektów.  Odbywa się to na przykład przez określanie zakresu każdego serwera do określonej domeny lub ou.
* Domena DNS może być zarejestrowana tylko w jednym katalogu usługi Azure AD, więc sieci UPN użytkowników w lokalnej usłudze AD muszą używać oddzielnych obszarów nazw
* Użytkownicy w jednym wystąpieniu usługi Azure AD będą mogli zobaczyć tylko użytkowników z ich wystąpienia.  Nie będą mogli zobaczyć użytkowników w innych przypadkach
* Tylko jeden katalog usługi Azure AD może włączyć hybrydową usługę Exchange z lokalną usługą AD
* Wzajemna wyłączność odnosi się również do odpisu.  Powoduje to, że niektóre funkcje zapisu nie są obsługiwane w tej topologii, ponieważ zakładają one jedną konfigurację lokalną.  Obejmuje to:
  * Grupowanie odpisu z konfiguracją domyślną
  * Odpisy urządzenia

Następujące nie są obsługiwane i nie powinny być wybierane jako implementacja:

* Nie jest obsługiwane łączenie wielu serwerów synchronizacji usługi Azure AD Connect z tym samym katalogiem usługi Azure AD, nawet jeśli są one skonfigurowane do synchronizowania wzajemnie wykluczanego zestawu obiektów
* Nie jest obsługiwane do synchronizacji tego samego użytkownika do wielu katalogów usługi Azure AD. 
* Nie można również wykonać zmiany konfiguracji, aby użytkownicy w jednej usłudze Azure AD pojawiali się jako kontakty w innym katalogu usługi Azure AD. 
* Nie można również zmodyfikować synchronizacji usługi Azure AD Connect w celu nawiązania połączenia z wieloma katalogami usługi Azure AD.
* Katalogi usługi Azure AD są według projektu izolowane. Nie jest obsługiwane, aby zmienić konfigurację synchronizacji usługi Azure AD Connect do odczytu danych z innego katalogu usługi Azure AD w celu utworzenia wspólnej i ujednoliconej gal między katalogami. Jest również nieobsługiwał się do eksportowania użytkowników jako kontaktów do innej lokalnej usługi AD przy użyciu synchronizacji usługi Azure AD Connect.

> [!NOTE]
> Jeśli organizacja ogranicza komputerom w sieci możliwość łączenia się z Internetem, w tym artykule wymieniono punkty końcowe (nazwy FQDN, IPv4 i zakresy adresów IPv6), które należy uwzględnić na listach dozwolonych dla ruchu wychodzącego i strefie zaufanych witryn programu Internet Explorer klienta komputerach, aby upewnić się, że komputery mogą pomyślnie korzystać z usługi Office 365. Aby uzyskać więcej informacji, zobacz [adresy URL usługi Office 365 i zakresy adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiowanie strategii uwierzytelniania wieloskładnikowego
W tym zadaniu zostanie zdefiniowana strategia uwierzytelniania wieloskładnikowego do użycia.  Uwierzytelnianie wieloskładnikowe platformy Azure jest dostępne w dwóch różnych wersjach.  Jeden jest oparty na chmurze, a drugi jest oparty lokalnie przy użyciu serwera usługi Azure MFA Server.  Na podstawie oceny, którą zrobiłeś powyżej, możesz określić, które rozwiązanie jest poprawne dla twojej strategii.  Skorzystaj z poniższej tabeli, aby określić, która opcja projektu najlepiej spełnia wymagania firmy dotyczące zabezpieczeń:

Opcje projektowania wieloczynnikowego:

| Zasób do zabezpieczenia | Usługa MFA w chmurze | Lokalna usługa MFA |
| --- | --- | --- |
| Aplikacje firmy Microsoft |tak |tak |
| Aplikacje SaaS w galerii aplikacji |tak |tak |
| Aplikacje usług IIS opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD |tak |tak |
| Aplikacje usług IIS nie są publikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD |nie |tak |
| Dostęp zdalny jako VPN, RDG |nie |tak |

Mimo że być może zdecydowałeś się na rozwiązanie dla swojej strategii, nadal musisz użyć oceny z góry, gdzie znajdują się użytkownicy.  Może to spowodować zmianę rozwiązania.  Skorzystaj z poniższej tabeli, aby pomóc Ci to ustalić:

| Lokalizacja użytkownika | Preferowana opcja projektowania |
| --- | --- |
| Usługa Azure Active Directory |Wieloskładnikoweuthentication w chmurze |
| Usługa Azure AD i lokalna usługa AD przy użyciu federacji z usługami AD FS |Obie |
| Usługa Azure AD i lokalna usługa AD przy użyciu usługi Azure AD Connect bez synchronizacji haseł |Obie |
| Usługa Azure AD i lokalnie korzystające z usługi Azure AD Connect z synchronizacją haseł |Obie |
| Lokalna ad |Serwer Multi-Factor Authentication |

> [!NOTE]
> Należy również upewnić się, że wybrana opcja projektowania uwierzytelniania wieloskładnikowego obsługuje funkcje wymagane dla projektu.  Aby uzyskać więcej informacji, przeczytaj [wybierz wieloczynnikowe rozwiązanie zabezpieczające.](../authentication/concept-mfa-howitworks.md)
> 

## <a name="multi-factor-auth-provider"></a>Dostawca auth wieloczynnikowych
Uwierzytelnianie wieloskładnikowe jest domyślnie dostępne dla administratorów globalnych, którzy mają dzierżawę usługi Azure Active Directory. Jeśli jednak chcesz rozszerzyć uwierzytelnianie wieloskładnikowe na wszystkich użytkowników i/lub chcesz, aby administratorzy globalni mogli korzystać z funkcji, takich jak portal zarządzania, niestandardowe pozdrowienia i raporty, musisz zakupić i skonfigurować dostawcę uwierzytelniania wieloskładnikowego.

> [!NOTE]
> Należy również upewnić się, że wybrana opcja projektowania uwierzytelniania wieloskładnikowego obsługuje funkcje wymagane dla projektu. 
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymogów ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

