---
title: Przenoszenie uwierzytelniania aplikacji z usług AD FS na usługę Azure Active Directory
description: Ten artykuł ma na celu pomóc organizacjom zrozumieć, jak przenieść aplikacje do usługi Azure AD, z naciskiem na sfederowanych aplikacji SaaS.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891942"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Przenoszenie uwierzytelniania aplikacji z usług federacyjnych Active Directory do usługi Azure Active Directory

[Usługa Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) oferuje uniwersalną platformę tożsamości, która zapewnia osobom, partnerom i klientom jedną tożsamość, aby uzyskać dostęp do aplikacji i współpracować z dowolnej platformy i urządzenia. Usługa Azure AD ma [pełny zestaw funkcji zarządzania tożsamościami.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Standaryzacja uwierzytelniania i autoryzacji aplikacji (aplikacji) do usługi Azure AD umożliwia korzyści, jakie zapewniają te funkcje. 

> [!NOTE]
> W tym artykule skupiono się na przenoszeniu uwierzytelniania aplikacji z lokalnych usług federacyjnych Active Directory i Active Directory na usługę Azure AD. Zapoznaj się z białą księgą [Migrowanie uwierzytelniania aplikacji do usługi Azure AD, aby zapoznać](https://aka.ms/migrateapps/whitepaper) się z omówieniem planowania tego przeniesienia. W opracowaniu w białej księdze omówiono sposób planowania migracji, testowania i szczegółowych informacji.

## <a name="introduction"></a>Wprowadzenie

Jeśli masz katalog lokalny, który zawiera konta użytkowników, prawdopodobnie masz wiele aplikacji, do których użytkownicy uwierzytelniają się. Każda z tych aplikacji jest skonfigurowana dla użytkowników, aby uzyskać dostęp przy użyciu ich tożsamości. 


Użytkownicy mogą również uwierzytelniać się bezpośrednio w lokalnej usłudze Active Directory. Usługi federacyjne Active Directory (AD FS) to usługa tożsamości lokalnej oparta na standardach. Usługi AD FS rozszerzają możliwość korzystania z funkcji logowania jednokrotnego między zaufanymi partnerami biznesowymi bez konieczności logowania użytkowników oddzielnie do każdej aplikacji. Jest to znane jako Federacja.

Wiele organizacji ma aplikacje Software as a Service (SaaS) lub niestandardowe aplikacje biznesowe (LOB) sfederowane bezpośrednio z usługami AD FS, obok aplikacji opartych na usłudze Office 365 i usłudze Azure AD. 

![Aplikacje połączone bezpośrednio lokalnie](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Aby zwiększyć bezpieczeństwo aplikacji, twoim celem jest, aby mieć jeden zestaw kontroli dostępu i zasad w środowisku lokalnym i chmurowym.** 

![Aplikacje połączone za pośrednictwem usługi Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typy aplikacji do migracji

Migrowanie całego uwierzytelniania aplikacji do usługi Azure AD jest optymalne, ponieważ zapewnia jedną płaszczyznę kontroli do zarządzania tożsamościami i dostępem.

Aplikacje mogą używać nowoczesnych lub starszych protokołów do uwierzytelniania. Należy wziąć pod uwagę pierwsze migrowanie aplikacji korzystających z nowoczesnych protokołów uwierzytelniania (takich jak SAML i Open ID Connect). Te aplikacje można ponownie skonfigurować do uwierzytelniania za pomocą usługi Azure AD za pomocą wbudowanego łącznika w naszej Galerii aplikacji lub rejestrując aplikację w usłudze Azure AD. Aplikacje korzystające ze starszych protokołów można zintegrować za pomocą [serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy). 

Aby uzyskać więcej informacji, zobacz [Jakie typy aplikacji można zintegrować z usługą Azure AD?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)

Raport aktywności [aplikacji usług AD FS](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) służy do migracji aplikacji do usługi Azure AD, jeśli masz [włączoną usługę Azure AD Connect Health.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) 

### <a name="the-migration-process"></a>Proces migracji

Podczas procesu przenoszenia uwierzytelniania aplikacji do usługi Azure AD odpowiednio przetestować aplikacje i konfigurację. Zaleca się dalsze używanie istniejących środowisk testowych do testowania migracji, przenosząc się do środowiska produkcyjnego. Jeśli środowisko testowe nie jest obecnie dostępne, można je skonfigurować przy użyciu [usługi Azure App Service](https://azure.microsoft.com/services/app-service/) lub maszyn [wirtualnych platformy Azure](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), w zależności od architektury aplikacji.

Można skonfigurować oddzielny test dzierżawy usługi Azure AD do użycia podczas opracowywania konfiguracji aplikacji. 

Proces migracji może wyglądać następująco:

**Etap 1 — bieżący stan: aplikacja produkcyjna uwierzytelniająca się za pomocą usług AD FS**

![Etap migracji 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Etap 2 — OPCJONALNIE: Test wystąpienia aplikacji wskazującej na testowanie dzierżawy platformy Azure**

Zaktualizuj konfigurację, aby wskazać wystąpienie testu aplikacji do testowej dzierżawy usługi Azure AD i wprowadzać wszelkie wymagane zmiany. Aplikacja może być testowana z użytkownikami w testowej dzierżawie usługi Azure AD. Podczas procesu tworzenia można używać narzędzi, takich jak [Fiddler](https://www.telerik.com/fiddler) do porównywania i weryfikowania żądań i odpowiedzi.

Jeśli konfigurowanie oddzielnej dzierżawy testowej nie jest możliwe, pomiń ten etap i wytrzyj wystąpienie testowe aplikacji i wskaż ją dzierżawie usługi Azure AD w sposób opisany w punkcie 3 poniżej.

![Etap migracji 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Etap 3 — test aplikacji wskazującej na produkcyjną dzierżawę platformy Azure**

Zaktualizuj konfigurację, aby wskazać wystąpienie testu aplikacji na wystąpienie produkcyjne platformy Azure. Teraz można przetestować z użytkownikami w wystąpieniu produkcji. W razie potrzeby przejrzyj sekcję tego artykułu na temat przechodzenia użytkowników.

![Etap migracji 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Etap 4 — aplikacja produkcyjna wskazująca produkcyjną dzierżawę usługi AD**

Zaktualizuj konfigurację aplikacji produkcyjnej, aby wskazać dzierżawę platformy Azure w produkcji.

![Etap migracji 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplikacje uwierzytelniają się przy użyciu usług AD FS, mogą używać grup usługi Active Directory do uzyskania uprawnień. Użyj [synchronizacji usługi Azure AD Connect,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) aby zsynchronizować dane tożsamości między środowiskiem lokalnym a usługą Azure AD przed rozpoczęciem migracji. Sprawdź te grupy i członkostwo przed migracją, aby uzyskać dostęp do tych samych użytkowników podczas migracji aplikacji.

### <a name="line-of-business-lob-apps"></a>Aplikacje biznesowe (LOB)

Aplikacje LOB są tworzone wewnętrznie przez organizację lub dostępne jako standardowy produkt pakowany zainstalowany w centrum danych. Przykłady obejmują aplikacje utworzone w aplikacjach Windows Identity Foundation i SharePoint (nie w usłudze SharePoint Online). 

Aplikacje LOB korzystające z aplikacji OAuth 2.0, OpenID Connect lub WS-Federation można zintegrować z usługą Azure AD jako [rejestracje aplikacji.](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) Integruj aplikacje niestandardowe, które używają SAML 2.0 lub Federacji WS jako [aplikacji innych niż galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) na stronie aplikacji przedsiębiorstwa w [witrynie Azure portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Logowanie jednokrotne oparte na saml

Aplikacje korzystające z SAML 2.0 do uwierzytelniania można skonfigurować dla [logowania jednokrotnego opartego na SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (samol oparte logowania jednokrotnego). Za [pomocą identyfikatora SSO opartego na saml](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)można mapować użytkowników do określonych ról aplikacji na podstawie reguł zdefiniowanych w oświadczeń SAML. 

Aby skonfigurować aplikację SaaS dla logowania jednokrotnego opartego na saml, zobacz [Konfigurowanie logowania jednokrotnego opartego na saml](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications). 

![Zrzuty ekranu użytkownika SSO SAML ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Wiele aplikacji SaaS ma [samouczek specyficzny dla aplikacji,](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) który przechodzi przez konfigurację logowania jednokrotnego opartego na SAML.

![Samouczek aplikacji](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Migracja niektórych aplikacji jest prosta. Aplikacje o bardziej złożonych wymaganiach, takich jak oświadczenia niestandardowe, mogą wymagać dodatkowej konfiguracji w usłudze Azure AD i/lub programie Azure AD Connect. Aby uzyskać informacje dotyczące obsługiwanych mapowań oświadczeń, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

Podczas mapowania atrybutów należy pamiętać o następujących ograniczeniach:

* Nie wszystkie atrybuty, które mogą być wystawione w usługach AD FS będą wyświetlane w usłudze Azure AD jako atrybuty do emitowania tokenów SAML, nawet jeśli te atrybuty są synchronizowane. Podczas edytowania atrybutu lista rozwijana Wartość wyświetli różne atrybuty, które są dostępne w usłudze Azure AD. Sprawdź konfigurację [synchronizacji usługi Azure AD Connect,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) aby upewnić się, że wymagany atrybut — na przykład samAccountName — jest synchronizowany z usługą Azure AD. Atrybuty rozszerzenia można użyć do emitowania dowolnego oświadczenia, które nie jest częścią standardowego schematu użytkownika w usłudze Azure AD.

* W najbardziej typowych scenariuszach aplikacja wymaga tylko oświadczenia NameID i innych typowych oświadczeń identyfikatora użytkownika. Aby ustalić, czy wymagane są dodatkowe oświadczenia, sprawdź, jakie oświadczenia są wystawiane z usług AD FS.

* Nie wszystkie oświadczenia mogą być problemy, jak niektóre oświadczenia są chronione w usłudze Azure AD. 

* Możliwość korzystania z zaszyfrowanych tokenów SAML jest teraz w wersji zapoznawczej. Zobacz [Jak: dostosowywać oświadczenia wystawione w tokenie SAML dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

 

### <a name="software-as-a-service-saas-apps"></a>Aplikacje software as a service (SaaS)

Jeśli użytkownik zaloguje się do aplikacji SaaS, takich jak Salesforce, ServiceNow lub Workday, i jest zintegrowany z usługami AD FS, używasz federacyjnego logowania dla aplikacji SaaS. 

Większość aplikacji SaaS można już skonfigurować w usłudze Azure AD. Firma Microsoft ma wiele wstępnie skonfigurowanych połączeń z aplikacjami SaaS w [galerii aplikacji usługi Azure AD,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)co ułatwi przejście. Aplikacje SAML 2.0 można zintegrować z usługą Azure AD za pośrednictwem galerii aplikacji usługi Azure AD lub [aplikacji innych niż galeria.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

Aplikacje używające uwierzytelniania OAuth 2.0 lub OpenID Connect można podobnie zintegrować z usługą Azure AD jako [rejestracje aplikacji](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Aplikacje korzystające ze starszych protokołów mogą używać [serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) do uwierzytelniania za pomocą usługi Azure AD.

W przypadku jakichkolwiek problemów z dołączaniem aplikacji SaaS można skontaktować się z [aliasem pomocy technicznej integracji aplikacji SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**Certyfikaty podpisywania SAML dla logowania samousznikowego:** Certyfikaty podpisywania są ważną częścią każdego wdrożenia logowania samousznikowego. Usługa Azure AD tworzy certyfikaty podpisywania w celu ustanowienia sfederowanego logowania syda przez SAML dla aplikacji SaaS. Po dodaniu aplikacji galerii lub innych niż galeria, można skonfigurować dodaną aplikację przy użyciu federacyjnej opcji SSO. Zobacz [Zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplikacje i konfiguracje, które można przenosić już dziś

Aplikacje, które można łatwo przenieść dziś obejmują saml 2.0 aplikacje, które używają standardowego zestawu elementów konfiguracji i oświadczeń:

* Nazwa główna użytkownika

* Adres e-mail

* Imię

* Nazwisko

* Atrybut alternatywny, taki jak **NameID** w języku SAML, w tym atrybut poczty usługi Azure AD, prefiks poczty, identyfikator pracownika, atrybuty rozszerzenia 1–15 lub lokalny atrybut **SamAccountName**. Aby uzyskać więcej informacji, zobacz [Edytowanie oświadczenia NameIdentifier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

* Oświadczenia niestandardowe.

Następujące wymagają dodatkowych kroków konfiguracji do migracji do usługi Azure AD:

* Reguły autoryzacji niestandardowej lub uwierzytelniania wieloskładnikowego (MFA) w uścignienia usługi AD FS. Można je skonfigurować przy użyciu funkcji [dostępu warunkowego usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* Aplikacje z wieloma punktami końcowymi adresów URL odpowiedzi. Można je skonfigurować w usłudze Azure AD przy użyciu programu PowerShell lub w interfejsie portalu Azure.

* Aplikacje usług federacyjnych w Internecie, takie jak aplikacje programu SharePoint, które wymagają tokenów w wersji 1.1 protokołu SAML. Można je skonfigurować ręcznie za pomocą programu PowerShell. Można również dodać wstępnie zintegrowany szablon ogólny dla aplikacji SharePoint i SAML 1.1 z galerii. Obsługujemy protokół SAML 2.0.

* Składanie złożonych oświadczeń przekształca reguły. Aby uzyskać informacje na temat mapowania obsługiwanych oświadczeń, zobacz:
   *  [Mapowanie oświadczeń w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikacje i konfiguracje, które nie są obecnie obsługiwane w usłudze Azure AD

Aplikacje, które wymagają następujących funkcji, nie mogą być migrowane dzisiaj.

**Możliwości protokołu**

* Obsługa wzorca ActAs WS-Trust

* Rozpoznawanie artefaktów języka SAML

* Weryfikacja podpisu podpisanych żądań SAML  
Należy pamiętać, że podpisane żądania są akceptowane, ale podpis nie jest weryfikowany.  
Biorąc pod uwagę, że usługa Azure AD zwróci tylko token do punktów końcowych wstępnie skonfigurowanych w aplikacji, weryfikacja podpisu prawdopodobnie nie jest wymagana w większości przypadków.

**Roszczenia w zakresie możliwości tokenów**

* Oświadczenia z magazynów atrybutów innych niż katalog usługi Azure AD, chyba że te dane są synchronizowane z usługą Azure AD. Aby uzyskać więcej informacji, zobacz [omówienie interfejsu API synchronizacji usługi Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Wydawanie atrybutów wielu wartości katalogu. Na przykład nie możemy wystawiać wielowartościowego oświadczenia dla adresów proxy w tej chwili.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapowanie ustawień aplikacji z usług AD FS na usługę Azure AD

Migracja rozpoczyna się od oceny konfiguracji aplikacji w środowisku lokalnym i sposobu mapowania tej konfiguracji do usługi Azure AD. Usługi AD FS i usługi Azure AD działają podobnie, więc pojęcia konfigurowania adresów zaufania, logowania i wylogowywania oraz identyfikatorów mają zastosowanie w obu przypadkach. Dokumentuj ustawienia konfiguracji usług AD FS aplikacji, aby można je było łatwo skonfigurować w usłudze Azure AD.

### <a name="map-app-configuration-settings"></a>Mapowanie ustawień konfiguracji aplikacji

W poniższej tabeli opisano niektóre z najbardziej typowych mapowania ustawień między zaufaniem jednostki uzależniającej usługi AD FS do aplikacji Azure AD ENTERPRISE:

* Usługi AD FS — znajdź ustawienie w zaufaniu jednostki uzależniającej usługi AD FS dla aplikacji. Kliknij prawym przyciskiem myszy jednostkę uzależniającą i wybierz polecenie Właściwości. 

* Usługa Azure AD — ustawienie jest skonfigurowane w [witrynie Azure portal](https://portal.azure.com/) we właściwościach logowania jednokrotnego każdej aplikacji.

| Ustawienie konfiguracji| AD FS| Jak skonfigurować w usłudze Azure AD| SAML Token |
| - | - | - | - |
| **Adres URL logowania do aplikacji** <p>Adres URL użytkownika do logowania się do aplikacji w dostawcy usług (SP) zainicjowany przepływ SAML.| Nie dotyczy| Otwórz podstawową konfigurację SAML z logowania opartego na SAML| Nie dotyczy |
| **Adres URL odpowiedzi aplikacji** <p>Adres URL aplikacji z punktu widzenia dostawcy tożsamości (IdP). IdP wysyła użytkownika i token tutaj po zalogowaniu się użytkownika do IdP.  Jest to również znane jako **saml potwierdzenia konsumenta punktu końcowego**.| Wybieranie karty **Punkty końcowe**| Otwórz podstawową konfigurację SAML z logowania opartego na SAML| Element docelowy w tokenie SAML. Przykładowa wartość:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **Adres URL wylogowania aplikacji** <p>Jest to adres URL, do którego wysyłane są żądania "oczyszczania wylogowywania", gdy użytkownik wyloguje się z aplikacji. IdP wysyła żądanie, aby wylogować użytkownika ze wszystkich innych aplikacji, jak również.| Wybieranie karty **Punkty końcowe**| Otwórz podstawową konfigurację SAML z logowania opartego na SAML| Nie dotyczy |
| **Identyfikator aplikacji** <p>Jest to identyfikator aplikacji z perspektywy IdP. Wartość adresu URL logowania jest często używana dla identyfikatora (ale nie zawsze).  Czasami aplikacja nazywa to "identyfikatorem jednostki".| Wybierz kartę **Identyfikatory**|Otwórz podstawową konfigurację SAML z logowania opartego na SAML| Mapy do **elementu Odbiorcy** w tokenie SAML. |
| **Metadane federacji aplikacji** <p>Jest to lokalizacja metadanych federacji aplikacji. Używana przez dostawcę tożsamości do automatycznego aktualizowania określonych ustawień konfiguracji, takich jak punkty końcowe lub certyfikaty szyfrowania.| Wybierz kartę **Monitorowanie**| Nie dotyczy. Usługa Azure AD nie obsługuje bezpośrednio umużania metadanych federacji aplikacji. Metadane federacji można zaimportować ręcznie.| Nie dotyczy |
| **Identyfikator użytkownika/ identyfikator nazwy** <p>Atrybut używany do jednoznacznego wskazywania tożsamości użytkownika z usług Azure AD lub AD FS do aplikacji.  Ten atrybut jest zazwyczaj nazwy UPN lub adres e-mail użytkownika.| Zasady składania oświadczeń. W większości przypadków reguła oświadczeń wystawia oświadczenie z typem, który kończy się nameidentifier.| Identyfikator można znaleźć w nagłówku **Atrybuty i oświadczenia użytkownika**. Domyślnie jest używana liczba UPN| Mapy do **NameID** elementu w tokenie SAML. |
| **Inne roszczenia** <p>Przykłady innych informacji o roszczeniach, które są często wysyłane z dostawcy identyfikatora do aplikacji, obejmują imię, nazwisko, adres e-mail i członkostwo w grupie.| W usługach AD FS są to inne reguły oświadczenia powiązane z jednostką uzależnioną.| Identyfikator można znaleźć w nagłówku **Atrybuty użytkownika & oświadczenia**. Wybierz pozycję **Wyświetl** i edytuj wszystkie inne atrybuty użytkownika.| Nie dotyczy |


### <a name="map-identity-provider-idp-settings"></a>Ustawienia dostawcy tożsamości mapy (IdP)

Skonfiguruj aplikacje tak, aby wskazywały usługi Azure AD i ad fs dla usługi SSO. W tym miejscu koncentrujemy się na aplikacjach SaaS korzystających z protokołu SAML. Jednak ta koncepcja obejmuje również niestandardowe aplikacje LOB.

> [!NOTE]
> Wartości konfiguracji usługi Azure AD są zgodne ze wzorcem, w którym identyfikator dzierżawy platformy Azure zastępuje {tenant-id} i identyfikator aplikacji zastępuje {application-id}. Te informacje można znaleźć w [witrynie Azure portal](https://portal.azure.com/) w obszarze Usługi Azure Active Directory > Properties: 

* Wybierz identyfikator katalogu, aby wyświetlić identyfikator dzierżawy. 

* Wybierz identyfikator aplikacji, aby wyświetlić identyfikator aplikacji.

 Na wysokim poziomie mapuj następujące kluczowe elementy konfiguracji aplikacji SaaS do usługi Azure AD. 

 

| Element| Wartość konfiguracji |
| - | - |
| Wystawca dostawcy tożsamości| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| Adres URL logowania dostawcy tożsamości| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Adres URL wylogowania dostawcy tożsamości| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Lokalizacja metadanych federacji| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Mapowanie ustawień logowania sytłego dla aplikacji SaaS

Aplikacje SaaS muszą wiedzieć, gdzie wysyłać żądania uwierzytelniania i jak sprawdzić poprawność odebranych tokenów. W poniższej tabeli opisano elementy do konfigurowania ustawień logowania jedno i przesuwu w aplikacji oraz ich wartości lub lokalizacje w usługach AD FS i usłudze Azure AD

| Ustawienie konfiguracji| AD FS| Jak skonfigurować w usłudze Azure AD |
| - | - | - |
| **Adres URL logowania do idp** <p>Adres URL logowania idp z punktu widzenia aplikacji (gdzie użytkownik jest przekierowywane do logowania).| Adres URL logowania usług AD FS to nazwa usługi federacyjnej usług AD FS, po której następuje "/adfs/ls/." <p>Na przykład:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Zastąp {tenant-id} identyfikatorem dzierżawy. <p> W przypadku aplikacji korzystających z protokołu SAML-P:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>W przypadku aplikacji korzystających z protokołu Federacji WS:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **Adres URL wylogowywania IdP**<p>Wyloguj adres URL IdP z perspektywy aplikacji (gdzie użytkownik jest przekierowywane, gdy zdecyduje się wylogować się z aplikacji).| Adres URL wylogowywania jest taki sam jak adres URL logowania lub ten sam adres URL z dołączenym adresem "wa=wsignout1.0". Na przykład:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Zastąp {tenant-id} identyfikatorem dzierżawy.<p>W przypadku aplikacji korzystających z protokołu SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> W przypadku aplikacji korzystających z protokołu Federacji WS:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certyfikat podpisywania tokenów**<p>IdP używa klucza prywatnego certyfikatu do podpisywania wystawionych tokenów. Weryfikuje, że token pochodzi od tego samego dostawcy tożsamości, z którym aplikacja ma skonfigurowaną relację zaufania.| Certyfikat podpisywania tokenu usług AD FS znajduje się w funkcji zarządzania usługami AD FS w obszarze **Certyfikaty**.| Znajdź go w witrynie Azure portal we **właściwościach logowania jednokrotnego** aplikacji pod **nagłówkiem SAML Signing Certificate**. W tym miejscu możesz pobrać certyfikat, aby następnie przekazać go do aplikacji.  <p>Jeśli aplikacja ma więcej niż jeden certyfikat, wszystkie certyfikaty można znaleźć w pliku XML metadanych federacji. |
| **Identyfikator/ "wystawca"**<p>Identyfikator IdP z perspektywy aplikacji (czasami nazywany "identyfikatorem wystawcy").<p>W tokenie SAML wartość jest wyświetlana jako element wystawcy.| Identyfikatorem usług AD FS jest zwykle identyfikator usługi federacyjnej w obszarze Zarządzanie usługami AD FS w obszarze **Właściwości usługi > edycji usługi federacyjnej**. Na przykład:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Zastąp {tenant-id} identyfikatorem dzierżawy.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **Metadane federacji IdP**<p>Lokalizacja publicznie dostępnych metadanych federacji IdP. (Niektóre aplikacje używają metadanych federacji jako alternatywy sytuacji, w której administrator indywidualnie konfiguruje adresy URL, identyfikator i certyfikat podpisywania tokenu).| Znajdź adres URL metadanych federacji usług AD FS w obszarze Zarządzanie usługami AD FS w obszarze **> punkty końcowe usługi > > typ: Metadane federacji**. Na przykład:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Odpowiednia wartość usługi Azure [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)AD jest zgodna ze wzorcem . Zamień {TenantDomainName} nazwą dzierżawy w formacie "contoso.onmicrosoft.com".   <p>Aby uzyskać więcej informacji, zobacz [Metadane federacji](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Reprezentowanie zasad zabezpieczeń usług AD FS w usłudze Azure AD

Podczas przenoszenia uwierzytelniania aplikacji do usługi Azure AD należy utworzyć mapowania z istniejących zasad zabezpieczeń do ich równoważnych lub alternatywnych wariantów dostępnych w usłudze Azure AD. Upewnienie się, że te mapowania można wykonać, spełniając standardy zabezpieczeń wymagane przez właścicieli aplikacji, znacznie ułatwi migrację pozostałej części aplikacji.

Dla każdego typu reguły i jego przykładów firma Microsoft sugeruje w tym miejscu, jak reguła wygląda w usługach AD FS, kod równoważny języka reguły usług AD FS i jak ta mapa w usłudze Azure AD.

### <a name="map-authorization-rules"></a>Reguły autoryzacji map

Poniżej przedstawiono przykłady typów reguł autoryzacji w usługach AD FS oraz sposób mapowania ich na usługę Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Przykład 1: Zezwalaj na dostęp wszystkim użytkownikom

Zezwalaj na dostęp wszystkim użytkownikom wygląda w u usług AD FS: 

![Etap migracji 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Ta mapa jest mapowana na usługę Azure AD w jeden z następujących sposobów:

W [portalu Azure:](https://portal.azure.com/)
* Opcja 1: Ustaw przypisanie użytkownika wymagane na Nie ![edytowanie zasad kontroli dostępu dla aplikacji SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Należy zauważyć, że ustawienie przypisania użytkownika wymaganego przełącznika na Tak wymaga przypisania użytkowników do aplikacji w celu uzyskania dostępu. Po ustawieniu na Nie wszyscy użytkownicy mają dostęp. Ten przełącznik nie kontroluje, co jest wyświetlane użytkownikom w środowisko Moje aplikacje. 

 
* Opcja 2: Na karcie Użytkownicy i grupy przypisz aplikację do grupy automatycznej "Wszyscy użytkownicy". <p>
Aby domyślna grupa "Wszyscy użytkownicy" była dostępna, należy [włączyć grupy dynamiczne](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) w dzierżawie usługi Azure AD.

   ![Moje aplikacje SaaS w usłudze Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Przykład 2: Zezwalaj grupie jawnie

Jawna autoryzacja grupy w u usług AD FS:


![Reguły autoryzacji wydawania ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


W ten sposób reguła jest mapowana na usługę Azure AD:

W [witrynie Azure portal](https://portal.azure.com/)najpierw [utworzysz grupę użytkowników odpowiadającą](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) grupie użytkowników z usług AD FS, a następnie przypiszesz uprawnienia aplikacji do tej grupy:

![Dodaj przypisanie ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Przykład 3: Autoryzowanie określonego użytkownika

Jawna autoryzacja użytkownika w u usług AD FS:

![Reguły autoryzacji wydawania ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

W ten sposób reguła jest mapowana na usługę Azure AD:

W [witrynie Azure portal](https://portal.azure.com/)dodaj użytkownika do aplikacji za pomocą karty Dodaj przydział w aplikacji, jak pokazano poniżej:

![Moje aplikacje SaaS na platformie Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Mapowanie reguł uwierzytelniania wieloskładnikowego 

Wdrożenie lokalne [uwierzytelniania wieloskładnikowego (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) i usług AD FS będzie nadal działać po migracji, ponieważ są sfederowane z usług ad fs. Należy jednak rozważyć migrację do wbudowanych funkcji usługi MFA platformy Azure, które są powiązane z przepływami pracy dostępu warunkowego usługi Azure AD. 

Poniżej przedstawiono przykłady typów reguł usługi MFA w usługach AD FS i sposób mapowania ich na usługę Azure AD na podstawie różnych warunków:

Ustawienia reguł usługi MFA w usług AD FS:

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Przykład 1: Wymuszanie usługi MFA na podstawie użytkowników/grup

Selektor Użytkownika/Grup jest regułą, która umożliwia wymuszanie usługi MFA na podstawie grupy (SID grupy) lub na użytkownika (podstawowy identyfikator SID). Oprócz przypisania użytkownika/grup wszystkie dodatkowe pola wyboru w ui konfiguracji usługi AD FS zapewniają funkcję interfejsu użytkownika konfiguracji usług AD FS jako dodatkowe reguły, które są oceniane po wymuszaniu reguły Użytkownik/Grupy. 


Określ reguły usługi MFA dla użytkownika lub grupy w usłudze Azure AD:

1. Utwórz [nową zasadę dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Wybierz **przydziały**. Dodaj użytkowników lub grupy, na których chcesz wymusić uwierzytelnianie wieloskładnikowe.

3. Skonfiguruj opcje **kontrolek dostępu,** jak pokazano poniżej:  
‎

![Ustawienia usługi AAD MFA](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Przykład 2: Wymuszanie usługi MFA dla niezarejestrowanych urządzeń

Określ reguły usługi MFA dla niezarejestrowanych urządzeń w usłudze Azure AD:

1. Utwórz [nową zasadę dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Ustaw **przydziały** dla **wszystkich użytkowników**.

3. Skonfiguruj opcje **kontrolek dostępu,** jak pokazano poniżej:  
‎

![Ustawienia usługi AAD MFA](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Po ustawieniu dla wielu formantów opcja Wymagaj jednego z wybranych formantów, oznacza to, że jeśli jeden z warunków określonych przez pole wyboru są spełnione przez użytkownika, będą one uzyskać dostęp do aplikacji.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Przykład 3: Wymuszanie usługi MFA na podstawie lokalizacji

Określ reguły usługi MFA na podstawie lokalizacji użytkownika w usłudze Azure AD:

1. Utwórz [nową zasadę dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Ustaw **przydziały** dla **wszystkich użytkowników**.

1. [Konfigurowanie nazwanych lokalizacji w usłudze Azure AD w](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) przeciwnym razie federacja z wewnątrz sieci firmowej jest zaufany. 

1. Skonfiguruj **reguły warunków,** aby określić lokalizacje, dla których chcesz wymusić uwierzytelnianie wieloskładnikowe.

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Skonfiguruj opcje **kontrolek dostępu,** jak pokazano poniżej:


![Zasady kontroli dostępu do map](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Atrybuty emisji map jako reguła oświadczeń

Oto przykład sposobu mapowania atrybutów w u usług AD FS:

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


W ten sposób reguła jest mapowana na usługę Azure AD:

W [witrynie Azure portal](https://portal.azure.com/)wybierz pozycję **Aplikacje przedsiębiorstwa**, **Logowanie jednokrotne**i dodaj **atrybuty tokenu SAML,** jak pokazano poniżej:

![Ustawienia usługi Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Wbudowane zasady kontroli dostępu mapuj

Usługi AD FS 2016 mają kilka wbudowanych zasad kontroli dostępu, które można wybrać:

![Wbudowana kontrola dostępu usługi Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Aby zaimplementować wbudowane zasady w usłudze Azure AD, można użyć [nowej zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) i skonfigurować formanty dostępu lub użyć niestandardowego projektanta zasad w usługach AD FS 2016, aby skonfigurować zasady kontroli dostępu. Edytor reguł zawiera wyczerpującą listę opcji Zezwalaj i Z wyjątkiem opcji, które mogą pomóc w dokonywaniu wszelkiego rodzaju permutacji.

![Zasady kontroli dostępu usługi Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



W tej tabeli wymieniono kilka przydatnych opcji Zezwolenia i Z wyjątkiem oraz sposób mapowania usługi Azure AD. 


| | Jak skonfigurować opcję Zezwalaj w usłudze Azure AD?| Jak skonfigurować opcję Z wyjątkiem w usłudze Azure AD? |
| - | - | - |
| Z określonych sieci| Mapy do [nazwanej lokalizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) w usłudze Azure AD| Użyj opcji **Wyklucz** dla [zaufanych lokalizacji](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| Z określonych grupy| [Ustawianie przypisania użytkownika/grup](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Użyj opcji **Wyklucz** w użytkownikach i grupach |
| Z urządzeń o określonym poziomie zaufania| Ustaw to z kontroli "Stan urządzenia" w obszarze Ustawienia ->| Użyj opcji **Wyklucz** w obszarze Stan stanu urządzenia i Uwzględnij **wszystkie urządzenia** |
| Z konkretnymi roszczeniami w żądaniu| Tego ustawienia nie można przeprowadzić migracji| Tego ustawienia nie można przeprowadzić migracji |


Przykład konfigurowania opcji Wyklucz dla zaufanych lokalizacji w witrynie Azure Portal:

![Zrzut ekranu przedstawiający mapowanie zasad kontroli dostępu](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Przechodzenie użytkowników z usług AD FS do usługi Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synchronizowanie grup usług AD FS w usłudze Azure AD

Podczas mapowania reguł autoryzacji aplikacje uwierzytelniają się za pomocą usług AD FS mogą używać grup usługi Active Directory do uprawnień. W takim przypadku należy użyć [usługi Azure AD Connect,](https://go.microsoft.com/fwlink/?LinkId=615771) aby zsynchronizować te grupy z usługą Azure AD przed migracją aplikacji. Upewnij się, że przed migracją zweryfikowałeś te grupy i członkostwo, aby można było udzielić dostępu tym samym użytkownikom podczas migracji aplikacji.

Aby uzyskać więcej informacji, zobacz [Wymagania wstępne dotyczące używania atrybutów grupy zsynchronizowanych z usługi Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims).

### <a name="setup-user-self-provisioning"></a>Samoa inicjowanie obsługi administracyjnej użytkownika instalatora 

Niektóre aplikacje SaaS obsługują możliwość samodzielnego inicjowania obsługi administracyjnej użytkowników podczas pierwszego logowania się do aplikacji. W usłudze Azure Active Directory (Azure AD) termin inicjowania obsługi administracyjnej aplikacji odnosi się do automatycznego tworzenia tożsamości użytkowników i ról w aplikacjach w chmurze[(SaaS),](https://azure.microsoft.com/overview/what-is-saas/)do których użytkownicy potrzebują dostępu. Użytkownicy, którzy są migrowani, będą już mieli konto w aplikacji SaaS. Każdy nowy użytkownik dodany po migracji będzie musiał zostać aprowizwany. Testowanie [aprowizacji aplikacji SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) po migracji aplikacji.

### <a name="sync-external-users-in-azure-ad"></a>Synchronizowanie użytkowników zewnętrznych w usłudze Azure AD

Istniejący użytkownik zewnętrzny może być skonfigurowany na dwa główne sposoby w ramach usług AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Użytkownicy zewnętrzni z kontem lokalnym w organizacji

Nadal będziesz mógł korzystać z tych kont w taki sam sposób, jak działają wewnętrzne konta użytkowników. Te konta użytkowników zewnętrznych mają nazwę zasad w organizacji, chociaż adres e-mail konta może wskazywać zewnętrznie. W miarę postępów w migracji można korzystać z korzyści, które oferuje [usługa Azure AD B2B,](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) migrując tych użytkowników, aby użyć własnej tożsamości firmowej, gdy taka tożsamość jest dostępna. Usprawnia to proces logowania dla tych użytkowników, ponieważ często logują się za pomocą własnego logowania firmowego. Administracja organizacji również zostanie złagodzona, ponieważ nie będzie już musiała zarządzać kontami dla użytkowników zewnętrznych.

#### <a name="federated-external-identities"></a>Sfederowane tożsamości zewnętrzne

Jeśli obecnie federujesz się z organizacją zewnętrzną, masz kilka podejść do podjęcia:

* [Dodaj użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). Możesz proaktywnie wysyłać zaproszenia do współpracy B2B z portalu administracyjnego usługi Azure AD do organizacji partnerskiej, aby poszczególne elementy członkowskie mogły nadal korzystać z aplikacji i zasobów, do których są przyzwyczajeni. 

* [Utwórz samoobsługowy przepływ pracy rejestracji B2B,](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) który generuje żądanie dla poszczególnych użytkowników w organizacji partnerskiej przy użyciu interfejsu API zaproszenia B2B.

Bez względu na to, jak są skonfigurowani obecni użytkownicy zewnętrzni, prawdopodobnie mają uprawnienia skojarzone z ich kontem, zarówno w członkostwie w grupie, jak i w określonych uprawnieniach. Oceń, czy te uprawnienia muszą zostać zmigrowane lub oczyszczone. Konta w organizacji reprezentujące użytkownika zewnętrznego muszą zostać wyłączone po migracji użytkownika do tożsamości zewnętrznej. Proces migracji należy omówić z partnerami biznesowymi, ponieważ może do czasu przerwania ich zdolności do łączenia się z zasobami.

## <a name="migrate-and-test-your-apps"></a>Migrowanie i testowanie aplikacji

Postępuj zgodnie z procesem migracji opisanym w tym artykule.

Następnie przejdź do [witryny Azure portal,](https://aad.portal.azure.com/) aby sprawdzić, czy migracja była sukcesem. Postępuj zgodnie z poniższymi instrukcjami:
1. Wybierz **aplikacje dla** > przedsiębiorstw**Wszystkie aplikacje** i znajdź aplikację z listy.

1. Wybierz **pozycję Zarządzaj** > **użytkownikami i grupami,** aby przypisać do aplikacji co najmniej jednego użytkownika lub grupę.

1. Wybierz **pozycję Zarządzaj dostępem** > **warunkowym**. Przejrzyj listę zasad i upewnij się, że nie blokujesz dostępu do aplikacji za pomocą [zasad dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

W zależności od sposobu konfigurowania aplikacji sprawdź, czy urządzenie sycowe działa poprawnie. 

| Typ uwierzytelniania| Testowanie |
| - | - |
| OAuth / OpenID Connect| Wybierz **aplikacje przedsiębiorstwa > uprawnienia** i upewnij się, że użytkownik wyraził zgodę na aplikację, która ma być używana w organizacji w ustawieniach użytkownika aplikacji.  
‎ |
| Logowanie jednokrotne oparte na języku SAML| Użyj przycisku [Testuj ustawienia SAML](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) znajdującego się w obszarze **Logowanie jednokrotne**.  
‎ |
| Logi SSO oparte na hasłach| Pobierz i zainstaluj rozszerzenie [MyApps Secure Sign](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[in .](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) To rozszerzenie ułatwia uruchamianie dowolnej aplikacji w chmurze organizacji, które wymagają użycia procesuzenia typu SSO.  
‎ |
| Serwer proxy aplikacji| Upewnij się, że łącznik jest uruchomiony i przypisany do aplikacji. Odwiedź[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) [przewodnik rozwiązywania problemów z serwerem proxy aplikacji, aby](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) uzyskać dalszą pomoc.  
‎ |

> [!NOTE]
> Pliki cookie ze starego środowiska usług AD FS będą nadal trwałe na komputerach użytkownika. Te pliki cookie mogą powodować problemy z migracją, ponieważ użytkownicy mogą być kierowani do starego środowiska logowania usług AD FS w porównaniu do nowego logowania usługi Azure AD. Może być konieczne ręczne wyczyszczenie plików cookie przeglądarki użytkownika lub użycie skryptu. Można również użyć programu System Center Configuration Manager lub podobnej platformy.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują błędy z testu zmigrowanych aplikacji, rozwiązywanie problemów może być pierwszym krokiem przed powrotem do istniejących stron korzystających z usług AD FS. Zobacz [Jak debugować aplikacje logowania jednokrotnego oparte na SAML w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues).

### <a name="rollback-migration"></a>Wycofywanie migracji

Jeśli migracja nie powiedzie się, zaleca się pozostawienie istniejących stron uzależnień na serwerach usług AD FS i usunięcie dostępu do stron uzależnień. Pozwoli to na szybkie rezerwowe w razie potrzeby podczas wdrażania.

### <a name="end-user-communication"></a>Komunikacja z użytkownikiem końcowym

Podczas gdy samo okno planowanej awarii może być minimalne, nadal należy zaplanować proaktywne komunikowanie tych ram czasowych z pracownikami podczas tworzenia przecięcia z usług AD FS na usługę Azure AD. Upewnij się, że środowisko aplikacji ma przycisk Opinie lub wskaźniki do działu pomocy technicznej w przypadku problemów.

Po zakończeniu wdrażania można wysłać komunikację informującą użytkowników o pomyślnym wdrożeniu i przypomnieć im o wszelkich nowych krokach, które muszą podjąć.

* Poinstruuj użytkowników, aby korzystali z [Panelu dostępu](https://myapps.microsoft.com.com/) w celu uzyskania dostępu do wszystkich zmigrowanych aplikacji. 

* Przypomnij użytkownikom, że może być konieczne zaktualizowanie ustawień usługi MFA. 

* Jeśli samoobsługowy resetowanie hasła jest wdrażany, użytkownicy mogą być zmuszeni do zaktualizowania lub zweryfikowania swoich metod uwierzytelniania. Zobacz szablony komunikacji [usługi MFA](https://aka.ms/mfatemplates) i [SSPR](https://aka.ms/ssprtemplates) dla użytkowników końcowych.

Komunikacja z użytkownikami zewnętrznymi: Ta grupa użytkowników ma zwykle największy wpływ na problemy. Jest to szczególnie ważne, jeśli postawa zabezpieczeń nakazuje inny zestaw reguł dostępu warunkowego lub profili ryzyka dla partnerów zewnętrznych. Upewnij się, że partnerzy zewnętrzni są świadomi harmonogramu migracji do chmury i mają ramy czasowe, w których są zachęcani do udziału we wdrożeniu pilotażowym, które testuje wszystkie przepływy unikatowe dla współpracy zewnętrznej. Na koniec upewnij się, że mają one dostęp do działu pomocy technicznej w przypadku rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki
Odczytanie [uwierzytelniania aplikacji migrujących do usługi Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Konfigurowanie [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) i usługi [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
