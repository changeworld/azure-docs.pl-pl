---
title: Jak i dlaczego aplikacje są dodawane do usługi Azure Active Directory
description: Co to oznacza dla aplikacji, które mają zostać dodane do usługi Azure AD i jak one dostać się tam?
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/04/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: b784cafce08634f1026a908e8ccdaaed41b62a42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111612"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak i dlaczego aplikacje są dodawane do usługi Azure AD

Istnieją dwa oświadczenia aplikacji w usłudze Azure AD: 
* [Obiekty aplikacji](app-objects-and-service-principals.md#application-object) — mimo że istnieją [wyjątki](#notes-and-exceptions), obiekty aplikacji mogą zostać uwzględnione w definicji aplikacji.
* [Jednostki usług](app-objects-and-service-principals.md#service-principal-object) — jest uznawana za wystąpienie aplikacji. Nazwy główne usług ogólnie odwołują się do obiektu aplikacji, a jeden obiekt aplikacji mogą być przywoływane przez wielu jednostek usługi w różnych katalogach.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Jakie są obiekty aplikacji i gdzie pochodzą z?
Możesz zarządzać [obiekty aplikacji](app-objects-and-service-principals.md#application-object) w witrynie Azure portal, za pośrednictwem [rejestracje aplikacji](https://aka.ms/appregistrations) środowiska. Obiekty aplikacji opisują aplikacji do usługi Azure AD i jest uznawana za definicję aplikacji, dzięki czemu usługa pozwalający na wystawianie tokenów dla aplikacji na podstawie jej ustawień. Obiekt aplikacji będzie istniało tylko w jego katalogu macierzystego, nawet jeśli jest on aplikacji z wieloma dzierżawami, Obsługa jednostki usługi w innych katalogach. Obiekt aplikacji może zawierać znaków poniżej (niewymienione tutaj również dodatkowych informacji):
* Nazwy, logo i wydawcy
* Identyfikatory URI przekierowania
* Wpisy tajne (symetrycznego i/lub asymetrycznego klucze używane do uwierzytelniania w aplikacji)
* Interfejs API zależności (OAuth)
* Opublikowane interfejsy API/zasobów/zakresy (OAuth)
* Role aplikacji (RBAC)
* Metadanych logowania jednokrotnego i konfiguracji
* Inicjowanie obsługi administracyjnej metadanych i konfiguracji użytkownika
* Metadane serwera proxy i konfiguracji

Obiekty aplikacji mogą być tworzone za pomocą wielu ścieżek, w tym:
* Rejestracje aplikacji w witrynie Azure portal
* Tworzenie nowej aplikacji przy użyciu programu Visual Studio i konfigurując go do korzystania z uwierzytelniania usługi Azure AD
* Gdy administrator dodaje aplikację z galerii aplikacji (co spowoduje również utworzyć jednostkę usługi)
* Za pomocą interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph lub programu PowerShell, aby utworzyć nową aplikację
* Wiele innych, łącznie z różnych uruchomień dla deweloperów na platformie Azure i w Eksplorator interfejsu API napotka w centrach deweloperów

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co to są jednostki usługi i gdzie pochodzą z?
Możesz zarządzać [jednostki usług](app-objects-and-service-principals.md#service-principal-object) w witrynie Azure portal, za pośrednictwem [aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) środowiska. Nazwy główne usług są, co określają sposób nawiązywania połączenia z usługi Azure AD aplikacji i jest uznawana za wystąpienie aplikacji w Twoim katalogu. Dla każdej danej aplikacji może mieć co najwyżej jeden obiekt aplikacji, (który jest zarejestrowany w katalogu "domowy") i jeden lub więcej obiektów nazw głównych usług reprezentujący wystąpień aplikacji w każdym katalogu, w którym działa. 

Nazwa główna usługi mogą obejmować:

* Odwołanie do obiektu aplikacji za pomocą właściwości Identyfikatora aplikacji
* Rekordy lokalnych użytkowników i przypisań roli aplikacji do grup
* Rekordy lokalnych użytkowników i administratorów uprawnienia nadane aplikacji
  * Na przykład: uprawnienia do aplikacji, dostęp do poczty e-mail danego użytkownika
* Rekordy zasad lokalnych, w tym zasady dostępu warunkowego
* Rekordy alternatywne ustawienia lokalne dla aplikacji
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (Inicjowanie obsługi użytkowników)
  * Role aplikacji specyficzne dla katalogu (Jeśli aplikacja obsługuje niestandardowe role)
  * Nazwa określonego katalogu lub logo

Podobnie jak obiekty aplikacji jednostki usługi mogą być tworzone za pomocą wielu ścieżek, w tym:

* Gdy użytkownicy logują się do aplikacji innych firm zintegrowane z usługą Azure AD
  * Podczas logowania użytkownicy zostaną poproszeni o przyznać uprawnienia do aplikacji w celu dostępu do swojego profilu i inne uprawnienia. Pierwszą osobą, aby wyrazić zgodę powoduje, że nazwy głównej usługi, która reprezentuje aplikacji, które mają zostać dodane do katalogu.
* Gdy użytkownicy logują się do usług online firmy Microsoft, takich jak [usługi Office 365](https://products.office.com/)
  * Po zasubskrybowaniu usługi Office 365 lub Rozpocznij okres próbny nazwy główne usług są tworzone w katalogu reprezentująca różne usługi, które są używane do dostarczania, wszystkie funkcje związane z usługą Office 365.
  * Niektóre usługi Office 365, takich jak SharePoint tworzenie jednostek usługi w sposób ciągły, aby umożliwić bezpieczną komunikację między składnikami, włącznie z przepływami pracy.
* Gdy administrator dodaje aplikację z galerii aplikacji (to spowoduje również utworzenie podstawowego obiektu aplikacji)
* Dodawanie aplikacji do użycia [serwera Proxy aplikacji usługi Azure AD](/azure/active-directory/manage-apps/application-proxy)
* Łączenie aplikacji do rejestracji jednokrotnej na temat korzystania z języka SAML lub hasło logowania jednokrotnego (SSO)
* Programowo za pośrednictwem usługi Azure AD interfejsu API programu Graph lub programu PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Jak są obiekty aplikacji i nazwy główne usług ze sobą powiązane?
Aplikacja ma jeden obiekt aplikacji w jego katalogu macierzystego, który odwołuje się do niego co najmniej jeden nazw głównych usług we wszystkich katalogów, w której działa (w tym katalogu głównym aplikacji).
![Diagram pokazujący, jak obiekty aplikacji i nazwy główne usług oddziałują ze sobą oraz wystąpień usługi Azure AD.][apps_service_principals_directory]

Na powyższym diagramie, firma Microsoft udostępnia dwa katalogi wewnętrznie (wyświetlane po lewej stronie) używa do publikowania aplikacji:

* Jeden dla Microsoft Apps (katalog usług firmy Microsoft)
* Jeden dla wstępnie zintegrowanych aplikacji innych firm (katalog galerii aplikacji)

Aplikacja wydawców/dostawców, którzy integracji z usługą Azure AD muszą mieć Publikowanie katalogu (wyświetlane po prawej stronie jako "Niektóre SaaS katalog").

Aplikacje, które możesz dodać siebie (reprezentowana jako **aplikacji (Twojej)** na diagramie) obejmują:

* Aplikacje, które opracowany (zintegrowane z usługą Azure AD)
* Aplikacje połączone dla logowanie jednokrotne
* Aplikacje są opublikowanych przy użyciu serwera proxy aplikacji usługi Azure AD

### <a name="notes-and-exceptions"></a>Uwagi i wyjątki
* Nie wszystkie jednostki usługi wskazywać z powrotem do obiektu aplikacji. Podczas tworzenia usługi Azure AD został pierwotnie usług udostępnianych aplikacjom były bardziej ograniczone i nazwy głównej usługi była wystarczająca do ustalenia tożsamości aplikacji. Oryginalna nazwa główna usługi została w kształcie bliżej konta usługi Windows Server Active Directory. Z tego powodu jest nadal możliwe utworzyć jednostki usługi przy użyciu różnych ścieżek, takich jak przy użyciu programu Azure AD PowerShell bez tworzenia obiektu aplikacji. Interfejs API programu Graph usługi Azure AD wymaga obiektu aplikacji przed tworzenia jednostki usługi.
* Obecnie jest nie wszystkie informacje opisane powyżej udostępniany programowo. Poniżej są dostępne tylko w interfejsie użytkownika:
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (Inicjowanie obsługi użytkowników)
* Aby uzyskać szczegółowe informacje dotyczące nazwy głównej usługi i obiekty aplikacji Zobacz Dokumentacja referencyjna interfejsu API REST usługi Azure AD Graph:
  * [Aplikacja](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Nazwa główna usługi](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Dlaczego aplikacje są zintegrować z usługą Azure AD?
Aplikacje są dodawane do usługi Azure AD, korzystanie z co najmniej jedną z usług oferowanych przez nią w tym:

* Aplikacja uwierzytelniania i autoryzacji
* Uwierzytelniania i autoryzacji użytkowników
* Logowanie Jednokrotne przy użyciu Federacji lub haseł
* Inicjowanie obsługi użytkowników i synchronizacja
* Kontrola dostępu oparta na rolach — używany katalog do definiowania ról aplikacji do wykonywania autoryzacji opartej na rolach sprawdza, czy w aplikacji
* Usługi autoryzacji OAuth — używane przez usługi Office 365 i innych aplikacji firmy Microsoft do autoryzowania dostępu do interfejsów API/zasobów
* Publikowanie aplikacji i serwera proxy — publikowanie aplikacji z sieci prywatnej do Internetu

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kto ma uprawnienia do dodawania aplikacji do mojego wystąpienia usługi Azure AD?
Dostępnych jest kilka zadań, że tylko administratorzy globalni mogą wykonywać (takie jak dodawanie aplikacji z galerii aplikacji i konfigurowanie aplikacji do korzystania z serwerem Proxy aplikacji) domyślnie wszyscy użytkownicy w katalogu mieć uprawnienia do rejestrowania aplikacji obiekty, które są one opracowywanie i uznania za pośrednictwem aplikacji, które one udziału/zapewniają dostęp do swoich danych organizacji za zgodą. Jeśli osoba jest pierwszy użytkownik w Twoim katalogu do logowania do aplikacji i wyrazić zgody, spowoduje to utworzenie nazwy głównej usługi w dzierżawie; w przeciwnym razie informacje grant zgody będą przechowywane na istniejącej nazwy głównej usługi.

Zezwolenie użytkownikom na rejestrowanie i wyrazić zgodę na aplikacje może początkowo dźwięku dotyczące, ale pamiętać o następujących:


* Aplikacje są w stanie korzystać z usługi Active Directory systemu Windows Server do uwierzytelniania użytkowników przez wiele lat bez konieczności aplikacji do zarejestrowania lub zapisywane w katalogu. Teraz organizacji zostanie ulepszona widoczność, aby dokładnie tak jak wiele aplikacji korzystają z katalogu i w jakim celu.
* Delegowanie obowiązków użytkownikom neguje zapotrzebowania na rejestrowanie aplikacji opartych na administratora oraz proces publikowania. Za pomocą Active Directory Federation Services (ADFS) była prawdopodobnie administrator musiały dodać aplikację jako jednostki uzależnionej strona imieniu deweloperów. Teraz deweloperzy mogą samoobsługi.
* Użytkownicy z logowaniem do aplikacji przy użyciu ich kont organizacji do celów biznesowych jest dobrym znakiem. Jeśli później opuszczenia organizacji, które automatycznie spowoduje utratę dostępu do swojego konta w aplikacji, które zostały przy użyciu.
* Masz rekord danych został udostępniony, z którym aplikacja jest dobrym znakiem. Dane są bardziej przenośne niż kiedykolwiek wcześniej i warto mieć rekord wyczyść kto udostępnione dla danych aplikacji, które.
* Właściciele interfejsu API, które używają usługi Azure AD do uwierzytelniania OAuth zdecydować, dokładnie jakie uprawnienia użytkowników są w stanie udzielić aplikacji i uprawnienia, które wymagają administrator, aby zaakceptować. Tylko administratorzy mogą wyrazić zgodę na zakresy większych i bardziej znaczące uprawnienia, podczas gdy zgody użytkownika jest ograniczone do ich danych i możliwości.
* Gdy użytkownik dodaje lub umożliwia aplikacji dostęp do swoich danych, zdarzenia można przeprowadzać inspekcję, aby można było wyświetlić raporty inspekcji w witrynie Azure portal, aby określić, jak aplikacja została dodana do katalogu.

Jeśli nadal chcesz uniemożliwić użytkownikom w katalogu, z rejestracji aplikacji oraz z logowaniem do aplikacji bez zgody administratora, istnieją dwa ustawienia, które można zmienić, aby wyłączyć te możliwości:

* Aby uniemożliwić użytkownikom wyrażanie zgody na aplikacje we własnym imieniu:
  1. W witrynie Azure portal przejdź do [ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) w sekcji aplikacje dla przedsiębiorstw.
  2. Zmiana **użytkownicy mogą zezwalać aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu** do **nie**. 
     
     > [!NOTE]
     > Jeśli zdecydujesz wyłączyć zgody użytkownika, administratora będą musieli wyrazić zgodę na nową aplikację, którą użytkownik musi używać.    
* Aby uniemożliwić użytkownikom rejestrowanie swoich własnych aplikacji:
  1. W witrynie Azure portal przejdź do [ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) sekcji w obszarze Azure Active Directory
  2. Zmiana **użytkownicy mogą rejestrować aplikacje** do **nie**.

> [!NOTE]
> Sama firma Microsoft używa domyślnej konfiguracji z użytkownicy mogli rejestrować aplikacje usługi i wyrazić zgodę na aplikacje we własnym imieniu.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

