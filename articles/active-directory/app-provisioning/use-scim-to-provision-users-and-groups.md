---
title: Tworzenie punktu końcowego scim dla inicjowania obsługi administracyjnej dla aplikacji z usługi Azure AD
description: System zarządzania tożsamościami między domenami (SCIM) standaryzuje automatyczne inicjowanie obsługi administracyjnej użytkowników. Dowiedz się, jak opracować punkt końcowy SCIM, zintegrować interfejs API scim z usługą Azure Active Directory i rozpocząć automatyzację inicjowania obsługi administracyjnej użytkowników i grup w aplikacjach w chmurze.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297675"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Tworzenie punktu końcowego scim i konfigurowanie inicjowania obsługi administracyjnej użytkowników za pomocą usługi Azure Active Directory (Azure AD)

Jako deweloper aplikacji można użyć interfejsu API zarządzania użytkownikami systemu między domenami (SCIM), aby umożliwić automatyczne inicjowanie obsługi administracyjnej użytkowników i grup między aplikacją a usługą Azure AD. W tym artykule opisano sposób tworzenia punktu końcowego scim i integracji z usługą inicjowania obsługi administracyjnej usługi Azure AD. Specyfikacja SCIM zawiera wspólny schemat użytkownika do inicjowania obsługi administracyjnej. W połączeniu ze standardami federacji, takimi jak SAML lub OpenID Connect, SCIM daje administratorom kompleksowe, oparte na standardach rozwiązanie do zarządzania dostępem.

SCIM to znormalizowana definicja dwóch punktów końcowych: punktu końcowego /Users i punktu końcowego /Groups. Używa typowych zleceń REST do tworzenia, aktualizowania i usuwania obiektów oraz wstępnie zdefiniowanego schematu dla wspólnych atrybutów, takich jak nazwa grupy, nazwa użytkownika, imię, nazwisko i adres e-mail. Aplikacje, które oferują SCIM 2.0 REST API można zmniejszyć lub wyeliminować ból pracy z zastrzeżonym interfejsem API zarządzania użytkownikami. Na przykład każdy zgodny klient SCIM wie, jak zrobić http post obiektu JSON do punktu końcowego /Users, aby utworzyć nowy wpis użytkownika. Zamiast wymagać nieco innego interfejsu API dla tych samych podstawowych akcji, aplikacje zgodne ze standardem SCIM mogą natychmiast korzystać z istniejących wcześniej klientów, narzędzi i kodu. 

![Inicjowanie obsługi administracyjnej z usługi Azure AD do aplikacji za pomocą narzędzia SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standardowy schemat obiektu użytkownika i interfejsy API odpoczynku dla zarządzania zdefiniowane w SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) umożliwiają dostawcom tożsamości i aplikacjom łatwiej integrować się ze sobą. Deweloperzy aplikacji, którzy budują punkt końcowy SCIM można zintegrować z dowolnego klienta zgodnego z scim bez konieczności wykonywania pracy niestandardowej.

Automatyzacja inicjowania obsługi administracyjnej aplikacji wymaga tworzenia i integrowania punktu końcowego scim z klientem scim usługi Azure. Wykonaj następujące kroki, aby rozpocząć inicjowanie obsługi administracyjnej użytkowników i grup do aplikacji. 
    
  * **[Krok 1: Zaprojektuj schemat użytkownika i grupy.](#step-1-design-your-user-and-group-schema)** Identyfikowanie obiektów i atrybutów potrzeb aplikacji i określanie sposobu mapowania schematu użytkownika i grupy obsługiwanego przez implementację scim usługi Azure AD.

  * **[Krok 2: Zrozumienie implementacji scim usługi Azure AD.](#step-2-understand-the-azure-ad-scim-implementation)** Dowiedz się, jak zaimplementowano klienta scim usługi Azure i modeluj obsługę żądań protokołu SCIM i odpowiedzi.

  * **[Krok 3: Tworzenie punktu końcowego SCIM.](#step-3-build-a-scim-endpoint)** Punkt końcowy musi być zgodny z technologią SCIM 2.0, aby zintegrować się z usługą inicjowania obsługi administracyjnej usługi Azure AD. Opcjonalnie można użyć bibliotek infrastruktury języka wspólnego (CLI) firmy Microsoft i przykładów kodu do utworzenia punktu końcowego. Próbki te mają jedynie na celu odniesienie i badanie; zaleca się, aby nie kodować aplikacji produkcyjnej, aby uwzględnić zależność od nich.

  * **[Krok 4: Zintegruj punkt końcowy scim z klientem scim usługi Azure.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Jeśli twoja organizacja używa aplikacji innej firmy, która implementuje profil SCIM 2.0 obsługiwane usługi Azure AD, można rozpocząć automatyzację inicjowania obsługi administracyjnej i deprowidowania użytkowników i grup od razu.

  * **[Krok 5: Opublikuj aplikację w galerii aplikacji usługi Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Ułatw klientom odnajdywanie aplikacji i łatwą konfigurację inicjowania obsługi administracyjnej. 

![Kroki integracji punktu końcowego scim z usługą Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Krok 1: Projektowanie schematu użytkownika i grupy

Każda aplikacja wymaga różnych atrybutów, aby utworzyć użytkownika lub grupę. Rozpocznij integrację, identyfikując obiekty (użytkowników, grupy) i atrybuty (imię i nazwisko, menedżer, stanowisko itp.), których wymaga aplikacja. Standard SCIM definiuje schemat zarządzania użytkownikami i grupami. Podstawowy schemat użytkownika wymaga tylko trzech atrybutów: **id** (identyfikator zdefiniowany przez dostawcę usług), **identyfikator externalId** (identyfikator zdefiniowany przez klienta) i **meta** (metadane tylko do odczytu obsługiwane przez dostawcę usług). Wszystkie inne atrybuty są opcjonalne. Oprócz schematu użytkownika podstawowego standard SCIM definiuje rozszerzenie użytkownika przedsiębiorstwa i model rozszerzania schematu użytkownika w celu zaspokojenia potrzeb aplikacji. Jeśli na przykład aplikacja wymaga menedżera użytkownika, można użyć schematu użytkownika przedsiębiorstwa do zbierania menedżera użytkownika i schematu podstawowego do zbierania wiadomości e-mail użytkownika. Aby zaprojektować schemat, wykonaj poniższe czynności:
  1. Wyświetl listę atrybutów, których wymaga aplikacja. Pomocne może być rozbicie wymagań na atrybuty potrzebne do uwierzytelniania (np. loginName i e-mail), atrybuty potrzebne do zarządzania cyklem życia użytkownika (np. status / aktywny) oraz inne atrybuty potrzebne do działania danej aplikacji (np. menedżer, tag).
  2. Sprawdź, czy te atrybuty są już zdefiniowane w schemacie użytkownika podstawowego lub schematu użytkownika przedsiębiorstwa. Jeśli wszystkie atrybuty, które są potrzebne i nie są objęte schematów użytkownika podstawowego lub przedsiębiorstwa, należy zdefiniować rozszerzenie schematu użytkownika, który obejmuje atrybuty, których potrzebujesz. W poniższym przykładzie dodaliśmy rozszerzenie do użytkownika, aby umożliwić inicjowanie obsługi administracyjnej "tag" na użytkownika. Najlepiej jest zacząć od tylko schematów użytkowników podstawowych i korporacyjnych i rozwinąć się do dodatkowych schematów niestandardowych później.  
  3. Mapowanie atrybutów SCIM do atrybutów użytkownika w usłudze Azure AD. Jeśli jeden z atrybutów zdefiniowanych w punkcie końcowym scim nie ma wyraźnego odpowiednika w schemacie użytkownika usługi Azure AD, istnieje duża szansa, że dane nie są przechowywane w obiekcie użytkownika w ogóle na większości dzierżaw. Należy wziąć pod uwagę, czy ten atrybut może być opcjonalne do tworzenia użytkownika. Jeśli atrybut jest krytyczny dla aplikacji do pracy, przewodnik administratora dzierżawy, aby rozszerzyć ich schematu lub użyć atrybutu rozszerzenia, jak pokazano poniżej dla "tagów" właściwości.

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabela 1: Nakreśl atrybuty, których potrzebujesz 
| Krok 1: Określanie atrybutów wymaganych przez aplikację| Krok 2: Mapuj wymagania aplikacji do standardu SCIM| Krok 3: Mapowanie atrybutów SCIM do atrybutów usługi Azure AD|
|--|--|--|
|Loginname|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|nazwa.lastName|lastName|
|poczta robocza|Wiadomości e-mail[wpisz eq "praca"].wartość|Poczta|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|aktywne|isSoftDeleted (obliczona wartość nie jest przechowywana na użytkowniku)|

Schemat zdefiniowany powyżej będzie reprezentowany przy użyciu ładunku Json poniżej. Należy zauważyć, że oprócz atrybutów wymaganych dla aplikacji reprezentacja JSON zawiera wymagane atrybuty "id", "externalId" i "meta".

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabela 2: Domyślne mapowanie atrybutów użytkownika
Następnie można użyć poniższej tabeli, aby zrozumieć, jak atrybuty aplikacji wymaga może mapować do atrybutu w usłudze Azure AD i SCIM RFC. Można [dostosować](customize-application-attributes.md) sposób mapowania atrybutów między usługą Azure AD i punktu końcowego SCIM. Pamiętaj, że nie musisz obsługiwać zarówno użytkowników, jak i grup ani wszystkich atrybutów pokazanych poniżej. Są one odwołaniem do tego, jak atrybuty w usłudze Azure AD są często mapowane na właściwości w protokole SCIM. 

| Użytkownik usługi Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted (Nieumiejeć) |aktywne |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|Idpracownika|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Numer telefonu faksowegoNumer |phoneNumbers[wpisz eq "fax"].wartość |
| givenName |name.givenName |
| jobTitle (Tytuł pracy) |title |
| mail (poczta) |wiadomości e-mail[wpisz eq "praca"].wartość |
| mailNickname (nazwa pocztowa) |identyfikator zewnętrzny |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| telefon komórkowy |phoneNumbers[wpisz eq "mobile"].wartość |
| Postalcode |adresy[wpisz eq "praca"].postalCode |
| adresy proxy |e-maile[wpisz eq "inne"]. Wartość |
| fizyczna-Dostawa-OfficeName |adresy[wpisz eq "inne"]. Sformatowany |
| Streetaddress |adresy[wpisz eq "praca"].streetAddress |
| surname |nazwa.familyName |
| numer telefonu |phoneNumbers[wpisz eq "praca"].wartość |
| nazwa użytkownika-główny |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabela 3: Domyślne mapowanie atrybutów grupy

| Grupa usługi Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail (poczta) |wiadomości e-mail[wpisz eq "praca"].wartość |
| mailNickname (nazwa pocztowa) |displayName |
| elementy członkowskie |elementy członkowskie |
| Objectid |identyfikator zewnętrzny |
| proxyAddresses |e-maile[wpisz eq "inne"]. Wartość |

Istnieje kilka punktów końcowych zdefiniowanych w ŚCIM RFC. Można rozpocząć pracę z punktem końcowym /User, a następnie rozwinąć stamtąd. Punkt końcowy /Schemas jest przydatne podczas korzystania z atrybutów niestandardowych lub jeśli schemat zmienia się często. Umożliwia klientowi automatyczne pobieranie najbardziej aktualnego schematu. Punkt końcowy /Bulk jest szczególnie przydatne podczas obsługi grup. W poniższej tabeli opisano różne punkty końcowe zdefiniowane w standardzie SCIM. Punkt końcowy /Schemas jest przydatne podczas korzystania z atrybutów niestandardowych lub jeśli schemat zmienia się często. Umożliwia klientowi automatyczne pobieranie najbardziej aktualnego schematu. Punkt końcowy /Bulk jest szczególnie przydatne podczas obsługi grup. W poniższej tabeli opisano różne punkty końcowe zdefiniowane w standardzie SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabela 4: Określ punkty końcowe, które chcesz opracować
|Punktu końcowego|OPIS|
|--|--|
|/Użytkownik|Wykonywanie operacji CRUD na obiekcie użytkownika.|
|/Grupa|Wykonywanie operacji CRUD na obiekcie grupy.|
|/ServiceProviderConfig|Zawiera szczegółowe informacje o funkcjach standardu SCIM, które są obsługiwane, na przykład zasoby, które są obsługiwane i metody uwierzytelniania.|
|/Typy zasobów|Określa metadane dotyczące każdego zasobu|
|/Schematy|Zestaw atrybutów obsługiwanych przez każdego klienta i usługodawcę może się różnić. Podczas gdy jeden dostawca usług może zawierać "nazwa", "tytuł" i "e-maile", podczas gdy inny dostawca usług używa "nazwa", "tytuł" i "numery telefonów". Punkt końcowy schematów umożliwia odnajdowanie obsługiwanych atrybutów.|
|/Luzem|Operacje zbiorcze umożliwiają wykonywanie operacji na dużej kolekcji obiektów zasobów w ramach jednej operacji (np. aktualizacji członkostwa dla dużej grupy).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Krok 2: Opis implementacji scim usługi Azure AD
> [!IMPORTANT]
> Zachowanie implementacji scim usługi Azure AD został ostatnio zaktualizowany w dniu 18 grudnia 2018. Aby uzyskać informacje na temat zmian, zobacz [zgodność protokołu SCIM 2.0 usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Jeśli budujesz aplikację, która obsługuje interfejs API zarządzania użytkownikami SCIM 2.0, w tej sekcji opisano szczegółowo, jak klient scim usługi Azure jest zaimplementowany. Pokazano również, jak modelować obsługę żądań protokołu SCIM i odpowiedzi. Po zaimplementacji punktu końcowego scim, można przetestować go, wykonując procedurę opisaną w poprzedniej sekcji.

W [specyfikacji protokołu SCIM 2.0](http://www.simplecloud.info/#Specification)aplikacja musi spełniać następujące wymagania:

* Obsługuje tworzenie użytkowników, a opcjonalnie również grupuje, zgodnie z [sekcją 3.3 protokołu SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Obsługuje modyfikowanie użytkowników lub grup za pomocą żądań PATCH, zgodnie z [sekcją 3.5.2 protokołu SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Obsługuje pobieranie znanego zasobu dla użytkownika lub grupy utworzonej wcześniej, zgodnie z [sekcją 3.4.1 protokołu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Obsługuje wykonywanie zapytań użytkowników lub grup zgodnie z [sekcją 3.4.2 protokołu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Domyślnie użytkownicy są pobierane przez `id` ich `username` `externalid`i pytane przez `displayName`ich i , i grupy są wyszukiwane przez .  
* Obsługuje wykonywanie zapytań użytkownika według identyfikatora i menedżera, zgodnie z sekcją 3.4.2 protokołu SCIM.  
* Obsługuje wykonywanie zapytań grup według identyfikatora i według członków, zgodnie z sekcją 3.4.2 protokołu SCIM.  
* Akceptuje jeden token na okaziciela do uwierzytelniania i autoryzacji usługi Azure AD do aplikacji.

Postępuj zgodnie z tymi ogólnymi wskazówkami podczas implementowania punktu końcowego scim, aby zapewnić zgodność z usługą Azure AD:

* `id`jest wymaganą właściwością dla wszystkich zasobów. Każda odpowiedź, która zwraca zasób należy upewnić się, że każdy zasób ma tę właściwość, z wyjątkiem `ListResponse` z zero elementów członkowskich.
* Odpowiedź na żądanie kwerendy/filtru `ListResponse`powinna być zawsze .
* Grupy są opcjonalne, ale obsługiwane tylko wtedy, gdy implementacja SCIM obsługuje żądania PATCH.
* Nie jest konieczne, aby uwzględnić cały zasób w odpowiedzi patch.
* Usługa Microsoft Azure AD używa tylko następujących operatorów:  
    - `eq`
    - `and`
* Nie wymaga dopasowania z uwzględnieniem wielkości liter na elementach `op` konstrukcyjnych w scim, w szczególności wartości operacji PATCH, zgodnie z definicją w https://tools.ietf.org/html/rfc7644#section-3.5.2. Usługa Azure AD emituje wartości `Add`"op" jako , `Replace`i `Remove`.
* Usługa Microsoft Azure AD wy żąda pobrania losowego użytkownika i grupy, aby upewnić się, że punkt końcowy i poświadczenia są prawidłowe. Odbywa się to również jako część przepływu **połączenia testowego** w [witrynie Azure portal.](https://portal.azure.com) 
* Atrybut, na który można wyszukiwać zasoby, powinien być ustawiony jako pasujący atrybut w aplikacji w [witrynie Azure portal.](https://portal.azure.com) Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów inicjowania obsługi administracyjnej użytkowników](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Inicjowanie obsługi administracyjnej i usuwanie obsługi administracyjnej przez użytkowników

Na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę Azure Active Directory do usługi SCIM w celu zarządzania cyklem życia użytkownika w magazynie tożsamości aplikacji.  

![Pokazuje sekwencję inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej przez użytkownika](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sekwencja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej przez użytkownika*

### <a name="group-provisioning-and-deprovisioning"></a>Inicjowanie obsługi administracyjnej grupy i anulowanie obsługi administracyjnej

Inicjowanie obsługi administracyjnej grupy i anulowanie obsługi administracyjnej są opcjonalne. Po zaimplementowaniu i włączeniu na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę Azure AD do usługi SCIM w celu zarządzania cyklem życia grupy w magazynie tożsamości aplikacji.  Te wiadomości różnią się od wiadomości o użytkownikach na dwa sposoby:

* Żądania pobierania grup określają, że atrybut członków ma zostać wykluczony z dowolnego zasobu dostarczonego w odpowiedzi na żądanie.  
* Żądania, aby ustalić, czy atrybut odwołania ma określoną wartość są żądania dotyczące atrybutu członków.  

![Pokazuje sekwencję inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej grupy](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sekwencja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej grupy*

### <a name="scim-protocol-requests-and-responses"></a>Żądania i odpowiedzi protokołu SCIM
Ta sekcja zawiera przykładowe żądania SCIM emitowane przez klienta SCIM usługi Azure i przykład oczekiwanych odpowiedzi. Aby uzyskać najlepsze wyniki, należy zakodować aplikację do obsługi tych żądań w tym formacie i emitować oczekiwane odpowiedzi.

> [!IMPORTANT]
> Aby zrozumieć, jak i kiedy usługa inicjowania obsługi administracyjnej użytkowników usługi Azure AD emituje operacje opisane poniżej, zobacz sekcję [Cykle inicjowania obsługi administracyjnej: Początkowe i przyrostowe](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) w [jak działa inicjowanie obsługi administracyjnej.](how-provisioning-works.md)

[Operacje użytkownika](#user-operations)
  - [Utwórz użytkownika](#create-user) [(odpowiedź żądania)](#request) / [Response](#response)
  - [Pobierz użytkownika](#get-user) [(prośba](#request-1) / [o odpowiedź)](#response-1)
  - [Pobierz użytkownika według zapytania](#get-user-by-query) [(Prośba](#request-2) / [o odpowiedź)](#response-2)
  - [Pobierz użytkownika przez zapytanie - Zero wyników](#get-user-by-query---zero-results) [(Odpowiedź](#response-3)[żądania)](#request-3)
/ 
  - [Aktualizuj użytkownika [Właściwości wielowartościowe]](#update-user-multi-valued-properties) ([Odpowiedź](#request-4) /  [żądania](#response-4))
  - [Aktualizuj użytkownika [Właściwości o pojedynczej wartości]](#update-user-single-valued-properties) ([Odpowiedź](#request-5)
/ [żądania](#response-5)) 
  - [Wyłącz użytkownika](#disable-user) [(odpowiedź żądania)](#request-14) / 
[Response](#response-14)
  - [Usuń użytkownika](#delete-user) [(odpowiedź żądania)](#request-6) / 
[Response](#response-6)


[Operacje grupowe](#group-operations)
  - [Utwórz grupę](#create-group) [(odpowiedź](#request-7) / [żądania)](#response-7)
  - [Pobierz grupę](#get-group)  / [(prośba o odpowiedź)](#response-8) [Request](#request-8)
  - [Pobierz grupę według displayName](#get-group-by-displayname) [(Odpowiedź](#request-9) / [żądania)](#response-9)
  - [Aktualizuj grupę [Atrybuty niebędące członkami]](#update-group-non-member-attributes) ( [Odpowiedź](#response-10)[żądania](#request-10) /
 )
  - [Grupa aktualizacji [Dodaj członków]](#update-group-add-members) ( [Prośba](#request-11) /
[o odpowiedź)](#response-11)
  - [Grupa aktualizacji [Usuń członków]](#update-group-remove-members) ( [Odpowiedź żądania)](#request-12) /
[Response](#response-12)
  - [Usuń grupę](#delete-group) [(odpowiedź](#request-13) /
[żądania)](#response-13)

### <a name="user-operations"></a>Operacje użytkownika

* Użytkownicy mogą być `userName` `email[type eq "work"]` wyszukiwane przez lub atrybuty.  

#### <a name="create-user"></a>Utwórz użytkownika

###### <a name="request"></a>Żądanie

*POST /Użytkownicy*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Odpowiedź

*Http/1.1 201 Utworzono*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Pobieranie użytkownika

###### <a name="request"></a><a name="request-1"></a>Żądanie
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Odpowiedź (znaleziono użytkownika)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Żądanie
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Odpowiedź (nie znaleziono użytkownika. Należy zauważyć, że szczegóły nie są wymagane, tylko stan.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Pobierz użytkownika według kwerendy

##### <a name="request"></a><a name="request-2"></a>Żądanie

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Pobierz użytkownika według zapytania - Zero wyników

##### <a name="request"></a><a name="request-3"></a>Żądanie

*GET /Users?filter=userName eq "nieistniejący użytkownik"*

##### <a name="response"></a><a name="response-3"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Aktualizuj użytkownika [Właściwości wielowartościowe]

##### <a name="request"></a><a name="request-4"></a>Żądanie

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Aktualizuj użytkownika [Właściwości o pojedynczej wartości]

##### <a name="request"></a><a name="request-5"></a>Żądanie

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Wyłączanie użytkownika

##### <a name="request"></a><a name="request-14"></a>Żądanie

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Odpowiedzi

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Usuń użytkownika

##### <a name="request"></a><a name="request-6"></a>Żądanie

*USUŃ /Użytkownicy/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

### <a name="group-operations"></a>Operacje grupowe

* Grupy są zawsze tworzone z pustą listą członków.
* Grupy mogą być wyszukiwane przez `displayName` atrybut.
* Aktualizacja do grupy patch żądania powinny dać *HTTP 204 Brak zawartości* w odpowiedzi. Powrót obiektu z listą wszystkich członków nie jest wskazane.
* Nie jest konieczne wspieranie zwracania wszystkich członków grupy.

#### <a name="create-group"></a>Tworzenie grupy

##### <a name="request"></a><a name="request-7"></a>Żądanie

*POST /Grupy HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Odpowiedzi

*Http/1.1 201 Utworzono*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Pobieranie grupy

##### <a name="request"></a><a name="request-8"></a>Żądanie

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Odpowiedzi
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Pobierz grupę według displayName

##### <a name="request"></a><a name="request-9"></a>Żądanie
*POBIERZ /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Aktualizuj grupę [Atrybuty niebędące członkami]

##### <a name="request"></a><a name="request-10"></a>Żądanie

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

### <a name="update-group-add-members"></a>Aktualizuj grupę [Dodawanie członków]

##### <a name="request"></a><a name="request-11"></a>Żądanie

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

#### <a name="update-group-remove-members"></a>Aktualizuj grupę [Usuń członków]

##### <a name="request"></a><a name="request-12"></a>Żądanie

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

#### <a name="delete-group"></a>Usuń grupę

##### <a name="request"></a><a name="request-13"></a>Żądanie

*USUŃ /Grupy/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

### <a name="security-requirements"></a>Wymagania bezpieczeństwa
**Wersje protokołu TLS**

Jedynymi dopuszczalnymi wersjami protokołu TLS są TLS 1.2 i TLS 1.3. Nie są dozwolone żadne inne wersje protokołu TLS. Nie jest dozwolona żadna wersja SSL. 
- Klucze RSA muszą mieć co najmniej 2048 bitów.
- Klucze ECC muszą mieć co najmniej 256 bitów, generowane przy użyciu zatwierdzonej krzywej eliptycznej


**Długości klawiszy**

Wszystkie usługi muszą używać certyfikatów X.509 generowanych przy użyciu kluczy kryptograficznych o wystarczającej długości, co oznacza:

**Zestawy szyfrowe**

Wszystkie usługi muszą być skonfigurowane do korzystania z następujących mechanizmów szyfrowania, w dokładnej kolejności określonej poniżej. Należy zauważyć, że jeśli masz tylko certyfikat RSA, zainstalowane mechanizmy szyfrowania ECDSA nie mają żadnego wpływu. </br>

Minimalny pasek apartamentów TLS 1.2 Cipher Suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Krok 3: Tworzenie punktu końcowego SCIM

Teraz, gdy zaprojektowałeś schemat i zrozumiałeś implementację scim usługi Azure AD, możesz rozpocząć tworzenie punktu końcowego scim. Zamiast zaczynać od zera i budować implementację całkowicie na własną rękę, możesz polegać na wielu bibliotekach SCIM open source opublikowanych przez społeczność SCIM.

[Kod referencyjny](https://aka.ms/SCIMReferenceCode) open source .NET Core opublikowany przez zespół inicjowania obsługi administracyjnej usługi Azure AD jest jednym z takich zasobów, które można szybko rozpocząć programowanie. Po zbudowaniu punktu końcowego SCIM, należy go przetestować. Można użyć kolekcji [testów listonosza](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) dostarczonych jako część kodu referencyjnego lub uruchomić za pośrednictwem przykładowych żądań / odpowiedzi podanych [powyżej](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

   > [!Note]
   > Kod referencyjny ma na celu ułatwienie rozpoczęcia tworzenia punktu końcowego scim i jest dostarczany "W STANIE, W JAKIM JEST". Opinie społeczności są mile widziane, aby pomóc w tworzeniu i utrzymaniu kodu.

Rozwiązanie składa się z dwóch projektów, _Microsoft.SCIM_ i _Microsoft.SCIM.WebHostSample_.

Projekt _Microsoft.SCIM_ to biblioteka, która definiuje składniki usługi sieci web zgodnej ze specyfikacją SCIM. Deklaruje interfejs _Microsoft.SCIM.IProvider_, żądania są tłumaczone na wywołania do metod dostawcy, które zostaną zaprogramowane do działania w magazynie tożsamości.

![Podział: Żądanie przetłumaczone na połączenia z metodami dostawcy](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Projekt Microsoft.SCIM.WebHostSample_ jest aplikacją sieci Web visual studio ASP.NET Core, opartą na _pustym_ szablonie. Dzięki temu przykładowy kod można wdrożyć jako autonomiczny, hostowany w kontenerach lub w internetowych usługach informacyjnych. Implementuje również interfejs _Microsoft.SCIM.IProvider_ przechowywania klas w pamięci jako przykładowego magazynu tożsamości.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Tworzenie niestandardowego punktu końcowego SCIM

Usługa SCIM musi mieć adres HTTP i certyfikat uwierzytelniania serwera, którego głównym urzędem certyfikacji jest jedna z następujących nazw:

* CNNIC (CNNIC)
* Comodo
* Cybertrust
* DigiCert
* Geotrust
* GlobalSign
* Idź tatuś
* Verisign
* Znak WoSign

Zestaw .NET Core SDK zawiera certyfikat deweloperski HTTPS, który może być używany podczas tworzenia, certyfikat jest instalowany jako część środowiska pierwszego uruchomienia. W zależności od sposobu uruchamiania ASP.NET Core Web Application będzie nasłuchiwać innego portu:

* Microsoft.SCIM.WebHostSample:https://localhost:5001
* IIS Express:https://localhost:44359/

Aby uzyskać więcej informacji na temat protokołu HTTPS w ASP.NET Core, użyj następującego łącza: [Wymuszanie protokołu HTTPS w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Obsługa uwierzytelniania punktów końcowych

Żądania z usługi Azure Active Directory zawierają token nośnika OAuth 2.0. Każda usługa odbierająca żądanie powinna uwierzytelnić wystawcę jako usługi Azure Active Directory dla oczekiwanej dzierżawy usługi Azure Active Directory.

W tokenie wystawca jest identyfikowany przez `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`oświadczenie iss, takie jak . W tym przykładzie adres podstawowy wartości `https://sts.windows.net`oświadczenia, , identyfikuje usługi Azure Active Directory jako wystawcy, podczas gdy segment adresu względnego, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, jest unikatowym identyfikatorem dzierżawy usługi Azure Active Directory, dla której token został wystawiony.

Odbiorcami tokenu będzie identyfikator szablonu aplikacji dla aplikacji w galerii, każda z aplikacji zarejestrowanych w jednej dzierżawie może otrzymać takie samo `iss` oświadczenie z żądaniami SCIM. Identyfikator szablonu aplikacji dla każdej aplikacji w galerii jest różny, prosimy o kontakt w [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) przypadku pytań dotyczących identyfikatora szablonu aplikacji dla aplikacji galerii. Identyfikator szablonu aplikacji dla wszystkich aplikacji niestandardowych to _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

W przykładowym kodzie żądania są uwierzytelniane przy użyciu pakietu Microsoft.AspNetCore.Authentication.JwtBearer. Poniższy kod wymusza, że żądania do dowolnego punktu końcowego usługi są uwierzytelnione przy użyciu tokenu nośnika wystawionego przez usługę Azure Active Directory dla określonej dzierżawy:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Token okaziciela jest również wymagane do korzystania z [dostarczonych testów listonosza](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) i wykonywania lokalnego debugowania przy użyciu localhost. Przykładowy kod używa ASP.NET środowiskach podstawowych, aby zmienić opcje uwierzytelniania na etapie rozwoju i włączyć użycie tokenu z podpisem własnym.

Aby uzyskać więcej informacji na temat wielu środowisk w ASP.NET Core użyj następującego łącza: [Użyj wielu środowisk w ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Poniższy kod wymusza, że żądania do dowolnego punktu końcowego usługi są uwierzytelnione przy użyciu tokenu nośnika podpisanego przy użyciu klucza niestandardowego:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Wyślij żądanie GET do kontrolera tokenu, aby uzyskać prawidłowy token elementu nośnego, metoda _GenerateJSONWebToken_ jest odpowiedzialna za utworzenie tokenu pasującego do parametrów skonfigurowanych do programowania:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Obsługa inicjowania obsługi inicjowania obsługi i usuwania obsługi obsługi użytkowników

***Przykład 1. Kwerenda usługi dla pasującego użytkownika***

Usługa Azure Active Directory wysyła zapytanie do usługi dla użytkownika o wartość atrybutu externalId odpowiadającą wartości atrybutu mailNickname użytkownika w usłudze Azure AD. Kwerenda jest wyrażona jako żądanie protokołu TRANSFERU hipertekstu (HTTP), takie jak w tym przykładzie, w którym jyoung jest próbką mailNickname użytkownika w usłudze Azure Active Directory.

>[!NOTE]
> Jest to tylko przykład. Nie wszyscy użytkownicy będą mieli atrybut mailNickname, a wartość, jaką użytkownik ma, może nie być unikatowa w katalogu. Ponadto atrybut używany do dopasowywania (który w tym przypadku jest externalId) można skonfigurować w [mapowania atrybutów usługi Azure AD.](customize-application-attributes.md)

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody QueryAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

W przykładowej kwerendzie dla użytkownika z daną wartością atrybutu externalId wartości argumentów przekazanych do metody QueryAsync są następujące:

* Parametry. AlternateFilters.Count: 1
* Parametry. AlternateFilters.ElementAt(0). Ścieżka atrybutu: "externalId"
* Parametry. AlternateFilters.ElementAt(0). PorównanieOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(0). PorównanieValue: "jyoung"

***Przykład 2. Aprowizuj użytkownika***

Jeśli odpowiedź na kwerendę do usługi sieci web dla użytkownika z wartością atrybutu externalId, która odpowiada wartości atrybutu mailNickname użytkownika, nie zwraca żadnych użytkowników, usługa Azure Active Directory żąda, aby usługa aprowizyła użytkownika odpowiadającego jednemu w usłudze Azure Active Directory.  Oto przykład takiego wniosku: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody CreateAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

W żądaniu aprowizji użytkownika wartość argumentu zasobu jest wystąpieniem klasy Microsoft.SCIM.Core2EnterpriseUser zdefiniowanej w bibliotece Microsoft.SCIM.Schemas.  Jeśli żądanie aprowizji użytkownik powiedzie się, implementacja metody ma zwrócić wystąpienie klasy Microsoft.SCIM.Core2EnterpriseUser, z wartością właściwości Identifier ustawioną na unikatowy identyfikator nowo zainicjowanej klasy Użytkownika.  

***Przykład 3. Zapytanie o bieżący stan użytkownika*** 

Aby zaktualizować użytkownika, o czym wiadomo, że istnieje w magazynie tożsamości, który jest obsługiwany przez narzędzie SCIM, usługa Azure Active Directory jest kontynuowana, żądając bieżącego stanu tego użytkownika z usługi za pomocą żądania, takiego jak: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie retrieveAsync metody dostawcy usługi. Oto podpis tej metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

W przykładzie żądania pobrania bieżącego stanu użytkownika wartości właściwości obiektu pod warunkiem, że jako wartość argumentu parametrów są następujące: 
  
* Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Przykład 4. Zapytanie o wartość atrybutu odwołania, który ma zostać zaktualizowany*** 

Jeśli atrybut odwołania ma zostać zaktualizowany, usługa Azure Active Directory wysyła zapytanie do usługi w celu ustalenia, czy bieżąca wartość atrybutu odwołania w magazynie tożsamości, który jest zgodny z usługą, jest już zgodna z wartością tego atrybutu w usłudze Azure Active Katalogu. Dla użytkowników jedynym atrybutem, którego bieżąca wartość jest wyszukiwana w ten sposób, jest atrybut menedżera. Oto przykład żądania, aby ustalić, czy atrybut menedżera obiektu użytkownika ma obecnie pewną wartość: W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody QueryAsync dostawcy usługi. Wartość właściwości obiektu podana jako wartość argumentu parametrów jest następująca: 
  
* Parametry. AlternateFilters.Count: 2
* Parametry. AlternateFilters.ElementAt(x). Ścieżka atrybutu: "ID"
* Parametry. AlternateFilters.ElementAt(x). PorównanieOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(x). PorównanieValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parametry. AlternateFilters.ElementAt(y). Ścieżka atrybutu: "menedżer"
* Parametry. AlternateFilters.ElementAt(y). PorównanieOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(y). PorównanieValue: "2819c23-7f76-453a-919d-4138619046466"
* Parametry. RequestedAttributePaths.ElementAt(0): "ID"
* Parametry. SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

W tym miejscu wartość indeksu x może wynosić 0, a wartość indeksu y może wynosić 1 lub wartość x może wynosić 1, a wartość y może wynosić 0, w zależności od kolejności wyrażeń parametru kwerendy filtru.   

***Przykład 5. Żądanie usługi Azure AD do usługi SCIM w celu zaktualizowania użytkownika*** 

Oto przykład żądania z usługi Azure Active Directory do usługi SCIM, aby zaktualizować użytkownika: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody UpdateAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

W przykładzie żądania aktualizacji użytkownika obiekt podany jako wartość argumentu patch ma następujące wartości właściwości: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest jako PatchRequest2). Liczba operacji: 1
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Nazwa operacji: OperationName.Add
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "menedżer"
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Wartość.Liczba: 1
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Wartość.ElementAt(0). Referencje: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Wartość.ElementAt(0). Wartość: 2819c23-7f76-453a-919d-4138619046466

***Przykład 6. Anulowanie obsługi administracyjnej użytkownika***

Aby anulować obsługę administracyjną użytkownika ze sklepu tożsamości z usługą SCIM, usługa Azure AD wysyła żądanie, takie jak:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody DeleteAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Obiekt podany jako wartość resourceIdentifier argument ma te wartości właściwości w przykładzie żądania anulowania obsługi administracyjnej użytkownika: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Krok 4: Integracja punktu końcowego scim z klientem scim usługi Azure

Usługę Azure AD można skonfigurować tak, aby automatycznie aprowizować przypisanych użytkowników i grupy do aplikacji implementuujnych określonego profilu [protokołu SCIM 2.0](https://tools.ietf.org/html/rfc7644). Specyfika profilu są udokumentowane w [kroku 2: Zrozumieć implementację scim usługi Azure AD](#step-2-understand-the-azure-ad-scim-implementation).

Skontaktuj się z dostawcą aplikacji lub dokumentacją dostawcy aplikacji, aby uzyskać instrukcje zgodności z tymi wymaganiami.

> [!IMPORTANT]
> Implementacja narzędzia Azure AD SCIM jest oparta na usłudze inicjowania obsługi administracyjnej użytkowników usługi Azure AD, która ma na celu ciągłe synchronizowanie użytkowników między usługą Azure AD a aplikacją docelową i implementuje bardzo specyficzny zestaw standardowych operacji. Ważne jest, aby zrozumieć te zachowania, aby zrozumieć zachowanie klienta scim usługi Azure. Aby uzyskać więcej informacji, zobacz sekcję [Cykle inicjowania obsługi administracyjnej: Początkowe i przyrostowe](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) w [sekcji Jak działa inicjowanie obsługi administracyjnej](how-provisioning-works.md).

### <a name="getting-started"></a>Wprowadzenie

Aplikacje obsługujące profil SCIM opisany w tym artykule można połączyć z usługą Azure Active Directory przy użyciu funkcji "aplikacja nieszgalaktyka" w galerii aplikacji usługi Azure AD. Po połączeniu usługa Azure AD uruchamia proces synchronizacji co 40 minut, w którym wysyła zapytanie do punktu końcowego scim aplikacji dla przypisanych użytkowników i grup oraz tworzy lub modyfikuje je zgodnie ze szczegółami przypisania.

**Aby połączyć aplikację obsługującą scim:**

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com). Należy pamiętać, że możesz uzyskać dostęp do bezpłatnej wersji próbnej usługi Azure Active Directory z licencjami P2, rejestrując się w [programie dla deweloperów](https://developer.microsoft.com/office/dev-program)
2. Wybierz **aplikacje enterprise** z lewego okienka. Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji dodanych z galerii.
3. Wybierz **+ Nowa aplikacja** > **Wszystkie** > **aplikacje niebędące galerią**.
4. Wprowadź nazwę aplikacji i wybierz pozycję **Dodaj,** aby utworzyć obiekt aplikacji. Nowa aplikacja zostanie dodana do listy aplikacji dla przedsiębiorstw i zostanie otwarta na ekranie zarządzania aplikacjami.

   ![Zrzut ekranu przedstawiający galerię aplikacji usługi Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Galeria aplikacji usługi Azure AD*

5. Na ekranie zarządzania aplikacjami wybierz pozycję **Inicjowanie obsługi administracyjnej** w lewym panelu.
6. W menu **Tryb inicjowania obsługi administracyjnej** wybierz polecenie **Automatyczne**.

   ![Przykład: Strona inicjowania obsługi administracyjnej aplikacji w witrynie Azure portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurowanie inicjowania obsługi administracyjnej w witrynie Azure portal*

7. W polu **Adres URL dzierżawy** wprowadź adres URL punktu końcowego scim aplikacji. Przykład: `https://api.contoso.com/scim/`
8. Jeśli punkt końcowy SCIM wymaga tokenu nośnika OAuth od wystawcy innego niż usługa Azure AD, następnie skopiuj wymagany token na okaziciela OAuth do opcjonalnego pola **tokenu tajnego.** Jeśli to pole pozostanie puste, usługa Azure AD zawiera token na okaziciela OAuth wystawiony z usługi Azure AD przy każdym żądaniu. Aplikacje korzystające z usługi Azure AD jako dostawcy tożsamości mogą sprawdzać poprawność tego tokenu wystawionego przez usługę Azure AD. 
   > [!NOTE]
   > ***Nie*** zaleca się pozostawiania tego pola pustego pola i polegania na tokenie wygenerowanym przez usługę Azure AD. Ta opcja jest dostępna głównie do celów testowych.
9. Wybierz **opcję Testuj połączenie,** aby usługa Azure Active Directory próbowała połączyć się z punktem końcowym SCIM. Jeśli próba nie powiedzie się, wyświetlane są informacje o błędzie.  

    > [!NOTE]
    > **Test połączenia** kwerendy punktu końcowego SCIM dla użytkownika, który nie istnieje, przy użyciu losowego identyfikatora GUID jako pasujące właściwości wybranej w konfiguracji usługi Azure AD. Oczekiwana poprawna odpowiedź to HTTP 200 OK z pustym komunikatem SCIM ListResponse.

10. Jeśli próby nawiązania połączenia z aplikacją powiodą się, wybierz pozycję **Zapisz,** aby zapisać poświadczenia administratora.
11. W sekcji **Mapowania** istnieją dwa wybieralne zestawy [mapowań atrybutów:](customize-application-attributes.md)jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdy z nich, aby przejrzeć atrybuty, które są synchronizowane z usługi Azure Active Directory do aplikacji. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania użytkowników i grup w aplikacji do operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    > [!NOTE]
    > Opcjonalnie można wyłączyć synchronizację obiektów grupy, wyłączając mapowanie "grup".

12. W obszarze **Ustawienia**pole **Zakres** określa, którzy użytkownicy i grupy są synchronizowane. Wybierz **pozycję Synchronizuj tylko przypisanych użytkowników i grupy** (zalecane), aby synchronizować tylko użytkowników i grupy przypisane na karcie **Użytkownicy i grupy.**
13. Po zakończeniu konfiguracji ustaw **stan inicjowania obsługi administracyjnej** **na Włączone**.
14. Wybierz **pozycję Zapisz,** aby uruchomić usługę inicjowania obsługi administracyjnej usługi Azure AD.
15. Jeśli synchronizujesz tylko przypisanych użytkowników i grupy (zalecane), wybierz kartę **Użytkownicy i grupy** oraz przypisz użytkowników lub grupy, które chcesz zsynchronizować.

Po rozpoczęciu początkowego cyklu można wybrać **dzienniki inicjowania obsługi administracyjnej** w lewym panelu, aby monitorować postęp, który pokazuje wszystkie działania wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji. Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](check-status-user-account-provisioning.md)

> [!NOTE]
> Początkowy cykl trwa dłużej niż później synchronizuje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Krok 5: Publikowanie aplikacji w galerii aplikacji usługi Azure AD

Jeśli budujesz aplikację, która będzie używana przez więcej niż jedną dzierżawę, możesz udostępnić ją w galerii aplikacji usługi Azure AD. Ułatwi to organizacjom odnajdywanie aplikacji i konfigurowanie inicjowania obsługi administracyjnej. Publikowanie aplikacji w galerii usługi Azure AD i udostępnianie inicjowania obsługi administracyjnej innym osobom jest łatwe. Sprawdź kroki [tutaj](../develop/howto-app-gallery-listing.md). Firma Microsoft będzie współpracować z użytkownikiem w celu zintegrowania aplikacji z naszą galerią, przetestowania punktu końcowego i wydania [dokumentacji](../saas-apps/tutorial-list.md) dołączania dla klientów do użycia. 

### <a name="gallery-onboarding-checklist"></a>Lista kontrolna dołączania do galerii
Postępuj zgodnie z poniższą listą kontrolną, aby upewnić się, że aplikacja jest szybko dołączana, a klienci mają bezproblemowe wdrażanie. Informacje zostaną zebrane od Ciebie podczas dołączania do galerii. 
> [!div class="checklist"]
> * Obsługa użytkownika i punktu końcowego [scim 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) (wymagany jest tylko jeden, ale oba są zalecane)
> * Obsługa co najmniej 25 żądań na sekundę na dzierżawę (wymagane)
> * Nawiązywanie kontaktów inżynierskich i pomocy technicznej, aby poprowadzić klientów do dołączania do galerii (wymagane)
> * 3 Niewygasające poświadczenia testu dla aplikacji (wymagane)
> * Obsługa przyznania kodu autoryzacji OAuth lub długowiekowego tokenu, jak opisano poniżej (wymagane)
> * Ustanowienie punktu kontaktowego technicznego i wsparcia technicznego, aby wspierać klientów po dołączaniu do galerii (wymagane)
> * Obsługa aktualizowania wielu członkostw w grupach za pomocą jednego PATCHA (zalecane) 
> * Dokumentowanie punktu końcowego SCIM publicznie (zalecane) 
> * [Odnajdowanie schematu obsługi](https://tools.ietf.org/html/rfc7643#section-6) (zalecane)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autoryzacja inicjowania obsługi administracyjnej łączników w galerii aplikacji
Specyfikacja SCIM nie definiuje schematu specyficznego dla scim uwierzytelniania i autoryzacji. Opiera się na wykorzystaniu istniejących norm branżowych. Klient inicjowania obsługi administracyjnej usługi Azure AD obsługuje dwie metody autoryzacji dla aplikacji w galerii. 

|Metoda autoryzacji|Zalety|Wady|Pomoc techniczna|
|--|--|--|--|
|Nazwa użytkownika i hasło (nie zalecane lub obsługiwane przez usługę Azure AD)|Łatwe do wdrożenia|Niepewny - [Twój $word Pa$ nie ma znaczenia](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Obsługiwane dla aplikacji galerii dla każdego przypadku. Nie są obsługiwane w przypadku aplikacji innych niż galeria.|
|Długowieczne tokeny na okaziciela|Tokeny trwałe nie wymagają obecności użytkownika. Są one łatwe dla administratorów do użycia podczas konfigurowania inicjowania obsługi administracyjnej.|Długowieczne tokeny mogą być trudne do udostępnienia administratorowi bez użycia niezabezpieczonych metod, takich jak poczta e-mail. |Obsługiwane dla aplikacji galerii i innych niż galeria. |
|Udzielenie kodu autoryzacji OAuth|Tokeny dostępu są znacznie krótsze niż hasła i mają zautomatyzowany mechanizm odświeżania, który nie ma długowiecznych tokenów nośnych.  Rzeczywisty użytkownik musi być obecny podczas wstępnej autoryzacji, dodając poziom odpowiedzialności. |Wymaga obecności użytkownika. Jeśli użytkownik opuści organizację, token jest nieprawidłowy i autoryzacja będzie musiała zostać ukończona ponownie.|Obsługiwane dla aplikacji galerii. Obsługa aplikacji innych niż galeria jest w toku.|
|Przyznanie poświadczeń klienta OAuth|Tokeny dostępu są znacznie krótsze niż hasła i mają zautomatyzowany mechanizm odświeżania, który nie ma długowiecznych tokenów nośnych. Zarówno udzielenie kodu autoryzacji, jak i poświadczenia klienta przyznają utworzenie tego samego typu tokenu dostępu, więc przejście między tymi metodami jest przezroczyste dla interfejsu API.  Inicjowanie obsługi administracyjnej może być całkowicie zautomatyzowane, a nowe tokeny mogą być po cichu wymagane bez interakcji z użytkownikiem. ||Nie są obsługiwane w przypadku aplikacji galerii i aplikacji innych niż galeria. Wsparcie jest w naszych zaległości.|

[!NOTE] Nie zaleca się pozostawiania pustego pola tokenu w interfejsie użytkownika niestandardowej aplikacji inicjującej inicjowanie obsługi administracyjnej usługi Azure AD. Wygenerowany token jest dostępny głównie do celów testowych.

**Przepływ kodu autoryzacji OAuth:** Usługa inicjowania obsługi administracyjnej obsługuje [udzielenie kodu autoryzacji](https://tools.ietf.org/html/rfc6749#page-24). Po przesłaniu prośby o opublikowanie aplikacji w galerii nasz zespół będzie współpracować z Tobą w celu zebrania następujących informacji:
*  Adres URL autoryzacji: adres URL klienta w celu uzyskania autoryzacji od właściciela zasobu za pośrednictwem przekierowania agenta użytkownika. Użytkownik jest przekierowywał do tego adresu URL, aby autoryzować dostęp. 
*  Adres URL wymiany tokenu: adres URL klienta do wymiany dotacji autoryzacji dla tokenu dostępu, zazwyczaj z uwierzytelnianiem klienta.
*  Identyfikator klienta: Serwer autoryzacji wystawia zarejestrowanemu klientowi identyfikator klienta, który jest unikatowym ciągiem reprezentującym informacje rejestracyjne dostarczone przez klienta.  Identyfikator klienta nie jest kluczem tajnym; jest narażony na właściciela zasobu i **nie może** być używany samodzielnie do uwierzytelniania klienta.  
*  Klucz tajny klienta: klucz tajny klienta jest kluczem tajnym generowanym przez serwer autoryzacji. Powinna to być unikatowa wartość znana tylko serwerowi autoryzacji. 

Należy zauważyć, że OAuth v1 nie jest obsługiwany ze względu na ekspozycję klucza tajnego klienta. OAuth v2 jest obsługiwany.  

Najlepsze rozwiązania (zalecane, ale nie wymagane):
* Obsługa wielu adresów URL przekierowań. Administratorzy mogą konfigurować inicjowanie obsługi administracyjnej zarówno z "portal.azure.com" i "aad.portal.azure.com". Obsługa wielu adresów URL przekierowań zapewni użytkownikom możliwość autoryzowania dostępu z jednego portalu.
* Obsługa wielu wpisów tajnych w celu zapewnienia płynnego odnawiania tajemnicy bez przestojów. 

**Długowieczne tokeny na okaziciela OAuth:** Jeśli aplikacja nie obsługuje przepływ udzielić kodu autoryzacji OAuth, można również wygenerować długo trwały token nośny OAuth niż administrator może użyć do skonfigurowania integracji inicjowania obsługi administracyjnej. Token powinien być wieczysty, w przeciwnym razie zadanie inicjowania obsługi administracyjnej zostanie [poddane kwarantannie](application-provisioning-quarantine-status.md) po wygaśnięciu tokenu. Ten token musi być mniejszy niż 1KB.  

Aby uzyskać dodatkowe metody uwierzytelniania i autoryzacji, poinformuj nas o tym w [uservoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Galeria go-to-market launch lista kontrolna
Aby zwiększyć świadomość i zapotrzebowanie na naszą wspólną integrację, zalecamy aktualizację istniejącej dokumentacji i wzmocnienie integracji w kanałach marketingowych.  Poniżej znajduje się zestaw działań z listy kontrolnej, które zalecamy, aby wesprzeć uruchomienie

* **Gotowość do sprzedaży i obsługi klienta.** Upewnij się, że twoje zespoły sprzedaży i wsparcia są świadome i mogą mówić do możliwości integracji. Zgłoś swój zespół sprzedaży i wsparcia, podaj im często zadawane pytania i uwzględnij integrację z materiałami sprzedażowymi. 
* **Wpis na blogu i/lub komunikat prasowy.** Stwórz wpis na blogu lub komunikat prasowy opisujący wspólną integrację, korzyści i chylić rozpoczęcie pracy. [Przykład: Komunikat prasowy Imprivata i usługi Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Media społecznościowe.** Wykorzystaj swoje media społecznościowe, takie jak Twitter, Facebook lub LinkedIn, aby promować integrację z klientami. Pamiętaj, aby @AzureAD dołączyć, abyśmy mogli retweet swój post. [Przykład: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketingowa strona internetowa.** Utwórz lub zaktualizuj swoje strony marketingowe (np. stronę integracji, stronę partnera, stronę cenową itp.), aby uwzględnić dostępność wspólnej integracji. [Przykład: Strona integracji pingboardu,](https://pingboard.com/org-chart-for) [strona integracji smartsheet,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [strona z cennikiem Monday.com](https://monday.com/pricing/) 
* **Dokumentacja techniczna.** Utwórz artykuł centrum pomocy lub dokumentację techniczną dotyczącą sposobu, w jaki klienci mogą rozpocząć pracę. [Przykład: Integracja z usługą Envoy + Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Komunikacja z klientem.** Ostrzegaj klientów o nowej integracji za pośrednictwem komunikacji z klientami (comiesięczne biuletyny, kampanie e-mailowe, informacje o wydaniu produktu). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Zezwalaj na adresy IP używane przez usługę inicjowania obsługi administracyjnej usługi Azure AD do tworzenia żądań SCIM

Niektóre aplikacje zezwalają na ruch przychodzący do aplikacji. Aby usługa inicjowania obsługi administracyjnej usługi Azure AD działała zgodnie z oczekiwaniami, używane adresy IP muszą być dozwolone. Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — [zakresy adresów IP platformy Azure i tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). W razie potrzeby można pobrać i zaprogramować te pliki IP do zapory. Zastrzeżone zakresy adresów IP dla inicjowania obsługi administracyjnej usługi Azure AD można znaleźć w obszarze "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Pokrewne artykuły:

* [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS](user-provisioning.md)
* [Dostosowywanie mapowań atrybutów do inicjowania obsługi administracyjnej przez użytkowników](customize-application-attributes.md)
* [Pisanie wyrażeń dla mapowań atrybutów](functions-for-customizing-application-data.md)
* [Filtry zakresu obsługi administracyjnej użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
* [Powiadomienia o inicjowaniu obsługi administracyjnej kont](user-provisioning.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
