---
title: Metody i przyczyny dodawania aplikacji do usługi Azure Active Directory
description: Co to znaczy dla aplikacji do dodania do usługi Azure AD i jak ich uzyskać istnieje?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: c9ebfcba59e3f46fb30f4cd2402ec4ebb606f6d0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156174"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Metody i przyczyny dodawania aplikacji do usługi Azure AD
Istnieją dwa oświadczenia aplikacji w usłudze Azure AD: 
* [Obiekty aplikacji](active-directory-application-objects.md#application-object) — mimo że istnieją [wyjątki](#notes-and-exceptions), te mogą być uważane za definicji aplikacji.
* [Usługa podmiotów](active-directory-application-objects.md#service-principal-object) — te mogą być uważane za wystąpienie aplikacji. Nazwy główne usług zazwyczaj odwołuje się do obiektu aplikacji i jeden obiekt aplikacji może odwoływać się wiele elementów głównych usług między katalogami.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Jakie są obiekty aplikacji i których pochodzą z?
[Obiekty aplikacji](active-directory-application-objects.md#application-object) (którym można zarządzać w portalu Azure za pośrednictwem [rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) wystąpić) opisu aplikacji do usługi Azure AD i mogą zostać uwzględnione definicję aplikacji, dzięki czemu Usługa wiedzieć, jak do wystawiania tokenów aplikacji na podstawie jej ustawień. Obiekt aplikacji będzie istniało tylko w jego katalogu macierzystego, nawet jeśli jest to aplikacja wielodostępne Obsługa nazwy główne usług w innych katalogach. Obiekt aplikacji może zawierać żadnego z następujących (jak opisane tutaj również dodatkowych informacji):
* Nazwa, logo i wydawca
* Adresy URL odpowiedzi
* Klucze tajne (symetrycznego i/lub asymetrycznego klucze służące do uwierzytelniania aplikacji)
* Zależności interfejsu API (OAuth)
* Opublikowane interfejsy API/zasobów/zakresów (OAuth)
* Aplikacja ról (RBAC)
* Metadane logowania jednokrotnego i konfiguracji
* Inicjowanie obsługi administracyjnej metadanych i konfiguracji użytkownika
* Metadane serwera proxy i konfiguracji

Obiekty aplikacji mogą być tworzone za pomocą wielu ścieżek, w tym:
* Rejestracji aplikacji w portalu Azure
* Tworzenie nowej aplikacji przy użyciu programu Visual Studio i skonfigurowania go do uwierzytelniania usługi Azure AD
* Gdy administrator dodaje aplikację z galerii aplikacji (spowoduje to również utworzenie nazwy głównej usługi)
* Przy użyciu interfejsu API programu Microsoft Graph, interfejsu API usługi Azure AD Graph lub programu PowerShell, aby utworzyć nową aplikację
* Wiele innych, łącznie z różnych uruchomień dewelopera na platformie Azure i w Eksploratorze interfejsu API napotyka między centrami developer

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co to są nazwy główne usług, i których pochodzą z?
[Usługi podmiotów zabezpieczeń](active-directory-application-objects.md#service-principal-object) (której można zarządzać za pomocą [aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) wystąpić) są, co faktycznie regulują aplikacji nawiązywania połączenia z usługą Azure AD i mogą być uważane za wystąpienie aplikacji w sieci katalog. Dla każdej danej aplikacji może mieć co najwyżej jeden obiekt aplikacji, (który jest zarejestrowany w katalogu "macierzystego") i co najmniej jeden usługi głównej obiekty reprezentujący wystąpienie aplikacji w każdym katalogu, w którym działa. 

Nazwy głównej usługi mogą obejmować:

* Odwołanie do obiektu aplikacji za pomocą właściwości Identyfikatora aplikacji
* Rekordy użytkowników lokalnych i przypisania roli aplikacji grupy
* Rejestruje lokalnych użytkowników i administratora uprawnienia do aplikacji
  * Na przykład: uprawnienia do aplikacji dostęp do poczty e-mail określonego użytkownika
* Rejestruje zasad lokalnych, włącznie z zasad dostępu warunkowego
* Rejestruje alternatywne ustawienia lokalne dla aplikacji
  * Reguły przekształcania oświadczeń
  * Mapowanie atrybutów (Inicjowanie obsługi użytkowników)
  * Role specyficzne dla katalogu aplikacji (Jeśli aplikacja obsługuje role niestandardowe)
  * Nazwa określonego katalogu lub logo

Podobnie jak obiekty aplikacji nazwy główne usług mogą być tworzone za pomocą wielu ścieżek, w tym:

* Gdy użytkownik zaloguje się do aplikacji innych firm zintegrowane z usługą Azure AD
  * Podczas rejestracji użytkownicy są proszeni o nadać uprawnienia do aplikacji w celu dostępu do swojego profilu i inne uprawnienia. Pierwszą osobą zgody powoduje, że nazwy głównej usługi, który reprezentuje aplikacji, które mają zostać dodane do katalogu.
* Gdy użytkownik zaloguje się do usługi online firmy Microsoft, takich jak [usługi Office 365](http://products.office.com/)
  * Po zasubskrybowaniu usługi Office 365 lub rozpocząć wersji próbnej, nazwy główne usług są tworzone w katalogu reprezentujący różnych usług, które są używane do dostarczania wszystkie funkcje związane z usługą Office 365.
  * Niektóre usługi Office 365, takich jak SharePoint Utwórz nazwy główne usług w sposób ciągły, aby umożliwić bezpiecznej komunikacji między składnikami, włącznie z przepływami pracy.
* Gdy administrator dodaje aplikację z galerii aplikacji (spowoduje to również utworzenie obiekt aplikacji)
* Dodaj aplikację do korzystania [serwera Proxy aplikacji usługi Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Łączenie aplikacji dla funkcji logowania jednokrotnego przy użyciu SAML lub hasło rejestracji jednokrotnej (SSO)
* Programowo przy użyciu usługi Azure AD Graph API lub programu PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Jak obiekty aplikacji i nazwy główne usług odnoszą się do siebie?
Aplikacja ma jeden obiekt aplikacji w jego katalogu macierzystego, który odwołuje się do niego co najmniej jeden nazwy główne usług w każdym z katalogów, w których działa (w tym katalogu macierzystego aplikacji).
![Diagram pokazujący sposób interakcji obiektów aplikacji i nazwy główne usług z sobą i wystąpienia usługi Azure AD.][apps_service_principals_directory]

Na powyższym diagramie, firma Microsoft udostępnia dwa katalogi wewnętrznie (wyświetlane po lewej stronie) używa do publikowania aplikacji:

* Jeden dla Microsoft Apps (katalog usług firmy Microsoft)
* Jeden dla wstępnie zintegrowanych aplikacji innych firm (katalog galerii aplikacji)

Aplikacja wydawców/dostawców, którzy integracji z usługą Azure AD muszą mieć katalog publikacji (wyświetlane po prawej stronie jako "Niektóre SaaS katalog").

Aplikacje, które Dodaj siebie (reprezentowane jako **aplikacji (należy do Ciebie)** na diagramie) obejmują:

* Aplikacje, które utworzono (zintegrowany z usługą Azure AD)
* Aplikacje połączone dla single-sign-on
* Aplikacje są publikowane za pomocą serwera proxy aplikacji usługi Azure AD

### <a name="notes-and-exceptions"></a>Uwagi i wyjątków
* Nie wszystkie jednostki usługi wskazywać z powrotem do obiektu aplikacji. Gdy usługi Azure AD został pierwotnie utworzony usług do aplikacji były bardziej ograniczone i nazwy głównej usługi został wystarczające do ustalenia tożsamości aplikacji. Oryginalnej nazwy głównej usługi jest w kształcie bliżej konta usługi Windows Server Active Directory. Z tego powodu jest nadal można utworzyć nazwy główne usług za pośrednictwem różnych ścieżek, takich jak przy użyciu programu Azure AD PowerShell bez tworzenia obiektu aplikacji. Azure AD Graph API wymaga obiekt aplikacji przed utworzeniem usługę podmiotu zabezpieczeń.
* Nie wszystkie informacje opisane powyżej obecnie uwidocznioną programowo. Poniżej są dostępne tylko w interfejsie użytkownika:
  * Reguły przekształcania oświadczeń
  * Mapowanie atrybutów (Inicjowanie obsługi użytkowników)
* Więcej informacji dotyczących nazwy głównej usługi i obiektów aplikacji w dokumentacji interfejsu API REST usługi Azure AD Graph odwołania:
  * [Aplikacji](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Nazwy głównej usługi](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Dlaczego aplikacje integrują się z usługą Azure AD?
Aplikacje są dodawane do usługi Azure AD skorzystać z przynajmniej jednej z usług, które zawiera, w tym:

* Aplikacja uwierzytelniania i autoryzacji
* Uwierzytelniania i autoryzacji użytkowników
* Logowanie Jednokrotne przy użyciu Federacji lub haseł
* Inicjowanie obsługi użytkowników i synchronizacja
* Kontrola dostępu oparta na rolach — użyj katalogu do definiowania ról aplikacji do pełnienia ról na podstawie sprawdzeń autoryzacji w aplikacji
* Usługi autoryzacji OAuth — używane przez usługi Office 365 i innych aplikacji firmy Microsoft do autoryzacji dostępu do interfejsów API/zasobów
* Publikowanie aplikacji i serwer proxy - publikowania aplikacji z sieci prywatnej do Internetu

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kto ma uprawnienia do dodawania aplikacji do mojego wystąpienia usługi Azure AD?
Obiekty, które istnieją niektórych zadań, że wyłącznie administratorzy globalni mogą wykonywać (takie jak dodawanie aplikacji w galerii aplikacji i konfigurowanie aplikacji do użycia serwera Proxy aplikacji) domyślnie wszyscy użytkownicy w katalogu mieć uprawnień do rejestrowania aplikacji są one opracowywanie i uznania za pośrednictwem aplikacji, które są udziału/udzielanie dostępu do swoich danych organizacji za pomocą zgody. Jeśli osoba jest pierwszy użytkownik w katalogu, aby zalogować się do aplikacji i udzielić zgody, który spowoduje utworzenie nazwy głównej usługi dzierżawy; w przeciwnym razie zgody grant informacje będą przechowywane na istniejącej nazwy głównej usługi.

Dzięki czemu użytkownicy mogą zarejestrować wyrażenia zgody na aplikacje może początkowo dźwięku dotyczące, ale należy pamiętać, że:


* Aplikacje są w stanie korzystać z usługi Active Directory systemu Windows Server do uwierzytelniania użytkowników przez wiele lat bez konieczności aplikacji do zarejestrowania lub zapisywane w katalogu. Teraz organizacji zostanie zwiększona widoczność, aby dokładnie ile aplikacje korzystają z katalogu i w jakim celu.
* Delegowanie obowiązków tych użytkowników Negacja potrzebę Rejestracja aplikacji opartych na administratora i proces publikowania. Z Active Directory Federation Services (ADFS) było prawdopodobne administrator musiał dodać aplikację jako uzależniona imieniu deweloperów. Deweloperzy mogą teraz samoobsługi.
* Użytkownicy logujący się do aplikacji przy użyciu konta organizacji do celów biznesowych jest to przydatne. Jeśli następnie opuszczenia organizacji, które automatycznie spowoduje utratę dostępu do swojego konta w aplikacji, który używa.
* O rekord danych został udostępniony, z którym aplikacja jest to przydatne. Dane są bardziej przenośne niż kiedykolwiek i warto mieć wyczyść kto udostępnionych danych z aplikacji, które.
* Właściciele interfejsu API, które używają usługi Azure AD do uwierzytelniania OAuth zdecydować, dokładnie jakie uprawnienia użytkowników są w stanie udzielić aplikacji i uprawnienia, które wymagają administratora, aby zaakceptować. Tylko administratorzy mogą wyrazić zgodę na zakresy większych i bardziej znaczących uprawnienia podczas zgodę użytkownika, ma zakres danych własnych użytkowników i możliwości.
* Gdy użytkownik dodaje lub umożliwia aplikacji dostęp do danych, zdarzenie może być sprawdzona, aby wyświetlić raporty inspekcji w portalu Azure, aby określić, jak aplikacje zostały dodane do katalogu.

Jeśli nadal chcesz uniemożliwić użytkownikom w katalogu z rejestrowanie aplikacji i zalogowaniu się do aplikacji bez zgody administratora, istnieją dwa ustawienia, które można zmienić, aby wyłączyć tych możliwości:

* Aby uniemożliwić użytkownikom zgodę aplikacji we własnym imieniu:
  1. W portalu Azure, przejdź do [ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) sekcję aplikacje przedsiębiorstwa.
  2. Zmień **użytkowników można wyrazić zgodę na dostęp do danych firmowych w ich imieniu aplikacji** do **nr**. 
     *Należy pamiętać, że jeśli zdecydujesz się wyłączyć zgodę użytkownika, administrator będzie musiał wyrażenia zgody na nowej aplikacji, które użytkownik musi używać.*
* Aby uniemożliwić użytkownikom rejestrowanie własnych aplikacji:
  1. W portalu Azure, przejdź do [ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) sekcję usługi Azure Active Directory
  2. Zmień **użytkownicy będą mogli zarejestrować aplikacji** do **nr**.

> [!NOTE]
> Firma Microsoft, sama używa konfiguracji domyślnej użytkownikom możliwość rejestrowania aplikacji i wyrazić zgodę na aplikacje we własnym imieniu.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

