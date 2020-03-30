---
title: Jak i dlaczego aplikacje są dodawane do usługi Azure AD
titleSuffix: Microsoft identity platform
description: Co to znaczy, że aplikacja ma zostać dodana do usługi Azure AD i jak się tam dostać?
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: d47ed3a4cd4fbdcb69b956d3c8418f70a71cf44f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263142"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak i dlaczego aplikacje są dodawane do usługi Azure AD

Istnieją dwie reprezentacje aplikacji w usłudze Azure AD:

* [Obiekty aplikacji](app-objects-and-service-principals.md#application-object) — chociaż istnieją [wyjątki](#notes-and-exceptions), obiekty aplikacji można uznać za definicję aplikacji.
* [Jednostki usługi](app-objects-and-service-principals.md#service-principal-object) — można uznać za wystąpienie aplikacji. Jednostki usługi zazwyczaj odwołują się do obiektu aplikacji, a do jednego obiektu aplikacji może odwoływać się wiele podmiotów usługi w katalogach.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Co to są obiekty aplikacji i skąd pochodzą?

Obiekty [aplikacji](app-objects-and-service-principals.md#application-object) można zarządzać w witrynie Azure portal za pośrednictwem środowiska [rejestracji aplikacji.](https://aka.ms/appregistrations) Obiekty aplikacji opisują aplikację do usługi Azure AD i można uznać za definicję aplikacji, dzięki czemu usługa wiedzieć, jak wystawiać tokeny do aplikacji na podstawie jej ustawień. Obiekt aplikacji będzie istnieć tylko w katalogu macierzystym, nawet jeśli jest to aplikacja obsługująca wiele dzierżaw w innych katalogach. Obiekt aplikacji może zawierać dowolną z następujących informacji (jak również dodatkowe informacje, które nie zostały wymienione w tym miejscu):

* Nazwa, logo i wydawca
* Przekierowywanie identyfikatorów URI
* Wpisy tajne (klucze symetryczne i/lub asymetryczne używane do uwierzytelniania aplikacji)
* Zależności interfejsu API (OAuth)
* Opublikowane interfejsy API/zasoby/zakresy (OAuth)
* Role aplikacji (RBAC)
* Metadane i konfiguracja usługi SSO
* Metadane i konfiguracja inicjowania obsługi administracyjnej przez użytkownika
* Metadane i konfiguracja serwera proxy

Obiekty aplikacji mogą być tworzone za pomocą wielu ścieżek, w tym:

* Rejestracje aplikacji w witrynie Azure portal
* Tworzenie nowej aplikacji przy użyciu programu Visual Studio i konfigurowanie jej do używania uwierzytelniania usługi Azure AD
* Gdy administrator dodaje aplikację z galerii aplikacji (która również utworzy jednostkę usługi)
* Tworzenie nowej aplikacji za pomocą interfejsu API programu Microsoft Graph lub programu PowerShell
* Wiele innych, w tym różne środowiska deweloperów na platformie Azure i w środowiskach eksploratora interfejsu API w centrach deweloperów

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Czym są podmioty świadczące usługi i skąd pochodzą?

Można zarządzać [jednostkami usług](app-objects-and-service-principals.md#service-principal-object) w witrynie Azure portal za pośrednictwem [środowiska enterprise applications.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Jednostki usługi są, które regulują aplikacji łączącej się z usługą Azure AD i można uznać za wystąpienie aplikacji w katalogu. Dla każdej aplikacji może mieć co najwyżej jeden obiekt aplikacji (który jest zarejestrowany w katalogu "home") i jeden lub więcej obiektów jednostkowych usługi reprezentujących wystąpienia aplikacji w każdym katalogu, w którym działa. 

Podmiot usługi może obejmować:

* Odwołanie do obiektu aplikacji za pośrednictwem właściwości identyfikator aplikacji
* Rekordy przypisania lokalnych użytkowników i grupowych ról aplikacji
* Rekordy uprawnień użytkowników lokalnych i administratorów przyznanych aplikacji
  * Na przykład: uprawnienie aplikacji do uzyskiwania dostępu do poczty e-mail określonego użytkownika
* Rekordy zasad lokalnych, w tym zasady dostępu warunkowego
* Rekordy alternatywnych ustawień lokalnych dla aplikacji
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (inicjowanie obsługi administracyjnej przez użytkowników)
  * Role aplikacji specyficzne dla katalogu (jeśli aplikacja obsługuje role niestandardowe)
  * Nazwa lub logo specyficzne dla katalogu

Podobnie jak obiekty aplikacji, podmioty usługi mogą być również tworzone za pośrednictwem wielu ścieżek, w tym:

* Gdy użytkownicy logują się do aplikacji innej firmy zintegrowanej z usługą Azure AD
  * Podczas logowania użytkownicy są proszeni o udzielenie aplikacji uprawnień dostępu do swojego profilu i innych uprawnień. Pierwsza osoba, która wyrazi zgodę powoduje, że podmiot usługi, który reprezentuje aplikację, które mają zostać dodane do katalogu.
* Gdy użytkownicy logują się do usług online firmy Microsoft, takich jak [Office 365](https://products.office.com/)
  * Podczas subskrybowania usługi Office 365 lub rozpoczęcia wersji próbnej w katalogu reprezentującym różne usługi, które są używane do dostarczania wszystkich funkcji skojarzonych z usługą Office 365, tworzony jest co najmniej jeden podmiot usługi.
  * Niektóre usługi usługi Office 365, takie jak SharePoint, tworzą podmioty usługi na bieżąco, aby umożliwić bezpieczną komunikację między składnikami, w tym przepływami pracy.
* Gdy administrator dodaje aplikację z galerii aplikacji (spowoduje to również utworzenie obiektu aplikacji)
* Dodawanie aplikacji do korzystania z [serwera proxy aplikacji usługi Azure AD](/azure/active-directory/manage-apps/application-proxy)
* Łączenie aplikacji do logowania jednokrotnego przy użyciu saml lub hasła logowania jednokrotnego (Logowanie jednokrotne)
* Programowo za pośrednictwem interfejsu API programu Microsoft Graph lub programu PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>W jaki sposób obiekty aplikacji i jednostki usługi są ze sobą powiązane?

Aplikacja ma jeden obiekt aplikacji w katalogu macierzystym, do którego odwołuje się jeden lub więcej podmiotów korzystających z usługi w każdym z katalogów, w których działa (w tym katalogu macierzystego aplikacji).

![Pokazuje relację między obiektami aplikacji a jednostkami usługi][apps_service_principals_directory]

Na poprzednim diagramie firma Microsoft przechowuje wewnętrznie dwa katalogi (pokazane po lewej stronie), których używa do publikowania aplikacji:

* Jeden dla aplikacji Microsoft Apps (katalog usług firmy Microsoft)
* Jeden dla wstępnie zintegrowanych aplikacji innych firm (katalog galerii aplikacji)

Wydawcy aplikacji/dostawcy, którzy integrują się z usługą Azure AD, muszą mieć katalog publikowania (wyświetlany po prawej stronie jako "Niektóre katalog SaaS").

Aplikacje, które można dodać samodzielnie (reprezentowane jako **aplikacja (twoja)** na diagramie) obejmują:

* Aplikacje opracowane (zintegrowane z usługą Azure AD)
* Aplikacje połączone w celu logowania jednokrotnego
* Aplikacje opublikowane przy użyciu serwera proxy aplikacji usługi Azure AD

### <a name="notes-and-exceptions"></a>Uwagi i wyjątki

* Nie wszystkie jednostki usługi wskazują z powrotem do obiektu aplikacji. Gdy usługa Azure AD została pierwotnie sbudniczą usługi świadczone aplikacjom były bardziej ograniczone, a podmiot usługi był wystarczający do ustanowienia tożsamości aplikacji. Oryginalna jednostka usługi była bliższa kształtowi konta usługi Active Directory systemu Windows Server. Z tego powodu nadal jest możliwe do tworzenia podmiotów usługi za pośrednictwem różnych ścieżek, takich jak przy użyciu programu Azure AD PowerShell, bez uprzedniego tworzenia obiektu aplikacji. Interfejs API programu Microsoft Graph wymaga obiektu aplikacji przed utworzeniem jednostki usługi.
* Nie wszystkie informacje opisane powyżej są obecnie eksponowane programowo. Następujące są dostępne tylko w interfejsie użytkownika:
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (inicjowanie obsługi administracyjnej przez użytkowników)
* Aby uzyskać bardziej szczegółowe informacje na temat jednostki usługi i obiektów aplikacji, zobacz dokumentację referencyjną interfejsu API programu Microsoft Graph:
  * [Aplikacja](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)
  * [Główny zobowiązany do świadczenia usług](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Dlaczego aplikacje integrują się z usługą Azure AD?

Aplikacje są dodawane do usługi Azure AD, aby wykorzystać jedną lub więcej usług, które zapewnia, w tym:

* Uwierzytelnianie i autoryzacja aplikacji
* Uwierzytelnianie i autoryzacja użytkownika
* Loga jedno i przesuwnienia przy użyciu federacji lub hasła
* Inicjowanie obsługi administracyjnej i synchronizacja użytkowników
* Kontrola dostępu oparta na rolach — używanie katalogu do definiowania ról aplikacji w celu przeprowadzania kontroli autoryzacji opartych na rolach w aplikacji
* Usługi autoryzacji OAuth — używane przez usługę Office 365 i inne aplikacje firmy Microsoft do autoryzowania dostępu do interfejsów API/zasobów
* Publikowanie aplikacji i serwer proxy — publikowanie aplikacji z sieci prywatnej w Internecie

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kto ma uprawnienia do dodawania aplikacji do mojego wystąpienia usługi Azure AD?

Chociaż istnieją pewne zadania, które mogą wykonywać tylko administratorzy globalni (takie jak dodawanie aplikacji z galerii aplikacji i konfigurowanie aplikacji do używania serwera proxy aplikacji) domyślnie wszyscy użytkownicy w katalogu mają prawa do rejestrowania obiektów aplikacji, które opracowują, i dyskrecję nad aplikacjami, które udostępniają/dają dostęp do swoich danych organizacyjnych za zgodą. Jeśli osoba jest pierwszym użytkownikiem w katalogu, aby zalogować się do aplikacji i udzielić zgody, który utworzy jednostkę usługi w dzierżawie; w przeciwnym razie informacje o udzieleniu zgody będą przechowywane na istniejącej stronie głównej usługi.

Zezwolenie użytkownikom na rejestrację i wyrażenie zgody na aplikacje może początkowo brzmieć na temat, ale należy pamiętać o następujących elementów:


* Aplikacje od wielu lat mogą korzystać z usługi Windows Server Active Directory do uwierzytelniania użytkowników bez konieczności rejestrowania lub rejestrowania aplikacji w katalogu. Teraz organizacja będzie miała lepszą widoczność do dokładnie ilu aplikacji korzysta z katalogu i w jakim celu.
* Delegowanie tych obowiązków użytkownikom neguje potrzebę rejestracji i publikowania aplikacji kierowanych przez administratora. W przypadku usług federacyjnych Active Directory (ADFS) prawdopodobnie administrator musiał dodać aplikację jako jednostkę uzależniającą w imieniu swoich deweloperów. Teraz deweloperzy mogą samoobsługi.
* Użytkownicy logujący się do aplikacji przy użyciu swoich kont organizacji do celów biznesowych jest dobrą rzeczą. Jeśli następnie opuszczą organizację, automatycznie stracą dostęp do swojego konta w aplikacji, z którą korzystali.
* Posiadanie rejestru danych, które zostały udostępnione, która aplikacja jest dobrą rzeczą. Dane są bardziej przewoźne niż kiedykolwiek i warto mieć jasny rejestr, kto udostępnił jakie dane, z którymi aplikacjami.
* Właściciele interfejsu API, którzy używają usługi Azure AD dla OAuth zdecydować dokładnie, jakie uprawnienia użytkownicy są w stanie udzielić do aplikacji i które uprawnienia wymagają administratora, aby wyrazić zgodę. Tylko administratorzy mogą wyrazić zgodę na większe zakresy i bardziej znaczące uprawnienia, podczas gdy zgoda użytkownika jest ograniczone do własnych danych i możliwości użytkowników.
* Gdy użytkownik dodaje lub zezwala aplikacji na dostęp do swoich danych, zdarzenie może być inspekcji, dzięki czemu można wyświetlić raporty inspekcji w witrynie Azure portal, aby określić, jak aplikacja została dodana do katalogu.

Jeśli nadal chcesz uniemożliwić użytkownikom w katalogu rejestrowanie aplikacji i logowanie się do aplikacji bez zgody administratora, istnieją dwa ustawienia, które można zmienić, aby wyłączyć te funkcje:

* Aby uniemożliwić użytkownikom wyrażenie zgody na aplikacje we własnym imieniu:
  1. W witrynie Azure portal przejdź do sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) w obszarze Aplikacje dla przedsiębiorstw.
  2. Zmień **Użytkownicy mogą wyrazić zgodę na dostęp do danych firmy w ich imieniu** na **Nie**.
     
     > [!NOTE]
     > Jeśli zdecydujesz się wyłączyć zgodę użytkownika, administrator będzie musiał wyrazić zgodę na każdą nową aplikację, z której użytkownik musi korzystać.

* Aby uniemożliwić użytkownikom rejestrowanie własnych aplikacji:
  1. W witrynie Azure portal przejdź do sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) w obszarze Usługa Azure Active Directory
  2. Zmień **Użytkownicy mogą rejestrować aplikacje** na **nie**.

> [!NOTE]
> Firma Microsoft używa domyślnej konfiguracji z użytkownikami mogą rejestrować aplikacje i wyrażać zgodę na aplikacje we własnym imieniu.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
